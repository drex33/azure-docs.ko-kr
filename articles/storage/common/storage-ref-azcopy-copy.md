---
title: azcopy copy| Microsoft Docs
description: 이 문서는 azcopy copy 명령에 대한 참조 정보를 제공합니다.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 09/01/2021
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 4cb943230c0211c67caf5d7a074d21077358c143
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128605452"
---
# <a name="azcopy-copy"></a>azcopy copy

원본 데이터를 대상 위치로 복사합니다.

## <a name="synopsis"></a>개요

원본 데이터를 대상 위치로 복사합니다. 지원되는 방향은 다음과 같습니다.

  - 로컬 <-> Azure Blob(SAS 또는 OAuth 인증)
  - 로컬 <-> Azure Files(공유/디렉터리 SAS 인증)
  - 로컬 <-> Azure Data Lake Storage Gen 2(SAS, OAuth 또는 공유 키 인증)
  - Azure Blob(SAS 또는 퍼블릭) -> Azure Blob(SAS 또는 OAuth 인증)
  - Azure Blob(SAS 또는 퍼블릭) -> Azure Files(SAS)
  - Azure Files(SAS) -> Azure Files(SAS)
  - Azure Files(SAS) -> Azure Blob(SAS 또는 OAuth 인증)
  - Amazon Web Services(AWS) S3(액세스 키) -> Azure 블록 Blob(SAS 또는 OAuth 인증)
  - Google Cloud Storage(서비스 계정 키) -> Azure 블록 Blob(SAS 또는 OAuth 인증) [미리 보기]

자세한 내용은 이 문서의 예제 섹션을 참조하세요.

## <a name="related-conceptual-articles"></a>관련 개념 문서

