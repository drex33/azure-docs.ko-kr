---
title: Azure Data Box 디스크 제한 | Microsoft Docs
description: Microsoft Azure Data Box Disk의 시스템 제한 및 권장 크기를 설명합니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: article
ms.date: 09/28/2021
ms.author: alkohli
ms.openlocfilehash: ab759cf81ce95ca7cf61a3777092fd92040baf6c
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/29/2021
ms.locfileid: "129208479"
---
# <a name="azure-data-box-disk-limits"></a>Azure Data Box Disk 제한


Microsoft Azure Data Box Disk 솔루션을 배포 및 운영하면서 이러한 제한을 고려합니다.

## <a name="data-box-service-limits"></a>Data Box 서비스 제한

 - Data Box 서비스는 [지역 가용성](data-box-disk-overview.md#region-availability)에 나열된 Azure 지역에서 사용할 수 있습니다.
 - 단일 스토리지 계정은 Data Box Disk를 통해 지원됩니다.

## <a name="data-box-disk-performance"></a>Data Box Disk 성능

USB 3.0을 통해 연결된 디스크를 테스트했을 때 디스크 성능은 최대 430MB/초였습니다. 실제 속도는 사용된 파일 크기에 따라 달라집니다. 작은 파일의 경우 성능이 저하될 수 있습니다.

## <a name="azure-storage-limits"></a>Azure Storage 제한

이 섹션에서는 Azure Storage 서비스에 대한 제한 및 Azure Files, Azure 블록 Blob 및 Azure 페이지 Blob에 대한 필수 명명 규칙을 Data Box 서비스에 적용 가능한 것으로 설명합니다. 스토리지 제한을 신중히 검토하고 모든 권장 사항을 수행합니다.

Azure Storage 서비스 제한에 대한 최신 정보 및 공유, 컨테이너 및 파일 이름 지정에 대한 모범 사례는 다음으로 이동합니다.

- [컨테이너 이름 지정 및 참조](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata)
- [공유 이름 지정 및 참조](/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata)
- [블록 Blob 및 페이지 Blob 규칙](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs)

> [!IMPORTANT]
> Azure Storage 서비스 제한을 초과하거나 Azure Files/Blob 명명 규칙을 준수하지 않는 파일 또는 디렉터리가 있는 경우 이러한 파일 또는 디렉터리는 Data Box 서비스를 통해 Azure Storage로 수집되지 않습니다.

## <a name="data-upload-caveats"></a>데이터 업로드 주의 사항

- 디스크에 직접 데이터를 복사하지 마세요. 미리 만든 *BlockBlob*, *PageBlob* 및 *AzureFile* 폴더에 데이터를 복사합니다.
- *BlockBlob* 및 *PageBlob* 아래의 폴더는 컨테이너입니다. 예를 들어 컨테이너는 *BlockBlob/컨테이너* 및 *PageBlob/컨테이너* 로 만들어집니다.
- 폴더의 이름이 기존 컨테이너와 같으면 폴더의 내용이 컨테이너의 콘텐츠와 병합됩니다. 클라우드에 아직 없는 파일 또는 Blob이 컨테이너에 추가됩니다. 파일 또는 Blob의 이름이 컨테이너에 이미 있는 파일 또는 Blob과 동일한 경우 기존 파일 또는 Blob을 덮어씁니다.
- *BlockBlob* 및 *PageBlob* 공유에 기록된 모든 파일은 각각 블록 Blob 및 페이지 Blob으로 업로드됩니다. 
- 파일의 계층 구조는 Blob 및 Azure Files 모두에 대해 클라우드에 업로드하는 동안 유지 관리됩니다. 예를 들어 `<container folder>\A\B\C.txt` 경로에 파일을 복사했습니다. 이 파일은 클라우드의 동일한 경로에 업로드됩니다.
- *BlockBlob* 및 *PageBlob* 폴더 아래에 만들어진 모든 빈 디렉터리 계층 구조(어떤 파일도 없는)는 업로드되지 않습니다.
- 데이터 업로드 중 성능을 향상하려면 [스토리지 계정에서 대용량 파일 공유를 사용하도록 설정하고 공유 용량을 100TiB로 늘리는](../../articles/storage/files/storage-how-to-create-file-share.md#enable-large-files-shares-on-an-existing-account) 것이 좋습니다. 대용량 파일 공유는 LRS(로컬 중복 스토리지)가 있는 스토리지 계정에 대해서만 지원됩니다.
- 데이터를 Azure에 업로드할 때 오류가 발생하는 경우 오류 로그는 대상 스토리지 계정에서 만들어집니다. 업로드가 완료되면 오류 로그 경로를 포털에서 사용할 수 있으며, 정정 작업을 수행하려면 로그를 검토할 수 있습니다. 업로드된 데이터를 확인하지 않고 원본에서 데이터를 삭제하지 마세요.
- 파일 메타데이터 및 NTFS 권한은 데이터가 Azure Files에 업로드될 때 보존되지 않습니다. 예를 들어 데이터가 복사될 때 파일의 마지막으로 수정된 특성은 유지되지 않습니다.
- 관리 디스크를 순서대로 지정했다면 다음 추가 고려 사항을 검토합니다.

    - 사전 생성된 폴더 전체 및 Data Box Disk 전체에서 리소스 그룹에 지정된 이름의 관리 디스크 하나만 가질 수 있습니다. 즉, 사전 생성된 폴더에 업로드된 VHD는 이름이 고유해야 함을 의미합니다. 지정된 이름이 리소스 그룹의 기존 관리 디스크와 일치하지 않도록 해야 합니다. VHD 이름이 동일할 경우 단 하나의 VHD만 해당 이름의 관리 디스크로 변환됩니다. 다른 VHD는 준비 스토리지 계정에 페이지 Blob으로 업로드됩니다.
    - 항상 사전 생성된 폴더 중 하나에 VHD를 복사합니다. 이러한 폴더 외부 또는 사용자가 만든 폴더 안에 VHD를 복사하는 경우 VHD는 Azure Storage 계정에 관리 디스크가 아닌 페이지 Blob으로 업로드됩니다.
    - 고정된 VHD만 업로드하여 관리 디스크를 만들 수 있습니다. 동적 VHD, 차이점 보관용 VHD 또는 VHDX 파일은 지원되지 않습니다.
    - 미리 생성된 관리 디스크 폴더에 복사되는 비 VHD 파일은 관리 디스크로 변환되지 않습니다.

## <a name="azure-storage-account-size-limits"></a>Azure Storage 계정 크기 제한

스토리지 계정에 복사할 수 있는 데이터 크기에 대한 제한은 다음과 같습니다. 업로드한 데이터가 이러한 제한을 준수하는지 확인합니다. 

| 데이터 형식             | 기본 제한          |
|--------------------------|------------------------|
| 블록 Blob, 페이지 Blob    | 이러한 제한에 대한 최신 정보는 [Azure Blob 스토리지 스케일링 목표](../storage/blobs/scalability-targets.md#scale-targets-for-blob-storage), [Azure 표준 스토리지 스케일링 목표](../storage/common/scalability-targets-standard-account.md#scale-targets-for-standard-storage-accounts) 및 [Azure Files 스케일링 목표](../storage/files/storage-files-scale-targets.md)를 참조하세요. <br /><br /> 제한에는 Data Box Disk를 비롯해 모든 원본 데이터가 포함됩니다.|


## <a name="azure-object-size-limits"></a>Azure 개체 크기 제한

쓸 수 있는 Azure 개체의 크기는 다음과 같습니다. 업로드되는 모든 파일이 이러한 제한을 준수하는지 확인합니다.

| Azure 개체 형식 | 기본 제한                                             |
|-------------------|-----------------------------------------------------------|
| 블록 Blob        | ~ 4.75TiB                                                 |
| 페이지 Blob         | 8TiB <br> (페이지 Blob 형식으로 업로드되는 모든 파일은 정렬된 512바이트여야 합니다. 그렇지 않으면 업로드가 실패합니다. <br> VHD 및 VHDX는 정렬된 512바이트입니다.) |
|Azure 파일        | 1TiB <br> 최대 공유의 크기는 5TiB     |
| 관리 디스크     |4TiB <br> 크기 및 제한에 대한 자세한 내용은 다음을 참조하세요. <li>[관리 디스크의 확장성 목표](../virtual-machines/disks-scalability-targets.md#managed-virtual-machine-disks)</li>|


## <a name="azure-block-blob-page-blob-and-file-naming-conventions"></a>Azure 블록 Blob과 페이지 Blob 및 파일 명명 규칙

| 엔터티                                       | 규칙                                                                                                                                                                                                                                                                                                               |
|----------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 블록 Blob 및 페이지 Blob에 대한 컨테이너 이름 <br> Azure Files의 파일 공유 이름 | 올바른 DNS 이름은 3~63자여야 합니다. <br>  문자 또는 숫자로 시작해야 합니다. <br> 소문자, 숫자 및 하이픈(-)만 포함할 수 있습니다. <br> 모든 하이픈(-)은 앞뒤에 문자 또는 숫자가 와야 합니다. <br> 이름에 연속적인 하이픈은 허용되지 않습니다. |
| Azure 파일에 대한 디렉터리와 파일 이름     |<li> 대/소문자 구분, 대/소문자를 구분하지 않으며 길이가 255자를 초과할 수 없습니다. </li><li> 슬래시(/)로 끝낼 수 없습니다. </li><li>사용된 경우 자동으로 제거됩니다. </li><li> 사용할 수 없는 문자: <code>" \\ / : \| < > * ?</code></li><li> 예약된 URL 문자는 적절히 이스케이프되어야 합니다. </li><li> 잘못된 URL 경로 문자는 허용되지 않습니다. \\uE000과 같은 코드 포인트는 유효한 유니코드 문자가 아닙니다. 제어 문자(0x00~0x1F, \\u0081 등)와 같은 일부 ASCII 또는 유니코드 문자도 허용되지 않습니다. HTTP/1.1에서 유니코드 문자열을 제어하는 규칙은 RFC 2616, 섹션 2.2의 기본 규칙 및 RFC 3987를 참조하세요. </li><li> 다음 파일 이름은 허용되지 않습니다: LPT1, LPT2, LPT3, LPT4, LPT5, LPT6, LPT7, LPT8, LPT9, COM1, COM2, COM3, COM4, COM5, COM6, COM7, COM8, COM9, PRN, AUX, NUL, CON, CLOCK$, 점 문자(.) 및 2개 점 문자(..).</li>|
| 블록 Blob 및 페이지 Blob에 대한 Blob 이름      | Blob 이름은 대/소문자를 구분하며 문자 조합을 포함할 수 있습니다. <br> Blob 이름은 길이가 1~1,024자 사이여야 합니다. <br> 예약된 URL 문자는 적절히 이스케이프되어야 합니다. <br>Blob 이름을 구성하는 경로 세그먼트 수는 254개를 초과할 수 없습니다. 경로 세그먼트는 가상 디렉터리 이름에 해당하는 연속 구분 기호 문자 사이의 문자열입니다(예: 슬래시 '/'). |

## <a name="managed-disk-naming-conventions"></a>관리 디스크 명명 규칙

| 엔터티 | 규칙                                             |
|-------------------|-----------------------------------------------------------|
| 관리 디스크 이름       | <li> 이름은 1~80자 길이여야 합니다. </li><li> 이름은 문자나 숫자로 시작하고 문자, 숫자 또는 밑줄로 끝나야 합니다. </li><li> 이름은 문자, 숫자, 밑줄, 마침표 또는 하이픈만 포함할 수 있습니다. </li><li>   이름에는 공백이나 `/`를 사용할 수 없습니다.                                              |

## <a name="next-steps"></a>다음 단계

- [Data Box Disk 시스템 요구 사항](data-box-disk-system-requirements.md) 검토
