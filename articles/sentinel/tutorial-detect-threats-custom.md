---
title: Azure Sentinel을 사용하여 위협을 검색하는 사용자 지정 분석 규칙 만들기 | Microsoft Docs
description: 이 자습서를 사용하여 Azure Sentinel에서 보안 위협을 검색하는 사용자 지정 분석 규칙을 만드는 방법을 알아봅니다. 이벤트 그룹화, 경고 그룹화 및 경고 보강을 활용하고 자동 사용 안 함을 이해합니다.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/25/2021
ms.author: yelevin
ms.openlocfilehash: 846eb51c85a00a90b5565064ffa106e4fc69e56c
ms.sourcegitcommit: 23040f695dd0785409ab964613fabca1645cef90
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/14/2021
ms.locfileid: "112059637"
---
# <a name="tutorial-create-custom-analytics-rules-to-detect-threats"></a>자습서: 위협 검색을 위한 사용자 지정 분석 규칙 만들기

이제 Azure Sentinel에 [데이터 원본을 연결](quickstart-onboard.md)했으므로 사용자 환경에 존재하는 위협 및 비정상적인 동작을 발견하는 데 도움이 되는 사용자 지정 분석 규칙을 만들 수 있습니다. 이러한 규칙은 사용자 환경에서 특정 이벤트 또는 이벤트 집합을 검색하고, 특정 이벤트 임계값 또는 조건에 도달하면 경고를 생성하고, SOC에서 심사 및 조사를 위해 인시던트를 생성하고, 자동화된 추적 및 수정 프로세스를 통해 위협에 대응합니다. 

이 자습서에서는 사용자 지정 규칙을 만들어 Azure Sentinel에서 위협을 검색하는 방법을 안내합니다.

이 자습서를 완료하고 나면 다음을 수행할 수 있습니다.
> [!div class="checklist"]
> * 분석 규칙 만들기
> * 이벤트 및 경고가 처리되는 방법 정의
> * 경고 및 인시던트를 생성하는 방법 정의
> * 규칙에 대한 자동화된 위협 대응 선택

## <a name="create-a-custom-analytics-rule-with-a-scheduled-query"></a>예약된 쿼리를 사용하여 사용자 지정 분석 규칙 만들기

1. Azure Sentinel 탐색 메뉴에서 **Analytics** 를 선택합니다.

1. 상단 작업 모음에서 **+만들기** 를 선택한 다음 **예약된 쿼리 규칙** 을 선택합니다. 그러면 **분석 규칙 마법사** 가 열립니다.

    :::image type="content" source="media/tutorial-detect-threats-custom/create-scheduled-query-small.png" alt-text="예약된 쿼리 만들기" lightbox="media/tutorial-detect-threats-custom/create-scheduled-query-full.png":::

### <a name="analytics-rule-wizard---general-tab"></a>분석 규칙 마법사 - 일반 탭

- 고유한 **이름** 및 **설명** 을 제공합니다. 

