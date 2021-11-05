---
title: '빠른 시작: Azure DB for MySQL - 유연한 서버 만들기 - ARM 템플릿'
description: 이 빠른 시작에서는 ARM 템플릿을 사용하여 Azure Database for MySQL 유연한 서버를 만드는 방법에 대해 알아봅니다.
author: mksuni
ms.service: mysql
ms.topic: quickstart
ms.custom: subject-armqs, devx-track-azurepowershell
ms.author: sumuth
ms.date: 10/23/2020
ms.openlocfilehash: a53c532d4f95edfea9aaa4ae96a9d0a92d9c9b10
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131425390"
---
# <a name="quickstart-use-an-arm-template-to-create-an-azure-database-for-mysql---flexible-server"></a>빠른 시작 - ARM 템플릿을 사용하여 Azure Database for MySQL - 유연한 서버 만들기

[[!INCLUDE[applies-to-mysql-flexible-server](../includes/applies-to-mysql-flexible-server.md)]

Azure Database for MySQL - 유연한 서버는 클라우드에서 고가용성 MySQL 데이터베이스를 실행, 관리 및 확장하는 데 사용하는 관리형 서비스입니다. ARM 템플릿(Azure Resource Manager 템플릿)을 사용하여 유연한 서버를 프로비저닝하여 여러 서버 또는 서버 상 여러 데이터베이스를 배포할 수 있습니다.

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

## <a name="prerequisites"></a>사전 요구 사항

활성 구독이 있는 Azure 계정. 

[!INCLUDE [flexible-server-free-trial-note](../includes/flexible-server-free-trial-note.md)]

## <a name="review-the-template"></a>템플릿 검토

Azure Database for MySQL 유연한 서버는 한 지역 내에서 하나 이상의 데이터베이스에 대한 부모 리소스입니다. 로그인, 방화벽, 사용자, 역할, 구성 등 해당 데이터베이스에 적용되는 관리 정책에 대한 범위를 제공합니다.

_mysql-flexible-server-template.json_ 파일을 만들고 여기에 이 JSON 스크립트를 복사합니다.

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "administratorLogin": {
      "type": "String"
    },
    "administratorLoginPassword": {
      "type": "SecureString"
    },
    "location": {
      "type": "String"
    },
    "serverName": {
      "type": "String"
    },
    "serverEdition": {
      "type": "String"
    },
    "storageSizeMB": {
      "type": "Int"
    },
    "haEnabled": {
      "type": "string",
      "defaultValue": "Disabled"
    },
    "availabilityZone": {
      "type": "String"
    },
    "version": {
      "type": "String"
    },
    "tags": {
      "defaultValue": {},
      "type": "Object"
    },
    "firewallRules": {
      "defaultValue": {},
      "type": "Object"
    },
    "vnetData": {
      "defaultValue": {},
      "type": "Object"
    },
    "backupRetentionDays": {
      "type": "Int"
    }
  },
  "variables": {
    "api": "2021-05-01",
    "firewallRules": "[parameters('firewallRules').rules]",
    "publicNetworkAccess": "[if(empty(parameters('vnetData')), 'Enabled', 'Disabled')]",
    "vnetDataSet": "[if(empty(parameters('vnetData')), json('{ \"subnetArmResourceId\": \"\" }'), parameters('vnetData'))]",
    "finalVnetData": "[json(concat('{ \"subnetArmResourceId\": \"', variables('vnetDataSet').subnetArmResourceId, '\"}'))]"
  },
  "resources": [
    {
      "type": "Microsoft.DBforMySQL/flexibleServers",
      "apiVersion": "[variables('api')]",
      "name": "[parameters('serverName')]",
      "location": "[parameters('location')]",
      "sku": {
        "name": "Standard_D4ds_v4",
        "tier": "[parameters('serverEdition')]"
      },
      "tags": "[parameters('tags')]",
      "properties": {
        "version": "[parameters('version')]",
        "administratorLogin": "[parameters('administratorLogin')]",
        "administratorLoginPassword": "[parameters('administratorLoginPassword')]",
        "publicNetworkAccess": "[variables('publicNetworkAccess')]",
        "DelegatedSubnetArguments": "[if(empty(parameters('vnetData')), json('null'), variables('finalVnetData'))]",
        "haEnabled": "[parameters('haEnabled')]",
        "storageProfile": {
          "storageMB": "[parameters('storageSizeMB')]",
          "backupRetentionDays": "[parameters('backupRetentionDays')]"
        },
        "availabilityZone": "[parameters('availabilityZone')]"
      }
    },
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2019-08-01",
      "name": "[concat('firewallRules-', copyIndex())]",
      "dependsOn": [
        "[concat('Microsoft.DBforMySQL/flexibleServers/', parameters('serverName'))]"
      ],
      "properties": {
        "mode": "Incremental",
        "template": {
          "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "resources": [
            {
              "type": "Microsoft.DBforMySQL/flexibleServers/firewallRules",
              "name": "[concat(parameters('serverName'),'/',variables('firewallRules')[copyIndex()].name)]",
              "apiVersion": "[variables('api')]",
              "properties": {
                "StartIpAddress": "[variables('firewallRules')[copyIndex()].startIPAddress]",
                "EndIpAddress": "[variables('firewallRules')[copyIndex()].endIPAddress]"
              }
            }
          ]
        }
      },
      "copy": {
        "name": "firewallRulesIterator",
        "count": "[if(greater(length(variables('firewallRules')), 0), length(variables('firewallRules')), 1)]",
        "mode": "Serial"
      },
      "condition": "[greater(length(variables('firewallRules')), 0)]"
    }
  ]
}
```

이러한 리소스는 템플릿에 정의되어 있습니다.

- Microsoft.DBforMySQL/flexibleServers

## <a name="deploy-the-template"></a>템플릿 배포

다음 PowerShell 코드 블록에서 **사용해 보세요** 를 선택하여 [Azure Cloud Shell](../../cloud-shell/overview.md)을 엽니다.

```azurepowershell-interactive
$serverName = Read-Host -Prompt "Enter a name for the new Azure Database for MySQL server"
$resourceGroupName = Read-Host -Prompt "Enter a name for the new resource group where the server will exist"
$location = Read-Host -Prompt "Enter an Azure region (for example, centralus) for the resource group"
$adminUser = Read-Host -Prompt "Enter the Azure Database for MySQL server's administrator account name"
$adminPassword = Read-Host -Prompt "Enter the administrator password" -AsSecureString

