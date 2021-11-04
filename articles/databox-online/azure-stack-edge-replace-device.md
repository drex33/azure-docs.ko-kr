---
title: Azure Stack Edge 디바이스 교체
description: 대체 Azure Stack Edge 디바이스를 가져오는 방법을 설명합니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 02/22/2021
ms.author: alkohli
ms.openlocfilehash: 96b6b32a694e39a7e227a5b98949f8eb1d4891e4
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131475961"
---
# <a name="replace-your-azure-stack-edge-device"></a>Azure Stack Edge 디바이스 바꾸기

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

이 문서에서는 Azure Stack Edge 디바이스를 교체하는 방법을 설명합니다. 기존 디바이스에 하드웨어 오류가 있거나 업그레이드가 필요한 경우 교체 디바이스가 필요합니다. 


이 문서에서는 다음 방법을 설명합니다.

> [!div class="checklist"]
>
> * 하드웨어 문제에 대한 지원 티켓 열기
> * Azure Portal에서 교체 디바이스의 새 주문 만들기
> * 교체 디바이스를 설치하고 활성화합니다.
> * 원래 디바이스 반환

## <a name="open-a-support-ticket"></a>지원 티켓 열기

기존 디바이스에 하드웨어 오류가 있는 경우 다음 단계를 수행하여 지원 티켓을 엽니다.

1. Microsoft 지원에서 지원 티켓을 열어 디바이스 반환 의사를 밝힙니다. **Azure Stack Edge 하드웨어** 문제 유형을 선택하고 **하드웨어 문제** 하위 유형을 선택합니다.  

    ![지원 티켓 열기](media/azure-stack-edge-replace-device/open-support-ticket-1.png)  

2. Microsoft 지원 엔지니어는 FRU(필드 교체 단위)가 문제를 해결할 수 있고 이 인스턴스에 사용할 수 있는지를 확인하는 데 도움을 받을 수 있습니다. FRU를 사용할 수 없거나 디바이스에 하드웨어 업그레이드가 필요한 경우에는 새 주문을 입력하고 이전 디바이스를 반환하는 방법을 안내합니다.

## <a name="create-a-new-order"></a>새 주문 작성

[새 리소스 만들기](azure-stack-edge-gpu-deploy-prep.md#create-a-new-resource)의 단계에 따라 교체 디바이스 활성화를 위한 새 리소스를 만듭니다.

> [!NOTE]
> 기존 리소스에 대해 교체 디바이스를 활성화하는 것은 지원되지 않습니다. 새 리소스는 새 순서로 간주됩니다. 디바이스를 배송한 후 14일 후에 요금을 청구하기 시작합니다.

## <a name="install-and-activate-the-replacement-device"></a>교체 디바이스 설치 및 활성화

교체 디바이스를 설치하고 활성화하려면 다음 단계를 수행합니다.

1. [디바이스를 설치합니다](azure-stack-edge-deploy-install.md).
2. 이전에 만든 새 리소스에 대해 [디바이스를 활성화](azure-stack-edge-deploy-connect-setup-activate.md)합니다.

## <a name="return-your-existing-device"></a>기존 디바이스 반환

모든 단계에 따라 원래 디바이스를 반환합니다.

1. [디바이스에서 데이터를 지웁니다](azure-stack-edge-return-device.md#erase-data-from-the-device).
2. 원래 디바이스에 대해 [디바이스 반환을 시작](azure-stack-edge-return-device.md#initiate-device-return)합니다.
3. [픽업을 예약합니다](azure-stack-edge-return-device.md#schedule-a-pickup).
4. Microsoft에서 디바이스를 받으면 반환된 디바이스와 관련된 [리소스를 삭제](azure-stack-edge-return-device.md#complete-return)할 수 있습니다.


## <a name="next-steps"></a>다음 단계

- [Azure Stack Edge 디바이스 반환](azure-stack-edge-return-device.md) 방법을 알아봅니다.
