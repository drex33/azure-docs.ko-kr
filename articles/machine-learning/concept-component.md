---
title: 구성 요소 (미리 보기) 란?
titleSuffix: Azure Machine Learning
description: Azure Machine Learning 구성 요소를 사용 하 여 기계 학습 파이프라인을 빌드할 수 있습니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: keli19
author: likebupt
ms.reviewer: laobri
ms.date: 10/21/2021
ms.topic: conceptual
ms.openlocfilehash: 5846a84df6c11364fed6fa65b852b55c42cd5364
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131570205"
---
# <a name="what-is-an-azure-machine-learning-component-preview"></a>Azure Machine Learning 구성 요소 (미리 보기) 란?

Azure Machine Learning 구성 요소 (이전에 모듈 이라고 함)는 Machine Learning 파이프라인에서 한 단계를 수행 하는 자체 포함 된 코드 조각입니다. 구성 요소는 고급 기계 학습 파이프라인의 빌딩 블록입니다 ( [Azure Machine Learning CLI를 사용 하 여 machine learning 파이프라인 만들기 및 실행](how-to-create-component-pipelines-cli.md)참조). 구성 요소는 데이터 처리, 모델 학습, 모델 점수 매기기 등의 작업을 수행할 수 있습니다.

구성 요소는 함수와 유사 합니다. 여기에는 이름, 매개 변수 및 입력이 필요 하 고 출력이 반환 됩니다. 구성 요소를 만드는 방법에 대 한 자세한 내용은 [구성 요소 만들기](#define-a-component-preview)를 참조 하세요.

## <a name="why-should-i-use-a-component-preview"></a>구성 요소 (미리 보기)를 사용 해야 하는 이유는 무엇 인가요?

구성 요소를 사용 하면 파이프라인에서 공통 논리를 관리 하 고 재사용할 수 있습니다.

- 구성 **가능: 개발자** 는 간단한 인터페이스 뒤에 복잡 한 논리를 숨길 수 있습니다. 구성 요소 사용자는 기본 논리에 대해 걱정할 필요가 없으며 매개 변수만 제공 하면 됩니다.

- **공유 및 다시 사용**: 구성 요소가 같은 작업 영역의 사용자와 자동으로 공유 됩니다. 파이프라인, 환경, 작업 영역 및 구독에서 구성 요소를 재사용할 수 있습니다. 기본 제공 버전 추적을 사용 하면 변경 내용을 추적 하 고 결과를 재현할 수 있습니다.

- **Cli 지원**: 구성 요소를 사용 하 여 cli에 파이프라인을 만듭니다 (v2).


## <a name="define-a-component-preview"></a>구성 요소 정의 (미리 보기)

Azure Machine Learning 구성 요소를 정의 하려면 다음 두 파일을 제공 해야 합니다.

- 유효한 [Yaml 구성 요소 사양 형식의](reference-yaml-component-command.md)구성 요소 사양입니다. 이 파일은 다음 정보를 지정 합니다.
  - 메타 데이터: 이름, display_name, 버전, 형식 등입니다.
  - 인터페이스: 입력 및 출력
  - 명령, 코드, & 환경: 구성 요소를 실행 하는 데 사용 되는 명령, 코드 및 환경
- 실제 실행 논리를 제공 하는 스크립트입니다.

### <a name="component-specification"></a>구성 요소 사양

구성 요소 사양 파일은 구성 요소에 대 한 메타 데이터 및 실행 매개 변수를 정의 합니다. 구성 요소 사양은 사용자가 제공 하는 Python 스크립트를 실행 하는 방법을 Azure Machine Learning 나타냅니다.

다음 예제는 학습 구성 요소에 대 한 구성 요소 사양입니다.

```yaml
name: Example_Train
display_name: Example Train
version: 20
type: command
description: Example of a torchvision training component
tags: {category: Component Tutorial, contact: user@contoso.com}
inputs:
  training_data: 
    type: path
    description: Training data organized in torchvision structure
  max_epochs:
    type: integer
    description: Maximum epochs for training
  learning_rate: 
    type: number
    description: Learning rate, default is 0.01
    default: 0.01
  learning_rate_schedule: 
    type: string
    default: time-based 
outputs:
  model_output:
    type: path
code:
  local_path: ./train_src
environment: azureml:AzureML-Minimal:1
command: >-
  python train.py 
  --training_data ${{inputs.training_data}} 
  --max_epochs ${{inputs.max_epochs}}   
  --learning_rate ${{inputs.learning_rate}} 
  --learning_rate_schedule ${{inputs.learning_rate_schedule}} 
  --model_output ${{outputs.model_output}}
```

다음 표에서는 예제의 필드에 대해 설명 합니다. 사용 가능한 필드의 전체 목록은 [Yaml 구성 요소 사양 참조 페이지](reference-yaml-component-command.md)를 참조 하세요.

| Name                | Type                                                     | 필수 | Description                                                  |
| ------------------- | -------------------------------------------------------- | -------- | ------------------------------------------------------------ |
| name                | 문자열                                                   | 예      | 구성 요소의 이름입니다. 구성 요소의 고유 식별자 여야 합니다. 는 숫자 또는 문자로 시작 해야 하 고 문자, 숫자, 및만 포함 해야 합니다 `_` `-` . 최대 길이는 255 자입니다.|
| 버전             | 문자열                                                   | 예      | 구성 요소 버전입니다. 는 문자열 이어야 합니다. |
| display_name        | 문자열                                                   | 예       | 구성 요소의 표시 이름입니다. 기본값은와 동일 `name` 합니다. |
| 형식                | 문자열                                                   | 예       | 구성 요소의 유형입니다. 현재 이 값은 `command`이 되어야 합니다.|
| description         | 문자열                                                   | 예       | 구성 요소에 대 한 자세한 설명입니다. |
| tags                | 사전 &lt; 문자열&gt;                                       | 예 | 구성 요소의 다양 한 큐브 뷰를 설명 하는 키-값 쌍의 목록입니다. 각 태그의 키와 값은 한 단어 또는 짧은 구 (예:,,) 여야 합니다 `Product:Office` `Domain:NLP` `Scenario:Image Classification` . |
| is_deterministic    | boolean                                                  | 예       | 동일한 입력 데이터를 지정 했을 때 구성 요소가 항상 동일한 결과를 생성할지 여부를 지정 합니다. 기본값은 `True`입니다. `False`외부 리소스에서 데이터를 로드 하는 구성 요소 (예를 들어, 데이터가 업데이트 될 수 있으므로 지정 된 url에서 데이터를 가져오는 구성 요소)에 대해를로 설정 해야 합니다. |
| 입력              | 사전 &lt; 문자열, 입력&gt; | 예       | 구성 요소의 입력 포트와 매개 변수를 정의 합니다. 문자열 키는 올바른 Python 변수 이름 이어야 하는 입력의 이름입니다. |
| outputs             | 사전 &lt; 문자열, 출력&gt;                    | 예       | 구성 요소의 출력 포트를 정의 합니다. 문자열 키는 유효한 Python 변수 이름 이어야 하는 출력의 이름입니다. |
| code                | 문자열                                                   | 예       | 소스 코드에 대 한 경로입니다. |
| environment         | 환경                              | 예       | 실행할 구성 요소에 대 한 런타임 환경입니다. |
| 명령을 사용합니다.             | 문자열                                             | 예    | 구성 요소 코드를 실행 하는 명령입니다.         |

### <a name="python-script"></a>Python 스크립트

Python 스크립트는 구성 요소에 대 한 실행 가능한 논리를 포함 합니다. 스크립트는 구성 요소에서 수행 하려는 작업을 Azure Machine Learning에 알려 줍니다.

를 실행 하려면 YAML 사양에서 정의한 인수와 Python 스크립트의 인수를 일치 해야 합니다. 다음 예제는 이전 섹션의 YAML 사양과 일치 하는 Python 학습 스크립트입니다.

```python
## Required imports 
import argparse
import os
## Import other dependencies your script needs
from pathlib import Path
from uuid import uuid4
from datetime import datetime

## Define an argument parser that matches the arguments from the components specification file
parser = argparse.ArgumentParser("train")
parser.add_argument("--training_data", type=str, help="Path to training data")
parser.add_argument("--max_epochs", type=int, help="Max # of epochs for the training")
parser.add_argument("--learning_rate", type=float, help="Learning rate")
parser.add_argument("--learning_rate_schedule", type=str, help="Learning rate schedule")
parser.add_argument("--model_output", type=str, help="Path of output model")

args = parser.parse_args()

## Implement your custom logic (in this case a training script)
print ("hello training world...")

lines = [
    f'Training data path: {args.training_data}',
    f'Max epochs: {args.max_epochs}',
    f'Learning rate: {args.learning_rate}',
    f'Learning rate: {args.learning_rate_schedule}',
    f'Model output path: {args.model_output}',
]

for line in lines:
    print(line)

print("mounted_path files: ")
arr = os.listdir(args.training_data)
print(arr)

for filename in arr:
    print ("reading file: %s ..." % filename)
    with open(os.path.join(args.training_data, filename), 'r') as handle:
        print (handle.read())

## Do the train and save the trained model as a file into the output folder.
## Here only output a dummy data for example.
curtime = datetime.now().strftime("%b-%d-%Y %H:%M:%S")
model = f"This is a dummy model with id: {str(uuid4())} generated at: {curtime}\n"
(Path(args.model_output) / 'model.txt').write_text(model)

```

:::image type="content" source="media/concept-component/component-introduction.png" lightbox="media/concept-component/component-introduction.png" alt-text="소스 코드 요소와 구성 요소 UI 간의 매핑을 보여 주는 개념 문서입니다." :::

## <a name="create-a-component"></a>구성 요소 만들기

### <a name="create-a-component-using-cli-v2"></a>CLI를 사용하여 구성 요소 만들기(v2)

구성 요소 사양 및 Python 스크립트 파일을 정의하고 [CLI(v2)를 성공적으로 설치한](how-to-configure-cli.md) 후 다음을 사용하여 작업 영역에서 구성 요소를 만들 수 있습니다.

```azurecli
az ml component create --file my_component.yml --version 1 --resource-group my-resource-group --workspace-name my-workspace
```

`az ml component create --help`명령에 대한 자세한 내용은 를 `create` 사용합니다.

### <a name="create-a-component-in-the-studio-ui"></a>Studio UI에서 구성 요소 만들기

Studio UI의 **구성 요소** 페이지에서 구성 요소를 만들 수 있습니다.

1. 구성 요소 페이지에서 **새 구성 요소를** 클릭합니다.

    :::image type="content" source="./media/concept-component/ui-create-component.png" lightbox="./media/concept-component/ui-create-component.png" alt-text="새 구성 요소 단추를 보여주는 스크린샷.":::

1. 마법사에 따라 만들기 프로세스를 완료합니다.

## <a name="use-components-to-build-ml-pipelines"></a>구성 요소를 사용하여 ML 파이프라인 빌드

Azure CLI(v2)를 사용하여 파이프라인 작업을 만들 수 있습니다. [CLI(ML 파이프라인) 만들기 및 실행을](how-to-create-component-pipelines-cli.md)참조하세요.

## <a name="manage-components"></a>구성 요소 관리

CLI(v2)를 사용하여 구성 요소 세부 정보를 확인하고 구성 요소를 관리할 수 있습니다. 를 사용하여 `az ml component -h` 구성 요소 명령에 대한 자세한 지침을 얻습니다.

### <a name="list-components"></a>구성 요소 나열

를 사용하여 `az ml component list` 작업 영역의 모든 구성 요소를 나열할 수 있습니다.

작업 영역에서 만든 모든 구성 요소는 스튜디오 UI의 **구성 요소** 페이지에서 볼 수 있습니다.

### <a name="show-details-for-a-component"></a>구성 요소에 대한 세부 정보 표시

를 사용하여 `az ml component show --name <COMPONENT_NAME>` 구성 요소의 세부 정보를 표시할 수 있습니다.

스튜디오 UI의 구성 **요소** 페이지에서 구성 요소 세부 정보를 확인할 수도 있습니다.


### <a name="upgrade-a-component"></a>구성 요소 업그레이드

를 사용하여 구성 요소를 업그레이드할 수 `az ml component create --file <NEW_VERSION.yaml>` 있습니다.

구성 요소 세부 정보 페이지에서 **업그레이드** 를 클릭하여 구성 요소의 새 버전을 업그레이드할 수도 있습니다.

:::image type="content" source="./media/concept-component/upgrade-component.png" lightbox="./media/concept-component/upgrade-component.png" alt-text="업그레이드 단추를 보여주는 스크린샷.":::

### <a name="delete-a-component"></a>구성 요소 삭제

를 사용하여 구성 요소를 삭제할 수 `az ml component delete --name <COMPONENT_NAME>` 있습니다. 

구성 요소를 선택하고 보관할 수도 있습니다.

:::image type="content" source="./media/concept-component/archive-component.png" alt-text="구성 요소의 보관 옵션을 보여주는 스크린샷.":::

## <a name="next-steps"></a>다음 단계

- [구성 요소 YAML 참조](reference-yaml-component-command.md)
- [ML 파이프라인 만들기 및 실행(CLI)](how-to-create-component-pipelines-cli.md)
- [디자이너에서 기계 학습 파이프라인 빌드](tutorial-designer-automobile-price-train-score.md)
