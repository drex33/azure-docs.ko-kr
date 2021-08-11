---
title: Azure Cosmos DB Gremlin API 소개
description: Azure Cosmos DB를 사용하여 Apache TinkerPop의 Gremlin 그래프 쿼리 언어로 대기 시간을 단축하면서 대량의 그래프를 저장하고 쿼리하고 트래버스하는 방법을 알아봅니다.
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: overview
ms.date: 07/26/2021
author: manishmsfte
ms.author: mansha
ms.openlocfilehash: 0454fc76b089a3959d522b812177c6ca3a679deb
ms.sourcegitcommit: 63f3fc5791f9393f8f242e2fb4cce9faf78f4f07
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/26/2021
ms.locfileid: "114690515"
---
# <a name="introduction-to-gremlin-api-in-azure-cosmos-db"></a>Azure Cosmos DB의 Gremlin API 소개
[!INCLUDE[appliesto-gremlin-api](includes/appliesto-gremlin-api.md)]

[Azure Cosmos DB](introduction.md) 는 중요 업무용 애플리케이션에 사용할 수 있도록 Microsoft에서 제공하는 전역 분산 다중 모델 데이터베이스 서비스입니다. 문서, 키-값, 그래프 및 열 패밀리 데이터 모델을 지원하는 다중 모델 데이터베이스입니다. Azure Cosmos DB는 모든 규모에 맞게 설계된 완전 관리형 데이터베이스 서비스에서 Gremlin API를 통해 그래프 데이터베이스 서비스를 제공합니다.  

:::image type="content" source="./media/graph-introduction/cosmosdb-graph-architecture.png" alt-text="Azure Cosmos DB 그래프 아키텍처" border="false":::

