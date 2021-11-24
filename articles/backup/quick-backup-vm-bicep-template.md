---
title: 빠른 시작 - Bicep 템플릿 VM 백업
description: Bicep 템플릿을 사용하여 가상 머신을 백업하는 방법 알아보기
ms.devlang: azurecli
ms.topic: quickstart
ms.date: 11/17/2021
ms.reviewer: Daya-Patil
ms.custom: mvc, subject-bicepqs, devx-track-azurepowershell, mode-arm
author: v-amallick
ms.service: backup
ms.author: v-amallick
ms.openlocfilehash: 21000ce7b11a2dfded70af963b7ffb956e00ced6
ms.sourcegitcommit: 56235f8694cc5f88db3afcc8c27ce769ecf455b0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133064648"
---
#  <a name="back-up-a-virtual-machine-in-azure-with-a-bicep-template"></a>Bicep 템플릿을 사용하여 Azure에서 가상 머신 백업

[Azure Backup](backup-overview.md)을 사용하면 Azure Portal, PowerShell, CLI, Azure Resource Manager, Bicep 등과 같은 여러 옵션을 사용하여 Azure VM을 백업할 수 있습니다. 이 문서에서는 Azure Bicep 템플릿 및 Azure PowerShell을 사용하여 Azure VM을 백업하는 방법을 설명합니다. 이 빠른 시작에서는 Recovery Services 자격 증명 모음을 만들기 위해 Bicep 템플릿을 배포하는 프로세스에 중점을 둡니다. Bicep 템플릿 개발에 대한 자세한 내용은 [Bicep 설명서](../azure-resource-manager/bicep/deploy-cli.md) 및 [템플릿 참조](/azure/templates/microsoft.recoveryservices/allversions)를 참조하세요.

Bicep은 Azure 리소스를 선언적으로 배포하기 위한 언어입니다. ARM 템플릿(Azure Resource Manager 템플릿)을 개발하는 데 JSON 대신 Bicep을 사용할 수 있습니다. Bicep 구문은 이러한 복잡성을 줄이고 개발 환경을 향상시킵니다. Bicep은 모든 JSON 템플릿 기능을 제공하는 ARM 템플릿 JSON에 대한 투명한 추상화입니다. 배포하는 동안 Bicep CLI는 Bicep 파일을 ARM 템플릿 JSON으로 변환합니다. Bicep 파일은 리소스를 만드는 일련의 프로그래밍 명령을 작성하지 않고 Azure 리소스 및 리소스 속성을 명시합니다.

ARM 템플릿에서 유효한 리소스 종류, API 버전, 속성은 Bicep 파일에서도 유효합니다.

## <a name="prerequisites"></a>필수 구성 요소

Bicep 개발 환경을 설정하려면 [Bicep 도구 설치](../azure-resource-manager/bicep/install.md)를 참조하세요.

>[!Note]
>문서에 설명된 대로 최신 [Azure PowerShell 모듈](/powershell/azure/new-azureps-module-az?view=azps-6.6.0) 및 Bicep CLI를 설치합니다.

## <a name="review-the-template"></a>템플릿 검토

