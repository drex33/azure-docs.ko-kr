---
title: 단일 테넌트 Azure Logic Apps용 DevOps 배포
description: 단일 테넌트 Azure Logic Apps용 DevOps 배포에 대해 알아봅니다.
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, azla
ms.topic: conceptual
ms.date: 09/13/2021
ms.openlocfilehash: a94abb3b2c640dbd0bcd372e83844d660af514cd
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128548200"
---
# <a name="devops-deployment-for-single-tenant-azure-logic-apps"></a>단일 테넌트 Azure Logic Apps용 DevOps 배포

분산 및 네이티브 클라우드 앱을 점점 더 많이 사용하게 되면서 조직은 더 많은 환경에서 더 많은 분산 구성 요소를 처리하게 되었습니다. 제어 및 일관성을 유지하기 위해 DevOps 도구 및 프로세스를 사용하여 환경을 자동화하고 더 많은 구성 요소를 더 빠르고 안정적으로 배포할 수 있습니다.

이 문서에서는 단일 테넌트 Azure Logic Apps에 대한 현재 CI/CD(연속 통합 및 연속 배포) 환경을 소개하고 간략하게 설명합니다.

<a name="single-tenant-versus-multi-tenant"></a>

## <a name="single-tenant-versus-multi-tenant"></a>단일 테넌트 대 다중 테넌트

*다중 테넌트* Azure Logic Apps에서 리소스 배포는 논리 앱과 인프라 모두에 대한 리소스 프로비저닝을 결합하고 처리하는 ARM 템플릿(Azure Resource Manager 템플릿)을 기준으로 합니다. *단일 테넌트* Azure Logic Apps에서는 앱과 인프라 간에 리소스 프로비저닝을 구분할 수 있으므로 배포가 더 쉬워집니다.

