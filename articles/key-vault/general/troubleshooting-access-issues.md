---
title: Azure 키 자격 증명 모음 액세스 정책 문제 해결
description: Azure 키 자격 증명 모음 액세스 정책 문제 해결
author: sebansal
ms.author: sebansal
ms.date: 08/10/2020
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.openlocfilehash: c4d82e0193d891f423245ce2743ad34ac7bcf5d1
ms.sourcegitcommit: d2875bdbcf1bbd7c06834f0e71d9b98cea7c6652
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/12/2021
ms.locfileid: "129856490"
---
# <a name="troubleshooting-azure-key-vault-access-policy-issues"></a>Azure 키 자격 증명 모음 액세스 정책 문제 해결

## <a name="frequently-asked-questions"></a>자주 묻는 질문

### <a name="i-am-not-able-to-list-or-get-secretskeyscertificate-i-am-seeing-something-went-wrong-error"></a>비밀/키/인증서를 나열하거나 가져올 수 없습니다. "문제가 발생했습니다."라는 메시지가 표시됩니다. 오류가 표시됩니다.
비밀을 나열/가져오기/만들기 또는 액세스하는 데 문제가 있는 경우 해당 작업을 수행하도록 정의된 액세스 정책이 있는지 확인합니다. [Key Vault 액세스 정책](./assign-access-policy-cli.md)

### <a name="how-can-i-identify-how-and-when-key-vaults-are-accessed"></a>키 자격 증명 모음을 어떻게 언제 액세스하는지 어떻게 식별할 수 있나요?

하나 이상의 키 자격 증명 모음을 만든 후에는 키 자격 증명 모음에 액세스하는 방법, 시기 및 사용자를 모니터링하려고 할 수도 있습니다. Azure Key Vault에 대한 로깅을 사용하도록 설정하여 모니터링을 수행할 수 있습니다. 로깅을 사용하도록 설정하는 단계별 가이드는 [자세히 알아보세요](./logging.md).

### <a name="how-can-i-monitor-vault-availability-service-latency-periods-or-other-performance-metrics-for-key-vault"></a>키 자격 증명 모음에 대한 자격 증명 모음 가용성, 서비스 대기 시간 기간 또는 기타 성능 메트릭을 어떻게 모니터링할 수 있나요?

서비스의 크기를 조정하기 시작하면 키 자격 증명 모음으로 전송되는 요청 수가 증가합니다. 이러한 수요로 인해 요청 대기 시간이 길어질 수 있으며 극단적인 경우에는 서비스의 성능에 영향을 줄 수 있는 요청이 제한됩니다. 키 자격 증명 모음 성능 메트릭을 모니터링하고 특정 임계값에 대한 경고를 받을 수 있습니다. 모니터링을 구성하는 단계별 가이드는 [자세히 알아보세요](./alert.md).

### <a name="i-am-not-able-to-modify-access-policy-how-can-it-be-enabled"></a>액세스 정책을 수정할 수 없습니다. 사용하도록 설정하려면 어떻게 해야 하나요?
액세스 정책을 수정하려면 사용자에게 충분한 AAD 권한이 있어야 합니다. 이 경우 사용자에게 더 높은 기여자 역할이 있어야 합니다.

### <a name="i-am-seeing-unknown-policy-error-what-does-that-mean"></a>'알 수 없는 정책' 오류가 표시됩니다. 어떤 의미인가요?
알 수 없는 섹션에서 액세스 정책을 볼 수 있는 두 가지 다른 가능성이 있습니다.
* 액세스 권한이 있는 이전 사용자가 있을 수 있으며, 어떤 이유로 인해 사용자가 존재하지 않을 수 있습니다.
* Powershell을 통해 액세스 정책이 추가되고 서비스 주체 대신 애플리케이션 objectid에 대한 액세스 정책이 추가된 경우입니다.

### <a name="how-can-i-assign-access-control-per-key-vault-object"></a>키 자격 증명 모음 개체별로 액세스 제어를 할당하려면 어떻게 해야 하나요? 

Key Vault RBAC 권한 모델은 개체별 사용 권한을 허용합니다. 개별 키, 비밀 및 인증서 권한은 특정 시나리오에만 사용해야 합니다.

-   계층 간에 액세스 제어를 분리해야 하는 다중 계층 애플리케이션

-   여러 애플리케이션 간에 개별 비밀 공유


### <a name="how-can-i-provide-key-vault-authenticate-using-access-control-policy"></a>액세스 제어 정책을 사용하여 키 자격 증명 모음 인증을 제공하려면 어떻게 해야 하나요?

