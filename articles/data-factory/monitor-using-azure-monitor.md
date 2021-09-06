---
title: Azure Monitor를 사용하여 데이터 팩터리 모니터링
description: Data Factory의 정보로 진단 로그를 사용하도록 설정하여 Azure Monitor를 사용하여 Azure Data Factory 파이프라인을 모니터링하는 방법을 알아봅니다.
author: minhe-msft
ms.author: hemin
ms.reviewer: jburchel
ms.service: data-factory
ms.subservice: monitoring
ms.topic: conceptual
ms.date: 07/13/2020
ms.openlocfilehash: 3029f7756d50e509d7bd539dc5fde8de8a075424
ms.sourcegitcommit: 0396ddf79f21d0c5a1f662a755d03b30ade56905
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/17/2021
ms.locfileid: "122530554"
---
# <a name="monitor-and-alert-data-factory-by-using-azure-monitor"></a>Azure Monitor를 사용하여 Data Factory 모니터링 및 경고

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

클라우드 애플리케이션은 복잡하며 이동하는 부분이 많습니다. 모니터는 애플리케이션이 정상 상태로 계속 실행되도록 하는 데 도움이 되는 데이터를 제공합니다. 또한 모니터는 잠재적인 문제를 방지하고 이전 문제를 해결하는 데도 도움이 됩니다. 모니터링 데이터를 사용하여 애플리케이션에 대해 심층적인 인사이트를 얻을 수 있습니다. 이러한 지식은 애플리케이션 성능 및 유지 관리를 향상시키는 데 도움이 됩니다. 또한 수동 개입이 필요한 작업을 자동화하는 데 도움이 됩니다.

Azure Monitor는 대부분의 Azure 서비스에 대한 기본 수준의 인프라 메트릭 및 로그를 제공합니다. Azure 진단 로그는 리소스에서 내보내며, 해당 리소스의 작업에 대한 풍부하고 빈번한 데이터를 제공합니다. ADF(Azure Data Factory)는 Azure Monitor에서 진단 로그를 작성할 수 있습니다. 7분 동안 이 기능의 소개 및 데모에 대한 다음 비디오를 시청하세요.

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Monitor-Data-Factory-pipelines-using-Operations-Management-Suite-OMS/player]

자세한 내용은 [Azure Monitor 개요](../azure-monitor/overview.md)를 참조하세요.

## <a name="keeping-azure-data-factory-metrics-and-pipeline-run-data"></a>Azure Data Factory 메트릭 및 파이프라인 실행 데이터 유지

Data Factory는 파이프라인 실행 데이터를 45일 동안만 저장합니다. 해당 데이터를 더 오랫동안 유지하려는 경우 Azure Monitor를 사용해야 합니다. Monitor를 사용하면 분석을 위한 진단 로그를 여러 다양한 대상으로 라우팅할 수 있습니다.

* **스토리지 계정**: 감사 또는 수동 검사를 위해 스토리지 계정에 진단 로그를 저장합니다. 진단 설정을 사용하여 보존 시간(일)을 지정할 수 있습니다.
* **이벤트 허브**: 로그를 Azure Event Hubs로 스트리밍합니다. 로그는 Power BI와 같은 파트너 서비스/사용자 지정 분석 솔루션에 입력됩니다.
* **Log Analytics**: Log Analytics를 사용하여 로그를 분석합니다. Azure Monitor와 Data Factory 통합은 다음과 같은 시나리오에서 유용합니다.
  * Data Factory에서 Monitor에 게시한 다양한 메트릭 세트에 대해 복잡한 쿼리를 작성하려고 합니다. Monitor를 통해 해당 쿼리에 대한 사용자 지정 경고를 생성할 수 있습니다.
  * 데이터 팩터리를 모니터링하려고 합니다. 여러 데이터 팩터리의 데이터를 단일 Monitor 작업 영역으로 라우팅할 수 있습니다.

또한 로그를 내보내는 리소스의 구독에 없는 스토리지 계정 또는 이벤트 허브 네임스페이스를 사용할 수도 있습니다. 설정을 구성하는 사용자는 두 구독 모두에 대한 적절한 Azure RBAC(Azure 역할 기반 액세스 제어) 액세스 권한이 있어야 합니다.

## <a name="configure-diagnostic-settings-and-workspace"></a>진단 설정 및 작업 영역 구성

데이터 팩터리에 대한 진단 설정을 생성하거나 추가합니다.

1. 포털에서 모니터로 이동합니다. **설정** > **진단 설정** 을 선택합니다.

1. 진단 설정을 지정하려는 데이터 팩터리를 선택합니다.

1. 선택한 데이터 팩터리에 설정이 없는 경우 설정을 생성하라는 메시지가 표시됩니다. **진단 켜기** 를 선택합니다.

   ![설정이 없는 경우 진단 설정 생성](media/data-factory-monitor-oms/monitor-oms-image1.png)

   데이터 팩터리에 대한 기존 설정이 있는 경우 데이터 팩터리에 대해 이미 구성된 설정 목록이 표시됩니다. **진단 설정 추가** 를 선택합니다.

   ![설정이 있는 경우 진단 설정 추가](media/data-factory-monitor-oms/add-diagnostic-setting.png)

1. 설정에 이름을 지정하고 **Log Analytics에 보내기** 를 선택한 후 **Log Analytics 작업 영역** 에서 작업 영역을 선택합니다.

    * _Azure-Diagnostics_ 모드에서 진단 로그는 _AzureDiagnostics_ 테이블로 흐릅니다.

    * _Resource-Specific_ 모드에서 Azure Data Factory의 진단 로그는 다음 테이블로 흐릅니다.
      - _ADFActivityRun_
      - _ADFPipelineRun_
      - _ADFTriggerRun_
      - _ADFSSISIntegrationRuntimeLogs_
      - _ADFSSISPackageEventMessageContext_
      - _ADFSSISPackageEventMessages_
      - _ADFSSISPackageExecutableStatistics_
      - _ADFSSISPackageExecutionComponentPhases_
      - _ADFSSISPackageExecutionDataStatistics_

      Log Analytics 테이블에 보낼 워크로드와 관련된 다양한 로그를 선택할 수 있습니다. 예를 들어 SSIS(SQL Server Integration Services)를 전혀 사용하지 않는 경우 SSIS 로그를 선택할 필요가 없습니다. SSIS IR(Integration Runtime) 시작/중지/유지 관리 작업을 로그하려는 경우 SSIS IR 로그를 선택할 수 있습니다. SSMS(SQL Server Management Studio), SQL Server 에이전트 또는 기타 지정된 도구에서 T-SQL을 통해 SSIS 패키지 실행을 호출하는 경우 SSIS 패키지 로그를 선택할 수 있습니다. ADF 파이프라인에서 SSIS 패키지 실행 작업을 통해 SSIS 패키지 실행을 호출하는 경우 모든 로그를 선택할 수 있습니다.

    * _AllMetrics_ 를 선택하면 다양한 ADF 메트릭을 사용하여 모니터링하거나 경고를 발생시킬 수 있습니다. 여기에는 ADF 활동, 파이프라인 및 트리거 실행은 물론 SSIS IR 작업 및 SSIS 패키지 실행에 대한 메트릭이 포함됩니다.

   ![설정 이름 지정 및 Log Analytics 작업 영역 선택](media/data-factory-monitor-oms/monitor-oms-image2.png)

    > [!NOTE]
    > Azure 로그 테이블의 열은 500개를 초과할 수 없으므로 리소스 관련(_Resource-Specific_) 모드를 선택할 것을 **적극 권장** 합니다. 자세한 내용은 [AzureDiagnostics 로그 참조](/azure/azure-monitor/reference/tables/azurediagnostics)를 확인하세요.

1. **저장** 을 선택합니다.

잠시 후 이 데이터 팩터리의 설정 목록에 새 설정이 나타납니다. 새 이벤트 데이터가 생성되는 즉시 진단 로그가 해당 작업 영역으로 스트리밍됩니다. 이벤트가 내보내지는 시점에서 Log Analytics에 표시되는 시점까지 최대 15분이 걸릴 수 있습니다.

## <a name="install-azure-data-factory-analytics-solution-from-azure-marketplace"></a>Azure Marketplace에서 Azure Data Factory Analytics 솔루션 설치

이 솔루션은 Data Factory의 전반적인 상태에 대한 요약과 함께 세부 정보를 자세히 살펴보고 예기치 않은 동작 패턴 문제를 해결할 수 있는 옵션을 제공합니다. 기본 제공되는 풍부한 보기를 통해 다음을 비롯한 주요 처리에 대한 인사이트를 얻을 수 있습니다.

