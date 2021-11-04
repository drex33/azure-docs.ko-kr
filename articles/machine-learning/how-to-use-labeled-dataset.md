---
title: 레이블이 있는 데이터 세트 만들기 및 탐색
titleSuffix: Azure Machine Learning
description: Azure Machine Learning 레이블 지정 프로젝트에서 데이터 레이블을 내보내고 이를 기계 학습 작업에 사용하는 방법에 대해 알아봅니다.
author: nibaccam
ms.author: nibaccam
ms.service: machine-learning
ms.subservice: mldata
ms.topic: how-to
ms.custom: data4ml
ms.date: 10/21/2021
ms.openlocfilehash: 53abb99aef3a04f3c975cd0fbde4d1097e01269b
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131560320"
---
# <a name="create-and-explore-azure-machine-learning-dataset-with-labels"></a>레이블이 있는 Azure Machine Learning 데이터 세트 만들기 및 탐색

이 문서에서는 Azure Machine Learning 데이터 레이블 지정 프로젝트에서 데이터 레이블을 내보내고 데이터 탐색을 위한 pandas 데이터 프레임 또는 이미지 변환을 위한 Torchvision 데이터 세트와 같은 인기 있는 형식으로 로드하는 방법을 알아봅니다. 

## <a name="what-are-datasets-with-labels"></a>레이블이 있는 데이터 세트란? 

레이블이 있는 Azure Machine Learning 데이터 세트를 레이블이 지정된 데이터 세트라고 부릅니다. 해당 특정 데이터 세트 형식의 레이블이 지정된 데이터 세트는 Azure Machine Learning 데이터 레이블 지정 프로젝트의 출력으로만 생성됩니다. 이미지 레이블 지정 또는 텍스트 [레이블 지정을 위한](how-to-create-image-labeling-projects.md) 데이터 레이블 지정 프로젝트를 [만듭니다.](how-to-create-text-labeling-projects.md) Machine Learning은 이미지 분류(다중 레이블 또는 다중 클래스) 및 경계 상자와 함께 개체 식별을 위한 데이터 레이블 지정 프로젝트를 지원합니다.

## <a name="prerequisites"></a>사전 요구 사항

