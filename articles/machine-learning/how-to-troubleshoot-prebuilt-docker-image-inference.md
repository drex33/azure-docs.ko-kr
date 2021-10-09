---
title: 미리 빌드된 Docker 이미지 문제 해결
titleSuffix: Azure Machine Learning
description: 유추를 위해 미리 빌드된 Docker 이미지를 사용하기 위한 문제 해결 단계입니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: mlops
ms.author: ssambare
author: shivanissambare
ms.date: 10/07/2021
ms.topic: how-to
ms.reviewer: larryfr
ms.custom: deploy, docker, prebuilt, troubleshoot
ms.openlocfilehash: 59a3709ef6145fa723d1919429fec4bf62a0998b
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2021
ms.locfileid: "129705337"
---
# <a name="troubleshooting-prebuilt-docker-images-for-inference"></a>유추를 위해 미리 작성 한 docker 이미지 문제 해결

Azure Machine Learning를 사용 하 여 유추를 위해 미리 빌드된 docker 이미지를 사용 하는 경우 발생할 수 있는 문제를 해결 하는 방법을 알아봅니다.

> [!IMPORTANT]
> Azure Machine Learning를 통해 미리 작성 된 [Docker 이미지에 Python 패키지 확장성](how-to-prebuilt-docker-images-inference-python-extensibility.md) 을 사용 하는 것은 현재 미리 보기 상태입니다. 미리 보기 기능은 지원 또는 서비스 수준 계약의 보증 없이 “있는 그대로” 제공됩니다. 자세한 내용은 [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

## <a name="model-deployment-failed"></a>모델 배포 실패

모델 배포에 실패하면 [Azure Machine Learning Studio](https://ml.azure.com/)에 로그가 표시되지 않고 `service.get_logs()`가 None을 반환합니다.
Score.py의 init() 함수에 문제가 있는 경우 `service.get_logs()`는 동일한 로그를 반환합니다.

따라서 아래에 표시된 명령 중 하나를 사용하여 컨테이너를 로컬에서 실행하고 `<MCR-path>`를 이미지 경로로 바꿔야 합니다. 이미지 및 경로 목록은 [유추를 위해 미리 빌드된 Docker 이미지](concept-prebuilt-docker-images-inference.md)를 참조하세요.

### <a name="mounting-extensibility-solution"></a>확장성 솔루션 탑재

`score.py`가 포함된 디렉터리로 이동한 후 다음을 실행합니다.

```bash
docker run -it -v $(pwd):/var/azureml-app -e AZUREML_EXTRA_PYTHON_LIB_PATH="myenv/lib/python3.7/site-packages" <mcr-path>
```

### <a name="requirementstxt-extensibility-solution"></a>requirements.txt 확장성 솔루션

`score.py`가 포함된 디렉터리로 이동한 후 다음을 실행합니다.

```bash
docker run -it -v $(pwd):/var/azureml-app -e AZUREML_EXTRA_REQUIREMENTS_TXT="requirements.txt" <mcr-path>
```

## <a name="enable-local-debugging"></a>로컬 디버깅 사용

로컬 유추 서버를 사용하면 항목 스크립트(`score.py`)를 신속하게 디버그할 수 있습니다. 기본 점수 스크립트에 버그가 있는 경우 서버에서 모델을 초기화하거나 제공하지 못합니다. 대신 예외 및 문제가 발생한 위치를 throw합니다. [Azure Machine Learning 유추 HTTP 서버에 대한 자세한 정보](how-to-inference-server-http.md)

## <a name="for-common-model-deployment-issues"></a>일반적인 모델 배포 문제

Azure Machine Learning에서 ACI(Azure Container Instances) 또는 AKS(Azure Kubernetes Service)로 모델을 배포할 때 발생하는 문제는 [모델 배포 문제 해결](how-to-troubleshoot-deployment.md)을 참조하세요.

## <a name="init-or-run-failing-to-write-a-file"></a>init() 또는 run()이 파일 쓰기에 실패함

미리 빌드된 Docker 이미지의 HTTP 서버는 *루트가 아닌 사용자* 로 실행되며 일부 디렉터리에 대한 액세스 권한이 없을 수 있습니다. 액세스 권한이 있는 디렉터리에만 씁니다. 예를 들어, 컨테이너의 `/tmp` 디렉터리입니다.

## <a name="extra-python-packages-not-installed"></a>추가 Python 패키지가 설치되지 않음

* 환경 변수 또는 파일 이름에 오타가 있는지 확인합니다.
* 컨테이너 로그를 확인하여 `pip install -r <your_requirements.txt>`가 설치되어 있는지 확인합니다.
* [유추 구성](/python/api/azureml-core/azureml.core.model.inferenceconfig#constructor) 생성자에서 소스 디렉터리가 올바르게 설정되었는지 확인합니다.
* 설치를 찾을 수 없고 로그에 "파일을 찾을 수 없음"이 표시되면 로그에 표시된 파일 이름이 올바른지 확인합니다.
* 설치가 시작되었지만 실패했거나 시간이 초과된 경우 클린 환경(즉, 캐시 디렉터리 없음, `pip install --no-cache-dir -r requriements.txt`)에서 동일한 Python 및 pip 버전을 사용하여 로컬로 동일한 `requirements.txt`를 설치해보세요. 문제를 로컬에서 재현할 수 있는지 확인합니다.

## <a name="mounting-solution-failed"></a>솔루션 탑재 실패

* 환경 변수 또는 디렉터리 이름에 오타가 있는지 확인합니다.
* 환경 변수는 `score.py` 파일의 상대 경로로 설정해야 합니다.
* [유추 구성](/python/api/azureml-core/azureml.core.model.inferenceconfig#constructor) 생성자에서 소스 디렉터리가 올바르게 설정되었는지 확인합니다.
* 디렉터리는 해당 환경의 "site-packages" 디렉터리여야 합니다.
* `score.py`가 여전히 `ModuleNotFound`를 반환하고 모듈이 탑재된 디렉터리에 있어야 하는 경우 `init()` 또는 `run()`에서 `sys.path`를 인쇄하여 누락된 경로가 있는지 확인합니다.

## <a name="building-an-image-based-on-the-prebuilt-docker-image-failed"></a>미리 빌드된 Docker 이미지를 기반으로 이미지 빌드 실패

* apt 패키지를 설치하는 동안 실패한 경우 apt 명령을 실행하기 전에 사용자가 루트로 설정되었는지 확인합니다. (루트가 아닌 사용자로 다시 전환해야 합니다.) 

## <a name="image-built-based-on-the-prebuilt-docker-image-cant-boot-up"></a>미리 빌드된 Docker 이미지를 기반으로 빌드된 이미지를 부팅할 수 없음

* 루트가 아닌 사용자는 `dockeruser`이어야 합니다. 그렇지 않으면 다음 디렉터리의 소유자를 이미지를 실행할 때 사용할 사용자 이름으로 설정해야 합니다.

    ```bash
    /var/runit
    /var/log
    /var/lib/nginx
    /run
    /opt/miniconda
    /var/azureml-app
    ```

* `ENTRYPOINT`가 새로 빌드된 이미지에서 변경된 경우 HTTP 서버 및 관련 구성 요소를 `runsvdir /var/runit`으로 로드해야 합니다.

## <a name="next-steps"></a>다음 단계

* [Python 패키지를 미리 빌드된 이미지에 추가](how-to-prebuilt-docker-images-inference-python-extensibility.md)합니다.
* [미리 빌드된 패키지를 새 Dockerfile의 기반으로 사용](how-to-extend-prebuilt-docker-image-inference.md)합니다.