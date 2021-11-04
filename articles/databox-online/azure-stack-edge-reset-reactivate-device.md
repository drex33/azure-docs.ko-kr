---
title: Azure Stack Edge 디바이스 초기화 및 재활성화
description: 데이터를 초기화하고 Azure Stack Edge 디바이스를 재활성화하는 방법을 알아봅니다.
services: databox
author: v-dalc
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 09/20/2021
ms.author: alkohli
ms.openlocfilehash: 4f37d453072277da4cb4ecade03cf3f58f28460e
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131005673"
---
# <a name="reset-and-reactivate-your-azure-stack-edge-device"></a>Azure Stack Edge 디바이스 초기화 및 재활성화

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

이 문서에서는 디바이스에 문제가 있거나 다른 이유로 새로 시작해야 하는 경우에 Azure Stack Edge 디바이스를 초기화, 재구성, 재활성화하는 방법을 설명합니다.

디바이스를 초기화하여 데이터를 제거한 후에는 디바이스를 새 리소스로 재활성화해야 합니다. 디바이스를 초기화하면 디바이스 구성이 제거되므로, 로컬 웹 UI를 통해 디바이스를 재구성해야 합니다.

예를 들어, 기존 Azure Stack Edge 리소스를 새 구독으로 이동해야 할 수 있습니다. 리소스를 이동하려면 다음을 수행합니다.

1. [디바이스 초기화](#reset-device) 단계에 따라 디바이스에서 데이터를 재설정합니다.
2. 기존 디바이스에서 새 구독을 사용하는 새 리소스를 만든 다음 디바이스를 활성화합니다. [디바이스 재활성화](#reactivate-device) 단계를 따릅니다.

## <a name="reset-device"></a>디바이스 다시 설정

디바이스의 데이터 디스크에서 데이터를 지우려면 디바이스를 초기화해야 합니다.

필요한 경우 초기화하기 전에 디바이스의 로컬 데이터를 복사합니다. 디바이스에서 Azure Storage 컨테이너로 데이터를 복사할 수 있습니다.

>[!IMPORTANT]
> 디바이스를 초기화하면 디바이스에서 모든 로컬 데이터와 워크로드가 지워지며, 이는 되돌릴 수 없습니다. 디바이스를 새로 시작하려는 경우에만 디바이스를 초기화합니다.

로컬 웹 UI 또는 PowerShell을 사용하여 디바이스를 초기화할 수 있습니다. PowerShell 지침은 [디바이스 초기화](./azure-stack-edge-connect-powershell-interface.md#reset-your-device)를 참조하세요.

[!INCLUDE [Reset data from the device](../../includes/azure-stack-edge-device-reset.md)]

## <a name="reactivate-device"></a>디바이스 재활성화

디바이스를 초기화하고 나면, 디바이스를 새 리소스로 재활성화해야 합니다. 새 주문을 한 후에는 새 리소스를 재구성한 다음 재활성화해야 합니다.

기존 디바이스를 재활성화하려면 다음 단계를 수행합니다.

1. [새 리소스 생성](azure-stack-edge-gpu-deploy-prep.md?tabs=azure-portal#create-a-new-resource) 단계에 따라 기존 디바이스에 대한 새 주문을 생성합니다. **배송지 주소** 탭에서 **디바이스가 이미 있습니다** 를 선택합니다.

   ![배송지 주소에 새 디바이스를 지정하지 않습니다](./media/azure-stack-edge-reset-reactivate-device/create-resource-with-no-new-device.png)

1. [활성화 키 가져오기](azure-stack-edge-gpu-deploy-prep.md?tabs=azure-portal#get-the-activation-key).

1. [디바이스에 연결](azure-stack-edge-gpu-deploy-connect.md).

1. [디바이스에 대한 네트워크 구성](azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy.md).

1. [디바이스 설정 구성](azure-stack-edge-gpu-deploy-set-up-device-update-time.md).

1. [인증서 구성](azure-stack-edge-gpu-deploy-configure-certificates.md).

1. [디바이스 활성화](azure-stack-edge-gpu-deploy-activate.md).

## <a name="next-steps"></a>다음 단계

- [Azure Stack Edge 디바이스 연결](azure-stack-edge-gpu-deploy-connect.md) 방법에 대해 알아봅니다.
