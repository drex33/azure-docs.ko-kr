---
title: Azure Synapse Spark 풀에 외부 Hive Metastore 사용
description: Azure Synapse Spark 풀에 대한 외부 Hive Metastore를 설정하는 방법을 알아봅니다.
keywords: 외부 Hive 메타스토어, 공유, Synapse
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: spark
ms.author: yanacai
author: yanancai
ms.date: 09/08/2021
ms.openlocfilehash: 805987eb38df3979904fc8b9f3bebfc7fdb1fdca
ms.sourcegitcommit: 613789059b275cfae44f2a983906cca06a8706ad
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/29/2021
ms.locfileid: "129276381"
---
# <a name="use-external-hive-metastore-for-synapse-spark-pool-preview"></a>Synapse Spark 풀에 외부 Hive Metastore 사용(미리 보기)

Azure Synapse Analytics 사용하면 동일한 작업 영역의 Apache Spark 풀이 관리되는 HMS(Hive Metastore 서비스) 호환 메타스토어를 카탈로그로 공유할 수 있습니다. 고객이 작업 영역 외부에 Hive 카탈로그를 보관하고 HDInsight 및 Azure Databricks 같은 작업 영역 외부의 다른 계산 엔진과 카탈로그 개체를 공유하려는 경우 외부 Hive Metastore에 연결할 수 있습니다. 이 문서에서는 Synapse Spark를 외부 Apache Hive Metastore에 연결하는 방법에 대해 알아봅니다. 

## <a name="supported-hive-metastore-versions"></a>지원되는 Hive 메타스토어 버전

이 기능은 Spark 2.4 및 Spark 3.0 모두에서 작동합니다. 다음 표에서는 각 Spark 버전에 대해 지원되는 Hive 메타스토어 서비스(HMS) 버전을 보여 있습니다.


|Spark 버전|HMS 1.2.X|HMS 2.1.X|HMS 2.3.x|HMS 3.1.X|
|--|--|--|--|--|
|2.4|예|예|예|예|
|3|예|예|예|예|

## <a name="set-up-hive-metastore-linked-service"></a>Hive metastore 연결된 서비스 설정

> [!NOTE]
> Azure SQL Database 외부 Hive 메타스토어로만 지원됩니다.

아래 단계에 따라 Synapse 작업 영역의 외부 Hive 메타스토어에 연결된 서비스를 설정합니다.

1. Synapse Studio 열고 왼쪽에서 **> 연결된 서비스 관리로** 이동하고 **새로** 만들기를 클릭하여 새 연결된 서비스를 만듭니다.

   :::image type="content" source="./media/use-external-metastore/set-up-hive-metastore-linked-service.png" alt-text="Hive Metastore 연결된 서비스 설정" border="true":::

2. **Azure SQL Database** 선택하고 **계속을** 클릭합니다.

3. 연결된 서비스의 **이름을** 제공합니다. 연결된 서비스의 이름을 기록합니다. 이 정보는 곧 Spark를 구성하는 데 사용됩니다.

4. Azure 구독 목록에서 외부 Hive 메타스토어에 대한 Azure SQL Database 선택하거나 정보를 수동으로 입력할 수 있습니다.

5. 현재 인증 유형으로 SQL 인증만 지원합니다. 연결을 설정하려면 **사용자 이름** 및 **암호를** 입력합니다.

6. **연결을 테스트하여** 사용자 이름 및 암호를 확인합니다.

7. **만들기를** 클릭하여 연결된 서비스를 만듭니다. 

### <a name="test-connection-and-get-the-metastore-version-in-notebook"></a>연결을 테스트하고 Notebook에서 metastore 버전을 얻습니다.
일부 네트워크 보안 규칙 설정은 Spark 풀에서 외부 Hive 메타스토어 DB로의 액세스를 차단할 수 있습니다. Spark 풀을 구성하기 전에 Spark 풀 Notebook에서 아래 코드를 실행하여 외부 Hive 메타스토어 DB에 대한 연결을 테스트합니다. 

출력 결과에서 Hive 메타스토어 버전을 얻을 수도 있습니다. Hive 메타스토어 버전은 Spark 구성에서 사용됩니다.

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

