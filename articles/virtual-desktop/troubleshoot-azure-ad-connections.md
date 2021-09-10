---
title: Azure AD 조인된 VM Azure Virtual Desktop에 연결 - Azure
description: Azure Virtual Desktop에서 Azure AD 조인된 VM에 연결하는 동안 문제를 해결하는 방법입니다.
services: virtual-desktop
author: Heidilohr
manager: lizross
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 08/11/2021
ms.author: helohr
ms.openlocfilehash: 97c81eadf7091f04b77b094a4622c1731d19c537
ms.sourcegitcommit: da9335cf42321b180757521e62c28f917f1b9a07
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/16/2021
ms.locfileid: "122567795"
---
# <a name="connections-to-azure-ad-joined-vms"></a>Azure AD 조인 VM에 연결

>[!IMPORTANT]
>이 콘텐츠는 Azure Resource Manager Azure Virtual Desktop 개체를 통해 Azure Virtual Desktop에 적용됩니다.

이 문서를 참조하여 Azure Virtual Desktop에서 Azure AD 조인된 VM에 연결하는 동안 문제를 해결합니다.

## <a name="provide-feedback"></a>피드백 제공

제품 팀 및 활발하게 활동하는 커뮤니티 멤버들과 Azure Virtual Desktop 서비스에 대해 토론하려면 [Azure Virtual Desktop 기술 커뮤니티](https://techcommunity.microsoft.com/t5/azure-virtual-desktop/bd-p/AzureVirtualDesktopForum)를 방문하세요.

## <a name="all-clients"></a>모든 클라이언트

### <a name="your-account-is-configured-to-prevent-you-from-using-this-device"></a>이 디바이스를 사용할 수 없도록 계정이 구성되어 있음

**귀하의 계정은 이 디바이스를 사용하지 못하도록 구성되어 있습니다. 자세한 내용은 시스템 관리자에게 문의** 하여 사용자 계정에 VM에 대한 [가상 머신 사용자 로그인 역할](../active-directory/devices/howto-vm-sign-in-azure-ad-windows.md#azure-role-not-assigned)이 부여되었는지 확인해야 합니다. 

## <a name="windows-desktop-client"></a>Windows 데스크톱 클라이언트

### <a name="the-logon-attempt-failed"></a>로그온 시도가 실패함

Windows 보안 자격 증명 프롬프트에 **로그온 시도 실패** 라는 오류가 표시되면 다음을 확인하세요.

- 세션 호스트 OR과 동일한 Azure AD 테넌트에 Azure AD 조인된 디바이스 또는 하이브리드 Azure AD 조인된 디바이스를 사용 중입니다.
- 세션 호스트와 동일한 Azure AD 테넌트에 Azure AD 등록된 Windows 10 2004 이상을 실행하는 디바이스를 사용 중입니다.
- 로컬 PC와 세션 호스트 모두에서 [PKU2U 프로토콜이 사용으로 설정](/windows/security/threat-protection/security-policy-settings/network-security-allow-pku2u-authentication-requests-to-this-computer-to-use-online-identities)됩니다.

### <a name="the-sign-in-method-youre-trying-to-use-isnt-allowed"></a>사용하려는 로그인 방법은 허용되지 않음

**사용하려는 로그인 방법이 허용되지 않습니다. 다른 로그인 방법을 시도하거나 시스템 관리자에게 문의하세요.** 라는 오류가 발생하는 경우 액세스를 제한하는 조건부 액세스 정책이 있습니다. [다단계 인증 사용](deploy-azure-ad-joined-vm.md#enabling-mfa-for-azure-ad-joined-vms)의 지침에 따라 Azure AD 조인된 VM에 다단계 인증을 사용하도록 설정합니다.

## <a name="web-client"></a>웹 클라이언트

### <a name="sign-in-failed-please-check-your-username-and-password-and-try-again"></a>로그인에 실패했습니다. 사용자 이름과 암호를 확인하고 다시 시도하세요.

**죄송합니다. NAME에 연결할 수 없습니다. 로그인에 실패했습니다. 사용자 이름과 암호를 확인하고 다시 시도하세요.** 라는 오류가 발생하는 경우 웹 클라이언트를 사용할 때 [다른 클라이언트의 연결을 사용으로 설정](deploy-azure-ad-joined-vm.md#connect-using-the-other-clients)했는지 확인합니다.

### <a name="we-couldnt-connect-to-the-remote-pc-because-of-a-security-error"></a>보안 오류로 인해 원격 PC에 연결할 수 없음

**죄송합니다. NAME에 연결할 수 없습니다. 보안 오류 때문에 원격 PC에 연결할 수 없습니다. 이 문제가 계속 발생하면 관리자 또는 기술 지원에 도움을 요청하세요.** 라는 오류가 발생하는 경우 액세스를 제한하는 조건부 액세스 정책이 있습니다. [다단계 인증 사용](deploy-azure-ad-joined-vm.md#enabling-mfa-for-azure-ad-joined-vms)의 지침에 따라 Azure AD 조인된 VM에 다단계 인증을 사용하도록 설정합니다.

## <a name="android-client"></a>Android 클라이언트

### <a name="error-code-2607---we-couldnt-connect-to-the-remote-pc-because-your-credentials-did-not-work"></a>오류 코드 2607 - 자격 증명이 작동하지 않아 원격 PC에 연결할 수 없습니다.

**We couldn't connect to the remote PC because your credentials did not work. The remote machine is AADJ joined.** (자격 증명이 작동하지 않아 원격 PC에 연결할 수 없습니다. 원격 머신이 AADJ에 조인되어 있습니다.)라는 오류가 표시되고 오류 코드 2607이 표시되면 Android 클라이언트를 사용할 때 [다른 클라이언트의 연결을 사용으로 설정](deploy-azure-ad-joined-vm.md#connect-using-the-other-clients)했는지 확인합니다.

## <a name="next-steps"></a>다음 단계

- Azure Virtual Desktop과 에스컬레이션 트랙 문제 해결에 대한 개요는 [문제 해결 개요, 피드백 및 지원](troubleshoot-set-up-overview.md)을 참조하세요.
- Azure Virtual Desktop 환경에서 Azure Virtual Desktop 환경 및 호스트 풀을 만드는 데 발생하는 문제를 해결하려면 [환경 및 호스트 풀 만들기](troubleshoot-set-up-issues.md)를 참조하세요.
- Azure Virtual Desktop에서 VM(가상 머신)을 구성하는 동안 문제를 해결하려면 [세션 호스트 가상 머신 구성](troubleshoot-vm-configuration.md)을 참조하세요.
- Azure Virtual Desktop 에이전트 또는 세션 연결과 관련된 문제를 해결하려면 [일반적인 Azure Virtual Desktop 에이전트 문제 해결](troubleshoot-agent.md)을 참조하세요.
- Azure Virtual Desktop과 함께 PowerShell을 사용할 때 발생하는 문제를 해결하려면 [Azure Virtual Desktop PowerShell](troubleshoot-powershell.md)을 참조하세요.
- 문제 해결 자습서를 진행하려면 [자습서: Resource Manager 템플릿 배포 문제 해결](../azure-resource-manager/templates/template-tutorial-troubleshoot.md)을 참조하세요.
