---
title: Azure Virtual Desktop 보안 모범 사례 - Azure
titleSuffix: Azure
description: Azure Virtual Desktop 환경을 안전하게 유지하기 위한 모범 사례입니다.
author: heidilohr
ms.topic: conceptual
ms.date: 12/15/2020
ms.author: helohr
ms.service: virtual-desktop
manager: femila
ms.openlocfilehash: b6161ab2b6c07e0da9a5f676a15598ca1bb103e9
ms.sourcegitcommit: 03e84c3112b03bf7a2bc14525ddbc4f5adc99b85
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/03/2021
ms.locfileid: "129402113"
---
# <a name="security-best-practices"></a>보안 모범 사례

Azure Virtual Desktop은 조직을 안전하게 유지하기 위한 다양한 보안 기능을 포함하는 관리형 가상 데스크톱 서비스입니다. Azure Virtual Desktop 배포에서 Microsoft는 고객을 대신하여 서비스의 일부를 관리합니다. 이 서비스에는 어디서나 원격 데스크톱에 액세스할 수 있는 위험을 줄여주는 역방향 연결과 같은 다양한 기본 제공 고급 보안 기능이 있습니다.

이 문서에서는 고객의 Azure Virtual Desktop 배포를 안전하게 유지하기 위해 관리자가 수행할 수 있는 추가 단계를 설명합니다.

## <a name="security-responsibilities"></a>보안 책임

클라우드 서비스에서 기존의 온-프레미스 VDI(가상 데스크톱 인프라)와 다른 점은 보안 책임을 처리하는 방법입니다. 예를 들어 기존의 온-프레미스 VDI에서 고객은 모든 보안 측면을 담당합니다. 그러나 대부분의 클라우드 서비스에서 이러한 책임은 고객과 회사 간에 공유됩니다.

Azure Virtual Desktop을 사용하는 경우 일부 구성 요소는 이미 사용자 환경에 맞게 보안을 유지하지만 조직의 보안 요구 사항에 맞게 다른 영역을 직접 구성해야 합니다.

Azure Virtual Desktop 배포에서 담당하는 보안 요구 사항은 다음과 같습니다.

| 보안 요구 사항 | 고객이 담당하나요? |
|---------------|:-------------------------:|
|ID|예|
|사용자 디바이스(모바일 및 PC)|예|
|앱 보안|예|
|세션 호스트 OS|예|
|배포 구성|예|
|네트워크 제어|예|
|가상화 컨트롤 플레인|아니요|
|물리적 호스트|아니요|
|실제 네트워크|아니요|
|실제 데이터 센터|아니요|

고객이 담당하지 않는 보안 요구 사항은 Microsoft에서 처리합니다.

## <a name="azure-security-best-practices"></a>Azure 보안 모범 사례

Azure Virtual Desktop은 Azure의 서비스입니다. Azure Virtual Desktop 배포의 안전을 최대화하려면 주변의 Azure 인프라 및 관리 평면도 보호해야 합니다. 인프라를 보호하려면 Azure Virtual Desktop에서 더 큰 Azure 에코시스템에 맞게 조정하는 방법을 고려합니다. Azure 에코시스템에 대한 자세한 내용은 [Azure 보안 모범 사례 및 패턴](../security/fundamentals/best-practices-and-patterns.md)을 참조하세요.

다음 섹션에서는 Azure 에코시스템을 보호하기 위한 모범 사례에 대해 설명합니다.

### <a name="enable-azure-security-center"></a>Azure Security Center 활성화

Azure Security Center 표준을 구독, 가상 머신, 키 자격 증명 모음 및 스토리지 계정에 사용하도록 설정하는 것이 좋습니다.

Azure Security Center 표준을 사용하면 다음을 수행할 수 있습니다.

* 취약성을 관리합니다.
* PCI와 같은 일반적인 프레임워크의 규정 준수를 평가합니다.
* 환경에 대한 전체 보안을 강화합니다.

자세한 내용은 [Security Center 표준에 Azure 구독 온보딩](../security-center/security-center-get-started.md)을 참조하세요.

### <a name="improve-your-secure-score"></a>보안 점수 향상

보안 점수는 전체 보안을 향상시키기 위한 권장 사항 및 모범 사례 권장 사항을 제공합니다. 이러한 권장 사항은 가장 중요한 항목을 선택하는 데 도움이 되도록 우선 순위가 지정되며, 빠른 수정 옵션은 잠재적 취약성을 빠르게 해결하는 데 도움이 됩니다. 또한 이러한 권장 사항은 시간이 지남에 따라 업데이트되므로 환경 보안을 유지하는 가장 좋은 방법을 최신 상태로 유지할 수 있습니다. 자세한 내용은 [Azure Security Center의 보안 점수 향상](../security-center/secure-score-security-controls.md)을 참조하세요.

