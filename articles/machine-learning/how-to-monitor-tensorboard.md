---
title: TensorBoard로 실험 시각화
titleSuffix: Azure Machine Learning
description: TensorBoard를 시작 하 여 실험 실행 기록을 시각화 하 고 하이퍼 매개 변수 조정 및 재 학습을 위한 잠재적 영역을 식별 합니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: mlops
author: minxia
ms.author: minxia
ms.date: 10/21/2021
ms.topic: how-to
ms.openlocfilehash: e01cc1b97659a4580c3cc33cf276b9fc566fb10a
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131558614"
---
# <a name="visualize-experiment-runs-and-metrics-with-tensorboard-and-azure-machine-learning"></a>TensorBoard 및 Azure Machine Learning을 사용한 실험 실행 및 메트릭 시각화


이 문서에서는 주 Azure Machine Learning SDK의 [`tensorboard` 패키지](/python/api/azureml-tensorboard/)를 사용하여 TensorBoard에서의 실험 실행과 메트릭을 보는 방법에 대해 알아봅니다. 실험 실행을 검사한 후에는 더 나은 기계 학습 모델 조정 및 재학습을 할 수 있습니다.

[TensorBoard](/python/api/azureml-tensorboard/azureml.tensorboard.tensorboard)는 실험 구조와 성능을 검사하고 이해하기 위한 웹 애플리케이션 모음입니다.

