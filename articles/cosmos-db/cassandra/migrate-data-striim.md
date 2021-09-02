---
title: Striim을 사용하여 Azure Cosmos DB Cassandra API 계정으로 데이터 마이그레이션
description: Striim을 사용하여 Oracle 데이터베이스에서 Azure Cosmos DB Cassandra API 계정으로 데이터를 마이그레이션하는 방법을 알아봅니다.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: how-to
ms.date: 07/22/2019
ms.author: sngun
ms.reviewer: sngun
ms.openlocfilehash: 5c567e5bf64fdfcda9d6600fdcf5aa15c1a34f25
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2021
ms.locfileid: "123112958"
---
# <a name="migrate-data-to-azure-cosmos-db-cassandra-api-account-using-striim"></a>Striim을 사용하여 Azure Cosmos DB Cassandra API 계정으로 데이터 마이그레이션
[!INCLUDE[appliesto-cassandra-api](../includes/appliesto-cassandra-api.md)]

Azure Marketplace의 Striim 이미지는 데이터 웨어하우스와 데이터베이스에서 Azure로의 지속적인 실시간 데이터 이동을 제공합니다. 데이터를 이동하는 동안 인라인 비정규화, 데이터 변환을 수행하고 실시간 분석 및 데이터 보고 시나리오를 사용할 수 있습니다. Striim을 시작하여 엔터프라이즈 데이터를 Azure Cosmos DB Cassandra API로 지속적으로 이동하는 것은 쉽습니다. Azure는 Striim을 쉽게 배포하고 Azure Cosmos DB로 데이터를 마이그레이션할 수 있도록 하는 마켓플레이스 제품을 제공합니다. 

이 문서에서는 Striim을 사용하여 **Oracle 데이터베이스** 에서 **Azure Cosmos DB Cassandra API 계정** 으로 데이터를 마이그레이션하는 방법을 보여 줍니다.

## <a name="prerequisites"></a>사전 요구 사항

