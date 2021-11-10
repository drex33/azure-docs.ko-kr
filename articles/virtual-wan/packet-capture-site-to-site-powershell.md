---
title: Virtual WAN 사이트간 연결에 대한 패킷 캡처 구성
description: PowerShell을 사용하여 Virtual WAN 사이트-사이트 VPN 게이트웨이에서 패킷 캡처를 수행하는 방법을 알아봅니다.
services: virtual-wan
titleSuffix: Azure Virtual WAN
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 11/09/2021
ms.author: cherylmc
ms.openlocfilehash: a5b4427627b164c4f87d2c65203a69c1d2cc2699
ms.sourcegitcommit: 838413a8fc8cd53581973472b7832d87c58e3d5f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/10/2021
ms.locfileid: "132139877"
---
# <a name="configure-a-packet-capture-for-virtual-wan-site-to-site-vpn-powershell"></a>Virtual WAN 사이트-사이트 VPN에 대한 패킷 캡처 구성: PowerShell

이 문서는 Azure PowerShell 사용하여 Azure Virtual WAN 사이트-사이트 VPN 게이트웨이에 대한 패킷 캡처를 만드는 데 도움이 됩니다. 패킷 캡처를 사용하면 문제의 범위를 네트워크의 특정 부분으로 좁힐 수 있습니다. 이 방법은 문제가 네트워크의 온-프레미스 쪽, 네트워크의 Azure 쪽 또는 그 사이에 있는지 여부를 확인하는 데 도움이 될 수 있습니다. 문제의 범위를 좁히면 보다 효율적으로 디버그하고 수정 작업을 수행합니다.

일반적으로 사용할 수 있는 패킷 캡처 도구가 있지만, 특히 대용량 트래픽 시나리오에서는 이러한 도구를 사용하여 관련 패킷 캡처를 얻는 것이 번거로울 수 있습니다. Virtual WAN 패킷 캡처에서 제공하는 필터링 기능은 주요 차별화 요소입니다. Virtual WAN 패킷 캡처는 일반적으로 사용 가능한 패킷 캡처 도구와 함께 사용할 수 있습니다.

## <a name="prerequisites"></a>필수 조건

환경에 다음 구성이 이미 설정되어 있는지 확인합니다.

* 가상 WAN 및 가상 허브.
* 가상 허브에 배포된 사이트-사이트 VPN 게이트웨이.
* VPN 사이트를 사이트간 VPN 게이트웨이에 연결하는 연결이 있을 수도 있습니다.

### <a name="working-with-azure-powershell"></a>Azure PowerShell 작업

[!INCLUDE [PowerShell](../../includes/vpn-gateway-cloud-shell-powershell.md)]

### <a name="set-up-the-environment"></a>환경 설정

다음 명령을 사용하여 올바른 구독을 사용하고 있으며 사이트간 VPN 게이트웨이에서 패킷 캡처를 수행할 수 있는 권한이 있는 사용자로 로그인되어 있는지 확인합니다.

```azurepowershell-interactive
$subid = “<insert Virtual WAN subscription ID here>”
Set-AzContext -SubscriptionId $subid
```

## <a name="create-a-storage-account-and-container"></a><a name="storage"></a> 스토리지 계정 및 컨테이너 만들기

스토리지 계정은 패킷 캡처 결과를 저장하는 데 사용됩니다.

1. 스토리지 계정을 만듭니다. 단계는 스토리지 [계정 만들기를 참조하세요.](../storage/common/storage-account-create.md?tabs=azure-portal)
1. 스토리지 계정 내에 컨테이너 개체를 만듭니다. 단계는 컨테이너 [만들기를 참조하세요.](../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container)

## <a name="generate-the-sas-url"></a><a name="URL"></a> SAS URL 생성

패킷 캡처를 중지하는 경우 만든 스토리지 컨테이너의 **SAS URL을** 제공해야 합니다. 이 URL을 통해 패킷 캡처 결과가 저장됩니다. 

다음 명령을 실행하여 SAS(공유 액세스 서명) URL을 생성합니다.

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

## <a name="start-a-packet-capture"></a><a name="start"></a> 패킷 캡처 시작

이 섹션에서는 사이트간 VPN 게이트웨이(모든 연결)에 대한 패킷 캡처를 시작하는 데 도움이 됩니다.