Azure Machine Learning 실험을 통한 TensorBoard 시작 방법은 실험 유형에 따라 달라집니다.
+ 실험에서 TensorBoard가 사용할 수 있는 로그 파일(예: PyTorch, Chainer 및 TensorFlow 실험)을 기본적으로 출력하는 경우 실험 실행 기록에서 [직접 TensorBoard를 시작](#launch-tensorboard)할 수 있습니다. 

+ TensorBoard 사용 가능 파일을 기본적으로 출력하지 않는 실험(예: Scikit-learn 또는 Azure Machine Learning 실험)의 경우 [`export_to_tensorboard()` 메서드](#export)를 사용하여 실행 기록을 TensorBoard 로그로 내보내고 여기에서 TensorBoard를 시작합니다. 

> [!TIP]
> 이 문서의 정보는 주로 모델 학습 프로세스를 모니터링하려는 데이터 과학자와 개발자를 위한 것입니다. 할당량, 완료된 학습 실행 또는 완료된 모델 배포와 같이 Azure Machine Learning의 리소스 사용과 이벤트를 모니터링하는 데 관심이 있는 관리자는 [Azure Machine Learning 모니터링](monitor-azure-machine-learning.md)을 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

* TensorBoard를 시작하여 실험 실행 기록을 보려면 실험에서 이전에 로깅을 사용하도록 설정하여 해당 메트릭과 성능을 추적했어야 합니다.  
* 이 문서의 코드는 다음 환경 중 하나에서 실행할 수 있습니다. 
    * Azure Machine Learning 컴퓨팅 인스턴스 - 다운로드 또는 설치 필요 없음
        * [빠른 시작: Azure Machine Learning 시작하기](quickstart-create-resources.md)를 완료하여 SDK 및 샘플 리포지토리가 미리 로드된 전용 Notebook 서버를 만듭니다.
        * Notebook 서버의 샘플 폴더에서 다음 디렉터리로 이동하여 두 개의 완료 및 확장된 Notebook을 찾습니다.
            * **how-to-use-azureml > track-and-monitor-experiments > tensorboard > export-run-history-to-tensorboard > export-run-history-to-tensorboard.ipynb**
            * **how-to-use-azureml > track-and-monitor-experiments > tensorboard > tensorboard > tensorboard.ipynb**
    * 사용자 고유의 Jupyter Notebook 서버에서 다음을 수행합니다.
       * `tensorboard` extra와 함께 [Azure Machine Learning SDK를 설치합니다](/python/api/overview/azure/ml/install).
        * [Azure Machine Learning 작업 영역을 만듭니다](how-to-manage-workspace.md).  
        * [작업 영역 구성 파일을 만듭니다](how-to-configure-environment.md#workspace).

## <a name="option-1-directly-view-run-history-in-tensorboard"></a>옵션 1: TensorBoard에서 직접 실행 기록 보기

이 옵션은 PyTorch, Chainer 및 TensorFlow 실험같이 TensorBoard에서 사용 가능한 로그 파일을 기본적으로 출력하는 실험에 사용할 수 있습니다. 이 실험의 사례가 아니면 [`export_to_tensorboard()` 메서드](#export)를 대신 사용합니다.

다음 코드 예에서는 원격 컴퓨팅 대상인 Azure Machine Learning 컴퓨팅의 TensorFlow 리포지토리에서 [MNIST 데모 실험](https://raw.githubusercontent.com/tensorflow/tensorflow/r1.8/tensorflow/examples/tutorials/mnist/mnist_with_summaries.py)을 사용합니다. 다음으로 TensorFlow 모델 학습을 위한 실행을 구성하고 시작한 다음, 이 TensorFlow 실험에 대해 TensorBoard를 시작합니다.

### <a name="set-experiment-name-and-create-project-folder"></a>실험 이름 설정 및 프로젝트 폴더 만들기

여기에서는 실험의 이름을 지정하고 해당 폴더를 만듭니다. 
 
```python
from os import path, makedirs
experiment_name = 'tensorboard-demo'

# experiment folder
exp_dir = './sample_projects/' + experiment_name

if not path.exists(exp_dir):
    makedirs(exp_dir)

```

### <a name="download-tensorflow-demo-experiment-code"></a>TensorFlow 데모 실험 코드 다운로드

TensorFlow 리포지토리에는 광범위한 TensorBoard 계측을 사용하는 MNIST 데모가 있습니다. Azure Machine Learning을 사용하기 위해 이 데모의 코드를 변경할 필요는 없습니다. 다음 코드에서 MNIST 코드를 다운로드하여 새로 만든 실험 폴더에 저장합니다.

```python
import requests
import os

tf_code = requests.get("https://raw.githubusercontent.com/tensorflow/tensorflow/r1.8/tensorflow/examples/tutorials/mnist/mnist_with_summaries.py")
with open(os.path.join(exp_dir, "mnist_with_summaries.py"), "w") as file:
    file.write(tf_code.text)
```
MNIST 코드 파일 mnist_with_summaries.py에서 `tf.summary.scalar()`, `tf.summary.histogram()`, `tf.summary.FileWriter()` 등을 호출하는 줄이 있습니다. 이러한 메서드는 실험의 주요 메트릭을 실행 기록으로 그룹화, 로그 및 태그합니다. `tf.summary.FileWriter()`는 로깅된 실험 메트릭의 데이터를 직렬화하여 TensorBoard에서 시각화를 생성할 수 있으므로 특히 중요합니다.

 ### <a name="configure-experiment"></a>실험 구성

다음에서는 실험을 구성하고 로그 및 데이터에 대한 디렉터리를 설정합니다. 이 로그는 나중에 TensorBoard가 액세스하는 실행 기록에 업로드됩니다.

> [!Note]
> 이 TensorFlow 예의 경우 로컬 컴퓨터에 TensorFlow를 설치해야 합니다. 또한 TensorBoard 모듈(TensorFlow에 포함된 모듈)은 로컬 컴퓨터가 TensorBoard를 실행하는 것처럼 이 Notebook의 커널에 액세스할 수 있어야 합니다.

```Python
import azureml.core
from azureml.core import Workspace
from azureml.core import Experiment

ws = Workspace.from_config()

# create directories for experiment logs and dataset
logs_dir = os.path.join(os.curdir, "logs")
data_dir = os.path.abspath(os.path.join(os.curdir, "mnist_data"))

if not path.exists(data_dir):
    makedirs(data_dir)

os.environ["TEST_TMPDIR"] = data_dir

# Writing logs to ./logs results in their being uploaded to the run history,
# and thus, made accessible to our TensorBoard instance.
args = ["--log_dir", logs_dir]

# Create an experiment
exp = Experiment(ws, experiment_name)
```

### <a name="create-a-cluster-for-your-experiment"></a>실험을 위한 클러스터 만들기
이 실험을 위해 AmlCompute 클러스터를 만들지만 모든 환경에서 실험을 만들 수 있으며 실험 실행 기록에 대해 TensorBoard를 시작할 수 있습니다. 

```Python
from azureml.core.compute import ComputeTarget, AmlCompute

cluster_name = "cpu-cluster"

cts = ws.compute_targets
found = False
if cluster_name in cts and cts[cluster_name].type == 'AmlCompute':
   found = True
   print('Found existing compute target.')
   compute_target = cts[cluster_name]
if not found:
    print('Creating a new compute target...')
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_D2_V2', 
                                                           max_nodes=4)

    # create the cluster
    compute_target = ComputeTarget.create(ws, cluster_name, compute_config)

compute_target.wait_for_completion(show_output=True, min_node_count=None)

# use get_status() to get a detailed status for the current cluster. 
# print(compute_target.get_status().serialize())
```

[!INCLUDE [low-pri-note](../../includes/machine-learning-low-pri-vm.md)]

### <a name="configure-and-submit-training-run"></a>학습 실행 구성 및 제출

ScriptRunConfig 개체를 만들어 학습 작업을 구성합니다.

```Python
from azureml.core import ScriptRunConfig
from azureml.core import Environment

# Here we will use the TensorFlow 2.2 curated environment
tf_env = Environment.get(ws, 'AzureML-TensorFlow-2.2-GPU')

src = ScriptRunConfig(source_directory=exp_dir,
                      script='mnist_with_summaries.py',
                      arguments=args,
                      compute_target=compute_target,
                      environment=tf_env)
run = exp.submit(src)
```

### <a name="launch-tensorboard"></a>TensorBoard 시작

실행 중이거나 완료된 후 TensorBoard를 시작할 수 있습니다. 다음에는 `run`에 로드된 실험 실행 기록을 사용하는 TensorBoard 개체 인스턴스인 `tb`를 만든 다음 `start()` 메서드를 사용하여 TensorBoard를 시작합니다. 
  
[TensorBoard 생성자](/python/api/azureml-tensorboard/azureml.tensorboard.tensorboard)는 실행 배열을 사용하므로 이를 단일 요소 배열로 전달해야 합니다.

```python
from azureml.tensorboard import Tensorboard

tb = Tensorboard([run])

# If successful, start() returns a string with the URI of the instance.
tb.start()

# After your job completes, be sure to stop() the streaming otherwise it will continue to run. 
tb.stop()
```

> [!Note]
> 이 예에서는 TensorFlow를 사용했지만 PyTorch 또는 Chainer에서 TensorBoard를 쉽게 사용할 수 있습니다. TensorFlow는 TensorBoard를 실행하는 컴퓨터에서 사용할 수 있어야 하지만 PyTorch 또는 Chainer 계산을 수행하는 컴퓨터에서는 필요하지 않습니다. 


<a name="export"></a>

## <a name="option-2-export-history-as-log-to-view-in-tensorboard"></a>옵션 2: TensorBoard에서 보기 위해 기록을 로그로 내보내기

다음 코드는 샘플 실험을 설정하고, Azure Machine Learning 실행 기록 API를 사용하여 로깅 프로세스를 시작하고, 시각화를 위해 TensorBoard에서 사용 가능한 로그로 실험 실행 기록을 내보냅니다. 

### <a name="set-up-experiment"></a>실험 설정

다음 코드는 새 실험을 설정하고 실행 디렉터리의 이름을 `root_run`로 지정합니다. 

```python
from azureml.core import Workspace, Experiment
import azureml.core

# set experiment name and run name
ws = Workspace.from_config()
experiment_name = 'export-to-tensorboard'
exp = Experiment(ws, experiment_name)
root_run = exp.start_logging()
```

여기서는 scikit-learn과 함께 기본 제공되는 작은 데이터 세트인 당뇨병 데이터 세트를 로드하고 테스트 및 학습 집합으로 분할합니다.

```Python
from sklearn.datasets import load_diabetes
from sklearn.linear_model import Ridge
from sklearn.metrics import mean_squared_error
from sklearn.model_selection import train_test_split
X, y = load_diabetes(return_X_y=True)
columns = ['age', 'gender', 'bmi', 'bp', 's1', 's2', 's3', 's4', 's5', 's6']
x_train, x_test, y_train, y_test = train_test_split(X, y, test_size=0.2, random_state=0)
data = {
    "train":{"x":x_train, "y":y_train},        
    "test":{"x":x_test, "y":y_test}
}
```

### <a name="run-experiment-and-log-metrics"></a>실험 및 로그 메트릭 실행

이 코드의 경우 실행 기록에서 선형 회귀 모델 및 로그 키 메트릭, 알파 계수, `alpha` 및 평균 제곱 오차(`mse`)를 학습합니다.

```Python
from tqdm import tqdm
alphas = [.1, .2, .3, .4, .5, .6 , .7]
# try a bunch of alpha values in a Linear Regression (aka Ridge regression) mode
for alpha in tqdm(alphas):
  # create child runs and fit lines for the resulting models
  with root_run.child_run("alpha" + str(alpha)) as run:
 
   reg = Ridge(alpha=alpha)
   reg.fit(data["train"]["x"], data["train"]["y"])    
 
   preds = reg.predict(data["test"]["x"])
   mse = mean_squared_error(preds, data["test"]["y"])
   # End train and eval

# log alpha, mean_squared_error and feature names in run history
   root_run.log("alpha", alpha)
   root_run.log("mse", mse)
```

### <a name="export-runs-to-tensorboard"></a>TensorBoard로 실행 내보내기

SDK [export_to_tensorboard()](/python/api/azureml-tensorboard/azureml.tensorboard.export) 메서드로 Azure Machine Learning 실험의 실행 기록을 TensorBoard 로그로 내보내므로 TensorBoard를 통해 볼 수 있습니다.  

다음 코드에서는 현재 작업 디렉터리에 `logdir` 폴더를 만듭니다. 이 폴더에는 `root_run`에서 실험 실행 기록과 로그를 내보낸 후 해당 실행을 완료된 것으로 표시합니다. 

```Python
from azureml.tensorboard.export import export_to_tensorboard
import os

logdir = 'exportedTBlogs'
log_path = os.path.join(os.getcwd(), logdir)
try:
    os.stat(log_path)
except os.error:
    os.mkdir(log_path)
print(logdir)

# export run history for the project
export_to_tensorboard(root_run, logdir)

root_run.complete()
```

> [!Note]
> 실행 이름을 `export_to_tensorboard(run_name, logdir)`로 지정하여 특정 실행을 TensorBoard로 내보낼 수 있습니다.

### <a name="start-and-stop-tensorboard"></a>TensorBoard 시작 및 중지
이 실험의 실행 기록을 내보낸 후 [start()](/python/api/azureml-tensorboard/azureml.tensorboard.tensorboard#start-start-browser-false-) 메서드를 사용하여 TensorBoard를 시작할 수 있습니다. 

```Python
from azureml.tensorboard import Tensorboard

# The TensorBoard constructor takes an array of runs, so be sure and pass it in as a single-element array here
tb = Tensorboard([], local_root=logdir, port=6006)

# If successful, start() returns a string with the URI of the instance.
tb.start()
```

완료되면 TensorBoard 개체의 [stop()](/python/api/azureml-tensorboard/azureml.tensorboard.tensorboard#stop--) 메서드를 호출해야 합니다. 그러지 않으면 TensorBoard가 Notebook 커널을 종료할 때까지 계속 실행됩니다. 

```python
tb.stop()
```

## <a name="next-steps"></a>다음 단계

이 방법에서 두 개의 실험을 만들었으며 실행 기록에 대해 TensorBoard를 시작하여 잠재적인 조정 및 재학습 영역을 파악하는 방법을 배웠습니다. 

* 모델에 만족하는 경우 [모델 배포 방법](how-to-deploy-and-where.md) 문서를 참조하세요. 
* [하이퍼 매개 변수 조정](how-to-tune-hyperparameters.md)에 대해 자세히 알아보세요.
