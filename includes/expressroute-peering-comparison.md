---
author: cherylmc
ms.date: 12/13/2019
ms.service: expressroute
ms.topic: include
ms.author: cherylmc
ms.openlocfilehash: 47e3d4e86e5288bb9a75d9d2b9a0b053ac50fc8e
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130242286"
---
|  | **프라이빗 피어링** | **Microsoft 피어링** |  **공용 피어링**(새 회로에 사용되지 않음) |
| --- | --- | --- | --- |
| **피어링당 지원되는 최대값 # 접두사** |기본적으로 4000, ExpressRoute 프리미엄으로 10,000 |200 |200 |
| **지원되는 IP 주소 범위** |WAN 내의 유효한 IP 주소. |사용자 또는 연결 공급자가 소유한 공용 IP 주소. |사용자 또는 연결 공급자가 소유한 공용 IP 주소. |
| **AS 번호 요구 사항** |프라이빗 및 공용 AS 번호. 공용 AS 번호를 사용하려는 경우 해당 번호를 소유하고 있어야 합니다. |프라이빗 및 공용 AS 번호. 하지만, 공용 IP 주소의 소유권을 증명해야 합니다. |프라이빗 및 공용 AS 번호. 하지만, 공용 IP 주소의 소유권을 증명해야 합니다. |
| **지원되는 IP 프로토콜**| IPv4, IPv6 |  IPv4, IPv6 | IPv4 |
| **라우팅 인터페이스 IP 주소** |RFC1918 및 공용 IP 주소 |라우팅 레지스트리의 사용자에게 등록된 공용 IP 주소. |라우팅 레지스트리의 사용자에게 등록된 공용 IP 주소. |
| **MD5 해시 지원** |예 |예 |예 |
