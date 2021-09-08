---
title: 분산 GPU 학습 가이드
titleSuffix: Azure Machine Learning
description: MPI, Horovod, DeepSpeed, PyTorch, PyTorch Lightning, Hugging Face Transformers, TensorFlow, InfiniBand 같은 Azure Machine Learning 지원 프레임워크를 사용하여 분산 교육을 수행하기 위한 모범 사례를 알아봅니다.
author: fuhuifang
ms.author: fufang
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 08/19/2021
ms.openlocfilehash: ff95cb0004fbde34e70bcb778d93a2f381dc5830
ms.sourcegitcommit: c2f0d789f971e11205df9b4b4647816da6856f5b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/23/2021
ms.locfileid: "122661657"
---
# <a name="distributed-gpu-training-guide"></a>분산 GPU 학습 가이드

Azure ML(Machine Learning)에서 분산 GPU 학습 코드를 사용하는 방법에 대해 자세히 알아봅니다. 이 문서에서는 분산 학습에 대해 설명하지 않습니다.  이는 Azure Machine Learning에서 기존 분산 학습 코드를 실행하는 데 도움이 됩니다. 또한 각 프레임워크에 따라 수행할 수 있는 팁과 예제가 나와 있습니다.

* MPI(메시지 전달 인터페이스)
    * Horovod
    * DeepSpeed
    * Open MPI의 환경 변수
* PyTorch
    * 프로세스 그룹 초기화
    * 시작 옵션
    * DistributedDataParallel(프로세스별 시작)
    * `torch.distributed.launch` 사용(노드별 시작)
    * PyTorch Lightning
    * Hugging Face 변환기
* TensorFlow
    * TensorFlow 환경 변수(TF_CONFIG)
* InfiniBand를 통해 GPU 학습 가속화

## <a name="prerequisites"></a>전제 조건

_데이터 병렬 처리_, _분산 데이터 병렬 처리_, _모델 병렬 처리_ 등 [분산 GPU 학습의 기본 개념](concept-distributed-training.md)을 검토합니다.

> [!TIP]
> 사용할 병렬 처리의 유형을 모르는 경우, 90% 이상 __분산 데이터 병렬 처리__ 를 사용해야 합니다.

## <a name="mpi"></a>MPI

