---
title: Azure Automation 확장 기반 Windows 또는 Linux 사용자 Hybrid Runbook Worker 배포(미리 보기)
description: 이 문서에서는 로컬 데이터 센터 또는 클라우드 환경의 Windows 기반 머신에서 Runbook을 실행하는 데 사용할 수 있는 확장 기반 Windows 또는 Linux Hybrid Runbook Worker 배포하는 방법을 알려줍니다.
services: automation
ms.subservice: process-automation
ms.date: 09/28/2021
ms.topic: how-to
ms.openlocfilehash: 48f83cd92f682b6fb66a1d7b5bebd49cd5053b62
ms.sourcegitcommit: e1037fa0082931f3f0039b9a2761861b632e986d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/12/2021
ms.locfileid: "132400778"
---
# <a name="deploy-an-extension-based-windows-or-linux-user-hybrid-runbook-worker-in-automation-preview"></a>Automation에서 확장 기반 Windows 또는 Linux 사용자 Hybrid Runbook Worker 배포(미리 보기)

확장 기반 온보딩은 **사용자** Hybrid Runbook Workers에만 해당됩니다. **시스템** Hybrid Runbook Worker 온보딩의 경우 [Automation에서 에이전트 기반 Windows Hybrid Runbook Worker 배포](./automation-windows-hrw-install.md) 또는 Automation에서 [에이전트 기반 Linux Hybrid Runbook Worker 배포를 참조하세요.](./automation-linux-hrw-install.md) 

Azure Automation의 사용자 Hybrid Runbook Worker 기능을 사용하여 [Azure Arc 사용 서버](../azure-arc/servers/overview.md)에 등록된 서버를 포함하여 Azure 또는 비 Azure 머신에서 직접 Runbook을 실행할 수 있습니다. 역할을 호스트하는 머신 또는 서버에서 해당 로컬 리소스를 관리할 수 있도록 해당 서버에 대해 또는 환경의 리소스에 대해 Runbook을 직접 실행할 수 있습니다.

Azure Automation Runbook을 저장하고 관리한 다음, 선택한 하나 이상의 컴퓨터에 배달합니다. 이 문서에서는 Windows 또는 Linux 머신에 사용자 Hybrid Runbook Worker 배포하고, 작업자를 제거하고, Hybrid Runbook Worker 그룹을 제거하는 방법을 설명합니다.

Runbook Worker를 성공적으로 배포한 후에는 [Hybrid Runbook Worker에서 Runbook 실행](automation-hrw-run-runbooks.md)을 검토하여 온-프레미스 데이터 센터 또는 다른 클라우드 환경의 프로세스를 자동화하도록 Runbook을 구성하는 방법을 알아봅니다.

## <a name="prerequisites"></a>필수 구성 요소

### <a name="machine-minimum-requirements"></a>머신 최소 요구 사항
- 코어 2개
- RAM 4GB
- Azure 가상 머신 또는 Arc 지원 서버에서 시스템 할당 관리 ID를 사용하도록 설정해야 합니다.  시스템 할당 관리 ID를 사용하도록 설정하지 않으면 추가 프로세스의 일부로 사용하도록 설정됩니다.
- 비 Azure 머신에는 Azure Arc 사용하도록 설정된 서버 에이전트가 설치되어 있어야 합니다(연결된 컴퓨터 에이전트). 를 설치하는 지침은 `AzureConnectedMachineAgent` [Azure Portal 하이브리드 머신을 Azure로 커넥트](../azure-arc/servers/onboard-portal.md)참조하세요.

### <a name="supported-operating-systems"></a>지원되는 운영 체제
| Windows | Linux(x64)|
|---|---|
| &#9679; Windows Server 2019(Server Core 포함), <br> &#9679; Windows Server 2016, 버전 1709 및 1803(Server Core 제외) 및 <br> &#9679; Windows Server 2012, 2012 R2 <br><br> | &#9679; Debian GNU/Linux 7 및 8, <br> &#9679; Ubuntu 18.04 및 20.04 LTS, <br> &#9679; SUSE Linux Enterprise Server 15 및 15.1(SUSE는 버전 13 또는 14를 릴리스하지 않았음) 및 <br> &#9679; Red Hat Enterprise Linux Server 7 및 8 |

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

Azure Automation 확장 기반 Hybrid Runbook Worker 실행하는 컴퓨터 간의 통신에 프록시 서버를 사용하는 경우 적절한 리소스에 액세스할 수 있는지 확인합니다. Hybrid Runbook Worker 및 Automation 서비스의 요청 제한 시간은 30초입니다. 3회 시도 후에는 요청이 실패합니다.

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

   :::image type="content" source="./media/extension-based-hybrid-runbook-worker-install/hybrid-worker-groups-portal.png" alt-text="포털에서 Hybrid Worker 그룹 옵션을 선택합니다.":::

