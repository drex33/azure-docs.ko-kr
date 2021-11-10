---
title: 단일 테넌트 워크플로의 값에 대한 매개 변수 만들기
description: 단일 테넌트 Azure Logic Apps의 배포 환경에서 달라지는 워크플로의 값에 대한 매개 변수를 정의합니다.
services: logic-apps
ms.suite: integration
ms.reviewer: azla
ms.topic: how-to
ms.date: 08/09/2021
ms.openlocfilehash: a29eda23d12ca07057ff1081ae8d9bc4cfdc56ed
ms.sourcegitcommit: 838413a8fc8cd53581973472b7832d87c58e3d5f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/10/2021
ms.locfileid: "132137024"
---
# <a name="create-parameters-to-use-in-workflows-across-environments-in-single-tenant-azure-logic-apps"></a>단일 테넌트 Azure Logic Apps의 환경에 있는 워크플로에서 사용할 매개 변수 만들기

Azure Logic Apps에서 *매개 변수* 를 정의하여 개발, 테스트 및 프로덕션 환경의 워크플로에서 변경될 수 있는 값을 추상화할 수 있습니다. 대신 매개 변수를 사용하는 경우 워크플로 디자인에 좀 더 집중하고 나중에 환경별 변수를 삽입할 수 있습니다.

이 문서에서는 단일 테넌트 Azure Logic Apps에서 매개 변수가 작동하는 방식과 단일 테넌트 매개 변수 환경을 사용하여 환경 변수를 편집, 참조 및 관리하는 방법을 소개합니다.

<a name="parameters-introduction"></a>

## <a name="parameters-in-single-tenant-versus-multi-tenant"></a>단일 테넌트 대 다중 테넌트의 매개 변수

*다중 테넌트* Azure Logic Apps를 사용해본 경우 매개 변수에 이미 익숙할 것입니다. 단일 테넌트 Azure Logic Apps의 워크플로 입력 매개 변수화는 다중 테넌트 Azure Logic Apps와 비슷한 방식으로 작동하지만 큰 차이가 있습니다.

예를 들어, 단일 테넌트 및 다중 테넌트 서비스 모두에서 워크플로 디자이너로 작업할 때 매개 변수를 정의할 수 있습니다. 매개 변수를 정의한 후에는 동일한 논리 앱 리소스에 있는 모든 워크플로 또는 연결에서 해당 매개 변수를 참조할 수 있습니다. 그러나 다중 테넌트 서비스의 경우 디자이너에서 매개 변수를 만들고 사용한 후에는 ARM 템플릿(Azure Resource Manager 템플릿) 및 템플릿 매개 변수 파일에서 환경 변수를 정의하고 설정합니다. 이 시나리오에서는 배포 시에 매개 변수를 정의하고 설정해야 합니다. 즉, 변수를 하나만 변경해야 하는 경우에도 논리 앱의 ARM 템플릿을 다시 배포해야 합니다.

이에 비해 *단일 테넌트* 서비스에서는 매개 변수와 앱 설정을 모두 사용하여 런타임 및 배포 시에 환경 변수를 사용할 수 있습니다. 단일 테넌트 Azure Logic Apps의 앱 설정에는 동일한 논리 앱의 *모든 워크플로* 에 대한 전역 구성 옵션이 포함됩니다. 자세한 내용은 [단일 테넌트 기반 논리 앱에 대한 호스트 및 앱 설정 편집](edit-app-settings-host-settings.md)을 검토하세요.

예를 들어, 앱 설정을 사용하여 Azure Key Vault와 통합하고 연결 문자열 및 키와 같은 [보안 문자열 을 직접 참조](../app-service/app-service-key-vault-references.md)할 수 있습니다. 배포 시 환경 변수를 정의할 수 있는 ARM 템플릿과 마찬가지로 [논리 앱 워크플로 정의](/azure/templates/microsoft.logic/workflows) 내에서 앱 설정을 정의할 수 있습니다. 그런 다음, 연결 엔드포인트, 스토리지 문자열 등 동적으로 생성된 인프라 값을 캡처할 수 있습니다.

단, 앱 설정에는 크기 제한이 있으며 Azure Logic Apps의 특정 영역에서는 참조가 불가능합니다. 매개 변수는 큰 값 크기 및 복잡한 개체에 대한 지원과 같은 앱 설정보다 광범위한 사용 사례를 제공합니다.

