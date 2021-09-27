---
title: Azure Automation 계정 인증 개요
description: 이 문서에서는 Azure Automation 계정 인증의 개요를 제공합니다.
keywords: 자동화 보안, 안전한 자동화, 자동화 인증
services: automation
ms.subservice: process-automation
ms.date: 08/02/2021
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 5a86a5c8c0922e0861411e93376047344ba6c5af
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124789107"
---
# <a name="azure-automation-account-authentication-overview"></a>Azure Automation 계정 인증 개요

Azure Automation을 사용하여 Azure, 온-프레미스 및 AWS(Amazon 웹 서비스)와 같은 기타 클라우드 공급자의 리소스에 대해 작업을 자동화할 수 있습니다. Runbook을 사용하여 작업을 자동화하거나 Azure 외부에서 관리하는 비즈니스 또는 운영 프로세스가 있는 경우에는 Hybrid Runbook Worker를 사용할 수 있습니다. 해당 환경 중 하나에서 작업하려면 필요한 최소 권한으로 리소스에 안전하게 액세스할 수 있는 권한이 필요합니다.

이 문서에서는 Azure Automation에서 지원하는 인증 시나리오를 살펴보고 관리해야 하는 환경에 따라 시작하는 방법을 설명합니다.

## <a name="automation-account"></a>Automation 계정

Azure Automation을 처음 시작하려면 Automation 계정을 하나 이상 만들어야 합니다. Automation 계정을 사용하면 다른 Automation 계정의 리소스에서 사용자의 Automation 리소스, Runbook, 자산, 구성을 격리할 수 있습니다. Automation 계정을 사용하여 리소스를 별도의 논리적 환경 또는 위임된 책임으로 구분할 수 있습니다. 예를 들어 개발, 프로덕션, 온-프레미스 환경에 서로 다른 계정을 사용할 수 있습니다. 또는 [업데이트 관리](update-management/overview.md)로 모든 컴퓨터의 운영 체제 업데이트를 관리하는 Automation 계정을 전용으로 사용할 수 있습니다. 

Azure Automation 계정은 Microsoft 계정 또는 Azure 구독에서 만든 계정과 다릅니다. Automation 계정 만들기에 대한 소개는 [Automation 계정 만들기](./quickstarts/create-account-portal.md)를 참조하세요.

## <a name="automation-resources"></a>자동화 리소스

각 Automation 계정의 Automation 리소스는 단일 Azure 지역과 연결되지만 계정은 Azure 구독 내 모든 리소스를 관리할 수 있습니다. 여러 지역에서 Automation 계정을 만드는 주된 이유는 데이터 및 리소스를 특정 지역으로 격리해야 하는 정책이 있을 수 있기 때문입니다.

Azure Automation에서 Azure Resource Manager 및 PowerShell cmdlet을 사용하여 리소스에 대해 만드는 모든 작업은 Azure AD(Azure Active Directory) 조직 ID 자격 증명 기반 인증을 사용하여 Azure에 인증해야 합니다.

## <a name="managed-identities-preview"></a>관리 ID(미리 보기)

Azure AD(Azure Active Directory)의 관리 ID를 사용하면 Runbook에서 다른 Azure AD 보호 리소스에 쉽게 액세스할 수 있습니다. ID는 Azure 플랫폼에서 관리되며, 비밀을 프로비저닝하거나 회전하지 않아도 됩니다. Azure AD의 관리 ID에 대한 자세한 내용은 [Azure 리소스의 관리 ID](../active-directory/managed-identities-azure-resources/overview.md)를 참조하세요.

관리 ID를 사용하는 경우 얻을 수 있는 몇 가지 혜택은 다음과 같습니다.

- Automation 실행 계정 대신 관리 ID를 사용하면 관리가 더 간단해집니다. 실행 계정에서 사용되는 인증서를 갱신할 필요가 없습니다.

- 관리 ID는 추가 비용 없이 사용할 수 있습니다.

