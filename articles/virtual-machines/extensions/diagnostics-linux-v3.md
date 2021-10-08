---
title: Azure Compute - Linux 진단 확장 3.0
description: Azure에서 실행되는 Linux VM에서 메트릭 및 로그 이벤트를 수집하도록 Azure Linux 진단 확장(LAD) 3.0을 구성하는 방법입니다.
ms.topic: article
ms.service: virtual-machines
ms.subservice: extensions
author: amjads1
ms.author: amjads
ms.collection: linux
ms.date: 12/13/2018
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 04264276b3419e32aa420792027502ce3d1acbb2
ms.sourcegitcommit: bee590555f671df96179665ecf9380c624c3a072
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/07/2021
ms.locfileid: "129669685"
---
# <a name="use-linux-diagnostic-extension-30-to-monitor-metrics-and-logs"></a>Linux 진단 확장 3.0을 사용하여 메트릭 및 로그 모니터링

이 문서에서는 Linux 진단 확장(LAD) 버전 3.0 이상에 대해 설명합니다.

> [!IMPORTANT]
> 버전 2.3 이하 버전에 대한 자세한 내용은 [Linux VM의 성능 및 진단 데이터 모니터링](/previous-versions/azure/virtual-machines/linux/classic/diagnostic-extension-v2)을 참조하세요.

## <a name="introduction"></a>소개

Linux 진단 확장을 통해 사용자는 Microsoft Azure에서 실행하는 Linux VM의 상태를 모니터링할 수 있습니다. 제공되는 기능은 다음과 같습니다.

* VM에서 시스템 성능 메트릭을 수집하여 지정된 스토리지 계정의 특정 테이블에 저장합니다.
* syslog에서 로그 이벤트를 검색하여 지정된 스토리지 계정의 특정 테이블에 저장합니다.
* 사용자가 수집 및 업로드된 데이터 메트릭을 사용자 지정할 수 있도록 설정합니다.
* 사용자가 수집 및 업로드된 이벤트의 심각도 수준과 syslog 기능을 사용자 지정할 수 있도록 설정합니다.
* 사용자가 지정된 스토리지 테이블에 지정된 로그 파일을 업로드할 수 있도록 설정합니다.
* 지정된 스토리지 계정의 JSON 형식 Blob 및 임의 Azure Event Hubs 엔드포인트로 메트릭과 로그 이벤트 전송을 지원합니다.

이 확장은 Azure 배포 모델 모두에서 작동합니다.

## <a name="install-the-extension-on-a-vm"></a>VM에 확장 설치

Azure PowerShell cmdlet, Azure CLI 스크립트, ARM 템플릿(Azure Resource Monitor 템플릿) 또는 Azure Portal을 사용하여 확장을 사용하도록 설정할 수 있습니다. 자세한 내용은 [확장 기능](features-linux.md)을 참조하세요.

