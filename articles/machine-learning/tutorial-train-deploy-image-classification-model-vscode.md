---
title: '자습서: 이미지 분류 모델 학습: VS Code(미리 보기)'
titleSuffix: Azure Machine Learning
description: TensorFlow 및 Azure Machine Learning Visual Studio Code 확장을 사용하여 이미지 분류 모델을 학습시키는 방법을 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: luisquintanilla
ms.author: luquinta
ms.date: 05/25/2021
ms.custom: contperf-fy20q4
ms.openlocfilehash: 195ea650e15024366d3d9d9426b466cf73951bb1
ms.sourcegitcommit: 34aa13ead8299439af8b3fe4d1f0c89bde61a6db
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/18/2021
ms.locfileid: "122418872"
---
# <a name="train-an-image-classification-tensorflow-model-using-the-azure-machine-learning-visual-studio-code-extension-preview"></a>Azure Machine Learning Visual Studio Code 확장(미리 보기)을 사용하여 이미지 분류 TensorFlow 모델 학습

TensorFlow 및 Azure Machine Learning Visual Studio Code 확장을 사용하여 필기 숫자를 인식하도록 이미지 분류 모델을 학습시키는 방법에 대해 알아봅니다.

이 자습서에서는 다음 작업에 대해 알아봅니다.

> [!div class="checklist"]
> * 코드 이해
> * 작업 영역 만들기
> * 학습용 GPU 클러스터 만들기
> * 모델 학습

## <a name="prerequisites"></a>필수 구성 요소

