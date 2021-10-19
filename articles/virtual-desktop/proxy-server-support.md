---
title: 프록시 서버 지침 Azure Virtual Desktop - Azure
description: Azure Virtual Desktop 배포에서 프록시 서버를 사용하기 위한 몇 가지 지침과 권장 사항입니다.
author: Heidilohr
ms.topic: conceptual
ms.date: 04/27/2021
ms.author: helohr
ms.reviewer: denisgun
manager: femila
ms.openlocfilehash: d90f6106d94777c58418a209097d84fe69458d06
ms.sourcegitcommit: 92889674b93087ab7d573622e9587d0937233aa2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/19/2021
ms.locfileid: "130177577"
---
# <a name="proxy-server-guidelines-for-azure-virtual-desktop"></a>Azure Virtual Desktop용 프록시 서버 지침

이 문서에서는 Azure Virtual Desktop에서 프록시 서버를 사용하는 방법을 보여줍니다. 이 문서의 권장 사항은 Azure Virtual Desktop 인프라, 클라이언트 및 세션 호스트 에이전트 사이의 연결에만 적용됩니다. 이 문서에서는 Office, Windows 10, FSLogix 또는 기타 Microsoft 애플리케이션용 네트워크 연결은 다루지 않습니다.

## <a name="what-are-proxy-servers"></a>프록시 서버가 무엇일까요?

