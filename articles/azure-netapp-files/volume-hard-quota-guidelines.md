---
title: 볼륨 하드 할당량 변경이 Azure NetApp Files 서비스에 미치는 영향 | Microsoft Docs
description: 볼륨 하드 할당량 사용에 대한 변경 사항, 변경 계획 방법, 용량 모니터링 및 관리 방법을 설명합니다.
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
ms.topic: conceptual
ms.date: 03/29/2021
ms.author: b-hchen
ms.openlocfilehash: f4f237818ec9e7598ea97be97b1918b870ffa83d
ms.sourcegitcommit: 9ef0965834870700468c822ddcafc011881fc2d5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/02/2021
ms.locfileid: "133480951"
---
# <a name="what-changing-to-volume-hard-quota-means-for-your-azure-netapp-files-service"></a>볼륨 하드 할당량 변경이 Azure NetApp Files 서비스에 미치는 영향

서비스의 시작부터 Azure NetApp Files는 용량 풀 프로비전 및 자동 증가 메커니즘을 사용하고 있습니다. Azure NetApp Files 볼륨은 선택한 계층 및 크기의 고객이 프로비전한 기본 용량 풀에서 씬 프로 비전됩니다. 볼륨 크기(할당량)는 성능 및 용량을 제공하는 데 사용되며 할당량은 언제든지 즉시 조정할 수 있습니다. 이 동작은 현재 볼륨 할당량이 볼륨에 대한 대역폭을 제어하는 데 사용되는 성능 레버라는 것을 의미합니다. 현재는 용량이 가득 차면 기본 용량 풀이 자동으로 증가합니다.   

> [!IMPORTANT] 
> 볼륨 및 용량 풀 프로비전의 Azure NetApp Files 동작은 *수동* 및 *제어 가능한* 메커니즘으로 변경됩니다. **2021년 4월 30일(업데이트 됨)부터 볼륨 크기(할당량)가 대역폭 성능과 프로비전된 용량을 관리하며, 기본 용량 풀은 더 이상 자동으로 증가하지 않습니다.** 

## <a name="reasons-for-the-change-to-volume-hard-quota"></a>볼륨 하드 할당량 변경 이유

많은 고객은 *초기* 동작에 대해 세 가지 주요 문제를 지적했습니다.
* VM 클라이언트는 OS 공간 또는 용량 모니터링 도구를 사용할 때 주어진 볼륨의 씬 프로비전(100TiB) 용량을 확인하여 부정확한 클라이언트 또는 애플리케이션 측 용량 가시성을 제공합니다.
* 애플리케이션 소유자는 용량 풀 자동 증가 동작으로 인해 프로비전된 용량 풀 공간(및 관련 비용)을 제어할 수 없습니다. 이 상황은 "런어웨이 프로세스"가 프로비전된 용량과 비용을 빠르게 채우고 증가시킬 수 있는 환경에서는 번거롭습니다.
* 고객은 볼륨 크기(할당량)와 성능 간의 직접적인 상관 관계를 확인하고 유지하기를 원합니다. 현재(암시적) 볼륨 초과 구독(용량 측면) 및 풀 자동 증가 동작으로 인해 고객은 볼륨 할당량이 적극적으로 설정되거나 재설정될 때까지 직접적인 상관 관계가 없습니다. 

많은 고객이 프로비전된 용량에 대한 직접 제어를 요청했습니다. 고객은 스토리지 용량 및 사용률을 제어하고 균형을 조정하고자 합니다. 또한 애플리케이션 볼륨의 가용, 사용 및 프로비전된 용량 및 성능에 대한 애플리케이션 측 및 클라이언트 쪽 가시성과 함께 비용을 제어하고자 합니다. 

## <a name="what-is-the-volume-hard-quota-change"></a>볼륨 하드 할당량 변경 내용   

볼륨 하드 할당량 변경으로 인해 Azure NetApp Files 볼륨은 더 이상 (최대) 100TiB로 씬 프로비전되지 않습니다. 볼륨은 실제 구성된 크기(할당량)로 프로비전됩니다. 또한 기본 용량 풀은 전체 용량 소비에 도달하면 더 이상 자동으로 증가하지 않습니다. 이 변경은 용량 자동 증가 없이도 있는 그대로 프로비전되는 Azure Managed Disks와 같은 동작을 반영합니다.

