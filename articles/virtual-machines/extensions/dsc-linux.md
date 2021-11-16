---
title: Linux용 Azure DSC 확장
description: Desired State Configuration을 사용하여 Azure Linux VM을 구성할 수 있도록 OMI 및 DSC 패키지를 설치합니다.
ms.topic: article
ms.service: virtual-machines
ms.subservice: extensions
author: mgoedtel
ms.author: magoedte
ms.collection: linux
ms.date: 06/12/2018
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: fcecc23d99ddc11169200ff903767be6c8c62b9b
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/15/2021
ms.locfileid: "132486173"
---
# <a name="dsc-extension-for-linux-microsoftostcextensionsdscforlinux"></a>Linux용 DSC 확장(Microsoft.OSTCExtensions.DSCForLinux)

DSC(Desired State Configuration)는 구성을 코드로 사용하여 IT 및 개발 인프라를 관리하는 데 사용할 수 있는 관리 플랫폼입니다.

> [!NOTE]
> Linux용 DSC 확장 및 [Linux용 Log Analytics 가상 머신 확장](./oms-linux.md)은 현재 충돌을 나타내며 병렬 구성으로 지원되지 않습니다. 동일한 VM에서 두 솔루션을 함께 사용하지 마세요.
>
> DSC 확장을 사용 하도록 설정 하기 전에 최신 버전의 DSC를 미리 보기에서 사용할 수 있다는 것을 알고 Azure Policy [게스트 구성](../../governance/policy/concepts/guest-configuration.md)이라는 기능을 통해 관리 합니다. 게스트 구성 기능은 Desired State Configuration (DSC) 확장 처리기의 기능, Azure Automation 상태 구성 및 가장 일반적으로 요청 되는 기능을 고객 피드백에서 결합 합니다. 게스트 구성에는 [Arc 사용 서버](../../azure-arc/servers/overview.md)를 통한 하이브리드 컴퓨터 지원 기능도 포함 되어 있습니다.

DSCForLinux 확장은 Microsoft에서 게시하고 지원합니다. 확장은 OMI 및 DSC 에이전트를 Azure 가상 머신에 설치합니다. DSC 확장에서는 다음 작업도 수행할 수 있습니다.

- Linux VM을 Azure Automation 계정에 등록하여 Azure Automation 서비스에서 구성을 끌어옵니다(ExtensionAction 등록).
- MOF 구성을 Linux VM에 밀어넣습니다(ExtensionAction 밀어넣기).
- 메타 MOF 구성을 Linux VM에 적용하여 노드 구성을 끌어오도록 끌어오기 서버를 구성합니다(ExtensionAction 끌어오기).
- Linux VM에 사용자 지정 DSC 모듈을 설치합니다(ExtensionAction 설치).
- Linux VM에서 사용자 지정 DSC 모듈을 제거합니다(ExtensionAction 제거).

## <a name="prerequisites"></a>필수 구성 요소

### <a name="operating-system"></a>운영 체제

Linux를 실행하는 노드의 경우 DSC Linux 확장은 [PowerShell DSC 문서](/powershell/scripting/dsc/getting-started/lnxgettingstarted)에 나열된 모든 Linux 배포판을 지원합니다.

### <a name="internet-connectivity"></a>인터넷 연결

DSCForLinux 확장을 사용하려면 대상 가상 머신이 인터넷에 연결되어 있어야 합니다. 예를 들어, 등록 확장에는 Automation 서비스에 대한 연결이 필요합니다.
밀어넣기, 끌어오기, 설치와 같은 다른 작업의 경우 Azure Storage 및 GitHub에 연결해야 합니다. 이 연결은 고객이 제공하는 설정에 따라 달라집니다.

## <a name="extension-schema"></a>확장 스키마

### <a name="public-configuration"></a>공용 구성

지원되는 모든 공용 구성 매개 변수는 다음과 같습니다.

* `FileUri`: (선택 사항, 문자열) MOF 파일/메타 MOF 파일/사용자 지정 리소스 zip 파일의 URI입니다.
* `ResourceName`: (선택 사항, 문자열) 사용자 지정 리소스 모듈의 이름입니다.
* `ExtensionAction`: (선택 사항, 문자열) 확장에서 수행할 작업을 지정합니다. 유효한 값은 Register, Push, Pull, Install, Remove입니다. 지정하지 않으면 기본적으로 밀어넣기 작업으로 간주됩니다.
* `NodeConfigurationName`: (선택 사항, 문자열) 적용할 노드 구성의 이름입니다.
* `RefreshFrequencyMins`: (선택 사항, 정수) DSC가 끌어오기 서버에서 구성을 가져오려고 시도하는 빈도(분)를 지정합니다.
       끌어오기 서버의 구성이 대상 노드의 현재 구성과 다르면 보류 중인 저장소에 복사되고 적용됩니다.
