---
title: Vowpal Wabbit 모델 학습
titleSuffix: Azure Machine Learning
description: Vowpal Wabbit의 인스턴스를 사용 하 여 machine learning 모델을 만드는 방법에 대해 학습 Vowpal Wabbit 모델 구성 요소를 사용 하는 방법을 알아봅니다.
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 07/02/2020
ms.openlocfilehash: 09128c5d25c45d7d8a35157ae2625a451fe16ddd
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131570153"
---
# <a name="train-vowpal-wabbit-model"></a>Vowpal Wabbit 모델 학습
이 문서에서는 Azure Machine Learning designer에서 **학습 Vowpal wabbit 모델** 구성 요소를 사용 하 여 Vowpal wabbit를 사용 하 여 기계 학습 모델을 만드는 방법을 설명 합니다.  

기계 학습에 Vowpal Wabbit를 사용하려면 Vowpal Wabbit 요구 사항에 따라 입력 형식을 지정하고 필요한 형식으로 데이터를 준비합니다. 이 구성 요소를 사용 하 여 Vowpal Wabbit 명령줄 인수를 지정 합니다. 

파이프라인이 실행되면 Vowpal Wabbit의 인스턴스가 지정된 데이터와 함께 실험 런타임에 로드됩니다. 학습을 완료하면 모델은 작업 영역으로 다시 직렬화됩니다. 모델을 즉시 사용하여 데이터의 점수를 매길 수 있습니다. 

새 데이터에서 기존 모델을 점진적으로 학습하려면 **학습 Vowpal Wabbit 모델** 의 **미리 학습된 Vowpal wabbit 모델** 입력 포트에 저장된 모델을 연결하고 다른 입력 포트에 새 데이터를 추가합니다.  

## <a name="what-is-vowpal-wabbit"></a>Vowpal Wabbit 정의  

VW (Vowpal Wabbit)는 Yahoo!에서 분산 컴퓨팅을 위해 개발된 고속 병렬 기계 학습 프레임워크입니다 John Langford(Microsoft Research)가 병렬 아키텍처의 과학적 계산을 위해 조정한 고속 병렬 기계 학습 프레임워크입니다.  

기계 학습에 중요한 Vowpal Wabbit의 기능에는 지속적인 학습(온라인 학습), 차원 감소 및 대화형 학습이 포함됩니다. 또한 모델 데이터를 메모리에 맞출 수 없는 문제에 대해서도 Vowpal Wabbit을 사용할 수 있습니다.  

Vowpal Wabbit의 기본 사용자는 분류, 회귀, 토픽 모델링 또는 행렬 인수분해와 같은 기계 학습 작업에 대해 이전에 프레임 워크를 사용한 데이터 과학자입니다. Vowpal Wabbit 용 Azure 래퍼는 온-프레미스 버전과 매우 유사한 성능 특성을 제공하므로 Vowpal Wabbit의 강력한 기능 및 기본 성능을 사용하고 학습된 모델을 조작 가능한 서비스로 쉽게 게시할 수 있습니다.  

또한 [기능 해싱](feature-hashing.md) 구성 요소에는 해싱 알고리즘을 사용 하 여 텍스트 데이터 집합을 이진 기능으로 변환할 수 있는 Vowpal Wabbit에서 제공 하는 기능이 포함 되어 있습니다.  

## <a name="how-to-configure-vowpal-wabbit-model"></a>Vowpal Wabbit 모델을 구성하는 방법  

이 섹션에서는 새 모델을 학습하는 방법 및 기존 모델에 새 데이터를 추가하는 방법을 설명합니다.

디자이너의 다른 구성 요소와 달리이 구성 요소는 구성 요소 매개 변수를 지정 하 고 모델을 학습 합니다. 기존 모델이 있는 경우 선택적 입력으로 추가하여 모델을 점진적으로 학습할 수 있습니다.

