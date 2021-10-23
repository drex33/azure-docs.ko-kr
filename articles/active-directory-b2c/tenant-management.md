---
title: Azure Active Directory B2C 관리
titleSuffix: Azure Active Directory B2C
description: Azure Active Directory B2C 테넌트를 관리하는 방법을 알아봅니다. Azure AD B2C에서 지원되는 Azure AD 기능, 관리자 역할을 사용하여 리소스를 관리하는 방법 및 회사 계정과 게스트 사용자를 Azure AD B2C 테넌트에 추가하는 방법을 알아봅니다.
services: active-directory-b2c
author: kengaderdus
manager: CelesteDG
ms.service: active-directory
ms.workload: identity
ms.topic: tutorial
ms.date: 10/04/2021
ms.custom: project-no-code
ms.author: kengaderdus
ms.subservice: B2C
ms.openlocfilehash: a39b34f0b783997a90e634157354543aa3eab79a
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2021
ms.locfileid: "130042981"
---
# <a name="manage-your-azure-active-directory-b2c-tenant"></a>Azure Active Directory B2C 테넌트 관리

Azure AD B2C(Azure Active Directory B2C)에서 테넌트는 소비자 사용자의 디렉터리를 나타냅니다. 각 Azure AD B2C 테넌트는 다른 Azure AD B2C 테넌트와는 전혀 다르고 별개입니다. Azure AD B2C 테넌트는 이미 있을 수 있는 Azure Active Directory 테넌트와 다릅니다. 이 문서에서는 Azure AD B2C 테넌트를 관리하는 방법에 대해 알아봅니다.

## <a name="prerequisites"></a>사전 요구 사항
- 아직 고유한 [Azure AD B2C 테넌트](tutorial-create-tenant.md)를 만들지 않았다면 지금 만듭니다. 기존 Azure AD B2C 테넌트를 사용해도 됩니다.
- [Azure AD B2C의 사용자 계정](user-overview.md)을 이해합니다.
- [리소스 액세스를 제어하기 위한 사용자 역할](roles-resource-access-control.md)을 이해합니다.


## <a name="add-an-administrator-work-account"></a>관리자 추가(회사 계정)

새 관리 계정을 만들려면 다음 단계를 수행합니다.

