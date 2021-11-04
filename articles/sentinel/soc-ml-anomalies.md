---
title: SOC-ML 변칙을 사용하여 Azure Sentinel에서 위협 검색 | Microsoft Docs
description: 이 문서에서는 Azure Sentinel에서 새로운 SOC-ML 변칙 검색 기능을 사용하는 방법을 설명합니다.
services: sentinel
cloud: na
documentationcenter: na
author: yelevin
manager: rkarlin
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/28/2021
ms.author: yelevin
ms.custom: ignite-fall-2021
ms.openlocfilehash: daa21e4c0caf27f87f3143dacc1d22ac0a5c0f57
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131064053"
---
# <a name="use-soc-ml-anomalies-to-detect-threats-in-azure-sentinel"></a>SOC-ML 변칙을 사용하여 Azure Sentinel에서 위협 검색

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

> [!IMPORTANT]
>
> - SOC-ML 변칙은 현재 **미리 보기** 로 제공됩니다. 베타 또는 미리 보기로 제공되거나 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 추가 약관은 [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

## <a name="what-are-soc-ml-anomalies"></a>SOC-ML 변칙이란?*

사이버 보안 군비 경쟁에서 공격자와 방어자가 끊임없이 싸우는 상황에서 공격자는 항상 탐지를 피할 방법을 찾고 있습니다. 그러나 불가피하게 공격은 여전히 공격받는 시스템에서 비정상적인 동작을 초래합니다. Azure Sentinel의 SOC-ML 기계 학습 기반 변칙은 즉시 작동하도록 배치할 수 있는 분석 규칙 템플릿을 사용하여 이 동작을 식별할 수 있습니다. 변칙은 악의적이거나 심지어 의심스러운 행동을 의미하지는 않지만 검색, 조사 및 위협 검색을 개선하는 데 사용할 수 있습니다.

- **검색 향상을 위한 추가 신호**: 보안 분석가는 변칙을 사용하여 새로운 위협을 검색하고 기존 검색을 보다 효과적으로 만들 수 있습니다. 단일 변칙은 악의적인 동작에 대한 강력한 신호가 아니지만 Kill 체인의 서로 다른 지점에서 발생하는 몇 가지 변칙과 함께 사용하면 누적 효과가 훨씬 더 강해집니다. 보안 분석가는 변칙으로 식별된 비정상적인 동작을 경고가 실행되는 조건으로 설정하여 기존 탐지 기능을 향상시킬 수도 있습니다.

- **조사 중 증거**: 보안 분석가는 또한 조사 중에 변칙을 사용하여 변칙을 확인하고 새로운 조사 경로를 찾고 잠재적 영향을 평가할 수 있습니다. 이러한 효율성은 보안 분석가가 조사에 소비하는 시간을 단축합니다.

- **사전 위협 헌팅의 시작**: 위협 헌터는 변칙을 컨텍스트로 사용하여 쿼리에서 의심스러운 동작을 발견했는지 확인할 수 있습니다. 동작이 의심스러운 경우 변칙은 추가 헌팅에 대한 잠재적 경로를 가리키기도 합니다. 변칙에서 제공하는 이러한 단서를 통해 위협을 검색하는 시간과 피해가 발생할 가능성을 줄일 수 있습니다.

변칙은 강력한 도구일 수 있지만 매우 노이즈가 많습니다. 일반적으로 특정 환경 또는 복잡한 사후 처리에 대해 많은 지루한 조정이 필요합니다. Azure Sentinel SOC-ML 변칙 템플릿은 데이터 과학 팀에서 기본 값을 제공하도록 조정되지만, 더 자세히 조정해야 하는 경우에는 프로세스가 간단하며 기계 학습에 대한 지식이 필요하지 않습니다. 많은 변칙에 대한 임계값 및 매개 변수는 이미 익숙한 분석 규칙 사용자 인터페이스를 통해 구성하고 미세 조정할 수 있습니다. 원래 임계값 및 매개 변수의 성능을 인터페이스 내의 새 임계값과 비교하고 테스트 또는 플라이팅 단계에서 필요에 따라 추가로 조정할 수 있습니다. 변칙이 성능 목표를 충족하면 단추를 클릭하여 새 임계값 또는 매개 변수가 있는 변칙을 프로덕션으로 승격할 수 있습니다. Azure Sentinel SOC-ML 변칙을 사용하면 어려운 작업 없이도 변칙의 이점을 얻을 수 있습니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 SOC-ML을 통해 Azure Sentinel 변칙을 검색하는 방법을 알아보았습니다.

- [변칙 규칙을 보고, 만들고, 관리하고, 미세 조정](work-with-anomaly-rules.md)하는 방법에 대해 알아봅니다.
- [다른 유형의 분석 규칙](detect-threats-built-in.md)에 대해 알아봅니다.
