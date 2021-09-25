---
title: Azure CLI 및 Bicep 파일을 | 리소스 배포 Microsoft Docs
description: Azure Resource Manager와 Azure CLI를 사용하여 Azure에 리소스를 배포합니다. 리소스는 Bicep 파일에 정의되어 있습니다.
author: mumian
ms.author: jgao
ms.topic: conceptual
ms.date: 09/17/2021
ms.custom: devx-track-azurecli, seo-azure-cli
ms.openlocfilehash: 0c474a7bf1d74b44b85f108b6a7fa28bcdc48902
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128625623"
---
# <a name="how-to-deploy-resources-with-bicep-and-azure-cli"></a>Bicep 및 Azure CLI 사용하여 리소스를 배포하는 방법

이 문서에서는 Bicep 파일과 함께 Azure CLI를 사용하여 Azure에 리소스를 배포하는 방법을 설명합니다. Azure 솔루션 배포 및 관리와 관련된 개념이 익숙하지 않은 경우 [Bicep 개요](./overview.md)를 참조하세요.

Bicep 파일을 배포하려면 [Azure CLI 버전 2.20.0 이상](/cli/azure/install-azure-cli)이 필요합니다.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

Azure CLI가 설치되어 있지 않으면 Azure Cloud Shell을 사용할 수 있습니다. 자세한 내용은 [Azure Cloud Shell에서 Bicep 파일 배포](./deploy-cloud-shell.md)를 참조하세요.

## <a name="deployment-scope"></a>배포 범위

리소스 그룹, 구독, 관리 그룹 또는 테넌트를 배포 대상으로 지정할 수 있습니다. 배포의 범위에 따라 다른 명령을 사용합니다.

