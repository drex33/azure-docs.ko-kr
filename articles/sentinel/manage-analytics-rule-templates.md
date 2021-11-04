---
title: Azure 센티널에서 예약 된 분석 규칙에 대 한 템플릿 버전 관리
description: 예약 된 분석 규칙 템플릿과 해당 템플릿에서 만든 규칙 간의 관계를 관리 하는 방법에 대해 알아봅니다. 템플릿에 대 한 업데이트를 규칙에 병합 하 고 규칙의 변경 내용을 원래 템플릿으로 다시 되돌립니다.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/03/2021
ms.author: yelevin
ms.custom: ignite-fall-2021
ms.openlocfilehash: 27e6a78ec66b3427aa911abba5d7f4cdda20d876
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131023085"
---
# <a name="manage-template-versions-for-your-scheduled-analytics-rules-in-azure-sentinel"></a>Azure 센티널에서 예약 된 분석 규칙에 대 한 템플릿 버전 관리

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

> [!IMPORTANT]
>
> 이 기능은 **미리 보기** 상태입니다. 베타 또는 미리 보기로 제공되거나 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 추가 약관은 [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

## <a name="introduction"></a>소개

Azure 센티널은 사용자의 복사본을 효과적으로 만들어 활성 규칙으로 전환 하는 [분석 규칙 템플릿과](detect-threats-built-in.md) 함께 제공 됩니다 .이는 템플릿에서 규칙을 만들 때 발생 합니다. 그러나이 시점에서 활성 규칙은 더 이상 템플릿에 연결 되지 않습니다. Microsoft 엔지니어 또는 다른 모든 사용자가 규칙 템플릿을 변경 하는 경우 해당 템플릿에서 만든 모든 규칙이 새 템플릿과 일치 하도록 동적으로 업데이트 ***되지 않습니다*** .

그러나 템플릿에서 생성 된 규칙은 제공 된 ***템플릿을 명심 하*** 여 다음과 같은 두 가지 이점을 누릴 수 있습니다.

- 템플릿에서 만들 때 (또는 그 후 언제 든 지) 규칙을 변경한 경우 항상 규칙을 다시 원래 버전 (템플릿의 복사본)으로 되돌릴 수 있습니다.

- 템플릿이 업데이트 되 면 알림 메시지를 받을 수 있으며, 규칙을 새 버전의 템플릿으로 업데이트 하거나 그대로 둘 수 있습니다.

이 문서에서는 이러한 작업을 관리 하는 방법과 기억해 야 할 사항에 대해 설명 합니다. 아래에서 설명 하는 절차는 템플릿에서 만든 모든 **[예약 된](detect-threats-built-in.md#scheduled)** 분석 규칙에 적용 됩니다.

## <a name="discover-your-rules-template-version-number"></a>규칙의 템플릿 버전 번호를 검색 합니다.

템플릿 버전 제어를 구현 하 여 규칙 템플릿의 버전 및 규칙 템플릿에서 만든 규칙을 확인 하 고 추적할 수 있습니다. 해당 템플릿이 업데이트 된 규칙은 규칙 이름 옆에 "*업데이트 사용 가능*" 배지를 표시 합니다.

1. **분석** 블레이드에서 **활성 규칙** 탭을 선택 합니다.

1. **예약 된** 유형의 규칙을 선택 합니다.  

    - 규칙이 "*업데이트 사용 가능*" 배지를 표시 하는 경우 세부 정보 창에는 **편집** 단추 옆에 있는 **검토 및 업데이트** 단추가 표시 됩니다 (아래의 다음 단계에 나오는 이미지 1 참조).

    - 템플릿이 템플릿에서 만들어졌지만 "*업데이트 사용 가능*" 배지를 포함 하지 않는 경우 세부 정보 창에는 **편집** 단추 옆의 **템플릿 비교** 단추가 표시 됩니다 (아래의 다음 단계에서는 이미지 2 및 3 참조).

    - **편집** 단추만 있으면 템플릿이 아닌 처음부터 규칙이 생성 된 것입니다.

        :::image type="content" source="media/manage-analytics-rule-templates/see-rules-with-updated-template.png" alt-text="템플릿 업데이트를 사용할 수 있음을 나타내는 배지를 포함 하는 활성 규칙 목록 스크린샷" lightbox="media/manage-analytics-rule-templates/see-rules-with-updated-template.png":::

1. 세부 정보 창의 맨 아래로 스크롤합니다. 여기에는 규칙을 만든 템플릿의 버전 및 사용 가능한 최신 버전의 템플릿이 표시 됩니다. 

    :::image type="content" source="media/manage-analytics-rule-templates/see-template-versions.png" alt-text="세부 정보 창의 스크린샷 아래로 스크롤하여 템플릿 버전 번호를 확인 합니다." border="false":::

    숫자는 "1.0.0" 형식 (주 버전, 부 버전, 빌드)입니다.  
    (에 대 한 빌드 번호는 사용 하지 않으며 항상 0이 됩니다.)

    - *주 버전* 번호의 차이점은 템플릿에 필요한 항목이 변경 되었음을 나타냅니다 .이는 규칙에서 위협을 검색 하는 방법 또는 완전히 기능 하는 방법에 영향을 줄 수 있습니다. 규칙에 포함 하려는 변경 내용입니다.

    - *부 버전* 번호의 차이점은 템플릿의 사소한 향상을 의미 합니다. 즉, 외관상의 변화가 나 이와 유사한 것을 의미 합니다 .이는 "적합 한" 이지만 규칙의 기능, 효율성 또는 성능을 유지 관리 하는 데 중요 하지 않습니다. 이는 쉽게 수행 하거나 탈퇴할 수 있는 변경 내용입니다.

    > [!NOTE]
    > 위의 이미지 2와 3은 템플릿에서 생성 된 규칙의 두 가지 예를 보여 줍니다. 템플릿은 업데이트 되지 않습니다.
    > - 이미지 2에는 현재 템플릿에 대 한 버전 번호가 있는 규칙이 표시 됩니다. 이는 10 월 2021에 Azure 센티널에서 템플릿 버전 제어의 초기 구현 후에 규칙이 생성 되었음을 나타냅니다.
    > - 이미지 3에는 현재 템플릿 버전이 없는 규칙이 표시 됩니다. 이는 규칙이 2021 년 10 월에 생성 된 것을 보여 줍니다. 사용할 수 있는 최신 템플릿 버전이 있는 경우 규칙을 만드는 데 사용한 템플릿의 최신 버전이 될 수 있습니다.

## <a name="compare-your-active-rule-with-its-template"></a>활성 규칙과 해당 템플릿을 비교 합니다.

수행 하려는 작업에 따라 다음 탭 중 하나를 선택 하 여 해당 작업에 대 한 지침을 확인 합니다.

# <a name="update-template"></a>[템플릿 업데이트](#tab/update)

규칙을 선택 하 고 업데이트를 고려 하려는 경우 세부 정보 창에서 **검토 및 업데이트** 를 선택 합니다 (위 참조). 이제 **분석 규칙 마법사** 에 **최신 버전으로 비교** 탭이 표시 됩니다.

이 탭에서 기존 규칙의 YAML 표현과 최신 버전의 템플릿을 나란히 비교할 수 있습니다. 

:::image type="content" source="media/manage-analytics-rule-templates/compare-template-versions.png" alt-text="분석 규칙 마법사의 ' 최신 버전으로 비교 ' 탭의 스크린샷":::

> [!NOTE]
> 이 규칙을 업데이트 하면 기존 규칙이 템플릿의 최신 버전으로 덮어쓰여집니다.
참조 된 이름이 변경 된 경우 기존 규칙에 대 한 참조를 수행 하는 모든 자동화 단계 또는 논리를 확인 해야 합니다. 또한 원래 규칙을 만들기 위해 쿼리, 일정, 그룹화 또는 기타 설정에 대 한 변경 내용을 덮어쓸 수 있습니다.

### <a name="update-your-rule-with-the-new-template-version"></a>새 템플릿 버전으로 규칙 업데이트

- 새 버전의 템플릿에 대 한 변경 내용을 허용할 수 있으며 원래 규칙에 영향을 주지 않은 다른 항목이 없으면 **검토 및 업데이트** 를 선택 하 여 변경 내용을 확인 하 고 적용 합니다. 

- 규칙을 추가로 사용자 지정 하거나 덮어쓸 수 있는 변경 내용을 다시 적용 하려면 **다음: 사용자 지정 변경 내용** 을 선택 합니다. 이를 선택 하는 경우 [분석 규칙 마법사](detect-threats-custom.md) 의 나머지 탭을 순환 하 여 변경 내용을 적용 한 후 **검토 및 업데이트** 탭에서 변경 내용을 확인 하 고 적용 합니다.

- 기존 규칙을 변경 하지 않고 기존 템플릿 버전을 유지 하려는 경우 오른쪽 위 모서리에 있는 X를 선택 하 여 마법사를 종료 하면 됩니다.

# <a name="revert-to-template"></a>[템플릿으로 되돌리기](#tab/revert)

규칙을 선택 하 고 원래 버전으로 되돌리려는를 결정 한 후 세부 정보 창에서 **템플릿과 비교** 를 선택 합니다 (위 참조). 이제 **분석 규칙 마법사** 에 **최신 버전으로 비교** 탭이 표시 됩니다.

이 탭에서 기존 규칙의 YAML 표현과 최신 버전의 템플릿을 나란히 비교할 수 있습니다. 이러한 두 버전 번호는 동일할 수 있지만 왼쪽에는 템플릿에서 생성 된 후 또는 이후에 변경 된 내용이 포함 된 활성 규칙이 표시 되 고 오른쪽에는 변경 되지 않은 템플릿이 표시 됩니다.

:::image type="content" source="media/manage-analytics-rule-templates/compare-template-versions-2.png" alt-text="분석 규칙 마법사의 ' 최신 버전으로 비교 ' 탭의 스크린샷":::

> [!NOTE]
> 이 규칙을 업데이트 하면 기존 규칙이 템플릿의 최신 버전으로 덮어쓰여집니다.
참조 된 이름이 변경 된 경우 기존 규칙에 대 한 참조를 수행 하는 모든 자동화 단계 또는 논리를 확인 해야 합니다. 또한 원래 규칙을 만들기 위해 쿼리, 일정, 그룹화 또는 기타 설정에 대 한 변경 내용을 덮어쓸 수 있습니다.

### <a name="revert-your-rule-to-its-original-template-version"></a>규칙을 원래 템플릿 버전으로 되돌리기

- 이 규칙의 원래 버전으로 완전히 되돌리려면 (템플릿의 정리 복사본) **검토 및 업데이트** 를 선택 하 여 변경 내용을 확인 하 고 적용 합니다. 

- 규칙을 다르게 사용자 지정 하거나 덮어쓸 수 있는 변경 내용을 다시 적용 하려면 **다음: 사용자 지정 변경 내용** 을 선택 합니다. 이를 선택 하는 경우 [분석 규칙 마법사](detect-threats-custom.md) 의 나머지 탭을 순환 하 여 변경 내용을 적용 한 후 **검토 및 업데이트** 탭에서 변경 내용을 확인 하 고 적용 합니다.

- 기존 규칙을 변경 하지 않으려는 경우 오른쪽 위 모서리에 있는 X를 선택 하 여 마법사를 종료 하면 됩니다.

---

## <a name="next-steps"></a>다음 단계
이 문서에서는 Azure 센티널 analytics 규칙 템플릿의 버전을 추적 하 고 활성 규칙을 기존 템플릿 버전으로 되돌리거나 새 규칙으로 업데이트 하는 방법을 알아보았습니다. Azure Sentinel에 대한 자세한 내용은 다음 문서를 참조하세요.

- [분석 규칙](detect-threats-built-in.md)에 대해 자세히 알아보세요.
- [분석 규칙 마법사](detect-threats-custom.md)에 대 한 자세한 내용을 참조 하세요.