## <a name="azure-virtual-desktop-security-best-practices"></a>Azure Virtual Desktop 보안 모범 사례

Azure Virtual Desktop에는 여러 가지 기본 제공 보안 제어가 있습니다. 이 섹션에서는 사용자와 데이터를 안전하게 유지하는 데 사용할 수 있는 보안 제어에 대해 알아봅니다.

### <a name="require-multi-factor-authentication"></a>다단계 인증 필요

Azure Virtual Desktop의 모든 사용자와 관리자에 대해 다단계 인증을 요구하면 전체 배포의 보안이 향상됩니다. 자세한 내용은 [Azure Virtual Desktop에 Azure AD 다단계 인증 사용](set-up-mfa.md)을 참조하세요.

### <a name="enable-conditional-access"></a>조건부 액세스 사용

[조건부 액세스](../active-directory/conditional-access/overview.md)를 사용하도록 설정하면 사용자에게 Azure Virtual Desktop 환경에 대한 액세스 권한을 부여하기 전에 위험을 관리할 수 있습니다. 액세스 권한을 부여할 사용자를 결정할 때 사용자, 해당 사용자가 로그인하는 방법 및 해당 사용자가 사용하는 디바이스도 고려하는 것이 좋습니다.

### <a name="collect-audit-logs"></a>감사 로그 수집

감사 로그 수집을 사용하도록 설정하면 Azure Windows Virtual Desktop과 관련된 사용자 및 관리자 활동을 볼 수 있습니다. 키 감사 로그의 몇 가지 예는 다음과 같습니다.

-   [Azure 활동 로그](../azure-monitor/essentials/activity-log.md)
-   [Azure Active Directory 활동 로그](../active-directory/reports-monitoring/concept-activity-logs-azure-monitor.md)
-   [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md)
-   [세션 호스트](../azure-monitor/agents/agent-windows.md)
-   [Azure Virtual Desktop 진단 로그](../virtual-desktop/diagnostics-log-analytics.md)
-   [Key Vault 로그](../key-vault/general/logging.md)

### <a name="use-remoteapps"></a>RemoteApps 사용

배포 모델을 선택할 때 원격 사용자에게 전체 가상 데스크톱에 대한 액세스 권한을 제공하거나 애플리케이션만 선택할 수 있습니다. RemoteApps(원격 애플리케이션)는 사용자가 가상 데스크톱에서 앱을 사용할 때 원활한 환경을 제공합니다. RemoteApps는 사용자가 애플리케이션에서 공개하는 원격 컴퓨터의 하위 집합으로만 작업할 수 있도록 하여 위험을 줄입니다.

### <a name="monitor-usage-with-azure-monitor"></a>Azure Monitor를 사용하여 사용량 모니터링

