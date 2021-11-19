---
title: ARM 템플릿(Azure Resource Manager 템플릿)을 사용하여 Azure App Configuration 저장소 만들기
titleSuffix: Azure App Configuration
description: ARM 템플릿(Azure Resource Manager 템플릿)을 사용하여 Azure App Configuration 저장소를 만드는 방법을 알아봅니다.
author: GrantMeStrength
ms.author: jken
ms.date: 06/09/2021
ms.service: azure-app-configuration
ms.topic: quickstart
ms.custom: subject-armqs, devx-track-azurepowershell
ms.openlocfilehash: beb16b541f764cde806d94bcbf2fe8649fcbae88
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2021
ms.locfileid: "132719678"
---
# <a name="quickstart-create-an-azure-app-configuration-store-by-using-an-arm-template"></a>빠른 시작: ARM 템플릿을 사용하여 Azure App Configuration 저장소 만들기

이 빠른 시작에서는 다음을 수행하는 방법을 설명합니다.

- ARM 템플릿(Azure Resource Manager 템플릿)을 사용하여 App Configuration 저장소를 배포합니다.
- ARM 템플릿을 사용하여 App Configuration 저장소에서 키-값을 만듭니다.
- ARM 템플릿에서 App Configuration 저장소의 키-값을 읽습니다.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

환경이 필수 구성 요소를 충족하고 ARM 템플릿 사용에 익숙한 경우 **Azure에 배포** 단추를 선택합니다. 그러면 Azure Portal에서 템플릿이 열립니다.

