---
title: 사용자 프로필 정보 추가 또는 업데이트 - Azure AD
description: 사진 및 작업 세부 정보를 포함하여 Azure Active Directory에서 사용자의 프로필에 정보를 추가하는 방법에 대한 지침입니다.
services: active-directory
author: ajburnle
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: how-to
ms.date: 06/10/2021
ms.author: ajburnle
ms.reviewer: jeffsta
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6239b0c7f56db49d54ee95b1d6fb0737d2ea9cb8
ms.sourcegitcommit: 942a1c6df387438acbeb6d8ca50a831847ecc6dc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/11/2021
ms.locfileid: "112017987"
---
# <a name="add-or-update-a-users-profile-information-using-azure-active-directory"></a>Azure Active Directory를 사용하여 사용자 프로필 정보 추가 또는 업데이트
프로필 사진, 작업 관련 정보 및 Azure AD(Azure Active Directory)를 사용하는 일부 설정을 비롯하여 사용자 프로필 정보를 추가합니다. 새 사용자를 추가하는 방법에 대한 자세한 내용은 [Azure Active Directory에서 사용자를 추가 또는 삭제하는 방법](add-users-azure-active-directory.md)을 참조하세요.

## <a name="add-or-change-profile-information"></a>프로필 정보 추가 또는 변경
앞으로 살펴보겠지만 사용자를 만드는 동안 추가할 수 있는 것보다 많은 정보를 사용자 프로필에서 사용할 수 있습니다. 이 추가 정보는 모두 선택 사항이며 조직에서 필요에 따라 추가할 수 있습니다.

## <a name="to-add-or-change-profile-information"></a>프로필 정보를 추가 또는 변경하려면

>[!Note]
>사용자 이름 및 메일 주소 속성은 악센트 문자를 포함할 수 없습니다.

1. 조직의 사용자 관리자 역할로 [Azure Portal](https://portal.azure.com/)에 로그인합니다.

2. **Azure Active Directory** 를 선택하고, **사용자** 를 선택한 다음, 사용자를 선택합니다. 예를 들어 _Alain Charon_ 입니다.

    **Alain Charon - 프로필** 페이지가 표시됩니다.

    ![편집 가능한 정보가 포함된 사용자 프로필 페이지](media/active-directory-users-profile-azure-portal/user-profile-all-blade.png)

3. **편집** 을 선택하여 편집 가능한 각 섹션에 포함된 정보를 선택적으로 추가하거나 업데이트합니다.

    - **프로필 사진.** 사용자 계정의 썸네일 이미지를 선택합니다. 이 사진은 Azure Active Directory 및 사용자의 개인 페이지(예: myapps.microsoft.com 페이지)에 나타납니다.

    - **ID.** 사용자에 대한 추가 ID 값(예: 결혼 후 얻은 성)을 추가 또는 업데이트합니다. 이 이름은 이름 및 성의 값과 별개로 설정할 수 있습니다. 예를 들어 이니셜, 회사 이름을 포함하거나 표시된 이름의 시퀀스를 변경하는 데 사용할 수 있습니다. 또 다른 예로, 이름이 ‘Chris Green’인 사용자가 2명 있는 경우 ID 문자열을 사용하여 해당 사용자의 이름을 ‘Chris B. Green’ ‘Chris R. Green (Contoso)’으로 설정할 수 있습니다.

    - **작업 정보.** 사용자의 직책, 부서 또는 관리자와 같은 작업 관련 정보를 추가합니다.

    - **설정.** 사용자가 Azure Active Directory 테넌트에 로그인할 수 있는지 여부를 결정합니다. 사용자의 전역 위치를 지정할 수도 있습니다.

    - **연락처 정보.** 일부 사용자의 전화 또는 모바일 연락처 정보를 제외하고 사용자의 관련 연락처 정보를 추가합니다(전역 관리자만 관리자 역할의 사용자의 정보를 업데이트할 수 있음).

    - **인증 연락처 정보.** 이 정보를 확인하여 사용자의 활성 전화 번호와 메일 주소가 있는지 확인합니다. 이 정보는 Azure Active Directory에서 로그인 중인 사용자가 실제로 사용자인지 확인하는 데 사용됩니다. 인증 연락처 정보는 전역 관리자만 업데이트할 수 있습니다.

4. **저장** 을 선택합니다.

    사용자에 대한 모든 변경 내용이 저장됩니다.

    >[!Note]
    >Windows Server Active Directory를 사용하여 인증 원본이 Windows Server Active Directory인 사용자의 ID, 연락처 정보 또는 작업 정보를 업데이트해야 합니다. 업데이트를 완료하면 변경 내용이 표시되기 전에 다음 동기화 주기가 완료될 때까지 기다려야 합니다.

## <a name="next-steps"></a>다음 단계
사용자 프로필을 업데이트한 후 다음 기본 프로세스를 수행할 수 있습니다.

- [사용자 추가 또는 삭제](add-users-azure-active-directory.md)

- [사용자에게 역할 할당](active-directory-users-assign-role-azure-portal.md)

- [기본 그룹 만들기 및 멤버 추가](active-directory-groups-create-azure-portal.md)

또는 위임 할당, 정책 사용 및 사용자 계정 공유와 같은 다른 사용자 관리 작업을 수행할 수 있습니다. 사용 가능한 다른 작업에 대한 자세한 내용은 [Azure Active Directory 사용자 관리 설명서](../enterprise-users/index.yml)를 참조하세요.
