---
title: 이미지 디렉터리 분할
titleSuffix: Azure Machine Learning
description: 디자이너에서 이미지 디렉터리 모듈을 사용하여 이미지 디렉터리의 이미지를 두 개의 고유 집합으로 나누는 방법에 대해 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 09/26/2020
ms.openlocfilehash: 3ee4dd9b2e344ecb3e1a6424ce7310270e7cd076
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "93421195"
---
# <a name="split-image-directory"></a>이미지 디렉터리 분할

이 토픽에서는 Azure Machine Learning 디자이너에서 이미지 분할 모듈을 사용하여 이미지 디렉터리의 이미지를 두 개의 고유 집합으로 나누는 방법에 대해 설명합니다.

이 모듈은 이미지 데이터를 학습 및 테스트 집합으로 분리해야 하는 경우에 특히 유용합니다. 

## <a name="how-to-configure-split-image-directory"></a>분할 이미지 디렉터리를 구성하는 방법

1. 파이프라인에 **분할 이미지 디렉터리** 모듈을 추가합니다. ‘Computer Vision/이미지 데이터 변환’ 범주에서 이 모듈을 찾을 수 있습니다.

2. 출력이 이미지 디렉터리인 모듈에 연결합니다.

3. **첫 번째 출력에서 이미지의 비율을** 입력하여 왼쪽 분할에 포함할 데이터의 비율을 지정합니다. 기본값은 0.9입니다. 분수 결과가 정수가 아닌 경우 모듈은 가까운 작은 정수를 사용합니다.


## <a name="technical-notes"></a>기술 정보

### <a name="expected-inputs"></a>예상 입력

| 이름                  | 유형           | 설명              |
| --------------------- | -------------- | ------------------------ |
| 이미지 디렉터리 입력 | ImageDirectory | 분할할 이미지 디렉터리입니다 |

### <a name="module-parameters"></a>모듈 매개 변수

| 이름                                   | Type  | 범위 | 옵션 | Description                            | 기본값 |
| -------------------------------------- | ----- | ----- | -------- | -------------------------------------- | ------- |
| 첫 번째 출력에서 이미지의 비율 | Float | 0-1   | 필수 | 첫 번째 출력에서 이미지의 비율 | 0.9     |

### <a name="outputs"></a>출력

| 이름                    | 유형           | 설명                              |
| ----------------------- | -------------- | ---------------------------------------- |
| 출력 이미지 디렉터리1 | ImageDirectory | 선택한 이미지를 포함하는 이미지 디렉터리 |
| 출력 이미지 디렉터리2 | ImageDirectory | 다른 모든 이미지를 포함하는 이미지 디렉터리 |

## <a name="next-steps"></a>다음 단계

Azure Machine Learning에서 [사용 가능한 모듈 세트](module-reference.md)를 참조하세요. 

