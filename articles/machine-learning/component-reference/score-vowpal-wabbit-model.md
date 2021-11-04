---
title: Vowpal Wabbit 모델 점수 매기기
titleSuffix: Azure Machine Learning
description: 점수 Vowpal Wabbit 모델 구성 요소를 사용 하 여 기존의 학습 된 Vowpal Wabbit 모델을 사용 하는 입력 데이터 집합에 대 한 점수를 생성 하는 방법에 대해 알아봅니다.
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 07/02/2020
ms.openlocfilehash: c673b01e48b9a20648b0d5fb3fa05511ee64f49b
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131570172"
---
# <a name="score-vowpal-wabbit-model"></a>Vowpal Wabbit 모델 점수 매기기
이 문서에서는 Azure Machine Learning designer에서 **점수 Vowpal wabbit 모델** 구성 요소를 사용 하 여 기존의 학습 된 Vowpal wabbit 모델을 사용 하 여 입력 데이터 집합에 대 한 점수를 생성 하는 방법을 설명 합니다.  

이 구성 요소는 Vowpal Wabbit 프레임 워크 버전 8.8.1의 최신 버전을 제공 합니다. VW 버전 8 형식으로 저장 된 학습 된 모델을 사용 하 여 데이터의 점수를 매기는 데이 구성 요소를 사용 합니다.  

## <a name="how-to-configure-score-vowpal-wabbit-model"></a>Vowpal Wabbit 모델 점수 매기기를 구성하는 방법

1.  **점수 Vowpal Wabbit 모델** 구성 요소를 실험에 추가 합니다.  
  
2.  학습된 Vowpal Wabbit 모델을 추가하고 왼쪽 입력 포트에 연결합니다. 동일한 실험에서 만든 학습된 모델을 사용하거나 디자이너 왼쪽 탐색 창의 **데이터 세트** 범주에서 저장된 모델을 찾을 수 있습니다. 그러나 Azure Machine Learning 디자이너에서 해당 모델을 사용할 수 있어야 합니다.  
  
    > [!NOTE]
    > Vowpal Wabbit 8.8.1 모델만 지원됩니다. 다른 알고리즘을 사용하여 학습된 저장된 모델은 연결할 수 없습니다.
  
3.  테스트 데이터 세트를 추가하고 오른쪽 입력 포트에 연결합니다. 테스트 데이터 세트가 테스트 데이터 파일을 포함하는 디렉터리인 경우 **테스트 데이터 파일의 이름** 을 사용하여 테스트 데이터 파일 이름을 지정합니다. 테스트 데이터 세트가 단일 파일이면 **테스트 데이터 파일의 이름** 을 비워 둡니다.

4. **VW 인수** 텍스트 상자에 Vowpal Wabbit 실행 파일의 유효한 명령줄 인수 집합을 입력합니다.  

    Azure Machine Learning에서 지원되거나 지원되지 않는 Vowpal Wabbit 인수에 대한 자세한 내용은 [기술 참고 사항](#technical-notes) 섹션을 참조하세요.  

5.  **테스트 데이터 파일의 이름**: 입력 데이터가 포함된 파일의 이름을 입력합니다. 이 인수는 테스트 데이터 세트가 디렉터리인 경우에만 사용됩니다.

6. **파일 형식 지정**: 학습 데이터에 사용되는 형식을 나타냅니다. Vowpal Wabbit는 다음 두 가지 입력 파일 형식을 지원합니다.  

   - **VW** 는 Vowpal Wabbit에서 사용되는 내부 형식을 나타냅니다. 자세한 내용은 [Vowpal Wabbit wiki 페이지](https://github.com/JohnLangford/vowpal_wabbit/wiki/Input-format)를 참조하세요. 
   - **SVMLight** 는 다른 기계 학습 도구에서 사용되는 형식입니다. 

7. 점수와 함께 레이블을 출력하려면 **레이블이 포함된 추가 열 포함** 옵션을 선택합니다.  

   일반적으로 Vowpal Wabbit에서 텍스트 데이터를 처리할 때는 레이블이 필요 없으며 각 데이터 행의 점수만 반환됩니다.  

8. 결과와 함께 원시 점수를 출력하려면 **원시 점수가 포함된 추가 열 포함** 옵션을 선택합니다.  

9. 파이프라인을 제출합니다.

## <a name="results"></a>결과

학습 완료 후 다음이 수행됩니다.

+ 결과를 시각화 하려면 [점수 Vowpal Wabbit 모델](score-vowpal-wabbit-model.md) 구성 요소의 출력을 마우스 오른쪽 단추로 클릭 합니다. 출력은 0에서 1 사이로 정규화된 예측 점수를 나타냅니다. 

+ 결과를 평가 하려면 출력 데이터 집합에 모델 구성 요소 요구 사항 평가를 충족 하는 특정 점수 열 이름이 포함 되어야 합니다.

  + 회귀 작업의 경우 평가할 데이터 세트에는 점수가 매겨진 레이블을 나타내는 `Regression Scored Labels`라는 하나의 열이 있어야 합니다.
  + 이진 분류 작업의 경우 평가할 데이터 세트에는 각각 점수가 매겨진 레이블 및 확률을 나타내는 `Binary Class Scored Labels`, `Binary Class Scored Probabilities`라는 두 개의 열이 있어야 합니다.
  + 다중 분류 작업의 경우 평가할 데이터 세트에는 점수가 매겨진 레이블을 나타내는 `Multi Class Scored Labels`라는 하나의 열이 있어야 합니다.

  점수 Vowpal Wabbit 모델 구성 요소의 결과는 직접 평가할 수 없습니다. 평가하기 전에 위의 요구 사항에 따라 데이터 세트를 수정해야 합니다.

##  <a name="technical-notes"></a>기술 정보

이 섹션에는 구현 정보, 팁, 질문과 대답이 포함되어 있습니다.

### <a name="parameters"></a>매개 변수

Vowpal Wabbit에는 알고리즘을 선택하고 튜닝하기 위한 많은 명령줄 옵션이 있습니다. 여기서 옵션을 자세히 설명할 수는 없으므로 [Vowpal Wabbit wiki 페이지](https://github.com/JohnLangford/vowpal_wabbit/wiki/Command-line-arguments)를 참조하는 것이 좋습니다.  

다음 매개 변수는 Azure Machine Learning Studio(클래식)에서 지원되지 않습니다.  

-   [https://github.com/JohnLangford/vowpal_wabbit/wiki/Command-line-arguments](https://github.com/JohnLangford/vowpal_wabbit/wiki/Command-line-arguments)에서 지정된 입출력 옵션  
  
     이러한 속성은 구성 요소에 의해 이미 자동으로 구성 됩니다.  
  
-   또한 여러 출력을 생성하거나 여러 입력을 사용하는 옵션은 허용되지 않습니다. 여기에는 *`--cbt`* , *`--lda`* , 및 *`--wap`* 가 포함됩니다.  
  
-   감독된 학습 알고리즘만 지원됩니다. 따라서 *`–active`* , `--rank`, *`--search`* 등의 옵션은 허용되지 않습니다.  

위에서 설명한 인수 이외의 모든 인수를 사용할 수 있습니다.

## <a name="next-steps"></a>다음 단계

Azure Machine Learning 하는 데 [사용할 수 있는 구성 요소 집합](component-reference.md) 을 참조 하세요. 