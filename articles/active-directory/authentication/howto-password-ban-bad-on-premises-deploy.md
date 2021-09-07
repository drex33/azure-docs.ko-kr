---
title: 온-프레미스 Azure AD 암호 보호 배포
description: 온-프레미스 Active Directory Domain Services 환경에서 Azure AD 암호 보호를 계획하고 배포하는 방법을 알아봅니다.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 03/05/2020
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: 81e40a295d9572fb38a1289d4bdf661095b9ca7a
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/22/2021
ms.locfileid: "114449126"
---
# <a name="plan-and-deploy-on-premises-azure-active-directory-password-protection"></a>온-프레미스 Azure Active Directory 암호 보호 계획 및 배포

사용자가 학교, 스포츠 팀 또는 유명 인사와 같은 일반적인 로컬 단어를 사용하는 암호를 만드는 경우가 많습니다. 이러한 암호는 쉽게 추측할 수 있으며 사전 기반 공격에 취약합니다. 조직에서 강력한 암호를 적용하기 위해 Azure AD(Azure Active Directory) 암호 보호는 전체 및 사용자 지정 금지 암호 목록을 제공합니다. 해당 금지 암호 목록에 일치하는 항목이 있으면 암호 변경 요청이 실패합니다.

온-프레미스 AD DS(Active Directory Domain Services) 환경을 보호하기 위해 온-프레미스 DC에서 작동하도록 Azure AD 암호 보호를 설치하고 구성할 수 있습니다. 이 문서에서는 온-프레미스 환경에서 Azure AD 암호 보호 프록시 서비스 및 Azure AD 암호 보호 DC 에이전트를 설치하고 등록하는 방법을 보여 줍니다.

온-프레미스 환경에서 Azure AD 암호 보호가 작동하는 방법에 대한 자세한 내용은 [Azure AD 암호 보호를 Windows Server Active Directory에 적용하는 방법](concept-password-ban-bad-on-premises.md)을 참조하세요.

## <a name="deployment-strategy"></a>배포 전략

다음 다이어그램은 Azure AD 암호 보호의 기본 구성 요소가 온-프레미스 Active Directory 환경에서 함께 작동하는 방식을 보여 줍니다.

![Azure AD 암호 보호 구성 요소가 함께 작동하는 방식](./media/concept-password-ban-bad-on-premises/azure-ad-password-protection.png)

소프트웨어를 배포하기 전에 소프트웨어의 작동 방식을 검토하는 것이 좋습니다. 자세한 내용은 [Azure AD 암호 보호에 대한 개념적 개요](concept-password-ban-bad-on-premises.md)를 참조하세요.

‘감사’ 모드에서 배포를 시작하는 것이 좋습니다. 감사 모드는 기본 초기 설정이며, 여기에서 암호를 계속 설정할 수 있습니다. 차단되는 암호는 이벤트 로그에 기록됩니다. 감사 모드에서 프록시 서버 및 DC 에이전트를 배포한 후 정책이 적용될 때 암호 정책이 사용자에게 미치는 영향을 모니터링합니다.

감사 스테이지 중에 많은 조직은 다음 상황이 적용되는 것을 알게 됩니다.

* 더 안전한 암호를 사용하도록 기존 운영 프로세스를 개선해야 합니다.
* 사용자들이 종종 보안되지 않은 암호를 사용합니다.
* 보안 적용의 향후 변경, 사용자에게 미칠 수 있는 영향, 더 안전한 암호를 선택하는 방법을 사용자에게 알려야 합니다.

더 강력한 암호 유효성 검사가 기존 Active Directory 도메인 컨트롤러 배포 자동화에 영향을 줄 수도 있습니다. 이러한 문제를 해결하는 데 도움이 되도록 감사 기간 평가 중에 한 번 이상의 DC 수준 올리기 및 한 번의 DC 수준 내리기를 수행하는 것이 좋습니다. 자세한 내용은 다음 문서를 참조하세요.

