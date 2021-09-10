---
title: Azure Automation 실행 계정 관리
description: 이 문서에서는 PowerShell 또는 Azure Portal에서 Azure Automation 실행 계정을 관리하는 방법을 설명합니다.
services: automation
ms.subservice: ''
ms.date: 08/02/2021
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: b6248ee0815d90be83030931c011bb2e8208eeaa
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122535499"
---
# <a name="manage-an-azure-automation-run-as-account"></a>Azure Automation 실행 계정 관리

Azure Automation의 실행 계정은 Automation Runbook 및 기타 Automation 기능을 사용하여 Azure Resource Manager 또는 Azure 클래식 배포 모델에서 리소스를 관리하기 위한 인증을 제공합니다. 

이 문서에서는 다음을 포함하여 실행 계정 또는 클래식 실행 계정을 관리하는 방법을 설명합니다.

   * 자체 서명된 인증서를 갱신하는 방법
   * 엔터프라이즈 또는 타사 CA(인증 기관)에서 인증서를 갱신하는 방법
   * 실행 계정에 대한 권한 관리

Azure Automation 계정 인증, 실행 관리를 위해 필요한 사용 권한, 프로세스 자동화 시나리오 관련 참고 자료에 대한 자세한 정보는 [Automation 계정 인증 개요](automation-security-overview.md)를 참조하세요.

## <a name="renew-a-self-signed-certificate"></a><a name="cert-renewal"></a>자체 서명된 인증서 갱신

실행 계정용으로 만든 자체 서명된 인증서는 생성 날짜로부터 1년이 되는 날에 만료됩니다. 실행 계정 만료되기 전인 어떤 시점에서 인증서를 갱신해야 합니다. 만료되기 전에 언제든지 갱신할 수 있습니다.

자체 서명된 인증서를 갱신하면 큐에 대기하거나 활발하게 실행 중이며 실행 계정으로 인증되는 모든 Runbook이 부정적인 영향을 받지 않도록 현재 유효한 인증서가 보존됩니다. 인증서는 만료 날짜까지 유효합니다.

>[!NOTE]
>실행 계정이 손상되었다고 생각되면 자체 서명된 인증서를 삭제하고 다시 만들 수 있습니다.

