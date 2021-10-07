---
title: Azure 서비스
description: Azure의 지역 유형 및 서비스 범주에 대해 알아봅니다.
author: prsandhu
ms.service: azure
ms.topic: conceptual
ms.date: 10/01/2021
ms.author: prsandhu
ms.reviewer: cynthn
ms.custom: references_regions
ms.openlocfilehash: dc0adacbda491f7658056e7d313b9bdc923b43af
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/06/2021
ms.locfileid: "129622456"
---
# <a name="azure-services"></a>Azure 서비스

Azure 지역에서 서비스의 가용성은 지역 유형에 따라 달라 집니다. Azure에는 두 가지 유형의 지역 ( *권장* 및 *대체*)이 있습니다.

- **권장**: 이러한 지역은 가장 광범위 한 서비스 기능을 제공 하며 현재 가용성 영역을 지원 합니다. **권장 사항** 에 따라 Azure Portal 지정 됩니다.
- **대체**: 이러한 지역은 권장 지역이 현재 있는 데이터 상주 경계 내에서 Azure의 공간을 확장 합니다. 대체 지역은 대기 시간을 최적화 하 고 재해 복구 요구 사항에 대 한 보조 지역을 제공 하지만 가용성 영역을 지원 하지 않습니다. Azure는 대체 지역을 정기적으로 평가 하 여 추천 지역이 되어야 하는지 확인 합니다. Azure Portal 지정 **합니다.**

## <a name="service-categories-across-region-types"></a>지역 유형 간의 서비스 범주

Azure 서비스는 *기본, 일반* 및 *전략* 의 세 가지 범주로 그룹화 됩니다.  지정 된 지역에 서비스를 배포 하는 방법에 대 한 Azure의 일반 정책은 주로 지역 유형, 서비스 범주 및 고객 수요를 중심으로 합니다.

- 기본: 지역이 일반 공급 되는 경우 모든 권장 및 대체 지역에서 사용할 수 있으며, 새 기본 서비스를 일반적으로 사용할 수 있는 90 일 이내에 사용할 수 **있습니다.**
- **메인스트림**: 지역 일반 공급의 90 일 이내에 권장 되는 모든 지역에서 사용할 수 있습니다. 대체 지역에서 수요를 기반으로 하며 대부분은 대체 지역의 많은 하위 집합에 이미 배포 되어 있습니다.
- **전략적** (이전에 특수화): 대상 서비스 제품으로, 종종 업계에서 집중 하거나 사용자 지정 된 하드웨어에서 지원 됩니다. 여러 지역에 걸친 수요 기반 가용성은 이미 권장 지역의 많은 하위 집합에 배포 됩니다.

