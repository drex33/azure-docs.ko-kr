---
title: Azure Firewall DNS 설정
description: DNS 서버 및 DNS 프록시 설정을 사용해 Azure Firewall을 구성할 수 있습니다.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: how-to
ms.date: 11/08/2021
ms.author: victorh
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 44a9da990b1f44f151ab6492cbca65d1b445ae65
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/09/2021
ms.locfileid: "132059548"
---
# <a name="azure-firewall-dns-settings"></a>Azure Firewall DNS 설정

사용자 지정 DNS 서버를 구성하고 Azure Firewall에 DNS 프록시를 사용하도록 설정할 수 있습니다. 이러한 설정은 **DNS 설정** 페이지에서 방화벽을 배포할 때 또는 나중에 구성합니다. 기본적으로 Azure Firewall은 Azure DNS를 사용하며 DNS 프록시는 사용하지 않도록 설정됩니다.

## <a name="dns-servers"></a>DNS 서버

DNS 서버는 도메인 이름을 IP 주소로 유지 관리하고 확인합니다. 기본 설정으로 Azure Firewall에서 이름 확인에 Azure DNS를 사용합니다. **DNS 서버** 설정을 사용하여 Azure Firewall 이름 확인을 위해 자체 DNS 서버를 구성할 수 있습니다. 단일 서버 또는 여러 서버를 구성할 수 있습니다. 여러 DNS 서버를 구성 하는 경우 사용 되는 서버는 임의로 선택 됩니다.

> [!NOTE]
> Azure Firewall Manager를 사용하여 관리되는 Azure Firewall 인스턴스의 경우 연결된 Azure Firewall 정책에서 DNS 설정이 구성됩니다.

### <a name="configure-custom-dns-servers---azure-portal"></a>사용자 지정 DNS 서버 구성 - Azure Portal

1. Azure Firewall **설정** 에서 **DNS 설정** 을 선택합니다.
2. **DNS 서버** 에서 이전에 가상 네트워크에 지정된 기존 DNS 서버를 입력하거나 추가할 수 있습니다.
3. **적용** 을 선택합니다.

이제 방화벽에서 이름 확인을 위해 DNS 트래픽을 지정된 DNS 서버로 보냅니다.

:::image type="content" source="media/dns-settings/dns-servers.png" alt-text="DNS 서버에 대한 설정을 보여주는 스크린샷.":::

### <a name="configure-custom-dns-servers---azure-cli"></a>사용자 지정 DNS 서버 구성 - Azure CLI

다음 예제에서는 Azure CLI를 사용하여 사용자 지정 DNS 서버에서 Azure Firewall을 업데이트합니다.

```azurecli-interactive
az network firewall update \
    --name fwName \ 
    --resource-group fwRG \
    --dns-servers 10.1.0.4 10.1.0.5
```

> [!IMPORTANT]
> `az network firewall` 명령의 경우 Azure CLI 확장 `azure-firewall`을 설치해야 합니다. `az extension add --name azure-firewall` 명령을 사용하여 설치할 수 있습니다. 

### <a name="configure-custom-dns-servers---azure-powershell"></a>사용자 지정 DNS 서버 구성 - Azure PowerShell

다음 예제에서는 Azure PowerShell를 사용하여 사용자 지정 DNS 서버에서 Azure Firewall을 업데이트합니다.

```azurepowershell
$dnsServers = @("10.1.0.4", "10.1.0.5")
$azFw = Get-AzFirewall -Name "fwName" -ResourceGroupName "fwRG"
$azFw.DNSServer = $dnsServers

$azFw | Set-AzFirewall
```

## <a name="dns-proxy"></a>DNS 프록시

DNS 프록시 역할을 하도록 Azure Firewall을 구성할 수 있습니다. DNS 프록시는 클라이언트 가상 머신에서 DNS 서버로의 DNS 요청에 대한 중개자입니다.

네트워크 규칙에서 FQDN(정규화된 도메인 이름) 필터링을 사용하도록 설정하려면 DNS 프록시를 사용하도록 설정하고 방화벽을 DNS 프록시로 사용하도록 가상 머신 구성을 업데이트합니다.

:::image type="content" source="media/dns-settings/dns-proxy-2.png" alt-text="사용자 지정 DNS 서버를 사용하는 DNS 프록시 구성.":::

네트워크 규칙에서 FQDN 필터링을 사용하도록 설정하고 방화벽을 DNS 프록시로 사용하도록 클라이언트 가상 머신을 구성하지 않으면 이러한 클라이언트의 DNS 요청이 다른 시간에 DNS 서버로 이동하거나 방화벽의 응답과는 다른 응답을 반환할 수 있습니다. DNS 프록시는 불일치를 방지하기 위해 Azure Firewall을 클라이언트 요청 경로에 배치합니다.


Azure Firewall이 DNS 프록시인 경우 두 가지 캐싱 함수 형식이 가능합니다.

- **긍정 캐시**: DNS 확인에 성공했습니다. 방화벽은 응답의 TTL (time to live)에 따라 최대 1 시간까지 이러한 응답을 캐시 합니다. 

- **부정 캐시**: DNS 확인 시 응답이 없거나 확인되지 않습니다. 방화벽은 응답의 TTL에 따라 최대 30 분까지 이러한 응답을 캐시 합니다.

DNS 프록시는 네트워크 규칙의 FQDN에서 모든 확인된 IP 주소를 저장합니다. IP 주소 하나를 확인하는 FQDN을 사용하는 것이 가장 좋습니다.

연결에 실패 한 경우 DNS 프록시는 Azure DNS을 포함 하 여 다른 DNS 서버에 대 한 다시 시도 또는 장애 조치 (failover)를 수행 하지 않습니다.

