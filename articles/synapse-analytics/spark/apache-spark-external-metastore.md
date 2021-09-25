---
title: Azure Synapse Spark 풀에 외부 Hive Metastore 사용
description: Azure Synapse Spark 풀에 대해 외부 Hive Metastore를 설정 하는 방법에 대해 알아봅니다.
keywords: 외부 Hive metastore, 공유, Synapse
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: spark
ms.author: yanacai
author: yanancai
ms.date: 09/08/2021
ms.openlocfilehash: 9a2d822d84d6356fbf34b45e24501e4426f1b06d
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128634291"
---
# <a name="use-external-hive-metastore-for-synapse-spark-pool-preview"></a>Synapse Spark 풀에 외부 Hive Metastore 사용 (미리 보기)

Azure Synapse Analytics를 사용 하면 동일한 작업 영역에 있는 Apache Spark 풀에서 관리 되는 HMS (Hive Metastore Service)와 호환 되는 Metastore를 카탈로그로 공유할 수 있습니다. 고객이 작업 영역 외부에서 Hive 카탈로그를 유지 하 고 HDInsight 및 Azure Databricks 같은 작업 영역 외부의 다른 계산 엔진과 카탈로그 개체를 공유 하려면 외부 Hive Metastore에 연결할 수 있습니다. 이 문서에서는 Synapse Spark를 외부 Apache Hive Metastore에 연결 하는 방법에 대해 알아봅니다. 

## <a name="supported-hive-metastore-versions"></a>지원 되는 Hive metastore 버전

이 기능은 Spark 2.4 및 Spark 3.0 둘 다에서 작동 합니다. 다음 표에서는 각 Spark 버전에 대해 지원 되는 HMS (Hive metastore 서비스) 버전을 보여 줍니다.


|Spark 버전|HMS 1.2. X|HMS 2.1. X|HMS 2.3. x|HMS 3.1. X|
|--|--|--|--|--|
|2.4|예|예|예|예|
|3|예|예|예|예|

## <a name="set-up-hive-metastore-linked-service"></a>연결 된 Hive metastore 서비스 설정

> [!NOTE]
> Azure SQL Database만 외부 Hive metastore 지원 됩니다.

Synapse 작업 영역에서 외부 Hive metastore에 대 한 연결 된 서비스를 설정 하려면 아래 단계를 수행 합니다.

1.  Synapse Studio를 열고 왼쪽에 있는 **연결 된 서비스 > 관리** 로 이동한 후 **새로 만들기** 를 클릭 하 여 새 연결 된 서비스를 만듭니다.

    :::image type="content" source="./media/use-external-metastore/set-up-hive-metastore-linked-service.png" alt-text="Hive Metastore 연결 된 서비스 설정" border="true":::

2.  **Azure SQL Database** 를 선택 하 고 **계속** 을 클릭 합니다.

3.  연결 된 서비스의 **이름을** 제공 합니다. 연결 된 서비스의 이름을 기록 합니다 .이 정보는 Spark를 구성 하는 데 사용 됩니다.

4.  Azure 구독 목록에서 외부 Hive metastore에 대 한 Azure SQL Database를 선택 하거나 정보를 수동으로 입력할 수 있습니다.

5.  현재 인증 유형으로 SQL 인증만 지원 합니다. **사용자 이름** 및 **암호** 를 제공 하 여 연결을 설정 합니다.

6.  **연결을 테스트** 하 여 사용자 이름과 암호를 확인 합니다.

7.  **만들기** 를 클릭 하 여 연결 된 서비스를 만듭니다. 

### <a name="test-connection-and-get-the-metastore-version-in-notebook"></a>연결 테스트 및 노트북에서 metastore 버전 가져오기
일부 네트워크 보안 규칙 설정은 Spark 풀에서 외부 Hive metastore DB로의 액세스를 차단할 수 있습니다. Spark 풀을 구성 하기 전에 모든 Spark 풀 노트북에서 아래 코드를 실행 하 여 외부 Hive metastore DB에 대 한 연결을 테스트 합니다. 

출력 결과에서 Hive metastore 버전을 가져올 수도 있습니다. Hive metastore 버전은 Spark 구성에 사용 됩니다.

```
%%spark 
import java.sql.DriverManager 
/** this url could be copied from Azure portal > Azure SQL database > Connection strings > JDBC **/ 
val url = s"jdbc:sqlserver://{your_servername_here}.database.windows.net:1433;database={your_database_here};user={your_username_here};password={your_password_here};encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=30;" 
try { 
    val connection = DriverManager.getConnection(url) 
    val result = connection.createStatement().executeQuery("select t.SCHEMA_VERSION from VERSION t") 
    result.next(); 
    println(s"Successful to test connection. Hive metastore version is ${result.getString(1)}") 
} catch { 
    case ex: Throwable =>println(s"Failed to establish connection:\n $ex") 
}  
```

