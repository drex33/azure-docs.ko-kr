---
title: Azure HDInsight에서 Hadoop과 함께 Data Lake Storage Gen1 사용
description: Azure Data Lake Storage Gen1에서 데이터를 쿼리하고 분석을 위해 결과를 저장하는 방법을 알아봅니다.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,hdiseo17may2017,seoapr2020, devx-track-azurepowershell
ms.date: 04/24/2020
ms.openlocfilehash: ca46b129c36cf8affeda370880dd8d800ff23ae7
ms.sourcegitcommit: 20acb9ad4700559ca0d98c7c622770a0499dd7ba
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/29/2021
ms.locfileid: "110701512"
---
# <a name="use-data-lake-storage-gen1-with-azure-hdinsight-clusters"></a>Azure HDInsight 클러스터에 Data Lake Storage Gen1 사용

> [!Note]
> 향상된 성능과 새로운 기능을 위해 [Azure Data Lake Storage Gen2](hdinsight-hadoop-use-data-lake-storage-gen2.md)를 사용하여 새 HDInsight 클러스터를 배포하세요.

HDInsight 클러스터에서 데이터를 분석하기 위해 [`Azure Blob storage`](../storage/common/storage-introduction.md), [Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-overview.md) 또는 [Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md)에 데이터를 저장할 수 있습니다. 모든 스토리지 옵션을 사용하면 사용자 데이터 손실 없이 계산에 사용된 HDInsight 클러스터를 안전하게 삭제할 수 있습니다.

이 문서에서는 Data Lake Storage Gen1이 HDInsight 클러스터에서 작동하는 방식에 대해 알아봅니다. Azure Blob Storage가 HDInsight 클러스터에서 작동하는 방식에 대해 알아보려면 [Azure HDInsight 클러스터에서 Azure Storage 사용](hdinsight-hadoop-use-blob-storage.md)을 참조하세요. HDInsight 클러스터를 만드는 방법에 대한 자세한 내용은 [HDInsight에서 Apache Hadoop 클러스터 만들기](hdinsight-hadoop-provision-linux-clusters.md)를 참조하세요.

> [!NOTE]  
> Data Lake Storage Gen1은 항상 보안 채널을 통해 액세스되기 때문에 `adls` 파일 시스템 구성표 이름이 없습니다. 항상 `adl`을 사용합니다.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="availability-for-hdinsight-clusters"></a>HDInsight 클러스터에 대한 가용성

Apache Hadoop은 기본 파일 시스템의 개념을 지원합니다. 기본 파일 시스템은 기본 체계와 권한을 의미합니다. 상대 경로를 확인하기 위해 사용할 수 있습니다. HDInsight 클러스터를 만드는 프로세스에서 Azure Storage의 Blob 컨테이너를 기본 파일 시스템으로 지정할 수 있습니다. 또는 HDInsight 3.5 이상 버전의 경우 몇 가지 예외를 제외하고 Azure Blob Storage나 Azure Data Lake Storage Gen1을 기본 파일 시스템으로 선택할 수 있습니다. 클러스터와 스토리지 계정은 동일한 지역에서 호스트되어야 합니다.

HDInsight 클러스터는 Data Lake Storage Gen1을 두 가지 방식으로 사용할 수 있습니다.

* 기본 스토리지로
* 추가 스토리지로, Azure Blob Storage를 기본 스토리지로

현재는 일부 HDInsight 클러스터 유형/버전에서만 Data Lake Storage Gen1을 기본 스토리지 및 추가 스토리지 계정으로 사용하도록 지원합니다.

| HDInsight 클러스터 유형 | Data Lake Storage Gen1을 기본 스토리지로 | Data Lake Storage Gen1을 추가 스토리지로| 참고 |
|------------------------|------------------------------------|---------------------------------------|------|
| HDInsight 버전 4.0 | 예 | 예 |ADLS Gen1은 HDInsight 4.0에서 지원되지 않습니다 |
| HDInsight 버전 3.6 | 예 | 예 | HBase 제외|
| HDInsight 버전 3.5 | 예 | 예 | HBase 제외|
| HDInsight 버전 3.4 | 예 | 예 | |
| HDInsight 버전 3.3 | 예 | 예 | |
| HDInsight 버전 3.2 | 예 | 예 | |
| Storm | | |Data Lake Storage Gen1을 사용하여 Storm 토폴로지에서 데이터를 쓸 수 있습니다. Storm 토폴로지에서 읽을 수 있는 참조 데이터에 Data Lake Storage Gen1을 사용할 수도 있습니다.|

