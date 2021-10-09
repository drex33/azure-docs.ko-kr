---
title: 패키지 관리
titleSuffix: Azure Machine Learning
description: 모델을 패키징합니다. 모델을 Docker 이미지로 패키징할 수 있습니다. 그런 다음, 다운로드하거나 Dockerfile을 만들고 사용하여 이미지를 빌드할 수 있습니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.date: 07/31/2020
ms.topic: how-to
ms.reviewer: larryfr
ms.custom: deploy
ms.openlocfilehash: 05af03c7ac60e2f98471ace4d57a296a54cf8ce6
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2021
ms.locfileid: "129707357"
---
# <a name="how-to-package-a-registered-model-with-docker"></a>Docker를 사용하여 등록된 모델을 패키징하는 방법

이 문서에서는 Docker를 사용하여 등록된 Azure Machine Learning 모델을 패키징하는 방법을 설명합니다.

## <a name="prerequisites"></a>사전 요구 사항

이 문서에서는 모델을 이미 학습시키고 기계 학습 작업 영역에 등록했다고 가정합니다. scikit-learn 모델을 학습시키고 등록하는 방법을 알아보려면 [이 자습서를 따르세요](how-to-train-scikit-learn.md).


## <a name="package-models"></a>패키지 관리

경우에 따라 모델을 배포 하지 않고 Docker 이미지를 만들 수 있습니다. 또는 이미지를 다운로드하여 로컬 Docker 설치에서 실행하려 할 수 있습니다. 이미지를 빌드하는 데 사용되는 파일을 다운로드, 검사, 수정하여 이미지를 수동으로 빌드하려 할 수도 있습니다.

모델을 패키징하면 이 모든 것을 수행할 수 있습니다. 모델을 웹 서비스로 호스팅하는 데 필요한 모든 자산을 패키징합니다. 이렇게 하면 완전히 빌드된 Docker 이미지나 빌드하는 데 필요한 파일을 다운로드할 수 있습니다. 모델 패키징을 사용하는 방법에는 두 가지가 있습니다.

**패키징된 모델 다운로드**: 웹 서비스로 호스팅하는 데 필요한 모델과 다른 파일이 포함된 Docker 이미지를 다운로드합니다.

**Dockerfile 생성**: Docker 이미지를 빌드하는 데 필요한 Dockerfile, 모델, 항목 스크립트 및 다른 자산을 다운로드합니다. 그런 다음, 이미지를 로컬에 빌드하기 전에 파일을 검사하거나 변경할 수 있습니다.

두 패키지 모두 로컬 Docker 이미지를 가져오는 데 사용될 수 있습니다.

> [!TIP]
> 패키지 만들기는 모델 배포와 비슷합니다. 등록된 모델과 유추 구성을 사용합니다.