## <a name="configure-spark-to-use-the-external-hive-metastore"></a>외부 Hive metastore를 사용 하도록 Spark 구성
외부 Hive metastore에 대 한 연결 된 서비스를 성공적으로 만든 후에는 외부 Hive metastore를 사용 하도록 Spark에서 몇 가지 구성을 설정 해야 합니다. Spark 풀 수준 또는 Spark 세션 수준에서 구성을 설정할 수 있습니다. 

구성 및 설명은 다음과 같습니다.

> [!NOTE]
> 기본 Hive metastore 버전은 2.3입니다. `spark.sql.hive.metastore.version` `spark.sql.hive.metastore.jars` Hive metastore 버전이 2.3 인 경우에는를 설정할 필요가 없습니다. 만 `spark.hadoop.hive.synapse.externalmetastore.linkedservice.name` 필요 합니다.

|Spark 구성|Description|
|--|--|
|`spark.sql.hive.metastore.version`|지원되는 버전: <ul><li>`1.2`</li><li>`2.1`</li><li>`3.1`</li></ul> 세 번째 부분 없이 처음 2 개 부분을 사용 해야 합니다.|
|`spark.sql.hive.metastore.jars`|<ul><li>버전 1.2: `/opt/hive-metastore/lib-1.2/*:/usr/hdp/current/hadoop-client/lib/*` </li><li>버전 2.1: `/opt/hive-metastore/lib-2.1/*:/usr/hdp/current/hadoop-client/lib/*` </li><li>버전 3.1: `/opt/hive-metastore/lib-3.1/*:/usr/hdp/current/hadoop-client/lib/*`</li></ul>|
|`spark.hadoop.hive.synapse.externalmetastore.linkedservice.name`|Azure SQL Database에 생성 된 연결 된 서비스의 이름입니다.|

### <a name="configure-spark-pool"></a>Spark 풀 구성 
Spark 풀을 만들 때 **추가 설정** 탭에서 아래 구성을 텍스트 파일에 넣고 **Apache Spark 구성** 섹션에 업로드 합니다. 기존 Spark 풀의 상황에 맞는 메뉴를 사용 하 고 Apache Spark 구성을 선택 하 여 이러한 구성을 추가할 수도 있습니다.

   :::image type="content" source="./media/use-external-metastore/config-spark-pool.png" alt-text="Spark 풀 구성":::

Metastore 버전 및 연결 된 서비스 이름을 업데이트 하 고 Spark 풀 구성에 대해 텍스트 파일에 configs 아래에 저장 합니다.

```
spark.sql.hive.metastore.version <your hms version, Make sure you use the first 2 parts without the 3rd part>
spark.hadoop.hive.synapse.externalmetastore.linkedservice.name <your linked service name to Azure SQL DB>
spark.sql.hive.metastore.jars /opt/hive-metastore/lib-<your hms version, 2 parts>/*:/usr/hdp/current/hadoop-client/lib/*
```

HiveCatalog21로 이름이 지정 된 연결 된 서비스를 사용 하는 metastore 버전 2.1에 대 한 예제는 다음과 같습니다.

```
spark.sql.hive.metastore.version 2.1
spark.hadoop.hive.synapse.externalmetastore.linkedservice.name HiveCatalog21
spark.sql.hive.metastore.jars /opt/hive-metastore/lib-2.1/*:/usr/hdp/current/hadoop-client/lib/*
```

### <a name="configure-a-spark-session"></a>Spark 세션 구성
Spark 풀을 구성 하지 않으려는 경우%% magic 구성 명령을 사용 하 여 노트북에서 Spark 세션을 구성할 수도 있습니다. 코드는 다음과 같습니다. 동일한 구성을 Spark batch 작업에 적용할 수도 있습니다. 

```
%%configure -f
{
    "conf":{
        "spark.sql.hive.metastore.version":"<your linked service name to Azure SQL DB>",
        "spark.hadoop.hive.synapse.externalmetastore.linkedservice.name":"<your linked service name to Azure SQL DB>",
        "spark.sql.hive.metastore.jars":"/opt/hive-metastore/lib-<your hms version, 2 parts>/*:/usr/hdp/current/hadoop-client/lib/*"
    }
}
```

### <a name="run-queries-to-verify-the-connection"></a>쿼리를 실행 하 여 연결 확인
이러한 모든 설정이 끝나면 Spark 노트북에서 아래 쿼리를 실행 하 여 카탈로그 개체를 나열 하 여 외부 Hive Metastore에 대 한 연결을 확인 하세요.
```python
spark.sql("show databases").show()
```

