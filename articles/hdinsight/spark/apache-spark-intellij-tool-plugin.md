---
title: 'Azure Toolkit for IntelliJ: Spark 앱 - HDInsight'
description: Azure Toolkit for IntelliJ를 사용하여 Scala로 작성된 Spark 애플리케이션을 개발한 후 HDInsight Spark 클러스터로 제출합니다.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 04/14/2020
ms.openlocfilehash: aeb6c9e266bdb663b9a664c8509e9cafab8a5128
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124743005"
---
# <a name="use-azure-toolkit-for-intellij-to-create-apache-spark-applications-for-hdinsight-cluster"></a>Azure Toolkit for IntelliJ를 사용하여 HDInsight 클러스터용 Apache Spark 애플리케이션 만들기

이 문서에서는 IntelliJ IDE용 **Azure 도구 키트** 플러그 인을 사용하여 Azure HDInsight에서 Apache Spark 애플리케이션을 개발하는 방법을 설명합니다. [Azure HDInsight](../hdinsight-overview.md)는 클라우드의 관리형 오픈 소스 분석 서비스입니다. 서비스를 사용하면 Hadoop, Apache Spark, Apache Hive 및 Apache Kafka와 같은 오픈 소스 프레임워크를 사용할 수 있습니다.

다음과 같은 몇 가지 방식으로 **Azure Toolkit** 플러그 인을 사용할 수 있습니다.

* HDInsight Spark 클러스터에서 Scala Spark 애플리케이션을 개발 및 제출합니다.
* Azure HDInsight Spark 클러스터 리소스에 액세스합니다.
* Scala Spark 애플리케이션을 로컬로 개발 및 실행합니다.

이 문서에서는 다음 방법을 설명합니다.
> [!div class="checklist"]
> * Azure Toolkit for IntelliJ 플러그 인 사용
> * Apache Spark 애플리케이션 개발
> * Azure HDInsight 클러스터에 애플리케이션 제출

## <a name="prerequisites"></a>사전 요구 사항

* HDInsight의 Apache Spark. 자세한 내용은 [Azure HDInsight에서 Apache Spark 클러스터 만들기](apache-spark-jupyter-spark-sql.md)를 참조하세요. 공용 클라우드의 HDInsight 클러스터만 지원 되 고 다른 안전한 클라우드 유형 (예: 정부 클라우드)은 지원 되지 않습니다.

* [Oracle Java Development 키트](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html).  이 문서에서는 Java 버전 8.0.202를 사용합니다.

