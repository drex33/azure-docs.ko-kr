---
title: 모델을 컴퓨팅 인스턴스에 배포
titleSuffix: Azure Machine Learning
description: 컴퓨팅 인스턴스를 사용하여 Azure Machine Learning 모델을 웹 서비스로 배포하는 방법을 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.custom: deploy
ms.reviewer: larryfr
ms.date: 04/22/2021
ms.openlocfilehash: cd848a6d07a21c965aa0de9be0206be86d1688a5
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128549819"
---
# <a name="deploy-a-model-locally"></a>모델을 로컬로 배포

Azure Machine Learning을 사용하여 모델을 Azure Machine Learning 컴퓨팅 인스턴스에 웹 서비스로 배포하는 방법에 대해 알아봅니다. 다음 조건 중 하나에 해당하는 경우 컴퓨팅 인스턴스를 사용합니다.

- 모델을 빠르게 배포하고 유효성을 검사해야 합니다.
- 개발 중인 모델을 테스트합니다.

> [!TIP]
> 컴퓨팅 인스턴스의 Jupyter Notebook에서 동일한 VM의 웹 서비스로 모델을 배포하는 것은 _로컬 배포_ 입니다. 이 경우 '로컬' 컴퓨터는 컴퓨팅 인스턴스입니다. 배포에 대한 자세한 내용은 [Azure Machine Learning을 사용한 모델 배포](how-to-deploy-and-where.md)를 참조하세요.

[!INCLUDE [endpoints-option](../../includes/machine-learning-endpoints-preview-note.md)]

## <a name="prerequisites"></a>사전 요구 사항

- 컴퓨팅 인스턴스가 실행되는 Azure Machine Learning 작업 영역. 자세한 내용은 [빠른 시작: Azure Machine Learning 시작](quickstart-create-resources.md)을 참조하세요.

## <a name="deploy-to-the-compute-instances"></a>컴퓨팅 인스턴스에 배포

로컬 배포를 보여주는 예제 노트북은 컴퓨팅 인스턴스에 포함됩니다. 다음 단계를 사용하여 노트북을 로드하고 모델을 VM에 웹 서비스로 배포합니다.

1. [Azure Machine Learning studio](https://ml.azure.com)에서 "Notebooks"을 선택한 다음 "Sample notebooks"의 how-to-use-azureml/deployment/deploy-to-local/register-model-deploy-local.ipynb를 선택합니다. 이 노트북을 사용자 폴더에 복제합니다.

1. 1단계에서 복제된 노트북을 찾고, 노트북을 실행할 컴퓨팅 인스턴스를 선택하거나 만듭니다.

    ![노트북에서 실행 중인 로컬 서비스의 스크린샷](./media/how-to-deploy-local-container-notebook-vm/deploy-local-service.png)


1. 노트북에는 서비스가 실행되는 URL 및 포트를 표시합니다. 예들 들어 `https://localhost:6789`입니다. `print('Local service port: {}'.format(local_service.port))`이 포함된 셀을 실행하여 포트를 표시할 수도 있습니다.

    ![실행 중인 로컬 서비스 포트의 스크린샷](./media/how-to-deploy-local-container-notebook-vm/deploy-local-service-port.png)

1. 컴퓨팅 인스턴스에서 서비스를 테스트 하려면 `https://localhost:<local_service.port>`URL을 사용합니다. 원격 클라이언트에서 테스트하려면 컴퓨팅 인스턴스에서 실행되는 서비스의 공용 URL을 가져옵니다. 공용 URL은 다음 수식을 사용하여 확인할 수 있습니다. 
    * Notebook VM: `https://<vm_name>-<local_service_port>.<azure_region_of_workspace>.notebooks.azureml.net/score`. 
    * 컴퓨팅 인스턴스: `https://<vm_name>-<local_service_port>.<azure_region_of_workspace>.instances.azureml.net/score`. 

    예제: 
    * Notebook VM: `https://vm-name-6789.northcentralus.notebooks.azureml.net/score` 
    * 컴퓨팅 인스턴스: `https://vm-name-6789.northcentralus.instances.azureml.net/score`

## <a name="test-the-service"></a>서비스 테스트

실행 중인 서비스에 샘플 데이터를 제출하려면, 다음 코드를 사용합니다. `service_url` 값을 이전 단계의 URL로 바꿉니다.

> [!NOTE]
> 컴퓨팅 인스턴스에서 배포를 인증하는 경우 Azure Active Directory를 사용하여 인증을 수행합니다. 예제 코드에서 `interactive_auth.get_authentication_header()`를 호출하면 AAD를 사용하여 인증하고, 컴퓨팅 인스턴스에서 서비스를 인증하는 데 사용할 수 있는 헤더를 반환합니다. 자세한 내용은 [Azure Machine Learning 리소스 및 워크플로에 대한 인증 설정](how-to-setup-authentication.md#interactive-authentication)을 참조하세요.
>
> Azure Kubernetes Service 또는 Azure Container 인스턴스에서 배포를 인증하는 경우 다른 인증 방법이 사용됩니다. 자세한 내용은 [웹 서비스로 배포된 Azure 컴퓨터 모델에 대한 인증 구성](how-to-authenticate-web-service.md)을 참조하세요.

```python
import requests
import json
from azureml.core.authentication import InteractiveLoginAuthentication

# Get a token to authenticate to the compute instance from remote
interactive_auth = InteractiveLoginAuthentication()
auth_header = interactive_auth.get_authentication_header()

# Create and submit a request using the auth header
headers = auth_header
# Add content type header
headers.update({'Content-Type':'application/json'})

# Sample data to send to the service
test_sample = json.dumps({'data': [
    [1,2,3,4,5,6,7,8,9,10],
    [10,9,8,7,6,5,4,3,2,1]
]})
test_sample = bytes(test_sample,encoding = 'utf8')

# Replace with the URL for your compute instance, as determined from the previous section
service_url = "https://vm-name-6789.northcentralus.notebooks.azureml.net/score"
# for a compute instance, the url would be https://vm-name-6789.northcentralus.instances.azureml.net/score
resp = requests.post(service_url, test_sample, headers=headers)
print("prediction:", resp.text)
```

## <a name="next-steps"></a>다음 단계

* [사용자 지정 Docker 이미지를 사용하여 모델을 배포하는 방법](./how-to-deploy-custom-container.md)
* [배포 문제 해결](how-to-troubleshoot-deployment.md)
* [TLS를 사용하여 Azure Machine Learning을 통해 웹 서비스 보호](how-to-secure-web-service.md)
* [웹 서비스로 배포된 ML 모델 사용](how-to-consume-web-service.md)
* [Application Insights를 사용하여 Azure Machine Learning 모델 모니터링](how-to-enable-app-insights.md)
* [프로덕션 환경에서 모델용 데이터 수집](how-to-enable-data-collection.md)