* `ConfigurationMode`: (선택 사항, 문자열) DSC에서 구성을 적용해야 하는 방법을 지정합니다. 유효한 값은 ApplyOnly, ApplyAndMonitor, ApplyAndAutoCorrect입니다.
* `ConfigurationModeFrequencyMins`: (선택 사항, 정수) DSC에서 구성이 원하는 상태에 있는지 확인하는 빈도(분)를 지정합니다.

> [!NOTE]
> 2\.3 이전 버전을 사용하는 경우 mode 매개 변수는 ExtensionAction과 동일합니다. 모드는 오버로드되는 용어로 보입니다. 혼동을 방지하기 위해 ExtensionAction은 버전 2.3부터 사용됩니다. 이전 버전과의 호환성을 위해 확장은 mode와 ExtensionAction을 모두 지원합니다.
>

### <a name="protected-configuration"></a>보호된 구성

지원되는 모든 보호된 구성 매개 변수는 다음과 같습니다.

* `StorageAccountName`: (선택 사항, 문자열) 파일이 포함된 스토리지 계정의 이름
* `StorageAccountKey`: (선택 사항, 문자열) 파일이 포함된 스토리지 계정의 키
* `RegistrationUrl`: (선택 사항, 문자열) Azure Automation 계정의 URL
* `RegistrationKey`: (선택 사항, 문자열) Azure Automation 계정의 액세스 키

## <a name="scenarios"></a>시나리오

### <a name="register-an-azure-automation-account"></a>Azure Automation 계정 등록

protected.json
```json
{
  "RegistrationUrl": "<azure-automation-account-url>",
  "RegistrationKey": "<azure-automation-account-key>"
}
```
public.json
```json
{
  "ExtensionAction" : "Register",
  "NodeConfigurationName" : "<node-configuration-name>",
  "RefreshFrequencyMins" : "<value>",
  "ConfigurationMode" : "<ApplyAndMonitor | ApplyAndAutoCorrect | ApplyOnly>",
  "ConfigurationModeFrequencyMins" : "<value>"
}
```

PowerShell 형식
```powershell
$privateConfig = '{
  "RegistrationUrl": "<azure-automation-account-url>",
  "RegistrationKey": "<azure-automation-account-key>"
}'

$publicConfig = '{
  "ExtensionAction" : "Register",
  "NodeConfigurationName": "<node-configuration-name>",
  "RefreshFrequencyMins": "<value>",
  "ConfigurationMode": "<ApplyAndMonitor | ApplyAndAutoCorrect | ApplyOnly>",
  "ConfigurationModeFrequencyMins": "<value>"
}'
```

### <a name="apply-an-mof-configuration-file-in-an-azure-storage-account-to-the-vm"></a>VM에 MOF 구성 파일(Azure Storage 계정에 있음) 적용

protected.json
```json
{
  "StorageAccountName": "<storage-account-name>",
  "StorageAccountKey": "<storage-account-key>"
}
```

public.json
```json
{
  "FileUri": "<mof-file-uri>",
  "ExtensionAction": "Push"
}
```

PowerShell 형식
```powershell
$privateConfig = '{
  "StorageAccountName": "<storage-account-name>",
  "StorageAccountKey": "<storage-account-key>"
}'

$publicConfig = '{
  "FileUri": "<mof-file-uri>",
  "ExtensionAction": "Push"
}'
```

### <a name="apply-an-mof-configuration-file-in-public-storage-to-the-vm"></a>VM에 MOF 구성 파일(공용 스토리지에 있음) 적용

public.json
```json
{
  "FileUri": "<mof-file-uri>"
}
```

PowerShell 형식
```powershell
$publicConfig = '{
  "FileUri": "<mof-file-uri>"
}'
```

### <a name="apply-a-meta-mof-configuration-file-in-an-azure-storage-account-to-the-vm"></a>VM에 메타 MOF 구성 파일(Azure Storage 계정에 있음) 적용

protected.json
```json
{
  "StorageAccountName": "<storage-account-name>",
  "StorageAccountKey": "<storage-account-key>"
}
```

public.json
```json
{
  "ExtensionAction": "Pull",
  "FileUri": "<meta-mof-file-uri>"
}
```

