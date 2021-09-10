---
title: HDInsight에 추가 Azure Storage 계정 추가
description: 기존 HDInsight 클러스터에 추가 Azure Storage 계정을 추가하는 방법을 알아봅니다.
ms.service: hdinsight
ms.topic: how-to
ms.custom: seoapr2020
ms.date: 04/27/2020
ms.openlocfilehash: 86fa4eb83592d5c9927139c096a60c2ebc4efbc0
ms.sourcegitcommit: cc099517b76bf4b5421944bd1bfdaa54153458a0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/09/2021
ms.locfileid: "113552085"
---
# <a name="add-additional-storage-accounts-to-hdinsight"></a>HDInsight에 추가 스토리지 계정 추가

스크립트 동작을 사용하여 HDInsight에 추가 Azure Storage ‘계정’을 추가하는 방법을 알아봅니다. 이 문서의 단계는 기존 HDInsight 클러스터에 스토리지 ‘계정’을 추가합니다. 이 문서는 스토리지 ‘계정’(기본 클러스터 스토리지 계정이 아님)에 적용되며, [`Azure Data Lake Storage Gen1`](hdinsight-hadoop-use-data-lake-storage-gen1.md) 및 [`Azure Data Lake Storage Gen2`](hdinsight-hadoop-use-data-lake-storage-gen2.md) 같은 추가 스토리지에는 적용되지 않습니다.

> [!IMPORTANT]  
> 이 문서의 정보는 클러스터를 만든 후 클러스터에 추가 스토리지 계정을 추가하는 방법에 대한 것입니다. 클러스터를 만드는 동안 스토리지 계정을 추가하는 방법에 대한 자세한 내용은 [Apache Hadoop, Apache Spark, Apache Kafka 등으로 HDInsight에서 클러스터 설정](hdinsight-hadoop-provision-linux-clusters.md)을 참조하세요.

## <a name="prerequisites"></a>필수 구성 요소

* HDInsight의 Hadoop 클러스터 [Linux에서 HDInsight 시작](./hadoop/apache-hadoop-linux-tutorial-get-started.md)을 참조하세요.
* 스토리지 계정 이름 및 키. [스토리지 계정 액세스 키 관리](../storage/common/storage-account-keys-manage.md)를 참조하세요.
* PowerShell을 사용하는 경우 AZ 모듈이 필요합니다.  [Azure PowerShell 개요](/powershell/azure/)를 참조하세요.

## <a name="how-it-works"></a>작동 방법

처리하는 동안 스크립트는 다음 작업을 수행합니다.

* 클러스터에 대한 core-site.xml 구성에 스토리지 계정이 이미 있는 경우 스크립트가 종료되고 추가 작업이 수행되지 않습니다.

* 스토리지 계정이 있고 키를 사용하여 액세스할 수 있는지 확인합니다.

* 클러스터 자격 증명을 사용하여 키를 암호화합니다.

* core-site.xml 파일에 스토리지 계정을 추가합니다.

* Apache Oozie, Apache Hadoop YARN, Apache Hadoop MapReduce2 및 Apache Hadoop HDFS 서비스를 중지하고 다시 시작합니다. 이러한 서비스를 중지하고 시작하면 서비스에서 새 스토리지 계정을 사용할 수 있습니다.

> [!WARNING]  
> HDInsight 클러스터와 다른 위치에서는 스토리지 계정을 사용할 수 없습니다.

## <a name="add-storage-account"></a>스토리지 계정 추가

