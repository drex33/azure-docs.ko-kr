---
title: Azure AD 권한 관리에서 액세스 패키지에 대한 요청 설정 변경 - Azure Active Directory
description: Azure Active Directory 권한 관리에서 액세스 패키지에 대한 요청 설정을 변경하는 방법에 대해 알아봅니다.
services: active-directory
documentationCenter: ''
author: ajburnle
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 07/01/2021
ms.author: ajburnle
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4ea285c078061ed6009aff8dfd1c17b1f0528b9a
ms.sourcegitcommit: 47fac4a88c6e23fb2aee8ebb093f15d8b19819ad
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2021
ms.locfileid: "122967252"
---
# <a name="change-request-settings-for-an-access-package-in-azure-ad-entitlement-management"></a>Azure AD 권한 관리에서 액세스 패키지에 요청 설정 변경

액세스 패키지 관리자 자격으로 언제든지 정책을 편집하거나 새 정책을 추가하여 액세스 패키지를 요청할 수 있는 사용자를 변경할 수 있습니다. 이 문서에서는 기존 액세스 패키지에 대한 요청 설정을 변경하는 방법을 설명합니다.

## <a name="choose-between-one-or-multiple-policies"></a>하나 또는 여러 정책 중에서 선택

정책을 사용해서 액세스 패키지를 요청할 수 있는 사용자를 지정할 수 있습니다. 액세스 패키지에서 새 정책을 만들거나 기존 정책을 편집하기 전에 액세스 패키지에 필요한 정책 수를 결정해야 합니다. 

액세스 패키지를 만들 때 정책을 만드는 요청 설정을 지정합니다. 대부분의 액세스 패키지에는 단일 정책이 포함되어 있지만 단일 액세스 패키지는 여러 정책이 포함될 수 있습니다. 서로 다른 요청 및 승인 설정을 사용하여 다른 사용자 집합에 할당을 부여할 수 있도록 허용하려면 액세스 패키지에 대한 정책을 여러 개 만듭니다. 

예를 들어 동일한 액세스 패키지에 내부 및 외부 사용자를 할당하는 데는 단일 정책을 사용할 수 없습니다. 그러나 동일한 액세스 패키지에 두 개의 정책을 만들 수 있습니다. 하나는 내부 사용자용이고 다른 하나는 외부 사용자용입니다. 사용자에게 적용되는 정책이 여러 개 있는 경우 요청 시 사용자에게 할당하려는 정책을 선택하라는 메시지가 표시됩니다. 다음 다이어그램에서는 두 개의 정책을 포함하는 액세스 패키지를 보여 줍니다.

![액세스 패키지의 여러 정책](./media/entitlement-management-access-package-request-policy/access-package-policy.png)

### <a name="how-many-policies-will-i-need"></a>필요한 정책 수는 몇 개인가요?

| 시나리오 | 정책 수 |
| --- | --- |
| 내 디렉터리의 모든 사용자에게 액세스 패키지에 대한 동일한 요청 및 승인 설정을 포함하려고 합니다 | 하나 |
| 연결된 특정 조직의 모든 사용자가 액세스 패키지를 요청할 수 있도록 하려고 합니다 | 하나 |
| 내 디렉터리 내부의 사용자와 내 디렉터리 외부의 사용자 모두가 액세스 패키지를 요청하도록 허용합니다 | 여러 접두사 |
| 일부 사용자에 다른 승인 설정을 지정하려고 합니다 | 여러 접두사 |
| 일부 사용자가 패키지 할당에 액세스하여 다른 사용자가 액세스를 확장할 수 있도록 하려고 합니다 | 여러 접두사 |

여러 정책이 적용될 때 사용되는 우선 순위 논리에 대한 자세한 내용은 [여러 정책](entitlement-management-troubleshoot.md#multiple-policies
)을 참조하세요.

