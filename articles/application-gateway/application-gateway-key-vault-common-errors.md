---
title: Application Gateway 일반적인 키 자격 증명 모음 오류
titleSuffix: Azure Application Gateway
description: 이 문서에서는 키 자격 증명 모음 관련 문제를 식별하고 Application Gateway 원활한 작업을 위해 문제를 해결하는 데 도움이 됩니다.
author: jaesoni
ms.service: application-gateway
ms.topic: reference
ms.date: 07/12/2021
ms.author: jaysoni
ms.openlocfilehash: cfcacf84b5a35862c3ef1423ed58ff4d5d7f3142
ms.sourcegitcommit: 613789059b275cfae44f2a983906cca06a8706ad
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/29/2021
ms.locfileid: "129272323"
---
# <a name="common-key-vault-errors-in-azure-application-gateway"></a>Azure Application Gateway 일반적인 키 자격 증명 모음 오류

이 문서는 이러한 오류의 원인을 포함하여 발생할 수 있는 키 자격 증명 모음 오류 코드의 세부 정보를 이해하는 데 도움이 됩니다. 이 문서에는 이러한 잘못된 구성을 해결하는 단계도 포함되어 있습니다.

> [!TIP]
> 버전을 지정하지 않는 비밀 식별자를 사용합니다. 이러한 방식으로 Azure Application Gateway Azure Key Vault 최신 버전을 사용할 수 있는 경우 인증서를 자동으로 회전합니다. 버전이 없는 비밀 URI의 예는 `https://myvault.vault.azure.net/secrets/mysecret/` 입니다.

## <a name="list-of-error-codes-and-their-details"></a>오류 코드 목록 및 세부 정보

다음 섹션에서는 발생할 수 있는 다양한 오류에 대해 다룹니다. Azure Advisor 세부 정보를 찾고 이 문제 해결 문서를 사용하여 문제를 해결할 수 있습니다. 자세한 내용은 [Azure Portal 사용하여 새 권장 사항에 대한 Azure Advisor 경고 만들기를 참조하세요.](../advisor/advisor-alerts-portal.md)

> [!NOTE]
> Azure Application Gateway 4시간마다 키 자격 증명 모음 진단에 대한 로그를 생성합니다. 구성을 수정한 후에도 진단에 오류가 계속 표시되면 로그가 새로 고쳐질 때까지 기다려야 할 수 있습니다.

[comment]: # (오류 코드 1)
### <a name="error-code-userassignedidentitydoesnothavegetpermissiononkeyvault"></a>오류 코드: UserAssignedIdentityDoesNotHaveGetPermissionOnKeyVault 

**설명:** 연결된 사용자 할당 관리 ID에는 "Get" 권한이 없습니다. 

**해결 방법:** 사용자 할당 관리 ID에 비밀에 대한 이 권한을 부여하도록 Key Vault 액세스 정책을 구성합니다. 
1. Azure Portal 연결된 키 자격 증명 모음으로 이동합니다.
1. 액세스 **정책** 창을 엽니다.
1. **권한 모델에** 대해 자격 증명 모음 액세스 **정책 을** 선택합니다.
1. **비밀 관리 작업** 에서 **Get** 권한을 선택합니다.
1. **저장** 을 선택합니다.

:::image type="content" source="./media/application-gateway-key-vault-common-errors/no-get-permssion-for-managed-identity.png " alt-text=" 권한 얻기 오류를 해결하는 방법을 보여 주는 스크린샷":::

자세한 내용은 [Azure Portal 사용하여 Key Vault 액세스 정책 할당을](../key-vault/general/assign-access-policy-portal.md)참조하세요.

[comment]: # (오류 코드 2)
### <a name="error-code-secretdisabled"></a>오류 코드: SecretDisabled 

**설명:** Key Vault 연결된 인증서를 사용하지 않도록 설정했습니다. 

**해결 방법:** 현재 Application Gateway에서 사용되는 인증서 버전을 다시 사용하도록 설정합니다.
1. Azure Portal 연결된 키 자격 증명 모음으로 이동합니다.
1. **인증서** 창을 엽니다.
1. 필요한 인증서 이름을 선택한 다음, 비활성화된 버전을 선택합니다.
1. 관리 페이지에서 토글을 사용하여 해당 인증서 버전을 사용하도록 설정합니다.

:::image type="content" source="./media/application-gateway-key-vault-common-errors/secret-disabled.png" alt-text="비밀을 다시 사용하도록 설정하는 방법을 보여 주는 스크린샷":::

[comment]: # (오류 코드 3)
### <a name="error-code-secretdeletedfromkeyvault"></a>오류 코드: SecretDeletedFromKeyVault 

**설명:** 연결된 인증서가 Key Vault 삭제되었습니다. 

**해결 방법:** 삭제된 인증서를 복구하려면 다음을 수행합니다. 
1. Azure Portal 연결된 키 자격 증명 모음으로 이동합니다.
1. **인증서** 창을 엽니다.
1. **관리되는 삭제된 인증서 탭을** 사용하여 삭제된 인증서를 복구합니다.

