---
title: Azure Data Box, Azure Data Box Heavy에 대한 문제 해결
description: 이러한 장치에 데이터를 복사할 때 Azure Data Box 및 Azure Data Box Heavy에 표시되는 문제를 해결하는 방법을 설명합니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: troubleshooting
ms.date: 07/14/2021
ms.author: alkohli
ms.openlocfilehash: 06f0408587cc7c5533bdb852f7ad4f59094a33da
ms.sourcegitcommit: 192444210a0bd040008ef01babd140b23a95541b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/15/2021
ms.locfileid: "114220262"
---
# <a name="troubleshoot-issues-related-to-azure-data-box-and-azure-data-box-heavy"></a>Azure Data Box 및 Azure Data Box Heavy 관련된 문제 해결

이 문서에서는 가져오기 주문을 위해 Azure Data Box 또는 Azure Data Box Heavy를 사용할 때 나타날 수 있는 문제를 해결하는 방법에 대한 정보를 자세히 설명합니다. 이 문서에는 가져오기 주문을 위해 데이터를 Data Box로 복사할 때 또는 Data Box에서 데이터를 업로드할 때 표시될 수 있는 오류 목록이 포함되어 있습니다.

이 문서의 정보는 Data Box에 대해 생성된 내보내기 주문에는 적용되지 않습니다.

## <a name="error-classes"></a>오류 클래스

Data Box 및 Data Box Heavy의 오류는 다음과 같이 요약됩니다.

