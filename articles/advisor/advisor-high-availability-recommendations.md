---
title: Azure Advisor를 사용하여 애플리케이션의 안정성 향상
description: Azure Advisor를 사용하여 중요 비즈니스용 Azure 배포의 안정성을 보장하고 개선합니다.
ms.topic: article
ms.date: 10/26/2021
ms.openlocfilehash: f7ea986424271315843af9557555aa82a708a12c
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131045865"
---
# <a name="improve-the-reliability-of-your-application-by-using-azure-advisor"></a>Azure Advisor를 사용하여 애플리케이션의 안정성 향상

Azure Advisor는 업무상 중요한 애플리케이션의 연속성을 보장하고 향상시키는 데 도움이 됩니다. [Azure Advisor](https://aka.ms/azureadvisordashboard)의 안정성 탭에서 **안정성** 권장 사항을 얻을 수 있습니다.

## <a name="check-the-version-of-your-check-point-network-virtual-appliance-image"></a>Check Point 네트워크 가상 어플라이언스 이미지의 버전 확인

Advisor는 플랫폼 서비스 작업 중에 네트워크 연결을 해제하는 것으로 알려진 Check Point 이미지 버전이 가상 머신에서 실행되고 있는지 여부를 확인할 수 있습니다. Advisor 권장 사항은 이 문제를 해결하는 최신 버전의 이미지로 업그레이드하는 데 도움이 됩니다. 이 검사는 보다 양호한 네트워크 연결을 통해 비즈니스 연속성을 보장합니다.

## <a name="ensure-application-gateway-fault-tolerance"></a>애플리케이션 게이트웨이 내결함성 보장

이 권장 사항은 애플리케이션 게이트웨이에서 제공하는 중요 업무용 애플리케이션의 비즈니스 연속성을 보장합니다. Advisor는 내결함성이 구성되지 않은 애플리케이션 게이트웨이 인스턴스를 식별합니다. 그런 다음, 사용자가 수행할 수 있는 수정 작업을 제안합니다. Advisor는 중간 규모 또는 대규모 단일 인스턴스 애플리케이션 게이트웨이를 식별하고 하나 이상의 인스턴스 추가를 권장합니다. 또한 단일 인스턴스 및 다중 인스턴스 소규모 애플리케이션 게이트웨이를 식별하고 중간 규모 또는 대규모 SKU로 마이그레이션을 권장합니다. Advisor는 애플리케이션 게이트웨이 인스턴스가 이러한 리소스에 대한 현재 SLA 요구 사항을 만족하도록 구성되어 있는지 확인하기 위해 이러한 작업을 권장합니다.

## <a name="protect-your-virtual-machine-data-from-accidental-deletion"></a>가상 머신 데이터를 실수로 삭제되지 않도록 보호

가상 머신 백업 설정은 비즈니스에 중요한 데이터의 가용성을 보장하고 실수로 인한 삭제 또는 손상에 대한 보호를 제공합니다. Advisor는 백업이 활성화되지 않은 가상 머신을 식별하고 백업을 활성화할 것을 권장합니다. 

## <a name="ensure-you-have-access-to-azure-experts-when-you-need-it"></a>필요할 때 Azure 전문가에게 문의할 수 있는지 확인

중요 비즈니스용 워크로드를 실행하는 경우 필요할 때 기술 지원 팀에게 문의할 수 있어야 합니다. Advisor는 지원 플랜에 기술 지원이 포함되지 않은 잠재적인 중요 비즈니스용 구독을 식별합니다. 기술 지원이 포함된 옵션으로 업그레이드할 것을 권장합니다.

## <a name="create-azure-service-health-alerts-to-be-notified-when-azure-problems-affect-you"></a>Azure 문제가 영향을 미칠 때 알림을 받도록 Azure Service Health 경고 만들기

Azure 문제가 영향을 미칠 때 알림을 받도록 Azure Service Health 경고를 설정하는 것이 좋습니다. [Azure Service Health](https://azure.microsoft.com/features/service-health/)는 Azure 서비스 문제가 영향을 미칠 때 개인화된 지침과 지원을 제공하는 무료 서비스입니다. Advisor는 경고가 구성되지 않은 구독을 식별하고 구독을 구성하도록 권장합니다.

## <a name="configure-traffic-manager-endpoints-for-resiliency"></a>복원력을 위해 Traffic Manager 엔드포인트 구성

여러 엔드포인트가 있는 Azure Traffic Manager 프로필은 특정 엔드포인트가 실패할 경우 가용성이 더 높습니다. 서로 다른 지역에 엔드포인트를 배치하면 서비스 복원력을 더 높일 수 있습니다. Advisor는 엔드포인트가 하나뿐인 Traffic Manager 프로필을 식별하고 다른 지역에 하나 이상의 엔드포인트를 더 추가하도록 권장합니다.

근접 라우팅에 대해 구성된 Traffic Manager 프로필의 모든 엔드포인트가 같은 지역에 있는 경우 다른 지역의 사용자는 연결 지연을 경험할 수 있습니다. 엔드포인트를 다른 지역에 추가하거나 다른 지역으로 이동하면 한 지역의 모든 엔드포인트가 실패하는 경우 가용성을 더 높일 수 있습니다. Advisor는 모든 엔드포인트가 같은 지역에 있는 근접 라우팅에 대해 구성된 Traffic Manager 프로필을 식별합니다. 다른 Azure 지역에 엔드포인트를 추가하거나 이동하는 것이 좋습니다.

Traffic Manager 프로필이 지리적 라우팅을 사용하도록 구성된 경우 트래픽은 정의된 지역에 따라 엔드포인트로 라우팅됩니다. 한 지역이 실패하면 미리 정의된 장애 조치(failover)가 없습니다. 지역 그룹화가 **모두(세계)** 로 구성된 엔드포인트가 있으면 트래픽 폐기를 방지하고 서비스 가용성을 높일 수 있습니다. Advisor는 지역 그룹화가 **모두(세계)** 로 구성된 엔드포인트가 없으며 지리적 라우팅을 사용하도록 구성된 Traffic Manager 프로필을 식별합니다. 엔드포인트를 **모두(세계)** 로 만들도록 구성을 변경할 것을 권장합니다.

## <a name="use-soft-delete-on-your-azure-storage-account-to-save-and-recover-data-after-accidental-overwrite-or-deletion"></a>실수로 데이터를 덮어쓰거나 삭제한 후에 데이터를 저장하고 복구할 수 있도록 Azure Storage 계정에 대해 일시 삭제 기능 사용

삭제한 Blob가 영구적으로 삭제되지 않고 일시 삭제된 상태로 전환되도록 스토리지 계정에 대해 [일시 삭제](../storage/blobs/soft-delete-blob-overview.md)를 사용하도록 설정합니다. 데이터를 덮어쓰는 경우 덮어쓴 데이터의 상태를 저장하기 위해 일시 삭제된 스냅샷이 생성됩니다. 일시 삭제 기능을 사용하면 실수로 삭제하거나 덮어쓰는 경우 복구할 수 있습니다. Advisor는 일시 삭제가 사용하도록 설정되지 않은 Azure Storage 계정을 확인하여 해당 기능을 사용하도록 설정할 것을 제안합니다.

## <a name="configure-your-vpn-gateway-to-active-active-for-connection-resiliency"></a>연결 복원을 위해 VPN 게이트웨이를 활성-활성으로 구성

활성-활성 구성에서 두 VPN 게이트웨이 인스턴스는 모두 온-프레미스 VPN 디바이스에 대한 S2S VPN 터널을 설정합니다. 게이트웨이 인스턴스 하나에서 계획된 유지 관리 이벤트 또는 계획되지 않은 이벤트가 발생하면 트래픽이 다른 활성 IPsec 터널로 자동 전환됩니다. Azure Advisor는 활성-활성으로 구성되지 않은 VPN 게이트웨이를 식별하여 고가용성을 유지할 수 있도록 해당 게이트웨이를 구성할 것을 제안합니다.

## <a name="use-production-vpn-gateways-to-run-your-production-workloads"></a>프로덕션 VPN 게이트웨이를 사용하여 프로덕션 워크로드 실행

Azure Advisor는 기본 SKU를 사용하는 VPN 게이트웨이를 확인하고 프로덕션 SKU를 대신 사용할 것을 권장합니다. 기본 SKU는 개발 및 테스트용으로 설계되었습니다. 프로덕션 SKU는 다음을 제공합니다.
- 추가 터널. 
- BGP 지원. 
- 활성-활성 구성 옵션. 
- 사용자 지정 Ipsec/IKE 정책. 
- 더 높은 안정성 및 가용성.

## <a name="ensure-reliable-outbound-connectivity-with-vnet-nat"></a>VNet NAT를 통해 안정적인 아웃바운드 연결 보장
표준 Load Balancer 또는 다른 Azure 리소스에서 제공하는 기본 아웃바운드 연결을 사용하면 연결 실패(SNAT 포트 소모라고도 함)가 발생하기 때문에 프로덕션 워크로드에는 권장되지 않습니다. 이 경우 연결 실패를 방지하는 VNet NAT를 사용하는 것이 좋습니다. NAT는 애플리케이션이 포트를 벗어나지 않도록 원활하게 확장할 수 있습니다. [VNet NAT 에 대해 자세히 알아봅니다.](../virtual-network/nat-gateway/nat-overview.md)

## <a name="ensure-virtual-machine-fault-tolerance-temporarily-disabled"></a>가상 머신 내결함성 보장(일시적으로 사용할 수 없음)

애플리케이션에 중복성을 제공하기 위해 여러 개의 가상 머신을 가용성 집합으로 그룹화하는 것이 좋습니다. Advisor는 가용성 집합에 속하지 않는 가상 머신을 식별하고 이러한 가상 머신을 가용성 집합으로 이동할 것을 권장합니다. 이 구성은 계획된 유지 관리 또는 계획되지 않은 유지 관리 중에 적어도 하나의 가상 머신을 사용할 수 있고 Azure 가상 머신 SLA가 충족되도록 합니다. 가상 머신에 대한 가용성 집합을 만들거나 기존 가용성 집합에 가상 머신을 추가할 수도 있습니다.

> [!NOTE]
> 가용성 집합을 만들도록 선택한 경우 해당 가용성 집합에 적어도 하나 이상의 가상 머신을 추가해야 합니다. 중단 시간 동안 하나 이상의 컴퓨터를 사용할 수 있도록 하기 위해 가용성 집합에 둘 이상의 가상 머신을 그룹화하는 것이 좋습니다.

## <a name="ensure-availability-set-fault-tolerance-temporarily-disabled"></a>가용성 집합 내결함성 보장(일시적으로 사용할 수 없음)

애플리케이션에 중복성을 제공하기 위해 여러 개의 가상 머신을 가용성 집합으로 그룹화하는 것이 좋습니다. Advisor는 단일 가상 머신을 포함하는 가용성 집합을 식별하고 여기에 하나 이상의 가상 머신을 추가할 것을 권장합니다. 이 구성은 계획된 유지 관리 또는 계획되지 않은 유지 관리 중에 적어도 하나의 가상 머신을 사용할 수 있고 Azure 가상 머신 SLA가 충족되도록 합니다. 가상 머신을 만들거나 기존 가상 머신을 가용성 집합에 추가할 수 있습니다.  

## <a name="use-managed-disks-to-improve-data-reliability"></a>관리 디스크를 사용하여 데이터 안정성 향상

스토리지 계정 또는 스토리지 배율 단위를 공유하는 디스크가 있는 가용성 집합의 가상 머신은 중단 중에 단일 스토리지 배율 단위 오류에 대해 복원력이 없습니다. Azure Managed Disks로 마이그레이션하여 가용성 집합에 있는 다른 VM의 디스크가 단일 실패 지점을 방지하기에 충분히 격리되도록 합니다.

## <a name="repair-invalid-log-alert-rules"></a>잘못된 로그 경고 규칙 복구

Azure Advisor는 조건 섹션에 잘못된 쿼리가 지정된 로그 경고 규칙을 검색합니다. Azure Monitor 로그 경고 규칙은 지정된 빈도로 쿼리를 실행하고 결과에 따라 경고를 발생시킵니다. 시간이 지나면서 참조되는 리소스, 테이블 또는 명령의 변경 때문에 쿼리가 잘못될 수 있습니다. Advisor는 규칙이 자동으로 해제되지 않도록 방지하고 모니터링 범위를 보장하기 위해 경고 쿼리를 수정할 것을 권장합니다. 자세한 내용은 [경고 규칙 문제 해결](../azure-monitor/alerts/alerts-troubleshoot-log.md#query-used-in-a-log-alert-isnt-valid)을 참조하세요.

## <a name="configure-consistent-indexing-mode-on-your-azure-cosmos-db-collection"></a>Azure Cosmos DB 컬렉션에 일관적인 인덱싱 모드 구성

지연 인덱싱 모드를 사용하여 Azure Cosmos DB 컨테이너를 구성하면 쿼리 결과의 새로 고침에 영향을 줄 수 있습니다. Advisor는 이러한 방식으로 구성된 컨테이너를 검색하고 일관성 모드로 전환할 것을 권장합니다. [Azure Cosmos DB의 인덱싱 정책에 대한 자세한 정보](../cosmos-db/how-to-manage-indexing-policy.md)

## <a name="configure-your-azure-cosmos-db-containers-with-a-partition-key"></a>파티션 키를 사용하여 Azure Cosmos DB 컨테이너 구성

Azure Advisor는 프로비전된 스토리지 할당량에 근접하고 있는 Azure Cosmos DB의 분할되지 않은 컬렉션을 식별합니다. 서비스에 의해 자동으로 확장될 수 있도록 파티션 키 정의를 사용하여 이러한 컬렉션을 새 컬렉션으로 마이그레이션할 것을 권장합니다. [파티션 키 선택에 대한 자세한 정보](../cosmos-db/partitioning-overview.md)

## <a name="upgrade-your-azure-cosmos-db-net-sdk-to-the-latest-version-from-nuget"></a>NuGet에서 Azure Cosmos DB .NET SDK를 최신 버전으로 업그레이드

Azure Advisor는 이전 버전의 .NET SDK를 사용하는 Azure Cosmos DB 계정을 식별합니다. 최신 픽스, 성능 향상 및 기능을 위해 NuGet에서 최신 버전으로 업그레이드할 것을 권장합니다. [Azure Cosmos DB .NET SDK에 대한 자세한 정보](../cosmos-db/sql-api-sdk-dotnet-standard.md)

## <a name="upgrade-your-azure-cosmos-db-java-sdk-to-the-latest-version-from-maven"></a>Azure Cosmos DB Java SDK를 최신 버전의 Maven으로 업그레이드

Azure Advisor는 이전 버전의 Java SDK를 사용하는 Azure Cosmos DB 계정을 식별합니다. 최신 픽스, 성능 향상 및 기능을 위해 최신 버전의 Maven으로 업그레이드할 것을 권장합니다. [Azure Cosmos DB Java SDK에 대한 자세한 정보](../cosmos-db/sql-api-sdk-java-v4.md)

## <a name="upgrade-your-azure-cosmos-db-spark-connector-to-the-latest-version-from-maven"></a>Azure Cosmos DB Spark Connector를 Maven에서 최신 버전으로 업그레이드

Azure Advisor는 이전 버전의 Azure Cosmos DB Spark 커넥터를 사용하는 Azure Cosmos DB 계정을 식별합니다. 최신 픽스, 성능 향상 및 기능을 위해 최신 버전의 Maven으로 업그레이드할 것을 권장합니다. [Azure Cosmos DB Spark 커넥터에 대한 자세한 정보](../cosmos-db/create-sql-api-spark.md)

## <a name="consider-moving-to-kafka-21-on-hdinsight-40"></a>HDInsight 4.0의 Kafka 2.1로 전환 고려

2020년 7월 1일부터 Azure HDInsight 4.0의 Kafka 1.1을 사용하여 새 Kafka 클러스터를 만들 수 없습니다. 기존 클러스터는 Microsoft의 지원 없이 있는 그대로 실행됩니다. 잠재적인 시스템/지원 중단을 방지하기 위해 2020년 6월 30일까지 HDInsight 4.0의 Kafka 2.1로 전환하는 것이 좋습니다.

## <a name="consider-upgrading-older-spark-versions-in-hdinsight-spark-clusters"></a>HDInsight Spark 클러스터의 이전 Spark 버전 업그레이드 고려

2020년 7월 1일부터 HDInsight 3.6의 Spark 2.1 또는 2.2를 사용하여 새 Kafka 클러스터를 만들 수 없습니다. HDInsight 4.0의 Spark 2.3을 사용하여 새 Kafka 클러스터를 만들 수 없습니다. 기존 클러스터는 Microsoft의 지원 없이 있는 그대로 실행됩니다. 

## <a name="enable-virtual-machine-replication"></a>가상 머신 복제 사용
다른 지역으로 복제하도록 설정되지 않은 가상 머신은 지역의 가동 중단 시 복원되지 않습니다. 가상 머신을 복제하면 Azure 지역 가동 중단 시 비즈니스에 대한 부정적인 영향이 줄어듭니다. Advisor는 복제가 설정되지 않은 VM을 검색하고 사용하도록 설정할 것을 권장합니다. 복제를 사용하도록 설정하면 가동 중단이 발생할 경우 원격 Azure 지역의 가상 머신을 신속하게 가져올 수 있습니다. [가상 머신 복제에 대한 자세한 정보](../site-recovery/azure-to-azure-quickstart.md)

## <a name="upgrade-to-the-latest-version-of-the-azure-connected-machine-agent"></a>최신 버전 Azure Connected Machine 에이전트로 업그레이드
[Azure Connected Machine 에이전트](../azure-arc/servers/manage-agent.md)는 버그 수정, 안정성 향상 및 새로운 기능을 통해 정기적으로 업데이트됩니다. 최신 버전의 컴퓨터 에이전트에서 작동하지 않는 리소스가 식별되었으며, 이 Advisor 권장 사항은 최상의 Azure Arc 환경을 위해 에이전트를 최신 버전으로 업그레이드할 것을 제안합니다.

## <a name="do-not-override-hostname-to-ensure-website-integrity"></a>웹 사이트 무결성을 보장하기 위해 호스트 이름을 재정의하지 마세요.
Advisor는 Application Gateway를 구성할 때 호스트 이름을 재정의하지 않을 것을 권장합니다. Application Gateway 프런트 엔드에 백 엔드에 액세스하는 데 사용되는 도메인과 다른 도메인이 있으면 잠재적으로 쿠키 또는 리디렉션 URL이 손상될 수 있습니다. 이는 모든 상황에서 발생하지 않을 수 있으며, 일반적으로 백 엔드의 특정 범주(예: REST API)는 이에 대해 영향을 적게 받습니다. 백 엔드에 대한 호스트 이름을 덮어쓸 필요가 없도록 백 엔드가 이를 처리하거나 Application Gateway 구성을 업데이트할 수 있는지 확인하세요. App Service 함께 사용하는 경우 웹앱에 사용자 지정 도메인 이름을 연결하고 호스트 이름을 백 엔드에 사용하지 않도록 `*.azurewebsites.net` 합니다. [사용자 지정 도메인에 대한 자세한 정보](../application-gateway/troubleshoot-app-service-redirection-app-service-url.md)

## <a name="next-steps"></a>다음 단계

Advisor 권장 사항에 대한 자세한 내용은 다음을 참조하세요.
* [Advisor 소개](advisor-overview.md)
* [Advisor 시작](advisor-get-started.md)
* [Advisor 점수](azure-advisor-score.md)
* [Advisor 비용 권장 사항](advisor-cost-recommendations.md)
* [Advisor 성능 권장 사항](advisor-performance-recommendations.md)
* [Advisor 보안 권장 사항](advisor-security-recommendations.md)
* [Advisor 뛰어난 운영 권장 사항](advisor-operational-excellence-recommendations.md)
