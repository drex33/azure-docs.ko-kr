---
title: Azure Data Box를 사용하여 파일 ACL, 특성, 타임스탬프 유지
description: SMB를 통해 데이터를 Azure Data Box에 복사하는 동안 유지되는 ACL, 타임스탬프, 특성입니다. Windows 및 Linux 데이터 복사 도구를 사용하여 메타데이터를 복사합니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: conceptual
ms.date: 07/16/2021
ms.author: alkohli
ms.openlocfilehash: 96d3957a7626e393728d4a309bc56ecaa19d4e83
ms.sourcegitcommit: 8669087bcbda39e3377296c54014ce7b58909746
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/18/2021
ms.locfileid: "114400924"
---
# <a name="preserving-file-acls-attributes-and-timestamps-with-azure-data-box"></a>Azure Data Box를 사용하여 파일 ACL, 특성, 타임스탬프 유지

Azure Data Box를 사용하면 Azure에 데이터를 보낼 때 ACL(액세스 제어 목록), 타임스탬프 및 파일 특성을 유지할 수 있습니다. 이 문서에서는 SMB(서버 메시지 블록)를 통해 데이터를 Data Box에 복사하여 Azure Files로 업로드할 때 전송할 수 있는 메타데이터에 대해 설명합니다. 

Windows 및 Linux 데이터 복사 도구를 사용하여 메타데이터를 복사하기 위한 특정 단계가 제공됩니다. 데이터를 Blob 스토리지로 전송할 때는 메타데이터가 유지되지 않습니다.

이 문서에서는 전송되는 ACL, 타임스탬프, 파일 특성을 총체적으로 ‘메타데이터’라고 합니다.

## <a name="transferred-metadata"></a>전송되는 메타데이터

Data Box의 데이터를 Azure Files로 업로드할 때 전송되는 메타데이터는 다음과 같습니다.

#### <a name="timestamps"></a>타임스탬프

다음 타임스탬프가 전송됩니다.
- CreationTime
- LastWriteTime

다음 타임스탬프는 전송되지 않습니다.
- LastAccessTime
  
#### <a name="file-attributes"></a>파일 특성

다른 설명이 없는 한 파일 및 디렉터리의 파일 특성은 모두 전송됩니다.

다음 파일 특성이 전송됩니다.
- FILE_ATTRIBUTE_READONLY(파일만)
- FILE_ATTRIBUTE_HIDDEN
- FILE_ATTRIBUTE_SYSTEM
- FILE_ATTRIBUTE_DIRECTORY(디렉터리만)
- FILE_ATTRIBUTE_ARCHIVE
- FILE_ATTRIBUTE_TEMPORARY(파일만)
- FILE_ATTRIBUTE_NO_SCRUB_DATA

다음 파일 특성은 전송되지 않습니다.
- FILE_ATTRIBUTE_OFFLINE
- FILE_ATTRIBUTE_NOT_CONTENT_INDEXED
  
디렉터리의 읽기 전용 특성은 전송되지 않습니다.

#### <a name="acls"></a>ACL

SMB를 통해 Data Box에 복사하는 디렉터리 및 파일에 대한 모든 ACL이 복사되고 전송됩니다. 전송에는 DACL(임의 ACL) 및 SACL(시스템 ACL)이 모두 포함됩니다. Linux의 경우 Windows NT ACL만 전송됩니다.

NTS(네트워크 파일 시스템)를 통해 데이터를 복사하거나 데이터 복사 서비스를 사용하여 데이터를 전송할 때는 ACL이 전송되지 않습니다. 데이터 복사 서비스는 공유에서 직접 데이터를 읽으며 ACL을 읽을 수 없습니다.

데이터 복사 도구가 ACL을 복사하지 않는 경우에도 디렉터리 및 파일에 대한 기본 ACL은 Azure Files로 전송됩니다. 기본 ACL은 기본 제공 관리자 계정, SYSTEM 계정 및 Data Box 데이터를 탑재하고 복사하는 데 사용된 SMB 공유 사용자 계정의 권한을 포함합니다.

ACL에는 ACL, 소유자, 그룹, SACL 속성이 있는 보안 설명자가 포함됩니다.

ACL 전송은 기본적으로 사용하도록 설정되어 있습니다. Data Box의 로컬 웹 UI에서 이 설정을 사용하지 않도록 설정할 수 있습니다. 자세한 내용은 [로컬 웹 UI를 사용하여 Data Box 및 Data Box Heavy 관리](./data-box-local-web-ui-admin.md)를 참조하세요.

> [!NOTE]
> 조건부 ACE(액세스 제어 항목) 문자열을 포함하는 ACL이 있는 파일은 복사되지 않습니다. 이것은 알려진 문제입니다. 해당 이슈를 해결하려면 공유를 탑재한 다음 ACL 복사를 지원하는 복사 도구를 사용하여 Azure Files 공유에 파일을 수동으로 복사합니다.

## <a name="copying-data-and-metadata"></a>데이터 및 메타데이터 복사

데이터의 ACL, 타임스탬프, 특성을 전송하려면 다음 절차를 사용하여 Data Box에 데이터를 복사합니다. 

### <a name="windows-data-copy-tool"></a>Windows 데이터 복사 도구

SMB를 통해 Data Box 데이터를 복사하려면 `robocopy`와 같은 SMB 호환 파일 복사 도구를 사용합니다. 다음 샘플 명령은 모든 파일 및 디렉터리를 복사하고 데이터와 함께 메타데이터를 전송합니다.

