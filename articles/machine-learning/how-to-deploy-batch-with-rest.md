---
title: REST API를 사용하여 일괄 처리 엔드포인트를 사용하여 모델 배포(미리 보기)
titleSuffix: Azure Machine Learning
description: REST API와 함께 일괄 처리 엔드포인트를 사용하여 모델을 배포하는 방법을 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
author: tracych
ms.author: tracych
ms.date: 10/21/2021
ms.reviewer: laobri
ms.custom: devplatv2
ms.openlocfilehash: 351855c870f3f3658488c66c401cef1c3c4b17c4
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/09/2021
ms.locfileid: "132059812"
---
# <a name="deploy-models-with-rest-preview-for-batch-scoring"></a>일괄 처리 채점을 위한 REST(미리 보기)를 통해 모델 배포 

Azure Machine Learning REST API 사용하여 일괄 처리 채점(미리 보기)을 위한 모델을 배포하는 방법을 알아봅니다.

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

REST API는 표준 HTTP 동사를 사용하여 리소스를 만들고, 검색하고, 업데이트하고, 삭제합니다. REST API는 HTTP 요청을 만들 수 있는 모든 언어 또는 도구에서 작동합니다. REST는 구조가 간단하기 때문에 스크립팅 환경 및 MLOps 자동화에 적합합니다.

이 문서에서는 새 REST API를 사용하여 다음 작업을 수행하는 방법을 알아봅니다.

> [!div class="checklist"]
> * 기계 학습 자산 만들기
> * 일괄 처리 엔드포인트 및 일괄 처리 배포 만들기
> * 일괄 처리 엔드포인트를 호출하여 일괄 처리 채점 작업 시작

## <a name="prerequisites"></a>사전 요구 사항

