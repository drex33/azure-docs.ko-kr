---
title: HDFS 호환 가능 Azure Storage에서 데이터 쿼리 - Azure HDInsight
description: Azure Storage 및 Azure Data Lake Storage에서 데이터를 쿼리하고 분석을 위해 결과를 저장하는 방법을 알아봅니다.
ms.service: hdinsight
ms.topic: how-to
ms.custom: seoapr2020
ms.date: 04/21/2020
ms.openlocfilehash: 5c0a735d01d91de3a114e373791037285c29c09b
ms.sourcegitcommit: 613789059b275cfae44f2a983906cca06a8706ad
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/29/2021
ms.locfileid: "129272664"
---
# <a name="use-azure-storage-with-azure-hdinsight-clusters"></a>Azure HDInsight 클러스터에서 Azure Storage 사용

[Azure Blob Storage](../storage/common/storage-introduction.md), [Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-overview.md) 또는 [Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md)에 데이터를 저장할 수 있습니다. 또는 이러한 옵션을 조합할 수도 있습니다. 이러한 스토리지 옵션을 사용하면 사용자 데이터 손실 없이 계산에 사용된 HDInsight 클러스터를 안전하게 삭제할 수 있습니다.

Apache Hadoop은 기본 파일 시스템의 개념을 지원합니다. 기본 파일 시스템은 기본 체계와 권한을 의미합니다. 상대 경로를 확인하기 위해 사용할 수 있습니다. HDInsight 클러스터를 만드는 과정에서 Azure Storage의 Blob 컨테이너를 기본 파일 시스템으로 지정할 수 있습니다. HDInsight 3.6의 경우 몇 가지 예외를 제외하고 Azure Blob storage나 Azure Data Lake Storage Gen1/Azure Data Lake Storage Gen2를 기본 파일 시스템으로 선택할 수 있습니다. 기본 및 연결된 스토리지로 Data Lake Storage Gen1을 사용하는 지원 가능성은 [HDInsight 클러스터에 대한 가용성](./hdinsight-hadoop-use-data-lake-storage-gen1.md#availability-for-hdinsight-clusters)을 참조하세요.

이 문서에서는 Azure Storage가 HDInsight 클러스터에서 작동하는 방식에 대해 알아봅니다. 
* Data Lake Storage Gen1이 HDInsight 클러스터에서 작동하는 방식에 대해 알아보려면 [Azure HDInsight 클러스터에서 Azure Data Lake Storage Gen1 사용](./hdinsight-hadoop-use-data-lake-storage-gen1.md)을 참조하세요.
* Data Lake Storage Gen2가 HDInsight 클러스터에서 작동하는 방식에 대해 알아보려면 [Azure HDInsight 클러스터에서 Azure Data Lake Storage Gen2 사용](./hdinsight-hadoop-use-data-lake-storage-gen2.md)을 참조하세요.
* HDInsight 클러스터를 만드는 방법에 대한 자세한 내용은 [HDInsight에서 Apache Hadoop 클러스터 만들기](./hdinsight-hadoop-provision-linux-clusters.md)를 참조하세요.

> [!IMPORTANT]  
> 스토리지 계정 종류 **BlobStorage** 는 HDInsight 클러스터의 보조 스토리지로만 사용할 수 있습니다.

| 스토리지 계정 종류 | 지원되는 서비스 | 지원되는 성능 계층 |지원되지 않는 성능 계층| 지원되는 액세스 계층 |
|----------------------|--------------------|-----------------------------|---|------------------------|
| StorageV2(범용 v2)  | Blob     | Standard                    |Premium| 핫, 쿨, 보관\*   |
| 스토리지(범용 v1)   | Blob     | Standard                    |Premium| 해당 없음                    |
| BlobStorage                    | Blob     | Standard                    |Premium| 핫, 쿨, 보관\*   |

기본 Blob 컨테이너는 비즈니스 데이터를 저장하는 데 사용하지 않는 것이 좋습니다. 스토리지 비용을 줄이기 위해 사용한 후에는 매번 기본 Blob 컨테이너를 삭제하는 것이 좋습니다. 기본 컨테이너에는 애플리케이션 및 시스템 로그가 포함되어 있습니다. 컨테이너를 삭제하기 전에 이러한 로그를 검색해야 합니다.

여러 클러스터에서 하나의 Blob 컨테이너를 기본 파일 시스템으로 공유하는 것은 지원되지 않습니다.

> [!NOTE]  
> 보관 액세스 계층은 몇 시간씩 검색이 대기되는 오프라인 계층으로, HDInsight용으로는 권장되지 않습니다. 자세한 내용은 [보관 액세스 계층](../storage/blobs/access-tiers-overview.md#archive-access-tier)을 참조하세요.

## <a name="access-files-from-within-cluster"></a>클러스터 내에서 파일에 액세스

HDInsight 클러스터에서 Data Lake Storage의 파일에 액세스할 수 있는 방법은 여러 가지입니다. URI 체계는 암호화되지 않은 액세스(*wasb:* 접두사가 있음)와 TLS로 암호화된 액세스(*wasbs* 가 있음)를 제공합니다. Azure의 동일한 지역에 있는 데이터에 액세스하는 경우에도 가능하면 *wasbs* 를 사용하는 것이 좋습니다.

* **정규화된 이름 사용**. 이 방법의 경우 액세스할 파일에 대한 전체 경로를 제공합니다.

    ```
    wasb://<containername>@<accountname>.blob.core.windows.net/<file.path>/
    wasbs://<containername>@<accountname>.blob.core.windows.net/<file.path>/
    ```

* **줄인 경로 형식 사용**. 이 방식의 경우 클러스터 루트에 대한 경로를 다음으로 대체합니다.

    ```
    wasb:///<file.path>/
    wasbs:///<file.path>/
    ```

* **상대 경로 사용**. 이 방법의 경우 액세스할 파일에 대한 상대 경로만 제공합니다.

    ```
    /<file.path>/
    ```

### <a name="data-access-examples"></a>데이터 액세스 예제

예제는 클러스터의 헤드 노드에 대한 [ssh 연결](./hdinsight-hadoop-linux-use-ssh-unix.md)을 기반으로 합니다. 이 예제에서는 세 가지 URI 스키마를 모두 사용합니다. `CONTAINERNAME` 및 `STORAGEACCOUNT`를 관련 값으로 바꾸기

#### <a name="a-few-hdfs-commands"></a>몇 가지 hdfs 명령

1. 로컬 스토리지에 파일을 만듭니다.

    ```bash
    touch testFile.txt
    ```

1. 클러스터 스토리지에 디렉터리를 만듭니다.

    ```bash
    hdfs dfs -mkdir wasbs://CONTAINERNAME@STORAGEACCOUNT.blob.core.windows.net/sampledata1/
    hdfs dfs -mkdir wasbs:///sampledata2/
    hdfs dfs -mkdir /sampledata3/
    ```

1. 로컬 스토리지에서 클러스터 스토리지로 데이터를 복사합니다.

    ```bash
    hdfs dfs -copyFromLocal testFile.txt  wasbs://CONTAINERNAME@STORAGEACCOUNT.blob.core.windows.net/sampledata1/
    hdfs dfs -copyFromLocal testFile.txt  wasbs:///sampledata2/
    hdfs dfs -copyFromLocal testFile.txt  /sampledata3/
    ```

1. 클러스터 스토리지의 디렉터리 콘텐츠를 나열합니다.

    ```bash
    hdfs dfs -ls wasbs://CONTAINERNAME@STORAGEACCOUNT.blob.core.windows.net/sampledata1/
    hdfs dfs -ls wasbs:///sampledata2/
    hdfs dfs -ls /sampledata3/
    ```

> [!NOTE]  
> HDInsight 외부에서 blob를 작업할 때 대부분의 유틸리티는 WASB 형식을 인식하지 않으며 대신 `example/jars/hadoop-mapreduce-examples.jar`과 같은 기본 경로 형식을 예상합니다.

#### <a name="creating-a-hive-table"></a>Hive 테이블 만들기

설명을 위해 세 가지 파일 위치가 표시되었습니다. 실제로 실행할 때는 `LOCATION` 항목 중 하나만 사용합니다.

```hql
DROP TABLE myTable;
CREATE EXTERNAL TABLE myTable (
    t1 string,
    t2 string,
    t3 string,
    t4 string,
    t5 string,
    t6 string,
    t7 string)
ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
STORED AS TEXTFILE
LOCATION 'wasbs://CONTAINERNAME@STORAGEACCOUNT.blob.core.windows.net/example/data/';
LOCATION 'wasbs:///example/data/';
LOCATION '/example/data/';
```

## <a name="access-files-from-outside-cluster"></a>외부 클러스터에서 파일에 액세스

Microsoft는 Azure Storage에서 작업하는 데 필요한 다음과 같은 도구를 제공합니다.

| 도구 | Linux | OS X | Windows |
| --- |:---:|:---:|:---:|
| [Azure Portal](../storage/blobs/storage-quickstart-blobs-portal.md) |✔ |✔ |✔ |
| [Azure CLI](../storage/blobs/storage-quickstart-blobs-cli.md) |✔ |✔ |✔ |
| [Azure PowerShell](../storage/blobs/storage-quickstart-blobs-powershell.md) | | |✔ |
| [AZCopy](../storage/common/storage-use-azcopy-v10.md) |✔ | |✔ |

## <a name="identify-storage-path-from-ambari"></a>Ambari에서 스토리지 경로 식별

* 구성된 기본 저장소의 전체 경로를 확인하려면

    **HDFS** > **구성** 으로 이동하고 필터 입력 상자에 `fs.defaultFS`를 입력합니다.

* wasb 저장소가 보조 스토리지로 구성되었는지 확인하려면 다음으로 이동합니다.

    **HDFS** > **구성** 으로 이동하고 필터 입력 상자에 `blob.core.windows.net`를 입력합니다.

Ambari REST API를 사용하여 경로를 가져오려면 [기본 스토리지 가져오기](./hdinsight-hadoop-manage-ambari-rest-api.md#get-the-default-storage)를 참조하세요.

## <a name="blob-containers"></a>Blob 컨테이너

Blob을 사용하려면 먼저 [Azure Storage 계정](../storage/common/storage-account-create.md)을 만듭니다. 이 단계의 일부로 스토리지 계정이 만들어지는 Azure 지역을 지정합니다. 클러스터와 스토리지 계정은 동일한 지역에서 호스트되어야 합니다. Hive 메타스토어 SQL Server 데이터베이스 및 Apache Oozie 메타스토어 SQL Server 데이터베이스는 동일한 지역에 위치해야 합니다.

어디에 있든, 만들어진 각 Blob은 Azure Storage 계정의 일부 컨테이너에 속합니다. 이 컨테이너는 HDInsight 외부에서 만든 기존 Blob일 수 있습니다. 또는 HDInsight 클러스터용으로 생성된 컨테이너일 수도 있습니다.

기본 Blob 컨테이너는 작업 기록 및 로그와 같은 클러스터 특정 정보를 저장합니다. 여러 HDInsight 클러스터의 기본 Blob 컨테이너를 공유하지 마세요. 이 작업을 수행하면 작업 기록이 손상될 수 있습니다. 각 클러스터에 다른 컨테이너를 사용하는 것이 좋습니다. 기본 스토리지 계정이 아닌 모든 관련 클러스터용으로 지정된, 연결된 스토리지 계정에 공유 데이터를 저장합니다. 연결된 스토리지 계정에 대한 자세한 내용은 [HDInsight 클러스터 만들기](hdinsight-hadoop-provision-linux-clusters.md)를 참조하세요. 그러나 원래 HDInsight 클러스터를 삭제한 후에 기본 스토리지 컨테이너를 다시 사용할 수 있습니다. HBase 클러스터의 경우 삭제된 HBase 클러스터에서 사용되는 기본 Blob 컨테이너를 사용하여 새 HBase 클러스터를 만들면 HBase 테이블 스키마 및 데이터를 실제로 유지할 수 있습니다.

[!INCLUDE [secure-transfer-enabled-storage-account](includes/hdinsight-secure-transfer.md)]

## <a name="use-additional-storage-accounts"></a>추가 스토리지 계정 사용

HDInsight 클러스터를 만드는 동안 클러스터와 연결할 Azure Storage 계정을 지정합니다. 또한 클러스터 생성 도중이나 생성 후에 동일한 Azure 구독 또는 서로 다른 Azure 구독의 스토리지 계정을 추가할 수 있습니다. 스토리지 계정 추가에 대한 지침은 [HDInsight 클러스터 만들기](hdinsight-hadoop-provision-linux-clusters.md)를 참조하세요.

> [!WARNING]  
> HDInsight 클러스터와 다른 위치에서는 추가 스토리지 계정을 사용할 수 없습니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 HDInsight로 HDFS 호환 Azure Storage를 사용하는 방법을 알아보았습니다. 이 스토리지를 사용하면 적응형 장기 보관 데이터 획득 솔루션을 구축할 수 있으며, HDInsight를 사용하여 저장된 구조적 및 비정형 데이터 내부의 정보를 활용할 수 있습니다.

자세한 내용은 다음을 참조하세요.

* [빠른 시작: Apache Hadoop 클러스터 만들기](hadoop/apache-hadoop-linux-create-cluster-get-started-portal.md)
* [자습서: HDInsight 클러스터 만들기](hdinsight-hadoop-provision-linux-clusters.md)
* [Azure HDInsight 클러스터에 Azure Data Lake Storage Gen2 사용](hdinsight-hadoop-use-data-lake-storage-gen2.md)
* [HDInsight에 데이터 업로드](hdinsight-upload-data.md)
* [자습서: Azure HDInsight에서 대화형 쿼리를 사용하여 데이터 추출, 변환 및 로드](./interactive-query/interactive-query-tutorial-analyze-flight-data.md)
* [Azure Storage 공유 액세스 서명을 사용하여 HDInsight에서 데이터 액세스 제한](hdinsight-storage-sharedaccesssignature-permissions.md)