## <a name="set-up-storage-connection"></a>저장소 연결 설정
Hive metastore 데이터베이스에 대 한 연결 된 서비스는 Hive 카탈로그 메타 데이터에 대 한 액세스만 제공 합니다. 기존 테이블을 쿼리하려면 Hive 테이블의 기본 데이터를 저장 하는 저장소 계정에 대 한 연결도 설정 해야 합니다.

### <a name="set-up-connection-to-adls-gen-2"></a>ADLS Gen 2에 대 한 연결 설정
#### <a name="workspace-primary-storage-account"></a>작업 영역 기본 저장소 계정
Hive 테이블의 기본 데이터가 작업 영역 기본 저장소 계정에 저장 된 경우 추가 설정을 수행할 필요가 없습니다. 작업 영역을 만드는 동안 저장소 설정 지침을 수행 하는 동안에만 작동 합니다.

#### <a name="other-adls-gen-2-account"></a>다른 ADLS Gen 2 계정
Hive 카탈로그의 기본 데이터가 다른 ADLS Gen 2 계정에 저장 된 경우 Spark 쿼리를 실행 하는 사용자에 게 ADLS Gen2 저장소 계정에 대 한 **Storage Blob 데이터 참가자** 역할이 있는지 확인 해야 합니다. 

### <a name="set-up-connection-to-blob-storage"></a>Blob Storage에 대 한 연결 설정
Hive 테이블의 기본 데이터를 Azure Blob storage 계정에 저장 하는 경우 다음 단계에 따라 연결을 설정 합니다.

1.  Synapse Studio를 열고 **데이터 > 연결 됨 탭 >** **외부 데이터에 커넥트**> 추가 단추로 이동 합니다.

    :::image type="content" source="./media/use-external-metastore/connect-to-storage-account.png" alt-text="스토리지 계정에 연결" border="true":::

2.  **Azure Blob Storage** 를 선택 하 고 **계속** 을 클릭 합니다.
3.  연결 된 서비스의 **이름을** 제공 합니다. 연결 된 서비스의 이름을 기록 합니다 .이 정보는 Spark 세션 구성에 곧 사용 됩니다.
4.  Azure Blob Storage 계정을 선택 합니다. 인증 방법이 **계정 키** 인지 확인 합니다. 현재 Spark 풀은 계정 키를 통해서만 Blob Storage 계정에 액세스할 수 있습니다.
5.  **연결을 테스트** 하 고 **만들기** 를 클릭 합니다.
6.  Blob Storage 계정에 대 한 연결 된 서비스를 만든 후 spark 쿼리를 실행할 때 노트북에서 아래 spark 코드를 실행 하 여 spark 세션의 Blob Storage 계정에 액세스할 수 있도록 해야 합니다. [이 작업](./apache-spark-secure-credentials-with-tokenlibrary.md)을 수행 해야 하는 이유에 대해 자세히 알아보세요.

```
%%pyspark
blob_account_name = "<your blob storage account name>"
blob_container_name = "<your container name>"
from pyspark.sql import SparkSession
sc = SparkSession.builder.getOrCreate()
token_library = sc._jvm.com.microsoft.azure.synapse.tokenlibrary.TokenLibrary
blob_sas_token = token_library.getConnectionString("<blob storage linked service name>")
spark.conf.set('fs.azure.sas.%s.%s.blob.core.windows.net' % (blob_container_name, blob_account_name), blob_sas_token)
```

저장소 연결을 설정한 후 Hive Metastore에서 기존 테이블을 쿼리할 수 있습니다.

## <a name="known-limitations"></a>알려진 제한 사항

- Synapse Studio 개체 탐색기는 외부 HMS 대신 관리 되는 Synapse metastore에 개체를 계속 표시 하 고,이 환경을 개선 하 고 있습니다.
- SQL <-external hms를 사용 하는 경우 [> spark 동기화가](../sql/develop-storage-files-spark-tables.md) 작동 하지 않습니다.  
- Azure SQL Database만 외부 Hive Metastore 데이터베이스로 지원 됩니다. SQL 권한 부여만 지원 됩니다.
- 현재 Spark는 외부 Hive 테이블 및 비트랜잭션/비 ACID 관리 Hive 테이블 에서만 작동 합니다. 지금은 Hive ACID/트랜잭션 테이블을 지원 하지 않습니다.
- 현재 Apache Ranger 통합은 지원되지 않습니다.

## <a name="troubleshooting"></a>문제 해결
### <a name="see-below-error-when-querying-a-hive-table-with-data-stored-in-blob-storage"></a>Blob Storage 저장된 데이터가 있는 Hive 테이블을 쿼리할 때 아래 오류를 참조하세요.
```
Py4JJavaError : An error occurred while calling o241.load. : org.apache.hadoop.fs.azure.AzureException: org.apache.hadoop.fs.azure.AzureException: No credentials found for account demohdicatalohdistorage.blob.core.windows.net in the configuration, and its container demohdicatalog-2021-07-15t23-42-51-077z is not accessible using anonymous credentials. Please check if the container exists first. If it is not publicly available, you have to provide account credentials.
```

