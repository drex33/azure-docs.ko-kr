---
title: Cloud Services와 Service Fabric 간 차이점
description: Cloud Services에서 서비스 패브릭으로 애플리케이션을 마이그레이션하기 위한 개념적 개요입니다.
ms.topic: conceptual
ms.date: 11/02/2017
ms.openlocfilehash: c7e7d346b5a39a262d1d55265becadb1c718cc04
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96575774"
---
# <a name="learn-about-the-differences-between-cloud-services-and-service-fabric-before-migrating-applications"></a>애플리케이션을 마이그레이션하기 전에 Cloud Services와 Service Fabric 간의 차이점에 대해 알아봅니다.
Microsoft Azure Service Fabric은 확장성이 뛰어난 매우 안정적인 분산된 애플리케이션을 위한 차세대 클라우드 애플리케이션 플랫폼입니다. 분산된 클라우드 애플리케이션을 패키징, 배포, 업그레이드 및 관리하 위한 여러 가지 새로운 기능을 소개합니다. 

Cloud Services에서 Service Fabric으로 애플리케이션 마이그레이션에 대한 소개 가이드입니다. Cloud Services와 서비스 패브릭 간의 아키텍처 및 디자인 차이점에 대해 주로 중점을 둡니다.

## <a name="applications-and-infrastructure"></a>애플리케이션 및 인프라
Cloud Services와 Service Fabric 간의 기본적인 차이는 VM, 작업 및 애플리케이션 간의 관계입니다. 여기에서 작업은 특정 작업을 수행하거나 서비스를 제공하도록 작성하는 코드로 정의됩니다.

* **Cloud Services는 VM으로 애플리케이션 배포에 대한 것입니다.**  작성하는 코드는 웹 또는 작업자 역할과 같은 VM 인스턴스에 밀접하게 결합됩니다. Cloud Services에서 작업을 배포하는 것은 작업을 실행하는 하나 이상의 VM 인스턴스를 배포하는 것입니다. 애플리케이션과 VM의 분리가 없으므로 애플리케이션의 공식 정의가 없습니다. 애플리케이션은 Cloud Services 배포 내에서 웹 또는 작업자 역할 인스턴스 집합 또는 전체 Cloud Services 배포로 생각할 수 있습니다. 이 예제에서 애플리케이션은 역할 인스턴스 집합으로 표시됩니다.

![Cloud Services 애플리케이션 및 토폴로지][1]

* **Service Fabric은 애플리케이션을 Windows 또는 Linux에서 Service Fabric을 실행하는 기존 VM 또는 머신에 배포에 대한 것입니다.** 작성하는 서비스는 Service Fabric 애플리케이션 플랫폼에서 추상화된 기본 인프라에서 완전히 분리되므로 애플리케이션을 여러 환경에 배포할 수 있습니다. Service Fabric의 작업은 "서비스"라고 하고 하나 이상의 서비스는 Service Fabric 애플리케이션 플랫폼에서 실행하는 공식적으로 정의된 애플리케이션에 그룹화됩니다. 단일 Service Fabric 클러스터에 여러 애플리케이션을 배포할 수 있습니다.

![Service Fabric 애플리케이션 및 토폴로지][2]

Service Fabric 자체는 Windows 또는 Linux에서 실행하는 애플리케이션 플랫폼 계층인 반면 Cloud Services는 연결된 작업과 Azure에서 관리하는 VM을 배포하기 위한 시스템입니다.
Service Fabric 애플리케이션 모델에는 많은 장점이 있습니다.

* 빠른 배포 시간. VM 인스턴스를 만드는 시간이 많이 걸릴 수 있습니다. Service Fabric에서 Service Fabric 애플리케이션 플랫폼을 호스팅하는 클러스터를 만들기 위해 VM이 한 번만 배포됩니다. 해당 지점에서 애플리케이션 패키지를 매우 신속하게 클러스터에 배포할 수 있습니다.
* 고밀도 호스팅. Cloud Services에서 작업자 역할 VM은 하나의 작업을 호스팅합니다. Service Fabric에서 애플리케이션은 이를 실행하는 VM에서 분리됩니다. 즉 많은 수의 애플리케이션을 작은 수의 VM에 배포할 수 있으며 이는 큰 배포를 위해 전체 비용을 낮출 수 있습니다.
* 서비스 패브릭 플랫폼은 Azure 또는 온-프레미스든지 Windows Server 또는 Linux 컴퓨터가 있는 모든 위치에서 실행할 수 있습니다. 플랫폼은 기본 인프라에 추상화 계층을 제공하므로 애플리케이션을 다른 환경에서 실행할 수 있습니다. 
* 분산된 애플리케이션 관리. Service Fabric은 분산된 애플리케이션을 호스팅할 뿐만 아니라 호스팅 VM 또는 머신 수명 수기의 해당 수명 주기를 독립적으로 관리하도록 돕는 플랫폼입니다.

