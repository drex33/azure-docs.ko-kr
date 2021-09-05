---
title: Azure Files에서 사용하기 위한 Linux의 P2S(지점 및 사이트 간) VPN 구성 | Microsoft Docs
description: Azure Files에서 사용하기 위한 Linux의 P2S(지점 및 사이트 간) VPN을 구성하는 방법
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 10/19/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 120f76666fb8ef55fd0cb51fe0c04b1d8448ce82
ms.sourcegitcommit: 05dd6452632e00645ec0716a5943c7ac6c9bec7c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/17/2021
ms.locfileid: "122538294"
---
# <a name="configure-a-point-to-site-p2s-vpn-on-linux-for-use-with-azure-files"></a>Azure Files에서 사용하기 위한 Linux의 P2S(지점 및 사이트 간) VPN 구성
P2S(지점 및 사이트 간) VPN 연결을 사용하여 개방형 인터넷을 통해 데이터를 보내지 않고도 Azure 외부에서 Azure 파일 공유를 탑재할 수 있습니다. 지점 및 사이트 간 VPN 연결은 Azure와 개별 클라이언트 간의 VPN 연결입니다. Azure Files에서 P2S VPN 연결을 사용하려면 연결하려는 각 클라이언트에 대해 P2S VPN 연결을 구성해야 합니다. 온-프레미스 네트워크에서 Azure 파일 공유에 연결해야 하는 클라이언트가 많은 경우에는 각 클라이언트에 대해 지점 및 사이트 간 연결 대신 S2S(사이트 간) VPN 연결을 사용할 수 있습니다. 자세한 내용은 [Azure Files에서 사용하기 위한 사이트 간 VPN 구성](storage-files-configure-s2s-vpn.md)을 참조하세요.

Azure Files에 사용할 수 있는 네트워킹 옵션에 대해 자세히 알아보려면 이 방법 문서를 계속하기 전에 먼저 [Azure Files 네트워킹 개요](storage-files-networking-overview.md)를 참조하는 것이 좋습니다.

이 문서에서는 Linux에서 지점 및 사이트 간 VPN을 구성하여 Azure 파일 공유를 온-프레미스에 직접 탑재하는 단계를 자세히 설명합니다.

## <a name="applies-to"></a>적용 대상
| 파일 공유 유형 | SMB | NFS |
|-|:-:|:-:|
| 표준 파일 공유(GPv2), LRS/ZRS | ![예](../media/icons/yes-icon.png) | ![아니요](../media/icons/no-icon.png) |
| 표준 파일 공유(GPv2), GRS/GZRS | ![예](../media/icons/yes-icon.png) | ![아니요](../media/icons/no-icon.png) |
| 프리미엄 파일 공유(FileStorage), LRS/ZRS | ![예](../media/icons/yes-icon.png) | ![예](../media/icons/yes-icon.png) |

## <a name="prerequisites"></a>필수 구성 요소
- 최신 버전의 Azure CLI. Azure CLI를 설치하는 방법에 대한 자세한 내용은 [Azure PowerShell CLI 설치](/cli/azure/install-azure-cli)를 참조하고 해당 운영 체제를 선택합니다. Linux에서 Azure PowerShell 모듈을 사용하려는 경우에는 Azure CLI에 대한 아래 지침이 제공될 수 있습니다.

- 온-프레미스에 탑재하려는 Azure 파일 공유. Azure 파일 공유는 스토리지 계정 내에 배포됩니다. 스토리지 계정은 여러 파일 공유뿐만 아니라 다른 스토리지 리소스(예: Blob 컨테이너 또는 큐)도 배포할 수 있는 공유 스토리지 풀을 나타내는 관리 구조입니다. [Azure 파일 공유 만들기](storage-how-to-create-file-share.md)에서 Azure 파일 공유 및 스토리지 계정을 배포하는 방법에 대해 자세히 알아봅니다.

- 온-프레미스에 탑재하려는 Azure 파일 공유가 포함된 스토리지 계정의 프라이빗 엔드포인트입니다. 프라이빗 엔드포인트를 만드는 방법에 대한 자세한 내용은 [Azure Files 네트워크 엔드포인트 구성](storage-files-networking-endpoints.md?tabs=azure-cli)을 참조하세요. 

## <a name="install-required-software"></a>필수 소프트웨어 설치
Azure 가상 네트워크 게이트웨이는 IPsec 및 OpenVPN을 비롯한 여러 VPN 프로토콜을 사용하는 VPN 연결을 제공할 수 있습니다. 이 가이드에서는 IPsec 사용 방법을 보여주며 strongSwan 패키지를 사용하여 Linux에서 지원을 제공합니다. 

> Ubuntu 18.10에서 확인됩니다.

```bash
sudo apt update
sudo apt install strongswan strongswan-pki libstrongswan-extra-plugins curl libxml2-utils cifs-utils unzip

installDir="/etc/"
```

