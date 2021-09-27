---
title: Application Gateway의 공통 키 자격 증명 모음 오류
titleSuffix: Azure Application Gateway
description: 이 문서에서는 주요 자격 증명 모음 관련 문제를 식별 하 고 Application Gateway의 원활한 작업을 위해 문제를 해결 하는 데 도움을 줍니다.
services: application-gateway
author: jaesoni
ms.service: application-gateway
ms.topic: reference
ms.date: 07/12/2021
ms.author: jaysoni
ms.openlocfilehash: 4f873e6fb751f3b368d86413c201b70634d9c7ce
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128644090"
---
# <a name="common-key-vault-errors-in-azure-application-gateway"></a>Azure 애플리케이션 게이트웨이의 공통 키 자격 증명 모음 오류

이 문서는 이러한 오류의 원인을 포함 하 여 발생할 수 있는 주요 자격 증명 모음 오류 코드의 세부 정보를 이해 하는 데 도움이 됩니다. 또한이 문서에는 이러한 잘못 된 구성을 해결 하는 단계가 포함 되어 있습니다.

> [!TIP]
> 버전을 지정 하지 않는 비밀 식별자를 사용 합니다. 이러한 방식으로 Azure Key Vault에서 최신 버전을 사용할 수 있는 경우 Azure 애플리케이션 게이트웨이는 인증서를 자동으로 회전 합니다. 버전이 없는 비밀 URI의 예 `https://myvault.vault.azure.net/secrets/mysecret/` 는 다음과 같습니다.

## <a name="list-of-error-codes-and-their-details"></a>오류 코드 목록 및 세부 정보