클라우드 기반 애플리케이션을 Key Vault에 인증하는 가장 간단한 방법은 관리 ID를 사용하는 것입니다. 자세한 내용은 [Azure Key Vault에 인증](authentication.md)을 참조하세요.
온-프레미스 애플리케이션을 만들거나 로컬 개발을 수행하거나 관리 ID를 사용할 수 없는 경우, 대신 서비스 주체를 수동으로 등록하고 액세스 제어 정책을 사용하여 키 자격 증명 모음에 액세스할 수 있습니다. [액세스 제어 정책 할당](assign-access-policy-portal.md)을 참조하세요.

### <a name="how-can-i-give-the-ad-group-access-to-the-key-vault"></a>AD 그룹에 키 자격 증명 모음에 대한 액세스 권한을 부여하려면 어떻게 해야 하나요?

Azure CLI `az keyvault set-policy` 명령 또는 Azure PowerShell Set-AzKeyVaultAccessPolicy cmdlet을 사용하여 키 자격 증명 모음에 AD 그룹 사용 권한을 부여합니다. [액세스 정책 할당 - CLI](assign-access-policy-cli.md) 및 [액세스 정책 할당 - PowerShell](assign-access-policy-powershell.md)을 참조하세요.

또한 애플리케이션에는 Key Vault에 할당된 ID 및 액세스 관리(IAM) 역할이 하나 이상 필요합니다. 그렇지 않으면 로그인 할 수 없고 구독에 액세스할 수 있는 권한이 없어 실패합니다. 관리 ID가 있는 Azure AD 그룹은 토큰을 새로 고치고 적용하는 데 최대 8시간이 필요할 수 있습니다.

### <a name="how-can-i-redeploy-key-vault-with-arm-template-without-deleting-existing-access-policies"></a>기존 액세스 정책을 삭제하지 않고 ARM 템플릿을 사용하여 Key Vault를 재배포하려면 어떻게 해야 하나요?

현재 Key Vault 재배포는 Key Vault의 모든 액세스 정책을 삭제하고 ARM 템플릿의 액세스 정책으로 바꿉니다. Key Vault 액세스 정책에 대한 증분 옵션은 없습니다. Key Vault에서 액세스 정책을 유지하려면 Key Vault에서 기존 액세스 정책을 읽고 이러한 정책을 사용하여 ARM 템플릿을 채워 액세스 중단을 방지해야 합니다.

이 시나리오에 도움이 될 수 있는 또 다른 옵션은 액세스 정책의 대안으로 Azure RBAC 및 역할을 사용하는 것입니다. Azure RBAC를 사용하면 정책을 다시 지정하지 않고 키 자격 증명 모음을 다시 배포할 수 있습니다. [여기](./rbac-guide.md)에서 이 솔루션에 대해 자세히 알아볼 수 있습니다.

### <a name="recommended-troubleshooting-steps-for-following-error-types"></a>다음 오류 유형에 대한 권장되는 문제 해결 단계

* HTTP 401: 인증 되지 않은 요청 - [문제 해결 단계](rest-error-codes.md#http-401-unauthenticated-request)
* HTTP 403: 권한 부족 - [문제 해결 단계](rest-error-codes.md#http-403-insufficient-permissions)
* HTTP 429: 요청이 너무 많음 - [문제 해결 단계](rest-error-codes.md#http-429-too-many-requests)
* 키 자격 증명 모음에 대한 삭제 액세스 권한이 있는지 확인합니다. [액세스 정책 할당 - CLI](assign-access-policy-cli.md), [액세스 정책 할당 - PowerShell](assign-access-policy-powershell.md) 또는 [액세스 정책 할당 - 포털](assign-access-policy-portal.md)을 참조하세요.
* 코드에서 Key Vault를 인증하는 데 문제가 있는 경우 [Authentication SDK](https://azure.github.io/azure-sdk/posts/2020-02-25/defaultazurecredentials.html)를 사용합니다.

### <a name="what-are-the-best-practices-i-should-implement-when-key-vault-is-getting-throttled"></a>키 자격 증명 모음이 제한될 때 구현해야 하는 최선의 방법은 무엇인가요?
[여기](overview-throttling.md#how-to-throttle-your-app-in-response-to-service-limits)서 설명하는 모범 사례를 따르세요.

## <a name="next-steps"></a>다음 단계

키 자격 증명 모음 인증 오류 문제를 해결하는 방법을 알아봅니다. [Key Vault 문제 해결 가이드](rest-error-codes.md).
