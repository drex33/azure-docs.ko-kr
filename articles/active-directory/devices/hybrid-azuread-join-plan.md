---
title: 하이브리드 Azure Active Directory 조인 계획 - Azure Active Directory
description: 하이브리드 Azure Active Directory 가입 디바이스를 구성하는 방법에 대해 알아봅니다.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 05/28/2021
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 30c0d0fa394c8b962206879a80d600987753f2f6
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/10/2021
ms.locfileid: "111953469"
---
# <a name="how-to-plan-your-hybrid-azure-active-directory-join-implementation"></a>방법: 하이브리드 Azure Active Directory 조인 구현 계획

사용자와 마찬가지로, 디바이스는 보호가 필요한 또 다른 핵심 ID이며, 언제 어디서나 디바이스를 사용하여 리소스를 보호해야 합니다. 다음 방법 중 하나를 사용하여 디바이스 ID를 Azure AD로 가져와서 관리하면 이 목표를 달성할 수 있습니다.

- Azure AD 조인
- 하이브리드 Azure AD 조인
- Azure AD 등록

Azure AD에 디바이스를 가져오면 클라우드와 온-프레미스 리소스에서 SSO(Single Sign-On)를 통해 사용자의 생산성을 극대화할 수 있습니다. 이와 동시에 [조건부 액세스](../conditional-access/overview.md)를 사용하여 클라우드 및 온-프레미스 리소스에 대한 액세스를 보호할 수 있습니다.

온-프레미스 Active Directory(AD) 환경을 사용하고, Azure AD에 AD 도메인 조인 컴퓨터를 연결하려는 경우 하이브리드 Azure AD 조인으로 이를 수행할 수 있습니다. 이 문서에서는 사용자 환경에서 하이브리드 Azure AD 조인을 구현하는 데 관련된 단계를 제공합니다. 

## <a name="prerequisites"></a>필수 구성 요소

이 문서에서는 사용자가 [Azure Active Directory의 디바이스 ID 관리 소개](./overview.md)를 잘 알고 있다고 가정합니다.

> [!NOTE]
> Windows 10 하이브리드 Azure AD 조인에 필요한 최소 도메인 컨트롤러 버전은 Windows Server 2008 R2입니다.

## <a name="plan-your-implementation"></a>구현 계획

하이브리드 Azure AD 구현을 계획하려면 다음 사항을 숙지해야 합니다.

> [!div class="checklist"]
> - 지원되는 디바이스 검토
> - 알아야 할 사항 검토
> - 하이브리드 Azure AD 조인의 제어된 유효성 검사 검토
> - ID 인프라에 따라 시나리오 선택
> - 하이브리드 Azure AD 조인에 대한 온-프레미스 AD UPN 지원 검토

## <a name="review-supported-devices"></a>지원되는 디바이스 검토

하이브리드 Azure AD 조인은 광범위한 Windows 디바이스를 지원합니다. 이전 버전의 Windows를 실행하는 디바이스에 대한 구성에는 추가 또는 다른 단계가 필요하므로 지원되는 디바이스는 두 가지 범주로 그룹화됩니다.

### <a name="windows-current-devices"></a>Windows 현재 디바이스

- Windows 10
- Windows Server 2016
  - **참고**: Azure 국가별 클라우드 고객은 버전 1803이 필요합니다.
- Windows Server 2019

Windows 데스크톱 운영 체제를 실행하는 디바이스의 경우 지원되는 버전은 [Windows 10 릴리스 정보](/windows/release-information/) 문서에 나열되어 있습니다. 모범 사례로 Microsoft는 최신 버전의 Windows 10으로 업그레이드 하는 것을 권장합니다.

### <a name="windows-down-level-devices"></a>Windows 하위 수준 디바이스