+ [입력 데이터를 필요한 형식 중 하나로 준비](#prepare-the-input-data)
+ [새 모델 학습](#create-and-train-a-vowpal-wabbit-model)
+ [기존 모델을 증분식으로 학습](#retrain-an-existing-vowpal-wabbit-model)

### <a name="prepare-the-input-data"></a>입력 데이터 준비

이 구성 요소를 사용 하 여 모델을 학습 하려면 입력 데이터 집합은 **SVMLight** 또는 **VW** 의 두 가지 지원 되는 형식 중 하나에서 단일 텍스트 열로 구성 되어야 합니다. 이는 Vowpal Wabbit가 텍스트 데이터만 분석하고 필요한 텍스트 파일 형식으로 기능 및 값을 준비해야 한다는 의미는 아닙니다.  

데이터는 두 종류의 데이터 세트, 파일 데이터 세트 또는 테이블 형식 데이터 세트에서 읽을 수 있습니다. 이러한 데이터 세트는 모두 SVMLight 또는 VW 형식이어야 합니다. Vowpal Wabbit 데이터 형식을 사용하는 경우 열 형식이 필요하지 않아 스파스 데이터 사용 시 공간이 절약된다는 이점이 있습니다. 이 형식에 대한 자세한 내용은 [Vowpal Wabbit wiki 페이지](https://github.com/JohnLangford/vowpal_wabbit/wiki/Input-format)를 참조하세요.  

### <a name="create-and-train-a-vowpal-wabbit-model"></a>Vowpal Wabbit 모델 만들기 및 학습

1. 실험에 **학습 Vowpal Wabbit 모델** 구성 요소를 추가 합니다. 
  
2. 학습 데이터 세트를 추가하고 **학습 데이터** 에 연결합니다. 학습 데이터 세트가 학습 데이터 파일을 포함하는 디렉터리인 경우 **학습 데이터 파일 이름** 을 사용하여 학습데이터 파일 이름을 지정합니다. 학습 데이터 세트가 단일 파일이면 **학습 데이터 파일의 이름** 을 비워 둡니다.

3. **VW 인수** 텍스트 상자에 Vowpal Wabbit 실행 파일에 대한 명령줄 인수를 입력합니다.

     예를 들어 *`–l`* 을 추가하여 학습률을 지정하거나, *`-b`* 을 추가하여 해시 비트 수를 나타낼 수 있습니다.  

     자세한 내용은 [Vowpal Wabbit 변수](#supported-and-unsupported-parameters) 섹션을 참조하세요.  

4. **학습 데이터 파일의 이름**: 입력 데이터가 포함된 파일의 이름을 입력합니다. 이 인수는 학습 데이터 세트가 디렉터리인 경우에만 사용됩니다.

5. **파일 형식 지정**: 학습 데이터에 사용되는 형식을 나타냅니다. Vowpal Wabbit는 다음 두 가지 입력 파일 형식을 지원합니다.  

    - **VW** 는 Vowpal Wabbit에서 사용되는 내부 형식을 나타냅니다. 자세한 내용은 [Vowpal Wabbit wiki 페이지](https://github.com/JohnLangford/vowpal_wabbit/wiki/Input-format)를 참조하세요. 
    - **SVMLight** 는 다른 기계 학습 도구에서 사용되는 형식입니다. 

6. **출력 읽을 수 있는 모델 파일**: 구성 요소에서 읽을 수 있는 모델을 실행 레코드에 저장 하도록 하려면이 옵션을 선택 합니다. 이 인수는 VW 명령줄의 `--readable_model` 매개 변수에 해당합니다.  

7. **반전 된 출력 해시 파일**: 구성 요소가 반전 된 해싱 함수를 실행 레코드의 한 파일에 저장 하도록 하려면이 옵션을 선택 합니다. 이 인수는 VW 명령줄의 `--invert_hash` 매개 변수에 해당합니다.  

8. 파이프라인을 제출합니다.

### <a name="retrain-an-existing-vowpal-wabbit-model"></a>기존 Vowpal Wabbit 모델 재학습

Vowpal Wabbit는 기존 모델에 새 데이터를 추가하여 증분 학습을 지원합니다. 다음 두 가지 방법으로 재학습 위해 기존 모델을 가져올 수 있습니다.

+ 동일한 파이프라인에서 다른 **학습 Vowpal Wabbit 모델** 구성 요소의 출력을 사용 합니다.  
  
+ 디자이너의 왼쪽 탐색 창에서 **데이터 세트** 범주에 저장된 모델을 찾아 파이프라인으로 끌어 놓습니다.  

1. 파이프라인에 **학습 Vowpal Wabbit 모델** 구성 요소를 추가 합니다.  
2. 이전에 학습 된 모델을 구성 요소의 **미리 학습 된 Vowpal Wabbit 모델** 입력 포트에 커넥트 합니다.
3. 구성 요소의 **학습 데이터** 입력 포트에 새 학습 데이터를 커넥트 합니다.
4. **학습 Vowpal Wabbit 모델** 의 매개 변수 창에서 새 학습 데이터의 형식을 지정하고 입력 데이터 세트 디렉터리인 경우 학습 데이터 파일 이름도 지정합니다.
5. 실행 기록에 해당 파일을 저장해야 하는 경우 **읽을 수 있는 출력 모델 파일** 을 선택하고 **반전된 출력 해시 파일** 옵션을 선택합니다.

6. 파이프라인을 제출합니다.  
7. 구성 요소를 선택 하 고 오른쪽 창의 **출력 + 로그** 탭에서 **데이터 집합 등록** 을 선택 하 여 Azure Machine Learning 작업 영역에서 업데이트 된 모델을 유지 합니다.  새 이름을 지정하지 않은 경우 업데이트된 모델은 기존에 저장된 모델을 덮어씁니다.

## <a name="results"></a>결과

+ 모델에서 점수를 생성하려면 [Vowpal Wabbit 모델 점수](score-vowpal-wabbit-model.md)를 사용합니다.

> [!NOTE]
> 디자이너에서 학습 된 모델을 배포 해야 하는 경우 **모델 점수 매기기** 대신 [Vowpal wabbit 모델 점수가](score-vowpal-wabbit-model.md) 유추 파이프라인에서 [웹 서비스 출력 구성 요소의](web-service-input-output.md) 입력에 연결 되어 있는지 확인 합니다.

## <a name="technical-notes"></a>기술 정보

이 섹션에는 구현 세부 정보, 팁, 자주 묻는 질문에 대한 대답이 포함되어 있습니다.

### <a name="advantages-of-vowpal-wabbit"></a>Vowpal Wabbit의 장점

Vowpal Wabbit은 n그램 등의 비선형 기능에 비해 학습 속도가 매우 빠릅니다.  

Vowpal Wabbit은 SGD(추측 기울기 하강) 등의 *온라인 학습* 기술을 사용하여 한 번에 레코드 하나씩 모델을 맞춥니다. 따라서 원시 데이터에 대해 매우 빠르게 반복되며, 대부분의 다른 모델에 비해 적절한 예측자를 더 빠르게 개발할 수 있습니다. 또한 이 방식에서는 모든 학습 데이터를 메모리로 읽어 들이지 않아도 됩니다.  

Vowpal Wabbit은 모든 데이터, 즉 텍스트 데이터뿐 아니라 다른 범주 변수도 해시로 변환합니다. 해시를 사용하는 경우 회귀 가중치 조회 효율성이 높아집니다. 효율적인 SGD에서는 이 조회의 효율성이 높아야 합니다.  

###  <a name="supported-and-unsupported-parameters"></a>지원되는/지원되지 않는 매개 변수 

이 섹션에서는 Azure Machine Learning 디자이너의 Vowpal Wabbit 명령줄 매개 변수에 대한 지원에 대해 설명합니다. 

일반적으로 제한된 인수 집합만 지원합니다. 전체 인수 목록은 [ Vowpal Wabbit wiki 페이지](https://github.com/JohnLangford/vowpal_wabbit/wiki/Command-line-arguments)를 사용하세요.    

지원되지 않는 매개 변수는 다음과 같습니다.

-   [https://github.com/JohnLangford/vowpal_wabbit/wiki/Command-line-arguments](https://github.com/JohnLangford/vowpal_wabbit/wiki/Command-line-arguments)에서 지정된 입출력 옵션  
  
     이러한 속성은 구성 요소에 의해 이미 자동으로 구성 됩니다.  
  
-   또한 여러 출력을 생성하거나 여러 입력을 사용하는 옵션은 허용되지 않습니다. 여기에는 *`--cbt`* , *`--lda`* , 및 *`--wap`* 가 포함됩니다.  
  
-   감독된 학습 알고리즘만 지원됩니다. 따라서, *`–active`* , `--rank`, *`--search`* 등의 옵션은 지원되지 않습니다. 

### <a name="restrictions"></a>제한

서비스의 목표가 숙련 된 Vowpal Wabbit 사용자를 지원 하기 때문에 입력 데이터는 다른 구성 요소에서 사용 하는 데이터 집합 형식이 아니라 Vowpal Wabbit 네이티브 텍스트 형식을 사용 하 여 미리 준비 해야 합니다.

## <a name="next-steps"></a>다음 단계

Azure Machine Learning 하는 데 [사용할 수 있는 구성 요소 집합](component-reference.md) 을 참조 하세요. 
