---
title: Azure CLI Azure 배포 템플릿 – Azure Resource Manager | Microsoft Docs
description: Azure Resource Manager 및 Azure CLI 사용하여 리소스 그룹을 만들고 Azure에 배포합니다. 리소스는 Azure 배포 템플릿에 정의됩니다.
ms.topic: conceptual
ms.date: 09/17/2021
ms.custom: devx-track-azurecli, seo-azure-cli
keywords: azure cli는 arm 템플릿 배포, 리소스 그룹 azure 만들기, Azure 배포 템플릿, 배포 리소스, arm 템플릿, azure arm 템플릿
ms.openlocfilehash: 5b7734e3b91f7e842f17888f3f3c67d05655fc97
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128585017"
---
# <a name="how-to-use-azure-resource-manager-arm-deployment-templates-with-azure-cli"></a>Azure CLI ARM(Azure Resource Manager) 배포 템플릿을 사용하는 방법

이 문서는 ARM 템플릿(Azure Resource Manager 템플릿)으로 Azure CLI를 사용하여 Azure에 리소스를 배포하는 방법을 설명합니다.  Azure 솔루션 배포 및 관리 개념에 익숙하지 않은 경우 [템플릿 배포 개요](overview.md)를 참조하세요.

배포 명령이 Azure CLI 버전 2.2.0에서 변경되었습니다. 이 문서의 예제에는 [Azure CLI 버전 2.20.0 이상](/cli/azure/install-azure-cli)이 필요합니다.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

Azure CLI가 설치되어 있지 않으면 Azure Cloud Shell을 사용할 수 있습니다. 자세한 내용은 [Azure Cloud Shell에서 ARM 템플릿 배포](deploy-cloud-shell.md)를 참조하세요.

## <a name="deployment-scope"></a>배포 범위

Azure 배포 템플릿을 리소스 그룹, 구독, 관리 그룹 또는 테넌트 대상으로 지정할 수 있습니다. 배포의 범위에 따라 다른 명령을 사용합니다.