> [!IMPORTANT]
> 완전히 빌드된 이미지를 다운로드하거나 이미지를 로컬에 빌드하려면 개발 환경에 [Docker](https://www.docker.com)가 설치되어 있어야 합니다.

### <a name="download-a-packaged-model"></a>패키징된 모델 다운로드

다음 예제에서는 작업 영역의 Azure 컨테이너 레지스트리에 등록된 이미지를 빌드합니다.

```python
package = Model.package(ws, [model], inference_config)
package.wait_for_creation(show_output=True)
```

패키지를 만든 후 `package.pull()`을 사용하여 이미지를 로컬 Docker 환경에 가져올 수 있습니다. 이 명령의 출력에 이미지 이름이 표시됩니다. 예를 들면 다음과 같습니다. 

`Status: Downloaded newer image for myworkspacef78fd10.azurecr.io/package:20190822181338`. 

모델을 다운로드한 후 `docker images` 명령을 사용하여 로컬 이미지를 나열합니다.

```text
REPOSITORY                               TAG                 IMAGE ID            CREATED             SIZE
myworkspacef78fd10.azurecr.io/package    20190822181338      7ff48015d5bd        4 minutes ago       1.43 GB
```

이 이미지에 따라 로컬 컨테이너를 시작하려면 다음 명령을 사용하여 셸이나 명령줄에서 이름이 지정된 컨테이너를 시작합니다. `<imageid>` 값을 `docker images` 명령으로 반환된 이미지 ID로 바꿉니다.

```bash
docker run -p 6789:5001 --name mycontainer <imageid>
```

이 명령은 `myimage`라는 이미지의 최신 버전을 시작합니다. 로컬 포트 6789를 웹 서비스가 수신 대기 중인 컨테이너의 포트(5001)에 매핑합니다. 또한 컨테이너를 쉽게 중지할 수 있도록 `mycontainer` 이름을 컨테이너에 할당합니다. 컨테이너가 시작되면 요청을 `http://localhost:6789/score`에 제출할 수 있습니다.

### <a name="generate-a-dockerfile-and-dependencies"></a>Dockerfile 및 종속성 생성

다음 예제에서는 이미지를 로컬에 빌드하는 데 필요한 Dockerfile, 모델 및 다른 자산을 다운로드하는 방법을 보여줍니다. `generate_dockerfile=True` 매개 변수는 완전히 빌드된 이미지가 아닌 파일을 원하고 있음을 나타냅니다.

```python
package = Model.package(ws, [model], inference_config, generate_dockerfile=True)
package.wait_for_creation(show_output=True)
# Download the package.
package.save("./imagefiles")
# Get the Azure container registry that the model/Dockerfile uses.
acr=package.get_container_registry()
print("Address:", acr.address)
print("Username:", acr.username)
print("Password:", acr.password)
```

이 코드는 이미지를 빌드하는 데 필요한 파일을 `imagefiles` 디렉터리로 다운로드합니다. 저장된 파일에 포함된 Dockerfile은 Azure 컨테이너 레지스트리에 저장된 기본 이미지를 참조합니다. 로컬 Docker 설치에 이미지를 빌드하면 주소, 사용자 이름 및 암호를 사용하여 레지스트리에 인증해야 합니다. 다음 단계를 수행하여 로컬 Docker 설치를 사용해 이미지를 빌드합니다.

1. 셸 또는 명령줄 세션에서 다음 명령을 사용하여 Azure 컨테이너 레지스트리로 Docker를 인증합니다. `<address>`, `<username>` 및 `<password>`를 `package.get_container_registry()`에서 검색한 값으로 바꿉니다.

    ```bash
    docker login <address> -u <username> -p <password>
    ```

2. 이미지를 빌드하려면 다음 명령을 사용합니다. `<imagefiles>`를 파일을 저장한 `package.save()`가 있는 디렉터리의 경로로 바꿉니다.

    ```bash
    docker build --tag myimage <imagefiles>
    ```

    이 명령은 이미지 이름을 `myimage`로 설정합니다.

이미지가 빌드되었는지 확인하려면 `docker images` 명령을 사용합니다. 목록에 `myimage` 이미지가 표시되어야 합니다.

```text
REPOSITORY      TAG                 IMAGE ID            CREATED             SIZE
<none>          <none>              2d5ee0bf3b3b        49 seconds ago      1.43 GB
myimage         latest              739f22498d64        3 minutes ago       1.43 GB
```

이 이미지에 따라 새 컨테이너를 시작하려면 다음 명령을 사용합니다.

```bash
docker run -p 6789:5001 --name mycontainer myimage:latest
```

이 명령은 `myimage`라는 이미지의 최신 버전을 시작합니다. 로컬 포트 6789를 웹 서비스가 수신 대기 중인 컨테이너의 포트(5001)에 매핑합니다. 또한 컨테이너를 쉽게 중지할 수 있도록 `mycontainer` 이름을 컨테이너에 할당합니다. 컨테이너가 시작되면 요청을 `http://localhost:6789/score`에 제출할 수 있습니다.

### <a name="example-client-to-test-the-local-container"></a>로컬 컨테이너를 테스트하는 예제 클라이언트

다음 코드는 컨테이너에서 사용할 수 있는 Python 클라이언트의 예제입니다.

```python
import requests
import json

# URL for the web service.
scoring_uri = 'http://localhost:6789/score'

# Two sets of data to score, so we get two results back.
data = {"data":
        [
            [ 1,2,3,4,5,6,7,8,9,10 ],
            [ 10,9,8,7,6,5,4,3,2,1 ]
        ]
        }
# Convert to JSON string.
input_data = json.dumps(data)

# Set the content type.
headers = {'Content-Type': 'application/json'}

# Make the request and display the response.
resp = requests.post(scoring_uri, input_data, headers=headers)
print(resp.text)
```

다른 프로그래밍 언어의 예제 클라이언트는 [웹 서비스로 배포된 모델 사용](how-to-consume-web-service.md)을 참조하세요.

### <a name="stop-the-docker-container"></a>Docker 컨테이너 중지

컨테이너를 중지하려면 다른 셸이나 명령줄에서 다음 명령을 사용합니다.

```bash
docker kill mycontainer
```

## <a name="next-steps"></a>다음 단계

* [실패한 배포 문제 해결](how-to-troubleshoot-deployment.md)
* [Azure Kubernetes Service로 배포](how-to-deploy-azure-kubernetes-service.md)
* [웹 서비스를 사용하는 클라이언트 애플리케이션 만들기](how-to-consume-web-service.md)
* [웹 서비스 업데이트](how-to-deploy-update-web-service.md)
* [사용자 지정 Docker 이미지를 사용하여 모델을 배포하는 방법](./how-to-deploy-custom-container.md)
* [TLS를 사용하여 Azure Machine Learning을 통해 웹 서비스 보호](how-to-secure-web-service.md)
* [Application Insights를 사용하여 Azure Machine Learning 모델 모니터링](how-to-enable-app-insights.md)
* [프로덕션 환경에서 모델용 데이터 수집](how-to-enable-data-collection.md)
* [모델 배포에 대한 이벤트 경고 및 트리거 만들기](how-to-use-event-grid.md)