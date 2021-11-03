---
title: 사용자 지정 텍스트 분류 FAQ
titleSuffix: Azure Cognitive Services
description: 사용자 지정 텍스트 분류 API를 사용할 때 질문과 대답을 알아봅니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-custom-classification, ignite-fall-2021
ms.openlocfilehash: 9320fee8eb7ad44b33246f28091dfb0b4bd72bb2
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131434392"
---
# <a name="frequently-asked-questions"></a>질문과 대답

개념에 대 한 일반적인 질문과 대답 및 언어에 대 한 Azure 인식 서비스의 사용자 지정 텍스트 분류와 관련 된 시나리오에 대해 알아봅니다.

## <a name="how-do-i-get-started-with-the-service"></a>서비스를 시작 어떻게 할까요??

[빠른](./quickstart.md) 시작을 참조 하 여 첫 번째 프로젝트를 신속 하 게 만들거나, 자세한 내용을 보려면 [프로젝트를 만드는 방법](how-to/create-project.md) 을 참조 하세요.

## <a name="what-are-the-service-limits"></a>서비스의 제한 사항은 무엇입니까?

자세한 내용은 [서비스 제한 문서](service-limits.md) 를 참조 하세요.

## <a name="which-languages-are-supported-in-this-feature"></a>이 기능은 어떤 언어를 지원하나요?

[언어 지원](./language-support.md) 문서를 참조하세요.

## <a name="how-many-tagged-files-are-needed"></a>태그가 지정 된 파일의 수는 몇 개입니까?

일반적으로 태그 지정이 정확 하 고 정확 하 게 완벽 하 게 수행 되는 경우 다양 하 고 기본적인 태그가 지정 된 [데이터](how-to/tag-data.md) 는 더 나은 결과를 초래 합니다. 모든 모델이 제대로 작동 하도록 하는 태그가 지정 된 클래스의 집합 수는 없습니다. 성능은 스키마와 스키마의 모호성에 따라 매우 다릅니다. 모호한 클래스에는 더 많은 태그가 필요 합니다. 또한 성능은 태그 지정의 품질에 따라 달라 집니다. 클래스 당 권장 되는 태그가 지정 된 인스턴스 수는 50입니다. 

## <a name="training-is-taking-a-long-time-is-this-expected"></a>학습 시간이 오래 걸리는데, 예상되는 건가요?

학습 프로세스는 다소 시간이 걸릴 수 있습니다. 대략적인 추정치로 1280만 문자를 결합 한 파일에 대 한 예상 학습 시간은 6 시간입니다.

## <a name="how-do-i-build-my-custom-model-programmatically"></a>프로그래밍 방식으로 사용자 지정 모델을 빌드하 어떻게 할까요??

