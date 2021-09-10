---
title: CLI를 사용하여 VPN Gateway에서 BGP 구성
titleSuffix: Azure VPN Gateway
description: CLI를 사용하여 VPN Gateway에서 BGP를 구성하는 방법을 알아봅니다.
services: vpn-gateway
author: yushwang
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/02/2020
ms.author: yushwang
ms.openlocfilehash: 47097be22fc9c75d7c56c8cb4d1f384936f7033a
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122536666"
---
# <a name="how-to-configure-bgp-on-an-azure-vpn-gateway-by-using-cli"></a>CLI를 사용하여 Azure VPN Gateway에서 BGP를 구성하는 방법

이 문서에서는 [Resource Manager 배포 모델](../azure-resource-manager/management/deployment-models.md) 및 Azure CLI를 사용하여 프레미스 간 S2S(사이트 간) VPN 연결 및 VNet 간 연결(즉, 가상 네트워크 간 연결)에서 BGP를 사용하도록 설정하도록 도와줍니다.

## <a name="about-bgp"></a>BGP 정보

BGP는 두 개 이상의 네트워크 간에 라우팅 및 연결 정보를 교환하도록 인터넷에서 일반적으로 사용하는 표준 라우팅 프로토콜입니다. BGP를 사용하여 BGP 피어 또는 인접 항목이라는 온-프레미스 VPN 디바이스들이 경로를 교환할 수 있습니다. 경로는 접두사의 가용성 및 연결에 관하여 두 게이트웨이가 모두 관련 게이트웨이 또는 라우터를 통과하도록 지정합니다. BGP 게이트웨이가 하나의 BGP 피어에서 파악한 경로를 다른 모든 BGP 피어로 전파하여 BGP를 통해 여러 네트워크 간에 전송 라우팅을 사용할 수도 있습니다.

BGP의 이점에 대한 자세한 내용 및 BGP 사용의 기술 요구 사항과 고려 사항을 이해하려면 [Azure VPN Gateway에서의 BGP 개요](vpn-gateway-bgp-overview.md)를 참조하세요.

이 문서를 통해 다음과 같은 작업을 수행할 수 있습니다.

