---
title: Azure API for FHIR용 고객 관리형 키 구성
description: Cosmos DB를 통해 Azure API for FHIR에서 지원되는 사용자 고유의 키 기능을 가져옵니다.
services: healthcare-apis
author: ginalee-dotcom
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: overview
ms.date: 05/04/2021
ms.author: ginle
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 0c32725d020043c3cd62828e4bdccfafef2f53aa
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "121786051"
---
# <a name="configure-customer-managed-keys-at-rest"></a>미사용 고객 관리형 키 구성

새 Azure API for FHIR 계정을 만들면 기본적으로 Microsoft 관리형 키를 사용하여 데이터가 암호화됩니다. 이제는 사용자가 직접 선택하고 관리하는 사용자 고유의 키를 사용하여 데이터에 대한 두 번째 암호화 계층을 추가할 수 있습니다.

Azure에서 이는 일반적으로 고객의 Azure Key Vault에 있는 암호화 키를 사용하여 수행됩니다. Azure SQL, Azure Storage 및 Cosmos DB는 현재 이 기능을 제공하는 몇 가지 예입니다. Azure API for FHIR은 Cosmos DB에서 제공하는 이 지원을 활용합니다. 계정을 만들 때 Azure Key Vault 키 URI를 지정하는 옵션이 제공됩니다. 이 키는 DB 계정이 프로비저닝될 때 Cosmos DB에 전달됩니다. FHIR 요청이 수행되면 Cosmos DB에서 키를 가져오고 이를 사용하여 데이터를 암호화/암호 해독합니다. 

시작 하려면 다음 링크를 참조 하세요.

