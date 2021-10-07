---
title: Service Fabric 관리형 클러스터의 다른 Azure 리소스에 대한 애플리케이션 액세스 권한 부여
description: 이 문서에서는 Service Fabric 관리형 클러스터에서 Azure Active Directory 기반 인증을 지원하는 다른 Azure 리소스에 대한 관리 ID 지원 Service Fabric 애플리케이션 액세스 권한을 부여하는 방법을 설명합니다.
ms.topic: article
ms.date: 10/05/2021
ms.openlocfilehash: 6bead810e52c53f6c045e6d13d8035542f73ae39
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/06/2021
ms.locfileid: "129615280"
---
# <a name="granting-a-service-fabric-applications-managed-identity-access-to-azure-resources-on-a-service-fabric-managed-cluster"></a>Service Fabric 관리형 클러스터에서 Azure 리소스에 대한 Service Fabric 애플리케이션의 관리 ID 액세스 권한 부여

애플리케이션이 관리 ID를 사용하여 다른 리소스에 액세스하려면 먼저 액세스 중인 보호된 Azure 리소스에 대한 해당 ID에 권한을 부여해야 합니다. 권한 부여는 일반적으로 Azure Resource Manager를 통해 라우팅되는 보호된 리소스를 소유하는 Azure 서비스의 '컨트롤 플레인'에 대한 관리 작업으로, 적용 가능한 역할 기반 액세스 검사를 적용합니다.

정확한 단계 순서는 액세스되는 Azure 리소스 유형과 권한을 부여하는 데 사용되는 언어/클라이언트에 따라 달라집니다. 이 문서의 나머지 부분에서는 애플리케이션에 할당된 사용자 할당 ID를 가정하고 편의를 위해 몇 가지 일반적인 예제를 포함하지만 이 항목에 대한 완전한 참조는 아닙니다. 권한 부여에 대한 최신 지침은 해당 Azure 서비스의 설명서를 참조하세요.  

## <a name="granting-access-to-azure-storage"></a>Azure Storage에 대한 액세스 권한 부여
Service Fabric 애플리케이션의 관리 ID(이 경우 사용자 할당)를 사용하여 Azure Storage Blob에서 데이터를 검색할 수 있습니다. 다음 단계를 수행하여 Azure Portal에 필요한 권한을 ID에 부여합니다.

1. 스토리지 계정으로 이동
2. 왼쪽 패널의 액세스 제어(IAM) 링크를 클릭합니다.
3. (선택 사항) 기존 액세스 확인: '찾기' 컨트롤에서 시스템 또는 사용자 할당 관리 ID를 선택합니다. 다음 결과 목록에서 적절한 ID를 선택합니다.
4. 페이지 상단에서 + 역할 할당 추가를 클릭하여 애플리케이션 ID에 대한 새 역할 할당을 추가합니다.
역할의 드롭다운에서 Storage Blob 데이터 판독기를 선택합니다.
5. 다음 드롭다운의 액세스 할당 아래에서 `User assigned managed identity`를 선택합니다.
6. 다음으로 적절한 구독이 구독 드롭다운에 나열되는지 확인한 다음, 리소스 그룹을 모든 리소스 그룹으로 설정합니다.
7. 선택에서 Service Fabric 애플리케이션에 해당하는 UAI를 선택한 다음, 저장을 클릭합니다.

시스템 할당 Service Fabric 관리 ID에 대한 지원은 Azure Portal의 통합을 포함하지 않습니다. 애플리케이션에서 시스템 할당 ID를 사용하는 경우 먼저 애플리케이션 ID의 클라이언트 ID를 찾은 다음, 위의 단계를 반복하되 찾기 컨트롤에서 `Azure AD user, group, or service principal` 옵션을 선택해야 합니다.

## <a name="granting-access-to-azure-key-vault"></a>Azure Key Vault에 대한 액세스 권한 부여
스토리지 액세스와 마찬가지로 Service Fabric 애플리케이션의 관리 ID를 활용하여 Azure Key Vault에 액세스할 수 있습니다. Azure Portal의 액세스 권한을 부여하는 단계는 위에 나온 단계와 비슷하므로 여기서는 설명하지 않습니다. 차이점은 아래 이미지를 참조하세요.

![Key Vault 액세스 정책](../key-vault/media/vs-secure-secret-appsettings/add-keyvault-access-policy.png)

다음 예제에서는 템플릿 배포를 통해 자격 증명 모음에 대한 액세스 권한을 부여하는 방법을 보여 줍니다. 아래의 조각을 템플릿의 `resources` 요소 아래에 있는 다른 항목으로 추가합니다. 이 샘플은 각각 사용자 할당 및 시스템 할당 ID 유형 모두에 대한 액세스 권한 부여를 보여 줍니다. 해당하는 항목을 선택하세요.

```json
    # under 'variables':
  "variables": {
        "userAssignedIdentityResourceId" : "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities/', parameters('userAssignedIdentityName'))]",
    }
    # under 'resources':
    {
        "type": "Microsoft.KeyVault/vaults/accessPolicies",
        "name": "[concat(parameters('keyVaultName'), '/add')]",
        "apiVersion": "2018-02-14",
        "properties": {
            "accessPolicies": [
                {
                    "tenantId": "[reference(variables('userAssignedIdentityResourceId'), '2018-11-30').tenantId]",
                    "objectId": "[reference(variables('userAssignedIdentityResourceId'), '2018-11-30').principalId]",
                    "dependsOn": [
                        "[variables('userAssignedIdentityResourceId')]"
                    ],
                    "permissions": {
                        "keys":         ["get", "list"],
                        "secrets":      ["get", "list"],
                        "certificates": ["get", "list"]
                    }
                }
            ]
        }
    },
```
시스템 할당 관리 ID의 경우:
```json
    # under 'variables':
  "variables": {
        "sfAppSystemAssignedIdentityResourceId": "[concat(resourceId('Microsoft.ServiceFabric/managedClusters/applications/', parameters('clusterName'), parameters('applicationName')), '/providers/Microsoft.ManagedIdentity/Identities/default')]"
    }
    # under 'resources':
    {
        "type": "Microsoft.KeyVault/vaults/accessPolicies",
        "name": "[concat(parameters('keyVaultName'), '/add')]",
        "apiVersion": "2018-02-14",
        "properties": {
            "accessPolicies": [
            {
                    "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'))]",
                    "tenantId": "[reference(variables('sfAppSystemAssignedIdentityResourceId'), '2018-11-30').tenantId]",
                    "objectId": "[reference(variables('sfAppSystemAssignedIdentityResourceId'), '2018-11-30').principalId]",
                    "dependsOn": [
                        "[variables('sfAppSystemAssignedIdentityResourceId')]"
                    ],
                    "permissions": {
                        "secrets": [
                            "get",
                            "list"
                        ],
                        "certificates": 
                        [
                            "get", 
                            "list"
                        ]
                    }
            },
        ]
        }
    }
```

자세한 내용은 [자격 증명 모음 - 액세스 정책 업데이트](/rest/api/keyvault/vaults/updateaccesspolicy)를 참조하세요.

## <a name="next-steps"></a>다음 단계
* [관리 ID가 있는 애플리케이션을 Service Fabric 관리형 클러스터에 배포](how-to-managed-cluster-application-managed-identity.md)