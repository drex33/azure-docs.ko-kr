---
title: 프라이빗 엔드포인트로 Azure Key Vault 관리형 HSM 구성
description: Azure Private Link 서비스와 Azure Key Vault 관리형 HSM을 통합하는 방법을 알아봅니다.
author: mbaldwin
ms.author: mbaldwin
ms.date: 06/21/2021
ms.service: key-vault
ms.subservice: managed-hsm
ms.topic: how-to
ms.custom: devx-track-azurecli
ms.openlocfilehash: d9dff0d6d9d8421e160c19c60efc9e871d7867ed
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/22/2021
ms.locfileid: "114443577"
---
# <a name="integrate-managed-hsm-with-azure-private-link"></a>Azure Private Link와 관리형 HSM 통합

Azure Private Link 서비스를 사용하면 가상 네트워크의 프라이빗 엔드포인트를 통해 Azure 서비스(예: 관리형 HSM, Azure Storage, Azure Cosmos DB 등)와 Azure에서 호스트되는 고객/파트너 서비스에 액세스할 수 있습니다.

Azure 프라이빗 엔드포인트는 Azure Private Link에서 제공하는 서비스에 비공개로 안전하게 연결하는 네트워크 인터페이스입니다. 프라이빗 엔드포인트는 VNet의 개인 IP 주소를 사용하여 서비스를 VNet으로 효과적으로 가져옵니다. 서비스에 대한 모든 트래픽은 프라이빗 엔드포인트를 통해 라우팅할 수 있으므로 게이트웨이, NAT 디바이스, ExpressRoute 또는 VPN 연결 또는 공용 IP 주소가 필요하지 않습니다. 가상 네트워크와 서비스 간의 트래픽은 Microsoft 백본 네트워크를 통해 이동하여 공용 인터넷에서 노출을 제거합니다. Azure 리소스의 인스턴스에 연결하여 액세스 제어에서 가장 높은 수준의 세분성을 제공할 수 있습니다.

자세한 내용은 [Azure Private Link란?](../../private-link/private-link-overview.md)을 참조하세요.

## <a name="prerequisites"></a>필수 구성 요소

Azure Private Link와 관리형 HSM을 통합하려면 다음 항목이 필요합니다.

- 관리형 HSM. 자세한 내용은 [Azure CLI를 사용하여 관리형 HSM 프로비저닝 및 활성화](quick-create-cli.md)를 참조하세요.
- Azure 가상 네트워크
- 가상 네트워크의 서브넷
- 관리형 HSM과 가상 네트워크 둘 다에 대한 소유자 또는 기여자 권한
- Azure CLI 버전 2.25.0 이상 `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드가 필요한 경우, [Azure CLI 설치](/cli/azure/install-azure-cli)를 참조하세요.

프라이빗 엔드포인트와 가상 네트워크는 동일한 지역에 있어야 합니다. 포털을 사용하여 프라이빗 엔드포인트에 대한 영역을 선택하면 해당 지역에 있는 가상 네트워크만 자동으로 필터링됩니다. HSM은 다른 지역에 있을 수 있습니다.

프라이빗 엔드포인트는 가상 네트워크에서 개인 IP 주소를 사용합니다.


## <a name="establish-a-private-link-connection-to-managed-hsm-using-cli-initial-setup"></a>CLI를 사용하여 관리형 HSM에 대한 프라이빗 링크 연결 설정(초기 설정)

```azurecli
az login                                                                   # Login to Azure CLI
az account set --subscription {SUBSCRIPTION ID}                            # Select your Azure Subscription
az group create -n {RESOURCE GROUP} -l {REGION}                            # Create a new Resource Group
az provider register -n Microsoft.KeyVault                                 # Register KeyVault as a provider
az keyvault update-hsm --hsm-name {HSM NAME} -g {RG} --default-action deny # Turn on firewall

