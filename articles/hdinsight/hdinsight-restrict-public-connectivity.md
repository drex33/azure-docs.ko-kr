---
title: Azure HDInsight 공용 연결 제한
description: 모든 아웃바운드 공용 IP 주소에 대한 액세스를 제거하는 방법 알아보기
ms.service: hdinsight
ms.topic: conceptual
ms.date: 09/20/2021
ms.openlocfilehash: c63cccd4f2331a9124dece37a1fd4b260f2b8621
ms.sourcegitcommit: 4abfec23f50a164ab4dd9db446eb778b61e22578
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2021
ms.locfileid: "130062826"
---
# <a name="restrict-public-connectivity-in-azure-hdinsight"></a>Azure HDInsight 공용 연결 제한

## <a name="overview"></a>개요
Azure HDInsight [기본 가상 네트워크 아키텍처에서](./hdinsight-virtual-network-architecture.md)HDInsight RP(리소스 공급자)는 공용 네트워크를 통해 클러스터와 통신합니다. 이 문서에서는 인바운드 연결이 프라이빗 네트워크로 제한되는 제한된 HDInsight 클러스터를 만드는 데 사용할 수 있는 고급 컨트롤에 대해 알아봅니다. HDInsight 클러스터 및 종속 리소스에 대한 공용 연결을 선택할 수 있는 경우 Azure HDInsight [네트워크 트래픽 제어의](./control-network-traffic.md)지침에 따라 클러스터의 연결을 제한하는 것이 좋습니다. 공용 연결을 제한하는 것 외에도 이러한 클러스터에서 사용하도록 구성할 수 있는 프라이빗 링크 사용 종속성 리소스에 대한 지원을 추가하고 있습니다.

다음 다이어그램에서는 가 아웃바운드 로 설정된 경우 잠재적인 HDInsight 가상 네트워크 아키텍처가 어떻게 표시되는지 보여줍니다. `resourceProviderConnection` 

:::image type="content" source="media/hdinsight-private-link/outbound-resource-provider-connection-only.png" alt-text="아웃바운드 리소스 공급자 연결을 사용하는 HDInsight 아키텍처의 다이어그램":::

> [!NOTE]
> 공용 연결을 제한하는 것은 Private Link 사용하도록 설정하기 위한 필수 조건이며 동일한 기능으로 간주해서는 안 됩니다.

## <a name="initialize-a-restricted-cluster"></a>제한된 클러스터 초기화

기본적으로 HDInsight 리소스 공급자는 공용 IP를 사용하여 클러스터에 *대한 인바운드* 연결을 사용합니다. 네트워크 `resourceProviderConnection` 속성이 *아웃바운드* 로 설정된 경우 클러스터 내부에서 리소스 공급자로 연결이 항상 시작되도록 HDInsight 리소스 공급자에 대한 연결을 되돌려 놓습니다. 이 구성에서는 인바운드 연결이 없으면 NSG(네트워크 보안 그룹)에서 인바운드 서비스 태그를 구성하거나 UDR(사용자 정의 경로)을 통해 방화벽/NVA(네트워크 가상 어플라이언스)를 무시할 필요가 없습니다.

클러스터를 만든 후 제한된 HDInsight 클러스터에 필요한 DNS 레코드를 추가하여 적절한 DNS 해상도를 설정해야 합니다. 다음 정식 이름 DNS 레코드(CNAME)는 Azure 관리 공용 DNS 영역에 만들어집니다. `azurehdinsight.net`

```dns
<clustername>    CNAME    <clustername>-int
```

클러스터 FQDN을 사용하여 클러스터에 액세스하려면 내부 부하 분산 장치를 직접 사용하거나 고유한 프라이빗 DNS 영역을 사용하여(이 경우 영역 이름은 이어야 `azurehdinsight.net` 합니다) 필요에 따라 클러스터 엔드포인트를 재정의할 수 있습니다. 예를 들어 프라이빗 DNS 영역 의 `azurehdinsight.net` 경우 필요에 따라 개인 IP를 추가할 수 있습니다.

```dns
<clustername>        A   10.0.0.1
<clustername-ssh>    A   10.0.0.2
```

> [!NOTE]
> 공용 연결이 사용되는 다른 클러스터와 동일한 VNet(의 프라이빗 DNS 영역 사용)에 제한된 클러스터를 사용하는 `azurehdinsight.net` 것은 의도하지 않은 DNS 확인 동작/충돌을 일으킬 수 있으므로 권장되지 않습니다.

DNS를 더 쉽게 설정할 수 있도록 클러스터 응답의 일부로 FQDN 및 해당 개인 IP 주소를 `GET` 반환합니다. 이 PowerShell 조각을 사용하여 시작할 수 있습니다.

```powershell
<#
    This script is offered as an example to help get started with automation and can be adjusted based on your needs.
    This script assumes:
    - HDInsight cluster has already been created and the context where this script is run has permissions to read/write resources in the same resource group.
    - Private DNS zone resource exists in the same subscription as the HDInsight cluster.
We recommend that you use the latest version of Az.HDInsight module

#>
$subscriptionId = "<Replace with subscription for deploying HDInsight clusters, and containing private DNS zone resource>"

$clusterName = "<Replace with cluster name>"
$clusterResourceGroupName = "<Replace with resource group name>"

# For example, azurehdinsight.net for public cloud.
$dnsZoneName = "<Replace with private DNS zone name>"
$dnsZoneResourceGroupName = "<Replace with private DNS zone resource group name>"

Connect-AzAccount -SubscriptionId $subscriptionId

# 1. Get cluster endpoints
$clusterEndpoints = $(Get-AzHDInsightCluster -ClusterName $clusterName ` -ResourceGroupName $clusterResourceGroupName).ConnectivityEndpoints