## <a name="configure-spark-to-use-the-external-hive-metastore"></a>외부 Hive 메타스토어를 사용하도록 Spark 구성
외부 Hive 메타스토어에 연결된 서비스를 성공적으로 만든 후에는 Spark에서 외부 Hive 메타스토어를 사용하도록 몇 가지 구성을 설정해야 합니다. Spark 풀 수준 또는 Spark 세션 수준에서 구성을 설정할 수 있습니다. 

구성 및 설명은 다음과 같습니다.

> [!NOTE]
> 기본 Hive 메타스토어 버전은 2.3입니다. `spark.sql.hive.metastore.version` `spark.sql.hive.metastore.jars` Hive 메타스토어 버전이 2.3인 경우 및 를 설정할 필요가 없습니다. `spark.hadoop.hive.synapse.externalmetastore.linkedservice.name`만 필요합니다.

|Spark 구성|Description|
|--|--|
|`spark.sql.hive.metastore.version`|지원되는 버전: <ul><li>`1.2`</li><li>`2.1`</li><li>`3.1`</li></ul> 세 번째 부분 없이 처음 2부를 사용해야 합니다.|
|`spark.sql.hive.metastore.jars`|<ul><li>버전 1.2: `/opt/hive-metastore/lib-1.2/*:/usr/hdp/current/hadoop-client/lib/*` </li><li>버전 2.1: `/opt/hive-metastore/lib-2.1/*:/usr/hdp/current/hadoop-client/lib/*` </li><li>버전 3.1: `/opt/hive-metastore/lib-3.1/*:/usr/hdp/current/hadoop-client/lib/*`</li></ul>|
|`spark.hadoop.hive.synapse.externalmetastore.linkedservice.name`|Azure SQL Database 만든 연결된 서비스의 이름입니다.|

### <a name="configure-spark-pool"></a>Spark 풀 구성 
Spark 풀을 만들 때 **추가 설정** 탭에서 아래 구성을 텍스트 파일에 배치하고 **Apache Spark 구성** 섹션에 업로드합니다. 기존 Spark 풀에 대한 상황에 맞는 메뉴를 사용하고 Apache Spark 구성을 선택하여 이러한 구성을 추가할 수도 있습니다.

   :::image type="content" source="./media/use-external-metastore/config-spark-pool.png" alt-text="Spark 풀 구성":::

metastore 버전 및 연결된 서비스 이름을 업데이트하고 Spark 풀 구성에 대한 텍스트 파일에 아래 구성을 저장합니다.

```
spark.sql.hive.metastore.version <your hms version, Make sure you use the first 2 parts without the 3rd part>
spark.hadoop.hive.synapse.externalmetastore.linkedservice.name <your linked service name to Azure SQL DB>
spark.sql.hive.metastore.jars /opt/hive-metastore/lib-<your hms version, 2 parts>/*:/usr/hdp/current/hadoop-client/lib/*
```

다음은 HiveCatalog21로 명명된 연결된 서비스가 있는 metastore 버전 2.1의 예입니다.

```
spark.sql.hive.metastore.version 2.1
spark.hadoop.hive.synapse.externalmetastore.linkedservice.name HiveCatalog21
spark.sql.hive.metastore.jars /opt/hive-metastore/lib-2.1/*:/usr/hdp/current/hadoop-client/lib/*
```

### <a name="configure-a-spark-session"></a>Spark 세션 구성
Spark 풀을 구성하지 않으려면 %%configure magic 명령을 사용하여 Notebook에서 Spark 세션을 구성할 수도 있습니다. 코드는 다음과 같습니다. Spark 일괄 처리 작업에도 동일한 구성을 적용할 수 있습니다. 

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

### <a name="run-queries-to-verify-the-connection"></a>쿼리를 실행하여 연결 확인
이러한 모든 설정 후에 Spark Notebook에서 아래 쿼리를 실행하여 카탈로그 개체를 나열하여 외부 Hive Metastore에 대한 연결을 확인합니다.
```python
spark.sql("show databases").show()
```

## <a name="set-up-storage-connection"></a>스토리지 연결 설정
Hive 메타스토어 데이터베이스에 연결된 서비스는 Hive 카탈로그 메타데이터에 대한 액세스만 제공합니다. 기존 테이블을 쿼리하려면 Hive 테이블의 기본 데이터도 저장하는 스토리지 계정에 대한 연결을 설정해야 합니다.