- **전술** 필드에서 규칙을 분류할 공격 범주 중에서 선택할 수 있습니다. 이는 [MITRE ATT&CK](https://attack.mitre.org/) 프레임워크의 전술을 기반으로 합니다.

- 경고 **심각도** 를 적절하게 설정합니다. 

- 규칙을 만들 때 **상태** 는 기본적으로 **사용** 으로 설정되어 있습니다. 즉, 규칙 만들기를 완료한 후 즉시 실행됩니다. 즉시 실행하지 않으려면 **사용 안 함** 을 선택합니다. 규칙은 **활성 규칙** 탭에 추가되고 여기에서 필요할 때 사용하도록 설정할 수 있습니다.

   :::image type="content" source="media/tutorial-detect-threats-custom/general-tab.png" alt-text="사용자 지정 분석 규칙 만들기 시작":::

## <a name="define-the-rule-query-logic-and-configure-settings"></a>규칙 쿼리 논리 정의 및 설정 구성

**규칙 논리 설정** 탭에서 **규칙 쿼리** 필드에 직접 쿼리를 작성하거나 Log Analytics에서 쿼리를 만든 다음 여기에 복사하여 붙여넣을 수 있습니다.

- 쿼리는 Kusto Query Language(KQL)로 작성됩니다. KQL [개념](/azure/data-explorer/kusto/concepts/) 및 [쿼리](/azure/data-explorer/kusto/query/)에 대해 자세히 알아보고 편리한 [빠른 참조 가이드](/azure/data-explorer/kql-quick-reference)를 참조하세요.

- 이 스크린샷에서 표시된 예는 *SecurityEvent* 테이블을 쿼리하여 [실패한 Windows 로그온 이벤트](/windows/security/threat-protection/auditing/event-4625)의 유형을 표시합니다.

   :::image type="content" source="media/tutorial-detect-threats-custom/set-rule-logic-tab-1-new.png" alt-text="쿼리 규칙 논리 및 설정 구성" lightbox="media/tutorial-detect-threats-custom/set-rule-logic-tab-all-1-new.png":::

- 다음은 [Azure 활동](../azure-monitor/essentials/activity-log.md)에서 비정상적으로 많은 리소스가 생성될 때 경고하는 또 다른 샘플 쿼리입니다.

    ```kusto
    AzureActivity
    | where OperationName == "Create or Update Virtual Machine" or OperationName =="Create Deployment"
    | where ActivityStatus == "Succeeded"
    | make-series dcount(ResourceId)  default=0 on EventSubmissionTimestamp in range(ago(7d), now(), 1d) by Caller
    ```

    > [!NOTE]
    > #### <a name="rule-query-best-practices"></a>규칙 쿼리 모범 사례
    > - 쿼리 길이는 1 ~ 10,000자 사이여야 하며 "`search *`" 또는 "`union *`"를 포함할 수 없습니다. [사용자 정의 함수](/azure/data-explorer/kusto/query/functions/user-defined-functions)를 사용하여 쿼리 길이 제한을 극복할 수 있습니다.
    >
    > - ADX 함수를 사용하여 Log Analytics 쿼리 창 안에 Azure Data Explorer 쿼리를 만드는 것은 **지원되지 않습니다**.
    >
    > - 쿼리에서 **`bag_unpack`** 함수를 사용할 때 "`project field1`"을 사용하여 열을 필드로 프로젝트하고 열이 존재하지 않으면 쿼리가 실패합니다. 이러한 상황을 방지하려면 열을 다음과 같이 프로젝트해야 합니다.
    >   - `project field1 = column_ifexists("field1","")`

### <a name="alert-enrichment"></a>경고 보강

> [!IMPORTANT]
> 경고 보강 기능은 현재 **미리 보기** 로 제공됩니다. 베타 또는 미리 보기로 제공되거나 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 추가 약관은 [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

- **엔터티 매핑** 구성 섹션을 사용하여 쿼리 결과의 매개 변수를 Azure Sentinel에서 인식할 수 있는 엔터티로 매핑할 수 있습니다. 엔터티는 다음에 나오는 조사 프로세스 및 수정 작업의 빌딩 블록으로 제공되는 필수 정보를 사용하여 규칙의 출력(경고 및 인시던트)을 보강합니다. **인시던트 설정** 탭에서 경고를 인시던트에 그룹화할 수 있는 기준이기도 합니다.

    [Azure Sentinel의 엔터티](entities-in-azure-sentinel.md)에 대해 자세히 알아봅니다.

    [이전 버전과의 호환성](map-data-fields-to-entities.md#notes-on-the-new-version)에 대한 중요한 정보와 함께 전체 엔터티 매핑 지침은 [Azure Sentinel의 엔터티에 데이터 필드 매핑](map-data-fields-to-entities.md)을 참조하세요.

- **사용자 지정 세부 정보** 구성 섹션을 사용하여 쿼리에서 이벤트 데이터 항목을 추출하고 이 규칙에 의해 생성된 경고에 표시하여 경고 및 인시던트에 즉시 이벤트 콘텐츠를 표시합니다.

    경고에 사용자 지정 세부 정보를 표시하는 방법에 대해 자세히 알아보고 [전체 지침](surface-custom-details-in-alerts.md)을 참조하세요.

- **경고 세부 정보** 구성 섹션을 사용하여 경고의 프레젠테이션 정보를 실제 콘텐츠에 맞게 조정합니다. 예를 들어, 경고 세부 정보를 사용하여 경고 자체의 제목에 공격자의 IP 주소 또는 계정 이름을 표시할 수 있으며 이러한 항목이 인시던트 큐에 표시되므로 사용자는 위협 환경에 대한 보다 풍부하고 명확한 정보를 얻을 수 있습니다.

    [경고 세부 정보 사용자 지정](customize-alert-details.md)에 대한 자세한 지침을 참조하세요.

### <a name="query-scheduling-and-alert-threshold"></a>쿼리 일정 및 경고 임계값

- **쿼리 일정** 섹션에서 다음 매개 변수를 설정합니다.

   :::image type="content" source="media/tutorial-detect-threats-custom/set-rule-logic-tab-2.png" alt-text="쿼리 일정 및 이벤트 그룹화 설정" lightbox="media/tutorial-detect-threats-custom/set-rule-logic-tab-all-2-new.png":::

    - 쿼리가 실행되는 빈도(5분마다 자주 또는 14일마다 한 번 드물게)를 제어하려면 **쿼리 실행 간격** 을 설정합니다.

    - **마지막 데이터 조회** 를 설정하여 쿼리가 적용되는 데이터의 기간을 결정합니다. 예를 들어 지난 10분간의 데이터 또는 지난 6시간의 데이터를 쿼리할 수 있습니다. 최댓값은 14일입니다.

        > [!NOTE]
        > **쿼리 간격 및 되돌아보기 기간**
        >
        >  이 두 설정은 한 지점까지 서로 독립적입니다. 간격보다 긴 기간(실제로 쿼리가 겹치는 경우)을 포함하는 짧은 간격으로 쿼리를 실행할 수 있지만 적용 기간을 초과하는 간격으로 쿼리를 실행할 수 없습니다. 그렇지 않으면 전체 쿼리 범위에 간격이 생깁니다.
        >
        > **수집 지연**
        >
        > 원본에서 이벤트를 생성하고 Azure Sentinel에 수집하는 동안 발생할 수 있는 **대기 시간** 을 고려하여 데이터 중복 없이 전체 범위를 보장하기 위해 Azure Sentinel은 예약된 시간부터 **5분 지연** 시간을 두어 예약된 분석 규칙을 실행합니다.
        >
        > 이 지연이 필요한 이유와 이 문제를 해결하는 방법에 대한 자세한 기술 설명은 "[Azure Sentinel 예약 경고 규칙에서 수집 지연 처리](https://techcommunity.microsoft.com/t5/azure-sentinel/handling-ingestion-delay-in-azure-sentinel-scheduled-alert-rules/ba-p/2052851)"라는 주제에 대한 Ron Marsiano의 훌륭한 블로그 게시물을 참조하세요.

- **경고 임계값** 섹션을 사용하여 규칙의 민감도 수준을 정의합니다. 예를 들어 쿼리가 실행될 때마다 1000개보다 많은 결과를 반환하는 경우에만 규칙이 경고를 생성하도록 하려면 **쿼리 결과 수가 다음일 때 경고 생성** 을 **보다 큼** 으로 설정하고 숫자 1000을 입력합니다. 이 필드는 필수 필드이므로 임계값을 설정하지 않으려면, 즉 경고에 모든 이벤트를 등록하려는 경우 숫자 필드에 0을 입력합니다.

### <a name="results-simulation"></a>결과 시뮬레이션

**결과 시뮬레이션** 영역에서 마법사의 오른쪽에 있는 **현재 데이터를 사용하여 테스트** 를 선택하면 Azure Sentinel에는 현재 정의된 일정에 따라 실행된 최근 50시간 동안 쿼리가 생성한 결과(로그 이벤트) 그래프가 표시됩니다. 쿼리를 수정하는 경우 **현재 데이터로 테스트** 를 다시 선택하여 그래프를 업데이트합니다. 그래프는 **쿼리 일정** 섹션의 설정에 따라 결정되는 정의된 기간 동안의 결과 수를 보여 줍니다.

다음은 위의 스크린샷에 나온 쿼리 결과 시뮬레이션입니다. 왼쪽은 기본 보기이며 오른쪽은 그래프의 특정 시점을 마우스로 가리킬 때 표시되는 내용입니다.

:::image type="content" source="media/tutorial-detect-threats-custom/results-simulation.png" alt-text="결과 시뮬레이션 스크린샷":::

쿼리에서 너무 많거나 너무 자주 발생하는 경고를 트리거하는 경우 **쿼리 일정** 및 **경고 임계값** [섹션](#query-scheduling-and-alert-threshold) 의 설정을 실험해 보고 **현재 데이터로 테스트** 를 다시 선택할 수 있습니다.

### <a name="event-grouping-and-rule-suppression"></a>이벤트 그룹화 및 규칙 제거

> [!IMPORTANT]
> 이벤트 그룹화는 현재 **미리 보기** 상태입니다. 베타 또는 미리 보기로 제공되거나 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 추가 약관은 [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

- **이벤트 그룹화** 에서 두 가지 방법 중 하나를 선택하여 **이벤트** 를 **경고** 로 그룹화합니다. 

    - **모든 이벤트를 단일 경고로 그룹화**(기본 설정). 쿼리가 위에서 지정된 **경고 임계값** 보다 많은 결과를 반환하는 한 규칙은 실행될 때마다 단일 경고를 생성합니다. 경고에는 결과에 반환된 모든 이벤트에 대한 요약이 포함됩니다. 

    - **각 이벤트에 대해 경고 트리거**. 규칙은 쿼리에서 반환된 각 이벤트에 대해 고유한 경고를 생성합니다. 이벤트를 개별적으로 표시하거나 특정 매개 변수(사용자, 호스트 이름 또는 기타 항목)별로 그룹화하려는 경우에 유용합니다. 쿼리에서 이러한 매개 변수를 정의할 수 있습니다.

        현재 규칙에서 생성할 수 있는 경고 수는 20개입니다. 특정 규칙에서 **이벤트 그룹화** 가 **각 이벤트에 대한 경고 트리거** 로 설정되고 규칙의 쿼리가 20개가 넘는 이벤트를 반환하는 경우 처음 19개의 이벤트는 고유한 경고를 생성하고 20번째 경고는 반환된 이벤트의 전체 집합을 요약합니다. 즉, 20번째 경고는 **모든 이벤트를 단일 경고로 그룹화** 옵션에서 생성된 것입니다.

        이 옵션을 선택하는 경우 Azure Sentinel은 새 필드인 **Originalquery** 를 쿼리 결과에 추가합니다. 기존 **Query** 필드와 새 필드를 비교한 결과는 다음과 같습니다.

        | 필드 이름 | 포함 | 이 필드에서 쿼리 실행<br>결과 |
        | - | :-: | :-: |
        | **쿼리** | 경고의 이 인스턴스를 생성한 이벤트의 압축된 레코드 | 경고의 이 인스턴스를 생성한 이벤트 |
        | **OriginalQuery** | 분석&nbsp;규칙에 따라 작성된 원래 쿼리 | 쿼리가 실행되는 시간대의 가장 최근 이벤트로, 쿼리에 정의된 매개 변수에 적합합니다. |
        |

        즉, **OriginalQuery** 필드는 일반적으로 **쿼리** 필드가 동작하는 것처럼 동작합니다. 이 추가 필드의 결과는 아래 [문제 해결](#troubleshooting) 섹션의 첫 번째 항목에서 설명하는 문제가 해결되었다는 것입니다.
 
    > [!NOTE]
    > **이벤트** 와 **경고** 의 차이점은 무엇인가요?
    >
    > - **이벤트** 는 단일 작업 발생에 대한 설명입니다. 예를 들어 로그 파일의 단일 항목은 이벤트로 간주될 수 있습니다. 이 컨텍스트에서 이벤트는 분석 규칙의 쿼리에서 반환되는 단일 결과를 나타냅니다.
    >
    > - **경고** 는 함께 수행되고 보안 관점에서 중요한 이벤트의 모음입니다. 예를 들어 업무 시간 외에 외국에서 관리 로그인과 같은 중요한 보안 관련이 있는 경우 경고에는 단일 이벤트가 포함될 수 있습니다.
    >
    > - 그러면 **인시던트** 는 무엇인가요? Azure Sentinel의 내부 논리는 **경고** 또는 경고 그룹에서 **인시던트** 를 만듭니다. 인시던트 큐는 SOC 분석가의 작업(분류, 조사 및 수정)의 중심입니다.
    > 
    > Azure Sentinel은 일부 데이터 원본에서 원시 이벤트를 수집하고 다른 데이터 원본에서 이미 처리된 경고를 수집합니다. 어느 쪽을 다루고 있는지 항상 메모하는 것이 중요합니다.

- 경고를 받은 후 쿼리 간격을 초과하는 기간 동안 이 규칙의 작업을 일시 중단하려는 경우에는 **제거** 섹션에서 **경고 생성 후 쿼리 실행 중지** 를 **설정** 할 수 있습니다. 이 기능을 설정하는 경우 **다음 기간 동안 쿼리 실행 중지** 를 쿼리 실행을 중지해야 하는 시간(최대 24시간)으로 설정해야 합니다.

## <a name="configure-the-incident-creation-settings"></a>인시던트 만들기 설정 구성

**인시던트 설정** 탭에서 Azure Sentinel이 경고를 조치 가능한 인시던트로 설정하는지 여부와 방법을 선택할 수 있습니다. 이 탭을 그대로 두면 Azure Sentinel은 각 경고에서 별도의 단일 인시던트를 만듭니다. 이 탭에서 설정을 변경하여 인시던트를 만들지 않거나 여러 경고를 단일 인시던트로 그룹화하도록 선택할 수 있습니다.

> [!IMPORTANT]
> 인시던트 설정 탭은 현재 **미리 보기** 상태입니다. 베타 또는 미리 보기로 제공되거나 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 추가 약관은 [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

예를 들면 다음과 같습니다.

:::image type="content" source="media/tutorial-detect-threats-custom/incident-settings-tab.png" alt-text="인시던트 만들기 및 경고 그룹화 설정 정의":::

### <a name="incident-settings"></a>인시던트 설정

**인시던트 설정** 섹션에서 **이 분석 규칙에 의해 트리거된 경고에서 인시던트 만들기** 가 기본적으로 **사용** 으로 설정됩니다. 즉, Azure Sentinel은 규칙에 의해 트리거되는 각 경고에서 별도의 단일 인시던트를 만듭니다.

- 이 규칙을 사용하여 인시던트가 생성되는 것을 원하지 않는 경우(예: 이 규칙이 후속 분석을 위해 정보만 수집하는 경우) **사용 안 함** 으로 설정합니다.

- 모든 단일 경고에 대해 하나의 인시던트 대신 경고 그룹에서 단일 인시던트를 만들려면 다음 섹션을 참조하세요.

### <a name="alert-grouping"></a>경고 그룹화

**경고 그룹화** 섹션에서 최대 150개의 유사하거나 반복되는 경고 그룹에서 단일 인시던트를 생성하려면(참고 참조) **이 분석 규칙에 의해 트리거되는 관련 경고 그룹화** 를 **사용** 으로 설정하고 다음 매개 변수를 설정합니다.


- **선택한 기간 내에 생성된 경고로 그룹 제한**: 유사하거나 반복되는 경고를 함께 그룹화할 기간을 결정합니다. 이 시간 프레임 내에 해당하는 모든 경고는 아래 그룹화 설정에 따라 인시던트 또는 인시던트 집합을 전체적으로 생성합니다. 이 시간 프레임을 벗어난 경고는 별도의 인시던트 또는 인시던트 집합을 생성합니다.

- **다음 기준으로 이 분석 규칙에 의해 트리거된 경고를 단일 인시던트로 그룹화**: 경고와 함께 그룹화할 기준을 선택합니다.

    | 옵션 | Description |
    | ------- | ---------- |
    | **모든 엔터티가 일치하는 경우 경고를 단일 인시던트로 그룹화** | 경고는 매핑된 각 엔터티에 대해 동일한 값을 공유하는 경우 함께 그룹화됩니다(위의 [규칙 논리 설정](#define-the-rule-query-logic-and-configure-settings) 탭에 정의됨). 권장 설정입니다. |
    | **이 규칙으로 트리거된 모든 경고를 단일 인시던트로 그룹화** | 이 규칙에 의해 생성된 모든 경고가 동일한 값을 공유하지 않더라도 함께 그룹화됩니다. |
    | **선택한 엔터티 및 세부 정보가 일치하는 경우 경고를 단일 인시던트로 그룹화** | 경고는 매핑된 모든 엔터티, 경고 세부 정보 및 해당 드롭다운 목록에서 선택한 사용자 지정 세부 정보의 동일한 값을 공유하는 경우 함께 그룹화됩니다.<br><br>예를 들어, 원본 또는 대상 IP 주소를 기준으로 별도의 인시던트를 만들거나 특정 엔터티 및 심각도와 일치하는 경고를 그룹화하려는 경우 이 설정을 사용할 수 있습니다.<br><br>**참고**: 이 옵션을 선택하는 경우 규칙에 대해 하나 이상의 엔터티 형식 또는 필드를 선택해야 합니다. 그러지 않으면 규칙 유효성 검사가 실패하고 규칙이 만들어지지 않습니다. |
    |

- **닫힌 일치 인시던트 다시 열기**: 인시던트가 해결되어 닫힌 후 나중에 해당 인시던트에 속해야 하는 다른 경고가 생성되는 경우 닫힌 인시던트를 다시 열려면 이 설정을 **사용** 으로 설정하고 경고가 새 인시던트를 만들도록 하려면 **사용 안 함** 으로 그대로 둡니다.
    
    > [!NOTE]
    > **최대 150개의 경고** 를 하나의 인시던트로 그룹화할 수 있습니다. 단일 인시던트로 그룹화하는 규칙에 의해 150개가 넘는 경고가 생성되는 경우 원본과 동일한 인시던트 세부 정보를 사용하여 새 인시던트가 생성되고 초과 경고는 새 인시던트로 그룹화됩니다.

## <a name="set-automated-responses-and-create-the-rule"></a>자동화된 대응 설정 및 규칙 만들기

1. **자동화된 대응** 탭에서 사용자 지정 규칙에 의해 경고가 생성될 때 자동으로 실행할 플레이북을 선택합니다. 플레이북을 만들고 자동화하는 방법에 대한 자세한 내용은 [위협에 대응](tutorial-respond-threats-playbook.md)을 참조하세요.

    :::image type="content" source="media/tutorial-detect-threats-custom/automated-response-tab.png" alt-text="자동화된 대응 설정 정의":::

1. **검토 및 만들기** 를 선택하여 새 경고 규칙에 대한 모든 설정을 검토합니다. "유효성 검사 통과" 메시지가 나타나면 **만들기** 를 선택하여 경고 규칙을 초기화합니다.

    :::image type="content" source="media/tutorial-detect-threats-custom/review-and-create-tab.png" alt-text="모든 설정 검토 및 규칙 만들기":::

## <a name="view-the-rule-and-its-output"></a>규칙 및 해당 출력 보기
  
- 기본 **분석** 화면의 **활성 규칙** 탭 아래에 있는 표에서 새로 만든 사용자 지정 규칙('예약 됨'유형)을 찾을 수 있습니다. 이 목록에서 각 규칙을 사용 또는 사용하지 않도록 설정하거나 삭제할 수 있습니다.

- 만든 경고 규칙의 결과를 보려면 **인시던트** 페이지로 이동하여 [인시던트를 조사](tutorial-investigate-cases.md)하고, 위협을 수정할 수 있습니다.

- 가양성을 제외하도록 규칙 쿼리를 업데이트할 수 있습니다. 자세한 내용은 [Azure Sentinel의 가양성 처리](false-positives.md)를 참조하세요.

> [!NOTE]
> Azure Sentinel에서 생성된 경고는 [Microsoft Graph 보안](/graph/security-concept-overview)을 통해 사용할 수 있습니다. 자세한 내용은 [Microsoft Graph 보안 경고 문서](/graph/api/resources/security-api-overview)를 참조하세요.

## <a name="export-the-rule-to-an-arm-template"></a>ARM 템플릿으로 규칙 내보내기

관리 및 배포할 규칙을 코드로 패키징하려는 경우 [규칙을 ARM(Azure Resource Manager) 템플릿으로 쉽게 내보낼 수 있습니다](import-export-analytics-rules.md). 사용자 인터페이스에서 규칙을 보고 편집하기 위해 템플릿 파일에서 규칙을 가져올 수도 있습니다.

## <a name="troubleshooting"></a>문제 해결

### <a name="issue-no-events-appear-in-query-results"></a>문제: 쿼리 결과에 이벤트가 표시되지 않습니다.

**이벤트 그룹화** 가 **각 이벤트에 대해 경고 트리거** 로 설정된 경우 특정 시나리오에서 나중에 쿼리 결과를 볼 때(예: 인시던트의 경고를 다시 피벗하는 경우) 쿼리 결과가 나타나지 않을 수 있습니다. 이는 경고에 대한 이벤트의 연결은 특정 이벤트 정보를 해시하고 쿼리에 해시를 포함하여 수행되기 때문입니다. 경고가 생성된 후 쿼리 결과가 변경된 경우 해시가 더 이상 유효하지 않으며 아무 결과도 표시되지 않습니다. 

이벤트를 보려면 규칙의 쿼리에서 해시가 포함된 줄을 수동으로 제거하고 쿼리를 실행합니다.

> [!NOTE]
> 이 문제는 이 이벤트 그룹화 옵션을 선택할 때 결과에 새 필드 **OriginalQuery** 를 추가하여 해결했습니다. 위의 [설명](#event-grouping-and-rule-suppression)을 참조하세요.

### <a name="issue-a-scheduled-rule-failed-to-execute-or-appears-with-auto-disabled-added-to-the-name"></a>문제: 예약된 규칙을 실행하지 못했거나 이름에 자동 사용 안 함이 추가된 상태로 표시됩니다.

예약된 쿼리 규칙이 실행되지 않는 경우는 드물지만 발생할 수 있습니다. Azure Sentinel은 실패의 특정 유형과 그 원인이 되는 상황을 기반으로 하여 일시적 또는 영구적으로 오류를 미리 분류합니다.

#### <a name="transient-failure"></a>일시적 오류

일시적인 상황으로 인해 일시적 오류가 발생하며 곧 정상으로 돌아오고 이 시점에서 규칙 실행이 성공합니다. 다음은 Azure Sentinel에서 임시로 분류하는 오류에 대한 몇 가지 예입니다.

- 규칙 쿼리를 실행하는 데 시간이 너무 오래 걸리고 시간이 초과되었습니다.
- 데이터 원본 및 Log Analytics 간 또는 Log Analytics와 Azure Sentinel 간의 연결 문제가 있습니다.
- 다른 모든 새롭고 알려지지 않은 오류는 일시적인 것으로 간주됩니다.

일시적인 오류가 발생하는 경우 Azure Sentinel은 미리 정해지고 지속적으로 증가하는 간격 후 일정 지점까지 규칙을 계속 실행하려고 합니다. 그 후에는 다음 예약된 시간에만 규칙이 다시 실행됩니다. 일시적 오류로 인해 규칙이 자동으로 사용하지 않도록 설정되지 않습니다.

#### <a name="permanent-failure---rule-auto-disabled"></a>영구적 오류 - 규칙 자동 사용 안 함

규칙 실행을 허용하는 조건의 변경으로 인해 영구적 오류가 발생하며, 사람의 개입 없이는 이전 상태로 돌아가지 않습니다. 다음은 영구적으로 분류되는 오류의 몇 가지 예입니다.

- 규칙 쿼리가 작동하는 대상 작업 영역이 삭제 되었습니다.
- 규칙 쿼리가 작동하는 대상 테이블이 삭제되었습니다.
- Azure Sentinel이 대상 작업 영역에서 제거되었습니다.
- 규칙 쿼리에서 사용하는 함수가 더 이상 유효하지 않습니다. 수정되었거나 제거되었습니다.
- 규칙 쿼리의 데이터 원본 중 하나에 대한 권한이 변경되었습니다.
- 규칙 쿼리의 데이터 원본 중 하나가 삭제되거나 연결 해제되었습니다.

**동일한 유형 및 동일한 규칙에 대해 미리 정해진 수의 연속적인 영구적 오류가 발생하는 경우**  Azure Sentinel은 규칙 실행 시도를 중지하고 다음 단계도 수행합니다.

- 규칙을 사용하지 않도록 설정합니다.
- 규칙 이름의 시작 부분에 **"자동 사용 안 함"** 이라는 단어를 추가합니다.
- 규칙의 설명에 실패 이유(및 사용하지 않음)를 추가합니다.

규칙 목록을 이름별로 정렬하여 자동 사용 안 함 규칙이 있는지 쉽게 확인할 수 있습니다. 자동 사용 안 함 규칙은 목록의 맨 위 또는 그 근처에 표시됩니다.

SOC 관리자는 자동 사용 안 함 규칙이 있는지 정기적으로 규칙 목록을 확인해야 합니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 Azure Sentinel을 사용하여 위협 검색을 시작하는 방법을 배웠습니다.

- [Azure Sentinel에서 인시던트를 조사](tutorial-investigate-cases.md)하는 방법을 알아봅니다.
- [Azure Sentinel의 엔터티](entities-in-azure-sentinel.md)에 대해 알아봅니다.
- [Azure Sentinel에서 자동화된 위협 대응을 설정](tutorial-respond-threats-playbook.md)하는 방법을 알아봅니다.
