---
title: azcopy sync | Microsoft Docs
description: 이 문서에서는 azcopy sync 명령에 대한 참조 정보를 제공합니다.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 09/01/2021
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 9ba814b4188984f3a0f6dbe16e866d6184d8cf2b
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128547664"
---
# <a name="azcopy-sync"></a>azcopy sync

원본 위치를 대상 위치로 복제합니다. 이 문서에서는 azcopy sync 명령에 대한 자세한 참조를 제공합니다. 원본과 대상 위치 간의 Blob 동기화에 대한 자세한 내용은 [AzCopy v10을 사용하여 Azure Blob Storage와 동기화](storage-use-azcopy-blobs-synchronize.md)를 참조하세요. Azure Files의 경우 [파일 동기화](storage-use-azcopy-files.md#synchronize-files)를 참조하세요.

## <a name="synopsis"></a>개요

마지막으로 수정한 시간이 비교에 사용됩니다. 대상에서 마지막으로 수정한 시간이 더 최근인 경우 파일을 건너뜁니다.

지원되는 쌍은 다음과 같습니다.

- 로컬 <-> Azure Blob(SAS 또는 OAuth 인증을 사용할 수 있음)
- Azure Blob <-> Azure Blob(원본에 SAS가 포함되어 있거나 공개적으로 액세스할 수 있어야 함. 대상에 SAS 또는 OAuth 인증을 사용할 수 있음)
- Azure 파일 <-> Azure 파일(원본에 SAS가 포함되어 있거나 공개적으로 액세스할 수 있어야 함. 대상에 SAS 인증을 사용해야 함)

sync 명령은 다음과 같은 점에서 copy 명령과 다릅니다.

1. 기본적으로 재귀 플래그는 true이며 sync는 모든 하위 디렉터리를 복사합니다. Sync는 재귀 플래그가 false인 경우 디렉터리 내의 최상위 파일만 복사합니다.
2. 가상 디렉터리 간에 동기화할 때 가상 디렉터리 중 하나와 이름이 같은 Blob이 있는 경우 경로에 후행 슬래시를 추가합니다(예 참조).
3. `deleteDestination` 플래그가 true 또는 prompt로 설정된 경우 sync는 대상에서 원본에 없는 파일 및 Blob을 삭제합니다.

## <a name="related-conceptual-articles"></a>관련 개념 문서

- [AzCopy 시작](storage-use-azcopy-v10.md)
- [자습서: AzCopy를 사용하여 클라우드 스토리지로 온-프레미스 데이터 마이그레이션](storage-use-azcopy-migrate-on-premises-data.md)
- [AzCopy 및 Blob Storage를 사용하여 데이터 전송](./storage-use-azcopy-v10.md#transfer-data)
- [AzCopy 및 File Storage를 사용하여 데이터 전송](storage-use-azcopy-files.md)

### <a name="advanced"></a>고급

파일 확장명을 지정하지 않은 경우 AzCopy는 파일 확장명 또는 콘텐츠(확장명이 지정되지 않은 경우)에 따라 로컬 디스크에서 업로드할 때 파일의 콘텐츠 유형을 자동으로 감지합니다.

기본 제공 조회 테이블은 작지만 Unix에서는 다음 이름 중 하나 이상의 이름으로 제공되는 경우 로컬 시스템의 mime.types 파일에 의해 확대됩니다.

- /etc/mime.types
- /etc/apache2/mime.types
- /etc/apache/mime.types

Windows에서는 레지스트리에서 MIME 형식이 추출됩니다.

```azcopy
azcopy sync <source> <destination> [flags]
```

## <a name="examples"></a>예

단일 파일을 동기화합니다.

```azcopy
azcopy sync "/path/to/file.txt" "https://[account].blob.core.windows.net/[container]/[path/to/blob]"
```

위와 동일하지만 파일 콘텐츠의 MD5 해시도 계산한 다음 해당 MD5 해시를 Blob의 Content-MD5 속성으로 저장합니다.

```azcopy
azcopy sync "/path/to/file.txt" "https://[account].blob.core.windows.net/[container]/[path/to/blob]" --put-md5
```

하위 디렉터리를 포함하여 전체 디렉터리를 동기화합니다(재귀는 기본적으로 설정).

```azcopy
azcopy sync "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]"
```

또는

```azcopy
azcopy sync "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --put-md5
```

디렉터리 내부의 파일만 동기화하고 하위 디렉터리 또는 하위 디렉터리 내부의 파일은 동기화하지 않습니다.

```azcopy
azcopy sync "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --recursive=false
```

디렉터리에 있는 파일의 하위 집합을 동기화합니다(예: jpg 및 pdf 파일만 또는 파일 이름이 `exactName`인 경우).

```azcopy
azcopy sync "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --include-pattern="*.jpg;*.pdf;exactName"
```

전체 디렉터리를 동기화하되 특정 파일을 범위에서 제외합니다(예 : foo로 시작하거나 bar로 끝나는 모든 파일).

```azcopy
azcopy sync "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --exclude-pattern="foo*;*bar"
```

단일 Blob을 동기화합니다.

```azcopy
azcopy sync "https://[account].blob.core.windows.net/[container]/[path/to/blob]?[SAS]" "https://[account].blob.core.windows.net/[container]/[path/to/blob]"
```

가상 디렉터리를 동기화합니다.

```azcopy
azcopy sync "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]?[SAS]" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --recursive=true
```

Blob과 이름이 같은 가상 디렉터리를 동기화합니다(정확성을 위해 경로에 후행 슬래시 추가).

```azcopy
azcopy sync "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]/?[SAS]" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]/" --recursive=true
```

Azure File 디렉터리 동기화:

```azcopy
azcopy sync "https://[account].file.core.windows.net/[share]/[path/to/dir]?[SAS]" "https://[account].file.core.windows.net/[share]/[path/to/dir]?[SAS]" --recursive=true
```

> [!NOTE]
> include/exclude 플래그를 함께 사용하면 포함 패턴과 일치하는 파일만 검색되지만 제외 패턴과 일치하는 파일은 항상 무시됩니다.

## <a name="options"></a>옵션

**--block-size-mb** 부동    Azure Storage로 업로드하거나 Azure Storage에서 다운로드할 때 이 블록 크기(MiB로 지정됨)를 사용합니다. 기본값은 파일 크기에 따라 자동으로 계산됩니다. 소수를 사용할 수 있습니다(예: `0.25`).

**--check-md5** 문자열   다운로드 시 MD5 해시의 유효성을 얼마나 엄격하게 검사해야 하는지 지정합니다. 이 옵션은 다운로드할 때만 사용할 수 있습니다. `NoCheck`, `LogOnly`, `FailIfDifferent`, `FailIfDifferentOrMissing` 등의 값을 사용할 수 있습니다. (기본값 `FailIfDifferent`). (기본값 `FailIfDifferent`)

**--cpk-by-name** 문자열 클라이언트가 이름으로 키를 제공하므로 Azure Blob에 대한 요청을 하는 클라이언트는 요청별로 암호화 키를 제공하는 옵션을 Storage 있습니다. 제공된 키 이름은 Azure Key Vault 가져와 데이터를 암호화하는 데 사용됩니다.

**--cpk-by-value**                클라이언트가 이름으로 제공한 키를 사용하면 Azure Blob에 대한 요청을 하는 클라이언트가 요청별로 암호화 키를 제공하는 옵션을 Storage 수 있습니다. 제공된 키 및 해당 해시는 환경 변수에서 페치됩니다.

**--delete-destination** 문자열   원본에 없는 추가 파일을 대상에서 삭제할지 여부를 정의합니다. `true`, `false` 또는 `prompt`로 설정할 수 있습니다. `prompt`로 설정하면 삭제할 파일 및 Blob을 예약하기 전에 사용자에게 질문이 표시됩니다. (기본값 `false`). (기본값 `false`)

**--dry-run**                     동기화 명령으로 복사하거나 제거할 파일의 경로를 인쇄합니다. 이 플래그는 실제 파일을 복사하거나 제거하지 않습니다.

**--exclude-attributes** 문자열   (Windows에만 해당) 해당 특성이 특성 목록과 일치하는 파일을 제외합니다. `A;S;R`

**--exclude-path** 문자열    원본과 대상을 비교할 때 해당 경로를 제외합니다. 이 옵션은 와일드카드 문자(*)를 지원하지 않습니다. 상대 경로 접두사(예: `myFolder;myFolder/subDirName/file.pdf`)를 확인합니다.

**--exclude-pattern** 문자열   이름이 패턴 목록과 일치하는 파일을 제외합니다. `*.jpg;*.pdf;exactName`

**--exclude-regex** 문자열 정규식과 일치하는 파일의 상대 경로를 제외합니다. 정규식을 ';'로 구분합니다.

**--help**    동기화에 대한 도움말이 표시됩니다.

**--include-attributes** 문자열   (Windows에만 해당)속성이 속성 목록과 일치하는 파일만 포함합니다. `A;S;R`

**--include-pattern** 문자열   이름이 패턴 목록과 일치하는 파일만 포함합니다. `*.jpg;*.pdf;exactName`

**--log-level** 문자열      `INFO`(모든 요청 및 응답), `WARNING`(느린 응답), `ERROR`(실패한 요청만) 및 `NONE`(출력 로그 없음) 등 로그 파일의 로그 세부 정보 표시 수준을 정의합니다. (기본값 `INFO`).

**--mirror-mode**  마지막 수정 시간 기반 비교를 사용하지 않도록 설정하고 이 플래그가 `true`로 설정된 경우 대상에서 충돌하는 파일 및 Blob을 덮어씁니다. 기본값은 `false`입니다.

**--preserve-smb-info**   기본적으로 True입니다. SMB 인식 리소스(Windows 및 Azure Files) 간에 SMB 속성 정보(마지막으로 쓴 시간, 만든 시간, 특성 비트)를 유지합니다. 이 플래그는 파일 전용 필터(예: include-pattern)가 지정되지 않는 한 파일과 폴더에 모두 적용됩니다. 폴더에 대해 전송되는 정보는 폴더에 대해 유지되지 않는 마지막으로 쓴 시간을 제외하면 파일에 대해 전송되는 정보와 동일합니다.

**--preserve-permissions**        기본적으로 False입니다. 인식 리소스(Windows 및 Azure Files 또는 Data Lake Storage Gen 2에서 Data Lake Storage Gen 2로) 간의 ACL을 유지합니다. 계층 구조 네임스페이스가 있는 계정의 경우 소유권 수정 및 권한 수정 권한이 있는 컨테이너 SAS 또는 OAuth 토큰이 필요합니다. 다운로드의 경우 새 소유자가 AzCopy를 실행하는 사용자가 아닌 권한을 복원하려면 --backup 플래그도 필요합니다. 이 플래그는 파일 전용 필터(예: include-pattern)를 지정하지 않는 한 파일과 폴더 모두에 적용됩니다.

**--put-md5**     각 파일의 MD5 해시를 만들고 해당 해시를 대상 Blob 또는 파일의 Content-MD5 속성으로 저장합니다. (기본적으로 해시는 생성되지 않습니다.) 업로드 시에만 사용할 수 있습니다.

**--recursive**    기본적으로 `True`이며 디렉터리 간에 동기화할 때 하위 디렉터리를 재귀적으로 확인합니다. (기본값 `True`).

**--s2s-preserve-access-tier**  서비스 간 복사가 진행되는 동안 액세스 계층을 유지합니다. 대상 스토리지 계정이 액세스 계층 설정을 지원하도록 하려면 [Azure Blob Storage: 핫, 쿨 및 보관 액세스 계층](../blobs/storage-blob-storage-tiers.md)을 참조하세요. 액세스 계층 설정이 지원되지 않는 경우에는 s2sPreserveAccessTier = false를 사용하여 액세스 계층 복사를 건너뜁니다. (기본값 `true`).

**--s2s-preserve-blob-tags**      서비스 간 동기화 중에 한 Blob Storage에서 다른 Blob Storage로 인덱스 태그를 유지합니다.

## <a name="options-inherited-from-parent-commands"></a>부모 명령에서 상속된 옵션

|옵션|Description|
|---|---|
|--cap-mbps uint32|전송 속도(초당 메가비트)의 한도를 지정합니다. 시간 경과별 처리량은 한도와 약간 다를 수 있습니다. 이 옵션이 0으로 설정되거나 생략되는 경우 처리량이 제한되지 않습니다.|
|--output-type string|명령 출력의 형식을 지정합니다. text, json 등을 선택할 수 있습니다. 기본값은 “text”입니다.|
|--trusted-microsoft-suffixes string   |Azure Active Directory 로그인 토큰이 전송될 수 있는 추가 도메인 접미사를 지정합니다.  기본값은 ‘ *.core.windows.net;* .core.chinacloudapi.cn; *.core.cloudapi.de;* .core.usgovcloudapi.net’입니다. 여기에 나열된 항목은 모두 기본값에 추가됩니다. 보안을 위해 여기에 Microsoft Azure 도메인만 배치해야 합니다. 여러 항목은 세미콜론으로 구분합니다.|

## <a name="see-also"></a>참고 항목

- [azcopy](storage-ref-azcopy.md)
