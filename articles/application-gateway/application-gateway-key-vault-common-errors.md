---
title: Application Gateway의 일반적인 Key Vault 오류
titleSuffix: Azure Application Gateway
description: 이 문서는 Key Vault 관련 문제를 식별하고 Application Gateway의 원활한 운영을 위해 이를 해결할 수 있도록 도와줍니다.
services: application-gateway
author: jaesoni
ms.service: application-gateway
ms.topic: reference
ms.date: 07/12/2021
ms.author: jaysoni
ms.openlocfilehash: 9be1caf74a7eeaa225c2025767eeef86053c17a9
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122536843"
---
# <a name="common-key-vault-errors-in-application-gateway"></a>Application Gateway의 일반적인 Key Vault 오류

이 문제 해결 가이드는 Key Vault 오류 코드에 대한 세부 정보와 원인 및 문제를 일으키는 연관된 Key Vault 리소스를 이해하는 데 도움을 줍니다. 또한 잘못된 구성의 해결을 위한 단계별 가이드도 포함되어 있습니다.

> [!NOTE]
> Application Gateway에서 Key Vault 진단을 위한 로그는 4시간 간격으로 생성됩니다. 따라서 일부 경우에는 구성을 수정한 후에도 진단에 오류가 계속 표시될 경우 로그가 새로 고침될 때까지 기다려야 할 수 있습니다.

> [!TIP]
> 버전이 없는 비밀 식별자를 사용하는 것이 좋습니다. 이렇게 하면 Key Vault에서 새 버전을 사용할 수 있는 경우 Application Gateway가 인증서를 자동으로 순환합니다.  `https://myvault.vault.azure.net/secrets/mysecret/` 버전이 없는 비밀 URI의 예입니다.

### <a name="list-of-error-codes-and-their-details"></a>오류 코드 목록 및 세부 정보

[comment]: # (오류 코드 1)
#### <a name="1-error-code-userassignedidentitydoesnothavegetpermissiononkeyvault"></a>**1) 오류 코드:** UserAssignedIdentityDoesNotHaveGetPermissionOnKeyVault 

**설명:** 연결된 사용자가 할당한 관리 ID에는 GET 권한이 없습니다. 

**해결 방법:** 연관된 사용자가 할당한 관리 ID에 비밀에 대한 GET 권한을 부여하도록 Key Vault의 액세스 정책을 구성합니다. 
1. Azure Portal에서 연결된 Key Vault로 이동
2. 액세스 정책 블레이드 열기
3. 권한 모델에 대해 “Vault 액세스 정책” 선택
4. 지정된 사용자가 할당한 관리 ID에 대해 비밀에 대한 “Get” 권한 선택
5. 구성 저장


:::image type="content" source="./media/application-gateway-key-vault-common-errors/no-get-permssion-for-managed-identity.png " alt-text=" 사용자가 할당한 ID에는 Key Vault에 대한 Get 권한이 포함되지 않습니다.":::

Key Vault의 액세스 정책에 대한 자세한 내용은 이 [문서](../key-vault/general/assign-access-policy-portal.md)를 참조하세요.
</br></br>



[comment]: # (오류 코드 2)
#### <a name="2-error-code-secretdisabled"></a>**2) 오류 코드:** SecretDisabled 

**설명:** 연관된 인증서가 Key Vault에서 사용되지 않습니다. 

**해결 방법:** 현재 Application Gateway에서 사용되는 인증서 버전을 다시 사용하도록 설정합니다.
1. Azure Portal에서 연결된 Key Vault로 이동
2. 인증서 블레이드 열기
3. 필요한 인증서 이름을 클릭한 후 사용되지 않는 버전을 클릭합니다.
4. 관리 페이지에서 토글을 사용하여 인증서 버전을 사용하도록 설정합니다.

:::image type="content" source="./media/application-gateway-key-vault-common-errors/secret-disabled.png" alt-text="비밀을 다시 사용하도록 설정합니다.":::
</br></br>


[comment]: # (오류 코드 3)
#### <a name="3-error-code-secretdeletedfromkeyvault"></a>**3) 오류 코드:** SecretDeletedFromKeyVault 

**설명:** 연관된 인증서가 Key Vault에서 삭제되었습니다. 

**해결 방법:** Key Vault 내에서 삭제된 인증서 개체는 일시 삭제 복구 기능을 사용하여 복원될 수 있습니다. 삭제된 인증서를 복구하려면 다음 안내를 따르세요. 
1. Azure Portal에서 연결된 Key Vault로 이동
2. 인증서 블레이드 열기
3. “관리되는 삭제된 인증서” 탭을 사용하여 삭제된 인증서를 복구합니다.

