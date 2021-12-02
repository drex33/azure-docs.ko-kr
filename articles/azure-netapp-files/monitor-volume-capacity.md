---
title: Azure NetApp Files 볼륨의 용량 모니터링 | Microsoft Docs
description: Azure NetApp Files 볼륨의 용량 사용률을 모니터링하는 방법을 설명합니다.
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
ms.openlocfilehash: b3b97e690f3e86c7ad87b3105042756f43e7bef4
ms.sourcegitcommit: 9ef0965834870700468c822ddcafc011881fc2d5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/02/2021
ms.locfileid: "133485528"
---
# <a name="monitor-the-capacity-of-a-volume"></a>볼륨 용량 모니터링  

이 문서에서는 Azure NetApp Files 볼륨의 용량 사용률을 모니터링하는 방법을 설명합니다.  

## <a name="using-windows-or-linux-clients"></a>Windows 또는 Linux 클라이언트 사용

이 섹션에서는 Windows 또는 Linux 클라이언트를 사용하여 볼륨 용량을 모니터링하는 방법을 보여 줍니다. 이 섹션에서 설명하는 시나리오는 4TiB Ultra 서비스 수준 용량 풀에서 1TiB 크기(할당량)로 구성된 볼륨을 가정합니다. 

### <a name="windows-smb-clients"></a>Windows(SMB) 클라이언트

Windows 클라이언트를 사용하여 네트워크 매핑된 드라이브 속성을 통해 볼륨의 사용된 용량 및 사용 가능한 용량을 확인할 수 있습니다. 다음 두 가지 방법 중 하나를 사용할 수 있습니다. 

* 파일 탐색기로 이동하여 매핑된 드라이브를 마우스 오른쪽 단추로 클릭하고 **속성** 을 선택하여 용량을 표시합니다.  

    [ ![탐색기 드라이브 속성 및 볼륨 속성을 보여 주는 스크린샷.](../media/azure-netapp-files/monitor-explorer-drive-properties.png) ](../media/azure-netapp-files/monitor-explorer-drive-properties.png#lightbox)

* 명령 프롬프트에서 `dir` 명령을 사용합니다. 

    ![dir 명령을 사용하여 용량을 표시하는 방법을 보여 주는 스크린샷.](../media/azure-netapp-files/monitor-volume-properties-dir-command.png) 

정확한 ‘사용 가능한 공간’을 확인하려면 파일 탐색기 또는 `dir` 명령을 사용합니다. 그러나 볼륨에 스냅샷이 생성되는 경우에는 ‘사용된 공간’이 예상 공간이 됩니다. [사용된 스냅샷 용량](azure-netapp-files-cost-model.md#capacity-consumption-of-snapshots) 은 볼륨에서 사용된 총 공간으로 계산됩니다. 스냅샷에 사용된 용량을 포함하여 절대 볼륨 사용량을 가져오려면 Azure Portal에서 [Azure NetApp 메트릭](azure-netapp-files-metrics.md#volumes)을 사용합니다. 

### <a name="linux-nfs-clients"></a>Linux(NFS) 클라이언트 

Linux 클라이언트는 [df 명령](https://linux.die.net/man/1/df)을 사용하여 볼륨의 사용된 용량 및 사용 가능한 용량을 확인할 수 있습니다.  

`-h` 옵션은 사용된 공간 및 사용 가능한 공간을 비롯하여 크기를 사용자가 읽을 수 있는 형식으로(M, G, T 단위 크기 사용) 표시합니다.

다음 스냅샷은 Linux의 볼륨 용량 보고를 보여 줍니다.

![Linux의 볼륨 용량 보고를 보여 주는 스크린샷.](../media/azure-netapp-files/monitor-volume-properties-linux-command.png) 

정확한 ‘사용 가능한 공간’을 확인하려면 `df` 명령을 사용합니다. 그러나 볼륨에 스냅샷이 생성되는 경우에는 ‘사용된 공간’이 예상 공간이 됩니다. [사용된 스냅샷 용량](azure-netapp-files-cost-model.md#capacity-consumption-of-snapshots) 은 볼륨에서 사용된 총 공간으로 계산됩니다. 스냅샷에 사용된 용량을 포함하여 절대 볼륨 사용량을 가져오려면 Azure Portal에서 [Azure NetApp 메트릭](azure-netapp-files-metrics.md#volumes)을 사용합니다. 

## <a name="using-azure-portal"></a>Azure Portal 사용
Azure NetApp Files는 표준 [Azure Monitor](../azure-monitor/overview.md) 기능을 활용합니다. 따라서 Azure Monitor를 사용하여 Azure NetApp Files 볼륨을 모니터링할 수 있습니다.  

## <a name="using-azure-cli"></a>Azure CLI 사용  

[Azure CLI(명령줄) 도구](azure-netapp-files-sdk-cli.md)의 [`az netappfiles volume`](/cli/azure/netappfiles/volume?view=azure-cli-latest&preserve-view=true) 명령을 사용하여 볼륨을 모니터링할 수 있습니다.
 
## <a name="using-rest-api"></a>REST API 사용  

[Azure NetApp Files용 REST API](azure-netapp-files-develop-with-rest-api.md) 및 [Azure NetApp Files용 PowerShell을 사용한 REST API](develop-rest-api-powershell.md)를 참조하세요. 

Azure NetApp Files에 대한 REST API 사양과 예제 코드는 [resource-manager GitHub 디렉터리](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/netapp/resource-manager/Microsoft.NetApp/stable)를 통해 사용할 수 있습니다. 

## <a name="next-steps"></a>다음 단계

* [볼륨 할당량 이해](volume-quota-introduction.md)
* [Azure NetApp Files 비용 모델](azure-netapp-files-cost-model.md)
* [용량 풀 또는 볼륨 크기 조정](azure-netapp-files-resize-capacity-pools-or-volumes.md)
* [용량 관리 FAQ](faq-capacity-management.md)