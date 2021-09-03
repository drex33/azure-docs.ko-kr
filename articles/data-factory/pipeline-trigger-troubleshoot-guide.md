---
title: Azure Data Factory에서 파이프라인 오케스트레이션 및 트리거 문제 해결
description: 여러 가지 방법을 통해 Azure Data Factory에서 파이프라인 트리거 문제를 해결할 수 있습니다.
author: ssabat
ms.service: data-factory
ms.date: 08/17/2021
ms.subservice: troubleshooting
ms.topic: troubleshooting
ms.author: susabat
ms.reviewer: susabat
ms.openlocfilehash: 29aff19ee900c8683249241725904e5509eced8b
ms.sourcegitcommit: 8000045c09d3b091314b4a73db20e99ddc825d91
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/19/2021
ms.locfileid: "122531207"
---
# <a name="troubleshoot-pipeline-orchestration-and-triggers-in-azure-data-factory"></a>Azure Data Factory에서 파이프라인 오케스트레이션 및 트리거 문제 해결

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Azure Data Factory의 파이프라인 실행은 파이프라인 실행의 인스턴스를 정의합니다. 예를 들어 오전 8시, 오전 9시, 오전 10시에 실행되는 파이프라인이 있다고 가정해보세요. 이 경우에는 개별적으로 3개의 파이프라인 실행이 있습니다. 각 파이프라인 실행에는 고유한 파이프라인 실행 ID가 있습니다. 실행 ID는 특정 파이프라인 실행을 정의하는 GUID(전역적으로 고유한 식별자)입니다.

파이프라인 실행은 일반적으로 파이프라인에 정의된 매개 변수에 인수를 전달하여 인스턴스화됩니다. 파이프라인은 수동으로 또는 트리거를 사용하여 실행할 수 있습니다. 자세한 내용은 [Azure Data Factory의 파이프라인 실행 및 트리거](concepts-pipeline-execution-triggers.md)를 참조하세요.

## <a name="common-issues-causes-and-solutions"></a>일반적인 문제, 원인 및 솔루션

### <a name="an-azure-functions-app-pipeline-throws-an-error-with-private-endpoint-connectivity"></a>Azure Functions 앱 파이프라인이 프라이빗 엔드포인트 연결에 오류 throw
 
Data Factory 및 함수 앱이 Azure의 프라이빗 엔드포인트에서 실행됩니다. 함수 앱과 상호 작용하는 파이프라인을 실행하려고 시도하는 중입니다. 세 가지 방법을 시도했지만, 한 가지 경우에 “잘못된 요청” 오류가 반환되고, 다른 두 경우에는 “103 금지된 오류”가 반환됩니다.

**원인**

Data Factory는 현재 함수 앱에 대해 프라이빗 엔드포인트 커넥터를 지원하지 않습니다. Azure Functions는 프라이빗 링크의 연결만 허용하도록 구성되어 있기 때문에 호출을 거절합니다.

**해결 방법**

**PrivateLinkService** 엔드포인트를 만들고 함수 앱의 DNS를 제공합니다.

### <a name="a-pipeline-run-is-canceled-but-the-monitor-still-shows-progress-status"></a>파이프라인 실행이 취소되지만 모니터에 계속 진행 상태가 표시됨

**원인**

파이프라인 실행을 취소해도 파이프라인 모니터링에 진행 상태가 계속 표시될 수 있습니다. 이 문제는 브라우저 캐시 문제로 인해 발생합니다. 모니터링 필터가 올바르지 않기 때문일 수도 있습니다.

**해결 방법**

브라우저를 새로 고침하고 올바른 모니터링 필터를 적용합니다.
 
### <a name="you-see-a-delimitedtextmorecolumnsthandefined-error-when-copying-a-pipeline"></a>파이프라인을 복사할 때 "DelimitedTextMoreColumnsThanDefined" 오류가 표시됨
 
 **원인**
 
가변 열 수, 서로 다른 구분 기호, 인용 문자 설정 또는 일부 데이터 문제와 같이 복사하려는 폴더에 스키마가 다른 파일이 포함되어 있으면 Data Factory 파이프라인이 다음 오류를 throw할 수 있습니다.

