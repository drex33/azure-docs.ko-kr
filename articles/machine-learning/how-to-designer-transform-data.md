---
title: 디자이너에서 데이터 변환
titleSuffix: Azure Machine Learning
description: Azure Machine Learning 디자이너에서 데이터 가져오고 변환하여 고유 데이터 세트를 만드는 방법을 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: mldata
author: peterclu
ms.author: peterlu
ms.date: 06/28/2020
ms.topic: how-to
ms.custom: designer
ms.openlocfilehash: 3d658421d392032e4425a3f87f8f59f9baab2ce4
ms.sourcegitcommit: f29615c9b16e46f5c7fdcd498c7f1b22f626c985
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/04/2021
ms.locfileid: "129427612"
---
# <a name="transform-data-in-azure-machine-learning-designer"></a>Azure Machine Learning 디자이너 데이터 변환


이 문서에서는 기계 학습을 위해 사용자 고유의 데이터를 준비할 수 있도록 Azure Machine Learning 디자이너에서 데이터 세트를 변환 및 저장하는 방법을 알아봅니다.

샘플 [성인 인구 조사 소득 이진 분류](./samples-designer.md) 데이터 세트를 사용하여 2개의 데이터 세트를 준비합니다. 2개 중 하나에는 미국의 성인 인구 조사 정보만 포함되고, 다른 하나에는 미국 이외의 인구 조사 정보가 포함됩니다.

이 문서에서는 다음 방법을 설명합니다.

1. 학습 준비를 위해 데이터 세트를 변환합니다.
1. 결과 데이터 세트를 데이터 저장소로 내보냅니다.
1. 결과를 확인합니다.

이 방법 안내 문서는 [디자이너 모델 재학습 방법](how-to-retrain-designer.md) 문서의 필수 조건 문서입니다. 이 문서에서는 변환된 데이터 세트를 사용하여 파이프라인 매개 변수로 여러 모델을 학습시키는 방법을 알아봅니다.

[!INCLUDE [machine-learning-missing-ui](../../includes/machine-learning-missing-ui.md)]

## <a name="transform-a-dataset"></a>데이터 세트 변환

이 섹션에서는 샘플 데이터 세트를 가져오고 데이터를 미국 및 미국 외 데이터 세트로 분할하는 방법을 알아봅니다. 사용자 고유 데이터를 디자이너로 가져오는 방법에 대한 자세한 내용은 [데이터 가져오기 방법](how-to-designer-import-data.md)을 참조하세요.

### <a name="import-data"></a>데이터 가져오기

다음 단계에 따라 샘플 데이터 세트를 가져옵니다.

1. <a href="https://ml.azure.com?tabs=jre" target="_blank">ml.azure.com</a>에 로그인하고, 사용하려는 작업 영역을 선택합니다.

1. 디자이너로 이동합니다. **사용하기 쉬운 사전 빌드 모듈** 을 선택하여 새 파이프라인을 만듭니다.

1. 파이프라인을 실행할 기본 컴퓨팅 대상을 선택하세요.

1. 파이프라인 캔버스의 왼쪽에는 데이터 세트와 모듈로 구성된 팔레트가 있습니다. **데이터 세트** 를 선택합니다. 그런 다음, **샘플** 섹션을 확인합니다.

1. **성인 인구 조사 소득 이진 분류** 데이터 세트를 캔버스로 끌어 놓습니다.

1. **성인 인구 조사 소득** 데이터 집합 모듈을 마우스 오른쪽 단추로 클릭하고 **데이터 집합 출력** **시각화** > 를 선택합니다

1. 데이터 미리 보기 창을 사용하여 데이터 세트를 살펴봅니다. "native-country" 열 값에 특히 주의하세요.

### <a name="split-the-data"></a>데이터 분할

이 섹션에서는 [데이터 분할 모듈](algorithm-module-reference/split-data.md)을 사용하여 "native-country" 열에 "United-States"가 포함된 행을 식별하고 이를 분할합니다. 

1. 캔버스 왼쪽의 모듈 팔레트에서 **데이터 변환** 섹션을 확장하고 **데이터 분할** 모듈을 찾습니다.

1. **데이터 분할** 모듈을 캔버스로 끌어가고 데이터 세트 모듈 아래에 모듈을 놓습니다.

1. 데이터 세트 모듈을 **데이터 분할** 모듈에 연결합니다.

1. **데이터** 분할 모듈을 선택합니다.

1. 캔버스 오른쪽의 모듈 세부 정보 창에서 **분할 모드** 를 **정규식** 으로 설정합니다.

1. **정규식** `\"native-country" United-States`를 입력합니다.

    이 **정규식** 모드는 특정 값으로 단일 열을 테스트합니다. 데이터 분할 모듈에 대한 자세한 내용은 관련 [알고리즘 모듈 참조 페이지](algorithm-module-reference/split-data.md)를 참조하세요.

파이프라인은 다음과 같습니다.

:::image type="content" source="./media/how-to-designer-transform-data/split-data.png" alt-text="파이프라인 및 데이터 분할 모듈 구성 방법을 보여주는 스크린샷":::


## <a name="save-the-datasets"></a>데이터 세트 저장

이제 파이프라인이 데이터를 분할하도록 설정되었으므로, 데이터 세트를 보관할 위치를 지정해야 합니다. 이 예에서는 **데이터 내보내기** 모듈을 사용하여 데이터 세트를 데이터 저장소에 저장합니다. 데이터 저장소에 대한 자세한 내용은 [Azure Storage 서비스에 연결](how-to-access-data.md)을 참조하세요.

1. 캔버스 왼쪽의 모듈 팔레트에서 **데이터 입력 및 출력** 섹션을 확장하고 **데이터 내보내기** 모듈을 찾습니다.