* Azure 구독 Azure 구독이 아직 없는 경우 시작하기 전에 [무료 계정](https://azure.microsoft.com/free/)을 만듭니다.
* [Python용 Azure Machine Learning SDK](/python/api/overview/azure/ml/intro) 또는 [Azure Machine Learning 스튜디오](https://ml.azure.com/)에 액세스합니다.
    * [azure-contrib-dataset](/python/api/azureml-contrib-dataset/) 패키지 설치
* Machine Learning 작업 영역. [Azure Machine Learning 작업 영역 만들기](how-to-manage-workspace.md)를 참조하세요.
* Azure Machine Learning 데이터 레이블 지정 프로젝트에 액세스합니다. 레이블 지정 프로젝트가 없는 경우 먼저 이미지 레이블 지정 또는 [텍스트 레이블](how-to-create-text-labeling-projects.md) [지정을](how-to-create-image-labeling-projects.md) 위한 프로젝트를 만듭니다.

## <a name="export-data-labels"></a>데이터 레이블 내보내기 

데이터 레이블 지정 프로젝트를 완료하면 레이블 지정 프로젝트에서 레이블 데이터를 내보낼 수 있습니다. 이렇게 하면 데이터와 해당 레이블에 대한 참조를 모두 캡처하여 [COCO 형식](http://cocodataset.org/#format-data) 또는 Azure Machine Learning 데이터 세트로 내보낼 수 있습니다. 레이블 지정 프로젝트의 **프로젝트 세부 정보** 페이지에서 **내보내기** 단추를 사용합니다.

### <a name="coco"></a>COCO 

 COCO 파일은 *export/coco* 내의 폴더에 있는 Azure Machine Learning 작업 영역의 기본 Blob 저장소에 만들어집니다. 
 
>[!NOTE]
>개체 감지 프로젝트에서 COCO 파일의 내보낸 “bbox”: [x, y, width, height] "값은 정규화되며 1로 스케일링됩니다. 예: 640x480 픽셀 이미지에서 (10, 10) 위치에 있는 너비가 30 픽셀, 높이가 60 픽셀인 경계 상자는 (0.015625. 0.02083, 0.046875, 0.125)로 주석이 추가됩니다. 좌표가 정규화되므로 모든 이미지에 대해 ‘0.0’을 “너비” 및 “높이”로 표시합니다. 실제 너비와 높이는 OpenCV 또는 Pillow(PIL)와 같은 Python 라이브러리를 사용하여 가져올 수 있습니다.

### <a name="azure-machine-learning-dataset"></a>Azure Machine Learning 데이터 세트

Azure Machine Learning 스튜디오의 **데이터 세트** 섹션에서 내보낸 Azure Machine Learning 데이터 세트에 액세스할 수 있습니다. 데이터 세트 **세부 정보** 페이지에서는 Python에서 레이블에 액세스할 수 있는 샘플 코드도 제공합니다.

![내보낸 데이터 세트](./media/how-to-create-labeling-projects/exported-dataset.png)

레이블이 지정한 데이터를 Azure Machine Learning 데이터 세트로 내보낸 후에는 AutoML을 사용하여 레이블이 지정한 데이터에 대해 학습된 컴퓨터 비전 모델을 빌드할 수 있습니다. [Python을 사용하여 컴퓨터 비전 모델을 학습하도록 AutoML 설정(미리 보기)에서](how-to-auto-train-image-models.md) 자세히 알아보세요.

## <a name="explore-labeled-datasets"></a>레이블이 지정된 데이터 세트 탐색

레이블이 지정된 데이터 세트를 pandas 데이터 프레임 또는 Torchvision 데이터 세트에 로드하여 데이터 탐색에 널리 사용되는 오픈 소스 라이브러리를 활용하고, PyTorch는 이미지 변환 및 학습을 위한 라이브러리를 제공합니다.

### <a name="pandas-dataframe"></a>pandas 데이터 프레임

`azureml-contrib-dataset` 클래스에서 [`to_pandas_dataframe()`](/python/api/azureml-core/azureml.data.tabulardataset#to-pandas-dataframe-on-error--null---out-of-range-datetime--null--) 메서드를 사용하여 레이블이 지정된 데이터 세트를 pandas 데이터 프레임에 로드할 수 있습니다. 다음 shell 명령을 사용하여 클래스를 설치합니다. 

```shell
pip install azureml-contrib-dataset
```

>[!NOTE]
>서비스 개선을 위해 노력하므로 azureml.contrib 네임스페이스는 자주 변경됩니다. 따라서 이 네임 스페이스의 모든 것을 미리 보기로 간주하므로 Microsoft에서 완벽히 지원하지 않아도 됩니다.

Azure Machine Learning은 pandas 데이터 프레임으로 변환할 때 파일 스트림에 대해 다음과 같은 파일 처리 옵션을 제공합니다.
* 다운로드: 데이터 파일을 로컬 경로로 다운로드합니다.
* 탑재: 탑재 지점에 데이터 파일을 탑재합니다. 탑재는 Azure Machine Learning Notebook VM 및 Azure Machine Learning 컴퓨팅을 포함한 Linux 기반 컴퓨팅에서만 작동합니다.

다음 코드에서 `animal_labels` 데이터 세트는 이전에 작업 영역에 저장된 레이블 지정 프로젝트의 출력입니다.

```Python
import azureml.core
import azureml.contrib.dataset
from azureml.core import Dataset, Workspace
from azureml.contrib.dataset import FileHandlingOption

# get animal_labels dataset from the workspace
animal_labels = Dataset.get_by_name(workspace, 'animal_labels')
animal_pd = animal_labels.to_pandas_dataframe(file_handling_option=FileHandlingOption.DOWNLOAD, target_path='./download/', overwrite_download=True)

import matplotlib.pyplot as plt
import matplotlib.image as mpimg

#read images from downloaded path
img = mpimg.imread(animal_pd.loc[0,'image_url'])
imgplot = plt.imshow(img)
```

### <a name="torchvision-datasets"></a>Torchvision 데이터 세트

`azureml-contrib-dataset` 클래스에서도 [to_torchvision()](/python/api/azureml-contrib-dataset/azureml.contrib.dataset.tabulardataset#to-torchvision--) 메서드를 사용하여 레이블이 지정된 데이터 세트를 Torchvision 데이터 세트에 로드할 수 있습니다. 이 방법을 사용하려면 [PyTorch](https://pytorch.org/)가 설치되어 있어야 합니다. 

다음 코드에서 `animal_labels` 데이터 세트는 이전에 작업 영역에 저장된 레이블 지정 프로젝트의 출력입니다.

```python
import azureml.core
import azureml.contrib.dataset
from azureml.core import Dataset, Workspace
from azureml.contrib.dataset import FileHandlingOption

from torchvision.transforms import functional as F

# get animal_labels dataset from the workspace
animal_labels = Dataset.get_by_name(workspace, 'animal_labels')

# load animal_labels dataset into torchvision dataset
pytorch_dataset = animal_labels.to_torchvision()
img = pytorch_dataset[0][0]
print(type(img))

# use methods from torchvision to transform the img into grayscale
pil_image = F.to_pil_image(img)
gray_image = F.to_grayscale(pil_image, num_output_channels=3)

imgplot = plt.imshow(gray_image)
```

## <a name="next-steps"></a>다음 단계
