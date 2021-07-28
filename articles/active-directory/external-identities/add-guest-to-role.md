---
title: Azure Active Directory B2B 협업 사용자를 역할에 추가
description: Azure Active Directory의 역할에 게스트 사용자 추가
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 05/08/2018
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 455d0ee4855645104dda662785794225f5a23d2d
ms.sourcegitcommit: 38d81c4afd3fec0c56cc9c032ae5169e500f345d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/07/2021
ms.locfileid: "109520756"
---
# <a name="grant-permissions-to-users-from-partner-organizations-in-your-azure-active-directory-tenant"></a>Azure Active Directory 테넌트에서 파트너 조직의 사용자에게 권한 부여

Azure AD(Azure Active Directory) B2B 협업 사용자가 디렉터리에 게스트 사용자로 추가되고 디렉터리의 게스트 사용 권한이 기본적으로 제한됩니다. 기업은 조직의 추가 권한 역할을 채우기 위해 게스트 사용자가 필요할 수 있습니다. 높은 권한 역할 정의를 지원하기 위해 조직의 요구에 따라 원하는 역할에 게스트 사용자를 추가할 수 있습니다.

디렉터리 역할이 게스트 사용자에게 할당된 경우 게스트 사용자에게 기본 읽기 권한을 포함하여 역할과 함께 제공되는 추가 권한이 부여됩니다. [Azure AD 기본 제공 역할](../roles/permissions-reference.md)을 참조하세요.

## <a name="default-role"></a>기본 역할

![기본 디렉터리 역할을 보여주는 스크린샷](./media/add-guest-to-role/default-role.png)

## <a name="global-administrator-role"></a>전역 관리자 역할

![전역 관리자 역할을 보여주는 스크린샷](./media/add-guest-to-role/global-admin-role.png)

## <a name="limited-administrator-role"></a>제한된 관리자 역할

![제한된 관리자 역할을 보여주는 스크린샷](./media/add-guest-to-role/limited-admin-role.png)

## <a name="next-steps"></a>다음 단계

- [Azure AD B2B 협업이란?](what-is-b2b.md)
- [B2B 협업 사용자 속성](user-properties.md)