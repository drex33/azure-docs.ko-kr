---
title: Azure HDInsight 공용 연결 제한
description: 모든 아웃바운드 공용 IP 주소에 대한 액세스를 제거하는 방법을 알아봅니다.
ms.service: hdinsight
ms.topic: conceptual
ms.date: 09/20/2021
ms.openlocfilehash: 6cc4a73c08d945f5a5ce26cef9c3774ffb5772d0
ms.sourcegitcommit: 37cc33d25f2daea40b6158a8a56b08641bca0a43
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2021
ms.locfileid: "130069143"
---
# <a name="restrict-public-connectivity-in-azure-hdinsight"></a>Azure HDInsight 공용 연결 제한

Azure HDInsight [기본 가상 네트워크 아키텍처에서](./hdinsight-virtual-network-architecture.md) HDInsight 리소스 공급자는 공용 네트워크를 통해 클러스터와 통신합니다. 이 문서에서는 인바운드 연결이 프라이빗 네트워크로 제한되는 제한된 HDInsight 클러스터를 만드는 데 사용할 수 있는 고급 컨트롤에 대해 알아봅니다. 

HDInsight 클러스터와 종속 리소스 간에 공용 연결을 원하는 경우 [Azure HDInsight 네트워크 트래픽 제어의](./control-network-traffic.md)지침에 따라 클러스터의 연결을 제한하는 것이 좋습니다. 공용 연결을 제한하는 것 외에도 HDInsight 클러스터에서 사용할 Azure Private Link 지원 종속성 리소스를 구성할 수 있습니다.

다음 다이어그램은 가 아웃바운드 로 설정된 경우 잠재적인 HDInsight 가상 네트워크 아키텍처가 어떻게 표시되는지 보여줍니다. `resourceProviderConnection` 

:::image type="content" source="media/hdinsight-private-link/outbound-resource-provider-connection-only.png" alt-text="아웃바운드 리소스 공급자 연결을 사용하는 HDInsight 아키텍처의 다이어그램.":::

> [!NOTE]
> 공용 연결을 제한하는 것은 Private Link 사용하도록 설정하기 위한 필수 조건이며 동일한 기능으로 간주해서는 안 됩니다.

## <a name="initialize-a-restricted-cluster"></a>제한된 클러스터 초기화

기본적으로 HDInsight 리소스 공급자는 공용 IP 주소를 사용하여 클러스터에 *대한 인바운드* 연결을 사용합니다. 네트워크 `resourceProviderConnection` 속성이 *아웃바운드* 로 설정된 경우 클러스터 내에서 연결이 항상 시작되고 리소스 공급자로 이동되도록 HDInsight 리소스 공급자에 대한 연결을 반대로 바칩니다. 

이 구성에서는 인바운드 연결이 없으면 네트워크 보안 그룹에서 인바운드 서비스 태그를 구성할 필요가 없습니다. 또한 사용자 정의 경로를 통해 방화벽 또는 네트워크 가상 어플라이언스도 무시할 필요가 없습니다.

클러스터를 만든 후 제한된 HDInsight 클러스터에 필요한 DNS 레코드를 추가하여 적절한 DNS 확인 을 설정합니다. 다음 정식 이름 DNS 레코드(CNAME)는 Azure 관리 공용 DNS 영역에 `azurehdinsight.net` 만들어집니다.

```dns
<clustername>    CNAME    <clustername>-int
```

FQDN(정규화된 도메인 이름)을 사용하여 클러스터에 액세스하려면 다음 방법 중 하나를 필요에 맞게 사용할 수 있습니다.

- 내부 부하 분산의 개인 IP 주소를 직접 사용합니다.
- 사용자 고유의 프라이빗 DNS 영역을 사용하여 클러스터 엔드포인트를 재정의합니다. 이 경우 영역 이름은 이어야 `azurehdinsight.net` 합니다.

예를 들어 프라이빗 DNS 영역 의 경우 `azurehdinsight.net` 필요에 따라 개인 IP 주소를 추가할 수 있습니다.

```dns
<clustername>        A   10.0.0.1
<clustername-ssh>    A   10.0.0.2
```

> [!NOTE]
> 공용 연결이 활성화된 다른 클러스터와 동일한 가상 네트워크(에 대한 프라이빗 DNS 영역 사용)에 제한된 클러스터를 배치하지 않는 것이 `azurehdinsight.net` 좋습니다. 의도하지 않은 DNS 확인 동작 또는 충돌이 발생할 수 있습니다.

