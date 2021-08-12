---
title: Azure Stack Edge Mini R 제한 | Microsoft Docs
description: Azure Stack Edge Mini R의 시스템 제한 및 권장 크기를 설명합니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 10/13/2020
ms.author: alkohli
ms.openlocfilehash: 6f01d62d1d11f5ff90661482ffd2db112657eee5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96467852"
---
# <a name="azure-stack-edge-mini-r-limits"></a>Azure Stack Edge Mini R 제한


Azure Stack Edge Mini R 솔루션을 배포 및 운영하면서 이러한 제한을 고려해야 합니다.

## <a name="azure-stack-edge-service-limits"></a>Azure Stack Edge 서비스 제한

[!INCLUDE [azure-stack-edge-gateway-service-limits](../../includes/azure-stack-edge-gateway-service-limits.md)]

## <a name="azure-stack-edge-mini-r-device-limits"></a>Azure Stack Edge Mini R 디바이스 제한

다음 표에서는 Azure Stack Edge Mini R 디바이스에 대한 제한을 설명합니다.

| 설명 | 제한|
|---|---:|
|아니요. 디바이스당 파일 수 | 1억 <!--check with devs-->|
|아니요. 컨테이너당 공유 수 | 1|
|디바이스당 공유 엔드포인트 및 REST 엔드포인트 최대 수| 24 |
|디바이스당 계층화된 스토리지 계정의 최대 수| 24|
|공유에 기록되는 최대 파일 크기| 500GB|
|디바이스당 리소스 그룹의 최대 수| 800|

## <a name="azure-storage-limits"></a>Azure Storage 제한

[!INCLUDE [azure-stack-edge-gateway-storage-limits](../../includes/azure-stack-edge-gateway-storage-limits.md)]

## <a name="data-upload-caveats"></a>데이터 업로드 주의 사항

[!INCLUDE [azure-stack-edge-gateway-storage-data-upload-caveats](../../includes/azure-stack-edge-gateway-storage-data-upload-caveats.md)]

## <a name="azure-storage-account-size-and-object-size-limits"></a>Azure Storage 계정 크기 및 개체 크기 제한

[!INCLUDE [azure-stack-edge-gateway-storage-acct-limits](../../includes/azure-stack-edge-gateway-storage-acct-limits.md)]

## <a name="azure-object-size-limits"></a>Azure 개체 크기 제한

[!INCLUDE [azure-stack-edge-gateway-storage-object-limits](../../includes/azure-stack-edge-gateway-storage-object-limits.md)]

## <a name="next-steps"></a>다음 단계

- [Azure Stack Edge 배포 준비](azure-stack-edge-gpu-deploy-prep.md)
