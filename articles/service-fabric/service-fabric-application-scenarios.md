---
title: 애플리케이션 시나리오 및 설계
description: 서비스 패브릭의 클라우드 애플리케이션 범주 개요 상태 저장 및 상태 비저장 서비스를 사용하는 애플리케이션 설계에 대해 논의합니다.
ms.topic: conceptual
ms.date: 01/08/2020
ms.openlocfilehash: 703adde38d195395ae07dea022da873754e1d2f1
ms.sourcegitcommit: 7854045df93e28949e79765a638ec86f83d28ebc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2021
ms.locfileid: "122867141"
---
# <a name="service-fabric-application-scenarios"></a>서비스 패브릭 애플리케이션 시나리오

Azure Service Fabric은 다양한 형식의 비즈니스 애플리케이션 및 서비스를 쓰고 실행할 수 있는 안정적이고 유연한 플랫폼을 제공합니다. 이러한 애플리케이션 및 마이크로 서비스는 상태 비저장이거나 상태 저장일 수 있으며, 가상 머신 간에 리소스를 분산하여 효율성을 극대화합니다.

Service Fabric의 고유한 아키텍처는 애플리케이션에서 거의 실시간으로 데이터 분석, 메모리 내 계산, 병렬 트랜잭션 및 이벤트 처리가 가능합니다. 리소스 요구 사항의 변화에 따라 간단하게 애플리케이션을 스케일 인하거나 스케일 아웃할 수 있습니다.

애플리케이션 빌드에 대한 설계 참고 자료는 [Azure Service Fabric의 마이크로 서비스 아키텍처](/azure/architecture/reference-architectures/microservices/service-fabric) 및 [Service Fabric을 사용한 애플리케이션 설계 모범 사례](service-fabric-best-practices-applications.md)를 참조하세요.

다음 형식의 애플리케이션에 Service Fabric 플랫폼을 사용하는 것이 좋습니다.

