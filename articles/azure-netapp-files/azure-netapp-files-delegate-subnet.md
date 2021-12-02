---
title: Azure NetApp Files에 서브넷 위임 | Microsoft Docs
description: 서브넷을 Azure NetApp Files에 위임하는 방법에 대해 알아봅니다. 볼륨을 만들 때는 위임된 서브넷을 지정해야 합니다.
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
ms.date: 08/25/2021
ms.author: b-hchen
ms.openlocfilehash: 9acc31a0605ea54c679ecfe49d53a8a93169cf11
ms.sourcegitcommit: 9ef0965834870700468c822ddcafc011881fc2d5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/02/2021
ms.locfileid: "133486431"
---
# <a name="delegate-a-subnet-to-azure-netapp-files"></a>Azure NetApp Files에 서브넷 위임 

Azure NetApp Files에 서브넷을 위임해야 합니다.   볼륨을 만들 때는 위임된 서브넷을 지정해야 합니다.

## <a name="considerations"></a>고려 사항

* 새 서브넷 만들기용 마법사의 기본값은 /24 네트워크 마스크이며, 이는 251개의 IP 주소를 제공합니다. 11개의 사용 가능한 IP 주소를 제공하는 /28 네트워크 마스크를 대부분의 사용 사례에 충분합니다. 많은 볼륨과 스토리지 엔드포인트가 예상되는 SAP HANA와 같은 시나리오에서는 더 큰 서브넷(예: /26 네트워크 마스크)을 고려해야 합니다. Azure VNet(Virtual Network)에서 많은 클라이언트 또는 VM IP 주소를 예약할 필요가 없는 경우 마법사에서 제안한 기본 네트워크 마스크 /24를 유지할 수도 있습니다. 위임된 네트워크의 네트워크 마스크는 초기 생성 후에 변경할 수 없습니다. 
* 각 VNet에서 하나의 서브넷만 Azure NetApp Files에 위임할 수 있습니다.   
   Azure를 통해 VNet에서 여러 개의 위임된 서브넷을 만들 수 있습니다.  그러나 두 개 이상의 위임된 서브넷을 사용하는 경우 새 볼륨을 만들려고 할 때마다 모두 실패합니다.  
   VNet에는 위임된 서브넷이 하나만 있을 수 있습니다. NetApp 계정은 각각 고유한 위임된 서브넷이 있는 여러 VNet에 볼륨을 배포할 수 있습니다.  
* 위임된 서브넷에서 네트워크 보안 그룹 또는 서비스 엔드포인트를 지정할 수 없습니다. 이렇게 하면 서브넷 위임이 실패합니다.
* 전역적으로 피어링된 가상 네트워크에서 볼륨에 액세스하는 것은 현재 지원되지 않습니다.
* [UDR](../virtual-network/virtual-networks-udr-overview.md#custom-routes)(사용자 정의 경로)과 NSG(네트워크 보안 그룹)는 Azure NetApp Files의 위임된 서브넷에서 지원되지 않습니다. 그러나 Azure NetApp Files에 위임된 서브넷과 동일한 VNet 내 에서도 UDR와 NSG를 다른 서브넷에 적용할 수 있습니다.  
   Azure NetApp Files는 위임된 서브넷에 대한 시스템 경로를 만듭니다. 경로는 문제를 해결하는 데 필요한 경우 경로 테이블의 **유효 경로** 에 표시됩니다.

## <a name="steps"></a>단계

1.  Azure Portal에서 **가상 네트워크** 블레이드로 이동하여 Azure NetApp Files에 사용할 가상 네트워크를 선택합니다.    

1. 가상 네트워크 블레이드에서 **서브넷** 을 선택하고 **+서브넷** 단추를 클릭합니다. 

1. 서브넷 추가 페이지에서 다음 필수 필드를 완료하여 Azure NetApp Files에 사용할 새 서브넷을 만듭니다.
    * **이름**: 서브넷 이름을 지정합니다.
    * **주소 범위**: IP 주소 범위를 지정합니다.
    * **서브넷 위임**: **Microsoft.NetApp/volumes** 를 선택합니다. 

      ![서브넷 위임](../media/azure-netapp-files/azure-netapp-files-subnet-delegation.png)
    
[Azure NetApp Files용 볼륨을 만들](azure-netapp-files-create-volumes.md) 때 서브넷을 만들고 위임할 수도 있습니다. 

## <a name="next-steps"></a>다음 단계

* [Azure NetApp Files에 대한 볼륨 만들기](azure-netapp-files-create-volumes.md)
* [Azure 서비스에 대한 가상 네트워크 통합에 대해 알아보기](../virtual-network/virtual-network-for-azure-services.md)
