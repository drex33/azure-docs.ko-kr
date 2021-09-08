---
title: 템플릿에서 배포 스크립트 사용 | Microsoft Docs
description: Azure Resource Manager 템플릿에서 배포 스크립트를 사용합니다.
services: azure-resource-manager
author: mumian
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 08/25/2021
ms.author: jgao
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: ece3693fa183ba31de569e7db632c3d294c10437
ms.sourcegitcommit: ef448159e4a9a95231b75a8203ca6734746cd861
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/30/2021
ms.locfileid: "123187184"
---
# <a name="use-deployment-scripts-in-arm-templates"></a>ARM 템플릿에서 배포 스크립트 사용

ARM 템플릿(Azure Resource 템플릿)에서 배포 스크립트를 사용하는 방법을 알아봅니다. `Microsoft.Resources/deploymentScripts`라는 새 리소스 종류를 사용하여 사용자는 템플릿 배포에서 스크립트를 실행하고 실행 결과를 검토할 수 있습니다. 이러한 스크립트는 다음과 같은 사용자 지정 단계를 수행하는 데 사용할 수 있습니다.

- 디렉터리에 사용자 추가
- 데이터 평면 작업 수행(예: BLOB 또는 시드 데이터베이스 복사)
- 라이선스 키 조회 및 유효성 검사
- 자체 서명된 인증서 만들기
- Azure AD에서 개체 만들기
- 사용자 지정 시스템에서 IP 주소 블록 조회

배포 스크립트의 이점은 다음과 같습니다.

- 쉽게 코딩하고, 사용하고, 디버그할 수 있습니다. 원하는 개발 환경에서 배포 스크립트를 개발할 수 있습니다. 스크립트는 템플릿 또는 외부 스크립트 파일에 포함될 수 있습니다.
- 스크립트 언어 및 플랫폼을 지정할 수 있습니다. 현재 Linux 환경에서 Azure PowerShell 및 Azure CLI 배포 스크립트가 지원됩니다.
- 스크립트에 명령줄 인수를 전달할 수 있습니다.
- 스크립트 출력을 지정하고 이를 배포에 다시 전달할 수 있습니다.

배포 스크립트 리소스는 Azure Container Instance를 사용할 수 있는 지역에서만 사용할 수 있습니다.  [Azure 지역의 Azure Container Instances에 대한 리소스 가용성](../../container-instances/container-instances-region-availability.md)을 참조하세요.

