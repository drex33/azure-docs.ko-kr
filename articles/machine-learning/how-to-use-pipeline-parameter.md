---
title: 파이프라인 매개 변수를 사용 하 여 다양 한 파이프라인 작성
titleSuffix: Azure Machine Learning
description: Azure Machine Learning 디자이너에서 파이프라인 매개 변수를 사용하는 방법입니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: mlops
ms.author: keli19
author: likebupt
ms.date: 04/09/2020
ms.topic: how-to
ms.custom: designer
ms.openlocfilehash: b3ef6f684123c863fd2bdfb5a5ec953b3b20cc6f
ms.sourcegitcommit: f29615c9b16e46f5c7fdcd498c7f1b22f626c985
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/04/2021
ms.locfileid: "129428068"
---
# <a name="use-pipeline-parameters-in-the-designer-to-build-versatile-pipelines"></a>디자이너에서 파이프라인 매개 변수를 사용하여 다양한 파이프라인 빌드

파이프라인 매개 변수를 사용하여 디자이너에서 유연한 파이프라인을 빌드합니다. 파이프라인 매개 변수를 사용하면 파이프라인 논리를 캡슐화하고 자산을 다시 사용하기 위해 런타임 시 동적으로 값을 설정할 수 있습니다.

파이프라인 매개 변수는 파이프라인 실행을 다시 제출하거나, [모델을 다시 학습](how-to-retrain-designer.md)하거나, [일괄 처리 예측을 수행할](how-to-run-batch-predictions-designer.md)때 특히 유용합니다.

이 문서에서는 다음을 수행하는 방법을 알아봅니다.

> [!div class="checklist"]
> * 파이프라인 매개 변수 만들기
> * 파이프라인 매개 변수 삭제 및 관리
> * 파이프라인 매개 변수를 조정하는 동안 파이프라인 실행 트리거

## <a name="prerequisites"></a>사전 요구 사항

* Azure Machine Learning 작업 영역 [Azure Machine Learning 작업 영역 만들기](how-to-manage-workspace.md)를 참조하세요.

* 디자이너에 대한 단계별 소개는 [디자이너 자습서](tutorial-designer-automobile-price-train-score.md)를 완료하세요. 

[!INCLUDE [machine-learning-missing-ui](../../includes/machine-learning-missing-ui.md)]

## <a name="create-pipeline-parameter"></a>파이프라인 매개 변수 만들기

디자이너에서 파이프라인 매개 변수를 만드는 방법에는 다음 세 가지가 있습니다.
- 설정 패널에서 파이프라인 매개 변수를 만들고 모듈에 바인딩합니다.
- 모듈 매개 변수를 파이프라인 매개 변수로 승격합니다.
- 데이터 세트를 파이프라인 매개 변수로 승격

> [!NOTE]
> 파이프라인 매개 변수는 `int`, `float` 및 `string` 등의 기본 데이터 형식만 지원합니다.

### <a name="option-1-create-a-pipeline-parameter-in-the-settings-panel"></a>옵션 1: 설정 패널에서 파이프라인 매개 변수 만들기

이 섹션에서는 설정 패널에서 파이프라인 매개 변수를 만듭니다.

이 예제에서는 **누락된 데이터 정리** 모듈을 사용하여 파이프라인이 누락된 데이터를 채우는 방법을 정의하는 파이프라인 매개 변수를 만듭니다.

1. 파이프라인 초안의 이름 옆에서 **기어 아이콘** 을 선택하여 **설정** 패널을 엽니다.

1. **파이프라인 매개 변수** 섹션에서 **+** 아이콘을 선택합니다.

1.  매개 변수에 대한 이름 및 기본값을 입력합니다. 

    예를 들어 매개 변수 이름으로 `replace-missing-value`를 기본값으로 `0`을 입력합니다.

   ![파이프라인 매개 변수를 만드는 방법을 보여 주는 스크린샷](media/how-to-use-pipeline-parameter/create-pipeline-parameter.png)


