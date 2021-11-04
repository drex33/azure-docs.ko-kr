---
title: 암호화된 추론 서비스 배포(미리 보기)
titleSuffix: Azure Machine Learning
description: Microsoft SEAL을 사용하여 이미지 분류를 위한 암호화된 예측 서비스를 배포하는 방법 알아보기
author: luisquintanilla
ms.author: luquinta
ms.date: 10/21/2021
services: machine-learning
ms.service: machine-learning
ms.subservice: enterprise-readiness
ms.topic: how-to
ms.custom: devx-track-python, deploy, responsible-ml
ms.openlocfilehash: 56718c33aee4d285e5dfcddef259e669ba59ae0d
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131553364"
---
# <a name="how-to-deploy-an-encrypted-inferencing-web-service-preview"></a>암호화된 추론 웹 서비스 배포 방법(미리 보기)

[ACI(Azure Container Instance)](../container-instances/index.yml)에서 암호화된 추론 서비스로 이미지 분류 모델을 배포하는 방법을 알아봅니다. 웹 서비스는 모델 및 채점 로직을 포함하는 Docker 컨테이너 이미지입니다.

이 가이드에서는 Azure Machine Learning Service를 사용하여 다음을 수행합니다.

> [!div class="checklist"]
> * 환경 구성
> * 암호화된 추론 서비스 웹 배포
> * 테스트 데이터 준비
> * 암호화된 예측 만들기
> * 리소스 정리

ACI는 모델 배포 워크플로를 테스트 및 이해하기 위한 훌륭한 솔루션입니다. 확장성 있는 프로덕션 배포에는 Azure Kubernetes Service를 사용하는 것이 좋습니다. 자세한 내용은 [배포 방법 및 위치](./how-to-deploy-and-where.md)를 참조하세요.

