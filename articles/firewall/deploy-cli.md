---
title: Azure CLI를 사용하여 Azure Firewall을 배포하고 구성
description: 이 문서에서는 Azure CLI를 사용하여 Azure Firewall을 배포하고 구성하는 방법을 알아봅니다.
services: firewall
author: vhorne
ms.service: firewall
ms.date: 08/29/2019
ms.author: victorh
ms.topic: how-to
ms.openlocfilehash: 6c637fd36d1c85002cbd8216b76a75879e16a99e
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/13/2021
ms.locfileid: "110066559"
---
# <a name="deploy-and-configure-azure-firewall-using-azure-cli"></a>Azure CLI를 사용하여 Azure Firewall을 배포하고 구성

아웃바운드 네트워크 액세스 제어는 전체 네트워크 보안 계획에서 중요한 부분입니다. 예를 들어 웹 사이트에 대한 액세스를 제한할 수 있습니다. 또는 액세스 가능한 아웃바운드 IP 주소 및 포트를 제한할 수 있습니다.

Azure 서브넷에서 아웃바운드 네트워크로의 액세스를 제어하는 한 가지 방법은 Azure Firewall입니다. Azure Firewall을 사용하면 다음을 구성할 수 있습니다.

* 서브넷에서 액세스할 수 있는 FQDN(정규화된 도메인 이름)을 정의하는 애플리케이션 규칙. FQDN은 [SQL 인스턴스를 포함](sql-fqdn-filtering.md)할 수도 있습니다.
* 원본 주소, 프로토콜, 대상 포트 및 대상 주소를 정의하는 네트워크 규칙.

네트워크 트래픽은 서브넷 기본 게이트웨이처럼 방화벽에 네트워크 트래픽을 라우팅할 경우 구성된 방화벽 규칙에 종속됩니다.

이 문서에서는 배포가 용이하도록 세 개의 서브넷을 사용하여 간소화된 단일 VNet을 만듭니다. 프로덕션 배포의 경우 [허브 및 스포크 모델](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke)이 추천됩니다. 방화벽은 자체 VNet에 있습니다. 워크로드 서버는 하나 이상의 서브넷이 있는 동일한 지역에서 피어링된 VNet에 있습니다.

* **AzureFirewallSubnet** - 방화벽은 이 서브넷에 있습니다.
* **워크로드-SN** - 워크로드 서버는 이 서브넷에 있습니다. 이 서브넷의 네트워크 트래픽은 방화벽을 통해 이동합니다.
* **점프-SN** - The "점프" 서버는 이 서브넷에 있습니다. 점프 서버에는 원격 데스크톱을 사용하여 연결할 수 있는 공용 IP 주소가 있습니다. 여기에서 다음 워크로드 서버에 연결할 수 있습니다(다른 원격 데스크톱을 사용하여).

![자습서 네트워크 인프라](media/tutorial-firewall-rules-portal/Tutorial_network.png)

이 문서에서는 다음 방법을 설명합니다.

* 테스트 네트워크 환경 설정
* 방화벽 배포
* 기본 경로 만들기
* [www.google.com]\(www.google.com) 액세스를 허용하도록 애플리케이션 규칙 구성
* 외부 DNS 서버 액세스를 허용하도록 네트워크 규칙 구성
* 방화벽 테스트

원하는 경우 [Azure Portal](tutorial-firewall-deploy-portal.md) 또는 [Azure PowerShell](deploy-ps.md)을 사용하여 이 절차를 완료할 수 있습니다.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- 이 문서에는 Azure CLI 버전 2.0.4 이상이 필요합니다. Azure Cloud Shell을 사용하는 경우 최신 버전이 이미 설치되어 있습니다.

## <a name="set-up-the-network"></a>네트워크 설정

먼저 방화벽 배포에 필요한 리소스를 포함하는 리소스 그룹을 만듭니다. 그런 다음, VNet, 서브넷 및 테스트 서버를 만듭니다.

### <a name="create-a-resource-group"></a>리소스 그룹 만들기

리소스 그룹에는 배포의 모든 리소스가 포함되어 있습니다.

```azurecli-interactive
az group create --name Test-FW-RG --location eastus
```

### <a name="create-a-vnet"></a>VNet 만들기

