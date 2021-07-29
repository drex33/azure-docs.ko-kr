---
title: 사용자 추가 또는 삭제 - Azure Active Directory | Microsoft Docs
description: Azure Active Directory를 사용하여 새 사용자를 추가하거나 기존 사용자를 삭제하는 방법에 대한 지침입니다.
services: active-directory
author: ajburnle
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: how-to
ms.date: 05/04/2021
ms.author: ajburnle
ms.reviewer: krbain
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 34342111aa0067b4f275820ac0111692c4a590f6
ms.sourcegitcommit: 02d443532c4d2e9e449025908a05fb9c84eba039
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/06/2021
ms.locfileid: "108762572"
---
# <a name="add-or-delete-users-using-azure-active-directory"></a>Azure Active Directory를 사용한 사용자 추가 또는 삭제

Azure AD(Azure Active Directory) 조직에서 새 사용자를 추가하거나 기존 사용자를 삭제합니다. 사용자를 추가하거나 삭제하려면 사용자 관리자 또는 전역 관리자여야 합니다.

## <a name="add-a-new-user"></a>새 사용자 추가

Azure Active Directory 포털을 사용하여 새 사용자를 만들 수 있습니다.

>[!Note]
>사용자 이름 및 이메일 주소 속성은 악센트 문자를 포함할 수 없습니다.

새 사용자를 추가하려면 다음 단계를 수행합니다.

