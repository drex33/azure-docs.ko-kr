---
title: Azure Event Hubs 미사용 데이터를 암호화하기 위한 고유한 키 구성
description: 이 문서에서는 Azure Event Hubs 미사용 데이터를 암호화하기 위한 고유한 키를 구성하는 방법에 대한 정보를 제공합니다.
ms.topic: conceptual
ms.date: 08/18/2021
ms.openlocfilehash: ed230cc1e0a377a580e0f23feeac74f6e0b5489d
ms.sourcegitcommit: d43193fce3838215b19a54e06a4c0db3eda65d45
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2021
ms.locfileid: "122568395"
---
# <a name="configure-customer-managed-keys-for-encrypting-azure-event-hubs-data-at-rest"></a>Azure Event Hubs 미사용 데이터를 암호화하기 위해 고객 관리형 키 구성
Azure Event Hubs는 Azure SSE(Azure Storage 서비스 암호화)를 사용하여 미사용 데이터의 암호화를 제공합니다. Event Hubs 서비스는 Azure Storage를 사용하여 데이터를 저장합니다. Azure Storage에 저장되는 모든 데이터는 Microsoft 관리형 키를 사용하여 암호화됩니다. 사용자 고유의 키(BYOK(Bring Your Own Key) 또는 고객 관리형 키라고도 함)를 사용하는 경우 데이터는 Microsoft 관리형 키를 사용하여 계속 암호화되지만, 그 외의 Microsoft 관리형 키는 고객 관리형 키를 사용하여 암호화됩니다. 이 기능을 사용하면 Microsoft 관리형 키를 암호화하는 데 사용되는 고객 관리형 키의 액세스를 만들고, 회전시키고, 사용하지 않도록 설정하며, 철회할 수 있습니다. BYOK를 사용하도록 설정하는 기능은 네임스페이스에서 한 번만 설정하면 됩니다.

> [!IMPORTANT]
> - BYOK 기능은 Event Hubs의 **프리미엄** 및 **전용** 계층에서 지원됩니다.
> - 암호화는 새 네임스페이스 또는 빈 네임스페이스에서만 사용할 수 있습니다. 네임스페이스에 이벤트 허브가 포함되어 있으면 암호화 작업이 불가능합니다.

Azure Key Vault를 사용하여 키를 관리하고 키 사용을 감사할 수 있습니다. 사용자 고유의 키를 만들어 키 자격 증명 모음에 저장할 수도 있고, Azure Key Vault API를 사용하여 키를 생성할 수도 있습니다. Azure Key Vault에 대한 자세한 내용은 [Azure Key Vault란?](../key-vault/general/overview.md)

이 문서에서는 Azure Portal을 사용하여 고객 관리형 키로 키 자격 증명 모음을 구성하는 방법을 보여줍니다. Azure Portal을 사용하여 키 자격 증명 모음을 만드는 방법은 [빠른 시작: Azure Portal을 사용하여 Azure Key Vault 만들기](../key-vault/general/quick-create-portal.md)를 참조하세요.

## <a name="enable-customer-managed-keys-azure-portal"></a>고객 관리형 키 사용(Azure Portal)
Azure Portal에서 고객 관리형 키를 사용하도록 설정하려면 다음 단계를 수행합니다. 전용 계층을 사용하는 경우 먼저 Event Hubs Dedicated 클러스터로 이동합니다.

1. BYOK를 사용할 네임스페이스를 선택합니다.
1. Event Hubs 네임스페이스의 **설정** 페이지에서 **암호화** 를 선택합니다. 
1. 다음 이미지에 표시된 것처럼 **미사용 고객 관리형 키 암호화** 를 선택합니다. 

    ![고객 관리형 키 사용](./media/configure-customer-managed-key/enable-customer-managed-key.png)

## <a name="set-up-a-key-vault-with-keys"></a>키를 사용하여 키 자격 증명 모음 설정
고객 관리형 키를 사용하도록 설정한 후에는 고객 관리형 키를 Azure Event Hubs 네임스페이스와 연결해야 합니다. Event Hubs는 Azure Key Vault만 지원합니다. 이전 섹션에서 **고객 관리형 키로 암호화** 옵션을 사용하도록 설정하는 경우 키를 Azure Key Vault로 가져와야 합니다. 또한 키에는 키를 위해 구성된 **일시 삭제** 및 **제거 안 함** 속성이 있어야 합니다. 해당 설정은 [PowerShell](../key-vault/general/key-vault-recovery.md) 또는 [CLI](../key-vault/general/key-vault-recovery.md)를 이용해 구성할 수 있습니다.

