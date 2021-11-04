---
title: 관리형 온라인 엔드포인트로 사용자 지정 컨테이너 배포
titleSuffix: Azure Machine Learning
description: 사용자 지정 컨테이너를 사용하여 Azure Machine Learning 오픈 소스 서버를 사용하는 방법을 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: mlops
ms.author: ssambare
author: shivanissambare
ms.reviewer: larryfr
ms.date: 10/21/2021
ms.topic: how-to
ms.custom: deploy, devplatv2
ms.openlocfilehash: 9b36a5799444b6da011693693b58f6574b135799
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131553636"
---
# <a name="deploy-a-tensorflow-model-served-with-tf-serving-using-a-custom-container-in-a-managed-online-endpoint-preview"></a>관리형 온라인 엔드포인트(미리 보기)에서 사용자 지정 컨테이너를 사용하여 TF 서비스 제공에 제공되는 TensorFlow 모델 배포

Azure Machine Learning에서 관리형 온라인 엔드포인트로 사용자 지정 컨테이너를 배포하는 방법을 알아봅니다.

사용자 지정 컨테이너 배포는 Azure Machine Learning에서 사용되는 기본 Python Flask 서버 이외의 웹 서버를 사용할 수 있습니다. 이러한 배포의 사용자는 Azure Machine Learning의 기본 제공 모니터링, 크기 조정, 경고 및 인증을 계속 활용할 수 있습니다.

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

> [!WARNING]
> Microsoft는 사용자 지정 이미지로 인해 발생하는 문제를 해결하는 데 도움을 주지 못할 수 없습니다. 문제가 발생하는 경우 기본 이미지 또는 Microsoft에서 제공하는 이미지 중 하나를 사용하여 문제가 이미지와 관련된 것인지 확인하라는 메시지가 표시될 수 있습니다.

## <a name="prerequisites"></a>필수 구성 요소

* Azure CLI 및 ML 확장을 설치하고 구성합니다. 자세한 내용은 [CLI(v2)(미리 보기) 설치, 설정 및 사용](how-to-configure-cli.md)을 참조하세요. 

* Azure 리소스 그룹이 있고, 사용자(또는 사용하는 서비스 주체)에게 이에 대한 `Contributor` 액세스 권한이 있어야 합니다. 위의 문서에 따라 ML 확장을 구성한 경우 이러한 리소스 그룹이 있을 것입니다. 

* Azure Machine Learning 작업 영역이 있어야 합니다. 위의 문서에 따라 ML 확장을 구성한 경우 이러한 작업 영역이 있을 것입니다.

* Azure CLI의 기본값을 아직 설정하지 않은 경우 기본 설정을 저장해야 합니다. 값을 반복해서 전달하지 않으려면 다음을 실행합니다.

   ```azurecli
   az account set --subscription <subscription id>
   az configure --defaults workspace=<azureml workspace name> group=<resource group>

* To deploy locally, you must have [Docker engine](https://docs.docker.com/engine/install/) running locally. This step is **highly recommended**. It will help you debug issues.

## Download source code

To follow along with this tutorial, download the source code below.

```azurecli
git clone https://github.com/Azure/azureml-examples --depth 1
cd azureml-examples/cli
```

## <a name="initialize-environment-variables"></a>환경 변수 초기화

환경 변수 정의:

:::code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-tfserving.sh" id="initialize_variables":::

## <a name="download-a-tensorflow-model"></a>TensorFlow 모델 다운로드

입력을 2로 나누고 결과에 2를 추가하는 모델을 다운로드하고 압축을 풉니다.

:::code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-tfserving.sh" id="download_and_unzip_model":::

## <a name="run-a-tf-serving-image-locally-to-test-that-it-works"></a>TF 서비스 제공 이미지를 로컬로 실행하여 작동하는지 테스트

docker를 사용하여 테스트를 위해 이미지를 로컬로 실행합니다.

:::code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-tfserving.sh" id="run_image_locally_for_testing":::

### <a name="check-that-you-can-send-liveness-and-scoring-requests-to-the-image"></a>이미지에 라이브 및 점수 매기기 요청을 보낼 수 있는지 확인

먼저 컨테이너가 "활성"인지 확인합니다. 즉, 컨테이너 내부의 프로세스가 계속 실행되고 있습니다. 200(정상) 응답이 표시됩니다.

:::code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-tfserving.sh" id="check_liveness_locally":::

그런 다음, 레이블이 지정되지 않은 데이터에 대한 예측을 얻을 수 있는지 확인합니다.

:::code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-tfserving.sh" id="check_scoring_locally":::

### <a name="stop-the-image"></a>이미지 중지

이제 로컬에서 테스트했으므로 이미지를 중지합니다.

:::code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-tfserving.sh" id="stop_image":::

## <a name="create-a-yaml-file-for-your-endpoint-and-deployment"></a>엔드포인트 및 배포에 대한 YAML 파일 만들기

YAML을 사용하여 클라우드 배포를 구성할 수 있습니다. 이 예제에 대한 샘플 YAML을 살펴보세요.

__tfserving-endpoint.yml__

:::code language="yaml" source="~/azureml-examples-cli-preview/cli/endpoints/online/custom-container/tfserving-endpoint.yml":::

__tfserving-deployment.yml__

:::code language="yaml" source="~/azureml-examples-cli-preview/cli/endpoints/online/custom-container/tfserving-deployment.yml":::

이 YAML에는 몇 가지 중요한 개념이 있습니다.

### <a name="readiness-route-vs-liveness-route"></a>준비 경로와 활동성 경로

HTTP 서버는 _사용 및_ 준비 상태 모두에 대한 경로를 _정의합니다._ 활동성 경로는 서버가 실행 중인지 여부를 확인하는 데 사용됩니다. 준비 경로는 서버가 작업을 수행할 준비가 되었는지 여부를 확인하는 데 사용됩니다. 기계 학습 유추에서 서버는 모델을 로드하기 전에 활동성 요청에 200 정상으로 응답할 수 있습니다. 서버는 모델이 메모리에 로드된 후에만 준비 요청에 200 정상으로 응답할 수 있습니다.

활동성 및 준비 상태 프로브에 대한 자세한 내용은 [Kubernetes 설명서](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-startup-probes/)를 검토하세요.

TF 서비스 제공은 활동성 경로만 정의하므로 이 배포는 활동성 및 준비 모두에 동일한 경로를 사용합니다.

### <a name="locating-the-mounted-model"></a>탑재된 모델 찾기

실시간 엔드포인트로 모델을 배포하는 경우 Azure Machine Learning은 모델을 엔드포인트로 _탑재_ 합니다. 모델 탑재를 사용하면 새 Docker 이미지를 만들지 않고도 새 버전의 모델을 배포할 수 있습니다. 기본적으로 *foo* 및 버전 *1* 이라는 이름으로 등록된 모델은 배포된 컨테이너 내의 다음 경로에 있습니다. `/var/azureml-app/azureml-models/foo/1`

예를 들어 로컬 컴퓨터에 의 디렉터리 구조가 있는 경우 `/azureml-examples/cli/endpoints/online/custom-container` 모델의 이름은 입니다. `half_plus_two`

:::image type="content" source="./media/how-to-deploy-custom-container/local-directory-structure.png" alt-text="로컬 디렉터리 구조의 트리 뷰를 보여 주는 다이어그램":::

및 `tfserving-deployment.yml`은 다음을 포함합니다.

```yaml
model:
    name: tfserving-mounted
    version: 1
    local_path: ./half_plus_two
