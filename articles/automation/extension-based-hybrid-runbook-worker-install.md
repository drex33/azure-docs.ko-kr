---
title: Azure Automation 확장 기반 Windows 또는 Linux 사용자 Hybrid Runbook Worker 배포(미리 보기)
description: 이 문서에서는 로컬 데이터 센터 또는 클라우드 환경의 Windows 기반 머신에서 Runbook을 실행하는 데 사용할 수 있는 확장 기반 Windows 또는 Linux Hybrid Runbook Worker 배포하는 방법을 알려줍니다.
services: automation
ms.subservice: process-automation
ms.date: 09/28/2021
ms.topic: how-to
ms.openlocfilehash: b10fb2b1adda12c52e5df8351e66a8ea18757894
ms.sourcegitcommit: 613789059b275cfae44f2a983906cca06a8706ad
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/29/2021
ms.locfileid: "129294399"
---
# <a name="deploy-an-extension-based-windows-or-linux-user-hybrid-runbook-worker-in-automation-preview"></a>Automation에서 확장 기반 Windows 또는 Linux 사용자 Hybrid Runbook Worker 배포(미리 보기)

확장 기반 온보딩은 **사용자** Hybrid Runbook Workers에만 해당됩니다. **시스템** Hybrid Runbook Worker 온보딩의 경우 [Automation에서 에이전트 기반 Windows Hybrid Runbook Worker 배포](./automation-windows-hrw-install.md) 또는 Automation에서 에이전트 [기반 Linux Hybrid Runbook Worker 배포를 참조하세요.](./automation-linux-hrw-install.md) 

Azure Automation의 사용자 Hybrid Runbook Worker 기능을 사용하여 [Azure Arc 사용 서버](../azure-arc/servers/overview.md)에 등록된 서버를 포함하여 Azure 또는 비 Azure 머신에서 직접 Runbook을 실행할 수 있습니다. 역할을 호스트하는 머신 또는 서버에서 해당 로컬 리소스를 관리할 수 있도록 해당 서버에 대해 또는 환경의 리소스에 대해 Runbook을 직접 실행할 수 있습니다.

Azure Automation Runbook을 저장 및 관리한 다음, 선택한 하나 이상의 머신에 배달합니다. 이 문서에서는 Windows 또는 Linux 머신에 사용자 Hybrid Runbook Worker 배포하고, 작업자를 제거하고, Hybrid Runbook Worker 그룹을 제거하는 방법을 설명합니다.

Runbook Worker를 성공적으로 배포한 후에는 [Hybrid Runbook Worker에서 Runbook 실행](automation-hrw-run-runbooks.md)을 검토하여 온-프레미스 데이터 센터 또는 다른 클라우드 환경의 프로세스를 자동화하도록 Runbook을 구성하는 방법을 알아봅니다.

## <a name="prerequisites"></a>필수 구성 요소

### <a name="minimum-requirements"></a>최소 요구 사항
- 코어 2개
- RAM 4GB
- 443 포트(아웃바운드)
- Azure 가상 머신 또는 Arc 지원 서버에서 시스템 할당 관리 ID를 사용하도록 설정해야 합니다.  시스템 할당 관리 ID를 사용하도록 설정하지 않으면 추가 프로세스의 일부로 사용하도록 설정됩니다.
- 비 Azure 머신에는 Azure Arc 사용하도록 설정된 서버 에이전트가 설치되어 있어야 합니다(연결된 컴퓨터 에이전트). 를 설치하는 지침은 `AzureConnectedMachineAgent` [Azure Portal 하이브리드 머신을 Azure로 커넥트](../azure-arc/servers/onboard-portal.md)참조하세요.

### <a name="supported-operating-systems"></a>지원되는 운영 체제
| Windows | Linux(x64)|
|---|---|
| &#9679; Windows Server 2019(Server Core 포함), <br> &#9679; Windows Server 2016, 버전 1709 및 1803(Server Core 제외) 및 <br> &#9679; Windows Server 2012, 2012 R2 <br><br> | &#9679; Debian GNU/Linux 7 및 8, <br> &#9679; Ubuntu 18.04 및 20.04 LTS, <br> &#9679; SUSE Linux Enterprise Server 15 및 15.1(SUSE는 버전 13 또는 14를 릴리스하지 않았습니다.) 및 <br> &#9679; Red Hat Enterprise Linux Server 7 및 8 |

