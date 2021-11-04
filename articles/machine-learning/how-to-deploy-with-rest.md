---
title: REST API를 통해 관리형 온라인 엔드포인트를 사용하여 모델 배포(미리 보기)
titleSuffix: Azure Machine Learning
description: REST API를 통해 관리형 온라인 엔드포인트를 사용하여 모델을 배포하는 방법을 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
author: rsethur
ms.author: seramasu
ms.date: 10/21/2021
ms.reviewer: laobri
ms.custom: devplatv2
ms.openlocfilehash: 05622e6300dc19e28efaba114aded16fc1a26788
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131557360"
---
# <a name="deploy-models-with-rest-preview"></a>REST를 사용하여 모델 배포(미리 보기)

Azure Machine Learning REST API를 사용하여 모델을 배포하는 방법을 알아봅니다(미리 보기).

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

## <a name="set-endpoint-name"></a>엔드포인트 이름 설정

> [!NOTE]
> 엔드포인트 이름은 Azure 지역 수준에서 고유해야 합니다. 예를 들어 westus2에는 my-endpoint라는 엔드포인트가 하나만 있을 수 있습니다.

:::code language="rest-api" source="~/azureml-examples-main/cli/deploy-rest.sh" id="set_endpoint_name":::

## <a name="azure-machine-learning-managed-online-endpoints"></a>Azure Machine Learning 관리형 온라인 엔드포인트
관리형 온라인 엔드포인트(미리 보기)를 사용하면 기본 인프라를 만들고 관리할 필요 없이 모델을 배포할 수 있습니다. 이 문서에서는 온라인 엔드포인트 및 배포를 만들고 호출하여 유효성을 검사합니다. 그러나 먼저 모델, 코드 및 환경을 포함하여 배포에 필요한 자산을 등록해야 합니다.

[Azure CLI를 포함](how-to-deploy-managed-online-endpoints.md)하고 [Studio](how-to-use-managed-online-endpoint-studio.md)를 사용하여 시각적으로 Azure Machine Learning 온라인 엔드포인트를 만드는 방법에는 여러 가지가 있습니다. 다음 예제에서는 REST API를 사용하여 관리형 온라인 엔드포인트를 만듭니다.

## <a name="create-machine-learning-assets"></a>기계 학습 자산 만들기

먼저 Azure Machine Learning 자산을 설정하여 작업을 구성합니다.

다음 REST API 호출에서는 `SUBSCRIPTION_ID`, `RESOURCE_GROUP`, `LOCATION` 및 `WORKSPACE`를 자리 표시자로 사용합니다. 자리 표시자를 고유한 값으로 바꿉니다. 

