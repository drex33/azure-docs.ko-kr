---
title: Azure App Service에 ML 모델 배포(미리 보기)
titleSuffix: Azure Machine Learning
description: Azure App Service를 사용하여 학습된 ML 모델을 웹앱에 배포하기 위해 Azure Machine Learning을 사용하는 방법을 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: mlops
ms.author: ssambare
author: shivanissambare
ms.reviewer: larryfr
ms.date: 10/21/2021
ms.topic: how-to
ms.custom: devx-track-python, deploy, devx-track-azurecli
ms.openlocfilehash: d4628dde4e8b0090b7c815ea50415b8a54524db2
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131557740"
---
# <a name="deploy-a-machine-learning-model-to-azure-app-service-preview"></a>Azure App Service에 기계 학습 모델 배포(미리 보기)


Azure App Service에서 Azure Machine Learning의 모델을 웹앱으로 배포하는 방법을 알아봅니다.

> [!IMPORTANT]
> Azure Machine Learning과 Azure App Service는 모두 일반적으로 사용할 수 있지만 Machine Learning Service에서 App Service로 모델을 배포하는 기능은 미리 보기 상태입니다.

Azure Machine Learning을 사용하면 학습된 기계 학습 모델에서 Docker 이미지를 만들 수 있습니다. 이 이미지에는 데이터를 수신하고 모델에 제출한 다음, 응답을 반환하는 웹 서비스가 포함되어 있습니다. Azure App Service는 이미지를 배포하는 데 사용할 수 있으며 다음과 같은 기능을 제공합니다.

* 보안 강화를 위한 고급 [인증](../app-service/configure-authentication-provider-aad.md). 인증 방법에는 Azure Active Directory 및 다단계 인증이 모두 포함됩니다.
* 다시 배포할 필요 없이 [자동 크기 조정](../azure-monitor/autoscale/autoscale-get-started.md?toc=%2fazure%2fapp-service%2ftoc.json)
* 클라이언트와 서비스 간의 보안 통신을 위한 [TLS 지원](../app-service/configure-ssl-certificate-in-code.md)

Azure App Service에서 제공하는 기능에 대한 자세한 내용은 [App Service 개요](../app-service/overview.md)를 참조하세요.

> [!IMPORTANT]
> 배포된 모델에 사용된 점수 매기기 데이터 또는 점수 매기기 결과를 기록하는 기능이 필요한 경우 대신 Azure Kubernetes Service에 배포해야 합니다. 자세한 내용은 [프로덕션 모델에 대한 데이터 수집](how-to-enable-data-collection.md)을 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

* Azure Machine Learning 작업 영역 자세한 내용은 [작업 영역 만들기](how-to-manage-workspace.md) 문서를 참조하세요.
* [Azure CLI](/cli/azure/install-azure-cli)
* 작업 영역에 등록된 학습된 기계 학습 모델 모델이 없는 경우 [이미지 분류 자습서: 모델 학습](tutorial-train-models-with-aml.md)을 사용하여 모델을 학습시키고 등록합니다.

    > [!IMPORTANT]
    > 이 문서의 코드 조각에서는 다음 변수가 설정되었다고 가정합니다.
    >
    > * `ws` - Azure Machine Learning 작업 영역입니다.
    > * `model` - 배포될 등록된 모델입니다.
    > * `inference_config` - 모델의 유추 구성입니다.
    >
    > 이러한 변수를 설정하는 방법에 대한 자세한 내용은 [Azure Machine Learning을 사용하여 모델 배포](how-to-deploy-and-where.md)를 참조하세요.

## <a name="prepare-for-deployment"></a>배포 준비

배포하기 전에 모델을 웹 서비스로 실행하는 데 필요한 항목을 정의해야 합니다. 다음 목록에서는 배포에 필요한 핵심 항목에 대해 설명합니다.

