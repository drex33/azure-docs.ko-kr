---
title: Microsoft Azure Data Box 시스템 요구 사항 | Microsoft Docs
description: Azure Data Box 및 Data Box에 연결하는 클라이언트에 대한 중요한 시스템 요구 사항에 대해 알아봅니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 10/07/2021
ms.author: alkohli
ms.openlocfilehash: 583ed98980ae2ac5e8f3bddd7c3267c3400b85a5
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/14/2021
ms.locfileid: "130006150"
---
# <a name="azure-data-box-system-requirements"></a>Azure Data Box 시스템 요구 사항

이 문서에서는 Microsoft Azure Data Box 및 Data Box에 연결하는 클라이언트에 대한 중요한 시스템 요구 사항을 설명합니다. Data Box를 배포하기 전에 정보를 신중하게 검토한 다음, 배포 및 작업 중에 필요한 경우 이 정보를 참조하는 것이 좋습니다.

시스템 요구 사항에는 다음 내용이 포함됩니다.

* **소프트웨어 요구 사항:** Data Box에 연결하는 호스트의 경우 지원되는 운영 체제, 파일 전송 프로토콜, 스토리지 계정, 스토리지 유형 및 로컬 웹 UI용 브라우저에 대해 설명합니다.
* **네트워킹 요구 사항:** Data Box의 경우 최상의 작동을 위한 네트워크 연결 및 포트에 대한 요구 사항을 설명합니다.


## <a name="software-requirements"></a>소프트웨어 요구 사항

소프트웨어 요구 사항에는 지원되는 운영 체제, 파일 전송 프로토콜, 스토리지 계정, 스토리지 유형 및 로컬 웹 UI용 브라우저가 포함됩니다.

### <a name="supported-operating-systems-for-clients"></a>클라이언트에 대해 지원되는 운영 체제

[!INCLUDE [data-box-supported-os-clients](../../includes/data-box-supported-os-clients.md)]


### <a name="supported-file-transfer-protocols-for-clients"></a>클라이언트에 대해 지원되는 파일 전송 프로토콜

[!INCLUDE [data-box-supported-file-systems-clients](../../includes/data-box-supported-file-systems-clients.md)]

> [!IMPORTANT] 
> 내보내기 주문의 경우 REST를 통해 Data Box 공유에 대한 연결이 지원되지 않습니다.

### <a name="supported-storage-accounts"></a>지원되는 스토리지 계정

[!INCLUDE [data-box-supported-storage-accounts](../../includes/data-box-supported-storage-accounts.md)]

### <a name="supported-storage-types"></a>지원되는 스토리지 형식

[!INCLUDE [data-box-supported-storage-types](../../includes/data-box-supported-storage-types.md)]

### <a name="supported-web-browsers"></a>지원되는 웹 브라우저

[!INCLUDE [data-box-supported-web-browsers](../../includes/data-box-supported-web-browsers.md)]

## <a name="networking-requirements"></a>네트워킹 요구 사항

데이터 센터에는 고속 네트워크가 있어야 합니다. 10GbE 연결이 하나 이상 있는 것이 좋습니다. 10GbE 연결을 사용할 수 없는 경우 1GbE 데이터 링크를 사용하여 데이터를 복사할 수 있지만 이 경우 복사 속도가 영향을 받습니다.

### <a name="port-requirements"></a>포트 요구 사항

다음 테이블에는 SMB 또는 NFS 트래픽을 허용하기 위해 방화벽에서 열려야 하는 포트가 나열되어 있습니다. 이 테이블에서 *In*(*인바운드*)은 디바이스에 대한 들어오는 클라이언트 요청 액세스에서 방향을 나타냅니다. *Out*(또는 *아웃바운드*)은 배포 후 데이터를 외부로 보내는 Data Box Edge 디바이스에서 방향을 나타냅니다. 예를 들어 데이터가 인터넷으로 아웃바운드될 수 있습니다.

[!INCLUDE [data-box-port-requirements](../../includes/data-box-port-requirements.md)]


## <a name="next-steps"></a>다음 단계

* [Azure Data Box 배포](data-box-deploy-ordered.md)