[스크립트 작업](hdinsight-hadoop-customize-cluster-linux.md#script-action-to-a-running-cluster)을 사용하여 다음 사항을 고려하여 변경 내용을 적용합니다.

|속성 | 값 |
|---|---|
|Bash 스크립트 URI|`https://hdiconfigactions.blob.core.windows.net/linuxaddstorageaccountv01/add-storage-account-v01.sh`|
|노드 유형|Head|
|매개 변수|`ACCOUNTNAME` `ACCOUNTKEY` `-p`(선택 사항)|

* `ACCOUNTNAME`은 HDInsight 클러스터에 추가할 스토리지 계정의 이름입니다.
* `ACCOUNTKEY`는 `ACCOUNTNAME`에 대한 액세스 키입니다.
* `-p`는 선택 사항입니다. 지정된 경우 키가 암호화되지 않고 core-site.xml 파일에 일반 텍스트로 저장됩니다.

## <a name="verification"></a>확인

Azure Portal에서 HDInsight 클러스터를 볼 때 __속성__ 에서 __스토리지 계정__ 항목을 선택하면 이 스크립트 동작을 통해 추가된 스토리지 계정이 표시되지 않습니다. Azure PowerShell 및 Azure CLI에도 추가 스토리지 계정이 표시되지 않습니다. 스크립트는 클러스터에 대한 `core-site.xml` 구성만 수정하므로 스토리지 정보는 표시되지 않습니다. 이 정보는 Azure 관리 API를 사용하여 클러스터 정보를 검색할 때 사용되지 않습니다.

추가 스토리지를 확인하려면 아래에 표시된 방법 중 하나를 사용합니다.

### <a name="powershell"></a>PowerShell

이 스크립트는 지정된 클러스터와 연결된 스토리지 계정 이름을 반환합니다. `CLUSTERNAME`을 실제 클러스터 이름으로 바꾸고 스크립트를 실행합니다.

```powershell
# Update values
$clusterName = "CLUSTERNAME"

$creds = Get-Credential -UserName "admin" -Message "Enter the cluster login credentials"

$clusterName = $clusterName.ToLower();

# getting service_config_version
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName`?fields=Clusters/desired_service_config_versions/HDFS" `
    -Credential $creds -UseBasicParsing
$respObj = ConvertFrom-Json $resp.Content

$configVersion=$respObj.Clusters.desired_service_config_versions.HDFS.service_config_version

$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations/service_config_versions?service_name=HDFS&service_config_version=$configVersion" `
    -Credential $creds
$respObj = ConvertFrom-Json $resp.Content

# extract account names
$value = ($respObj.items.configurations | Where type -EQ "core-site").properties | Get-Member -membertype properties | Where Name -Like "fs.azure.account.key.*"
foreach ($name in $value ) { $name.Name.Split(".")[4]}
```

### <a name="apache-ambari"></a>Apache Ambari

1. 웹 브라우저에서 `https://CLUSTERNAME.azurehdinsight.net`로 이동합니다. 여기서 `CLUSTERNAME`은 클러스터의 이름입니다.

1. **HDFS** > **구성** > **고급** > **사용자 지정 core-site** 로 이동합니다.

1. `fs.azure.account.key`로 시작하는 키를 관찰합니다. 계정 이름은 다음 샘플 이미지에 표시된 것처럼 키의 일부가 됩니다.

   :::image type="content" source="./media/hdinsight-hadoop-add-storage/apache-ambari-verification.png" alt-text="Apache Ambari를 통한 확인":::

## <a name="remove-storage-account"></a>스토리지 계정 제거

1. 웹 브라우저에서 `https://CLUSTERNAME.azurehdinsight.net`로 이동합니다. 여기서 `CLUSTERNAME`은 클러스터의 이름입니다.

1. **HDFS** > **구성** > **고급** > **사용자 지정 core-site** 로 이동합니다.

1. 다음 키를 제거합니다.
    * `fs.azure.account.key.<STORAGE_ACCOUNT_NAME>.blob.core.windows.net`
    * `fs.azure.account.keyprovider.<STORAGE_ACCOUNT_NAME>.blob.core.windows.net`

해당 키를 제거하고 구성을 저장한 후에는 Oozie, Yarn, MapReduce2, HDFS, Hive를 하나씩 다시 시작해야 합니다.

## <a name="known-issues"></a>알려진 문제

### <a name="storage-firewall"></a>스토리지 방화벽

**선택한 네트워크** 에 **방화벽 및 가상 네트워크** 제한을 적용하여 스토리지 계정을 보호하도록 선택하는 경우 HDInsight에서 스토리지 계정에 액세스할 수 있도록 **신뢰할 수 있는 Microsoft 서비스 허용** 예외를 사용해야 합니다`.`

### <a name="unable-to-access-storage-after-changing-key"></a>키를 변경한 후 스토리지에 액세스할 수 없음

스토리지 계정의 키를 변경하면 HDInsight에서 더 이상 스토리지 계정에 액세스할 수 없습니다. HDInsight에서는 클러스터에 대한 core-site.xml에서 키의 캐시된 복사본을 사용합니다. 이 캐시된 복사본은 새 키와 일치하도록 업데이트해야 합니다.

스크립트는 스토리지 계정에 대한 항목이 이미 있는지 확인하므로 스크립트 동작을 다시 실행해도 키가 업데이트되지 **않습니다**. 항목이 이미 있는 경우 변경하지 않습니다.

이 문제를 해결하려면 다음을 수행합니다.

* 액세스 키를 회전하는 방법은 [스토리지 계정 액세스 키 업데이트](hdinsight-rotate-storage-keys.md)를 참조하세요.

* 스토리지 계정을 제거한 다음 스토리지 계정을 다시 추가할 수도 있습니다.

## <a name="next-steps"></a>다음 단계

기존 HDInsight 클러스터에 추가 스토리지 계정을 추가하는 방법을 살펴보았습니다. 스크립트 동작에 대한 자세한 내용은 [스크립트 동작을 사용하여 Linux 기반 HDInsight 클러스터 사용자 지정](hdinsight-hadoop-customize-cluster-linux.md)를 참조하세요.