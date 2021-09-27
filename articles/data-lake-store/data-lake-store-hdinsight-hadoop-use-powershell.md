---
title: PowerShell - HDInsight 및 Data Lake Storage Gen1 - 추가 기능 스토리지 - Azure
description: Azure PowerShell을 사용하여 Azure Data Lake Storage Gen1을 추가 스토리지로 사용하는 HDInsight 클러스터를 구성하는 방법을 알아봅니다.
author: normesta
ms.service: data-lake-store
ms.topic: how-to
ms.date: 05/29/2018
ms.author: normesta
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 60bb4a49fd264f992af20ceaf3acb1217148ec24
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128655641"
---
# <a name="use-azure-powershell-to-create-an-hdinsight-cluster-with-azure-data-lake-storage-gen1-as-additional-storage"></a>Azure PowerShell을 사용하여 Azure Data Lake Storage Gen1을 (추가 스토리지로) 사용하는 HDInsight 클러스터 만들기

> [!div class="op_single_selector"]
> * [포털 사용](data-lake-store-hdinsight-hadoop-use-portal.md)
> * [PowerShell 사용(기본 스토리지의 경우)](data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md)
> * [PowerShell 사용(추가 스토리지의 경우)](data-lake-store-hdinsight-hadoop-use-powershell.md)
> * [Resource Manager 사용](data-lake-store-hdinsight-hadoop-use-resource-manager-template.md)
>
>

Azure PowerShell을 사용하여 Azure Data Lake Storage Gen1을 **추가 스토리지로** 사용하는 HDInsight 클러스터를 구성하는 방법에 대해 알아봅니다. Data Lake Storage Gen1을 기본 스토리지로 사용하는 HDInsight 클러스터를 만드는 방법에 대한 지침은 [Data Lake Storage Gen1을 기본 스토리지로 사용하는 HDInsight 클러스터 만들기](data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md)를 참조하세요.

> [!NOTE]
> Data Lake Storage Gen1을 HDInsight 클러스터의 추가 스토리지로 사용하려는 경우 이 문서에서 설명한 대로 클러스터를 만드는 동안 이 작업을 수행하는 것이 좋습니다. 기존의 HDInsight 클러스터에 Data Lake Storage Gen1을 추가 스토리지로 추가하는 것은 복잡한 프로세스이며 오류가 발생하기 쉽습니다.
>

지원되는 클러스터 유형의 경우 Data Lake Storage Gen1은 기본 스토리지 또는 추가 스토리지 계정으로 사용할 수 있습니다. Data Lake Storage Gen1을 추가 스토리지로 사용하는 경우 클러스터의 기본 스토리지 계정은 여전히 Azure Blob Storage(WASB)이고 클러스터 관련 파일(예: 로그 등)은 여전히 기본 스토리지에 기록되지만 처리하려는 데이터는 Data Lake Storage Gen1에 저장될 수 있습니다. Data Lake Storage Gen1을 추가 스토리지 계정으로 사용하는 것은 클러스터에서 스토리지로 읽고 쓰는 성능 또는 기능에 영향을 주지 않습니다.

## <a name="using-data-lake-storage-gen1-for-hdinsight-cluster-storage"></a>HDInsight 클러스터 스토리지에 대해 Data Lake Storage Gen1 사용

Data Lake Storage Gen1에서 HDInsight를 사용하는 경우 다음 중요 사항을 고려해야 합니다.

* 추가 스토리지로 Data Lake Storage Gen1에 액세스할 수 있는 HDInsight 클러스터를 만드는 옵션은 HDInsight 버전 3.2, 3.4, 3.5 및 3.6에서 사용할 수 있습니다.

PowerShell을 사용하여 Data Lake Storage Gen1과 함께 작동하도록 HDInsight를 구성하는 단계는 다음과 같습니다.

* Data Lake Storage Gen1 계정 만들기 
* Data Lake Storage Gen1에 대한 역할 기반 액세스를 위한 인증 설정
* Data Lake Storage Gen1에 대한 인증을 사용하여 HDInsight 클러스터 만들기
* 클러스터에서 테스트 작업 실행

## <a name="prerequisites"></a>사전 요구 사항

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

이 자습서를 시작하기 전에 다음이 있어야 합니다.

