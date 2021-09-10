---
title: Azure Virtual Desktop에서 Azure AD 조인 VM 배포 - Azure
description: Azure Virtual Desktop에서 Azure AD 조인 VM을 구성하고 배포하는 방법
services: virtual-desktop
author: Heidilohr
manager: lizross
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 08/11/2021
ms.author: helohr
ms.openlocfilehash: c7767ad85fabf748a442644f6c7c6701375d58c0
ms.sourcegitcommit: da9335cf42321b180757521e62c28f917f1b9a07
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/16/2021
ms.locfileid: "122538251"
---
# <a name="deploy-azure-ad-joined-virtual-machines-in-azure-virtual-desktop"></a>Azure Virtual Desktop에서 Azure AD 조인 가상 머신 배포

> [!IMPORTANT]
> Azure AD 조인 VM 지원은 현재 공개 미리 보기입니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며, 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.
> 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

이 문서에서는 Azure Virtual Desktop에서 Azure Active Directory 조인 가상 머신을 배포하고 액세스하는 프로세스를 설명합니다. Azure AD 조인 VM은 VM에서 온-프레미스나 가상화된 Active Directory DC(도메인 컨트롤러)로의 가시선 또는 Azure AD DS(Azure AD 도메인 서비스)로 배포에 대한 요구 사항을 제거합니다. 일부 경우에는 DC에 대한 요구 사항을 완전히 제거하여 환경의 배포와 관리를 간소화할 수 있습니다. 관리가 용이하도록 이러한 VM이 자동으로 Intune에 등록될 수도 있습니다.

> [!NOTE]
> Azure Virtual Desktop(클래식)은 이 기능을 지원하지 않습니다.

## <a name="supported-configurations"></a>지원되는 구성

현재 Azure AD 조인 VM에서 다음 구성이 지원됩니다.

- 로컬 사용자 프로필이 있는 개인 데스크톱
- 점프 상자로 사용되는 풀링된 데스크톱. 이 구성에서 사용자는 네트워크의 다른 PC에 연결하기 전에 Azure Virtual Desktop VM에 액세스합니다. 사용자는 VM에 데이터를 저장하면 안 됩니다.
- 사용자가 VM에 데이터를 저장할 필요가 없는 풀링된 데스크톱이나 앱. 예를 들어 데이터를 온라인으로 저장하거나 원격 데이터베이스에 연결하는 애플리케이션의 경우입니다.

사용자 계정은 동일한 Azure AD 테넌트에서 클라우드 전용 또는 하이브리드 사용자가 될 수 있습니다. 현재 외부 사용자는 지원되지 않습니다.

## <a name="deploy-azure-ad-joined-vms"></a>Azure AD 조인 VM 배포

> [!IMPORTANT]
> 퍼블릭 미리 보기 중에는 호스트 풀이 [유효성 검사 환경](create-validation-host-pool.md)에 있도록 구성해야 합니다.

[새 호스트 풀을 만들거나](create-host-pools-azure-marketplace.md) [기존 호스트 풀을 확장](expand-existing-host-pool.md)할 때 Azure Portal에서 직접 Azure AD 조인 VM을 배포할 수 있습니다. Virtual Machines 탭에서 VM을 Active Directory 또는 Azure Active Directory에 조인할지 여부를 선택합니다. **Azure Active Directory** 를 선택하면 자동으로 **Intune에 VM을 등록** 할 수 있는 옵션이 제공되므로 [Windows 10 Enterprise](/mem/intune/fundamentals/windows-virtual-desktop) 및 [Windows 10 Enterprise 다중 세션](/mem/intune/fundamentals/windows-virtual-desktop-multi-session) VM을 간편하게 관리할 수 있습니다. Azure Active Directory 옵션은 VM을 구독과 동일한 Azure AD 테넌트에 조인합니다.

> [!NOTE]
> - 호스트 풀에는 동일한 도메인 가입 유형의 VM만 포함되어야 합니다. 예를 들어 AD 조인 VM은 다른 AD VM에만 있어야 하고 그 반대의 경우도 마찬가지입니다.
> - 호스트 풀 VM은 Windows 10 단일 세션 또는 다중 세션 버전 2004 이상이어야 합니다.

호스트 풀을 만든 후에는 사용자 액세스 권한을 할당해야 합니다. Azure AD 조인 VM의 경우 다음 두 가지 작업을 수행해야 합니다.

- 앱 그룹에 사용자를 추가하여 리소스에 대한 액세스 권한을 부여합니다.
- 사용자가 VM에 로그인할 수 있도록 사용자에게 Virtual Machine 사용자 로그인 역할을 부여합니다.

[앱 그룹 관리](manage-app-groups.md)의 지침을 수행하여 앱과 데스크톱에 대한 사용자 액세스 권한을 할당합니다. 가능하면 개별 사용자 대신 사용자 그룹을 사용하는 것이 좋습니다.

