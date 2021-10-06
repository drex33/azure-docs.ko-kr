---
title: '자습서: Azure Virtual WAN 사이트 간 연결에서 패킷 캡처 수행'
description: 이 자습서에서는 Virtual WAN 사이트 간 VPN Gateway에서 패킷 캡처를 수행하는 방법을 알아봅니다.
services: virtual-wan
author: wtnlee
ms.service: virtual-wan
ms.topic: tutorial
ms.date: 04/13/2021
ms.author: wellee
Customer intent: As someone with a networking background using Virtual WAN, I want to perform a packet capture on my Site-to-site VPN Gateway.
ms.openlocfilehash: 8970a275e295a54581876f13a2d4b0112c0116d9
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128581988"
---
# <a name="perform-packet-capture-on-the-azure-virtual-wan-site-to-site-vpn-gateway"></a>Virtual WAN 사이트 간 VPN Gateway에서 패킷 캡처 수행 

연결 및 성능과 관련된 문제는 복잡한 경우가 많습니다. 문제의 구체적인 원인을 파악하는 데 상당한 시간과 노력이 소요될 수 있습니다. 패킷 캡처를 수행하면 네트워크의 특정 부분으로 문제의 범위를 좁힐 수 있습니다. 이를 통해 문제가 네트워크의 고객 측에 있는지, 네트워크의 Azure 측에 있는지, 그 사이에 있는지 파악할 수 있습니다. 문제의 범위를 좁힌 후에 디버그와 수정 작업을 수행하는 것이 더 효율적입니다.

이 문서에서는 Azure Virtual WAN 사이트 간 VPN Gateway에서 패킷 캡처를 시작하고 중지하는 방법을 설명합니다. 현재 이 기능은 PowerShell을 통해서만 사용할 수 있습니다.

## <a name="prerequisites"></a>필수 구성 요소

이 문서의 단계를 수행하려면 환경에 다음 구성이 이미 갖춰져 있어야 합니다.

* Virtual Hub에 Virtual WAN, 가상 허브 및 사이트 간 VPN 게이트웨이가 배포되어 있어야 합니다. 또한 VPN 사이트와 사이트 간 VPN Gateway가 연결되어 있어야 합니다.


### <a name="working-with-azure-powershell"></a>Azure PowerShell 작업

[!INCLUDE [PowerShell](../../includes/vpn-gateway-cloud-shell-powershell.md)]

## <a name="set-up-the-environment"></a>환경 설정

PowerShell에서 다음 스크립트를 실행하여 올바른 구독 컨텍스트에 있는지 확인합니다. 이를 통해 사이트 간 VPN Gateway에서 패킷 캡처를 수행할 수 있는 권한이 있는 사용자로 로그인하게 됩니다.

   ```azurepowershell-interactive
    $subid = “<insert Virtual WAN subscription ID here>”
    Set-AzContext -SubscriptionId $subid
   ```

## <a name="create-a-storage-account"></a><a name="createstorage"></a> 스토리지 계정 만들기

패킷 캡처 결과를 저장하려면 Azure 구독에서 스토리지 계정을 만들어야 합니다. 스토리지 계정을 만드는 방법은 이 [문서](.././storage/common/storage-account-create.md)를 참조하세요.

계정을 만든 후에는 다음 명령을 실행하여 SAS(공유 액세스 서명) URL을 생성하세요. 이 URL을 통해 패킷 캡처 결과가 저장됩니다.
   ```azurepowershell-interactive
  $rg = “<resource group name containing storage account>” 
$storeName = “<name of storage account> “
$containerName = “<name of container you want to store packet capture in>
$key = Get-AzStorageAccountKey -ResourceGroupName $rg -Name $storeName
$context = New-AzStorageContext -StorageAccountName  $storeName -StorageAccountKey $key[0].value
New-AzStorageContainer -Name $containerName -Context $context
$container = Get-AzStorageContainer -Name $containerName  -Context $context
$now = get-date
$sasurl = New-AzStorageContainerSASToken -Name $containerName -Context $context -Permission "rwd" -StartTime $now.AddHours(-1) -ExpiryTime $now.AddDays(1) -FullUri
   ```

## <a name="start-the-packet-capture"></a>패킷 캡처 시작

패킷 캡처는 단일 VPN 연결 또는 전체 사이트 간 VPN Gateway 중 하나에서 수행할 수 있습니다. VPN 연결에서 캡처를 수행할 경우 동시에 6개 연결에 대해서만 캡처를 수행할 수 있습니다.

### <a name="packet-capture-on-a-site-to-site-vpn-gateway-all-connections"></a>사이트 간 VPN Gateway에서 패킷 캡처(모든 연결)