> [!IMPORTANT]
> 스크립트를 실행하고 문제를 해결하려면 스토리지 계정 및 컨테이너 인스턴스가 필요합니다. 기존 스토리지 계정을 지정할 수 있습니다. 그러지 않으면 컨테이너 인스턴스와 함께 스토리지 계정이 스크립트 서비스에 의해 자동으로 생성됩니다. 자동으로 생성된 두 리소스는 일반적으로 배포 스크립트 실행이 터미널 상태가 되면 스크립트 서비스에 의해 삭제됩니다. 리소스가 삭제될 때까지 해당 리소스에 대한 요금이 청구됩니다. 자세한 내용은 [배포 스크립트 리소스 정리](#clean-up-deployment-script-resources)를 참조하세요.

> [!NOTE]
> 이제 Azure 로그인에 대한 다시 시도 논리가 래퍼 스크립트에 기본적으로 제공됩니다. 배포 스크립트와 동일한 템플릿에서 권한을 부여하는 경우 배포 스크립트 서비스는 관리 ID 역할 할당이 복제될 때까지 10초 간격으로 10분 동안 로그인을 다시 시도합니다.

## <a name="configure-the-minimum-permissions"></a>최소 권한 구성

배포 스크립트 API 버전 2020-10-01 이상의 경우 배포 스크립트 실행과 관련된 두 가지 주체가 있습니다.

- **배포 주체**(템플릿을 배포하는 데 사용되는 주체): 이 주체는 배포 스크립트 리소스를 실행하는 데 필요한 기본 리소스(스토리지 계정 및 Azure 컨테이너 인스턴스)를 만드는 데 사용됩니다. 최소 권한 권한을 구성하려면 다음 속성이 있는 사용자 지정 역할을 배포 주체에 할당합니다.

    ```json
    {
      "roleName": "deployment-script-minimum-privilege-for-deployment-principal",
      "description": "Configure least privilege for the deployment principal in deployment script",
      "type": "customRole",
      "IsCustom": true,
      "permissions": [
        {
          "actions": [
            "Microsoft.Storage/storageAccounts/*",
            "Microsoft.ContainerInstance/containerGroups/*",
            "Microsoft.Resources/deployments/*",
            "Microsoft.Resources/deploymentScripts/*"
          ],
        }
      ],
      "assignableScopes": [
        "[subscription().id]"
      ]
    }
    ```

    Azure Storage 및 Azure Container Instance 리소스 공급자가 등록되지 않은 경우 `Microsoft.Storage/register/action` 및 `Microsoft.ContainerInstance/register/action`도 추가해야 합니다.

- **배포 스크립트 주체**: 이 주체는 배포 스크립트가 Azure에 인증하고 Azure CLI/PowerShell을 호출해야 하는 경우에만 필요합니다. 배포 스크립트 주체를 지정하는 방법에는 두 가지가 있습니다.

  - `identity` 속성에서 사용자 할당 관리 ID를 지정합니다([샘플 템플릿](#sample-templates) 참조). 지정된 경우 스크립트 서비스는 배포 스크립트를 호출하기 전에 `Connect-AzAccount -Identity`을 호출합니다. 관리 ID에는 스크립트에서 작업을 완료하는 데 필요한 액세스 권한이 있어야 합니다. 현재는 `identity` 속성에 대해 사용자 할당 관리 ID만 지원됩니다. 다른 ID로 로그인하려면 이 목록의 두 번째 메서드를 사용합니다.
  - 서비스 주체 자격 증명을 보안 환경 변수로 전달한 다음 배포 스크립트에서 [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) 또는 [az login](/cli/azure/reference-index?view=azure-cli-latest#az_login&preserve-view=true)을 호출할 수 있습니다.

  관리 ID가 사용되는 경우 배포 주체에는 관리 ID 리소스에 할당된 **관리 ID 운영자** 역할(기본 제공 역할)이 필요합니다.

## <a name="sample-templates"></a>샘플 템플릿

다음 JSON은 예제입니다. 자세한 내용은 최신 [템플릿 스키마](/azure/templates/microsoft.resources/deploymentscripts)를 참조하세요.

```json
{
  "type": "Microsoft.Resources/deploymentScripts",
  "apiVersion": "2020-10-01",
  "name": "runPowerShellInline",
  "location": "[resourceGroup().location]",
  "kind": "AzurePowerShell", // or "AzureCLI"
  "identity": {
    "type": "userAssigned",
    "userAssignedIdentities": {
      "/subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourceGroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myID": {}
    }
  },
  "properties": {
    "forceUpdateTag": "1",
    "containerSettings": {
      "containerGroupName": "mycustomaci"
    },
    "storageAccountSettings": {
      "storageAccountName": "myStorageAccount",
      "storageAccountKey": "myKey"
    },
    "azPowerShellVersion": "3.0",  // or "azCliVersion": "2.0.80",
    "arguments": "-name \\\"John Dole\\\"",
    "environmentVariables": [
      {
        "name": "UserName",
        "value": "jdole"
      },
      {
        "name": "Password",
        "secureValue": "jDolePassword"
      }
    ],
    "scriptContent": "
      param([string] $name)
      $output = 'Hello {0}. The username is {1}, the password is {2}.' -f $name,${Env:UserName},${Env:Password}
      Write-Output $output
      $DeploymentScriptOutputs = @{}
      $DeploymentScriptOutputs['text'] = $output
    ", // or "primaryScriptUri": "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-helloworld.ps1",
    "supportingScriptUris":[],
    "timeout": "PT30M",
    "cleanupPreference": "OnSuccess",
    "retentionInterval": "P1D"
  }
}
```

> [!NOTE]
> 이 예제는 데모용입니다. `scriptContent` 및 `primaryScriptUri` 속성은 템플릿에 함께 사용할 수 없습니다.

> [!NOTE]
> _scriptContent_ 는 여러 줄이 포함된 스크립트를 표시합니다.  Azure Portal 및 Azure DevOps 파이프라인은 여러 줄이 있는 배포 스크립트를 구문 분석할 수 없습니다. 세미콜론 또는 _\\r\\n_ 또는  _\\n_ 을 사용하여 PowerShell 명령을 연결하거나 외부 스크립트 파일과 함께 `primaryScriptUri` 속성을 사용할 수 있습니다. 다양한 자유 JSON 문자열 이스케이프/이스케이프 해제 도구를 사용할 수 있습니다. 예를 들어 [https://www.freeformatter.com/json-escape.html](https://www.freeformatter.com/json-escape.html) 와 같습니다.

속성 값 세부 정보:

- `identity`: 배포 스크립트 API 버전 2020-10-01 이상에서는 스크립트에서 Azure 관련 작업을 수행해야 하는 경우가 아니라면 사용자 할당 관리 ID가 선택 사항입니다.  API 버전 2019-10-01-preview의 경우 배포 스크립트 서비스가 스크립트를 실행하는 데 사용하므로 관리 ID가 필요합니다. ID 속성이 지정된 경우 스크립트 서비스는 사용자 스크립트를 호출하기 전에 `Connect-AzAccount -Identity`를 호출합니다. 현재 사용자가 할당한 관리 ID만 지원됩니다. 다른 ID로 로그인하려면 스크립트에서 [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount)를 호출하면 됩니다.
- `kind`: 스크립트 유형을 지정합니다. 현재는 Azure PowerShell 및 Azure CLI 스크립트가 지원됩니다. 값은 **AzurePowerShell** 및 **AzureCLI** 입니다.
- `forceUpdateTag`: 템플릿 배포 간에 이 값을 변경하면 배포 스크립트가 강제로 다시 실행됩니다. `newGuid()` 또는 `utcNow()` 함수를 사용하는 경우 두 함수는 모두 매개 변수의 기본값에서만 사용할 수 있습니다. 자세한 내용은 [스크립트를 두 번 이상 실행](#run-script-more-than-once)을 참조하세요.
- `containerSettings`: Azure Container Instance를 사용자 지정하려면 설정을 지정합니다. 배포 스크립트에는 새 Azure Container Instance가 필요합니다. 기존 Azure Container Instance는 지정할 수 없습니다. 그러나 `containerGroupName`을 사용하여 컨테이너 그룹 이름을 사용자 지정할 수 있습니다. 지정하지 않으면 그룹 이름이 자동으로 생성됩니다.
- `storageAccountSettings`: 기존 스토리지 계정을 사용하려면 설정을 지정합니다. `storageAccountName`을 지정하지 않으면 스토리지 계정이 자동으로 만들어집니다. [기존 스토리지 계정 사용](#use-existing-storage-account)을 참조하세요.
- `azPowerShellVersion`/`azCliVersion`: 사용할 모듈 버전을 지정합니다. [지원되는 Azure PowerShell 버전](https://mcr.microsoft.com/v2/azuredeploymentscripts-powershell/tags/list) 목록을 참조하세요. [지원되는 Azure CLI 버전](https://mcr.microsoft.com/v2/azure-cli/tags/list) 목록을 참조하세요.

  >[!IMPORTANT]
  > 배포 스크립트는 MCR(Microsoft Container Registry)에서 사용 가능한 CLI 이미지를 사용합니다. 배포 스크립트의 CLI 이미지를 인증하는 데 약 한 달이 걸립니다. 30일 이내에 릴리스된 CLI 버전은 사용하지 마세요. 이미지의 릴리스 날짜를 확인하려면 [Azure CLI 릴리스 정보](/cli/azure/release-notes-azure-cli)를 참조하세요. 지원되지 않는 버전을 사용하는 경우 오류 메시지에 지원되는 버전이 나열됩니다.

- `arguments`: 매개 변수의 값을 지정합니다. 값은 공백으로 구분됩니다.

  배포 스크립트는 [CommandLineToArgvW ](/windows/win32/api/shellapi/nf-shellapi-commandlinetoargvw) 시스템 호출을 불러와 인수를 문자열 배열로 분할합니다. 인수가 Azure Container Instance에 [명령 속성](/rest/api/container-instances/containergroups/createorupdate#containerexec)으로 전달되고 명령 속성이 문자열 배열이기 때문에 이 단계가 필요합니다.

  인수에 이스케이프된 문자가 포함된 경우 [JsonEscaper](https://www.jsonescaper.com/)를 사용하여 문자를 두 번 이스케이프 합니다. 원래 이스케이프된 문자열을 도구에 붙여넣은 다음, **이스케이프** 를 선택합니다.  이 도구는 이중 이스케이프된 문자열을 출력합니다. 예를 들어 이전 샘플 템플릿에서 인수는 `-name \"John Dole\"`입니다. 이스케이프된 문자열은 `-name \\\"John Dole\\\"`입니다.

  Object 형식의 ARM 템플릿 매개 변수를 인수로 전달하려면 [string()](./template-functions-string.md#string) 함수를 사용하여 개체를 문자열로 변환한 다음, [replace()](./template-functions-string.md#replace) 함수를 사용하여 `\"`를 `\\\"`로 바꿉니다. 예를 들면 다음과 같습니다.

  ```json
  replace(string(parameters('tables')), '\"', '\\\"')
  ```

  자세한 내용은 [샘플 템플릿](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-jsonEscape.json)을 참조하세요.

- `environmentVariables`: 스크립트에 전달할 환경 변수를 지정합니다. 자세한 내용은 [배포 스크립트 개발](#develop-deployment-scripts)을 참조하세요.
- `scriptContent`: 스크립트 콘텐츠를 지정합니다. 외부 스크립트를 실행하려면 `primaryScriptUri`를 대신 사용합니다. 예를 보려면 [인라인 스크립트 사용](#use-inline-scripts) 및 [외부 스크립트 사용](#use-external-scripts)을 참조하세요.
- `primaryScriptUri`: 지원되는 파일 확장명을 사용하여 기본 배포 스크립트에 공개적으로 액세스할 수 있는 URL을 지정합니다. 자세한 내용은 [외부 스크립트 사용](#use-external-scripts)을 참조하세요.
- `supportingScriptUris`: `scriptContent` 또는 `primaryScriptUri`에서 호출되는 지원 파일에 공개적으로 액세스할 수 있는 URL 배열을 지정합니다. 자세한 내용은 [외부 스크립트 사용](#use-external-scripts)을 참조하세요.
- `timeout`: [ISO 8601 형식](https://en.wikipedia.org/wiki/ISO_8601)에 지정된 최대 허용 스크립트 실행 시간을 지정합니다. 기본값은 **P1D** 입니다.
- `cleanupPreference`. 스크립트 실행이 터미널 상태가 되면 배포 리소스를 정리하는 기본 설정을 지정합니다. 기본 설정은 **항상** 이며 이는 터미널 상태(성공, 실패, 취소됨)에도 불구하고 리소스를 삭제함을 의미합니다. 자세한 내용은 [배포 스크립트 리소스 정리](#clean-up-deployment-script-resources)를 참조하세요.
- `retentionInterval`: 배포 스크립트 실행이 터미널 상태에 도달하면 서비스에서 배포 스크립트 리소스를 유지하는 간격을 지정합니다. 이 기간이 만료되면 배포 스크립트 리소스가 삭제됩니다. 기간은 [ISO 8601 패턴](https://en.wikipedia.org/wiki/ISO_8601)을 기반으로 합니다. 보존 간격은 1~26시간(PT26H)입니다. 이 속성은 `cleanupPreference`가 **OnExpiration** 으로 설정된 경우에 사용됩니다. 자세한 내용은 [배포 스크립트 리소스 정리](#clean-up-deployment-script-resources)를 참조하세요.

### <a name="additional-samples"></a>추가 샘플

- [샘플 1](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-keyvault.json): 주요 자격 증명 모음을 만들고 배포 스크립트를 사용하여 키 자격 증명 모음에 인증서를 할당합니다.
- [샘플 2](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-keyvault-subscription.json): 구독 수준에서 리소스 그룹을 만들고 리소스 그룹에 키 자격 증명 모음을 만든 다음, 배포 스크립트를 사용하여 키 자격 증명 모음에 인증서를 할당합니다.
- [샘플 3](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-keyvault-mi.json): 사용자 할당 관리 ID를 만들고, 리소스 그룹 수준에서 ID에 참가자 역할을 할당하고, 주요 자격 증명 모음을 만든 다음, 배포 스크립트를 사용하여 인증서를 주요 자격 증명 모음에 할당합니다.

## <a name="use-inline-scripts"></a>인라인 스크립트 사용

다음 템플릿에는 `Microsoft.Resources/deploymentScripts` 유형을 사용하여 정의된 리소스가 하나 있습니다. 강조 표시된 부분이 인라인 스크립트입니다.

:::code language="json" source="~/resourcemanager-templates/deployment-script/deploymentscript-helloworld.json" range="1-44" highlight="24-30":::

> [!NOTE]
> 인라인 배포 스크립트는 큰따옴표로 묶여 있으므로 배포 스크립트 내의 문자열을 백슬래시( **&#92;** ) 또는 작은따옴표로 묶어야 합니다. 이전 JSON 샘플에 표시된 대로 문자열 대체를 사용할 수도 있습니다.

스크립트는 하나의 매개 변수를 사용하고 매개 변수 값을 출력합니다. `DeploymentScriptOutputs`는 출력을 저장하는 데 사용됩니다. 출력 섹션에서 `value` 줄은 저장된 값에 액세스하는 방법을 보여 줍니다. `Write-Output`은 디버깅용으로 사용됩니다. 출력 파일에 액세스하는 방법에 대한 자세한 내용은 [배포 스크립트 모니터링 및 문제 해결](#monitor-and-troubleshoot-deployment-scripts)을 참조하세요. 속성 설명은 [샘플 템플릿](#sample-templates)을 참조하세요.

스크립트를 실행하려면 **사용해 보기** 를 선택하여 Cloud Shell을 연 후 다음 코드를 셸 창에 붙여넣습니다.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the name of the resource group to be created"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"

New-AzResourceGroup -Name $resourceGroupName -Location $location

New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-helloworld.json"

Write-Host "Press [ENTER] to continue ..."
```

출력은 다음과 같이 표시됩니다.

![Resource Manager 템플릿 배포 스크립트 Hello World 출력](./media/deployment-script-template/resource-manager-template-deployment-script-helloworld-output.png)

## <a name="use-external-scripts"></a>외부 스크립트 사용

인라인 스크립트 외에 외부 스크립트 파일도 사용할 수 있습니다. 파일 확장명이 _ps1_ 인 기본 PowerShell 스크립트만 지원됩니다. CLI 스크립트의 경우 스크립트가 유효한 Bash 스크립트이면 기본 스크립트에 모든 확장명을 사용할 수 있으며 확장명을 사용하지 않을 수도 있습니다. 외부 스크립트 파일을 사용하려면 `scriptContent`를 `primaryScriptUri`로 바꿉니다. 다음은 그 예입니다.

```json
"primaryScriptUri": "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-helloworld.ps1",
```

자세한 내용은[예제 템플릿](https://github.com/Azure/azure-docs-json-samples/blob/master/deployment-script/deploymentscript-helloworld-primaryscripturi.json)을 참조하세요.

외부 스크립트 파일에 액세스할 수 있어야 합니다. Azure Storage 계정에 저장된 스크립트 파일을 보호하려면 SAS 토큰을 생성하여 템플릿의 URI에 포함합니다. 배포를 완료할 만큼 충분한 여유를 두고 만료 기간을 설정합니다. 자세한 내용은 [SAS 토큰을 사용하여 개인 ARM 템플릿 배포](./secure-template-with-sas-token.md)를 참조하세요.

배포 스크립트에서 참조하는 스크립트 `primaryScriptUri` 또는 `supportingScriptUris`의 무결성을 확인해야 합니다. 신뢰하는 스크립트만 참조하세요.

## <a name="use-supporting-scripts"></a>지원 스크립트 사용

복잡한 논리를 하나 이상의 지원 스크립트 파일로 분리할 수 있습니다. `supportingScriptUris` 속성을 사용하면 필요한 경우 지원 스크립트 파일에 URI 배열을 제공할 수 있습니다.

```json
"scriptContent": "
    ...
    ./Create-Cert.ps1
    ...
"

"supportingScriptUris": [
  "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/create-cert.ps1"
],
```

지원 스크립트 파일은 인라인 스크립트 및 기본 스크립트 파일 모두에서 호출할 수 있습니다. 지원 스크립트 파일에는 파일 확장명에 대한 제한이 없습니다.

지원 파일은 런타임 시 `azscripts/azscriptinput`에 복사됩니다. 인라인 스크립트 및 기본 스크립트 파일에서 지원 파일을 참조하려면 상대 경로를 사용합니다.

## <a name="work-with-outputs-from-powershell-script"></a>PowerShell 스크립트에서 출력 작업

다음 템플릿에서는 두 `deploymentScripts` 리소스 간에 값을 전달하는 방법을 보여 줍니다.

:::code language="json" source="~/resourcemanager-templates/deployment-script/deploymentscript-basic.json" range="1-68" highlight="30-31,50":::

첫 번째 리소스에서는 `$DeploymentScriptOutputs`라는 변수를 정의하고 이 변수를 사용하여 출력 값을 저장합니다. 템플릿 내의 다른 리소스에서 출력 값에 액세스하려면 다음을 사용합니다.

```json
reference('<ResourceName>').outputs.text
```

## <a name="work-with-outputs-from-cli-script"></a>CLI 스크립트에서 출력 작업

PowerShell 배포 스크립트와는 달리 CLI/Bash 지원은 스크립트 출력을 저장하는 공통 변수를 노출하지 않으며, 대신 스크립트 출력 파일이 있는 위치를 저장하는 `AZ_SCRIPTS_OUTPUT_PATH`라는 환경 변수가 있습니다. Resource Manager 템플릿에서 배포 스크립트를 실행하는 경우 이 환경 변수는 Bash 셸에서 자동으로 설정됩니다. `AZ_SCRIPTS_OUTPUT_PATH`의 값은 */mnt/azscripts/azscriptoutput/scriptoutputs.json* 입니다.

배포 스크립트 출력은 `AZ_SCRIPTS_OUTPUT_PATH` 위치에 저장해야 하며 출력은 유효한 JSON 문자열 개체여야 합니다. 파일의 내용은 키-값 쌍으로 저장해야 합니다. 예를 들어 문자열의 배열은 `{ "MyResult": [ "foo", "bar"] }`로 저장됩니다.  배열 결과만 저장하는 것은 유효하지 않습니다(예: `[ "foo", "bar" ]`).

:::code language="json" source="~/resourcemanager-templates/deployment-script/deploymentscript-basic-cli.json" range="1-44" highlight="32":::

[jq](https://stedolan.github.io/jq/)는 이전 샘플에서 사용된 것입니다. 컨테이너 이미지와 함께 제공됩니다. [개발 환경 구성](#configure-development-environment)을 참조하세요.

## <a name="use-existing-storage-account"></a>기존 스토리지 계정 사용

스크립트를 실행하고 문제를 해결하려면 스토리지 계정 및 컨테이너 인스턴스가 필요합니다. 기존 스토리지 계정을 지정할 수 있습니다. 그러지 않으면 컨테이너 인스턴스와 함께 스토리지 계정이 스크립트 서비스에 의해 자동으로 생성됩니다. 기존 스토리지 계정을 사용하기 위한 요구 사항:

- 지원되는 스토리지 계정 형식은 다음과 같습니다.

    | SKU             | 지원되는 종류     |
    |-----------------|--------------------|
    | Premium_LRS     | FileStorage        |
    | Premium_ZRS     | FileStorage        |
    | Standard_GRS    | Storage, StorageV2 |
    | Standard_GZRS   | StorageV2          |
    | Standard_LRS    | Storage, StorageV2 |
    | Standard_RAGRS  | Storage, StorageV2 |
    | Standard_RAGZRS | StorageV2          |
    | Standard_ZRS    | StorageV2          |

    이러한 조합은 파일 공유를 지원합니다. 자세한 내용은 [Azure 파일 공유 만들기](../../storage/files/storage-how-to-create-file-share.md) 및 [스토리지 계정 유형](../../storage/common/storage-account-overview.md)을 참조하세요.

- 스토리지 계정 방화벽 규칙은 아직 지원되지 않습니다. 자세한 내용은 [Azure Storage 방화벽 및 가상 네트워크 구성](../../storage/common/storage-network-security.md)을 참조하세요.
- 배포 주체에는 스토리지 계정을 관리할 수 있는 사용 권한이 있어야 합니다. 여기에는 파일 공유 읽기, 만들기, 삭제가 포함됩니다.

기존 스토리지 계정을 지정하려면 `Microsoft.Resources/deploymentScripts`의 속성 요소에 다음 JSON을 추가합니다.

```json
"storageAccountSettings": {
  "storageAccountName": "myStorageAccount",
  "storageAccountKey": "myKey"
},
```

- `storageAccountName`: 스토리지 계정의 이름을 지정합니다.
- `storageAccountKey` : 스토리지 계정 키 중 하나를 지정합니다. [listKeys()](./template-functions-resource.md#listkeys) 함수를 사용하여 키를 검색할 수 있습니다. 다음은 그 예입니다.

    ```json
    "storageAccountSettings": {
        "storageAccountName": "[variables('storageAccountName')]",
        "storageAccountKey": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName')), '2019-06-01').keys[0].value]"
    }
    ```

전체 `Microsoft.Resources/deploymentScripts` 정의 샘플은 [샘플 템플릿](#sample-templates)을 참조하세요.

기존 스토리지 계정이 사용되는 경우 스크립트 서비스는 고유한 이름을 사용하여 파일 공유를 만듭니다. 스크립트 서비스에서 파일 공유를 정리하는 방법은 [배포 스크립트 리소스 정리](#clean-up-deployment-script-resources)를 참조하세요.

## <a name="develop-deployment-scripts"></a>배포 스크립트 개발

### <a name="handle-non-terminating-errors"></a>종료되지 않는 오류 처리

배포 스크립트에서 `$ErrorActionPreference` 변수를 사용하여 PowerShell이 종료되지 않는 오류에 대응하는 방식을 제어할 수 있습니다. 배포 스크립트에 변수가 설정되어 있지 않으면 스크립트 서비스에서 기본값인 **Continue** 를 사용합니다.

스크립트 서비스는 `$ErrorActionPreference` 설정에도 불구하고 스크립트에 오류가 발생하면 리소스 프로비저닝 상태를 **실패** 로 설정합니다.

### <a name="use-environment-variables"></a>환경 변수 사용

배포 스크립트는 다음과 같은 환경 변수를 사용합니다.

|환경 변수|기본값|시스템이 예약됨|
|--------------------|-------------|---------------|
|AZ_SCRIPTS_AZURE_ENVIRONMENT|AzureCloud|N|
|AZ_SCRIPTS_CLEANUP_PREFERENCE|OnExpiration|N|
|AZ_SCRIPTS_OUTPUT_PATH|<AZ_SCRIPTS_PATH_OUTPUT_DIRECTORY>/<AZ_SCRIPTS_PATH_SCRIPT_OUTPUT_FILE_NAME>|Y|
|AZ_SCRIPTS_PATH_INPUT_DIRECTORY|/mnt/azscripts/azscriptinput|Y|
|AZ_SCRIPTS_PATH_OUTPUT_DIRECTORY|/mnt/azscripts/azscriptoutput|Y|
|AZ_SCRIPTS_PATH_USER_SCRIPT_FILE_NAME|Azure PowerShell: userscript.ps1; Azure CLI: userscript.sh|Y|
|AZ_SCRIPTS_PATH_PRIMARY_SCRIPT_URI_FILE_NAME|primaryscripturi.config|Y|
|AZ_SCRIPTS_PATH_SUPPORTING_SCRIPT_URI_FILE_NAME|supportingscripturi.config|Y|
|AZ_SCRIPTS_PATH_SCRIPT_OUTPUT_FILE_NAME|scriptoutputs.json|Y|
|AZ_SCRIPTS_PATH_EXECUTION_RESULTS_FILE_NAME|executionresult.json|Y|
|AZ_SCRIPTS_USER_ASSIGNED_IDENTITY|/subscriptions/|N|

`AZ_SCRIPTS_OUTPUT_PATH` 사용에 대한 자세한 내용은 [CLI 스크립트에서 출력 작업](#work-with-outputs-from-cli-script)을 참조하세요.

### <a name="pass-secured-strings-to-deployment-script"></a>배포 스크립트에 보안 문자열 전달

컨테이너 인스턴스에서 환경 변수(EnvironmentVariable)를 설정하면 컨테이너가 실행하는 애플리케이션 또는 스크립트의 동적 구성을 제공할 수 있습니다. 배포 스크립트는 Azure Container Instance와 동일한 방식으로 비보안 환경 변수 및 보안 환경 변수를 처리합니다. 자세한 내용은 [컨테이너 인스턴스에서 환경 변수 설정](../../container-instances/container-instances-environment-variables.md#secure-values)을 참조하세요. 예제는 [샘플 템플릿](#sample-templates)을 참조하세요.

환경 변수에 대해 허용되는 최대 크기는 64KB입니다.

## <a name="monitor-and-troubleshoot-deployment-scripts"></a>배포 스크립트 모니터링 및 문제 해결

스크립트 서비스는 스크립트 실행을 위한 [스토리지 계정](../../storage/common/storage-account-overview.md)(기존 스토리지 계정을 지정하지 않는 경우) 및 [컨테이너 인스턴스](../../container-instances/container-instances-overview.md)를 만듭니다. 이러한 리소스는 스크립트 서비스에서 자동으로 생성되는 경우 리소스 이름에 `azscripts` 접미사가 붙습니다.

![Resource Manager 템플릿 배포 스크립트 리소스 이름](./media/deployment-script-template/resource-manager-template-deployment-script-resources.png)

사용자 스크립트, 실행 결과 및 stdout 파일은 스토리지 계정의 파일 공유에 저장됩니다. `azscripts`라는 폴더가 있습니다. 이 폴더에는 입력 및 출력 파일용 두 개의 폴더 `azscriptinput` 및 `azscriptoutput`이 있습니다.

출력 폴더에는 _executionresult.json_ 및 스크립트 출력 파일이 포함되어 있습니다. _executionresult.json_ 에서 스크립트 실행 오류 메시지를 볼 수 있습니다. 출력 파일은 스크립트가 성공적으로 실행되는 경우에만 생성됩니다. 입력 폴더에는 시스템 PowerShell 스크립트 파일과 사용자 배포 스크립트 파일이 포함되어 있습니다. 사용자 배포 스크립트 파일을 수정된 파일로 바꾸고 Azure 컨테이너 인스턴스에서 배포 스크립트를 다시 실행할 수 있습니다.

### <a name="use-the-azure-portal"></a>Azure Portal 사용

배포 스크립트 리소스를 배포한 후 리소스는 Azure Portal의 리소스 그룹 아래에 나열됩니다. 다음 스크린샷은 배포 스크립트 리소스의 **개요** 페이지를 보여 줍니다.

![Resource Manager 템플릿 배포 스크립트 포털 개요](./media/deployment-script-template/resource-manager-deployment-script-portal.png)

개요 페이지에는 **프로 비전 상태**, **스토리지 계정**, **컨테이너 인스턴스** 및 **로그** 와 같은 리소스에 대한 몇 가지 중요한 정보가 표시됩니다.

왼쪽 메뉴에서 배포 스크립트 콘텐츠, 스크립트에 전달된 인수 및 출력을 볼 수 있습니다. 배포 스크립트를 포함하여 배포 스크립트에 대한 템플릿을 내보낼 수도 있습니다.

### <a name="use-powershell"></a>PowerShell 사용

Azure PowerShell을 사용하여 구독 또는 리소스 그룹 범위에서 배포 스크립트를 관리할 수 있습니다.

- [Get-AzDeploymentScript](/powershell/module/az.resources/get-azdeploymentscript): 배포 스크립트를 가져오거나 나열합니다.
- [Get-AzDeploymentScriptLog](/powershell/module/az.resources/get-azdeploymentscriptlog): 배포 스크립트 실행의 로그를 가져옵니다.
- [Remove-AzDeploymentScript](/powershell/module/az.resources/remove-azdeploymentscript): 배포 스크립트와 연결된 해당 리소스를 제거합니다.
- [Save-AzDeploymentScriptLog](/powershell/module/az.resources/save-azdeploymentscriptlog): 배포 스크립트 실행 로그를 디스크에 저장합니다.

`Get-AzDeploymentScript`는 다음과 유사하게 출력됩니다.

```output
Name                : runPowerShellInlineWithOutput
Id                  : /subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourceGroups/myds0618rg/providers/Microsoft.Resources/deploymentScripts/runPowerShellInlineWithOutput
ResourceGroupName   : myds0618rg
Location            : centralus
SubscriptionId      : 01234567-89AB-CDEF-0123-456789ABCDEF
ProvisioningState   : Succeeded
Identity            : /subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourceGroups/mydentity1008rg/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myuami
ScriptKind          : AzurePowerShell
AzPowerShellVersion : 3.0
StartTime           : 6/18/2020 7:46:45 PM
EndTime             : 6/18/2020 7:49:45 PM
ExpirationDate      : 6/19/2020 7:49:45 PM
CleanupPreference   : OnSuccess
StorageAccountId    : /subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourceGroups/myds0618rg/providers/Microsoft.Storage/storageAccounts/ftnlvo6rlrvo2azscripts
ContainerInstanceId : /subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourceGroups/myds0618rg/providers/Microsoft.ContainerInstance/containerGroups/ftnlvo6rlrvo2azscripts
Outputs             :
                      Key                 Value
                      ==================  ==================
                      text                Hello John Dole

RetentionInterval   : P1D
Timeout             : PT1H
```

### <a name="use-azure-cli"></a>Azure CLI 사용

Azure CLI를 사용하여 구독 또는 리소스 그룹 범위에서 배포 스크립트를 관리할 수 있습니다.

- [az deployment-scripts delete](/cli/azure/deployment-scripts#az_deployment_scripts_delete): 배포 스크립트를 삭제합니다.
- [az deployment-scripts list](/cli/azure/deployment-scripts#az_deployment_scripts_list): 모든 배포 스크립트를 나열합니다.
- [az deployment-scripts show](/cli/azure/deployment-scripts#az_deployment_scripts_show): 배포 스크립트를 검색합니다.
- [az deployment-scripts show-log](/cli/azure/deployment-scripts#az_deployment_scripts_show_log): 배포 스크립트 로그를 표시합니다.

List 명령 출력은 다음과 유사합니다.

```json
[
  {
    "arguments": "-name \\\"John Dole\\\"",
    "azPowerShellVersion": "3.0",
    "cleanupPreference": "OnSuccess",
    "containerSettings": {
      "containerGroupName": null
    },
    "environmentVariables": null,
    "forceUpdateTag": "20200625T025902Z",
    "id": "/subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourceGroups/myds0624rg/providers/Microsoft.Resources/deploymentScripts/runPowerShellInlineWithOutput",
    "identity": {
      "tenantId": "01234567-89AB-CDEF-0123-456789ABCDEF",
      "type": "userAssigned",
      "userAssignedIdentities": {
        "/subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourceGroups/myidentity1008rg/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myuami": {
          "clientId": "01234567-89AB-CDEF-0123-456789ABCDEF",
          "principalId": "01234567-89AB-CDEF-0123-456789ABCDEF"
        }
      }
    },
    "kind": "AzurePowerShell",
    "location": "centralus",
    "name": "runPowerShellInlineWithOutput",
    "outputs": {
      "text": "Hello John Dole"
    },
    "primaryScriptUri": null,
    "provisioningState": "Succeeded",
    "resourceGroup": "myds0624rg",
    "retentionInterval": "1 day, 0:00:00",
    "scriptContent": "\r\n          param([string] $name)\r\n          $output = \"Hello {0}\" -f $name\r\n          Write-Output $output\r\n          $DeploymentScriptOutputs = @{}\r\n          $DeploymentScriptOutputs['text'] = $output\r\n        ",
    "status": {
      "containerInstanceId": "/subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourceGroups/myds0624rg/providers/Microsoft.ContainerInstance/containerGroups/64lxews2qfa5uazscripts",
      "endTime": "2020-06-25T03:00:16.796923+00:00",
      "error": null,
      "expirationTime": "2020-06-26T03:00:16.796923+00:00",
      "startTime": "2020-06-25T02:59:07.595140+00:00",
      "storageAccountId": "/subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourceGroups/myds0624rg/providers/Microsoft.Storage/storageAccounts/64lxews2qfa5uazscripts"
    },
    "storageAccountSettings": null,
    "supportingScriptUris": null,
    "systemData": {
      "createdAt": "2020-06-25T02:59:04.750195+00:00",
      "createdBy": "someone@contoso.com",
      "createdByType": "User",
      "lastModifiedAt": "2020-06-25T02:59:04.750195+00:00",
      "lastModifiedBy": "someone@contoso.com",
      "lastModifiedByType": "User"
    },
    "tags": null,
    "timeout": "1:00:00",
    "type": "Microsoft.Resources/deploymentScripts"
  }
]
```

### <a name="use-rest-api"></a>Rest API 사용

REST API를 사용하여 리소스 그룹 수준 및 구독 수준에서 배포 스크립트 리소스 배포 정보를 가져올 수 있습니다.

```rest
/subscriptions/<SubscriptionID>/resourcegroups/<ResourceGroupName>/providers/microsoft.resources/deploymentScripts/<DeploymentScriptResourceName>?api-version=2020-10-01
```

```rest
/subscriptions/<SubscriptionID>/providers/microsoft.resources/deploymentScripts?api-version=2020-10-01
```

다음 예에서는 [ARMClient](https://github.com/projectkudu/ARMClient)를 사용합니다.

```azurepowershell
armclient login
armclient get /subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourcegroups/myrg/providers/microsoft.resources/deploymentScripts/myDeployementScript?api-version=2020-10-01
```

다음과 유사하게 출력됩니다.

```json
{
  "kind": "AzurePowerShell",
  "identity": {
    "type": "userAssigned",
    "tenantId": "01234567-89AB-CDEF-0123-456789ABCDEF",
    "userAssignedIdentities": {
      "/subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourceGroups/myidentity1008rg/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myuami": {
        "principalId": "01234567-89AB-CDEF-0123-456789ABCDEF",
        "clientId": "01234567-89AB-CDEF-0123-456789ABCDEF"
      }
    }
  },
  "location": "centralus",
  "systemData": {
    "createdBy": "someone@contoso.com",
    "createdByType": "User",
    "createdAt": "2020-06-25T02:59:04.7501955Z",
    "lastModifiedBy": "someone@contoso.com",
    "lastModifiedByType": "User",
    "lastModifiedAt": "2020-06-25T02:59:04.7501955Z"
  },
  "properties": {
    "provisioningState": "Succeeded",
    "forceUpdateTag": "20200625T025902Z",
    "azPowerShellVersion": "3.0",
    "scriptContent": "\r\n          param([string] $name)\r\n          $output = \"Hello {0}\" -f $name\r\n          Write-Output $output\r\n          $DeploymentScriptOutputs = @{}\r\n          $DeploymentScriptOutputs['text'] = $output\r\n        ",
    "arguments": "-name \\\"John Dole\\\"",
    "retentionInterval": "P1D",
    "timeout": "PT1H",
    "containerSettings": {},
    "status": {
      "containerInstanceId": "/subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourceGroups/myds0624rg/providers/Microsoft.ContainerInstance/containerGroups/64lxews2qfa5uazscripts",
      "storageAccountId": "/subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourceGroups/myds0624rg/providers/Microsoft.Storage/storageAccounts/64lxews2qfa5uazscripts",
      "startTime": "2020-06-25T02:59:07.5951401Z",
      "endTime": "2020-06-25T03:00:16.7969234Z",
      "expirationTime": "2020-06-26T03:00:16.7969234Z"
    },
    "outputs": {
      "text": "Hello John Dole"
    },
    "cleanupPreference": "OnSuccess"
  },
  "id": "/subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourceGroups/myds0624rg/providers/Microsoft.Resources/deploymentScripts/runPowerShellInlineWithOutput",
  "type": "Microsoft.Resources/deploymentScripts",
  "name": "runPowerShellInlineWithOutput"
}

```

다음 REST API는 로그를 반환합니다.

```rest
/subscriptions/<SubscriptionID>/resourcegroups/<ResourceGroupName>/providers/microsoft.resources/deploymentScripts/<DeploymentScriptResourceName>/logs?api-version=2020-10-01
```

배포 스크립트 리소스를 삭제하기 전에만 작동합니다.

포털에서 deploymentScripts 리소스를 보려면 **숨겨진 유형 표시** 를 선택합니다.

![Resource Manager 템플릿 배포 스크립트, 숨겨진 유형 표시, 포털](./media/deployment-script-template/resource-manager-deployment-script-portal-show-hidden-types.png)

## <a name="clean-up-deployment-script-resources"></a>배포 스크립트 리소스 정리

스크립트를 실행하고 문제를 해결하려면 스토리지 계정 및 컨테이너 인스턴스가 필요합니다. 기존 스토리지 계정을 지정할 수 있습니다. 그러지 않으면 컨테이너 인스턴스와 함께 스토리지 계정이 스크립트 서비스에 의해 자동으로 생성됩니다. 자동으로 생성된 두 리소스는 배포 스크립트 실행이 터미널 상태가 되면 스크립트 서비스에 의해 삭제됩니다. 리소스가 삭제될 때까지 해당 리소스에 대한 요금이 청구됩니다. 가격 정보는 [Container Instances 가격 책정](https://azure.microsoft.com/pricing/details/container-instances/) 및 [Azure Storage 가격 책정](https://azure.microsoft.com/pricing/details/storage/)을 참조하세요.

이러한 리소스의 수명 주기는 템플릿의 다음 속성에 의해 제어됩니다.

- `cleanupPreference`: 스크립트 실행이 터미널 상태가 될 때 적용되는 정리 기본 설정입니다. 지원되는 값은

  - **항상**: 스크립트 실행이 터미널 상태가 되면 자동으로 생성된 리소스를 삭제합니다. 기존 스토리지 계정이 사용되는 경우 스크립트 서비스는 스토리지 계정에 생성된 파일 공유를 삭제합니다. 리소스 정리 후 `deploymentScripts` 리소스가 계속 존재할 수 있기 때문에 스크립트 서비스는 리소스를 삭제하기 전에 스크립트 실행 결과(예: stdout, 출력, 값 반환 등)를 유지합니다.
  - **OnSuccess**: 스크립트가 성공적으로 실행되는 경우에만 자동으로 생성된 리소스를 삭제합니다. 기존 스토리지 계정이 사용되는 경우 스크립트 서비스는 스크립트 실행에 성공한 경우에만 파일 공유를 제거합니다. 여전히 리소스에 액세스하여 디버그 정보를 찾을 수 있습니다.
  - **OnExpiration**: `retentionInterval` 설정이 만료된 경우에만 자동으로 생성된 리소스를 삭제합니다. 기존 스토리지 계정이 사용되는 경우 스크립트 서비스는 파일 공유를 제거하지만 스토리지 계정은 유지합니다.

- `retentionInterval`: 스크립트 리소스가 유지되고 만료 및 삭제되기 전까지의 시간 간격을 지정합니다.

> [!NOTE]
> 스크립트 서비스에 의해 생성되는 스토리지 계정 및 컨테이너 인스턴스는 다른 용도로 사용하지 않는 것이 좋습니다. 스크립트 수명 주기에 따라 두 리소스가 제거될 수 있습니다.

컨테이너 인스턴스 및 스토리지 계정은 `cleanupPreference`에 따라 삭제됩니다. 그러나 스크립트가 실패하고 `cleanupPreference`가 **항상** 으로 설정되어 있지 않은 경우 배포 프로세스는 자동으로 컨테이너를 1시간 동안 계속 실행합니다. 이 시간 동안 스크립트 문제를 해결할 수 있습니다. 배포에 성공한 후에도 컨테이너를 계속 실행하려면 스크립트에 절전 단계를 추가하세요. 예를 들어 스크립트 끝에 [시작-절전](/powershell/module/microsoft.powershell.utility/start-sleep)을 추가합니다. 절전 단계를 추가하지 않으면 컨테이너는 터미널 상태로 설정되며 아직 삭제되지 않은 경우에도 액세스할 수 없습니다.

## <a name="run-script-more-than-once"></a>스크립트를 두 번 이상 실행

배포 스크립트 실행은 idempotent 작업입니다. `deploymentScripts` 리소스 속성(인라인 스크립트 포함)이 변경되지 않은 경우 템플릿을 다시 배포할 때 스크립트가 실행되지 않습니다. 배포 스크립트 서비스는 템플릿의 리소스 이름을 같은 리소스 그룹에 있는 기존 리소스와 비교합니다. 동일한 배포 스크립트를 여러 번 실행하려는 경우 다음 두 가지 옵션이 있습니다.

- `deploymentScripts` 리소스 그룹의 이름을 바꿉니다. 예를 들어 리소스 이름 또는 리소스 이름의 일부로 [utcNow](./template-functions-date.md#utcnow) 템플릿 함수를 사용합니다. 리소스 이름을 변경하면 새 `deploymentScripts` 리소스가 생성됩니다. 스크립트 실행 기록을 보관하는 것이 좋습니다.

    > [!NOTE]
    > `utcNow` 함수는 매개 변수의 기본값에만 사용할 수 있습니다.

- `forceUpdateTag` 템플릿 속성에 다른 값을 지정합니다. 예를 들어 값으로 `utcNow`를 사용합니다.

> [!NOTE]
> idempotent 배포 스크립트를 작성합니다. 이렇게 하면 실수로 다시 실행되는 경우 시스템이 변경되지 않습니다. 예를 들어 배포 스크립트를 사용하여 Azure 리소스를 만드는 경우 리소스를 만들기 전에 리소스가 존재하지 않는지 확인하므로 스크립트가 성공하거나 리소스를 다시 만들지 않습니다.

## <a name="configure-development-environment"></a>개발 환경 구성

배포 스크립트 개발 환경으로 미리 구성된 컨테이너 이미지를 사용할 수 있습니다. 자세한 내용은 [템플릿에서 배포 스크립트에 대한 개발 환경 구성](./deployment-script-template-configure-dev.md)을 참조하세요.

스크립트를 성공적으로 테스트한 후에는 이 스크립트를 템플릿에서 배포 스크립트로 사용할 수 있습니다.

## <a name="deployment-script-error-codes"></a>배포 스크립트 오류 코드

| 오류 코드 | Description |
|------------|-------------|
| DeploymentScriptInvalidOperation | 템플릿의 배포 스크립트 리소스 정의에 잘못된 속성 이름이 있습니다. |
| DeploymentScriptResourceConflict | 터미널 상태가 아닌 상태에 있는 배포 스크립트 리소스는 삭제할 수 없으며 실행이 1시간을 초과하지 않았습니다. 또는 동일한 리소스 식별자(동일한 구독, 리소스 그룹 이름 및 리소스 이름)를 사용하는 동일한 배포 스크립트를 다시 실행할 수는 없으며, 동시에 다른 스크립트 본문 콘텐츠가 있습니다. |
| DeploymentScriptOperationFailed | 배포 스크립트 작업이 내부적으로 실패했습니다. Microsoft 지원에 문의하세요. |
| DeploymentScriptStorageAccountAccessKeyNotSpecified | 기존 스토리지 계정에 대한 액세스 키를 지정하지 않았습니다.|
| DeploymentScriptContainerGroupContainsInvalidContainers | 배포 스크립트 서비스에서 만든 컨테이너 그룹이 외부에서 수정되었으며 잘못된 컨테이너가 추가되었습니다. |
| DeploymentScriptContainerGroupInNonterminalState | 둘 이상의 배포 스크립트 리소스가 동일한 리소스 그룹에서 동일한 Azure Container Instance 이름을 사용하며 그 중 하나는 아직 실행을 완료하지 않았습니다. |
| DeploymentScriptStorageAccountInvalidKind | BlobBlobStorage 또는 BlobStorage 유형의 기존 스토리지 계정은 파일 공유를 지원하지 않으므로 사용할 수 없습니다. |
| DeploymentScriptStorageAccountInvalidKindAndSku | 기존 스토리지 계정은 파일 공유를 지원하지 않습니다. 지원되는 스토리지 계정의 종류 목록은 [기존 스토리지 계정 사용](#use-existing-storage-account)을 참조하세요. |
| DeploymentScriptStorageAccountNotFound | 스토리지 계정이 존재하지 않거나 외부 프로세스 또는 도구에 의해 삭제되었습니다. |
| DeploymentScriptStorageAccountWithServiceEndpointEnabled | 지정된 스토리지 계정에 서비스 엔드포인트가 있습니다. 서비스 엔드포인트가 포함된 스토리지 계정은 지원되지 않습니다. |
| DeploymentScriptStorageAccountInvalidAccessKey | 기존 스토리지 계정에 지정된 액세스 키가 잘못되었습니다. |
| DeploymentScriptStorageAccountInvalidAccessKeyFormat | 스토리지 계정 키 형식이 잘못되었습니다. [스토리지 계정 액세스 키 관리](../../storage/common/storage-account-keys-manage.md)를 참조하세요. |
| DeploymentScriptExceededMaxAllowedTime | 배포 스크립트 실행 시간이 배포 스크립트 리소스 정의에 지정된 시간 제한 값을 초과했습니다. |
| DeploymentScriptInvalidOutputs | 배포 스크립트 출력이 올바른 JSON 개체가 아닙니다. |
| DeploymentScriptContainerInstancesServiceLoginFailure | 사용자 할당 관리 ID를 1분 간격으로 10번 시도한 후에는 로그인할 수 없습니다. |
| DeploymentScriptContainerGroupNotFound | 배포 스크립트 서비스에서 만든 컨테이너 그룹이 외부 도구나 프로세스에 의해 삭제되었습니다. |
| DeploymentScriptDownloadFailure | 지원 스크립트를 다운로드하지 못했습니다. [지원 스크립트 사용](#use-supporting-scripts)을 참조하세요.|
| DeploymentScriptError | 사용자 스크립트에서 예외가 발생했습니다. |
| DeploymentScriptBootstrapScriptExecutionFailed | 부트스트랩 스크립트에서 오류가 발생했습니다. 부트스트랩 스크립트는 배포 스크립트 실행을 오케스트레이션하는 시스템 스크립트입니다. |
| DeploymentScriptExecutionFailed | 배포 스크립트를 실행하는 동안 알 수 없는 오류가 발생했습니다. |
| DeploymentScriptContainerInstancesServiceUnavailable | ACI(Azure Container Instance)를 만들 때 ACI에서 서비스를 사용할 수 없음 오류가 발생했습니다. |
| DeploymentScriptContainerGroupInNonterminalState | ACI(Azure Container Instance)를 만들 때 다른 배포 스크립트는 동일한 범위(동일한 구독, 리소스 그룹 이름 및 리소스 이름)에서 동일한 ACI 이름을 사용합니다. |
| DeploymentScriptContainerGroupNameInvalid | 지정된 ACI(Azure Container Instance)가 ACI 요구 사항을 충족하지 않습니다. [Azure Container Instances에서 일반적인 문제 해결](../../container-instances/container-instances-troubleshooting.md#issues-during-container-group-deployment)을 참조하세요.|

## <a name="next-steps"></a>다음 단계

이 문서에서는 배포 스크립트를 사용하는 방법을 알아보았습니다. 배포 스크립트 자습서는 다음을 참조하세요.

> [!div class="nextstepaction"]
> [자습서: Azure Resource Manager 템플릿에서 배포 스크립트 사용](./template-tutorial-deployment-script.md)

> [!div class="nextstepaction"]
> [Learn 모듈: 배포 스크립트를 사용하여 ARM 템플릿 확장](/learn/modules/extend-resource-manager-template-deployment-scripts/)