1. 2개의 **데이터 내보내기** 모듈을 **데이터 분할** 모듈 아래에 끌어다 놓습니다.

1. **데이터 분할** 모듈의 각 출력 포트를 서로 다른 **데이터 내보내기** 모듈에 연결합니다.

    파이프라인이 이제 다음과 같이 표시됩니다.

    ![데이터 내보내기 모듈 연결 방법을 보여주는 스크린샷](media/how-to-designer-transform-data/export-data-pipeline.png).

1. **데이터 분할** 모듈의 가장 *왼쪽* 포트에 연결된 **데이터 내보내기** 모듈을 선택합니다.

    **데이터 분할** 모듈에는 출력 포트 순서가 중요합니다. 첫 번째 출력 포트에는 정규식이 true인 행이 포함됩니다. 여기에서는 첫 번째 포트에 미국 내 소득에 대한 행이 포함되고, 두 번째 포트에는 미국 외 소득에 대한 행이 포함됩니다.

1. 캔버스 오른쪽에 있는 모듈 세부 정보 창에서 다음 옵션을 설정합니다.
    
    **데이터 저장소 유형**: Azure Blob Storage

    **데이터 저장소**: 기존 데이터 저장소를 선택하거나 "새 데이터 저장소"를 선택하여 지금 새로 만듭니다.

    **경로**: `/data/us-income`

    **파일 형식**: csv

    > [!NOTE]
    > 이 문서에서는 사용자에게 현재 Azure Machine Learning 작업 영역에 등록된 데이터 저장소에 대한 액세스 권한이 있다고 가정합니다. 데이터 저장소 설정에 대한 자세한 내용은 [Azure Storage 서비스에 연결](how-to-connect-data-ui.md#create-datastores)을 참조하세요.

    데이터 저장소가 없으면 지금 만들 수 있습니다. 예시를 보여주기 위해 이 문서에서는 작업 영역과 연결된 기본 BLOB 스토리지 계정에 데이터 세트를 저장합니다. 데이터 세트는 `data`라는 새 폴더에 있는 `azureml` 컨테이너에 저장됩니다.

1.  **데이터 분할** 모듈의 가장 *오른쪽* 포트에 연결된 **데이터 내보내기** 모듈을 선택합니다.

1. 캔버스 오른쪽에 있는 모듈 세부 정보 창에서 다음 옵션을 설정합니다.
    
    **데이터 저장소 유형**: Azure Blob Storage

    **데이터 저장소**: 위와 동일한 데이터 저장소 선택

    **경로**: `/data/non-us-income`

    **파일 형식**: csv

1. **데이터 분할** 의 왼쪽 포트에 연결된 **데이터 내보내기** 모듈에 `/data/us-income` **경로** 가 포함되었는지 확인합니다.

1. 오른쪽 포트에 연결된 **데이터 내보내기** 모듈에 `/data/non-us-income` **경로** 가 포함되었는지 확인합니다.

    파이프라인 및 설정이 다음과 같이 표시됩니다.
    
    ![데이터 내보내기 모듈 구성 방법을 보여주는 스크린샷](media/how-to-designer-transform-data/us-income-export-data.png).

### <a name="submit-the-run"></a>실행 제출

이제 데이터를 분할하고 내보내도록 파이프라인이 설정되었으므로 파이프라인 실행을 제출합니다.

1. 캔버스 위쪽에서 **제출** 을 선택합니다.

1. **파이프라인 실행 설정** 대화 상자에서 **새로 만들기** 를 선택하여 실험을 만듭니다.

    실험은 관련된 파이프라인 실행을 논리적으로 그룹으로 묶습니다. 나중에 이 파이프라인을 실행할 때는 로깅 및 추적 목적으로 동일한 실험을 사용해야 합니다.

1. "split-census-data"와 같이 실험 이름을 자세히 지정하세요.

1. **제출** 을 선택합니다.

## <a name="view-results"></a>결과 보기

파이프라인 실행이 완료된 다음에는 Azure Portal에서 BlOB 스토리지로 이동하여 결과를 확인할 수 있습니다. 또한 **데이터 분할** 모듈의 중간 결과를 검토하여 데이터가 올바르게 분할되었는지 확인할 수도 있습니다.

1. **데이터** 분할 모듈을 선택합니다.

1. 캔버스 오른쪽에 있는 모듈 세부 정보 창에서 **출력 + 로그** 를 선택합니다. 

1. **결과 데이터 세트1** 옆에 있는 시각화 아이콘 ![시각화 아이콘](media/how-to-designer-transform-data/visualize-icon.png)을 선택합니다. 

1. "native-country" 열에 값이 "United-States"만 있는지 확인합니다.

1. **결과 데이터 세트2** 옆에 있는 시각화 아이콘 ![시각화 아이콘](media/how-to-designer-transform-data/visualize-icon.png)을 선택합니다. 

1. "native-country" 열에 "United-States" 값이 없는지 확인합니다.

## <a name="clean-up-resources"></a>리소스 정리

이 방법 안내 문서의 2부, [Azure Machine Learning 디자이너로 모델 재학습](how-to-retrain-designer.md)을 계속하려면 이 섹션을 건너뛰세요.

[!INCLUDE [aml-ui-cleanup](../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>다음 단계

이 문서에서는 데이터 세트를 변환하고 등록된 데이터 저장소에 저장하는 방법을 배웠습니다.

변환된 데이터 세트 및 파이프라인 매개 변수를 사용해서 기계 학습 모델 학습을 계속하려면 이 방법 시리즈의 다음 문서인 [Azure Machine Learning 디자이너로 모델 재학습](how-to-retrain-designer.md)을 계속 진행하세요.