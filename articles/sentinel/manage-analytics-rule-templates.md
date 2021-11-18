---
title: Microsoft Sentinel에서 예약된 분석 규칙에 대한 템플릿 버전 관리
description: 예약된 분석 규칙 템플릿과 해당 템플릿에서 만든 규칙 간의 관계를 관리하는 방법을 알아봅니다. 템플릿에 대한 업데이트를 규칙에 병합하고 규칙의 변경 내용을 다시 원래 템플릿으로 되돌려 줍니다.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/09/2021
ms.author: yelevin
ms.custom: ignite-fall-2021
ms.openlocfilehash: c8402afbe6068e6eb7c81276bdbecc86c30acbbc
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2021
ms.locfileid: "132723117"
---
# <a name="manage-template-versions-for-your-scheduled-analytics-rules-in-microsoft-sentinel"></a>Microsoft Sentinel에서 예약된 분석 규칙에 대한 템플릿 버전 관리

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

> [!IMPORTANT]
>
> 이 기능은 **미리 보기** 에 있습니다. 베타 또는 미리 보기로 제공되거나 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 추가 약관은 [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

## <a name="introduction"></a>소개

Microsoft Sentinel은 실제로 복사본을 만들어 활성 규칙으로 전환하는 [분석 규칙 템플릿과](detect-threats-built-in.md) 함께 제공됩니다. 이는 템플릿에서 규칙을 만들 때 발생합니다. 그러나 이 시점에서 활성 규칙은 더 이상 템플릿에 연결되지 않습니다. Microsoft 엔지니어 또는 다른 사람이 규칙 템플릿을 변경한 경우 해당 템플릿에서 미리 만든 규칙은 새 템플릿과 일치하도록 동적으로 ***업데이트되지 않습니다.***

그러나 템플릿에서 만든 규칙은  두 가지 이점을 제공하는 템플릿을 기억합니다.

- 템플릿에서 규칙을 만들 때(또는 그 이후 언제든지) 규칙을 변경한 경우 언제든지 규칙을 원래 버전(템플릿의 복사본)으로 되돌릴 수 있습니다.

- 템플릿이 업데이트되면 알림을 받을 수 있으며, 규칙을 새 버전의 템플릿으로 업데이트하거나 그대로 둘 수 있습니다.

이 문서에서는 이러한 작업을 관리하는 방법과 유의해야 할 사항을 보여줍니다. 아래에서 설명한 절차는 템플릿에서 만든 **[예약된](detect-threats-built-in.md#scheduled)** 분석 규칙에 적용됩니다.

## <a name="discover-your-rules-template-version-number"></a>규칙의 템플릿 버전 번호 검색

템플릿 버전 제어의 구현을 사용하면 규칙 템플릿의 버전과 규칙에서 만든 규칙을 보고 추적할 수 있습니다. 템플릿이 업데이트된 규칙은 규칙 이름 옆에 *"업데이트 사용 가능"* 배지를 표시합니다.

1. **분석** 블레이드에서 **활성 규칙** 탭을 선택합니다.

1. **예약된** 형식의 규칙을 선택합니다.  

    - 규칙에 *"업데이트 사용 가능"* 배지가 표시되면 세부 정보 창에 **편집** 단추 옆에 검토 **및 업데이트** 단추가 있습니다(아래 다음 단계에서 이미지 1 참조).

    - 규칙이 템플릿에서 만들어졌지만 *"업데이트 사용 가능"* 배지가 없는 경우 세부 정보 창에는 **편집** 단추 옆에 **템플릿과 비교** 단추가 있습니다(아래 다음 단계에서 이미지 2 및 3 참조).

    - **편집** 단추만 있는 경우 규칙은 템플릿이 아닌 처음부터 만들어졌습니다.

        :::image type="content" source="media/manage-analytics-rule-templates/see-rules-with-updated-template.png" alt-text="템플릿 업데이트를 사용할 수 있음을 나타내는 배지가 있는 활성 규칙 목록의 스크린샷" lightbox="media/manage-analytics-rule-templates/see-rules-with-updated-template.png":::

1. 세부 정보 창의 맨 아래에 Scroll down 두 개의 버전 번호, 즉 규칙이 만들어진 템플릿 버전과 사용 가능한 최신 버전의 템플릿이 표시됩니다. 

    :::image type="content" source="media/manage-analytics-rule-templates/see-template-versions.png" alt-text="세부 정보 창의 스크린샷. 템플릿 버전 번호를 보려면 Scroll down." border="false":::

    숫자는 "1.0.0" 형식(주 버전, 부 버전 및 빌드)입니다.  
    (당분간 빌드 번호는 사용되지 않으며 항상 0이 됩니다.)

    - *주 버전* 번호의 차이는 템플릿에 필수적인 항목이 변경되었음을 나타내며, 규칙이 위협을 감지하는 방법 또는 완전히 작동하는 기능에 영향을 줄 수 있습니다. 이는 규칙에 포함하려는 변경입니다.

    - *부 버전* 번호의 차이는 템플릿의 사소한 개선(외관 변경 또는 이와 유사한 기능)을 나타냅니다. 이는 "좋은 기능"이지만 규칙의 기능, 효율성 또는 성능을 유지하는 데 중요하지 않습니다. 이는 쉽게 전환하거나 나갈 수 있는 변경입니다.

    > [!NOTE]
    > 위의 이미지 2와 3은 템플릿이 업데이트되지 않은 템플릿에서 만든 규칙의 두 가지 예를 보여줍니다.
    > - 이미지 2는 현재 템플릿에 대한 버전 번호가 있는 규칙을 보여줍니다. 이는 2021년 10월 Microsoft Sentinel의 템플릿 버전 제어 초기 구현 후에 규칙이 생성되었음을 나타냅니다.
    > - 이미지 3은 현재 템플릿 버전이 없는 규칙을 보여줍니다. 이는 규칙이 2021년 10월 이전에 만들어졌음을 보여줍니다. 사용 가능한 최신 템플릿 버전이 있는 경우 규칙을 만드는 데 사용된 버전보다 최신 버전의 템플릿일 수 있습니다.

## <a name="compare-your-active-rule-with-its-template"></a>활성 규칙을 해당 템플릿과 비교

수행하려는 작업에 따라 다음 탭 중 하나를 선택하여 해당 작업에 대한 지침을 확인합니다.

# <a name="update-template"></a>[템플릿 업데이트](#tab/update)

규칙을 선택하고 업데이트할 것을 결정한 경우 세부 정보 창에서 **검토 및 업데이트를** 선택합니다(위 참조). 이제 분석 규칙 **마법사에** **최신 버전과 비교** 탭이 표시됩니다.

이 탭에는 기존 규칙의 YAML 표현과 최신 버전의 템플릿이 나란히 비교됩니다. 

:::image type="content" source="media/manage-analytics-rule-templates/compare-template-versions.png" alt-text="분석 규칙 마법사의 '최신 버전과 비교' 탭의 스크린샷.":::

> [!NOTE]
> 이 규칙을 업데이트하면 기존 규칙을 최신 버전의 템플릿으로 덮어쓰게 됩니다.

참조된 이름이 변경된 경우 기존 규칙을 참조하는 모든 자동화 단계 또는 논리를 확인해야 합니다. 또한 원래 규칙을 만들 때 만든 사용자 지정(쿼리, 일정 예약, 그룹화 또는 기타 설정 변경)을 덮어쓸 수 있습니다.

### <a name="update-your-rule-with-the-new-template-version"></a>새 템플릿 버전으로 규칙 업데이트

- 템플릿의 새 버전에 대한 변경 내용이 허용되고 원래 규칙의 다른 어떤 것도 영향을 받지 않은 경우 **검토 및 업데이트를** 선택하여 변경 내용을 확인하고 적용합니다. 

- 규칙을 추가로 사용자 지정하거나 덮어쓸 수 있는 변경 내용을 다시 적용하려면 **다음: 사용자 지정 변경 내용을** 선택합니다. 이 옵션을 선택하면 [분석 규칙 마법사의](detect-threats-custom.md) 나머지 탭을 순환하여 변경합니다. 그런 다음 검토 및 업데이트 탭에서 변경 내용의 유효성을 **검사하고** 적용합니다.

- 기존 규칙을 변경하지 않고 기존 템플릿 버전을 유지하려면 오른쪽 위 모서리에서 X를 선택하여 마법사를 종료하면 됩니다.

# <a name="revert-to-template"></a>[템플릿으로 되돌리기](#tab/revert)

규칙을 선택하고 원래 버전으로 되돌리려는 경우 세부 정보 창에서 **템플릿과 비교를** 선택합니다(위 참조). 이제 분석 규칙 **마법사에** **최신 버전과 비교** 탭이 표시됩니다.

이 탭에는 기존 규칙의 YAML 표현과 최신 버전의 템플릿이 나란히 비교됩니다. 이러한 두 버전 번호는 동일할 수 있지만 왼쪽에는 템플릿에서 만든 도중 또는 이후에 변경된 내용이 포함된 활성 규칙이 표시되는 반면 오른쪽에는 변경되지 않은 템플릿이 표시됩니다.

:::image type="content" source="media/manage-analytics-rule-templates/compare-template-versions-2.png" alt-text="분석 규칙 마법사의 '최신 버전과 비교' 탭의 스크린샷.":::

> [!NOTE]
> 이 규칙을 업데이트하면 기존 규칙을 최신 버전의 템플릿으로 덮어쓰게 됩니다.
참조된 이름이 변경된 경우 기존 규칙을 참조하는 모든 자동화 단계 또는 논리를 확인해야 합니다. 또한 원래 규칙을 만들 때 만든 사용자 지정(쿼리, 일정 예약, 그룹화 또는 기타 설정 변경)을 덮어쓸 수 있습니다.

### <a name="revert-your-rule-to-its-original-template-version"></a>규칙을 원래 템플릿 버전으로 되돌리기

- 템플릿의 클린 복사본인 이 규칙의 원래 버전으로 완전히 되돌리려면 **검토 및 업데이트를** 선택하여 변경 내용을 확인하고 적용합니다. 

- 규칙을 다르게 사용자 지정하거나 덮어쓸 수 있는 변경 내용을 다시 적용하려면 **다음: 사용자 지정 변경 내용을** 선택합니다. 이 옵션을 선택하면 [분석 규칙 마법사의](detect-threats-custom.md) 나머지 탭을 순환하여 변경합니다. 그런 다음 검토 및 업데이트 탭에서 변경 내용의 유효성을 **검사하고** 적용합니다.

- 기존 규칙을 변경하지 않으려면 오른쪽 위 모서리에서 X를 선택하여 마법사를 종료하면 됩니다.

---

## <a name="next-steps"></a>다음 단계
이 문서에서는 Microsoft Sentinel 분석 규칙 템플릿의 버전을 추적하고 활성 규칙을 기존 템플릿 버전으로 되돌리거나 새 템플릿 버전으로 업데이트하는 방법을 배웠습니다. Microsoft Sentinel에 대한 자세한 내용은 다음 문서를 참조하세요.

- [분석 규칙에](detect-threats-built-in.md)대해 자세히 알아보세요.
- [분석 규칙 마법사](detect-threats-custom.md)에 대한 자세한 내용을 참조하세요.