### <a name="deploy-a-virtual-network"></a>가상 네트워크 배포 
지점 및 사이트 간 VPN을 통해 온-프레미스에서 Azure 파일 공유 및 기타 Azure 리소스에 액세스하려면 VNet(가상 네트워크)을 만들어야 합니다. 자동으로 생성되는 P2S VPN 연결은 온-프레미스 Linux 머신과 이 Azure 가상 네트워크 간의 브리지입니다.

다음 스크립트는 세 개의 서브넷을 사용하여 Azure 가상 네트워크를 만듭니다. 하나는 스토리지 계정의 서비스 엔드포인트를 위한 것이고, 다른 하나는 변경될 수 있는 스토리지 계정의 공용 IP에 대한 사용자 지정 라우팅을 생성하지 않고 온-프레미스에서 스토리지 계정에 액세스해야 하는 스토리지 계정의 프라이빗 엔드포인트입니다. 마지막은 VPN 서비스를 제공하는 가상 네트워크 게이트웨이를 위한 것입니다. 

`<region>`, `<resource-group>` 및 `<desired-vnet-name>`을 사용자 환경에 적합한 값으로 바꿔야 합니다.

```bash
region="<region>"
resourceGroupName="<resource-group>"
virtualNetworkName="<desired-vnet-name>"

virtualNetwork=$(az network vnet create \
    --resource-group $resourceGroupName \
    --name $virtualNetworkName \
    --location $region \
    --address-prefixes "192.168.0.0/16" \
    --query "newVNet.id" | tr -d '"')

serviceEndpointSubnet=$(az network vnet subnet create \
    --resource-group $resourceGroupName \
    --vnet-name $virtualNetworkName \
    --name "ServiceEndpointSubnet" \
    --address-prefixes "192.168.0.0/24" \
    --service-endpoints "Microsoft.Storage" \
    --query "id" | tr -d '"')

privateEndpointSubnet=$(az network vnet subnet create \
    --resource-group $resourceGroupName \
    --vnet-name $virtualNetworkName \
    --name "PrivateEndpointSubnet" \
    --address-prefixes "192.168.1.0/24" \
    --query "id" | tr -d '"')

gatewaySubnet=$(az network vnet subnet create \
    --resource-group $resourceGroupName \
    --vnet-name $virtualNetworkName \
    --name "GatewaySubnet" \
    --address-prefixes "192.168.2.0/24" \
    --query "id" | tr -d '"')
```

## <a name="create-certificates-for-vpn-authentication"></a>VPN 인증에 대한 인증서 만들기
온-프레미스 Linux 머신에서 VPN 연결을 인증하여 가상 네트워크에 액세스할 수 있도록 하려면 두 개의 인증서를 만들어야 합니다. 하나는 가상 머신 게이트웨이에 제공되는 루트 인증서이고, 다른 하나는 루트 인증서로 서명되는 클라이언트 인증서입니다. 다음 스크립트에서는 필수 인증서를 만듭니다.

```bash
rootCertName="P2SRootCert"
username="client"
password="1234"

mkdir temp
cd temp

sudo ipsec pki --gen --outform pem > rootKey.pem
sudo ipsec pki --self --in rootKey.pem --dn "CN=$rootCertName" --ca --outform pem > rootCert.pem

rootCertificate=$(openssl x509 -in rootCert.pem -outform der | base64 -w0 ; echo)

sudo ipsec pki --gen --size 4096 --outform pem > "clientKey.pem"
sudo ipsec pki --pub --in "clientKey.pem" | \
    sudo ipsec pki \
        --issue \
        --cacert rootCert.pem \
        --cakey rootKey.pem \
        --dn "CN=$username" \
        --san $username \
        --flag clientAuth \
        --outform pem > "clientCert.pem"

openssl pkcs12 -in "clientCert.pem" -inkey "clientKey.pem" -certfile rootCert.pem -export -out "client.p12" -password "pass:$password"
```

## <a name="deploy-virtual-network-gateway"></a>가상 네트워크 게이트웨이 배포
Azure 가상 네트워크 게이트웨이는 온-프레미스 Linux 머신에서 연결할 서비스입니다. 이 서비스를 배포하려면 두 가지 기본 구성 요소가 필요합니다. 즉, 전 세계에 있는 클라이언트에 대한 게이트웨이를 식별하는 공용 IP와 클라이언트를 인증하는 데 사용되는 이전에 만든 루트 인증서가 필요합니다.

`<desired-vpn-name-here>`를 이러한 리소스에 사용할 이름으로 바꿔야 합니다.

> [!Note]  
> Azure 가상 네트워크 게이트웨이를 배포하는 데 최대 45분이 걸릴 수 있습니다. 이 리소스가 배포되는 동안 이 bash 스크립트는 배포가 완료 될 때까지 차단됩니다.
>
> P2S IKEv2/OpenVPN 연결은 **기본** SKU에서 지원되지 않습니다. 따라서 이 스크립트는 가상 네트워크 게이트웨이에 대해 **VpnGw1** SKU를 사용합니다.