사용자에게 Azure AD 조인 VM에 대한 액세스 권한을 부여하려면 [VM에 대한 역할 할당을 구성](../active-directory/devices/howto-vm-sign-in-azure-ad-windows.md#configure-role-assignments-for-the-vm)해야 합니다. VM, VM이 포함된 리소스 그룹 또는 구독 중 하나에 **Virtual Machine 사용자 로그인** 이나 **Virtual Machine 관리자 로그인** 역할을 할당할 수 있습니다. 호스트 풀의 모든 VM에 적용되게 하려면 리소스 그룹 수준에서 앱 그룹에 사용한 사용자 그룹과 동일한 사용자 그룹에 Virtual Machine 사용자 로그인 역할을 할당하는 것이 좋습니다.

## <a name="access-azure-ad-joined-vms"></a>Azure AD 조인 VM에 액세스

이 섹션에서는 다양한 Azure Virtual Desktop 클라이언트에서 Azure AD 조인 VM에 액세스하는 방법을 설명합니다.

> [!NOTE]
> 현재 Windows 저장소 클라이언트를 사용하여 Azure AD 조인 VM에 연결할 수 없습니다.

> [!NOTE]
> 현재 Azure Virtual Desktop은 Azure AD 조인 VM에 대한 Single Sign-On을 지원하지 않습니다.

### <a name="connect-using-the-windows-desktop-client"></a>Windows 데스크톱 클라이언트를 사용하여 연결

기본 구성에서는 [Windows 데스크톱 클라이언트](user-documentation/connect-windows-7-10.md)를 사용하여 Windows 10에서 연결을 지원합니다. 자격 증명, 스마트 카드, [비즈니스용 Windows Hello 인증서 신뢰](/windows/security/identity-protection/hello-for-business/hello-hybrid-cert-trust) 또는 [비즈니스용 Windows Hello 키 신뢰(인증서 포함)](/windows/security/identity-protection/hello-for-business/hello-deployment-rdp-certs)를 사용하여 세션 호스트에 로그인할 수 있습니다. 그러나 세션 호스트에 액세스하려면 로컬 PC가 다음 조건 중 하나를 충족해야 합니다.

- 로컬 PC는 세션 호스트와 동일한 Azure AD 테넌트에 조인된 Azure AD입니다.
- 로컬 PC는 세션 호스트와 동일한 Azure AD 테넌트에 조인된 하이브리드 Azure AD입니다.
- 로컬 PC가 Windows 10 버전 2004 이상에서 실행되고 있으며 세션 호스트와 동일한 Azure AD 테넌트에 등록된 Azure AD입니다.

Azure AD에 조인되지 않은 Windows 디바이스에서 액세스를 사용하려면 **targetisaadjoined:i:1** 을 [사용자 지정 RDP 속성](customize-rdp-properties.md)으로 호스트 풀에 추가합니다. 이러한 연결은 세션 호스트에 로그인할 때 사용자 이름과 암호 자격 증명 입력으로 제한됩니다.

### <a name="connect-using-the-other-clients"></a>다른 클라이언트를 사용하여 연결

웹, Android, macOS 및 iOS 클라이언트를 사용하여 Azure AD 조인 VM에 액세스하려면 **targetisaadjoined:i:1** 을 [사용자 지정 RDP 속성](customize-rdp-properties.md)으로 호스트 풀에 추가합니다. 이러한 연결은 세션 호스트에 로그인할 때 사용자 이름과 암호 자격 증명 입력으로 제한됩니다.

### <a name="enabling-mfa-for-azure-ad-joined-vms"></a>Azure AD 조인 VM에 MFA 사용

Azure Virtual Desktop 앱에서 조건부 액세스 정책을 설정하면 Azure AD 조인 VM에 [다단계 인증](set-up-mfa.md)을 사용할 수 있습니다. 연결에 성공하려면 [레거시 사용자별 다단계 인증을 사용하지 않습니다](../active-directory/devices/howto-vm-sign-in-azure-ad-windows.md#using-conditional-access). 비즈니스용 Windows Hello와 같은 강력한 인증 방법에 대한 로그인을 제한하지 않으려면 조건부 액세스 정책에서 [Azure Windows VM 로그인 앱](../active-directory/devices/howto-vm-sign-in-azure-ad-windows.md#mfa-sign-in-method-required)을 제외해야 합니다.

## <a name="user-profiles"></a>사용자 프로필

현재 Azure Virtual Desktop은 Azure AD 조인 VM의 로컬 프로필만 지원합니다.

## <a name="next-steps"></a>다음 단계

일부 Azure AD 조인 VM을 배포했습니다. 이제 지원되는 Azure Virtual Desktop 클라이언트에 로그인하여 사용자 세션의 일부로 테스트할 수 있습니다. 세션에 연결하는 방법은 다음 문서를 확인하세요.

- [Windows Desktop 클라이언트를 사용하여 연결](user-documentation/connect-windows-7-10.md)
- [웹 클라이언트를 사용하여 연결](user-documentation/connect-web.md)
- [Azure AD 조인 VM에 연결 문제 해결](troubleshoot-azure-ad-connections.md)