* __항목 스크립트__. 이 스크립트는 요청을 수락하고, 모델을 사용하여 요청을 채점하고, 결과를 반환합니다.

    > [!IMPORTANT]
    > 항목 스크립트는 모델에 따라 다릅니다. 들어오는 요청 데이터의 형식, 모델에 필요한 데이터의 형식 및 클라이언트에 반환되는 데이터의 형식을 이해해야 합니다.
    >
    > 요청 데이터가 모델에서 사용할 수 없는 형식인 경우 스크립트가 이를 허용되는 형식으로 변환할 수 있습니다. 응답을 클라이언트에 반환하기 전에 변환할 수도 있습니다.

    > [!IMPORTANT]
    > Azure Machine Learning SDK는 웹 서비스에서 데이터 저장소 또는 데이터 세트에 액세스하는 방법을 제공하지 않습니다. Azure Storage 계정에서와 같이 배포 외부에 저장된 데이터에 액세스하기 위해 배포된 모델이 필요한 경우 관련 SDK를 사용하여 사용자 지정 코드 솔루션을 개발해야 합니다. 예를 들면 [Azure Storage SDK for Python](https://github.com/Azure/azure-storage-python)입니다.
    >
    > 시나리오에 사용할 수 있는 또 다른 대안은 점수를 매길 때 데이터 저장소에 대한 액세스를 제공하는 [일괄 처리 예측](./tutorial-pipeline-batch-scoring-classification.md)입니다.

    항목 스크립트에 대한 자세한 내용은 [Azure Machine Learning을 사용하여 모델 배포](how-to-deploy-and-where.md)를 참조하세요.

* 항목 스크립트나 모델을 실행하는 데 필요한 도우미 스크립트 또는 Python/Conda 패키지 같은 **종속성**.

이러한 엔터티는 __유추 구성__ 에 캡슐화됩니다. 추론 구성은 항목 스크립트 및 기타 종속성을 참조합니다.

> [!IMPORTANT]
> Azure App Service에 사용할 추론 구성을 만들 때는 [환경](/python/api/azureml-core/azureml.core.environment(class)) 개체를 사용해야 합니다. 사용자 지정 환경을 정의하는 경우 pip 종속성으로 버전이 1.0.45 이상인 azureml-defaults를 추가해야 합니다. 이 패키지에는 모델을 웹 서비스로 호스팅하는 데 필요한 기능이 포함되어 있습니다. 다음 예제에서는 환경 개체를 만들고 유추 구성에서 사용하는 방법을 보여 줍니다.
>
> ```python
> from azureml.core.environment import Environment
> from azureml.core.conda_dependencies import CondaDependencies
> from azureml.core.model import InferenceConfig
>
> # Create an environment and add conda dependencies to it
> myenv = Environment(name="myenv")
> # Enable Docker based environment
> myenv.docker.enabled = True
> # Build conda dependencies
> myenv.python.conda_dependencies = CondaDependencies.create(conda_packages=['scikit-learn'],
>                                                            pip_packages=['azureml-defaults'])
> inference_config = InferenceConfig(entry_script="score.py", environment=myenv)
> ```

환경에 대한 자세한 내용은 [학습 및 배포 환경 만들기 및 관리](how-to-use-environments.md)를 참조하세요.

추론 구성에 대한 자세한 내용은 [Azure Machine Learning을 사용하여 모델 배포](how-to-deploy-and-where.md)를 참조하세요.

> [!IMPORTANT]
> Azure App Service에 배포할 때는 __배포 구성__ 을 만들 필요가 없습니다.

## <a name="create-the-image"></a>이미지 만들기

Azure App Service에 배포되는 Docker 이미지를 만들려면 [Model.package](/python/api/azureml-core/azureml.core.model.model)를 사용합니다. 다음 코드 조각에서는 모델 및 추론 구성에서 새 이미지를 빌드하는 방법을 보여줍니다.

> [!NOTE]
> 이 코드 조각에서는 `model`에 등록된 모델이 포함되어 있고, `inference_config`에 추론 환경에 대한 구성이 포함되어 있다고 가정합니다. 자세한 내용은 [Azure Machine Learning을 사용하여 모델 배포](how-to-deploy-and-where.md)를 참조하세요.

```python
from azureml.core import Model

package = Model.package(ws, [model], inference_config)
package.wait_for_creation(show_output=True)
# Display the package location/ACR path
print(package.location)
```

`show_output=True`인 경우 Docker 빌드 프로세스의 출력이 표시됩니다. 프로세스가 완료되면 Azure Container Registry에 작업 영역에 대한 이미지가 생성됩니다. 이미지가 빌드되면 Azure Container Registry상의 위치가 표시됩니다. 반환되는 위치는 `<acrinstance>.azurecr.io/package@sha256:<imagename>` 형식입니다. 예들 들어 `myml08024f78fd10.azurecr.io/package@sha256:20190827151241`입니다.

> [!IMPORTANT]
> 이미지를 배포할 때 사용되는 위치 정보를 저장합니다.

## <a name="deploy-image-as-a-web-app"></a>이미지를 웹앱으로 배포

1. 다음 명령을 사용하여 이미지를 포함하는 Azure Container Registry에 대한 로그인 자격 증명을 가져옵니다. `<acrinstance>`을 `package.location`에서 이전에 반환된 값으로 바꿉니다.

    ```azurecli-interactive
    az acr credential show --name <myacr>
    ```

    이 명령의 출력은 다음 JSON 문서와 비슷합니다.

    ```json
    {
    "passwords": [
        {
        "name": "password",
        "value": "Iv0lRZQ9762LUJrFiffo3P4sWgk4q+nW"
        },
        {
        "name": "password2",
        "value": "=pKCxHatX96jeoYBWZLsPR6opszr==mg"
        }
    ],
    "username": "myml08024f78fd10"
    }
    ```

    __사용자 이름__ 및 __암호__ 중 하나의 값을 저장합니다.

1. 서비스를 배포하기 위한 리소스 그룹 또는 App Service 요금제가 아직 없는 경우, 다음 명령에서 두 가지 모두를 만드는 방법을 보여줍니다.

    ```azurecli-interactive
    az group create --name myresourcegroup --location "West Europe"
    az appservice plan create --name myplanname --resource-group myresourcegroup --sku B1 --is-linux
    ```

    이 예제에서는 __기본__ 가격 책정 계층(`--sku B1`)이 사용됩니다.

    > [!IMPORTANT]
    > Azure Machine Learning에서 만든 이미지는 Linux를 사용하므로 `--is-linux` 매개 변수를 사용해야 합니다.

1. 웹앱을 만들려면 다음 명령을 사용합니다. `<app-name>`을 사용할 이름으로 바꿉니다. `<acrinstance>` 및 `<imagename>`을 이전에 반환된 `package.location`의 값으로 바꿉니다.

    ```azurecli-interactive
    az webapp create --resource-group myresourcegroup --plan myplanname --name <app-name> --deployment-container-image-name <acrinstance>.azurecr.io/package@sha256:<imagename>
    ```

    이 명령은 다음 JSON 문서와 유사한 정보를 반환합니다.

    ```json
    {
    "adminSiteName": null,
    "appServicePlanName": "myplanname",
    "geoRegion": "West Europe",
    "hostingEnvironmentProfile": null,
    "id": "/subscriptions/0000-0000/resourceGroups/myResourceGroup/providers/Microsoft.Web/serverfarms/myplanname",
    "kind": "linux",
    "location": "West Europe",
    "maximumNumberOfWorkers": 1,
    "name": "myplanname",
    < JSON data removed for brevity. >
    "targetWorkerSizeId": 0,
    "type": "Microsoft.Web/serverfarms",
    "workerTierName": null
    }
    ```

    > [!IMPORTANT]
    > 이때 웹앱이 생성됩니다. 그러나 이미지를 포함하는 Azure Container Registry에 자격 증명을 제공하지 않았으므로 웹앱이 활성화되지 않습니다. 다음 단계에서는 Container Registry에 대한 인증 정보를 제공합니다.

1. Container Registry에 액세스하는 데 필요한 자격 증명을 웹앱에 제공하려면 다음 명령을 사용합니다. `<app-name>`을 사용할 이름으로 바꿉니다. `<acrinstance>` 및 `<imagename>`을 이전에 반환된 `package.location`의 값으로 바꿉니다. `<username>` 및 `<password>`를 이전에 검색한 ACR 로그인 정보로 바꿉니다.

    ```azurecli-interactive
    az webapp config container set --name <app-name> --resource-group myresourcegroup --docker-custom-image-name <acrinstance>.azurecr.io/package@sha256:<imagename> --docker-registry-server-url https://<acrinstance>.azurecr.io --docker-registry-server-user <username> --docker-registry-server-password <password>
    ```

    이 명령은 다음 JSON 문서와 유사한 정보를 반환합니다.

    ```json
    [
    {
        "name": "WEBSITES_ENABLE_APP_SERVICE_STORAGE",
        "slotSetting": false,
        "value": "false"
    },
    {
        "name": "DOCKER_REGISTRY_SERVER_URL",
        "slotSetting": false,
        "value": "https://myml08024f78fd10.azurecr.io"
    },
    {
        "name": "DOCKER_REGISTRY_SERVER_USERNAME",
        "slotSetting": false,
        "value": "myml08024f78fd10"
    },
    {
        "name": "DOCKER_REGISTRY_SERVER_PASSWORD",
        "slotSetting": false,
        "value": null
    },
    {
        "name": "DOCKER_CUSTOM_IMAGE_NAME",
        "value": "DOCKER|myml08024f78fd10.azurecr.io/package@sha256:20190827195524"
    }
    ]
    ```

이때 웹앱은 이미지를 로드하기 시작합니다.

> [!IMPORTANT]
> 이미지가 로드되기까지 몇 분 정도 걸릴 수 있습니다. 진행률을 모니터링하려면 다음 명령을 사용합니다.
>
> ```azurecli-interactive
> az webapp log tail --name <app-name> --resource-group myresourcegroup
> ```
>
> 이미지가 로드되고 사이트가 활성화되면 로그에 `Container <container name> for site <app-name> initialized successfully and is ready to serve requests`라는 메시지가 표시됩니다.

이미지가 배포되면 다음 명령을 사용하여 호스트 이름을 찾을 수 있습니다.

```azurecli-interactive
az webapp show --name <app-name> --resource-group myresourcegroup
```

이 명령은 호스트 이름 `<app-name>.azurewebsites.net`과 유사한 정보를 반환합니다. 이 값을 서비스에 대한 __기본 URL__ 의 일부로 사용합니다.

## <a name="use-the-web-app"></a>웹앱 사용

모델에 요청을 전달하는 웹 서비스는 `{baseurl}/score`에 있습니다. 예들 들어 `https://<app-name>.azurewebsites.net/score`입니다. 다음 Python 코드는 URL에 데이터를 제출하고 응답을 표시하는 방법을 보여줍니다.

```python
import requests
import json

scoring_uri = "https://mywebapp.azurewebsites.net/score"

headers = {'Content-Type':'application/json'}

test_sample = json.dumps({'data': [
    [1,2,3,4,5,6,7,8,9,10],
    [10,9,8,7,6,5,4,3,2,1]
]})

response = requests.post(scoring_uri, data=test_sample, headers=headers)
print(response.status_code)
print(response.elapsed)
print(response.json())
```

## <a name="next-steps"></a>다음 단계

* [App Service on Linux](/azure/app-service/containers/) 설명서에서 웹앱을 구성하는 방법 알아보기
* [Azure에서 자동 크기 조정 시작](../azure-monitor/autoscale/autoscale-get-started.md?toc=%2fazure%2fapp-service%2ftoc.json)에서 크기 조정에 대해 자세히 알아보기
* [Azure App Service에서 TLS/SSL 인증서 사용](../app-service/configure-ssl-certificate-in-code.md)
* [Azure Active Directory 로그인을 사용하도록 App Service 앱 구성](../app-service/configure-authentication-provider-aad.md)
* [웹 서비스로 배포된 ML 모델 사용](how-to-consume-web-service.md)