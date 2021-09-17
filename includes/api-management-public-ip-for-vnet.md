---
author: dlepow
ms.service: api-management
ms.topic: include
ms.date: 04/12/2021
ms.author: danlep
ms.openlocfilehash: 1cac7360aefbbeb74cbba340c33b155941793fb4
ms.sourcegitcommit: f2d0e1e91a6c345858d3c21b387b15e3b1fa8b4c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/07/2021
ms.locfileid: "123537241"
---
* **표준 SKU [공용 IPv4 주소](../articles/virtual-network/public-ip-addresses.md#standard)** 입니다. 공용 IP 주소 리소스는 외부 또는 내부 액세스를 위해 가상 네트워크를 설정할 때 필요합니다. 내부 가상 네트워크를 사용하는 경우 공용 IP 주소는 관리 작업에만 사용됩니다. [API Management의 IP 주소](../articles/api-management/api-management-howto-ip-addresses.md)에 대해 자세히 알아보세요.

  * IP 주소는 API Management 인스턴스 및 가상 네트워크와 동일한 지역 및 구독에 있어야 합니다.

  * IP 주소의 값은 해당 지역에 API Management 인스턴스의 가상 공용 IPv4 주소로 할당됩니다. 

  * 외부 가상 네트워크에서 내부 가상 네트워크로 또는 그 반대로 변경하거나, 네트워크에서 서브넷을 변경하거나, API Management 인스턴스의 가용성 영역을 업데이트하는 경우 다른 공용 IP 주소를 구성해야 합니다. 

