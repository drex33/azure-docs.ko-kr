---
title: 네트워크 트래픽 흐름 보호
titleSuffix: Azure Machine Learning
description: Azure Machine Learning 작업 영역이 보안 가상 네트워크에 있을 때 구성 요소 간에 네트워크 트래픽이 흐르는 방법을 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: enterprise-readiness
ms.topic: conceptual
ms.author: jhirono
author: jhirono
ms.reviewer: larryfr
ms.date: 09/22/2021
ms.openlocfilehash: 9e35609a6e6d450b2938dfbb4feab319f5a3c9c2
ms.sourcegitcommit: e82ce0be68dabf98aa33052afb12f205a203d12d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/07/2021
ms.locfileid: "129660261"
---
# <a name="network-traffic-flow-when-using-a-secured-workspace"></a>보안 작업 영역을 사용하는 경우 네트워크 트래픽 흐름

Azure Machine Learning 작업 영역 및 연결된 리소스가 Azure Virtual Network 보호되면 리소스 간의 네트워크 트래픽이 변경됩니다. 가상 네트워크가 없으면 네트워크 트래픽이 공용 인터넷 또는 Azure 데이터 센터 내에서 흐릅니다. VNet(가상 네트워크)이 도입되면 네트워크 보안을 강화할 수도 있습니다. 예를 들어 VNet과 공용 인터넷 간의 인바운드 및 아웃바운드 통신을 차단합니다. 그러나 Azure Machine Learning 공용 인터넷의 일부 리소스에 액세스해야 합니다. 예를 들어 Azure 리소스 관리는 배포 및 관리 작업에 사용됩니다.

이 문서에서는 공용 인터넷으로/에서 필요한 트래픽을 나열합니다. 또한 다음과 같은 시나리오에서 클라이언트 개발 환경과 보안 Azure Machine Learning 작업 영역 간에 네트워크 트래픽이 흐르는 방식을 설명합니다.

* Azure Machine Learning __Studio를__ 사용하여 작업:

    * 작업 영역
    * AutoML
    * Designer
    * 데이터 세트 및 데이터 저장소

    > [!TIP]
    > Azure Machine Learning Studio는 웹 브라우저에서 부분적으로 실행되고 Azure 서비스를 호출하여 모델 학습, 디자이너 사용 또는 데이터 세트 보기와 같은 작업을 수행하는 웹 기반 UI입니다. 이러한 호출 중 일부는 SDK, CLI, REST API 또는 VS Code 사용하는 경우와 다른 통신 흐름을 사용합니다.

* Azure Machine Learning __Studio,__ __SDK,__ __CLI__ 또는 __REST API__ 사용하여 다음 작업을 수행할 수 있습니다.

    * 컴퓨팅 인스턴스 및 클러스터
    * Azure Kubernetes Service
    * Azure Machine Learning 관리되는 Docker 이미지

> [!TIP]
> 시나리오 또는 태스크가 여기에 나열되지 않은 경우 보안 작업 영역의 사용 여부에 관계없이 동일하게 작동해야 합니다.

## <a name="assumptions"></a>가정

이 문서에서는 다음 구성을 가정합니다.

* 프라이빗 엔드포인트를 사용하여 VNet과 통신하는 작업 영역을 Azure Machine Learning.
* 또한 작업 영역에서 사용하는 Azure Storage 계정, Key Vault 및 Container Registry 프라이빗 엔드포인트를 사용하여 VNet과 통신합니다.
* VPN Gateway 또는 Express Route는 클라이언트 워크스테이션에서 VNet에 액세스하는 데 사용됩니다.

## <a name="inbound-and-outbound-requirements"></a>인바운드 및 아웃바운드 요구 사항


