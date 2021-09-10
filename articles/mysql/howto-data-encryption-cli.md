---
title: 데이터 암호화 - Azure CLI - Azure Database for MySQL
description: Azure CLI를 사용하여 Azure Database for MySQL에 대한 데이터 암호화를 설정 및 관리하는 방법을 알아봅니다.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: how-to
ms.date: 03/30/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 2661e73b5ef1a820d28d71b93fd96db034e8daf6
ms.sourcegitcommit: 98e126b0948e6971bd1d0ace1b31c3a4d6e71703
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/26/2021
ms.locfileid: "114675336"
---
# <a name="data-encryption-for-azure-database-for-mysql-by-using-the-azure-cli"></a>Azure CLI를 사용하여 Azure Database for MySQL에 대한 데이터 암호화

[!INCLUDE[applies-to-mysql-single-server](includes/applies-to-mysql-single-server.md)]

Azure CLI를 사용하여 Azure Database for MySQL 데이터 암호화를 설정하고 관리하는 방법을 알아봅니다.

## <a name="prerequisites-for-azure-cli"></a>Azure CLI의 필수 조건

* Azure 구독 및 해당 구독에 대한 관리자 권한이 있어야 합니다.
* 고객 관리형 키에 사용할 키 자격 증명 모음 및 키를 만듭니다. 또한 키 자격 증명 모음에서 제거 방지 및 일시 삭제를 사용하도록 설정합니다.

  ```azurecli-interactive
  az keyvault create -g <resource_group> -n <vault_name> --enable-soft-delete true --enable-purge-protection true
  ```

* 만든 Azure Key Vault에서 Azure Database for MySQL의 데이터 암호화에 사용할 키를 만듭니다.

  ```azurecli-interactive
  az keyvault key create --name <key_name> -p software --vault-name <vault_name>
  ```

