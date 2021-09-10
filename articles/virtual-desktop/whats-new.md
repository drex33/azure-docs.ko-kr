---
title: Azure Virtual Desktop의 새로운 기능 - Azure
description: Azure Virtual Desktop에 대한 새로운 기능 및 제품 업데이트.
author: Heidilohr
ms.topic: overview
ms.date: 07/30/2021
ms.author: helohr
ms.reviewer: thhickli; darank
manager: femila
ms.custom: references_regions
ms.openlocfilehash: 88c94a3f1b6329c80cddcec49c7ebb445a21d8e0
ms.sourcegitcommit: 851b75d0936bc7c2f8ada72834cb2d15779aeb69
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/31/2021
ms.locfileid: "123318144"
---
# <a name="whats-new-in-azure-virtual-desktop"></a>Azure Virtual Desktop의 새로운 기능

Azure Virtual Desktop은 정기적으로 업데이트됩니다. 이 문서에서는 다음에 대해 알아봅니다.

- 최신 업데이트
- 새로운 기능
- 기존 기능의 향상된 기능
- 버그 수정

이 문서는 매월 업데이트됩니다. 새 업데이트를 유지하려면 이 문서를 다시 확인하세요.

## <a name="client-updates"></a>클라이언트 업데이트

다음 문서를 확인하여 Azure Virtual Desktop 및 원격 데스크톱 서비스의 클라이언트에 대한 업데이트를 알아봅니다.

- [Windows](/windows-server/remote/remote-desktop-services/clients/windowsdesktop-whatsnew)
- [macOS](/windows-server/remote/remote-desktop-services/clients/mac-whatsnew)
- [iOS](/windows-server/remote/remote-desktop-services/clients/ios-whatsnew)
- [Android](/windows-server/remote/remote-desktop-services/clients/android-whatsnew)
- [Web](/windows-server/remote/remote-desktop-services/clients/web-client-whatsnew)

## <a name="azure-virtual-desktop-agent-updates"></a>Azure Virtual Desktop 에이전트 업데이트

Azure Virtual Desktop 에이전트는 한 달에 한 번 이상 업데이트됩니다.

Azure Virtual Desktop 에이전트에서 변경된 내용은 다음과 같습니다.

- 버전 1.0.3130.2900: 이 업데이트는 2021년 7월에 릴리스되었으며 다음과 같이 변경되었습니다.
    - 일반 개선 사항 및 버그 수정
    - Intune 등록을 위한 호스트 풀 경로를 가져오는 문제를 해결합니다.
    - 에이전트 문제를 더 잘 진단하기 위해 로깅이 추가되었습니다.
    - 오케스트레이션 시간 제한 문제를 해결합니다.
- 버전 1.0.3050.2500: 이 업데이트는 2021년 7월에 릴리스되었으며 다음과 같이 변경되었습니다.
    - 에이전트 상태에 대한 내부 모니터가 업데이트되었습니다.
    - 스택 상태에 대한 재시도 논리가 업데이트되었습니다.
- 버전 1.0.2990.1500: 이 업데이트는 2021년 4월에 릴리스되었으며 다음과 같이 변경되었습니다.
    - 에이전트 오류 메시지가 업데이트되었습니다.
    - Windows 7 VM에 비 Windows 7 에이전트를 설치할 수 없도록 하는 예외를 추가했습니다.
    - 하트비트 서비스 논리를 업데이트했습니다.
- 버전 1.0.2944.1400: 이 업데이트는 2021년 4월에 릴리스되었으며 다음과 같이 변경되었습니다.
    - 에이전트 오류에 대한 이벤트 뷰어 로그에 Azure Virtual Desktop 에이전트 문제 해결 가이드에 대한 링크를 배치했습니다.
    - 더 나은 오류 처리를 위해 추가 예외를 추가했습니다.
    - 고객이 액세스할 수 있는 필수 URL을 확인할 수 있는 WVDAgentUrlTool.exe가 추가되었습니다.
-   버전 1.0.2866.1500: 이 업데이트는 2021년 3월에 릴리스되었으며 스택 상태 검사와 관련된 문제를 해결합니다.
-   버전 1.0.2800.2802: 이 업데이트는 2021년 3월에 릴리스되었으며 일반적인 개선 사항과 버그 수정이 있습니다.
-   버전 1.0.2800.2800: 이 업데이트는 2021년 3월에 릴리스되었으며 역방향 연결 문제를 해결합니다.
-   버전 1.0.2800.2700: 이 업데이트는 2021년 2월에 릴리스되었으며 액세스 거부 오케스트레이션 문제를 해결합니다.

## <a name="fslogix-updates"></a>FSLogix 업데이트

FSLogix의 최신 업데이트가 궁금하신가요? [FSLogix의 새로운 기능](/fslogix/whats-new)을 확인하세요.

## <a name="august-2021"></a>2021년 8월

2021년 8월에 변경된 내용은 다음과 같습니다.

### <a name="windows-11-preview-on-avd"></a>AVD의 Windows 11(미리 보기)