예를 들어, Visual Studio Code를 로컬 개발 도구로 사용하여 워크플로를 로컬로 실행하는 경우 논리 앱 프로젝트에서 **parameters.json** 파일을 사용하여 매개 변수를 정의할 수 있습니다. 그런 다음, 프로젝트의 **workflow.json** 파일에 있는 워크플로 또는 프로젝트의 **connections.json** 파일에 있는 연결 개체에서 이 매개 변수 파일의 매개 변수를 참조할 수 있습니다. 다음 목록에서는 몇 가지 일반적인 사용 사례를 설명합니다.

* 테스트 중에 사용하는 모든 값을 포함하는 테스트 매개 변수 파일이 있습니다. 배포 시 테스트 매개 변수 파일을 프로덕션 매개 변수 파일로 바꿀 수 있습니다.

* **connections.json** 파일의 여러 다른 부분을 매개 변수화합니다. 그런 다음, **connections.json** 파일을 소스 제어에 체크 인하고 **parameters.json** 파일을 통해 모든 연결을 관리할 수 있습니다.

* `authentication` JSON 개체와 같은 복합 개체를 매개 변수화합니다. 예를 들어 `authentication` 개체 값을 `@parameters('api-auth')`와 같은 단일 매개 변수 식을 보유하는 문자열로 바꿀 수 있습니다.

* 프로젝트의 **local.settings.json** 파일에서 앱 설정을 검토하고 편집합니다. 그런 다음, 매개 변수에서 이러한 앱 설정을 참조할 수 있습니다.

> [!NOTE]
> 일반적으로 값을 매개 변수화하는 기본 방법으로 앱 설정이 아닌 매개 변수를 사용하는 것이 좋습니다. 그러면 보안 키 또는 문자열을 저장해야 할 때 권장 사항에 따라 매개 변수의 앱 설정을 참조할 수 있습니다. 원할 경우 매개 변수를 통해 앱 설정을 참조하여 솔루션에서 두 옵션을 모두 사용할 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

* Azure 계정 및 구독 구독이 없는 경우 [Azure 체험 계정에 등록](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)합니다.

* [단일 테넌트 Azure Logic Apps에 호스트되는 논리 앱 워크플로](single-tenant-overview-compare.md).

  논리 앱이 없는 경우 Azure Portal 또는 [Visual Studio Code](create-single-tenant-workflows-visual-studio-code.md)에서 [논리 앱(표준)을 만듭니다](create-single-tenant-workflows-azure-portal.md).

## <a name="define-use-and-edit-parameters"></a>매개 변수 정의, 사용 및 편집

### <a name="azure-portal"></a>Azure portal