Azure Virtual Desktop 트래픽에 대한 프록시는 무시하는 것이 좋습니다. 트래픽이 이미 암호화되어 있기 때문에 프록시는 Azure Virtual Desktop의 보안을 강화하지 않습니다. 연결 보안에 대한 자세한 정보는 [연결 보안](network-connectivity.md#connection-security)을 참조하세요. 

프록시 서버 대부분은 장기 실행 WebSocket 연결을 지원하도록 설계되어 있지 않으며 연결 안정성에 영향을 줄 수 있습니다. Azure Virtual Desktop에서는 여러 장기 연결을 사용하기 때문에 프록시 서버 확장성에도 문제가 발생합니다. 프록시 서버를 사용하는 경우 크기가 맞아야 이러한 연결을 실행할 수 있습니다.

프록시 서버의 지역이 호스트에서 멀리 떨어져 있으면 사용자 연결 시 대기 시간이 더 길어집니다. 대기 시간이 길어진다는 것은 저속 연결과 사용자 환경의 저하를 의미합니다. 그래픽, 오디오 또는 입력 디바이스와 대기 시간이 짧은 상호 작용이 필요한 시나리오에서 특히 그렇습니다. 프록시 서버를 사용해야 하는 경우에는 Azure Virtual Desktop 에이전트 및 클라이언트와 동일한 지역에 서버를 두어야 합니다.

Azure Virtual Desktop 트래픽의 유일한 경로로 프록시 서버를 구성하는 경우 RDP(원격 데스크톱 프로토콜) 데이터는 UDP(사용자 데이터그램 프로토콜) 대신 TCP(전송 컨트롤 프로토콜)를 통해 강제 적용됩니다. 이 이동은 원격 연결의 시각적 품질 및 응답성을 낮춥니다.

요약하자면, Azure Virtual Desktop에서 프록시 서버를 사용하지 않는 것이 좋습니다. 이는 대기 시간 저하와 패킷 손실의 성능 관련 문제가 발생하기 때문입니다. 

## <a name="bypassing-a-proxy-server"></a>프록시 서버 바이패스

조직의 네트워크 및 보안 정책에 웹 트래픽용 프록시 서버가 필요한 경우 프록시 서버를 통해 트래픽을 계속 라우팅하는 동시에 Azure Virtual Desktop 연결을 무시하도록 환경을 구성할 수 있습니다. 그러나 각 조직의 정책은 고유하므로 어떤 방법이 배포에 있어 다른 방법보다 더 효율적으로 작동할 수 있습니다. 환경에서 성능 및 안정성 손실을 방지하기 위해 시도할 수 있는 몇 가지 구성 방법은 다음과 같습니다.

- Azure 방화벽의 Azure 서비스 태그
- 프록시 서버를 사용하지 않는 프록시 자동 구성(. PAC) 파일
- 로컬 프록시 구성의 바이패스 목록
- 사용자 단위 구성에 프록시 서버 사용
- 프록시를 통해 서비스 트래픽을 유지하면서 RDP 연결에 RDP Shortpath 사용

## <a name="recommendations-for-using-proxy-servers"></a>프록시 서버 사용에 대한 권장 사항

일부 조직에서는 추적 또는 패킷 검사를 위해 모든 사용자 트래픽이 프록시 서버를 통과하도록 요구합니다. 이 섹션에서는 이러한 경우에 환경을 구성하는 방법을 설명합니다.

### <a name="use-proxy-servers-in-the-same-azure-geography"></a>동일한 Azure 지리에서 프록시 서버 사용

프록시 서버를 사용하는 경우 Azure Virtual Desktop 인프라와의 모든 통신을 처리하고 가장 가까운 Azure Front Door에 대한 DNS 확인 및 Anycast 라우팅을 수행합니다. 프록시 서버가 Azure 지리에서 멀리 있거나 분산되어 있는 경우 지리적 해상도는 정확하지 않습니다. 지리적 해상도가 떨어진다는 것은 연결이 더 멀리 떨어진 Azure Virtual Desktop 클러스터로 라우팅된다는 의미입니다. 이 문제를 방지하려면 Azure Virtual Desktop 클러스터와 지리적으로 가까운 프록시 서버만 사용합니다.

### <a name="use-rdp-shortpath-for-managed-networks-for-desktop-connectivity"></a>데스크톱 연결을 위해 관리형 네트워크에 RDP Shortpath 사용

관리형 네트워크에 RDP Shortpath를 사용하도록 설정하면 가능한 경우 RDP 데이터가 프록시 서버를 무시합니다. 프록시 서버를 우회하면 UDP 전송을 사용하는 동안 최적의 라우팅이 보장됩니다. 기타 Azure Virtual Desktop 트래픽(예: 브로커링, 오케스트레이션 및 진단)은 계속 프록시 서버를 통과합니다.

### <a name="dont-use-ssl-termination-on-the-proxy-server"></a>프록시 서버에서 SSL 종료를 사용하지 않음

SSL(Secure Sockets Layer) 종료는 Azure Virtual Desktop 구성 요소의 보안 인증서를 프록시 서버에서 생성된 인증서로 대체합니다. 이 프록시 서버 기능을 사용하면 프록시 서버에서 HTTPS 트래픽에 대한 패킷 검사를 수행할 수 있습니다. 하지만 패킷 검사는 서비스 응답 시간을 늘려 사용자가 로그인하는 데 시간이 더 오래 걸립니다. 역방향 연결 시나리오의 경우에는 역방향 연결 RDP 트래픽이 이진값이고 추가 수준의 암호화를 사용하기 때문에 RDP 트래픽 패킷 검사가 필요하지 않습니다.

SSL 검사를 사용하도록 프록시 서버를 구성하는 경우 SSL 검사를 변경한 후에는 서버를 원래 상태로 되돌릴 수 없다는 점에 주의해야 합니다. SSL 검사를 사용하도록 설정한 상태에서 Azure Virtual Desktop 환경의 항목이 작동을 중지하는 경우에는 SSL 검사를 사용하지 않도록 설정하고 지원 사례를 열기 전에 다시 시도해야 합니다. SSL 검사를 수행하면 에이전트와 서비스 간의 신뢰할 수 있는 연결을 방해하므로 Azure Virtual Desktop 에이전트의 작동이 중지될 수도 있습니다.

### <a name="dont-use-proxy-servers-that-need-authentication"></a>인증이 필요한 프록시 서버 사용 안 함

세션 호스트의 Azure Virtual Desktop 구성 요소는 운영 체제의 컨텍스트에서 실행되므로 인증이 필요한 프록시 서버를 지원하지 않습니다. 프록시 서버 인증이 필요한 경우 연결에 실패합니다.

### <a name="plan-for-the-proxy-server-network-capacity"></a>프록시 서버 네트워크 용량 계획

프록시 서버에는 용량 한도가 있습니다. 레귤러 HTTP 트래픽과 달리 RDP 트래픽은 시간이 오래 걸리는 대화형 연결을 사용하여 양방향이며 많은 대역폭을 이용합니다. 프록시 서버를 설정하기 전에 서버의 처리량이 얼마나 되는지를 프록시 서버 공급업체에 문의하세요. 또한 한 번에 실행할 수 있는 프록시 세션의 수를 확인해야 합니다. 프록시 서버를 배포한 후 Azure Virtual Desktop 트래픽의 병목 상태에서 리소스 사용을 신중하게 모니터링합니다.

### <a name="proxy-servers-for-windows-7-session-hosts"></a>Windows 7 세션 호스트에 대한 프록시 서버

Windows 7에서 실행되는 세션 호스트는 역방향 연결 RDP 데이터에 대한 프록시 서버 연결을 지원하지 않습니다. 세션 호스트가 Azure Virtual Desktop 게이트웨이에 직접 연결할 수 없는 경우 연결이 작동하지 않습니다.

### <a name="proxy-servers-and--teams-optimization"></a>프록시 서버 및 팀 최적화

Azure Virtual Desktop은 팀 최적화를 위한 프록시 서버를 지원하지 않습니다.

## <a name="session-host-configuration-recommendations"></a>세션 호스트 구성 권장 사항

세션 호스트 수준 프록시 서버를 구성하려면 시스템 전체 프록시를 사용하도록 설정해야 합니다. 시스템 전체 구성은 세션 호스트에서 실행되는 모든 OS 구성 요소 및 애플리케이션에 영향을 줍니다. 다음 섹션에서는 시스템 전체 프록시를 구성하기 위한 권장 사항을 설명합니다.
 
### <a name="use-the-web-proxy-auto-discovery-wpad-protocol"></a>WPAD(웹 프록시 자동 검색) 프로토콜 사용

Azure Virtual Desktop 에이전트는 자동으로 WPAD(웹 프록시 자동 검색) 프로토콜을 사용하여 네트워크에서 프록시 서버를 찾으려고 시도합니다. 위치를 시도하는 동안 에이전트는 DNS(도메인 이름 서버)에서 **wpad.domainsuffix** 라는 파일을 검색합니다. 에이전트가 DNS에서 파일을 찾은 경우에는 **wpad.dat** 라는 파일에 대해 HTTP 요청을 수행합니다. 응답은 아웃바운드 프록시 서버를 선택하는 프록시 구성 스크립트가 됩니다.

WPAD에 DNS 확인을 사용하도록 네트워크를 구성하려면 [Internet Explorer 11 설정 자동 검색](/internet-explorer/ie11-deploy-guide/auto-detect-settings-for-ie11)의 지침을 따르세요. [Set-DnsServerGlobalQueryBlockList](/powershell/module/dnsserver/set-dnsserverglobalqueryblocklist?view=windowsserver2019-ps&preserve-view=true)의 지시에 따라 DNS 서버 전체 쿼리 차단 목록에서 WPAD 확인을 허용하는지 확인합니다.

### <a name="manually-set-a-device-wide-internet-explorer-proxy"></a>수동으로 디바이스 수준 Internet Explorer 프록시 설정

디바이스 수준 프록시 또는 프록시 자동 구성(.PAC) 파일을 설정할 수 있으며, [네트워크 프록시 CSP](/windows/client-management/mdm/networkproxy-csp)를 사용하여 모든 대화형, LocalSystem 및 NetworkService 사용자에게 적용됩니다. 

다음 예제와 같이 **bitsadmin** 명령을 실행하여 로컬 시스템 계정에 대한 프록시 서버를 구성할 수도 있습니다. 

```console
bitsadmin /util /setieproxy LOCALSYSTEM AUTOSCRIPT http://server/proxy.pac 
```

## <a name="client-side-proxy-support"></a>클라이언트 쪽 프록시 지원

Azure Virtual Desktop 클라이언트는 시스템 설정 또는 [네트워크 프록시 CSP](/windows/client-management/mdm/networkproxy-csp)를 사용하여 구성된 프록시 서버를 지원합니다.

### <a name="support-for-clients-running-on-windows-7"></a>Windows 7에서 실행되는 클라이언트에 대한 지원

Windows 7에서 실행되는 클라이언트는 역방향 연결 RDP 데이터에 대한 프록시 서버 연결을 지원하지 않습니다. 클라이언트에서 Azure Virtual Desktop 게이트웨이에 직접 연결할 수 없는 경우 연결이 작동하지 않습니다.

### <a name="azure-virtual-desktop-client-support"></a>Azure Virtual Desktop 클라이언트 지원

다음 테이블에서는 프록시 서버를 지원하는 Azure Virtual Desktop 클라이언트를 보여줍니다.

| 클라이언트 이름 | 프록시 서버 지원 |
|---|---|
| Windows 데스크톱 | Yes |
| 웹 클라이언트 | Yes |
| Android | No |
| iOS | 예 |
| macOS | 예 |
| Windows 스토어 | Yes |

Linux 기반 씬 클라이언트의 프록시 지원에 대한 자세한 정보는 [씬 클라이언트 지원](./user-documentation/linux-overview.md)을 참조하세요.

## <a name="support-limitations"></a>지원 제한 사항

프록시 서버 역할을 하는 타사 서비스 및 애플리케이션이 많습니다. 이러한 타사 서비스에는 분산 차세대 방화벽, 웹 보안 시스템 및 기본 프록시 서버가 포함됩니다. 모든 구성이 Azure Virtual Desktop과 호환된다고 보장할 수는 없습니다. Microsoft는 프록시 서버를 통해 설정된 연결에 대해서만 제한적으로 지원합니다. 프록시 서버를 사용하는 동안 연결 문제가 발생 하는 경우 Microsoft 지원에서 프록시 바이패스를 구성한 다음 문제를 재현하는 것이 좋습니다.

## <a name="next-steps"></a>다음 단계

Azure Virtual Desktop 배포를 안전하게 유지하는 방법에 대한 자세한 정보는 [보안 가이드](security-guide.md)를 참조하세요.