## <a name="application-architecture"></a>애플리케이션 아키텍처
Cloud Services 애플리케이션의 아키텍처는 일반적으로 Service Bus, Azure 테이블 및 Blob Storage, SQL, Redis 및 다른 사용자와 같은 다양한 외부 서비스 종속성을 포함하여 애플리케이션의 상태 및 데이터와 Cloud Services 배포에서 웹과 작업자 역할 간 통신을 관리합니다. 완전한 Cloud Services 애플리케이션의 예는 다음과 같이 표시될 수 있습니다.  

![Cloud Services 아키텍처][9]

Service Fabric 애플리케이션은 완전한 애플리케이션에서 동일한 외부 서비스를 사용하도록 선택할 수도 있습니다. 이 예제 Cloud Services 아키텍처를 사용하는 Cloud Services에서 Service Fabric으로 가장 간단한 마이그레이션 경로는 전체 아키텍처를 동일하게 유지하여 Cloud Services 배포를 Service Fabric 애플리케이션으로 바꾸는 것입니다. 웹 및 작업자 역할은 최소한의 코드 변경으로 서비스 패브릭 상태 비저장 서비스에 이식할 수 있습니다.

![간단한 마이그레이션 후 서비스 패브릭 아키텍처][10]

이 단계에서 시스템은 전과 동일하게 계속해서 작동해야 합니다. 서비스 패브릭의 상태 저장 기능을 활용하여 외부 상태 저장소는 적용 가능한 상태 저장 서비스로 내부화될 수 있습니다. 외부 서비스가 이전에 했던 것과 같이 애플리케이션에 동등한 기능을 제공하는 사용자 지정 서비스를 작성해야 하므로 이는 Service Fabric 상태 비저장 서비스로 웹 및 작업자 역할의 간단한 마이그레이션보다 더 복잡합니다. 이에 따른 장점은 다음과 같습니다. 

* 외부 종속성 제거 
* 배포, 관리 및 업그레이드 모델 통합 

이러한 서비스 내부화의 예제 결과 아키텍처는 다음과 같을 수 있습니다.

![전체 마이그레이션 후 서비스 패브릭 아키텍처][11]

## <a name="communication-and-workflow"></a>통신 및 워크플로
대부분의 클라우드 서비스 애플리케이션은 둘 이상의 계층으로 구성됩니다. 마찬가지로 Service Fabric 애플리케이션은 둘 이상의 서비스(일반적으로 많은 서비스)로 구성됩니다. 두 일반적인 통신 모델은 직접 통신이며 외부 영구 스토리지를 경유합니다.

### <a name="direct-communication"></a>직접 통신
직접 통신을 사용하여 계층은 각 계층에서 노출된 엔드포인트를 통해 직접 통신할 수 있습니다. Cloud Services와 같은 상태 비저장 환경에서 이는 부하를 균형 조정하도록 임의로 또는 라운드 로빈 방식으로 VM 역할의 인스턴스를 선택하고 해당 엔드포인트에 직접 연결하는 것을 의미합니다.

![Cloud Services 직접 통신][5]

 직접 통신은 서비스 패브릭에서 일반적인 통신 모델입니다. 서비스 패브릭과 Cloud Services의 주요 차이점은 Cloud Services에서는 VM에 연결하는 반면 서비스 패브릭에서는 서비스에 연결한다는 점입니다. 이는 몇 가지 이유로 중요한 차이점입니다.

* Service Fabric의 서비스는 이를 호스팅하는 VM에 연결되지 않습니다. 서비스는 클러스터에서 이동할 수 있으며 실제로 다양한 이유(리소스 균형 조정, 장애 조치, 애플리케이션 및 인프라 업그레이드 및 배치 또는 부하 제약 조건)로 이동하도록 예상됩니다. 즉, 서비스 인스턴스의 주소는 언제든지 변경될 수 있습니다. 
* 서비스 패브릭의 VM은 각각 고유 엔드포인트로 여러 서비스를 호스팅할 수 있습니다.

서비스 패브릭은 서비스의 엔드포인트 주소를 확인하는 데 사용할 수 있는 이름 지정 서비스라고 하는 서비스 검색 메커니즘을 제공합니다. 

![Service Fabric이 서비스의 엔드포인트 주소를 확인하는 데 사용할 수 있는 Naming Service라는 서비스 검색 메커니즘을 제공하는 방법을 보여 주는 다이어그램][6]