이 문서에서는 Azure Cosmos DB Gremlin API의 개요를 제공하고 이를 사용하여 수십억 개의 꼭짓점 및 에지가 있는 대량의 그래프를 저장하는 방법을 설명합니다. 밀리초 단위 대기 시간 내에 그래프를 쿼리하고 그래프 구조를 쉽게 발전시킬 수 있습니다. Azure Cosmos DB의 Gremlin API는 그래프 컴퓨팅 프레임워크인 [Apache TinkerPop](https://tinkerpop.apache.org)를 기반으로 빌드되었습니다. Azure Cosmos DB의 Gremlin API는 Gremlin 쿼리 언어를 사용합니다.

Azure Cosmos DB의 Gremlin API는 그래프 데이터베이스 알고리즘의 기능을 확장성 높은 관리형 인프라와 결합하여 유연성과 관계형 접근 방식이 결여되어 발생하는 대부분의 일반적인 데이터 문제에 대한 고유하고 유연한 솔루션을 제공합니다.

> [!NOTE]
> Azure Cosmos DB 그래프 엔진은 Apache TinkerPop 사양을 밀접하게 따릅니다. 그러나 Azure Cosmos DB와 관련된 구현 세부 정보에는 몇 가지 차이점이 있습니다. Apache TinkerPop에서 지원되는 일부 기능은 Azure Cosmos DB에서 사용할 수 없습니다. 지원되지 않는 기능에 대한 자세한 내용은 [Apache TinkerPop과의 호환성](gremlin-support.md) 문서를 참조하세요.

## <a name="features-of-azure-cosmos-dbs-gremlin-api"></a>Azure Cosmos DB의 Gremlin API 기능

Azure Cosmos DB는 전역 배포, 스토리지 및 처리량의 탄력적인 확장, 자동 인덱싱 및 쿼리, 튜닝 가능한 일관성 수준을 제공하고 TinkerPop 표준을 지원하는 완전히 관리되는 그래프 데이터베이스입니다.

> [!NOTE]
> 이제 Azure Cosmos DB의 Gremlin API에서 [서버리스 용량 모드](serverless.md)를 사용할 수 있습니다.

Azure Cosmos DB Gremlin API에서 제공하는 차별화된 기능은 다음과 같습니다.

* **탄력적으로 확장 가능한 처리량 및 스토리지**

  실제 사용되는 그래프는 단일 서버의 용량을 초과하여 확장되어야 합니다. Azure Cosmos DB는 거의 제한 없이 스토리지 및 프로비저닝된 처리량을 제공하도록 수평적으로 확장할 수 있는 그래프 데이터베이스를 지원합니다. 그래프 데이터베이스 규모가 증가하면 데이터는 [그래프 분할](./graph-partitioning.md)을 사용하여 자동으로 분산됩니다.

* **다중 지역 복제**

  Azure Cosmos DB는 그래프 데이터를 전 세계 모든 Azure 지역에 자동으로 복제할 수 있습니다. 글로벌 복제는 데이터에 대한 글로벌 액세스가 필요한 애플리케이션의 개발을 간소화합니다. Azure Cosmos DB는 전 세계 어디서나 읽기 및 쓰기 대기 시간을 최소화할 뿐 아니라, 드물지만 한 지역에서 서비스 중단이 발생하더라도 애플리케이션의 연속성을 보장할 수 있는 지역별 장애 조치(failover) 메커니즘을 제공합니다.

* **가장 널리 채택된 그래프 쿼리 표준을 통한 빠른 쿼리 및 이동**

  다른 유형의 꼭짓점 및 에지를 저장하고 익숙한 Gremlin 구문을 통해 쿼리합니다. Gremlin은 일반적인 그래프 알고리즘을 구현하기 위한 풍부한 인터페이스를 제공하는 명령형 기능 쿼리 언어입니다.
  
  Azure Cosmos DB를 통해 스키마 힌트, 보조 인덱스 또는 뷰를 지정할 필요 없이 풍부한 실시간 쿼리 및 이동을 사용할 수 있습니다. [Gremlin을 사용하여 그래프 쿼리](gremlin-support.md)에서 자세한 내용을 참조하세요.

* **완전 관리형 그래프 데이터베이스**

  Azure Cosmos DB는 데이터베이스 및 컴퓨터 리소스를 관리할 필요가 없습니다. 대부분의 기존 그래프 데이터베이스 플랫폼은 인프라의 제한 사항이 적용되며 운영을 보장하기 위해 높은 수준의 유지 관리가 필요합니다. 
  
  완전 관리형 서비스인 Cosmos DB는 가상 머신을 관리하고, 런타임 소프트웨어를 업데이트하고, 분할 또는 복제를 관리하고, 복잡한 데이터 계층 업그레이드를 처리할 필요가 없습니다. 모든 그래프가 자동으로 백업되고 지역적 실패로부터 보호됩니다. 이를 통해 개발자는 그래프 데이터베이스를 운영하고 관리하는 대신 애플리케이션의 가치를 실현하는 데 집중할 수 있습니다. 

* **자동 인덱싱**:

  기본적으로 Azure Cosmos DB는 그래프에서 노드(꼭짓점이라고도 함) 및 에지 내의 모든 속성을 자동으로 인덱싱하고 스키마 또는 보조 인덱스 생성을 예상하거나 요구하지 않습니다. [Azure Cosmos DB의 인덱싱](./index-overview.md)에 대해 자세히 알아봅니다.

* **Apache TinkerPop과의 호환성**

  Azure Cosmos DB는 [오픈 소스 Apache TinkerPop 표준](https://tinkerpop.apache.org/)을 지원합니다. Tinkerpop 표준에는 Azure Cosmos DB의 Gremlin API와 쉽게 통합할 수 있는 애플리케이션 및 라이브러리의 충분한 에코시스템이 있습니다.

* **튜닝 가능한 일관성 수준**

  Azure Cosmos DB는 애플리케이션의 일관성과 성능 간에 최적의 균형을 유지할 수 있도록 잘 정의된 5가지 일관성 수준을 제공합니다. 쿼리 및 읽기 작업에 대해 Azure Cosmos DB는 강력, 제한된 부실, 세션, 일관된 접두사, 최종의 5가지 일관성 수준을 제공합니다. 이러한 잘 정의된 세부적인 일관성 수준을 통해 일관성, 가용성 및 대기 시간 간에 적절한 절충을 이룰 수 있습니다. [Azure Cosmos DB의 튜닝 가능한 데이터 일관성 수준](consistency-levels.md)에서 자세히 알아봅니다.

## <a name="scenarios-that-use-gremlin-api"></a>Gremlin API를 사용하는 시나리오

다음은 Azure Cosmos DB 그래프 지원을 유용하게 사용할 수 있는 시나리오입니다.

* **소셜 네트워크/고객 365**

  고객 및 다른 사람과의 상호 작용에 대한 데이터를 조합하여 개인 설정된 환경을 개발하거나, 고객 행동을 예측하거나, 관심 영역이 비슷한 다른 사람과 연결해줄 수 있습니다. Azure Cosmos DB를 사용하여 소셜 네트워크를 관리하고 고객 기본 설정 및 데이터를 추적할 수 있습니다.

* **추천 엔진**

  이 시나리오는 소매 업계에서 일반적으로 사용됩니다. 제품, 사용자 및 사용자 상호 작용(예: 항목 구입, 검색, 등급 지정)에 대한 정보를 조합하여 사용자 지정된 추천을 작성할 수 있습니다. 대기 시간이 짧고, 탄력적으로 확장되고, 네이티브 그래프를 지원하는 Azure Cosmos DB는 이러한 시나리오에 이상적입니다.

* **지리 공간적**

  원격 통신, 물류 및 여행 계획에서 사용되는 많은 애플리케이션은 하나의 영역 내에서 원하는 위치를 찾거나 두 위치 간에 가장 짧은/최적 경로를 찾아야 합니다. Azure Cosmos DB는 이러한 문제를 자연스럽게 해결합니다.

* **사물 인터넷**

  IoT 디바이스 간의 네트워크 및 연결을 그래프로 모델링하여 디바이스 및 자산의 상태를 보다 잘 이해할 수 있습니다. 또한 한 네트워크 부분의 변경이 다른 부분에 어떤 영향을 미칠 수 있는지도 알 수 있습니다.

## <a name="introduction-to-graph-databases"></a>그래프 데이터베이스 소개

실제 환경의 경우처럼 데이터는 자연스럽게 연결되어 있습니다. 기존 데이터 모델링은 엔터티를 개별적으로 정의하고 런타임에 해당 관계를 컴퓨팅하는 데 중점을 둡니다. 이 모델에도 장점이 있지만 복잡하게 연결된 데이터는 해당 제약 조건에 따라 관리하기 어려울 수 있습니다.  

그래프 데이터베이스 접근 방식은 스토리지 계층에서 관계를 지속하므로 매우 효율적인 그래프 검색 작업이 가능합니다. Azure Cosmos DB의 Gremlin API는 [속성 그래프 모델](https://tinkerpop.apache.org/docs/current/reference/#intro)을 지원합니다.

### <a name="property-graph-objects"></a>속성 그래프 개체

속성 [그래프](http://mathworld.wolfram.com/Graph.html)는 [꼭짓점](http://mathworld.wolfram.com/GraphVertex.html) 및 [가장자리](http://mathworld.wolfram.com/GraphEdge.html)로 구성된 구조입니다. 두 개체는 개수 제한 없이 키-값 쌍을 속성으로 사용할 수 있습니다. 

* **꼭짓점/노드** - 꼭짓점은 사람, 장소, 이벤트 등의 개별 엔터티를 나타냅니다.

* **에지/관계** - 에지는 꼭짓점 간의 관계를 나타냅니다. 예를 들어, 한 사용자가 다른 사용자를 알고 있으며, 이벤트에 관계되어 있고, 최근 한 위치에 있었을 수 있습니다.

* **속성** -  속성은 꼭짓점 및 에지에 대한 정보를 나타냅니다. 꼭짓점이든, 가장자리든 제한 없이 속성을 포함할 수 있으며 쿼리에서 개체를 설명하고 필터링하는 데 사용될 수 있습니다. 예제 속성에는 이름 및 사용 기간이 포함된 꼭짓점이나 타임스탬프 및/또는 가중치가 포함될 수 있는 가장자리가 있습니다.

* **레이블** - 레이블은 꼭짓점 또는 에지의 이름 또는 식별자입니다. 레이블은 그룹의 모든 꼭짓점/에지가 특정 레이블을 갖도록 여러 꼭짓점 또는 에지를 그룹화할 수 있습니다. 예를 들어, 그래프에는 "person" 레이블 형식의 여러 개의 꼭짓점이 있을 수 있습니다.

그래프 데이터베이스는 스키마 또는 제한된 데이터 모델에 대한 종속성이 없으므로 일반적으로 NoSQL 또는 비관계형 데이터베이스 범주에 포함됩니다. 이처럼 스키마가 없으므로 연결된 구조를 자연스럽고 효율적으로 모델링 및 저장할 수 있습니다.

### <a name="graph-database-by-example"></a>예제별 그래프 데이터베이스

샘플 그래프를 사용하여 Gremlin에서 쿼리를 표현할 수 있는 방법을 살펴봅니다. 다음 그림에서는 사용자, 관심 영역 및 디바이스에 대한 데이터를 그래프 형태로 관리하는 비즈니스 애플리케이션을 보여줍니다.  

:::image type="content" source="./media/gremlin-support/sample-graph.png" alt-text="사람, 디바이스 및 관심 분야를 보여주는 샘플 데이터베이스" border="false"::: 

이 그래프에는 다음과 같은 *꼭짓점* 유형(Gremlin에서는 "레이블"이라고도 함)이 있습니다.

* **사람**: 그래프에는 Robin, Thomas 및 Ben 세 사람이 있음
* **관심 분야**: 관심 있는 분야로 이 예제에서는 축구 게임
* **디바이스**: 사람들이 사용하는 디바이스
* **운영 체제**: 디바이스가 실행되는 운영 체제
* **Place**: 디바이스에 액세스하는 위치

이러한 엔터티 간 관계는 다음 *에지* 유형을 통해 나타냅니다.

* **알고 있습니다**: 예: “Thomas knows Robin”
* **관심 분야**: 그래프에서 사람의 관심 분야를 나타내는 데 사용합니다(예: “Ben is interested in Football”).
* **OS 실행**: 노트북에서 Windows OS가 실행됩니다.
* **사용**: 사람이 사용하는 디바이스를 나타냅니다. 예를 들어 Robin은 일련 번호가 77인 Motorola 휴대폰을 사용합니다.
* **located**: 디바이스에 액세스하는 위치를 나타내려면

Gremlin 콘솔은 Apache TinkerPop에서 제공하는 대화형 터미널이며, 이 터미널은 그래프 데이터와 상호 작용하는 데 사용됩니다. 자세한 내용은 [Gremlin 콘솔을 사용하는 방법](create-graph-gremlin-console.md)의 빠른 시작 문서를 참조하세요. 또한 원하는 플랫폼(Java, Node.js, Python 또는 .NET)에서 Gremlin 드라이버를 사용하여 이러한 작업을 수행할 수도 있습니다. 다음 예제에서는 Gremlin 콘솔을 사용하여 이 그래프 데이터에 대해 쿼리를 실행하는 방법을 보여줍니다.

먼저 CRUD를 살펴보겠습니다. 다음 Gremlin 문은 "Thomas" 꼭짓점을 그래프에 삽입합니다.

```java
:> g.addV('person').property('id', 'thomas.1').property('firstName', 'Thomas').property('lastName', 'Andersen').property('age', 44)
```

그런 후 다음 Gremlin 문은 Thomas와 Robin 사이에 "knows" 에지를 삽입합니다.

```java
:> g.V('thomas.1').addE('knows').to(g.V('robin.1'))
```

다음 쿼리는 이름의 내림차순으로 "person" 꼭짓점을 반환합니다.

```java
:> g.V().hasLabel('person').order().by('firstName', decr)
```

그래프의 강점은 "Thomas의 친구들은 어떤 운영 체제를 사용하는가?"와 같은 질문에 답변해야 할 경우에 잘 나타납니다. 다음과 같은 Gremlin 순회를 실행하여 그래프에서 해당 정보를 가져올 수 있습니다.

```java
:> g.V('thomas.1').out('knows').out('uses').out('runsos').group().by('name').by(count())
```

## <a name="next-steps"></a>다음 단계

Azure Cosmos DB의 그래프 지원에 대해 자세히 알아보려면 다음을 참조하세요.

* [Azure Cosmos DB 그래프 자습서](create-graph-dotnet.md) 시작
* [Gremlin을 사용하여 Azure Cosmos DB에서 그래프를 쿼리](gremlin-support.md)하는 방법 알아보기
