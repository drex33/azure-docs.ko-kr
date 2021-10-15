---
title: Azure 프라이빗 엔드포인트란?
description: Azure 프라이빗 엔드포인트에 대한 자세한 정보
services: private-link
author: asudbring
ms.service: private-link
ms.topic: conceptual
ms.date: 09/09/2021
ms.author: allensu
ms.openlocfilehash: 485cd0d3d7ce2c64bfb0f37e07d785dee8808e3a
ms.sourcegitcommit: 4abfec23f50a164ab4dd9db446eb778b61e22578
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2021
ms.locfileid: "130062674"
---
# <a name="what-is-azure-private-endpoint"></a>Azure 프라이빗 엔드포인트란?

개인 끝점은 가상 네트워크의 개인 IP 주소를 사용 하는 네트워크 인터페이스입니다. 이 네트워크 인터페이스는 개인적이 고 안전 하 게 Azure 개인 링크를 통해 제공 되는 서비스에 연결 합니다. 개인 끝점을 사용 하도록 설정 하면 서비스를 가상 네트워크로 가져오는 것입니다.

서비스는 다음과 같은 Azure 서비스일 수 있습니다.

* Azure Storage
* Azure Cosmos DB
* Azure SQL Database
* [Private Link 서비스](private-link-service-overview.md)를 사용하는 자체 서비스입니다.
  
## <a name="private-endpoint-properties"></a>프라이빗 엔드포인트 속성 
 프라이빗 엔드포인트는 다음 속성을 지정합니다. 

|속성  |Description |
|---------|---------|
|속성    |    리소스 그룹의 고유한 이름입니다.      |
|서브넷    |  배포할 서브넷 및 개인 IP 주소가 할당되는 위치입니다. 서브넷 요구 사항은이 문서의 제한 사항 섹션을 참조 하세요.         |
|프라이빗 링크 리소스    |   사용 가능한 형식 목록에서 리소스 ID 또는 별칭을 사용하여 연결할 프라이빗 링크 리소스입니다. 이 리소스로 전송되는 모든 트래픽에 대해 고유한 네트워크 식별자가 생성됩니다.       |
|대상 하위 리소스   |      연결할 하위 리소스입니다. 각 프라이빗 링크 리소스 종류에는 기본 설정에 따라 선택할 수 있는 다양한 옵션이 있습니다.    |
|연결 승인 방법    |  자동 또는 수동. Azure 역할 기반 액세스 제어 권한에 따라 개인 끝점이 자동으로 승인 될 수 있습니다. Azure 역할 기반 액세스 제어 없이 프라이빗 링크 리소스에 연결하려고 하면 수동 방법을 사용하여 리소스 소유자가 연결을 승인하도록 합니다.        |
|요청 메시지     |  요청된 연결에 대한 메시지가 수동으로 승인되도록 지정할 수 있습니다. 이 메시지는 특정 요청을 식별하는 데 사용할 수 있습니다.        |
|연결 상태   |   프라이빗 엔드포인트가 활성 상태인지 여부를 지정하는 읽기 전용 속성입니다. 승인된 상태의 프라이빗 엔드포인트만 트래픽을 보내는 데 사용할 수 있습니다. 사용 가능한 추가 상태: <br>-**승인됨**: 연결이 자동 또는 수동으로 승인되었으며, 사용할 준비가 되었습니다.</br><br>-**보류 중**: 연결이 수동으로 만들어지고, 프라이빗 링크 리소스 소유자의 승인이 보류 중입니다.</br><br>-**거부됨**: Private Link 리소스 소유자가 연결을 거부했습니다.</br><br>-**연결 끊김**: 프라이빗 링크 리소스 소유자가 연결을 거부했습니다. 프라이빗 엔드포인트는 정보형으로 지정되며 정리를 위해 삭제해야 합니다. </br>|

개인 끝점에 대 한 몇 가지 주요 정보: 