| __시나리오__ | __필수 인바운드__ | __필요한 아웃바운드__ | __추가 구성__ | 
| ----- | ----- | ----- | ----- |
| [스튜디오에서 작업 영역에 액세스](#scenario-access-workspace-from-studio) | 해당 없음 | <ul><li>Azure Active Directory</li><li>Azure Front Door</li><li>Azure Machine Learning Service</li></ul> | 사용자 지정 DNS 서버를 사용해야 할 수 있습니다. 자세한 내용은 [사용자 지정 DNS와 함께 작업 영역 사용을 참조하세요.](how-to-custom-dns.md) | 
| [Studio에서 AutoML, 디자이너, 데이터 세트 및 데이터 저장소 사용](#scenario-use-automl-designer-dataset-and-datastore-from-studio) | 해당 없음 | 해당 없음 | <ul><li>작업 영역 서비스 주체 구성</li><li>신뢰할 수 있는 Azure 서비스에서 액세스 허용</li></ul>자세한 내용은 [가상 네트워크에서 작업 영역을 보호하는 방법을 참조하세요.](how-to-secure-workspace-vnet.md#secure-azure-storage-accounts) | 
| [컴퓨팅 인스턴스 및 컴퓨팅 클러스터 사용](#scenario-use-compute-instance-and-compute-cluster) | <ul><li>포트 44224의 Azure Machine Learning 서비스</li><li>포트 29876-29877의 Azure Batch Management 서비스</li></ul> | <ul><li>Azure Active Directory</li><li>Azure Resource Manager</li><li>Azure Machine Learning Service</li><li>Azure Storage 계정</li><li>Azure Key Vault</li></ul> | 방화벽을 사용하는 경우 사용자 정의 경로를 만듭니다. 자세한 내용은 [인바운드 및 아웃바운드 트래픽 구성을 참조하세요.](how-to-access-azureml-behind-firewall.md) | 
| [Azure Kubernetes Service 사용](#scenario-use-azure-kubernetes-service) | 해당 없음 | AKS에 대한 아웃바운드 구성에 대한 자세한 내용은 [Azure Kubernetes Service 배포하는 방법을](how-to-deploy-azure-kubernetes-service.md#understand-connectivity-requirements-for-aks-inferencing-cluster)참조하세요. | 내부 Load Balancer 구성합니다. 자세한 내용은 [Azure Kubernetes Service 배포하는 방법을](how-to-deploy-azure-kubernetes-service.md#understand-connectivity-requirements-for-aks-inferencing-cluster)참조하세요. | 
| [Azure Machine Learning 관리되는 Docker 이미지 사용](#scenario-use-docker-images-managed-by-azure-ml) | 해당 없음 | <ul><li>Microsoft Container Registry</li><li>`viennaglobal.azurecr.io` 글로벌 컨테이너 레지스트리</li></ul> | 작업 영역에 대한 Azure Container Registry VNet 뒤에 있는 경우 컴퓨팅 클러스터를 사용하여 이미지를 빌드하도록 작업 영역을 구성합니다. 자세한 내용은 [가상 네트워크에서 작업 영역을 보호하는 방법을 참조하세요.](how-to-secure-workspace-vnet.md#enable-azure-container-registry-acr) | 


## <a name="scenario-access-workspace-from-studio"></a>시나리오: 스튜디오에서 작업 영역에 액세스

> [!NOTE]
> 이 섹션의 정보는 Azure Machine Learning Studio의 작업 영역 사용과 관련이 있습니다. Azure Machine Learning SDK, REST API, CLI 또는 Visual Studio Code 사용하는 경우 이 섹션의 정보는 적용되지 않습니다.

스튜디오에서 작업 영역에 액세스할 때 네트워크 트래픽 흐름은 다음과 같습니다.

* 리소스에 인증하려면 __Azure Active Directory__ 사용됩니다.
* 관리 및 배포 작업의 경우 __Azure Resource Manager__ 사용됩니다.
* Azure Machine Learning 특정 작업의 경우 __Azure Machine Learning 서비스가__ 사용됩니다.
* Azure Machine Learning Studio에 액세스하려면 ( https://ml.azure.com) , __Azure FrontDoor가__ 사용됩니다.
* 대부분의 스토리지 작업의 경우 트래픽은 작업 영역에 대한 기본 스토리지의 프라이빗 엔드포인트를 통해 흐릅니다. 예외는 [AutoML, 디자이너, 데이터 세트 및 데이터 저장소 사용](#scenario-use-automl-designer-dataset-and-datastore-from-studio) 섹션에서 설명합니다.
* 또한 VNet 내에서 리소스의 이름을 확인할 수 있는 DNS 솔루션을 구성해야 합니다. 자세한 내용은 [사용자 지정 DNS와 함께 작업 영역 사용을 참조하세요.](how-to-custom-dns.md)

:::image type="content" source="./media/concept-secure-network-traffic-flow/workspace-traffic-studio.png" alt-text="Studio를 사용하는 경우 클라이언트와 작업 영역 간의 네트워크 트래픽 다이어그램":::

## <a name="scenario-use-automl-designer-dataset-and-datastore-from-studio"></a>시나리오: Studio에서 AutoML, 디자이너, 데이터 세트 및 데이터 저장소 사용

Azure Machine Learning Studio의 다음 기능은 _데이터 프로파일링을 사용합니다._

* 데이터 세트: 스튜디오에서 데이터 세트를 탐색합니다.
* 디자이너: 모듈 출력 데이터를 시각화합니다.
* AutoML: 데이터 미리 보기/프로필을 보고 대상 열을 선택합니다.
* 레이블 지정

데이터 프로파일링은 작업 영역의 기본 Azure Storage 계정에 액세스할 수 있는 Azure Machine Learning 관리되는 서비스에 따라 달라집니다. 관리되는 서비스는 _VNet에 없으므로 VNet의_ 스토리지 계정에 직접 액세스할 수 없습니다. 대신 작업 영역에서는 서비스 주체를 사용하여 스토리지에 액세스합니다.

> [!TIP]
> 작업 영역을 만들 때 서비스 주체를 제공할 수 있습니다. 그렇지 않으면 작업 영역과 이름이 같아집니다.

스토리지 계정에 대한 액세스를 허용하려면 작업 영역에 대한 __리소스 인스턴스를__ 허용하도록 스토리지 계정을 구성하거나 신뢰할 수 있는 __서비스 목록에서 Azure 서비스가 이 스토리지 계정에 액세스하도록 허용을__ 선택합니다. 이 설정을 사용하면 관리형 서비스가 Azure 데이터 센터 네트워크를 통해 스토리지에 액세스할 수 있습니다. 

다음으로, 스토리지 계정의 프라이빗 __엔드포인트에__ 읽기 역할의 작업 영역에 대한 서비스 주체를 추가합니다. 이 역할은 작업 영역 및 스토리지 서브넷 정보를 확인하는 데 사용됩니다. 동일한 경우 액세스가 허용됩니다. 마지막으로 서비스 주체에는 스토리지 계정에 __대한 Blob 데이터 기여자__ 액세스 권한도 필요합니다.

자세한 내용은 가상 네트워크에서 작업 영역을 보호하는 방법의 Azure Storage 계정 [섹션을 참조하세요.](how-to-secure-workspace-vnet.md#secure-azure-storage-accounts)

:::image type="content" source="./media/concept-secure-network-traffic-flow/storage-traffic-studio.png" alt-text="클라이언트, 데이터 프로파일링 및 스토리지 간의 트래픽 다이어그램":::

## <a name="scenario-use-compute-instance-and-compute-cluster"></a>시나리오: 컴퓨팅 인스턴스 및 컴퓨팅 클러스터 사용

Azure Machine Learning 컴퓨팅 인스턴스 및 컴퓨팅 클러스터는 Microsoft에서 호스트하는 관리형 서비스입니다. Azure Batch 서비스를 기반으로 빌드됩니다. Microsoft 관리형 환경에 있는 동안 VNet에도 삽입됩니다.

컴퓨팅 인스턴스 또는 컴퓨팅 클러스터를 만들 때 VNet에도 다음 리소스가 만들어집니다.

* 필요한 아웃바운드 규칙이 있는 네트워크 보안 그룹입니다. 이러한 규칙은 Azure Machine Learning(포트 44224의 TCP) 및 Azure Batch 서비스(포트 29876-29877의 TCP)에서 __인바운드__ 액세스를 허용합니다.

    > [!IMPORTANT]
    > 방화벽을 사용하여 VNet에 대한 인터넷 액세스를 차단하는 경우 이 트래픽을 허용하도록 방화벽을 구성해야 합니다. 예를 들어 Azure Firewall 사용하여 사용자 정의 경로를 만들 수 있습니다. 자세한 내용은 [방화벽에서 Azure Machine Learning 사용하는 방법을 참조하세요.](how-to-access-azureml-behind-firewall.md#inbound-configuration)

* 공용 IP가 있는 부하 분산기.

또한 다음 서비스 태그에 대한 __아웃바운드__ 액세스를 허용합니다. 각 태그에 대해 `region` 을 컴퓨팅 인스턴스/클러스터의 Azure 지역으로 대체합니다.

* `Storage.region`- 이 아웃바운드 액세스는 Azure Batch 서비스 관리 VNet 내의 Azure Storage 계정에 연결하는 데 사용됩니다.
* `Keyvault.region` - 이 아웃바운드 액세스는 Azure Batch 서비스 관리 VNet 내의 Azure Key Vault 계정에 연결하는 데 사용됩니다.

컴퓨팅 인스턴스 또는 클러스터에서 데이터 액세스는 VNet에 대한 Storage 계정의 프라이빗 엔드포인트를 통과합니다.

컴퓨팅 인스턴스에서 Visual Studio Code 사용하는 경우 다른 아웃바운드 트래픽을 허용해야 합니다. 자세한 내용은 [방화벽에서 Azure Machine Learning 사용하는 방법을 참조하세요.](how-to-access-azureml-behind-firewall.md)

:::image type="content" source="./media/concept-secure-network-traffic-flow/compute-instance-and-cluster.png" alt-text="컴퓨팅 인스턴스 또는 클러스터를 사용하는 경우 트래픽 흐름 다이어그램":::

## <a name="scenario-use-azure-kubernetes-service"></a>시나리오: Azure Kubernetes Service 사용

Azure Kubernetes Service 필요한 아웃바운드 구성에 대한 자세한 내용은 Azure Kubernetes Service [에 배포하는 방법의](how-to-deploy-azure-kubernetes-service.md#understand-connectivity-requirements-for-aks-inferencing-cluster)연결 요구 사항 섹션을 참조하세요.

> [!NOTE]
> Azure Kubernetes Service 부하 분산기는 Azure Machine Learning 만든 부하 분산제와 동일하지 않습니다. VNet에서만 사용할 수 있는 보안 애플리케이션으로 모델을 호스트하려면 Azure Machine Learning 만든 내부 부하 분산기를 사용합니다. 공용 액세스를 허용하려면 Azure Machine Learning 만든 공용 부하 분산기를 사용합니다.

모델에 외부 데이터 원본과 같은 추가 인바운드 또는 아웃바운드 연결이 필요한 경우 네트워크 보안 그룹 또는 방화벽을 사용하여 트래픽을 허용합니다.

## <a name="scenario-use-docker-images-managed-by-azure-ml"></a>시나리오: Azure ML 관리되는 Docker 이미지 사용

Azure Machine Learning 모델을 학습하거나 유추를 수행하는 데 사용할 수 있는 Docker 이미지를 제공합니다. 사용자 고유의 이미지를 지정하지 않으면 Azure Machine Learning 제공된 이미지가 사용됩니다. 이러한 이미지는 Microsoft Container Registry(MCR)에서 호스트됩니다. 또한 라는 지역 복제 Azure Container Registry `viennaglobal.azurecr.io` 호스트됩니다.

제공하는 Azure Container Registry 같은 사용자 고유의 Docker 이미지를 제공하는 경우 MCR 또는 와 아웃바운드 통신이 필요하지 `viennaglobal.azurecr.io` 않습니다.

> [!TIP]
> Azure Container Registry VNet에서 보호되는 경우 Azure Machine Learning Docker 이미지를 빌드하는 데 사용할 수 없습니다. 대신 이미지를 빌드할 Azure Machine Learning 컴퓨팅 클러스터를 지정해야 합니다. 자세한 내용은 [가상 네트워크에서 작업 영역을 보호하는 방법을 참조하세요.](how-to-secure-workspace-vnet.md#enable-azure-container-registry-acr)

:::image type="content" source="./media/concept-secure-network-traffic-flow/azure-machine-learning-docker-images.png" alt-text="제공된 Docker 이미지를 사용하는 경우의 트래픽 흐름 다이어그램":::
## <a name="next-steps"></a>다음 단계

이제 보안 구성에서 네트워크 트래픽이 흐르는 방법을 배웠으므로 가상 네트워크 [격리 및 개인 정보 개요](how-to-network-security-overview.md) 문서를 참조하여 가상 네트워크에서 Azure ML 보안에 대해 자세히 알아보세요.

모범 사례에 대한 자세한 내용은 [엔터프라이즈 보안에 대한 Azure Machine Learning 모범 사례](/azure/cloud-adoption-framework/ready/azure-best-practices/ai-machine-learning-enterprise-security) 문서를 참조하세요.