이 샘플에서 사용되는 암호화 방법은 [동형 암호화](https://github.com/Microsoft/SEAL#homomorphic-encryption)입니다. 동형 암호화에서는 비밀(암호 해독) 키 액세스 없이도 암호화된 데이터에 대해 계산을 수행할 수 있습니다. 계산 결과는 암호화되며 비밀 키의 소유자만 볼 수 있습니다. 

## <a name="prerequisites"></a>사전 요구 사항

이 가이드에서는 Azure Machine Learning에 이미지 분류 모델이 등록되어 있다고 가정합니다. 그렇지 않은 경우 [미리 학습된 모델](https://github.com/Azure/MachineLearningNotebooks/raw/master/tutorials/image-classification-mnist-data/sklearn_mnist_model.pkl)을 사용하는 모델을 등록하거나, [Azure Machine Learning 자습서](tutorial-train-models-with-aml.md)를 사용하여 이미지 분류 모델 학습을 완료하여 직접 만듭니다.

## <a name="configure-local-environment"></a>로컬 환경 구성

Jupyter Notebook에서

1. 이 샘플에 필요한 Python 패키지를 가져옵니다.

    ```python
    %matplotlib inline
    import numpy as np
    import matplotlib.pyplot as plt

    import azureml.core

    # display the core SDK version number
    print("Azure ML SDK Version: ", azureml.core.VERSION)
    ```

2. 안전한 추론을 위해 동형 암호화 라이브러리를 설치합니다.

    > [!NOTE]
    > `encrypted-inference` 패키지는 현재 미리 보기로 제공됩니다.

    [`encrypted-inference`](https://pypi.org/project/encrypted-inference)는 [Microsoft SEAL](https://github.com/Microsoft/SEAL)을 기반으로 암호화된 추론에 대한 바인딩을 포함하는 라이브러리입니다.

    ```python
    !pip install encrypted-inference==0.9
    ```

## <a name="configure-the-inferencing-environment"></a>추론 환경 구성

추론에 대한 환경을 만들고 `encrypted-inference` 패키지를 conda 종속성으로 추가합니다.

```python
from azureml.core.environment import Environment
from azureml.core.conda_dependencies import CondaDependencies

# to install required packages
env = Environment('tutorial-env')
cd = CondaDependencies.create(pip_packages=['azureml-dataprep[pandas,fuse]>=1.1.14', 'azureml-defaults', 'azure-storage-blob', 'encrypted-inference==0.9'], conda_packages = ['scikit-learn==0.22.1'])

env.python.conda_dependencies = cd

# Register environment to re-use later
env.register(workspace = ws)
```

## <a name="deploy-encrypted-inferencing-web-service"></a>암호화된 추론 서비스 웹 배포

모델을 ACI에서 호스팅되는 웹 서비스로 배포합니다.

올바른 ACI용 환경을 빌드하기 위해서 다음을 제공합니다.

* 모델 사용 방법을 보여 주는 채점 스크립트
* ACI를 빌드하기 위한 구성 파일
* 학습된 모델

### <a name="create-scoring-script"></a>채점 스크립트 만들기

추론을 위해 웹 서비스에서 사용하는 채점 스크립트 `score.py`를 만듭니다.

다음 두 개의 필수 함수를 채점 스크립트에 포함해야 합니다.

* `init()` 함수는 일반적으로 모델을 전역 개체에 로드합니다. 이 함수는 Docker 컨테이너를 시작할 때 한 번만 실행됩니다.
* `run(input_data)` 함수는 모델을 사용하여 입력 데이터를 기반으로 값을 예측합니다. 실행에 대한 입력 및 출력은 일반적으로 serialization 및 deserialization용으로 JSON을 사용하지만 다른 형식도 지원됩니다. 함수는 서비스 호출자가 업로드한 동형 암호화 기반 공개 키를 가져옵니다.

```python
%%writefile score.py
import json
import os
import pickle
import joblib
from azure.storage.blob import BlobServiceClient, BlobClient, ContainerClient, PublicAccess
from encrypted.inference.eiserver import EIServer

def init():
    global model
    # AZUREML_MODEL_DIR is an environment variable created during deployment.
    # It is the path to the model folder (./azureml-models/$MODEL_NAME/$VERSION)
    # For multiple models, it points to the folder containing all deployed models (./azureml-models)
    model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), 'sklearn_mnist_model.pkl')
    model = joblib.load(model_path)

    global server
    server = EIServer(model.coef_, model.intercept_, verbose=True)

def run(raw_data):

    json_properties = json.loads(raw_data)

    key_id = json_properties['key_id']
    conn_str = json_properties['conn_str']
    container = json_properties['container']
    data = json_properties['data']

    # download the public keys from blob storage
    blob_service_client = BlobServiceClient.from_connection_string(conn_str=conn_str)
    blob_client = blob_service_client.get_blob_client(container=container, blob=key_id)
    public_keys = blob_client.download_blob().readall()

    result = {}
    # make prediction
    result = server.predict(data, public_keys)

    # you can return any data type as long as it is JSON-serializable
    return result
```

### <a name="create-configuration-file"></a>구성 파일 만들기

배포 구성 파일을 만들고 ACI 컨테이너에 필요한 RAM 기가바이트 및 CPU 개수를 지정합니다. 모델에 따라 다르긴 하지만 대개 다수의 모델에서 기본적으로 1기가바이트의 RAM 및 1개 코어면 충분합니다. 나중에 더 필요할 것 같면, 이미지를 다시 만들고 서비스를 다시 배포해야 합니다.

```python
from azureml.core.webservice import AciWebservice

aciconfig = AciWebservice.deploy_configuration(cpu_cores=1, 
                                               memory_gb=1, 
                                               tags={"data": "MNIST",  "method" : "sklearn"}, 
                                               description='Encrypted Predict MNIST with sklearn + SEAL')
```

### <a name="deploy-to-azure-container-instances"></a>Azure Container Instances에 배포

예상 완료 시간: **약 2~5분**

이미지를 구성하고 배포합니다. 다음 코드는 다음과 같은 단계를 거칩니다.

1. 환경 파일(`myenv.yml`)을 사용하여 모델에 필요한 종속성이 포함된 환경 개체를 만듭니다.
1. 다음을 사용하여 모델을 웹 서비스로 배포하는 데 필요한 유추 구성을 만듭니다.
   * 채점 파일(`score.py`)
   * 이전 단계에서 만든 환경 개체
1. 모델을 ACI 컨테이너에 배포합니다.
1. 웹 서비스 HTTP 엔드포인트를 가져옵니다.

```python
%%time
from azureml.core.webservice import Webservice
from azureml.core.model import InferenceConfig
from azureml.core.environment import Environment
from azureml.core import Workspace
from azureml.core.model import Model

ws = Workspace.from_config()
model = Model(ws, 'sklearn_mnist')

myenv = Environment.get(workspace=ws, name="tutorial-env")
inference_config = InferenceConfig(entry_script="score.py", environment=myenv)

service = Model.deploy(workspace=ws,
                       name='sklearn-encrypted-mnist-svc',
                       models=[model],
                       inference_config=inference_config,
                       deployment_config=aciconfig)

service.wait_for_deployment(show_output=True)
```

REST 클라이언트 호출을 수락하는 채점 웹 서비스의 HTTP 엔드포인트를 가져옵니다. 이 엔드포인트는 웹 서비스를 테스트하거나 애플리케이션에 통합하려는 모든 사용자와 공유할 수 있습니다.

```python
print(service.scoring_uri)
```

## <a name="prepare-test-data"></a>테스트 데이터 준비

1. 테스트 데이터를 다운로드합니다. 이 경우 *data* 라는 디렉터리에 저장됩니다.

    ```python
    import os
    from azureml.core import Dataset
    from azureml.opendatasets import MNIST
    
    data_folder = os.path.join(os.getcwd(), 'data')
    os.makedirs(data_folder, exist_ok=True)
    
    mnist_file_dataset = MNIST.get_file_dataset()
    mnist_file_dataset.download(data_folder, overwrite=True)
    ```

1. 테스트 데이터를 *data* 디렉터리에 다운로드합니다.

    ```python
    from utils import load_data
    import os
    import glob
    
    data_folder = os.path.join(os.getcwd(), 'data')
    # note we also shrink the intensity values (X) from 0-255 to 0-1. This helps the neural network converge faster
    X_test = load_data(glob.glob(os.path.join(data_folder,"**/t10k-images-idx3-ubyte.gz"), recursive=True)[0], False) / 255.0
    y_test = load_data(glob.glob(os.path.join(data_folder,"**/t10k-labels-idx1-ubyte.gz"), recursive=True)[0], True).reshape(-1)
    ```

## <a name="make-encrypted-predictions"></a>암호화된 예측 만들기

모델에 테스트 데이터 세트를 사용하여 예측을 가져옵니다.

암호화된 예측을 만들려면 다음을 수행합니다.

1. 새 `EILinearRegressionClient`, 동형 암호화 기반 클라이언트, 공개 키를 만듭니다.

    ```python
    from encrypted.inference.eiclient import EILinearRegressionClient

    # Create a new Encrypted inference client and a new secret key.
    edp = EILinearRegressionClient(verbose=True)

    public_keys_blob, public_keys_data = edp.get_public_keys()
    ```

1. 동형 암호화 생성 공개 키를 작업 영역 기본 Blob 저장소에 업로드합니다. 이렇게 하면 유추 서버와 키를 공유할 수 있습니다.

    ```python
    import azureml.core
    from azureml.core import Workspace, Datastore
    import os

    ws = Workspace.from_config()

    datastore = ws.get_default_datastore()
    container_name=datastore.container_name

    # Create a local file and write the keys to it
    public_keys = open(public_keys_blob, "wb")
    public_keys.write(public_keys_data)
    public_keys.close()

    # Upload the file to blob store
    datastore.upload_files([public_keys_blob])

    # Delete the local file
    os.remove(public_keys_blob)
    ```

1. 테스트 데이터 암호화

    ```python
    #choose any one sample from the test data 
    sample_index = 1

    #encrypt the data
    raw_data = edp.encrypt(X_test[sample_index])

    ```

1. SDK의 `run` API를 사용하여 서비스를 호출하고 테스트 데이터 세트를 모델에 제공하여 예측을 가져옵니다. 공개 키가 업로드된 Blob 스토리지에 연결 문자열을 보내야 합니다.

    ```python
    import json
    from azureml.core import Webservice

    service = Webservice(ws, 'sklearn-encrypted-mnist-svc')

    #pass the connection string for blob storage to give the server access to the uploaded public keys 
    conn_str_template = 'DefaultEndpointsProtocol={};AccountName={};AccountKey={};EndpointSuffix=core.windows.net'
    conn_str = conn_str_template.format(datastore.protocol, datastore.account_name, datastore.account_key)

    #build the json 
    data = json.dumps({"data": raw_data, "key_id" : public_keys_blob, "conn_str" : conn_str, "container" : container_name })
    data = bytes(data, encoding='ASCII')

    print ('Making an encrypted inference web service call ')
    eresult = service.run(input_data=data)

    print ('Received encrypted inference results')
    ```

1. 클라이언트를 사용하여 결과의 암호를 해독합니다.

    ```python
    import numpy as np

    results = edp.decrypt(eresult)

    print ('Decrypted the results ', results)

    #Apply argmax to identify the prediction result
    prediction = np.argmax(results)

    print ( ' Prediction : ', prediction)
    print ( ' Actual Label : ', y_test[sample_index])
    ```

## <a name="clean-up-resources"></a>리소스 정리

이 샘플에서 만든 웹 서비스를 삭제합니다.

```python
service.delete()
```

만든 Azure 리소스를 더 이상 사용하지 않으려는 경우 삭제합니다. 이렇게 하면 사용하지 않은 리소스가 계속 실행되어 발생하는 요금을 방지할 수 있습니다. 자세한 정보는 [리소스 정리](how-to-manage-workspace.md#clean-up-resources) 방법에 대한 가이드를 참조하세요.