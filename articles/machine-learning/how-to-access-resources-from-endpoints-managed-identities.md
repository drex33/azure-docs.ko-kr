---
title: 관리되는 엔드포인트에서 Azure 리소스에 액세스
titleSuffix: Azure Machine Learning
description: 시스템 할당 또는 사용자 할당 관리 ID를 통해 관리되는 온라인 엔드포인트에서 기계 학습 모델 배포를 위한 Azure 리소스에 안전하게 액세스합니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: seramasu
ms.reviewer: laobri
author: rsethur
ms.date: 10/21/2021
ms.topic: how-to
ms.custom: devplatv2
ms.openlocfilehash: ff8778dfa69552deaf26dd13438ac73fed260658
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131570084"
---
# <a name="access-azure-resources-from-a-managed-online-endpoint-preview-with-a-managed-identity"></a>관리 ID를 통해 관리되는 온라인 엔드포인트(미리 보기)에서 Azure 리소스에 액세스 

관리형 온라인 엔드포인트와 시스템 할당 관리 ID 또는 사용자 할당 관리 ID를 사용하여 점수 매기기 스크립트에서 Azure 리소스에 액세스하는 방법을 알아봅니다. 

관리형 엔드포인트(미리 보기)를 사용하면 Azure Machine Learning 컴퓨팅 리소스를 프로비전하고 기계 학습 모델을 배포해야 하는 부담을 관리할 수 있습니다. 일반적으로 모델은 추론을 위해 Azure Container Registry 또는 Blob Storage와 같은 Azure 리소스에 액세스해야 합니다. 관리 ID를 사용하면 코드에서 자격 증명을 관리할 필요 없이 이러한 리소스에 액세스할 수 있습니다. [관리 ID 에 대해 자세히 알아보세요.](../active-directory/managed-identities-azure-resources/overview.md)