* **리소스 그룹** 에 배포하려면 [az deployment group create](/cli/azure/deployment/group#az_deployment_group_create)를 사용합니다.

  ```azurecli-interactive
  az deployment group create --resource-group <resource-group-name> --template-file <path-to-template>
  ```

* **구독** 에 배포하려면 [az deployment sub create](/cli/azure/deployment/sub#az_deployment_sub_create)를 사용합니다.

  ```azurecli-interactive
  az deployment sub create --location <location> --template-file <path-to-template>
  ```

  구독 수준 배포에 대한 자세한 내용은 [구독 수준에서 리소스 그룹 및 리소스 만들기](deploy-to-subscription.md)를 참조하세요.

* **관리 그룹** 에 배포하려면 [az deployment mg create](/cli/azure/deployment/mg#az_deployment_mg_create)를 사용합니다.

  ```azurecli-interactive
  az deployment mg create --location <location> --template-file <path-to-template>
  ```

  관리 그룹 수준 배포에 대한 자세한 내용은 [관리 그룹 수준에서 리소스 만들기](deploy-to-management-group.md)를 참조하세요.

* **테넌트** 에 배포하려면 [az deployment tenant create](/cli/azure/deployment/tenant#az_deployment_tenant_create)를 사용합니다.

  ```azurecli-interactive
  az deployment tenant create --location <location> --template-file <path-to-template>
  ```

  테넌트 수준 배포에 대한 자세한 내용은 [테넌트 수준에서 리소스 만들기](deploy-to-tenant.md)를 참조하세요.

모든 범위에서 템플릿을 배포하는 사용자는 리소스를 만드는 데 필요한 권한이 있어야 합니다.

## <a name="deploy-local-template"></a>로컬 템플릿 배포

로컬 컴퓨터 또는 외부에 저장된 컴퓨터에서 ARM 템플릿을 배포할 수 있습니다. 이 섹션에서는 로컬 템플릿 배포에 대해 설명합니다.

존재하지 않는 리소스 그룹에 배포하는 경우 리소스 그룹을 만듭니다. 리소스 그룹의 이름은 영숫자, 마침표, 밑줄, 하이픈 및 괄호만 포함할 수 있습니다. 최대 90자까지 가능합니다. 이름은 마침표로 끝날 수 없습니다.

```azurecli-interactive
az group create --name ExampleGroup --location "Central US"
```

로컬 템플릿을 배포하려면 배포 명령에 `--template-file` 매개 변수를 사용합니다. 다음 예제에서는 매개 변수 값을 설정하는 방법을 보여 줍니다.

```azurecli-interactive
az deployment group create \
  --name ExampleDeployment \
  --resource-group ExampleGroup \
  --template-file <path-to-template> \
  --parameters storageAccountType=Standard_GRS
```

Azure 배포 템플릿을 완료하는 데 몇 분 정도 걸릴 수 있습니다. 완료되면 결과가 포함된 메시지가 표시됩니다.

```output
"provisioningState": "Succeeded",
```

## <a name="deploy-remote-template"></a>원격 템플릿 배포

로컬 컴퓨터에 ARM 템플릿을 저장하는 대신, 외부 위치에 저장할 수 있습니다. 원본 제어 리포지토리(예: GitHub)에 템플릿을 저장할 수 있습니다. 또는 조직에서 공유 액세스에 대한 Azure Storage 계정에 저장할 수 있습니다.

[!INCLUDE [Deploy templates in private GitHub repo](../../../includes/resource-manager-private-github-repo-templates.md)]

존재하지 않는 리소스 그룹에 배포하는 경우 리소스 그룹을 만듭니다. 리소스 그룹의 이름은 영숫자, 마침표, 밑줄, 하이픈 및 괄호만 포함할 수 있습니다. 최대 90자까지 가능합니다. 이름은 마침표로 끝날 수 없습니다.

```azurecli-interactive
az group create --name ExampleGroup --location "Central US"
```

외부 템플릿을 배포하려면 `template-uri` 매개 변수를 사용합니다.

```azurecli-interactive
az deployment group create \
  --name ExampleDeployment \
  --resource-group ExampleGroup \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/quickstarts/microsoft.storage/storage-account-create/azuredeploy.json" \
  --parameters storageAccountType=Standard_GRS
```

앞의 예제에서는 템플릿에 중요한 데이터가 포함되어 있지 않으므로 대부분의 시나리오에 적합한 이 템플릿에 대해 공개적으로 액세스할 수 있는 URI가 필요합니다. 중요한 데이터(예: 관리자 암호)를 지정해야 하는 경우 해당 값을 안전한 매개 변수로 전달합니다. 그러나 템플릿에 대한 액세스를 관리하려는 경우 [템플릿 사양](#deploy-template-spec)을 사용하는 것이 좋습니다.

스토리지 계정에 저장된 상대 경로를 사용하여 원격 연결된 템플릿을 배포하려면 `query-string`을 사용하여 SAS 토큰을 지정합니다.

```azurecli-interactive
az deployment group create \
  --name linkedTemplateWithRelativePath \
  --resource-group myResourceGroup \
  --template-uri "https://stage20210126.blob.core.windows.net/template-staging/mainTemplate.json" \
  --query-string $sasToken
```

자세한 내용은 [연결된 템플릿에 상대 경로 사용](./linked-templates.md#linked-template)을 참조하세요.

## <a name="azure-deployment-template-name"></a>Azure 배포 템플릿 이름

ARM 템플릿을 배포할 때 Azure 배포 템플릿에 이름을 지정할 수 있습니다. 해당 이름은 배포 기록에서 배포를 검색하는 데 도움이 될 수 있습니다. 배포에 이름을 제공하지 않으면 템플릿 파일의 이름이 사용됩니다. 예를 들어 _azuredeploy.json_ 이라는 템플릿을 배포하고 배포 이름을 지정하지 않으면 배포 이름은 `azuredeploy`가 됩니다.

배포를 실행할 때마다 리소스 그룹의 배포 기록에 항목이 배포 이름과 함께 추가됩니다. 다른 배포를 실행하고 동일한 이름을 지정하는 경우 이전 항목이 현재 배포로 바뀝니다. 배포 기록에서 고유한 항목을 유지하려면 각 배포에 고유한 이름을 지정합니다.

고유한 이름을 만들려면 임의의 번호를 할당하면 됩니다.

```azurecli-interactive
deploymentName='ExampleDeployment'$RANDOM
```

또는 날짜 값을 추가할 수 있습니다.

```azurecli-interactive
deploymentName='ExampleDeployment'$(date +"%d-%b-%Y")
```

같은 배포 이름의 동일한 리소스 그룹에 동시 배포를 실행하는 경우 마지막 배포만 완료됩니다. 완료되지 않은 동일한 이름의 배포는 마지막 배포로 대체됩니다. 예를 들어 `storage1`이라는 스토리지 계정을 배포하는 `newStorage`라는 배포를 실행하는 동시에 `storage2`라는 스토리지 계정을 배포하는 `newStorage`라는 다른 배포를 실행하는 경우 스토리지 계정을 하나만 배포합니다. 결과 스토리지 계정의 이름은 `storage2`입니다.

그러나 `storage1`이라는 스토리지 계정을 배포하는 `newStorage`라는 배포를 실행하고 배포가 완료된 직후에 `storage2`라는 스토리지 계정을 배포하는 `newStorage`라는 다른 배포를 실행하면 두 개의 스토리지 계정이 배포됩니다. 하나는 `storage1`이고 다른 하나는 `storage2`입니다. 그러나 배포 기록에는 하나의 항목만 기록됩니다.

각 배포에 고유한 이름을 지정하는 경우 충돌 없이 동시에 실행할 수 있습니다. `storage1`이라는 스토리지 계정을 배포하는 `newStorage1`이라는 배포를 실행하는 동시에 `storage2`라는 스토리지 계정을 배포하는 `newStorage2`라는 다른 배포를 실행하면 두 개의 스토리지 계정이 배포되고 배포 기록에 두 개의 항목이 기록됩니다.

동시 배포와의 충돌을 방지하고 배포 기록에서 고유한 항목이 기록되게 하려면 각 배포에 고유한 이름을 지정합니다.

## <a name="deploy-template-spec"></a>템플릿 사양 배포

로컬 또는 원격 템플릿을 배포하는 대신 [템플릿 사양](template-specs.md)을 만들 수 있습니다. 템플릿 사양은 ARM 템플릿을 포함하는 Azure 구독의 리소스입니다. 이를 통해 조직의 사용자와 안전하게 템플릿을 공유할 수 있습니다. Azure RBAC(역할 기반 액세스 제어)를 사용하여 템플릿 사양에 대한 액세스 권한을 부여합니다. 이 기능은 현재 미리 보기로 제공됩니다.

다음 예제에서는 템플릿 사양을 만들고 배포하는 방법을 보여 줍니다.

먼저 ARM 템플릿을 제공하여 템플릿 사양을 만듭니다.

```azurecli
az ts create \
  --name storageSpec \
  --version "1.0" \
  --resource-group templateSpecRG \
  --location "westus2" \
  --template-file "./mainTemplate.json"
```

그런 다음 템플릿 사양의 ID를 가져와서 배포합니다.

```azurecli
id = $(az ts show --name storageSpec --resource-group templateSpecRG --version "1.0" --query "id")

az deployment group create \
  --resource-group demoRG \
  --template-spec $id
```

자세한 내용은 [Azure Resource Manager 템플릿 사양](template-specs.md)을 참조하세요.

## <a name="preview-changes"></a>변경 내용 미리 보기

ARM 템플릿을 배포하기 전에 템플릿이 환경에 적용되는 변경 내용을 미리 볼 수 있습니다. [가상 작업](./deploy-what-if.md)을 사용하여 템플릿이 예상대로 변경사항을 적용하는지 확인합니다. 가상 작업은 템플릿의 오류도 확인합니다.

## <a name="parameters"></a>매개 변수

매개 변수 값을 전달하려면 인라인 매개 변수 또는 매개 변수 파일을 사용할 수 있습니다.

### <a name="inline-parameters"></a>인라인 매개 변수입니다.

인라인 매개 변수를 전달하려면 `parameters`에 값을 제공합니다. 예를 들어 Bash 셸에서 문자열 및 배열을 템플릿에 전달하려면 다음을 사용합니다.

```azurecli-interactive
az deployment group create \
  --resource-group testgroup \
  --template-file <path-to-template> \
  --parameters exampleString='inline string' exampleArray='("value1", "value2")'
```

Windows 명령 프롬프트(CMD) 또는 PowerShell을 사용하여 Azure CLI를 사용하는 경우 배열을 `exampleArray="['value1','value2']"` 형식으로 전달합니다.

파일의 콘텐츠를 가져와서 해당 콘텐츠를 인라인 매개 변수로 제공할 수도 있습니다.

```azurecli-interactive
az deployment group create \
  --resource-group testgroup \
  --template-file <path-to-template> \
  --parameters exampleString=@stringContent.txt exampleArray=@arrayContent.json
```

파일에서 매개 변수 값을 가져오면 구성 값을 제공해야 하는 경우에 유용합니다. 예를 들어, [Linux 가상 머신에 대한 Cloud-Init 값](../../virtual-machines/linux/using-cloud-init.md)을 제공할 수 있습니다.

_arrayContent.json_ 형식은 다음과 같습니다.

```json
[
    "value1",
    "value2"
]
```

예를 들어 태그를 설정하기 위해 개체를 전달하려면 JSON을 사용합니다. 예를 들어 템플릿에 다음과 같은 매개 변수가 포함될 수 있습니다.

```json
    "resourceTags": {
      "type": "object",
      "defaultValue": {
        "Cost Center": "IT Department"
      }
    }
```

이 경우 다음 Bash 스크립트와 같이 JSON 문자열을 전달하여 매개 변수를 설정할 수 있습니다.

```bash
tags='{"Owner":"Contoso","Cost Center":"2345-324"}'
az deployment group create --name addstorage  --resource-group myResourceGroup \
--template-file $templateFile \
--parameters resourceName=abcdef4556 resourceTags="$tags"
```

개체에 전달하려는 JSON 주위에 큰따옴표를 사용합니다.

변수를 사용하여 매개 변수 값을 포함할 수 있습니다. Bash에서 변수를 모든 매개 변수 값으로 설정하고 배포 명령에 추가합니다.

```azurecli-interactive
params="prefix=start suffix=end"

az deployment group create \
  --resource-group testgroup \
  --template-file <path-to-template> \
  --parameters $params
``` 

그러나 Windows 명령 프롬프트(CMD) 또는 PowerShell에서 Azure CLI를 사용하는 경우에는 변수를 JSON 문자열로 설정합니다. 따옴표를 이스케이프합니다(예: `$params = '{ \"prefix\": {\"value\":\"start\"}, \"suffix\": {\"value\":\"end\"} }'`).

### <a name="parameter-files"></a>매개 변수 파일

매개 변수를 스크립트에 인라인 값으로 전달하는 것보다는, 매개 변수 값이 포함된 JSON 파일을 사용하는 것이 더 쉬울 수 있습니다. 매개 변수 파일은 로컬 파일이어야 합니다. 외부 매개 변수 파일은 Azure CLI에서 사용할 수 없습니다.

매개 변수 파일에 대한 자세한 내용은 [Resource Manager 매개 변수 파일 만들기](parameter-files.md)를 참조하세요.

로컬 매개 변수 파일을 전달하려면 `@`을 사용하여 _storage.parameters.json_ 이라는 로컬 파일을 지정합니다.

```azurecli-interactive
az deployment group create \
  --name ExampleDeployment \
  --resource-group ExampleGroup \
  --template-file storage.json \
  --parameters '@storage.parameters.json'
```

## <a name="handle-extended-json-format"></a>확장 JSON 형식 처리

버전이 2.3.0 이하인 Azure CLI를 사용하여 다중 선 줄 문자열이나 주석이 있는 템플릿을 배포하려면 `--handle-extended-json-format` 스위치를 사용해야 합니다.  예를 들면 다음과 같습니다.

```json
{
  "type": "Microsoft.Compute/virtualMachines",
  "apiVersion": "2018-10-01",
  "name": "[variables('vmName')]", // to customize name, change it in variables
  "location": "[
    parameters('location')
    ]", //defaults to resource group location
  /*
    storage account and network interface
    must be deployed first
  */
  "dependsOn": [
    "[resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))]",
    "[resourceId('Microsoft.Network/networkInterfaces/', variables('nicName'))]"
  ],
```

## <a name="next-steps"></a>다음 단계

* 오류 발생 시 성공적인 배포로 롤백하려면 [오류 발생 시 성공적인 배포로 롤백](rollback-on-error.md)을 참조하세요.
* 리소스 그룹에 있지만 템플릿에 정의되지 않은 리소스를 처리하는 방법을 지정하려면 [Azure Resource Manager 배포 모드](deployment-modes.md)를 참조하세요.
* 템플릿에서 매개 변수를 정의하는 방법을 이해하려면 [ARM 템플릿의 구조 및 구문 이해](./syntax.md)를 참조하세요.
* 일반적인 배포 오류를 해결하는 방법은 [Azure Resource Manager를 사용한 일반적인 Azure 배포 오류 해결](common-deployment-errors.md)을 참조하세요.