1. 패킷 캡처를 실행하려면 사이트-사이트 VPN 게이트웨이의 **-Name** 값이 필요합니다. **-Name** 값을 찾으려면 Azure Portal 가상 허브로 이동하여 **연결** 아래에서 **VPN(사이트 간에) 을** 클릭합니다.

   :::image type="content" source="./media/packet-capture-site-to-site-powershell/vpn-gateway-name.png" alt-text="가상 WAN 게이트웨이 이름의 이미지." lightbox="./media//packet-capture-site-to-site-powershell/vpn-gateway-name.png":::

1. 패킷 캡처를 시작하려면 다음 명령을 실행합니다.

   ```azurepowershell-interactive
   Start-AzVpnGatewayPacketCapture -ResourceGroupName $rg -Name "<name of the Gateway>"
   ```

## <a name="optional-specify-filters"></a><a name="filters"></a> 선택 사항: 필터 지정

패킷 캡처를 간소화하기 위해 패킷 캡처에 필터를 지정하여 특정 동작에 중점을 둘 수 있습니다.

>[!NOTE]
> TracingFlags 및 TCPFlags의 경우 캡처하려는 프로토콜에 대한 숫자 값을 더해서 여러 프로토콜을 지정할 수 있습니다(논리적 OR와 동일함). 예를 들어 ESP 및 OPVN 패킷만 캡처하려면 TracingFlag 값 8+1 = 9를 지정합니다.  

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

다음 예제에서는 필터 문자열을 사용하여 패킷 캡처를 보여 주세요. 필요에 맞게 매개 변수를 변경할 수 있습니다.

```azurepowershell-interactive
$filter="{`"TracingFlags`":11,`"MaxPacketBufferSize`":120,`"MaxFileSize`":500,`"Filters`":[{`"SourceSubnets`":[`"10.19.0.4/32`",`"10.20.0.4/32`"],`"DestinationSubnets`":[`"10.20.0.4/32`",`"10.19.0.4/32`"],`"TcpFlags`":9,`"CaptureSingleDirectionTrafficOnly`":true}]}"
Start-AzVpnConnectionPacketCapture -ResourceGroupName $rg -Name "<name of the VPN connection>" -ParentResourceName “<name of the Gateway>” -LinkConnection “<comma separated list of links>” -SasUrl $sasurl -FilterData $filter

Start-AzVpnGatewayPacketCapture -ResourceGroupName $rg -Name "<name of the Gateway>" -FilterData $filter
```

## <a name="stop-a-packet-capture"></a><a name="stop"></a> 패킷 캡처 중지

중지하기 전에 패킷 캡처를 최소 600초 동안 실행하는 것이 좋습니다. 패킷 캡처를 중지하면 매개 변수는 패킷 캡처 시작 섹션의 매개 [변수와](#start) 유사합니다. 명령에서 SAS URL 값은 [스토리지 계정 만들기](#storage) 섹션에서 생성되었습니다. 매개 `SasUrl` 변수가 올바르게 구성되지 않은 경우 스토리지 오류로 캡처가 실패할 수 있습니다.

패킷 캡처를 중지할 준비가 되면 다음 명령을 실행합니다.

```azurepowershell-interactive
Stop-AzVpnGatewayPacketCapture -ResourceGroupName $rg -Name <GatewayName> -SasUrl $sasurl
```

## <a name="view-a-packet-capture"></a><a name="view"></a>패킷 캡처 보기

이 섹션에서는 패킷 캡처 PCAP 파일을 다운로드하여 볼 수 있도록 도와줍니다.

1. Azure Portal 만든 스토리지 계정으로 이동합니다.
1. **컨테이너** 를 클릭 하 여 저장소 계정에 대 한 컨테이너를 표시 합니다.
1. 만든 컨테이너를 클릭 합니다.
1. 폴더 구조를 탐색 하 여 PCAP 파일을 찾습니다. 폴더 이름 및 구조는 날짜 및 UTC 시간을 기준으로 합니다. PCAP 파일을 찾았으면 **다운로드** 를 클릭 합니다.

   :::image type="content" source="./media/packet-capture-site-to-site-powershell/download.png" alt-text="파일 다운로드 방법을 보여 주는 그래픽" lightbox="./media/packet-capture-site-to-site-powershell/download-expand.png":::
1. 패킷 캡처 데이터 파일은 PCAP 형식으로 생성됩니다. Wireshark 또는 다른 일반적으로 사용 가능한 애플리케이션을 사용하여 PCAP 파일을 열 수 있습니다.

## <a name="next-steps"></a>다음 단계

다음으로 가상 WAN에 대해 자세히 알아보려면 [가상 WAN FAQ](virtual-wan-faq.md)를 참조 하세요.
