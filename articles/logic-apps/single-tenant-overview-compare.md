---
title: 단일 테넌트 대 다중 테넌트 Azure Logic Apps
description: Azure Logic Apps에서 단일 테넌트, 다중 테넌트 및 ISE(통합 서비스 환경) 사이의 차이점을 알아봅니다.
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, azla
ms.topic: conceptual
ms.date: 08/18/2021
ms.openlocfilehash: 72e16387901ee11b902c5b0bec34e1bd058ffc0c
ms.sourcegitcommit: d43193fce3838215b19a54e06a4c0db3eda65d45
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2021
ms.locfileid: "122539587"
---
# <a name="single-tenant-versus-multi-tenant-and-integration-service-environment-for-azure-logic-apps"></a>Azure Logic Apps의 단일 테넌트 및 다중 테넌트와 통합 서비스 환경

Azure Logic Apps는 앱, 데이터, 서비스 및 시스템이 통합되어 있는 자동화된 *논리 앱 워크플로* 를 만들고 실행할 수 있는 클라우드 기반 플랫폼입니다. 이 플랫폼을 사용하면 엔터프라이즈 및 B2B(Business-to-Business) 시나리오를 위한 확장성이 뛰어난 통합 솔루션을 신속하게 개발할 수 있습니다. 논리 앱을 만들려면 **논리 앱(사용량)** 리소스 종류 또는 **논리 앱(표준)** 리소스 종류를 사용합니다. 사용량 리소스 종류는 *다중 테넌트* Azure Logic Apps 또는 *통합 서비스 환경* 에서 실행되는 반면, 표준 리소스 종류는 *단일 테넌트* Azure Logic Apps 환경에서 실행됩니다.

사용할 리소스 종류를 선택하기 전에 이 문서를 살펴보고 리소스 종류와 서비스 환경이 서로 어떻게 다른지 알아보세요. 그런 다음, 시나리오의 요구 사항, 솔루션 요구 사항 및 워크플로를 배포, 호스트 및 실행하려는 환경에 따라 사용하기에 가장 적합한 종류를 판단할 수 있습니다.

Azure Logic Apps를 처음 사용하는 경우 다음 문서를 참조하세요.