Windows 11(미리 보기) 이미지는 이제 고객이 Azure Virtual Desktop을 사용하여 테스트하고 유효성을 검사할 수 있도록 Azure Marketplace에 제공됩니다. 자세한 내용은 [공지](https://techcommunity.microsoft.com/t5/azure-virtual-desktop/windows-11-preview-is-now-available-on-azure-virtual-desktop/ba-p/2666468)를 참조하세요.

### <a name="multimedia-redirection-mmr-is-now-in-public-preview"></a>이제 MMR(멀티미디어 리디렉션)이 공개 미리 보기로 제공됩니다.

MMR(멀티미디어 리디렉션)은 Azure Virtual Desktop 웹 브라우저에서 비디오를 시청하는 동안 부드럽게 비디오를 재생하며 Microsoft Edge 및 Google Chrome에서 작동합니다. [블로그 게시물](https://techcommunity.microsoft.com/t5/azure-virtual-desktop/public-preview-announcing-public-preview-of-multimedia/m-p/2663244#M7692)에서 자세히 알아보세요.

### <a name="ip-virtualization-support-for-windows-server-2019"></a>Windows Server 2019에 대한 IP 가상화 지원

IP 가상화는 Windows Server 2008 R2 이상에서 지원됩니다. Windows Server 2019에 IP 가상화를 사용하려면 추가 단계가 필요합니다. 자세한 내용은 [공지](https://techcommunity.microsoft.com/t5/azure-virtual-desktop/ip-virtualization-support-for-windows-server-2019/m-p/2658650)를 참조하세요.

### <a name="windows-defender-application-control-and-azure-disk-encryption-is-now-supported"></a>이제 Windows Defender 애플리케이션 제어 및 Azure Disk Encryption이 지원됩니다.

이제 Azure Virtual Desktop은 Windows VM에서 실행할 수 있는 드라이버 및 애플리케이션을 제어하는 Windows Defender 애플리케이션 제어, 그리고 Windows BitLocker를 사용하여 VM의 OS 및 데이터 디스크에 대한 볼륨 암호화를 제공하는 Azure Disk Encryption을 지원합니다. 자세한 내용은 [공지](https://techcommunity.microsoft.com/t5/azure-virtual-desktop/support-for-windows-defender-application-control-and-azure-disk/m-p/2658633#M7685)를 참조하세요.
 
### <a name="signing-into-azure-ad-using-smart-cards-are-now-supported-in-azure-virtual-desktop"></a>이제 Azure Virtual Desktop에서 스마트 카드를 사용하여 Azure AD에 로그인할 수 있습니다.

Azure AD의 새 기능은 아니지만, 이제 Azure Virtual Desktop에서 스마트 카드로 로그인하도록 Active Directory Federation Services를 구성할 수 있습니다. 자세한 내용은 [공지](https://techcommunity.microsoft.com/t5/azure-virtual-desktop/signing-in-to-azure-ad-using-smart-cards-now-supported-in-azure/m-p/2654209#M7671)를 참조하세요.

### <a name="screen-capture-protection-is-now-generally-available"></a>화면 캡처 보호의 일반 공급 전환

클라이언트 엔드포인트에서 실행되는 소프트웨어에서 중요한 정보를 화면 캡처할 수 없도록 AVD의 화면 캡처 보호를 사용하여 보호하세요. [블로그 게시물](https://techcommunity.microsoft.com/t5/azure-virtual-desktop/announcing-general-availability-of-screen-capture-protection-for/m-p/2699684)에서 자세히 알아보세요.

## <a name="july-2021"></a>2021년 7월

2021년 7월에 변경된 내용은 다음과 같습니다.

### <a name="azure-virtual-desktop-images-now-include-optimized-teams"></a>이제 Azure Virtual Desktop 이미지에 최적화된 Teams가 포함됩니다.

엔터프라이즈용 Microsoft 365 앱이 포함되어 있는 Azure Virtual Desktop 이미지 갤러리에 제공되는 모든 이미지에는 이제 미디어 최적화 버전의 Azure Virtual Desktop용 Teams가 미리 설치되어 있습니다. 자세한 내용은 [공지](https://techcommunity.microsoft.com/t5/azure-virtual-desktop/media-optimization-for-microsoft-teams-now-part-of-win10/m-p/2550054#M7442)를 참조하세요.

### <a name="azure-active-directory-domain-join-for-session-hosts-is-in-public-preview"></a>세션 호스트용 Azure Active Directory 도메인 가입이 공개 미리 보기로 제공됩니다.

이제 Azure Virtual Desktop VM(가상 머신)을 Azure AD(Azure Active Directory)에 직접 조인할 수 있습니다. 이 기능을 통해 모든 디바이스에서 기본 자격 증명을 사용하여 VM에 연결할 수 있습니다. 또한 VM을 Microsoft Endpoint Manager에 자동으로 등록할 수 있습니다. 특정 시나리오의 경우 이 기능은 도메인 컨트롤러를 없애고, 비용을 절감하고, 배포를 간소화하는 데 도움이 됩니다. 자세한 내용은 [Azure Virtual Desktop에서 Azure AD 조인 가상 머신 배포](deploy-azure-ad-joined-vm.md)를 참조하세요.

### <a name="fslogix-version-2105-is-now-available"></a>FSLogix 버전 2105 사용 가능

이제 FSLogix 2105 버전이 일반 공급됩니다. 이 버전에는 공개 미리 보기 버전(버전 2105)에서 사용할 수 없었던 향상된 로그인 시간과 버그 수정이 포함되어 있습니다. 자세한 내용은 [FSLogix 릴리스 정보](/fslogix/whats-new) 및 [블로그 게시물](https://techcommunity.microsoft.com/t5/azure-virtual-desktop/announcing-general-availability-of-fslogix-2105-2-9-7838-44263/m-p/2539491#M7412)을 참조하세요.

### <a name="azure-virtual-desktop-in-china-has-entered-public-preview"></a>중국에서 Azure Virtual Desktop이 공개 미리 보기로 제공되기 시작했습니다.

중국에서 Azure Virtual Desktop을 사용할 수 있게 되면서 글로벌 서비스 범위가 확장되었으며, 조직은 향상된 성능과 대기 시간을 통해 이 지역의 고객을 지원할 수 있습니다. 자세한 내용은 [공지 페이지](https://azure.microsoft.com/updates/azure-virtual-desktop-is-now-available-in-the-azure-china-cloud-in-preview/)를 참조하세요.
 
### <a name="the-getting-started-feature-for-azure-virtual-desktop"></a>Azure Virtual Desktop 시작 기능

이 기능은 Azure Portal에서 Azure Virtual Desktop 환경을 설정하는 간소화된 온보딩 환경을 제공합니다. 이 기능을 사용하여 자동화된 Azure Active Directory Domain Services에 대한 시스템 요구 사항을 충족하는 배포를 간단하고 쉬운 방법으로 만들 수 있습니다. 자세한 내용은 이 [블로그 게시물](https://techcommunity.microsoft.com/t5/azure-virtual-desktop/getting-started-wizard-in-azure-virtual-desktop/m-p/2451385)을 참조하세요.

### <a name="start-vm-on-connect-is-now-generally-available"></a>연결 시 VM 시작 기능 일반 제공

이제 연결 시 VM 시작 기능이 일반 제공됩니다. 이 기능을 사용하면 할당이 해제되거나 중지된 VM을 끄고 사용자의 요구에 따라 유연하게 배포하여 비용을 최적화할 수 있습니다. 자세한 내용은 [연결 시 가상 머신 시작](start-virtual-machine-connect.md)을 참조하세요.

### <a name="remote-app-streaming-documentation"></a>원격 앱 스트리밍 설명서

최근에 Azure Virtual Desktop을 사용하여 고객과 비즈니스 파트너에게 앱을 서비스로 제공하기 위한 원격 앱 스트리밍의 새로운 가격 책정 옵션이 발표되었습니다. 예를 들어 소프트웨어 공급업체는 원격 앱 스트리밍을 사용하여 고객이 액세스할 수 있는 SaaS(Software as a Service) 솔루션으로 앱을 제공할 수 있습니다. 원격 앱 스트리밍에 대한 자세한 내용은 [설명서](./remote-app-streaming/overview.md)를 참조하세요.

2021년 7월 14일부터 2021년 12월 31일까지 원격 앱 스트리밍을 사용하는 고객에게 비즈니스 파트너와 고객이 무료로 Azure Virtual Desktop에 액세스할 수 있는 프로모션 제안이 제공됩니다. 이 제안은 외부 사용자 액세스 권한에만 적용됩니다. 일반 청구는 2022년 1월 1일부터 재개됩니다. 이 기간에는 Microsoft 365 E3 또는 Windows E3와 같은 라이선스에 있는 기존 Windows 라이선스 자격을 계속 사용할 수 있습니다. 이 제안에 대해 자세히 알아보려면 [Azure Virtual Desktop 가격 책정 페이지](https://azure.microsoft.com/pricing/details/virtual-desktop/)를 참조하세요.

### <a name="new-azure-virtual-desktop-handbooks"></a>새 Azure Virtual Desktop 안내서

최근에 다양한 시나리오에서 Azure Virtual Desktop을 설계하고 배포하는 데 도움이 되도록 다음과 같은 네 가지 새 안내서가 출시되었습니다. 

- [애플리케이션 관리](https://azure.microsoft.com/resources/azure-virtual-desktop-handbook-application-management/)에서는 애플리케이션 제공을 현대화하고 IT 관리를 간소화하는 방법을 보여줍니다.  
- [재해 복구](https://azure.microsoft.com/resources/azure-virtual-desktop-handbook-disaster-recovery/)에서는 재해 복구 전략을 개발하여 비즈니스 복원력을 강화하는 방법을 알아봅니다.  
- [Azure Virtual Desktop을 사용하는 Citrix Cloud](https://azure.microsoft.com/resources/migration-guide-citrix-cloud-with-azure-virtual-desktop/) 마이그레이션 가이드에서는 Citrix 투자 가치를 높이는 방법을 알아봅니다.
- [Azure Virtual Desktop을 사용하는 VMware Horizon](https://azure.microsoft.com/resources/migration-guide-vmware-horizon-cloud-and-azure-virtual-desktop/) 마이그레이션 가이드에서는 기존 VMware 투자 가치를 높이는 방법을 알아봅니다.

## <a name="june-2021"></a>2021년 6월

2021년 6월에 변경된 내용은 다음과 같습니다.

### <a name="windows-virtual-desktop-is-now-azure-virtual-desktop"></a>Windows Virtual Desktop은 이제 Azure Virtual Desktop입니다.

유연한 클라우드 데스크톱 및 원격 애플리케이션 플랫폼이라는 당사의 비전에 더 잘 부합하기 위해 Windows Virtual Desktop의 이름을 Azure Virtual Desktop으로 변경했습니다. [블로그의 공지 게시물](https://azure.microsoft.com/blog/azure-virtual-desktop-the-desktop-and-app-virtualization-platform-for-the-hybrid-workplace/)에서 자세히 알아보세요.

### <a name="eu-uk-and-canada-geographies-are-now-generally-available"></a>EU, 영국 및 캐나다 지역은 이제 일반 공급됩니다.

유럽 연합, 영국 및 캐나다를 위한 메타데이터 서비스가 이제 일반 공급됩니다. 이러한 신규 위치는 미국 외부의 데이터 주권에 매우 중요합니다. 자세한 내용은 [블로그 게시물](https://techcommunity.microsoft.com/t5/azure-virtual-desktop/announcing-public-preview-of-azure-virtual-desktop-service/m-p/2478401#M7314)을 참조하세요.

### <a name="the-getting-started-tool-is-now-in-public-preview"></a>이제 시작 도구는 공개 미리 보기로 제공됩니다.

처음 사용하는 사용자가 배포 프로세스를 보다 쉽게 수행할 수 있도록 Azure Virtual Desktop 시작 도구를 만들었습니다. 배포 프로세스를 단순화하고 자동화함으로써 이 도구를 통해 다양한 사용자가 Azure Virtual Desktop을 더 빠르고 쉽게 채택할 수 있기를 바랍니다. [블로그 게시물](https://techcommunity.microsoft.com/t5/azure-virtual-desktop/getting-started-wizard-in-azure-virtual-desktop/m-p/2451385)에서 자세히 알아보세요.

### <a name="azure-virtual-desktop-pricing-calculator-updates"></a>Azure Virtual Desktop 가격 계산기 업데이트

Azure 가격 계산기에서 Azure Virtual Desktop 가격 책정 환경을 개선하기 위해 다음과 같은 몇 가지 중요한 업데이트를 수행했습니다.  
  
- 서비스 이름을 Azure Virtual Desktop으로 업데이트했습니다.  
- 또한 다음과 같은 새 항목으로 레이아웃을 업데이트했습니다.  
   - 관리 디스크 및 파일 스토리지 대역폭이 모두 포함된 Storage 섹션  
   - 사용자당 비용을 표시하는 사용자 지정 섹션

[이 페이지](https://azure.microsoft.com/pricing/calculator/)에서 가격 계산기에 액세스할 수 있습니다.

### <a name="single-sign-on-sso-using-active-directory-federation-services-ad-fs"></a>AD FS(Active Directory Federation Services)를 사용하는 SSO(Single Sign-On)

AD FS Single Sign-On 기능이 이제 일반 공급됩니다. 이 기능을 통해 고객은 AD FS를 사용하여 Windows 및 웹 클라이언트의 사용자에게 Single Sign-On 환경을 제공할 수 있습니다. 자세한 내용은 [Azure Virtual Desktop에 대한 AD FS Single Sign-On 구성](configure-adfs-sso.md)을 참조하세요.

## <a name="may-2021"></a>2021년 5월

2021년 5월 버전의 새로운 기능은 다음과 같습니다.

### <a name="smart-card-authentication"></a>스마트 카드 인증

이제 KDC(키 배포 센터) 프록시 RDP(원격 데스크톱 프로토콜) 속성을 공식적으로 출시했습니다. 이러한 속성을 사용하면 Azure Virtual Desktop 세션의 RDP 부분에 대해 Kerberos 인증이 사용하도록 설정됩니다. 여기에는 암호 없이 네트워크 수준 인증을 허용하는 방식이 포함됩니다. [블로그 게시물](https://techcommunity.microsoft.com/t5/windows-virtual-desktop/new-feature-smart-card-authentication-for-windows-virtual/m-p/2323226)에서 자세히 알아보세요.

### <a name="the-web-client-now-supports-file-transfer"></a>이제 웹 클라이언트에서 파일 전송을 지원함

웹 클라이언트의 퍼블릭 미리 보기 버전인 1.0.24.7(preview) 버전부터 이제 원격 세션과 로컬 컴퓨터 간에 파일을 전송할 수 있습니다. 원격 세션에 파일을 업로드하려면 웹 클라이언트 페이지 맨 위에 있는 메뉴에서 업로드 아이콘을 선택합니다. 파일을 다운로드하려면 원격 세션의 시작 메뉴에서 **원격 데스크톱 가상 드라이브** 를 검색합니다. 가상 드라이브를 연 후 파일을 다운로드 폴더로 끌어서 놓으면 브라우저가 로컬 컴퓨터에 파일을 다운로드하기 시작합니다.

### <a name="start-vm-on-connect-support-updates"></a>연결 시 VM 시작 지원 업데이트

연결 시 VM 시작 지원 업데이트(미리 보기)는 이제 풀링된 호스트 풀과 Azure Government 클라우드를 지원합니다. 자세한 내용은 [블로그 게시물](https://techcommunity.microsoft.com/t5/windows-virtual-desktop/leverage-start-vm-on-connect-for-pooled-host-pools-and-azure-gov/m-p/2349866)을 읽어보세요.

### <a name="latency-improvements-for-the-united-arab-emirates-region"></a>아랍에미리트 지역에 대한 대기 시간 개선

Azure 컨트롤 플레인을 UAE(아랍에미리트)로 확장했으므로 해당 지역의 고객은 이제 개선된 대기 시간을 활용할 수 있습니다. [Azure Virtual Desktop 로드맵](https://www.microsoft.com/microsoft-365/roadmap?filters=Windows%20Virtual%20Desktop&searchterms=64545)에서 자세히 알아보세요.

### <a name="ending-internet-explorer-11-support"></a>Internet Explorer 11 지원 종료

2021년 9월 30일부터 Azure Virtual Desktop 웹 클라이언트는 Internet Explorer 11을 더 이상 지원하지 않습니다. 대신 웹 클라이언트 및 원격 세션에 [Microsoft Edge](https://www.microsoft.com/edge?form=MY01R2&OCID=MY01R2&r=1) 브라우저를 사용하는 것이 좋습니다. 자세한 내용은 [블로그 게시물](https://techcommunity.microsoft.com/t5/windows-virtual-desktop/windows-virtual-desktop-web-client-to-end-support-for-internet/m-p/2369007)의 공지를 참조하세요.

### <a name="microsoft-endpoint-manager-public-preview"></a>Microsoft Endpoint Manager 퍼블릭 미리 보기

Windows 10 Enterprise 다중 세션의 Microsoft Endpoint Manager 지원에 대한 퍼블릭 미리 보기를 시작했습니다. 이 새로운 기능을 통해 로컬 디바이스와 동일한 도구를 사용하여 Windows 10 VM을 관리할 수 있습니다. [Microsoft Endpoint Manager 설명서](/mem/intune/fundamentals/windows-virtual-desktop-multi-session)에서 자세히 알아보세요.

### <a name="fslogix-agent-public-preview"></a>FSLogix 에이전트 퍼블릭 미리 보기

최신 버전의 FSLogix 에이전트에 대한 퍼블릭 미리 보기를 출시했습니다. [블로그 게시물](https://techcommunity.microsoft.com/t5/windows-virtual-desktop/public-preview-fslogix-release-2105-is-now-available-in-public/m-p/2380996/thread-id/7105)에서 자세한 내용을 확인하고 미리 보기에 액세스하는 데 필요한 양식을 제출합니다.

### <a name="may-2021-updates-for-teams-for-azure-virtual-desktop"></a>Azure Virtual Desktop용 Teams의 2021년 5월 업데이트

이 업데이트에서는 비디오를 공유하는 동안 화면이 검은색 상태를 유지하는 문제를 해결했습니다. 또한 세션 클라이언트와 Teams 서버 간의 비디오 해상도가 일치하지 않는 문제를 해결했습니다. 이제 Azure Virtual Desktop의 Teams는 Teams 서버의 입력을 기준으로 해상도 및 비트 전송률을 변경해야 합니다.

### <a name="azure-portal-deployment-updates"></a>Azure Portal 배포 업데이트

Azure Portal에서 다음과 같이 배포 프로세스를 업데이트했습니다.

- 새 Azure Virtual Desktop 세션 호스트 VM을 만들 때 "이미지"의 드롭다운 목록 상자에 새 이미지(GEN2 포함)를 추가했습니다.
- 이제 호스트 풀을 만들 때 가상 머신에 대한 부팅 진단을 구성할 수 있습니다.
- 고급 호스트 풀 RDP 속성 탭에서 RDP 프록시에 도구 설명을 추가했습니다.
- MSIX 패키지에서 애플리케이션을 추가할 때 아이콘 경로에 대한 정보 풍선이 추가되었습니다.
- 더 이상 비관리 디스크를 사용하여 관리형 부팅 진단을 수행할 수 없습니다.
- 현재 Azure Portal에서 타사 마켓플레이스 이미지를 사용하여 호스트 풀 만들기를 지원할 수 있도록 Azure Resource Manager에서 호스트 풀을 만들기 위한 템플릿이 업데이트되었습니다.

### <a name="single-sign-on-using-active-directory-federation-services-public-preview"></a>Active Directory Federation Services 퍼블릭 미리 보기를 사용하는 Single Sign-On

호스트 풀별로 SSO(Single Sign-On)를 수행하기 위한 AD FS(Active Directory Federation Services) 지원에 대한 퍼블릭 미리 보기를 시작했습니다. [Azure Virtual Desktop에 대한 AD FS Single Sign-On 구성](configure-adfs-sso.md)에서 자세히 알아보세요. 

### <a name="enterprise-scale-support"></a>엔터프라이즈급 지원

Azure Virtual Desktop에 대한 엔터프라이즈급 지원을 위해 클라우드 채택 프레임워크의 업데이트된 섹션을 출시했습니다. 자세한 내용은 [Azure Virtual Desktop 생성 세트에 대한 엔터프라이즈급 지원](/azure/cloud-adoption-framework/scenarios/wvd/enterprise-scale-landing-zone)을 참조하세요.

### <a name="customer-adoption-kit"></a>고객 채택 키트

최근에 고객 및 파트너가 해당 고객을 위해 Azure Virtual Desktop을 설정하는 데 도움이 되는 Azure Virtual Desktop 고객 채택 키트를 출시했습니다. [여기](https://www.microsoft.com/azure/partners/resources/customer-adoption-kit-windows-virtual-desktop)에서 키트를 다운로드할 수 있습니다.

## <a name="april-2021"></a>2021년 4월

4월 버전의 새로운 기능은 다음과 같습니다.

### <a name="use-the-start-vm-on-connect-feature-preview-in-the-azure-portal"></a>Azure Portal에서 연결 시 VM 시작 기능(미리 보기) 사용

이제 Azure Portal에서 연결 시 VM 시작(미리보기)을 구성할 수 있습니다. 이 업데이트를 사용하면 사용자가 Android 및 macOS 클라이언트에서 VM에 액세스할 수 있습니다. 자세한 내용은 [연결 시 VM 시작](start-virtual-machine-connect.md#use-the-azure-portal)을 참조하세요.

### <a name="required-url-check-tool"></a>필수 URL 확인 도구 

Azure Virtual Desktop 에이전트, 버전 1.0.2944.400에는 URL의 유효성을 검사하고 가상 시스템이 작동해야 하는 URL에 액세스할 수 있는지 여부를 표시하는 도구가 포함되어 있습니다. 필수 URL에 액세스할 수 있는 경우 도구에 해당 URL이 나열되므로 필요하다면 차단 해제할 수 있습니다. 자세한 내용은 [안전 URL 목록](safe-url-list.md#required-url-check-tool)을 참조하세요.

### <a name="updates-to-the-azure-portal-ui-for-azure-virtual-desktop"></a>Azure Virtual Desktop에 대한 Azure Portal UI 업데이트

Azure Virtual Desktop용 Azure Portal UI의 최신 업데이트에서 변경된 내용은 다음과 같습니다.

- 드레이닝 모드가 사용하도록 설정된 상태에서 세션 호스트를 검색할 때 오류가 나타나는 문제가 해결되었습니다.
- 포털 SDK가 버전 7.161.0으로 업그레이드되었습니다.
- 사용자 세션 탭에 리소스 ID 누락된 오류 메시지가 나타나는 문제가 해결되었습니다.
- 이제 Azure Portal에 세션 호스트에 대한 자세한 하위 상태 메시지가 표시됩니다.

### <a name="april-2021-updates-for-teams-on-azure-virtual-desktop"></a>Azure Virtual Desktop의 Teams에 대한 2021년 4월 업데이트

Azure Virtual Desktop의 Teams에 대한 새로운 기능은 다음과 같습니다.

- Windows 10 기반 클라이언트용 출력 비디오 스트림의 비디오 처리를 위한 하드웨어 가속화가 추가되었습니다.
- 전면 카메라와 후면 또는 외부 카메라를 둘 다 사용하여 회의에 참석하면 전면 카메라가 기본적으로 선택됩니다.
- Teams가 x86 기반 컴퓨터에서 충돌하는 문제가 해결되었습니다.
- 화면 공유 중에 줄무늬가 생기는 문제가 해결되었습니다.
- 회의 구성원이 수신 영상 또는 화면 공유를 볼 수 없는 문제가 해결되었습니다.

### <a name="msix-app-attach-is-now-generally-available"></a>이제 MSIX 앱 연결을 일반적으로 사용할 수 있음

이제 Azure Virtual Desktop에 대한 MSIX 앱 연결이 퍼블릭 미리 보기로 제공되며 모든 사용자가 사용할 수 있습니다. MSIX 앱 연결에 대한 자세한 내용은 [TechCommunity 공지](https://techcommunity.microsoft.com/t5/windows-virtual-desktop/msix-app-attach-is-now-generally-available/m-p/2270468)를 참조하세요.

### <a name="the-macos-client-now-supports-apple-silicon-and-big-sur"></a>이제 macOS 클라이언트가 Apple Silicon과 Big Sur을 지원함

이제 macOS Azure Virtual Desktop 클라이언트가 Apple Silicon과 Big Sur을 지원합니다. 전체 업데이트 목록은 [macOS 클라이언트의 새로운 기능](/windows-server/remote/remote-desktop-services/clients/mac-whatsnew)에서 제공됩니다.

## <a name="march-2021"></a>2021년 3월

2021년 3월에 변경된 내용은 다음과 같습니다.

### <a name="updates-to-the-azure-portal-ui-for-azure-virtual-desktop"></a>Azure Virtual Desktop에 대한 Azure Portal UI 업데이트

Azure Portal에서 Azure Virtual Desktop이 다음과 같이 업데이트되었습니다.

- 호스트 풀을 만들고 VM을 추가하는 워크플로의 새 가용성 옵션(가용성 집합 및 영역)이 지원됩니다.
- "지원 필요" 상태인 호스트가 사용할 수 없음으로 표시되는 문제가 해결되었습니다. 이제 해당 호스트 옆에 경고 아이콘이 표시됩니다.
- 활성 세션에 대한 정렬 기능이 지원됩니다.
- 이제 호스트 세부 정보 탭에서 특정 사용자에게 메시지를 보내거나 특정 사용자를 로그아웃시킬 수 있습니다.
- 최대 세션 제한 필드가 변경되었습니다.
- 호스트 풀을 만드는 워크플로에 OU 유효성 검사 경로가 추가되었습니다.
- 이제 개인 호스트 풀을 만들 때 최신 버전의 Windows 10 이미지를 사용할 수 있습니다.

### <a name="generation-2-images-and-trusted-launch"></a>2세대 이미지 및 신뢰할 수 있는 시작

이제 Azure Marketplace에는 Windows 10 Enterprise 및 Windows 10 Enterprise 다중 세션을 위한 2세대 이미지가 있습니다. 이러한 이미지를 통해 신뢰할 수 있는 시작 VM을 사용할 수 있습니다. 2세대 VM에 대한 자세한 내용은 [1 또는 2세대 가상 머신을 만들어야 합니까?](../virtual-machines/generation-2.md)를 참조하세요. Azure Virtual Desktop의 신뢰할 수 있는 시작 VM을 프로비저닝하는 방법을 알아보려면 [TechCommunity 게시물](https://techcommunity.microsoft.com/t5/windows-virtual-desktop/windows-virtual-desktop-support-for-trusted-launch/m-p/2206170)을 참조하세요.

### <a name="fslogix-is-now-preinstalled-on-windows-10-enterprise-multi-session-images"></a>FSLogix가 이제 Windows 10 Enterprise 다중 세션 이미지에 미리 설치됨

고객 피드백에 따라, 구성되지 않은 FSLogix 버전이 이미 설치되어 있는 Windows 10 Enterprise 다중 세션 이미지의 새 버전이 설정되었습니다. 이를 통해 Azure Virtual Desktop 배포가 더 수월해지기를 바랍니다.

### <a name="azure-monitor-for-azure-virtual-desktop-is-now-in-general-availability"></a>Azure Virtual Desktop용 Azure Monitor가 이제 일반 공급 방식으로 제공됨

Azure Virtual Desktop용 Azure Monitor가 이제 일반 공급 방식으로 제공됩니다. 이 기능은 배포를 모니터링하고, 단일 위치에서 이벤트, 상태 및 권장 문제 해결 방법을 볼 수 있게 해주는 자동화된 서비스입니다. 자세한 내용은 [설명서](azure-monitor.md) 또는 [TechCommunity 게시물](https://techcommunity.microsoft.com/t5/windows-virtual-desktop/azure-monitor-for-windows-virtual-desktop-is-generally-available/m-p/2242861)을 참조하세요.

### <a name="march-2021-updates-for-teams-on-azure-virtual-desktop"></a>Azure Virtual Desktop의 Teams에 대한 2021년 3월 업데이트

Azure Virtual Desktop의 Teams가 다음과 같이 업데이트되었습니다.

- 통화 및 2x2 모드에서 영상 화질 성능이 개선되었습니다.
- 하드웨어 동영상 처리 오프로드(XVP)를 사용하여 CPU 사용률이 5-10%(CPU 세대에 따라 다음) 감소되었습니다.
- 이제 이전 머신은 XVP 및 하드웨어 디코딩을 사용하여 2x2 모드에서 더 많은 수신 동영상 스트림을 원활하게 표시할 수 있습니다.
- 더 나은 AV 동기화 성능을 제공하고 일시적인 문제를 줄이기 위해 WebRTC 스택이 M74에서 M88로 업데이트되었습니다.
- 소프트웨어 H264 인코더가 OpenH264(웹의 Teams에서 사용되는 OSS)로 대체되어 송신 카메라의 동영상 품질이 개선되었습니다.
- 3월 30일에 일반 대중을 위한 Teams Server에 2x2 모드가 지원됩니다. 2x2 모드는 최대 4개의 수신 동영상 스트림을 동시에 표시합니다.

### <a name="start-vm-on-connect-public-preview"></a>Start VM on Connect 공개 미리 보기

새 호스트 풀 설정인 Start VM on Connect가 이제 공개 미리 보기로 제공됩니다. 이 설정을 사용하여 필요할 때마다 VM을 켤 수 있습니다. 비용을 절약하려면 Azure Compute 설정을 구성하여 VM의 할당을 취소해야 합니다. 자세한 내용은 [블로그 게시물](https://aka.ms/wvdstartvmonconnect) 및 [설명서](start-virtual-machine-connect.md)를 참조하세요.

### <a name="azure-virtual-desktop-specialty-certification"></a>Azure Virtual Desktop 특수 인증

Azure의 Azure Virtual Desktop에 대한 전문성을 증명할 수 있는 AZ-140 시험의 베타 버전이 출시되었습니다. 자세히 알아보려면 [TechCommunity 게시물](https://techcommunity.microsoft.com/t5/microsoft-learn-blog/beta-exam-prove-your-expertise-in-windows-virtual-desktop-on/ba-p/2147107)을 확인하세요.

## <a name="february-2021"></a>2021년 2월

2021년 2월에 변경된 내용은 다음과 같습니다.

### <a name="portal-experience"></a>포털 환경

다음과 같은 방법으로 Azure Portal 환경을 개선했습니다.

- 세션 호스트 그리드 탭에 있는 호스트의 대량 드레이닝 모드. 
- 이제 MSIX 앱 연결을 공개 미리 보기에서 사용할 수 있습니다.
- 다크 모드의 호스트 풀 개요 정보를 수정했습니다.

### <a name="eu-metadata-storage-now-in-public-preview"></a>현재 공개 미리 보기로 제공되는 EU 메타데이터 스토리지

이제 Azure Virtual Desktop의 서비스 메타데이터에 대한 스토리지 옵션으로 유럽(EU) 지리의 퍼블릭 미리 보기를 호스팅하고 있습니다. 고객은 해당 서비스 개체를 만들 때 서유럽 또는 북유럽 중에서 선택할 수 있습니다. 호스트 풀에 대한 서비스 개체 및 메타데이터는 각 지역과 연결된 Azure 지리에 저장됩니다. 자세히 알아보려면 [공개 미리 보기를 발표하는 블로그 게시물](https://techcommunity.microsoft.com/t5/windows-virtual-desktop/announcing-public-preview-of-windows-virtual-desktop-service/m-p/2143939)을 참조하세요.

### <a name="teams-on-azure-virtual-desktop-plugin-updates"></a>Azure Virtual Desktop의 Teams 플러그 인 업데이트

화면이 갑자기 어두워지거나 비디오와 사운드가 동기화되지 않는 경우와 같이 가장 일반적으로 보고되는 문제를 해결하여 Azure Virtual Desktop 플러그 인의 화상 통화 품질을 개선했습니다. 이러한 향상된 기능을 통해 활성 스피커가 전환된 단일 비디오 보기의 성능이 향상됩니다. Teams에서 특수 문자가 포함된 하드웨어 디바이스를 사용할 수 없는 문제도 해결했습니다.

## <a name="january-2021"></a>2021년 1월

2021년 1월에 변경된 내용은 다음과 같습니다.

### <a name="new-azure-virtual-desktop-offer"></a>새로운 Azure Virtual Desktop 제품

새 고객은 네이티브 Microsoft 솔루션을 사용하는 경우 D 시리즈 및 Bs 시리즈 가상 머신의 Azure Virtual Desktop 컴퓨팅 비용을 최대 90일 동안 30% 절약할 수 있습니다. 이 제안은 2021년 3월 31일 이전에 Azure Portal에서 사용할 수 있습니다. [Azure Virtual Desktop 제품 페이지](https://azure.microsoft.com/services/virtual-desktop/offer/)에서 자세히 알아보세요.

### <a name="networksecuritygrouprules-value-change"></a>networkSecurityGroupRules 값 변경 

Azure Resource Manager 중첩 템플릿에서 networkSecurityGroupRules 기본값이 개체에서 배열로 변경되었습니다. 이렇게 하면 networkSecurityGroupRules 값을 지정하지 않고 managedDisks-customimagevm.json을 사용하는 경우 오류를 방지할 수 있습니다. 이는 호환성이 손상되는 변경이 아니므로 이전 버전과 호환됩니다.

### <a name="fslogix-hotfix-update"></a>FSLogix 핫픽스 업데이트

이전 릴리스(2.9.7621.30127)의 문제를 해결하기 위해 FSLogix 버전 2009 HF_01(2.9.7654.46150)이 릴리스되었습니다. 이전 버전 사용을 중지하고 FSLogix를 가능한 한 빨리 업데이트하는 것이 좋습니다.

자세한 내용은 [FSLogix의 새로운 기능](/fslogix/whats-new#fslogix-apps-2009-hf_01-29765446150)에 있는 릴리스 정보를 참조하세요.

### <a name="azure-portal-experience-improvements"></a>향상된 Azure Portal 환경 기능

Azure Portal 환경이 다음과 같이 향상되었습니다.

- 이제 Active Directory 도메인 조인 계정 자격 증명을 사용하여 만든 로컬 계정을 추가하는 대신 로컬 VM 관리자 자격 증명을 직접 추가할 수 있습니다.
- 이제 사용자는 개별 할당 및 그룹 할당을 모두 개별 사용자 및 그룹에 대한 별도의 탭에 나열할 수 있습니다.
- 이제 Azure Virtual Desktop 에이전트의 버전 번호가 호스트 풀에 대한 Virtual Machine 개요에 표시됩니다.
- 호스트 풀 및 애플리케이션 그룹에 대한 대량 삭제가 추가되었습니다.
- 이제 드레이닝 모드를 호스트 풀의 여러 세션 호스트에 사용하거나 사용하지 않도록 설정할 수 있습니다.
- VM 세부 정보 페이지에서 공용 IP 필드가 제거되었습니다.

### <a name="azure-virtual-desktop-agent-troubleshooting"></a>Azure Virtual Desktop 에이전트 문제 해결

일반적인 문제가 발생한 고객을 지원하기 위해 [Azure Virtual Desktop 에이전트 문제 해결 가이드](troubleshoot-agent.md)가 최근에 설정되었습니다.

### <a name="microsoft-defender-for-endpoint-integration"></a>엔드포인트용 Microsoft Defender 통합

Microsoft Defender for Endpoint 통합은 이제 일반 공급됩니다. 이 기능은 로컬 Windows 10 컴퓨터와 동일한 조사 환경을 Azure Virtual Desktop VM에 제공합니다. Windows 10 Enterprise 다중 세션을 사용하는 경우 Microsoft Defender for Endpoint는 최대 50개의 동시 사용자 연결을 지원하므로 Windows 10 Enterprise 다중 세션의 비용을 절약하고 Microsoft Defender for Endpoint의 신뢰도를 제공합니다. 자세한 내용은 이 [블로그 게시물](https://techcommunity.microsoft.com/t5/microsoft-defender-for-endpoint/windows-virtual-desktop-support-is-now-generally-available/ba-p/2103712)을 참조하세요.

### <a name="azure-security-baseline-for-azure-virtual-desktop"></a>Azure Virtual Desktop에 대한 Azure 보안 기준

최근에 사용자의 주의가 필요한 Azure Virtual Desktop에 대한 [Azure 보안 기준 문서](security-baseline.md)가 게시되었습니다. 이러한 지침에는 Azure 보안 벤치마크 버전 2.0을 Azure Virtual Desktop에 적용하는 방법에 대한 정보가 포함되어 있습니다. Azure 보안 벤치마크는 Azure에서 클라우드 솔루션을 보호하는 데 사용하도록 권장되는 설정 및 사례에 대해 설명합니다.

## <a name="december-2020"></a>2020년 12월

2020년 12월에 변경된 내용은 다음과 같습니다. 

### <a name="azure-monitor-for-azure-virtual-desktop"></a>Azure Virtual Desktop용 Azure Monitor

이제 Azure Virtual Desktop용 Azure Monitor 퍼블릭 미리 보기를 사용할 수 있습니다. 이 새로운 기능에는 IT 전문가가 Azure Virtual Desktop 환경을 이해하는 데 도움을 주는 Azure Monitor 통합 문서 위에 구축된 강력한 대시보드가 포함되어 있습니다. 자세한 내용은 [블로그의 공지](https://techcommunity.microsoft.com/t5/windows-virtual-desktop/azure-monitor-for-windows-virtual-desktop-public-preview/m-p/1946587)를 확인하세요. 

### <a name="azure-resource-manager-template-change"></a>Azure Resource Manager 템플릿 변경 

최신 업데이트에서는 호스트 풀을 만들고 프로비저닝하기 위해 Azure Resource Manager 템플릿에서 모든 공용 IP 주소 매개 변수를 제거했습니다. 배포를 안전하게 유지하기 위해 Azure Virtual Desktop에 공용 IP를 사용하지 않는 것이 좋습니다. 배포가 공용 IP에 의존하는 경우 개인 IP를 대신 사용하도록 다시 구성해야 합니다. 그렇지 않으면 배포가 제대로 작동하지 않습니다.

### <a name="msix-app-attach-public-preview"></a>MSIX 앱 연결 공개 미리 보기 

MSIX 앱 연결은 이번 달에 공개 미리 보기를 시작한 다른 서비스입니다. MSIX 앱 연결은 Azure Virtual Desktop 세션 호스트 VM에 MSIX 애플리케이션을 동적으로 제공하는 서비스입니다. 자세한 내용은 [블로그의 공지](https://techcommunity.microsoft.com/t5/windows-virtual-desktop/msix-app-attach-azure-portal-integration-public-preview/m-p/1986231)를 확인하세요. 

### <a name="screen-capture-protection"></a>화면 캡처 보호 

이번 달에는 화면 캡처 보호를 위한 공개 미리 보기의 시작도 표시되어 있습니다. 이 기능을 사용하여 클라이언트 엔드포인트에서 중요한 정보가 캡처되지 않도록 할 수 있습니다. [이 페이지](https://aka.ms/WVDScreenCaptureProtection)로 이동하여 화면 캡처 보호를 사용해 보세요.  

### <a name="built-in-roles"></a>기본 제공 역할

관리자 권한으로 Azure Virtual Desktop에 대한 새로운 기본 제공 역할을 추가했습니다. 자세한 내용은 [Azure Virtual Desktop에 대한 기본 제공 역할](rbac.md)을 참조하세요. 

### <a name="application-group-limit-increase"></a>애플리케이션 그룹 제한 증가

Azure Active Directory 테넌트당 기본 애플리케이션 그룹 제한을 200개 그룹으로 늘렸습니다.

### <a name="client-updates-for-december-2020"></a>2020년 12월 클라이언트 업데이트

다음 클라이언트의 새 버전을 릴리스했습니다. 

- Android
- macOS
- Windows

클라이언트 업데이트에 대한 자세한 내용은 [클라이언트 업데이트](whats-new.md#client-updates)를 참조하세요.

## <a name="november-2020"></a>2020년 11월

### <a name="azure-portal-experience"></a>Azure Portal 환경

Azure Portal 사용자 환경에서 다음과 같은 두 가지 버그를 해결했습니다.

- 데스크톱 애플리케이션 이름을 "VM 추가" 워크플로에서 더 이상 덮어쓰지 않습니다.
- 이제 세션 호스트가 확장 집합의 일부인 경우 세션 호스트 탭이 로드됩니다.

### <a name="fslogix-client-version-2009"></a>FSLogix 클라이언트 버전 2009 

여러 수정 및 개선 사항이 있는 새 버전의 FSLogix 클라이언트를 출시했습니다. [블로그 게시물](https://social.msdn.microsoft.com/Forums/en-US/defe5828-fba4-4715-a68c-0e4d83eefa6b/release-notes-for-fslogix-apps-release-2009-29762130127?forum=FSLogix)에서 자세히 알아보세요.

### <a name="rdp-shortpath-public-preview"></a>RDP Shortpath 공개 미리 보기

RDP Shortpath는 지점 및 사이트 간과 사이트 간 VPN 및 ExpressRoute를 사용하여 Azure Virtual Desktop 세션 호스트에 직접 연결하는 것을 소개합니다. URCP 전송 프로토콜도 소개합니다. RDP Shortpath는 사용자 환경을 개선하기 위해 대기 시간과 네트워크 홉을 줄이도록 설계되었습니다. [Azure Virtual Desktop RDP Shortpath](shortpath.md)에서 자세히 알아보세요.

### <a name="azdesktopvirtualization-version-201"></a>Az.DesktopVirtualization 버전 2.0.1

Azure Virtual Desktop cmdlet 2.0.1 버전을 출시했습니다. 이 업데이트에는 MSIX 앱 연결을 관리할 수 있는 cmdlet이 포함되어 있습니다. [PowerShell 갤러리](https://www.powershellgallery.com/packages/Az.DesktopVirtualization/2.0.1)에서 새 버전을 다운로드할 수 있습니다.

### <a name="azure-advisor-updates"></a>Azure Advisor 업데이트

이제 Azure Advisor는 Azure Virtual Desktop의 근접 지침에 대한 새로운 권장 사항과 깊이 우선 부하가 분산된 호스트 풀의 성능을 최적화하기 위한 새로운 권장 사항을 제공합니다. [Azure 웹 사이트](https://azure.microsoft.com/updates/new-recommendations-from-azure-advisor/)에서 자세히 알아보세요.

## <a name="october-2020"></a>2020년 10월

2020년 10월에 변경된 내용은 다음과 같습니다.

### <a name="improved-performance"></a>성능 향상

- 다음 Azure 지역에서 연결 대기 시간을 줄여 성능을 최적화했습니다.
    - 스위스
    - 캐나다

이제 [환경 측정기](https://azure.microsoft.com/services/virtual-desktop/assessment/)를 사용하여 이러한 영역에서 사용자 환경 품질을 추정할 수 있습니다.

### <a name="azure-government-cloud-availability"></a>Azure Government 클라우드 가용성

이제 Azure Government 클라우드가 일반 공급됩니다. [블로그 게시물](https://azure.microsoft.com/updates/windows-virtual-desktop-is-now-generally-available-in-the-azure-government-cloud/)에서 자세히 알아보세요.

### <a name="azure-virtual-desktop-azure-portal-updates"></a>Azure Virtual Desktop Azure Portal 업데이트

Azure Virtual Desktop Azure Portal에서 다음과 같은 기능을 업데이트했습니다.

- 사용자가 "세션" 탭을 열 수 없도록 하는 resourceID 오류를 수정했습니다.
- "세션 호스트" 탭에서 UI를 간소화했습니다.
- RDP 속성에서 "기본값", "유용성" 및 "기본값 복원" 설정을 수정했습니다.
- 모든 탭에서 일관된 "제거" 및 "삭제" 함수를 만들었습니다.
- 이제 포털에서 "앱 추가" 워크플로의 앱 이름 유효성을 검사합니다.
- 세션 호스트 내보내기 데이터가 열에 정렬되지 않은 문제를 해결했습니다.
- 포털이 사용자 세션을 검색할 수 없는 문제를 해결했습니다.
- 다른 리소스 그룹에서 가상 머신을 생성할 때 발생하는 세션 호스트 검색 문제를 해결했습니다.
- 활성 세션과 연결이 끊긴 세션을 모든 나열하도록 "세션 호스트" 탭을 업데이트했습니다.
- 이제 "애플리케이션" 탭에 페이지가 있습니다.
- "명령줄 필요" 텍스트가 "애플리케이션 목록" 탭에 올바르게 표시되지 않는 문제를 해결했습니다.
- 독일어 버전의 공유 이미지 갤러리를 사용하는 동안 포털에서 호스트 풀 또는 가상 머신을 배포할 수 없는 문제를 해결했습니다.

### <a name="client-updates-for-october-2020"></a>2020년 10월 클라이언트 업데이트

새 버전의 클라이언트를 릴리스했습니다. 자세한 내용은 다음 문서를 참조하세요.

- [Windows](/windows-server/remote/remote-desktop-services/clients/windowsdesktop-whatsnew)
- [iOS](/windows-server/remote/remote-desktop-services/clients/ios-whatsnew)

기타 클라이언트에 대한 자세한 내용은 [클라이언트 업데이트](#client-updates)를 참조하세요.

## <a name="september-2020"></a>2020년 9월

2020년 9월에 변경된 내용은 다음과 같습니다.

- 다음 Azure 지역에서 연결 대기 시간을 줄여 성능을 최적화했습니다.
    - 독일
    - 남아프리카(유효성 검사 환경에만 해당)

이제 [환경 측정기](https://azure.microsoft.com/services/virtual-desktop/assessment/)를 사용하여 이러한 영역에서 사용자 환경 품질을 추정할 수 있습니다.

- Azure Virtual Desktop용 Windows Desktop 클라이언트의 버전 1.2.1364를 출시했습니다. 이 업데이트에서는 다음과 같이 변경했습니다.
    - Windows 7에서 SSO(Single Sign-On)가 작동하지 않는 문제가 해결되었습니다.
    - 다른 앱에서 오디오 스트림이 전용 모드로 열려 있는 동안 팀에 대한 미디어 최적화를 사용하도록 설정한 사용자가 팀 회의를 호출하거나 조인하려고 할 때 클라이언트의 연결이 끊어지는 문제를 해결했습니다.
    - 팀에 대한 미디어 최적화를 사용하도록 설정한 경우 팀에서 오디오나 비디오 디바이스를 열거하지 않는 문제를 해결했습니다.
    - "설정과 관련하여 도움이 필요한가요?" 링크가 데스크톱 설정 페이지에 추가되었습니다.
    - 고대비 어두운 테마를 사용할 때 발생하는 "구독" 단추와 관련된 문제가 해결되었습니다.
    
- 사용자의 놀라운 도움 덕분에 Microsoft Store 원격 데스크톱 클라이언트에 대한 두 가지 중요한 문제를 해결했습니다. 클라이언트의 단계별 릴리스를 전 세계의 더 많은 사용자에게 확대함에 따라 계속해서 피드백을 검토하고 문제를 해결할 것입니다.
    
- Azure Portal에서 VM을 배포에 추가하기 위한 워크플로의 일부로 VM 위치, 이미지, 리소스 그룹, 접두사 이름, 네트워크 구성을 변경할 수 있는 새로운 기능을 추가했습니다.

- IT 전문가는 이제 Microsoft Endpoint Manager를 사용하여 하이브리드 Azure Active Directory에 조인된 Windows 10 Enterprise VM을 관리할 수 있습니다. 자세한 내용은 [블로그 게시물](https://techcommunity.microsoft.com/t5/microsoft-endpoint-manager-blog/microsoft-endpoint-manager-announces-support-for-windows-virtual/ba-p/1681048)을 참조하세요.

## <a name="august-2020"></a>2020년 8월

2020년 8월에 변경된 내용은 다음과 같습니다.

- 다음 Azure 지역에서 연결 대기 시간을 줄이기 위해 성능을 개선했습니다. 

    - 영국
    - 프랑스
    - 노르웨이
    - 대한민국

   [경험 예측 도구](https://azure.microsoft.com/services/virtual-desktop/assessment/)를 사용하여 이러한 변경 내용이 사용자에게 미치는 영향에 대한 일반적인 개념을 파악할 수 있습니다.

- 이제 Microsoft Store 원격 데스크톱 클라이언(v10.2.1522+)를 일반적으로 사용할 수 있습니다. 이 버전의 Microsoft Store 원격 데스크톱 클라이언트는 Azure Virtual Desktop과 호환됩니다. 또한 향상된 사용자 환경을 위해 새로 고쳐진 UI 흐름을 도입했습니다. 이 업데이트에는 흐름 디자인, 밝은 모드와 어두운 모드 및 기타 많은 흥미로운 변경 내용이 포함됩니다. 또한 iOS, macOS 및 Android 클라이언트와 동일한 기본 RDP(원격 데스크톱 프로토콜) 엔진을 사용하도록 클라이언트를 다시 작성했습니다. 이를 통해 모든 플랫폼에서 새로운 기능을 더 빠르게 제공할 수 있습니다. [클라이언트를 다운로드](https://www.microsoft.com/p/microsoft-remote-desktop/9wzdncrfj3ps?rtc=1&activetab=pivot:overviewtab)하여 사용해 보세요!

- 클라이언트가 채팅, 채널 및 일정에서 UTC 표준 시간대만 표시하는 Teams Desktop 클라이언트(버전 1.3.00.21759)의 문제를 해결했습니다. 업데이트된 클라이언트는 이제 원격 세션의 표준 시간대를 대신 표시합니다.

- Azure Advisor는 이제 Azure Virtual Desktop의 일부입니다. Azure Portal을 통해 Azure Virtual Desktop에 액세스하면 Azure Virtual Desktop 환경을 최적화하기 위한 권장 사항을 확인할 수 있습니다. [Azure Advisor](azure-advisor.md)에서 자세히 알아보세요.

- 이제 Azure CLI는 Azure Virtual Desktop 배포를 자동화하는 데 도움이 되는 Azure Virtual Desktop(`az desktopvirtualization`)을 지원합니다. 확장 명령 목록은 [desktopvirtualization](/cli/azure/desktopvirtualization)을 체크 아웃하세요.

- Azure Virtual Desktop Azure Resource Manager 인터페이스와 완벽하게 호환되도록 배포 템플릿을 업데이트했습니다. [GitHub](https://github.com/Azure/RDS-Templates/tree/master/ARM-wvd-templates)에서 템플릿을 찾을 수 있습니다.

- Azure Virtual Desktop US Gov 포털은 현재 퍼블릭 미리 보기로 제공됩니다. 자세한 내용은 [공지 사항](https://azure.microsoft.com/updates/windows-virtual-desktop-is-now-available-in-the-azure-government-cloud-in-preview/)을 참조하세요.

## <a name="july-2020"></a>2020년 7월  

7월은 Azure 리소스 관리 통합이 포함된 Azure Virtual Desktop이 일반 공급되었습니다.

이 새 릴리스에서 변경된 내용은 다음과 같습니다. 

- "2019년 가을 릴리스"는 "Azure Virtual Desktop(클래식)"이라고 하며, "2020년 봄 릴리스"는 이제 "Azure Virtual Desktop"입니다. 자세한 내용은 [이 블로그 게시물](https://azure.microsoft.com/blog/new-windows-virtual-desktop-capabilities-now-generally-available/)을 참조하세요. 

새 기능에 대해 자세히 알아보려면 [이 블로그 게시물](https://techcommunity.microsoft.com/t5/itops-talk-blog/windows-virtual-desktop-spring-update-enters-public-preview/ba-p/1340245)을 확인하세요. 

### <a name="autoscaling-tool-update"></a>자동 크기 조정 도구 업데이트

미리 보기 상태에 있던 최신 버전의 자동 크기 조정 도구는 이제 일반 공급됩니다. 이 도구는 Azure 자동화 계정 및 Azure Logic App을 사용하여 호스트 풀 내에서 세션 호스트 VM(가상 머신)을 자동으로 종료하고 다시 시작하여 인프라 비용을 절감합니다. [Azure Automation을 사용하여 세션 호스트 크기 조정](set-up-scaling-script.md)에서 자세히 알아보세요.

### <a name="azure-portal"></a>Azure portal

이제 Azure Virtual Desktop에서 Azure Portal을 사용하여 다음 작업을 수행할 수 있습니다. 

- 개인 데스크톱 세션 호스트에 직접 사용자 할당  
- 호스트 풀의 유효성 검사 환경 설정 변경 

### <a name="diagnostics"></a>진단

Log Analytics 작업 영역에 대해 미리 작성된 새로운 쿼리를 릴리스했습니다. 쿼리에 액세스하려면 **로그** 로 이동하고 **범주** 에서 **Azure Virtual Desktop** 을 선택합니다. [진단 기능에 대해 Log Analytics 사용](diagnostics-log-analytics.md)에서 자세히 알아보세요.

### <a name="update-for-remote-desktop-client-for-android"></a>Android용 원격 데스크톱 클라이언트 업데이트

이제 [Android용 원격 데스크톱 클라이언트](https://play.google.com/store/apps/details?id=com.microsoft.rdc.androidx)에서 Azure Virtual Desktop 연결을 지원합니다. 10.0.7 버전부터 Android 클라이언트는 향상된 사용자 환경을 위한 새 UI를 제공합니다. 또한 클라이언트는 Azure Virtual Desktop 작업 영역을 구독할 때 조건부 액세스를 사용하도록 Android 디바이스에서 Microsoft Authenticator와 통합됩니다.  

이제 이전 버전의 원격 데스크톱 클라이언트를 "원격 데스크톱 8"이라고 합니다. 이전 버전의 클라이언트에 있는 기존 연결은 새 클라이언트로 원활하게 전송됩니다. 새 클라이언트는 iOS 및 macOS 클라이언트와 동일한 기본 RDP 코어 엔진에 다시 작성되었으며, 모든 플랫폼에서 새로운 기능의 빠른 릴리스를 제공합니다. 

### <a name="teams-update"></a>팀 업데이트

Azure Virtual Desktop에 대한 Microsoft Teams에 개선 사항이 있습니다. 가장 중요한 점은 Azure Virtual Desktop은 이제 Windows 데스크톱 클라이언트에 대한 오디오 및 비디오 최적화를 지원한다는 것입니다. 리디렉션은 통화 및 모임에서 오디오 또는 비디오를 사용할 때 사용자 간에 직접 경로를 만들어 대기 시간을 단축시킵니다. 거리를 줄이면 홉 수가 줄어들기 때문에 호출 모양과 소리가 더 부드러워집니다. [Azure Virtual Desktop에서 Teams 사용](./teams-on-avd.md)에서 자세히 알아보세요.

## <a name="june-2020"></a>2020년 6월

지난달에는 Azure Resource Manager가 통합된 Azure Virtual Desktop을 미리 보기로 도입되었습니다. 이 업데이트에는 여러 흥미로운 새로운 기능들이 포함되어 있습니다. 이 Azure Virtual Desktop 버전의 새로운 기능은 다음과 같습니다.

### <a name="azure-virtual-desktop-is-now-integrated-with-azure-resource-manager"></a>이제 Azure Virtual Desktop이 Azure Resource Manager와 통합되었습니다.

이제 Azure Virtual Desktop이 Azure Resource Manager에 통합되었습니다. 최신 업데이트에서는 이제 모든 Azure Virtual Desktop 개체가 Azure Resource Manager 리소스입니다. 또한 이 업데이트는 Azure RBAC(Azure 역할 기반 액세스 제어)와도 통합됩니다. 자세한 내용은 [Azure Resource Manager란?](../azure-resource-manager/management/overview.md)을 참조하세요.

이러한 변화가 미치는 영향은 다음과 같습니다.

- 이제 Azure Virtual Desktop이 Azure Portal과 통합되었습니다. 즉, 포털에서 직접 모든 항목을 관리할 수 있습니다. PowerShell, 웹앱 또는 타사 도구는 필요하지 않습니다. 시작하려면 [Azure Portal을 사용하여 호스트 풀 만들기](create-host-pools-azure-marketplace.md)에 있는 자습서를 확인하세요.

- 이 업데이트 전에는 개별 사용자에게만 RemoteApps 및 데스크톱을 게시할 수 있었습니다. 이제 Azure Resource Manager를 사용하면 Azure Active Directory 그룹에 리소스를 게시할 수 있습니다.

- 이전 버전의 Azure Virtual Desktop에는 테넌트 또는 호스트 풀에 할당할 수 있는 네 가지 기본 제공 관리자 역할이 있습니다. 이러한 역할은 이제 [Azure RBAC(Azure 역할 기반 액세스 제어)](../role-based-access-control/overview.md)에 있습니다. 모든 Azure Virtual Desktop Azure Resource Manager 개체에 이러한 역할을 적용할 수 있으며, 이를 통해 완전하고 다양한 위임 모델을 사용할 수 있습니다.

- 이 업데이트에서는 더 이상 호스트 풀을 확장하기 위해 Azure Marketplace 또는 GitHub 템플릿을 반복적으로 실행할 필요가 없습니다. 호스트 풀을 확장하려면 Azure Portal의 호스트 풀로 이동하고 **+ 추가** 를 선택하여 추가 세션 호스트를 배포하기만 하면 됩니다.

- 이제 호스트 풀 배포가 [Azure 공유 이미지 갤러리](../virtual-machines/shared-image-galleries.md)와 완전히 통합되었습니다. 공유 이미지 갤러리는 이미지 버전 관리를 비롯한 VM(가상 머신) 이미지 정의를 저장하는 별도의 Azure 서비스입니다. 또한 글로벌 복제를 사용하여 로컬 배포를 위해 이미지를 복사하여 다른 Azure 지역으로 보낼 수 있습니다.

- PowerShell 또는 진단 서비스 웹앱을 통해 수행되는 함수 모니터링은 이제 Azure Portal의 Log Analytics로 이동되었습니다. 또한 이제 두 가지 옵션을 통해 보고서를 시각화할 수 있습니다. Kusto 쿼리를 실행하고 통합 문서를 사용하여 시각적 보고서를 만들 수 있습니다.

- 더 이상 Azure Virtual Desktop을 사용하도록 Azure AD(Azure Active Directory) 동의를 완료할 필요가 없습니다. 이 업데이트에서 Azure 구독의 Azure AD 테넌트는 사용자를 인증하고 관리자를 위한 Azure RBAC 제어를 제공합니다.

### <a name="powershell-support"></a>PowerShell 지원

이 업데이트를 통해 Azure PowerShell Az 모듈에 새 AzWvd cmdlet을 추가했습니다. 이 새 모듈은 .NET Core에서 실행되는 PowerShell Core에서 지원됩니다.

모듈을 설치하려면 [Azure Virtual Desktop용 PowerShell 모듈 설치](powershell-module.md)에 나온 지침을 따르세요.

[AzWvd PowerShell 참조](/powershell/module/az.desktopvirtualization/#desktopvirtualization)에서 사용 가능한 명령 목록을 확인할 수도 있습니다.

새 기능에 대한 자세한 내용은 [블로그 게시물](https://techcommunity.microsoft.com/t5/itops-talk-blog/windows-virtual-desktop-spring-update-enters-public-preview/ba-p/1340245)을 확인하세요.

### <a name="additional-gateways"></a>추가 게이트웨이

연결 대기 시간을 줄이기 위해 새 게이트웨이 클러스터를 남아프리카 공화국에 추가했습니다.

### <a name="microsoft-teams-on-azure-virtual-desktop-preview"></a>Azure Virtual Desktop(미리 보기)의 Microsoft Teams

Azure Virtual Desktop용 Microsoft Teams의 기능이 일부 개선되었습니다. 가장 중요한 점은 Azure Virtual Desktop은 이제 호출에 대한 오디오 및 시각적 리디렉션을 지원한다는 것입니다. 리디렉션은 오디오 또는 비디오를 사용하여 호출할 때 사용자 간에 직접 경로를 만들어 대기 시간을 단축시킵니다. 거리를 줄이면 홉 수가 줄어들기 때문에 호출 모양과 소리가 더 부드러워집니다.

자세한 내용은 [블로그 게시물](https://azure.microsoft.com/updates/windows-virtual-desktop-media-optimization-for-microsoft-teams-is-now-available-in-public-preview/)을 참조하세요.

## <a name="next-steps"></a>다음 단계

[Microsoft 365 Azure Virtual Desktop 로드맵](https://www.microsoft.com/microsoft-365/roadmap?filters=Windows%20Virtual%20Desktop)에서 향후 계획에 대해 알아봅니다.
