---
author: bwren
ms.author: bwren
ms.service: azure-monitor
ms.topic: include
ms.date: 09/17/2021
ms.openlocfilehash: 5d31cea42dd51cee65e79c44b2a44615db027aff
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132298028"
---
Azure Monitor의 로그 데이터는 Log Analytics 작업 영역에 저장됩니다. 구독에서 작업 영역을 이미 만든 경우 해당 작업 영역을 사용할 수 있습니다. 또한 각 Azure 구독에서 만든 기본 작업 영역을 사용하도록 선택할 수 있습니다. 

새 Log Analytics를 만들려는 경우 다음 절차를 사용할 수 있습니다. 기존 항목을 사용하려는 경우 다음 섹션으로 이동합니다.

Azure Portal의 **모든 서비스** 에서 **Log Analytics 작업 영역** 을 선택합니다.

:::image type="content" source="media/azure-monitor-tutorial-workspace/azure-portal.png" lightbox="media/azure-monitor-tutorial-workspace/azure-portal.png" alt-text="Azure Portal에서 Log Analytics 작업 영역 선택":::

**만들기** 를 클릭하여 새 작업 영역을 만듭니다.

:::image type="content" source="media/azure-monitor-tutorial-workspace/create-workspace.png" lightbox="media/azure-monitor-tutorial-workspace/create-workspace.png" alt-text="작업 영역 만들기 단추":::

**기본 사항** 탭에서 작업 영역에 대한 **구독**, **리소스 그룹** 및 **지역** 을 선택합니다. 모니터링할 리소스와 동일할 필요는 없습니다. 모든 Azure Monitor 구독에서 전역적으로 고유해야 하는 **이름** 을 제공합니다.

:::image type="content" source="media/azure-monitor-tutorial-workspace/workspace-basics.png" lightbox="media/azure-monitor-tutorial-workspace/workspace-basics.png" alt-text="작업 영역 기본 사항":::


**검토 + 만들기** 를 클릭하여 작업 영역을 만듭니다.