* [Azure Logic Apps란?](logic-apps-overview.md)
* [*논리 앱 워크플로* 란?](logic-apps-overview.md#logic-app-concepts)

<a name="resource-environment-differences"></a>

## <a name="resource-types-and-environments"></a>리소스 종류 및 환경

논리 앱 워크플로를 만들려면 시나리오, 솔루션 요구 사항, 원하는 기능 및 워크플로를 실행하려는 환경에 따라 **논리 앱** 리소스 종류를 선택합니다.

다음 표에는 원래 **논리 앱(표준)** 리소스 종류와 **Logic App(소비)** 리소스 종류의 차이점이 간략히 요약되어 있습니다. 또한 *단일 테넌트* 환경과 *다중 테넌트* 환경 및 *ISE(통합 서비스 환경)* 의 논리 앱 워크플로 배포, 호스트 및 실행 방법도 비교합니다.

[!INCLUDE [Logic app resource type and environment differences](../../includes/logic-apps-resource-environment-differences-table.md)]

<a name="resource-type-introduction"></a>

## <a name="logic-app-standard-resource"></a>논리 앱(표준) 리소스

**논리 앱(표준)** 리소스 종류는 새롭게 설계된 단일 테넌트 Azure Logic Apps 런타임으로 구동됩니다. 이 런타임은 [Azure Functions 확장성 모델](../azure-functions/functions-bindings-register.md)을 사용하며 Azure Functions 런타임에 확장으로 호스트됩니다. 이러한 설계는 논리 앱을 위한 이식성, 유연성 및 더 우수한 성능과 Azure Functions 플랫폼 및 Azure App Service 에코시스템에서 상속된 기타 기능 및 이점을 제공합니다.

예를 들어 Azure 함수 앱 및 해당 함수를 실행할 수 있는 곳이라면 어디든지 단일 테넌트 기반 논리 앱 및 해당 워크플로를 실행할 수 있습니다. 표준 리소스 종류는 Azure 함수 앱이 여러 함수를 호스트하는 방법과 비슷하게 여러 워크플로를 호스트할 수 있는 리소스 구조를 도입합니다. 일대다 매핑을 통해 동일한 논리 앱 및 테넌트에서 워크플로가 컴퓨팅 및 처리 리소스를 공유하므로 근접성으로 인해 더 우수한 성능을 제공합니다. 이 구조는 논리 앱 리소스와 워크플로 간에 일대일 매핑되는 **Logic App(소비)** 리소스와 다릅니다.

이식성, 유연성 및 성능 향상에 대해 자세히 알아보려면 다음 섹션을 계속 진행하세요. 또는 단일 테넌트 Azure Logic Apps 런타임 및 Azure Functions 확장성에 대한 자세한 내용은 다음 설명서를 참조하세요.

* [어디서나 실행되는 Azure Logic Apps - 런타임 심층 살펴보기](https://techcommunity.microsoft.com/t5/integrations-on-azure/azure-logic-apps-running-anywhere-runtime-deep-dive/ba-p/1835564)
* [Azure Functions 소개](../azure-functions/functions-overview.md)
* [Azure Functions 트리거 및 바인딩](../azure-functions/functions-triggers-bindings.md)

<a name="portability"></a>
<a name="flexibility"></a>

### <a name="portability-and-flexibility"></a>이식성 및 유연성

**논리 앱(표준)** 리소스 종류를 사용하여 논리 앱을 만들 때 단일 테넌트 서비스 환경뿐만 아니라 Azure 함수 앱 및 해당 함수를 실행할 수 있는 곳이라면 어디든지 워크플로를 실행할 수 있습니다.

**Azure Logic Apps(표준)** 확장과 함께 Visual Studio Code를 사용하는 경우 Azure에 배포하지 않고도 개발 환경에서 워크플로를 *로컬로* 개발, 빌드 및 실행할 수 있습니다. 시나리오에 컨테이너가 필요한 경우 [Azure Arc 지원 Logic Apps를 사용하여 단일 테넌트 기반 논리 앱을 만듭니다](azure-arc-enabled-logic-apps-create-deploy-workflows.md). 자세한 내용은 [Azure Arc 지원 Logic Apps란?](azure-arc-enabled-logic-apps-overview.md)을 검토하세요.

이러한 기능은 Azure에서 기존의 실행 중인 리소스를 토대로 개발을 진행해야 하는 다중 테넌트 모델에 비해 크게 개선된 것으로 상당한 이점을 제공합니다. 또한 **Logic App(소비)** 리소스 배포를 자동화하기 위한 다중 테넌트 모델은 앱과 인프라 모두에 대한 리소스 프로비전을 결합하고 처리하는 ARM 템플릿(Azure Resource Manager 템플릿)을 완전한 기반으로 합니다.

**논리 앱(표준)** 리소스 종류를 사용하면 인프라 배포와 앱 배포를 분리할 수 있으므로 배포가 더 쉬워집니다. 단일 테넌트 Azure Logic Apps 런타임 및 워크플로를 논리 앱의 일부로 함께 패키지화할 수 있습니다. 논리 앱 리소스를 즉시 배포할 수 있는 아티팩트로 빌드, 어셈블 및 압축하는 일반 단계 또는 작업을 사용할 수 있습니다. 인프라를 배포할 때 여전히 ARM 템플릿을 사용하여 해당 용도로 사용하는 다른 프로세스 및 파이프라인과 함께 해당 리소스를 별도로 프로비전할 수 있습니다.

앱을 배포하려면 아티팩트를 호스트 환경에 복사한 다음, 앱을 시작하여 워크플로를 실행합니다. 또는 이미 잘 알고 사용하고 있는 도구와 프로세스를 사용하여 아티팩트를 배포 파이프라인에 통합합니다. 이렇게 하면 개발에 사용하는 기술 스택에 관계 없이 선택한 자체 도구를 사용하여 배포할 수 있습니다.

표준 빌드 및 배포 옵션을 사용하면 인프라 배포와는 별도로 앱 개발에 집중할 수 있습니다. 따라서 일반 앱에 사용하는 여러 유사하거나 동일한 배포 옵션을 적용할 수 있는 보다 일반적인 프로젝트 모델을 얻게 됩니다. 또한 앱 프로젝트를 중심으로 배포 파이프라인을 빌드하고 프로덕션에 게시하기 전에 필요한 테스트 및 유효성 검사를 실행하기 위한 보다 일관된 환경을 활용할 수 있습니다.

<a name="performance"></a>

### <a name="performance"></a>성능

**논리 앱(표준)** 리소스 종류를 사용하면 동일한 단일 논리 앱 및 테넌트에서 여러 워크플로를 만들고 실행할 수 있습니다. 이 일대다 매핑을 사용하면 이러한 워크플로에서 컴퓨팅, 처리, 스토리지 및 네트워크와 같은 리소스를 공유하며 이들 리소스가 근접해 있어 더 우수한 성능을 제공합니다.

**논리 앱(표준)** 리소스 종류 및 단일 테넌트 Azure Logic Apps 런타임은 더 널리 사용되는 관리형 커넥터를 기본 제공 작업으로 사용할 수 있도록 하여 또 다른 중요한 향상을 제공합니다. 예를 들어 Azure Service Bus, Azure Event Hubs, SQL 등에 대해 기본 제공 작업을 사용할 수 있습니다. 한편, 관리형 커넥터 버전도 여전히 사용할 수 있으며 계속 작동합니다.

새로운 기본 제공 작업을 사용하여 만든 연결은 *기본 제공 연결* 또는 *서비스 공급자 연결이라고 합니다*. 관리형 연결에서는 이를 *API 연결* 이라고 하며, ARM 템플릿을 사용하여 배포해야 하는 Azure 리소스로 별도로 생성되고 실행됩니다. 기본 제공 작업 및 연결은 워크플로를 실행하는 것과 동일한 프로세스에서 로컬로 실행됩니다. 둘 다 단일 테넌트 Azure Logic Apps 런타임에서 호스트됩니다. 따라서 워크플로에 근접해 있으므로 기본 제공 작업 및 연결을 통해 성능을 향상시킬 수 있습니다. 이 설계는 서비스 공급자 연결이 동일한 빌드 아티팩트로 패키지화되므로 배포 파이프라인에서도 잘 작동합니다.

## <a name="create-build-and-deploy-options"></a>옵션 만들기, 빌드 및 배포

원하는 환경에 따라 논리 앱을 만들 때 다음과 같은 여러 옵션이 있습니다.

**단일 테넌트 환경**

| 옵션 | 리소스 및 도구 | 자세한 정보 |
|--------|---------------------|------------------|
| Azure portal | **논리 앱(표준)** 리소스 종류 | [단일 테넌트 Logic Apps에 대한 통합 워크플로 만들기 - Azure Portal](create-single-tenant-workflows-azure-portal.md) |
| Visual Studio Code | [**Azure Logic Apps(표준)** 확장](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurelogicapps) | [단일 테넌트 Logic Apps에 대한 통합 워크플로 만들기 - Visual Studio Code](create-single-tenant-workflows-visual-studio-code.md) |
| Azure CLI | Logic Apps Azure CLI 확장 | 아직 사용할 수 없음 |
||||

**다중 테넌트 환경**

| 옵션 | 리소스 및 도구 | 자세한 정보 |
|--------|---------------------|------------------|
| Azure portal | **Logic App(소비)** 리소스 종류 | [빠른 시작: 다중 테넌트 Azure Logic Apps에서 통합 워크플로 만들기 - Azure Portal](quickstart-create-first-logic-app-workflow.md) |
| Visual Studio Code | [**Azure Logic Apps(사용량)** 확장](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-logicapps) | [빠른 시작: 다중 테넌트 Azure Logic Apps에서 통합 워크플로 만들기 - Visual Studio Code](quickstart-create-logic-apps-visual-studio-code.md)
| Azure CLI | [**Logic Apps Azure CLI** 확장](https://github.com/Azure/azure-cli-extensions/tree/master/src/logic) | - [빠른 시작: 다중 테넌트 Azure Logic Apps에서 통합 워크플로 만들기 및 관리 - Azure CLI](quickstart-logic-apps-azure-cli.md) <p><p>- [az logic](/cli/azure/logic) |
| Azure 리소스 관리자 | [**논리 앱 만들기** ARM 템플릿](https://azure.microsoft.com/resources/templates/logic-app-create/) | [빠른 시작: 다중 테넌트 Azure Logic Apps에서 통합 워크플로 만들기 및 배포 - ARM 템플릿](quickstart-create-deploy-azure-resource-manager-template.md) |
| Azure PowerShell | [Az. LogicApp 모듈](/powershell/module/az.logicapp) | [Azure PowerShell 시작](/powershell/azure/get-started-azureps) |
| Azure REST API | [Azure Logic Apps REST API](/rest/api/logic) | [Azure REST API 시작하기 참조](/rest/api/azure) |
||||

**통합 서비스 환경**

| 옵션 | 리소스 및 도구 | 자세한 정보 |
|--------|---------------------|------------------|
| Azure portal | 기존 ISE 리소스를 사용하는 **논리 앱(사용량)** 리소스 종류 | [빠른 시작: 다중 테넌트 Azure Logic Apps에서 통합 워크플로 만들기 - Azure Portal](quickstart-create-first-logic-app-workflow.md)과 동일하지만 다중 테넌트 지역이 아닌 ISE를 선택합니다. |
||||

개발 환경이 **사용량** 또는 **표준** 논리 앱 리소스 중 어느 것을 만드는 지에 따라 달라지지만 Azure 구독에서 배포된 모든 논리 앱을 찾아서 액세스할 수 있습니다.

예를 들어 Azure Portal에서 **논리 앱** 페이지에는 **사용량** 및 **표준** 논리 앱 리소스 종류가 모두 표시됩니다. Visual Studio Code에서 배포된 논리 앱은 Azure 구독 아래에 나타나지만 사용한 확장(예: **Azure: Logic Apps(사용량)** 및 **Azure: Logic Apps(표준))** 별로 그룹화됩니다.

<a name="stateful-stateless"></a>

## <a name="stateful-and-stateless-workflows"></a>상태 저장 및 상태 비저장 워크플로

**논리 앱(표준)** 리소스 종류를 사용하면 동일한 논리 앱 내에서 다음과 같은 워크플로 유형을 만들 수 있습니다.

* *상태 저장*

  이전 이벤트의 데이터를 유지, 검토 또는 참조해야 하는 경우 상태 저장 워크플로를 만듭니다. 이 워크플로는 각 작업의 모든 입출력과 해당 상태를 외부 스토리지에 저장하고 전송하므로, 각 실행이 완료된 후 실행 세부 정보와 기록을 검토할 수 있습니다. 상태 저장 워크플로는 중단이 발생할 때 높은 복원력을 제공합니다. 서비스 및 시스템이 복원된 후에는 저장된 상태로부터 중단된 실행을 다시 생성하고 워크플로를 다시 실행하여 완료할 수 있습니다. 상태 저장 워크플로는 상태 비저장 워크플로보다 훨씬 긴 시간 동안 계속해서 실행 가능합니다.

* *상태 비저장*

  나중에 검토할 수 있도록 각 실행이 완료된 후 외부 스토리지에서 이전 이벤트의 데이터를 유지, 검토 또는 참조할 필요가 없는 경우에는 상태 비저장 워크플로를 만듭니다. 이 워크플로는 외부 스토리지가 아니라 ‘메모리 내에만’ 각 작업의 모든 입출력과 해당 상태를 저장합니다. 결과적으로 상태 비저장 워크플로는 실행 시간이 더 짧고(일반적으로 5분 미만), 응답 시간이 더 짧아서 성능이 더욱 빠르고, 처리량이 더 많고, 실행 세부 정보와 기록이 외부 스토리지에 저장되지 않으므로 실행 비용이 절감됩니다. 그러나 중단이 발생하면 중단된 실행이 자동으로 복원되지 않으므로 호출자는 중단된 실행을 수동으로 다시 제출해야 합니다. 이러한 워크플로는 동기적으로만 실행할 수 있습니다.

  > [!IMPORTANT]
  > 상태 비저장 워크플로는 ‘총’ 크기가 64KB를 초과하지 않는 파일과 같은 데이터 또는 콘텐츠를 처리할 때 최상의 성능을 제공합니다. 여러 개의 큰 첨부 파일과 같이 콘텐츠 크기가 더 커지면 워크플로 성능이 크게 저하되거나 메모리 부족 예외로 인해 워크플로가 충돌할 수도 있습니다. 워크플로가 더 큰 콘텐츠 크기를 처리해야 하는 경우에는 상태 저장 워크플로를 대신 사용합니다.

  간편한 디버깅을 위해 상태 비저장 워크플로에 실행 기록을 사용하도록 설정하고, 모두 마친 후 실행 기록을 사용하지 않도록 설정할 수 있습니다. 실행 기록을 사용하면 성능에 약간의 영향이 있습니다. 자세한 내용은 [Visual Studio Code에서 단일 테넌트 기반 워크플로 만들기](create-single-tenant-workflows-visual-studio-code.md#enable-run-history-stateless) 또는 [Azure Portal에서 단일 테넌트 기반 워크플로 만들기](create-single-tenant-workflows-visual-studio-code.md#enable-run-history-stateless)를 참조하세요.

  > [!NOTE]
  > 상태 비저장 워크플로는 현재 트리거가 아닌 Azure에 배포되는 [관리 커넥터](../connectors/managed.md)에 대한 *작업* 만 지원합니다. 워크플로를 시작하려면 [기본 제공 요청, Event Hubs 또는 Service Bus 트리거](../connectors/built-in.md) 중 하나를 선택합니다. 이러한 트리거는 기본적으로 Azure Logic Apps 런타임에서 실행됩니다. 제한된/사용할 수 없는/지원되지 않는 트리거, 작업 및 커넥터에 대한 자세한 내용은 [변경된 기능, 제한된 기능, 사용할 수 없는 기능 또는 지원되지 않는 기능](#limited-unavailable-unsupported)을 참조하세요.

<a name="nested-behavior"></a>

### <a name="nested-behavior-differences-between-stateful-and-stateless-workflows"></a>상태 저장 워크플로와 상태 비저장 워크플로의 중첩된 동작 차이점

[요청 트리거](../connectors/connectors-native-reqres.md), [HTTP Webhook 트리거](../connectors/connectors-native-webhook.md) 또는 [ApiConnectionWebhook 유형](../logic-apps/logic-apps-workflow-actions-triggers.md#apiconnectionwebhook-trigger)을 포함하고 있으며 HTTPS 요청을 받을 수 있는 관리형 커넥터 트리거를 사용하여 동일한 **논리 앱(표준)** 리소스에 있는 다른 워크플로에서 [호출 가능한 워크플로를 만들 수 있습니다](../logic-apps/logic-apps-http-endpoint.md).

다음은 부모 워크플로가 자식 워크플로를 호출한 후에 중첩된 워크플로가 따를 수 있는 동작 패턴입니다.

* 비동기 폴링 패턴

  부모는 초기 호출에 대한 응답을 기다리지 않지만, 자식의 실행이 완료될 때까지 계속해서 자식의 실행 기록을 확인합니다. 기본적으로 상태 저장 워크플로는 이 패턴을 따릅니다. 이 패턴은 [요청 시간 제한](../logic-apps/logic-apps-limits-and-config.md)을 초과할 가능성이 있는 장기 실행 자식 워크플로에 적합합니다.

* 동기 패턴("자체 유도")

  자식은 즉시 `202 ACCEPTED` 응답을 반환하여 호출을 승인하고, 부모는 자식의 결과를 기다리지 않고 다음 작업을 계속합니다. 그 대신 부모는 자식의 실행이 완료될 때 결과를 받습니다. 응답 작업이 없는 자식 상태 저장 워크플로는 항상 동기 패턴을 따릅니다. 자식 상태 저장 워크플로의 경우 실행 기록을 검토할 수 있습니다.

  이 동작을 사용하려면 워크플로의 JSON 정의에서 `operationOptions` 속성을 `DisableAsyncPattern`으로 설정합니다. 자세한 내용은 [트리거 및 작업 형식 - 작업 옵션](../logic-apps/logic-apps-workflow-actions-triggers.md#operation-options)을 참조하세요.

* 트리거 및 대기

  자식 상태 비저장 워크플로의 경우 부모는 결과를 반환하는 자식의 응답을 기다립니다. 이 패턴은 기본 제공 [HTTP 트리거 또는 작업](../connectors/connectors-native-http.md)을 사용하여 자식 워크플로를 호출할 때와 비슷한 방식으로 작동합니다. 응답 작업이 없는 자식 상태 비저장 워크플로는 즉시 `202 ACCEPTED` 응답을 반환하지만, 부모는 자식이 완료될 때까지 기다렸다가 다음 작업을 계속합니다. 이러한 동작은 자식 상태 비저장 워크플로에만 적용됩니다.

다음 표는 부모와 자식이 상태 저장인지, 상태 비저장인지 아니면 혼합 워크플로 유형인지에 따라 자식 워크플로의 동작을 지정합니다.

| 부모 워크플로 | 하위 워크플로 | 자식 동작 |
|-----------------|----------------|----------------|
| 상태 저장 | 상태 저장 | 비동기 또는 `"operationOptions": "DisableAsyncPattern"` 설정을 사용하는 동기 |
| 상태 저장 | 상태 비저장 | 트리거 및 대기 |
| 상태 비저장 | 상태 저장 | 동기 |
| 상태 비저장 | 상태 비저장 | 트리거 및 대기 |
||||

<a name="other-capabilities"></a>

## <a name="other-single-tenant-model-capabilities"></a>단일 테넌트 모델의 다른 기능

단일 테넌트 모델 및 **논리 앱(표준)** 리소스 종류에는 다음과 같은 많은 최신 기능과 새로운 기능이 포함되어 있습니다.

* SaaS(Software as a Service) 및 PaaS(Platform as a Service) 앱과 서비스를 위한 [400개가 넘는 관리형 커넥터](/connectors/connector-reference/connector-reference-logicapps-connectors)와 온-프레미스 시스템을 위한 커넥터를 사용하여 논리 앱과 워크플로를 만들 수 있습니다.

  * 이제 기본 제공 작업으로 더 많은 관리형 커넥터를 사용할 수 있으며 이러한 커넥터는 Azure Functions와 같은 다른 기본 제공 작업과 비슷하게 실행됩니다. 기본 제공 작업은 단일 테넌트 Azure Logic Apps 런타임에서 기본적으로 실행됩니다. 예를 들어 새로운 기본 제공 커넥터로 Azure Service Bus, Azure Event Hubs, SQL Server 및 MQ 등이 있습니다.

    > [!NOTE]
    > 기본 제공 SQL Server 버전의 경우 **쿼리 실행** 작업만 [온-프레미스 데이터 게이트웨이](logic-apps-gateway-connection.md)를 사용하지 않고 Azure 가상 네트워크에 직접 연결할 수 있습니다.

  * [단일 테넌트 Azure Logic Apps 확장 프레임워크](https://techcommunity.microsoft.com/t5/integrations-on-azure/azure-logic-apps-running-anywhere-built-in-connector/ba-p/1921272)를 사용하여 필요한 모든 서비스에 대한 자체적인 기본 제공 커넥터를 만들 수 있습니다. 이러한 커넥터는 Azure Service Bus 및 SQL Server와 같은 기본 제공 커넥터와 비슷하지만, 현재 지원되지 않는 [사용자 지정 관리형 커넥터](../connectors/apis-list.md#custom-apis-and-connectors)와는 다릅니다. 사용자 지정 기본 제공 커넥터는 단일 테넌트 런타임과 동일한 프로세스에서 실행되므로 더 많은 처리량, 짧은 대기 시간 및 로컬 연결을 제공합니다.

    제작 기능은 현재 Visual Studio Code에서만 사용할 수 있지만, 기본적으로 사용하도록 설정되어 있지 않습니다. 이러한 커넥터를 만들려면 [프로젝트를 확장 번들 기반(Node.js)에서 NuGet 패키지 기반(.NET)으로 변환](create-single-tenant-workflows-visual-studio-code.md#enable-built-in-connector-authoring)해야 합니다. 자세한 내용은 [어디서나 실행되는 Azure Logic Apps - 기본 제공 커넥터 확장성](https://techcommunity.microsoft.com/t5/integrations-on-azure/azure-logic-apps-running-anywhere-built-in-connector/ba-p/1921272)을 참조하세요.

  * 통합 계정 없이 Liquid 작업과 XML 작업에 다음 작업을 사용할 수 있습니다. 이 작업에는 다음 작업이 포함됩니다.

    * XML: **XML 변환**, **XML 유효성 검사**

    * Liquid: **JSON 간 변환**, **JSON에서 텍스트로 변환**, **XML에서 JSON으로 변환**, **XML에서 텍스트로 변환**

    > [!NOTE]
    > 단일 테넌트 Azure Logic Apps(표준)에서 이 작업을 사용하려면 Liquid 맵, XML 맵 또는 XML 스키마가 있어야 합니다. **스키마** 및 **맵** 섹션이 포함된 **아티팩트** 아래 논리 앱 리소스 메뉴의 Azure Portal에서 이 아티팩트를 업로드할 수 있습니다. 또는 각 **Mpas** 및 **Schemas** 폴더를 사용하여 Visual Studio Code 프로젝트의 **Artifacts** 폴더에 이 아티팩트를 추가할 수 있습니다. 그런 다음, ‘동일한 논리 앱 리소스’ 내의 여러 워크플로에서 이 아티팩트를 사용할 수 있습니다.

  * Azure Logic Apps는 클라우드 연결 런타임 엔드포인트에 요청을 보내는 데 사용할 수 있는 SAS(공유 액세스 서명) 연결 문자열을 생성하기 때문에 **논리 앱(표준)** 리소스를 어디서나 실행할 수 있습니다. Azure Logic Apps 서비스는 Azure에 배포할 때 관련 값을 Azure Key Vault에 쉽게 저장할 수 있도록 이 연결 문자열을 다른 애플리케이션과 함께 저장합니다.

    > [!NOTE]
    > 기본적으로 **논리 앱(표준)** 리소스에는 실행 시 연결을 인증하기 위해 자동으로 사용하도록 설정되는 [시스템이 할당한 관리 ID](../logic-apps/create-managed-service-identity.md)가 있습니다. 이 ID는 연결을 만들 때 사용하는 인증 자격 증명이나 연결 문자열과는 다릅니다. 이 ID를 사용하지 않도록 설정하면 실행 시 연결이 작동하지 않습니다. 이 설정을 보려면 논리 앱 메뉴의 **설정** 에서 **ID** 를 선택합니다.

* Visual Studio Code 개발 환경에서 논리 앱 및 워크플로를 로컬로 실행, 테스트 및 디버그할 수 있습니다.

  논리 앱을 실행하고 테스트하기 전에 워크플로에 대한 **workflow.json** 파일 내에 중단점을 추가하고 사용하면 디버깅을 더 쉽게 수행할 수 있습니다. 하지만 현재는 트리거가 아닌 작업에만 중단점이 지원됩니다. 자세한 내용은 [Visual Studio Code에서 단일 테넌트 기반 워크플로 만들기](create-single-tenant-workflows-visual-studio-code.md#manage-breakpoints)를 참조하세요.

* Visual Studio Code에서 Azure, Azure Arc 지원 Logic Apps 등의 다양한 호스팅 환경에 논리 앱과 워크플로를 직접 게시하거나 배포합니다.

* Azure 구독 및 논리 앱 설정에서 지원하는 경우 [Application Insights](../azure-monitor/app/app-insights-overview.md)를 사용하여 논리 앱에 진단 로깅 및 추적 기능을 사용하도록 설정합니다.

* Azure Functions와 마찬가지로 [Azure Functions 프리미엄 플랜](../azure-functions/functions-premium-plan.md)을 사용하여 논리 앱을 만들고 배포할 때 Azure 가상 네트워크와 비공개로 연결하고 통합하는 기능을 비롯한 네트워킹 기능을 사용합니다. 자세한 내용은 다음 설명서를 검토하세요.

  * [Azure Functions 네트워킹 옵션](../azure-functions/functions-networking-options.md)

  * [어디서나 실행되는 Azure Logic Apps - Azure Logic Apps의 네트워킹 기능](https://techcommunity.microsoft.com/t5/integrations-on-azure/logic-apps-anywhere-networking-possibilities-with-logic-app/ba-p/2105047)

* **논리 앱(표준)** 리소스의 개별 워크플로에서 사용하는 관리형 연결을 위한 액세스 키를 다시 생성합니다. 이 작업의 경우 논리 앱 리소스 수준이 아닌 [개별 워크플로 수준에서 **Logic Apps(소비)** 리소스에 대해 동일한 단계를 수행](logic-apps-securing-a-logic-app.md#regenerate-access-keys)합니다.

<a name="limited-unavailable-unsupported"></a>

## <a name="changed-limited-unavailable-or-unsupported-capabilities"></a>변경된 기능, 제한된 기능, 사용할 수 없는 기능 또는 지원되지 않는 기능

**논리 앱(표준)** 에서는 이러한 기능이 변경되었거나, 현재 제한되어 있거나, 사용할 수 없거나, 지원되지 않습니다.

* **트리거 및 작업**: 기본 제공 트리거 및 작업은 단일 테넌트 Azure Logic Apps 런타임에서 기본적으로 실행되는 반면, 관리형 커넥터는 Azure에서 호스트되고 실행됩니다. 슬라이딩 윈도우 및 Batch와 같은 일부 기본 제공 트리거는 사용할 수 없습니다. 상태 저장 또는 상태 비저장 워크플로를 시작하려면 [기본 제공 되풀이, 요청, HTTP, HTTP Webhook, Event Hubs 또는 Service Bus 트리거](../connectors/apis-list.md)를 사용합니다. 기본 제공 트리거 및 작업은 디자이너의 **기본 제공** 탭에 나타납니다.

  *상태 저장* 워크플로의 경우 아래에 나열된 사용할 수 없는 작업을 제외하고 [관리형 커넥터 트리거 및 작업](../connectors/managed.md)이 **Azure** 탭 아래에 표시됩니다. *상태 비저장* 워크플로의 경우 트리거를 선택할 때 **Azure** 탭이 나타나지 않습니다. [트리거가 아닌 관리형 커넥터 *작업*](../connectors/managed.md)만 선택할 수 있습니다. 상태 비저장 워크플로에 대해 Azure에서 호스트되는 관리형 커넥터를 사용하도록 설정할 수 있지만, 사용자가 추가할 수 있는 관리형 커넥터 트리거가 디자이너에 표시되지 않습니다.

  > [!NOTE]
  > Visual Studio Code에서 로컬로 실행하려면 webhook 기반 트리거 및 작업에서 추가 설정이 필요합니다. 자세한 내용은 [Visual Studio Code에서 단일 테넌트 기반 워크플로 만들기](create-single-tenant-workflows-visual-studio-code.md#webhook-setup)를 참조하세요.

  * 사용할 수 없는 트리거 및 작업은 변경되었거나, 현재 제한되었거나, 지원되지 않거나, 제공되지 않습니다.

    * [온-프레미스 데이터 게이트웨이 *트리거*](../connectors/managed.md#on-premises-connectors)는 사용할 수 없지만 게이트웨이 작업은 *사용할 수 있습니다*.

    * 기본 제공 작업 [Azure Functions - Azure 함수 선택](logic-apps-azure-functions.md)은 이제 **Azure Function 작업 - Azure 함수 호출** 입니다. 이 작업은 현재 **HTTP 트리거** 템플릿에서 만든 함수에 대해서만 작동합니다.

      Azure Portal에서 사용자 환경을 통해 연결을 만들어 액세스할 수 있는 HTTP 트리거 함수를 선택할 수 있습니다. 코드 보기 또는 **workflow.json** 파일에서 함수 작업의 JSON 정의를 살펴보면 작업이 `connectionName` 참조를 통해 함수를 참조한다는 것을 알 수 있습니다. 이 버전은 함수 정보를 연결로 추상화하며, 이 내용은 Visual Studio Code에서 연결을 만든 후에 사용할 수 있는 논리 앱 프로젝트의 **connections.json** 파일에서 찾을 수 있습니다.

      > [!NOTE]
      > 단일 테넌트 모델에서 함수 작업은 쿼리 문자열 인증만 지원합니다. Azure Logic Apps는 연결을 설정할 때 함수에서 기본 키를 가져오고, 이 키를 앱의 설정에 저장하며, 함수를 호출할 때 이 키를 인증에 사용합니다.
      >
      > 다중 테넌트 모델과 마찬가지로 이 키를 갱신하는 경우(예: 포털의 Azure Functions 환경을 통해) 잘못된 키로 인해 함수 작업이 더 이상 작동하지 않습니다. 이 문제를 해결하려면 호출하려는 함수에 대한 연결을 다시 만들거나 앱의 설정을 새 키로 업데이트해야 합니다.

    * 기본 제공 작업 [인라인 코드](logic-apps-add-run-inline-code.md)의 이름이 **인라인 코드 작업** 으로 바뀌고, 더 이상 통합 계정이 필요하지 않고, [제한 사항이 업데이트](logic-apps-limits-and-config.md)되었습니다.

    * 기본 제공 작업 [Azure Logic Apps - 논리 앱 워크플로 선택](logic-apps-http-endpoint.md)은 이제 **워크플로 작업 - 이 워크플로 앱의 워크플로 호출** 입니다.

    * [통합 계정의 일부 기본 제공 트리거와 작업](../connectors/managed.md#integration-account-connectors)은 사용할 수 없습니다(예: **플랫 파일** 인코딩 및 디코딩 작업).

    * [사용자 지정 관리형 커넥터는](../connectors/apis-list.md#custom-apis-and-connectors) 현재 지원되지 않습니다. 하지만 Visual Studio Code를 사용하는 경우에는 *사용자 지정 기본 제공 작업* 을 만들 수 있습니다. 자세한 내용은 [Visual Studio Code를 사용하여 단일 테넌트 기반 워크플로 만들기](create-single-tenant-workflows-visual-studio-code.md#enable-built-in-connector-authoring)를 참조하세요.

* **Visual Studio Code에서 중단점 디버깅**: 워크플로에 대한 **workflow.json** 파일 내에서 중단점을 추가하고 사용할 수 있지만, 현재는 트리거가 아닌 작업에만 중단점이 지원됩니다. 자세한 내용은 [Visual Studio Code에서 단일 테넌트 기반 워크플로 만들기](create-single-tenant-workflows-visual-studio-code.md#manage-breakpoints)를 참조하세요.

* **트리거 기록 및 실행 기록**: **논리 앱(표준)** 리소스 종류의 경우 논리 앱 수준이 아닌 워크플로 수준에서 트리거 기록 및 실행 기록이 Azure Portal에 표시됩니다. 자세한 내용은 [Azure Portal을 사용하여 단일 테넌트 기반 워크플로 만들기](create-single-tenant-workflows-azure-portal.md)를 참조하세요.

* **확대/축소 컨트롤**: 현재는 디자이너에서 확대/축소 컨트롤을 사용할 수 없습니다.

* **배포 대상**: **논리 앱(표준)** 리소스 종류를 [ISE(통합 서비스 환경)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) 또는 Azure 배포 슬롯에 배포할 수 없습니다.

* **Azure API Management**: 현재 **논리 앱(표준)** 리소스 종류를 Azure API Management로 가져올 수 없습니다. 그러나 **Logic App(소비)** 리소스 종류는 가져올 수 있습니다.

<a name="firewall-permissions"></a>

## <a name="strict-network-and-firewall-traffic-permissions"></a>엄격한 네트워크 및 방화벽 트래픽 권한

현재 환경에 트래픽을 제한하는 엄격한 네트워크 요구 사항 또는 방화벽이 있는 경우 논리 앱 워크플로의 트리거 또는 작업 연결에 대한 액세스를 허용해야 합니다. 이러한 연결에 대한 FQDN(정규화된 도메인 이름)을 찾으려면 다음 토픽에서 해당 섹션을 검토하세요.

* [단일 테넌트 논리 앱에 대한 방화벽 권한 - Visual Studio Code](create-single-tenant-workflows-visual-studio-code.md#firewall-setup)
* [단일 테넌트 논리 앱에 대한 방화벽 권한 - Azure Portal](create-single-tenant-workflows-azure-portal.md#firewall-setup)

## <a name="next-steps"></a>다음 단계

* [Azure Portal에서 단일 테넌트 기반 워크플로 만들기](create-single-tenant-workflows-azure-portal.md)
* [Visual Studio Code에서 단일 테넌트 기반 워크플로 만들기](create-single-tenant-workflows-visual-studio-code.md)

단일 테넌트 Azure Logic Apps에 대한 여러분의 의견을 듣고 싶습니다.

* 버그 또는 문제가 발견되는 경우 [GitHub에서 문제를 알려주세요](https://github.com/Azure/logicapps/issues).
* [이 사용자 의견 양식을 사용](https://aka.ms/lafeedback)하여 질문, 요청, 의견 및 기타 피드백을 보내주세요.
