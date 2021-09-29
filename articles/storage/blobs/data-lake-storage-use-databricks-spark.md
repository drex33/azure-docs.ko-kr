---
title: '자습서: Azure Data Lake Storage Gen2, Azure Databricks 및 Spark | Microsoft Docs'
description: 이 자습서에서는 Azure Databricks 클러스터에서 Spark 쿼리를 실행하여 Azure Data Lake Storage Gen2 스토리지 계정의 데이터에 액세스하는 방법을 보여 줍니다.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: tutorial
ms.date: 11/19/2019
ms.author: normesta
ms.reviewer: dineshm
ms.custom: devx-track-python
ms.openlocfilehash: 1df5b2ba2aa44102975bfd68330466fccd0c6bb0
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128593176"
---
# <a name="tutorial-azure-data-lake-storage-gen2-azure-databricks--spark"></a>자습서: Azure Data Lake Storage Gen2, Azure Databricks 및 Spark

이 자습서에서는 Azure Databricks 클러스터를 Azure Data Lake Storage Gen2가 활성화된 Azure 스토리지 계정에 저장된 데이터에 연결하는 방법을 보여줍니다. 이 연결을 통해 기본적으로 사용자의 클러스터에서 데이터에 대한 쿼리 및 분석을 실행할 수 있습니다.

이 자습서에서는 다음을 수행합니다.

> [!div class="checklist"]
> - Databricks 클러스터 만들기
> - 스토리지 계정으로 비구조적 데이터 수집
> - Blob 스토리지의 데이터에 대한 분석 실행

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="prerequisites"></a>사전 요구 사항

- Azure Data Lake Storage Gen2 계정을 만듭니다.

  [Data Lake Storage Gen2에서 사용할 스토리지 계정 만들기](create-data-lake-storage-account.md)를 참조하세요.

- 사용자 계정에 [Storage Blob 데이터 기여자 역할](assign-azure-role-data-access.md)이 할당되었는지 확인합니다.

- AzCopy v10을 설치합니다. [AzCopy v10을 사용하여 데이터 전송](../common/storage-use-azcopy-v10.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)을 참조하세요.

