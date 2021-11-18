---
title: Microsoft 센티널 플레이 북에서 트리거 및 작업 사용 | Microsoft Docs
description: Microsoft 센티널 경고 및 인시던트의 정보에 대 한 플레이 북의 액세스 권한을 제공 하 고이 정보를 사용 하 여 수정 작업을 수행 하는 방법을 자세히 알아보세요.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/09/2021
ms.author: yelevin
ms.custom: ignite-fall-2021
ms.openlocfilehash: 1a73cb629792ec60a88531083d63c95a628e25fb
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2021
ms.locfileid: "132712414"
---
# <a name="use-triggers-and-actions-in-microsoft-sentinel-playbooks"></a>Microsoft 센티널 플레이 북에서 트리거 및 작업 사용

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

이 문서에서는 플레이 북이 Microsoft 센티널 및 작업 영역 테이블의 정보를 조작 하는 데 사용할 수 있는 [Logic Apps Microsoft 센티널 커넥터](/connectors/azuresentinel/)의 트리거 및 작업 유형에 대해 설명 합니다. 필요한 특정 형식의 Microsoft 센티널 정보를 가져오는 방법을 추가로 보여 줍니다.

이 문서는 [microsoft 센티널에서 플레이 북을 인증](authenticate-playbooks-to-sentinel.md)하는 가이드와 함께 다른 플레이 북 설명서와 함께 제공 됩니다. [자습서: Microsoft 센티널에서 자동화 규칙으로 플레이 북 사용](tutorial-respond-threats-playbook.md)을 참조 하세요. 이 세 문서는 서로를 참조합니다.

플레이 북에 대 한 소개는 [Microsoft 센티널에서 플레이 북을 통해 위협 대응 자동화](automate-responses-with-playbooks.md)를 참조 하세요.

Microsoft 센티널 커넥터의 전체 사양은 [Logic Apps 커넥터 설명서](/connectors/azuresentinel/)를 참조 하세요.

## <a name="permissions-required"></a>필요한 사용 권한

