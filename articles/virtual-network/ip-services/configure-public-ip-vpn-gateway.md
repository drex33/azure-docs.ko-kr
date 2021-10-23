---
title: VPN Gateway를 사용하여 공용 IP 주소 관리
titleSuffix: Azure Virtual Network
description: 공용 IP 주소를 VPN Gateway에서 사용하는 방법과 구성을 변경하는 방법에 대해 알아봅니다.
author: asudbring
ms.author: allensu
ms.service: virtual-network
ms.subservice: ip-services
ms.topic: how-to
ms.date: 06/28/2021
ms.custom: template-how-to
ms.openlocfilehash: fa553360085a2793dba43e91cb08fde74eebf52e
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130233926"
---
# <a name="manage-a-public-ip-address-with-a-vpn-gateway"></a>VPN Gateway를 사용하여 공용 IP 주소 관리

공용 IP 주소는 표준 및 기본의 두 가지 SKU로 제공됩니다. SKU를 선택하면 IP 주소의 기능이 결정됩니다. SKU에 따라 IP 주소를 연결할 수 있는 리소스가 결정됩니다. 

VPN Gateway는 공용 인터넷을 통해 Azure 가상 네트워크와 온-프레미스 위치 간에 암호화된 트래픽을 보내는 데 사용되는 가상 네트워크 게이트웨이입니다. VPN Gateway를 사용하여 Microsoft 네트워크를 통해 Azure 가상 네트워크 간에 암호화된 트래픽을 보낼 수도 있습니다. VPN Gateway는 각 가상 네트워크당 하나만 사용할 수 있습니다. VPN Gateway는 VPN 게이트웨이의 SKU에 따라 표준 및 기본 SKU 공용 IP 주소를 지원합니다. 공용 IP 접두사는 지원되지 않습니다.

VPN Gateway는 구성하려면 공용 IP 주소가 필요합니다. 공용 IP 주소는 VPN의 외부 연결점으로 사용됩니다. 

이 문서에서는 구독에서 기존 공용 IP를 사용하여 VPN Gateway를 만드는 방법을 알아봅니다. 

## <a name="prerequisites"></a>필수 구성 요소