`/copyall` 또는 `/dcopy:DAT` 옵션을 사용하는 경우 필수 백업 운영자 권한이 사용하도록 설정되어 있는지 확인합니다. 자세한 내용은 [로컬 웹 UI를 사용하여 Data Box 및 Data Box Heavy 관리](./data-box-local-web-ui-admin.md)를 참조하세요. 

```console
robocopy <Source> <Target> * /copyall /e /dcopy:DAT /r:3 /w:60 /is /nfl /ndl /np /MT:32 or 64 /fft /log+:<LogFile>
```

라는 설치 관리자 실행 파일에 포함됩니다. 여기서

|옵션 |설명 |
|------------------- | ----- |
|`/copyall` |모든 특성을 복사합니다.|
|`/e`      |빈 디렉터리를 포함하여 하위 디렉터리를 복사합니다.         |
|`/dcopy:DAT`  |데이터, 특성, 타임스탬프를 복사합니다. 참고: 디렉터리의 `CreationTime`을 전송하려면 /dcopy:DAT 옵션을 사용해야 합니다. |
|`/r:3`    |실패한 복사를 3번 다시 시도하도록 지정합니다.         |
|`/w:60`   |다시 시도 간격으로 60초 대기 시간을 지정합니다.         |
|`/is`     |동일한 파일을 포함합니다.         |
|`/nfl`    |파일 이름을 로깅하지 않습니다.         |
|`/ndl`    |디렉터리 이름을 로깅하지 않습니다.        |
|`/np`     |복사 작업의 진행률을 표시하지 않습니다.         |
|`/MT:32 or 64`  |32개 또는 64개 스레드로 다중 스레딩을 사용합니다.           |
|`/fft`    |파일 시스템의 타임스탬프 세분성을 줄입니다.        |
|`/log+:<LogFile>`  |기존 로그 파일에 출력을 추가합니다.|

`robocopy` 매개 변수에 대한 자세한 내용은 [자습서: SMB를 통해 Azure Data Box에 데이터 복사](./data-box-deploy-copy-data.md)를 참조하세요.

> [!NOTE]
> `/copyall`을 사용하여 데이터를 복사하면 디렉터리와 파일에 대한 원본 ACL이 Azure Files로 전송됩니다. 원본 데이터에 대한 읽기 권한만 있고 원본 데이터를 수정할 수 없는 경우 Data Box의 데이터에 대한 읽기 권한만 갖게 됩니다. 디렉터리와 파일에 대한 모든 ACL을 데이터와 함께 복사하려는 경우에만 `/copyall`을 사용합니다.

#### <a name="use-robocopy-to-list-copy-modify-files-on-data-box"></a>robocopy를 사용하여 Data Box의 파일 나열, 복사, 수정

`robocopy`를 사용하여 데이터를 복사할 때 사용하는 몇 가지 일반적인 시나리오는 다음과 같습니다.

- **데이터만 Data Box에 복사, 디렉터리와 파일에 대한 ACL 복사 안 함**

    데이터, 특성, 타임스탬프만 복사하려면 `/dcopy:DAT` 옵션을 사용합니다. 디렉터리와 파일에 대한 ACL은 복사되지 않습니다.

- **데이터 및 디렉터리와 파일에 대한 ACL을 Data Box에 복사**

    디렉터리와 파일에 대한 모든 ACL을 포함하여 모든 원본 데이터를 복사하려면 `/copyall`을 사용합니다.

- **robocopy를 사용하여 Data Box의 파일 시스템 나열**

    디렉터리 내용을 나열하려면 다음 명령을 사용합니다.

    `robocopy <source-dir> NULL /l /s /xx /njh /njs /fp /B`

    파일 탐색기에서는 해당 파일을 나열할 수 없습니다.
    
- **Data Box의 폴더와 파일 복사 또는 삭제**

    단일 파일을 복사하려면 다음 명령을 사용합니다.

    `robocopy <source-dir> <destination-dir> <file-name> /B`

    단일 파일을 삭제하려면 다음 명령을 사용합니다.

    `robocopy <source-dir> <destination-dir> <file-name> /purge /B`

    위 명령에서 `<source-dir>`에는 `<file-name>` 파일이 없어야 합니다. 위 명령은 대상을 원본과 동기화하므로 대상에서 해당 파일이 제거됩니다.

    파일 탐색기에서는 위 작업을 수행하지 못할 수도 있습니다.

자세한 내용은 [robocopy 명령 사용](/windows-server/administration/windows-commands/robocopy)을 참조하세요.

### <a name="linux-data-copy-tool"></a>Linux 데이터 복사 도구

Linux에서 메타데이터 전송은 2단계 프로세스로 진행됩니다. 먼저 메타데이터를 복사하지 않는 `rsync`와 같은 도구를 사용하여 원본 데이터를 복사합니다. 데이터를 복사한 후 `smbcacls` 또는 `cifsacl`과 같은 도구를 사용하여 메타데이터를 복사할 수 있습니다. 

다음 샘플 명령은 `rsync`를 사용하여 데이터를 복사하는 첫 번째 단계를 수행합니다. 

```console
cp -aR /etc /opt/ 
rsync -avP /etc /opt (-a copies a directory)
```

## <a name="next-steps"></a>다음 단계

- [SMB를 통해 Azure Data Box에 데이터 복사](./data-box-deploy-copy-data.md)
