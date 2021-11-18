---
title: Azure Portal 사용하여 S2S VPN 연결에 대한 패킷 캡처 구성
titleSuffix: Azure Virtual WAN
description: 포털을 사용하여 Azure Virtual WAN 대한 사이트-사이트 패킷 캡처를 구성하는 방법을 알아봅니다.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 11/09/2021
ms.author: cherylmc
ms.openlocfilehash: c378a6a6e38739600ddad8b0b956e31136d6fffe
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2021
ms.locfileid: "132730630"
---
# <a name="configure-a-packet-capture-for-virtual-wan-site-to-site-vpn-azure-portal"></a>Virtual WAN 사이트-사이트 VPN에 대한 패킷 캡처 구성: Azure Portal

이 문서는 Azure Portal 사용하여 Azure Virtual WAN 사이트-사이트 VPN 게이트웨이에 대한 패킷 캡처를 만드는 데 도움이 됩니다. 패킷 캡처를 사용하면 문제의 범위를 네트워크의 특정 부분으로 좁힐 수 있습니다. 문제가 네트워크의 온-프레미스 쪽, 네트워크의 Azure 쪽 또는 그 사이에 있는지 여부를 확인하는 데 도움이 될 수 있습니다. 문제의 범위를 좁히면 보다 효율적으로 디버그하고 수정 작업을 수행합니다.

일반적으로 사용할 수 있는 패킷 캡처 도구가 있지만, 특히 대용량 트래픽 시나리오에서는 이러한 도구를 사용하여 관련 패킷 캡처를 얻는 것이 번거로울 수 있습니다. Virtual WAN 패킷 캡처에서 제공하는 필터링 기능은 주요 차별화 요소입니다. Virtual WAN 패킷 캡처는 일반적으로 사용 가능한 패킷 캡처 도구와 함께 사용할 수 있습니다.

[!INCLUDE [Portal rollout](../../includes/virtual-wan-portal-feature-rollout.md)]

## <a name="prerequisites"></a>필수 구성 요소

환경에 다음 구성이 이미 설정되어 있는지 확인합니다.

* 가상 WAN 및 가상 허브.
* 가상 허브에 배포된 사이트-사이트 VPN 게이트웨이.
* VPN 사이트를 사이트간 VPN 게이트웨이에 연결하는 연결이 있을 수도 있습니다.

## <a name="create-a-storage-account-and-container"></a><a name="storage"></a> 스토리지 계정 및 컨테이너 만들기

스토리지 계정은 패킷 캡처 결과를 저장하는 데 사용됩니다.

1. 스토리지 계정을 만듭니다. 단계는 스토리지 [계정 만들기를 참조하세요.](../storage/common/storage-account-create.md?tabs=azure-portal)
1. 스토리지 계정 내에 컨테이너 개체를 만듭니다. 단계는 컨테이너 [만들기를 참조하세요.](../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container)

## <a name="generate-the-sas-url"></a><a name="URL"></a> SAS URL 생성

패킷 캡처를 중지하는 경우 만든 스토리지 컨테이너의 **SAS URL을** 제공해야 합니다. 이 URL을 통해 패킷 캡처 결과가 저장됩니다.
스토리지 컨테이너에 대한 SAS URL을 생성하려면 다음을 수행합니다.

1. 새로 만든 컨테이너로 이동합니다.
1. **설정** 아래에서 공유 **액세스 토큰을 선택합니다.**
1. 사용 **권한** 탭에서 **읽기** 및 **쓰기가** 모두 사용되는지 확인합니다.
1. 페이지 아래쪽에서 **SAS 토큰 및 URL 생성** 단추를 클릭합니다.
1. 클립보드에 생성된 **Blob SAS URL** 링크를 복사하려면 클릭합니다.

   :::image type="content" source="./media/packet-capture-site-to-site-portal/generate-url.png" alt-text="읽기 및 쓰기가 모두 선택된 공유 액세스 토큰 페이지입니다." lightbox="./media/packet-capture-site-to-site-portal/generate-url-expand.png":::

## <a name="start-a-packet-capture"></a><a name="start"></a> 패킷 캡처 시작

