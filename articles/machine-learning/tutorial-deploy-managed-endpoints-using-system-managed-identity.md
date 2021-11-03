---
title: '자습서: 리소스에 액세스하기 위한 관리형 온라인 엔드포인트'
description: 관리형 온라인 엔드포인트와 시스템이 할당한 관리 ID를 사용하여 기계 학습 모델 배포용 Azure 리소스에 안전하게 액세스합니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: enterprise-readiness
ms.author: seramasu
ms.reviewer: laobri
author: rsethur
ms.date: 08/05/2021
ms.topic: tutorial
ms.custom: tutorial, devplatv2
ms.openlocfilehash: 18e8c09f1d1a734d2e68e656658c3017a115f9a9
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131079342"
---
# <a name="tutorial-access-resources-with-managed-online-endpoints-and-identity-preview"></a>자습서: 관리형 온라인 엔드포인트와 ID(미리 보기)를 사용하여 리소스 액세스

이 자습서에서는 관리형 온라인 엔드포인트와 시스템이 할당한 관리 ID를 사용하여 채점 스크립트에서 Azure 리소스에 안전하게 액세스하는 방법을 알아봅니다.

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]
이 자습서에서는 Azure CLI 및 ML 확장을 사용하여 다음 작업을 수행하는 방법을 보여줍니다.

> [!div class="checklist"]
> * Azure CLI에서 사용할 기본값 설정
> * 관리형 온라인 엔드포인트에서 사용할 변수 구성
> * Blob 스토리지 계정 및 Blob 컨테이너 만들기 
> * 관리형 온라인 엔드포인트 만들기
> * 시스템 할당 관리 ID에 스토리지 액세스 권한 부여
> * 관리형 엔드포인트와 연결된 배포 만들기


## <a name="prerequisites"></a>필수 구성 요소