- Windows 8.1
- Windows 7 지원은 2020년 1월 14일에 종료되었습니다. 자세한 내용은 [Windows 7에 대한 지원이 종료됨](https://support.microsoft.com/en-us/help/4057281/windows-7-support-ended-on-january-14-2020)을 참조하세요.
- Windows Server 2012 R2
- Windows Server 2012
- Windows Server 2008 R2. Windows Server 2008 및 2008 R2에 대한 지원 정보는 [Windows Server 2008 지원 종료 대비](https://www.microsoft.com/cloud-platform/windows-server-2008)를 참조하세요.

첫 번째 계획 단계에서는 사용자 환경을 검토하고 Windows 하위 수준 디바이스를 지원해야 하는지 여부를 결정해야 합니다.

## <a name="review-things-you-should-know"></a>알아야 할 사항 검토

### <a name="unsupported-scenarios"></a>지원되지 않는 시나리오

- 도메인 컨트롤러(DC) 역할을 실행하는 Windows Server에서는 하이브리드 Azure AD 조인이 지원되지 않습니다.

- 자격 증명 로밍 또는 사용자 프로필 로밍 또는 필수 프로필을 사용하는 경우에는 Windows 하위 수준 디바이스에서 하이브리드 Azure AD 조인이 지원되지 않습니다.

- Server Core OS는 어떤 유형의 디바이스 등록도 지원하지 않습니다.

- USMT(사용자 상태 마이그레이션 도구)에서는 디바이스 등록이 작동하지 않습니다.  

### <a name="os-imaging-considerations"></a>OS 이미징 고려 사항

- 시스템 준비 도구(Sysprep)를 사용하고 설치를 위해 **Windows 10 1809 이전** 이미지를 사용하는 경우 이미지가 Azure AD에 이미 하이브리드 Azure AD 조인으로 등록된 디바이스에서 만들어지지 않았는지 확인합니다.

- VM(가상 머신) 스냅샷을 사용하여 추가 VM을 만드는 경우, 스냅샷이 Azure AD에 이미 하이브리드 Azure AD 조인으로 등록된 VM에서 만들어지지 않았는지 확인합니다.

- 다시 부팅 시 디스크에 대한 변경 내용을 지우는 [통합 쓰기 필터](/windows-hardware/customize/enterprise/unified-write-filter)와 유사한 기술을 사용하는 경우 이 기술은 디바이스가 하이브리드 Azure AD에 조인된 후에 적용되어야 합니다. 하이브리드 Azure AD 조인을 완료하기 전에 해당 기술을 사용하도록 설정하면 디바이스가 다시 부팅될 때마다 조인이 해제됩니다.

### <a name="handling-devices-with-azure-ad-registered-state"></a>디바이스에서 Azure AD 등록 상태 처리

Windows 10 도메인 조인 디바이스가 테넌트에 [등록된 Azure AD](overview.md#getting-devices-in-azure-ad)인 경우 하이브리드 Azure AD 조인 및 Azure AD 등록 디바이스의 이중 상태로 이어질 수 있습니다. 이 시나리오를 자동으로 해결하려면 Windows 10 1803(KB4489894 적용) 이상으로 업그레이드하는 것이 좋습니다. 1803 이전 릴리스에서는 하이브리드 Azure AD 조인을 사용하도록 설정하기 전에 Azure AD 등록 상태를 수동으로 제거해야 합니다. 1803 이상 릴리스에서는 이중 상태를 방지하기 위해 다음과 같은 변경 내용이 적용되었습니다.

- 사용자에 대한 기존 Azure AD 등록 상태는 <i>디바이스가 하이브리드 Azure AD에 조인되고 동일한 사용자가 로그인한 후</i> 자동으로 제거됩니다. 예를 들어 디바이스에 사용자 A의 Azure AD 등록 상태가 있는 경우 사용자 A가 디바이스에 로그인할 때만 사용자 A에 대한 이중 상태가 정리됩니다. 동일한 디바이스에 여러 사용자가 있는 경우 해당하는 사용자가 로그인하면 이중 상태가 개별적으로 정리됩니다. Azure AD 등록 상태를 제거하는 것 외에도, 자동 등록을 통해 Azure AD 등록의 일부로 등록을 수행하는 경우 Windows 10은 Intune 또는 다른 MDM에서 디바이스 등록을 취소합니다.
- 디바이스의 로컬 계정에 대한 Azure AD 등록 상태는 해당 변경의 영향을 받지 않습니다. 도메인 계정에만 적용됩니다. 사용자가 도메인 사용자가 아니므로 로컬 계정에 대한 Azure AD 등록 상태는 사용자 로그온 후에도 자동으로 제거되지 않습니다. 
- HKLM\SOFTWARE\Policies\Microsoft\Windows\WorkplaceJoin: "BlockAADWorkplaceJoin"=dword:00000001 레지스트리 값을 추가하여 도메인 조인 디바이스가 Azure AD에 등록되지 않도록 방지할 수 있습니다.
- Windows 10 1803에서 비즈니스용 Windows Hello를 구성한 경우 사용자는 이중 상태 정리 후 비즈니스용 Windows Hello를 다시 설정해야 합니다. 이 문제는 KB4512509를 통해 해결되었습니다.

> [!NOTE]
> Windows 10이 Azure AD 등록 상태를 로컬에서 자동으로 제거하더라도 Azure AD의 디바이스 개체는 Intune에 의해 관리되는 경우 즉시 삭제되지 않습니다. dsregcmd /status를 실행하여 Azure AD 등록 상태 제거를 확인하고 이를 바탕으로 디바이스가 Azure AD가 등록되지 않도록 고려할 수 있습니다.

### <a name="hybrid-azure-ad-join-for-single-forest-multiple-azure-ad-tenants"></a>단일 포리스트, 여러 Azure AD 테넌트용 하이브리드 Azure AD 조인

디바이스를 각 테넌트에 대한 하이브리드 Azure AD 조인으로 등록하려면 조직은 SCP 구성이 AD가 아닌 디바이스에서 수행되도록 해야 합니다. 이를 수행하는 방법에 대한 자세한 내용은 [하이브리드 Azure AD 조인의 제어된 유효성 검사](hybrid-azuread-join-control.md) 문서에서 확인할 수 있습니다. 조직에서는 특정 Azure AD 기능이 단일 포리스트, 여러 Azure AD 테넌트 구성에서 작동하지 않는다는 것을 이해하는 것도 중요합니다.
- [디바이스 쓰기 저장](../hybrid/how-to-connect-device-writeback.md)이 작동하지 않습니다. 이는 [ADFS를 사용하여 페더레이션된 온-프레미스 앱에 대한 디바이스 기반 조건부 액세스](/windows-server/identity/ad-fs/operations/configure-device-based-conditional-access-on-premises)에 영향을 줍니다. 이는 하이브리드 [인증서 신뢰 모델을 사용할 때 비즈니스용 Windows Hello 배포](/windows/security/identity-protection/hello-for-business/hello-hybrid-cert-trust)에도 영향을 줍니다.
- [그룹 쓰기 저장](../hybrid/how-to-connect-group-writeback.md)이 작동하지 않습니다. 이는 Exchange가 설치된 포리스트에 대한 Office 365 그룹의 쓰기 저장에 영향을 줍니다.
- [Seamless SSO](../hybrid/how-to-connect-sso.md)가 작동하지 않습니다. 이는 조직에서 OS/broowser 플랫폼(예: Windows 10 확장이 없는 Firefox, Safari, Chrome을 사용하는 iOS/Linux) 간에 사용할 수 있는 SSO 시나리오에 영향을 미칩니다.
- [관리되는 환경에서 Windows 하위 수준 디바이스에 대한 하이브리드 Azure AD 조인](./hybrid-azuread-join-managed-domains.md#enable-windows-down-level-devices)이 작동하지 않습니다. 예를 들어 관리되는 환경에서 Windows Server 2012 R2의 하이브리드 Azure AD 조인에는 Seamless SSO가 필요하며 Seamless SSO가 작동하지 않으므로 이러한 설정에 대한 하이브리드 Azure AD 조인은 작동하지 않습니다.
- [온-프레미스 Azure AD 암호 보호](../authentication/concept-password-ban-bad-on-premises.md)가 작동하지 않습니다. 이는 Azure AD에 저장된 것과 동일한 글로벌 및 사용자 지정 금지 암호 목록을 사용하여 온-프레미스 AD DS(Active Directory Domain Services) 도메인 컨트롤러에 대해 암호 변경 및 암호 재설정 이벤트를 수행하는 기능에 영향을 줍니다.


### <a name="additional-considerations"></a>기타 고려 사항

- 사용자 환경에서 VDI(가상 데스크톱 인프라)를 사용하는 경우 [디바이스 ID 및 데스크톱 가상화](./howto-device-identity-virtual-desktop-infrastructure.md)를 참조하세요.

- 하이브리드 Azure AD 조인은 FIPS 규격 TPM 2.0에 대해 지원되며 TPM 1.2에 대해서는 지원되지 않습니다. 디바이스에 FIPS 규격 TPM 1.2가 있는 경우 하이브리드 Azure AD 조인을 진행하기 전에 디바이스를 사용하지 않도록 설정해야 합니다. TPM은 TPM 제조업체에 따라 다르므로 Microsoft에서 FIPS 모드를 사용하지 않도록 설정하는 도구를 제공하지 않습니다. 하드웨어 OEM에 지원을 문의하세요. 

- Windows 10 1903 릴리스부터는 TPM 1.2가 하이브리드 Azure AD 조인과 함께 사용되지 않으며 해당 TPM이 있는 디바이스는 TPM이 없는 것으로 간주됩니다.

- UPN 변경은 Windows 10 2004 업데이트를 시작하는 경우에만 지원됩니다. Windows 10 2004 업데이트 이전 디바이스의 경우 사용자 디바이스에서 SSO 및 조건부 액세스 문제가 발생할 수 있습니다. 이 문제를 해결하려면 Azure AD에서 디바이스 조인을 취소한 다음(상승된 권한으로 "dsregcmd /leave" 실행) 다시 조인(자동으로 진행됨)해야 합니다. 하지만 비즈니스용 Windows Hello를 통해 로그인하는 사용자에게는 이 문제가 발생하지 않습니다.

## <a name="review-controlled-validation-of-hybrid-azure-ad-join"></a>하이브리드 Azure AD 조인의 제어된 유효성 검사 검토

모든 필수 구성 요소가 준비되면 Windows 디바이스가 Azure AD 테넌트의 디바이스로 자동 등록됩니다. Azure AD에서 해당 디바이스 ID 상태를 하이브리드 Azure AD 조인이라고 합니다. 이 문서에서 다루는 개념에 대한 자세한 내용은 [Azure Active Directory의 디바이스 ID 관리 소개](overview.md) 문서에서 찾을 수 있습니다.

조직에서는 조직 전체를 대상으로 한 번에 사용하도록 설정하기 전에 하이브리드 Azure AD 조인에 대해 제어된 유효성 검사를 수행하는 것이 좋습니다. 수행 방법을 알아보려면 [하이브리드 Azure AD 조인에 대한 제어된 유효성 검사](hybrid-azuread-join-control.md) 문서를 검토하세요.

## <a name="select-your-scenario-based-on-your-identity-infrastructure"></a>ID 인프라에 따라 시나리오 선택

하이브리드 Azure AD 조인은 UPN을 라우팅할 수 있는지에 따라 관리형 환경과 페더레이션된 환경 모두에서 작동합니다. 지원되는 시나리오에 대한 표는 페이지 하단을 참조하세요.  

### <a name="managed-environment"></a>관리 환경

관리형 환경은 [Seamless Single Sign On](../hybrid/how-to-connect-sso.md)을 사용하여 [PHS(암호 해시 동기화)](../hybrid/whatis-phs.md) 또는 [PTA(통과 인증)](../hybrid/how-to-connect-pta.md)를 통해 배포할 수 있습니다.

이러한 시나리오는 인증용 페더레이션 서버를 구성할 필요가 없습니다.

> [!NOTE]
> [단계적 롤아웃을 사용한 클라우드 인증](../hybrid/how-to-connect-staged-rollout.md)은 Windows 10 1903 업데이트를 시작하는 경우에만 지원됩니다.

### <a name="federated-environment"></a>페더레이션 환경

페더레이션 환경은 다음 요구 사항을 지원하는 ID 공급자가 있어야 합니다. AD FS(Active Directory Federation Services)를 사용하는 페더레이션된 환경을 사용하는 경우에는 아래 요구 사항이 이미 지원됩니다.

- **WIAORMULTIAUTHN 클레임:** 이 클레임은 Windows 하위 수준 디바이스의 하이브리드 Azure AD 조인을 수행하는 데 필요합니다.
- **WS-Trust 프로토콜:** 이 프로토콜은 Azure AD를 사용하여 현재 Windows 하이브리드 Azure AD 조인 디바이스를 인증하는 데 필요합니다. AD FS를 사용하는 경우 다음 WS-Trust 엔드포인트를 사용하도록 설정해야 합니다. `/adfs/services/trust/2005/windowstransport`  
`/adfs/services/trust/13/windowstransport`  
  `/adfs/services/trust/2005/usernamemixed` 
  `/adfs/services/trust/13/usernamemixed`
  `/adfs/services/trust/2005/certificatemixed` 
  `/adfs/services/trust/13/certificatemixed` 

> [!WARNING] 
> **adfs/services/trust/2005/windowstransport** 또는 **adfs/services/trust/13/windowstransport** 는 모두 인트라넷 연결 엔드포인트로만 사용하도록 설정해야 하며 웹 애플리케이션 프록시를 통해 엑스트라넷 연결 엔드포인트로 노출되어서는 안됩니다. WS-Trust Windows 엔드포인트를 비활성화는 방법에 대해 자세히 알아보려면 [프록시에서 WS-Trust Windows 엔드포인트 사용 안 함](/windows-server/identity/ad-fs/deployment/best-practices-securing-ad-fs#disable-ws-trust-windows-endpoints-on-the-proxy-ie-from-extranet)을 참조하세요. **서비스** > **엔드포인트** 에서 AD FS 관리 콘솔을 통해 어떤 엔드포인트가 사용하도록 설정되었는지 확인할 수 있습니다.

> [!NOTE]
> Azure AD는 관리형 도메인에서 스마트 카드나 인증서를 지원하지 않습니다.

버전 1.1.819.0부터 Azure AD Connect는 하이브리드 Azure AD 조인을 구성하는 마법사를 제공합니다. 마법사를 사용하면 구성 프로세스를 크게 간소화할 수 있습니다. 필요한 버전의 Azure AD Connect를 설치할 수 없는 경우 [디바이스 등록을 수동으로 구성하는 방법](hybrid-azuread-join-manual.md)을 참조하세요. 

ID 인프라와 일치하는 시나리오에 따라 다음을 참조하세요.

- [페더레이션된 환경에 대한 하이브리드 Azure Active Directory 조인 구성](hybrid-azuread-join-federated-domains.md)
- [관리형 환경에 대한 하이브리드 Azure Active Directory 조인 구성](hybrid-azuread-join-managed-domains.md)

## <a name="review-on-premises-ad-users-upn-support-for-hybrid-azure-ad-join"></a>하이브리드 Azure AD 조인에 대한 온-프레미스 AD 사용자 UPN 지원 검토

경우에 따라 온-프레미스 AD 사용자 UPN이 Azure AD UPN과 다를 수 있습니다. 그러한 경우 Windows 10 하이브리드 Azure AD 조인은 [인증 방법](../hybrid/choose-ad-authn.md), 도메인 유형 및 Windows 10 버전을 기반으로 온-프레미스 AD UPN에 대한 제한된 지원을 제공합니다. 사용자 환경에 있을 수 있는 온-프레미스 AD UPN에는 두 가지 유형이 있습니다.

- 라우팅 가능 사용자 UPN: 라우팅 가능 UPN에는 도메인 등록 기관에 등록되어 유효한 것으로 확인된 도메인이 있습니다. 예를 들어 contoso.com이 Azure AD의 기본 도메인인 경우 contoso.org는 Contoso에서 소유하고 [Azure AD에서 확인](../fundamentals/add-custom-domain.md)된 기본 도메인입니다.
- 라우팅 불가 사용자 UPN: 라우팅 불가 UPN에는 확인된 도메인이 없습니다. 조직의 사설망 내에서만 적용됩니다. 예를 들어 contoso.com이 Azure AD의 기본 도메인인 경우 contoso.com은 온-프레미스 AD의 기본 도에미인이지만 인터넷에서 확인할 수 없는 도메인이며 Consoso의 네트워크 내에서만 사용됩니다.

> [!NOTE]
> 이 섹션의 정보는 온-프레미스 사용자 UPN에만 적용됩니다. 온-프레미스 컴퓨터 도메인 접미사(예: computer1.contoso.local)에는 적용되지 않습니다.

아래 표에서는 Windows 10 하이브리드 Azure AD 조인에서 이러한 온-프레미스 AD UPN에 대한 지원을 자세히 설명합니다.

| 온-프레미스 AD UPN 유형 | 도메인 유형 | Windows 10 버전 | Description |
| ----- | ----- | ----- | ----- |
| 라우팅 가능 | 페더레이션 | 1703 릴리스 | 일반 공급 |
| 라우팅 불가능 | 페더레이션 | 1803 릴리스 | 일반 공급 |
| 라우팅 가능 | 관리 | 1803 릴리스 | 일반적으로 사용할 수 있는 Windows 잠금 화면의 Azure AD SSPR은 지원되지 않습니다. 온-프레미스 UPN은 `onPremisesUserPrincipalName` Azure AD의 특성과 동기화되어야 합니다. |
| 라우팅 불가능 | 관리 | 지원되지 않음 | |

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [페더레이션된 환경에 대한 하이브리드 Azure Active Directory 조인 구성](hybrid-azuread-join-federated-domains.md)
> [관리형 환경에 대한 Azure Active Directory 조인 구성](hybrid-azuread-join-managed-domains.md)

<!--Image references-->
[1]: ./media/hybrid-azuread-join-plan/12.png