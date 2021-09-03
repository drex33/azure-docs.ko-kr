---
title: Azure Virtual Desktop에 대한 Azure 다단계 인증 설정 - Azure
description: Azure Virtual Desktop 보안을 강화하기 위해 Azure 다단계 인증을 설정하는 방법입니다.
author: Heidilohr
ms.topic: how-to
ms.date: 12/10/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: 6e01b132dd68fd711af30f816df15795991efd41
ms.sourcegitcommit: 8bca2d622fdce67b07746a2fb5a40c0c644100c6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/09/2021
ms.locfileid: "111754190"
---
# <a name="enable-azure-multifactor-authentication-for-azure-virtual-desktop"></a>Azure Virtual Desktop에 대해 Azure 다단계 인증을 사용하도록 설정

>[!IMPORTANT]
> Azure Virtual Desktop(클래식) 문서에서 이 페이지를 방문한 경우, 완료되면 [Azure Virtual Desktop(클래식) 설명서로 돌아가야 합니다](./virtual-desktop-fall-2019/tenant-setup-azure-active-directory.md).

Azure Virtual Desktop용 Windows 클라이언트는 로컬 머신과 Azure Virtual Desktop을 통합하는 데 유용한 옵션입니다. 그러나 Windows 클라이언트에 Azure Virtual Desktop 계정을 구성할 때 사용자와 사용자의 안전을 유지하기 위해 수행해야 하는 특정 조치가 있습니다.

처음 로그인하면 클라이언트에서 사용자 이름, 암호 및 Azure 다단계 인증을 요청합니다. 그런 다음, 클라이언트는 다음에 로그인할 때 Azure AD(Active Directory) 엔터프라이즈 애플리케이션의 토큰을 기억합니다. 세션 호스트의 자격 증명을 요청하는 메시지에서 **이 컴퓨터에 저장** 을 선택하면 사용자는 자격 증명을 다시 입력할 필요 없이 클라이언트를 다시 시작한 후 로그인할 수 있습니다.

자격 증명을 기억하는 것은 편리하지만 엔터프라이즈 시나리오 또는 개인 디바이스에 대한 배포를 덜 안전하게 만들 수도 있습니다. 사용자를 보호하기 위해 클라이언트가 Azure 다단계 인증 자격 증명을 계속 더 자주 요청하도록 할 수 있습니다. 이 문서에서는 Azure Virtual Desktop에 대한 조건부 액세스 정책을 구성하여 이 설정을 사용하도록 설정하는 방법을 보여 줍니다.

## <a name="prerequisites"></a>사전 요구 사항

시작하기 위해 필요한 항목은 다음과 같습니다.

