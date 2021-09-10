---
title: 단일 테넌트 Azure Logic Apps에서 런타임 및 환경 설정 편집
description: 단일 테넌트 Azure Logic Apps에서 논리 앱의 런타임 및 환경 설정을 변경합니다.
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, azla
ms.topic: how-to
ms.date: 08/18/2021
ms.openlocfilehash: 731fe8f29fc4e67e2d74d46702768e642928ae09
ms.sourcegitcommit: d43193fce3838215b19a54e06a4c0db3eda65d45
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2021
ms.locfileid: "122539565"
---
# <a name="edit-host-and-app-settings-for-logic-apps-in-single-tenant-azure-logic-apps"></a>단일 테넌트 Azure Logic Apps에서 논리 앱의 호스트 및 앱 설정 편집

*단일 테넌트* Azure Logic Apps에서 논리 앱의 *앱 설정* 에는 해당 논리 앱의 *모든 워크플로* 에 영향을 주는 전역 구성 옵션을 지정합니다. 단, 이 설정은 *로컬 개발 환경* 에서 이러한 워크플로가 실행될 때에 *만* 적용됩니다. 워크플로는 로컬에서 실행될 때 *로컬 환경 변수* 로 이러한 앱 설정에 액세스할 수 있습니다. 이러한 로컬 환경 변수의 값은 로컬 개발 도구에서 사용되며 환경마다 자주 바뀔 수 있습니다. 예를 들어 이러한 값에는 연결 문자열이 포함될 수 있습니다. Azure에 배포할 경우 앱 설정은 무시되며 배포에 포함되지 않습니다.

또한 논리 앱에는 해당 논리 앱의 *모든 워크플로* 에 적용되는 런타임 구성 설정 및 값을 지정하는 *호스트 설정* 도 있습니다(예: 처리량, 용량, 데이터 크기 등의 기본값, *로컬에서 또는 Azure에서 실행되는지 여부*).

<a name="app-settings-parameters-deployment"></a>

## <a name="app-settings-parameters-and-deployment"></a>앱 설정, 매개 변수 및 배포

*다중 테넌트* Azure Logic Apps에서 배포는 논리 앱과 인프라 둘 다에 대한 리소스 프로비전을 결합하고 처리하는 ARM 템플릿(Azure Resource Manager 템플릿)을 바탕으로 합니다. 하지만 이러한 설계는 다양한 개발, 테스트 및 프로덕션 환경에서 논리 앱의 환경 변수를 유지해야 하는 경우에 문제가 됩니다. 배포 시 ARM 템플릿의 모든 항목이 정의됩니다. 따라서 한 가지 변수만 변경해야 하더라도 모든 항목을 다시 배포해야 합니다.

*단일 테넌트* Azure Logic Apps에서는 앱과 인프라 간에 리소스 프로비전을 구분할 수 있으므로 배포가 더 쉬워집니다. *매개 변수* 를 사용하여 환경 간에 바뀔 수 있는 값을 추출할 수 있습니다. 워크플로에서 사용할 매개 변수를 정의하면 먼저 워크플로 설계에 집중하고 나중에 환경별 변수를 삽입할 수 있습니다. 앱 설정 및 매개 변수를 사용하면 런타임에서 환경 변수를 호출하고 참조할 수 있습니다. 이렇게 하면 자주 다시 배포할 필요가 없습니다.

앱 설정은 Azure Key Vault와 통합됩니다. 연결 문자열 및 키와 같은 [보안 문자열을 직접 참조](../app-service/app-service-key-vault-references.md)할 수 있습니다. 배포 시 환경 변수를 정의할 수 있는 ARM 템플릿(Azure Resource Manager 템플릿)과 마찬가지로 [논리 앱 워크플로 정의](/azure/templates/microsoft.logic/workflows) 내에서 앱 설정을 정의할 수 있습니다. 그런 다음, 연결 엔드포인트, 스토리지 문자열 등 동적으로 생성된 인프라 값을 캡처할 수 있습니다. 단, 앱 설정에는 크기 제한이 있으며 Azure Logic Apps의 특정 영역에서는 참조가 불가능합니다.

배포를 위한 논리 앱 설정에 대한 자세한 내용은 다음 설명서를 참조하세요.

- [환경마다 워크플로에서 바뀌는, 단일 테넌트 Azure Logic Apps 값에 대한 매개 변수 만들기](parameterize-workflow-app.md)
- [단일 테넌트 기반 논리 앱에 대한 DevOps 배포 개요](devops-deployment-single-tenant-azure-logic-apps.md)
- [단일 테넌트 기반 논리 앱에 대한 DevOps 배포 설정](set-up-devops-deployment-single-tenant-azure-logic-apps.md)

## <a name="visual-studio-code-project-structure"></a>Visual Studio Code 프로젝트 구조

[!INCLUDE [Visual Studio Code - logic app project structure](../../includes/logic-apps-single-tenant-project-structure-visual-studio-code.md)]

<a name="reference-local-settings-json"></a>