이 가상 네트워크에는 세 개의 서브넷이 있습니다.

> [!NOTE]
> AzureFirewallSubnet 서브넷의 크기는 /26입니다. 서브넷 크기에 대한 자세한 내용은 [Azure Firewall FAQ](firewall-faq.yml#why-does-azure-firewall-need-a--26-subnet-size)를 참조하세요.

```azurecli-interactive
az network vnet create \
  --name Test-FW-VN \
  --resource-group Test-FW-RG \
  --location eastus \
  --address-prefix 10.0.0.0/16 \
  --subnet-name AzureFirewallSubnet \
  --subnet-prefix 10.0.1.0/26
az network vnet subnet create \
  --name Workload-SN \
  --resource-group Test-FW-RG \
  --vnet-name Test-FW-VN   \
  --address-prefix 10.0.2.0/24
az network vnet subnet create \
  --name Jump-SN \
  --resource-group Test-FW-RG \
  --vnet-name Test-FW-VN   \
  --address-prefix 10.0.3.0/24
```

### <a name="create-virtual-machines"></a>가상 머신 만들기

이제 점프 및 워크로드 가상 머신을 만들어 적절한 서브넷에 배치합니다.
메시지가 표시되면 가상 머신에 대한 암호를 입력합니다.

Srv-Jump 가상 머신을 만듭니다.

```azurecli-interactive
az vm create \
    --resource-group Test-FW-RG \
    --name Srv-Jump \
    --location eastus \
    --image win2016datacenter \
    --vnet-name Test-FW-VN \
    --subnet Jump-SN \
    --admin-username azureadmin
az vm open-port --port 3389 --resource-group Test-FW-RG --name Srv-Jump
```



테스트하는 데 특정 DNS 서버 IP 주소를 사용하고 공용 IP 주소는 사용하지 않는 Srv-Work용 NIC를 만듭니다.

```azurecli-interactive
az network nic create \
    -g Test-FW-RG \
    -n Srv-Work-NIC \
   --vnet-name Test-FW-VN \
   --subnet Workload-SN \
   --public-ip-address "" \
   --dns-servers 209.244.0.3 209.244.0.4
```

이제 워크로드 가상 머신을 만듭니다.
메시지가 표시되면 가상 머신에 대한 암호를 입력합니다.

```azurecli-interactive
az vm create \
    --resource-group Test-FW-RG \
    --name Srv-Work \
    --location eastus \
    --image win2016datacenter \
    --nics Srv-Work-NIC \
    --admin-username azureadmin
```

[!INCLUDE [ephemeral-ip-note.md](../../includes/ephemeral-ip-note.md)]

## <a name="deploy-the-firewall"></a>방화벽 배포

이제 가상 네트워크에 방화벽을 배포합니다.

```azurecli-interactive
az network firewall create \
    --name Test-FW01 \
    --resource-group Test-FW-RG \
    --location eastus
az network public-ip create \
    --name fw-pip \
    --resource-group Test-FW-RG \
    --location eastus \
    --allocation-method static \
    --sku standard
az network firewall ip-config create \
    --firewall-name Test-FW01 \
    --name FW-config \
    --public-ip-address fw-pip \
    --resource-group Test-FW-RG \
    --vnet-name Test-FW-VN
az network firewall update \
    --name Test-FW01 \
    --resource-group Test-FW-RG 
az network public-ip show \
    --name fw-pip \
    --resource-group Test-FW-RG
fwprivaddr="$(az network firewall ip-config list -g Test-FW-RG -f Test-FW01 --query "[?name=='FW-config'].privateIpAddress" --output tsv)"
```

개인 IP 주소를 참고합니다. 기본 경로를 만들 때 나중에 사용할 수 있습니다.

## <a name="create-a-default-route"></a>기본 경로 만들기

BGP 경로 전파를 사용하지 않도록 설정하여 테이블 만들기

```azurecli-interactive
az network route-table create \
    --name Firewall-rt-table \
    --resource-group Test-FW-RG \
    --location eastus \
    --disable-bgp-route-propagation true
```

경로를 만듭니다.

```azurecli-interactive
az network route-table route create \
  --resource-group Test-FW-RG \
  --name DG-Route \
  --route-table-name Firewall-rt-table \
  --address-prefix 0.0.0.0/0 \
  --next-hop-type VirtualAppliance \
  --next-hop-ip-address $fwprivaddr
```

경로 테이블을 서브넷에 연결합니다.

```azurecli-interactive
az network vnet subnet update \
    -n Workload-SN \
    -g Test-FW-RG \
    --vnet-name Test-FW-VN \
    --address-prefixes 10.0.2.0/24 \
    --route-table Firewall-rt-table
```

## <a name="configure-an-application-rule"></a>애플리케이션 규칙 구성

애플리케이션 규칙은 www.google.com에 대한 아웃바운드 액세스를 허용합니다.

```azurecli-interactive
az network firewall application-rule create \
   --collection-name App-Coll01 \
   --firewall-name Test-FW01 \
   --name Allow-Google \
   --protocols Http=80 Https=443 \
   --resource-group Test-FW-RG \
   --target-fqdns www.google.com \
   --source-addresses 10.0.2.0/24 \
   --priority 200 \
   --action Allow
```

Azure Firewall은 기본적으로 허용되는 인프라 FQDN에 대한 기본 제공 규칙 컬렉션을 포함합니다. 이러한 FQDN은 플랫폼에 대해 특정적이며 다른 용도로 사용할 수 없습니다. 자세한 내용은 [인프라 FQDN](infrastructure-fqdns.md)을 참조하세요.

## <a name="configure-a-network-rule"></a>네트워크 규칙 구성

네트워크 규칙은 포트 53(DNS)에서 두 IP 주소에 대한 아웃바운드 액세스를 허용합니다.

```azurecli-interactive
az network firewall network-rule create \
   --collection-name Net-Coll01 \
   --destination-addresses 209.244.0.3 209.244.0.4 \
   --destination-ports 53 \
   --firewall-name Test-FW01 \
   --name Allow-DNS \
   --protocols UDP \
   --resource-group Test-FW-RG \
   --priority 200 \
   --source-addresses 10.0.2.0/24 \
   --action Allow
```

## <a name="test-the-firewall"></a>방화벽 테스트

이제 방화벽이 예상대로 작동하는지 테스트합니다.

1. **Srv-Work** 가상 머신의 개인 IP 주소를 기록해 둡니다.

   ```azurecli-interactive
   az vm list-ip-addresses \
   -g Test-FW-RG \
   -n Srv-Work
   ```

1. 원격 데스크톱을 **Srv-Jump** 가상 머신과 연결하고 로그인합니다. 여기에서 **Srv-Work** 개인 IP 주소에 대한 원격 데스크톱 연결을 열고 로그인합니다.

3. **SRV-Work** 에서 PowerShell 창을 열고 다음 명령을 실행합니다.

   ```
   nslookup www.google.com
   nslookup www.microsoft.com
   ```

   두 명령 모두 DNS 쿼리가 방화벽을 통과하고 있음을 보여 주는 답변을 반환해야 합니다.

1. 다음 명령을 실행합니다.

   ```
   Invoke-WebRequest -Uri https://www.google.com
   Invoke-WebRequest -Uri https://www.google.com

   Invoke-WebRequest -Uri https://www.microsoft.com
   Invoke-WebRequest -Uri https://www.microsoft.com
   ```

   `www.google.com` 요청은 성공해야 하고 `www.microsoft.com` 요청은 실패해야 합니다. 이는 방화벽 규칙이 예상대로 작동하고 있음을 보여 줍니다.

이제 방화벽 규칙이 작동하는지 확인했습니다.

* 구성된 외부 DNS 서버를 사용하여 DNS 이름을 확인할 수 있습니다.
* 다른 모든 FQDN이 아닌 허용된 FQDN 하나만 찾아볼 수 있습니다.

## <a name="clean-up-resources"></a>리소스 정리

다음 자습서를 위해 방화벽 리소스를 그대로 두어도 되고, 더 이상 필요하지 않은 경우 **Test-FW-RG** 리소스 그룹을 삭제하여 모든 방화벽 관련 리소스를 삭제해도 됩니다.

```azurecli-interactive
az group delete \
  -n Test-FW-RG
```

## <a name="next-steps"></a>다음 단계

* [자습서: Azure Firewall 로그 모니터링](./firewall-diagnostics.md)