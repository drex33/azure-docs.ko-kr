---
title: Azure VMware Solution 네트워킹 및 연결
description: Azure VMware Solution 네트워킹 및 연결에 대해 설명합니다.
ms.topic: include
ms.date: 08/10/2021
author: shortpatti
ms.author: v-patsho
ms.service: azure-vmware
ms.openlocfilehash: 569beec244e2ccf80aae3e4bb3f7a3e2cf65980a
ms.sourcegitcommit: e7d500f8cef40ab3409736acd0893cad02e24fc0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122070748"
---
<!-- Used in articles\azure-vmware\introduction.md and articles\azure-vmware\concepts-networking.md 

articles\azure-vmware\includes\azure-vmware-solution-networking-description.md

-->

[ExpressRoute Global Reach](../../expressroute/expressroute-global-reach.md)는 프라이빗 클라우드를 온-프레미스 환경에 연결하는 데 사용됩니다. MSEE(Microsoft Enterprise Edge) 수준에서 회로를 직접 연결하므로 연결에는 구독의 온-프레미스에 대한 ExpressRoute 회로가 있는 vNet(가상 네트워크)이 필요합니다.  그 이유는 vNet 게이트웨이(ExpressRoute 게이트웨이)가 트래픽을 전송할 수 없기 때문입니다. 즉, 동일한 게이트웨이에 두 개의 회로를 연결할 수 있지만 회로 간에 트래픽을 보내지 않습니다.

>[!NOTE]
>예를 들어 로컬 규정으로 인해 ExpressRoute Global Reach가 활성화되지 않은 위치의 경우 Azure IaaS VM을 사용하여 라우팅 솔루션을 빌드해야 합니다. 몇 가지 예는 [AzureCAT-AVS/네트워킹](https://github.com/Azure/AzureCAT-AVS/tree/main/networking)을 참조하세요.