### <a name="queues"></a>큐
Cloud Services와 같은 상태 비저장 환경의 계층 간 일반 통신 메커니즘은 한 계층에서 다른 계층으로 작업 태스크를 지속적으로 저장하도록 외부 스토리지 큐를 사용하는 것입니다. 일반적인 시나리오는 작업자 역할 인스턴스가 작업을 큐에서 제거하고 처리할 수 있는 Azure 큐 또는 Service Bus에 작업을 전송하는 웹 계층입니다.

![Cloud Services 큐 통신][7]

서비스 패브릭에서 동일한 통신 모델을 사용할 수 있습니다. 기존 Cloud Services 애플리케이션을 Service Fabric에 마이그레이션하는 경우에 유용할 수 있습니다. 

![서비스 패브릭 직접 통신][8]

## <a name="parity"></a>Parity
[Cloud Services는 제어 수준과 사용 편의성에서 Service Fabric과 비슷하지만 이제는 레거시 서비스이며 새로운 개발에는 Service Fabric이 권장됩니다](/azure/architecture/guide/technology-choices/compute-decision-tree). API 비교는 다음과 같습니다.


| **클라우드 서비스 API** | **Service Fabric API** | **참고** |
| --- | --- | --- |
| RoleInstance.GetID | FabricRuntime.GetNodeContext.NodeId or .NodeName | ID는 NodeName의 속성입니다. |
| RoleInstance.GetFaultDomain | FabricClient.QueryManager.GetNodeList | NodeName을 필터링하고 FD 속성을 사용합니다. |
| RoleInstance.GetUpgradeDomain | FabricClient.QueryManager.GetNodeList | NodeName을 필터링하고 Upgrade 속성을 사용합니다. |
| RoleInstance.GetInstanceEndpoints | FabricRuntime.GetActivationContext 또는 Naming(ResolveService) | FabricRuntime.GetActivationContext를 통해 제공될 뿐 아니라 .Initialize 중에 제공된 ServiceInitializationParameters.CodePackageActivationContext를 통해 제공되는 CodePackageActivationContext입니다. |
| RoleEnvironment.GetRoles | FabricClient.QueryManager.GetNodeList | 동일한 종류의 유형별 필터링을 수행하려는 경우 FabricClient.ClusterManager.GetClusterManifest를 통해 클러스터 매니페스트에서 노드 유형 목록을 가져오고 해당 목록에서 역할/노드 유형을 선택할 수 있습니다. |
| RoleEnvironment.GetIsAvailable | Connect-WindowsFabricCluster 또는 특정 노드를 가리키는 FabricRuntime 만들기 | * |
| RoleEnvironment.GetLocalResource | CodePackageActivationContext.Log/Temp/Work | * |
| RoleEnvironment.GetCurrentRoleInstance | CodePackageActivationContext.Log/Temp/Work | * |
| LocalResource.GetRootPath | CodePackageActivationContext.Log/Temp/Work | * |
| Role.GetInstances | FabricClient.QueryManager.GetNodeList or ResolveService | * |
| RoleInstanceEndpoint.GetIPEndpoint | FabricRuntime.GetActivationContext 또는 Naming(ResolveService) | * |

## <a name="next-steps"></a>다음 단계
Cloud Services에서 Service Fabric으로 가장 간단한 마이그레이션 경로는 애플리케이션의 전체 아키텍처를 거의 동일하게 유지하여 Cloud Services 배포를 Service Fabric 애플리케이션으로 바꾸는 것입니다. 다음 문서는 웹 또는 작업자 역할을 서비스 패브릭 상태 비저장 서비스로 변환하는 데 도움이 되는 가이드를 제공합니다.

* [간단한 마이그레이션: 웹 또는 작업자 역할을 서비스 패브릭 상태 비저장 서비스로 변환](service-fabric-cloud-services-migration-worker-role-stateless-service.md)

<!--Image references-->
[1]: ./media/service-fabric-cloud-services-migration-differences/topology-cloud-services.png
[2]: ./media/service-fabric-cloud-services-migration-differences/topology-service-fabric.png
[5]: ./media/service-fabric-cloud-services-migration-differences/cloud-service-communication-direct.png
[6]: ./media/service-fabric-cloud-services-migration-differences/service-fabric-communication-direct.png
[7]: ./media/service-fabric-cloud-services-migration-differences/cloud-service-communication-queues.png
[8]: ./media/service-fabric-cloud-services-migration-differences/service-fabric-communication-queues.png
[9]: ./media/service-fabric-cloud-services-migration-differences/cloud-services-architecture.png
[10]: ./media/service-fabric-cloud-services-migration-differences/service-fabric-architecture-simple.png
[11]: ./media/service-fabric-cloud-services-migration-differences/service-fabric-architecture-full.png