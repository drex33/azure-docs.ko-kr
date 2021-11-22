---
title: 관리형 가상 네트워크 및 관리형 프라이빗 엔드포인트
description: Azure Data Factory의 관리형 가상 네트워크 및 관리형 프라이빗 엔드포인트에 대해 알아봅니다.
ms.author: lle
author: lrtoyou1223
ms.service: data-factory
ms.subservice: integration-runtime
ms.topic: conceptual
ms.custom: seo-lt-2019, references_regions, devx-track-azurepowershell
ms.date: 09/28/2021
ms.openlocfilehash: 179260306a7f590f93c7713d0e61cdee8c4b46f9
ms.sourcegitcommit: 6f30424a4ab8dffc4e690086e898ab52bc4da777
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/22/2021
ms.locfileid: "132902025"
---
# <a name="azure-data-factory-managed-virtual-network"></a>Azure Data Factory 관리형 가상 네트워크

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

이 문서에서는 Azure Data Factory의 관리형 가상 네트워크 및 관리형 프라이빗 엔드포인트에 대해 설명합니다.


## <a name="managed-virtual-network"></a>관리형 가상 네트워크

Azure Data Factory 관리형 가상 네트워크(VNET) 내에 Azure IR(Integration Runtime)을 만들면 통합 런타임은 관리형 가상 네트워크로 프로비저닝되며 프라이빗 엔드포인트를 활용하여 지원되는 데이터 저장소에 안전하게 연결합니다. 

관리형 가상 네트워크 내에 Azure IR을 만들면 데이터 통합 프로세스가 격리되고 보호됩니다. 

관리형 가상 네트워크 사용의 이점

- 관리형 가상 네트워크를 사용하면 Azure Data Factory에 대한 가상 네트워크를 관리하는 부담에서 벗어날 수 있습니다. 결과적으로 가상 네트워크에서 많은 개인 IP를 사용할 수 있고 사전 네트워크 인프라 계획이 필요한 Azure Integration Runtime에 대해 서브넷을 만들 필요가 없습니다. 
- Azure 네트워킹에 대한 깊이 있는 지식이 없어도 데이터 통합을 안전하게 수행할 수 있습니다. 대신 보안 ETL을 시작하는 것이 데이터 엔지니어에게 훨씬 간단합니다. 
- 관리형 프라이빗 엔드포인트와 함께 관리형 가상 네트워크는 데이터 반출을 방지합니다. 

> [!IMPORTANT]
>현재, 관리형 Virtual Network는 Azure Data Factory 지역과 동일한 지역에서만 지원됩니다.

> [!Note]
>기존의 글로벌 Azure integration runtime은 Azure Data Factory 관리 가상 네트워크에서 Azure integration runtime으로 전환할 수 없으며 그 반대의 경우도 마찬가지입니다.
 

:::image type="content" source="./media/managed-vnet/managed-vnet-architecture-diagram.png" alt-text="ADF 관리형 가상 네트워크 아키텍처":::

## <a name="managed-private-endpoints"></a>관리형 프라이빗 엔드포인트

관리형 프라이빗 엔드포인트는 Azure Data Factory 관리형 가상 네트워크에 생성되는 프라이빗 엔드포인트이며, Azure 리소스에 대한 프라이빗 링크를 설정합니다. Azure Data Factory는 사용자 대신 이러한 프라이빗 엔드포인트를 관리합니다. 

:::image type="content" source="./media/tutorial-copy-data-portal-private/new-managed-private-endpoint.png" alt-text="새 관리형 프라이빗 엔드포인트":::

Azure Data Factory는 프라이빗 링크를 지원합니다. 프라이빗 링크를 사용하면 Azure(PaaS) 서비스(예: Azure Storage, Azure Cosmos DB, Azure Synapse Analytics)에 액세스할 수 있습니다.

