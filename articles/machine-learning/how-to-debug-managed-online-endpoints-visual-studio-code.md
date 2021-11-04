---
title: VS Code에서 로컬로 온라인 끝점 디버그 (미리 보기)
titleSuffix: Azure Machine Learning
description: Azure에 배포 하기 전에 Visual Studio Code를 사용 하 여 온라인 끝점을 로컬로 테스트 하 고 디버그 하는 방법을 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
author: luisquintanilla
ms.author: luquinta
ms.date: 11/03/2021
ms.topic: troubleshooting
ms.custom: devplatv2
ms.openlocfilehash: 33b46c490912d0c651c5629da17795efe074b6d9
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131570025"
---
# <a name="debug-online-endpoints-locally-in-visual-studio-code-preview"></a>Visual Studio Code에서 로컬로 온라인 끝점 디버그 (미리 보기)

Visual Studio Code (VS Code) 디버거를 사용 하 여 Azure에 배포 하기 전에 온라인 끝점을 로컬로 테스트 및 디버그 하는 방법을 알아봅니다.

로컬 끝점 Azure Machine Learning 점수 매기기 스크립트, 환경 구성, 코드 구성 및 기계 학습 모델을 로컬로 테스트 하 고 디버그 하는 데 도움이 됩니다.

## <a name="online-endpoint-local-debugging"></a>온라인 끝점 로컬 디버깅

클라우드에 끝점을 배포 하기 전에 끝점을 로컬로 디버깅 하면 이전에 코드 및 구성에서 오류를 catch 하는 데 도움이 될 수 있습니다. VS Code를 사용 하 여 끝점을 로컬로 디버깅 하기 위한 여러 옵션이 있습니다.

- [Azure Machine Learning 유추 HTTP 서버(미리 보기)](how-to-inference-server-http.md)
- 로컬 엔드포인트

이 가이드는 로컬 끝점을 중심으로 설명 합니다.

다음 표에서는 가장 적합 한 작업을 선택 하는 데 도움이 되는 시나리오의 개요를 제공 합니다.

| 시나리오 | 유추 HTTP 서버 | 로컬 엔드포인트 |
|--|--|--|
| Docker 이미지 다시 빌드 **없이** 로컬 python 환경 업데이트 | 예 | 예 |
| 점수 매기기 스크립트 업데이트 | 예 | 예 |
| 배포 구성 업데이트 (배포, 환경, 코드, 모델) | 예 | 예 |
| VS Code 디버거 통합 | 예 | 예 |

## <a name="prerequisites"></a>필수 구성 요소

이 가이드에서는 다음 항목이 PC에 로컬로 설치 되어 있다고 가정 합니다.

