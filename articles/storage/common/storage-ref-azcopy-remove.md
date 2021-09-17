---
title: azcopy remove | Microsoft Docs
description: 이 문서에서는 azcopy remove 명령에 대한 참조 정보를 제공합니다.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 09/21/2021
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 8556dc719aa8abcfa1506b4d1147c29959b32746
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/03/2021
ms.locfileid: "123427627"
---
# <a name="azcopy-remove"></a>azcopy remove

Azure Storage 계정에서 Blob 또는 파일을 삭제합니다.

## <a name="synopsis"></a>개요

```azcopy
azcopy remove [resourceURL] [flags]
```

## <a name="related-conceptual-articles"></a>관련 개념 문서

- [AzCopy 시작](storage-use-azcopy-v10.md)
- [AzCopy 및 Blob Storage를 사용하여 데이터 전송](./storage-use-azcopy-v10.md#transfer-data)
- [AzCopy 및 File Storage를 사용하여 데이터 전송](storage-use-azcopy-files.md)

## <a name="examples"></a>예

SAS 토큰을 사용하여 단일 Blob 제거:

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/blob]?[SAS]"
```

SAS 토큰을 사용하여 전체 가상 디렉터리를 제거합니다.
 
```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive=true
```

가상 디렉터리에 있는 Blob만 제거하고, 하위 디렉터리나 하위 디렉터리에 있는 Blob은 제거하지 않습니다.

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --recursive=false
```

가상 디렉터리에서 Blob의 하위 집합을 제거합니다(예: jpg 및 pdf 파일만 제거, 또는 Blob 이름이 `exactName`인 경우).

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive=true --include-pattern="*.jpg;*.pdf;exactName"
```

전체 가상 디렉터리를 제거하되 특정 Blob을 범위에서 제외합니다(예: foo로 시작하거나 bar로 끝나는 모든 파일).

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive=true --exclude-pattern="foo*;*bar"
```

관련 상대 경로(URL 인코딩되지 않음)를 파일에 넣어 특정 Blob과 가상 디렉터리를 제거합니다.

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/parent/dir]" --recursive=true --list-of-files=/usr/bar/list.txt
- file content:
    dir1/dir2
    blob1
    blob2
```
계층 구조 네임스페이스가 있는 Blob Storage 계정에서 단일 파일을 제거합니다(포함/제외는 지원되지 않음).

```azcopy
azcopy rm "https://[account].dfs.core.windows.net/[container]/[path/to/file]?[SAS]"
```

계층 구조 네임스페이스가 있는 Blob Storage 계정에서 단일 디렉터리를 제거합니다(포함/제외는 지원되지 않음).

```azcopy
azcopy rm "https://[account].dfs.core.windows.net/[container]/[path/to/directory]?[SAS]"
```

## <a name="options"></a>옵션

**--delete-snapshots** 문자열   기본적으로 Blob에 스냅샷이 있으면 삭제 작업이 실패하게 됩니다. 루트 Blob과 관련 스냅샷을 모두 제거하려면 `include`를 지정합니다. 스냅샷만 제거하고 루트 Blob은 유지하려면 `only`를 지정합니다.

**--dry-run**                   명령에 의해 제거될 경로 파일을 인쇄합니다. 이 플래그는 파일 제거를 트리거하지 않습니다.

**--exclude-path** 문자열   제거할 때 해당 경로를 제외합니다. 이 옵션은 와일드카드 문자(*)를 지원하지 않습니다. 상대 경로 접두사를 확인하세요. `myFolder;myFolder/subDirName/file.pdf`

**--exclude-pattern** 문자열   이름이 패턴 목록과 일치하는 파일을 제외합니다. 예: `*.jpg`;`*.pdf`;`exactName`

**--force-if-read-only**   Azure Files 파일이나 폴더를 삭제할 때 기존 개체에 읽기 전용 특성 모음이 있더라도 삭제가 진행되게 합니다.

**--from-to-string** 필요에 따라 원본 대상 조합을 지정합니다. 예: BlobTrash, FileTrash, BlobFSTrash

**--help**   제거에 대한 도움말입니다

**--include-path** 문자열   제거 시 해당 경로만 포함합니다. 이 옵션은 와일드카드 문자(*)를 지원하지 않습니다. 상대 경로 접두사를 확인하세요. `myFolder;myFolder/subDirName/file.pdf`

**--include-pattern** 문자열  이름이 패턴 목록과 일치하는 파일만 포함합니다. 예: *`.jpg`;* `.pdf`;`exactName`

**--list-of-files** 문자열  삭제할 파일 및 디렉터리 목록을 포함하는 파일의 위치를 정의합니다. 상대 경로는 줄 바꿈으로 구분되어야 하며 경로는 URL 인코딩되지 않아야 합니다. 

**--list-of-versions** 문자열   각 버전 ID가 별도의 줄에 나열되는 파일을 지정합니다. 원본이 단일 Blob을 가리켜야 하며 이 플래그를 사용하여 파일에 지정된 모든 버전 ID가 원본 Blob에만 속해야 합니다. 지정된 Blob의 지정된 버전 ID가 Azure Storage에서 삭제됩니다. 

**--log-level** 문자열   로그 파일의 로그 세부 정보 표시를 정의합니다. 대표적인 사용 가능한 수준은 `INFO`(모든 요청/응답), `WARNING`(느린 응답), `ERROR`(실패한 요청만), `NONE`(출력 로그 없음)입니다. (기본값 `INFO`) (기본값 `INFO`)

**--recursive**    디렉터리 간에 동기화할 때 하위 디렉터리를 재귀적으로 확인합니다.

## <a name="options-inherited-from-parent-commands"></a>부모 명령에서 상속된 옵션

|옵션|Description|
|---|---|
|--cap-mbps float|전송 속도(초당 메가비트)의 한도를 지정합니다. 시간 경과별 처리량은 한도와 약간 다를 수 있습니다. 이 옵션이 0으로 설정되거나 생략되는 경우 처리량이 제한되지 않습니다.|
|--output-type string|명령 출력의 형식을 지정합니다. text, json 등을 선택할 수 있습니다. 기본값은 “text”입니다.|
|--trusted-microsoft-suffixes string   |Azure Active Directory 로그인 토큰이 전송될 수 있는 추가 도메인 접미사를 지정합니다.  기본값은 ‘ *.core.windows.net;* .core.chinacloudapi.cn; *.core.cloudapi.de;* .core.usgovcloudapi.net’입니다. 여기에 나열된 항목은 모두 기본값에 추가됩니다. 보안을 위해 여기에 Microsoft Azure 도메인만 배치해야 합니다. 여러 항목은 세미콜론으로 구분합니다.|

## <a name="see-also"></a>참고 항목

- [azcopy](storage-ref-azcopy.md)
