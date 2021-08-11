---
title: .NET 및 HDInsight를 사용하여 Apache Sqoop 작업 실행 - Azure
description: HDInsight .NET SDK를 사용하여 Apache Hadoop 클러스터와 Azure SQL Database 간에 Apache Sqoop 가져오기 및 내보내기를 실행하는 방법을 알아봅니다.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive, hdiseo17may2017, devx-track-csharp
ms.date: 01/14/2020
ms.openlocfilehash: a06326f5b4cf34a06475c10e368c6dcbb49ca9e4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98928341"
---
# <a name="run-apache-sqoop-jobs-by-using-net-sdk-for-apache-hadoop-in-hdinsight"></a>HDInsight에서 Apache Hadoop에 대해 .NET SDK를 사용하여 Apache Sqoop 작업 실행

[!INCLUDE [sqoop-selector](../../../includes/hdinsight-selector-use-sqoop.md)]

Azure HDInsight .NET SDK를 사용하여 HDInsight 클러스터와 Azure SQL Database 또는 SQL Server 데이터베이스 간에 가져오고 내보내는 HDInsight에서 Apache Sqoop 작업을 실행하는 방법을 알아봅니다.

## <a name="prerequisites"></a>사전 요구 사항

* [HDInsight에서 Hadoop과 함께 Apache Sqoop 사용](./hdinsight-use-sqoop.md)에서 [테스트 환경 설정](./hdinsight-use-sqoop.md#create-cluster-and-sql-database) 완료.

* [Visual Studio](https://visualstudio.microsoft.com/vs/community/)

* Sqoop에 익숙해야 합니다. 자세한 내용은 [Sqoop 사용자 가이드](https://sqoop.apache.org/docs/1.4.7/SqoopUserGuide.html)를 참조하세요.

## <a name="use-sqoop-on-hdinsight-clusters-with-the-net-sdk"></a>.NET SDK를 사용하여 HDInsight 클러스터에서 Sqoop 사용

HDInsight .NET SDK는 .NET 클라이언트 라이브러리를 제공하므로 .NET에서 HDInsight 클러스터 작업을 더 쉽게 수행할 수 있습니다. 이 섹션에서는 C# 콘솔 애플리케이션을 만들어 `hivesampletable`을 필수 조건에서 만든 Azure SQL Database 테이블로 내보냅니다.

## <a name="set-up"></a>설정

1. Visual Studio를 시작하고 C# 콘솔 애플리케이션을 만듭니다.

1. **도구** > **NuGet 패키지 관리자** > **패키지 관리자 콘솔** 로 이동하고 다음 명령을 실행합니다.

    ```
    Install-Package Microsoft.Azure.Management.HDInsight.Job
    ```

## <a name="sqoop-export"></a>Sqoop 내보내기

Hive에서 SQL Server로  이 예제에서는 Hive `hivesampletable` 테이블의 데이터를 SQL Database의 `mobiledata` 테이블로 내보냅니다.

1. Program.cs 파일에서 다음 코드를 사용합니다. 코드를 편집하여 `ExistingClusterName` 및 `ExistingClusterPassword`의 값을 설정합니다.

    ```csharp
    using Microsoft.Azure.Management.HDInsight.Job;
    using Microsoft.Azure.Management.HDInsight.Job.Models;
    using Hyak.Common;
    
    namespace SubmitHDInsightJobDotNet
    {
        class Program
        {
            private static HDInsightJobManagementClient _hdiJobManagementClient;
    
            private const string ExistingClusterName = "<Your HDInsight Cluster Name>";
            private const string ExistingClusterPassword = "<Cluster User Password>";
            private const string ExistingClusterUri = ExistingClusterName + ".azurehdinsight.net";
            private const string ExistingClusterUsername = "admin";
    
            static void Main(string[] args)
            {
                System.Console.WriteLine("The application is running ...");
    
                var clusterCredentials = new BasicAuthenticationCloudCredentials { Username = ExistingClusterUsername, Password = ExistingClusterPassword };
                _hdiJobManagementClient = new HDInsightJobManagementClient(ExistingClusterUri, clusterCredentials);
    
                SubmitSqoopJob();
    
                System.Console.WriteLine("Press ENTER to continue ...");
                System.Console.ReadLine();
            }
    
            private static void SubmitSqoopJob()
            {
                var sqlDatabaseServerName = ExistingClusterName + "dbserver";
                var sqlDatabaseLogin = "sqluser";
                var sqlDatabaseLoginPassword = ExistingClusterPassword;
                var sqlDatabaseDatabaseName = ExistingClusterName + "db";
    
                // Connection string for using Azure SQL Database; Comment if using SQL Server
                var connectionString = "jdbc:sqlserver://" + sqlDatabaseServerName + ".database.windows.net;user=" + sqlDatabaseLogin + "@" + sqlDatabaseServerName + ";password=" + sqlDatabaseLoginPassword + ";database=" + sqlDatabaseDatabaseName;
    
                // Connection string for using SQL Server; Uncomment if using SQL Server
                // var connectionString = "jdbc:sqlserver://" + sqlDatabaseServerName + ";user=" + sqlDatabaseLogin + ";password=" + sqlDatabaseLoginPassword + ";database=" + sqlDatabaseDatabaseName;
    
                //sqoop start
                var tableName = "mobiledata";
    
                var parameters = new SqoopJobSubmissionParameters
                {
                     Command = "export --connect " + connectionString + " --table " + tableName + " --hcatalog-table hivesampletable"
                };
                //sqoop end
    
                System.Console.WriteLine("Submitting the Sqoop job to the cluster...");
                var response = _hdiJobManagementClient.JobManagement.SubmitSqoopJob(parameters);
                System.Console.WriteLine("Validating that the response is as expected...");
                System.Console.WriteLine("Response status code is " + response.StatusCode);
                System.Console.WriteLine("Validating the response object...");
                System.Console.WriteLine("JobId is " + response.JobSubmissionJsonResponse.Id);
            }
        }
    }
    ```

1. 프로그램을 실행하려면 **F5** 키를 선택합니다.

## <a name="sqoop-import"></a>Sqoop 가져오기

SQL Server에서 Azure Storage로 이 예제에서는 위에서 수행된 내보내기를 사용합니다.  이 예제에서는 SQL Database의 `mobiledata` 테이블에서 클러스터 기본 스토리지 계정의 `wasb:///tutorials/usesqoop/importeddata` 디렉터리로 데이터를 가져옵니다.

1. `//sqoop start //sqoop end` 블록에 있는 위 코드를 다음 코드로 바꿉니다.

    ```csharp
    var tableName = "mobiledata";
    var exportDir = "/tutorials/usesqoop/importeddata";
    
    var parameters = new SqoopJobSubmissionParameters
    {
        Command = "import --connect " + connectionString + " --table " + tableName + " --target-dir " +  exportDir + " --fields-terminated-by \\t --lines-terminated-by \\n -m 1"
    };
    ```

1. 프로그램을 실행하려면 **F5** 키를 선택합니다.

## <a name="limitations"></a>제한 사항

Linux 기반 HDInsight에에 대한 제한 사항은 다음과 같습니다.

* 대량 내보내기: Microsoft SQL Server 또는 Azure SQL Database로 데이터를 내보내는 데 사용되는 Sqoop 커넥터는 현재 대량 삽입을 지원하지 않습니다.

* 일괄 처리: `-batch` 스위치를 사용하면 Sqoop에서 삽입 작업을 일괄 처리하는 대신 여러 번의 삽입 작업을 수행합니다.

## <a name="next-steps"></a>다음 단계

이제 Sqoop을 사용하는 방법에 대해 알아봤습니다. 자세한 내용은 다음을 참조하세요.

* [HDInsight에서 Apache Oozie 사용](../hdinsight-use-oozie-linux-mac.md): Oozie 워크플로에서 Sqoop 작업을 사용합니다.
* [HDInsight에 데이터 업로드](../hdinsight-upload-data.md): HDInsight 또는 Azure Blob 스토리지에 데이터를 업로드하는 다른 방법을 찾습니다.
