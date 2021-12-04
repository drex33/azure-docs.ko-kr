---
title: 인바운드 및 아웃바운드 네트워크 트래픽 구성
titleSuffix: Azure Machine Learning
description: 보안 Azure Machine Learning 작업 영역을 사용할 때 필요한 인바운드 및 아웃바운드 네트워크 트래픽을 구성하는 방법입니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: enterprise-readiness
ms.topic: how-to
ms.author: jhirono
author: jhirono
ms.reviewer: larryfr
ms.date: 11/30/2021
ms.custom: devx-track-python, ignite-fall-2021
ms.openlocfilehash: 33746d7e4909c99e740101fff89230b2ddb6582b
ms.sourcegitcommit: 1e9139680ca51f55ac965c4dd6dd82bf2fd43675
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/04/2021
ms.locfileid: "133542930"
---
# <a name="configure-inbound-and-outbound-network-traffic"></a>인바운드 및 아웃바운드 네트워크 트래픽 구성

이 문서에서는 VNet(가상 네트워크)에서 Azure Machine Learning 작업 영역을 보호할 때의 네트워크 통신 요구 사항에 대해 알아봅니다. Azure Machine Learning 작업 영역 및 공용 인터넷에 대 한 액세스를 제어 하도록 Azure 방화벽을 구성 하는 방법을 포함 합니다. Azure Machine Learning 보안 설정에 대한 자세한 내용은 [Azure Machine Learning 엔터프라이즈 보안](concept-enterprise-security.md)을 참조하세요.

> [!NOTE]
> 이 문서의 정보는 개인 끝점으로 구성 된 Azure Machine Learning 작업 영역에 적용 됩니다.

> [!TIP]
> 이 문서는 Azure Machine Learning 워크플로 보안에 대한 시리즈의 일부입니다. 이 시리즈의 다른 문서를 참조하세요.
>
> * [Virtual Network 개요](how-to-network-security-overview.md)
> * [작업 영역 리소스 보호](how-to-secure-workspace-vnet.md)
> * [학습 환경 보호](how-to-secure-training-vnet.md)
> * [보안 유추 환경](how-to-secure-inferencing-vnet.md)
> * [스튜디오 기능 사용](how-to-enable-studio-virtual-network.md)
> * [사용자 지정 DNS 사용](how-to-custom-dns.md)

## <a name="well-known-ports"></a>잘 알려진 포트

다음은이 문서에 나열 된 서비스에서 사용 하는 잘 알려진 포트입니다. 이 문서에서 포트 범위를 사용 하 고이 섹션에 나열 되지 않은 경우에는 서비스에만 적용 되며 사용 되는 항목에 대 한 정보를 게시 하지 못할 수 있습니다.


| 포트 | 설명 |
| ----- | ----- | 
| 80 | 보안 되지 않은 웹 트래픽 (HTTP) |
| 443 | 보안 웹 트래픽 (HTTPS) |
| 445 | Azure File storage에서 파일 공유에 액세스 하는 데 사용 되는 SMB 트래픽 |
| 8787 | 계산 인스턴스에서 RStudio에 연결할 때 사용 됩니다. |

## <a name="required-public-internet-access"></a>필수 공용 인터넷 액세스

[!INCLUDE [machine-learning-required-public-internet-access](../../includes/machine-learning-public-internet-access.md)]

## <a name="azure-firewall"></a>Azure Firewall