### <a name="policy-inheritance"></a>정책 상속

 독립 실행형 방화벽에 적용된 정책 DNS 설정은 독립 실행형 방화벽 DNS 설정을 재정의합니다. 자식 정책은 모든 부모 정책 DNS 설정을 상속하지만 부모 정책을 재정의할 수 있습니다.

예를 들어 네트워크 규칙에서 FQDN을 사용하려면 DNS 프록시를 사용하도록 설정해야 합니다. 그러나 상위 정책에 DNS 프록시가 사용하도록 설정되어 있지 **않으면** 하위 정책은 이 설정을 로컬로 재정의하지 않는 한 네트워크 규칙에서 FQDN을 지원하지 않습니다.

### <a name="dns-proxy-configuration"></a>DNS 프록시 구성

DNS 프록시 구성에는 다음 세 단계가 필요합니다.
1. Azure Firewall DNS 설정에서 DNS 프록시를 사용합니다.
2. 필요에 따라 사용자 지정 DNS 서버를 구성하거나 제공된 기본값을 사용합니다.
3. 가상 네트워크 DNS 서버 설정에서 Azure Firewall의 개인 IP 주소를 사용자 지정 DNS 주소로 구성합니다. 이 설정을 통해 DNS 트래픽이 Azure Firewall로 전송됩니다.

#### <a name="configure-dns-proxy---azure-portal"></a>DNS 프록시 구성 - Azure Portal

DNS 프록시를 구성하려면 방화벽 개인 IP 주소를 사용하도록 가상 네트워크 DNS 서버 설정을 구성해야 합니다. 그런 다음 Azure Firewall **DNS 설정** 에서 DNS 프록시를 사용합니다.

##### <a name="configure-virtual-network-dns-servers"></a>가상 네트워크 DNS 서버 구성 

1. Azure Firewall 인스턴스를 통해 DNS 트래픽이 라우팅되는 가상 네트워크를 선택합니다.
2. **설정** 아래에서 **DNS 서버** 를 선택합니다.
3. **DNS 서버** 아래에서 **사용자 지정** 을 선택합니다.
4. 방화벽의 개인 IP 주소를 입력합니다.
5. **저장** 을 선택합니다.
6. 가상 네트워크에 연결된 VM을 다시 시작하여 새 DNS 서버 설정을 할당합니다. VM이 다시 시작될 때까지 현재 DNS 설정을 계속 사용합니다.

##### <a name="enable-dns-proxy"></a>DNS 프록시 사용

1. Azure Firewall 인스턴스를 선택합니다.
2. **설정** 아래에서 **DNS 설정** 을 선택합니다.
3. 기본적으로 **DNS 프록시** 는 사용하지 않는 것으로 설정됩니다. 사용하도록 설정되면 방화벽은 포트 53에서 수신하고 DNS 요청을 구성된 DNS 서버로 전달합니다.
4. **DNS 서버** 구성을 검토하여 해당 설정이 사용자 환경에 적합한지 확인합니다.
5. **저장** 을 선택합니다.

:::image type="content" source="media/dns-settings/dns-proxy.png" alt-text="DNS 프록시에 대한 설정을 보여주는 스크린샷.":::

#### <a name="configure-dns-proxy---azure-cli"></a>DNS 프록시 구성 - Azure CLI

Azure CLI를 사용하여 Azure Firewall에서 DNS 프록시 설정을 구성할 수 있습니다. 또한 Azure Firewall을 DNS 서버로 사용하도록 가상 네트워크를 업데이트 하는 데 사용할 수 있습니다.

##### <a name="configure-virtual-network-dns-servers"></a>가상 네트워크 DNS 서버 구성

다음 예제에서는 Azure Firewall을 DNS 서버로 사용하도록 가상 네트워크를 구성합니다.
 
```azurecli-interactive
az network vnet update \
    --name VNetName \ 
    --resource-group VNetRG \
    --dns-servers <firewall-private-IP>
```

##### <a name="enable-dns-proxy"></a>DNS 프록시 사용

다음 예제에서는 Azure Firewall에서 DNS 프록시 기능을 사용하도록 설정합니다.

```azurecli-interactive
az network firewall update \
    --name fwName \ 
    --resource-group fwRG \
    --enable-dns-proxy true
```

#### <a name="configure-dns-proxy---azure-powershell"></a>DNS 프록시 구성 - Azure PowerShell

Azure PowerShell을 사용하여 Azure Firewall에서 DNS 프록시 설정을 구성할 수 있습니다. 또한 Azure Firewall을 DNS 서버로 사용하도록 가상 네트워크를 업데이트 하는 데 사용할 수 있습니다.

##### <a name="configure-virtual-network-dns-servers"></a>가상 네트워크 DNS 서버 구성

다음 예제에서는 Azure Firewall을 DNS 서버로 사용하도록 가상 네트워크를 구성합니다.

```azurepowershell
$dnsServers = @("<firewall-private-IP>")
$VNet = Get-AzVirtualNetwork -Name "VNetName" -ResourceGroupName "VNetRG"
$VNet.DhcpOptions.DnsServers = $dnsServers

$VNet | Set-AzVirtualNetwork
```

##### <a name="enable-dns-proxy"></a>DNS 프록시 사용

다음 예제에서는 Azure Firewall에서 DNS 프록시 기능을 사용하도록 설정합니다.

```azurepowershell
$azFw = Get-AzFirewall -Name "fwName" -ResourceGroupName "fwRG"
$azFw.DNSEnableProxy = $true

$azFw | Set-AzFirewall
```

## <a name="next-steps"></a>다음 단계

- [Azure Firewall DNS 프록시 세부 정보](dns-details.md)
- [네트워크 규칙의 FQDN 필터링](fqdn-filtering-network-rules.md)
