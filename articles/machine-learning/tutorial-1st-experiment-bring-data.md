---
title: '자습서: 데이터 업로드 및 모델 학습'
titleSuffix: Azure Machine Learning
description: 원격 학습 실행에서 사용자 고유의 데이터를 업로드하고 사용하는 방법입니다. 3부로 구성된 시작 시리즈 중 3부입니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: aminsaied
ms.author: amsaied
ms.reviewer: sgilley
ms.date: 04/29/2021
ms.custom: tracking-python, contperf-fy21q3, FY21Q4-aml-seo-hack, contperf-fy21q4
ms.openlocfilehash: 43684ffe7287aa8cd227f471b878450de39b72d6
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2021
ms.locfileid: "132704317"
---
# <a name="tutorial-upload-data-and-train-a-model-part-3-of-3"></a>자습서: 데이터 업로드 및 모델 학습(3/3부)

이 자습서에서는 사용자 고유의 데이터를 업로드하고 사용하여 Azure Machine Learning에서 기계 학습 모델을 학습시키는 방법을 보여 줍니다. 이 자습서는 ‘3부로 구성된 자습서 시리즈 중 3부’입니다.  

[2부: 모델 학습](tutorial-1st-experiment-sdk-train.md)에서는 `PyTorch`의 샘플 데이터를 사용하여 클라우드에서 모델을 학습시켰습니다.  또한 PyTorch API에서 `torchvision.datasets.CIFAR10` 메서드를 통해 해당 데이터를 다운로드했습니다. 이 자습서에서는 다운로드한 데이터를 사용하여 Azure Machine Learning에서 사용자 고유의 데이터로 작업하기 위한 워크플로를 학습합니다.

이 자습서에서는 다음을 수행합니다.

> [!div class="checklist"]
> * Azure에 데이터 업로드
> * 제어 스크립트 만들기
> * 새 Azure Machine Learning 개념(매개 변수, 데이터 세트, 데이터 저장소 전달) 이해
> * 학습 스크립트 제출 및 실행
> * 클라우드에서 코드 출력 보기

## <a name="prerequisites"></a>사전 요구 사항

이전 자습서에서 다운로드한 데이터가 필요합니다.  다음 단계를 완료했는지 확인합니다.

