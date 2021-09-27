---
title: Azure API Management의 관리 ID 사용 | Microsoft Docs
description: Azure Portal, PowerShell 및 Resource Manager 템플릿을 사용하여 API Management에서 시스템 할당 및 사용자 할당 ID를 만드는 방법에 대해 알아봅니다.
services: api-management
documentationcenter: ''
author: dlepow
manager: anneta
editor: ''
ms.service: api-management
ms.workload: integration
ms.topic: article
ms.date: 03/09/2021
ms.author: danlep
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 32dd5d723a7592e265a7aea791b8aa7161107239
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128594710"
---
# <a name="use-managed-identities-in-azure-api-management"></a>Azure API Management의 관리 ID 사용

이 문서에서는 Azure API Management 인스턴스에 대한 관리 ID를 만드는 방법과 다른 리소스에 액세스하는 방법을 보여 줍니다. Azure AD(Azure Active Directory)에서 생성된 관리 ID를 사용하면 API Management 인스턴스에서 Azure Key Vault처럼 Azure AD로 보호되는 다른 리소스에 쉽고 안전하게 액세스할 수 있습니다. Azure에서 이 ID를 관리하므로 어떤 비밀도 프로비전하거나 회전할 필요가 없습니다. 관리 ID에 대한 자세한 내용은 [Azure 리소스용 관리 ID란?](../active-directory/managed-identities-azure-resources/overview.md)을 참조하세요.

두 가지 유형의 ID를 API Management 인스턴스에 부여할 수 있습니다.

- *시스템 할당 ID* 는 서비스에 연결되어 있어 해당 서비스를 삭제하면 이 ID도 삭제됩니다. 서비스에는 시스템 할당 ID가 하나만 있을 수 있습니다.
- *사용자 할당 ID* 는 서비스에 할당할 수 있는 독립 실행형 Azure 리소스입니다. 서비스에는 여러 사용자 할당 ID가 있을 수 있습니다.

## <a name="create-a-system-assigned-managed-identity"></a>시스템에서 할당한 관리 ID 만들기

### <a name="azure-portal"></a>Azure portal

Azure Portal에서 관리 ID를 설정하려면 먼저 API Management 인스턴스를 만든 다음, 기능을 사용하도록 설정합니다.

1. 평소처럼 포털에서 API Management 인스턴스를 만듭니다. 포털에서 찾아봅니다.
2. **관리 ID** 를 선택합니다.
3. **시스템 할당** 탭에서 **상태** 를 **켜기** 로 바꿉니다. **저장** 을 선택합니다.

    :::image type="content" source="./media/api-management-msi/enable-system-msi.png" alt-text="시스템 할당 관리 ID 사용을 위한 선택 항목" border="true":::

### <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

다음 단계에서는 API Management 인스턴스를 만들고 Azure PowerShell을 사용하여 ID를 할당하는 과정을 안내합니다.

1. 필요한 경우 [Azure PowerShell 가이드](/powershell/azure/install-az-ps)의 지침에 따라 Azure PowerShell을 설치합니다. 그런 다음, `Connect-AzAccount`를 실행하여 Azure와 연결합니다.

2. 다음 코드를 사용하여 인스턴스를 만듭니다. API Management 인스턴스에서 Azure PowerShell을 사용하는 방법에 대한 예제는 [API Management PowerShell 샘플](powershell-samples.md)을 참조하세요.

    ```azurepowershell-interactive
    # Create a resource group.
    New-AzResourceGroup -Name $resourceGroupName -Location $location

    # Create an API Management Consumption Sku service.
    New-AzApiManagement -ResourceGroupName $resourceGroupName -Name consumptionskuservice -Location $location -Sku Consumption -Organization contoso -AdminEmail contoso@contoso.com -SystemAssignedIdentity
    ```

3. 기존 인스턴스를 업데이트하여 ID를 만듭니다.

    ```azurepowershell-interactive
    # Get an API Management instance
    $apimService = Get-AzApiManagement -ResourceGroupName $resourceGroupName -Name $apiManagementName

    # Update an API Management instance
    Set-AzApiManagement -InputObject $apimService -SystemAssignedIdentity
    ```

### <a name="azure-resource-manager-template"></a>Azure Resource Manager 템플릿

API Management 인스턴스는 ID를 사용하여 리소스 정의에 다음 속성을 포함하는 방법으로 만들 수 있습니다.

```json
"identity" : {
    "type" : "SystemAssigned"
}
```

이 속성에 의해 Azure에서 API Management 인스턴스에 대한 ID를 만들어서 관리합니다.

