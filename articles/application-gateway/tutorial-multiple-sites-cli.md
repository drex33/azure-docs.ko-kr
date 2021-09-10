---
title: CLI를 사용하여 여러 웹 사이트를 호스트
titleSuffix: Azure Application Gateway
description: Azure CLI를 사용하여 여러 웹 사이트를 호스트하는 애플리케이션 게이트웨이를 만드는 방법을 알아봅니다.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: how-to
ms.date: 11/13/2019
ms.author: victorh
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: c637f22a21b73450746a90b83ea7c87249da1d45
ms.sourcegitcommit: 0ab53a984dcd23b0a264e9148f837c12bb27dac0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/08/2021
ms.locfileid: "113507432"
---
# <a name="create-an-application-gateway-that-hosts-multiple-web-sites-using-the-azure-cli"></a>Azure CLI를 사용하여 여러 웹 사이트를 호스트하는 애플리케이션 게이트웨이 만들기

Azure CLI를 사용하여 [애플리케이션 게이트웨이](multiple-site-overview.md)를 만들 때 [여러 웹 사이트의 호스팅](overview.md)을 구성할 수 있습니다. 이 문서에서는 가상 머신 확장 집합을 사용하여 백 엔드 주소 풀을 정의합니다. 그런 다음, 웹 트래픽이 풀에서 적절한 서버에 도착하도록 소유한 도메인을 기준으로 수신기와 규칙을 구성합니다. 이 문서에서는 여러 도메인을 소유하고 있으며 *www\.contoso.com* 및 *www\.fabrikam.com* 의 예를 사용한다고 가정합니다.

이 문서에서는 다음 방법을 설명합니다.

* 네트워크 설정
* 애플리케이션 게이트웨이 만들기
* 백 엔드 수신기 만들기
* 라우팅 규칙 만들기
* 백 엔드 풀을 사용하여 가상 머신 확장 집합 만들기
* 도메인에서 CNAME 레코드 만들기

:::image type="content" source="./media/tutorial-multiple-sites-cli/scenario.png" alt-text="다중 사이트 Application Gateway":::

원하는 경우 [Azure PowerShell](tutorial-multiple-sites-powershell.md)을 사용하여 이 절차를 완료할 수 있습니다.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

 - 이 자습서에는 Azure CLI 버전 2.0.4 이상이 필요합니다. Azure Cloud Shell을 사용하는 경우 최신 버전이 이미 설치되어 있습니다.

## <a name="create-a-resource-group"></a>리소스 그룹 만들기

리소스 그룹은 Azure 리소스가 배포 및 관리되는 논리적 컨테이너입니다. [az group create](/cli/azure/group)를 사용하여 리소스 그룹을 만듭니다.

다음 예제에서는 *eastus* 위치에 *myResourceGroupAG* 라는 리소스 그룹을 만듭니다.

```azurecli-interactive
az group create --name myResourceGroupAG --location eastus
```

## <a name="create-network-resources"></a>네트워크 리소스 만들기

[az network vnet create](/cli/azure/network/vnet)를 사용하여 가상 네트워크와 *myAGSubnet* 이라는 서브넷을 만듭니다. 그런 후 [az network vnet subnet create](/cli/azure/network/vnet/subnet)를 사용하여 백 엔드 서버에 필요한 서브넷을 추가할 수 있습니다. [az network public-ip create](/cli/azure/network/public-ip)를 사용하여 *myAGPublicIPAddress* 라는 IP 주소를 만듭니다.

```azurecli-interactive
az network vnet create \
  --name myVNet \
  --resource-group myResourceGroupAG \
  --location eastus \
  --address-prefix 10.0.0.0/16 \
  --subnet-name myAGSubnet \
  --subnet-prefix 10.0.1.0/24

az network vnet subnet create \
  --name myBackendSubnet \
  --resource-group myResourceGroupAG \
  --vnet-name myVNet \
  --address-prefix 10.0.2.0/24

az network public-ip create \
  --resource-group myResourceGroupAG \
  --name myAGPublicIPAddress \
  --allocation-method Static \
  --sku Standard
```

## <a name="create-the-application-gateway"></a>Application Gateway 만들기