- [AzCopy 시작](storage-use-azcopy-v10.md)
- [자습서: AzCopy를 사용하여 클라우드 스토리지로 온-프레미스 데이터 마이그레이션](storage-use-azcopy-migrate-on-premises-data.md)
- [AzCopy 및 Blob Storage를 사용하여 데이터 전송](./storage-use-azcopy-v10.md#transfer-data)
- [AzCopy 및 File Storage를 사용하여 데이터 전송](storage-use-azcopy-files.md)

## <a name="advanced"></a>고급

AzCopy는 로컬 디스크에서 파일을 업로드할 때 파일의 콘텐츠 형식을 자동으로 검색합니다. AzCopy는 파일 확장명 또는 콘텐츠를 기준으로 콘텐츠 형식을 검색합니다(확장명이 지정되지 않은 경우).

기본 제공 조회 테이블은 작지만 Unix에서는 다음 이름 중 하나 이상의 이름으로 제공되는 경우 로컬 시스템의 `mime.types` 파일에 의해 확대됩니다.

- /etc/mime.types
- /etc/apache2/mime.types
- /etc/apache/mime.types

Windows에서는 레지스트리에서 MIME 형식이 추출됩니다. 플래그를 사용하여 이 기능을 끌 수 있습니다. 자세한 내용은 이 문서의 플래그 섹션을 참조하세요.

명령줄을 사용하여 환경 변수를 설정하는 경우 명령줄 기록에서 해당 변수를 읽을 수 있습니다. 명령줄 기록에서 자격 증명을 포함하는 변수를 지우는 것이 좋습니다. 기록에 변수가 나타나지 않게 하려면 스크립트를 사용하여 사용자에게 자격 증명을 요청하는 메시지를 표시하고 환경 변수를 설정하면 됩니다.

```
azcopy copy [source] [destination] [flags]
```

## <a name="examples"></a>예

OAuth 인증을 사용하여 단일 파일을 업로드합니다. AzCopy에 아직 로그인하지 않았다면 다음 명령을 실행하기 전에 `azcopy login`명령을 실행합니다.

```azcopy
azcopy cp "/path/to/file.txt" "https://[account].blob.core.windows.net/[container]/[path/to/blob]"
```

위와 동일하지만 이번에는 파일 콘텐츠의 MD5 해시를 계산하고 Blob의 콘텐츠-MD5 속성으로 저장합니다.

```azcopy
azcopy cp "/path/to/file.txt" "https://[account].blob.core.windows.net/[container]/[path/to/blob]" --put-md5
```

SAS 토큰을 사용하여 단일 파일을 업로드합니다.

```azcopy
azcopy cp "/path/to/file.txt" "https://[account].blob.core.windows.net/[container]/[path/to/blob]?[SAS]"
```

SAS 토큰과 파이핑(블록 Blob에만 해당)를 사용하여 단일 파일을 업로드합니다.

```azcopy
cat "/path/to/file.txt" | azcopy cp "https://[account].blob.core.windows.net/[container]/[path/to/blob]?[SAS]
```

SAS 토큰을 사용하여 전체 디렉터리를 업로드합니다.

```azcopy
azcopy cp "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive
```

또는

```azcopy
azcopy cp "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive --put-md5
```

SAS 토큰 및 와일드카드(*) 문자를 사용하여 파일 집합을 업로드합니다.

```azcopy
azcopy cp "/path/*foo/*bar/*.pdf" "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]"
```

SAS 토큰 및 와일드카드(*) 문자를 사용하여 파일 및 디렉터리를 업로드합니다.

```azcopy
azcopy cp "/path/*foo/*bar*" "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive
```

파일 및 디렉터리를 Azure Storage 계정에 업로드하고 Blob에 쿼리 문자열로 인코딩된 태그를 설정합니다.

- {Key = "bla bla", val = "foo"} 및 {key = "bla bla 2", val = "bar"} 태그를 설정하려면 다음 구문을 사용합니다. `azcopy cp "/path/*foo/*bar*" "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --blob-tags="bla%20bla=foo&bla%20bla%202=bar"`

- 키와 값은 URL로 인코딩되고 키-값 쌍은 앰퍼샌드('&')로 구분됩니다.

- Blob에서 태그를 설정하는 동안, 없는 경우 서비스로부터 권한 부여 오류가 반환되는 추가 권한(tags의 ‘t’)이 SAS에 있습니다.

OAuth 인증을 사용하여 단일 파일을 다운로드합니다. AzCopy에 아직 로그인하지 않았다면 다음 명령을 실행하기 전에 `azcopy login`명령을 실행합니다.

```azcopy
azcopy cp "https://[account].blob.core.windows.net/[container]/[path/to/blob]" "/path/to/file.txt"
```

SAS 토큰을 사용하여 단일 파일을 다운로드합니다.

```azcopy
azcopy cp "https://[account].blob.core.windows.net/[container]/[path/to/blob]?[SAS]" "/path/to/file.txt"
```

SAS 토큰을 사용하여 단일 파일을 다운로드 한 다음 출력을 파일로 파이핑합니다(블록 Blob에만 해당).

```azcopy
azcopy cp "https://[account].blob.core.windows.net/[container]/[path/to/blob]?[SAS]" > "/path/to/file.txt"
```

SAS 토큰을 사용하여 전체 디렉터리를 다운로드합니다.

```azcopy
azcopy cp "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" "/path/to/dir" --recursive
```

URL에 와일드카드 문자(*)를 사용하는 방법에 대한 참고:

URL에 와일드카드 문자를 사용하기 위해 지원되는 방법은 오직 2가지입니다.

- URL의 마지막 슬래시(/) 바로 다음에 한 개를 사용할 수 있습니다. 와일드카드 문자를 이렇게 사용하면 디렉터리에 있는 모든 파일이 하위 디렉터리에 배치되지 않고 대상에 직접 복사됩니다.

- URL이 Blob이 아닌 컨테이너를 참조하는 경우 와일드카드 문자를 컨테이너 이름에 사용할 수도 있습니다. 이 방법을 사용하여 컨테이너의 하위 집합에서 파일을 가져올 수 있습니다.

포함하는 디렉터리 자체를 복사하지 않고 디렉터리의 콘텐츠를 다운로드합니다.

```azcopy
azcopy cp "https://[srcaccount].blob.core.windows.net/[container]/[path/to/folder]/*?[SAS]" "/path/to/dir"
```

전체 스토리지 계정을 다운로드합니다.

```azcopy
azcopy cp "https://[srcaccount].blob.core.windows.net/" "/path/to/dir" --recursive
```

컨테이너 이름에 와일드카드 기호(*)를 사용하여 스토리지 계정 내 컨테이너의 하위 집합을 다운로드합니다.

```azcopy
azcopy cp "https://[srcaccount].blob.core.windows.net/[container*name]" "/path/to/dir" --recursive
```

SAS 토큰을 사용하여 단일 Blob을 다른 Blob으로 복사합니다.

```azcopy
azcopy cp "https://[srcaccount].blob.core.windows.net/[container]/[path/to/blob]?[SAS]" "https://[destaccount].blob.core.windows.net/[container]/[path/to/blob]?[SAS]"
```

SAS 토큰과 인증 토큰을 사용하여 단일 Blob을 다른 Blob으로 복사합니다. 원본 계정 URL의 끝에 SAS 토큰을 사용해야 하지만 `azcopy login` 명령을 사용하여 AzCopy에 로그인하는 경우 대상 계정이 필요하지 않습니다.

```azcopy
azcopy cp "https://[srcaccount].blob.core.windows.net/[container]/[path/to/blob]?[SAS]" "https://[destaccount].blob.core.windows.net/[container]/[path/to/blob]"
```

SAS 토큰을 사용하여 Blob 가상 디렉터리 한 개를 다른 Blob 가상 디렉터리로 복사합니다.

```azcopy
azcopy cp "https://[srcaccount].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" "https://[destaccount].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive=true
```

SAS 토큰을 사용하여 모든 Blob 컨테이너, 디렉터리 및 Blob을 스토리지 계정에서 다른 계정으로 복사합니다.

```azcopy
azcopy cp "https://[srcaccount].blob.core.windows.net?[SAS]" "https://[destaccount].blob.core.windows.net?[SAS]" --recursive
```

액세스 키 및 SAS 토큰을 사용하여 단일 개체를 AWS(Amazon Web Services) S3에서 Blob Storage로 복사합니다. 먼저 AWS S3 원본에 대해 환경 변수 `AWS_ACCESS_KEY_ID` 및 `AWS_SECRET_ACCESS_KEY`를 설정합니다.

```azcopy
azcopy cp "https://s3.amazonaws.com/[bucket]/[object]" "https://[destaccount].blob.core.windows.net/[container]/[path/to/blob]?[SAS]"
```

액세스 키 및 SAS 토큰을 사용하여 전체 디렉터리를 AWS S3에서 Blob Storage로 복사합니다. 먼저 AWS S3 원본에 대해 환경 변수 `AWS_ACCESS_KEY_ID` 및 `AWS_SECRET_ACCESS_KEY`를 설정합니다.

```azcopy
azcopy cp "https://s3.amazonaws.com/[bucket]/[folder]" "https://[destaccount].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive
```

  [폴더] 자리 표시자에 대한 자세한 내용은 https://docs.aws.amazon.com/AmazonS3/latest/user-guide/using-folders.html 을 참조하세요.

액세스 키 및 SAS 토큰을 사용하여 모든 버킷을 AWS(Amazon Web Services) S3에서 Blob Storage로 복사합니다. 먼저 AWS S3 원본에 대해 환경 변수 `AWS_ACCESS_KEY_ID` 및 `AWS_SECRET_ACCESS_KEY`를 설정합니다.

```azcopy
azcopy cp "https://s3.amazonaws.com/" "https://[destaccount].blob.core.windows.net?[SAS]" --recursive
```

액세스 키 및 SAS 토큰을 사용하여 모든 버킷을 AWS(Amazon Web Services) 지역에서 Blob Storage로 복사합니다. 먼저 AWS S3 원본에 대해 환경 변수 `AWS_ACCESS_KEY_ID` 및 `AWS_SECRET_ACCESS_KEY`를 설정합니다.

```azcopy
- azcopy cp "https://s3-[region].amazonaws.com/" "https://[destaccount].blob.core.windows.net?[SAS]" --recursive
```

버킷 이름에 와일드카드 기호(*)를 사용하여 버킷의 하위 집합을 복사합니다. 이전 예제와 마찬가지로 액세스 키와 SAS 토큰이 필요합니다. AWS S3 원본에 대해 환경 변수 `AWS_ACCESS_KEY_ID` 및 `AWS_SECRET_ACCESS_KEY`를 설정해야 합니다.

```azcopy
- azcopy cp "https://s3.amazonaws.com/[bucket*name]/" "https://[destaccount].blob.core.windows.net?[SAS]" --recursive
```

파일 및 디렉터리를 Azure Storage 계정으로 전송하고 쿼리 문자열로 인코딩되어 제공된 태그를 Blob에 설정합니다.

- {key = "bla bla", val = "foo"} 및 {key = "bla bla 2", val = "bar"} 태그를 설정하려면 다음 구문을 사용합니다. `azcopy cp "https://[account].blob.core.windows.net/[source_container]/[path/to/directory]?[SAS]" "https://[account].blob.core.windows.net/[destination_container]/[path/to/directory]?[SAS]" --blob-tags="bla%20bla=foo&bla%20bla%202=bar"`

- 키와 값은 URL로 인코딩되고 키-값 쌍은 앰퍼샌드('&')로 구분됩니다.

- Blob에서 태그를 설정하는 동안, 없는 경우 서비스로부터 권한 부여 오류가 반환되는 추가 권한(tags의 ‘t’)이 SAS에 있습니다.

서비스 계정 키와 SAS 토큰을 사용하여 단일 개체를 Google Cloud Storage에서 Blob Storage로 복사합니다. 먼저 Google Cloud Storage 원본에 대해 GOOGLE_APPLICATION_CREDENTIALS 환경 변수를 설정합니다.

```azcopy
azcopy cp "https://storage.cloud.google.com/[bucket]/[object]" "https://[destaccount].blob.core.windows.net/[container]/[path/to/blob]?[SAS]"
```

서비스 계정 키와 SAS 토큰을 사용하여 전체 디렉터리를 Google Cloud Storage에서 Blob Storage로 복사합니다. 먼저 Google Cloud Storage 원본에 대해 GOOGLE_APPLICATION_CREDENTIALS 환경 변수를 설정합니다.

```azcopy
  - azcopy cp "https://storage.cloud.google.com/[bucket]/[folder]" "https://[destaccount].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive=true
```

서비스 계정 키와 SAS 토큰을 사용하여 전체 버킷을 Google Cloud Storage에서 Blob Storage로 복사합니다. 먼저 Google Cloud Storage 원본에 대해 GOOGLE_APPLICATION_CREDENTIALS 환경 변수를 설정합니다.

```azcopy 
azcopy cp "https://storage.cloud.google.com/[bucket]" "https://[destaccount].blob.core.windows.net/?[SAS]" --recursive=true
```

서비스 계정 키와 SAS 토큰을 사용하여 모든 버킷을 Google Cloud Storage에서 Blob Storage로 복사합니다. 먼저 GCS 원본에 대한 환경 변수 GOOGLE_APPLICATION_CREDENTIALS 및 GOOGLE_CLOUD_PROJECT=<> 설정합니다. `project-id`

```azcopy
  - azcopy cp "https://storage.cloud.google.com/" "https://[destaccount].blob.core.windows.net/?[SAS]" --recursive=true
```

서비스 계정 키와 대상에 대한 SAS 토큰을 사용하여 Google Cloud Storage의 버킷 이름에 와일드카드 기호(*)를 사용하여 버킷의 하위 집합을 복사합니다. 먼저 Google Cloud Storage 원본에 대한 환경 변수 GOOGLE_APPLICATION_CREDENTIALS 및 GOOGLE_CLOUD_PROJECT= `project-id`<> 설정합니다.

```azcopy
azcopy cp "https://storage.cloud.google.com/[bucket*name]/" "https://[destaccount].blob.core.windows.net/?[SAS]" --recursive=true
```

## <a name="options"></a>옵션

**--backup**: 업로드를 위해 Windows의 SeBackupPrivilege를 활성화하거나 다운로드를 위해 SeRestorePrivilege를 활성화하여 AzCopy에서 파일 시스템 권한에 관계없이 모든 파일을 보고 읽고 모든 권한을 복원할 수 있도록 해 줍니다. AzCopy를 실행하는 계정에 이미 해당 권한이 있어야 합니다. 예를 들어 관리자 권한을 보유하고 있거나 `Backup Operators` 그룹의 구성원이어야 합니다. 이 플래그는 계정이 이미 보유 중인 권한을 활성화합니다.

**--blob-tags** 문자열: Blob에 태그를 설정하여 스토리지 계정의 데이터를 분류합니다.

**--blob-type** 문자열: 대상에서 Blob의 형식을 정의합니다. Blob을 업로드하고 계정 간에 복사할 때 사용됩니다(기본값 `Detect`). 유효한 값은 `Detect`, `BlockBlob`, `PageBlob`및 `AppendBlob`입니다. 계정 간에 복사하는 경우 값이 `Detect`이면 AzCopy에서 원본 Blob의 형식을 사용하여 대상 Blob의 형식을 확인하게 됩니다. 파일을 업로드할 때는 `Detect`은 파일 확장명을 토대로 파일이 VHD 또는 VHDX 파일인지 여부를 확인합니다. 파일이 VHD 또는 VHDX 파일인 경우 AzCopy에서는 해당 파일을 페이지 Blob으로 처리합니다. (기본값 "Detect")

**--block-blob-tier** 스트링: 이 Blob 계층을 사용하여 블록 Blob를 Azure Storage에 업로드합니다. (기본값 "None")

**--block-size-mb** 부동: Azure Storage로 업로드하고 Azure Storage에서 다운로드할 때 이 블록 크기(MiB로 지정됨)를 사용합니다. 기본값은 파일 크기에 따라 자동으로 계산됩니다. 소수를 사용할 수 있습니다(예: 0.25).

**--cache-control** 문자열: cache-control 헤더를 설정합니다. 다운로드 시 반환됩니다.

**--check length**: 전송 후 대상에서 파일의 길이를 확인합니다. 원본과 대상이 일치하지 않는 경우 전송이 실패한 것으로 표시됩니다. (기본값은 `true`)

**--check-md5** 문자열: 다운로드 시 MD5 해시의 유효성을 얼마나 엄격하게 검사해야 하는지 지정합니다. 다운로드할 때만 사용할 수 있습니다. 사용 가능한 옵션은 `NoCheck`, `LogOnly`, `FailIfDifferent`, `FailIfDifferentOrMissing`입니다. (기본값 `FailIfDifferent`) (기본값 "FailIfDifferent")

**--content-disposition** 문자열: content-disposition 헤더를 설정합니다. 다운로드 시 반환됩니다.

**--content-encoding** 문자열: content-encoding 헤더를 설정합니다. 다운로드 시 반환됩니다.

**--content-language** 문자열: content-language 헤더를 설정합니다. 다운로드 시 반환됩니다.

**--content-type** 문자열: 파일의 콘텐츠 형식을 지정합니다. no-guess-mime-type을 의미합니다. 다운로드 시 반환됩니다.

**--cpk-by-name** 문자열 클라이언트가 이름으로 키를 제공하므로 Azure Blob에 대한 요청을 하는 클라이언트는 요청별로 암호화 키를 제공하는 옵션을 Storage. 제공된 키 이름은 Azure Key Vault 가져와 데이터를 암호화하는 데 사용됩니다.

**--cpk-by-value**                          클라이언트가 이름으로 제공한 키를 사용하면 Azure Blob에 대한 요청을 하는 클라이언트가 요청별로 암호화 키를 제공하는 옵션을 Storage 수 있습니다. 제공된 키와 해당 해시는 환경 변수에서 페치됩니다.

**--decompress**: 다운로드 시 파일의 content-encoding이 파일이 압축되었음을 나타내는 경우 해당 파일의 압축을 자동으로 풉니다. 지원되는 content-encoding 값은 `gzip` 및 `deflate`입니다. 파일 확장명 `.gz`/`.gzip` 또는 `.zz`는 필요하지 않으며 있는 경우 제거됩니다.

**--dry-run**                              이 명령으로 복사할 파일 경로를 인쇄합니다. 이 플래그는 실제 파일을 복사하지 않습니다.

**--disable-auto-decoding**    Windows에서 잘못된 문자의 자동 디코딩을 사용하도록 설정하려면 기본적으로 False입니다. `true`로 설정하여 자동 디코딩을 사용하지 않도록 설정할 수 있습니다.

**--exclude-attributes** 문자열: (Windows에만 해당) 해당 특성이 특성 목록과 일치하는 파일을 제외합니다. 예: A;S;R

**--exclude-blob-type** 문자열: 필요에 따라 컨테이너 또는 계정에서 Blob을 복사할 때 제외할 Blob 형식(`BlockBlob`/ `PageBlob`/ `AppendBlob`)을 지정합니다. 비 Azure 서비스에서 서비스로 데이터를 복사하는 경우에는 이 플래그를 사용할 수 없습니다. 둘 이상의 Blob은 `;`으로 구분해야 합니다.

**--exclude-path** 문자열: 복사 시 해당 경로를 제외합니다. 이 옵션은 와일드카드 문자(*)를 지원하지 않습니다. 상대 경로 접두사(예: `myFolder;myFolder/subDirName/file.pdf`)를 확인합니다. 계정 순회와 함께 사용하는 경우 경로는 컨테이너 이름을 포함하지 않습니다.

**--exclude-pattern** 문자열: 복사 시 해당 파일을 제외합니다. 이 옵션은 와일드카드 문자(*)를 지원합니다.

**--exclude-regex** 문자열 정규식과 일치하는 파일의 모든 상대 경로를 제외합니다. 정규식을 ';'로 구분합니다.

**--follow-symlinks**: 로컬 파일 시스템에서 업로드할 때 바로 가기 링크를 따릅니다.

**--force-if-read-only**: Windows 또는 Azure Files에서 기존 파일을 덮어쓸 때 기존 파일에 읽기 전용 특성 집합이 있는 경우에도 덮어쓰기가 작동하도록 강제합니다.

**--from-to** 문자열: 필요에 따라 원본 대상 조합을 지정합니다. 예: `LocalBlob`, `BlobLocal`, `LocalBlobFS`.

**--help**: 복사에 대한 도움말입니다.

**--include-after** 문자열: 지정된 날짜/시간 이후에 수정된 파일만 포함합니다. 값은 ISO8601 형식이어야 합니다. 표준 시간대가 지정되지 않은 경우 이 값은 AzCopy를 실행하는 머신의 현지 표준 시간대에 있는 것으로 간주됩니다. 예를 들어 `2020-08-19T15:04:00Z`는 UTC 시간을 나타내며 `2020-08-19`는 현지 표준 시간대로 자정(00:00)을 나타냅니다. AzCopy 10.5에서는 이 플래그는 폴더가 아닌 파일에 적용되므로 이 플래그를 `--preserve-smb-info` 또는 `--preserve-permissions`와 함께 사용할 때에는 폴더 속성이 복사되지 않습니다.

 **--include-before** 문자열: 지정된 날짜/시간 이전에 수정된 파일만 포함합니다. 값은 ISO8601 형식이어야 합니다. 표준 시간대가 지정되지 않은 경우 이 값은 AzCopy를 실행하는 머신의 현지 표준 시간대에 있는 것으로 간주됩니다. 예를 들어 `2020-08-19T15:04:00Z`는 UTC 시간을 나타내며 `2020-08-19`는 현지 표준 시간대로 자정(00:00)을 나타냅니다. AzCopy 10.7에서는 이 플래그는 폴더가 아닌 파일에 적용되므로 이 플래그를 `--preserve-smb-info` 또는 `--preserve-permissions`와 함께 사용할 때에는 폴더 속성이 복사되지 않습니다.

**--include-attributes** 문자열: (Windows에만 해당)해당 특성이 특성 목록과 일치하는 파일을 포함합니다. 예: A;S;R

**--include-path** 문자열: 복사 시 해당 경로만 포함합니다. 이 옵션은 와일드카드 문자(*)를 지원하지 않습니다. 상대 경로 접두사(예: `myFolder;myFolder/subDirName/file.pdf`)를 확인합니다.

**--include-directory-stub**               디렉터리 스텁을 무시하려면 기본적으로 False입니다. 디렉터리 스텁은 'hdi_isfolder:true' 메타데이터가 있는 Blob입니다. 값을 true로 설정하면 전송 중에 디렉터리 스텁이 유지됩니다.

**--include-pattern** 문자열: 복사 시 해당 파일만 포함합니다. 이 옵션은 와일드카드 문자(*)를 지원합니다. `;`을 사용하여 파일을 구분합니다.

**--include-regex** 문자열 정규식과 일치하는 파일의 상대 경로만 포함합니다. 정규식을 ';'로 구분합니다.

**--list-of-versions** 문자열: 각 버전 ID가 별도의 줄에 나열되는 파일을 지정합니다. 원본이 단일 Blob을 가리켜야 하며 이 플래그를 사용하여 파일에 지정된 모든 버전 ID가 원본 Blob에만 속해야 합니다. AzCopy가 지정된 버전을 제공된 대상 폴더에 다운로드합니다. 자세한 내용은 [이전 버전의 Blob 다운로드](./storage-use-azcopy-v10.md#transfer-data)를 참조하세요.

**--log-level** 문자열: 로그 파일에서 로그의 세부 정보 표시 수준을 정의합니다. 예: INFO(모든 요청/응답), WARNING(느린 응답), ERROR(실패하 요청만) 및 NONE(출력 로그 없음). (기본값 `INFO`).

**--metadata** 문자열: 해당 키-값 쌍을 메타데이터로 하여 Azure Storage에 업로드합니다.

**--no-guess-mime-type**: AzCopy가 파일의 확장명 또는 콘텐츠를 기준으로 콘텐츠 형식을 검색하지 못하도록 방지합니다.

**--overwrite** 문자열: 이 플래그가 true로 설정된 경우 대상에서 충돌하는 파일 및 Blob을 덮어씁니다. (기본값 `true`) 가능한 값은 `true`, `false`, `prompt` 및 `ifSourceNewer`입니다. 폴더를 지원하는 대상의 경우 이 플래그가 `true`이거나 폴더에 긍정적인 응답이 제공되면 충돌하는 폴더 수준 속성을 덮어씁니다. (기본값 "true")

**--page-blob-tier** 문자열: 이 Blob 계층을 사용하여 페이지 Blob을 Azure Storage에 업로드합니다. (기본값 `None`). (기본값 "None")

**--preserve-last-modified-time**: 대상이 파일 시스템인 경우에만 사용할 수 있습니다.

**--preserve-owner**: 다운로드에만 적용되며 `--preserve-permissions`이 사용되는 경우에만 해당합니다. true(기본값)로 설정된 경우 다운로드 시 파일 소유자 및 그룹이 유지됩니다. false로 설정된 경우 `--preserve-permissions`는 여전히 ACL을 유지하지만 소유자 및 그룹은 AzCopy를 실행하는 사용자(기본값 true)에 따라 달라집니다.

**--smb-정보를 유지**   합니다.   기본적으로 True입니다. SMB 인식 리소스(Windows 및 Azure Files) 간에 SMB 속성 정보(마지막으로 쓴 시간, 만든 시간, 특성 비트)를 유지합니다. Azure Files에서 지원하는 특성 비트만 전송되고 다른 모든 항목은 무시됩니다. 이 플래그는 파일 전용 필터(예: include-pattern)가 지정되지 않는 한 파일과 폴더에 모두 적용됩니다. 폴더에 대해 전송되는 정보는 폴더에 대해 유지되지 않는 마지막으로 쓴 시간을 제외하면 파일에 대해 전송되는 정보와 동일합니다.

**--유지-권한**                False 이면 기본적으로 False입니다. 인식 리소스 (Windows 및 Azure Files 또는 Data Lake Storage gen 2 Data Lake Storage gen 2) 간에 acl을 유지 합니다. 계층 네임 스페이스가 있는 계정의 경우 소유권 수정 및 권한 수정 권한이 있는 컨테이너 SAS 또는 OAuth 토큰이 필요 합니다. 다운로드를 위해 새 소유자가 AzCopy를 실행 하는 사용자가 아닌 경우에는--backup 플래그를 사용 하 여 사용 권한을 복원 해야 합니다. 이 플래그는 파일 전용 필터가 지정 된 경우를 제외 하 고 파일 및 폴더에 모두 적용 됩니다 (예: include-패턴).

**--put-md5**: 각 파일의 MD5 해시를 만들고 해당 해시를 대상 Blob 또는 파일의 Content-MD5 속성으로 저장합니다. (기본적으로 해시는 생성되지 않습니다.) 업로드 시에만 사용할 수 있습니다.

**--recursive**: 로컬 파일 시스템에서 업로드할 때 하위 디렉터리를 재귀적으로 확인합니다.

**--s2s-detect-source-changed**: 원본 파일/Blob을 읽는 중에 변경이 발생하는지 탐지합니다. (이 매개 변수는 서비스 간 복사본에만 적용됩니다. 업로드 및 다운로드의 경우에 해당 확인을 사용하도록 영구적으로 설정되어 있기 때문입니다.)

**--s2s-handle-invalid-metadata** 문자열: 잘못된 메타데이터 키가 처리되는 방식을 지정합니다. 사용 가능한 옵션은 ExcludeIfInvalid, FailIfInvalid, RenameIfInvalid입니다. (기본값 `ExcludeIfInvalid`).

**--s2s-preserve-access-tier**: 서비스 간 복사가 진행되는 동안 액세스 계층을 유지합니다. 대상 스토리지 계정이 액세스 계층 설정을 지원하도록 하려면 [Azure Blob Storage: 핫, 쿨 및 보관 액세스 계층](../blobs/storage-blob-storage-tiers.md)을 참조하세요. 액세스 계층 설정이 지원되지 않는 경우에는 s2sPreserveAccessTier = false를 사용하여 액세스 계층 복사를 건너뜁니다. (기본값 `true`).

**--s2s--blob-태그**               한 blob 저장소에서 다른 blob 저장소로 서비스를 전송 하는 동안 인덱스 태그를 유지 합니다.

**--s2s-preserve-properties**: 서비스 간 복사가 진행되는 동안 전체 속성을 유지합니다. AWS S3 및 Azure File 비단일 파일 원본의 경우 목록 작업에서 개체 및 파일의 전체 속성이 반환되지 않습니다. 전체 속성을 유지하려면 AzCopy는 개체 또는 파일당 하나의 추가 요청을 전송해야 합니다. (기본값 true)

## <a name="options-inherited-from-parent-commands"></a>부모 명령에서 상속된 옵션

**--cap-mbps float**: 전송 속도(초당 메가비트)의 한도를 지정합니다. 시간 경과별 처리량은 한도와 약간 다를 수 있습니다. 이 옵션이 0으로 설정되거나 생략되는 경우 처리량이 제한되지 않습니다.

**--output-type** 문자열   명령의 출력 형식입니다. text, json 등을 선택할 수 있습니다. 기본값은 `text`입니다. (기본값 "text")

**--trusted-microsoft-suffixes** 문자열: Azure Active Directory 로그인 토큰이 전송될 수 있는 추가 도메인 접미사를 지정합니다.  기본값은 `*.core.windows.net;*.core.chinacloudapi.cn;*.core.cloudapi.de;*.core.usgovcloudapi.net`입니다. 여기에 나열된 항목은 모두 기본값에 추가됩니다. 보안을 위해 여기에 Microsoft Azure 도메인만 배치해야 합니다. 여러 항목은 세미콜론으로 구분합니다.

## <a name="see-also"></a>참조

- [azcopy](storage-ref-azcopy.md)