## <a name="reference-for-app-settings---localsettingsjson"></a>앱 설정 참조 - local.settings.json

Visual Studio Code의 논리 앱 프로젝트의 루트 수준에서 **local.settings.json** 파일에는 로컬 개발 환경에서 실행되는 동안 해당 논리 앱의 *모든 워크플로* 에 영향을 주는 전역 구성 옵션이 포함됩니다. 워크플로가 로컬에서 실행될 때 이러한 설정은 로컬 환경 변수로 액세스되며, 워크플로를 실행하는 다양한 환경 간에 해당 값이 바뀔 수 있습니다. 이러한 설정을 보고 관리하려면 [앱 설정 관리 - local.settings.json](#manage-app-settings)을 참조하세요.

Azure Logic Apps의 앱 설정은 Azure Functions 또는 Azure Web Apps의 앱 설정과 유사하게 작동합니다. 이러한 다른 서비스를 이전에 사용한 적이 있다면 앱 설정에 이미 익숙할 수 있습니다. 자세한 내용은 [Azure Functions의 앱 설정 참조](../azure-functions/functions-app-settings.md) 및 [Azure Functions Core Tools 작업 - 로컬 설정 파일](../azure-functions/functions-develop-local.md#local-settings-file)을 참조하세요.

| 설정 | 기본값 | 설명 |
|---------|---------------|-------------|
| `AzureWebJobsStorage` | 없음 | Azure Storage 계정에 대한 연결 문자열을 설정합니다. |
| `Workflows.<workflowName>.FlowState` | 없음 | <*workflowName*>의 상태를 설정합니다. |
| `Workflows.<workflowName>.RuntimeConfiguration.RetentionInDays` | 없음 | <*workflowName*>의 작업 옵션을 설정합니다. |
| `Workflows.Connection.AuthenticationAudience` | 없음 | Azure에서 호스트되는 연결을 인증할 대상 그룹을 설정합니다. |
| `Workflows.WebhookRedirectHostUri` | 없음 | 웹후크 콜백 URL에 사용할 호스트 이름을 설정합니다. |
| `WEBSITE_LOAD_ROOT_CERTIFICATES` | 없음 | 신뢰할 수 있는 루트 인증서의 지문을 설정합니다. |
||||

<a name="manage-app-settings"></a>

## <a name="manage-app-settings---localsettingsjson"></a>앱 설정 관리 - local.settings.json

앱 설정을 추가, 업데이트 또는 삭제하려면 Visual Studio Code, Azure Portal, Azure CLI 또는 ARM(Bicep) 템플릿에 대한 다음 섹션을 선택하고 검토합니다. 논리 앱별 앱 설정은 [사용할 수 있는 앱 설정에 대한 참조 가이드 - local.settings.json](#reference-local-settings-json)을 참조하세요.

### <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Visual Studio Code에서 논리 앱의 앱 설정을 검토하려면 다음 단계를 수행합니다.

1. 논리 앱 프로젝트의 루트 프로젝트 수준에서 **local.settings.json** 파일을 찾아 엽니다.

1. `Values` 개체에서 논리 앱의 앱 설정을 검토합니다.

   이러한 설정에 대한 자세한 내용은 [사용할 수 있는 앱 설정에 대한 참조 가이드 - local.settings.json](#reference-local-settings-json)을 참조하세요.

앱 설정을 추가하려면 다음 단계를 수행합니다.

1. **local.settings.json** 파일에서 `Values` 개체를 찾습니다.

1. `Values` 개체에서 Visual Studio Code에서 로컬로 실행할 때 적용할 앱 설정을 추가합니다. 몇 가지 설정을 사용하여 특정 워크플로에 대한 설정을 추가할 수 있습니다. 예를 들면 다음과 같습니다.

   ```json
   {
      "IsEncrypted": false,
      "Values": {
         "AzureWebJobsStorage": "UseDevelopmentStorage=true",
         "Workflows.WorkflowName1.FlowState" : "Disabled",
         <...>
     }
   }
   ```

### <a name="azure-portal"></a>[Azure Portal](#tab/azure-portal)

Azure Portal에서 단일 테넌트 기반 논리 앱의 앱 설정을 검토하려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com/) 검색 상자에서 논리 앱을 찾아 선택합니다.

1. 논리 앱 메뉴의 **설정** 에서 **구성** 을 선택합니다.

1. **구성** 페이지의 **애플리케이션 설정** 탭에서 논리 앱의 앱 설정을 검토합니다.

   이러한 설정에 대한 자세한 내용은 [사용할 수 있는 앱 설정에 대한 참조 가이드 - local.settings.json](#reference-local-settings-json)을 참조하세요.

1. 모든 값을 보려면 **값 표시** 를 선택합니다. 또는 단일 값을 보려면 해당 값을 선택합니다.

설정을 추가하려면 다음 단계를 수행합니다.

1. **애플리케이션 설정** 탭의 **애플리케이션 설정** 에서 **새 애플리케이션 설정** 을 선택합니다.

1. **이름** 에 새 설정의 *키* 또는 이름을 입력합니다.

1. **값** 에 새 설정의 값을 입력합니다.

1. 새 *키-값* 쌍을 만들 준비가 되면 **확인** 을 선택합니다.

:::image type="content" source="./media/edit-app-settings-host-settings/portal-app-settings-values.png" alt-text="단일 테넌트 기반 논리 앱의 앱 설정 및 값이 표시된 Azure Portal 구성 창을 보여주는 스크린샷" lightbox="./media/edit-app-settings-host-settings/portal-app-settings-values.png":::

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI를 사용하여 현재 앱 설정을 검토하려면 `az logicapp config appsettings list` 명령을 실행합니다. 명령에 `--name -n` 및 `--resource-group -g` 매개 변수가 포함되어 있는지 확인합니다. 예를 들면 다음과 같습니다.

```azurecli
az logicapp config appsettings list --name MyLogicApp --resource-group MyResourceGroup
```

이러한 설정에 대한 자세한 내용은 [사용할 수 있는 앱 설정에 대한 참조 가이드 - local.settings.json](#reference-local-settings-json)을 참조하세요.

Azure CLI를 사용하여 앱 설정을 추가하거나 업데이트하려면 `az logicapp config appsettings set` 명령을 실행합니다. 명령에 `--name n` 및 `--resource-group -g` 매개 변수가 포함되어 있는지 확인합니다. 예를 들어 다음 명령은 값이 `12345`인 `CUSTOM_LOGIC_APP_SETTING`이라는 키로 설정을 만듭니다.

```azurecli
az logicapp config appsettings set --name MyLogicApp --resource-group MyResourceGroup --settings CUSTOM_LOGIC_APP_SETTING=12345 
```

---

<a name="reference-host-json"></a>

## <a name="reference-for-host-settings---hostjson"></a>호스트 설정에 대한 참조 - host.json

Visual Studio Code에서는 논리 앱 프로젝트의 루트 수준에서 **host.json** 메타데이터 파일에 로컬에서 또는 Azure에서 실행하든 관계 없이 논리 앱 리소스의 *모든 워크플로* 에 적용되는 런타임 설정 및 기본값이 포함됩니다. 이러한 설정을 보고 관리하려면 [호스트 설정 관리 - host.json](#manage-host-settings)을 참조하세요. 또한 [Azure Logic Apps 설명서의 제한 및 구성](logic-apps-limits-and-config.md#definition-limits)에서 관련 제한 정보를 확인할 수 있습니다.

<a name="job-orchestration"></a>

### <a name="job-orchestration-throughput"></a>작업 오케스트레이션 처리량

다음 설정은 단일 테넌트 Azure Logic Apps에서 워크플로 작업을 실행할 때의 처리량 및 용량에 영향을 미칩니다.

| 설정 | 기본값 | 설명 |
|---------|---------------|-------------|
| `Jobs.BackgroundJobs.DispatchingWorkersPulseInterval` | `00:00:01` <br>(1초) | 이전 폴링에서 작업이 반환되지 않을 때 작업 디스패처가 작업 큐를 폴링하는 간격을 설정합니다. 이전 폴링에서 작업이 반환되면 작업 디스패처는 즉시 큐를 폴링합니다. |
| `Jobs.BackgroundJobs.NumWorkersPerProcessorCount` | `192`개 디스패처 작업자 인스턴스 | 프로세서 코어당 *디스패처 작업자 인스턴스* 의 수 또는 *작업 디스패처* 의 수를 설정합니다. 이 값은 코어당 워크플로 실행 수에 영향을 줍니다. |
| `Jobs.BackgroundJobs.NumPartitionsInJobTriggersQueue` | `1`개 작업 큐 | 처리할 작업에 대해 작업 디스패처가 모니터링하는 작업 큐의 수를 설정합니다. 이 값은 작업 큐가 존재하는 스토리지 파티션의 수에도 영향을 줍니다. |
| `Jobs.BackgroundJobs.NumPartitionsInJobDefinitionsTable` | `4`개 작업 파티션 | 작업 정의 테이블의 작업 파티션 수를 설정합니다. 이 값은 파티션 스토리지 제한의 영향을 받는 실행 처리량 크기를 제어합니다. |
||||

<a name="run-duration-history"></a>

### <a name="run-duration-and-history"></a>실행 지속 시간 및 기록

| 설정 | 기본값 | 설명 |
|---------|---------------|-------------|
| `Runtime.FlowRetentionThreshold` | `90.00:00:00` <br>(90일) | 실행이 시작된 후 워크플로 실행 기록을 유지하는 시간을 설정합니다. |
| `Runtime.Backend.FlowRunTimeout` | `90.00:00:00` <br>(90일) | 시간 제한을 적용하기 전에 워크플로를 계속 실행할 수 있는 시간을 설정합니다. <p><p>**중요**: 이 값이 `Runtime.FlowRetentionThreshold` 값보다 작거나 같은지 확인하세요. 그렇지 않으면 연관된 작업이 완료되기 전에 실행 기록이 삭제될 수 있습니다. |
||||

<a name="inputs-outputs"></a>

### <a name="inputs-and-outputs"></a>입력 및 출력

| 설정 | 기본값 | 설명 |
|---------|---------------|-------------|
| `Runtime.FlowRunActionJob.MaximumActionResultSize` | `209715200`바이트 | 한 작업에서 결합된 입력 및 출력에 허용되는 최대 크기(바이트)를 설정합니다. |
| `Runtime.ContentLink.MaximumContentSizeInBytes` | `104857600`자 | 한 트리거나 작업에서 입력 또는 출력에 허용되는 최대 크기(문자 수)를 설정합니다. |
||||

<a name="pagination"></a>

### <a name="pagination"></a>페이지 매김

| 설정 | 기본값 | 설명 |
|---------|---------------|-------------|
| `Runtime.FlowRunRetryableActionJobCallback.MaximumPageCount` | `1000`페이지 | 작업에서 페이지 매김을 지원하고 사용하도록 설정된 경우에 런타임에서 반환하거나 처리할 최대 페이지 수를 설정합니다. |
||||

<a name="chunking"></a>

### <a name="chunking"></a>청크

| 설정 | 기본값 | 설명 |
|---------|---------------|-------------|
| `Runtime.FlowRunRetryableActionJobCallback.MaximumContentLengthInBytesForPartialContent` | `1073741824`바이트 | 작업에서 청크를 지원하고 사용할 수 있도록 설정된 경우에 다운로드하거나 업로드하는 콘텐츠의 최대 크기(바이트)를 설정합니다. |
| `Runtime.FlowRunRetryableActionJobCallback.MaxChunkSizeInBytes` | `52428800`바이트 | 작업에서 청크를 지원하고 사용할 수 있도록 설정된 경우에 각 콘텐츠 청크의 최대 크기(바이트)를 설정합니다. |
| `Runtime.FlowRunRetryableActionJobCallback.MaximumRequestCountForPartialContent` | `1000`개 요청 | 작업에서 청크를 지원하고 사용하도록 설정된 경우에 작업 실행 시 콘텐츠 다운로드를 위해 수행할 수 있는 최대 요청 수를 설정합니다. |
||||

<a name="trigger-concurrency"></a>

### <a name="trigger-concurrency"></a>트리거 동시성

| 설정 | 기본값 | 설명 |
|---------|---------------|-------------|
| `Runtime.Trigger.MaximumRunConcurrency` | `100`개 실행 | 트리거가 시작할 수 있는 최대 동시 실행 수를 설정합니다. 이 값은 트리거의 동시성 정의에 표시됩니다. |
| `Runtime.Trigger.MaximumWaitingRuns` | `200`개 실행 | 동시 실행이 최댓값을 충족한 후 대기할 수 있는 최대 실행 수를 설정합니다. 이 값은 트리거의 동시성 정의에 표시됩니다. |
||||

<a name="for-each-loop"></a>

### <a name="for-each-loops"></a>for each 루프

| 설정 | 기본값 | 설명 |
|---------|---------------|-------------|
| `Runtime.Backend.FlowDefaultForeachItemsLimit` | `100000` <br>(100,000개 배열 항목) | *상태 저장 워크플로* 의 `For each` 루프에서 처리할 최대 배열 항목 수를 설정합니다. |
| `Runtime.Backend.Stateless.FlowDefaultForeachItemsLimit` | `100`개 항목 | *상태 비저장 워크플로* 의 `For each` 루프에서 처리할 최대 배열 항목 수를 설정합니다. |
| `Runtime.Backend.ForeachDefaultDegreeOfParallelism` | `20`회 반복 | `For each` 루프에서 기본 동시 반복 횟수(병렬화 수준)를 설정합니다. 순차적으로 실행하려면 값을 `1`로 설정합니다. |
| `Runtime.Backend.FlowDefaultSplitOnItemsLimit` | `100000` <br>(100,000개 배열 항목) | `SplitOn` 설정에 따라 여러 워크플로 인스턴스로 분리하거나 분할할 최대 배열 항목 수를 설정합니다. |
||||

<a name="until-loop"></a>

### <a name="until-loops"></a>until 루프

| 설정 | 기본값 | 설명 |
|---------|---------------|-------------|
| `Runtime.Backend.MaximumUntilLimitCount` | `5000`회 반복 | *상태 비저장 워크플로* 의 `Until` 작업에서 `Count` 속성에 허용되는 최대 수를 설정합니다. |
| `Runtime.Backend.Stateless.MaximumUntilLimitCount` | `100`회 반복 | *상태 비저장 워크플로* 의 `Until` 작업에서 `Count` 속성에 허용되는 최대 수를 설정합니다. |
| `Runtime.Backend.Stateless.FlowRunTimeout` | `00:05:00` <br>(5분) | 상태 비저장 워크플로에서 `Until` 루프의 최대 대기 시간을 설정합니다. |
||||

<a name="variables"></a>

### <a name="variables"></a>변수

| 설정 | 기본값 | 설명 |
|---------|---------------|-------------|
| `Runtime.Backend.DefaultAppendArrayItemsLimit` | `100000` <br>(100,000개 배열 항목) | 배열 형식 변수의 최대 항목 수를 설정합니다. |
| `Runtime.Backend.VariableOperation.MaximumVariableSize` | 상태 저장 워크플로: `104857600`자 <p><p>상태 비저장 워크플로: `1024`자 | 변수에 저장할 수 있는 콘텐츠의 최대 크기(문자 수)를 설정합니다. |
||||

<a name="http-webhook"></a>

### <a name="http-operations"></a>HTTP 작업

| 설정 | 기본값 | 설명 |
|---------|---------------|-------------|
| `Runtime.Backend.HttpOperation.RequestTimeout` | `00:03:45` <br>(3분 45초) | HTTP 트리거 및 작업에 대한 요청 제한 시간 값을 설정합니다. |
| `Runtime.Backend.HttpOperation.MaxContentSize` | `104857600`바이트 | HTTP 트리거 및 작업에 대한 최대 요청 크기(바이트)를 설정합니다. |
| `Runtime.Backend.HttpOperation.DefaultRetryCount` | `4`회 다시 시도 | HTTP 트리거 및 작업에 대한 기본 다시 시도 횟수를 설정합니다. |
| `Runtime.Backend.HttpOperation.DefaultRetryInterval` | `00:00:07` <br>(7초) | HTTP 트리거 및 작업에 대한 기본 다시 시도 간격을 설정합니다. |
| `Runtime.Backend.HttpOperation.DefaultRetryMaximumInterval` | `01:00:00` <br>(1시간) | HTTP 트리거 및 작업에 대한 최대 다시 시도 간격을 설정합니다. |
| `Runtime.Backend.HttpOperation.DefaultRetryMinimumInterval` | `00:00:05` <br>(5초) | HTTP 트리거 및 작업에 대한 최소 다시 시도 간격을 설정합니다. |
||||

<a name="http-webhook"></a>

### <a name="http-webhook-operations"></a>HTTP webhook 작업

| 설정 | 기본값 | 설명 |
|---------|---------------|-------------|
| `Runtime.Backend.HttpWebhookOperation.RequestTimeout` | `00:02:00` <br>(2분) | HTTP webhook 트리거 및 작업에 대한 요청 시간 제한 값을 설정합니다. |
| `Runtime.Backend.HttpWebhookOperation.MaxContentSize` | `104857600`바이트 | HTTP webhook 트리거 및 작업에 대한 최대 요청 크기(바이트)를 설정합니다. |
| `Runtime.Backend.HttpWebhookOperation.DefaultRetryCount` | `4`회 다시 시도 | HTTP webhook 트리거 및 작업에 대한 기본 다시 시도 횟수를 설정합니다. |
| `Runtime.Backend.HttpWebhookOperation.DefaultRetryInterval` | `00:00:07` <br>(7초) | HTTP webhook 트리거 및 작업에 대한 기본 다시 시도 간격을 설정합니다. |
| `Runtime.Backend.HttpWebhookOperation.DefaultRetryMaximumInterval` | `01:00:00` <br>(1시간) | HTTP webhook 트리거 및 작업에 대한 최대 다시 시도 간격을 설정합니다. |
| `Runtime.Backend.HttpWebhookOperation.DefaultRetryMinimumInterval` | `00:00:05` <br>(5초) | HTTP webhook 트리거 및 작업에 대한 최소 다시 시도 간격을 설정합니다. |
| `Runtime.Backend.HttpWebhookOperation.DefaultWakeUpInterval` | `01:00:00` <br>(1시간) | HTTP webhook 트리거와 동작 작업에 대한 기본 절전 모드 해제 간격을 설정합니다. |
||||

<a name="built-in-azure-functions"></a>

### <a name="built-in-azure-functions-operations"></a>기본 제공 Azure Functions 작업

| 설정 | 기본값 | 설명 |
|---------|---------------|-------------|
| `Runtime.Backend.FunctionOperation.RequestTimeout` | `00:03:45` <br>(3분 45초) | Azure Functions 작업에 대한 요청 시간 제한 값을 설정합니다. |
| `Runtime.Backend.FunctionOperation.MaxContentSize` | `104857600`바이트 | Azure Functions 작업에 대한 최대 요청 크기(바이트)를 설정합니다. |
| `Runtime.Backend.FunctionOperation.DefaultRetryCount` | `4`회 다시 시도 | Azure Functions 작업에 대한 기본 다시 시도 횟수를 설정합니다. |
| `Runtime.Backend.FunctionOperation.DefaultRetryInterval` | `00:00:07` <br>(7초) | Azure Functions 작업에 대한 기본 다시 시도 간격을 설정합니다. |
| `Runtime.Backend.FunctionOperation.DefaultRetryMaximumInterval` | `01:00:00` <br>(1시간) | Azure Functions 작업에 대한 최대 다시 시도 간격을 설정합니다. |
| `Runtime.Backend.FunctionOperation.DefaultRetryMinimumInterval` | `00:00:05` <br>(5초) | Azure Functions 작업에 대한 최소 다시 시도 간격을 설정합니다. |
||||

<a name="built-in-sql"></a>

### <a name="built-in-sql-operations"></a>기본 제공 SQL 작업

| 설정 | 기본값 | 설명 |
|---------|---------------|-------------|
| `Runtime.ServiceProviders.Sql.QueryExecutionTimeout` | `00:00:30` <br>(30초) | SQL 서비스 공급자 작업에 대한 요청 제한 시간 값을 설정합니다. |
||||

<a name="built-in-service-bus"></a>

### <a name="built-in-azure-service-bus-operations"></a>기본 제공 Azure Service Bus 작업

| 설정 | 기본값 | 설명 |
|---------|---------------|-------------|
| `ServiceProviders.ServiceBus.MessageSenderOperationTimeout` | `00:01:00` <br>(1분) | 기본 제공 Service Bus 작업을 통해 메시지를 보내기 위한 시간 제한을 설정합니다. |
| `Runtime.ServiceProviders.ServiceBus.MessageSenderPoolSizePerProcessorCount` | 메시지 보낸 사람 `64`명 | 메시지 보낸 사람 풀에서 사용할 프로세서 코어당 Azure Service Bus 메시지 보낸 사람의 수를 설정합니다. |
||||

<a name="managed-api-connector"></a>

### <a name="managed-api-connector-operations"></a>관리형 API 커넥터 작업

| 설정 | 기본값 | 설명 |
|---------|---------------|-------------|
| `Runtime.Backend.ApiConnectionOperation.RequestTimeout` | `00:02:00` <br>(2분) | 관리형 API 커넥터 트리거 및 작업에 대한 요청 제한 시간 값을 설정합니다. |
| `Runtime.Backend.ApiConnectionOperation.MaxContentSize` | `104857600`바이트 | 관리형 API 커넥터 트리거 및 작업에 대한 최대 요청 크기(바이트)를 설정합니다. |
| `Runtime.Backend.ApiConnectionOperation.DefaultRetryCount` | `4`회 다시 시도 | 관리형 API 커넥터 트리거 및 작업에 대한 기본 다시 시도 횟수를 설정합니다. |
| `Runtime.Backend.ApiConnectionOperation.DefaultRetryInterval` | `00:00:07` <br>(7초) | 관리형 API 커넥터 트리거 및 작업에 대한 기본 다시 시도 간격을 설정합니다. |
| `Runtime.Backend.ApiWebhookOperation.DefaultRetryMaximumInterval` | `01:00:00` <br>(1일) | 관리형 API 커넥터 webhook 트리거 및 작업에 대한 최대 다시 시도 간격을 설정합니다. |
| `Runtime.Backend.ApiConnectionOperation.DefaultRetryMinimumInterval` | `00:00:05` <br>(5초) | 관리형 API 커넥터 트리거 및 작업에 대한 최소 다시 시도 간격을 설정합니다. |
| `Runtime.Backend.ApiWebhookOperation.DefaultWakeUpInterval` | `01:00:00` <br>(1일) | 관리형 API 커넥터 webhook 트리거 및 동작 작업에 대한 기본 절전 모드 해제 간격을 설정합니다. |
||||

<a name="blob-storage"></a>

### <a name="blob-storage"></a>Blob 스토리지

| 설정 | 기본값 | 설명 |
|---------|---------------|-------------|
| `Runtime.ContentStorage.RequestOptionsServerTimeout` | `00:00:30` <br>(30초) | Azure Logic Apps 런타임의 Blob 요청에 대한 시간 제한 값을 설정합니다. |
| `Runtime.DataStorage.RequestOptionsMaximumExecutionTime` | `00:02:00` <br>(2분) | Azure Logic Apps 런타임의 테이블 및 큐 스토리지 요청에 대해 다시 시도를 포함한 작업 제한 시간 값을 설정합니다. |
| `Runtime.ContentStorage.RequestOptionsDeltaBackoff` | `00:00:02` <br>(2초) | Blob 스토리지로 보내는 각 다시 시도 사이의 백오프 간격을 설정합니다. |
| `Runtime.ContentStorage.RequestOptionsMaximumAttempts` | `4`회 다시 시도 | 테이블 및 큐 스토리지로 보내는 최대 다시 시도 횟수를 설정합니다. |
||||

<a name="store-inline-or-blob"></a>

### <a name="store-content-inline-or-use-blobs"></a>콘텐츠를 인라인으로 저장하거나 Blob 사용

| 설정 | 기본값 | 설명 |
|---------|---------------|-------------|
| `Runtime.FlowRunEngine.ForeachMaximumItemsForContentInlining` | `20`개 항목 | `For each` 루프를 실행할 때 각 항목의 값은 테이블 스토리지에 다른 메타데이터와 함께 인라인으로 또는 Blob 스토리지에 별도로 저장됩니다. 다른 메타데이터와 인라인으로 저장할 항목의 수를 설정합니다. |
| `Runtime.FlowRunRetryableActionJobCallback.MaximumPagesForContentInlining` | `20`페이지 | Blob 스토리지에 저장하기 전에 테이블 스토리지에 인라인 콘텐츠로 저장할 최대 페이지 수를 설정합니다. |
| `Runtime.FlowTriggerSplitOnJob.MaximumItemsForContentInlining` | `40`개 항목 | `SplitOn` 설정에서 배열 항목을 여러 워크플로 인스턴스로 분리하면 각 항목의 값은 테이블 스토리지에 다른 메타데이터와 함께 인라인으로 또는 Blob 스토리지에 별도로 저장됩니다. 인라인으로 저장할 항목 수를 설정합니다. |
| `Runtime.ScaleUnit.MaximumCharactersForContentInlining` | `8192`자 | Blob 스토리지에 저장하기 전에 테이블 스토리지에 인라인으로 저장할 작업 입력 및 출력 문자의 최대 수를 설정합니다. |
||||

<a name="table-queue-storage"></a>

### <a name="table-and-queue-storage"></a>테이블 및 큐 스토리지

| 설정 | 기본값 | 설명 |
|---------|---------------|-------------|
| `Runtime.DataStorage.RequestOptionsServerTimeout` | `00:00:16` <br>(16초) | Azure Logic Apps 런타임의 테이블 및 큐 스토리지 요청에 대한 제한 시간 값을 설정합니다. |
| `Runtime.DataStorage.RequestOptionsMaximumExecutionTime` | `00:00:45` <br>(45초) | Azure Logic Apps 런타임의 테이블 및 큐 스토리지 요청에 대해 다시 시도를 포함한 작업 제한 시간 값을 설정합니다. |
| `Runtime.DataStorage.RequestOptionsDeltaBackoff` | `00:00:02` <br>(2초) | 테이블 및 큐 스토리지로 보내는 각 다시 시도 사이의 백오프 간격을 설정합니다. |
| `Runtime.DataStorage.RequestOptionsMaximumAttempts` | `4`회 다시 시도 | 테이블 및 큐 스토리지로 보내는 최대 다시 시도 횟수를 설정합니다. |
||||

<a name="retry-policy"></a>

### <a name="retry-policy-for-all-other-operations"></a>다른 모든 작업에 대한 다시 시도 정책

| 설정 | 기본값 | 설명 |
|---------|---------------|-------------|
| `Runtime.ScaleMonitor.MaxPollingLatency` | `00:00:30` <br>(30초) | 런타임 크기 조정에 대한 최대 폴링 대기 시간을 설정합니다. |
| `Runtime.Backend.Operation.MaximumRetryCount` | `90`회 다시 시도 | 워크플로 작업에 대한 다시 시도 정책 정의의 최대 다시 시도 횟수를 설정합니다. |
| `Runtime.Backend.Operation.MaximumRetryInterval` | `01:00:00:01` <br>(1일 및 1초) | 워크플로 작업에 대한 다시 시도 정책 정의의 최대 간격을 설정합니다. |
| `Runtime.Backend.Operation.MinimumRetryInterval` | `00:00:05` <br>(5초) | 워크플로 작업에 대한 다시 시도 정책 정의의 최소 간격을 설정합니다. |
||||

<a name="manage-host-settings"></a>

## <a name="manage-host-settings---hostjson"></a>호스트 설정 관리 - host.json

또한 해당 논리 앱의 *모든 워크플로* 에 적용되는 런타임 구성 설정 및 값을 지정하는 호스트 설정을 추가, 업데이트 또는 삭제할 수 있습니다(예: 처리량, 용량, 데이터 크기 등의 기본값, *로컬에서 또는 Azure에서 실행되는지 여부*). 논리 앱별 호스트 설정은 [사용할 수 있는 런타임 및 배포 설정에 대한 참조 가이드 - host.json](#reference-host-json)을 참조하세요.

### <a name="visual-studio-code---hostjson"></a>Visual Studio Code - host.json

Visual Studio Code에서 논리 앱의 호스트 설정을 검토하려면 다음 단계를 수행합니다.

1. 논리 앱 프로젝트의 루트 프로젝트 수준에서 **host.json** 파일을 찾아 엽니다.

1. `extensions` 개체의 `workflows` 및 `settings` 아래에서 논리 앱에 대해 이전에 추가된 호스트 설정을 검토합니다. 그렇지 않으면 `extensions` 개체가 파일에 표시되지 않습니다.

   호스트 설정에 대한 자세한 내용은 [사용할 수 있는 호스트 설정에 대한 참조 가이드 - host.json](#reference-host-json)을 참조하세요.

호스트 설정을 추가하려면 다음 단계를 수행합니다.

1. **host.json** 파일의 `extensionBundle` 개체 아래에 `workflow` 및 `settings` 개체를 포함하는 `extensions` 개체를 추가합니다. 예를 들면 다음과 같습니다.

   ```json
   {
      "version": "2.0",
      "extensionBundle": {
         "id": "Microsoft.Azure.Functions.ExtensionBundle",
         "version": "[1.*, 2.0.0)"
      },
      "extensions": {
         "workflow": {
            "settings": {
            }
         }
      }
   }
   ```

1. `settings` 개체에서 논리 앱의 모든 워크플로에 사용하려는 호스트 설정이 포함된 플랫 목록을 추가합니다. 이러한 워크플로는 로컬에서 또는 Azure에서 실행됩니다. 예를 들면 다음과 같습니다.

   ```json
   {
      "version": "2.0",
      "extensionBundle": {
         "id": "Microsoft.Azure.Functions.ExtensionBundle",
         "version": "[1.*, 2.0.0)"
      },
      "extensions": {
         "workflow": {
            "settings": {
               "Runtime.Trigger.MaximumWaitingRuns": "100"
            }
         }
      }
   }
   ```

### <a name="azure-portal---hostjson"></a>Azure Portal - host.json

Azure Portal에서 단일 테넌트 기반 논리 앱의 호스트 설정을 검토하려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com/) 검색 상자에서 논리 앱을 찾아 선택합니다.

1. 논리 앱 메뉴의 **개발 도구** 에서 **고급 도구** 를 선택합니다.

1. **고급 도구** 페이지에서 **Go** 를 선택합니다. 그러면 논리 앱에 대한 **Kudu** 환경이 열립니다.

1. Kudu 도구 모음의 **디버그 콘솔** 메뉴에서 **CMD** 를 선택합니다.

1. Azure Portal에서 논리 앱을 중지합니다.

   1. 논리 앱 메뉴에서 **개요** 를 선택합니다.

   1. **개요** 창의 도구 모음에서 **중지** 를 선택합니다.

1. 논리 앱 메뉴의 **개발 도구** 에서 **고급 도구** 를 선택합니다.

1. **고급 도구** 창에서 **Go** 를 선택합니다. 그러면 논리 앱에 대한 Kudu 환경이 열립니다.

1. Kudu 도구 모음에서 **디버그 콘솔** 메뉴를 열고 **CMD** 를 선택합니다.

   명령 프롬프트를 사용하여 **wwwroot** 폴더로 이동할 수 있도록 콘솔 창이 열립니다. 또는 콘솔 창 위에 표시된 디렉터리 구조를 찾아볼 수도 있습니다.

1. **wwwroot** 폴더의 경로 `...\home\site\wwwroot`에서 찾아봅니다.

1. 콘솔 창 위의 디렉터리 테이블에서 **host.json** 파일 옆에 있는 **편집** 을 선택합니다.

1. **host.json** 파일이 열리면 이전에 논리 앱에 추가된 모든 호스트 설정을 검토합니다.

   호스트 설정에 대한 자세한 내용은 [사용할 수 있는 호스트 설정에 대한 참조 가이드 - host.json](#reference-host-json)을 참조하세요.

설정을 추가하려면 다음 단계를 수행합니다.

1. 설정을 추가하거나 편집하기 전에 Azure Portal에서 논리 앱을 중지합니다.

   1. 논리 앱 메뉴에서 **개요** 를 선택합니다.
   1. **개요** 창의 도구 모음에서 **중지** 를 선택합니다.

1. **host.json** 파일로 돌아갑니다. `extensionBundle` 개체 아래에 `workflow` 및 `settings` 개체를 포함하는 `extensions` 개체를 추가합니다. 예를 들면 다음과 같습니다.

   ```json
   {
      "version": "2.0",
      "extensionBundle": {
         "id": "Microsoft.Azure.Functions.ExtensionBundle",
         "version": "[1.*, 2.0.0)"
      },
      "extensions": {
         "workflow": {
            "settings": {
            }
         }
      }
   }
   ```

1. `settings` 개체에서 논리 앱의 모든 워크플로에 사용하려는 호스트 설정이 포함된 플랫 목록을 추가합니다. 이러한 워크플로는 로컬에서 또는 Azure에서 실행됩니다. 예를 들면 다음과 같습니다.

   ```json
   {
      "version": "2.0",
      "extensionBundle": {
         "id": "Microsoft.Azure.Functions.ExtensionBundle",
         "version": "[1.*, 2.0.0)"
      },
      "extensions": {
         "workflow": {
            "settings": {
               "Runtime.Trigger.MaximumWaitingRuns": "100"
            }
         }
      }
   }
   ```

1. 완료되었으면 논리 앱을 **저장** 해야 합니다.

1. 이제 논리 앱을 다시 시작합니다. 논리 앱의 **개요** 페이지로 돌아가서 **다시 시작** 을 선택합니다.

---

## <a name="next-steps"></a>다음 단계

- [환경마다 워크플로에서 바뀌는, 단일 테넌트 Azure Logic Apps 값에 대한 매개 변수 만들기](parameterize-workflow-app.md)
- [단일 테넌트 Azure Logic Apps의 DevOps 배포 설정](set-up-devops-deployment-single-tenant-azure-logic-apps.md)