* **데이터 수집, 처리, 및 IoT**: Service Fabric은 자체 상태 저장 서비스를 통해 큰 규모를 처리하고 대기 시간은 낮습니다. 디바이스와 계산에 대한 데이터가 공존하는 수백만 대의 디바이스에서 데이터를 처리하는 데 유용합니다.

    Service Fabric을 사용하여 IoT 서비스를 구축한 고객으로는 [Honeywell](https://customers.microsoft.com/story/honeywell-builds-microservices-based-thermostats-on-azure), [PCL Construction](https://customers.microsoft.com/story/pcl-construction-professional-services-azure), [Crestron](https://customers.microsoft.com/story/crestron-partner-professional-services-azure), [BMW](https://customers.microsoft.com/story/bmw-enables-driver-mobility-via-azure-service-fabric/), [Schneider Electric](https://customers.microsoft.com/story/schneider-electric-powers-engergy-solutions-on-azure-service-fabric), [Mesh Systems](https://customers.microsoft.com/story/mesh-systems-lights-up-the-market-with-iot-based-azure-solutions)가 있습니다.

* **게임 및 세션 기반 대화형 애플리케이션**: Service Fabric은 온라인 게임 또는 인스턴트 메시지와 같이 애플리케이션에 낮은 대기 시간 읽기 및 쓰기가 필요한 경우에 유용합니다. Service Fabric을 사용하면 별도의 저장소 또는 캐시를 만들 필요 없이 이러한 대화형 상태 저장 애플리케이션을 빌드할 수 있습니다. [Azure 게임 솔루션](https://azure.microsoft.com/solutions/gaming/)을 방문하여 [게임 서비스에서 Service Fabric 사용](/gaming/azure/reference-architectures/multiplayer-synchronous-sf)에 대한 참고 자료를 확인하세요.

    게임 서비스를 구축한 고객으로는 [Next Games](https://customers.microsoft.com/story/next-games-media-telecommunications-azure)와 [Digamore](https://customers.microsoft.com/story/digamore-entertainment-scores-with-a-new-gaming-platform-based-on-azure-service-fabric/)가 있습니다. 대화형 세션을 구축한 고객으로는 [Honeywell with Hololens](https://customers.microsoft.com/story/honeywell-manufacturing-hololens)가 있습니다.

* **데이터 분석 및 워크플로 처리**: 이벤트 또는 데이터 스트림을 안정적으로 처리해야 하는 애플리케이션은 Service Fabric에서 최적화된 읽기 및 쓰기를 활용합니다. 또한 Service Fabric을 사용하면 결과가 손실 없이 다음 처리 단계에 안정적으로 전달되어야 하는 파이프라인을 처리하는 애플리케이션을 지원합니다. 이러한 파이프라인에는 데이터 일관성 및 계산의 보장이 필수적인 트랜잭션 및 재무 시스템이 포함됩니다.

    비즈니스 워크플로 서비스를 구축한 고객으로는 [Zeiss Group](https://customers.microsoft.com/story/zeiss-group-focuses-on-azure-service-fabric-for-key-integration-platform), [Quorum Business Solutions](https://customers.microsoft.com/en-us/story/quorum-business-solutions-expand-energy-managemant-solutions-using-azure-service-fabric), [Société General](https://customers.microsoft.com/en-us/story/societe-generale-speeds-real-time-market-quotes-using-azure-service-fabric)이 있습니다.

* **데이터 계산**: Service Fabric을 사용하면 데이터 계산을 많이 수행하는 상태 저장 애플리케이션을 빌드할 수 있습니다. Service Fabric을 사용하면 애플리케이션에서 처리(계산)와 데이터의 공동 배치가 가능합니다. 

   일반적으로 애플리케이션이 데이터에 액세스해야 하는 경우 외부 데이터 캐시 또는 스토리지 계층과 관련된 네트워크 대기 시간이 계산 시간의 한도를 정합니다. 상태 저장 서비스 Fabric 서비스는 그 대기 시간을 없애서 더 최적화된 읽기와 쓰기를 사용할 수 있게 합니다.

   예를 들어 100밀리초보다 적은 왕복 시간을 요구하는 고객에게 근 실시간 권장 사항 선택을 수행하는 애플리케이션이 있다고 가정합니다. Service Fabric 서비스의 대기 시간 및 성능 특성은 원격 스토리지에서 필요한 데이터를 가져오는 표준 구현 모델과 비교해 사용자에게 응답성이 뛰어난 환경을 제공합니다. 권장 사항 선택의 계산이 데이터 및 규칙과 공동 배치된 덕분에 시스템의 응답성이 향상됩니다.

    계산 서비스를 구축한 고객으로는 [Solidsoft Reply](https://customers.microsoft.com/story/solidsoft-reply-platform-powers-e-verification-of-pharmaceuticals)와 [Infosupport](https://customers.microsoft.com/story/service-fabric-customer-profile-info-support-and-fudura)가 있습니다.

* **가용성이 높은 서비스**: Service Fabric은 여러 보조 서비스 복제본을 만들어 빠른 장애 조치(failover)를 제공합니다. 노드, 프로세스 또는 개별 서비스가 하드웨어 또는 다른 오류로 인해 중단되면 최소한의 서비스 손실로 보조 복제본 중 하나가 주 복제본으로 승격됩니다.

* **확장 가능한 서비스**: 개별 서비스를 분할하여 클러스터 전체로 상태를 확장할 수 있습니다. 또한 개별 서비스를 즉시 만들고 제거할 수 있습니다. 서비스를 소수 노드의 소수 인스턴스에서 다수 노드의 수천 개의 인스턴스로 스케일 아웃할 수 있으며, 그 후 다시 원하는 대로 스케일링할 수 있습니다. Service Fabric을 사용하여 이러한 서비스를 빌드하고 전체 수명 주기를 관리할 수 있습니다.

## <a name="application-design-case-studies"></a>애플리케이션 디자인 사례 연구

애플리케이션 설계에 Service Fabric을 사용하는 방법을 보여 주는 사례 연구는 [고객 스토리](https://customers.microsoft.com/en-us/search?sq=%22Azure%20Service%20Fabric%22&ff=&p=2&so=story_publish_date%20desc)와 [Azure의 마이크로 서비스](https://azure.microsoft.com/solutions/microservice-applications/) 사이트에 게시됩니다.

## <a name="designing-applications-composed-of-stateless-and-stateful-microservices"></a>상태 비저장 및 상태 저장 마이크로 서비스로 구성된 애플리케이션 설계

Azure Cloud Services 작업자 역할이 있는 애플리케이션을 빌드하는 것은 상태 비저장 서비스의 예시입니다. 이와는 반대로 상태 저장 마이크로 서비스는 요청 및 응답 이후에도 믿을 수 있는 상태를 유지합니다. 이 기능은 복제에 의해 지원되는 트랜잭션 보증을 제공하는 간단한 API를 통해 상태의 고가용성과 일관성을 제공합니다.

Service Fabric의 상태 저장 서비스는 고가용성을 보편화하여, 데이터베이스 및 기타 데이터 저장소가 아닌 모든 형식의 애플리케이션에 적용합니다. 자연적 진행 방향입니다. 애플리케이션은 이미 NoSQL 데이터베이스에 대한 고가용성을 위해 순수하게 관계형 데이터베이스를 사용하지 않습니다. 이제 애플리케이션 자체에서는 안정성, 일관성, 또는 가용성을 유지하면서 추가 성능 향상을 위해 자체 내에서 관리되는 "핫" 상태 및 데이터를 보유할 수 있습니다.

마이크로 서비스로 구성된 애플리케이션을 빌드하는 경우 일반적으로 상태 비저장 웹앱(예: ASP.NET 및 Node.js)의 조합을 통해 상태 비저장 및 상태 저장 비즈니스 중간 계층 서비스를 호출합니다. 앱과 서비스는 모두 Service Fabric 배포 명령을 통해 동일한 Service Fabric 클러스터에 배포됩니다. 각각의 서비스는 규모, 안정성 및 리소스 사용량과 관련하여 독립적입니다. 이러한 독립성은 개발 및 수명 주기 관리에 있어서의 민첩성과 유연성을 향상시킵니다.

상태 저장 마이크로 서비스는 순수한 상태 비저장 애플리케이션의 가용성 및 대기 시간 요구 사항을 해결하기 위해 일반적으로 필요했던 추가 큐 및 캐시에 대한 필요성을 제거하기 때문에 애플리케이션 설계를 간소화합니다. 상태 저장 서비스는 가용성이 높고 대기 시간이 낮으므로 애플리케이션에서 관리할 세부 정보가 더 적습니다.

아래 다이어그램에는 상태 비저장 애플리케이션과 상태 저장 애플리케이션 간의 설계 차이점을 표시합니다. [Reliable Services](service-fabric-reliable-services-introduction.md) 및 [Reliable Actors](service-fabric-reliable-actors-introduction.md) 프로그래밍 모델을 활용하여 상태 저장 서비스에서 애플리케이션 복잡성을 줄이고 높은 처리량과 낮은 대기 시간을 달성합니다.

상태 비저장 서비스를 사용하는 애플리케이션 예시는 ![상태 비저장 서비스를 사용하는 애플리케이션][Image1]에서 확인할 수 있습니다.

상태 저장 서비스를 사용하는 애플리케이션 예시는 ![상태 저장 서비스를 사용하는 애플리케이션][Image2]에서 확인할 수 있습니다.

## <a name="next-steps"></a>다음 단계

* Service Fabric [Reliable Services](service-fabric-reliable-services-quick-start.md) 및 [Reliable Actors](service-fabric-reliable-actors-get-started.md) 프로그래밍 모델을 사용하여 상태 비저장 및 상태 저장 서비스 빌드를 시작합니다.
* Azure 아키텍처 센터를 방문하여 [Azure에서 마이크로 서비스를 구축](/azure/architecture/microservices/)하는 방법에 대한 참고 자료를 확인하세요.
* 애플리케이션 설계 참고 자료에 대한 [Azure Service Fabric 애플리케이션 및 클러스터 모범 사례](./service-fabric-best-practices-security.md)로 이동합니다.

* 참고 항목:
  * [마이크로서비스 이해](service-fabric-overview-microservices.md)
  * [서비스 상태 정의 및 관리](service-fabric-concepts-state.md)
  * [서비스 가용성](service-fabric-availability-services.md)
  * [서비스 크기 조정](service-fabric-concepts-scalability.md)
  * [파티션 서비스](service-fabric-concepts-partitioning.md)

[Image1]: media/service-fabric-application-scenarios/AppwithStatelessServices.png
[Image2]: media/service-fabric-application-scenarios/AppwithStatefulServices.png
