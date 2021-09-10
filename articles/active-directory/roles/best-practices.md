---
title: Azure AD 역할 모범 사례 - Azure Active Directory
description: Azure Active Directory 역할 사용의 모범 사례입니다.
services: active-directory
author: rolyon
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: conceptual
ms.date: 03/28/2021
ms.author: rolyon
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 48d424a64df215a7506130a44d57fc45d638255d
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122566950"
---
# <a name="best-practices-for-azure-ad-roles"></a>Azure AD 역할 모범 사례

이 문서에서는 Azure AD RBAC(Azure Active Directory 역할 기반 액세스 제어) 사용의 몇 가지 모범 사례를 설명합니다. 이 모범 사례는 Azure AD RBAC에 대한 Microsoft의 경험과 여러분 같은 고객의 경험에서 얻은 것입니다. 또한 [Azure AD에서 하이브리드 및 클라우드 배포를 위한 권한 있는 액세스 보안](security-planning.md)에서 자세한 보안 지침을 읽어보는 것이 좋습니다.

## <a name="1-manage-to-least-privilege"></a>1. 최소 권한으로 관리

액세스 제어 전략을 계획하는 경우 최소 권한으로 관리하는 것이 좋습니다. 최소 권한은 작업을 수행하는 데 필요한 권한만 관리자에게 부여하는 것을 의미합니다. 관리자에게 역할을 할당하는 경우 특정 권한 세트, 특정 범위, 특정 기간의 세 가지 측면을 고려해야 합니다. 처음에는 더 넓은 범위에서 더 넓은 역할을 할당하는 것이 더 편리하게 보이겠지만, 이렇게 하지 마세요. 역할과 범위를 제한함으로써 보안 주체가 손상된 경우 리소스를 위험으로부터 제한할 수 있습니다. Azure AD RBAC는 65개를 초과하는 [기본 제공 역할](permissions-reference.md)을 지원합니다. 사용자, 그룹, 애플리케이션 등의 디렉터리 개체를 관리하고 Exchange, SharePoint, Intune 등의 Microsoft 365 서비스를 관리하는 Azure AD 역할이 있습니다. Azure AD 기본 제공 역할을 더 잘 이해하려면 [Azure Active Directory의 역할 이해](concept-understand-roles.md)를 참조하세요. 사용자의 요구 사항을 충족하는 기본 제공 역할이 없는 경우 고유한 [사용자 지정 역할](custom-create.md)을 만들 수 있습니다.  
 
### <a name="finding-the-right-roles"></a>적합한 역할 찾기

다음 단계를 수행하여 적합한 역할을 찾을 수 있습니다.