DNS를 더 쉽게 설정할 수 있도록 클러스터 응답의 일부로 FQDN 및 해당 개인 IP 주소를 `GET` 반환합니다. 이 PowerShell 조각을 사용하여 시작할 수 있습니다.

```powershell
<#
    This script is an example to help you get started with automation and can be adjusted based on your needs.
    This script assumes:
    - The HDInsight cluster has already been created, and the context where this script is run has permissions to read/write resources in the same resource group.
    - The private DNS zone resource exists in the same subscription as the HDInsight cluster.
We recommend that you use the latest version of the Az.HDInsight module.

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

# 2. Confirm that the DNS zone exists.
Get-AzPrivateDnsZone -ResourceGroupName $dnsZoneResourceGroupName -Name $dnsZoneName -ErrorAction Stop

# 3. Update DNS entries for the cluster in the private DNS zone:
#    - If the entries already exist, update to the new IP.
#    - If the entries don't exist, create them.
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

## <a name="add-private-link-connectivity-to-cluster-dependent-resources-optional"></a>클러스터 종속 리소스에 Private Link 연결 추가(선택 사항)

`resourceProviderConnection` *아웃바운드를* 구성하면 프라이빗 엔드포인트를 사용하여 클러스터별 리소스에 액세스할 수도 있습니다. 해당 리소스는 다음과 같습니다.

- Storage: Azure Data Lake Storage Gen2 및 Azure Blob Storage
- SQL 메타스토어: Apache Ranger, Ambari, Oozie 및 Hive
- Azure Key Vault 

이러한 리소스에 프라이빗 엔드포인트를 반드시 사용하는 것은 아닙니다. 그러나 이러한 리소스에 프라이빗 엔드포인트를 사용하려는 경우 HDInsight 클러스터를 만들기 전에 리소스를 만들고 프라이빗 엔드포인트 및 DNS 항목을 구성해야 합니다. 이러한 모든 리소스는 프라이빗 엔드포인트를 통해 또는 다른 엔드포인트를 통해 클러스터 서브넷 내에서 액세스할 수 있어야 합니다.

프라이빗 엔드포인트를 통해 Azure Data Lake Storage Gen2에 연결하는 경우 Gen2 스토리지 계정에 및 둘 다에 대한 엔드포인트 집합이 있는지 `blob` `dfs` 확인합니다. 자세한 내용은 [프라이빗 엔드포인트 만들기를 참조하세요.](../private-link/create-private-endpoint-portal.md)

## <a name="use-a-firewall-optional"></a>방화벽 사용(선택 사항)
HDInsight 클러스터는 여전히 공용 인터넷에 연결하여 아웃바운드 의존도를 얻을 수 있습니다. 액세스를 제한하려는 경우 [방화벽을 구성할](./hdinsight-restrict-outbound-traffic.md)수 있지만 요구 사항은 아닙니다.

## <a name="create-clusters"></a>클러스터 만들기

다음 JSON 코드 조각에는 프라이빗 HDInsight 클러스터를 만들기 위해 Azure Resource Manager 템플릿에서 구성해야 하는 두 가지 네트워크 속성이 포함되어 있습니다.

```json
networkProperties: {
    "resourceProviderConnection": "Inbound" | "Outbound"
}
```

Private Link를 포함하여 많은 HDInsight 엔터프라이즈 보안 기능이 있는 전체 템플릿은 [HDInsight 엔터프라이즈 보안 템플릿](https://github.com/Azure-Samples/hdinsight-enterprise-security/tree/main/ESP-HIB-PL-Template)을 참조하세요.

PowerShell을 사용하여 클러스터를 만들려면 [예제를](/powershell/module/az.hdinsight/new-azhdinsightcluster#example-4--create-an-azure-hdinsight-cluster-with-relay-outbound-and-private-link-feature)참조하세요.

Azure CLI 사용하여 클러스터를 만들려면 [예제를 참조하세요.](/cli/azure/hdinsight#az_hdinsight_create-examples)

## <a name="next-steps"></a>다음 단계

* [클러스터에서 Private Link 사용](./hdinsight-private-link.md)
* [Azure HDInsight용 Enterprise Security Package](enterprise-security-package.md)
* [Azure HDInsight 엔터프라이즈 보안에 대한 일반 정보 및 지침](./domain-joined/general-guidelines.md)