반면에 인증서 개체가 영구적으로 삭제되면 새 인증서를 만들고 새 인증서 세부 정보로 Application Gateway 업데이트해야 합니다. Azure CLI 또는 Azure PowerShell 통해 구성하는 경우 버전 없이 비밀 식별자 URI를 사용합니다. 이 선택을 통해 인스턴스는 인증서의 갱신된 버전(있는 경우)을 검색할 수 있습니다.

:::image type="content" source="./media/application-gateway-key-vault-common-errors/secret-deleted.png " alt-text="Key Vault 삭제된 인증서를 복구하는 방법을 보여 주는 스크린샷":::

[comment]: # (오류 코드 4)
### <a name="error-code-userassignedmanagedidentitynotfound"></a>오류 코드: UserAssignedManagedIdentityNotFound 

**설명:** 연결된 사용자 할당 관리 ID가 삭제되었습니다. 

**해결 방법:** ID를 다시 사용하려면 다음을 수행합니다.
1. 이전에 사용한 이름과 동일한 리소스 그룹 아래에 관리 ID를 다시 만듭니다. 리소스 활동 로그에는 자세한 정보가 포함되어 있습니다. 
1. ID를 만든 후 Application Gateway **- Access Control(IAM)** 로 이동합니다. 최소한 ID를 **읽기** 역할로 할당합니다.
1. 마지막으로 원하는 Key Vault 리소스로 이동하고 액세스 정책을 설정하여 이 새 관리 ID에 대한 비밀 **얻기** 권한을 부여합니다. 

자세한 내용은 [통합 작동 방식을 참조하세요.](./key-vault-certs.md#how-integration-works)

[comment]: # (오류 코드 5)
### <a name="error-code-keyvaulthasrestrictedaccess"></a>오류 코드: KeyVaultHasRestrictedAccess

**설명:** Key Vault 대한 제한된 네트워크 설정이 있습니다. 

**해결 방법:** 제한된 액세스에 Key Vault 방화벽을 사용하도록 설정하면 이 오류가 발생합니다. 다음 단계를 수행하여 제한된 Key Vault 네트워크에서 Application Gateway 구성할 수 있습니다.
1. Key Vault **네트워킹** 창을 엽니다.
1. **방화벽 및 가상 네트워크 탭을** 선택하고 **프라이빗 엔드포인트 및 선택한 네트워크를 선택합니다.**
1. 그런 다음 Virtual Network를 사용하여 Application Gateway 가상 네트워크 및 서브넷을 추가합니다. 프로세스 중에 확인란을 선택하여 'Microsoft.KeyVault' 서비스 엔드포인트도 구성합니다.
1. 마지막으로, **예를** 선택하여 신뢰할 수 있는 서비스가 Key Vault 방화벽을 무시하도록 허용합니다.

:::image type="content" source="./media/application-gateway-key-vault-common-errors/key-vault-restricted-access.png" alt-text="제한된 네트워크 오류를 해결 하는 방법을 보여 주는 스크린샷.":::

[comment]: # (오류 코드 6)
### <a name="error-code-keyvaultsoftdeleted"></a>오류 코드: KeyVaultSoftDeleted 

**설명:** 연결된 키 자격 증명 모음이 일시 삭제 상태입니다. 

**해결 방법:** Azure Portal 키 자격 증명 모음을 *검색합니다.* **서비스** 아래에서 키 자격 증명 **모음을** 선택합니다.

:::image type="content" source="./media/application-gateway-key-vault-common-errors/key-vault-soft-deleted-1.png" alt-text="Key Vault 서비스를 검색하는 방법을 보여 주는 스크린샷":::

**관리되는 삭제된 자격 증명 모음을 선택합니다.** 여기에서 삭제된 Key Vault 리소스를 찾아 복구할 수 있습니다.
:::image type="content" source="./media/application-gateway-key-vault-common-errors/key-vault-soft-deleted-2.png" alt-text="삭제된 키 자격 증명 모음을 복구하는 방법을 보여 주는 스크린샷":::

[comment]: # (오류 코드 7)
### <a name="error-code-customerkeyvaultsubscriptiondisabled"></a>오류 코드: CustomerKeyVaultSubscriptionDisabled 

**설명:** Key Vault 구독을 사용할 수 없습니다. 

**해결 방법:** Azure 구독은 다양한 이유로 비활성화할 수 있습니다. 해결하는 데 필요한 작업을 수행하려면 [비활성화된 Azure 구독 다시 활성화를 참조하세요.](../cost-management-billing/manage/subscription-disabled.md)

## <a name="next-steps"></a>다음 단계

이러한 문제 해결 문서는 Application Gateway 계속 사용할 때 유용할 수 있습니다.

- [Azure Application Gateway Resource Health 개요](resource-health-overview.md)
- [Azure Application Gateway 세션 선호도 문제 해결](how-to-troubleshoot-application-gateway-session-affinity-issues.md)