1. [Azure Portal](https://portal.azure.com)에서 단일 테넌트 기반 논리 앱을 엽니다. **워크플로** 아래에서 디자이너의 워크플로를 선택하고 엽니다.

1. 디자이너 도구 모음에서 **매개 변수** 를 선택합니다.

   ![디자이너 도구 모음에서 Azure Portal, 워크플로 디자이너 및 "매개 변수"가 선택된 것을 보여 주는 스크린샷](./media/parameterize-workflow-app/portal-designer-select-parameters.png)

1. **매개 변수** 창에서 **매개 변수 만들기** 를 선택합니다.

1. 만들 매개 변수에 대한 다음 정보를 제공합니다.

   | 속성 | 필수 | Description |
   |----------|----------|-------------|
   | **이름** | 예 | 만들 매개 변수의 이름입니다. |
   | **형식** | 예 | **Array**, **Bool**, **Float**, **Int**, **Object** 및 **String** 과 같은 매개 변수의 데이터 형식입니다. <p><p>**참고**: 단일 테넌트 기반 워크플로에서는 `securestring` 및 `secureobject`와 같은 보안 데이터 형식이 지원되지 않습니다. |
   | **값** | 예 | 매개 변수의 값입니다. <p><p>단일 테넌트 Azure Logic Apps에서 워크플로 논리, 연결 정보 및 매개 변수 값이 단일 위치에 존재하지 않으므로 매개 변수 값을 지정해야 합니다. 디자이너는 로드하기 전에 매개 변수 값을 확인할 수 있어야 합니다. |
   |||

   다음 예제에서는 문자열 매개 변수에 대한 정의를 보여 줍니다.

   ![예제 매개 변수 정의가 있는 Azure Portal, 워크플로 디자이너 및 "매개 변수" 창을 보여 주는 스크린샷](./media/parameterize-workflow-app/define-parameter.png)

1. 완료되면 **매개 변수** 창을 닫되, 워크플로를 저장하여 새 매개 변수 정의를 저장해야 합니다.

1. 동일한 논리 앱 내의 워크플로에 있는 트리거 또는 동작에서 매개 변수를 참조하려면 다음 단계를 수행합니다.

   1. 디자이너에서 원하는 워크플로를 열고 트리거 또는 동작을 확장합니다.

   1. 매개 변수를 사용하려는 속성에서 해당 속성의 편집 상자 내부를 클릭합니다.

   1. 열리는 동적 콘텐츠 목록의 **매개 변수** 에서 이전에 만든 매개 변수를 선택합니다. 예를 들면 다음과 같습니다.

      ![속성 편집 상자에 커서가 있고, 확장된 동적 콘텐츠 목록 및 이전에 만든 매개 변수가 선택된 예제 동작을 보여 주는 스크린샷](./media/parameterize-workflow-app/reference-parameter.png)

1. 동일한 논리 앱에서 매개 변수를 보거나 편집하려면 다음 단계를 수행합니다.

   * 해당 논리 앱에서 워크플로를 엽니다. 워크플로 메뉴에서 **디자이너** 를 선택합니다. 디자이너 도구 모음에서 **매개 변수** 를 선택합니다.

     **매개 변수** 창이 열리고 해당 논리 앱의 워크플로에서 정의한 모든 매개 변수가 표시됩니다.

   * 대량 JSON을 보거나 편집하려면 논리 앱의 주 메뉴에서 **매개 변수** 를 선택합니다.

     **매개 변수** JSON 보기가 열리고 해당 논리 앱의 워크플로에서 정의한 모든 매개 변수가 표시됩니다.

### <a name="visual-studio-code"></a>Visual Studio Code

1. **parameters.json** 이라는 프로젝트 루트 수준 JSON 파일에서 모든 매개 변수와 해당 값을 정의합니다. 이 파일에는 *키-값* 쌍을 포함하는 개체가 있습니다. 각 키는 각 매개 변수의 이름이고, 각 값은 각 매개 변수의 구조입니다. 각 구조에는 `type` 및 `value` 선언이 모두 포함되어야 합니다.

   > [!IMPORTANT]
   > **parameters.json** 파일은 프로젝트의 다른 위치(예: 워크플로 정의 또는 연결)에서 참조하거나 사용하는 모든 매개 변수와 해당 값을 정의하고 포함해야 합니다.

   다음 예제에서는 샘플 매개 변수 파일을 보여줍니다.

   ```json
   {
        "responseString": { 
            "type": "string", 
            "value": "hello" 
        },
        "functionAuth": { 
            "type": "object", 
            "value": { 
                "type": "QueryString", 
                "name": "Code", 
                "value": "@appsetting('<AzureFunctionsOperation-FunctionAppKey>')" 
            }
        }
    }
    ```

    > [!NOTE]
    > **parameters.json** 파일에서 `@appsetting`은 유일하게 유효한 식 형식입니다.

1. 트리거 또는 작업 입력에서 매개 변수를 참조하려면 `@parameters('<parameter-name>')` 식을 사용합니다.

#### <a name="parameterize-connections-file"></a>연결 파일 매개 변수화

**connections.json** 파일을 매개 변수화하려면 `ConnectionRuntimeUrl` 같은 리터럴 값을 단일 `parameters()` 식(예: `@parameters('api-runtimeUrl')`)으로 바꿉니다. **connections.json** 파일에서 유일하게 유효한 식 형식은 `@parameters` 및 `@appsetting`입니다.

> [!IMPORTANT]
>
> 개발 동안 **connections.json** 파일을 매개 변수화하면 디자이너 환경이 로컬과 Azure Portal 모두에서 제한됩니다. 개발에 디자이너를 사용해야 하는 경우 매개 변수화되지 않은 **connections.json** 파일을 대신 사용합니다. 그런 다음, 배포 파이프라인에서 매개 변수화된 파일로 바꿉니다. 
> 런타임은 여전히 매개 변수화와 함께 작동합니다. 디자이너 개선 사항은 개발 중입니다.

다음 예제에서는 앱 설정과 매개 변수를 모두 사용하는 매개 변수화된 **connections.json** 파일을 보여 줍니다. 가능한 경우 매개 변수를 사용하려고 하겠지만, 이 시나리오는 앱 설정이 배포 중에 생성되고 개발 파이프라인에서 동적으로 더 쉽게 채워지므로 매개 변수보다는 앱 설정을 사용하게 되는 예외 또는 에지 사례입니다. 이 샘플 파일은 복잡한 `blob_auth` 인증 개체에 대해 매개 변수를 사용하고 다른 값에 대해 앱 설정을 사용합니다. 이 경우 워크플로에서 매개 변수를 참조할 가능성이 낮기 때문에 인증 개체에 매개 변수를 사용할 수 있습니다.

```json
{
   "serviceProviderConnections": {
      "serviceBus": {
         "parameterValues": {
            "connectionString": "@appsetting('serviceBus_connectionString')"
        },
        "serviceProvider": {
           "id": "/serviceProviders/serviceBus"
        },
        "displayName": "servicebus"
     }
   },
   "managedApiConnections": {
      "azureblob": {
         "api": {
            "id": "/subscriptions/@{appsetting('WORKFLOWS_SUBSCRIPTION_ID')}/providers/Microsoft.Web/locations/@{appsetting('WORKFLOWS_LOCATION_NAME')}/managedApis/azureblob"
         },
         "connection": {
            "id": "/subscriptions/@{appsetting('WORKFLOWS_SUBSCRIPTION_ID')}/resourceGroups/@{appsetting('WORKFLOWS_RESOURCE_GROUP_NAME')}/providers/Microsoft.Web/connections/azureblob"
         },
         "connectionRuntimeUrl": "@appsetting('BLOB_CONNECTION_RUNTIMEURL')",
         "authentication": "@parameters('blob_auth')"
      }
   }
}
```

> [!NOTE]
> 일반 텍스트가 인라인인 식이 있는 경우 해당 식을 중괄호()로 묶어 해당 식에 보간된 형식을 사용해야 {} 합니다. 이 형식은 구문 분석 문제를 방지하는 데 도움이 됩니다.
>
> 예를 들어 가 있는 경우 `"<text>/@<function-name>('<parameter-name>')/<text>"` 대신 다음 버전을 `"<text>/@{<function-name>('<parameter-name>')}/<text>"` 사용합니다. 
>
> 자세한 내용은 [함수 사용에 대한 고려 사항을 검토하세요.](workflow-definition-language-functions-reference.md#function-considerations)
     

## <a name="manage-parameters-files"></a>매개 변수 파일 관리

일반적으로 매개 변수 파일의 여러 버전을 관리해야 합니다. 개발, 테스트, 프로덕션 등의 여러 배포 환경을 대상으로 하는 값이 있을 수 있습니다. 이러한 매개 변수 파일을 관리하는 방법은 종종 ARM 템플릿 매개 변수 파일을 관리하는 방법과 비슷합니다. 특정 환경에 배포하는 경우 일반적으로 DevOps에 대한 파이프라인을 통해 해당 매개 변수 파일을 승격합니다.

Azure CLI를 사용하여 매개 변수 파일을 동적으로 바꾸려면 다음 명령을 실행합니다.

```azurecli
az functionapp deploy --resource-group MyResourceGroup --name MyLogicApp --src-path C:\parameters.json --type static --target-path parameters.json
```

NuGet 기반 논리 앱 프로젝트가 있는 경우 빌드 출력에 매개 변수 파일을 포함하도록 프로젝트 파일( **&lt; logic-app-name&gt;.csproj**)을 업데이트해야 합니다. 예를 들면 다음과 같습니다.

```csproj
<ItemGroup>
  <None Update="parameters.json">
    <CopyToOutputDirectory>PreserveNewest</CopyToOutputDirectory>
  </None>
</ItemGroup>
```

> [!NOTE]
> 현재 매개 변수 파일을 동적으로 바꾸는 기능은 Azure Portal 또는 워크플로 디자이너에서 아직 사용할 수 없습니다.

DevOps 배포를 위한 논리 앱 설정에 대한 자세한 내용은 다음 설명서를 검토하세요.

* [단일 테넌트 기반 논리 앱에 대한 DevOps 배포 개요](devops-deployment-single-tenant-azure-logic-apps.md)
* [단일 테넌트 기반 논리 앱에 대한 DevOps 배포 설정](set-up-devops-deployment-single-tenant-azure-logic-apps.md)

## <a name="manage-app-settings"></a>모바일 앱 설정

단일 테넌트 Azure Logic Apps의 앱 설정에는 동일한 논리 앱의 *모든 워크플로* 에 대한 전역 구성 옵션이 포함됩니다. Visual Studio Code에서 로컬로 워크플로를 실행하는 경우 이러한 앱 설정은 파일의 **local.settings.json** 파일에서 로컬 환경 변수로 액세스할 수 있습니다. 그런 다음, 매개 변수에서 이러한 앱 설정을 참조할 수 있습니다.

앱 설정을 추가, 업데이트 또는 삭제하려면 Visual Studio Code, Azure Portal, Azure CLI 또는 ARM(Bicep) 템플릿에 대한 다음 섹션을 선택하고 검토합니다.

### <a name="azure-portal"></a>[Azure Portal](#tab/azure-portal)

Azure Portal에서 논리 앱의 앱 설정을 검토하려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com/)에서 단일 테넌트 기반 논리 앱을 엽니다.

1. 논리 앱 메뉴의 **설정** 에서 **구성** 을 선택합니다.

1. **구성** 페이지의 **애플리케이션 설정** 탭에서 논리 앱의 앱 설정을 검토합니다.

1. 모든 값을 보려면 **값 표시** 를 선택합니다. 또는 단일 값을 보려면 해당 값을 선택합니다.

새 설정을 추가하려면 다음 단계를 수행합니다.

1. **애플리케이션 설정** 탭의 **애플리케이션 설정** 에서 **새 애플리케이션 설정** 을 선택합니다.

1. **이름** 에 새 설정의 *키* 또는 이름을 입력합니다.

1. **값** 에 새 설정의 값을 입력합니다.

1. 새 *키-값* 쌍을 만들 준비가 되면 **확인** 을 선택합니다.

:::image type="content" source="./media/parameterize-workflow-app/portal-app-settings-values.png" alt-text="단일 테넌트 기반 논리 앱의 앱 설정 및 값이 표시된 Azure Portal 구성 창을 보여주는 스크린샷" lightbox="./media/parameterize-workflow-app/portal-app-settings-values.png":::

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI를 사용하여 현재 앱 설정을 검토하려면 `az logicapp config appsettings list` 명령을 실행합니다. 명령에 `--name -n` 및 `--resource-group -g` 매개 변수가 포함되어 있는지 확인합니다. 예를 들면 다음과 같습니다.

```azurecli
az logicapp config appsettings list --name MyLogicApp --resource-group MyResourceGroup
```

Azure CLI를 사용하여 앱 설정을 추가하거나 업데이트하려면 `az logicapp config appsettings set` 명령을 실행합니다. 명령에 `--name n` 및 `--resource-group -g` 매개 변수가 포함되어 있는지 확인합니다. 예를 들어 다음 명령은 값이 `12345`인 `CUSTOM_LOGIC_APP_SETTING`이라는 키로 설정을 만듭니다.

```azurecli
az logicapp config appsettings set --name MyLogicApp --resource-group MyResourceGroup --settings CUSTOM_LOGIC_APP_SETTING=12345 
```

### <a name="resource-manager-or-bicep-template"></a>[Resource Manager 또는 Bicep 템플릿](#tab/azure-resource-manager)

ARM 템플릿 또는 Bicep 템플릿에서 앱 설정을 검토하고 정의하려면 논리 앱의 리소스 정의를 찾고 `appSettings` JSON 개체를 업데이트합니다. 전체 리소스 정의는 [ARM 템플릿 참조](/azure/templates/microsoft.web/sites)를 확인하세요.

이 예제에서는 ARM 템플릿 또는 Bicep 템플릿에 대한 파일 설정을 보여줍니다.

```json
"appSettings": [
    {
        "name": "string",
        "value": "string"
    },
    {
        "name": "string",
        "value": "string"
    },
    <...>
], 
```

---

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Azure Logic Apps의 단일 테넌트 및 다중 테넌트와 통합 서비스 환경](single-tenant-overview-compare.md)
