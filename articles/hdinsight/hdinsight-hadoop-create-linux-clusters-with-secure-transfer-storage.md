---
title: Apache Hadoop 및 보안 전송 스토리지 - Azure HDInsight
description: 보안 전송이 활성화된 Azure Storage 계정을 사용하여 HDInsight 클러스터를 만드는 방법을 알아봅니다.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 02/18/2020
ms.openlocfilehash: deb10f2b3e4e2b5e7d911992a601f66e1e557268
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/29/2021
ms.locfileid: "129211654"
---
# <a name="apache-hadoop-clusters-with-secure-transfer-storage-accounts-in-azure-hdinsight"></a>Azure HDInsight에서 보안 전송 스토리지 계정을 사용하는 Apache Hadoop 클러스터

[보안 전송 필요](../storage/common/storage-require-secure-transfer.md) 기능은 보안 연결을 통해 모든 요청을 계정에 적용하여 Azure Storage 계정의 보안을 강화합니다. 이 기능과 wasbs 체계는 HDInsight 클러스터 버전 3.6 이상에서만 지원됩니다.

> [!IMPORTANT]
> 클러스터를 만든 후 보안 스토리지 전송을 사용하도록 설정하면 스토리지 계정을 사용하여 오류가 발생할 수 있으므로 권장하지 않습니다. 보안 전송을 이미 사용하는 것으로 설정된 스토리지 계정으로 새 클러스터를 만드는 것이 좋습니다.

## <a name="storage-accounts"></a>Storage 계정

### <a name="azure-portal"></a>Azure Portal

기본적으로 보안 전송 필요 속성은 Azure Portal에서 스토리지 계정을 만들 때 사용하도록 설정됩니다.

Azure Portal로 기존 스토리지 계정을 업데이트하려면 [Azure Portal에서 보안 전송 필요](../storage/common/storage-require-secure-transfer.md#require-secure-transfer-for-an-existing-storage-account)를 참조하세요.

### <a name="powershell"></a>PowerShell

PowerShell cmdlet [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount)의 경우 매개 변수 `-EnableHttpsTrafficOnly`가 `1`로 설정되어 있는지 확인합니다.

PowerShell로 기존 스토리지 계정을 업데이트하려면 [PowerShell에서 보안 전송 필요](../storage/common/storage-require-secure-transfer.md#require-secure-transfer-with-powershell)를 참조하세요.

### <a name="azure-cli"></a>Azure CLI

Azure CLI 명령 [az storage account create](/cli/azure/storage/account#az_storage_account_create)의 경우 매개 변수 `--https-only`가 `true`로 설정되어 있는지 확인합니다.

Azure CLI로 기존 스토리지 계정을 업데이트하려면 [에서 보안 전송 필요](../storage/common/storage-require-secure-transfer.md#require-secure-transfer-with-azure-cli)를 참조하세요.

### <a name="secure-transfer-errors"></a>보안 전송 오류


HDInsight 클러스터를 만든 후 실수로 '보안 전송 필요' 옵션을 사용하도록 설정한 경우 다음과 같은 오류 메시지가 표시될 수 있습니다.

`com.microsoft.azure.storage.StorageException: The account being accessed does not support http.`

Hbase 클러스터의 경우에만 다음 단계를 시도하여 클러스터 기능을 복원할 수 있습니다.
1. Ambari에서 HBase를 중지합니다.
2. Ambari에서 HDFS를 중지합니다.
3. Ambari에서 HDFS --> Configs --> Advanced --> fs.defaultFS로 이동합니다.
4. wasb를 wasbs로 변경하고 저장합니다.
5. 가속 쓰기 기능을 사용하는 경우 hbase 구성 아래의 'hbase.rootDir'도 wasb에서 wasbs로 변경해야 합니다.
6. 필요한 모든 서비스를 다시 시작합니다.

## <a name="add-additional-storage-accounts"></a>추가 스토리지 계정 추가

보안 전송이 활성화된 스토리지 계정을 추가할 수 있는 여러 옵션이 있습니다.

* 마지막 섹션에서 Azure Resource Manager 템플릿을 수정합니다.
* [Azure Portal](https://portal.azure.com)을 사용하여 클러스터를 만들고 연결된 스토리지 계정을 지정합니다.
* 스크립트 동작을 사용하여 보안 전송이 활성화된 스토리지 계정을 기존 HDInsight 클러스터에 추가합니다. 자세한 내용은 [HDInsight에 추가 스토리지 계정 추가](hdinsight-hadoop-add-storage.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

* 기본 데이터 저장소로 [Apache Hadoop HDFS](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsUserGuide.html) 대신 Azure Storage(WASB) 사용
* HDInsight에서 Azure Storage를 사용하는 방법에 대한 자세한 내용은 [HDInsight에서 Azure Storage 사용](hdinsight-hadoop-use-blob-storage.md)을 참조하세요.
* 데이터를 HDInsight로 업로드하는 방법에 대한 정보는 [HDInsight에 데이터 업로드](hdinsight-upload-data.md)를 참조하세요.
