---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 4ce308871a14ee972290fca47bda8f2f677ebf0c
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130217616"
---
* 변환은 VM을 다시 시작하므로 기존 유지 관리 기간 동안 VM의 마이그레이션을 예약합니다. 

* 변환하면 되돌릴 수 없습니다. 

* [가상 머신 참가자](../articles/role-based-access-control/built-in-roles.md#virtual-machine-contributor) 역할이 있는 사용자는 VM 크기를 변경할 수 없습니다. (사전 변환이 가능하기 때문입니다.) 이는 관리 디스크가 있는 VM의 경우 사용자에게 OS 디스크에 대한 Microsoft.Compute/disks/write 권한이 있어야 하기 때문입니다.

* 변환을 테스트해야 합니다. 프로덕션에서 마이그레이션을 수행하기 전에 테스트 가상 머신을 마이그레이션합니다.

* 변환 중 VM의 할당을 취소합니다. VM은 변환 후 시작될 때 새 IP 주소를 받습니다. 필요한 경우 VM에 [고정 IP 주소를 할당](../articles/virtual-network/ip-services/public-ip-addresses.md)할 수 있습니다.

* 변환 프로세스를 지원하는 데 필요한 최소 버전의 Azure VM 에이전트를 검토합니다. 에이전트 버전을 확인하고 업데이트하는 방법에 대한 정보는 [Azure에서 VM 에이전트에 대한 최소 버전 지원](https://support.microsoft.com/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support)을 참조하세요.