다음 명령을 실행하세요. 사이트 간 VPN Gateway의 이름은 가상 허브로 이동하여 연결 아래에서 VPN(사이트 간)을 클릭하면 확인할 수 있습니다.

:::image type="content" source="./media/virtual-wan-pcap-screenshots/vpn-gateway-name.png" alt-text="가상 WAN 게이트웨이 이름의 이미지." lightbox="./media/virtual-wan-pcap-screenshots/vpn-gateway-name.png":::

   ```azurepowershell-interactive
Start-AzVpnGatewayPacketCapture -ResourceGroupName $rg -Name "<name of the Gateway>"
   ```

### <a name="packet-capture-on-specific-site-to-site-vpn-connections"></a>특정 사이트 간 VPN 연결에서 패킷 캡처

>[!NOTE]
> 동시에 5개 VPN 연결에 대해서만 패킷 캡처를 수행할 수 있습니다.


VPN 사이트 연결 이름을 찾으려면 다음 PowerShell 명령을 사용하세요.

   ```azurepowershell-interactive
$connections = Get-AzVpnConnection -ResourceGroupName $rg -ParentResourceName "<name of the Gateway"
$connections
$connections.name
   ```
위 Powershell 블록의 로컬 변수 *connections* 에는 사이트 간 VPN Gateway에 연결된 모든 VPN 사이트 배열이 포함됩니다. 패킷 캡처를 수행하려는 VPN 사이트에 해당하는 항목을 찾으세요. *이름* 필드를 복사하고 다음 Start-AzVpnConnectionPacketCapture 명령에서 *-Name* 매개 변수로 사용합니다. 또는 *connections.name* 에 액세스하여 모든 연결 이름을 볼 수 있습니다. 

링크 이름을 찾으려면 다음 단계를 수행하세요. 가상 허브로 이동하고 연결에서 VPN(사이트 간)을 클릭합니다. 그런 다음, 패킷 캡처를 수행하려는 VPN 사이트로 이동하여 오른쪽에 있는 세 개의 점을 클릭합니다. 표시되는 팝업 메뉴에서 **VPN 연결 편집** 을 클릭합니다.

:::image type="content" source="./media/virtual-wan-pcap-screenshots/sample-connection.png" alt-text="VPN 연결 이름을 확인하는 방법을 보여주는 이미지." lightbox="./media/virtual-wan-pcap-screenshots/sample-connection.png":::

특정 VPN 사이트에 연결된 링크의 이름은 이전 섹션의 VPN 사이트를 클릭한 후 **링크** 표에서 확인할 수 있습니다. 

:::image type="content" source="./media/virtual-wan-pcap-screenshots/link-name-sample.png" alt-text="VPN 링크 이름을 확인하는 방법을 보여주는 이미지." lightbox="./media/virtual-wan-pcap-screenshots/link-name-sample.png":::

   ```azurepowershell-interactive
Start-AzVpnConnectionPacketCapture -ResourceGroupName $rg -Name "<name of the VPN connection>" -ParentResourceName “<name of the Gateway>” -LinkConnection “<comma separated list of links eg. "link1,link2">”
   ```

## <a name="optional-specifying-filters"></a>선택 사항: 필터 지정

몇 가지 일반적인 패킷 캡처 도구가 있습니다. 이러한 도구로 관련 패킷 캡처를 가져오면 번거로울 수 있는데, 특히 대용량 트래픽 상황에서는 더욱 그러합니다. 패킷 캡처를 간소화하기 위해 패킷 캡처에 필터를 지정하여 특정 동작에 중점을 둘 수 있습니다. 사용 가능한 매개 변수는 다음과 같습니다.

>[!NOTE]
> TracingFlags 및 TCPFlags의 경우 캡처하려는 프로토콜에 대한 숫자 값을 더해서 여러 프로토콜을 지정할 수 있습니다(논리적 OR와 동일함). 예를 들어 ESP 및 OPVN 패킷만 캡처하려면 TracingFlag 값을 8+1 = 9로 지정합니다.  