예를 들어 전체 Azure Resource Manager 템플릿은 다음과 같이 보일 수 있습니다.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "0.9.0.0",
    "resources": [{
        "apiVersion": "2020-01-01",
        "name": "contoso",
        "type": "Microsoft.ApiManagement/service",
        "location": "[resourceGroup().location]",
        "tags": {},
        "sku": {
            "name": "Developer",
            "capacity": "1"
        },
        "properties": {
            "publisherEmail": "admin@contoso.com",
            "publisherName": "Contoso"
        },
        "identity": {
            "type": "systemAssigned"
        }
    }]
}
```

인스턴스가 생성되면 다음과 같은 추가 속성이 있습니다.

```json
"identity": {
    "type": "SystemAssigned",
    "tenantId": "<TENANTID>",
    "principalId": "<PRINCIPALID>"
}
```

`tenantId` 속성은 ID가 속한 Azure AD 테넌트를 식별합니다. `principalId` 속성은 인스턴스의 새 ID에 대한 고유 식별자입니다. Azure AD 내에서 서비스 주체는 사용자가 API Management 인스턴스에 지정한 이름과 동일한 이름을 갖습니다.

> [!NOTE]
> API Management 인스턴스에는 시스템 할당 ID와 사용자 할당 ID 둘 다가 동시에 있을 수 있습니다. 이 경우에 `type` 속성이 `SystemAssigned,UserAssigned`가 됩니다.

## <a name="supported-scenarios-using-system-assigned-identity"></a>시스템 할당 ID 사용의 지원되는 시나리오

### <a name="obtain-a-custom-tlsssl-certificate-for-the-api-management-instance-from-azure-key-vault"></a><a name="use-ssl-tls-certificate-from-azure-key-vault"></a>Azure Key Vault에서 API Management 인스턴스에 대한 사용자 지정 TLS/SSL 인증서 가져오기
API Management 인스턴스의 시스템 할당 ID를 사용하여 Azure Key Vault에 저장된 사용자 지정 TLS/SSL 인증서를 검색할 수 있습니다. 그런 다음, API Management 인스턴스의 사용자 지정 도메인에 이러한 인증서를 할당할 수 있습니다. 다음 고려 사항을 염두에 두십시오.

- 비밀의 콘텐츠 형식은 *application/x-pkcs12* 이어야 합니다.
- 비밀을 포함하는 Key Vault 인증서 비밀 엔드포인트를 사용합니다.

> [!Important]
> 인증서의 개체 버전을 제공하지 않으면 API Management가 Key Vault에서 업데이트된 후 4시간 이내에 최신 버전의 인증서를 자동으로 가져옵니다.

다음 예제에서는 다음 단계를 포함하는 Azure Resource Manager 템플릿을 보여줍니다.

1. 관리 ID를 사용하여 API Management 인스턴스를 만듭니다.
2. Azure Key Vault 인스턴스의 액세스 정책을 업데이트하고 API Management 인스턴스가 비밀을 가져올 수 있도록 허용합니다.
3. Key Vault 인스턴스의 인증서를 통해 사용자 지정 도메인 이름을 설정하여 API Management 인스턴스를 업데이트합니다.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "publisherEmail": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "The email address of the owner of the service"
            }
        },
        "publisherName": {
            "type": "string",
            "defaultValue": "Contoso",
            "minLength": 1,
            "metadata": {
                "description": "The name of the owner of the service"
            }
        },
        "sku": {
            "type": "string",
            "allowedValues": ["Developer",
            "Standard",
            "Premium"],
            "defaultValue": "Developer",
            "metadata": {
                "description": "The pricing tier of this API Management instance"
            }
        },
        "skuCount": {
            "type": "int",
            "defaultValue": 1,
            "metadata": {
                "description": "The instance size of this API Management instance."
            }
        },
        "keyVaultName": {
            "type": "string",
            "metadata": {
                "description": "Name of the vault"
            }
        },
        "proxyCustomHostname1": {
            "type": "string",
            "metadata": {
                "description": "Proxy Custom hostname."
            }
        },
        "keyVaultIdToCertificate": {
            "type": "string",
            "metadata": {
                "description": "Reference to the Key Vault certificate. https://contoso.vault.azure.net/secrets/contosogatewaycertificate."
            }
        }
    },
    "variables": {
        "apiManagementServiceName": "[concat('apiservice', uniqueString(resourceGroup().id))]",
        "apimServiceIdentityResourceId": "[concat(resourceId('Microsoft.ApiManagement/service', variables('apiManagementServiceName')),'/providers/Microsoft.ManagedIdentity/Identities/default')]"
    },
    "resources": [{
        "apiVersion": "2020-01-01",
        "name": "[variables('apiManagementServiceName')]",
        "type": "Microsoft.ApiManagement/service",
        "location": "[resourceGroup().location]",
        "tags": {
        },
        "sku": {
            "name": "[parameters('sku')]",
            "capacity": "[parameters('skuCount')]"
        },
        "properties": {
            "publisherEmail": "[parameters('publisherEmail')]",
            "publisherName": "[parameters('publisherName')]"
        },
        "identity": {
            "type": "systemAssigned"
        }
    },
    {
        "type": "Microsoft.KeyVault/vaults/accessPolicies",
        "name": "[concat(parameters('keyVaultName'), '/add')]",
        "apiVersion": "2015-06-01",
        "dependsOn": [
            "[resourceId('Microsoft.ApiManagement/service', variables('apiManagementServiceName'))]"
        ],
        "properties": {
            "accessPolicies": [{
                "tenantId": "[reference(variables('apimServiceIdentityResourceId'), '2015-08-31-PREVIEW').tenantId]",
                "objectId": "[reference(variables('apimServiceIdentityResourceId'), '2015-08-31-PREVIEW').principalId]",
                "permissions": {
                    "secrets": ["get"]
                }
            }]
        }
    },
    {
        "apiVersion": "2017-05-10",
        "name": "apimWithKeyVault",
        "type": "Microsoft.Resources/deployments",
        "dependsOn": [
        "[resourceId('Microsoft.ApiManagement/service', variables('apiManagementServiceName'))]"
        ],
        "properties": {
            "mode": "incremental",
            "templateLink": {
                "uri": "https://raw.githubusercontent.com/solankisamir/arm-templates/master/basicapim.keyvault.json",
                "contentVersion": "1.0.0.0"
            },
            "parameters": {
                "publisherEmail": { "value": "[parameters('publisherEmail')]"},
                "publisherName": { "value": "[parameters('publisherName')]"},
                "sku": { "value": "[parameters('sku')]"},
                "skuCount": { "value": "[parameters('skuCount')]"},
                "proxyCustomHostname1": {"value" : "[parameters('proxyCustomHostname1')]"},
                "keyVaultIdToCertificate": {"value" : "[parameters('keyVaultIdToCertificate')]"}
            }
        }
    }]
}
```

