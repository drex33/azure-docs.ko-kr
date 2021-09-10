---
title: Azure Sentinel을 사용하여 Azure AD B2C 보호
titleSuffix: Azure AD B2C
description: 이 자습서에서는 Azure Sentinel을 사용하여 Azure AD B2C에 대한 보안 분석을 수행하는 방법을 보여 줍니다.
services: active-directory-b2c
author: agabrielle
manager: ''
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.author: agher
ms.subservice: B2C
ms.date: 07/19/2021
ms.openlocfilehash: a405cb97a021c05a3b0c6aa004d3f92ce4657d24
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/14/2021
ms.locfileid: "122537647"
---
# <a name="tutorial-how-to-perform-security-analytics-for-azure-ad-b2c-data-with-azure-sentinel"></a>자습서: Azure Sentinel을 사용하여 Azure AD B2C 데이터에 대한 보안 분석을 수행하는 방법

로그 및 감사 정보를 Azure Sentinel로 라우팅하여 Azure AD B2C 환경을 추가로 보호할 수 있습니다. Azure Sentinel은 클라우드 네이티브 **SIEM(보안 정보 이벤트 관리) 및 SOAR(보안 오케스트레이션 자동화 응답)** 솔루션입니다. Azure Sentinel은 **Azure AD B2C** 에 대한 경고 검색, 위협 가시성, 주도적 헌팅, 위협 대응 기능을 제공합니다.

Azure AD B2C와 함께 Azure Sentinel을 활용하면 다음을 수행할 수 있습니다.

- Microsoft 분석 및 업계 최고의 위협 인텔리전스를 사용하여 이전에 탐지되지 않은 위협을 탐지하고 가양성을 최소화합니다.
- 수년간에 걸친 Microsoft 사이버 보안 성과물을 활용하여 AI를 통해 위협을 조사하고 의심스러운 대규모 활동을 헌팅합니다.
- 일반 작업의 기본 제공 오케스트레이션 및 자동화로 빠르게 인시던트에 대응합니다.
- 조직의 보안 및 규정 준수 요구 사항을 충족합니다.

이 자습서에서는 다음에 대해 알아봅니다.

1. Azure Monitor 로그 작업 영역에 B2C 로그를 전송하는 방법
1. Log Analytics 작업 영역에서 **Azure Sentinel** 을 사용하도록 설정
1. Sentinel에서 인시던트를 트리거할 샘플 규칙 만들기
1. 마지막으로, 몇 가지 자동화된 응답 구성

## <a name="configure-aad-b2c-with-azure-monitor-logs-analytics"></a>Azure Monitor Log Analytics를 사용하여 AAD B2C 구성

다음 단계에서는 Azure AD B2C 테넌트 내의 Azure Active Directory에서 **_진단 설정_** 을 사용하도록 설정하는 프로세스를 안내합니다.
진단 설정은 리소스에 대한 로그 및 메트릭을 보낼 위치를 정의합니다.

[Azure Monitor를 사용하여 Azure AD B2C 모니터링](./azure-monitor.md)의 **1~5** 단계를 수행하여 Azure Monitor에 로그를 보내도록 Azure AD B2C를 구성합니다.

## <a name="deploy-an-azure-sentinel-instance"></a>Azure Sentinel 인스턴스 배포

> [!IMPORTANT]
> Azure Sentinel을 사용하도록 설정하려면 Azure Sentinel 작업 영역이 있는 구독에 대한 **기여자 권한** 이 필요합니다. Azure Sentinel을 사용하려면 작업 영역이 속한 리소스 그룹에 대해 참가자 또는 읽기 권한자 권한이 있어야 합니다.

Azure Monitor에 로그를 보내도록 Azure AD B2C 인스턴스를 구성한 후에는 Azure Sentinel 인스턴스를 사용하도록 설정해야 합니다.

1. Azure Portal에 로그인합니다. LA(로그 분석)가 이전 단계에서 만든 작업 영역인 구독이 선택되었는지 확인합니다.

2. **Azure Sentinel** 을 검색하여 선택합니다.

3. **추가** 를 선택합니다.

   :::image type="content" source="./media/azure-sentinel/azure-sentinel-add.png" alt-text="Azure Portal에서 Azure Sentinel 검색":::

4. 이전 단계에서 사용한 작업 영역을 선택합니다.

   :::image type="content" source="./media/azure-sentinel/create-new-workspace.png" alt-text="Sentinel 작업 영역 선택":::

5. **Azure Sentinel 추가** 를 선택합니다.

   > [!NOTE]
   > Azure Sentinel은 둘 이상의 작업 영역에서 실행할 수 있지만 데이터는 단일 작업 영역으로 격리됩니다. Sentinel을 사용하도록 설정하는 방법에 대한 자세한 내용은 [빠른 시작](../sentinel/quickstart-onboard.md)을 참조하세요.