다음 섹션에서는 발생할 수 있는 다양 한 오류에 대해 설명 합니다. Azure Advisor에서 세부 정보를 확인 하 고 문제 해결 문서를 사용 하 여 문제를 해결할 수 있습니다. 자세한 내용은 [Azure Portal를 사용 하 여 새 권장 사항에 대 한 Azure Advisor 경고 만들기](https://docs.microsoft.com/azure/advisor/advisor-alerts-portal)를 참조 하세요.

> [!NOTE]
> Azure 애플리케이션 게이트웨이는 4 시간 마다 key vault 진단에 대 한 로그를 생성 합니다. 구성을 수정한 후 진단에서 오류를 계속 표시 하면 로그가 새로 고쳐질 때까지 기다려야 할 수 있습니다.

[comment]: # (오류 코드 1)
### <a name="error-code-userassignedidentitydoesnothavegetpermissiononkeyvault"></a>오류 코드: UserAssignedIdentityDoesNotHaveGetPermissionOnKeyVault 

**설명:** 연결 된 사용자 할당 관리 id에는 "Get" 권한이 없습니다. 

**해결 방법:** 사용자 할당 관리 id에 암호에 대 한이 사용 권한을 부여 하도록 Key Vault의 액세스 정책을 구성 합니다. 
1. Azure Portal에서 연결 된 키 자격 증명 모음으로 이동 합니다.
1. **액세스 정책** 창을 엽니다.
1. **권한 모델** 에 대해 **자격 증명 모음 액세스 정책** 을 선택 합니다.
1. **비밀 관리 작업** 에서 **Get** 권한을 선택 합니다.
1. **저장** 을 선택합니다.

:::image type="content" source="./media/application-gateway-key-vault-common-errors/no-get-permssion-for-managed-identity.png " alt-text=" 권한 가져오기 오류를 해결 하는 방법을 보여 주는 스크린샷":::

자세한 내용은 [Azure Portal를 사용 하 여 Key Vault 액세스 정책 할당](../key-vault/general/assign-access-policy-portal.md)을 참조 하세요.

[comment]: # (오류 코드 2)
### <a name="error-code-secretdisabled"></a>오류 코드: SecretDisabled 

**설명:** 연결 된 인증서가 Key Vault에서 사용 하지 않도록 설정 되었습니다. 

**해결 방법:** 현재 Application Gateway에서 사용되는 인증서 버전을 다시 사용하도록 설정합니다.
1. Azure Portal에서 연결 된 키 자격 증명 모음으로 이동 합니다.
1. **인증서** 창을 엽니다.
1. 필요한 인증서 이름을 선택 하 고 사용 하지 않도록 설정 된 버전을 선택 합니다.
1. 관리 페이지에서 설정/해제를 사용 하 여 해당 인증서 버전을 사용 하도록 설정 합니다.

:::image type="content" source="./media/application-gateway-key-vault-common-errors/secret-disabled.png" alt-text="비밀을 다시 사용 하도록 설정 하는 방법을 보여 주는 스크린샷":::

[comment]: # (오류 코드 3)
### <a name="error-code-secretdeletedfromkeyvault"></a>오류 코드: SecretDeletedFromKeyVault 

**설명:** 연결 된 인증서가 Key Vault에서 삭제 되었습니다. 

**해결 방법:** 삭제 된 인증서를 복구 하려면: 
1. Azure Portal에서 연결 된 키 자격 증명 모음으로 이동 합니다.
1. **인증서** 창을 엽니다.
1. **관리 되는 삭제 된 인증서** 탭을 사용 하 여 삭제 된 인증서를 복구할 수 있습니다.

반면 인증서 개체가 영구적으로 삭제 되는 경우 새 인증서를 만들고 새 인증서 세부 정보를 사용 하 여 Application Gateway를 업데이트 해야 합니다. Azure CLI 또는 Azure PowerShell를 통해 구성 하는 경우에는 버전이 없는 비밀 식별자 URI를 사용 합니다. 이 옵션을 선택 하면 인스턴스가 있는 경우 갱신 된 버전의 인증서를 검색할 수 있습니다.

:::image type="content" source="./media/application-gateway-key-vault-common-errors/secret-deleted.png " alt-text="Key Vault에서 삭제 된 인증서를 복구 하는 방법을 보여 주는 스크린샷":::

[comment]: # (오류 코드 4)
### <a name="error-code-userassignedmanagedidentitynotfound"></a>오류 코드: UserAssignedManagedIdentityNotFound 

**설명:** 연결 된 사용자 할당 관리 id가 삭제 되었습니다. 

**해결 방법:** Id를 다시 사용 하려면 다음을 수행 합니다.
1. 이전에 사용 했던 것과 동일한 리소스 그룹에서 동일한 이름으로 관리 되는 id를 다시 만듭니다. 리소스 활동 로그에 자세한 내용이 포함 되어 있습니다. 
1. Id를 만든 후에 **Application Gateway-Access Control (IAM)** 로 이동 합니다. 최소한 **판독기** 역할에 id를 할당 합니다.
1. 마지막으로 원하는 Key Vault 리소스로 이동 하 고 해당 액세스 정책을 설정 하 여이 새로운 관리 되는 id에 대 한 암호 **가져오기** 권한을 부여 합니다. 

자세한 내용은 [통합 작동 방식](./key-vault-certs.md#how-integration-works)을 참조 하세요.

[comment]: # (오류 코드 5)
### <a name="error-code-keyvaulthasrestrictedaccess"></a>오류 코드: KeyVaultHasRestrictedAccess

**설명:** Key Vault에 대 한 제한 된 네트워크 설정이 있습니다. 

**해결 방법:** 제한 된 액세스에 Key Vault 방화벽을 사용 하는 경우이 오류가 발생 합니다. 다음 단계를 수행 하 여 제한 된 Key Vault의 네트워크에 Application Gateway를 계속 구성할 수 있습니다.
1. Key Vault에서 **네트워킹** 창을 엽니다.
1. **방화벽 및 가상 네트워크** 탭을 선택 하 고 **개인 끝점 및 선택한 네트워크** 를 선택 합니다.
1. 그런 다음 가상 네트워크를 사용 하 여 Application Gateway의 가상 네트워크 및 서브넷을 추가 합니다. 프로세스 중에 확인란을 선택 하 여 ' Microsoft KeyVault ' 서비스 엔드포인트도 구성 합니다.
1. 마지막으로, **예** 를 선택 하 여 신뢰할 수 있는 서비스에서 Key Vault의 방화벽을 우회 하도록 허용 합니다.

:::image type="content" source="./media/application-gateway-key-vault-common-errors/key-vault-restricted-access.png" alt-text="제한 된 네트워크 오류를 해결 하는 방법을 보여 주는 스크린샷":::

[comment]: # (오류 코드 6)
### <a name="error-code-keyvaultsoftdeleted"></a>오류 코드: KeyVaultSoftDeleted 

**설명:** 연결 된 키 자격 증명 모음이 일시 삭제 상태입니다. 

**해결 방법:** Azure Portal에서 *key vault* 를 검색 합니다. **서비스** 에서 **키 자격 증명 모음** 을 선택 합니다.

:::image type="content" source="./media/application-gateway-key-vault-common-errors/key-vault-soft-deleted-1.png" alt-text="Key Vault 서비스를 검색 하는 방법을 보여 주는 스크린샷":::

**관리 되는 삭제 된 자격 증명 모음** 을 선택 합니다. 여기에서 삭제 된 Key Vault 리소스를 찾아서 복구할 수 있습니다.
:::image type="content" source="./media/application-gateway-key-vault-common-errors/key-vault-soft-deleted-2.png" alt-text="삭제 된 키 자격 증명 모음을 복구 하는 방법을 보여 주는 스크린샷":::

[comment]: # (오류 코드 7)
### <a name="error-code-customerkeyvaultsubscriptiondisabled"></a>오류 코드: CustomerKeyVaultSubscriptionDisabled 

**설명:** Key Vault에 대 한 구독을 사용할 수 없습니다. 

**해결 방법:** 다양 한 이유로 Azure 구독을 사용 하지 않도록 설정할 수 있습니다. 확인 하는 데 필요한 작업을 수행 하려면 [비활성화 된 Azure 구독 다시 활성화](../cost-management-billing/manage/subscription-disabled.md)를 참조 하세요.

## <a name="next-steps"></a>다음 단계

이러한 문제 해결 문서는 Application Gateway을 계속 사용할 때 도움이 될 수 있습니다.

- [Azure Application Gateway Resource Health 개요](https://docs.microsoft.com/azure/application-gateway/resource-health-overview)
- [Azure Application Gateway 세션 선호도 문제 해결](https://docs.microsoft.com/azure/application-gateway/how-to-troubleshoot-application-gateway-session-affinity-issues)