1. [Azure Portal](https://portal.azure.com) 또는 [Azure AD 관리 센터](https://aad.portal.azure.com)에 로그인합니다.

1. **Azure Active Directory** > **역할 및 관리자** 를 선택하여 Azure AD 역할 목록을 확인합니다.

1. **서비스** 필터를 사용하여 역할 목록의 범위를 좁힙니다.

    ![서비스 필터가 열려 있는 Azure AD의 역할 및 관리자 페이지](./media/best-practices/roles-administrators.png)

1. [Azure AD 기본 제공 역할](permissions-reference.md) 설명서를 참조하세요. 가독성을 높이기 위해 각 역할과 연결된 권한이 함께 나열됩니다. 역할 권한의 구조와 의미를 이해하려면 [역할 권한을 이해하는 방법](permissions-reference.md#how-to-understand-role-permissions)을 참조하세요.

1. [작업별 최소 권한 역할](delegate-by-task.md) 설명서를 참조하세요.

## <a name="2-use-privileged-identity-management-to-grant-just-in-time-access"></a>2. Privileged Identity Management를 사용하여 Just-In-Time 액세스 권한 부여

최소 권한의 원칙 중 하나는 특정 기간 동안만 액세스 권한을 부여하는 것입니다. [Azure AD PIM(Privileged Identity Management)](../privileged-identity-management/pim-configure.md)을 사용하여 관리자에게 Just-In-Time 액세스 권한을 부여할 수 있습니다. Azure AD에서 PIM을 사용하도록 설정하는 것이 좋습니다. PIM을 사용하면 사용자를 Azure AD 역할의 적격 멤버로 만들 수 있습니다. 그러면 필요할 때 제한된 시간 동안 역할을 활성화할 수 있습니다. 기간이 만료되면 권한 있는 액세스가 자동으로 제거됩니다. 또한 사용자가 역할 할당을 활성화할 때 승인이 필요하거나 알림 메일을 받도록 [PIM 설정을 구성](../privileged-identity-management/pim-how-to-change-default-settings.md)할 수 있습니다. 새로운 사용자가 권한이 높은 역할에 추가되면 알림이 경고를 제공합니다. 

## <a name="3-turn-on-multi-factor-authentication-for-all-your-administrator-accounts"></a>3. 모든 관리자 계정에 대해 다단계 인증 켜기

[Microsoft의 연구에 따르면](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/your-pa-word-doesn-t-matter/ba-p/731984), MFA(다단계 인증)를 사용하는 경우 계정이 손상될 가능성은 99.9% 더 적습니다. 
 
다음 두 가지 방법을 사용하여 Azure AD 역할에서 MFA를 사용하도록 설정할 수 있습니다.
- Privileged Identity Management의 [역할 설정](../privileged-identity-management/pim-how-to-change-default-settings.md)
- [조건부 액세스](../conditional-access/howto-conditional-access-policy-admin-mfa.md)

## <a name="4-configure-recurring-access-reviews-to-revoke-unneeded-permissions-over-time"></a>4. 시간이 지남에 따라 불필요한 권한을 취소하도록 되풀이 액세스 검토 구성

액세스 검토를 통해 조직은 정기적으로 관리자의 액세스 권한을 검토하여 적합한 사용자만 계속 액세스하도록 할 수 있습니다. 관리자를 정기적으로 감사해야 하는 이유는 다음과 같습니다.
- 악의적인 행위자가 계정을 손상시킬 수 있습니다.
- 사용자가 회사 내에서 팀을 이동합니다. 감사가 없으면 시간이 지남에 따라 불필요한 액세스 권한이 축적될 수 있습니다.
 
역할의 액세스 검토에 관한 정보는 [PIM에서 Azure AD 역할의 액세스 검토 만들기](../privileged-identity-management/pim-how-to-start-security-review.md)를 참조하세요. 역할이 할당된 그룹의 액세스 검토에 관한 정보는 [Azure AD 액세스 검토에서 그룹 및 애플리케이션의 액세스 검토 만들기](../governance/create-access-review.md)를 참조하세요.

## <a name="5-limit-the-number-of-global-administrators-to-less-than-5"></a>5. 전역 관리자 수를 5개 미만으로 제한

모범 사례에 따라 조직 내에서 **5명 미만** 의 사용자에게 전역 관리자 역할을 할당하는 것이 좋습니다. 전역 관리자는 중요한 리소스를 관리하며 공격 표면을 낮게 유지하는 것이 가장 좋습니다. 앞에서 설명한 대로 이러한 계정은 모두 다단계 인증을 사용하여 보호해야 합니다.

기본적으로 사용자가 Microsoft 클라우드 서비스에 등록하면 Azure AD 테넌트가 만들어지고 사용자는 전역 관리자 역할의 멤버가 됩니다. 전역 관리자 역할에 할당된 사용자는 Azure AD 조직의 모든 관리 설정을 읽고 수정할 수 있습니다. 몇 가지를 제외하고 전역 관리자는 Microsoft 365 조직의 모든 구성 설정을 읽고 수정할 수도 있습니다. 전역 관리자는 데이터를 읽을 수 있도록 액세스 권한을 승격할 수도 있습니다.

전역 관리자 역할에 영구적으로 할당되는 두 개의 비상 계정을 유지하는 것이 좋습니다. [Azure AD에서 응급 액세스 계정 관리](../roles/security-emergency-access.md)에서 설명하는 것처럼 이 계정에는 일반 관리 계정과 동일한 다단계 인증 메커니즘이 필요하지 않습니다. 

## <a name="6-use-groups-for-azure-ad-role-assignments-and-delegate-the-role-assignment"></a>6. Azure AD 역할 할당에 그룹을 사용하고 역할 할당 위임

그룹을 활용하는 외부 거버넌스 시스템이 있는 경우 개별 사용자 대신 Azure AD 그룹에 역할을 할당하는 것을 고려해야 합니다. PIM에서 역할 할당 가능 그룹을 관리하여 이 권한 있는 그룹에 지속적인 소유자 또는 멤버가 없는지 확인할 수도 있습니다. 자세한 내용은 [권한 있는 액세스 Azure AD 그룹의 관리 기능](../privileged-identity-management/groups-features.md)을 참조하세요.

역할 할당 가능 그룹에 소유자를 할당할 수 있습니다. 해당 소유자는 그룹에서 추가되거나 제거되는 사용자를 결정하므로 역할 할당을 받는 사용자를 간접적으로 결정합니다. 이 방식으로 전역 관리자 또는 권한 있는 역할 관리자는 그룹을 사용하여 역할별로 역할 관리를 위임할 수 있습니다. 자세한 내용은 [Azure AD 그룹을 사용하여 역할 할당 관리](groups-concept.md)를 참조하세요.

## <a name="7-activate-multiple-roles-at-once-using-privileged-access-groups"></a>7. 권한 있는 액세스 그룹을 사용하여 한 번에 여러 역할 활성화

한 개인에게 PIM을 통해 Azure AD 역할에 대한 5개 또는 6개의 적격 할당이 있는 경우일 수 있습니다. 각 역할을 개별적으로 활성화해야 하므로 생산성이 저하될 수 있습니다. 더욱 심각한 것은 수십 또는 수백 개의 Azure 리소스가 할당될 수 있으므로 문제가 악화됩니다.
 
이 경우 [권한 있는 액세스 그룹](../privileged-identity-management/groups-features.md)을 사용해야 합니다. 권한 있는 액세스 그룹을 만들고 여러 역할(Azure AD 및/또는 Azure)에 영구적 액세스 권한을 부여합니다. 해당 사용자를 이 그룹의 적격 멤버나 소유자로 설정합니다. 하나만 활성화하면 연결된 모든 리소스에 액세스할 수 있습니다.

![한 번에 여러 역할을 활성화하는 작업을 보여 주는 권한 있는 액세스 그룹 다이어그램](./media/best-practices/privileged-access-group.png)

## <a name="8-use-cloud-native-accounts-for-azure-ad-roles"></a>8. Azure AD 역할에 클라우드 네이티브 계정 사용

Azure AD 역할 할당에 대해 온-프레미스 동기화된 계정을 사용하지 마세요. 온-프레미스 계정이 손상된 경우 Azure AD 리소스도 손상시킬 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [Azure AD에서 하이브리드 및 클라우드 배포를 위한 권한 있는 액세스 보안](security-planning.md)
