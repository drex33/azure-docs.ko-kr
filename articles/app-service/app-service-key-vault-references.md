---
title: Key Vault 참조 사용
description: Azure App Service 및 Azure Functions를 설정하여 Azure Key Vault 참조를 사용하는 방법을 알아봅니다. 애플리케이션 코드에 Key Vault 비밀을 사용할 수 있게 합니다.
author: mattchenderson
ms.topic: article
ms.date: 06/11/2021
ms.author: mahender
ms.custom: seodec18
ms.openlocfilehash: 381cc0d1114e60cbb9678c0df9a66176f3b51e99
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128627599"
---
# <a name="use-key-vault-references-for-app-service-and-azure-functions"></a>App Service 및 Azure Functions의 Key Vault 참조 사용

이 항목에서는 코드 변경 없이도 App Service 또는 Azure Functions 애플리케이션에서 Azure Key Vault의 비밀을 사용하는 방법을 보여 줍니다. [Azure Key Vault](../key-vault/general/overview.md)는 액세스 정책 및 감사 기록에 대한 전체 제어와 함께 중앙 집중식 비밀 관리를 제공하는 서비스입니다.

## <a name="granting-your-app-access-to-key-vault"></a>Key Vault에 앱 액세스 권한 부여

Key Vault에서 비밀을 읽으려면 자격 증명 모음을 만들고 해당 자격 증명에 앱 권한을 부여해야 합니다.

1. [Key Vault 빠른 시작](../key-vault/secrets/quick-create-cli.md)에 따라 키 자격 증명 모음을 만듭니다.

