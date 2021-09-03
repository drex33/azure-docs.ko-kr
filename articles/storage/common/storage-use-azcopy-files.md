---
title: AzCopy v10을 사용하여 Azure Files에서 데이터 전송 | Microsoft Docs
description: AzCopy 및 파일 스토리지를 사용하여 데이터를 전송합니다. AzCopy는 스토리지 계정에서 또는 스토리지 계정으로 Blob 또는 파일을 복사하는 명령줄 도구입니다. Azure Files로 AzCopy를 사용합니다.
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 04/02/2021
ms.author: normesta
ms.subservice: common
ms.openlocfilehash: 91e771554c5ce1940243eaec4a0ea3cf965c3f8f
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/22/2021
ms.locfileid: "114468573"
---
# <a name="transfer-data-with-azcopy-and-file-storage"></a>AzCopy 및 File Storage를 사용하여 데이터 전송 

AzCopy는 스토리지 계정에서 또는 스토리지 계정으로 파일을 복사하는 데 사용할 수 있는 명령줄 유틸리티입니다. 이 문서에는 Azure Files를 사용하는 예제 명령이 포함되어 있습니다.

시작하기 전에 [AzCopy 시작](storage-use-azcopy-v10.md) 문서를 확인하여 AzCopy를 다운로드하고 도구를 숙지하십시오.

> [!TIP]
> 이 문서의 예제에서는 경로 인수를 작은따옴표('')로 묶습니다. Windows 명령 셸(cmd.exe)을 제외하고 모든 명령 셸에서 작은따옴표를 사용합니다. Windows 명령 셸(cmd.exe)을 사용하는 경우 작은따옴표(‘’) 대신 큰따옴표(“”)로 경로 인수를 묶습니다.

## <a name="create-file-shares"></a>파일 공유 만들기

[azcopy make](storage-ref-azcopy-make.md) 명령을 사용하여 파일 공유를 만들 수 있습니다. 이 섹션의 예제에서는 `myfileshare`라는 파일 공유를 만듭니다.

**구문**

`azcopy make 'https://<storage-account-name>.file.core.windows.net/<file-share-name><SAS-token>'`

**예제**

```azcopy
azcopy make 'https://mystorageaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D'
```

자세한 참조 문서는 [azcopy make](storage-ref-azcopy-make.md)를 참조하세요.

## <a name="upload-files"></a>파일 업로드

[azcopy copy](storage-ref-azcopy-copy.md) 명령을 사용하여 로컬 컴퓨터에서 파일과 디렉터리를 업로드할 수 있습니다.

이 섹션에는 다음 예제가 포함되어 있습니다.

> [!div class="checklist"]
> * 파일 업로드
> * 디렉터리 업로드
> * 디렉터리의 콘텐츠 업로드
> * 특정 파일 업로드

