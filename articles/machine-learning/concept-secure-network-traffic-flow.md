---
title: 보안 네트워크 트래픽 흐름
titleSuffix: Azure Machine Learning
description: Azure Machine Learning 작업 영역이 보안 가상 네트워크에 있는 경우 구성 요소 간에 네트워크 트래픽이 흐르는 방법에 대해 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: enterprise-readiness
ms.topic: conceptual
ms.author: jhirono
author: jhirono
ms.reviewer: larryfr
ms.date: 11/09/2021
ms.openlocfilehash: d71d986ca975b9617af9b966c8795cd30d7db01d
ms.sourcegitcommit: 838413a8fc8cd53581973472b7832d87c58e3d5f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/10/2021
ms.locfileid: "132134934"
---
# <a name="network-traffic-flow-when-using-a-secured-workspace"></a>보안 작업 영역을 사용 하는 경우 네트워크 트래픽 흐름

Azure Virtual Network에서 Azure Machine Learning 작업 영역 및 연결 된 리소스가 보호 되 면 리소스 간의 네트워크 트래픽이 변경 됩니다. 가상 네트워크가 없으면 네트워크 트래픽이 공용 인터넷을 통해 또는 Azure 데이터 센터 내에서 흐릅니다. VNet (가상 네트워크)이 도입 되 면 네트워크 보안을 강화 해야 할 수도 있습니다. 예를 들어 VNet과 공용 인터넷 간의 인바운드 및 아웃 바운드 통신을 차단 합니다. 그러나 Azure Machine Learning는 공용 인터넷의 일부 리소스에 대 한 액세스가 필요 합니다. 예를 들어 Azure 리소스 관리는 배포 및 관리 작업에 사용 됩니다.

이 문서에서는 공용 인터넷에 대 한 필수 트래픽을 나열 합니다. 또한 다음 시나리오에서 클라이언트 개발 환경과 보안 Azure Machine Learning 작업 영역 간에 네트워크 트래픽이 흐르는 방식을 설명 합니다.

* Azure Machine Learning __studio__ 를 사용 하 여 작업 합니다.

    * 작업 영역
    * AutoML
    * 디자이너
    * 데이터 세트 및 데이터 저장소

    > [!TIP]
    > Azure Machine Learning studio는 웹 브라우저에서 부분적으로 실행 되는 웹 기반 UI로, Azure 서비스를 호출 하 여 모델 학습, 디자이너 사용, 데이터 집합 보기 등의 작업을 수행 합니다. 이러한 호출 중 일부는 SDK, CLI, REST API 또는 VS Code를 사용 하는 경우와 다른 통신 흐름을 사용 합니다.

* Azure Machine Learning __studio__, __SDK__, __CLI__ 또는 __REST API__ 를 사용 하 여 작업 합니다.

    * 계산 인스턴스 및 클러스터
    * Azure Kubernetes Service
    * Azure Machine Learning에서 관리 하는 Docker 이미지

> [!TIP]
> 시나리오 또는 작업이 여기에 나열 되지 않으면 보안 작업 영역을 사용 하거나 사용 하지 않고 동일한 작업을 수행 해야 합니다.

## <a name="assumptions"></a>가정

이 문서에서는 다음 구성을 가정 합니다.

* VNet과 통신 하기 위해 개인 끝점을 사용 하 여 작업 영역을 Azure Machine Learning 합니다.
* 작업 영역에서 사용 하는 Azure Storage 계정, Key Vault 및 Container Registry는 VNet과 통신 하는 데에도 개인 끝점을 사용 합니다.
* 클라이언트 워크스테이션에서 VNet에 액세스 하기 위해 VPN gateway 또는 Express 경로를 사용 합니다.

## <a name="inbound-and-outbound-requirements"></a>인바운드 및 아웃 바운드 요구 사항