1. 조직의 사용자 관리자 역할로 [Azure Portal](https://portal.azure.com/)에 로그인합니다.

1. 모든 페이지에서 *Azure Active Directory* 를 검색하고 선택합니다.

1. **사용자** 를 선택한 다음, **새 사용자** 를 선택합니다.

    ![사용자를 통해 사용자 추가 - Azure AD의 모든 사용자](media/add-users-azure-active-directory/add-user-in-users-all-users.png)

1. **사용자**  페이지에서 이 사용자에 대한 정보를 입력합니다.

   - **이름**. 필수 요소. 새 사용자의 이름 및 성입니다. 예를 들어, *Mary Parker* 입니다.

   - **사용자 이름**. 필수 요소. 새 사용자의 사용자 이름입니다. 예: `mary@contoso.com`.

     사용자 이름의 도메인 이름 부분은 초기 기본 도메인 이름 *\<yourdomainname>.onmicrosoft.com* 이거나 *contoso.com* 과 같은 사용자 지정 도메인 이름을 사용해야 합니다. 사용자 지정 도메인 이름 작성 방법에 대한 자세한 내용은 [Azure Active Directory 포털을 사용하여 사용자 지정 도메인 이름 추가](add-custom-domain.md)을 참조하세요.

   - **그룹** - 원할 경우, 하나 이상의 기존 그룹에 사용자를 추가할 수 있습니다. 사용자를 나중에 그룹에 추가할 수도 있습니다. 사용자를 그룹에 추가하는 데 대한 자세한 내용은 [Azure Active Directory를 사용하여 기본 그룹 만들기 및 멤버 추가](active-directory-groups-create-azure-portal.md)를 참조하세요.

   - **디렉터리 역할**: 사용자에 대한 Azure AD 관리 권한이 필요한 경우 Azure AD 역할에 추가할 수 있습니다. Azure AD에서 사용자에게 전역 관리자 또는 하나 이상의 제한된 관리자 역할을 할당할 수 있습니다. 역할 할당에 대한 자세한 내용은 [사용자에게 역할을 할당하는 방법](active-directory-users-assign-role-azure-portal.md)을 참조하세요.

   - **작업 정보**: 여기서 사용자에 대한 추가 정보를 추가하거나 나중에 추가할 수 있습니다. 사용자 정보 추가에 대한 자세한 내용은 [사용자 프로필 정보를 추가하거나 변경하는 방법](active-directory-users-profile-azure-portal.md)을 참조하세요.

1. **암호** 상자에 제공된 자동 생성된 암호를 복사합니다. 처음으로 로그인하는 사용자에게 이 암호를 제공해야 합니다.

1. **만들기** 를 선택합니다.

사용자가 만들어지고 Azure AD 조직에 추가됩니다.

## <a name="add-a-new-guest-user"></a>새 게스트 사용자 추가

**새 사용자** 페이지에서 **사용자 초대** 를 선택하여 새 게스트 사용자를 조직과 협업하도록 초대할 수도 있습니다. 조직의 외부 협업 설정이 게스트 초대를 허용하도록 구성된 경우 사용자는 이메일로 협업을 시작하려면 동의해야 하는 초대를 받게 됩니다. B2B 협업 사용자를 초대하는 방법에 대한 자세한 내용은 [Azure Active Directory에 B2B 사용자 초대](../external-identities/add-users-administrator.md)를 참조하세요.

## <a name="add-a-consumer-user"></a>소비자 사용자 추가

Azure Active Directory B2C(Azure AD B2C) 디렉터리에서 수동으로 소비자 계정을 만들고자 하는 시나리오가 있을 수 있습니다. 소비자 계정을 만드는 방법에 대한 자세한 내용은 [Azure AD B2C에서 소비자 사용자 만들기 및 삭제](../../active-directory-b2c/manage-users-portal.md)를 참조하세요.

## <a name="add-a-new-user-within-a-hybrid-environment"></a>하이브리드 환경 내에 새 사용자 추가

Azure Active Directory(클라우드)와 Windows Server Active Directory(온-프레미스)를 모두 사용하는 환경이 있는 경우 기존 사용자 계정 데이터를 동기화하여 새 사용자를 추가할 수 있습니다. 하이브리드 환경에 대한 자세한 내용은 [Azure Active Directory와 온-프레미스 디렉터리 통합](../hybrid/whatis-hybrid-identity.md)을 참조하세요.

## <a name="delete-a-user"></a>사용자 삭제

Azure Active Directory 포털을 사용하여 기존 사용자를 삭제할 수 있습니다.

>[!Note]
>조직에서 사용자를 삭제하려면 전역 관리자 또는 사용자 관리자 역할이 할당되어야 합니다. 전역 관리자는 다른 관리자를 포함하여 모든 사용자를 삭제할 수 있습니다. 사용자 관리자는 관리자가 아닌 사용자, 기술 지원팀 관리자, 기타 사용자 관리자를 삭제할 수 있습니다. 자세한 내용은 [Azure AD의 관리자 역할 권한](../roles/permissions-reference.md)을 참조하세요.

사용자를 삭제하려면 다음 단계를 수행합니다.

1. 조직에 대한 사용자 관리자 계정을 사용하여 [Azure Portal](https://portal.azure.com/)에 로그인합니다.

1. 모든 페이지에서 *Azure Active Directory* 를 검색하고 선택합니다.

1. Azure AD 테넌트에서 삭제할 사용자를 검색하고 선택합니다. 예를 들어, _Mary Parker_ 입니다.

1. **사용자 삭제** 를 선택합니다.

    ![사용자 - 사용자 삭제가 강조 표시된 모든 사용자 페이지](media/add-users-azure-active-directory/delete-user-all-users-blade.png)

사용자가 삭제되고 **사용자 - 모든 사용자** 페이지에 더 이상 표시되지 않습니다. 이 사용자는 다음 30일 동안 **삭제된 사용자** 페이지에서 볼 수 있으며 해당 기간 복원할 수 있습니다. 사용자를 복원하는 방법에 대한 자세한 내용은 [Azure Active Directory를 사용하여 최근에 삭제된 사용자 복원 또는 제거](active-directory-users-restore.md)를 참조하세요.

사용자를 삭제하면 해당 사용자가 사용하는 모든 라이선스를 다른 사용자가 사용할 수 있게 됩니다.

>[!Note]
>인증 원본이 Windows Server Active Directory인 사용자의 ID, 연락처 정보 또는 작업 정보를 업데이트하려면 Windows Server Active Directory를 사용해야 합니다. 업데이트를 완료하면 변경 내용이 표시되기 전에 다음 동기화 주기가 완료될 때까지 기다려야 합니다.

## <a name="next-steps"></a>다음 단계

사용자를 추가한 후 다음 기본 프로세스를 수행할 수 있습니다.

- [프로필 정보 추가 또는 변경](active-directory-users-profile-azure-portal.md)

- [사용자에게 역할 할당](active-directory-users-assign-role-azure-portal.md)

- [기본 그룹 만들기 및 멤버 추가](active-directory-groups-create-azure-portal.md)

- [동적 그룹 및 사용자와 함께 작업](../enterprise-users/groups-create-rule.md)

또는 [adding guest users from another directory](../external-identities/what-is-b2b.md)(다른 디렉터리의 게스트 사용자 추가) 또는 [restoring a deleted user](active-directory-users-restore.md)(삭제된 사용자 복원)와 같은 다른 사용자 관리 작업을 수행할 수 있습니다. 사용 가능한 다른 작업에 대한 자세한 내용은 [Azure Active Directory 사용자 관리 설명서](../enterprise-users/index.yml)를 참조하세요.