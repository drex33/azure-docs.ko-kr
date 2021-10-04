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
ms.date: 09/14/2021
ms.custom: devx-track-python
ms.openlocfilehash: 50c8a38a5acbfea119770a5ea81a21d51fd4ea83
ms.sourcegitcommit: f29615c9b16e46f5c7fdcd498c7f1b22f626c985
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/04/2021
ms.locfileid: "129424543"
---
# <a name="configure-inbound-and-outbound-network-traffic"></a>인바운드 및 아웃바운드 네트워크 트래픽 구성

이 문서에서는 VNet(가상 네트워크)에서 Azure Machine Learning 작업 영역을 보호할 때의 네트워크 통신 요구 사항에 대해 알아봅니다. 여기에는 Azure Machine Learning 작업 영역 및 공용 인터넷에 대한 액세스를 제어하도록 Azure Firewall을 구성하는 방법이 포함됩니다. Azure Machine Learning 보안 설정에 대한 자세한 내용은 [Azure Machine Learning 엔터프라이즈 보안](concept-enterprise-security.md)을 참조하세요.

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
    | AzureActiveDirectory | TCP | * |
    | AzureMachineLearning | TCP | 443 |
    | AzureResourceManager | TCP | 443 |
    | Storage.region       | TCP | 443 |
    | AzureFrontDoor.FrontEnd</br>* Azure 중국에서는 필요하지 않습니다. | TCP | 443 | 
    | ContainerRegistry.region  | TCP | 443 |
    | MicrosoftContainerRegistry.region | TCP | 443 |
    | Keyvault. 지역 | TCP | 443 |

    > [!TIP]
    > * ContainerRegistry.region은 사용자 지정 Docker 이미지에만 필요합니다. 여기에는 Microsoft에서 제공하는 기본 이미지에 대한 약간의 수정(예: 추가 패키지)이 포함됩니다.
    > * MicrosoftContainerRegistry.region은 _Microsoft에서 제공하는 기본 Docker 이미지_ 를 사용하고 _사용자 관리 종속성을 사용하도록 설정_ 하려는 경우에만 필요합니다.
    > * Keyvault는 [hbi_workspace](/python/api/azureml-core/azureml.core.workspace%28class%29#create-name--auth-none--subscription-id-none--resource-group-none--location-none--create-resource-group-true--sku--basic---friendly-name-none--storage-account-none--key-vault-none--app-insights-none--container-registry-none--cmk-keyvault-none--resource-cmk-uri-none--hbi-workspace-false--default-cpu-compute-target-none--default-gpu-compute-target-none--exist-ok-false--show-output-true-) 플래그를 사용 하 여 작업 영역을 만든 경우에만 필요 합니다.
    > * `region`이 포함된 항목의 경우 사용 중인 Azure 지역으로 바꿉니다. `ContainerRegistry.westus`)을 입력합니다.

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

### <a name="microsoft-hosts"></a>Microsoft 호스트

다음 표의 호스트는 Microsoft에서 소유하며 작업 영역의 적절한 기능에 필요한 서비스를 제공합니다. 표에는 Azure 공용, Azure Government 및 Azure 중국 21Vianet 지역에 대한 호스트가 나열되어 있습니다.

**일반 Azure 호스트**

| **필수** | **Azure 공용** | **Azure Government** | **Azure China 21Vianet** |
| ----- | ----- | ----- | ----- |
| Azure Active Directory | login.microsoftonline.com | login.microsoftonline.us | login.chinacloudapi.cn |
| Azure portal | management.azure.com | management.azure.us | management.azure.cn |
| Azure Resource Manager | management.azure.com | management.usgovcloudapi.net | management.chinacloudapi.cn |

**Azure Machine Learning 호스트**

> [!IMPORTANT]
> 다음 표에서 `<storage>`를 Azure Machine Learning 작업 영역의 기본 스토리지 계정 이름으로 바꿉니다.

| **필수** | **Azure 공용** | **Azure Government** | **Azure China 21Vianet** |
| ----- | ----- | ----- | ----- |
| Azure Machine Learning Studio | ml.azure.com | ml.azure.us | studio.ml.azure.cn |
| API |\*.azureml.ms | \*.ml.azure.us | \*.ml.azure.cn |
| 통합 Notebook | \*.notebooks.azure.net | \*.notebooks.usgovcloudapi.net |\*.notebooks.chinacloudapi.cn |
| 통합 Notebook | \<storage\>.file.core.windows.net | \<storage\>.file.core.usgovcloudapi.net | \<storage\>.file.core.chinacloudapi.cn |
| 통합 Notebook | \<storage\>.dfs.core.windows.net | \<storage\>.dfs.core.usgovcloudapi.net | \<storage\>.dfs.core.chinacloudapi.cn |
| 통합 Notebook | \<storage\>.blob.core.windows.net | \<storage\>.blob.core.usgovcloudapi.net | \<storage\>.blob.core.chinacloudapi.cn |
| 통합 Notebook | graph.microsoft.com | graph.microsoft.us | graph.chinacloudapi.cn |
| 통합 Notebook | \*.aznbcontent.net |  | |

