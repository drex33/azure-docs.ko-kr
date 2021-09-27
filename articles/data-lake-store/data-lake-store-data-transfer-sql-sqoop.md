---
title: Data Lake Storage Gen1과 Azure SQL 간에 데이터 복사 - Sqoop | Microsoft Docs
description: Sqoop를 사용하여 Azure SQL Database와 Azure Data Lake Storage Gen1 간에 데이터 복사
author: normesta
ms.service: data-lake-store
ms.topic: how-to
ms.date: 07/30/2019
ms.author: normesta
ms.openlocfilehash: e19a8e25ef879bef5c50f371e458127e534a2beb
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128648129"
---
# <a name="copy-data-between-data-lake-storage-gen1-and-azure-sql-database-using-sqoop"></a>Sqoop를 사용하여 Data Lake Storage Gen1과 Azure SQL Database 간에 데이터 복사

Apache Sqoop를 사용하여 Azure SQL Database와 Azure Data Lake Storage Gen1 간에 데이터를 가져오고 내보내는 방법을 알아봅니다.

## <a name="what-is-sqoop"></a>Sqoop 정의

빅 데이터 애플리케이션은 로그 및 파일과 같은 비구조적 및 반구조적 데이터를 처리하기 위한 자연스러운 선택입니다. 그러나 관계형 데이터베이스에 저장된 구조적 데이터를 처리해야 할 수도 있습니다.