### <a name="other-requirements"></a>기타 요구 사항
| Windows | Linux(x64)|
|---|---|
| Windows PowerShell 5.1(WMF 5.1 다운로드). PowerShell Core 지원되지 않습니다.| Linux 강화를 사용하도록 설정하면 안 됩니다.  |
| .NET Framework 4.6.2 이상 ||

### <a name="package-requirements-for-linux"></a>Linux에 대한 패키지 요구 사항

| 필수 패키지 | Description |최소 버전|
|--------------------- | --------------------- | -------------------|
|Glibc |GNU C 라이브러리| 2.5-12 |
|Openssl| OpenSSL 라이브러리 | 1.0(TLS 1.1 및 TLS 1.2가 지원됨)|
|Curl | cURL 웹 클라이언트 | 7.15.5|
|Python-ctypes | Python용 외장 함수 라이브러리| Python 2.x 또는 Python 3.x 필요 |
|PAM | 플러그형 인증 모듈|

| 선택적 패키지 | Description | 최소 버전|
|--------------------- | --------------------- | -------------------|
| PowerShell Core | PowerShell Runbook을 실행하려면 PowerShell Core를 설치해야 합니다. 자세한 내용은 [Linux에 PowerShell Core 설치를 참조하세요.](/powershell/scripting/install/installing-powershell-core-on-linux) | 6.0.0 |

### <a name="network-requirements"></a>네트워크 요구 사항

### <a name="proxy-server-use"></a>프록시 서버 사용

확장 기반 Hybrid Runbook Worker 실행하는 컴퓨터와 Azure Automation 간의 통신에 프록시 서버를 사용하는 경우 적절한 리소스에 액세스할 수 있는지 확인합니다. Hybrid Runbook Worker 및 Automation 서비스의 요청 제한 시간은 30초입니다. 3회 시도 후에는 요청이 실패합니다.

### <a name="firewall-use"></a>방화벽 사용

방화벽을 사용하여 인터넷 액세스를 제한하는 경우 액세스를 허용하도록 방화벽을 구성해야 합니다. Azure Automation과 커뮤니케이션하기 위해 Hybrid Runbook Worker 및 [Automation State Configuration](./automation-dsc-overview.md)에는 다음 포트와 URL이 필요합니다.

| 속성 | Description |
|---|---|
|포트 | 아웃바운드 인터넷 액세스의 경우 443|
|전역 URL |\* .azure-automation.net|
|US Gov 버지니아 전역 URL |*.azure-automation.us|
|에이전트 서비스 |`https://<workspaceId>.agentsvc.azure-automation.net`|

## <a name="create-hybrid-worker-group"></a>Hybrid Worker 그룹 만들기 

Azure Portal Hybrid Worker 그룹을 만들려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

1. Automation 계정으로 이동합니다. 

1. **프로세스 자동화에서** **Hybrid Worker 그룹 을** 선택합니다. 

1. **+ Hybrid Worker 그룹 만들기를** 선택합니다. 

1. 기본  **사항**   탭의 **이름** 텍스트 상자에 Hybrid Worker 그룹의 이름을 입력합니다. 

1. 자격 **증명으로 실행 사용** 옵션의 경우: 
   - **아니요를** 선택하면 로컬 시스템 계정을 사용하여 하이브리드 확장이 설치됩니다.
   - **예를** 선택한 경우 드롭다운 목록에서 자격 증명 자산을 선택합니다.

1. **다음을** 선택하여 **Hybrid Worker 선택** 탭으로 진행합니다. 이 Hybrid Worker 그룹에 추가할 Azure 가상 머신 또는 Azure Arc 지원 서버를 선택할 수 있습니다. 컴퓨터를 선택하지 않으면 빈 Hybrid Worker 그룹이 만들어집니다. 나중에 컴퓨터를 추가할 수 있습니다. 

