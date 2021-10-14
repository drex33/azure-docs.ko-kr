---
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: include
ms.date: 10/07/2021
ms.author: alkohli
ms.openlocfilehash: 8c823b6709dbd5010ec48c2fbca185d9a211cb40
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/14/2021
ms.locfileid: "130000111"
---
아래에는 Data Box 디바이스용으로 지원되는 스토리지 계정 및 스토리지 유형의 목록이 나와 있습니다. 모든 스토리지 계정 유형의 모든 기능이 포함된 전체 목록은 [스토리지 계정 유형](../articles/storage/common/storage-account-overview.md#types-of-storage-accounts)을 참조하세요.

#### <a name="supported-storage-accounts-for-imports"></a>가져오기에 대해 지원 되는 저장소 계정

가져오기 주문의 경우 다음 표에서는 지원되는 스토리지 계정을 보여 줍니다.

| **스토리지 계정/지원되는 스토리지 유형** | **블록 Blob** |**페이지 blob**<sup>1</sup> |**Azure 파일** |**지원되는 액세스 계층**|
| --- | --- | -- | -- | -- |
| Classic Standard | Y | Y | Y |
| 범용 v1 Standard  | Y | Y | Y | 핫, 쿨 |
| 범용 v1 Premium  |  | Y| | |
| 범용 v2 Standard<sup>2</sup>  | Y | Y | Y | 핫, 쿨|
| 범용 v2 Premium  |  |Y | |  |
| Azure Premium FileStorage |  |  | Y |  |  
| Blob Storage Standard | Y | | | 핫, 쿨 |


<sup>1</sup> *페이지 blob에 업로드 된 데이터는 vhd와 같이 512 바이트 정렬 되어야 합니다.*

<sup>2</sup> *Azure Data Lake Storage Gen2 (ADLS Gen2)는 가져오기에 대해 지원 되지만 내보내기에 대해서는 지원 되지 않습니다.*


#### <a name="supported-storage-accounts-for-exports"></a>내보내기에 대해 지원 되는 저장소 계정

내보내기 주문의 경우 다음 표에서는 지원되는 스토리지 계정을 보여 줍니다.

| **스토리지 계정/지원되는 스토리지 유형** | **블록 Blob** |**페이지 Blob*** |**Azure 파일** |**지원되는 액세스 계층**|
| --- | --- | -- | -- | -- |
| Classic Standard | Y | Y | Y | |
| 범용 v1 Standard  | Y | Y | Y | 핫, 쿨 |
| 범용 v1 Premium  |  | Y| | |
| 범용 v2 Standard  | Y | Y | Y | 핫, 쿨 |
| 범용 v2 Premium  |  |Y | | |
| Azure Premium FileStorage |  |  | Y |  |
| Blob Storage Standard |Y | | | 핫, 쿨 |
| 블록 Blob Storage 프리미엄 |Y | | | 핫, 쿨 |
| 페이지 Blob Storage 프리미엄 | |Y | | |

#### <a name="caveats-for-storage-accounts"></a>저장소 계정에 대 한 주의 사항

- 범용 계정:
  - 가져오기 주문의 경우에는 Data Box에서 큐, 테이블 및 디스크 저장소 형식을 지원 하지 않습니다.
  - 내보내기 주문의 경우 Data Box는 Queue, Table, Disk 및 Azure Data Lake Gen2 저장소 유형을 지원 하지 않습니다.
- Data Box는 Blob Storage 및 블록 Blob Storage 계정에 Blob 추가를 지원하지 않습니다.
- Azure Blob Storage의 NFS(네트워크 파일 시스템) 3.0 프로토콜 지원은 Data Box에서 지원되지 않습니다.
- 페이지 Blob에 업로드된 데이터는 VHD와 같이 512바이트로 정렬되어야 합니다.
- 내보내기의 경우:
  - 최대 80TB를 내보낼 수 있습니다.
  - 파일 기록과 Blob 스냅샷은 내보내지 않습니다.
  - 보관 Blob은 내보내기가 지원되지 않습니다. 내보내기 전에 보관 계층의 Blob을 리하이드레이션합니다. 자세한 내용은 [보관된 Blob을 온라인 계층으로 리하이드레이션](../articles/storage/blobs/archive-rehydrate-overview.md)을 참조하세요.