PowerShell 형식
```powershell
$privateConfig = '{
  "StorageAccountName": "<storage-account-name>",
  "StorageAccountKey": "<storage-account-key>"
}'

$publicConfig = '{
  "ExtensionAction": "Pull",
  "FileUri": "<meta-mof-file-uri>"
}'
```

### <a name="apply-a-meta-mof-configuration-file-in-public-storage-to-the-vm"></a>VM에 메타 MOF 구성 파일(공용 스토리지에 있음) 적용

public.json

```json
{
  "FileUri": "<meta-mof-file-uri>",
  "ExtensionAction": "Pull"
}
```

PowerShell 형식

```powershell
$publicConfig = '{
  "FileUri": "<meta-mof-file-uri>",
  "ExtensionAction": "Pull"
}'
```

### <a name="install-a-custom-resource-module-a-zip-file-in-an-azure-storage-account-to-the-vm"></a>VM에 사용자 지정 리소스 모듈(Azure Storage 계정의 zip 파일) 설치

protected.json

```json
{
  "StorageAccountName": "<storage-account-name>",
  "StorageAccountKey": "<storage-account-key>"
}
```

public.json

```json
{
  "ExtensionAction": "Install",
  "FileUri": "<resource-zip-file-uri>"
}
```

PowerShell 형식
```powershell
$privateConfig = '{
  "StorageAccountName": "<storage-account-name>",
  "StorageAccountKey": "<storage-account-key>"
}'

$publicConfig = '{
  "ExtensionAction": "Install",
  "FileUri": "<resource-zip-file-uri>"
}'
```

### <a name="install-a-custom-resource-module-a-zip-file-in-public-storage-to-the-vm"></a>VM에 사용자 지정 리소스 모듈(퍼블릭 스토리지의 zip 파일) 설치

public.json

```json
{
  "ExtensionAction": "Install",
  "FileUri": "<resource-zip-file-uri>"
}

```

PowerShell 형식

```powershell
$publicConfig = '{
  "ExtensionAction": "Install",
  "FileUri": "<resource-zip-file-uri>"
}'
```

### <a name="remove-a-custom-resource-module-from-the-vm"></a>VM에서 사용자 지정 리소스 모듈 제거

public.json

```json
{
  "ResourceName": "<resource-name>",
  "ExtensionAction": "Remove"
}
```

PowerShell 형식

```powershell
$publicConfig = '{
  "ResourceName": "<resource-name>",
  "ExtensionAction": "Remove"
}'
```

## <a name="template-deployment"></a>템플릿 배포

Azure Resource Manager 템플릿을 사용하여 Azure VM 확장을 배포할 수 있습니다. Azure Automation에 온보딩하는 것처럼 배포 후 구성이 필요한 하나 이상의 가상 머신을 배포하는 경우 템플릿을 사용하는 것이 좋습니다.

Resource Manager 템플릿 샘플은 [dsc-linux-azure-storage-on-ubuntu](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.compute/dsc-linux-azure-storage-on-ubuntu) 및 [dsc-linux-public-storage-on-ubuntu](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.compute/dsc-linux-public-storage-on-ubuntu)입니다.

Azure Resource Manager 템플릿에 관한 자세한 내용은 [Azure Resource Manager 템플릿 작성](../../azure-resource-manager/templates/syntax.md)을 참조하세요.

## <a name="azure-cli-deployment"></a>Azure CLI 배포

### <a name="use-azure-cliazure-cli"></a>[Azure CLI][azure-cli] 사용

DSCForLinux 확장을 배포하기 전에 섹션 3의 다양한 시나리오에 따라 `public.json` 및 `protected.json`을 구성합니다.

#### <a name="classic"></a>클래식

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]

클래식 배포 모드는 Azure 서비스 관리 모드라고도 합니다. 다음을 실행하여 클래식 모드로 전환할 수 있습니다.

```
$ azure config mode asm
```

다음을 실행하여 DSCForLinux 확장을 배포할 수 있습니다.

```
$ azure vm extension set <vm-name> DSCForLinux Microsoft.OSTCExtensions <version> \
--private-config-path protected.json --public-config-path public.json
```

사용 가능한 최신 확장 버전을 확인하려면 다음을 실행합니다.

```
$ azure vm extension list
```

#### <a name="resource-manager"></a>리소스 관리자

다음을 실행하여 Azure Resource Manager 모드로 전환할 수 있습니다.

```
$ azure config mode arm
```

다음을 실행하여 DSCForLinux 확장을 배포할 수 있습니다.

```
$ azure vm extension set <resource-group> <vm-name> \
DSCForLinux Microsoft.OSTCExtensions <version> \
--private-config-path protected.json --public-config-path public.json
```

