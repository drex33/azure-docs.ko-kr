---
title: Microsoft 센티널에서 NRT (거의 실시간) 검색 분석 규칙을 사용 하 여 작업 | Microsoft Docs
description: 이 문서에서는 Microsoft 센티널에서 NRT (거의 실시간) 검색 분석 규칙을 보고 만드는 방법을 설명 합니다.
services: sentinel
cloud: na
documentationcenter: na
author: yelevin
manager: rkarlin
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 11/09/2021
ms.author: yelevin
ms.custom: ignite-fall-2021
ms.openlocfilehash: 33f6417193944a4596e6900af5c68ae52aa4aca4
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2021
ms.locfileid: "132721483"
---
# <a name="work-with-near-real-time-nrt-detection-analytics-rules-in-microsoft-sentinel"></a>Microsoft 센티널에서 NRT (거의 실시간) 검색 분석 규칙을 사용 합니다.

> [!IMPORTANT]
>
> - NRT (거의 실시간) 규칙은 현재 **미리 보기로** 제공 됩니다. 베타 또는 미리 보기로 제공되거나 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 추가 약관은 [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

Microsoft 센티널의 [거의 실시간 분석 규칙](near-real-time-rules.md) 은 기본 제공 되는 최신 위협 검색 기능을 제공 합니다. 이 유형의 규칙은 1 분 간격으로 쿼리를 실행 하 여 응답성이 높은 응답성으로 설계 되었습니다.

이 기간 동안 이러한 템플릿은 아래에 설명 된 대로 제한 된 응용 프로그램을 갖지만, 기술은 신속 하 게 진화 하 고 증가 합니다.

## <a name="view-near-real-time-nrt-rules"></a>NRT (거의 실시간) 규칙 보기

1. Microsoft 센티널 탐색 메뉴에서 **Analytics** 를 선택 합니다.

1. **분석** 블레이드의 **활성 규칙** 탭에서 **nrt** 템플릿 목록을 필터링 합니다.

    1. **규칙 유형** 필터를 클릭한 다음 아래에 표시되는 드롭다운 목록을 클릭합니다.

    1. 표시 해제 **모두 선택** 표시 하 고 **nrt** 를 표시 합니다.

    1. 필요한 경우 드롭다운 목록의 맨 위를 클릭하여 취소한 다음 **확인** 을 클릭합니다.

## <a name="create-nrt-rules"></a>NRT 규칙 만들기

정기적으로 [예약 된 쿼리 분석 규칙](detect-threats-custom.md)을 만드는 것과 동일한 방식으로 nrt 규칙을 만듭니다.

1. Microsoft 센티널 탐색 메뉴에서 **Analytics** 를 선택 합니다.

1. 단추 모음에서 **만들기** 를 선택 하 고 드롭다운 목록에서 **nrt 쿼리 규칙** 을 선택 합니다.

    :::image type="content" source="media/create-nrt-rules/create-nrt-rule.png" alt-text="새 NRT 규칙을 만듭니다.":::

1. [**분석 규칙 마법사**](detect-threats-custom.md)의 지침을 따릅니다.

    NRT 규칙의 구성은 대부분 예약 된 분석 규칙과 동일 합니다. 

    - 쿼리 논리에서 [**watchlists**](watchlists.md) 및 [**위협 인텔리전스 피드**](understand-threat-intelligence.md) 를 참조할 수 있습니다.

    - 모든 경고 보강 메서드: [**엔터티 매핑**](map-data-fields-to-entities.md), [**사용자 지정 세부 정보**](surface-custom-details-in-alerts.md)및 [**경고 정보**](customize-alert-details.md)를 사용할 수 있습니다.

    - 경고를 인시던트에 그룹화 하는 방법을 선택 하 고 특정 결과가 생성 될 때 쿼리를 표시 하지 않을 수 있습니다.

    - 경고와 인시던트에 대 한 응답을 자동화할 수 있습니다.

    그러나 [**NRT 규칙의 특성 및 제한 사항**](near-real-time-rules.md#considerations)때문에 예약 된 분석 규칙의 다음 기능은 마법사에서 *사용할 수 없습니다* .

    - 쿼리 **일정** 은 1 분 lookback 기간으로 분당 한 번 실행 되도록 예약 되므로 구성할 수 없습니다. 
    - 경고가 항상 생성 되기 때문에 **경고 임계값** 은 관련이 없습니다.
    - 이벤트가 항상 이벤트를 캡처하는 규칙에 의해 생성 된 경고로 그룹화 되기 때문에 **이벤트 그룹화** 구성을 사용할 수 없습니다. NRT 규칙은 각 이벤트에 대 한 경고를 생성할 수 없습니다.

    또한 쿼리 자체에는 다음과 같은 요구 사항이 있습니다.

    - 쿼리 자체는 하나의 테이블만 참조할 수 있으며, 공용 구조체 또는 조인을 포함할 수 없습니다.

    - 작업 영역에서 쿼리를 실행할 수 없습니다.

    - 경고의 크기 제한으로 인해 쿼리는 `project` 테이블에서 필요한 필드만 포함 하는 문을 사용 해야 합니다. 그렇지 않으면 화면에 표시할 정보가 잘릴 수 있습니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 Microsoft 센티널에서 NRT (거의 실시간) 분석 규칙을 만드는 방법을 알아보았습니다.

- [Microsoft 센티널의 NRT (거의 실시간) 분석 규칙](near-real-time-rules.md)에 대해 자세히 알아보세요.
- 다른 [분석 규칙 유형](detect-threats-built-in.md)을 탐색합니다.
