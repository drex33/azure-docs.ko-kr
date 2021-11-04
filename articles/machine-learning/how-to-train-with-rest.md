---
title: REST를 사용하여 모델 학습(미리 보기)
titleSuffix: Azure Machine Learning
description: REST API를 사용하여 모델을 학습시키고 작업을 만드는 방법을 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
author: wenxwei
ms.author: wenxwei
ms.date: 10/21/2021
ms.reviewer: peterlu
ms.custom: devplatv2
ms.openlocfilehash: f411d33baea7863b9139069f1eefd1282cf7b730
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131562089"
---
# <a name="train-models-with-rest-preview"></a>REST를 사용하여 모델 학습(미리 보기)

Azure Machine Learning REST API를 사용하여 학습 작업을 만들고 관리하는 방법을 알아봅니다(미리 보기).

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

REST API는 표준 HTTP 동사를 사용하여 리소스를 만들고, 검색하고, 업데이트하고, 삭제합니다. REST API는 HTTP 요청을 만들 수 있는 모든 언어 또는 도구에서 작동합니다. REST는 구조가 간단하기 때문에 스크립팅 환경 및 MLOps 자동화에 적합합니다.

이 문서에서는 새 REST API를 사용하여 다음 작업을 수행하는 방법을 알아봅니다.

> [!div class="checklist"]
> * 기계 학습 자산 만들기
> * 기본 학습 작업 만들기 
> * 하이퍼 매개 변수 튜닝 스윕 작업 만들기

## <a name="prerequisites"></a>사전 요구 사항

