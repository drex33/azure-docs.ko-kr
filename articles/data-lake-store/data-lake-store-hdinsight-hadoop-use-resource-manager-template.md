---
title: 템플릿 - Data Lake Storage Gen1이 포함된 HDInsight 클러스터
description: Azure Resource Manager 템플릿을 사용하여 Azure Data Lake Storage Gen1이 포함된 Azure HDInsight 클러스터를 만들고 사용할 수 있습니다.
author: normesta
ms.service: data-lake-store
ms.topic: how-to
ms.date: 05/29/2018
ms.author: normesta
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 1cf45c977feae9f8c9bba6b63421805c35735ceb
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128597344"
---
# <a name="create-an-hdinsight-cluster-with-azure-data-lake-storage-gen1-using-azure-resource-manager-template"></a>Azure Resource Manager 템플릿을 사용하여 Azure Data Lake Storage Gen1을 사용하는 HDInsight 클러스터 만들기
> [!div class="op_single_selector"]
> * [포털 사용](data-lake-store-hdinsight-hadoop-use-portal.md)
> * [PowerShell 사용(기본 스토리지의 경우)](data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md)
> * [PowerShell 사용(추가 스토리지의 경우)](data-lake-store-hdinsight-hadoop-use-powershell.md)
> * [Resource Manager 사용](data-lake-store-hdinsight-hadoop-use-resource-manager-template.md)
>
>

Azure PowerShell을 사용하여 Azure Data Lake Storage Gen1을 **추가 스토리지로** 사용하는 HDInsight 클러스터를 구성하는 방법에 대해 알아봅니다.

지원되는 클러스터 유형의 경우 Data Lake Storage Gen1을 기본 스토리지 또는 추가 스토리지 계정으로 사용할 수 있습니다. Data Lake Storage Gen1을 추가 스토리지로 사용하는 경우 클러스터의 기본 스토리지 계정은 여전히 Azure Blob Storage(WASB)이고 클러스터 관련 파일(예: 로그 등)은 여전히 기본 스토리지에 기록되지만 처리하려는 데이터는 Data Lake Storage Gen1 계정에 저장될 수 있습니다. Data Lake Storage Gen1을 추가 스토리지 계정으로 사용하는 것은 클러스터에서 스토리지로 읽고 쓰는 성능 또는 기능에 영향을 주지 않습니다.

## <a name="using-data-lake-storage-gen1-for-hdinsight-cluster-storage"></a>HDInsight 클러스터 스토리지에 대해 Data Lake Storage Gen1 사용

Data Lake Storage Gen1에서 HDInsight를 사용하는 경우 다음 중요 사항을 고려해야 합니다.

* 기본 스토리지인 Data Lake Storage Gen1에 액세스할 수 있는 HDInsight 클러스터를 만드는 옵션은 HDInsight 버전 3.5 및 3.6에서 사용할 수 있습니다.

* 추가 스토리지로 Data Lake Storage Gen1에 액세스할 수 있는 HDInsight 클러스터를 만드는 옵션은 HDInsight 버전 3.2, 3.4, 3.5 및 3.6에서 사용할 수 있습니다.

이 문서에서 Data Lake Storage Gen1을 추가 스토리지로 사용하여 Hadoop 클러스터를 프로비전합니다. Data Lake Storage Gen1을 기본 스토리지로 사용하여 Hadoop 클러스터를 만드는 방법에 대한 지침은 [Azure Portal을 사용하여 Data Lake Storage Gen1이 포함된 HDInsight 클러스터 만들기](data-lake-store-hdinsight-hadoop-use-portal.md)를 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

이 자습서를 시작하기 전에 다음이 있어야 합니다.

