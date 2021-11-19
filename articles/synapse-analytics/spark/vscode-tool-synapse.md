---
title: 자습서 - VSCode용 Spark & Hive Tools(Spark 애플리케이션)
description: 자습서 - VSCode용 Spark & Hive Tools를 사용하여 Python으로 작성된 Spark 애플리케이션을 개발하여 서버리스 Apache Spark 풀에 제출합니다.
services: synapse-analytics
author: jejiang
ms.author: jejiang
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.topic: tutorial
ms.subservice: spark
ms.date: 09/03/2020
ms.openlocfilehash: 07168ec69046973d4e02c2dc40b3b5e256ea26b4
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/15/2021
ms.locfileid: "132492116"
---
# <a name="tutorial-create-an-apache-spark-applications-with-vscode-using-a-synapse-workspace"></a>자습서: Synapse 작업 영역을 사용하여 VSCode로 Apache Spark 애플리케이션 만들기

Visual Studio Code용 Apache Spark & Hive Tools를 사용하는 방법을 알아봅니다. 도구를 사용하여 Apache Hive 일괄 작업, 대화형 Hive 쿼리 및 Apache Spark용 PySpark 스크립트를 만들고 제출합니다. 먼저 Visual Studio Code에서 Spark & Hive Tools를 설치하는 방법을 설명합니다. 그런 다음 Spark & Hive Tools에 작업을 제출하는 방법을 살펴보겠습니다.

Spark & Hive Tools는 Visual Studio Code가 지원하는 플랫폼에 설치할 수 있습니다. 다른 플랫폼의 사전 요구 사항은 다음과 같습니다.

## <a name="prerequisites"></a>사전 요구 사항

이 문서의 단계를 완료하려면 다음 항목이 필요합니다.