>[!NOTE]
>엔터프라이즈 또는 타사 CA에서 발급한 인증서를 사용하도록 실행 계정을 구성하고 자체 서명된 인증서를 갱신하는 옵션을 사용하는 경우 엔터프라이즈 인증서는 자체 서명된 인증서로 교체됩니다. 이 경우 인증서를 갱신하려면 [엔터프라이즈 또는 타사 인증서 갱신](#renew-an-enterprise-or-third-party-certificate)을 참조하세요.

다음 단계를 사용하여 자체 서명된 인증서를 갱신합니다.

1. [Azure 포털](https://portal.azure.com)에 로그인합니다.

1. Automation 계정으로 이동하고 계정 설정 섹션에서 **실행 계정** 을 선택합니다.

    :::image type="content" source="media/manage-runas-account/automation-account-properties-pane.png" alt-text="Automation 계정 속성 창":::

1. **실행 계정** 속성 페이지에서 인증서 갱신이 필요한 계정에 따라 **실행 계정** 또는 **클래식 실행 계정** 을 선택합니다.

1. 선택한 계정의 **속성** 페이지에서 **인증서 갱신** 을 선택합니다.

    :::image type="content" source="media/manage-runas-account/automation-account-renew-runas-certificate.png" alt-text="실행 계정용 인증서 갱신":::

1. 인증서가 갱신되는 동안 메뉴의 **알림** 에서 진행률을 추적할 수 있습니다.

## <a name="renew-an-enterprise-or-third-party-certificate"></a>엔터프라이즈 또는 타사 인증서 갱신

모든 인증서에는 기본 제공 만료 날짜가 있습니다. 실행 계정에 할당한 인증서가 CA(인증 기관)에서 발급된 경우 만료되기 전에 새 인증서를 사용하여 실행 계정을 구성하는 다른 단계를 수행해야 합니다. 만료되기 전에 언제든지 갱신할 수 있습니다.

1. [새 인증서 만들기](./shared-resources/certificates.md#create-a-new-certificate) 단계에 따라 갱신된 인증서를 가져옵니다. 자동화하려면 인증서에 다음 구성이 필요합니다.

   * 공급자 **Microsoft Enhanced RSA 및 AES 암호화 공급자** 지정
   * 내보낼 수 있음으로 표시
   * SHA256 알고리즘을 사용하도록 구성됨
   * `*.pfx` 또는 `*.cer` 형식으로 저장됩니다. 

   인증서를 가져온 후 인증서 **지문** 값을 기록하거나 복사합니다. 이 값은 실행 연결 속성을 새 인증서로 업데이트하는 데 사용됩니다. 

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

1. **Automation 계정** 을 검색하여 선택합니다.

1. Automation 계정 페이지의 목록에서 Automation 계정을 선택합니다.

1. 왼쪽 창에서 **연결** 을 선택합니다.

1. **연결** 페이지에서 **AzureRunAsConnection** 을 선택하고 새 인증서 지문으로 **인증서 지문** 을 업데이트합니다.

1. 변경 내용을 적용하려면 **저장** 을 선택합니다.

## <a name="grant-run-as-account-permissions-in-other-subscriptions"></a>다른 구독의 실행 계정 권한 부여

Azure Automation은 하나의 구독에서 단일 Automation 계정을 사용하고 여러 구독에서 Azure Resource Manager 리소스에 대해 runbook을 실행하도록 지원합니다. 이 구성은 Azure 클래식 배포 모델을 지원하지 않습니다.

다른 구독의 [Contributor](../role-based-access-control/built-in-roles.md#contributor) 역할 또는 보다 제한적인 권한으로 실행 계정 서비스 사용자를 할당합니다. 자세한 내용은 Azure Automation의 [역할 기반 액세스 제어](automation-role-based-access-control.md)를 참조하세요. 다른 구독의 역할에 실행 계정을 할당하려면 이 작업을 수행하는 사용자 계정이 해당 구독의 **Owner** 역할의 구성원이어야 합니다.

> [!NOTE]
> 이 구성은 일반적인 Azure AD 테넌트를 사용하는 조직의 여러 구독만 지원합니다.

실행 계정 권한을 부여하기 전에 먼저 할당할 서비스 사용자의 표시 이름을 기록해야 합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. Automation 계정에서 **계정 설정** 아래의 **실행 계정** 을 선택합니다.
1. **Azure 실행 계정** 을 선택합니다.
1. **Azure 실행 계정** 페이지에서 **표시 이름** 값을 복사하거나 적어 둡니다.

역할 할당을 추가하는 방법에 대한 자세한 단계는 사용하려는 방법에 따라 다음 문서를 참조하세요.

* [Azure Portal을 사용하여 Azure 역할 할당](../role-based-access-control/role-assignments-portal.md)
* [Azure PowerShell을 사용하여 Azure 역할 할당](../role-based-access-control/role-assignments-powershell.md)
* [Azure CLI를 사용하여 Azure 역할 할당](../role-based-access-control/role-assignments-cli.md)
* [REST API를 사용하여 Azure 역할 할당](..//role-based-access-control/role-assignments-rest.md)

실행 계정을 역할에 할당한 후 runbook에서 `Set-AzContext -SubscriptionId "xxxx-xxxx-xxxx-xxxx"`을 지정하여 사용할 구독 컨텍스트를 설정합니다. 자세한 내용은 [Set-AzContext](/powershell/module/az.accounts/set-azcontext)을 참조하세요.

## <a name="limit-run-as-account-permissions"></a>실행 계정 권한 제한

Azure의 리소스에 대한 Automation 대상 지정을 제어하기 위해 [Update-AutomationRunAsAccountRoleAssignments.ps1](https://aka.ms/AA5hug8) 스크립트를 실행할 수 있습니다. 이 스크립트는 기존 실행 계정 서비스 주체를 변경하여 사용자 지정 역할 정의를 만들고 사용합니다. 이 역할에는 [Key Vault](../key-vault/index.yml)를 제외한 모든 리소스에 대한 권한이 있습니다.

>[!IMPORTANT]
>**Update-AutomationRunAsAccountRoleAssignments.ps1** 스크립트를 실행한 후에는 실행 계정을 통해 Key Vault에 액세스하는 Runbook이 더 이상 작동하지 않습니다. 스크립트를 실행하기 전에 Azure Key Vault를 호출하려면 계정에서 Runbook을 검토해야 합니다. Azure Automation Runbook에서 Key Vault에 액세스하려면 [Key Vault 권한에 실행 계정을 추가](#add-permissions-to-key-vault)해야 합니다.

실행 서비스 사용자가 수행할 수 있는 작업을 추가로 제한해야 하는 경우에는 사용자 지정 역할 정의의 `NotActions` 요소에 다른 리소스 유형을 추가할 수 있습니다. 다음 예제에서는 `Microsoft.Compute/*`에 대한 액세스를 제한합니다. 역할 정의를 위해 `NotActions`에 이 리소스 종류를 추가하면 해당 역할은 컴퓨팅 리소스에 액세스할 수 없습니다. 역할 정의를 자세히 알아보려면 [Azure 리소스에 대한 역할 정의 이해](../role-based-access-control/role-definitions.md)를 참조하세요.

```powershell
$roleDefinition = Get-AzRoleDefinition -Name 'Automation RunAs Contributor'
$roleDefinition.NotActions.Add("Microsoft.Compute/*")
$roleDefinition | Set-AzRoleDefinition
```

실행 계정에서 사용하는 서비스 사용자가 **Contributor** 역할 또는 사용자 지정 역할에 할당됐는지 확인할 수 있습니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. Automation 계정으로 이동하고 계정 설정 섹션에서 **실행 계정** 을 선택합니다.
1. **Azure 실행 계정** 을 선택합니다.
1. **역할** 을 선택하여 사용 중인 역할 정의를 찾습니다.

:::image type="content" source="media/manage-runas-account/verify-role.png" alt-text="실행 계정 역할 확인" lightbox="media/manage-runas-account/verify-role-expanded.png":::

여러 구독 또는 Automation 계정의 실행 계정에서 사용하는 역할 정의를 확인할 수도 있습니다. PowerShell 갤러리에서 [Check-AutomationRunAsAccountRoleAssignments.ps1](https://aka.ms/AA5hug5) 스크립트를 사용하여 이 작업을 수행합니다.

### <a name="add-permissions-to-key-vault"></a>Key Vault에 권한 추가

Azure Automation을 통해 Key Vault 및 실행 계정 서비스 주체가 사용자 지정 역할 정의를 사용하고 있는지 확인할 수 있습니다. 다음이 필요합니다.

* Key Vault에 대한 권한을 부여합니다.
* 액세스 정책을 설정합니다.

PowerShell 갤러리에서 [Extend-AutomationRunAsAccountRoleAssignmentToKeyVault.ps1](https://aka.ms/AA5hugb) 스크립트를 사용하여 Key Vault에 대한 실행 계정 권한을 부여할 수 있습니다. Key Vault에 대한 사용 권한 설정에 대한 세부 정보는 [Key Vault 액세스 정책 할당](../key-vault/general/assign-access-policy-powershell.md)을 참조하세요.

## <a name="resolve-misconfiguration-issues-for-run-as-accounts"></a>실행 계정의 잘못된 구성 문제 해결

실행 계정 또는 클래식 실행 계정에 필요한 일부 구성 항목이 초기 설정 중에 제대로 삭제되거나 생성되지 않았습니다. 잘못된 구성의 가능한 인스턴스는 다음과 같습니다.

* 인증서 자산
* 연결 자산
* 기여자 역할에서 제거된 실행 계정
* Azure AD의 서비스 주체 또는 애플리케이션

잘못된 구성 인스턴스에서 Automation 계정은 변경 내용을 검색하고 계정의 실행 계정 속성 창에서 ‘불완전’이라는 상태를 표시합니다.

:::image type="content" source="media/manage-runas-account/automation-account-runas-config-incomplete.png" alt-text="실행 계정 구성이 완료되지 않았습니다.":::

실행 계정을 선택하면 계정 속성 창은 다음 오류 메시지를 표시합니다.

```text
The Run As account is incomplete. Either one of these was deleted or not created - Azure Active Directory Application, Service Principal, Role, Automation Certificate asset, Automation Connect asset - or the Thumbprint is not identical between Certificate and Connection. Please delete and then re-create the Run As Account.
```

실행 계정을 [삭제](delete-run-as-account.md)하고 [다시 만들](create-run-as-account.md)어서 실행 계정 문제를 신속하게 해결할 수 있습니다.

## <a name="next-steps"></a>다음 단계

* [애플리케이션 개체 및 서비스 주체 개체](../active-directory/develop/app-objects-and-service-principals.md).
* [Azure Cloud Services 인증서 개요](../cloud-services/cloud-services-certs-create.md).
* 실행 계정을 만들거나 다시 만들려면 [실행 계정 만들기](create-run-as-account.md)를 참조하세요.
* 실행 계정을 더 이상 사용하지 않아도 된다면 [실행 계정 삭제](delete-run-as-account.md)를 참조하세요.