az network vnet create -g {RG} -n {vNet NAME} --location {REGION}           # Create a Virtual Network

    # Create a Subnet
az network vnet subnet create -g {RG} --vnet-name {vNet NAME} --name {subnet NAME} --address-prefixes {addressPrefix}

    # Disable Virtual Network Policies
az network vnet subnet update --name {subnet NAME} --resource-group {RG} --vnet-name {vNet NAME} --disable-private-endpoint-network-policies true

    # Create a Private DNS Zone
az network private-dns zone create --resource-group {RG} --name privatelink.managedhsm.azure.net

    # Link the Private DNS Zone to the Virtual Network
az network private-dns link vnet create --resource-group {RG} --virtual-network {vNet NAME} --zone-name privatelink.managedhsm.azure.net --name {dnsZoneLinkName} --registration-enabled true

```

### <a name="allow-trusted-services-to-access-managed-hsm"></a>신뢰할 수 있는 서비스가 관리형 HSM에 액세스하도록 허용

방화벽을 켜면 퍼블릭 인터넷과 Azure 서비스를 포함하여, 프라이빗 엔드포인트 연결을 사용하지 않는 위치에서의 HSM 액세스가 모두 거부됩니다. Microsoft 서비스가 관리형 HSM의 키에 액세스하도록 허용하려면 `--baypss AzureServices` 옵션을 사용합니다. 키에 액세스하려면 개별 엔터티(예: Azure Storage 계정 또는 Azure SQL Server)에 특정 역할이 할당되어 있어야 합니다. 

> [!NOTE]
> 신뢰할 수 있는 특정 서비스 사용 시나리오만 지원됩니다. 자세한 내용은 [신뢰할 수 있는 서비스 사용 시나리오 목록](../general/overview-vnet-service-endpoints.md#trusted-services)을 참조하세요.

```azurecli
az keyvault update-hsm --hsm-name {HSM NAME} -g {RG} --default-action deny --bypass AzureServices
```

### <a name="create-a-private-endpoint-automatically-approve"></a>프라이빗 엔드포인트 만들기(자동으로 승인) 
```azurecli
az network private-endpoint create --resource-group {RG} --vnet-name {vNet NAME} --subnet {subnet NAME} --name {Private Endpoint Name}  --private-connection-resource-id "/subscriptions/{AZURE SUBSCRIPTION ID}/resourceGroups/{RG}/providers/Microsoft.KeyVault/managedHSMs/{HSM NAME}" --group-id managedhsm --connection-name {Private Link Connection Name} --location {AZURE REGION}
```

> [!NOTE]
> 이 HSM을 삭제하면 프라이빗 엔드포인트의 작동이 중지됩니다. 나중에 이 HSM을 복구(삭제 취소)하는 경우 새 프라이빗 엔드포인트를 다시 만들어야 합니다.

### <a name="create-a-private-endpoint-manually-request-approval"></a>프라이빗 엔드포인트 만들기(수동으로 승인 요청) 
```azurecli
az network private-endpoint create --resource-group {RG} --vnet-name {vNet NAME} --subnet {subnet NAME} --name {Private Endpoint Name}  --private-connection-resource-id "/subscriptions/{AZURE SUBSCRIPTION ID}/resourceGroups/{RG}/providers/Microsoft.KeyVault/managedHSMs/{HSM NAME}" --group-id managedhsm --connection-name {Private Link Connection Name} --location {AZURE REGION} --manual-request
```

### <a name="manage-private-link-connections"></a>Private Link 연결 관리

```azurecli
# Show Connection Status
az network private-endpoint show --resource-group {RG} --name {Private Endpoint Name}

# Approve a Private Link Connection Request
az keyvault private-endpoint-connection approve --description {"OPTIONAL DESCRIPTION"} --resource-group {RG} --hsm-name {HSM NAME} –-name {PRIVATE LINK CONNECTION NAME}

