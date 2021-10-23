---
title: Elastic Database 도구 시작하기
description: 쉽게 실행할 수 있는 샘플 앱을 포함하여 Azure SQL Database의 Elastic Database 도구 기능에 대해 기본적으로 설명합니다.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: scoriani
ms.author: scoriani
ms.reviewer: mathoma
ms.date: 10/18/2021
ms.openlocfilehash: a27957a289f3ab94e0c55462715a668b9f85e215
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130239511"
---
# <a name="get-started-with-elastic-database-tools"></a>Elastic Database 도구 시작하기
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

이 문서는 샘플 앱을 실행하도록 도와서 [Elastic Database 클라이언트 라이브러리](elastic-database-client-library.md)에 대한 개발자 환경을 소개합니다. 샘플 앱은 간단한 분할 애플리케이션을 만들고 Azure SQL Database의 Elastic Database 도구의 핵심 기능을 살펴봅니다. [분할 맵 관리](elastic-scale-shard-map-management.md), [데이터 종속 라우팅](elastic-scale-data-dependent-routing.md) 및 [다중 분할 쿼리](elastic-scale-multishard-querying.md)에 대한 사용 사례에 중점을 둡니다. 클라이언트 라이브러리는 .Net은 물론 Java에서도 사용할 수 있습니다.

## <a name="elastic-database-tools-for-java"></a>Java용 Elastic Database 도구

### <a name="prerequisites"></a>사전 요구 사항

