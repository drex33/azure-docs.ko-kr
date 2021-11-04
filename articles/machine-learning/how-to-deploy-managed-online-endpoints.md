---
title: 온라인 엔드포인트를 사용하여 ML 모델 배포(미리 보기)
titleSuffix: Azure Machine Learning
description: Azure에 있는 웹 서비스로 기계 학습 모델을 배포하는 방법을 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: mlops
ms.author: seramasu
ms.reviewer: laobri
author: rsethur
ms.date: 10/21/2021
ms.topic: how-to
ms.custom: how-to, devplatv2, ignite-fall-2021
ms.openlocfilehash: dba2e849fb28dfb0f6667b496c65bdef8cbdf046
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131557550"
---
# <a name="deploy-and-score-a-machine-learning-model-by-using-an-online-endpoint-preview"></a>온라인 엔드포인트를 사용하여 기계 학습 모델 배포 및 점수 매기기(미리 보기)

기본 인프라를 만들고 관리할 필요가 없도록 온라인 엔드포인트(미리 보기)를 사용하여 모델을 배포하는 방법을 알아봅니다. 먼저 로컬 머신에 모델을 배포하여 오류를 디버그한 다음, Azure에서 배포하고 테스트합니다.

로그를 보고 SLA(서비스 수준 약정)를 모니터링하는 방법도 알아봅니다. 모델로 시작하여 온라인/실시간 채점에 사용할 수 있는 확장 가능한 HTTPS/REST 엔드포인트로 끝납니다. 

자세한 내용은 [Azure Machine Learning 엔드포인트(미리 보기)란?](concept-endpoints.md)을 참조하세요.

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

## <a name="prerequisites"></a>필수 구성 요소

