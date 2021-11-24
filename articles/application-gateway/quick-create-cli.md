---
title: '빠른 시작: CLI를 사용하여 웹 트래픽 보내기'
titleSuffix: Azure Application Gateway
description: 이 빠른 시작에서는 Azure CLI를 사용하여 백 엔드 풀의 가상 머신에 웹 트래픽을 보내는 Azure Application Gateway를 만드는 방법을 알아봅니다.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: quickstart
ms.date: 06/14/2021
ms.author: victorh
ms.custom: mvc, devx-track-js, devx-track-azurecli, mode-api
ms.openlocfilehash: d916c8271c97dbee2704163554c34fb720272d92
ms.sourcegitcommit: 56235f8694cc5f88db3afcc8c27ce769ecf455b0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133054712"
---
# <a name="quickstart-direct-web-traffic-with-azure-application-gateway---azure-cli"></a>빠른 시작: Azure Application Gateway를 통해 웹 트래픽 보내기 - Azure CLI

이 빠른 시작에서는 Azure CLI를 사용하여 애플리케이션 게이트웨이를 만듭니다. 그런 다음, 테스트하여 제대로 작동하는지 확인합니다. 

애플리케이션 게이트웨이는 애플리케이션 웹 트래픽을 백 엔드 풀의 특정 리소스로 보냅니다. 수신기를 포트에 할당하고, 규칙을 만들고, 백 엔드 풀에 리소스를 추가합니다. 간단히 나타내기 위해 이 문서에서는 퍼블릭 프런트 엔드 IP 주소 1개, 애플리케이션 게이트웨이에 단일 사이트를 호스트하는 기본 수신기 1개, 기본 요청 라우팅 규칙 1개 및 백 엔드 풀의 가상 머신 2개를 이용한 간단한 설정을 사용합니다.

:::image type="content" source="media/quick-create-portal/application-gateway-qs-resources.png" alt-text="애플리케이션 게이트웨이 리소스":::


[Azure PowerShell](quick-create-powershell.md) 또는 [Azure Portal](quick-create-portal.md)을 사용하여 이 빠른 시작을 완료할 수도 있습니다.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- 이 문서에는 Azure CLI 버전 2.0.4 이상이 필요합니다. Azure Cloud Shell을 사용하는 경우 최신 버전이 이미 설치되어 있습니다.

## <a name="create-resource-group"></a>리소스 그룹 만들기

Azure에서 관련 리소스를 리소스 그룹에 할당합니다. `az group create`를 사용하여 리소스 그룹을 만듭니다. 

다음 예제에서는 *eastus* 위치에 *myResourceGroupAG* 라는 리소스 그룹을 만듭니다.

```azurecli-interactive 
az group create --name myResourceGroupAG --location eastus
```

## <a name="create-network-resources"></a>네트워크 리소스 만들기 

Azure가 사용자가 만든 리소스 간에 통신하려면 가상 네트워크가 필요합니다.  애플리케이션 게이트웨이 서브넷은 애플리케이션 게이트웨이만 포함할 수 있습니다. 다른 리소스는 허용되지 않습니다.  Application Gateway에 대한 새 서브넷을 만들거나 기존 서브넷을 사용할 수 있습니다. 이 예제에서는 두 개의 서브넷을 만듭니다. 하나는 애플리케이션 게이트웨이용이고, 다른 하나는 백 엔드 서버용입니다. 사용 사례에 따라 Application Gateway의 프런트 엔드 IP를 공용 또는 프라이빗 IP로 구성할 수 있습니다. 이 예제에서는 공용 프런트 엔드 IP 주소를 선택합니다.

가상 네트워크 및 서브넷을 만들려면 `az network vnet create`를 사용합니다. `az network public-ip create`를 실행하여 공용 IP 주소를 만듭니다.

```azurecli-interactive
az network vnet create \
  --name myVNet \
  --resource-group myResourceGroupAG \
  --location eastus \
  --address-prefix 10.21.0.0/16 \
  --subnet-name myAGSubnet \
  --subnet-prefix 10.21.0.0/24
az network vnet subnet create \
  --name myBackendSubnet \
  --resource-group myResourceGroupAG \
  --vnet-name myVNet   \
  --address-prefix 10.21.1.0/24
az network public-ip create \
  --resource-group myResourceGroupAG \
  --name myAGPublicIPAddress \
  --allocation-method Static \
  --sku Standard
```

## <a name="create-the-backend-servers"></a>백 엔드 서버 만들기

백 엔드 풀은 NIC, 가상 머신 확장 집합, 공용 IP 주소, 내부 IP 주소, FQDN(정규화된 도메인 이름) 및 다중 테넌트 백 엔드(예: Azure App Service)로 구성할 수 있습니다. 이 예제에서는 애플리케이션 게이트웨이의 백 엔드 서버로 사용되는 두 개의 가상 머신을 만듭니다. 그리고 가상 머신에 IIS를 설치하여 애플리케이션 게이트웨이를 테스트합니다.

#### <a name="create-two-virtual-machines"></a>두 개의 가상 머신 만들기

애플리케이션 게이트웨이가 성공적으로 만들어졌는지 확인할 수 있도록 가상 머신에 NGINX 웹 서버를 설치합니다. cloud-init 구성 파일을 사용하여 NGINX를 설치하고 Linux 가상 머신에서 "Hello World" Node.js 앱을 실행할 수 있습니다. cloud-init에 대한 자세한 내용은 [Azure의 가상 머신에 대한 Cloud-init 지원](../virtual-machines/linux/using-cloud-init.md)을 참조하세요.

Azure Cloud Shell에서 다음 구성을 복사하여 *cloud-init.txt* 파일에 붙여넣습니다. *editor cloud-init.txt* 를 입력하여 파일을 만듭니다.

