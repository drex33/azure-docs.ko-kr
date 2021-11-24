---
title: 빠른 시작 - Azure CLI를 사용하여 Azure 프라이빗 엔드포인트 만들기
description: 이 빠른 시작을 통해 Azure CLI를 사용하여 프라이빗 엔드포인트를 만드는 방법을 알아봅니다.
services: private-link
author: asudbring
ms.service: private-link
ms.topic: quickstart
ms.date: 11/07/2020
ms.author: allensu
ms.custom: mode-other
ms.openlocfilehash: 9b9e2f9293e48c15bca30d2fd9c7bead19607441
ms.sourcegitcommit: 56235f8694cc5f88db3afcc8c27ce769ecf455b0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133052228"
---
# <a name="quickstart-create-a-private-endpoint-using-azure-cli"></a>빠른 시작: Azure CLI를 사용하여 프라이빗 엔드포인트 만들기

Private Endpoint를 통해 Azure 웹앱에 안전하게 연결하여 Azure Private Link를 시작합니다.

이 빠른 시작에서는 Azure 웹앱에 대한 프라이빗 엔드포인트를 만들고, 가상 머신을 배포하여 프라이빗 연결을 테스트합니다.  

Azure SQL 및 Azure Storage와 같은 다양한 종류의 Azure 서비스에 대한 프라이빗 엔드포인트를 만들 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

* 활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* **PremiumV2 계층** 이상의 App Service 요금제가 Azure 구독에 배포된 Azure 웹앱  
    * 자세한 내용 및 예제는 [빠른 시작: Azure에서 ASP.NET Core 웹앱 만들기](../app-service/quickstart-dotnetcore.md)를 참조하세요. 
    * 웹앱 및 엔드포인트를 만드는 방법에 대한 자세한 자습서는 [자습서: Azure Private Endpoint를 사용하여 웹앱에 연결](tutorial-private-endpoint-webapp-portal.md)을 참조하세요.
* Azure Portal에 로그인하고 `az login`을 실행하여 구독이 활성 상태인지 확인합니다.
* 터미널 또는 명령 창에서 `az --version`을 실행하여 Azure CLI 버전을 확인합니다. 최신 버전은 [최신 릴리스 정보](/cli/azure/release-notes-azure-cli?tabs=azure-cli)를 참조하세요.
  * 최신 버전이 없는 경우 [사용 중인 운영 체제 또는 플랫폼의 설치 가이드](/cli/azure/install-azure-cli)에 따라 설치를 업데이트합니다.

## <a name="create-a-resource-group"></a>리소스 그룹 만들기

Azure 리소스 그룹은 Azure 리소스가 배포 및 관리되는 논리적 컨테이너입니다.