```

그러면 배포의 아래에 모델이 배치됩니다. `/var/azureml-app/azureml-models/tfserving-deployment/1`

:::image type="content" source="./media/how-to-deploy-custom-container/deployment-location.png" alt-text="배포 디렉터리 구조의 트리 뷰를 보여 주는 다이어그램":::

필요에 따라 를 구성할 수 `model_mount_path` 있습니다. 모델을 탑재하는 경로를 변경할 수 있습니다. 예를 들어 `model_mount_path` _tfserving-deployment.yml_ 에 매개 변수가 있을 수 있습니다.

> [!IMPORTANT]
> 은 `model_mount_path` Linux(컨테이너 이미지의 OS)에서 유효한 절대 경로여야 합니다.

```YAML
name: tfserving-deployment
endpoint_name: tfserving-endpoint
model:
  name: tfserving-mounted
  version: 1
  local_path: ./half_plus_two
model_mount_path: /var/tfserving-model-mount
.....
```

그러면 모델이 `/var/tfserving-model-mount/tfserving-deployment/1` 배포의 에 배치됩니다. 더 이상 가 `azureml-app/azureml-models` 아니라 지정한 탑재 경로 아래에 있습니다.

:::image type="content" source="./media/how-to-deploy-custom-container/mount-path-deployment-location.png" alt-text="mount_model_path 사용하는 경우 배포 디렉터리 구조의 트리 뷰를 보여 주는 다이어그램":::

### <a name="create-your-endpoint-and-deployment"></a>엔드포인트 및 배포 만들기

이제 YAML이 어떻게 생성되었는지 이해했으므로 엔드포인트를 만듭니다.

```azurecli
az ml online-endpoint create --name tfserving-endpoint -f endpoints/online/custom-container/tfserving-endpoint.yml
```

배포를 만드는 데 몇 분 정도 걸릴 수 있습니다.



```azurecli
az ml online-deployment create --name tfserving-deployment -f endpoints/online/custom-container/tfserving-deployment.yml
```

### <a name="invoke-the-endpoint"></a>엔드포인트 호출

배포가 완료되면 배포된 엔드포인트에 점수 매기기 요청을 할 수 있는지 확인합니다.

:::code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-tfserving.sh" id="invoke_endpoint":::

### <a name="delete-endpoint-and-model"></a>엔드포인트 및 모델 삭제

이제 엔드포인트를 통해 점수를 성공적으로 매겼기 때문에 삭제할 수 있습니다.

```azurecli
az ml online-endpoint delete --name tfserving-endpoint
```

```azurecli
az ml model delete -n tfserving-mounted --version 1
```

## <a name="next-steps"></a>다음 단계

- [온라인 엔드포인트에 대한 안전한 롤아웃(미리 보기)](how-to-safely-rollout-managed-endpoints.md)
- [관리형 온라인 엔드포인트 배포 문제 해결](./how-to-troubleshoot-online-endpoints.md)
- [Torch 제공 샘플](https://github.com/Azure/azureml-examples/blob/main/cli/deploy-torchserve.sh)