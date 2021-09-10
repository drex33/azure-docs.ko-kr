---
title: Azure Cosmos DB에서 지속적인 백업 및 특정 시점 복원을 통해 계정 프로비저닝
description: Azure Portal, PowerShell, CLI 및 Resource Manager 템플릿을 사용하는 지속적인 백업 및 특정 시점 복원을 통해 계정을 프로비저닝하는 방법을 알아봅니다.
author: kanshiG
ms.service: cosmos-db
ms.topic: how-to
ms.date: 07/29/2021
ms.author: govindk
ms.reviewer: sngun
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 78996e58111d380778e8d02673f518720250e6fb
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122567320"
---
# <a name="provision-an-azure-cosmos-db-account-with-continuous-backup-and-point-in-time-restore"></a>지속적인 백업 및 특정 시점 복원을 통해 Azure Cosmos DB 계정 프로비저닝 
[!INCLUDE[appliesto-sql-mongodb-api](includes/appliesto-sql-mongodb-api.md)]

Azure Cosmos DB의 특정 시점 복원 기능을 사용하면 컨테이너 내에서 실수로 인한 변경을 복구하거나, 삭제된 계정, 데이터베이스 또는 컨테이너를 복원하거나, 백업이 있던 모든 지역으로 복원할 수 있습니다. 지속적인 백업 모드를 사용하면 지난 30일 이내의 모든 시점으로 복원할 수 있습니다.

