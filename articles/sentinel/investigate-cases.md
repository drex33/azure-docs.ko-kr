---
title: Microsoft Sentinel을 사용하여 인시던트 조사| Microsoft Docs
description: 이 문서에서는 Microsoft Sentinel을 사용하여 할당하고 조사할 수 있는 인시던트 생성 고급 경고 규칙을 만드는 방법을 알아봅니다.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: microsoft-sentinel
ms.subservice: microsoft-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/09/2021
ms.author: yelevin
ms.custom: ignite-fall-2021
ms.openlocfilehash: 77cf1b6dd4038613af9d079fc6226ab8c784577a
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132520145"
---
# <a name="investigate-incidents-with-microsoft-sentinel"></a>Microsoft Sentinel을 사용하여 인시던트 조사

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

> [!IMPORTANT]
> 언급된 기능은 현재 미리 보기로 제공됩니다. [Azure Preview 추가 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)에는 베타, 미리 보기 또는 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 추가 법률 용어가 포함되어 있습니다.
>

이 문서는 Microsoft Sentinel을 사용하여 인시던트 조사를 도와줍니다. 데이터 원본을 Microsoft Sentinel에 연결한 후에는 의심스러운 일이 발생할 때 알림을 받기를 원합니다. 이 작업을 수행할 수 있도록 Microsoft Sentinel을 사용하면 할당하고 조사할 수 있는 인시던트 생성 고급 경고 규칙을 만들 수 있습니다.

이 문서에서는 다음 내용을 설명합니다.
> [!div class="checklist"]
> * 인시던트 조사
> * 조사 그래프 사용
> * 위협에 대응

인시던트는 여러 경고를 포함할 수 있습니다. 이는 특정 조사에 대한 모든 관련 증거의 집계입니다. **분석** 페이지에서 만든 분석 규칙에 따라 인시던트가 생성됩니다. 심각도 및 상태와 같은 경고와 관련된 속성은 인시던트 수준에서 설정됩니다. Microsoft Sentinel에 찾고 있는 위협의 종류와 이를 찾는 방법을 알려면 인시던트 조사로 감지된 위협을 모니터링할 수 있습니다.

## <a name="prerequisites"></a>필수 구성 요소

- 분석 규칙을 설정할 때 엔터티 매핑 필드를 사용한 경우에만 인시던트를 조사할 수 있습니다. 조사 그래프에서 원본 인시던트에 엔터티를 포함해야 합니다.