프라이빗 링크를 사용하면 데이터 저장소와 관리형 가상 네트워크 간의 트래픽이 전적으로 Microsoft 백본 네트워크를 통해 트래버스됩니다. Private Link는 데이터 반출 위험을 방지합니다. 프라이빗 엔드포인트를 만들어 리소스에 대한 프라이빗 링크를 설정합니다.

프라이빗 엔드포인트는 관리형 가상 네트워크의 개인 IP 주소를 사용하여 서비스를 효과적으로 가져옵니다. 프라이빗 엔드포인트는 전체 서비스가 아닌 Azure의 특정 리소스에 매핑됩니다. 고객은 연결 범위를 조직에서 승인한 특정 리소스로 제한할 수 있습니다. [프라이빗 링크 및 프라이빗 엔드포인트](../private-link/index.yml)에 대해 자세히 알아보세요.

> [!NOTE]
> 모든 Azure 데이터 원본에 연결하는 관리형 프라이빗 엔드포인트를 만드는 것이 좋습니다. 
 
> [!WARNING]
> PaaS 데이터 저장소(Blob, ADLS Gen2, Azure Synapse Analytics)에 프라이빗 엔드포인트가 이미 만들어져 있고 모든 네트워크의 액세스를 허용하는 경우에도 ADF는 관리형 프라이빗 엔드포인트를 통해서만 액세스할 수 있습니다. 프라이빗 엔드포인트가 아직 없는 경우 해당되는 시나리오로 하나 생성해야 합니다. 

프라이빗 엔드포인트 연결은 Azure Data Factory에서 관리형 프라이빗 엔드포인트를 만들 때 “보류 중” 상태로 생성됩니다. 승인 워크플로가 시작됩니다. 프라이빗 링크 리소스 소유자가 연결을 승인 또는 거부합니다.

:::image type="content" source="./media/tutorial-copy-data-portal-private/manage-private-endpoint.png" alt-text="프라이빗 엔드포인트 관리":::

소유자가 연결을 승인하면 프라이빗 링크가 설정됩니다. 거부하면 프라이빗 링크가 설정되지 않습니다. 어떤 경우든 관리형 프라이빗 엔드포인트는 연결 상태로 업데이트 됩니다.

:::image type="content" source="./media/tutorial-copy-data-portal-private/approve-private-endpoint.png" alt-text="관리형 프라이빗 엔드포인트 승인":::

승인된 상태의 관리형 프라이빗 엔드포인트만이 지정된 프라이빗 링크 리소스에 트래픽을 보낼 수 있습니다.

## <a name="interactive-authoring"></a>대화형 작성
대화형 작성 기능은 연결 테스트, 폴더 목록 및 테이블 목록 찾아보기, 스키마 가져오기, 데이터 미리 보기 등의 기능에 사용됩니다. ADF 관리형 가상 네트워크에 있는 Azure Integration Runtime을 만들거나 편집할 때 대화형 작성을 사용하도록 설정할 수 있습니다. 백 엔드 서비스는 대화형 작성 기능에 대한 컴퓨팅을 미리 할당합니다. 그러지 않으면 대화형 작업이 수행될 때마다 컴퓨팅이 할당되어 더 많은 시간이 걸립니다. 대화형 작성의 TTL(Time To Live)은 60분입니다. 즉, 마지막 대화형 작성 작업 60분 후에 자동으로 사용하지 않도록 설정됩니다.

:::image type="content" source="./media/managed-vnet/interactive-authoring.png" alt-text="대화형 작성":::

## <a name="activity-execution-time-using-managed-virtual-network"></a>관리형 가상 네트워크를 사용한 작업 실행 시간
기본적으로 관리 되는 가상 네트워크의 Azure integration runtime은 데이터 팩터리에서 하나의 계산 노드를 예약 하지 않으므로 글로벌 Azure integration runtime 보다 큐 시간을 더 많이 차지 하므로 각 작업을 시작할 준비를 하 고 Azure integration runtime이 아닌 가상 네트워크 조인에서 주로 발생 합니다. 파이프라인 작업 및 외부 작업을 포함한 복사가 아닌 작업의 경우 처음 트리거할 때 60분의 TTL(Time To Live)이 있습니다. TTL 내에서는 노드가 이미 준비되어 있으므로 큐 시간이 단축됩니다. 
> [!NOTE]
> 복사 작업은 아직 TTL을 지원하지 않습니다.

