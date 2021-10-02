---
title: Azure 가상 네트워크에서 기본 Load Balancer를 사용하여 IPv6 이중 스택 애플리케이션 배포 - Resource Manager 템플릿
titlesuffix: Azure Virtual Network
description: 이 문서에서는 Azure Resource Manager VM 템플릿을 사용하여 Azure 가상 네트워크에 IPv6 이중 스택 애플리케이션을 배포하는 방법을 알려줍니다.
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: NA
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 03/31/2020
ms.author: kumud
ms.openlocfilehash: 548b416ed0f21df83dafdb1c2d7015c625c36e4c
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/01/2021
ms.locfileid: "129373040"
---
# <a name="deploy-an-ipv6-dual-stack-application-with-basic-load-balancer-in-azure---template"></a>Azure에서 기본 Load Balancer를 사용하여 IPv6 이중 스택 애플리케이션 배포 - 템플릿

이 문서에서는 다음에 적용되는 Azure Resource Manager VM 템플릿의 일부가 포함된 IPv6 구성 작업 목록을 제공합니다. 이 문서에서 설명한 템플릿을 사용하여 IPv4와 IPv6 서브넷을 사용한 이중 스택을 포함한 기본 Load Balancer를 사용하는 이중 스택(IPv4 + IPv6) 애플리케이션, 이중(IPv4 + IPv6) 프런트 엔드 구성을 갖춘 기본 Load Balancer, 이중 IP 구성을 갖춘 NIC, 네트워크 보안 그룹, 그리고 공용 IP를 배포합니다.

표준 Load Balancer를 사용하여 이중 스택(IPV4 + IPv6) 애플리케이션을 배포하려면 [표준 Load Balancer를 사용하여 Ipv6 이중 스택 애플리케이션 배포 - 템플릿](ipv6-configure-standard-load-balancer-template-json.md)을 참조하세요.

## <a name="required-configurations"></a>필수 구성

템플릿에서 템플릿 섹션을 검색하여 어디에서 발생해야 하는지 확인합니다.

### <a name="ipv6-addressspace-for-the-virtual-network"></a>가상 네트워크의 IPv6 addressSpace

추가할 템플릿 섹션:

```JSON
        "addressSpace": {
          "addressPrefixes": [
            "[variables('vnetv4AddressRange')]",
            "[variables('vnetv6AddressRange')]"    
```

### <a name="ipv6-subnet-within-the-ipv6-virtual-network-addressspace"></a>IPv6 가상 네트워크 addressSpace 내의 IPv6 서브넷

추가할 템플릿 섹션:
```JSON
          {
            "name": "V6Subnet",
            "properties": {
              "addressPrefix": "[variables('subnetv6AddressRange')]"
            }

```

### <a name="ipv6-configuration-for-the-nic"></a>NIC에 대한 IPv6 구성

추가할 템플릿 섹션:
```JSON
          {
            "name": "ipconfig-v6",
            "properties": {
              "privateIPAllocationMethod": "Dynamic",
          "privateIPAddressVersion":"IPv6",
              "subnet": {
                "id": "[variables('v6-subnet-id')]"
              },
              "loadBalancerBackendAddressPools": [
                {
                  "id": "[concat(resourceId('Microsoft.Network/loadBalancers','loadBalancer'),'/backendAddressPools/LBBAP-v6')]"
                }
```

### <a name="ipv6-network-security-group-nsg-rules"></a>IPv6 NSG(네트워크 보안 그룹) 규칙

```JSON
          {
            "name": "default-allow-rdp",
            "properties": {
              "description": "Allow RDP",
              "protocol": "Tcp",
              "sourcePortRange": "33819-33829",
              "destinationPortRange": "5000-6000",
              "sourceAddressPrefix": "fd00:db8:deca:deed::/64",
              "destinationAddressPrefix": "fd00:db8:deca:deed::/64",
              "access": "Allow",
              "priority": 1003,
              "direction": "Inbound"
            }
```

