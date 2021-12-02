---
title: Azure NetApp Files에 대한 용량 풀 또는 볼륨의 크기 조정 | Microsoft Docs
description: 용량 풀 또는 볼륨의 크기를 변경하는 방법을 알아봅니다. 용량 풀 크기를 조정하면 구매한 Azure NetApp Files 용량을 변경합니다.
services: azure-netapp-files
documentationcenter: ''
author: b-hchen
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 04/30/2021
ms.author: b-hchen
ms.openlocfilehash: 4a18aba34b12d16923fae114294bff2739ccf6cc
ms.sourcegitcommit: 9ef0965834870700468c822ddcafc011881fc2d5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/02/2021
ms.locfileid: "133485937"
---
# <a name="resize-a-capacity-pool-or-a-volume"></a>용량 풀 또는 볼륨 크기 조정
볼륨 또는 용량 풀이 가득 찰 때와 같이, 필요에 따라 용량 풀 또는 볼륨의 크기를 변경할 수 있습니다. 

볼륨의 용량을 모니터링하는 방법에 대한 자세한 내용은 [볼륨의 용량 모니터링](monitor-volume-capacity.md)을 참조하세요.

## <a name="resize-the-capacity-pool-using-the-azure-portal"></a>Azure Portal을 사용하여 용량 풀 크기 조정 

1-TiB 증분 또는 감소로 용량 풀 크기를 변경할 수 있습니다. 그러나 용량 풀 크기는 4TiB 미만일 수 없습니다. 용량 풀 크기를 조정하면 구매한 Azure NetApp Files 용량을 변경합니다.

1. NetApp 계정 보기에서 **용량 풀** 로 이동하고, 크기를 조정할 용량 풀을 클릭합니다.
2. 바로 가기 메뉴를 표시하려면 용량 풀 이름을 마우스 오른쪽 단추를 클릭하거나, 용량 풀의 행의 끝에 있는 "…" 아이콘을 클릭합니다. **크기 조정** 을 클릭합니다. 

    ![풀 바로 가기 메뉴를 보여 주는 스크린샷입니다.](../media/azure-netapp-files/resize-pool-context-menu.png)  

3. 풀 크기 조정 창에서 풀 크기를 지정합니다.  **확인** 을 클릭합니다.

    ![풀 크기 조정 창을 보여 주는 스크린샷입니다.](../media/azure-netapp-files/resize-pool-window.png) 

## <a name="resize-a-volume-using-the-azure-portal"></a>Azure Portal을 사용하여 볼륨 크기 조정

필요에 따라 볼륨의 크기를 변경할 수 있습니다. 볼륨의 용량 소비는 해당 풀의 프로비전된 용량에 대해 계산됩니다.

1. NetApp 계정 보기에서 **볼륨** 으로 이동하고 크기를 조정할 볼륨을 클릭합니다.
2. 볼륨 이름을 마우스 오른쪽 단추로 클릭하거나 볼륨의 행의 끝에 있는"…" 아이콘을 클릭하여 바로 가기 메뉴를 표시합니다. **크기 조정** 을 클릭합니다.

    ![바로 가기 메뉴를 보여 주는 스크린샷입니다.](../media/azure-netapp-files/resize-volume-context-menu.png) 
    
3. 볼륨 할당량 업데이트 창에서 볼륨의 할당량을 지정합니다. **확인** 을 클릭합니다.   

    ![볼륨 할당량 업데이트 창을 보여 주는 스크린샷입니다.](../media/azure-netapp-files/resize-volume-quota-window.png) 

## <a name="resizing-the-capacity-pool-or-a-volume-using-azure-cli"></a>Azure CLI를 사용하여 용량 풀 또는 볼륨 크기 조정  

[Azure CLI(명령줄) 도구](azure-netapp-files-sdk-cli.md)의 다음 명령을 사용하여 용량 풀 또는 볼륨의 크기를 조정할 수 있습니다.

