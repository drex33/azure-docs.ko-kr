---
title: Azure HDInsight 클러스터에 가상 네트워크 만들기
description: Azure 가상 네트워크를 사용하여 HDInsight를 다른 클라우드 리소스 또는 데이터 센터의 리소스에 연결하는 방법을 알아봅니다.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive, devx-track-azurecli, devx-track-azurepowershell
ms.date: 05/12/2021
ms.openlocfilehash: 642ec1908298c331777776f886e005e715a727f2
ms.sourcegitcommit: ee8ce2c752d45968a822acc0866ff8111d0d4c7f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/14/2021
ms.locfileid: "113730643"
---
# <a name="create-virtual-networks-for-azure-hdinsight-clusters"></a>Azure HDInsight 클러스터에 가상 네트워크 만들기

이 문서에서는 [Azure 가상 네트워크](../virtual-network/virtual-networks-overview.md)를 만들고 구성하기 위한 예제 및 코드 샘플을 제공합니다. Azure HDInsight 클러스터와 함께 사용하기 위해 네트워크 보안 그룹 (NSGs)을 만들고 DNS를 구성하는 방법에 대한 자세한 예가 제공됩니다.

Azure HDInsight에 대한 가상 네트워크를 사용하는 방법에 대한 배경 내용은 [Azure HDInsight의 가상 네트워크 계획](hdinsight-plan-virtual-network-deployment.md)을 참조하세요.

## <a name="prerequisites-for-code-samples-and-examples"></a>코드 샘플 및 예제에 대한 사전 요구 사항

이 문서의 코드 샘플을 실행하기 전에 TCP/IP 네트워킹에 대해 이해하고 있어야 합니다. TCP/IP 네트워킹에 익숙하지 않은 경우, 프로덕션 네트워크를 수정하기 전에 다른 사용자와 상의하세요.

이 문서의 샘플에 대한 다른 사전 요구 사항에는 다음 항목이 포함됩니다.

* PowerShell을 사용하는 경우 [AZ Module](/powershell/azure/)을 설치해야 합니다.
* Azure CLI를 사용하려 하나 아직 설치하지 않은 경우 [Azure CLI 설치](/cli/azure/install-azure-cli)를 참조하세요.

> [!IMPORTANT]  
> Azure Virtual Network를 사용하여 HDInsight를 온-프레미스 네트워크에 연결하는 단계별 지침을 찾으려면 [온-프레미스 네트워크에 HDInsight 연결](connect-on-premises-network.md) 문서를 참조하세요.

## <a name="example-network-security-groups-with-hdinsight"></a><a id="hdinsight-nsg"></a>예제: HDInsight에서 네트워크 보안 그룹

이 섹션의 예제에서는 네트워크 보안 그룹 규칙을 만드는 방법을 보여 줍니다. 이러한 규칙을 통해 HDInsight는 Azure 관리 서비스와 통신할 수 있습니다. 예제를 사용하기 전에 사용 중인 Azure 지역과 일치하도록 IP 주소를 조정합니다. [HDInsight 관리 IP 주소](hdinsight-management-ip-addresses.md)에서 이 정보를 찾을 수 있습니다.

### <a name="azure-resource-manager-template"></a>Azure Resource Manager 템플릿

다음 리소스 관리 템플릿은 인바운드 트래픽을 제한하지만 HDInsight에 필요한 IP 주소에서의 트래픽은 허용하는 가상 네트워크를 생성합니다. 또한 이 템플릿은 가상 네트워크에 HDInsight 클러스터를 만듭니다.