파이프라인 매개 변수를 만든 후에는 동적으로 설정하려는 [모듈 매개 변수에 연결](#attach-module-parameter-to-pipeline-parameter)해야 합니다.

### <a name="option-2-promote-a-module-parameter"></a>옵션 2: 모듈 매개 변수 승격

모듈 값에 대한 파이프라인 매개 변수를 만드는 가장 간단한 방법은 모듈 매개 변수를 승격하는 것입니다. 다음 단계를 사용하여 모듈 매개 변수를 파이프라인 매개 변수로 승격합니다.

1. 파이프라인 매개 변수를 연결하려는 모듈을 선택합니다.
1. 모듈 세부 정보 창에서 지정하려는 매개 변수를 마우스로 가리킵니다.
1. 표시되는 줄임표( **...** )를 선택합니다.
1. **파이프라인 매개 변수 추가** 를 선택합니다.

    ![모듈 매개 변수를 파이프라인 parameter1로 승격하는 방법을 보여 주는 스크린샷](media/how-to-use-pipeline-parameter/promote-module-para-to-pipeline-para.png)

1. 매개 변수 이름 및 기본값을 입력합니다.
1. **저장** 을 선택합니다.

이제 이 파이프라인을 제출할 때 언제든지 이 매개 변수에 새 값을 지정할 수 있습니다.

### <a name="option-3-promote-a-dataset-to-a-pipeline-parameter"></a>옵션 3: 데이터 세트를 파이프라인 매개 변수로 승격

변수 데이터 세트를 사용하여 파이프라인을 제출하려면 데이터 세트를 파이프라인 매개 변수로 승격시켜야 합니다.

1. 파이프라인 매개 변수로 바꾸려는 데이터 세트를 선택합니다.

1. 데이터 세트의 세부 정보 패널에서 **파이프라인 매개 변수로 설정** 을 선택합니다.

   ![파이프라인 매개 변수로 데이터 세트를 설정하는 방법을 보여 주는 스크린샷](media/how-to-use-pipeline-parameter/set-dataset-as-pipeline-parameter.png)

이제 다음에 파이프라인을 실행할 때 파이프라인 매개 변수를 사용하여 다른 데이터 세트를 지정할 수 있습니다.

## <a name="attach-and-detach-module-parameter-to-pipeline-parameter"></a>파이프라인 매개 변수에 모듈 매개 변수 연결 및 분리 

이 섹션에서는 파이프라인 매개 변수에 모듈 매개 변수를 연결 및 분리하는 방법에 대해 알아봅니다.

### <a name="attach-module-parameter-to-pipeline-parameter"></a>파이프라인 매개 변수에 모듈 매개 변수 연결

파이프라인 실행을 트리거할 때 한 번에 값을 변경하려는 경우 중복된 모듈의 동일한 모듈 매개 변수를 동일한 파이프라인 매개 변수에 연결할 수 있습니다.

다음 예제에는 중복된 **누락 데이터 정리** 모듈이 있습니다. 각 **누락 데이터 정리** 모듈에 대해 **교체 값** 을 파이프라인 매개 변수 **replace-missing-value** 에 연결합니다.

1. **누락 데이터 정리** 모듈을 선택합니다.

1. 모듈 세부 정보 창에서 캔버스 오른쪽에 있는 **정리 모드** 를 "사용자 지정 대체 값"으로 설정합니다.

1. **대체 값** 필드를 마우스로 가리킵니다.

1. 표시되는 줄임표( **...** )를 선택합니다.

1. 파이프라인 매개 변수 `replace-missing-value`를 선택합니다.

   ![파이프라인 매개 변수를 연결하는 방법을 보여 주는 스크린샷](media/how-to-use-pipeline-parameter/attach-replace-value-to-pipeline-parameter.png)

**대체 값** 필드를 파이프라인 매개 변수에 성공적으로 연결했습니다. 


### <a name="detach-module-parameter-to-pipeline-parameter"></a>파이프라인 매개 변수에 모듈 매개 변수 분리

**대체 값** 을 파이프라인 매개 변수에 연결한 후에는 실행할 수 없습니다.

모듈 매개 변수 옆에 있는 줄임표( **...** )를 클릭하여 모듈 매개 변수를 파이프라인 매개 변수로 분리하고 **파이프라인 매개 변수에서 분리** 를 선택할 수 있습니다.

 ![파이프라인 매개 변수에 연결한 후에 실행할 수 없는 동작을 보여 주는 스크린샷](media/how-to-use-pipeline-parameter/non-actionable-module-parameter.png)

## <a name="update-and-delete-pipeline-parameters"></a>파이프라인 매개 변수 업데이트 및 삭제

이 섹션에서는 파이프라인 매개 변수를 업데이트하고 삭제하는 방법에 대해 알아봅니다.

### <a name="update-pipeline-parameters"></a>파이프라인 매개 변수 업데이트

다음 단계를 사용하여 모듈 파이프라인 매개 변수를 업데이트합니다.

1. 캔버스 위쪽에서 기어 아이콘을 선택합니다.
1. **파이프라인 매개 변수** 섹션에서 모든 파이프라인 매개 변수에 대한 이름 및 기본값을 보고 업데이트할 수 있습니다.

### <a name="delete-a-dataset-pipeline-parameter"></a>데이터 세트 파이프라인 매개 변수 삭제

다음 단계를 사용하여 데이터 세트 파이프라인 매개 변수를 삭제합니다.

1. 데이터 세트 모듈을 선택합니다.
1. **파이프라인 매개 변수로 설정** 옵션을 선택 취소합니다.


### <a name="delete-module-pipeline-parameters"></a>모듈 파이프라인 매개 변수 삭제

다음 단계를 사용하여 모듈 파이프라인 매개 변수를 삭제합니다.

1. 캔버스 위쪽에서 기어 아이콘을 선택합니다.

1. 파이프라인 매개 변수 옆의 줄임표( **...** )를 선택합니다.

    이 보기에는 파이프라인 매개 변수가 연결된 모듈이 표시됩니다.

    ![모듈에 적용된 현재 파이프라인 매개 변수를 보여 주는 스크린샷](media/how-to-use-pipeline-parameter/delete-pipeline-parameter2.png)

1. **매개 변수 삭제** 를 선택하여 파이프라인 매개 변수를 삭제합니다.

    > [!NOTE]
    > 파이프라인 매개 변수를 삭제하면 연결된 모든 모듈 매개 변수가 분리되고, 분리된 모듈 매개 변수 값은 현재 파이프라인 매개 변수 값을 유지합니다.     

## <a name="trigger-a-pipeline-run-with-pipeline-parameters"></a>파이프라인 매개 변수를 사용하여 파이프라인 실행 트리거 

이 섹션에서는 파이프라인 매개 변수를 설정하는 동안 파이프라인 실행을 제출하는 방법에 대해 알아봅니다.

### <a name="resubmit-a-pipeline-run"></a>파이프라인 실행 다시 제출

파이프라인 매개 변수를 사용하여 파이프라인을 제출한 후에는 다른 매개 변수를 사용하여 파이프라인 실행을 다시 제출할 수 있습니다.

1. 파이프라인 세부 정보 페이지로 이동합니다. **파이프라인 실행 개요** 창에서 현재 파이프라인 매개 변수 및 값을 확인할 수 있습니다.

1. **다시 제출** 을 선택합니다.
1. **파이프라인 실행 설정** 에서 새 파이프라인 매개 변수를 지정합니다. 

![파이프라인 매개 변수를 사용하여 파이프라인 다시 제출을 보여 주는 스크린샷](media/how-to-use-pipeline-parameter/resubmit-pipeline-run.png)

### <a name="use-published-pipelines"></a>게시된 파이프라인 사용

파이프라인을 게시하여 해당 파이프라인 매개 변수를 사용할 수도 있습니다. **게시된 파이프라인** 은 클라이언트 애플리케이션에서 REST 엔드포인트를 통해 호출할 수 있는 컴퓨팅 리소스에 배포된 파이프라인입니다.

게시된 엔드포인트는 재학습 및 일괄 처리 예측 시나리오에 특히 유용합니다. 자세한 내용은 [디자이너에서 모델을 다시 학습하는 방법](how-to-retrain-designer.md) 또는 [디자이너에서 일괄 처리 예측 실행](how-to-run-batch-predictions-designer.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계

이 문서에서는 디자이너에서 파이프라인 매개 변수를 만드는 방법을 알아보았습니다. 다음으로 파이프라인 매개 변수를 사용하여 [모델을 다시 학습](how-to-retrain-designer.md)하거나 [일괄 처리 예측](how-to-run-batch-predictions-designer.md)을 수행하는 방법을 확인합니다.

[SDK를 사용하여 프로그래밍 방식으로 파이프라인을 사용](how-to-deploy-pipelines.md)하는 방법에 대해서도 알아볼 수 있습니다.