* [`az netappfiles pool`](/cli/azure/netappfiles/pool?preserve-view=true&view=azure-cli-latest)
* [`az netappfiles volume`](/cli/azure/netappfiles/volume?preserve-view=true&view=azure-cli-latest)

## <a name="resizing-the-capacity-pool-or-a-volume-using-rest-api"></a>REST API를 사용하여 용량 풀 또는 볼륨 크기 조정

자동화를 빌드하여 용량 풀 및 볼륨 크기 변경을 처리할 수 있습니다.   

[Azure NetApp Files용 REST API](azure-netapp-files-develop-with-rest-api.md) 및 [Azure NetApp Files용 PowerShell을 사용한 REST API](develop-rest-api-powershell.md)를 참조하세요. 

Azure NetApp Files에 대한 REST API 사양과 예제 코드는 [resource-manager GitHub 디렉터리](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/netapp/resource-manager/Microsoft.NetApp/stable)를 통해 사용할 수 있습니다. 

## <a name="resize-a-cross-region-replication-destination-volume"></a>지역 간 복제 대상 볼륨 크기 조정 

[지역 간 복제](cross-region-replication-introduction.md) 관계에서 대상 볼륨은 원본 볼륨의 크기에 따라 자동으로 크기가 조정됩니다. 따라서 대상 볼륨의 크기를 별도로 조정할 필요가 없습니다. 이러한 자동 크기 조정 동작은 볼륨이 활성 복제 관계에 있거나 복제 피어링이 [다시 동기화 작업](cross-region-replication-manage-disaster-recovery.md#resync-replication)으로 중단된 경우에 적용됩니다. 

다음 표에서는 [미러 상태](cross-region-replication-display-health-status.md)를 기준으로 하는 대상 볼륨 크기 조정 동작에 대해 설명합니다.

|  미러 상태  | 대상 볼륨 크기 조정 동작 |
|-|-|
| *미러링됨* | 대상 볼륨이 초기화되어 미러링 업데이트를 받을 준비가 된 경우 원본 볼륨의 크기를 조정하면 대상 볼륨의 크기가 자동으로 조정됩니다. |
| *끊어짐* | 원본 볼륨의 크기를 조정하고 미러 상태가 *끊어짐* 인 경우 다시 [동기화 작업](cross-region-replication-manage-disaster-recovery.md#resync-replication)을 사용하여 대상 볼륨의 크기가 자동으로 조정됩니다.  |
| *초기화되지 않음* | 원본 볼륨의 크기를 조정할 때 미러 상태가 아직 *초기화되지 않음* 인 경우 대상 볼륨의 크기를 수동으로 조정해야 합니다. 따라서 초기화가 완료될 때까지 기다리는 것이 좋습니다. 즉, 미러 상태가 *미러링됨* 인 경우 원본 볼륨의 크기를 조정하는 것이 좋습니다. | 

> [!IMPORTANT]
> 지역 간 복제의 원본 볼륨과 대상 볼륨 모두의 용량 풀에 충분한 여유가 있는지 확인합니다. 원본 볼륨의 크기를 조정하면 대상 볼륨의 크기가 자동으로 조정됩니다. 그러나 대상 볼륨을 호스트하는 용량 풀에 충분한 여유가 없는 경우 원본 볼륨과 대상 볼륨 모두의 크기를 조정하지 못합니다.

## <a name="next-steps"></a>다음 단계

- [용량 풀 만들기](azure-netapp-files-set-up-capacity-pool.md)
- [수동 QoS 용량 풀 관리](manage-manual-qos-capacity-pool.md)
- [볼륨의 서비스 수준을 동적으로 변경](dynamic-change-volume-service-level.md) 
- [볼륨 할당량 이해](volume-quota-introduction.md)
- [볼륨 용량 모니터링](monitor-volume-capacity.md)
- [용량 관리 FAQ](faq-capacity-management.md)