* [Azure 구독](../../guides/developer/azure-developer-guide.md#understanding-accounts-subscriptions-and-billing)이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)을 만듭니다.

* 일부 데이터가 포함된 온-프레미스에서 실행되는 Oracle 데이터베이스가 있어야 합니다.

## <a name="deploy-the-striim-marketplace-solution"></a>Striim 마켓플레이스 솔루션 배포

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.

1. **리소스 만들기** 를 선택하고 Azure Marketplace에서 **Striim** 을 검색합니다. 첫 번째 옵션과 **만들기** 를 선택합니다.

   :::image type="content" source="../sql/media/cosmosdb-sql-api-migrate-data-striim/striim-azure-marketplace.png" alt-text="Striim 마켓플레이스 항목 찾기":::

1. 그런 다음, Striim 인스턴스의 구성 속성을 입력합니다. Striim 환경은 가상 머신에 배포됩니다. **기본** 창에서 **VM 사용자 이름**, **VM 암호** 를 입력합니다(이 암호는 VM에 SSH로 연결하는 데 사용됨). Striim을 배포할 **구독**, **리소스 그룹** 및 **위치 세부 정보** 를 선택합니다. 완료되면 **확인** 을 선택합니다.

   :::image type="content" source="../sql/media/cosmosdb-sql-api-migrate-data-striim/striim-configure-basic-settings.png" alt-text="Striim의 기본 설정 구성":::


1. **Striim 클러스터 설정** 창에서 Striim 배포 유형과 가상 머신 크기를 선택합니다.

   |설정 | 값 | 설명 |
   | ---| ---| ---|
   |Striim 배포 유형 |독립 실행형 | Striim은 **독립 실행형** 또는 **클러스터** 배포 유형에서 실행할 수 있습니다. 독립 실행형 모드는 단일 가상 머신에 Striim 서버를 배포하며 데이터 볼륨에 따라 VM의 크기를 선택할 수 있습니다. 클러스터 모드는 선택한 크기의 여러 VM에 Striim 서버를 배포합니다. 노드가 2개보다 많은 클러스터 환경은 자동 고가용성 및 장애 조치(failover)를 제공합니다.</br></br> 이 자습서에서는 독립 실행형 옵션을 선택할 수 있습니다. 기본 "Standard_F4s" 크기의 VM을 사용합니다. | 
   | Striim 클러스터의 이름|    <Striim_cluster_Name>|  Striim 클러스터의 이름입니다.|
   | Striim 클러스터 암호|   <Striim_cluster_password>|  클러스터의 암호입니다.|

   양식을 작성한 후 계속하려면 **확인** 을 선택합니다.

1. **Striim 액세스 설정** 창에서 **공용 IP 주소**(기본값 선택), **Striim의 도메인 이름**, Striim UI에 로그인하는 데 사용하려는 **관리자 암호** 를 구성합니다. VNET 및 서브넷을 구성합니다(기본값 선택). 세부 정보를 입력한 후 계속하려면 **확인** 을 선택합니다.

   :::image type="content" source="../sql/media/cosmosdb-sql-api-migrate-data-striim/striim-access-settings.png" alt-text="Striim 액세스 설정":::

1. Azure는 배포의 유효성을 검사하고 모든 것이 양호한지 확인합니다. 유효성 검사를 완료하는 데 몇 분 정도 걸립니다. 유효성 검사가 완료되면 **확인** 을 선택합니다.
  
1. 마지막으로 사용 약관을 검토하고 **만들기** 를 선택하여 Striim 인스턴스를 만듭니다. 

## <a name="configure-the-source-database"></a>원본 데이터베이스 구성

이 섹션에서는 Oracle 데이터베이스를 데이터 이동의 원본으로 구성합니다.  Oracle에 연결하려면 [Oracle JDBC 드라이버](https://www.oracle.com/technetwork/database/features/jdbc/jdbc-ucp-122-3110062.html)가 필요합니다. 원본 Oracle 데이터베이스에서 변경 사항을 읽으려면 [LogMiner](https://www.oracle.com/technetwork/database/features/availability/logmineroverview-088844.html) 또는 [XStream API](https://docs.oracle.com/cd/E11882_01/server.112/e16545/xstrm_intro.htm#XSTRM72647)를 사용할 수 있습니다. Oracle 데이터베이스에서 데이터를 읽거나 쓰거나 유지하려면 Oracle JDBC 드라이버가 Striim의 Java 클래스 경로에 있어야 합니다.

[ojdbc8.jar](https://www.oracle.com/technetwork/database/features/jdbc/jdbc-ucp-122-3110062.html) 드라이버를 로컬 컴퓨터에 다운로드합니다. 나중에 Striim 클러스터에 설치합니다.

## <a name="configure-target-database"></a>대상 데이터베이스 구성

이 섹션에서는 Azure Cosmos DB Cassandra API 계정을 데이터 이동 대상으로 구성합니다.

1. Azure Portal을 사용하여 [Azure Cosmos DB Cassandra API 계정](manage-data-dotnet.md#create-a-database-account)을 만듭니다.

1. Azure Cosmos 계정의 **데이터 탐색기** 창으로 이동합니다. **새 테이블** 을 선택하여 새 컨테이너를 만듭니다. Oracle 데이터베이스에서 Azure Cosmos DB로 *제품* 및 *주문* 데이터를 마이그레이션하는 경우를 가정합니다. Orders 컨테이너를 사용하여 **StriimDemo** 라는 새 키스페이스를 만듭니다. **RU 1,000개**(이 예에서는 RU 1,000개를 사용하지만 워크로드에 대한 예상 처리량을 사용해야 함)와 기본 키로 **/ORDER_ID** 를 사용하는 컨테이너를 프로비저닝합니다. 이러한 값은 원본 데이터에 따라 달라집니다. 

   :::image type="content" source="./media/migrate-data-striim/create-cassandra-api-account.png" alt-text="Cassandra API 계정 만들기":::

## <a name="configure-oracle-to-azure-cosmos-db-data-flow"></a>Oracle을 Azure Cosmos DB 데이터 흐름으로 구성

1. 이제 Striim으로 돌아갑니다. Striim과 상호 작용하기 전에 앞서 다운로드한 Oracle JDBC 드라이버를 설치합니다.

1. Azure Portal에서 배포한 Striim 인스턴스로 이동합니다. 상단 메뉴 표시줄에서 **연결** 단추를 선택하고 **SSH** 탭에서 **VM 로컬 계정을 사용하여 로그인** 필드에 URL을 복사합니다.

   :::image type="content" source="../sql/media/cosmosdb-sql-api-migrate-data-striim/get-ssh-url.png" alt-text="SSH URL 얻기":::

1. 새 터미널 창을 열고 Azure Portal에서 복사한 SSH 명령을 실행합니다. 이 문서에서는 MacOS에서 터미널을 사용합니다. Windows 컴퓨터에서는 PuTTY 또는 다른 SSH 클라이언트를 사용하여 유사한 지침을 따를 수 있습니다. 메시지가 표시되면 **yes** 를 입력하여 계속하고 이전 단계에서 가상 머신에 설정한 **암호** 를 입력합니다.

   :::image type="content" source="../sql/media/cosmosdb-sql-api-migrate-data-striim/striim-vm-connect.png" alt-text="Striim VM에 연결":::

1. 이제 새 터미널 탭을 열어 이전에 다운로드한 **ojdbc8.jar** 파일을 복사합니다. 다음 SCP 명령을 사용하여 로컬 컴퓨터의 jar 파일을 Azure에서 실행 중인 Striim 인스턴스의 tmp 폴더로 복사합니다.

   ```bash
   cd <Directory_path_where_the_Jar_file_exists> 
   scp ojdbc8.jar striimdemo@striimdemo.westus.cloudapp.azure.com:/tmp
   ```

   :::image type="content" source="../sql/media/cosmosdb-sql-api-migrate-data-striim/copy-jar-file.png" alt-text="로컬 컴퓨터에서 Striim으로 Jar 파일 복사":::

1. 다음으로 Striim 인스턴스에 SSH를 수행하고 sudo로 로그인한 창으로 다시 이동합니다. 다음 명령을 사용하여 **ojdbc8.jar** 파일을 **/tmp** 디렉터리에서 Striim 인스턴스의 **lib** 디렉터리로 이동합니다.

   ```bash
   sudo su
   cd /tmp
   mv ojdbc8.jar /opt/striim/lib
   chmod +x ojdbc8.jar
   ```

   :::image type="content" source="../sql/media/cosmosdb-sql-api-migrate-data-striim/move-jar-file.png" alt-text="Jar 파일을 lib 폴더로 이동":::


1. 동일한 터미널 창에서 다음 명령을 실행하여 Striim 서버를 다시 시작합니다.

   ```bash
   Systemctl stop striim-node
   Systemctl stop striim-dbms
   Systemctl start striim-dbms
   Systemctl start striim-node
   ```
 
1. Striim을 시작하는 데 1분 정도 걸립니다. 상태를 보려면 다음 명령을 실행합니다. 

   ```bash
   tail -f /opt/striim/logs/striim-node.log
   ```

1. 이제 Azure로 다시 이동하여 Striim VM의 공용 IP 주소를 복사합니다. 

   :::image type="content" source="../sql/media/cosmosdb-sql-api-migrate-data-striim/copy-public-ip-address.png" alt-text="Striim VM IP 주소 복사":::

1. Striim의 웹 UI로 이동하려면 브라우저에서 새 탭을 열고 공용 IP를 복사하고 그 뒤에 9080을 입력합니다. Azure Portal에서 지정한 관리자 암호와 함께 **관리자** 사용자 이름을 사용하여 로그인합니다.

   :::image type="content" source="../sql/media/cosmosdb-sql-api-migrate-data-striim/striim-login-ui.png" alt-text="Striim에 로그인":::

1. 이제 Striim의 홈페이지가 표시됩니다. **Dashboards**, **Apps** 및 **SourcePreview** 의 세 가지 창이 있습니다. Dashboards 창에서는 실시간으로 데이터를 이동하고 시각화할 수 있습니다. Apps 창에는 스트리밍 데이터 파이프라인 또는 데이터 흐름이 포함됩니다. 페이지 오른쪽에는 데이터를 이동하기 전에 미리 볼 수 있는 SourcePreview가 있습니다.

1. **Apps** 창을 선택합니다. 여기서는 이 창을 중점적으로 설명하겠습니다. Striim에 대해 학습하는 데 사용할 수 있는 다양한 샘플 앱이 있습니다. 그러나 이 문서에서는 직접 만들겠습니다. 오른쪽 상단에서 **앱 추가** 단추를 선택합니다.

   :::image type="content" source="../sql/media/cosmosdb-sql-api-migrate-data-striim/add-striim-app.png" alt-text="Striim 앱 추가":::

1. Striim 애플리케이션을 만드는 방법에는 몇 가지가 있습니다. 이 시나리오에서는 **처음부터 시작** 을 선택합니다.

   :::image type="content" source="./media/migrate-data-striim/start-app-from-scratch.png" alt-text="처음부터 앱 시작":::

1. **oraToCosmosDB** 와 같이 애플리케이션에 친숙한 이름을 지정하고 **저장** 을 선택합니다.

   :::image type="content" source="./media/migrate-data-striim/create-new-application.png" alt-text="새 애플리케이션 만들기":::

1. 흐름 디자이너가 나타납니다. 여기서 스트리밍 애플리케이션을 만들기 위해 기본 제공 커넥터를 끌어 놓을 수 있습니다. 검색 창에서 **Oracle** 을 입력하고 **Oracle CDC** 원본을 앱 캔버스로 끌어 놓습니다.  

   :::image type="content" source="./media/migrate-data-striim/oracle-cdc-source.png" alt-text="Oracle CDC 원본":::

1. Oracle 인스턴스의 원본 구성 속성을 입력합니다. 원본 이름은 Striim 애플리케이션의 명명 규칙일 뿐이며 **src_onPremOracle** 과 같은 이름을 사용할 수 있습니다. 또한 어댑터 유형, 연결 URL, 사용자 이름, 암호, 테이블 이름과 같은 기타 세부 사항을 입력합니다. **저장** 을 선택하여 계속합니다.

   :::image type="content" source="./media/migrate-data-striim/configure-source-parameters.png" alt-text="원본 매개 변수 구성":::

1. 이제 스트림의 웨이브 아이콘을 클릭하여 대상 Azure Cosmos DB 인스턴스를 연결합니다. 

   :::image type="content" source="./media/migrate-data-striim/connect-to-target.png" alt-text="대상에 연결":::

1. 대상을 구성하기 전에 [Baltimore 루트 인증서를 Striim의 Java 환경에](/bingmaps/articles/ssl-certificate-validation-for-java-applications#configuring-root-certificates) 추가했는지 확인합니다.

1. 대상 Azure Cosmos DB 인스턴스의 구성 속성을 입력하고 **저장** 을 선택하여 계속합니다. 유의해야 할 주요 매개 변수는 다음과 같습니다.

   * **어댑터** - **DatabaseWriter** 를 사용합니다. Azure Cosmos DB Cassandra API에 쓸 때 DatabaseWriter가 필요합니다. Cassandra 드라이버 3.6.0은 Striim과 함께 번들로 제공됩니다. DatabaseWriter가 Azure Cosmos 컨테이너에서 프로비전된 RU 수를 초과하면 애플리케이션 작동이 중단됩니다.

   * **연결 URL** - Azure Cosmos DB JDBC 연결 URL을 지정합니다. URL은 `jdbc:cassandra://<contactpoint>:10350/<databaseName>?SSL=true` 형식입니다.

   * **사용자 이름** - Azure Cosmos 계정 이름을 지정합니다.
   
   * **암호** - Azure Cosmos 계정의 기본 키를 지정합니다.

   * **테이블** - 대상 테이블에는 기본 키가 있어야 하며 기본 키는 업데이트할 수 없습니다.

   :::image type="content" source="./media/migrate-data-striim/configure-target-parameters1.png" alt-text="구성 가능한 대상 속성을 보여주는 스크린샷":::

   :::image type="content" source="./media/migrate-data-striim/configure-target-parameters2.png" alt-text="대상 속성 구성":::

1. 이제 계속해서 Striim 애플리케이션을 실행합니다. 상단 메뉴 모음에서 **생성일** 을 선택하고 **앱 배포** 를 선택합니다. 배포 창에서 배포 토폴로지의 특정 부분에서 애플리케이션의 특정 부분을 실행할지 여부를 지정할 수 있습니다. Azure를 통한 간단한 배포 토폴로지를 실행 중이므로 기본 옵션을 사용합니다.

   :::image type="content" source="./media/migrate-data-striim/deploy-the-app.png" alt-text="앱 배포":::


1. 이제 계속해서 스트림을 미리 보고 Striim을 통해 흐르는 데이터를 살펴보겠습니다. 웨이브 아이콘을 클릭하고 옆의 눈 아이콘을 클릭합니다. 배포 후 스트림을 미리 보고 데이터 흐름을 확인할 수 있습니다. **웨이브** 아이콘과 그 옆에 있는 **안구** 를 선택합니다. 상단 메뉴 모음에서 **Deployed(배포됨)** 단추를 선택하고 **Start App(앱 시작)** 을 선택합니다.

   :::image type="content" source="./media/migrate-data-striim/start-the-app.png" alt-text="앱 시작":::

1. **CDC(변경 데이터 캡처)** 읽기 프로그램을 사용하여 Striim은 데이터베이스에서 새로운 변경 사항만 선택합니다. 원본 테이블을 통해 흐르는 데이터가 있는 경우 표시됩니다. 그러나 이는 샘플 테이블이므로 원본이 애플리케이션에 연결되어 있지 않습니다. 샘플 데이터 생성기를 사용하는 경우 이벤트 체인을 Oracle 데이터베이스에 삽입할 수 있습니다.

1. Striim 플랫폼을 통해 흐르는 데이터가 표시됩니다. Striim은 테이블과 관련된 모든 메타데이터도 선택하므로 데이터를 모니터링하고 데이터가 올바른 대상에 도달하는지 확인하는 데 도움이 됩니다.

   :::image type="content" source="./media/migrate-data-striim/setup-cdc-pipeline.png" alt-text="CDC 파이프라인 설정":::

1. 마지막으로 Azure에 로그인하고 Azure Cosmos 계정으로 이동하겠습니다. 데이터 탐색기를 새로 고치면 데이터가 도착한 것을 볼 수 있습니다. 

Azure에서 Striim 솔루션을 사용하면 데이터를 지속적으로 Azure Cosmos DB로 마이그레이션(Oracle, Cassandra, MongoDB 등 다양한 원본에서 Azure Cosmos DB로)할 수 있습니다. 자세한 내용은 [Striim 웹 사이트](https://www.striim.com/)를 방문하여 [Striim 30일 체험 평가판을 다운로드](https://go2.striim.com/download-free-trial)하고 Striim으로 마이그레이션 경로를 설정할 때 문제가 발생하면 [지원 요청](https://go2.striim.com/request-support-striim)을 제출합니다.

## <a name="next-steps"></a>다음 단계

* 데이터를 Azure Cosmos DB SQL API로 마이그레이션하는 경우 [Striim을 사용하여 Cassandra API 계정으로 데이터를 마이그레이션하는 방법](../cosmosdb-sql-api-migrate-data-striim.md)을 참조하세요.

* [Azure Cosmos DB 메트릭을 사용하여 데이터 모니터링 및 디버그](../use-metrics.md)