1. [학습 스크립트를 만듭니다](tutorial-1st-experiment-sdk-train.md#create-training-scripts).  
1. [로컬로 테스트](tutorial-1st-experiment-sdk-train.md#test-local)합니다.

## <a name="adjust-the-training-script"></a>학습 스크립트 조정

이제 Azure Machine Learning에서 실행되는 학습 스크립트(get-started/src/train.py)가 있으며 모델 성능을 모니터링할 수 있습니다. 인수를 도입하여 학습 스크립트를 매개 변수화하겠습니다. 인수를 사용하면 다른 하이퍼 매개 변수를 쉽게 비교할 수 있습니다.

현재 학습 스크립트는 각 실행에서 CIFAR10 데이터 세트를 다운로드하도록 설정되어 있습니다. 아래의 Python 코드는 디렉터리에서 데이터를 읽도록 조정되었습니다.

>[!NOTE] 
> `argparse`를 사용하여 스크립트를 매개 변수화합니다.

1. *train.py* 를 열고 다음 코드로 바꿉니다.

    ```python
    import os
    import argparse
    import torch
    import torch.optim as optim
    import torchvision
    import torchvision.transforms as transforms
    from model import Net
    from azureml.core import Run
    run = Run.get_context()
    if __name__ == "__main__":
        parser = argparse.ArgumentParser()
        parser.add_argument(
            '--data_path',
            type=str,
            help='Path to the training data'
        )
        parser.add_argument(
            '--learning_rate',
            type=float,
            default=0.001,
            help='Learning rate for SGD'
        )
        parser.add_argument(
            '--momentum',
            type=float,
            default=0.9,
            help='Momentum for SGD'
        )
        args = parser.parse_args()
        print("===== DATA =====")
        print("DATA PATH: " + args.data_path)
        print("LIST FILES IN DATA PATH...")
        print(os.listdir(args.data_path))
        print("================")
        # prepare DataLoader for CIFAR10 data
        transform = transforms.Compose([
            transforms.ToTensor(),
            transforms.Normalize((0.5, 0.5, 0.5), (0.5, 0.5, 0.5))
        ])
        trainset = torchvision.datasets.CIFAR10(
            root=args.data_path,
            train=True,
            download=False,
            transform=transform,
        )
        trainloader = torch.utils.data.DataLoader(
            trainset,
            batch_size=4,
            shuffle=True,
            num_workers=2
        )
        # define convolutional network
        net = Net()
        # set up pytorch loss /  optimizer
        criterion = torch.nn.CrossEntropyLoss()
        optimizer = optim.SGD(
            net.parameters(),
            lr=args.learning_rate,
            momentum=args.momentum,
        )
        # train the network
        for epoch in range(2):
            running_loss = 0.0
            for i, data in enumerate(trainloader, 0):
                # unpack the data
                inputs, labels = data
                # zero the parameter gradients
                optimizer.zero_grad()
                # forward + backward + optimize
                outputs = net(inputs)
                loss = criterion(outputs, labels)
                loss.backward()
                optimizer.step()
                # print statistics
                running_loss += loss.item()
                if i % 2000 == 1999:
                    loss = running_loss / 2000
                    run.log('loss', loss)  # log loss metric to AML
                    print(f'epoch={epoch + 1}, batch={i + 1:5}: loss {loss:.2f}')
                    running_loss = 0.0
        print('Finished Training')
    ```

1. 파일을 **저장** 합니다.  원하는 경우 탭을 닫습니다.

### <a name="understanding-the-code-changes"></a>코드 변경 내용 이해

`train.py`의 코드는 `argparse` 라이브러리를 사용하여 `data_path`, `learning_rate` 및 `momentum`을 설정했습니다.

```python
# .... other code
parser = argparse.ArgumentParser()
parser.add_argument('--data_path', type=str, help='Path to the training data')
parser.add_argument('--learning_rate', type=float, default=0.001, help='Learning rate for SGD')
parser.add_argument('--momentum', type=float, default=0.9, help='Momentum for SGD')
args = parser.parse_args()
# ... other code
```

또한 사용자 정의 매개 변수를 사용하도록 최적화 프로그램을 업데이트하기 위해 `train.py` 스크립트가 조정되었습니다.

```python
optimizer = optim.SGD(
    net.parameters(),
    lr=args.learning_rate,     # get learning rate from command-line argument
    momentum=args.momentum,    # get momentum from command-line argument
)
```


## <a name="upload-the-data-to-azure"></a><a name="upload"></a> Azure에 데이터 업로드

Azure Machine Learning에서 이 스크립트를 실행하려면 Azure에서 학습 데이터를 사용할 수 있도록 해야 합니다. Azure Machine Learning 작업 영역에는 _기본_ 데이터 저장소가 장착되어 제공됩니다. 이는 학습 데이터를 저장할 수 있는 Azure Blob Storage 계정입니다.

>[!NOTE] 
> Azure Machine Learning을 사용하면 데이터를 저장하는 다른 클라우드 기반 데이터 저장소를 연결할 수 있습니다. 자세한 내용은 [데이터 저장소 설명서](./concept-data.md)를 참조하세요.  

1. **get-started** 폴더에 새 Python 제어 스크립트를 만듭니다. 이때 **/src** 폴더가 ‘아닌’ **get-started** 에 있어야 합니다.  스크립트 이름을 *upload-data.py* 로 지정하고 다음 코드를 파일에 복사합니다.
    
    ```python
    # upload-data.py
    from azureml.core import Workspace
    ws = Workspace.from_config()
    datastore = ws.get_default_datastore()
    datastore.upload(src_dir='./data',
                     target_path='datasets/cifar10',
                     overwrite=True)
    
    ```

    `target_path` 값은 CIFAR10 데이터가 업로드되는 데이터 저장소의 경로를 지정합니다.

    >[!TIP] 
    > Azure Machine Learning을 사용하여 데이터를 업로드하는 동안 [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/)를 사용하여 임시 파일을 업로드할 수 있습니다. ETL 도구가 필요한 경우 [Azure Data Factory](../data-factory/introduction.md)를 사용하여 데이터를 Azure에 수집할 수 있습니다.

2. **터미널에서 스크립트 저장 및 실행** 을 선택하여 *upload-data.py* 스크립트를 실행합니다.

    다음과 같은 표준 출력이 표시됩니다.

    ```txt
    Uploading ./data\cifar-10-batches-py\data_batch_2
    Uploaded ./data\cifar-10-batches-py\data_batch_2, 4 files out of an estimated total of 9
    .
    .
    Uploading ./data\cifar-10-batches-py\data_batch_5
    Uploaded ./data\cifar-10-batches-py\data_batch_5, 9 files out of an estimated total of 9
    Uploaded 9 files
    ```

## <a name="create-a-control-script"></a><a name="control-script"></a> 제어 스크립트 만들기

이전에 수행한 것처럼 **get-started** 폴더에 *run-pytorch-data.py* 라는 새 Python 컨트롤 스크립트를 만듭니다.

```python
# run-pytorch-data.py
from azureml.core import Workspace
from azureml.core import Experiment
from azureml.core import Environment
from azureml.core import ScriptRunConfig
from azureml.core import Dataset

if __name__ == "__main__":
    ws = Workspace.from_config()
    datastore = ws.get_default_datastore()
    dataset = Dataset.File.from_files(path=(datastore, 'datasets/cifar10'))

    experiment = Experiment(workspace=ws, name='day1-experiment-data')

    config = ScriptRunConfig(
        source_directory='./src',
        script='train.py',
        compute_target='cpu-cluster',
        arguments=[
            '--data_path', dataset.as_named_input('input').as_mount(),
            '--learning_rate', 0.003,
            '--momentum', 0.92],
    )

    # use curated pytorch environment 
    env = ws.environments['AzureML-PyTorch-1.3-CPU']
    config.run_config.environment = env

    run = experiment.submit(config)
    aml_url = run.get_portal_url()
    print("Submitted to compute cluster. Click link below")
    print("")
    print(aml_url)
```

> [!TIP]
> 컴퓨팅 클러스터를 만들 때 다른 이름을 사용한 경우 코드 `compute_target='cpu-cluster'`의 이름도 조정합니다.

### <a name="understand-the-code-changes"></a>코드 변경 내용 이해

제어 스크립트는 [이 시리즈의 3부](tutorial-1st-experiment-sdk-train.md)에 있는 것과 비슷하며 다음과 같은 새 줄이 있습니다.

:::row:::
   :::column span="":::
      `dataset = Dataset.File.from_files( ... )`
   :::column-end:::
   :::column span="2":::
      [데이터 세트](/python/api/azureml-core/azureml.core.dataset.dataset)는 Azure Blob Storage에 업로드한 데이터를 참조하는 데 사용됩니다. 데이터 세트는 데이터를 기반으로 하는 있는 추상화 계층이며, 안정성과 신뢰성을 향상시키도록 설계되었습니다.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      `config = ScriptRunConfig(...)`
   :::column-end:::
   :::column span="2":::
      [ScriptRunConfig](/python/api/azureml-core/azureml.core.scriptrunconfig)는 `train.py`에 전달되는 인수 목록을 포함하도록 수정되었습니다. `dataset.as_named_input('input').as_mount()` 인수는 지정된 디렉터리가 컴퓨팅 대상에 _탑재됨_ 을 의미합니다.
   :::column-end:::
:::row-end:::

## <a name="submit-the-run-to-azure-machine-learning"></a><a name="submit-to-cloud"></a> Azure Machine Learning에 실행 제출

**터미널에서 스크립트 저장 및 실행** 을 선택하여 *run-pytorch-data.py* 스크립트를 실행합니다.  이 실행은 업로드한 데이터를 사용하여 컴퓨팅 클러스터에서 모델을 학습합니다.

그러면 Azure Machine Learning 스튜디오에서 실험에 대한 URL이 출력됩니다. 해당 링크로 이동하면 코드가 실행되는 것을 확인할 수 있습니다.

[!INCLUDE [amlinclude-info](../../includes/machine-learning-py38-ignore.md)]


### <a name="inspect-the-log-file"></a><a name="inspect-log"></a> 로그 파일 검사

스튜디오에서 이전 URL 출력을 선택하여 실험 실행으로 이동한 다음, **출력 + 로그** 로 이동합니다. `70_driver_log.txt` 파일을 선택합니다. 다음 출력이 표시될 때까지 로그 파일을 아래로 스크롤합니다.

```txt
Processing 'input'.
Processing dataset FileDataset
{
  "source": [
    "('workspaceblobstore', 'datasets/cifar10')"
  ],
  "definition": [
    "GetDatastoreFiles"
  ],
  "registration": {
    "id": "XXXXX",
    "name": null,
    "version": null,
    "workspace": "Workspace.create(name='XXXX', subscription_id='XXXX', resource_group='X')"
  }
}
Mounting input to /tmp/tmp9kituvp3.
Mounted input to /tmp/tmp9kituvp3 as folder.
Exit __enter__ of DatasetContextManager
Entering Run History Context Manager.
Current directory:  /mnt/batch/tasks/shared/LS_root/jobs/dsvm-aml/azureml/tutorial-session-3_1600171983_763c5381/mounts/workspaceblobstore/azureml/tutorial-session-3_1600171983_763c5381
Preparing to call script [ train.py ] with arguments: ['--data_path', '$input', '--learning_rate', '0.003', '--momentum', '0.92']
After variable expansion, calling script [ train.py ] with arguments: ['--data_path', '/tmp/tmp9kituvp3', '--learning_rate', '0.003', '--momentum', '0.92']

Script type = None
===== DATA =====
DATA PATH: /tmp/tmp9kituvp3
LIST FILES IN DATA PATH...
['cifar-10-batches-py', 'cifar-10-python.tar.gz']
```

참고:

- Azure Machine Learning에서 자동으로 Blob Storage를 컴퓨팅 클러스터에 탑재했습니다.
- 제어 스크립트에 사용된 ``dataset.as_named_input('input').as_mount()``가 탑재 지점으로 확인됩니다.


## <a name="clean-up-resources"></a>리소스 정리

지금 다른 자습서로 계속 진행하거나 자체 학습 실행을 시작하려면 [다음 단계](#next-steps)로 건너뜁니다.

### <a name="stop-compute-instance"></a>컴퓨팅 인스턴스 중지

지금 사용하지 않을 경우 컴퓨팅 인스턴스를 중지합니다.

1. 스튜디오의 왼쪽에서 **컴퓨팅** 을 선택합니다.
1. 위쪽 탭에서 **컴퓨팅 인스턴스** 를 선택합니다.
1. 목록에서 컴퓨팅 인스턴스를 선택합니다.
1. 맨 위의 도구 모음에서 **중지** 를 선택합니다.


### <a name="delete-all-resources"></a>모든 리소스 삭제

[!INCLUDE [aml-delete-resource-group](../../includes/aml-delete-resource-group.md)]

또한 리소스 그룹을 유지하면서 단일 작업 영역을 삭제할 수도 있습니다. 작업 영역 속성을 표시하고 **삭제** 를 선택합니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 `Datastore`를 사용하여 데이터를 Azure에 업로드하는 방법을 살펴보았습니다. 데이터 저장소는 작업 영역에 대한 클라우드 스토리지로 제공되므로 데이터를 유지할 수 있는 지속적이고 유연한 장소를 제공했습니다.

명령줄을 통해 데이터 경로를 허용하도록 학습 스크립트를 수정하는 방법을 살펴보았습니다. `Dataset`를 사용하여 디렉터리를 원격 실행에 탑재할 수 있었습니다.

이제 모델이 있으므로 다음을 알아봅니다.

> [!div class="nextstepaction"]
> [Azure Machine Learning을 사용하여 모델을 배포하는 방법](how-to-deploy-and-where.md)
