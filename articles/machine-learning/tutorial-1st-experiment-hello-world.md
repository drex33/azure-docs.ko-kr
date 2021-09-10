---
title: '자습서: Python 스크립트 시작'
titleSuffix: Azure Machine Learning
description: Azure Machine Learning에서 첫 번째 Python 스크립트를 시작합니다. 3부로 구성된 시작 시리즈 중 1부입니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: aminsaied
ms.author: amsaied
ms.reviewer: sgilley
ms.date: 04/27/2021
ms.custom: devx-track-python, FY21Q4-aml-seo-hack, contperf-fy21q4
ms.openlocfilehash: 472fcf4c7a1cc486db5aded40c87ffec2a9e796a
ms.sourcegitcommit: 9f1a35d4b90d159235015200607917913afe2d1b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/21/2021
ms.locfileid: "122634696"
---
# <a name="tutorial-get-started-with-a-python-script-in-azure-machine-learning-part-1-of-3"></a>자습서: Azure Machine Learning에서 Python 스크립트 시작(1/3부)

이 자습서에서는 Azure Machine Learning을 사용하여 클라우드에서 첫 번째 Python 스크립트를 실행합니다. 이 자습서는 ‘3부로 구성된 자습서 시리즈 중 1부’입니다.

이 자습서는 기계 학습 모델 학습처럼 복잡하지 않습니다. 클라우드에서 “Hello World” Python 스크립트를 실행합니다. 제어 스크립트를 사용하여 Azure Machine Learning에서 실행을 구성하고 만드는 방법을 알아봅니다.

이 자습서에서는 다음을 수행합니다.

> [!div class="checklist"]
> * "Hello world!" Python 스크립트
> * "Hello world!"를 Azure Machine Learning에 제출하는 Python 제어 스크립트를 만듭니다.
> * 제어 스크립트에서 Azure Machine Learning 개념을 이해합니다.
> * "Hello world!" 스크립트를 제출하고 실행합니다.
> * 클라우드에서 코드 출력 보기

## <a name="prerequisites"></a>사전 요구 사항

- [빠른 시작: 작업 영역을 설정하여 Azure Machine Learning 시작](quickstart-create-resources.md)을 완료하여 이 자습서 시리즈에서 사용할 작업 영역, 컴퓨팅 인스턴스 및 컴퓨팅 클러스터를 만듭니다.

## <a name="create-and-run-a-python-script"></a>Python 스크립트 만들기 및 실행

이 자습서에서는 컴퓨팅 인스턴스를 개발 컴퓨터로 사용합니다.  먼저 몇 개의 폴더와 스크립트를 만듭니다.

