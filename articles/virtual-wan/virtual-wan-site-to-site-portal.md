---
title: '자습서: Azure Virtual WAN을 사용하여 사이트 간 연결 만들기'
description: Azure Virtual WAN을 사용하여 Azure에 사이트 간 VPN 연결을 만드는 방법을 알아봅니다.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: tutorial
ms.date: 08/18/2021
ms.author: cherylmc
ms.openlocfilehash: ece8300ee9d44699dfcce8fd89b1e07b94d99df9
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124823282"
---
# <a name="tutorial-create-a-site-to-site-connection-using-azure-virtual-wan"></a>자습서: Azure Virtual WAN을 사용하여 사이트 간 연결 만들기

이 자습서에서는 가상 WAN을 사용하여 IPsec/IKE(IKEv1 및 IKEv2) VPN 연결을 통해 Azure에서 리소스를 연결하는 방법을 보여줍니다. 이 연결 유형은 할당된 외부 연결 공용 IP 주소를 갖고 있는 온-프레미스에 있는 VPN 디바이스를 필요로 합니다. Virtual WAN에 대한 자세한 내용은 [Virtual WAN 개요](virtual-wan-about.md)를 참조하세요.

이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * 가상 WAN 만들기
> * 허브 기본 설정 구성
> * 사이트 간 VPN 게이트웨이 설정 구성
> * 사이트 만들기
> * 허브에 사이트 연결
> * 허브에 VPN 사이트 연결
> * 허브에 VNet 연결
> * 구성 파일 다운로드
> * VPN 게이트웨이 보기 또는 편집