Azure ML은 각 노드에 지정된 수의 프로세스를 시작하는 [MPI 작업](https://www.mcs.anl.gov/research/projects/mpi/)을 제공합니다. 이 접근 방식을 채택하여 `process_count_per_node`가 노드별 시작 관리자에 대해 1(기본값)로 설정되었는지, 아니면 프로세스별 시작 관리자의 디바이스/GPU 수와 같은지에 따라 프로세스별 시작 관리자 또는 노드별 시작 관리자 중 하나를 사용하여 분산 학습을 실행할 수 있습니다. Azure ML은 백그라운드에서 전체 MPI 시작 명령(`mpirun`)을 생성합니다.  `mpirun` 또는 `DeepSpeed launcher` 같은 사용자 고유의 전체 헤드 노드 시작 관리자 명령을 제공할 수 없습니다.

> [!TIP]
> Azure Machine Learning MPI 작업에서 사용되는 기본 Docker 이미지에는 MPI 라이브러리가 설치되어 있어야 합니다. [개방형 MPI](https://www.open-mpi.org/)는 모든 [AzureML GPU 기본 이미지](https://github.com/Azure/AzureML-Containers)에 포함되어 있습니다. 사용자 지정 Docker 이미지를 사용하는 경우, 이미지에 MPI 라이브러리가 포함되어 있는지 확인해야 합니다. 개방형 MPI를 사용하는 것이 좋지만, Intel MPI와 같은 다른 MPI 구현을 사용할 수도 있습니다. 또한 Azure ML은 인기 프레임워크용으로 [큐레이팅된 환경](resource-curated-environments.md)을 제공합니다. 

MPI를 사용하여 분산 학습을 실행하려면 다음 단계를 수행합니다.

1. 기본 딥 러닝 프레임워크와 MPI가 있는 Azure ML 환경을 사용합니다. AzureML은 인기 프레임워크용으로 [큐레이팅된 환경](resource-curated-environments.md)을 제공합니다.
1. `process_count_per_node`와 `node_count`를 사용하여 `MpiConfiguration`을 정의합니다. `process_count_per_node`는 프로세스별 시작의 경우 노드별 GPU 수와 같아야 하고, 사용자 스크립트로 노드별 프로세스를 시작해야 하는 경우 노드별 시작에 대해 1(기본값)로 설정해야 합니다.
1. `MpiConfiguration` 개체를 `ScriptRunConfig`의 `distributed_job_config` 매개 변수로 전달합니다.

```python
from azureml.core import Workspace, ScriptRunConfig, Environment, Experiment
from azureml.core.runconfig import MpiConfiguration

curated_env_name = 'AzureML-PyTorch-1.6-GPU'
pytorch_env = Environment.get(workspace=ws, name=curated_env_name)
distr_config = MpiConfiguration(process_count_per_node=4, node_count=2)

run_config = ScriptRunConfig(
  source_directory= './src',
  script='train.py',
  compute_target=compute_target,
  environment=pytorch_env,
  distributed_job_config=distr_config,
)

# submit the run configuration to start the job
run = Experiment(ws, "experiment_name").submit(run_config)
```

### <a name="horovod"></a>Horovod

딥 러닝 프레임워크를 사용하는 분산 학습에 [Horovod](https://horovod.readthedocs.io/en/stable/index.html)를 이용하는 경우 MPI 작업 구성을 사용합니다.

코드가 다음 팁을 따르는지 확인합니다.

* 학습 코드는 Azure ML 파트를 추가하기 전에 Horovod를 통해 올바르게 계측됩니다.
* Azure ML 환경에는 Horovod 및 MPI가 포함되어 있습니다. PyTorch 및 TensorFlow 큐레이팅된 GPU 환경은 Horovod와 해당 종속성을 사용하여 미리 구성됩니다.
* 원하는 배포를 사용하여 `MpiConfiguration`을 만듭니다.

### <a name="horovod-example"></a>Horovod 예제

* [azureml-examples: Horovod를 사용한 TensorFlow 분산 학습](https://github.com/Azure/azureml-examples/tree/main/python-sdk/workflows/train/tensorflow/mnist-distributed-horovod)

### <a name="deepspeed"></a>DeepSpeed

Azure ML에서 [DeepSpeed](https://www.deepspeed.ai/) 라이브러리로 분산 학습을 실행하는 데 DeepSpeed의 사용자 지정 시작 관리자를 사용하지 마세요. 대신 [MPI로](https://www.deepspeed.ai/getting-started/#mpi-and-azureml-compatibility) 학습 작업을 시작하도록 MPI 작업을 구성합니다.

코드가 다음 팁을 따르는지 확인합니다.

* Azure ML 환경에는 DeepSpeed와 해당 종속성, 개방형 MPI 및 mpi4py가 포함되어 있습니다.
* 배포를 사용하여 `MpiConfiguration`을 만듭니다.

### <a name="deepseed-example"></a>DeepSeed 예제

* [azureml-examples: CIFAR-10에서 DeepSpeed를 사용하여 분산 학습](https://github.com/Azure/azureml-examples/tree/main/python-sdk/workflows/train/deepspeed/cifar)

### <a name="environment-variables-from-open-mpi"></a>Open MPI의 환경 변수

개방형 MPI 이미지로 MPI 작업을 실행할 때 각 프로세스에 대해 다음의 환경 변수가 시작되었습니다.

1. `OMPI_COMM_WORLD_RANK` - 프로세스의 순위
2. `OMPI_COMM_WORLD_SIZE` - 세계 크기
3. `AZ_BATCH_MASTER_NODE` - `MASTER_ADDR:MASTER_PORT` 포트가 있는 기본 주소
4. `OMPI_COMM_WORLD_LOCAL_RANK` - 노드에 있는 프로세스의 로컬 순위
5. `OMPI_COMM_WORLD_LOCAL_SIZE` - 노드의 프로세스 수

> [!TIP]
> 이름에도 불구하고 `OMPI_COMM_WORLD_NODE_RANK` 환경 변수는 `NODE_RANK`에 해당하지 않습니다. 노드별 시작 관리자를 사용하려면 `process_count_per_node=1`을 설정하고 `OMPI_COMM_WORLD_RANK`를 `NODE_RANK`로 사용합니다.

## <a name="pytorch"></a>PyTorch

Azure ML은 PyTorch의 네이티브 분산 학습 기능(`torch.distributed`)을 사용하여 분산 작업 실행을 지원합니다.

> [!TIP]
> 데이터 병렬 처리 시 [공식 PyTorch 지침](https://pytorch.org/tutorials/intermediate/ddp_tutorial.html#comparison-between-dataparallel-and-distributeddataparallel)은 단일 노드 및 다중 노드 분산 학습에 모두 DataParallel을 통해 DDP(DistributedDataParallel)를 사용하는 것입니다. 또한 PyTorch에서는 [다중 처리 패키지에 대해 DistributedDataParallel을 사용하는 것이 좋습니다](https://pytorch.org/docs/stable/notes/cuda.html#use-nn-parallel-distributeddataparallel-instead-of-multiprocessing-or-nn-dataparallel). 따라서 Azure Machine Learning 설명서와 예제는 DistributedDataParallel 학습에 초점을 맞춥니다.

### <a name="process-group-initialization"></a>프로세스 그룹 초기화

분산 학습의 백본은 서로를 알고, 백 엔드를 사용해 서로 통신할 수 있는 프로세스 그룹을 기반으로 합니다. PyTorch의 경우 프로세스 그룹이 __모든 분산 프로세스__ 에서 [torch.distributed.init_process_group](https://pytorch.org/docs/stable/distributed.html#torch.distributed.init_process_group)을 호출하여 만들어져 총칭하여 프로세스 그룹을 형성합니다.

```
torch.distributed.init_process_group(backend='nccl', init_method='env://', ...)
```

사용되는 가장 일반적인 통신 백 엔드는 `mpi`, `nccl`, `gloo`입니다. `nccl` GPU 기반 학습은 최고의 성능을 발휘하려는 경우에 권장되며, 가능할 때마다 사용해야 합니다. 

`init_method`는 각 프로세스가 서로를 검색하는 방법, 통신 백 엔드를 사용하여 프로세스 그룹을 초기화하고 확인하는 방법을 알려 줍니다. 기본적으로 `init_method`가 지정되어 있지 않으면 PyTorch에서는 환경 변수 초기화 메서드(`env://`)를 사용합니다. `init_method`는 Azure ML에서 분산 PyTorch를 실행하기 위한 학습 코드에서 사용하도록 권장되는 초기화 방법입니다.  PyTorch는 초기화를 위해 다음의 환경 변수를 찾습니다.

- **`MASTER_ADDR`** - 0순위의 프로세스를 호스팅할 컴퓨터의 IP 주소입니다.
- **`MASTER_PORT`** - 0순위의 프로세스를 호스팅할 컴퓨터에서 사용 가능한 포트입니다.
- **`WORLD_SIZE`** - 프로세스의 총수입니다. 분산 학습에 사용되는 디바이스의 총수(GPU)와 같아야 합니다.
- **`RANK`** - 현재 프로세스의 순위(전체)입니다. 가능한 값은 0~(세계 크기 - 1)입니다.

프로세스 그룹 초기화에 대한 자세한 내용은 [PyTorch 설명서](https://pytorch.org/docs/stable/distributed.html#torch.distributed.init_process_group)를 참조하세요.

이외에도 많은 애플리케이션에서 다음과 같은 환경 변수도 필요합니다.
- **`LOCAL_RANK`** - 노드 내 프로세스의 로컬 순위(상대)입니다. 가능한 값은 0~(노드의 프로세스 수 - 1)입니다. 이 정보는 데이터 준비와 같은 많은 작업이 노드별로 한 번만 수행되어야 하기 때문에 유용합니다(일반적으로 local_rank = 0).
- **`NODE_RANK`** - 다중 노드 학습을 위한 노드의 순위입니다. 가능한 값은 0~(총 노드 수 - 1)입니다.

### <a name="pytorch-launch-options"></a>PyTorch 시작 옵션

Azure ML PyTorch 작업은 분산 학습을 시작하기 위한 두 가지 유형의 옵션을 지원합니다.

- __프로세스별 시작 관리자__: 시스템은 프로세스 그룹을 설정하기 위한 관련 정보(예: 환경 변수)를 모두 사용하여 모든 분산 프로세스를 시작합니다.
- __노드별 시작 관리자__: 각 노드에서 실행될 유틸리티 시작 관리자를 Azure ML에 제공합니다. 유틸리티 시작 관리자가 지정된 노드에서 각 프로세스의 시작을 처리합니다. 각 노드 내에서 로컬로 `RANK` 및 `LOCAL_RANK`가 관리자에 의해 설정됩니다. **torch.distributed.launch** 유틸리티와 PyTorch Lightning은 모두 이 범주에 속합니다.

이러한 시작 옵션 간에는 근본적인 차이점이 없습니다. 선택은 주로 사용자 기본 설정 또는 vanilla PyTorch를 기반으로 빌드된 프레임워크/라이브러리(예: Lightning 또는 Hugging Face)의 규칙에 따라 결정됩니다.

다음 섹션에서는 각 시작 옵션에 대해 Azure ML PyTorch 작업을 구성하는 방법을 자세히 설명합니다.

### <a name="distributeddataparallel-per-process-launch"></a><a name="per-process-launch"></a> DistributedDataParallel(프로세스별 시작)

`torch.distributed.launch` 같은 시작 관리자 유틸리티를 사용할 필요가 없습니다. 분산 PyTorch 작업을 실행하려면 다음을 수행합니다.

1. 학습 스크립트 및 인수를 지정합니다.
1. `PyTorchConfiguration`을 만들고 `process_count`와 `node_count`를 지정합니다. `process_count`는 작업에 대해 실행하려는 총 프로세스 수에 해당합니다. `process_count`는 일반적으로 `# GPUs per node x # nodes`와 같아야 합니다. `process_count`가 지정되지 않은 경우 Azure ML에서는 기본적으로 노드당 하나의 프로세스를 시작합니다.

Azure ML에서 각 노드에 `MASTER_ADDR`, `MASTER_PORT`, `WORLD_SIZE`, `NODE_RANK` 환경 변수를 설정하고, 프로세스 수준 `RANK`, `LOCAL_RANK` 환경 변수를 설정합니다.

노드별 다중 프로세스 교육에 이 옵션을 사용하려면 Azure ML Python SDK `>= 1.22.0`을 사용합니다. Process_count가 1.22.0에 도입되었습니다.

```python
from azureml.core import ScriptRunConfig, Environment, Experiment
from azureml.core.runconfig import PyTorchConfiguration

curated_env_name = 'AzureML-PyTorch-1.6-GPU'
pytorch_env = Environment.get(workspace=ws, name=curated_env_name)
distr_config = PyTorchConfiguration(process_count=8, node_count=2)

run_config = ScriptRunConfig(
  source_directory='./src',
  script='train.py',
  arguments=['--epochs', 50],
  compute_target=compute_target,
  environment=pytorch_env,
  distributed_job_config=distr_config,
)

run = Experiment(ws, 'experiment_name').submit(run_config)
```

> [!TIP]
> 학습 스크립트가 local rank(로컬 순위) 또는 rank(순위)와 같은 정보를 스크립트 인수로 전달하는 경우 인수에서 환경 변수를 참조할 수 있습니다.
>
> ```python
> arguments=['--epochs', 50, '--local_rank', $LOCAL_RANK]
> ```

### <a name="pytorch-per-process-launch-example"></a>Pytorch 프로세스별 시작의 예제

- [azureml-examples: CIFAR-10에서 PyTorch를 사용하여 분산 학습](https://github.com/Azure/azureml-examples/tree/main/python-sdk/workflows/train/pytorch/cifar-distributed)

### <a name="using-torchdistributedlaunch-per-node-launch"></a><a name="per-node-launch"></a> `torch.distributed.launch` 사용(노드별 시작)

PyTorch는 노드별로 여러 프로세스를 시작하는 데 사용할 수 있는 [torch.distributed.launch](https://pytorch.org/docs/stable/distributed.html#launch-utility)에서 시작 유틸리티를 제공합니다. `torch.distributed.launch` 모듈은 각 노드에서 여러 학습 프로세스를 생성합니다.

다음 단계에서는 Azure ML에서 노드별 시작 관리자를 사용하여 PyTorch 작업을 구성하는 방법을 설명합니다.  이 작업은 다음 명령을 실행하는 것과 동일합니다.

```shell
python -m torch.distributed.launch --nproc_per_node <num processes per node> \
  --nnodes <num nodes> --node_rank $NODE_RANK --master_addr $MASTER_ADDR \
  --master_port $MASTER_PORT --use_env \
  <your training script> <your script arguments>
```

1. `torch.distributed.launch` 명령을 `ScriptRunConfig` 생성자의 `command` 매개 변수에 제공합니다. Azure ML은 학습 클러스터의 각 노드에서 이 명령을 실행합니다. `--nproc_per_node`는 각 노드에서 사용 가능한 GPU 수보다 작거나 같아야 합니다. MASTER_ADDR, MASTER_PORT, NODE_RANK는 모두 Azure ML에서 설정되므로, 명령의 환경 변수만 참조할 수 있습니다. Azure ML에서는 MASTER_PORT를 `6105`로 설정하지만, 원하는 경우 다른 값을 torch.distributed.launch 명령의 `--master_port` 인수에 전달할 수 있습니다. (시작 유틸리티는 환경 변수를 다시 설정합니다.)
2. `PyTorchConfiguration`을 만들고, `node_count`를 지정합니다.

```python
from azureml.core import ScriptRunConfig, Environment, Experiment
from azureml.core.runconfig import PyTorchConfiguration

curated_env_name = 'AzureML-PyTorch-1.6-GPU'
pytorch_env = Environment.get(workspace=ws, name=curated_env_name)
distr_config = PyTorchConfiguration(node_count=2)
launch_cmd = "python -m torch.distributed.launch --nproc_per_node 4 --nnodes 2 --node_rank $NODE_RANK --master_addr $MASTER_ADDR --master_port $MASTER_PORT --use_env train.py --epochs 50".split()

run_config = ScriptRunConfig(
  source_directory='./src',
  command=launch_cmd,
  compute_target=compute_target,
  environment=pytorch_env,
  distributed_job_config=distr_config,
)

run = Experiment(ws, 'experiment_name').submit(run_config)
```

> [!TIP]
> **단일 노드 다중 GPU 학습:** 시작 유틸리티를 사용하여 단일 노드 다중 GPU PyTorch 학습을 실행하는 경우, ScriptRunConfig의 `distributed_job_config` 매개 변수를 지정할 필요가 없습니다.
>
>```python
> launch_cmd = "python -m torch.distributed.launch --nproc_per_node 4 --use_env train.py --epochs 50".split()
>
> run_config = ScriptRunConfig(
>  source_directory='./src',
>  command=launch_cmd,
>  compute_target=compute_target,
>  environment=pytorch_env,
> )
> ```

### <a name="pytorch-per-node-launch-example"></a>PyTorch 노드별 시작의 예제

- [azureml-examples: CIFAR-10에서 PyTorch를 사용하여 분산 학습](https://github.com/Azure/azureml-examples/tree/main/python-sdk/workflows/train/pytorch/cifar-distributed)

### <a name="pytorch-lightning"></a>PyTorch Lightning

[PyTorch Lightning](https://pytorch-lightning.readthedocs.io/en/stable/)은 PyTorch에 대한 고급 인터페이스를 제공하는, 간단한 오픈 소스 라이브러리입니다. Lightning은 vanilla PyTorch에 필요한 하위 수준의 분산 학습 구성을 대부분 추상화합니다. Lightning을 사용하면 단일 GPU, 단일 노드 다중 GPU 및 다중 노드 다중 GPU 설정에서 학습 스크립트를 실행할 수 있습니다. 백그라운드에서 `torch.distributed.launch`와 유사한 여러 프로세스가 시작됩니다.

단일 노드 학습(단일 노드 다중 GPU 포함)의 경우 `distributed_job_config`를 지정하지 않아도 Azure ML에서 코드를 실행할 수 있습니다. 다중 노드 학습의 경우 Lightning을 사용하려면 학습 클러스터의 각 노드에 대해 다음 환경 변수를 설정해야 합니다.

- MASTER_ADDR
- MASTER_PORT
- NODE_RANK

Azure ML에서 다중 노드 Lightning 학습을 실행하려면, 주로 [노드별 시작 가이드](#per-node-launch)를 따르면 됩니다.

- `PyTorchConfiguration`을 정의하고 `node_count`를 지정합니다. Lightning은 각 노드에 대한 작업자 프로세스 시작 작업을 내부적으로 처리하므로, `process_count`를 지정하지 않습니다.
- PyTorch 작업의 경우 Azure ML에서 Lightning에 필요한 MASTER_ADDR, MASTER_PORT 및 NODE_RANK 환경 변수 설정 작업을 처리합니다.
- Lightning은 `--gpus` 및 `--num_nodes` 트레이너 플래그에서 세계의 크기를 계산하고 순위와 로컬 순위를 내부적으로 관리합니다.

```python
from azureml.core import ScriptRunConfig, Experiment
from azureml.core.runconfig import PyTorchConfiguration

nnodes = 2
args = ['--max_epochs', 50, '--gpus', 2, '--accelerator', 'ddp', '--num_nodes', nnodes]
distr_config = PyTorchConfiguration(node_count=nnodes)

run_config = ScriptRunConfig(
  source_directory='./src',
  script='train.py',
  arguments=args,
  compute_target=compute_target,
  environment=pytorch_env,
  distributed_job_config=distr_config,
)

run = Experiment(ws, 'experiment_name').submit(run_config)
```

### <a name="pytorch-lightning-example"></a>PyTorch Lightning의 예제

* [azureml-examples: PyTorch Lightning을 사용하여 다중 노드 학습](https://github.com/Azure/azureml-examples/blob/main/python-sdk/experimental/using-pytorch-lightning/4.train-multi-node-ddp.ipynb)

### <a name="hugging-face-transformers"></a>Hugging Face 변환기

Hugging Face는 `torch.distributed.launch`와 함께 변환기 라이브러리를 사용하여 분산 학습을 실행하기 위한 [예제](https://github.com/huggingface/transformers/tree/master/examples)를 다수 제공합니다. 변환기 트레이너 API를 사용하여 이러한 예제와 사용자 지정 학습 스크립트를 실행하려면 [`torch.distributed.launch` 사용](#per-node-launch) 섹션을 따릅니다.

GPU가 8개인 한 노드에서 `run_glue.py` 스크립트를 사용하여 텍스트 분류 MNLI 작업에 있는 BERT 대형 모델을 미세 조정하는 샘플 작업 구성 코드:

```python
from azureml.core import ScriptRunConfig
from azureml.core.runconfig import PyTorchConfiguration

distr_config = PyTorchConfiguration() # node_count defaults to 1
launch_cmd = "python -m torch.distributed.launch --nproc_per_node 8 text-classification/run_glue.py --model_name_or_path bert-large-uncased-whole-word-masking --task_name mnli --do_train --do_eval --max_seq_length 128 --per_device_train_batch_size 8 --learning_rate 2e-5 --num_train_epochs 3.0 --output_dir /tmp/mnli_output".split()

run_config = ScriptRunConfig(
  source_directory='./src',
  command=launch_cmd,
  compute_target=compute_target,
  environment=pytorch_env,
  distributed_job_config=distr_config,
)
```

또한 [프로세스별 시작](#per-process-launch) 옵션을 이용해 `torch.distributed.launch`를 사용하지 않고도 분산 학습을 실행할 수 있습니다. 이 방법을 사용하는 경우 유의해야 할 한 가지 사항은 변환기 [TrainingArguments](https://huggingface.co/transformers/main_classes/trainer.html?highlight=launch#trainingarguments)에서 로컬 순위가 인수(`--local_rank`)로 전달될 것으로 예상한다는 점입니다. `torch.distributed.launch`에서는 `--use_env=False`일 때 이 작업을 처리하지만, 프로세스별 시작을 사용하는 경우 Azure ML에서 `LOCAL_RANK` 환경 변수만 설정하므로 `--local_rank=$LOCAL_RANK` 학습 스크립트에 대한 인수로 로컬 순위를 명시적으로 전달해야 합니다.

## <a name="tensorflow"></a>TensorFlow

학습 코드에서 [네이티브 분산 TensorFlow](https://www.tensorflow.org/guide/distributed_training)를 사용하는 경우(예: TensorFlow 2.x의 `tf.distribute.Strategy` API), `TensorflowConfiguration`을 사용하여 Azure ML을 통해 분산 작업을 시작할 수 있습니다.

이렇게 하려면 `ScriptRunConfig` 생성자의 `distributed_job_config` 매개 변수에 `TensorflowConfiguration` 개체를 지정합니다. `tf.distribute.experimental.MultiWorkerMirroredStrategy`를 사용하는 경우 학습 작업의 노드 수에 해당하는 `TensorflowConfiguration`에서 `worker_count`를 지정합니다.

```python
from azureml.core import ScriptRunConfig, Environment, Experiment
from azureml.core.runconfig import TensorflowConfiguration

curated_env_name = 'AzureML-TensorFlow-2.3-GPU'
tf_env = Environment.get(workspace=ws, name=curated_env_name)
distr_config = TensorflowConfiguration(worker_count=2, parameter_server_count=0)

run_config = ScriptRunConfig(
  source_directory='./src',
  script='train.py',
  compute_target=compute_target,
  environment=tf_env,
  distributed_job_config=distr_config,
)

# submit the run configuration to start the job
run = Experiment(ws, "experiment_name").submit(run_config)
```

학습 스크립트가 분산 학습에 매개 변수 서버 전략(예: 레거시 TensorFlow 1.x)을 사용하는 경우, 작업에 사용할 매개 변수 서버의 수(예: `tf_config = TensorflowConfiguration(worker_count=2, parameter_server_count=1)`)도 지정해야 합니다.

### <a name="tf_config"></a>TF_CONFIG

TensorFlow에서 **TF_CONFIG** 환경 변수는 여러 컴퓨터에서 학습하는 데 필요합니다. TensorFlow 작업의 경우 Azure ML에서 학습 스크립트를 실행하기 전에 각 작업자에 대해 TF_CONFIG 변수를 적절하게 구성하고 설정합니다.

필요한 경우 학습 스크립트에서 TF_CONFIG에 액세스할 수 있습니다(`os.environ['TF_CONFIG']`).

최고 작업자 노드에 설정된 TF_CONFIG 예제:
```json
TF_CONFIG='{
    "cluster": {
        "worker": ["host0:2222", "host1:2222"]
    },
    "task": {"type": "worker", "index": 0},
    "environment": "cloud"
}'
```

### <a name="tensorflow-example"></a>TensorFlow 예제

- [azureml-examples: MirroredStrategy를 사용하여 분산 TensorFlow 학습](https://github.com/Azure/azureml-examples/tree/main/python-sdk/workflows/train/tensorflow/mnist-distributed)

## <a name="accelerating-gpu-training-with-infiniband"></a><a name="infiniband"></a> InfiniBand를 통해 GPU 학습 가속화

특정 Azure VM 시리즈, 특히 NC, ND, H 시리즈에는 현재 SR-IOV 및 Infiniband를 지원하는 RDMA 지원 VM이 있습니다. 이러한 VM은 대기 시간이 짧은 고대역폭 InfiniBand 네트워크를 통해 통신하는데, 이는 이더넷 기반 연결보다 훨씬 더 성능이 뛰어납니다. InfiniBand용 SR-IOV는 모든 MPI 라이브러리에 대해 완전한 성능을 지원합니다(MPI는 NVIDIA의 NCCL 소프트웨어를 비롯한 많은 분산 학습 프레임워크와 도구에서 사용됨). 이러한 SKU는 계산 집약적인 GPU 가속 기계 학습 워크로드의 요구 사항을 충족하기 위한 것입니다. 자세한 내용은 [SR-IOV를 사용하여 Azure Machine Learning에서 분산 학습 가속화](https://techcommunity.microsoft.com/t5/azure-ai/accelerating-distributed-training-in-azure-machine-learning/ba-p/1059050)를 참조하세요.

이러한 RDMA 지원, InfiniBand 지원 크기(예: `Standard_ND40rs_v2`) 중 하나로 `AmlCompute` 클러스터를 만드는 경우 OS 이미지는 InfiniBand를 사용하는 데 필요한 Mellanox OFED 드라이버가 미리 설치/구성된 상태로 제공됩니다.

## <a name="next-steps"></a>다음 단계

* [Azure에 기계 학습 모델 배포](how-to-deploy-and-where.md)
* [관리형 온라인 엔드포인트(미리 보기)를 사용하여 기계 학습 모델 배포 및 채점](how-to-deploy-managed-online-endpoints.md)
* [Azure에서 분산 딥 러닝 학습의 참조 아키텍처](/azure/architecture/reference-architectures/ai/training-deep-learning)
