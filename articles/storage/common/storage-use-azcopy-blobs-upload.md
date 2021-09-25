---
title: AzCopy v10을 사용하여 Azure Blob Storage에 파일 업로드 | Microsoft Docs
description: 이 문서에는 Azure Blob Storage에 파일을 업로드하는 데 도움이 되는 AzCopy 예제 명령 컬렉션이 포함되어 있습니다.
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 04/02/2021
ms.author: normesta
ms.subservice: common
ms.reviewer: dineshm
ms.openlocfilehash: c1171ada070131477c06292628da6eca9ee9c2ec
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128590991"
---
# <a name="upload-files-to-azure-blob-storage-by-using-azcopy"></a>AzCopy를 사용하여 Azure Blob Storage에 파일 업로드

AzCopy v10 명령줄 유틸리티를 사용하여 파일과 디렉터리를 Blob Storage에 업로드할 수 있습니다.

Blob 다운로드, Blob Storage와 동기화 또는 계정 간 Blob 복사와 같은 다른 작업 형식의 예를 보려면 이 문서의 [다음 단계](#next-steps) 섹션에 있는 링크를 참조하세요.

## <a name="get-started"></a>시작

AzCopy를 다운로드하고 스토리지 서비스에 인증 자격 증명을 제공하는 방법에 관해 알아보려면 [AzCopy 시작](storage-use-azcopy-v10.md) 문서를 참조하세요.

> [!NOTE]
> 이 문서의 예제에서는 Azure AD(Azure Active Directory)를 사용하여 권한 부여 자격 증명을 제공했다고 가정합니다.
>
> SAS 토큰을 사용하여 blob 데이터에 대한 액세스 권한을 부여하려면 각 AzCopy 명령에서 해당 토큰을 리소스 URL에 추가할 수 있습니다. 예: `'https://<storage-account-name>.blob.core.windows.net/<container-name><SAS-token>'`

## <a name="create-a-container"></a>컨테이너 만들기

[azcopy make](storage-ref-azcopy-make.md) 명령을 사용하여 컨테이너를 만들 수 있습니다.

> [!TIP]
> 이 예에서는 경로 인수를 작은따옴표(‘’)로 묶습니다. Windows 명령 셸(cmd.exe)을 제외하고 모든 명령 셸에서 작은따옴표를 사용합니다. Windows 명령 셸(cmd.exe)을 사용하는 경우 작은따옴표(‘’) 대신 큰따옴표(“”)로 경로 인수를 묶습니다.

다음은 테이블 예제입니다.

**구문**

`azcopy make 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>'`

**예제**

```azcopy
https://mystorageaccount.blob.core.windows.net/mycontainer
```

**예제(계층 구조 네임스페이스)**

```azcopy
https://mystorageaccount.dfs.core.windows.net/mycontainer
```

자세한 참조 문서는 [azcopy make](storage-ref-azcopy-make.md)를 참조하세요.

## <a name="upload-a-file"></a>파일 업로드

[azcopy copy](storage-ref-azcopy-copy.md) 명령을 사용하여 파일을 업로드합니다.

> [!TIP]
> 이 예에서는 경로 인수를 작은따옴표(‘’)로 묶습니다. Windows 명령 셸(cmd.exe)을 제외하고 모든 명령 셸에서 작은따옴표를 사용합니다. Windows 명령 셸(cmd.exe)을 사용하는 경우 작은따옴표(‘’) 대신 큰따옴표(“”)로 경로 인수를 묶습니다.

**구문**

`azcopy copy '<local-file-path>' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<blob-name>'`

**예제**

```azcopy
azcopy copy 'C:\myDirectory\myTextFile.txt' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myTextFile.txt'
```

**예제(계층 구조 네임스페이스)**

```azcopy
azcopy copy 'C:\myDirectory\myTextFile.txt' 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myTextFile.txt'
```

파일 경로 또는 파일 이름의 어디서든 와일드카드 기호(*)를 사용하여 파일을 업로드할 수도 있습니다. 예: `'C:\myDirectory\*.txt'` 또는 `C:\my*\*.txt`.

## <a name="upload-a-directory"></a>디렉터리 업로드

[azcopy copy](storage-ref-azcopy-copy.md) 명령을 사용하여 디렉터리를 업로드합니다.

이 예에서는 디렉터리와 해당 디렉터리의 모든 파일을 blob 컨테이너에 복사합니다. 결과는 컨테이너에 같은 이름으로 디렉터리가 생깁니다.

> [!TIP]
> 이 예에서는 경로 인수를 작은따옴표(‘’)로 묶습니다. Windows 명령 셸(cmd.exe)을 제외하고 모든 명령 셸에서 작은따옴표를 사용합니다. Windows 명령 셸(cmd.exe)을 사용하는 경우 작은따옴표(‘’) 대신 큰따옴표(“”)로 경로 인수를 묶습니다.

**구문**

`azcopy copy '<local-directory-path>' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>' --recursive`

**예제**

```azcopy
azcopy copy 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer' --recursive
```

**예제(계층 구조 네임스페이스)**

```azcopy
azcopy copy 'C:\myDirectory' 'https://mystorageaccount.dfs.core.windows.net/mycontainer' --recursive
```

컨테이너 내의 디렉터리로 복사하려면 명령 문자열에서 해당 디렉터리의 이름을 지정하기만 하면 됩니다.

**예제**

```azcopy
azcopy copy 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory' --recursive
```

**예제(계층 구조 네임스페이스)**

```azcopy
azcopy copy 'C:\myDirectory' 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myBlobDirectory' --recursive
```

컨테이너에 존재하지 않는 디렉터리의 이름을 지정하면 AzCopy는 해당 이름으로 새 디렉터리를 만듭니다.

## <a name="upload-directory-contents"></a>디렉터리 콘텐츠 업로드

[azcopy copy](storage-ref-azcopy-copy.md) 명령을 사용하여 디렉터리의 콘텐츠를 업로드합니다. 포함하는 디렉터리 자체를 복사하지 않고 콘텐츠를 업로드하려면 와일드카드 기호(*)를 사용합니다.

> [!TIP]
> 이 예에서는 경로 인수를 작은따옴표(‘’)로 묶습니다. Windows 명령 셸(cmd.exe)을 제외하고 모든 명령 셸에서 작은따옴표를 사용합니다. Windows 명령 셸(cmd.exe)을 사용하는 경우 작은따옴표(‘’) 대신 큰따옴표(“”)로 경로 인수를 묶습니다.

**구문**

`azcopy copy '<local-directory-path>\*' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<directory-path>'`

**예제**

```azcopy
azcopy copy 'C:\myDirectory\*' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory'
```

**예제(계층 구조 네임스페이스)**

```azcopy
azcopy copy 'C:\myDirectory\*' 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myBlobDirectory'
```

모든 하위 디렉터리에 파일을 업로드하려면 `--recursive` 플래그를 추가합니다.

## <a name="upload-specific-files"></a>특정 파일 업로드

전체 파일 이름, 와일드카드 문자(*)가 포함된 부분 이름을 사용하거나 날짜 및 시간을 사용하여 특정 파일을 업로드할 수 있습니다.

> [!TIP]
> 이 예에서는 경로 인수를 작은따옴표(‘’)로 묶습니다. Windows 명령 셸(cmd.exe)을 제외하고 모든 명령 셸에서 작은따옴표를 사용합니다. Windows 명령 셸(cmd.exe)을 사용하는 경우 작은따옴표(‘’) 대신 큰따옴표(“”)로 경로 인수를 묶습니다.

### <a name="specify-multiple-complete-file-names"></a>전체 파일 이름을 여러 개 지정

`--include-path` 옵션과 함께 [azcopy copy](storage-ref-azcopy-copy.md) 명령을 사용합니다. 세미콜론(`;`)을 사용하여 개별 파일 이름을 구분합니다.

**구문**

`azcopy copy '<local-directory-path>' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>' --include-path <semicolon-separated-file-list>`

**예제**

```azcopy
azcopy copy 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer' --include-path 'photos;documents\myFile.txt' --recursive'
```

**예제(계층 구조 네임스페이스)**

```azcopy
azcopy copy 'C:\myDirectory' 'https://mystorageaccount.dfs.core.windows.net/mycontainer' --include-path 'photos;documents\myFile.txt' --recursive'
```

이 예에서 AzCopy는 `C:\myDirectory\photos` 디렉터리와 `C:\myDirectory\documents\myFile.txt` 파일을 전송합니다. `C:\myDirectory\photos` 디렉터리의 모든 파일을 전송하려면 `--recursive` 옵션을 포함합니다.

`--exclude-path` 옵션을 사용하여 파일을 제외할 수도 있습니다. 자세히 알아보려면 [azcopy copy](storage-ref-azcopy-copy.md) 참조 문서를 확인하세요.

### <a name="use-wildcard-characters"></a>와일드카드 문자 사용

`--include-pattern` 옵션과 함께 [azcopy copy](storage-ref-azcopy-copy.md) 명령을 사용합니다. 와일드카드 문자를 포함하는 부분 이름을 지정합니다. 세미콜론(`;`)을 사용하여 이름을 구분합니다.

**구문**

`azcopy copy '<local-directory-path>' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>' --include-pattern <semicolon-separated-file-list-with-wildcard-characters>`

**예제**

```azcopy
azcopy copy 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer' --include-pattern 'myFile*.txt;*.pdf*'
```

**예제(계층 구조 네임스페이스)**

```azcopy
azcopy copy 'C:\myDirectory' 'https://mystorageaccount.dfs.core.windows.net/mycontainer' --include-pattern 'myFile*.txt;*.pdf*'
```

`--exclude-pattern` 옵션을 사용하여 파일을 제외할 수도 있습니다. 자세히 알아보려면 [azcopy copy](storage-ref-azcopy-copy.md) 참조 문서를 확인하세요.

`--include-pattern`및 `--exclude-pattern` 옵션은 경로가 아니라 파일 이름에만 적용됩니다.  디렉터리 트리에 있는 모든 텍스트 파일을 복사하려면 `–recursive` 옵션을 사용하여 전체 디렉터리 트리를 가져온 다음 `–include-pattern`을 사용하고 `*.txt`를 지정하여 모든 텍스트 파일을 가져옵니다.

### <a name="upload-files-that-were-modified-before-or-after-a-date-and-time"></a>날짜 및 시간 이전 또는 이후 수정된 파일 업로드

`--include-before` 또는 `--include-after` 옵션과 함께 [azcopy copy](storage-ref-azcopy-copy.md) 명령을 사용합니다. ISO-8601 형식으로 날짜와 시간을 지정합니다(예: `2020-08-19T15:04:00Z`).

다음 예에서는 지정한 날짜 또는 그 이후에 수정된 파일을 업로드합니다.

**구문**

`azcopy copy '<local-directory-path>\*' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-or-directory-name>'  --include-after <Date-Time-in-ISO-8601-format>`

**예제**

```azcopy
azcopy copy 'C:\myDirectory\*' 'https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory'  --include-after '2020-08-19T15:04:00Z'
```

**예제(계층 구조 네임스페이스)**

```azcopy
azcopy copy 'C:\myDirectory\*' 'https://mystorageaccount.dfs.core.windows.net/mycontainer/FileDirectory'   --include-after '2020-08-19T15:04:00Z'
```

자세한 참조는 [azcopy copy](storage-ref-azcopy-copy.md) 참조 문서를 확인하세요.

## <a name="upload-with-index-tags"></a>인덱스 태그로 업로드

파일을 업로드하고 대상 Blob에 [Blob 인덱스 태그(미리 보기) ](../blobs/storage-manage-find-blobs.md)를 추가할 수 있습니다.

Azure AD 권한 부여를 사용하는 경우 보안 주체에 [Storage Blob 데이터 소유자](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) 역할이 할당되거나 사용자 지정 Azure 역할을 통해 `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags/write` [Azure 리소스 공급자 작업](../../role-based-access-control/resource-provider-operations.md#microsoftstorage)에 대한 사용 권한이 부여되어야 합니다. SAS(공유 액세스 서명) 토큰을 사용하는 경우 해당 토큰은 `t` SAS 권한을 통해 blob의 태그에 대한 액세스를 제공해야 합니다.

태그를 추가하려면 URL로 인코딩된 키-값 쌍과 함께 `--blob-tags` 옵션을 사용합니다. 예를 들어, `my tag` 키와 `my tag value` 값을 추가하려면 `--blob-tags='my%20tag=my%20tag%20value'`를 대상 매개 변수에 추가합니다.

앰퍼샌드(`&`)를 사용하여 여러 인덱스 태그를 구분합니다.  예를 들어, `my second tag` 키와 `my second tag value` 값을 추가하려는 경우 전체 옵션 문자열은 `--blob-tags='my%20tag=my%20tag%20value&my%20second%20tag=my%20second%20tag%20value'`입니다.

다음 예에서는 `--blob-tags` 옵션 사용 방법을 보여 줍니다.

> [!TIP]
> 이 예에서는 경로 인수를 작은따옴표(‘’)로 묶습니다. Windows 명령 셸(cmd.exe)을 제외하고 모든 명령 셸에서 작은따옴표를 사용합니다. Windows 명령 셸(cmd.exe)을 사용하는 경우 작은따옴표(‘’) 대신 큰따옴표(“”)로 경로 인수를 묶습니다.

**파일 업로드**

```azcopy
azcopy copy 'C:\myDirectory\myTextFile.txt' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myTextFile.txt' --blob-tags='my%20tag=my%20tag%20value&my%20second%20tag=my%20second%20tag%20value'
```

**디렉터리 업로드**

```azcopy
azcopy copy 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer' --recursive --blob-tags='my%20tag=my%20tag%20value&my%20second%20tag=my%20second%20tag%20value'
```

**디렉터리 콘텐츠 업로드**

```azcopy
azcopy copy 'C:\myDirectory\*' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory' --blob-tags='my%20tag=my%20tag%20value&my%20second%20tag=my%20second%20tag%20value'
```

> [!NOTE]
> 원본의 디렉터리를 지정하는 경우 대상에 복사되는 모든 Blob에는 명령에서 지정한 것과 같은 태그가 있습니다.

## <a name="upload-with-optional-flags"></a>선택적 플래그를 사용하여 업로드

선택적 플래그를 사용하여 업로드 작업을 조정할 수 있습니다. 다음은 몇 가지 예제입니다.

|시나리오|플래그|
|---|---|
|추가 Blob 또는 페이지 Blob으로 파일을 업로드합니다.|**--blob-type**=\[BlockBlob\|PageBlob\|AppendBlob\]|
|특정 액세스 계층(예: 보관 계층)에 업로드합니다.|**--block-blob-tier**=\[None\|Hot\|Cool\|Archive\]|

전체 목록을 보려면 [옵션](storage-ref-azcopy-copy.md#options)을 참조하세요.

## <a name="next-steps"></a>다음 단계

다음 문서에서 더 많은 예제를 찾아보세요.

- [예제: 다운로드](storage-use-azcopy-blobs-download.md)
- [예제: 계정 간 복사](storage-use-azcopy-blobs-copy.md)
- [예제: 동기화](storage-use-azcopy-blobs-synchronize.md)
- [예제: Amazon S3 버킷](storage-use-azcopy-s3.md)
- [예: Google Cloud Storage](storage-use-azcopy-google-cloud.md)
- [예: Azure Files](storage-use-azcopy-files.md)
- [자습서: AzCopy를 사용하여 클라우드로 온-프레미스 데이터 마이그레이션](storage-use-azcopy-migrate-on-premises-data.md)

이러한 문서를 참조하여 설정을 구성하고, 성능을 최적화하고, 문제를 해결하세요.

- [AzCopy 구성 설정](storage-ref-azcopy-configuration-settings.md)
- [AzCopy의 성능 최적화](storage-use-azcopy-optimize.md)
- [로그 파일을 사용하여 Azure Storage의 AzCopy V10 문제 해결](storage-use-azcopy-configure.md)
