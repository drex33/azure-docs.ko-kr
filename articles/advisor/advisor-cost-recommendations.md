---
title: Azure Advisor를 사용하여 서비스 비용 절감
description: Azure Advisor를 사용하여 Azure 배포 비용을 최적화합니다.
ms.topic: article
ms.date: 10/29/2021
ms.openlocfilehash: 099a68dc637380696990c19f404b8da1e5abc419
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2021
ms.locfileid: "132706862"
---
# <a name="reduce-service-costs-by-using-azure-advisor"></a>Azure Advisor를 사용하여 서비스 비용 절감

Azure Advisor는 유휴 및 사용 미달 리소스를 식별하여 전체적인 Azure 사용을 최적화하고 줄이는 데 도움을 줍니다. Advisor 대시보드의 **비용** 탭에서 비용 관련 권장 지침을 얻을 수 있습니다.

## <a name="how-to-access-cost-recommendations-in-azure-advisor"></a>Azure Advisor에서 비용 권장 사항에 액세스하는 방법

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

1. 아무 페이지에서나 [**Advisor**](https://aka.ms/azureadvisordashboard)를 검색하고 선택합니다.

1. **Advisor** 대시보드에서 **비용** 탭을 선택합니다.

## <a name="optimize-virtual-machine-spend-by-resizing-or-shutting-down-underutilized-instances"></a>사용량이 낮은 인스턴스의 크기를 조정하거나 종료하여 가상 머신 소비 최적화 

특정 애플리케이션 시나리오에서는 기본적으로 사용률이 낮을 수 있으나 가상 머신의 크기와 수를 관리하여 비용을 절감할 수도 있습니다. 

권장되는 작업은 평가 중인 리소스에 따라 종료하거나 크기를 조정하는 것입니다.

Advisor의 고급 평가 모델은 다음 문이 모두 true인 경우 가상 머신 종료를 고려합니다. 
- CPU 사용률 최댓값의 95%가 3% 미만입니다. 
- 네트워크 사용률은 7일 동안 2% 미만입니다.
- 메모리 압력이 임계값보다 낮습니다.

Advisor는 다음과 같은 경우 더 작은 SKU(동일한 SKU 제품군 내) 또는 더 적은 인스턴스 수로 현재 부하를 처리할 수 있을 때 가상 머신 크기 조정을 고려합니다.
- 사용자 쪽이 아닌 워크로드에서 현재 부하가 80% 사용률을 넘지 않습니다. 
- 사용자 쪽 워크로드의 경우 부하는 40%를 넘지 않습니다. 

여기에서 Advisor는 워크로드의 CPU 사용률 특성을 분석하여 워크로드의 유형을 결정합니다.

Advisor는 두 가지 권장 사항인 크기 조정 또는 종료의 예상 비용 절감액을 보여 줍니다. 크기 조정의 경우 Advisor는 현재 및 대상 SKU 정보를 제공합니다.

사용량이 낮은 가상 머신을 더 적극적으로 식별하고 싶은 경 구독당 CPU 사용률 규칙을 조정할 수 있습니다.

## <a name="optimize-spend-for-mariadb-mysql-and-postgresql-servers-by-right-sizing"></a>적절한 크기 조정으로 MariaDB, MySQL, PostgreSQL 서버에 대한 비용 최적화 
Advisor는 사용자의 사용량을 분석하고, MariaDB, MySQL, PostgreSQL 데이터베이스 서버 리소스가 지난 7일 동안 오랜 시간 충분히 사용되지 않았는지 평가합니다. 리소스 사용률이 낮으면 원치 않는 지출이 발생하며, 이는 성능에 영향을 크게 미치지 않고 해결할 수 있습니다. 비용을 줄이고 리소스를 효율적으로 관리하려면 컴퓨팅 크기(vCore)를 절반으로 줄이는 것이 좋습니다.

## <a name="reduce-costs-by-eliminating-unprovisioned-expressroute-circuits"></a>프로비저닝되지 않은 ExpressRoute 회로를 제거하여 비용 절감

Advisor는 공급자 상태가 1개월 넘게 **프로비저닝되지 않음** 인 Azure ExpressRoute 회로를 식별합니다. 그리고 연결 공급자를 사용하여 회로를 프로비저닝하지 않으려는 경우 회로를 삭제하도록 권장합니다.

## <a name="reduce-costs-by-deleting-or-reconfiguring-idle-virtual-network-gateways"></a>유휴 상태의 가상 네트워크 게이트웨이를 삭제하거나 다시 구성하여 비용 절감

Advisor는 90일 넘게 유휴 상태였던 가상 네트워크 게이트웨이를 식별합니다. 게이트웨이는 시간당 요금이 부과되므로 다시 구성하거나, 더 이상 사용하지 않으려는 경우 삭제하는 것이 좋습니다. 

## <a name="buy-reserved-virtual-machine-instances-to-save-money-over-pay-as-you-go-costs"></a>예약 가상 머신 인스턴스를 구매하여 종량제 비용보다 비용 절약

Advisor는 지난 30일 동안 가상 머신 사용량을 검토하고 Azure 예약을 구매하여 비용을 절감할 수 있는지 확인합니다. Advisor는 절감 가능성이 가장 높은 지역 및 크기와 구매 예약의 예상 절감 비용을 보여 줍니다. Azure 예약을 사용하여 가상 머신에 대한 기본 비용을 미리 구입할 수 있습니다. 할인은 예약과 동일한 크기 및 지역이 있는 새로운 VM 또는 기존 VM에 자동으로 적용됩니다. [Azure Reserved VM Instances에 대한 자세한 정보](https://azure.microsoft.com/pricing/reserved-vm-instances/)

또한 Advisor는 30일 이내에 만료되는 예약 인스턴스를 알려줍니다. 종량제 요금을 사용하지 않으려면 새 예약 인스턴스를 구매하는 것이 좋습니다.

## <a name="buy-reserved-instances-for-several-resource-types-to-save-over-your-pay-as-you-go-costs"></a>종량제 비용을 절약하기 위해 여러 리소스 종류에 대한 예약 인스턴스 구매

Advisor는 다음 리소스에 대한 지난 30일간의 사용 패턴을 분석하고 비용을 최적화하는 예약된 용량 구매를 권장합니다.

### <a name="azure-cosmos-db-reserved-capacity"></a>Azure Cosmos DB 예약된 용량
Advisor는 지난 30일간의 Azure Cosmos DB 사용 패턴을 분석하고 비용을 최적화하기 위해 예약된 용량 구매를 권장합니다. 예약된 용량을 사용하면 시간별 Azure Cosmos DB 사용량을 사전 구매하고 종량제 비용을 절감할 수 있습니다. 예약된 용량은 요금 청구 혜택이며 새 배포와 기존 배포에 자동으로 적용됩니다. Advisor는 3년 예약 가격과 지난 30일 동안의 사용 패턴을 추정하여 개별 구독별로 절감 추정액을 계산합니다. 공유 범위 권장 사항은 예약된 용량 구매에 사용할 수 있으며 비용 절감 효과를 높일 수 있습니다.

### <a name="sql-database-and-sql-managed-instance-reserved-capacity"></a>SQL Database 및 SQL Managed Instance 예약된 용량
Advisor는 지난 30일간의 SQL Database 및 SQL Managed Instance 사용 패턴을 분석합니다. 그런 다음 비용을 최적화하는 예약된 용량 구매를 권장합니다. 예약된 용량을 사용하면 시간별 SQL DB 사용량을 미리 구매하여 SQL 계산 비용을 절감할 수 있습니다. SQL 라이선스는 별도로 청구되며 예약에 의해 할인되지 않습니다. 예약된 용량은 요금 청구 혜택이며 새 배포와 기존 배포에 자동으로 적용됩니다. Advisor는 3년 예약 가격과 지난 30일 동안의 사용 패턴을 추정하여 개별 구독별로 절감 추정액을 계산합니다. 공유 범위 권장 사항은 예약된 용량 구매에 사용할 수 있으며 비용 절감 효과를 높일 수 있습니다. 자세한 내용은 [Azure SQL Database 및 SQL Managed Instance 예약된 용량](../azure-sql/database/reserved-capacity-overview.md)을 참조하세요.

### <a name="app-service-stamp-fee-reserved-capacity"></a>App Service 인지세 예약된 용량
Advisor는 Azure App Service의 격리된 환경에 대한 지난 30일간의 인지세 사용 패턴을 분석하고 비용을 최적화하는 예약된 용량 구매를 권장합니다. 예약된 용량을 사용하여 격리된 환경 인지세에 대한 시간별 사용량을 미리 구매하면 종량제 대비 비용을 절감할 수 있습니다. 예약된 용량은 App Service 인스턴스가 아닌 인지세에만 적용됩니다. 예약된 용량은 요금 청구 혜택이며 새 배포와 기존 배포에 자동으로 적용됩니다. Advisor는 지난 30일 동안의 사용 패턴을 기반으로 3년 예약 가격을 사용하여 개별 구독별로 절감 추정액을 계산합니다.

### <a name="blob-storage-reserved-capacity"></a>Blob Storage 예약된 용량
Advisor는 지난 30일간의 Azure Blob Storage와 Azure Data Lake 스토리지 사용량을 분석합니다. 그런 다음 비용을 최적화하는 예약된 용량 구매를 계산합니다. 예약된 용량으로 시간별 사용량을 미리 구매하면 현재 주문형 대비 비용을 절감할 수 있습니다. Blob Storage 예약된 용량은 Azure Blob 범용 v2와 Azure Data Lake Storage Gen2에 저장된 데이터에만 적용됩니다. 예약된 용량은 요금 청구 혜택이며 새 배포와 기존 배포에 자동으로 적용됩니다. Advisor는 3년 예약 가격과 지난 30일 동안 관찰된 사용 패턴을 사용하여 개별 구독별로 절감 추정액을 계산합니다. 공유 범위 권장 사항은 예약된 용량 구매에 사용할 수 있으며 비용 절감 효과를 높일 수 있습니다.

### <a name="mariadb-mysql-and-postgresql-reserved-capacity"></a>MariaDB, MySQL, PostgreSQL 예약된 용량
Advisor는 지난 30일간의 Azure Database for MariaDB, Azure Database for MySQL, Azure Database for PostgreSQL 사용 패턴을 분석합니다. 그런 다음 비용을 최적화하는 예약된 용량 구매를 권장합니다. 예약된 용량을 사용하면 MariaDB, MySQL, PostgreSQL의 시간별 사용량을 사전 구매하고 현재 비용을 절감할 수 있습니다. 예약된 용량은 요금 청구 혜택이며 새 배포와 기존 배포에 자동으로 적용됩니다. Advisor는 3년 예약 가격과 지난 30일 동안 관찰된 사용 패턴을 사용하여 개별 구독별로 절감 추정액을 계산합니다. 공유 범위 권장 사항은 예약된 용량 구매에 사용할 수 있으며 비용 절감 효과를 높일 수 있습니다.

### <a name="azure-synapse-analytics-reserved-capacity"></a>Azure Synapse Analytics 예약된 용량
Advisor는 지난 30일간의 Azure Synapse Analytics 사용 패턴을 분석하고 비용을 최적화하기 위해 예약된 용량 구매를 권장합니다. 예약된 용량을 사용하여 시간별 Synapse Analytics 사용량을 미리 구매하면 주문형 대비 비용을 절감할 수 있습니다. 예약된 용량은 요금 청구 혜택이며 새 배포와 기존 배포에 자동으로 적용됩니다. Advisor는 3년 예약 가격과 지난 30일 동안 관찰된 사용 패턴을 사용하여 개별 구독별로 절감 추정액을 계산합니다. 공유 범위 권장 사항은 예약된 용량 구매에 사용할 수 있으며 비용 절감 효과를 높일 수 있습니다.

## <a name="delete-unassociated-public-ip-addresses-to-save-money"></a>비용 절감을 위해 연결되지 않은 공용 IP 주소 삭제

Advisor는 부하 분산 장치 및 VM과 같은 Azure 리소스와 연결되지 않은 공용 IP 주소를 식별합니다. 연결되지 않은 공용 IP 주소에는 소액의 요금이 부과됩니다. 공용 IP 주소를 사용할 계획이 없다면 주소를 삭제하여 비용을 절감할 수 있습니다.

## <a name="delete-azure-data-factory-pipelines-that-are-failing"></a>실패한 Azure Data Factory 파이프라인 삭제

Advisor는 반복적으로 실패하는 Azure Data Factory 파이프라인을 검색합니다. 문제를 해결하거나 해당 파이프라인이 필요하지 않은 경우 파이프라인을 삭제하는 것이 좋습니다. 오류가 발생하는 동안에는 서비스를 제공하지 않더라도 해당 파이프라인에 대한 요금이 청구됩니다.

## <a name="use-standard-snapshots-for-managed-disks"></a>관리 디스크에 Standard Storage 사용
60%의 비용을 절감하려면 부모 디스크의 스토리지 유형과 관계없이 Standard Storage에 스냅샷을 저장하는 것이 좋습니다. 해당 옵션은 관리 디스크 스냅샷의 기본 옵션입니다. Advisor는 Premium Storage에 저장된 스냅샷을 식별하고 Premium Storage에서 Standard Storage로 마이그레이션하는 것을 권장합니다. [관리 디스크 가격에 대해 자세히 알아보세요.](https://aka.ms/aa_manageddisksnapshot_learnmore)

## <a name="use-lifecycle-management"></a>수명 주기 관리 사용
Advisor는 Azure Blob Storage 개체 수, 총 크기, 트랜잭션에 대한 인텔리전스를 사용하여 하나 이상의 스토리지 계정에서 데이터를 계층화하는 수명 주기 관리를 사용하도록 설정해야 하는지 검색합니다. 자동으로 데이터를 쿨 또는 보관으로 계층화하여 스토리지 비용을 최적화하는 동시에 애플리케이션 호환성을 위해 Azure Blob Storage에 데이터를 유지하는 수명 주기 관리 규칙을 만들라는 메시지가 표시됩니다.

## <a name="create-an-ephemeral-os-disk-recommendation"></a>사용 후 삭제되는 OS 디스크 만들기 권장 사항
[임시 OS 디스크](../virtual-machines/ephemeral-os-disks.md)를 사용하면 다음을 수행할 수 있습니다. 
- OS 디스크에 대한 스토리지 비용을 절약합니다. 
- OS 디스크에 대한 읽기/쓰기 대기 시간을 단축합니다. 
- OS 및 임시 디스크를 원래 상태로 다시 설정하여 VM 이미지로 다시 설치하는 작업을 더 빠르게 수행합니다.

수명이 짧은 IaaS VM 또는 상태 비저장 워크로드를 사용하는 VM의 경우 임시 OS 디스크를 사용하는 것이 좋습니다. Advisor는 임시 OS 디스크를 사용할 수 있는 리소스에 대한 권장 사항을 제공합니다.

## <a name="reduce-azure-data-explorer-table-cache-period-policy-for-cluster-cost-optimization-preview"></a>클러스터 비용 최적화를 위해 Azure Data Explorer 테이블 캐시 기간(정책) 줄이기(미리 보기)
Advisor는 테이블 캐시 정책을 줄이면 CPU 사용률, 메모리, 높은 캐시 크기 구성이 있는 Azure Data Explorer 클러스터 노드를 확보할 수 있는 리소스를 식별합니다.

## <a name="configure-manual-throughput-instead-of-autoscale-on-your-azure-cosmos-db-database-or-container"></a>Azure Cosmos DB 데이터베이스 또는 컨테이너에서 자동 크기 조정 대신 수동 처리량을 구성합니다.
지난 7일 동안의 사용량을 기준으로 자동 크기 조정 대신 수동 처리량을 사용하여 절약할 수 있습니다. 최대 처리량(RU/s)의 평균 사용률이 66%보다 크거나 10% 이하인 경우 수동 처리량이 더 비용 효율적입니다. 비용 절감액은 지난 7일 동안의 사용량을 기준으로 권장되는 수동 처리량을 사용하여 절감할 수 있는 잠재적 절감액을 나타냅니다. 실제 절감액은 설정한 수동 처리량과 처리량의 평균 사용률이 분석된 기간과 계속 유사한지 여부에 따라 달라질 수 있습니다. 예상 절감액은 사용자 계정에 적용될 수 있는 할인을 고려하지 않습니다.

## <a name="enable-autoscale-on-your-azure-cosmos-db-database-or-container"></a>Azure Cosmos DB 데이터베이스 또는 컨테이너에서 자동 크기 조정 사용
지난 7일 동안의 사용량에 따라 자동 크기 조정을 사용하도록 설정하여 비용을 절감할 수 있습니다. 각 시간에 대해 프로비저닝된 RU/s를 RU/s의 실제 사용률(자동으로 크기 조정된 사용률)과 비교하고 해당 기간 동안 비용 절감액을 계산했습니다. 자동 크기 조정은 사용하지 않을 때 RU/s를 축소하여 비용을 최적화하는 데 도움이 됩니다.

## <a name="next-steps"></a>다음 단계

Advisor 권장 사항에 대한 자세한 내용은 다음을 참조하세요.
* [Advisor 소개](advisor-overview.md)
* [Advisor 점수](azure-advisor-score.md)
* [Advisor 시작](advisor-get-started.md)
* [Advisor 성능 권장 사항](advisor-performance-recommendations.md)
* [Advisor 고가용성 권장 사항](advisor-high-availability-recommendations.md)
* [Advisor 보안 권장 사항](advisor-security-recommendations.md)
* [Advisor 뛰어난 운영 권장 사항](advisor-operational-excellence-recommendations.md)
