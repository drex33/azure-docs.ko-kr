---
title: Azure Import/Export을 사용 하 여 내보내기에 필요한 드라이브 수 확인 | Microsoft Docs
description: Azure Import/Export 서비스를 사용 하 여 내보내기에 필요한 드라이브 수를 확인 합니다.
author: alkohli
services: storage
ms.service: storage
ms.topic: how-to
ms.date: 10/01/2021
ms.author: alkohli
ms.subservice: common
ms.openlocfilehash: eda9ddd4fa6fca9e4d46c5b86a7f3ab8b7c8cb03
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2021
ms.locfileid: "129717693"
---
# <a name="check-number-of-drives-needed-for-an-export-with-azure-importexport"></a>Azure Import/Export로 내보내기에 필요한 드라이브 수 확인 

이 문서에서는 Azure Import/Export 내보내기 주문에 대해 제공할 물리적 드라이브 수를 결정 하는 방법을 설명 합니다. Azure Import/Export 도구를 사용 합니다. 이 문서에서는이 도구를 설치 하 고 사용 하는 방법을 설명 합니다.

Azure Import/Export 버전 1 도구는 내보낼 모든 blob을 나열 하 고 필요한 오버 헤드를 고려 하 여 지정 된 크기의 드라이브에 패키지 하는 방법을 계산한 다음, blob 및 드라이브 사용 정보를 보유 하는 데 필요한 드라이브 수를 예측 합니다.

내보내기 순서로 2.5 "Ssd, 2.5" Hdd 또는 3.5 "Hdd을 사용할 수 있습니다. 자세한 내용은 [지원 되는 디스크](storage-import-export-requirements.md#supported-disks)를 참조 하세요.


## <a name="prerequisite"></a>필수 조건

* [지원 되는 OS 버전](storage-import-export-requirements.md#supported-operating-systems)을 실행 하는 Windows 시스템에서 도구를 사용 해야 합니다.

## <a name="determine-how-many-drives-you-need"></a>필요한 드라이브 수 결정

내보내기 순서에 필요한 실제 디스크 수를 확인 하려면 다음 단계를 수행 합니다.

1. Windows 시스템에서 blob에 대 한 Azure Import/Export 버전 1 도구 (WAImportExportV1.zip)의 현재 릴리스를 다운로드 합니다.
  1. [WAImportExport 버전 1을 다운로드](https://www.microsoft.com/download/details.aspx?id=42659)합니다. 현재 버전은 1.5.0.300입니다.
  1. `waimportexportv1` 기본 폴더에 압축을 풉니다. 예들 들어 `C:\WaImportExportV1`입니다.

2. 관리 권한이 있는 PowerShell 또는 명령줄 창을 엽니다. 압축을 푼 폴더로 디렉터리를 변경하려면 다음 명령을 실행합니다.

   `cd C:\WaImportExportV1`

3. 선택한 Blob에 필요한 디스크 수를 확인하려면 다음 명령을 실행합니다.

   `WAImportExport.exe PreviewExport /ExportBlobListFile:<Path to XML blob list file> /DriveSize:<Size of drives used>`

    다음 표에는 매개 변수가 나와 있습니다.

    |명령줄 매개 변수|Description|
    |--------------------------|-----------------|
    |**/logdir:**|선택 사항입니다. 로그 디렉터리입니다. 이 디렉터리에 자세한 로그 파일이 기록됩니다. 지정하지 않으면 현재 디렉터리가 로그 디렉터리로 사용됩니다.|
    |**/ExportBlobListFile:**|필수 사항입니다. 내보낼 Blob에 대한 Blob 경로 또는 Blob 경로 접두사 목록을 포함하고 있는 XML 파일의 경로입니다. Import/Export 서비스 REST API의 [작업 배치](/rest/api/storageimportexport/jobs) 작업에서 `BlobListBlobPath` 요소에 사용되는 파일 형식입니다.|
    |**/DriveSize:**|필수 사항입니다. 내보내기 작업에 사용할 드라이브의 크기입니다(*예*: 500GB, 1.5TB).|

    [PreviewExport 명령 예제](#example-of-previewexport-command)를 참조하세요.

4. 내보내기 작업을 위해 배송될 드라이브에 읽거나 쓸 수 있는지 확인합니다.

## <a name="example-of-previewexport-command"></a>PreviewExport 명령 예제

다음 예제에서는 `PreviewExport` 명령을 보여 줍니다.

```powershell
    WAImportExport.exe PreviewExport /ExportBlobListFile:C:\WAImportExport\mybloblist.xml /DriveSize:500GB
```

내보내기 Blob 목록 파일에는 다음과 같이 Blob 이름과 Blob 접두사가 포함될 수 있습니다.

```xml
<?xml version="1.0" encoding="utf-8"?>
<BlobList>
<BlobPath>pictures/animals/koala.jpg</BlobPath>
<BlobPathPrefix>/vhds/</BlobPathPrefix>
<BlobPathPrefix>/movies/</BlobPathPrefix>
</BlobList>
```

### <a name="examples-of-valid-blob-paths"></a>유효한 Blob 경로의 예

다음 표에서는 &lt; &gt; &lt; &gt; blob 목록 파일에서 blobpath 및 blobpathprefix 태그와 함께 사용할 올바른 blob 경로 및 blob 경로 접두사의 예를 보여 줍니다.

* *경로* 는 단일 blob 또는 파일을 선택 하 고 필터링 합니다.
* *접두사* 는 여러 blob 또는 여러 파일을 선택 하 고 필터링 합니다.

   | 선택기 | Blob 경로/접두사 | Description |
   | --- | --- | --- |
   | 시작 단어 |/ |스토리지 계정의 모든 Blob을 내보냄 |
   | 시작 단어 |/$root/ |루트 컨테이너의 모든 Blob을 내보냄 |
   | 시작 단어 |/book |접두사 **book** |
   | 시작 단어 |/music/ |컨테이너 **music** |
   | 시작 단어 |/music/love |접두사 **love** 로 시작하는 컨테이너 **music** 의 모든 Blob을 내보냄 |
   | 같음 |$root/logo.bmp |루트 컨테이너의 Blob **logo.bmp** 를 내보냄 |
   | 같음 |videos/story.mp4 |컨테이너 **videos** 의 Blob **story.mp4** 를 내보냄 |


### <a name="sample-output"></a>샘플 출력

정보 로그가 생략된 출력의 예제는 다음과 같습니다.

```powershell
Number of unique blob paths/prefixes:   3
Number of duplicate blob paths/prefixes:        0
Number of nonexistent blob paths/prefixes:      1

Drive size:     500.00 GB
Number of blobs that can be exported:   6
Number of blobs that cannot be exported:        2
Number of drives needed:        3
        Drive #1:       blobs = 1, occupied space = 454.74 GB
        Drive #2:       blobs = 3, occupied space = 441.37 GB
        Drive #3:       blobs = 2, occupied space = 131.28 GB
```

## <a name="next-steps"></a>다음 단계

- [Azure Blob storage에서 blob 내보내기](storage-import-export-data-from-blobs.md)
