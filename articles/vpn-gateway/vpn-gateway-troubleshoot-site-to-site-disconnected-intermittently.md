---
title: 'Azure 사이트 간 VPN 일시적 연결 끊김 문제 해결:'
description: 사이트 간 VPN 연결이 자주 끊어지는 문제를 해결하는 방법을 알아봅니다.
services: vpn-gateway
titleSuffix: Azure VPN Gateway
author: chadmath
ms.service: vpn-gateway
ms.topic: troubleshooting
ms.date: 03/22/2021
ms.author: genli
ms.openlocfilehash: 47a6649c154ed3fed5ba444a58bae93ac0398bbc
ms.sourcegitcommit: 16580bb4fbd8f68d14db0387a3eee1de85144367
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/24/2021
ms.locfileid: "112676837"
---
# <a name="troubleshooting-azure-site-to-site-vpn-disconnects-intermittently"></a>문제 해결: Azure 사이트 간 VPN 일시적 연결 끊김

새로운 또는 기존 Microsoft Azure 사이트 간 VPN 연결이 안정적이지 않거나 연결이 자주 끊어지는 문제가 발생할 수 있습니다. 이 문서에서는 이 문제의 원인을 식별하여 문제를 해결하는 데 도움이 되는 문제 해결 단계를 제공합니다. 

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="troubleshooting-steps"></a>문제 해결 단계

### <a name="prerequisite-step"></a>필수 조건 단계

Azure Virtual Network Gateway의 형식을 확인합니다.

1. [Azure 포털](https://portal.azure.com)로 이동합니다.
2. Virtual Network Gateway의 **개요** 페이지에서 형식 정보를 확인합니다.
    
    ![게이트웨이 개요](media/vpn-gateway-troubleshoot-site-to-site-disconnected-intermittently/gatewayoverview.png)

### <a name="step-1-check-whether-the-on-premises-vpn-device-is-validated"></a>1단계 온-프레미스 VPN 디바이스가 확인되었는지 확인

1. [확인된 VPN 디바이스 및 운영 체제 버전](vpn-gateway-about-vpn-devices.md#devicetable)을 사용 중인지 확인합니다. VPN 디바이스가 확인되지 않은 경우 디바이스 제조업체에 호환성 문제가 있는지 문의해야 할 수 있습니다.
2. VPN 디바이스가 올바르게 구성되었는지 확인합니다. 자세한 내용은 [디바이스 구성 샘플 편집](vpn-gateway-about-vpn-devices.md#editing)을 참조하세요.

### <a name="step-2-check-the-security-association-settingsfor-policy-based-azure-virtual-network-gateways"></a>2단계 보안 연결 설정 확인(정책 기반 Azure Virtual Network Gateway의 경우)

1. Microsoft Azure의 **로컬 네트워크 게이트웨이** 정의에 있는 가상 네트워크, 서브넷 및 범위가 온-프레미스 VPN 디바이스의 구성과 같은지 확인합니다.
2. 보안 연결 설정이 일치하는지 확인합니다.

### <a name="step-3-check-for-user-defined-routes-or-network-security-groups-on-gateway-subnet"></a>3단계 게이트웨이 서브넷에 대한 사용자 정의 경로 또는 네트워크 보안 그룹 확인

게이트웨이 서브넷의 사용자 정의 경로가 일부 트래픽은 제한하고 다른 트래픽은 허용하고 있을 수 있습니다. 이 경우 VPN 연결이 일부 트래픽에 대해서는 불안정하고 다른 트래픽에 대해서는 양호한 것으로 나타날 수 있습니다. 

### <a name="step-4-check-the-one-vpn-tunnel-per-subnet-pair-setting-for-policy-based-virtual-network-gateways"></a>4단계 “서브넷 쌍당 하나의 VPN 터널” 설정 확인(정책 기반 가상 네트워크 게이트웨이의 경우)

온-프레미스 VPN 디바이스가 정책 기반 가상 네트워크 게이트웨이에 대해 **서브넷 쌍당 하나의 VPN 터널** 을 가지도록 설정되어 있는지 확인합니다.

### <a name="step-5-check-for-security-association-limitation-for-policy-based-virtual-network-gateways"></a>5단계 보안 연결 제한 확인(정책 기반 가상 네트워크 게이트웨이의 경우)

정책 기반 가상 네트워크 게이트웨이에는 200개의 서브넷 보안 연결 쌍 제한이 있습니다. Azure Virtual Network 서브넷 수를 로컬 서브넷 수로 곱한 값이 200보다 큰 경우 이따금 서브넷 연결이 끊어지는 현상이 발생합니다.

### <a name="step-6-check-on-premises-vpn-device-external-interface-address"></a>6단계 온-프레미스 VPN 디바이스 외부 인터페이스 주소 확인

VPN 디바이스의 인터넷 연결 IP 주소가 Azure의 **로컬 네트워크 게이트웨이 주소 공간** 정의에 포함된 경우 간헐적으로 연결 끊김이 발생할 수 있습니다.

### <a name="step-7-check-whether-the-on-premises-vpn-device-has-perfect-forward-secrecy-enabled"></a>7단계 온-프레미스 VPN 디바이스에 PFS(Perfect Forward Secrecy)가 사용하도록 설정되어 있는지 확인

**PFS(Perfect Forward Secrecy)** 기능은 연결 끊김 문제를 발생시킬 수 있습니다. VPN 디바이스에 **PFS(Perfect Forward Secrecy)** 기능이 사용하도록 설정되어 있으면 이 기능을 사용하지 않도록 설정합니다. 그런 다음 [가상 네트워크 게이트웨이 IPsec 정책을 업데이트](vpn-gateway-ipsecikepolicy-rm-powershell.md#managepolicy)합니다.

## <a name="next-steps"></a>다음 단계

- [가상 네트워크에 대한 사이트 간 연결 구성](./tutorial-site-to-site-portal.md)
- [사이트 간 VPN 연결에 대한 IPsec/IKE 정책 구성](vpn-gateway-ipsecikepolicy-rm-powershell.md)