## <a name="create-a-sentinel-rule"></a>Sentinel 규칙 만들기

> [!NOTE]
> Azure Sentinel에서는 Microsoft 보안 전문가 및 분석가 팀이 설계한, 위협 탐지 규칙을 만드는 데 도움이 되는 기본 제공 템플릿을 제공합니다. 템플릿에서 생성된 규칙은 데이터에서 의심스러운 활동을 자동으로 검색합니다. 현재 네이티브 Azure AD B2C 커넥터가 없으므로 예제에서는 네이티브 규칙을 사용하지 않습니다. 이 자습서를 위해 고유한 규칙을 만듭니다.

이제 Sentinel을 사용하도록 설정했으므로 B2C 테넌트에서 의심스러운 활동이 발생하면 알림을 받으려고 합니다.

사용자 환경에 존재하는 위협과 비정상적인 동작을 발견하는 데 도움이 되는 사용자 지정 분석 규칙을 만들 수 있습니다. 이 규칙은 특정 이벤트나 이벤트 집합을 검색하고, 추가 조사를 위해 인시던트를 생성하기 위한 특정 이벤트 임계값이나 조건에 도달할 경우 경고합니다.

> [!NOTE]
> 분석 규칙을 자세히 검토하려면 [자습서](/azure/active-directory-b2c/articles/sentinel/detect-threats-custom.md)를 참조하세요.

이 시나리오에서는 누군가가 환경에 강제로 액세스하려고 시도했지만 실패한 경우 알림을 받으려고 합니다. 무차별 암호 대입 공격을 의미할 수 있으며, **_60초 이내에 2번 이상 실패한 로그인_** 에 대한 알림을 받으려고 합니다.

1. Azure Sentinel 탐색 메뉴에서 **Analytics** 를 선택합니다.
2. 상단 작업 모음에서 **+만들기** 를 선택한 다음 **예약된 쿼리 규칙** 을 선택합니다. 그러면 **분석 규칙 마법사** 가 열립니다.

   :::image type="content" source="./media/azure-sentinel/create-scheduled-rule.png" alt-text="예약된 쿼리 만들기 규칙 선택":::