관리 REST는 [서비스 사용자 인증 토큰](how-to-manage-rest.md#retrieve-a-service-principal-authentication-token)을 요청합니다. `TOKEN`을 고유한 값으로 바꿉니다. 다음 명령을 사용하여 이 토큰을 검색할 수 있습니다.

:::code language="rest-api" source="~/azureml-examples-cli-preview/cli/deploy-rest.sh" id="get_access_token":::

서비스 공급자는 `api-version` 인수를 사용하여 호환성을 보장합니다. `api-version` 인수는 서비스마다 다릅니다. 이후 버전을 수용할 수 있도록 API 버전을 변수로 설정합니다.

:::code language="rest-api" source="~/azureml-examples-cli-preview/cli/deploy-rest.sh" id="api_version":::

### <a name="get-storage-account-details"></a>스토리지 계정 세부 정보 가져오기

모델 및 코드를 등록하려면 먼저 스토리지 계정에 업로드해야 합니다. 스토리지 계정의 세부 정보는 데이터 저장소에서 확인할 수 있습니다. 이 예제에서는 작업 영역에 대한 기본 데이터 저장소 및 Azure Storage 계정을 가져옵니다. GET 요청을 통해 작업 영역을 쿼리하여 정보가 포함된 JSON 파일을 가져옵니다.

[jq](https://stedolan.github.io/jq/) 도구를 사용하여 JSON 결과를 구문 분석하고 필요한 값을 얻을 수 있습니다. Azure Portal을 사용하여 동일한 정보를 찾을 수도 있습니다.

:::code language="rest-api" source="~/azureml-examples-main/cli/deploy-rest.sh" id="get_storage_details":::

### <a name="upload--register-code"></a>코드 업로드 및 등록

이제 데이터 저장소가 생겼으므로 채점 스크립트를 업로드할 수 있습니다. Azure Storage CLI를 사용하여 Blob을 기본 컨테이너에 업로드합니다.

:::code language="rest-api" source="~/azureml-examples-main/cli/deploy-rest.sh" id="upload_code":::

> [!TIP]
> Azure Portal 또는 [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/)와 같은 다른 방법을 사용하여 업로드할 수도 있습니다.

코드를 업로드한 후에는 PUT 요청으로 코드를 지정하고 `datastoreId`를 사용하여 데이터 저장소를 참조할 수 있습니다.

:::code language="rest-api" source="~/azureml-examples-main/cli/deploy-rest.sh" id="create_code":::

### <a name="upload-and-register-model"></a>모델 업로드 및 등록

코드와 마찬가지로 모델 파일을 업로드합니다.

:::code language="rest-api" source="~/azureml-examples-main/cli/deploy-rest.sh" id="upload_model":::

이제 모델을 등록합니다.

:::code language="rest-api" source="~/azureml-examples-main/cli/deploy-rest.sh" id="create_model":::

### <a name="create-environment"></a>환경 만들기
필요한 종속성이 있는 환경에서 배포를 실행해야 합니다. PUT 요청을 사용하여 환경을 만듭니다. Microsoft Container Registry의 docker 이미지 사용 `Docker`를 사용하여 docker 이미지를 구성하고 `condaFile`을 사용하여 conda 종속성을 추가할 수 있습니다.

다음 코드 조각에서는 Conda 환경의 콘텐츠(YAML 파일)를 환경 변수로 읽었습니다.

:::code language="rest-api" source="~/azureml-examples-main/cli/deploy-rest.sh" id="create_environment":::

### <a name="create-endpoint"></a>엔드포인트 만들기

온라인 엔드포인트를 만듭니다.

:::code language="rest-api" source="~/azureml-examples-main/cli/deploy-rest.sh" id="create_endpoint":::

### <a name="create-deployment"></a>배포 만들기

엔드포인트 아래에 배포를 만듭니다.

:::code language="rest-api" source="~/azureml-examples-main/cli/deploy-rest.sh" id="create_deployment":::

### <a name="invoke-the-endpoint-to-score-data-with-your-model"></a>엔드포인트를 호출하여 모델로 데이터 채점

엔드포인트를 호출하려면 채점 URI와 액세스 토큰이 필요합니다. 먼저 채점 URI를 가져옵니다.

:::code language="rest-api" source="~/azureml-examples-main/cli/deploy-rest.sh" id="get_endpoint":::

엔드포인트 액세스 토큰 가져오기:

:::code language="rest-api" source="~/azureml-examples-main/cli/deploy-rest.sh" id="get_access_token":::

이제 curl을 사용하여 엔드포인트를 호출합니다.

:::code language="rest-api" source="~/azureml-examples-main/cli/deploy-rest.sh" id="score_endpoint":::

### <a name="check-the-logs"></a>로그 확인

배포 로그를 확인합니다.

:::code language="rest-api" source="~/azureml-examples-main/cli/deploy-rest.sh" id="get_deployment_logs":::

### <a name="delete-the-endpoint"></a>엔드포인트 삭제

배포를 사용하지 않을 경우 아래 명령을 사용하여 배포를 삭제해야 합니다(엔드포인트 및 모든 기본 배포 삭제).

:::code language="rest-api" source="~/azureml-examples-main/cli/deploy-rest.sh" id="delete_endpoint":::

## <a name="next-steps"></a>다음 단계

* [Azure CLI를 사용](how-to-deploy-managed-online-endpoints.md)하여 모델을 배포하는 방법을 알아봅니다.
* [스튜디오를 사용](how-to-use-managed-online-endpoint-studio.md)하여 모델을 배포하는 방법을 알아봅니다.
* [관리형 온라인 엔드포인트 배포 및 채점(미리 보기) 문제 해결](how-to-troubleshoot-managed-online-endpoints.md)에 대해 알아봅니다.
* [관리 되는 온라인 끝점 및 관리 id (미리 보기)를 사용 하 여 Azure 리소스에 액세스](how-to-access-resources-from-endpoints-managed-identities.md) 하는 방법을 알아봅니다.
* [온라인 엔드포인트를 모니터링](how-to-monitor-online-endpoints.md)하는 방법을 알아봅니다.
* [온라인 엔드포인트에 대한 안전한 롤아웃(미리 보기)](how-to-safely-rollout-managed-endpoints.md)에 대해 알아봅니다.
* [Azure Machine Learning 관리형 온라인 엔드포인트 비용 보기(미리보기)](how-to-view-online-endpoints-costs.md).
* [관리형 온라인 엔드포인트 SKU 목록(미리 보기)](reference-managed-online-endpoints-vm-sku-list.md)
* [Azure Machine Learning으로 리소스 할당량 관리 및 늘리기](how-to-manage-quotas.md#azure-machine-learning-managed-online-endpoints-preview)에서 관리형 온라인 엔드포인트에 대한 제한에 대해 알아보세요.