* **Azure 구독**. [Azure 평가판](https://azure.microsoft.com/pricing/free-trial/)을 참조하세요.
* **Azure PowerShell 1.0 이상**. [Azure PowerShell 설치 및 구성 방법](/powershell/azure/)을 참조하세요.
* **Azure Active Directory 서비스 사용자**. 이 자습서의 단계에서는 Azure AD에서 서비스 사용자를 만드는 방법에 대한 지침을 제공합니다. 그러나 서비스 사용자를 만들려면 Azure AD 관리자여야 합니다. Azure AD 관리자인 경우 이 필수 조건을 건너뛰고 자습서를 진행할 수 있습니다.

    **Azure AD 관리자가 아닌 경우** 서비스 사용자를 만드는 데 필요한 단계를 수행할 수 없습니다. 이 경우 먼저 Azure AD 관리자가 서비스 주체를 만들어야 Data Lake Storage Gen1과 HDInsight 클러스터를 만들 수 있습니다. 또한 [인증서를 사용하여 서비스 사용자 만들기](../active-directory/develop/howto-authenticate-service-principal-powershell.md#create-service-principal-with-certificate-from-certificate-authority)에 설명된 대로 인증서를 사용하여 서비스 사용자를 만들어야 합니다.

## <a name="create-an-hdinsight-cluster-with-data-lake-storage-gen1"></a>Data Lake Storage Gen1을 사용하는 HDInsight 클러스터 만들기
Resource Manager 템플릿 및 템플릿 사용을 위한 필수 조건은 GitHub의 [Deploy a HDInsight Linux cluster with new Data Lake Storage Gen1](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.hdinsight/hdinsight-datalake-store-azure-storage)(새로운 Data Lake Storage Gen1을 사용하는 HDInsight Linux 클러스터 배포)에서 확인할 수 있습니다. 이 링크에 제공된 지침에 따라 Data Lake Storage Gen1을 추가 스토리지로 사용하는 HDInsight 클러스터를 만듭니다.

위에 언급된 링크의 지침에는 PowerShell이 필요합니다. 이러한 지침을 시작하기 전에 Azure 계정에 로그인해야 합니다. 바탕 화면에서 새 Azure PowerShell 창을 열고 다음 코드 조각을 입력합니다. 로그인하라는 메시지가 표시되면 구독 관리자/소유자 중 하나로 로그인해야 합니다.

```
# Log in to your Azure account
Connect-AzAccount

# List all the subscriptions associated to your account
Get-AzSubscription

# Select a subscription
Set-AzContext -SubscriptionId <subscription ID>
```

템플릿은 다음과 같은 리소스 종류를 배포합니다.

* [Microsoft.DataLakeStore/accounts](/azure/templates/microsoft.datalakestore/accounts)
* [Microsoft.Storage/storageAccounts](/azure/templates/microsoft.storage/storageaccounts)
* [Microsoft.HDInsight/clusters](/azure/templates/microsoft.hdinsight/clusters)

## <a name="upload-sample-data-to-data-lake-storage-gen1"></a>Data Lake Storage Gen1에 샘플 데이터 업로드
Resource Manager 템플릿은 Data Lake Storage Gen1을 사용하여 새 스토리지 계정을 만들고 HDInsight 클러스터에 연결합니다. 이제 일부 샘플 데이터를 Data Lake Storage Gen1에 업로드해야 합니다. Data Lake Storage Gen1의 스토리지 계정에 있는 데이터에 액세스하는 작업을 HDInsight 클러스터에서 실행하려면 자습서의 뒷부분에서 이 데이터가 필요합니다. 데이터를 업로드하는 방법에 대한 자침은 [Data Lake Storage Gen1에 파일 업로드](data-lake-store-get-started-portal.md#uploaddata)를 참조하세요. 업로드할 일부 샘플 데이터를 찾는 경우 **Azure 데이터 레이크 Git 리포지토리** 의 [Ambulance Data](https://github.com/Azure/usql/tree/master/Examples/Samples/Data/AmbulanceData)폴더에 있을 수 있습니다.

## <a name="set-relevant-acls-on-the-sample-data"></a>샘플 데이터에서 관련 ACL 설정
HDInsight 클러스터에서 업로드한 샘플 데이터에 액세스할 수 있는지 확인하려면 HDInsight 클러스터와 Data Lake Storage Gen1 간의 ID를 설정하는 데 사용되는 Azure AD 애플리케이션에서 액세스하려는 파일/폴더에 액세스할 수 있는지 확인해야 합니다. 이렇게 하려면 다음 단계를 수행합니다.

1. HDInsight 클러스터 및 Data Lake Storage Gen1의 스토리지 계정과 연결된 Azure AD 애플리케이션의 이름을 찾습니다. 이름을 찾는 한 가지 방법은 Resource Manager 템플릿을 사용하여 만든 HDInsight 클러스터 블레이드를 열고 **클러스터 Azure AD ID** 탭을 클릭한 다음, **서비스 사용자 표시 이름** 값을 확인하는 것입니다.
2. 이제 HDInsight 클러스터에서 액세스하려는 파일/폴더에서 Azure AD 애플리케이션에 대한 액세스를 제공합니다. Data Lake Storage Gen1의 파일/폴더에 대한 올바른 ACL을 설정하려면 [Data Lake Storage Gen1의 데이터 보안](data-lake-store-secure-data.md#filepermissions)을 참조하세요.

## <a name="run-test-jobs-on-the-hdinsight-cluster-to-use-data-lake-storage-gen1"></a>HDInsight 클러스터에서 테스트 작업을 실행하여 Data Lake Storage Gen1 사용
HDInsight 클러스터를 구성한 후에 클러스터에서 테스트 작업을 실행하여 HDInsight 클러스터가 Data Lake Storage Gen1에 액세스할 수 있는지 테스트할 수 있습니다. 이렇게 하려면 이전에 Data Lake Storage Gen1의 스토리지 계정에 업로드한 샘플 데이터를 사용하여 테이블을 만드는 샘플 Hive 작업을 실행합니다.

이 섹션에서는 HDInsight Linux 클러스터로 SSH하고 샘플 Hive 쿼리를 실행합니다. Windows 클라이언트를 사용하는 경우 [https://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](https://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)에서 다운로드할 수 있는 **PuTTY** 를 사용하는 것이 좋습니다.

PuTTY 사용에 대한 자세한 내용은 [Windows에서 HDInsight의 Linux 기반 Hadoop에 SSH 사용](../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md)을 참조하세요.

1. 연결되면 다음 명령을 사용하여 Hive CLI를 시작합니다.

   ```
   hive
   ```
2. CLI를 사용하여 다음 문을 입력하여 Data Lake Storage Gen1에서 샘플 데이터를 사용한 **vehicles** 라는 새 테이블을 만듭니다.

   ```
   DROP TABLE vehicles;
   CREATE EXTERNAL TABLE vehicles (str string) LOCATION 'adl://<mydatalakestoragegen1>.azuredatalakestore.net:443/';
   SELECT * FROM vehicles LIMIT 10;
   ```

   다음과 비슷한 내용이 출력됩니다.

   ```
   1,1,2014-09-14 00:00:03,46.81006,-92.08174,51,S,1
   1,2,2014-09-14 00:00:06,46.81006,-92.08174,13,NE,1
   1,3,2014-09-14 00:00:09,46.81006,-92.08174,48,NE,1
   1,4,2014-09-14 00:00:12,46.81006,-92.08174,30,W,1
   1,5,2014-09-14 00:00:15,46.81006,-92.08174,47,S,1
   1,6,2014-09-14 00:00:18,46.81006,-92.08174,9,S,1
   1,7,2014-09-14 00:00:21,46.81006,-92.08174,53,N,1
   1,8,2014-09-14 00:00:24,46.81006,-92.08174,63,SW,1
   1,9,2014-09-14 00:00:27,46.81006,-92.08174,4,NE,1
   1,10,2014-09-14 00:00:30,46.81006,-92.08174,31,N,1
   ```


## <a name="access-data-lake-storage-gen1-using-hdfs-commands"></a>HDFS 명령을 사용하여 Data Lake Storage Gen1 액세스
Data Lake Storage Gen1을 사용하도록 HDInsight 클러스터를 구성하고 나면 HDFS 셸 명령을 사용하여 저장소에 액세스할 수 있습니다.

이 섹션에서는 HDInsight Linux 클러스터로 SSH하고 HDFS 명령을 실행합니다. Windows 클라이언트를 사용하는 경우 [https://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](https://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)에서 다운로드할 수 있는 **PuTTY** 를 사용하는 것이 좋습니다.

PuTTY 사용에 대한 자세한 내용은 [Windows에서 HDInsight의 Linux 기반 Hadoop에 SSH 사용](../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md)을 참조하세요.

연결되면 다음 HDFS 파일 시스템 명령을 사용하여 Data Lake Storage Gen1의 스토리지 계정에 있는 파일을 나열합니다.

```
hdfs dfs -ls adl://<storage account with Data Lake Storage Gen1 name>.azuredatalakestore.net:443/
```

이전에 Data Lake Storage Gen1에 업로드한 파일이 나열되어야 합니다.

```
15/09/17 21:41:15 INFO web.CaboWebHdfsFileSystem: Replacing original urlConnectionFactory with org.apache.hadoop.hdfs.web.URLConnectionFactory@21a728d6
Found 1 items
-rwxrwxrwx   0 NotSupportYet NotSupportYet     671388 2015-09-16 22:16 adl://mydatalakestoragegen1.azuredatalakestore.net:443/mynewfolder
```

`hdfs dfs -put` 명령을 사용하여 일부 파일을 Data Lake Storage Gen1에 업로드한 다음, `hdfs dfs -ls`를 사용하여 파일이 성공적으로 업로드되었는지 여부를 확인할 수도 있습니다.


## <a name="next-steps"></a>다음 단계
* [Azure Storage Blob에서 Data Lake Storage Gen1로 데이터 복사](data-lake-store-copy-data-wasb-distcp.md)
* [Azure HDInsight 클러스터에 Data Lake Storage Gen1 사용](../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen1.md)