- 본인에게 관리 권한이 있는 **Azure 구독** 이러한 구독이 없는 경우 [평가판 또는 유료 개인 구독](https://azure.microsoft.com/free/)을 사용해 보세요.
- [Azure Machine Learning 작업 영역](how-to-manage-workspace.md)
- 작업 영역의 서비스 사용자. 관리 REST 요청은 [서비스 사용자 인증](how-to-setup-authentication.md#use-service-principal-authentication)을 사용합니다.
- 서비스 사용자 인증 토큰. [서비스 사용자 인증 토큰 검색](./how-to-manage-rest.md#retrieve-a-service-principal-authentication-token)의 단계에 따라 이 토큰을 검색하세요. 
- **curl** 유틸리티. **curl** 프로그램은 [Linux용 Windows 하위 시스템](/windows/wsl/install-win10) 또는 모든 UNIX에서 사용할 수 있습니다. PowerShell에서 **curl** 은 **Invoke-WebRequest** 의 별칭으로, `curl -d "key=val" -X POST uri`는 `Invoke-WebRequest -Body "key=val" -Method POST -Uri uri`가 됩니다. 
- [jq](https://stedolan.github.io/jq/) JSON 프로세서입니다.

> [!IMPORTANT]
> 이 문서의 코드 조각에서는 Bash 셸을 사용한다고 가정합니다.
>
> 코드 조각은 `/cli/batch-score-rest.sh` [AzureML 예제 리포지토리](https://github.com/Azure/azureml-examples)의 파일에서 가져옵니다.

## <a name="set-endpoint-name"></a>엔드포인트 이름 설정

> [!NOTE]
> Batch 엔드포인트 이름은 Azure 지역 수준에서 고유해야 합니다. 예를 들어 westus2에는 mybatbatdpoint라는 이름의 일괄 처리 엔드포인트가 하나만 있을 수 있습니다.

:::code language="rest-api" source="~/azureml-examples-main/cli/batch-score-rest.sh" id="set_endpoint_name":::

## <a name="azure-machine-learning-batch-endpoints"></a>일괄 처리 엔드포인트 Azure Machine Learning

[Batch 엔드포인트(미리 보기)는](concept-endpoints.md#what-are-batch-endpoints-preview) 일괄 처리 채점을 위해 모델을 호스트하는 프로세스를 간소화하므로 인프라가 아닌 기계 학습에 집중할 수 있습니다. 이 문서에서는 일괄 처리 엔드포인트 및 배포를 만들고 이를 호출하여 일괄 처리 채점 작업을 시작합니다. 그러나 먼저 모델, 코드 및 환경을 포함하여 배포에 필요한 자산을 등록해야 합니다.

Azure CLI 를 [포함하여](how-to-use-batch-endpoint.md)Azure Machine Learning 일괄 처리 엔드포인트를 만들고 [스튜디오](how-to-use-batch-endpoints-studio.md)를 사용하여 시각적으로 만드는 여러 가지 방법이 있습니다. 다음 예제에서는 REST API 배치 엔드포인트 및 배포를 만듭니다.

## <a name="create-machine-learning-assets"></a>기계 학습 자산 만들기

먼저 Azure Machine Learning 자산을 설정하여 작업을 구성합니다.

다음 REST API 호출에서는 `SUBSCRIPTION_ID`, `RESOURCE_GROUP`, `LOCATION` 및 `WORKSPACE`를 자리 표시자로 사용합니다. 자리 표시자를 고유한 값으로 바꿉니다. 

관리 REST는 [서비스 사용자 인증 토큰](how-to-manage-rest.md#retrieve-a-service-principal-authentication-token)을 요청합니다. `TOKEN`을 고유한 값으로 바꿉니다. 다음 명령을 사용하여 이 토큰을 검색할 수 있습니다.

:::code language="rest-api" source="~/azureml-examples-main/cli/batch-score-rest.sh" range="13":::

서비스 공급자는 `api-version` 인수를 사용하여 호환성을 보장합니다. `api-version` 인수는 서비스마다 다릅니다. 이후 버전을 수용할 수 있도록 API 버전을 변수로 설정합니다.

:::code language="rest-api" source="~/azureml-examples-main/cli/batch-score-rest.sh" range="11":::

### <a name="create-compute"></a>컴퓨팅 만들기
일괄 처리 채점은 로컬이 아닌 클라우드 컴퓨팅 리소스에서만 실행됩니다. 클라우드 컴퓨팅 리소스는 일괄 처리 채점 워크플로를 실행할 수 있는 재사용 가능한 가상 컴퓨터 클러스터입니다.

컴퓨팅 클러스터 만들기:

:::code language="rest-api" source="~/azureml-examples-main/cli/batch-score-rest.sh" id="create_compute":::

> [!TIP]
> 기존 컴퓨팅을 대신 사용하려면 일괄 처리 배포 를 만들 때 전체 Azure Resource Manager [ID를](#create-batch-deployment)지정해야 합니다. 전체 ID는 형식을 `/subscriptions/$SUBSCRIPTION_ID/resourceGroups/$RESOURCE_GROUP/providers/Microsoft.MachineLearningServices/workspaces/$WORKSPACE/computes/<your-compute-name>` 사용합니다.

### <a name="get-storage-account-details"></a>스토리지 계정 세부 정보 가져오기

모델 및 코드를 등록하려면 먼저 스토리지 계정에 업로드해야 합니다. 스토리지 계정의 세부 정보는 데이터 저장소에서 확인할 수 있습니다. 이 예제에서는 작업 영역에 대한 기본 데이터 저장소 및 Azure Storage 계정을 가져옵니다. GET 요청을 통해 작업 영역을 쿼리하여 정보가 포함된 JSON 파일을 가져옵니다.

[jq](https://stedolan.github.io/jq/) 도구를 사용하여 JSON 결과를 구문 분석하고 필요한 값을 얻을 수 있습니다. Azure Portal을 사용하여 동일한 정보를 찾을 수도 있습니다.

:::code language="rest-api" source="~/azureml-examples-main/cli/batch-score-rest.sh" id="get_storage_details":::

### <a name="upload--register-code"></a>코드 업로드 및 등록

이제 데이터 저장소가 생겼으므로 채점 스크립트를 업로드할 수 있습니다. Azure Storage CLI를 사용하여 Blob을 기본 컨테이너에 업로드합니다.

:::code language="rest-api" source="~/azureml-examples-main/cli/batch-score-rest.sh" id="upload_code":::

> [!TIP]
> Azure Portal 또는 [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/)와 같은 다른 방법을 사용하여 업로드할 수도 있습니다.

코드를 업로드한 후에는 PUT 요청을 통해 코드를 지정할 수 있습니다.

:::code language="rest-api" source="~/azureml-examples-main/cli/batch-score-rest.sh" id="create_code":::

### <a name="upload-and-register-model"></a>모델 업로드 및 등록

코드와 마찬가지로 모델 파일을 업로드합니다.

:::code language="rest-api" source="~/azureml-examples-main/cli/batch-score-rest.sh" id="upload_model":::

이제 모델을 등록합니다.

:::code language="rest-api" source="~/azureml-examples-main/cli/batch-score-rest.sh" id="create_model":::

### <a name="create-environment"></a>환경 만들기
필요한 종속성이 있는 환경에서 배포를 실행해야 합니다. PUT 요청을 사용하여 환경을 만듭니다. Microsoft Container Registry의 docker 이미지 사용 `image`를 사용하여 docker 이미지를 구성하고 `condaFile`을 사용하여 conda 종속성을 추가할 수 있습니다.

다음 코드를 실행하여 `condaFile` json으로 정의된 를 읽습니다. 소스 파일은 `/cli/endpoints/batch/mnist/environment/conda.json` 예제 리포지토리의 에 있습니다.

:::code language="rest-api" source="~/azureml-examples-main/cli/batch-score-rest.sh" id="read_condafile":::

이제 다음 조각을 실행하여 환경을 만듭니다.

:::code language="rest-api" source="~/azureml-examples-main/cli/batch-score-rest.sh" id="create_environment":::

## <a name="deploy-with-batch-endpoints"></a>일괄 처리 엔드포인트를 통해 배포

다음으로 일괄 처리 엔드포인트, 배포를 만들고 기본 배포를 설정합니다.

### <a name="create-batch-endpoint"></a>일괄 처리 엔드포인트 만들기

일괄 처리 엔드포인트를 만듭니다.

:::code language="rest-api" source="~/azureml-examples-main/cli/batch-score-rest.sh" id="create_endpoint":::

### <a name="create-batch-deployment"></a>일괄 처리 배포 만들기

엔드포인트에서 일괄 처리 배포를 만듭니다.

:::code language="rest-api" source="~/azureml-examples-main/cli/batch-score-rest.sh" id="create_deployment":::

### <a name="set-the-default-batch-deployment-under-the-endpoint"></a>엔드포인트에서 기본 일괄 처리 배포 설정

하나의 엔드포인트 아래에는 일괄 처리 채점 작업을 실행하기 위해 를 호출할 때 사용되는 하나의 기본 일괄 처리 배포만 있습니다.

:::code language="rest-api" source="~/azureml-examples-main/cli/batch-score-rest.sh" id="set_endpoint_defaults":::

## <a name="run-batch-scoring"></a>일괄 처리 점수 매기기 실행

일괄 처리 엔드포인트를 호출하는 경우 일괄 처리 채점 작업이 트리거됩니다. 작업은 `id` 응답에 반환되며 일괄 처리 점수 매기기 진행 상황을 추적하는 데 사용할 수 있습니다. 다음 조각에서 `jq` 는 작업을 얻는 데 `id` 사용됩니다.

### <a name="invoke-the-batch-endpoint-to-start-a-batch-scoring-job"></a>일괄 처리 엔드포인트를 호출하여 일괄 처리 채점 작업 시작

일괄 처리 엔드포인트를 호출하는 점수 매기기 URI 및 액세스 토큰을 얻습니다. 먼저 채점 URI를 가져옵니다.

:::code language="rest-api" source="~/azureml-examples-main/cli/batch-score-rest.sh" id="get_endpoint":::

일괄 처리 엔드포인트 액세스 토큰을 얻습니다.

:::code language="rest-api" source="~/azureml-examples-main/cli/batch-score-rest.sh" id="get_access_token":::

이제 일괄 처리 엔드포인트를 호출하여 일괄 처리 채점 작업을 시작합니다. 다음 예제에서는 클라우드에서 공개적으로 사용할 수 있는 데이터의 점수를 점수로 정합니다.

:::code language="rest-api" source="~/azureml-examples-main/cli/batch-score-rest.sh" id="score_endpoint_with_data_in_cloud":::

데이터가 등록된 Azure Machine Learning 데이터 저장소에 저장된 경우 데이터 세트를 통해 일괄 처리 엔드포인트를 호출할 수 있습니다. 다음 코드는 새 데이터 세트를 만듭니다.

:::code language="rest-api" source="~/azureml-examples-main/cli/batch-score-rest.sh" id="create_dataset":::

다음으로, 일괄 처리 엔드포인트를 호출할 때 데이터 세트를 참조합니다.

:::code language="rest-api" source="~/azureml-examples-main/cli/batch-score-rest.sh" id="score_endpoint_with_dataset":::

이전 코드 조각에서 사용자 지정 출력 위치는 , 및 를 사용하여 `datastoreId` `path` `outputFileName` 제공됩니다. 이러한 설정을 사용하면 일괄 처리 점수 매기기 결과를 저장할 위치를 구성할 수 있습니다.

> [!IMPORTANT]
> 고유한 출력 위치를 제공해야 합니다. 출력 파일이 이미 있으면 일괄 처리 채점 작업이 실패합니다.

이 예제에서는 출력이 작업 영역의 기본 Blob Storage에 저장됩니다. 폴더 이름은 엔드포인트 이름과 동일하며 파일 이름은 다음 코드에 의해 임의로 생성됩니다.

:::code language="azurecli" source="~/azureml-examples-main/cli/batch-score-rest.sh" ID="unique_output" :::

### <a name="check-the-batch-scoring-job"></a>일괄 처리 점수 매기기 작업 확인

일괄 처리 채점 작업은 일반적으로 전체 입력 집합을 처리하는 데 다소 시간이 소요됩니다. 작업 상태를 모니터링 하 고 완료 된 후 결과를 확인 합니다.

> [!TIP]
> 이 예에서는 일괄 처리 끝점의 기본 배포를 호출 합니다. 기본이 아닌 배포를 호출 하려면 HTTP 헤더를 사용 하 `azureml-model-deployment` 고 값을 배포 이름으로 설정 합니다. 예를 들어를 사용 하 여의 매개 변수를 `--header "azureml-model-deployment: $DEPLOYMENT_NAME"` 말아 사용 합니다.

:::code language="rest-api" source="~/azureml-examples-main/cli/batch-score-rest.sh" id="check_job":::

### <a name="check-batch-scoring-results"></a>일괄 처리 채점 결과 확인

결과를 확인 하는 방법에 대 한 자세한 내용은 [일괄 처리 점수 매기기 결과 확인](how-to-use-batch-endpoint.md#check-batch-scoring-results)을 참조 하세요.

## <a name="delete-the-batch-endpoint"></a>일괄 처리 끝점 삭제

일괄 처리 끝점을 사용 하지 않는 경우 다음 명령을 사용 하 여 삭제 해야 합니다. 일괄 처리 끝점과 모든 기본 배포를 삭제 합니다.

:::code language="rest-api" source="~/azureml-examples-main/cli/batch-score-rest.sh" id="delete_endpoint":::

## <a name="next-steps"></a>다음 단계

* [Azure CLI를 사용 하 여](how-to-use-batch-endpoint.md)일괄 처리 점수 매기기를 위한 모델을 배포 하는 방법을 알아봅니다.
* [Studio를 사용 하 여](how-to-use-batch-endpoints-studio.md)일괄 처리 점수 매기기를 위한 모델을 배포 하는 방법을 알아봅니다.
* [일괄 처리 끝점 문제 해결](how-to-troubleshoot-batch-endpoints.md) 에 대 한 자세한 정보