- 인시던트를 할당해야 하는 게스트 사용자가 있는 경우 해당 사용자에게 Azure AD 테넌트에서 [ 디렉터리 읽기 권한자](../active-directory/roles/permissions-reference.md#directory-readers) 역할이 할당되어야 합니다. 일반(게스트가 아닌) 사용자에게는 기본적으로 이 역할이 할당됩니다.

## <a name="how-to-investigate-incidents"></a>인시던트를 조사하는 방법

1. **인시던트** 를 선택합니다. **인시던트** 페이지에서는 보유한 인시던트 수, 시작된 인시던트 수, **진행 중** 으로 설정한 인시던트 수 및 종료된 인시던트 수를 알 수 있습니다. 각 인시던트에 대해 발생한 시간 및 인시던트의 상태를 확인할 수 있습니다. 심각도를 확인하여 먼저 처리할 인시던트를 결정합니다.

    ![인시던트 심각도 보기](media/tutorial-investigate-cases/incident-severity.png)

1. 상태 또는 심각도와 같이 필요에 따라 인시던트를 필터링할 수 있습니다. 자세한 내용은 [인시던트 검색](#search-for-incidents)을 참조하세요.

1. 조사를 시작하려면 특정 인시던트를 선택합니다. 오른쪽에서 심각도, 관련 엔터티 수 요약, 이 인시던트를 트리거한 원시 이벤트 및 인시던트의 고유 ID를 포함하여 인시던트에 대한 자세한 정보를 볼 수 있습니다.

1. 인시던트의 경고 및 엔터티에 대한 자세한 내용을 보려면 인시던트 페이지에서 **전체 세부 정보 보기** 를 선택하고 인시던트 정보를 요약하는 관련 탭을 검토합니다. 

    ![경고 세부 정보 보기](media/tutorial-investigate-cases/incident-timeline.png)

    예를 들면 다음과 같습니다.

    - **타임라인** 탭에서 공격자 활동의 타임라인 막대를 다시 생성하는 데 도움이 될 수 있는 인시던트의 경고 및 책갈피의 타임라인을 검토합니다.
    - **경고** 탭에서 경고 자체를 검토합니다. 경고를 트리거한 쿼리, 쿼리당 반환된 결과 수, 경고에 대한 플레이북을 실행하는 기능 등 경고에 대한 모든 관련 정보를 확인할 수 있습니다. 인시던트를 더 자세히 드릴다운하려면 **이벤트** 수를 선택합니다. 그러면 결과를 생성한 쿼리와 Log Analytics 경고를 트리거한 이벤트가 열립니다. 
    - **엔터티** 탭에서 경고 규칙 정의의 일부로 매핑한 모든 엔터티를 확인할 수 있습니다.

1. 인시던트를 적극적으로 조사하는 경우에는 종료할 때까지 인시던트의 상태를 **진행 중** 으로 설정하는 것이 좋습니다.

1. 인시던트를 특정 사용자에게 할당할 수 있습니다. 각 인시던트에 대해 **인시던트 소유자** 필드를 설정하여 소유자를 할당할 수 있습니다. 모든 인시던트는 할당되지 않은 상태로 시작합니다. 조사한 내용과 인시던트에 대한 우려 사항을 다른 분석가가 이해할 수 있도록 설명을 추가할 수도 있습니다.

    ![사용자에게 인시던트 할당](media/tutorial-investigate-cases/assign-incident-to-user.png)

1. 조사 맵을 보려면 **조사** 를 선택합니다.



## <a name="use-the-investigation-graph-to-deep-dive"></a>조사 그래프를 사용하여 심층 분석

조사 그래프를 통해 분석가는 각 조사에 대해 적절한 질문을 할 수 있습니다. 조사 그래프는 관련 데이터와 관련 엔터티를 상호 연결하여 범위를 파악하고 잠재적 보안 위협의 근본 원인을 식별하는 데 도움이 됩니다. 그래프에 표시된 모든 엔터티를 선택하고 다른 확장 옵션 중에서 선택하여 더 자세히 살펴보고 조사할 수 있습니다.  
  
조사 그래프는 다음을 제공합니다.

- **원시 데이터의 시각적 컨텍스트**: 라이브 시각적 그래프는 원시 데이터에서 자동으로 추출된 엔터티 관계를 표시합니다. 이를 통해 다양한 데이터 원본 간의 연결을 쉽게 확인할 수 있습니다.

- **전체 조사 범위 검색**: 기본 제공 탐색 쿼리를 사용하여 조사 범위를 확장하여 위반 사항의 전체 범위를 노출합니다.

- **기본 제공 조사 단계**: 미리 정의된 탐색 옵션을 사용하여 위협에 대해 적절한 질문을 하고 있는지 확인합니다.

조사 그래프를 사용하려면 다음을 수행합니다.

1. 인시던트를 선택하고 **조사** 를 선택합니다. 그러면 조사 그래프로 이동합니다. 그래프는 경고에 직접 연결된 엔터티 및 추가로 연결된 각 리소스의 설명 맵을 제공합니다.


    [ ![지도 보기.](media/tutorial-investigate-cases/investigation-map.png) ](media/tutorial-investigate-cases/investigation-map.png#lightbox)

   > [!IMPORTANT] 
   > - 분석 규칙을 설정할 때 엔터티 매핑 필드를 사용한 경우에만 인시던트를 조사할 수 있습니다. 조사 그래프에서 원본 인시던트에 엔터티를 포함해야 합니다.
   >
   > - Microsoft Sentinel은 현재 **최대 30일이 지난 인시던트 조사를 지원합니다.**


1. 엔터티를 선택하면 해당 엔터티에 대한 정보를 검토할 수 있는 **엔터티** 창이 열립니다.

    ![맵에서 엔터티 보기](media/tutorial-investigate-cases/map-entities.png)
  
1. 각 엔터티를 가리켜 조사를 확장하면 조사를 심화하기 위해 엔터티 유형별 보안 전문가 및 분석가가 디자인한 질문 목록이 표시됩니다. 이러한 옵션을 **탐색 쿼리** 라고 합니다.

    ![자세히 내용 탐색하기](media/tutorial-investigate-cases/exploration-cases.png)

   예를 들어 컴퓨터에서 관련 경고를 요청할 수 있습니다. 탐색 쿼리를 선택하면 결과 엔터티가 그래프에 다시 추가됩니다. 이 예에서는 **관련 경고** 를 선택하여 그래프에 다음 경고를 반환했습니다.

    ![관련 경고 보기](media/tutorial-investigate-cases/related-alerts.png)

1. 각 탐색 쿼리에 대해 **이벤트\>** 를 선택하여 원시 이벤트 결과 및 Log Analytics에서 사용되는 쿼리를 여는 옵션을 선택할 수 있습니다.

1. 인시던트를 이해하기 위해 그래프는 병렬 타임라인을 제공합니다.

    ![맵에서 타임라인 보기](media/tutorial-investigate-cases/map-timeline.png)

1. 타임라인을 가리켜 그래프에서 어떤 시점에 어떤 항목이 발생했는지 확인합니다.

    ![맵에서 타임라인을 사용하여 경고 조사](media/tutorial-investigate-cases/use-timeline.png)


## <a name="closing-an-incident"></a>인시던트 종결

특정 인시던트를 해결한 후(예: 조사가 결론에 도달했을 때) 인시던트의 상태를 **종결됨** 으로 설정해야 합니다. 이렇게 하면 종결 이유를 지정하여 인시던트를 분류하라는 메시지가 표시됩니다. 이 단계는 필수입니다. **분류 선택** 을 클릭하고 드롭다운 목록에서 다음 중 하나를 선택합니다.

- 진양성 - 의심스러운 활동
- 무해한 양성 - 의심스럽지만 예상됨
- 가양성 - 잘못된 경고 논리
- 가양성 - 잘못된 데이터
- 결정되지 않음

:::image type="content" source="media/tutorial-investigate-cases/closing-reasons-dropdown.png" alt-text="분류 선택 목록에서 사용할 수 있는 분류를 강조 표시하는 스크린샷.":::

가양성 및 무해한 긍정에 대한 자세한 내용은 [Microsoft Sentinel에서 가양성 처리](false-positives.md)를 참조하세요.

적절한 분류를 선택한 후 **설명** 필드에 설명 텍스트를 추가합니다. 이 문제는 이 인시던트를 다시 참조해야 하는 경우에 유용합니다. 완료되면 **적용** 을 클릭합니다. 그러면 인시던트가 종결됩니다.

:::image type="content" source="media/tutorial-investigate-cases/closing-reasons-comment-apply.png" alt-text="{대체 텍스트}":::

## <a name="search-for-incidents"></a>인시던트 검색

특정 인시던트를 신속하게 찾으려면 인시던트 그리드 위의 검색 상자에 검색 문자열을 입력하고 **Enter** 키를 눌러 적절하게 표시되는 인시던트 목록을 수정합니다. 인시던트가 결과에 포함되지 않은 경우 **고급 검색** 옵션을 사용하여 검색 범위를 좁힐 수 있습니다.

검색 매개 변수를 수정하려면 **검색** 단추를 선택한 다음, 검색을 실행하려는 매개 변수를 선택합니다.

예를 들면 다음과 같습니다.

:::image type="content" source="media/tutorial-investigate-cases/advanced-search.png" alt-text="기본 및/또는 고급 검색 옵션을 선택하는 인시던트 검색 상자 및 단추의 스크린샷.":::

기본적으로 인시던트 검색은 **인시던트 ID**, **제목**, **태그**, **소유자** 및 **제품 이름** 값에서만 실행됩니다. 검색 창에서 목록을 아래로 스크롤하여 검색할 다른 매개 변수를 하나 이상 선택하고 **적용** 을 선택하여 검색 매개 변수를 업데이트합니다. **기본값으로 설정** 을 선택하면 선택된 매개 변수를 기본 옵션으로 다시 설정합니다.


> [!NOTE]
> **소유자** 필드 검색은 이름 및 이메일 주소를 모두 지원합니다.
>

고급 검색 옵션을 사용하면 검색 동작이 다음과 같이 변경됩니다.

|검색 동작  |설명  |
|---------|---------|
|**검색 단추 색**     |검색 단추의 색은 현재 검색에 사용되는 매개 변수의 형식에 따라 변경됩니다. <br><br>- 기본 매개 변수만 선택하면 단추가 회색으로 표시됩니다. <br>- 고급 검색 매개 변수와 같은 다른 매개 변수를 선택하는 즉시 단추가 파란색으로 바뀝니다.         |
|**자동 새로 고침**     | 고급 검색 매개 변수를 사용하면 결과를 자동으로 새로 고치도록 선택할 수 없게 됩니다.        |
|**엔터티 매개 변수**     |모든 엔터티 매개 변수는 고급 검색에 대해 지원됩니다. 엔터티 매개 변수에서 검색할 때 검색은 모든 엔터티 매개 변수에서 실행됩니다.         |
|**검색 문자열**     |    단어 문자열을 검색하면 검색 쿼리에 모든 단어가 포함됩니다. 검색 문자열은 대/소문자를 구분합니다.     |
|**작업 영역 간 지원**     |    작업 영역 간 보기에는 고급 검색이 지원되지 않습니다.     |
| **표시된 검색 결과 수** | 고급 검색 매개 변수를 사용하는 경우 한 번에 50개의 결과만 표시됩니다. |
|     |         |

> [!NOTE]
> 고급 검색은 현재 공개 미리 보기로 제공됩니다.
>

> [!TIP]
>  찾고 있는 인시던트를 찾을 수 없는 경우 검색 매개 변수를 제거하여 검색을 확장합니다. 검색 결과 항목이 너무 많으면 필터를 더 추가하여 결과의 범위를 좁힐 수 있습니다.
>


## <a name="next-steps"></a>다음 단계
이 문서에서는 Microsoft Sentinel을 사용하여 인시던트 조사를 시작하는 방법을 배웠습니다. 자세한 내용은 다음을 참조하세요.

- [자습서: Microsoft Sentinel에서 자동화 규칙으로 플레이북 사용](tutorial-respond-threats-playbook.md)
- [UEBA 데이터를 사용하여 인시던트 조사](investigate-with-ueba.md)