New-AzResourceGroup -Name $resourceGroupName -Location $location # Use this command when you need to create a new resource group for your deployment
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName `
    -TemplateFile "D:\Azure\Templates\EngineeringSite.json
    -serverName $serverName `
    -administratorLogin $adminUser `
    -administratorLoginPassword $adminPassword

Read-Host -Prompt "Press [ENTER] to continue ..."
```

## <a name="review-deployed-resources"></a>배포된 리소스 검토

Azure에서 서버가 생성되었는지 확인하려면 다음 단계를 수행합니다.

### <a name="azure-portal"></a>Azure portal

1. [Azure Portal](https://portal.azure.com)에서 **Azure Database for MySQL 서버** 를 검색하여 선택합니다.
1. 데이터베이스 목록에서 새 서버를 선택합니다. 그러면 새 Azure Database for MySQL 서버에 대한 **개요** 페이지가 표시됩니다.

### <a name="powershell"></a>PowerShell

Azure Database for MySQL 유연한 서버에 대한 자세한 내용을 보려면 새 서버의 이름을 입력해야 합니다.

```azurepowershell-interactive
$serverName = Read-Host -Prompt "Enter the name of your Azure Database for MySQL server"
Get-AzResource -ResourceType "Microsoft.DBforMySQL/flexibleServers" -Name $serverName | ft
Write-Host "Press [ENTER] to continue..."
```

### <a name="cli"></a>CLI

Azure Database for MySQL 유연한 서버에 대한 자세한 내용을 보려면 새 서버의 이름 및 리소스 그룹을 입력해야 합니다.

```azurecli-interactive
echo "Enter your Azure Database for MySQL server name:" &&
read serverName &&
echo "Enter the resource group where the Azure Database for MySQL server exists:" &&
read resourcegroupName &&
az resource show --resource-group $resourcegroupName --name $serverName --resource-type "Microsoft.DbForMySQL/flexibleServers"
```

## <a name="clean-up-resources"></a>리소스 정리

[다음 단계](#next-steps)로 이동하려면 이 리소스 그룹, 서버 및 단일 데이터베이스를 그대로 유지합니다. 다음 단계에서는 다른 메서드를 사용하여 데이터베이스를 연결하고 쿼리하는 방법을 보여줍니다.

리소스 그룹을 삭제하려면 다음을 수행합니다.

### <a name="azure-portal"></a>Azure portal

1. [Azure Portal](https://portal.azure.com)에서 **리소스 그룹** 을 검색하여 선택합니다.
1. 리소스 그룹 목록에서 리소스 그룹의 이름을 선택합니다.
1. 리소스 그룹의 **개요** 페이지에서 **리소스 그룹 삭제** 를 선택합니다.
1. 확인 대화 상자에서 리소스 그룹의 이름을 입력한 다음, **삭제** 를 선택합니다.

### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
Remove-AzResourceGroup -Name $resourceGroupName
Write-Host "Press [ENTER] to continue..."
```

### <a name="cli"></a>CLI

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName &&
echo "Press [ENTER] to continue ..."
```
---

## <a name="next-steps"></a>다음 단계

ARM 템플릿을 만드는 과정을 안내하는 단계별 자습서는 다음을 참조하세요.

> [!div class="nextstepaction"]
> 첫 번째 ARM 템플릿 만들기 및 배포[

MySQL을 사용하여 App Service를 통해 앱을 빌드하는 단계별 자습서는 다음을 참조하세요.

> [!div class="nextstepaction"]
>[MySQL로 PHP(Laravel) 웹앱 빌드](tutorial-php-database-app.md)