* JDK(Java Developer Kit) 버전 1.8 이상
* [Maven](https://maven.apache.org/download.cgi)
* SQL Database 또는 로컬 SQL Server 인스턴스

### <a name="download-and-run-the-sample-app"></a>샘플 앱 다운로드 및 실행

JAR 파일을 빌드하고 샘플 프로젝트를 시작하려면 다음을 수행합니다.

1. 샘플 앱과 함께 클라이언트 라이브러리가 포함된 [GitHub 리포지토리](https://github.com/Microsoft/elastic-db-tools-for-java)를 복제합니다.

2. _./sample/src/main/resources/resource.properties_ 파일을 편집하여 다음을 설정합니다.
    * TEST_CONN_USER
    * TEST_CONN_PASSWORD
    * TEST_CONN_SERVER_NAME

3. 샘플 프로젝트를 빌드하려면 _./sample_ 디렉터리에서 다음 명령을 실행합니다.

    ```
    mvn install
    ```

4. 샘플 프로젝트를 시작하려면 _./sample_ 디렉터리에서 다음 명령을 실행합니다.

    ```
    mvn -q exec:java "-Dexec.mainClass=com.microsoft.azure.elasticdb.samples.elasticscalestarterkit.Program"
    ```

5. 클라이언트 라이브러리 기능에 대해 자세히 살펴보려면 여러 옵션을 사용해 봅니다. 샘플 앱 구현에 대해 알아보려면 코드를 살펴보세요.

    ![Progress-java][5]

축하합니다! Azure SQL Database에서 Elastic Database 도구를 사용하여 첫 번째 분할 애플리케이션을 빌드하고 실행했습니다. Visual Studio 또는 SQL Server Management Studio를 통해 데이터베이스에 연결하여 샘플에서 작성된 분할된 데이터베이스를 빠르게 확인해 보세요. 샘플에서 작성된 분할된 데이터베이스 맵 관리자 데이터베이스와 새 샘플 분할 데이터베이스를 확인할 수 있습니다.

클라이언트 라이브러리를 자신의 Maven 프로젝트에 추가하려면 POM 파일에 다음 종속성을 추가합니다.

```xml
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>elastic-db-tools</artifactId>
    <version>1.0.0</version>
</dependency>
```

## <a name="elastic-database-tools-for-net"></a>.Net용 Elastic Database 도구

### <a name="prerequisites"></a>사전 요구 사항

* C#이 있는 Visual Studio 2012 이상. [Visual Studio 다운로드](https://www.visualstudio.com/downloads/download-visual-studio-vs.aspx)에서 무료 버전을 다운로드하세요.
* NuGet 2.7 이상. 최신 버전을 설치하려면 [NuGet 설치](https://docs.nuget.org/docs/start-here/installing-nuget)를 참조하세요.

### <a name="download-and-run-the-sample-app"></a>샘플 앱 다운로드 및 실행

라이브러리를 설치하려면 [Microsoft.Azure.SqlDatabase.ElasticScale.Client](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/)로 이동합니다. 라이브러리는 다음 섹션에 설명된 샘플 앱과 함께 설치됩니다.

샘플을 다운로드하고 실행하려면 다음 단계를 수행합니다.

1. [Azure SQL용 탄력적 DB 도구 - 시작 샘플](https://github.com/Azure/elastic-db-tools)을 다운로드합니다. 선택한 위치에 샘플의 압축을 풉니다.

2. 프로젝트를 만들려면 *탄력적-데이터베이스-도구-마스터* 디렉터리에서 *ElasticDatabaseTools.sln* 솔루션을 엽니다. 

3. 시작 프로젝트로 *ElasticScaleStarterKit* 프로젝트를 설정합니다.

4. *ElasticScaleStarterKit* 프로젝트에서 *App.config* 파일을 여십시오. 그런 다음 파일의 지침에 따라 서버 이름과 로그인 정보(사용자 이름 및 암호)를 추가합니다.

5. 애플리케이션을 빌드 및 실행합니다. 메시지가 표시되면 Visual Studio가 솔루션의 NuGet 패키지를 복원하도록 허용합니다. 그러면 NuGet에서 최신 버전의 Elastic Database 클라이언트 라이브러리가 다운로드됩니다.

6. 클라이언트 라이브러리 기능에 대해 자세히 살펴보려면 여러 옵션을 사용해 봅니다. 애플리케이션이 콘솔 출력에서 수행하는 단계와 백그라운드의 코드도 확인해 보세요.

   ![진행률][4]

축하합니다! SQL Database에서 Elastic Database 도구를 사용하여 첫 번째 분할 애플리케이션을 빌드하고 실행했습니다. Visual Studio 또는 SQL Server Management Studio를 통해 데이터베이스에 연결하여 샘플에서 작성된 분할된 데이터베이스를 빠르게 확인해 보세요. 샘플에서 작성된 분할된 데이터베이스 맵 관리자 데이터베이스와 새 샘플 분할 데이터베이스를 확인할 수 있습니다.

> [!IMPORTANT]
> Azure 및 SQL Database에 대한 업데이트와 동기화된 상태를 유지하도록 항상 최신 버전의 Management Studio를 사용하는 것이 좋습니다. [SQL Server Management Studio를 업데이트합니다](/sql/ssms/download-sql-server-management-studio-ssms).

## <a name="key-pieces-of-the-code-sample"></a>코드 샘플의 주요 부분

* **분할된 데이터베이스 및 분할된 데이터베이스 맵 관리**: 코드는 *ShardManagementUtils.cs* 파일에서 분할된 데이터베이스, 범위 및 매핑으로 작업하는 방법을 보여 줍니다. 자세한 내용은 [분할된 데이터베이스 맵 관리자를 사용하여 데이터베이스 확장](https://go.microsoft.com/?linkid=9862595)을 참조하세요.  

* **데이터 종속 라우팅**: 적절한 분할로의 트랜잭션 라우팅은 *DataDependentRoutingSample.cs* 파일에 나와 있습니다. 자세한 내용은 [데이터 종속 라우팅](https://go.microsoft.com/?linkid=9862596)을 참조하세요.

* **여러 분할된 데이터베이스에 대한 쿼리**: 여러 분할된 데이터베이스에 대한 쿼리 방법은 *MultiShardQuerySample.cs* 파일에서 설명합니다. 자세한 내용은 [다중 분할 쿼리](https://go.microsoft.com/?linkid=9862597)를 참조하세요.

* **비어 있는 분할 추가**: *CreateShardSample.cs* 파일의 코드를 사용하여 비어 있는 새 분할을 반복적으로 추가할 수 있습니다. 자세한 내용은 [분할된 데이터베이스 맵 관리자를 사용하여 데이터베이스 확장](https://go.microsoft.com/?linkid=9862595)을 참조하세요.

## <a name="other-elastic-scale-operations"></a>기타 탄력적인 확장 작업

* **기존의 분할된 데이터베이스 분할**: 분할/병합 도구를 통해 분할된 데이터베이스를 분할하는 기능이 제공됩니다. 자세한 내용은 [확장된 클라우드 데이터베이스 간 데이터 이동](elastic-scale-overview-split-and-merge.md)을 참조하세요.

* **기존의 분할된 데이터베이스 병합**: 분할 병합도 분할/병합 도구를 사용하여 수행할 수 있습니다. 자세한 내용은 [확장된 클라우드 데이터베이스 간 데이터 이동](elastic-scale-overview-split-and-merge.md)을 참조하세요.

## <a name="cost"></a>Cost

Elastic Database 도구 라이브러리는 무료로 제공됩니다. Elastic Database 도구를 사용하면 Azure 사용량 비용 이외의 요금이 추가되지 않습니다.

예를 들어 샘플 애플리케이션이 새 데이터베이스를 만드는 경우 이 기능의 비용은 선택한 SQL Database 버전과 애플리케이션의 Azure 사용량에 따라 부과됩니다.

가격 책정 정보는 [SQL Database 가격 책정 정보](https://azure.microsoft.com/pricing/details/sql-database/)를 참조하세요.

## <a name="next-steps"></a>다음 단계

Elastic Database 도구에 대한 자세한 내용은 다음 페이지를 참조하세요.

* 코드 샘플:
  * Elastic Database 도구([.NET](https://github.com/Azure/elastic-db-tools), [Java](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-elasticdb-tools%22))
  * [Azure SQL용 Elastic Database 도구 - Entity Framework 통합](https://code.msdn.microsoft.com/Elastic-Scale-with-Azure-bae904ba?SRC=VSIDE)
  * [스크립트 센터의 분할된 데이터베이스 탄력성](https://gallery.technet.microsoft.com/scriptcenter/Elastic-Scale-Shard-c9530cbe)
* 블로그: [탄력적인 확장 발표](https://azure.microsoft.com/blog/20../../introducing-elastic-scale-preview-for-azure-sql-database/)
* 토론 포럼: [Azure SQL Database에 대한 Microsoft Q&A 질문 페이지](/answers/topics/azure-sql-database.html)
* 성능 측정을 위한: [분할된 맵 관리자에 대한 성능 카운터](elastic-database-client-library.md)

<!--Anchors-->
[The Elastic Scale Sample Application]: #The-Elastic-Scale-Sample-Application
[Download and Run the Sample App]: #Download-and-Run-the-Sample-App
[Cost]: #Cost
[Next steps]: #next-steps

<!--Image references-->
[1]: ./media/elastic-scale-get-started/newProject.png
[2]: ./media/elastic-scale-get-started/click-online.png
[3]: ./media/elastic-scale-get-started/click-CSharp.png
[4]: ./media/elastic-scale-get-started/output2.png
[5]: ./media/elastic-scale-get-started/java-client-library.PNG