* [Ntdsutil.exe에서 약한 디렉터리 서비스 복구 모드 암호를 설정하지 못함](howto-password-ban-bad-on-premises-troubleshoot.md#ntdsutilexe-fails-to-set-a-weak-dsrm-password)
* [약한 디렉터리 서비스 복구 모드 암호로 인해 도메인 컨트롤러 복제본 수준 올리기가 실패함](howto-password-ban-bad-on-premises-troubleshoot.md#domain-controller-replica-promotion-fails-because-of-a-weak-dsrm-password)
* [약한 로컬 관리자 암호로 인해 도메인 컨트롤러 수준 내리기가 실패함](howto-password-ban-bad-on-premises-troubleshoot.md#domain-controller-demotion-fails-due-to-a-weak-local-administrator-password)

기능이 적절한 기간 동안 감사 모드에서 실행된 후에 ‘감사’에서 ‘적용’으로 구성을 전환하여 더 안전한 암호를 요구할 수 있습니다. 이 시간 동안 추가로 모니터링하는 것이 좋습니다.

Azure AD 암호 보호는 암호 변경 또는 설정 작업 중에만 암호의 유효성을 검사할 수 있다는 점에 유의해야 합니다. Azure AD 암호 보호를 배포하기 전에 Active Directory에서 수락되고 저장된 암호는 유효성이 검사되지 않으며 있는 그대로 계속 작동합니다. 시간이 지남에 따라 기존 암호가 정상적으로 만료되므로 결국에는 모든 사용자 및 계정에서 Azure AD 암호 보호로 유효성이 검사된 암호를 사용하기 시작합니다. “암호 사용 기간 제한 없음“으로 구성된 계정은 해당 설정에서 제외됩니다.

### <a name="multiple-forest-considerations"></a>여러 포리스트 고려 사항

Azure AD 암호 보호를 여러 포리스트에 배포하기 위한 추가 요구 사항은 없습니다.

각 포리스트는 [온-프레미스 Azure AD 암호 보호를 배포](#download-required-software)하기 위해 다음 섹션에서 설명한 대로 독립적으로 구성됩니다. 각 Azure AD 암호 보호 프록시는 조인되는 포리스트의 도메인 컨트롤러만 지원할 수 있습니다.

포리스트의 Azure AD 암호 보호 소프트웨어는 Active Directory 신뢰 구성과 관계없이 다른 포리스트에 배포된 암호 보호 소프트웨어를 인식하지 못합니다.

### <a name="read-only-domain-controller-considerations"></a>읽기 전용 도메인 컨트롤러 고려 사항

암호 변경 또는 설정 이벤트는 RODC(읽기 전용 도메인 컨트롤러)에서 처리 및 유지되지 않습니다. 대신, 쓰기 가능 도메인 컨트롤러에 전달됩니다. Azure AD 암호 보호 DC 에이전트 소프트웨어를 RODC에 설치할 필요가 없습니다.

또한 읽기 전용 도메인 컨트롤러에서 Azure AD 암호 보호 프록시 서비스를 실행하는 기능은 지원되지 않습니다.

### <a name="high-availability-considerations"></a>고가용성 고려 사항

포리스트의 DC가 Azure에서 새 정책 또는 기타 데이터를 다운로드하려고 할 때 Azure AD 암호 보호 프록시 서버의 가용성은 암호 보호의 주요 관심사입니다. 각 Azure AD 암호 보호 DC 에이전트는 호출할 프록시 서버를 결정할 때 간단한 라운드 로빈 스타일 알고리즘을 사용합니다. 에이전트는 응답하지 않는 프록시 서버를 건너뜁니다.

디렉터리 및 sysvol 폴더 상태의 정상 복제를 포함하는 대부분의 완전 연결된 Active Directory 배포의 경우 두 개의 Azure AD 암호 보호 프록시 서버는 가용성을 충분히 보장합니다. 해당 구성으로 인해 새 정책 및 기타 데이터를 적시에 다운로드할 수 있습니다. 원하는 경우 추가 Azure AD 암호 보호 프록시 서버를 배포할 수 있습니다.

Azure AD 암호 보호 DC 에이전트 소프트웨어의 설계는 고가용성과 관련된 일반적인 문제를 완화합니다. Azure AD 암호 보호 DC 에이전트는 가장 최근에 다운로드한 암호 정책의 로컬 캐시를 유지 관리합니다. 등록된 모든 프록시 서버를 사용할 수 없게 되는 경우에도 Azure AD 암호 보호 DC 에이전트는 캐시된 암호 정책을 계속 적용합니다.

대규모 배포에서 암호 정책의 적절한 업데이트 빈도는 일반적으로 시간 이하 단위가 아니라 일 단위입니다. 따라서 프록시 서버를 잠시 중단해도 Azure AD 암호 보호에 크게 영향을 주지 않습니다.

## <a name="deployment-requirements"></a>배포 요구 사항

라이선스에 대한 자세한 내용은 [Azure AD 암호 보호 라이선스 요구 사항](concept-password-ban-bad.md#license-requirements)을 참조하세요.

다음 핵심 요구 사항이 적용됩니다.

* 도메인 컨트롤러를 포함하여 Azure AD 암호 보호 구성 요소가 설치된 모든 컴퓨터에는 Universal C 런타임이 설치되어 있어야 합니다.
    * Windows 업데이트에서 모든 업데이트가 있는지 확인하여 런타임을 가져올 수 있습니다. 또는 OS 특정 업데이트 패키지에서 가져올 수 있습니다. 자세한 내용은 [Windows에서 유니버설 C 런타임 업데이트](https://support.microsoft.com/help/2999226/update-for-uniersal-c-runtime-in-windows)를 참조하세요.
* Azure AD에 Windows Server Active Directory 포리스트를 등록하려면 포리스트 루트 도메인의 Active Directory 도메인 관리자 권한을 가진 계정이 필요합니다.
* Windows Server 2012 이상 버전을 실행하는 도메인의 모든 도메인 컨트롤러에서 키 배포 서비스를 사용하도록 설정해야 합니다. 기본적으로 해당 서비스는 수동 트리거 시작을 통해 사용하도록 설정됩니다.

* 각 도메인에 있는 하나 이상의 도메인 컨트롤러와 Azure AD 암호 보호를 위해 프록시 서비스를 호스트하는 하나 이상의 서버 간에 네트워크 연결이 설정되어 있어야 합니다. 해당 연결을 통해 도메인 컨트롤러가 프록시 서비스의 RPC 엔드포인트 매퍼 포트 135 및 RPC 서버 포트에 액세스할 수 있어야 합니다.
    * 기본적으로 RPC 서버 포트는 범위(49152 - 65535)에서 동적 RPC 포트이지만 [정적 포트를 사용](#static)하도록 구성할 수 있습니다.
* Azure AD 암호 보호 프록시 서비스를 설치할 모든 컴퓨터에는 다음 엔드포인트에 대한 네트워크 액세스 권한이 있어야 합니다.

    |**엔드포인트**|**용도**|
    | --- | --- |
    |`https://login.microsoftonline.com`|인증 요청|
    |`https://enterpriseregistration.windows.net`|Azure AD 암호 보호 기능|

### <a name="azure-ad-password-protection-dc-agent"></a>Azure AD 암호 보호 DC 에이전트

다음 요구 사항은 Azure AD 암호 보호 DC 에이전트에 적용됩니다.

* Azure AD 암호 보호 DC 에이전트 소프트웨어를 설치할 모든 컴퓨터는 Windows Server 2012 이상(Windows Server Core 버전 포함)을 실행해야 합니다.
    * Active Directory 도메인 또는 포리스트는 Windows Server 2012 DFL(도메인 기능 수준) 또는 FFL(포리스트 기능 수준)에 있을 필요가 없습니다. [디자인 원칙](concept-password-ban-bad-on-premises.md#design-principles)에서 설명한 대로 DC 에이전트 또는 프록시 소프트웨어를 실행하는 데 필요한 최소 DFL 또는 FFL이 없습니다.
* Azure AD 암호 보호 프록시 서비스를 설치할 모든 컴퓨터에는 .NET 4.7.2가 설치되어 있어야 합니다.
    * .NET 4.7.2가 아직 설치되지 않은 경우 [Windows용 .NET Framework 4.7.2 오프라인 설치 관리자](https://support.microsoft.com/topic/microsoft-net-framework-4-7-2-offline-installer-for-windows-05a72734-2127-a15d-50cf-daf56d5faec2)에 있는 설치 관리자를 다운로드하여 실행합니다.
* Azure AD 암호 보호 DC 에이전트 서비스를 실행하는 모든 Active Directory 도메인은 sysvol 복제에 DFSR(분산 파일 시스템 복제)을 사용해야 합니다.
   * 도메인에서 DFSR을 아직 사용하지 않는 경우 먼저 마이그레이션한 후에 Azure AD 암호 보호를 설치해야 합니다. 자세한 내용은 [SYSVOL 복제 마이그레이션 가이드: FRS를 DFS로 복제](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd640019(v=ws.10))를 참조하세요.

    > [!WARNING]
    > 현재 Azure AD 암호 보호 DC 에이전트 소프트웨어는 FRS(DFSR의 이전 기술)를 여전히 sysvol 복제에 사용하는 도메인의 도메인 컨트롤러에 설치되지만 소프트웨어가 이 환경에서 제대로 작동하지 않습니다.
    >
    > 부정적인 추가 부작용으로, 복제에 실패한 개별 파일과 성공한 것처럼 보이지만 모든 파일을 자동으로 복제하지 못하는 sysvol 복원 절차가 있습니다.
    >
    > DFSR의 고유한 이점과 Azure AD 암호 보호 배포의 차단을 해제하기 위해 최대한 빨리 DFSR을 사용하도록 도메인을 마이그레이션합니다. 이후 버전의 소프트웨어는 FRS를 여전히 사용하는 도메인에서 실행되는 경우 자동으로 사용하지 않도록 설정됩니다.

### <a name="azure-ad-password-protection-proxy-service"></a>Azure AD 암호 보호 프록시 서비스

다음 요구 사항은 Azure AD 암호 보호 프록시 서비스에 적용됩니다.

* Azure AD 암호 보호 프록시 서비스를 설치할 모든 컴퓨터는 Windows Server 2012 R2 이상(Windows Server Core 버전 포함)을 실행해야 합니다.

    > [!NOTE]
    > 도메인 컨트롤러에서 아웃바운드 직접 인터넷 연결을 사용할 수 있는 경우에도 Azure AD 암호 보호 프록시 서비스 배포는 Azure AD 암호 보호를 배포하기 위한 필수 요구 사항입니다.

* Azure AD 암호 보호 프록시 서비스를 설치할 모든 컴퓨터에는 .NET 4.7.2가 설치되어 있어야 합니다.
    * .NET 4.7.2가 아직 설치되지 않은 경우 [Windows용 .NET Framework 4.7.2 오프라인 설치 관리자](https://support.microsoft.com/topic/microsoft-net-framework-4-7-2-offline-installer-for-windows-05a72734-2127-a15d-50cf-daf56d5faec2)에 있는 설치 관리자를 다운로드하여 실행합니다.
* Azure AD 암호 보호 프록시 서비스를 호스트하는 모든 머신은 프록시 서비스에 로그온하는 기능을 도메인 컨트롤러에 부여하도록 구성해야 합니다. 해당 기능은 “네트워크에서 이 컴퓨터 액세스“ 권한 할당을 통해 제어됩니다.
* Azure AD 암호 보호 프록시 서비스를 호스트하는 모든 머신은 아웃바운드 TLS 1.2 HTTP 트래픽을 허용하도록 구성해야 합니다.
* 지정된 테넌트에서 처음으로 Azure AD 암호 보호 프록시 서비스를 등록하려면 *전역 관리자* 계정이 필요합니다. Azure AD를 사용한 후속 프록시 및 포리스트 등록은 *전역 관리자* 또는 *보안 관리자* 자격 증명이 있는 계정을 사용할 수 있습니다.
* [애플리케이션 프록시 환경 설정 절차](../app-proxy/application-proxy-add-on-premises-application.md#prepare-your-on-premises-environment)에 지정된 포트 및 URL 세트에 대해 네트워크 액세스를 사용하도록 설정해야 합니다. 위에서 설명한 두 개의 엔드포인트에 추가됩니다.

### <a name="microsoft-azure-ad-connect-agent-updater-prerequisites"></a>Microsoft Azure AD Connect 에이전트 업데이트 프로그램 필수 구성 요소

Microsoft Azure AD Connect 에이전트 업데이트 프로그램 서비스는 Azure AD 암호 보호 프록시 서비스와 나란히 설치됩니다. Microsoft Azure AD Connect 에이전트 업데이트 프로그램 서비스가 작동하려면 추가 구성이 필요합니다.

* 환경에서 HTTP 프록시 서버를 사용하는 경우 [기존 온-프레미스 프록시 서버 작업](../app-proxy/application-proxy-configure-connectors-with-proxy-servers.md)에 지정된 지침을 따릅니다.
* Microsoft Azure AD Connect 에이전트 업데이트 프로그램 서비스에도 [TLS 요구 사항](../app-proxy/application-proxy-add-on-premises-application.md#tls-requirements)에 지정된 TLS 1.2 단계가 필요합니다.

> [!WARNING]
> Azure AD 암호 보호 프록시와 Azure AD 애플리케이션 프록시는 서로 다른 버전의 Microsoft Azure AD Connect 에이전트 업데이트 서비스를 설치하기 때문에 지침은 애플리케이션 프록시 콘텐츠를 참조합니다. 이러한 서로 다른 버전은 나란히 설치하는 경우 호환되지 않습니다. 이렇게 하면 에이전트 업데이트 서비스에서 소프트웨어 업데이트를 위해 Azure에 연결하지 못하므로, Azure AD 암호 보호 프록시 및 애플리케이션 프록시를 동일한 컴퓨터에 설치하면 안 됩니다.

## <a name="download-required-software"></a>필수 소프트웨어 다운로드

온-프레미스 Azure AD 암호 보호 배포에는 다음 두 가지 필수 설치 관리자가 있습니다.

* Azure AD 암호 보호 DC 에이전트(*AzureADPasswordProtectionDCAgentSetup.msi*)
* Azure AD 암호 보호 프록시(*AzureADPasswordProtectionProxySetup.exe*)

두 설치 관리자를 모두 [Microsoft 다운로드 센터](https://www.microsoft.com/download/details.aspx?id=57071)에서 다운로드합니다.

## <a name="install-and-configure-the-proxy-service"></a>프록시 서비스 설치 및 구성

Azure AD 암호 보호 프록시 서비스는 일반적으로 온-프레미스 AD DS 환경의 구성원 서버에 있습니다. 설치된 후 Azure AD 암호 보호 프록시 서비스는 Azure Ad와 통신하여 Azure AD 테넌트의 전역 및 사용자 지정 금지 암호 목록의 복사본을 유지 관리합니다.

다음 섹션에서는 Azure AD 암호 보호 DC 에이전트를 온-프레미스 AD DS 환경의 도메인 컨트롤러에 설치합니다. 이러한 DC 에이전트는 프록시 서비스와 통신하여 도메인 내에서 암호 변경 이벤트를 처리할 때 사용할 최신 금지 암호 목록을 가져옵니다.

Azure AD 암호 보호 프록시 서비스를 호스팅하는 하나 이상의 서버를 선택합니다. 다음과 같은 고려 사항이 서버에 적용됩니다.

* 이러한 각 서비스는 단일 포리스트에 대한 암호 정책만 제공할 수 있습니다. 호스트 컴퓨터는 해당 포리스트의 모든 도메인에 조인해야 합니다.
* 프록시 서비스는 루트 또는 자식 도메인 또는 이러한 도메인의 조합에 설치할 수 있습니다.
* 포리스트의 각 도메인에 있는 하나 이상의 DC와 하나의 암호 보호 프록시 서버 간에 네트워크 연결이 필요합니다.
* 테스트를 위해 도메인 컨트롤러에서 Azure AD 암호 보호 프록시 서비스를 실행할 수 있지만, 해당 도메인 컨트롤러에는 인터넷 연결이 필요합니다. 이 연결은 보안 문제가 될 수 있습니다. 이 구성은 테스트용으로만 사용하는 것이 좋습니다.
* 이전의 [고가용성 고려 사항](#high-availability-considerations) 섹션에서 설명한 대로 중복성을 위해 포리스트당 둘 이상의 Azure AD 암호 보호 프록시 서버를 사용하는 것이 좋습니다.
* 읽기 전용 도메인 컨트롤러에서 Azure AD 암호 보호 프록시 서비스를 실행하는 기능은 지원되지 않습니다.

Azure AD 암호 보호 프록시 서비스를 설치하려면 다음 단계를 완료합니다.

1. Azure AD 암호 보호 프록시 서비스를 설치하려면 `AzureADPasswordProtectionProxySetup.exe` 소프트웨어 설치 관리자를 실행합니다.

    소프트웨어 설치는 다시 부팅할 필요가 없으며 다음 예제와 같이 표준 MSI 절차를 사용하여 자동화할 수 있습니다.
    
    ```console
    AzureADPasswordProtectionProxySetup.exe /quiet
    ```
    
    > [!NOTE]
    > 설치 오류를 방지하려면 먼저 Windows 방화벽 서비스를 실행한 후에 `AzureADPasswordProtectionProxySetup.exe` 패키지를 설치해야 합니다.
    >
    > Windows 방화벽이 실행되지 않도록 구성된 경우 해결 방법은 설치 중에 방화벽 서비스를 일시적으로 사용하도록 설정하고 실행하는 것입니다. 프록시 소프트웨어는 설치 후 Windows 방화벽에 특별히 종속되지 않습니다.
    >
    > 타사 방화벽을 사용하는 경우에도 배포 요구 사항을 충족하도록 구성해야 합니다. 여기에는 135 포트 및 프록시 RPC 서버 포트에 대한 인바운드 액세스 허용이 포함됩니다. 자세한 내용은 이전의 [배포 요구 사항](#deployment-requirements) 섹션을 참조하세요.

1. Azure AD 암호 보호 프록시 소프트웨어에는 새 PowerShell 모듈(`AzureADPasswordProtection`)이 포함되어 있습니다. 다음 단계에서는 이 PowerShell 모듈에서 다양한 cmdlet을 실행합니다.

    이 모듈을 사용하려면 관리자 권한으로 PowerShell 창을 열고, 다음과 같이 새 모듈을 가져옵니다.
    
    ```powershell
    Import-Module AzureADPasswordProtection
    ```
    
    > [!WARNING]
    > 64비트 버전의 PowerShell을 사용해야 합니다. 특정 cmdlet은 PowerShell(x86)에서 작동하지 않을 수 있습니다.

1. Azure AD 암호 보호 프록시 서비스가 실행 중인지 확인하려면 다음 PowerShell 명령을 사용합니다.

    ```powershell
    Get-Service AzureADPasswordProtectionProxy | fl
    ```

    결과에 *실행 중* **상태** 가 표시됩니다.

1. 프록시 서비스가 컴퓨터에서 실행되고 있지만 Azure AD와 통신할 수 있는 자격 증명이 없습니다. `Register-AzureADPasswordProtectionProxy` cmdlet을 사용하여 Azure AD 암호 보호 프록시 서버를 Azure AD에 등록합니다.

    이 cmdlet에는 지정된 테넌트에 대해 프록시가 처음 등록될 때 *전역 관리자* 자격 증명이 필요합니다. 동일하거나 다른 프록시에 대해 해당 테넌트의 후속 프록시 등록은 *전역 관리자* 또는 *보안 관리자* 자격 증명을 사용할 수 있습니다.

    이 명령이 한 번 성공하면 추가 호출도 성공하지만 필요하지 않습니다.

    `Register-AzureADPasswordProtectionProxy` cmdlet은 다음 세 가지 인증 모드를 지원합니다. 처음 두 모드는 Azure AD Multi-Factor Authentication을 지원하지만, 세 번째 모드는 지원하지 않습니다.

    > [!TIP]
    > 이 cmdlet이 특정 Azure 테넌트에 대해 처음 실행되는 경우 완료되기 전에 상당한 지연이 있을 수 있습니다. 오류가 보고되는 경우를 제외하고는 이 지연에 대해 걱정하지 마세요.

     * 대화형 인증 모드:

        ```powershell
        Register-AzureADPasswordProtectionProxy -AccountUpn 'yourglobaladmin@yourtenant.onmicrosoft.com'
        ```

        > [!NOTE]
        > 이 모드는 Server Core 운영 체제에서 작동하지 않습니다. 대신 다음 인증 모드 중 하나를 사용합니다. 또한 Internet Explorer 보안 강화 구성을 사용하도록 설정하는 경우 이 모드가 실패할 수 있습니다. 해결 방법은 해당 구성을 사용하지 않도록 설정하고, 프록시를 등록한 다음, 다시 사용하도록 설정하는 것입니다.

     * 디바이스 코드 인증 모드:

        ```powershell
        Register-AzureADPasswordProtectionProxy -AccountUpn 'yourglobaladmin@yourtenant.onmicrosoft.com' -AuthenticateUsingDeviceCode
        ```

        메시지가 표시되면 링크를 따라 웹 브라우저를 열고, 인증 코드를 입력합니다.

     * 자동(암호 기반) 인증 모드:

        ```powershell
        $globalAdminCredentials = Get-Credential
        Register-AzureADPasswordProtectionProxy -AzureCredential $globalAdminCredentials
        ```

        > [!NOTE]
        > 계정에 Azure AD Multi-Factor Authentication이 필요한 경우 이 모드는 실패합니다. 이 경우 이전 두 가지 인증 모드 중 하나를 사용하거나 MFA가 필요하지 않은 다른 계정을 대신 사용합니다.
        >
        > 또한 Azure AD 암호 보호 내부에서 사용하는 Azure 디바이스 등록에서 MFA를 전체적으로 요구하도록 구성한 경우 MFA가 필요할 수 있습니다. 이 요구 사항을 해결하기 위해 이전 두 가지 인증 모드 중 하나를 사용하여 MFA를 지원하는 다른 계정을 사용하거나 Azure 디바이스 등록 MFA 요구 사항을 일시적으로 완화할 수도 있습니다.
        >
        > 이렇게 변경하려면 Azure Portal에서 **Azure Active Directory** 를 검색하여 선택한 다음, **디바이스 > 디바이스 설정** 을 차례로 선택합니다. **디바이스를 조인하려면 다단계 인증 필요** 를 *아니요* 로 설정합니다. 등록이 완료되면 이 설정을 *예* 로 다시 구성해야 합니다.
        >
        > MFA 요구 사항은 테스트 목적으로만 무시하는 것이 좋습니다.

    현재는 이후 기능을 위해 예약된 *-ForestCredential* 매개 변수를 지정할 필요가 없습니다.

    Azure AD 암호 보호 프록시 서비스는 서비스의 수명 동안 한 번만 등록해야 합니다. 그 후에는 Azure AD 암호 보호 프록시 서비스에서 다른 필요한 유지 관리를 자동으로 수행합니다.

1. 이제 `Register-AzureADPasswordProtectionForest` PowerShell cmdlet을 사용하여 Azure와 통신하는 데 필요한 자격 증명으로 온-프레미스 Active Directory 포리스트를 등록합니다.

    > [!NOTE]
    > 여러 Azure AD 암호 보호 프록시 서버가 환경에 설치된 경우 포리스트를 등록하는 데 사용하는 프록시 서버는 중요하지 않습니다.

    이 cmdlet에는 Azure 테넌트에 대한 *전역 관리자* 또는 *보안 관리자* 자격 증명이 필요합니다. 온-프레미스 Active Directory 엔터프라이즈 관리자 권한도 필요합니다. 또한 이 cmdlet은 로컬 관리자 권한이 있는 계정을 사용하여 실행해야 합니다. 포리스트를 등록하는 데 사용되는 Azure 계정은 온-프레미스 Active Directory 계정과 다를 수 있습니다.
    
    이 단계는 포리스트마다 한 번씩 실행됩니다.

    `Register-AzureADPasswordProtectionForest` cmdlet은 다음 세 가지 인증 모드를 지원합니다. 처음 두 모드는 Azure AD Multi-Factor Authentication을 지원하지만, 세 번째 모드는 지원하지 않습니다.

    > [!TIP]
    > 이 cmdlet이 특정 Azure 테넌트에 대해 처음 실행되는 경우 완료되기 전에 상당한 지연이 있을 수 있습니다. 오류가 보고되는 경우를 제외하고는 이 지연에 대해 걱정하지 마세요.

     * 대화형 인증 모드:

        ```powershell
        Register-AzureADPasswordProtectionForest -AccountUpn 'yourglobaladmin@yourtenant.onmicrosoft.com'
        ```

        > [!NOTE]
        > 이 모드는 Server Core 운영 체제에서 작동하지 않습니다. 대신 다음 두 가지 인증 모드 중 하나를 사용합니다. 또한 Internet Explorer 보안 강화 구성을 사용하도록 설정하는 경우 이 모드가 실패할 수 있습니다. 해결 방법은 해당 구성을 사용하지 않도록 설정하고, 포리스트를 등록한 다음, 다시 사용하도록 설정하는 것입니다.  

     * 디바이스 코드 인증 모드:

        ```powershell
        Register-AzureADPasswordProtectionForest -AccountUpn 'yourglobaladmin@yourtenant.onmicrosoft.com' -AuthenticateUsingDeviceCode
        ```

        메시지가 표시되면 링크를 따라 웹 브라우저를 열고, 인증 코드를 입력합니다.

     * 자동(암호 기반) 인증 모드:

        ```powershell
        $globalAdminCredentials = Get-Credential
        Register-AzureADPasswordProtectionForest -AzureCredential $globalAdminCredentials
        ```

        > [!NOTE]
        > 계정에 Azure AD Multi-Factor Authentication이 필요한 경우 이 모드는 실패합니다. 이 경우 이전 두 가지 인증 모드 중 하나를 사용하거나 MFA가 필요하지 않은 다른 계정을 대신 사용합니다.
        >
        > 또한 Azure AD 암호 보호 내부에서 사용하는 Azure 디바이스 등록에서 MFA를 전체적으로 요구하도록 구성한 경우 MFA가 필요할 수 있습니다. 이 요구 사항을 해결하기 위해 이전 두 가지 인증 모드 중 하나를 사용하여 MFA를 지원하는 다른 계정을 사용하거나 Azure 디바이스 등록 MFA 요구 사항을 일시적으로 완화할 수도 있습니다.
        >
        > 이렇게 변경하려면 Azure Portal에서 **Azure Active Directory** 를 검색하여 선택한 다음, **디바이스 > 디바이스 설정** 을 차례로 선택합니다. **디바이스를 조인하려면 다단계 인증 필요** 를 *아니요* 로 설정합니다. 등록이 완료되면 이 설정을 *예* 로 다시 구성해야 합니다.
        >
        > MFA 요구 사항은 테스트 목적으로만 무시하는 것이 좋습니다.

       이러한 예제는 현재 로그인한 사용자가 루트 도메인의 Active Directory 도메인 관리자이기도 한 경우에만 성공합니다. 그렇지 않은 경우 *-ForestCredential* 매개 변수를 통해 대체 도메인 자격 증명을 제공할 수 있습니다.

    Active Directory 포리스트는 포리스트의 수명 동안 한 번만 등록해야 합니다. 그 후에는 포리스트의 Azure AD 암호 보호 DC 에이전트에서 다른 필요한 유지 관리를 자동으로 수행합니다. `Register-AzureADPasswordProtectionForest`가 포리스트에 대해 성공적으로 실행되면 cmdlet의 추가 호출이 성공하지만 필요하지 않습니다.
    
    `Register-AzureADPasswordProtectionForest`가 성공하려면 Azure AD 암호 보호 프록시 서버의 도메인에서 Windows Server 2012 이상을 실행하는 하나 이상의 DC를 사용할 수 있어야 합니다. 이 단계를 수행하기 전에 Azure AD 암호 보호 DC 에이전트 소프트웨어를 도메인 컨트롤러에 설치할 필요가 없습니다.

### <a name="configure-the-proxy-service-to-communicate-through-an-http-proxy"></a>HTTP 프록시를 통해 통신하도록 프록시 서비스 구성

사용자 환경에서 Azure와 통신하기 위해 특정 HTTP 프록시를 사용해야 하는 경우 다음 단계를 사용하여 Azure AD 암호 보호 서비스를 구성합니다.

*AzureADPasswordProtectionProxy.exe.config* 파일을 `%ProgramFiles%\Azure AD Password Protection Proxy\Service` 폴더에 만듭니다. 다음 내용을 포함합니다.

   ```xml
   <configuration>
      <system.net>
         <defaultProxy enabled="true">
         <proxy bypassonlocal="true"
            proxyaddress="http://yourhttpproxy.com:8080" />
         </defaultProxy>
      </system.net>
   </configuration>
   ```

HTTP 프록시에 인증이 필요한 경우 *useDefaultCredentials* 태그를 추가합니다.

   ```xml
   <configuration>
      <system.net>
         <defaultProxy enabled="true" useDefaultCredentials="true">
         <proxy bypassonlocal="true"
            proxyaddress="http://yourhttpproxy.com:8080" />
         </defaultProxy>
      </system.net>
   </configuration>
   ```

두 경우 모두에서 `http://yourhttpproxy.com:8080`을 특정 HTTP 프록시 서버의 주소와 포트로 바꿉니다.

HTTP 프록시가 권한 부여 정책을 사용하도록 구성된 경우 암호 보호를 위해 프록시 서비스를 호스팅하는 컴퓨터의 Active Directory 컴퓨터 계정에 대한 액세스 권한을 부여해야 합니다.

*AzureADPasswordProtectionProxy.exe.config* 파일을 만들거나 업데이트한 후에 Azure AD 암호 보호 프록시 서비스를 중지하고 다시 시작하는 것이 좋습니다.

프록시 서비스는 HTTP 프록시에 연결하는 데 특정 자격 증명을 사용하도록 지원하지 않습니다.

### <a name="configure-the-proxy-service-to-listen-on-a-specific-port"></a>특정 포트에서 수신 대기하도록 프록시 서비스 구성

Azure AD 암호 보호 DC 에이전트 소프트웨어는 RPC over TCP를 사용하여 프록시 서비스와 통신합니다. 기본적으로 Azure AD 암호 보호 프록시 서비스는 사용 가능한 모든 동적 RPC 엔드포인트에서 수신 대기합니다. 사용자 환경의 네트워킹 토폴로지 또는 방화벽 요구 사항에 따라 필요한 경우 특정 TCP 포트에서 수신 대기하도록 서비스를 구성할 수 있습니다. 정적 포트를 구성하는 경우 포트 135 및 선택한 정적 포트를 열어야 합니다.

<a id="static" /></a>정적 포트에서 실행되도록 서비스를 구성하려면 다음과 같이 `Set-AzureADPasswordProtectionProxyConfiguration` cmdlet을 사용합니다.

```powershell
Set-AzureADPasswordProtectionProxyConfiguration –StaticPort <portnumber>
```

> [!WARNING]
> 이러한 변경 내용을 적용하려면 Azure AD 암호 보호 프록시 서비스를 중지했다가 다시 시작해야 합니다.

동적 포트에서 실행되도록 서비스를 구성하려면 동일한 절차를 사용하지만 다음과 같이 *StaticPort* 를 0으로 다시 설정합니다.

```powershell
Set-AzureADPasswordProtectionProxyConfiguration –StaticPort 0
```

> [!WARNING]
> 이러한 변경 내용을 적용하려면 Azure AD 암호 보호 프록시 서비스를 중지했다가 다시 시작해야 합니다.

Azure AD 암호 보호 프록시 서비스는 포트 구성을 변경한 후에 수동으로 다시 시작해야 합니다. 이러한 구성이 변경되면 도메인 컨트롤러에서 Azure AD 암호 보호 DC 에이전트 서비스를 다시 시작할 필요가 없습니다.

서비스의 현재 구성을 쿼리하려면 다음 예제와 같이 `Get-AzureADPasswordProtectionProxyConfiguration` cmdlet을 사용합니다.

```powershell
Get-AzureADPasswordProtectionProxyConfiguration | fl
```

다음 출력 예제에서는 Azure AD 암호 보호 프록시 서비스에서 동적 포트를 사용하고 있음을 보여 줍니다.

```output
ServiceName : AzureADPasswordProtectionProxy
DisplayName : Azure AD password protection Proxy
StaticPort  : 0
```

## <a name="install-the-dc-agent-service"></a>DC 에이전트 서비스 설치

Azure AD 암호 보호 DC 에이전트 서비스를 설치하려면 `AzureADPasswordProtectionDCAgentSetup.msi` 패키지를 실행합니다.

다음 예제와 같이 표준 MSI 절차를 사용하여 소프트웨어 설치를 자동화할 수 있습니다.

```console
msiexec.exe /i AzureADPasswordProtectionDCAgentSetup.msi /quiet /qn /norestart
```

설치 관리자에서 컴퓨터를 자동으로 다시 부팅하도록 하려면 `/norestart` 플래그를 생략할 수 있습니다.

소프트웨어를 설치 또는 제거하려면 다시 시작해야 합니다. 해당 요구 사항은 암호 필터 DLL이 다시 시작을 통해서만 로드 또는 언로드되기 때문입니다.

DC 에이전트 소프트웨어가 도메인 컨트롤러에 설치되고 해당 컴퓨터가 다시 부팅되면 온-프레미스 Azure AD 암호 보호 설치가 완료됩니다. 다른 구성은 필요하지 않거나 가능하지 않습니다. 온-프레미스 DC에 대한 암호 변경 이벤트는 Azure AD에서 구성된 금지 암호 목록을 사용합니다.

Azure Portal에서 온-프레미스 Azure AD 암호 보호를 사용하도록 설정하거나 사용자 지정 금지 암호를 구성하려면 [온-프레미스 Azure AD 암호 보호 사용](howto-password-ban-bad-on-premises-operations.md)을 참조하세요.

> [!TIP]
> 아직 도메인 컨트롤러가 아닌 머신에 Azure AD 암호 보호 DC 에이전트를 설치할 수 있습니다. 이 경우 서비스는 시작되고 실행되지만, 컴퓨터가 도메인 컨트롤러 수준으로 올라갈 때까지 비활성 상태로 유지됩니다.

## <a name="upgrading-the-proxy-service"></a>프록시 서비스 업그레이드

Azure AD 암호 보호 프록시 서비스는 자동 업그레이드를 지원합니다. 자동 업그레이드는 프록시 서비스와 나란히 설치되는 Microsoft Azure AD Connect 에이전트 업데이트 서비스를 사용합니다. 자동 업그레이드는 기본적으로 설정되어 있으며, `Set-AzureADPasswordProtectionProxyConfiguration` cmdlet을 사용하여 사용하거나 사용하지 않도록 설정할 수 있습니다.

`Get-AzureADPasswordProtectionProxyConfiguration` cmdlet을 사용하여 현재 설정을 쿼리할 수 있습니다. 자동 업그레이드 설정은 항상 사용하도록 설정하는 것이 좋습니다.

`Get-AzureADPasswordProtectionProxy` cmdlet을 사용하여 포리스트에 현재 설치된 모든 Azure AD 암호 보호 프록시 서버의 소프트웨어 버전을 쿼리할 수 있습니다.

### <a name="manual-upgrade-process"></a>수동 업그레이드 프로세스

최신 버전의 `AzureADPasswordProtectionProxySetup.exe` 소프트웨어 설치 프로그램을 실행하여 수동 업그레이드를 수행합니다. 최신 버전의 소프트웨어는 [Microsoft 다운로드 센터](https://www.microsoft.com/download/details.aspx?id=57071)에서 다운로드할 수 있습니다.

현재 버전의 Azure AD 암호 보호 프록시 서비스를 제거할 필요가 없습니다. 설치 관리자에서 현재 위치 업그레이드를 수행합니다. 프록시 서비스를 업그레이드할 때 다시 부팅할 필요가 없습니다. 소프트웨어 업그레이드는 표준 MSI 절차(예: `AzureADPasswordProtectionProxySetup.exe /quiet`)를 사용하여 자동화할 수 있습니다.

## <a name="upgrading-the-dc-agent"></a>DC 에이전트 업그레이드

최신 버전의 Azure AD 암호 보호 DC 에이전트 소프트웨어를 사용할 수 있는 경우 최신 버전의 `AzureADPasswordProtectionDCAgentSetup.msi` 소프트웨어 패키지를 실행하여 업그레이드를 수행합니다. 최신 버전의 소프트웨어는 [Microsoft 다운로드 센터](https://www.microsoft.com/download/details.aspx?id=57071)에서 다운로드할 수 있습니다.

현재 버전의 DC 에이전트 소프트웨어를 제거할 필요가 없습니다. 설치 관리자에서 현재 위치 업그레이드를 수행합니다. DC 에이전트 소프트웨어를 업그레이드하는 경우 항상 다시 부팅해야 합니다. 이 요구 사항은 핵심 Windows 동작으로 인해 발생합니다.

소프트웨어 업그레이드는 표준 MSI 절차(예: `msiexec.exe /i AzureADPasswordProtectionDCAgentSetup.msi /quiet /qn /norestart`)를 사용하여 자동화할 수 있습니다.

설치 프로그램이 머신을 자동으로 다시 부팅하도록 하려는 경우에는 `/norestart` 플래그를 생략할 수 있습니다.

`Get-AzureADPasswordProtectionDCAgent` cmdlet을 사용하여 포리스트에 현재 설치된 모든 Azure AD 암호 보호 DC 에이전트의 소프트웨어 버전을 쿼리할 수 있습니다.

## <a name="next-steps"></a>다음 단계

이제 Azure AD 암호 보호에 필요한 서비스를 온-프레미스 서버에 설치했으므로 [Azure Portal에서 온 프레미스 Azure AD 암호 보호를 사용하도록 설정](howto-password-ban-bad-on-premises-operations.md)하여 배포를 완료합니다.
