---
title: Computer Vision 작업을 위한 데이터 준비
titleSuffix: Azure Machine Learning
description: 분류, 개체 검색 및 구분에 대한 컴퓨터 비전 모델을 학습하기 위한 자동화된 ML Azure Machine Learning 위한 이미지 데이터 준비
author: vadthyavath
ms.author: rvadthyavath
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.custom: template-how-to
ms.date: 10/13/2021
ms.openlocfilehash: f6a747d4d8133114d13f000fd3e1e523c86c80dd
ms.sourcegitcommit: 1244a72dbec39ac8cf16bb1799d8c46bde749d47
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/18/2021
ms.locfileid: "132756536"
---
# <a name="prepare-data-for-computer-vision-tasks-with-automated-machine-learning-preview"></a>자동화된 기계 학습을 통해 Computer Vision 작업을 위한 데이터 준비(미리 보기)

> [!IMPORTANT]
> Azure Machine Learning 자동화된 ML 통해 Computer Vision 모델 학습에 대한 지원은 실험적인 공개 미리 보기 기능입니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

이 문서에서는 [Azure Machine Learning 자동화된 기계 학습을](concept-automated-ml.md)사용하여 컴퓨터 비전 모델을 학습시키기 위해 이미지 데이터를 준비하는 방법을 알아봅니다. 

자동화된 기계 학습을 사용하여 컴퓨터 비전 작업에 대한 모델을 생성하려면 레이블이 지정되어 있는 이미지 데이터를 [Azure Machine Learning TabularDataset](/python/api/azureml-core/azureml.data.tabulardataset)형식의 모델 학습에 대한 입력으로 가져와야 합니다. 

TabularDataset에 자동화된 ML 사용할 수 있는 스키마가 포함되어 있는지 확인하려면 Azure Machine Learning 데이터 레이블 지정 도구를 사용하거나 변환 스크립트를 사용할 수 있습니다. 

## <a name="prerequisites"></a>사전 요구 사항

* [AutoML 컴퓨터 비전 실험에 대해 JSONL 파일에 허용되는 스키마를 숙지합니다.](reference-automl-images-schema.md)

* 자동화된 ML 사용하여 컴퓨터 비전 모델을 학습하는 데 사용할 레이블이 지정되는 데이터입니다.

## <a name="azure-machine-learning-data-labeling"></a>Azure Machine Learning 데이터 레이블 지정

레이블이 지정한 데이터가 없는 경우 Azure Machine Learning 데이터 레이블 지정 도구를 사용하여 이미지에 수동으로 [레이블을](how-to-create-image-labeling-projects.md) 지정할 수 있습니다. 이 도구는 학습에 필요한 데이터를 허용되는 형식으로 자동으로 생성합니다.

에 대한 데이터 레이블 지정 작업을 만들고, 관리하고, 모니터링하는 데 도움이 됩니다. 

+ 이미지 분류(다중 클래스 및 다중 레이블)
+ 개체 검색(경계 상자)
+ 인스턴스 구분(다각형)

데이터 레이블 지정 프로젝트가 이미 있고 해당 데이터를 사용하려는 경우 [레이블이 지정한 데이터를 Azure Machine Learning TabularDataset 로](how-to-create-image-labeling-projects.md#export-the-labels)내보낼 수 있습니다. 그러면 컴퓨터 비전 모델 학습을 위해 자동화된 ML 직접 사용할 수 있습니다.

## <a name="use-conversion-scripts"></a>변환 스크립트 사용

VOC 또는 COCO와 같이 인기 있는 컴퓨터 비전 데이터 형식으로 데이터에 레이블을 지정한 경우 학습 및 유효성 검사 데이터를 위한 JSONL 파일을 생성하는 [도우미 스크립트를](https://github.com/Azure/azureml-examples/blob/main/python-sdk/tutorials/automl-with-azureml/image-object-detection/coco2jsonl.py) [Notebook 예제에서](https://github.com/Azure/azureml-examples/tree/main/python-sdk/tutorials/automl-with-azureml)사용할 수 있습니다.

데이터가 앞에서 언급한 형식을 따르지 않는 경우 자체 스크립트를 사용하여 [AutoML 이미지 실험용 JSONL](reference-automl-images-schema.md)파일의 스키마에 정의된 스키마를 기반으로 JSON 줄 파일을 생성할 수 있습니다.

데이터 파일이 허용되는 JSONL 형식으로 변환된 후 Azure의 스토리지 계정에 업로드할 수 있습니다. 

## <a name="upload-the-jsonl-file-and-images-to-storage"></a>JSONL 파일 및 이미지를 스토리지에 업로드

자동화된 ML 학습에 데이터를 사용하려면 데이터 [저장소를](how-to-access-data.md)통해 [Azure Machine Learning 작업 영역에](concept-workspace.md) 데이터를 업로드합니다. 데이터 저장소는 Azure의 스토리지에 데이터를 업로드/다운로드하고 원격 컴퓨팅 대상에서 상호 작용하는 메커니즘을 제공합니다.

이미지 및 JSONL 파일로 구성된 전체 부모 디렉터리를 작업 영역을 만들 때 자동으로 생성되는 기본 데이터 저장소로 업로드.  이 데이터 저장소는 작업 영역 만들기의 일부로 생성된 기본 Azure Blob Storage 컨테이너에 연결됩니다.

```python
# Retrieve default datastore that's automatically created when we setup a workspace
ds = ws.get_default_datastore()
ds.upload(src_dir='./fridgeObjects', target_path='fridgeObjects')
```
데이터 업로드가 완료되면 [Azure Machine Learning TabularDataset을](/python/api/azureml-core/azureml.data.tabulardataset) 만들고 나중에 컴퓨터 비전 모델에 대한 자동화된 ML 실험에 대한 입력으로 사용할 수 있도록 작업 영역에 등록할 수 있습니다.

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

* [자동화된 기계 학습을 통해 컴퓨터 비전 모델을 학습합니다.](how-to-auto-train-image-models.md)
* [자동화된 기계 학습을 통해 작은 개체 감지 모델을 학습합니다.](how-to-use-automl-small-object-detect.md) 