## <a name="open-an-existing-access-package-and-add-a-new-policy-of-request-settings"></a>기존 액세스 패키지를 열고 요청 설정의 새 정책 추가

다른 요청 및 승인 설정이 필요한 사용자 집합이 있는 경우 새 정책을 만들어야 할 수 있습니다. 기존 액세스 패키지에 새 정책 추가를 시작하려면 다음 단계를 수행합니다.

**필수 역할:** 전역 관리자, Identity Governance 관리자, 사용자 관리자, 카탈로그 소유자, 액세스 패키지 관리자

1. Azure Portal에서 **Azure Active Directory** 를 클릭한 다음, **Identity Governance** 를 클릭합니다.

1. 왼쪽 메뉴에서 **액세스 패키지** 를 클릭한 다음 액세스 패키지를 엽니다.

1. **정책** 을 클릭한 다음 **정책 추가** 를 클릭합니다.

1. **기본** 탭에서 시작합니다. 정책의 이름 및 설명을 입력합니다.

    ![이름 및 설명을 사용하여 정책 만들기](./media/entitlement-management-access-package-request-policy/policy-name-description.png)

1. **다음** 을 클릭하여 **요청** 탭을 엽니다.

1. **액세스를 요청할 수 있는 사용자** 를 변경합니다. 다음 섹션의 단계를 통해 설정을 다음 옵션 중 하나로 변경할 수 있습니다. 
    - [디렉터리에 있는 사용자](#for-users-in-your-directory) 
    - [디렉터리에 없는 사용자](#for-users-not-in-your-directory)
    - [없음(관리자 직접 할당만 해당)](#none-administrator-direct-assignments-only)

## <a name="for-users-in-your-directory"></a>디렉터리에 있는 사용자

디렉터리에 있는 사용자가 이 액세스 패키지를 요청할 수 있도록 하려면 다음 단계를 수행합니다. 요청 정책을 정의할 때 개별 사용자 또는 더 일반적으로 사용자 그룹을 지정할 수 있습니다. 예를 들어 조직에 **모든 직원** 과 같은 그룹이 이미 있을 수 있습니다.  해당 그룹이 액세스를 요청할 수 있는 사용자에 대한 정책에 추가되면 해당 그룹의 모든 구성원이 액세스를 요청할 수 있습니다.

1. **Users who can request access**(액세스를 요청할 수 있는 사용자) 섹션에서 **For users in your directory**(디렉터리에 있는 사용자의 경우) 클릭합니다.

    이 옵션을 선택하면 디렉터리에서 이 액세스 패키지를 요청할 수 있는 사용자를 더 구체화하는 새로운 옵션이 표시됩니다.

    ![액세스 패키지 - 요청 - 디렉터리의 사용자](./media/entitlement-management-access-package-request-policy/for-users-in-your-directory.png)

1. 다음 옵션 중 하나를 선택합니다.

    |  | Description |
    | --- | --- |
    | **특정 사용자 및 그룹** | 지정한 디렉터리의 사용자 또는 그룹만 이 액세스 패키지를 요청할 수 있도록 하려면 이 옵션을 선택합니다. |
    | **모든 구성원(게스트 제외)** | 디렉터리의 모든 구성원 사용자가 이 액세스 패키지를 요청할 수 있도록 하려면 이 옵션을 선택합니다. 이 옵션에는 디렉터리에 초대했을 수 있는 게스트 사용자가 포함되지 않습니다. |
    | **모든 사용자(게스트 포함)** | 디렉터리의 모든 구성원 사용자와 게스트 사용자가 이 액세스 패키지를 요청할 수 있도록 하려면 이 옵션을 선택합니다. |

    게스트 사용자는 [Azure AD B2B](../external-identities/what-is-b2b.md)를 사용하여 디렉터리에 초대된 외부 사용자를 나타냅니다. 구성원 사용자와 게스트 사용자의 차이점에 대한 자세한 내용은 [Azure Active Directory의 기본 사용자 권한이란?](../fundamentals/users-default-permissions.md)을 참조하세요.

1. **특정 사용자 및 그룹** 을 선택한 경우 **사용자 및 그룹 추가** 를 클릭합니다.

1. [사용자 및 그룹 선택] 창에서 추가하려는 사용자와 그룹을 선택합니다.

    ![액세스 패키지 - 요청 - 사용자 및 그룹 선택](./media/entitlement-management-access-package-request-policy/select-users-groups.png)

1. **선택** 을 클릭하여 사용자와 그룹을 추가합니다.

1. 승인이 필요한 경우 [Azure AD 권한 관리에서 액세스 패키지에 대한 승인 설정 변경](entitlement-management-access-package-approval-policy.md) 단계를 통해 승인 설정을 구성합니다.

1. [요청 사용 ](#enable-requests) 섹션으로 이동합니다.
 
## <a name="for-users-not-in-your-directory"></a>디렉터리에 없는 사용자

 **디렉터리에 없는 사용자** 는 다른 Azure AD 디렉터리 또는 도메인에 있는 사용자를 나타냅니다. 이러한 사용자는 아직 디렉터리에 초대되지 않았을 수 있습니다. **협업 제한 사항** 에서 초대를 허용하도록 Azure AD 디렉터리를 구성해야 합니다. 자세한 내용은 [B2B 외부 협업을 사용하도록 설정 및 게스트를 초대할 수 있는 사용자 관리](../external-identities/delegate-invitations.md)를 참조하세요.

> [!NOTE]
> 요청이 승인되거나 자동으로 승인되는 게스트 사용자 계정은 디렉터리에 아직 없는 사용자에 대해 만들어집니다. 게스트는 초대되지만 초대 메일은 받지 않습니다. 대신 액세스 패키지 할당이 전달되면 이메일을 받게 됩니다. 나중에 해당 게스트 사용자에게 액세스 패키지 할당이 더 이상 없는 경우 마지막 할당이 만료되었거나 취소 되었으므로 게스트 사용자 계정은 기본적으로 로그인이 차단되며 이후에 삭제됩니다. 게스트 사용자에게 패키지 할당이 없는 경우에도 해당 게스트 사용자가 무기한으로 디렉터리에 남아 있게 하려면 권한 관리 구성 설정을 변경할 수 있습니다. 게스트 사용자 개체에 대한 자세한 내용은 [Azure Active Directory B2B 협업 사용자 속성](../external-identities/user-properties.md)을 참조하세요.

디렉터리에 없는 사용자가 이 액세스 패키지를 요청할 수 있도록 하려면 다음 단계를 수행합니다.

1. **액세스를 요청할 수 있는 사용자** 섹션에서 **디렉터리에 없는 사용자** 를 클릭합니다.

    이 옵션을 선택하면 새 옵션이 표시됩니다.

    ![액세스 패키지 - 요청 - 디렉터리에 없는 사용자](./media/entitlement-management-access-package-request-policy/for-users-not-in-your-directory.png)

1. 다음 옵션 중 하나를 선택합니다.

    |  | Description |
    | --- | --- |
    | **연결된 특정 조직** | 관리자가 이전에 추가한 조직 목록에서 선택하려면 이 옵션을 선택합니다. 선택한 조직의 모든 사용자가 이 액세스 패키지를 요청할 수 있습니다. |
    | **구성된 모든 연결 조직** | 구성된 모든 연결된 조직의 모든 사용자가 이 액세스 패키지를 요청할 수 있는 경우 이 옵션을 선택합니다. 구성된 연결된 조직의 사용자만 구성된 모든 조직의 사용자에게 표시되는 액세스 패키지를 요청할 수 있습니다. |
    | **모든 사용자(모든 연결된 조직 + 모든 새 외부 사용자)** | 인터넷의 모든 사용자가 이 액세스 패키지를 요청할 수 있어야 하는 경우 이 옵션을 선택합니다.  사용자가 디렉터리의 연결된 조직에 속해 있지 않으면 패키지를 요청할 때 연결된 조직이 자동으로 생성됩니다. 자동으로 생성된 연결된 조직은 **제안된** 상태가 됩니다. 제안된 상태에 대한 자세한 내용은 [연결된 조직의 상태 속성](entitlement-management-organization.md#state-properties-of-connected-organizations)을 참조하세요. |

    연결된 조직은 관계가 있는 외부 Azure AD 디렉터리 또는 도메인입니다.

1. **연결된 특정 조직** 을 선택한 경우 **디렉터리 추가** 를 클릭하여 관리자가 이전에 추가한 연결된 조직 목록에서 선택합니다.

1. 이전에 연결된 조직을 검색하려면 이름 또는 도메인 이름을 입력합니다.

    ![액세스 패키지 - 요청 - 디렉터리 선택](./media/entitlement-management-access-package-request-policy/select-directories.png)

    협업하려는 조직이 목록에 없는 경우 관리자에게 연결된 조직으로 추가하도록 요청할 수 있습니다. 자세한 내용은 [연결된 조직 추가](entitlement-management-organization.md)를 참조하세요.

1. 연결된 조직을 모두 선택했으면 **선택** 을 클릭합니다.

    > [!NOTE]
    > 선택한 연결된 조직의 모든 사용자가 이 액세스 패키지를 요청할 수 있습니다. 여기에는 Azure B2B 허용 또는 차단 목록에서 차단하는 경우를 제외하고는 조직과 연결된 모든 하위 도메인의 Azure AD 사용자가 포함됩니다. 자세한 내용은 [특정 조직의 B2B 사용자 초대 허용 또는 차단](../external-identities/allow-deny-list.md)을 참조하세요.

1. 승인이 필요한 경우 [Azure AD 권한 관리에서 액세스 패키지에 대한 승인 설정 변경](entitlement-management-access-package-approval-policy.md) 단계를 통해 승인 설정을 구성합니다.
 
1. [요청 사용 ](#enable-requests) 섹션으로 이동합니다.

## <a name="none-administrator-direct-assignments-only"></a>없음(관리자 직접 할당만 해당)

액세스 요청을 무시하고 관리자가 특정 사용자를 이 액세스 패키지에 직접 할당할 수 있게 하려면 다음 단계를 수행합니다. 사용자는 액세스 패키지를 요청할 필요가 없습니다. 수명 주기 설정은 계속 지정할 수 있지만 요청 설정이 없습니다.

1. **액세스를 요청할 수 있는 사용자** 섹션에서 **없음(관리자 직접 할당만 해당)** 을 클릭합니다.

    ![액세스 패키지 - 요청 - 없음(관리자 직접 할당만 해당)](./media/entitlement-management-access-package-request-policy/none-admin-direct-assignments-only.png)

    액세스 패키지가 만들어지면 특정 내부 및 외부 사용자를 액세스 패키지에 직접 할당할 수 있습니다. 외부 사용자를 지정하는 경우 게스트 사용자 계정이 디렉터리에 만들어집니다. 사용자를 직접 할당하는 방법에 대한 자세한 내용은 [액세스 패키지에 대한 할당 보기, 추가 및 제거](entitlement-management-access-package-assignments.md)를 참조하세요.

1. [요청 사용 ](#enable-requests) 섹션으로 건너뜁니다.

> [!NOTE]
> 사용자를 액세스 패키지에 할당하는 경우 관리자는 기존 정책 요구 사항에 따라 사용자가 해당 액세스 패키지에 적합한지 확인해야 합니다. 그렇지 않으면 사용자가 액세스 패키지에 성공적으로 할당되지 않습니다. 액세스 패키지가 사용자 요청 승인을 요구하는 정책을 포함하는 경우 사용자는 지정된 승인자로부터 필요한 승인 없이 패키지에 직접 할당될 수 없습니다.


## <a name="open-and-edit-an-existing-policy-of-request-settings"></a>요청 설정의 기존 정책 열기 및 편집

액세스 패키지에 대한 요청 및 승인 설정을 변경하려면 해당하는 정책을 열어야 합니다. 다음 단계를 수행하여 액세스 패키지에 대한 요청 설정을 열고 편집합니다.

**필수 역할:** 글로벌 관리자, 사용자 관리자, 카탈로그 소유자 또는 액세스 패키지 관리자

1. Azure Portal에서 **Azure Active Directory** 를 클릭한 다음, **Identity Governance** 를 클릭합니다.

1. 왼쪽 메뉴에서 **액세스 패키지** 를 클릭한 다음 액세스 패키지를 엽니다.

1. **정책** 을 클릭한 다음 편집하려는 정책을 클릭합니다.

    정책 세부 정보 창이 페이지 아래쪽에 열립니다.

    ![액세스 패키지 - 정책 세부 정보 창](./media/entitlement-management-shared/policy-details.png)

1. 정책을 편집하려면 **편집** 을 클릭합니다.

    ![액세스 패키지 - 정책 편집](./media/entitlement-management-shared/policy-edit.png)

1. **요청** 탭을 클릭하여 요청 설정을 엽니다.

1. 필요에 따라 요청 설정을 변경하려면 이전 섹션의 단계를 사용합니다.

1. [요청 사용 ](#enable-requests) 섹션으로 이동합니다.

## <a name="enable-requests"></a>요청 사용

1. 요청 정책의 사용자가 액세스 패키지를 즉시 사용할 수 있게 하려면 [사용] 토글을 **예** 로 이동합니다.

    액세스 패키지 만들기가 완료되면 나중에 언제든지 사용할 수 있습니다.

    **없음(관리자 직접 할당에만 해당)** 을 선택하고 사용을 **아니요** 로 설정하면 관리자가 이 액세스 패키지를 직접 할당할 수 없습니다.

    ![액세스 패키지 - 정책 - 정책 설정 사용](./media/entitlement-management-access-package-approval-policy/enable-requests.png)

1. **다음** 을 클릭합니다.

1. 액세스 패키지에 대한 액세스를 요청할 때 요청자에게 추가 정보를 제공하도록 요구하려면 [Azure AD 권한 관리에서 액세스 패키지에 대한 승인 및 요청자 정보 설정 변경](entitlement-management-access-package-approval-policy.md#collect-additional-requestor-information-for-approval)의 단계를 통해 요청자 정보를 구성합니다.

1. 수명 주기 설정을 구성합니다.

1. 정책을 편집하는 경우 **업데이트** 를 클릭합니다. 새 정책을 추가하는 경우 **만들기** 를 클릭합니다.

## <a name="prevent-requests-from-users-with-incompatible-access-preview"></a>호환되지 않는 액세스 권한이 있는 사용자의 요청 방지(미리 보기)

요청할 수 있는 사용자에 대한 정책 확인 외에도, 그룹이나 또 다른 액세스 패키지를 통해 일부 액세스 권한이 이미 있는 사용자가 과도한 액세스 권한을 얻지 못하도록 액세스를 추가로 제한할 수 있습니다.

사용자가 액세스 패키지를 요청할 수 없도록 구성하려는 경우 사용자에게 또 다른 액세스 패키지에 대한 할당이 이미 있거나 사용자가 그룹의 멤버이면 [액세스 패키지에 대한 의무 분리 검사 구성](entitlement-management-access-package-incompatible.md)의 단계를 사용합니다.

## <a name="next-steps"></a>다음 단계

- [액세스 패키지의 승인 설정 변경](entitlement-management-access-package-approval-policy.md)
- [액세스 패키지의 수명 주기 설정 변경](entitlement-management-access-package-lifecycle-policy.md)
- [액세스 패키지의 요청 보기](entitlement-management-access-package-requests.md)