$endpointMapping = @{}

foreach($endpoint in $clusterEndpoints)
{
    $label = $endpoint.Location.ToLower().Replace(".$dnsZoneName".ToLower(), "")
    $ip = $endpoint.PrivateIPAddress

    $endpointMapping.Add($label, $ip)
}

# 2. Confirm DNS zone exists
Get-AzPrivateDnsZone -ResourceGroupName $dnsZoneResourceGroupName -Name $dnsZoneName -ErrorAction Stop

# 3. Update DNS entries for the cluster in the private DNS zone
#    - If the entries already exist, update to the new IP
#    - If the entries do not exist, create them
$recordSets = Get-AzPrivateDnsRecordSet -ZoneName $dnsZoneName -ResourceGroupName $dnsZoneResourceGroupName -RecordType A

foreach($label in $endpointMapping.Keys)
{
    $updateRecord = $null

    foreach($record in $recordSets)
    {
        if($record.Name -eq $label)
        {
            $updateRecord = $record
            break;
        }
        
    }

    if($null -ne $updateRecord)
    {
        $updateRecord.Records[0].Ipv4Address = $endpointMapping[$label]
        Set-AzPrivateDnsRecordSet -RecordSet $updateRecord | Out-Null
    }
    else
    {
        New-AzPrivateDnsRecordSet `
            -ResourceGroupName $dnsZoneResourceGroupName `
            -ZoneName $dnsZoneName `
            -Name $label `
            -RecordType A `
            -Ttl 3600 `
            -PrivateDnsRecord (New-AzPrivateDnsRecordConfig -Ipv4Address $endpointMapping[$label]) | Out-Null
    }
}

```

## <a name="adding-private-link-connectivity-private-endpoints-to-cluster-dependent-resources-optional"></a>클러스터 종속 리소스에 프라이빗 링크 연결(프라이빗 엔드포인트) 추가(선택 사항)

`resourceProviderConnection` *아웃바운드로* 구성하면 Storage(Azure Data Lake Storage Gen2 및 Windows Azure Storage Blob), SQL 메타 저장소(Apache Ranger, Ambari, Oozie 및 Hive) 및 프라이빗 엔드포인트를 사용하여 Azure Key Vault 같은 클러스터별 리소스에 액세스할 수 있습니다. 이러한 리소스에 프라이빗 엔드포인트를 반드시 사용하는 것은 아니지만 이러한 리소스에 프라이빗 엔드포인트를 사용하려는 경우 HDInsight 클러스터를 만들기 전에 이러한 리소스를 만들고 프라이빗 엔드포인트 및 DNS 항목을 구성해야 합니다. 이러한 모든 리소스는 프라이빗 엔드포인트를 통해 또는 다른 엔드포인트를 통해 클러스터 서브넷 내에서 액세스할 수 있어야 합니다.
프라이빗 엔드포인트를 통해 Azure Data Lake Storage Gen2에 연결할 때에는 Gen2 스토리지 계정에 ‘blob’ 및 ‘dfs’ 모두에 대한 엔드포인트 세트가 있어야 합니다. 자세한 내용은 [프라이빗 엔드포인트 만들기를](../private-link/create-private-endpoint-portal.md)참조하세요.

## <a name="using-firewall-optional"></a>방화벽 사용(선택 사항)
HDInsight 클러스터는 여전히 퍼블릭 인터넷에 연결하여 아웃바운드 의존도를 얻을 수 있습니다. 더 제한하려는 경우 [방화벽을 구성할](./hdinsight-restrict-outbound-traffic.md)수 있지만 요구 사항은 아닙니다.

## <a name="how-to-create-clusters"></a>클러스터를 만드는 방법
### <a name="use-arm-template-properties"></a>ARM 템플릿 속성 사용

다음 JSON 코드 조각에는 프라이빗 HDInsight 클러스터를 만들기 위해 ARM 템플릿에서 구성해야 하는 두 가지 네트워크 속성이 있습니다.

```json
networkProperties: {
    "resourceProviderConnection": "Inbound" | "Outbound"
}
```

Private Link를 포함하여 많은 HDInsight 엔터프라이즈 보안 기능이 있는 전체 템플릿은 [HDInsight 엔터프라이즈 보안 템플릿](https://github.com/Azure-Samples/hdinsight-enterprise-security/tree/main/ESP-HIB-PL-Template)을 참조하세요.

### <a name="use-azure-powershell"></a>Azure PowerShell 사용

PowerShell을 사용하려면 [여기](/powershell/module/az.hdinsight/new-azhdinsightcluster#example-4--create-an-azure-hdinsight-cluster-with-relay-outbound-and-private-link-feature)에서 예제를 참조하세요.

### <a name="use-azure-cli"></a>Azure CLI 사용
Azure CLI를 사용하려면 [여기](/cli/azure/hdinsight#az_hdinsight_create-examples)에서 예제를 참조하세요.

## <a name="next-steps"></a>다음 단계

* [클러스터에서 Private Link 사용](./hdinsight-private-link.md)
* [Azure HDInsight용 Enterprise Security Package](enterprise-security-package.md)
* [Azure HDInsight의 엔터프라이즈 보안 일반 정보 및 지침](./domain-joined/general-guidelines.md)