이 가이드에서는 관리 ID, 스토리지 계정 또는 관리되는 온라인 엔드포인트가 없다고 가정합니다. 이러한 구성 요소가 이미 있는 경우 [관리 ID에 대한 액세스 권한 부여 섹션으로](#give-access-permission-to-the-managed-identity) 건너뜁니다. 

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

## <a name="prerequisites"></a>필수 구성 요소

* Azure Machine Learning을 사용하려면 Azure 구독이 있어야 합니다. Azure 구독이 없는 경우 시작하기 전에 체험 계정을 만듭니다. 지금 [Azure Machine Learning 평가판 또는 유료 버전](https://azure.microsoft.com/free/)을 사용해 보세요.

* Azure CLI 및 ML 확장을 설치하고 구성합니다. 자세한 내용은 [2.0 CLI(미리 보기) 설치, 설정 및 사용](how-to-configure-cli.md)을 참조하세요.

* 사용자(또는 사용하는 서비스 주체)에게 및 액세스 권한이 있어야 하는 Azure 리소스 `User Access Administrator`  `Contributor` 그룹입니다. 위의 문서에 따라 ML 확장을 구성한 경우 이러한 리소스 그룹이 있을 것입니다.

* Azure Machine Learning 작업 영역 위 문서에 따라 ML 확장을 구성한 경우 작업 영역이 있습니다.

* 채점 및 배포에 사용할 수 있도록 학습된 기계 학습 모델 샘플과 함께 팔로우하는 경우 모델이 제공됩니다.

* Azure CLI에 대한 기본값을 아직 설정하지 않은 경우 기본 설정을 저장합니다. 구독, 작업 영역 및 리소스 그룹에 대한 값을 여러 번 전달하지 않으려면 다음 코드를 실행하세요.

   ```azurecli
   az account set --subscription <subscription ID>
   az configure --defaults workspace=<Azure Machine Learning workspace name> group=<resource group>
   ```

* 샘플을 따라 하려면 샘플 리포지토리를 복제합니다.

    ```azurecli
    git clone https://github.com/Azure/azureml-examples --depth 1
    cd azureml-examples/cli
    ```

## <a name="define-configuration-yaml-file-for-deployment"></a>배포를 위한 구성 YAML 파일 정의

CLI를 사용하여 관리형 엔드포인트를 배포하려면 YAML 파일에서 구성을 정의해야 합니다. YAML 스키마에 대한 자세한 내용은 [온라인 엔드포인트 YAML 참조](reference-yaml-endpoint-managed-online.md) 문서를 참조하세요.

다음 예제의 YAML 파일은 온라인 엔드포인트를 만드는 데 사용됩니다. 

# <a name="system-assigned-managed-identity"></a>[시스템 할당 관리 ID](#tab/system-identity)

다음 YAML 예제는 에 `endpoints/online/managed/managed-identities/1-sai-create-endpoint` 있습니다. 파일, 

* 엔드포인트 를 참조할 이름을 `my-sai-endpoint` 정의합니다.
* 엔드포인트에 액세스하는 데 사용할 권한 부여 `auth-mode: key` 유형()을 지정합니다.

:::code language="yaml" source="~/azureml-examples-cli-preview/cli/endpoints/online/managed/managed-identities/1-sai-create-endpoint.yml":::

이 YAML 예제, `2-sai-deployment.yml` ,

* 만들려는 엔드포인트 유형이 `online` 엔드포인트임을 지정합니다.
* 엔드포인트에 라는 연결된 배포가 있음을 `blue` 나타냅니다.
* 배포할 모델, 사용할 환경 및 채점 스크립트와 같은 배포 세부 정보를 구성합니다.

:::code language="yaml" source="~/azureml-examples-cli-preview/cli/endpoints/online/managed/managed-identities/2-sai-deployment.yml":::

# <a name="user-assigned-managed-identity"></a>[사용자 할당 관리 ID](#tab/user-identity)

다음 YAML 예제는 에 `endpoints/online/managed/managed-identities/1-uai-create-endpoint` 있습니다. 파일, 

* 엔드포인트 를 참조할 이름을 `my-uai-endpoint` 정의합니다.
* 엔드포인트에 액세스하는 데 사용할 권한 부여 `auth-mode: key` 유형()을 지정합니다.
* 사용할 ID 형식을 나타냅니다. `type: user_assigned`

:::code language="yaml" source="~/azureml-examples-cli-preview/cli/endpoints/online/managed/managed-identities/1-uai-create-endpoint.yml":::

이 YAML 예제, `2-sai-deployment.yml` ,

* 만들려는 엔드포인트 유형이 `online` 엔드포인트임을 지정합니다.
* 엔드포인트에 라는 연결된 배포가 있음을 `blue` 나타냅니다.
* 배포할 모델, 사용할 환경 및 채점 스크립트와 같은 배포 세부 정보를 구성합니다.

:::code language="yaml" source="~/azureml-examples-cli-preview/cli/endpoints/online/managed/managed-identities/2-uai-deployment.yml":::

---

## <a name="configure-variables-for-deployment"></a>배포를 위한 변수 구성

배포에 사용하기 위해 만들려는 작업 영역, 작업 영역 위치 및 엔드포인트에 대한 변수 이름을 구성합니다.

# <a name="system-assigned-managed-identity"></a>[시스템 할당 관리 ID](#tab/system-identity)

다음 코드는 이러한 값을 엔드포인트의 환경 변수로 내보냅니다.

::: code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-managed-online-endpoint-access-resource-sai.sh" id="set_variables" :::

다음으로 Blob 스토리지 계정, Blob 컨테이너 및 파일의 이름을 지정합니다. 이러한 변수 이름은 여기서 정의하며, 다음 섹션의 `az storage account create` 및 `az storage container create` 명령에서 참조됩니다.

다음 코드는 이러한 값을 환경 변수로 내보냅니다.

::: code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-managed-online-endpoint-access-resource-sai.sh" id="configure_storage_names" :::

이러한 변수를 내보낸 후에는 로컬로 텍스트 파일을 만듭니다. 엔드포인트가 배포되면 점수 매기기 스크립트는 엔드포인트를 만들 때 생성되는 시스템 할당 관리 ID를 사용하여 이 텍스트 파일에 액세스합니다.

# <a name="user-assigned-managed-identity"></a>[사용자 할당 관리 ID](#tab/user-identity)

엔드포인트, 작업 영역, 작업 영역 위치의 이름을 결정하고 해당 값을 환경 변수로 내보냅니다.

::: code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-managed-online-endpoint-access-resource-uai.sh" id="set_variables" :::

다음으로 Blob 스토리지 계정, Blob 컨테이너 및 파일의 이름을 지정합니다. 이러한 변수 이름은 여기서 정의하며, 다음 섹션의 `az storage account create` 및 `az storage container create` 명령에서 참조됩니다.

::: code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-managed-online-endpoint-access-resource-uai.sh" id="configure_storage_names" :::

이러한 변수를 내보낸 후에는 로컬로 텍스트 파일을 만듭니다. 엔드포인트가 배포되면 점수 매기기 스크립트는 엔드포인트에서 사용되는 사용자 할당 관리 ID를 사용하여 이 텍스트 파일에 액세스합니다. 

사용자 ID 이름의 이름을 결정하고 해당 값을 환경 변수로 내보냅니다.

::: code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-managed-online-endpoint-access-resource-uai.sh" id="set_user_identity_name" :::

---

## <a name="create-the-managed-identity"></a>관리 ID 만들기 
Azure 리소스에 액세스하려면 엔드포인트에 대한 시스템 할당 또는 사용자 할당 관리 ID를 만듭니다. 

# <a name="system-assigned-managed-identity"></a>[시스템 할당 관리 ID](#tab/system-identity)

온라인 [엔드포인트 를 만들](#create-a-managed-online-endpoint)때 시스템 할당 관리 ID가 자동으로 생성되므로 별도의 ID를 만들 필요가 없습니다. 

# <a name="user-assigned-managed-identity"></a>[사용자 할당 관리 ID](#tab/user-identity)

사용자 할당 관리 ID를 만들려면 다음을 사용합니다.

::: code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-managed-online-endpoint-access-resource-uai.sh" id="create_user_identity" :::

---

## <a name="create-storage-account-and-container"></a>스토리지 계정 및 컨테이너 만들기

이 예제에서는 Blob Storage 계정 및 Blob 컨테이너를 만든 다음 이전에 만든 텍스트 파일을 Blob 컨테이너에 업로드합니다. 엔드포인트 및 관리 ID 액세스 권한을 부여할 스토리지 계정 및 Blob 컨테이너입니다. 

# <a name="system-assigned-managed-identity"></a>[시스템 할당 관리 ID](#tab/system-identity)

먼저 스토리지 계정을 만듭니다.

::: code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-managed-online-endpoint-access-resource-sai.sh" id="create_storage_account" :::

다음으로, 스토리지 계정에 Blob 컨테이너를 만듭니다.

::: code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-managed-online-endpoint-access-resource-sai.sh" id="create_storage_container" :::

그런 다음, 텍스트 파일을 Blob 컨테이너에 업로드합니다.

::: code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-managed-online-endpoint-access-resource-sai.sh" id="upload_file_to_storage" :::

# <a name="user-assigned-managed-identity"></a>[사용자 할당 관리 ID](#tab/user-identity)

먼저 스토리지 계정을 만듭니다.  

::: code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-managed-online-endpoint-access-resource-uai.sh" id="create_storage_account" :::

다음을 사용하여 기존 스토리지 계정 ID를 검색할 수도 있습니다. 

::: code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-managed-online-endpoint-access-resource-uai.sh" id="get_storage_account_id" :::

다음으로, 스토리지 계정에 Blob 컨테이너를 만듭니다. 

::: code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-managed-online-endpoint-access-resource-uai.sh" id="create_storage_container" :::

그런 다음, 컨테이너에 파일을 업로드합니다. 

::: code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-managed-online-endpoint-access-resource-uai.sh" id="upload_file_to_storage" :::

---

## <a name="create-a-managed-online-endpoint"></a>관리형 온라인 엔드포인트 만들기

다음 코드는 배포를 지정하지 않고 관리형 온라인 엔드포인트를 만듭니다. 

# <a name="system-assigned-managed-identity"></a>[시스템 할당 관리 ID](#tab/system-identity)
관리형 엔드포인트를 만들 때 기본적으로 엔드포인트에 대한 시스템 할당 관리 ID가 만들어집니다.

>[!IMPORTANT]
> 시스템 할당 관리 ID는 변경이 불가능하며 생성된 후에는 변경할 수 없습니다.

::: code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-managed-online-endpoint-access-resource-sai.sh" id="create_endpoint" :::

다음을 사용하여 엔드포인트의 상태를 확인합니다.

::: code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-managed-online-endpoint-access-resource-sai.sh" id="check_endpoint_Status" :::

문제가 발생하는 경우 [관리형 온라인 엔드포인트 배포 및 채점(미리 보기) 문제 해결](how-to-troubleshoot-managed-online-endpoints.md)을 참조하세요.

# <a name="user-assigned-managed-identity"></a>[사용자 할당 관리 ID](#tab/user-identity)

::: code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-managed-online-endpoint-access-resource-uai.sh" id="create_endpoint" :::

다음을 사용하여 엔드포인트의 상태를 확인합니다.

::: code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-managed-online-endpoint-access-resource-uai.sh" id="check_endpoint_Status" :::

문제가 발생하는 경우 [관리형 온라인 엔드포인트 배포 및 채점(미리 보기) 문제 해결](how-to-troubleshoot-managed-online-endpoints.md)을 참조하세요.

---

## <a name="give-access-permission-to-the-managed-identity"></a>관리 ID에 대한 액세스 권한 부여

>[!IMPORTANT] 
> 온라인 엔드포인트에는 컨테이너 레지스트리에 대한 Azure Container Registry 끌어오기 권한인 AcrPull 권한과 작업 영역의 기본 데이터 저장소에 대한 Blob 데이터 판독기 Storage 권한이 필요합니다.

관리 엔드포인트 권한이 시스템 할당 관리 ID를 통해 스토리지에 액세스하도록 허용하거나 이전 섹션에서 만든 스토리지 계정에 액세스할 수 있는 권한을 사용자 할당 관리 ID에 부여할 수 있습니다.

# <a name="system-assigned-managed-identity"></a>[시스템 할당 관리 ID](#tab/system-identity)

엔드포인트에 대해 만들어진 시스템 할당 관리 ID를 검색합니다.

::: code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-managed-online-endpoint-access-resource-sai.sh" id="get_system_identity" :::

여기서 시스템 할당 관리 ID에 스토리지 액세스 권한을 부여할 수 있습니다.

::: code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-managed-online-endpoint-access-resource-sai.sh" id="give_permission_to_user_storage_account" :::

# <a name="user-assigned-managed-identity"></a>[사용자 할당 관리 ID](#tab/user-identity)

사용자 할당 관리 ID 클라이언트 ID를 검색합니다.

::: code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-managed-online-endpoint-access-resource-uai.sh" id="get_user_identity_client_id" :::

사용자 할당 관리 ID를 검색합니다.

::: code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-managed-online-endpoint-access-resource-uai.sh" id="get_user_identity_id" :::

작업 영역과 연결된 컨테이너 레지스트리를 가져옵니다.

::: code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-managed-online-endpoint-access-resource-uai.sh" id="get_container_registry_id" :::

작업 영역의 기본 스토리지를 검색합니다.

::: code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-managed-online-endpoint-access-resource-uai.sh" id="get_workspace_storage_id" :::

사용자가 할당한 관리 ID에 스토리지 계정의 권한을 부여합니다.  

::: code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-managed-online-endpoint-access-resource-uai.sh" id="give_permission_to_user_storage_account" :::

사용자가 할당한 관리 ID에 컨테이너 레지스트리의 권한을 부여합니다.

::: code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-managed-online-endpoint-access-resource-uai.sh" id="give_permission_to_container_registry" :::

사용자가 할당한 관리 ID에 기본 작업 영역 스토리지 권한을 부여합니다.

::: code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-managed-online-endpoint-access-resource-uai.sh" id="give_permission_to_workspace_storage_account" :::

---

## <a name="scoring-script-to-access-azure-resource"></a>Azure 리소스에 액세스하는 채점 스크립트

ID 토큰을 사용하여 Azure 리소스에 액세스하는 방법(이 시나리오에서는 이전 섹션에서 만든 스토리지 계정)을 이해하려면 다음 스크립트를 참조하세요. 

:::code language="python" source="~/azureml-examples-cli-preview/cli/endpoints/online/model-1/onlinescoring/score_managedidentity.py":::

## <a name="create-a-deployment-with-your-configuration"></a>구성을 사용하여 배포 만들기

관리형 엔드포인트와 연결된 배포를 만듭니다. [관리되는 온라인 엔드포인트에 배포하는 방법에 대해 자세히 알아봅니다.](how-to-deploy-managed-online-endpoints.md)

>[!WARNING]
> 이 배포는 기본 환경/이미지가 처음으로 빌드되는지 여부에 따라 약 8-14분이 걸릴 수 있습니다. 동일한 환경을 사용하는 후속 배포는 더 빨라집니다.

# <a name="system-assigned-managed-identity"></a>[시스템 할당 관리 ID](#tab/system-identity)

::: code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-managed-online-endpoint-access-resource-sai.sh" id="deploy" :::

>[!NOTE]
> 인수 값은 `--name` `name` YAML 파일 내의 키를 재정의할 수 있습니다.

배포 상태를 확인합니다.

::: code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-managed-online-endpoint-access-resource-sai.sh" id="check_deploy_Status" :::

특정 데이터만 반환하도록 위의 쿼리를 구체화하려면 [쿼리 Azure CLI 명령 출력을](/cli/azure/query-azure-cli)참조하세요.

> [!NOTE]
> 채점 스크립트의 init 메서드는 시스템 할당 관리 ID 토큰을 사용하여 스토리지 계정에서 파일을 읽습니다.

init 메서드 출력을 확인하려면 다음 코드를 사용하여 배포 로그를 살펴봅니다. 

::: code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-managed-online-endpoint-access-resource-sai.sh" id="check_deployment_log" :::


# <a name="user-assigned-managed-identity"></a>[사용자 할당 관리 ID](#tab/user-identity)

::: code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-managed-online-endpoint-access-resource-uai.sh" id="create_endpoint" :::

>[!Note]
> 인수 값은 `--name` `name` YAML 파일 내의 키를 재정의할 수 있습니다.

명령이 실행되면 배포 상태를 확인할 수 있습니다.

::: code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-managed-online-endpoint-access-resource-uai.sh" id="check_endpoint_Status" :::

특정 데이터만 반환하도록 위의 쿼리를 구체화하려면 [쿼리 Azure CLI 명령 출력을](/cli/azure/query-azure-cli)참조하세요.

::: code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-managed-online-endpoint-access-resource-uai.sh" id="check_deployment_log" :::

> [!NOTE]
> 채점 스크립트의 init 메서드는 시스템 할당 관리 ID 토큰을 사용하여 스토리지 계정에서 파일을 읽습니다.

init 메서드 출력을 확인하려면 다음 코드를 사용하여 배포 로그를 살펴봅니다. 

::: code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-managed-online-endpoint-access-resource-uai.sh" id="check_deployment_log" :::

---

배포가 완료되면 모델, 환경 및 엔드포인트가 Azure Machine Learning 작업 영역에 등록됩니다.

## <a name="confirm-your-endpoint-deployed-successfully"></a>엔드포인트가 성공적으로 배포되었는지 확인

엔드포인트가 배포되면 정상적으로 작동하는지 확인합니다. 추론 세부 정보는 모델마다 다릅니다. 이 가이드의 경우 JSON 쿼리 매개 변수는 다음과 같습니다. 

:::code language="json" source="~/azureml-examples-cli-preview/cli/endpoints/online/model-1/sample-request.json" :::

엔드포인트를 호출하려면 다음을 실행합니다.

# <a name="system-assigned-managed-identity"></a>[시스템 할당 관리 ID](#tab/system-identity)

::: code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-managed-online-endpoint-access-resource-sai.sh" id="test_endpoint" :::

# <a name="user-assigned-managed-identity"></a>[사용자 할당 관리 ID](#tab/user-identity)

::: code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-managed-online-endpoint-access-resource-uai.sh" id="test_endpoint" :::

---

## <a name="delete-the-endpoint-and-storage-account"></a>엔드포인트 및 스토리지 계정 삭제

배포된 엔드포인트 및 스토리지를 계속 사용할 계획이 없으면 삭제하여 비용을 절감합니다. 엔드포인트를 삭제하면 연결된 모든 배포도 삭제됩니다.

# <a name="system-assigned-managed-identity"></a>[시스템 할당 관리 ID](#tab/system-identity)
 
::: code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-managed-online-endpoint-access-resource-sai.sh" id="delete_endpoint" :::
::: code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-managed-online-endpoint-access-resource-sai.sh" id="delete_storage_account" :::

# <a name="user-assigned-managed-identity"></a>[사용자 할당 관리 ID](#tab/user-identity)

::: code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-managed-online-endpoint-access-resource-uai.sh" id="delete_endpoint" :::
::: code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-managed-online-endpoint-access-resource-uai.sh" id="delete_storage_account" :::
::: code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-managed-online-endpoint-access-resource-uai.sh" id="delete_user_identity" :::

---

## <a name="next-steps"></a>다음 단계

* [관리형 온라인 엔드포인트(미리 보기)를 사용하여 기계 학습 모델을 배포하고 점수](how-to-deploy-managed-online-endpoints.md)매기기
* 배포에 대한 자세한 내용은 [온라인 엔드포인트(미리 보기)의 안전한 출시](how-to-safely-rollout-managed-endpoints.md)를 참조하세요.
* CLI 사용에 대한 자세한 내용은 [Azure Machine Learning용 CLI 확장 사용](reference-azure-machine-learning-cli.md)을 참조하세요.
* 사용할 수 있는 컴퓨팅 리소스를 확인하려면 [관리형 온라인 엔드포인트 SKU 목록(미리 보기)](reference-managed-online-endpoints-vm-sku-list.md)을 참조하세요.
* 비용에 대한 자세한 내용은 [Azure Machine Learning 관리형 온라인 엔드포인트(미리 보기)의 비용 보기](how-to-view-online-endpoints-costs.md)를 참조하세요.
* 엔드포인트 모니터링에 대한 자세한 내용은 [관리형 온라인 엔드포인트(미리 보기) 모니터링](how-to-monitor-online-endpoints.md)을 참조하세요.
* 관리되는 엔드포인트에 대한 제한은 [Azure Machine Learning 리소스에 대한 할당량 관리 및 증가를](how-to-manage-quotas.md#azure-machine-learning-managed-online-endpoints-preview)참조하세요.