1. 애플리케이션에 대한 [관리 ID](overview-managed-identity.md)를 만듭니다.

    Key Vault 참조는 기본적으로 앱의 시스템 할당 ID를 사용하지만 [사용자가 할당한 ID를 지정](#access-vaults-with-a-user-assigned-identity)할 수 있습니다.

1. 이전에 만든 애플리케이션 ID에 대한 [Key Vault에서 액세스 정책](../key-vault/general/security-features.md#privileged-access)을 만듭니다. 이 정책에 대한 “가져오기” 비밀 권한을 사용하도록 설정합니다. "권한 있는 애플리케이션" 또는 `applicationId` 설정은 관리 ID와 호환되지 않으므로 구성하지 마세요.

### <a name="access-network-restricted-vaults"></a>네트워크 제한 자격 증명 모음 액세스

자격 증명 모음이 [네트워크 제한](../key-vault/general/overview-vnet-service-endpoints.md)으로 구성된 경우 애플리케이션에 네트워크 액세스 권한이 있는지도 확인해야 합니다.

1. [App Service 네트워킹 기능](./networking-features.md) 및 [Azure Functions 네트워킹 옵션](../azure-functions/functions-networking-options.md)에 설명된 대로 애플리케이션에 아웃바운드 네트워킹 기능이 구성되어 있는지 확인합니다.

    또한 프라이빗 엔드포인트를 사용하려는 Linux 애플리케이션은 가상 네트워크를 통해 모든 트래픽 경로를 갖도록 앱을 명시적으로 구성해야 합니다. 이 요구 사항은 향후 업데이트에서 제거됩니다. 이를 설정하려면 다음 CLI 명령을 사용합니다.

    ```azurecli
    az webapp config set --subscription <sub> -g <rg> -n <appname> --generic-configurations '{"vnetRouteAllEnabled": true}'
    ```

2. 애플리케이션에서 액세스할 네트워크 또는 서브넷에 사용할 자격 증명 모음 구성 계정이 있는지 확인합니다.

> [!NOTE]
> Windows 컨테이너는 현재 VNet 통합에 대 한 Key Vault 참조를 지원 하지 않습니다.

### <a name="access-vaults-with-a-user-assigned-identity"></a>사용자가 할당한 ID를 사용하여 자격 증명 모음에 액세스

일부 앱은 시스템 할당 ID를 아직 사용할 수 없는 경우 생성 시 비밀을 참조해야 합니다. 이러한 경우 사용자가 할당한 ID를 만들고 자격 증명 모음에 대한 액세스 권한을 미리 부여할 수 있습니다.

사용자가 할당한 ID에 대한 사용 권한을 부여한 후에는 다음 단계를 수행합니다.

1. 아직 없는 경우 애플리케이션에 [ID를 할당](./overview-managed-identity.md#add-a-user-assigned-identity)합니다.

1. `keyVaultReferenceIdentity` 속성을 사용자가 할당한 ID의 리소스 ID로 설정하여 Key Vault 참조 작업에 이 ID를 사용하도록 앱을 구성합니다.

    ```azurecli-interactive
    userAssignedIdentityResourceId=$(az identity show -g MyResourceGroupName -n MyUserAssignedIdentityName --query id -o tsv)
    appResourceId=$(az webapp show -g MyResourceGroupName -n MyAppName --query id -o tsv)
    az rest --method PATCH --uri "${appResourceId}?api-version=2021-01-01" --body "{'properties':{'keyVaultReferenceIdentity':'${userAssignedIdentityResourceId}'}}"
    ```

이 구성은 앱에 대한 모든 참조에 적용됩니다.

## <a name="reference-syntax"></a>참조 구문

Key Vault 참조는 `@Microsoft.KeyVault({referenceString})` 형식이며, 여기서 `{referenceString}`은 다음 옵션 중 하나로 대체됩니다.

> [!div class="mx-tdBreakAll"]
> | 참조 문자열                                                            | 설명                                                                                                                                                                                 |
> |-----------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
> | SecretUri=_secretUri_                                                       | **SecretUri** 는 Key Vault의 비밀에 관한 전체 데이터 평면 URI이어야 하며, 선택적으로 버전(예: `https://myvault.vault.azure.net/secrets/mysecret/` 또는 `https://myvault.vault.azure.net/secrets/mysecret/ec96f02080254f109c51a1f14cdb1931`)을 포함해야 합니다.  |
> | VaultName=_vaultName_;SecretName=_secretName_;SecretVersion=_secretVersion_ | **VaultName** 은 필수이며 Key Vault 리소스의 이름이어야 합니다. **SecretName** 은 필수이며 대상 비밀의 이름이어야 합니다. **SecretVersion** 은 선택 사항이지만, 있으면 사용할 비밀의 버전을 나타냅니다. |

예를 들어 전체 참조는 다음과 같습니다.

```
@Microsoft.KeyVault(SecretUri=https://myvault.vault.azure.net/secrets/mysecret/)
```

또는 다음과 같습니다.

```
@Microsoft.KeyVault(VaultName=myvault;SecretName=mysecret)
```

## <a name="rotation"></a>회전

참조에 버전을 지정하지 않으면 앱은 Key Vault에 있는 최신 버전을 사용합니다. 순환 이벤트와 같이 최신 버전을 사용할 수 있게 되면 앱은 자동으로 업데이트되고 1일 이내에 최신 버전을 사용하기 시작합니다. 앱에 관한 모든 구성 변경으로 인해 참조된 모든 비밀이 최신 버전으로 즉시 업데이트됩니다.

## <a name="source-application-settings-from-key-vault"></a>Key Vault의 원본 애플리케이션 설정

Key Vault 참조를 [애플리케이션 설정](configure-common.md#configure-app-settings) 값으로 사용할 수 있으므로 사이트 구성 대신 Key Vault에서 비밀을 유지할 수 있습니다. 애플리케이션 설정은 미사용 시 안전하게 암호화되지만, 비밀 관리 기능이 필요한 경우에는 Key Vault로 이동해야 합니다.

[애플리케이션 설정](configure-common.md#add-or-edit)에 Key Vault 참조를 사용하려면 참조를 설정 값으로 설정합니다. 앱은 키를 사용하여 비밀을 정상적으로 참조할 수 있습니다. 코드 변경은 필요하지 않습니다.

> [!TIP]
> Key Vault 참조를 사용하는 대부분의 애플리케이션 설정은 각 환경에 대해 별도의 자격 증명 모음이 있어야 슬롯 설정으로 표시되어야 합니다.

### <a name="considerations-for-azure-files-mounting"></a>Azure Files 탑재 시 고려 사항

앱은 `WEBSITE_CONTENTAZUREFILECONNECTIONSTRING` 애플리케이션 설정을 사용하여 Azure Files를 파일 시스템으로 탑재할 수 있습니다. 이 설정에는 앱이 제대로 시작될 수 있는지 확인하기 위한 추가 유효성 검사가 포함되어 있습니다. 플랫폼은 Azure Files 내에서 콘텐츠 공유에 따라 달라지며 `WEBSITE_CONTENTSHARE` 설정을 통해 지정되지 않는 한 기본 이름을 사용합니다. 이러한 설정을 수정하는 모든 요청에 대해 플랫폼은 이 콘텐츠 공유가 있는지 확인하고, 그렇지 않은 경우에는 이를 만들려고 시도합니다. 콘텐츠 공유를 찾거나 만들 수 없는 경우 요청이 차단됩니다.

이 설정에 대한 Key Vault 참조를 사용하는 경우 들어오는 요청을 처리하는 동안 비밀 자체를 확인할 수 없기 때문에 이 유효성 검사는 기본적으로 실패합니다. 이 문제를 방지하려면 `WEBSITE_SKIP_CONTENTSHARE_VALIDATION`를 "1"로 설정하여 유효성 검사를 건너뛸 수 있습니다. 그러면 모든 검사가 무시되고 콘텐츠 공유가 생성되지 않습니다. 미리 만들어져 있는지 확인해야 합니다. 

> [!CAUTION]
> 유효성 검사를 건너뛰고 연결 문자열 또는 콘텐츠 공유가 유효하지 않으면 앱이 제대로 시작되지 않고 HTTP 500 오류만 제공됩니다.

사이트를 만드는 과정에서 전파되지 않은 관리 ID 권한 또는 설정되지 않은 가상 네트워크 통합으로 인해 콘텐츠 공유의 탑재가 실패할 수도 있습니다. 배포 템플릿에서 나중에 이를 수용할 때까지 Azure Files 설정을 연기할 수 있습니다. 자세한 내용은 [Azure Resource Manager 배포](#azure-resource-manager-deployment)를 참조하세요. App Service는 Azure Files가 설정될 때까지 기본 파일 시스템을 사용하고 파일은 복사되지 않으므로 Azure Files를 탑재하기 전에 중간 기간 동안 배포 시도가 발생하지 않도록 해야 합니다.

### <a name="azure-resource-manager-deployment"></a>Azure Resource Manager 배포

Azure Resource Manager 템플릿을 통해 리소스 배포를 자동화할 때 이 기능이 작동하려면 종속성을 특정 순서로 시퀀스해야 할 수 있습니다. 중요한 것은, 사이트 정의에서 `siteConfig` 속성을 사용하는 대신 애플리케이션 설정을 자체 리소스로 정의해야 합니다. 이는 사이트를 먼저 정의해야 사이트를 사용하여 시스템 할당 ID를 만들고 액세스 정책에서 사용할 수 있기 때문입니다.

함수 앱을 위한 예제 의사 템플릿은 다음과 같습니다.

```json
{
    //...
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[variables('storageAccountName')]",
            //...
        },
        {
            "type": "Microsoft.Insights/components",
            "name": "[variables('appInsightsName')]",
            //...
        },
        {
            "type": "Microsoft.Web/sites",
            "name": "[variables('functionAppName')]",
            "identity": {
                "type": "SystemAssigned"
            },
            //...
            "resources": [
                {
                    "type": "config",
                    "name": "appsettings",
                    //...
                    "dependsOn": [
                        "[resourceId('Microsoft.Web/sites', variables('functionAppName'))]",
                        "[resourceId('Microsoft.KeyVault/vaults/', variables('keyVaultName'))]",
                        "[resourceId('Microsoft.KeyVault/vaults/secrets', variables('keyVaultName'), variables('storageConnectionStringName'))]",
                        "[resourceId('Microsoft.KeyVault/vaults/secrets', variables('keyVaultName'), variables('appInsightsKeyName'))]"
                    ],
                    "properties": {
                        "AzureWebJobsStorage": "[concat('@Microsoft.KeyVault(SecretUri=', reference(variables('storageConnectionStringResourceId')).secretUriWithVersion, ')')]",
                        "WEBSITE_CONTENTAZUREFILECONNECTIONSTRING": "[concat('@Microsoft.KeyVault(SecretUri=', reference(variables('storageConnectionStringResourceId')).secretUriWithVersion, ')')]",
                        "APPINSIGHTS_INSTRUMENTATIONKEY": "[concat('@Microsoft.KeyVault(SecretUri=', reference(variables('appInsightsKeyResourceId')).secretUriWithVersion, ')')]",
                        "WEBSITE_ENABLE_SYNC_UPDATE_SITE": "true"
                        //...
                    }
                },
                {
                    "type": "sourcecontrols",
                    "name": "web",
                    //...
                    "dependsOn": [
                        "[resourceId('Microsoft.Web/sites', variables('functionAppName'))]",
                        "[resourceId('Microsoft.Web/sites/config', variables('functionAppName'), 'appsettings')]"
                    ],
                }
            ]
        },
        {
            "type": "Microsoft.KeyVault/vaults",
            "name": "[variables('keyVaultName')]",
            //...
            "dependsOn": [
                "[resourceId('Microsoft.Web/sites', variables('functionAppName'))]"
            ],
            "properties": {
                //...
                "accessPolicies": [
                    {
                        "tenantId": "[reference(resourceId('Microsoft.Web/sites/', variables('functionAppName')), '2020-12-01', 'Full').identity.tenantId]",
                        "objectId": "[reference(resourceId('Microsoft.Web/sites/', variables('functionAppName')), '2020-12-01', 'Full').identity.principalId]",
                        "permissions": {
                            "secrets": [ "get" ]
                        }
                    }
                ]
            },
            "resources": [
                {
                    "type": "secrets",
                    "name": "[variables('storageConnectionStringName')]",
                    //...
                    "dependsOn": [
                        "[resourceId('Microsoft.KeyVault/vaults/', variables('keyVaultName'))]",
                        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
                    ],
                    "properties": {
                        "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountResourceId'),'2019-09-01').key1)]"
                    }
                },
                {
                    "type": "secrets",
                    "name": "[variables('appInsightsKeyName')]",
                    //...
                    "dependsOn": [
                        "[resourceId('Microsoft.KeyVault/vaults/', variables('keyVaultName'))]",
                        "[resourceId('Microsoft.Insights/components', variables('appInsightsName'))]"
                    ],
                    "properties": {
                        "value": "[reference(resourceId('microsoft.insights/components/', variables('appInsightsName')), '2019-09-01').InstrumentationKey]"
                    }
                }
            ]
        }
    ]
}
```

> [!NOTE] 
> 이 예제에서 원본 제어 배포는 애플리케이션 설정에 따라 다릅니다. 일반적으로 앱 설정 업데이트는 비동기적으로 동작하므로 안전하지 않은 동작입니다. 그러나 `WEBSITE_ENABLE_SYNC_UPDATE_SITE` 애플리케이션 설정을 포함했으므로 업데이트가 동기적입니다. 이는 애플리케이션 설정이 완전히 업데이트된 후에만 원본 제어 배포가 시작됨을 의미합니다. 더 많은 앱 설정은 [Azure App Service에서 환경 변수 및 앱 설정](reference-app-settings.md)을 참조하세요.

## <a name="troubleshooting-key-vault-references"></a>Key Vault 참조 문제 해결

참조가 제대로 확인되지 않으면 참조 값이 대신 사용됩니다. 즉, 애플리케이션 설정의 경우 값이 `@Microsoft.KeyVault(...)` 구문을 갖는 환경 변수가 만들어집니다. 이로 인해 애플리케이션에서 오류를 throw할 수 있습니다. 이 경우 특정 구조의 비밀이 필요하기 때문입니다.

이는 가장 일반적으로는 [Key Vault 액세스 정책](#granting-your-app-access-to-key-vault)의 잘못된 구성으로 인해 발생합니다. 그러나 비밀이 더 이상 존재하지 않거나 참조 자체의 구문 오류가 원인일 수도 있습니다.

구문이 올바르면 포털에서 현재 해결 상태를 확인하여 오류의 다른 원인을 확인할 수 있습니다. 애플리케이션 설정으로 이동하여 해당 참조에 관해 "편집"을 선택합니다. 설정 구성 아래에서 오류를 비롯한 상태 정보가 표시됩니다. 해당 항목이 없으면 참조 구문이 잘못되었음을 의미합니다.

기본 제공 탐지기 중 하나를 사용하여 추가 정보를 얻을 수도 있습니다.

### <a name="using-the-detector-for-app-service"></a>App Service를 위한 탐지기 사용

1. 포털에서 앱으로 이동합니다.
2. **문제 진단 및 해결** 을 선택합니다.
3. **가용성 및 성능** 을 선택하고 **웹앱 중단** 을 선택합니다.
4. **Key Vault 애플리케이션 설정 진단** 을 찾고 **추가 정보** 를 클릭합니다.


### <a name="using-the-detector-for-azure-functions"></a>Azure Functions을 위한 탐지기 사용

1. 포털에서 앱으로 이동합니다.
2. **플랫폼 기능** 으로 이동합니다.
3. **문제 진단 및 해결** 을 선택합니다.
4. **가용성 및 성능** 을 선택하고 **함수 앱 중단 또는 오류 보고** 를 선택합니다.
5. **Key Vault 애플리케이션 설정 진단** 을 클릭합니다.