| __시나리오__ | __필수 인바운드__ | __필수 아웃 바운드__ | __추가 구성__ | 
| ----- | ----- | ----- | ----- |
| [Studio에서 작업 영역 액세스](#scenario-access-workspace-from-studio) | 해당 없음 | <ul><li>Azure Active Directory</li><li>Azure Front Door</li><li>Azure Machine Learning Service</li></ul> | 사용자 지정 DNS 서버를 사용 해야 할 수도 있습니다. 자세한 내용은 [사용자 지정 DNS로 작업 영역 사용](how-to-custom-dns.md)을 참조 하세요. | 
| [Studio에서 AutoML, designer, dataset 및 데이터 저장소 사용](#scenario-use-automl-designer-dataset-and-datastore-from-studio) | 해당 없음 | 해당 없음 | <ul><li>작업 영역 서비스 주체 구성</li><li>신뢰할 수 있는 Azure 서비스에서의 액세스 허용</li></ul>자세한 내용은 [가상 네트워크에서 작업 영역을 보호 하는 방법](how-to-secure-workspace-vnet.md#secure-azure-storage-accounts)을 참조 하세요. | 
| [계산 인스턴스 및 계산 클러스터 사용](#scenario-use-compute-instance-and-compute-cluster) | <ul><li>포트 44224의 Azure Machine Learning 서비스</li><li>포트 29876-29877에서 Azure Batch 관리 서비스</li></ul> | <ul><li>Azure Active Directory</li><li>Azure Resource Manager</li><li>Azure Machine Learning Service</li><li>Azure Storage 계정</li><li>Azure Key Vault</li></ul> | 방화벽을 사용 하는 경우 사용자 정의 경로를 만듭니다. 자세한 내용은 [인바운드 및 아웃 바운드 트래픽 구성](how-to-access-azureml-behind-firewall.md)을 참조 하세요. | 
| [Azure Kubernetes Service 사용](#scenario-use-azure-kubernetes-service) | 해당 없음 | AKS에 대 한 아웃 바운드 구성에 대 한 자세한 내용은 [Azure Kubernetes Service에 배포 하는 방법](how-to-deploy-azure-kubernetes-service.md#understand-connectivity-requirements-for-aks-inferencing-cluster)을 참조 하세요. | 내부 Load Balancer를 구성 합니다. 자세한 내용은 [Azure Kubernetes Service에 배포 하는 방법](how-to-deploy-azure-kubernetes-service.md#understand-connectivity-requirements-for-aks-inferencing-cluster)을 참조 하세요. | 
| [Azure Machine Learning에서 관리 하는 Docker 이미지 사용](#scenario-use-docker-images-managed-by-azure-ml) | 해당 없음 | <ul><li>Microsoft Container Registry</li><li>`viennaglobal.azurecr.io` 전역 컨테이너 레지스트리</li></ul> | 작업 영역에 대 한 Azure Container Registry VNet 뒤에 있는 경우 계산 클러스터를 사용 하 여 이미지를 빌드하기 위해 작업 영역을 구성 합니다. 자세한 내용은 [가상 네트워크에서 작업 영역을 보호 하는 방법](how-to-secure-workspace-vnet.md#enable-azure-container-registry-acr)을 참조 하세요. | 

> [!IMPORTANT]
> Azure Machine Learning는 여러 저장소 계정을 사용 합니다. 각는 서로 다른 데이터를 저장 하 고 용도가 다릅니다.
>
> * __저장소__: Azure 구독의 Azure Storage 계정은 데이터와 아티팩트 (예: 모델, 학습 데이터, 학습 로그 및 Python 스크립트)를 저장 하는 데 사용 됩니다. 예를 들어 작업 영역에 대 한 _기본_ 저장소 계정은 구독에 있습니다. Azure Machine Learning 계산 인스턴스 및 계산 클러스터는 포트 445 (SMB) 및 443 (HTTPS)를 통해이 저장소의 __파일__ 및 __blob__ 데이터에 액세스 합니다.
> 
>    계산 인스턴스 또는 계산 클러스터를 사용할 때 저장소 계정은 SMB 프로토콜을 사용 하 여 파일 공유로 탑재 됩니다. 계산 인스턴스/클러스터가 데이터에 액세스 하는 방법입니다.
>
> * __microsoft 저장소__: Azure Machine Learning 계산 인스턴스 및 계산 클러스터는 Azure Batch를 사용 하 고 Microsoft 구독에 있는 저장소에 액세스 합니다. 이 저장소는 계산 인스턴스/클러스터를 관리 하는 데만 사용 됩니다. 여기에는 데이터가 저장 되지 않습니다. 계산 인스턴스 및 계산 클러스터는 포트 443 (HTTPS)를 사용 하 여이 저장소의 __blob__, __테이블__ 및 __큐__ 데이터에 액세스 합니다.
>
> 또한 Machine Learning는 Azure Cosmos DB 인스턴스에 메타 데이터를 저장 합니다. 기본적으로이 인스턴스는 Microsoft 구독에서 호스팅되며 Microsoft에서 관리 합니다. 필요에 따라 Azure 구독에서 Azure Cosmos DB 인스턴스를 사용할 수 있습니다. 자세한 내용은 [Azure Machine Learning를 사용 하 여 데이터 암호화](concept-data-encryption.md#azure-cosmos-db)를 참조 하세요.

## <a name="scenario-access-workspace-from-studio"></a>시나리오: studio에서 작업 영역 액세스

> [!NOTE]
> 이 섹션의 정보는 Azure Machine Learning studio에서 작업 영역을 사용 하는 것과 관련이 있습니다. Azure Machine Learning SDK, REST API, CLI 또는 Visual Studio Code를 사용 하는 경우이 섹션의 정보는 적용 되지 않습니다.

Studio에서 작업 영역에 액세스 하는 경우 네트워크 트래픽 흐름은 다음과 같습니다.

* 리소스에 인증 하기 위해 __Azure Active Directory__ 사용 됩니다.
* 관리 및 배포 작업의 경우 __Azure Resource Manager__ 사용 됩니다.
* Azure Machine Learning 특정 작업의 경우 __Azure Machine Learning 서비스가__ 사용 됩니다.
* Azure Machine Learning studio (에 대 한 액세스를 위해 https://ml.azure.com) __Azure FrontDoor__ 가 사용 됩니다.
* 대부분의 저장소 작업의 경우 트래픽은 작업 영역에 대 한 기본 저장소의 개인 끝점을 통해 흐릅니다. 예외는 [AutoML, 디자이너, 데이터 집합 및 데이터 저장소 사용](#scenario-use-automl-designer-dataset-and-datastore-from-studio) 섹션에 설명 되어 있습니다.
* 또한 VNet 내의 리소스 이름을 확인할 수 있는 DNS 솔루션을 구성 해야 합니다. 자세한 내용은 [사용자 지정 DNS로 작업 영역 사용](how-to-custom-dns.md)을 참조 하세요.

:::image type="content" source="./media/concept-secure-network-traffic-flow/workspace-traffic-studio.png" alt-text="Studio를 사용 하는 경우 클라이언트와 작업 영역 간의 네트워크 트래픽 다이어그램":::

## <a name="scenario-use-automl-designer-dataset-and-datastore-from-studio"></a>시나리오: studio에서 AutoML, designer, dataset 및 데이터 저장소 사용

Azure Machine Learning studio의 다음 기능은 _데이터 프로 파일링_ 을 사용 합니다.

* 데이터 집합: studio에서 데이터 집합을 탐색 합니다.
* Designer: 모듈 출력 데이터를 시각화 합니다.
* AutoML: 데이터 미리 보기/프로필을 보고 대상 열을 선택 합니다.
* 레이블 지정

데이터 프로 파일링은 작업 영역에 대 한 기본 Azure Storage 계정에 액세스할 수 있는 Azure Machine Learning 관리 서비스에 따라 달라 집니다. 관리 서비스가 _vnet_ 에 없으므로 vnet에서 저장소 계정에 직접 액세스할 수 없습니다. 대신 작업 영역에서는 서비스 주체를 사용 하 여 저장소에 액세스 합니다.

> [!TIP]
> 작업 영역을 만들 때 서비스 주체를 제공할 수 있습니다. 그렇지 않으면 사용자를 위해 생성 되 고 작업 영역과 동일한 이름을 갖게 됩니다.

저장소 계정에 대 한 액세스를 허용 하려면 작업 영역에 대 한 __리소스 인스턴스__ 를 허용 하도록 저장소 계정을 구성 하거나 __신뢰할 수 있는 서비스에 대 한 Azure 서비스 허용 목록에서이 저장소 계정에 액세스 하도록 허용__ 을 선택 합니다. 이 설정을 통해 관리 되는 서비스는 Azure 데이터 센터 네트워크를 통해 저장소에 액세스할 수 있습니다. 

그런 다음 작업 영역에 대 한 서비스 주체를 저장소 계정의 개인 끝점에 대 한 __읽기 권한자__ 역할에 추가 합니다. 이 역할은 작업 영역 및 저장소 서브넷 정보를 확인 하는 데 사용 됩니다. 동일한 경우 액세스가 허용 됩니다. 마지막으로, 서비스 주체는 저장소 계정에 대 한 __Blob 데이터 참가자__ 액세스 권한도 필요 합니다.

자세한 내용은 [가상 네트워크에서 작업 영역을 보호 하는 방법](how-to-secure-workspace-vnet.md#secure-azure-storage-accounts)의 Azure Storage 계정 섹션을 참조 하세요.

:::image type="content" source="./media/concept-secure-network-traffic-flow/storage-traffic-studio.png" alt-text="클라이언트, 데이터 프로 파일링 및 저장소 간의 트래픽 다이어그램":::

## <a name="scenario-use-compute-instance-and-compute-cluster"></a>시나리오: 계산 인스턴스 및 계산 클러스터 사용

계산 인스턴스 및 계산 클러스터 Azure Machine Learning Microsoft에서 호스팅하는 관리 되는 서비스입니다. 이러한 서비스는 Azure Batch 서비스를 기반으로 빌드됩니다. Microsoft 관리 환경에 존재 하는 동안에는 VNet에도 삽입 됩니다.

계산 인스턴스 또는 계산 클러스터를 만들 때 VNet에서 다음 리소스도 생성 됩니다.

* 필요한 아웃 바운드 규칙을 사용 하는 네트워크 보안 그룹입니다. 이러한 규칙은 Azure Machine Learning (포트 44224의 tcp) 및 Azure Batch 서비스 (포트 29876-29877의 tcp)에서 __인바운드__ 액세스를 허용 합니다.

    > [!IMPORTANT]
    > 방화벽을 통해 VNet에 대 한 인터넷 액세스를 차단 하는 경우이 트래픽을 허용 하도록 방화벽을 구성 해야 합니다. 예를 들어 Azure 방화벽을 사용 하면 사용자 정의 경로를 만들 수 있습니다. 자세한 내용은 [방화벽과 함께 Azure Machine Learning를 사용 하는 방법](how-to-access-azureml-behind-firewall.md#inbound-configuration)을 참조 하세요.

* 공용 IP가 있는 부하 분산 장치

또한 다음 서비스 태그에 대 한 __아웃 바운드__ 액세스를 허용 합니다. 각 태그에 대해를 `region` 계산 인스턴스/클러스터의 Azure 지역으로 바꿉니다.

* `Storage.region`-이 아웃 바운드 액세스는 Azure Batch service 관리 VNet 내의 Azure Storage 계정에 연결 하는 데 사용 됩니다.
* `Keyvault.region` -이 아웃 바운드 액세스는 Azure Batch service 관리 VNet 내의 Azure Key Vault 계정에 연결 하는 데 사용 됩니다.

계산 인스턴스 또는 클러스터의 데이터 액세스는 VNet에 대 한 Storage 계정의 개인 끝점을 거칩니다.

계산 인스턴스에서 Visual Studio Code를 사용 하는 경우 다른 아웃 바운드 트래픽을 허용 해야 합니다. 자세한 내용은 [방화벽과 함께 Azure Machine Learning를 사용 하는 방법](how-to-access-azureml-behind-firewall.md)을 참조 하세요.

:::image type="content" source="./media/concept-secure-network-traffic-flow/compute-instance-and-cluster.png" alt-text="계산 인스턴스 또는 클러스터를 사용할 때의 트래픽 흐름 다이어그램":::

## <a name="scenario-use-azure-kubernetes-service"></a>시나리오: Azure Kubernetes Service 사용

Azure Kubernetes Service에 필요한 아웃 바운드 구성에 대 한 자세한 내용은 [Azure Kubernetes service에 배포 하는 방법](how-to-deploy-azure-kubernetes-service.md#understand-connectivity-requirements-for-aks-inferencing-cluster)의 연결 요구 사항 섹션을 참조 하세요.

> [!NOTE]
> Azure Kubernetes Service 부하 분산 장치는 Azure Machine Learning에서 만든 부하 분산 장치와 동일 하지 않습니다. 모델을 VNet 에서만 사용할 수 있는 보안 응용 프로그램으로 호스트 하려면 Azure Machine Learning 만든 내부 부하 분산 장치를 사용 합니다. 공용 액세스를 허용 하려면 Azure Machine Learning에서 만든 공용 부하 분산 장치를 사용 합니다.

외부 데이터 원본과 같은 추가 인바운드 또는 아웃 바운드 연결이 모델에 필요한 경우 네트워크 보안 그룹 또는 방화벽을 사용 하 여 트래픽을 허용 합니다.

## <a name="scenario-use-docker-images-managed-by-azure-ml"></a>시나리오: Azure ML에서 관리 하는 Docker 이미지 사용

Azure Machine Learning은 모델을 학습 하거나 유추를 수행 하는 데 사용할 수 있는 Docker 이미지를 제공 합니다. 사용자 고유의 이미지를 지정 하지 않으면 Azure Machine Learning에서 제공 하는 이미지를 사용 합니다. 이러한 이미지는 Microsoft Container Registry (MCR)에서 호스팅됩니다. 또한 명명 된 지역 복제 Azure Container Registry에 호스팅됩니다 `viennaglobal.azurecr.io` .

사용자가 제공 하는 Azure Container Registry와 같이 사용자 고유의 docker 이미지를 제공 하는 경우 MCR 또는과의 아웃 바운드 통신이 필요 하지 않습니다 `viennaglobal.azurecr.io` .

> [!TIP]
> Azure Container Registry VNet에서 보호 되는 경우에는 Azure Machine Learning에서 Docker 이미지를 빌드하는 데 사용할 수 없습니다. 대신 이미지를 빌드하기 위해 Azure Machine Learning 계산 클러스터를 지정 해야 합니다. 자세한 내용은 [가상 네트워크에서 작업 영역을 보호 하는 방법](how-to-secure-workspace-vnet.md#enable-azure-container-registry-acr)을 참조 하세요.

:::image type="content" source="./media/concept-secure-network-traffic-flow/azure-machine-learning-docker-images.png" alt-text="제공 된 Docker 이미지를 사용 하는 경우 트래픽 흐름 다이어그램":::
## <a name="next-steps"></a>다음 단계

이제 보안 구성에서 네트워크 트래픽이 흐르는 방법을 배웠으므로 [가상 네트워크 격리 및 개인 정보 개요](how-to-network-security-overview.md) 문서를 참조 하 여 가상 네트워크에서 Azure ML 보안에 대해 자세히 알아보세요.

모범 사례에 대 한 자세한 내용은 [엔터프라이즈 보안을 위한 Azure Machine Learning 모범 사례](/azure/cloud-adoption-framework/ready/azure-best-practices/ai-machine-learning-enterprise-security) 문서를 참조 하세요.