### <a name="set-up-connection-to-adls-gen-2"></a>ADLS Gen 2에 대한 연결 설정
#### <a name="workspace-primary-storage-account"></a>작업 영역 기본 스토리지 계정
Hive 테이블의 기본 데이터가 작업 영역 기본 스토리지 계정에 저장된 경우 추가 설정을 수행할 필요가 없습니다. 작업 영역을 만드는 동안 스토리지 설정 지침을 따르는 한 작동합니다.

#### <a name="other-adls-gen-2-account"></a>기타 ADLS Gen 2 계정
Hive 카탈로그의 기본 데이터가 다른 ADLS Gen 2 계정에 저장된 경우 Spark 쿼리를 실행하는 사용자에게 ADLS Gen2 스토리지 계정에 **대한 blob 데이터 기여자** 역할이 Storage 확인해야 합니다. 

### <a name="set-up-connection-to-blob-storage"></a>Blob Storage 연결 설정
Hive 테이블의 기본 데이터가 Azure Blob Storage 계정에 저장된 경우 아래 단계에 따라 연결을 설정합니다.

1. Synapse Studio 열고 데이터 > **연결된 탭 > 추가** 단추 > 외부 데이터에 **커넥트 으로 이동합니다.**

   :::image type="content" source="./media/use-external-metastore/connect-to-storage-account.png" alt-text="스토리지 계정에 연결" border="true":::

2. **Azure Blob Storage** 선택하고 **계속을** 클릭합니다.
3. 연결된 서비스의 **이름을** 제공합니다. 연결된 서비스의 이름을 기록합니다. 이 정보는 곧 Spark 세션 구성에서 사용됩니다.
4. Azure Blob Storage 계정을 선택합니다. 인증 방법이 **계정 키인지 확인합니다.** 현재 Spark 풀은 계정 키를 통해 Blob Storage 계정에만 액세스할 수 있습니다.
5. **연결을 테스트하고** **만들기를** 클릭합니다.
6. Blob Storage 계정에 연결된 서비스를 만든 후 Spark 쿼리를 실행할 때 Notebook에서 아래의 Spark 코드를 실행하여 Spark 세션에 대한 Blob Storage 계정에 액세스해야 합니다. [여기에서](./apache-spark-secure-credentials-with-tokenlibrary.md)이 작업을 수행해야 하는 이유에 대해 자세히 알아보세요.

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

스토리지 연결을 설정하면 Hive Metastore의 기존 테이블을 쿼리할 수 있습니다.

## <a name="known-limitations"></a>알려진 제한 사항

- Synapse Studio 개체 탐색기는 외부 HMS 대신 관리되는 Synapse 메타스토어에 개체를 계속 표시할 예정이며, 이에 대한 환경이 개선되고 있습니다.
- [외부 HMS를](../sql/develop-storage-files-spark-tables.md) 사용하는 경우 SQL < > Spark 동기화가 작동하지 않습니다.  
- Azure SQL Database 외부 Hive Metastore 데이터베이스로만 지원됩니다. SQL 권한 부여만 지원됩니다.
- 현재 Spark는 외부 Hive 테이블 및 비 트랜잭션/비 ACID 관리형 Hive 테이블만 작동합니다. 현재 Hive ACID/트랜잭션 테이블을 지원하지 않습니다.
- 현재 Apache 레인저 통합은 지원 되지 않습니다.

## <a name="troubleshooting"></a>문제 해결
### <a name="see-below-error-when-querying-a-hive-table-with-data-stored-in-blob-storage"></a>에 저장 된 데이터를 사용 하 여 Hive 테이블을 쿼리 하는 경우 아래 오류를 참조 Blob Storage
```
Py4JJavaError : An error occurred while calling o241.load. : org.apache.hadoop.fs.azure.AzureException: org.apache.hadoop.fs.azure.AzureException: No credentials found for account demohdicatalohdistorage.blob.core.windows.net in the configuration, and its container demohdicatalog-2021-07-15t23-42-51-077z is not accessible using anonymous credentials. Please check if the container exists first. If it is not publicly available, you have to provide account credentials.
```

