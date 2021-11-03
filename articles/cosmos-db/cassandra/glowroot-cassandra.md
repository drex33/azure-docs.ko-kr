---
title: Azure Cosmos DB Cassandra API(미리 보기)에서 Glowroot 실행
description: 이 문서에서는 Azure Cosmos DB Cassandra API Glowroot를 실행하는 방법을 자세히 설명합니다.
author: IriaOsara
ms.author: IriaOsara
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: how-to
ms.date: 10/02/2021
ms.custom: template-how-to
ms.openlocfilehash: 1808e3df0b70f61c3cfabf96e61b6675dff6b011
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131103150"
---
# <a name="run-glowroot-on-azure-cosmos-db-cassandra-api"></a>Azure Cosmos DB Cassandra API에서 Glowroot 실행
[!INCLUDE[appliesto-cassandra-api](../includes/appliesto-cassandra-api.md)]

Glowroot는 애플리케이션의 성능을 최적화하고 모니터링하는 데 사용되는 애플리케이션 성능 관리 도구입니다. 이 문서에서는 이제 Azure Cosmos DB Cassandra API 내에서 Glowroot를 사용하여 애플리케이션의 성능을 모니터링하는 방법을 설명합니다.

## <a name="prerequisites-and-setup"></a>필수 조건 및 설정

* [Azure Cosmos DB Cassandra API 계정을 만듭니다.](manage-data-java.md#create-a-database-account)
* [Windows JAVA(버전 8) 설치](https://developers.redhat.com/products/openjdk/download)
> [!NOTE]
> 최신 버전과 호환되지 않는 알려진 특정 빌드 대상이 있습니다. 최신 버전의 JAVA가 이미 있는 경우에도 JDK8을 다운로드할 수 있습니다.
> JDK8 외에 최신 JAVA가 설치된 경우: 로컬 명령 프롬프트에서 %JAVA_HOME% 변수를 JDK8을 대상으로 설정합니다. 이렇게 하면 현재 세션에 대한 java 버전만 변경되고 전역 컴퓨터 설정은 그대로 유지됩니다. 
* [maven 설치](https://maven.apache.org/download.cgi)
    * 다음을 실행하여 성공적인 설치를 확인합니다. `mvn --version`

## <a name="run-glowroot-central-collector-with-cosmos-db-endpoint"></a>Cosmos DB 엔드포인트를 통해 Glowroot 중앙 수집기 실행
엔드포인트 구성이 완료되면 
1. [Glowroot 중앙 수집기 배포 다운로드](https://github.com/glowroot/glowroot/wiki/Central-Collector-Installation#central-collector-installation)
2. Cosmos DB Cassandra API 엔드포인트에서 다음 속성을 입력합니다.
    * cassandra.contactPoints
    * cassandra.username
    * cassandra.password
3. , 및 속성을 `cassandra.ssl=true` `cassandra.gcGraceSeconds=0` `cassandra.port=10350` 설정합니다.
4. glowroot-central.properties가 glowroot-central.jar과 동일한 폴더에 있는지 확인합니다.
5. `java -jar glowroot-central.jar`를 실행하여 Glowroot 실행을 시작합니다.

## <a name="faqs"></a>FAQ
Glowroot를 실행하거나 테스트하는 데 문제가 있는 경우 지원 티켓을 엽니다. Glowroot 테스트가 실행될 구독 ID 및 계정 이름을 제공합니다.

## <a name="next-steps"></a>다음 단계
- Java 애플리케이션을 사용하여 [Cassandra API 계정, 데이터베이스 및 테이블 만들기](create-account-java.md)를 시작합니다.
- Azure Cosmos DB Cassandra API에서 [지원되는 기능](cassandra-support.md)에 대해 알아봅니다.