> [!TIP]
> 선택적 플래그를 사용하여 업로드 작업을 조정할 수 있습니다. 다음은 몇 가지 예제입니다.  
>
> |시나리오|플래그|
> |---|---|
> |ACL(액세스 제어 목록)을 파일과 함께 복사합니다.|**--preserve-smb-permissions**=\[true\|false\]|
> |파일과 함께 SMB 속성 정보를 복사합니다.|**--preserve-smb-info**=\[true\|false\]|
> 
> 전체 목록을 보려면 [옵션](storage-ref-azcopy-copy.md#options)을 참조하세요.

> [!NOTE]
> AzCopy는 파일의 md5 해시 코드를 자동으로 계산하고 저장하지 않습니다. AzCopy를 수행하려면 각 copy 명령에 `--put-md5` 플래그를 추가합니다. 이를 통해 AzCopy는 파일이 다운로드되었을 때 다운로드한 데이터의 MD5 해시를 계산하고 파일의 `Content-md5` 속성에 저장된 MD5 해시가 계산된 해시와 일치하는지 확인합니다.

### <a name="upload-a-file"></a>파일 업로드

**구문**

`azcopy copy '<local-file-path>' 'https://<storage-account-name>.file.core.windows.net/<file-share-name>/<file-name><SAS-token>'`

**예제**

```azcopy
azcopy copy 'C:\myDirectory\myTextFile.txt' 'https://mystorageaccount.file.core.windows.net/myfileshare/myTextFile.txt?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D' --preserve-smb-permissions=true --preserve-smb-info=true
```

파일 경로 또는 파일 이름의 어디서든 와일드카드 기호(*)를 사용하여 파일을 업로드할 수도 있습니다. 예: `'C:\myDirectory\*.txt'` 또는 `C:\my*\*.txt`.

### <a name="upload-a-directory"></a>디렉터리 업로드

이 예에서는 디렉터리와 해당 디렉터리의 모든 파일을 파일 공유에 복사합니다. 결과는 파일 공유에 동일한 이름으로 디렉터리가 생깁니다.

**구문**

`azcopy copy '<local-directory-path>' 'https://<storage-account-name>.file.core.windows.net/<file-share-name><SAS-token>' --recursive`

**예제**

```azcopy
azcopy copy 'C:\myDirectory' 'https://mystorageaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D' --recursive --preserve-smb-permissions=true --preserve-smb-info=true
```

파일 공유 내의 디렉터리로 복사하려면 명령 문자열에서 해당 디렉터리의 이름을 지정하기만 하면 됩니다.

**예제**

```azcopy
azcopy copy 'C:\myDirectory' 'https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareDirectory?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D' --recursive --preserve-smb-permissions=true --preserve-smb-info=true
```

파일 공유에 존재하지 않는 디렉터리의 이름을 지정하면 AzCopy는 해당 이름으로 새 디렉터리를 만듭니다.

### <a name="upload-the-contents-of-a-directory"></a>디렉터리의 콘텐츠 업로드

와일드카드 기호(*)를 사용하여 포함하는 디렉터리 자체를 복사하지 않고 디렉터리의 콘텐츠를 업로드할 수 있습니다.

**구문**

`azcopy copy '<local-directory-path>/*' 'https://<storage-account-name>.file.core.windows.net/<file-share-name>/<directory-path><SAS-token>'`

**예제**

```azcopy
azcopy copy 'C:\myDirectory\*' 'https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareDirectory?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D" --preserve-smb-permissions=true --preserve-smb-info=true
```

> [!NOTE]
> 모든 하위 디렉터리에 파일을 업로드하려면 `--recursive` 플래그를 추가합니다.

### <a name="upload-specific-files"></a>특정 파일 업로드

전체 파일 이름, 와일드카드 문자(*)가 포함된 부분 이름을 사용하거나 날짜 및 시간을 사용하여 특정 파일을 업로드할 수 있습니다.

#### <a name="specify-multiple-complete-file-names"></a>전체 파일 이름을 여러 개 지정

`--include-path` 옵션과 함께 [azcopy copy](storage-ref-azcopy-copy.md) 명령을 사용합니다. 세미콜론(`;`)을 사용하여 개별 파일 이름을 구분합니다.

**구문**

`azcopy copy '<local-directory-path>' 'https://<storage-account-name>.file.core.windows.net/<file-share-or-directory-name><SAS-token>' --include-path <semicolon-separated-file-list>`

**예제**

```azcopy
azcopy copy 'C:\myDirectory' 'https://mystorageaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --include-path 'photos;documents\myFile.txt' --preserve-smb-permissions=true --preserve-smb-info=true
```

이 예제에서 AzCopy는 `C:\myDirectory\photos` 디렉터리와 `C:\myDirectory\documents\myFile.txt` 파일을 전송합니다. `C:\myDirectory\photos` 디렉터리의 모든 파일을 전송하려면 `--recursive` 옵션을 포함해야 합니다.

`--exclude-path` 옵션을 사용하여 파일을 제외할 수도 있습니다. 자세히 알아보려면 [azcopy copy](storage-ref-azcopy-copy.md) 참조 문서를 확인하세요.

#### <a name="use-wildcard-characters"></a>와일드카드 문자 사용

`--include-pattern` 옵션과 함께 [azcopy copy](storage-ref-azcopy-copy.md) 명령을 사용합니다. 와일드카드 문자를 포함하는 부분 이름을 지정합니다. 세미콜론(`;`)을 사용하여 이름을 구분합니다.

**구문**

`azcopy copy '<local-directory-path>' 'https://<storage-account-name>.file.core.windows.net/<file-share-or-directory-name><SAS-token>' --include-pattern <semicolon-separated-file-list-with-wildcard-characters>`

**예제**

```azcopy
azcopy copy 'C:\myDirectory' 'https://mystorageaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --include-pattern 'myFile*.txt;*.pdf*' --preserve-smb-permissions=true --preserve-smb-info=true
```

`--exclude-pattern` 옵션을 사용하여 파일을 제외할 수도 있습니다. 자세히 알아보려면 [azcopy copy](storage-ref-azcopy-copy.md) 참조 문서를 확인하세요.

`--include-pattern`및 `--exclude-pattern` 옵션은 경로가 아니라 파일 이름에만 적용됩니다.  디렉터리 트리에 있는 모든 텍스트 파일을 복사하려면 `--recursive` 옵션을 사용하여 전체 디렉터리 트리를 가져온 다음 `--include-pattern`을 사용하고 `*.txt`를 지정하여 모든 텍스트 파일을 가져옵니다.

#### <a name="upload-files-that-were-modified-after-a-date-and-time"></a>날짜 및 시간 이후 수정된 파일 업로드 

`--include-after` 옵션과 함께 [azcopy copy](storage-ref-azcopy-copy.md) 명령을 사용합니다. ISO 8601형식으로 날짜와 시간을 지정합니다(예: `2020-08-19T15:04:00Z`). 

**구문**

`azcopy copy '<local-directory-path>\*' 'https://<storage-account-name>.file.core.windows.net/<file-share-or-directory-name><SAS-token>'  --include-after <Date-Time-in-ISO-8601-format>`

**예제**

```azcopy
azcopy copy 'C:\myDirectory\*' 'https://mystorageaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --include-after '2020-08-19T15:04:00Z' --preserve-smb-permissions=true --preserve-smb-info=true
```

자세한 참조는 [azcopy copy](storage-ref-azcopy-copy.md) 참조 문서를 확인하세요.

## <a name="download-files"></a>파일 다운로드

[azcopy copy](storage-ref-azcopy-copy.md) 명령을 사용하여 로컬 컴퓨터에 파일, 디렉터리, 파일 공유를 다운로드할 수 있습니다.

이 섹션에는 다음 예제가 포함되어 있습니다.

> [!div class="checklist"]
> * 파일 다운로드
> * 디렉터리 다운로드
> * 디렉터리의 콘텐츠 다운로드
> * 특정 파일 다운로드

> [!TIP]
> 선택적 플래그를 사용하여 다운로드 작업을 조정할 수 있습니다. 다음은 몇 가지 예제입니다.
>
> |시나리오|플래그|
> |---|---|
> |ACL(액세스 제어 목록)을 파일과 함께 복사합니다.|**--preserve-smb-permissions**=\[true\|false\]|
> |파일과 함께 SMB 속성 정보를 복사합니다.|**--preserve-smb-info**=\[true\|false\]|
> |자동으로 파일의 압축을 풉니다.|**--decompress**|
> 
> 전체 목록을 보려면 [옵션](storage-ref-azcopy-copy.md#options)을 참조하세요.

> [!NOTE]
> 파일의 `Content-md5` 속성 값에 해시가 포함된 경우 AzCopy는 다운로드된 데이터의 MD5 해시를 계산하고 파일의 `Content-md5` 속성에 저장된 MD5 해시가 계산된 해시와 일치하는지 확인합니다. 이 값이 일치하지 않는 경우 copy 명령에 `--check-md5=NoCheck` 또는 `--check-md5=LogOnly`를 추가하여 이 동작을 재정의하지 않으면 다운로드에 실패합니다.

### <a name="download-a-file"></a>파일 다운로드

**구문**

`azcopy copy 'https://<storage-account-name>.file.core.windows.net/<file-share-name>/<file-path><SAS-token>' '<local-file-path>'`

**예제**

```azcopy
azcopy copy 'https://mystorageaccount.file.core.windows.net/myfileshare/myTextFile.txt?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D' 'C:\myDirectory\myTextFile.txt' --preserve-smb-permissions=true --preserve-smb-info=true
```

### <a name="download-a-directory"></a>디렉터리 다운로드

**구문**

`azcopy copy 'https://<storage-account-name>.file.core.windows.net/<file-share-name>/<directory-path><SAS-token>' '<local-directory-path>' --recursive`

**예제**

```azcopy
azcopy copy 'https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareDirectory?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D' 'C:\myDirectory'  --recursive --preserve-smb-permissions=true --preserve-smb-info=true
```

이 예제에서는 다운로드한 모든 파일을 포함하는 `C:\myDirectory\myFileShareDirectory`라는 디렉터리를 생성합니다.

### <a name="download-the-contents-of-a-directory"></a>디렉터리의 콘텐츠 다운로드

와일드카드 기호(*)를 사용하여 포함하는 디렉터리 자체를 복사하지 않고 디렉터리의 콘텐츠를 다운로드할 수 있습니다.

**구문**

`azcopy copy 'https://<storage-account-name>.file.core.windows.net/<file-share-name>/*<SAS-token>' '<local-directory-path>/'`

**예제**

```azcopy
azcopy copy 'https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareDirectory/*?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D' 'C:\myDirectory' --preserve-smb-permissions=true --preserve-smb-info=true
```

> [!NOTE]
> 모든 하위 디렉터리에 파일을 다운로드하려면 `--recursive` 플래그를 추가합니다.

### <a name="download-specific-files"></a>특정 파일 다운로드

전체 파일 이름, 와일드카드 문자(*)가 포함된 부분 이름을 사용하거나 날짜 및 시간을 사용하여 특정 파일을 다운로드할 수 있습니다.

#### <a name="specify-multiple-complete-file-names"></a>전체 파일 이름을 여러 개 지정

`--include-path` 옵션과 함께 [azcopy copy](storage-ref-azcopy-copy.md) 명령을 사용합니다. 세미콜론(`;`)을 사용하여 개별 파일 이름을 구분합니다.

**구문**

`azcopy copy 'https://<storage-account-name>.file.core.windows.net/<file-share-or-directory-name><SAS-token>' '<local-directory-path>'  --include-path <semicolon-separated-file-list>`

**예제**

```azcopy
azcopy copy 'https://mystorageaccount.file.core.windows.net/myFileShare/myDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'C:\myDirectory'  --include-path 'photos;documents\myFile.txt' --recursive --preserve-smb-permissions=true --preserve-smb-info=true
```

이 예제에서 AzCopy는 `https://mystorageaccount.file.core.windows.net/myFileShare/myDirectory/photos` 디렉터리와 `https://mystorageaccount.file.core.windows.net/myFileShare/myDirectory/documents/myFile.txt` 파일을 전송합니다. `https://mystorageaccount.file.core.windows.net/myFileShare/myDirectory/photos` 디렉터리의 모든 파일을 전송하려면 `--recursive` 옵션을 포함합니다.

`--exclude-path` 옵션을 사용하여 파일을 제외할 수도 있습니다. 자세히 알아보려면 [azcopy copy](storage-ref-azcopy-copy.md) 참조 문서를 확인하세요.

#### <a name="use-wildcard-characters"></a>와일드카드 문자 사용

`--include-pattern` 옵션과 함께 [azcopy copy](storage-ref-azcopy-copy.md) 명령을 사용합니다. 와일드카드 문자를 포함하는 부분 이름을 지정합니다. 세미콜론(`;`)을 사용하여 이름을 구분합니다.

**구문**

`azcopy copy 'https://<storage-account-name>.file.core.windows.net/<file-share-or-directory-name><SAS-token>' '<local-directory-path>' --include-pattern <semicolon-separated-file-list-with-wildcard-characters>`

**예제**

```azcopy
azcopy copy 'https://mystorageaccount.file.core.windows.net/myfileshare/myDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'C:\myDirectory'  --include-pattern 'myFile*.txt;*.pdf*' --preserve-smb-permissions=true --preserve-smb-info=true
```

`--exclude-pattern` 옵션을 사용하여 파일을 제외할 수도 있습니다. 자세히 알아보려면 [azcopy copy](storage-ref-azcopy-copy.md) 참조 문서를 확인하세요.

`--include-pattern`및 `--exclude-pattern` 옵션은 경로가 아니라 파일 이름에만 적용됩니다.  디렉터리 트리에 있는 모든 텍스트 파일을 복사하려면 `--recursive` 옵션을 사용하여 전체 디렉터리 트리를 가져온 다음 `--include-pattern`을 사용하고 `*.txt`를 지정하여 모든 텍스트 파일을 가져옵니다.

#### <a name="download-files-that-were-modified-after-a-date-and-time"></a>날짜 및 시간 이전 또는 이후 수정된 파일 다운로드 

`--include-after` 옵션과 함께 [azcopy copy](storage-ref-azcopy-copy.md) 명령을 사용합니다. ISO-8601형식으로 날짜와 시간을 지정합니다(예: `2020-08-19T15:04:00Z`). 

**구문**

`azcopy copy 'https://<storage-account-name>.file.core.windows.net/<file-share-or-directory-name>/*<SAS-token>' '<local-directory-path>'  --include-after <Date-Time-in-ISO-8601-format>`

**예제**

```azcopy
azcopy copy 'https://mystorageaccount.file.core.windows.net/myfileshare/*?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'C:\myDirectory' --include-after '2020-08-19T15:04:00Z' --preserve-smb-permissions=true --preserve-smb-info=true
```

자세한 참조는 [azcopy copy](storage-ref-azcopy-copy.md) 참조 문서를 확인하세요.

#### <a name="download-from-a-share-snapshot"></a>공유 스냅샷에서 다운로드

공유 스냅샷의 **날짜/시간** 값을 참조하여 특정 버전의 파일이나 디렉터리를 다운로드할 수 있습니다. 공유 스냅샷에 대해 자세히 알아보려면 [Azure Files의 공유 스냅샷 개요](../files/storage-snapshots-files.md)를 참조하세요. 

**구문**

`azcopy copy 'https://<storage-account-name>.file.core.windows.net/<file-share-name>/<file-path-or-directory-name><SAS-token>&sharesnapshot=<DateTime-of-snapshot>' '<local-file-or-directory-path>'`

**예(파일 다운로드)**

```azcopy
azcopy copy 'https://mystorageaccount.file.core.windows.net/myfileshare/myTextFile.txt?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D&sharesnapshot=2020-09-23T08:21:07.0000000Z' 'C:\myDirectory\myTextFile.txt' --preserve-smb-permissions=true --preserve-smb-info=true
```

**예(디렉터리 다운로드)**

```azcopy
azcopy copy 'https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareDirectory?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D&sharesnapshot=2020-09-23T08:21:07.0000000Z' 'C:\myDirectory'  --recursive --preserve-smb-permissions=true --preserve-smb-info=true
```

## <a name="copy-files-between-storage-accounts"></a>스토리지 계정 간에 파일 복사

AzCopy를 사용하여 파일을 다른 스토리지 계정에 복사할 수 있습니다. 복사 작업은 동기식이므로 명령이 반환될 때 모든 파일이 복사되었음을 나타냅니다.

AzCopy는 [서버 간](/rest/api/storageservices/put-block-from-url) [API](/rest/api/storageservices/put-page-from-url)를 사용하므로 스토리지 서버 간에 데이터가 직접 복사됩니다. 이 복사 작업은 컴퓨터의 네트워크 대역폭을 사용하지 않습니다. `AZCOPY_CONCURRENCY_VALUE` 환경 변수의 값을 설정하여 해당 작업의 처리량을 늘릴 수 있습니다. 자세히 알아보려면 [동시성 향상](storage-use-azcopy-optimize.md#increase-concurrency)을 참조하세요.

공유 스냅샷의 **날짜/시간** 값을 참조하여 특정 버전의 파일을 복사할 수도 있습니다. 공유 스냅샷에 대해 자세히 알아보려면 [Azure Files의 공유 스냅샷 개요](../files/storage-snapshots-files.md)를 참조하세요. 

이 섹션에는 다음 예제가 포함되어 있습니다.

> [!div class="checklist"]
> * 다른 스토리지 계정에 파일 복사
> * 다른 스토리지 계정에 디렉터리 복사
> * 다른 스토리지 계정에 파일 공유 복사
> * 다른 스토리지 계정에 모든 파일 공유, 디렉터리 및 파일 복사

> [!TIP]
> 선택적 플래그를 사용하여 복사 작업을 조정할 수 있습니다. 다음은 몇 가지 예제입니다.
>
> |시나리오|플래그|
> |---|---|
> |ACL(액세스 제어 목록)을 파일과 함께 복사합니다.|**--preserve-smb-permissions**=\[true\|false\]|
> |파일과 함께 SMB 속성 정보를 복사합니다.|**--preserve-smb-info**=\[true\|false\]|
> 
> 전체 목록을 보려면 [옵션](storage-ref-azcopy-copy.md#options)을 참조하세요.

### <a name="copy-a-file-to-another-storage-account"></a>다른 스토리지 계정에 파일 복사

**구문**

`azcopy copy 'https://<source-storage-account-name>.file.core.windows.net/<file-share-name>/<file-path><SAS-token>' 'https://<destination-storage-account-name>.file.core.windows.net/<file-share-name>/<file-path><SAS-token>'`

**예제**

```azcopy
azcopy copy 'https://mysourceaccount.file.core.windows.net/mycontainer/myTextFile.txt?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.file.core.windows.net/mycontainer/myTextFile.txt?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --preserve-smb-permissions=true --preserve-smb-info=true
```

**예제(스냅샷 공유)**

```azcopy
azcopy copy 'https://mysourceaccount.file.core.windows.net/mycontainer/myTextFile.txt?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D&sharesnapshot=2020-09-23T08:21:07.0000000Z' 'https://mydestinationaccount.file.core.windows.net/mycontainer/myTextFile.txt?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --preserve-smb-permissions=true --preserve-smb-info=true
```

### <a name="copy-a-directory-to-another-storage-account"></a>다른 스토리지 계정에 디렉터리 복사

**구문**

`azcopy copy 'https://<source-storage-account-name>.file.core.windows.net/<file-share-name>/<directory-path><SAS-token>' 'https://<destination-storage-account-name>.file.core.windows.net/<file-share-name><SAS-token>' --recursive`

**예제**

```azcopy
azcopy copy 'https://mysourceaccount.file.core.windows.net/myFileShare/myFileDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.file.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --recursive --preserve-smb-permissions=true --preserve-smb-info=true
```

**예제(스냅샷 공유)**

```azcopy
azcopy copy 'https://mysourceaccount.file.core.windows.net/myFileShare/myFileDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D&sharesnapshot=2020-09-23T08:21:07.0000000Z' 'https://mydestinationaccount.file.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --recursive --preserve-smb-permissions=true --preserve-smb-info=true
```

### <a name="copy-a-file-share-to-another-storage-account"></a>다른 스토리지 계정에 파일 공유 복사

**구문**

`azcopy copy 'https://<source-storage-account-name>.file.core.windows.net/<file-share-name><SAS-token>' 'https://<destination-storage-account-name>.file.core.windows.net/<file-share-name><SAS-token>' --recursive`

**예제**

```azcopy
azcopy copy 'https://mysourceaccount.file.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.file.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D --preserve-smb-permissions=true --preserve-smb-info=true
```

**예제(스냅샷 공유)**

```azcopy
azcopy copy 'https://mysourceaccount.file.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D&sharesnapshot=2020-09-23T08:21:07.0000000Z' 'https://mydestinationaccount.file.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --recursive --preserve-smb-permissions=true --preserve-smb-info=true
```

### <a name="copy-all-file-shares-directories-and-files-to-another-storage-account"></a>다른 스토리지 계정에 모든 파일 공유, 디렉터리 및 파일 복사

**구문**

`azcopy copy 'https://<source-storage-account-name>.file.core.windows.net/<SAS-token>' 'https://<destination-storage-account-name>.file.core.windows.net/<SAS-token>' --recursive'`

**예제**

```azcopy
azcopy copy 'https://mysourceaccount.file.core.windows.net?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.file.core.windows.net?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --recursive --preserve-smb-permissions=true --preserve-smb-info=true
```

**예제(스냅샷 공유)**

```azcopy
azcopy copy 'https://mysourceaccount.file.core.windows.net?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D&sharesnapshot=2020-09-23T08:21:07.0000000Z' 'https://mydestinationaccount.file.core.windows.net?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --recursive --preserve-smb-permissions=true --preserve-smb-info=true
```

## <a name="synchronize-files"></a>파일 동기화

로컬 파일 시스템의 내용을 파일 공유와 동기화하거나 파일 공유의 내용을 다른 파일 공유와 동기화할 수 있습니다. 또한 파일 공유에 있는 디렉터리의 내용을 다른 파일 공유에 있는 디렉터리의 내용과 동기화할 수 있습니다. 한 가지 방법으로 동기화가 있습니다. 즉, 이 두 엔드포인트 중에서 소스와 대상을 선택합니다. 동기화에서도 서버 간 API를 사용합니다.

> [!NOTE]
> 현재 이 시나리오는 계층 구조 네임스페이스가 없는 계정에만 지원됩니다. 현재 AzCopy의 릴리스는 Azure Files와 Blob Storage 간에 동기화되지 않습니다.

### <a name="guidelines"></a>지침

- [sync](storage-ref-azcopy-sync.md) 명령은 파일 이름과 마지막으로 수정된 타임스탬프를 비교합니다. `--delete-destination` 선택적 플래그를 `true` 또는 `prompt` 값으로 설정하여 해당 파일이 소스 디렉터리에 더는 존재하지 않는 경우 대상 디렉터리의 파일을 삭제합니다.

- `--delete-destination` 플래그를 `true`로 설정하면 AzCopy에서 프롬프트를 제공하지 않고 파일을 삭제합니다. AzCopy가 파일을 삭제하기 전에 프롬프트를 표시하려면 `--delete-destination` 플래그를 `prompt`로 설정합니다.

- `--delete-destination` 플래그를 `prompt` 또는 `false`로 설정하려는 경우 [sync](storage-ref-azcopy-sync.md) 명령 대신 [copy](storage-ref-azcopy-copy.md) 명령을 사용하고 `--overwrite` 매개 변수를 `ifSourceNewer`로 설정하는 것이 좋습니다. 복사 작업은 파일을 이동하기 전에 원본 또는 대상을 인덱싱하지 않아도 되기 때문에 [copy](storage-ref-azcopy-copy.md) 명령을 사용하면 메모리를 적게 사용하고 청구 비용을 줄일 수 있습니다. 

- 마지막으로 수정된 시간은 파일이 전송되어야 하는지 여부를 결정하는 데 중요하므로 sync 명령을 실행하는 머신에 정확한 시스템 클록이 있어야 합니다. 시스템의 클록 오차가 심각한 경우 sync 명령을 실행하려는 시간에 너무 가까운 대상의 파일을 수정하지 마십시오.

> [!TIP]
> 선택적 플래그를 사용하여 동기화 작업을 조정할 수 있습니다. 다음은 몇 가지 예제입니다.
>
> |시나리오|플래그|
> |---|---|
> |ACL(액세스 제어 목록)을 파일과 함께 복사합니다.|**--preserve-smb-permissions**=\[true\|false\]|
> |파일과 함께 SMB 속성 정보를 복사합니다.|**--preserve-smb-info**=\[true\|false\]|
> |패턴을 기반으로 파일을 제외합니다.|**--exclude-path**|
> |동기화 관련 로그 항목의 세부 정보를 지정합니다.|**--log-level**=\[WARNING\|ERROR\|INFO\|NONE\]|
> 
> 전체 목록을 보려면 [옵션](storage-ref-azcopy-sync.md#options)을 참조하세요.

### <a name="update-a-file-share-with-changes-to-a-local-file-system"></a>로컬 파일 시스템의 변경 내용으로 파일 공유 업데이트

이 경우 파일 공유는 대상이고, 로컬 파일 시스템은 원본입니다. 

> [!TIP]
> 이 예에서는 경로 인수를 작은따옴표(‘’)로 묶습니다. Windows 명령 셸(cmd.exe)을 제외하고 모든 명령 셸에서 작은따옴표를 사용합니다. Windows 명령 셸(cmd.exe)을 사용하는 경우 작은따옴표(‘’) 대신 큰따옴표(“”)로 경로 인수를 묶습니다.

**구문**

`azcopy sync '<local-directory-path>' 'https://<storage-account-name>.file.core.windows.net/<file-share-name><SAS-token>' --recursive`

**예제**

```azcopy
azcopy sync 'C:\myDirectory' 'https://mystorageaccount.file.core.windows.net/myfileShare?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --recursive
```

### <a name="update-a-local-file-system-with-changes-to-a-file-share"></a>파일 공유의 변경 내용으로 로컬 파일 시스템 업데이트

이 경우 로컬 파일 시스템은 대상이고, 파일 공유는 원본입니다.

> [!TIP]
> 이 예에서는 경로 인수를 작은따옴표(‘’)로 묶습니다. Windows 명령 셸(cmd.exe)을 제외하고 모든 명령 셸에서 작은따옴표를 사용합니다. Windows 명령 셸(cmd.exe)을 사용하는 경우 작은따옴표(‘’) 대신 큰따옴표(“”)로 경로 인수를 묶습니다.

**구문**

`azcopy sync 'https://<storage-account-name>.file.core.windows.net/<file-share-name><SAS-token>' 'C:\myDirectory' --recursive`

**예제**

```azcopy
azcopy sync 'https://mystorageaccount.file.core.windows.net/myfileShare?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'C:\myDirectory' --recursive
```

### <a name="update-a-file-share-with-changes-to-another-file-share"></a>다른 파일 공유에 대한 변경 내용으로 파일 공유 업데이트

이 명령에 표시되는 첫 번째 파일 공유는 원본입니다. 두 번째는 대상입니다.

**구문**

`azcopy sync 'https://<source-storage-account-name>.file.core.windows.net/<file-share-name><SAS-token>' 'https://<destination-storage-account-name>.file.core.windows.net/<file-share-name><SAS-token>' --recursive`

**예제**

```azcopy
azcopy sync 'https://mysourceaccount.file.core.windows.net/myfileShare?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --recursive --preserve-smb-permissions=true --preserve-smb-info=true
```

### <a name="update-a-directory-with-changes-to-a-directory-in-another-file-share"></a>다른 파일 공유에 있는 디렉터리의 변경 내용으로 디렉터리 업데이트

이 명령에 표시되는 첫 번째 디렉터리는 원본입니다. 두 번째는 대상입니다.

**구문**

`azcopy sync 'https://<source-storage-account-name>.file.core.windows.net/<file-share-name>/<directory-name><SAS-token>' 'https://<destination-storage-account-name>.file.core.windows.net/<file-share-name>/<directory-name><SAS-token>' --recursive`

**예제**

```azcopy
azcopy sync 'https://mysourceaccount.file.core.windows.net/myFileShare/myDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.file.core.windows.net/myFileShare/myDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --recursive --preserve-smb-permissions=true --preserve-smb-info=true
```

### <a name="update-a-file-share-to-match-the-contents-of-a-share-snapshot"></a>공유 스냅샷의 내용과 일치하도록 파일 공유 업데이트

이 명령에 표시되는 첫 번째 파일 공유는 원본입니다. URI의 끝에서 문자열 `&sharesnapshot=` 뒤에 스냅샷의 **날짜/시간** 값을 추가합니다.

**구문**

`azcopy sync 'https://<source-storage-account-name>.file.core.windows.net/<file-share-name><SAS-token>&sharesnapsot<snapshot-ID>' 'https://<destination-storage-account-name>.file.core.windows.net/<file-share-name><SAS-token>' --recursive`

**예제**

```azcopy
azcopy sync 'https://mysourceaccount.file.core.windows.net/myfileShare?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D&sharesnapshot=2020-03-03T20%3A24%3A13.0000000Z' 'https://mydestinationaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --recursive --preserve-smb-permissions=true --preserve-smb-info=true
``` 

공유 스냅샷에 대해 자세히 알아보려면 [Azure Files의 공유 스냅샷 개요](../files/storage-snapshots-files.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

다음 문서에서 더 많은 예제를 찾아보세요.

- [AzCopy 시작](storage-use-azcopy-v10.md)
- [데이터 전송](storage-use-azcopy-v10.md#transfer-data)

이러한 문서를 참조하여 설정을 구성하고, 성능을 최적화하고, 문제를 해결하세요.

- [AzCopy 구성 설정](storage-ref-azcopy-configuration-settings.md)
- [AzCopy의 성능 최적화](storage-use-azcopy-optimize.md)
- [로그 파일을 사용하여 Azure Storage의 AzCopy V10 문제 해결](storage-use-azcopy-configure.md)

