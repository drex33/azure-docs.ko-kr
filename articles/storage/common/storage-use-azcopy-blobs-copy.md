---
title: AzCopy v10을 사용하여 Azure Storage 계정 간 blob 복사 | Microsoft Docs
description: 이 문서에는 스토리지 계정 간에 blob을 복사하는 데 유용한 AzCopy 예제 명령의 컬렉션이 포함되어 있습니다.
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 04/02/2021
ms.author: normesta
ms.subservice: common
ms.reviewer: dineshm
ms.openlocfilehash: 1effb888e1210b431817be2ccf05ddc7d521362d
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128636724"
---
# <a name="copy-blobs-between-azure-storage-accounts-by-using-azcopy"></a>AzCopy를 사용하여 Azure Storage 계정 간에 Blob 복사

AzCopy v10 명령줄 유틸리티를 사용하여 스토리지 계정 간에 Blob, 디렉터리, 컨테이너를 복사할 수 있습니다.

파일 업로드, blob 다운로드, Blob Storage와 동기화와 같은 다른 작업 형식의 예를 보려면 이 문서의 [다음 단계](#next-steps) 섹션에 있는 링크를 참조하세요.

AzCopy는 [서버 간](/rest/api/storageservices/put-block-from-url) [API](/rest/api/storageservices/put-page-from-url)를 사용하므로 스토리지 서버 간에 데이터가 직접 복사됩니다. 이 복사 작업은 컴퓨터의 네트워크 대역폭을 사용하지 않습니다.

AzCopy를 다운로드하고 스토리지 서비스에 인증 자격 증명을 제공하는 방법에 관해 알아보려면 [AzCopy 시작](storage-use-azcopy-v10.md)을 참조하세요.

## <a name="guidelines"></a>지침

AzCopy 명령에 다음 지침을 적용합니다.

- 클라이언트는 원본 스토리지 계정과 대상 스토리지 계정 모두에 대한 네트워크 액세스 권한이 있어야 합니다. 각 스토리지 계정의 네트워크 설정을 구성하는 방법에 관한 내용은 [Azure Storage 방화벽 및 가상 네트워크 구성](storage-network-security.md?toc=/azure/storage/blobs/toc.json)을 참조하세요.

- 각 원본 URL에 SAS 토큰을 추가합니다.

  Azure AD(Azure Active Directory)를 사용하여 권한 부여 자격 증명을 제공하는 경우 대상 URL에서만 SAS 토큰을 생략할 수 있습니다. 대상 계정에 적절한 역할을 설정했는지 확인합니다. [옵션 1: Azure Active Directory 사용](storage-use-azcopy-v10.md?toc=/azure/storage/blobs/toc.json#option-1-use-azure-active-directory)을 참조하세요.

  이 문서의 예제에서는 Azure AD를 사용하여 ID를 인증했다고 가정하므로, 예제에서는 대상 URL에서 SAS 토큰을 생략합니다.

-  프리미엄 블록 Blob 스토리지 계정에 복사하는 경우 `s2s-preserve-access-tier`를 `false`(예: `--s2s-preserve-access-tier=false`)로 설정하여 복사 작업에서 Blob의 액세스 계층을 생략합니다. 프리미엄 블록 blob 저장소 계정은 액세스 계층을 지원하지 않습니다.

- 계층 구조 네임스페이스가 있는 계정으로/에서 복사하는 경우 URL 구문에 `dfs.core.windows.net` 대신 `blob.core.windows.net`을 사용합니다. [Data Lake Storage에 대한 다중 프로토콜 액세스](../blobs/data-lake-storage-multi-protocol-access.md)를 사용하면 `blob.core.windows.net`을 사용할 수 있으며, 이 구문은 계정 간 복사 시나리오에 지원되는 유일한 구문입니다.

- `AZCOPY_CONCURRENCY_VALUE` 환경 변수의 값을 설정하여 복사 작업의 처리량을 늘릴 수 있습니다. 자세히 알아보려면 [동시성 향상](storage-use-azcopy-optimize.md#increase-concurrency)을 참조하세요.

- 원본 blob에 인덱스 태그가 있으며 해당 태그를 유지하려면 대상 blob에 다시 적용해야 합니다. 인덱스 태그를 설정하는 방법에 관한 내용은 이 문서의 [인덱스 태그를 사용하여 다른 스토리지 계정에 blob 복사](#copy-between-accounts-and-add-index-tags) 섹션을 참조하세요.

## <a name="copy-a-blob"></a>blob 복사

[azcopy copy](storage-ref-azcopy-copy.md) 명령을 사용하여 다른 스토리지 계정에 blob을 복사합니다.

> [!TIP]
> 이 예에서는 경로 인수를 작은따옴표(‘’)로 묶습니다. Windows 명령 셸(cmd.exe)을 제외하고 모든 명령 셸에서 작은따옴표를 사용합니다. Windows 명령 셸(cmd.exe)을 사용하는 경우 작은따옴표(‘’) 대신 큰따옴표(“”)로 경로 인수를 묶습니다.

**구문**

`azcopy copy 'https://<source-storage-account-name>.blob.core.windows.net/<container-name>/<blob-path><SAS-token>' 'https://<destination-storage-account-name>.blob.core.windows.net/<container-name>/<blob-path>'`

**예제**

```azcopy
azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer/myTextFile.txt?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer/myTextFile.txt'
```

복사 작업은 동기식이므로 명령이 반환될 때 모든 파일이 복사되었음을 나타냅니다.

## <a name="copy-a-directory"></a>디렉터리 복사

[azcopy copy](storage-ref-azcopy-copy.md) 명령을 사용하여 다른 스토리지 계정에 디렉터리를 복사합니다.

> [!TIP]
> 이 예에서는 경로 인수를 작은따옴표(‘’)로 묶습니다. Windows 명령 셸(cmd.exe)을 제외하고 모든 명령 셸에서 작은따옴표를 사용합니다. Windows 명령 셸(cmd.exe)을 사용하는 경우 작은따옴표(‘’) 대신 큰따옴표(“”)로 경로 인수를 묶습니다.

**구문**

`azcopy copy 'https://<source-storage-account-name>.blob.core.windows.net/<container-name>/<directory-path><SAS-token>' 'https://<destination-storage-account-name>.blob.core.windows.net/<container-name>' --recursive`

**예제**

```azcopy
azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer/myBlobDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer' --recursive
```

복사 작업은 동기식이므로 명령이 반환될 때 모든 파일이 복사되었음을 나타냅니다.

## <a name="copy-a-container"></a>컨테이너 복사

[azcopy copy](storage-ref-azcopy-copy.md) 명령을 사용하여 다른 스토리지 계정에 컨테이너를 복사합니다.

> [!TIP]
> 이 예에서는 경로 인수를 작은따옴표(‘’)로 묶습니다. Windows 명령 셸(cmd.exe)을 제외하고 모든 명령 셸에서 작은따옴표를 사용합니다. Windows 명령 셸(cmd.exe)을 사용하는 경우 작은따옴표(‘’) 대신 큰따옴표(“”)로 경로 인수를 묶습니다.

**구문**

`azcopy copy 'https://<source-storage-account-name>.blob.core.windows.net/<container-name><SAS-token>' 'https://<destination-storage-account-name>.blob.core.windows.net/<container-name>' --recursive`

**예제**

```azcopy
azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer' --recursive
```

복사 작업은 동기식이므로 명령이 반환될 때 모든 파일이 복사되었음을 나타냅니다.

## <a name="copy-containers-directories-and-blobs"></a>컨테이너, 디렉터리, blob 복사

[azcopy copy](storage-ref-azcopy-copy.md) 명령을 사용하여 다른 스토리지 계정에 모든 컨테이너, 디렉터리 및 blob을 복사합니다.

> [!TIP]
> 이 예에서는 경로 인수를 작은따옴표(‘’)로 묶습니다. Windows 명령 셸(cmd.exe)을 제외하고 모든 명령 셸에서 작은따옴표를 사용합니다. Windows 명령 셸(cmd.exe)을 사용하는 경우 작은따옴표(‘’) 대신 큰따옴표(“”)로 경로 인수를 묶습니다.

**구문**

`azcopy copy 'https://<source-storage-account-name>.blob.core.windows.net/<SAS-token>' 'https://<destination-storage-account-name>.blob.core.windows.net/' --recursive`

**예제**

```azcopy
azcopy copy 'https://mysourceaccount.blob.core.windows.net/?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net' --recursive
```

복사 작업은 동기식이므로 명령이 반환될 때 모든 파일이 복사되었음을 나타냅니다.

<a id="copy-between-accounts-and-add-index-tags"></a>

## <a name="copy-blobs-and-add-index-tags"></a>blob 복사 및 인덱스 태그 추가

blob을 다른 스토리지 계정에 복사하고 [blob 인덱스 태그(미리 보기)](../blobs/storage-manage-find-blobs.md)를 대상 blob에 추가합니다.

Azure AD 권한을 사용하는 경우 보안 주체에 [Storage Blob 데이터 소유자](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) 역할이 할당되거나 사용자 지정 Azure 역할을 통해 `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags/write` [Azure 리소스 공급자 작업](../../role-based-access-control/resource-provider-operations.md#microsoftstorage)에 대한 사용 권한이 부여되어야 합니다. SAS(공유 액세스 서명) 토큰을 사용하는 경우 해당 토큰은 `t` SAS 권한을 통해 blob의 태그에 대한 액세스를 제공해야 합니다.

태그를 추가하려면 URL로 인코딩된 키-값 쌍과 함께 `--blob-tags` 옵션을 사용합니다.

예를 들어, `my tag` 키와 `my tag value` 값을 추가하려면 `--blob-tags='my%20tag=my%20tag%20value'`를 대상 매개 변수에 추가합니다.

앰퍼샌드(`&`)를 사용하여 여러 인덱스 태그를 구분합니다.  예를 들어, `my second tag` 키와 `my second tag value` 값을 추가하려는 경우 전체 옵션 문자열은 `--blob-tags='my%20tag=my%20tag%20value&my%20second%20tag=my%20second%20tag%20value'`입니다.

다음 예에서는 `--blob-tags` 옵션 사용 방법을 보여 줍니다.

> [!TIP]
> 이 예에서는 경로 인수를 작은따옴표(‘’)로 묶습니다. Windows 명령 셸(cmd.exe)을 제외하고 모든 명령 셸에서 작은따옴표를 사용합니다. Windows 명령 셸(cmd.exe)을 사용하는 경우 작은따옴표(‘’) 대신 큰따옴표(“”)로 경로 인수를 묶습니다.

**Blob 예**

```azcopy

azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer/myTextFile.txt?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer/myTextFile.txt' --blob-tags='my%20tag=my%20tag%20value&my%20second%20tag=my%20second%20tag%20value'
```

**디렉터리 예**

```azcopy
azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer/myBlobDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer' --recursive --blob-tags='my%20tag=my%20tag%20value&my%20second%20tag=my%20second%20tag%20value'
```

 **컨테이너 예**

```azcopy
azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer' --recursive --blob-tags='my%20tag=my%20tag%20value&my%20second%20tag=my%20second%20tag%20value'
```

**계정 예**

```azcopy
azcopy copy 'https://mysourceaccount.blob.core.windows.net/?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net' --recursive --blob-tags='my%20tag=my%20tag%20value&my%20second%20tag=my%20second%20tag%20value'
```

복사 작업은 동기식이므로 명령이 반환될 때 모든 파일이 복사되었음을 나타냅니다.

> [!NOTE]
> 원본의 디렉터리, 컨테이너 또는 계정을 지정하는 경우 대상에 복사되는 모든 Blob에는 명령에서 지정한 것과 같은 태그가 있습니다.

## <a name="copy-with-optional-flags"></a>선택적 플래그를 사용하여 복사

선택적 플래그를 사용하여 복사 작업을 조정할 수 있습니다. 다음은 몇 가지 예제입니다.

|시나리오|플래그|
|---|---|
|블록, 페이지 또는 추가 Blob으로 blob을 복사합니다.|**--blob-type**=\[BlockBlob\|PageBlob\|AppendBlob\]|
|특정 액세스 계층(예: 보관 계층)에 복사합니다.|**--block-blob-tier**=\[None\|Hot\|Cool\|Archive\]|
|자동으로 파일의 압축을 풉니다.|**--decompress**=\[gzip\|deflate\]|

전체 목록을 보려면 [옵션](storage-ref-azcopy-copy.md#options)을 참조하세요.

## <a name="next-steps"></a>다음 단계

다음 문서에서 더 많은 예제를 찾아보세요.

- [예제: 업로드](storage-use-azcopy-blobs-upload.md)
- [예제: 다운로드](storage-use-azcopy-blobs-download.md)
- [예제: 동기화](storage-use-azcopy-blobs-synchronize.md)
- [예제: Amazon S3 버킷](storage-use-azcopy-s3.md)
- [예: Google Cloud Storage](storage-use-azcopy-google-cloud.md)
- [예: Azure Files](storage-use-azcopy-files.md)
- [자습서: AzCopy를 사용하여 클라우드로 온-프레미스 데이터 마이그레이션](storage-use-azcopy-migrate-on-premises-data.md)

이러한 문서를 참조하여 설정을 구성하고, 성능을 최적화하고, 문제를 해결하세요.

- [AzCopy 구성 설정](storage-ref-azcopy-configuration-settings.md)
- [AzCopy의 성능 최적화](storage-use-azcopy-optimize.md)
- [로그 파일을 사용하여 Azure Storage의 AzCopy V10 문제 해결](storage-use-azcopy-configure.md)