1. 전역 관리자 또는 권한 있는 역할 관리자 권한으로 [Azure Portal](https://portal.azure.com/)에 로그인합니다.
1. Azure AD B2C 테넌트가 포함된 디렉터리를 사용하고 있는지 확인합니다. 포털 도구 모음에서 **디렉터리 + 구독** 아이콘을 선택합니다.
1. **포털 설정 | 디렉터리 + 구독** 페이지의 **디렉터리 이름** 목록에서 Azure AD B2C 디렉터리를 찾은 다음, **전환** 을 선택합니다.
1. **Azure 서비스** 에서 **Azure AD B2C** 를 선택합니다. 또는 검색 상자를 사용하여 **Azure AD B2C** 를 찾고 선택합니다.
1. **관리** 에서 **사용자** 를 선택합니다.
1. **새 사용자** 를 선택합니다.
1. **사용자**  페이지에서 이 사용자에 대한 정보를 입력합니다.

   - **이름**. 필수 요소. 새 사용자의 이름 및 성입니다. 예를 들어, *Mary Parker* 입니다.
   - **사용자 이름**. 필수 요소. 새 사용자의 사용자 이름입니다. 예: `mary@contoso.com`.
     사용자 이름의 도메인 부분에는 초기 기본 도메인 이름( *\<yourdomainname>.onmicrosoft.com*)을 사용해야 합니다.
   - **그룹** - 원할 경우, 하나 이상의 기존 그룹에 사용자를 추가할 수 있습니다. 사용자를 나중에 그룹에 추가할 수도 있습니다. 
   - **디렉터리 역할**: 사용자에 대한 Azure AD 관리 권한이 필요한 경우 Azure AD 역할에 추가할 수 있습니다. Azure AD에서 사용자에게 전역 관리자 또는 하나 이상의 제한된 관리자 역할을 할당할 수 있습니다. 역할 할당에 대한 자세한 내용은 [역할을 사용하여 리소스 액세스 제어](roles-resource-access-control.md)를 참조하세요.
   - **작업 정보**: 여기서 사용자에 대한 추가 정보를 추가하거나 나중에 추가할 수 있습니다. 

1. **암호** 상자에 제공된 자동 생성된 암호를 복사합니다. 처음으로 로그인하는 사용자에게 이 암호를 제공해야 합니다.
1. **만들기** 를 선택합니다.

사용자가 만들어지고 Azure AD B2C 테넌트에 추가됩니다. Azure AD B2C 테넌트에서 고유한 하나 이상의 회사 계정을 전역 관리자 역할에 할당하는 것이 좋습니다. 이 계정은 비상 계정으로 간주할 수 있습니다.

## <a name="invite-an-administrator-guest-account"></a>관리자 초대(게스트 계정)

새 게스트 사용자를 초대하여 테넌트를 관리할 수도 있습니다. 이 ID의 수명 주기는 외부에서 관리할 수 있으므로 조직에 Azure AD가 있는 경우에도 게스트 계정은 기본 설정 옵션입니다. 

사용자를 초대하려면 다음 단계를 수행합니다.

1. 전역 관리자 또는 권한 있는 역할 관리자 권한으로 [Azure Portal](https://portal.azure.com/)에 로그인합니다.
1. Azure AD B2C 테넌트가 포함된 디렉터리를 사용하고 있는지 확인합니다. 포털 도구 모음에서 **디렉터리 + 구독** 아이콘을 선택합니다.
1. **포털 설정 | 디렉터리 + 구독** 페이지의 **디렉터리 이름** 목록에서 Azure AD B2C 디렉터리를 찾은 다음, **전환** 을 선택합니다.
1. **Azure 서비스** 에서 **Azure AD B2C** 를 선택합니다. 또는 검색 상자를 사용하여 **Azure AD B2C** 를 찾고 선택합니다.
1. **관리** 에서 **사용자** 를 선택합니다.
1. **새 게스트 계정** 을 선택합니다.
1. **사용자**  페이지에서 이 사용자에 대한 정보를 입력합니다.

   - **이름**. 필수 요소. 새 사용자의 이름 및 성입니다. 예를 들어, *Mary Parker* 입니다.
   - **이메일 주소**. 필수 요소. 초대하려는 사용자의 이메일 주소입니다. 예: `mary@contoso.com`.   
   - **개인 메시지**. 초대 이메일에 포함될 개인 메시지를 추가합니다.
   - **그룹** - 원할 경우, 하나 이상의 기존 그룹에 사용자를 추가할 수 있습니다. 사용자를 나중에 그룹에 추가할 수도 있습니다.
   - **디렉터리 역할**: 사용자에 대한 Azure AD 관리 권한이 필요한 경우 Azure AD 역할에 추가할 수 있습니다. Azure AD에서 사용자에게 전역 관리자 또는 하나 이상의 제한된 관리자 역할을 할당할 수 있습니다. 역할 할당에 대한 자세한 내용은 [역할을 사용하여 리소스 액세스 제어](roles-resource-access-control.md)를 참조하세요.
   - **작업 정보**: 여기서 사용자에 대한 추가 정보를 추가하거나 나중에 추가할 수 있습니다.

1. **만들기** 를 선택합니다.

사용자에게 초대 이메일을 보냅니다. 로그인하려면 사용자가 초대를 수락해야 합니다.

### <a name="resend-the-invitation-email"></a>초대 이메일 다시 보내기

게스트가 초대 이메일을 받지 못했거나 초대가 만료된 경우 초대를 다시 보낼 수 있습니다. 초대 이메일을 보내는 대신 게스트에게 초대를 수락하는 데 사용할 수 있는 직접 링크를 제공할 수 있습니다. 초대를 다시 보내고 직접 링크를 가져오려면 다음을 수행합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. Azure AD B2C 테넌트가 포함된 디렉터리를 사용하고 있는지 확인합니다. 포털 도구 모음에서 **디렉터리 + 구독** 아이콘을 선택합니다.
1. **포털 설정 | 디렉터리 + 구독** 페이지의 **디렉터리 이름** 목록에서 Azure AD B2C 디렉터리를 찾은 다음, **전환** 을 선택합니다.
1. **Azure 서비스** 에서 **Azure AD B2C** 를 선택합니다. 또는 검색 상자를 사용하여 **Azure AD B2C** 를 찾고 선택합니다.
1. **관리** 에서 **사용자** 를 선택합니다.
1. 초대를 다시 보낼 사용자를 검색하여 선택합니다.
1. **사용자 | 프로필** 페이지의 **ID** 아래에서 **(관리)** 를 선택합니다.
        ![게스트 계정 초대 이메일을 다시 보내는 방법을 보여 주는 스크린샷](./media/tenant-management/guest-account-resend-invite.png)

1. **초대 다시 보내기** 에 대해 **예** 를 선택합니다. **초대를 다시 보내시겠습니까?** 가 표시되면 **예** 를 선택합니다.
1. Azure AD B2C에서 초대를 보냅니다. 초대 URL을 복사하여 게스트에 직접 제공할 수도 있습니다.
    
    ![초대 URL을 가져오는 방법을 보여 주는 스크린샷](./media/tenant-management/guest-account-invitation-url.png)  
 
## <a name="add-a-role-assignment"></a>역할 할당 추가

역할은 [사용자를 만들거나](#add-an-administrator-work-account) [게스트 사용자를 초대](#invite-an-administrator-guest-account)할 때 할당할 수 있습니다. 사용자에 대한 역할을 추가하거나, 변경하거나, 제거할 수 있습니다.

1. 전역 관리자 또는 권한 있는 역할 관리자 권한으로 [Azure Portal](https://portal.azure.com/)에 로그인합니다.
1. Azure AD B2C 테넌트가 포함된 디렉터리를 사용하고 있는지 확인합니다. 포털 도구 모음에서 **디렉터리 + 구독** 아이콘을 선택합니다.
1. **포털 설정 | 디렉터리 + 구독** 페이지의 **디렉터리 이름** 목록에서 Azure AD B2C 디렉터리를 찾은 다음, **전환** 을 선택합니다.
1. **Azure 서비스** 에서 **Azure AD B2C** 를 선택합니다. 또는 검색 상자를 사용하여 **Azure AD B2C** 를 찾고 선택합니다.
1. **관리** 에서 **사용자** 를 선택합니다.
1. 역할을 변경하려는 사용자를 선택합니다. 그런 다음, **할당된 역할** 을 선택합니다.
1. **할당 추가** 를 선택하고, 할당할 역할(예: *애플리케이션 관리자*)을 선택한 다음, **추가** 를 선택합니다.

## <a name="remove-a-role-assignment"></a>역할 할당 제거

사용자의 역할 할당을 제거해야 하는 경우 다음 단계를 수행합니다.

1. **Azure AD B2C** 를 선택하고 **사용자** 를 선택한 다음, 사용자를 검색하여 선택합니다.
1. **할당된 역할** 을 선택합니다. 제거할 역할(예: *애플리케이션 관리자*)을 선택한 다음, **할당 제거** 를 선택합니다.

## <a name="review-administrator-account-role-assignments"></a>관리자 계정 역할 할당 검토

감사 프로세스의 일부로 일반적으로 Azure AD B2C 디렉터리의 특정 역할에 할당된 사용자를 검토합니다. 다음 단계를 사용하여 현재 권한 있는 역할이 할당된 사용자를 감사합니다.

1. 전역 관리자 또는 권한 있는 역할 관리자 권한으로 [Azure Portal](https://portal.azure.com/)에 로그인합니다.
1. Azure AD B2C 테넌트가 포함된 디렉터리를 사용하고 있는지 확인합니다. 포털 도구 모음에서 **디렉터리 + 구독** 아이콘을 선택합니다.
1. **포털 설정 | 디렉터리 + 구독** 페이지의 **디렉터리 이름** 목록에서 Azure AD B2C 디렉터리를 찾은 다음, **전환** 을 선택합니다.
1. **Azure 서비스** 에서 **Azure AD B2C** 를 선택합니다. 또는 검색 상자를 사용하여 **Azure AD B2C** 를 찾고 선택합니다.
1. **관리** 아래에서 **역할 및 관리자** 를 선택합니다.
1. 역할(예: **전역 관리자**)을 선택합니다. **역할 | 할당** 페이지에 해당 역할의 사용자가 나열됩니다.

## <a name="delete-an-administrator-account"></a>관리자 계정 삭제

기존 사용자를 삭제하려면 *전역 관리자* 역할 할당이 있어야 합니다. 전역 관리자는 다른 관리자를 포함하여 모든 사용자를 삭제할 수 있습니다. *사용자 관리자* 는 관리자가 아닌 사용자를 삭제할 수 있습니다.

1. Azure AD B2C 디렉터리에서 **사용자** 를 선택한 다음, 삭제하려는 사용자를 선택합니다.
1. **삭제** 를 선택한 다음, **예** 를 선택하여 삭제를 확인합니다.

사용자가 삭제되고 **사용자 - 모든 사용자** 페이지에 더 이상 표시되지 않습니다. 이 사용자는 다음 30일 동안 **삭제된 사용자** 페이지에서 볼 수 있으며 해당 기간 복원할 수 있습니다. 사용자를 복원하는 방법에 대한 자세한 내용은 [Azure Active Directory를 사용하여 최근에 삭제된 사용자 복원 또는 제거](../active-directory/fundamentals/active-directory-users-restore.md)를 참조하세요.

## <a name="protect-administrative-accounts"></a>관리 계정 보호

보안을 강화하기 위해 MFA(다단계 인증)를 사용하여 모든 관리자 계정을 보호하는 것이 좋습니다. MFA는 로그인 중에 사용자에게 모바일 디바이스의 확인 코드 또는 Microsoft Authenticator 앱의 요청과 같은 더 많은 형식의 식별을 요구하는 ID 확인 프로세스입니다.

![로그인 스크린샷에서 사용 중인 인증 방법](./media/tenant-management/sing-in-with-multi-factor-authentication.png)

[Azure AD 보안 기본값](../active-directory/fundamentals/concept-fundamentals-security-defaults.md)을 사용하도록 설정하여 모든 관리 계정에서 MFA를 사용하도록 적용할 수 있습니다.

## <a name="get-your-tenant-name"></a>테넌트 이름 가져오기

Azure AD B2C 테넌트 이름을 얻으려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. Azure AD B2C 테넌트가 포함된 디렉터리를 사용하고 있는지 확인합니다. 포털 도구 모음에서 **디렉터리 + 구독** 아이콘을 선택합니다.
1. **포털 설정 | 디렉터리 + 구독** 페이지의 **디렉터리 이름** 목록에서 Azure AD B2C 디렉터리를 찾은 다음, **전환** 을 선택합니다.
1. Azure Portal에서 **Azure AD B2C** 를 검색하고 선택합니다.
1. **개요** 에서 **도메인 이름** 을 복사합니다.

![이 스크린샷은 Azure AD B2C 테넌트 이름을 얻는 방법을 보여 줍니다.](./media/tenant-management/get-azure-ad-b2c-tenant-name.png)  

## <a name="get-your-tenant-id"></a>테넌트 ID 가져오기

Azure AD B2C 테넌트 ID를 얻으려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. Azure AD B2C 테넌트가 포함된 디렉터리를 사용하고 있는지 확인합니다. 포털 도구 모음에서 **디렉터리 + 구독** 아이콘을 선택합니다.
1. **포털 설정 | 디렉터리 + 구독** 페이지의 **디렉터리 이름** 목록에서 Azure AD B2C 디렉터리를 찾은 다음, **전환** 을 선택합니다.
1. Azure Portal에서 **Azure Active Directory** 를 검색하고 선택합니다.
1. **개요** 에서 **테넌트 ID** 를 복사합니다.

![이 스크린샷은 Azure AD B2C 테넌트 ID를 얻는 방법을 보여 줍니다.](./media/tenant-management/get-azure-ad-b2c-tenant-id.png)  

## <a name="next-steps"></a>다음 단계

- [리소스 정리 및 테넌트 삭제](tutorial-delete-tenant.md)