* IntelliJ IDEA. 이 문서에서는 [IntelliJ IDEA 커뮤니티 버전  2018.3.4](https://www.jetbrains.com/idea/download/)를 사용합니다.

* Azure Toolkit for IntelliJ.  [Azure Toolkit for IntelliJ 설치](/azure/developer/java/toolkit-for-intellij/)를 참조하세요.

## <a name="install-scala-plugin-for-intellij-idea"></a>IntelliJ IDEA용 Scala 플러그 인 설치

Scala 플러그인 설치 단계:

1. IntelliJ IDEA를 엽니다.

2. 시작 화면에서 **구성** > **플러그인** 으로 이동하여 **플러그인** 창을 엽니다.

    :::image type="content" source="./media/apache-spark-intellij-tool-plugin/enable-scala-plugin1.png" alt-text="IntelliJ IDEA는 Scala 플러그인을 활성화합니다" border="true":::

3. 새 창에 제공되는 Scala 플러그인에 대해 **설치** 를 선택합니다.  

    :::image type="content" source="./media/apache-spark-intellij-tool-plugin/install-scala-plugin.png" alt-text="IntelliJ IDEA는 Scala 플러그인을 설치합니다" border="true":::

4. 플러그 인이 성공적으로 설치된 후에 IDE를 다시 시작해야 합니다.

## <a name="create-a-spark-scala-application-for-an-hdinsight-spark-cluster"></a>HDInsight Spark 클러스터에서 Spark Scala 애플리케이션 만들기

1. IntelliJ IDEA를 시작하고 **새 프로젝트 만들기** 를 선택하여 **새 프로젝트** 창을 엽니다.

2. 왼쪽 창에서 **Azure Spark/HDInsight** 를 선택합니다.

3. 주 창에서 **Spark 프로젝트(Scala)** 를 선택합니다.

4. **빌드 도구** 드롭다운 목록에서 다음 옵션 중 하나를 선택합니다.
   * **Maven**: Scala 프로젝트 만들기 마법사 지원의 경우
   * **SBT** - 종속성 관리 및 Scala 프로젝트용 빌드의 경우

     :::image type="content" source="./media/apache-spark-intellij-tool-plugin/create-hdi-scala-app.png" alt-text="IntelliJ IDEA 새 프로젝트 대화 상자" border="true":::

5. **다음** 을 선택합니다.

6. **새 프로젝트** 창에서 다음 정보를 제공합니다.  

    |  속성   | Description   |  
    | ----- | ----- |  
    |프로젝트 이름| 이름을 입력합니다.  이 문서에서는 `myApp`을 사용합니다.|  
    |프로젝트&nbsp;위치| 프로젝트를 저장할 위치를 입력합니다.|
    |프로젝트 SDK| IDEA를 처음 사용하는 경우에는 이 필드가 비어 있습니다.  **새로 만들기...** 를 만들기 JDK로 이동합니다.|
    |Spark 버전|만들기 마법사는 Spark SDK 및 Scala SDK에 대해 적합한 버전을 통합합니다. Spark 클러스터 버전이 2.0 이전인 경우 **Spark 1.x** 를 선택합니다. 그렇지 않으면 **Spark2.x** 를 선택합니다. 이 예제에서는 **Spark 2.3.0(Scala 2.11.8)** 을 사용합니다.|

    :::image type="content" source="./media/apache-spark-intellij-tool-plugin/intellij-new-project.png" alt-text="Apache Spark SDK 선택" border="true":::

7. **마침** 을 선택합니다.  프로젝트를 사용할 수 있게 되기까지 몇 분 정도 걸릴 수 있습니다.

8. Spark 프로젝트가 자동으로 아티팩트를 만듭니다. 아티팩트를 보려면 다음 단계를 수행합니다.

   a. 메뉴 모음에서 **파일** > **프로젝트 구조...** 로 이동합니다.

   b. **프로젝트 구조** 창에서 **아티팩트** 를 선택합니다.  

   다. 아티팩트를 확인한 후 **취소** 를 선택합니다.

      :::image type="content" source="./media/apache-spark-intellij-tool-plugin/default-artifact-dialog.png" alt-text="대화 상자의 아티팩트 정보" border="true":::

9. 다음 단계를 수행하여 응용 프로그램 소스 코드를 추가합니다.

    a. 프로젝트에서 **myApp** > **src** > **main** > **scala** 로 이동합니다.  

    b. 마우스 오른쪽 단추로 **scala** 를 클릭하고 **새로 만들기** > **Scala 클래스** 로 이동합니다.

   :::image type="content" source="./media/apache-spark-intellij-tool-plugin/hdi-spark-scala-code.png" alt-text="프로젝트에서 Scala 클래스를 만들기 위한 명령" border="true":::

   다. **새 Scala 클래스 만들기** 대화 상자에서 이름을 제공하고 **종류** 드롭다운 목록에서 **개체** 를 선택한 다음, **확인** 을 선택합니다.

     :::image type="content" source="./media/apache-spark-intellij-tool-plugin/hdi-spark-scala-code-object.png" alt-text="새 Scala 클래스 만들기 대화 상자" border="true":::

   d. 그러면 **myApp.scala** 파일이 기본 보기에서 열립니다. 기본 코드를 아래 코드로 바꿉니다.  

    ```scala
    import org.apache.spark.SparkConf
    import org.apache.spark.SparkContext

    object myApp{
        def main (arg: Array[String]): Unit = {
        val conf = new SparkConf().setAppName("myApp")
        val sc = new SparkContext(conf)

        val rdd = sc.textFile("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

        //find the rows that have only one digit in the seventh column in the CSV file
        val rdd1 =  rdd.filter(s => s.split(",")(6).length() == 1)

        rdd1.saveAsTextFile("wasbs:///HVACOut")
        }

    }
    ```

    이 코드는 HVAC.csv(모든 HDInsight Spark 클러스터에서 사용 가능)에서 데이터를 읽고, CSV 파일의 일곱 번째 열에 한 자리 수만 있는 행을 검색하고, 출력을 클러스터의 기본 스토리지 컨테이너 아래의 `/HVACOut`에 씁니다.

## <a name="connect-to-your-hdinsight-cluster"></a>HDInsight 클러스터 연결

[Azure 구독에 로그인](#sign-in-to-your-azure-subscription)하거나 Ambari 사용자 이름/암호 또는 도메인 가입 자격 증명을 사용하여 [HDInsight 클러스터를 연결](#link-a-cluster)해 시작할 수 있습니다. Ambari 사용자 이름/암호 또는 도메인 가입 자격 증명을 사용하여 HDInsight 클러스터에 연결합니다.

### <a name="sign-in-to-your-azure-subscription"></a>Azure 구독에 로그인합니다.

1. 메뉴 모음에서 **보기** > **도구 창** > **Azure 탐색기** 로 이동합니다.

   :::image type="content" source="./media/apache-spark-intellij-tool-plugin/show-azure-explorer1.png" alt-text="Azure Explorer를 표시하는 IntelliJ IDEA" border="true":::

2. Azure 탐색기에서 **Azure** 노드를 마우스 오른쪽 단추로 클릭한 다음, **로그인** 을 선택합니다.

   :::image type="content" source="./media/apache-spark-intellij-tool-plugin/explorer-rightclick-azure.png" alt-text="IntelliJ IDEA Explorer에서 Azure를 마우스 오른쪽 단추로 클릭" border="true":::

3. **Azure 로그인** 대화 상자에서 **디바이스 로그인** 과 **로그인** 을 차례로 선택합니다.

    :::image type="content" source="./media/apache-spark-intellij-tool-plugin/intellij-view-explorer2.png" alt-text="`IntelliJ IDEA Azure 로그인 장치 로그인`" border="true":::

4. **Azure 디바이스 로그인** 대화 상자에서 **복사 및 열기** 를 클릭합니다.

   :::image type="content" source="./media/apache-spark-intellij-tool-plugin/intellij-view-explorer5.png" alt-text="`IntelliJ IDEA Azure 장치 로그인`" border="true":::

5. 브라우저 인터페이스에서 코드를 붙여넣고 **다음** 을 클릭합니다.

   :::image type="content" source="./media/apache-spark-intellij-tool-plugin/intellij-view-explorer6.png" alt-text="`HDI의 Microsoft 입력 코드 대화 상자`" border="true":::

6. Azure 자격 증명을 입력한 다음, 브라우저를 닫습니다.

   :::image type="content" source="./media/apache-spark-intellij-tool-plugin/intellij-view-explorer7.png" alt-text="`HDI의 Microsoft 입력 이메일 대화 상자`" border="true":::

7. 로그인하고 나면 **구독 선택** 대화 상자에 자격 증명과 연결된 모든 Azure 구독의 목록이 표시됩니다. 구독을 선택한 후 **선택** 단추를 선택합니다.

    :::image type="content" source="./media/apache-spark-intellij-tool-plugin/Select-Subscriptions.png" alt-text="구독 선택 대화 상자" border="true":::

8. **Azure 탐색기** 에서 **HDInsight** 를 확장하여 구독에 포함된 HDInsight Spark 클러스터를 표시합니다.

    :::image type="content" source="./media/apache-spark-intellij-tool-plugin/intellij-view-explorer3.png" alt-text="IntelliJ IDEA Azure Explorer 기본 보기" border="true":::

9. 클러스터와 연결된 리소스(예: 스토리지 계정)를 표시하려면 클러스터 이름 노드를 더 확장할 수 있습니다.

    :::image type="content" source="./media/apache-spark-intellij-tool-plugin/intellij-view-explorer4.png" alt-text="Azure Explorer 스토리지 계정" border="true":::

### <a name="link-a-cluster"></a>클러스터 연결

Apache Ambari 관리 사용자 이름을 사용하여 HDInsight 클러스터에 연결할 수 있습니다. 마찬가지로, 도메인에 가입된 HDInsight 클러스터의 경우, `user1@contoso.com`과 같이 도메인 및 사용자 이름을 사용하여 연결할 수 있습니다. 또한 Livy 서비스 클러스터를 연결할 수 있습니다.

1. 메뉴 모음에서 **보기** > **도구 창** > **Azure 탐색기** 로 이동합니다.

1. Azure 탐색기에서 **HDInsight** 노드를 마우스 오른쪽 단추로 클릭한 다음, **클러스터 연결** 을 선택합니다.

   :::image type="content" source="./media/apache-spark-intellij-tool-plugin/link-a-cluster-context-menu.png" alt-text="Azure Explorer에서 클러스터에 연결 상황에 맞는 메뉴" border="true":::

1. **클러스터 연결** 창에 표시되는 사용 가능한 옵션은 **링크 리소스 유형** 드롭다운 목록에서 선택한 값에 따라 다릅니다.  값을 입력하고 **확인** 을 선택합니다.

    * **HDInsight 클러스터**  
  
        |속성 |값 |
        |----|----|
        |링크 리소스 유형|드롭다운 목록에서 **HDInsight 클러스터** 를 선택합니다.|
        |클러스터 이름/URL| 클러스터 이름을 입력합니다.|
        |인증 유형| **기본 인증** 상태로 유지|
        |사용자 이름| 클러스터 사용자 이름을 입력합니다. 기본값은 admin입니다.|
        |암호| 사용자 이름에 대한 암호를 입력합니다.|

        :::image type="content" source="./media/apache-spark-intellij-tool-plugin/link-hdinsight-cluster-dialog.png" alt-text="IntelliJ IDEA에서 클러스터에 연결 대화 상자" border="true":::

    * **Livy 서비스**  
  
        |속성 |값 |
        |----|----|
        |링크 리소스 유형|드롭다운 목록에서 **Livy 서비스** 를 선택합니다.|
        |Livy 엔드포인트| Livy 엔드포인트를 입력합니다.|
        |클러스터 이름| 클러스터 이름을 입력합니다.|
        |Yarn 엔드포인트|선택 사항입니다.|
        |인증 유형| **기본 인증** 상태로 유지|
        |사용자 이름| 클러스터 사용자 이름을 입력합니다. 기본값은 admin입니다.|
        |암호| 사용자 이름에 대한 암호를 입력합니다.|

        :::image type="content" source="./media/apache-spark-intellij-tool-plugin/link-livy-cluster-dialog.png" alt-text="IntelliJ IDEA에서 Livy 클러스터에 연결 대화 상자" border="true":::

1. **HDInsight** 노드에서 연결된 클러스터를 볼 수 있습니다.

   :::image type="content" source="./media/apache-spark-intellij-tool-plugin/hdinsight-linked-cluster.png" alt-text="Azure Explorer에서 cluster1에 연결됨" border="true":::

1. **Azure Explorer** 에서 클러스터 연결을 해제할 수도 있습니다.

   :::image type="content" source="./media/apache-spark-intellij-tool-plugin/hdi-unlinked-cluster.png" alt-text="Azure Explorer에서 클러스터에 연결 해제" border="true":::

## <a name="run-a-spark-scala-application-on-an-hdinsight-spark-cluster"></a>HDInsight Spark 클러스터에서 Spark Scala 애플리케이션 실행

Scala 애플리케이션을 작성한 후 이를 클러스터에 제출할 수 있습니다.

1. 프로젝트에서 **myApp** > **src** > **main** > **scala** > **myApp** 으로 이동합니다.  **myApp** 을 마우스 오른쪽 단추로 클릭하고 **Spark 애플리케이션 제출**(일반적으로 목록 맨 아래에 표시됨)을 선택합니다.

      :::image type="content" source="./media/apache-spark-intellij-tool-plugin/hdi-submit-spark-app-1.png" alt-text="HDInsight에 Spark 애플리케이션 제출 명령" border="true":::

2. **Spark 애플리케이션 제출** 대화 상자 창에서 **1. HDInsight의 Spark** 를 선택합니다.

3. **구성 편집** 창에서 다음 값을 제공하고 **확인** 을 선택합니다.

    |속성 |값 |
    |----|----|
    |Spark 클러스터(Linux만 해당)|애플리케이션을 실행하려는 HDInsight Spark 클러스터를 선택합니다.|
    |제출할 아티팩트를 선택합니다.|기본 설정을 유지합니다.|
    |주 클래스 이름|기본값은 선택한 파일의 기본 클래스입니다. 줄임표( **...** )를 선택하고 다른 클래스를 선택하여 클래스를 변경할 수 있습니다.|
    |작업 구성|기본 키 및, 또는 값을 변경할 수 있습니다. 자세한 내용은 [Apache Livy REST API](https://livy.incubator.apache.org/docs/latest/rest-api.html)를 참조하세요.|
    |명령줄 인수|필요한 경우 기본 클래스에 대해 공백으로 구분된 인수를 입력할 수 있습니다.|
    |참조된 Jar 및 참조된 파일|참조되는 Jar 및 파일의 경로를 입력할 수 있습니다(있는 경우). Azure 가상 파일 시스템의 파일을 찾아 볼 수도 있습니다. 여기에는 현재 ADLS Gen 2 클러스터만 지원됩니다. 자세한 내용은 [Apache Spark 구성](https://spark.apache.org/docs/latest/configuration.html#runtime-environment)을 참조하세요.  [클러스터에 리소스를 업로드하는 방법](../../storage/blobs/quickstart-storage-explorer.md)을 참조하세요.|
    |작업 업로드 스토리지|추가 옵션을 표시하려면 확장합니다.|
    |스토리지 유형|드롭다운 목록에서 **Azure Blob을 사용하여 업로드** 를 선택합니다.|
    |스토리지 계정|스토리지 계정을 입력합니다.|
    |스토리지 키|스토리지 키를 입력합니다.|
    |스토리지 컨테이너|**스토리지 계정** 및 **스토리지 키** 를 입력했으면 드롭다운 목록에서 스토리지 컨테이너를 선택합니다.|

    :::image type="content" source="./media/apache-spark-intellij-tool-plugin/hdi-submit-spark-app-02.png" alt-text="Spark 제출 대화 상자" border="true":::

4. **SparkJobRun** 을 선택하여 선택된 클러스터에 프로젝트를 제출합니다. **클러스터의 원격 Spark 작업** 탭 아래쪽에는 작업 실행 진행률이 표시됩니다. 빨간색 단추를 클릭하여 애플리케이션을 중지할 수 있습니다.

    :::image type="content" source="./media/apache-spark-intellij-tool-plugin/hdi-spark-app-result.png" alt-text="Apache Spark 제출 창" border="true":::

## <a name="debug-apache-spark-applications-locally-or-remotely-on-an-hdinsight-cluster"></a>HDInsight 클러스터에서 로컬 또는 원격으로 Apache Spark 애플리케이션 디버그

Spark 애플리케이션을 클러스터에 제출하는 또 다른 권장되는 방법이 있습니다. **구성 실행/디버그** IDE에서 매개 변수를 설정하여 이 작업을 수행할 수 있습니다. [Azure Toolkit for IntelliJ를 사용하여 SSH를 통해 HDInsight 클러스터에서 로컬 또는 원격으로 Apache Spark 애플리케이션 디버그](apache-spark-intellij-tool-debug-remotely-through-ssh.md)를 참조하세요.

## <a name="access-and-manage-hdinsight-spark-clusters-by-using-azure-toolkit-for-intellij"></a>IntelliJ용 Azure 도구 키트를 사용하여 HDInsight Spark 클러스터 액세스 및 관리

Azure Toolkit for IntelliJ를 사용하여 다양한 작업을 수행할 수 있습니다.  대부분의 작업은 **Azure Explorer** 에서 시작됩니다.  메뉴 모음에서 **보기** > **도구 창** > **Azure 탐색기** 로 이동합니다.

### <a name="access-the-job-view"></a>작업 보기 액세스

1. Azure Explorer에서 **HDInsight** > \<Your Cluster> > **작업** 으로 이동합니다.

    :::image type="content" source="./media/apache-spark-intellij-tool-plugin/intellij-job-view-node.png" alt-text="IntelliJ Azure Explorer 작업 보기 노드" border="true":::

2. 오른쪽 창의 **Spark 작업 보기** 탭에는 클러스터에서 실행된 모든 애플리케이션이 표시됩니다. 자세한 내용을 보려면 원하는 애플리케이션 이름을 선택합니다.

    :::image type="content" source="./media/apache-spark-intellij-tool-plugin/intellij-view-job-logs.png" alt-text="Spark 작업 보기 애플리케이션 세부 정보" border="true":::

3. 실행 중인 기본 작업 정보를 표시하려면 작업 그래프 위로 마우스를 가져갑니다. 모든 작업이 생성하는 단계 그래프 및 정보를 보려면 작업 그래프에서 노드를 선택합니다.

    :::image type="content" source="./media/apache-spark-intellij-tool-plugin/Job-graph-stage-info.png" alt-text="Spark 작업 보기 작업 단계 세부 정보" border="true":::

4. *드라이버 Stderr*, *드라이버 Stdout* 및 *디렉터리 정보* 와 같은 자주 사용되는 로그를 보려면 **로그** 탭을 선택합니다.

    :::image type="content" source="./media/apache-spark-intellij-tool-plugin/intellij-job-log-info.png" alt-text="Spark 작업 보기 로그 세부 정보" border="true":::

5. Spark 기록 UI 및 YARN UI(응용 프로그램 수준)를 볼 수 있습니다. 다음과 같이 창 위쪽의 링크를 선택합니다.

### <a name="access-the-spark-history-server"></a>Spark 기록 서버 액세스

1. Azure 탐색기에서 **HDInsight** 를 확장하고, 마우스 오른쪽 단추로 Spark 클러스터 이름을 클릭한 다음, **Spark 기록 UI 열기** 를 선택합니다.  
2. 메시지가 나타나면 클러스터를 설정할 때 지정한 클러스터의 관리자 자격 증명을 입력합니다.

3. Spark 기록 서버 대시보드에서 애플리케이션 이름을 사용하여 방금 실행을 마친 애플리케이션을 찾을 수 있습니다. 위의 코드에서 `val conf = new SparkConf().setAppName("myApp")`을 사용하여 애플리케이션 이름을 설정합니다. Spark 애플리케이션 이름은 **myApp** 입니다.

### <a name="start-the-ambari-portal"></a>Ambari 포털 시작

1. Azure 탐색기에서 **HDInsight** 를 확장하고, 마우스 오른쪽 단추로 Spark 클러스터 이름을 클릭한 다음, **클러스터 관리 포털(Ambari) 열기** 를 선택합니다.  

2. 메시지가 표시되면 클러스터에 대한 관리자 자격 증명을 입력합니다. 클러스터 설치 프로세스 동안 이러한 자격 증명을 지정했을 것입니다.

### <a name="manage-azure-subscriptions"></a>Azure 구독 관리

기본적으로 IntelliJ용 Azure 도구 키트에는 모든 Azure 구독의 Spark 클러스터가 나열됩니다. 필요한 경우 액세스하려는 구독을 지정할 수 있습니다.  

1. Azure 탐색기에서 **Azure** 루트 노드를 마우스 오른쪽 단추로 클릭한 다음, **구독 선택** 을 선택합니다.  

2. **구독 선택** 창에서 액세스하지 않으려는 구독 옆의 확인란을 선택 취소하고 **닫기** 를 선택합니다.

## <a name="spark-console"></a>Spark 콘솔

Spark 로컬 콘솔(Scala)을 실행하거나 Spark Livy 대화형 세션 콘솔(Scala)을 실행할 수 있습니다.

### <a name="spark-local-consolescala"></a>Spark 로컬 콘솔(Scala)

WINUTILS.EXE 필수 구성 요소를 충족하는지 확인합니다.

1. 메뉴 모음에서 **실행** > **구성 편집...** 으로 이동합니다.

2. **구성 실행/디버그** 창의 왼쪽 분할 창에서 **HDInsight의 Apache Spark** > **[HDInsight의 Spark] myApp** 으로 이동합니다.

3. 주 창에서 **`Locally Run`** 탭을 선택합니다.

4. 다음 값을 입력하고 **확인** 을 선택합니다.

    |속성 |값 |
    |----|----|
    |작업 기본 클래스|기본값은 선택한 파일의 기본 클래스입니다. 줄임표( **...** )를 선택하고 다른 클래스를 선택하여 클래스를 변경할 수 있습니다.|
    |환경 변수|HADOOP_HOME의 값이 올바른지 확인합니다.|
    |WINUTILS.exe 위치|경로가 올바른지 확인합니다.|

    :::image type="content" source="./media/apache-spark-intellij-tool-plugin/console-set-configuration.png" alt-text="로컬 콘솔 구성 설정" border="true":::

5. 프로젝트에서 **myApp** > **src** > **main** > **scala** > **myApp** 으로 이동합니다.  

6. 메뉴 모음에서 **도구** > **Spark 콘솔** > **Spark 로컬 콘솔(Scala) 실행** 으로 이동합니다.

7. 그러면 종속성을 자동으로 수정할 것인지 묻는 두 개의 대화 상자가 표시될 수 있습니다. 자동으로 수정하려면 **자동 수정** 을 선택합니다.

    :::image type="content" source="./media/apache-spark-intellij-tool-plugin/intellij-console-autofix1.png" alt-text="IntelliJ IDEA Spark 자동 수정 대화 상자1" border="true":::

    :::image type="content" source="./media/apache-spark-intellij-tool-plugin/intellij-console-autofix2.png" alt-text="IntelliJ IDEA Spark 자동 수정 대화 상자2" border="true":::

8. 콘솔은 아래 그림과 같이 표시됩니다. 콘솔 창에서 `sc.appName`을 입력한 다음, Ctrl+Enter를 누릅니다.  결과가 표시됩니다. 빨간색 단추를 클릭하여 로컬 콘솔을 중지할 수 있습니다.

    :::image type="content" source="./media/apache-spark-intellij-tool-plugin/local-console-result.png" alt-text="IntelliJ IDEA 로컬 콘솔 결과" border="true":::

### <a name="spark-livy-interactive-session-consolescala"></a>Spark Livy 대화형 세션 콘솔(Scala)

1. 메뉴 모음에서 **실행** > **구성 편집...** 으로 이동합니다.

2. **구성 실행/디버그** 창의 왼쪽 분할 창에서 **HDInsight의 Apache Spark** > **[HDInsight의 Spark] myApp** 으로 이동합니다.

3. 주 창에서 **`Remotely Run in Cluster`** 탭을 선택합니다.

4. 다음 값을 입력하고 **확인** 을 선택합니다.

    |속성 |값 |
    |----|----|
    |Spark 클러스터(Linux만 해당)|애플리케이션을 실행하려는 HDInsight Spark 클러스터를 선택합니다.|
    |주 클래스 이름|기본값은 선택한 파일의 기본 클래스입니다. 줄임표( **...** )를 선택하고 다른 클래스를 선택하여 클래스를 변경할 수 있습니다.|

    :::image type="content" source="./media/apache-spark-intellij-tool-plugin/interactive-console-configuration.png" alt-text="대화형 콘솔 구성 설정" border="true":::

5. 프로젝트에서 **myApp** > **src** > **main** > **scala** > **myApp** 으로 이동합니다.  

6. 메뉴 모음에서 **도구** > **Spark 콘솔** > **Spark Livy 대화형 세션 콘솔(Scala)** 로 이동합니다.

7. 콘솔은 아래 그림과 같이 표시됩니다. 콘솔 창에서 `sc.appName`을 입력한 다음, Ctrl+Enter를 누릅니다.  결과가 표시됩니다. 빨간색 단추를 클릭하여 로컬 콘솔을 중지할 수 있습니다.

    :::image type="content" source="./media/apache-spark-intellij-tool-plugin/interactive-console-result.png" alt-text="IntelliJ IDEA 대화형 콘솔 결과" border="true":::

### <a name="send-selection-to-spark-console"></a>Spark 콘솔로 선택 내용 보내기

일부 코드를 로컬 콘솔 또는 Livy 대화식 세션 콘솔(Scala)로 보내면 편리하게 스크립트 결과를 예측할 수 있습니다. Scala 파일에서 일부 코드를 강조 표시한 다음, 마우스 오른쪽 단추로 **Spark 콘솔로 선택 내용 보내기** 를 클릭합니다. 선택한 코드가 콘솔로 전송된 후 실행됩니다. 결과는 콘솔의 코드 뒤에 표시됩니다. 콘솔에서 오류가 발생했는지 확인합니다.  

   :::image type="content" source="./media/apache-spark-intellij-tool-plugin/send-selection-to-console.png" alt-text="Spark 콘솔로 선택 내용 보내기" border="true":::

## <a name="integrate-with-hdinsight-identity-broker-hib"></a>HIB(HDInsight Identity Broker)와 통합

### <a name="connect-to-your-hdinsight-esp-cluster-with-id-broker-hib"></a>ID 브로커(HIB)를 사용하여 HDInsight ESP 클러스터에 연결

Azure 구독에 로그인하는 일반적인 단계에 따라 ID 브로커(HIB)를 사용하여 HDInsight ESP 클러스터에 연결할 수 있습니다. 로그인하면 Azure Explorer에 클러스터 목록이 표시됩니다. 자세한 지침은 [HDInsight 클러스터에 연결](#connect-to-your-hdinsight-cluster)을 참조하세요.

### <a name="run-a-spark-scala-application-on-an-hdinsight-esp-cluster-with-id-broker-hib"></a>HDInsight ESP 클러스터에서 ID 브로커(HIB)를 사용하여 Spark Scala 응용 프로그램 실행

일반적인 단계에 따라 ID 브로커(HIB)를 사용하여 HDInsight ESP 클러스터에 작업을 제출할 수 있습니다. 자세한 지침은 [HDInsight Spark 클러스터에서 Spark Scala 애플리케이션 실행](#run-a-spark-scala-application-on-an-hdinsight-spark-cluster)을 참조하세요.

필요한 파일은 사용자의 로그인 계정을 이름으로 사용하는 폴더에 업로드 되며, 구성 파일에서 업로드 경로를 확인할 수 있습니다.

   :::image type="content" source="./media/apache-spark-intellij-tool-plugin/upload-path-in-the-configuration.png" alt-text="구성의 업로드 경로" border="true":::

### <a name="spark-console-on-an-hdinsight-esp-cluster-with-id-broker-hib"></a>ID 브로커(HIB)를 사용하는 HDInsight ESP 클러스터의 Spark 콘솔

ID 브로커(HIB)를 사용하는 HDInsight ESP 클러스터에서 Spark 로컬 콘솔(Scala)을 실행하거나 Spark Livy 대화형 세션 콘솔(Scala)을 실행할 수 있습니다. 자세한 지침은 [Spark 콘솔](#spark-console)을 참조하세요.

   > [!NOTE]  
   > Id Broker(HIB)를 사용하는 HDInsight ESP 클러스터의 경우 현재 [클러스터 연결](#link-a-cluster) 및 [Apache Spark 애플리케이션의 원격 디버그](#debug-apache-spark-applications-locally-or-remotely-on-an-hdinsight-cluster)가 지원되지 않습니다.

## <a name="reader-only-role"></a>독자 전용 역할

사용자가 판독기 전용 역할 권한이 있는 클러스터에 작업을 제출하는 경우, Ambari 자격 증명이 필요합니다.

### <a name="link-cluster-from-context-menu"></a>상황에 맞는 메뉴에서 클러스터 연결

1. 독자 전용 역할 계정으로 로그인합니다.

2. **Azure 탐색기** 에서 **HDInsight** 를 확장하여 구독에 포함된 HDInsight 클러스터를 표시합니다. **"Role:Reader"** 표시가 있는 클러스터에는 판독기 전용 역할 권한만 있습니다.

    :::image type="content" source="./media/apache-spark-intellij-tool-plugin/intellij-view-explorer15.png" alt-text="`IntelliJ Azure Explorer 역할: 판독기`" border="true":::

3. 판독기 전용 역할 권한이 있는 클러스터를 마우스 오른쪽 단추로 클릭합니다. 상황에 맞는 메뉴에서 **Link this cluster**(이 클러스터 연결)를 선택하여 클러스터를 연결합니다. Ambari 사용자 이름 및 암호를 입력합니다.

    :::image type="content" source="./media/apache-spark-intellij-tool-plugin/intellij-view-explorer11.png" alt-text="IntelliJ Azure Explorer에서 이 클러스터에 연결" border="true":::

4. 클러스터가 연결되면 HDInsight가 새로 고쳐집니다.
   클러스터의 단계가 연결됨으로 변경됩니다.
  
    :::image type="content" source="./media/apache-spark-intellij-tool-plugin/intellij-view-explorer8.png" alt-text="IntelliJ Azure Explorer에서 연결 대화 상자" border="true":::

### <a name="link-cluster-by-expanding-jobs-node"></a>작업 노드를 확장하여 클러스터 연결

1. **작업** 노드를 클릭하면 **Cluster Job Access Denied**(클러스터 작업 액세스 거부됨) 창이 표시됩니다.

2. **Link this cluster**(이 클러스터 연결)를 클릭하여 클러스터를 연결합니다.

    :::image type="content" source="./media/apache-spark-intellij-tool-plugin/intellij-view-explorer9.png" alt-text="클러스터 작업 액세스 거부 대화" border="true":::

### <a name="link-cluster-from-rundebug-configurations-window"></a>실행/디버그 구성 창에서 클러스터 연결

1. HDInsight 구성을 만듭니다. 그런 다음, **Remotely Run in Cluster**(클러스터에서 원격으로 실행)를 선택합니다.

2. **Spark 클러스터(Linux 전용)** 에 대해 판독기 전용 역할 권한이 있는 클러스터를 선택합니다. 경고 메시지가 표시됩니다. **이 클러스터에 연결** 을 클릭하 여 클러스터에 연결할 수 있습니다.

   :::image type="content" source="./media/apache-spark-intellij-tool-plugin/create-configuration.png" alt-text="IntelliJ IDEA 실행/디버그 구성 만들기" border="true":::

### <a name="view-storage-accounts"></a>스토리지 계정 보기

* 판독기 전용 역할 권한이 있는 클러스터에 대해 **스토리지 계정** 노드를 클릭하면 **Storage Access Denied**(스토리지 계정 거부됨) 창이 표시됩니다. **Azure Storage Explorer 열기** 를 클릭하여 Storage Explorer를 엽니다.

   :::image type="content" source="./media/apache-spark-intellij-tool-plugin/intellij-view-explorer14.png" alt-text="`IntelliJ IDEA Storage Access Denied`" border="true":::

   :::image type="content" source="./media/apache-spark-intellij-tool-plugin/intellij-view-explorer10.png" alt-text="IntelliJ IDEA 스토리지 액세스 거부됨 단추" border="true":::

* 연결된 클러스터에 대해 **스토리지 계정** 노드를 클릭하면 **Storage Access Denied**(스토리지 계정 거부됨) 창이 표시됩니다. **Azure Storage 열기** 를 클릭하여 Storage Explorer를 엽니다.

   :::image type="content" source="./media/apache-spark-intellij-tool-plugin/intellij-view-explorer13.png" alt-text="`IntelliJ IDEA 스토리지 액세스 거부됨2`" border="true":::

   :::image type="content" source="./media/apache-spark-intellij-tool-plugin/intellij-view-explorer12.png" alt-text="IntelliJ IDEA 스토리지 액세스 거부됨2 단추" border="true":::

## <a name="convert-existing-intellij-idea-applications-to-use-azure-toolkit-for-intellij"></a>Azure Toolkit for IntelliJ를 사용하도록 기존 IntelliJ IDEA 애플리케이션 변환

IntelliJ IDEA에서 만든 기존 Spark Scala 애플리케이션을 Azure Toolkit for IntelliJ와 호환되도록 변환할 수 있습니다. 이렇게 하면 플러그 인을 사용하여 HDInsight Spark 클러스터에 애플리케이션을 제출할 수 있습니다.

1. IntelliJ IDEA를 통해 만들어진 기존 Spark Scala 응용 프로그램의 경우 관련된 `.iml` 파일을 엽니다.

2. 루트 수준에서는 다음 텍스트와 같은 **모듈** 요소입니다.

    ```xml
    <module org.jetbrains.idea.maven.project.MavenProjectsManager.isMavenModule="true" type="JAVA_MODULE" version="4">
    ```

   요소를 편집하여 `UniqueKey="HDInsightTool"` 를 추가해 **모듈** 요소가 다음 텍스트와 같이 표시되도록 합니다.

    ```xml
    <module org.jetbrains.idea.maven.project.MavenProjectsManager.isMavenModule="true" type="JAVA_MODULE" version="4" UniqueKey="HDInsightTool">
    ```

3. 변경 내용을 저장합니다. 이제 애플리케이션이 Azure Toolkit for IntelliJ와 호환됩니다. 프로젝트에서 프로젝트 이름을 마우스 오른쪽 단추로 클릭하여 이 테스트를 수행할 수 있습니다. 이제 팝업 메뉴에 **HDInsight에 Spark 애플리케이션 제출** 옵션이 표시됩니다.

## <a name="clean-up-resources"></a>리소스 정리

이 애플리케이션을 계속 사용할 계획이 없으면 다음 단계에 따라 생성된 클러스터를 삭제합니다.

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.

1. 맨 위에 있는 **검색** 상자에 **HDInsight** 를 입력합니다.

1. **서비스** 에서 **HDInsight 클러스터** 를 선택합니다.

1. 표시되는 HDInsight 클러스터 목록에서 이 자습서용으로 만든 클러스터 옆에 있는 **...** 를 선택합니다.

1. **삭제** 를 선택합니다. **예** 를 선택합니다.

:::image type="content" source="./media/apache-spark-intellij-tool-plugin/hdinsight-azure-portal-delete-cluster.png " alt-text="HDInsight 클러스터를 삭제하는 Azure Portal" border="true":::

## <a name="next-steps"></a>다음 단계

이 문서에서는 Azure Toolkit for IntelliJ 플러그 인을 사용하여 [Scala](https://www.scala-lang.org/)로 작성된 Apache Spark 애플리케이션을 개발하는 방법을 배웠습니다. 그런 다음 IntelliJ IDE(통합 개발 환경)에서 직접 HDInsight Spark 클러스터에 제출합니다. 다음 문서로 진행하여 Apache Spark에 등록된 데이터를 Power BI와 같은 BI 분석 도구로 가져오는 방법을 확인하세요.

> [!div class="nextstepaction"]
> [Power BI를 사용하여 Apache Spark 데이터 분석](apache-spark-use-bi-tools.md)
