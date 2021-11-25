---
title: '빠른 시작: Synapse Analytics 갤러리의 샘플 Notebook 사용'
description: Synapse Analytics 갤러리의 샘플 Notebook을 사용하여 데이터를 살펴보고 기계 학습 모델을 빌드하는 방법을 알아봅니다.
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: machine-learning
ms.topic: quickstart
ms.reviewer: ''
ms.date: 06/11/2021
author: garyericson
ms.author: garye
ms.custom: mode-other
ms.openlocfilehash: 7cddb9bb7ccfe4f0e56e13884c4f805123bff072
ms.sourcegitcommit: 56235f8694cc5f88db3afcc8c27ce769ecf455b0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133041041"
---
# <a name="quickstart-use-a-sample-notebook-from-the-synapse-analytics-gallery"></a>빠른 시작: Synapse Analytics 갤러리의 샘플 Notebook 사용

이 빠른 시작에서는 Synapse Analytics 갤러리의 샘플 기계 학습 Notebook을 작업 영역으로 복사하고, 수정하고, 실행하는 방법을 알아봅니다.
샘플 Notebook은 NYC 택시 이동의 개방형 데이터 세트를 수집하고 시각화를 사용하여 데이터를 준비합니다. 그런 다음, 모델을 학습시켜 지정된 이동에 대한 팁이 있는지 여부를 예측합니다.

이 Notebook에서는 모델을 만드는 데 사용되는 기본 단계인 **데이터 가져오기**, **데이터 준비**, **모델 학습** 및 **평가** 를 보여 줍니다. 이 샘플을 사용자 고유의 데이터로 모델을 만들기 위한 시작점으로 사용할 수 있습니다.

## <a name="prerequisites"></a>필수 구성 요소

* [Azure Synapse Analytics 작업 영역](../get-started-create-workspace.md)(기본 스토리지로 구성된 Azure Data Lake Storage Gen2 스토리지 계정이 있음). 사용하는 Data Lake Storage Gen2 파일 시스템의 *Storage Blob 데이터 기여자* 여야 합니다.
* Azure Synapse Analytics 작업 영역의 [Spark 풀](../get-started-analyze-spark.md)

## <a name="copy-the-notebook-to-your-workspace"></a>작업 영역에 Notebook 복사

1. 작업 영역을 열고 홈페이지에서 **학습** 을 선택합니다.
1. **지식 센터** 에서 **갤러리 찾아보기** 를 선택합니다.
1. 갤러리에서 **Notebook** 을 선택합니다.
1. "데이터 살펴보기 및 ML 모델링 - Spark MLib를 사용하여 NYC 택시 예측" Notebook을 찾아 선택합니다.

   :::image type="content" source="media\quickstart-gallery-sample-notebook\gallery-select-ml-notebook.png" alt-text="갤러리에서 기계 학습 샘플 Notebook을 선택합니다.":::

1. **계속** 을 선택합니다.
1. Notebook 미리 보기 페이지에서 **Notebook 열기** 를 선택합니다. 샘플 Notebook이 작업 영역에 복사되어 열립니다.

    :::image type="content" source="media\quickstart-gallery-sample-notebook\gallery-open-ml-notebook.png" alt-text="기계 학습 샘플 Notebook을 작업 영역에서 엽니다.":::

1. 열린 Notebook의 **연결 대상** 메뉴에서 Apache Spark 풀을 선택합니다.

## <a name="run-the-notebook"></a>노트북 실행

이 Notebook은 각각 특정 기능을 수행하는 여러 셀로 나뉩니다.
각 셀을 수동으로 실행하고 셀을 순차적으로 실행하거나 **모두 실행** 을 선택하여 모든 셀을 실행할 수 있습니다.

Notebook의 각 셀에 대한 설명은 다음과 같습니다.

1. Notebook에서 사용하는 PySpark 함수를 가져옵니다.
1. **데이터 수집** - 처리를 위해 Azure Open Dataset **NycTlcYellow** 의 데이터를 로컬 데이터 프레임으로 수집합니다. 이 코드는 특정 기간 내의 데이터를 추출합니다. 시작 및 종료 날짜를 수정하여 다른 데이터를 얻을 수 있습니다.
1. 더 빠른 개발을 위해 데이터 세트를 다운 샘플링합니다. 이 단계를 수정하여 샘플 크기 또는 샘플링 초기값을 변경할 수 있습니다.
1. **예비 데이터 분석** - 차트를 표시하여 데이터를 확인합니다. 이렇게 하면 모델을 만들기 전에 필요한 데이터 준비를 파악할 수 있습니다.
1. **데이터 준비 및 기능화** - 시각화를 통해 검색된 이상값 데이터를 필터링하고 몇 가지 유용한 파생 변수를 만듭니다.
1. **데이터 준비 및 기능화 2부** - 원치 않는 열을 삭제하고 몇 가지 추가 기능을 만듭니다.
1. **인코딩** - 문자열 변수를 로지스틱 회귀 모델에 필요한 숫자로 변환합니다.
1. **테스트 및 학습 데이터 세트 생성** - 데이터를 별도의 테스트 및 학습 데이터 세트로 분할합니다. 데이터를 분할하는 데 사용되는 비율 및 무작위 초기값을 수정할 수 있습니다.
1. **모델 학습** - 로지스틱 회귀 모델을 학습시키고 "Area under ROC" 메트릭을 표시하여 모델이 얼마나 잘 작동하는지 확인합니다. 또한 다른 위치에서 사용하려는 경우 이 단계에서 학습된 모델을 저장합니다.
1. **평가 및 시각화** - 모델의 ROC 곡선을 그려 추가로 평가합니다.

## <a name="save-the-notebook"></a>Notebook 저장

작업 영역 명령 모음에서 **게시** 를 선택하여 Notebook을 저장합니다.

## <a name="copying-the-sample-notebook"></a>샘플 Notebook 복사

이 Notebook의 복사본을 만들려면 위쪽 명령 모음에서 줄임표를 클릭하고 **복제** 를 선택하여 작업 영역에 복사본을 만들거나 **내보내기** 를 선택하여 Notebook(`.ipynb`) 파일의 복사본을 다운로드합니다.

:::image type="content" source="media\quickstart-gallery-sample-notebook\copy-notebook.png" alt-text="내보내기 또는 복제 명령을 통해 Notebook의 복사본을 만듭니다.":::

## <a name="clean-up-resources"></a>리소스 정리

작업이 끝날 때 Spark 인스턴스가 종료되도록 하려면 연결된 세션(Notebook)을 종료합니다. Apache Spark 풀에 지정된 **유휴 시간** 에 도달하면 풀이 종료됩니다. Notebook 오른쪽 상단에 있는 상태 표시줄에서 **세션 중지** 를 선택할 수도 있습니다.

:::image type="content" source="media\quickstart-gallery-sample-notebook\stop-session.png" alt-text="세션을 중지합니다.":::

## <a name="next-steps"></a>다음 단계

* [GitHub에서 추가 Synapse 샘플 Notebook 확인](https://github.com/Azure-Samples/Synapse/tree/main/MachineLearning)
* [Apache Spark를 사용한 기계 학습](../spark/apache-spark-machine-learning-concept.md)