아래에 사용된 템플릿은 [Azure 빠른 시작 템플릿](https://azure.microsoft.com/resources/templates/recovery-services-create-vm-and-configure-backup/)에서 가져옵니다. 이 템플릿을 사용하면 _보호_ 용 _DefaultPolicy_ 로 구성된 간단한 Windows VM 및 Recovery Services 자격 증명 모음을 배포할 수 있습니다.

```json
@description('Specifies a name for generating resource names.')
@maxLength(8)
param projectName string

@description('Specifies the location for all resources.')
param location string = resourceGroup().location

@description('Specifies the administrator username for the Virtual Machine.')
param adminUsername string

@description('Specifies the administrator password for the Virtual Machine.')
@secure()
param adminPassword string

@description('Specifies the unique DNS Name for the Public IP used to access the Virtual Machine.')
param dnsLabelPrefix string

@description('Virtual machine size.')
param vmSize string = 'Standard_A2'

@description('Specifies the Windows version for the VM. This will pick a fully patched image of this given Windows version.')
@allowed([
  '2008-R2-SP1'
  '2012-Datacenter'
  '2012-R2-Datacenter'
  '2016-Nano-Server'
  '2016-Datacenter-with-Containers'
  '2016-Datacenter'
  '2019-Datacenter'
  '2019-Datacenter-Core'
  '2019-Datacenter-Core-smalldisk'
  '2019-Datacenter-Core-with-Containers'
  '2019-Datacenter-Core-with-Containers-smalldisk'
  '2019-Datacenter-smalldisk'
  '2019-Datacenter-with-Containers'
  '2019-Datacenter-with-Containers-smalldisk'
])
param windowsOSVersion string = '2016-Datacenter'

var storageAccountName = '${projectName}store'
var networkInterfaceName = '${projectName}-nic'
var vNetAddressPrefix = '10.0.0.0/16'
var vNetSubnetName = 'default'
var vNetSubnetAddressPrefix = '10.0.0.0/24'
var publicIPAddressName = '${projectName}-ip'
var vmName = '${projectName}-vm'
var vNetName = '${projectName}-vnet'
var vaultName = '${projectName}-vault'
var backupFabric = 'Azure'
var backupPolicyName = 'DefaultPolicy'
var protectionContainer = 'iaasvmcontainer;iaasvmcontainerv2;${resourceGroup().name};${vmName}'
var protectedItem = 'vm;iaasvmcontainerv2;${resourceGroup().name};${vmName}'
var networkSecurityGroupName = 'default-NSG'

resource storageAccount 'Microsoft.Storage/storageAccounts@2021-06-01' = {
  name: storageAccountName
  location: location
  sku: {
    name: 'Standard_LRS'
  }
  kind: 'Storage'
  properties: {}
}

resource publicIPAddress 'Microsoft.Network/publicIPAddresses@2020-06-01' = {
  name: publicIPAddressName
  location: location
  properties: {
    publicIPAllocationMethod: 'Dynamic'
    dnsSettings: {
      domainNameLabel: dnsLabelPrefix
    }
  }
}

resource networkSecurityGroup 'Microsoft.Network/networkSecurityGroups@2020-06-01' = {
  name: networkSecurityGroupName
  location: location
  properties: {
    securityRules: [
      {
        name: 'default-allow-3389'
        properties: {
          priority: 1000
          access: 'Allow'
          direction: 'Inbound'
          destinationPortRange: '3389'
          protocol: 'Tcp'
          sourceAddressPrefix: '*'
          sourcePortRange: '*'
          destinationAddressPrefix: '*'
        }
      }
    ]
  }
}

resource vNet 'Microsoft.Network/virtualNetworks@2020-06-01' = {
  name: vNetName
  location: location
  properties: {
    addressSpace: {
      addressPrefixes: [
        vNetAddressPrefix
      ]
    }
    subnets: [
      {
        name: vNetSubnetName
        properties: {
          addressPrefix: vNetSubnetAddressPrefix
          networkSecurityGroup: {
            id: networkSecurityGroup.id
          }
        }
      }
    ]
  }
}

resource networkInterface 'Microsoft.Network/networkInterfaces@2020-06-01' = {
  name: networkInterfaceName
  location: location
  properties: {
    ipConfigurations: [
      {
        name: 'ipconfig1'
        properties: {
          privateIPAllocationMethod: 'Dynamic'
          publicIPAddress: {
            id: publicIPAddress.id
          }
          subnet: {
            id: '${vNet.id}/subnets/${vNetSubnetName}'
          }
        }
      }
    ]
  }
}

resource virtualMachine 'Microsoft.Compute/virtualMachines@2020-06-01' = {
  name: vmName
  location: location
  properties: {
    hardwareProfile: {
      vmSize: vmSize
    }
    osProfile: {
      computerName: vmName
      adminUsername: adminUsername
      adminPassword: adminPassword
    }
    storageProfile: {
      imageReference: {
        publisher: 'MicrosoftWindowsServer'
        offer: 'WindowsServer'
        sku: windowsOSVersion
        version: 'latest'
      }
      osDisk: {
        createOption: 'FromImage'
      }
      dataDisks: [
        {
          diskSizeGB: 1023
          lun: 0
          createOption: 'Empty'
        }
      ]
    }
    networkProfile: {
      networkInterfaces: [
        {
          id: networkInterface.id
        }
      ]
    }
    diagnosticsProfile: {
      bootDiagnostics: {
        enabled: true
        storageUri: storageAccount.properties.primaryEndpoints.blob
      }
    }
  }
}

resource recoveryServicesVault 'Microsoft.RecoveryServices/vaults@2020-02-02' = {
  name: vaultName
  location: location
  sku: {
    name: 'RS0'
    tier: 'Standard'
  }
  properties: {}
}

resource vaultName_backupFabric_protectionContainer_protectedItem 'Microsoft.RecoveryServices/vaults/backupFabrics/protectionContainers/protectedItems@2020-02-02' = {
  name: '${vaultName}/${backupFabric}/${protectionContainer}/${protectedItem}'
  properties: {
    protectedItemType: 'Microsoft.Compute/virtualMachines'
    policyId: '${recoveryServicesVault.id}/backupPolicies/${backupPolicyName}'
    sourceResourceId: virtualMachine.id
  }
} 

```

위 템플릿에 정의된 리소스는 다음과 같습니다.

- [Microsoft.Storage/storageAccounts](/azure/templates/microsoft.storage/storageaccounts)
- [Microsoft.Network/publicIPAddresses](/azure/templates/microsoft.network/publicipaddresses)
- [Microsoft.Network/networkSecurityGroups](/azure/templates/microsoft.network/networksecuritygroups)
- [Microsoft.Network/virtualNetworks](/azure/templates/microsoft.network/virtualnetworks)
- [Microsoft.Network/networkInterfaces](/azure/templates/microsoft.network/networkinterfaces)
- [Microsoft.Compute/virtualMachines](/azure/templates/microsoft.compute/virtualmachines)
- [Microsoft.RecoveryServices/vaults](/azure/templates/microsoft.recoveryservices/2016-06-01/vaults)
- [Microsoft.RecoveryServices/vaults/backupFabrics/protectionContainers/protectedItems](/azure/templates/microsoft.recoveryservices/vaults/backupfabrics/protectioncontainers/protecteditems)

## <a name="deploy-the-template"></a>템플릿 배포

템플릿을 배포하려면 _사용해 보세요_ 를 선택하여 Azure Cloud Shell을 열고, 다음 PowerShell 스크립트를 셸 창에 붙여넣습니다. 코드를 붙여넣으려면 셸 창을 마우스 오른쪽 단추로 클릭하고 **붙여넣기** 를 선택합니다.

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter a project name (limited to eight characters) that is used to generate Azure resource names"
$location = Read-Host -Prompt "Enter the location (for example, centralus)"
$adminUsername = Read-Host -Prompt "Enter the administrator username for the virtual machine"
$adminPassword = Read-Host -Prompt "Enter the administrator password for the virtual machine" -AsSecureString
$dnsPrefix = Read-Host -Prompt "Enter the unique DNS Name for the Public IP used to access the virtual machine"

$resourceGroupName = "${projectName}rg"
$templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/quickstarts/microsoft.recoveryservices/recovery-services-create-vm-and-configure-backup/main.bicep"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri -projectName $projectName -adminUsername $adminUsername -adminPassword $adminPassword -dnsLabelPrefix $dnsPrefix 

```

## <a name="validate-the-deployment"></a>배포 유효성 검사

### <a name="start-a-backup-job"></a>백업 작업 시작

템플릿은 VM을 만들고 VM에서 백업을 사용하도록 설정합니다. 템플릿을 배포한 후에는 백업 작업을 시작해야 합니다. 자세한 내용은 [백업 작업 시작](quick-backup-vm-powershell.md#start-a-backup-job)을 참조하세요.

### <a name="monitor-the-backup-job"></a>백업 작업 모니터링

백업 작업을 모니터링 하려면 [백업 작업 모니터링](quick-backup-vm-powershell.md#monitor-the-backup-job)을 참조하세요.

## <a name="clean-up-resources"></a>리소스 정리

- VM을 더 이상 백업할 필요가 없으면 이를 정리할 수 있습니다.
- VM 복원을 시도하려면 정리 프로세스를 건너뜁니다.
- 기존 VM을 사용한 경우 마지막 [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) cmdlet을 건너뛰어 리소스 그룹과 VM을 유지할 수 있습니다.

다음 단계를 수행합니다.

1. 보호 기능을 해제하고 복원 지점 및 자격 증명 모음을 제거합니다.

1. 리소스 그룹 및 연결된 VM 리소스를 다음과 같이 삭제합니다.

   ```azurepowershell
   Disable-AzRecoveryServicesBackupProtection -Item $item -RemoveRecoveryPoints
   $vault = Get-AzRecoveryServicesVault -Name "myRecoveryServicesVault"
   Remove-AzRecoveryServicesVault -Vault $vault
   Remove-AzResourceGroup -Name "myResourceGroup" 

   ```

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 Recovery Services 자격 증명 모음을 만들고, VM에 대한 보호를 사용하도록 설정하고, 초기 복구 지점을 만들었습니다.

- Azure Portal에서 VM을 백업하는 방법을 [알아봅니다](tutorial-backup-vm-at-scale.md).
- VM을 빠르게 복원하는 방법을 [알아봅니다](tutorial-restore-disk.md).
- Bicep 템플릿을 만드는 [방법을 알아봅니다](../azure-resource-manager/bicep/quickstart-create-bicep-use-visual-studio-code.md?tabs=CLI).
- ARM 템플릿(Azure Resource Manager 템플릿)을 Bicep 파일로 디컴파일하는 [방법을 알아봅니다](../azure-resource-manager/bicep/decompile.md?tabs=azure-cli).