> [!WARNING]  
> HDInsight HBase는 Azure Data Lake Storage Gen1에서 지원되지 않습니다.

Data Lake Storage Gen1 추가 스토리지 계정으로 사용하는 것은 성능에 영향을 미치지 않습니다. 또는 클러스터에서 Azure Blob Storage를 읽거나 쓸 수 있습니다.

## <a name="use-data-lake-storage-gen1-as-default-storage"></a>Data Lake Storage Gen1을 기본 스토리지로 사용

HDInsight가 Data Lake Storage Gen1을 기본 스토리지로 하여 배포되는 경우 클러스터 관련 파일은 `adl://mydatalakestore/<cluster_root_path>/`에 저장됩니다. 여기서 `<cluster_root_path>`는 Data Lake Storage에서 만드는 폴더의 이름입니다. 각 클러스터에 대한 루트 경로를 지정하면 동일한 Data Lake Storage 계정을 둘 이상의 클러스터에 사용할 수 있습니다. 따라서 다음 위치에 설정할 수 있습니다.

* 클러스터1에 `adl://mydatalakestore/cluster1storage` 경로를 사용할 수 있습니다.
* 클러스터2에 `adl://mydatalakestore/cluster2storage` 경로를 사용할 수 있습니다.

두 클러스터 모두 동일한 Data Lake Storage Gen1 계정인 **mydatalakestore** 를 사용하는 것에 유의하세요. 각 클러스터는 Data Lake Storage의 자체 루트 파일 시스템에 액세스 권한을 갖습니다. Azure Portal 배포 환경은 루트 경로에 **/clusters/\<clustername>** 와 같은 폴더 이름을 사용하라는 메시지를 표시합니다.

Data Lake Storage Gen1을 기본 스토리지로 사용하면 다음 경로에 대한 서비스 보안 주체 액세스 권한을 부여해야 합니다.

* Data Lake Storage Gen1 계정 루트.  예: adl://mydatalakestore/.
* 모든 클러스터 폴더에 대한 경로.  예: adl://mydatalakestore/clusters.
* 클러스터에 대한 폴더.  예: adl://mydatalakestore/clusters/cluster1storage.

서비스 주체 및 액세스 부여 만들기에 대한 자세한 내용은 Data Lake Storage 액세스 구성을 참조하세요.

### <a name="extracting-a-certificate-from-azure-keyvault-for-use-in-cluster-creation"></a>클러스터 생성에 사용하기 위해 Azure KeyVault에서 인증서 추출

서비스 보안 주체에 대한 인증서가 Azure Key Vault에 저장된 경우, 인증서를 올바른 형식으로 변환해야 합니다. 다음 코드 조각은 전환을 수행하는 방법을 표시합니다.

먼저 Key Vault에서 인증서를 다운로드하고 `SecretValueText`를 추출합니다.

```powershell
$certPassword = Read-Host "Enter Certificate Password"
$cert = (Get-AzureKeyVaultSecret -VaultName 'MY-KEY-VAULT' -Name 'MY-SECRET-NAME')
$certValue = [System.Convert]::FromBase64String($cert.SecretValueText)
```

다음으로 `SecretValueText`를 인증서로 변환합니다.

```powershell
$certObject = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2 -ArgumentList $certValue,$null,"Exportable, PersistKeySet"
$certBytes = $certObject.Export([System.Security.Cryptography.X509Certificates.X509ContentType]::Pkcs12, $certPassword.SecretValueText);
$identityCertificate = [System.Convert]::ToBase64String($certBytes)
```

그런 다음, `$identityCertificate`를 사용하여 다음 코드 조각에서처럼 새 클러스터를 배포할 수 있습니다.

