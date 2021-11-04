---
author: cherylmc
ms.author: cherylmc
ms.date: 07/02/2021
ms.service: bastion
ms.topic: include
ms.openlocfilehash: 8a56b564d9126197b76d6e96cc56bb51eca980eb
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131520059"
---
* 만들 수 있는 가장 작은 서브넷 AzureBastionSubnet 크기는/26입니다. 호스트 크기 조정을 수용 하기 위해/26 이상의 크기를 만드는 것이 좋습니다. 
   * 크기 조정에 대한 자세한 내용은 [구성 설정 - 호스트 크기 조정](../articles/bastion/configuration-settings.md#instance)을 참조하세요.
   * 설정에 대한 자세한 내용은 [구성 설정 - AzureBastionSubnet](../articles/bastion/configuration-settings.md#instance)을 참조하세요.
* 경로 테이블 또는 위임 없이 **AzureBastionSubnet** 을 만듭니다. 
* **AzureBastionSubnet** 의 네트워크 보안 그룹을 사용하는 경우 [NSG 사용](../articles/bastion/bastion-nsg.md) 문서를 참조하세요.