- 동작합니다. 구독이 없는 경우 [Azure Machine Learning의 평가판 또는 유료 버전](https://azure.microsoft.com/free/t.com/free/)에 가입하여 사용해 보세요.
- 간단한 플랫폼 간 코드 편집기인 [Visual Studio Code](https://code.visualstudio.com/docs/setup/setup-overview)를 설치합니다.
- Azure Machine Learning Visual Studio Code 확장. 설치 지침은 [Azure Machine Learning Visual Studio Code 확장 가이드 설정](./how-to-setup-vs-code.md)을 참조하세요.
- CLI(v2)(미리 보기) 설치 지침은 [CLI(v2)(미리 보기) 설치, 설정 및 사용](how-to-configure-cli.md)을 참조하세요.

## <a name="understand-the-code"></a>코드 이해

이 자습서의 코드는 TensorFlow를 사용하여 0-9의 필기 숫자를 분류하는 이미지 분류 기계 학습 모델을 학습시킵니다. 이를 위해 입력으로 28px x 28px 이미지의 픽셀 값을 사용하고, 분류되는 각 숫자에 대해 하나씩 10개 확률의 목록을 출력하는 신경망을 만듭니다. 데이터의 모양을 보여 주는 샘플은 아래와 같습니다.  

![MNIST 숫자](./media/tutorial-train-deploy-image-classification-model-vscode/digits.png)

이 자습서의 코드는 컴퓨터의 아무 곳에 [Azure ML 예제 리포지토리](https://github.com/Azure/azureml-examples/archive/refs/heads/main.zip)를 다운로드하고 압축을 풀어서 가져옵니다.

## <a name="create-a-workspace"></a>작업 영역 만들기

Azure Machine Learning에서 애플리케이션을 빌드하려면 가장 먼저 작업 영역을 만들어야 합니다. 작업 영역에는 학습된 모델 자체뿐만 아니라 모델을 학습시킬 수 있는 리소스도 포함됩니다. 자세한 내용은 [작업 영역이란?](./concept-workspace.md)을 참조하세요.

1. Visual Studio Code에서 *azureml-examples-main/cli/jobs/train/tensorflow/mnist* 디렉터리를 엽니다.
1. Visual Studio Code 작업 막대에서 **Azure** 아이콘을 선택하여 Azure Machine Learning 보기를 엽니다.
1. Azure Machine Learning 보기에서 구독 노드를 마우스 오른쪽 단추로 클릭하고 **작업 영역 만들기** 를 선택합니다.

    > [!div class="mx-imgBorder"]
    > ![작업 영역 만들기](./media/tutorial-train-deploy-image-classification-model-vscode/create-workspace.png)

1. 사양 파일이 나타납니다. 다음 옵션을 사용하여 사양 파일을 구성합니다. 

    ```yml
    $schema: https://azuremlschemas.azureedge.net/latest/workspace.schema.json
    name: TeamWorkspace
    location: WestUS2
    friendly_name: team-ml-workspace
    description: A workspace for training machine learning models
    tags:
      purpose: training
      team: ml-team
    ```

    사양 파일은 `WestUS2` 영역에 `TeamWorkspace`라는 작업 영역을 만듭니다. 사양 파일에 정의된 나머지 옵션은 작업 영역에 대한 식별 이름 지정, 설명 및 태그를 제공합니다.

1. 사양 파일을 마우스 오른쪽 단추로 클릭하고 **Azure ML: 리소스 만들기** 를 선택합니다. 리소스 만들기는 YAML 사양 파일에 정의된 구성 옵션을 사용하고 CLI(v2)를 사용하여 작업을 제출합니다. 이 시점에서 새 작업 영역 및 종속 리소스를 계정에 만들도록 요구하는 요청을 Azure에 보냅니다. 몇 분 후에 새 작업 영역이 구독 노드에 표시됩니다.
1. `TeamWorkspace`를 기본 작업 영역으로 설정합니다. 이렇게 하면 기본적으로 작업 영역에 사용자가 만든 리소스와 작업이 배치됩니다. Visual Studio Code 상태 표시줄에서 **Azure ML 작업 영역 설정** 단추를 선택하고 프롬프트에 따라 `TeamWorkspace`를 기본 작업 영역으로 설정합니다.

작업 영역에 대한 자세한 내용은 [VS Code에서 리소스를 관리하는 방법](how-to-manage-resources-vscode.md)을 참조하세요.

## <a name="create-a-gpu-cluster-for-training"></a>학습용 GPU 클러스터 만들기

컴퓨팅 대상은 학습 작업을 실행하는 컴퓨팅 리소스 또는 환경입니다. 자세한 내용은 [Azure Machine Learning 컴퓨팅 대상 설명서](./concept-compute-target.md)를 참조하세요.

1. Azure Machine Learning 보기에서 작업 영역 노드를 확장합니다.
1. 작업 영역의 **컴퓨팅** 노드 내에서 **컴퓨팅 클러스터** 노드를 마우스 오른쪽 단추로 클릭하고 **컴퓨팅 만들기** 를 선택합니다.

    > [!div class="mx-imgBorder"]
    > ![학습 컴퓨팅 클러스터 만들기](./media/tutorial-train-deploy-image-classification-model-vscode/create-compute.png)

1. 사양 파일이 나타납니다. 다음 옵션을 사용하여 사양 파일을 구성합니다.

    ```yml
    $schema: https://azuremlschemas.azureedge.net/latest/compute.schema.json
    name: gpu-cluster
    type: amlcompute
    size: Standard_NC12
    
    min_instances: 0
    max_instances: 3
    idle_time_before_scale_down: 120
    ```

    사양 파일은 최대 3개의 Standard_NC12 VM 노드를 사용하여 `gpu-cluster`라는 GPU 클러스터를 만듭니다. 이 노드는 120초 동안 비활성 상태가 되면 자동으로 0개 노드로 스케일 다운됩니다.

    VM 크기에 대한 자세한 내용은 [Azure에서 Linux 가상 머신에 대한 크기](../virtual-machines/sizes.md)를 참조하세요.

1. 사양 파일을 마우스 오른쪽 단추로 클릭하고 **Azure ML: 리소스 만들기** 를 선택합니다.

몇 분 후에 새 컴퓨팅 대상이 작업 영역의 *컴퓨팅 > 컴퓨팅 클러스터* 노드에 표시됩니다.

## <a name="train-image-classification-model"></a><a name="train-the-model"></a> 이미지 분류 모델 학습

TensorFlow 모델은 학습 프로세스 중에 분류되는 각 숫자에 대한 학습 데이터와 이에 포함된 학습 패턴을 처리하여 학습됩니다.

작업 영역 및 컴퓨팅 대상과 마찬가지로 학습 작업은 리소스 템플릿을 사용하여 정의됩니다. 이 샘플의 경우 사양은 다음과 같은 *job.yml* 파일에 정의되어 있습니다.

```yml
$schema: https://azuremlschemas.azureedge.net/latest/commandJob.schema.json
code: 
    local_path: src
command: >
    python train.py
environment: azureml:AzureML-TensorFlow2.4-Cuda11-OpenMpi4.1.0-py36:1
compute:
    target: azureml:gpu-cluster
experiment_name: tensorflow-mnist-example
description: Train a basic neural network with TensorFlow on the MNIST dataset.
```

이 사양 파일은 *train.py* Python 스크립트에서 코드를 실행하는 최근에 만든 `gpu-cluster` 컴퓨터 대상에 `tensorflow-mnist-example`이라는 학습 작업을 제출합니다. 사용되는 환경은 TensorFlow 및 학습 스크립트를 실행하는 데 필요한 기타 소프트웨어 종속성이 포함된 Azure Machine Learning에서 제공하는 큐레이팅된 환경 중 하나입니다. 큐레이팅된 환경에 대한 자세한 내용은 [Azure Machine Learning 큐레이팅된 환경](resource-curated-environments.md)을 참조하세요.

학습 작업 제출하려면 다음을 수행합니다.

1. *job.yml* 파일을 엽니다.
1. 텍스트 편집기에서 파일을 마우스 오른쪽 단추로 클릭하고 **Azure ML: 리소스 만들기** 를 선택합니다.

> [!div class="mx-imgBorder"]
> ![실험 실행](./media/tutorial-train-deploy-image-classification-model-vscode/run-experiment.png)

이 시점에서 선택한 작업 영역의 컴퓨팅 대상에서 실험을 실행하도록 요구하는 요청을 Azure에 보냅니다. 이 프로세스는 몇 분 정도 걸립니다. 학습 작업을 실행하는 시간은 컴퓨팅 형식 및 학습 데이터 크기와 같은 몇 가지 요인의 영향을 받습니다. 실험 진행률을 추적하려면 마우스 오른쪽 단추로 현재 실행 노드를 클릭하고 **Azure Portal에서 실행 보기** 를 선택합니다.

외부 웹 사이트를 열도록 요청하는 대화 상자가 표시되면 **열기** 를 선택합니다.

> [!div class="mx-imgBorder"]
> ![실험 진행률 추적](./media/tutorial-train-deploy-image-classification-model-vscode/track-experiment-progress.png)

모델 학습이 완료되면 실행 노드 옆의 상태 레이블이 "완료됨"으로 업데이트됩니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음 작업에 대해 알아봅니다.

> [!div class="checklist"]
> * 코드 이해
> * 작업 영역 만들기
> * 학습용 GPU 클러스터 만들기
> * 모델 학습

다음 단계는 아래를 참조하세요.

* [Visual Studio Code를 사용하여 Azure Machine Learning 리소스 만들기 및 관리](how-to-set-up-vs-code-remote.md).
* 전체 개발 환경을 위해 [Visual Studio Code를 컴퓨팅 인스턴스에 연결](how-to-set-up-vs-code-remote.md)합니다.
* 코드를 로컬로 편집, 실행 및 디버그하는 방법을 연습하려면 [Python hello-world 자습서](https://code.visualstudio.com/docs/Python/Python-tutorial)를 참조하세요.
* 원격 Jupyter 서버를 사용하여 [Visual Studio Code에서 Jupyter Notebook을 실행](how-to-manage-resources-vscode.md)합니다.
* Visual Studio Code 외부에서 Azure Machine Learning을 사용하여 학습하는 방법을 연습하려면 [자습서: Azure Machine Learning을 사용하여 모델 학습](tutorial-train-models-with-aml.md)을 참조하세요.