>[!NOTE]
>LAD VM 확장의 일부 구성 요소는 [Log Analytics VM 확장](./oms-linux.md)에도 제공됩니다. 이 아키텍처로 인해 동일한 ARM 템플릿에서 두 확장을 모두 인스턴스화하면 충돌이 발생할 수 있습니다. 
>
>설치 시간 충돌을 방지하려면 [`dependsOn` 지시문](../../azure-resource-manager/templates/resource-dependency.md#dependson)을 사용하여 확장이 순차적으로 설치되도록 합니다. 확장은 어느 순서로든 설치할 수 있습니다.

관련 설치 지침 및 [다운로드 가능한 샘플 구성](https://raw.githubusercontent.com/Azure/azure-linux-extensions/master/Diagnostic/tests/lad_2_3_compatible_portal_pub_settings.json)을 참조하여 다음을 수행할 수 있도록 LAD 3.0을 구성합니다.

* LAD 2.3과 동일한 메트릭을 캡처 및 저장합니다.
* 유용한 파일 시스템 메트릭 집합을 캡처합니다. LAD 3.0의 새로운 기능입니다.
* LAD 2.3이 사용된 기본 syslog 컬렉션을 캡처합니다.
* VM 메트릭에 대한 차트 및 경고에 대해 Azure Portal 환경을 사용하도록 설정합니다.

다운로드 가능한 구성은 예제일 뿐입니다. 필요에 따라 수정할 수 있습니다.

### <a name="supported-linux-distributions"></a>지원되는 Linux 배포

LAD는 다음의 배포 및 버전을 지원합니다. 배포 및 버전 목록은 Azure에서 보증하는 Linux 공급업체 이미지에만 적용됩니다. 확장은 일반적으로 어플라이언스와 같은 타사 BYOL 및 BYOS 이미지를 지원하지 않습니다.

Debian 7과 같이 주 버전만 나와 있는 배포는 모든 부 버전에 대해서도 지원됩니다. 부 버전을 지정하면 해당 버전만 지원됩니다. 더하기 기호(+)를 추가하면 지정된 버전 이상의 부 버전이 지원됩니다.

지원되는 배포판 및 버전:

- Ubuntu 20.04, 18.04, 16.04, 14.04
- CentOS 7, 6.5 이상
- Oracle Linux 7, 6.4 이상
- OpenSUSE 13.1 이상
- SUSE Linux Enterprise Server 12
- Debian 9, 8, 7
- RHEL(Red Hat Enterprise Linux) 7, 6.7 이상

### <a name="prerequisites"></a>필수 구성 요소

* **Azure Linux 에이전트 버전 2.2.0 이상**. 대부분의 Azure VM Linux 갤러리 이미지에는 2.2.7 이후 버전이 포함되어 있습니다. VM에 설치된 버전을 확인하려면 `/usr/sbin/waagent -version`을 실행합니다. VM이 이전 버전을 실행하는 경우 [게스트 에이전트를 업데이트](./update-linux-agent.md)합니다.
* **Azure CLI** 필요한 경우 머신에서 [Azure CLI](/cli/azure/install-azure-cli) 환경을 설정합니다.
* **wget 명령** 입니다. 아직 없는 경우 `sudo apt-get install wget`을 실행합니다.
* 기존 **Azure 구독** 입니다. 
* 데이터를 저장할 기존 **범용 스토리지 계정** 입니다. 범용 스토리지 계정이 테이블 스토리지를 지원해야 합니다. Blob 스토리지 계정은 작동하지 않습니다.
* **Python 2**

### <a name="python-requirement"></a>Python 요구 사항

Linux 진단 확장에는 Python 2가 필요합니다. 가상 머신이 기본적으로 Python 2가 포함되지 않은 배포를 사용하는 경우에는 Python 2를 설치해야 합니다. 다음 샘플 명령을 통해 다양한 배포에 Python 2를 설치합니다.   

- Red Hat, CentOS, Oracle: `yum install -y python2`
- Ubuntu, Debian: `apt-get install -y python2`
- SUSE: `zypper install -y python2`

`python2` 실행 파일의 별칭은 *python* 으로 지정해야 합니다. 별칭을 설정하는 한 가지 방법은 다음과 같습니다.

1. 다음 명령을 실행하여 기존 별칭을 제거합니다.
 
    ```
    sudo update-alternatives --remove-all python
    ```

2. 다음 명령을 실행하여 별칭을 만듭니다.

    ```
    sudo update-alternatives --install /usr/bin/python python /usr/bin/python2 1
    ```

### <a name="sample-installation"></a>샘플 설치

다음 예제에서 다운로드된 샘플 구성은 표준 데이터 세트를 수집하여 테이블 스토리지로 전송합니다. 샘플 구성의 URL과 해당 내용은 변경될 수 있습니다. 

대부분의 경우, 포털 설정 JSON 파일의 복사본을 다운로드하고 요구 사항에 맞게 사용자 지정해야 합니다. 그런 다음, 매번 URL에서 다운로드하는 대신 템플릿 또는 자체적인 자동화를 통해 사용자 지정 버전의 구성 파일을 사용합니다.

> [!NOTE]
> 다음 샘플의 경우 코드를 실행하기 전에 첫 번째 섹션의 변수에 대해 올바른 값을 입력합니다. 
#### <a name="azure-cli-sample"></a>Azure CLI 샘플

```azurecli
# Set your Azure VM diagnostic variables.
my_resource_group=<your_azure_resource_group_name_containing_your_azure_linux_vm>
my_linux_vm=<your_azure_linux_vm_name>
my_diagnostic_storage_account=<your_azure_storage_account_for_storing_vm_diagnostic_data>

# Login to Azure before you do anything else.
az login

# Select the subscription that contains the storage account.
az account set --subscription <your_azure_subscription_id>

# Download the sample public settings. (You could also use curl or any web browser.)
wget https://raw.githubusercontent.com/Azure/azure-linux-extensions/master/Diagnostic/tests/lad_2_3_compatible_portal_pub_settings.json -O portal_public_settings.json

# Build the VM resource ID. Replace the storage account name and resource ID in the public settings.
my_vm_resource_id=$(az vm show -g $my_resource_group -n $my_linux_vm --query "id" -o tsv)
sed -i "s#__DIAGNOSTIC_STORAGE_ACCOUNT__#$my_diagnostic_storage_account#g" portal_public_settings.json
sed -i "s#__VM_RESOURCE_ID__#$my_vm_resource_id#g" portal_public_settings.json

# Build the protected settings (storage account SAS token).
my_diagnostic_storage_account_sastoken=$(az storage account generate-sas --account-name $my_diagnostic_storage_account --expiry 2037-12-31T23:59:00Z --permissions wlacu --resource-types co --services bt -o tsv)
my_lad_protected_settings="{'storageAccountName': '$my_diagnostic_storage_account', 'storageAccountSasToken': '$my_diagnostic_storage_account_sastoken'}"

# Finally, tell Azure to install and enable the extension.
az vm extension set --publisher Microsoft.Azure.Diagnostics --name LinuxDiagnostic --version 3.0 --resource-group $my_resource_group --vm-name $my_linux_vm --protected-settings "${my_lad_protected_settings}" --settings portal_public_settings.json
```
#### <a name="azure-cli-sample-to-install-lad-30-on-the-virtual-machine-scale-set-instance"></a>가상 머신 확장 집합 인스턴스에 LAD 3.0을 설치하는 Azure CLI 샘플

```azurecli
#Set your Azure Virtual Machine Scale Sets diagnostic variables.
$my_resource_group=<your_azure_resource_group_name_containing_your_azure_linux_vm>
$my_linux_vmss=<your_azure_linux_vmss_name>
$my_diagnostic_storage_account=<your_azure_storage_account_for_storing_vm_diagnostic_data>

# Login to Azure before you do anything else.
az login

# Select the subscription that contains the storage account.
az account set --subscription <your_azure_subscription_id>

# Download the sample public settings. (You could also use curl or any web browser.)
wget https://raw.githubusercontent.com/Azure/azure-linux-extensions/master/Diagnostic/tests/lad_2_3_compatible_portal_pub_settings.json -O portal_public_settings.json

# Build the virtual machine scale set resource ID. Replace the storage account name and resource ID in the public settings.
$my_vmss_resource_id=$(az vmss show -g $my_resource_group -n $my_linux_vmss --query "id" -o tsv)
sed -i "s#__DIAGNOSTIC_STORAGE_ACCOUNT__#$my_diagnostic_storage_account#g" portal_public_settings.json
sed -i "s#__VM_RESOURCE_ID__#$my_vmss_resource_id#g" portal_public_settings.json

# Build the protected settings (storage account SAS token).
$my_diagnostic_storage_account_sastoken=$(az storage account generate-sas --account-name $my_diagnostic_storage_account --expiry 2037-12-31T23:59:00Z --permissions wlacu --resource-types co --services bt -o tsv)
$my_lad_protected_settings="{'storageAccountName': '$my_diagnostic_storage_account', 'storageAccountSasToken': '$my_diagnostic_storage_account_sastoken'}"

# Finally, tell Azure to install and enable the extension.
az vmss extension set --publisher Microsoft.Azure.Diagnostics --name LinuxDiagnostic --version 3.0 --resource-group $my_resource_group --vmss-name $my_linux_vmss --protected-settings "${my_lad_protected_settings}" --settings portal_public_settings.json
```

#### <a name="powershell-sample"></a>PowerShell 샘플

```powershell
$storageAccountName = "yourStorageAccountName"
$storageAccountResourceGroup = "yourStorageAccountResourceGroupName"
$vmName = "yourVMName"
$VMresourceGroup = "yourVMResourceGroupName"

# Get the VM object
$vm = Get-AzVM -Name $vmName -ResourceGroupName $VMresourceGroup

# Get the public settings template from GitHub and update the templated values for storage account and resource ID
$publicSettings = (Invoke-WebRequest -Uri https://raw.githubusercontent.com/Azure/azure-linux-extensions/master/Diagnostic/tests/lad_2_3_compatible_portal_pub_settings.json).Content
$publicSettings = $publicSettings.Replace('__DIAGNOSTIC_STORAGE_ACCOUNT__', $storageAccountName)
$publicSettings = $publicSettings.Replace('__VM_RESOURCE_ID__', $vm.Id)

# If you have customized public settings, you can inline those rather than using the preceding template: $publicSettings = '{"ladCfg":  { ... },}'

# Generate a SAS token for the agent to use to authenticate with the storage account
$sasToken = New-AzStorageAccountSASToken -Service Blob,Table -ResourceType Service,Container,Object -Permission "racwdlup" -Context (Get-AzStorageAccount -ResourceGroupName $storageAccountResourceGroup -AccountName $storageAccountName).Context -ExpiryTime $([System.DateTime]::Now.AddYears(10))

# Build the protected settings (storage account SAS token)
$protectedSettings="{'storageAccountName': '$storageAccountName', 'storageAccountSasToken': '$sasToken'}"

# Finally, install the extension with the settings you built
Set-AzVMExtension -ResourceGroupName $VMresourceGroup -VMName $vmName -Location $vm.Location -ExtensionType LinuxDiagnostic -Publisher Microsoft.Azure.Diagnostics -Name LinuxDiagnostic -SettingString $publicSettings -ProtectedSettingString $protectedSettings -TypeHandlerVersion 3.0 
```

### <a name="update-the-extension-settings"></a>확장 설정 업데이트

보호 또는 퍼블릭 설정을 변경한 후 동일한 명령을 실행하여 VM에 배포합니다. 설정이 변경되면 업데이트 내용이 확장으로 전송됩니다. LAD가 구성을 다시 로드하고 자체를 다시 시작합니다.

### <a name="migrate-from-previous-versions-of-the-extension"></a>이전 확장 버전에서 마이그레이션

최신 확장 버전은 *4.0* 입니다. 

> [!IMPORTANT]
> 이 확장에서는 구성에 대해 호환성이 손상되는 변경을 도입했습니다. 변경으로 인해 확장의 보안이 강화되어 2.x 버전과의 호환성을 유지할 수 없었습니다. 또한 이 확장에 대한 확장 게시자는 2.x 버전의 게시자와 다릅니다.
>
> 2\.x에서 새 버전으로 마이그레이션하려면 먼저 이전 게시자 이름으로 된 이전 확장을 제거합니다. 그런 다음, 버전 3을 설치합니다.

권장 사항:

* 자동 부 버전 업그레이드를 사용하도록 설정하여 확장을 설치합니다.
  * 클래식 배포 모델 VM에서, Azure XPLAT CLI 또는 PowerShell을 통해 확장을 설치하는 경우 버전 `3.*`를 지정합니다.
  * Azure Resource Manager 배포 모델 VM에서는 VM 배포 템플릿에 `"autoUpgradeMinorVersion": true`를 포함합니다.
* LAD 3.0에 대해 새 스토리지 계정 또는 다른 스토리지 계정을 사용합니다. LAD 2.3과 LAD 3.0 간에 다음과 같은 몇 가지 사소한 비호환성 문제가 있어 계정 공유에 문제가 발생합니다.
  * LAD 3.0은 syslog 이벤트를 이름이 다른 테이블에 저장합니다.
  * LAD 3.0에서는 `builtin` 메트릭의 `counterSpecifier` 문자열이 다릅니다.

## <a name="protected-settings"></a>보호 설정

이 구성 정보 집합에는 퍼블릭 보기로부터 보호해야 하는 중요한 정보가 포함되어 있습니다. 예를 들어 스토리지 자격 증명이 포함됩니다. 이러한 설정은 확장에 의해 전송되어 암호화된 형태로 저장됩니다.

```json
{
    "storageAccountName" : "the storage account to receive data",
    "storageAccountEndPoint": "the hostname suffix for the cloud for this account",
    "storageAccountSasToken": "SAS access token",
    "mdsdHttpProxy": "HTTP proxy settings",
    "sinksConfig": { ... }
}
```

Name | 값
---- | -----
storageAccountName | 확장에 의해 데이터가 기록될 스토리지 계정의 이름입니다.
storageAccountEndPoint | (선택 사항) 스토리지 계정이 있는 클라우드를 식별하는 엔드포인트입니다. 이 설정이 없는 경우 LAD는 Azure 퍼블릭 클라우드 `https://core.windows.net`으로 기본 설정됩니다. Azure 독일, Azure Government 또는 Azure 중국 21Vianet에서 스토리지 계정을 사용하려면 이 값을 필요에 따라 설정합니다.
storageAccountSasToken | Blob 및 테이블 서비스에 대한 [계정 SAS 토큰](https://azure.microsoft.com/blog/sas-update-account-sas-now-supports-all-storage-services/)(`ss='bt'`) 컨테이너 및 개체에 적용됩니다(`srt='co'`). 추가, 만들기, 나열, 업데이트, 쓰기 권한을 부여합니다(`sp='acluw'`). 앞에 물음표(?)를 포함하지 *마세요*.
mdsdHttpProxy | (선택 사항) 지정된 스토리지 계정 및 엔드포인트에 연결하기 위해 확장에 필요한 HTTP 프록시 정보입니다.
sinksConfig | (선택 사항) 메트릭 및 이벤트를 전달할 수 있는 대체 대상의 세부 정보입니다. 다음 섹션에서는 확장에서 지원되는 각 데이터 싱크에 관한 세부 정보를 다룹니다.

ARM 템플릿 내에서 SAS 토큰을 가져오려면 `listAccountSas` 함수를 사용합니다. 예제 템플릿은 [목록 함수 예제](../../azure-resource-manager/templates/template-functions-resource.md#list-example)를 참조하세요.

Azure Portal을 통해 필요한 SAS 토큰을 생성할 수 있습니다.

1. 확장에서 쓰게 할 범용 스토리지 계정을 선택합니다.
1. 왼쪽 메뉴의 **설정** 에서 **공유 액세스 서명** 을 선택합니다.
1. 앞에서 설명한 대로 항목을 선택합니다.
1. **SAS 생성** 을 선택합니다.

:::image type="content" source="./media/diagnostics-linux/make_sas.png" alt-text="SAS 생성 단추가 있는 공유 액세스 서명 페이지를 보여 주는 스크린샷":::

생성된 SAS를 `storageAccountSasToken` 필드에 복사합니다. 앞의 물음표(?)를 제거합니다.

### <a name="sinksconfig"></a>sinksConfig

```json
"sinksConfig": {
    "sink": [
        {
            "name": "sinkname",
            "type": "sinktype",
            ...
        },
        ...
    ]
},
```

선택 사항인 `sinksConfig` 섹션은 확장에서 수집한 정보를 전송할 추가적인 대상을 정의합니다. `sink` 배열에는 각 추가 데이터 싱크에 대한 개체가 포함되어 있습니다. `type` 특성은 개체의 다른 특성을 결정합니다.

요소 | 값
------- | -----
name | 확장 구성의 다른 위치에서 이 싱크를 참조하는 문자열입니다.
type | 정의 중인 싱크 유형입니다. 이 유형의 인스턴스에서 다른 값(있는 경우)을 결정합니다.

LAD 3.0 버전에서는 `EventHub` 및 `JsonBlob`의 두 가지 싱크 형식을 지원합니다.

#### <a name="eventhub-sink"></a>EventHub 싱크

```json
"sink": [
    {
        "name": "sinkname",
        "type": "EventHub",
        "sasURL": "https SAS URL"
    },
    ...
]
```

`"sasURL"` 항목에는 SAS 토큰을 포함하여 데이터를 게시해야 하는 이벤트 허브에 대한 전체 URL이 포함됩니다. LAD는 클레임 보내기를 사용하도록 설정하는 SAS 명명 정책이 필요합니다. 

예를 들면 다음과 같습니다.

* `contosohub`라는 Azure Event Hubs 네임스페이스를 만듭니다.
* `syslogmsgs`라는 네임스페이스에 이벤트 허브를 만듭니다.
* 이벤트 허브에 클레임 보내기를 사용하도록 설정하는 공유 액세스 정책을 만듭니다. 정책 이름을 `writer`로 지정합니다.

SAS가 UTC 기준 2018년 1월 1일 자정까지 작동하는 경우, sasURL 값은 다음 예제와 같을 수 있습니다.

```https
https://contosohub.servicebus.windows.net/syslogmsgs?sr=contosohub.servicebus.windows.net%2fsyslogmsgs&sig=xxxxxxxxxxxxxxxxxxxxxxxxx&se=1514764800&skn=writer
```

Event Hubs에 대한 SAS 토큰을 생성하고 검색하는 방법에 대한 자세한 내용은 [SAS 토큰 생성](/rest/api/eventhub/generate-sas-token#powershell)을 참조하세요.

#### <a name="jsonblob-sink"></a>JsonBlob 싱크

```json
"sink": [
    {
        "name": "sinkname",
        "type": "JsonBlob"
    },
    ...
]
```

`JsonBlob` 싱크로 전달되는 데이터는 Azure Storage의 Blob에 저장됩니다. 각 LAD 인스턴스는 매시간 각 싱크 이름에 대한 Blob을 만듭니다. 각 Blob에는 항상 구문상 유효한 JSON 배열의 개체가 포함되어 있습니다. 새 항목이 배열에 원자 단위로 추가됩니다. 

Blob은 싱크와 동일한 이름을 가진 컨테이너에 저장됩니다. Blob 컨테이너 이름에 대한 Azure Storage 규칙은 `JsonBlob` 싱크 이름에 적용됩니다. 이름은 3~63자여야 하며, 소문자로 된 영숫자 ASCII 문자 또는 대시로 구성되어야 합니다.

## <a name="public-settings"></a>공용 설정

이 퍼블릭 설정 구조체는 확장에서 수집한 정보를 제어하는 다양한 설정 블록을 포함합니다. 각 설정은 선택 사항입니다. `ladCfg`를 지정할 경우 `StorageAccount`도 지정해야 합니다.

```json
{
    "ladCfg":  { ... },
    "perfCfg": { ... },
    "fileLogs": { ... },
    "StorageAccount": "the storage account to receive data",
    "mdsdHttpProxy" : ""
}
```

요소 | 값
------- | -----
StorageAccount | 확장에 의해 데이터가 기록될 스토리지 계정의 이름입니다. [보호 설정](#protected-settings)에서 지정된 이름이어야 합니다.
mdsdHttpProxy | (선택 사항) [보호 설정](#protected-settings)에서와 동일해야 합니다. 퍼블릭 값은 프라이빗 값(설정된 경우)으로 재정의됩니다. [보호 설정](#protected-settings)에서 비밀(예: 암호)을 포함하는 프록시 설정을 배치합니다.

다음 섹션에서는 나머지 요소에 대한 세부 정보를 제공합니다.

### <a name="ladcfg"></a>ladCfg

```json
"ladCfg": {
    "diagnosticMonitorConfiguration": {
        "eventVolume": "Medium",
        "metrics": { ... },
        "performanceCounters": { ... },
        "syslogEvents": { ... }
    },
    "sampleRateInSeconds": 15
}
```

`ladCfg` 구조체는 선택 사항입니다. 이 구조체는 Azure Monitor Metrics 서비스 및 기타 데이터 싱크에 전달되는 메트릭과 로그의 수집을 제어합니다. 다음을 지정해야 합니다.

* `performanceCounters`와 `syslogEvents`중 하나 또는 둘 다 
* `metrics` 구조체

요소 | 값
------- | -----
eventVolume | (선택 사항) 스토리지 테이블 내에서 만든 파티션의 수를 제어합니다. `"Large"`, `"Medium"` 또는 `"Small"`이어야 합니다. 값을 지정하지 않으면 기본값 `"Medium"`이 사용됩니다.
sampleRateInSeconds | (선택 사항) 원시(집계되지 않은) 메트릭 컬렉션 간의 기본 간격입니다. 지원되는 가장 작은 샘플 속도는 15초입니다. 값을 지정하지 않으면 기본값 `15`가 사용됩니다.

#### <a name="metrics"></a>메트릭

```json
"metrics": {
    "resourceId": "/subscriptions/...",
    "metricAggregation" : [
        { "scheduledTransferPeriod" : "PT1H" },
        { "scheduledTransferPeriod" : "PT5M" }
    ]
}
```

요소 | 값
------- | -----
resourceId | VM 또는 VM이 속한 확장 집합의 Azure Resource Manager 리소스 ID입니다. `JsonBlob` 싱크가 구성에 사용되는 경우 이 설정도 지정해야 합니다.
scheduledTransferPeriod | 집계 메트릭이 계산되어 Azure Monitor Metrics로 전송되는 빈도입니다. 빈도는 IS 8601 시간 간격으로 표시됩니다. 최소 전송 기간은 60초, 즉 PT1M입니다. `scheduledTransferPeriod`를 하나 이상 지정하세요.

`performanceCounters` 섹션에 지정된 메트릭 샘플은 15초마다 또는 카운터에 명시적으로 정의된 샘플 속도로 수집됩니다. 예제와 같이 여러 `scheduledTransferPeriod` 빈도가 나타날 경우 각 집계는 독립적으로 계산됩니다.

#### <a name="performancecounters"></a>performanceCounters

```json
"performanceCounters": {
    "sinks": "",
    "performanceCounterConfiguration": [
        {
            "type": "builtin",
            "class": "Processor",
            "counter": "PercentIdleTime",
            "counterSpecifier": "/builtin/Processor/PercentIdleTime",
            "condition": "IsAggregate=TRUE",
            "sampleRate": "PT15S",
            "unit": "Percent",
            "annotation": [
                {
                    "displayName" : "Aggregate CPU %idle time",
                    "locale" : "en-us"
                }
            ]
        }
    ]
}
```

선택 사항인 `performanceCounters` 섹션은 메트릭 컬렉션을 제어합니다. 각 [`scheduledTransferPeriod`](#metrics)에 대해 원시 샘플이 집계되어 다음 값이 나타납니다.

* 평균
* 최소
* 최대
* 마지막으로 수집된 값
* 집계를 계산하는 데 사용되는 원시 샘플 수

요소 | 값
------- | -----
sinks | (선택 사항) LAD가 집계된 메트릭 결과를 보내는 쉼표로 구분된 싱크 이름 목록입니다. 모든 집계된 메트릭은 나열된 각 싱크에 게시됩니다. 예: `"EHsink1, myjsonsink"`. 자세한 내용은 [`sinksConfig`](#sinksconfig)을(를) 참조하세요.
type | 메트릭의 실제 공급자를 식별합니다.
class | `"counter"`와 함께 공급자의 네임스페이스 내에서 특정 메트릭을 식별합니다.
counter | `"class"`와 함께 공급자의 네임스페이스 내에서 특정 메트릭을 식별합니다.
counterSpecifier | Azure Monitor Metrics 네임스페이스 내에서 특정 메트릭을 식별합니다.
condition(조건) | (선택 사항) 메트릭이 적용되는 개체의 특정 인스턴스가 선택됩니다. 또는 해당 개체의 모든 인스턴스에 걸친 집계가 선택됩니다. 
sampleRate | 이 메트릭의 원시 샘플이 수집되는 속도를 설정하는 IS 8601 간격입니다. 값이 설정되지 않은 경우 컬렉션 간격은 [`sampleRateInSeconds`](#ladcfg)로 설정됩니다. 지원되는 최소 샘플 속도는 15초(PT15S)입니다.
단위 | 메트릭에 대한 단위를 정의합니다. `"Count"`, `"Bytes"`, `"Seconds"`, `"Percent"`, `"CountPerSecond"`, `"BytesPerSecond"`, `"Millisecond"` 문자열 중 하나여야 합니다. 수집된 데이터의 소비자는 수집된 데이터 값이 이 단위와 일치할 것으로 예상합니다. LAD는 이 필드를 무시합니다.
displayName | Azure Monitor 메트릭의 데이터에 연결할 레이블입니다. 이 레이블은 연결된 로캘 설정으로 지정된 언어로 표시됩니다. LAD는 이 필드를 무시합니다.

`counterSpecifier`는 임의 식별자입니다. Azure Portal 차트 및 경고 기능과 같이 메트릭의 소비자는 `counterSpecifier`를 메트릭 또는 메트릭 인스턴스를 식별하는 ‘키’로 사용합니다. 

`builtin` 메트릭에는 `/builtin/`으로 시작하는 `counterSpecifier` 값을 사용하는 것이 좋습니다. 특정 메트릭 인스턴스를 수집하는 경우 `counterSpecifier` 값에 인스턴스 식별자를 연결하는 것이 좋습니다. 

다음은 몇 가지 예입니다.

* `/builtin/Processor/PercentIdleTime` - 모든 vCPU의 평균 유휴 시간
* `/builtin/Disk/FreeSpace(/mnt)` - `/mnt` 파일 시스템의 사용 가능한 공간
* `/builtin/Disk/FreeSpace` - 탑재된 모든 파일 시스템에서 사용 가능한 평균 공간

LAD와 Azure Portal은 `counterSpecifier` 값과 일치하는 패턴이 없을 것으로 예상합니다. `counterSpecifier` 값을 구성하는 방식을 일관되게 유지합니다.

`performanceCounters`를 지정할 경우 LAD는 항상 Azure Storage의 테이블에 데이터를 작성합니다. JSON Blob 또는 Event Hubs 중 하나 또는 둘 다에 동일한 데이터를 작성할 수 있습니다. 그러나 데이터를 테이블에 저장을 사용할 수 없습니다. 

동일한 스토리지 계정 이름 및 엔드포인트를 사용하는 LAD의 모든 인스턴스는 해당 메트릭과 로그를 동일한 테이블에 추가합니다. 너무 많은 VM이 동일한 테이블 파티션에 작성할 경우 Azure는 해당 파티션에 쓰기를 제한할 수 있습니다. 

`eventVolume` 설정은 항목이 1(작음), 10(중간) 또는 100(큼)개의 파티션에 분산되도록 합니다. 일반적으로 중간 분량의 파티션은 트래픽 제한을 방지하기에 충분합니다. 

Azure Portal의 Azure Monitor Metrics 기능은 이 테이블의 데이터를 사용하여 그래프를 생성하거나 경고를 트리거합니다. 테이블 이름은 다음 문자열이 연결된 것입니다.

* `WADMetrics`
* 테이블에 저장된 집계 값에 대한 `"scheduledTransferPeriod"`
* `P10DV2S`
* 10일마다 변경되는 "YYYYMMDD" 형식의 날짜

예에는 `WADMetricsPT1HP10DV2S20170410` 및 `WADMetricsPT1MP10DV2S20170609`가 포함됩니다.

#### <a name="syslogevents"></a>syslogEvents

```json
"syslogEvents": {
    "sinks": "",
    "syslogEventConfiguration": {
        "facilityName1": "minSeverity",
        "facilityName2": "minSeverity",
        ...
    }
}
```

선택 사항인 `syslogEvents` 섹션은 syslog의 로그 이벤트 컬렉션을 제어합니다. 섹션을 생략할 경우 syslog 이벤트는 전혀 캡처되지 않습니다.

`syslogEventConfiguration` 컬렉션에는 해당하는 각 syslog 기능에 대한 항목이 하나씩 있습니다. 특정 기능의 `minSeverity`가 `"NONE"`이거나 요소에 해당 기능이 아예 나타나지 않는 경우, 해당 기능의 이벤트가 캡처되지 않습니다.

요소 | 값
------- | -----
sinks | 개별 로그 이벤트가 게시되는 쉼표로 구분된 싱크 이름 목록입니다. `syslogEventConfiguration`의 제한 사항과 일치하는 모든 로그 이벤트가 나열된 각 싱크에 게시됩니다. 예: `"EHforsyslog"`
facilityName | `"LOG_USER"` 또는 `"LOG\LOCAL0"`와 같은 syslog 기능 이름입니다. 자세한 내용은 [syslog 기본 페이지](http://man7.org/linux/man-pages/man3/syslog.3.html)의 “기능” 섹션을 참조하세요.
minSeverity | `"LOG_ERR"` 또는 `"LOG_INFO"`와 같은 syslog 심각도 수준입니다. 자세한 내용은 [syslog 기본 페이지](http://man7.org/linux/man-pages/man3/syslog.3.html)의 “수준” 섹션을 참조하세요. 확장은 지정된 수준 이상으로 전송되는 이벤트를 캡처합니다.

`syslogEvents`를 지정할 경우 LAD는 항상 Azure Storage의 테이블에 데이터를 작성합니다. JSON Blob 또는 Event Hubs 중 하나 또는 둘 다에 동일한 데이터를 작성할 수 있습니다. 그러나 데이터를 테이블에 저장을 사용할 수 없습니다. 

이 테이블에 대한 분할 동작은 `performanceCounters`의 경우와 동일합니다. 테이블 이름은 다음 문자열이 연결된 것입니다.

* `LinuxSyslog`
* 10일마다 변경되는 "YYYYMMDD" 형식의 날짜

예에는 `LinuxSyslog20170410` 및 `LinuxSyslog20170609`가 포함됩니다.

### <a name="perfcfg"></a>perfCfg

`perfCfg` 섹션은 선택 사항입니다. 임의의 [OMI(Open Management Infrastructure)](https://github.com/Microsoft/omi) 쿼리 실행을 제어합니다.

```json
"perfCfg": [
    {
        "namespace": "root/scx",
        "query": "SELECT PercentAvailableMemory, PercentUsedSwap FROM SCX_MemoryStatisticalInformation",
        "table": "LinuxOldMemory",
        "frequency": 300,
        "sinks": ""
    }
]
```

요소 | 값
------- | -----
네임스페이스 | (선택 사항) 실행해야 할 쿼리 내의 OMI 네임스페이스입니다. 지정하지 않으면 기본값 `"root/scx"`가 사용됩니다. 이는 [System Center 플랫폼 간 공급자](https://github.com/Microsoft/SCXcore)에 의해 실행됩니다.
Query | 실행할 OMI 쿼리
테이블 | (선택 사항) 지정된 스토리지 계정의 Azure Storage 테이블입니다. 자세한 내용은 [보호된 설정](#protected-settings)을 참조하세요.
frequency | (선택 사항) 쿼리 실행 사이의 시간(초)입니다. 기본값은 300(5분)입니다. 최솟값은 15초입니다.
sinks | (선택 사항) 원시 샘플 메트릭 결과가 게시되어야 하는 쉼표로 구분된 추가적인 싱크 이름 목록입니다. 원시 샘플의 집계는 확장 또는 Azure Monitor Metrics에서 계산되지 않습니다.

`"table"` 및 `"sinks"` 중 하나 또는 둘 다 지정해야 합니다.

### <a name="filelogs"></a>fileLogs

`fileLogs` 섹션은 로그 파일의 캡처를 제어합니다. LAD는 파일에 작성된 새 텍스트 줄을 캡처합니다. 새 텍스트 줄을 테이블 행 및/또는 지정된 싱크(`JsonBlob` 또는 `EventHub`)에 기록합니다.

> [!NOTE]
> `fileLogs`는 `omsagent`라는 LAD의 하위 구성 요소에 의해 캡처됩니다. `fileLogs`를 수집하려면 `omsagent` 사용자에게 지정한 파일에 대한 읽기 권한이 있는지 확인합니다. 사용자에게는 해당 파일의 경로에 있는 모든 디렉터리에 대한 실행 권한도 있어야 합니다. LAD가 설치되면 `sudo su omsagent -c 'cat /path/to/file'`을 실행하여 권한을 확인할 수 있습니다.

```json
"fileLogs": [
    {
        "file": "/var/log/mydaemonlog",
        "table": "MyDaemonEvents",
        "sinks": ""
    }
]
```

요소 | 값
------- | -----
파일 | 확인 및 캡처할 로그 파일의 전체 경로 이름입니다. 경로 이름은 단일 파일의 이름을 지정해야 합니다. 디렉터리의 이름을 지정하거나 와일드카드 문자를 포함할 수 없습니다. `omsagent` 사용자 계정에는 파일 경로에 대한 읽기 권한이 있어야 합니다.
테이블 | (선택 사항) 파일의 ‘테일’에서 가져온 새로운 줄을 작성할 Azure Storage 테이블입니다. 테이블은 보호된 구성 지정된 스토리지 계정에 있어야 합니다. 
sinks | (선택 사항) 로그 줄이 전송되는 쉼표로 구분된 추가적인 싱크 이름 목록입니다.

`"table"` 및 `"sinks"` 중 하나 또는 둘 다 지정해야 합니다.

## <a name="metrics-supported-by-the-builtin-provider"></a>기본 제공 공급자가 지원하는 메트릭

`builtin` 메트릭 공급자는 광범위한 사용자에게 가장 흥미로운 메트릭의 원본입니다. 이러한 메트릭은 다음과 같은 다섯 가지 광범위한 클래스에 속합니다.

* 프로세서
* 메모리
* 네트워크
* 파일 시스템
* 디스크

### <a name="builtin-metrics-for-the-processor-class"></a>프로세서 클래스의 기본 제공 메트릭

메트릭의 프로세서 클래스는 VM의 프로세서 사용량에 대한 정보를 제공합니다. 백분율이 집계되면 결과는 모든 CPU의 평균입니다. 

2-vCPU VM에서 하나의 vCPU가 100% 사용 중이고 다른 vCPU가 100% 유휴 상태인 경우 보고된 `PercentIdleTime` 값은 50입니다. 같은 기간 동안 각 vCPU가 50% 사용 중인 경우에도 보고되는 결과는 50입니다. 4-vCPU VM에서 하나의 vCPU가 100% 사용 중이고 다른 vCPU가 유휴 상태인 경우 보고되는 `PercentIdleTime`은 75입니다.

카운터 | 의미
------- | -------
PercentIdleTime | 집계 기간 중 프로세서가 커널 유휴 루프를 실행한 시간의 백분율
PercentProcessorTime | 유휴 상태가 아닌 스레드를 실행하는 시간의 백분율
PercentIOWaitTime | IO 작업이 완료될 때까지 기다리는 시간의 백분율
PercentInterruptTime | 하드웨어 또는 소프트웨어 인터럽트 및 DPC(지연된 프로시저 호출)를 실행하는 시간의 백분율
PercentUserTime | 집계 기간 중 비 유휴 시간 동안 일반 우선 순위에서 사용자 모드에서 소요된 시간의 백분율
PercentNiceTime | 비 유휴 시간 동안 낮은(좋은) 우선 순위에서 소요된 시간의 백분율
PercentPrivilegedTime | 비 유휴 시간 동안 특권(커널) 모드에서 소요된 시간의 백분율

처음 4개 카운터의 합계가 100%여야 합니다. 마지막 3개 카운터의 합계도 100%입니다. 마지막 3개 카운터는 `PercentProcessorTime`, `PercentIOWaitTime`, `PercentInterruptTime`의 합계를 나눕니다.

모든 프로세서에서 단일 메트릭을 집계하려면 `"condition": "IsAggregate=TRUE"`로 설정합니다. 특정 프로세서(예: 4 vCPU VM의 두 번째 논리 프로세서)에 대한 메트릭을 얻으려면 `"condition": "Name=\\"1\\""`를 설정합니다. 논리 프로세서 수는 `[0..n-1]` 범위에 있습니다.

### <a name="builtin-metrics-for-the-memory-class"></a>메모리 클래스의 기본 제공 메트릭

메트릭의 Memory 클래스는 메모리 사용률, 페이징, 스와핑에 관한 정보를 제공합니다.

카운터 | 의미
------- | -------
AvailableMemory | 사용 가능한 실제 메모리(MiB)
PercentAvailableMemory | 총 메모리 중 사용 가능한 실제 메모리의 백분율
UsedMemory | 사용 중인 실제 메모리(MiB)
PercentUsedMemory | 총 메모리 중 사용 중인 실제 메모리의 백분율
PagesPerSec | 총 페이징(읽기/쓰기)
PagesReadPerSec | 백업 저장소에서 읽어온 페이지(예: 스왑 파일, 프로그램 파일, 매핑된 파일)
PagesWrittenPerSec | 백업 저장소에 기록된 페이지(예: 스왑 파일, 매핑된 파일)
AvailableSwap | 사용하지 않는 스왑 공간(MiB)
PercentAvailableSwap | 총 스왑 중 사용되지 않는 스왑 공간의 백분율
UsedSwap | 사용 중인 스왑 공간(MiB)
PercentUsedSwap | 총 스왑 중 사용 중인 스왑 공간의 백분율

이 메트릭 클래스에는 인스턴스가 하나만 있습니다. `"condition"` 특성에는 유용한 설정이 없으며 생략해야 합니다.

### <a name="builtin-metrics-for-the-network-class"></a>네트워크 클래스의 기본 제공 메트릭

메트릭의 네트워크 클래스는 시작 이후 개별 네트워크 인터페이스의 네트워크 활동에 대한 정보를 제공합니다. 

LAD는 대역폭 메트릭을 노출하지 않습니다. 호스트 메트릭에서 해당 메트릭을 가져올 수 있습니다.

카운터 | 의미
------- | -------
BytesTransmitted | 시작 이후 전송한 총 바이트 수
BytesReceived | 시작 이후 수신한 총 바이트 수
BytesTotal | 시작 이후 전송하거나 수신한 총 바이트 수
PacketsTransmitted | 시작 이후 전송한 총 패킷
PacketsReceived | 시작 이후 수신한 총 패킷
TotalRxErrors | 시작 이후 수신 오류 건수
TotalTxErrors | 시작 이후 전송 오류 건수
TotalCollisions | 시작 이후 네트워크 포트에서 보고된 충돌 건수

네트워크 클래스가 인스턴스화되더라도 LAD는 모든 네트워크 디바이스에서 집계되는 네트워크 메트릭을 캡처하지 않습니다. 특정 인터페이스(예: eth0)에 대한 메트릭을 가져오려면 `"condition": "InstanceID=\\"eth0\\""`를 설정합니다.

### <a name="builtin-metrics-for-the-file-system-class"></a>파일 시스템 클래스의 기본 제공 메트릭

메트릭의 파일 시스템 클래스는 파일 시스템 사용량에 대한 정보를 제공합니다. 절대값과 백분율 값은 루트가 아닌 일반 사용자에게 표시되는 것으로 보고됩니다.

카운터 | 의미
------- | -------
FreeSpace | 사용 가능한 디스크 공간(바이트)
UsedSpace | 사용된 디스크 공간(바이트)
PercentFreeSpace | 사용 가능한 공간의 백분율
PercentUsedSpace | 사용된 공간의 백분율
PercentFreeInodes | 사용되지 않는 인덱스 노드(inode)의 백분율
PercentUsedInodes | 모든 파일 시스템에서 합한 할당된(사용 중인) inode의 백분율
BytesReadPerSecond | 초당 읽은 바이트
BytesWrittenPerSecond | 초당 쓴 바이트
초당 바이트 수 | 초당 읽거나 쓴 바이트
ReadsPerSecond | 초당 읽기 작업
WritesPerSecond | 초당 쓰기 작업
TransfersPerSecond | 초당 읽기 또는 쓰기 작업

`"condition": "IsAggregate=True"`로 설정하면 모든 파일 시스템에서 집계된 값을 얻을 수 있습니다. `"condition": 'Name="/mnt"'`로 설정하면 특정 탑재된 파일 시스템(예: `"/mnt"`)에 대한 값을 얻을 수 있습니다. 

> [!NOTE]
> JSON 대신 Azure Portal에서 작업하는 경우 조건 필드 형식은 `Name='/mnt'` 입니다.

### <a name="builtin-metrics-for-the-disk-class"></a>디스크 클래스의 기본 제공 메트릭

메트릭의 디스크 클래스는 디스크 디바이스 사용량에 대한 정보를 제공합니다. 이러한 통계는 전체 드라이브에 적용됩니다. 

한 개의 디바이스에 여러 파일 시스템이 있는 경우 해당 디바이스의 카운터가 모든 파일 시스템에 걸쳐 효과적으로 집계됩니다.

카운터 | 의미
------- | -------
ReadsPerSecond | 초당 읽기 작업
WritesPerSecond | 초당 쓰기 작업
TransfersPerSecond | 초당 총 작업
AverageReadTime | 읽기 작업당 평균 시간(초)
AverageWriteTime | 쓰기 작업당 평균 시간(초)
AverageTransferTime | 작업당 평균 시간(초)
AverageDiskQueueLength | 대기 중인 디스크 작업의 평균 수
ReadBytesPerSecond | 초당 읽은 바이트 수
WriteBytesPerSecond | 초당 쓴 바이트 수
초당 바이트 수 | 초당 읽거나 쓴 바이트 수

`"condition": "IsAggregate=True"`로 설정하면 모든 디스크에서 집계된 값을 얻을 수 있습니다. 특정 디바이스(예: `/dev/sdf1`)에 대한 정보를 얻으려면 `"condition": "Name=\\"/dev/sdf1\\""`로 설정합니다.

## <a name="install-and-configure-lad-30"></a>LAD 3.0 설치 및 구성

### <a name="azure-cli"></a>Azure CLI

보호 설정이 *ProtectedSettings.json* 파일에 있고 퍼블릭 구성 정보가 *PublicSettings.json* 파일에 있는 경우 다음 명령을 실행합니다.

```azurecli
az vm extension set --publisher Microsoft.Azure.Diagnostics --name LinuxDiagnostic --version 3.0 --resource-group <resource_group_name> --vm-name <vm_name> --protected-settings ProtectedSettings.json --settings PublicSettings.json
```

해당 명령은 Azure CLI의 Azure Resource Manager 모드를 사용하고 있다고 가정합니다. 클래식 배포 모델 VM에 대한 LAD를 구성하려면 “asm” 모드(`azure config mode asm`)로 전환하고 명령에서 리소스 그룹 이름을 생략합니다. 

자세한 내용은 [플랫폼 간 CLI 설명서](/cli/azure/authenticate-azure-cli)를 참조하세요.

### <a name="powershell"></a>PowerShell

보호 설정이 `$protectedSettings` 변수에 있고 퍼블릭 구성 정보가 `$publicSettings` 변수에 있는 경우 다음 명령을 실행합니다.

```powershell
Set-AzVMExtension -ResourceGroupName <resource_group_name> -VMName <vm_name> -Location <vm_location> -ExtensionType LinuxDiagnostic -Publisher Microsoft.Azure.Diagnostics -Name LinuxDiagnostic -SettingString $publicSettings -ProtectedSettingString $protectedSettings -TypeHandlerVersion 3.0
```

## <a name="example-lad-30-configuration"></a>LAD 3.0 구성 예

이전 정의에 따라, 본 섹션에서는 LAD 3.0 확장 구성 샘플과 설명을 제공합니다. 이 샘플을 사례에 적용하려면 사용자 고유의 스토리지 계정 이름, 계정 SAS 토큰, Event Hubs SAS 토큰을 사용하세요.

> [!NOTE]
> LAD를 설치하기 위해 Azure CLI를 사용하는지 또는 PowerShell을 사용하는지 여부에 따라 퍼블릭 및 보호 설정을 제공하는 방법이 달라집니다. 
>
> * Azure CLI를 사용하는 경우 위의 샘플 명령을 사용하려면 다음 설정을 *ProtectedSettings.json* 및 *PublicSettings.json* 에 저장합니다. 
> * PowerShell을 사용하는 경우 `$protectedSettings = '{ ... }'`를 실행하여 `$protectedSettings` 및 `$publicSettings`에 다음 설정을 저장합니다.

### <a name="protected-settings"></a>보호 설정

보호 설정은 다음을 구성합니다.

* 스토리지 계정
* 일치하는 계정 SAS 토큰
* 여러 싱크(SAS 토큰이 있는 `JsonBlob` 또는 `EventHub`)

```json
{
  "storageAccountName": "yourdiagstgacct",
  "storageAccountSasToken": "sv=xxxx-xx-xx&ss=bt&srt=co&sp=wlacu&st=yyyy-yy-yyT21%3A22%3A00Z&se=zzzz-zz-zzT21%3A22%3A00Z&sig=fake_signature",
  "sinksConfig": {
    "sink": [
      {
        "name": "SyslogJsonBlob",
        "type": "JsonBlob"
      },
      {
        "name": "FilelogJsonBlob",
        "type": "JsonBlob"
      },
      {
        "name": "LinuxCpuJsonBlob",
        "type": "JsonBlob"
      },
      {
        "name": "MyJsonMetricsBlob",
        "type": "JsonBlob"
      },
      {
        "name": "LinuxCpuEventHub",
        "type": "EventHub",
        "sasURL": "https://youreventhubnamespace.servicebus.windows.net/youreventhubpublisher?sr=https%3a%2f%2fyoureventhubnamespace.servicebus.windows.net%2fyoureventhubpublisher%2f&sig=fake_signature&se=1808096361&skn=yourehpolicy"
      },
      {
        "name": "MyMetricEventHub",
        "type": "EventHub",
        "sasURL": "https://youreventhubnamespace.servicebus.windows.net/youreventhubpublisher?sr=https%3a%2f%2fyoureventhubnamespace.servicebus.windows.net%2fyoureventhubpublisher%2f&sig=yourehpolicy&skn=yourehpolicy"
      },
      {
        "name": "LoggingEventHub",
        "type": "EventHub",
        "sasURL": "https://youreventhubnamespace.servicebus.windows.net/youreventhubpublisher?sr=https%3a%2f%2fyoureventhubnamespace.servicebus.windows.net%2fyoureventhubpublisher%2f&sig=yourehpolicy&se=1808096361&skn=yourehpolicy"
      }
    ]
  }
}
```

### <a name="public-settings"></a>공용 설정

퍼블릭 설정으로 LAD는 다음을 수행합니다.

* 프로세서 시간 비율 메트릭 및 사용된 디스크 공간 메트릭을 `WADMetrics*` 테이블에 업로드합니다.
* syslog 기능 `"user"` 및 심각도 `"info"`의 메시지를 `LinuxSyslog*` 테이블에 업로드합니다.
* 원시 OMI 쿼리 결과(`PercentProcessorTime` 및 `PercentIdleTime`)를 명명된 `LinuxCPU` 테이블에 업로드합니다.
* `/var/log/myladtestlog` 파일에서 추가된 줄을 `MyLadTestLog` 테이블에 업로드합니다.

각각의 경우 데이터는 다음으로 업로드됩니다.

* Azure Blob Storage. 컨테이너 이름은 `JsonBlob` 싱크에 정의되어 있습니다.
* `EventHub` 싱크에 지정된 Event Hubs 엔드포인트입니다.

```json
{
  "StorageAccount": "yourdiagstgacct",
  "ladCfg": {
    "sampleRateInSeconds": 15,
    "diagnosticMonitorConfiguration": {
      "performanceCounters": {
        "sinks": "MyMetricEventHub,MyJsonMetricsBlob",
        "performanceCounterConfiguration": [
          {
            "unit": "Percent",
            "type": "builtin",
            "counter": "PercentProcessorTime",
            "counterSpecifier": "/builtin/Processor/PercentProcessorTime",
            "annotation": [
              {
                "locale": "en-us",
                "displayName": "Aggregate CPU %utilization"
              }
            ],
            "condition": "IsAggregate=TRUE",
            "class": "Processor"
          },
          {
            "unit": "Bytes",
            "type": "builtin",
            "counter": "UsedSpace",
            "counterSpecifier": "/builtin/FileSystem/UsedSpace",
            "annotation": [
              {
                "locale": "en-us",
                "displayName": "Used disk space on /"
              }
            ],
            "condition": "Name=\"/\"",
            "class": "Filesystem"
          }
        ]
      },
      "metrics": {
        "metricAggregation": [
          {
            "scheduledTransferPeriod": "PT1H"
          },
          {
            "scheduledTransferPeriod": "PT1M"
          }
        ],
        "resourceId": "/subscriptions/your_azure_subscription_id/resourceGroups/your_resource_group_name/providers/Microsoft.Compute/virtualMachines/your_vm_name"
      },
      "eventVolume": "Large",
      "syslogEvents": {
        "sinks": "SyslogJsonBlob,LoggingEventHub",
        "syslogEventConfiguration": {
          "LOG_USER": "LOG_INFO"
        }
      }
    }
  },
  "perfCfg": [
    {
      "query": "SELECT PercentProcessorTime, PercentIdleTime FROM SCX_ProcessorStatisticalInformation WHERE Name='_TOTAL'",
      "table": "LinuxCpu",
      "frequency": 60,
      "sinks": "LinuxCpuJsonBlob,LinuxCpuEventHub"
    }
  ],
  "fileLogs": [
    {
      "file": "/var/log/myladtestlog",
      "table": "MyLadTestLog",
      "sinks": "FilelogJsonBlob,LoggingEventHub"
    }
  ]
}
```

구성에서 `resourceId`는 VM 또는 가상 머신 확장 집합의 resourceId와 일치해야 합니다.

* Azure 플랫폼 메트릭 차트 및 경고는 작업 중인 VM의 `resourceId`를 파악하고 있습니다. `resourceId` 조회 키를 사용하여 VM에 관한 데이터를 찾을 것으로 예상합니다.
* Azure 자동 스케일링을 사용하는 경우 자동 스케일링 구성의 `resourceId`는 LAD에서 사용하는 `resourceId`와 일치해야 합니다.
* `resourceId`는 LAD를 통해 작성된 JSON Blob의 이름에 포함됩니다.

## <a name="view-your-data"></a>데이터 보기

Azure Portal을 사용하여 성능 데이터를 보거나 경고를 설정합니다.

:::image type="content" source="./media/diagnostics-linux/graph_metrics.png" alt-text="Azure Portal을 보여 주는 스크린샷. 메트릭에 사용된 디스크 공간이 선택되어 있으며 결과 차트가 나타나 있습니다.":::

`performanceCounters` 데이터는 항상 Azure Storage 테이블에 저장됩니다. Azure Storage API는 다양한 언어 및 플랫폼에 사용할 수 있습니다.

`JsonBlob` 싱크로 전송된 데이터는 [보호 설정](#protected-settings)에서 명명된 스토리지 계정의 Blob에 저장됩니다. Azure Blob Storage API를 사용하여 Blob 데이터를 사용할 수 있습니다.

또한 다음 UI 도구를 사용하여 Azure Storage의 데이터에 액세스할 수 있습니다.

* Visual Studio 서버 Explorer
* [Azure Storage Explorer](https://azurestorageexplorer.codeplex.com/)

Azure Storage Explorer 세션의 다음 스크린샷은 테스트 VM에서 올바르게 구성된 LAD 3.0 확장에서 생성된 Azure Storage 테이블 및 컨테이너를 보여 줍니다. 이미지가 [샘플 LAD 3.0 구성](#example-lad-30-configuration)과 정확히 일치하지는 않습니다.

:::image type="content" source="./media/diagnostics-linux/stg_explorer.png" alt-text="Azure Storage Explorer를 보여 주는 스크린샷":::


Event Hubs 엔드포인트에 게시된 메시지를 사용하는 방법에 대한 자세한 내용은 관련 [Event Hubs 설명서](../../event-hubs/event-hubs-about.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

* [Azure Monitor](../../azure-monitor/alerts/alerts-classic-portal.md)에서 수집하는 메트릭에 대한 경고를 만듭니다.
* 메트릭에 대한 [모니터링 차트](../../azure-monitor/data-platform.md)를 만듭니다.
* 메트릭을 사용해 [가상 머신 확장 집합 만들기](../linux/tutorial-create-vmss.md)를 수행하여 자동 스케일링을 제어합니다.
