---
title: ParallelRunStep 문제 해결
titleSuffix: Azure Machine Learning
description: 기계 학습 파이프라인에서 ParallelRunStep을 사용할 때 오류가 발생하는 경우 문제를 해결하는 방법에 대한 팁입니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: mlops
ms.topic: troubleshooting
ms.custom: troubleshooting
ms.reviewer: larryfr, vaidyas, laobri, tracych
ms.author: pansav
author: psavdekar
ms.date: 10/21/2021
ms.openlocfilehash: 47ad08328588ca3b380298b6e796cc05d3af29fe
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131564331"
---
# <a name="troubleshooting-the-parallelrunstep"></a>ParallelRunStep 문제 해결

이 문서에서는 [Azure Machine Learning SDK](/python/api/overview/azure/ml/intro)에서 [ParallelRunStep](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.parallel_run_step.parallelrunstep) 클래스를 사용할 때 오류가 발생하는 경우 문제 해결 방법에 대해 알아봅니다.

파이프라인 문제 해결에 대한 일반적인 팁은 [기계 학습 파이프라인 문제 해결](how-to-debug-pipelines.md)을 참조하세요.

## <a name="testing-scripts-locally"></a>로컬에서 스크립트 테스트

 ParallelRunStep은 ML 파이프라인에서 단계로 실행됩니다. 첫 번째 단계로 [로컬에서 스크립트를 테스트](how-to-debug-visual-studio-code.md#debug-and-troubleshoot-machine-learning-pipelines)할 수 있습니다.

## <a name="entry-script-requirements"></a>항목 스크립트 요구 사항

에 대한 항목 스크립트는 `ParallelRunStep` 함수를 포함해야 하며 필요에 따라 함수를 *포함해야 합니다.* `run()` `init()`
- `init()`: 이후 처리를 위해 비용이 많이 드는 준비 또는 일반적인 준비에 이 함수를 사용합니다. 예를 들어 모델을 글로벌 개체에 로드하는 데 사용합니다. 이 함수는 프로세스를 시작할 때 한 번만 호출됩니다.
    > [!NOTE]
    > `init`메서드가 출력 디렉터리를 만드는 경우 및 를 `parents=True` 지정합니다. `exist_ok=True` `init` 메서드는 작업이 실행 중인 모든 노드의 각 작업자 프로세스에서 호출됩니다.
-  `run(mini_batch)`: 이 함수는 각 `mini_batch` 인스턴스에 대해 실행됩니다.
    -  `mini_batch`: `ParallelRunStep`은(는) run 메서드를 호출하고 목록 또는 pandas `DataFrame`을(를) 메서드에 인수로 전달합니다. 입력이 `FileDataset`이면 mini_batch의 각 항목이 파일 경로가 되고 입력이 `TabularDataset`이면 Pandas `DataFrame`입니다.
    -  `response`: run() 메서드는 Pandas `DataFrame` 또는 배열을 반환해야 합니다. append_row output_action의 경우 반환되는 요소가 공통 출력 파일에 추가됩니다. summary_only의 경우 요소의 내용이 무시됩니다. 모든 출력 작업의 경우 반환되는 각 출력 요소는 입력 미니 일괄 처리의 입력 요소에 대한 성공적인 실행 하나를 나타냅니다. 입력을 실행 출력 결과에 매핑하기에 충분한 데이터가 실행 결과에 포함되어 있는지 확인합니다. 실행 출력은 출력 파일에 기록되지만 순서대로 기록된다는 보장은 없으므로, 사용자는 출력의 키를 사용하여 입력에 매핑해야 합니다.
        > [!NOTE]
        > 입력 요소 한 개에 대해 출력 요소 한 개가 있어야 합니다.

```python
%%writefile digit_identification.py
# Snippets from a sample script.
# Refer to the accompanying digit_identification.py
# (https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/machine-learning-pipelines/parallel-run)
# for the implementation script.

import os
import numpy as np
import tensorflow as tf
from PIL import Image
from azureml.core import Model


def init():
    global g_tf_sess

    # Pull down the model from the workspace
    model_path = Model.get_model_path("mnist")

    # Construct a graph to execute
    tf.reset_default_graph()
    saver = tf.train.import_meta_graph(os.path.join(model_path, 'mnist-tf.model.meta'))
    g_tf_sess = tf.Session()
    saver.restore(g_tf_sess, os.path.join(model_path, 'mnist-tf.model'))


def run(mini_batch):
    print(f'run method start: {__file__}, run({mini_batch})')
    resultList = []
    in_tensor = g_tf_sess.graph.get_tensor_by_name("network/X:0")
    output = g_tf_sess.graph.get_tensor_by_name("network/output/MatMul:0")

    for image in mini_batch:
        # Prepare each image
        data = Image.open(image)
        np_im = np.array(data).reshape((1, 784))
        # Perform inference
        inference_result = output.eval(feed_dict={in_tensor: np_im}, session=g_tf_sess)
        # Find the best probability, and add it to the result list
        best_result = np.argmax(inference_result)
        resultList.append("{}: {}".format(os.path.basename(image), best_result))

    return resultList
```

다른 파일 또는 폴더가 유추 스크립트와 동일한 디렉터리에 있는 경우 현재 작업 디렉터리를 찾아 참조할 수 있습니다.

```python
script_dir = os.path.realpath(os.path.join(__file__, '..',))
file_path = os.path.join(script_dir, "<file_name>")
```

### <a name="parameters-for-parallelrunconfig"></a>ParallelRunConfig에 대한 매개 변수

`ParallelRunConfig`는 Azure Machine Learning 파이프라인 내에서 `ParallelRunStep` 인스턴스에 대한 주요 구성입니다. 스크립트를 래핑하고 다음 항목을 포함하여 필요한 매개 변수를 구성하는 데 사용됩니다.
- `entry_script`: 여러 노드에서 병렬로 실행되는 로컬 파일 경로인 사용자 스크립트입니다. `source_directory`가 있으면 상대 경로를 사용합니다. 없으면 머신에서 액세스할 수 있는 아무 경로를 사용합니다.
- `mini_batch_size`: 단일 `run()` 호출에 전달된 미니 일괄 처리의 크기입니다. (선택 사항이며 기본값은 `FileDataset`의 경우 `10` 파일이고 `TabularDataset`의 경우 `1MB`임)
    - `FileDataset`의 경우 최솟값이 `1`인 파일 수입니다. 여러 파일을 한 미니 일괄 처리로 결합할 수 있습니다.
    - `TabularDataset`의 경우 데이터의 크기입니다. 예제 값은 `1024`, `1024KB`, `10MB` 및 `1GB`입니다. 권장 값은 `1MB`입니다. `TabularDataset`의 미니 일괄 처리는 파일 경계를 넘지 않습니다. 예를 들어 다양한 크기의 .csv 파일이 있는 경우 가장 작은 파일은 100KB이고 가장 큰 파일은 10MB입니다. `mini_batch_size = 1MB`를 설정하는 경우 크기가 1MB보다 작은 파일은 하나의 미니 일괄 처리로 취급됩니다. 크기가 1MB보다 큰 파일은 여러 개의 미니 일괄 처리로 분할됩니다.
        > [!NOTE]
        > SQL에서 지원되는 TabularDatasets는 분할할 수 없습니다.
        > 단일 parquet 파일 및 단일 행 그룹의 TabularDatasets는 분할할 수 없습니다.

- `error_threshold`: 처리 중에 무시해야 하는 `FileDataset`에 대한 `TabularDataset` 및 파일 오류에 대한 레코드 실패 횟수입니다. 전체 입력의 오류 횟수가 이 값을 초과하면 작업이 중단됩니다. 오류 임계값은 `run()` 메서드로 전송되는 개별 미니 일괄 처리가 아닌 전체 입력에 적용됩니다. 범위는 `[-1, int.max]`입니다. `-1` 부분은 처리 중에 발생하는 모든 오류를 무시한다는 뜻입니다.
- `output_action`: 다음 값 중 하나는 출력을 구성하는 방법을 나타냅니다.
    - `summary_only`: 사용자 스크립트는 출력을 저장합니다. `ParallelRunStep`은 출력을 오류 임계값 계산에만 사용합니다.
    - `append_row`: 모든 입력에서, 출력 폴더에 줄로 구분된 모든 출력을 추가하는 하나의 파일만 생성됩니다.
- `append_row_file_name`: append_row output_action의 출력 파일 이름을 사용자 지정하는 방법은 다음과 같습니다(선택 사항이며 기본값은 `parallel_run_step.txt`).
- `source_directory`: 컴퓨팅 대상에서 실행할 모든 파일이 포함된 폴더 경로입니다(선택 사항).
- `compute_target`: `AmlCompute`만 지원됩니다.
- `node_count`: 사용자 스크립트를 실행하는 데 사용되는 컴퓨팅 노드의 수입니다.
- `process_count_per_node`: 항목 스크립트를 병렬 실행할 노드당 작업자 프로세스 수입니다. GPU 머신의 경우 기본값은 1입니다. CPU 머신의 경우 기본값은 노드당 코어 수입니다. 작업자 프로세스는 가져온 미니 일괄 처리를 전달하여 `run()`을 반복적으로 호출합니다. 작업에서 실행되는 작업자 프로세스의 총수는 `process_count_per_node * node_count`이며 이 수에 따라 병렬로 실행되는 `run()`의 최대 수가 결정됩니다.
- `environment`: Python 환경 정의입니다. 기존 Python 환경을 사용하도록 구성할 수도 있고 임시 환경을 설정하도록 구성할 수도 있습니다. 이 정의는 필요한 애플리케이션 종속성을 설정하는 역할도 담당합니다(선택 사항).
- `logging_level`: 로그의 자세한 정도입니다. 값의 자세한 정도는 `WARNING`, `INFO`, `DEBUG` 순서로 높아집니다. (선택 사항이며 기본값은 `INFO`)
- `run_invocation_timeout`: `run()` 메서드 호출 시간 제한(초)입니다. (선택 사항이며 기본값은 `60`)
- `run_max_try`: 미니 일괄 처리의 최대 `run()` 시도 횟수입니다. 예외가 throw되거나 `run_invocation_timeout`에 도달할 때 아무 것도 반환되지 않으면 `run()`이 실패합니다(선택 사항이며 기본값은 `3`).

`mini_batch_size`, `node_count`, `process_count_per_node`, `logging_level`, `run_invocation_timeout` 및 `run_max_try`를 `PipelineParameter`로 지정할 수 있습니다. 그러면 파이프라인 실행을 다시 제출할 때 매개 변수 값을 세밀하게 조정할 수 있습니다. 이 예에서는 `mini_batch_size` 및 `Process_count_per_node`에 `PipelineParameter`를 사용하고 다른 실행을 다시 제출할 때 이 값을 변경할 것입니다.

#### <a name="cuda-devices-visibility"></a>CUDA 디바이스 표시 유형
GPU가 탑재된 컴퓨팅 대상의 경우 환경 변수 `CUDA_VISIBLE_DEVICES`는 작업자 프로세스에서 설정됩니다. AmlCompute에서는 환경 변수 `AZ_BATCHAI_GPU_COUNT_FOUND`가 자동으로 설정되며 이 변수에서 GPU 디바이스의 총수를 확인할 수 있습니다. 각 작업자 프로세스에 전용 GPU가 할당되도록 하려면 `process_count_per_node`를 머신의 GPU 디바이스 수와 동일하게 설정합니다. 각 작업자 프로세스는 `CUDA_VISIBLE_DEVICES`에 고유 인덱스를 할당합니다. 작업자 프로세스가 어떤 이유로든 중지되면 다음에 시작되는 작업자 프로세스가 해제된 GPU 인덱스를 사용합니다.

GPU 디바이스의 총수가 `process_count_per_node` 미만인 경우 GPU 인덱스를 모두 사용할 때까지 작업자 프로세스에 GPU 인덱스가 할당됩니다.

예를 들어, GPU 디바이스의 총수가 2개이고 `process_count_per_node = 4`인 경우 프로세스 0과 프로세스 1에는 인덱스 0과 1이 할당됩니다. 프로세스 2와 3에는 환경 변수가 할당되지 않습니다. GPU 할당에 이 환경 변수를 사용하는 라이브러리의 경우 프로세스 2와 3에는 GPU가 사용되지 않고 GPU 디바이스를 차지하려고 시도하지 않습니다. 프로세스 0이 중지되면 GPU 인덱스 0이 해제됩니다. GPU 인덱스 0은 다음 프로세스인 프로세스 4에 할당됩니다.

자세한 내용은 [CUDA Pro 팁: CUDA_VISIBLE_DEVICES로 GPU 표시 유형 제어](https://developer.nvidia.com/blog/cuda-pro-tip-control-gpu-visibility-cuda_visible_devices/)를 참조하세요.

### <a name="parameters-for-creating-the-parallelrunstep"></a>ParallelRunStep 만들기에 대한 매개 변수

스크립트, 환경 구성 및 매개 변수를 사용하여 ParallelRunStep을 만듭니다. 유추 스크립트의 실행 대상으로 작업 영역에 이미 연결한 컴퓨팅 대상을 지정합니다. `ParallelRunStep`을 사용하여 다음 매개 변수를 모두 사용하는 일괄 처리 유추 파이프라인 단계를 만듭니다.
- `name`: 단계의 이름이며, 고유한 3-32자 문자와 regex ^\[a-z\]([-a-z0-9]*[a-z0-9])?$로 구성되어야 한다는 명명 제한이 있습니다.
- `parallel_run_config`: 이전에 정의된 `ParallelRunConfig` 개체입니다.
- `inputs`: 병렬 처리를 위해 분할하려는 하나 이상의 단일 형식 Azure Machine Learning 데이터 세트입니다.
- `side_inputs`: 분할할 필요 없이 측면 입력으로 사용되는 하나 이상의 참조 데이터 또는 데이터 세트입니다.
- `output`: 출력 데이터를 저장할 디렉터리 경로를 나타내는 `OutputFileDatasetConfig` 개체입니다.
- `arguments`: 사용자 스크립트에 전달된 인수 목록입니다. 항목 스크립트에서 인수를 검색하려면 unknown_args를 사용합니다(선택 사항).
- `allow_reuse`: 동일한 설정/입력으로 실행할 때 이전 결과를 단계에 다시 사용할 것인지 여부를 지정합니다. 이 매개 변수가 `False`이면 파이프라인 실행 중에 이 단계에 대해 항상 새로운 실행이 생성됩니다. (선택 사항이며 기본값은 `True`)

```python
from azureml.pipeline.steps import ParallelRunStep

parallelrun_step = ParallelRunStep(
    name="predict-digits-mnist",
    parallel_run_config=parallel_run_config,
    inputs=[input_mnist_ds_consumption],
    output=output_dir,
    allow_reuse=True
)
```

## <a name="debugging-scripts-from-remote-context"></a>원격 컨텍스트에서 스크립트 디버그

채점 스크립트의 로컬 디버깅을 실제 파이프라인에서의 디버깅으로 전환하는 것은 어려운 작업일 수 있습니다. 포털에서 로그 찾기에 대한 자세한 내용은 [원격 컨텍스트에서 스크립트 디버깅에 관한 기계 학습 파이프라인 섹션](how-to-debug-pipelines.md)을 참조하세요. 해당 섹션의 정보는 ParallelRunStep에도 적용됩니다.

예를 들어 로그 파일 `70_driver_log.txt`에는 ParallelRunStep 코드를 시작하는 컨트롤러의 정보가 포함됩니다.

ParallelRunStep 작업의 분산 특성으로 인해 여러 원본의 로그가 있습니다. 그러나 다음과 같이 높은 수준의 정보를 제공하는 두 통합 파일이 생성됩니다.

- `~/logs/job_progress_overview.txt`: 이 파일은 지금까지 생성된 미니 일괄 처리(작업)의 수와 지금까지 처리된 미니 일괄 처리 수에 대한 높은 수준의 정보를 제공합니다. 이 끝에서는 작업의 결과를 보여 줍니다. 작업이 실패할 경우 오류 메시지와, 문제 해결을 시작할 지점이 표시됩니다.

- `~/logs/sys/master_role.txt`: 이 파일은 실행 중인 작업의 주체 노드(오케스트레이터라고도 함) 보기를 제공합니다. 작업 만들기, 진행률 모니터링, 실행 결과가 포함됩니다.

EntryScript 도우미 및 print 문을 사용하여 항목 스크립트에서 생성된 로그는 다음 파일에 있습니다.

- `~/logs/user/entry_script_log/<node_id>/<process_name>.log.txt`: 이 파일은 EntryScript 도우미를 사용하여 entry_script에서 작성된 로그입니다.

- `~/logs/user/stdout/<node_id>/<process_name>.stdout.txt`: 이 파일은 entry_script의 stdout(예: print 문)에 대한 로그입니다.

- `~/logs/user/stderr/<node_id>/<process_name>.stderr.txt`: 이 파일은 entry_script의 stderr에 대한 로그입니다.

스크립트의 오류에 대한 간략한 해석은 다음과 같습니다.

- `~/logs/user/error.txt`: 이 파일은 스크립트의 오류를 요약하려 합니다.

스크립트의 오류에 대한 자세한 내용은 다음과 같습니다.

- `~/logs/user/error/`: 항목 스크립트를 로드하고 실행하는 동안 throw된 예외에 대한 전체 스택 추적을 포함합니다.

각 노드가 점수 스크립트를 실행하는 방법을 완전히 해석하려면 각 노드에 대한 개별 프로세스 로그를 확인합니다. 프로세스 로그는 작업자 노드에서 그룹화한 `sys/node` 폴더에 있습니다.

- `~/logs/sys/node/<node_id>/<process_name>.txt`: 이 파일은 작업자가 선택하거나 완료할 때 각각의 미니 일괄 처리에 대한 상세 정보를 제공합니다. 각 미니 일괄 처리에 대해 이 파일에는 다음이 포함됩니다.

    - 작업자 프로세스의 IP 주소 및 PID
    - 전체 항목 수, 성공적으로 처리된 항목 수, 실패한 항목 수
    - 시작 시간, 기간, 처리 시간 및 실행 메서드 시간.

또한 각 노드의 리소스 사용량에 대한 정기 확인 결과를 볼 수 있습니다. 로그 파일 및 설정 파일은 다음 폴더에 있습니다.

- `~/logs/perf`: 확인 간격(초)을 변경하려면 `--resource_monitor_interval`을 설정합니다. 기본 간격은 `600`입니다. 이는 약 10분입니다. 모니터링을 중지하려면 값을 `0`으로 설정합니다. 각 `<node_id>` 폴더에는 다음이 포함됩니다.

    - `os/`: 노드에서 실행 중인 모든 프로세스에 대한 정보입니다. 한 번 확인 시 운영 체제 명령을 실행하고 결과를 파일에 저장합니다. 명령은 Linux의 경우 `ps`이고 Windows의 경우 `tasklist`입니다.
        - `%Y%m%d%H`: 하위 폴더 이름은 시간(시)입니다.
            - `processes_%M`: 파일 이름은 확인 시간(분)으로 끝납니다.
    - `node_disk_usage.csv`: 노드의 자세한 디스크 사용량입니다.
    - `node_resource_usage.csv`: 노드의 리소스 사용량 개요입니다.
    - `processes_resource_usage.csv`: 각 프로세스의 리소스 사용량 개요입니다.

## <a name="how-do-i-log-from-my-user-script-from-a-remote-context"></a>원격 컨텍스트의 내 사용자 스크립트는 어떻게 기록하나요?

ParallelRunStep은 process_count_per_node를 기반으로 한 노드에서 여러 프로세스를 실행할 수 있습니다. 노드의 각 프로세스에서 로그를 구성하고 print 및 log 문을 결합하려면 아래와 같이 ParallelRunStep 로거를 사용하는 것이 좋습니다. EntryScript에서 로거를 가져오고 로그가 포털의 **logs/user** 폴더에 표시되도록 합니다.

**로거를 사용 하는 샘플 항목 스크립트:**
```python
from azureml_user.parallel_run import EntryScript

def init():
    """Init once in a worker process."""
    entry_script = EntryScript()
    logger = entry_script.logger
    logger.debug("This will show up in files under logs/user on the Azure portal.")


def run(mini_batch):
    """Call once for a mini batch. Accept and return the list back."""
    # This class is in singleton pattern and will return same instance as the one in init()
    entry_script = EntryScript()
    logger = entry_script.logger
    logger.debug(f"{__file__}: {mini_batch}.")
    ...

    return mini_batch
```

## <a name="where-does-the-message-from-python-logging-sink-to"></a>Python `logging`의 메시지는 어디에 싱크되나요?
ParallelRunStep은 루트 로거의 처리기를 설정하며 이 처리기에서 메시지를 `logs/user/stdout/<node_id>/processNNN.stdout.txt`로 싱크합니다.

`logging`은 `WARNING` 수준으로 기본 설정됩니다. 기본적으로 `INFO` 또는 `DEBUG`과 같은 `WARNING` 아래의 수준은 표시되지 않습니다.

## <a name="how-could-i-write-to-a-file-to-show-up-in-the-portal"></a>파일에 쓰기 작업을 수행하여 포털에 표시하려면 어떻게 해야 하나요?
`logs` 폴더의 파일은 포털에 업로드되어 표시됩니다.
아래와 같이 `logs/user/entry_script_log/<node_id>` 폴더를 가져와 쓰기 작업을 수행할 파일 경로를 작성할 수 있습니다.

```python
from pathlib import Path
from azureml_user.parallel_run import EntryScript

def init():
    """Init once in a worker process."""
    entry_script = EntryScript()
    log_dir = entry_script.log_dir
    log_dir = Path(entry_script.log_dir)  # logs/user/entry_script_log/<node_id>/.
    log_dir.mkdir(parents=True, exist_ok=True) # Create the folder if not existing.

    proc_name = entry_script.agent_name  # The process name in pattern "processNNN".
    fil_path = log_dir / f"{proc_name}_<file_name>" # Avoid conflicting among worker processes with proc_name.
```

## <a name="how-to-handle-log-in-new-processes"></a>새 프로세스의 로그를 처리 하는 방법
모듈을 사용 하 여 스크립트를 입력 하 여 새 프로세스를 생성 하 [`subprocess`](https://docs.python.org/3/library/subprocess.html) 고, 입력/출력/오류 파이프에 연결 하 고, 반환 코드를 가져올 수 있습니다.

에서 함수를 사용 하는 것이 좋습니다 [`run()`](https://docs.python.org/3/library/subprocess.html#subprocess.run) `capture_output=True` . 에 오류가 표시 됩니다 `logs/user/error/<node_id>/<process_name>.txt` .

를 사용 하려는 경우 `Popen()` 다음과 같이 stdout/stderr을 파일로 리디렉션해야 합니다.
```python
from pathlib import Path
from subprocess import Popen

from azureml_user.parallel_run import EntryScript


def init():
    """Show how to redirect stdout/stderr to files in logs/user/entry_script_log/<node_id>/."""
    entry_script = EntryScript()
    proc_name = entry_script.agent_name  # The process name in pattern "processNNN".
    log_dir = Path(entry_script.log_dir)  # logs/user/entry_script_log/<node_id>/.
    log_dir.mkdir(parents=True, exist_ok=True) # Create the folder if not existing.
    stdout_file = str(log_dir / f"{proc_name}_demo_stdout.txt")
    stderr_file = str(log_dir / f"{proc_name}_demo_stderr.txt")
    proc = Popen(
        ["...")],
        stdout=open(stdout_file, "w"),
        stderr=open(stderr_file, "w"),
        # ...
    )

```

> [!NOTE]
> 작업자 프로세스가 동일한 프로세스에서 "시스템" 코드와 항목 스크립트 코드를 실행 합니다.
>
> `stdout`을 지정 하지 않거나 지정 하지 않으면 `stderr` 항목 스크립트에서로 만든 하위 프로세스는 `Popen()` 작업자 프로세스의 설정을 상속 합니다.
>
> `stdout`는 `logs/sys/node/<node_id>/processNNN.stdout.txt`에, `stderr`은 `logs/sys/node/<node_id>/processNNN.stderr.txt`에 쓰입니다.


## <a name="how-do-i-write-a-file-to-the-output-directory-and-then-view-it-in-the-portal"></a>출력 디렉터리에 파일을 쓴 다음, 이 파일을 포털에서 보려면 어떻게 할까요?

`EntryScript` 클래스에서 출력 디렉터리를 가져와 여기에 쓰기 작업을 수행할 수 있습니다. 쓰인 파일을 보려면 Azure Machine Learning 포털의 실행 단계 뷰에서 **출력 + 로그** 탭을 선택합니다. **데이터 출력** 링크를 선택한 다음, 대화 상자에 설명된 단계를 완료합니다.

다음 예제와 같이 항목 스크립트에 `EntryScript`를 사용합니다.

```python
from pathlib import Path
from azureml_user.parallel_run import EntryScript

def run(mini_batch):
    output_dir = Path(entry_script.output_dir)
    (Path(output_dir) / res1).write...
    (Path(output_dir) / res2).write...
```

## <a name="how-can-i-pass-a-side-input-such-as-a-file-or-files-containing-a-lookup-table-to-all-my-workers"></a>조회 테이블이 포함된 파일과 같은 사이드 입력을 모든 작업자에게 전달하려면 어떻게 해야 하나요?

사용자는 ParalleRunStep의 side_inputs 매개 변수를 사용하여 스크립트에 참조 데이터를 전달할 수 있습니다. side_inputs으로 제공되는 모든 데이터 세트는 각 작업자 노드에 탑재됩니다. 사용자는 인수를 전달하여 탑재 위치를 가져올 수 있습니다.

참조 데이터를 포함하는 [데이터 세트](/python/api/azureml-core/azureml.core.dataset.dataset)를 생성하고 로컬 탑재 경로를 지정한 다음, 작업 영역에 등록합니다. `ParallelRunStep`의 `side_inputs` 매개 변수에 전달합니다. 또한 `arguments` 섹션에서 해당 경로를 추가하여 탑재된 경로에 쉽게 액세스할 수 있습니다.

> [!NOTE]
> FileDatasets는 side_inputs에만 사용합니다.

```python
local_path = "/tmp/{}".format(str(uuid.uuid4()))
label_config = label_ds.as_named_input("labels_input").as_mount(local_path)
batch_score_step = ParallelRunStep(
    name=parallel_step_name,
    inputs=[input_images.as_named_input("input_images")],
    output=output_dir,
    arguments=["--labels_dir", label_config],
    side_inputs=[label_config],
    parallel_run_config=parallel_run_config,
)
```

그런 다음, 다음과 같이 추론 스크립트(예: init () 메서드)에서 액세스할 수 있습니다.

```python
parser = argparse.ArgumentParser()
parser.add_argument('--labels_dir', dest="labels_dir", required=True)
args, _ = parser.parse_known_args()

labels_path = args.labels_dir
```

## <a name="how-to-use-input-datasets-with-service-principal-authentication"></a>서비스 주체 인증으로 입력 데이터 세트를 사용하려면 어떻게 해야 하나요?
사용자는 작업 영역에서 사용되는 서비스 주체 인증으로 입력 데이터 세트를 전달할 수 있습니다. ParallelRunStep에서 이러한 데이터 세트를 사용하려면 ParallelRunStep 구성을 생성하기 위해 해당 데이터 세트를 등록해야 합니다.

```python
service_principal = ServicePrincipalAuthentication(
    tenant_id="***",
    service_principal_id="***",
    service_principal_password="***")

ws = Workspace(
    subscription_id="***",
    resource_group="***",
    workspace_name="***",
    auth=service_principal
    )

default_blob_store = ws.get_default_datastore() # or Datastore(ws, '***datastore-name***')
ds = Dataset.File.from_files(default_blob_store, '**path***')
registered_ds = ds.register(ws, '***dataset-name***', create_new_version=True)
```

## <a name="how-to-check-progress-and-analyze-it"></a>진행 상태 확인 및 분석 방법
이 섹션에서는 ParallelRunStep 작업의 진행 상태를 확인하고 예기치 않은 동작의 원인을 확인하는 방법에 대해 설명합니다.

### <a name="how-to-check-job-progress"></a>작업 진행 상태를 확인하는 방법은 무엇인가요?
`~/logs/job_progress_overview.<timestamp>.txt`에서는 StepRun의 전반적인 상태를 살펴보는 것 외에도 예약된/처리된 미니 일괄 처리 수와 출력이 생성되는 진행 상태를 볼 수 있습니다. 파일은 매일 회전하며 타임스탬프가 가장 큰 파일을 검사하여 최신 정보를 확인할 수 있습니다.

### <a name="what-should-i-check-if-there-is-no-progress-for-a-while"></a>한동안 아무것도 진행된 것이 없는 경우 무엇을 확인해야 하나요?
`~/logs/sys/errror`로 이동하여 예외가 있는지 확인할 수 있습니다. 예외가 없는 경우 항목 스크립트로 인해 시간이 오래 걸리는 것일 수 있습니다. 코드에서 진행 상태 정보를 출력하여 시간이 오래 걸리는 부분을 찾아내거나 `"--profiling_module", "cProfile"`을 `ParallelRunStep`의 `arguments`에 추가하여 `~/logs/sys/node/<node_id>` 폴더에 `<process_name>.profile`이라는 프로필 파일을 생성할 수 있습니다.

### <a name="when-will-a-job-stop"></a>작업은 언제 중지되나요?
취소하지 않는다면 작업은 다음 상태에서 중지됩니다.
- 완료됨. 모든 미니 일괄 처리가 처리되고 `append_row` 모드에 대해 출력이 생성된 경우입니다.
- 실패함. [`Parameters for ParallelRunConfig`](#parameters-for-parallelrunconfig)에서 `error_threshold`가 초과되었거나 작업 중에 시스템 오류가 발생한 경우입니다.

### <a name="where-to-find-the-root-cause-of-failure"></a>실패의 근본 원인은 어디에서 찾을 수 있나요?
`~logs/job_result.txt`의 리드를 따라 원인 및 자세한 오류 로그를 찾을 수 있습니다.

### <a name="will-node-failure-impact-the-job-result"></a>노드 실패가 작업 결과에 영향을 주나요?
지정된 컴퓨팅 클러스터에 사용 가능한 다른 노드가 있다면 영향을 주지 않습니다. 오케스트레이터는 실패한 노드를 대체하여 새 노드를 시작하고 ParallelRunStep은 해당 작업에 대해 복원력이 있습니다.

### <a name="what-happens-if-init-function-in-entry-script-fails"></a>항목 스크립트의 `init` 함수가 실패하면 어떻게 되나요?
ParallelRunStep에는 특정 시간 동안 재시도를 통해 일시적인 문제에서 복구할 수 있는 기회를 제공하여 작업 실패가 너무 오래 지속되지 않도록 하는 메커니즘이 있습니다. 해당 메커니즘은 다음과 같습니다.
1. 노드가 시작된 후 모든 에이전트의 `init`이 계속 실패하는 경우 `3 * process_count_per_node`회 실패 후에 시도를 중지합니다.
2. 작업이 시작된 후 모든 노드의 모든 에이전트의 `init`이 계속 실패하는 경우 작업 실행 시간이 2분을 초과하고 실패 횟수가 `2 * node_count * process_count_per_node`회가 되면 시도를 중지합니다.
3. 모든 에이전트가 `init`에서 `3 * run_invocation_timeout + 30`초 이상 중단되는 경우 너무 오랫동안 진행이 없어 작업이 실패합니다.

### <a name="what-will-happen-on-outofmemory-how-can-i-check-the-cause"></a>OutOfMemory에서는 어떤 현상이 발생하나요? 원인을 확인하려면 어떻게 해야 하나요?
ParallelRunStep은 미니 일괄 처리를 처리하려는 현재 시도를 실패 상태로 설정하고 실패한 프로세스를 다시 시작하려고 시도합니다. `~logs/perf/<node_id>`를 확인하여 메모리 사용량이 많은 프로세스를 찾을 수 있습니다.

### <a name="why-do-i-have-a-lot-of-processnnn-files"></a>processNNN 파일이 많은 이유는 무엇인가요?
ParallelRunStep은 비정상적으로 종료된 작업자 프로세스를 대체하는 새 작업자 프로세스를 시작하고 각 프로세스는 `processNNN` 파일을 로그로 생성합니다. 그러나 사용자 스크립트의 `init` 함수가 실행되는 동안 예외로 인해 프로세스가 실패하고 오류가 지속적으로 `3 * process_count_per_node`회 반복되는 경우 새 작업자 프로세스가 시작되지 않습니다.

## <a name="next-steps"></a>다음 단계

* [Azure Machine Learning 파이프라인을 보여주는 Jupyter Notebook](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/machine-learning-pipelines)을 참조하세요.

* [azureml-pipeline-steps](/python/api/azureml-pipeline-steps/azureml.pipeline.steps) 패키지에 대한 도움말은 SDK 참조를 확인하세요.

* ParallelRunStep 클래스에 대 한 ParallelRunConfig 클래스 및 [설명서](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.parallelrunstep) 에 대 한 참조 [설명서](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.parallelrunconfig) 를 봅니다.

* ParallelRunStep으로 파이프라인 사용에 관한 [고급 자습서](tutorial-pipeline-batch-scoring-classification.md)를 따르세요. 이 자습서에서는 다른 파일을 측면 입력으로 전달하는 방법을 보여줍니다.
