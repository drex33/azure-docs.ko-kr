---
title: Microsoft 센티널에서 NRT (거의 실시간) 분석 규칙으로 신속 하 게 위협 감지 | Microsoft Docs
description: 이 문서에서는 새로운 NRT (거의 실시간) 분석 규칙을 통해 Microsoft 센티널에서 위협을 신속 하 게 검색할 수 있는 방법을 설명 합니다.
services: sentinel
cloud: na
documentationcenter: na
author: yelevin
manager: rkarlin
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/09/2021
ms.author: yelevin
ms.custom: ignite-fall-2021
ms.openlocfilehash: 4cafdcc07ec0cfcd5aa3a7731c7a9571441183fd
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2021
ms.locfileid: "132712931"
---
# <a name="detect-threats-quickly-with-near-real-time-nrt-analytics-rules-in-microsoft-sentinel"></a>Microsoft 센티널에서 NRT (거의 실시간) 분석 규칙으로 신속 하 게 위협 감지

> [!IMPORTANT]
>
> - NRT (거의 실시간) 규칙은 현재 **미리 보기로** 제공 됩니다. 베타 또는 미리 보기로 제공되거나 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 추가 약관은 [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

## <a name="what-are-near-real-time-nrt-analytics-rules"></a>NRT (거의 실시간) 분석 규칙 이란?

보안 위협에 직면 하는 경우 시간과 속도는 기본적으로입니다. 이를 신속 하 게 분석 하 고 대응할 수 있도록 구체화 될 때 위협을 인식 해야 합니다. Microsoft 센티널의 거의 실시간 (NRT) 분석 규칙은 온-프레미스 SIEM 및 특정 시나리오에서 응답 시간을 단축 하는 기능에 더 빠르게 위협 감지 기능을 제공 합니다.

Microsoft 센티널의 [거의 실시간 분석 규칙](detect-threats-built-in.md#nrt) 은 기본 제공 되는 최신 위협 검색 기능을 제공 합니다. 이 유형의 규칙은 1 분 간격으로 쿼리를 실행 하 여 응답성이 높은 응답성으로 설계 되었습니다.

## <a name="how-do-they-work"></a>어떻게 작동 합니까?

NRT 규칙은 1 분 마다 한 번씩 실행 되도록 하드 코딩 되 고, 이전 분에는 이벤트를 수집 하 여 최대한 최신 정보를 제공할 수 있도록 합니다.

수집 시간 지연을 고려 하 여 기본 제공 되는 5 분 지연 시간에 실행 되는 일반적인 예약 된 규칙과 달리 NRT 규칙은 2 분 지연 에서만 실행 되며, 원본 (TimeGenerated 필드)에서 생성 시간이 아닌 이벤트 수집 시간을 쿼리하여 수집 지연 문제를 해결 합니다. 이로 인해 검색의 빈도와 정확도가 모두 향상 됩니다. 이 문제를 더 자세히 이해 하려면 [쿼리 일정 및 경고 임계값](detect-threats-custom.md#query-scheduling-and-alert-threshold) 및 [예약 된 분석 규칙의 수집 지연 처리](ingestion-delay.md)를 참조 하세요.

NRT 규칙은 예약 된 분석 규칙과 동일한 많은 기능과 기능을 포함 합니다. 전체 경고 보강 기능 집합을 사용할 수 있습니다. 엔터티 및 surface 사용자 지정 세부 정보를 매핑하고 경고 세부 정보에 대 한 동적 콘텐츠를 구성할 수 있습니다. 경고를 인시던트에 그룹화 하는 방법을 선택 하 고, 결과를 생성 한 후 쿼리 실행을 일시적으로 억제할 수 있으며, 규칙에서 생성 된 경고 및 인시던트에 대 한 응답으로 실행할 자동화 규칙 및 플레이 북를 정의할 수 있습니다.

이 기간 동안 이러한 템플릿은 아래에 설명 된 대로 제한 된 응용 프로그램을 갖지만, 기술은 신속 하 게 진화 하 고 증가 합니다.

## <a name="considerations"></a>고려 사항
현재 다음과 같은 제한 사항이 NRT 규칙의 사용을 제어 합니다.

1. 지금은 고객 당 20 개의 규칙을 정의할 수 없습니다.

1. 이 유형의 규칙은 새로운 규칙 이므로 구문은 현재 제한 되어 있지만 점차적으로 진화 하 고 있습니다. 따라서 지금은 다음과 같은 제한 사항이 적용 됩니다.

    1. NRT 규칙에 정의 된 쿼리는 **하나의 테이블만** 참조할 수 있습니다. 그러나 쿼리는 여러 watchlists 및 위협 인텔리전스 피드를 참조할 수 있습니다.

    1. 공용 구조체 또는 조인을 사용할 수 없습니다.

    1. 이 규칙 유형은 거의 실시간 이므로 기본 제공 지연 시간을 최소 (2 분)로 줄였습니다.

    1. NRT 규칙은 TimeGenerated 필드로 표현 되는 이벤트 생성 시간이 아닌 수집 시간을 사용 하므로 데이터 원본 지연과 수집 시간 대기 시간 (위 참조)을 안전 하 게 무시할 수 있습니다.

    1. 쿼리는 단일 작업 영역 내 에서만 실행할 수 있습니다. 작업 영역 간 기능은 없습니다.

    1. 이벤트 그룹화가 없습니다. NRT 규칙은 모든 해당 이벤트를 그룹화 하는 단일 경고를 생성 합니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 Microsoft 센티널에서 NRT (거의 실시간) 분석 규칙을 사용 하는 방법을 알아보았습니다.

- [NRT 규칙을 만드는](create-nrt-rules.md)방법에 대해 알아봅니다.
- [다른 유형의 분석 규칙](detect-threats-built-in.md)에 대해 알아봅니다.