- 서버리스 Apache Spark 풀. 서버리스 Apache Spark 풀을 만들려면 [Azure Portal을 사용하여 Apache Spark 풀 만들기](../../synapse-analytics/quickstart-create-apache-spark-pool-portal.md)를 참조하세요.
- [Visual Studio Code](https://code.visualstudio.com/)
- [Mono](https://www.mono-project.com/docs/getting-started/install/). Mono는 Linux 및 macOS에만 필요합니다.
- [Visual Studio Code용 PySpark 대화형 환경](../../hdinsight/set-up-pyspark-interactive-environment.md)
- 로컬 디렉터리. 이 문서에서는 **C:\HD\Synaseexample** 을 사용합니다.

## <a name="install-spark--hive-tools"></a>Spark & Hive Tools 설치

사전 요구 사항을 충족하면 다음 단계에 따라 Visual Studio Code용 Spark & Hive Tools를 설치할 수 있습니다.

1. Visual Studio Code를 엽니다.

2. 메뉴 모음에서 **보기** > **확장** 으로 이동합니다.

3. 검색 상자에 **Spark & Hive** 를 입력합니다.

4. 검색 결과에서 **Spark & Hive Tools** 를 선택하고 **설치** 를 선택합니다.

     ![Visual Studio Code Python용 Spark & Hive 설치](./media/vscode-tool-synapse/install-hdInsight-plugin.png)

5. 필요한 경우 **다시 로드** 를 선택합니다.

## <a name="open-a-work-folder"></a>작업 폴더 열기

작업 폴더를 열고 Visual Studio Code에서 파일을 만들려면 다음 단계를 수행합니다.

1. 메뉴 모음에서 **파일** > **폴더 열기...**  > **C:\HD\Synaseexample** 로 이동한 다음, **폴더 선택** 단추를 선택합니다. 왼쪽 **탐색기** 뷰에 폴더가 표시됩니다.

2. **탐색기** 보기에서 **Synaseexample** 폴더를 선택한 다음, 작업 폴더 옆에 있는 **새 파일** 아이콘을 선택합니다.

     ![Visual Studio Code 새 파일 아이콘](./media/vscode-tool-synapse/visual-studio-code-new-file.png)

3. `.py`(Spark 스크립트) 파일 확장명을 사용하여 새 파일의 이름을 지정합니다. 이 예제에서는 **HelloWorld.py** 를 사용합니다.

## <a name="connect-to-your-spark-pools"></a>Spark 풀에 연결

Azure 구독에 로그인하여 Spark 풀에 연결합니다.

### <a name="sign-in-to-your-azure-subscription"></a>Azure 구독에 로그인합니다.

다음 단계에 따라 Azure에 연결합니다.

1. 메뉴 모음에서 **보기** > **명령 팔레트...** 로 이동한 다음, **Azure: Sign In** 을 입력합니다.

     ![Visual Studio Code용 Spark & Hive Tools 로그인](./media/vscode-tool-synapse/hdinsight-for-vscode-extension-login.png)

2. 로그인 지침에 따라 Azure에 로그인합니다. 연결되면 Visual Studio Code 창 아래쪽의 상태 표시줄에 Azure 계정 이름이 표시됩니다.

## <a name="set-the-default-spark-pool"></a>기본 Spark 풀 설정

1. [이전에](#open-a-work-folder) 설명한 **Synaseexample** 폴더를 닫은 경우 다시 엽니다.  

2. [이전에](#open-a-work-folder) 만든 **HelloWorld.py** 파일을 선택합니다. 스크립트 편집기에서 열립니다.

3. 스크립트 편집기를 마우스 오른쪽 단추로 클릭한 후 **Synapse: 기본 Spark 풀 설정** 을 선택합니다.  

4. Azure 계정에 아직 연결하지 않은 경우 [연결](#connect-to-your-spark-pools)합니다.

5. 현재 스크립트 파일의 기본 Spark 풀로 Spark 풀을 선택합니다. 

6. **Synapse: PySpark Interactive** 를 사용하여 이 파일을 제출합니다. 또한 이 도구는 **.VSCode\settings.json** 구성 파일을 자동으로 업데이트합니다.

     ![기본 클러스터 구성 설정](./media/vscode-tool-synapse/set-default-cluster-configuration.png)

## <a name="submit-interactive-synapse-pyspark-queries-to-spark-pool"></a>Spark 풀에 대화형 Synapse PySpark 쿼리 제출

사용자가 Spark 풀에서 Synapse PySpark 대화형 작업을 수행할 수 있는 방법은 다음과 같습니다.

### <a name="using-the-synapse-pyspark-interactive-command-in-py-file"></a>PY 파일에서 Synapse PySpark 대화형 명령 사용
PySpark 대화형 명령을 사용하여 쿼리를 제출하려면 다음 단계를 수행합니다.

1. [이전에](#open-a-work-folder) 설명한 **Synaseexample** 폴더를 닫은 경우 다시 엽니다.  

2. [이전](#open-a-work-folder) 단계에 따라 새 **HelloWorld.py** 파일을 만듭니다.

3. 다음 코드를 복사하여 스크립트 파일에 붙여넣습니다.

```python
import sys
from operator import add
from pyspark.sql import SparkSession, Row
 
spark = SparkSession\
 .builder\
 .appName("PythonWordCount")\
 .getOrCreate()
 
data = [Row(col1='pyspark and spark', col2=1), Row(col1='pyspark', col2=2), Row(col1='spark vs hadoop', col2=2), Row(col1='spark', col2=2), Row(col1='hadoop', col2=2)]
df = spark.createDataFrame(data)
lines = df.rdd.map(lambda r: r[0])
 
counters = lines.flatMap(lambda x: x.split(' ')) \
 .map(lambda x: (x, 1)) \
 .reduceByKey(add)
 
output = counters.collect()
sortedCollection = sorted(output, key = lambda r: r[1], reverse = True)
 
for (word, count) in sortedCollection:
 print("%s: %i" % (word, count))
```

4. PySpark/Synapse Pyspark 커널을 설치하라는 메시지가 창의 오른쪽 아래 모서리에 표시됩니다. **설치** 단추를 클릭하여 PySpark/Synapse Pyspark 설치를 진행하거나 **건너뛰기** 단추를 클릭하여 이 단계를 건너뜁니다.

     ![pyspark 커널 설치](./media/vscode-tool-synapse/install-the-pyspark-kernel.png)

5. 나중에 설치해야 하는 경우 **파일** > **기본 설정** > **설정** 으로 이동한 다음, 설정에서 **Hdinsight: Pyspark 설치 건너뛰기 사용** 을 선택 취소합니다. 
    
     ![pyspark 설치 건너뛰기 사용](./media/vscode-tool-synapse/enable-skip-pyspark-installation.png)

6. 4단계에서 설치에 성공하면 창 오른쪽 아래 모서리에 "PySpark/Synapse Pyspark가 설치되었습니다"라는 메시지 상자가 표시됩니다. **다시 로드** 단추를 클릭하여 창을 다시 로드합니다.

     ![pyspark 설치 성공](./media/vscode-tool-synapse/pyspark-kernel-installed-successfully.png)

7. 메뉴 모음에서 **보기** > **명령 팔레트...** 로 이동하거나 **Shift + Ctrl + P** 바로 가기 키를 사용하여 **Python: Select Interpreter to start Jupyter Server** 를 입력합니다.

     ![jupyter 서버를 시작할 인터프리터 선택](./media/vscode-tool-synapse/select-interpreter-to-start-jupyter-server.png)

8. 아래의 python 옵션을 선택합니다.

     ![아래 옵션을 선택합니다.](./media/vscode-tool-synapse/choose-the-below-option.png)
    
9. 메뉴 모음에서 **보기** > **명령 팔레트...** 로 이동하거나 **Shift + Ctrl + P** 바로 가기 키를 사용하여 **Developer: Reload Window** 를 입력합니다.

     ![창 다시 로드](./media/vscode-tool-synapse/reload-window.png)

10. Azure 계정에 아직 연결하지 않은 경우 [연결](#connect-to-your-spark-pools)합니다.

11. 모든 코드를 선택하고 스크립트 편집기를 마우스 오른쪽 단추로 클릭한 다음, **Synapse: Pyspark Interactive** 를 선택하여 쿼리를 제출합니다. 

     ![pyspark 대화형 상황에 맞는 메뉴](./media/vscode-tool-synapse/pyspark-interactive-right-click.png)

12. 기본 Spark 풀을 지정하지 않은 경우 Spark 풀을 선택합니다. 몇 분 후에 **Python 대화형** 결과가 새 탭에 나타납니다. PySpark를 클릭하여 커널을 **Synapse PySpark** 로 전환한 다음, 선택한 코드를 다시 제출하면 코드가 성공적으로 실행됩니다. 도구를 사용하면 바로 가기 메뉴를 사용하여 전체 스크립트 파일 대신 코드 블록을 제출할 수도 있습니다.

     ![대화형](./media/vscode-tool-synapse/pyspark-interactive-python-interactive-window.png)

### <a name="perform-interactive-query-in-py-file-using-a--comment"></a>#%% 주석을 사용하여 PY 파일에서 대화형 쿼리 수행

1. 코드 앞에 **#%%** 를 추가하여 Notebook 환경을 얻으십시오.

     ![#%% 추가](./media/vscode-tool-synapse/run-cell.png)

2. **셀 실행** 을 클릭합니다. 몇 분 후에 Python 대화형 결과가 새 탭에 나타납니다. PySpark를 클릭하여 커널을 **Synapse PySpark** 로 전환한 다음, **셀 실행** 을 다시 클릭하면 코드가 성공적으로 실행됩니다. 

     ![셀 실행 결과](./media/vscode-tool-synapse/run-cell-get-results.png)

## <a name="leverage-ipynb-support-from-python-extension"></a>Python 확장의 IPYNB 지원 활용

1. 명령 팔레트의 명령을 사용하거나 작업 영역에서 새 .ipynb 파일을 생성하여 Jupyter Notebook을 만들 수 있습니다. 자세한 내용은 [Visual Studio Code에서 Jupyter Notebook 작업](https://code.visualstudio.com/docs/python/jupyter-support)을 참조하세요.

2. **셀 실행** 단추를 클릭하고 프롬프트에 따라 **기본 Spark 풀을 설정** 한(Notebook을 열기 전에 매번 기본 클러스터/풀을 설정하는 것이 좋음) 다음, 창을 **다시 로드** 합니다.

     ![기본 Spark 풀을 설정하고 다시 로드](./media/vscode-tool-synapse/set-the-default-spark-pool-and-reload.png)

3. PySpark를 클릭하여 커널을 **Synapse Pyspark** 로 전환한 다음, **셀 실행** 을 클릭하면 잠시 후 결과가 표시됩니다.

     ![ipynb 실행 결과](./media/vscode-tool-synapse/run-ipynb-file-results.png)


> [!NOTE]
>
>1. Ms-python >=2020.5.78807 버전이 이 확장에서 지원되지 않는 것은 [알려진 문제](#known-issues)입니다.
>  
>2. Synapse Pyspark 커널로 전환하고 Azure Portal에서 자동 설정을 사용하지 않도록 설정하는 것이 좋습니다. 그렇지 않으면 클러스터의 절전 모드를 해제하고 처음 사용할 때 synapse 커널을 설정하는 데 시간이 오래 걸릴 수 있습니다. 
>
>    ![자동 설정](./media/vscode-tool-synapse/auto-settings.png)

## <a name="spark-session-config"></a>Spark 세션 구성

**세션 구성** 에서 현재 Spark 세션에 제공할 실행기의 크기와 수, 제한 시간을 지정할 수 있습니다. Spark 세션을 다시 시작하면 구성 변경 사항이 적용됩니다. 캐시된 Notebook 변수는 모두 지워집니다.

```python
%%configure -f
{
    // refer to https://github.com/cloudera/livy#request-body for a list of valid parameters to config the session.
    "driverMemory":"2g",
    "driverCores":3,
    "executorMemory":"2g",
    "executorCores":2,
    "jars":[],
    "conf":{
        "spark.driver.maxResultSize":"10g"
    }
}
```

> [!NOTE]
>
> 출력 셀에서 표시 함수 및 Spark SQL이 제대로 렌더링되지 않을 수 있습니다. 

## <a name="submit-pyspark-batch-job-to-spark-pool"></a>Spark 풀에 PySpark 일괄 작업 제출

1. [이전에](#open-a-work-folder) 언급한 **Synaseexample** 폴더를 닫은 경우 다시 엽니다.  

2. [이전](#open-a-work-folder) 단계에 따라 새 **BatchFile.py** 파일을 만듭니다.

3. 다음 코드를 복사하여 스크립트 파일에 붙여넣습니다.

    ```python
    from __future__ import print_function
    import sys
    from operator import add
    from pyspark.sql import SparkSession
    if __name__ == "__main__":
        spark = SparkSession\
            .builder\
            .appName("PythonWordCount")\
            .getOrCreate()
    
        lines = spark.read.text('/HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv').rdd.map(lambda r: r[0])
        counts = lines.flatMap(lambda x: x.split(' '))\
                    .map(lambda x: (x, 1))\
                    .reduceByKey(add)
        output = counts.collect()
        for (word, count) in output:
            print("%s: %i" % (word, count))
        spark.stop()
    ```

4. Azure 계정에 아직 연결하지 않은 경우 [연결](#connect-to-your-spark-pools)합니다.

5. 스크립트 편집기를 마우스 오른쪽 단추로 클릭한 후 **Synapse: PySpark 일괄 처리** 를 선택합니다.

6. PySpark 작업을 제출할 Spark 풀을 선택합니다.

     ![Python 작업 결과 출력 제출](./media/vscode-tool-synapse/submit-pythonjob-result.png)

Spark 풀에 일괄 작업을 제출하면 제출 로그가 Visual Studio Code의 **출력** 창에 나타납니다. **Spark UI** URL 및 **Spark 작업 애플리케이션 UI** URL도 표시됩니다. 웹 브라우저에서 URL을 열어 작업 상태를 추적할 수 있습니다.

## <a name="access-and-manage-synapse-workspace"></a>Synapse 작업 영역 액세스 및 관리

VSCode용 Spark & Hive Tools 내 Azure 탐색기에서 다른 작업을 수행할 수 있습니다. Azure 탐색기에서.

![azure 이미지](./media/vscode-tool-synapse/azure.png)

### <a name="launch-workspace"></a>작업 영역 시작

1. Azure 탐색기에서 **SYNAPSE** 로 이동하여 펼치고 Synapse 구독 목록을 표시합니다.

     ![synapse 탐색기](./media/vscode-tool-synapse/synapse-explorer.png)

2. Synapse 작업 영역의 구독을 클릭하고 펼친 다음, 작업 영역 목록을 표시합니다.

3. 작업 영역을 마우스 오른쪽 단추로 클릭한 다음, **View Apache Spark applications**(Apache Spark 애플리케이션 보기)를 선택하면, Synapse Studio 웹 사이트에 Apache Spark 애플리케이션 페이지가 열립니다.

     ![작업 영역을 마우스 오른쪽 단추로 클릭](./media/vscode-tool-synapse/right-click-on-workspace.png)

     ![synapse studio 애플리케이션](./media/vscode-tool-synapse/synapse-studio-application.png)

4. 작업 영역을 펼치면 **기본 스토리지** 및 **Spark 풀** 이 표시됩니다.

5. **기본 스토리지** 를 마우스 오른쪽 단추로 클릭하면 **전체 경로 복사** 및 **Synapse Studio에서 열기** 가 표시됩니다. 

     ![기본 스토리지를 마우스 오른쪽 단추로 클릭](./media/vscode-tool-synapse/right-click-on-default-storage.png)

     - **전체 경로 복사** 를 클릭하면, 기본 ADLS Gen2 계정 URL이 복사되고 필요한 곳에 붙여 넣을 수 있습니다.

     - **Synapse Studio에서 열기** 를 클릭하면 Synapse Studio에 기본 스토리지 계정이 열립니다.

     ![synapse studio의 기본 스토리지](./media/vscode-tool-synapse/default-storage-in-synapse-studio.png)

6. **기본 스토리지** 를 펼치면 기본 스토리지 계정이 표시됩니다.

7. **Spark 풀** 을 펼치면 작업 영역의 모든 Spark 풀이 표시됩니다.

     ![스토리지 풀 펼치기](./media/vscode-tool-synapse/expand-storage-pool.png)


## <a name="known-issues"></a>알려진 문제

### <a name="ms-python-2020578807-version-is-not-supported-on-this-extention"></a>ms-python >=2020.5.78807 버전은 이 확장에서 지원되지 않음 

"Jupyter Notebook에 연결하지 못했습니다."는 python 버전 >=2020.5.78807의 알려진 문제입니다. 이 문제를 방지하려면 사용자가 **[2020.4.76186](https://github.com/microsoft/vscode-python/releases/download/2020.4.76186/ms-python-release.vsix)** 버전의 ms-python을 사용하는 것이 좋습니다.

![의 알려진 문제](./media/vscode-tool-synapse/known-issue.png)


## <a name="next-steps"></a>다음 단계

- [Azure Synapse Analytics](../overview-what-is.md)
- [Azure Synapse Analytics 작업 영역에 사용할 새 Apache Spark 풀 만들기](../../synapse-analytics/quickstart-create-apache-spark-pool-studio.md)