- 본인에게 관리 권한이 있는 **Azure 구독** 이러한 구독이 없는 경우 [평가판 또는 유료 개인 구독](https://azure.microsoft.com/free/)을 사용해 보세요.
- [Azure Machine Learning 작업 영역](how-to-manage-workspace.md)
- 작업 영역의 서비스 사용자. 관리 REST 요청은 [서비스 사용자 인증](how-to-setup-authentication.md#use-service-principal-authentication)을 사용합니다.
- 서비스 사용자 인증 토큰. [서비스 사용자 인증 토큰 검색](./how-to-manage-rest.md#retrieve-a-service-principal-authentication-token)의 단계에 따라 이 토큰을 검색하세요. 
- **curl** 유틸리티. **curl** 프로그램은 [Linux용 Windows 하위 시스템](/windows/wsl/install-win10) 또는 모든 UNIX에서 사용할 수 있습니다. PowerShell에서 **curl** 은 **Invoke-WebRequest** 의 별칭으로, `curl -d "key=val" -X POST uri`는 `Invoke-WebRequest -Body "key=val" -Method POST -Uri uri`가 됩니다. 

## <a name="azure-machine-learning-jobs"></a>Azure Machine Learning 작업
작업은 계산 작업의 모든 측면을 지정하는 리소스입니다. 작업은 다음 세 가지를 집계합니다.

- 실행할 버전
- 실행 방법
- 실행 위치

SDK, Azure CLI 및 시각적 도구인 스튜디오를 사용하여 Azure Machine Learning 작업을 제출하는 여러 가지 방법이 있습니다. 다음 예제에서는 REST API를 사용하여 LightGBM 학습 작업을 제출합니다.

## <a name="create-machine-learning-assets"></a>기계 학습 자산 만들기

먼저 Azure Machine Learning 자산을 설정하여 작업을 구성합니다.

다음 REST API 호출에서는 `$SUBSCRIPTION_ID`, `$RESOURCE_GROUP`, `$LOCATION` 및 `$WORKSPACE`를 자리 표시자로 사용합니다. 자리 표시자를 고유한 값으로 바꿉니다. 

관리 REST는 [서비스 사용자 인증 토큰](how-to-manage-rest.md#retrieve-a-service-principal-authentication-token)을 요청합니다. `$TOKEN`을 고유한 값으로 바꿉니다. 다음 명령을 사용하여 이 토큰을 검색할 수 있습니다.

```bash
TOKEN=$(az account get-access-token --query accessToken -o tsv)
```

서비스 공급자는 `api-version` 인수를 사용하여 호환성을 보장합니다. `api-version` 인수는 서비스마다 다릅니다. 현재 Azure Machine Learning API 버전은 `2021-03-01-preview`입니다. 이후 버전을 수용할 수 있도록 API 버전을 변수로 설정합니다.

```bash
API_VERSION="2021-03-01-preview"
```

### <a name="compute"></a>Compute

기계 학습 작업을 실행하려면 컴퓨팅 리소스가 필요합니다. 작업 영역의 컴퓨팅 리소스를 나열할 수 있습니다.

```bash
curl "https://management.azure.com/subscriptions/$SUBSCRIPTION_ID/resourceGroups/$RESOURCE_GROUP/providers/Microsoft.MachineLearningServices/workspaces/$WORKSPACE/computes?api-version=$API_VERSION" \
--header "Authorization: Bearer $TOKEN"
```

이 예제에서는 `cpu-cluster`라는 기존 컴퓨팅 클러스터를 사용합니다. 컴퓨팅 이름을 캡슐화의 변수로 설정합니다.

```bash
COMPUTE_NAME="cpu-cluster"
```

> [!TIP]
> [PUT 요청을 사용하여 명명된 컴퓨팅 리소스를 만들거나 덮어쓸 수 있습니다](./how-to-manage-rest.md#create-and-modify-resources-using-put-and-post-requests). 

### <a name="environment"></a>환경 

LightGBM 예제는 LightGBM 환경에서 실행해야 합니다. PUT 요청을 사용하여 환경을 만듭니다. Microsoft Container Registry의 docker 이미지 사용

`Docker`를 사용하여 docker 이미지를 구성하고 `condaFile`을 사용하여 conda 종속성을 추가할 수 있습니다. 

:::code language="rest-api" source="~/azureml-examples-main/cli/train-rest.sh" id="create_environment":::

### <a name="datastore"></a>데이터 저장소

학습 작업은 데이터에서 실행되어야 하므로 데이터 저장소를 지정해야 합니다. 이 예제에서는 작업 영역에 대한 기본 데이터 저장소 및 Azure Storage 계정을 가져옵니다. GET 요청을 통해 작업 영역을 쿼리하여 정보가 포함된 JSON 파일을 반환합니다.

[jq](https://stedolan.github.io/jq/) 도구를 사용하여 JSON 결과를 구문 분석하고 필요한 값을 얻을 수 있습니다. Azure Portal을 사용하여 동일한 정보를 찾을 수도 있습니다.

```bash
response=$(curl --location --request GET "https://management.azure.com/subscriptions/$SUBSCRIPTION_ID/resourceGroups/$RESOURCE_GROUP/providers/Microsoft.MachineLearningServices/workspaces/$WORKSPACE/datastores?api-version=$API_VERSION&isDefault=true" \
--header "Authorization: Bearer $TOKEN")

AZURE_STORAGE_ACCOUNT=$(echo $response | jq '.value[0].properties.contents.accountName')
AZUREML_DEFAULT_DATASTORE=$(echo $response | jq '.value[0].name')
AZUREML_DEFAULT_CONTAINER=$(echo $response | jq '.value[0].properties.contents.containerName')
AZURE_STORAGE_KEY=$(az storage account keys list --account-name $AZURE_STORAGE_ACCOUNT | jq '.[0].value')
```

### <a name="data"></a>데이터

이제 데이터 저장소가 생겼으므로 데이터 세트를 만들 수 있습니다. 이 예제에서는 공통 데이터 세트 `iris.csv`를 사용하고 `path`에서 이 데이터 세트를 가리킵니다. 

:::code language="rest-api" source="~/azureml-examples-main/cli/train-rest.sh" id="create_data":::

### <a name="code"></a>코드

데이터 세트와 데이터 저장소가 생겼으므로 작업에서 실행될 학습 스크립트를 업로드할 수 있습니다. Azure Storage CLI를 사용하여 Blob을 기본 컨테이너에 업로드합니다. Azure Portal 또는 Azure Storage Explorer 같은 다른 방법을 사용하여 업로드할 수도 있습니다.


```bash
az storage blob upload-batch -d $AZUREML_DEFAULT_CONTAINER/src \
 -s jobs/train/lightgbm/iris/src --account-name $AZURE_STORAGE_ACCOUNT --account-key $AZURE_STORAGE_KEY
```

코드를 업로드한 후에는 PUT 요청으로 코드를 지정하고 `datastoreId`를 사용하여 데이터 저장소를 참조할 수 있습니다. 

:::code language="rest-api" source="~/azureml-examples-main/cli/train-rest.sh" id="create_code":::

## <a name="submit-a-training-job"></a>학습 작업 제출

이제 자산이 배치되었으므로, LightGBM 작업을 실행하여 학습된 모델 및 메타데이터를 출력할 수 있습니다. 학습 작업을 구성하려면 다음 정보가 필요합니다. 

- **run_id**: [선택 사항] 모든 작업에서 고유해야 하는 작업의 이름입니다. YAML 파일에서 `name` 필드를 통해 또는 명령줄에서 `--name/-n`을 통해 이름을 지정하지 않으면 GUID/UUID가 자동으로 생성되어 이름에 사용됩니다.
- **jobType**: 작업 유형입니다. 기본 학습 작업에는 `Command`를 사용합니다.
- **codeId**: 학습 스크립트의 경로입니다.
- **command**: 실행할 명령입니다. 입력 데이터는 명령에 쓸 수 있으며 데이터 바인딩을 사용하여 참조할 수 있습니다. 
- **environmentId**: 환경의 경로입니다.
- **inputDataBindings**: 데이터 바인딩을 통해 입력 데이터를 참조할 수 있습니다. 환경 변수를 만듭니다. 그러면 AZURE_ML_INPUT_에 바인딩 이름이 추가되며 이것을 `command`에서 참조할 수 있습니다. 데이터 바인딩을 만들려면 `dataId`로 만든 데이터 경로를 추가해야 합니다. 
- **experimentName**: [선택 사항] 작업에 태그를 지정하면 Azure Machine Learning 스튜디오에서 작업을 구성하는 데 도움이 됩니다. 각 작업의 실행 기록은 스튜디오 "실험" 탭의 해당 실험 아래에 구성됩니다. 생략하면 기본적으로 작업이 생성된 작업 디렉터리의 이름으로 설정됩니다.
- **compute**: `target`은 컴퓨팅 대상을 지정합니다. 컴퓨팅 경로일 수 있습니다. `instanceCount`는 작업에 필요한 인스턴스 수를 지정합니다.

다음 명령을 사용하여 학습 작업을 제출합니다.

:::code language="rest-api" source="~/azureml-examples-main/cli/train-rest.sh" id="create_job":::

## <a name="submit-a-hyperparameter-sweep-job"></a>하이퍼 매개 변수 스윕 작업 제출

Azure Machine Learning에서는 학습 하이퍼 매개 변수를 효율적으로 조정할 수도 있습니다. REST API를 사용하여 하이퍼 매개 변수 튜닝 제품군을 만들 수 있습니다. Azure Machine Learning의 하이퍼 매개 변수 튜닝 옵션에 대한 자세한 내용은 [하이퍼 매개 변수 모델 튜닝](how-to-tune-hyperparameters.md)을 참조하세요. 하이퍼 매개 변수 튜닝 매개 변수를 지정하여 스윕을 구성합니다.

- **jobType**: 작업 유형입니다. 스윕 작업의 경우 `Sweep`입니다. 
- **algorithm**: 샘플링 알고리즘입니다. "random"으로 시작하는 것이 좋은 경우가 자주 있습니다. 옵션의 열거형은 스윕 작업 [스키마](https://azuremlschemas.azureedge.net/latest/sweepJob.schema.json)를 참조하세요. 
- **trial**: 실행할 각 평가판에 대한 명령 작업 구성입니다. 
- **objective**: `primaryMetric`은 학습 코드에서 로그된 메트릭의 이름과 일치해야 하는 최적화 메트릭입니다. `goal`은 방향을 지정합니다(minimize 또는 maximize). 옵션의 전체 열거형은 [스키마](https://azuremlschemas.azureedge.net/latest/sweepJob.schema.json)를 참조하세요. 
- **searchSpace**: 스윕할 하이퍼 매개 변수의 사전입니다. 키는 하이퍼 매개 변수의 이름입니다(예: `learning_rate`). 이 값은 하이퍼 매개 변수 분포입니다. 옵션의 열거형은 [스키마](https://azuremlschemas.azureedge.net/latest/sweepJob.schema.json)를 참조하세요.
- **maxTotalTrials**: 실행할 개별 평가판의 최대 수입니다.
- **maxConcurrentTrials**: [선택 사항] 컴퓨팅 클러스터에서 동시에 실행할 최대 평가판 수입니다.
- **timeout**: [선택 사항] 스윕 작업을 실행할 최대 시간(분)입니다.

동일한 LightGBM 예제를 사용하여 스윕 작업을 만들려면 다음 명령을 사용합니다. 

:::code language="rest-api" source="~/azureml-examples-main/cli/train-rest.sh" id="create_a_sweep_job":::

## <a name="next-steps"></a>다음 단계

이제 학습된 모델이 생겼으므로, [모델을 배포하는 방법](how-to-deploy-and-where.md)을 알아봅니다.
