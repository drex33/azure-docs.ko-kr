---
title: 연결되지 않은 NIC를 찾아서 삭제
description: Azure CLI를 사용하여 VM에 연결되지 않은 Azure NIC를 찾아서 삭제하는 방법
author: cynthn
ms.service: virtual-machines
ms.subservice: networking
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 04/10/2018
ms.author: cynthn
ms.openlocfilehash: 35c2bb34b3fa19a8bc127f45fe00223686f820b6
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/23/2021
ms.locfileid: "122691899"
---
# <a name="how-to-find-and-delete-unattached-network-interface-cards-nics-for-azure-vms"></a>Azure VM에 대한 연결되지 않은 NIC(네트워크 인터페이스 카드)를 찾아서 삭제하는 방법

**적용 대상:** :heavy_check_mark: Linux VM :heavy_check_mark: 유연한 확장 집합 

Azure에서 VM(가상 머신)을 삭제하는 경우 NIC(네트워크 인터페이스 카드)는 삭제되지 않습니다. 여러 VM을 만들고 삭제하는 경우 사용되지 않은 NIC는 내부 IP 주소 임대를 계속 사용합니다. 다른 VM NIC를 만들 때는 서브넷의 주소 공간에서 IP 임대를 획득하지 못할 수 있습니다. 이 문서에서는 연결되지 않은 NIC를 찾아서 삭제하는 방법을 보여 줍니다.

## <a name="find-and-delete-unattached-nics"></a>연결되지 않은 NIC 찾기 및 삭제

NIC에 대한 *virtualMachine* 속성은 NIC가 연결된 NIC는 VM의 ID 및 리소스 그룹을 저장합니다. 다음 스크립트는 구독의 모든 NIC를 반복하면서 *virtualMachine* 속성이 Null인지 확인합니다. 이 속성이 Null이면 NIC는 VM에 연결되지 않은 것입니다.

연결되지 않은 모든 NIC를 보려면 먼저 *deleteUnattachedNics* 변수를 *0* 으로 설정하여 스크립트를 실행하는 것이 좋습니다. 목록 출력을 검토한 후에 연결되지 않은 모든 NIC를 삭제하려면 *deleteUnattachedNics* 를 *1* 로 설정하여 스크립트를 실행합니다.

```azurecli
# Set deleteUnattachedNics=1 if you want to delete unattached NICs
# Set deleteUnattachedNics=0 if you want to see the Id(s) of the unattached NICs
deleteUnattachedNics=0

unattachedNicsIds=$(az network nic list --query '[?virtualMachine==`null`].[id]' -o tsv)
for id in ${unattachedNicsIds[@]}
do
   if (( $deleteUnattachedNics == 1 ))
   then

       echo "Deleting unattached NIC with Id: "$id
       az network nic delete --ids $id
       echo "Deleted unattached NIC with Id: "$id
   else
       echo $id
   fi
done
```

## <a name="next-steps"></a>다음 단계

Azure에서 가상 네트워크를 만들고 관리하는 방법에 대한 자세한 내용은 [VM 네트워크 만들기 및 관리](tutorial-virtual-network.md)를 참조하세요.