* **리소스 그룹** 에 배포하려면 [az deployment group create](/cli/azure/deployment/group#az_deployment_group_create)를 사용합니다.

  ```azurecli-interactive
  az deployment group create --resource-group <resource-group-name> --template-file <path-to-bicep>
  ```

* **구독** 에 배포하려면 [az deployment sub create](/cli/azure/deployment/sub#az_deployment_sub_create)를 사용합니다.

  ```azurecli-interactive
  az deployment sub create --location <location> --template-file <path-to-bicep>
  ```

  구독 수준 배포에 대한 자세한 내용은 [구독 수준에서 리소스 그룹 및 리소스 만들기](deploy-to-subscription.md)를 참조하세요.

* **관리 그룹** 에 배포하려면 [az deployment mg create](/cli/azure/deployment/mg#az_deployment_mg_create)를 사용합니다.

  ```azurecli-interactive
  az deployment mg create --location <location> --template-file <path-to-bicep>
  ```

  관리 그룹 수준 배포에 대한 자세한 내용은 [관리 그룹 수준에서 리소스 만들기](deploy-to-management-group.md)를 참조하세요.

* **테넌트** 에 배포하려면 [az deployment tenant create](/cli/azure/deployment/tenant#az_deployment_tenant_create)를 사용합니다.

  ```azurecli-interactive
  az deployment tenant create --location <location> --template-file <path-to-bicep>
  ```

  테넌트 수준 배포에 대한 자세한 내용은 [테넌트 수준에서 리소스 만들기](deploy-to-tenant.md)를 참조하세요.

모든 범위에서 Bicep 파일을 배포하는 사용자는 리소스를 만드는 데 필요한 권한이 있어야 합니다.

## <a name="deploy-local-bicep-file"></a>로컬 Bicep 파일 배포

로컬 머신 또는 외부에 저장된 머신에서 Bicep 파일을 배포할 수 있습니다. 이 섹션에서는 로컬 Bicep 파일 배포에 대해 설명합니다.

존재하지 않는 리소스 그룹에 배포하는 경우 리소스 그룹을 만듭니다. 리소스 그룹의 이름은 영숫자, 마침표, 밑줄, 하이픈 및 괄호만 포함할 수 있습니다. 최대 90자까지 가능합니다. 이름은 마침표로 끝날 수 없습니다.

```azurecli-interactive
az group create --name ExampleGroup --location "Central US"
```

로컬 Bicep 파일을 배포하려면 배포 명령에 `--template-file` 매개 변수를 사용합니다. 다음 예제에서는 매개 변수 값을 설정하는 방법을 보여 줍니다.

```azurecli-interactive
az deployment group create \
  --name ExampleDeployment \
  --resource-group ExampleGroup \
  --template-file <path-to-bicep> \
  --parameters storageAccountType=Standard_GRS
```

배포가 완료될 때까지 몇 분 정도 걸릴 수 있습니다. 완료되면 결과가 포함된 메시지가 표시됩니다.

```output
"provisioningState": "Succeeded",
```

## <a name="deploy-remote-bicep-file"></a>원격 Bicep 파일 배포

현재 Azure CLI는 원격 Bicep 파일 배포를 지원하지 않습니다. [Bicep CLI](./install.md#vs-code-and-bicep-extension)를 사용하여 Bicep 파일을 JSON 템플릿으로 컴파일한 다음 JSON 파일을 원격 위치로 로드합니다.

## <a name="parameters"></a>매개 변수

매개 변수 값을 전달하려면 인라인 매개 변수 또는 매개 변수 파일을 사용할 수 있습니다.

### <a name="inline-parameters"></a>인라인 매개 변수입니다.

인라인 매개 변수를 전달하려면 `parameters`에 값을 제공합니다. 예를 들어 Bash 셸에서 문자열 및 배열을 Bicep 파일에 전달하려면 다음을 사용합니다.

```azurecli-interactive
az deployment group create \
  --resource-group testgroup \
  --template-file <path-to-bicep> \
  --parameters exampleString='inline string' exampleArray='("value1", "value2")'
```

Windows 명령 프롬프트(CMD) 또는 PowerShell을 사용하여 Azure CLI를 사용하는 경우 배열을 `exampleArray="['value1','value2']"` 형식으로 전달합니다.

파일의 콘텐츠를 가져와서 해당 콘텐츠를 인라인 매개 변수로 제공할 수도 있습니다.

```azurecli-interactive
az deployment group create \
  --resource-group testgroup \
  --template-file <path-to-bicep> \
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

예를 들어 태그를 설정하기 위해 개체를 전달하려면 JSON을 사용합니다. 예를 들어 Bicep 파일에는 다음과 같은 매개 변수가 포함될 수 있습니다.

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
--template-file $bicepFile \
--parameters resourceName=abcdef4556 resourceTags="$tags"
```

개체에 전달하려는 JSON 주위에 큰따옴표를 사용합니다.

변수를 사용하여 매개 변수 값을 포함할 수 있습니다. Bash에서 변수를 모든 매개 변수 값으로 설정하고 배포 명령에 추가합니다.

```azurecli-interactive
params="prefix=start suffix=end"

az deployment group create \
  --resource-group testgroup \
  --template-file <path-to-bicep> \
  --parameters $params
```

그러나 Windows 명령 프롬프트(CMD) 또는 PowerShell에서 Azure CLI를 사용하는 경우에는 변수를 JSON 문자열로 설정합니다. 따옴표를 이스케이프합니다(예: `$params = '{ \"prefix\": {\"value\":\"start\"}, \"suffix\": {\"value\":\"end\"} }'`).

### <a name="parameter-files"></a>매개 변수 파일

매개 변수를 스크립트에 인라인 값으로 전달하는 것보다는, 매개 변수 값이 포함된 JSON 파일을 사용하는 것이 더 쉬울 수 있습니다. 매개 변수 파일은 로컬 파일이어야 합니다. 외부 매개 변수 파일은 Azure CLI에서 사용할 수 없습니다. Bicep 파일은 JSON 매개 변수 파일을 사용합니다.

매개 변수 파일에 대한 자세한 내용은 [Resource Manager 매개 변수 파일 만들기](./parameter-files.md)를 참조하세요.

로컬 매개 변수 파일을 전달하려면 `@`을 사용하여 _storage.parameters.json_ 이라는 로컬 파일을 지정합니다.

```azurecli-interactive
az deployment group create \
  --name ExampleDeployment \
  --resource-group ExampleGroup \
  --template-file storage.bicep \
  --parameters @storage.parameters.json
```

## <a name="preview-changes"></a>변경 내용 미리 보기

Bicep 파일을 배포하기 전에 Bicep 파일이 환경에 적용할 변경 사항을 미리 볼 수 있습니다. [가상 작업](./deploy-what-if.md)을 사용하여 Bicep 파일이 예상대로 변경 사항을 적용하는지 확인합니다. 가상 작업은 Bicep 파일의 오류도 확인합니다.

## <a name="deploy-template-specs"></a>템플릿 사양 배포

현재 Azure CLI은 Bicep 파일을 제공하여 템플릿 사양을 만드는 것을 지원하지 않습니다. 그러나 [Microsoft.Resources/templateSpecs](/azure/templates/microsoft.resources/templatespecs) 리소스를 사용하여 Bicep 파일을 만들어 템플릿 사양을 배포할 수 있습니다. [템플릿 사양 만들기 샘플은](https://github.com/Azure/azure-docs-bicep-samples/blob/main/samples/create-template-spec/azuredeploy.bicep) Bicep 파일에서 템플릿 사양을 만드는 방법을 보여줍니다. Bicep CLI를 사용하여 ARM 템플릿 JSON에 Bicep 파일을 빌드한 다음, JSON 템플릿을 사용하여 템플릿 사양을 만들 수도 있습니다.

## <a name="deployment-name"></a>배포 이름

Bicep 파일을 배포할 때 배포에 이름을 지정할 수 있습니다. 해당 이름은 배포 기록에서 배포를 검색하는 데 도움이 될 수 있습니다. 배포에 이름을 제공하지 않으면 Bicep 파일의 이름이 사용됩니다. 예를 들어 `azuredeploy.bicep`이라는 Bicep 파일을 배포하고 배포 이름을 지정하지 않으면 배포 이름은 `azuredeploy`로 지정됩니다.

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

## <a name="next-steps"></a>다음 단계

* 오류 발생 시 성공적인 배포로 롤백하려면 [오류 발생 시 성공적인 배포로 롤백](../templates/rollback-on-error.md)을 참조하세요.
- 파일에서 매개 변수를 정의하는 방식을 이해하려면 [Bicep 파일의 구조 및 구문 이해](file.md)를 참조하세요.
* 일반적인 배포 오류를 해결하는 방법은 [Azure Resource Manager를 사용한 일반적인 Azure 배포 오류 해결](../templates/common-deployment-errors.md)을 참조하세요.