**논리 앱(표준)** 리소스 종류를 사용하여 논리 앱을 만들 때 워크플로는 다시 디자인된 단일 테넌트 Azure Logic Apps 런타임을 통해 구동됩니다. 이 런타임은 [Azure Functions 확장성 모델](../azure-functions/functions-bindings-register.md) 확장성을 사용하며 [Azure Functions 런타임에 확장으로 호스트됩니다](https://techcommunity.microsoft.com/t5/integrations-on-azure/azure-logic-apps-running-anywhere-runtime-deep-dive/ba-p/1835564). 이 디자인은 논리 앱을 위한 이식성, 유연성 및 더 많은 성능과 Azure Functions 플랫폼 및 Azure App Service 에코시스템에서 상속된 기타 기능 및 이점을 제공합니다.

예를 들어 재디자인한 컨테이너화된 런타임과 워크플로를 논리 앱의 일부로 함께 패키지할 수 있습니다. 논리 앱 리소스를 즉시 배포할 수 있는 아티팩트로 빌드, 어셈블 및 압축하는 일반 단계 또는 작업을 사용할 수 있습니다. 앱을 배포하려면 아티팩트를 호스트 환경에 복사한 다음, 앱을 시작하여 워크플로를 실행합니다. 또는 이미 잘 알고 사용하고 있는 도구와 프로세스를 사용하여 아티팩트를 배포 파이프라인에 통합합니다. 예를 들어, 시나리오에 컨테이너가 필요한 경우 논리 앱을 컨테이너화한 후 기존 파이프라인에 통합할 수 있습니다.

가상 네트워크 및 연결과 같은 인프라 리소스를 설정하고 배포하려면 ARM 템플릿을 계속 사용하고 이러한 리소스를 해당 목적으로 사용하는 다른 프로세스 및 파이프라인과 함께 별도로 프로비저닝할 수 있습니다.

표준 빌드 및 배포 옵션을 사용하면 인프라 배포와는 별도로 앱 개발에 집중할 수 있습니다. 따라서 일반 앱에 사용하는 여러 유사하거나 동일한 배포 옵션을 적용할 수 있는 보다 일반적인 프로젝트 모델을 얻게 됩니다. 또한 앱 프로젝트를 중심으로 배포 파이프라인을 빌드하고 프로덕션에 게시하기 전에 필요한 테스트 및 유효성 검사를 실행하기 위한 보다 일관된 환경을 활용할 수 있습니다. 사용하는 기술 스택에 관계 없이 선택한 도구를 사용하여 논리 앱을 배포할 수 있습니다.

<a name="devops-deployment-features"></a>

## <a name="devops-deployment-capabilities"></a>DevOps 배포 기능

단일 테넌트 Azure Logic Apps는 Azure Functions 플랫폼과 Azure App Service 에코시스템의 많은 기능과 이점을 상속합니다. 이러한 업데이트에는 전체 새 배포 모델 및 논리 앱 워크플로에 DevOps를 사용하는 다양한 방법이 포함되어 있습니다.

<a name="local-development-testing"></a>

### <a name="local-development-and-testing"></a>로컬 개발 및 테스트

Azure Logic Apps(표준) 확장과 함께 Visual Studio Code를 사용하는 경우 Azure에 배포하지 않고도 개발 환경에서 단일 테넌트 기반 논리 앱 워크플로를 로컬로 개발, 빌드 및 실행할 수 있습니다. Azure Functions 실행할 수 있는 워크플로를 실행할 수도 있습니다. 예를 들어, 시나리오에 컨테이너가 필요한 경우 논리 앱을 컨테이너화하고 컨테이너로 배포할 수 있습니다.

이 기능은 주요 개선 사항으로, 다중 테넌트 모델에 비해 상당한 이점을 제공하며, 이를 위해 Azure에서 기존 및 실행 중인 리소스를 토대로 개발을 진행해야 합니다.

<a name="separate-concerns"></a>

### <a name="separate-concerns"></a>관심 영역 분리

단일 테넌트 모델은 앱과 기본 인프라 간의 문제를 분리하는 기능을 제공합니다. 예를 들어 앱을 다른 환경을 위한 변경할 수 없는 아티팩트로서 별도로 개발, 빌드, 압축 및 배포할 수 있습니다. 논리 앱 워크플로에는 일반적으로 기본 인프라보다 더 자주 업데이트하는 "애플리케이션 코드"가 있습니다. 이러한 계층을 분리하여 논리 앱의 워크플로를 빌드하는 데 좀 더 집중하고 여러 환경에 필요한 리소스를 배포하는 데 필요한 업무를 좀 더 줄일 수 있습니다.

![앱 및 인프라에 대한 별도의 배포 파이프라인을 보여 주는 개념 다이어그램](./media/devops-deployment-single-tenant/deployment-pipelines-logic-apps.png)

<a name="resource-structure"></a>

### <a name="logic-app-resource-structure"></a>논리 앱 리소스 구조

[!INCLUDE [Visual Studio Code - logic app resource structure](../../includes/logic-apps-single-tenant-resource-structure.md)]

### <a name="logic-app-project-structure"></a>논리 앱 프로젝트 구조

[!INCLUDE [Visual Studio Code - logic app project structure](../../includes/logic-apps-single-tenant-project-structure-visual-studio-code.md)]

<a name="deployment-containers"></a>

### <a name="container-deployment"></a>컨테이너 배포

단일 테 넌 트 Azure Logic Apps는 컨테이너에 대 한 배포를 지원 합니다. 즉, 논리 앱 워크플로를 컨테이너 화 컨테이너를 실행할 수 있는 위치에서 실행할 수 있습니다. 앱을 컨테이너화한 후 배포는 배포하고 관리하는 다른 컨테이너와 거의 동일하게 작동합니다.

Azure DevOps를 포함하는 예제는 [컨테이너에 대한 CI/CD](https://azure.microsoft.com/solutions/architecture/cicd-for-containers/)를 검토하세요.

<a name="app-settings-parameters"></a>

### <a name="app-settings-and-parameters"></a>앱 설정 및 매개 변수

다중 테넌트 Azure Logic Apps에서 ARM 템플릿은 다양한 개발, 테스트 및 프로덕션 환경에서 논리 앱에 대한 환경 변수를 유지 관리해야 할 때 어려움을 유발합니다. ARM 템플릿의 모든 항목은 배포 시 정의됩니다. 단일 변수만 경 해야 하는 경우 모든 항목을 다시 배포해야 합니다.

단일 테넌트 Azure Logic Apps에서 앱 설정 및 매개 변수를 사용하여 런타임에 환경 변수를 호출하고 참조할 수 있으므로 자주 다시 배포할 필요가 없습니다.

<a name="managed-connectors-built-in-operations"></a>

## <a name="managed-connectors-and-built-in-operations"></a>관리형 커넥터 및 기본 제공 작업

Azure Logic Apps 에코시스템은 단일 테넌트 Azure Logic Apps에서 사용할 수 있는 지속적으로 증가하는 컬렉션의 일부로 [수백 개의 Microsoft 관리 커넥터](/connectors/connector-reference/connector-reference-logicapps-connectors) 및 기본 제공 작업을 제공합니다. Microsoft에서 이러한 커넥터와 기본 제공 작업을 유지 관리하는 방법은 단일 테넌트 Azure Logic Apps에서도 거의 동일하게 유지됩니다.

가장 중요한 개선 사항은 단일 테넌트 서비스를 통해 더 인기 있는 관리형 커넥터를 기본 제공 작업으로 사용할 수 있다는 것입니다. 예를 들어 Azure Service Bus, Azure Event Hubs, SQL 등에 대해 기본 제공 작업을 사용할 수 있습니다. 한편, 관리형 커넥터 버전은 계속 사용할 수 있으며 계속 작동합니다.

기본 제공 작업을 사용하여 만든 연결은 기본 제공 연결 또는 *서비스 공급자 연결* 이라고 합니다. 기본 제공 작업 및 해당 연결은 워크플로를 실행하는 동일한 프로세스에서 로컬로 실행됩니다. 둘 다 재디자인된 Logic Apps 런타임에 호스트됩니다. 이와 대조적으로 관리형 연결 또는 API 연결은 ARM 템플릿을 사용하여 배포하는 Azure 리소스와는 별도로 만들어지고 실행됩니다. 따라서 기본 제공 작업 및 해당 연결을 통해 워크플로에 근접할 수 있으므로 성능을 향상시킬 수 있습니다. 이 디자인은 서비스 공급자 연결이 동일한 빌드 아티팩트로 패키지되므로 배포 파이프라인에서도 잘 작동합니다.

Visual Studio Code에서 디자이너를 사용하여 워크플로를 개발하거나 변경하는 경우 Logic Apps 엔진은 프로젝트의 **connections.json** 파일에 필요한 연결 메타데이터를 자동으로 생성합니다. 다음 섹션에서는 워크플로에서 만들 수 있는 세 가지 종류의 연결에 대해 설명합니다. 환경 간을 이동할 때 엔드포인트가 변경되기 때문에 연결 형식마다 JSON 구조가 다르다는 사실을 이해하는 것이 중요합니다.

<a name="service-provider-connections"></a>

### <a name="service-provider-connections"></a>서비스 공급자 연결

단일 테넌트 Azure Logic Apps에서 Azure Service Bus 또는 Azure Event Hubs와 같은 서비스에 대한 기본 제공 작업을 사용하는 경우 워크플로와 동일한 프로세스에서 실행되는 서비스 공급자 연결을 만듭니다. 이 연결 인프라는 논리 앱 리소스의 일부로 호스트 되 고 관리 되며, 앱 설정은 워크플로에서 사용 하는 모든 서비스 공급자 기반 기본 제공 작업에 대 한 연결 문자열을 저장 합니다.

논리 앱 프로젝트에서 각 워크플로에는 워크플로의 기본 JSON 정의가 포함된 workflow.json 파일이 포함되어 있습니다. 그런 다음, 이 워크플로 정의는 프로젝트 connections.json 파일의 필요한 연결 문자열을 참조합니다.

다음 예제에서는 기본 제공 Service Bus 작업에 대한 서비스 공급자 연결이 프로젝트의 connections.json 파일에 표시되는 방식을 보여 줍니다.

```json
"serviceProviderConnections": {
   "{service-bus-connection-name}": {
      "parameterValues": {
         "connectionString": "@appsetting('servicebus_connectionString')"
      },
      "serviceProvider": {
         "id": "/serviceProviders/serviceBus"
      },
      "displayName": "{service-bus-connection-name}"
   },
   <...>
}
```

<a name="managed-connections"></a>

### <a name="managed-connections"></a>관리형 연결

워크플로에서 관리형 커넥터를 처음 사용하는 경우 대상 서비스 또는 시스템에 대한 관리형 API 연결을 만들고 ID를 인증하라는 메시지가 표시됩니다. 이러한 커넥터는 Azure의 공유 커넥터 에코시스템에서 관리됩니다. API 연결이 존재하고 Azure에서 별도의 리소스로 실행됩니다.

Visual Studio Code에서 디자이너를 사용하여 워크플로를 계속 만들고 개발하는 동안 Logic Apps 엔진은 워크플로의 관리형 커넥터에 필요한 리소스를 Azure에서 자동으로 만듭니다. 이 엔진은 논리 앱을 포함하도록 디자인한 Azure 리소스 그룹에 이러한 연결 리소스를 자동으로 추가합니다.

다음 예제에서는 관리형 Service Bus 커넥터에 대한 API 연결이 프로젝트의 connections.json 파일에 표시되는 방법을 보여 줍니다.

```json
"managedApiConnections": {
   "{service-bus-connection-name}": { 
      "api": {
         "id": "/subscriptions/{subscription-ID}/providers/Microsoft.Web/locations/{region}/managedApis/servicebus"
      },
      "connection": { 
         "id": "/subscriptions/{subscription-ID}/resourcegroups/{resource-group-name}/providers/Microsoft.Web/connections/servicebus"
      }, 
      "connectionRuntimeUrl": "{connection-runtime-URL}",
      "authentication": { 
         "type": "Raw",
         "scheme": "Key",
         "parameter": "@appsetting('servicebus_1-connectionKey')"
      },
   },
   <...>
}
```

<a name="azure-functions-connections"></a>

### <a name="azure-functions-connections"></a>Azure Functions 연결

Azure Functions에서 만들고 호스트한 함수를 호출하려면 기본 제공 Azure Functions 작업을 사용합니다. Azure Functions 호출에 대한 연결 메타데이터는 다른 기본 제공 연결과 다릅니다. 이 메타데이터는 논리 앱 프로젝트의 connections.json 파일에 저장되지만 다음과 같이 다르게 보입니다.

```json
"functionConnections": {
   "{function-operation-name}": {
      "function": { 
         "id": "/subscriptions/{subscription-ID}/resourceGroups/{resource-group-name}/providers/Microsoft.Web/sites/{function-app-name}/functions/{function-name}"
      },
      "triggerUrl": "{function-url}",
      "authentication": {
        "type": "QueryString",
         "name": "Code",
         "value": "@appsetting('azureFunctionOperation_functionAppKey')"
      }, 
      "displayName": "{functions-connection-display-name}"
   },
   <...>
}
```

## <a name="authentication"></a>인증

단일 테넌트 Azure Logic Apps에서 논리 앱 워크플로에 대한 호스팅 모델은 워크로드가 다중 테넌트 모델보다 좀 더 격리된 방식이 유리한 단일 테넌트입니다. 또한 단일 테 넌 트 Azure Logic Apps 런타임은 이식 가능 합니다. 즉, Azure Functions를 실행할 수 있는 워크플로를 실행할 수 있습니다. 그러나 이 디자인은 논리 앱이 Azure에서 관리형 커넥터 에코시스템에 액세스할 수 있도록 해당 ID를 인증하는 방법이 필요합니다. 또한 관리형 연결을 사용할 때 작업을 실행하려면 앱에 올바른 권한이 필요합니다.

기본적으로 각 단일 테넌트 기반 논리 앱에는 자동으로 사용하도록 설정된 시스템 할당 관리 ID가 있습니다. 이 ID는 연결을 만들 때 사용하는 인증 자격 증명이나 연결 문자열과는 다릅니다. 런타임에 논리 앱은 이 ID를 사용하여 Azure 액세스 정책을 통해 해당 연결을 인증합니다. 이 ID를 사용하지 않도록 설정하면 런타임에 연결이 작동하지 않습니다.

다음 섹션에서는 논리 앱이 실행되는 위치에 따라 관리형 연결을 인증하는 데 사용할 수 있는 인증 유형에 대한 자세한 정보를 제공합니다. 각 관리형 연결에 대해 논리 앱 프로젝트의 connections.json 파일에는 논리 앱이 관리형 연결을 인증하는 데 사용할 수 있는 인증 유형을 지정하는 `authentication` 개체가 있습니다.

### <a name="managed-identity"></a>관리 ID

Azure에서 호스트되고 실행되는 논리 앱의 경우 [관리 ID](create-managed-service-identity.md)는 Azure에서 호스트되고 실행되는 관리형 연결을 인증하는 데 사용할 수 있는 기본 및 권장 인증 유형입니다. 논리 앱 프로젝트의 connections.json 파일에서 관리형 연결에는 인증 유형으로 `ManagedServiceIdentity`를 지정하는 `authentication` 개체가 있습니다.

```json
"authentication": {
   "type": "ManagedServiceIdentity"
}
```

### <a name="raw"></a>Raw

Visual Studio Code를 사용하여 로컬 개발 환경에서 실행되는 논리 앱의 경우 원시 인증 키는 Azure에서 호스트되고 실행되는 관리형 연결을 인증하는 데 사용됩니다. 이러한 키는 프로덕션이 아닌 개발 용도로만 디자인되었으며 7일의 만료 기간이 적용됩니다. 논리 앱 프로젝트의 connections.json 파일에서 관리형 연결에는 다음 인증 정보를 지정하는 `authentication` 개체가 있습니다.

```json
"authentication": {
   "type": "Raw", 
   "scheme": "Key", 
   "parameter": "@appsetting('connectionKey')"
 }
```

## <a name="next-steps"></a>다음 단계

- [단일 테넌트 Azure Logic Apps용 DevOps 배포 설정](set-up-devops-deployment-single-tenant-azure-logic-apps.md)