> [!NOTE]
> 사이트가 많은 경우 일반적으로 [가상 WAN 파트너](https://aka.ms/virtualwan)를 사용하여 이러한 구성을 만듭니다. 하지만 네트워킹에 익숙하고 자신의 VPN 디바이스를 구성하는 데 능숙한 경우에는 구성을 직접 만들 수 있습니다.
>

:::image type="content" source="./media/virtual-wan-about/virtualwan.png" alt-text="스크린샷은 Virtual WAN에 대한 네트워킹 다이어그램을 보여줍니다.":::

## <a name="prerequisites"></a>필수 구성 요소

구성을 시작하기 전에 다음 기준을 충족하는지 확인합니다.

[!INCLUDE [Before you begin](../../includes/virtual-wan-before-include.md)]

## <a name="create-a-virtual-wan"></a><a name="openvwan"></a>Virtual WAN 만들기

[!INCLUDE [Create a virtual WAN](../../includes/virtual-wan-create-vwan-include.md)]

## <a name="configure-hub-settings"></a><a name="hub"></a>허브 설정 구성

허브는 사이트 간, Express 경로 또는 지점 및 사이트 간 기능을 위한 게이트웨이를 포함할 수 있는 가상 네트워크입니다. 이 자습서에서는 먼저 가상 허브에 대한 **기본 사항** 탭을 채운 다음, 다음 섹션에서 사이트 간 탭을 계속 작성합니다. 빈 허브(게이트웨이를 포함하지 않는 허브)를 만든 다음, 나중에 게이트웨이(S2S, P2S, ExpressRoute 등)를 추가할 수 있습니다. 허브를 만든 후에는, 사이트를 연결하거나 허브 안에 게이트웨이를 만들지 않더라도 허브 요금이 청구됩니다.

[!INCLUDE [Create a hub](../../includes/virtual-wan-tutorial-s2s-hub-include.md)]

## <a name="configure-a-site-to-site-gateway"></a><a name="gateway"></a>사이트 간 게이트웨이 구성

이 섹션에서는 사이트 간 연결 설정을 구성한 다음, 허브 및 사이트 간 VPN 게이트웨이 만들기를 진행합니다. 허브와 게이트웨이를 만드는 데 약 30분 정도 걸릴 수 있습니다.

[!INCLUDE [Create a gateway](../../includes/virtual-wan-tutorial-s2s-gateway-include.md)]

## <a name="create-a-site"></a><a name="site"></a>사이트 만들기

이 섹션에서는 사이트를 만듭니다. 사이트는 실제 위치에 해당합니다. 필요한 만큼 사이트를 만듭니다. 예를 들어 NY에 지사가 있고, 런던에 지사가 있고, LA에 지사가 있는 경우 3개의 사이트를 별도로 만들 수 있습니다. 이 사이트에는 온-프레미스 VPN 디바이스 엔드포인트가 포함됩니다. 가상 WAN에서 가상 허브당 최대 1000개의 사이트를 만들 수 있습니다. 허브가 여러 개 있는 경우 해당 허브당 1,000개의 사이트를 만들 수 있습니다. Virtual WAN 파트너 CPE 디바이스가 있는 경우 Azure에 대한 자동화에 대해 알아보려면 해당 파트너에게 문의하세요. 일반적으로 자동화는 대규모 분기 정보를 Azure로 내보내고 CPE에서 Azure Virtual WAN VPN 게이트웨이로의 연결을 설정하는 간단한 클릭 환경을 의미합니다. 자세한 내용은 [Azure에서 CPE 파트너로의 Automation 지침](virtual-wan-configure-automation-providers.md)을 참조하세요.

[!INCLUDE [Create a site](../../includes/virtual-wan-tutorial-s2s-site-include.md)]

## <a name="connect-the-vpn-site-to-a-hub"></a><a name="connectsites"></a>허브에 VPN 사이트 연결

이 섹션에서는 VPN 사이트를 허브에 연결합니다.

[!INCLUDE [Connect VPN sites](../../includes/virtual-wan-tutorial-s2s-connect-vpn-site-include.md)]

## <a name="connect-a-vnet-to-the-hub"></a><a name="vnet"></a>허브에 VNet 연결

이 섹션에서는 허브와 VNet 사이에 연결을 만듭니다.

[!INCLUDE [Connect](../../includes/virtual-wan-connect-vnet-hub-include.md)]

## <a name="download-vpn-configuration"></a><a name="device"></a>VPN 구성 다운로드

VPN 디바이스 구성 파일을 사용하여 온-프레미스 VPN 디바이스를 구성합니다. 기본 단계는 다음과 같습니다. 구성 파일에 포함된 내용 및 VPN 디바이스를 구성하는 방법에 대한 정보는 다음과 같습니다. 

1. **가상 HUB -> VPN(사이트 간)** 페이지로 이동합니다.

1. **VPN(사이트 간)** 페이지의 위에서 **VPN 구성 다운로드** 를 선택합니다. Azure는 'microsoft-network-[location]' 리소스 그룹에 스토리지 계정을 만듭니다. 여기서 location은 WAN의 위치입니다.

1. 파일 만들기가 끝나면 링크를 클릭하여 파일을 다운로드합니다. 파일 콘텐츠에 대한 자세한 내용은 이 섹션의 [VPN 디바이스 구성 파일 정보](#config-file)를 참조하세요.

1. 온-프레미스 VPN 디바이스에 구성을 적용합니다. 자세한 내용은 이 섹션의 [VPN 디바이스 구성](#vpn-device)을 참조하세요.

1. VPN 디바이스에 구성을 적용한 후에는 Azure에서 만든 스토리지 계정을 유지할 필요가 없습니다. 삭제할 수 있습니다.

### <a name="about-the-vpn-device-configuration-file"></a><a name="config-file"></a>VPN 디바이스 구성 파일 정보

디바이스 구성 파일에는 온-프레미스 VPN 디바이스를 구성할 때 사용할 설정이 포함되어 있습니다. 이 파일을 볼 때 다음 정보를 확인합니다.

* **vpnSiteConfiguration -** 이 섹션은 Virtual WAN에 연결된 사이트로 설정된 디바이스 정보를 나타냅니다. 여기에는 분기 디바이스의 이름 및 공용 IP 주소가 포함됩니다.
* **vpnSiteConnections -** 이 섹션에서는 다음 설정에 관한 정보를 제공합니다.

    * 가상 허브 VNet의 **주소 공간**.<br>예제:
 
        ```
        "AddressSpace":"10.1.0.0/24"
        ```
    * 허브에 연결된 VNet의 **주소 공간**.<br>예제:

         ```
        "ConnectedSubnets":["10.2.0.0/16","10.3.0.0/16"]
         ```
    * 가상 허브 vpngateway의 **IP 주소**. vpngateway의 각 연결은 활성-활성 구성의 2개 터널로 구성되므로 이 파일에 두 IP 주소가 모두 나열됩니다. 이 예제에서는 각 사이트에 대한 “Instance0” 및 “Instance1”이 표시됩니다.<br>예제:

        ``` 
        "Instance0":"104.45.18.186"
        "Instance1":"104.45.13.195"
        ```
    * BGP, 미리 공유한 키 등의 **Vpngateway 연결 구성 세부 정보**. PSK는 자동으로 생성되는 미리 공유한 키입니다. 사용자 지정 PSK의 개요 페이지에서 연결을 언제든지 편집할 수 있습니다.
  
### <a name="example-device-configuration-file"></a>예제 디바이스 구성 파일

  ```
  { 
      "configurationVersion":{ 
         "LastUpdatedTime":"2018-07-03T18:29:49.8405161Z",
         "Version":"r403583d-9c82-4cb8-8570-1cbbcd9983b5"
      },
      "vpnSiteConfiguration":{ 
         "Name":"testsite1",
         "IPAddress":"73.239.3.208"
      },
      "vpnSiteConnections":[ 
         { 
            "hubConfiguration":{ 
               "AddressSpace":"10.1.0.0/24",
               "Region":"West Europe",
               "ConnectedSubnets":[ 
                  "10.2.0.0/16",
                  "10.3.0.0/16"
               ]
            },
            "gatewayConfiguration":{ 
               "IpAddresses":{ 
                  "Instance0":"104.45.18.186",
                  "Instance1":"104.45.13.195"
               }
            },
            "connectionConfiguration":{ 
               "IsBgpEnabled":false,
               "PSK":"bkOWe5dPPqkx0DfFE3tyuP7y3oYqAEbI",
               "IPsecParameters":{ 
                  "SADataSizeInKilobytes":102400000,
                  "SALifeTimeInSeconds":3600
               }
            }
         }
      ]
   },
   { 
      "configurationVersion":{ 
         "LastUpdatedTime":"2018-07-03T18:29:49.8405161Z",
         "Version":"1f33f891-e1ab-42b8-8d8c-c024d337bcac"
      },
      "vpnSiteConfiguration":{ 
         "Name":" testsite2",
         "IPAddress":"66.193.205.122"
      },
      "vpnSiteConnections":[ 
         { 
            "hubConfiguration":{ 
               "AddressSpace":"10.1.0.0/24",
               "Region":"West Europe"
            },
            "gatewayConfiguration":{ 
               "IpAddresses":{ 
                  "Instance0":"104.45.18.187",
                  "Instance1":"104.45.13.195"
               }
            },
            "connectionConfiguration":{ 
               "IsBgpEnabled":false,
               "PSK":"XzODPyAYQqFs4ai9WzrJour0qLzeg7Qg",
               "IPsecParameters":{ 
                  "SADataSizeInKilobytes":102400000,
                  "SALifeTimeInSeconds":3600
               }
            }
         }
      ]
   },
   { 
      "configurationVersion":{ 
         "LastUpdatedTime":"2018-07-03T18:29:49.8405161Z",
         "Version":"cd1e4a23-96bd-43a9-93b5-b51c2a945c7"
      },
      "vpnSiteConfiguration":{ 
         "Name":" testsite3",
         "IPAddress":"182.71.123.228"
      },
      "vpnSiteConnections":[ 
         { 
            "hubConfiguration":{ 
               "AddressSpace":"10.1.0.0/24",
               "Region":"West Europe"
            },
            "gatewayConfiguration":{ 
               "IpAddresses":{ 
                  "Instance0":"104.45.18.187",
                  "Instance1":"104.45.13.195"
               }
            },
            "connectionConfiguration":{ 
               "IsBgpEnabled":false,
               "PSK":"YLkSdSYd4wjjEThR3aIxaXaqNdxUwSo9",
               "IPsecParameters":{ 
                  "SADataSizeInKilobytes":102400000,
                  "SALifeTimeInSeconds":3600
               }
            }
         }
      ]
   }
  ```

### <a name="configuring-your-vpn-device"></a><a name="vpn-device"></a>VPN 디바이스 구성

>[!NOTE]
> Virtual WAN 파트너 솔루션을 사용하여 작업하는 경우 VPN 디바이스 구성이 자동으로 수행됩니다. 디바이스 컨트롤러는 Azure에서 구성 파일을 가져오고 Azure에 대한 연결을 설정하는 디바이스에 적용합니다. 따라서 VPN 디바이스를 수동으로 구성하는 방법을 알 필요가 없습니다.
>

디바이스를 구성하는 지침이 필요한 경우에는 [VPN 디바이스 구성 스크립트 페이지](~/articles/vpn-gateway/vpn-gateway-about-vpn-devices.md#configscripts)의 지침을 다음 주의 사항과 함께 사용하면 됩니다.

* VPN 디바이스 페이지의 지침은 Virtual WAN용으로 작성되지 않았지만 구성 파일의 Virtual WAN 값을 사용하여 VPN 디바이스를 수동으로 구성할 수 있습니다.
 
* VPN Gateway용 다운로드 가능한 디바이스 구성 스크립트는 구성이 다르기 때문에 Virtual WAN에서는 작동하지 않습니다.

* 새 가상 WAN은 IKEv1 및 IKEv2를 모두 지원할 수 있습니다.

* 가상 WAN은 정책 기반 및 경로 기반 VPN 디바이스와 디바이스 지침을 모두 사용할 수 있습니다.

## <a name="view-or-edit-gateway-settings"></a><a name="gateway-config">게이트웨이 설정 보기 또는 편집</a>

언제든지 **가상 HUB -> VPN(사이트 간)** 으로 이동하고 **보기/구성** 을 선택하여 VPN 게이트웨이 설정을 보고 편집할 수 있습니다.

:::image type="content" source="media/virtual-wan-site-to-site-portal/view-configuration-1.png" alt-text="'보기/구성' 작업을 가리키는 화살표가 있는 'VPN(사이트 간)' 페이지를 보여주는 스크린샷." lightbox="media/virtual-wan-site-to-site-portal/view-configuration-1-expand.png":::

**VPN Gateway 편집** 페이지에서 다음 설정을 확인할 수 있습니다.

* **공용 IP 주소**: Azure에서 할당.
* **개인 IP 주소**: Azure에서 할당.
* **기본 BGP IP 주소**: Azure에서 할당.
* **사용자 지정 BGP IP 주소**: 이 필드는 APIPA(자동 개인 IP 주소 지정)용으로 예약되어 있습니다. Azure는 169.254.21.* 및 169.254.22.* 범위에서 BGP IP를 지원합니다. Azure는 이러한 범위에서 BGP 연결을 허용하지만 기본 BGP IP를 사용하여 연결을 다이얼링합니다.

   :::image type="content" source="media/virtual-wan-site-to-site-portal/view-configuration-2.png" alt-text="스크린샷은 편집 단추가 강조 표시된 VPN Gateway 편집 페이지를 보여줍니다." lightbox="media/virtual-wan-site-to-site-portal/view-configuration-2-expand.png":::

## <a name="clean-up-resources"></a><a name="cleanup"></a>리소스 정리

만든 리소스가 더 이상 필요하지 않은 경우 해당 리소스를 삭제합니다. 일부 Virtual WAN 리소스는 종속성으로 인해 특정 순서로 삭제해야 합니다. 삭제를 완료하는 데 약 30분이 걸릴 수 있습니다.

[!INCLUDE [Delete resources](../../includes/virtual-wan-resource-cleanup.md)]

## <a name="next-steps"></a>다음 단계

다음으로, Virtual WAN에 대한 자세한 내용은 다음을 참조하세요.

> [!div class="nextstepaction"]
> * [가상 WAN FAQ](virtual-wan-faq.md)