이 문서에서는 [Azure Portal](#provision-portal), [PowerShell](#provision-powershell), [CLI](#provision-cli) 및 [Resource Manager 템플릿](#provision-arm-template)을 사용하는 지속적인 백업 및 특정 시점 복원을 통해 계정을 프로비저닝하는 방법을 설명합니다.

> [!NOTE]
> 다음 조건이 충족되는 경우에만 지속적인 백업 모드 계정을 프로비저닝할 수 있습니다.
>
> * 계정이 SQL API 또는 MongoDB용 API 유형인 경우
> * 계정에 단일 쓰기 지역이 있는 경우
> * 계정이 CMK(고객 관리형 키)에서 사용하도록 설정되지 않은 경우

## <a name="provision-using-azure-portal"></a><a id="provision-portal"></a>Azure Portal을 사용하여 프로비저닝

새 Azure Cosmos DB 계정을 만들 때 **백업 정책** 탭에서 **연속** 모드를 선택하여 새 계정에 특정 시점 복원 기능을 사용하도록 설정합니다. 특정 시점 복원을 사용하면 데이터는 새 계정으로 복원되며, 현재는 기존 계정으로 복원할 수 없습니다.

:::image type="content" source="./media/provision-account-continuous-backup/configure-continuous-backup-portal.png" alt-text="지속적인 백업 구성을 사용하여 Azure Cosmos DB 계정 프로비전" border="true" lightbox="./media/provision-account-continuous-backup/configure-continuous-backup-portal.png":::

## <a name="provision-using-azure-powershell"></a><a id="provision-powershell"></a>Azure PowerShell을 사용하여 프로비저닝

계정을 프로비저닝하려면 먼저 [최신 버전의 Azure PowerShell](/powershell/azure/install-az-ps?view=azps-6.2.1&preserve-view=true) 또는 6.2.0 이상 버전을 설치합니다. 다음으로 Azure 계정에 연결하고 다음 명령을 사용하여 필요한 구독을 선택합니다.

1. 다음 명령을사용하여 Azure에 로그인합니다.

   ```azurepowershell
   Connect-AzAccount
   ```

1. 다음 명령을 사용하여 특정 구독을 선택합니다.

   ```azurepowershell
   Select-AzSubscription -Subscription <SubscriptionName>
   ```

#### <a name="sql-api-account"></a><a id="provision-powershell-sql-api"></a>SQL API 계정

지속적인 백업으로 계정을 프로비전하려면 일반 프로비전 명령과 함께 `-BackupPolicyType Continuous` 인수를 추가합니다.

다음 cmdlet은 ‘미국 서부’ 지역의 *MyRG* 리소스 그룹 아래에 만든 지속적인 백업 정책을 사용하는 단일 지역 쓰기 계정 *Pitracct* 의 예입니다.

```azurepowershell

New-AzCosmosDBAccount `
  -ResourceGroupName "MyRG" `
  -Location "West US" `
  -BackupPolicyType Continuous `
  -Name "pitracct" `
  -ApiKind "Sql"
      
```

#### <a name="api-for-mongodb"></a><a id="provision-powershell-mongodb-api"></a>MongoDB용 API

다음 cmdlet은 ‘미국 서부’ 지역의 *MyRG* 리소스 그룹 아래에 만든 지속적인 백업 계정 *Pitracct* 의 예입니다.

```azurepowershell

New-AzCosmosDBAccount `
  -ResourceGroupName "MyRG" `
  -Location "West US" `
  -BackupPolicyType Continuous `
  -Name "Pitracct" `
  -ApiKind "MongoDB" `
  -ServerVersion "3.6"

```

## <a name="provision-using-azure-cli"></a><a id="provision-cli"></a>Azure CLI를 사용하여 프로비전

계정을 프로비저닝하려면 먼저 다음 단계에 따라 Azure CLI를 설치합니다.

1. 최신 버전의 Azure CLI 설치

   * 최신 버전의 [Azure CLI](/cli/azure/install-azure-cli) 또는 2.26.0 이상 버전을 설치합니다.
   * CLI를 이미 설치한 경우 `az upgrade` 명령을 실행하여 최신 버전으로 업데이트합니다. 이 명령은 2.11 이상 CLI 버전에서만 작동합니다. 이전 버전인 경우 위의 링크를 사용하여 최신 버전을 설치하세요.

1. 로그인하고 구독 선택

   * `az login` 명령을 사용하여 Azure 계정에 로그인합니다.
   * `az account set -s <subscriptionguid>` 명령을 사용하여 필요한 구독을 선택합니다.

### <a name="sql-api-account"></a><a id="provision-cli-sql-api"></a>SQL API 계정

SQL API 계정에 지속적인 백업을 프로비전하려면 일반 프로비전 명령과 함께 추가 인수 `--backup-policy-type Continuous`를 전달해야 합니다. 다음 명령은 ‘미국 서부’ 지역의 *MyRG* 리소스 그룹 아래에 만든 지속적인 백업 정책을 사용하는 *Pitracct* 라는 단일 지역 쓰기 계정의 예입니다.

```azurecli-interactive

az cosmosdb create \
  --name Pitracct \
  --resource-group MyRG \
  --backup-policy-type Continuous \
  --default-consistency-level Session \
  --locations regionName="West US"

```

### <a name="api-for-mongodb"></a><a id="provision-cli-mongo-api"></a>MongoDB용 API

다음 명령은 ‘미국 서부’ 지역의 *MyRG* 리소스 그룹 아래에 만든 지속적인 백업 정책을 사용하는 *Pitracct* 라는 단일 지역 쓰기 계정의 예를 보여 줍니다.

```azurecli-interactive

az cosmosdb create \
  --name Pitracct \
  --kind MongoDB \
  --resource-group MyRG \
  --server-version "3.6" \
  --backup-policy-type Continuous \
  --default-consistency-level Session \
  --locations regionName="West US"

```

## <a name="provision-using-resource-manager-template"></a><a id="provision-arm-template"></a>Resource Manager 템플릿을 사용하여 프로비저닝

Azure Resource Manager 템플릿을 사용하여 연속 모드로 Azure Cosmos DB 계정을 배포할 수 있습니다. 계정을 프로비전하기 위해 템플릿을 정의하는 경우 다음 예제와 같이 `backupPolicy` 매개 변수를 포함합니다.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [
    {
      "name": "ademo-pitr1",
      "type": "Microsoft.DocumentDB/databaseAccounts",
      "apiVersion": "2016-03-31",
      "location": "West US",
      "properties": {
        "locations": [
          {
            "locationName": "West US"
          }
        ],
        "backupPolicy": {
          "type": "Continuous"
        },
        "databaseAccountOfferType": "Standard"
      }
    }
  ]
}
```

다음으로 Azure PowerShell 또는 CLI를 사용하여 템플릿을 배포합니다. 다음 예제에서는 CLI 명령을 사용하여 템플릿을 배포하는 방법을 보여줍니다.

```azurecli-interactive
az group deployment create -g <ResourceGroup> --template-file <ProvisionTemplateFilePath>
```

## <a name="next-steps"></a>다음 단계

* [라이브 또는 삭제된 Azure Cosmos DB 계정 복원](restore-account-continuous-backup.md)
* [정기적 백업에서 지속적인 백업으로 계정을 마이그레이션하는 방법](migrate-continuous-backup.md)
* [지속적인 백업 모드의 리소스 모델](continuous-backup-restore-resource-model.md)
* 지속적인 백업 모드를 사용하여 데이터를 복원하는 데 필요한 [권한을 관리](continuous-backup-restore-permissions.md)합니다.