# Deny a Private Link Connection Request
az keyvault private-endpoint-connection reject --description {"OPTIONAL DESCRIPTION"} --resource-group {RG} --hsm-name {HSM NAME} –-name {PRIVATE LINK CONNECTION NAME}

# Delete a Private Link Connection Request
az keyvault private-endpoint-connection delete --resource-group {RG} --hsm-name {HSM NAME} --name {PRIVATE LINK CONNECTION NAME}
```

### <a name="add-private-dns-records"></a>프라이빗 DNS 레코드 추가
```azurecli
# Determine the Private Endpoint IP address
az network private-endpoint show -g {RG} -n {PE NAME}      # look for the property networkInterfaces then id; the value must be placed on {PE NIC} below.
az network nic show --ids {PE NIC}                         # look for the property ipConfigurations then privateIpAddress; the value must be placed on {NIC IP} below.

# https://docs.microsoft.com/en-us/azure/dns/private-dns-getstarted-cli#create-an-additional-dns-record
az network private-dns zone list -g {RG}
az network private-dns record-set a add-record -g {RG} -z "privatelink.managedhsm.azure.net" -n {HSM NAME} -a {NIC IP}
az network private-dns record-set list -g {RG} -z "privatelink.managedhsm.azure.net"

# From home/public network, you wil get a public IP. If inside a vnet with private zone, nslookup will resolve to the private ip.
nslookup {HSM NAME}.managedhsm.azure.net
nslookup {HSM NAME}.privatelink.managedhsm.azure.net
```

---

## <a name="validate-that-the-private-link-connection-works"></a>프라이빗 링크 연결이 작동하는지 확인

프라이빗 엔드포인트 리소스와 동일한 서브넷에 있는 리소스가 개인 IP 주소를 통해 HSM에 연결하고, 프라이빗 DNS 영역이 올바르게 통합되었는지 확인해야 합니다.

먼저 [Azure Portal에서 Windows 가상 머신 만들기](../../virtual-machines/windows/quick-create-portal.md)의 단계에 따라 가상 머신을 만듭니다.

"네트워킹" 탭에서 다음을 수행합니다.

1. 가상 네트워크와 서브넷을 지정합니다. 새 가상 네트워크를 만들거나 기존 가상 네트워크를 선택할 수 있습니다. 기존 가상 네트워크를 선택하는 경우 지역이 일치하는지 확인합니다.
1. 공용 IP 리소스를 만듭니다.
1. "NIC 네트워크 보안 그룹"에서 "없음"을 선택합니다.
1. "부하 분산"에서 "아니요"를 선택합니다.

명령줄을 열고 다음 명령을 실행합니다.

```console
nslookup <your-HSM-name>.managedhsm.azure.net
```

ns lookup 명령을 실행하여 퍼블릭 엔드포인트를 통해 관리형 HSM의 IP 주소를 확인하는 경우 다음과 같은 결과가 표시됩니다.

```console
c:\ >nslookup <your-hsm-name>.managedhsm.azure.net

Non-authoritative answer:
Name:    
Address:  (public IP address)
Aliases:  <your-hsm-name>.managedhsm.azure.net
```

ns lookup 명령을 실행하여 프라이빗 엔드포인트를 통해 관리형 HSM의 IP 주소를 확인하는 경우 다음과 같은 결과가 표시됩니다.

```console
c:\ >nslookup your_hsm_name.managedhsm.azure.net

Non-authoritative answer:
Name:    
Address:  10.1.0.5 (private IP address)
Aliases:  <your-hsm-name>.managed.azure.net
          <your-hsm-name>.privatelink.managedhsm.azure.net
