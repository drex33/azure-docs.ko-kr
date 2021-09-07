---
title: Azure Machine Learning 유추 HTTP 서버
titleSuffix: Azure Machine Learning
description: Azure Machine Learning 유추 HTTP 서버를 사용하여 로컬 개발을 사용하도록 설정하는 방법을 알아봅니다.
author: shivanissambare
ms.author: ssambare
ms.reviewer: larryfr
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.custom: inference server, local development, local debugging, devplatv2
ms.date: 05/14/2021
ms.openlocfilehash: 4d8c2dbbfe313d480fce953af0b39ee01cd32230
ms.sourcegitcommit: 5d605bb65ad2933e03b605e794cbf7cb3d1145f6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2021
ms.locfileid: "122597789"
---
# <a name="azure-machine-learning-inference-http-server-preview"></a>Azure Machine Learning 유추 HTTP 서버(미리 보기)

Azure Machine Learning 유추 HTTP 서버[(미리 보기)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)는 로컬 개발 환경에서 입력 스크립트(`score.py`)의 유효성을 쉽게 검사할 수 있도록 하는 Python 패키지입니다. 점수 매기기 스크립트에 문제가 있으면 서버에서 오류를 반환합니다. 또한 오류가 발생한 위치도 반환합니다.

연속 통합 및 배포 파이프라인에서 유효성 검사 게이트를 만들 때에도 서버를 사용할 수 있습니다. 예를 들어 후보 스크립트를 사용하여 서버를 시작하고 로컬 엔드포인트에 대해 테스트 도구 모음을 실행합니다.

## <a name="prerequisites"></a>필수 구성 요소

- 필요: Python >=3.7

## <a name="installation"></a>설치

> [!NOTE]
> 패키지 충돌을 방지하려면 가상 환경에 서버를 설치합니다.

`azureml-inference-server-http package`를 설치하려면 cmd/터미널에서 다음 명령을 실행합니다.

```bash
python -m pip install azureml-inference-server-http
```

## <a name="use-the-server"></a>서버 사용

1. 파일을 저장할 디렉터리를 만듭니다.

    ```bash
    mkdir server_quickstart
    cd server_quickstart
    ```

1. 패키지 충돌을 방지하려면 가상 환경을 만들고 활성화합니다.

    ```bash
    virtualenv myenv
    source myenv/bin/activate
    ```