> [!IMPORTANT]
> Azure Firewall은 _Azure Virtual Network 리소스_ 에 대한 보안을 제공합니다. Azure Storage 계정과 같은 일부 Azure 서비스에는 _해당 특정 서비스 인스턴스의 퍼블릭 엔드포인트에 적용_ 되는 자체 방화벽 설정이 있습니다. 이 문서의 정보는 Azure Firewall에만 해당됩니다.
> 
> 서비스 인스턴스 방화벽 설정에 대한 자세한 내용은 [가상 네트워크에서 스튜디오 사용](how-to-enable-studio-virtual-network.md#firewall-settings)을 참조하세요.

* Azure Machine Learning 컴퓨팅 클러스터 및 컴퓨팅 인스턴스에 대한 __인바운드__ 트래픽의 경우 [UDR(사용자 정의 경로)](../virtual-network/virtual-networks-udr-overview.md)을 사용하여 방화벽을 건너뜁니다.

* __아웃바운드__ 트래픽의 경우 __네트워크__ 및/또는 __애플리케이션__ 규칙을 만듭니다. 

이러한 규칙 컬렉션은 [몇 가지 Azure Firewall 개념](../firewall/firewall-faq.yml#what-are-some-azure-firewall-concepts)에 대해 자세히 설명합니다.

### <a name="inbound-configuration"></a>인바운드 구성

[!INCLUDE [udr info for computes](../../includes/machine-learning-compute-user-defined-routes.md)]

### <a name="outbound-configuration"></a>아웃바운드 구성

1. 다음 서비스 태그로 __들어오고__ __나가는__ 트래픽을 허용하는 __네트워크 규칙__ 을 추가합니다.

    | 서비스 태그 | 프로토콜 | 포트 |
    | ----- |:-----:|:-----:|
    | AzureActiveDirectory | TCP | 80, 443 |
    | AzureMachineLearning | TCP | 443 |
    | AzureResourceManager | TCP | 443 |
    | Storage.region       | TCP | 443 |
    | AzureFrontDoor.FrontEnd</br>* Azure 중국에서는 필요하지 않습니다. | TCP | 443 | 
    | ContainerRegistry.region  | TCP | 443 |
    | MicrosoftContainerRegistry.region | TCP | 443 |
    | Keyvault. 지역 | TCP | 443 |

    > [!TIP]
    > * ContainerRegistry.region은 사용자 지정 Docker 이미지에만 필요합니다. Microsoft에서 제공 하는 기본 이미지에 대 한 작은 수정 (예: 추가 패키지)을 포함 합니다.
    > * MicrosoftContainerRegistry.region은 _Microsoft에서 제공하는 기본 Docker 이미지_ 를 사용하고 _사용자 관리 종속성을 사용하도록 설정_ 하려는 경우에만 필요합니다.
    > * Keyvault는 [hbi_workspace](/python/api/azureml-core/azureml.core.workspace%28class%29#create-name--auth-none--subscription-id-none--resource-group-none--location-none--create-resource-group-true--sku--basic---friendly-name-none--storage-account-none--key-vault-none--app-insights-none--container-registry-none--cmk-keyvault-none--resource-cmk-uri-none--hbi-workspace-false--default-cpu-compute-target-none--default-gpu-compute-target-none--exist-ok-false--show-output-true-) 플래그를 사용 하 여 작업 영역을 만든 경우에만 필요 합니다.
    > * `region`이 포함된 항목의 경우 사용 중인 Azure 지역으로 바꿉니다. 예: `ContainerRegistry.westus`.

1. 다음 호스트에 대한 __애플리케이션 규칙__ 을 추가합니다.

    > [!NOTE]
    > 인터넷의 모든 Python 리소스에 필요한 호스트의 전체 목록이 아니라 가장 일반적으로 사용됩니다. 예를 들어 GitHub 리포지토리 또는 다른 호스트에 대한 액세스 권한이 필요한 경우 해당 시나리오에 필요한 호스트를 식별하고 추가해야 합니다.

    | **호스트 이름** | **용도** |
    | ---- | ---- |
    | **graph.windows.net** | Azure Machine Learning 컴퓨팅 인스턴스/클러스터에서 사용됩니다. |
    | **anaconda.com**</br>**\*.anaconda.com** | 기본 패키지를 설치하는 데 사용됩니다. |
    | **\*.anaconda.org** | 리포지토리 데이터를 가져오는 데 사용됩니다. |
    | **pypi.org** | 기본 인덱스에서 종속성을 나열하는 데 사용되며 (있는 경우) 사용자 설정에서 인덱스를 덮어쓰지 않습니다. 인덱스를 덮어쓰는 경우 **\*.pythonhosted.org** 도 허용해야 합니다. |
    | **cloud.r-project.org** | R 개발용 CRAN 패키지를 설치할 때 사용됩니다. |
    | **\*pytorch.org** | PyTorch를 기반으로 하는 일부 예제에서 사용됩니다. |
    | **\*.tensorflow.org** | Tensorflow를 기반으로 하는 일부 예제에서 사용됩니다. |
    | **update.code.visualstudio.com**</br></br>**\*.vo.msecnd.net** | 설정 스크립트를 통해 컴퓨팅 인스턴스에 설치된 VS Code 서버 비트를 검색하는 데 사용됩니다.|
    | **raw.githubusercontent.com/microsoft/vscode-tools-for-ai/master/azureml_remote_websocket_server/\*** | 컴퓨팅 인스턴스에 설치된 WebSocket 서버 비트를 검색하는 데 사용됩니다. WebSocket 서버는 Visual Studio Code 클라이언트(데스크톱 애플리케이션)의 요청을 컴퓨팅 인스턴스에서 실행 중인 Visual Studio Code 서버로 전송하는 데 사용됩니다.|
    | **dc.applicationinsights.azure.com** | Microsoft 지원으로 작업할 때 메트릭 및 진단 정보를 수집 하는 데 사용 됩니다. |
    | **dc.applicationinsights.microsoft.com** | Microsoft 지원으로 작업할 때 메트릭 및 진단 정보를 수집 하는 데 사용 됩니다. |
    | **dc.services.visualstudio.com** | Microsoft 지원으로 작업할 때 메트릭 및 진단 정보를 수집 하는 데 사용 됩니다. | 
    

    __프로토콜:포트__ 에 대해 __http, https__ 사용을 선택합니다.

    애플리케이션 규칙을 구성하는 방법에 대한 자세한 내용은 [Azure Firewall 배포 및 구성](../firewall/tutorial-firewall-deploy-portal.md#configure-an-application-rule)을 참조하세요.

1. AKS(Azure Kubernetes Service)에 배포된 모델의 아웃바운드 트래픽을 제한하려면 [Azure Kubernetes Service에서 송신 트래픽 제한](../aks/limit-egress-traffic.md) 및 [Azure Kubernetes Service에 ML 모델 배포](how-to-deploy-azure-kubernetes-service.md#connectivity) 문서를 참조하세요.

### <a name="azure-kubernetes-services"></a>Azure Kubernetes Services

Azure Machine Learning에서 Azure Kubernetes Service를 사용하는 경우 다음 트래픽이 허용되어야 합니다.

* [Azure Kubernetes Service에서 송신 트래픽 제한](../aks/limit-egress-traffic.md) 문서에 설명된 AKS에 대한 일반적인 인바운드/아웃바운드 요구 사항입니다.
* mcr.microsoft.com으로의 __아웃바운드__.
* AKS 클러스터에 모델을 배포하는 경우 [Azure Kubernetes Service에 ML 모델 배포](how-to-deploy-azure-kubernetes-service.md#connectivity) 문서의 지침을 사용하세요.

## <a name="other-firewalls"></a>기타 방화벽

각 방화벽에는 고유한 용어 및 특정 구성이 있으므로 이 섹션의 지침은 일반적입니다. 질문이 있는 경우 사용 중인 방화벽에 대한 설명서를 확인합니다.

올바르게 구성되지 않은 경우 방화벽에서 작업 영역 사용에 문제를 일으킬 수 있습니다. Azure Machine Learning 작업 영역에서 사용되는 다양한 호스트 이름이 있습니다. 다음 섹션에서는 Azure Machine Learning에 필요한 호스트를 나열합니다.

### <a name="dependencies-api"></a>종속성 API

Azure Machine Learning REST API를 사용 하 여 __아웃 바운드__ 트래픽을 허용 해야 하는 호스트 및 포트의 목록을 가져올 수도 있습니다. 이 API를 사용 하려면 다음 단계를 사용 합니다.

1. 인증 토큰을 가져옵니다. 다음 명령은 [Azure CLI](/cli/azure/install-azure-cli) 를 사용 하 여 인증 토큰 및 구독 ID를 가져오는 방법을 보여 줍니다.

    ```azurecli-interactive
    TOKEN=$(az account get-access-token --query accessToken -o tsv)
    SUBSCRIPTION=$(az account show --query id -o tsv)
    ```

2. API를 호출 합니다. 다음 명령에서 다음 값을 바꿉니다.
    * 을 `<region>` 작업 영역에 있는 Azure 지역으로 바꿉니다. 예들 들어 `westus2`입니다.
    * `<resource-group>`을 작업 영역이 포함된 리소스 그룹으로 바꿉니다.
    * `<workspace-name>`을 작업 영역의 이름으로 바꿉니다.

    ```azurecli-interactive
    az rest --method GET \
        --url "https://<region>.api.azureml.ms/rp/workspaces/subscriptions/$SUBSCRIPTION/resourceGroups/<resource-group>/providers/Microsoft.MachineLearningServices/workspaces/<workspace-name>/outboundNetworkDependenciesEndpoints?api-version=2018-03-01-preview" \
        --header Authorization="Bearer $TOKEN"
    ```

API 호출의 결과는 JSON 문서입니다. 다음 코드 조각은이 문서를 발췌 한 것입니다.

```json
{
  "value": [
    {
      "properties": {
        "category": "Azure Active Directory",
        "endpoints": [
          {
            "domainName": "login.microsoftonline.com",
            "endpointDetails": [
              {
                "port": 80
              },
              {
                "port": 443
              }
            ]
          }
        ]
      }
    },
    {
      "properties": {
        "category": "Azure portal",
        "endpoints": [
          {
            "domainName": "management.azure.com",
            "endpointDetails": [
              {
                "port": 443
              }
            ]
          }
        ]
      }
    },
...
```

### <a name="microsoft-hosts"></a>Microsoft 호스트

다음 표의 호스트는 Microsoft에서 소유하며 작업 영역의 적절한 기능에 필요한 서비스를 제공합니다. 표에는 Azure 공용, Azure Government 및 Azure 중국 21Vianet 지역에 대한 호스트가 나열되어 있습니다.

> [!IMPORTANT]
> Azure Machine Learning은 구독과 Microsoft에서 관리 하는 구독에서 Azure Storage 계정을 사용 합니다. 해당 하는 경우이 섹션에서 다음 용어를 구분 하는 데 사용 됩니다.
>
> * __저장소__: 데이터와 아티팩트 (예: 모델, 학습 데이터, 학습 로그 및 Python 스크립트)를 저장 하는 데 사용 되는 구독의 Azure Storage 계정입니다. >
> * __microsoft 저장소__: Azure Machine Learning 계산 인스턴스와 계산 클러스터는 Azure Batch에 의존 하며 Microsoft 구독에 있는 저장소에 액세스 해야 합니다. 이 저장소는 계산 인스턴스를 관리 하는 데만 사용 됩니다. 여기에는 데이터가 저장 되지 않습니다.

**일반 Azure 호스트**

# <a name="azure-public"></a>[Azure 공용](#tab/public)

| **필수** | **호스트** | **프로토콜** | **Ports** |
| ----- | ----- | ----- | ---- | 
| Azure Active Directory | login.microsoftonline.com | TCP | 80, 443 |
| Azure portal | management.azure.com | TCP | 443 |
| Azure Resource Manager | management.azure.com | TCP | 443 |

# <a name="azure-government"></a>[Azure Government](#tab/gov)

| **필수** | **호스트** | **프로토콜** | **Ports** |
| ----- | ----- | ----- | ---- |
| Azure Active Directory | login.microsoftonline.us | TCP | 80, 443 |
| Azure portal | management.azure.us | TCP | 443 |
| Azure 리소스 관리자 | management.usgovcloudapi.net | TCP | 443 |

# <a name="azure-china-21vianet"></a>[Azure China 21Vianet](#tab/china)

| **필수** | **호스트** | **프로토콜** | **Ports** |
| ----- | ----- | ----- | ----- |
| Azure Active Directory | login.chinacloudapi.cn | TCP | 80, 443 |
| Azure portal | management.azure.cn | TCP | 443 |
| Azure 리소스 관리자 | management.chinacloudapi.cn | TCP | 443 |

---

**Azure Machine Learning 호스트**

> [!IMPORTANT]
> 다음 표에서 `<storage>`를 Azure Machine Learning 작업 영역의 기본 스토리지 계정 이름으로 바꿉니다.

# <a name="azure-public"></a>[Azure 공용](#tab/public)

| **필수** | **호스트** | **프로토콜** | **Ports** |
| ----- | ----- | ----- | ----- |
| Azure Machine Learning Studio | ml.azure.com | TCP | 443 |
| API |\*.azureml.ms | TCP | 443 |
| 통합 Notebook | \*.notebooks.azure.net | TCP | 443 |
| 통합 Notebook | \<storage\>.file.core.windows.net | TCP | 443, 445 |
| 통합 Notebook | \<storage\>.dfs.core.windows.net | TCP | 443 |
| 통합 Notebook | \<storage\>.blob.core.windows.net | TCP | 443 |
| 통합 Notebook | graph.microsoft.com | TCP | 443 |
| 통합 Notebook | \*.aznbcontent.net | TCP | 443 |

# <a name="azure-government"></a>[Azure Government](#tab/gov)

| **필수** | **호스트** | **프로토콜** | **Ports** |
| ----- | ----- | ----- | ----- |
| Azure Machine Learning Studio | ml.azure.us | TCP | 443 |
| API | \*.ml.azure.us | TCP | 443 |
| 통합 Notebook | \*.notebooks.usgovcloudapi.net | TCP | 443 |
| 통합 Notebook | \<storage\>.file.core.usgovcloudapi.net | TCP | 443, 445 |
| 통합 Notebook | \<storage\>.dfs.core.usgovcloudapi.net | TCP | 443 |
| 통합 Notebook  | \<storage\>.blob.core.usgovcloudapi.net | TCP | 443 |
| 통합 Notebook | graph.microsoft.us | TCP | 443 |
| 통합 Notebook | \*.aznbcontent.net | TCP | 443 |

# <a name="azure-china-21vianet"></a>[Azure China 21Vianet](#tab/china)

| **필수** | **호스트** | **프로토콜** | **Ports** |
| ----- | ----- | ----- | ----- |
| Azure Machine Learning Studio | studio.ml.azure.cn | TCP | 443 |
| API | \*.ml.azure.cn | TCP | 443 |
| 통합 Notebook | \*.notebooks.chinacloudapi.cn | TCP | 443 |
| 통합 Notebook | \<storage\>.file.core.chinacloudapi.cn | TCP | 443, 445 |
| 통합 Notebook | \<storage\>.dfs.core.chinacloudapi.cn | TCP | 443 |
| 통합 Notebook | \<storage\>.blob.core.chinacloudapi.cn | TCP | 443 |
| 통합 Notebook | graph.chinacloudapi.cn | TCP | 443 |
| 통합 Notebook | \*.aznbcontent.net | TCP | 443 |

---

**Azure Machine Learning 컴퓨팅 인스턴스 및 컴퓨팅 클러스터 호스트**

> [!TIP]
> * __Azure Key Vault__ 호스트는 hbi_workspace [플래그를](/python/api/azureml-core/azureml.core.workspace%28class%29#create-name--auth-none--subscription-id-none--resource-group-none--location-none--create-resource-group-true--sku--basic---friendly-name-none--storage-account-none--key-vault-none--app-insights-none--container-registry-none--cmk-keyvault-none--resource-cmk-uri-none--hbi-workspace-false--default-cpu-compute-target-none--default-gpu-compute-target-none--exist-ok-false--show-output-true-) 사용하도록 설정된 작업 영역을 만든 경우에만 필요합니다.
> * __컴퓨팅 인스턴스용__ 포트 8787 및 18881은 Azure Machine 작업 영역에 프라이빗 엔드포인트가 있는 경우에만 필요합니다.
> * 다음 표에서 `<storage>`를 Azure Machine Learning 작업 영역의 기본 스토리지 계정 이름으로 바꿉니다.

# <a name="azure-public"></a>[Azure 공용](#tab/public)

| **필수** | **호스트** | **프로토콜** | **Ports** |
| ----- | ----- | ----- | ----- |
| 컴퓨팅 클러스터/인스턴스 | graph.windows.net | TCP | 443 |
| 컴퓨팅 인스턴스 | \*.instances.azureml.net | TCP | 443 |
| 컴퓨팅 인스턴스 | \*.instances.azureml.ms | TCP | 443, 8787, 18881 |
| Microsoft 스토리지 액세스 | \*.blob.core.windows.net | TCP | 443 |
| Microsoft 스토리지 액세스 | \*.table.core.windows.net | TCP | 443 |
| Microsoft 스토리지 액세스 | \*.queue.core.windows.net | TCP | 443 |
| 스토리지 계정 | \<storage\>.file.core.windows.net | TCP | 443, 445 |
| 스토리지 계정 | \<storage\>.blob.core.windows.net | TCP | 443 |
| Azure Key Vault | \*.vault.azure.net | TCP | 443 |

# <a name="azure-government"></a>[Azure Government](#tab/gov)

| **필수** | **호스트** | **프로토콜** | **Ports** |
| ----- | ----- | ----- | ----- |
| 컴퓨팅 클러스터/인스턴스 | graph.windows.net | TCP | 443 |
| 컴퓨팅 인스턴스 | \*.instances.azureml.us | TCP | 443 |
| 컴퓨팅 인스턴스 | \*.instances.azureml.ms | TCP | 443, 8787, 18881 |
| Microsoft 스토리지 액세스 | \*.blob.core.usgovcloudapi.net | TCP | 443 |
| Microsoft 스토리지 액세스 | \*.table.core.usgovcloudapi.net | TCP | 443 |
| Microsoft 스토리지 액세스 | \*.queue.core.usgovcloudapi.net | TCP | 443 |
| 스토리지 계정 | \<storage\>.file.core.usgovcloudapi.net | TCP | 443, 445 |
| 스토리지 계정 | \<storage\>.blob.core.usgovcloudapi.net | TCP | 443 |
| Azure Key Vault | \*.vault.usgovcloudapi.net | TCP | 443 |

# <a name="azure-china-21vianet"></a>[Azure China 21Vianet](#tab/china)

| **필수** | **호스트** | **프로토콜** | **Ports** |
| ----- | ----- | ----- | ----- |
| 컴퓨팅 클러스터/인스턴스 | graph.chinacloudapi.cn | TCP | 443 |
| 컴퓨팅 인스턴스 |  \*.instances.azureml.cn | TCP | 443 |
| 컴퓨팅 인스턴스 | \*.instances.azureml.ms | TCP | 443, 8787, 18881 |
| Microsoft storage 액세스 | \*blob.core.chinacloudapi.cn | TCP | 443 |
| Microsoft storage 액세스 | \*.table.core.chinacloudapi.cn | TCP | 443 |
| Microsoft storage 액세스 | \*.queue.core.chinacloudapi.cn | TCP | 443 |
| 스토리지 계정 | \<storage\>.file.core.chinacloudapi.cn | TCP | 443, 445 |
| 스토리지 계정 | \<storage\>.blob.core.chinacloudapi.cn | TCP | 443 |
| Azure Key Vault | \*.vault.azure.cn | TCP | 443 |

---

**Azure Machine Learning에서 유지 관리하는 Docker 이미지**

| **필수** | **호스트** | **프로토콜** | **Ports** |
| ----- | ----- | ----- | ----- |
| Microsoft Container Registry | mcr.microsoft.com | TCP | 443 |
| Azure Machine Learning 미리 빌드된 이미지 | viennaglobal.azurecr.io | TCP | 443 |

> [!TIP]
> * 모든 사용자 지정 Docker 이미지에는 __Azure Container Registry__ 가 필요합니다. 여기에는 Microsoft에서 제공하는 기본 이미지에 대한 약간의 수정(예: 추가 패키지)이 포함됩니다.
> * __Microsoft Container Registry__ 는 _Microsoft에서 제공하는 기본 Docker 이미지_ 를 사용하고 _사용자 관리 종속성을 사용하도록 설정_ 하려는 경우에만 필요합니다.
> * 페더레이션된 ID를 사용하려는 경우 [Active Directory Federation Services 보안 설정에 대한 모범 사례](/windows-server/identity/ad-fs/deployment/best-practices-securing-ad-fs) 문서를 따르세요.

또한 [인바운드 구성](#inbound-configuration) 섹션의 정보를 사용하여 `BatchNodeManagement` 및 `AzureMachineLearning`에 대한 IP 주소를 추가합니다.

AKS에 배포된 모델에 대한 액세스 제한에 대한 자세한 내용은 [Azure Kubernetes Service에서 송신 트래픽 제한](../aks/limit-egress-traffic.md)을 참조하세요.

**모니터링, 메트릭 및 진단**

Azure Monitor 및 Application Insights에 대 한 메트릭 및 기타 모니터링 정보의 로깅을 지원 하려면 다음 호스트에 대 한 아웃 바운드 트래픽을 허용 합니다.

> [!NOTE]
> 이러한 호스트에 기록 되는 정보는 작업 영역에서 실행 하는 문제를 진단 하기 위해 Microsoft 지원 에서도 사용 됩니다.

* **dc.applicationinsights.azure.com**
* **dc.applicationinsights.microsoft.com**
* **dc.services.visualstudio.com**
* **. in.applicationinsights.azure.com**

이러한 호스트의 IP 주소 목록은 [Azure Monitor에서 사용 하는 ip 주소](../azure-monitor/app/ip-addresses.md)를 참조 하세요.

### <a name="python-hosts"></a>Python 호스트

이 섹션의 호스트는 Python 패키지를 설치하는 데 사용되며 개발, 교육 및 배포 중에 필요합니다. 

> [!NOTE]
> 인터넷의 모든 Python 리소스에 필요한 호스트의 전체 목록이 아니라 가장 일반적으로 사용됩니다. 예를 들어 GitHub 리포지토리 또는 다른 호스트에 대한 액세스 권한이 필요한 경우 해당 시나리오에 필요한 호스트를 식별하고 추가해야 합니다.

| **호스트 이름** | **용도** |
| ---- | ---- |
| **anaconda.com**</br>**\*.anaconda.com** | 기본 패키지를 설치하는 데 사용됩니다. |
| **\*.anaconda.org** | 리포지토리 데이터를 가져오는 데 사용됩니다. |
| **pypi.org** | 기본 인덱스에서 종속성을 나열하는 데 사용되며 (있는 경우) 사용자 설정에서 인덱스를 덮어쓰지 않습니다. 인덱스를 덮어쓰는 경우 **\*.pythonhosted.org** 도 허용해야 합니다. |
| **\*pytorch.org** | PyTorch를 기반으로 하는 일부 예제에서 사용됩니다. |
| **\*.tensorflow.org** | Tensorflow를 기반으로 하는 일부 예제에서 사용됩니다. |

### <a name="r-hosts"></a>R 호스트

이 섹션의 호스트는 R 패키지를 설치하는 데 사용되며 개발, 교육 및 배포 중에 필요합니다.

> [!NOTE]
> 인터넷의 모든 R 리소스에 필요한 호스트의 전체 목록이 아니라 가장 일반적으로 사용됩니다. 예를 들어 GitHub 리포지토리 또는 다른 호스트에 대한 액세스 권한이 필요한 경우 해당 시나리오에 필요한 호스트를 식별하고 추가해야 합니다.

| **호스트 이름** | **용도** |
| ---- | ---- |
| **cloud.r-project.org** | CRAN 패키지를 설치할 때 사용됩니다. |

### <a name="azure-kubernetes-services"></a>Azure Kubernetes Services

Azure Machine Learning에서 Azure Kubernetes Service를 사용하는 경우 다음 트래픽이 허용되어야 합니다.

* [Azure Kubernetes Service에서 송신 트래픽 제한](../aks/limit-egress-traffic.md) 문서에 설명된 AKS에 대한 일반적인 인바운드/아웃바운드 요구 사항입니다.
* mcr.microsoft.com으로의 __아웃바운드__.
* AKS 클러스터에 모델을 배포하는 경우 [Azure Kubernetes Service에 ML 모델 배포](how-to-deploy-azure-kubernetes-service.md#connectivity) 문서의 지침을 사용하세요.

### <a name="azure-arc-enabled-kubernetes"></a>Azure Arc 사용 Kubernetes <a id="arc-kubernetes"></a>

Azure Arc 사용 Kubernetes 클러스터는 Azure Arc 연결에 따라 달라 집니다. [Azure Arc 네트워크 요구 사항을](../azure-arc/kubernetes/quickstart-connect-cluster.md?tabs=azure-cli#meet-network-requirements)충족 하는지 확인 합니다.

이 섹션의 호스트는 Kubernetes 클러스터에 Azure Machine Learning 확장을 배포 하 고 교육 및 추론 작업을 클러스터에 제출 하는 데 사용 됩니다.

**Azure Machine Learning 확장 배포**

클러스터에 Azure Machine Learning 확장을 배포할 때 다음 끝점에 대 한 아웃 바운드 액세스를 사용 하도록 설정 합니다.

| 대상 엔드포인트| 포트 | 사용 |
|--|--|--|
|  *.data.mcr.microsoft.com| https:443 | Azure CDN(Content Delivery Network)이 지원하는 MCR 스토리지에 필요합니다. |
| quay.io, *.quay.io | https:443 | AML 확장 구성 요소에 대한 컨테이너 이미지를 끌어오는 데 필요한 Quay.io 레지스트리 |
| gcr.io| https:443 | Google 클라우드 리포지토리- AML 확장 구성 요소에 대한 컨테이너 이미지를 끌어와야 합니다. |
| storage.googleapis.com | https:443 | Google 클라우드 스토리지, gcr 이미지가 호스트 |
| registry-1.docker.io, production.cloudflare.docker.com  | https:443 | Docker 허브 레지스트리- AML 확장 구성 요소에 대한 컨테이너 이미지를 끌어와야 합니다. |
| auth.docker.io| https:443 | Docker 허브 레지스트리에 액세스하는 데 필요한 Docker 리포지토리 인증 |
| *.kusto.windows.net, *.table.core.windows.net, *.queue.core.windows.net | https:443 | Kusto에서 시스템 로그를 업로드하고 분석하는 데 필요 |

**학습 워크로드만**

다음 엔드포인트에 대한 아웃바운드 액세스를 사용하도록 설정하여 학습 워크로드를 클러스터에 제출합니다.

| 대상 엔드포인트| 포트 | 사용 |
|--|--|--|
| pypi.org | https:443 | 작업 환경을 초기화하는 데 사용되는 pip 패키지를 설치하기 위한 Python 패키지 인덱스 |
| archive.ubuntu.com, security.ubuntu.com, ppa.launchpad.net | http:80 | 이 주소를 사용하면 init 컨테이너가 필요한 보안 패치 및 업데이트를 다운로드할 수 있습니다. |

**워크로드 학습 및 추론**

학습 워크로드에 대한 엔드포인트 외에도 다음 엔드포인트에 대한 아웃바운드 액세스를 사용하도록 설정하여 학습 및 추론 워크로드를 제출합니다.

| 대상 엔드포인트| 포트 | 사용 |
|--|--|--|
| *.azurecr.io | https:443 | 학습 또는 유추 작업을 호스트하기 위해 컨테이너 이미지를 끌어오는 데 필요한 Azure 컨테이너 레지스트리|
| *.blob.core.windows.net | https:443 | 기계 학습 프로젝트 스크립트, 컨테이너 이미지 및 작업 로그/메트릭을 가져오는 데 필요한 Azure Blob Storage |
| *.workspace. \<region\> . api.azureml.ms ,  \<region\> .experiments.azureml.net,  \<region\> .api.azureml.ms | https:443 | AML 통신하는 데 필요한 Azure Machine Learning Service API |

### <a name="visual-studio-code-hosts"></a>Visual Studio Code 호스트

이 섹션의 호스트는 Visual Studio Code 패키지를 설치하여 Visual Studio Code와 Azure Machine Learning 작업 영역의 컴퓨팅 인스턴스 간의 원격 연결을 설정하는 데 사용됩니다.

> [!NOTE]
> 인터넷의 모든 Visual Studio Code 리소스에 필요한 호스트의 전체 목록이 아니라 가장 일반적으로 사용됩니다. 예를 들어 GitHub 리포지토리 또는 다른 호스트에 대한 액세스 권한이 필요한 경우 해당 시나리오에 필요한 호스트를 식별하고 추가해야 합니다.

| **호스트 이름** | **용도** |
| ---- | ---- |
|  **update.code.visualstudio.com**</br></br>**\*.vo.msecnd.net** | 설정 스크립트를 통해 컴퓨팅 인스턴스에 설치된 VS Code 서버 비트를 검색하는 데 사용됩니다.|
| **raw.githubusercontent.com/microsoft/vscode-tools-for-ai/master/azureml_remote_websocket_server/\*** |컴퓨팅 인스턴스에 설치된 WebSocket 서버 비트를 검색하는 데 사용됩니다. WebSocket 서버는 Visual Studio Code 클라이언트(데스크톱 애플리케이션)의 요청을 컴퓨팅 인스턴스에서 실행 중인 Visual Studio Code 서버로 전송하는 데 사용됩니다. |

## <a name="next-steps"></a>다음 단계

이 문서는 Azure Machine Learning 워크플로 보안에 대한 시리즈의 일부입니다. 이 시리즈의 다른 문서를 참조하세요.

* [Virtual Network 개요](how-to-network-security-overview.md)
* [작업 영역 리소스 보호](how-to-secure-workspace-vnet.md)
* [학습 환경 보호](how-to-secure-training-vnet.md)
* [보안 유추 환경](how-to-secure-inferencing-vnet.md)
* [스튜디오 기능 사용](how-to-enable-studio-virtual-network.md)
* [사용자 지정 DNS 사용](how-to-custom-dns.md)

Azure Firewall 구성에 대한 자세한 내용은 [자습서: Azure Portal을 사용하여 Azure Firewall 배포 및 구성](../firewall/tutorial-firewall-deploy-portal.md)을 참조하세요.