* 기존 키 자격 증명 모음을 사용하려면 고객 관리형 키로 사용할 다음 속성이 있어야 합니다.

  * [일시 삭제](../key-vault/general/soft-delete-overview.md)

    ```azurecli-interactive
    az resource update --id $(az keyvault show --name \ <key_vault_name> -o tsv | awk '{print $1}') --set \ properties.enableSoftDelete=true
    ```

  * [제거 보호](../key-vault/general/soft-delete-overview.md#purge-protection)

    ```azurecli-interactive
    az keyvault update --name <key_vault_name> --resource-group <resource_group_name>  --enable-purge-protection true
    ```
  * 보존 일수를 90일로 설정
  ```azurecli-interactive
    az keyvault update --name <key_vault_name> --resource-group <resource_group_name>  --retention-days 90
    ```

* 고객 관리형 키로 사용하려면 키에 다음 특성이 있어야 합니다.
  * 만료 날짜 없음
  * 사용 안 함 없음
  * **가져오기**, **래핑**, **래핑 해제** 작업 수행
  * recoverylevel 특성이 **복구 가능** 으로 설정됨(보존 기간이 90일로 설정된 일시 삭제를 사용해야 함)
  * 제거 보호 사용

다음 명령을 사용하여 키의 특성을 확인할 수 있습니다.

```azurecli-interactive
az keyvault key show --vault-name <key_vault_name> -n <key_name>
```
* Azure Database for MySQL - 단일 서버는 범용 또는 메모리 최적화 가격 책정 계층 및 범용 스토리지 v2에 있어야 합니다. 계속 진행하기 전에 [고객 관리형 키를 사용한 데이터 암호화](concepts-data-encryption-mysql.md#limitations) 제한 사항을 참조하세요.

## <a name="set-the-right-permissions-for-key-operations"></a>키 작업에 대한 올바른 사용 권한 설정

1. Azure Database for MySQL에 대한 관리 ID를 가져오는 방법에는 두 가지가 있습니다.

   ### <a name="create-an-new-azure-database-for-mysql-server-with-a-managed-identity"></a>관리 ID를 사용하여 새 Azure Database for MySQL 서버를 만듭니다.

   ```azurecli-interactive
   az mysql server create --name -g <resource_group> --location <locations> --storage-size size>  -u <user>-p <pwd> --backup-retention <7> --sku-name <sku name> -geo-redundant-backup <Enabled/Disabled>  --assign-identity
   ```

   ### <a name="update-an-existing-the-azure-database-for-mysql-server-to-get-a-managed-identity"></a>관리 ID를 가져오기 위해 기존 Azure Database for MySQL 서버를 업데이트합니다.

   ```azurecli-interactive
   az mysql server update --name  <server name>  -g <resource_group> --assign-identity
   ```

2. MySQL 서버의 이름인 **보안 주체** 에 대한 **키 사용 권한**(**가져오기**, **래핑**, **래핑 해제**)을 설정합니다.

    ```azurecli-interactive
    az keyvault set-policy --name -g <resource_group> --key-permissions get unwrapKey wrapKey --object-id <principal id of the server>
    ```

## <a name="set-data-encryption-for-azure-database-for-mysql"></a>Azure Database for MySQL에 대한 데이터 암호화 설정

1. Azure Key Vault에서 만든 키를 사용하여 Azure Database for MySQL에 대한 데이터 암호화를 사용하도록 설정합니다.

    ```azurecli-interactive
    az mysql server key create –name  <server name>  -g <resource_group> --kid <key url>
    ```

    키 url: `https://YourVaultName.vault.azure.net/keys/YourKeyName/01234567890123456789012345678901>`

## <a name="using-data-encryption-for-restore-or-replica-servers"></a>복원 또는 복제본 서버에 데이터 암호화 사용

Key Vault에 저장된 고객 관리형 키를 사용하여 Azure Database for MySQL을 암호화한 후에는 새로 만든 서버 복사본도 암호화됩니다. 로컬 또는 지역 복원 작업을 통해 또는 복제본(로컬/영역 간) 작업을 통해 새 복사본을 만들 수 있습니다. 따라서 암호화된 MySQL 서버의 경우 다음 단계를 사용하여 암호화된 복원된 서버를 만들 수 있습니다.

### <a name="creating-a-restoredreplica-server"></a>복원/복제본 서버 만들기

* [복원 서버 만들기](howto-restore-server-cli.md) 
* [읽기 복제본 서버 만들기](howto-read-replicas-cli.md) 

### <a name="once-the-server-is-restored-revalidate-data-encryption-the-restored-server"></a>서버를 복원하고 나면 복원된 서버의 데이터 암호화 유효성을 다시 검사합니다.

*   복제본 서버에 대한 ID를 할당합니다.
```azurecli-interactive
az mysql server update --name  <server name>  -g <resoure_group> --assign-identity
```

*   복원/복제본 서버에 사용해야 하는 기존 키를 가져옵니다.

```azurecli-interactive
az mysql server key list --name  '<server_name>'  -g '<resource_group_name>'
```

*   복원/복제본 서버의 새 ID에 대한 정책을 설정합니다.
  
```azurecli-interactive
az keyvault set-policy --name <keyvault> -g <resoure_group> --key-permissions get unwrapKey wrapKey --object-id <principl id of the server returned by the step 1>
```

* 암호화 키를 사용하여 복원/복제본 서버의 유효성을 다시 검사합니다.

```azurecli-interactive
az mysql server key create –name  <server name> -g <resource_group> --kid <key url>
```

## <a name="additional-capability-for-the-key-being-used-for-the-azure-database-for-mysql"></a>Azure Database for MySQL에 사용되는 키에 대한 추가 기능

### <a name="get-the-key-used"></a>사용된 키를 가져옵니다.

```azurecli-interactive
az mysql server key show --name  <server name>  -g <resource_group> --kid <key url>
```

키 url: `https://YourVaultName.vault.azure.net/keys/YourKeyName/01234567890123456789012345678901>`

### <a name="list-the-key-used"></a>사용된 키를 나열합니다.

```azurecli-interactive
az mysql server key list --name  <server name>  -g <resource_group>
```

### <a name="drop-the-key-being-used"></a>사용 중인 키를 드롭합니다.

```azurecli-interactive
az mysql server key delete -g <resource_group> --kid <key url>
```

## <a name="using-an-azure-resource-manager-template-to-enable-data-encryption"></a>Azure Resource Manager 템플릿을 사용하여 데이터 암호화 사용

Azure Portal 외에도 새 서버 및 기존 서버에 대해 Azure Resource Manager 템플릿을 사용하여 Azure Database for MySQL 서버에서 데이터 암호화를 사용하도록 설정할 수 있습니다.

### <a name="for-a-new-server"></a>새 서버의 경우

미리 만든 Azure Resource Manager 템플릿 중 하나를 사용하여 데이터 암호화를 사용하도록 설정한 서버를 프로비저닝합니다. [데이터 암호화 사용 예](https://github.com/Azure/azure-mysql/tree/master/arm-templates/ExampleWithDataEncryption)

이 Azure Resource Manager 템플릿은 Azure Database for MySQL 서버를 만들고 매개 변수로 전달되는 **KeyVault** 및 **키** 를 사용하여 서버에서 데이터 암호화를 사용하도록 설정합니다.

### <a name="for-an-existing-server"></a>기존 서버의 경우

또한 Azure Resource Manager 템플릿을 사용하여 기존 Azure Database for MySQL 서버에서 데이터 암호화를 사용하도록 설정할 수 있습니다.

* 앞서 속성 개체의 `Uri` 속성 아래에서 복사한 Azure Key Vault 키의 리소스 ID를 제공합니다.

* API 버전으로 *2020-01-01-preview* 를 사용합니다.

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "location": {
      "type": "string"
    },
    "serverName": {
      "type": "string"
    },
    "keyVaultName": {
      "type": "string",
      "metadata": {
        "description": "Key vault name where the key to use is stored"
      }
    },
    "keyVaultResourceGroupName": {
      "type": "string",
      "metadata": {
        "description": "Key vault resource group name where it is stored"
      }
    },
    "keyName": {
      "type": "string",
      "metadata": {
        "description": "Key name in the key vault to use as encryption protector"
      }
    },
    "keyVersion": {
      "type": "string",
      "metadata": {
        "description": "Version of the key in the key vault to use as encryption protector"
      }
    }
  },
  "variables": {
    "serverKeyName": "[concat(parameters('keyVaultName'), '_', parameters('keyName'), '_', parameters('keyVersion'))]"
  },
  "resources": [
    {
      "type": "Microsoft.DBforMySQL/servers",
      "apiVersion": "2017-12-01",
      "kind": "",
      "location": "[parameters('location')]",
      "identity": {
        "type": "SystemAssigned"
      },
      "name": "[parameters('serverName')]",
      "properties": {
      }
    },
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2019-05-01",
      "name": "addAccessPolicy",
      "resourceGroup": "[parameters('keyVaultResourceGroupName')]",
      "dependsOn": [
        "[resourceId('Microsoft.DBforMySQL/servers', parameters('serverName'))]"
      ],
      "properties": {
        "mode": "Incremental",
        "template": {
          "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "resources": [
            {
              "type": "Microsoft.KeyVault/vaults/accessPolicies",
              "name": "[concat(parameters('keyVaultName'), '/add')]",
              "apiVersion": "2018-02-14-preview",
              "properties": {
                "accessPolicies": [
                  {
                    "tenantId": "[subscription().tenantId]",
                    "objectId": "[reference(resourceId('Microsoft.DBforMySQL/servers/', parameters('serverName')), '2017-12-01', 'Full').identity.principalId]",
                    "permissions": {
                      "keys": [
                        "get",
                        "wrapKey",
                        "unwrapKey"
                      ]
                    }
                  }
                ]
              }
            }
          ]
        }
      }
    },
    {
      "name": "[concat(parameters('serverName'), '/', variables('serverKeyName'))]",
      "type": "Microsoft.DBforMySQL/servers/keys",
      "apiVersion": "2020-01-01-preview",
      "dependsOn": [
        "addAccessPolicy",
        "[resourceId('Microsoft.DBforMySQL/servers', parameters('serverName'))]"
      ],
      "properties": {
        "serverKeyType": "AzureKeyVault",
        "uri": "[concat(reference(resourceId(parameters('keyVaultResourceGroupName'), 'Microsoft.KeyVault/vaults/', parameters('keyVaultName')), '2018-02-14-preview', 'Full').properties.vaultUri, 'keys/', parameters('keyName'), '/', parameters('keyVersion'))]"
      }
    }
  ]
}

```

## <a name="next-steps"></a>다음 단계

* [Azure Database for MySQL에 대한 데이터 암호화 유효성 검사](howto-data-encryption-validation.md)
* [Azure Database for MySQL에서 데이터 암호화 문제 해결](howto-data-encryption-troubleshoot.md)
* [고객 관리형 키 개념을 사용한 데이터 암호화](concepts-data-encryption-mysql.md).

