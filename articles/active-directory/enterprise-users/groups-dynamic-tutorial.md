---
title: 동적 그룹에 사용자 추가 - 자습서 - Azure AD | Microsoft Docs
description: 이 자습서에서는 사용자 멤버 자격 규칙과 함께 그룹을 사용하여 자동을 사용자를 추가 또는 제거합니다.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: enterprise-users
ms.workload: identity
ms.topic: tutorial
ms.date: 09/02/2021
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro;seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: b3ef7aeea68002cdc3ff19efcc989701fb93714f
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/03/2021
ms.locfileid: "123427357"
---
# <a name="tutorial-add-or-remove-group-members-automatically"></a>자습서: 자동으로 그룹 구성원 추가 또는 제거

Azure AD(Azure Active Directory)에서 자동으로 보안 그룹 또는 Microsoft 365 그룹에 사용자를 추가하거나 제거할 수 있으므로, 항상 수동으로 할 필요가 없습니다. 사용자 또는 디바이스의 속성이 변경될 때마다 Azure AD는 Azure AD 조직의 모든 동적 그룹 규칙을 평가하여 변경 내용에 구성원을 추가 또는 제거해야 하는지 확인합니다.

이 자습서에서는 다음 작업 방법을 알아봅니다.
> [!div class="checklist"]
> * 파트너 회사에서 자동으로 채워지는 게스트 사용자 그룹 만들기
> * 게스트 사용자가 액세스할 파트너 관련 기능에 대한 그룹에 라이선스 할당
> * 보너스: 구성원에게 내부 전용 사이트에 대한 액세스 권한을 부여할 수 있도록 게스트 사용자를 제거하여 **모든 사용자** 를 안전하게 보호

Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.

## <a name="prerequisites"></a>사전 요구 사항

이 기능을 사용하려면 조직의 글로벌 관리자로서 하나의 Azure AD Premium 라이선스가 있어야 합니다. 아직 없는 경우 Azure AD에서 **라이선스** > **제품** > **사용/구매** 를 선택합니다.

사용자에게 라이선스를 할당하지 않아도 사용자가 동적 그룹의 구성원이 됩니다. 이러한 모든 사용자를 수용하려면 조직에서 사용 가능한 최소한의 Azure AD Premium P1 라이선스만 있으면 됩니다. 

## <a name="create-a-group-of-guest-users"></a>게스트 사용자 그룹 만들기

먼저, 모두 단일 파트너 회사 소속인 게스트 사용자 그룹을 만들겠습니다. 이들에게는 특별한 라이선스가 필요하므로, 이 용도에 사용할 그룹을 따로 만드는 것이 종종 더 효율적입니다.

1. 조직의 글로벌 관리자 계정을 사용하여 Azure Portal(https://portal.azure.com) )에 로그인합니다.
2. **Azure Active Directory** > **그룹** > **새 그룹** 을 선택합니다.
   ![새 그룹을 시작하는 명령 선택](./media/groups-dynamic-tutorial/new-group.png)
3. **그룹** 블레이드에서:
  
   * 그룹 형식으로 **보안** 을 선택합니다.
   * 그룹의 이름 및 설명으로 `Guest users Contoso`를 입력합니다.
   * **구성원 자격 유형** 을 **동적 사용자** 로 변경합니다.
   
4. **소유자** 를 선택하고, **소유자** 추가 블레이드에서 원하는 소유자를 검색합니다. 원하는 소유자를 클릭하여 선택 항목에 추가합니다.
5. **선택** 을 클릭하여 **소유자 추가** 블레이드를 닫습니다.  
6. **동적 사용자 구성원** 상자에서 **동적 쿼리 편집** 을 선택합니다.
7. **동적 구성원 규칙** 블레이드에서 다음을 수행합니다.

   * **속성** 필드에서 기존 값을 클릭하고 **userType** 을 선택합니다. 
   * **연산자 필드** 에서 **같음** 이 선택되었는지 확인합니다.  
   * **값** 필드를 선택하고 **게스트** 를 입력합니다. 
   * **식 추가** 하이퍼링크를 클릭하여 다른 줄을 추가합니다.
   * **및/또는** 필드에서 **및** 을 선택합니다.
   * **속성** 필드에서 **companyName** 을 선택합니다.
   * **연산자 필드** 에서 **같음** 이 선택되었는지 확인합니다.
   * **값** 필드에 **Contoso** 를 입력합니다.
   * **저장** 을 클릭하여 **동적 구성원 규칙** 블레이드를 닫습니다.
   
8. **그룹** 블레이드에서 **만들기** 를 선택하여 그룹을 만듭니다.

## <a name="assign-licenses"></a>라이선스 할당

새 그룹이 생겼으니, 파트너 사용자에게 필요한 라이선스를 적용할 수 있습니다.

1. Azure AD에서 **라이선스** 를 선택하고, 하나 이상의 라이선스를 선택한 다음, **할당** 을 선택합니다.
2. **사용자 및 그룹** 을 선택하고, **게스트 사용자 Contoso** 를 선택하고, 변경 내용을 저장합니다.
3. **할당 옵션** 을 사용하면 선택한 라이선스에 포함된 서비스 플랜을 켜거나 끌 수 있습니다. 변경할 때 반드시 **확인** 을 클릭하여 변경 내용을 저장하세요.
4. 할당을 완료하려면 **라이선스 할당** 창에서 창 맨 아래에 있는 **할당** 을 클릭합니다.

## <a name="remove-guests-from-all-users-group"></a>모든 사용자 그룹에서 게스트 제거

아마도 여러분의 궁극적인 관리 계획은 모든 게스트 사용자를 각각 고유한 그룹에 회사별로 할당하는 것입니다. 이제 조직의 구성원인 사용자에게만 예약되도록 **모든 사용자** 그룹을 변경할 수도 있습니다. 그 후 홈 조직에 관련된 앱 및 라이선스를 할당하는 데 사용할 수 있습니다.

   ![모든 사용자 그룹을 구성원만으로 변경](./media/groups-dynamic-tutorial/all-users-edit.png)

## <a name="clean-up-resources"></a>리소스 정리

**게스트 사용자 그룹을 제거하려면**

1. 조직의 글로벌 관리자 계정을 사용하여 [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. **Azure Active Directory** > **그룹** 을 선택합니다. **게스트 사용자 Contoso** 그룹을 선택하고, 줄임표(...)를 선택한 다음, **삭제** 를 선택합니다. 그룹을 삭제하면 할당된 모든 라이선스가 제거됩니다.

**모든 사용자 그룹을 복원하려면**
1. **Azure Active Directory** > **그룹** 을 선택합니다. **모든 사용자** 그룹의 이름을 선택하여 그룹을 엽니다.
1. **동적 구성원 규칙** 을 선택하고, 규칙의 모든 텍스트를 지우고, **저장** 을 선택합니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음 작업 방법을 알아보았습니다.
> [!div class="checklist"]
> * 게스트 사용자 그룹 만들기
> * 새 그룹에 라이선스 할당
> * 모든 사용자 그룹을 구성원만으로 변경

그 다음 문서를 계속 진행하여 그룹 기반 라이선스 기본 사항을 알아보세요.
> [!div class="nextstepaction"]
> [그룹 라이선스 기본 사항](../fundamentals/active-directory-licensing-whatis-azure-portal.md)