1. 기본  **사항**   탭의 **이름** 텍스트 상자에 Hybrid Worker 그룹의 이름을 입력합니다. 

1. 자격 **증명으로 실행 사용** 옵션의 경우: 
   - **아니요를** 선택하면 로컬 시스템 계정을 사용하여 하이브리드 확장이 설치됩니다.
   - **예를** 선택한 경우 드롭다운 목록에서 자격 증명 자산을 선택합니다.

1. **다음을** 선택하여 **Hybrid Workers** 탭으로 진행합니다. 이 Hybrid Worker 그룹에 추가할 Azure 가상 머신 또는 Azure Arc 지원 서버를 선택할 수 있습니다. 컴퓨터를 선택하지 않으면 빈 Hybrid Worker 그룹이 만들어집니다. 나중에 컴퓨터를 추가할 수 있습니다. 

   :::image type="content" source="./media/extension-based-hybrid-runbook-worker-install/basics-tab-portal.png" alt-text="기본 탭에 이름 및 자격 증명을 입력합니다.":::

1. **머신 추가를** 선택하여 **Hybrid Worker로 머신 추가 페이지로** 이동합니다. 다른 Hybrid Worker 그룹에 속하지 않는 컴퓨터만 표시됩니다. 

1. Hybrid Worker 그룹에 추가하려는 머신 옆의 확인란을 선택합니다. 비 Azure 머신이 나열되지 않으면 Azure Arc Connected Machine 에이전트가 컴퓨터에 설치되어 있는지 확인합니다.  

1. **추가** 를 선택합니다. 

1. **다음을** 선택하여 **검토 + 만들기** 탭으로 진행합니다. 

1. **만들기** 를 선택합니다. Hybrid Worker 확장이 컴퓨터에 설치되고 Hybrid Worker가 Hybrid Worker 그룹에 등록됩니다. 하이브리드 작업자를 그룹에 추가 하는 것은 즉시 수행 되지만 확장을 설치 하는 데 몇 분 정도 걸릴 수 있습니다. **새로 고침** 을 선택 하 여 새 그룹을 표시 합니다. 하이브리드 작업자 세부 정보를 보려면 그룹 이름을 선택 합니다.

   > [!NOTE]
   > 선택한 컴퓨터는 이미 다른 hybrid worker 그룹의 일부인 경우 hybrid worker 그룹에 추가 되지 않습니다.

## <a name="add-a-machine-to-a-hybrid-worker-group"></a>Hybrid worker 그룹에 컴퓨터 추가

기존 hybrid worker 그룹에 컴퓨터를 추가할 수도 있습니다.

1. **프로세스 자동화** 에서 **hybrid worker 그룹** 을 선택 하 고 기존 hybrid worker 그룹을 선택 하 여 **Hybrid Worker 그룹** 페이지로 이동 합니다.

1. **Hybrid worker 그룹** 에서 **hybrid worker** 를 선택 합니다.

   :::image type="content" source="./media/extension-based-hybrid-runbook-worker-install/hybrid-worker-group-add-machine.png" alt-text="추가 단추를 클릭 하 여 기존 그룹에 컴퓨터를 추가 합니다.":::

1. **+ 추가** 를 선택 하 여 **컴퓨터를 Hybrid worker로 추가** 페이지로 이동 합니다. 다른 hybrid worker 그룹의 일부가 아닌 컴퓨터만 표시 됩니다. 

1. Hybrid worker 그룹에 추가 하려는 컴퓨터 옆의 확인란을 선택 합니다. Azure가 아닌 컴퓨터가 나열 되지 않는 경우 Azure Arc 연결 된 컴퓨터 에이전트가 컴퓨터에 설치 되어 있는지 확인 합니다.

1. **추가** 를 선택 하 여 그룹에 컴퓨터를 추가 합니다. 추가 되 면 컴퓨터 유형을 Azure 가상 머신 또는 Arc 사용 서버로 볼 수 있습니다. **Platform** 필드는 작업자를 **에이전트 기반 (V1)** 또는 **확장 기반 (V2)** 으로 표시 합니다.

   :::image type="content" source="./media/extension-based-hybrid-runbook-worker-install/hybrid-worker-group-platform.png" alt-text="에이전트 또는 확장 기반을 표시 하는 플랫폼 필드입니다.":::

## <a name="delete-a-hybrid-runbook-worker"></a>Hybrid Runbook Worker 삭제

포털에서 Hybrid Runbook Worker를 삭제할 수 있습니다.

1. **프로세스 자동화** 에서 **hybrid worker 그룹** 을 선택 하 고 hybrid worker 그룹을 선택 하 여 **Hybrid Worker 그룹** 페이지로 이동 합니다.

1. **Hybrid worker 그룹** 에서 **hybrid worker** 를 선택 합니다.