3. 분석 규칙 마법사 - 일반 탭

   - 고유한 **이름** 과 **설명** 을 제공합니다.
     - **이름**: _B2C 실패한 로그인_ **설명**: _60초 이내에 2번 이상 실패한 로그인 알림_
   - **전술** 필드에서 규칙을 분류할 공격 범주 중에서 선택할 수 있습니다. 이는 [MITRE ATT&CK](https://attack.mitre.org/) 프레임워크의 전술을 기반으로 합니다.

     - 예제에서는 _PreAttack_ 을 선택합니다.

       > [!Tip]
       > MITRE ATT&CK®는 실제 관찰을 기반으로 한 악의적 사용자 전술과 기법이 포함된 기술 자료로, 전 세계에서 액세스할 수 있습니다. ATT&CK 기술 자료는 특정 위협 모델과 방법론 개발의 기초로 사용됩니다.

   - 경고 **심각도** 를 적절하게 설정합니다.
     - 첫 번째 규칙이므로 _높음_ 을 선택합니다. 나중에 규칙을 변경할 수 있습니다.
   - 규칙을 만들 때 **상태** 는 기본적으로 **사용** 으로 설정되어 있습니다. 즉, 규칙 만들기를 완료한 후 즉시 실행됩니다. 즉시 실행하지 않으려면 **사용 안 함** 을 선택합니다. 규칙은 **활성 규칙** 탭에 추가되고 여기에서 필요할 때 사용하도록 설정할 수 있습니다.

     :::image type="content" source="./media/azure-sentinel/create-new-rule.png" alt-text="기본 규칙 속성 제공":::

4. 규칙 쿼리 논리를 정의하고 설정을 구성합니다.

   **규칙 논리 설정** 탭의 **규칙 쿼리** 필드에서 직접 쿼리를 작성합니다. 이 쿼리는 B2C 테넌트에서 60초 이내에 실패한 로그인이 두 번 이상 있을 때 경고하고 _UserPrincipalName_ 으로 구성됩니다.

   ```kusto
   SigninLogs
   | where ResultType != "0"
   | summarize Count = count() by bin(TimeGenerated, 60s), UserPrincipalName
   | project Count = toint(Count), UserPrincipalName
   | where Count >= 1
   ```

   :::image type="content" source="./media/azure-sentinel/rule-query.png" alt-text="논리 탭에서 규칙 쿼리 입력":::

   쿼리 일정 섹션에서 다음 매개 변수를 설정합니다.

   :::image type="content" source="./media/azure-sentinel/query-scheduling.png" alt-text="쿼리 예약 매개 변수 설정":::

5. **인시던트 설정(미리 보기)** 과 **자동화된 응답** 에서 다음을 클릭합니다. 나중에 자동화된 응답을 구성하고 추가합니다.

6. 다음을 클릭하여 **검토 및 만들기** 탭에 액세스하고 새 경고 규칙에 대한 모든 설정을 검토합니다. "유효성 검사 통과" 메시지가 나타나면 **만들기** 를 선택하여 경고 규칙을 초기화합니다.

   :::image type="content" source="./media/azure-sentinel/review-create.png" alt-text="규칙 검토 및 만들기":::

7. 규칙과 규칙이 생성하는 인시던트를 확인합니다.

   기본 **분석** 화면의 **활성 규칙** 탭 아래에 있는 표에서 새로 만든 사용자 지정 규칙('예약 됨'유형)을 찾을 수 있습니다. 이 목록에서 규칙을 **_편집_**, **_사용하도록 설정_**, **_사용하지 않도록 설정_** 또는 **_삭제_** 할 수 있습니다.

   :::image type="content" source="./media/azure-sentinel/rule-crud.png" alt-text="규칙을 편집, 사용하도록 설정, 사용하지 않도록 설정 또는 삭제하는 옵션이 표시된 분석 화면":::

   새 B2C 실패한 로그인 규칙의 결과를 보려면 **인시던트** 페이지로 이동합니다. 여기서 위협을 심사, 조사, 수정할 수 있습니다.

   인시던트는 여러 경고를 포함할 수 있습니다. 이는 특정 조사에 대한 모든 관련 증거의 집계입니다. 인시던트 수준에서 심각도, 상태 등의 속성을 설정할 수 있습니다.

   > [!NOTE]
   > 인시던트 조사를 자세히 검토하려면 [자습서](/azure/active-directory-b2c/articles/sentinel/investigate-cases.md)를 참조하세요.

   조사를 시작하려면 특정 인시던트를 선택합니다. 오른쪽에서 심각도, 관련 엔터티, 인시던트를 트리거한 원시 이벤트, 인시던트의 고유 ID를 포함하여 인시던트에 대한 자세한 정보를 볼 수 있습니다.

   :::image type="content" source="./media/azure-sentinel/select-incident.png" alt-text="인시던트 화면":::

   인시던트의 경고 및 엔터티에 대한 자세한 내용을 보려면 인시던트 페이지에서 **전체 세부 정보 보기** 를 선택하고 인시던트 정보가 요약된 관련 탭을 검토합니다.

   :::image type="content" source="./media/azure-sentinel/full-details.png" alt-text="규칙 73":::

   인시던트에 대한 자세한 내용을 검토하려면 **증거->이벤트** 또는 **이벤트->Log Analytics에 연결** 을 선택합니다.

   결과에는 로그인을 _number_ 번 시도한 ID의 _UserPrincipalName_ 이 표시됩니다.

   :::image type="content" source="./media/azure-sentinel/logs.png" alt-text="선택한 인시던트의 세부 정보":::

## <a name="automated-response"></a>자동화된 대응

Azure Sentinel에서는 강력한 SOAR 기능도 제공합니다. 추가 정보는 [여기](../sentinel/automation-in-azure-sentinel.md)에 제공된 공식 Sentinel 설명서에서 확인할 수 있습니다.

Sentinel에서 플레이북이라고 불리는 자동화된 작업을 요구 사항에 맞게 분석 규칙에 연결할 수 있습니다.

이 예제에서는 규칙을 통해 만든 인시던트 발생 시 메일 알림을 추가합니다.

작업을 수행하기 위해 Sentinel GitHub 리포지토리인 [Incident-Email-Notification](https://github.com/Azure/Azure-Sentinel/tree/master/Playbooks/Incident-Email-Notification)의 기존 플레이북을 사용합니다.

플레이북이 구성된 후 기존 규칙을 편집하고 자동화 탭에서 플레이북을 선택하기만 하면 됩니다.

:::image type="content" source="./media/azure-sentinel/automation-tab.png" alt-text="규칙에 연결된 자동화된 응답의 구성 화면":::

## <a name="next-steps"></a>다음 단계

- 완벽한 규칙은 없으므로 필요한 경우 규칙 쿼리를 업데이트하여 가양성을 제외할 수 있습니다. 자세한 내용은 [Azure Sentinel의 가양성 처리](../sentinel/false-positives.md)를 참조하세요.

- 데이터 분석과 풍부한 시각적 보고서 생성에 도움이 되도록 데이터를 기반으로 하여 인사이트를 표시하는, 전문가가 만든 통합 문서 갤러리에서 선택하여 다운로드합니다. 요구 사항에 맞게 [통합 문서](https://github.com/azure-ad-b2c/siem#workbooks)를 쉽게 사용자 지정할 수 있습니다.

- Sentinel에 대한 자세한 내용은 [Azure Sentinel 설명서](../sentinel/index.yml)를 참조하세요.