예를 들어 4TiB Ultra 서비스 수준 용량 풀에서 1TiB 크기(할당량)로 구성된 Azure NetApp Files 볼륨을 고려합니다. 애플리케이션이 볼륨에 지속적으로 데이터를 작성합니다.

*초기* 동작:  
* 예상 대역폭: 128MiB/s
* 사용 가능한(및 클라이언트 표시 가능한) 총 용량: 100TiB   
    이 크기를 초과하는 볼륨에는 데이터를 더 쓸 수 없습니다.
* 용량 풀: 가득 찬 경우 1TiB씩 자동으로 증가합니다.
* 볼륨 할당량 변경: 볼륨의 성능(대역폭)만 변경합니다. 클라이언트 표시 또는 가용 용량은 변경되지 않습니다.

*변경된* 동작:  
* 예상 대역폭: 128MiB/s
* 사용 가능한(및 클라이언트 표시) 총 용량: 1TiB 이 크기를 초과하는 볼륨에는 데이터를 더 쓸 수 없습니다.
* 용량 풀: 크기는 4TiB로 유지되며 자동으로 증가하지 않습니다. 
* 볼륨 할당량 변경: 볼륨의 성능(대역폭) 및 클라이언트의 표시 또는 사용 가능한 용량을 변경합니다.

Azure NetApp Files 볼륨 및 용량 풀의 사용률을 사전에 모니터링해야 합니다. 거의 모든 사용을 위해 볼륨 및 풀 사용률을 의도적으로 변경해야 합니다. Azure NetApp Files는 [즉시 볼륨 및 용량 풀 크기 조정 작업](azure-netapp-files-resize-capacity-pools-or-volumes.md)을 계속 허용합니다.

## <a name="how-to-operationalize-the-volume-hard-quota-change"></a>볼륨 하드 할당량 변경을 운영하는 방법

이 섹션에서는 원활한 전환을 위해 볼륨 하드 할당량 변경을 운영하는 방법에 대한 지침을 제공합니다. 또한 현재 프로비전된 볼륨 및 용량 풀, 진행 중인 모니터링, 경고 및 용량 관리 옵션에 대한 정보를 제공합니다.

### <a name="currently-provisioned-volumes-and-capacity-pools"></a>현재 프로비전된 볼륨 및 용량 풀

볼륨 하드 할당량 변경으로 인해 운영 모델을 변경해야 합니다. 프로비전된 볼륨과 용량 풀은 지속적인 용량 관리가 필요합니다.  변경된 동작은 즉시 발생하기 때문에 Azure NetApp Files 팀은 이 섹션에 설명된 대로 이전에 프로비전된 기존 볼륨 및 용량 풀에 대한 일련의 일회성 수정 조치를 권장합니다.

#### <a name="one-time-corrective-or-preventative-measures-recommendations"></a>일회성 수정 또는 예방 조치 권장 사항  

볼륨 하드 할당량 변경으로 인해 이전에 프로비전된 볼륨 및 풀에 대해 프로비전 및 사용 가능한 용량이 변경됩니다. 따라서 일부 용량 할당에 문제가 발생할 수 있습니다. 고객의 단기 공간 부족 상황을 방지하기 위해 Azure NetApp Files 팀은 다음과 같은 일회성 수정/예방 조치를 권장합니다. 

