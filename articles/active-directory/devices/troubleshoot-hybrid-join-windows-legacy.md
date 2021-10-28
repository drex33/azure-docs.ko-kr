---
title: 레거시 하이브리드 Azure Active Directory 조인 디바이스 문제 해결
description: 하위 수준 디바이스에 조인된 하이브리드 Azure Active Directory 문제 해결
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: troubleshooting
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: karenhoran
ms.reviewer: jairoc
ms.collection: M365-identity-device-management
ms.openlocfilehash: 68c08c8ed88ae9973a453288bd3e55660505caa3
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128620904"
---
# <a name="troubleshooting-hybrid-azure-active-directory-joined-down-level-devices"></a>하위 수준 디바이스에 조인된 하이브리드 Azure Active Directory 문제 해결 

이 문서는 다음 디바이스에만 적용됩니다. 

- Windows 7 
- Windows 8.1 
- Windows Server 2008 R2 
- Windows Server 2012 
- Windows Server 2012 R2 

Windows 10 또는 Windows Server 2016의 경우 [Windows 10 및 Windows Server 2016 디바이스에 조인된 하이브리드 Azure Active Directory 문제 해결](troubleshoot-hybrid-join-windows-current.md)을 참조하세요.

이 문서에서는 다음 시나리오를 지원하도록 [디바이스에 조인된 하이브리드 Azure Active Directory를 구성](hybrid-azuread-join-plan.md)했다고 가정합니다.

- 디바이스 기반 조건부 액세스

이 문서에서는 잠재적인 문제를 해결하는 방법에 대한 문제 해결 지침을 제공합니다.  

**알아야 할 사항:** 

