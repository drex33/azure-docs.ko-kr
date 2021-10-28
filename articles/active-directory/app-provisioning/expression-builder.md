---
title: 식 작성기가 Azure Active Directory의 애플리케이션 프로비저닝과 작동하는 방식 이해
description: 식 작성기가 Azure Active Directory의 애플리케이션 프로비저닝과 작동하는 방식 이해
services: active-directory
author: kenwith
manager: karenh444
ms.service: active-directory
ms.subservice: app-provisioning
ms.topic: conceptual
ms.workload: identity
ms.date: 06/02/2021
ms.author: kenwith
ms.reviewer: arvinh
ms.openlocfilehash: ed5623f2b7d4585a7ffe417dbfa0943bf37ebf21
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/14/2021
ms.locfileid: "129991473"
---
# <a name="understand-how-expression-builder-in-application-provisioning-works"></a>애플리케이션 프로비저닝에서 식 작성기가 작동하는 방식 이해

[식](functions-for-customizing-application-data.md)을 사용하여 [특성을 매핑](./customize-application-attributes.md)할 수 있습니다. 이전에는 이러한 식을 수동으로 만들어 식 상자에 입력해야 했습니다. 식 작성기는 식을 만드는 데 사용할 수 있는 도구입니다.

:::image type="content" source="media/expression-builder/expression-builder.png" alt-text="함수를 선택하기 전 기본 식 작성기 페이지" lightbox="media/expression-builder/expression-builder.png":::

식 작성에 대한 참조는 [특성 매핑을 위한 식 작성 참조](functions-for-customizing-application-data.md)를 참조하세요. 

## <a name="finding-the-expression-builder"></a>식 작성기 찾기

애플리케이션 프로비저닝에서 특성 매핑에 식을 사용합니다. 특성 매핑 페이지에서 **고급 옵션 표시** 를 선택한 다음 **식 작성기** 를 선택하여 식 작성기에 액세스합니다.

:::image type="content" source="media/expression-builder/accessing-expression-builder.png" alt-text="고급 설정을 표시하는 확인란이 선택되고 식 작성기라는 링크가 표시됩니다." lightbox="media/expression-builder/accessing-expression-builder.png":::

## <a name="using-expression-builder"></a>식 작성기 사용

식 작성기를 사용하려면 함수 및 특성을 선택한 다음 필요한 경우 접미사를 입력합니다. 그런 다음 **식 추가** 를 선택하여 코드 상자에 식을 추가합니다. 사용 가능한 함수 및 사용 방법에 대한 자세한 내용은 [특성 매핑을 위한 식 작성 참조](functions-for-customizing-application-data.md)를 참조하세요.

사용자를 검색하거나 값을 제공하고 **식 테스트** 를 선택하여 식을 테스트합니다. 식 테스트의 출력이 **식 출력 보기** 상자에 나타납니다.

식에 만족하는 경우 특성 매핑으로 전달합니다. 이를 복사하여 작업 중인 특성 매핑의 식 상자에 붙여넣습니다.

## <a name="known-limitations"></a>알려진 제한 사항
* 식 작성기에서 확장 특성을 선택할 수 없습니다. 그러나 특성 매핑 식에는 확장 특성을 사용할 수 있습니다. 

## <a name="next-steps"></a>다음 단계

[특성 매핑을 위한 식 작성을 위한 참조](functions-for-customizing-application-data.md)