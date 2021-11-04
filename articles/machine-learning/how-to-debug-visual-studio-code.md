---
title: Visual Studio Code를 사용한 대화형 디버깅
titleSuffix: Azure Machine Learning
description: Visual Studio Code를 사용하여 Azure Machine Learning 코드, 파이프라인 및 배포를 대화형으로 디버그합니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: mlops
ms.topic: how-to
author: luisquintanilla
ms.author: luquinta
ms.date: 10/21/2021
ms.openlocfilehash: eb1f4fb0e3f833bdcc9631f72c6ffe127005a0e3
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131556486"
---
# <a name="interactive-debugging-with-visual-studio-code"></a>Visual Studio Code를 사용한 대화형 디버깅

VS Code(Visual Studio Code) 및 [debugpy](https://github.com/microsoft/debugpy/)를 사용하여 Azure Machine Learning 실험, 파이프라인 및 배포를 대화형으로 디버그하는 방법에 대해 알아봅니다.

## <a name="run-and-debug-experiments-locally"></a>로컬로 실험 실행 및 디버그

Azure Machine Learning 확장을 사용하여 기계 학습 실험을 클라우드에 전송하기 전에 유효성 검사, 실행 및 디버그할 수 있습니다.

### <a name="prerequisites"></a>사전 요구 사항

* Azure Machine Learning VS Code 확장(미리 보기). 자세한 내용은 [Azure Machine Learning VS Code 확장 설정](how-to-setup-vs-code.md)을 참조하세요.

    > [!IMPORTANT]
    > Azure Machine Learning VS Code 확장은 기본적으로 CLI(v2)를 사용합니다. 이 가이드의 지침에서는 1.0 CLI를 사용합니다. 1\.0 CLI로 전환하려면 Visual Studio Code의 `azureML.CLI Compatibility Mode` 설정을 `1.0`으로 설정합니다. Visual Studio Code 설정을 수정하는 자세한 내용은 [사용자 및 작업 영역 설정 설명서를 참조하세요.](https://code.visualstudio.com/docs/getstarted/settings)

* [Docker](https://www.docker.com/get-started)
  * Mac 및 Windows용 Docker Desktop
  * Linux용 Docker 엔진

    > [!NOTE]
    > Windows에서 [Linux 컨테이너를 사용하도록 Docker를 구성](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers)해야 합니다.

    > [!TIP]
    > Windows의 경우 필수는 아니지만 [WSL(Linux용 Windows 하위 시스템) 2에서 Docker를 사용](/windows/wsl/tutorials/wsl-containers#install-docker-desktop)하는 것이 좋습니다.

* [Python 3](https://www.python.org/downloads/)

### <a name="debug-experiment-locally"></a>로컬로 실험 디버그

> [!IMPORTANT]
> 실험을 로컬로 실행하기 전에 다음을 확인합니다.
>
> * Docker가 실행되고 있습니다.
> * `azureML.CLI Compatibility Mode`Visual Studio Code 설정은 `1.0` 필수 구성에 지정된 대로 로 설정됩니다.

1. VS Code에서 Azure Machine Learning 확장 보기를 엽니다.
1. 작업 영역이 포함된 구독 노드를 펼칩니다. 아직 없는 경우 확장을 사용하여 [Azure Machine Learning 작업 영역을 만들](how-to-manage-resources-vscode.md#create-a-workspace) 수 있습니다.
1. 작업 영역 노드를 펼칩니다.
1. 마우스 오른쪽 단추로 **실험** 노드를 클릭하고 **실험 만들기** 를 선택합니다. 프롬프트가 표시되면 실험 이름을 입력합니다.
1. **실험** 노드를 펼치고, 마우스 오른쪽 단추로 실행하려는 실험을 클릭한 다음, **실험 실행** 을 선택합니다.
1. 실험을 실행하기 위한 옵션 목록에서 **로컬** 을 선택합니다.
1. **Windows에서만 처음 사용합니다**. 파일 공유를 허용할지 묻는 메시지가 표시되면 **예** 를 선택합니다. 파일 공유를 사용하도록 설정하면 Docker에서 스크립트가 포함된 디렉터리를 컨테이너에 탑재할 수 있습니다. 또한 Docker는 실행의 로그 및 출력을 시스템의 임시 디렉터리에 저장할 수도 있습니다.
1. **예** 를 선택하여 실험을 디버그합니다. 그렇지 않은 경우 **아니요** 를 선택합니다. '아니요'가 선택되면 디버거에 연결하지 않고 로컬로 실험을 실행합니다.
1. **새 실행 구성 만들기** 를 선택하여 실행 구성을 만듭니다. 실행 구성은 실행하려는 스크립트, 종속성 및 사용되는 데이터 세트를 정의합니다. 또는 이미 있는 경우 드롭다운에서 해당 항목을 선택합니다.
    1. 환경을 선택합니다. [큐레이팅된 Azure Machine Learning](resource-curated-environments.md) 중에서 선택하거나 직접 만들 수 있습니다.
    1. 실행하려는 스크립트의 이름을 제공합니다. 경로는 VS Code에서 열린 디렉터리를 기준으로 하는 상대 경로입니다.
    1. Azure Machine Learning 데이터 세트를 사용할지 여부를 선택합니다. 확장을 사용하여 [Azure Machine Learning 데이터 세트](how-to-manage-resources-vscode.md#create-dataset)를 만들 수 있습니다.
    1. 실험을 실행하는 컨테이너에 디버거를 연결하려면 debugpy가 필요합니다. debugpy를 종속성으로 추가하려면 **debugpy 추가** 를 선택합니다. 그렇지 않으면 **건너뛰기** 를 선택합니다. debugpy를 종속성으로 추가하지 않으면 디버거에 연결하지 않고 실험을 실행합니다.
    1. 편집기에서 실행 구성 설정이 포함된 구성 파일이 열립니다. 설정에 만족하면 **실험 제출** 을 선택합니다. 또는 메뉴 모음에서 명령 팔레트(**보기 > 명령 팔레트**)를 열고, 텍스트 상자에서 `Azure ML: Submit experiment` 명령을 입력합니다.
1. 실험이 제출되면 스크립트 및 실행 구성에 지정된 구성이 포함된 Docker 이미지가 만들어집니다.

    Docker 이미지 빌드 프로세스가 시작되면 `60_control_log.txt` 파일의 내용이 VS Code의 출력 콘솔로 스트림됩니다.

    > [!NOTE]
    > Docker 이미지를 처음 만드는 경우 몇 분 정도 걸릴 수 있습니다.

1. 이미지가 빌드되면 디버거를 시작하라는 메시지가 표시됩니다. 스크립트에서 중단점을 설정하고, 디버깅을 시작할 준비가 되면 **디버거 시작** 을 선택합니다. 이렇게 하면 VS Code 디버거가 실험을 실행하는 컨테이너에 연결됩니다. 또는 Azure Machine Learning 확장에서 마우스로 현재 실행에 대한 노드 위를 가리키고, 재생 아이콘을 선택하여 디버거를 시작합니다.

    > [!IMPORTANT]
    > 단일 실험에는 여러 디버그 세션을 사용할 수 없습니다. 그러나 여러 VS Code 인스턴스를 사용하여 둘 이상의 실험을 디버그할 수 있습니다.

이 시점에서 VS Code를 사용하여 코드를 단계별로 실행하고 디버그할 수 있어야 합니다.

실행을 취소하려면 마우스 오른쪽 단추로 실행 노드를 클릭하고, **실행 취소** 를 선택합니다.

원격 실험 실행과 마찬가지로 실행 노드를 펼쳐 로그와 출력을 검사할 수 있습니다.

> [!TIP]
> 사용자 환경에 정의된 동일한 종속성을 사용하는 Docker 이미지는 실행 간에 다시 사용됩니다. 그러나 새 환경 또는 다른 환경을 사용하여 실험을 실행하면 새 이미지가 만들어집니다. 이러한 이미지는 로컬 스토리지에 저장되므로 오래되었거나 사용되지 않는 Docker 이미지를 제거하는 것이 좋습니다. 시스템에서 이미지를 제거하려면 [Docker CLI](https://docs.docker.com/engine/reference/commandline/rmi/) 또는 [VS Code Docker 확장](https://code.visualstudio.com/docs/containers/overview)을 사용하세요.

## <a name="debug-and-troubleshoot-machine-learning-pipelines"></a>기계 학습 파이프라인 디버그 및 문제 해결

경우에 따라 ML 파이프라인에 사용된 Python 코드를 대화형으로 디버그해야 할 수도 있습니다. VS Code 및 debugpy를 사용하면 학습 환경에서 실행되는 코드에 연결할 수 있습니다.

### <a name="prerequisites"></a>사전 요구 사항

* __Azure Virtual Network__ 를 사용하도록 구성된 __Azure Machine Learning 작업 영역__
* Python 스크립트를 파이프라인 단계의 일부로 사용하는 __Azure Machine Learning 파이프라인__. 예를 들어 PythonScriptStep입니다.
* __가상 네트워크__ 에 있고 __파이프라인에서 학습을 위해 사용__ 하는 Azure Machine Learning 컴퓨팅 클러스터
* __가상 네트워크__ 에 있는 __개발 환경__. 개발 환경은 다음 중 하나일 수 있습니다.

  * 가상 네트워크의 Azure Virtual Machine
  * 가상 네트워크에 있는 Notebook VM의 컴퓨팅 인스턴스
  * VPN 또는 ExpressRoute를 통해 가상 네트워크에 대한 개인 네트워크 연결이 있는 클라이언트 컴퓨터

Azure Machine Learning에서 Azure Virtual Network를 사용하는 방법에 대한 자세한 내용은 [가상 네트워크 격리 및 개인 정보 개요](how-to-network-security-overview.md)를 참조하세요.

> [!TIP]
> 가상 네트워크 내에 없는 Azure Machine Learning 리소스를 사용할 수 있지만 가상 네트워크를 사용하는 것이 좋습니다.

### <a name="how-it-works"></a>작동 방식

ML 파이프라인 단계는 Python 스크립트를 실행합니다. 이러한 스크립트는 다음 작업을 수행하도록 수정됩니다.

1. 실행되는 호스트의 IP 주소를 기록합니다. IP 주소를 사용하여 디버거를 스크립트에 연결합니다.

2. debugpy 디버그 구성 요소를 시작하고, 디버거가 연결될 때까지 기다립니다.

3. 개발 환경에서 학습 프로세스를 통해 만들어진 로그를 모니터링하여 스크립트가 실행되는 IP 주소를 찾습니다.

4. `launch.json` 파일을 사용하여 디버거를 연결할 IP 주소를 VS Code에 알려줍니다.

5. 디버거를 연결하고, 스크립트를 대화형으로 단계별로 실행합니다.

### <a name="configure-python-scripts"></a>Python 스크립트 구성

디버깅을 사용하도록 설정하려면 ML 파이프라인의 단계에서 사용되는 Python 스크립트를 다음과 같이 변경합니다.

1. 다음 import 문을 추가합니다.

    ```python
    import argparse
    import os
    import debugpy
    import socket
    from azureml.core import Run
    ```

1. 다음 인수를 추가합니다. 이러한 인수를 사용하면 필요에 따라 디버거를 사용하도록 설정하고 디버거 연결 시간 제한을 설정할 수 있습니다.

    ```python
    parser.add_argument('--remote_debug', action='store_true')
    parser.add_argument('--remote_debug_connection_timeout', type=int,
                        default=300,
                        help=f'Defines how much time the AML compute target '
                        f'will await a connection from a debugger client (VSCODE).')
    parser.add_argument('--remote_debug_client_ip', type=str,
                        help=f'Defines IP Address of VS Code client')
    parser.add_argument('--remote_debug_port', type=int,
                        default=5678,
                        help=f'Defines Port of VS Code client')
    ```

1. 다음 명령문을 추가합니다. 이러한 명령문은 코드가 실행되는 노드의 IP 주소를 기록할 수 있도록 현재 실행 컨텍스트를 로드합니다.

    ```python
    global run
    run = Run.get_context()
    ```

1. debugpy를 시작하고 디버거가 연결될 때까지 기다리는 `if` 문을 추가합니다. 디버거가 시간 제한 이전에 연결되지 않더라도 스크립트는 정상적으로 계속됩니다. `listen` 함수의 `HOST` 및 `PORT` 값을 사용자 고유의 값으로 바꾸세요.

    ```python
    if args.remote_debug:
        print(f'Timeout for debug connection: {args.remote_debug_connection_timeout}')
        # Log the IP and port
        try:
            ip = args.remote_debug_client_ip
        except:
            print("Need to supply IP address for VS Code client")
        print(f'ip_address: {ip}')
        debugpy.listen(address=(ip, args.remote_debug_port))
        # Wait for the timeout for debugger to attach
        debugpy.wait_for_client()
        print(f'Debugger attached = {debugpy.is_client_connected()}')
    ```

다음 Python 예제에서는 디버깅을 사용하도록 설정하는 간단한 `train.py` 파일을 보여 줍니다.

```python
# Copyright (c) Microsoft. All rights reserved.
# Licensed under the MIT license.

import argparse
import os
import debugpy
import socket
from azureml.core import Run

print("In train.py")
print("As a data scientist, this is where I use my training code.")

parser = argparse.ArgumentParser("train")

parser.add_argument("--input_data", type=str, help="input data")
parser.add_argument("--output_train", type=str, help="output_train directory")

# Argument check for remote debugging
parser.add_argument('--remote_debug', action='store_true')
parser.add_argument('--remote_debug_connection_timeout', type=int,
                    default=300,
                    help=f'Defines how much time the AML compute target '
                    f'will await a connection from a debugger client (VSCODE).')
parser.add_argument('--remote_debug_client_ip', type=str,
                    help=f'Defines IP Address of VS Code client')
parser.add_argument('--remote_debug_port', type=int,
                    default=5678,
                    help=f'Defines Port of VS Code client')

# Get run object, so we can find and log the IP of the host instance
global run
run = Run.get_context()

args = parser.parse_args()

# Start debugger if remote_debug is enabled
if args.remote_debug:
    print(f'Timeout for debug connection: {args.remote_debug_connection_timeout}')
    # Log the IP and port
    # ip = socket.gethostbyname(socket.gethostname())
    try:
        ip = args.remote_debug_client_ip
    except:
        print("Need to supply IP address for VS Code client")
    print(f'ip_address: {ip}')
    debugpy.listen(address=(ip, args.remote_debug_port))
    # Wait for the timeout for debugger to attach
    debugpy.wait_for_client()
    print(f'Debugger attached = {debugpy.is_client_connected()}')

print("Argument 1: %s" % args.input_data)
print("Argument 2: %s" % args.output_train)

if not (args.output_train is None):
    os.makedirs(args.output_train, exist_ok=True)
    print("%s created" % args.output_train)
```

### <a name="configure-ml-pipeline"></a>ML 파이프라인 구성

debugpy를 시작하고 실행 컨텍스트를 가져오는 데 필요한 Python 패키지를 제공하려면 환경을 만들고 `pip_packages=['debugpy', 'azureml-sdk==<SDK-VERSION>']`을 설정합니다. SDK 버전을 사용하는 것과 일치하도록 변경합니다. 다음 코드 조각에서는 환경을 만드는 방법을 보여 줍니다.

```python
# Use a RunConfiguration to specify some additional requirements for this step.
from azureml.core.runconfig import RunConfiguration
from azureml.core.conda_dependencies import CondaDependencies
from azureml.core.runconfig import DEFAULT_CPU_IMAGE

# create a new runconfig object
run_config = RunConfiguration()

# enable Docker 
run_config.environment.docker.enabled = True

# set Docker base image to the default CPU-based image
run_config.environment.docker.base_image = DEFAULT_CPU_IMAGE

# use conda_dependencies.yml to create a conda environment in the Docker image for execution
run_config.environment.python.user_managed_dependencies = False

# specify CondaDependencies obj
run_config.environment.python.conda_dependencies = CondaDependencies.create(conda_packages=['scikit-learn'],
                                                                           pip_packages=['debugpy', 'azureml-sdk==<SDK-VERSION>'])
```

[Python 스크립트 구성](#configure-python-scripts) 섹션에서 새 인수가 ML 파이프라인 단계에서 사용하는 스크립트에 추가되었습니다. 다음 코드 조각에서는 이러한 인수를 사용하여 디버깅을 구성 요소에 사용하도록 설정하고 시간 제한을 설정하는 방법을 보여 줍니다. 또한 `runconfig=run_config`를 설정하여 이전에 만든 환경을 사용하는 방법도 보여 줍니다.

```python
# Use RunConfig from a pipeline step
step1 = PythonScriptStep(name="train_step",
                         script_name="train.py",
                         arguments=['--remote_debug', '--remote_debug_connection_timeout', 300,'--remote_debug_client_ip','<VS-CODE-CLIENT-IP>','--remote_debug_port',5678],
                         compute_target=aml_compute,
                         source_directory=source_directory,
                         runconfig=run_config,
                         allow_reuse=False)
```

파이프라인이 실행되면 각 단계에서 자식 실행을 만듭니다. 디버깅이 사용하도록 설정된 경우 수정된 스크립트는 자식 실행에 대한 `70_driver_log.txt`의 다음 텍스트와 비슷한 정보를 기록합니다.

```text
Timeout for debug connection: 300
ip_address: 10.3.0.5
```

`ip_address` 값을 저장합니다. 해당 정보는 다음 섹션에서 사용됩니다.

> [!TIP]
> 이 파이프라인 단계의 자식 실행에 대한 실행 로그에서 IP 주소를 찾을 수도 있습니다. 이 정보를 확인하는 방법에 대한 자세한 내용은 [Azure ML 실험 실행 및 메트릭 모니터링](how-to-log-view-metrics.md)을 참조하세요.

### <a name="configure-development-environment"></a>개발 환경 구성

1. debugpy를 VS Code 개발 환경에 설치하려면 다음 명령을 사용합니다.

    ```bash
    python -m pip install --upgrade debugpy
    ```

    VS Code에서 debugpy를 사용하는 방법에 대한 자세한 내용은 [원격 디버깅](https://code.visualstudio.com/docs/python/debugging#_debugging-by-attaching-over-a-network-connection)을 참조하세요.

1. 디버거를 실행하는 Azure Machine Learning 컴퓨팅과 통신하도록 VS Code를 구성하려면 새 디버그 구성을 만듭니다.

    1. VS Code에서 __디버그__ 메뉴를 선택한 다음, __구성 열기__ 를 선택합니다. __launch.json__ 이라는 파일이 열립니다.

    1. __launch.json__ 파일에서 `"configurations": [`가 포함된 줄을 찾고, 다음 텍스트를 그 뒤에 삽입합니다. `"host": "<IP-ADDRESS>"` 항목을 이전 섹션의 로그에 반환된 IP 주소로 변경합니다. `"localRoot": "${workspaceFolder}/code/step"` 항목을 디버그하고 있는 스크립트의 복사본이 포함된 로컬 디렉터리로 변경합니다.

        ```json
        {
            "name": "Azure Machine Learning Compute: remote debug",
            "type": "python",
            "request": "attach",
            "port": 5678,
            "host": "<IP-ADDRESS>",
            "redirectOutput": true,
            "pathMappings": [
                {
                    "localRoot": "${workspaceFolder}/code/step1",
                    "remoteRoot": "."
                }
            ]
        }
        ```

        > [!IMPORTANT]
        > 이미 다른 항목이 구성 섹션에 있으면 삽입한 코드 뒤에 쉼표(,)를 추가합니다.

        > [!TIP]
        > 특히 파이프라인의 경우 코드가 각 단계에만 관련되도록 스크립트의 리소스를 개별 디렉터리에 보관하는 것이 가장 좋습니다. 이 예제에서 `localRoot` 예제 값은 `/code/step1`을 참조합니다.
        >
        > 여러 스크립트를 디버그하는 경우 서로 다른 디렉터리에서 각 스크립트에 대한 별도의 구성 섹션을 만듭니다.

    1. __launch.json__ 파일을 저장합니다.

### <a name="connect-the-debugger"></a>디버거 연결

1. VS Code를 열고. 스크립트의 로컬 복사본을 엽니다.
2. 연결되면 스크립트를 중지하려는 중단점을 설정합니다.
3. 자식 프로세스에서 스크립트를 실행하고 `Timeout for debug connection`이 로그에 표시되는 동안 F5 키를 사용하거나 __디버그__ 를 선택합니다. 메시지가 표시되면 __Azure Machine Learning 컴퓨팅: 원격 디버그__ 구성을 선택합니다. 또한 사이드바에서 디버그 아이콘을 선택하고, [디버그] 드롭다운 메뉴에서 __Azure Machine Learning: 원격 디버그__ 항목을 선택한 다음, 녹색 화살표를 사용하여 디버거를 연결할 수도 있습니다.

    이 시점에서 VS Code는 컴퓨팅 노드의 debugpy에 연결하고 이전에 설정한 중단점에서 중지합니다. 이제 실행되는 코드를 단계별로 실행하고 변수를 보는 등의 작업을 수행할 수 있습니다.

    > [!NOTE]
    > 로그에 `Debugger attached = False`라는 항목이 표시되면 시간 제한이 만료되고 스크립트가 디버거 없이 계속된 것입니다. 파이프라인을 다시 제출하고, `Timeout for debug connection` 메시지 후 및 시간 제한이 만료되기 전에 디버거를 연결하세요.

## <a name="debug-and-troubleshoot-deployments"></a>배포 디버그 및 문제 해결

경우에 따라 모델 배포에 포함된 Python 코드를 대화형으로 디버그해야 할 수도 있습니다. 예를 들어 항목 스크립트가 실패하고 추가 로깅으로 이유를 확인할 수 없는 경우입니다. VS Code 및 debugpy를 사용하여 Docker 컨테이너 내에서 실행되는 코드에 연결할 수 있습니다.

> [!TIP]
> 관리되는 온라인 엔드포인트 및 배포를 로컬로 디버깅하여 시간을 절약하고 버그를 조기에 catch합니다. 자세한 내용은 [Visual Studio Code 로컬로 관리되는 온라인 엔드포인트 디버그(미리 보기)를](how-to-debug-managed-online-endpoints-visual-studio-code.md)참조하세요.

> [!IMPORTANT]
> `Model.deploy()` 및 `LocalWebservice.deploy_configuration`을 사용하여 모델을 로컬로 배포하는 경우 이 디버깅 방법이 작동하지 않습니다. 대신 [Model.package()](/python/api/azureml-core/azureml.core.model.model#package-workspace--models--inference-config-none--generate-dockerfile-false-) 메서드를 사용하여 이미지를 만들어야 합니다.

로컬 웹 서비스를 배포하려면 로컬 시스템에서 작동하는 Docker를 설치해야 합니다. Docker를 사용하는 방법에 대한 자세한 내용은 [Docker 설명서](https://docs.docker.com/)를 참조하세요. 컴퓨팅 인스턴스를 사용할 때는 Docker가 이미 설치되어 있습니다.

### <a name="configure-development-environment"></a>개발 환경 구성

1. debugpy를 로컬 VS Code 개발 환경에 설치하려면 다음 명령을 사용합니다.

    ```bash
    python -m pip install --upgrade debugpy
    ```

    VS Code에서 debugpy를 사용하는 방법에 대한 자세한 내용은 [원격 디버깅](https://code.visualstudio.com/docs/python/debugging#_debugging-by-attaching-over-a-network-connection)을 참조하세요.

1. Docker 이미지와 통신하도록 VS Code를 구성하려면 새 디버그 구성을 만듭니다.

    1. VS Code의 __실행__ 확장에서 __디버그__ 메뉴를 선택한 다음, __구성 열기__ 를 선택합니다. __launch.json__ 이라는 파일이 열립니다.

    1. __launch.json__ 파일에서 __"configurations"__ 항목(`"configurations": [`이 포함된 줄)을 찾고, 다음 텍스트를 그 뒤에 삽입합니다. 

        ```json
        {
            "name": "Azure Machine Learning Deployment: Docker Debug",
            "type": "python",
            "request": "attach",
            "connect": {
                "port": 5678,
                "host": "0.0.0.0",
            },
            "pathMappings": [
                {
                    "localRoot": "${workspaceFolder}",
                    "remoteRoot": "/var/azureml-app"
                }
            ]
        }
        ```
        삽입되면 __launch.json__ 파일이 다음과 비슷합니다.
        ```json
        {
        // Use IntelliSense to learn about possible attributes.
        // Hover to view descriptions of existing attributes.
        // For more information, visit: https://go.microsoft.com/fwlink/linkid=830387
        "version": "0.2.0",
        "configurations": [
            {
                "name": "Python: Current File",
                "type": "python",
                "request": "launch",
                "program": "${file}",
                "console": "integratedTerminal"
            },
            {
                "name": "Azure Machine Learning Deployment: Docker Debug",
                "type": "python",
                "request": "attach",
                "connect": {
                    "port": 5678,
                    "host": "0.0.0.0"
                    },
                "pathMappings": [
                    {
                        "localRoot": "${workspaceFolder}",
                        "remoteRoot": "/var/azureml-app"
                    }
                ]
            }
            ]
        }
        ```

        > [!IMPORTANT]
        > 이미 다른 항목이 구성 섹션에 있는 경우 삽입한 코드 뒤에 쉼표( __,__ )를 추가합니다.

        이 섹션에서는 __5678__ 포트를 사용하여 Docker 컨테이너에 연결됩니다.

    1. __launch.json__ 파일을 저장합니다.

### <a name="create-an-image-that-includes-debugpy"></a>debugpy가 포함된 이미지 만들기

1. debugpy를 포함하도록 배포에 대한 conda 환경을 수정합니다. 다음 예제에서는 `pip_packages` 매개 변수를 사용하여 추가하는 방법을 보여 줍니다.

    ```python
    from azureml.core.conda_dependencies import CondaDependencies 


    # Usually a good idea to choose specific version numbers
    # so training is made on same packages as scoring
    myenv = CondaDependencies.create(conda_packages=['numpy==1.15.4',
                                'scikit-learn==0.19.1', 'pandas==0.23.4'],
                                 pip_packages = ['azureml-defaults==1.0.83', 'debugpy'])

    with open("myenv.yml","w") as f:
        f.write(myenv.serialize_to_string())
    ```

1. debugpy를 시작하고 서비스가 시작될 때 연결을 기다리려면 다음을 `score.py` 파일의 위쪽에 추가합니다.

    ```python
    import debugpy
    # Allows other computers to attach to debugpy on this IP address and port.
    debugpy.listen(('0.0.0.0', 5678))
    # Wait 30 seconds for a debugger to attach. If none attaches, the script continues as normal.
    debugpy.wait_for_client()
    print("Debugger attached...")
    ```

1. 환경 정의를 기반으로 하는 이미지를 만들고, 이 이미지를 로컬 레지스트리로 끌어옵니다. 

    > [!NOTE]
    > 다음 예제에서는 `ws`에서 Azure Machine Learning 작업 영역을 가리키고 `model`이 배포할 모델이라고 가정합니다. `myenv.yml` 파일에는 1단계에서 만든 conda 종속성이 포함되어 있습니다.

    ```python
    from azureml.core.conda_dependencies import CondaDependencies
    from azureml.core.model import InferenceConfig
    from azureml.core.environment import Environment


    myenv = Environment.from_conda_specification(name="env", file_path="myenv.yml")
    myenv.docker.base_image = None
    myenv.docker.base_dockerfile = "FROM mcr.microsoft.com/azureml/openmpi3.1.2-ubuntu18.04:20210615.v1"
    inference_config = InferenceConfig(entry_script="score.py", environment=myenv)
    package = Model.package(ws, [model], inference_config)
    package.wait_for_creation(show_output=True)  # Or show_output=False to hide the Docker build logs.
    package.pull()
    ```

    이미지가 만들어지고 다운로드되면(이 프로세스는 10분 넘게 걸릴 수 있으므로 잠시 기다림) 마지막으로 이미지 경로(리포지토리, 이름 및 태그 포함, 이 경우 digest이기도 함)가 다음과 비슷한 메시지에 표시됩니다.

    ```text
    Status: Downloaded newer image for myregistry.azurecr.io/package@sha256:<image-digest>
    ```

1. 이미지 작업을 더 쉽게 로컬로 수행하려면 다음 명령을 사용하여 이 이미지에 대한 태그를 추가할 수 있습니다. 다음 명령의 `myimagepath`를 이전 단계의 위치 값으로 바꿉니다.

    ```bash
    docker tag myimagepath debug:1
    ```

    나머지 단계에서는 전체 이미지 경로 값 대신 로컬 이미지를 `debug:1`로 참조할 수 있습니다.

### <a name="debug-the-service"></a>서비스 디버깅

> [!TIP]
> `score.py` 파일에서 debugpy 연결에 대한 시간 제한을 설정하는 경우 시간 제한이 만료되기 전에 VS Code를 디버그 세션에 연결해야 합니다. VS Code를 시작하고, `score.py`의 로컬 복사본을 열고, 중단점을 설정하고, 이 섹션의 단계를 사용하기 전에 이동할 수 있도록 준비합니다.
>
> 디버깅 및 중단점 설정에 대한 자세한 내용은 [디버깅](https://code.visualstudio.com/Docs/editor/debugging)을 참조하세요.

1. 이미지를 사용하여 Docker 컨테이너를 시작하려면 다음 명령을 사용합니다.

    ```bash
    docker run -it --name debug -p 8000:5001 -p 5678:5678 -v <my_local_path_to_score.py>:/var/azureml-app/score.py debug:1 /bin/bash
    ```

    이렇게 하면 `score.py`가 컨테이너의 항목에 로컬로 연결됩니다. 따라서 편집기에서 변경한 내용이 자동으로 컨테이너에 반영됩니다.

2. 더 나은 환경을 위해 새 VS Code 인터페이스를 사용하여 컨테이너로 이동할 수 있습니다. VS Code 사이드바에서 `Docker` 확장을 선택하고, 만든 로컬 컨테이너(이 설명서에서는 `debug:1`임)를 찾습니다. 마우스 오른쪽 단추로 이 컨테이너를 클릭하고, `"Attach Visual Studio Code"`를 선택합니다. 그러면 새 VS Code 인터페이스가 자동으로 열리고 이 인터페이스에서 사용자가 만든 컨테이너의 내부를 표시합니다.

    ![컨테이너 VS Code 인터페이스](./media/how-to-troubleshoot-deployment/container-interface.png)

3. 컨테이너 내의 셸에서 다음 명령을 실행합니다.

    ```bash
    runsvdir /var/runit
    ```
    그러면 컨테이너 내의 셸에서 다음 출력이 표시됩니다.

    ![컨테이너 실행 콘솔 출력](./media/how-to-troubleshoot-deployment/container-run.png)

4. VS Code를 컨테이너 내의 debugpy에 연결하려면 VS Code를 열고, F5 키를 사용하거나 __디버그__ 를 선택합니다. 메시지가 표시되면 __Azure Machine Learning 배포: Docker 디버그__ 구성을 선택합니다. 또한 사이드바에서 __실행__ 확장을 선택하고, [디버그] 드롭다운 메뉴에서 __Azure Machine Learning 배포: Docker 디버그__ 항목을 선택한 다음, 녹색 화살표를 사용하여 디버거를 연결할 수도 있습니다.

    ![디버그 아이콘, 디버깅 시작 단추 및 구성 선택기](./media/how-to-troubleshoot-deployment/start-debugging.png)
    
    녹색 화살표를 클릭하고 디버거를 연결하면 컨테이너 VS Code 인터페이스에서 몇 가지 새로운 정보가 표시됩니다.
    
    ![컨테이너 디버거 연결됨 정보](./media/how-to-troubleshoot-deployment/debugger-attached.png)
    
    또한 기본 VS Code 인터페이스에 표시되는 항목은 다음과 같습니다.

    ![score.py의 VS Code 중단점](./media/how-to-troubleshoot-deployment/local-debugger.png)

이제 컨테이너에 연결된 로컬 `score.py`가 이미 설정한 중단점에서 중지되었습니다. 이 시점에서 VS Code는 Docker 컨테이너 내의 debugpy에 연결하고, 이전에 설정한 중단점에서 Docker 컨테이너를 중지합니다. 이제 실행되는 코드를 단계별로 실행하고 변수를 보는 등의 작업을 수행할 수 있습니다.

VS Code를 사용하여 Python을 디버그하는 방법에 대한 자세한 내용은 [Python 코드 디버그](https://code.visualstudio.com/docs/python/debugging)를 참조하세요.

### <a name="stop-the-container"></a>컨테이너 중지

컨테이너를 중지하려면 다음 명령을 사용합니다.

```bash
docker stop debug
```

## <a name="next-steps"></a>다음 단계

이제 VS Code 원격을 설정했으므로 컴퓨팅 인스턴스를 VS Code의 원격 컴퓨팅으로 사용하여 코드를 대화형으로 디버그할 수 있습니다. 

문제 해결에 대해 자세히 알아보세요.

* [로컬 모델 배포](how-to-troubleshoot-deployment-local.md)
* [원격 모델 배포](how-to-troubleshoot-deployment.md)
* [기계 학습 파이프라인](how-to-debug-pipelines.md)
* [ParallelRunStep](how-to-debug-parallel-run-step.md)

