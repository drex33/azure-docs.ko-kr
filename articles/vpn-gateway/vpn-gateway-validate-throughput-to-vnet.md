---
title: 가상 네트워크에 대한 VPN 처리량 확인
description: 온-프레미스 리소스에서 Azure Virtual Machine으로의 네트워크 처리량에 대해 유효성을 검사하는 방법을 알아봅니다.
titleSuffix: Azure VPN Gateway
services: vpn-gateway
author: cherylmc
manager: dcscontentpm
ms.service: vpn-gateway
ms.topic: troubleshooting
ms.date: 09/02/2020
ms.author: radwiv
ms.reviewer: chadmat;genli
ms.openlocfilehash: b5c3853ca2849590b0338c3f82b3e8606cd35d2d
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128643672"
---
# <a name="how-to-validate-vpn-throughput-to-a-virtual-network"></a>가상 네트워크에 대한 VPN 처리량의 유효성을 검사하는 방법

VPN Gateway 연결을 통해 Azure 내 Virtual Network와 온-프레미스 IT 인프라 사이에 안전한 프레미스 간 연결을 설정할 수 있습니다.

이 문서에서는 온-프레미스 리소스에서 Azure VM(가상 컴퓨터)으로의 네트워크 처리량을 유효성 검사하는 방법을 보여줍니다.

> [!NOTE]
> 이 문서는 일반적인 문제를 진단하고 해결하는 데 사용됩니다. 다음 정보를 사용하여 문제를 해결할 수 없는 경우 [지원에 문의](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)하세요.

## <a name="overview"></a>개요

VPN Gateway 연결에는 다음 구성 요소가 포함됩니다.