* Azure Machine Learning을 사용하려면 Azure 구독이 있어야 합니다. Azure 구독이 없는 경우 시작하기 전에 체험 계정을 만듭니다. [Azure Machine Learning 평가판 또는 유료 버전](https://azure.microsoft.com/free/)을 사용해 보세요.

* Azure CLI 및 Azure CLI에 대한 `ml` 확장을 설치하고 구성합니다. 자세한 내용은 [CLI(v2)(미리 보기) 설치, 설정 및 사용](how-to-configure-cli.md)을 참조하세요. 

* Azure 리소스 그룹이 있어야 하며 사용자(또는 사용하는 서비스 주체)는 이에 대한 기여자 액세스 권한이 있어야 합니다. 리소스 그룹은 [CLI(v2) 설치, 설정 및 사용(미리 보기)](how-to-configure-cli.md)에서 만들어집니다. 

* Azure Machine Learning 작업 영역이 있어야 합니다. 작업 영역은 [CLI(v2) 설치, 설정 및 사용(미리 보기)](how-to-configure-cli.md)에서 만들어집니다.

* Azure CLI에 대한 기본값을 아직 설정하지 않은 경우 기본 설정을 저장합니다. 구독, 작업 영역 및 리소스 그룹에 대한 값을 여러 번 전달하지 않으려면 다음 코드를 실행하세요.

   ```azurecli
   az account set --subscription <subscription ID>
   az configure --defaults workspace=<Azure Machine Learning workspace name> group=<resource group>
   ```

* (선택 사항) 로컬로 배포하려면 로컬 컴퓨터에 [Docker 엔진을 설치](https://docs.docker.com/engine/install/)해야 합니다. 문제를 더 쉽게 디버그할 수 있도록 이 옵션을 *권장합니다*.

> [!IMPORTANT]
> 이 문서의 예제에서는 Bash 셸을 사용한다고 가정합니다. 예를 들어 Linux 시스템 또는 [Linux용 Windows 하위 시스템.](/windows/wsl/about) 

## <a name="prepare-your-system"></a>시스템 준비

이 문서를 따르려면 먼저 샘플 리포지토리(azureml-examples)를 복제합니다. 그런 후 다음 코드를 실행하여 샘플 디렉터리로 이동합니다.

```azurecli
git clone https://github.com/Azure/azureml-examples
cd azureml-examples
cd cli
```

엔드포인트 이름을 설정하려면 운영 체제에 따라 다음 명령 중 하나를 선택합니다(`YOUR_ENDPOINT_NAME`을 고유한 이름으로 대체).

Unix의 경우 다음 명령을 실행합니다.

:::code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-local-endpoint.sh" ID="set_endpoint_name":::

> [!NOTE]
> 최근에 CLI 인터페이스를 변경했습니다. 이전에는 및 를 둘 다 `endpoint` `deployment` `az ml endpoint` 사용했으며, 이제 및 로 `az ml online-endpoint` `az ml online-deployment` 구분했습니다.  이렇게 하면 CI/CD 스크립트에서 엔드포인트를 더 쉽게 사용할 수 있습니다.

> [!NOTE]
> 엔드포인트 이름은 Azure 지역 내에서 고유해야 합니다. 예를 들어 Azure `westus2` 지역에는 이름이 인 엔드포인트가 하나만 있을 수 `my-endpoint` 있습니다. 

## <a name="review-the-endpoint-and-deployment-configurations"></a>엔드포인트 및 배포 구성 검토

다음 조각은 *endpoints/online/managed/sample/endpoint.yml* 파일을 보여줍니다. 

:::code language="yaml" source="~/azureml-examples-cli-preview/cli/endpoints/online/managed/sample/endpoint.yml":::

> [!NOTE]
> YAML에 대한 전체 설명은 [관리형 온라인 엔드포인트(미리 보기) YAML 참조](reference-yaml-endpoint-managed-online.md)를 참조하세요.

엔드포인트 YAML 형식에 대한 참조는 다음 표에 설명되어 있습니다. 이러한 특성을 지정하는 방법을 알아보려면 [시스템 준비](#prepare-your-system)의 YAML 예 또는 [온라인 엔드포인트 YAML 참조](reference-yaml-endpoint-managed-online.md)를 참조하세요. 관리형 엔드포인트와 관련된 제한에 대한 자세한 내용은 [Azure Machine Learning을 사용하여 리소스의 할당량 관리 및 증대](how-to-manage-quotas.md#azure-machine-learning-managed-online-endpoints-preview)를 참조하세요.

| 키 | 설명 |
| --- | --- |
| `$schema`    | (선택 사항) YAML 스키마입니다. YAML 파일에서 사용 가능한 모든 옵션을 보려면 브라우저에서 이전 예제의 스키마를 볼 수 있습니다.|
| `name`       | 엔드포인트의 이름입니다. Azure 지역에서 고유해야 합니다.|
| `traffic` | 엔드포인트에서 각 배포로 전환되는 트래픽의 비율입니다. 트래픽 값의 합은 100이어야 합니다. |
| `auth_mode` | `key`키 기반 인증에 사용합니다. `aml_token`Azure Machine Learning 토큰 기반 인증에 사용합니다. `key`는 만료되지 않지만 `aml_token`은 만료됩니다. (`az ml online-endpoint get-credentials` 명령을 사용하여 가장 최근 토큰을 가져옵니다.) |

이 예제에는 온라인 엔드포인트에 모델을 배포하는 데 필요한 모든 파일이 포함되어 있습니다. 모델을 배포하려면 다음이 있어야 합니다.

- 모델 파일(또는 작업 영역에 이미 등록된 모델의 이름 및 버전). 이 예에는 회귀를 수행하는 scikit-learn 모델이 있습니다.
- 모델을 채점하는 데 필요한 코드입니다. 이 경우에는 *score.py* 파일이 있습니다.
- 모델이 실행되는 환경입니다. 보시다시피 환경은 Conda 종속성이 있는 Docker 이미지이거나 Dockerfile일 수 있습니다.
- 인스턴스 유형 및 크기 조정 용량을 지정하는 설정.

다음 조각은 필요한 모든 *입력이 있는 엔드포인트/online/managed/sample/blue-deployment.yml* 파일을 보여줍니다. 

:::code language="yaml" source="~/azureml-examples-cli-preview/cli/endpoints/online/managed/sample/blue-deployment.yml":::

다음 표에서는 의 특성을 `deployment` 설명합니다.

| 키 | 설명 |
| --- | --- |
| `name`  | 배포의 이름입니다. |
| `model` | 이 예제에서는 모델 속성을 인라인으로 `local_path` 지정합니다. 모델 파일은 자동으로 업로드되고 자동 생성 이름으로 등록됩니다. 관련 모범 사례는 다음 섹션의 팁을 참조하세요. |
| `code_configuration.code.local_path` | 모델 채점을 위한 모든 Python 소스 코드가 포함된 디렉터리입니다. 중첩된 디렉터리 및 패키지를 사용할 수 있습니다. |
| `code_configuration.scoring_script` | `code_configuration.code.local_path` 채점 디렉터리에 있는 Python 파일입니다. 이 Python 코드에는 `init()` 함수와 `run()` 함수가 있어야 합니다. `init()` 함수는 모델을 만들거나 업데이트한 후에 호출됩니다(모델을 메모리에 캐시하는 등의 용도로 사용할 수 있음). `run()` 함수는 실제 채점/예측을 수행하도록 엔드포인트를 호출할 때마다 호출됩니다. |
| `environment` | 모델 및 코드를 호스트할 환경의 세부 정보를 포함합니다. 이 예제에는 를 포함하는 인라인 정의가 `path` 있습니다. 이미지에 `environment.docker.image`를 사용합니다. `conda_file` 종속성은 이미지 위에 설치됩니다. 자세한 내용은 다음 섹션의 팁을 참조하세요. |
| `instance_type` | 배포 인스턴스를 호스트할 VM SKU입니다. 자세한 내용은 [관리형 온라인 엔드포인트 지원 VM SKU](reference-managed-online-endpoints-vm-sku-list.md)를 참조하세요. |
| `instance_count` | 배포의 인스턴스 수입니다. 예상되는 워크로드 값을 기준으로 합니다. 고가용성을 위해 `scale_settings.instance_count`를 `3` 이상으로 설정하는 것이 좋습니다. |

YAML 스키마에 대한 자세한 내용은 [온라인 엔드포인트 YAML 참조](reference-yaml-endpoint-managed-online.md)를 참조하세요.

> [!NOTE]
> 관리형 엔드포인트 대신 Kubernetes를 컴퓨팅 대상으로 사용하려면 다음을 수행합니다.
> 1. Azure Machine Learning Studio 를 사용하여 Kubernetes 클러스터를 컴퓨팅 대상으로 만들고 [Azure Machine Learning](how-to-attach-arc-kubernetes.md?&tabs=studio#attach-arc-cluster)작업 영역에 연결합니다.
> 1. [엔드포인트 YAML을](https://github.com/Azure/azureml-examples/blob/main/cli/endpoints/online/aks/simple-flow/1-create-aks-endpoint-with-blue.yml) 사용하여 관리형 엔드포인트 YAML 대신 Kubernetes를 대상으로 지정합니다. `target`의 값을 등록된 컴퓨팅 대상의 이름으로 변경하려면 YAML을 편집해야 합니다.
>
> 이 문서에서 사용되는 모든 명령(선택적 SLA 모니터링 및 Azure Log Analytics 통합 제외)은 관리형 엔드포인트 또는 Kubernetes 엔드포인트에서 사용할 수 있습니다.

### <a name="register-your-model-and-environment-separately"></a>모델 및 환경을 별도로 등록

이 예제에서는 `local_path` 인라인으로 (파일을 업로드할 위치)를 지정합니다. CLI는 자동으로 파일을 업로드하고 모델과 환경을 등록합니다. 프로덕션 환경에서는 모델 및 환경을 등록하고 YAML에 별도로 등록된 이름 및 버전을 지정하는 것이 모범 사례입니다. `model: azureml:my-model:1` 또는 `environment: azureml:my-env:1` 형식을 사용합니다.

등록을 수행하려면 `model` 및 `environment`의 YAML 정의를 별도의 YAML 파일에 추출하고 `az ml model create` 및 `az ml environment create` 명령을 사용할 수 있습니다. 이러한 명령에 대해 자세히 알아보려면 `az ml model create -h` 및 `az ml environment create -h`를 실행합니다.

### <a name="use-different-cpu-and-gpu-instance-types"></a>다른 CPU 및 GPU 인스턴스 유형 사용

이전 YAML은 범용 유형(`Standard_F2s_v2`) 및 GPU가 아닌 Docker 이미지를 사용합니다(YAML에서 `image` 특성 참조). GPU 컴퓨팅의 경우 GPU 컴퓨팅 유형 SKU 및 GPU Docker 이미지를 선택합니다.

지원되는 범용 및 GPU 인스턴스 유형을 보려면 [관리형 온라인 엔드포인트에서 지원되는 VM SKU](reference-managed-online-endpoints-vm-sku-list.md)를 참조하세요. Azure Machine Learning CPU 및 GPU 기본 이미지 목록은 [Azure Machine Learning 기본 이미지](https://github.com/Azure/AzureML-Containers)를 참조하세요.

### <a name="use-more-than-one-model"></a>두 개 이상의 모델 사용

현재 YAML에는 배포당 하나의 모델만 지정할 수 있습니다. 모델이 두 개 이상 있는 경우 모델을 등록할 때 모든 모델(파일 또는 하위 디렉터리)을 등록에 사용할 폴더에 복사합니다. 채점 스크립트에서 환경 변수 `AZUREML_MODEL_DIR`을 사용하여 모델 루트 폴더의 경로를 가져옵니다. 기본 디렉터리 구조는 보존됩니다.

## <a name="understand-the-scoring-script"></a>채점 스크립트 이해

> [!TIP]
> 온라인 엔드포인트에 대한 점수 매기기 스크립트의 형식은 이전 버전의 CLI 및 Python SDK에서 사용된 것과 동일한 형식입니다.

앞서 언급했듯이 `code_configuration.scoring_script`에는 `init()` 함수와 `run()` 함수가 있어야 합니다. 이 예제에서는 이 [score.py 파일](https://github.com/Azure/azureml-examples/blob/main/cli/endpoints/online/model-1/onlinescoring/score.py)을 사용합니다. 컨테이너가 초기화/시작되면 `init()` 함수가 호출됩니다. 초기화는 일반적으로 배포를 만들거나 업데이트한 직후에 발생합니다. 이 예제에서와 같이 메모리에 모델을 캐시하는 것과 같은 글로벌 초기화 작업을 수행하는 논리를 여기에 작성합니다. `run()` 함수는 엔드포인트를 호출할 때마다 호출되며, 실제 채점/예측을 수행해야 합니다. 이 예제에서는 JSON 입력에서 데이터를 추출하고, scikit-learn 모델의 `predict()` 메서드를 호출하고, 결과를 반환합니다.

## <a name="deploy-and-debug-locally-by-using-local-endpoints"></a>로컬 엔드포인트를 사용하여 로컬로 배포 및 디버그

디버깅 시간을 절약하려면 엔드포인트를 로컬에서 테스트 실행하는 것이 *매우 좋습니다*. 자세한 내용은 [Visual Studio Code 로컬로 관리되는 온라인 엔드포인트 디버그를 참조하세요.](how-to-debug-managed-online-endpoints-visual-studio-code.md)

> [!NOTE]
> * 로컬로 배포하려면 [Docker 엔진](https://docs.docker.com/engine/install/)을 설치해야 합니다.
> * Docker 엔진이 실행 중이어야 합니다. Docker 엔진은 일반적으로 컴퓨터가 시작될 때 시작됩니다. 그렇지 않은 경우 [Docker 엔진 문제를 해결](https://docs.docker.com/config/daemon/#start-the-daemon-manually)할 수 있습니다.

> [!IMPORTANT]
> 로컬 엔드포인트 배포의 목표는 Azure에 배포하기 전에 코드 및 구성의 유효성을 검사하고 디버그하는 것입니다. 로컬 배포에는 다음과 같은 제한 사항이 있습니다.
> - 로컬 엔드포인트는 트래픽 규칙, 인증, 스케일링 설정 또는 프로브 설정을 지원하지 *않습니다*. 
> - 로컬 엔드포인트는 엔드포인트당 하나의 배포만 지원합니다. 

### <a name="deploy-the-model-locally"></a>로컬에서 모델 배포

먼저 엔드포인트를 만듭니다. 필요에 따라 로컬 엔드포인트의 경우 이 단계를 건너뛰고 배포(다음 단계)를 직접 만들어 필요한 메타데이터를 만들 수 있습니다. 이 방법은 개발 및 테스트 목적으로 유용합니다.

:::code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-local-endpoint.sh" ID="create_endpoint":::

이제 엔드포인트 아래에 라는 `blue` 배포를 만듭니다.

:::code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-local-endpoint.sh" ID="create_deployment":::

`--local` 플래그는 Docker 환경에 엔드포인트를 배포하도록 CLI에 지시합니다.

> [!TIP]
> Visual Studio Code 사용하여 엔드포인트를 로컬로 테스트하고 디버그합니다. 자세한 내용은 [Visual Studio Code 로컬로 관리되는 온라인 엔드포인트 디버그를 참조하세요.](how-to-debug-managed-online-endpoints-visual-studio-code.md)

### <a name="verify-the-local-deployment-succeeded"></a>로컬 배포에 성공했는지 확인

상태를 확인하여 모델이 오류 없이 배포되었는지 확인합니다.

:::code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-local-endpoint.sh" ID="get_status":::

출력은 다음 JSON과 유사하게 표시됩니다. 여기서 `provisioning_state`는 `Succeeded`입니다.

```json
{
  "auth_mode": "key",
  "location": "local",
  "name": "docs-endpoint",
  "properties": {},
  "provisioning_state": "Succeeded",
  "scoring_uri": "http://localhost:49158/score",
  "tags": {},
  "traffic": {}
}
```

### <a name="invoke-the-local-endpoint-to-score-data-by-using-your-model"></a>로컬 엔드포인트를 호출하여 모델로 데이터 채점

엔드포인트를 호출하여 편리한 명령 `invoke`를 사용하고 JSON 파일에 저장된 쿼리 매개 변수를 전달하여 모델을 채점합니다.

:::code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-local-endpoint.sh" ID="test_endpoint":::

REST 클라이언트(예: curl)를 사용하려면 채점 URI가 있어야 합니다. 채점 URI를 얻으려면 `az ml online-endpoint show --local -n $ENDPOINT_NAME`을 실행합니다. 반환된 데이터에서 `scoring_uri` 특성을 찾습니다. 샘플 curl 기반 명령은 이 문서의 후반부에서 사용할 수 있습니다.

### <a name="review-the-logs-for-output-from-the-invoke-operation"></a>호출 작업의 출력에 대한 로그를 검토합니다.

예제 *score.py* 파일에서 `run()` 메서드는 콘솔에 일부 출력을 로그합니다. `get-logs` 명령을 다시 사용하여 이 출력을 볼 수 있습니다.

:::code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-local-endpoint.sh" ID="get_logs":::

##  <a name="deploy-your-managed-online-endpoint-to-azure"></a>Azure에 관리형 온라인 엔드포인트 배포

다음으로 Azure에 관리형 온라인 엔드포인트를 배포합니다.

### <a name="deploy-to-azure"></a>Azure에 배포

클라우드에서 엔드포인트를 만들려면 다음 코드를 실행합니다.

::: code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-managed-online-endpoint.sh" ID="create_endpoint" :::

엔드포인트에서 라는 배포를 만들려면 `blue` 다음 코드를 실행합니다.

::: code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-managed-online-endpoint.sh" ID="create_deployment" :::

이 배포는 기본 환경/이미지가 처음으로 빌드되는지 여부에 따라 최대 15분 정도 걸릴 수 있습니다. 동일한 환경을 사용하는 후속 배포는 더 빨리 처리를 완료합니다.

> [!Important]
> 위의 --all-traffic 플래그는 `az ml online-deployment create` 새로 만든 배포에 엔드포인트에 트래픽의 100%를 할당합니다. 이는 개발 및 테스트 목적으로 유용하지만 프로덕션의 경우 명시적 명령을 통해 새 배포에 대한 트래픽을 여는 것이 좋습니다. 예를 들어 `az ml online-endpoint update -n $ENDPOINT_NAME --traffic "blue=100"` 

> [!TIP]
> * CLI 콘솔을 차단하지 않으려는 경우 명령에 `--no-wait` 플래그를 추가할 수 있습니다. 그러나 이렇게 하면 배포 상태의 대화형 표시가 중지됩니다.
>
> * [관리형 온라인 엔드포인트 배포 문제 해결(미리 보기)](./how-to-troubleshoot-online-endpoints.md)을 사용하여 오류를 디버그합니다.

### <a name="check-the-status-of-the-deployment"></a>배포 상태 확인

`show` 명령에는 엔드포인트 및 배포에 대한 정보가 `provisioning_status`에 포함되어 있습니다.

::: code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-managed-online-endpoint.sh" ID="get_status" :::

`list` 명령을 사용하여 작업 영역의 모든 엔드포인트를 테이블 형식으로 나열할 수 있습니다.

```azurecli
az ml online-endpoint list --output table
```

### <a name="check-the-status-of-the-cloud-deployment"></a>클라우드 배포 상태 확인

로그를 확인하여 모델이 오류 없이 배포되었는지 확인합니다.

:::code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-managed-online-endpoint.sh" ID="get_logs" :::

기본적으로 로그는 inference-server에서 풀합니다. 스토리지 이니셜라이저(모델 및 코드와 같은 자산을 컨테이너에 탑재함)의 로그를 보려면 `--container storage-initializer` 플래그를 추가합니다.

### <a name="invoke-the-endpoint-to-score-data-by-using-your-model"></a>엔드포인트를 호출하여 모델을 사용하여 데이터 채점

선택한 `invoke` 명령 또는 REST 클라이언트를 사용하여 엔드포인트를 호출하고 일부 데이터를 채점할 수 있습니다. 

::: code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-managed-online-endpoint.sh" ID="test_endpoint" :::

다음 예제에서는 엔드포인트에 인증하는 데 사용되는 키를 얻는 방법을 보여줍니다.

:::code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-managed-online-endpoint.sh" ID="test_endpoint_using_curl_get_key":::

다음으로 curl을 사용하여 데이터를 채점합니다.

::: code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-managed-online-endpoint.sh" ID="test_endpoint_using_curl" :::

및 명령을 사용하여 `show` `get-credentials` 인증 자격 증명을 얻습니다. 또한 플래그를 사용하여 `--query` 특성을 필요한 것으로만 필터링합니다. `--query`에 대한 자세한 내용은 [Azure CLI 명령 출력 쿼리](/cli/azure/query-azure-cli)를 참조하세요.

호출 로그를 보려면 `get-logs`를 다시 실행합니다.

### <a name="optional-update-the-deployment"></a>(선택 사항) 배포 업데이트

코드, 모델, 환경 또는 스케일링 설정을 업데이트하려면 YAML 파일을 업데이트하고 `az ml online-endpoint update` 명령을 실행합니다. 

> [!Note]
> 단일 명령에서 인스턴스 수 및 를 다른 모델 설정(코드, 모델 또는 환경)과 함께 업데이트하는 경우 `update` 먼저 크기 조정 작업이 수행되고 다른 업데이트가 적용됩니다. 프로덕션 환경에서는 이러한 작업을 별도로 수행하는 것이 좋습니다.

`update`의 작동 방식을 이해하려면 다음을 수행합니다.

1. *online/model-1/onlinescoring/score.py* 파일을 엽니다.
1. `init()` 함수의 마지막 줄을 변경합니다. `logging.info("Init complete")` 다음에 `logging.info("Updated successfully")`를 추가합니다. 
1. 파일을 저장합니다.
1. 다음 명령을 실행합니다.

    ```azurecli
    az ml online-deployment update -n blue --endpoint $ENDPOINT_NAME -f endpoints/online/managed/sample/blue-deployment.yml
    ```

    > [!Note]
    > YAML을 사용한 업데이트는 선언적입니다. 즉, YAML의 변경 내용이 기본 Azure Resource Manager 리소스(엔드포인트 및 배포)에 반영됩니다. 선언적 접근 방식은 [GitOps](https://www.atlassian.com/git/tutorials/gitops)를 용이하게 합니다. 엔드포인트 및 배포에 대한 *모든* 변경 사항(`instance_count` 포함)은 YAML을 거칩니다. `--set` 플래그를 사용하여 YAML을 사용하지 않고 업데이트할 수 있습니다.
    
1. `init()` 함수를 수정했기 때문에(`init()`은 엔드포인트가 생성되거나 업데이트될 때 실행됨) `Updated successfully` 메시지가 로그에 표시됩니다. 다음을 실행하여 로그를 검색합니다.

    :::code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-managed-online-endpoint.sh" ID="get_logs" :::

이 `update` 명령은 로컬 배포에서도 작동합니다. 동일한 `az ml online-deployment update` 명령에 `--local` 플래그를 사용합니다.

> [!TIP]
> `update` 명령을 통해 [Azure CLI의 `--set` 매개 변수](/cli/azure/use-cli-effectively#generic-update-arguments)를 사용하여 YAML의 특성을 *재정의하거나* YAML 파일을 전달하지 않고 특정 특성을 설정할 수 있습니다. 단일 특성에 `--set`를 사용하는 것은 개발 및 테스트 시나리오에서 특히 중요합니다. 예를 들어 첫 번째 배포의 `instance_count` 값을 스케일 업하려면 `--set instance_count=2` 플래그를 사용하면 됩니다. 그러나 YAML이 업데이트되지 않으므로 이 기법을 사용하면 [GitOps](https://www.atlassian.com/git/tutorials/gitops)가 용이하지 않습니다.
> [!Note]
> 위의 예는 내부 롤링 업데이트의 예입니다. 즉, 동일한 배포가 새 구성으로 업데이트되고 한 번에 20%의 노드가 있습니다. 배포에 10개의 노드가 있는 경우 한 번에 2개의 노드가 업데이트됩니다. 프로덕션 사용의 경우 보다 안전한 대안을 제공하는 [파란색-녹색 배포를](how-to-safely-rollout-managed-endpoints.md)고려할 수 있습니다.
### <a name="optional-configure-autoscaling"></a>필드 자동 크기 조정 구성

자동 크기 조정은 응용 프로그램의 부하를 처리 하기 위해 적절 한 양의 리소스를 자동으로 실행 합니다. 관리 되는 온라인 끝점은 Azure monitor 자동 크기 조정 기능과 통합 하 여 자동 크기 조정을 지원 합니다. 자동 크기 조정을 구성 하려면 [온라인 끝점을 자동 크기 조정 하는 방법](how-to-autoscale-endpoints.md)을 참조 하세요.

### <a name="optional-monitor-sla-by-using-azure-monitor"></a>(선택 사항) Azure Monitor를 사용하여 SLA 모니터링

SLA에 따라 메트릭을 보고 알림을 설정하려면 [관리형 온라인 엔드포인트 모니터링](how-to-monitor-online-endpoints.md)에 설명된 단계를 완료하세요.

### <a name="optional-integrate-with-log-analytics"></a>(선택 사항) Log Analytics와 통합

`get-logs` 명령은 자동으로 선택된 인스턴스에서 마지막 몇 백 줄의 로그만 제공합니다. 그러나 Log Analytics는 지속적으로 로그를 저장하고 분석하는 방법을 제공합니다. 

먼저 [Azure Portal에서 Log Analytics 작업 영역 만들기](../azure-monitor/logs/quick-create-workspace.md#create-a-workspace)의 단계를 완료하여 Log Analytics 작업 영역을 만듭니다.

그런 다음, Azure Portal에서 다음을 수행합니다.

1. 리소스 그룹으로 이동합니다.
1. 엔드포인트를 선택합니다.
1. **ARM 리소스 페이지** 를 선택합니다.
1. **진단 설정** 을 선택합니다.
1. **설정 추가** 를 선택합니다.
1. Log Analytics 작업 영역에 콘솔 로그를 보내도록 설정하려면 선택합니다.

로그를 연결하는 데 최대 1시간이 걸릴 수 있습니다. 1시간 후 채점 요청을 보내고 다음 단계를 사용하여 로그를 확인합니다.

1. Log Analytics 작업 영역을 엽니다. 
1. 왼쪽 메뉴에서 **로그** 를 선택합니다.
1. 자동으로 열리는 **쿼리** 대화 상자를 닫습니다.
1. **AmlOnlineEndpointConsoleLog** 를 두 번 클릭합니다.
1. **실행** 을 선택합니다.

## <a name="delete-the-endpoint-and-the-deployment"></a>엔드포인트 및 배포 삭제

배포를 사용하지 않을 경우 다음 코드를 실행하여 배포를 삭제해야 합니다(엔드포인트 및 모든 기본 배포 삭제).

::: code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-managed-online-endpoint.sh" ID="delete_endpoint" :::

## <a name="next-steps"></a>다음 단계

자세한 내용은 다음 문서를 검토하세요.

- [REST를 사용하여 모델 배포(미리 보기)](how-to-deploy-with-rest.md)
- [스튜디오에서 관리형 온라인 엔드포인트(미리 보기) 만들기 및 사용](how-to-use-managed-online-endpoint-studio.md)
- [온라인 엔드포인트에 대한 안전한 롤아웃(미리 보기)](how-to-safely-rollout-managed-endpoints.md)
- [관리 되는 온라인 끝점을 자동 크기 조정 하는 방법](how-to-autoscale-endpoints.md)
- [일괄 처리 채점에 일괄 처리 엔드포인트(미리 보기) 사용](how-to-use-batch-endpoint.md)
- [Azure Machine Learning 관리형 온라인 엔드포인트(미리 보기)에 대한 비용 보기](how-to-view-online-endpoints-costs.md)
- [관리 되는 온라인 끝점 및 관리 id (미리 보기)를 사용 하 여 Azure 리소스에 액세스](how-to-access-resources-from-endpoints-managed-identities.md)
- [관리형 온라인 엔드포인트 배포 문제 해결](how-to-troubleshoot-online-endpoints.md)