1. Hybrid worker 그룹에서 삭제 하려는 컴퓨터 옆의 확인란을 선택 합니다.

1. **삭제** 를 선택합니다.  선택한 hybrid worker가 영구적으로 삭제 되는 대화 상자에서 **hybrid worker를 삭제** 하는 경고가 표시 됩니다. **삭제** 를 선택합니다. 이 작업은 **확장 기반 (V2)** 작업자에 대 한 확장을 삭제 하거나 포털에서 **에이전트 기반 (V1)** 항목을 제거 합니다. 그러나 VM에 오래 된 hybrid worker가 남아 있습니다. 에이전트를 수동으로 제거 하려면 [에이전트 제거](../azure-monitor/agents/agent-manage.md#uninstall-agent)를 참조 하세요.

   :::image type="content" source="./media/extension-based-hybrid-runbook-worker-install/delete-machine-from-group.png" alt-text="기존 그룹에서 가상 컴퓨터를 삭제 합니다.":::

   > [!NOTE]
   > 하이브리드 작업자는 **에이전트 기반 (V1)** 및 **확장 기반 (V2)** 의 두 플랫폼과 함께 존재할 수 있습니다. 이미 **에이전트 기반 (V1)** 을 실행 하는 하이브리드 작업자에 **확장 기반 (V2)** 을 설치 하는 경우 그룹에 Hybrid Runbook Worker의 두 항목이 표시 됩니다. 하나는 플랫폼 **확장 기반 (V2)** 및 기타 **에이전트 기반 (V1)** 입니다.

## <a name="delete-a-hybrid-runbook-worker-group"></a>Hybrid Runbook Worker 그룹 삭제

포털에서 빈 Hybrid Runbook Worker 그룹을 삭제할 수 있습니다.

1. **프로세스 자동화** 에서 **hybrid worker 그룹** 을 선택 하 고 hybrid worker 그룹을 선택 하 여 **Hybrid Worker 그룹** 페이지로 이동 합니다.

1. **삭제** 를 선택합니다. Hybrid worker 그룹에서 hybrid worker로 정의 된 모든 컴퓨터를 제거 하는 경고가 대화 상자에 표시 됩니다. 그룹에 이미 추가 된 worker가 있는 경우 먼저 그룹에서 작업자를 삭제 해야 합니다.

1. **Yes** 를 선택합니다. Hybrid worker 그룹이 삭제 됩니다.

## <a name="use-azure-resource-manager-template"></a>Azure Resource Manager 템플릿 사용

ARM (Azure Resource Manager) 템플릿을 사용 하 여 새 Azure Windows VM을 만들고 기존 Automation 계정 및 Hybrid Worker 그룹에 연결할 수 있습니다. ARM 템플릿에 대 한 자세한 내용은 [arm 템플릿 이란?](../azure-resource-manager/templates/overview.md) 을 참조 하세요.

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

- [hybridRunbookWorkerGroups/hybridRunbookWorkers](/azure/templates/microsoft.automation/automationaccounts/hybridrunbookworkergroups/hybridrunbookworkers)
- [Microsoft.Compute/virtualMachines/extensions](/azure/templates/microsoft.compute/virtualmachines/extensions)

### <a name="review-parameters"></a>매개 변수 검토

이 템플릿에 사용 된 매개 변수를 검토 합니다.

|속성 |Description |
|---|---|
|automationAccount| 기존 Automation 계정의 이름입니다. |
|automationAccountLocation | 기존 Automation 계정의 지역입니다. |
|서 그룹 | 기존 Hybrid Worker 그룹의 이름입니다. |
|virtualMachineName| 만들 VM의 이름입니다. 기본값은 `simple-vm`입니다.|
|adminUsername| VM 관리 사용자 이름입니다. |
|adminPassword| VM 관리자 암호입니다. |
|vmLocation| 새 VM의 지역입니다. 기본값은 `North Central US`입니다.|
|vmSize| 새 VM의 크기입니다. 기본값은 `Standard_DS1_v2`입니다. |
|osVersion| 새 Windows VM에 대 한 OS입니다. 기본값은 `2019-Datacenter`입니다.|
|dnsNameForPublicIP| 공용 IP에 대 한 DNS 이름입니다. |


## <a name="next-steps"></a>다음 단계

* 온-프레미스 데이터 센터 또는 다른 클라우드 환경의 프로세스를 자동화하도록 Runbook을 구성하는 방법을 알아보려면 [Hybrid Runbook Worker에서 Runbook 실행](automation-hrw-run-runbooks.md)을 참조하세요.

* Hybrid Runbook Worker 문제를 해결하는 방법을 알아보려면 [Hybrid Runbook Worker 문제 해결](troubleshoot/hybrid-runbook-worker.md#general)을 참조하세요.
