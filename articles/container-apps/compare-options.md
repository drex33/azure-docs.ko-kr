---
title: 다른 Azure 컨테이너 옵션과 Container Apps 비교
description: Azure Container Apps를 사용하는 시기와 Azure Container Instances, Azure App Service, Azure Functions 및 Azure Kubernetes Service를 포함한 d 컨테이너 옵션과 비교하는 방법을 이해합니다.
services: container-apps
author: jeffhollan
ms.service: container-apps
ms.topic: quickstart
ms.date: 11/03/2021
ms.author: jehollan
ms.custom: ignite-fall-2021, mode-other
ms.openlocfilehash: 5bbd7a318fbeef6229856e98c01b2764cfb28075
ms.sourcegitcommit: 56235f8694cc5f88db3afcc8c27ce769ecf455b0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133051039"
---
# <a name="comparing-container-apps-with-other-azure-container-options"></a>다른 Azure 컨테이너 옵션과 Container Apps 비교

팀이 Azure에서 클라우드 원시 및 컨테이너화된 애플리케이션을 빌드하고 배포할 수 있는 다양한 옵션이 있습니다. 이 문서는 Azure Container Apps에 가장 적합한 시나리오 및 사용 사례와 다음을 비롯한 Azure의 다른 컨테이너 옵션과 비교하는 방법을 이해하는 데 도움이 됩니다.  
- [Azure App Service](#azure-app-service)
- [Azure Container Instances](#azure-container-instances)
- [Azure Kubernetes Service](#azure-kubernetes-service)
- [Azure Functions](#azure-functions)
- [Azure Spring Cloud](#azure-spring-cloud)

모든 사용 사례 및 모든 팀에 대한 완벽한 솔루션은 없습니다. 다음 설명에서는 팀 및 요구 사항에 가장 적합한 것을 찾는 데 도움이 되는 일반적인 참고 자료와 권장 사항을 시작점으로 제공합니다.

> [!IMPORTANT]
> Azure Container Apps는 현재 공개 미리 보기로 제공되며, 이러한 다른 옵션은 GA(일반 공급)입니다.


## <a name="container-option-comparisons"></a>컨테이너 옵션 비교

### <a name="azure-container-apps"></a>Azure Container Apps
Azure Container Apps를 사용하면 컨테이너를 기반으로 서버리스 마이크로 서비스를 빌드할 수 있습니다. Container Apps의 고유한 기능은 다음과 같습니다.

* 범용 컨테이너를 실행하는 데 최적화되었으며, 특히 컨테이너에 배포된 많은 마이크로 서비스에 걸쳐 있는 애플리케이션에 최적화되어 있습니다.
* Kubernetes 및 [Dapr](https://dapr.io/), [KEDA](https://keda.sh/) 및 [envoy](https://www.envoyproxy.io/)와 같은 오픈 소스 기술을 통해 제공됩니다.
* [서비스 검색](connect-apps.md) 및 [트래픽 분할](revisions.md)과 같은 기능을 통해 Kubernetes 스타일 앱 및 마이크로 서비스를 지원합니다.
* 트래픽에 따라 비율 크기 조정을 지원하고 [0으로 비율 크기 조정](scale-app.md)을 포함하여 [큐와 같은 이벤트 원본](scale-app.md)에서 끌어와서 이벤트 기반 애플리케이션 아키텍처를 사용하도록 설정합니다.
* 장기 실행 프로세스를 지원하고 [백그라운드 작업](background-processing.md)을 실행할 수 있습니다.

Azure Container Apps는 기본 Kubernetes API에 대한 직접 액세스를 제공하지 않습니다. Kubernetes API 및 컨트롤 플레인에 액세스해야 하는 경우 [Azure Kubernetes Service](../aks/intro-kubernetes.md)를 사용해야 합니다. 그러나 Kubernetes 스타일 애플리케이션을 빌드하고 모든 원시 Kubernetes API 및 클러스터 관리에 직접 액세스할 필요가 없는 경우 Container Apps는 모범 사례를 기반으로 완전 관리형 환경을 제공합니다. 이러한 이유로 많은 팀에서 Azure Container Apps를 사용하여 컨테이너 마이크로 서비스 빌드를 시작하는 것을 선호할 수 있습니다.

### <a name="azure-app-service"></a>Azure App Service
Azure App Service는 웹 사이트 및 웹 API를 비롯한 웹 애플리케이션에 대한 완전 관리형 호스팅을 제공합니다. 이러한 웹 애플리케이션은 코드 또는 컨테이너를 사용하여 배포할 수 있습니다. Azure App Service는 웹 애플리케이션에 최적화되어 있습니다. Azure App Service는 Azure Container Apps 또는 Azure Functions를 비롯한 다른 Azure 서비스와 통합됩니다. 웹앱을 빌드할 때 Azure App Service는 이상적인 옵션입니다.

### <a name="azure-container-instances"></a>Azure Container Instances
ACI(Azure Container Instances)는 주문형 Hyper-V 격리 컨테이너의 단일 Pod를 제공합니다. Container Apps에 비해 하위 수준 "빌딩 블록" 옵션으로 간주할 수 있습니다. 비율 크기 조정, 부하 분산 및 인증서와 같은 개념은 ACI 컨테이너와 함께 제공되지 않습니다. 예를 들어 5개의 컨테이너 인스턴스로 확장하려면 5개의 고유한 컨테이너 인스턴스를 만듭니다. Azure Container Apps는 인증서, 수정 버전, 비율 크기 조정 및 환경을 포함하여 컨테이너를 기반으로 하는 다양한 애플리케이션 관련 개념을 제공합니다. 사용자는 종종 다른 서비스를 통해 Azure Container Instances 상호 작용합니다. 예를 들어 Azure Kubernetes Service [가상 노드](../aks/virtual-nodes.md)를 통해 오케스트레이션을 레이어하고 ACI 위에 확장할 수 있습니다. Azure Container Apps가 최적화하는 시나리오와 맞지 않는 덜 "의견 있는" 빌딩 블록이 필요한 경우 Azure Container Instances가 이상적인 옵션입니다.

### <a name="azure-kubernetes-service"></a>Azure Kubernetes Service
Azure Kubernetes Service가 Azure에서 완전 관리형 Kubernetes 옵션을 제공합니다. Kubernetes API에 대한 직접 액세스를 지원하고 모든 Kubernetes 워크로드를 실행합니다. 전체 클러스터는 사용자의 제어 및 책임 내에서 클러스터 구성 및 작업을 통해 구독에 상주합니다. Azure에서 Kubernetes의 완전 관리형 버전을 찾는 팀의 경우 Azure Kubernetes Service가 이상적인 옵션입니다.

### <a name="azure-functions"></a>Azure Functions
Azure Functions는 서버리스 FaaS(Functions-as-a-Service) 솔루션입니다. 함수 프로그래밍 모델을 사용하여 이벤트 기반 함수를 실행하는 데 최적화되어 있습니다. 규모 및 이벤트와의 통합과 관련하여 Azure Container Apps와 많은 특징을 공유하지만 코드 또는 컨테이너로 배포된 임시 함수에 최적화되어 있습니다. Azure Functions 프로그래밍 모델은 이벤트를 트리거하고 다른 데이터 원본에 바인딩하려는 팀에 생산성 혜택을 제공합니다. FaaS 스타일 함수를 빌드할 때 Azure Functions는 이상적인 옵션입니다. Azure Functions 프로그래밍 모델은 베이스 컨테이너 이미지로 사용할 수 있으며, 환경 요구 사항이 변경되면 팀이 코드를 다시 사용할 수 있도록 다른 컨테이너 기반 컴퓨팅 플랫폼으로 이식할 수 있습니다.

### <a name="azure-spring-cloud"></a>Azure Spring Cloud
Azure Spring Cloud를 사용하면 코드 변경 없이 Spring Boot 마이크로서비스 애플리케이션을 Azure에 쉽게 배포할 수 있습니다. 이 서비스는 개발자가 코드에 집중할 수 있도록 Spring Cloud 애플리케이션의 인프라를 관리합니다. Azure Spring Cloud는 포괄적인 모니터링 및 진단, 구성 관리, 서비스 검색, CI/CD 통합, 청록색 배포 등을 사용하여 수명 주기 관리를 제공합니다. 팀 또는 조직이 주로 Spring인 경우 Azure Spring Cloud가 이상적인 옵션입니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [첫 번째 컨테이너 앱 배포](get-started.md)
