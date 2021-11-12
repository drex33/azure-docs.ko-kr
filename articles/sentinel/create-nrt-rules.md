---
title: Microsoft Sentinel | NRT(근 실시간) 검색 분석 규칙 사용 Microsoft Docs
description: 이 문서에서는 Microsoft Sentinel에서 NRT(근 실시간) 검색 분석 규칙을 보고 만드는 방법을 설명합니다.
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
ms.topic: how-to
ms.date: 11/09/2021
ms.author: yelevin
ms.custom: ignite-fall-2021
ms.openlocfilehash: 9e807363a1a1cc058d6b54c009f9ff90d6a5aa93
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132277888"
---
# <a name="work-with-near-real-time-nrt-detection-analytics-rules-in-microsoft-sentinel"></a>Microsoft Sentinel에서 거의 실시간(NRT) 검색 분석 규칙 작업

> [!IMPORTANT]
>
> - NRT(근 실시간) 규칙은 현재 미리 **보기** 에 있습니다. 베타 또는 미리 보기로 제공되거나 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 추가 약관은 [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

Microsoft Sentinel의 [근 실시간 분석 규칙은](near-real-time-rules.md) 가장 가까운 시간 위협 탐지 기능을 제공합니다. 이 유형의 규칙은 단 1분 간격으로 쿼리를 실행하여 응답성이 뛰어나도록 설계되었습니다.

당분간 이러한 템플릿은 아래에 설명된 대로 애플리케이션이 제한되어 있지만 기술이 빠르게 진화하고 성장하고 있습니다.

## <a name="view-near-real-time-nrt-rules"></a>NRT(근 실시간) 규칙 보기

1. Microsoft Sentinel 탐색 메뉴에서 **분석을** 선택합니다.

1. **분석** 블레이드의 **활성 규칙** 탭에서 **NRT** 템플릿 목록을 필터링합니다.

    1. **규칙 유형** 필터를 클릭한 다음 아래에 표시되는 드롭다운 목록을 클릭합니다.

    1. Select all **표시를 해제한** 다음 **NRT** 를 표시합니다.

    1. 필요한 경우 드롭다운 목록의 맨 위를 클릭하여 취소한 다음 **확인** 을 클릭합니다.

## <a name="create-nrt-rules"></a>NRT 규칙 만들기

정기적인 예약 쿼리 분석 규칙을 만드는 것과 동일한 방식으로 NRT [규칙을 만듭니다.](detect-threats-custom.md)

1. Microsoft Sentinel 탐색 메뉴에서 **분석을** 선택합니다.

1. 단추 모음에서 **만들기를** 선택한 다음, 드롭다운 목록에서 **NRT 쿼리 규칙을** 선택합니다.

    :::image type="content" source="media/create-nrt-rules/create-nrt-rule.png" alt-text="새 NRT 규칙을 만듭니다.":::

1. [**분석 규칙 마법사**](detect-threats-custom.md)의 지침을 따릅니다.

    NRT 규칙의 구성은 대부분의 경우 예약된 분석 규칙과 동일합니다. 

    - 쿼리 논리에서 [**감시 목록 및**](watchlists.md) 위협 인텔리전스 [**피드를**](understand-threat-intelligence.md) 참조할 수 있습니다.

    - [**엔터티 매핑,**](map-data-fields-to-entities.md) [**사용자 지정 세부 정보**](surface-custom-details-in-alerts.md)및 경고 세부 정보 와 같은 모든 [**경고**](customize-alert-details.md)보강 방법을 사용할 수 있습니다.

    - 경고를 인시던트로 그룹화하고 특정 결과가 생성될 때 쿼리를 표시하지 않을 수 있습니다.

    - 경고와 인시던트 모두에 대한 응답을 자동화할 수 있습니다.

    그러나 [**NRT 규칙 의 특성 및 제한 사항으로**](near-real-time-rules.md#considerations)인해 예약된 분석 규칙의 다음 기능은 마법사에서 *사용할 수 없습니다.*

    - **쿼리는** 1분 조회 기간으로 분당 한 번 실행하도록 자동으로 예약되므로 쿼리 예약은 구성할 수 없습니다. 
    - **경고 임계값은** 항상 생성되므로 관련이 없습니다.
    - 이벤트는 항상 **이벤트를 캡처하는** 규칙에서 만든 경고로 그룹화되므로 이벤트 그룹화 구성을 사용할 수 없습니다. NRT 규칙은 각 이벤트에 대한 경고를 생성할 수 없습니다.

    또한 쿼리 자체에는 다음과 같은 요구 사항이 있습니다.

    - 쿼리 자체는 하나의 테이블만 참조할 수 있으며 공용 구성 또는 조인을 포함할 수 없습니다.

    - 작업 영역에서는 쿼리를 실행할 수 없습니다.

    - 경고의 크기 제한으로 인해 쿼리는 문을 사용하여 `project` 테이블에서 필요한 필드만 포함해야 합니다. 그렇지 않으면 표면화하려는 정보가 잘릴 수 있습니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 Microsoft Sentinel에서 NRT(근 실시간) 분석 규칙을 만드는 방법을 배웠습니다.

- [Microsoft Sentinel의 NRT(근 실시간) 분석 규칙에](near-real-time-rules.md)대해 자세히 알아보세요.
- 다른 [분석 규칙 유형](detect-threats-built-in.md)을 탐색합니다.