[az group create](/cli/azure/group#az_group_create)를 사용하여 리소스 그룹을 만듭니다.

* 이름을 **CreatePrivateEndpointQS-rg** 로 지정합니다. 
* 위치: **eastus**

```azurecli-interactive
az group create \
    --name CreatePrivateEndpointQS-rg \
    --location eastus
```

## <a name="create-a-virtual-network-and-bastion-host"></a>가상 네트워크 및 베스천 호스트 만들기

이 섹션에서는 가상 네트워크, 서브넷 및 베스천 호스트를 만듭니다. 

베스천 호스트는 가상 머신에 안전하게 연결하여 프라이빗 엔드포인트를 테스트하는 데 사용됩니다.

[az network vnet create](/cli/azure/network/vnet#az_network_vnet_create)를 사용하여 가상 네트워크를 만듭니다.

* 이름: **myVNet**
* **10.0.0.0/16** 의 주소 접두사.
* 서브넷: **myBackendSubnet**
* **10.0.0.0/24** 의 서브넷 접두사.
* **CreatePrivateEndpointQS-rg** 리소스 그룹에서
* 위치: **eastus**.

```azurecli-interactive
az network vnet create \
    --resource-group CreatePrivateEndpointQS-rg\
    --location eastus \
    --name myVNet \
    --address-prefixes 10.0.0.0/16 \
    --subnet-name myBackendSubnet \
    --subnet-prefixes 10.0.0.0/24
```

[az network vnet subnet update](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_update)를 사용하여 프라이빗 엔드포인트에 대한 프라이빗 엔드포인트 네트워크 정책을 사용하지 않도록 서브넷을 업데이트합니다.

```azurecli-interactive
az network vnet subnet update \
    --name myBackendSubnet \
    --resource-group CreatePrivateEndpointQS-rg \
    --vnet-name myVNet \
    --disable-private-endpoint-network-policies true
```

[az network public-ip create](/cli/azure/network/public-ip#az_network_public_ip_create)를 사용하여 베스천 호스트에 대한 공용 IP 주소를 만듭니다.

* **myBastionIP** 라는 표준 영역 중복 공용 IP 주소를 만듭니다.
* **CreatePrivateEndpointQS-rg** 에서

```azurecli-interactive
az network public-ip create \
    --resource-group CreatePrivateEndpointQS-rg \
    --name myBastionIP \
    --sku Standard
```

[az network vnet subnet create](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_create)를 사용하여 베스천 서브넷을 만듭니다.

* 이름은 **AzureBastionSubnet** 입니다.
* **10.0.1.0/24** 의 주소 접두사.
* 가상 네트워크: **myVNet**
* 리소스 그룹 **CreatePrivateEndpointQS-rg** 에서

```azurecli-interactive
az network vnet subnet create \
    --resource-group CreatePrivateEndpointQS-rg \
    --name AzureBastionSubnet \
    --vnet-name myVNet \
    --address-prefixes 10.0.1.0/24
```

[az network bastion create](/cli/azure/network/bastion#az_network_bastion_create)를 사용하여 베스천 호스트를 만듭니다.

* 이름은 **myBastionHost** 입니다.
* **CreatePrivateEndpointQS-rg** 에서
* 공용 IP **myBastionIP** 와 연결됩니다.
* 가상 네트워크 **myVNet** 과 연결됩니다.
* 위치: **eastus**.

```azurecli-interactive
az network bastion create \
    --resource-group CreatePrivateEndpointQS-rg \
    --name myBastionHost \
    --public-ip-address myBastionIP \
    --vnet-name myVNet \
    --location eastus
```

Azure Bastion 호스트를 배포하는 데 몇 분 정도 걸릴 수 있습니다.

## <a name="create-test-virtual-machine"></a>테스트 가상 머신 만들기

이 섹션에서는 프라이빗 엔드포인트를 테스트하는 데 사용할 가상 머신을 만듭니다.

 [az vm create](/cli/azure/vm#az_vm_create)를 사용하여 VM을 만듭니다. 메시지가 표시되면 VM에 대한 자격 증명으로 사용할 암호를 제공합니다.

* 이름을 **myVM** 으로 지정합니다.
* **CreatePrivateEndpointQS-rg** 에서
* 네트워크 **myVNet** 에서
* 서브넷: **myBackendSubnet**
* 서버 이미지 **Win2019Datacenter**.

```azurecli-interactive
az vm create \
    --resource-group CreatePrivateEndpointQS-rg \
    --name myVM \
    --image Win2019Datacenter \
    --public-ip-address "" \
    --vnet-name myVNet \
    --subnet myBackendSubnet \
    --admin-username azureuser
```

[!INCLUDE [ephemeral-ip-note.md](../../includes/ephemeral-ip-note.md)]

## <a name="create-private-endpoint"></a>프라이빗 엔드포인트 만들기

이 섹션에서는 프라이빗 엔드포인트를 만듭니다.

[az webapp list](/cli/azure/webapp#az_webapp_list)를 사용하여 이전에 만든 웹앱의 리소스 ID를 셸 변수에 배치합니다.

[az network private-endpoint create](/cli/azure/network/private-endpoint#az_network_private_endpoint_create)를 사용하여 엔드포인트 및 연결을 만듭니다.

* 이름을 **myPrivateEndpoint** 로 지정합니다.
* 리소스 그룹 **CreatePrivateEndpointQS-rg** 에서
* 가상 네트워크: **myVNet**
* 서브넷: **myBackendSubnet**
* **myConnection** 이라는 연결입니다.
* 웹앱 **\<webapp-resource-group-name>** .

```azurecli-interactive
id=$(az webapp list \
    --resource-group <webapp-resource-group-name> \
    --query '[].[id]' \
    --output tsv)

az network private-endpoint create \
    --name myPrivateEndpoint \
    --resource-group CreatePrivateEndpointQS-rg \
    --vnet-name myVNet --subnet myBackendSubnet \
    --private-connection-resource-id $id \
    --group-id sites \
    --connection-name myConnection  
```

## <a name="configure-the-private-dns-zone"></a>프라이빗 DNS 영역 구성

이 섹션에서는 [az network private-dns zone create](/cli/azure/network/private-dns/zone#az_network_private_dns_zone_create)를 사용하여 프라이빗 DNS 영역을 만들고 구성합니다.  

[az network private-dns link vnet create](/cli/azure/network/private-dns/link/vnet#az_network_private_dns_link_vnet_create)를 사용하여 dns 영역에 대한 가상 네트워크 링크를 만듭니다.

[az network private-endpoint dns-zone-group create](/cli/azure/network/private-endpoint/dns-zone-group#az_network_private_endpoint_dns_zone_group_create)를 사용하여 dns 영역 그룹을 만듭니다.

* **privatelink.azurewebsites.net** 이라는 영역
* 가상 네트워크: **myVNet**
* 리소스 그룹 **CreatePrivateEndpointQS-rg** 에서
* **myDNSLink** 라는 DNS 링크.
* **myPrivateEndpoint** 와 연결됩니다.
* **MyZoneGroup** 이라는 영역 그룹.

```azurecli-interactive
az network private-dns zone create \
    --resource-group CreatePrivateEndpointQS-rg \
    --name "privatelink.azurewebsites.net"

az network private-dns link vnet create \
    --resource-group CreatePrivateEndpointQS-rg \
    --zone-name "privatelink.azurewebsites.net" \
    --name MyDNSLink \
    --virtual-network myVNet \
    --registration-enabled false

az network private-endpoint dns-zone-group create \
   --resource-group CreatePrivateEndpointQS-rg \
   --endpoint-name myPrivateEndpoint \
   --name MyZoneGroup \
   --private-dns-zone "privatelink.azurewebsites.net" \
   --zone-name webapp
```

## <a name="test-connectivity-to-private-endpoint"></a>프라이빗 엔드포인트에 연결 테스트

이 섹션에서는 이전 단계에서 만든 가상 머신을 사용하여 프라이빗 엔드포인트에서 SQL 서버에 연결합니다.

1. [Azure 포털](https://portal.azure.com) 
 
2. 왼쪽 탐색 창에서 **리소스 그룹** 을 선택합니다.

3. **CreatePrivateEndpointQS-rg** 를 선택합니다.

4. **myVM** 을 선택합니다.

5. **myVM** 에 대한 개요 페이지에서 **연결** 을 선택한 다음, **Bastion** 을 선택합니다.

6. 파란색 **Bastion 사용** 단추를 선택합니다.

7. 가상 머신 만들기에서 입력한 사용자 이름과 암호를 입력합니다.

8. 연결한 후 서버에서 Windows PowerShell을 엽니다.

9. `nslookup <your-webapp-name>.azurewebsites.net`를 입력합니다. **\<your-webapp-name>** 을 이전 단계에서 만든 웹앱의 이름으로 바꿉니다.  아래 표시된 것과 유사한 메시지가 표시됩니다.

    ```powershell
    Server:  UnKnown
    Address:  168.63.129.16

    Non-authoritative answer:
    Name:    mywebapp8675.privatelink.azurewebsites.net
    Address:  10.0.0.5
    Aliases:  mywebapp8675.azurewebsites.net
    ```

    웹앱 이름에 대해 **10.0.0.5** 의 개인 IP 주소가 반환됩니다.  이 주소는 이전에 만든 가상 네트워크의 서브넷에 있습니다.

10. **myVM** 에 대한 베스천 연결에서 Internet Explorer를 엽니다.

11. 웹앱의 URL(**https://\<your-webapp-name>.azurewebsites.net**)을 입력합니다.

12. 애플리케이션이 배포되지 않은 경우 기본 웹앱 페이지가 표시됩니다.

    :::image type="content" source="./media/create-private-endpoint-portal/web-app-default-page.png" alt-text="기본 웹앱 페이지" border="true":::

13. **myVM** 에 대한 연결을 닫습니다.

## <a name="clean-up-resources"></a>리소스 정리 
프라이빗 엔드포인트 및 VM 사용을 마쳤으면 [az group delete](/cli/azure/group#az_group_delete)를 사용하여 리소스 그룹과 리소스 그룹에 포함된 모든 리소스를 제거합니다.

```azurecli-interactive
az group delete \
    --name CreatePrivateEndpointQS-rg
```

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 다음을 만들었습니다.

* 가상 네트워크 및 베스천 호스트
* 가상 머신
* Azure Web App에 대한 프라이빗 엔드포인트

가상 머신을 사용하여 프라이빗 엔드포인트에서 웹앱에 대한 연결을 안전하게 테스트했습니다.

프라이빗 엔드포인트를 지원하는 서비스에 대한 자세한 내용은 다음을 참조하세요.
> [!div class="nextstepaction"]
> [Private Link 가용성](private-link-overview.md#availability)