- [Azure 구독에 대한 Azure Cosmos DB 리소스 공급자 등록](../../cosmos-db/how-to-setup-cmk.md#register-resource-provider) 
- [Azure Key Vault 인스턴스 구성](../../cosmos-db/how-to-setup-cmk.md#configure-your-azure-key-vault-instance)
- [Azure Key Vault 인스턴스에 액세스 정책 추가](../../cosmos-db/how-to-setup-cmk.md#add-access-policy)
- [Azure Key Vault에서 새 키 생성](../../cosmos-db/how-to-setup-cmk.md#generate-a-key-in-azure-key-vault)

## <a name="using-azure-portal"></a>Azure Portal 사용

Azure Portal에서 fhir 계정을 위한 Azure API를 만들 때 **추가 설정** 탭의 **데이터베이스 설정** 에서 **데이터 암호화** 구성 옵션을 확인할 수 있습니다. 기본적으로 서비스 관리 키 옵션이 선택 됩니다.

> [!Important]
> 데이터 암호화 옵션은 FHIR 용 Azure API를 만든 경우에만 사용할 수 있으며 나중에 변경할 수 없습니다. 그러나 **고객이 관리** 하는 키 옵션을 선택 하면 암호화 키를 보고 업데이트할 수 있습니다. 


KeyPicker에서 키를 선택할 수 있습니다.

:::image type="content" source="media/bring-your-own-key/bring-your-own-key-keypicker.png" alt-text="키 선택기":::

**고객 관리 키** 옵션을 선택 하 여 여기에서 Azure Key Vault 키를 지정할 수도 있습니다.
 
다음과 같이 키 URI를 입력할 수도 있습니다.

:::image type="content" source="media/bring-your-own-key/bring-your-own-key-create.png" alt-text="Azure API for FHIR 만들기":::

> [!Important]
> Azure Key Vault에 대 한 모든 권한이 적절 하 게 설정 되었는지 확인 합니다. 자세한 내용은 [Azure Key Vault 인스턴스에 액세스 정책 추가](../../cosmos-db/how-to-setup-cmk.md#add-access-policy)를 참조 하세요. 또한 Key Vault 속성에서 일시 삭제를 사용 하도록 설정 해야 합니다. 이러한 단계를 완료 하지 않으면 배포 오류가 발생 합니다. 자세한 내용은 [key vault에서 일시 삭제를 사용 하도록 설정 되어 있는지 확인 하 고 일시 삭제를 사용 하도록 설정](../../key-vault/general/key-vault-recovery.md?tabs=azure-portal#verify-if-soft-delete-is-enabled-on-a-key-vault-and-enable-soft-delete)을 참조 하세요.

기존 FHIR 계정의 경우 아래와 같이 **데이터베이스** 블레이드에서 키 암호화 선택 (**서비스 관리 키** 또는 **고객 관리 키**)을 볼 수 있습니다. 구성 옵션을 선택한 후에는 수정할 수 없습니다. 그러나 키는 수정하고 업데이트할 수 있습니다.

:::image type="content" source="media/bring-your-own-key/bring-your-own-key-database.png" alt-text="Database":::

또한 지정된 키의 새 버전을 만들 수 있습니다. 그러면 서비스 중단 없이 데이터가 새 버전을 통해 암호화됩니다. 키에 대한 액세스를 제거하여 데이터에 대한 액세스를 제거할 수도 있습니다. 키가 비활성화되면 쿼리에 오류가 발생합니다. 키가 다시 활성화되면 쿼리는 다시 성공합니다.

## <a name="using-azure-powershell"></a>Azure PowerShell 사용

Azure Key Vault 키 URI를 사용하면 아래 PowerShell 명령을 실행하여 PowerShell을 통해 CMK를 구성할 수 있습니다.

```powershell
New-AzHealthcareApisService
    -Name "myService"
    -Kind "fhir-R4"
    -ResourceGroupName "myResourceGroup"
    -Location "westus2"
    -CosmosKeyVaultKeyUri "https://<my-vault>.vault.azure.net/keys/<my-key>"
```

## <a name="using-azure-cli"></a>Azure CLI 사용

PowerShell 메서드와 마찬가지로 `key-vault-key-uri` 매개 변수 아래에 Azure Key Vault 키 URI를 전달하고 아래 CLI 명령을 실행하여 CMK를 구성할 수 있습니다. 

```azurecli-interactive
az healthcareapis service create
    --resource-group "myResourceGroup"
    --resource-name "myResourceName"
    --kind "fhir-R4"
    --location "westus2"
    --cosmos-db-configuration key-vault-key-uri="https://<my-vault>.vault.azure.net/keys/<my-key>"

```
## <a name="using-azure-resource-manager-template"></a>Azure Resource Manager 템플릿 사용

Azure Key Vault 키 URI를 사용하여 **속성** 개체의 **keyVaultKeyUri** 속성 아래에 전달하여 CMK를 구성할 수 있습니다.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "services_myService_name": {
            "defaultValue": "myService",
            "type": "String"
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.HealthcareApis/services",
            "apiVersion": "2020-03-30",
            "name": "[parameters('services_myService_name')]",
            "location": "westus2",
            "kind": "fhir-R4",
            "properties": {
                "accessPolicies": [],
                "cosmosDbConfiguration": {
                    "offerThroughput": 400,
                    "keyVaultKeyUri": "https://<my-vault>.vault.azure.net/keys/<my-key>"
                },
                "authenticationConfiguration": {
                    "authority": "https://login.microsoftonline.com/72f988bf-86f1-41af-91ab-2d7cd011db47",
                    "audience": "[concat('https://', parameters('services_myService_name'), '.azurehealthcareapis.com')]",
                    "smartProxyEnabled": false
                },
                "corsConfiguration": {
                    "origins": [],
                    "headers": [],
                    "methods": [],
                    "maxAge": 0,
                    "allowCredentials": false
                }
            }
        }
    ]
}
```

다음 PowerShell 스크립트를 사용하여 템플릿을 배포할 수 있습니다.

```powershell
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$accountLocation = "West US 2"
$keyVaultKeyUri = "https://<my-vault>.vault.azure.net/keys/<my-key>"

New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile "deploy.json" `
    -accountName $accountName `
    -location $accountLocation `
    -keyVaultKeyUri $keyVaultKeyUri
```

## <a name="next-steps"></a>다음 단계

이 문서에서는 Azure Portal, PowerShell, CLI 및 리소스 관리자 템플릿을 사용 하 여 미사용에서 고객이 관리 하는 키를 구성 하는 방법을 배웠습니다. 자세한 내용은 Azure Cosmos DB FAQ 섹션을 참조 하세요. 
 
>[!div class="nextstepaction"]
>[Cosmos DB: CMK 설정 방법](../../cosmos-db/how-to-setup-cmk.md#frequently-asked-questions)