> [!NOTE]
> Azure Resource Manager 모드에서는 현재 `azure vm extension list`를 사용할 수 없습니다.
>

### <a name="use-azure-powershellazure-powershell"></a>[Azure PowerShell][azure-powershell] 사용

#### <a name="classic"></a>클래식

다음을 실행하여 Azure 서비스 관리 모드에서 Azure 계정에 로그인할 수 있습니다.

```powershell
Add-AzureAccount
```

그리고 다음을 실행하여 DSCForLinux 확장을 배포합니다.

```powershell
$vmname = '<vm-name>'
$vm = Get-AzureVM -ServiceName $vmname -Name $vmname
$extensionName = 'DSCForLinux'
$publisher = 'Microsoft.OSTCExtensions'
$version = '< version>'
```

이전 섹션의 다양한 시나리오에 따라 $privateConfig 및 $publicConfig의 콘텐츠를 변경합니다.

```
$privateConfig = '{
  "StorageAccountName": "<storage-account-name>",
  "StorageAccountKey": "<storage-account-key>"
}'
```

```
$publicConfig = '{
  "ExtensionAction": "Push",
  "FileUri": "<mof-file-uri>"
}'
```

```powershell
Set-AzureVMExtension -ExtensionName $extensionName -VM $vm -Publisher $publisher `
  -Version $version -PrivateConfiguration $privateConfig `
  -PublicConfiguration $publicConfig | Update-AzureVM
```

#### <a name="resource-manager"></a>리소스 관리자

다음을 실행하여 Azure Resource Manager 모드에서 Azure 계정에 로그인할 수 있습니다.

```powershell
Login-AzAccount
```

Azure Resource Manager에서 Azure PowerShell을 사용하는 방법에 관한 자세한 내용은 [Azure PowerShell을 사용하여 Azure 리소스 관리](../../azure-resource-manager/management/manage-resources-powershell.md)를 참조하세요.

다음을 실행하여 DSCForLinux 확장을 배포할 수 있습니다.

```powershell
$rgName = '<resource-group-name>'
$vmName = '<vm-name>'
$location = '< location>'
$extensionName = 'DSCForLinux'
$publisher = 'Microsoft.OSTCExtensions'
$version = '< version>'
```

이전 섹션의 다양한 시나리오에 따라 $privateConfig 및 $publicConfig의 콘텐츠를 변경합니다.

```
$privateConfig = '{
  "StorageAccountName": "<storage-account-name>",
  "StorageAccountKey": "<storage-account-key>"
}'
```

```
$publicConfig = '{
  "ExtensionAction": "Push",
  "FileUri": "<mof-file-uri>"
}'
```

```powershell
Set-AzVMExtension -ResourceGroupName $rgName -VMName $vmName -Location $location `
  -Name $extensionName -Publisher $publisher -ExtensionType $extensionName `
  -TypeHandlerVersion $version -SettingString $publicConfig -ProtectedSettingString $privateConfig
```

## <a name="troubleshoot-and-support"></a>문제 해결 및 지원

### <a name="troubleshoot"></a>문제 해결

확장 배포 상태에 대한 데이터는 Azure CLI를 사용하여 Azure Portal에서 검색할 수 있습니다. 지정된 VM에 대한 확장의 배포 상태를 보려면 Azure CLI를 사용하여 다음 명령을 실행합니다.

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

확장 실행 출력은 다음 파일에 기록됩니다.

```
/var/log/azure/<extension-name>/<version>/extension.log file.
```

오류 코드: 51은 지원되지 않는 배포판 또는 지원되지 않는 확장 작업을 나타냅니다.
OMI의 상위 버전이 이미 머신에 있으면 경우에 따라 DSC Linux 확장에서 OMI를 설치하지 못합니다. [오류 응답: (000003) 다운그레이드가 허용되지 않음]

### <a name="support"></a>지원

이 문서의 어디서든 도움이 필요한 경우 [MSDN Azure 및 Stack Overflow 포럼](https://azure.microsoft.com/support/community/)에서 Azure 전문가에게 문의하세요. 또는 Azure 지원 인시던트를 제출할 수 있습니다. [Azure 지원 사이트](https://azure.microsoft.com/support/options/)로 이동하고 **지원 받기** 를 선택합니다. Azure 지원을 사용하는 방법에 대한 자세한 내용은 [Microsoft Azure 지원 FAQ](https://azure.microsoft.com/support/faq/)를 참조하세요.

## <a name="next-steps"></a>다음 단계

확장에 대한 자세한 내용은 [Linux용 가상 머신 확장 및 기능](features-linux.md)을 참조하세요.
