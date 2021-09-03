---
title: Azure Stack Edge Pro GPU 디바이스에서 Edge 리소스 그룹 관리
description: Azure Portal을 통해 Azure Stack Edge Pro GPU, Azure Stack Edge Pro R 및 Azure Stack Edge Mini R 디바이스에서 Edge 리소스 그룹을 관리하는 방법을 알아봅니다.
services: databox
author: v-dalc
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 08/02/2021
ms.author: alkohli
ms.openlocfilehash: c77fb42579af8d1e4bad7e55746ad36d16c983b3
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122536540"
---
# <a name="manage-edge-resource-groups-on-azure-stack-edge-pro-gpu-devices"></a>Azure Stack Edge Pro GPU 디바이스에서 Edge 리소스 그룹 관리

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Edge 리소스 그룹에는 가상 머신 만들기 및 배포 중에 로컬 Azure Resource Manager를 통해 디바이스에서 만들어진 리소스가 포함됩니다. 이러한 로컬 리소스에는 가상 머신, VM 이미지, 디스크, 네트워크 인터페이스 및 Edge 스토리지 계정과 같은 기타 리소스 유형이 포함될 수 있습니다.

이 문서에서는 Azure Stack Edge Pro GPU 디바이스에서 Edge 리소스 그룹을 보고 삭제하는 방법을 설명합니다.

## <a name="view-edge-resource-groups"></a>Edge 리소스 그룹 보기

현재 구독에 대한 Edge 리소스 그룹을 보려면 다음 단계를 따르세요.

1. 디바이스에서 **가상 머신** 으로 이동하고 **리소스** 창으로 이동합니다. **Edge 리소스 그룹** 을 선택합니다.

    ![Azure Stack Edge 디바이스의 가상 머신에 대한 리소스 보기의 스크린샷. Edge 리소스 그룹 탭이 표시되고 강조 표시됩니다.](media/azure-stack-edge-gpu-manage-edge-resource-groups-portal/edge-resource-groups-01.png)

    > [!NOTE]
    > 디바이스에서 Azure Resource Manager 환경을 설정한 후 Azure Powershell에서 [Get-AzResource](/powershell/module/az.resources/get-azresource?view=azps-6.1.0&preserve-view=true)를 사용하여 동일한 목록을 가져올 수 있습니다. 자세한 내용은 [Azure Resource Manager에 연결](azure-stack-edge-gpu-connect-resource-manager.md)을 참조하세요.


## <a name="delete-an-edge-resource-group"></a>Edge 리소스 그룹 삭제

더 이상 사용하지 않는 Edge 리소스 그룹을 삭제하려면 다음 단계를 따르세요.

> [!NOTE]
> - 리소스 그룹을 삭제하려면 비어 있어야 합니다. 
> - ASRG 리소스 그룹은 삭제할 수 없습니다. 해당 리소스 그룹은 디바이스에서 컴퓨팅을 사용하도록 설정할 때 자동으로 만들어지는 ASEVNET 가상 네트워크를 저장합니다.

1. 디바이스에서 **가상 머신** 으로 이동하고 **리소스** 창으로 이동합니다. **Edge 리소스 그룹** 을 선택합니다.

    ![Azure Stack Edge 디바이스의 가상 머신에 대한 리소스 보기를 보여 주는 스크린샷. Edge 리소스 그룹 탭이 표시되고 강조 표시됩니다.](media/azure-stack-edge-gpu-manage-edge-resource-groups-portal/edge-resource-groups-01.png)

1. 삭제하려는 리소스 그룹을 선택합니다. 리소스 그룹의 맨 오른쪽에서 삭제 아이콘(휴지통)을 선택합니다.

   삭제 아이콘은 리소스 그룹에 리소스가 없는 경우에만 표시됩니다.

    ![Azure Stack Edge 디바이스의 VM에 대한 리소스 보기, Edge 리소스 그룹 탭의 스크린샷. 리소스 그룹의 휴지통 아이콘은 삭제할 수 있음을 나타냅니다. 아이콘이 강조 표시되어 있습니다.](media/azure-stack-edge-gpu-manage-edge-resource-groups-portal/edge-resource-groups-02.png)

1. Edge 리소스 그룹을 삭제할 것인지 확인하는 메시지가 표시됩니다. 작업을 되돌릴 수 없습니다. **Yes** 를 선택합니다.

    ![VM의 리소스 보기에 있는 Edge 리소스 그룹 탭의 스크린샷. 강조 표시된 휴지통 아이콘은 리소스 그룹을 삭제할 수 있음을 나타냅니다.](./media/azure-stack-edge-gpu-manage-edge-resource-groups-portal/edge-resource-groups-03.png)

    삭제가 완료되면 리소스 그룹이 목록에서 제거됩니다.

## <a name="next-steps"></a>다음 단계

- Azure Stack Edge Pro GPU 디바이스를 관리하는 방법에 대한 자세한 내용은 [로컬 웹 UI를 사용하여 Azure Stack Edge Pro GPU 관리](azure-stack-edge-manage-access-power-connectivity-mode.md)를 참조하세요.

- [디바이스에 Azure Resource Manager 환경을 설정합니다](azure-stack-edge-gpu-connect-resource-manager.md).