---
title: Azure Cosmos DB에서 Spring Data Apache Cassandra API를 사용하는 방법
description: Azure Cosmos DB에서 Spring Data Apache Cassandra API를 사용하는 방법을 알아보세요.
ms.service: cosmos-db
author: TheovanKraay
ms.author: thvankra
ms.subservice: cosmosdb-cassandra
ms.devlang: java
ms.topic: how-to
ms.date: 07/17/2021
ms.openlocfilehash: 92f9045891aa56589e13d887dfea6c3157292185
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122536729"
---
# <a name="how-to-use-spring-data-apache-cassandra-api-with-azure-cosmos-db"></a>Azure Cosmos DB에서 Spring Data Apache Cassandra API를 사용하는 방법
[!INCLUDE[appliesto-cassandra-api](../includes/appliesto-cassandra-api.md)]

이 문서는 [Spring Data]를 사용하는 샘플 애플리케이션을 만들어 [Azure Cosmos DB Cassandra API](/azure/cosmos-db/cassandra-introduction)를 사용하여 정보를 저장 및 검색하는 것을 보여줍니다.

## <a name="prerequisites"></a>사전 요구 사항

이 문서의 단계를 완료하려면 다음 필수 구성 요소가 필요합니다.

* Azure 구독. Azure 구독이 아직 없는 경우 [MSDN 구독자 혜택]을 활성화하거나 [체험판 Azure 계정]에 등록할 수 있습니다.
* 지원되는 JDK(Java Development Kit) Azure에서 개발할 때 사용할 수 있는 JDK에 대한 자세한 내용은 [Azure 및 Azure Stack에 대한 Java 지원](/azure/developer/java/fundamentals/java-support-on-azure)을 참조하세요.
* [Apache Maven](http://maven.apache.org/), 버전 3.0 이상
* [Curl](https://curl.haxx.se/) 또는 기능을 테스트하는 유사한 HTTP 유틸리티.
* [Git](https://git-scm.com/downloads) 클라이언트

> [!NOTE]
> 아래에 언급된 샘플은 Azure Cosmos DB Cassandra API를 사용할 때 더 나은 환경을 위해 사용자 지정 확장을 구현합니다. 여기에는 사용자 지정 다시 시도 및 부하 분산 정책과 권장 연결 설정 구현이 포함됩니다. 사용자 지정 정책이 사용되는 방식에 대한 보다 광범위한 탐색은 [버전 3](https://github.com/Azure-Samples/azure-cosmos-cassandra-extensions-java-sample) 및 [버전 4](https://github.com/Azure-Samples/azure-cosmos-cassandra-extensions-java-sample-v4)에 대한 Java 샘플을 참조하세요. 

## <a name="create-a-cosmos-db-cassandra-api-account"></a>Cosmos DB Cassandra API 계정 만들기

[!INCLUDE [cosmos-db-create-dbaccount-cassandra](../includes/cosmos-db-create-dbaccount-cassandra.md)]

## <a name="configure-the-sample-application"></a>샘플 애플리케이션 구성

다음 절차에서는 테스트 애플리케이션을 구성합니다.

1. 명령 셸을 열고 다음 예 중 하나를 복제합니다.

   Java [버전 3 드라이버](https://github.com/datastax/java-driver/tree/3.x) 및 해당 Spring 버전의 경우:

   ```shell
   git clone https://github.com/Azure-Samples/spring-data-cassandra-on-azure-extension-v3.git
   ```
   
   Java [버전 4 드라이버](https://github.com/datastax/java-driver/tree/4.x) 및 해당 Spring 버전의 경우:

   ```shell
   git clone https://github.com/Azure-Samples/spring-data-cassandra-on-azure-extension-v4.git
   ```     

    > [!NOTE]    
    > 아래에 설명된 사용법은 위의 Java 버전 3 및 버전 4 샘플 모두에 대해 동일하지만 사용자 지정 다시 시도 및 부하 분산 정책을 포함하기 위해 구현된 방식이 다릅니다. 기존 Spring Java 애플리케이션을 변경하는 경우 사용자 지정 정책을 구현하는 방법을 이해하기 위해 코드를 검토하는 것이 좋습니다.  

1. 샘플 프로젝트의 *리소스* 디렉터리에서 *application.properties* 파일을 찾거나, 파일이 아직 없는 경우 해당 파일을 만듭니다.

1. 텍스트 편집기에서 *application.properties* 파일을 열어 파일에 다음 줄을 추가하거나 구성하고 샘플 값을 앞서 다룬 적절한 값으로 바꿉니다.

   ```yaml
   spring.data.cassandra.contact-points=<Account Name>.cassandra.cosmos.azure.com
   spring.data.cassandra.port=10350
   spring.data.cassandra.username=<Account Name>
   spring.data.cassandra.password=********
   ```

   위치:

   | 매개 변수 | 설명 |
   |---|---|
   | `spring.data.cassandra.contact-points` | 이 문서에서 앞서 다룬 **접점** 을 지정합니다. |
   | `spring.data.cassandra.port` | 이 문서에서 앞서 다룬 **포트** 를 지정합니다. |
   | `spring.data.cassandra.username` | 이 문서에서 앞서 다룬 **사용자 이름** 을 지정합니다. |
   | `spring.data.cassandra.password` | 이 문서에서 앞서 다룬 **기본 암호** 를 지정합니다. |

1. *application.properties* 파일을 저장하고 닫습니다.

## <a name="package-and-test-the-sample-application"></a>샘플 애플리케이션 패키지 및 테스트하기 

애플리케이션을 빌드하고 테스트할 .pom 파일이 포함된 디렉터리로 이동합니다.

1. 다음 예와 같이 Maven을 사용하여 샘플 애플리케이션을 컴파일합니다.

   ```shell
   mvn clean package
   ```

1. 다음 예와 같이 샘플 애플리케이션을 시작합니다.

   ```shell
   java -jar target/spring-data-cassandra-on-azure-0.1.0-SNAPSHOT.jar
   ```

1. 다음 예와 같이 명령 프롬프트에서 `curl`을 사용하여 새 레코드를 만듭니다.

   ```shell
   curl -s -d "{\"name\":\"dog\",\"species\":\"canine\"}" -H "Content-Type: application/json" -X POST http://localhost:8080/pets

   curl -s -d "{\"name\":\"cat\",\"species\":\"feline\"}" -H "Content-Type: application/json" -X POST http://localhost:8080/pets
   ```

   애플리케이션이 다음과 같이 값을 반환해야 합니다.

   ```shell
   Added Pet{id=60fa8cb0-0423-11e9-9a70-39311962166b, name='dog', species='canine'}.

   Added Pet{id=72c1c9e0-0423-11e9-9a70-39311962166b, name='cat', species='feline'}.
   ```

1. 다음 예와 같이 명령 프롬프트에서 `curl`을 사용하여 모든 기존 레코드 검색합니다.

   ```shell
   curl -s http://localhost:8080/pets
   ```

   애플리케이션이 다음과 같이 값을 반환해야 합니다.

   ```json
   [{"id":"60fa8cb0-0423-11e9-9a70-39311962166b","name":"dog","species":"canine"},{"id":"72c1c9e0-0423-11e9-9a70-39311962166b","name":"cat","species":"feline"}]
   ```

## <a name="clean-up-resources"></a>리소스 정리

[!INCLUDE [cosmosdb-delete-resource-group](../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>다음 단계

Spring과 Azure에 대한 자세한 사항은 Azure의 Spring 설명서 센터를 참조합니다.

> [!div class="nextstepaction"]
> [Azure의 Spring](../../index.yml)

### <a name="additional-resources"></a>추가 리소스

Java와 함께 Azure를 사용하는 방법에 관한 자세한 정보는 [Java 개발자를 위한 Azure]와 [Azure DevOps 및 Java 사용하기]를 참조하세요.

<!-- URL List -->

[Java 개발자를 위한 Azure]: ../index.yml
[체험판 Azure 계정]: https://azure.microsoft.com/pricing/free-trial/
[Azure DevOps 및 Java 사용하기]: /azure/devops/
[MSDN 구독자 혜택]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/
[Spring Boot]: http://projects.spring.io/spring-boot/
[Spring Data]: https://spring.io/projects/spring-data
[Spring Initializr]: https://start.spring.io/
[Spring Framework]: https://spring.io/

<!-- IMG List -->

[COSMOSDB01]: media/access-data-spring-data-app/create-cosmos-db-01.png
[COSMOSDB02]: media/access-data-spring-data-app/create-cosmos-db-02.png
[COSMOSDB03]: media/access-data-spring-data-app/create-cosmos-db-03.png
[COSMOSDB04]: media/access-data-spring-data-app/create-cosmos-db-04.png
[COSMOSDB05]: media/access-data-spring-data-app/create-cosmos-db-05.png
[COSMOSDB05-1]: media/access-data-spring-data-app/create-cosmos-db-05-1.png
[COSMOSDB06]: media/access-data-spring-data-app/create-cosmos-db-06.png