[![Azure에 배포](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fquickstarts%2Fmicrosoft.appconfiguration%2Fapp-configuration-store-kv%2Fazuredeploy.json)

## <a name="prerequisites"></a>필수 구성 요소

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="authorization"></a>권한 부여

ARM 템플릿 내의 키-값 데이터에 액세스하려면 기여자 또는 소유자와 같은 Azure Resource Manager 역할이 필요합니다. Azure App Configuration [데이터 평면 역할](concept-enable-rbac.md) 중 하나를 통한 액세스는 현재 지원되지 않습니다.

> [!NOTE]
> 액세스 키 인증을 사용하지 않도록 설정하면 ARM 템플릿 내에서 키-값 데이터 액세스가 사용하지 않도록 설정됩니다. 자세한 내용은 [액세스 키 인증 사용 안 함](./howto-disable-access-key-authentication.md#limitations)을 참조하세요.

## <a name="review-the-template"></a>템플릿 검토

이 빠른 시작에서 사용되는 템플릿은 [Azure 빠른 시작 템플릿](https://azure.microsoft.com/resources/templates/app-configuration-store-kv/)에서 나온 것입니다. 두 개의 키-값이 내부에 있는 새 App Configuration 저장소를 만듭니다. 그런 다음, `reference` 함수를 사용하여 두 개의 키-값 리소스에 대한 값을 출력합니다. 키의 값을 이러한 방식으로 읽으면 템플릿의 다른 위치에서 키를 사용할 수 있습니다.

빠른 시작에서는 `copy` 요소를 사용하여 키-값 리소스의 여러 인스턴스를 만듭니다. `copy` 요소에 대한 자세한 내용은 [ARM 템플릿의 리소스 반복](../azure-resource-manager/templates/copy-resources.md)을 참조하세요.

> [!IMPORTANT]
> 이 템플릿에는 App Configuration 리소스 공급자 버전 `2020-07-01-preview` 이상이 필요합니다. 이 버전에서는 `reference` 함수를 사용하여 키-값을 읽습니다. 이전 버전에서 키-값을 읽는 데 사용된 `listKeyValue` 함수는 `2020-07-01-preview` 버전부터 사용할 수 없습니다.

:::code language="json" source="~/quickstart-templates/quickstarts/microsoft.appconfiguration/app-configuration-store-kv/azuredeploy.json":::

템플릿에는 두 개의 Azure 리소스가 정의되어 있습니다.

- [Microsoft.AppConfiguration/configurationStores](/azure/templates/microsoft.appconfiguration/2020-07-01-preview/configurationstores): App Configuration 저장소를 만듭니다.
- [Microsoft.AppConfiguration/configurationStores/keyValues](/azure/templates/microsoft.appconfiguration/2020-07-01-preview/configurationstores/keyvalues): 키-값을 App Configuration 저장소 내에 만듭니다.

> [!TIP]
> `keyValues` 리소스의 이름은 키와 레이블의 조합입니다. 키와 레이블은 `$` 구분 기호로 조인됩니다. 레이블은 선택 사항입니다. 위의 예제에서 이름이 `myKey`인 `keyValues` 리소스는 레이블이 없는 키-값을 만듭니다.
>
> URL 인코딩이라고도 하는 퍼센트 인코딩을 사용하면 ARM 템플릿 리소스 이름에 허용되지 않는 문자를 키 또는 레이블에 포함할 수 있습니다. `%`도 허용되는 문자가 아니므로 `~`가 대신 사용됩니다. 이름을 올바르게 인코딩하려면 다음 단계를 수행합니다.
>
> 1. URL 인코딩을 적용합니다.
> 2. `~`을 `~7E`로 바꿉니다.
> 3. `%`을 `~`로 바꿉니다.
>
> 예를 들어 키 이름이 `AppName:DbEndpoint`이고 레이블 이름이 `Test`인 키-값 쌍을 만들려면 리소스 이름이 `AppName~3ADbEndpoint$Test`여야 합니다.

> [!NOTE]
> 앱 구성을 사용하면 가상 네트워크에서 [프라이빗 링크](concept-private-endpoint.md)를 통해 키-값 데이터에 액세스할 수 있습니다. 기본적으로 이 기능을 사용하도록 설정하면 공용 네트워크를 통한 앱 구성 데이터에 대한 모든 요청이 거부됩니다. ARM 템플릿은 가상 네트워크 외부에서 실행되므로 ARM 템플릿에서의 데이터 액세스는 허용되지 않습니다. 프라이빗 링크를 사용할 때 ARM 템플릿에서 데이터 액세스를 허용하려면 다음 Azure CLI 명령을 사용하여 공용 네트워크 액세스를 사용하도록 설정하면 됩니다. 이 시나리오에서는 공용 네트워크 액세스를 활성화하는 보안 관련 사항을 고려해야 합니다.
>
> ```azurecli-interactive
> az appconfig update -g MyResourceGroup -n MyAppConfiguration --enable-public-network true
> ```

## <a name="deploy-the-template"></a>템플릿 배포

다음 이미지를 선택하고 Azure에 로그인하여 템플릿을 엽니다. 템플릿에서 두 개의 키-값이 내부에 있는 App Configuration 저장소를 만듭니다.

[![Azure에 배포](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fquickstarts%2Fmicrosoft.appconfiguration%2Fapp-configuration-store-kv%2Fazuredeploy.json)

다음 PowerShell cmdlet을 사용하여 템플릿을 배포할 수도 있습니다. 키-값은 PowerShell 콘솔의 출력에 있습니다.

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter a project name that is used for generating resource names"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/quickstarts/microsoft.appconfiguration/app-configuration-store-kv/azuredeploy.json"

$resourceGroupName = "${projectName}rg"

New-AzResourceGroup -Name $resourceGroupName -Location "$location"
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri

Read-Host -Prompt "Press [ENTER] to continue ..."
```

## <a name="review-deployed-resources"></a>배포된 리소스 검토

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. Azure Portal 검색 상자에서 **App Configuration** 을 입력합니다. 목록에서 **App Configuration** 을 선택합니다.
1. 새로 만든 App Configuration 리소스를 선택합니다.
1. **작업** 아래에서 **구성 탐색기** 를 클릭합니다.
1. 두 개의 키-값이 있는지 확인합니다.

## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요하지 않은 경우 리소스 그룹, App Configuration 저장소 및 모든 관련 리소스를 삭제합니다. 나중에 App Configuration 저장소를 사용하려는 경우 삭제를 건너뛸 수 있습니다. 이 저장소를 계속 사용하지 않으려면 다음과 같은 cmdlet을 실행하여 이 빠른 시작에서 만든 리소스를 모두 삭제합니다.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
Remove-AzResourceGroup -Name $resourceGroupName
Write-Host "Press [ENTER] to continue..."
```

## <a name="next-steps"></a>다음 단계

기능 플래그 및 Key Vault 참조를 App Configuration 저장소에 추가하는 방법에 대해 알아보려면 ARM 템플릿 예제를 참조하세요.

- [app-configuration-store-ff](https://azure.microsoft.com/resources/templates/app-configuration-store-ff/)
- [app-configuration-store-keyvaultref](https://azure.microsoft.com/resources/templates/app-configuration-store-keyvaultref/)