`
Operation on target Copy_sks  failed: Failure happened on 'Sink' side.
ErrorCode=DelimitedTextMoreColumnsThanDefined,
'Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,
Message=Error found when processing 'Csv/Tsv Format Text' source '0_2020_11_09_11_43_32.avro' with row number 53: found more columns than expected column count 27.,
Source=Microsoft.DataTransfer.Common,'
`

**해결 방법**

복사 작업을 만들 때 **이진 복사** 옵션을 선택합니다. 이렇게 하면 대량 복사 또는 한 데이터 레이크에서 다른 데이터 레이크로 데이터 마이그레이션을 수행할 때 Data Factory가 스키마를 읽기 위한 파일을 열지 않습니다. 대신 Data Factory가 각 파일을 이진으로 취급하고 이를 다른 위치에 복사합니다.

### <a name="a-pipeline-run-fails-when-you-reach-the-capacity-limit-of-the-integration-runtime-for-data-flow"></a>데이터 흐름에 대한 통합 런타임의 용량 제한에 도달하면 파이프라인 실행이 실패함

**문제점**

오류 메시지:

`
Type=Microsoft.DataTransfer.Execution.Core.ExecutionException,Message=There are substantial concurrent MappingDataflow executions which is causing failures due to throttling under Integration Runtime 'AutoResolveIntegrationRuntime'.
`

**원인**