* **프로비전된 볼륨 크기**:   
    변경률 및 경고를 기반으로 적절한 버퍼를 갖도록 프로비전된 모든 볼륨의 크기를 조정하거나 처리 시간(예: 일반적인 워크로드 고려 사항에 따라 20%)을 최대 100TiB([볼륨 크기 제한](azure-netapp-files-resource-limits.md#resource-limits))로 조정합니다. 버퍼 용량을 포함한 이 새로운 볼륨 크기는 다음 요인을 기반으로 해야 합니다.
    * **프로비전된** 볼륨 용량으로, 사용된 용량이 프로비전된 볼륨 할당량보다 적은 경우입니다.
    * **사용된** 볼륨 용량으로, 사용된 용량이 프로비전된 볼륨 할당량보다 많은 경우입니다.  
    기본 용량 풀을 늘릴 필요가 없는 경우 볼륨 수준 용량 증가에 대한 추가 비용은 없습니다. 이 변경의 결과로, 볼륨에 대한 대역폭 제한이 *증가* 할 수 있습니다([자동 QoS 용량 풀 유형](azure-netapp-files-understand-storage-hierarchy.md#qos_types) 이 사용되는 경우).

* **프로비전된 용량 풀 크기**:   
    볼륨 크기 조정 후 볼륨 크기의 합계가 호스팅 용량 풀의 크기보다 커지면 용량 풀을 볼륨의 합계보다 크거나 같은 크기(최대 500TiB, [용량 풀 크기 제한](azure-netapp-files-resource-limits.md#resource-limits))로 늘려야 합니다. 추가 용량 풀 용량은 일반적으로 ACR 요금이 부과됩니다.

아래 섹션에 설명된 대로 모니터링 또는 경고 설정에 도움이 필요한 경우 Azure NetApp Files 전문가와 협력하여 환경의 유효성을 검사해야 합니다.

### <a name="ongoing-capacity-management"></a>지속적인 용량 관리  

일회성 수정 조치를 수행한 후에는 지속적인 프로세스를 통합하여 용량을 모니터링하고 관리해야 합니다. 다음 섹션에는 용량 모니터링 및 관리에 대한 제안 사항과 대안이 나와 있습니다.

### <a name="monitor-capacity-utilization"></a>용량 사용률 모니터링

다양한 수준에서 용량 활용도를 모니터링할 수 있습니다. 

#### <a name="vm-level-monitoring"></a>VM 수준 모니터링 

가장 높은 수준의 모니터링(애플리케이션에 가장 가까움)은 애플리케이션 가상 머신 내에서 수행됩니다. VM 클라이언트 OS 내에서 용량 보고 동작의 변화를 관찰할 수 있습니다.

다음의 두 시나리오에서는 4TiB Ultra 서비스 수준 용량 풀에서 1TiB 크기(할당량)로 구성된 Azure NetApp Files 볼륨을 고려합니다. 

##### <a name="windows"></a>Windows

Windows 클라이언트는 네트워크 매핑된 드라이브 속성을 사용하여 볼륨의 사용된 용량 및 사용 가능한 용량을 확인할 수 있습니다. **탐색기** -> **드라이브** -> **속성** 옵션을 사용할 수 있습니다.  

다음 예제에서는 변경된 동작 *전에* Windows에서 보고하는 볼륨 용량을 보여줍니다.

![동작 변경 전 볼륨의 스토리지 용량 예를 보여 주는 스크린샷입니다.](../media/azure-netapp-files/hard-quota-windows-capacity-before.png)

아래와 같이 명령 프롬프트에서 `dir` 명령을 사용할 수도 있습니다.

![동작이 변경되기 전에 명령을 사용하여 볼륨의 스토리지 용량을 표시하는 스크린샷입니다.](../media/azure-netapp-files/hard-quota-command-capacity-before.png)

다음 예제에서는 변경된 동작 *후에* Windows에서 보고하는 볼륨 용량을 보여줍니다.

![동작 변경 후 볼륨의 스토리지 용량 예를 보여 주는 스크린샷입니다.](../media/azure-netapp-files/hard-quota-windows-capacity-after.png)

다음 예제에서는 `dir` 명령 출력을 보여 줍니다.  

![동작이 변경된 후에 명령을 사용하여 볼륨의 스토리지 용량을 표시하는 스크린샷입니다.](../media/azure-netapp-files/hard-quota-command-capacity-after.png)

##### <a name="linux"></a>Linux 

Linux 클라이언트는 [`df` 명령](https://linux.die.net/man/1/df)을 사용하여 볼륨의 사용된 용량 및 사용 가능한 용량을 확인할 수 있습니다. `-h` 옵션은 M, G 및 T 단위 크기를 사용하여 사용자가 읽을 수 있는 형식의 크기, 사용된 공간 및 사용 가능한 공간을 표시합니다.

다음 예제에서는 변경된 동작 *전에* Linux의 볼륨 용량 보고를 보여 줍니다.  

![동작이 변경되기 전에 Linux를 사용하여 볼륨의 스토리지 용량을 표시하는 스크린샷입니다.](../media/azure-netapp-files/hard-quota-linux-capacity-before.png)

다음 예제에서는 변경된 동작 *후에* Linux의 볼륨 용량 보고를 보여 줍니다.  

![동작이 변경된 후에 Linux를 사용하여 볼륨의 스토리지 용량을 표시하는 스크린샷입니다.](../media/azure-netapp-files/hard-quota-linux-capacity-after.png)


### <a name="configure-alerts-using-anfcapacitymanager"></a>ANFCapacityManager를 사용하여 경고 구성

커뮤니티에서 지원하는 Logic Apps ANFCapacityManager 도구를 사용하여 Azure NetApp Files 용량을 모니터링하고 맞춤형 경고를 받을 수 있습니다. ANFCapacityManager 도구는 [ANFCapacityManager GitHub 페이지](https://github.com/ANFTechTeam/ANFCapacityManager)에서 제공됩니다.

ANFCapacityManager는 용량 기반 경고 규칙을 관리하는 Azure Logic Apps입니다. Azure NetApp Files 볼륨의 공간이 부족하지 않도록 볼륨 크기를 자동으로 늘립니다. 배포가 쉬우며 다음과 같은 경고 관리 기능을 제공합니다.

* Azure NetApp Files 용량 풀 또는 볼륨이 생성되면 ANFCapacityManager는 지정된 사용 비율 임계값을 기반으로 메트릭 경고 규칙을 만듭니다.
* Azure NetApp Files 용량 풀 또는 볼륨의 크기가 조정되면 ANFCapacityManager는 지정된 용량 사용 백분율 임계값을 기반으로 메트릭 경고 규칙을 수정합니다. 경고 규칙이 없으면 새로 만들어집니다.
* Azure NetApp Files 용량 풀 또는 볼륨이 삭제되면 해당 메트릭 경고 규칙이 삭제됩니다.

다음과 같은 주요 경고 설정을 구성할 수 있습니다.  

* **용량 풀(%) 전체 임계값** - 이 설정은 용량 풀에 대한 경고를 트리거하는 사용된 임계값을 결정합니다. 값이 90이면 용량 풀이 90% 사용에 도달할 때 경고가 트리거됩니다.
* **볼륨(%) 전체 임계값** - 이 설정은 볼륨에 대한 경고를 트리거하는 데 사용된 임계값을 결정합니다. 값이 80이면 볼륨이 80% 사용에 도달 할 때 경고가 트리거됩니다.
* **용량 알림을 위한 기존 작업 그룹** - 이 설정은 용량 기반 경고에 대해 트리거되는 작업 그룹입니다. 이 설정은 사용자가 미리 만들어야 합니다. 작업 그룹은 이메일, SMS 또는 기타 형식을 보낼 수 있습니다.

다음 일러스트레이션에서는 경고 구성을 보여 줍니다.  

![ANFCapacityManager를 사용하여 경고 구성을 보여 주는 일러스트레이션입니다.](../media/azure-netapp-files/hard-quota-anfcapacitymanager-configuration.png)

ANFCapacityManager를 설치한 후 다음 동작을 예상할 수 있습니다. Azure NetApp Files 용량 풀 또는 볼륨이 생성, 수정 또는 삭제되면 논리 앱이 이름(`ANF_Pool_poolname` 또는 `ANF_Volume_poolname_volname`)을 사용하여 용량 기반 메트릭 경고 규칙을 자동으로 생성, 수정 또는 삭제합니다. 

### <a name="manage-capacity"></a>용량 관리

모니터링 및 경고 외에도 Azure NetApp Files(증가된) 용량 소비를 관리하기 위한 애플리케이션 용량 관리 방식도 통합해야 합니다. Azure NetApp Files 볼륨 또는 용량 풀이 가득 차면 [애플리케이션 중단 없이 추가 용량을 즉시 제공](azure-netapp-files-resize-capacity-pools-or-volumes.md)할 수 있습니다. 이 섹션에서는 필요에 따라 볼륨 및 용량 풀 프로비전 공간을 늘리는 다양한 수동 및 자동화 방법을 설명합니다.
 
#### <a name="manual"></a>수동 

포털이나 CLI를 사용하여 볼륨 또는 용량 풀 크기를 수동으로 늘릴 수 있습니다. 

##### <a name="portal"></a>포털 

필요에 따라 [볼륨의 크기를 변경](azure-netapp-files-resize-capacity-pools-or-volumes.md#resize-a-volume-using-the-azure-portal)할 수 있습니다. 볼륨의 용량 소비는 해당 풀의 프로비전된 용량에 대해 계산됩니다.

1. NetApp 계정 관리 블레이드에서 **볼륨** 을 클릭합니다.  
2. 상황에 맞는 메뉴를 표시하려면 크기를 조정하려는 볼륨 이름을 마우스 오른쪽 단추를 클릭하거나 볼륨의 행의 끝에 있는 `…` 아이콘을 클릭합니다. 
3. 상황에 맞는 메뉴 옵션을 사용하여 볼륨을 크기 조정하거나 삭제합니다.   

   ![볼륨에 대한 상황에 맞는 메뉴 옵션을 보여 주는 스크린샷입니다.](../media/azure-netapp-files/hard-quota-volume-options.png) 

   ![볼륨 할당량 업데이트 창을 보여 주는 스크린샷입니다.](../media/azure-netapp-files/hard-quota-update-volume-quota.png) 

경우에 따라 호스팅 용량 풀에 볼륨 크기를 조정할 수 있는 충분한 용량이 없습니다. 그러나 1TiB씩 증가하거나 감소하여 [용량 풀 크기를 변경](azure-netapp-files-resize-capacity-pools-or-volumes.md#resizing-the-capacity-pool-or-a-volume-using-azure-cli)할 수 있습니다. 용량 풀 크기는 4TiB 미만일 수 없습니다. *용량 풀 크기를 조정하면 구매한 Azure NetApp Files 용량을 변경합니다.*

1. NetApp 계정 관리 블레이드에서 크기를 조정하려는 용량 풀을 클릭합니다.
2. 상황에 맞는 메뉴를 표시하려면 용량 풀 이름을 마우스 오른쪽 단추를 클릭하거나, 용량 풀의 행의 끝에 있는 `…` 아이콘을 클릭합니다.
3. 상황에 맞는 메뉴 옵션을 사용하여 용량 풀을 크기 조정하거나 삭제합니다.    

   ![용량 풀의 상황에 맞는 메뉴 옵션을 보여 주는 스크린샷입니다.](../media/azure-netapp-files/hard-quota-pool-options.png) 

   ![풀 크기 조정 창을 보여 주는 스크린샷입니다.](../media/azure-netapp-files/hard-quota-update-resize-pool.png) 


##### <a name="cli-or-powershell"></a>CLI 또는 PowerShell

Azure CLI 및 Azure PowerShell을 비롯한 [Azure NetApp Files CLI 도구](azure-netapp-files-sdk-cli.md#cli-tools)를 사용하여 볼륨 또는 용량 풀 크기를 수동으로 변경할 수 있습니다.  다음 두 명령을 사용하여 Azure NetApp Files 볼륨 및 풀 리소스를 관리할 수 있습니다.  

* [`az netappfiles pool`](/cli/azure/netappfiles/pool)
* [`az netappfiles volume`](/cli/azure/netappfiles/volume)

Azure CLI를 사용하여 Azure NetApp Files 리소스를 관리하려면 Azure Portal을 열고 메뉴 모음 상단에서 Azure **Cloud Shell** 링크를 선택합니다. 

[ ![Cloud Shell 링크에 액세스하는 방법을 보여 주는 스크린샷입니다.](../media/azure-netapp-files/hard-quota-update-cloud-shell-link.png) ](../media/azure-netapp-files/hard-quota-update-cloud-shell-link.png#lightbox)

이 작업을 수행하면 Azure Cloud Shell이 열립니다.

[ ![Cloud Shell 창을 보여 주는 스크린샷입니다.](../media/azure-netapp-files/hard-quota-update-cloud-shell-window.png) ](../media/azure-netapp-files/hard-quota-update-cloud-shell-window.png#lightbox)

다음 예제에서는 명령을 사용하여 볼륨 크기를 [표시](/cli/azure/netappfiles/volume#az_netappfiles_volume_show) 및 [업데이트](/cli/azure/netappfiles/volume#az_netappfiles_volume_update)합니다.
 
[ ![PowerShell을 사용하여 볼륨 크기를 보여 주는 스크린샷입니다.](../media/azure-netapp-files/hard-quota-update-powershell-volume-show.png) ](../media/azure-netapp-files/hard-quota-update-powershell-volume-show.png#lightbox)

[ ![PowerShell을 사용하여 볼륨 크기를 업데이트하는 방법을 보여 주는 스크린샷입니다.](../media/azure-netapp-files/hard-quota-update-powershell-volume-update.png) ](../media/azure-netapp-files/hard-quota-update-powershell-volume-update.png#lightbox)

다음 예제에서는 명령을 사용하여 용량 풀의 크기를 [표시](/cli/azure/netappfiles/pool#az_netappfiles_pool_show) 및 [업데이트](/cli/azure/netappfiles/pool#az_netappfiles_pool_update)합니다.

[ ![PowerShell을 사용하여 용량 풀 크기를 보여 주는 스크린샷입니다.](../media/azure-netapp-files/hard-quota-update-powershell-pool-show.png) ](../media/azure-netapp-files/hard-quota-update-powershell-pool-show.png#lightbox) 

[ ![PowerShell을 사용하여 용량 풀 크기를 업데이트하는 방법을 보여 주는 스크린샷입니다.](../media/azure-netapp-files/hard-quota-update-powershell-pool-update.png) ](../media/azure-netapp-files/hard-quota-update-powershell-pool-update.png#lightbox)

#### <a name="automated"></a>자동  

자동화된 프로세스를 구축하여 변경된 동작을 관리할 수 있습니다.

##### <a name="rest-api"></a>REST API   

Azure NetApp Files 서비스용 REST API는 NetApp 계정, 용량 풀, 볼륨 및 스냅샷과 같은 리소스에 대한 HTTP 작업을 정의합니다. Azure NetApp Files에 대한 REST API 사양은 [Azure NetApp Files Resource Manager GitHub 페이지](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/netapp/resource-manager)를 통해 게시됩니다. GitHub에서 [REST API와 함께 사용하기 위한 예제 코드](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/netapp/resource-manager/Microsoft.NetApp/stable/2020-06-01/examples)를 찾을 수 있습니다.

[REST API를 사용하여 Azure NetApp Files 개발](azure-netapp-files-develop-with-rest-api.md)을 참조하세요. 

##### <a name="rest-api-using-powershell"></a>PowerShell을 사용하는 REST API  

Azure NetApp Files 서비스용 REST API는 NetApp 계정, 용량 풀, 볼륨 및 스냅샷과 같은 리소스에 대한 HTTP 작업을 정의합니다. [Azure NetApp Files용 REST API 사양](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/netapp/resource-manager)은 GitHub를 통해 게시됩니다.

[PowerShell을 사용하여 REST API로 Azure NetApp Files 개발](develop-rest-api-powershell.md)을 참조하세요.

##### <a name="capacity-management-using-anfcapacitymanager"></a>ANFCapacityManager를 사용하여 용량 관리

ANFCapacityManager는 용량 기반 경고 규칙을 관리하는 Azure Logic Apps입니다. Azure NetApp Files 볼륨의 공간이 부족하지 않도록 볼륨 크기를 자동으로 늘립니다. 경고를 보내는 것 외에도 볼륨 및 용량 풀 크기의 자동 증가를 사용하도록 설정하여 Azure NetApp Files 볼륨의 공간 부족을 방지할 수 있습니다. 

* 필요에 따라 Azure NetApp Files 볼륨이 지정된 사용 백분율 임계값에 도달하면 볼륨 할당량(크기)이 10에서 100 사이의 지정된 백분율만큼 증가합니다.  
* 볼륨 크기 증가가 포함된 용량 풀의 용량을 초과하는 경우 새 볼륨 크기를 수용하기 위해 용량 풀 크기도 증가합니다.

다음과 같이 핵심 용량 관리 설정을 구성할 수 있습니다.  

* **자동 증가 백분율 증가** - 지정된 **% 전체 임계값** 에 도달하면 볼륨을 자동으로 증가시킬 기존 볼륨 크기의 백분율입니다. 값이 0이면 자동 증가 기능이 사용하지 않도록 설정됩니다. 10에서 100 사이의 값을 사용하는 것이 좋습니다.

    ![볼륨 자동 증가율 설정 창을 보여 주는 스크린샷입니다.](../media/azure-netapp-files/hard-quota-volume-anfcapacitymanager-auto-grow-percent.png) 

## <a name="faq"></a>FAQ 

이 섹션에는 볼륨 하드 할당량 변경에 대한 몇 가지 질문과 답변이 나와 있습니다. 

### <a name="does-snapshot-space-count-towards-the-usable-or-provisioned-capacity-of-a-volume"></a>스냅샷 공간이 볼륨의 가용 용량이나 프로비전된 용량에 포함되나요?

예. 사용된 스냅샷 용량은 볼륨의 프로비전된 공간에 포함됩니다. 볼륨이 가득 찬 경우 두 가지 수정 옵션을 고려하세요.

* 이 문서에 설명된 대로 볼륨의 크기를 조정합니다.
* 이전 스냅샷을 제거하여 호스팅 볼륨의 공간을 확보합니다.

### <a name="does-this-change-mean-the-volume-auto-grow-behavior-will-disappear-from-azure-netapp-files"></a>이렇게 변경하면 Azure NetApp Files에서 볼륨 자동 증가 동작이 사라지나요?

흔히 하는 오해는 Azure NetApp Files *볼륨* 이 채워지면 자동으로 증가한다는 것입니다. 볼륨은 실제 설정된 할당량에 관계없이 100TiB의 크기로 씬 프로비전되며 기본 *용량 풀* 은 1TiB씩 자동 증가합니다. 이렇게 변경하면 (표시 및 사용 가능한) *볼륨* 크기가 설정된 할당량으로 처리되고 *용량 풀* 이 더 이상 자동으로 증가하지 않습니다. 이 변경에 따라 일반적으로 원하는 정확한 클라이언트 쪽 공간 및 용량 보고가 수행됩니다. "런어웨이" 용량 소비를 방지합니다.

### <a name="does-this-change-have-any-effect-on-volumes-replicated-with-cross-region-replication-preview"></a>이 변경이 지역 간 복제(미리 보기)로 복제된 볼륨에 영향을 주나요? 

하드 볼륨 할당량은 복제 대상 볼륨에 적용되지 않습니다.

### <a name="does-this-change-have-any-effect-on-metrics-currently-available-in-azure-monitor"></a>이 변경이 현재 Azure Monitor에서 사용할 수 있는 메트릭에 영향을 미치나요?

포털 메트릭 및 Azure Monitor 통계는 새로운 할당 및 사용률 모델을 정확하게 반영합니다.

### <a name="does-this-change-have-any-effect-on-the-resource-limits-for-azure-netapp-files"></a>이 변경이 Azure NetApp Files의 리소스 제한에 영향을 주나요?

이 문서에 설명된 할당량 변경 외에 Azure NetApp Files에 대한 리소스 제한은 변경되지 않습니다.

### <a name="is-there-an-example-anfcapacitymanager-workflow"></a>ANFCapacityManager 워크플로에 대한 예제가 있나요?  

예. [볼륨 자동 증가 워크플로 예제 GitHub 페이지](https://github.com/ANFTechTeam/ANFCapacityManager/blob/master/ResizeWorkflow.md)를 참조하세요.

### <a name="is-anfcapacitymanager-microsoft-supported"></a>ANFCapacityManager Microsoft가 지원되나요?  

[ANFCapacityManager 논리 앱은 있는 그대로 제공되며 NetApp 또는 Microsoft에서는 지원되지 않습니다](https://github.com/ANFTechTeam/ANFCapacityManager#disclaimer). 특정 사용 환경 또는 요구 사항에 맞게 수정하는 것이 좋습니다. 중요 비즈니스용 또는 프로덕션 환경에 배포하기 전에 기능을 테스트해야 합니다.

### <a name="how-can-i-report-a-bug-or-submit-a-feature-request-for-anfcapacitymanger"></a>버그를 보고하거나 ANFCapacityManger에 대한 기능 요청을 제출하려면 어떻게 해야 하나요?
[ANFCapacityManager GitHub 페이지](https://github.com/ANFTechTeam/ANFCapacityManager/issues)에서 **새 문제** 를 클릭하여 버그 및 기능 요청을 제출할 수 있습니다.

## <a name="next-steps"></a>다음 단계
* [용량 풀 또는 볼륨 크기 조정](azure-netapp-files-resize-capacity-pools-or-volumes.md) 
* [Azure NetApp Files에 대한 메트릭](azure-netapp-files-metrics.md)
