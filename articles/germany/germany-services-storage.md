---
title: Azure 독일 스토리지 서비스 | Microsoft Docs
description: 이 항목에서는 Azure 독일의 스토리지 서비스를 비교합니다. 다른 관련 정보도 찾을 수 있습니다.
ms.topic: article
ms.date: 10/16/2020
author: gitralf
ms.author: ralfwi
ms.service: germany
ms.custom: bfdocs
ms.openlocfilehash: 840ac16914196a238bcf8fdaaa518799dcb104e9
ms.sourcegitcommit: 613789059b275cfae44f2a983906cca06a8706ad
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/29/2021
ms.locfileid: "129275034"
---
# <a name="azure-germany-storage-services"></a>Azure 독일 스토리지 서비스

[!INCLUDE [closureinfo](../../includes/germany-closure-info.md)]

## <a name="storage"></a>Storage
Azure Storage 및 사용 방법에 대한 자세한 내용은 [Storage 글로벌 설명서](../storage/index.yml)를 참조하세요.

Azure Storage에 저장된 데이터는 고가용성을 보장하기 위해 복제됩니다. 지역 중복 스토리지 및 읽기 액세스 지역 중복 스토리지의 경우 Azure는 *페어링 지역* 간에 데이터를 복제합니다. Azure 독일의 경우 이러한 페어링 지역은 다음과 같습니다.

| 주 지역 | 보조(페어링) 지역 |
| --- | --- |
| 독일 중부 | 독일 북동부 |
| 독일 북동부 | 독일 중부 |

데이터 복제는 독일 국경 내에서 데이터를 유지됩니다. 지역 차원의 정전이나 재해 발생 시에도 가용성을 보장하기 위해 기본 지역과 보조 지역이 페어링되어 데이터 센터 간에 필요한 거리를 확보합니다. 지역 중복 고가용성 스토리지의 경우 스토리지 계정을 만들 때 지역 중복 스토리지 또는 읽기 액세스 지역 중복 스토리지를 선택합니다.  

스토리지 서비스 암호화는 Azure Storage 계정 내의 미사용 데이터를 보호합니다. 이 기능을 사용하도록 설정하면 Azure는 스토리지에 유지하기 전에 데이터를 자동으로 암호화합니다. 데이터는 256비트 AES 암호화를 통해 암호화됩니다. 스토리지 서비스 암호화는 블록 Blob, 추가 Blob 및 페이지 Blob의 암호화를 지원합니다.

### <a name="storage-service-availability-by-azure-germany-region"></a>Azure 독일 지역별 스토리지 서비스 가용성

| 서비스 | 독일 중부 | 독일 북동부 |
| --- | --- | --- |
| [Blob Storage](../storage/common/storage-introduction.md#blob-storage) |GA |GA |
| [Azure 파일](../storage/common/storage-introduction.md#azure-files) | GA | GA |
| [Table Storage](../storage/common/storage-introduction.md#table-storage) |GA  |GA |
| [Queue Storage](../storage/common/storage-introduction.md#queue-storage) |GA | GA |
| [핫/쿨 Blob 스토리지](../storage/blobs/access-tiers-overview.md) |GA |GA |
| [스토리지 서비스 암호화](../storage/common/storage-service-encryption.md) |GA |GA |
| 가져오기/내보내기 |해당 없음 |해당 없음 |
| StorSimple |해당 없음 |해당 없음 |

### <a name="variations"></a>변형
Azure 독일의 스토리지 계정에 대한 URL은 글로벌 Azure의 URL과 다릅니다.

| 서비스 유형 | 글로벌 Azure | Azure 독일 |
| --- | --- | --- |
| Blob Storage | *.blob.core.windows.net | *.blob.core.cloudapi.de |
| Azure 파일 | *.file.core.windows.net | *.file.core.cloudapi.de | 
| Queue 스토리지 | *.queue.core.windows.net | *.queue.core.cloudapi.de |
| Table Storage | *.table.core.windows.net | *.table.core.cloudapi.de |

> [!NOTE]
> 모든 스크립트와 코드는 적절한 엔드포인트를 고려해야 합니다. 자세한 내용은 [Azure Storage 연결 문자열 구성](../storage/common/storage-configure-connection-string.md)을 참조하세요. 
>
>

API에 대한 자세한 내용은 [클라우드 Storage 계정 생성자](/dotnet/api/microsoft.azure.cosmos.table.cloudstorageaccount.-ctor)를 참조하세요.

이러한 오버로드에 사용할 엔드포인트 접미사는 *core.cloudapi.de* 입니다.

> [!NOTE]
> [파일 공유를 탑재](../storage/files/storage-dotnet-how-to-use-files.md)하는 동안 오류 53("네트워크 경로를 찾을 수 없습니다")이 반환되면 방화벽이 아웃바운드 포트를 차단하고 있을 수 있습니다. 스토리지 계정과 동일한 Azure 구독에 있는 가상 머신에 파일 공유를 탑재해 보세요.
>
>


## <a name="next-steps"></a>다음 단계
추가 정보 및 업데이트를 보려면 [Azure 독일 블로그](/archive/blogs/azuregermany/)를 구독하세요.