1. **머신 추가를** 선택하여 **Hybrid Worker로 머신 추가 페이지로** 이동합니다. 다른 Hybrid Worker 그룹에 속하지 않는 컴퓨터만 표시됩니다. 

1. Hybrid Worker 그룹에 추가하려는 머신 옆의 확인란을 선택합니다. 비 Azure 머신이 나열되지 않으면 Azure Arc Connected Machine 에이전트가 컴퓨터에 설치되어 있는지 확인합니다.  

1. **추가** 를 선택합니다. 

1. **다음을** 선택하여 **검토 + 만들기** 탭으로 진행합니다. 

1. **생성** 를 선택합니다. Hybrid Worker 확장이 컴퓨터에 설치되고 Hybrid Worker가 Hybrid Worker 그룹에 등록됩니다. **프로세스 자동화** Hybrid Worker 그룹 사용자 하이브리드 작업자 그룹 에서 만들기를 확인할 수  >    >  **있습니다.** **새로 고침을** 선택해야 할 수도 있습니다. 그룹에 Hybrid Worker를 추가하면 즉시 발생하지만 확장 설치에는 몇 분 정도 걸릴 수 있습니다. 그룹 이름을 선택하여 Hybrid Worker 세부 정보를 확인합니다.

> [!NOTE]
> 선택한 컴퓨터가 이미 다른 Hybrid Worker 그룹에 속해 있는 경우 Hybrid Worker 그룹에 추가되지 않습니다.

## <a name="add-a-machine-to-the-hybrid-worker-group"></a>Hybrid Worker 그룹에 컴퓨터 추가

빈 Hybrid Worker 그룹을 만든 다음 컴퓨터를 추가할 수도 있습니다.  

1. **프로세스 자동화에서** **Hybrid Worker 그룹을** 선택한 다음, Hybrid Worker 그룹을 선택하여 Hybrid Worker **그룹** 페이지로 이동합니다.

1. **Hybrid Worker 그룹** 아래에서 Hybrid **Worker를** 선택합니다.

1. **+ 추가를** 선택하여 **Hybrid Worker로 머신 추가 페이지로** 이동합니다. 다른 Hybrid Worker 그룹에 속하지 않는 컴퓨터만 표시됩니다. 

1. Hybrid Worker 그룹에 추가하려는 머신 옆의 확인란을 선택합니다. 비 Azure 머신이 나열되지 않으면 Azure Arc Connected Machine 에이전트가 컴퓨터에 설치되어 있는지 확인합니다.

1. **추가를** 선택하여 머신을 그룹에 추가합니다. 추가되면 머신 유형을 Azure 가상 머신 또는 Arc 지원 서버로 볼 수 있습니다. **플랫폼** 필드에는 작업자가 **에이전트 기반(V1)** 또는 **확장 기반(V2)으로** 표시됩니다.

## <a name="delete-a-hybrid-runbook-worker"></a>Hybrid Runbook Worker 삭제

포털에서 Hybrid Runbook Worker 삭제할 수 있습니다.

1. **프로세스 자동화에서** **Hybrid Worker 그룹을** 선택한 다음, Hybrid Worker 그룹을 선택하여 Hybrid Worker **그룹** 페이지로 이동합니다.

1. **Hybrid Worker 그룹** 아래에서 Hybrid **Worker를** 선택합니다.

1. Hybrid Worker 그룹에서 삭제하려는 머신 옆의 확인란을 선택합니다.

