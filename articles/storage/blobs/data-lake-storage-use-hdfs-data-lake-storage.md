---
title: Azure Data Lake Storage Gen2에서 HDFS CLI 사용
description: Azure Data Lake Storage Gen2용 HDFS(Hadoop 분산 파일 시스템) CLI를 사용합니다. 컨테이너를 만들고, 파일 또는 디렉터리 목록을 얻는 등의 기능을 지원합니다.
services: storage
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 12/06/2018
ms.author: normesta
ms.subservice: data-lake-storage-gen2
ms.reviewer: artek
ms.openlocfilehash: 8861255cb81c1200108dff22354be7636c1bd2d5
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128633795"
---
# <a name="using-the-hdfs-cli-with-data-lake-storage-gen2"></a>Data Lake Storage Gen2에서 HDFS CLI 사용

[HDFS(Hadoop 분산 파일 시스템)와](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsDesign.html)마찬가지로 명령줄 인터페이스를 사용하여 스토리지 계정의 데이터에 액세스하고 관리할 수 있습니다. 이 문서에서는 시작하는 데 도움이 되는 몇 가지 예제를 제공합니다.

HDInsight는 컴퓨팅 노드에 로컬로 연결된 분산 컨테이너에 대한 액세스를 제공합니다. HDFS 및 Hadoop에서 지원하는 다른 파일 시스템과 직접 상호 작용하는 셸을 사용하여 이 컨테이너에 액세스할 수 있습니다.

HDFS CLI에 대한 자세한 내용은 [공식 설명서](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html) 및 [HDFS 권한 가이드를 참조하세요.](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsPermissionsGuide.html)

> [!NOTE]
> HDInsight 대신 Azure Databricks 사용하고 명령줄 인터페이스를 사용하여 데이터와 상호 작용하려는 경우 Databricks CLI를 사용하여 Databricks 파일 시스템과 상호 작용할 수 있습니다. [Databricks CLI 를](/azure/databricks/dev-tools/cli/)참조하세요.

## <a name="use-the-hdfs-cli-with-an-hdinsight-hadoop-cluster-on-linux"></a>Linux에서 HDInsight Hadoop 클러스터로 HDFS CLI 사용

먼저 [서비스에 원격 액세스](../../hdinsight/hdinsight-hadoop-linux-information.md#remote-access-to-services)를 확인합니다. [SSH](../../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md)를 선택하면 샘플 PowerShell 코드가 다음과 같이 표시합니다.

```powershell
#Connect to the cluster via SSH.
ssh sshuser@clustername-ssh.azurehdinsight.net
#Execute basic HDFS commands. Display the hierarchy.
hdfs dfs -ls /
#Create a sample directory.
hdfs dfs -mkdir /samplefolder
```

Azure Portal에 있는 HDInsight 클러스터 블레이드의 “SSH + 클러스터 로그인” 섹션에서 연결 문자열을 찾을 수 있습니다. SSH 자격 증명은 클러스터 생성 시 지정되었습니다.

> [!IMPORTANT]
> 클러스터가 만들어지면 HDInsight 클러스터 요금 청구가 시작되고 클러스터가 삭제되면 요금 청구가 중지됩니다. 분 단위로 청구되므로 더 이상 사용하지 않으면 항상 클러스터를 삭제해야 합니다. 클러스터를 삭제하는 방법은 [토픽에 대한 문서](../../hdinsight/hdinsight-delete-cluster.md)를 참조하세요. 그러나 Data Lake Storage Gen2가 사용되는 스토리지 계정에 저장된 데이터는 HDInsight 클러스터가 삭제된 후에도 유지됩니다.

## <a name="create-a-container"></a>컨테이너 만들기

`hdfs dfs -D "fs.azure.createRemoteFileSystemDuringInitialization=true" -ls abfs://<container-name>@<storage-account-name>.dfs.core.windows.net/`

- `<container-name>` 자리 표시자를 컨테이너에 지정할 이름으로 바꿉니다.

- `<storage-account-name>` 자리 표시자를 스토리지 계정 이름으로 바꿉니다.

## <a name="get-a-list-of-files-or-directories"></a>파일 또는 디렉터리 목록 가져오기

`hdfs dfs -ls <path>`

자리 `<path>` 표시자를 컨테이너 또는 컨테이너 폴더의 URI로 대체합니다.

예를 들면 다음과 같습니다. `hdfs dfs -ls abfs://my-file-system@mystorageaccount.dfs.core.windows.net/my-directory-name`

## <a name="create-a-directory"></a>디렉터리 만들기

`hdfs dfs -mkdir [-p] <path>`

자리 `<path>` 표시자를 루트 컨테이너 이름 또는 컨테이너 내의 폴더로 대체합니다.

예를 들면 다음과 같습니다. `hdfs dfs -mkdir abfs://my-file-system@mystorageaccount.dfs.core.windows.net/`

## <a name="delete-a-file-or-directory"></a>파일 또는 디렉터리 삭제

`hdfs dfs -rm <path>`

`<path>` 자리 표시자를 삭제하려는 파일 또는 폴더의 URI로 바꿉니다.

예를 들면 다음과 같습니다. `hdfs dfs -rmdir abfs://my-file-system@mystorageaccount.dfs.core.windows.net/my-directory-name/my-file-name`

## <a name="display-the-access-control-lists-acls-of-files-and-directories"></a>파일 및 디렉터리의 Access Control Lists(ACL) 표시

`hdfs dfs -getfacl [-R] <path>`

예제:

`hdfs dfs -getfacl -R /dir`

[getfacl](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html#getfacl) 참조

## <a name="set-acls-of-files-and-directories"></a>파일 및 디렉터리의 ACL 설정

`hdfs dfs -setfacl [-R] [-b|-k -m|-x <acl_spec> <path>]|[--set <acl_spec> <path>]`

예제:

`hdfs dfs -setfacl -m user:hadoop:rw- /file`

[setfacl](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html#setfacl) 참조

## <a name="change-the-owner-of-files"></a>파일의 소유자 변경

`hdfs dfs -chown [-R] <new_owner>:<users_group> <URI>`

[chown](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html#chown) 참조

## <a name="change-group-association-of-files"></a>파일의 그룹 연결 변경

`hdfs dfs -chgrp [-R] <group> <URI>`

[chgrp](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html#chgrp) 참조

## <a name="change-the-permissions-of-files"></a>파일의 사용 권한 변경

`hdfs dfs -chmod [-R] <mode> <URI>`

[chmod](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html#chmod)를 참조합니다.

[Apache Hadoop 2.4.1 파일 시스템 셸 가이드](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html) 웹 사이트에서 명령의 전체 목록을 볼 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [Azure Databricks에서 Azure Data Lake Storage Gen2 지원 계정 사용](./data-lake-storage-use-databricks-spark.md)

- [파일 및 디렉터리에 대한 액세스 제어 목록에 대해 알아보기](./data-lake-storage-access-control.md)