[Apache Sqoop](https://sqoop.apache.org/docs/1.4.4/SqoopUserGuide.html)는 Data Lake Storage Gen1과 같은 빅 데이터 리포지토리와 관계형 데이터베이스 간에 데이터를 전송하도록 설계된 도구입니다. 이 도구를 사용하여 Azure SQL Database와 같은 RDBMS(관계형 데이터베이스 관리 시스템)에서 Data Lake Storage Gen1로 데이터를 가져올 수 있습니다. 그런 다음 빅 데이터 워크로드를 사용하여 데이터를 변환 및 분석한 후 RDBMS로 다시 내보낼 수 있습니다. 이 문서에서는 Azure SQL Database의 데이터베이스를 관계형 데이터베이스로 사용하여 가져오기/내보내기를 수행합니다.

## <a name="prerequisites"></a>필수 구성 요소

시작하기 전에 다음이 있어야 합니다.

* **Azure 구독**. [Azure 평가판](https://azure.microsoft.com/pricing/free-trial/)을 참조하세요.
* **Azure Data Lake Storage Gen1 계정**. 계정을 만드는 방법에 대한 지침은 [Azure Data Lake Storage Gen1 시작](data-lake-store-get-started-portal.md)을 참조하세요.
* Data Lake Storage Gen1 계정에 대한 액세스 권한이 있는 **Azure HDInsight 클러스터**. [Data Lake Storage Gen1을 사용하여 HDInsight 클러스터 만들기](data-lake-store-hdinsight-hadoop-use-portal.md)를 참조하세요. 이 문서에서는 Data Lake Storage Gen1 액세스 권한이 있는 HDInsight Linux 클러스터가 있다고 가정합니다.
* **Azure SQL Database**. Azure SQL Database에서 데이터베이스를 만드는 방법에 대한 지침은 [Azure SQL Database에서 데이터베이스 만들기](../azure-sql/database/single-database-create-quickstart.md)를 참조하세요.

## <a name="create-sample-tables-in-the-database"></a>데이터베이스에서 샘플 테이블 만들기

1. 시작하려면 데이터베이스에서 샘플 테이블 두 개를 만듭니다. [SQL Server Management Studio](../azure-sql/database/connect-query-ssms.md) 또는 Visual Studio를 사용하여 데이터베이스에 연결하고 다음 쿼리를 실행합니다.

    **Table1 만들기**

    ```tsql
    CREATE TABLE [dbo].[Table1](
    [ID] [int] NOT NULL,
    [FName] [nvarchar](50) NOT NULL,
    [LName] [nvarchar](50) NOT NULL,
     CONSTRAINT [PK_Table_1] PRIMARY KEY CLUSTERED
           (
                  [ID] ASC
           )
    ) ON [PRIMARY]
    GO
    ```

    **Table2 만들기**

    ```tsql
    CREATE TABLE [dbo].[Table2](
    [ID] [int] NOT NULL,
    [FName] [nvarchar](50) NOT NULL,
    [LName] [nvarchar](50) NOT NULL,
     CONSTRAINT [PK_Table_2] PRIMARY KEY CLUSTERED
           (
                  [ID] ASC
           )
    ) ON [PRIMARY]
    GO
    ```

1. 다음 명령을 실행하여 **Table1** 에 몇 가지 샘플 데이터를 추가합니다. **Table2** 를 빈 상태로 둡니다. 나중에 **Table1** 에서 Data Lake Storage Gen1로 데이터를 가져옵니다. 그런 다음, Data Lake Storage Gen1에서 **Table2** 로 데이터를 내보냅니다.

    ```tsql
    INSERT INTO [dbo].[Table1] VALUES (1,'Neal','Kell'), (2,'Lila','Fulton'), (3, 'Erna','Myers'), (4,'Annette','Simpson');
    ```

## <a name="use-sqoop-from-an-hdinsight-cluster-with-access-to-data-lake-storage-gen1"></a>Data Lake Storage Gen1 액세스 권한이 있는 HDInsight 클러스터에서 Sqoop 사용

HDInsight 클러스터에는 사용 가능한 Sqoop 패키지가 이미 있습니다. Data Lake Storage Gen1을 추가 스토리지로 사용하도록 HDInsight 클러스터를 구성한 경우 구성 변경 없이 Sqoop를 사용하여 관계형 데이터베이스(예: Azure SQL Database)와 Data Lake Storage Gen1 계정 간에 데이터를 가져오고 내보낼 수 있습니다.

1. 이 문서에서는 Linux 클러스터를 만든 것으로 가정하므로 SSH를 사용하여 클러스터에 연결해야 합니다. [Linux 기반 HDInsight 클러스터에 연결](../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md)을 참조하세요.

1. 클러스터에서 Data Lake Storage Gen1 계정에 액세스할 수 있는지 확인합니다. SSH 프롬프트에서 다음 명령을 실행합니다.

    ```console
    hdfs dfs -ls adl://<data_lake_storage_gen1_account>.azuredatalakestore.net/
    ```

   이 명령은 Data Lake Storage Gen1 계정의 파일/폴더 목록을 제공합니다.

### <a name="import-data-from-azure-sql-database-into-data-lake-storage-gen1"></a>Azure SQL Database에서 Data Lake Storage Gen1로 데이터 가져오기

1. Sqoop 패키지를 사용할 수 있는 디렉터리로 이동합니다. 일반적으로 이 위치는 `/usr/hdp/<version>/sqoop/bin`입니다.

1. **Table1** 에서 Data Lake Storage Gen1 계정으로 데이터를 가져옵니다. 다음 구문을 사용합니다.

    ```console
    sqoop-import --connect "jdbc:sqlserver://<sql-database-server-name>.database.windows.net:1433;username=<username>@<sql-database-server-name>;password=<password>;database=<sql-database-name>" --table Table1 --target-dir adl://<data-lake-storage-gen1-name>.azuredatalakestore.net/Sqoop/SqoopImportTable1
    ```

   **sql-database-server-name** 자리 표시자는 데이터베이스가 실행되는 서버의 이름을 나타냅니다. **sql-database-name** 자리 표시자는 실제 데이터베이스 이름을 나타냅니다.

   예제:

    ```console
    sqoop-import --connect "jdbc:sqlserver://mysqoopserver.database.windows.net:1433;username=user1@mysqoopserver;password=<password>;database=mysqoopdatabase" --table Table1 --target-dir adl://myadlsg1store.azuredatalakestore.net/Sqoop/SqoopImportTable1
    ```

1. 데이터가 Data Lake Storage Gen1 계정으로 전송되었는지 확인합니다. 다음 명령 실행:

    ```console
    hdfs dfs -ls adl://hdiadlsg1store.azuredatalakestore.net/Sqoop/SqoopImportTable1/
    ```

   다음 출력이 표시되어야 합니다.

    ```console
    -rwxrwxrwx   0 sshuser hdfs          0 2016-02-26 21:09 adl://hdiadlsg1store.azuredatalakestore.net/Sqoop/SqoopImportTable1/_SUCCESS
    -rwxrwxrwx   0 sshuser hdfs         12 2016-02-26 21:09 adl://hdiadlsg1store.azuredatalakestore.net/Sqoop/SqoopImportTable1/part-m-00000
    -rwxrwxrwx   0 sshuser hdfs         14 2016-02-26 21:09 adl://hdiadlsg1store.azuredatalakestore.net/Sqoop/SqoopImportTable1/part-m-00001
    -rwxrwxrwx   0 sshuser hdfs         13 2016-02-26 21:09 adl://hdiadlsg1store.azuredatalakestore.net/Sqoop/SqoopImportTable1/part-m-00002
    -rwxrwxrwx   0 sshuser hdfs         18 2016-02-26 21:09 adl://hdiadlsg1store.azuredatalakestore.net/Sqoop/SqoopImportTable1/part-m-00003
    ```

   각 **part-m-** _ 파일은 원본 테이블 _ *Table1**의 행에 해당합니다. part-m-* 파일의 내용을 살펴보고 확인할 수 있습니다.

### <a name="export-data-from-data-lake-storage-gen1-into-azure-sql-database"></a>Data Lake Storage Gen1에서 Azure SQL Database로 데이터 내보내기

1. Data Lake Storage Gen1 계정에서 Azure SQL Database의 빈 테이블 **Table2** 로 데이터를 내보냅니다. 다음 구문을 사용합니다.

    ```console
    sqoop-export --connect "jdbc:sqlserver://<sql-database-server-name>.database.windows.net:1433;username=<username>@<sql-database-server-name>;password=<password>;database=<sql-database-name>" --table Table2 --export-dir adl://<data-lake-storage-gen1-name>.azuredatalakestore.net/Sqoop/SqoopImportTable1 --input-fields-terminated-by ","
    ```

   예제:

    ```console
    sqoop-export --connect "jdbc:sqlserver://mysqoopserver.database.windows.net:1433;username=user1@mysqoopserver;password=<password>;database=mysqoopdatabase" --table Table2 --export-dir adl://myadlsg1store.azuredatalakestore.net/Sqoop/SqoopImportTable1 --input-fields-terminated-by ","
    ```

1. 데이터가 SQL Database 테이블에 업로드되었는지 확인합니다. [SQL Server Management Studio](../azure-sql/database/connect-query-ssms.md) 또는 Visual Studio를 사용하여 Azure SQL Database에 연결한 후, 다음 쿼리를 실행합니다.

    ```tsql
    SELECT * FROM TABLE2
    ```

   이 명령의 출력은 다음과 같습니다.

    ```output
     ID  FName    LName
    -------------------
    1    Neal     Kell
    2    Lila     Fulton
    3    Erna     Myers
    4    Annette  Simpson
    ```

## <a name="performance-considerations-while-using-sqoop"></a>Sqoop 사용에 대한 성능 고려 사항

Data Lake Storage Gen1에 데이터를 복사하는 Sqoop 작업의 성능 튜닝에 대한 자세한 내용은 [Sqoop 성능 블로그 게시물](/archive/blogs/shanyu/performance-tuning-for-hdinsight-storm-and-microsoft-azure-eventhubs)을 참조하세요.

## <a name="next-steps"></a>다음 단계

* [Azure Storage Blob에서 Data Lake Storage Gen1로 데이터 복사](data-lake-store-copy-data-azure-storage-blob.md)
* [Data Lake Storage Gen1의 데이터 보호](data-lake-store-secure-data.md)
* [Data Lake Storage Gen1에서 Azure Data Lake Analytics 사용](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Data Lake Storage Gen1에서 Azure HDInsight 사용](data-lake-store-hdinsight-hadoop-use-portal.md)