- 개인 끝점을 사용 하면 동일한에서 소비자 간에 연결할 수 있습니다.
    
    - Virtual Network
    - 지역적으로 피어링된 가상 네트워크
    - 전역적으로 피어링된 가상 네트워크
    - [VPN](https://azure.microsoft.com/services/vpn-gateway/) 또는 [Express 경로](https://azure.microsoft.com/services/expressroute/) 를 사용 하 여 온-프레미스
    - 개인 링크로 제공 되는 서비스
 
- 네트워크 연결은 프라이빗 엔드포인트에 연결하는 클라이언트에서만 시작할 수 있습니다. 서비스 공급자는 서비스 소비자에 대한 연결을 만들기 위한 라우팅 구성이 없습니다. 연결은 단일 방향으로만 설정될 수 있습니다.

- 프라이빗 엔드포인트를 만들 때 리소스 수명 주기 동안 읽기 전용 네트워크 인터페이스가 생성됩니다. 이 인터페이스에는 프라이빗 링크 리소스에 매핑되는 서브넷의 동적 개인 IP 주소가 할당됩니다. 프라이빗 IP 주소의 값은 프라이빗 엔드포인트의 전체 수명 주기 동안 변경되지 않고 유지됩니다.
 
- 프라이빗 엔드포인트는 가상 네트워크와 동일한 지역 및 구독에 배포되어야 합니다. 
 
- 프라이빗 링크 리소스는 가상 네트워크 및 프라이빗 엔드포인트가 아닌 다른 지역에 배포할 수 있습니다.
 
- 동일한 프라이빗 링크 리소스를 사용하여 여러 프라이빗 엔드포인트를 만들 수 있습니다. 공용 DNS 서버 구성을 사용 하는 단일 네트워크의 경우 지정 된 개인 링크 리소스에 대해 단일 개인 끝점을 사용 하는 것이 좋습니다. DNS 확인에서 중복 항목 또는 충돌을 방지 하려면이 방법을 사용 합니다. 
 
- 동일한 가상 네트워크 내에서 동일하거나 다른 서브넷에 여러 프라이빗 엔드포인트를 만들 수 있습니다. 하나의 구독에서 만들 수 있는 프라이빗 엔드포인트 수는 제한됩니다. 자세한 내용은  [Azure 제한](../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits)을 참조하세요.

- 프라이빗 링크 리소스의 구독도 Microsoft에 등록해야 합니다. 네트워크 리소스 공급자. 자세한 내용은 [Azure 리소스 공급자](../azure-resource-manager/management/resource-providers-and-types.md)를 참조하세요.
 
## <a name="private-link-resource"></a>프라이빗 링크 리소스 
프라이빗 링크 리소스는 지정된 프라이빗 엔드포인트의 대상입니다. 

다음 표에서는 개인 끝점을 지 원하는 사용 가능한 리소스를 나열 합니다. 
 
| 프라이빗 링크 리소스 이름 | 리소스 유형 | 하위 리소스 |
| ---------------------------| ------------- | ------------- |
| **Azure App Configuration** | Microsoft.Appconfiguration/configurationStores | configurationStores |
| **Azure Automation** | Microsoft.Automation/automationAccounts | 웹후크, DSCAndHybridWorker |
| **Azure Cosmos DB** | Microsoft.AzureCosmosDB/databaseAccounts | Sql, MongoDB, Cassandra, Gremlin, Table |
| **Azure Batch** | Microsoft.Batch/batchAccounts | Batch 계정 |
| **Azure Cache for Redis** | Microsoft.Cache/Redis | redisCache |
| **Azure Cache for Redis Enterprise** | Microsoft.Cache/redisEnterprise | redisEnterprise |
| **Cognitive Services** | Microsoft.CognitiveServices/accounts | account |
| **Azure Managed Disks** | Microsoft.Compute/diskAccesses | 관리 디스크 |
| **Azure Container Registry** | Microsoft.ContainerRegistry/registries | 사용된 |
| **Azure Kubernetes Service - Kubernetes API** | Microsoft.ContainerService/managedClusters | 관리 |
| **Azure Data Factory** | Microsoft.DataFactory/factories | 데이터 팩터리 |
| **Azure Database for MariaDB** | Microsoft.DBforMariaDB/servers | mariadbServer |
| **Azure Database for MySQL** | Microsoft.DBforMySQL/servers | mysqlServer |
| **Azure Database for PostgreSQL - 단일 서버** | Microsoft.DBforPostgreSQL/servers | postgresqlServer |
| **Azure IoT Hub** | Microsoft.Devices/IotHubs | iotHub |
| **Microsoft Digital Twins** | Microsoft.DigitalTwins/digitalTwinsInstances | digitaltwinsinstance |
| **Azure Event Grid** | Microsoft.EventGrid/domains | 도메인 |
| **Azure Event Grid** | Microsoft.EventGrid/topics  | Event Grid 토픽 |
| **Azure 이벤트 허브** | Microsoft.EventHub/namespaces | 네임스페이스 |
| **Azure HDInsight** | Microsoft.HDInsight/clusters | cluster |
| **FHIR용 Azure API** | Microsoft.HealthcareApis/services | 서비스 |
| **Azure Keyvault HSM** | Microsoft.Keyvault/managedHSMs | HSM |
| **Azure Key Vault** | Microsoft.KeyVault/vaults | 자격 증명 모음 |
| **Azure Machine Learning** | Microsoft.MachineLearningServices/workspaces | amlworkspace |
| **Azure Migrate** | Microsoft.Migrate/assessmentProjects | project |
| **Application Gateway** | Microsoft.Network/applicationgateways | 애플리케이션 게이트웨이 |
| **프라이빗 링크 서비스**(사용자 고유의 서비스) |  Microsoft.Network/privateLinkServices | empty |
| **Power BI** | Microsoft.PowerBI/privateLinkServicesForPowerBI | Power BI |
| **Azure Purview** | Microsoft.Purview/accounts | account |
| **Azure Purview** | Microsoft.Purview/accounts | portal |
| **Azure Backup** | Microsoft.RecoveryServices/vaults | 자격 증명 모음 |
| **Azure Relay** | Microsoft.Relay/namespaces | 네임스페이스 |
| **Microsoft Search** | Microsoft.Search/searchServices | 검색 서비스 |
| **Azure Service Bus** | Microsoft.ServiceBus/namespaces | 네임스페이스 |
| **SignalR** | Microsoft.SignalRService/SignalR | signalr |
| **SignalR** | Microsoft.SignalRService/webPubSub | webpubsub |
| **Azure SQL Database** | Microsoft.Sql/servers | Sql Server(sqlServer) |
| **Azure Storage** | Microsoft.Storage/storageAccounts | Blob(blob, blob_secondary)<BR> Table(table, table_secondary)<BR> 큐(queue, queue_secondary)<BR> 파일(file, file_secondary)<BR> 웹(web, web_secondary) |
| **Azure 파일 동기화** | microsoft.storagesync/storageSyncServices | 파일 동기화 서비스 |
| **Azure Synapse** | Microsoft.Synapse/privateLinkHubs | synapse |
| **Azure Synapse Analytics** | Microsoft.Synapse/workspaces | Sql, SqlOnDemand, Dev | 
| **Azure App Service** | Microsoft.Web/hostingEnvironments | 호스팅 환경 |
| **Azure App Service** | Microsoft.Web/sites | sites |
| **Azure App Service** | Microsoft.Web/staticSites | staticSite |

## <a name="network-security-of-private-endpoints"></a>프라이빗 엔드포인트의 네트워크 보안 

개인 끝점을 사용 하는 경우 개인 링크 리소스에 대 한 트래픽이 보호 됩니다. 플랫폼은 지정 된 개인 링크 리소스에만 도달 하는 네트워크 연결의 유효성을 검사 하는 액세스 제어를 수행 합니다. 동일한 Azure 서비스 내에서 더 많은 리소스에 액세스하려면 추가 프라이빗 엔드포인트가 필요합니다. 
 
워크로드가 퍼블릭 엔드포인트에 액세스하여 지원되는 Azure 서비스에 연결하지 못하게 하기 위해 완전히 잠글 수 있습니다. 이 컨트롤은 리소스에 대 한 추가 네트워크 보안 계층을 제공 합니다. 보안은 동일한 Azure 서비스에서 호스트 되는 다른 리소스에 대 한 액세스를 방지 하는 보호 기능을 제공 합니다. 
 
## <a name="access-to-a-private-link-resource-using-approval-workflow"></a>승인 워크플로를 사용하여 프라이빗 링크 리소스에 액세스 
다음 연결 승인 방법을 사용하여 프라이빗 링크 리소스에 연결할 수 있습니다.
- **자동** 승인 - 사용자가 특정 프라이빗 링크 리소스를 소유하거나 해당 권한이 있는 경우. 필요한 권한은 다음 형식의 프라이빗 링크 리소스 종류를 기준으로 합니다. Microsoft.\<Provider>/<resource_type>/privateEndpointConnectionsApproval/action
- **수동** 요청 - 필요한 권한이 없고 액세스를 요청하려는 경우. 승인 워크플로가 시작됩니다. 프라이빗 엔드포인트 및 이후 프라이빗 엔드포인트 연결은 "보류 중" 상태로 만들어집니다. Private Link 리소스 소유자가 연결을 승인해야 합니다. 승인된 후 다음 승인 워크플로 다이어그램에 표시된 것처럼 프라이빗 엔드포인트가 트래픽을 정상적으로 보낼 수 있습니다.  

![워크플로 승인](media/private-endpoint-overview/private-link-paas-workflow.png)
 
프라이빗 링크 리소스 소유자는 프라이빗 엔드포인트 연결에 대해 다음 작업을 수행할 수 있습니다. 

- 모든 프라이빗 엔드포인트 연결 정보를 검토합니다. 
- 프라이빗 엔드포인트 연결을 승인합니다. 해당 프라이빗 엔드포인트가 프라이빗 링크 리소스로 트래픽을 보낼 수 있습니다. 
- 프라이빗 엔드포인트 연결을 거부합니다. 해당 프라이빗 엔드포인트는 상태를 반영하도록 업데이트됩니다.
- 모든 상태의 프라이빗 엔드포인트 연결을 삭제합니다. 해당 프라이빗 엔드포인트는 작업을 반영하도록 연결이 끊어진 상태로 업데이트되고, 프라이빗 엔드포인트 소유자는 이 시점에서만 리소스를 삭제할 수 있습니다. 
 
> [!NOTE]
> 승인된 상태의 프라이빗 엔드포인트만 지정된 프라이빗 링크 리소스에 트래픽을 보낼 수 있습니다. 

### <a name="connect-with-alias"></a>별칭을 사용 하는 커넥트

별칭은 서비스 소유자가 표준 부하 분산 장치 뒤에 프라이빗 링크 서비스를 만들 때 생성되는 고유한 모니커입니다. 서비스 소유자는이 별칭을 소비자와 오프 라인으로 공유할 수 있습니다. 

소비자는 리소스 URI 또는 별칭을 사용 하 여 개인 링크 서비스에 대 한 연결을 요청할 수 있습니다. 별칭을 사용 하 여 연결 하려면 수동 연결 승인 방법을 사용 하 여 개인 끝점을 만들어야 합니다. 수동 연결 승인 방법을 사용하려면 프라이빗 엔드포인트 만들기 흐름에서 수동 요청 매개 변수를 true로 설정합니다. 자세한 내용은 [AzPrivateEndpoint](/powershell/module/az.network/new-azprivateendpoint) 및 [az network private-endpoint create](/cli/azure/network/private-endpoint#az_network_private_endpoint_create)를 참조 하세요.

## <a name="dns-configuration"></a>DNS 구성

개인 링크 리소스에 연결 하는 데 사용 되는 DNS 설정이 중요 합니다. 연결에 FQDN (정규화 된 도메인 이름)을 사용할 때 DNS 설정이 올바른지 확인 합니다. 설정은 개인 끝점의 개인 IP 주소로 확인 되어야 합니다. 기존 Azure 서비스에는 공용 엔드포인트를 통해 연결할 때 사용할 DNS 구성이 이미 있을 수 있습니다. 이 구성은 프라이빗 엔드포인트를 사용하여 연결하도록 재정의해야 합니다. 
 
개인 끝점과 연결 된 네트워크 인터페이스에는 DNS를 구성 하는 데 필요한 정보가 포함 되어 있습니다. 이 정보에는 개인 링크 리소스에 대 한 FQDN 및 개인 IP 주소가 포함 됩니다. 

전용 끝점에 대 한 DNS를 구성 하기 위한 권장 사항에 대 한 자세한 내용은 [개인 끝점 dns 구성](private-endpoint-dns.md)을 참조 하세요.
 
## <a name="limitations"></a>제한 사항
 
다음 표에서는 프라이빗 엔드포인트를 사용하는 경우의 알려진 제한 사항 목록을 제공합니다. 

| 제한 사항 | Description |완화 방법 |
| --------- | --------- | --------- |
| 사용자 정의 경로를 사용 하 여 개인 끝점으로 향하는 트래픽은 비대칭 일 수 있습니다. | 개인 끝점에서 트래픽 반환은 NVA (네트워크 가상 어플라이언스)를 우회 하 여 원본 VM으로 복귀 하려고 시도 합니다. | 원본 네트워크 주소 변환 (SNAT)은 대칭 라우팅을 보장 하는 데 사용 됩니다. UDR을 사용 하 여 개인 끝점으로 향하는 모든 트래픽의 경우 NVA에서 트래픽에 SNAT를 사용 하는 것이 좋습니다. |

> [!IMPORTANT]
> 개인 끝점에 대 한 NSG 및 UDR 지원은 선택 영역에서 공개 미리 보기 상태입니다. 자세한 내용은 개인 [링크 UDR 지원의 공개 미리 보기](https://azure.microsoft.com/updates/public-preview-of-private-link-udr-support/) 및 [개인 링크 네트워크 보안 그룹 지원의 공개 미리 보기](https://azure.microsoft.com/updates/public-preview-of-private-link-network-security-group-support/)를 참조 하세요.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

## <a name="public-preview-limitations"></a>공용 미리 보기 제한 사항

### <a name="nsg"></a>NSG

| 제한 사항 | Description | 완화 방법 |
| ---------- | ----------- | ---------- |
| 효율적인 경로 가져오기 및 보안 규칙은 개인 끝점 네트워크 인터페이스에서 사용할 수 없습니다. | 네트워크 인터페이스로 이동 하 여 유효한 경로 및 보안 규칙에 대 한 관련 정보를 확인할 수 없습니다. | Q4CY21 |
| NSG 흐름 로그는 지원 되지 않습니다. | NSG 흐름 로그는 개인 끝점으로 향하는 인바운드 트래픽에 대해 작동 하지 않습니다. | 지금은 정보가 없습니다. |
| ZRS 저장소 계정으로 일시적으로 삭제 됩니다. | ZRS 저장소 계정을 사용 하는 고객은 저장소 개인 끝점 서브넷에 NSG를 적용 한 경우에도 정기적으로 일시적으로 삭제 될 수 있습니다. | 9월 |
| Azure Key Vault로 간헐적으로 삭제 됩니다. | Azure Key Vault를 사용 하는 고객은 Azure Key Vault 개인 끝점 서브넷에 NSG를 적용 한 경우에도 정기적으로 일시적으로 삭제 될 수 있습니다. | 9월 |
| NSG 당 주소 접두사 수에 대 한 제한입니다. | 단일 규칙의 NSG에 500 개 이상의 주소 접두사가 있는 것은 지원 되지 않습니다. | 9월 |
| AllowVirtualNetworkAccess 플래그 | 다른 VNet (VNet B)에 대 한 피어 링 링크에서 **AllowVirtualNetworkAccess** 플래그가 false 인 vnet 피어 링을 설정 하는 고객은 **VirtualNetwork** 태그를 사용 하 여 개인 끝점 리소스에 액세스 하는 VNet B의 트래픽을 거부할 수 없습니다. 개인 끝점에 대 한 트래픽을 거부 하기 위해 VNet B의 주소 접두사에 대 한 블록을 명시적으로 저장 해야 합니다. | 9월 |
| 지원 되지 않는 이중 포트 NSG 규칙입니다. | NSG 규칙에 여러 포트 범위를 사용 하는 경우 허용 규칙 및 거부 규칙에 대해 첫 번째 포트 범위만 허용 됩니다. 여러 포트 범위를 사용 하는 규칙은 기본적으로 특정 포트 대신 모두 거부 됩니다. </br> **자세한 내용은 아래의 규칙 예를 참조 하세요.** | 9월 |

| 우선 순위 | 원본 포트 | 대상 포트 | 작업 | 유효 작업 |
| -------- | ----------- | ---------------- | ------ | ---------------- |
| 10 | 10-12 | 10-12 | 허용/거부 | 원본/대상 포트의 단일 포트 범위는 예상 대로 작동 합니다. |
| 10 | 10-12, 13-14 | 14-15, 16-17 | 허용 | 원본 포트 10-12 및 대상 포트 14-15만 허용 됩니다. |
| 10 | 10-12, 13-14 | 120-130, 140-150 | 거부 | 원본 및 대상 포트 범위가 여러 개 있으므로 모든 원본 포트의 트래픽이 모든 대상 포트에서 거부 됩니다. |
| 10 | 10-12, 13-14 | 120-130 | 거부 | 모든 원본 포트의 트래픽은 대상 포트 120-130에 대해서만 거부 됩니다. 원본 포트 범위와 대상 포트 범위가 여러 개 있습니다. |

**표: 예제 이중 포트 규칙**

### <a name="udr"></a>UDR

| 제한 사항 | Description | 완화 방법 |
| ---------- | ----------- | ---------- |
| 원본 네트워크 주소 변환 (SNAT)은 항상 권장 됩니다. | 개인 끝점 데이터 평면의 변수 특성 때문에 반환 트래픽이 적용 되도록 개인 끝점으로 향하는 트래픽을 SNAT 하는 것이 좋습니다. | 지금은 정보가 없습니다. |
 
## <a name="next-steps"></a>다음 단계

- 개인 끝점 및 개인 링크에 대 한 자세한 내용은 [Azure 개인 링크 란?](private-link-overview.md)을 참조 하세요.
- 웹 앱에 대 한 개인 끝점 만들기를 시작 하려면 빠른 시작 [-Azure Portal를 사용 하 여 개인 끝점 만들기](create-private-endpoint-portal.md)를 참조 하세요.
