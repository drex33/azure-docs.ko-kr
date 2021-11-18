---
title: Microsoft Sentinel 엔터티에 데이터 필드 매핑 | Microsoft Docs
description: 더 나은 인시던트 정보를 위해 테이블의 데이터 필드를 분석 규칙의 Microsoft Sentinel 엔터티에 매핑
author: yelevin
ms.topic: how-to
ms.date: 11/09/2021
ms.author: yelevin
ms.custom: ignite-fall-2021
ms.openlocfilehash: d8b022de05be6434ab6c852ce0263fbb93addb5b
ms.sourcegitcommit: 1244a72dbec39ac8cf16bb1799d8c46bde749d47
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/18/2021
ms.locfileid: "132754258"
---
# <a name="map-data-fields-to-entities-in-microsoft-sentinel"></a>Microsoft Sentinel의 엔터티에 데이터 필드 매핑 

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

> [!IMPORTANT]
>
> - 새 버전의 엔터티 매핑 기능은 **미리 보기** 로 제공되고 있습니다. 베타 또는 미리 보기로 제공되거나 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 추가 약관은 [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

> [!IMPORTANT]
>
> - 이전 버전과의 호환성 및 새 버전과 이전 버전의 엔터티 매핑 간 차이점에 대한 중요한 정보는 이 문서의 끝에 있는 [새 버전에 대한 참고 사항](#notes-on-the-new-version)을 참조하세요.

## <a name="introduction"></a>소개

엔터티 매핑은 [예약된 쿼리 분석 규칙](detect-threats-custom.md) 구성의 중요한 부분입니다. 뒤에 나오는 조사 프로세스 및 수정 작업의 구성 요소 역할을 하는 필수 정보로 규칙의 출력(경고와 인시던트)을 보강합니다.

아래에 자세히 설명된 절차는 분석 규칙 만들기 마법사의 일부입니다. 여기서는 기존 분석 규칙에서 엔터티 매핑을 추가하거나 변경하는 시나리오를 해결하기 위해 독립적으로 처리됩니다.

## <a name="how-to-map-entities"></a>엔터티 매핑 방법

1. Microsoft Sentinel 탐색 메뉴에서 **분석을** 선택합니다.

1. 예약된 쿼리 규칙을 선택하고 **편집** 을 클릭합니다. 또는 화면 맨 위에서 **만들기 > 예약된 쿼리 규칙** 을 클릭하여 새 규칙을 만듭니다.

1. **규칙 논리 설정** 탭을 클릭합니다. 

1. **경고 보강(미리 보기)** 섹션에서 **엔터티 매핑** 을 펼칩니다.

    :::image type="content" source="media/map-data-fields-to-entities/alert-enrichment.png" alt-text="엔터티 매핑 펼치기":::

1. 이제 펼쳐진 **엔터티 매핑** 섹션의 **엔터티 형식** 드롭다운 목록에서 엔터티 형식을 선택합니다.

    :::image type="content" source="media/map-data-fields-to-entities/choose-entity-type.png" alt-text="엔터티 형식 선택":::

1. 엔터티의 **식별자** 를 선택합니다. 식별자는 엔터티를 충분히 식별할 수 있는 엔터티 특성입니다. **식별자** 드롭다운 목록에서 식별자를 선택한 다음, **값** 드롭다운 목록에서 식별자에 해당하는 데이터 필드를 선택합니다. 몇 가지 예외를 제외하고 **값** 목록은 규칙 쿼리의 주체로 정의된 테이블의 데이터 필드로 채워집니다.

    지정된 엔터티의 **식별자를 최대 3개까지** 정의할 수 있습니다. 일부 식별자는 필수이고 나머지는 선택 사항입니다. 필수 식별자를 하나 이상 선택해야 합니다. 그러지 않으면 필수 식별자를 알려주는 경고 메시지가 표시됩니다. 최상의 결과인 최대 고유 식별을 위해 가능하면 항상 **강력한 식별자** 를 사용해야 하며, 여러 개의 강력한 식별자를 사용하면 데이터 원본 간 상관 관계가 강화됩니다. 사용 가능한 [엔터티 및 식별자](entities-reference.md)의 전체 목록을 참조하세요.

    :::image type="content" source="media/map-data-fields-to-entities/map-entities.png" alt-text="필드를 엔터티에 매핑":::

1. **새 엔터티 추가** 를 클릭하여 더 많은 엔터티를 매핑합니다. 단일 분석 규칙에서 **최대 5개의 엔터티** 를 매핑할 수 있습니다. 동일한 형식의 엔터티를 둘 이상 매핑할 수도 있습니다. 예를 들어 ‘원본 IP 주소’ 필드와 ‘대상 IP 주소’ 필드에서 하나씩, 두 개의 **IP** 엔터티를 매핑할 수 있습니다.  이렇게 하면 두 엔터티를 모두 추적할 수 있습니다.

    마음이 바뀌거나 실수한 경우 엔터티 드롭다운 목록 옆에 있는 휴지통 아이콘을 클릭하여 엔터티 매핑을 제거할 수 있습니다.

1. 엔터티 매핑을 마쳤으면 **검토 및 만들기** 탭을 클릭합니다. 규칙 유효성 검사에 성공하면 **저장** 을 클릭합니다.

## <a name="notes-on-the-new-version"></a>새 버전에 대한 참고 사항

- 이전 버전을 사용하여 이 분석 규칙의 엔터티 매핑을 이전에 정의한 경우 해당 매핑이 쿼리 코드에 표시됩니다. 새 버전으로 정의된 엔터티 매핑은 **쿼리 코드에 표시되지 않습니다**. 분석 규칙은 한 번에 하나의 엔터티 매핑 버전만 지원할 수 있으며 새 버전이 우선 적용됩니다. 따라서 여기서 정의하는 단일 매핑으로 인해 쿼리 코드에 정의된 **모든** 매핑은 쿼리 실행 시 **무시** 됩니다. 

- 새 버전이 미리 보기로 제공되는 한, **이전 버전** 의 엔터티 매핑을 계속 사용해야 하는 경우에도 URL의 기능 플래그를 사용하여 엔터티 매핑에 액세스할 수 있습니다. `https://portal.azure.com/`과 `#blade` 사이에 커서를 놓고 `?feature.EntityMapping=false` 텍스트를 삽입합니다.

  - 이전 버전의 한도는 계속 적용됩니다. 사용자, 호스트, IP 주소, URL, 파일 해시 엔터티만 매핑할 수 있으며 각각 하나만 매핑할 수 있습니다.

  - 이전 버전으로 되돌리기 **전에** 새 버전을 사용하여 만든 엔터티 매핑을 **제거** 해야 합니다. 그러지 않으면 이전 버전을 사용하는 엔터티 매핑은 **작동하지 않습니다**.

- 새 버전의 엔터티 매핑이 일반 공급된 후에는 이전 버전을 더 이상 사용할 수 없습니다. 이전 엔터티 매핑을 새 버전으로 마이그레이션하는 것이 좋습니다.


## <a name="next-steps"></a>다음 단계

이 문서에서는 Microsoft Sentinel 분석 규칙의 엔터티에 데이터 필드를 매핑하는 방법을 배웠습니다. Microsoft Sentinel에 대한 자세한 내용은 다음 문서를 참조하세요.
- [예약된 쿼리 분석 규칙](detect-threats-custom.md)을 전체적으로 파악합니다.
- [Microsoft Sentinel의 엔터티에](entities.md)대해 자세히 알아봅니다.
