---
title: AKS 비즈니스 연속성 및 재해 복구에 대한 모범 사례
description: AKS(Azure Kubernetes Service)에서 고가용성 및 재해 복구 준비를 제공하여 애플리케이션에 대한 최대 작동 시간을 제공하는 클러스터 운영자의 모범 사례를 알아봅니다.
services: container-service
author: lastcoolnameleft
ms.topic: conceptual
ms.date: 03/11/2021
ms.author: thfalgou
ms.custom: fasttrack-edit
ms.openlocfilehash: eba7d2eeaff11e16566faf2388f449badc805404
ms.sourcegitcommit: 93c7420c00141af83ed3294923b4826dd4dc6ff2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/02/2021
ms.locfileid: "133435883"
---
# <a name="best-practices-for-business-continuity-and-disaster-recovery-in-azure-kubernetes-service-aks"></a>AKS(Azure Kubernetes Services)의 비즈니스 연속성 및 재해 복구 모범 사례

AKS(Azure Kubernetes Services)에서 클러스터를 관리할 때 애플리케이션 가동 시간이 중요합니다. 기본적으로 AKS는 [VMSS(가상 머신 확장 집합)](../virtual-machine-scale-sets/overview.md)에서 여러 노드를 사용하여 고가용성을 제공합니다. 이러한 여러 노드는 Azure 지역 실패로부터 시스템을 보호하지 않습니다. 작동 시간을 최대화하려면 비즈니스 연속성을 유지하고 재해 복구를 준비하기 위해 미리 계획을 세워야 합니다.

이 문서에서는 AKS 비즈니스 연속성 및 재해 복구 계획 방법을 집중적으로 살펴보았습니다. 다음 방법을 알아봅니다.

> [!div class="checklist"]
> * 여러 지역에서 AKS 클러스터 계획 작성.
> * Azure Traffic Manager를 사용하여 여러 클러스터에 트래픽 라우팅.
> * 컨테이너 이미지 레지스트리에 지역에서 복제 사용.
> * 여러 클러스터에서 애플리케이션 상태 계획 작성.
> * 여러 지역의 스토리지 복제.

## <a name="plan-for-multiregion-deployment"></a>다중 지역 배포 계획 작성

> **모범 사례**:
>
> 여러 AKS 클러스터를 배포하는 경우 AKS를 사용할 수 있는 지역을 선택합니다. 쌍을 이루는 지역을 사용합니다.

AKS 클러스터는 단일 Azure 지역에 배포됩니다. 지역 실패로부터 시스템을 보호하려면 애플리케이션을 다른 지역의 여러 AKS 클러스터에 배포하세요. AKS 클러스터를 배포할 위치를 계획하는 경우 다음을 고려하세요.