통합 런타임의 용량 제한에 도달했습니다. 동시에 동일한 통합 런타임을 사용하여 대량의 데이터 흐름을 실행 중일 수 있습니다. 자세한 내용은 [Azure 구독 및 서비스 제한, 할당량 및 제약 조건](../azure-resource-manager/management/azure-subscription-service-limits.md#version-2)을 참조하세요.

**해결 방법**
 
- 서로 다른 트리거 시간에 파이프라인을 실행합니다.
- 새 통합 런타임을 만들고 여러 통합 런타임 간에 파이프라인을 분할합니다.

### <a name="a-pipeline-run-error-while-invoking-rest-api-in-a-web-activity"></a>웹 작업에서 REST API를 호출하는 동안 파이프라인 오류 발생

**문제점**

오류 메시지:

`
Operation on target Cancel failed: {“error”:{“code”:”AuthorizationFailed”,”message”:”The client ‘<client>’ with object id ‘<object>’ does not have authorization to perform action ‘Microsoft.DataFactory/factories/pipelineruns/cancel/action’ over scope ‘/subscriptions/<subscription>/resourceGroups/<resource group>/providers/Microsoft.DataFactory/factories/<data factory name>/pipelineruns/<pipeline run id>’ or the scope is invalid. If access was recently granted, please refresh your credentials.”}}
`

**원인**

파이프라인은 Azure Data Factory 구성원에 기여자 역할이 할당된 경우에만 웹 작업을 사용하여 ADF REST API 메서드를 호출할 수 있습니다. 먼저 Azure Data Factory에서 관리 ID를 구성하고 기여자 보안 역할에 추가해야 합니다. 

**해결 방법**

웹 작업의 설정 탭에서 Azure Data Factory의 REST API를 사용하기 전에 보안이 구성되어 있어야 합니다. Azure Data Factory 파이프라인은 Azure Data Factory에서 관리 ID에 *기여자* 역할이 할당된 경우에만 웹 작업을 사용하여 ADF REST API 메서드를 호출할 수 있습니다. 먼저 Azure Portal열 열고 왼쪽 메뉴에서 **모든 리소스** 링크를 클릭하여 시작합니다. *역할 할당 추가* 상자에서 **추가** 단추를 클릭하여 **Azure Data Factory** 를 선택하고 기여자 역할이 있는 ADF에서 관리 ID를 추가합니다.


### <a name="how-to-check-and-branch-on-activity-level-success-and-failure-in-pipelines"></a>파이프라인에서 활동 수준의 성공 및 실패를 확인하고 이에 따라 분기하는 방법

**원인**

Azure Data Factory 오케스트레이션은 조건부 논리를 허용하며 사용자가 이전 활동의 결과에 따라 다른 경로를 선택할 수 있습니다. 허용되는 조건부 경로는 **성공 시**(기본 경로), **실패 시**, **완료 시** 및 **건너뛰기 시** 의 4개입니다. 

Azure Data Factory는 모든 리프 수준 활동의 결과를 평가합니다. 파이프라인 결과는 모든 리프가 성공한 경우에만 성공합니다. 리프 활동을 건너뛰었으면 대신 해당 부모 활동을 평가합니다. 

**해결 방법**

* [파이프라인 실패 및 오류 처리 방법](https://techcommunity.microsoft.com/t5/azure-data-factory/understanding-pipeline-failures-and-error-handling/ba-p/1630459)에 따라 활동 수준 확인을 구현합니다.
* [Query By Factory](/rest/api/datafactory/pipelineruns/querybyfactory)에 따라 Azure Logic Apps를 사용하여 정기적으로 파이프라인을 모니터링합니다.
* [시각적으로 파이프라인 모니터링](monitor-visually.md)

### <a name="how-to-monitor-pipeline-failures-in-regular-intervals"></a>정기적으로 파이프라인 실패를 모니터링하는 방법

**원인**

5분 정도의 간격으로 실패한 Data Factory 파이프라인을 모니터링해야 할 수 있습니다. 엔드포인트를 사용하여 데이터 팩터리에서 파이프라인 실행을 쿼리하고 필터링할 수 있습니다. 

**해결 방법**
* [팩터리 기준 쿼리](/rest/api/datafactory/pipelineruns/querybyfactory)에 설명된 대로 5분 간격으로 실패한 파이프라인을 모두 쿼리하도록 Azure 논리 앱을 설정할 수 있습니다. 그런 후 인시던트를 티켓팅 시스템에 보고할 수 있습니다.
* [여기](monitor-visually.md#rerun-pipelines-and-activities)에 설명된 대로 파이프라인과 작업을 다시 실행할 수 있습니다.
* 작업을 취소했거나 실패한 경우 [작업 실패에서 재실행](monitor-visually.md#rerun-from-failed-activity)에 따라 작업을 다시 실행할 수 있습니다.
* [시각적으로 파이프라인 모니터링](monitor-visually.md)

### <a name="degree-of-parallelism--increase-does-not-result-in-higher-throughput"></a>병렬 처리 수준을 늘려도 처리량이 늘어나지 않음

**원인** 

*ForEach* 에서의 병렬 처리 수준은 실제로 최대 병렬 처리 수준입니다. 특정 횟수의 실행이 동시에 발생하는 것은 보장되지 않지만 이 매개 변수는 설정된 값을 초과하지 않도록 보장합니다. 원본 및 싱크에 대한 동시 액세스를 제어할 때 활용할 수 있는 제한으로 간주해야 합니다.

*ForEach* 에 대한 알려진 사실
 * Foreach에는 기본값이 20이고 최댓값이 50인 일괄 처리 수(n)라는 속성이 있습니다.
 * 일괄 처리 수 n은 n개 큐를 생성하기 위해 사용됩니다. 
 * 모든 큐는 순차적으로 실행되지만 여러 큐를 병렬로 실행할 수 있습니다.
 * 큐는 미리 생성됩니다. 즉, 런타임 중 큐에 대한 균형 조정이 수행되지 않습니다.
 * 언제든지 큐당 처리되는 항목은 최대 1개입니다. 즉, 언제든 최대 n개 항목만 처리됩니다.
 * foreach의 총 처리 시간은 가장 긴 큐의 처리 시간과 동일합니다. 즉, foreach 활동은 큐의 생성 방법에 따라 달라집니다.
 
**해결 방법**

 * 병렬로 실행되는 *For Each* 내에서는 *SetVariable* 활동을 사용하지 않아야 합니다.
 * 고객은 큐가 생성되는 방법을 고려해서 각 *foreach* 가 비슷한 처리 시간의 항목을 갖는 여러 *foreach* 를 설정하여 foreach 성능을 향상시킬 수 있습니다. 
 * 이렇게 하면 장기 실행이 순차 방식 대신 병렬 방식으로 처리됩니다.

 ### <a name="pipeline-status-is-queued-or-stuck-for-a-long-time"></a>파이프라인 상태가 오랫동안 큐에 있거나 움직이지 않음
 
 **원인**
 
 이 문제는 동시성 제한 도달, 서비스 중단, 네트워크 오류 등의 여러 가지 이유로 발생할 수 있습니다.
 
 **해결 방법**
 
* **동시성 제한:** 파이프라인에 동시성 정책이 있으면 진행 중인 오래된 파이프라인 실행이 없는지 확인합니다. 
* **모니터링 제한:** ADF 작성 캔버스로 이동하여 파이프라인을 선택하고 동시성 속성이 할당되어 있는지 확인합니다. 있으면 모니터링 보기로 이동하여 지난 45일 동안 진행 중인 실행이 없는지 확인합니다. 진행 중인 실행이 있으면 취소할 수 있으며 새 파이프라인 실행을 시작해야 합니다.

* **일시적인 문제:** 일시적인 네트워크 문제, 자격 증명 오류, 서비스 중단 등이 해당 실행에 영향을 주었을 수 있습니다. 이를 위해 Azure Data Factory에는 문제가 발생했을 때 모든 실행을 모니터링하고 시작하는 내부 복구 프로세스가 있습니다. [여기](https://docs.microsoft.com/azure/data-factory/monitor-visually#rerun-pipelines-and-activities)에 설명된 대로 파이프라인과 작업을 다시 실행할 수 있습니다. 작업을 취소했거나 실패한 경우 [작업 실패에서 재실행](https://docs.microsoft.com/azure/data-factory/monitor-visually#rerun-from-failed-activity)에 따라 작업을 다시 실행할 수 있습니다. 이 프로세스는 매시간 발생하므로 실행이 한 시간보다 오래 중단된 경우 지원 사례를 만듭니다.


 
### <a name="longer-start-up-times-for-activities-in-adf-copy-and-data-flow"></a>ADF 복사 및 Data Flow 활동의 시작 시간이 오래 걸림

**원인**

이 문제는 Data Flow 또는 최적화된 SHIR의 TTL(Time to live) 기능을 구현하지 않은 경우에 발생할 수 있습니다.

**해결 방법**

* 각 복사 작업을 시작하는 데 최대 2분이 걸리고 이 문제가 주로 VNet 조인(및 Azure IR)에서 발생하는 경우 이는 복사 성능 문제일 수 있습니다. 문제 해결 단계를 보려면 [복사 성능 향상](copy-activity-performance-troubleshooting.md)으로 이동합니다.
* TTL(Time to live) 기능을 사용하여 데이터 흐름 작업의 클러스터 시작 작동 시간을 줄일 수 있습니다. [Data Flow 통합 런타임](control-flow-execute-data-flow-activity.md#data-flow-integration-runtime)을 검토하세요.

 ### <a name="hitting-capacity-issues-in-shirself-hosted-integration-runtime"></a>SHIR(자체 호스팅 통합 런타임)의 용량 한도 문제
 
 **원인**
 
이 문제는 워크로드별로 SHIR을 확장하지 않은 경우에 발생할 수 있습니다.

**해결 방법**

* SHIR의 용량 문제가 발생하면 활동 균형을 조정하도록 VM을 업그레이드하여 노드를 늘립니다. 자체 호스팅 IR 일반 실패 또는 오류, 자체 호스팅 IR 업그레이드 또는 자체 호스팅 IR 연결 문제에 대한 오류 메시지(이로 인해 긴 큐가 생성될 수 있음)가 표시되는 경우 [ 자체 호스팅 통합 런타임 문제 해결](self-hosted-integration-runtime-troubleshoot-guide.md)로 이동합니다.

### <a name="error-messages-due-to-long-queues-for-adf-copy-and-data-flow"></a>ADF 복사 및 Data Flow의 긴 큐로 인한 오류 메시지

**원인**

긴 큐 관련 오류 메시지는 여러 가지 이유로 표시될 수 있습니다. 

**해결 방법**
* 커넥터를 통해 원본 또는 대상에서 오류 메시지(이로 인해 긴 큐가 생성될 수 있음)가 표시되는 경우 [커넥터 문제 해결 가이드](connector-troubleshoot-guide.md)로 이동하세요.
* 매핑 데이터 흐름에 대한 오류 메시지(이로 인해 긴 큐가 생성될 수 있음)가 표시되는 경우 [데이터 흐름 문제 해결 가이드](data-flow-troubleshoot-guide.md)로 이동하세요.
* Databricks, 사용자 지정 활동, HDI와 같은 다른 활동에 대한 오류 메시지(이로 인해 긴 큐가 생성될 수 있음)가 표시되는 경우 [활동 문제 해결 안내서](data-factory-troubleshoot-guide.md)로 이동하세요.
* SSIS 패키지 실행에 대한 오류 메시지(이로 인해 긴 큐가 생성될 수 있음)가 표시되는 경우 Azure-SSIS [패키지 실행 문제 해결 가이드](ssis-integration-runtime-ssis-activity-faq.yml) 및 [통합 런타임 관리 문제 해결 가이드](ssis-integration-runtime-management-troubleshoot.md)로 이동하세요.

### <a name="error-message---codebadrequest-messagenull"></a>오류 메시지 - "code":"BadRequest", "message":"null"

**원인**

management.azure.com에 도달하는 JSON 페이로드가 손상되었으므로 사용자 오류입니다. 사용자 호출이 ADF 서비스 계층에 도달하지 않았으므로 로그가 저장되지 않습니다.

**해결 방법**

Edge/Chrome 브라우저 **개발자 도구** 를 사용하여 ADF 포털에서 API 호출의 네트워크를 추적합니다. 특수 문자(예: $), 공백 및 기타 유형의 사용자 입력으로 인해 발생할 수 있는 잘못된 JSON 페이로드가 표시됩니다. 문자열 식을 수정한 후 브라우저에서 나머지 ADF 사용 호출을 진행합니다.

### <a name="foreach-activities-do-not-run-in-parallel-mode"></a>ForEach 작업은 병렬 모드에서 실행되지 않습니다.

**원인**

디버그 모드에서 ADF를 실행하고 있습니다.

**해결 방법**

트리거 모드에서 파이프라인을 실행합니다.

### <a name="cannot-publish-because-account-is-locked"></a>계정이 잠겨 있어 게시할 수 없습니다.

**원인**

스토리지 이벤트 트리거를 제거하기 위해 협업 분기를 변경했습니다. 게시하려고 하는데 `Trigger deactivation error` 메시지가 표시됩니다.

**해결 방법**

이벤트 트리거에 사용되는 스토리지 계정이 잠겨 있기 때문입니다. 계정을 잠금 해제합니다.

### <a name="expression-builder-fails-to-load"></a>식 작성기가 로드되지 않음

**원인**

웹 브라우저의 네트워크 또는 캐시 문제로 인해 식 작성기가 로드되지 않을 수 있습니다.  

**해결 방법**


웹 브라우저를 지원되는 브라우저의 최신 버전으로 업그레이드하고 사이트의 쿠키를 지우고 페이지를 새로 고칩니다.

### <a name="codebadrequestmessageerrorcodeflowrunsizelimitexceeded"></a>"Code":"BadRequest","message":"ErrorCode=FlowRunSizeLimitExceeded

**원인**

많은 작업을 연결했습니다.

**해결 방법**

파이프라인을 하위 파이프라인으로 분할하고 **ExecutePipelines** 작업으로 함께 연결할 수 있습니다. 

###  <a name="how-to-optimize-pipeline-with-mapping-data-flows-to-avoid-internal-server-errors-concurrency-errors-etc-during-execution"></a>실행 중 내부 서버 오류, 동시성 오류 등을 피하기 위해 매핑 데이터 흐름으로 파이프라인을 최적화하는 방법

**원인**

매핑 데이터 흐름을 최적화하지 않았습니다.

**해결 방법**

* 많은 양의 데이터 및 변환을 처리할 때 메모리 최적화 컴퓨팅을 사용합니다.
* 각 작업의 경우 배치 크기를 줄입니다.
* ADF의 성능에 맞게 데이터베이스와 웨어하우스를 스케일 업합니다. 
* 병렬로 실행되는 작업에는 별도의 IR(통합 런타임)을 사용합니다.
* 원본에서 파티션을 조정하고 이에 따라 싱크합니다. 
* [데이터 흐름 최적화](concepts-data-flow-performance.md)를 검토합니다.

## <a name="next-steps"></a>다음 단계

문제 해결을 위한 도움이 필요한 경우 다음 리소스를 참조하세요.

*  [Data Factory 블로그](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Data Factory 기능 요청](https://feedback.azure.com/forums/270578-data-factory)
*  [Azure 비디오](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Microsoft Q&A 질문 페이지](/answers/topics/azure-data-factory.html)
*  [Data Factory에 대한 Twitter 정보](https://twitter.com/hashtag/DataFactory)
