---
title: Blob Storage에 요청에 대한 암호화 키 제공
titleSuffix: Azure Storage
description: Azure Blob Storage에 대한 요청을 수행하는 클라이언트는 요청별로 암호화 키를 제공할 수 있습니다. 요청에 암호화 키를 포함하면 Blob Storage 작업에 대한 암호화 설정의 세부적인 제어 기능이 제공됩니다.
services: storage
author: tamram
ms.service: storage
ms.date: 12/14/2020
ms.topic: conceptual
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.openlocfilehash: 2c16832287ec8a37c8af803919e1c2870d5bfd1b
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2021
ms.locfileid: "132724105"
---
# <a name="provide-an-encryption-key-on-a-request-to-blob-storage"></a>Blob Storage에 요청에 대한 암호화 키 제공

Azure Blob Storage에 대한 요청을 수행하는 클라이언트는 요청별로 AES-256 암호화 키를 제공할 수 있습니다. 요청에 암호화 키를 포함하면 Blob Storage 작업에 대한 암호화 설정의 세부적인 제어 기능이 제공됩니다. 고객이 제공한 키는 Azure Key Vault 또는 다른 키 스토리지에 저장할 수 있습니다.

## <a name="encrypting-read-and-write-operations"></a>읽기 및 쓰기 작업 암호화

클라이언트 애플리케이션에서 요청에 대한 암호화 키를 제공하는 경우 Azure Storage는 blob 데이터를 읽고 쓰는 동안 암호화 및 암호 해독을 투명하게 수행합니다. Azure Storage는 blob의 내용과 함께 암호화 키의 SHA-256 해시를 작성합니다. 해시는 blob에 대한 모든 후속 작업이 동일한 암호화 키를 사용하는지 확인하는 데 사용됩니다.

Azure Storage는 클라이언트가 요청과 함께 보내는 암호화 키를 저장하거나 관리하지 않습니다. 암호화 또는 암호 해독 프로세스가 완료되는 즉시 키가 안전하게 삭제됩니다.

클라이언트가 요청에 대해 고객이 제공한 키를 사용하여 blob을 만들거나 업데이트하는 경우 해당 blob에 대한 후속 읽기 및 쓰기 요청에도 키가 제공되어야 합니다. 이미 고객이 제공한 키로 암호화된 blob을 요청하는 경우, 키를 제공하지 않으면 오류 코드 409(충돌)가 발생하면서 요청이 실패합니다.

클라이언트 애플리케이션에서 요청에 대한 암호화 키를 보내고 스토리지 계정도 Microsoft 관리형 키 또는 고객 관리형 키를 사용하여 암호화되는 경우, Azure Storage는 요청에 따라 제공된 키를 암호화와 암호 해독에 사용합니다.

요청의 일환으로 암호화 키를 보내려면 클라이언트는 HTTPS를 사용하여 Azure Storage에 대한 보안 연결을 설정해야 합니다.

blob 스냅샷마다 자체 암호화 키가 있을 수 있습니다.

## <a name="request-headers-for-specifying-customer-provided-keys"></a>고객이 제공한 키를 지정하기 위한 요청 헤더

REST 호출 시, 클라이언트는 다음 헤더를 사용하면 요청에 대한 암호화 키 정보를 Blob Storage에 안전하게 전달할 수 있습니다.

|요청 헤더 | Description |
|---------------|-------------|
|`x-ms-encryption-key` |쓰기 및 읽기 요청에 모두 필요합니다. Base 64로 인코딩된 AES-256 암호화 키 값입니다. |
|`x-ms-encryption-key-sha256`| 쓰기 및 읽기 요청에 모두 필요합니다. 암호화 키에서 Base64로 인코딩된 SHA256입니다. |
|`x-ms-encryption-algorithm` | 쓰기 요청에 필요하며 읽기 요청의 경우에는 선택 사항입니다. 지정된 키를 사용하여 데이터를 암호화할 때 사용할 알고리즘을 지정합니다.  이 헤더의 값은 `AES256`이어야 합니다. |