```bash
vpnName="<desired-vpn-name-here>"
publicIpAddressName="$vpnName-PublicIP"

publicIpAddress=$(az network public-ip create \
    --resource-group $resourceGroupName \
    --name $publicIpAddressName \
    --location $region \
    --sku "Basic" \
    --allocation-method "Dynamic" \
    --query "publicIp.id" | tr -d '"')

az network vnet-gateway create \
    --resource-group $resourceGroupName \
    --name $vpnName \
    --vnet $virtualNetworkName \
    --public-ip-addresses $publicIpAddress \
    --location $region \
    --sku "VpnGw1" \
    --gateway-typ "Vpn" \
    --vpn-type "RouteBased" \
    --address-prefixes "172.16.201.0/24" \
    --client-protocol "IkeV2" > /dev/null

az network vnet-gateway root-cert create \
    --resource-group $resourceGroupName \
    --gateway-name $vpnName \
    --name $rootCertName \
    --public-cert-data $rootCertificate \
    --output none
```

## <a name="configure-the-vpn-client"></a>VPN 클라이언트 구성
Azure 가상 네트워크 게이트웨이는 온-프레미스 Linux 머신에서 VPN 연결을 초기화하는 데 필요한 구성 파일이 포함된 다운로드 가능한 패키지를 만듭니다. 다음 스크립트는 적합한 지점에서 생성된 인증서를 저장하고 다운로드 가능한 패키지의 구성 파일에서 올바른 값을 포함하는 `ipsec.conf` 파일을 구성합니다.

```bash
vpnClient=$(az network vnet-gateway vpn-client generate \
    --resource-group $resourceGroupName \
    --name $vpnName \
    --authentication-method EAPTLS | tr -d '"')

curl $vpnClient --output vpnClient.zip
unzip vpnClient.zip

vpnServer=$(xmllint --xpath "string(/VpnProfile/VpnServer)" Generic/VpnSettings.xml)
vpnType=$(xmllint --xpath "string(/VpnProfile/VpnType)" Generic/VpnSettings.xml | tr '[:upper:]' '[:lower:]')
routes=$(xmllint --xpath "string(/VpnProfile/Routes)" Generic/VpnSettings.xml)

sudo cp "${installDir}ipsec.conf" "${installDir}ipsec.conf.backup"
sudo cp "Generic/VpnServerRoot.cer" "${installDir}ipsec.d/cacerts"
sudo cp "${username}.p12" "${installDir}ipsec.d/private" 

echo -e "\nconn $virtualNetworkName" | sudo tee -a "${installDir}ipsec.conf" > /dev/null
echo -e "\tkeyexchange=$vpnType" | sudo tee -a "${installDir}ipsec.conf" > /dev/null
echo -e "\ttype=tunnel" | sudo tee -a "${installDir}ipsec.conf" > /dev/null
echo -e "\tleftfirewall=yes" | sudo tee -a "${installDir}ipsec.conf" > /dev/null
echo -e "\tleft=%any" | sudo tee -a "${installDir}ipsec.conf" > /dev/null
echo -e "\tleftauth=eap-tls" | sudo tee -a "${installDir}ipsec.conf" > /dev/null
echo -e "\tleftid=%client" | sudo tee -a "${installDir}ipsec.conf" > /dev/null
echo -e "\tright=$vpnServer" | sudo tee -a "${installDir}ipsec.conf" > /dev/null
echo -e "\trightid=%$vpnServer" | sudo tee -a "${installDir}ipsec.conf" > /dev/null
echo -e "\trightsubnet=$routes" | sudo tee -a "${installDir}ipsec.conf" > /dev/null
echo -e "\tleftsourceip=%config" | sudo tee -a "${installDir}ipsec.conf" > /dev/null 
echo -e "\tauto=add" | sudo tee -a "${installDir}ipsec.conf" > /dev/null

echo ": P12 client.p12 '$password'" | sudo tee -a "${installDir}ipsec.secrets" > /dev/null

sudo ipsec restart
sudo ipsec up $virtualNetworkName 
```

## <a name="mount-azure-file-share"></a>Azure 파일 공유 탑재
지점 및 사이트 간 VPN을 설정했으므로 Azure 파일 공유를 탑재할 수 있습니다. 다음 예제에서는 영구적이지 않은 공유를 탑재합니다. 지속적으로 탑재하려면 [Linux에 SMB 파일 공유 탑재](storage-how-to-use-files-linux.md) 또는 [Linux에 NFS 파일 공유 탑재](storage-files-how-to-mount-nfs-shares.md)를 참조하세요. 

## <a name="see-also"></a>참조
- [Azure Files 네트워킹 개요](storage-files-networking-overview.md)
- [Azure Files에서 사용하기 위한 Windows의 P2S(지점 및 사이트 간) VPN 구성](storage-files-configure-p2s-vpn-windows.md)
- [Azure Files에서 사용할 S2S(사이트 간) VPN 구성](storage-files-configure-s2s-vpn.md)
