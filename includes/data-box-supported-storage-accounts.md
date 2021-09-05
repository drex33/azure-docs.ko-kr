---
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: include
ms.date: 08/02/2021
ms.author: alkohli
ms.openlocfilehash: 092484b09af008770260c64cd6b7e0ea15265811
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "121801243"
---
아래에는 Data Box 디바이스용으로 지원되는 스토리지 계정 및 스토리지 유형의 목록이 나와 있습니다. 모든 스토리지 계정 유형의 모든 기능이 포함된 전체 목록은 [스토리지 계정 유형](../articles/storage/common/storage-account-overview.md#types-of-storage-accounts)을 참조하세요.

가져오기 주문의 경우 다음 표에서는 지원되는 스토리지 계정을 보여 줍니다.

| **스토리지 계정/지원되는 스토리지 유형** | **블록 Blob** |**페이지 Blob*** |**Azure 파일** |**참고 사항**|
| --- | --- | -- | -- | -- |
| Classic Standard | Y | Y | Y |
| 범용 v1 Standard  | Y | Y | Y | 핫 및 쿨이 모두 지원됩니다.|
| 범용 v1 Premium  |  | Y| | |
| 범용 v2 Standard  | Y | Y | Y | 핫 및 쿨이 모두 지원됩니다.|
| 범용 v2 Premium  |  |Y | | |
| Azure Premium FileStorage |  |  | Y |  |  
| Blob Storage Standard |Y | | |핫 및 쿨이 모두 지원됩니다. |

\*  *- 페이지 Blob에 업로드된 데이터는 VHD와 같이 512바이트로 정렬되어야 합니다.*

내보내기 주문의 경우 다음 표에서는 지원되는 스토리지 계정을 보여 줍니다.

| **스토리지 계정/지원되는 스토리지 유형** | **블록 Blob** |**페이지 Blob*** |**Azure 파일** |**지원되는 액세스 계층**|
| --- | --- | -- | -- | -- |
| Classic Standard | Y | Y | Y | |
| 범용 v1 Standard  | Y | Y | Y | 핫, 쿨|
| 범용 v1 Premium  |  | Y| | |
| 범용 v2 Standard  | Y | Y | Y | 핫, 쿨|
| 범용 v2 Premium  |  |Y | | |
| Azure Premium FileStorage |  |  | Y |  |
| Blob Storage Standard |Y | | |핫, 쿨 |
| 블록 Blob Storage 프리미엄 |Y | | |핫, 쿨 |
| 페이지 Blob Storage 프리미엄 | |Y | | |

> [!IMPORTANT]
> - 범용 계정의 경우에는 Data Box가 가져오기 주문에 큐, 테이블 및 디스크 스토리지 유형을 지원하지 않습니다. 내보내기 주문의 경우에는 Data Box가 범용 계정에 큐, 테이블, 디스크 및 Azure Data Lake Gen 2 스토리지 유형을 지원하지 않습니다.
> - Data Box는 Blob Storage 및 블록 Blob Storage 계정에 Blob 추가를 지원하지 않습니다.
> - Azure Blob Storage의 NFS(네트워크 파일 시스템) 3.0 프로토콜 지원은 Data Box에서 지원되지 않습니다.
> - 페이지 Blob에 업로드된 데이터는 VHD와 같이 512바이트로 정렬되어야 합니다.
> - 최대 80TB를 내보낼 수 있습니다.
> - 파일 기록과 Blob 스냅샷은 내보내지 않습니다.
> - 보관 Blob은 내보내기가 지원되지 않습니다. 내보내기 전에 보관 계층의 Blob을 리하이드레이션합니다. 자세한 내용은 [보관된 Blob을 온라인 계층으로 리하이드레이션](../articles/storage/blobs/storage-blob-rehydration.md#rehydrate-an-archived-blob-to-an-online-tier)을 참조하세요.