- 하위 수준 Windows 디바이스용 하이브리드 Azure AD 가입이 Windows 10의 경우와 약간 다르게 작동합니다. 대부분의 고객은 AD FS(페더레이션된 도메인용)가 필요한지 또는 Seamless SSO를 구성해야 하는지(관리되는 도메인용)를 잘 모릅니다.
- Firefox 및 Microsoft Edge 브라우저의 프라이빗 검색 모드에서는 Seamless SSO가 작동하지 않습니다. 또한 브라우저가 고급 보호 모드에서 실행 중이거나 고급 보안 구성이 활성화된 경우에는 Internet Explorer에서 작동하지 않습니다.
- 페더레이션된 도메인을 사용하는 고객의 경우 SCP(서비스 연결 지점)가 관리되는 도메인 이름(예: contoso.com 대신 contoso.onmicrosoft.com)을 가리키도록 구성된 경우 하위 수준 Windows 디바이스에 대한 하이브리드 Azure AD 가입이 작동하지 않습니다.
- 여러 도메인 사용자가 하위 수준 하이브리드 Azure AD 가입 디바이스에 로그인하면 동일한 물리적 디바이스가 Azure AD에 여러 번 나타납니다.  예를 들어 *jdoe* 및 *jharnett* 가 디바이스에 로그인하는 경우 **사용자** 정보 탭에 각각에 대해 별도 등록(DeviceID)이 만들어집니다. 
- 운영 체제 재설치 또는 수동 재등록으로 인해 사용자 정보 탭에 디바이스에 대한 여러 항목이 있을 수도 있습니다.
- 초기 디바이스 등록/조인은 로그온 또는 잠금/잠금 해제 상태에서 시도를 수행하도록 구성됩니다. 작업 스케줄러 작업에 의해 트리거되는 5분 지연이 있을 수 있습니다. 
- Windows 7 SP1 또는 Windows Server 2008 R2 SP1의 경우, [KB4284842](https://support.microsoft.com/help/4284842)가 설치되어 있는지 확인합니다. 이 업데이트는 암호를 변경한 후 고객이 보호된 키에 액세스할 수 없어서 발생하는 이후의 인증 실패를 방지합니다.
- 사용자가 UPN을 변경한 후 하이브리드 Azure AD 조인이 실패하고 원활한 SSO 인증 프로세스가 중단될 수 있습니다. 조인 프로세스 중에는 브라우저 세션 쿠키를 지우거나 사용자가 명시적으로 로그아웃하고 이전 UPN을 제거하지 않는 한, 이전 UPN을 Azure AD에 계속 보내는 것을 볼 수 있습니다.

## <a name="step-1-retrieve-the-registration-status"></a>1단계: 등록 상태 검색 

**장치 등록 상태를 확인하려면**  

1. 하이브리드 Azure AD 조인을 수행한 사용자 계정으로 로그온합니다.
1. 명령 프롬프트를 엽니다. 
1. `"%programFiles%\Microsoft Workplace Join\autoworkplace.exe" /i` 입력

이 명령을 실행하면 조인 상태에 대한 세부 정보를 제공하는 대화 상자가 표시됩니다.

:::image type="content" source="./media/troubleshoot-hybrid-join-windows-legacy/01.png" alt-text="Windows용 Workplace Join 대화 상자의 스크린샷. 이메일 주소가 포함된 텍스트는 특정 디바이스가 작업 공간에 조인되어 있음을 나타냅니다." border="false":::

## <a name="step-2-evaluate-the-hybrid-azure-ad-join-status"></a>2단계: 하이브리드 Azure AD 조인 상태 평가 

하이브리드 Azure AD 가입 디바이스가 아니면 "가입" 단추를 클릭하여 하이브리드 Azure AD 가입을 시도할 수 있습니다. 하이브리드 Azure AD 가입 시도가 실패하면 실패 세부 사항이 표시됩니다.

**가장 일반적인 문제는 다음과 같습니다.**

- 잘못 구성된 AD FS 또는 Azure AD, 또는 네트워크 문제

    :::image type="content" source="./media/troubleshoot-hybrid-join-windows-legacy/02.png" alt-text="Windows용 Workplace Join 대화 상자의 스크린샷. 텍스트는 계정 인증 중에 오류가 발생했음을 보고합니다." border="false":::
    
   - Autoworkplace.exe가 Azure AD 또는 AD FS를 사용하여 자동으로 인증할 수 없습니다. 이러한 상황은 누락되었거나 잘못 구성된 AD FS(페더레이션된 도메인용) 또는 누락되었거나 잘못 구성된 Azure AD Seamless Single Sign-On(관리되는 도메인용) 또는 네트워크 문제로 인해 야기될 수 있습니다. 
   - 사용자에 대한 다단계 인증(MFA)이 활성화/구성되었고 AD FS 서버에 WIAORMULTIAUTHN이 구성되지 않았을 수도 있습니다. 
   - 또 다른 가능성은 HRD(홈 영역 검색) 페이지가 사용자 상호 작용을 기다리고 **autoworkplace.exe** 가 자동으로 토큰을 요청하는 것을 방지하는 경우입니다.
   - 클라이언트의 IE 인트라넷 영역에서 AD FS 및 Azure AD URL이 누락되었을 수 있습니다.
   - 네트워크 연결 문제로 인해 **autoworkplace.exe** 가 AD FS 또는 Azure AD URL에 연결하지 못할 수 있습니다. 
   - **Autoworkplace.exe** 를 사용하려면 클라이언트에서 조직의 온-프레미스 AD 도메인 컨트롤러로 직접 가시권이 형성되어야 합니다. 즉, 하이브리드 Azure AD 조인은 클라이언트가 조직의 인트라넷에 연결된 경우에만 성공적으로 수행됩니다.
   - 조직에서 Azure AD Seamless Single Sign-On을 사용하고, 디바이스의 IE 인트라넷 설정에 `https://autologon.microsoftazuread-sso.com` 또는 `https://aadg.windows.net.nsatc.net`이 없고, 인트라넷 영역에 **스크립트를 통한 상태 표시줄 업데이트 허용** 이 활성화되어 있지 않습니다.
- 도메인 사용자로 로그온되지 않음

   :::image type="content" source="./media/troubleshoot-hybrid-join-windows-legacy/03.png" alt-text="Windows용 Workplace Join 대화 상자의 스크린샷. 텍스트는 계정 확인 중에 오류가 발생했음을 보고합니다." border="false":::

   발생할 수 있는 몇 가지 다른 이유가 있습니다.

   - 로그인한 사용자가 도메인 사용자(예: 로컬 사용자)가 아닙니다. 도메인 사용자에 대해 하위 수준 디바이스의 하이브리드 Azure AD 조인만 지원됩니다.
   - 클라이언트가 도메인 컨트롤러에 연결할 수 없습니다.    
- 할당량에 도달함

    :::image type="content" source="./media/troubleshoot-hybrid-join-windows-legacy/04.png" alt-text="Windows용 Workplace Join 대화 상자의 스크린샷. 사용자가 조인된 최대 디바이스 수에 도달했기 때문에 텍스트에서 오류를 보고합니다." border="false":::

- 서비스가 응답하지 않음 

    :::image type="content" source="./media/troubleshoot-hybrid-join-windows-legacy/05.png" alt-text="Windows용 Workplace Join 대화 상자의 스크린샷. 텍스트는 서버가 응답하지 않아 오류가 발생했음을 보고합니다." border="false":::

**Applications and Services Log\Microsoft-Workplace Join** 의 이벤트 로그에서 상태 정보를 찾을 수도 있습니다.
  
**실패한 하이브리드 Azure AD 조인에 대한 가장 일반적인 원인은 다음과 같습니다.** 

- 컴퓨터가 조직의 내부 네트워크에 연결되어 있지 않거나 온-프레미스 AD 도메인 컨트롤러에 연결된 VPN에 연결되어 있지 않습니다.
- 로컬 컴퓨터 계정으로 컴퓨터에 로그온됩니다. 
- 서비스 구성 문제: 
   - AD FS 서버가 **WIAORMULTIAUTHN** 을 지원하도록 구성되지 않았습니다. 
   - 컴퓨터의 포리스트에 Azure AD에서 확인된 도메인 이름을 가리키는 서비스 연결 지점 개체가 없습니다. 
   - 또는 도메인이 관리되는 경우 Seamless SSO가 구성되지 않았거나 작동하지 않습니다.
   - 디바이스 제한에 도달했습니다. 

## <a name="next-steps"></a>다음 단계

- [Microsoft 오류 조회 도구](/windows/win32/debug/system-error-code-lookup-tool)