- 사용자에게 Azure Active Directory Premium P1 또는 P2가 포함된 라이선스를 할당합니다.
- 사용자가 그룹 멤버로 할당된 Azure Active Directory 그룹입니다.
- 모든 사용자에 대해 Azure 다단계 인증을 사용하도록 설정합니다. 이 방법에 대한 자세한 내용은 [사용자에 대해 2단계 인증을 요구하는 방법](../active-directory/authentication/howto-mfa-userstates.md#view-the-status-for-a-user)을 참조하세요.

> [!NOTE]
> 다음 설정은 [Azure Virtual Desktop 웹 클라이언트](https://rdweb.wvd.microsoft.com/arm/webclient/index.html)에도 적용됩니다.

## <a name="create-a-conditional-access-policy"></a>조건부 액세스 정책 만들기

Azure Virtual Desktop에 연결할 때 다단계 인증이 필요한 조건부 액세스 정책을 만드는 방법은 다음과 같습니다.

1. **Azure Portal** 에 전역 관리자, 보안 관리자 또는 조건부 액세스 관리자로 로그인합니다.
2. **Azure Active Directory** > **Security** > **조건부 액세스** 로 이동합니다.
3. **새 정책** 을 선택합니다.
4. 정책에 이름을 지정합니다. 조직에서 정책 이름에 의미 있는 표준을 만드는 것이 좋습니다.
5. **할당** 에서 **사용자 및 그룹** 을 선택합니다.
6. **포함** 에서 **사용자 및 그룹 선택** > **사용자 및 그룹** > [필수 조건](#prerequisites) 단계에서 만든 그룹을 선택합니다.
7. **완료** 를 선택합니다.
8. **클라우드 앱 또는 작업** > **포함** 에서 **앱 선택** 을 선택합니다.
9. 사용 중인 Azure Virtual Desktop의 버전에 따라 다음 앱 중 하나를 선택합니다.
   
   - Azure Virtual Desktop(클래식)을 사용하는 경우 다음 앱을 선택합니다.
       
       - **Azure Virtual Desktop**(앱 ID 5a0aa725-4958-4b0c-80a9-34562e23f3b7)
       - **Azure Virtual Desktop 클라이언트**(앱 ID fa4345a4-a730-4230-84a8-7d9651b86739): 웹 클라이언트에서 정책을 설정할 수 있습니다.
       
        그런 다음 11단계로 건너뜁니다.

   - Azure Virtual Desktop을 사용하는 경우 이 앱을 대신 선택합니다.
       
       -  **Azure Virtual Desktop**(앱 ID 9cdead84-a844-4324-93f2-b2e6bb768d07)
       
        그런 다음 10단계로 이동합니다.

   >[!IMPORTANT]
   > Azure Virtual Desktop Azure Resource Manager 공급자라는 앱을 선택하지 마세요(50e95039-b200-4007-bc97-8d5790743a63). 이 앱은 사용자 피드를 검색하는 데만 사용되며 다단계 인증을 사용할 수 없습니다.
   > 
   > Azure Virtual Desktop(클래식)을 사용하는 경우 조건부 액세스 정책이 모든 액세스를 차단하고 Azure Virtual Desktop 앱 ID만 제외하는 경우 정책에 앱 ID 9cdead84-a844-4324-93f2-b2e6bb768d07을 추가하여 이 문제를 해결할 수 있습니다. 이 앱 ID를 추가하지 않으면 Azure Virtual Desktop(클래식) 리소스의 피드 검색을 차단합니다.

10. **조건** > **클라이언트 앱** 으로 이동합니다. **구성** 에서 **예** 를 선택한 다음 정책을 적용할 위치를 선택합니다.
    
    - 정책을 웹 클라이언트에 적용하려면 **브라우저** 를 선택합니다.
    - 정책을 다른 클라이언트에 적용하려면 **모바일 앱 및 데스크톱 클라이언트** 를 선택합니다.
    - 모든 클라이언트에 정책을 적용하려면 두 확인란을 모두 선택합니다.
   
    > [!div class="mx-imgBorder"]
    > ![클라이언트 앱 페이지의 스크린샷 사용자가 모바일 앱 및 데스크톱 클라이언트 확인란을 선택했습니다.](media/select-apply.png)

11. 앱을 선택했으면 **선택** 을 선택한 다음 **완료** 를 선택합니다.

    > [!div class="mx-imgBorder"]
    > ![클라우드 앱 또는 작업 페이지의 스크린샷 Azure Virtual Desktop 및 Azure Virtual Desktop 클라이언트 앱은 빨간색으로 강조 표시됩니다.](media/cloud-apps-enterprise.png)

    >[!NOTE]
    >선택하려는 앱의 앱 ID를 찾으려면 **엔터프라이즈 애플리케이션** 으로 이동하여 애플리케이션 유형 드롭다운 메뉴에서 **Microsoft 애플리케이션** 을 선택합니다.

12. **액세스 제어** > **권한 부여** 에서 **액세스 권한 부여**, **다단계 인증 요구**, **선택** 을 차례로 선택합니다.
13. **액세스 제어** > **세션** 에서 **로그인 빈도** 를 선택하고 프롬프트 사이에 원하는 시간으로 값을 설정한 다음 **선택** 을 선택합니다. 예를 들어 값을 **1** 로 설정하고 단위를 **시간** 으로 설정하면 마지막 연결 후 1시간 후에 연결이 시작되는 경우 다단계 인증이 필요합니다.
14. 설정을 확인하고 **정책 사용** 을 **켜기** 로 설정합니다.
15. **만들기** 를 선택하여 정책을 사용하도록 설정합니다.

>[!NOTE]
>웹 클라이언트를 사용하여 브라우저를 통해 Azure Virtual Desktop에 로그인하는 경우 로그에 클라이언트 앱 ID가 a85cf173-4192-42f8-81fa-777a763e6e2c(Azure Virtual Desktop 클라이언트)로 나열됩니다. 클라이언트 앱이 조건부 액세스 정책이 설정된 서버 앱 ID에 내부적으로 연결되어 있기 때문입니다. 

## <a name="next-steps"></a>다음 단계

- [조건부 액세스 정책에 대해 자세히 알아보세요.](../active-directory/conditional-access/concept-conditional-access-policies.md)

- [사용자 로그인 빈도에 대해 자세히 알아보기](../active-directory/conditional-access/howto-conditional-access-session-lifetime.md#user-sign-in-frequency)