* 온-프레미스 VPN 디바이스([유효성이 검사된 VPN 디바이스](vpn-gateway-about-vpn-devices.md#devicetable) 목록 보기).
* 공용 인터넷
* Azure VPN Gateway
* Azure VM

다음 다이어그램에서는 VPN을 통한 온-프레미스 네트워크와 Azure Virtual Network의 논리적 연결을 보여 줍니다.

![VPN을 사용한 고객 네트워크와 MSFT 네트워크의 논리적 연결](./media/vpn-gateway-validate-throughput-to-vnet/VPNPerf.png)

## <a name="calculate-the-maximum-expected-ingressegress"></a>최대 예상 수신/송신 계산

1. 애플리케이션의 기준선 처리량 요구 사항을 결정합니다.
1. Azure VPN Gateway 처리량 제한을 결정합니다. 도움말은 [VPN Gateway 정보](vpn-gateway-about-vpngateways.md#gwsku)의 "게이트웨이 SKU" 섹션을 참조하세요.
1. VM 크기에 대한 [Azure VM 처리량 지침](../virtual-machines/sizes.md)을 결정합니다.
1. ISP(인터넷 서비스 공급자) 대역폭을 결정합니다.
1. VM, VPN Gateway 또는 ISP 중 가장 낮은 대역폭을 활용하여 예상 처리량을 계산합니다. 이 값은 초당 메가비트를 8로 나눈 값입니다.

계산된 처리량이 애플리케이션의 기준선 처리량 요구 사항을 충족하지 않을 경우 병목 현상으로 식별한 리소스의 대역폭을 늘려야 합니다. Azure VPN Gateway의 크기를 조정하려면 [Changing a gateway SKU](vpn-gateway-about-vpn-gateway-settings.md#gwsku)(게이트웨이 SKU 변경)를 참조하세요. 가상 머신의 크기를 조정하려면 [VM 크기 조정](../virtual-machines/resize-vm.md)을 참조하세요. 예상 인터넷 대역폭을 사용할 수 없으면 ISP에 문의할 수도 있습니다.

> [!NOTE]
> VPN Gateway 처리량은 사이트 간\VNET 간 또는 지점 및 사이트 간 모든 연결의 집계입니다.

## <a name="validate-network-throughput-by-using-performance-tools"></a>성능 도구를 사용하여 네트워크 처리량의 유효성 검사

테스트 중에 VPN 터널 처리량 포화가 발생하면 정확한 결과가 제공되지 않으므로 이 유효성 검사는 사용량이 많지 않은 시간에 수행해야 합니다.

이 테스트에 사용하는 도구는 Windows 및 Linux에서 둘 다 작동하고 클라이언트 및 서버 모드가 둘 다 있는 iPerf입니다. 이 도구는 Windows VM의 경우 3Gbps로 제한됩니다.

이 도구는 디스크에 대한 읽기/쓰기 작업을 수행하지 않습니다. 종단 간에 자체 생성된 TCP 트래픽을 생성할 뿐입니다. 클라이언트 노드와 서버 노드 간에 대역폭을 측정하는 실험을 기반으로 통계를 생성합니다. 두 노드 사이에서 테스트할 경우 한 노드는 서버 역할을 하고 다른 노드는 클라이언트 역할을 합니다. 이 테스트가 완료되면 두 노드에서 모두 업로드 및 다운로드 처리량을 둘 다 테스트하도록 노드의 역할을 전환해 보는 것이 좋습니다.

### <a name="download-iperf"></a>iPerf 다운로드

[iPerf](https://iperf.fr/download/iperf_3.1/iperf-3.1.2-win64.zip)를 다운로드합니다. 자세한 내용은 [iPerf 설명서](https://iperf.fr/iperf-doc.php)를 참조하세요.

 > [!NOTE]
 > 이 문서에서 설명된 타사 제품은 Microsoft가 아닌 회사에서 제조되었습니다. Microsoft는 이러한 제품의 성능에 대한 어떠한 묵시적 또는 다른 형태의 보증도 하지 않습니다.

### <a name="run-iperf-iperf3exe"></a>iPerf(iperf3.exe) 실행

1. 트래픽을 허용하는 NSG/ACL 규칙을 사용하도록 설정합니다(Azure VM에서 공용 IP 주소 테스트의 경우).

1. 두 노드에서 모두 포트 5001에 대한 방화벽 예외를 사용하도록 설정합니다.

   **Windows:** 관리자 권한으로 다음 명령을 실행합니다.

   ```CMD
   netsh advfirewall firewall add rule name="Open Port 5001" dir=in action=allow protocol=TCP localport=5001
   ```

   테스트가 완료될 때 규칙을 제거하려면 이 명령을 실행합니다.

   ```CMD
   netsh advfirewall firewall delete rule name="Open Port 5001" protocol=TCP localport=5001
   ```

   **Azure Linux:** Azure Linux 이미지에는 허용되는 방화벽이 있습니다. 포트를 수신 중인 애플리케이션이 있으면 트래픽이 통과할 수 있습니다. 보안 설정된 사용자 지정 이미지를 사용하려면 명시적으로 열린 포트가 필요할 수 있습니다. 일반적인 Linux OS 계층 방화벽에는 `iptables`, `ufw` 또는 `firewalld`가 포함됩니다.

1. 서버 노드에서 iperf3.exe가 추출된 디렉터리로 변경합니다. 그다음에 서버 모드에서 iPerf를 실행하고 다음 명령으로 포트 5001을 수신하도록 설정합니다.

   ```CMD
   cd c:\iperf-3.1.2-win65

   iperf3.exe -s -p 5001
   ```

   > [!Note]
   > 사용자 환경에서 특정 방화벽 제한을 고려하여 포트 5001을 사용자 지정할 수 있습니다.

1. 클라이언트 노드에서 iperf 도구가 추출된 디렉터리로 변경하고 다음 명령을 실행합니다.

   ```CMD
   iperf3.exe -c <IP of the iperf Server> -t 30 -p 5001 -P 32
   ```

   클라이언트는 포트 5001에서 30초간의 트래픽을 서버에 직접 보냅니다. 표시된 '-P'는 32개의 동시 연결을 만들어 서버 노드에 연결 중임을 나타냅니다.

   다음 화면에는 이 예제의 출력이 표시됩니다.

   ![출력](./media/vpn-gateway-validate-throughput-to-vnet/06theoutput.png)

1. (선택 사항) 테스트 결과를 유지하려면 이 명령을 실행합니다.

   ```CMD
   iperf3.exe -c IPofTheServerToReach -t 30 -p 5001 -P 32  >> output.txt
   ```

1. 이전 단계를 완료한 후 전환된 역할로 같은 단계를 실행하면 서버 노드가 클라이언트 노드가 되고 클라이언트 노드가 서버가 됩니다.

> [!Note]
> Iperf는 유일한 도구가 아닙니다. [NTTTCP는 테스트를 위한 대체 솔루션](../virtual-network/virtual-network-bandwidth-testing.md)입니다.

## <a name="test-vms-running-windows"></a>Windows가 실행되는 VM 테스트

### <a name="load-latteexe-onto-the-vms"></a>VM에 Latte.exe 로드

최신 버전의 [Latte.exe](https://gallery.technet.microsoft.com/Latte-The-Windows-tool-for-ac33093b)를 다운로드합니다.

`c:\tools`와 같은 별도의 폴더에 Latte.exe를 두는 것이 좋습니다.

### <a name="allow-latteexe-through-the-windows-firewall"></a>Windows 방화벽을 통과하도록 Latte.exe 허용

수신기에서 Windows 방화벽에 Latte.exe 트래픽이 도착하도록 허용 규칙을 만듭니다. 특정 TCP 포트에서 인바운드를 허용하는 것보다 이름으로 전체 Latte.exe 프로그램을 허용하는 것이 가장 쉽습니다.

### <a name="allow-latteexe-through-the-windows-firewall-like-this"></a>Windows 방화벽을 통과하도록 Latte.exe 허용

`netsh advfirewall firewall add rule program=<PATH>\latte.exe name="Latte" protocol=any dir=in action=allow enable=yes profile=ANY`

예를 들어 latte.exe를 "c:\tools" 폴더에 복사한 경우 명령은 다음과 같습니다.

`netsh advfirewall firewall add rule program=c:\tools\latte.exe name="Latte" protocol=any dir=in action=allow enable=yes profile=ANY`

### <a name="run-latency-tests"></a>대기 시간 테스트 실행

수신기에서 latte.exe를 시작합니다(PowerShell이 아닌 CMD에서 실행).

`latte -a <Receiver IP address>:<port> -i <iterations>`

전형적인 결과를 반환하려면 약 65,000번 반복해야 합니다.

사용 가능한 포트 번호는 모두 충분합니다.

VM의 IP 주소가 10.0.0.4이면 다음과 같이 표시됩니다.

`latte -c -a 10.0.0.4:5005 -i 65100`

수신기에서 latte.exe를 시작합니다(PowerShell이 아닌 CMD에서 실행).

`latte -c -a <Receiver IP address>:<port> -i <iterations>`

결과 명령은 클라이언트 또는 수신기임을 나타내도록 "-c"를 추가하는 것을 제외하고는 수신기에서와 동일합니다.

`latte -c -a 10.0.0.4:5005 -i 65100`

결과를 기다립니다. VM이 얼마나 떨어져 있는지에 따라 완료하는 데 몇 분 정도 걸릴 수 있습니다. 더 긴 테스트를 실행하기 전에 성공 여부를 테스트하려면 더 짧은 반복으로 시작하는 것이 좋습니다.

## <a name="test-vms-running-linux"></a>Linux가 실행되는 VM 테스트

[SockPerf](https://github.com/mellanox/sockperf)를 사용하여 VM을 테스트합니다.

### <a name="install-sockperf-on-the-vms"></a>VM에 SockPerf 설치

Linux VM(송신기 및 수신기 둘 다)에서 다음 명령을 실행하여 VM에서 SockPerf를 준비합니다.

#### <a name="centos--rhel---install-git-and-other-helpful-tools"></a>CentOS / RHEL - GIT 및 기타 유용한 도구 설치

`sudo yum install gcc -y -q`
`sudo yum install git -y -q`
`sudo yum install gcc-c++ -y`
`sudo yum install ncurses-devel -y`
`sudo yum install -y automake`

#### <a name="ubuntu---install-git-and-other-helpful-tools"></a>Ubuntu - GIT 및 기타 유용한 도구 설치

`sudo apt-get install build-essential -y`
`sudo apt-get install git -y -q`
`sudo apt-get install -y autotools-dev`
`sudo apt-get install -y automake`

#### <a name="bash---all"></a>Bash - 모두

bash 명령줄에서(git가 설치되어 있는 것으로 가정)

`git clone https://github.com/mellanox/sockperf`
`cd sockperf/`
`./autogen.sh`
`./configure --prefix=`

속도가 느린 경우 몇 분 정도 걸릴 수 있습니다.

`make`

빠른 설치 설정

`sudo make install`

### <a name="run-sockperf-on-the-vms"></a>VM에서 SockPerf 실행

#### <a name="sample-commands-after-installation-serverreceiver---assumes-servers-ip-is-10004"></a>설치 후의 샘플 명령입니다. 서버/수신기 - 서버의 IP가 10.0.0.4인 것으로 가정

`sudo sockperf sr --tcp -i 10.0.0.4 -p 12345 --full-rtt`

#### <a name="client---assumes-servers-ip-is-10004"></a>클라이언트 - 서버의 IP가 10.0.0.4인 것으로 가정

`sockperf ping-pong -i 10.0.0.4 --tcp -m 1400 -t 101 -p 12345  --full-rtt`

> [!Note]
> VM과 게이트웨이 간의 처리량 테스트 중에 중간 홉(예: 가상 어플라이언스)이 없는지 확인합니다.
> 위의 iPERF/NTTTCP 테스트에서 발생하는 결과가 잘못된 경우(전체 처리량을 기준으로) 이 문서(https://docs.microsoft.com/azure/virtual-network/virtual-network-tcpip-performance-tuning )를 참조하여 문제의 가능한 근본 원인에 대한 주요 요인을 파악하세요.

특히, 이러한 테스트를 수행하는 동안 클라이언트와 서버에서 병렬로 수집된 패킷 캡처 추적(Wireshark/네트워크 모니터)의 분석은 좋지 않은 성능을 평가하는 데 도움이 됩니다. 이러한 추적에는 패킷 손실, 긴 대기 시간, MTU 크기 등이 포함될 수 있습니다. 조각화, TCP 0 창, 사용 불가능한 조각 등이 있습니다.

## <a name="address-slow-file-copy-issues"></a>느린 파일 복사 문제 처리

이전 단계(iPERF/NTTTCP/기타)로 평가되는 전체 처리량이 양호하더라도 Windows 탐색기를 사용하거나 RDP 세션을 통해 끌어서 놓는 경우 파일 복사 속도가 느려질 수 있습니다. 일반적으로 이 문제의 원인은 다음 요소 중 하나이거나 둘 다에 해당합니다.

* Windows 탐색기 및 RDP와 같은 파일 복사 애플리케이션은 파일을 복사할 때 여러 스레드를 사용하지 않습니다. 성능을 개선하기 위해 [Richcopy](/previous-versions/technet-magazine/dd547088(v=msdn.10))와 같은 다중 스레드 파일 복사 애플리케이션을 통해 16개 또는 32개의 스레드를 사용하여 파일을 복사합니다. Richcopy에서 파일 복사에 사용할 스레드 수를 변경하려면 **작업** > **복사 옵션** > **파일 복사** 를 클릭합니다.

   ![느린 파일 복사 문제](./media/vpn-gateway-validate-throughput-to-vnet/Richcopy.png)<br>

   > [!Note]
   > 모든 애플리케이션이 동일하게 작동하는 것은 아니며 모든 애플리케이션/프로세스가 모든 스레드를 사용하는 것은 아닙니다. 테스트를 실행하면 일부 스레드가 비어 있고 정확한 처리량 결과를 제공하지 않는 것을 확인할 수 있습니다.
   > 애플리케이션 파일 전송 성능을 확인하려면 연속된 스레드 수를 늘려서 다중 스레드를 사용하거나 애플리케이션 또는 파일 전송의 최적 처리량을 찾기 위해 줄입니다.

* 부족한 VM 디스크 읽기/쓰기 속도. 자세한 내용은 [Azure Storage 문제 해결](/previous-versions/azure/storage/common/storage-e2e-troubleshooting)을 참조하세요.

## <a name="on-premises-device-external-facing-interface"></a>온-프레미스 디바이스 외부 연결 인터페이스

Azure에서 로컬 네트워크 게이트웨이의 VPN을 통해 연결하려는 온-프레미스 범위의 서브넷이 언급되었습니다. 동시에 온-프레미스 디바이스에 대한 Azure의 VNET 주소 공간을 정의합니다.

* **경로 기반 게이트웨이**: 경로 기반 VPN에 대한 정책 또는 트래픽 선택기는 임의 또는 와일드카드로 구성됩니다.

* **정책 기반 게이트웨이**: 정책 기반 VPN은 온-프레미스 네트워크와 Azure VNet 간의 주소 접두사의 조합에 따라 IPsec 터널을 통해 패킷을 암호화하고 전달합니다. 정책 또는 트래픽 선택기는 일반적으로 VPN 구성에서 액세스 목록으로 정의됩니다.

* **UsePolicyBasedTrafficSelector** 연결: 연결에 대해 “UsePolicyBasedTrafficSelectors”가 $True이면 온-프레미스의 정책 기반 VPN 방화벽에 연결되도록 Azure VPN Gateway가 구성됩니다. PolicyBasedTrafficSelectors를 사용하도록 설정한 경우 VPN 디바이스에 온-프레미스 네트워크(로컬 네트워크 게이트웨이) 접두사 및 Azure Virtual Network 접두사 간의 모든 조합으로 정의된 일치하는 트래픽 선택기가 있는지 확인해야 합니다.

부적절한 구성으로 인해 터널 내의 빈번한 연결 끊김, 패킷 삭제, 잘못된 처리량 및 대기 시간이 발생할 수 있습니다.

## <a name="check-latency"></a>대기 시간 확인

다음 도구를 사용하여 대기 시간을 확인할 수 있습니다.

* WinMTR
* TCPTraceroute
* `ping` 및 `psping`(이러한 도구는 RTT를 적절하게 예측하지만 모든 경우에 사용될 수는 없습니다.)

![대기 시간 확인](./media/vpn-gateway-validate-throughput-to-vnet/08checkinglatency.png)

MS 네트워크 백본을 시작하기 전에 임의의 홉에서 대기 시간이 급증하는 경우 인터넷 서비스 공급자를 사용하여 추가 조사를 진행하는 것이 좋습니다.

"msn.net" 내 홉에서 매우 비정상적인 대기 시간 급증이 발생한 경우 추가 조사를 위해 MS 지원 담당자에게 문의하세요.

## <a name="next-steps"></a>다음 단계

자세한 정보 또는 도움말을 보려면 다음 링크를 확인하세요.

* [Microsoft 지원](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)