* 데이터 팩터리 파이프라인, 활동 및 트리거 실행을 한눈에 보여 주는 요약
* 데이터 팩터리 활동 실행에 대한 정보를 유형별로 상세히 검색할 수 있는 기능
* 데이터 팩터리 주요 파이프라인, 활동 오류 요약

1. **Azure Marketplace** 로 이동하고 **Analytics** 필터를 선택하고 **Azure Data Factory Analytics(미리 보기)** 를 검색합니다.

   !["Azure Marketplace"로 이동하여 "Analytics 필터"를 입력하고 "Azure Data Factory Analytics(미리 보기)" 선택](media/data-factory-monitor-oms/monitor-oms-image3.png)

1. **Azure Data Factory Analytics(미리 보기)** 에 대한 세부 정보

   !["Azure Data Factory Analytics(미리 보기)"에 대한 세부 정보](media/data-factory-monitor-oms/monitor-oms-image4.png)

1. **만들기** 를 선택한 후 **Log Analytics 작업 영역** 을 만들거나 선택합니다.

   ![새 솔루션 만들기](media/data-factory-monitor-oms/monitor-log-analytics-image-5.png)

### <a name="monitor-data-factory-metrics"></a>Data Factory 메트릭 모니터링

이 솔루션을 설치하면 선택한 Log Analytics 작업 영역의 통합 문서 섹션 내에 기본 보기 집합이 생성됩니다. 그 결과 다음과 같은 메트릭이 활성화됩니다.

* ADF 실행 - 1) Data Factory의 파이프라인 실행
* ADF 실행 - 2) Data Factory의 활동 실행
* ADF 실행 - 3) Data Factory의 트리거 실행
* ADF 오류 - 1) Data Factory의 상위 10개 파이프라인 오류
* ADF 오류 - 2) Data Factory의 상위 10개 활동 실행
* ADF 오류 - 3) Data Factory의 상위 10개 트리거 오류
* ADF 통계 - 1) 형식별 활동 실행
* ADF 통계 - 2) 형식별 트리거 실행
* ADF 통계 - 3) 최대 파이프라인 실행 기간

!["통합 문서(미리 보기)" 및 "AzureDataFactoryAnalytics"가 강조 표시된 창](media/data-factory-monitor-oms/monitor-oms-image6.png)

이전 메트릭을 시각화하고, 이러한 메트릭에 숨겨진 쿼리를 확인하며, 쿼리를 편집하고, 경고를 만들고, 기타 작업을 수행할 수 있습니다.

