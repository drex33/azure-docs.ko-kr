---
title: 오프라인 평가를 수행하는 방법 - Personalizer
titleSuffix: Azure Cognitive Services
description: 이 문서에서는 오프라인 평가를 사용하여 앱의 효과를 측정하고 학습 루프를 분석하는 방법을 보여 줍니다.
author: jeffmend
ms.author: jeffme
ms.manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: how-to
ms.date: 02/20/2020
ms.openlocfilehash: 6f02bb929e0ae0e7de5feaf8707eae0cf84a5e64
ms.sourcegitcommit: 16e25fb3a5fa8fc054e16f30dc925a7276f2a4cb
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2021
ms.locfileid: "122830597"
---
# <a name="analyze-your-learning-loop-with-an-offline-evaluation"></a>오프라인 평가를 통해 학습 루프 분석

오프라인 평가를 완료하고 결과를 이해하는 방법을 알아봅니다.

오프라인 평가를 통해 Personalizer가 애플리케이션의 기본 동작에 비해 얼마나 효율적인지 측정하고, 개인 설정에 가장 많이 기여하는 기능이 무엇인지 알아보고, 새 기계 학습 값을 자동으로 검색할 수 있습니다.

자세한 내용은 [오프라인 평가](concepts-offline-evaluation.md)를 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

* 구성된 Personalizer 루프
* Personalizer 루프에는 전형적인 데이터 양이 있어야 합니다. 의미 있는 평가 결과를 위해 로그에 최소 50,000개의 이벤트를 권장합니다. 필요에 따라 이전에 내보낸 _학습 정책_ 파일을 동일한 평가에서 비교하고 테스트할 수 있습니다.

## <a name="run-an-offline-evaluation"></a>오프라인 평가 실행

1. [Azure Portal](https://azure.microsoft.com/free/cognitive-services)에서 Personalizer 리소스를 찾습니다.
1. Azure Portal에서 **평가** 섹션으로 이동하여 **평가 만들기** 를 선택합니다.
    ![Azure Portal에서 **평가** 섹션으로 이동하여 **평가 만들기**를 선택합니다.](./media/offline-evaluation/create-new-offline-evaluation.png)
1. 다음 값을 구성 합니다.

    * 평가 이름.
    * 시작 날짜 및 종료 날짜 - 평가에 사용할 데이터 범위를 지정하는 날짜입니다. 이 데이터는 [데이터 보존](how-to-settings.md) 값에 지정된 대로 로그에 있어야 합니다.
    * 최적화 검색을 **예** 로 설정합니다.

    > [!div class="mx-imgBorder"]
    > ![오프라인 평가 설정 선택](./media/offline-evaluation/create-an-evaluation-form.png)

1. **확인** 을 선택하여 평가를 시작합니다.

## <a name="review-the-evaluation-results"></a>평가 결과 검토

처리할 데이터의 양, 비교할 학습 정책의 수, 최적화 요청 여부에 따라 평가가 오래 걸릴 수 있습니다.

완료되면 평가 목록에서 평가를 선택한 다음 **애플리케이션의 점수와 다른 잠재적인 학습 설정 비교** 를 선택할 수 있습니다. 현재 학습 정책이 새 정책과 비교하여 수행되는 방식을 확인하려면 이 기능을 선택합니다.

1. [학습 정책](concepts-offline-evaluation.md#discovering-the-optimized-learning-policy)의 성능을 검토합니다.

    > [!div class="mx-imgBorder"]
    > [![평가 결과 검토](./media/offline-evaluation/evaluation-results.png)](./media/offline-evaluation/evaluation-results.png#lightbox)

1. **적용** 을 선택하여 모델을 데이터에 가장 적합하도록 향상시키는 정책을 적용합니다.

## <a name="next-steps"></a>다음 단계

* [오프라인 평가 수행 방법](concepts-offline-evaluation.md)에 대해 알아봅니다.