**Azure Machine Learning 컴퓨팅 인스턴스 및 컴퓨팅 클러스터 호스트**

| **필수** | **Azure 공용** | **Azure Government** | **Azure China 21Vianet** |
| ----- | ----- | ----- | ----- |
| 컴퓨팅 클러스터/인스턴스 | graph.windows.net | graph.windows.net | graph.chinacloudapi.cn |
| 컴퓨팅 인스턴스 | \*.instances.azureml.net | \*.instances.azureml.us | \*.instances.azureml.cn |
| 컴퓨팅 인스턴스 | \*.instances.azureml.ms |  |  |
| Azure Storage 계정 | \*.blob.core.windows.net</br>\*.table.core.windows.net</br>\*.queue.core.windows.net | \*.blob.core.usgovcloudapi.net</br>\*.table.core.usgovcloudapi.net</br>\*.queue.core.usgovcloudapi.net | \*blob.core.chinacloudapi.cn</br>\*.table.core.chinacloudapi.cn</br>\*.queue.core.chinacloudapi.cn |
| Azure Key Vault | \*.vault.azure.net | \*.vault.usgovcloudapi.net | \*.vault.azure.cn |

> [!IMPORTANT]
> 방화벽은 __TCP__ 포트 __18881, 443 및 8787__ 을 통해 \*.instances.azureml.ms와의 통신을 허용해야 합니다.

> [!TIP]
> Azure Key Vault의 FQDN은 [hbi_workspace](/python/api/azureml-core/azureml.core.workspace%28class%29#create-name--auth-none--subscription-id-none--resource-group-none--location-none--create-resource-group-true--sku--basic---friendly-name-none--storage-account-none--key-vault-none--app-insights-none--container-registry-none--cmk-keyvault-none--resource-cmk-uri-none--hbi-workspace-false--default-cpu-compute-target-none--default-gpu-compute-target-none--exist-ok-false--show-output-true-) 플래그를 사용 하 여 작업 영역을 만든 경우에만 필요 합니다.

**Azure Machine Learning에서 유지 관리하는 Docker 이미지**

| **필수** | **Azure 공용** | **Azure Government** | **Azure China 21Vianet** |
| ----- | ----- | ----- | ----- |
| Microsoft Container Registry | mcr.microsoft.com | mcr.microsoft.com | mcr.microsoft.com |
| Azure Machine Learning 미리 빌드된 이미지 | viennaglobal.azurecr.io | viennaglobal.azurecr.io | viennaglobal.azurecr.io |

> [!TIP]
> * 모든 사용자 지정 Docker 이미지에는 __Azure Container Registry__ 가 필요합니다. 여기에는 Microsoft에서 제공하는 기본 이미지에 대한 약간의 수정(예: 추가 패키지)이 포함됩니다.
> * __Microsoft Container Registry__ 는 _Microsoft에서 제공하는 기본 Docker 이미지_ 를 사용하고 _사용자 관리 종속성을 사용하도록 설정_ 하려는 경우에만 필요합니다.
> * 페더레이션된 ID를 사용하려는 경우 [Active Directory Federation Services 보안 설정에 대한 모범 사례](/windows-server/identity/ad-fs/deployment/best-practices-securing-ad-fs) 문서를 따르세요.

또한 [인바운드 구성](#inbound-configuration) 섹션의 정보를 사용하여 `BatchNodeManagement` 및 `AzureMachineLearning`에 대한 IP 주소를 추가합니다.

AKS에 배포된 모델에 대한 액세스 제한에 대한 자세한 내용은 [Azure Kubernetes Service에서 송신 트래픽 제한](../aks/limit-egress-traffic.md)을 참조하세요.

**진단 지원**

Microsoft 지원 작업 영역에서 발생한 문제를 진단하려면 다음 호스트에 대한 아웃바운드 트래픽을 허용해야 합니다.

* **dc.applicationinsights.azure.com**
* **dc.applicationinsights.microsoft.com**
* **dc.services.visualstudio.com**

이러한 호스트에 대한 IP 주소 목록은 [Azure Monitor 사용되는 IP 주소를](../azure-monitor/app/ip-addresses.md)참조하세요.

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