### <a name="authenticate-to-the-back-end-by-using-an-api-management-identity"></a>API Management ID를 사용하여 백 엔드에 인증

시스템 할당 ID를 사용하여 [인증 관리 ID](api-management-authentication-policies.md#ManagedIdentity) 정책을 통해 백 엔드에 인증할 수 있습니다.

### <a name="connect-to-azure-resources-behind-ip-firewall-using-system-assigned-managed-identity"></a><a name="apim-as-trusted-service"></a>시스템 할당 관리 ID를 사용하여 IP 방화벽 뒤에 있는 Azure 리소스에 연결


API Management는 다음 리소스에 대한 신뢰할 수 있는 Microsoft 서비스입니다. 이를 통해 서비스는 방화벽 뒤에 있는 다음 리소스에 연결할 수 있습니다. 해당 리소스 인스턴스에 대한 [시스템 할당 관리 ID](../active-directory/managed-identities-azure-resources/overview.md)에 적절 한 Azure 역할을 명시적으로 할당한 후 인스턴스의 액세스 범위는 관리 ID에 할당된 Azure 역할에 해당합니다.


|Azure 서비스 | 링크|
|---|---|
|Azure Storage | [Trusted-access-to-azure-storage](../storage/common/storage-network-security.md?tabs=azure-portal#trusted-access-based-on-system-assigned-managed-identity)|
|Azure Service Bus | [Trusted-access-to-azure-service-bus](../service-bus-messaging/service-bus-ip-filtering.md#trusted-microsoft-services)|
|Azure Event Hub | [Trused-access-to-azure-event-hub](../event-hubs/event-hubs-ip-filtering.md#trusted-microsoft-services)|


## <a name="create-a-user-assigned-managed-identity"></a>사용자 할당 관리 ID 만들기

> [!NOTE]
> 사용자 할당 관리 ID를 최대 10개까지 API Management 인스턴스를 연결할 수 있습니다.

### <a name="azure-portal"></a>Azure portal

포털에서 관리 ID를 설정하려면 먼저 API Management 인스턴스를 만든 다음, 기능을 사용하도록 설정합니다.

1. 평소처럼 포털에서 API Management 인스턴스를 만듭니다. 포털에서 찾아봅니다.
2. **관리 ID** 를 선택합니다.
3. **사용자 할당** 탭에서 **추가** 를 클릭합니다.
4. 이전에 만든 ID를 검색하고 이를 선택합니다. **추가** 를 선택합니다.

   :::image type="content" source="./media/api-management-msi/enable-user-assigned-msi.png" alt-text="사용자 할당 관리 ID 사용을 위한 선택 항목" border="true":::

### <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

다음 단계에서는 API Management 인스턴스를 만들고 Azure PowerShell을 사용하여 ID를 할당하는 과정을 안내합니다.

1. 필요한 경우 [Azure PowerShell 가이드](/powershell/azure/install-az-ps)의 지침에 따라 Azure PowerShell을 설치합니다. 그런 다음, `Connect-AzAccount`를 실행하여 Azure와 연결합니다.

2. 다음 코드를 사용하여 인스턴스를 만듭니다. API Management 인스턴스에서 Azure PowerShell을 사용하는 방법에 대한 예제는 [API Management PowerShell 샘플](powershell-samples.md)을 참조하세요.

    ```azurepowershell-interactive
    # Create a resource group.
    New-AzResourceGroup -Name $resourceGroupName -Location $location

    # Create a user-assigned identity. This requires installation of the "Az.ManagedServiceIdentity" module.
    $userAssignedIdentity = New-AzUserAssignedIdentity -Name $userAssignedIdentityName -ResourceGroupName $resourceGroupName

    # Create an API Management Consumption Sku service.
    $userIdentities = @($userAssignedIdentity.Id)

    New-AzApiManagement -ResourceGroupName $resourceGroupName -Location $location -Name $apiManagementName -Organization contoso -AdminEmail admin@contoso.com -Sku Consumption -UserAssignedIdentity $userIdentities
    ```

3. 서비스에 ID를 할당하도록 기존 서비스를 업데이트합니다.

    ```azurepowershell-interactive
    # Get an API Management instance
    $apimService = Get-AzApiManagement -ResourceGroupName $resourceGroupName -Name $apiManagementName

    # Create a user-assigned identity. This requires installation of the "Az.ManagedServiceIdentity" module.
    $userAssignedIdentity = New-AzUserAssignedIdentity -Name $userAssignedIdentityName -ResourceGroupName $resourceGroupName

    # Update an API Management instance
    $userIdentities = @($userAssignedIdentity.Id)
    Set-AzApiManagement -InputObject $apimService -UserAssignedIdentity $userIdentities
    ```

### <a name="azure-resource-manager-template"></a>Azure Resource Manager 템플릿

API Management 인스턴스는 ID를 사용하여 리소스 정의에 다음 속성을 포함하는 방법으로 만들 수 있습니다.

```json
"identity": {
    "type": "UserAssigned",
    "userAssignedIdentities": {
        "<RESOURCEID>": {}
    }
}
```

사용자 할당 형식을 추가하면 Azure에서 인스턴스에 대해 지정된 사용자 할당 ID를 사용하도록 지시하는 것입니다.

예를 들어 전체 Azure Resource Manager 템플릿은 다음과 같이 보일 수 있습니다.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "0.9.0.0",
    "resources": [{
        "apiVersion": "2020-12-01",
        "name": "contoso",
        "type": "Microsoft.ApiManagement/service",
        "location": "[resourceGroup().location]",
        "tags": {},
        "sku": {
            "name": "Developer",
            "capacity": "1"
        },
        "properties": {
            "publisherEmail": "admin@contoso.com",
            "publisherName": "Contoso"
        },
        "identity": {
            "type": "UserAssigned",
             "userAssignedIdentities": {
                "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', variables('identityName'))]": {}
             }
        },
         "dependsOn": [
          "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', variables('identityName'))]"
        ]
    }]
}
```

서비스가 생성되면 다음과 같은 추가 속성이 있습니다.

```json
"identity": {
    "type": "UserAssigned",
    "userAssignedIdentities": {
        "<RESOURCEID>": {
            "principalId": "<PRINCIPALID>",
            "clientId": "<CLIENTID>"
        }
    }
}
```

`principalId` 속성은 Azure AD 관리에 사용되는 ID의 고유 식별자입니다. `clientId` 속성은 런타임 호출 중 사용할 ID를 지정하는 데 사용되는 애플리케이션의 새 ID에 대한 고유 식별자입니다.

> [!NOTE]
> API Management 인스턴스에는 시스템 할당 ID와 사용자 할당 ID 둘 다가 동시에 있을 수 있습니다. 이 경우에 `type` 속성이 `SystemAssigned,UserAssigned`가 됩니다.

## <a name="supported-scenarios-using-user-assigned-managed-identity"></a>사용자 할당 관리 ID 사용의 지원되는 시나리오

### <a name="obtain-a-custom-tlsssl-certificate-for-the-api-management-instance-from-azure-key-vault"></a><a name="use-ssl-tls-certificate-from-azure-key-vault-ua"></a>Azure Key Vault에서 API Management 인스턴스에 대한 사용자 지정 TLS/SSL 인증서 가져오기
사용자 할당 ID를 사용하여 API Management 인스턴스와 KeyVault 간의 트러스트를 설정할 수 있습니다. 그런 다음, 이 트러스트를 사용하여 Azure Key Vault에 저장된 사용자 지정 TLS/SSL 인증서를 검색할 수 있습니다. 그런 다음, API Management 인스턴스의 사용자 지정 도메인에 이러한 인증서를 할당할 수 있습니다.

다음 고려 사항을 염두에 두십시오.

- 비밀의 콘텐츠 형식은 *application/x-pkcs12* 이어야 합니다.
- 비밀을 포함하는 Key Vault 인증서 비밀 엔드포인트를 사용합니다.

> [!Important]
> 인증서의 개체 버전을 제공하지 않으면 API Management가 Key Vault에서 업데이트된 후 4시간 이내에 최신 버전의 인증서를 자동으로 가져옵니다.

전체 템플릿은 [사용자 할당 ID 방식의 KeyVault 기반 SSL을 사용하는 API Management](https://github.com/Azure/azure-quickstart-templates/blob/master/quickstarts/microsoft.apimanagement/api-management-key-vault-create/azuredeploy.json)를 참조하세요.

이 서식 파일에서 다음을 배포합니다:

* Azure API Management
* Azure 관리 사용자 할당 ID
* SSL/TLS 인증서를 저장하기 위한 Azure KeyVault

배포를 자동으로 실행하려면 다음 단추를 클릭합니다.

[![Azure에 배포](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fquickstarts%2Fmicrosoft.apimanagement%2Fapi-management-key-vault-create%2Fazuredeploy.json)

### <a name="authenticate-to-the-back-end-by-using-a-user-assigned-identity"></a>사용자 할당 ID를 사용하여 백 엔드에 인증

사용자 할당 ID를 사용하여 [인증 관리 ID](api-management-authentication-policies.md#ManagedIdentity) 정책을 통해 백 엔드에 인증할 수 있습니다.

## <a name="remove-an-identity"></a><a name="remove"></a>ID 제거

포털이 생성된 것과 동일한 방식으로 포털 또는 Azure Resource Manager 템플릿을 통해 기능을 사용하지 않도록 설정하여 시스템 할당 ID를 제거할 수 있습니다. 사용자 할당 ID는 개별 제거할 수 있습니다. 모든 ID를 제거하려면 ID 유형을 `"None"`으로 설정합니다.

이런 방식으로 시스템 할당 ID를 제거하면 Azure AD에서도 삭제됩니다. API Management 인스턴스가 삭제될 때 시스템 할당 ID도 Azure AD에서 자동으로 제거됩니다.

Azure Resource Manager 템플릿을 사용하여 모든 ID를 제거하려면 이 섹션을 업데이트합니다.

```json
"identity": {
    "type": "None"
}
```

> [!Important]
> Key Vault에서 사용자 지정 SSL 인증서를 사용하여 API Management 인스턴스를 구성하고 관리 ID를 사용하지 않도록 설정하려고 하면 요청이 실패합니다.
>
> Azure Key Vault 인증서에서 인라인으로 인코딩된 인증서로 전환한 다음, 관리 ID를 사용하지 않도록 설정하여 스스로 차단을 해제할 수 있습니다. 자세한 내용은 [사용자 지정 도메인 이름 구성](configure-custom-domain.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계

Azure 리소스의 관리 ID에 대해 자세히 알아보기

* [Azure 리소스에 대한 관리 ID란?](../active-directory/managed-identities-azure-resources/overview.md)
* [Azure 리소스 관리자 템플릿](https://github.com/Azure/azure-quickstart-templates)
* [정책의 관리 ID로 인증](./api-management-authentication-policies.md#ManagedIdentity)
