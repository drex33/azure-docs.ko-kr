---
title: Microsoft 센티널 경고의 Surface 사용자 지정 세부 정보 | Microsoft Docs
description: Microsoft 센티널 분석 규칙의 경고에서 사용자 지정 이벤트 세부 정보를 추출 하 고 노출 하 여 더 나은 전체 인시던트 정보를 확인 합니다.
author: yelevin
ms.topic: how-to
ms.date: 11/09/2021
ms.author: yelevin
ms.custom: ignite-fall-2021
ms.openlocfilehash: d4a6a580ca22ab95c0c3c8faebe7828cbd73fc1a
ms.sourcegitcommit: 1244a72dbec39ac8cf16bb1799d8c46bde749d47
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/18/2021
ms.locfileid: "132762104"
---
# <a name="surface-custom-event-details-in-alerts-in-microsoft-sentinel"></a>Microsoft 센티널의 경고에서 사용자 지정 이벤트 세부 정보 노출 

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

> [!IMPORTANT]
>
> - 사용자 지정 세부 정보 기능은 **PREVIEW** 에 있습니다. 베타 또는 미리 보기로 제공되거나 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 추가 약관은 [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

## <a name="introduction"></a>소개

[예약 된 쿼리 분석 규칙](detect-threats-custom.md) 은 Microsoft 센티널에 연결 된 데이터 원본의 **이벤트** 를 분석 하 고 이러한 이벤트의 내용이 보안 관점에서 중요 한 경우 **경고** 를 생성 합니다. 이러한 경고는 Microsoft 센티널의 다양 한 엔진에 의해 추가 분석, 그룹화 및 필터링 되며 SOC 분석가의 주의가 보장 되는 **인시던트에** distilled 됩니다. 그러나 분석가가 인시던트를 볼 때는 구성 요소 경고 자체의 속성만 즉시 표시됩니다. 실제 콘텐츠, 즉 이벤트에 포함된 정보를 찾으려면 탐색이 필요합니다.

**분석 규칙 마법사** 의 **사용자 지정 세부 정보** 기능을 사용하면 이러한 이벤트로 구성된 경고에 이벤트 데이터를 표시하여 이벤트 데이터를 경고 속성의 일부로 만들 수 있습니다. 실제로 이 기능은 인시던트에 대한 즉각적인 이벤트 콘텐츠 가시성을 제공하여 사용자가 심사하고, 조사하고, 결론을 내리고, 훨씬 더 빠르고 효율적으로 응답할 수 있도록 합니다.

아래에 자세히 설명된 절차는 분석 규칙 만들기 마법사의 일부입니다. 이는 기존 분석 규칙에서 사용자 지정 세부 정보를 추가하거나 변경하게 되는 시나리오를 해결하기 위해 독립적으로 처리됩니다.

## <a name="how-to-surface-custom-event-details"></a>사용자 지정 이벤트 정보를 노출하는 방법

1. Microsoft 센티널 탐색 메뉴에서 **Analytics** 를 선택 합니다.

1. 예약된 쿼리 규칙을 선택하고 **편집** 을 클릭합니다. 또는 화면 맨 위에서 **만들기 > 예약된 쿼리 규칙** 을 클릭하여 새 규칙을 만듭니다.

1. **규칙 논리 설정** 탭을 클릭합니다.

1. **경고 보강(미리 보기)** 섹션에서 **사용자 지정 세부 정보** 를 확장합니다.

    :::image type="content" source="media/surface-custom-details-in-alerts/alert-enrichment.png" alt-text="사용자 지정 세부 정보 찾기 및 선택":::

1. 이제 확장된 **사용자 지정 세부 정보** 섹션에서 표시하려는 세부 정보에 해당하는 키-값 쌍을 추가합니다.

    1. **키** 필드에 경고의 필드 이름으로 표시될 이름을 입력합니다.

    1. **값** 필드의 드롭다운 목록에서 알림에 표시할 이벤트 매개 변수를 선택합니다. 이 목록은 규칙 쿼리의 대상인 테이블의 필드에 해당하는 값으로 채워집니다.
    
        :::image type="content" source="media/surface-custom-details-in-alerts/custom-details.png" alt-text="사용자 지정 세부 정보 추가":::

1. **새 항목 추가** 를 클릭하고 키-값 쌍을 정의하는 마지막 단계를 반복하여 세부 정보를 표시합니다. 

    생각이 바뀌거나 실수를 한 경우 해당 세부 정보의 **값** 드롭다운 목록 옆에 있는 휴지통 아이콘을 클릭하여 사용자 지정 세부 정보를 삭제할 수 있습니다.

1. 사용자 지정 세부 정보 정의를 마쳤으면 **검토 및 만들기** 탭을 클릭합니다. 규칙 유효성 검사에 성공하면 **저장** 을 클릭합니다.

    > [!NOTE]
    > 
    > **서비스 제한**
    > - 단일 분석 규칙에서 **최대 20개의 사용자 지정 세부 정보** 를 정의할 수 있습니다.
    >
    > - 모든 사용자 지정 세부 정보의 크기 제한은 전체적으로 **2KB** 입니다.

## <a name="next-steps"></a>다음 단계
이 문서에서는 Microsoft 센티널 분석 규칙을 사용 하 여 경고에서 사용자 지정 세부 정보를 노출 하는 방법을 배웠습니다. Microsoft 센티널에 대해 자세히 알아보려면 다음 문서를 참조 하세요.
- [예약된 쿼리 분석 규칙](detect-threats-custom.md)을 전체적으로 파악합니다.
- [Microsoft 센티널의 엔터티에](entities.md)대해 자세히 알아보세요.