[REST api](https://aka.ms/ct-authoring-swagger) 를 사용 하 여 사용자 지정 모델을 빌드할 수 있습니다. 이 [빠른](quickstart.md?pivots=rest-api) 시작을 따라 제작 api를 호출 하는 방법에 대 한 예제를 위해 api를 통해 프로젝트 만들기 및 모델 만들기를 시작할 수 있습니다. 


## <a name="what-is-the-recommended-cicd-process"></a>권장 CI/CD 프로세스는 무엇 인가요?

동일한 프로젝트 내의 동일한 데이터 집합에서 여러 모델을 학습 시킬 수 있습니다. 모델을 성공적으로 학습 한 후에 [는 해당 평가를 볼](how-to/view-model-evaluation.md)수 있습니다. [언어 스튜디오](https://aka.ms/languageStudio)에서 모델을 [배포 하 고 테스트할](quickstart.md#deploy-your-model) 수 있습니다. 데이터에서 태그를 추가 하거나 제거 하 고 **새** 모델을 학습 하 고 테스트할 수 있습니다. 동일한 프로젝트를 사용 하 여 학습 된 모델의 최대 수에 대 한 자세한 내용은 [서비스 제한](service-limits.md)을 참조 하세요. 새 모델을 학습 하는 경우 데이터 집합은 학습 및 테스트 집합으로 임의로 [분할](how-to/train-model.md#data-splits) 됩니다. 이로 인해 동일한 테스트 집합에 대해 모델 평가를 수행 하는 것을 보장할 수 없으므로 결과를 비교할 수 없습니다. 사용자 고유의 테스트 집합을 개발 하 고이를 사용 하 여 향상 된 기능을 측정할 수 있도록 두 모델을 평가 하는 것이 좋습니다.

## <a name="does-a-low-or-high-model-score-guarantee-bad-or-good-performance-in-production"></a>낮은 또는 높은 모델 점수가 프로덕션 환경에서 불량 하거나 좋은 성능을 보장 하나요?

모델 평가는 항상 포괄적이 지 않을 수 있습니다. 다음에 종속 됩니다. 
* **테스트 집합이** 너무 작으면 좋은/잘못 된 점수가 모델의 실제 성능을 나타내지는 않습니다. 또한 테스트 집합에서 특정 클래스가 누락 되거나 아래에 표시 되 면 모델 성능에 영향을 줍니다.
* **데이터 다양성** 데이터에서 프로덕션 환경에서 예상 되는 텍스트의 시나리오/예제를 거의 포함 하지 않는 경우 모델은 가능한 모든 시나리오에 노출 되지 않으며 학습 되지 않은 시나리오에서 제대로 작동 하지 않을 수 있습니다.
* **데이터 표현** 모델을 학습 하는 데 사용 되는 데이터 집합이 프로덕션 환경에서 모델에 도입 되는 데이터를 나타내지 않는 경우 모델 성능이 크게 영향을 받습니다.

자세한 내용은 [데이터 선택 및 스키마 디자인](how-to/design-schema.md) 문서를 참조 하세요.

## <a name="how-do-i-improve-model-performance"></a>모델 성능을 향상 어떻게 할까요? 있나요?

* 모델 [혼동 행렬](how-to/view-model-evaluation.md)보기. 특정 클래스가 자주 분류 되지 않는 경우이 클래스에 대해 태그가 지정 된 인스턴스를 더 추가 하는 것이 좋습니다. 두 클래스를 서로 자주 분류 하는 경우에는 스키마가 모호 하다는 것을 알 수 있습니다. 이렇게 하면 더 나은 성능을 위해 둘 다를 하나의 클래스로 병합 하는 것이 좋습니다.

*  [데이터 배포 검사](how-to/improve-model.md#examine-data-distribution-from-language-studio) 클래스 중 하나에 더 많은 태그가 지정 된 인스턴스가 있는 경우에는 모델이이 클래스에 따라 달라질 수 있습니다. 다른 클래스에 더 많은 데이터를 추가 하거나 dominating 클래스에서 대부분의 예제를 제거 합니다. 

* 데이터 선택 및 스키마 디자인에 대 한 자세한 내용은 [여기](how-to/design-schema.md)를 참조 하세요.

* [테스트 집합을 검토](how-to/improve-model.md) 하 여 예측 및 태그가 지정 된 클래스를 나란히 표시 하 여 모델 성능을 보다 잘 파악 하 고 스키마 또는 태그의 변경 내용이 필요한 지 여부를 결정할 수 있습니다.

## <a name="when-i-retrain-my-model-i-get-different-results-why-is-this"></a>모델을 다시 학습 때 다른 결과가 발생 하는 이유는 무엇 인가요?

* 새 모델을 학습 하는 경우 데이터 집합은 학습 및 테스트 집합으로 임의로 [분할](how-to/train-model.md#data-splits) 되므로 반사 된 모델 평가가 동일한 테스트 집합에 있다는 보장이 없으므로 결과를 비교할 수 없습니다.

* 동일한 모델을 다시 학습 하는 경우 테스트 집합은 동일 하지만 모델에서 수행 하는 예측이 약간 변경 된 것을 확인할 수 있습니다. 이는 학습 된 모델이 충분히 강력 하지 않아 데이터의 기반이 되는 방법 및 태그가 지정 된 데이터의 품질에 대 한 요소 이기 때문입니다. 

## <a name="how-do-i-get-predictions-in-different-languages"></a>다른 언어로 예측을 얻으려면 어떻게 해야 하나요?

먼저 프로젝트를 [만들](how-to/create-project.md) 때 다국어 옵션을 사용 하도록 설정 하거나 나중에 프로젝트 설정 페이지에서 사용할 수 있도록 설정 해야 합니다. 모델을 학습 하 고 배포한 후에는 [여러 언어로](language-support.md#multiple-language-support)쿼리를 시작할 수 있습니다. 다른 언어에 대해 다양한 결과를 얻을 수 있습니다. 모든 언어의 정확도를 높이려면 해당 언어로 프로젝트에 태그가 지정 된 인스턴스를 추가 하 여 학습 된 모델을 해당 언어의 더 많은 구문에 도입 합니다.

## <a name="i-trained-my-model-but-i-cant-test-it"></a>모델을 학습 했지만 테스트할 수 없습니다.

모델을 테스트 하려면 먼저 [배포](quickstart.md#deploy-your-model) 해야 합니다. 

## <a name="how-do-i-use-my-trained-model-to-make-predictions"></a>학습 된 모델을 사용 하 여 예측을 만드는 어떻게 할까요?

모델을 배포한 후에 [는 예측 API를 호출](how-to/call-api.md)합니다. 자세한 내용은 [예측 API 참조](https://aka.ms/ct-runtime-swagger) 를 참조 하세요.

## <a name="data-privacy-and-security"></a>데이터 개인 정보 보호 및 보안

사용자 지정 텍스트 분류는 GDPR (일반 데이터 보호 규정) 용 데이터 프로세서입니다. GDPR 정책과 호환 되는 사용자 지정 분류 사용자에 게는 [언어 스튜디오](https://aka.ms/languageStudio) 또는 [REST api](https://aka.ms/ct-authoring-swagger)를 사용 하 여 프로그래밍 방식으로 사용자 콘텐츠를 보거나, 내보내거나, 삭제할 수 있는 모든 권한이 있습니다.

사용자의 데이터는 Azure Storage 계정에만 저장 됩니다. 사용자 지정 분류는 학습 중에 읽을 수 있는 액세스 권한만 갖습니다.

## <a name="how-to-clone-my-project"></a>내 프로젝트를 복제 하는 방법

프로젝트를 복제 하려면 내보내기 API를 사용 하 여 프로젝트 자산을 내보낸 다음 새 프로젝트로 가져와야 합니다. 두 작업에 대 한 [REST api](https://aka.ms/ct-authoring-swagger) 참조를 참조 하세요.

## <a name="next-steps"></a>다음 단계

* [사용자 지정 텍스트 분류 개요](overview.md)
* [빠른 시작](quickstart.md)