![데이터 팩터리에서 실행된 파이프라인에 대한 그래픽 표현"](media/data-factory-monitor-oms/monitor-oms-image8.png)

> [!NOTE]
> Azure Data Factory Analytics(미리 보기)는 진단 로그를 _Resource-specific_ 대상 테이블로 보냅니다. _ADFPipelineRun_, _ADFTriggerRun_, _ADFActivityRun_ 테이블에 대해 쿼리를 작성할 수 있습니다.

## <a name="data-factory-metrics"></a>Data Factory 메트릭

Monitor를 사용하면 Azure 워크로드의 성능과 상태에 대한 가시성을 확보할 수 있습니다. 가장 중요한 유형의 Monitor 데이터는 메트릭이며 성능 카운터라고도 합니다. 메트릭은 대부분의 Azure 리소스에서 내보내집니다. Monitor는 모니터링 및 문제 해결을 위해 이러한 메트릭을 구성 및 사용하는 몇 가지 방법을 제공합니다.

Azure Data Factory 버전 2에서 내보내는 메트릭은 다음과 같습니다.

| **메트릭**                           | **메트릭 표시 이름**                  | **단위** | **집계 유형** | **설명**                |
|--------------------------------------|------------------------------------------|----------|----------------------|--------------------------------|
| ActivityCancelledRuns                 | 취소된 활동 실행 메트릭           | 개수    | 합계                | 1분 기간 내에 취소된 활동 실행의 총 수입니다. |
| ActivityFailedRuns                   | 실패한 활동 실행 메트릭             | 개수    | 합계                | 1분 기간 내에 실패한 활동 실행의 총 수입니다. |
| ActivitySucceededRuns                | 성공한 활동 실행 메트릭          | 개수    | 합계                | 1분 기간 내에 성공한 활동 실행의 총 수입니다. |
| PipelineCancelledRuns                 | 취소된 파이프라인 실행 메트릭           | 개수    | 합계                | 1분 기간 내에 취소된 파이프라인 실행의 총 수입니다. |
| PipelineFailedRuns                   | 실패한 파이프라인 실행 메트릭             | 개수    | 합계                | 1분 기간 내에 실패한 파이프라인 실행의 총 수입니다. |
| PipelineSucceededRuns                | 성공한 파이프라인 실행 메트릭          | 개수    | 합계                | 1분 기간 내에 성공한 파이프라인 실행의 총 수입니다. |
| TriggerCancelledRuns                  | 취소된 트리거 실행 메트릭            | 개수    | 합계                | 1분 기간 내에 취소된 트리거 실행의 총 수입니다. |
| TriggerFailedRuns                    | 실패한 트리거 실행 메트릭              | 개수    | 합계                | 1분 기간 내에 실패한 트리거 실행의 총 수입니다. |
| TriggerSucceededRuns                 | 성공한 트리거 실행 메트릭           | 개수    | 합계                | 1분 기간 내에 성공한 트리거 실행의 총 수입니다. |
| SSISIntegrationRuntimeStartCancelled  | 취소된 SSIS 통합 런타임 시작 메트릭           | 개수    | 합계                | 1분 기간 내에 취소된 SSIS 통합 런타임 시작의 총 수입니다. |
| SSISIntegrationRuntimeStartFailed    | 실패한 SSIS 통합 런타임 시작 메트릭             | 개수    | 합계                | 1분 기간 내에 실패한 SSIS 통합 런타임 시작의 총 수입니다. |
| SSISIntegrationRuntimeStartSucceeded | 성공한 SSIS 통합 런타임 시작 메트릭          | 개수    | 합계                | 1분 기간 내에 성공한 SSIS 통합 런타임 시작의 총 수입니다. |
| SSISIntegrationRuntimeStopStuck      | 중단된 SSIS 통합 런타임 중지 메트릭               | 개수    | 합계                | 1분 기간 내에 중단된 SSIS 통합 런타임 중지의 총 수입니다. |
| SSISIntegrationRuntimeStopSucceeded  | 성공한 SSIS 통합 런타임 중지 메트릭           | 개수    | 합계                | 1분 기간 내에 성공한 SSIS 통합 런타임 중지의 총 수입니다. |
| SSISPackageExecutionCancelled         | 취소된 SSIS 패키지 실행 메트릭  | 개수    | 합계                | 1분 기간 내에 취소된 SSIS 패키지 실행의 총 수입니다. |
| SSISPackageExecutionFailed           | 실패한 SSIS 패키지 실행 메트릭    | 개수    | 합계                | 1분 기간 내에 실패한 SSIS 패키지 실행의 총 수입니다. |
| SSISPackageExecutionSucceeded        | 성공한 SSIS 패키지 실행 메트릭 | 개수    | 합계                | 1분 기간 내에 성공한 SSIS 패키지 실행의 총 수입니다. |

이 메트릭에 액세스하려면 [Azure Monitor 데이터 플랫폼](../azure-monitor/data-platform.md)의 지침을 완료하세요.

> [!NOTE]
> 완료되고, 트리거된 활동 및 파이프라인 실행의 이벤트만 내보내집니다. 진행 중 및 디버그 실행은 내보내지지 **않습니다**. 반면, **모든** SSIS 패키지 실행의 이벤트는 호출 메서드에 관계 없이 완료된 이벤트 및 진행 중인 이벤트를 포함하여 내보내집니다. 예를 들어, Azure 지원 SSDT(SQL Server Data Tools)에서 패키지 실행은 SSMS, SQL Server 에이전트 또는 기타 지정된 도구의 T-SQL을 통해, ADF 파이프라인에서 SSIS 패키지 실행 활동의 트리거된 실행 또는 디버그 실행으로 호출할 수 있습니다.

## <a name="data-factory-alerts"></a>Data Factory 경고

Azure Portal에 로그인하고 **모니터** > **경고** 를 선택하여 경고를 만듭니다.

![포털 메뉴의 경고](media/monitor-using-azure-monitor/alerts_image3.png)

### <a name="create-alerts"></a>경고 만들기

1. **+ 새로운 경고 규칙** 을 선택하여 새 경고를 만듭니다.

    ![새 경고 규칙](media/monitor-using-azure-monitor/alerts_image4.png)

1. 경고 조건을 정의합니다.

    > [!NOTE]
    > **리소스 종류별로 필터링** 드롭다운 목록에서 **모두** 를 선택해야 합니다.

    !["경고 조건 정의" > "대상 선택"을 선택하면 "리소스 선택" 창이 열림 ](media/monitor-using-azure-monitor/alerts_image5.png)

    !["경고 조건 정의" > "조건 추가"를 선택하면 "신호 논리 구성" 창이 열림](media/monitor-using-azure-monitor/alerts_image6.png)

    !["신호 유형 구성" 창](media/monitor-using-azure-monitor/alerts_image7.png)

1. 경고 세부 정보를 정의합니다.

    ![경고 세부 정보](media/monitor-using-azure-monitor/alerts_image8.png)

1. 작업 그룹을 정의합니다.

    !["새 작업 그룹"이 강조 표시된 규칙 만들기](media/monitor-using-azure-monitor/alerts_image9.png)

    ![새 작업 그룹 만들기](media/monitor-using-azure-monitor/alerts_image10.png)

    ![이메일, SMS, 푸시 및 음성 구성](media/monitor-using-azure-monitor/alerts_image11.png)

    ![작업 그룹 정의](media/monitor-using-azure-monitor/alerts_image12.png)

## <a name="set-up-diagnostic-logs-via-the-azure-monitor-rest-api"></a>Azure Monitor REST API를 통해 진단 로그 설정

### <a name="diagnostic-settings"></a>진단 설정

진단 설정을 사용하여 비컴퓨팅 리소스에 대한 진단 로그를 구성합니다. 리소스 컨트롤에 대한 설정에는 다음과 같은 기능이 있습니다.

* 진단 로그를 보낼 위치를 지정합니다. 예를 들면 Azure 스토리지 계정, Azure 이벤트 허브 또는 모니터 로그가 있습니다.
* 전송되는 로그 범주를 지정합니다.
* 각 로그 범주를 스토리지 계정에 유지해야 하는 기간을 지정합니다.
* 보존 기간이 0일이면 로그가 영구적으로 유지됩니다. 그렇지 않으면 값은 1에서 2,147,483,647 사이의 날짜 수일 수 있습니다.
* 보존 정책이 설정되어도 스토리지 계정에 로그 저장이 가능하도록 설정되지 않으면 보존 정책은 효과가 없습니다. 예를 들어 이런 조건은 Event Hubs 또는 Monitor 로그 옵션만 선택한 경우 발생할 수 있습니다.
* 보존 정책은 하루 단위로 적용됩니다. 하루는 UTC(협정 세계시) 자정을 기준으로 구분됩니다. 하루가 끝날 때 경과 일수가 보존 정책을 초과한 로그는 삭제됩니다. 예를 들어, 보존 정책이 1일이면 오늘이 시작될 때 어제 이전의 로그는 삭제됩니다.

### <a name="enable-diagnostic-logs-via-the-azure-monitor-rest-api"></a>Azure Monitor REST API를 통해 진단 로그를 사용하도록 설정

#### <a name="create-or-update-a-diagnostics-setting-in-the-monitor-rest-api"></a>Monitor REST API에서 진단 설정 만들기 또는 업데이트

##### <a name="request"></a>요청

```
PUT
https://management.azure.com/{resource-id}/providers/microsoft.insights/diagnosticSettings/service?api-version={api-version}
```

##### <a name="headers"></a>헤더

* `{api-version}`을 `2016-09-01`로 바꿉니다.
* `{resource-id}`를 진단 설정을 편집하려는 리소스의 ID로 대체합니다. 자세한 내용은 [리소스 그룹을 사용하여 Azure 리소스 관리](../azure-resource-manager/management/manage-resource-groups-portal.md)(영문)를 참조하세요.
* `Content-Type` 헤더를 `application/json`으로 설정합니다.
* 인증 헤더를 Azure AD(Azure Active Directory)에서 가져온 JSON 웹 토큰으로 설정합니다. 자세한 내용은 [요청 인증](../active-directory/develop/authentication-vs-authorization.md)을 참조하세요.

##### <a name="body"></a>본문

```json
{
    "properties": {
        "storageAccountId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>/providers/Microsoft.Storage/storageAccounts/<storageAccountName>",
        "serviceBusRuleId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>/providers/Microsoft.EventHub/namespaces/<eventHubName>/authorizationrules/RootManageSharedAccessKey",
        "workspaceId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<LogAnalyticsName>",
        "metrics": [
        ],
        "logs": [
                {
                    "category": "PipelineRuns",
                    "enabled": true,
                    "retentionPolicy": {
                        "enabled": false,
                        "days": 0
                    }
                },
                {
                    "category": "TriggerRuns",
                    "enabled": true,
                    "retentionPolicy": {
                        "enabled": false,
                        "days": 0
                    }
                },
                {
                    "category": "ActivityRuns",
                    "enabled": true,
                    "retentionPolicy": {
                        "enabled": false,
                        "days": 0
                    }
                }
            ]
    },
    "location": ""
}
```

| 속성 | 형식 | Description |
| --- | --- | --- |
| **storageAccountId** |String | 진단 로그를 보내려는 스토리지 계정의 리소스 ID입니다. |
| **serviceBusRuleId** |String | 진단 로그 스트리밍을 위해 Event Hubs를 만들려는 서비스 버스 네임스페이스의 서비스 버스 규칙 ID입니다. 규칙 ID의 형식은 `{service bus resource ID}/authorizationrules/{key name}`입니다.|
| **workspaceId** | String | 로그가 저장될 작업 영역의 작업 영역 ID입니다. |
|**메트릭**| 호출된 파이프라인에 전달될 파이프라인 실행의 매개 변수 값| 매개 변수 이름을 인수 값에 매핑하는 JSON 개체입니다. |
| **로그**| 복합 형식| 리소스 종류에 대한 진단 로그 범주의 이름입니다. 리소스에 대한 진단 로그 범주 목록을 가져오려면 GET 진단 설정 작업을 수행합니다. |
| **category**| String| 로그 범주 및 해당 보존 정책의 배열입니다. |
| **timeGrain** | String | ISO 8601 기간 형식으로 캡처되는 메트릭의 세분성입니다. 속성 값은 1분을 지정하는 `PT1M`이어야 합니다. |
| **enabled**| 부울 | 이 리소스에 대해 메트릭 또는 로그 범주 수집을 사용할지 여부를 지정합니다. |
| **retentionPolicy**| 복합 형식| 메트릭 또는 로그 범주에 대한 보존 정책을 설명합니다. 이 속성은 스토리지 계정에만 사용됩니다. |
|**days**| Int| 메트릭 또는 로그를 보관할 일 수입니다. 속성 값이 0이면 로그가 영구적으로 보관됩니다. 이 속성은 스토리지 계정에만 사용됩니다. |

##### <a name="response"></a>응답

200 정상입니다.

```json
{
    "id": "/subscriptions/<subID>/resourcegroups/adf/providers/microsoft.datafactory/factories/shloadobetest2/providers/microsoft.insights/diagnosticSettings/service",
    "type": null,
    "name": "service",
    "location": null,
    "kind": null,
    "tags": null,
    "properties": {
        "storageAccountId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>//providers/Microsoft.Storage/storageAccounts/<storageAccountName>",
        "serviceBusRuleId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>//providers/Microsoft.EventHub/namespaces/<eventHubName>/authorizationrules/RootManageSharedAccessKey",
        "workspaceId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>//providers/Microsoft.OperationalInsights/workspaces/<LogAnalyticsName>",
        "eventHubAuthorizationRuleId": null,
        "eventHubName": null,
        "metrics": [],
        "logs": [
            {
                "category": "PipelineRuns",
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            },
            {
                "category": "TriggerRuns",
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            },
            {
                "category": "ActivityRuns",
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            }
        ]
    },
    "identity": null
}
```

#### <a name="get-information-about-diagnostics-settings-in-the-monitor-rest-api"></a>Monitor REST API에서 진단 설정에 대한 정보 가져오기

##### <a name="request"></a>요청

```
GET
https://management.azure.com/{resource-id}/providers/microsoft.insights/diagnosticSettings/service?api-version={api-version}
```

##### <a name="headers"></a>헤더

* `{api-version}`을 `2016-09-01`로 바꿉니다.
* `{resource-id}`를 진단 설정을 편집하려는 리소스의 ID로 대체합니다. 자세한 내용은 [리소스 그룹을 사용하여 Azure 리소스 관리](../azure-resource-manager/management/manage-resource-groups-portal.md)(영문)를 참조하세요.
* `Content-Type` 헤더를 `application/json`으로 설정합니다.
* 인증 헤더를 Azure AD에서 가져온 JSON 웹 토큰으로 설정합니다. 자세한 내용은 [요청 인증](../active-directory/develop/authentication-vs-authorization.md)을 참조하세요.

##### <a name="response"></a>응답

200 정상입니다.

```json
{
    "id": "/subscriptions/<subID>/resourcegroups/adf/providers/microsoft.datafactory/factories/shloadobetest2/providers/microsoft.insights/diagnosticSettings/service",
    "type": null,
    "name": "service",
    "location": null,
    "kind": null,
    "tags": null,
    "properties": {
        "storageAccountId": "/subscriptions/<subID>/resourceGroups/shloprivate/providers/Microsoft.Storage/storageAccounts/azmonlogs",
        "serviceBusRuleId": "/subscriptions/<subID>/resourceGroups/shloprivate/providers/Microsoft.EventHub/namespaces/shloeventhub/authorizationrules/RootManageSharedAccessKey",
        "workspaceId": "/subscriptions/<subID>/resourceGroups/ADF/providers/Microsoft.OperationalInsights/workspaces/mihaipie",
        "eventHubAuthorizationRuleId": null,
        "eventHubName": null,
        "metrics": [],
        "logs": [
            {
                "category": "PipelineRuns",
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            },
            {
                "category": "TriggerRuns",
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            },
            {
                "category": "ActivityRuns",
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            }
        ]
    },
    "identity": null
}
```
자세한 내용은 [진단 설정](/rest/api/monitor/diagnosticsettings)을 참조하세요.

## <a name="schema-of-logs-and-events"></a>로그 및 이벤트 스키마

### <a name="monitor-schema"></a>모니터 스키마

#### <a name="activity-run-log-attributes"></a>활동 실행 로그 특성

```json
{
   "Level": "",
   "correlationId":"",
   "time":"",
   "activityRunId":"",
   "pipelineRunId":"",
   "resourceId":"",
   "category":"ActivityRuns",
   "level":"Informational",
   "operationName":"",
   "pipelineName":"",
   "activityName":"",
   "start":"",
   "end":"",
   "properties":
       {
          "Input": "{
              "source": {
                "type": "BlobSource"
              },
              "sink": {
                "type": "BlobSink"
              }
           }",
          "Output": "{"dataRead":121,"dataWritten":121,"copyDuration":5,
               "throughput":0.0236328132,"errors":[]}",
          "Error": "{
              "errorCode": "null",
              "message": "null",
              "failureType": "null",
              "target": "CopyBlobtoBlob"
        }
    }
}
```

| 속성 | 형식 | 설명 | 예제 |
| --- | --- | --- | --- |
| **수준** |String | 진단 로그의 수준입니다. 활동 실행 로그의 경우 속성 값을 4로 설정합니다. | `4` |
| **correlationId** |String | 특정 요청을 추적하기 위한 고유 ID입니다. | `319dc6b4-f348-405e-b8d7-aafc77b73e77` |
| **time** | String | 이벤트 시간(시간대 UTC 형식: `YYYY-MM-DDTHH:MM:SS.00000Z`)입니다. | `2017-06-28T21:00:27.3534352Z` |
|**activityRunId**| String| 활동 실행의 ID입니다. | `3a171e1f-b36e-4b80-8a54-5625394f4354` |
|**pipelineRunId**| String| 파이프라인 실행의 ID입니다. | `9f6069d6-e522-4608-9f99-21807bfc3c70` |
|**resourceId**| String | 데이터 팩터리 리소스와 연결된 ID입니다. | `/SUBSCRIPTIONS/<subID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |
|**category**| String | 진단 로그의 범주입니다. 속성 값을 `ActivityRuns`로 설정합니다. | `ActivityRuns` |
|**level**| String | 진단 로그의 수준입니다. 속성 값을 `Informational`로 설정합니다. | `Informational` |
|**operationName**| String | 활동의 이름과 그 상태입니다. 활동이 시작 하트비트인 경우 속성 값은 `MyActivity -`입니다. 활동이 끝 하트비트인 경우 속성 값은 `MyActivity - Succeeded`입니다. | `MyActivity - Succeeded` |
|**pipelineName**| String | 파이프라인의 이름 | `MyPipeline` |
|**activityName**| String | 작업의 이름입니다. | `MyActivity` |
|**start**| String | 활동 실행의 시작 시간(시간대 UTC 형식)입니다. | `2017-06-26T20:55:29.5007959Z`|
|**end**| String | 활동 실행의 종료 시간(시간대 UTC 형식)입니다. 진단 로그에 활동이 시작되었지만 아직 종료되지 않았다고 표시되는 경우 속성 값은 `1601-01-01T00:00:00Z`입니다. | `2017-06-26T20:55:29.5007959Z` |

#### <a name="pipeline-run-log-attributes"></a>파이프라인 실행 로그 특성

```json
{
   "Level": "",
   "correlationId":"",
   "time":"",
   "runId":"",
   "resourceId":"",
   "category":"PipelineRuns",
   "level":"Informational",
   "operationName":"",
   "pipelineName":"",
   "start":"",
   "end":"",
   "status":"",
   "properties":
    {
      "Parameters": {
        "<parameter1Name>": "<parameter1Value>"
      },
      "SystemParameters": {
        "ExecutionStart": "",
        "TriggerId": "",
        "SubscriptionId": ""
      }
    }
}
```

| 속성 | 형식 | 설명 | 예제 |
| --- | --- | --- | --- |
| **수준** |String | 진단 로그의 수준입니다. 활동 실행 로그의 경우 속성 값을 4로 설정합니다. | `4` |
| **correlationId** |String | 특정 요청을 추적하기 위한 고유 ID입니다. | `319dc6b4-f348-405e-b8d7-aafc77b73e77` |
| **time** | String | 이벤트 시간(시간대 UTC 형식: `YYYY-MM-DDTHH:MM:SS.00000Z`)입니다. | `2017-06-28T21:00:27.3534352Z` |
|**runId**| String| 파이프라인 실행의 ID입니다. | `9f6069d6-e522-4608-9f99-21807bfc3c70` |
|**resourceId**| String | 데이터 팩터리 리소스와 연결된 ID입니다. | `/SUBSCRIPTIONS/<subID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |
|**category**| String | 진단 로그의 범주입니다. 속성 값을 `PipelineRuns`로 설정합니다. | `PipelineRuns` |
|**level**| String | 진단 로그의 수준입니다. 속성 값을 `Informational`로 설정합니다. | `Informational` |
|**operationName**| String | 파이프라인의 이름과 상태입니다. 파이프라인 실행이 완료되면 속성 값은 `Pipeline - Succeeded`입니다. | `MyPipeline - Succeeded`. |
|**pipelineName**| String | 파이프라인의 이름 | `MyPipeline` |
|**start**| String | 활동 실행의 시작 시간(시간대 UTC 형식)입니다. | `2017-06-26T20:55:29.5007959Z`. |
|**end**| String | 활동 실행의 종료 시간(시간대 UTC 형식)입니다. 진단 로그에 활동이 시작되었지만 아직 종료되지 않은 것으로 표시되면 속성 값은 `1601-01-01T00:00:00Z`입니다.  | `2017-06-26T20:55:29.5007959Z` |
|**status**| String | 파이프라인 실행의 최종 상태입니다. 가능한 속성 값은 `Succeeded` 및 `Failed`입니다. | `Succeeded`|

#### <a name="trigger-run-log-attributes"></a>트리거 실행 로그 특성

```json
{
   "Level": "",
   "correlationId":"",
   "time":"",
   "triggerId":"",
   "resourceId":"",
   "category":"TriggerRuns",
   "level":"Informational",
   "operationName":"",
   "triggerName":"",
   "triggerType":"",
   "triggerEvent":"",
   "start":"",
   "status":"",
   "properties":
   {
      "Parameters": {
        "TriggerTime": "",
       "ScheduleTime": ""
      },
      "SystemParameters": {}
    }
}
```

| 속성 | 형식 | 설명 | 예제 |
| --- | --- | --- | --- |
| **수준** |String | 진단 로그의 수준입니다. 활동 실행 로그의 경우 속성 값을 4로 설정합니다. | `4` |
| **correlationId** |String | 특정 요청을 추적하기 위한 고유 ID입니다. | `319dc6b4-f348-405e-b8d7-aafc77b73e77` |
| **time** | String | 이벤트 시간(시간대 UTC 형식: `YYYY-MM-DDTHH:MM:SS.00000Z`)입니다. | `2017-06-28T21:00:27.3534352Z` |
|**triggerId**| String| 트리거 실행의 ID입니다. | `08587023010602533858661257311` |
|**resourceId**| String | 데이터 팩터리 리소스와 연결된 ID입니다. | `/SUBSCRIPTIONS/<subID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |
|**category**| String | 진단 로그의 범주입니다. 속성 값을 `PipelineRuns`로 설정합니다. | `PipelineRuns` |
|**level**| String | 진단 로그의 수준입니다. 속성 값을 `Informational`로 설정합니다. | `Informational` |
|**operationName**| String | 트리거의 이름과 트리거가 성공적으로 실행되었는지 여부를 나타내는 최종 상태입니다. 하트비트가 성공한 경우 속성 값은 `MyTrigger - Succeeded`입니다. | `MyTrigger - Succeeded` |
|**triggerName**| String | 트리거의 이름입니다. | `MyTrigger` |
|**triggerType**| String | 트리거의 유형입니다. 가능한 속성 값은 `Manual Trigger` 및 `Schedule Trigger`입니다. | `ScheduleTrigger` |
|**triggerEvent**| String | 트리거의 이벤트입니다. | `ScheduleTime - 2017-07-06T01:50:25Z` |
|**start**| String | 트리거 실행 시작 시간(시간대 UTC 형식)입니다. | `2017-06-26T20:55:29.5007959Z`|
|**status**| String | 트리거가 성공적으로 실행되었는지 여부를 보여주는 최종 상태입니다. 가능한 속성 값은 `Succeeded` 및 `Failed`입니다. | `Succeeded`|

#### <a name="ssis-integration-runtime-log-attributes"></a>SSIS 통합 런타임 로그 특성

SSIS IR 시작/중지/유지 관리 작업의 로그 특성은 다음과 같습니다.

```json
{
   "time": "",
   "operationName": "",
   "category": "",
   "correlationId": "",
   "dataFactoryName": "",
   "integrationRuntimeName": "",
   "level": "",
   "resultType": "",
   "properties": {
      "message": ""
   },
   "resourceId": ""
}
```

| 속성                   | 형식   | 설명                                                   | 예제                        |
| -------------------------- | ------ | ------------------------------------------------------------- | ------------------------------ |
| **time**                   | String | 이벤트 시간(UTC 형식: `YYYY-MM-DDTHH:MM:SS.00000Z`) | `2017-06-28T21:00:27.3534352Z` |
| **operationName**          | String | SSIS IR 작업의 이름                            | `Start/Stop/Maintenance/Heartbeat` |
| **category**               | String | 진단 로그의 범주                               | `SSISIntegrationRuntimeLogs` |
| **correlationId**          | String | 특정 작업을 추적하기 위한 고유 ID             | `f13b159b-515f-4885-9dfa-a664e949f785Deprovision0059035558` |
| **dataFactoryName**        | String | ADF의 이름                                          | `MyADFv2` |
| **integrationRuntimeName** | String | SSIS IR의 이름                                      | `MySSISIR` |
| **level**                  | String | 진단 로그의 수준                                  | `Informational` |
| **resultType**             | String | SSIS IR 작업의 결과                          | `Started/InProgress/Succeeded/Failed/Healthy/Unhealthy` |
| **message**                | String | SSIS IR 작업의 출력 메시지                  | `The stopping of your SSIS integration runtime has succeeded.` |
| **resourceId**             | String | ADF 리소스의 고유 ID                            | `/SUBSCRIPTIONS/<subscriptionID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |

#### <a name="ssis-event-message-context-log-attributes"></a>SSIS 이벤트 메시지 컨텍스트 로그 특성

SSIS IR에서 SSIS 패키지 실행을 통해 생성되는 이벤트 메시지와 관련된 조건의 로그 특성은 다음과 같습니다. 많은 SSIS 패키지 속성의 런타임 값을 보여주는 [SSIS 카탈로그(SSISDB) 이벤트 메시지 컨텍스트 테이블 또는 뷰](/sql/integration-services/system-views/catalog-event-message-context)와 유사한 정보를 전달합니다. `Basic/Verbose` 로깅 수준을 선택하면 생성되고 디버깅/준수 검사에 유용합니다.

```json
{
   "time": "",
   "operationName": "",
   "category": "",
   "correlationId": "",
   "dataFactoryName": "",
   "integrationRuntimeName": "",
   "level": "",
   "properties": {
      "operationId": "",
      "contextDepth": "",
      "packagePath": "",
      "contextType": "",
      "contextSourceName": "",
      "contextSourceId": "",
      "propertyName": "",
      "propertyValue": ""
   },
   "resourceId": ""
}
```

| 속성                   | 형식   | 설명                                                          | 예제                        |
| -------------------------- | ------ | -------------------------------------------------------------------- | ------------------------------ |
| **time**                   | String | 이벤트 시간(UTC 형식: `YYYY-MM-DDTHH:MM:SS.00000Z`)        | `2017-06-28T21:00:27.3534352Z` |
| **operationName**          | String | `YourSSISIRName-SSISPackageEventMessageContext`로 설정됩니다.       | `mysqlmissisir-SSISPackageEventMessageContext` |
| **category**               | String | 진단 로그의 범주                                      | `SSISPackageEventMessageContext` |
| **correlationId**          | String | 특정 작업을 추적하기 위한 고유 ID                    | `e55700df-4caf-4e7c-bfb8-78ac7d2f28a0` |
| **dataFactoryName**        | String | ADF의 이름                                                 | `MyADFv2` |
| **integrationRuntimeName** | String | SSIS IR의 이름                                             | `MySSISIR` |
| **level**                  | String | 진단 로그의 수준                                         | `Informational` |
| **operationId**            | String | SSISDB에서 특정 작업을 추적하기 위한 고유 ID          | `1`(1은 SSISDB에 저장되지 **않은** 패키지/T-SQL을 통해 호출된 패키지와 관련된 작업을 나타냄) |
| **contextDepth**           | String | 이벤트 메시지 컨텍스트의 깊이                              | `0`(0은 패키지 실행이 시작되기 전 컨텍스트를 나타냄, 1은 오류 발생시 컨텍스트를 나타냄, 컨텍스트가 오류에서 멀어질수록 증가함) |
| **packagePath**            | String | 이벤트 메시지 컨텍스트 원본인 패키지 개체의 경로      | `\Package` |
| **contextType**            | String | 이벤트 메시지 컨텍스트 원본인 패키지 개체의 유형      | `60`([더 많은 컨텍스트 유형](/sql/integration-services/system-views/catalog-event-message-context#remarks) 보기) |
| **contextSourceName**      | String | 이벤트 메시지 컨텍스트 원본인 패키지 개체의 이름      | `MyPackage` |
| **contextSourceId**        | String | 이벤트 메시지 컨텍스트 원본인 패키지 개체의 고유 ID | `{E2CF27FB-EA48-41E9-AF6F-3FE938B4ADE1}` |
| **propertyName**           | String | 이벤트 메시지 컨텍스트 원본의 패키지 속성 이름   | `DelayValidation` |
| **propertyValue**          | String | 이벤트 메시지 컨텍스트 원본의 패키지 속성 값  | `False` |
| **resourceId**             | String | ADF 리소스의 고유 ID                                   | `/SUBSCRIPTIONS/<subscriptionID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |

#### <a name="ssis-event-messages-log-attributes"></a>SSIS 이벤트 메시지 로그 특성

SSIS IR에서 SSIS 패키지 실행을 통해 생성되는 이벤트 메시지의 로그 특성은 다음과 같습니다. 이벤트 메시지의 자세한 텍스트/메타데이터를 보여주는 [SSISDB 이벤트 메시지 테이블 또는 뷰](/sql/integration-services/system-views/catalog-event-messages)와 유사한 정보를 전달합니다. `None`을 제외한 모든 로깅 수준에서 생성됩니다.

```json
{
   "time": "",
   "operationName": "",
   "category": "",
   "correlationId": "",
   "dataFactoryName": "",
   "integrationRuntimeName": "",
   "level": "",
   "properties": {
      "operationId": "",
      "messageTime": "",
      "messageType": "",
      "messageSourceType": "",
      "message": "",
      "packageName": "",
      "eventName": "",
      "messageSourceName": "",
      "messageSourceId": "",
      "subcomponentName": "",
      "packagePath": "",
      "executionPath": "",
      "threadId": ""
   }
}
```

| 속성                   | 형식   | 설명                                                        | 예제                        |
| -------------------------- | ------ | ------------------------------------------------------------------ | ------------------------------ |
| **time**                   | String | 이벤트 시간(UTC 형식: `YYYY-MM-DDTHH:MM:SS.00000Z`)      | `2017-06-28T21:00:27.3534352Z` |
| **operationName**          | String | `YourSSISIRName-SSISPackageEventMessages`로 설정됩니다.           | `mysqlmissisir-SSISPackageEventMessages` |
| **category**               | String | 진단 로그의 범주                                    | `SSISPackageEventMessages` |
| **correlationId**          | String | 특정 작업을 추적하기 위한 고유 ID                  | `e55700df-4caf-4e7c-bfb8-78ac7d2f28a0` |
| **dataFactoryName**        | String | ADF의 이름                                               | `MyADFv2` |
| **integrationRuntimeName** | String | SSIS IR의 이름                                           | `MySSISIR` |
| **level**                  | String | 진단 로그의 수준                                       | `Informational` |
| **operationId**            | String | SSISDB에서 특정 작업을 추적하기 위한 고유 ID        | `1`(1은 SSISDB에 저장되지 **않은** 패키지/T-SQL을 통해 호출된 패키지와 관련된 작업을 나타냄) |
| **messageTime**            | String | 이벤트 메시지가 생성된 시간(UTC 형식)          | `2017-06-28T21:00:27.3534352Z` |
| **messageType**            | String | 이벤트 메시지의 유형                                     | `70`([더 많은 메시지 유형](/sql/integration-services/system-views/catalog-operation-messages-ssisdb-database#remarks) 보기) |
| **messageSourceType**      | String | 이벤트 메시지 원본의 유형                              | `20`([더 많은 이벤트 메시지 원본 유형](/sql/integration-services/system-views/catalog-operation-messages-ssisdb-database#remarks) 보기) |
| **message**                | String | 이벤트 메시지의 텍스트                                     | `MyPackage:Validation has started.` |
| **packageName**            | String | 실행된 패키지 파일의 이름                             | `MyPackage.dtsx` |
| **eventName**              | String | 관련 런타임 이벤트의 이름                                 | `OnPreValidate` |
| **messageSourceName**      | String | 이벤트 메시지 원본인 패키지 구성 요소의 이름         | `Data Flow Task` |
| **messageSourceId**        | String | 이벤트 메시지 원본인 패키지 구성 요소의 고유 ID    | `{1a45a5a4-3df9-4f02-b818-ebf583829ad2}    ` |
| **subcomponentName**       | String | 이벤트 메시지 원본인 데이터 흐름 구성 요소의 이름       | `SSIS.Pipeline` |
| **packagePath**            | String | 이벤트 메시지 원본인 패키지 개체의 경로            | `\Package\Data Flow Task` |
| **executionPath**          | String | 부모 패키지에서 실행된 구성 요소까지의 전체 경로            | `\Transformation\Data Flow Task`(이 경로는 구성 요소 반복도 캡처함) |
| **threadId**               | String | 이벤트 메시지가 로그될 때 실행되는 스레드의 고유 ID | `{1a45a5a4-3df9-4f02-b818-ebf583829ad2}    ` |

#### <a name="ssis-executable-statistics-log-attributes"></a>SSIS 실행 파일 통계 로그 특성

SSIS IR에서 SSIS 패키지 실행을 통해 생성되는 실행 파일 통계의 로그 특성은 다음과 같습니다. 여기서 실행 파일은 패키지 제어 흐름의 컨테이너 또는 태스크입니다. 실행 중인 각 실행 파일에 대한 행을 보여주는 [SSISDB 실행 파일 통계 테이블 또는 뷰](/sql/integration-services/system-views/catalog-executable-statistics)와 유사한 정보를 반복을 포함하여 전달합니다. `None`을 제외한 모든 로깅 수준에서 생성되며 작업 수준 병목 상태/실패를 식별하는 데 유용합니다.

```json
{
   "time": "",
   "operationName": "",
   "category": "",
   "correlationId": "",
   "dataFactoryName": "",
   "integrationRuntimeName": "",
   "level": "",
   "properties": {
      "executionId": "",
      "executionPath": "",
      "startTime": "",
      "endTime": "",
      "executionDuration": "",
      "executionResult": "",
      "executionValue": ""
   },
   "resourceId": ""
}
```

| 속성                   | 형식   | 설명                                                      | 예제                        |
| -------------------------- | ------ | ---------------------------------------------------------------- | ------------------------------ |
| **time**                   | String | 이벤트 시간(UTC 형식: `YYYY-MM-DDTHH:MM:SS.00000Z`)    | `2017-06-28T21:00:27.3534352Z` |
| **operationName**          | String | `YourSSISIRName-SSISPackageExecutableStatistics`로 설정됩니다.  | `mysqlmissisir-SSISPackageExecutableStatistics` |
| **category**               | String | 진단 로그의 범주                                  | `SSISPackageExecutableStatistics` |
| **correlationId**          | String | 특정 작업을 추적하기 위한 고유 ID                | `e55700df-4caf-4e7c-bfb8-78ac7d2f28a0` |
| **dataFactoryName**        | String | ADF의 이름                                             | `MyADFv2` |
| **integrationRuntimeName** | String | SSIS IR의 이름                                         | `MySSISIR` |
| **level**                  | String | 진단 로그의 수준                                     | `Informational` |
| **executionId**            | String | SSISDB에서 특정 실행을 추적하기 위한 고유 ID      | `1`(1은 SSISDB에 저장되지 **않은** 패키지/T-SQL을 통해 호출된 패키지와 관련된 실행을 나타냄) |
| **executionPath**          | String | 부모 패키지에서 실행된 구성 요소까지의 전체 경로          | `\Transformation\Data Flow Task`(이 경로는 구성 요소 반복도 캡처함) |
| **startTime**              | String | 실행 파일이 실행 전 단계로 들어가는 시간(UTC 형식)  | `2017-06-28T21:00:27.3534352Z` |
| **endTime**                | String | 실행 파일이 실행 후 단계로 들어가는 시간(UTC 형식) | `2017-06-28T21:00:27.3534352Z` |
| **executionDuration**      | String | 실행 파일의 실행 시간(밀리초)                   | `1,125` |
| **executionResult**        | String | 실행 파일을 실행한 결과                                 | `0`(0은 성공, 1은 실패, 2는 완료, 3은 취소를 나타냄) |
| **executionValue**         | String | 실행 파일을 실행하여 반환된 사용자 정의 값            | `1` |
| **resourceId**             | String | ADF 리소스의 고유 ID                               | `/SUBSCRIPTIONS/<subscriptionID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |

#### <a name="ssis-execution-component-phases-log-attributes"></a>SSIS 실행 구성 요소 단계 로그 특성

SSIS IR에서 SSIS 패키지 실행을 통해 생성되는 데이터 흐름 구성 요소에 대한 런타임 통계의 로그 특성은 다음과 같습니다. 모든 실행 단계에서 데이터 흐름 구성 요소에 소요된 시간을 보여주는 [SSISDB 실행 구성 요소 단계 테이블 또는 뷰](/sql/integration-services/system-views/catalog-execution-component-phases)와 유사한 정보를 전달합니다. `Performance/Verbose` 로깅 수준을 선택하면 생성되며 데이터 흐름 실행 통계를 캡처하는 데 유용합니다.

```json
{
   "time": "",
   "operationName": "",
   "category": "",
   "correlationId": "",
   "dataFactoryName": "",
   "integrationRuntimeName": "",
   "level": "",
   "properties": {
      "executionId": "",
      "packageName": "",
      "taskName": "",
      "subcomponentName": "",
      "phase": "",
      "startTime": "",
      "endTime": "",
      "executionPath": ""
   },
   "resourceId": ""
}
```

| 속성                   | 형식   | 설명                                                         | 예제                        |
| -------------------------- | ------ | ------------------------------------------------------------------- | ------------------------------ |
| **time**                   | String | 이벤트 시간(UTC 형식: `YYYY-MM-DDTHH:MM:SS.00000Z`)       | `2017-06-28T21:00:27.3534352Z` |
| **operationName**          | String | `YourSSISIRName-SSISPackageExecutionComponentPhases`로 설정됩니다. | `mysqlmissisir-SSISPackageExecutionComponentPhases` |
| **category**               | String | 진단 로그의 범주                                     | `SSISPackageExecutionComponentPhases` |
| **correlationId**          | String | 특정 작업을 추적하기 위한 고유 ID                   | `e55700df-4caf-4e7c-bfb8-78ac7d2f28a0` |
| **dataFactoryName**        | String | ADF의 이름                                                | `MyADFv2` |
| **integrationRuntimeName** | String | SSIS IR의 이름                                            | `MySSISIR` |
| **level**                  | String | 진단 로그의 수준                                        | `Informational` |
| **executionId**            | String | SSISDB에서 특정 실행을 추적하기 위한 고유 ID         | `1`(1은 SSISDB에 저장되지 **않은** 패키지/T-SQL을 통해 호출된 패키지와 관련된 실행을 나타냄) |
| **packageName**            | String | 실행된 패키지 파일의 이름                              | `MyPackage.dtsx` |
| **taskName**               | String | 실행된 데이터 흐름 태스크의 이름                                 | `Data Flow Task` |
| **subcomponentName**       | String | 데이터 흐름 구성 요소의 이름                                     | `Derived Column` |
| **phase**                  | String | 실행 단계의 이름                                         | `AcquireConnections` |
| **startTime**              | String | 실행 단계가 시작되는 시간(UTC 형식)                  | `2017-06-28T21:00:27.3534352Z` |
| **endTime**                | String | 실행 단계가 끝나는 시간(UTC 형식)                    | `2017-06-28T21:00:27.3534352Z` |
| **executionPath**          | String | 데이터 흐름 태스크의 실행 경로                            | `\Transformation\Data Flow Task` |
| **resourceId**             | String | ADF 리소스의 고유 ID                                  | `/SUBSCRIPTIONS/<subscriptionID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |

#### <a name="ssis-execution-data-statistics-log-attributes"></a>SSIS 실행 데이터 통계 로그 특성

SSIS IR에서 SSIS 패키지 실행을 통해 생성되는 업스트림에서 다운스트림 구성 요소까지 데이터 흐름 파이프라인의 각 구간을 통과하는 데이터 이동의 로그 특성은 다음과 같습니다. 데이터 흐름 태스크를 통해 이동된 데이터의 행 수를 보여주는 [SSISDB 실행 데이터 통계 테이블 또는 뷰](/sql/integration-services/system-views/catalog-execution-data-statistics)와 유사한 정보를 전달합니다. `Verbose` 로깅 수준을 선택하면 생성되며 데이터 흐름 처리량을 계산하는 데 유용합니다.

```json
{
   "time": "",
   "operationName": "",
   "category": "",
   "correlationId": "",
   "dataFactoryName": "",
   "integrationRuntimeName": "",
   "level": "",
   "properties": {
      "executionId": "",
      "packageName": "",
      "taskName": "",
      "dataflowPathIdString": "",
      "dataflowPathName": "",
      "sourceComponentName": "",
      "destinationComponentName": "",
      "rowsSent": "",
      "createdTime": "",
      "executionPath": ""
   },
   "resourceId": ""
}
```

| 속성                     | 형식   | 설명                                                        | 예제                        |
| ---------------------------- | ------ | ------------------------------------------------------------------ | ------------------------------ |
| **time**                     | String | 이벤트 시간(UTC 형식: `YYYY-MM-DDTHH:MM:SS.00000Z`)      | `2017-06-28T21:00:27.3534352Z` |
| **operationName**            | String | `YourSSISIRName-SSISPackageExecutionDataStatistics`로 설정됩니다. | `mysqlmissisir-SSISPackageExecutionDataStatistics` |
| **category**                 | String | 진단 로그의 범주                                    | `SSISPackageExecutionDataStatistics` |
| **correlationId**            | String | 특정 작업을 추적하기 위한 고유 ID                  | `e55700df-4caf-4e7c-bfb8-78ac7d2f28a0` |
| **dataFactoryName**          | String | ADF의 이름                                               | `MyADFv2` |
| **integrationRuntimeName**   | String | SSIS IR의 이름                                           | `MySSISIR` |
| **level**                    | String | 진단 로그의 수준                                       | `Informational` |
| **executionId**              | String | SSISDB에서 특정 실행을 추적하기 위한 고유 ID        | `1`(1은 SSISDB에 저장되지 **않은** 패키지/T-SQL을 통해 호출된 패키지와 관련된 실행을 나타냄) |
| **packageName**              | String | 실행된 패키지 파일의 이름                             | `MyPackage.dtsx` |
| **taskName**                 | String | 실행된 데이터 흐름 태스크의 이름                                | `Data Flow Task` |
| **dataflowPathIdString**     | String | 데이터 흐름 경로 추적을 위한 고유 ID                          | `Paths[SQLDB Table3.ADO NET Source Output]` |
| **dataflowPathName**         | String | 데이터 흐름 경로의 이름                                         | `ADO NET Source Output` |
| **sourceComponentName**      | String | 데이터를 보내는 데이터 흐름 구성 요소의 이름                    | `SQLDB Table3` |
| **destinationComponentName** | String | 데이터를 받는 데이터 흐름 구성 요소의 이름                 | `Derived Column` |
| **rowsSent**                 | String | 원본 구성 요소가 보낸 행 수                        | `500` |
| **createdTime**              | String | 행 값을 얻은 시간(UTC 형식)                | `2017-06-28T21:00:27.3534352Z` |
| **executionPath**            | String | 데이터 흐름 태스크의 실행 경로                           | `\Transformation\Data Flow Task` |
| **resourceId**               | String | ADF 리소스의 고유 ID                                 | `/SUBSCRIPTIONS/<subscriptionID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |

### <a name="log-analytics-schema"></a>Log Analytics 스키마

Log Analytics는 Monitor에서 스키마를 상속하지만 다음과 같은 예외 사항이 있습니다.

* 각 열 이름의 첫 글자는 대문자로 표시됩니다. 예를 들어 Monitor의 열 이름 "correlationId"는 Log Analytics에서 "CorrelationId"입니다.
* "Level" 열이 없습니다.
* 동적 "properties" 열은 다음과 같은 동적 JSON Blob 유형으로 유지됩니다.

    | Azure Monitor 열 | Log Analytics 열 | 형식 |
    | --- | --- | --- |
    | $.properties.UserProperties | UserProperties | 동적 |
    | $.properties.Annotations | 주석 | 동적 |
    | $.properties.Input | 입력 | 동적 |
    | $.properties.Output | 출력 | 동적 |
    | $.properties.Error.errorCode | 오류 코드 | int |
    | $.properties.Error.message | ErrorMessage | 문자열 |
    | $.properties.Error | 오류 | 동적 |
    | $.properties.Predecessors | Predecessors | 동적 |
    | $.properties.Parameters | 매개 변수 | 동적 |
    | $.properties.SystemParameters | SystemParameters | 동적 |
    | $.properties.Tags | 태그 | 동적 |

## <a name="monitor-ssis-operations-with-azure-monitor"></a>Azure Monitor를 사용하여 SSIS 작업 모니터링

SSIS 워크로드를 리프트 앤 시프트하려면 다음을 지원하는 [ADF에서 SSIS IR을 프로비전](./tutorial-deploy-ssis-packages-azure.md)합니다.

- Azure SQL Database 서버/Managed Instance(프로젝트 배포 모델)가 호스트하는 SSIS 카탈로그(SSISDB)에 배포된 패키지 실행
- Azure SQL Managed Instance(패키지 배포 모델)가 호스트하는 파일 시스템, Azure Files 또는 SQL Server 데이터베이스(MSDB)에 배포된 패키지 실행

프로비전된 후에는 [Azure PowerShell을 사용하거나 ADF 포털의 **모니터** 허브에서 SSIS IR 운영 상태를 확인](./monitor-integration-runtime.md#azure-ssis-integration-runtime)할 수 있습니다. 프로젝트 배포 모델을 사용하면 SSIS 패키지 실행 로그가 SSISDB 내부 테이블 또는 뷰에 저장되므로 SSMS와 같은 지정된 도구를 사용하여 해당 로그를 쿼리, 분석 및 시각적으로 표현할 수 있습니다. 패키지 배포 모델을 사용하면 SSIS 패키지 실행 로그를 파일 시스템 또는 Azure Files에 CSV 파일로 저장할 수 있습니다. 이 로그는 다른 지정된 도구를 사용하여 구문 분석하고 처리한 후에야 쿼리, 분석 및 시각적 표현이 가능합니다.

이제는 [Azure Monitor](../azure-monitor/data-platform.md) 통합을 통해, Azure Portal의 SSIS IR 작업 및 SSIS 패키지 실행에서 생성된 모든 메트릭 및 로그를 쿼리, 분석 및 시각적으로 표현할 수 있습니다. 또한 경고를 발생시킬 수도 있습니다.

### <a name="configure-diagnostic-settings-and-workspace-for-ssis-operations"></a>SSIS 작업을 위한 진단 설정 및 작업 영역 구성

SSIS IR 작업 및 SSIS 패키지 실행에서 생성된 모든 메트릭과 로그를 Azure Monitor로 보내려면 [ADF에 대한 진단 설정 및 작업 영역을 구성](#configure-diagnostic-settings-and-workspace)해야 합니다.

### <a name="ssis-operational-metrics"></a>SSIS 운영 메트릭

SSIS 운영 [메트릭은](../azure-monitor/essentials/data-platform-metrics.md) SSIS IR 시작 및 중지 작업의 상태는 물론 특정 시점의 SSIS 패키지 실행을 설명하는 성능 카운터 또는 숫자 값입니다. [Azure Monitor의 ADF 메트릭](#data-factory-metrics)에 속합니다.

Azure Monitor에서 ADF에 대한 진단 설정 및 작업 영역을 구성할 때 _AllMetrics_ 확인란을 선택하면 SSIS 운영 메트릭을 [Azure Metrics Explorer를 사용한 대화형 분석](../azure-monitor/essentials/metrics-getting-started.md), [Azure 대시보드의 프레젠테이션](../azure-monitor/app/tutorial-app-dashboards.md) 및 [근 실시간 경고](../azure-monitor/alerts/alerts-metric.md)에 사용할 수 있습니다.

![설정 이름 지정 및 Log Analytics 작업 영역 선택](media/data-factory-monitor-oms/monitor-oms-image2.png)

### <a name="ssis-operational-alerts"></a>SSIS 운영 경고

ADF 포털에서 SSIS 운영 메트릭에 대한 경고를 발생시키려면 [ADF **모니터** 허브의 **경고 및 메트릭** 페이지를 선택하고 제공된 단계별 지침을 따릅니다](./monitor-visually.md#alerts).

![ADF 포털에서 SSIS 운영 경고 발생](media/data-factory-monitor-oms/data-factory-monitor-alerts-ssis.png)

Azure Portal에서 SSIS 운영 메트릭에 대한 경고를 발생시키려면 [Azure **모니터** 허브의 **경고** 페이지를 선택하고 제공된 단계별 지침을 따릅니다](#data-factory-alerts).

![Azure Portal에서 SSIS 운영 경고 발생](media/data-factory-monitor-oms/azure-monitor-alerts-ssis.png)

### <a name="ssis-operational-logs"></a>SSIS 운영 로그

SSIS 운영 [로그](../azure-monitor/logs/data-platform-logs.md)는 SSIS IR 운영 및 SSIS 패키지 실행에 의해 생성되는 이벤트이며, 식별된 문제에 대한 충분한 컨텍스트를 제공하고 근본 원인을 분석하는 데 유용합니다. 

Azure Monitor에서 ADF에 대한 진단 설정 및 작업 영역을 구성할 때 관련 SSIS 운영 로그를 선택하여 Azure Data Explorer를 기반으로 하는 Log Analytics에 보낼 수 있습니다. 여기에서 [풍부한 쿼리 언어를 사용한 분석](../azure-monitor/logs/log-query-overview.md), [Azure 대시보드의 프레젠테이션](../azure-monitor/app/tutorial-app-dashboards.md) 및 [근 실시간 경고](../azure-monitor/alerts/alerts-log.md)에 사용할 수 있습니다.

![설정 이름 지정 및 Log Analytics 작업 영역 선택](media/data-factory-monitor-oms/monitor-oms-image2.png)

Azure Monitor 및 Log Analytics에서 SSIS 패키지 실행 로그의 스키마 및 콘텐츠는 SSISDB 내부 테이블 또는 뷰의 스키마와 비슷합니다.

| Azure Monitor 로그 범주          | Log Analytics 테이블                     | SSISDB 내부 테이블/뷰              |
| ------------------------------------- | ---------------------------------------- | ----------------------------------------- |
| `SSISIntegrationRuntimeLogs`          | `ADFSSISIntegrationRuntimeLogs`          |                                           |
| `SSISPackageEventMessageContext`      | `ADFSSISPackageEventMessageContext`      | `[internal].[event_message_context]`      |
| `SSISPackageEventMessages`            | `ADFSSISPackageEventMessages`            | `[internal].[event_messages]`             |
| `SSISPackageExecutableStatistics`     | `ADFSSISPackageExecutableStatistics`     | `[internal].[executable_statistics]`      |
| `SSISPackageExecutionComponentPhases` | `ADFSSISPackageExecutionComponentPhases` | `[internal].[execution_component_phases]` |
| `SSISPackageExecutionDataStatistics`  | `ADFSSISPackageExecutionDataStatistics`  | `[internal].[execution_data_statistics]`  |

SSIS 운영 로그 특성/속성에 대한 자세한 내용은 [ADF의 Azure Monitor 및 Log Analytics 스키마](#schema-of-logs-and-events)를 참조하십시오.

선택한 SSIS 패키지 실행 로그는 호출 메서드에 관계 없이 항상 Log Analytics로 전송됩니다. 예를 들어, Azure 지원 SSDT에서 패키지 실행은 SSMS, SQL Server 에이전트 또는 기타 지정된 도구의 T-SQL을 통해, ADF 파이프라인에서 SSIS 패키지 실행 활동의 트리거된 실행 또는 디버그 실행으로 호출할 수 있습니다.

Logs Analytics에서 SSIS IR 작업 로그를 쿼리할 때는 각각 `Start/Stop/Maintenance/Heartbeat` 및 `Started/InProgress/Succeeded/Failed/Healthy/Unhealthy`로 설정 된 **OperationName** 및 **ResultType** 속성을 사용할 수 있습니다.

![Log Analytics에서 SSIS IR 작업 로그 쿼리](media/data-factory-monitor-oms/log-analytics-query.png)

SSIS IR 노드 상태를 쿼리하려면 **OperationName** 속성을 `Heartbeat`로 설정하면 됩니다. 각 노드는 일반적으로 상태를 반영하는 **ResultType** 속성을 사용하여 분당 하나의 `Heartbeat` 레코드를 Log Analytics에 보냅니다. 이 속성은 패키지 실행에 사용할 수 있는 경우 `Healthy`이고 그렇지 않은 경우 `Unhealthy`입니다. 예를 들어 SSIS IR에 사용 가능한 노드가 2개 있는 경우 1분 이내에 **ResultType** 속성이 `Healthy`로 설정된 `Heartbeat` 레코드 2개가 항상 표시됩니다.

![Log Analytics에서 SSIS IR 하트비트 쿼리](media/data-factory-monitor-oms/log-analytics-query-3.png)

다음 패턴을 쿼리하여 SSIS IR 노드의 비활성을 검색할 수 있습니다.

* SSIS IR이 아직 실행되고 있는 경우에는 1분 동안 누락된 `Heartbeat` 레코드가 있습니다.
* SSIS IR이 아직 실행되고 있는 경우에는 1분 동안 **ResultType** 속성이 `Unhealthy`로 설정된 `Heartbeat` 레코드가 있습니다.

위의 쿼리를 [경고](../azure-monitor/alerts/alerts-unified-log.md)로 전환하고 [SSIS IR 모니터링 페이지](monitor-integration-runtime.md#monitor-the-azure-ssis-integration-runtime-in-azure-portal)로 이동하여 해당 경고를 받은 시기를 확인할 수 있습니다.

Logs Analytics에서 SSIS 패키지 실행 로그를 쿼리할 때 **OperationId**/**ExecutionId**/**CorrelationId** 속성을 사용하여 조인할 수 있습니다. **OperationId**/**ExecutionId** 는 SSISDB에 저장되지 **않은**/T-SQL을 통해 호출된 패키지와 관련된 모든 작업/실행에 대해 항상 `1`로 설정됩니다.

![Log Analytics에서 SSIS 패키지 실행 로그 쿼리](media/data-factory-monitor-oms/log-analytics-query2.png)

## <a name="next-steps"></a>다음 단계
[프로그래밍 방식으로 파이프라인 모니터링 및 관리](monitor-programmatically.md)
