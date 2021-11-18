---
title: Azure Cosmos DB 정기 백업 계정에 대한 백업 스토리지 중복 업데이트
description: Azure CLI 및 PowerShell을 사용하여 백업 스토리지 중복성을 업데이트하는 방법을 알아봅니다. 계정에 Azure 정책을 구성하여 필요한 스토리지 중복성을 적용할 수도 있습니다.
author: kanshiG
ms.service: cosmos-db
ms.topic: how-to
ms.date: 11/16/2021
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: af39691955425831f7a68b0d1dc885994cdc5262
ms.sourcegitcommit: 1244a72dbec39ac8cf16bb1799d8c46bde749d47
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/18/2021
ms.locfileid: "132765139"
---
# <a name="update-backup-storage-redundancy-for-azure-cosmos-db-periodic-backup-accounts"></a>Azure Cosmos DB 정기 백업 계정에 대한 백업 스토리지 중복 업데이트
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

기본적으로 Azure Cosmos DB는 [쌍을 이루는 지역](../best-practices-availability-paired-regions.md)으로 복제되는 지역 중복 [Blob Storage](../storage/common/storage-redundancy.md)에 정기 모드 백업 데이터를 저장합니다. 기본 백업 스토리지 중복성을 재정의할 수 있습니다. 이 문서에서는 Azure CLI 및 PowerShell을 사용하여 백업 스토리지 중복성을 업데이트하는 방법을 설명합니다. 또한 계정에서 Azure 정책을 구성하여 필요한 스토리지 중복성을 적용하는 방법을 보여 줍니다.

## <a name="update-using-cli"></a>CLI를 사용하여 업데이트

Azure CLI 사용하여 기존 계정의 백업 스토리지 중복성을 업데이트하려면 다음 단계를 사용합니다.

1. Azure CLI 또는 2.30.0보다 높거나 같은 버전인 경우 최신 버전을 설치합니다. "cosmosdb-preview" 확장이 설치된 경우 제거해야 합니다.

1. 다음 명령을 사용하여 계정이 있는 지역에서 사용할 수 있는 백업 중복 옵션을 얻을 수 있습니다.

   ```azurecli-interactive
   az cosmosdb locations show --location <region_Name>
   ```

   ```bash
    {
     "id": "subscriptionId/<Subscription_ID>/providers/Microsoft.DocumentDB/locations/eastus/",
     "name": "East US",
     "properties": {
       "backupStorageRedundancies": [
         "Geo",
         "Zone",
         "Local"
       ],
       "isResidencyRestricted": false,
       "supportsAvailabilityZone": true
     },
     "type": "Microsoft.DocumentDB/locations"
    }
   ```

   이전 명령은 특정 지역에서 사용할 수 있는 백업 중복 목록입니다. 지원되는 값은 `backupStorageRedundancies` 속성에 표시됩니다. 예를 들어 "미국 동부"와 같은 일부 지역에서는 세 가지 중복 옵션 "Geo", "Zone" 및 "Local"을 지원하는 반면, "아랍에미리트 북부"와 같은 일부 지역은 "지역" 및 "로컬" 중복 옵션만 지원합니다. 업데이트하기 전에 계정이 있는 모든 지역에서 지원되는 백업 스토리지 중복 옵션을 선택합니다.

1. 선택한 백업 중복 옵션을 사용하여 다음 명령을 실행하여 기존 계정에서 백업 중복성을 업데이트합니다.

   ```azurecli-interactive
   az cosmosdb update -n <account_Name> -g <resource_Group> --backup-redundancy "Geo"
   ```

1. 다음 명령을 실행하여 선택한 백업 중복 옵션을 사용하여 새 계정을 만듭니다.

   ```azurecli-interactive
   az cosmosdb create -n <account_Name> -g <resource_Group> --backup-redundancy "Geo" --locations regionName=westus
   ```

## <a name="update-using-powershell"></a>PowerShell을 사용하여 업데이트

1. 최신 버전의 Azure PowerShell 또는 1.4.0 이상 버전을 설치합니다.

   ```powershell-interactive
   Install-Module -Name Az.CosmosDB -RequiredVersion 1.4.0
   ```

1. 다음 명령을 사용하여 계정이 있는 지역에서 사용할 수 있는 백업 중복 옵션을 얻을 수 있습니다.

   ```powershell-interactive
   $location = Get-AzCosmosDBLocation -Location <region_Name>
   $location.Properties.BackupStorageRedundancies
   ```

   이전 명령은 특정 지역에서 사용할 수 있는 백업 중복 목록입니다. 지원되는 값은 `backupStorageRedundancies` 속성에 표시됩니다. 예를 들어 "미국 동부"와 같은 일부 지역에서는 세 가지 중복 옵션 "Geo", "Zone" 및 "Local"을 지원하는 반면, "아랍에미리트 북부"와 같은 일부 지역은 "지역" 및 "로컬" 중복 옵션만 지원합니다. 업데이트하기 전에 계정이 있는 모든 지역에서 지원되는 백업 스토리지 중복 옵션을 선택합니다.

1. 선택한 백업 중복 옵션을 사용하여 다음 명령을 실행하여 기존 계정에서 백업 중복성을 업데이트합니다.

   ```powershell-interactive
   Update-AzCosmosDBAccount `
   -Name <account_Name> `
   -ResourceGroupName <resource_Group> `
   -BackupStorageRedundancy "Geo"
   ```

1. 다음 명령을 실행하여 선택한 백업 중복 옵션을 사용하여 새 계정을 만듭니다.

   ```powershell-interactive
   New-AzCosmosDBAccount `
    -Name <account_Name> `
    -ResourceGroupName <resource_Group> `
    -Location <region_Name> `
    -BackupPolicyType Periodic`
    -BackupStorageRedundancy "Geo"

   ```

## <a name="add-a-policy-for-the-backup-storage-redundancy"></a>백업 스토리지 중복에 대한 정책 추가

Azure Policy 조직 표준을 적용하고 대규모 규정 준수를 평가하는 데 도움이 됩니다. 다음 샘플에서는 데이터베이스 계정에 대한 Azure 정책을 추가하여 "영역" 유형의 백업 중복성을 갖는 방법을 보여 있습니다.

```json
"parameters": {},
    "policyRule": {
      "if": {
        "allOf": [
          {
            "field": "Microsoft.DocumentDB/databaseAccounts/backupPolicy.periodicModeProperties.backupStorageRedundancy",
            "match": "Zone"
          }
        ]
      },
      "then": {
        "effect": "audit"
      }
    }
```

## <a name="next-steps"></a>다음 단계

* [주기적 백업 모드'(configure-periodic-backup-restore.md)를 통해 Azure Cosmos DB 계정을 프로비전합니다.
* Azure Portal , [PowerShell,](provision-account-continuous-backup.md#provision-powershell) [CLI](provision-account-continuous-backup.md#provision-cli) [또는](provision-account-continuous-backup.md#provision-arm-template) [Azure Resource Manager](provision-account-continuous-backup.md#provision-portal)사용하여 연속 백업이 있는 계정을 프로비전합니다.
* [Azure Portal](restore-account-continuous-backup.md#restore-account-portal), [PowerShell](restore-account-continuous-backup.md#restore-account-powershell), [CLI](restore-account-continuous-backup.md#restore-account-cli) 또는 [Azure Resource Manager](restore-account-continuous-backup.md#restore-arm-template)를 사용하여 지속적인 백업 계정을 복원합니다.