```powershell
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile $pathToArmTemplate `
    -identityCertificate $identityCertificate `
    -identityCertificatePassword $certPassword.SecretValueText `
    -clusterName  $clusterName `
    -clusterLoginPassword $SSHpassword `
    -sshPassword $SSHpassword `
    -servicePrincipalApplicationId $application.ApplicationId
```

## <a name="use-data-lake-storage-gen1-as-additional-storage"></a>추가 스토리지로 Data Lake Storage Gen1 사용

Data Lake Storage Gen1을 클러스터에 대한 추가 스토리지로 사용할 수도 있습니다. 이런 경우 클러스터 기본 스토리지는 Azure Blob Storage 또는 Azure Data Lake Storage Gen1 계정입니다. 추가 스토리지로 Azure Data Lake Storage Gen1에 저장된 데이터에 대해 HDInsight 작업을 실행하는 경우 정규화된 경로를 사용합니다. 예를 들면 다음과 같습니다.

`adl://mydatalakestore.azuredatalakestore.net/<file_path>`

이제 URL에 **cluster_root_path** 가 없습니다. 이 경우 Data Lake Storage가 기본 스토리지가 아니기 때문입니다. 따라서 파일 경로를 제공하기만 하면 됩니다.

Data Lake Storage Gen1을 기본 스토리지로 사용하려면 파일이 저장된 경로에 대해 서비스 보안 주체 액세스 권한을 부여해야 합니다.  예를 들면 다음과 같습니다.

`adl://mydatalakestore.azuredatalakestore.net/<file_path>`

서비스 주체 및 액세스 부여 만들기에 대한 자세한 내용은 Data Lake Storage 액세스 구성을 참조하세요.

## <a name="use-more-than-one-data-lake-storage-gen1-account"></a>두 개 이상의 Data Lake Storage Gen1 계정 사용

Data Lake Storage 계정을 새로 추가하고 Data Lake Storage 계정을 두 개 이상 추가할 수 있습니다. 하나 이상의 Data Lake Storage 계정의 데이터에 대한 HDInsight 클러스터 권한을 부여합니다. 아래의 Data Lake Storage Gen1 액세스 구성을 참조하세요.

## <a name="configure-data-lake-storage-gen1-access"></a>Data Lake Storage Gen1 액세스 구성

