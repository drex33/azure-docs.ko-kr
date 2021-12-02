---
title: Azure VM 확장 및 Linux용 기능
description: Linux에서 제공하거나 개선한 항목별로 그룹화된 Azure 가상 시스템에 사용할 수 있는 확장 기능을 자세히 알아보십시오.
ms.topic: article
ms.service: virtual-machines
ms.subservice: extensions
author: amjads1
ms.author: amjads
ms.collection: linux
ms.date: 03/30/2018
ms.openlocfilehash: 8a87519b3fa69cb42bcfc25d5924505b19733820
ms.sourcegitcommit: 93c7420c00141af83ed3294923b4826dd4dc6ff2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/02/2021
ms.locfileid: "133436269"
---
# <a name="virtual-machine-extensions-and-features-for-linux"></a>Linux용 가상 머신 확장 및 기능

Azure VM(가상 머신) 확장은 Azure VM에서 배포 후 구성 및 자동화 태스크를 제공하는 작은 애플리케이션입니다. 예를 들어 가상 머신에 소프트웨어 설치, 바이러스 백신 보호 또는 내부 스크립트를 실행하는 기능이 필요한 경우 VM 확장을 사용할 수 있습니다. 

Azure CLI, PowerShell, ARM 템플릿(Azure Resource Manager 템플릿) 및 Azure Portal 사용하여 Azure VM 확장을 실행할 수 있습니다. 확장을 새 VM 배포와 번들로 묶거나 기존 시스템에 대해 실행할 수 있습니다.

이 문서에서는 Azure VM 확장의 개요, 확장을 사용하기 위한 필수 조건 및 이를 검색, 관리 및 제거하는 방법에 대한 지침을 제공합니다. 이 문서에서는 많은 VM 확장을 사용할 수 있기 때문에 일반화된 정보를 제공합니다. 각 구성에는 잠재적으로 고유한 구성과 자체 설명서가 있습니다. 

## <a name="use-cases-and-samples"></a>사용 사례 및 샘플

각 Azure VM 확장에는 특정 사용 사례가 있습니다. 예제는 다음과 같습니다.