| 매개 변수 | 설명 | 기본값 | 사용 가능한 값|
|--- |--- | --- | ---|
| TracingFlags | 캡처할 패킷 유형을 지정하는 정수 | 11(ESP, IKE, OVPN) | ESP = 1 IKE  = 2 OPVN = 8 |
| TCPFlags | 캡처할 TCP 패킷 유형을 지정하는 정수 | 0(없음) | FIN = 1, SYN = 2, RST = 4, PSH = 8, ACK = 16,URG = 32, ECE = 64, CWR = 128| 
| MaxPacketBufferSize|캡처할 패킷의 최대 크기(바이트). 크기가 지정한 값보다 큰 패킷은 잘립니다. |120|모두|
| MaxFileSize |최대 캡처 파일 크기(Mb). 캡처 결과는 순환 버퍼에 저장되므로 FIFO 방식으로 오버플로가 처리됩니다(오래된 패킷이 먼저 제거됨).|100|모두|
|SourceSubnets|지정한 CIDR 범위의 패킷이 캡처됩니다. 배열로 지정합니다.|[](모든 IPv4 주소)|쉼표로 구분된 IPV4 서브넷의 배열|
| DestinationSubnets |지정한 CIDR 범위를 대상으로 하는 패킷이 캡처됩니다. 배열로 지정합니다. |[](모든 IPv4 주소)|쉼표로 구분된 IPV4 서브넷의 배열|
|SourcePort |지정한 범위에 원본이 있는 패킷이 캡처됩니다. 배열로 지정합니다.|[](모든 포트)|쉼표로 구분된 포트의 배열|
|DestinationPort |지정한 범위에 대상이 있는 패킷이 캡처됩니다. 배열로 지정합니다.|[](모든 포트)|쉼표로 구분된 포트의 배열|
| CaptureSingleDirectionTrafficOnly |True이면 양방향 흐름 중 한 방향만 패킷 캡처에 나타납니다. 따라서 가능한 모든 IP 및 포트의 조합이 캡처됩니다.|True|True, False|
|프로토콜|IANA 프로토콜에 해당하는 정수의 배열입니다. |[](모든 프로토콜)| [여기](https://www.iana.org/assignments/protocol-numbers/protocol-numbers.xhtml)에 있는 모든 프로토콜 |

다음은 필터 문자열을 사용한 패킷 캡처의 예입니다. 필요에 따라 위의 표를 기준으로 매개 변수를 변경할 수 있습니다.  

   ```azurepowershell-interactive
$filter="{`"TracingFlags`":11,`"MaxPacketBufferSize`":120,`"MaxFileSize`":500,`"Filters`":[{`"SourceSubnets`":[`"10.19.0.4/32`",`"10.20.0.4/32`"],`"DestinationSubnets`":[`"10.20.0.4/32`",`"10.19.0.4/32`"],`"TcpFlags`":9,`"CaptureSingleDirectionTrafficOnly`":true}]}"
Start-AzVpnConnectionPacketCapture -ResourceGroupName $rg -Name "<name of the VPN connection>" -ParentResourceName “<name of the Gateway>” -LinkConnection “<comma separated list of links>” -Sasurl $sasurl -FilterData $filter

Start-AzVpnGatewayPacketCapture -ResourceGroupName $rg -Name "<name of the Gateway>" -FilterData $filter
   ```

## <a name="stopping-the-packet-capture"></a>패킷 캡처 중지
최소 600초 이상 패킷 캡처가 실행되도록 두는 것이 좋습니다. 경로에 있는 여러 구성 요소 간 동기화 문제로 인해 짧은 시간 동안 패킷 캡처를 수행하면 전체 데이터가 제공되지 않을 수 있습니다. 패킷 캡처를 중지할 준비가 되면 다음 명령을 실행합니다.

매개 변수는 패킷 캡처 시작 섹션의 매개 변수와 비슷합니다. SAS URL은 [스토리지 계정 만들기](#createstorage) 섹션에서 생성했습니다. 만약 `SASurl` 매개 변수가 올바르게 구성되지 않은 경우 스토리지 오류로 추적이 실패할 수 있습니다.

### <a name="gateway-level-packet-capture"></a>게이트웨이 수준 패킷 캡처

   ```azurepowershell-interactive
Stop-AzVpnGatewayPacketCapture -ResourceGroupName $rg -Name <GatewayName> -SasUrl $sasurl
   ```

### <a name="connection-level-packet-captures"></a>연결 수준 패킷 캡처

   ```azurepowershell-interactive
Stop-AzVpnConnectionPacketCapture -ResourceGroupName $rg -Name <name of the VPN connection> -ParentResourceName "<name of VPN Gateway>" -LinkConnectionName <comma separated list of links e.g. "link1,link2">-SasUrl $sasurl
   ```

## <a name="viewing-your-packet-capture"></a>패킷 캡처 보기

Azure Portal에서 "스토리지 계정 만들기"에서 만든 스토리지 계정으로 이동하여 컨테이너를 클릭하고 파일을 다운로드합니다. 패킷 캡처 데이터 파일은 PCAP 형식으로 생성됩니다. Wireshark 또는 다른 일반적으로 사용 가능한 애플리케이션을 사용하여 PCAP 파일을 열 수 있습니다.

## <a name="next-steps"></a>다음 단계

다음으로, Virtual WAN에 대한 자세한 내용은 다음을 참조하세요.

> [!div class="nextstepaction"]
> * [가상 WAN FAQ](virtual-wan-faq.md)