요청에 대한 암호화 키 지정은 선택 사항입니다. 그러나 쓰기 작업에 위의 헤더 중 하나를 지정하는 경우 모든 헤더를 지정해야 합니다.

## <a name="blob-storage-operations-supporting-customer-provided-keys"></a>고객이 제공한 키를 지원하는 Blob Storage 작업

다음 Blob Storage 작업은 요청 시 고객이 제공한 암호화 키의 전송을 지원합니다.

- [Blob 배치](/rest/api/storageservices/put-blob)
- [블록 목록 배치](/rest/api/storageservices/put-block-list)
- [블록 배치](/rest/api/storageservices/put-block)
- [URL에서 블록 배치](/rest/api/storageservices/put-block-from-url)
- [페이지 배치](/rest/api/storageservices/put-page)
- [URL에서 페이지 배치](/rest/api/storageservices/put-page-from-url)
- [추가 블록](/rest/api/storageservices/append-block)
- [Blob 속성 설정](/rest/api/storageservices/set-blob-properties)
- [Blob 메타데이터 설정](/rest/api/storageservices/set-blob-metadata)
- [Blob 가져오기](/rest/api/storageservices/get-blob)
- [Blob 속성 가져오기](/rest/api/storageservices/get-blob-properties)
- [Blob 메타데이터 가져오기](/rest/api/storageservices/get-blob-metadata)
- [Blob 스냅샷](/rest/api/storageservices/snapshot-blob)

## <a name="rotate-customer-provided-keys"></a>고객 제공 키 순환

blob을 암호화하는 데 사용된 암호화 키를 순환하려면 blob을 다운로드한 다음, 새 암호화 키를 사용하여 blob을 다시 업로드합니다.

> [!IMPORTANT]
> 요청 시 제공된 키를 사용하여 암호화된 컨테이너 또는 blob에 대한 읽기나 쓰기에 Azure Portal을 사용할 수 없습니다.
>
> Blob Storage에 대한 요청에 제공한 암호화 키를 Azure Key Vault 같은 보안 키 저장소에 보호해야 합니다. 암호화 키가 없는 컨테이너 또는 Blob에서 쓰기 작업을 시도하면 작업이 실패하고 개체에 대한 액세스 권한이 손실됩니다.

## <a name="feature-support"></a>기능 지원

이 표에서는 사용자 계정에서 이 기능이 지원되는 방법과 특정 기능을 활성화할 때 지원에 미치는 영향을 보여 줍니다.

| Storage 계정 유형 | Blob Storage(기본 지원) | Data Lake Storage Gen2 <sup>1</sup> | NFS 3.0 <sup>1</sup> | SFTP <sup>1</sup> |
|--|--|--|--|--|
| 표준 범용 v2 | ![예](../media/icons/yes-icon.png) |![예](../media/icons/no-icon.png)              | ![예](../media/icons/no-icon.png) | ![예](../media/icons/no-icon.png) |
| Premium 블록 Blob          | ![예](../media/icons/yes-icon.png) |![예](../media/icons/no-icon.png)              | ![예](../media/icons/no-icon.png) | ![예](../media/icons/no-icon.png) |

<sup>1</sup> Data Lake Storage Gen2, NFS (네트워크 파일 시스템) 3.0 프로토콜 및 SFTP (Secure file Transfer protocol) 지원에는 모두 계층적 네임 스페이스를 사용 하는 저장소 계정이 필요 합니다.

## <a name="next-steps"></a>다음 단계

- [.NET을 사용하여 Blob Storage에 대한 요청에 고객이 제공한 키를 지정](storage-blob-customer-provided-key.md)
- [미사용 데이터에 대한 Azure Storage 암호화](../common/storage-service-encryption.md)