반면에 인증서 개체가 영구적으로 삭제된 경우에는 새 인증서를 만들고 새로운 인증서 세부 정보를 사용해서 Application Gateway를 업데이트해야 합니다. Azure CLI 또는 Azure PowerShell을 통해 구성할 때는 버전이 없는 비밀 식별자 URI를 사용하여 인스턴스가 인증서의 갱신된 버전을 검색하도록(있는 경우) 허용하는 것이 좋습니다.

:::image type="content" source="./media/application-gateway-key-vault-common-errors/secret-deleted.png " alt-text="Key Vault에서 삭제된 인증서를 복구합니다.":::
</br></br>


[comment]: # (오류 코드 4)
#### <a name="4-error-code-userassignedmanagedidentitynotfound"></a>**4) 오류 코드:** UserAssignedManagedIdentityNotFound 

**설명:** 연관된 사용자가 할당한 관리 ID가 삭제되었습니다. 

**해결 방법:** 아래의 안내에 따라 이 문제를 해결하세요.
1. 이전에 사용된 동일한 이름을 사용하고 동일한 리소스 그룹 아래에서 관리 ID를 다시 만듭니다. 자세한 내용은 리소스 활동 로그를 참조하세요. 
2. 만든 후에는 Application Gateway - Access Control(IAM)에서 새로운 관리 ID에 최소한 읽기 역할을 할당합니다.
3. 마지막으로 원하는 Key Vault 리소스로 이동하고 이 새로운 관리 ID에 대해 GET 비밀 권한을 부여하도록 액세스 정책을 설정합니다. 

[자세한 정보](./key-vault-certs.md#how-integration-works)
</br></br>

[comment]: # (오류 코드 5)
#### <a name="5-error-code-keyvaulthasrestrictedaccess"></a>**5) 오류 코드:** KeyVaultHasRestrictedAccess

**설명:** Key Vault의 네트워크 설정이 제한됩니다. 

**해결 방법:** 제한된 액세스 권한에 대해 Key Vault 방화벽을 사용하도록 설정할 때 이 문제가 발생합니다. Key Vault의 제한된 네트워크에서 Application Gateway를 구성하려면 다음과 같이 하면 됩니다.
1. Key Vault의 네트워킹 블레이드 사용
2. “방화벽 및 가상 네트워크” 탭에서 프라이빗 엔드포인트 및 선택된 네트워크 선택
3. 마지막으로 “예”를 선택하여 신뢰할 수 있는 서비스가 Key Vault 방화벽을 우회하도록 허용합니다.

:::image type="content" source="./media/application-gateway-key-vault-common-errors/key-vault-restricted-access.png" alt-text="Key Vault에 제한된 액세스가 포함됩니다.":::
</br></br>


[comment]: # (오류 코드 6)
#### <a name="6-error-code-keyvaultsoftdeleted"></a>**6) 오류 코드:** KeyVaultSoftDeleted 

**설명:** 연관된 Key Vault가 일시 삭제 상태에 있습니다. 

**해결 방법:** 일시 삭제된 Key Vault는 쉽게 복구할 수 있습니다. Azure Portal에서 Key Vaults 서비스 페이지로 이동합니다.

:::image type="content" source="./media/application-gateway-key-vault-common-errors/key-vault-soft-deleted-1.png" alt-text="Key Vault 서비스를 검색합니다.":::
</br></br>
관리되는 삭제된 자격 증명 모음 탭을 클릭합니다. 여기에서 삭제된 Key Vault 리소스를 찾고 이를 복구할 수 있습니다.
:::image type="content" source="./media/application-gateway-key-vault-common-errors/key-vault-soft-deleted-2.png" alt-text="일시 삭제를 사용하여 삭제된 Key Vault를 복구합니다.":::
</br></br>


[comment]: # (오류 코드 7)
#### <a name="7-error-code-customerkeyvaultsubscriptiondisabled"></a>**7) 오류 코드:** CustomerKeyVaultSubscriptionDisabled 

**설명:** Key Vault의 구독이 사용되지 않습니다. 

**해결 방법:** 여러 이유로 인해 Azure 구독이 사용되지 않습니다. [사용되지 않는 Azure 구독 다시 활성화](../cost-management-billing/manage/subscription-disabled.md) 가이드를 참조하고 필요한 조치를 취하세요.
</br></br>



