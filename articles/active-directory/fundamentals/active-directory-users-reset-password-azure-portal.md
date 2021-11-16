---
title: 사용자의 암호 다시 설정 - Azure Active Directory | Microsoft Docs
description: Azure Active Directory를 사용하여 사용자의 암호를 다시 설정하는 방법에 대한 지침입니다.
services: active-directory
author: ajburnle
manager: daveba
ms.assetid: fad5624b-2f13-4abc-b3d4-b347903a8f16
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: how-to
ms.date: 09/05/2018
ms.author: ajburnle
ms.reviewer: jeffsta
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7efa458ebcae7e837be4bb574f4a2707e9cb9e30
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124732615"
---
# <a name="reset-a-users-password-using-azure-active-directory"></a>Azure Active Directory를 사용하여 사용자의 암호 다시 설정

암호를 잊어버린 경우, 사용자가 디바이스로부터 잠긴 경우 또는 사용자가 암호를 받지 못한 경우에는 관리자가 사용자의 암호를 다시 설정할 수 있습니다.

>[!Note]
>Azure AD 테넌트가 사용자의 홈 디렉터리가 아니면 해당 암호를 다시 설정할 수 없습니다. 즉, 사용자가 다른 조직의 계정, Microsoft 계정 또는 Google 계정을 사용하여 조직에 로그인하는 경우 해당 암호를 다시 설정할 수 없습니다.<br><br>사용자에게 Windows Server Active Directory인 인증 원본이 있는 경우 비밀번호 쓰기 저장을 설정할 때에만 암호를 다시 설정할 수 있습니다.<br><br>사용자에게 외부 Azure AD인 인증 원본이 있는 경우 암호를 다시 설정할 수 없습니다. 사용자 또는 외부 Azure AD의 관리자만 암호를 다시 설정할 수 있습니다.

>[!Note]
>관리자가 아니라서 자신의 회사 또는 학교 암호를 다시 설정하는 방법에 대한 지침을 찾는 경우에는 [회사 또는 학교 암호 다시 설정](https://support.microsoft.com/account-billing/reset-your-work-or-school-password-using-security-info-23dde81f-08bb-4776-ba72-e6b72b9dda9e)을 참조하세요.

## <a name="to-reset-a-password"></a>암호를 다시 설정하려면

1. 사용자 관리자 또는 암호 관리자로 [Azure Portal](https://portal.azure.com/)에 로그인합니다. 사용 가능한 역할에 대한 자세한 내용은 [Azure AD 기본 제공 역할](../roles/permissions-reference.md)을 참조하세요.

2. **Azure Active Directory** 를 선택하고, **사용자** 를 선택하고, 다시 설정할 사용자를 검색하여 선택한 다음, **암호 다시 설정** 을 선택합니다.

    **암호 다시 설정** 옵션이 표시된 **Alain Charon - 프로필** 페이지가 나타납니다.

    ![암호 다시 설정 옵션이 강조 표시된 사용자의 프로필 페이지](media/active-directory-users-reset-password-azure-portal/user-profile-reset-password-link.png)

3. **암호 다시 설정** 페이지에서 **암호 다시 설정** 을 선택합니다.

    > [!Note]
    > Azure Active Directory를 사용하는 경우 사용자에 대해 임시 암호가 자동으로 생성됩니다. Active Directory 온-프레미스를 사용하는 경우 사용자의 암호를 만듭니다.

4. 암호를 복사하고 사용자에게 제공합니다. 사용자는 다음 로그인 프로세스 중에 암호를 변경해야 합니다.

    >[!Note]
    >임시 암호는 만료되지 않습니다. 임시 암호가 생성된 이후 얼마나 시간이 경과했는지와 상관없이 다음에 사용자가 로그인할 때에도 암호는 계속 작동합니다.

> [!IMPORTANT]
> 관리자가 사용자의 암호를 재설정할 수 없고 Azure AD Connect 서버의 애플리케이션 이벤트 로그에 hr=80231367 오류 코드가 표시되면 Active Directory에서 사용자의 특성을 검토합니다.  **AdminCount** 특성을 1로 설정하면 관리자가 사용자의 암호를 다시 설정하지 못합니다.  관리자가 사용자의 암호를 다시 설정하려면 **AdminCount** 특성을 0으로 설정해야 합니다.


## <a name="next-steps"></a>다음 단계

사용자 암호를 다시 설정하면 다음 기본 프로세스를 수행할 수 있습니다.

- [사용자 추가 또는 삭제](add-users-azure-active-directory.md)

- [사용자에게 역할 할당](active-directory-users-assign-role-azure-portal.md)

- [프로필 정보 추가 또는 변경](active-directory-users-profile-azure-portal.md)

- [기본 그룹 만들기 및 멤버 추가](active-directory-groups-create-azure-portal.md)

또는 위임 할당, 정책 사용 및 사용자 계정 공유와 같은 복잡한 추가 사용자 시나리오를 수행할 수 있습니다. 사용 가능한 다른 작업에 대한 자세한 내용은 [Azure Active Directory 사용자 관리 설명서](../enterprise-users/index.yml)를 참조하세요.