[az network application-gateway create](/cli/azure/network/application-gateway#az_network_application_gateway_create)를 사용하여 애플리케이션 게이트웨이를 만들 수 있습니다. Azure CLI를 사용하여 애플리케이션 게이트웨이를 만들 때 용량, sku, HTTP 설정 등의 구성 정보를 지정합니다. 애플리케이션 게이트웨이는 앞에서 만든 *myAGSubnet* 및 *myAGPublicIPAddress* 에 할당됩니다. 

```azurecli-interactive
az network application-gateway create \
  --name myAppGateway \
  --location eastus \
  --resource-group myResourceGroupAG \
  --vnet-name myVNet \
  --subnet myAGsubnet \
  --capacity 2 \
  --sku Standard_v2 \
  --http-settings-cookie-based-affinity Disabled \
  --frontend-port 80 \
  --http-settings-port 80 \
  --http-settings-protocol Http \
  --public-ip-address myAGPublicIPAddress
```

애플리케이션 게이트웨이가 생성될 때까지 몇 분 정도 걸릴 수 있습니다. 애플리케이션 게이트웨이가 생성되면 다음과 같은 새 기능을 볼 수 있습니다.

- *appGatewayBackendPool* - 애플리케이션 게이트웨이에 백 엔드 주소 풀이 하나 이상 있어야 합니다.
- *appGatewayBackendHttpSettings* - 포트 80 및 HTTP 프로토콜을 통신에 사용하도록 지정합니다.
- *appGatewayHttpListener* - *appGatewayBackendPool* 에 연결되는 기본 수신기입니다.
- *appGatewayFrontendIP* - *myAGPublicIPAddress* 를 *appGatewayHttpListener* 에 할당합니다.
- *rule1* - *appGatewayHttpListener* 에 연결되는 기본 라우팅 규칙입니다.

### <a name="add-the-backend-pools"></a>백 엔드 풀 추가

[az network application-gateway address-pool create](/cli/azure/network/application-gateway/address-pool#az_network_application_gateway_address-pool_create) 명령을 사용하여 백 엔드 서버를 포함하는 데 필요한 백 엔드 풀 추가
```azurecli-interactive
az network application-gateway address-pool create \
  --gateway-name myAppGateway \
  --resource-group myResourceGroupAG \
  --name contosoPool

az network application-gateway address-pool create \
  --gateway-name myAppGateway \
  --resource-group myResourceGroupAG \
  --name fabrikamPool
```

### <a name="add-listeners"></a>수신기 추가

[az network application-gateway http-listener create](/cli/azure/network/application-gateway/http-listener#az_network_application_gateway_http_listener_create)를 사용하여 트래픽을 라우팅하는 데 필요한 수신기를 추가합니다.

>[!NOTE]
> Application Gateway 또는 WAF v2 SKU를 사용하여 수신기당 호스트 이름을 5개까지 구성할 수 있으며, 호스트 이름에 와일드카드 문자를 사용할 수도 있습니다. 자세한 내용은 [수신기의 와일드카드 호스트 이름](multiple-site-overview.md#wildcard-host-names-in-listener-preview)을 참조하세요.
>Azure CLI를 사용하여 수신기에서 여러 호스트 이름 및 와일드카드 문자를 사용하려면 `--host-name` 대신 `--host-names`를 사용해야 합니다. host-names를 사용하여 최대 5개의 호스트 이름을 공백으로 구분된 값으로 지정할 수 있습니다. 예를 들어 `--host-names "*.contoso.com *.fabrikam.com"`

```azurecli-interactive
az network application-gateway http-listener create \
  --name contosoListener \
  --frontend-ip appGatewayFrontendIP \
  --frontend-port appGatewayFrontendPort \
  --resource-group myResourceGroupAG \
  --gateway-name myAppGateway \
  --host-name www.contoso.com

az network application-gateway http-listener create \
  --name fabrikamListener \
  --frontend-ip appGatewayFrontendIP \
  --frontend-port appGatewayFrontendPort \
  --resource-group myResourceGroupAG \
  --gateway-name myAppGateway \
  --host-name www.fabrikam.com   
  ```

### <a name="add-routing-rules"></a>라우팅 규칙 추가

규칙 우선 순위 필드를 사용하지 않는 경우 규칙은 나열된 순서대로 처리됩니다. 트래픽은 특이성에 관계없이 일치하는 첫 번째 규칙을 사용하여 전달됩니다. 예를 들어 기본 수신기를 사용하는 규칙과 다중 사이트 수신기를 사용하는 규칙이 둘 다 같은 포트에 있는 경우 다중 사이트 규칙이 예상대로 작동하려면 다중 사이트 수신기를 사용하는 규칙은 기본 수신기를 사용하는 규칙 앞에 나열되어야 합니다.

이 예제에서는 애플리케이션 게이트웨이가 배포되었을 때 두 개의 새 규칙을 만들고 생성된 기본 규칙을 삭제합니다. [az network application-gateway rule create](/cli/azure/network/application-gateway/rule#az_network_application_gateway_rule_create)를 사용하여 규칙을 추가할 수 있습니다.

```azurecli-interactive
az network application-gateway rule create \
  --gateway-name myAppGateway \
  --name contosoRule \
  --resource-group myResourceGroupAG \
  --http-listener contosoListener \
  --rule-type Basic \
  --address-pool contosoPool

az network application-gateway rule create \
  --gateway-name myAppGateway \
  --name fabrikamRule \
  --resource-group myResourceGroupAG \
  --http-listener fabrikamListener \
  --rule-type Basic \
  --address-pool fabrikamPool

az network application-gateway rule delete \
  --gateway-name myAppGateway \
  --name rule1 \
  --resource-group myResourceGroupAG
```
### <a name="add-priority-to-routing-rules"></a>라우팅 규칙에 우선 순위 추가

보다 구체적인 규칙이 먼저 처리되도록 하려면 규칙 우선 순위 필드를 사용하여 우선 순위가 더 높은지 확인합니다. 모든 기존 요청 라우팅 규칙에 대해 규칙 우선 순위 필드를 설정해야 하며 나중에 생성되는 새 규칙에도 규칙 우선 순위 값이 있어야 합니다.
```azurecli-interactive
az network application-gateway rule create \
  --gateway-name myAppGateway \
  --name wccontosoRule \
  --resource-group myResourceGroupAG \
  --http-listener wccontosoListener \
  --rule-type Basic \
  --priority 200 \
  --address-pool wccontosoPool

az network application-gateway rule create \
  --gateway-name myAppGateway \
  --name shopcontosoRule \
  --resource-group myResourceGroupAG \
  --http-listener shopcontosoListener \
  --rule-type Basic \
  --priority 100 \
  --address-pool shopcontosoPool

```

## <a name="create-virtual-machine-scale-sets"></a>가상 머신 확장 집합 만들기

이 예제에서는 애플리케이션에서 세 개의 백 엔드 풀을 지원하는 세 개의 가상 머신 확장 집합을 만듭니다. 사용자가 만든 확장 집합의 이름은 *myvmss1*, *myvmss2* 및 *myvmss3* 입니다. 각 확장 집합에는 IIS를 설치하는 두 개의 가상 머신 인스턴스가 포함됩니다.

```azurecli-interactive
for i in `seq 1 2`; do

  if [ $i -eq 1 ]
  then
    poolName="contosoPool"
  fi

  if [ $i -eq 2 ]
  then
    poolName="fabrikamPool"
  fi

  az vmss create \
    --name myvmss$i \
    --resource-group myResourceGroupAG \
    --image UbuntuLTS \
    --admin-username azureuser \
    --admin-password Azure123456! \
    --instance-count 2 \
    --vnet-name myVNet \
    --subnet myBackendSubnet \
    --vm-sku Standard_DS2 \
    --upgrade-policy-mode Automatic \
    --app-gateway myAppGateway \
    --backend-pool-name $poolName
done
```

### <a name="install-nginx"></a>NGINX 설치

```azurecli-interactive
for i in `seq 1 2`; do

  az vmss extension set \
    --publisher Microsoft.Azure.Extensions \
    --version 2.0 \
    --name CustomScript \
    --resource-group myResourceGroupAG \
    --vmss-name myvmss$i \
    --settings '{ "fileUris": ["https://raw.githubusercontent.com/Azure/azure-docs-powershell-samples/master/application-gateway/iis/install_nginx.sh"],
  "commandToExecute": "./install_nginx.sh" }'

done
```

## <a name="create-a-cname-record-in-your-domain"></a>도메인에서 CNAME 레코드 만들기

애플리케이션 게이트웨이가 해당 공용 IP 주소로 생성된 후 DNS 주소를 가져와 도메인에서 CNAME 레코드를 만드는 데 사용할 수 있습니다. [az network public-ip show](/cli/azure/network/public-ip#az_network_public_ip_show)를 사용하여 애플리케이션 게이트웨이의 DNS 주소를 가져올 수 있습니다. DNSSettings의 *fqdn* 값을 복사하여 만드는 CNAME 레코드의 값으로 사용합니다. 

```azurecli-interactive
az network public-ip show \
  --resource-group myResourceGroupAG \
  --name myAGPublicIPAddress \
  --query [dnsSettings.fqdn] \
  --output tsv
```

A 레코드를 사용할 경우 애플리케이션 게이트웨이를 다시 시작할 때 VIP가 변경될 수 있으므로 권장되지 않습니다.

## <a name="test-the-application-gateway"></a>애플리케이션 게이트웨이 테스트

브라우저의 주소 표시줄에 도메인 이름을 입력합니다. 예: http:\//www.contoso.com.

![애플리케이션 게이트웨이에서 contoso 사이트 테스트](./media/tutorial-multiple-sites-cli/application-gateway-nginxtest1.png)

주소를 다른 도메인으로 변경하면 다음 예제와 같은 내용이 표시됩니다.

![애플리케이션 게이트웨이에서 fabrikam 사이트 테스트](./media/tutorial-multiple-sites-cli/application-gateway-nginxtest2.png)

## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요 없는 리소스 그룹, 애플리케이션 게이트웨이 및 모든 관련 리소스를 제거합니다.

```azurecli-interactive
az group delete --name myResourceGroupAG
```

## <a name="next-steps"></a>다음 단계

[URL 경로 기반 회람 규칙을 사용하여 애플리케이션 게이트웨이 만들기](./tutorial-url-route-cli.md)
