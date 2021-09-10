---
title: Azure 사이트 간 VPN 연결에서 연결할 수 없는 문제 해결
titleSuffix: Azure VPN Gateway
description: 갑자기 작동 중단되어 다시 연결할 수 없는 사이트 간 VPN 연결 문제를 해결하는 방법을 알아봅니다.
services: vpn-gateway
author: chadmath
ms.service: vpn-gateway
ms.topic: troubleshooting
ms.date: 03/22/2021
ms.author: genli
ms.openlocfilehash: 878c63b090510124fe03848d4f5aa1b3243f78d5
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122535783"
---
# <a name="troubleshooting-an-azure-site-to-site-vpn-connection-cannot-connect-and-stops-working"></a>문제 해결: Azure 사이트 간 VPN 연결에서 연결할 수 없고 작동이 중지됨

온-프레미스 네트워크와 Azure Virtual Network 사이에 사이트 간 VPN 연결을 구성한 후 VPN 연결이 갑자기 작동을 중지하며, 이를 다시 연결할 수 없는 경우가 있습니다. 이 문서에서는 이 문제를 해결하는 데 도움이 되는 문제 해결 단계를 제공합니다. 

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="troubleshooting-steps"></a>문제 해결 단계

이 문제를 해결하려면 먼저 [Azure VPN 게이트웨이를 다시 설정](./reset-gateway.md)하고 온-프레미스 VPN 디바이스에서 터널을 다시 설정해 봅니다. 문제가 계속되면 다음 단계에 따라 문제의 원인을 식별합니다.

### <a name="prerequisite-step"></a>필수 조건 단계

Azure VPN 게이트웨이 유형을 확인합니다.

1. [Azure Portal](https://portal.azure.com)로 이동합니다.

2. VPN 게이트웨이에 대한 **개요** 페이지에서 유형 정보를 확인합니다.
    
    ![게이트웨이 개요](media/vpn-gateway-troubleshoot-site-to-site-cannot-connect/gatewayoverview.png)

### <a name="step-1-check-whether-the-on-premises-vpn-device-is-validated"></a>1단계. 온-프레미스 VPN 디바이스가 확인되었는지 확인

1. [확인된 VPN 디바이스 및 운영 체제 버전](vpn-gateway-about-vpn-devices.md#devicetable)을 사용 중인지 확인합니다. 확인된 VPN 디바이스가 아닌 경우 디바이스 제조업체에 호환성 문제가 있는지 문의해야 할 수 있습니다.

2. VPN 디바이스가 올바르게 구성되었는지 확인합니다. 자세한 내용은 [디바이스 구성 예제 편집](vpn-gateway-about-vpn-devices.md#editing)을 참조하세요.

### <a name="step-2-verify-the-shared-key"></a>2단계. 공유 키 확인

온-프레미스 VPN 디바이스와 Azure Virtual Network VPN의 공유 키를 비교하여 키가 일치하는지 확인합니다. 

Azure VPN 연결에 대한 공유 키를 보려면 다음 방법 중 하나를 사용합니다.

**Azure Portal**

1. 만든 VPN 게이트웨이 사이트 간 연결로 이동합니다.

2. **설정** 섹션에서 **공유 키** 를 클릭합니다.
    
    ![공유 키](media/vpn-gateway-troubleshoot-site-to-site-cannot-connect/sharedkey.png)

**Azure PowerShell**

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Azure [Resource Manager](../azure-resource-manager/management/deployment-models.md) 배포 모델의 경우:

```azurepowershell
Get-AzVirtualNetworkGatewayConnectionSharedKey -Name <Connection name> -ResourceGroupName <Resource group name>
```

클래식 배포 모델:

```azurepowershell
Get-AzureVNetGatewayKey -VNetName -LocalNetworkSiteName
```

### <a name="step-3-verify-the-vpn-peer-ips"></a>3단계: VPN 피어 IP 확인

-   Azure의 **로컬 네트워크 게이트웨이** 개체에 있는 IP 정의가 온-프레미스 디바이스 IP와 일치해야 합니다.
-   온-프레미스 디바이스에 설정된 Azure 게이트웨이 IP 정의는 Azure 게이트웨이 IP와 일치해야 합니다.

### <a name="step-4-check-udr-and-nsgs-on-the-gateway-subnet"></a>4단계. 게이트웨이 서브넷에서 UDR 및 NSG 확인

게이트웨이 서브넷에서 UDR(사용자 정의 라우팅) 또는 NSG(네트워크 보안 그룹)를 확인하고 제거한 다음 결과를 테스트합니다. 문제가 해결되면 적용된 UDR 또는 NSG의 설정을 확인합니다.

### <a name="step-5-check-the-on-premises-vpn-device-external-interface-address"></a>5단계. 온-프레미스 VPN 디바이스 외부 인터페이스 주소 확인

VPN 디바이스의 인터넷 연결 IP 주소가 Azure의 **로컬 네트워크** 정의에 포함된 경우 이따금 연결 끊김이 발생할 수 있습니다.

### <a name="step-6-verify-that-the-subnets-match-exactly-azure-policy-based-gateways"></a>6단계. 서브넷이 정확하게 일치하는지 확인(Azure 정책 기반 게이트웨이)

-   Azure Virtual Network와 온-프레미스 정의 간에 가상 네트워크 주소 공간이 정확하게 일치하는지 확인합니다.
-   **로컬 네트워크 게이트웨이** 와 온-프레미스 네트워크에 대한 온-프레미스 정의 간에 서브넷이 정확하게 일치하는지 확인합니다.

### <a name="step-7-verify-the-azure-gateway-health-probe"></a>7단계. Azure 게이트웨이 상태 프로브 확인

1. 다음 URL로 이동하여 상태 프로브를 엽니다.

    `https://<YourVirtualNetworkGatewayIP>:8081/healthprobe`

2. 인증서 경고를 클릭합니다.
3. 응답이 수신되면 VPN 게이트웨이가 정상으로 간주됩니다. 응답을 수신하지 못하면 게이트웨이가 정상이 아니거나 게이트웨이 서브넷에 문제를 일으키는 NSG가 있는 것입니다. 다음 텍스트는 샘플 응답입니다.

    ```xml
    <?xml version="1.0"?>
    <string xmlns="http://schemas.microsoft.com/2003/10/Serialization/">Primary Instance: GatewayTenantWorker_IN_1 GatewayTenantVersion: 14.7.24.6</string>
    ```

### <a name="step-8-check-whether-the-on-premises-vpn-device-has-the-perfect-forward-secrecy-feature-enabled"></a>8단계: 온-프레미스 VPN 디바이스에 PFS(Perfect Forward Secrecy) 기능이 사용하도록 설정되어 있는지 확인

PFS(Perfect Forward Secrecy) 기능은 연결 끊김 문제를 일으킬 수 있습니다. VPN 디바이스에 PFS(Perfect Forward Secrecy) 기능이 사용하도록 설정되어 있으면 이 기능을 사용하지 않도록 설정합니다. 그런 다음 VPN 게이트웨이 IPsec 정책을 업데이트합니다.

## <a name="next-steps"></a>다음 단계

-   [가상 네트워크에 대한 사이트 간 연결 구성](./tutorial-site-to-site-portal.md)
-   [사이트 간 VPN 연결에 대한 IPsec/IKE 정책 구성](vpn-gateway-ipsecikepolicy-rm-powershell.md)