[Azure Monitor](https://azure.microsoft.com/services/monitor/)를 사용하여 Azure Virtual Desktop 서비스의 사용량 및 가용성을 모니터링합니다. 서비스에 영향을 주는 이벤트가 있을 때마다 알림을 받을 수 있도록 Azure Virtual Desktop 서비스에 대한 [서비스 상태 경고](../service-health/alerts-activity-log-service-notifications-portal.md)를 만드는 것이 좋습니다.

## <a name="session-host-security-best-practices"></a>세션 호스트 보안 모범 사례

세션 호스트는 Azure 구독 및 가상 네트워크 내에서 실행되는 가상 머신입니다. Azure Virtual Desktop 배포의 전체 보안은 세션 호스트에 적용하는 보안 제어에 따라 달라집니다. 이 섹션에서는 세션 호스트를 안전하게 유지하기 위한 모범 사례에 대해 설명합니다.

### <a name="enable-endpoint-protection"></a>엔드포인트 보호 사용

알려진 악성 소프트웨어로부터 배포를 보호하려면 모든 세션 호스트에서 엔드포인트 보호를 사용하도록 설정하는 것이 좋습니다. Windows Defender 바이러스 백신 또는 타사 프로그램을 사용할 수 있습니다. 자세한 내용은 [VDI 환경에서 Windows Defender 바이러스 백신 배포 가이드](/windows/security/threat-protection/windows-defender-antivirus/deployment-vdi-windows-defender-antivirus)를 참조하세요.

FSLogix와 같은 프로필 솔루션 또는 VHD 파일을 탑재하는 기타 솔루션의 경우 VHD 파일 확장명을 제외하는 것이 좋습니다.

### <a name="install-an-endpoint-detection-and-response-product"></a>엔드포인트 검색 및 응답 제품 설치

고급 검색 및 응답 기능을 제공하려면 EDR(엔드포인트 검색 및 응답) 제품을 설치하는 것이 좋습니다. [Azure Security Center](../security-center/security-center-services.md)가 사용하도록 설정된 서버 운영 체제의 경우 EDR 제품을 설치하면 Defender ATP가 배포됩니다. 클라이언트 운영 체제의 경우 [Defender ATP](/windows/security/threat-protection/microsoft-defender-atp/onboarding) 또는 타사 제품을 해당 엔드포인트에 배포할 수 있습니다.

### <a name="enable-threat-and-vulnerability-management-assessments"></a>위협 및 취약성 관리 평가 사용

운영 체제 및 애플리케이션에 있는 소프트웨어 취약성을 식별하는 것은 환경을 안전하게 유지하는 데 매우 중요합니다. Azure Security Center는 서버 운영 체제에 대한 취약성 평가를 통해 문제 지점을 식별하는 데 도움이 될 수 있습니다. 데스크톱 운영 체제에 대한 위협 및 취약성 관리를 제공하는 Defender ATP를 사용할 수도 있습니다. Azure Security Center 및 Defender ATP를 사용하는 것이 좋지만 원하는 경우 타사 제품을 사용할 수도 있습니다.

### <a name="patch-software-vulnerabilities-in-your-environment"></a>사용자 환경에서 소프트웨어 취약성 패치

취약성이 식별되면 패치를 적용해야 합니다. 이는 실행되는 운영 체제, 그 내부에 배포된 애플리케이션 및 새 컴퓨터를 만드는 이미지를 포함하는 가상 환경에도 적용됩니다. 공급업체 패치 알림 통신에 따라 적시에 패치를 수행합니다. 새로 배포된 컴퓨터가 최대한 안전하게 유지하기 위해 매월 기본 이미지를 패치하는 것이 좋습니다.

### <a name="establish-maximum-inactive-time-and-disconnection-policies"></a>최대 비활성 시간 및 연결 끊기 정책 설정

사용자가 비활성 상태일 때 로그아웃하면 리소스가 유지되고 권한 없는 사용자가 액세스할 수 없게 됩니다. 시간 제한에서 사용자 생산성과 리소스 사용량 간의 균형을 유지하는 것이 좋습니다. 상태 비저장 애플리케이션과 상호 작용하는 사용자의 경우 컴퓨터를 끄고 리소스를 유지하는 더 적극적인 정책을 고려합니다. 시뮬레이션 또는 CAD 렌더링과 같이 사용자가 유휴 상태일 때 계속 실행되는 장기 실행 애플리케이션의 연결을 끊으면 사용자의 작업이 중단되고 컴퓨터를 다시 시작해야 할 수도 있습니다.

### <a name="set-up-screen-locks-for-idle-sessions"></a>유휴 세션에 대한 화면 잠금 설정

유휴 시간 동안 컴퓨터 화면을 잠그도록 Azure Virtual Desktop을 구성하고, 잠금을 해제하려면 인증을 요구하여 원치 않는 시스템 액세스를 방지할 수 있습니다.

### <a name="establish-tiered-admin-access"></a>계층화된 관리자 액세스 설정

사용자에게 가상 데스크톱에 대한 관리자 액세스 권한을 부여하지 않는 것이 좋습니다. 소프트웨어 패키지가 필요한 경우 Microsoft Endpoint Manager와 같은 구성 관리 유틸리티를 통해 이를 제공하는 것이 좋습니다. 다중 세션 환경에서는 사용자가 소프트웨어를 직접 설치할 수 없도록 하는 것이 좋습니다.

### <a name="consider-which-users-should-access-which-resources"></a>액세스해야 하는 사용자 및 리소스에 대한 고려

세션 호스트를 기존 데스크톱 배포의 확장으로 간주합니다. 네트워크 조각화 및 필터링을 사용하는 것과 같이 환경의 다른 데스크톱에서와 동일한 방식으로 네트워크 리소스에 대한 액세스를 제어하는 것이 좋습니다. 기본적으로 세션 호스트는 인터넷의 모든 리소스에 연결할 수 있습니다. Azure Firewall, 네트워크 가상 어플라이언스 또는 프록시를 사용하는 등 트래픽을 제한할 수 있는 여러 가지 방법이 있습니다. 트래픽을 제한해야 하는 경우 Azure Virtual Desktop이 제대로 작동할 수 있도록 적절한 규칙을 추가해야 합니다.

### <a name="manage-office-pro-plus-security"></a>Office Pro Plus 보안 관리

세션 호스트를 보호하는 것 외에도 세션 호스트 내에서 실행되는 애플리케이션을 보호하는 것도 중요합니다. Office Pro Plus는 세션 호스트에 배포되는 가장 일반적인 애플리케이션 중 하나입니다. Office 배포 보안을 향상시키려면 [보안 정책 관리자](/DeployOffice/overview-of-security-policy-advisor)를 기업용 Microsoft 365 앱에 사용하는 것이 좋습니다. 이 도구는 추가 보안을 위해 배포에 적용할 수 있는 정책을 식별합니다. 또한 보안 정책 관리자는 보안 및 생산성에 미치는 영향에 기반한 정책을 권장합니다.

### <a name="other-security-tips-for-session-hosts"></a>세션 호스트에 대한 기타 보안 팁

운영 체제 기능을 제한하여 세션 호스트의 보안을 강화할 수 있습니다. 수행할 수 있는 몇 가지 작업은 다음과 같습니다.

- 드라이브, 프린터 및 USB 디바이스를 원격 데스크톱 세션에서 사용자의 로컬 디바이스로 리디렉션하도록 디바이스 리디렉션을 제어합니다. 보안 요구 사항을 평가하고 이러한 기능을 사용하지 않도록 설정해야 하는지 여부를 확인하는 것이 좋습니다.

- 로컬 및 원격 드라이브 매핑을 숨겨 Windows 탐색기 액세스를 제한합니다. 이렇게 하면 사용자가 시스템 구성 및 사용자에 대한 원치 않는 정보를 검색할 수 없도록 방지합니다.

- RDP에서 사용자 환경의 세션 호스트에 직접 액세스하지 못하도록 방지합니다. 관리 또는 문제 해결을 위해 RDP에 직접 액세스해야 하는 경우 [Just-In-Time](../security-center/security-center-just-in-time.md) 액세스를 사용하도록 설정하여 세션 호스트의 잠재적인 공격 노출 영역을 제한합니다.

- 로컬 및 원격 파일 시스템에 액세스할 때 제한된 권한을 사용자에게 부여합니다. 권한은 로컬 및 원격 파일 시스템에서 최소 권한으로 액세스 제어 목록을 사용하도록 하여 제한할 수 있습니다. 이렇게 하면 사용자는 필요한 항목에만 액세스할 수 있으며 중요한 리소스를 변경하거나 삭제할 수 없습니다.

- 원치 않는 소프트웨어가 세션 호스트에서 실행되지 않도록 방지합니다. 세션 호스트에서 추가 보안을 위해 AppLocker를 사용하도록 설정하여 허용하는 앱만 호스트에서 실행할 수 있도록 할 수 있습니다.

## <a name="azure-virtual-desktop-support-for-trusted-launch"></a>신뢰할 수 있는 시작에 대한 Azure Virtual Desktop 지원

신뢰할 수 있는 시작은 루트킷, 부팅 키트 및 커널 수준 맬웨어와 같은 공격 벡터를 통해 "스택 맨 아래" 위협으로부터 보호하기 위한 향상된 보안 기능을 갖춘 2세대 Azure VM입니다. 다음은 Azure Virtual Desktop에서 지원되는 신뢰할 수 있는 시작의 향상된 보안 기능입니다. 신뢰할 수 있는 시작에 대한 자세한 내용은 [Azure 가상 머신에 대한 신뢰할 수 있는 시작(미리 보기)](../virtual-machines/trusted-launch.md)을 방문하세요.

### <a name="secure-boot"></a>보안 부팅

보안 부팅은 맬웨어 기반 루트킷 및 부팅 키트로부터 펌웨어를 보호하기 위해 플랫폼 펌웨어에서 지원하는 모드입니다. 이 모드에서는 서명된 OS 및 드라이버만 컴퓨터를 시작할 수 있습니다. 

### <a name="monitor-boot-integrity-using-remote-attestation"></a>원격 증명을 사용하여 부팅 무결성 모니터링

원격 증명은 VM 상태를 확인하는 좋은 방법입니다. 원격 증명은 계획 부팅 레코드가 있고, 정품이며, vTPM(가상 신뢰할 수 있는 플랫폼 모듈)에서 시작되는지 확인합니다. 상태 확인은 플랫폼이 올바르게 시작되었다는 암호화 확신을 제공합니다. 

### <a name="vtpm"></a>vTPM

vTPM은 VM당 TPM의 가상 인스턴스가 포함된 하드웨어 TPM(신뢰할 수 있는 플랫폼 모듈)의 가상화된 버전입니다. vTPM은 VM의 전체 부팅 체인(UEFI, OS, 시스템 및 드라이버)에 대한 무결성 측정을 수행하여 원격 증명을 사용 하도록 설정합니다. 

VM에서 원격 증명을 사용하려면 vTPM을 사용하도록 설정하는 것이 좋습니다. vTPM을 사용하도록 설정하면 전체 볼륨 암호화를 제공하여 미사용 데이터를 보호하는 BitLocker 기능을 사용하도록 설정할 수도 있습니다. vTPM을 사용하는 모든 기능은 특정 VM에 바인딩되는 비밀을 생성합니다. 풀링된 시나리오에서 사용자가 Azure Virtual Desktop 서비스에 연결하면 사용자를 호스트 풀의 모든 VM으로 리디렉션할 수 있습니다. 기능이 설계되는 방법에 따라 영향을 받을 수 있습니다.

>[!NOTE]
>BitLocker는 FSLogix 프로필 데이터를 저장하는 특정 디스크를 암호화하는 데 사용하면 안 됩니다.

### <a name="virtualization-based-security"></a>가상화 기반 보안

VBS(가상화 기반 보안)는 하이퍼바이저를 사용하여 OS에 액세스할 수 없는 안전한 메모리 영역을 만들고 격리합니다. HVCI(Hypervisor-Protected Code Integrity) 및 Windows Defender Credential Guard는 모두 VBS를 사용하여 취약성으로부터 더 향상된 보호 기능을 제공합니다. 

#### <a name="hypervisor-protected-code-integrity"></a>Hypervisor-Protected Code Integrity

HVCI는 VBS를 사용하여 악성 코드 또는 확인되지 않은 코드의 삽입 및 실행으로부터 Windows 커널 모드 프로세스를 보호하는 강력한 시스템 완화 기능입니다.

#### <a name="windows-defender-credential-guard"></a>Windows Defender Credential Guard

Windows Defender Credential Guard는 VBS를 사용하여 권한 있는 시스템 소프트웨어만 액세스할 수 있도록 비밀을 격리하고 보호합니다. 이렇게 하면 이러한 비밀 및 자격 증명 도난 공격(예: Pass-the-Hash 공격)에 대한 무단 액세스를 방지합니다.

### <a name="deploy-trusted-launch-in-your-azure-virtual-desktop-environment"></a>Azure Virtual Desktop 환경에서 신뢰할 수 있는 시작 배포

Azure Virtual Desktop은 현재 호스트 풀 설정 프로세스 중에 신뢰할 수 있는 시작을 자동으로 구성할 수 있도록 지원하지 않습니다. Azure Virtual Desktop 환경에서 신뢰할 수 있는 시작을 사용하려면 신뢰할 수 있는 시작을 정상적으로 배포한 다음, 수동으로 가상 머신을 원하는 호스트 풀에 추가해야 합니다.

## <a name="nested-virtualization"></a>중첩된 가상화

Azure Virtual Desktop에서 중첩된 가상화 실행을 지원하는 운영 체제는 다음과 같습니다.

- Windows Server 2016
- Windows Server 2019
- Windows 10 Enterprise
- Windows 10 Enterprise 다중 세션

## <a name="windows-defender-application-control"></a>Windows Defender 애플리케이션 제어

다음 운영 체제에서는 Azure Virtual Desktop에서 Windows Defender 애플리케이션 제어 사용을 지원합니다.

- Windows Server 2016
- Windows Server 2019
- Windows 10 Enterprise
- Windows 10 Enterprise 다중 세션

>[!NOTE]
>Windows Defender Access Control을 사용하는 경우 디바이스 수준의 정책만 대상으로 지정하는 것이 좋습니다. 개별 사용자를 대상으로 정책을 지정할 수 있지만 정책이 적용되면 디바이스의 모든 사용자에게 동일한 영향을 줍니다.

## <a name="next-steps"></a>다음 단계

다단계 인증을 사용하도록 설정하는 방법에 대한 자세한 내용은 [다단계 인증 설정](set-up-mfa.md)을 참조하세요.
