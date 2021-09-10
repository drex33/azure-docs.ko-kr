---
title: Azure Private Link 가용성
description: 이 문서에서는 Private Link를 지원하는 Azure 서비스에 대해 알아봅니다.
author: asudbring
ms.author: allensu
ms.service: private-link
ms.topic: conceptual
ms.date: 3/15/2021
ms.custom: template-concept,references_regions
ms.openlocfilehash: 052a59ede2acc751d6847646ca1b103de0c1b78a
ms.sourcegitcommit: 8000045c09d3b091314b4a73db20e99ddc825d91
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/19/2021
ms.locfileid: "122539533"
---
# <a name="azure-private-link-availability"></a>Azure Private Link 가용성

Azure Private Link를 사용하면 가상 네트워크의 [프라이빗 엔드포인트](private-endpoint-overview.md)를 통해 Azure PaaS Services(예: Azure Storage 및 SQL Database)와 Azure 호스팅 고객 소유/파트너 서비스에 액세스할 수 있습니다. 

> [!IMPORTANT]
> Azure Private Link가 이제 일반 공급됩니다. 프라이빗 엔드포인트 및 Private Link 서비스(표준 부하 분산 장치 뒤의 서비스)가 모두 일반 공급됩니다. 다른 Azure PaaS는 다른 일정에 따라 Azure Private Link에 온보딩됩니다. 알려진 제한은 [프라이빗 엔드포인트](private-endpoint-overview.md#limitations) 및 [Private Link Service](private-link-service-overview.md#limitations)를 참조하세요. 

## <a name="service-availability"></a>서비스 가용성

다음 표에는 Private Link 서비스 및 이러한 서비스를 사용할 수 있는 지역이 나열되어 있습니다. 

### <a name="ai--machine-learning"></a>AI + Machine Learning

|지원되는 서비스  |사용 가능한 지역 | 기타 고려 사항 | 상태  |
|:-------------------|:-----------------|:----------------|:--------|
|Azure Machine Learning | 모든 공용 지역    |  | GA   <br/> [Azure Machine Learning에 대한 프라이빗 엔드포인트를 만드는 방법을 알아봅니다.](../machine-learning/how-to-configure-private-link.md)   |

### <a name="analytics"></a>분석

|지원되는 서비스  |사용 가능한 지역 | 기타 고려 사항 | 상태  |
|:-------------------|:-----------------|:----------------|:--------|
|Azure Synapse Analytics| 모든 공용 지역 <br/> Azure Government 지역 |  프록시 [연결 정책](../azure-sql/database/connectivity-architecture.md#connection-policy)에 대해 지원됨 |GA <br/> [Azure Synapse Analytics에 대한 프라이빗 엔드포인트를 만드는 방법을 알아봅니다.](../azure-sql/database/private-endpoint-overview.md)|
|Azure Event Hub | 모든 공용 지역<br/>Azure Government 지역      |   | GA   <br/> [Azure Event Hub에 대한 프라이빗 엔드포인트를 만드는 방법을 알아봅니다.](../event-hubs/private-link-service.md)  |
| Azure Monitor <br/>(Log Analytics 및 Application Insights) | 모든 공용 지역      |  | GA   <br/> [Azure Monitor에 대한 프라이빗 엔드포인트를 만드는 방법을 알아봅니다.](../azure-monitor/logs/private-link-security.md)   |
|Azure 데이터 팩터리 | 모든 공용 지역<br/> Azure Government 지역<br/>모든 중국 지역    | 자격 증명을 Azure key vault에 저장해야 합니다.| GA   <br/> [Azure Data Factory에 대한 프라이빗 엔드포인트를 만드는 방법을 알아봅니다.](../data-factory/data-factory-private-link.md)   |

### <a name="compute"></a>컴퓨팅

|지원되는 서비스  |사용 가능한 지역 | 기타 고려 사항 | 상태  |
|:-------------------|:-----------------|:----------------|:--------|
|Azure App Configuration | 모든 공용 지역      |  | GA  </br> [Azure App Configuration에 대한 프라이빗 엔드포인트를 만드는 방법을 알아봅니다.](../azure-app-configuration/concept-private-endpoint.md) |
|Azure Managed Disks | 모든 공용 지역<br/> Azure Government 지역<br/>모든 중국 지역    | [알려진 제한 사항에 따른 선택](../virtual-machines/disks-enable-private-links-for-import-export-portal.md#limitations) | GA   <br/> [Azure Managed Disks에 대한 프라이빗 엔드포인트를 만드는 방법을 알아봅니다.](../virtual-machines/disks-enable-private-links-for-import-export-portal.md)   |

### <a name="containers"></a>컨테이너

|지원되는 서비스  |사용 가능한 지역 | 기타 고려 사항 | 상태  |
|:-------------------|:-----------------|:----------------|:--------|
|Azure Container Registry | 모든 공용 지역<br/> Azure Government 지역    | 컨테이너 레지스트리의 프리미엄 계층에서 지원됨 [계층 선택](../container-registry/container-registry-skus.md)| GA   <br/> [Azure Container Registry에 대한 프라이빗 엔드포인트를 만드는 방법을 알아봅니다.](../container-registry/container-registry-private-link.md)   |
|Azure Kubernetes Service - Kubernetes API | 모든 공용 지역      |  | GA   <br/> [Azure Kubernetes Service에 대한 프라이빗 엔드포인트를 만드는 방법을 알아봅니다.](../aks/private-clusters.md)   |

### <a name="databases"></a>데이터베이스

|지원되는 서비스  |사용 가능한 지역 | 기타 고려 사항 | 상태  |
|:-------------------|:-----------------|:----------------|:--------|
|  Azure SQL Database         | 모든 공용 지역 <br/> Azure Government 지역<br/>모든 중국 지역      |  프록시 [연결 정책](../azure-sql/database/connectivity-architecture.md#connection-policy)에 대해 지원됨 | GA <br/> [Azure SQL에 대한 프라이빗 엔드포인트를 만드는 방법을 알아봅니다.](./tutorial-private-endpoint-sql-portal.md)      |
|Azure Cosmos DB|  모든 공용 지역<br/> Azure Government 지역</br> 모든 중국 지역 | |GA <br/> [Cosmos DB에 대한 프라이빗 엔드포인트를 만드는 방법을 알아봅니다.](./tutorial-private-endpoint-cosmosdb-portal.md)|
|  Azure Database for PostgreSQL - 단일 서버         | 모든 공용 지역 <br/> Azure Government 지역<br/>모든 중국 지역     | 범용 및 메모리 최적화된 가격 책정 계층에 지원됨 | GA <br/> [Azure Database for PostgreSQL에 대한 프라이빗 엔드포인트를 만드는 방법을 알아봅니다.](../postgresql/concepts-data-access-and-security-private-link.md)      |
|  Azure Database for MySQL         | 모든 공용 지역<br/> Azure Government 지역<br/>모든 중국 지역      |  | GA <br/> [Azure Database for MySQL에 대한 프라이빗 엔드포인트를 만드는 방법을 알아봅니다.](../mysql/concepts-data-access-security-private-link.md)     |
|  Azure Database for MariaDB         | 모든 공용 지역<br/> Azure Government 지역<br/>모든 중국 지역     |  | GA <br/> [Azure Database for MariaDB에 대한 프라이빗 엔드포인트를 만드는 방법을 알아봅니다.](../mariadb/concepts-data-access-security-private-link.md)      |

### <a name="integration"></a>통합

|지원되는 서비스  |사용 가능한 지역 | 기타 고려 사항 | 상태  |
|:-------------------|:-----------------|:----------------|:--------|
|Azure Event Grid| 모든 공용 지역<br/> Azure Government 지역       |  | GA   <br/> [Azure Event Grid에 대한 프라이빗 엔드포인트를 만드는 방법을 알아봅니다.](../event-grid/network-security.md) |
|Azure Service Bus | 모든 공용 지역<br/>Azure Government 지역  | Azure Service Bus의 프리미엄 계층에서 지원됩니다. [계층 선택](../service-bus-messaging/service-bus-premium-messaging.md) | GA   <br/> [Azure Service Bus에 대한 프라이빗 엔드포인트를 만드는 방법을 알아봅니다.](../service-bus-messaging/private-link-service.md)    |

### <a name="internet-of-things-iot"></a>IoT(사물 인터넷)

|지원되는 서비스  |사용 가능한 지역 | 기타 고려 사항 | 상태  |
|:-------------------|:-----------------|:----------------|:--------|
| Azure IoT Hub | 모든 공용 지역    |  | GA   <br/> [Azure IoT Hub에 대한 프라이빗 엔드포인트를 만드는 방법을 알아봅니다.](../iot-hub/virtual-network-support.md) |
|  Azure Digital Twins         | Azure Digital Twins에서 지원하는 모든 공용 지역     |  | 미리 보기 <br/> [Azure Digital Twins에 대한 프라이빗 엔드포인트를 만드는 방법을 알아봅니다.](../digital-twins/how-to-enable-private-link-portal.md)      |

### <a name="management-and-governance"></a>관리 및 거버넌스

| 지원되는 서비스 | 사용 가능한 지역 | 기타 고려 사항 | 상태  |
| ------------ | ----------------| ------------| ----------------|
| Azure Automation  | 모든 공용 지역<br/> Azure Government 지역 |  | 미리 보기 </br> [Azure Automation에 대한 프라이빗 엔드포인트를 만드는 방법을 알아봅니다.](../automation/how-to/private-link-security.md)|
|Azure Backup | 모든 공용 지역<br/> Azure Government 지역   |  | GA <br/> [Azure Backup에 대한 프라이빗 엔드포인트를 만드는 방법을 알아봅니다.](../backup/private-endpoints.md)   |

### <a name="security"></a>보안

|지원되는 서비스  |사용 가능한 지역 | 기타 고려 사항 | 상태  |
|:-------------------|:-----------------|:----------------|:--------|
|  Azure Key Vault         | 모든 공용 지역<br/> Azure Government 지역      |  | GA   <br/> [Azure Key Vault에 대한 프라이빗 엔드포인트를 만드는 방법을 알아봅니다.](../key-vault/general/private-link-service.md)   |

### <a name="storage"></a>스토리지
|지원되는 서비스  |사용 가능한 지역 | 기타 고려 사항 | 상태  |
|:-------------------|:-----------------|:----------------|:--------|
| Azure Blob 스토리지(Data Lake Storage Gen2 포함)       |  모든 공용 지역<br/> Azure Government 지역       |  Account Kind General Purpose V2에서만 지원 | GA <br/> [Blob 스토리지에 대한 프라이빗 엔드포인트를 만드는 방법을 알아봅니다.](tutorial-private-endpoint-storage-portal.md)  |
| Azure 파일 | 모든 공용 지역<br/> Azure Government 지역      | |   GA <br/> [Azure Files 네트워크 엔드포인트를 만드는 방법을 알아봅니다.](../storage/files/storage-files-networking-endpoints.md)   |
| Azure 파일 동기화 | 모든 공용 지역      | |   GA <br/> [Azure Files 네트워크 엔드포인트를 만드는 방법을 알아봅니다.](../storage/file-sync/file-sync-networking-endpoints.md)   |
| Azure Queue 스토리지       |  모든 공용 지역<br/> Azure Government 지역       |  Account Kind General Purpose V2에서만 지원 | GA <br/> [큐 스토리지에 대한 프라이빗 엔드포인트를 만드는 방법을 알아봅니다.](tutorial-private-endpoint-storage-portal.md) |
| Azure Table Storage       |  모든 공용 지역<br/> Azure Government 지역       |  Account Kind General Purpose V2에서만 지원 | GA <br/> [테이블 스토리지에 대한 프라이빗 엔드포인트를 만드는 방법을 알아봅니다.](tutorial-private-endpoint-storage-portal.md)  |
| Azure Batch | 다음을 제외한 모든 공용 지역: 독일 중부, 독일 북동부 <br/> Azure Government 지역  | | GA <br/> [Azure Batch에 대한 프라이빗 엔드포인트를 만드는 방법을 알아봅니다.](../batch/private-connectivity.md) |

### <a name="web"></a>웹
|지원되는 서비스  |사용 가능한 지역 | 기타 고려 사항 | 상태  |
|:-------------------|:-----------------|:----------------|:--------|
| Azure SignalR | 미국 동부, 미국 중남부,<br/>미국 서부 2, 모든 중국 지역      |  | 미리 보기   <br/> [Azure SignalR에 대한 프라이빗 엔드포인트를 만드는 방법을 알아봅니다.](../azure-signalr/howto-private-endpoints.md)   |
|Azure Web Apps | 모든 공용 지역<br/> 중국 북부 2 및 동부 2    | PremiumV2, PremiumV3 또는 함수 프리미엄 계획에서 지원됨  | GA   <br/> [Azure Web Apps에 대한 프라이빗 엔드포인트를 만드는 방법을 알아봅니다.](./tutorial-private-endpoint-webapp-portal.md)   |
|Azure Search | 모든 공용 지역 <br/> Azure Government 지역 | 개인 모드 서비스에서 지원됨 | GA   <br/> [Azure Search에 대한 프라이빗 엔드포인트를 만드는 방법을 알아봅니다.](../search/service-create-private-endpoint.md)    |
|Azure Relay | 모든 공용 지역      |  | 미리 보기 <br/> [Azure Relay에 대한 프라이빗 엔드포인트를 만드는 방법을 알아봅니다.](../azure-relay/private-link-service.md)  |

### <a name="private-link-service-with-a-standard-load-balancer"></a>표준 Load Balancer를 사용하는 Private Link 서비스

|지원되는 서비스  |사용 가능한 지역 | 기타 고려 사항 | 상태  |
|:-------------------|:-----------------|:----------------|:--------|
|표준 Azure Load Balancer 뒤에 있는 Private Link 서비스 | 모든 공용 지역<br/> Azure Government 지역<br/>모든 중국 지역  | 표준 Load Balancer에서 지원 | GA <br/> [Private Link 서비스를 만드는 방법을 알아봅니다.](create-private-link-service-portal.md) |

## <a name="next-steps"></a>다음 단계

Azure Private Link 서비스에 대해 자세히 알아봅니다.
- [Azure Private Link란?](private-link-overview.md)
- [Azure Portal을 사용하여 프라이빗 엔드포인트 만들기](create-private-endpoint-portal.md)
