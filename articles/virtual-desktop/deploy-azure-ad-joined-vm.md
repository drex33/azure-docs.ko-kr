---
title: Azure Virtual Desktop에서 Azure AD 조인 VM 배포 - Azure
description: Azure Virtual Desktop에서 Azure AD 조인 VM을 구성하고 배포하는 방법
services: virtual-desktop
author: Heidilohr
manager: lizross
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 09/15/2021
ms.author: helohr
ms.openlocfilehash: e6325c6511c6df9c3f3c021bc24a3f66b2e56c0f
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/29/2021
ms.locfileid: "129207738"
---
# <a name="deploy-azure-ad-joined-virtual-machines-in-azure-virtual-desktop"></a>Azure 가상 데스크톱에서 Azure AD에 가입 된 가상 머신 배포

이 문서에서는 Azure Virtual Desktop에서 Azure Active Directory 조인 가상 머신을 배포하고 액세스하는 프로세스를 설명합니다. Azure AD에 가입 된 Vm은 VM에서 온-프레미스 또는 가상화 된 Active Directory 도메인 컨트롤러 (DC)로의 회선이 나 azure AD Domain services (Azure AD DS)를 배포 하는 데 필요한 요구를 제거 합니다. 일부 경우에는 DC에 대한 요구 사항을 완전히 제거하여 환경의 배포와 관리를 간소화할 수 있습니다. 관리가 용이하도록 이러한 VM이 자동으로 Intune에 등록될 수도 있습니다.

> [!NOTE]
> Azure AD에 가입 된 Vm은 현재 Azure 상용 클라우드에서만 지원 됩니다.

## <a name="supported-configurations"></a>지원되는 구성

현재 Azure AD 조인 VM에서 다음 구성이 지원됩니다.

- 로컬 사용자 프로필이 있는 개인 데스크톱
- 점프 상자로 사용되는 풀링된 데스크톱. 이 구성에서 사용자는 네트워크의 다른 PC에 연결하기 전에 Azure Virtual Desktop VM에 액세스합니다. 사용자는 VM에 데이터를 저장하면 안 됩니다.
- 사용자가 VM에 데이터를 저장할 필요가 없는 풀링된 데스크톱이나 앱. 예를 들어 데이터를 온라인으로 저장하거나 원격 데이터베이스에 연결하는 애플리케이션의 경우입니다.

사용자 계정은 동일한 Azure AD 테넌트에서 클라우드 전용 또는 하이브리드 사용자가 될 수 있습니다.

## <a name="known-limitations"></a>알려진 제한 사항

다음과 같은 알려진 제한 사항은 온-프레미스 또는 Active Directory 도메인에 가입 된 리소스에 대 한 액세스에 영향을 줄 수 있으며, Azure AD 조인 Vm이 사용자 환경에 적합 한지 여부를 결정할 때 고려해 야 합니다. 현재 사용자가 클라우드 기반 리소스나 Azure AD 기반 인증에만 액세스 해야 하는 시나리오에는 Azure AD 조인 Vm을 사용 하는 것이 좋습니다.

- Azure 가상 데스크톱 (클래식)은 Azure AD에 가입 된 Vm을 지원 하지 않습니다.
- Azure AD에 가입 된 Vm은 현재 외부 사용자를 지원 하지 않습니다.
- Azure AD에 가입 된 Vm은 현재 로컬 사용자 프로필만 지원 합니다.
- Azure AD에 가입 된 Vm은 FSLogix 또는 MSIX 앱 연결에 대 한 Azure Files 파일 공유에 액세스할 수 없습니다. 이러한 기능 중 하나에 액세스 하려면 Kerberos 인증이 필요 합니다.
- Windows 저장소 클라이언트는 현재 Azure AD에 가입 된 vm을 지원 하지 않습니다.
- 현재 Azure Virtual Desktop은 Azure AD 조인 VM에 대한 Single Sign-On을 지원하지 않습니다.

## <a name="deploy-azure-ad-joined-vms"></a>Azure AD 조인 VM 배포

[새 호스트 풀을 만들거나](create-host-pools-azure-marketplace.md) [기존 호스트 풀을 확장](expand-existing-host-pool.md)할 때 Azure Portal에서 직접 Azure AD 조인 VM을 배포할 수 있습니다. Virtual Machines 탭에서 VM을 Active Directory 또는 Azure Active Directory에 조인할지 여부를 선택합니다. **Azure Active Directory** 를 선택하면 자동으로 **Intune에 VM을 등록** 할 수 있는 옵션이 제공되므로 [Windows 10 Enterprise](/mem/intune/fundamentals/windows-virtual-desktop) 및 [Windows 10 Enterprise 다중 세션](/mem/intune/fundamentals/windows-virtual-desktop-multi-session) VM을 간편하게 관리할 수 있습니다. Azure Active Directory 옵션은 VM을 구독과 동일한 Azure AD 테넌트에 조인합니다.

> [!NOTE]
> - 호스트 풀에는 동일한 도메인 가입 유형의 VM만 포함되어야 합니다. 예를 들어 AD 조인 VM은 다른 AD VM에만 있어야 하고 그 반대의 경우도 마찬가지입니다.
> - 호스트 풀 VM은 Windows 10 단일 세션 또는 다중 세션 버전 2004 이상이어야 합니다.

### <a name="assign-user-access-to-host-pools"></a>호스트 풀에 사용자 액세스 할당

호스트 풀을 만든 후 사용자가 해당 리소스에 액세스할 수 있도록 사용자에 게 액세스 권한을 할당 해야 합니다. 리소스에 대 한 액세스 권한을 부여 하려면 각 사용자를 앱 그룹에 추가 합니다. [앱 그룹 관리](manage-app-groups.md)의 지침을 수행하여 앱과 데스크톱에 대한 사용자 액세스 권한을 할당합니다. 가능하면 개별 사용자 대신 사용자 그룹을 사용하는 것이 좋습니다.