- [Docker](https://docs.docker.com/engine/install/)
- [VS 코드](https://code.visualstudio.com/#alt-downloads)
- [Azure CLI](/cli/azure/install-azure-cli)
- [Azure CLI `ml` 확장](how-to-configure-cli.md)

자세한 내용은 [관리 되는 온라인 끝점을 배포 하기 위해 시스템을 준비 하는 방법](how-to-deploy-managed-online-endpoints.md#prepare-your-system)에 대 한 가이드를 참조 하세요.

이 문서의 예제는 [azureml-예제](https://github.com/azure/azureml-examples) 리포지토리에 포함 된 코드 샘플을 기반으로 합니다. YAML 및 기타 파일을 복사/붙여넣기 하지 않고 로컬에서 명령을 실행 하려면 리포지토리를 복제 한 다음 디렉터리를 `cli` 리포지토리의 디렉터리로 변경 합니다.

```azurecli
git clone https://github.com/Azure/azureml-examples --depth 1
cd azureml-examples
cd cli
```

Azure CLI에 대한 기본값을 아직 설정하지 않은 경우 기본 설정을 저장합니다. 구독, 작업 영역 및 리소스 그룹에 대 한 값이 여러 번 전달 되지 않도록 하려면 다음 명령을 사용 합니다. 다음 매개 변수를 특정 구성에 대 한 값으로 바꿉니다.

* `<subscription>` 를 Azure 구독 ID로 바꿉니다.
* `<workspace>`을(를) Azure Machine Learning 작업 영역 이름으로 바꿉니다.
* 을 `<resource-group>` 작업 영역을 포함 하는 Azure 리소스 그룹으로 바꿉니다.
* 을 `<location>` 작업 영역을 포함 하는 Azure 지역으로 바꿉니다.

> [!TIP]
> 명령을 사용 하 여 현재 기본값을 확인할 수 있습니다 `az configure -l` .

```azurecli
az account set --subscription <subscription>
az configure --defaults workspace=<workspace> group=<resource-group> location=<location>
```

## <a name="launch-development-container"></a>개발 컨테이너 시작

로컬 끝점 Azure Machine Learning Docker 및 VS Code development 컨테이너 (dev container)를 사용 하 여 로컬 디버깅 환경을 빌드하고 구성 합니다. 개발자 컨테이너를 사용 하 여 Docker 컨테이너 내에서 VS Code 기능을 활용할 수 있습니다. Dev 컨테이너에 대 한 자세한 내용은 [개발 컨테이너 만들기](https://code.visualstudio.com/docs/remote/create-dev-container)를 참조 하세요.

VS Code에서 로컬로 온라인 끝점을 디버깅 하려면 `--vscode-debug` 온라인 배포를 만들거나 업데이트 하 고 Azure Machine Learning 때 플래그를 사용 합니다. 다음 명령은 예 리포지토리의 배포 예제를 사용 합니다.

```azurecli
az ml online-deployment create --file endpoints/online/managed/sample/blue-deployment.yml --local --vscode-debug
```

> [!IMPORTANT]
> wsl (Linux용 Windows 하위 시스템)에서 VS Code 실행 파일의 경로를 포함 하도록 path 환경 변수를 업데이트 하거나 wsl interop를 사용 해야 합니다. 자세한 내용은 [Linux와 Windows 상호 운용성](/windows/wsl/interop)을 참조 하세요.

Docker 이미지는 로컬로 빌드됩니다. 환경 구성 또는 모델 파일 오류는 프로세스의이 단계에서 표시 됩니다.

> [!NOTE]
> 신규 또는 업데이트 된 개발 컨테이너를 처음 시작 하는 경우 몇 분 정도 걸릴 수 있습니다.

이미지가 성공적으로 빌드되면 dev 컨테이너가 VS Code 창에서 열립니다.

몇 가지 VS Code 확장을 사용 하 여 dev 컨테이너에서 배포를 디버그할 수 있습니다. Azure Machine Learning는 dev 컨테이너에 이러한 확장을 자동으로 설치 합니다.

- 유추 디버그
- [Pylance](https://marketplace.visualstudio.com/items?itemName=ms-python.vscode-pylance)
- [Jupyter](https://marketplace.visualstudio.com/items?itemName=ms-toolsai.jupyter)
- [Python](https://marketplace.visualstudio.com/items?itemName=ms-python.python)

> [!IMPORTANT]
> 디버그 세션을 시작 하기 전에 VS Code 확장이 dev 컨테이너에서 설치를 완료 했는지 확인 합니다.  

## <a name="start-debug-session"></a>디버그 세션 시작

환경을 설정한 후에는 VS Code 디버거를 사용 하 여 배포를 로컬로 테스트 하 고 디버그할 수 있습니다.

1. Visual Studio Code에서 점수 매기기 스크립트를 엽니다.

    > [!TIP]
    > 이전에 배포 된 끝점에서 사용 하는 score.py 스크립트는 `azureml-samples/cli/endpoints/online/managed/sample/score.py` 복제 한 리포지토리의에 있습니다. 그러나이 가이드의 단계에서는 모든 점수 매기기 스크립트를 사용 합니다.

1. 점수 매기기 스크립트의 아무 곳에 나 중단점을 설정 합니다.

    - 시작 동작을 디버깅 하려면 함수 내에 중단점을 추가 `init` 합니다.
    - 점수 매기기 동작을 디버깅 하려면 함수 내에 중단점을 추가 `run` 합니다.

1. VS Code 실행 보기를 선택 합니다.
1. 실행 및 디버그 드롭다운에서 **Azure ML: 디버그 로컬 끝점** 을 선택 하 여 끝점을 로컬로 디버깅 하기 시작 합니다.

    실행 뷰의 **중단점** 섹션에서 다음을 확인 합니다.

    - **발생 한 예외** 를 **선택 취소** 합니다.
    - **Catch** 되지 않은 예외 **검사**

    :::image type="content" source="media/how-to-debug-managed-online-endpoints-visual-studio-code/configure-debug-profile.png" alt-text="Azure ML 디버그 로컬 환경 디버그 프로필 구성":::

1. 실행 및 디버그 드롭다운 옆의 재생 아이콘을 선택 하 여 디버깅 세션을 시작 합니다.

    이 시점에서 함수의 모든 중단점이 `init` catch 됩니다. 디버그 작업을 사용 하 여 코드를 단계별로 실행 합니다. 디버그 작업에 대 한 자세한 내용은 [디버그 작업 가이드](https://code.visualstudio.com/Docs/editor/debugging#_debug-actions)를 참조 하세요.

VS Code 디버거에 대 한 자세한 내용은 [의 디버깅](https://code.visualstudio.com/Docs/editor/debugging) 을 참조 하세요 VS Code

## <a name="debug-your-endpoint"></a>끝점 디버그

이제 응용 프로그램이 디버거에서 실행 되 고 있으므로 점수 매기기 스크립트를 디버그 하는 예측을 만들어 보세요.

`ml`확장 명령을 사용 `invoke` 하 여 로컬 끝점에 대 한 요청을 만듭니다.

```azurecli
az ml online-endpoint invoke --name <ENDPOINT-NAME> --request-file <REQUEST-FILE> --local
```

이 경우 `<REQUEST-FILE>` 는 다음 json과 유사 하 게 예측 하기 위해 모델에 대 한 입력 데이터 샘플이 포함 된 json 파일입니다.

```json
{"data": [
    [1,2,3,4,5,6,7,8,9,10], 
    [10,9,8,7,6,5,4,3,2,1]
]}
```

> [!TIP]
> 점수 매기기 URI는 끝점이 요청을 수신 대기 하는 주소입니다. 확장을 사용 `ml` 하 여 점수 매기기 URI를 가져옵니다.
>
>    ```azurecli
>    az ml online-endpoint show --name <ENDPOINT-NAME> --local
>    ```
>
> 출력은 다음과 비슷해야 합니다.
>
> ```json
> {
>  "auth_mode": "aml_token",
>  "location": "local",
>  "name": "my-new-endpoint",
>  "properties": {},
>  "provisioning_state": "Succeeded",
>  "scoring_uri": "http://localhost:5001/score",
>  "tags": {},
>  "traffic": {},
>  "type": "online"
>}
>```
>
>점수 매기기 URI는 속성에서 찾을 수 있습니다 `scoring_uri` .

이 시점에서 함수의 모든 중단점이 `run` catch 됩니다. 디버그 작업을 사용 하 여 코드를 단계별로 실행 합니다. 디버그 작업에 대 한 자세한 내용은 [디버그 작업 가이드](https://code.visualstudio.com/Docs/editor/debugging#_debug-actions)를 참조 하세요.

## <a name="edit-your-endpoint"></a>끝점 편집

응용 프로그램을 디버그 하 고 문제를 해결할 때 점수 매기기 스크립트 및 구성을 업데이트 해야 하는 시나리오가 있습니다.

코드에 변경 내용을 적용 하려면 다음을 수행 합니다.

1. 코드 업데이트
1. 명령 팔레트의 명령을 사용 하 여 디버그 세션을 다시 시작 `Developer: Reload Window` 합니다. 자세한 내용은 [명령 팔레트 설명서](https://code.visualstudio.com/docs/getstarted/userinterface#_command-palette)를 참조 하세요.

> [!NOTE]
> 코드 및 끝점 자산이 포함 된 디렉터리는 dev 컨테이너에 탑재 되므로 dev 컨테이너에서 변경한 내용은 로컬 파일 시스템과 동기화 됩니다.

환경 및 끝점 구성에 대 한 업데이트와 관련 된 광범위 한 변경 내용은 `ml` 확장 `update` 명령을 사용 합니다. 이렇게 하면 변경 내용으로 전체 이미지 다시 작성이 트리거됩니다.

```azurecli
az ml online-deployment update --file <DEPLOYMENT-YAML-SPECIFICATION-FILE> --local --vscode-debug
```

업데이트 된 이미지가 빌드되고 개발 컨테이너가 시작 되 면 VS Code 디버거를 사용 하 여 업데이트 된 끝점을 테스트 하 고 문제를 해결 합니다.

## <a name="next-steps"></a>다음 단계

- [관리형 온라인 엔드포인트(미리 보기)를 사용하여 기계 학습 모델 배포 및 채점](how-to-deploy-managed-online-endpoints.md)
- [관리형 온라인 엔드포인트 배포 및 채점(미리 보기) 문제 해결](how-to-troubleshoot-managed-online-endpoints.md)