연결 된 서비스를 통해 저장소 계정에 대 한 키 인증을 사용 하는 경우 Spark 세션의 토큰을 가져오기 위해 추가 단계를 수행 해야 합니다. 아래 코드를 실행 하 여 쿼리를 실행 하기 전에 Spark 세션을 구성 합니다. 이 작업을 수행 해야 하는 이유에 대해 자세히 알아보세요.

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

### <a name="see-below-error-when-query-a-table-stored-in-adls-gen2-account"></a>ADLS Gen2 계정에 저장 된 테이블을 쿼리 하는 경우 아래 오류를 참조 하세요.
```
Py4JJavaError : An error occurred while calling o305.load. : Operation failed: "This request is not authorized to perform this operation using this permission.", 403, HEAD
```

이는 Spark 쿼리를 실행 하는 사용자에 게 기본 저장소 계정에 대 한 충분 한 액세스 권한이 없는 경우에 발생할 수 있습니다. Spark 쿼리를 실행 하는 사용자에 게 ADLS Gen2 저장소 계정에 대 한 **Storage Blob 데이터 참가자** 역할이 있는지 확인 합니다. 이 단계는 나중에 연결 된 서비스를 만든 후에 수행할 수 있습니다.

### <a name="hms-schema-related-settings"></a>HMS 스키마 관련 설정 
HMS 백엔드 스키마/버전이 변경 되는 것을 방지 하기 위해 다음 hive configs는 기본적으로 시스템에 의해 설정 됩니다. 
```
spark.hadoop.hive.metastore.schema.verification true 
spark.hadoop.hive.metastore.schema.verification.record.version false 
spark.hadoop.datanucleus.fixedDatastore true 
spark.hadoop.datanucleus.schema.autoCreateAll false 
```

HMS 버전이 1.2.1 또는 1.2.2 인 경우 1.2.0를 true로 설정 하는 경우에만 요청 하는 Hive에 문제가 있습니다. 제안 사항은 HMS 버전을 1.2.0로 수정 하거나, 다음 두 가지 구성으로 덮어써서 해결할 수 있습니다.

```
spark.hadoop.hive.metastore.schema.verification false 
spark.hadoop.hive.synapse.externalmetastore.schema.usedefault false
```

HMS 버전을 마이그레이션해야 하는 경우 [hive 스키마 도구](https://cwiki.apache.org/confluence/display/Hive/Hive+Schema+Tool)를 사용 하는 것이 좋습니다. 그리고 HMS가 HDInsight 클러스터에서 사용 되는 경우 [Hms 제공 버전](../../hdinsight/interactive-query/apache-hive-migrate-workloads.md)을 사용 하는 것이 좋습니다. 

### <a name="when-sharing-the-metastore-with-hdinsight-40-spark-clusters-i-cannot-see-the-tables"></a>Metastore을 HDInsight 4.0 Spark 클러스터와 공유 하는 경우 테이블을 볼 수 없습니다.
HDInsight 4.0의 spark 클러스터와 Hive 카탈로그를 공유 하려면 `spark.hadoop.metastore.catalog.default` Synapse spark의 속성이 hdinsight spark의 값과 일치 하는지 확인 하세요. 기본값은 `Spark`입니다.

### <a name="when-sharing-the-hive-metastore-with-hdinsight-40-hive-clusters-i-can-list-the-tables-successfully-but-only-get-empty-result-when-i-query-the-table"></a>HDInsight 4.0 Hive 클러스터와 Hive metastore를 공유 하는 경우 테이블을 성공적으로 나열할 수 있지만 테이블을 쿼리할 때 빈 결과만 얻을 수 있습니다.
제한 사항에 설명 된 대로 Synapse Spark 풀은 외부 hive 테이블 및 비트랜잭션/ACID 관리 테이블만 지원 하 고 현재 Hive ACID/트랜잭션 테이블은 지원 하지 않습니다. 기본적으로 HDInsight 4.0 Hive 클러스터에서 관리 되는 모든 테이블은 기본적으로 ACID/트랜잭션 테이블로 만들어지므로 해당 테이블을 쿼리할 때 빈 결과를 얻을 수 있습니다. 