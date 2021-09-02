---
title: Personalizer에서 다중 슬롯을 사용하는 방법
description: Personalizer에서 다중 슬롯을 사용하여 서비스에서 제공하는 콘텐츠 권장 사항을 개선하는 방법에 대해 알아봅니다.
services: cognitive-services
author: jeffmend
ms.author: jeffme
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: quickstart
ms.date: 05/24/2021
zone_pivot_groups: programming-languages-set-six
ms.openlocfilehash: ea1f244b43becd65b3bc85f2e40dc2c029ccbf43
ms.sourcegitcommit: 16e25fb3a5fa8fc054e16f30dc925a7276f2a4cb
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2021
ms.locfileid: "122829273"
---
# <a name="get-started-with-multi-slot-for-azure-personalizer"></a>Azure Personalizer에 대한 다중 슬롯 시작

다중 슬롯 개인 설정(미리 보기)을 사용하면 사용자에게 둘 이상의 작업(예: 제품 또는 콘텐츠)이 표시되는 웹 레이아웃, 캐러셀 및 목록에서 콘텐츠를 대상으로 지정할 수 있습니다. Personalizer 다중 슬롯 API를 사용하면 Personalizer의 AI 모델이 사용자 인터페이스의 배치를 고려하고 학습하면서, 특정 동작을 구동하는 사용자 컨텍스트 및 제품을 학습할 수 있습니다. 예를 들어 Personalizer는 특정 제품 또는 콘텐츠가 페이지의 주요 강조 표시보다 사이드바 또는 바닥글로 더 많은 클릭을 발생시킨다는 것을 알 수 있습니다. 

이 가이드에서는 Personalizer 다중 슬롯 API를 사용하는 방법에 대해 알아봅니다.

::: zone pivot="programming-language-csharp"

[!INCLUDE [Try multi-slot with C#](./includes/quickstart-multislot-csharp.md)]

::: zone-end

::: zone pivot="programming-language-javascript"

[!INCLUDE [Try multi-slot with Node.js](./includes/quickstart-multislot-nodejs.md)]

::: zone-end

::: zone pivot="programming-language-python"

[!INCLUDE [Try multi-slot with Python](./includes/quickstart-multislot-python.md)]

::: zone-end

## <a name="next-steps"></a>다음 단계

* [다중 슬롯에 대한 자세한 정보](concept-multi-slot-personalization.md)