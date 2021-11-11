---
title: 관리형 네트워크에 대한 Azure Virtual Desktop RDP Shortpath
titleSuffix: Azure
description: Azure Virtual Desktop에 대한 관리형 네트워크에 대한 RDP Shortpath를 설정하는 방법입니다.
author: gundarev
ms.topic: conceptual
ms.date: 10/18/2021
ms.author: denisgun
ms.openlocfilehash: 43f4a8a1f467637f3a3703704361db525912f750
ms.sourcegitcommit: c434baa76153142256d17c3c51f04d902e29a92e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/10/2021
ms.locfileid: "132179248"
---
# <a name="azure-virtual-desktop-rdp-shortpath-for-managed-networks"></a>관리형 네트워크에 대한 Azure Virtual Desktop RDP Shortpath

관리형 네트워크에 대한 RDP Shortpath는 원격 데스크톱 클라이언트와 세션 호스트 간에 직접 UDP 기반 전송을 설정하는 Azure Virtual Desktop의 기능입니다. RDP는 이 전송을 사용하여 원격 데스크톱과 RemoteApp을 제공하고 더 나은 안정성과 일관성 있는 대기 시간을 제공합니다.

## <a name="key-benefits"></a>주요 이점

* RDP Shortpath 전송은 매우 효율적인 [URCP(Universal Rate Control Protocol)](https://www.microsoft.com/en-us/research/publication/urcp-universal-rate-control-protocol-for-real-time-communication-applications/)를 기반으로 합니다. URCP는 네트워크 상태에 대한 활성 모니터링으로 UDP를 개선하며 공평하고 완전한 링크 사용률을 제공합니다. URCP는 원격 데스크톱의 필요에 따라 낮은 지연 및 손실 수준으로 작동합니다. URCP는 네트워크 매개 변수를 동적으로 학습하고 속도 제어 메커니즘을 사용하는 프로토콜을 제공하여 성능을 극대화합니다.
* RDP Shortpath는 원격 데스크톱 클라이언트와 세션 호스트 간의 직접 연결을 설정합니다. 직접 연결은 Azure Virtual Desktop 게이트웨이에 대한 종속성을 줄이고, 연결의 안정성을 향상하며, 각 사용자 세션에 사용 가능한 대역폭을 늘립니다.
* 추가 릴레이를 제거하면 왕복 시간이 단축되어 대기 시간이 중요한 애플리케이션 및 입력 메서드를 통해 사용자 환경이 향상됩니다.
* RDP Shortpath는 DSCP(Differentiated Services Code Point) 표시를 통해 RDP 연결에 대한 [QoS](./rdp-quality-of-service-qos.md)(서비스 품질) 우선 순위를 구성할 수 있도록 지원합니다.
* RDP Shortpath 전송을 사용하면 각 세션에 대해 스로틀 비율을 지정하여 [아웃바운드 네트워크 트래픽을 제한](./rdp-bandwidth.md#limit-network-bandwidth-use-with-throttle-rate)할 수 있습니다.

## <a name="connection-security"></a>연결 보안

RDP Shortpath는 RDP 다중 전송 기능을 확장합니다. 역방향 연결 전송을 대체하는 것이 아니라 보완합니다. 모든 초기 세션 중개는 Azure Virtual Desktop 인프라를 통해 관리됩니다.

배포는 역방향 연결 전송을 통해 인증된 들어오는 Shortpath 트래픽에 대해서만 사용자 구성 UDP 포트를 사용합니다. RDP Shortpath 수신기는 역방향 연결 세션과 일치하지 않는 한 모든 연결 시도를 무시합니다.

RDP Shortpath는 세션 호스트의 인증서를 사용하여 클라이언트와 세션 호스트 간의 TLS 연결을 사용합니다. 기본적으로 RDP 암호화에 사용되는 인증서는 배포하는 동안 OS에 의해 자체 생성됩니다. 원할 경우 고객은 엔터프라이즈 인증 기관에서 발급한 중앙에서 관리되는 인증서를 배포할 수 있습니다. 인증서 구성에 관한 자세한 내용은 [Windows Server 설명서](/troubleshoot/windows-server/remote/remote-desktop-listener-certificate-configurations)를 참조하세요.

## <a name="rdp-shortpath-connection-sequence"></a>RDP Shortpath 연결 시퀀스

[역방향 연결 전송을](./network-connectivity.md)설정하면 클라이언트 및 세션 호스트가 RDP 연결을 시작하고 다중 전송 기능을 협상합니다.

세션 호스트가 다중 전송 기능을 협상하는 방법은 다음과 같습니다.

1. 세션 호스트는 프라이빗 및 퍼블릭 IPv4와 IPv6 주소 목록을 클라이언트에 보냅니다.
2. 클라이언트는 백그라운드 스레드를 시작하여 호스트의 IP 주소 중 하나에 직접 병렬 UDP 기반 전송을 설정합니다.
3. 클라이언트는 제공된 IP 주소를 검색하는 동안 역방향 연결 전송에 대한 초기 연결 설정을 계속하여 사용자 연결에 지연 시간이 발생하지 않도록 합니다.
4. 클라이언트에 직접 가시선이 있는 경우 클라이언트는 세션 호스트와 보안 TLS 연결을 설정합니다.
5. Shortpath 전송을 설정하면 RDP는 원격 그래픽, 입력 및 디바이스 리디렉션을 포함한 모든 DVC(동적 가상 채널)를 새 전송으로 이동합니다.
6. 방화벽이나 네트워크 토폴로지에 따라 클라이언트가 직접 UDP 연결을 설정하지 못하는 경우 RDP는 역방향 연결 전송을 계속합니다.

아래 다이어그램은 RDP Shortpath 네트워크 연결을 대략적으로 보여 줍니다.

:::image type="content" source="media/rdp-shortpath-connections.svg" alt-text="RDP Shortpath 네트워크 연결 다이어그램" lightbox="media/rdp-shortpath-connections.svg":::

## <a name="requirements"></a>요구 사항

RDP Shortpath를 지원하려면 Azure Virtual Desktop 클라이언트에서 세션 호스트에 대한 직접적인 가시선을 사용해야 합니다. 다음 방법 중 하나를 사용하여 직접 가시선을 얻을 수 있습니다.

- 원격 클라이언트 컴퓨터가 Windows 10 또는 Windows 7을 실행하고 [Windows Desktop 클라이언트가](/windows-server/remote/remote-desktop-services/clients/windowsdesktop) 설치되어 있어야 합니다. 현재 Windows 아닌 클라이언트는 지원되지 않습니다.
- [ExpressRoute 프라이빗 피어링](../expressroute/expressroute-circuit-peerings.md) 사용
- [사이트간 VPN(가상 사설망)(IPsec 기반)](../vpn-gateway/tutorial-site-to-site-portal.md) 사용
- 지점 [및 사이트 간의 VPN 사용(IPsec 기반)](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md)
- 공용 [IP 주소 할당](../virtual-network/ip-services/virtual-network-public-ip-address.md) 사용

다른 VPN 유형을 사용하여 Azure에 연결하는 경우 UDP(사용자 데이터그램 프로토콜) 기반 VPN을 사용하는 것이 좋습니다. 대부분의 TCP(Transmission Control Protocol) 기반 VPN 솔루션은 중첩된 UDP를 지원하지만 TCP 정체 제어의 상속된 오버헤드를 추가하여 RDP 성능을 저하합니다.

직접 가시선을 사용하면 클라이언트가 방화벽에 의해 차단되지 않고 세션 호스트에 직접 연결할 수 있습니다.

## <a name="configure-rdp-shortpath-for-managed-networks"></a>관리형 네트워크에 대한 RDP Shortpath 구성

관리형 네트워크에 RDP Shortpath를 사용하도록 설정하려면 세션 호스트에서 RDP Shortpath 수신기를 사용하도록 설정해야 합니다. 사용자 환경에서 사용되는 여러 세션 호스트에 대한 RDP Shortpath를 사용하도록 설정할 수 있습니다. 그러나 호스트 풀의 모든 호스트에서 RDP Shortpath를 사용하도록 설정할 필요는 없습니다.

RDP Shortpath 수신기를 사용하도록 설정하려면 다음을 수행합니다.

1. 먼저 Azure Virtual Desktop에 대한 규칙 및 설정을 추가하는 관리 템플릿을 설치합니다. Azure [Virtual Desktop 정책 템플릿 파일(AVDGPTemplate.cab)을](https://aka.ms/avdgpo) 다운로드하고 .cab 파일 및 .zip 보관 파일의 콘텐츠를 추출합니다.
2. **terminalserver-avd.admx** 파일을 복사한 다음 **%windir%\PolicyDefinitions** 폴더에 붙여넣습니다.
3. **en-us\terminalserver-avd.adml** 파일을 복사한 다음 **%windir%\PolicyDefinitions\en-us 폴더에** 붙여넣습니다.
4. 파일이 올바르게 복사되었는지 확인하려면 **그룹 정책 편집기를** 열고 Azure Virtual Desktop 원격 데스크톱 세션 호스트 **컴퓨터 구성**  >  **관리 템플릿** Windows 구성  >  **요소**  >    >  **원격 데스크톱 서비스**  >  이동합니다.
5. 다음 스크린샷과 같이 하나 이상의 Azure Virtual Desktop 정책이 표시됩니다.

   :::image type="content" source="media/azure-virtual-desktop-gpo.png" alt-text="그룹 정책 편집기의 스크린샷" lightbox="media/azure-virtual-desktop-gpo.png":::

   > [!NOTE]
   > Active Directory 도메인의 그룹 정책 중앙 저장소에 관리 템플릿을 설치할 수도 있습니다.
   > 그룹 정책 관리 템플릿의 중앙 저장소에 대한 자세한 내용은 [Windows에서 그룹 정책 관리 템플릿의 중앙 저장소를 만들고 관리하는 방법](/troubleshoot/windows-client/group-policy/create-and-manage-central-store)을 참조하세요.

6. **"관리형 네트워크에 RDP Shortpath 사용"** 정책을 열고 **"사용"으로** 설정합니다. 이 정책 설정을 사용하도록 설정하면 Azure Virtual Desktop 세션 호스트가 들어오는 연결을 수신 대기하는 데 사용할 포트 번호를 구성할 수도 있습니다. 기본 포트는 3390입니다.
7. 세션 호스트를 다시 시작하여 변경 내용을 적용합니다.

## <a name="configure-windows-defender-firewall-with-advanced-security"></a>고급 보안이 포함된 Windows Defender 방화벽 구성

RDP Shortpath 인바운드 네트워크 트래픽을 허용하려면 그룹 정책 관리 MMC 스냅인에서 고급 보안이 포함된 Windows Defender 방화벽 노드를 사용하여 방화벽 규칙을 만듭니다.

1. [고급 보안이 포함된 Windows Defender 방화벽](/windows/security/threat-protection/windows-firewall/open-the-group-policy-management-console-to-windows-firewall-with-advanced-security)에 대한 그룹 정책 관리 콘솔을 엽니다.
2. 탐색 창에서 **인바운드 규칙** 을 선택합니다.
3. **작업** 을 선택하고 **새 규칙** 을 선택합니다.
4. 새 인바운드 규칙 마법사의 **규칙 유형** 페이지에서 **사용자 지정** 을 선택하고 **다음** 을 선택합니다.
5. **프로그램** 페이지에서 **이 프로그램 경로** 를 선택하고 “%SystemRoot%\system32\svchost.exe”를 입력한 후 **다음** 을 선택합니다.
6. **프로토콜 및 포트** 페이지에서 UDP 프로토콜 형식을 선택합니다. 로컬 **포트** 에서 "특정 포트"를 선택하고 구성된 UDP 포트를 입력합니다. 기본 설정을 켜고 나면 포트 번호는 3390이 됩니다.
7. **범위** 페이지에서 이 페이지에 입력된 IP 주소에서 들어오고 나가는 네트워크 트래픽에만 규칙이 적용되도록 지정할 수 있습니다. 디자인에 맞게 적절히 구성하고 **다음** 을 선택합니다.
8. **작업** 페이지에서 **연결 허용** 을 선택하고 **다음** 을 선택합니다.
9. **프로필** 페이지에서 이 규칙이 적용되는 네트워크 위치 유형을 선택하고 **다음** 을 선택합니다.
10. **이름** 페이지에서 규칙의 이름 및 설명을 입력한 다음, **마침을** 선택합니다.

완료되면 새 규칙이 다음 스크린샷의 형식과 일치하는지 확인합니다.
:::image type="content" source="media/rdp-shortpath-firewall-general-tab.png" alt-text="연결 허용 옵션이 선택된 RDP Shortpath 네트워크 연결에 대한 방화벽 구성의 일반 탭 스크린샷" lightbox="media/rdp-shortpath-firewall-general-tab.png":::

:::image type="content" source="media/rdp-shortpath-firewall-service-settings.png" alt-text="원격 데스크톱 서비스 선택한 RDP Shortpath 네트워크 연결에 대한 방화벽 구성에 대한 프로그램 및 서비스 탭의 스크린샷" lightbox="media/rdp-shortpath-firewall-service-settings.png":::

:::image type="content" source="media/rdp-shortpath-firewall-protocol-and-ports.png" alt-text="UDP 포트 3390이 구성된 RDP Shortpath 네트워크 연결에 대한 방화벽 구성에 대한 프로토콜 및 포트 탭의 스크린샷" lightbox="media/rdp-shortpath-firewall-protocol-and-ports.png":::

PowerShell을 사용하여 다음과 같이 Windows Firewall을 구성할 수도 있습니다.

```powershell
New-NetFirewallRule -DisplayName 'Remote Desktop - Shortpath (UDP-In)'  -Action Allow -Description 'Inbound rule for the Remote Desktop service to allow RDP traffic. [UDP 3390]' -Group '@FirewallAPI.dll,-28752' -Name 'RemoteDesktop-UserMode-In-Shortpath-UDP'  -PolicyStore PersistentStore -Profile Domain, Private -Service TermService -Protocol udp -LocalPort 3390 -Program '%SystemRoot%\system32\svchost.exe' -Enabled:True
```

### <a name="using-powershell-to-configure-windows-defender-firewall"></a>PowerShell을 사용하여 Windows Defender 방화벽 구성

PowerShell을 사용하여 다음 cmdlet을 실행하여 그룹 정책을 구성할 수도 있습니다.

```powershell
# Replace $domainName value with the name of your Active Directory domain
# Replace $policyName value with the name of existing Group Policy Object
$domainName = "contoso.com"
$policyName = "RDP Shortpath Policy"
$gpoSession = Open-NetGPO -PolicyStore "$domainName\$policyName"
New-NetFirewallRule -DisplayName 'Remote Desktop - Shortpath (UDP-In)'  -Action Allow -Description 'Inbound rule for the Remote Desktop service to allow RDP traffic. [UDP 3390]' -Group '@FirewallAPI.dll,-28752' -Name 'RemoteDesktop-UserMode-In-Shortpath-UDP' -Profile Domain, Private -Service TermService -Protocol udp -LocalPort 3390 -Program '%SystemRoot%\system32\svchost.exe' -Enabled:True -GPOSession $gpoSession
Save-NetGPO -GPOSession $gpoSession
```

## <a name="configuring-azure-network-security-group"></a>Azure 네트워크 보안 그룹 구성

네트워크 보안 경계에서 RDP Shortpath 수신기에 대한 액세스를 허용하려면 인바운드 UDP 포트 3390을 허용하도록 Azure 네트워크 보안 그룹을 구성해야 합니다.
[네트워크 보안 그룹 설명서](../virtual-machines/windows/nsg-quickstart-portal.md)에 따라 다음 매개 변수를 사용하여 트래픽을 허용하는 인바운드 보안 규칙을 만듭니다.

* **원본** - **모두** 또는 클라이언트가 있는 IP 범위
* **원본 포트 범위** -  **\***
* **대상** - **모든**
* **대상 포트 범위** - **3390**
* **프로토콜** - **UDP**
* **작업** - **허용**
* 선택적으로 **우선 순위** 를 변경합니다. 이 우선 순위는 규칙이 적용되는 순서에 영향을 줍니다. 숫자 값이 적을수록 규칙이 먼저 적용됩니다.
* **이름** -- **RDP Shortpath**

### <a name="disabling-rdp-shortpath-for-a-specific-subnet"></a>특정 서브넷에 대해 RDP Shortpath 사용 안 함

RDP Shortpath 전송을 사용 하 여에서 특정 서브넷을 차단 해야 하는 경우 올바른 원본 IP 범위를 지정 하는 다른 네트워크 보안 그룹을 구성할 수 있습니다.

## <a name="verify-your-network-connectivity"></a>네트워크 연결을 확인합니다.

다음으로 네트워크에서 RDP Shortpath를 사용 하 고 있는지 확인 해야 합니다. "연결 정보" 대화 상자를 사용 하거나 Log Analytics를 사용 하 여이 작업을 수행할 수 있습니다.

### <a name="connection-information-dialog"></a>연결 정보 대화 상자

연결이 RDP Shortpath를 사용 하 고 있는지 확인 하 고 다음 스크린샷에 표시 된 것 처럼 화면 위쪽의 **연결** 도구 모음으로 이동 하 고 안테나 아이콘을 선택 하 여 "연결 정보" 대화 상자를 엽니다.

:::image type="content" source="media/rdp-shortpath-connection-bar.png" alt-text="원격 데스크톱 연결 막대 이미지":::

:::image type="content" source="media/rdp-shortpath-connection-info.png" alt-text="원격 데스크톱 연결 정보 대화 상자의 이미지":::

### <a name="using-event-logs"></a>이벤트 로그 사용

세션에서 RDP Shortpath 전송을 사용 하 고 있는지 확인 하려면 다음을 수행 합니다.

1. 원하는 Azure 가상 데스크톱 클라이언트를 사용 하 여 VM 데스크톱에 연결 합니다.
2. **이벤트 뷰어** 를 열고 **응용 프로그램 및 서비스 로그**  >  **microsoft**  >  **Windows**  >  **remotedesktopservices services**-  >  **RdpCoreCDV/Operational** 로 이동 합니다.
3. 이벤트 ID 131이 표시 되 면 네트워크에서 RDP Shortpath transport를 사용 하 고 있는 것입니다.

### <a name="use-log-analytics"></a>Log Analytics 사용

[Azure Log Analytics](./diagnostics-log-analytics.md)을 사용 하는 경우 [WVDConnections 테이블](/azure/azure-monitor/reference/tables/wvdconnections)을 쿼리하여 연결을 모니터링할 수 있습니다. UdpUse 이라는 열은 Azure 가상 데스크톱 RDP 스택이 현재 사용자 연결에서 UDP 프로토콜을 사용 하는지 여부를 나타냅니다.
가능한 값은 다음과 같습니다.

* **0** - 사용자 연결에서 RDP Shortpath를 사용하지 않음
- **1** -사용자 연결이 관리 되는 네트워크에 RDP Shortpath를 사용 합니다.
  
다음 쿼리 목록에서 연결 정보를 검토할 수 있습니다. [Log Analytics 쿼리 편집기](../azure-monitor/logs/log-analytics-tutorial.md#write-a-query)에서 이 쿼리를 실행할 수 있습니다. 각 쿼리에 대해 `userupn`을 조회할 사용자의 UPN으로 바꿉니다.

```kusto
let Events = WVDConnections | where UserName == "userupn" ;
Events
| where State == "Connected"
| project CorrelationId , UserName, ResourceAlias , StartTime=TimeGenerated, UdpUse, SessionHostName, SessionHostSxSStackVersion
| join (Events
| where State == "Completed"
| project EndTime=TimeGenerated, CorrelationId, UdpUse)
on CorrelationId
| project StartTime, Duration = EndTime - StartTime, ResourceAlias, UdpUse,  SessionHostName, SessionHostSxSStackVersion
| sort by StartTime asc
```

## <a name="troubleshooting"></a>문제 해결

### <a name="verify-shortpath-listener"></a>Shortpath 수신기 확인

UDP 수신기를 사용하도록 설정되었는지 확인하려면 세션 호스트에서 다음 PowerShell 명령을 사용합니다.

```powershell
Get-NetUDPEndpoint -OwningProcess ((Get-WmiObject win32_service -Filter "name = 'TermService'").ProcessId)  -LocalPort 3390
```

사용하도록 설정하면 다음과 같은 출력이 표시됩니다.

```dos
LocalAddress                             LocalPort
------------                             ---------
::                                       3390
0.0.0.0                                  3390
```

충돌이 있는 경우 다음 명령을 실행 하 여 포트를 차단 하는 프로세스를 식별할 수 있습니다.

```powershell
Get-Process -id (Get-NetUDPEndpoint  -LocalPort 3390 -LocalAddress 0.0.0.0).OwningProcess
```

## <a name="disabling-rdp-shortpath"></a>RDP Shortpath 사용 안 함

RDP Shortpath 전송을 사용하지 않도록 설정해야 하는 경우도 있습니다. 그룹 정책을 사용하여 RDP Shortpath를 사용하지 않도록 설정할 수 있습니다.

### <a name="disabling-rdp-shortpath-on-the-client"></a>클라이언트에서 RDP Shortpath 사용 안 함

특정 클라이언트에 대해 RDP Shortpath를 사용하지 않도록 설정하려면 다음 그룹 정책을 통해 UDP 지원을 사용하지 않도록 설정합니다.

1. 클라이언트에서 **gpedit.msc** 를 실행 합니다.
2. **컴퓨터 구성**  >  **관리 템플릿**  >  **Windows 구성 요소**  >  **원격 데스크톱 서비스**  >  **원격 데스크톱 연결 클라이언트** 로 이동 합니다.
3. **"클라이언트에서 UDP 끄기"** 설정을 **사용** 으로 설정 합니다.

### <a name="disable-rdp-shortpath-on-the-session-host"></a>세션 호스트에서 RDP Shortpath 사용 안 함

특정 세션 호스트에 대해 RDP Shortpath를 사용하지 않도록 설정하려면 다음 그룹 정책을 통해 UDP 지원을 사용하지 않도록 설정합니다.

1. 세션 호스트에서 **gpedit.msc** 를 실행합니다.
2. **컴퓨터 구성 > 관리 템플릿 > Windows 구성 요소 >** 원격 데스크톱 서비스 > 원격 데스크톱 세션 호스트 연결로 이동 합니다.
3. **"RDP 전송 프로토콜 선택"** 설정을 **TCP로만** 설정 합니다.

## <a name="next-steps"></a>다음 단계

* Azure Virtual Desktop 네트워크 연결에 대한 자세한 내용은 [Azure Virtual Desktop 네트워크 연결 이해](network-connectivity.md)를 참조하세요.
* Azure Virtual Desktop용 QoS(서비스 품질)를 시작하려면 [Azure Virtual Desktop용 QoS(서비스 품질) 구현](rdp-quality-of-service-qos.md)을 참조하세요.