* [**AKS 지역 가용성**](./quotas-skus-regions.md#region-availability)
    * 사용자에게 가까운 Azure 지역을 선택합니다. 
    * AKS는 계속해서 새 지역으로 확장됩니다.
* [**Azure 쌍을 이루는 지역**](../availability-zones/cross-region-replication-azure.md)
    * 사용자의 지리적 영역에 대해, 쌍을 이루는 두 지역을 선택합니다.
    * AKS 플랫폼 업데이트(계획된 유지 관리)는 쌍을 이루는 지역 간에 최소 24시간 지연으로 직렬화됩니다. 
    * 쌍을 이루는 지역에 대한 복구 활동은 필요에 따라 우선 순위가 지정됩니다. 
* **서비스 가용성**
    * 쌍을 이루는 지역에 핫/핫, 핫/웜 또는 핫/콜드 여부를 결정합니다.
    * 두 지역을 동시에 실행하고 싶은데, 한 지역은 트래픽을 처리할 *준비가 완료* 되었나요? 또는,
    * 한 지역에 트래픽 처리를 준비할 시간을 주고 싶으신가요?

AKS 지역 가용성 및 쌍을 이루는 지역은 공동으로 고려해야 합니다. 지역 재해 복구를 함께 관리하도록 설계된 쌍을 이루는 지역에 AKS 클러스터를 배포합니다. 예를 들어 AKS는 미국 동부 및 미국 서부에서 사용할 수 있습니다. 이 지역들은 쌍을 이루는 지역입니다. AKS BC/DR 전략을 만들 때 이 두 지역을 선택합니다.

애플리케이션을 배포할 때 이러한 여러 AKS 클러스터를 배포하는 또 다른 단계를 CI/CD 파이프라인에 추가해야 합니다. 배포 파이프라인을 업데이트하면 애플리케이션을 지역 및 AKS 클러스터 중 하나에만 배포할 수 있습니다. 해당 시나리오에서는, 보조 지역으로 전송되는 고객 트래픽은 최신 코드 업데이트를 수신하지 않습니다.

## <a name="use-azure-traffic-manager-to-route-traffic"></a>Azure Traffic Manager를 사용하여 트래픽 라우팅

> **모범 사례**:
>
> Azure Traffic Manager는 가장 가까운 AKS 클러스터 및 애플리케이션 인스턴스로 안내할 수 있습니다. 최상의 성능 및 중복도를 위해 AKS 클러스터로 보내기 전에 Traffic Manager를 통해 모든 애플리케이션 트래픽을 전달합니다.

서로 다른 지역에 여러 AKS 클러스터가 있는 경우 Traffic Manager를 사용하여 각 클러스터에서 실행 중인 애플리케이션에 대한 트래픽 흐름을 제어합니다. [Azure Traffic Manager](../traffic-manager/index.yml)는 네트워크 트래픽을 여러 Azure 지역에 분산할 수 있는 DNS 기반 트래픽 부하 분산 장치입니다. Traffic Manager를 사용하여 클러스터 응답 시간 또는 지리적 위치에 따라 사용자를 라우팅할 수 있습니다.

![Traffic Manager와 ASK](media/operator-best-practices-bc-dr/aks-azure-traffic-manager.png)

단일 AKS 클러스터가 있다면 일반적으로 지정된 애플리케이션의 서비스 IP 또는 DNS 이름에 연결합니다. 다중 클러스터 배포에서는 각 AKS 클러스터의 서비스를 가리키는 Traffic Manager DNS 이름에 연결해야 합니다. 이러한 서비스는 Traffic Manager 엔드포인트를 사용하여 정의합니다. 각 엔드포인트는 *서비스 부하 분산 장치 IP* 입니다. 이 구성을 사용하여 한 지역의 Traffic Manager 엔드포인트에서 다른 지역의 엔드포인트로 네트워크 트래픽을 보낼 수 있습니다.

![Traffic Manager를 통한 지리적 라우팅](media/operator-best-practices-bc-dr/traffic-manager-geographic-routing.png)

Traffic Manager는 DNS 조회를 수행하고 가장 적합한 엔드포인트를 반환합니다. 중첩된 프로필은 기본 위치의 우선 순위를 지정할 수 있습니다. 예를 들어, 지리적으로 가장 가까운 지역에 연결해야 합니다. 해당 지역에 문제가 있는 경우 Traffic Manager는 보조 지역으로 안내합니다. 이 방식을 통해 지리적으로 가장 가까운 지역을 사용할 수 없는 경우에도 애플리케이션 인스턴스에 연결할 수 있습니다.

이러한 엔드포인트 및 라우팅을 설정하는 정보는 [Traffic Manager를 사용하는 지리적 트래픽 라우팅 방법 구성](../traffic-manager/traffic-manager-configure-geographic-routing-method.md)을 참조하세요.

### <a name="application-routing-with-azure-front-door-service"></a>Azure Front Door Service를 사용한 애플리케이션 라우팅

분할된 TCP 기반 애니캐스트 프로토콜을 사용하여 [Azure Front Door Service](../frontdoor/front-door-overview.md)는 최종 사용자가 가장 가까운 Front Door POP(Point of Presence)에 즉시 연결하도록 합니다. Azure Front Door Service의 추가 기능:
* TLS 종료
* 사용자 지정 도메인
* 웹 애플리케이션 방화벽
* URL 다시 쓰기
* 세션 선호도 

애플리케이션 트래픽 요구 사항을 검토하여 어떤 솔루션이 가장 적합한지 알아보세요.

### <a name="interconnect-regions-with-global-virtual-network-peering"></a>글로벌 가상 네트워크 피어링을 사용하여 지역 상호 연결

두 가상 네트워크를 서로 [가상 네트워크 피어링](../virtual-network/virtual-network-peering-overview.md)을 통해 연결하여 클러스터 간 통신을 사용하도록 설정합니다. 가상 네트워크 피어링은 가상 네트워크를 상호 연결하여 Microsoft의 백본 네트워크 전체에 걸쳐, 심지어 다른 지리적 지역에서도 높은 대역폭을 제공합니다.

AKS 클러스터를 실행하는 가상 네트워크를 피어링하기 전에 AKS 클러스터에서 표준 Load Balancer를 사용합니다. 이 필수 구성 요소를 사용하면 가상 네트워크 피어링에서 Kubernetes 서비스에 연결할 수 있습니다.

## <a name="enable-geo-replication-for-container-images"></a>컨테이너 이미지에 지역 복제 사용

> **모범 사례**:
> 
> Azure Container Registry에 컨테이너 이미지를 저장하고 레지스트리를 각 AKS 지역에 지역 복제합니다.

AKS에 애플리케이션을 배포하고 실행하려면 컨테이너 이미지를 저장하고 끌어오는 방법이 필요합니다. Container Registry는 AKS와 통합하여 컨테이너 이미지 또는 Helm 차트를 안전하게 저장할 수 있습니다. Container Registry는 전 세계 Azure 지역에 이미지를 자동으로 복제하는 다중 마스터 지역 복제를 지원합니다. 

성능 및 가용성을 향상시키려면 다음을 수행합니다.
1. Container Registry 지역 복제를 이용해 AKS 클러스터가 있는 각 지역에 레지스트리를 생성합니다. 
1. 그러면 각 AKS 클러스터가 동일한 Azure 지역의 로컬 Container Registry 레지스트리에서 컨테이너 이미지를 끌어옵니다.

![컨테이너 이미지용 Container Registry 지역 복제](media/operator-best-practices-bc-dr/acr-geo-replication.png)

Container Registry 지역에서 복제를 사용하여 동일한 지역에서 이미지를 가져오는 경우 결과는 다음과 같습니다.

* **더 빠름**: 동일한 Azure 지역 내에서 속도가 빠르고 대기 시간이 짧은 네트워크 연결로 이미지를 가져옵니다.
* **더 안정적**: 지역을 사용할 수 없는 경우 AKS 클러스터는 사용 가능한 컨테이너 레지스트리에서 이미지를 가져옵니다.
* **더 저렴함**: 데이터 센터 간에 네트워크 송신 요금이 부과되지 않습니다.

지역 복제는 *프리미엄* SKU 컨테이너 레지스트리 기능입니다. 지역 복제를 구성하는 단계에 대한 정보는 [Azure Container Registry 지역 복제](../container-registry/container-registry-geo-replication.md)를 참조하세요.

## <a name="remove-service-state-from-inside-containers"></a>컨테이너 내부에서 서비스 상태 제거

> **모범 사례**:
> 
> 컨테이너 내부에 서비스 상태를 저장하지 마세요. 대신, 다중 지역 복제를 지원하는 Azure PaaS(Platform as a Service)를 사용하세요.

*서비스 상태* 는 서비스가 기능하기 위해 필요한 메모리 내 또는 디스크 상의 데이터를 나타냅니다. 상태의 예로는 서비스에서 읽고 쓰는 데이터 구조 및 멤버 변수를 들 수 있습니다. 서비스의 설계 방식에 따라 디스크에 저장되는 파일이나 기타 리소스도 상태에 포함될 수 있습니다. 예를 들어 데이터베이스에서 데이터 및 트랜잭션 로그를 저장하는 데 사용하는 파일이 상태에 포함될 수 있습니다.

상태는 외부화할 수도 있고 상태를 조작하는 코드와 함께 배치할 수도 있습니다. 일반적으로 네트워크를 통해 다른 컴퓨터에서 실행되거나 동일한 컴퓨터에서 프로세스를 실행하는 데이터베이스 또는 다른 데이터 저장소를 사용하여 상태를 외부화합니다.

컨테이너 및 마이크로서비스는 내부에서 실행되는 프로세스가 상태를 유지하지 하는 경우에 가장 복원력이 우수합니다. 애플리케이션은 대부분 몇 가지 상태를 포함하기 때문에 다음과 같은 PaaS 솔루션을 사용합니다.
* Azure Cosmos DB
* Azure Database for PostgreSQL
* Azure Database for MySQL
* Azure SQL Database

이식 가능한 애플리케이션을 빌드하려면 다음 지침을 참조하세요.

* [12-요소 앱 방법론](https://12factor.net/)
* [여러 Azure 지역에서 웹 애플리케이션 실행](/azure/architecture/reference-architectures/app-service-web-app/multi-region)

## <a name="create-a-storage-migration-plan"></a>스토리지 마이그레이션 계획 작성

> **모범 사례**:
>
> Azure Storage를 사용하는 경우 주 지역에서 백업 지역으로 스토리지를 마이그레이션하는 방법을 준비하고 테스트합니다.

애플리케이션이 데이터에 Azure Storage를 사용할 수도 있습니다. 이 경우 애플리케이션이 여러 Azure 지역의 여러 AKS 클러스터에 분산되어 있습니다. 스토리지를 동기화 상태로 유지해야 합니다. 스토리지를 복제하는 두 가지 일반적인 방법은 다음과 같습니다.

* 인프라 기반 비동기 복제
* 애플리케이션 기반 비동기 복제

### <a name="infrastructure-based-asynchronous-replication"></a>인프라 기반 비동기 복제

Pod를 삭제한 후에도 애플리케이션에서 영구 스토리지를 요구할 수 있습니다. Kubernetes에서는 영구 볼륨을 사용하여 데이터 스토리지를 유지할 수 있습니다. 영구 볼륨은 노드 VM에 탑재되며, Pod에 공개됩니다. Pod가 동일한 클러스터의 다른 노드로 이동되더라도 영구 볼륨은 Pod를 따릅니다.

사용하는 복제 전략은 스토리지 솔루션에 따라 달라집니다. 다음 일반적인 스토리지 솔루션은 재해 복구 및 복제에 대한 자체 참고 자료를 제공합니다.
* [Gluster](https://docs.gluster.org/en/latest/Administrator-Guide/Geo-Replication/)
* [Ceph](https://docs.ceph.com/docs/master/cephfs/disaster-recovery/)
* [Rook](https://rook.io/docs/rook/v1.2/ceph-disaster-recovery.html)
* [Portworx](https://docs.portworx.com/scheduler/kubernetes/going-production-with-k8s.html#disaster-recovery-with-cloudsnaps) 

일반적으로 사용자는 애플리케이션이 데이터를 쓰는 공통 스토리지 포인트를 사용합니다. 이 데이터는 여러 지역에 복제된 후 로컬로 액세스됩니다.

![인프라 기반 비동기 복제](media/operator-best-practices-bc-dr/aks-infra-based-async-repl.png)

Azure Managed Disks를 사용하는 경우 [Velero on Azure][velero] 및 [Kasten][kasten]을 사용해 복제와 재해 복구를 처리할 수 있습니다. 이러한 옵션은 Kubernetes에서 지원하지 않는 솔루션을 기본으로 백업합니다.

### <a name="application-based-asynchronous-replication"></a>애플리케이션 기반 비동기 복제

Kubernetes는 현재 애플리케이션 기반 비동기 복제에 대한 기본 구현을 제공하지 않습니다. 컨테이너와 Kubernetes가 느슨하게 결합되기 때문에 기존 애플리케이션 또는 언어 접근 방식도 작동합니다. 일반적으로 애플리케이션 자체는 스토리지 요청을 복제하며, 이는 이후 각 클러스터의 기본 데이터 스토리지에 기록됩니다.

![애플리케이션 기반 비동기 복제](media/operator-best-practices-bc-dr/aks-app-based-async-repl.png)

## <a name="next-steps"></a>다음 단계

이 문서에서는 AKS 클러스터의 비즈니스 연속성 및 재해 복구 고려 사항을 집중적으로 살펴보았습니다. AKS의 클러스터 작업에 대한 자세한 내용은 다음 모범 사례에 대한 이 문서를 참조하세요.

* [다중 테넌트 및 클러스터 격리][aks-best-practices-cluster-isolation]
* [기본 Kubernetes 스케줄러 기능][aks-best-practices-scheduler]

<!-- INTERNAL LINKS -->
[aks-best-practices-scheduler]: operator-best-practices-scheduler.md
[aks-best-practices-cluster-isolation]: operator-best-practices-cluster-isolation.md

[velero]: https://github.com/vmware-tanzu/velero-plugin-for-microsoft-azure/blob/master/README.md
[kasten]: https://www.kasten.io/
