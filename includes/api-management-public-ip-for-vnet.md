---
author: dlepow
ms.service: api-management
ms.topic: include
ms.date: 04/12/2021
ms.author: danlep
ms.openlocfilehash: bc537ab8a2c030c74c06ab9ae8bc512ea16cf75c
ms.sourcegitcommit: b00a2d931b0d6f1d4ea5d4127f74fc831fb0bca9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/20/2021
ms.locfileid: "132869322"
---
* **표준 SKU [공용 IPv4 주소](../articles/virtual-network/ip-services/public-ip-addresses.md#sku)** 입니다. 공용 IP 주소 리소스는 외부 또는 내부 액세스를 위해 가상 네트워크를 설정할 때 필요합니다. 내부 가상 네트워크를 사용하는 경우 공용 IP 주소는 관리 작업에만 사용됩니다. [API Management의 IP 주소](../articles/api-management/api-management-howto-ip-addresses.md)에 대해 자세히 알아보세요.

  * IP 주소는 API Management 인스턴스 및 가상 네트워크와 동일한 지역 및 구독에 있어야 합니다.

  * IP 주소의 값은 해당 지역에 API Management 인스턴스의 가상 공용 IPv4 주소로 할당됩니다. 

  * 외부 가상 네트워크에서 내부 가상 네트워크로 또는 그 반대로 변경하거나, 네트워크에서 서브넷을 변경하거나, API Management 인스턴스의 가용성 영역을 업데이트하는 경우 다른 공용 IP 주소를 구성해야 합니다.
