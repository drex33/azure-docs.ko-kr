---
title: Azure HDInsight에서 Azure Storage 계정 액세스 키 업데이트
description: Azure HDInsight 클러스터에서 Azure Storage 계정 액세스 키를 업데이트하는 방법을 알아봅니다.
ms.service: hdinsight
ms.topic: how-to
ms.date: 06/29/2021
ms.openlocfilehash: 9adfb5e438aec8625cd7c4b164f5999181d9c31f
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122566312"
---
# <a name="update-azure-storage-account-access-keys-in-hdinsight-cluster"></a>HDInsight 클러스터에서 Azure 스토리지 계정 액세스 키 업데이트

이 문서에서는 Azure HDInsight에서 기본 또는 보조 스토리지 계정에 대한 Azure Storage 계정 액세스 키를 순환시키는 방법을 알아봅니다.

>[!CAUTION]
> 스토리지 쪽에서 액세스 키를 직접 순환시키면 HDInsight 클러스터에 액세스할 수 없습니다.

## <a name="prerequisites"></a>필수 구성 요소

* 프로세스 전반에 걸쳐 HDInsight 클러스터에 액세스할 수 있도록 하기 위해 스토리지 계정의 기본 및 보조 액세스 키를 시차를 두고 교대로 순환시키는 방식을 사용할 것입니다.

    다음은 기본 및 보조 스토리지 액세스 키를 사용하고 이에 대한 순환 정책을 설정하는 방법에 대한 예입니다.
    1. HDInsight 클러스터를 만들 때 스토리지 계정에서 액세스 키1을 사용합니다.
    1. N일마다 액세스 키2에 대한 순환 정책을 설정합니다. 이 순환의 일부로 HDInsight가 액세스 키1을 사용한 다음 스토리지 계정에서 액세스 키2를 순환시키도록 업데이트합니다.
    1. N/2일마다 액세스 키1에 대한 순환 정책을 설정합니다. 이 순환의 일부로 HDInsight가 액세스 키 2를 사용한 다음 스토리지 계정에서 액세스 키 1을 순환시키도록 업데이트합니다.
    1. 위의 접근 방식을 사용하면 액세스 키 1이 N/2, 3N/2 등으로 순환되고 액세스 키 2가 N, 2N, 3N 등으로 순환됩니다.

* 스토리지 계정 키의 주기적 순환을 설정하려면 [비밀 순환 자동화](../key-vault/secrets/tutorial-rotation-dual.md)를 참조하세요.

## <a name="update-storage-account-access-keys"></a>스토리지 계정 액세스 키 업데이트

[스크립트 작업](hdinsight-hadoop-customize-cluster-linux.md#script-action-to-a-running-cluster)을 사용하여 다음을 고려하여 키를 업데이트합니다.

|속성 | 값 |
|---|---|
|Bash 스크립트 URI|`https://hdiconfigactions.blob.core.windows.net/linuxaddstorageaccountv01/update-storage-account-v01.sh`|
|노드 유형|Head|
|매개 변수|`ACCOUNTNAME` `ACCOUNTKEY` `-p`(선택 사항)|

* `ACCOUNTNAME`은 HDInsight 클러스터의 스토리지 계정 이름입니다.
* `ACCOUNTKEY`는 `ACCOUNTNAME`에 대한 액세스 키입니다.
* `-p`는 선택 사항입니다. 지정된 경우 키가 암호화되지 않고 core-site.xml 파일에 일반 텍스트로 저장됩니다.

## <a name="known-issues"></a>알려진 문제

앞의 스크립트는 클러스터 쪽에서만 액세스 키를 직접 업데이트하고 HDInsight 리소스 공급자 쪽에서 복사본을 갱신하지 않습니다. 따라서 액세스 키가 순환된 후 스토리지 계정에서 호스팅되는 스크립트 작업이 실패합니다.

해결 방법: 스크립트 작업에 [SAS URI](hdinsight-storage-sharedaccesssignature-permissions.md)를 사용하거나 스크립트를 공개적으로 액세스할 수 있도록 합니다.

## <a name="next-steps"></a>다음 단계

* [추가 스토리지 계정 추가](hdinsight-hadoop-add-storage.md)