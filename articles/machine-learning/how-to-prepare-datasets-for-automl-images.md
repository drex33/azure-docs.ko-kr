---
title: 컴퓨터 비전 작업을 위한 데이터 준비
titleSuffix: Azure Machine Learning
description: 분류, 개체 검색 및 구분에 대 한 컴퓨터 비전 모델을 학습 하 Azure Machine Learning 자동화 된 ML에 대 한 이미지 데이터 준비
author: vadthyavath
ms.author: rvadthyavath
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.custom: template-how-to
ms.date: 10/13/2021
ms.openlocfilehash: 7f2f3cf3c0af623c6c33e46fe9735a2af03c14c6
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/14/2021
ms.locfileid: "130008702"
---
# <a name="prepare-data-for-computer-vision-tasks-with-automated-machine-learning-preview"></a>자동화 된 machine learning (미리 보기)을 사용 하 여 컴퓨터 비전 작업을 위한 데이터 준비

> [!IMPORTANT]
> Azure Machine Learning에서 자동화 된 ML를 사용 하 여 컴퓨터 비전 모델 학습에 대 한 지원은 실험적 공개 미리 보기 기능입니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

이 문서에서는 [Azure Machine Learning에서 자동화 된 machine learning](concept-automated-ml.md)을 사용 하 여 컴퓨터 비전 모델 학습을 위한 이미지 데이터를 준비 하는 방법에 대해 알아봅니다. 

자동화 된 machine learning을 사용 하 여 컴퓨터 비전 작업을 위한 모델을 생성 하려면 레이블 이미지 데이터를 모델 학습의 입력으로 [Azure Machine Learning TabularDataset](/python/api/azureml-core/azureml.data.tabulardataset)형식으로 가져와야 합니다. 

자동 ML에서 사용 하기 위해 TabularDataset에 허용 된 스키마가 포함 되어 있는지 확인 하기 위해 Azure Machine Learning 데이터 레이블 도구를 사용 하거나 변환 스크립트를 사용할 수 있습니다. 

## <a name="prerequisites"></a>사전 요구 사항

* [AutoML 컴퓨터 비전 실험을 위해 JSONL 파일에 대해](reference-automl-images-schema.md)허용 되는 스키마를 숙지 합니다.

* 자동화 된 ML를 사용 하 여 컴퓨터 비전 모델을 학습 하는 데 사용 하려는 레이블이 지정 된 데이터입니다.

## <a name="azure-machine-learning-data-labeling"></a>Azure Machine Learning 데이터 레이블 지정

레이블이 지정 된 데이터가 없는 경우 Azure Machine Learning의 [데이터 레이블 도구](how-to-create-image-labeling-projects.md) 를 사용 하 여 이미지에 수동으로 레이블을 지정할 수 있습니다. 이 도구는 허용 되는 형식으로 학습에 필요한 데이터를 자동으로 생성 합니다.

다음에 대 한 데이터 레이블 작업을 만들고, 관리 하 고, 모니터링 하는 데 도움이 됩니다. 

+ 이미지 분류 (다중 클래스 및 다중 레이블)
+ 개체 검색 (경계 상자)
+ 인스턴스 구분 (다각형)

데이터 레이블 프로젝트가 이미 있는 상태에서 해당 데이터를 사용 하려는 경우 [레이블이 지정 된 데이터를 Azure Machine Learning TabularDataset으로 내보낼](how-to-create-image-labeling-projects.md#export-the-labels)수 있습니다 .이 데이터는 자동 ML에서 컴퓨터 비전 모델 학습에 직접 사용할 수 있습니다.

## <a name="use-conversion-scripts"></a>변환 스크립트 사용

VOC 또는 COCO와 같이 널리 사용 되는 컴퓨터 비전 데이터 형식으로 데이터 레이블을 지정한 경우 학습 및 유효성 검사 데이터에 대 한 JSONL 파일을 생성 하는 스크립트를 제공 합니다. 자세한 지침 및 스크립트는 [노트북](https://github.com/Azure/azureml-examples/tree/main/python-sdk/tutorials/automl-with-azureml) 을 참조 하세요.

데이터가 앞에서 언급 한 형식 중 하나를 따르지 않는 경우 [AutoML 이미지 실험의 JSONL 파일용 스키마](reference-automl-images-schema.md)에 정의 된 스키마에 따라 사용자 고유의 스크립트를 사용 하 여 JSON 줄 파일을 생성할 수 있습니다.

데이터 파일이 수락 된 JSONL 형식으로 변환 된 후 Azure의 저장소 계정에 업로드할 수 있습니다. 

## <a name="upload-the-jsonl-file-and-images-to-storage"></a>저장소에 JSONL 파일 및 이미지를 업로드 합니다.

자동화 된 ML 학습에 데이터를 사용 하려면 데이터 [저장소](how-to-access-data.md)를 통해 [Azure Machine Learning 작업 영역](concept-workspace.md) 에 데이터를 업로드 합니다. 데이터 저장소는 Azure의 저장소에 데이터를 업로드/다운로드 하 고 원격 계산 대상에서 상호 작용할 수 있는 메커니즘을 제공 합니다.

이미지 및 JSONL 파일을 구성 하는 전체 부모 디렉터리를 작업 영역을 만들 때 자동으로 생성 되는 기본 데이터 저장소로 업로드 합니다.  이 데이터 저장소는 작업 영역을 만들 때 생성 된 기본 Azure blob storage 컨테이너에 연결 됩니다.

```python
# Retrieve default datastore that's automatically created when we setup a workspace
ds = ws.get_default_datastore()
ds.upload(src_dir='./fridgeObjects', target_path='fridgeObjects')
```
데이터 업로드가 완료 되 면 [Azure Machine Learning TabularDataset](/python/api/azureml-core/azureml.data.tabulardataset) 를 만든 후 나중에 컴퓨터 비전 모델에 대 한 자동화 된 ML 실험의 입력으로 사용할 수 있도록 작업 영역에 등록할 수 있습니다.

```python
from azureml.core import Dataset
from azureml.data import DataType

training_dataset_name = 'fridgeObjectsTrainingDataset'
# create training dataset
training_dataset = Dataset.Tabular.from_json_lines_files(path=ds.path("fridgeObjects/train_annotations.jsonl"),
                                                         set_column_types={"image_url": DataType.to_stream(ds.workspace)}
                                                        )
training_dataset = training_dataset.register( workspace=ws,name=training_dataset_name)

print("Training dataset name: " + training_dataset.name)
```

## <a name="next-steps"></a>다음 단계

* [자동화 된 machine learning을 사용 하 여 컴퓨터 비전 모델을 학습](how-to-auto-train-image-models.md)합니다.
* [자동화 된 machine learning을 사용 하 여 작은 개체 검색 모델을 학습](how-to-use-automl-small-object-detect.md)합니다. 