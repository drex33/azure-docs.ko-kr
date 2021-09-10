---
title: AzCopy v10을 사용하여 Azure Blob Storage에서 Blob 다운로드 | Microsoft Docs
description: 이 문서에는 Azure Blob Storage에서 blob을 다운로드하는 데 도움이 되는 AzCopy 예제 명령 컬렉션이 포함되어 있습니다.
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 04/02/2021
ms.author: normesta
ms.subservice: common
ms.reviewer: dineshm
ms.openlocfilehash: c57744817e26cc79c101246f146d32d3db061ed5
ms.sourcegitcommit: 82d82642daa5c452a39c3b3d57cd849c06df21b0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/07/2021
ms.locfileid: "113361482"
---
# <a name="download-blobs-from-azure-blob-storage-by-using-azcopy"></a>AzCopy를 사용하여 Azure Blob Storage에서 Blob 다운로드

AzCopy v10 명령줄 유틸리티를 사용하여 Blob Storage에서 blob과 디렉터리를 다운로드할 수 있습니다. 

파일 업로드, Blob Storage와 동기화 또는 계정 간 Blob 복사와 같은 다른 작업 형식의 예를 보려면 이 문서의 [다음 단계](#next-steps) 섹션에 있는 링크를 참조하세요.

## <a name="get-started"></a>시작

AzCopy를 다운로드하고 스토리지 서비스에 인증 자격 증명을 제공하는 방법에 관해 알아보려면 [AzCopy 시작](storage-use-azcopy-v10.md) 문서를 참조하세요.

> [!NOTE] 
> 이 문서의 예제에서는 Azure AD(Azure Active Directory)를 사용하여 권한 부여 자격 증명을 제공했다고 가정합니다.
>
> SAS 토큰을 사용하여 blob 데이터에 대한 액세스 권한을 부여하려면 각 AzCopy 명령에서 해당 토큰을 리소스 URL에 추가할 수 있습니다. 예: `'https://<storage-account-name>.blob.core.windows.net/<container-name><SAS-token>'`

## <a name="download-a-blob"></a>Blob 다운로드

[azcopy copy](storage-ref-azcopy-copy.md) 명령을 사용하여 blob을 다운로드합니다.

> [!TIP]
> 이 예에서는 경로 인수를 작은따옴표(‘’)로 묶습니다. Windows 명령 셸(cmd.exe)을 제외하고 모든 명령 셸에서 작은따옴표를 사용합니다. Windows 명령 셸(cmd.exe)을 사용하는 경우 작은따옴표(‘’) 대신 큰따옴표(“”)로 경로 인수를 묶습니다.

**구문**

``azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<blob-path>' '<local-file-path>'``

**예제**

```azcopy
azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/myTextFile.txt' 'C:\myDirectory\myTextFile.txt'
```

**예제(계층 구조 네임스페이스)**

```azcopy
azcopy copy 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myTextFile.txt' 'C:\myDirectory\myTextFile.txt'
```

> [!NOTE]
> Blob의 `Content-md5` 속성 값에 해시가 포함된 경우 AzCopy는 다운로드된 데이터의 MD5 해시를 계산하고 Blob의 `Content-md5` 속성에 저장된 MD5 해시가 계산된 해시와 일치하는지 확인합니다. 이 값이 일치하지 않는 경우 copy 명령에 `--check-md5=NoCheck` 또는 `--check-md5=LogOnly`를 추가하여 이 동작을 재정의하지 않으면 다운로드에 실패합니다.

## <a name="download-a-directory"></a>디렉터리 다운로드

[azcopy copy](storage-ref-azcopy-copy.md) 명령을 사용하여 디렉터리를 다운로드합니다.

> [!TIP]
> 이 예에서는 경로 인수를 작은따옴표(‘’)로 묶습니다. Windows 명령 셸(cmd.exe)을 제외하고 모든 명령 셸에서 작은따옴표를 사용합니다. Windows 명령 셸(cmd.exe)을 사용하는 경우 작은따옴표(‘’) 대신 큰따옴표(“”)로 경로 인수를 묶습니다.

**구문**

`azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<directory-path>' '<local-directory-path>' --recursive`

**예제**

```azcopy
azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory' 'C:\myDirectory'  --recursive
```

**예제(계층 구조 네임스페이스)**

```azcopy
azcopy copy 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myBlobDirectory' 'C:\myDirectory'  --recursive
```

이 예에서는 다운로드한 모든 blob을 포함하는 `C:\myDirectory\myBlobDirectory`라는 디렉터리를 생성합니다.

## <a name="download-directory-contents"></a>디렉터리 콘텐츠 다운로드

와일드카드 기호(*)를 사용하여 포함하는 디렉터리 자체를 복사하지 않고 디렉터리의 콘텐츠를 다운로드할 수 있습니다.

> [!TIP]
> 이 예에서는 경로 인수를 작은따옴표(‘’)로 묶습니다. Windows 명령 셸(cmd.exe)을 제외하고 모든 명령 셸에서 작은따옴표를 사용합니다. Windows 명령 셸(cmd.exe)을 사용하는 경우 작은따옴표(‘’) 대신 큰따옴표(“”)로 경로 인수를 묶습니다.

> [!NOTE]
> 현재 이 시나리오는 계층 구조 네임스페이스가 없는 계정에만 지원됩니다.

**구문**

`azcopy copy 'https://<storage-account-name>.blob.core.windows.net/<container-name>/*' '<local-directory-path>/'`

**예제**

```azcopy
azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory/*' 'C:\myDirectory'
```

모든 하위 디렉터리에 파일을 다운로드하려면 `--recursive` 플래그를 추가합니다.

## <a name="download-specific-blobs"></a>특정 blob 다운로드

전체 파일 이름, 와일드카드 문자(*)가 포함된 부분 이름을 사용하거나 날짜 및 시간을 사용하여 특정 blob을 다운로드할 수 있습니다. 

> [!TIP]
> 이 예에서는 경로 인수를 작은따옴표(‘’)로 묶습니다. Windows 명령 셸(cmd.exe)을 제외하고 모든 명령 셸에서 작은따옴표를 사용합니다. Windows 명령 셸(cmd.exe)을 사용하는 경우 작은따옴표(‘’) 대신 큰따옴표(“”)로 경로 인수를 묶습니다.

#### <a name="specify-multiple-complete-blob-names"></a>전체 blob 이름을 여러 개 지정

`--include-path` 옵션과 함께 [azcopy copy](storage-ref-azcopy-copy.md) 명령을 사용합니다. 세미콜론(`;`)을 사용하여 개별 blob 이름을 구분합니다.

**구문**

`azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-or-directory-name>' '<local-directory-path>'  --include-path <semicolon-separated-file-list>`

**예제**

```azcopy
azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory' 'C:\myDirectory'  --include-path 'photos;documents\myFile.txt' --recursive
```

**예제(계층 구조 네임스페이스)**

```azcopy
azcopy copy 'https://mystorageaccount.dfs.core.windows.net/mycontainer/FileDirectory' 'C:\myDirectory'  --include-path 'photos;documents\myFile.txt'--recursive
```

이 예에서 AzCopy는 `https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory/photos` 디렉터리와 `https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory/documents/myFile.txt` 파일을 전송합니다. `https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory/photos` 디렉터리의 모든 blob을 전송하려면 `--recursive` 옵션을 포함합니다.

`--exclude-path` 옵션을 사용하여 blob을 제외할 수도 있습니다. 자세히 알아보려면 [azcopy copy](storage-ref-azcopy-copy.md) 참조 문서를 확인하세요.

#### <a name="use-wildcard-characters"></a>와일드카드 문자 사용

`--include-pattern` 옵션과 함께 [azcopy copy](storage-ref-azcopy-copy.md) 명령을 사용합니다. 와일드카드 문자를 포함하는 부분 이름을 지정합니다. 세미콜론(`;`)을 사용하여 이름을 구분합니다.

**구문**

`azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-or-directory-name>' '<local-directory-path>' --include-pattern <semicolon-separated-file-list-with-wildcard-characters>`

**예제**

```azcopy
azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory' 'C:\myDirectory'  --include-pattern 'myFile*.txt;*.pdf*'
```

**예제(계층 구조 네임스페이스)**

```azcopy
azcopy copy 'https://mystorageaccount.dfs.core.windows.net/mycontainer/FileDirectory' 'C:\myDirectory'  --include-pattern 'myFile*.txt;*.pdf*'
```

`--exclude-pattern` 옵션을 사용하여 blob을 제외할 수도 있습니다. 자세히 알아보려면 [azcopy copy](storage-ref-azcopy-copy.md) 참조 문서를 확인하세요.

`--include-pattern`및 `--exclude-pattern` 옵션은 경로가 아니라 blob 이름에만 적용됩니다.  디렉터리 트리에 있는 모든 텍스트 파일(blob)을 복사하려면 `–recursive` 옵션을 사용하여 전체 디렉터리 트리를 가져온 다음 `–include-pattern`을 사용하고 `*.txt`를 지정하여 모든 텍스트 파일을 가져옵니다.

#### <a name="download-blobs-that-were-modified-before-or-after-a-date-and-time"></a>날짜 및 시간 이전 또는 이후 수정된 blob 다운로드 

`--include-before` 또는 `--include-after` 옵션과 함께 [azcopy copy](storage-ref-azcopy-copy.md) 명령을 사용합니다. ISO-8601 형식으로 날짜와 시간을 지정합니다(예: `2020-08-19T15:04:00Z`). 

다음 예에서는 지정한 날짜 또는 그 이후에 수정된 파일을 다운로드합니다.

**구문**

`azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-or-directory-name>/*' '<local-directory-path>' --include-after <Date-Time-in-ISO-8601-format>`

**예제**

```azcopy
azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory/*' 'C:\myDirectory'  --include-after '2020-08-19T15:04:00Z'
```

**예제(계층 구조 네임스페이스)**

```azcopy
azcopy copy 'https://mystorageaccount.dfs.core.windows.net/mycontainer/FileDirectory/*' 'C:\myDirectory'  --include-after '2020-08-19T15:04:00Z'
```

자세한 참조는 [azcopy copy](storage-ref-azcopy-copy.md) 참조 문서를 확인하세요.

#### <a name="download-previous-versions-of-a-blob"></a>이전 버전의 blob 다운로드

[Blob 버전 관리](../blobs/versioning-enable.md)를 사용하도록 설정한 경우 blob의 이전 버전을 하나 이상 다운로드할 수 있습니다. 

먼저 [버전 ID](../blobs/versioning-overview.md)목록을 포함하는 텍스트 파일을 만듭니다. 각 버전 ID는 별도의 줄에 표시되어야 합니다. 예를 들면 다음과 같습니다. 

```
2020-08-17T05:50:34.2199403Z
2020-08-17T05:50:34.5041365Z
2020-08-17T05:50:36.7607103Z
```

그런 다음 `--list-of-versions` 옵션과 함께 [azcopy copy](storage-ref-azcopy-copy.md) 명령을 사용합니다. 버전 목록이 포함된 텍스트 파일의 위치를 지정합니다(예: `D:\\list-of-versions.txt` ).  

#### <a name="download-a-blob-snapshot"></a>Blob 스냅샷 다운로드

Blob 스냅샷의 **DateTime** 값을 참조하여 [blob 스냅샷을](../blobs/snapshots-overview.md) 다운로드할 수 있습니다. 

**구문**

`azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<blob-path>?sharesnapshot=<DateTime-of-snapshot>' '<local-file-path>'`

**예제**

```azcopy
azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/myTextFile.txt?sharesnapshot=2020-09-23T08:21:07.0000000Z' 'C:\myDirectory\myTextFile.txt'
```

**예제(계층 구조 네임스페이스)**

```azcopy
azcopy copy 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myTextFile.txt?sharesnapshot=2020-09-23T08:21:07.0000000Z' 'C:\myDirectory\myTextFile.txt'
```

> [!NOTE]
> SAS 토큰을 사용하여 blob 데이터에 대한 액세스 권한을 부여하는 경우 SAS 토큰 뒤에 스냅샷 **DateTime** 을 추가합니다. 예: `'https://mystorageaccount.blob.core.windows.net/mycontainer/myTextFile.txt?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D&sharesnapshot=2020-09-23T08:21:07.0000000Z'`

## <a name="download-with-optional-flags"></a>선택적 플래그를 사용하여 다운로드

선택적 플래그를 사용하여 다운로드 작업을 조정할 수 있습니다. 다음은 몇 가지 예제입니다.

|시나리오|플래그|
|---|---|
|자동으로 파일의 압축을 풉니다.|**--decompress**|
|복사 관련 로그 항목의 세부 정보를 지정합니다.|**--log-level**=\[WARNING\|ERROR\|INFO\|NONE\]|
|대상에서 충돌하는 파일과 blob의 덮어쓰기 여부와 방법을 지정합니다.|**--overwrite**=\[true\|false\|ifSourceNewer\|prompt\]|

전체 목록을 보려면 [옵션](storage-ref-azcopy-copy.md#options)을 참조하세요.

## <a name="next-steps"></a>다음 단계

다음 문서에서 더 많은 예제를 찾아보세요.

- [예제: 업로드](storage-use-azcopy-blobs-upload.md)
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