Azure AD에 가입 된 vm의 경우 Active Directory 또는 Azure Active Directory 도메인 서비스 기반 배포에 대 한 요구 사항에 따라 두 가지 추가 작업을 수행 해야 합니다.  

- 사용자가 Vm에 로그인 할 수 있도록 **가상 컴퓨터 사용자 로그인** 역할을 할당 합니다.
- 로컬 관리자 권한이 필요한 관리자에 게 **가상 컴퓨터 관리자 로그인** 역할을 할당 합니다.

사용자에게 Azure AD 조인 VM에 대한 액세스 권한을 부여하려면 [VM에 대한 역할 할당을 구성](../active-directory/devices/howto-vm-sign-in-azure-ad-windows.md#configure-role-assignments-for-the-vm)해야 합니다. VM, VM이 포함된 리소스 그룹 또는 구독 중 하나에 **Virtual Machine 사용자 로그인** 이나 **Virtual Machine 관리자 로그인** 역할을 할당할 수 있습니다. 호스트 풀의 모든 VM에 적용되게 하려면 리소스 그룹 수준에서 앱 그룹에 사용한 사용자 그룹과 동일한 사용자 그룹에 Virtual Machine 사용자 로그인 역할을 할당하는 것이 좋습니다.

## <a name="access-azure-ad-joined-vms"></a>Azure AD 조인 VM에 액세스

이 섹션에서는 다양한 Azure Virtual Desktop 클라이언트에서 Azure AD 조인 VM에 액세스하는 방법을 설명합니다.

### <a name="connect-using-the-windows-desktop-client"></a>Windows 데스크톱 클라이언트를 사용하여 연결

기본 구성에서는 [Windows 데스크톱 클라이언트](user-documentation/connect-windows-7-10.md)를 사용하여 Windows 10에서 연결을 지원합니다. 자격 증명, 스마트 카드, [비즈니스용 Windows Hello 인증서 신뢰](/windows/security/identity-protection/hello-for-business/hello-hybrid-cert-trust) 또는 [비즈니스용 Windows Hello 키 신뢰(인증서 포함)](/windows/security/identity-protection/hello-for-business/hello-deployment-rdp-certs)를 사용하여 세션 호스트에 로그인할 수 있습니다. 그러나 세션 호스트에 액세스하려면 로컬 PC가 다음 조건 중 하나를 충족해야 합니다.

- 로컬 PC는 세션 호스트와 동일한 Azure AD 테넌트에 조인된 Azure AD입니다.
- 로컬 PC는 세션 호스트와 동일한 Azure AD 테넌트에 조인된 하이브리드 Azure AD입니다.
- 로컬 PC가 Windows 10 버전 2004 이상에서 실행되고 있으며 세션 호스트와 동일한 Azure AD 테넌트에 등록된 Azure AD입니다.

Azure AD에 조인되지 않은 Windows 디바이스에서 액세스를 사용하려면 **targetisaadjoined:i:1** 을 [사용자 지정 RDP 속성](customize-rdp-properties.md)으로 호스트 풀에 추가합니다. 이러한 연결은 세션 호스트에 로그인할 때 사용자 이름과 암호 자격 증명 입력으로 제한됩니다.

### <a name="connect-using-the-other-clients"></a>다른 클라이언트를 사용하여 연결

웹, Android, macOS 및 iOS 클라이언트를 사용하여 Azure AD 조인 VM에 액세스하려면 **targetisaadjoined:i:1** 을 [사용자 지정 RDP 속성](customize-rdp-properties.md)으로 호스트 풀에 추가합니다. 이러한 연결은 세션 호스트에 로그인할 때 사용자 이름과 암호 자격 증명 입력으로 제한됩니다.

### <a name="enabling-mfa-for-azure-ad-joined-vms"></a>Azure AD 조인 VM에 MFA 사용

Azure 가상 데스크톱 앱에 대 한 조건부 액세스 정책을 설정 하 여 Azure AD에 가입 된 Vm에 대해 [다단계 인증](set-up-mfa.md) 을 사용 하도록 설정할 수 있습니다. 연결에 성공 하려면 [레거시 사용자별 다단계 인증을 사용 하지 않도록 설정](../active-directory/devices/howto-vm-sign-in-azure-ad-windows.md#mfa-sign-in-method-required)해야 합니다. 비즈니스용 Windows Hello와 같은 강력한 인증 방법에 대한 로그인을 제한하지 않으려면 조건부 액세스 정책에서 [Azure Windows VM 로그인 앱](../active-directory/devices/howto-vm-sign-in-azure-ad-windows.md#mfa-sign-in-method-required)을 제외해야 합니다.

## <a name="user-profiles"></a>사용자 프로필

현재 Azure Virtual Desktop은 Azure AD 조인 VM의 로컬 프로필만 지원합니다.

## <a name="next-steps"></a>다음 단계

일부 Azure AD 조인 VM을 배포했습니다. 이제 지원되는 Azure Virtual Desktop 클라이언트에 로그인하여 사용자 세션의 일부로 테스트할 수 있습니다. 세션에 연결하는 방법은 다음 문서를 확인하세요.

- [Windows Desktop 클라이언트를 사용하여 연결](user-documentation/connect-windows-7-10.md)
- [웹 클라이언트를 사용하여 연결](user-documentation/connect-web.md)
- [Azure AD 조인 VM에 연결 문제 해결](troubleshoot-azure-ad-connections.md)