- Automation 실행 계정에서 사용하는 인증서를 갱신할 필요가 없습니다.

- Runbook 코드에서 실행 연결 개체를 지정할 필요가 없습니다. 인증서, 연결, 실행 계정 등을 만들지 않고 Runbook에서 Automation 계정의 관리 ID를 사용하여 리소스에 액세스할 수 있습니다.

Automation 계정에는 다음 두 가지 유형의 ID를 부여할 수 있습니다.

- 시스템 할당 ID는 애플리케이션에 연결되어 있어 해당 앱을 삭제하면 이 ID도 삭제됩니다. 앱에는 하나의 시스템 할당 ID만 있을 수 있습니다.

- 사용자 할당 ID는 앱에 할당할 수 있는 독립 실행형 Azure 리소스입니다. 앱에는 여러 사용자 할당 ID가 있을 수 있습니다.

> [!NOTE]
> 사용자가 할당한 ID는 클라우드 작업에서만 지원됩니다. 다양한 관리 ID에 대한 자세한 내용은 [ID 유형 관리](../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types)를 참조하세요.

관리 ID 사용에 대한 자세한 내용은 [Azure Automation에 관리 ID 사용(미리 보기)](enable-managed-identity-for-automation.md)을 참조하세요.

## <a name="run-as-accounts"></a>실행 계정

Azure Automation의 실행 계정은 클래식 배포 모델로 배포된 리소스 또는 Azure Resource Manager 리소스를 관리하기 위한 인증을 제공합니다. Azure Automation에는 두 가지 유형의 실행 계정이 있습니다.

실행 계정을 만들거나 갱신하려면 다음 세 가지 수준에서 권한이 필요합니다.

- 구독,
- Azure AD(Azure Active Directory),
- Automation 계정

### <a name="subscription-permissions"></a>구독 권한

`Microsoft.Authorization/*/Write` 권한이 필요합니다. 이 권한은 다음 Azure 기본 제공 역할 중 하나의 멤버 자격을 통해 얻습니다.