* Azure 기계 학습을 사용하려면 Azure 구독이 있어야 합니다. Azure 구독이 없는 경우 시작하기 전에 체험 계정을 만듭니다. 지금 [Azure Machine Learning 평가판 또는 유료 버전](https://azure.microsoft.com/free/)을 사용해 보세요.

* Azure CLI 및 ML 확장을 설치하고 구성해야 합니다. 자세한 내용은 [CLI(v2)(미리 보기) 설치, 설정 및 사용](how-to-configure-cli.md)을 참조하세요. 

* Azure 리소스 그룹이 있고, 사용자(또는 사용하는 서비스 주체)에게 이에 대한 `Contributor` 액세스 권한이 있어야 합니다. 위의 문서에 따라 ML 확장을 구성한 경우 이러한 리소스 그룹이 있을 것입니다. 

* Azure Machine Learning 작업 영역이 있어야 합니다. 위의 문서에 따라 ML 확장을 구성한 경우 이러한 작업 영역이 있을 것입니다.

* 채점 및 배포에 사용할 수 있도록 학습된 기계 학습 모델


## <a name="set-the-defaults-for-azure-cli"></a>Azure CLI 기본값 설정

이 자습서 전체에서 올바른 리소스를 사용하려면 사용하려는 Azure 구독 ID, Azure Machine Learning 작업 영역 및 리소스 그룹의 기본값을 설정합니다. 이렇게 하면 Azure CLI 명령을 호출할 때마다 값을 반복적으로 전달하지 않아도 됩니다. 

> [!IMPORTANT]
> 사용자 계정에는 리소스 그룹에 할당된 "사용자 액세스 관리자" 역할이 있어야 합니다. 

```azurecli
az account set --subscription <subscription id>
az configure --defaults workspace=<azureml workspace name> group=<resource group>
```

## <a name="define-the-configuration-yaml-file-for-your-deployment"></a>배포에 대한 구성 YAML 파일 업데이트

관리형 엔드포인트를 배포하려면 먼저 YAML 파일에서 엔드포인트의 구성을 정의해야 합니다.

다음 코드 예제는 다음과 같은 관리형 엔드포인트를 만듭니다.  
* `endpoints/online/managed/managed-identities/` 디렉터리의 YAML 파일을 표시합니다.
* `my-sai-endpoint` 엔드포인트를 참조할 이름을 정의합니다.
* `auth-mode: key` 엔드포인트에 액세스하는 데 사용할 권한 부여 유형을 지정합니다.
* 만들려는 엔드포인트 유형이 `online` 엔드포인트임을 지정합니다. 
* 엔드포인트에 `blue`라는 배포가 연결되었음을 나타냅니다.
* 배포할 모델, 사용할 환경 및 채점 스크립트와 같은 배포 세부 정보를 구성합니다.

:::code language="yaml" source="~/azureml-examples-main/cli/endpoints/online/managed/managed-identities/2-sai-deployment.yml":::

YAML 참조는 [관리형 온라인 엔드포인트(미리 보기) YAML 참조](reference-yaml-endpoint-managed-online.md)를 참조하세요.

## <a name="configure-variables-for-your-deployment"></a>배포에 대한 변수 구성

작업 영역, 작업 영역 위치 및 만들려는 엔드포인트에 대한 변수 이름을 구성합니다. 다음 코드는 이러한 값을 엔드포인트의 환경 변수로 내보냅니다.

::: code language="azurecli" source="~/azureml-examples-main/cli/deploy-managed-online-endpoint-access-resource-sai.sh" id="set_variables" :::

다음으로 Blob 스토리지 계정, Blob 컨테이너 및 파일의 이름을 지정합니다. 이러한 변수 이름은 여기서 정의하며, 다음 섹션의 `az storage account create` 및 `az storage container create` 명령에서 참조됩니다.

다음 코드는 이러한 값을 환경 변수로 내보냅니다.

::: code language="azurecli" source="~/azureml-examples-main/cli/deploy-managed-online-endpoint-access-resource-sai.sh" id="configure_storage_names" :::


이러한 변수를 내보낸 후에는 로컬로 텍스트 파일을 만듭니다. 엔드포인트가 배포되면 채점 스크립트는 엔드포인트를 만들 때 생성된 시스템 할당 관리 ID를 사용하여 이 텍스트 파일에 액세스합니다.

## <a name="create-blob-storage-and-container"></a>Blob 스토리지 및 컨테이너 만들기

이 예제에서는 Blob 스토리지 계정 및 Blob 컨테이너를 만든 다음, 이전에 만든 텍스트 파일을 Blob 컨테이너에 업로드합니다. 

먼저 스토리지 계정을 만듭니다. 

::: code language="azurecli" source="~/azureml-examples-main/cli/deploy-managed-online-endpoint-access-resource-sai.sh" id="create_storage_account" :::

다음으로, 스토리지 계정에 Blob 컨테이너를 만듭니다. 

::: code language="azurecli" source="~/azureml-examples-main/cli/deploy-managed-online-endpoint-access-resource-sai.sh" id="create_storage_container" :::

그런 다음, 텍스트 파일을 Blob 컨테이너에 업로드합니다. 

::: code language="azurecli" source="~/azureml-examples-main/cli/deploy-managed-online-endpoint-access-resource-sai.sh" id="upload_file_to_storage" :::

## <a name="create-a-managed-online-endpoint"></a>관리형 온라인 엔드포인트 만들기

다음 코드는 배포를 지정하지 않고 관리형 온라인 엔드포인트를 만듭니다. 배포 만들기는 자습서의 뒷부분에서 수행합니다.

관리형 엔드포인트를 만들 때 기본적으로 엔드포인트에 대한 시스템 할당 관리 ID가 만들어집니다.

>[!IMPORTANT]
> 시스템 할당 관리 ID는 변경이 불가능하며 생성된 후에는 변경할 수 없습니다.

::: code language="azurecli" source="~/azureml-examples-main/cli/deploy-managed-online-endpoint-access-resource-sai.sh" id="create_endpoint" :::

다음을 사용하여 엔드포인트의 상태를 확인합니다. 

::: code language="azurecli" source="~/azureml-examples-main/cli/deploy-managed-online-endpoint-access-resource-sai.sh" id="check_endpoint_Status" :::

문제가 발생하는 경우 [관리형 온라인 엔드포인트 배포 및 채점(미리 보기) 문제 해결](./how-to-troubleshoot-online-endpoints.md)을 참조하세요.

## <a name="give-storage-permission-to-system-assigned-managed-identity"></a>시스템 할당 관리 ID에 스토리지 권한 부여

관리형 엔드포인트에 시스템 할당 관리 ID를 통해 스토리지에 액세스하는 권한을 부여할 수 있습니다. 

엔드포인트에 대해 생성된 시스템 할당 관리 ID를 검색합니다. 

::: code language="azurecli" source="~/azureml-examples-main/cli/deploy-managed-online-endpoint-access-resource-sai.sh" id="get_system_identity" :::

여기서 시스템 할당 관리 ID에 스토리지 액세스 권한을 부여할 수 있습니다.

::: code language="azurecli" source="~/azureml-examples-main/cli/deploy-managed-online-endpoint-access-resource-sai.sh" id="give_permission_to_user_storage_account" :::

## <a name="scoring-script-to-access-azure-resource"></a>Azure 리소스에 액세스하는 채점 스크립트

시스템 할당 관리 ID 토큰을 사용하여 Azure 리소스에 액세스하는 방법을 이해하려면 다음 채점 스크립트를 참조하세요. 이 시나리오에서 Azure 리소스는 이전 섹션에서 만든 스토리지 계정입니다. 

:::code language="python" source="~/azureml-examples-main/cli/endpoints/online/model-1/onlinescoring/score_managedidentity.py":::

## <a name="create-a-deployment-using-your-configuration"></a>구성을 사용하여 배포 만들기

관리형 엔드포인트와 연결된 배포를 만듭니다.

이 배포는 기본 환경/이미지가 처음으로 빌드되는지 여부에 따라 약 8-14분이 걸릴 수 있습니다. 동일한 환경을 사용하는 후속 배포는 더 빨라집니다.

::: code language="azurecli" source="~/azureml-examples-main/cli/deploy-managed-online-endpoint-access-resource-sai.sh" id="deploy" :::

배포 상태를 확인합니다. 

::: code language="azurecli" source="~/azureml-examples-main/cli/deploy-managed-online-endpoint-access-resource-sai.sh" id="check_deploy_Status" :::

> [!NOTE]
> 채점 스크립트의 init 메서드는 시스템 할당 관리 ID 토큰을 사용하여 스토리지 계정에서 파일을 읽습니다. 

init 메서드 출력을 확인하려면 다음 코드를 사용하여 배포 로그를 살펴봅니다. 

::: code language="azurecli" source="~/azureml-examples-main/cli/deploy-managed-online-endpoint-access-resource-sai.sh" id="check_deployment_log" :::

이 명령이 완료되면 모델, 환경 및 엔드포인트가 Azure Machine Learning 작업 영역에 등록됩니다.

### <a name="confirm-your-endpoint-deployed-successfully"></a>엔드포인트가 성공적으로 배포되었는지 확인

엔드포인트가 배포되면 정상적으로 작동하는지 확인합니다. 추론 세부 정보는 모델마다 다릅니다. 이 자습서의 경우 JSON 쿼리 매개 변수는 다음과 같습니다. 

:::code language="json" source="~/azureml-examples-main/cli/endpoints/online/model-1/sample-request.json" :::

엔드포인트를 호출하려면 다음을 실행합니다. 

::: code language="azurecli" source="~/azureml-examples-main/cli/deploy-managed-online-endpoint-access-resource-sai.sh" id="test_endpoint" :::


## <a name="delete-the-endpoint-and-storage-account"></a>엔드포인트 및 스토리지 계정 삭제

배포된 엔드포인트 및 스토리지를 계속 사용할 계획이 없으면 삭제하여 비용을 절감합니다. 엔드포인트를 삭제하면 연결된 모든 배포도 삭제됩니다. 

::: code language="azurecli" source="~/azureml-examples-main/cli/deploy-managed-online-endpoint-access-resource-sai.sh" id="delete_endpoint" :::
::: code language="azurecli" source="~/azureml-examples-main/cli/deploy-managed-online-endpoint-access-resource-sai.sh" id="delete_storage_account" :::

## <a name="next-steps"></a>다음 단계

이 Azure Machine Learning 자습서에서는 기계 학습 CLI를 사용하여 다음 작업을 수행했습니다.

> [!div class="checklist"]
> * Azure CLI에서 사용할 기본값 설정
> * 엔드포인트에서 사용할 변수 구성
> * Blob 스토리지 계정 및 Blob 컨테이너 만들기 
> * 관리형 엔드포인트 만들기
> * 시스템 할당 관리 ID에 스토리지 액세스 권한 부여
> * 관리형 엔드포인트와 연결된 배포 만들기

* CLI 사용에 대한 자세한 내용은 [Azure Machine Learning용 CLI 확장 사용](reference-azure-machine-learning-cli.md)을 참조하세요.
* 특정 데이터만 반환하도록 JSON 쿼리를 구체화하려면 [Azure CLI 명령 출력 쿼리](/cli/azure/query-azure-cli)를 참조하세요.
* YAML 스키마에 대한 자세한 내용은 [온라인 엔드포인트 YAML 참조](reference-yaml-endpoint-managed-online.md) 문서를 참조하세요.
* 사용할 수 있는 컴퓨팅 리소스를 확인하려면 [관리형 온라인 엔드포인트 SKU 목록(미리 보기)](reference-managed-online-endpoints-vm-sku-list.md)을 참조하세요.
* 비용에 대한 자세한 내용은 [Azure Machine Learning 관리형 온라인 엔드포인트(미리 보기)의 비용 보기](how-to-view-online-endpoints-costs.md)를 참조하세요.
* 배포에 대한 자세한 내용은 [온라인 엔드포인트(미리 보기)의 안전한 출시](how-to-safely-rollout-managed-endpoints.md)를 참조하세요.
* 엔드포인트 모니터링에 대한 자세한 내용은 [관리형 온라인 엔드포인트(미리 보기) 모니터링](how-to-monitor-online-endpoints.md)을 참조하세요.
* 관리 엔드포인트의 제한 사항에 대한 자세한 내용은 [Azure Machine Learning을 사용하여 리소스 할당량 관리 및 증대](how-to-manage-quotas.md#azure-machine-learning-managed-online-endpoints-preview)를 참조하세요.