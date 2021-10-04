---
title: 웹 서비스에 Azure AD ID 사용
titleSuffix: Azure Machine Learning
description: 채점 중에 Azure Kubernetes Service의 웹 서비스에 Azure AD ID를 사용하여 클라우드 리소스에 액세스합니다.
services: machine-learning
ms.author: larryfr
author: BlackMist
ms.reviewer: aashishb
ms.service: machine-learning
ms.subservice: enterprise-readiness
ms.date: 11/16/2020
ms.topic: how-to
ms.openlocfilehash: 2ebc7681ebe0c3ed8ea0aa5045d554ebdf5f1fd7
ms.sourcegitcommit: f29615c9b16e46f5c7fdcd498c7f1b22f626c985
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/04/2021
ms.locfileid: "129425135"
---
# <a name="use-azure-ad-identity-with-your-machine-learning-web-service-in-azure-kubernetes-service"></a>Azure Kubernetes Service에서 Machine Learning 웹 서비스와 함께 Azure AD ID 사용

이 문서에서는 Azure Kubernetes Service에서 배포된 기계 학습 모델에 Azure AD(Azure Active Directory) ID를 할당하는 방법에 대해 알아봅니다. [Azure AD Pod ID](https://github.com/Azure/aad-pod-identity) 프로젝트를 사용하면 애플리케이션에서 [관리 ID](../active-directory/managed-identities-azure-resources/overview.md) 및 Kubernetes 기본 형식을 사용하여 Azure AD를 통해 안전하게 클라우드 리소스에 액세스할 수 있습니다. 이렇게 하면 `score.py` 스크립트 내에 직접 자격 증명을 포함하거나 해당 스크립트에서 토큰을 관리할 필요 없이 웹 서비스가 Azure 리소스에 안전하게 액세스할 수 있습니다. 이 문서에서는 Azure Kubernetes Service 클러스터에서 Azure ID를 만들고 설치하고, 배포된 웹 서비스에 ID를 할당하는 단계를 설명합니다.

## <a name="prerequisites"></a>필수 구성 요소

- [Machine Learning 서비스용 Azure CLI 확장](reference-azure-machine-learning-cli.md), [Python용 Azure Machine Learning SDK](/python/api/overview/azure/ml/intro) 또는 [Azure Machine Learning Visual Studio Code 확장](how-to-setup-vs-code.md).

- `kubectl` 명령을 사용하여 AKS 클러스터에 액세스합니다. 자세한 내용은 [클러스터에 연결](../aks/kubernetes-walkthrough.md#connect-to-the-cluster)을 참조하세요.

- AKS 클러스터에 배포된 Azure Machine Learning 웹 서비스.

## <a name="create-and-install-an-azure-identity"></a>Azure ID 만들기 및 설치

1. AKS 클러스터에서 Kubernetes RBAC를 사용하고 있는지 확인하려면 다음 명령을 사용합니다.

    ```azurecli-interactive
    az aks show --name <AKS cluster name> --resource-group <resource group name> --subscription <subscription id> --query enableRbac
    ```

    이 명령은 Kubernetes RBAC를 사용하고 있는 경우 `true` 값을 반환합니다. 이 값에 따라 다음 단계에서 사용할 명령이 결정됩니다.

1. AKS 클러스터에 [Azure AD Pod ID](https://azure.github.io/aad-pod-identity/docs/getting-started/installation/)를 설치합니다.

1. Azure AD Pod ID 프로젝트 페이지에 표시된 단계에 따라 [Azure에서 ID를 만듭니다](https://azure.github.io/aad-pod-identity/docs/demo/standard_walkthrough/#2-create-an-identity-on-azure).

1. Azure AD Pod ID 프로젝트 페이지에 표시된 단계에 따라 [AzureIdentity를 배포합니다](https://azure.github.io/aad-pod-identity/docs/demo/standard_walkthrough/#3-deploy-azureidentity).

1. Azure AD Pod ID 프로젝트 페이지에 표시된 단계에 따라 [AzureIdentityBinding을 배포합니다](https://azure.github.io/aad-pod-identity/docs/demo/standard_walkthrough/#5-deploy-azureidentitybinding).

1. 이전 단계에서 만든 Azure ID가 AKS 클러스터의 동일한 노드 리소스 그룹에 없는 경우 Azure AD Pod ID 프로젝트 페이지에 표시된 [역할 할당](https://azure.github.io/aad-pod-identity/docs/getting-started/role-assignment/#user-assigned-identities-that-are-not-within-the-node-resource-group) 단계를 따릅니다.

## <a name="assign-azure-identity-to-web-service"></a>웹 서비스에 Azure ID 할당

다음 단계에서는 이전 섹션에서 만든 Azure ID를 사용하고 **선택기 레이블** 을 통해 AKS 웹 서비스에 할당합니다.

먼저 AKS 클러스터에서 Azure ID를 할당하려는 배포의 이름 및 네임스페이스를 식별합니다. 다음 명령을 실행하여 이 정보를 가져올 수 있습니다. 네임스페이스는 Azure Machine Learning 작업 영역 이름이어야 하고 배포 이름은 포털에 표시된 것처럼 엔드포인트 이름이어야 합니다.

```azurecli-interactive
kubectl get deployment --selector=isazuremlapp=true --all-namespaces --show-labels
```

배포 사양을 편집하여 배포에 Azure ID 선택기 레이블을 추가합니다. 선택기 값은 [AzureIdentityBinding 배포](https://azure.github.io/aad-pod-identity/docs/demo/standard_walkthrough/#5-deploy-azureidentitybinding)의 5단계에서 정의한 값이어야 합니다.

```yaml
apiVersion: "aadpodidentity.k8s.io/v1"
kind: AzureIdentityBinding
metadata:
  name: demo1-azure-identity-binding
spec:
  AzureIdentity: <a-idname>
  Selector: <label value to match>
```

배포를 편집하여 Azure ID 선택기 레이블을 추가합니다. `/spec/template/metadata/labels`에서 다음 섹션으로 이동합니다. `isazuremlapp: “true”`와 같은 값이 표시되어야 합니다. 아래와 같이 aad-pod-identity 레이블을 추가합니다.

```azurecli-interactive
    kubectl edit deployment/<name of deployment> -n azureml-<name of workspace>
```

```yaml
spec:
  template:
    metadata:
      labels:
       aadpodidbinding: "<value of Selector in AzureIdentityBinding>"
      ...
```

레이블이 올바르게 추가되었는지 확인하려면 다음 명령을 실행합니다. 또한 새로 만든 Pod의 상태를 확인해야 합니다.

```azurecli-interactive
   kubectl get pod -n azureml-<name of workspace> --show-labels
```

Pod가 실행되면 이제 코드에 자격 증명을 포함하지 않고도 이 배포 관련 웹 서비스에서 Azure ID를 통해 Azure 리소스에 액세스할 수 있습니다.

## <a name="assign-roles-to-your-azure-identity"></a>Azure ID에 역할 할당

[Azure 관리 ID를 적절한 역할에 할당](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)하여 다른 Azure 리소스에 액세스합니다. 할당하는 역할에는 올바른 **데이터 작업** 이 있어야 합니다. 예를 들어 [Storage Blob 데이터 읽기 권한자 역할](../role-based-access-control/built-in-roles.md#storage-blob-data-reader)에는 Storage Blob에 대한 읽기 권한이 부여되지만 일반 [읽기 권한자 역할](../role-based-access-control/built-in-roles.md#reader)은 그렇지 않을 수도 있습니다.

## <a name="use-azure-identity-with-your-web-service"></a>웹 서비스에 Azure ID 사용

AKS 클러스터에 모델을 배포합니다. `score.py` 스크립트에는 Azure ID로 액세스할 수 있는 Azure 리소스를 가리키는 작업이 포함될 수 있습니다. 액세스하려는 리소스에 대한 필수 클라이언트 라이브러리 종속성을 설치해야 합니다. 다음은 Azure ID를 사용하여 서비스의 다양한 Azure 리소스에 액세스하는 방법에 대한 몇 가지 예입니다.

### <a name="access-key-vault-from-your-web-service"></a>웹 서비스에서 Key Vault에 액세스

**Key Vault** 내부 암호에 대한 읽기 권한을 Azure ID에 제공하면 `score.py`는 다음 코드를 사용하여 Key Vault에 액세스할 수 있습니다.

```python
from azure.identity import DefaultAzureCredential
from azure.keyvault.secrets import SecretClient

my_vault_name = "yourkeyvaultname"
my_vault_url = "https://{}.vault.azure.net/".format(my_vault_name)
my_secret_name = "sample-secret"

# This will use your Azure Managed Identity
credential = DefaultAzureCredential()
secret_client = SecretClient(
    vault_url=my_vault_url,
    credential=credential)
secret = secret_client.get_secret(my_secret_name)
```

> [!IMPORTANT]
> 이 예제에서는 DefaultAzureCredential을 사용합니다. 특정 액세스 정책을 사용하여 ID 액세스 권한을 부여하려면 [Azure CLI를 사용하여 Key Vault 액세스 정책 할당](../key-vault/general/assign-access-policy-cli.md)을 참조하세요.

### <a name="access-blob-from-your-web-service"></a>웹 서비스에서 Blob에 액세스

**Storage Blob** 내부 데이터에 대한 Azure ID 읽기 권한을 제공하면 `score.py`는 다음 코드를 사용하여 Storage Blob에 액세스할 수 있습니다.

```python
from azure.identity import DefaultAzureCredential
from azure.storage.blob import BlobServiceClient

my_storage_account_name = "yourstorageaccountname"
my_storage_account_url = "https://{}.blob.core.windows.net/".format(my_storage_account_name)

# This will use your Azure Managed Identity
credential = DefaultAzureCredential()
blob_service_client = BlobServiceClient(
    account_url=my_storage_account_url,
    credential=credential
)
blob_client = blob_service_client.get_blob_client(container="some-container", blob="some_text.txt")
blob_data = blob_client.download_blob()
blob_data.readall()
```

## <a name="next-steps"></a>다음 단계

* Python Azure ID 클라이언트 라이브러리를 사용하는 방법에 대한 자세한 내용은 GitHub의 [리포지토리](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/identity/azure-identity#azure-identity-client-library-for-python)를 참조하세요.
* Azure Kubernetes Service 클러스터에 모델을 배포하는 방법에 대한 자세한 지침은 [방법](how-to-deploy-azure-kubernetes-service.md)을 참조하세요.