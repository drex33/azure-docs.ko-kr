---
title: Azure VMware Solution 네트워킹 및 연결
description: Azure VMware Solution 네트워킹 및 연결에 대해 설명합니다.
ms.topic: include
ms.date: 08/10/2021
author: suzizuber
ms.author: v-szuber
ms.service: azure-vmware
ms.openlocfilehash: 0ee52f437027690787ca5dc95eda2705b796b604
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/06/2021
ms.locfileid: "129638379"
---
<!-- Used in introduction.md and concepts-networking.md -->

Azure VMware Solution은 온-프레미스 및 Azure 기반 리소스에서 액세스할 수 있는 프라이빗 클라우드 환경을 제공합니다. Azure ExpressRoute, VPN 연결 또는 Azure Virtual WAN과 같은 서비스는 연결을 제공합니다. 그러나 이러한 서비스에는 서비스를 활성화하기 위한 특정 네트워크 주소 범위와 방화벽 포트가 필요합니다.

프라이빗 클라우드가 배포되면 관리, 프로비저닝 및 vMotion을 위한 개인 네트워크가 만들어집니다. 이러한 프라이빗 네트워크를 사용하여 vCenter 및 NSX-T Manager와 가상 머신 vMotion 또는 배포에 액세스합니다.

[!INCLUDE [expressroute-global-reach](expressroute-global-reach.md)]

프라이빗 클라우드에 배포된 가상 머신은 [Azure Virtual WAN 공용 IP](../enable-public-internet-access.md) 기능을 통해 인터넷에 액세스할 수 있습니다. 새 프라이빗 클라우드의 경우 기본적으로 인터넷 액세스가 비활성화되어 있습니다. 