1. **삭제** 를 선택합니다.  선택한 **Hybrid Worker가** 영구적으로 삭제된다는 경고가 대화 상자에 표시됩니다. **삭제** 를 선택합니다. 이 작업은 **확장 기반(V2)** 작업자에 대한 확장을 삭제하거나 포털에서 **에이전트 기반(V1)** 항목을 제거합니다. 그러나 부실한 Hybrid Worker는 VM에 남겨 둡니다. 에이전트를 수동으로 제거하려면 에이전트 [제거를](../azure-monitor/agents/agent-manage.md#uninstall-agent)참조하세요.

> [!NOTE]
> Hybrid Worker는 **에이전트 기반(V1) 및 확장 기반(V2)** 플랫폼 모두와 공존할 수 **있습니다.** 이미 에이전트 기반(V1)을 실행하는 Hybrid Worker에 **확장 기반(V2)을** 설치하는 경우 그룹에 Hybrid Runbook Worker 두 개의 항목이 표시됩니다.  하나는 확장 **기반 플랫폼(V2)이고** 다른 하나는 **에이전트 기반(V1)입니다.**

## <a name="delete-a-hybrid-runbook-worker-group"></a>Hybrid Runbook Worker 그룹 삭제

포털에서 Hybrid Runbook Worker 그룹을 삭제할 수 있습니다.

1. **프로세스 자동화에서** **Hybrid Worker 그룹을** 선택한 다음, Hybrid Worker 그룹을 선택하여 Hybrid Worker **그룹** 페이지로 이동합니다.

1. **삭제** 를 선택합니다. Hybrid Worker 그룹에서 Hybrid Worker로 정의된 모든 컴퓨터를 제거하라는 경고가 대화 상자에 표시됩니다. 그룹에 작업자가 이미 추가된 경우 먼저 그룹에서 작업자를 삭제해야 합니다.

1. **예** 를 선택합니다. Hybrid Worker 그룹이 삭제됩니다.

## <a name="use-azure-resource-manager-template"></a>Azure Resource Manager 템플릿 사용

ARM(Azure Resource Manager) 템플릿을 사용하여 새 Azure Windows VM을 만들고 기존 Automation 계정 및 Hybrid Worker 그룹에 연결할 수 있습니다. ARM 템플릿에 대한 자세한 내용은 [ARM 템플릿이란?을 참조하세요.](../azure-resource-manager/templates/overview.md)

### <a name="review-the-template"></a>템플릿 검토

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "automationAccount": {
      "type": "string"
    },
    "automationAccountLocation": {
      "type": "string"
    },
    "workerGroupName": {
      "type": "string"
    },
    "virtualMachineName": {
      "type": "string",
      "defaultValue": "simple-vm",
      "metadata": {
        "description": "Name of the virtual machine."
      }
    },
    "adminUsername": {
      "type": "string",
      "metadata": {
        "description": "Username for the Virtual Machine."
      }
    },
    "adminPassword": {
      "type": "securestring",
      "minLength": 12,
      "metadata": {
        "description": "Password for the Virtual Machine."
      }
    },
    "vmLocation": {
      "type": "string",
      "defaultValue": "North Central US",
      "metadata": {
        "description": "Location for the VM."
      }
    },
    "vmSize": {
      "type": "string",
      "defaultValue": "Standard_DS1_v2",
      "metadata": {
        "description": "Size of the virtual machine."
      }
    },
    "osVersion": {
      "type": "string",
      "defaultValue": "2019-Datacenter",
      "allowedValues": [
        "2008-R2-SP1",
        "2012-Datacenter",
        "2012-R2-Datacenter",
        "2016-Nano-Server",
        "2016-Datacenter-with-Containers",
        "2016-Datacenter",
        "2019-Datacenter",
        "2019-Datacenter-Core",
        "2019-Datacenter-Core-smalldisk",
        "2019-Datacenter-Core-with-Containers",
        "2019-Datacenter-Core-with-Containers-smalldisk",
        "2019-Datacenter-smalldisk",
        "2019-Datacenter-with-Containers",
        "2019-Datacenter-with-Containers-smalldisk"
      ],
      "metadata": {
        "description": "The Windows version for the VM. This will pick a fully patched image of this given Windows version."
      }
    },
    "dnsNameForPublicIP": {
      "type": "string",
      "metadata": {
        "description": "DNS name for the public IP"
      }
    },
    "_CurrentDateTimeInTicks": {
      "type": "string",
      "defaultValue": "[utcNow('yyyy-MM-dd')]"
    }
  },
  "variables": {
    "nicName": "myVMNict",
    "addressPrefix": "10.0.0.0/16",
    "subnetName": "Subnet",
    "subnetPrefix": "10.0.0.0/24",
    "subnetRef": "[resourceId('Microsoft.Network/virtualNetworks/subnets', variables('virtualNetworkName'), variables('subnetName'))]",
    "vmName": "[parameters('virtualMachineName')]",
    "virtualNetworkName": "MyVNETt",
    "publicIPAddressName": "myPublicIPt",
    "networkSecurityGroupName": "default-NSGt",
    "UniqueStringBasedOnTimeStamp": "[uniqueString(deployment().name, parameters('_CurrentDateTimeInTicks'))]"
  },
  "resources": [
    {
      "apiVersion": "2020-08-01",
      "type": "Microsoft.Network/publicIPAddresses",
      "name": "[variables('publicIPAddressName')]",
      "location": "[parameters('vmLocation')]",
      "properties": {
        "publicIPAllocationMethod": "Dynamic",
        "dnsSettings": {
          "domainNameLabel": "[parameters('dnsNameForPublicIP')]"
        }
      }
    },
    {
      "comments": "Default Network Security Group for template",
      "type": "Microsoft.Network/networkSecurityGroups",
      "apiVersion": "2020-08-01",
      "name": "[variables('networkSecurityGroupName')]",
      "location": "[parameters('vmLocation')]",
      "properties": {
        "securityRules": [
          {
            "name": "default-allow-3389",
            "properties": {
              "priority": 1000,
              "access": "Allow",
              "direction": "Inbound",
              "destinationPortRange": "3389",
              "protocol": "Tcp",
              "sourceAddressPrefix": "*",
              "sourcePortRange": "*",
              "destinationAddressPrefix": "*"
            }
          }
        ]
      }
    },
    {
      "apiVersion": "2020-08-01",
      "type": "Microsoft.Network/virtualNetworks",
      "name": "[variables('virtualNetworkName')]",
      "location": "[parameters('vmLocation')]",
      "dependsOn": [
        "[resourceId('Microsoft.Network/networkSecurityGroups', variables('networkSecurityGroupName'))]"
      ],
      "properties": {
        "addressSpace": {
          "addressPrefixes": [
            "[variables('addressPrefix')]"
          ]
        },
        "subnets": [
          {
            "name": "[variables('subnetName')]",
            "properties": {
              "addressPrefix": "[variables('subnetPrefix')]",
              "networkSecurityGroup": {
                "id": "[resourceId('Microsoft.Network/networkSecurityGroups', variables('networkSecurityGroupName'))]"
              }
            }
          }
        ]
      }
    },
    {
      "apiVersion": "2020-08-01",
      "type": "Microsoft.Network/networkInterfaces",
      "name": "[variables('nicName')]",
      "location": "[parameters('vmLocation')]",
      "dependsOn": [
        "[variables('publicIPAddressName')]",
        "[variables('virtualNetworkName')]"
      ],
      "properties": {
        "ipConfigurations": [
          {
            "name": "ipconfig1",
            "properties": {
              "privateIPAllocationMethod": "Dynamic",
              "publicIPAddress": {
                "id": "[resourceId('Microsoft.Network/publicIPAddresses',variables('publicIPAddressName'))]"
              },
              "subnet": {
                "id": "[variables('subnetRef')]"
              }
            }
          }
        ]
      }
    },
    {
      "apiVersion": "2020-12-01",
      "type": "Microsoft.Compute/virtualMachines",
      "name": "[variables('vmName')]",
      "location": "[parameters('vmLocation')]",
      "dependsOn": [
        "[variables('nicName')]"
      ],
      "identity": {
             "type": "SystemAssigned"
      } ,
      "properties": {
        "hardwareProfile": {
          "vmSize": "[parameters('vmSize')]"
        },
        "osProfile": {
          "computerName": "[variables('vmName')]",
          "adminUsername": "[parameters('adminUsername')]",
          "adminPassword": "[parameters('adminPassword')]"
        },
        "storageProfile": {
          "imageReference": {
            "publisher": "MicrosoftWindowsServer",
            "offer": "WindowsServer",
            "sku": "[parameters('osVersion')]",
            "version": "latest"
          },
          "osDisk": {
            "createOption": "FromImage"
          }
        },
        "networkProfile": {
          "networkInterfaces": [
            {
              "id": "[resourceId('Microsoft.Network/networkInterfaces',variables('nicName'))]"
            }
          ]
        }
      }
    },
    {
      "type": "Microsoft.Automation/automationAccounts",
      "apiVersion": "2021-06-22",
      "name": "[parameters('automationAccount')]",
      "location": "[parameters('automationAccountLocation')]",
      "properties": {
        "sku": {
          "name": "Basic"
        }
      },
      "resources": [
        {
          "name": "[concat(parameters('workerGroupName'),'/',guid('AzureAutomationJobName', variables('UniqueStringBasedOnTimeStamp')))]",
          "type": "hybridRunbookWorkerGroups/hybridRunbookWorkers",
          "apiVersion": "2021-06-22",
          "dependsOn": [
            "[resourceId('Microsoft.Automation/automationAccounts', parameters('automationAccount'))]",
            "[resourceId('Microsoft.Compute/virtualMachines', variables('vmName'))]"
          ],
          "properties": {
            "vmResourceId": "[resourceId('Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]"
          }
        }
      ]
    },
    {
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "name": "[concat(parameters('virtualMachineName'),'/HybridWorkerExtension')]",
      "apiVersion": "2020-12-01",
      "location": "[parameters('vmLocation')]",
      "dependsOn": [
        "[resourceId('Microsoft.Automation/automationAccounts', parameters('automationAccount'))]",
        "[resourceId('Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]"
      ],
      "properties": {
        "publisher": "Microsoft.Azure.Automation.HybridWorker",
        "type": "HybridWorkerForWindows",
        "typeHandlerVersion": "0.1",
        "autoUpgradeMinorVersion": true,
        "settings": {
          "AutomationAccountURL": "[reference(resourceId('Microsoft.Automation/automationAccounts', parameters('automationAccount'))).AutomationHybridServiceUrl]"
        }
      }
    }
  ],
  "outputs": {
    "output1": {
      "type": "string",
      "value": "[reference(resourceId('Microsoft.Automation/automationAccounts', parameters('automationAccount'))).AutomationHybridServiceUrl]"
    }
  }
}
```

템플릿에 정의된 Azure 리소스:

- hybridRunbookWorkerGroups/hybridRunbookWorkers
- [Microsoft.Compute/virtualMachines/extensions:](/azure/templates/microsoft.compute/virtualmachines/extensions) 

### <a name="review-parameters"></a>매개 변수 검토

이 템플릿에 사용된 매개 변수를 검토합니다.

|속성 |Description |
|---|---|
|automationAccount| 기존 Automation 계정의 이름입니다. |
|automationAccountLocation | 기존 Automation 계정의 지역입니다. |
|workerGroupName | 기존 Hybrid Worker 그룹의 이름입니다. |
|virtualMachineName| 만들 VM의 이름입니다. 기본값은 `simple-vm`입니다.|
|adminUsername| VM 관리자 사용자 이름입니다. |
|adminPassword| VM 관리자 암호입니다. |
|vmLocation| 새 VM의 지역입니다. 기본값은 `North Central US`입니다.|
|vmSize| 새 VM의 크기입니다. 기본값은 `Standard_DS1_v2`입니다. |
|osVersion| 새 Windows VM에 대한 OS입니다. 기본값은 `2019-Datacenter`입니다.|
|dnsNameForPublicIP| 공용 IP의 DNS 이름입니다. |


## <a name="next-steps"></a>다음 단계

* 온-프레미스 데이터 센터 또는 다른 클라우드 환경의 프로세스를 자동화하도록 Runbook을 구성하는 방법을 알아보려면 [Hybrid Runbook Worker에서 Runbook 실행](automation-hrw-run-runbooks.md)을 참조하세요.

* Hybrid Runbook Worker 문제를 해결하는 방법을 알아보려면 [Hybrid Runbook Worker 문제 해결](troubleshoot/hybrid-runbook-worker.md#general)을 참조하세요.