| 역할 \ 커넥터 구성 요소 | 트리거 | “Get” 작업 | 인시던트 업데이트,<br>주석 추가 |
| ------------- | :-----------: | :------------: | :-----------: |
| **[Microsoft 센티널 판독기](../role-based-access-control/built-in-roles.md#microsoft-sentinel-reader)** | &#10003; | &#10003; | &#10007; |
| **Microsoft 센티널 [응답기](../role-based-access-control/built-in-roles.md#microsoft-sentinel-responder) / [참가자](../role-based-access-control/built-in-roles.md#microsoft-sentinel-contributor)** | &#10003; | &#10003; | &#10003; |
| 

[Microsoft 센티널의 권한에](./roles.md)대해 자세히 알아보세요.

## <a name="microsoft-sentinel-triggers-summary"></a>Microsoft 센티널 트리거 요약

Microsoft 센티널 커넥터를 다양 한 방식으로 사용할 수 있지만 커넥터의 구성 요소는 각각 다른 Microsoft 센티널 발생에 의해 트리거되는 두 개의 흐름으로 나눌 수 있습니다.

| 트리거 | 전체 트리거 이름<br>Logic Apps 디자이너 | 사용하는 경우 | 알려진 제한 사항 
| --------- | ------------ | -------------- | -------------- | 
| **인시던트 트리거** | "Microsoft 센티널 인시던트 생성 규칙이 트리거됨 (미리 보기)" | 대부분의 인시던트 자동화 시나리오에 권장됩니다.<br><br>플레이북은 엔터티 및 경고를 비롯한 인시던트 개체를 수신합니다. 이 트리거를 사용 하면 플레이 북를 **Automation 규칙** 에 연결할 수 있으므로, Microsoft 센티널에서 인시던트가 생성 될 때 트리거될 수 있으며 [자동화 규칙의 모든 이점을](./automate-incident-handling-with-automation-rules.md) 인시던트에 적용할 수 있습니다. | 이 트리거를 사용 하는 플레이 북은 Microsoft 센티널에서 수동으로 실행할 수 없습니다.<br><br>이 트리거가 포함된 플레이북은 경고 그룹화를 지원하지 않습니다. 즉, 각 인시던트에 의해 전송된 첫 번째 경고만 받게 됩니다.
| **경고 트리거** | "Microsoft 센티널 경고에 대 한 응답이 트리거되는 경우" | Microsoft 센티널 포털에서 수동으로 경고를 실행 해야 하는 플레이 북 또는 해당 경고에 대 한 인시던트를 생성 하지 않는 **예약 된** 분석 규칙에 대해 권장 됩니다. | 이 트리거는 **Microsoft 보안** 분석 규칙에서 생성된 경고에 대한 응답을 자동화하는 데 사용할 수 없습니다.<br><br>이 트리거를 사용하는 플레이북은 **자동화 규칙** 으로 호출할 수 없습니다. |
|

이러한 두 흐름에서 사용하는 스키마는 동일하지 않습니다. 가장 좋은 방법은 대부분의 시나리오에 적용 되는 **Microsoft 센티널 인시던트 트리거** 흐름을 사용 하는 것입니다.

### <a name="incident-dynamic-fields"></a>인시던트 동적 필드

**Microsoft 센티널 인시던트 생성 규칙이 트리거된 경우** 에서 받은 **인시던트** 개체는 다음과 같은 동적 필드를 포함 합니다.

- 인시던트 속성(“인시던트: 필드 이름”으로 표시됨)

- 경고(배열)

  - 경고 속성(“경고: 필드 이름”으로 표시됨)

    **경고: \<property name>** 과 같은 경고 속성을 선택하면 인시던트에 여러 경고가 포함될 수 있으므로 *for each* 루프가 자동으로 생성됩니다.

- 엔터티(모든 경고 엔터티의 배열)

- 작업 영역 정보 필드(인시던트가 만들어진 Sentinel 작업 영역에 적용됨)
  - 구독 ID
  - 작업 영역 이름
  - 작업 영역 ID
  - 리소스 그룹 이름

## <a name="microsoft-sentinel-actions-summary"></a>Microsoft 센티널 작업 요약

| 구성 요소 | 사용하는 경우 |
| --------- | -------------- |
| **경고 - 인시던트 가져오기** | 경고 트리거로 시작하는 플레이북에서. 인시던트 속성을 가져오거나 **인시던트 ARM ID** 를 검색하여 **인시던트 업데이트** 또는 **인시던트에 주석 추가** 작업에 사용하는 데 유용합니다. |
| **인시던트 가져오기** | 외부 원본에서 또는 Sentinel이 아닌 트리거를 사용하여 플레이북을 트리거하는 경우. **인시던트 ARM ID** 로 식별합니다. 인시던트 속성 및 주석을 검색합니다. |
| **인시던트 업데이트** | 인시던트의 **상태** 를 변경하려면(예: 인시던트를 닫을 때) **소유자** 를 할당하거나, 태그를 추가 또는 제거하거나, **심각도**, **제목** 또는 **설명** 을 변경합니다.
| **인시던트에 주석 추가** | 외부 원본에서 수집된 정보를 사용하여 인시던트를 보강하려는 경우, 엔터티에 대해 플레이북에서 수행하는 작업을 감사하려는 경우, 인시던트 조사에 유용한 추가 정보를 제공하려는 경우. |
| **엔터티 - \<*entity type*\> 가져오기** | 플레이북 생성 시 알려진 특정 엔터티 유형(**IP**, **Account**, **Host**, **URL** 또는 **FileHash**)에서 작동하는 플레이북에서 구문 분석을 수행하고 고유한 필드에서 작업할 수 있어야 합니다. |
|

## <a name="work-with-incidents---usage-examples"></a>인시던트 작업 - 사용 예제

> [!TIP] 
> **인시던트 업데이트** 및 **인시던트에 주석 추가** 작업에는 **인시던트 ARM ID** 가 필요합니다.
>
> 미리 **경고 - 인시던트 가져오기** 작업을 사용하여 **인시던트 ARM ID** 를 가져옵니다.

### <a name="update-an-incident"></a>인시던트 업데이트
-  **인시던트가 생성되면** 플레이북이 트리거됩니다.

    ![인시던트 트리거 단순 업데이트 흐름 예제](media/playbook-triggers-actions/incident-simple-flow.png)

-  **경고가 생성되면** 플레이북이 트리거됩니다.

    ![경고 트리거 단순 인시던트 업데이트 흐름 예제](media/playbook-triggers-actions/alert-update-flow.png)
      
### <a name="use-incident-information"></a>인시던트 정보 사용

이메일로 인시던트 세부 정보를 보내는 기본 플레이북:
-  **인시던트가 생성되면** 플레이북이 트리거됩니다.

    ![인시던트 트리거 단순 가져오기 흐름 예제](media/playbook-triggers-actions/incident-simple-mail-flow.png)

-  **경고가 생성되면** 플레이북이 트리거됩니다.

    ![경고 트리거 단순 인시던트 가져오기 흐름 예제](media/playbook-triggers-actions/alert-simple-mail-flow.png)

### <a name="add-a-comment-to-the-incident"></a>인시던트에 주석 추가

-  **인시던트가 생성되면** 플레이북이 트리거됩니다.

    ![인시던트 트리거 단순 주석 추가 예제](media/playbook-triggers-actions/incident-comment.png)

-  **경고가 생성되면** 플레이북이 트리거됩니다.

    ![“경고 트리거 단순 주석 추가 예제”](media/playbook-triggers-actions/alert-comment.png)

## <a name="work-with-specific-entity-types"></a>특정 엔터티 형식 작업

**엔터티** 동적 필드는 각각 엔터티를 나타내는 JSON 개체의 배열입니다. 각 엔터티 형식에는 고유한 속성에 따라 고유한 스키마가 있습니다.

**“엔터티 - \<entity name> 가져오기”** 작업을 통해 다음을 수행할 수 있습니다.

- 요청된 형식별로 엔터티 배열을 필터링합니다.
- 이 형식의 특정 필드를 구문 분석하여 추가 작업에서 동적 필드로 사용할 수 있습니다.

입력은 **엔터티** 동적 필드입니다.

응답은 엔터티의 배열로, 특수 속성이 구문 분석되고 *For each* 루프에서 직접 사용할 수 있습니다.

현재 지원되는 엔터티 형식은 다음과 같습니다.

- [IP](/connectors/azuresentinel/#entities---get-ips)
- [Host](/connectors/azuresentinel/#entities---get-hosts)
- [계정](/connectors/azuresentinel/#entities---get-accounts)
- [URL](/connectors/azuresentinel/#entities---get-urls)
- [FileHash](/connectors/azuresentinel/#entities---get-filehashes)

    :::image type="content" source="media/playbook-triggers-actions/entities-actions.png" alt-text="엔터티 작업 목록":::

다른 엔터티 형식의 경우 Logic Apps의 기본 제공 작업을 사용하여 비슷한 기능을 구현할 수 있습니다.

- [**배열 필터링**](../logic-apps/logic-apps-perform-data-operations.md#filter-array-action)을 사용하여 요청된 형식별로 엔터티 배열을 필터링합니다.

- 이 형식의 특정 필드를 구문 분석하면 [**Parse JSON**](../logic-apps/logic-apps-perform-data-operations.md#parse-json-action)을 사용하여 추가 작업에서 동적 필드로 사용할 수 있습니다.

## <a name="work-with-custom-details"></a>사용자 지정 세부 정보 작업

**인시던트 트리거** 에서 사용할 수 있는 **경고 사용자 지정 세부 정보** 동적 필드는 JSON 개체 배열로, 각 개체는 경고의 사용자 지정 세부 정보를 나타냅니다. [사용자 지정 세부 정보](surface-custom-details-in-alerts.md)는 경고에서 이벤트의 정보를 노출하여 인시던트의 일부로 표시, 추적 및 분석할 수 있도록 하는 키-값 쌍입니다.

경고의 이 필드는 사용자 지정이 가능하므로 해당 스키마는 표시되는 이벤트의 유형에 따라 달라집니다. 이 이벤트의 인스턴스에서 데이터를 제공하여 사용자 지정 세부 정보 필드가 구문 분석되는 방법을 결정하는 스키마를 생성해야 합니다.

다음 예제를 참조하십시오.

![분석 규칙에 정의된 사용자 지정 세부 정보입니다.](./media/playbook-triggers-actions/custom-details-values.png)

이러한 키-값 쌍에서 키(왼쪽 열)는 사용자가 만든 사용자 지정 필드를 나타내고, 값(오른쪽 열)은 사용자 지정 필드를 채우는 이벤트 데이터의 필드를 나타냅니다.

다음 JSON 코드를 제공하여 스키마를 생성할 수 있습니다. 코드에서는 키 이름을 배열로 표시하고 값(값을 포함하는 열이 아닌 실제 값으로 표시됨)을 배열의 항목으로 표시합니다.

```json
{ "FirstCustomField": [ "1", "2" ], "SecondCustomField": [ "a", "b" ] }
```

1. **Parse JSON** 기본 제공 작업을 사용하여 새 단계를 추가합니다. 검색 필드에 ‘parse json’을 입력하여 찾을 수 있습니다.

1. 인시던트 트리거 아래의 **동적 콘텐츠** 목록에서 **경고 사용자 지정 세부 정보** 를 찾아 선택합니다.

    ![“동적 콘텐츠”에서 “경고 사용자 지정 세부 정보”를 선택합니다.](./media/playbook-triggers-actions/custom-details-dynamic-field.png)

    인시던트에 경고 배열이 포함되어 있으므로 **For each** 루프가 생성됩니다.

1. **샘플 페이로드를 사용하여 스키마 생성** 링크를 클릭합니다.

    ![‘샘플 페이로드를 사용하여 스키마 생성’ 링크 선택](./media/playbook-triggers-actions/generate-schema-link.png)

1. 샘플 페이로드를 제공합니다. Log Analytics(**Logs** 블레이드)에서 이 경고의 다른 인스턴스를 찾아보고 사용자 지정 세부 정보 개체(**Extended Properties** 아래)를 복사하면 샘플 페이로드를 찾을 수 있습니다. 아래 스크린샷에서는 위에 표시된 JSON 코드를 사용했습니다.

    ![샘플 JSON 페이로드를 입력합니다.](./media/playbook-triggers-actions/sample-payload.png)

1. 사용자 지정 필드는 **Array** 형식의 동적 필드를 사용할 준비가 되었습니다. 위에서 설명한 대로 스키마와 **동적 콘텐츠** 아래에 표시되는 목록에서 배열 및 해당 항목을 볼 수 있습니다.

    ![사용할 준비가 된 스키마의 필드입니다.](./media/playbook-triggers-actions/fields-ready-to-use.png)
    
## <a name="next-steps"></a>다음 단계

이 문서에서는 Microsoft 센티널 플레이 북의 트리거와 작업을 사용 하 여 위협에 대응 하는 방법에 대해 자세히 알아보았습니다. 
- Microsoft 센티널을 사용 하 여 [위협을 사전](hunting.md) 에 파악 하는 방법을 알아봅니다.
