---
title: '열 추가: 구성 요소 참조'
titleSuffix: Azure Machine Learning
description: 끌어서 놓기 Azure Machine Learning 디자이너에서 열 추가 구성 요소를 사용 하 여 두 데이터 집합을 연결 하는 방법을 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/22/2019
ms.openlocfilehash: 9ee1a1440e239e95417528503a2d00db1ed4a8d1
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131570077"
---
# <a name="add-columns-component"></a>열 추가 구성 요소

이 문서에서는 Azure Machine Learning 디자이너의 구성 요소에 대해 설명 합니다.

이 구성 요소를 사용 하 여 두 데이터 집합을 연결 합니다. 입력으로 지정한 두 데이터 집합의 모든 열을 결합하여 단일 데이터 집합을 만듭니다. 두 개 이상의 데이터 집합을 사용해야 하는 경우 **열 추가** 의 여러 인스턴스를 사용합니다.



## <a name="how-to-configure-add-columns"></a>열 추가 구성 방법
1. 파이프라인에 **열 추가** 구성 요소를 추가 합니다.

2. 연결하려는 두 데이터 집합을 연결합니다. 두 개 이상의 데이터 집합을 결합하려는 경우 **열 추가** 의 여러 조합을 함께 연결하면 됩니다.

    - 행 수가 다른 두 열을 결합할 수 있습니다. 출력 데이터 집합은 작은 원본 열의 각 행에 대해 누락된 값으로 패딩됩니다.

    - 추가할 개별 열을 선택할 수는 없습니다. **열 추가** 를 사용하면 각 데이터 집합의 모든 열이 서로 결합됩니다. 따라서 열의 하위 집합만 추가하려면 데이터 집합에서 열 선택을 사용하여 원하는 열이 있는 데이터 집합을 만듭니다.

3. 파이프라인을 제출합니다.

### <a name="results"></a>결과
파이프라인이 실행된 후:

- 새 데이터 집합의 첫 번째 행을 보려면 **열 추가** 구성 요소를 마우스 오른쪽 단추로 클릭 하 고 시각화를 선택 합니다. 또는 구성 요소를 선택 하 고 오른쪽 패널의 **출력** 탭으로 전환한 다음 **포트 출력** 의 히스토그램 아이콘을 클릭 하 여 결과를 시각화 합니다.

새 데이터 집합의 열 수는 두 입력 데이터 집합의 열 합과 같습니다.

입력 데이터 집합에 이름이 같은 두 개의 열이 있는 경우 숫자 접미사가 열 이름에 추가됩니다. 예를 들어 TargetOutcome이라는 열의 인스턴스가 두 개 있는 경우 왼쪽 열의 이름이 TargetOutcome_1, 이름이 바뀐 오른쪽 열의 이름이 TargetOutcome_2가 됩니다.

## <a name="next-steps"></a>다음 단계

Azure Machine Learning 하는 데 [사용할 수 있는 구성 요소 집합](component-reference.md) 을 참조 하세요. 