지역에 배포 되는 서비스 및 미리 보기에 대 한 향후 로드맵 및 지역에서 서비스의 일반 공급을 보려면 [지역별 사용 가능한 제품](https://azure.microsoft.com/global-infrastructure/services/)을 참조 하세요.

지역에서 서비스 제공을 사용할 수 없는 경우 Microsoft 영업 담당자에 게 자세한 내용을 문의 하 고 옵션을 탐색 합니다.

| 영역 형식 | 비지역 | 기본 | 일반 | 전략적 | 가용성 영역 | 데이터 상주 |
| --- | --- | --- | --- | --- | --- | --- |
| 권장 | **예** | **예** | **예** | 수요 기반 | **예** | **예** |
| 대체 | **예** | **예** | 수요 기반 | 수요 기반 | 해당 없음 | **예** |

## <a name="available-services-by-category"></a>범주별 사용 가능한 서비스

Azure는 일반 공급으로 서비스 범주를 기본, 일반 및 전략적으로 할당 합니다. 일반적으로 서비스는 전략적 서비스로 시작 되며 일반으로 업그레이드 되 고 필요에 따라 기본으로 업그레이드 되며 증가를 사용 합니다.

Azure 서비스는 범주별로 다음 표에 표시 됩니다. 일부 서비스는 비 지역입니다. 즉, 지역에 관계 없이 전역적으로 사용할 수 있습니다. 지역별 서비스의 전체 목록 및 자세한 내용은 [지역별 사용 가능한 제품](https://azure.microsoft.com/global-infrastructure/services/)을 참조 하세요.

> [!div class="mx-tableFixed"]
> | ![이 서비스가 기본 서비스 임을 나타내는 아이콘입니다.](media/icon-foundational.svg) 기본                           | ![이 서비스가 기본 서비스 임을 나타내는 아이콘입니다.](media/icon-mainstream.svg) 일반                                        | 
> |----------------------------------------|---------------------------------------------------|
> | Azure 스토리지 계정                 | Azure API Management                              | 
> | Azure Application Gateway              | Azure App Configuration                           | 
> | Azure Backup                           | Azure App Service                                 | 
> | Azure Cosmos DB                        | Azure Automation                                  | 
> | Azure Data Lake Storage Gen2           | Azure Active Directory Domain Services            | 
> | Azure ExpressRoute                     | Azure Bastion                                     | 
> | Azure 공용 IP                        | Azure Cache for Redis                             | 
> | Azure SQL Database                     | Azure Cognitive Services                          | 
> | Azure SQL Managed Instance             | Azure Cognitive Services: Computer Vision         | 
> | 디스크 스토리지                           | Azure Cognitive Services: Content Moderator       | 
> | Azure Event Hubs                       | Azure Cognitive Services: Face                    | 
> | Azure Key Vault                        | Azure Cognitive Services: Text Analytics          | 
> | Azure Load Balancer                    | Azure Data Explorer                               | 
> | Azure Service Bus                      | Azure Database for MySQL                          | 
> | Azure Service Fabric                   | Azure Database for PostgreSQL                     | 
> | Azure Storage: 핫/쿨 Blob Storage 계층   | Azure DDoS Protection                       | 
> | Storage: Managed Disks                 | Azure Firewall                                    | 
> | Azure Virtual Machine Scale Sets       | Azure Firewall Manager                            | 
> | Azure Virtual Machines                 | Azure 기능                                   | 
> | Virtual Machines: Azure Dedicated Host | Azure IoT Hub                                     | 
> | Virtual Machines: Av2 시리즈           | AKS(Azure Kubernetes Service)                    | 
> | Virtual Machines: Bs 시리즈            | Azure Monitor: Application Insights               | 
> | Virtual Machines: DSv2 시리즈          | Azure Monitor: Log Analytics                      | 
> | Virtual Machines: DSv3 시리즈          | Azure Private Link                                | 
> | Virtual Machines: Dv2 시리즈           | Azure Site Recovery                               | 
> | Virtual Machines: Dv3 시리즈           | Azure Synapse Analytics                           |     
> | Virtual Machines: ESv3 시리즈          | Azure Batch                                       | 
> | Virtual Machines: Ev3 시리즈           | Azure Cloud Service: M 시리즈                     | 
> | Azure Virtual Network                  | Azure Container Instances                         | 
> | Azure VPN Gateway                      | Azure Container Registry                          | 
> |                                        | Azure 데이터 팩터리                                | 
> |                                        | Azure Event Grid                                  | 
> |                                        | Azure HDInsight                                   |  
> |                                        | Azure Logic Apps                                  | 
> |                                        | Azure Media Services                              | 
> |                                        | Azure Network Watcher                             | 
> |                                        | 프리미엄 Blob Storage                              | 
> |                                        | 프리미엄 Files Storage                             | 
> |                                        | Virtual Machines: Ddsv4 시리즈                    | 
> |                                        | Virtual Machines: Ddv4 시리즈                     | 
> |                                        | Virtual Machines: Dsv4 시리즈                     | 
> |                                        | Virtual Machines: Dv4 시리즈                      | 
> |                                        | Virtual Machines: Edsv4 시리즈                    | 
> |                                        | Virtual Machines: Edv4 시리즈                     | 
> |                                        | Virtual Machines: Esv4 시리즈                     | 
> |                                        | Virtual Machines: Ev4 시리즈                      | 
> |                                        | Virtual Machines: Fsv2 시리즈                     | 
> |                                        | Virtual Machines: M 시리즈                        | 
> |                                        | Azure 가상 WAN                                 | 

### <a name="strategic-services"></a>전략적 서비스
앞서 언급했듯이 Azure는 서비스를 기본, 일반 및 전략적의 세 가지 범주로 분류합니다. 서비스 범주는 일반 공급 시 할당됩니다. 종종 서비스는 전략적 서비스로 수명 주기를 시작하고 수요와 사용률이 증가하면 일반 또는 기본 서비스로 승격될 수 있습니다. 다음 표에서는 전략적 서비스를 나열합니다. 

> [!div class="mx-tableFixed"]
> | ![이 서비스를 나타내는 아이콘은 전략적입니다.](media/icon-strategic.svg) 전략적                                          |
> |------------------------------------------------------|
> | FHIR용 Azure API                                   |
> | Azure Analysis Services                              |
> | Azure Blockchain Service                             |
> | Azure Cognitive Services: Anomaly Detector           |
> | Azure Cognitive Services: Custom Vision              |
> | Azure Cognitive Services: Form Recognizer            |
> | Azure Cognitive Services: 몰입형 리더           |
> | Azure Cognitive Services: Language Understanding     |
> | Azure Cognitive Services: Personalizer               |
> | Azure Cognitive Services: QnA Maker                  |
> | Azure Cognitive Services: 음성 서비스            |
> | Azure Data Share                                     |
> | Azure Databricks                                     |
> | Azure Database for MariaDB                           |
> | Azure Database Migration Service                     |
> | Azure 전용 HSM                                  |
> | Azure Digital Twins                                  |
> | Azure Health Bot                                     |
> | Azure HPC Cache                                      |
> | Azure Lab Services                                   |
> | Azure NetApp Files                                   |
> | Azure Red Hat OpenShift                              |
> | Azure SignalR Service                                |
> | Azure Spring Cloud                                   |
> | Azure Stream Analytics                               |
> | Azure Time Series Insights                           |
> | Azure VMware 솔루션                                |
> | Azure VMware Solution by CloudSimple                 |
> | Azure Spatial Anchors                                |
> | Storage: Archive Storage                             |
> | Azure Ultra Disk Storage                             |
> | 비디오 인덱서                                        |
> | Virtual Machines: DASv4 시리즈                       |
> | Virtual Machines: DAv4 시리즈                        |
> | Virtual Machines: DCsv2-series                       |
> | Virtual Machines: EASv4 시리즈                       |
> | Virtual Machines: EAv4 시리즈                        |
> | Virtual Machines: HBv1 시리즈                        |
> | Virtual Machines: HBv2 시리즈                        |
> | Virtual Machines: HCv1 시리즈                        |
> | Virtual Machines: H 시리즈                           |
> | Virtual Machines: LSv2 시리즈                        |
> | Virtual Machines: Mv2 시리즈                         |
> | Virtual Machines: NCv3 시리즈                        |
> | Virtual Machines: NDv2 시리즈                        |
> | Virtual Machines: NVv3 시리즈                        |
> | Virtual Machines: NVv4 시리즈                        | 
> | Virtual Machines: SAP HANA(대규모 인스턴스)  |

\*가용성 영역을 지원하는 VM: AV2 시리즈, B 시리즈, DSv2 시리즈, DSv3 시리즈, Dv2 시리즈, Dv3 시리즈, ESv3 시리즈, Ev3 시리즈, F 시리즈, FS 시리즈, FSv2 시리즈 및 M 시리즈.\*

이전 세대의 서비스 또는 가상 머신은 나열되지 않습니다. 자세한 내용은 [이전 세대의 가상 머신 크기](../virtual-machines/sizes-previous-gen.md)를 참조하세요.

아직 일반 공급되지 않은 미리 보기 서비스에 대해 자세히 알아보고 이러한 서비스 목록을 보려면 [지역별 사용 가능한 제품을](https://azure.microsoft.com/global-infrastructure/services/)참조하세요. 가용성 영역을 지원하는 서비스의 전체 목록은 가용성 영역을 [지원하는 Azure 서비스를 참조하세요.](az-region.md)

## <a name="next-steps"></a>다음 단계

- [가용성 영역을 지원하는 Azure 서비스](az-region.md)
- [Azure의 영역 및 가용성 영역](az-overview.md)
