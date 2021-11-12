---
title: VM 인사이트에서 모니터링 사용 안 함
description: 이 문서에서는 VM 인사이트에서 가상 머신 모니터링을 중지하는 방법을 설명합니다.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/12/2020
ms.openlocfilehash: 8edb225316dfd12acff5c1f6a036246d68d746c8
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132306438"
---
# <a name="disable-monitoring-of-your-vms-in-vm-insights"></a>VM 인사이트에서 VM 모니터링 사용 안 함

VM(가상 머신) 모니터링을 사용으로 설정한 후, 나중에 VM 인사이트에서 모니터링을 사용하지 않게 선택할 수 있습니다. 이 문서에서는 하나 이상의 VM에 대한 모니터링을 사용하지 않게 설정하는 방법을 보여 줍니다.  

현재 VM 인사이트에서는 VM 모니터링을 선택적 사용하지 않게 설정하는 기능을 지원하지 않습니다. Log Analytics 작업 영역에서는 VM 인사이트와 기타 솔루션을 지원할 수 있습니다. 다른 모니터링 데이터도 수집할 수 있습니다. Log Analytics 작업 영역에서 이 서비스를 제공하는 경우 시작하기 전에 모니터링을 사용하지 않게 설정하는 메소드와 그 영향을 이해해야 합니다.

VM 인사이트는 해당 환경을 제공하기 위해 다음 구성 요소를 사용합니다.

* VM 및 기타 원본의 모니터링 데이터를 저장하는 Log Analytics 작업 영역.
* 작업 영역에 구성된 성능 카운터의 컬렉션. 이 컬렉션은 작업 영역에 연결된 모든 VM의 모니터링 구성을 업데이트합니다.
* `VMInsights` - 작업 영역에 구성된 모니터링 솔루션입니다. 이 솔루션은 작업 영역에 연결된 모든 VM의 모니터링 구성을 업데이트합니다.
* `MicrosoftMonitoringAgent`(Windows의 경우) 또는 `OmsAgentForLinux`(Linux의 경우) 및 `DependencyAgent` - Azure VM 확장입니다. 이러한 확장은 데이터를 수집하여 작업 영역으로 보냅니다.

VM 모니터링을 사용하지 않도록 준비할 때 다음 사항을 염두에 두어야 합니다.

* 단일 VM으로 평가하고 미리 선택된 기본 Log Analytics 작업 영역을 사용한 경우 VM에서 종속성 에이전트를 제거하고 이 작업 영역에서 Log Analytics 에이전트의 연결을 해제하여 모니터링을 비활성화할 수 있습니다. 이 접근 방식은 다른 용도로 VM을 사용하고 나중에 다른 작업 영역에 VM을 다시 연결하려는 경우에 적합합니다.
* 다른 모니터링 솔루션 및 다른 원본의 데이터 수집을 지원하는 기존 Log Analytics 작업 영역을 선택한 경우 작업 영역을 중단하거나 영향을 주지 않고 작업 영역에서 솔루션 구성 요소를 제거할 수 있습니다.  

>[!NOTE]
> 작업 영역에서 솔루션 구성 요소를 제거한 후에도 Azure VM의 성능 및 매핑 데이터를 계속 볼 수 있습니다. 데이터는 결국 **성능** 및 **맵** 보기에 표시되지 않습니다. **사용** 옵션은 향후 모니터링을 다시 사용으로 설정할 수 있도록 선택한 Azure VM에서 사용할 수 있습니다.  

## <a name="remove-vm-insights-completely"></a>VM 인사이트를 완전히 제거

Log Analytics 작업 영역이 여전히 필요한 경우 다음 단계에 따라 VM 인사이트를 완전히 제거합니다. 작업 영역에서 `VMInsights` 솔루션을 제거합니다.  

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. Azure Portal에서 **모든 서비스** 를 선택합니다. 리소스 목록에서 **Log Analytics** 를 입력합니다. 입력을 시작하면 입력한 내용을 바탕으로 목록에서 제안 항목이 필터링됩니다. **Log Analytics** 를 선택합니다.
3. VM 인사이트를 사용하도록 설정했을 때 선택한 작업 영역을 Log Analytics 작업 영역 목록에서 선택합니다.
4. 왼쪽에서 **솔루션** 을 선택합니다.  
5. 솔루션 목록에서 **VMInsights(작업 영역 이름)** 를 선택합니다. 솔루션의 **개요** 페이지에서 **삭제** 를 선택합니다. 확인하라는 프롬프트가 나타나면 **예** 를 선택합니다.

## <a name="disable-monitoring-and-keep-the-workspace"></a>모니터링 사용 안 함 및 작업 영역 유지  

Log Analytics 작업 영역이 여전히 다른 원본의 모니터링을 지원해야 하는 경우 다음 단계에 따라 VM 인사이트를 평가하는 데 사용한 VM에서 모니터링을 사용하지 않게 설정합니다. Azure VM인 경우 해당 VM에서 직접 Windows 또는 Linux용 종속성 에이전트 VM 확장 및 Log Analytics 에이전트 VM 확장을 제거합니다. 

>[!NOTE]
>다음의 경우 Log Analytics 에이전트를 제거하지 마세요. 
>
> * Azure Automation은 프로세스를 오케스트레이션하거나 구성 또는 업데이트를 관리하기 위해 VM을 관리합니다. 
> * Microsoft Defender for Cloud는 보안 및 위협 탐지를 위해 VM을 관리합니다. 
>
> Log Analytics 에이전트를 제거하면 해당 서비스 및 솔루션이 VM을 사전에 관리하지 못하게 됩니다. 

1. [Azure Portal](https://portal.azure.com)에 로그인합니다. 
2. Azure Portal에서 **Virtual Machines** 를 선택합니다. 
3. 목록에서 VM을 선택합니다. 
4. 왼쪽에서 **확장** 을 선택합니다. **확장** 페이지에서 **DependencyAgent** 를 선택합니다.
5. 확장 속성 페이지에서 **제거** 를 선택합니다.
6. **확장** 페이지에서 **MicrosoftMonitoringAgent** 를 선택합니다. 확장 속성 페이지에서 **제거** 를 선택합니다.  