- [소유자](../role-based-access-control/built-in-roles.md#owner)
- [사용자 액세스 관리자](../role-based-access-control/built-in-roles.md#user-access-administrator)

클래식 실행 계정을 구성하거나 갱신하려면 구독 수준에서 공동 관리자 역할이 있어야 합니다. 클래식 구독 권한을 자세히 알아보려면 [Azure 클래식 구독 관리자](../role-based-access-control/classic-administrators.md#add-a-co-administrator)를 참조하세요.

### <a name="azure-ad-permissions"></a>Azure AD 권한

서비스 주체를 만들거나 갱신하려면 다음 Azure AD 기본 제공 역할 중 하나의 구성원이어야 합니다.

- [애플리케이션 관리자](../active-directory/roles/permissions-reference.md#application-administrator)
- [애플리케이션 개발자](../active-directory/roles/permissions-reference.md#application-developer)

디렉터리 수준에서 테넌트의 **모든** 사용자에게 멤버 자격을 할당할 수 있으며, 이것이 기본 동작입니다. 디렉터리 수준에서 두 역할 중 하나에 멤버 자격을 부여할 수 있습니다. 자세한 내용은 [Azure AD 인스턴스에 애플리케이션을 추가할 수 있는 권한이 있는 사람은 누구인가요?](../active-directory/develop/active-directory-how-applications-are-added.md#who-has-permission-to-add-applications-to-my-azure-ad-instance)를 참조하세요.

### <a name="automation-account-permissions"></a>Automation 계정 권한

Automation 계정을 만들거나 업데이트하려면 다음 Automation 계정 역할 중 하나의 구성원이어야 합니다.

- [소유자](./automation-role-based-access-control.md#owner)
- [기여자](./automation-role-based-access-control.md#contributor)
- [사용자 지정 Azure Automation 기여자](./automation-role-based-access-control.md#custom-azure-automation-contributor-role)

Azure Resource Manager 및 클래식 배포 모델에 대한 자세한 내용은 [Resource Manager 및 클래식 배포](../azure-resource-manager/management/deployment-models.md)를 참조하세요.

>[!NOTE]
>Azure CSP(클라우드 솔루션 공급자) 구독은 Azure Resource Manager 모델만 지원합니다. Azure Resource Manager 기반이 아닌 서비스는 프로그램에서 사용할 수 없습니다. CSP 구독을 사용하는 경우 Azure 클래식 실행 계정이 생성되지 않지만 Azure 실행 계정은 생성됩니다. CSP 구독에 대해 자세히 알아보려면 [CSP 구독에서 사용 가능한 서비스](/azure/cloud-solution-provider/overview/azure-csp-available-services)를 참조하세요.

Automation 계정을 만들면 실행 계정은 기본적으로 자체 서명된 인증서와 함께 동시에 만들어집니다. Automation 계정과 함께 만들지 않기로 선택하면 나중에 개별적으로 만들 수 있습니다. Azure 클래식 실행 계정은 선택 사항이며 클래식 리소스를 관리해야 하는 경우 별도로 생성됩니다.

자체 서명된 기본 인증서 대신 엔터프라이즈 또는 타사 CA(인증 기관)에서 발급한 인증서를 사용하려면 실행 및 클래식 실행 계정에 대한 [실행 계정을 만드는 PowerShell 스크립트](create-run-as-account.md#powershell-script-to-create-a-run-as-account) 옵션을 사용할 수 있습니다.


> [!VIDEO https://www.microsoft.com/videoplayer/embed/RWwtF3]

### <a name="run-as-account"></a>실행 계정

실행 계정을 만들 때는 다음 작업을 수행합니다.

* 자체 서명된 인증서로 Azure AD 애플리케이션을 만들고, Azure AD에 애플리케이션의 서비스 주체 계정을 만들며, 현재 구독에 있는 계정에 대해 [기여자](../role-based-access-control/built-in-roles.md#contributor) 역할을 할당합니다. 인증서 설정을 [Reader](../role-based-access-control/built-in-roles.md#reader) 또는 다른 어떤 역할로든 변경할 수 있습니다. 자세한 내용은 [Azure Automation의 역할 기반 액세스 제어](automation-role-based-access-control.md)를 참조하세요.

* 지정된 Automation 계정에서 `AzureRunAsCertificate`라는 Automation 인증서 자산을 만듭니다. 인증서 자산은 Azure AD 애플리케이션에서 사용하는 인증서 프라이빗 키를 보유합니다.

* 지정된 Automation 계정에서 `AzureRunAsConnection`이라는 Automation 연결 자산을 만듭니다. 이 연결 자산은 애플리케이션 ID, 테넌트 ID, 구독 ID 및 인증서 지문을 보유합니다.

### <a name="azure-classic-run-as-account"></a>Azure 클래식 실행 계정

Azure 클래식 실행 계정을 만들 때 다음 작업을 수행합니다.

> [!NOTE]
> 이 유형의 실행 계정을 만들거나 갱신하려면 구독의 공동 관리자여야 합니다.

* 구독에 관리 인증서를 만듭니다.

* 지정된 Automation 계정에서 `AzureClassicRunAsCertificate`라는 Automation 인증서 자산을 만듭니다. 인증서 자산은 관리 인증서에서 사용되는 인증서 프라이빗 키를 보유합니다.

* 지정된 Automation 계정에서 `AzureClassicRunAsConnection`이라는 Automation 연결 자산을 만듭니다. 연결 자산은 구독 이름, 구독 ID 및 인증서 자산 이름을 보유합니다.

## <a name="service-principal-for-run-as-account"></a>실행 계정에 대한 서비스 주체

실행 계정의 서비스 주체에는 기본적으로 Azure AD를 읽을 수 있는 권한이 없습니다. Azure AD를 읽거나 관리하는 권한을 추가하려면 **API 권한** 에서 서비스 주체에 대한 권한을 부여해야 합니다. 자세히 알아보려면 [웹 API 액세스 권한 추가](../active-directory/develop/quickstart-configure-app-access-web-apis.md#add-permissions-to-access-your-web-api)를 참조하세요.

## <a name="run-as-account-permissions"></a><a name="permissions"></a>실행 계정 권한

이 섹션에서는 일반 실행 계정 및 클래식 실행 계정의 권한을 둘 다 정의합니다.

* 실행 계정을 만들거나 업데이트하기 위해 Azure Active Directory의 애플리케이션 관리자와 구독의 소유자가 모든 작업을 완료할 수 있습니다.
* 클래식 실행 계정을 구성하거나 갱신하려면 구독 수준에서 공동 관리자 역할이 있어야 합니다. 클래식 구독 권한을 자세히 알아보려면 [Azure 클래식 구독 관리자](../role-based-access-control/classic-administrators.md#add-a-co-administrator)를 참조하세요.

업무가 구분되어 있는 경우를 위해 다음 표에 작업, 해당 cmdlet 및 필요한 권한의 목록이 나와 있습니다.

|Task|Cmdlet  |최소 권한  |권한을 설정하는 위치|
|---|---------|---------|---|
|Azure AD 애플리케이션 만들기|[New-AzADApplication](/powershell/module/az.resources/new-azadapplication)     | 애플리케이션 개발자 역할<sup>1</sup>        |[Azure AD](../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app)</br>홈 > Azure AD > 앱 등록 |
|애플리케이션에 자격 증명을 추가합니다.|[New-AzADAppCredential](/powershell/module/az.resources/new-azadappcredential)     | 애플리케이션 관리자 또는 전역 관리자<sup>1</sup>         |[Azure AD](../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app)</br>홈 > Azure AD > 앱 등록|
|Azure AD 서비스 주체 만들기 및 가져오기|[New-AzADServicePrincipal](/powershell/module/az.resources/new-azadserviceprincipal)</br>[Get-AzADServicePrincipal](/powershell/module/az.resources/get-azadserviceprincipal)     | 애플리케이션 관리자 또는 전역 관리자<sup>1</sup>        |[Azure AD](../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app)</br>홈 > Azure AD > 앱 등록|
|지정된 보안 주체의 Azure 역할 할당 또는 가져오기|[New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment)</br>[Get-AzRoleAssignment](/powershell/module/Az.Resources/Get-AzRoleAssignment)      | 사용자 액세스 관리자 또는 소유자이거나 다음 권한이 있어야 합니다.</br></br><code>Microsoft.Authorization/Operations/read</br>Microsoft.Authorization/permissions/read</br>Microsoft.Authorization/roleDefinitions/read</br>Microsoft.Authorization/roleAssignments/write</br>Microsoft.Authorization/roleAssignments/read</br>Microsoft.Authorization/roleAssignments/delete</code></br></br> | [구독](../role-based-access-control/role-assignments-portal.md)</br>홈 > 구독 > \<subscription name\> - 액세스 제어(IAM)|
|Automation 인증서 생성 또는 제거|[New-AzAutomationCertificate](/powershell/module/Az.Automation/New-AzAutomationCertificate)</br>[Remove-AzAutomationCertificate](/powershell/module/az.automation/remove-azautomationcertificate)     | 리소스 그룹의 기여자         |Automation 계정 리소스 그룹|
|Automation 연결 생성 또는 제거|[New-AzAutomationConnection](/powershell/module/az.automation/new-azautomationconnection)</br>[Remove-AzAutomationConnection](/powershell/module/az.automation/remove-azautomationconnection)|리소스 그룹의 기여자 |Automation 계정 리소스 그룹|

<sup>1</sup> Azure AD 테넌트의 **사용자 설정** 페이지에 있는 **사용자가 애플리케이션을 등록할 수 있음** 옵션이 **예** 로 설정된 경우 Azure AD 테넌트의 관리자가 아닌 사용자가 [AD 애플리케이션을 등록](../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app)할 수 있습니다. 애플리케이션 등록 설정이 **아니요** 인 경우 이 작업을 수행하는 사용자를 이 표에서 정의해야 합니다.

구독의 전역 관리자 역할에 추가되기 전에 구독 Active Directory 인스턴스의 멤버가 아닌 경우 게스트로 추가됩니다. 이 경우에는 **Automation 계정 추가** 페이지에 `You do not have permissions to create…` 경고가 표시됩니다.

오류 메시지를 생성하는 상황이 해결되었는지 확인하려면 다음을 수행합니다.

1. Azure Portal의 Azure Active Directory 창에서 **사용자 및 그룹** 을 선택합니다.
2. **모든 사용자** 를 선택합니다.
3. 이름을 선택하고 **프로필** 을 선택합니다.
4. 사용자 프로필 아래의 **사용자 유형** 특성 값이 **게스트** 로 설정되지 않았는지 확인합니다.

## <a name="role-based-access-control"></a>역할 기반 액세스 제어

Azure Resource Manager에서 역할 기반 액세스 제어를 사용하여 Azure AD 사용자 계정 및 실행 계정에 허용된 작업을 수락하고 서비스 주체를 인증할 수 있습니다. Automation 사용 권한 관리 모델을 개발하는 방법에 대한 자세한 내용은 [Azure Automation에서 역할 기반 액세스 제어 문서](automation-role-based-access-control.md)를 참조하세요.

리소스 그룹의 권한 할당에 대한 엄격한 보안 컨트롤이 있는 경우 실행 계정 멤버 자격을 리소스 그룹의 **참가자** 역할에 할당해야 합니다.

> [!NOTE]
> Automation 작업을 실행하는 데 **Log Analytics 기여자** 역할을 사용하지 않는 것이 좋습니다. 대신 Azure Automation 기여자 사용자 지정 역할을 만들고 Automation 계정과 관련된 작업에 사용합니다. 자세한 내용은 [사용자 지정 Azure Automation 기여자 역할](./automation-role-based-access-control.md#custom-azure-automation-contributor-role)을 참조하세요.

## <a name="runbook-authentication-with-hybrid-runbook-worker"></a>Hybrid Runbook Worker를 사용하여 Runbook 인증

데이터 센터의 Hybrid Runbook Worker에서 실행되거나 AWS와 같은 기타 클라우드 환경의 컴퓨팅 서비스에서 실행되는 Runbook은 일반적으로 Azure 리소스를 인증하는 Runbook에 사용되는 것과 동일한 방법을 사용할 수 없습니다. 이러한 리소스는 Azure 외부에서 실행되므로 로컬로 액세스하는 리소스에 인증하려면 Automation에서 정의한 자체 보안 자격 증명이 필요하기 때문입니다. Runbook Workers를 사용하는 Runbook 인증에 대한 자세한 내용은 [Hybrid Runbook Worker에서 Runbook 실행](automation-hrw-run-runbooks.md)을 참조하세요.

Azure VM에서 Hybrid Runbook Worker를 사용하는 Runbook의 경우 실행 계정 대신 [관리형 ID로 Runbook 인증](automation-hrw-run-runbooks.md#runbook-auth-managed-identities)을 사용하여 Azure 리소스를 인증할 수 있습니다.

## <a name="next-steps"></a>다음 단계

* Azure Portal에서 Automation 계정을 만들려면 [독립 실행형 Azure Automation 계정 만들기](automation-create-standalone-account.md)를 참조하세요.
* 템플릿을 사용하여 계정을 만들려면 [Azure Resource Manager 템플릿을 사용하여 Automation 계정 만들기](quickstart-create-automation-account-template.md)를 참조하세요.
* Amazon Web Services를 사용하는 인증의 경우 [Amazon Web Services를 사용하여 Runbook 인증](automation-config-aws-account.md)을 참조하세요.
* Azure 리소스에 대한 관리 ID 기능을 지원하는 Azure 서비스 목록은 [Azure 리소스에 대한 관리 ID를 지원하는 서비스](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md)를 참조하세요.