- 서비스 주체를 생성합니다. [방법: 포털을 사용하여 리소스에 액세스할 수 있는 Azure AD 애플리케이션 및 서비스 주체 만들기](../../active-directory/develop/howto-create-service-principal-portal.md)를 참조하세요.

  해당 문서의 단계를 수행할 때 해야 하는 두어 가지 항목이 있습니다.

  :heavy_check_mark: 문서의 [애플리케이션을 역할에 할당](../../active-directory/develop/howto-create-service-principal-portal.md#assign-a-role-to-the-application) 섹션에 있는 단계를 수행할 때 **Storage Blob 데이터 참가자** 역할을 서비스 주체에 할당해야 합니다.

  > [!IMPORTANT]
  > 역할을 Data Lake Storage Gen2 스토리지 계정의 범위에 할당해야 합니다. 역할은 부모 리소스 그룹 또는 구독에 할당할 수 있지만, 이러한 역할 할당이 스토리지 계정에 전파될 때까지 권한 관련 오류가 발생합니다.

  :heavy_check_mark: 문서의 [로그인을 위한 값 가져오기](../../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in) 섹션에 있는 단계를 수행하는 경우 테넌트 ID, 앱 ID 및 클라이언트 비밀 값을 텍스트 파일에 붙여넣습니다. 곧 이 값들이 필요합니다.

### <a name="download-the-flight-data"></a>비행 데이터 다운로드

이 자습서에서는 Bureau of Transportation Statistics의 비행 데이터를 사용하여 ETL 작업을 수행하는 방법을 보여줍니다. 자습서를 완료하려면 이 데이터를 다운로드해야 합니다.

1. [Research and Innovative Technology Administration, Bureau of Transportation Statistics](https://www.transtats.bts.gov/DL_SelectFields.asp?gnoyr_VQ=FGJ)로 이동합니다.

2. **미리 압축된 파일** 확인란을 선택하여 모든 데이터 필드를 선택합니다.

3. **다운로드** 단추를 선택하고 결과를 컴퓨터에 저장합니다.

4. 압축된 파일을 풀고, 파일 이름 및 파일 경로를 적어 둡니다. 이후 단계에서 이 정보가 필요합니다.

## <a name="create-an-azure-databricks-service"></a>Azure Databricks 서비스 만들기

이 섹션에서는 Azure Portal을 사용하여 Azure Databricks 서비스를 만듭니다.

1. Azure Portal에서 **리소스 만들기** > **분석** > **Azure Databricks** 를 차례로 선택합니다.

    ![Azure Portal의 Databricks](./media/data-lake-storage-use-databricks-spark/azure-databricks-on-portal.png "Azure Portal의 Databricks")

2. **Azure Databricks 서비스** 아래에서 다음 값을 입력하여 Databricks 서비스를 만듭니다.

    |속성  |설명  |
    |---------|---------|
    |**작업 영역 이름**     | Databricks 작업 영역에 대한 이름을 제공합니다.  |
    |**구독**     | 드롭다운에서 Azure 구독을 선택합니다.        |
    |**리소스 그룹**     | 새 리소스 그룹을 만들지, 아니면 기존 그룹을 사용할지 여부를 지정합니다. 리소스 그룹은 Azure 솔루션에 관련된 리소스를 보유하는 컨테이너입니다. 자세한 내용은 [Azure Resource Manager 개요](../../azure-resource-manager/management/overview.md)를 참조하세요. |
    |**위치**     | **미국 서부 2** 를 선택합니다. 사용 가능한 다른 영역은 [지역별 사용 가능한 Azure 서비스](https://azure.microsoft.com/regions/services/)를 참조하세요.       |
    |**가격 책정 계층**     |  **표준** 을 선택합니다.     |

    ![Azure Databricks 작업 영역 만들기](./media/data-lake-storage-use-databricks-spark/create-databricks-workspace.png "Azure Databricks 서비스 만들기")

3. 계정 생성에는 몇 분 정도가 소요됩니다. 작업 상태를 모니터링하려면 맨 위에 있는 진행률 표시줄을 확인합니다.

4. **대시보드에 고정** 을 선택한 다음, **만들기** 를 선택합니다.

## <a name="create-a-spark-cluster-in-azure-databricks"></a>Azure Databricks에 Spark 클러스터 만들기

1. Azure Portal에서 본인이 만든 Databricks 서비스로 이동한 다음, **작업 영역 시작** 을 선택합니다.

2. Azure Databricks 포털로 리디렉션됩니다. 포털에서 **클러스터** 를 선택합니다.

    ![Azure의 Databricks](./media/data-lake-storage-use-databricks-spark/databricks-on-azure.png "Azure의 Databricks")

3. **새 클러스터** 페이지에서 값을 제공하여 클러스터를 만듭니다.

    ![Azure에서 Databricks Spark 클러스터 만들기](./media/data-lake-storage-use-databricks-spark/create-databricks-spark-cluster.png "Azure에서 Databricks Spark 클러스터 만들기")

    다음 필드에 대한 값을 입력하고, 다른 필드에는 기본값을 그대로 적용합니다.

    - 클러스터의 이름을 입력합니다.

    - **Terminate after 120 minutes of inactivity**(비활성 120분 후 종료) 확인란을 선택했는지 확인합니다. 클러스터를 사용하지 않는 경우 클러스터를 종료하는 기간(분)을 제공합니다.

4. **클러스터 만들기** 를 선택합니다. 클러스터가 실행되면 Notebook을 클러스터에 연결하고 Spark 작업을 실행할 수 있습니다.

## <a name="ingest-data"></a>데이터 수집

### <a name="copy-source-data-into-the-storage-account"></a>스토리지 계정에 원본 데이터 복사

AzCopy를 사용하여 *.csv* 파일의 데이터를 Data Lake Storage Gen2 계정으로 복사합니다.

1. 명령 프롬프트 창을 열고, 다음 명령을 입력하여 스토리지 계정에 로그인합니다.

   ```bash
   azcopy login
   ```

   명령 프롬프트 창에 나타나는 지침에 따라 사용자 계정을 인증합니다.

2. *.csv* 계정의 데이터를 복사하려면 다음 명령을 입력합니다.

   ```bash
   azcopy cp "<csv-folder-path>" https://<storage-account-name>.dfs.core.windows.net/<container-name>/folder1/On_Time.csv
   ```

   - `<csv-folder-path>` 자리 표시자 값을 *.csv* 파일의 경로로 바꿉니다.

   - `<storage-account-name>` 자리 표시자 값을 스토리지 계정 이름으로 바꿉니다.

   - `<container-name>` 자리 표시자를 스토리지 계정의 컨테이너 이름으로 바꿉니다.

## <a name="create-a-container-and-mount-it"></a>컨테이너 생성 및 탑재

이 섹션에서는 스토리지 계정에 컨테이너와 폴더를 만듭니다.

1. [Azure Portal](https://portal.azure.com)에서 본인이 만든 Azure Databricks 서비스로 이동한 다음, **작업 영역 시작** 을 선택합니다.

2. 왼쪽 창에서 **작업 영역** 을 선택합니다. **작업 영역** 드롭다운에서 **만들기** > **Notebook** 을 차례로 선택합니다.

    ![Databricks에서 Notebook 만들기](./media/data-lake-storage-use-databricks-spark/databricks-create-notebook.png "Databricks에서 Notebook 만들기")

3. **노트북 만들기** 대화 상자에서 노트북 이름을 입력합니다. 언어로 **Python** 을 선택한 다음, 앞에서 만든 Spark 클러스터를 선택합니다.

4. **만들기** 를 선택합니다.

5. 다음 코드 블록을 복사하여 첫 번째 셀에 붙여넣습니다. 하지만 이 코드를 아직 실행하지 마십시오.

    ```python
    configs = {"fs.azure.account.auth.type": "OAuth",
           "fs.azure.account.oauth.provider.type": "org.apache.hadoop.fs.azurebfs.oauth2.ClientCredsTokenProvider",
           "fs.azure.account.oauth2.client.id": "<appId>",
           "fs.azure.account.oauth2.client.secret": "<clientSecret>",
           "fs.azure.account.oauth2.client.endpoint": "https://login.microsoftonline.com/<tenant>/oauth2/token",
           "fs.azure.createRemoteFileSystemDuringInitialization": "true"}

    dbutils.fs.mount(
    source = "abfss://<container-name>@<storage-account-name>.dfs.core.windows.net/folder1",
    mount_point = "/mnt/flightdata",
    extra_configs = configs)
    ```

18. 이 코드 블록에서 `appId`, `clientSecret`, `tenant` 및 `storage-account-name` 자리 표시자 값을 이 자습서의 필수 조건을 수행하는 동안 수집한 값으로 바꿉니다. `container-name` 자리 표시자 값을 컨테이너 이름으로 바꿉니다.

19. 이 블록에서 코드를 실행하려면 **SHIFT + ENTER** 키를 누릅니다.

   나중에 명령을 추가할 것이므로 이 Notebook을 계속 열어 둡니다.

### <a name="use-databricks-notebook-to-convert-csv-to-parquet"></a>Databricks Notebook을 사용하여 CSV를 Parquet로 변환

이전에 만든 Notebook에서 새 셀을 추가하고, 해당 셀에 다음 코드를 붙여넣습니다.

```python
# Use the previously established DBFS mount point to read the data.
# create a data frame to read data.

flightDF = spark.read.format('csv').options(
    header='true', inferschema='true').load("/mnt/flightdata/*.csv")

# read the airline csv file and write the output to parquet format for easy query.
flightDF.write.mode("append").parquet("/mnt/flightdata/parquet/flights")
print("Done")
```

## <a name="explore-data"></a>데이터 살펴보기

새 셀에서 AzCopy를 통해 업로드된 CSV 파일 목록을 가져오는 다음 스크립트를 붙여넣습니다.

```python
import os.path
import IPython
from pyspark.sql import SQLContext
display(dbutils.fs.ls("/mnt/flightdata"))
```

새 파일을 만들고 *parquet/flights* 폴더에 파일을 나열하려면 다음 스크립트를 실행합니다.

```python
dbutils.fs.put("/mnt/flightdata/1.txt", "Hello, World!", True)
dbutils.fs.ls("/mnt/flightdata/parquet/flights")
```

이러한 코드 샘플을 통해 Data Lake Storage Gen2가 사용되는 스토리지 계정에 저장된 데이터를 사용하여 HDFS의 계층적 특성을 살펴보았습니다.

## <a name="query-the-data"></a>데이터 쿼리

다음으로, 스토리지 계정에 업로드한 데이터를 쿼리할 수 있습니다. 다음 코드 블록을 각각 **Cmd 1** 에 입력하고, **Cmd+Enter** 를 눌러 Python 스크립트를 실행합니다.

데이터 원본에 대한 데이터 프레임을 만들려면 다음 스크립트를 실행합니다.

- `<csv-folder-path>` 자리 표시자 값을 *.csv* 파일의 경로로 바꿉니다.

```python
# Copy this into a Cmd cell in your notebook.
acDF = spark.read.format('csv').options(
    header='true', inferschema='true').load("/mnt/flightdata/On_Time.csv")
acDF.write.parquet('/mnt/flightdata/parquet/airlinecodes')

# read the existing parquet file for the flights database that was created earlier
flightDF = spark.read.format('parquet').options(
    header='true', inferschema='true').load("/mnt/flightdata/parquet/flights")

# print the schema of the dataframes
acDF.printSchema()
flightDF.printSchema()

# print the flight database size
print("Number of flights in the database: ", flightDF.count())

# show the first 20 rows (20 is the default)
# to show the first n rows, run: df.show(n)
acDF.show(100, False)
flightDF.show(20, False)

# Display to run visualizations
# preferably run this in a separate cmd cell
display(flightDF)
```

데이터에 대한 몇 가지 기본 분석 쿼리를 실행하는 이 스크립트를 입력합니다.

```python
# Run each of these queries, preferably in a separate cmd cell for separate analysis
# create a temporary sql view for querying flight information
FlightTable = spark.read.parquet('/mnt/flightdata/parquet/flights')
FlightTable.createOrReplaceTempView('FlightTable')

# create a temporary sql view for querying airline code information
AirlineCodes = spark.read.parquet('/mnt/flightdata/parquet/airlinecodes')
AirlineCodes.createOrReplaceTempView('AirlineCodes')

# using spark sql, query the parquet file to return total flights in January and February 2016
out1 = spark.sql("SELECT * FROM FlightTable WHERE Month=1 and Year= 2016")
NumJan2016Flights = out1.count()
out2 = spark.sql("SELECT * FROM FlightTable WHERE Month=2 and Year= 2016")
NumFeb2016Flights = out2.count()
print("Jan 2016: ", NumJan2016Flights, " Feb 2016: ", NumFeb2016Flights)
Total = NumJan2016Flights+NumFeb2016Flights
print("Total flights combined: ", Total)

# List out all the airports in Texas
out = spark.sql(
    "SELECT distinct(OriginCityName) FROM FlightTable where OriginStateName = 'Texas'")
print('Airports in Texas: ', out.show(100))

# find all airlines that fly from Texas
out1 = spark.sql(
    "SELECT distinct(Reporting_Airline) FROM FlightTable WHERE OriginStateName='Texas'")
print('Airlines that fly to/from Texas: ', out1.show(100, False))
```

## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요하지 않으면 리소스 그룹 및 모든 관련 리소스를 삭제합니다. 이렇게 하려면 스토리지 계정에 대한 리소스 그룹을 선택하고 **삭제** 를 선택합니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Azure HDInsight에서 Apache Hive를 사용하여 데이터 추출, 변환 및 로드](data-lake-storage-tutorial-extract-transform-load-hive.md)
