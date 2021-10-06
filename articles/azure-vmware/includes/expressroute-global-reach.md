---
title: Azure VMware Solution 네트워킹 및 연결
description: Azure VMware Solution 네트워킹 및 연결에 대해 설명합니다.
ms.topic: include
ms.date: 08/10/2021
author: shortpatti
ms.author: v-patsho
ms.service: azure-vmware
ms.openlocfilehash: c80e256691066239d89b101f206f758725809fad
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128909314"
---
<!-- Used in articles\azure-vmware\introduction.md and articles\azure-vmware\concepts-networking.md 

articles\azure-vmware\includes\azure-vmware-solution-networking-description.md

-->

[ExpressRoute Global Reach](../../expressroute/expressroute-global-reach.md)는 프라이빗 클라우드를 온-프레미스 환경에 연결하는 데 사용됩니다. MSEE(Microsoft Enterprise Edge) 수준에서 회로를 직접 연결합니다. 연결하려면 구독에서 온-프레미스에 연결되는 ExpressRoute 회로를 사용하는 vNet(가상 네트워크)이 필요합니다.  그 이유는 vNet 게이트웨이(ExpressRoute 게이트웨이)가 트래픽을 전송할 수 없기 때문입니다. 즉, 동일한 게이트웨이에 두 개의 회로를 연결할 수 있지만 회로 간에 트래픽을 보내지 않습니다.

각 Azure VMware Solution 환경은 '로컬' 피어링 위치에 Global Reach를 연결할 수 있는 자체 ExpressRoute 지역(자체 가상 MSEE 디바이스)입니다.  한 지역에 있는 Azure VMware Solution 인스턴스 여러 개를 같은 피어링 위치에 연결할 수 있습니다. 

>[!NOTE]
>예를 들어 로컬 규정으로 인해 ExpressRoute Global Reach가 활성화되지 않은 위치의 경우 Azure IaaS VM을 사용하여 라우팅 솔루션을 빌드해야 합니다. 몇 가지 예는 [AzureCAT-AVS/네트워킹](https://github.com/Azure/AzureCAT-AVS/tree/main/networking)을 참조하세요.