1. 새로운 키 자격 증명 모음을 만들려면 Azure Key Vault [빠른 시작](../key-vault/general/overview.md)을 따릅니다. 기존 키를 가져오는 방법에 대한 자세한 내용은 [키, 비밀, 인증서 정보](../key-vault/general/about-keys-secrets-certificates.md)를 참조하세요.

    > [!IMPORTANT]
    > Azure Event Hubs에서 고객 관리형 키를 사용하려면 키 자격 증명 모음에 두 가지 필수 속성이 구성되어 있어야 합니다. 바로 **일시 삭제** 와 **제거 안 함** 입니다. 이러한 속성은 Azure Portal에서 새 키 자격 증명 모음을 만들 때 기본적으로 사용하도록 설정됩니다. 그러나 기존 키 자격 증명 모음에서 이러한 속성을 사용하도록 설정해야 하는 경우에는 PowerShell 또는 Azure CLI를 사용해야 합니다.
1. 자격 증명 모음을 만들 때 일시 삭제 및 제거 보호를 모두 설정하려면 [az keyvault create](/cli/azure/keyvault#az_keyvault_create) 명령을 사용합니다.

    ```azurecli-interactive
    az keyvault create --name ContosoVault --resource-group ContosoRG --location westus --enable-soft-delete true --enable-purge-protection true
    ```    
1. 이미 일시 삭제를 사용하는 기존 자격 증명 모음에 제거 보호를 추가하려면 [az keyvault update](/cli/azure/keyvault#az_keyvault_update) 명령을 사용합니다.

    ```azurecli-interactive
    az keyvault update --name ContosoVault --resource-group ContosoRG --enable-purge-protection true
    ```
1. 다음 단계에 따라 키를 만듭니다.
    1. 새 키를 만들려면 **설정** 아래 **키** 메뉴에서 **생성/가져오기** 를 선택합니다.
        
        ![생성/가져오기 단추 선택](./media/configure-customer-managed-key/select-generate-import.png)
    1. **옵션** 을 **생성** 으로 설정하고 키에 이름을 지정합니다.

        ![키 만들기](./media/configure-customer-managed-key/create-key.png) 
    1. 이제 이 키를 선택하여 드롭다운 목록에서 암호화할 Event Hubs 네임스페이스와 연결할 수 있습니다. 

        ![키 자격 증명 모음에서 키 선택](./media/configure-customer-managed-key/select-key-from-key-vault.png)

        > [!NOTE]
        > 중복 구성을 위해 최대 3개의 키를 추가할 수 있습니다. 키 중 하나가 만료되었거나 액세스할 수 없는 경우 다른 키가 암호화에 사용됩니다.
    1. 키 세부 정보를 입력하고 **선택** 을 클릭합니다. 그러면 Microsoft 관리형 키와 사용자의 키(고객 관리형 키)를 암호화할 수 있습니다. 

## <a name="managed-identities"></a>관리 ID
Event Hubs 네임스페이스에 할당할 수 있는 관리 ID에는 두 가지 유형이 있습니다.

- **시스템 할당**: Event Hubs 네임스페이스에서 직접 관리 ID를 사용하도록 설정할 수 있습니다. 시스템이 할당한 관리 ID를 사용하도록 설정하면 해당 Event Hubs 네임스페이스의 수명 주기와 연결된 ID가 Azure AD에 만들어집니다. 따라서 네임스페이스가 삭제되면 Azure에서 자동으로 ID를 삭제합니다. 의도적으로 해당 Azure 리소스(네임스페이스)만 이 ID를 사용하여 Azure AD에서 토큰을 요청할 수 있습니다.
- **사용자 할당**: 관리 ID를 사용자 할당 ID라고 하는 독립 실행형 Azure 리소스로 만들 수도 있습니다. 사용자 할당 관리 ID를 만들고 하나 이상의 Event Hubs 네임스페이스에 할당할 수 있습니다. 사용자가 할당한 관리 ID는 이를 사용하는 리소스와 별도로 관리됩니다. 네임스페이스의 수명 주기와 관련이 없습니다. 더 이상 필요하지 않을 때 사용자 할당 ID를 명시적으로 삭제할 수 있습니다.    

    자세한 내용은 [Azure 리소스에 대한 관리 ID란?](../active-directory/managed-identities-azure-resources/overview.md)을 참조하세요.


## <a name="encrypt-using-system-assigned-identities-template"></a>시스템 할당 ID(템플릿)를 사용하여 암호화
이 섹션에서는 **Azure Resource Manager 템플릿** 을 사용하여 다음 작업을 수행하는 방법을 보여 줍니다. 

1. 관리형 서비스 ID를 사용하여 **Event Hubs 네임스페이스** 를 만듭니다.
2. **키 자격 증명 모음** 을 만들고 키 자격 증명 모음에 대한 서비스 ID 액세스 권한을 부여합니다. 
3. 키 자격 증명 모음 정보(키/값)를 사용하여 Event Hubs 네임스페이스를 업데이트합니다. 


### <a name="create-an-event-hubs-cluster-and-namespace-with-managed-service-identity"></a>관리형 서비스 ID를 사용하여 Event Hubs 클러스터 및 네임스페이스 만들기
이 섹션에서는 Azure Resource Manager 템플릿 및 PowerShell을 사용하여 관리형 서비스 ID로 Azure Event Hubs 네임스페이스를 만드는 방법을 보여 줍니다. 

1. Azure Resource Manager 템플릿을 만들어서 관리형 서비스 ID를 사용하여 Event Hubs 네임스페이스를 만듭니다. 파일 이름: **CreateEventHubClusterAndNamespace.json**: 

    ```json
    {
       "$schema":"https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion":"1.0.0.0",
       "parameters":{
          "clusterName":{
             "type":"string",
             "metadata":{
                "description":"Name for the Event Hub cluster."
             }
          },
          "namespaceName":{
             "type":"string",
             "metadata":{
                "description":"Name for the Namespace to be created in cluster."
             }
          },
          "location":{
             "type":"string",
             "defaultValue":"[resourceGroup().location]",
             "metadata":{
                "description":"Specifies the Azure location for all resources."
             }
          }
       },
       "resources":[
          {
             "type":"Microsoft.EventHub/clusters",
             "apiVersion":"2018-01-01-preview",
             "name":"[parameters('clusterName')]",
             "location":"[parameters('location')]",
             "sku":{
                "name":"Dedicated",
                "capacity":1
             }
          },
          {
             "type":"Microsoft.EventHub/namespaces",
             "apiVersion":"2018-01-01-preview",
             "name":"[parameters('namespaceName')]",
             "location":"[parameters('location')]",
             "identity":{
                "type":"SystemAssigned"
             },
             "sku":{
                "name":"Standard",
                "tier":"Standard",
                "capacity":1
             },
             "properties":{
                "isAutoInflateEnabled":false,
                "maximumThroughputUnits":0,
                "clusterArmId":"[resourceId('Microsoft.EventHub/clusters', parameters('clusterName'))]"
             },
             "dependsOn":[
                "[resourceId('Microsoft.EventHub/clusters', parameters('clusterName'))]"
             ]
          }
       ],
       "outputs":{
          "EventHubNamespaceId":{
             "type":"string",
             "value":"[resourceId('Microsoft.EventHub/namespaces',parameters('namespaceName'))]"
          }
       }
    }
    ```
2. **CreateEventHubClusterAndNamespaceParams.json** 라는 이름의 템플릿 매개 변수 파일을 만듭니다. 

    > [!NOTE]
    > 다음 값을 바꿉니다. 
    > - `<EventHubsClusterName>` - Event Hubs 클러스터의 이름    
    > - `<EventHubsNamespaceName>` - Event Hubs 네임스페이스의 이름
    > - `<Location>` - Event Hubs 네임스페이스의 위치

    ```json
    {
       "$schema":"https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
       "contentVersion":"1.0.0.0",
       "parameters":{
          "clusterName":{
             "value":"<EventHubsClusterName>"
          },
          "namespaceName":{
             "value":"<EventHubsNamespaceName>"
          },
          "location":{
             "value":"<Location>"
          }
       }
    }
    
    ```
3. 다음 PowerShell 명령을 실행하여 템플릿을 배포해 Event Hubs 네임스페이스를 만듭니다. 그런 다음 나중에 사용할 Event Hubs 네임스페이스의 ID를 검색합니다. 명령을 실행하기 전에 `{MyRG}`를 리소스 그룹의 이름으로 대체합니다.  

    ```powershell
    $outputs = New-AzResourceGroupDeployment -Name CreateEventHubClusterAndNamespace -ResourceGroupName {MyRG} -TemplateFile ./CreateEventHubClusterAndNamespace.json -TemplateParameterFile ./CreateEventHubClusterAndNamespaceParams.json

    $EventHubNamespaceId = $outputs.Outputs["eventHubNamespaceId"].value
    ```
 
### <a name="grant-event-hubs-namespace-identity-access-to-key-vault"></a>키 자격 증명 모음에 Event Hubs 네임스페이스 ID 액세스 권한 부여

1. 다음 명령을 실행하여 **제거 보호** 및 **일시 삭제** 를 사용하는 키 자격 증명 모음을 만듭니다. 

    ```powershell
    New-AzureRmKeyVault -Name {keyVaultName} -ResourceGroupName {RGName}  -Location {location} -EnableSoftDelete -EnablePurgeProtection    
    ```     
    
    또는    
    
    다음 명령을 실행하여 **기존 키 자격 증명 모음** 을 업데이트합니다. 명령을 실행하기 전에 리소스 그룹 및 키 자격 증명 모음 이름의 값을 지정합니다. 
    
    ```powershell
    ($updatedKeyVault = Get-AzureRmResource -ResourceId (Get-AzureRmKeyVault -ResourceGroupName {RGName} -VaultName {keyVaultName}).ResourceId).Properties| Add-Member -MemberType "NoteProperty" -Name "enableSoftDelete" -Value "true"-Force | Add-Member -MemberType "NoteProperty" -Name "enablePurgeProtection" -Value "true" -Force
    ``` 
2. Event Hubs 네임스페이스의 관리형 ID가 키 자격 증명 모음의 키 값에 액세스할 수 있도록 키 자격 증명 모음 액세스 정책을 설정합니다. 이전 섹션의 Event Hubs 네임스페이스 ID를 사용합니다. 

    ```powershell
    $identity = (Get-AzureRmResource -ResourceId $EventHubNamespaceId -ExpandProperties).Identity
    
    Set-AzureRmKeyVaultAccessPolicy -VaultName {keyVaultName} -ResourceGroupName {RGName} -ObjectId $identity.PrincipalId -PermissionsToKeys get,wrapKey,unwrapKey,list
    ```

### <a name="encrypt-data-in-event-hubs-namespace-with-customer-managed-key-from-key-vault"></a>키 자격 증명 모음의 고객 관리형 키를 사용하여 Event Hubs 네임스페이스의 데이터 암호화
지금까지 다음 단계를 완료했습니다. 

1. 관리 ID를 사용하여 프리미엄 네임스페이스를 만들었습니다.
2. 키 자격 증명 모음을 만들고 관리형 ID에 키 자격 증명 모음에 대한 액세스 권한을 부여했습니다. 

이 단계에서는 키 자격 증명 모음 정보를 사용하여 Event Hubs 네임스페이스를 업데이트합니다. 

1. 다음과 같은 내용이 포함된 **CreateEventHubClusterAndNamespace.json** 이라는 JSON 파일을 만듭니다. 

    ```json
    {
       "$schema":"https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion":"1.0.0.0",
       "parameters":{
          "clusterName":{
             "type":"string",
             "metadata":{
                "description":"Name for the Event Hub cluster."
             }
          },
          "namespaceName":{
             "type":"string",
             "metadata":{
                "description":"Name for the Namespace to be created in cluster."
             }
          },
          "location":{
             "type":"string",
             "defaultValue":"[resourceGroup().location]",
             "metadata":{
                "description":"Specifies the Azure location for all resources."
             }
          },
          "keyVaultUri":{
             "type":"string",
             "metadata":{
                "description":"URI of the KeyVault."
             }
          },
          "keyName":{
             "type":"string",
             "metadata":{
                "description":"KeyName."
             }
          }
       },
       "resources":[
          {
             "type":"Microsoft.EventHub/namespaces",
             "apiVersion":"2018-01-01-preview",
             "name":"[parameters('namespaceName')]",
             "location":"[parameters('location')]",
             "identity":{
                "type":"SystemAssigned"
             },
             "sku":{
                "name":"Standard",
                "tier":"Standard",
                "capacity":1
             },
             "properties":{
                "isAutoInflateEnabled":false,
                "maximumThroughputUnits":0,
                "clusterArmId":"[resourceId('Microsoft.EventHub/clusters', parameters('clusterName'))]",
                "encryption":{
                   "keySource":"Microsoft.KeyVault",
                   "keyVaultProperties":[
                      {
                         "keyName":"[parameters('keyName')]",
                         "keyVaultUri":"[parameters('keyVaultUri')]"
                      }
                   ]
                }
             }
          }
       ]
    }
    ``` 

2. 다음 템플릿 매개 변수 파일을 만듭니다. **UpdateEventHubClusterAndNamespaceParams.json**. 

    > [!NOTE]
    > 다음 값을 바꿉니다. 
    > - `<EventHubsClusterName>` - Event Hubs 클러스터의 이름        
    > - `<EventHubsNamespaceName>` - Event Hubs 네임스페이스의 이름
    > - `<Location>` - Event Hubs 네임스페이스의 위치
    > - `<KeyVaultName>` - 키 자격 증명 모음의 이름
    > - `<KeyName>` - 키 자격 증명 모음에 있는 키의 이름

    ```json
    {
       "$schema":"https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
       "contentVersion":"1.0.0.0",
       "parameters":{
          "clusterName":{
             "value":"<EventHubsClusterName>"
          },
          "namespaceName":{
             "value":"<EventHubsNamespaceName>"
          },
          "location":{
             "value":"<Location>"
          },
          "keyName":{
             "value":"<KeyName>"
          },
          "keyVaultUri":{
             "value":"https://<KeyVaultName>.vault.azure.net"
          }
       }
    }
    ```             
3. 다음 PowerShell 명령을 실행하여 Resource Manager 템플릿을 배포합니다. 명령을 실행하기 전에 `{MyRG}`는 해당 리소스 그룹의 이름으로 바꿉니다. 

    ```powershell
    New-AzResourceGroupDeployment -Name UpdateEventHubNamespaceWithEncryption -ResourceGroupName {MyRG} -TemplateFile ./UpdateEventHubClusterAndNamespace.json -TemplateParameterFile ./UpdateEventHubClusterAndNamespaceParams.json 
    ```

## <a name="encrypt-using-user-assigned-identities-template"></a>사용자 할당 ID(템플릿)를 사용하여 암호화

1. **사용자 할당 ID** 를 만듭니다.
1. **키 자격 증명 모음** 을 만들고 액세스 정책을 통해 사용자 할당 ID에 대한 액세스 권한을 부여합니다.
1. 관리되는 사용자 ID 및 키 자격 증명 모음 정보를 사용하여 **Event Hubs 네임스페이스** 를 만듭니다.

### <a name="create-a-user-assigned-identity"></a>사용자 할당 ID 만들기
[사용자 할당 관리 ID 만들기](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity) 도움말의 지침에 따라 사용자 할당 ID를 만듭니다. [CLI](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md), [PowerShell](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell.md), [Azure Resource Manager 템플릿](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-arm.md) 및 [REST](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-rest.md)를 사용하여 사용자 할당 ID를 만들 수도 있습니다. 

> [!NOTE]
> 네임스페이스에 최대 **4** 개의 사용자 ID를 할당할 수 있습니다. 이러한 연결은 네임스페이스가 삭제되거나 템플릿의 `identity -> type`을 `None`에 전달할 때 삭제됩니다. 

### <a name="create-a-key-vault-and-grant-access-to-user-assigned-identity"></a>키 자격 증명 모음을 만들고 사용자 할당 ID에 대한 액세스 권한 부여 

1. 다음 명령을 실행하여 보호 제거 및 일시 삭제를 사용하는 키 자격 증명 모음을 만듭니다.

    ```azurepowershell-interactive
    New-AzureRmKeyVault -Name "{keyVaultName}" -ResourceGroupName {RGName} -Location "{location}" -EnableSoftDelete -EnablePurgeProtection           
    ```
    
    또는

    다음 명령을 실행하여 기존 키 자격 증명 모음을 업데이트합니다. 명령을 실행하기 전에 리소스 그룹 및 키 자격 증명 모음 이름의 값을 지정합니다.

    ```azurepowershell-interactive
    ($updatedKeyVault = Get-AzureRmResource -ResourceId (Get-AzureRmKeyVault -ResourceGroupName {RGName} -VaultName {keyVaultName}).ResourceId).Properties| Add-Member -MemberType "NoteProperty" -Name "enableSoftDelete" -Value "true"-Force | Add-Member -MemberType "NoteProperty" -Name "enablePurgeProtection" -Value "true" -Force            
    ```
2. 다음 PowerShell 명령을 사용하여 사용자 ID에 대한 **서비스 주체 ID** 를 가져옵니다. 이 예에서 `ud1`은 암호화에 사용할 사용자 할당 ID입니다.

    ```azurepowershell-interactive
    $servicePrincipal=Get-AzADServicePrincipal -SearchString "ud1"    
    ```
3. 액세스 정책을 할당하여 키 자격 증명 모음에 대한 사용자 할당 ID 액세스 권한을 부여합니다.     

    ```azurepowershell-interactive
    Set-AzureRmKeyVaultAccessPolicy -VaultName {keyVaultName} -ResourceGroupName {RGName} -ObjectId $servicePrincipal.Id -PermissionsToKeys get,wrapKey,unwrapKey,list    
    ```

    > [!NOTE]
    > 최대 **3** 개의 키를 추가할 수 있지만 암호화에 사용되는 사용자 ID는 모든 키에 대해 동일해야 합니다. 현재 단일 암호화 ID만 지원됩니다. 

### <a name="create-an-event-hubs-namespace-with-user-identity-and-key-vault-information"></a>사용자 ID 및 키 자격 증명 모음 정보로 Event Hubs 네임스페이스 만들기
이 섹션에서는 Azure Resource Manager 템플릿을 사용하여 다음 작업을 수행하는 방법을 보여 주는 예를 제공합니다. 

- Event Hubs 네임스페이스에 사용자 관리 ID를 할당합니다.

    ```json
                "identity": {
                    "type": "UserAssigned",
                    "userAssignedIdentities": {
                        "[parameters('identity').userAssignedIdentity]": {}
                    }
                },
    ```    
- 키 자격 증명 모음의 키와 키에 액세스할 사용자 관리 ID를 지정하여 네임스페이스에서 암호화를 사용하도록 설정합니다. 

    ```json
                    "encryption":{
                       "keySource":"Microsoft.KeyVault",
                       "keyVaultProperties":[
                            {
                                "keyName": "[parameters('keyName')]",
                                "keyVaultUri": "[parameters('keyVaultUri')]",
                                "identity": {
                                    "userAssignedIdentity": "[parameters('identity').userAssignedIdentity]"
                                }
                            }
                       ]
                    }
    ```
   

1. 다음 콘텐츠로 **CreateEventHubsNamespaceWithUserIdentityAndEncryption.json** 이라는 JSON 파일을 만듭니다.

    ```json
    {
       "$schema":"https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion":"1.0.0.0",
       "parameters":{
        "clusterName":{
            "type":"string",
             "metadata":{
                "description":"Name for the Event Hub cluster."
             }
          },
          "namespaceName":{
             "type":"string",
             "metadata":{
                "description":"Name for the Namespace to be created in cluster."
             }
          },
          "location":{
             "type":"string",
             "defaultValue":"[resourceGroup().location]",
             "metadata":{
                "description":"Specifies the Azure location for all resources."
             }
          },
          "keyVaultUri":{
             "type":"string",
             "metadata":{
                "description":"URI of the KeyVault."
             }
          },
          "keyName":{
             "type":"string",
             "metadata":{
                "description":"KeyName."
             },
         "identity": {
            "type": "Object",
            "defaultValue": {
                "userAssignedIdentity": ""
            },
            "metadata": {
                "description": "user-assigned identity."
            }
         }
       },
       "resources":[
          {
             "type":"Microsoft.EventHub/clusters",
             "apiVersion":"2018-01-01-preview",
             "name":"[parameters('clusterName')]",
             "location":"[parameters('location')]",
             "sku":{
                "name":"Dedicated",
                "capacity":1
             }
          },
          {
             "type":"Microsoft.EventHub/namespaces",
             "apiVersion":"2021-01-01-preview",
             "name":"[parameters('namespaceName')]",
             "location":"[parameters('location')]",
             "sku":{
                "name":"Standard",
                "tier":"Standard",
                "capacity":1
             },
            "identity": {
                "type": "UserAssigned",
                "userAssignedIdentities": {
                    "[parameters('identity').userAssignedIdentity]": {}
                }
            },
             "properties":{
                "encryption":{
                   "keySource":"Microsoft.KeyVault",
                   "keyVaultProperties":[
                        {
                            "keyName": "[parameters('keyName')]",
                            "keyVaultUri": "[parameters('keyVaultUri')]",
                            "identity": {
                                "userAssignedIdentity": "[parameters('identity').userAssignedIdentity]"
                            }
                        }
                   ]
                }
             }
          }
       ]
    }        
    ```  
1. 템플릿 매개 변수 파일(**CreateEventHubsNamespaceWithUserIdentityAndEncryptionParams.json**)을 만듭니다.

    ```json
    {
       "$schema":"https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
       "contentVersion":"1.0.0.0",
       "parameters":{
          "namespaceName":{
             "value":"<EventHubsNamespaceName>"
          },
          "location":{
             "value":"<Location>"
          },
          "keyVaultUri":{
             "value":"https://<KeyVaultName>.vault.azure.net"
          },
          "keyName":{
             "value":"<KeyName>"
          },
          "identity": {
            "value": {
                "userAssignedIdentity": "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER MANAGED IDENTITY NAME>"
            }
         }
       }
    }
    ```

    매개 변수 파일에서 자리 표시자를 적절한 값으로 바꿉니다.
    
    | 자리 표시자 | 값 | 
    | ----------- | ----- | 
    | `<EventHubsNamespaceName>` | Event Hubs 네임스페이스의 이름입니다. | 
    | `<Location>` | 네임스페이스를 만들 위치입니다. | 
    | `<KeyVaultName>` | Key Vault의 이름입니다. | 
    | `<KeyName>` | 키 자격 증명 모음에 있는 키의 이름입니다. | 
    | `<AZURE SUBSCRIPTION ID>` | Azure 구독 ID. |
    | `<RESOURCE GROUP NAME>` | 사용자 관리 ID의 리소스 그룹입니다. | 
    | `<USER MANAGED IDENTITY NAME>` | 사용자 관리 ID의 이름입니다. | 

3. 다음 PowerShell 명령을 실행하여 Resource Manager 템플릿을 배포합니다. 명령을 실행하기 전에 `{MyRG}`는 해당 리소스 그룹의 이름으로 바꿉니다.

    ```azurepowershell-interactive
    New-AzResourceGroupDeployment -Name CreateEventHubsNamespaceWithEncryption -ResourceGroupName {MyRG} -TemplateFile ./ CreateEventHubsNamespaceWithUserIdentityAndEncryption.json -TemplateParameterFile ./ CreateEventHubsNamespaceWithUserIdentityAndEncryptionParams.json        
    ```


## <a name="use-both-user-assigned-and-system-assigned-identities"></a>사용자 할당 및 시스템 할당 ID 모두 사용
네임스페이스에는 시스템 할당 ID와 사용자 할당 ID 둘 다 동시에 있을 수 있습니다. 이 경우 `type` 속성은 다음 예와 같이 `SystemAssigned`, `UserAssigned`입니다. 

```json
"identity": {
    "type": "SystemAssigned, UserAssigned",
    "userAssignedIdentities": {
        "/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<userIdentity1>" : {}
    }
}
```

이 시나리오에서는 미사용 데이터를 암호화하기 위해 시스템 할당 ID 또는 사용자 할당 ID를 선택할 수 있습니다.  

Resource Manager 템플릿에서 `identity` 특성을 지정하지 않으면 시스템 관리 ID가 사용됩니다. 다음은 코드 조각 예입니다. 

```json
"properties":{
   "encryption":{
      "keySource":"Microsoft.KeyVault",
      "keyVaultProperties":[
         {
            "keyName":"[parameters('keyName')]",
            "keyVaultUri":"[parameters('keyVaultUri')]"
         }
      ]
   }
}
```

암호화에 사용자 관리 ID를 사용하는 방법은 다음 예를 참조하세요. `identity` 특성이 사용자 관리 ID로 설정되어 있습니다. 

```json
"properties":{
   "encryption":{
      "keySource":"Microsoft.KeyVault",
      "keyVaultProperties":[
         {
            "keyName":"[parameters('keyName')]",
            "keyVaultUri":"[parameters('keyVaultUri')]",
            "identity": {
                "userAssignedIdentity": "[parameters('identity').userAssignedIdentity]"
            }
         }
      ]
   }
}
```

## <a name="enable-infrastructure-or-double-encryption-of-data"></a>데이터의 인프라(또는 이중) 암호화 사용
데이터 보안에 대한 더 높은 수준의 보증이 필요한 경우 이중 암호화라고도 하는 인프라 수준 암호화를 사용하도록 설정할 수 있습니다. 

인프라 암호화를 사용하도록 설정하면 Event Hubs 네임스페이스 계정의 데이터가 다른 암호화 알고리즘 2개와 다른 키 2개를 사용하여 두 번(서비스 수준에서 한 번, 인프라 수준에서 한 번) 암호화됩니다. 따라서 Event Hubs 데이터의 인프라 암호화는 암호화 알고리즘 또는 키 중 하나가 손상될 수 있는 시나리오로부터 보호합니다.

아래와 같이 위의 **CreateEventHubClusterAndNamespace.json** 에서 `requireInfrastructureEncryption` 속성으로 Azure Resource Manager 템플릿을 업데이트하여 인프라 암호화를 사용하도록 설정할 수 있습니다. 

```json
"properties":{
   "isAutoInflateEnabled":false,
   "maximumThroughputUnits":0,
   "clusterArmId":"[resourceId('Microsoft.EventHub/clusters', parameters('clusterName'))]",
   "encryption":{
      "keySource":"Microsoft.KeyVault",
      "requireInfrastructureEncryption":true,
      "keyVaultProperties":[
         {
            "keyName":"[parameters('keyName')]",
            "keyVaultUri":"[parameters('keyVaultUri')]"
         }
      ]
   }
}
```

## <a name="rotate-revoke-and-cache-encryption-keys"></a>암호화 키 회전, 철회 및 캐시

### <a name="rotate-your-encryption-keys"></a>암호화 키 회전
Azure Key Vaults 회전 메커니즘을 사용하여 키 자격 증명 모음에서 키를 회전할 수 있습니다. 키 회전을 자동화하도록 활성화하고 만료일을 설정할 수도 있습니다. Event Hubs 서비스는 새로운 키 버전을 검색하고 자동으로 이를 사용하기 시작합니다.

### <a name="revoke-access-to-keys"></a>키 액세스 철회
암호화 키에 대한 액세스를 철회해도 Event Hubs의 데이터는 제거되지 않습니다. 그러나 Event Hubs 네임스페이스에서 데이터에 액세스할 수 없게 됩니다. 액세스 정책을 통해, 또는 키를 삭제하여 암호화 키를 철회할 수 있습니다. [키 자격 증명 모음에 대한 보안 액세스](../key-vault/general/security-features.md)에서 액세스 정책 및 키 자격 증명 모음 보안에 대해 자세히 알아보세요.

암호화 키가 해지되면 암호화된 네임스페이스의 Event Hubs 서비스가 작동하지 않게 됩니다. 키에 대한 액세스를 사용하도록 설정하거나 삭제 키가 복원된 경우, 암호화된 Event Hubs 네임스페이스의 데이터에 액세스할 수 있도록 Event Hubs 서비스에서 키를 선택합니다.

### <a name="caching-of-keys"></a>키 캐싱
Event Hubs 인스턴스(이벤트 허브)는 5분마다 나열된 암호화 키를 폴링합니다. 캐시하여 5분 후인 다음 폴링 전까지 사용합니다. 하나 이상의 키를 사용할 수 있는 한 이벤트 허브에 액세스할 수 있습니다. 폴링 시점에 나열된 모든 키에 액세스할 수 없는 경우 모든 Event Hubs를 사용할 수 없게 됩니다. 

다음은 자세한 내용입니다. 

- 5분마다 Event Hubs 서비스는 네임스페이스의 레코드에 나열된 모든 고객 관리형 키를 폴링합니다.
    - 키를 회전한 경우 레코드가 새 키로 업데이트됩니다.
    - 키가 철회되면 레코드에서 제거됩니다.
    - 모든 키가 철회될 경우 네임스페이스의 암호화 상태가 **철회됨** 으로 설정됩니다. Event Hubs 네임스페이스에서 데이터에 액세스할 수 없게 됩니다.

## <a name="considerations-when-using-geo-disaster-recovery"></a>지리적 재해 복구 사용 시 고려 사항

> [!IMPORTANT]
> BYOK 암호화를 사용하는 네임스페이스에서 Geo-DR을 사용하도록 설정하려면 페어링을 위한 보조 네임스페이스에 시스템 할당 또는 사용자 할당 관리 ID가 사용하도록 설정되어 있어야 합니다. 

### <a name="geo-disaster-recovery---encryption-with-system-assigned-identities"></a>지리적 재해 복구 - 시스템 할당 ID로 암호화
고객 관리형 키를 통한 Microsoft 관리형 키의 암호화를 사용하도록 설정하려면 지정한 Azure KeyVault에서 시스템 할당 관리 ID에 대해 [액세스 정책](../key-vault/general/secure-your-key-vault.md)을 설정해야 합니다. 이렇게 하면 Azure Event Hubs 네임스페이스에서 Azure KeyVault에 대한 제어된 액세스가 보장됩니다.

이런 이유로

- Event Hubs 네임스페이스에 대해 [지역 재해 복구](event-hubs-geo-dr.md)를 이미 사용하도록 설정했고 고객 관리형 키를 사용하도록 설정하려는 경우
    - 페어링을 중단합니다.
    - 기본 및 보조 네임스페이스 모두에 대해 시스템 할당 관리 ID의 [액세스 정책](../key-vault/general/assign-access-policy-portal.md)을 키 자격 증명 모음으로 설정합니다.
    - 기본 네임스페이스에 대해 암호화를 설정합니다.
    - 기본 및 보조 네임스페이스를 쌍으로 연결합니다.
- 고객 관리형 키가 이미 설정된 Event Hubs 네임스페이스에서 지역 DR을 사용하도록 설정하려는 경우 다음 단계를 따릅니다. 
    - 보조 네임스페이스의 관리 ID에 대해 [액세스 정책](../key-vault/general/assign-access-policy-portal.md)을 키 자격 증명 모음으로 설정합니다.
    - 기본 및 보조 네임스페이스를 쌍으로 연결합니다.

### <a name="geo-disaster-recovery---encryption-with-user-assigned-identities"></a>지리적 재해 복구 - 사용자 할당 ID로 암호화
몇 가지 권장 사항입니다. 

1.  관리 ID를 만들고 관리 ID에 Key Vault 권한을 할당합니다. 
2.  ID를 사용자 할당 ID로 추가하고 두 네임스페이스에서 ID로 암호화를 사용하도록 설정합니다. 
3.  네임스페이스를 함께 페어링 

사용자 할당 ID로 Geo-DR 및 암호화를 사용하도록 설정하기 위한 조건:

1.  보조 네임스페이스가 암호화 사용 기본 네임스페이스와 쌍을 이루려면 사용자 할당 ID로 암호화가 이미 사용하도록 설정되어 있어야 합니다. 
2.  보조 네임스페이스에 네임스페이스와 연결된 사용자 할당 ID가 있더라도 이미 페어링된 기본 네임스페이스에서 암호화를 사용하도록 설정할 수 없습니다.

## <a name="set-up-diagnostic-logs"></a>진단 로그 설정 
BYOK를 사용하는 네임스페이스에 대한 진단 로그를 설정하면 작업에 관한 필수 정보가 제공됩니다. 이러한 로그를 사용하도록 설정하고 나중에 이벤트 허브로 스트리밍하거나, 로그 분석을 통해 분석하거나, 사용자 지정 분석을 수행하기 위해 스토리지로 스트리밍할 수 있습니다. 진단 로그에 대한 자세한 내용은 [Azure 진단 로그 개요](../azure-monitor/essentials/platform-logs-overview.md)를 참조하세요. 스키마의 경우 [데이터 참조 모니터링](monitor-event-hubs-reference.md#customer-managed-key-user-logs-schema)을 참조하세요.

## <a name="next-steps"></a>다음 단계
다음 문서를 참조하세요.
- [Event Hubs 개요](event-hubs-about.md)
- [Key Vault 개요](../key-vault/general/overview.md)