연결된 서비스를 통해 스토리지 계정에 키 인증을 사용하는 경우 Spark 세션에 대한 토큰을 얻기 위한 추가 단계를 수행해야 합니다. 아래 코드를 실행하여 쿼리를 실행하기 전에 Spark 세션을 구성합니다. 여기서 이 작업을 수행해야 하는 이유에 대해 자세히 알아보세요.

```
%%pyspark
blob_account_name = "<your blob storage account name>"
blob_container_name = "<your container name>"
from pyspark.sql import SparkSession
sc = SparkSession.builder.getOrCreate()
token_library = sc._jvm.com.microsoft.azure.synapse.tokenlibrary.TokenLibrary
blob_sas_token = token_library.getConnectionString("<blob storage linked service name>")
spark.conf.set('fs.azure.sas.%s.%s.blob.core.windows.net' % (blob_container_name, blob_account_name), blob_sas_token)
```

### <a name="see-below-error-when-query-a-table-stored-in-adls-gen2-account"></a>ADLS Gen2 계정에 저장된 테이블을 쿼리할 때 아래 오류를 참조하세요.
```
Py4JJavaError : An error occurred while calling o305.load. : Operation failed: "This request is not authorized to perform this operation using this permission.", 403, HEAD
```

이 문제는 Spark 쿼리를 실행하는 사용자가 기본 스토리지 계정에 충분히 액세스할 수 없기 때문에 발생할 수 있습니다. Spark 쿼리를 실행하는 사용자에게 ADLS Gen2 스토리지 계정에 **Storage Blob 데이터 기여자** 역할이 있는지 확인합니다. 이 단계는 연결된 서비스를 만든 후 나중에 수행할 수 있습니다.

### <a name="hms-schema-related-settings"></a>HMS 스키마 관련 설정 
HMS 백 엔드 스키마/버전을 변경하지 않으려면 다음 hive 구성이 기본적으로 시스템에서 설정됩니다. 
```
spark.hadoop.hive.metastore.schema.verification true 
spark.hadoop.hive.metastore.schema.verification.record.version false 
spark.hadoop.datanucleus.fixedDatastore true 
spark.hadoop.datanucleus.schema.autoCreateAll false 
```

HMS 버전이 1.2.1 또는 1.2.2인 경우 spark.hadoop.hive.metastore.schema.verification을 true로 설정하는 경우 1.2.0만 요구한다는 문제가 Hive에 있습니다. HMS 버전을 1.2.0으로 수정하거나 다음 두 가지 구성을 덮어쓰는 것이 좋습니다.

```
spark.hadoop.hive.metastore.schema.verification false 
spark.hadoop.hive.synapse.externalmetastore.schema.usedefault false
```

HMS 버전을 마이그레이션해야 하는 경우 [Hive 스키마 도구](https://cwiki.apache.org/confluence/display/Hive/Hive+Schema+Tool)를 사용하는 것이 좋습니다. HDInsight 클러스터에서 HMS를 사용한 경우 [HDI 제공 버전](https://docs.microsoft.com/azure/hdinsight/interactive-query/apache-hive-migrate-workloads)를 사용하는 것이 좋습니다. 

### <a name="when-sharing-the-metastore-with-hdinsight-40-spark-clusters-i-cannot-see-the-tables"></a>HDInsight 4.0 Spark 클러스터와 metastore를 공유하는 경우 테이블을 볼 수 없습니다.
HDInsight 4.0의 Spark 클러스터와 Hive 카탈로그를 공유하려면 `spark.hadoop.metastore.catalog.default` Synapse spark의 속성이 HDInsight Spark의 값과 일치하는지 확인하세요. 기본값은 `Spark`입니다.

### <a name="when-sharing-the-hive-metastore-with-hdinsight-40-hive-clusters-i-can-list-the-tables-successfully-but-only-get-empty-result-when-i-query-the-table"></a>HDInsight 4.0 Hive 클러스터와 Hive 메타스토어를 공유하는 경우 테이블을 성공적으로 나열할 수 있지만 테이블을 쿼리할 때만 빈 결과를 얻을 수 있습니다.
제한 사항에서 설명한 대로 Synapse Spark 풀은 외부 hive 테이블 및 비 트랜잭션/ACID 관리 테이블만 지원하며 현재 Hive ACID/트랜잭션 테이블을 지원하지 않습니다. 기본적으로 HDInsight 4.0 Hive 클러스터에서는 관리되는 모든 테이블이 기본적으로 ACID/트랜잭션 테이블로 만들어지기 때문에 해당 테이블을 쿼리할 때 빈 결과를 얻게 됩니다. 