* [보안 Azure Virtual Network 및 HDInsight Hadoop 클러스터 배포](https://azure.microsoft.com/resources/templates/hdinsight-secure-vnet/)

### <a name="azure-powershell"></a>Azure PowerShell

다음 PowerShell 스크립트를 사용하여 인바운드 트래픽을 제한하며 북유럽 지역의 IP 주소에서 전송되는 트래픽을 허용하는 가상 네트워크를 만듭니다.

> [!IMPORTANT]  
> 이 예제에 사용된 `hdirule1` 및 `hdirule2`의 IP 주소를 사용 중인 Azure 지역에 맞게 변경합니다. [HDInsight 관리 IP 주소](hdinsight-management-ip-addresses.md)에 대한 정보를 찾을 수 있습니다.

```azurepowershell
$vnetName = "Replace with your virtual network name"
$resourceGroupName = "Replace with the resource group the virtual network is in"
$subnetName = "Replace with the name of the subnet that you plan to use for HDInsight"

# Get the Virtual Network object
$vnet = Get-AzVirtualNetwork `
    -Name $vnetName `
    -ResourceGroupName $resourceGroupName

# Get the region the Virtual network is in.
$location = $vnet.Location

# Get the subnet object
$subnet = $vnet.Subnets | Where-Object Name -eq $subnetName

# Create a Network Security Group.
# And add exemptions for the HDInsight health and management services.
$nsg = New-AzNetworkSecurityGroup `
    -Name "hdisecure" `
    -ResourceGroupName $resourceGroupName `
    -Location $location `
    | Add-AzNetworkSecurityRuleConfig `
        -name "hdirule1" `
        -Description "HDI health and management address 52.164.210.96" `
        -Protocol "*" `
        -SourcePortRange "*" `
        -DestinationPortRange "443" `
        -SourceAddressPrefix "52.164.210.96" `
        -DestinationAddressPrefix "VirtualNetwork" `
        -Access Allow `
        -Priority 300 `
        -Direction Inbound `
    | Add-AzNetworkSecurityRuleConfig `
        -Name "hdirule2" `
        -Description "HDI health and management 13.74.153.132" `
        -Protocol "*" `
        -SourcePortRange "*" `
        -DestinationPortRange "443" `
        -SourceAddressPrefix "13.74.153.132" `
        -DestinationAddressPrefix "VirtualNetwork" `
        -Access Allow `
        -Priority 301 `
        -Direction Inbound `
    | Add-AzNetworkSecurityRuleConfig `
        -Name "hdirule3" `
        -Description "HDI health and management 168.61.49.99" `
        -Protocol "*" `
        -SourcePortRange "*" `
        -DestinationPortRange "443" `
        -SourceAddressPrefix "168.61.49.99" `
        -DestinationAddressPrefix "VirtualNetwork" `
        -Access Allow `
        -Priority 302 `
        -Direction Inbound `
    | Add-AzNetworkSecurityRuleConfig `
        -Name "hdirule4" `
        -Description "HDI health and management 23.99.5.239" `
        -Protocol "*" `
        -SourcePortRange "*" `
        -DestinationPortRange "443" `
        -SourceAddressPrefix "23.99.5.239" `
        -DestinationAddressPrefix "VirtualNetwork" `
        -Access Allow `
        -Priority 303 `
        -Direction Inbound `
    | Add-AzNetworkSecurityRuleConfig `
        -Name "hdirule5" `
        -Description "HDI health and management 168.61.48.131" `
        -Protocol "*" `
        -SourcePortRange "*" `
        -DestinationPortRange "443" `
        -SourceAddressPrefix "168.61.48.131" `
        -DestinationAddressPrefix "VirtualNetwork" `
        -Access Allow `
        -Priority 304 `
        -Direction Inbound `
    | Add-AzNetworkSecurityRuleConfig `
        -Name "hdirule6" `
        -Description "HDI health and management 138.91.141.162" `
        -Protocol "*" `
        -SourcePortRange "*" `
        -DestinationPortRange "443" `
        -SourceAddressPrefix "138.91.141.162" `
        -DestinationAddressPrefix "VirtualNetwork" `
        -Access Allow `
        -Priority 305 `
        -Direction Inbound `

# Set the changes to the security group
Set-AzNetworkSecurityGroup -NetworkSecurityGroup $nsg

# Apply the NSG to the subnet
Set-AzVirtualNetworkSubnetConfig `
    -VirtualNetwork $vnet `
    -Name $subnetName `
    -AddressPrefix $subnet.AddressPrefix `
    -NetworkSecurityGroup $nsg
$vnet | Set-AzVirtualNetwork
```

이 예제에서는 필요한 IP 주소에서 인바운드 트래픽을 허용하도록 규칙을 추가하는 방법을 보여 줍니다. 다른 소스에서 인바운드 액세스를 제한하는 규칙은 포함하지 않습니다. 다음 코드는 인터넷에서 SSH 액세스를 사용 설정하는 방법을 보여줍니다.

```azurepowershell
Get-AzNetworkSecurityGroup -Name hdisecure -ResourceGroupName RESOURCEGROUP |
Add-AzNetworkSecurityRuleConfig -Name "SSH" -Description "SSH" -Protocol "*" -SourcePortRange "*" -DestinationPortRange "22" -SourceAddressPrefix "*" -DestinationAddressPrefix "VirtualNetwork" -Access Allow -Priority 306 -Direction Inbound
```

### <a name="azure-cli"></a>Azure CLI

다음 단계에 따라 인바운드 트래픽을 제한하지만 HDInsight에 필요한 IP 주소에서의 트래픽은 허용하는 가상 네트워크를 만듭니다.

1. 다음 명령을 사용하여 `hdisecure`이라는 새 네트워크 보안 그룹을 만듭니다. `RESOURCEGROUP`을 Azure가상 네트워크가 포함된 리소스 그룹으로 바꿉니다. `LOCATION`을 그룹이 만들어진 위치(지역)로 바꿉니다.

    ```azurecli
    az network nsg create -g RESOURCEGROUP -n hdisecure -l LOCATION
    ```

    그룹을 만들면 새 그룹에 대한 정보를 받습니다.

2. 다음을 사용하여 Azure HDInsight 상태 및 관리 서비스에서 포트 443에 대한 인바운드 통신을 허용하는 새 네트워크 보안 그룹에 규칙을 추가합니다. `RESOURCEGROUP`을 Azure 가상 네트워크가 포함된 리소스 그룹의 이름으로 바꿉니다.

    > [!IMPORTANT]  
    > 이 예제에 사용된 `hdirule1` 및 `hdirule2`의 IP 주소를 사용 중인 Azure 지역에 맞게 변경합니다. [HDInsight 관리 IP 주소](hdinsight-management-ip-addresses.md)에서 이 정보를 찾을 수 있습니다.

    ```azurecli
    az network nsg rule create -g RESOURCEGROUP --nsg-name hdisecure -n hdirule1 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "52.164.210.96" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 300 --direction "Inbound"
    az network nsg rule create -g RESOURCEGROUP --nsg-name hdisecure -n hdirule2 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "13.74.153.132" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 301 --direction "Inbound"
    az network nsg rule create -g RESOURCEGROUP --nsg-name hdisecure -n hdirule3 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "168.61.49.99" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 302 --direction "Inbound"
    az network nsg rule create -g RESOURCEGROUP --nsg-name hdisecure -n hdirule4 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "23.99.5.239" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 303 --direction "Inbound"
    az network nsg rule create -g RESOURCEGROUP --nsg-name hdisecure -n hdirule5 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "168.61.48.131" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 304 --direction "Inbound"
    az network nsg rule create -g RESOURCEGROUP --nsg-name hdisecure -n hdirule6 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "138.91.141.162" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 305 --direction "Inbound"
    ```

3. 이 네트워크 보안 그룹에 대한 고유 식별자를 검색하려면 다음 명령을 사용합니다.

    ```azurecli
    az network nsg show -g RESOURCEGROUP -n hdisecure --query "id"
    ```

    이 명령은 다음 텍스트와 유사한 값을 반환합니다.

    ```output
    "/subscriptions/SUBSCRIPTIONID/resourceGroups/RESOURCEGROUP/providers/Microsoft.Network/networkSecurityGroups/hdisecure"
    ```

4. 다음 명령을 사용하여 네트워크 보안 그룹을 서브넷에 적용합니다. `GUID`과 `RESOURCEGROUP` 값을 이전 단계에서 반환된 값으로 대체합니다. `VNETNAME`과 `SUBNETNAME` 값을 만들려는 가상 네트워크 이름 및 서브넷 이름으로 바꿉니다.

    ```azurecli
    az network vnet subnet update -g RESOURCEGROUP --vnet-name VNETNAME --name SUBNETNAME --set networkSecurityGroup.id="/subscriptions/GUID/resourceGroups/RESOURCEGROUP/providers/Microsoft.Network/networkSecurityGroups/hdisecure"
    ```

    이 명령이 완료되면 Virtual Network에 HDInsight를 설치할 수 있습니다.

이러한 단계를 사용하면 Azure 클라우드의 HDInsight 상태 및 관리 서비스에 대한 액세스만 열립니다. Virtual Network 외부에서 HDInsight 클러스터에 대한 기타 액세스는 차단됩니다. 가상 네트워크 외부에서 액세스할 수 있도록 하려는 경우 네트워크 보안 그룹 규칙을 추가해야 합니다.

다음 코드는 인터넷에서 SSH 액세스를 사용 설정하는 방법을 보여줍니다.

```azurecli
az network nsg rule create -g RESOURCEGROUP --nsg-name hdisecure -n ssh --protocol "*" --source-port-range "*" --destination-port-range "22" --source-address-prefix "*" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 306 --direction "Inbound"
```

## <a name="example-dns-configuration"></a><a id="example-dns"></a> 예제: DNS 구성

### <a name="name-resolution-between-a-virtual-network-and-a-connected-on-premises-network"></a>가상 네트워크와 연결된 온-프레미스 네트워크 간에 이름 확인

이 예제에서는 다음과 같이 가정합니다.

* VPN 게이트웨이를 사용하여 온-프레미스 네트워크에 연결된 Azure Virtual Network가 있습니다.

* 가상 네트워크의 사용자 지정 DNS 서버는 운영 체제로 Linux 또는 Unix 실행합니다.

* [바인딩](https://www.isc.org/downloads/bind/)이 사용자 지정 DNS 서버에 설치됩니다.

가상 네트워크의 사용자 지정 DNS 서버에서:

1. Azure PowerShell 또는 Azure CLI를 사용하여 가상 네트워크의 DNS 접미사를 찾습니다.

    `RESOURCEGROUP`을 가상 네트워크가 포함된 리소스 그룹의 이름으로 바꾸고 명령을 입력합니다.

    ```azurepowershell
    $NICs = Get-AzNetworkInterface -ResourceGroupName "RESOURCEGROUP"
    $NICs[0].DnsSettings.InternalDomainNameSuffix
    ```

    ```azurecli
    az network nic list --resource-group RESOURCEGROUP --query "[0].dnsSettings.internalDomainNameSuffix"
    ```

1. 가상 네트워크에 대한 사용자 지정 DNS 서버에서 `/etc/bind/named.conf.local` 파일의 내용으로 다음 텍스트를 사용합니다.

    ```
    // Forward requests for the virtual network suffix to Azure recursive resolver
    zone "0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net" {
        type forward;
        forwarders {168.63.129.16;}; # Azure recursive resolver
    };
    ```

    `0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net` 값을 가상 네트워크의 DNS 접미사로 바꿉니다.

    이 구성은 가상 네트워크의 DNS 접미사에 대한 모든 DNS 요청을 Azure 재귀 확인자에 전달합니다.

1. 가상 네트워크에 대한 사용자 지정 DNS 서버에서 `/etc/bind/named.conf.options` 파일의 내용으로 다음 텍스트를 사용합니다.

    ```
    // Clients to accept requests from
    // TODO: Add the IP range of the joined network to this list
    acl goodclients {
        10.0.0.0/16; # IP address range of the virtual network
        localhost;
        localnets;
    };

    options {
            directory "/var/cache/bind";

            recursion yes;

            allow-query { goodclients; };

            # All other requests are sent to the following
            forwarders {
                192.168.0.1; # Replace with the IP address of your on-premises DNS server
            };

            dnssec-validation auto;

            auth-nxdomain no;    # conform to RFC1035
            listen-on { any; };
    };
    ```
    
    * `10.0.0.0/16` 값을 가상 네트워크의 IP 주소 범위로 바꿉니다. 이 항목으로 이 범위 내의 주소로 이름 확인 요청이 가능합니다.

    * 온-프레미스 네트워크의 IP 주소 범위를 `acl goodclients { ... }` 섹션에 추가합니다.  이 항목으로 온-프레미스 네트워크의 리소스에서 이름 확인 요청이 가능합니다.
    
    * `192.168.0.1` 값을 온-프레미스 DNS 서버의 IP 주소로 바꿉니다. 이 항목은 다른 모든 DNS 요청을 온-프레미스 DNS 서버에 라우팅합니다.

1. 구성을 사용하려면 바인딩을 다시 시작합니다. 예들 들어 `sudo service bind9 restart`입니다.

1. 온-프레미스 DNS 서버에 조건부 전달자를 추가합니다. 1단계에서 DNS 접미사에 대한 요청을 사용자 지정 DNS 서버에 보내도록 조건부 전달자를 구성합니다.

    > [!NOTE]  
    > 조건부 전달자를 추가하는 방법에 대한 구체적인 정보는 DNS 소프트웨어에 대한 설명서를 참조하세요.

다음 단계를 완료한 후 FQDN(정규화된 도메인 이름)을 사용하여 네트워크의 리소스에 연결할 수 있습니다. 이제 HDInsight를 가상 네트워크에 설치할 수 있습니다.

### <a name="name-resolution-between-two-connected-virtual-networks"></a>두 개의 연결된 가상 네트워크 간의 이름 확인

이 예제에서는 다음과 같이 가정합니다.

* VPN 게이트웨이 또는 피어링을 사용하여 연결된 두 Azure Virtual Networks가 있습니다.

* 두 네트워크의 사용자 지정 DNS 서버는 운영 체제로 Linux 또는 Unix를 실행합니다.

* [바인딩](https://www.isc.org/downloads/bind/)이 사용자 지정 DNS 서버에 설치됩니다.

1. Azure PowerShell 또는 Azure CLI를 사용하여 두 가상 네트워크의 DNS 접미사를 찾습니다.

    `RESOURCEGROUP`을 가상 네트워크가 포함된 리소스 그룹의 이름으로 바꾸고 명령을 입력합니다.

    ```azurepowershell
    $NICs = Get-AzNetworkInterface -ResourceGroupName "RESOURCEGROUP"
    $NICs[0].DnsSettings.InternalDomainNameSuffix
    ```

    ```azurecli
    az network nic list --resource-group RESOURCEGROUP --query "[0].dnsSettings.internalDomainNameSuffix"
    ```

2. 사용자 지정 DNS 서버에서 `/etc/bind/named.config.local` 파일의 내용으로 다음 텍스트를 사용합니다. 두 가상 네트워크의 사용자 지정 DNS 서버에서 다음과 같이 변경합니다.

    ```
    // Forward requests for the virtual network suffix to Azure recursive resolver
    zone "0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net" {
        type forward;
        forwarders {10.0.0.4;}; # The IP address of the DNS server in the other virtual network
    };
    ```

    `0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net` 값을 __다른__ 가상 네트워크의 DNS 접미사로 바꿉니다. 이 항목은 원격 네트워크의 DNS 접미사 요청을 해당 네트워크의 사용자 지정 DNS로 라우팅합니다.

3. 두 가상 네트워크의 사용자 지정 DNS 서버에서 `/etc/bind/named.conf.options` 파일의 내용으로 다음 텍스트를 사용합니다.

    ```
    // Clients to accept requests from
    acl goodclients {
        10.1.0.0/16; # The IP address range of one virtual network
        10.0.0.0/16; # The IP address range of the other virtual network
        localhost;
        localnets;
    };

    options {
            directory "/var/cache/bind";

            recursion yes;

            allow-query { goodclients; };

            forwarders {
            168.63.129.16;   # Azure recursive resolver
            };

            dnssec-validation auto;

            auth-nxdomain no;    # conform to RFC1035
            listen-on { any; };
    };
    ```

   `10.0.0.0/16` 및 `10.1.0.0/16` 값을 가상 네트워크의 IP 주소 범위로 바꿉니다. 이 항목으로 각 네트워크의 리소스가 DNS 서버에 요청할 수 있습니다.

    가상 네트워크의 DNS 접미사에 해당하지 않는 모든 요청(예: microsoft.com)은 Azure 재귀 확인자에 의해 처리됩니다.

4. 구성을 사용하려면 바인딩을 다시 시작합니다. 예를 들어, 두 DNS 서버에서 `sudo service bind9 restart`입니다.

다음 단계를 완료한 후 FQDN(정규화된 도메인 이름)을 사용하여 가상 네트워크의 리소스에 연결할 수 있습니다. 이제 HDInsight를 가상 네트워크에 설치할 수 있습니다.

## <a name="test-your-settings-before-deploying-an-hdinsight-cluster"></a>HDInsight 클러스터를 배포하기 전에 설정 테스트

클러스터를 배포하기 전에, 계획된 클러스터와 동일한 VNet 및 서브넷의 가상 머신에서 [networkValidator 도구](https://github.com/Azure-Samples/hdinsight-diagnostic-scripts/blob/main/HDInsightNetworkValidator)를 실행하여 많은 네트워크 구성 설정이 올바른지 확인할 수 있습니다.

**networkValidator.sh 스크립트를 실행하기 위해 가상 머신을 배포하려면**

1. [Azure Portal Ubuntu Server 18.04 LTS 페이지](https://portal.azure.com/?feature.customportal=false#create/Canonical.UbuntuServer1804LTS-ARM)를 열고 **만들기** 를 클릭합니다.

1. **기본 사항** 탭의 **프로젝트 세부 정보** 에서 구독을 선택하고, 기존 리소스 그룹을 선택하거나 새 리소스 그룹을 만듭니다.

    :::image type="content" source="./media/hdinsight-create-virtual-network/project-details.png" alt-text="가상 머신에 대한 Azure 구독 및 리소스 그룹을 선택하는 위치를 보여 주는 프로젝트 세부 정보 섹션의 스크린샷":::

1. **인스턴스 세부 정보** 아래에서 고유한 **가상 머신 이름** 을 입력하고, VNet과 동일한 **지역** 을 선택하고, **가용성 옵션** 으로 ‘인프라 중복이 필요하지 않습니다.’를 선택하고, **이미지** 로 *Ubuntu 18.04 LTS* 를 선택하고, **Azure Spot 인스턴스** 를 비워두고 **크기** 로 Standard_B1s(또는 그 이상)를 선택합니다.

    :::image type="content" source="./media/hdinsight-create-virtual-network/instance-details.png" alt-text="가상 머신의 이름을 입력하고 해당 지역, 이미지, 크기를 선택하는 인스턴스 세부 정보 섹션의 스크린샷":::

1. **관리자 계정** 에서 **암호** 를 선택하고 관리자 계정의 사용자 이름 및 암호를 입력합니다. 

    :::image type="content" source="./media/hdinsight-create-virtual-network/administrator-account.png" alt-text="인증 유형을 선택하고 관리자 자격 증명을 입력하는 관리자 계정 섹션의 스크린샷":::

1. **인바운드 포트 규칙** > **퍼블릭 인바운드 포트** 에서 **선택된 포트 허용** 을 선택한 다음, 드롭다운에서 **SSH(22)** 를 선택하고, **다음: 디스크 >** 를 선택합니다.

    :::image type="content" source="./media/hdinsight-create-virtual-network/inbound-port-rules.png" alt-text="인바운드 연결이 허용되는 포트를 선택하는 인바운드 포트 규칙 섹션의 스크린샷":::

1. **디스크 옵션** 에서 ‘OS 디스크 유형에 대해 표준 SSD’를 선택하고 **다음: 네트워킹 >** 을 클릭합니다.

1. **네트워킹** 페이지의 **네트워크 인터페이스** 아래에서 HDInsight 클러스터를 추가할 **가상 네트워크** 및 **서브넷** 을 선택한 다음, 페이지 하단에서 **검토 + 만들기** 단추를 선택합니다.

    :::image type="content" source="./media/hdinsight-create-virtual-network/vnet.png" alt-text="가상 머신을 추가할 VNet과 서브넷을 선택할 네트워크 인터페이스 섹션의 스크린샷":::

1. **가상 머신 만들기** 페이지에서 만들려는 VM의 세부 정보를 볼 수 있습니다. 준비가 되면 **만들기** 를 선택합니다.

1. 배포가 완료되면 **리소스로 이동** 을 선택합니다.

1. 새 VM에 대한 페이지에서 공용 IP 주소를 선택하고 클립보드에 복사합니다.

    :::image type="content" source="./media/hdinsight-create-virtual-network/ip-address.png" alt-text="가상 머신의 IP 주소를 복사하는 방법을 보여 주는 스크린샷":::

**/networkValidator.sh 스크립트 실행**

1. 새 가상 머신으로 SSH합니다.
1. 다음 명령을 사용하여 [GitHub](https://github.com/Azure-Samples/hdinsight-diagnostic-scripts/tree/main/HDInsightNetworkValidator)의 모든 파일을 가상 머신으로 복사합니다.

    `wget -i https://raw.githubusercontent.com/Azure-Samples/hdinsight-diagnostic-scripts/main/HDInsightNetworkValidator/all.txt`

1. 텍스트 편집기에서 params.txt 파일을 열고 모든 변수에 값을 추가합니다. 관련 유효성 검사를 생략하려면 빈 문자열("")을 사용합니다.
1. `sudo chmod +x ./setup.sh`를 실행하여 setup.sh를 실행 가능하게 만들고 `sudo ./setup.sh`와 함께 실행하여 Python 2.x용 pip를 설치하고 필요한 Python 2.x 모듈을 설치합니다.
1. `sudo python2 ./networkValidator.py`와 함께 주 스크립트를 실행합니다.
1. 스크립트가 완료되면 요약 섹션에는 검사가 성공했는지와 클러스터를 만들 수 있는지가 표시됩니다. 또는 문제가 발생했는지가 표시되는데, 이 경우 오류 출력 및 관련 설명서를 검토하여 오류를 해결해야 합니다.

    오류를 수정하려고 시도한 후에는 스크립트를 다시 실행하여 진행 상황을 확인할 수 있습니다.
1. 검사를 완료하고 요약에 “SUCCESS: You can create your HDInsight cluster in this VNet/Subnet.”(성공: 이 VNet/서브넷에 HDInsight 클러스터를 만들 수 있습니다.)이라는 메시지가 표시되면 클러스터를 만들 수 있습니다. 
1. 유효성 검사 스크립트 실행이 완료되면 새 가상 머신을 삭제합니다. 

## <a name="next-steps"></a>다음 단계

* 온-프레미스 네트워크에 연결하기 위해 HDInsight를 구성하는 완전한 예제는 [HDInsight를 온-프레미스 네트워크에 연결](./connect-on-premises-network.md)을 참조하세요.
* Azure 가상 네트워크의 HDInsight에서 Apache Hbase 클러스터를 구성하려면 [Azure 가상 네트워크의 HDInsight에서 Apache HBase 클러스터 만들기](hbase/apache-hbase-provision-vnet.md)를 참조하세요.
* Apache HBase 지리적 복제를 구성하려면 [Azure 가상 네트워크에서 Apache HBase 클러스터 복제 설정](hbase/apache-hbase-replication.md)을 참조하세요.
* Azure 가상 네트워크에 대한 자세한 내용은 [Azure Virtual Network 개요](../virtual-network/virtual-networks-overview.md)를 참조하세요.

* 네트워크 보안 그룹에 대한 자세한 내용은 [네트워크 보안 그룹](../virtual-network/network-security-groups-overview.md)을 참조하세요.

* 사용자 정의 경로에 대한 자세한 내용은 [사용자 정의 경로 및 IP 전달](../virtual-network/virtual-networks-udr-overview.md)을 참조하세요.