이 섹션에서는 가상 허브에서 패킷 캡처를 시작합니다.

1. 가상 허브로 이동합니다.
1. **VPN(사이트 대 사이트) 을** 클릭합니다.
1. **VPN(사이트 대 사이트)** 페이지에서 페이지 맨 위에 있는 **패킷 캡처** 단추를 클릭합니다.

   :::image type="content" source="./media/packet-capture-site-to-site-portal/packet-capture.png" alt-text="캡처 시작 페이지를 보여주는 그래픽." lightbox="./media/packet-capture-site-to-site-portal/packet-capture-expand.png":::
1. 패킷 **캡처** 페이지에서 **시작을** 클릭합니다.
1. 필요한 경우 **패킷 캡처 시작 페이지에서** 설정을 수정합니다. 옵션은 [필터](#filters) 섹션을 참조하세요.
1. **시작** 단추를 클릭하여 패킷 캡처를 시작합니다. 패킷 캡처를 600초 이상 실행하도록 하는 것이 좋습니다. 경로의 여러 구성 요소 간에 동기화 문제로 인해 패킷 캡처가 짧아져 전체 데이터를 제공하지 못할 수 있습니다.

## <a name="optional-specify-filters"></a><a name="filters"></a> 선택 사항: 필터 지정

패킷 캡처를 간소화하기 위해 패킷 캡처에 필터를 지정하여 특정 동작에 중점을 둘 수 있습니다.

| 매개 변수 | 설명 | 기본값 | 사용 가능한 값 |
|---|---|---|---|
|TracingFlags|  캡처할 패킷 유형을 지정하는 정수| 11(ESP, IKE, OVPN) |ESP = 1 IKE = 2 OPVN = 8|
|TCPFlags|  캡처할 TCP 패킷 유형을 지정하는 정수 |0(없음)|  FIN = 1, SYN = 2, RST = 4, PSH = 8, ACK = 16,URG = 32, ECE = 64, CWR = 128|
|MaxPacketBufferSize|   캡처할 패킷의 최대 크기(바이트). 크기가 지정한 값보다 큰 패킷은 잘립니다.    |120    |모두|
|MaxFileSize |  최대 캡처 파일 크기(Mb). 캡처 결과는 순환 버퍼에 저장되므로 FIFO 방식으로 오버플로가 처리됩니다(오래된 패킷이 먼저 제거됨).|    100|    모두|
|SourceSubnets |    지정한 CIDR 범위의 패킷이 캡처됩니다. 배열로 지정합니다. |   [ ] (모든 IPv4 주소)    |쉼표로 구분된 IPV4 서브넷의 배열|
|DestinationSubnets |지정한 CIDR 범위를 대상으로 하는 패킷이 캡처됩니다. 배열로 지정합니다. |  [ ] (모든 IPv4 주소)|   쉼표로 구분된 IPV4 서브넷의 배열|
|SourcePort|    지정한 범위에 원본이 있는 패킷이 캡처됩니다. 배열로 지정합니다.|   [ ] (모든 포트)|    쉼표로 구분된 포트의 배열|
|DestinationPort|   지정한 범위에 대상이 있는 패킷이 캡처됩니다. 배열로 지정합니다.   |[ ] (모든 포트)|   쉼표로 구분된 포트의 배열|
|CaptureSingleDirectionTrafficOnly |    True이면 양방향 흐름 중 한 방향만 패킷 캡처에 나타납니다. 따라서 가능한 모든 IP 및 포트의 조합이 캡처됩니다.|  True|   True, False|
|프로토콜|  IANA 프로토콜에 해당하는 정수의 배열입니다.|   [ ] (모든 프로토콜)|    이 [iana.org](https://www.iana.org/assignments/protocol-numbers/protocol-numbers.xhtml) 페이지에 나열된 모든 프로토콜입니다.|

> [!NOTE]
> TracingFlags 및 TCPFlags의 경우 캡처하려는 프로토콜의 숫자 값을 추가하여 여러 프로토콜을 지정할 수 있습니다(논리적 OR과 동일). 예를 들어 ESP 및 OPVN 패킷만 캡처하려면 TracingFlag 값을 8+1 = 9로 지정합니다.
>

## <a name="stop-a-packet-capture"></a>패킷 캡처 중지

이 섹션은 패킷 캡처를 중지하거나 중단하는 데 도움이 됩니다.

1. 가상 허브 페이지에서 패킷 **캡처** 단추를 클릭하여 **패킷 캡처** 페이지를 연 다음 **중지를** 클릭합니다. 그러면 **패킷 캡처 중지** 페이지가 열립니다. 이 시점에서 패킷 캡처는 아직 중지되지 않았습니다.
   :::image type="content" source="./media/packet-capture-site-to-site-portal/packet-stop.png" alt-text="중지 단추를 보여주는 그래픽." lightbox="./media/packet-capture-site-to-site-portal/packet-stop-expand.png":::

1. 패킷 **캡처 중지** 페이지에서 이전에 만든 스토리지 컨테이너의 *SaS URL을* **출력 Sas URL** 필드에 붙여넣습니다. 패킷 캡처 파일이 저장되는 위치입니다.

   :::image type="content" source="./media/packet-capture-site-to-site-portal/output.png" alt-text="출력 Sas URL을 붙여넣을 위치를 보여주는 그래픽" lightbox="./media/packet-capture-site-to-site-portal/output-expand.png":::
1. 다음으로 **중지를** 클릭합니다. 패킷 캡처가 중지되고 PCAP 파일이 만들어지고 스토리지 계정에 저장됩니다. 이 작업을 완료하는 데 몇 분 정도 걸릴 수 있습니다.

### <a name="to-abort-a-packet-capture"></a>패킷 캡처를 중단하려면

어떤 이유로든 패킷 캡처를 중단해야 하는 경우 가상 허브로 이동하고 **패킷 캡처** 단추를 클릭하여 패킷 **캡처 페이지를** 연 다음 **중단을** 클릭합니다. 패킷 캡처가 중단되면 PCAP 파일이 생성되거나 저장되지 않습니다.

## <a name="view-a-packet-capture"></a><a name="view"></a>패킷 캡처 보기

이 섹션에서는 패킷 캡처 PCAP 파일을 다운로드하여 볼 수 있도록 도와줍니다.

1. Azure Portal 만든 스토리지 계정으로 이동합니다.
1. **컨테이너를 클릭하여** 스토리지 계정에 대한 컨테이너를 봅니다.
1. 만든 컨테이너를 클릭합니다.
1. 폴더 구조를 탐색하여 PCAP 파일을 찾습니다. 폴더 이름과 구조는 날짜 및 UTC 시간을 기반으로 합니다. PCAP 파일을 찾으면 **다운로드를** 클릭합니다.

   :::image type="content" source="./media/packet-capture-site-to-site-portal/download.png" alt-text="파일을 다운로드하는 방법을 보여주는 그래픽" lightbox="./media/packet-capture-site-to-site-portal/download-expand.png":::
1. 패킷 캡처 데이터 파일은 PCAP 형식으로 생성됩니다. Wireshark 또는 다른 일반적으로 사용 가능한 애플리케이션을 사용하여 PCAP 파일을 열 수 있습니다.

## <a name="key-considerations"></a>주요 고려 사항

* 패킷 캡처를 실행하면 성능에 영향을 줄 수 있습니다. 패킷 캡처가 필요하지 않은 경우 이를 중지해야 합니다.
* 제안된 최소 패킷 캡처 기간은 600초입니다. 경로에 있는 여러 구성 요소 간 동기화 문제로 인해 짧은 시간 동안 패킷 캡처를 수행하면 전체 데이터가 제공되지 않을 수 있습니다.
* 패킷 캡처 데이터 파일은 PCAP 형식으로 생성됩니다. Wireshark 또는 일반적으로 사용 가능한 기타 애플리케이션을 사용하여 PCAP 파일을 엽니다.
* SASurl 매개 변수가 올바르게 구성되지 않은 경우 스토리지 오류로 추적이 실패할 수 있습니다.

## <a name="next-steps"></a>다음 단계

Azure Virtual WAN 대한 자세한 내용은 [FAQ를 참조하세요.](virtual-wan-faq.md)