```yaml
#cloud-config
package_upgrade: true
packages:
  - nginx
  - nodejs
  - npm
write_files:
  - owner: www-data:www-data
  - path: /etc/nginx/sites-available/default
    content: |
      server {
        listen 80;
        location / {
          proxy_pass http://localhost:3000;
          proxy_http_version 1.1;
          proxy_set_header Upgrade $http_upgrade;
          proxy_set_header Connection keep-alive;
          proxy_set_header Host $host;
          proxy_cache_bypass $http_upgrade;
        }
      }
  - owner: azureuser:azureuser
  - path: /home/azureuser/myapp/index.js
    content: |
      var express = require('express')
      var app = express()
      var os = require('os');
      app.get('/', function (req, res) {
        res.send('Hello World from host ' + os.hostname() + '!')
      })
      app.listen(3000, function () {
        console.log('Hello world app listening on port 3000!')
      })
runcmd:
  - service nginx restart
  - cd "/home/azureuser/myapp"
  - npm init
  - npm install express -y
  - nodejs index.js
```

`az network nic create`를 사용하여 네트워크 인터페이스를 만듭니다. 가상 머신을 만들려면 `az vm create`를 사용합니다.

```azurecli-interactive
for i in `seq 1 2`; do
  az network nic create \
    --resource-group myResourceGroupAG \
    --name myNic$i \
    --vnet-name myVNet \
    --subnet myBackendSubnet
  az vm create \
    --resource-group myResourceGroupAG \
    --name myVM$i \
    --nics myNic$i \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys \
    --custom-data cloud-init.txt
done
```

## <a name="create-the-application-gateway"></a>Application Gateway 만들기

`az network application-gateway create`를 사용하여 애플리케이션 게이트웨이를 만듭니다. Azure CLI를 사용하여 애플리케이션 게이트웨이를 만들 때 용량, SKU, HTTP 설정 등의 구성 정보를 지정합니다. 그러면 Azure가 네트워크 인터페이스의 개인 IP 주소를 애플리케이션 게이트웨이의 백 엔드 풀에 서버로 추가합니다.

```azurecli-interactive
address1=$(az network nic show --name myNic1 --resource-group myResourceGroupAG | grep "\"privateIpAddress\":" | grep -oE '[^ ]+$' | tr -d '",')
address2=$(az network nic show --name myNic2 --resource-group myResourceGroupAG | grep "\"privateIpAddress\":" | grep -oE '[^ ]+$' | tr -d '",')
az network application-gateway create \
  --name myAppGateway \
  --location eastus \
  --resource-group myResourceGroupAG \
  --capacity 2 \
  --sku Standard_v2 \
  --public-ip-address myAGPublicIPAddress \
  --vnet-name myVNet \
  --subnet myAGSubnet \
  --servers "$address1" "$address2"
```

Azure가 애플리케이션 게이트웨이를 만들 때까지 최대 30분이 걸릴 수 있습니다. 만들어지면 **애플리케이션 게이트웨이** 페이지의 **설정** 섹션에서 다음 설정을 볼 수 있습니다.

- **appGatewayBackendPool**: **백 엔드 풀** 페이지에 있습니다. 필요한 백 엔드 풀을 지정합니다.
- **appGatewayBackendHttpSettings**: **HTTP 설정** 페이지에 있습니다. 애플리케이션 게이트웨이가 통신에 포트 80 및 HTTP 프로토콜을 사용하도록 지정합니다.
- **appGatewayHttpListener**: **수신기 페이지** 에 있습니다. **appGatewayBackendPool** 과 연결되는 기본 수신기를 지정합니다.
- **appGatewayFrontendIP**: **프런트 엔드 IP 구성** 페이지에 있습니다. *myAGPublicIPAddress* 를 **appGatewayHttpListener** 에 할당합니다.
- **rule1**: **규칙** 페이지에 있습니다. **appGatewayHttpListener** 에 연결되는 기본 회람 규칙을 지정합니다.

## <a name="test-the-application-gateway"></a>애플리케이션 게이트웨이 테스트

Azure에서 애플리케이션 게이트웨이를 만들려면 반드시 NGINX 웹 서버가 필요한 것은 아니지만, 이 빠른 시작에서는 Azure가 애플리케이션 게이트웨이를 성공적으로 만들었는지 확인하기 위해 설치했습니다. 새 애플리케이션 게이트웨이의 공용 IP 주소를 가져오려면 `az network public-ip show`를 사용합니다. 

```azurecli-interactive
az network public-ip show \
  --resource-group myResourceGroupAG \
  --name myAGPublicIPAddress \
  --query [ipAddress] \
  --output tsv
```

공용 IP 주소를 복사하여 브라우저의 주소 표시줄에 붙여넣습니다.
    
![애플리케이션 게이트웨이 테스트](./media/quick-create-cli/application-gateway-nginxtest.png)

브라우저를 새로 고치면 보조 VM의 이름이 표시됩니다. 이는 애플리케이션 게이트웨이가 성공적으로 만들어졌으며 백 엔드에 연결할 수 있다는 의미입니다.

## <a name="clean-up-resources"></a>리소스 정리

애플리케이션 게이트웨이로 만든 리소스가 더 이상 필요 없으면 `az group delete` 명령을 사용하여 리소스 그룹을 삭제합니다. 리소스 그룹을 삭제하면 애플리케이션 게이트웨이 및 모든 관련 리소스도 함께 삭제됩니다.

```azurecli-interactive 
az group delete --name myResourceGroupAG
```

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Azure CLI를 사용하여 애플리케이션 게이트웨이로 웹 트래픽 관리](./tutorial-manage-web-traffic-cli.md)
