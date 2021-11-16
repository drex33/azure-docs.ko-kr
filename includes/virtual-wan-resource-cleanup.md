---
author: cherylmc
ms.author: cherylmc
ms.date: 10/15/2021
ms.service: virtual-wan
ms.topic: include
ms.openlocfilehash: 5cac86b599e7e52cbc6018de6d97392bf28e4608
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131520470"
---
1. 만든 가상 WAN을 엽니다.

1. 가상 WAN에 연결된 가상 허브를 선택하여 허브 페이지를 엽니다.

1. 게이트웨이 유형에 대해 아래 순서에 따라 모든 게이트웨이 엔터티를 삭제합니다. 이 작업을 완료하는 데 약 30분이 걸릴 수 있습니다.

    **VPN:**  
   1. VPN 사이트 연결 끊기  
   1. VPN 연결 삭제  
   1. VPN 게이트웨이 삭제  

    **ExpressRoute:**  
   1. ExpressRoute 연결 삭제  
   1. ExpressRoute 게이트웨이 삭제  

1. 이 시점에서 허브를 삭제하거나, 나중에 리소스 그룹을 삭제할 때 허브를 삭제할 수 있습니다.

1. 가상 WAN에 연결된 모든 허브에 대해 반복합니다.

1. Azure Portal에서 리소스 그룹으로 이동합니다.

1. **리소스 그룹 삭제** 를 선택합니다. 그러면 허브 및 가상 WAN을 포함하여 리소스 그룹의 모든 항목이 삭제됩니다.