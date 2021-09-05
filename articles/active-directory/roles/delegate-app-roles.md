---
title: 애플리케이션 관리 관리자 권한 위임 - Azure AD | Microsoft Docs
description: Azure Active Directory에서 애플리케이션 액세스 관리에 대한 권한 부여
services: active-directory
documentationcenter: ''
author: rolyon
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: how-to
ms.date: 11/04/2020
ms.author: rolyon
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2cd749289b9a389b495481517a56b2652fb2026f
ms.sourcegitcommit: 8000045c09d3b091314b4a73db20e99ddc825d91
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/19/2021
ms.locfileid: "122531083"
---
# <a name="delegate-app-registration-permissions-in-azure-active-directory"></a>Azure Active Directory에서 앱 등록 권한 위임

이 문서에서는 애플리케이션 관리 요구를 해결하기 위해 Azure AD(Azure Active Directory)에서 사용자 지정 역할에 부여된 권한을 사용하는 방법을 설명합니다. Azure AD에서 다음과 같은 방법으로 애플리케이션 생성 및 관리 권한을 위임할 수 있습니다.

- [애플리케이션을 만들고](#restrict-who-can-create-applications) 자신이 만든 애플리케이션을 관리할 수 있는 사용자를 제한합니다. 기본적으로 Azure AD에서 모든 사용자는 애플리케이션을 등록하고 자신이 만든 애플리케이션의 모든 측면을 관리할 수 있습니다. 이는 선택한 사람에게만 해당 사용 권한을 허용하도록 제한할 수 있습니다.
- [한 명 이상의 소유자를 애플리케이션에 할당합니다](#assign-application-owners). 사용자에게 특정 애플리케이션에 대한 Azure AD 구성의 모든 측면을 관리할 수 있는 기능을 부여하는 간단한 방법입니다.
- 모든 애플리케이션에 대해 Azure AD에서 구성을 관리할 수 있도록 액세스 권한을 부여하는 [기본 제공 관리 역할을 할당](#assign-built-in-application-admin-roles)합니다. 애플리케이션 구성과 관련이 없는 Azure AD의 다른 부분을 관리할 수 있는 권한을 부여하지 않고 광범위한 애플리케이션 구성 권한을 관리할 수 있도록 IT 전문가에게 액세스 권한을 부여하는 데 권장되는 방법입니다.
- 매우 구체적인 사용 권한을 정의하고 사용자에게 단일 애플리케이션의 범위를 제한된 소유자로 할당하거나 디렉터리 범위(모든 애플리케이션)에서 제한된 관리자로 할당하는 [사용자 지정 역할을 생성](#create-and-assign-a-custom-role-preview)합니다.

두 가지 이유로 인해, 위의 방법 중 하나를 사용하여 액세스 권한을 부여하는 것이 중요합니다. 첫째, 관리 작업을 수행하는 기능을 위임하면 전역 관리자 오버헤드가 줄어듭니다. 둘째, 제한된 권한을 사용하면 보안 상태를 개선하고 무단 액세스 가능성을 줄입니다. 역할 보안 플랜에 대한 지침은 [Azure AD에서 하이브리드 및 클라우드 배포에 대한 권한 있는 액세스 보안](security-planning.md)을 참조하세요.

## <a name="restrict-who-can-create-applications"></a>애플리케이션을 만들 수 있는 사용자 제한

기본적으로 Azure AD에서 모든 사용자는 애플리케이션을 등록하고 자신이 만든 애플리케이션의 모든 측면을 관리할 수 있습니다. 또한 누구나 회사 데이터에 액세스하는 앱에 동의할 수 있습니다. 전역 스위치를 ‘아니요’로 설정하고 선택한 사용자를 애플리케이션 개발자 역할에 추가하여 이러한 권한을 선택적으로 부여하도록 선택할 수 있습니다.

### <a name="to-disable-the-default-ability-to-create-application-registrations-or-consent-to-applications"></a>애플리케이션 등록을 만들거나 애플리케이션에 동의할 수 있는 기본 기능을 사용하지 않도록 설정하려면

1. Azure AD 조직의 전역 관리자 역할에 적합한 계정으로 Azure AD 조직에 로그인합니다.
1. 다음 중 하나 또는 두 가지 모두를 설정합니다.

    - [조직의 사용자 설정 페이지](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/UserSettings)에서 **Users can register applications(사용자가 애플리케이션을 등록할 수 있음)** 설정을 아니요로 합니다. 이렇게 하면 사용자가 애플리케이션 등록을 만들 수 있는 기본값 기능을 사용하지 않도록 설정됩니다.
    - [엔터프라이즈 애플리케이션의 사용자 설정](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/UserSettings/menuId/)에서 **Users can consent to applications accessing company data on their behalf(사용자는 애플리케이션이 사용자 대신 회사 데이터에 액세스하도록 동의할 수 있음)** 설정을 아니요로 합니다. 이렇게 하면 사용자가 대신 회사 데이터에 액세스하는 애플리케이션에 동의할 수 있는 기본값 기능이 사용되지 않습니다.

### <a name="grant-individual-permissions-to-create-and-consent-to-applications-when-the-default-ability-is-disabled"></a>기본값 기능이 사용하지 않도록 설정된 경우 애플리케이션을 만들고 동의할 수 있는 개별 사용 권한 부여

**사용자가 애플리케이션을 등록할 수 있습니다** 설정이 아니오로 설정된 경우 애플리케이션 등록을 만들 수 있는 기능을 부여하도록 애플리케이션 개발자 역할을 할당합니다. 또한 이 역할은 **사용자가 앱이 사용자 대신 회사 데이터에 액세스하는 것에 동의할 수 있음** 에 대한 설정이 ‘아니오’로 된 경우 대신 동의할 수 있는 권한을 부여합니다.

## <a name="assign-application-owners"></a>애플리케이션 소유자 할당

소유자 할당은 특정 애플리케이션 등록 또는 엔터프라이즈 애플리케이션에 대한 Azure AD 구성의 모든 측면을 관리하는 기능을 부여하는 간단한 방법입니다. 자세한 내용은 [엔터프라이즈 애플리케이션 소유자 할당](../manage-apps/assign-app-owners.md)을 참조하세요.

## <a name="assign-built-in-application-admin-roles"></a>기본 제공 애플리케이션 관리자 역할 할당

Azure AD에는 모든 애플리케이션에 대해 Azure AD의 구성 관리에 대한 액세스 권한을 부여하기 위한 기본 제공 관리자 역할 집합이 있습니다. 이러한 역할은 IT 전문가에게 애플리케이션 구성과 관련이 없는 Azure AD의 다른 부분을 관리하기 위한 액세스 권한을 부여하지 않고도 광범위한 애플리케이션 구성 권한을 관리할 수 있는 권한을 부여하는 데 권장되는 방법입니다.

- 애플리케이션 관리자: 이 역할의 사용자는 엔터프라이즈 애플리케이션, 애플리케이션 등록 및 애플리케이션 프록시 설정의 모든 측면을 만들고 관리할 수 있습니다. 또한 이 역할은 위임된 권한과 Microsoft Graph를 제외한 애플리케이션 권한에 동의하는 기능을 부여합니다. 이 역할에 할당된 사용자는 새 애플리케이션 등록 또는 엔터프라이즈 애플리케이션을 만들 때 소유자로 추가되지 않습니다.
- 클라우드 애플리케이션 관리자: 이 역할의 사용자는 애플리케이션 관리자 역할과 동일한 권한을 가집니다. 애플리케이션 프록시를 관리하는 기능은 제외됩니다. 이 역할에 할당된 사용자는 새 애플리케이션 등록 또는 엔터프라이즈 애플리케이션을 만들 때 소유자로 추가되지 않습니다.

이러한 역할에 대한 자세한 내용은 [Azure AD 기본 제공 역할](permissions-reference.md)을 참조하세요.

[Azure Active Directory를 사용하여 사용자에게 역할 할당](../fundamentals/active-directory-users-assign-role-azure-portal.md) 방법 가이드의 지침에 따라 애플리케이션 관리자 또는 클라우드 애플리케이션 관리자 역할을 할당합니다.

> [!IMPORTANT]
> 애플리케이션 관리자 및 클라우드 애플리케이션 관리자는 애플리케이션에 자격 증명을 추가하고 이러한 자격 증명을 사용하여 애플리케이션의 ID를 가장할 수 있습니다. 애플리케이션에는 관리자 역할의 사용 권한에 대한 권한 상승이 있을 수 있습니다. 이 역할의 관리자는 애플리케이션의 권한에 따라 애플리케이션을 가장하는 동안 사용자 또는 다른 개체를 잠재적으로 만들거나 업데이트할 수 있습니다.
> 두 역할 모두, 조건부 액세스 설정을 관리하는 기능은 부여하지 않습니다.

## <a name="create-and-assign-a-custom-role-preview"></a>사용자 지정 역할 만들기 및 할당(미리 보기)

사용자 지정 역할을 만들고 사용자 지정 역할을 할당하는 단계는 다음과 같습니다.

- [사용자 지정 ‘역할 정의’를 만들고](custom-create.md) [사전 설정 목록에서 해당 정의에 대한 권한을 추가](custom-available-permissions.md)합니다. 이러한 권한은 기본 제공 역할에 사용되는 권한과 동일한 권한입니다.
- 사용자 지정 역할을 할당하는 [‘역할 할당’을 만듭니다](custom-assign-powershell.md).

이 분리를 통해 단일 역할 정의를 만든 후 다른 ‘범위’에서 여러 번 할당할 수 있습니다. 사용자 지정 역할은 조직 전체의 범위 또는 단일 Azure AD 개체인 경우의 범위에서 할당될 수 있습니다. 개체 범위의 예로는 단일 앱 등록이 있습니다. 다른 범위를 사용하여, 동일한 역할 정의가 Sally에게는 조직의 모든 앱 등록에 할당되고 Naveen에게는 Contoso Expense Reports 앱 등록에만 할당됩니다.

애플리케이션 관리 권한 위임에 있어 사용자 지정 역할을 만들고 사용하는 경우에서의 팁
- 사용자 지정 역할은 Azure Portal의 최신 앱 등록 블레이드에만 액세스 권한을 부여합니다. 레거시 앱 등록 블레이드에는 액세스 권한을 부여하지 않습니다.
- 사용자 지정 역할은 “[Azure AD 관리 포털에 대한 액세스 제한](../fundamentals/users-default-permissions.md)” 사용자 설정이 예로 설정된 경우 Azure Portal에 대한 액세스 권한을 부여하지 않습니다.
- 사용자가 역할 할당을 사용하여 액세스할 수 있는 앱 등록은 앱 등록 페이지의 ‘모든 애플리케이션’ 탭에만 표시됩니다. ‘소유한 애플리케이션’ 탭에는 표시되지 않습니다.

사용자 지정 역할의 기본 사항에 대한 자세한 내용은 [사용자 지정 역할 개요](custom-overview.md)를 참조하고 [사용자 지정 역할을 만들기](custom-create.md) 및 [역할 할당](custom-assign-powershell.md) 방법을 참조하세요.

## <a name="next-steps"></a>다음 단계

- [애플리케이션 등록 하위 유형 및 권한](custom-available-permissions.md)
- [Azure AD 기본 제공 역할](permissions-reference.md)