| 오류 카테고리*        | 설명        | 권장 조치    |
|----------------------------------------------|---------|--------------------------------------|
| 컨테이너 또는 공유 이름 | 컨테이너 또는 공유 이름은 Azure 명명 규칙을 따르지 않습니다.  |오류 목록을 다운로드합니다. <br> 컨테이너 또는 공유의 이름을 변경합니다. [자세히 알아봅니다](#container-or-share-name-errors).  |
| 컨테이너 또는 공유 크기 제한 | 컨테이너 또는 공유의 총 데이터가 Azure 제한을 초과합니다.   |오류 목록을 다운로드합니다. <br> 컨테이너 또는 공유의 전체 데이터를 줄입니다. [자세히 알아봅니다](#container-or-share-size-limit-errors).|
| 개체 또는 파일 크기 제한 | 컨테이너 또는 공유의 개체 또는 파일이 Azure 제한을 초과합니다.|오류 목록을 다운로드합니다. <br> 컨테이너 또는 공유에서 파일 크기를 줄입니다. [자세히 알아봅니다](#object-or-file-size-limit-errors). |    
| 데이터 또는 파일 형식 | 데이터 형식 또는 파일 형식은 지원되지 않습니다. |오류 목록을 다운로드합니다. <br> 페이지 Blob 또는 관리 디스크의 경우, 데이터가 512바이트 정렬되어 미리 만들어진 폴더에 복사되었는지 확인합니다. [자세히 알아봅니다](#data-or-file-type-errors). |
| 폴더 또는 파일 내부 오류 | 파일이나 폴더에 내부 오류가 있습니다. |오류 목록을 다운로드합니다. <br> 파일을 제거하고 다시 복사합니다. 이름을 바꾸거나 파일을 추가하거나 삭제하여 폴더를 수정합니다. 오류는 30분 이내에 사라집니다.  [자세히 알아봅니다](#folder-or-file-internal-errors). |
| 심각하지 않은 blob 또는 파일 오류  | Blob 또는 파일 이름이 Azure 명명 규칙을 따르지 않거나 파일 형식이 지원되지 않습니다. | 이러한 blob 또는 파일은 복사되지 않거나 이름이 변경될 수 있습니다. [이러한 오류를 해결하는 방법을 알아봅니다](#non-critical-blob-or-file-errors). |

\* 처음 다섯 개의 오류 범주는 심각한 오류이며, 배송 준비를 진행하기 전에 수정해야 합니다.


## <a name="container-or-share-name-errors"></a>컨테이너 또는 공유 이름 오류

컨테이너 및 공유 이름과 관련된 오류입니다.

### <a name="error_container_or_share_name_length"></a>ERROR_CONTAINER_OR_SHARE_NAME_LENGTH     

**오류 설명:** 컨테이너 또는 공유 이름은 3자~63자여야 합니다. 

**권장 해결 방법:** 데이터를 복사한 Data Box 또는 Data Box Heavy 공유(SMB/NFS) 아래의 폴더는 스토리지 계정의 Azure 컨테이너가 됩니다. 

- 장치 로컬 웹 UI의 **연결 및 복사** 페이지에서 오류 파일을 다운로드하고 검토하여 문제가 있는 폴더 이름을 확인합니다.
- Data Box 또는 Data Box Heavy 공유에서 폴더 이름을 변경하여 다음을 확인합니다.

    - 이름이 3자에서 63자 사이입니다.
    - 공유 이름에는 소문자, 숫자, 하이픈만 사용할 수 있습니다.
    - 이름은 하이픈으로 시작하거나 끝날 수 없습니다.
    - 이름에는 하이픈을 연속해서 사용할 수 없습니다.
    - 유효한 이름의 예: `my-folder-1`, `my-really-extra-long-folder-111`
    - 잘못된 이름의 예: `my-folder_1`, `my`, `--myfolder`, `myfolder--`, `myfolder!`

    자세한 내용은 [컨테이너 이름](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata#container-names) 및 [공유 이름](/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#share-names)에 대한 Azure 명명 규칙을 참조하세요.


### <a name="error_container_or_share_name_alpha_numeric_dash"></a>ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH

**오류 설명:** 컨테이너 또는 공유 이름은 문자, 숫자, 하이픈으로만 구성되어야 합니다.

**권장 해결 방법:** 데이터를 복사한 Data Box 또는 Data Box Heavy 공유(SMB/NFS) 아래의 폴더는 스토리지 계정의 Azure 컨테이너가 됩니다. 

- 장치 로컬 웹 UI의 **연결 및 복사** 페이지에서 오류 파일을 다운로드하고 검토하여 문제가 있는 폴더 이름을 확인합니다.
- Data Box 또는 Data Box Heavy 공유에서 폴더 이름을 변경하여 다음을 확인합니다.

    - 이름이 3자에서 63자 사이입니다.
    - 공유 이름에는 소문자, 숫자, 하이픈만 사용할 수 있습니다.
    - 이름은 하이픈으로 시작하거나 끝날 수 없습니다.
    - 이름에는 하이픈을 연속해서 사용할 수 없습니다.
    - 유효한 이름의 예: `my-folder-1`, `my-really-extra-long-folder-111`
    - 잘못된 이름의 예: `my-folder_1`, `my`, `--myfolder`, `myfolder--`, `myfolder!`

    자세한 내용은 [컨테이너 이름](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata#container-names) 및 [공유 이름](/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#share-names)에 대한 Azure 명명 규칙을 참조하세요.

### <a name="error_container_or_share_name_improper_dash"></a>ERROR_CONTAINER_OR_SHARE_NAME_IMPROPER_DASH

**오류 설명:** 컨테이너 이름 및 공유 이름은 하이픈으로 시작하거나 끝날 수 없으며 하이픈을 연속해서 사용할 수 없습니다.

**권장 해결 방법:** 데이터를 복사한 Data Box 또는 Data Box Heavy 공유(SMB/NFS) 아래의 폴더는 스토리지 계정의 Azure 컨테이너가 됩니다. 

- 장치 로컬 웹 UI의 **연결 및 복사** 페이지에서 오류 파일을 다운로드하고 검토하여 문제가 있는 폴더 이름을 확인합니다.
- Data Box 또는 Data Box Heavy 공유에서 폴더 이름을 변경하여 다음을 확인합니다.

    - 이름이 3자에서 63자 사이입니다.
    - 공유 이름에는 소문자, 숫자, 하이픈만 사용할 수 있습니다.
    - 이름은 하이픈으로 시작하거나 끝날 수 없습니다.
    - 이름에는 하이픈을 연속해서 사용할 수 없습니다.
    - 유효한 이름의 예: `my-folder-1`, `my-really-extra-long-folder-111`
    - 잘못된 이름의 예: `my-folder_1`, `my`, `--myfolder`, `myfolder--`, `myfolder!`

    자세한 내용은 [컨테이너 이름](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata#container-names) 및 [공유 이름](/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#share-names)에 대한 Azure 명명 규칙을 참조하세요.
    
### <a name="error_file_or_directory_name_illegal"></a>ERROR_FILE_OR_DIRECTORY_NAME_ILLEGAL

**오류 설명**: 디렉터리 또는 컨테이너 이름에 잘못된 문자가 포함되어 있습니다.

**권장 해결 방법**: 복사한 디렉터리 또는 컨테이너 이름에 지원되지 않는 문자가 포함되어 있습니다.

- 로컬 웹 UI의 연결 및 복사 페이지에서 오류 파일을 다운로드하고 검토하여 문제가 있는 폴더 이름을 확인합니다. 
- 디렉터리 또는 컨테이너의 이름을 변경하여 Azure 명명 규칙을 준수하는지 확인합니다.

자세한 내용은 [디렉터리](/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#directory-and-file-names) 및 [컨테이너](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata#container-names)에 대한 Azure 명명 규칙을 참조하세요.

## <a name="container-or-share-size-limit-errors"></a>컨테이너 또는 공유 크기 제한 오류

이러한 오류는 컨테이너 또는 공유에서 허용되는 데이터의 크기를 초과하는 데이터와 관련된 오류입니다.

### <a name="error_container_or_share_capacity_exceeded"></a>ERROR_CONTAINER_OR_SHARE_CAPACITY_EXCEEDED

**오류 설명:** Azure 파일 공유는 데이터를 5TiB까지 제한하고 스토리지 계정에는 대량 파일 공유를 사용할 수 없습니다. 일부 공유가 이 제한을 초과했습니다.

**권장 해결 방법:** 로컬 웹 UI의 **연결 및 복사** 페이지에서 오류 파일을 다운로드하고 검토합니다.

- 오류 로그에서 이 문제가 있는 폴더를 확인하고 해당 폴더의 파일이 5TiB 미만인지 확인합니다.
- 5TiB 제한은 대량 파일 공유를 허용하는 스토리지 계정에는 적용되지 않습니다. 그러나 주문을 할 때 대량 파일 공유를 구성해야 합니다. 
  - [Microsoft 지원](data-box-disk-contact-microsoft-support.md)에 문의하여 새 배송 레이블을 요청합니다.
  - [스토리지 계정에서 대량 파일 공유를 사용하도록 설정합니다](../storage/files/storage-how-to-create-file-share.md#enable-large-files-shares-on-an-existing-account)
  - [스토리지 계정에서 파일 공유를 확장](../storage/files/storage-how-to-create-file-share.md#expand-existing-file-shares)하고 할당량을 100TiB로 설정합니다.
  
  
## <a name="object-or-file-size-limit-errors"></a>개체 또는 파일 크기 제한 오류

이러한 오류는 Azure에서 허용되는 개체 또는 파일의 최대 크기를 초과하는 데이터와 관련된 오류입니다. 

### <a name="error_blob_or_file_size_limit"></a>ERROR_BLOB_OR_FILE_SIZE_LIMIT

**오류 설명:** 파일 크기가 업로드할 수 있는 최대 파일 크기를 초과합니다.

**권장 해결 방법:** Blob 또는 파일 크기가 업로드에 허용된 최대 한도를 초과합니다.

- 로컬 웹 UI의 **연결 및 복사** 페이지에서 오류 파일을 다운로드하고 검토합니다.
- Blob 및 파일 크기가 Azure 개체 크기 제한을 초과하지 않는지 확인합니다.

## <a name="data-or-file-type-errors"></a>데이터 또는 파일 형식 오류

이러한 오류는 컨테이너 또는 공유에 있는 지원되지 않는 파일 형식 또는 데이터 형식과 관련된 오류입니다. 

### <a name="error_blob_or_file_size_alignment"></a>ERROR_BLOB_OR_FILE_SIZE_ALIGNMENT

**오류 설명:** Blob 또는 파일이 잘못 정렬되었습니다.

**권장 해결 방법:** Data Box 또는 Data Box Heavy의 페이지 blob 공유는 512바이트 정렬된 파일만 지원합니다(예: VHD/VHDX). 페이지 blob 공유에 복사된 데이터는 페이지 blob으로 Azure에 업로드됩니다.

페이지 blob 공유에서 비 VHD/VHDX 데이터를 제거합니다. 블록 blob 또는 제네릭 데이터용 Azure 파일에 대한 공유를 사용할 수 있습니다.

자세한 내용은 [페이지 blob 개요](../storage/blobs/storage-blob-pageblob-overview.md)를 참조하세요.

### <a name="error_blob_or_file_type_unsupported"></a>ERROR_BLOB_OR_FILE_TYPE_UNSUPPORTED

**오류 설명:** 지원되지 않는 파일 형식이 관리 디스크 공유에 있습니다. 고정 VHD만 허용됩니다.

**권장 해결 방법:**

- 고정 VHD만 업로드하여 관리 디스크를 생성해야 합니다.
- VHDX 파일 또는 **dynamic** 및 **differencing** VHD는 지원되지 않습니다.

### <a name="error_directory_disallowed_for_type"></a>ERROR_DIRECTORY_DISALLOWED_FOR_TYPE

**오류 설명:** 관리 디스크에 대한 기존 폴더에는 디렉터리가 허용되지 않습니다. 이러한 폴더에는 고정 VHD만 사용할 수 있습니다.

**권장 해결 방법:** 관리 디스크의 경우, 각 공유 내에서 스토리지 계정의 컨테이너에 해당하는 다음 3개의 폴더가 생성됩니다. 프리미엄 SSD, 표준 HDD, 표준 SSD. 이러한 폴더는 관리 디스크의 성능 계층에 해당합니다.

- 이러한 기존 폴더 중 하나에 페이지 blob 데이터(VHD)를 복사하는지 확인합니다.
- 이러한 기존 폴더에서는 폴더 또는 디렉터리를 사용할 수 없습니다. 기존 폴더 내에서 생성한 폴더를 모두 제거합니다.

자세한 내용은 [관리 디스크에 복사](data-box-deploy-copy-data-from-vhds.md#connect-to-data-box)를 참조하세요.

### <a name="reparse_point_error"></a>REPARSE_POINT_ERROR

**오류 설명:** 바로가기 링크는 Linux에서 허용되지 않습니다. 

**권장 해결 방법:** 바로가기 링크는 일반적으로 링크, 파이프, 기타 유사한 파일입니다. 링크를 제거하거나 링크를 확인하고 데이터를 복사합니다.

## <a name="folder-or-file-internal-errors"></a>폴더 또는 파일 내부 오류

**오류 설명:** 파일이나 폴더가 내부 오류 상태에 있습니다.

**권장 해결 방법:** 파일의 경우, 파일을 제거하고 다시 복사합니다. 폴더의 경우, 폴더를 수정합니다. 폴더의 이름을 바꾸거나 폴더에서 파일을 추가하거나 삭제합니다. 오류는 30분 내에 자체적으로 해결되어야 합니다. 오류가 지속되면 Microsoft 지원에 문의하세요.

## <a name="non-critical-blob-or-file-errors"></a>심각하지 않은 blob 또는 파일 오류

다음 섹션에서는 데이터 복사 중에 표시되는 blob, 파일 또는 컨테이너의 이름과 관련된 모든 중요하지 않은 오류를 요약하여 설명합니다. 이러한 오류가 발생하는 경우, Azure 명명 규칙을 준수하도록 이름이 수정됩니다. 데이터 업로드에 대한 해당 주문 상태가 **경고와 함께 완료** 됩니다.  

### <a name="error_blob_or_file_name_character_control"></a>ERROR_BLOB_OR_FILE_NAME_CHARACTER_CONTROL

**오류 설명:** Blob 또는 파일 이름에 지원되지 않는 제어 문자가 포함되어 있습니다.

**권장 해결 방법:** 복사한 blob 또는 파일에 지원되지 않는 문자가 있는 이름이 포함되어 있습니다.

로컬 웹 UI의 **연결 및 복사** 페이지에서 오류 파일을 다운로드하고 검토합니다.
파일을 제거하거나 이름을 변경하여 지원되지 않는 문자를 제거합니다.

자세한 내용은 [blob 이름](/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata#blob-names) 및 [파일 이름](/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#directory-and-file-names)에 대한 Azure 명명 규칙을 참조하세요.

### <a name="error_blob_or_file_name_character_illegal"></a>ERROR_BLOB_OR_FILE_NAME_CHARACTER_ILLEGAL

**오류 설명:** Blob 또는 파일 이름에 잘못된 문자가 포함되어 있습니다.

**권장 해결 방법:** 복사한 blob 또는 파일에 지원되지 않는 문자가 있는 이름이 포함되어 있습니다.

로컬 웹 UI의 **연결 및 복사** 페이지에서 오류 파일을 다운로드하고 검토합니다.
파일을 제거하거나 이름을 변경하여 지원되지 않는 문자를 제거합니다.

자세한 내용은 [blob 이름](/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata#blob-names) 및 [파일 이름](/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#directory-and-file-names)에 대한 Azure 명명 규칙을 참조하세요.


### <a name="error_blob_or_file_name_ending"></a>ERROR_BLOB_OR_FILE_NAME_ENDING

**오류 설명:** Blob 또는 파일 이름이 잘못된 문자로 끝납니다.

**권장 해결 방법:** 복사한 blob 또는 파일에 지원되지 않는 문자가 있는 이름이 포함되어 있습니다.

로컬 웹 UI의 **연결 및 복사** 페이지에서 오류 파일을 다운로드하고 검토합니다.
파일을 제거하거나 이름을 변경하여 지원되지 않는 문자를 제거합니다.

자세한 내용은 [blob 이름](/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata#blob-names) 및 [파일 이름](/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#directory-and-file-names)에 대한 Azure 명명 규칙을 참조하세요.


### <a name="error_blob_or_file_name_segment_count"></a>ERROR_BLOB_OR_FILE_NAME_SEGMENT_COUNT

**오류 설명:** Blob 또는 파일 이름에 너무 많은 경로 세그먼트가 포함되어 있습니다.

**권장 해결 방법:** 복사한 blob 또는 파일이 최대 경로 세그먼트 수를 초과합니다. 경로 세그먼트는 가상 디렉터리 이름에 해당하는 연속 구분 기호 문자 사이의 문자열입니다(예: 슬래시 '/').

- 로컬 웹 UI의 **연결 및 복사** 페이지에서 오류 파일을 다운로드하고 검토합니다.
- [Blob 이름](/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata#blob-names) 및 [파일 이름이](/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#directory-and-file-names) Azure 명명 규칙을 준수하는지 확인합니다.

### <a name="error_blob_or_file_name_aggregate_length"></a>ERROR_BLOB_OR_FILE_NAME_AGGREGATE_LENGTH

**오류 설명:** Blob 또는 파일 이름이 너무 깁니다.

**권장 해결 방법:** Blob 또는 파일 이름이 최대 길이를 초과합니다.

- 로컬 웹 UI의 **연결 및 복사** 페이지에서 오류 파일을 다운로드하고 검토합니다.
- Blob 이름은 1024자를 초과하면 안됩니다.
- 이름이 1024자를 초과하지 않도록 blob 또는 파일을 제거하거나 이름을 변경합니다.

자세한 내용은 blob 이름 및 파일 이름에 대한 Azure 명명 규칙을 참조하세요.

### <a name="error_blob_or_file_name_component_length"></a>ERROR_BLOB_OR_FILE_NAME_COMPONENT_LENGTH

**오류 설명:** Blob 또는 파일의 세그먼트 중 하나가 너무 깁니다.

**권장 해결 방법:** Blob 또는 파일 이름에 있는 경로 세그먼트 중 하나가 최대 문자 수를 초과합니다. 경로 세그먼트는 가상 디렉터리 이름에 해당하는 연속 구분 기호 문자 사이의 문자열입니다(예: 슬래시 '/').

- 로컬 웹 UI의 **연결 및 복사** 페이지에서 오류 파일을 다운로드하고 검토합니다.
- [Blob 이름](/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata#blob-names) 및 [파일 이름이](/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#directory-and-file-names) Azure 명명 규칙을 준수하는지 확인합니다.


### <a name="error_container_or_share_name_disallowed_for_type"></a>ERROR_CONTAINER_OR_SHARE_NAME_DISALLOWED_FOR_TYPE

**오류 설명:** 관리 디스크 공유에 잘못된 컨테이너 이름이 지정되었습니다.

**권장 해결 방법:** 관리 디스크의 경우, 각 공유 내에서 스토리지 계정의 컨테이너에 해당하는 다음 폴더가 생성됩니다. 프리미엄 SSD, 표준 HDD, 표준 SSD. 이러한 폴더는 관리 디스크의 성능 계층에 해당합니다.

- 이러한 기존 폴더 중 하나에 페이지 blob 데이터(VHD)를 복사하는지 확인합니다. 이러한 기존 컨테이너의 데이터만 Azure에 업로드됩니다.
- 프리미엄 SSD, 표준 HDD, 표준 SSD와 같은 수준에서 생성한 다른 폴더는 유효한 성능 계층에 해당하지 않으며, 사용할 수 없습니다.
- 성능 계층 외부에서 생성한 파일 또는 폴더를 제거합니다.

자세한 내용은 [관리 디스크에 복사](data-box-deploy-copy-data-from-vhds.md#connect-to-data-box)를 참조하세요.


## <a name="next-steps"></a>다음 단계

- [Data Box Blob Storage 시스템 요구 사항](data-box-system-requirements-rest.md)에 대해 알아봅니다.
