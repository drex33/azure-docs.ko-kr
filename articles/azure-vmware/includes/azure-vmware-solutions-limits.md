---
title: Azure VMware Solution 제한
description: Azure VMware Solution 제한 사항.
ms.topic: include
ms.date: 03/24/2021
author: shortpatti
ms.author: v-patsho
ms.service: azure-vmware
ms.openlocfilehash: 02840f5d74a1490b0af71933f795385b70467d7b
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122535757"
---
<!-- Used in /azure/azure-resource-manager/management/azure-subscription-service-limits.md and concepts-networking.md -->

다음 표에서는 Azure VMware Solution의 최대 제한에 대해 설명합니다.

| **리소스** | **제한** |
| :-- | :-- |
| 프라이빗 클라우드당 클러스터 수 | 12 |
| 클러스터당 최소 호스트 수 | 3 |
| 클러스터당 최대 호스트 수 | 16 |
| 프라이빗 클라우드당 호스트 수 | 96 |
| 프라이빗 클라우드당 vCenter | 1  |
| HCX 사이트 페어링 | 3개(고급 버전 포함), 10개(엔터프라이즈 버전 포함) |
| AVS ExpressRoute 최대 연결된 프라이빗 클라우드 | 4<br />사용된 가상 네트워크 게이트웨이는 실제 최대 연결된 프라이빗 클라우드를 결정합니다.  자세한 내용은 [ExpressRoute 가상 네트워크 게이트웨이 정보](../../expressroute/expressroute-about-virtual-network-gateways.md)를 참조하세요. | 
| AVS ExpressRoute 포트 속도 | 10Gbps<br />사용되는 가상 네트워크 게이트웨이는 실제 대역폭을 결정합니다. 자세한 내용은 [ExpressRoute 가상 네트워크 게이트웨이 정보](../../expressroute/expressroute-about-virtual-network-gateways.md)를 참조하세요. | 
| vWAN을 통해 노출되는 공용 IP | 100 |
| vSAN 용량 제한 | 총 가용의 75%(SLA에 25% 사용 가능)  |

다른 VMware 관련 제한에 대해서는 [VMware 구성 최대 도구](https://configmax.vmware.com/)를 사용하세요.