- [Linux용 DSC 확장을](https://github.com/Azure/azure-linux-extensions/tree/master/DSC)사용하여 VM에 PowerShell DSC(원하는 상태 구성)를 적용합니다.
- Microsoft Monitoring Agent VM 확장을 사용하여 [VM 모니터링을](/previous-versions/azure/virtual-machines/linux/tutorial-monitor)구성합니다.
- [Chef](https://docs.chef.io/) 또는 [Datadog](https://www.datadoghq.com/blog/introducing-azure-monitoring-with-one-click-datadog-deployment/) 확장을 사용하여 Azure 인프라 모니터링을 구성합니다. 

프로세스 관련 확장 외에도 Windows 및 Linux 가상 머신에 대해 사용자 지정 스크립트 확장을 사용할 수 있습니다. [Linux용 사용자 지정 스크립트 확장을](custom-script-linux.md) 사용하면 모든 Bash 스크립트를 VM에서 실행할 수 있습니다. 사용자 지정 스크립트는 네이티브 Azure 도구로 제공할 수 있는 것 이상의 구성이 필요한 Azure 배포를 디자인할 때 유용합니다. 

## <a name="prerequisites"></a>사전 요구 사항

### <a name="azure-linux-agent"></a>Azure Linux 에이전트

VM에서 확장을 처리하려면 [Azure Linux 에이전트가](agent-linux.md) 설치되어 있어야 합니다. 일부 개별 확장에는 리소스에 대한 액세스 권한 또는 종속성 같은 필수 구성 요소가 있습니다.

Azure Linux 에이전트는 Azure VM과 Azure 패브릭 컨트롤러 간의 상호 작용을 관리합니다. 에이전트는 VM 확장 실행을 포함하여 Azure VM 배포 및 관리의 여러 기능적 측면을 담당합니다. 

Azure Linux 에이전트는 Azure Marketplace 이미지에 미리 설치됩니다. 지원되는 운영 체제에 수동으로 설치할 수도 있습니다.

에이전트는 여러 운영 체제에서 실행됩니다. 그러나 확장 프레임워크에는 확장에서 를 [사용하는 운영 체제에 대한 제한이 있습니다.](https://support.microsoft.com/en-us/help/4078134/azure-extension-supported-operating-systems) 일부 확장은 모든 운영 체제에서 지원되지 않으며 오류 코드 51("지원되지 않는 OS")을 내보낸다. 지원 가능성에 대한 개별 확장 설명서를 확인합니다.

### <a name="network-access"></a>네트워크 액세스

확장 패키지는 Azure Storage 확장 리포지토리에서 다운로드됩니다. 확장 상태 업로드는 Azure Storage 게시됩니다. 

[지원되는 버전의 Azure Linux 에이전트](https://support.microsoft.com/en-us/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support)를 사용하는 경우 VM 지역의 Azure Storage 대한 액세스를 허용할 필요가 없습니다. 에이전트를 사용하여 에이전트 통신을 위해 Azure 패브릭 컨트롤러로 통신을 리디렉션할 수 있습니다. 지원되지 않는 버전의 에이전트를 사용 중인 경우 VM에서 해당 지역의 Azure Storage 아웃바운드 액세스를 허용해야 합니다.

> [!IMPORTANT]
> 게스트 방화벽을 사용하여 개인 IP 주소 168.63.129.16에 대한 액세스를 차단한 경우 지원되는 버전의 에이전트를 사용하거나 아웃바운드 액세스를 구성한 경우에도 확장이 실패합니다.

에이전트는 확장 패키지 및 보고 상태를 다운로드하는 데 사용할 수 있습니다. 예를 들어 확장 설치가 GitHub(사용자 지정 스크립트 확장)에서 스크립트를 다운로드해야 하거나 Azure Storage(Azure Backup)에 액세스해야 하는 경우 추가 방화벽 또는 NSG(네트워크 보안 그룹) 포트를 열어야 합니다. 확장은 자체적인 권한의 애플리케이션이므로 서로 다른 요구 사항이 있습니다. Azure Storage 액세스해야 하는 확장의 경우 Azure NSG [서비스 태그를](../../virtual-network/network-security-groups-overview.md#service-tags)사용하여 액세스를 허용할 수 있습니다.

에이전트 트래픽 요청을 리디렉션하기 위해 Azure Linux 에이전트에는 프록시 서버 지원이 있습니다. 단, 이 프록시 서버 지원은 확장을 적용하지 않습니다. 프록시로 작동하려면 각 개별 확장을 구성해야 합니다.

## <a name="discover-vm-extensions"></a>VM 확장 검색

많은 VM 확장을 Azure VM에서 사용할 수 있습니다. 전체 목록을 보려면 [az vm extension image list](/cli/azure/vm/extension/image#az_vm_extension_image_list)를 사용합니다. 다음 예제에서는 *westus* 위치의 모든 사용 가능한 확장을 나열합니다.

```azurecli
az vm extension image list --location westus --output table
```

## <a name="run-vm-extensions"></a>VM 확장 실행

Azure VM 확장은 기존 VM에서 실행됩니다. 이는 이미 배포된 VM에서 구성을 변경하거나 연결을 복구해야 하는 경우에 유용합니다. VM 확장은 ARM 템플릿 배포와 함께 번들로 제공될 수도 있습니다. ARM 템플릿에서 확장을 사용하면 배포 후 개입 없이 Azure VM을 배포하고 구성할 수 있습니다.

다음 방법을 사용하여 기존 VM에 대해 확장을 실행할 수 있습니다.

### <a name="azure-cli"></a>Azure CLI

[az vm](/cli/azure/vm/extension#az_vm_extension_set) extension set 명령을 사용하여 기존 VM에 대해 Azure VM 확장을 실행할 수 있습니다. 다음 예에서는 *myResourceGroup* 이라는 리소스 그룹의 *myVM* 이라는 VM에 대해 사용자 지정 스크립트 확장을 실행합니다. 실행할 예제 리소스 그룹 이름, VM 이름 및 스크립트(https: \/ /raw.githubusercontent.com/me/project/hello.sh)를 사용자 고유의 정보로 바꿉니다. 

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name customScript \
  --publisher Microsoft.Azure.Extensions \
  --settings '{"fileUris": ["https://raw.githubusercontent.com/me/project/hello.sh"],"commandToExecute": "./hello.sh"}'
```

확장이 올바르게 실행되면 다음 예제와 비슷하게 출력됩니다.

```bash
info:    Executing command vm extension set
+ Looking up the VM "myVM"
+ Installing extension "CustomScript", VM: "mvVM"
info:    vm extension set command OK
```

### <a name="azure-portal"></a>Azure Portal

Azure Portal 통해 기존 VM에 VM 확장을 적용할 수 있습니다. 포털에서 VM을 선택하고 **확장을** 선택한 다음, **추가를** 선택합니다. 사용 가능한 확장 목록에서 원하는 확장을 선택하고 마법사의 지침을 따릅니다.

다음 이미지는 Azure Portal Linux용 사용자 지정 스크립트 확장 설치를 보여줍니다.

![Linux용 사용자 지정 스크립트 확장을 설치하는 대화 상자의 스크린샷.](./media/features-linux/installscriptextensionlinux.png)

### <a name="azure-resource-manager-templates"></a>Azure 리소스 관리자 템플릿

ARM 템플릿에 VM 확장을 추가하고 템플릿 배포를 사용하여 실행할 수 있습니다. 템플릿을 사용하여 확장을 배포할 때 완전히 구성된 Azure 배포를 만들 수 있습니다. 

예를 들어 다음 JSON은 부하가 분산된 VM 집합 및 Azure SQL 데이터베이스를 배포한 다음 각 VM에 .NET Core 애플리케이션을 설치하는 [전체 ARM 템플릿에서](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-linux) 가져옵니다. VM 확장은 소프트웨어 설치를 관리합니다.

```json
{
    "apiVersion": "2015-06-15",
    "type": "extensions",
    "name": "config-app",
    "location": "[resourceGroup().location]",
    "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', concat(variables('vmName'),copyindex()))]"
    ],
    "tags": {
    "displayName": "config-app"
    },
    "properties": {
    "publisher": "Microsoft.Azure.Extensions",
    "type": "CustomScript",
    "typeHandlerVersion": "2.0",
    "autoUpgradeMinorVersion": true,
    "settings": {
        "fileUris": [
        "https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/scripts/config-music.sh"
        ]
    },
    "protectedSettings": {
        "commandToExecute": "[concat('sudo sh config-music.sh ',variables('musicStoreSqlName'), ' ', parameters('adminUsername'), ' ', parameters('sqlAdminPassword'))]"
    }
    }
}
```

ARM 템플릿을 만드는 자세한 내용은 [Azure Resource Manager 템플릿의 가상 머신을](../windows/template-description.md#extensions)참조하세요.

## <a name="help-secure-vm-extension-data"></a>VM 확장 데이터 보호 지원

VM 확장을 실행하는 경우 자격 증명, 스토리지 계정 이름 및 액세스 키와 같은 중요한 정보를 포함해야 할 수 있습니다. 많은 VM 확장에는 데이터를 암호화하고 대상 VM 내에서만 해독하는 보호된 구성이 포함되어 있습니다. 각 확장에는 보호되는 특정 구성 스키마가 있으며 각각은 확장 관련 설명서에 자세히 나와 있습니다.

다음 예제에서는 Linux용 사용자 지정 스크립트 확장의 인스턴스를 보여 줍니다. 실행할 명령에는 자격 증명 집합이 포함됩니다. 이 예제에서는 실행할 명령이 암호화되지 않습니다.

```json
{
  "apiVersion": "2015-06-15",
  "type": "extensions",
  "name": "config-app",
  "location": "[resourceGroup().location]",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', concat(variables('vmName'),copyindex()))]"
  ],
  "tags": {
    "displayName": "config-app"
  },
  "properties": {
    "publisher": "Microsoft.Azure.Extensions",
    "type": "CustomScript",
    "typeHandlerVersion": "2.0",
    "autoUpgradeMinorVersion": true,
    "settings": {
      "fileUris": [
        "https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/scripts/config-music.sh"
      ],
      "commandToExecute": "[concat('sudo sh config-music.sh ',variables('musicStoreSqlName'), ' ', parameters('adminUsername'), ' ', parameters('sqlAdminPassword'))]"
    }
  }
}
```

속성을 `commandToExecute` `protected` 구성으로 이동하면 다음 예제와 같이 실행 문자열을 보호할 수 있습니다.

```json
{
  "apiVersion": "2015-06-15",
  "type": "extensions",
  "name": "config-app",
  "location": "[resourceGroup().location]",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', concat(variables('vmName'),copyindex()))]"
  ],
  "tags": {
    "displayName": "config-app"
  },
  "properties": {
    "publisher": "Microsoft.Azure.Extensions",
    "type": "CustomScript",
    "typeHandlerVersion": "2.0",
    "autoUpgradeMinorVersion": true,
    "settings": {
      "fileUris": [
        "https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/scripts/config-music.sh"
      ]
    },
    "protectedSettings": {
      "commandToExecute": "[concat('sudo sh config-music.sh ',variables('musicStoreSqlName'), ' ', parameters('adminUsername'), ' ', parameters('sqlAdminPassword'))]"
    }
  }
}
```

### <a name="how-agents-and-extensions-are-updated"></a>에이전트 및 확장을 업데이트하는 방법

에이전트 및 확장은 동일한 자동 업데이트 메커니즘을 공유합니다.

업데이트를 사용할 수 있고 자동 업데이트를 사용하도록 설정하면 확장이 변경된 후에 또는 다음과 같은 다른 VM 모델이 변경된 후에만 업데이트가 VM에 설치됩니다.

- 데이터 디스크
- 확장
- 부팅 진단 컨테이너
- 게스트 OS 암호
- VM 크기
- 네트워크 프로필

게시자는 다양한 시간에 지역에서 업데이트를 사용할 수 있도록 하므로 다른 버전의 다른 지역에 VM이 있을 수 있습니다.

> [!NOTE]
> 일부 업데이트에는 추가 방화벽 규칙이 필요할 수 있습니다. [네트워크 액세스를](#network-access)참조하세요.

#### <a name="agent-updates"></a>에이전트 업데이트

Linux VM 에이전트에는 *프로비전 에이전트 코드와* *확장 처리 코드가* 하나의 패키지에 포함되어 있습니다. 구분할 수 없습니다. 

[cloud-init 를 사용하여 Azure에서 프로비저닝하려는 경우 프로비전 에이전트를](../linux/using-cloud-init.md)사용하지 않도록 설정할 수 있습니다.

지원되는 버전의 에이전트는 자동 업데이트를 사용할 수 있습니다. 업데이트할 수 있는 유일한 코드는 프로비전 에이전트 코드가 아니라 확장 처리 코드입니다. 프로비저닝 에이전트 코드는 한 번 실행 코드입니다.

확장 처리 코드는 다음을 담당합니다.

- Azure 패브릭과 통신
- 설치, 상태 보고, 개별 확장 업데이트 및 확장 제거와 같은 VM 확장 작업을 처리 합니다. 업데이트에는 확장 처리 코드에 대 한 보안 수정, 버그 수정 및 향상 된 기능이 포함 되어 있습니다.

에이전트가 설치되면 부모 디먼이 생성됩니다. 그런 다음이 부모는 확장을 처리 하는 데 사용 되는 자식 프로세스를 생성 합니다. 에이전트에 대해 업데이트를 사용할 수 있는 경우 다운로드 됩니다. 부모는 자식 프로세스를 중지 하 고 업그레이드 한 다음 다시 시작 합니다. 업데이트에 문제가 있는 경우 부모 프로세스가 이전 자식 버전으로 롤백됩니다.

부모 프로세스는 자동으로 업데이트할 수 없습니다. 부모는 배포 패키지 업데이트를 통해서만 업데이트할 수 있습니다.

실행 중인 버전을 확인 하려면 다음과 같이 확인 합니다 `waagent` .

```bash
waagent --version
```

다음 예제와 유사하게 출력됩니다.

```bash
WALinuxAgent-2.2.17 running on ubuntu 16.04
Python: 3.6.0
Goal state agent: 2.2.18
```

위의 예제 출력에서 부모 (또는 패키지 배포 버전)는 `WALinuxAgent-2.2.17` 입니다. `Goal state agent`값은 자동 업데이트 버전입니다.

에이전트에 대해 항상 자동 업데이트를 사용 하도록 설정 하는 것이 좋습니다 [. enable = y](./update-linux-agent.md). 자동 업데이트를 사용 하지 않는 경우 에이전트를 수동으로 업데이트 해야 하며 버그 및 보안 수정이 발생 하지 않습니다.

#### <a name="extension-updates"></a>확장 업데이트

확장 업데이트를 사용할 수 있고 자동 업데이트를 사용 하도록 설정한 경우 [VM 모델을 변경한](#how-agents-and-extensions-are-updated) 후에는 Azure Linux 에이전트가 확장을 다운로드 하 여 업그레이드 합니다.

자동 확장 업데이트는 *부* 또는 *핫픽스* 중 하나입니다. 확장을 프로 비전 할 때 사소한 업데이트를 옵트인 또는 옵트아웃 (opt out) 할 수 있습니다. 다음 예에서는를 사용 하 여 ARM 템플릿에서 부 버전을 자동으로 업그레이드 하는 방법을 보여 줍니다 `"autoUpgradeMinorVersion": true,` .

```json
    "publisher": "Microsoft.Azure.Extensions",
    "type": "CustomScript",
    "typeHandlerVersion": "2.0",
    "autoUpgradeMinorVersion": true,
    "settings": {
        "fileUris": [
        "https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/scripts/config-music.sh"
        ]
    },
```

최신 부 릴리스 버그 수정 사항을 얻으려면 항상 확장 배포에서 자동 업데이트를 선택 하는 것이 좋습니다. 보안 또는 주요 버그 수정을 수행 하는 핫픽스 업데이트를 옵트아웃 (opt out) 할 수 없습니다.

자동 업데이트를 사용 하지 않도록 설정 하거나 주 버전을 업그레이드 해야 하는 경우 [az vm extension set](/cli/azure/vm/extension#az_vm_extension_set) 을 사용 하 고 대상 버전을 지정 합니다.

### <a name="how-to-identify-extension-updates"></a>확장 업데이트를 식별하는 방법

#### <a name="identify-if-the-extension-is-set-with-autoupgrademinorversion-on-a-vm"></a>확장이 VM에서 autoUpgradeMinorVersion로 설정 되었는지 확인 합니다.

확장이로 프로 비전 된 경우 VM 모델에서 볼 수 있습니다 `autoUpgradeMinorVersion` . 확인하려면 [az vm show](/cli/azure/vm#az_vm_show)를 사용하고 다음과 같이 리소스 그룹 및 VM 이름을 제공합니다.

```azurecli
az vm show --resource-group myResourceGroup --name myVM
```

다음 예제 출력에서는가로 설정 된 것을 보여 줍니다 `autoUpgradeMinorVersion` `true` .

```json
  "resources": [
    {
      "autoUpgradeMinorVersion": true,
      "forceUpdateTag": null,
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM/extensions/CustomScriptExtension",
```

#### <a name="identify-when-an-autoupgrademinorversion-event-occurred"></a>AutoUpgradeMinorVersion 이벤트가 발생 한 시기를 식별 합니다.

확장에 대한 업데이트가 발생한 경우를 확인하려면 */var/log/waagent.log* 에서 VM의 에이전트 로그를 검토합니다.

다음 예제에서는 VM이 `Microsoft.OSTCExtensions.LinuxDiagnostic-2.3.9025` 설치 되었습니다. 에서 핫픽스를 사용할 수 있습니다 `Microsoft.OSTCExtensions.LinuxDiagnostic-2.3.9027` .

```bash
INFO [Microsoft.OSTCExtensions.LinuxDiagnostic-2.3.9027] Expected handler state: enabled
INFO [Microsoft.OSTCExtensions.LinuxDiagnostic-2.3.9027] Decide which version to use
INFO [Microsoft.OSTCExtensions.LinuxDiagnostic-2.3.9027] Use version: 2.3.9027
INFO [Microsoft.OSTCExtensions.LinuxDiagnostic-2.3.9027] Current handler state is: NotInstalled
INFO [Microsoft.OSTCExtensions.LinuxDiagnostic-2.3.9027] Download extension package
INFO [Microsoft.OSTCExtensions.LinuxDiagnostic-2.3.9027] Unpack extension package
INFO Event: name=Microsoft.OSTCExtensions.LinuxDiagnostic, op=Download, message=Download succeeded
INFO [Microsoft.OSTCExtensions.LinuxDiagnostic-2.3.9027] Initialize extension directory
INFO [Microsoft.OSTCExtensions.LinuxDiagnostic-2.3.9027] Update settings file: 0.settings
INFO [Microsoft.OSTCExtensions.LinuxDiagnostic-2.3.9025] Disable extension.
INFO [Microsoft.OSTCExtensions.LinuxDiagnostic-2.3.9025] Launch command:diagnostic.py -disable
...
INFO Event: name=Microsoft.OSTCExtensions.LinuxDiagnostic, op=Disable, message=Launch command succeeded: diagnostic.py -disable
INFO [Microsoft.OSTCExtensions.LinuxDiagnostic-2.3.9027] Update extension.
INFO [Microsoft.OSTCExtensions.LinuxDiagnostic-2.3.9027] Launch command:diagnostic.py -update
2017/08/14 20:21:57 LinuxAzureDiagnostic started to handle.
```

## <a name="agent-permissions"></a>에이전트 사용 권한

해당 작업을 수행하려면 에이전트는 *루트* 로 실행해야 합니다.

## <a name="troubleshoot-vm-extensions"></a>VM 확장 문제 해결

각 VM 확장에는 특정 문제 해결 단계가 있을 수 있습니다. 예를 들어 사용자 지정 스크립트 확장을 사용 하는 경우 확장이 실행 된 VM에서 로컬로 스크립트 실행 세부 정보를 찾을 수 있습니다. 

다음 문제 해결 작업은 모든 VM 확장에 적용 됩니다.

- Azure Linux 에이전트 로그를 확인 하려면 확장이 */var/log/waagent.log* 에서 프로 비전 될 때 활동을 확인 합니다.

- */Var/log/azure/ \<extensionName>* 에서 자세한 내용은 확장 로그를 확인 하세요.

- 오류 코드, 알려진 문제 및 기타 확장 관련 정보는 확장 관련 설명서의 문제 해결 섹션을 확인 하세요.

- 시스템 로그를 확인합니다. 패키지 관리자에 대 한 배타적 액세스가 필요한 다른 응용 프로그램의 장기 실행 설치와 같이 확장과 함께 데 방해가 되 수 있는 다른 작업이 있는지 확인 합니다.

### <a name="common-reasons-for-extension-failures"></a>확장 오류에 대한 일반적인 이유

- 확장을 실행 하는 데 20 분이 걸립니다. 예외는 90 분의 사용자 지정 스크립트, Chef 및 DSC입니다. 배포가이 시간을 초과 하면 시간 제한으로 표시 됩니다. 이 오류의 원인은 리소스가 부족할 수 있으며, 다른 VM 구성 또는 시작 작업은 확장을 프로 비전 하는 동안 많은 양의 리소스를 사용 하는 것입니다.

- 최소 필수 구성 요소가 충족 되지 않았습니다. 일부 확장은 HPC 이미지와 같은 VM SKU에 대해 종속됩니다. 확장에는 Azure Storage 또는 공용 서비스와 통신 하는 것과 같은 특정 네트워킹 액세스 요구 사항이 있을 수 있습니다. 다른 예로는 패키지 리포지토리에 대 한 액세스, 디스크 공간 부족 또는 보안 제한이 있습니다.

- 패키지 관리자 액세스가 배타적입니다. 경우에 따라 패키지 관리자에 대 한 단독 액세스를 필요로 하기 때문에 장기 실행 VM 구성 및 확장 설치가 충돌할 수 있습니다.

### <a name="view-extension-status"></a>확장 상태 보기

VM 확장이 VM에 대해 실행된 후에 다음과 같이 [az vm get-instance-view](/cli/azure/vm#az_vm_get_instance_view)를 사용하여 확장 상태를 반환합니다.

```azurecli
az vm get-instance-view \
    --resource-group rgName \
    --name myVM \
    --query "instanceView.extensions"
```

다음 예제와 유사하게 출력됩니다.

```bash
  {
    "name": "customScript",
    "statuses": [
      {
        "code": "ProvisioningState/failed/0",
        "displayStatus": "Provisioning failed",
        "level": "Error",
        "message": "Enable failed: failed to execute command: command terminated with exit status=127\n[stdout]\n\n[stderr]\n/bin/sh: 1: ech: not found\n",
        "time": null
      }
    ],
    "substatuses": null,
    "type": "Microsoft.Azure.Extensions.customScript",
    "typeHandlerVersion": "2.0.6"
  }
```

Azure Portal에서 확장 실행 상태를 찾을 수도 있습니다. VM을 선택 하 고 **확장** 을 선택한 다음 원하는 확장을 선택 합니다.

### <a name="rerun-a-vm-extension"></a>VM 확장 다시 실행

VM 확장을 다시 실행 해야 하는 경우가 있을 수 있습니다. 확장을 다시 실행하려면 확장을 제거한 다음 원하는 실행 방법으로 확장을 다시 실행하면 됩니다. 확장을 제거하려면 다음과 같이 [az vm extension delete](/cli/azure/vm/extension#az_vm_extension_delete)를 사용합니다.

```azurecli
az vm extension delete \
    --resource-group myResourceGroup \
    --vm-name myVM \
    --name customScript
```

Azure Portal에서 확장을 제거할 수도 있습니다.

1. VM을 선택합니다.
2. **확장** 을 섡택합니다.
3. 확장을 선택 합니다.
4. **제거** 를 선택합니다.

## <a name="common-vm-extension-reference"></a>일반적인 VM 확장 참조

| 확장 이름 | Description |
| --- | --- |
| [Linux용 사용자 지정 스크립트 확장](custom-script-linux.md) |Azure 가상 컴퓨터에 대해 스크립트를 실행 합니다. |
| [VMAccess 확장](https://github.com/Azure/azure-linux-extensions/tree/master/VMAccess) |Azure 가상 컴퓨터에 대 한 액세스 권한을 다시 확보 합니다. 또한 [사용자 및 자격 증명을 관리](https://azure.microsoft.com/blog/using-vmaccess-extension-to-reset-login-credentials-for-linux-vm/)하는 데 사용할 수 있습니다. |
| [Azure Diagnostics 확장](https://azure.microsoft.com/blog/windows-azure-virtual-machine-monitoring-with-wad-extension/) |Azure 진단 관리 |

## <a name="next-steps"></a>다음 단계

VM 확장에 대 한 자세한 내용은 [Azure virtual machine 확장 및 기능](overview.md)을 참조 하세요.