HDInsight 클러스터에서 Azure Data Lake Storage Gen1 액세스를 구성하려면 Azure AD(Azure Active Directory) 서비스 보안 주체가 있어야 합니다. Azure AD 관리자만 서비스 주체를 만들 수 있습니다. 서비스 주체는 인증서로 만들어야 합니다. 자세한 내용은 [빠른 시작: HDInsight에서 클러스터 설정](./hdinsight-hadoop-provision-linux-clusters.md) 및 [자체 서명된 인증서로 서비스 주체 만들기](../active-directory/develop/howto-authenticate-service-principal-powershell.md#create-service-principal-with-self-signed-certificate)를 참조하세요.

> [!NOTE]  
> Azure Data Lake Storage Gen1을 HDInsight 클러스터의 추가 스토리지로 사용하려는 경우 이 문서에서 설명한 대로 클러스터를 만드는 동안 이 작업을 수행하는 것이 좋습니다. 기존 HDInsight 클러스터에 Azure Data Lake Storage Gen1을 추가 스토리지로 추가하는 시나리오는 지원되지 않습니다.

액세스 제어 모델에 대한 자세한 정보는 [Azure Data Lake Storage Gen1의 액세스 컨트롤](../data-lake-store/data-lake-store-access-control.md)을 참조 하세요.

## <a name="access-files-from-the-cluster"></a>클러스터에서 파일 액세스

HDInsight 클러스터에서 Data Lake Storage의 파일에 액세스할 수 있는 방법은 여러 가지입니다.

* **정규화된 이름 사용**. 이 방법의 경우 액세스할 파일에 대한 전체 경로를 제공합니다.

    ```
    adl://<data_lake_account>.azuredatalakestore.net/<cluster_root_path>/<file_path>
    ```

* **줄인 경로 형식 사용**. 이 방식의 경우 클러스터 루트에 대한 경로를 다음으로 대체합니다.

    ```
    adl:///<file path>
    ```

* **상대 경로 사용**. 이 방법의 경우 액세스할 파일에 대한 상대 경로만 제공합니다.

    ```
    /<file.path>/
    ```

### <a name="data-access-examples"></a>데이터 액세스 예제

예제는 클러스터의 헤드 노드에 대한 [ssh 연결](./hdinsight-hadoop-linux-use-ssh-unix.md)을 기반으로 합니다. 이 예제에서는 세 가지 URI 스키마를 모두 사용합니다. `DATALAKEACCOUNT` 및 `CLUSTERNAME`를 관련 값으로 바꿉니다.

#### <a name="a-few-hdfs-commands"></a>몇 가지 hdfs 명령

1. 로컬 스토리지에 파일을 만듭니다.

    ```bash
    touch testFile.txt
    ```

1. 클러스터 스토리지에 디렉터리를 만듭니다.

    ```bash
    hdfs dfs -mkdir adl://DATALAKEACCOUNT.azuredatalakestore.net/clusters/CLUSTERNAME/sampledata1/
    hdfs dfs -mkdir adl:///sampledata2/
    hdfs dfs -mkdir /sampledata3/
    ```

1. 로컬 스토리지에서 클러스터 스토리지로 데이터를 복사합니다.

    ```bash
    hdfs dfs -copyFromLocal testFile.txt adl://DATALAKEACCOUNT.azuredatalakestore.net/clusters/CLUSTERNAME/sampledata1/
    hdfs dfs -copyFromLocal testFile.txt adl:///sampledata2/
    hdfs dfs -copyFromLocal testFile.txt /sampledata3/
    ```

1. 클러스터 스토리지의 디렉터리 콘텐츠를 나열합니다.

    ```bash
    hdfs dfs -ls adl://DATALAKEACCOUNT.azuredatalakestore.net/clusters/CLUSTERNAME/sampledata1/
    hdfs dfs -ls adl:///sampledata2/
    hdfs dfs -ls /sampledata3/
    ```

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
LOCATION 'adl://DATALAKEACCOUNT.azuredatalakestore.net/clusters/CLUSTERNAME/example/data/';
LOCATION 'adl:///example/data/';
LOCATION '/example/data/';
```

## <a name="identify-storage-path-from-ambari"></a>Ambari에서 스토리지 경로 식별

구성된 기본 저장소의 전체 경로를 식별하려면 **HDFS** > **구성** 으로 이동하여 필터 입력 상자에 `fs.defaultFS`를 입력합니다.

## <a name="create-hdinsight-clusters-with-access-to-data-lake-storage-gen1"></a>Data Lake Storage Gen1에 대한 액세스로 HDInsight 클러스터 만들기

Data Lake Storage Gen1에 대한 액세스로 HDInsight 클러스터를 만드는 방법에 대한 자세한 지침은 다음 링크를 사용하세요.

* [포털 사용](./hdinsight-hadoop-provision-linux-clusters.md)
* [PowerShell 사용(Data Lake Storage Gen1을 기본 스토리지로)](../data-lake-store/data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md)
* [PowerShell 사용(Data Lake Storage Gen1을 추가 스토리지로)](../data-lake-store/data-lake-store-hdinsight-hadoop-use-powershell.md)
* [Azure 템플릿 사용](../data-lake-store/data-lake-store-hdinsight-hadoop-use-resource-manager-template.md)

## <a name="refresh-the-hdinsight-certificate-for-data-lake-storage-gen1-access"></a>Data Lake Storage Gen1 액세스에 대한 HDInsight 인증서 새로 고침

다음 예제 PowerShell 코드는 로컬 파일 또는 Azure Key Vault에서 인증서를 읽고, Azure Data Lake Storage Gen1에 액세스하도록 HDInsight 클러스터를 새 인증서로 업데이트합니다. 고유한 HDInsight 클러스터 이름, 리소스 그룹 이름, 구독 ID, `app ID`, 인증서의 로컬 경로를 제공합니다. 메시지가 표시되면 암호를 입력합니다.

```powershell-interactive
$clusterName = '<clustername>'
$resourceGroupName = '<resourcegroupname>'
$subscriptionId = '01234567-8a6c-43bc-83d3-6b318c6c7305'
$appId = '01234567-e100-4118-8ba6-c25834f4e938'
$addNewCertKeyCredential = $true
$certFilePath = 'C:\localfolder\adls.pfx'
$KeyVaultName = "my-key-vault-name"
$KeyVaultSecretName = "my-key-vault-secret-name"
$certPassword = Read-Host "Enter Certificate Password"
# certSource
# 0 - create self signed cert
# 1 - read cert from file path
# 2 - read cert from key vault
$certSource = 0

Login-AzAccount
Select-AzSubscription -SubscriptionId $subscriptionId

if($certSource -eq 0)
{
    Write-Host "Generating new SelfSigned certificate"

    $cert = New-SelfSignedCertificate -CertStoreLocation "cert:\CurrentUser\My" -Subject "CN=hdinsightAdlsCert" -KeySpec KeyExchange
    $certBytes = $cert.Export([System.Security.Cryptography.X509Certificates.X509ContentType]::Pkcs12, $certPassword);
    $certString = [System.Convert]::ToBase64String($certBytes)
}
elseif($certSource -eq 1)
{

    Write-Host "Reading the cert file from path $certFilePath"

    $cert = new-object System.Security.Cryptography.X509Certificates.X509Certificate2($certFilePath, $certPassword)
    $certString = [System.Convert]::ToBase64String([System.IO.File]::ReadAllBytes($certFilePath))
}
elseif($certSource -eq 2)
{

    Write-Host "Reading the cert file from Azure Key Vault $KeyVaultName"

    $cert = (Get-AzureKeyVaultSecret -VaultName $KeyVaultName -Name $KeyVaultSecretName)
    $certValue = [System.Convert]::FromBase64String($cert.SecretValueText)
    $certObject = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2 -ArgumentList $certValue, $null,"Exportable, PersistKeySet"

    $certBytes = $certObject.Export([System.Security.Cryptography.X509Certificates.X509ContentType]::Pkcs12, $certPassword.SecretValueText);

    $certString =[System.Convert]::ToBase64String($certBytes)
}

if($addNewCertKeyCredential)
{
    Write-Host "Creating new KeyCredential for the app"
    $keyValue = [System.Convert]::ToBase64String($cert.GetRawCertData())
    New-AzADAppCredential -ApplicationId $appId -CertValue $keyValue -EndDate $cert.NotAfter -StartDate $cert.NotBefore
    Write-Host "Waiting for 7 minutes for the permissions to get propagated"
    Start-Sleep -s 420 #7 minutes
}

Write-Host "Updating the certificate on HDInsight cluster..."

Invoke-AzResourceAction `
    -ResourceGroupName $resourceGroupName `
    -ResourceType 'Microsoft.HDInsight/clusters' `
    -ResourceName $clusterName `
    -ApiVersion '2015-03-01-preview' `
    -Action 'updateclusteridentitycertificate' `
    -Parameters @{ ApplicationId = $appId; Certificate = $certString; CertificatePassword = $certPassword.ToString() } `
    -Force
```

## <a name="next-steps"></a>다음 단계

이 문서에서는 HDInsight로 HDFS 호환 Azure Data Lake Storage Gen1을 사용하는 방법을 알아보았습니다. 이 스토리지를 사용하면 적응형 장기 보관 데이터 획득 솔루션을 빌드할 수 있습니다. 또한 저장된 구조적 및 비구조적 데이터 내부의 정보를 잠금 해제하는 데 HDInsight를 사용할 수 있습니다.

자세한 내용은 다음을 참조하세요.

* [빠른 시작: HDInsight에서 클러스터 설정](./hdinsight-hadoop-provision-linux-clusters.md)
* [Azure PowerShell을 사용하여 Data Lake Storage Gen1을 사용하는 HDInsight 클러스터 만들기](../data-lake-store/data-lake-store-hdinsight-hadoop-use-powershell.md)
* [HDInsight에 데이터 업로드](hdinsight-upload-data.md)
* [Azure Blob Storage 공유 액세스 서명을 사용하여 HDInsight에서 데이터 액세스 제한](hdinsight-storage-sharedaccesssignature-permissions.md)
