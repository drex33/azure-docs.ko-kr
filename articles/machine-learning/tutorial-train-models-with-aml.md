---
title: '자습서: 예제 Jupyter Notebook 학습'
titleSuffix: Azure Machine Learning
description: 클라우드 기반 Python Jupyter Notebook에서 Azure Machine Learning을 사용하여 scikit-learn으로 이미지 분류 모델을 학습합니다. 이 자습서는 2의 1부입니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: sdgilley
ms.author: sgilley
ms.date: 10/21/2021
ms.custom: seodec18, devx-track-python, FY21Q4-aml-seo-hack, contperf-fy21q4
ms.openlocfilehash: 4f29290c96b5f9603b4d626a8f87ee1c75168abc
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132289721"
---
# <a name="tutorial-train-an-image-classification-model-with-an-example-jupyter-notebook"></a>자습서: Jupyter Notebook 예제를 통해 이미지 분류 모델 학습 

이 자습서에서는 원격 컴퓨팅 리소스에 대해 기계 학습 모델을 학습합니다. Python Jupyter Notebook에서 Azure Machine Learning에 대한 학습 및 배포 워크플로를 사용합니다.  그런 다음, 이 노트를 템플릿으로 사용하여 자신의 데이터로 고유한 Machine Learning 모델을 학습할 수 있습니다. 이 자습서는 **2부로 구성된 자습서 시리즈 중 제1부** 입니다.  