```

## <a name="troubleshooting-guide"></a>문제 해결 가이드

* 프라이빗 엔드포인트가 승인된 상태인지 확인합니다. 
    1. ```az keyvault private-endpoint-connections show``` 하위 명령을 사용하여 프라이빗 엔드포인트 연결의 상태를 확인합니다.
    2. 연결 상태가 [승인됨]이고 프로비저닝 상태가 [성공]인지 확인합니다. 
    3. 가상 네트워크가 사용 중인 가상 네트워크와 일치하는지 확인합니다.

* 프라이빗 DNS 영역 리소스가 있는지 확인합니다. 
    1. 이름이 정확하게 privatelink.managedhsm.azure.net인 프라이빗 DNS 영역 리소스가 있어야 합니다. 
    2. 설정 방법은 다음 링크를 참조하세요. [프라이빗 DNS 영역](../../dns/private-dns-privatednszone.md)
    
* 프라이빗 DNS 영역이 가상 네트워크에 연결되어 있는지 확인합니다. 반환된 공용 IP 주소를 계속 가져오는 경우에 문제가 발생할 수 있습니다. 
    1. 프라이빗 영역 DNS가 가상 네트워크에 연결되어 있지 않으면 가상 네트워크에서 시작되는 DNS 쿼리는 HSM의 공용 IP 주소를 반환합니다. 
    2. Azure Portal에서 프라이빗 DNS 영역 리소스로 이동하여 가상 네트워크 링크 옵션을 클릭합니다. 
    4. HSM을 호출할 가상 네트워크가 나열되어야 합니다. 
    5. 나열되지 않으면 추가합니다. 
    6. 자세한 단계는 [가상 네트워크를 프라이빗 DNS 영역에 연결](../../dns/private-dns-getstarted-portal.md#link-the-virtual-network) 문서를 참조하세요.

* 프라이빗 DNS 영역에서 HSM에 대한 A 레코드가 누락되지 않았는지 확인합니다. 
    1. 프라이빗 DNS 영역 페이지로 이동합니다. 
    2. 개요를 클릭하고 HSM의 간단한 이름을 사용하는 A 레코드가 있는지 확인합니다. 접미사를 지정하지 마세요.
    3. 철자를 확인하고 A 레코드를 만들거나 수정합니다. TTL 3600(1시간)을 사용할 수 있습니다. 
    4. 올바른 개인 IP 주소를 지정합니다. 
    
* A 레코드의 IP 주소가 올바른지 확인합니다. 
    1. Azure Portal에서 프라이빗 엔드포인트 리소스를 열어 IP 주소를 확인할 수 있습니다.
    2. Azure Portal에서 Microsoft.Network/privateEndpoints 리소스로 이동합니다.
    3. 개요 페이지에서 네트워크 인터페이스를 찾아 해당 링크를 클릭합니다. 
    4. 이 링크는 속성 개인 IP 주소를 포함하고 있는 NIC 리소스의 개요를 보여줍니다. 
    5. 이 주소가 A 레코드에 지정된 올바른 IP 주소인지 확인합니다.

## <a name="limitations-and-design-considerations"></a>제한 사항 및 디자인 고려 사항

> [!NOTE]
> 구독당 프라이빗 엔드포인트가 사용하도록 설정된 관리형 HSM 수는 조정 가능한 한도입니다. 아래에 표시된 제한은 기본 제한입니다. 구독에 대한 한도 증가를 요청하려면 Azure 지원 티켓을 만드세요. 이러한 요청은 사례별 기준으로 승인합니다.

**가격 책정**: 가격 책정 정보는 [Azure Private Link 가격 책정](https://azure.microsoft.com/pricing/details/private-link/)을 참조하세요.

**관리형 HSM당 최대 프라이빗 엔드포인트 수**: 64

**구독당 프라이빗 엔드포인트가 있는 기본 관리형 HSM 수**: 400

자세한 내용은 [Azure Private Link 서비스: 제한 사항](../../private-link/private-link-service-overview.md#limitations)을 참조하세요.

## <a name="next-steps"></a>다음 단계

- [Azure Private Link](../../private-link/private-link-service-overview.md)에 대해 자세히 알아봅니다.
- [관리형 HSM](overview.md)에 관해 자세히 알아봅니다.
