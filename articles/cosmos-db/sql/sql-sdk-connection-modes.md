---
title: Azure Cosmos DB SQL SDK 연결 모드
description: Azure Cosmos DB SQL SDK에서 제공되는 다양한 연결 모드에 대해 알아봅니다.
author: ealsur
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 10/05/2021
ms.author: maquaran
ms.custom: devx-track-dotnet, contperf-fy21q2
ms.openlocfilehash: f03cffa9bcd317bed27d8a0b771d83d7cba6e3dd
ms.sourcegitcommit: 57b7356981803f933cbf75e2d5285db73383947f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/05/2021
ms.locfileid: "129546750"
---
# <a name="azure-cosmos-db-sql-sdk-connectivity-modes"></a>Azure Cosmos DB SQL SDK 연결 모드
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

클라이언트가 Azure Cosmos DB에 연결하는 방법은 특히 관찰되는 클라이언트 쪽 대기 시간 측면에서 성능에 중요한 영향을 미칩니다. Azure Cosmos DB는 HTTPS를 통해 게이트웨이 모드라고 하는 단순한 개방형 RESTful 프로그래밍 모델을 제공합니다. 또한 통신 모델에서 REST를 지원하기도 하며 초기 인증 및 암호화 트래픽에 직접 모드라고도 하는 TLS를 사용하는 효율적인 TCP 프로토콜을 제공합니다.

## <a name="available-connectivity-modes"></a>사용 가능한 연결 모드

사용 가능한 두 연결 모드는 다음과 같습니다.

  * 게이트웨이 모드
      
    게이트웨이 모드는 모든 SDK 플랫폼에서 지원됩니다. 애플리케이션이 엄격한 방화벽으로 제한된 회사 네트워크 내에서 실행되는 경우 표준 HTTPS 포트 및 단일 DNS 엔드포인트를 사용하므로 게이트웨이 모드를 선택하는 것이 가장 좋습니다. 그러나 게이트웨이 모드의 경우 성능 유지를 위해 Azure Cosmos DB에서 데이터를 읽거나 쓸 때마다 네트워크 홉이 추가됩니다. 또한 소켓 연결 수가 제한된 환경에서 애플리케이션을 실행할 때 게이트웨이 연결 모드를 권장합니다.

    특히 [소비 플랜](../../azure-functions/consumption-plan.md)에서 Azure Functions SDK를 사용하는 경우 [연결에 대한 현재 제한 사항](../../azure-functions/manage-connections.md)을 알고 있어야 합니다.

  * 직접 모드

    직접 모드는 TCP 프로토콜을 통한 연결을 지원하며 네트워크 홉 수가 적으므로 더 나은 성능을 제공합니다. 애플리케이션은 백 엔드 복제본에 직접 연결됩니다. 직접 모드는 현재 .NET 및 Java SDK 플랫폼에서만 지원됩니다.
     
:::image type="content" source="./media/performance-tips/connection-policy.png" alt-text="Azure Cosmos DB 연결 모드" border="false":::

이러한 연결 모드는 기본적으로 데이터 플레인 요청(문서 읽기 및 쓰기)을 클라이언트 머신에서 Azure Cosmos DB 백 엔드의 파티션으로 가져오는 조건을 경로에 지정합니다. 직접 모드는 최상의 성능을 위한 기본 설정 옵션입니다. 즉 이를 통해 클라이언트는 Azure Cosmos DB 백 엔드에서 직접 파티션에 대한 TCP 연결을 열고 중간자 없이 요청을 *직접* 보낼 수 있습니다. 반대로 게이트웨이 모드에서는 클라이언트의 요청이 Azure Cosmos DB 프런트 엔드의 "게이트웨이" 서버로 라우팅된 다음, Azure Cosmos DB 백 엔드에서 적절한 파티션으로 전달됩니다.

## <a name="service-port-ranges"></a>서비스 포트 범위

직접 모드를 사용할 경우 Azure Cosmos DB에서 동적 TCP 포트를 사용하므로 게이트웨이 포트 외에 10000에서 20000 사이의 포트 범위가 열려 있는지 확인해야 합니다. [프라이빗 엔드포인트](../how-to-configure-private-endpoints.md)에서 직접 모드를 사용하는 경우 TCP 포트의 전체 범위(0-65535)가 열려 있어야 합니다. 이러한 포트가 열려 있지 않은 상태에서 TCP 프로토콜을 사용하려고 하면 503 서비스를 사용할 수 없음 오류가 발생할 수 있습니다.

다음 표에서는 각 API에 사용되는 다양한 API 및 서비스 포트에 사용할 수 있는 연결 모드를 요약해서 설명합니다.

|연결 모드  |지원되는 프로토콜  |지원되는 SDK  |API/서비스 포트  |
|---------|---------|---------|---------|
|게이트웨이  |   HTTPS    |  모든 SDK    |   SQL(443), MongoDB(10250, 10255, 10256), Table(443), Cassandra(10350), Graph(443) <br> 포트 10250은 지역 복제 없이 기본 Azure Cosmos DB API for MongoDB 인스턴스로 매핑됩니다. 반면 포트 10255 및 10256은 지역 복제가 있는 인스턴스에 매핑합니다.   |
|직접    |     TCP    |  .NET SDK Java SDK    | 퍼블릭/서비스 엔드포인트를 사용하는 경우: 10000-20000 범위의 포트<br>프라이빗 엔드포인트를 사용하는 경우: 0-65535 범위의 포트 |

## <a name="next-steps"></a>다음 단계

특정 SDK 플랫폼 성능 최적화:

* [.NET V2 SDK 성능 팁](performance-tips.md)

* [.NET V3 SDK 성능 팁](performance-tips-dotnet-sdk-v3-sql.md)
 
* [Java V4 SDK 성능 팁](performance-tips-java-sdk-v4-sql.md)

* Azure Cosmos DB로 마이그레이션하기 위한 용량 계획을 수행하려고 하시나요? 용량 계획을 위해 기존 데이터베이스 클러스터에 대한 정보를 사용할 수 있습니다.
    * 기존 데이터베이스 클러스터의 vCore 및 서버 수만 알고 있는 경우 [vCore 또는 vCPU를 사용하여 요청 단위 예측](../convert-vcore-to-request-unit.md)에 대해 읽어보세요. 
    * 현재 데이터베이스 워크로드에 대한 일반적인 요청 비율을 알고 있는 경우 [Azure Cosmos DB 용량 플래너를 사용하여 요청 단위 예측](estimate-ru-with-capacity-planner.md)에 대해 읽어보세요.