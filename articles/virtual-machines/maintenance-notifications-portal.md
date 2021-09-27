---
title: 유지 관리 알림에 포털 사용
description: 포털을 사용하여 Azure에서 실행 중인 가상 머신에 대한 유지 관리 알림을 확인하고 셀프 서비스 유지 관리를 시작합니다.
author: shants123
ms.service: virtual-machines
ms.subservice: maintenance-control
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 09/08/2021
ms.author: shants
ms.openlocfilehash: f0d07f37d01c2634df17d1f53aad4fd180e60ba4
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124818331"
---
# <a name="handling-planned-maintenance-notifications-using-the-portal"></a>포털을 사용하여 계획된 유지 관리 알림 처리

**적용 대상:** :heavy_check_mark: Linux VM :heavy_check_mark: Windows VM :heavy_check_mark: 유연한 확장 집합 :heavy_check_mark: 단일 확장 집합

[계획된 유지 관리](maintenance-notifications.md) 웨이브가 예약되면 영향을 받는 가상 머신의 목록을 확인할 수 있습니다. 

Azure Portal을 사용하여 유지 관리하도록 예약된 VM을 찾아볼 수 있습니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

2. 왼쪽 탐색 모음에서 **Virtual Machines** 를 클릭합니다.

3. Virtual Machines 창에서 **유지 관리-> 가상 머신 유지 관리** 단추를 선택 하 여 유지 관리 열이 있는 목록을 엽니다.

   **유지 관리 상태**: VM에 대한 유지 관리 상태를 표시합니다. 가능한 값은 다음과 같습니다.
      
    | 값 | Description |
    |-------|-------------|
    | 지금 시작 | VM이 유지 관리를 직접 시작할 수 있는 셀프 서비스 유지 관리 기간에 있습니다. VM에서 유지 관리를 시작하는 방법은 아래를 참조하세요. | 
    | 예약됨 | 유지 관리를 시작하기 위한 옵션 없이 VM의 유지 관리가 예약됩니다. 해당 보기에서 유지 관리 - 예약 창을 선택하거나 VM을 클릭하여 유지 관리 기간을 알아볼 수 있습니다. | 
    | 이미 업데이트됨 | VM은 이미 업데이트되었으며 지금은 필요한 추가 작업이 없습니다. | 
    | 나중에 다시 시도 | 유지 관리를 시작했으나 실패했습니다. 나중에 셀프 서비스 유지 관리 옵션을 사용할 수 있습니다. | 
    | 지금 다시 시도 | 이전에 실패한 셀프 시작 유지 관리를 다시 시도할 수 있습니다. | 
    | - | VM이 계획된 유지 관리 웨이브에 속하지 않습니다. |

   **유지 관리 - 셀프 서비스 기간**: VM에서 유지 관리를 직접 시작할 수 있는 기간을 보여 줍니다.
   
   **유지 관리 - 예약된 기간**: Azure에서 유지 관리를 완료하기 위해 VM을 유지 관리할 기간을 보여 줍니다. 



## <a name="notification-and-alerts-in-the-portal"></a>포털에서 알림 및 경고

Azure에서는 구독 소유자 및 공동 소유자 그룹에 이메일을 보내 계획된 유지 관리를 위한 일정을 알립니다. Azure 활동 로그 경고를 만들어 이 통신에 받는 사람 및 채널을 더 추가할 수 있습니다. 자세한 내용은 [서비스 알림에 대한 활동 로그 경고 만들기](../service-health/alerts-activity-log-service-notifications-portal.md)를 참조하세요.

**이벤트 유형** 을 **계획된 유지 관리** 로, **서비스** 를 **Virtual Machine Scale Sets** 및/또는 **Virtual Machines** 로 설정했는지 확인합니다.

## <a name="start-maintenance-on-your-vm-from-the-portal"></a>포털에서 VM에 대한 유지 관리 시작

VM 세부 정보를 확인하는 동안 유지 관리 관련 세부 정보를 자세히 볼 수 있습니다.  
VM 세부 정보 보기의 맨 위에서, VM이 계획된 유지 관리 웨이브에 포함되면 새 알림 리본이 추가됩니다. 또한 가능하면 유지 관리를 시작하는 새로운 옵션이 추가됩니다. 


유지 관리 알림을 클릭하면 유지 관리 페이지에 계획된 유지 관리에 대한 자세한 정보가 표시됩니다. 여기에서 VM에 대한 **유지 관리를 시작** 할 수 있습니다.

유지 관리를 시작하면 가상 머신이 유지 관리되고 몇 분 이내 결과를 반영하여 유지 관리 상태가 업데이트됩니다.

셀프 서비스 기간을 놓쳤더라도 Azure에서 VM이 유지 관리될 기간을 계속 확인할 수 있습니다. 


## <a name="next-steps"></a>다음 단계

[Azure CLI](maintenance-notifications-cli.md) 또는 [PowerShell](maintenance-notifications-powershell.md)을 사용하여 계획된 유지 관리를 처리할 수도 있습니다.