1. [pypi](https://pypi.org/project/azureml-inference-server-http/) 피드에서 `azureml-inference-server-http` 패키지를 설치합니다.

    ```bash
    python -m pip install azureml-inference-server-http
    ```

1. 항목 스크립트(`score.py`)를 만듭니다. 다음 예제에서는 기본 항목 스크립트를 만듭니다.

    ```bash
    echo '
    import time

    def init():
        time.sleep(1)

    def run(input_data):
        return {"message":"Hello, World!"}
    ' > score.py
    ```

1. 서버를 시작하고 항목 스크립트로 `score.py`를 설정합니다.

    ```bash
    azmlinfsrv --entry_script score.py
    ```

    > [!NOTE]
    > 서버는 0.0.0.0에서 호스트됩니다. 즉, 호스팅 머신의 모든 IP 주소를 수신 대기합니다.

1. `curl`을 사용하여 서버에 점수 매기기 요청을 보냅니다.

    ```bash
    curl -p 127.0.0.1:5001/score
    ```

    서버는 다음과 같이 응답해야 합니다.

    ```bash
    {"message": "Hello, World!"}
    ```

이제 점수 매기기 스크립트를 수정하고 서버를 다시 실행하여 변경 내용을 테스트할 수 있습니다.

## <a name="server-routes"></a>서버 경로

서버가 이러한 경로에서 포트 5001에서 수신 대기하고 있습니다.

| 속성 | 경로|
| --- | --- |
| 활동성 프로브 | 127.0.0.1:5001/|
| 점수 | 127.0.0.1:5001/점수|

## <a name="server-parameters"></a>서버 매개 변수

다음 표에는 서버에서 허용하는 매개 변수가 나와 있습니다.

| 매개 변수 | 필수 | 기본값 | Description |
| ---- | --- | ---- | ----|
| entry_script | True | 해당 없음 | 점수 매기기 스크립트의 상대 또는 절대 경로입니다.|
| model_dir | 아니요 | 해당 없음 | 추론에 사용되는 모델을 보유하고 있는 디렉터리의 상대 또는 절대 경로입니다.
| 포트 | 거짓 | 5001 | 서버의 서비스 포트입니다.|
| worker_count | 거짓 | 1 | 동시 요청을 처리할 작업자 스레드 수입니다. |
| appinsights_instrumentation_key | 아니요 | 해당 없음 | 로그가 게시될 Application Insights에 대한 계측 키입니다. |

## <a name="request-flow"></a>요청 흐름

다음 단계에서는 Azure Machine Learning 유추 HTTP 서버에서 들어오는 요청을 처리하는 방법을 설명합니다.

1. python CLI 래퍼는 서버 네트워크 스택을 중심으로 하며 서버를 시작하는 데 사용됩니다.
1. 클라이언트는 서버에 요청을 보냅니다.
1. 요청이 수신되면 [WSGI](https://www.fullstackpython.com/wsgi-servers.html) 서버를 거치며 작업자 중 하나로 발송됩니다.
    - [Gunicorn](https://docs.gunicorn.org/)은 __Linux__ 에서 사용됩니다.
    - [Waitress](https://docs.pylonsproject.org/projects/waitress/)는 __Windows__ 에서 사용됩니다.
1. 그러면 요청이 [Flask](https://flask.palletsprojects.com/) 앱에 의해 처리되고 항목 스크립트 및 종속성이 로드됩니다.
1. 마지막으로 요청이 항목 스크립트에 전송됩니다. 그런 다음, 항목 스크립트는 로드된 모델에 대한 유추 호출을 수행하고 응답을 반환합니다.

:::image type="content" source="./media/how-to-inference-server-http/inference-server-architecture.png" alt-text="HTTP 서버 프로세스의 다이어그램":::

## <a name="how-to-integrate-with-visual-studio-code"></a>Visual Studio Code와 통합하는 방법

VSCode(Visual Studio Code) 및 [Python 확장](https://marketplace.visualstudio.com/items?itemName=ms-python.python)을 사용하여 [azureml-inference-server-http](https://pypi.org/project/azureml-inference-server-http/) 패키지로 디버그하는 방법에는 두 가지가 있습니다. 

1. 사용자는 명령줄에서 AzureML 유추 서버를 시작하고 VSCode + Python 확장을 사용하여 프로세스에 연결합니다.
1. 사용자는 VSCode에서 `launch.json`을 설정하고 VSCode 내에서 AzureML 유추 서버를 시작합니다.

두 가지 방법 모두에서 사용자는 중단점을 설정하고 단계별로 디버그할 수 있습니다.

## <a name="frequently-asked-questions"></a>질문과 대답

### <a name="do-i-need-to-reload-the-server-when-changing-the-score-script"></a>점수 매기기 스크립트를 변경할 때 서버를 다시 로드해야 하나요?

점수 매기기 스크립트(`score.py`)를 변경한 후에는 `ctrl + c`를 사용하여 서버를 중지합니다. 그런 다음, `azmlinfsrv --entry_script score.py`를 사용하여 다시 시작합니다.

### <a name="which-os-is-supported"></a>지원되는 OS는 무엇인가요?

Azure Machine Learning 유추 서버는 Windows 및 Linux 기반 운영 체제에서 실행됩니다.

## <a name="next-steps"></a>다음 단계

* 항목 스크립트를 만들고 모델을 배포하는 방법에 대한 자세한 내용은 [Azure Machine Learning을 사용하여 모델을 배포하는 방법](how-to-deploy-and-where.md)을 참조하세요.
* [유추를 위해 미리 빌드된 Docker 목록](concept-prebuilt-docker-images-inference.md)에 대해 자세히 알아보기