## <a name="conditional-configuration"></a>조건부 구성

네트워크 가상 어플라이언스를 사용하는 경우 경로 테이블에 IPv6 경로를 추가합니다. 그렇지 않다면 아 구성은 선택 사항입니다.

```JSON
    {
      "type": "Microsoft.Network/routeTables",
      "name": "v6route",
      "apiVersion": "[variables('ApiVersion')]",
      "location": "[resourceGroup().location]",
      "properties": {
        "routes": [
          {
            "name": "v6route",
            "properties": {
              "addressPrefix": "fd00:db8:deca:deed::/64",
              "nextHopType": "VirtualAppliance",
              "nextHopIpAddress": "fd00:db8:ace:f00d::1"
            }
```

## <a name="optional-configuration"></a>선택적 구성

### <a name="ipv6-internet-access-for-the-virtual-network"></a>가상 네트워크에 대한 IPv6 인터넷 액세스

```JSON
{
            "name": "LBFE-v6",
            "properties": {
              "publicIPAddress": {
                "id": "[resourceId('Microsoft.Network/publicIPAddresses','lbpublicip-v6')]"
              }
```

### <a name="ipv6-public-ip-addresses"></a>IPv6 공용 IP 주소

```JSON
    {
      "apiVersion": "[variables('ApiVersion')]",
      "type": "Microsoft.Network/publicIPAddresses",
      "name": "lbpublicip-v6",
      "location": "[resourceGroup().location]",
      "properties": {
        "publicIPAllocationMethod": "Dynamic",
        "publicIPAddressVersion": "IPv6"
      }
```

### <a name="ipv6-front-end-for-load-balancer"></a>부하 분산 장치에 대한 IPv6 프런트 엔드

```JSON
          {
            "name": "LBFE-v6",
            "properties": {
              "publicIPAddress": {
                "id": "[resourceId('Microsoft.Network/publicIPAddresses','lbpublicip-v6')]"
              }
```

### <a name="ipv6-back-end-address-pool-for-load-balancer"></a>부하 분산 장치에 대한 IPv6 백 엔드 주소 풀

```JSON
              "backendAddressPool": {
                "id": "[concat(resourceId('Microsoft.Network/loadBalancers', 'loadBalancer'), '/backendAddressPools/LBBAP-v6')]"
              },
              "protocol": "Tcp",
              "frontendPort": 8080,
              "backendPort": 8080
            },
            "name": "lbrule-v6"
```

### <a name="ipv6-load-balancer-rules-to-associate-incoming-and-outgoing-ports"></a>발신 및 수신 포트를 연결하는 IPv6 부하 분산 장치 규칙

```JSON
          {
            "name": "ipconfig-v6",
            "properties": {
              "privateIPAllocationMethod": "Dynamic",
          "privateIPAddressVersion":"IPv6",
              "subnet": {
                "id": "[variables('v6-subnet-id')]"
              },
              "loadBalancerBackendAddressPools": [
                {
                  "id": "[concat(resourceId('Microsoft.Network/loadBalancers','loadBalancer'),'/backendAddressPools/LBBAP-v6')]"
                }
```

## <a name="sample-vm-template-json"></a>샘플 VM 템플릿 JSON
Azure Resource Manager 템플릿을 사용하여 Azure 가상 네트워크에서 기본 Load Balancer로 IPv6 이중 스택 애플리케이션을 배포하려면 [여기](https://azure.microsoft.com/resources/templates/ipv6-in-vnet/)에서 샘플 템플릿을 참조하세요.

## <a name="next-steps"></a>다음 단계

[공용 IP 주소](https://azure.microsoft.com/pricing/details/ip-addresses/), [네트워크 대역폭](https://azure.microsoft.com/pricing/details/bandwidth/) 또는 [Load Balancer](https://azure.microsoft.com/pricing/details/load-balancer/) 가격 책정에 대한 세부 정보를 확인할 수 있습니다.
