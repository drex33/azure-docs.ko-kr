---
title: Microsoft Sentinel | NRT(근 실시간) 분석 규칙을 사용하여 위협을 빠르게 검색 Microsoft Docs
description: 이 문서에서는 새로운 NRT(근 실시간) 분석 규칙을 통해 Microsoft Sentinel에서 위협을 신속하게 검색하는 방법을 설명합니다.
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
ms.date: 11/09/2021
ms.author: yelevin
ms.custom: ignite-fall-2021
ms.openlocfilehash: 70fbed7df5ece53eb4403fea7f055563eaac1633
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132308189"
---
# <a name="detect-threats-quickly-with-near-real-time-nrt-analytics-rules-in-microsoft-sentinel"></a>Microsoft Sentinel에서 거의 실시간(NRT) 분석 규칙을 사용하여 위협을 신속하게 검색

> [!IMPORTANT]
>
> - NRT(근 실시간) 규칙은 현재 미리 **보기** 에 있습니다. 베타 또는 미리 보기로 제공되거나 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 추가 약관은 [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

## <a name="what-are-near-real-time-nrt-analytics-rules"></a>NRT(근 실시간) 분석 규칙은 무엇인가요?

보안 위협에 직면하는 경우 시간과 속도가 가장 핵심입니다. 위협이 구체화될 때 위협을 파악하여 신속하게 분석하고 대응하여 포함할 수 있어야 합니다. Microsoft Sentinel의 NRT(근 실시간) 분석 규칙은 온-프레미스 SIEM에 더 가까운 빠른 위협 탐지와 특정 시나리오에서 응답 시간을 단축하는 기능을 제공합니다.

Microsoft Sentinel의 [근 실시간 분석 규칙은](detect-threats-built-in.md#nrt) 가장 가까운 시간 위협 탐지 기능을 제공합니다. 이 유형의 규칙은 단 1분 간격으로 쿼리를 실행하여 응답성이 뛰어나도록 설계되었습니다.

## <a name="how-do-they-work"></a>어떻게 작동 합니까?

NRT 규칙은 1분마다 한 번씩 실행되고 이전 분에 수집된 이벤트를 캡처하도록 하드 코딩되므로 최대한 많은 정보를 제공할 수 있습니다.

기본 제공 5분 지연에서 실행되는 일반 예약된 규칙과 달리 NRT 규칙은 단 2분 지연으로 실행되어 원본(TimeGenerated 필드)에서 생성 시간이 아닌 이벤트의 집계 시간을 쿼리하여 집계 지연 문제를 해결합니다. 이렇게 하면 검색에서 빈도와 정확도가 모두 향상됩니다. (이 문제를 완전히 이해하려면 [쿼리 예약 및 경고 임계값 및](detect-threats-custom.md#query-scheduling-and-alert-threshold) Microsoft [Sentinel 예약된 경고 규칙에서의 검색 지연 처리에서](https://techcommunity.microsoft.com/t5/azure-sentinel/handling-ingestion-delay-in-azure-sentinel-scheduled-alert-rules/ba-p/2052851)언급된 연결된 문서를 참조하세요.)

NRT 규칙에는 예약된 분석 규칙과 동일한 많은 기능이 있습니다. 전체 경고 보강 기능 집합을 사용할 수 있습니다. 엔터티를 매핑하고 사용자 지정 세부 정보를 표면화할 수 있으며 경고 세부 정보를 위해 동적 콘텐츠를 구성할 수 있습니다. 경고를 인시던트로 그룹화하는 방법을 선택하고, 결과를 생성한 후 쿼리 실행을 일시적으로 표시하지 않을 수 있으며, 규칙에서 생성된 경고 및 인시던트 대응으로 실행할 자동화 규칙 및 플레이북을 정의할 수 있습니다.

당분간 이러한 템플릿은 아래에 설명된 대로 애플리케이션이 제한되지만 기술은 빠르게 진화하고 성장하고 있습니다.

## <a name="considerations"></a>고려 사항
현재 NRT 규칙의 사용을 제어하는 제한은 다음과 같습니다.

1. 현재 고객당 20개 이하의 규칙을 정의할 수 있습니다.

1. 이 유형의 규칙은 새로운 형식이기 때문에 해당 구문은 현재 제한되지만 점진적으로 진화합니다. 따라서 현재 다음과 같은 제한이 적용됩니다.

    1. NRT 규칙에 정의된 쿼리는 **하나의 테이블만** 참조할 수 있습니다. 그러나 쿼리는 여러 감시 목록 및 위협 인텔리전스 피드를 참조할 수 있습니다.

    1. 공용체 또는 조인은 사용할 수 없습니다.

    1. 이 규칙 유형은 거의 실시간으로 적용되므로 기본 제공 지연 시간을 최소(2분)로 줄입니다.

    1. NRT 규칙은 이벤트 생성 시간(TimeGenerated 필드로 표시)이 아닌 수집 시간을 사용하므로 데이터 원본 지연 및 수집 시간 대기 시간을 무시해도 됩니다(위 참조).

    1. 쿼리는 단일 작업 영역 내에서만 실행할 수 있습니다. 작업 영역 간 기능은 없습니다.

    1. 이벤트 그룹화가 없습니다. NRT 규칙은 적용 가능한 모든 이벤트를 그룹화하는 단일 경고를 생성합니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 Microsoft Sentinel에서 NRT(근 실시간) 분석 규칙이 작동하는 방식을 알아 냈습니다.

- [NRT 규칙을 만드는](create-nrt-rules.md)방법을 알아봅니다.
- [다른 유형의 분석 규칙](detect-threats-built-in.md)에 대해 알아봅니다.
