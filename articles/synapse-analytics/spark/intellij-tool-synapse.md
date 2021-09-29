---
title: 자습서 - Azure Toolkit for IntelliJ(Spark 애플리케이션)
description: 자습서 - Azure Toolkit for IntelliJ를 사용하여 Scala로 작성되는 Spark 애플리케이션을 개발하고 서버리스 Apache Spark 풀에 제출합니다.
services: synapse-analytics
author: jejiang
ms.author: jejiang
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.topic: tutorial
ms.subservice: spark
ms.date: 04/15/2020
ms.openlocfilehash: d237a1ff5ff2fa55a5a22fbc3b57c93849bdb87d
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124818825"
---
# <a name="tutorial-create-an-apache-spark-application-with-intellij-using-a-synapse-workspace"></a>자습서: Synapse 작업 영역을 통해 IntelliJ를 사용하여 Apache Spark 애플리케이션 만들기

이 자습서에서는 Azure Toolkit for IntelliJ 플러그 인을 사용하여 [Scala](https://www.scala-lang.org/)로 작성되는 Apache Spark 애플리케이션을 개발한 다음, IntelliJ IDE(통합 개발 환경)에서 직접 서버리스 Apache Spark 풀에 제출하는 방법을 보여줍니다. 다음과 같은 몇 가지 방식으로 플러그 인을 사용할 수 있습니다.

- Spark 풀에서 Scala Spark 애플리케이션을 개발하여 제출합니다.
- Spark 풀 리소스에 액세스합니다.
- Scala Spark 애플리케이션을 로컬로 개발 및 실행합니다.

이 자습서에서는 다음 작업 방법을 알아봅니다.
> [!div class="checklist"]
>
> - Azure Toolkit for IntelliJ 플러그 인 사용
> - Apache Spark 애플리케이션 개발
> - Spark 풀에 애플리케이션 제출

## <a name="prerequisites"></a>필수 구성 요소

- [IntelliJ IDEA Community 버전](https://www.jetbrains.com/idea/download/download-thanks.html?platform=windows&code=IIC).
- Azure 도구 키트 플러그 인 3.27.0-2019.2 – [IntelliJ 플러그 인 리포지토리](/java/azure/intellij/azure-toolkit-for-intellij-installation)에서 설치
- [JDK(버전 1.8)](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)
- Scala 플러그 인 – [IntelliJ 플러그 인 리포지토리에서 설치](../../hdinsight/spark/apache-spark-intellij-tool-plugin.md#install-scala-plugin-for-intellij-idea)
- 다음 사전 요구 사항은 Windows 사용자에게만 적용됩니다.

  Windows 컴퓨터에서 로컬 Spark Scala 애플리케이션을 실행하는 동안 [SPARK-2356](https://issues.apache.org/jira/browse/SPARK-2356)에서 설명한 예외가 발생할 수 있습니다. 이 예외는 Windows에 WinUtils.exe가 없기 때문에 발생합니다.
  이 오류를 해결하려면 [WinUtils 실행 파일](https://github.com/steveloughran/winutils/releases/download/tag_2017-08-29-hadoop-2.8.1-native/hadoop-2.8.1.zip)을 **C:\WinUtils\bin** 같은 위치에 저장합니다. 그런 다음 **HADOOP_HOME** 환경 변수를 추가하고 이 변수 값을 **C:\WinUtils** 로 설정합니다.

## <a name="create-a-spark-scala-application-for-a-spark-pool"></a>Spark 풀용 Spark Scala 애플리케이션 만들기

1. IntelliJ IDEA를 시작하고 **새 프로젝트 만들기** 를 선택하여 **새 프로젝트** 창을 엽니다.
2. 왼쪽 창에서 **Apache Spark/HDInsight** 를 선택합니다.
3. 주 창에서 **샘플이 포함된 Spark 프로젝트(Scala)** 를 선택합니다.
4. **빌드 도구** 드롭다운 목록에서 다음 형식 중 하나를 선택합니다.

   - **Maven**: Scala 프로젝트 만들기 마법사 지원의 경우
   - **SBT** - 종속성 관리 및 Scala 프로젝트용 빌드의 경우

    ![IntelliJ IDEA 새 프로젝트 대화 상자](./media/intellij-tool-synapse/create-synapse-application01.png)

5. **다음** 을 선택합니다.
6. **새 프로젝트** 창에서 다음 정보를 제공합니다.

    | 속성 | Description |
    | ----- | ----- |
    |프로젝트 이름| 이름을 입력합니다. 이 자습서에서는 `myApp`를 사용합니다.|
    |프로젝트&nbsp;위치| 프로젝트를 저장할 위치를 입력합니다.|
    |프로젝트 SDK| IDEA를 처음 사용하는 경우에는 비어 있을 수 있습니다. **새로 만들기...** 를 만들기 JDK로 이동합니다.|
    |Spark 버전|만들기 마법사는 Spark SDK 및 Scala SDK에 대해 적합한 버전을 통합합니다. Synapse는 **Spark 2.4.0** 만 지원합니다.|
    |||

    ![Apache Spark SDK 선택](./media/intellij-tool-synapse/create-synapse-application02.png)

7. **마침** 을 선택합니다. 프로젝트를 사용할 수 있게 되기까지 몇 분 정도 걸릴 수 있습니다.
8. Spark 프로젝트가 자동으로 아티팩트를 만듭니다. 아티팩트를 보려면 다음 작업을 수행합니다.

   a. 메뉴 모음에서 **파일** > **프로젝트 구조...** 로 이동합니다.

   b. **프로젝트 구조** 창에서 **아티팩트** 를 선택합니다.

   다. 아티팩트를 확인한 후 **취소** 를 선택합니다.

    ![대화 상자의 아티팩트 정보](./media/intellij-tool-synapse/default-artifact-dialog.png)

9. **myApp** > **src** > **main** > **scala**> **sample**> **LogQuery** 에서 **LogQuery** 를 찾습니다. 이 자습서에서는 **LogQuery** 를 사용하여 실행합니다.

   ![프로젝트에서 Scala 클래스를 만들기 위한 명령](./media/intellij-tool-synapse/select-run-file.png)

## <a name="connect-to-your-spark-pools"></a>Spark 풀에 연결

Azure 구독에 로그인하여 Spark 풀에 연결합니다.

### <a name="sign-in-to-your-azure-subscription"></a>Azure 구독에 로그인합니다.

1. 메뉴 모음에서 **보기** > **도구 창** > **Azure 탐색기** 로 이동합니다.

   ![Azure 탐색기를 표시하는 IntelliJ IDEA](./media/intellij-tool-synapse/show-azure-explorer1.png)

2. Azure 탐색기에서 **Azure** 노드를 마우스 오른쪽 단추로 클릭한 다음, **로그인** 을 선택합니다.

   ![IntelliJ IDEA 탐색기에서 Azure를 마우스 오른쪽 단추로 클릭](./media/intellij-tool-synapse/explorer-rightclick-azure.png)

3. **Azure 로그인** 대화 상자에서 **디바이스 로그인** 과 **로그인** 을 차례로 선택합니다.

    ![IntelliJ IDEA Azure 로그인](./media/intellij-tool-synapse/intellij-view-explorer2.png)

4. **Azure 디바이스 로그인** 대화 상자에서 **복사 및 열기** 를 선택합니다.

   ![IntelliJ IDEA Azure 디바이스 로그인](./media/intellij-tool-synapse/intellij-view-explorer5.png)

5. 브라우저 인터페이스에서 코드를 붙여넣은 다음, **다음** 을 선택합니다.

   ![HDI의 Microsoft 코드 입력 대화 상자](./media/intellij-tool-synapse/intellij-view-explorer6.png)

6. Azure 자격 증명을 입력한 다음, 브라우저를 닫습니다.

   ![HDI의 Microsoft 이메일 입력 대화 상자](./media/intellij-tool-synapse/intellij-view-explorer7.png)

7. 로그인하고 나면 **구독 선택** 대화 상자에 자격 증명과 연결된 모든 Azure 구독의 목록이 표시됩니다. 구독을 선택한 다음, **선택** 을 선택합니다.

    ![구독 선택 대화 상자](./media/intellij-tool-synapse/Select-Subscriptions.png)

8. **Azure 탐색기** 에서 **Synapse의 Apache Spark** 를 확장하여 구독에 포함된 작업 영역을 확인합니다.

    ![IntelliJ IDEA Azure Explorer 기본 보기](./media/intellij-tool-synapse/azure-explorer-workspace.png)

9. Spark 풀을 보려면 작업 영역을 더 확장해야 합니다.

    ![Azure Explorer 스토리지 계정](./media/intellij-tool-synapse/azure-explorer-pool.png)

## <a name="remote-run-a-spark-scala-application-on-a-spark-pool"></a>Spark 풀에서 Spark Scala 애플리케이션 원격 실행

Scala 애플리케이션을 만든 후에는 원격으로 실행할 수 있습니다.

1. 아이콘을 선택하여 **구성 실행/디버그** 창을 엽니다.

    ![HDInsight에 Spark 애플리케이션 제출 명령 1](./media/intellij-tool-synapse/open-configuration-window.png)

2. **구성 실행/디버그** 대화 상자 창에서 **+** 기호를 선택한 다음, **Synapse의 Apache Spark** 를 선택합니다.

    ![HDInsight에 Spark 애플리케이션 제출 명령 2](./media/intellij-tool-synapse/create-synapse-configuration02.png)

3. **구성 실행/디버그** 창에서 다음 값을 입력한 다음, **확인** 을 선택합니다.

    |속성 |값 |
    |----|----|
    |Spark 풀|애플리케이션을 실행하려는 Spark 풀을 선택합니다.|
    |제출할 아티팩트를 선택합니다.|기본 설정을 유지합니다.|
    |주 클래스 이름|기본값은 선택한 파일의 기본 클래스입니다. 줄임표( **...** )를 선택하고 다른 클래스를 선택하여 클래스를 변경할 수 있습니다.|
    |작업 구성|기본 키 및 값을 변경할 수 있습니다. 자세한 내용은 [Apache Livy REST API](http://livy.incubator.apache.org./docs/latest/rest-api.html)를 참조하세요.|
    |명령줄 인수|필요한 경우 기본 클래스에 대해 공백으로 구분된 인수를 입력할 수 있습니다.|
    |참조된 Jar 및 참조된 파일|참조되는 Jar 및 파일의 경로를 입력할 수 있습니다(있는 경우). Azure 가상 파일 시스템의 파일을 찾아 볼 수도 있습니다. 여기에는 현재 ADLS Gen2 클러스터만 지원됩니다. 자세한 내용은 다음을 참조하세요. [Apache Spark 구성](https://spark.apache.org/docs/2.4.5/configuration.html#runtime-environment) 및 [클러스터에 리소스를 업로드하는 방법](../../storage/blobs/quickstart-storage-explorer.md).|
    |작업 업로드 스토리지|추가 옵션을 표시하려면 확장합니다.|
    |스토리지 유형|드롭다운 목록에서 **Azure Blob을 사용하여 업로드** 또는 **클러스터 기본 스토리지 계정을 사용하여 업로드** 를 선택합니다.|
    |스토리지 계정|스토리지 계정을 입력합니다.|
    |스토리지 키|스토리지 키를 입력합니다.|
    |스토리지 컨테이너|**스토리지 계정** 및 **스토리지 키** 를 입력했으면 드롭다운 목록에서 스토리지 컨테이너를 선택합니다.|

    ![Spark 제출 대화 상자 1](./media/intellij-tool-synapse/create-synapse-configuration03.png)

4. **SparkJobRun** 아이콘을 선택하여 선택한 Spark 풀에 프로젝트를 제출합니다. **클러스터의 원격 Spark 작업** 탭 아래쪽에는 작업 실행 진행률이 표시됩니다. 빨간색 단추를 선택하여 애플리케이션을 중지할 수 있습니다.

    ![Apache Spark 제출 창](./media/intellij-tool-synapse/remotely-run-synapse.png)

    ![Spark 제출 대화 상자 2](./media/intellij-tool-synapse/remotely-run-result.png)

## <a name="local-rundebug-apache-spark-applications"></a>로컬로 Apache Spark 애플리케이션 실행/디버그

아래 지침에 따라 Apache Spark 작업의 로컬 실행 및 로컬 디버그를 설정할 수 있습니다.

### <a name="scenario-1-do-local-run"></a>시나리오 1: 로컬 실행

1. **구성 실행/디버그** 대화 상자에서 더하기 기호( **+** )를 선택합니다. **Synapse의 Apache Spark** 옵션을 선택합니다. 저장할 **이름**, **주 클래스 이름** 을 입력합니다.

    ![Intellij 구성 실행 디버그 로컬 실행 1](./media/intellij-tool-synapse/local-run-synapse.png)

    - 환경 변수 및 WinUtils.exe 위치는 windows 사용자에게만 적용됩니다.
    - 환경 변수: 이전에 시스템 환경 변수를 설정했고 수동으로 추가할 필요가 없는 경우 자동으로 시스템 환경 변수를 검색할 수 있습니다.
    - [WinUtils.exe 위치](http://public-repo-1.hortonworks.com/hdp-win-alpha/winutils.exe): 오른쪽에 있는 폴더 아이콘을 선택하여 WinUtils 위치를 지정할 수 있습니다.

2. 그런 다음, 로컬 재생 단추를 선택합니다.

    ![Intellij 구성 실행 디버그 로컬 실행 2](./media/intellij-tool-synapse/local-run-synapse01.png)

3. 로컬 실행이 완료되고 스크립트에 출력이 포함되어 있으면 **data** > **__default__** 에서 출력 파일을 확인할 수 있습니다.

    ![Intellij 프로젝트 로컬 실행 결과 1](./media/intellij-tool-synapse/spark-local-run-result.png)

### <a name="scenario-2-do-local-debugging"></a>시나리오 2: 로컬 디버깅 수행

1. **LogQuery** 스크립트를 열고 중단점을 설정합니다.
2. **로컬 디버그** 아이콘을 선택하여 로컬 디버깅을 수행합니다.

    ![Intellij 프로젝트 로컬 실행 결과 2](./media/intellij-tool-synapse/local-debug-synapse.png)

## <a name="access-and-manage-synapse-workspace"></a>Synapse 작업 영역 액세스 및 관리

Azure Toolkit for IntelliJ 내에서 Azure 탐색기를 사용하여 여러 가지 작업을 수행할 수 있습니다. 메뉴 모음에서 **보기** > **도구 창** > **Azure 탐색기** 로 이동합니다.

### <a name="launch-workspace"></a>작업 영역 시작

1. Azure 탐색기에서 **Synapse의 Apache Spark** 로 이동하여 확장합니다.

    ![IntelliJ IDEA Azure Explorer 기본 보기](./media/intellij-tool-synapse/azure-explorer-workspace.png)

2. 작업 영역을 마우스 오른쪽 단추로 클릭한 다음, **작업 영역 시작** 을 선택하면 웹 사이트가 열립니다.

    ![Spark 작업 보기 애플리케이션 세부 정보 1](./media/intellij-tool-synapse/launch-workspace-synapse.png)

    ![Spark 작업 보기 애플리케이션 세부 정보 2](./media/intellij-tool-synapse/launch-workspace-result.png)

## <a name="spark-console"></a>Spark 콘솔

Spark 로컬 콘솔(Scala)을 실행하거나 Spark Livy 대화형 세션 콘솔(Scala)을 실행할 수 있습니다.

### <a name="spark-local-console-scala"></a>Spark 로컬 콘솔(Scala)

WINUTILS.EXE 필수 구성 요소를 충족하는지 확인합니다.

1. 메뉴 모음에서 **실행** > **구성 편집...** 으로 이동합니다.
2. **구성 실행/디버그** 창의 왼쪽 창에서 **Synapse의 Apache Spark** >  **[Synapse의 Spark] myApp** 으로 이동합니다.
3. 주 창에서 **로컬 실행** 탭을 선택합니다.
4. 다음 값을 입력하고 **확인** 을 선택합니다.

    |속성 |값 |
    |----|----|
    |환경 변수|HADOOP_HOME의 값이 올바른지 확인합니다.|
    |WINUTILS.exe 위치|경로가 올바른지 확인합니다.|

    ![로컬 콘솔 구성 설정](./media/intellij-tool-synapse/local-console-synapse01.png)

5. 프로젝트에서 **myApp** > **src** > **main** > **scala** > **myApp** 으로 이동합니다.
6. 메뉴 모음에서 **도구** > **Spark 콘솔** > **Spark 로컬 콘솔(Scala) 실행** 으로 이동합니다.
7. 그러면 종속성을 자동으로 수정할 것인지 묻는 두 개의 대화 상자가 표시될 수 있습니다. 자동으로 수정하려면 **자동 수정** 을 선택합니다.

    ![IntelliJ IDEA Spark 자동 수정 대화 상자1](./media/intellij-tool-synapse/intellij-console-autofix1.png)

    ![IntelliJ IDEA Spark 자동 수정 대화 상자2](./media/intellij-tool-synapse/intellij-console-autofix2.png)

8. 콘솔은 아래 그림과 같이 표시됩니다. 콘솔 창에서 `sc.appName`을 입력한 다음, Ctrl+Enter를 누릅니다. 결과가 표시됩니다. 빨간색 단추를 선택하여 로컬 콘솔을 중지할 수 있습니다.

    ![IntelliJ IDEA 로컬 콘솔 결과](./media/intellij-tool-synapse/local-console-result.png)

### <a name="spark-livy-interactive-session-console-scala"></a>Spark Livy 대화형 세션 콘솔(Scala)

IntelliJ 2018.2 및 2018.3에서만 지원됩니다.

1. 메뉴 모음에서 **실행** > **구성 편집...** 으로 이동합니다.

2. **구성 실행/디버그** 창의 왼쪽 창에서 **Synapse의 Apache Spark** >  **[Synapse의 Spark] myApp** 으로 이동합니다.

3. 주 창에서 **클러스터에서 원격으로 실행** 탭을 선택합니다.

4. 다음 값을 입력하고 **확인** 을 선택합니다.

    |속성 |값 |
    |----|----|
    |주 클래스 이름| 주 클래스 이름을 선택합니다.| 
    |Spark 풀|애플리케이션을 실행하려는 Spark 풀을 선택합니다.|
    ||

    ![대화형 콘솔 구성 설정](./media/intellij-tool-synapse/interactive-console-configuration.png)

5. 프로젝트에서 **myApp** > **src** > **main** > **scala** > **myApp** 으로 이동합니다.

6. 메뉴 모음에서 **도구** > **Spark 콘솔** > **Spark Livy 대화형 세션 콘솔(Scala)** 로 이동합니다.
7. 콘솔은 아래 그림과 같이 표시됩니다. 콘솔 창에서 `sc.appName`을 입력한 다음, Ctrl+Enter를 누릅니다. 결과가 표시됩니다. 빨간색 단추를 선택하여 로컬 콘솔을 중지할 수 있습니다.

    ![IntelliJ IDEA 대화형 콘솔 결과](./media/intellij-tool-synapse/interactive-console-result.png)

### <a name="send-selection-to-spark-console"></a>Spark 콘솔로 선택 내용 보내기

로컬 콘솔 또는 Livy Interactive Session Console(Scala)에 코드를 전송하여 스크립트 결과를 볼 수 있습니다. 이렇게 하려면 Scala 파일에서 일부 코드를 강조 표시한 다음, 마우스 오른쪽 단추로 **Spark 콘솔로 선택 내용 보내기** 를 클릭합니다. 선택한 코드가 콘솔로 전송된 후 실행됩니다. 결과는 콘솔의 코드 뒤에 표시됩니다. 콘솔에서 기존 오류를 확인합니다.

   ![Spark 콘솔로 선택 내용 보내기](./media/intellij-tool-synapse/send-selection-to-console.png)

## <a name="next-steps"></a>다음 단계

- [Azure Synapse Analytics](../overview-what-is.md)
- [Azure Synapse Analytics 작업 영역에 사용할 새 Apache Spark 풀 만들기](../../synapse-analytics/quickstart-create-apache-spark-pool-studio.md)