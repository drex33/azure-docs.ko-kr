---
title: 가용성 집합 개요
description: Azure의 가용성 집합에 대한 자세한 정보
author: mimckitt
ms.author: mimckitt
ms.service: virtual-machines
ms.topic: conceptual
ms.date: 02/18/2021
ms.openlocfilehash: ac6995a2653ea9f88a25e75f6c1b98e2446a4788
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128622439"
---
# <a name="availability-sets-overview"></a>가용성 집합 개요

**적용 대상:** :heavy_check_mark: Linux VM :heavy_check_mark: Windows VM

이 문서에서는 Azure VM(가상 머신)의 가용성 기능에 대한 개요를 제공합니다.

## <a name="what-is-an-availability-set"></a>가용성 집합이란? 

가용성 집합은 중복성과 가용성을 제공하기 위해 Azure에서 애플리케이션이 빌드되는 방식을 이해할 수 있도록 하는 VM의 논리적 그룹입니다. 고가용성 애플리케이션을 제공하고 [99.95% Azure SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/)를 충족할 수 있도록 가용성 집합 내에 둘 이상의 VM을 만드는 것이 좋습니다. 가용성 집합 자체에 대한 비용은 없으므로 만드는 각 VM 인스턴스에 대해서만 요금을 지불합니다.

## <a name="how-do-availability-sets-work"></a>가용성 집합 작동 방식
기본 Azure 플랫폼에서는 가용성 집합에 포함된 각각의 가상 머신을 **업데이트 도메인** 및 **장애 도메인** 에 할당합니다. 각 가용성 집합은 최대 3개의 장애 도메인과 20개의 업데이트 도메인으로 구성할 수 있습니다. 업데이트 도메인은 동시에 다시 부팅할 수 있는 가상 머신 그룹과 기본 물리적 하드웨어를 나타냅니다. 5개의 업데이트 도메인이 있는 단일 가용성 내에서 5개 이상의 가상 머신이 구성된 경우 여섯 번째 가상 머신은 첫 번째 가상 머신과 동일한 업데이트 도메인에 배치되고, 7번째 가상 머신은 두 번째 가상 머신과 동일한 업데이트 도메인에 배치됩니다. 재부팅되는 업데이트 도메인의 순서는 계획된 유지 보수 중 순차적으로 진행할 수 없으며 한 번에 하나의 업데이트 도메인만이 재부팅됩니다. 다시 부팅된 업데이트 도메인을 복구할 시간으로 30분이 제공되며 이 시간이 지나면 다른 업데이트 도메인에서 유지 관리가 시작됩니다.

장애 도메인은 공통 전원과 네트워크 스위치를 공유하는 가상 머신 그룹을 정의합니다. 기본적으로 가용성 집합 내에 구성된 가상 머신은 최대 3개의 장애 도메인에 분산되어 있습니다. 가상 머신을 가용성 집합에 배치한다고 해서 애플리케이션이 운영 체제 또는 애플리케이션 고유의 오류로부터 보호되는 것은 아닙니다. 잠재적인 물리적 하드웨어 오류, 네트워크 중단, 전력 차단의 영향이 제한될 뿐입니다.

:::image type="content" source="./media/virtual-machines-common-manage-availability/ud-fd-configuration.png" alt-text="다양한 컴퓨팅 클러스터가 장애 도메인으로 분할되고 해당 장애 도메인 내에 여러 업데이트 도메인이 있음을 보여주는 다이어그램":::

또한 VM은 디스크 장애 도메인에 맞춰 정렬됩니다. 이러한 정렬은 VM에 연결된 모든 관리 디스크가 동일한 장애 도메인 내에 있도록 합니다. 

관리 디스크의 VM만 관리 가용성 집합에서 만들어질 수 있습니다. 관리 디스크 장애 도메인의 수는 지역에 따라 다릅니다. 즉, 지역당 2개 또는 3개의 관리 디스크 장애 도메인이 있을 수 있습니다. 

:::image type="content" source="./media/virtual-machines-common-manage-availability/md-fd-updated.png" alt-text="디스크 및 VM의 장애 도메인을 정렬하는 방법을 보여주는 다이어그램.":::

## <a name="next-steps"></a>다음 단계
이제 이러한 가용성 및 중복 기능을 사용하여 Azure 환경을 빌드하기 시작할 수 있습니다. 모범 사례 정보는 [Azure 가용성 모범 사례](/azure/architecture/checklist/resiliency-per-service)를 참조하세요.