* [VPN Gateway에 대해 BGP를 사용하도록 설정](#enablebgp)(필수)

  그러고 나서 다음 섹션 중 하나 또는 둘 다를 완료할 수 있습니다.

* [BGP를 사용하여 프레미스 간 연결 설정](#crossprembgp)
* [BGP를 사용하여 VNet 간 연결 설정](#v2vbgp)

세 섹션은 각각 네트워크 연결에서 BGP를 사용하기 위한 기본 구성 요소를 형성합니다. 세 섹션을 모두 완료하면 다음 다이어그램에 표시된 대로 토폴로지를 빌드합니다.

![BGP 토폴로지](./media/vpn-gateway-bgp-resource-manager-ps/bgp-crosspremv2v.png)

섹션을 결합하여 필요에 따라 더 복잡한 다중 홉 전송 네트워크를 빌드할 수 있습니다.

## <a name="enable-bgp-for-your-vpn-gateway"></a><a name ="enablebgp"></a>VPN Gateway에 대해 BGP를 사용하도록 설정

이 섹션은 다른 두 구성 섹션을 수행하기 전에 반드시 수행해야 합니다. 다음 구성 단계에서는 다음 다이어그램에 표시된 대로 Azure VPN Gateway의 BGP 매개 변수를 설정합니다.

![BGP 게이트웨이](./media/vpn-gateway-bgp-resource-manager-ps/bgp-gateway.png)

### <a name="before-you-begin"></a>시작하기 전에

최신 버전의 CLI 명령(2.0 이상)을 설치합니다. CLI 설치 명령에 대한 자세한 내용은 [Azure CLI 설치](/cli/azure/install-azure-cli) 및 [Azure CLI 시작](/cli/azure/get-started-with-azure-cli)을 참조하세요.

### <a name="step-1-create-and-configure-testvnet1"></a>1단계: TestVNet1 만들기 및 구성

#### <a name="1-connect-to-your-subscription"></a><a name="Login"></a>1. 구독에 연결

[!INCLUDE [CLI login](../../includes/vpn-gateway-cli-login-include.md)]

#### <a name="2-create-a-resource-group"></a>2. 리소스 그룹 만들기

다음 예제에서는 “eastus” 위치에 TestRG1이라는 리소스 그룹을 만듭니다. 가상 네트워크를 만들려는 지역에 이미 리소스 그룹이 있는 경우 그 리소스 그룹을 대신 사용할 수 있습니다.

```azurecli
az group create --name TestBGPRG1 --location eastus
```

#### <a name="3-create-testvnet1"></a>3. TestVNet1 만들기

다음 예제에서는 TestVNet1이라는 가상 네트워크와 GatewaySubnet, FrontEnd 및 Backend라는 세 개의 서브넷을 만듭니다. 값을 대체할 때 언제나 게이트웨이 서브넷 이름을 'GatewaySubnet'이라고 구체적으로 지정해야 합니다. 다른 이름을 지정하는 경우 게이트웨이 만들기가 실패합니다.

첫 번째 명령은 프런트 엔드 주소 공간 및 프런트 엔드 서브넷을 만듭니다. 두 번째 명령은 백 엔드 서브넷에 대한 추가 주소 공간을 만듭니다. 세 번째와 네 번째 명령은 백 엔드 서브넷 및 게이트웨이 서브넷을 만듭니다.

```azurecli
az network vnet create -n TestVNet1 -g TestBGPRG1 --address-prefix 10.11.0.0/16 -l eastus --subnet-name FrontEnd --subnet-prefix 10.11.0.0/24 
 
az network vnet update -n TestVNet1 --address-prefixes 10.11.0.0/16 10.12.0.0/16 -g TestBGPRG1 
 
az network vnet subnet create --vnet-name TestVNet1 -n BackEnd -g TestBGPRG1 --address-prefix 10.12.0.0/24 
 
az network vnet subnet create --vnet-name TestVNet1 -n GatewaySubnet -g TestBGPRG1 --address-prefix 10.12.255.0/27 
```

### <a name="step-2-create-the-vpn-gateway-for-testvnet1-with-bgp-parameters"></a>2단계: BGP 매개 변수를 사용하여 TestVNet1용 VPN 게이트웨이 만들기

#### <a name="1-create-the-public-ip-address"></a>1. 공용 IP 주소 만들기

공용 IP 주소를 요청합니다. 공용 IP 주소는 가상 네트워크용으로 만들 VPN Gateway에 할당됩니다.

```azurecli
az network public-ip create -n GWPubIP -g TestBGPRG1 --allocation-method Dynamic 
```

#### <a name="2-create-the-vpn-gateway-with-the-as-number"></a>2. AS 번호를 사용하여 VPN 게이트웨이 만들기

TestVNet1용 가상 네트워크 게이트웨이를 만듭니다. BGP에는 경로 기반 VPN Gateway가 필요합니다. 또한 TestVNet1에 대한 ASN(익명 시스템 번호)을 설정하기 위해 추가 매개 변수 `-Asn`도 필요합니다. 종종 선택한 게이트웨이 SKU에 따라 게이트웨이를 만드는 데 45분 이상 걸릴 수 있습니다.

`--no-wait` 매개 변수를 사용하여 이 명령을 실행하면 피드백 또는 출력이 보이지 않습니다. `--no-wait` 매개 변수는 백그라운드에서 게이트웨이를 만드는 것을 허용합니다. VPN 게이트웨이가 즉시 만들어진다는 것을 의미하지는 않습니다.

```azurecli
az network vnet-gateway create -n VNet1GW -l eastus --public-ip-address GWPubIP -g TestBGPRG1 --vnet TestVNet1 --gateway-type Vpn --sku HighPerformance --vpn-type RouteBased --asn 65010 --no-wait
```

#### <a name="3-obtain-the-azure-bgp-peer-ip-address"></a>3. Azure BGP 피어 IP 주소 가져오기

게이트웨이를 만든 후 Azure VPN Gateway에서 BGP 피어 IP 주소를 가져와야 합니다. 온-프레미스 VPN 디바이스에 대해 VPN Gateway를 BGP 피어로 구성하려면 이 주소가 필요합니다.

다음 명령을 실행하고 출력의 위쪽에 있는 `bgpSettings` 섹션 확인

```azurecli
az network vnet-gateway list -g TestBGPRG1 
 
  
"bgpSettings": { 
      "asn": 65010, 
      "bgpPeeringAddress": "10.12.255.30", 
      "peerWeight": 0 
    }
```

게이트웨이가 만들어지면 BGP를 사용하여 프레미스 간 연결 또는 VNet 간 연결을 설정하도록 이 게이트웨이를 사용할 수 있습니다.

## <a name="establish-a-cross-premises-connection-with-bgp"></a><a name ="crossprembgp"></a>BGP를 사용하여 프레미스 간 연결 설정

프레미스 간 연결을 설정하려면 온-프레미스 VPN 디바이스를 나타내는 로컬 네트워크 게이트웨이를 만들어야 합니다. 그런 후 Azure VPN Gateway를 로컬 네트워크 게이트웨이와 연결합니다. 이러한 단계는 다른 연결을 만들 때와 유사하지만 BGP 구성 매개 변수를 지정하는 데 필요한 추가 속성을 포함합니다.

![프레미스 간에 대한 BGP](./media/vpn-gateway-bgp-resource-manager-ps/bgp-crossprem.png)


### <a name="step-1-create-and-configure-the-local-network-gateway"></a>1단계: 로컬 네트워크 게이트웨이 만들기 및 구성

이 연습에서는 다이어그램에 표시된 구성을 계속 빌드합니다. 값을 구성에 사용할 값으로 바꾸어야 합니다. 로컬 네트워크 게이트웨이로 작업하는 경우 다음 사항을 염두에 두어야 합니다.

* 로컬 네트워크 게이트웨이는 VPN 게이트웨이와 같은 위치 및 리소스 그룹에 있을 수도 있고 다른 위치 및 리소스 그룹에 있을 수도 있습니다. 이 예제에서는 다른 위치의 다른 리소스 그룹에 게이트웨이를 표시됩니다.
* 로컬 네트워크 게이트웨이에 대해 선언해야 하는 최소 접두사는 VPN 디바이스의 BGP 피어 IP 주소의 호스트 주소입니다. 이 경우에는 10.51.255.254/32의 /32 접두사입니다.
* 다시 확인하면 온-프레미스 네트워크와 Azure Virtual Network 간에는 서로 다른 BGP ASN을 사용해야 합니다. 동일한 경우 온-프레미스 VPN 디바이스가 이미 다른 BGP 인접과의 피어에 ASN을 사용하고 있으면 VNet ASN을 변경해야 합니다.

계속하기 전에 이 연습의 [VPN Gateway에 대해 BGP를 사용하도록 설정](#enablebgp) 섹션을 완료했는지 그리고 구독 1에 여전히 연결되어 있는지 확인합니다. 참고로 이 예제에서는 새 리소스 그룹 만듭니다. 또한 로컬 네트워크 게이트웨이에 대한 두 개의 추가 매개 변수(`Asn` 및 `BgpPeerAddress`)를 확인합니다.

```azurecli
az group create -n TestBGPRG5 -l eastus2 
 
az network local-gateway create --gateway-ip-address 23.99.221.164 -n Site5 -g TestBGPRG5 --local-address-prefixes 10.51.255.254/32 --asn 65050 --bgp-peering-address 10.51.255.254
```

### <a name="step-2-connect-the-vnet-gateway-and-local-network-gateway"></a>2단계: VNet 게이트웨이 및 로컬 네트워크 게이트웨이 연결

이 단계에서는 TestVNet1에서 Site5까지 연결을 만듭니다. 이 연결에 BGP를 사용하려면 `--enable-bgp` 매개 변수를 지정해야 합니다. 

이 예에서 가상 네트워크 게이트웨이와 로컬 네트워크 게이트웨이는 서로 다른 리소스 그룹에 존재합니다. 게이트웨이들이 서로 다른 리소스 그룹에 있는 경우 두 게이트웨이의 전체 리소스 ID를 지정하여 가상 네트워크 간에 연결을 설정해야 합니다.

#### <a name="1-get-the-resource-id-of-vnet1gw"></a>1. VNet1GW의 리소스 ID 가져오기

다음 명령의 출력을 사용하여 VNet1GW의 리소스 ID를 가져옵니다.

```azurecli
az network vnet-gateway show -n VNet1GW -g TestBGPRG1
```

출력에서 `"id":` 줄을 찾습니다. 따옴표 안의 값은 다음 섹션에서 연결을 만드는 데 필요합니다.

예제 출력:

```
{ 
  "activeActive": false, 
  "bgpSettings": { 
    "asn": 65010, 
    "bgpPeeringAddress": "10.12.255.30", 
    "peerWeight": 0 
  }, 
  "enableBgp": true, 
  "etag": "W/\"<your etag number>\"", 
  "gatewayDefaultSite": null, 
  "gatewayType": "Vpn", 
  "id": "/subscriptions/<subscription ID>/resourceGroups/TestBGPRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW",
```

연결을 만들 때 쉽게 붙여 넣을 수 있도록 `"id":` 다음에 나오는 값을 텍스트 편집기(예: 메모장)에 복사합니다. 

```
"id": "/subscriptions/<subscription ID>/resourceGroups/TestRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW"
```

#### <a name="2-get-the-resource-id-of-site5"></a>2. Site5의 리소스 ID 가져오기

다음 명령을 사용하여 출력에서 Site5의 리소스 ID를 가져옵니다.

```azurecli
az network local-gateway show -n Site5 -g TestBGPRG5
```

#### <a name="3-create-the-testvnet1-to-site5-connection"></a>3. TestVNet1-Site5 연결 만들기

이 단계에서는 TestVNet1에서 Site5까지 연결을 만듭니다. 앞에서 설명한 대로 동일한 Azure VPN Gateway에 대해 BGP와 비BGP를 모두 연결할 수 있습니다. 연결 속성에서 BGP를 사용하도록 설정할 수 없으면 BGP 매개 변수가 두 게이트웨이에 이미 구성된 경우에도 Azure가 이 연결에 대해 BGP를 사용하도록 설정하지 않습니다. 구독 ID를 사용자 고유의 ID로 바꾸어야 합니다.

```azurecli
az network vpn-connection create -n VNet1ToSite5 -g TestBGPRG1 --vnet-gateway1 /subscriptions/<subscription ID>/resourceGroups/TestBGPRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW --enable-bgp -l eastus --shared-key "abc123" --local-gateway2 /subscriptions/<subscription ID>/resourceGroups/TestBGPRG5/providers/Microsoft.Network/localNetworkGateways/Site5 --no-wait
```

이 연습을 위해 다음 예제에서 온-프레미스 VPN 디바이스의 BGP 구성 섹션에 입력할 매개 변수를 나열합니다.

```
Site5 ASN            : 65050
Site5 BGP IP         : 10.52.255.254
Prefixes to announce : (for example) 10.51.0.0/16 and 10.52.0.0/16
Azure VNet ASN       : 65010
Azure VNet BGP IP    : 10.12.255.30
Static route         : Add a route for 10.12.255.30/32, with nexthop being the VPN tunnel interface on your device
eBGP Multihop        : Ensure the "multihop" option for eBGP is enabled on your device if needed
```

몇 분 후 연결이 설정됩니다. BGP 피어링 세션은 IPsec 연결이 설정된 후에 시작됩니다.

## <a name="establish-a-vnet-to-vnet-connection-with-bgp"></a><a name ="v2vbgp"></a>BGP를 사용하여 VNet 간 연결 설정

이 섹션에서는 다음 다이어그램에 표시된 대로 BGP를 사용하여 VNet 간 연결을 추가합니다. 

![VNet-VNet에 대한 BGP](./media/vpn-gateway-bgp-resource-manager-ps/bgp-vnet2vnet.png)

다음 지침은 이전 섹션의 단계에서 계속합니다. BGP를 사용하여 TestVNet1 및 VPN Gateway를 만들고 구성하려면 [VPN Gateway에 대해 BGP를 사용하도록 설정](#enablebgp) 섹션을 완료해야 합니다.

### <a name="step-1-create-testvnet2-and-the-vpn-gateway"></a>1단계: TestVNet2 및 VPN 게이트웨이 만들기

새 가상 네트워크 TestVNet2의 IP 주소 공간이 VNet 범위와 겹치지 않는지 확인해야 합니다.

이 예제에서 가상 네트워크는 동일한 구독에 속합니다. 다른 구독 간의 VNet 간 연결을 설정할 수 있습니다. 자세한 내용은 [VNet 간 연결 구성](vpn-gateway-howto-vnet-vnet-cli.md)을 참조하세요. BGP를 사용하는 연결을 만들 때 `-EnableBgp $True`를 추가해야 합니다.

#### <a name="1-create-a-new-resource-group"></a>1. 새 리소스 그룹 만들기

```azurecli
az group create -n TestBGPRG2 -l westus
```

#### <a name="2-create-testvnet2-in-the-new-resource-group"></a>2. 새 리소스 그룹에서 TestVNet2 만들기

첫 번째 명령은 프런트 엔드 주소 공간 및 프런트 엔드 서브넷을 만듭니다. 두 번째 명령은 백 엔드 서브넷에 대한 추가 주소 공간을 만듭니다. 세 번째와 네 번째 명령은 백 엔드 서브넷 및 게이트웨이 서브넷을 만듭니다.

```azurecli
az network vnet create -n TestVNet2 -g TestBGPRG2 --address-prefix 10.21.0.0/16 -l westus --subnet-name FrontEnd --subnet-prefix 10.21.0.0/24 
 
az network vnet update -n TestVNet2 --address-prefixes 10.21.0.0/16 10.22.0.0/16 -g TestBGPRG2 
 
az network vnet subnet create --vnet-name TestVNet2 -n BackEnd -g TestBGPRG2 --address-prefix 10.22.0.0/24 
 
az network vnet subnet create --vnet-name TestVNet2 -n GatewaySubnet -g TestBGPRG2 --address-prefix 10.22.255.0/27
```

#### <a name="3-create-the-public-ip-address"></a>3. 공용 IP 주소 만들기

공용 IP 주소를 요청합니다. 공용 IP 주소는 가상 네트워크용으로 만들 VPN Gateway에 할당됩니다.

```azurecli
az network public-ip create -n GWPubIP2 -g TestBGPRG2 --allocation-method Dynamic
```

#### <a name="4-create-the-vpn-gateway-with-the-as-number"></a>4. AS 번호를 사용하여 VPN 게이트웨이 만들기

TestVNet2용 가상 네트워크 게이트웨이를 만듭니다. Azure VPN 게이트웨이에서 기본 ASN을 재정의해야 합니다. BGP 및 전송 라우팅을 사용할 수 있도록 하려면 연결된 가상 네트워크용 ASN은 서로 달라야 합니다.
 
```azurecli
az network vnet-gateway create -n VNet2GW -l westus --public-ip-address GWPubIP2 -g TestBGPRG2 --vnet TestVNet2 --gateway-type Vpn --sku Standard --vpn-type RouteBased --asn 65020 --no-wait
```

### <a name="step-2-connect-the-testvnet1-and-testvnet2-gateways"></a>2단계: TestVNet1 및 TestVNet2 게이트웨이 연결

이 단계에서는 TestVNet1에서 Site5까지 연결을 만듭니다. 이 연결에 BGP를 사용하려면 `--enable-bgp` 매개 변수를 지정해야 합니다.

다음 예제에서 가상 네트워크 게이트웨이와 로컬 네트워크 게이트웨이는 서로 다른 리소스 그룹에 있습니다. 게이트웨이들이 서로 다른 리소스 그룹에 있는 경우 두 게이트웨이의 전체 리소스 ID를 지정하여 가상 네트워크 간에 연결을 설정해야 합니다. 

#### <a name="1-get-the-resource-id-of-vnet1gw"></a>1. VNet1GW의 리소스 ID 가져오기 

다음 명령의 출력에서 VNet1GW의 리소스 ID를 가져옵니다.

```azurecli
az network vnet-gateway show -n VNet1GW -g TestBGPRG1
```

#### <a name="2-get-the-resource-id-of-vnet2gw"></a>2. VNet2GW의 리소스 ID 가져오기

다음 명령의 출력에서 VNet2GW의 리소스 ID를 가져옵니다.

```azurecli
az network vnet-gateway show -n VNet2GW -g TestBGPRG2
```

#### <a name="3-create-the-connections"></a>3. 연결 만들기

TestVNet1에서 TestVNet2까지 및 TestVNet2에서 TestVNet1까지의 연결을 만듭니다. 구독 ID를 사용자 고유의 ID로 바꾸어야 합니다.

```azurecli
az network vpn-connection create -n VNet1ToVNet2 -g TestBGPRG1 --vnet-gateway1 /subscriptions/<subscription ID>/resourceGroups/TestBGPRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW --enable-bgp -l eastus --shared-key "efg456" --vnet-gateway2 /subscriptions/<subscription ID>/resourceGroups/TestBGPRG2/providers/Microsoft.Network/virtualNetworkGateways/VNet2GW
```

```azurecli
az network vpn-connection create -n VNet2ToVNet1 -g TestBGPRG2 --vnet-gateway1 /subscriptions/<subscription ID>/resourceGroups/TestBGPRG2/providers/Microsoft.Network/virtualNetworkGateways/VNet2GW --enable-bgp -l westus --shared-key "efg456" --vnet-gateway2 /subscriptions/<subscription ID>/resourceGroups/TestBGPRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW
```

> [!IMPORTANT]
> *두 연결 모두* 에 대해 BGP를 사용하도록 설정합니다.
> 
> 

이 단계를 완료한 후 몇 분 후에 연결이 설정됩니다. VNet 간 연결이 완료되면 BGP 피어링 세션이 끝납니다.

## <a name="next-steps"></a>다음 단계

연결이 완료되면 가상 네트워크에 가상 머신을 추가할 수 있습니다. 단계를 보려면 [가상 머신 만들기](../virtual-machines/windows/quick-create-portal.md)를 참조하세요.