- 활성 구독이 있는 Azure 계정. [체험 계정 만들기](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
- 구독의 표준 SKU 공용 IP 주소 1개. IP 주소를 리소스에 연결할 수 없습니다. 표준 SKU 공용 IP 주소를 만드는 방법에 대한 자세한 내용은 [공용 IP 만들기 - Azure Portal](./create-public-ip-portal.md)을 참조하세요.
    - 이 문서에 나오는 예의 목적에 맞게 새 공용 IP 주소의 이름을 **myStandardPublicIP** 로 지정합니다.

## <a name="create-vpn-gateway-existing-public-ip"></a>공용 IP가 있는 VPN Gateway 만들기

이 섹션에서는 VPN Gateway를 만듭니다. 필수 구성 요소에서 만든 IP 주소를 VPN 게이트웨이의 공용 IP로 선택합니다.

### <a name="create-virtual-network"></a>가상 네트워크 만들기

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

2. 포털 맨 위에 있는 검색 상자에 **가상 네트워크** 를 입력합니다.

3. 검색 결과에서 **가상 네트워크** 를 선택합니다.

4. **+ 만들기** 를 선택합니다.

5. **가상 네트워크 만들기** 에서 다음 정보를 입력하거나 선택합니다.

    | 설정 | 값 |
    | ------- | ----- |
    | **프로젝트 세부 정보** |   |
    | Subscription | 구독을 선택합니다. |
    | Resource group | **새로 만들기** 를 선택합니다. </br> **myResourceGroupVPN** 을 입력합니다. </br> **확인** 을 선택합니다. |
    | **인스턴스 세부 정보** |   |
    | 속성 | **myVNet** 을 입력합니다. |
    | 지역 | **미국 서부 2** 를 선택합니다. |
    
6. **검토 + 만들기** 탭을 선택하거나, 파란색 **검토 + 만들기** 단추를 선택합니다.

7. **만들기** 를 선택합니다.

8. 포털 맨 위에 있는 검색 상자에 **가상 네트워크** 를 입력합니다.

9. 검색 결과에서 **가상 네트워크** 를 선택합니다.

10. **가상 네트워크** 에서 **myVNET** 을 선택합니다.

11. **myVNET** 의 **설정** 에서 **서브넷** 을 선택합니다.

12. **+ 게이트웨이 서브넷** 을 선택합니다.

13. **서브넷 추가** 에서 **서브넷 주소 범위** 를 **/24** 에서 **/27** 로 변경합니다.

14. **저장** 을 선택합니다.

### <a name="create-vpn-gateway"></a>VPN 게이트웨이 만들기

2. 포털 상단의 검색 상자에 **가상 네트워크 게이트웨이** 를 입력합니다.

3. 검색 결과에서 **가상 네트워크 게이트웨이** 를 선택합니다.

4. **+ 만들기** 를 선택합니다.

5. **가상 네트워크 게이트웨이 만들기** 에서 다음 정보를 입력하거나 선택합니다.

    | 설정 | 값 |
    | ------- | ----- |
    | **프로젝트 세부 정보** |   |
    | Subscription | 구독을 선택합니다. |
    | **인스턴스 세부 정보** |   |
    | 이름 | **myVPNGateway** 를 입력합니다. |
    | 지역 | **미국 서부 2** 를 선택합니다. |
    | 게이트웨이 유형 | **VPN**(기본값)을 그대로 둡니다. |
    | VPN 유형 | **경로 기반**(기본값)을 그대로 둡니다. |
    | SKU | **VpnGw1AZ** 를 선택합니다. |
    | 가상 네트워크 | **myVNet** 을 선택합니다. |
    | 서브넷 | 항목은 이전에 만든 **GatewaySubnet** 을 자동 선택합니다. |
    | **공용 IP 주소** |   |
    | 공용 IP 주소 | **기존 항목 사용** 을 선택합니다. |
    | 공용 IP 주소 선택 | **myStandardPublicIP** 또는 공용 IP 주소를 선택합니다. |
    | 활성-활성 모드 사용 | **사용 안 함**(기본값)을 그대로 둡니다. |
    | BGP 구성 | **사용 안 함**(기본값)을 그대로 둡니다. |

6. **검토 + 만들기** 탭을 선택하거나, 파란색 **검토 + 만들기** 단추를 선택합니다.

7. **만들기** 를 선택합니다.

> [!NOTE]
> 이는 VPN Gateway의 간단한 배포입니다. 고급 구성과 설정은 [자습서: Azure Portal을 사용하여 VPN Gateway 만들기 및 관리](../../vpn-gateway/tutorial-create-gateway-portal.md)를 참조하세요.
>
> Azure VPN Gateway에 대한 자세한 정보는 [VPN Gateway란?](../../vpn-gateway/vpn-gateway-about-vpngateways.md)을 참조하세요.

## <a name="change-or-remove-public-ip-address"></a>공용 IP 주소 변경 또는 제거

VPN Gateway는 만든 후 공용 IP 주소 변경을 지원하지 않습니다.

## <a name="caveats"></a>제한 사항

* 지금은 VPN Gateway에 대해 공용 IPv6 주소가 지원되지 않습니다.
 
## <a name="next-steps"></a>다음 단계

이 문서에서는 VPN Gateway을 만들고 기존 공용 IP를 사용하는 방법을 알아보았습니다. 

- Azure의 공용 IP 주소에 대한 자세한 내용은 [공용 IP 주소](./public-ip-addresses.md)를 참조하세요.
- VPN Gateway에 대한 자세한 내용은 [VPN Gateway란?](../../vpn-gateway/vpn-gateway-about-vpngateways.md)을 참조하세요.