* **Azure 구독**. [Azure 평가판](https://azure.microsoft.com/pricing/free-trial/)을 참조하세요.
* **Azure PowerShell 1.0 이상**. [Azure PowerShell 설치 및 구성 방법](/powershell/azure/)을 참조하세요.
* **Windows SDK**. [여기](https://dev.windows.com/en-us/downloads)에서 설치할 수 있습니다. 이를 사용하여 보안 인증서를 만듭니다.
* **Azure Active Directory 서비스 사용자**. 이 자습서의 단계에서는 Azure AD에서 서비스 사용자를 만드는 방법에 대한 지침을 제공합니다. 그러나 서비스 사용자를 만들려면 Azure AD 관리자여야 합니다. Azure AD 관리자인 경우 이 필수 조건을 건너뛰고 자습서를 진행할 수 있습니다.

    **Azure AD 관리자가 아닌 경우** 서비스 사용자를 만드는 데 필요한 단계를 수행할 수 없습니다. 이 경우 먼저 Azure AD 관리자가 서비스 주체를 만들어야 Data Lake Storage Gen1과 HDInsight 클러스터를 만들 수 있습니다. 또한 [인증서를 사용하여 서비스 사용자 만들기](../active-directory/develop/howto-authenticate-service-principal-powershell.md#create-service-principal-with-certificate-from-certificate-authority)에 설명된 대로 인증서를 사용하여 서비스 사용자를 만들어야 합니다.

## <a name="create-a-data-lake-storage-gen1-account"></a>Data Lake Storage Gen1 계정 만들기 
다음 단계에 따라 Data Lake Storage Gen1 계정을 만듭니다.

1. 바탕 화면에서 새 Azure PowerShell 창을 열고 다음 코드 조각을 입력합니다. 로그인하라는 메시지가 표시되면 구독 관리자/소유자 중 하나로 로그인해야 합니다.

    ```azurepowershell
    # Log in to your Azure account
    Connect-AzAccount

    # List all the subscriptions associated to your account
    Get-AzSubscription

    # Select a subscription
    Set-AzContext -SubscriptionId <subscription ID>

    # Register for Data Lake Storage Gen1
    Register-AzResourceProvider -ProviderNamespace "Microsoft.DataLakeStore"
    ```

   > [!NOTE]
   > Data Lake Storage Gen1 리소스 공급자를 등록할 때 `Register-AzResourceProvider : InvalidResourceNamespace: The resource namespace 'Microsoft.DataLakeStore' is invalid`와 유사한 오류가 나타나는 경우 구독이 Data Lake Storage Gen1에 대해 승인되지 않았을 수 있습니다. 이러한 [지침](data-lake-store-get-started-portal.md)에 따라 Data Lake Storage Gen1에 대해 Azure 구독을 활성화해야 합니다.
   >
   >
2. Data Lake Storage Gen1의 스토리지 계정은 Azure 리소스 그룹과 연결됩니다. Azure 리소스 그룹을 만드는 작업부터 시작합니다.

    ```azurepowershell
    $resourceGroupName = "<your new resource group name>"
    New-AzResourceGroup -Name $resourceGroupName -Location "East US 2"
    ```

    다음과 유사한 출력이 표시됩니다.

    ```output
    ResourceGroupName : hdiadlgrp
    Location          : eastus2
    ProvisioningState : Succeeded
    Tags              :
    ResourceId        : /subscriptions/<subscription-id>/resourceGroups/hdiadlgrp
    ```

3. Data Lake Storage Gen1에서 스토리지 계정을 만듭니다. 지정하는 계정 이름은 소문자와 숫자만 포함해야 합니다.

    ```azurepowershell
    $dataLakeStorageGen1Name = "<your new storage account with Data Lake Storage Gen1 name>"
    New-AzDataLakeStoreAccount -ResourceGroupName $resourceGroupName -Name $dataLakeStorageGen1Name -Location "East US 2"
    ```

    다음과 유사한 출력이 표시됩니다.

    ```output
    ...
    ProvisioningState           : Succeeded
    State                       : Active
    CreationTime                : 5/5/2017 10:53:56 PM
    EncryptionState             : Enabled
    ...
    LastModifiedTime            : 5/5/2017 10:53:56 PM
    Endpoint                    : hdiadlstore.azuredatalakestore.net
    DefaultGroup                :
    Id                          : /subscriptions/<subscription-id>/resourceGroups/hdiadlgrp/providers/Microsoft.DataLakeStore/accounts/hdiadlstore
    Name                        : hdiadlstore
    Type                        : Microsoft.DataLakeStore/accounts
    Location                    : East US 2
    Tags                        : {}
    ```

5. Data Lake Storage Gen1에 일부 예제 데이터를 업로드합니다. 나중에 이 문서에서 사용하여 HDInsight 클러스터에서 데이터에 액세스할 수 있는지 확인합니다. 업로드할 일부 샘플 데이터를 찾는 경우 **Azure 데이터 레이크 Git 리포지토리** 의 [Ambulance Data](https://github.com/MicrosoftBigData/usql/tree/master/Examples/Samples/Data/AmbulanceData)폴더에 있을 수 있습니다.

    ```azurepowershell
    $myrootdir = "/"
    Import-AzDataLakeStoreItem -AccountName $dataLakeStorageGen1Name -Path "C:\<path to data>\vehicle1_09142014.csv" -Destination $myrootdir\vehicle1_09142014.csv
    ```

## <a name="set-up-authentication-for-role-based-access-to-data-lake-storage-gen1"></a>Data Lake Storage Gen1에 대한 역할 기반 액세스를 위한 인증 설정

모든 Azure 구독은 Azure Active Directory와 연결됩니다. Azure Portal 또는 Azure Resource Manager API를 사용하여 구독 리소스에 액세스하는 사용자와 서비스는 먼저 해당 Azure Active Directory에 인증해야 합니다. Azure 리소스에 대한 적절한 역할을 할당하여 Azure 구독과 서비스에 액세스 권한을 부여합니다.  서비스의 경우 서비스 주체는 Azure AD(Azure Active Directory)의 서비스를 식별합니다. 이 섹션에서는 애플리케이션에 대한 서비스 주체를 만들고 Azure PowerShell을 통해 역할을 할당하여 HDInsight와 같은 애플리케이션 서비스에 Azure 리소스(이전에 만든 Data Lake Storage Gen1의 스토리지 계정)에 대한 액세스 권한을 할당하는 방법을 설명합니다.

Data Lake Storage Gen1에 대한 Active Directory 인증을 설정하려면 다음 작업을 수행해야 합니다.

* 자체 서명된 인증서 만들기
* Azure Active Directory 및 서비스 주체의 애플리케이션 만들기

### <a name="create-a-self-signed-certificate"></a>자체 서명된 인증서 만들기

이 섹션의 단계를 진행하기 전에 [Windows SDK](https://dev.windows.com/en-us/downloads) 가 설치되어 있는지 확인합니다. 또한 인증서가 만들어지는 **C:\mycertdir** 과 같은 디렉터리가 만들어져 있어야 합니다.

1. PowerShell 창에서 Windows SDK를 설치한 위치로 이동(일반적으로 `C:\Program Files (x86)\Windows Kits\10\bin\x86`)하고 [MakeCert][makecert] 유틸리티를 사용하여 자체 서명된 인증서와 프라이빗 키를 만듭니다. 다음 명령을 사용합니다.

    ```azurepowershell
    $certificateFileDir = "<my certificate directory>"
    cd $certificateFileDir

    makecert -sv mykey.pvk -n "cn=HDI-ADL-SP" CertFile.cer -r -len 2048
    ```

    프라이빗 키 암호를 입력하라는 메시지가 표시됩니다. 명령을 성공적으로 실행한 후 지정한 인증서 디렉터리에서 **CertFile.cer** 및 **mykey.pvk** 를 확인해야 합니다.
2. [Pvk2Pfx][pvk2pfx] 유틸리티를 사용하여 MakeCert가 생성한 .pvk 및 .cer 파일을 .pfx 파일로 변환합니다. 다음 명령을 실행합니다.

    ```azurepowershell
    pvk2pfx -pvk mykey.pvk -spc CertFile.cer -pfx CertFile.pfx -po <password>
    ```

    메시지가 표시되면 이전에 지정한 프라이빗 키 암호를 입력합니다. **-po** 매개 변수에 대해 지정한 값은 .pfx 파일에 연관된 암호입니다. 명령을 성공적으로 완료한 후 지정한 인증서 디렉터리에서 CertFile.pfx도 또한 확인해야 합니다.

### <a name="create-an-azure-active-directory-and-a-service-principal"></a>Azure Active Directory 및 서비스 주체 만들기

이 섹션에서는 Azure Active Directory 애플리케이션용 서비스 주체를 만들고, 서비스 주체에 역할을 할당하고, 인증서를 제공하여 서비스 주체로 인증하는 단계를 수행합니다. 다음 명령을 실행하여 Azure Active Directory에서 애플리케이션을 만듭니다.

1. PowerShell 콘솔 창에 다음 cmdlet을 붙여 넣습니다. **-DisplayName** 속성에 대해 지정한 값이 고유한지 확인합니다. 또한 **-HomePage** 및 **-IdentiferUris** 에 대한 값은 자리 표시자이며 확인되지 않습니다.

    ```azurepowershell
    $certificateFilePath = "$certificateFileDir\CertFile.pfx"

    $password = Read-Host -Prompt "Enter the password" # This is the password you specified for the .pfx file

    $certificatePFX = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2($certificateFilePath, $password)

    $rawCertificateData = $certificatePFX.GetRawCertData()

    $credential = [System.Convert]::ToBase64String($rawCertificateData)

    $application = New-AzADApplication `
        -DisplayName "HDIADL" `
        -HomePage "https://contoso.com" `
        -IdentifierUris "https://mycontoso.com" `
        -CertValue $credential  `
        -StartDate $certificatePFX.NotBefore  `
        -EndDate $certificatePFX.NotAfter

    $applicationId = $application.ApplicationId
    ```

2. 애플리케이션 ID를 사용하여 서비스 주체를 만듭니다.

    ```azurepowershell
    $servicePrincipal = New-AzADServicePrincipal -ApplicationId $applicationId

     $objectId = $servicePrincipal.Id
    ```

3. 서비스 주체에게 HDInsight 클러스터에서 액세스할 Data Lake Storage Gen1 폴더 및 파일에 대한 액세스 권한을 할당합니다. 아래 코드 조각은 Data Lake Storage Gen1의 스토리지 계정 루트(샘플 데이터 파일을 복사한 위치)와 파일 자체에 대한 액세스 권한을 제공합니다.

    ```azurepowershell
    Set-AzDataLakeStoreItemAclEntry -AccountName $dataLakeStorageGen1Name -Path / -AceType User -Id $objectId -Permissions All
    Set-AzDataLakeStoreItemAclEntry -AccountName $dataLakeStorageGen1Name -Path /vehicle1_09142014.csv -AceType User -Id $objectId -Permissions All
    ```

## <a name="create-an-hdinsight-linux-cluster-with-data-lake-storage-gen1-as-additional-storage"></a>Data Lake Storage Gen1을 추가 스토리지로 사용하는 HDInsight Linux 클러스터 만들기

이 섹션에서는 Data Lake Storage Gen1을 추가 스토리지로 사용하는 HDInsight Hadoop Linux 클러스터를 만듭니다. 이 릴리스의 경우 HDInsight 클러스터와 Data Lake Storage Gen1의 스토리지 계정은 동일한 위치에 있어야 합니다.

1. 구독 테넌트 ID 검색을 시작합니다. 나중에 필요합니다.

    ```azurepowershell
    $tenantID = (Get-AzContext).Tenant.TenantId
    ```

2. 이 릴리스에서 Hadoop 클러스터의 경우 Data Lake Storage Gen1은 클러스터에 대해 추가 스토리지로만 사용될 수 있습니다. 기본 스토리지는 여전히 Azure Blob Storage(WASB)입니다. 따라서 먼저 클러스터에 필요한 스토리지 계정 및 스토리지 컨테이너를 만들어 보겠습니다.

    ```azurepowershell
    # Create an Azure storage account
    $location = "East US 2"
    $storageAccountName = "<StorageAccountName>"   # Provide a Storage account name

    New-AzStorageAccount -ResourceGroupName $resourceGroupName -StorageAccountName $storageAccountName -Location $location -Type Standard_GRS

    # Create an Azure Blob Storage container
    $containerName = "<ContainerName>"              # Provide a container name
    $storageAccountKey = (Get-AzStorageAccountKey -Name $storageAccountName -ResourceGroupName $resourceGroupName)[0].Value
    $destContext = New-AzStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
    New-AzStorageContainer -Name $containerName -Context $destContext
    ```

3. HDInsight 클러스터를 만듭니다. 다음 cmdlet을 사용합니다.

    ```azurepowershell
    # Set these variables
    $clusterName = $containerName                   # As a best practice, have the same name for the cluster and container
    $clusterNodes = <ClusterSizeInNodes>            # The number of nodes in the HDInsight cluster
    $httpCredentials = Get-Credential
    $sshCredentials = Get-Credential

    New-AzHDInsightCluster -ClusterName $clusterName -ResourceGroupName $resourceGroupName -HttpCredential $httpCredentials -Location $location -DefaultStorageAccountName "$storageAccountName.blob.core.windows.net" -DefaultStorageAccountKey $storageAccountKey -DefaultStorageContainer $containerName  -ClusterSizeInNodes $clusterNodes -ClusterType Hadoop -Version "3.4" -OSType Linux -SshCredential $sshCredentials -ObjectID $objectId -AadTenantId $tenantID -CertificateFilePath $certificateFilePath -CertificatePassword $password
    ```

    cmdlet이 성공적으로 완료된 후 클러스터 세부 정보가 나열되는 출력이 표시됩니다.


## <a name="run-test-jobs-on-the-hdinsight-cluster-to-use-the-data-lake-storage-gen1"></a>HDInsight 클러스터에서 테스트 작업을 실행하여 Data Lake Storage Gen1 사용
HDInsight 클러스터를 구성한 후에 클러스터에서 테스트 작업을 실행하여 HDInsight 클러스터가 Data Lake Storage Gen1에 액세스할 수 있는지 테스트할 수 있습니다. 이렇게 하려면 이전에 Data Lake Storage Gen1의 스토리지 계정에 업로드한 샘플 데이터를 사용하여 테이블을 만드는 샘플 Hive 작업을 실행합니다.

이 섹션에서는 사용자가 만든 HDInsight Linux 클러스터로 SSH하고 샘플 Hive 쿼리를 실행합니다.

* Windows 클라이언트를 사용하여 클러스터로 SSH하는 경우 [Windows에서 HDInsight의 Linux 기반 Hadoop과 SSH 사용](../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md)을 참조하세요.
* Linux 클라이언트를 사용하여 클러스터로 SSH하는 경우 [Linux에서 HDInsight의 Linux 기반 Hadoop과 SSH 사용](../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md)을 참조하세요.

1. 연결되면 다음 명령을 사용하여 Hive CLI를 시작합니다.

    ```azurepowershell
    hive
    ```

2. CLI를 사용하여 다음 문을 입력하여 Data Lake Storage Gen1에서 샘플 데이터를 사용한 **vehicles** 라는 새 테이블을 만듭니다.

    ```azurepowershell
    DROP TABLE vehicles;
    CREATE EXTERNAL TABLE vehicles (str string) LOCATION 'adl://<mydatalakestoragegen1>.azuredatalakestore.net:443/';
    SELECT * FROM vehicles LIMIT 10;
    ```

    다음과 유사한 결과가 표시됩니다.

    ```output
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

이 섹션에서는 사용자가 만든 HDInsight Linux 클러스터로 SSH하고 HDFS 명령을 실행합니다.

* Windows 클라이언트를 사용하여 클러스터로 SSH하는 경우 [Windows에서 HDInsight의 Linux 기반 Hadoop과 SSH 사용](../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md)을 참조하세요.
* Linux 클라이언트를 사용하여 클러스터로 SSH하는 경우 [Linux에서 HDInsight의 Linux 기반 Hadoop과 SSH 사용](../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md)을 참조하세요.

연결되면 다음 HDFS 파일 시스템 명령을 사용하여 Data Lake Storage Gen1의 스토리지 계정에 있는 파일을 나열합니다.

```azurepowershell
hdfs dfs -ls adl://<storage account with Data Lake Storage Gen1 name>.azuredatalakestore.net:443/
```

이전에 Data Lake Storage Gen1에 업로드한 파일이 나열되어야 합니다.

```output
15/09/17 21:41:15 INFO web.CaboWebHdfsFileSystem: Replacing original urlConnectionFactory with org.apache.hadoop.hdfs.web.URLConnectionFactory@21a728d6
Found 1 items
-rwxrwxrwx   0 NotSupportYet NotSupportYet     671388 2015-09-16 22:16 adl://mydatalakestoragegen1.azuredatalakestore.net:443/mynewfolder
```

`hdfs dfs -put` 명령을 사용하여 일부 파일을 Data Lake Storage Gen1에 업로드한 다음, `hdfs dfs -ls`를 사용하여 파일이 성공적으로 업로드되었는지 여부를 확인할 수도 있습니다.

## <a name="see-also"></a>참고 항목
* [Azure HDInsight 클러스터에 Data Lake Storage Gen1 사용](../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen1.md)
* [포털: HDInsight 클러스터를 만들어 Data Lake Storage Gen1 사용](data-lake-store-hdinsight-hadoop-use-portal.md)

[makecert]: /windows-hardware/drivers/devtest/makecert
[pvk2pfx]: /windows-hardware/drivers/devtest/pvk2pfx
