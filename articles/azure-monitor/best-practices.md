---
title: Azure Monitor 모범 사례
description: Azure Monitor 배포하기 위한 지침 및 권장 사항입니다.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/18/2021
ms.openlocfilehash: 2caff70e56f80d4f46859cec58275263375ee4de
ms.sourcegitcommit: 92889674b93087ab7d573622e9587d0937233aa2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/19/2021
ms.locfileid: "130182379"
---
# <a name="azure-monitor-best-practices"></a>Azure Monitor 모범 사례
이 시나리오에서는 클라우드 및 하이브리드 애플리케이션 및 리소스의 성능과 가용성을 모니터링하도록 Azure Monitor 기능을 구성하는 데 권장되는 지침을 제공합니다. 

Azure Monitor Azure 구독을 만드는 순간 사용할 수 있습니다. 활동 로그는 즉시 구독의 활동에 대한 이벤트 수집을 시작하고, 만든 모든 Azure 리소스에 대해 플랫폼 메트릭이 수집됩니다. 메트릭 탐색기 등의 기능을 사용하여 데이터를 분석할 수 있습니다. 다른 기능에는 구성이 필요합니다. 이 시나리오에서는 모든 Azure Monitor 기능을 활용하는 데 필요한 구성 단계를 식별합니다. 또한 활용해야 하는 기능 및 특정 요구 사항에 따라 구성 옵션을 결정하는 방법에 대한 권장 사항도 제공합니다.

Azure Monitor에서 모든 Azure 리소스를 모니터링할 수 있도록 설정하는 것은 Azure Monitor 구성 요소를 구성하고 Azure Monitor가 수집할 모니터링 데이터를 생성하도록 Azure 리소스를 구성하는 것입니다. 완전한 구현의 목표는 모든 클라우드 리소스 및 애플리케이션에서 모든 유용한 데이터를 수집하고 해당 데이터를 기반으로 전체 Azure Monitor 기능 집합을 사용하도록 설정하는 것입니다.


> [!IMPORTANT]
> Azure Monitor 익숙하지 않거나 단순히 단일 Azure 리소스를 모니터링하는 데 집중하는 경우 Azure Monitor 사용하여 [Azure 리소스 모니터링](/essentials/monitor-azure-resource.md)자습서로 시작해야 합니다. 이 자습서에서는 Azure Monitor 대한 일반적인 개념과 단일 Azure 리소스를 모니터링하기 위한 지침을 제공합니다. 이 시나리오에서는 전체 애플리케이션 및 리소스 집합을 대규모로 모니터링하는 데 Azure Monitor 모든 기능을 활용하도록 환경을 준비하기 위한 권장 사항을 제공합니다.

## <a name="scope-of-the-scenario"></a>시나리오의 범위
이 시나리오의 목표는 완전한 Azure Monitor 구현의 기본 단계를 안내하여 해당 기능을 최대한 활용하고 클라우드 및 하이브리드 애플리케이션 및 리소스의 관찰 가능성을 극대화하는 것입니다. 실제 구성 세부 정보가 아닌 구성 요구 사항 및 배포 옵션에 중점을 둡니다. 실제로 필요한 구성을 수행하기 위한 세부 정보를 제공하는 다른 콘텐츠에 대한 링크가 제공됩니다.

## <a name="scenario-articles"></a>시나리오 문서
이 문서에서는 시나리오를 소개합니다. 특정 영역으로 바로 이동하려면 다음 표에 설명된 이 시나리오의 일부인 다른 문서 중 하나를 참조하세요.

| 아티클 | 설명 |
|:---|:---|
| [계획](best-practices-plan.md)  | 구현을 시작하기 전에 고려해야 할 계획입니다. 조직 및 수집해야 하는 요구 사항에 대한 디자인 결정 및 정보를 포함합니다. |
| [데이터 수집 구성](best-practices-data-collection.md) | Azure 및 하이브리드 애플리케이션 및 리소스에서 모니터링 데이터를 수집하는 데 필요한 작업입니다. |
| [분석 및 시각화](best-practices-analysis.md) | 수집된 모니터링 데이터를 분석하기 위해 만들 수 있는 표준 기능 및 추가 시각화입니다. |
| [경고 및 자동화된 응답](best-practices-alerts.md) | 경고가 생성될 때 자동으로 트리거되는 알림 및 프로세스를 구성합니다. |




## <a name="next-steps"></a>다음 단계

- [모니터링 전략 및 구성 계획](best-practices-plan.md)