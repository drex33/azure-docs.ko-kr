---
title: 동적 그룹 및 B2B Collaboration - Azure Active Directory | Microsoft Docs
description: Azure Active Directory B2B 협업 기능에서 Azure AD 동적 그룹을 사용하는 방법을 보여줍니다.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 07/13/2021
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6638e06a8bd7020db993db024d76a566b878ca23
ms.sourcegitcommit: ee8ce2c752d45968a822acc0866ff8111d0d4c7f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/14/2021
ms.locfileid: "113730409"
---
# <a name="dynamic-groups-and-azure-active-directory-b2b-collaboration"></a>동적 그룹 및 Azure Active Directory B2B 협업

## <a name="what-are-dynamic-groups"></a>동적 그룹이란?
Azure AD(Azure Active Directory)에 대한 보안 그룹 구성원의 동적 구성은 [Azure Portal](https://portal.azure.com)에서 사용할 수 있습니다. 관리자는 사용자 특성(예: userType, 부서 또는 국가/지역)에 따라 Azure AD에서 생성된 그룹을 채우도록 규칙을 설정할 수 있습니다. 특성에 따라 구성원을 보안 그룹에 자동으로 추가하거나 보안 그룹에서 제거할 수 있습니다. 이러한 그룹은 SharePoint 사이트, 문서 등의 애플리케이션 또는 클라우드 리소스에 대한 액세스 권한을 제공하고 구성원에게 라이선스를 할당할 수 있습니다. [Azure Active Directory의 전용 그룹](../fundamentals/active-directory-groups-create-azure-portal.md)에서 동적 그룹에 대해 자세히 알아보세요.

동적 그룹을 만들고 사용하려면 적절한 [Azure AD Premium P1 또는 P2 라이선스](https://www.microsoft.com/security/business/identity-access-management/azure-ad-pricing)가 필요합니다. 자세한 내용은 [Azure Active Directory에서 동적 그룹 멤버 자격에 대한 특성 기반 규칙 만들기](../enterprise-users/groups-dynamic-membership.md) 문서를 참조하세요.

## <a name="creating-an-all-users-dynamic-group"></a>"모든 사용자" 동적 그룹 만들기
멤버 자격 규칙을 사용하여 테넌트 내의 모든 사용자가 포함된 그룹을 만들 수 있습니다. 나중에 테넌트에서 사용자를 추가하거나 제거하면 그룹의 멤버 자격이 자동으로 조정됩니다.

1. 테넌트에서 전역 관리자 또는 사용자 관리자 역할이 할당된 계정으로 [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. **Azure Active Directory** 를 선택합니다.
2. **관리** 에서 **그룹** 을 선택한 다음 **새 그룹** 을 선택합니다.
1. **새 그룹** 페이지의 **그룹 유형** 에서 **보안** 을 선택합니다. 새 그룹의 **그룹 이름** 및 **그룹 설명** 을 입력합니다. 
2. **멤버 자격 유형** 에서 **동적 사용자** 를 선택한 후 **동적 쿼리 추가** 를 선택합니다. 
4. **규칙 구문** 텍스트 상자 위에 있는 **편집** 을 선택합니다. **규칙 구문 편집** 페이지의 텍스트 상자에 다음 식을 입력합니다.

   ```
   user.objectId -ne null
   ```
1. **확인** 을 선택합니다. 규칙이 규칙 구문 상자에 나타납니다.

   ![모든 사용자 동적 그룹에 대한 규칙 구문](media/use-dynamic-groups/all-user-rule-syntax.png)

1.  **저장** 을 선택합니다. 새 동적 그룹에는 이제 구성원 사용자뿐만 아니라 B2B 게스트 사용자도 포함됩니다.


1. **새 그룹** 페이지에서 **만들기** 를 선택하여 그룹을 만듭니다.

## <a name="creating-a-group-of-members-only"></a>구성원 그룹만 만들기

그룹에서 게스트 사용자를 제외하고 테넌트의 구성원만 포함하도록 하려면 위에서 설명한 대로 동적 그룹을 생성하고 **규칙 구문** 상자에 다음 식을 입력합니다.

```
(user.objectId -ne null) and (user.userType -eq "Member")
```

다음 이미지는 구성원만 포함하고 게스트를 제외하도록 수정된 동적 그룹의 규칙 구문을 보여 줍니다.

![사용자 유형이 구성원과 같은 규칙 표시](media/use-dynamic-groups/all-member-user-rule-syntax.png)

## <a name="creating-a-group-of-guests-only"></a>게스트 그룹만 만들기

정책(예: Azure AD 조건부 액세스 정책)을 적용할 수 있도록 게스트 사용자만 포함된 새 동적 그룹을 만드는 것이 유용할 수도 있습니다. 위에서 설명한 대로 동적 그룹을 만들고 **규칙 구문** 상자에 다음 식을 입력합니다.

```
(user.objectId -ne null) and (user.userType -eq "Guest")
```

다음 이미지는 게스트만 포함하고 구성원 사용자를 제외하도록 수정된 동적 그룹의 규칙 구문을 보여 줍니다.

![사용자 형식이 게스트와 같은 규칙 표시](media/use-dynamic-groups/all-guest-user-rule-syntax.png)

## <a name="next-steps"></a>다음 단계

- [B2B 협업 사용자 속성](user-properties.md)
- [역할에 B2B 협업 사용자 추가](./add-users-administrator.md)
- [B2B Collaboration 사용자에 대한 조건부 액세스](conditional-access.md)