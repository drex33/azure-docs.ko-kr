---
author: cherylmc
ms.author: cherylmc
ms.date: 07/02/2021
ms.service: bastion
ms.topic: include
ms.openlocfilehash: 62df24c61cf50f57e5f59ebca8eb2d11b2d03b12
ms.sourcegitcommit: 75ad40bab1b3f90bb2ea2a489f8875d4b2da57e4
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/12/2021
ms.locfileid: "113640646"
---
* 만들 수 있는 가장 작은 서브넷 AzureBastionSubnet 크기는 /27입니다. 그러나 호스트 크기 조정을 수용할 수 있도록 /26 이상 크기를 만드는 것이 좋습니다. 
   * 크기 조정에 대한 자세한 내용은 [구성 설정 - 호스트 크기 조정](../articles/bastion/configuration-settings.md#instance)을 참조하세요.
   * 설정에 대한 자세한 내용은 [구성 설정 - AzureBastionSubnet](../articles/bastion/configuration-settings.md#instance)을 참조하세요.
* 경로 테이블 또는 위임 없이 **AzureBastionSubnet** 을 만듭니다. 
* **AzureBastionSubnet** 의 네트워크 보안 그룹을 사용하는 경우 [NSG 사용](../articles/bastion/bastion-nsg.md) 문서를 참조하세요.
