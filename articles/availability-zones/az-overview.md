---
title: Azure의 지역 및 가용성 영역
description: 기술 및 규정 요구 사항에 맞는 Azure의 지역 및 가용성 영역에 대해 알아봅니다.
author: prsandhu
ms.service: azure
ms.topic: conceptual
ms.date: 04/09/2021
ms.author: prsandhu
ms.reviewer: cynthn
ms.custom: fasttrack-edit, mvc
ms.openlocfilehash: 8d2198c4c2c2dd100d7bbf88eebc4e521d6ace04
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122529234"
---
# <a name="regions-and-availability-zones-in-azure"></a>Azure의 지역 및 가용성 영역

Microsoft Azure 서비스는 클라우드 운영이 최적의 수준에서 이루어지도록 전역적으로 제공됩니다. 기술 및 규정 고려 사항(서비스 기능, 데이터 보존, 규정 준수 요구 사항 및 대기 시간)에 따라 요구 사항에 가장 적합한 지역을 선택할 수 있습니다.

## <a name="terminology"></a>용어

Azure의 지역 및 가용성 영역을 더 잘 이해할 수 있도록 주요 용어 또는 개념에 대한 이해를 돕는 설명을 제공합니다.

| 용어 또는 개념 | Description |
| --- | --- |
| region | 대기 시간이 정의된 경계 내에 배포되며 대기 시간이 짧은 전용 지역 네트워크를 통해 연결되는 데이터 센터 집합입니다. |
| geography | 하나 이상의 Azure 지역을 포함하는 특정 영역입니다. 지리적 위치는 데이터 보존 및 규정 준수 경계를 유지 하는 불연속 시장을 정의합니다. 지리적 위치를 통해 특정 데이터 상주 및 준수 요구 사항이 있는 고객은 데이터와 애플리케이션을 가깝게 유지할 수 있습니다. 지리적 위치는 전용 대용량 네트워킹 인프라와 연결을 통해 전체 지역 오류를 견디는 내결함성이 있습니다. |
| 가용성 영역 | 지역 내의 고유한 물리적 위치입니다. 각 영역은 독립된 전원, 냉각 및 네트워킹을 갖춘 하나 이상의 데이터 센터로 구성됩니다. |
| 권장 지역 | 가장 광범위한 서비스 기능을 제공하며 현재 또는 나중에 가용성 영역을 지원하도록 설계된 지역입니다. 이는 Azure Portal에 **권장** 지역으로 지정됩니다. |
| 대체(기타) 지역 | 권장 지역이 있는 데이터 보존 경계 내에서 Azure의 공간을 확장하는 지역입니다. 대체 지역은 대기 시간을 최적화하고 재해 복구 요구 사항에 맞는 두 번째 지역을 제공하는 데 도움이 됩니다. 이는 가용성 영역을 지원하도록 설계되지 않았습니다(하지만 Azure가 이러한 지역에 대한 정기적인 평가를 수행하여 권장 지역이 되어야 하는지 확인). 이는 Azure Portal에 **기타** 지역으로 지정됩니다. |
| 기본 서비스 | 지역이 일반 공급될 때 모든 지역에서 사용할 수 있는 핵심 Azure 서비스입니다. |
| 일반 서비스 | 지역 일반 공급 또는 대체 지역의 수요 기반 공급 후 90일 이내에 모든 권장 지역에서 사용할 수 있는 Azure 서비스입니다. |
| 특수 서비스 | 사용자 지정/특수 하드웨어에서 지원하는 지역에서 수요를 기반으로 공급되는 Azure 서비스입니다. |
| 지역 서비스 | 지역별로 배포되고 고객이 서비스를 배포할 지역을 지정할 수 있게 해주는 Azure 서비스입니다. 전체 목록은 [지역별로 사용 가능한 제품](https://azure.microsoft.com/global-infrastructure/services/?products=all)을 참조하세요. |
| 비지역 서비스 | 특정 Azure 지역에 대한 종속성이 없는 Azure 서비스입니다. 비지역 서비스는 둘 이상의 지역에 배포되고, 지역 오류가 발생하면 다른 지역의 서비스 인스턴스가 계속해서 고객에게 서비스를 제공합니다. 전체 목록은 [지역별로 사용 가능한 제품](https://azure.microsoft.com/global-infrastructure/services/?products=all)을 참조하세요. |

## <a name="regions"></a>영역

지역은 대기 시간이 정의된 경계 내에 배치되고, 지역별 짧은 대기 시간 네트워크를 통해 연결된 데이터 센터 집합입니다. Azure는 여러 지역에 지역 간 복원력을 제공하는 등, 필요한 곳에 애플리케이션을 유연하게 배포할 수 있게 해줍니다. 자세한 내용은 [복원력 핵심 요소](/azure/architecture/framework/resiliency/principles)를 참조하세요.

## <a name="availability-zones"></a>가용성 영역

가용성 영역은 데이터 센터 오류에서 애플리케이션 및 데이터를 보호하는 고가용성 제품입니다. 가용성 영역은 Azure 지역 내의 고유한 물리적 위치입니다. 각 영역은 독립된 전원, 냉각 및 네트워킹을 갖춘 하나 이상의 데이터 센터로 구성됩니다. 복원력을 보장하려면 활성화된 모든 지역에서 최소한 세 개의 별도 영역이 필요합니다. 지역 내에서 가용성 영역의 물리적 구분은 애플리케이션 및 데이터를 데이터 센터 오류로부터 보호할 수 있습니다. 영역 중복 서비스는 단일 지점 오류에서 보호하기 위해 가용성 영역에서 애플리케이션 및 데이터를 복제합니다. Azure는 가용성 영역을 통해 업계 최고의 99.99% VM 작동 시간 SLA를 제공합니다. 전체 [Azure SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/)는 Azure의 보장된 가용성에 대해 전반적으로 설명합니다.

Azure 지역의 가용성 영역은 장애 도메인과 업데이트 도메인의 조합입니다. 예를 들어 Azure 지역의 3개 영역에 VM을 3개 이상 만들면 장애 도메인 3개와 업데이트 도메인 3개에 VM이 효과적으로 분산됩니다. Azure 플랫폼은 업데이트 도메인에 분산된 VM을 인식하여 여러 영역에 있는 VM이 같은 시간에 업데이트를 예약하지 않도록 합니다.

영역 내에서 컴퓨팅, 스토리지, 네트워킹 및 데이터 리소스를 공동 배치하고 다른 영역에 복제하여 애플리케이션 아키텍처에 고가용성을 빌드합니다. 가용성 영역을 지원하는 Azure 서비스는 다음의 두 범주로 나뉩니다.

- **영역 서비스** - 리소스가 특정 영역(예: 가상 머신, 관리 디스크, 표준 IP 주소)에 고정됩니다.
- **영역 중복 서비스** – Azure 플랫폼이 영역(예: 영역 중복 스토리지, SQL Database)에서 자동으로 복제됩니다.

> [!NOTE]
> 표준 SKU 공용 IP 주소 및 공용 IP 주소 접두사 리소스 종류 모두에는 “영역 없음” 옵션도 있습니다.  이를 통해 고객은 표준 SKU 공용 IP를 활용하고 표준 SKU만 허용하는 리소스에 연결할 수 있지만, 중복성 보장은 제공되지 않습니다.  (기본에서 표준 SKU로 [업그레이드된](https://docs.microsoft.com/azure/virtual-network/public-ip-upgrade-portal) 모든 공용 IP 주소는 “영역 없음” 유형이 됩니다.)

Azure에서 포괄적인 비즈니스 연속성을 구현하려면 Azure 지역 쌍과 가용성 영역의 조합을 사용하여 애플리케이션 아키텍처를 빌드하십시오. 고가용성에 대한 Azure 지역 내의 가용성 영역을 사용하여 애플리케이션 및 데이터를 동기적으로 복제하고 재해 복구 보호에 대한 Azure 지역에서 비동기적으로 복제할 수 있습니다.
 
![지역에서 차례로 한 영역의 개념 보기](./media/az-overview/az-graphic-two.png)

> [!IMPORTANT]
> 가용성 영역 식별자(위 그림의 숫자 1, 2, 3)는 논리적으로 각 구독의 실제 영역에 독립적으로 매핑됩니다. 즉, 지정된 구독의 가용성 영역 1이 다른 구독의 가용성 영역 1과 다른 실제 영역을 참조할 수 있습니다. 따라서 가상 머신 배치 시 여러 구독에 가용성 영역 ID를 사용하지 않는 것이 좋습니다.

## <a name="region-and-service-categories"></a>지역 및 서비스 범주

권장 지역 및 대체 지역에서 제공되는 서비스를 살펴보면 Azure의 지역별 Azure 서비스 공급 방식을 잘 알 수 있습니다.

- **권장 지역** - 가장 광범위한 서비스 기능을 제공하며 현재 또는 나중에 가용성 영역을 지원하도록 설계된 지역입니다. 이는 Azure Portal에 **권장** 지역으로 지정됩니다.
- **대체(기타) 지역** - 권장 지역이 있는 데이터 보존 경계 내에서 Azure의 공간을 확장하는 지역입니다. 대체 지역은 대기 시간을 최적화하고 재해 복구 요구 사항에 맞는 두 번째 지역을 제공하는 데 도움이 됩니다. 이는 가용성 영역을 지원하도록 설계되지 않았습니다(하지만 Azure가 이러한 지역에 대한 정기적인 평가를 수행하여 권장 지역이 되어야 하는지 확인). 이는 Azure Portal에 **기타** 지역으로 지정됩니다.

### <a name="comparing-region-types"></a>지역 유형 비교

Azure 서비스는 기본, 일반 및 특수 서비스라는 세 가지 범주로 그룹화됩니다. 지정된 지역에 서비스를 배포하는 방법에 대한 Azure의 일반 정책은 주로 지역 유형, 서비스 범주 및 고객 수요를 기반으로 합니다.

- **기본** – 지역이 일반 공급될 때 또는 새 기본 서비스가 일반 공급된 후 90일 이내에 모든 권장 및 대체 지역에서 사용할 수 있습니다.
- **일반** – 지역 일반 공급, 대체 지역의 수요 기반 공급 후 90일 이내에 모든 권장 지역에서 사용할 수 있습니다(대부분은 대다수 대체 지역에 이미 배포됨).
- **특수** – 대상 지정 서비스 제품으로, 주로 특정 산업에 특화되거나 사용자 지정/특수 하드웨어로 구동됩니다. 지역 간에 수요를 기반으로 공급됩니다(대부분은 대다수 권장 지역에 이미 배포됨).

특정 지역에 배포되는 서비스와 특정 지역의 서비스 미리 보기 또는 일반 공급에 대한 향후 로드맵을 보려면 [지역별로 사용 가능한 제품](https://azure.microsoft.com/global-infrastructure/services/?products=all)을 참조하세요.

특정 지역에서 서비스 제공 사항을 사용할 수 없는 경우, Microsoft 영업 담당자에게 연락하여 관심사를 공유할 수 있습니다.

| 영역 형식 | 비지역 | 기본 | 일반 | 특수화 | 가용성 영역 | 데이터 상주 |
| --- | --- | --- | --- | --- | --- | --- |
| 권장 | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | 수요 기반 | :heavy_check_mark: | :heavy_check_mark: |
| 대체 | :heavy_check_mark: | :heavy_check_mark: | 수요 기반 | 수요 기반 | 해당 없음 | :heavy_check_mark: |

### <a name="services-by-category"></a>범주별 서비스

앞서 언급했듯이, Azure는 서비스를 기본,일반, 특수라는 세 가지 범주로 분류합니다. 서비스 범주는 일반 공급 시 할당됩니다. 일반적으로 서비스는 특수 서비스로 수명 주기를 시작하고 수요와 사용률이 늘어남에 따라 일반 또는 기본 서비스로 승격될 수 있습니다. 다음 표에는 기본, 일반 서비스 범주가 나와 있습니다. 이 표에서 다음 사항에 유의해야 합니다.

- 일부 서비스는 비지역 서비스입니다. 자세한 내용과 비지역 서비스 목록은 [지역별로 사용 가능한 제품](https://azure.microsoft.com/global-infrastructure/services/)을 참조하세요.
- 이전 세대의 서비스 또는 가상 머신은 나와 있지 않습니다. 자세한 내용은 [이전 세대의 가상 머신 크기](../virtual-machines/sizes-previous-gen.md) 설명서를 참조하세요.
- 서비스는 GA(일반 공급) 시까지 범주가 할당되지 않습니다. 자세한 내용과 미리 보기 서비스 목록은 [지역별로 사용 가능한 제품](https://azure.microsoft.com/global-infrastructure/services/)을 참조하세요. 

> [!div class="mx-tableFixed"]
> | 기본                           | 일반                                        | 
> |----------------------------------------|---------------------------------------------------|
> | 스토리지 계정                       | API Management                                    | 
> | Application Gateway                    | 앱 구성                                 | 
> | Azure Backup                           | App Service                                       | 
> | Azure Cosmos DB                        | Automation                                        | 
> | Azure Data Lake Storage Gen2           | Azure Active Directory Domain Services            | 
> | Azure ExpressRoute                     | Azure Bastion                                     | 
> | Azure 공용 IP                        | Azure Cache for Redis                             | 
> | Azure SQL Database                     | Azure Cognitive Services                          | 
> | Azure SQL Managed Instance             | Azure Cognitive Services: Computer Vision         | 
> | Disk Storage                           | Azure Cognitive Services: Content Moderator       | 
> | Event Hubs                             | Azure Cognitive Services: Face                    | 
> | Key Vault                              | Azure Cognitive Services: Text Analytics          | 
> | 부하 분산 장치                          | Azure Data Explorer                               | 
> | Service Bus                            | Azure Database for MySQL                          | 
> | Service Fabric                         | Azure Database for PostgreSQL                     | 
> | Storage: 핫/쿨 Blob Storage 계층   | Azure DDoS Protection                             | 
> | Storage: Managed Disks                 | Azure Firewall                                    | 
> | Virtual Machine Scale Sets             | Azure Firewall Manager                            | 
> | Virtual Machines                       | Azure 기능                                   | 
> | Virtual Machines: Azure Dedicated Host | Azure IoT Hub                                     | 
> | Virtual Machines: Av2-Series           | AKS(Azure Kubernetes Service)                    | 
> | Virtual Machines: Bs-Series            | Azure Monitor: Application Insights               | 
> | Virtual Machines: DSv2-Series          | Azure Monitor: Log Analytics                      | 
> | Virtual Machines: DSv3-Series          | Azure Private Link                                | 
> | Virtual Machines: Dv2-Series           | Azure Site Recovery                               | 
> | Virtual Machines: Dv3-Series           | Azure Synapse Analytics                           |     
> | Virtual Machines: ESv3-Series          | Batch                                             | 
> | Virtual Machines: Ev3-Series           | Cloud Services: M-series                          | 
> | Virtual Network                        | Container Instances                               | 
> | VPN Gateway                            | Container Registry                                | 
> |                                        | Data Factory                                      | 
> |                                        | Event Grid                                        | 
> |                                        | HDInsight                                         |  
> |                                        | Logic Apps                                        | 
> |                                        | Media Services                                    | 
> |                                        | Network Watcher                                   | 
> |                                        | 프리미엄 Blob Storage                              | 
> |                                        | 프리미엄 Files Storage                             | 
> |                                        | Virtual Machines: Ddsv4-Series                    | 
> |                                        | Virtual Machines: Ddv4-Series                     | 
> |                                        | Virtual Machines: Dsv4-Series                     | 
> |                                        | Virtual Machines: Dv4-Series                      | 
> |                                        | Virtual Machines: Edsv4-Series                    | 
> |                                        | Virtual Machines: Edv4-Series                     | 
> |                                        | Virtual Machines: Esv4-Series                     | 
> |                                        | Virtual Machines: Ev4-Series                      | 
> |                                        | Virtual Machines: Fsv2-Series                     | 
> |                                        | Virtual Machines: M-Series                        | 
> |                                        | 가상 WAN                                       | 



### <a name="specialized-services"></a>특수 서비스
앞서 언급했듯이, Azure는 서비스를 기본,일반, 특수라는 세 가지 범주로 분류합니다. 서비스 범주는 일반 공급 시 할당됩니다. 일반적으로 서비스는 특수 서비스로 수명 주기를 시작하고 수요와 사용률이 늘어남에 따라 일반 또는 기본 서비스로 승격될 수 있습니다. 다음 표에는 특수 서비스가 나와 있습니다. 

> [!div class="mx-tableFixed"]
> | 특수화                                          |
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
> | Spatial Anchors                                      |
> | Storage: Archive Storage                             |
> | Ultra Disk Storage                                   |
> | 비디오 인덱서                                        |
> | Virtual Machines: DASv4-Series                       |
> | Virtual Machines: DAv4-Series                        |
> | Virtual Machines: DCsv2-series                       |
> | Virtual Machines: EASv4-Series                       |
> | Virtual Machines: EAv4-Series                        |
> | Virtual Machines: HBv1-Series                        |
> | Virtual Machines: HBv2-Series                        |
> | Virtual Machines: HCv1-Series                        |
> | Virtual Machines: H-Series                           |
> | Virtual Machines: LSv2-Series                        |
> | Virtual Machines: Mv2-Series                         |
> | Virtual Machines: NCv3-Series                        |
> | Virtual Machines: NDv2-Series                        |
> | Virtual Machines: NVv3-Series                        |
> | Virtual Machines: NVv4-Series                        | 
> | Virtual Machines: SAP HANA(대규모 인스턴스)  |




## <a name="next-steps"></a>다음 단계

- [Azure에서 가용성 영역을 지원하는 지역](az-region.md)
- [빠른 시작 템플릿](https://aka.ms/azqs)