이 자습서에서는 Azure Machine Learning과 함께 [MNIST](http://yann.lecun.com/exdb/mnist/) 데이터 세트 및 [scikit-learn](https://scikit-learn.org)을 사용하여 간단한 로지스틱 회귀 분석을 학습합니다. MNIST는 70,000개의 회색조 이미지로 구성된 인기 있는 데이터 세트입니다. 각 이미지는 0-9의 숫자를 나타내는 28x28 픽셀의 필기체 숫자입니다. 목표는 지정된 이미지가 나타내는 숫자를 식별하기 위한 다중 클래스 분류자를 만드는 것입니다.

다음 작업을 수행하는 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * 개발 환경 설정
> * 데이터 액세스 및 검사
> * 원격 클러스터의 간단한 로지스틱 회귀 모델을 학습합니다.
> * 학습 결과 검토 및 최상의 모델 등록

모델을 선택하고 배포하는 방법은 이 [자습서의 제2부](tutorial-deploy-models-with-aml.md)에서 알아봅니다.

Azure 구독이 없는 경우 시작하기 전에 체험 계정을 만듭니다. 지금 [Azure Machine Learning 평가판 또는 유료 버전](https://azure.microsoft.com/free/)을 사용해 보세요.

> [!NOTE]
> 이 문서의 코드는 [Azure Machine Learning SDK](/python/api/overview/azure/ml/intro) 버전 1.13.0에서 테스트되었습니다.

## <a name="prerequisites"></a>필수 구성 요소

* [빠른 시작: Azure Machine Learning 시작](quickstart-create-resources.md)을 완료하여 다음을 수행합니다.
    * 작업 영역을 만듭니다.
    * 개발 환경에 사용할 클라우드 기반 컴퓨팅 인스턴스를 만듭니다.
    * 모델 학습에 사용할 클라우드 기반 컴퓨팅 클러스터를 만듭니다.

## <a name="run-a-notebook-from-your-workspace"></a><a name="azure"></a>작업 영역에서 Notebook 실행

Azure Machine Learning의 작업 영역에는 설치할 필요가 없는 미리 구성된 환경을 위해 클라우드 Notebook 서버가 포함됩니다. 환경, 패키지 및 종속성을 제어하려면 [사용자 고유의 환경](how-to-configure-environment.md#local)을 사용합니다.

 다음 비디오를 따르거나 자세한 단계를 사용하여 작업 영역에서 자습서 Notebook을 복제하고 실행합니다.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4mTUr]

> [!NOTE]
> 비디오는 프로세스를 이해하는 데 도움이 되지만 다른 파일을 여는 것을 보여 줍니다.  이 자습서에서는 **tutorials** 폴더를 복제하면 **tutorials/image-classification-mnist-data** 폴더에서 **img-classification-part1-training.ipynb** 파일을 엽니다.

### <a name="clone-a-notebook-folder"></a><a name="clone"></a> Notebook 폴더 복제

Azure Machine Learning 스튜디오에서 다음 실험 설정 및 실행 단계를 완료합니다. 이 통합 인터페이스에는 모든 기술 수준의 데이터 과학 전문가를 위한 데이터 과학 시나리오를 수행하는 기계 학습 도구가 포함되어 있습니다.

1. [Azure Machine Learning Studio](https://ml.azure.com/)에 로그인합니다.

1. 해당 구독과 직접 만든 작업 영역을 선택합니다.

1. 왼쪽에서 **Notebook** 을 선택합니다.

1. 위쪽에서 **샘플** 탭을 선택합니다.

1. **Python** 폴더를 엽니다.

1. 버전 번호가 있는 폴더를 엽니다. 이 번호는 Python SDK의 현재 릴리스를 나타냅니다.

1. **tutorials** 폴더의 오른쪽에 있는 **...** 단추를 선택한 다음, **복제** 를 선택합니다.

    :::image type="content" source="media/tutorial-1st-experiment-sdk-setup/clone-tutorials.png" alt-text="tutorials 폴더 복제를 보여 주는 스크린샷":::

1. 작업 영역에 액세스하는 각 사용자가 폴더 목록에 표시됩니다. **tutorials** 폴더를 복제할 폴더를 선택합니다.

### <a name="open-the-cloned-notebook"></a><a name="open"></a> 복제된 Notebook 열기

1. **사용자 파일** 섹션에서 복제된 **tutorials** 폴더를 엽니다.

    > [!IMPORTANT]
    > **samples** 폴더에서는 Notebook을 볼 수 있지만 실행할 수는 없습니다. Notebook을 실행하려면 **사용자 파일** 섹션에서 복제된 버전의 Notebook을 열어야 합니다.
    
1. **tutorials/image-classification-mnist-data** 폴더에서 **img-classification-part1-training.ipynb** 파일을 선택합니다.

    :::image type="content" source="media/tutorial-1st-experiment-sdk-setup/expand-user-folder.png" alt-text="tutorials 폴더 열기를 보여 주는 스크린샷":::

1. 위쪽 바에서 Notebook을 실행하는 데 사용할 컴퓨팅 인스턴스를 선택합니다.


자습서 및 함께 제공되는 **utils.py** 파일은 고유의 [로컬 환경](how-to-configure-environment.md#local)에서 사용하려는 경우 [GitHub](https://github.com/Azure/MachineLearningNotebooks/tree/master/tutorials)에서도 사용할 수 있습니다. 컴퓨팅 인스턴스를 사용하지 않는 경우 `pip install azureml-sdk[notebooks] azureml-opendatasets matplotlib`를 실행하여 이 자습서에 대한 종속성을 설치합니다. 

> [!Important]
> 이 문서의 나머지 부분에는 Notebook에 표시되는 것과 동일한 콘텐츠가 포함되어 있습니다.  
>
> 코드를 실행할 때 함께 읽도록 하려면 지금 Jupyter Notebook으로 전환합니다. 
> Notebook에서 단일 코드 셀을 실행하려면 코드 셀을 클릭하고 **Shift+Enter** 키를 누릅니다. 또는 상단 도구 모음에서 **모두 실행** 을 선택하여 전체 Notebook을 실행합니다.

## <a name="set-up-your-development-environment"></a><a name="start"></a>개발 환경 설정

개발 작업에 대한 모든 설정은 Python Notebook에서 수행할 수 있습니다. 설정에 포함되는 작업은 다음과 같습니다.

* Python 패키지 가져오기
* 로컬 컴퓨터에서 원격 리소스와 통신할 수 있도록 작업 영역에 연결
* 모든 실행을 추적하는 실험 만들기
* 학습에 사용할 원격 컴퓨팅 대상 만들기

### <a name="import-packages"></a>패키지 가져오기

이 세션에 필요한 Python 패키지를 가져옵니다. Azure Machine Learning SDK 버전도 표시합니다.

```python
%matplotlib inline
import numpy as np
import matplotlib.pyplot as plt

import azureml.core
from azureml.core import Workspace

# check core SDK version number
print("Azure ML SDK Version: ", azureml.core.VERSION)
```

### <a name="connect-to-a-workspace"></a>작업 영역에 연결

기존 작업 영역에서 작업 영역 개체를 만듭니다. `Workspace.from_config()`는 **config.json** 파일을 읽고, 세부 정보를 `ws`라는 개체에 로드합니다.  컴퓨팅 인스턴스에는 루트 디렉터리에 저장된 이 파일의 복사본이 있습니다.  다른 곳에서 코드를 실행하는 경우 [파일을 만들](how-to-configure-environment.md#workspace)어야 합니다.

```python
# load workspace configuration from the config.json file in the current folder.
ws = Workspace.from_config()
print(ws.name, ws.location, ws.resource_group, sep='\t')
```

>[!NOTE]
> 다음 코드를 처음 실행할 때 작업 영역에 인증하라는 메시지가 표시될 수 있습니다. 화면상의 지침을 따릅니다.

### <a name="create-an-experiment"></a>실험 만들기

작업 영역에서 실행을 추적하는 실험을 만듭니다. 작업 영역에는 여러 개의 실험이 있을 수 있습니다.

```python
from azureml.core import Experiment
experiment_name = 'Tutorial-sklearn-mnist'

exp = Experiment(workspace=ws, name=experiment_name)
```

### <a name="create-or-attach-an-existing-compute-target"></a>기존 컴퓨팅 대상 만들기 또는 연결

데이터 과학자는 관리형 서비스인 Azure Machine Learning 컴퓨팅을 사용하여 Azure 가상 머신 클러스터에서 기계 학습 모델을 학습할 수 있습니다. 예를 들어 GPU가 지원되는 VM이 있습니다. 이 자습서에서는 학습 환경으로 Azure Machine Learning 컴퓨팅을 만듭니다. 이 자습서의 뒷부분에서 이 VM에서 실행할 Python 코드를 제출합니다. 

아래 코드는 작업 영역에 아직 컴퓨팅 클러스터가 없으면 새로 만듭니다. 사용하지 않을 때는 0으로 축소되는 클러스터를 설정하고 최대 4개의 노드로 확장할 수 있습니다.

 **컴퓨팅 대상을 만드는 데 약 5분이 걸립니다.** 작업 영역에 이미 컴퓨팅 리소스가 있으면 코드는 해당 컴퓨팅 리소스를 사용하고 만들기 프로세스를 건너뜁니다.  

> [!TIP]
> 빠른 시작에서 컴퓨팅 클러스터를 만든 경우 아래 코드의 `compute_name`이 동일한 이름을 사용하는지 확인합니다.

```python
from azureml.core.compute import AmlCompute
from azureml.core.compute import ComputeTarget
import os

# choose a name for your cluster
compute_name = os.environ.get("AML_COMPUTE_CLUSTER_NAME", "cpu-cluster")
compute_min_nodes = os.environ.get("AML_COMPUTE_CLUSTER_MIN_NODES", 0)
compute_max_nodes = os.environ.get("AML_COMPUTE_CLUSTER_MAX_NODES", 4)

# This example uses CPU VM. For using GPU VM, set SKU to STANDARD_NC6
vm_size = os.environ.get("AML_COMPUTE_CLUSTER_SKU", "STANDARD_D2_V2")


if compute_name in ws.compute_targets:
    compute_target = ws.compute_targets[compute_name]
    if compute_target and type(compute_target) is AmlCompute:
        print('found compute target. just use it. ' + compute_name)
else:
    print('creating a new compute target...')
    provisioning_config = AmlCompute.provisioning_configuration(vm_size=vm_size,
                                                                min_nodes=compute_min_nodes,
                                                                max_nodes=compute_max_nodes)

    # create the cluster
    compute_target = ComputeTarget.create(
        ws, compute_name, provisioning_config)

    # can poll for a minimum number of nodes and for a specific timeout.
    # if no min node count is provided it will use the scale settings for the cluster
    compute_target.wait_for_completion(
        show_output=True, min_node_count=None, timeout_in_minutes=20)

    # For a more detailed view of current AmlCompute status, use get_status()
    print(compute_target.get_status().serialize())
```

이제 클라우드에서 모델을 학습하는 데 필요한 패키지 및 컴퓨팅 리소스가 준비되었습니다. 

## <a name="explore-data"></a>데이터 살펴보기

모델을 학습하기 전에 학습하는 데 사용할 데이터를 파악해야 합니다. 이 섹션에서는 다음을 수행하는 방법을 알아봅니다.

* MNIST 데이터 세트 다운로드
* 일부 샘플 이미지 표시

### <a name="download-the-mnist-dataset"></a>MNIST 데이터 세트 다운로드

Azure Open Datasets를 사용하여 원시 MNIST 데이터 파일을 가져옵니다. [Azure Open Datasets](../open-datasets/overview-what-are-open-datasets.md)는 기계 학습 솔루션에 시나리오별 기능을 추가하여 보다 정확한 모델을 만들 수 있는 큐레이팅된 공개 데이터 세트입니다. 각 데이터 세트에는 여러 다른 방식으로 데이터를 검색하기 위한 해당 클래스(이 경우에는 `MNIST`)가 있습니다.

이 코드는 `Dataset`의 하위 클래스인 `FileDataset` 개체로 데이터를 검색합니다. `FileDataset`는 데이터 저장소 또는 퍼블릭 URL 형식의 단일 또는 여러 파일을 참조합니다. 이 클래스는 데이터 원본 위치에 대한 참조를 만들어 컴퓨팅에 파일을 다운로드하거나 탑재하는 기능을 제공합니다. 또한 학습 중에 쉽게 검색할 수 있도록 작업 영역에 데이터 세트를 등록합니다.

[방법](how-to-create-register-datasets.md)에 따라 SDK에서 데이터 세트 및 사용법에 대해 자세히 알아보세요.

```python
from azureml.core import Dataset
from azureml.opendatasets import MNIST

data_folder = os.path.join(os.getcwd(), 'data')
os.makedirs(data_folder, exist_ok=True)

mnist_file_dataset = MNIST.get_file_dataset()
mnist_file_dataset.download(data_folder, overwrite=True)

mnist_file_dataset = mnist_file_dataset.register(workspace=ws,
                                                 name='mnist_opendataset',
                                                 description='training and test dataset',
                                                 create_new_version=True)
```

### <a name="display-some-sample-images"></a>일부 샘플 이미지 표시

압축된 파일을 `numpy` 배열로 로드합니다. 그런 다음, `matplotlib`를 사용하여 데이터 세트에 있는 30개의 무작위 이미지를 그리고, 위에 레이블을 표시합니다. 이 단계를 수행하려면 `utils.py` 파일에 포함된 `load_data` 함수가 필요합니다. 이 파일은 샘플 폴더에 포함되어 있습니다. 이 Notebook과 동일한 폴더에 있는지 확인합니다. `load_data` 함수는 압축 파일을 numpy 배열로 간단히 구문 분석합니다.

```python
# make sure utils.py is in the same directory as this code
from utils import load_data
import glob


# note we also shrink the intensity values (X) from 0-255 to 0-1. This helps the model converge faster.
X_train = load_data(glob.glob(os.path.join(data_folder,"**/train-images-idx3-ubyte.gz"), recursive=True)[0], False) / 255.0
X_test = load_data(glob.glob(os.path.join(data_folder,"**/t10k-images-idx3-ubyte.gz"), recursive=True)[0], False) / 255.0
y_train = load_data(glob.glob(os.path.join(data_folder,"**/train-labels-idx1-ubyte.gz"), recursive=True)[0], True).reshape(-1)
y_test = load_data(glob.glob(os.path.join(data_folder,"**/t10k-labels-idx1-ubyte.gz"), recursive=True)[0], True).reshape(-1)


# now let's show some randomly chosen images from the traininng set.
count = 0
sample_size = 30
plt.figure(figsize=(16, 6))
for i in np.random.permutation(X_train.shape[0])[:sample_size]:
    count = count + 1
    plt.subplot(1, sample_size, count)
    plt.axhline('')
    plt.axvline('')
    plt.text(x=10, y=-10, s=y_train[i], fontsize=18)
    plt.imshow(X_train[i].reshape(28, 28), cmap=plt.cm.Greys)
plt.show()
```

이미지의 무작위 샘플이 다음과 같이 표시됩니다.

![무작위 이미지 샘플](./media/tutorial-train-models-with-aml/digits.png)

이제 이러한 이미지의 모양과 예상되는 예측 결과를 이해할 수 있을 것입니다.

## <a name="train-on-a-remote-cluster"></a>원격 클러스터에서 학습

이 태스크의 경우 이전에 설정한 원격 학습 클러스터에서 실행할 작업을 제출하세요.  작업을 제출하는 방법은 아래와 같습니다.
* 디렉터리 만들기
* 학습 스크립트 만들기
* 스크립트 실행 구성 만들기
* 작업 제출

### <a name="create-a-directory"></a>디렉터리 만들기

필요한 코드를 컴퓨터에서 원격 리소스로 전달하기 위한 디렉터리를 만듭니다.

```python
import os
script_folder = os.path.join(os.getcwd(), "sklearn-mnist")
os.makedirs(script_folder, exist_ok=True)
```

### <a name="create-a-training-script"></a>학습 스크립트 만들기

클러스터에 작업을 제출하려면 먼저 학습 스크립트를 만듭니다. 다음 코드를 실행하여 방금 만든 디렉터리에 `train.py`라는 학습 스크립트를 만듭니다.

```python
%%writefile $script_folder/train.py

import argparse
import os
import numpy as np
import glob

from sklearn.linear_model import LogisticRegression
import joblib

from azureml.core import Run
from utils import load_data

# let user feed in 2 parameters, the dataset to mount or download, and the regularization rate of the logistic regression model
parser = argparse.ArgumentParser()
parser.add_argument('--data-folder', type=str, dest='data_folder', help='data folder mounting point')
parser.add_argument('--regularization', type=float, dest='reg', default=0.01, help='regularization rate')
args = parser.parse_args()

data_folder = args.data_folder
print('Data folder:', data_folder)

# load train and test set into numpy arrays
# note we scale the pixel intensity values to 0-1 (by dividing it with 255.0) so the model can converge faster.
X_train = load_data(glob.glob(os.path.join(data_folder, '**/train-images-idx3-ubyte.gz'), recursive=True)[0], False) / 255.0
X_test = load_data(glob.glob(os.path.join(data_folder, '**/t10k-images-idx3-ubyte.gz'), recursive=True)[0], False) / 255.0
y_train = load_data(glob.glob(os.path.join(data_folder, '**/train-labels-idx1-ubyte.gz'), recursive=True)[0], True).reshape(-1)
y_test = load_data(glob.glob(os.path.join(data_folder, '**/t10k-labels-idx1-ubyte.gz'), recursive=True)[0], True).reshape(-1)

print(X_train.shape, y_train.shape, X_test.shape, y_test.shape, sep = '\n')

# get hold of the current run
run = Run.get_context()

print('Train a logistic regression model with regularization rate of', args.reg)
clf = LogisticRegression(C=1.0/args.reg, solver="liblinear", multi_class="auto", random_state=42)
clf.fit(X_train, y_train)

print('Predict the test set')
y_hat = clf.predict(X_test)

# calculate accuracy on the prediction
acc = np.average(y_hat == y_test)
print('Accuracy is', acc)

run.log('regularization rate', np.float(args.reg))
run.log('accuracy', np.float(acc))

os.makedirs('outputs', exist_ok=True)
# note file saved in the outputs folder is automatically uploaded into experiment record
joblib.dump(value=clf, filename='outputs/sklearn_mnist_model.pkl')
```

이 스크립트가 데이터를 가져오고 모델을 저장하는 방법은 다음과 같습니다.

- 학습 스크립트에서 인수를 읽어 데이터가 있는 디렉터리를 찾습니다. 나중에 작업을 제출할 때 이 인수에 대한 데이터 저장소를 가리킵니다().

  `parser.add_argument('--data-folder', type=str, dest='data_folder', help='data directory mounting point')`

- 학습 스크립트는 **outputs** 라는 디렉터리에 모델을 저장합니다. 이 디렉터리에 작성된 모든 내용은 작업 영역으로 자동 업로드됩니다. 자습서의 뒷부분에서는 이 디렉터리의 모델에 액세스합니다. `joblib.dump(value=clf, filename='outputs/sklearn_mnist_model.pkl')`

- 학습 스크립트가 데이터 세트를 올바르게 로드하려면 `utils.py` 파일이 필요합니다. 다음 코드는 `utils.py`를 `script_folder`에 복사합니다. 그러면 원격 리소스의 학습 스크립트와 함께 이 파일에 액세스할 수 있습니다.

  ```python
  import shutil
  shutil.copy('utils.py', script_folder)
  ```

### <a name="configure-the-training-job"></a>학습 작업 구성

[ScriptRunConfig](/python/api/azureml-core/azureml.core.scriptrunconfig) 개체를 만들어 학습 스크립트, 사용할 환경 및 실행할 컴퓨팅 대상 등 학습 작업의 구성 세부 정보를 지정합니다. 다음을 지정하여 ScriptRunConfig를 구성합니다.

* 스크립트를 포함하는 디렉터리. 이 디렉터리의 모든 파일은 실행을 위해 클러스터 노드로 업로드됩니다.
* 컴퓨팅 대상. 이 경우 만든 Azure Machine Learning 컴퓨팅 클러스터를 사용합니다.
* 학습 스크립트 이름(**train.py**)
* 스크립트를 실행하는 데 필요한 라이브러리가 포함된 환경입니다.
* 학습 스크립트에 필요한 인수

이 자습서에서 이 대상은 AmlCompute입니다. 스크립트 폴더의 모든 파일은 실행을 위해 클러스터 노드에 업로드됩니다. **--data_folder** 는 데이터 세트를 사용하도록 설정되었습니다.

먼저 scikit-learn 라이브러리, 데이터 세트에 액세스하는 데 필요한 azureml-dataset-runtime 및 azureml-defaults가 포함된 환경을 만듭니다. 여기에는 로깅 메트릭에 대한 종속성이 포함됩니다. azureml-defaults에는 자습서 2부 뒤부분에 나오는 웹 서비스로 모델을 배포하는 데 필요한 종속성도 포함되어 있습니다.

환경이 정의되면 작업 영역에 등록하여 자습서 2부에서 다시 사용합니다.

```python
from azureml.core.environment import Environment
from azureml.core.conda_dependencies import CondaDependencies

# to install required packages
env = Environment('tutorial-env')
cd = CondaDependencies.create(pip_packages=['azureml-dataset-runtime[pandas,fuse]', 'azureml-defaults'], conda_packages=['scikit-learn==0.22.1'])

env.python.conda_dependencies = cd

# Register environment to re-use later
env.register(workspace=ws)
```

그런 다음, 학습 스크립트, 컴퓨팅 대상 및 환경을 지정하여 ScriptRunConfig를 만듭니다.

```python
from azureml.core import ScriptRunConfig

args = ['--data-folder', mnist_file_dataset.as_mount(), '--regularization', 0.5]

src = ScriptRunConfig(source_directory=script_folder,
                      script='train.py', 
                      arguments=args,
                      compute_target=compute_target,
                      environment=env)
```

### <a name="submit-the-job-to-the-cluster"></a>클러스터에 작업 제출

ScriptRunConfig 개체를 제출하여 실험을 실행합니다.

```python
run = exp.submit(config=src)
run
```

호출은 비동기이므로 작업이 시작되는 즉시 **준비 중** 또는 **실행 중** 상태가 반환됩니다.

## <a name="monitor-a-remote-run"></a>원격 실행 모니터링

전체적으로 첫 번째 실행은 **약 10분** 이 걸립니다. 그러나 스크립트 종속성이 변경되지 않는 한 후속 실행에서도 동일한 이미지가 다시 사용됩니다. 따라서 컨테이너 시작 시간이 훨씬 더 빠릅니다.

기다리는 동안 수행되는 작업은 다음과 같습니다.

- **이미지 만들기**: Azure ML 환경에서 지정한 Python 환경과 일치하는 Docker 이미지가 만들어집니다. 이미지는 작업 영역에 업로드됩니다. 이미지를 만들고 업로드하는 데 **약 5분** 이 걸립니다.

  컨테이너가 후속 실행을 위해 캐시되므로 이 단계는 각 Python 환경에 대해 한 번만 수행됩니다. 이미지 생성 중에 로그가 실행 기록으로 스트리밍됩니다. 이러한 로그를 사용하여 이미지 만들기 진행 상황을 모니터링할 수 있습니다.

- **크기 조정**: 원격 클러스터에서 현재 사용 가능한 것보다 더 많은 노드를 실행해야 하는 경우 추가 노드가 자동으로 추가됩니다. 크기 조정에는 일반적으로 **약 5분** 이 걸립니다.

- **실행**: 이 단계에서는 필요한 스크립트와 파일이 컴퓨팅 대상으로 보내집니다. 다음으로, 데이터 저장소가 탑재되거나 복사됩니다. 그런 다음, **entry_script** 가 실행됩니다. 작업이 실행되는 동안 **stdout** 및 **./logs** 디렉터리가 실행 기록으로 스트리밍됩니다. 이러한 로그를 사용하여 실행 진행 상황을 모니터링할 수 있습니다.

- **후 처리 중**: 실행의 **./outputs** 디렉터리가 작업 영역의 실행 기록으로 복사되므로 이러한 결과에 액세스할 수 있습니다.

실행 중인 작업의 진행 상황은 여러 가지 방법으로 확인할 수 있습니다. 이 자습서에서는 Jupyter 위젯과 `wait_for_completion` 메서드를 사용합니다.

### <a name="jupyter-widget"></a>Jupyter 위젯

[Jupyter 위젯](/python/api/azureml-widgets/azureml.widgets)으로 실행의 진행 상태를 감시합니다. 실행 제출과 마찬가지로, 위젯은 비동기이며 작업이 완료될 때까지 10~15초마다 라이브 업데이트를 제공합니다.

```python
from azureml.widgets import RunDetails
RunDetails(run).show()
```

위젯은 학습이 끝날 때 다음과 같이 표시됩니다.

![Notebook 위젯](./media/tutorial-train-models-with-aml/widget.png)

실행을 취소해야 하는 경우 [다음 지침](./how-to-track-monitor-analyze-runs.md)을 따릅니다.

### <a name="get-log-results-upon-completion"></a>완료 시 로그 결과 가져오기

모델 학습 및 모니터링은 백그라운드에서 발생합니다. 더 많은 코드를 실행하기 전에 모델 학습이 완료될 때까지 기다립니다. 모델 학습이 완료되는 시간을 표시하려면 `wait_for_completion`을 사용합니다.

```python
run.wait_for_completion(show_output=False)  # specify True for a verbose log
```

### <a name="display-run-results"></a>실행 결과 표시

이제 모델이 원격 클러스터에서 학습을 마쳤습니다. 다음과 같이 모델의 정확도를 확인합니다.

```python
print(run.get_metrics())
```

출력은 원격 모델 정확도가 0.9204라는 것을 보여줍니다.

`{'regularization rate': 0.8, 'accuracy': 0.9204}`

다음 자습서에서는 이 모델에 대해 더 자세히 살펴봅니다.

## <a name="register-model"></a>모델 등록

학습 스크립트의 마지막 단계에서는 작업이 실행되는 클러스터의 VM에 있는 `outputs`라는 디렉터리에 `outputs/sklearn_mnist_model.pkl` 파일을 작성했습니다. `outputs`는 이 디렉터리의 모든 콘텐츠가 작업 영역에 자동으로 업로드된다는 점에서 특수한 디렉터리입니다. 이 콘텐츠는 작업 영역의 실험 실행 레코드에 표시됩니다. 따라서 이제는 작업 영역에서도 모델 파일을 사용할 수 있습니다.

해당 실행과 연결된 파일을 볼 수 있습니다.

```python
print(run.get_file_names())
```

나중에 사용자 또는 다른 공동 작업자가 이 모델을 쿼리, 검사 및 배포할 수 있도록 해당 모델을 작업 영역에 등록합니다.

```python
# register model
model = run.register_model(model_name='sklearn_mnist',
                           model_path='outputs/sklearn_mnist_model.pkl')
print(model.name, model.id, model.version, sep='\t')
```

## <a name="clean-up-resources"></a>리소스 정리

[!INCLUDE [aml-delete-resource-group](../../includes/aml-delete-resource-group.md)]

Azure Machine Learning 컴퓨팅 클러스터만 삭제할 수도 있습니다. 그러나 자동 크기 조정이 설정되고 클러스터 최솟값이 0이 됩니다. 따라서 이 특정 리소스를 사용하지 않는 경우에는 추가 컴퓨팅 요금이 부과하지 않습니다.

```python
# Optionally, delete the Azure Machine Learning Compute cluster
compute_target.delete()
```

## <a name="next-steps"></a>다음 단계

이 Azure Machine Learning 자습서에서는 Python을 사용하여 다음 작업을 수행했습니다.

> [!div class="checklist"]
> * 개발 환경 설정
> * 데이터 액세스 및 검사
> * 인기 있는 scikit-learn 기계 학습 라이브러리를 사용하여 원격 클러스터에서 여러 모델 학습
> * 학습 세부 정보 검토 및 최상의 모델 등록

이 등록된 모델을 자습서 시리즈의 제2부에 나와 있는 지침에 따라 배포할 준비가 되었습니다.

> [!div class="nextstepaction"]
> [자습서 2 - 모델 배포](tutorial-deploy-models-with-aml.md)
