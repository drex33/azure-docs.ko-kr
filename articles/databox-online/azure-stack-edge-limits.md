---
title: Azure Stack Edge Pro GPU/Pro FPGA 제한 | Microsoft Docs
description: 서비스 제한, 디바이스 제한 및 스토리지 제한을 포함하여 Azure Stack Edge Pro GPU/Pro FPGA를 배포하고 운영할 때의 제한 및 권장 크기에 대해 알아봅니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 10/12/2020
ms.author: alkohli
ms.openlocfilehash: 64be85db0e2d11d2aee5a61742f427087a77cef2
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122536127"
---
# <a name="azure-stack-edge-limits"></a>Azure Stack Edge 제한 사항

Microsoft Azure Stack Edge Pro GPU 또는 Azure Stack Edge Pro FPGA 솔루션을 배포 및 운영할 때 이러한 제한을 고려합니다. 

## <a name="azure-stack-edge-service-limits"></a>Azure Stack Edge 서비스 제한

[!INCLUDE [data-box-edge-gateway-service-limits](../../includes/data-box-edge-gateway-service-limits.md)]

## <a name="azure-stack-edge-device-limits"></a>Azure Stack Edge 디바이스 제한

다음 표에서는 Azure Stack Edge 디바이스에 대한 제한을 설명합니다.

| 설명 | 값 |
|---|---|
|아니요. 디바이스당 파일 수 |1억 |
|아니요. 컨테이너당 공유 수 |1 |
|디바이스당 공유 엔드포인트 및 REST 엔드포인트 최대 수(GPU 디바이스만 해당)| 24 |
|디바이스당 계층화된 디바이스 계정 최대 수(GPU 디바이스만 해당)| 24|
|공유에 기록되는 최대 파일 크기| 5TB |
|디바이스당 리소스 그룹의 최대 수| 800 |

## <a name="azure-storage-limits"></a>Azure Storage 제한

[!INCLUDE [data-box-edge-gateway-storage-limits](../../includes/data-box-edge-gateway-storage-limits.md)]

## <a name="data-upload-caveats"></a>데이터 업로드 주의 사항

[!INCLUDE [data-box-edge-gateway-storage-data-upload-caveats](../../includes/data-box-edge-gateway-storage-data-upload-caveats.md)]

## <a name="azure-storage-account-size-and-object-size-limits"></a>Azure Storage 계정 크기 및 개체 크기 제한

[!INCLUDE [data-box-edge-gateway-storage-acct-limits](../../includes/data-box-edge-gateway-storage-acct-limits.md)]


## <a name="azure-object-size-limits"></a>Azure 개체 크기 제한

[!INCLUDE [data-box-edge-gateway-storage-object-limits](../../includes/data-box-edge-gateway-storage-object-limits.md)]

## <a name="next-steps"></a>다음 단계

- [Azure Stack Edge Pro GPU 배포 준비](azure-stack-edge-gpu-deploy-prep.md)
- [Azure Stack Edge Pro FPGA 배포 준비](azure-stack-edge-deploy-prep.md)