> [!NOTE]
> 2 관리 가상 네트워크에서는 복사 작업을 위한 DIU가 지원 되지 않습니다.

## <a name="create-managed-virtual-network-via-azure-powershell"></a>Azure PowerShell을 통해 관리형 가상 네트워크 만들기
```powershell
$subscriptionId = ""
$resourceGroupName = ""
$factoryName = ""
$managedPrivateEndpointName = ""
$integrationRuntimeName = ""
$apiVersion = "2018-06-01"
$privateLinkResourceId = ""

$vnetResourceId = "subscriptions/${subscriptionId}/resourceGroups/${resourceGroupName}/providers/Microsoft.DataFactory/factories/${factoryName}/managedVirtualNetworks/default"
$privateEndpointResourceId = "subscriptions/${subscriptionId}/resourceGroups/${resourceGroupName}/providers/Microsoft.DataFactory/factories/${factoryName}/managedVirtualNetworks/default/managedprivateendpoints/${managedPrivateEndpointName}"
$integrationRuntimeResourceId = "subscriptions/${subscriptionId}/resourceGroups/${resourceGroupName}/providers/Microsoft.DataFactory/factories/${factoryName}/integrationRuntimes/${integrationRuntimeName}"

# Create managed Virtual Network resource
New-AzResource -ApiVersion "${apiVersion}" -ResourceId "${vnetResourceId}" -Properties @{}

# Create managed private endpoint resource
New-AzResource -ApiVersion "${apiVersion}" -ResourceId "${privateEndpointResourceId}" -Properties @{
        privateLinkResourceId = "${privateLinkResourceId}"
        groupId = "blob"
    }

# Create integration runtime resource enabled with VNET
New-AzResource -ApiVersion "${apiVersion}" -ResourceId "${integrationRuntimeResourceId}" -Properties @{
        type = "Managed"
        typeProperties = @{
            computeProperties = @{
                location = "AutoResolve"
                dataFlowProperties = @{
                    computeType = "General"
                    coreCount = 8
                    timeToLive = 0
                }
            }
        }
        managedVirtualNetwork = @{
            type = "ManagedVirtualNetworkReference"
            referenceName = "default"
        }
    }

```
> [!Note]
> 다른 데이터 원본의 **groupId** 의 경우 [개인 링크 리소스](https://docs.microsoft.com/azure/private-link/private-endpoint-overview#private-link-resource)에서 가져올 수 있습니다.

## <a name="limitations-and-known-issues"></a>제한 사항 및 알려진 문제

### <a name="supported-data-sources"></a>지원되는 데이터 원본
다음 데이터 원본에는 기본 개인 끝점이 지원 되며 ADF 관리 Virtual Network의 개인 링크를 통해 연결할 수 있습니다.
- Azure Blob Storage(스토리지 계정 V1 포함 안 함)
- Azure Cognitive Search
- Azure Cosmos DB SQL API
- Azure Data Lake Storage Gen2
- Azure Database for MariaDB
- Azure Database for MySQL
- Azure Database for PostgreSQL
- Azure Files(스토리지 계정 V1 포함 안 함)
- Azure Key Vault
- Azure Machine Learning
- Azure Private Link 서비스
- Azure Purview
- Azure SQL Database(Azure SQL Managed Instance 포함 안 함)
- Azure Synapse Analytics
- Azure Table Storage(스토리지 계정 V1 포함 안 함)

> [!Note]
> 공용 네트워크를 통해 Data Factory에서 지 원하는 모든 데이터 원본에 계속 액세스할 수 있습니다.

> [!NOTE]
> Azure SQL Managed Instance는 현재 기본 프라이빗 엔드포인트를 지원하지 않으므로 프라이빗 연결된 서비스 및 부하 분산 장치를 사용하여 관리되는 Virtual Network에서 액세스할 수 있습니다. [프라이빗 엔드포인트를 사용하여 Data Factory 관리형 VNET에서 SQL Managed Instance에 액세스하는 방법](tutorial-managed-virtual-network-sql-managed-instance.md)을 참조하세요.

### <a name="on-premises-data-sources"></a>온-프레미스 데이터 원본
개인 끝점을 사용 하 여 관리 Virtual Network에서 온-프레미스 데이터 원본에 액세스 하려면 [개인 끝점을 사용 하 Data Factory 관리 되는 VNET에서 온-프레미스 SQL Server에 액세스 하는 방법](tutorial-managed-virtual-network-on-premise-sql-server.md)자습서를 참조 하세요.

### <a name="azure-data-factory-managed-virtual-network-is-available-in-the-following-azure-regions"></a>다음 Azure 지역에서 Azure Data Factory 관리 Virtual Network를 사용할 수 있습니다.
일반적으로 관리 되는 가상 네트워크는 다음과 같은 경우를 제외 하 고 모든 Azure Data Factory 지역에서 사용할 수 있습니다.
- 인도 남부


### <a name="outbound-communications-through-public-endpoint-from-adf-managed-virtual-network"></a>ADF 관리형 가상 네트워크의 퍼블릭 엔드포인트를 통한 아웃바운드 통신
- 아웃바운드 통신을 위해 모든 포트가 열립니다.
- Azure Storage 및 Azure Data Lake Gen2는 ADF 관리형 가상 네트워크에서 공용 엔드포인트를 통해 연결할 수 없습니다.

### <a name="linked-service-creation-of-azure-key-vault"></a>Azure Key Vault의 연결된 서비스 만들기 
- Azure Key Vault에 대한 연결된 서비스를 만들 때 Azure Integration Runtime 참조가 없습니다. 따라서 Azure Key Vault의 연결된 서비스를 만드는 동안에는 프라이빗 엔드포인트를 만들 수 없습니다. 그러나 Azure Key Vault 연결된 서비스를 참조하는 데이터 저장소에 대한 연결된 서비스를 만들 때 이 연결된 서비스는 관리형 가상 네트워크가 사용하도록 설정된 Azure Integration Runtime을 참조하면 만드는 동안 Azure Key Vault 연결된 서비스에 대한 프라이빗 엔드포인트를 만들 수 있습니다. 
- Azure Key Vault의 연결된 서비스에 대한 **연결 테스트** 작업은 URL 형식에 대해서만 유효성을 검사하고 네트워크 작업은 수행하지 않습니다.
- **프라이빗 엔드포인트 사용** 열은 Azure Key Vault의 프라이빗 엔드포인트를 만드는 경우에도 항상 빈 값으로 표시됩니다.

### <a name="linked-service-creation-of-azure-hdi"></a>Azure HDI의 연결된 서비스 만들기
- 프라이빗 링크 서비스 및 부하 분산 장치와 포트 전달을 사용하여 HDI용 프라이빗 엔드포인트를 만드는 경우에도 **프라이빗 엔드포인트 사용** 열은 항상 공백으로 표시됩니다.

:::image type="content" source="./media/managed-vnet/akv-pe.png" alt-text="AKV의 프라이빗 엔드포인트":::

## <a name="next-steps"></a>다음 단계

- 자습서: [관리형 가상 네트워크 및 프라이빗 엔드포인트를 사용하여 복사 파이프라인 빌드](tutorial-copy-data-portal-private.md) 
- 자습서: [관리형 가상 네트워크 및 프라이빗 엔드포인트를 사용하여 매핑 데이터 흐름 파이프라인 빌드](tutorial-data-flow-private.md)