1. [Azure Machine Learning 스튜디오](https://ml.azure.com)에 로그인하고 메시지가 나타나면 작업 영역을 선택합니다.
1. 왼쪽에서 **Notebooks** 를 선택합니다.
1. **파일** 도구 모음에서 **+** 를 선택한 다음 **새 폴더 만들기** 를 선택합니다.
  :::image type="content" source="media/tutorial-1st-experiment-hello-world/create-folder.png" alt-text="도구 모음의 새 폴더 만들기 도구를 보여 주는 스크린샷":::
1. 폴더의 이름을 **get-started** 로 지정합니다.
1. 폴더 이름의 오른쪽에 **...** 를 사용하여 **get-started** 아래 다른 폴더를 만듭니다.
  :::image type="content" source="media/tutorial-1st-experiment-hello-world/create-sub-folder.png" alt-text="하위 폴더 만들기 메뉴를 보여 주는 스크린샷":::
1. 새 폴더 이름을 **src** 로 지정합니다.  파일 위치가 올바르지 않은 경우 **위치 편집** 링크를 사용합니다.
1. **src** 폴더의 오른쪽에 **...** 을 사용하여 **src** 폴더에 새 파일을 만듭니다. 
1. 파일 이름을 *hello.py* 로 지정합니다.  **파일 형식** 을 *Python(* .py)*으로 전환합니다.

이 코드를 파일에 복사합니다.

```python
# src/hello.py
print("Hello world!")
```

이제 프로젝트 폴더 구조가 다음과 같이 됩니다. 

:::image type="content" source="media/tutorial-1st-experiment-hello-world/directory-structure.png" alt-text="src 하위 폴더에서 hello.py를 보여 주는 폴더 구조.":::


### <a name="test-your-script"></a><a name="test"></a> 스크립트 테스트

코드를 로컬로 실행할 수 있습니다. 이 경우 컴퓨팅 인스턴스에서 실행한다는 의미입니다. 코드를 로컬로 실행하면 코드의 대화형 디버깅을 활용할 수 있습니다.  

이전에 컴퓨팅 인스턴스를 중지한 경우 컴퓨팅 드롭다운 오른쪽의 **컴퓨팅 시작** 도구로 시작합니다. 상태가 *실행 중* 으로 변경될 때까지 잠시 기다립니다.

:::image type="content" source="media/tutorial-1st-experiment-hello-world/start-compute.png" alt-text="중지된 컴퓨팅 인스턴스를 시작하는 것을 보여 주는 스크린샷":::

스크립트를 실행하려면 **터미널에서 스크립트 저장 및 실행** 을 선택합니다.

:::image type="content" source="media/tutorial-1st-experiment-hello-world/save-run-in-terminal.png" alt-text="도구 모음의 터미널 도구에서 스크립트를 저장하고 실행하는 것을 보여 주는 스크린샷":::

열린 터미널 창에서 스크립트의 출력을 볼 수 있습니다. 탭을 닫고 **종료** 를 선택하여 세션을 닫습니다.

## <a name="create-a-control-script"></a><a name="control-script"></a> 제어 스크립트 만들기

*제어 스크립트* 를 사용하면 서로 다른 컴퓨팅 리소스에서 `hello.py` 스크립트를 실행할 수 있습니다. 제어 스크립트를 사용하여 기계 학습 코드가 실행되는 방법과 위치를 제어합니다.  

**get-started** 폴더 끝의 **...** 을 선택하여 새 파일을 만듭니다.  *run-hello.py* 라는 Python 파일을 작성하고 다음 코드를 해당 파일에 복사하고 붙여넣습니다.

```python
# get-started/run-hello.py
from azureml.core import Workspace, Experiment, Environment, ScriptRunConfig

ws = Workspace.from_config()
experiment = Experiment(workspace=ws, name='day1-experiment-hello')

config = ScriptRunConfig(source_directory='./src', script='hello.py', compute_target='cpu-cluster')

run = experiment.submit(config)
aml_url = run.get_portal_url()
print(aml_url)
```

> [!TIP]
> 컴퓨팅 클러스터를 만들 때 다른 이름을 사용한 경우 코드 `compute_target='cpu-cluster'`의 이름도 조정합니다.

### <a name="understand-the-code"></a>코드 이해

제어 스크립트의 작동 방식은 다음과 같이 설명됩니다.

:::row:::
   :::column span="":::
      `ws = Workspace.from_config()`
   :::column-end:::
   :::column span="2":::
      [작업 영역](/python/api/azureml-core/azureml.core.workspace.workspace)은 Azure Machine Learning 작업 영역에 연결되므로 Azure Machine Learning 리소스와 통신할 수 있습니다.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      `experiment =  Experiment( ... )`
   :::column-end:::
   :::column span="2":::
      [실험](/python/api/azureml-core/azureml.core.experiment.experiment)은 여러 실행을 단일 이름으로 구성하는 간단한 방법을 제공합니다. 나중에 실험을 통해 수십 개의 실행 간에 메트릭을 쉽게 비교할 수 있는 방법을 확인할 수 있습니다.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      `config = ScriptRunConfig( ... )` 
   :::column-end:::
   :::column span="2":::
      [ScriptRunConfig](/python/api/azureml-core/azureml.core.scriptrunconfig)에서 `hello.py` 코드를 래핑하고 작업 영역에 전달합니다. 이름에서 알 수 있듯이 이 클래스를 사용하여 Azure Machine Learning에서 _스크립트_ 를 _실행_ 하는 방법을 _구성_ 할 수 있습니다. 또한 스크립트가 실행되는 컴퓨팅 대상을 지정합니다. 이 코드에서 대상은 [설정 자습서](./quickstart-create-resources.md)에서 만든 컴퓨팅 클러스터입니다.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      `run = experiment.submit(config)`
   :::column-end:::
   :::column span="2":::
       스크립트를 제출합니다. 이 제출을 [실행](/python/api/azureml-core/azureml.core.run%28class%29)이라고 합니다. 실행은 코드의 단일 실행을 캡슐화합니다. 실행을 사용하여 스크립트 진행률을 모니터링하고, 출력을 캡처하고, 결과를 분석하고, 메트릭을 시각화하는 등의 작업을 수행합니다.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      `aml_url = run.get_portal_url()` 
   :::column-end:::
   :::column span="2":::
        `run` 개체에서 코드 실행에 대한 핸들을 제공합니다. Python 스크립트에서 출력되는 URL을 사용하여 Azure Machine Learning 스튜디오에서 진행률을 모니터링합니다.  
   :::column-end:::
:::row-end:::


## <a name="submit-and-run-your-code-in-the-cloud"></a><a name="submit"></a> 클라우드에서 코드 제출 및 실행

1. **터미널에서 스크립트 저장 및 실행** 을 선택하여 컨트롤 스크립트를 실행합니다. 이어서 컴퓨팅 클러스터에서 [설정 자습서](quickstart-create-resources.md)에 따라 만든 `hello.py`을 실행합니다.

1. 터미널에서 인증하려면 로그인하라는 메시지가 표시될 수 있습니다.  코드를 복사하고 링크를 따라 이 단계를 완료합니다.

1. 인증이 완료되면 터미널에 링크가 표시됩니다. 실행을 보려면 링크를 선택합니다.

    [!INCLUDE [amlinclude-info](../../includes/machine-learning-py38-ignore.md)]

## <a name="view-the-output"></a>출력 보기

1. 열리는 페이지에서 실행 상태를 볼 수 있습니다.
1. 실행 상태가 **완료됨** 이면 페이지 상단에서 **출력 + 로그** 를 선택합니다.
1. 실행 결과를 보려면 **70_driver_log.txt** 를 선택합니다.

## <a name="monitor-your-code-in-the-cloud-in-the-studio"></a><a name="monitor"></a>스튜디오의 클라우드에서 코드 모니터링

스크립트의 출력에는 다음과 같은 스튜디오에 대한 링크(`https://ml.azure.com/experiments/hello-world/runs/<run-id>?wsid=/subscriptions/<subscription-id>/resourcegroups/<resource-group>/workspaces/<workspace-name>`)가 포함됩니다.

링크를 따릅니다.  처음에는 **큐 대기** 또는 **준비 중** 상태가 표시됩니다.  첫 번째 실행은 완료하는 데 5~10분 정도 걸립니다. 다음과 같은 경우에 발생합니다.

* Docker 이미지가 클라우드에 빌드됩니다.
* 컴퓨팅 클러스터의 크기가 0에서 1 노드로 조정됩니다.
* Docker 이미지가 컴퓨팅에 다운로드됩니다. 

후속 실행은 Docker 이미지가 컴퓨팅에 캐시되므로 훨씬 더 빠릅니다(~15초). 첫 번째 실행이 완료된 후 아래 코드를 다시 전송하여 테스트할 수 있습니다.

10분 정도 기다립니다.  실행이 완료되었음을 알리는 메시지가 표시됩니다. 그런 다음 **새로 고침** 을 사용하여 상태가 *완료됨* 으로 변경되었음을 확인합니다.  작업이 완료되면 **출력 + 로그** 탭으로 이동합니다. 여기서 다음과 같은 `70_driver_log.txt` 파일을 볼 수 있습니다.

```txt
 1: [2020-08-04T22:15:44.407305] Entering context manager injector.
 2: [context_manager_injector.py] Command line Options: Namespace(inject=['ProjectPythonPath:context_managers.ProjectPythonPath', 'RunHistory:context_managers.RunHistory', 'TrackUserError:context_managers.TrackUserError', 'UserExceptions:context_managers.UserExceptions'], invocation=['hello.py'])
 3: Starting the daemon thread to refresh tokens in background for process with pid = 31263
 4: Entering Run History Context Manager.
 5: Preparing to call script [ hello.py ] with arguments: []
 6: After variable expansion, calling script [ hello.py ] with arguments: []
 7:
 8: Hello world!
 9: Starting the daemon thread to refresh tokens in background for process with pid = 31263
10:
11:
12: The experiment completed successfully. Finalizing run...
13: Logging experiment finalizing status in history service.
14: [2020-08-04T22:15:46.541334] TimeoutHandler __init__
15: [2020-08-04T22:15:46.541396] TimeoutHandler __enter__
16: Cleaning up all outstanding Run operations, waiting 300.0 seconds
17: 1 items cleaning up...
18: Cleanup took 0.1812913417816162 seconds
19: [2020-08-04T22:15:47.040203] TimeoutHandler __exit__
```

8번 줄에 "Hello World!" 출력이 표시됩니다.

`70_driver_log.txt` 파일에는 실행의 표준 출력이 포함됩니다. 이 파일은 클라우드에서 원격 실행을 디버그할 때 유용할 수 있습니다.


## <a name="next-steps"></a>다음 단계

이 자습서에서는 간단한 "Hello world!" 스크립트를 사용하여 Azure에서 실행했습니다. Azure Machine Learning 작업 영역에 연결하고, 실험을 만들고, `hello.py` 코드를 클라우드에 제출하는 방법을 확인했습니다.

다음 자습서에서는 `print("Hello world!")`보다 더 흥미로운 항목을 실행하여 이러한 학습을 기반으로 합니다.

> [!div class="nextstepaction"]
> [자습서: 모델 학습](tutorial-1st-experiment-sdk-train.md)

>[!NOTE] 
> 여기서 자습서 시리즈를 완료하고 다음 단계로 진행하지 않으려면 [리소스를 정리](tutorial-1st-experiment-bring-data.md#clean-up-resources)하세요.