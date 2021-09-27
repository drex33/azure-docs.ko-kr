---
title: HCX 네트워크 확장 만들기
description: 온-프레미스 환경에서 Azure VMware 솔루션으로 네트워크를 확장 하는 방법을 알아봅니다.
ms.topic: how-to
ms.date: 09/07/2021
ms.openlocfilehash: fd58285ab7e2999c758e818020e39799a19f6c53
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124838682"
---
# <a name="create-a-hcx-network-extension"></a>HCX 네트워크 확장 만들기

온-프레미스 환경에서 Azure VMware Solution으로 네트워크를 확장할 수 있는 선택적 단계입니다.

1. **서비스** 아래에서 **네트워크 확장** > **네트워크 확장 만들기** 를 차례로 선택합니다.

   :::image type="content" source="media/tutorial-vmware-hcx/create-network-extension.png" alt-text="네트워크 확장 만들기를 시작하기 위한 선택 항목을 보여주는 스크린샷" lightbox="media/tutorial-vmware-hcx/create-network-extension.png":::

1. Azure VMware Solution으로 확장하려는 각 네트워크를 선택하고 **다음** 을 선택합니다.

   :::image type="content" source="media/tutorial-vmware-hcx/select-extend-networks.png" alt-text="네트워크 선택 항목을 보여주는 스크린샷":::

1. 확장하려는 각 네트워크의 온-프레미스 게이트웨이 IP를 입력한 다음, **제출** 을 선택합니다.

   :::image type="content" source="media/tutorial-vmware-hcx/extend-networks-gateway.png" alt-text="게이트웨이 IP 주소의 항목을 보여주는 스크린샷":::

   네트워크 확장이 완료될 때까지 몇 분 정도 걸립니다. 확장이 완료되면 상태가 **확장 완료** 로 표시됩니다.

   :::image type="content" source="media/tutorial-vmware-hcx/extension-complete.png" alt-text="[확장 완료] 상태를 보여주는 스크린샷":::


