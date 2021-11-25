---
author: cherylmc
ms.author: cherylmc
ms.date: 07/02/2021
ms.service: bastion
ms.topic: include
ms.openlocfilehash: 1829cd779d317a0ae10bd56c2639fd39778c5cf7
ms.sourcegitcommit: 56235f8694cc5f88db3afcc8c27ce769ecf455b0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133105623"
---
 >[!IMPORTANT]
 >2021 년 11 월 2 일 이후에 배포 되는 Azure 방호 리소스의 경우 최소 AzureBastionSubnet 크기는/26 이상 (/25,/24 등)입니다. 이 날짜 이전에 크기/27의 서브넷에 배포 된 모든 Azure 방호 리소스는이 변경의 영향을 받지 않으며 계속 작동 하지만, 나중에 [호스트 크기 조정을](../articles/bastion/configure-host-scaling.md) 사용 하도록 선택 하는 경우 기존 AzureBastionSubnet의 크기를/26으로 늘릴 것을 권장 합니다.


* 만들 수 있는 가장 작은 서브넷 AzureBastionSubnet 크기는/26입니다. 호스트 크기 조정을 수용 하기 위해/26 이상의 크기를 만드는 것이 좋습니다. 
   * 크기 조정에 대한 자세한 내용은 [구성 설정 - 호스트 크기 조정](../articles/bastion/configuration-settings.md#instance)을 참조하세요.
   * 설정에 대한 자세한 내용은 [구성 설정 - AzureBastionSubnet](../articles/bastion/configuration-settings.md#instance)을 참조하세요.
* 경로 테이블 또는 위임 없이 **AzureBastionSubnet** 을 만듭니다. 
* **AzureBastionSubnet** 의 네트워크 보안 그룹을 사용하는 경우 [NSG 사용](../articles/bastion/bastion-nsg.md) 문서를 참조하세요.
