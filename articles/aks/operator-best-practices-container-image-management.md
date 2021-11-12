---
title: 운영자 모범 사례 - AKS(Azure Kubernetes Services)의 컨테이너 이미지 관리
description: AKS(Azure Kubernetes Services)에서 컨테이너 이미지를 관리하고 보안을 유지하는 방법에 대한 클러스터 운영자 모범 사례 알아보기
services: container-service
ms.topic: conceptual
ms.date: 03/11/2021
ms.openlocfilehash: e6c767d1fdf765739f361b91df21e5bc104bc171
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132306532"
---
# <a name="best-practices-for-container-image-management-and-security-in-azure-kubernetes-service-aks"></a>AKS(Azure Kubernetes Services)의 컨테이너 이미지 관리 및 보안에 대한 모범 사례

컨테이너 및 컨테이너 이미지 보안은 AKS(Azure Kubernetes Service)에서 애플리케이션을 개발하고 실행하는 동안 중요한 우선 순위입니다. 오래된 기본 이미지 또는 패치되지 않은 애플리케이션 런타임이 포함된 컨테이너는 보안 위험 및 가능한 공격 벡터를 초래합니다. 

빌드 및 런타임에 컨테이너에서 검사 및 수정 도구를 통합하고 실행하여 위험을 최소화합니다. 취약성 또는 오래된 기본 이미지를 더 일찍 포착할수록 클러스터의 보안이 높아질 수 있습니다. 

이 문서에서 *"컨테이너"* 는 다음을 모두 의미합니다.
* 컨테이너 레지스트리에 저장된 컨테이너 이미지
* 실행 중인 컨테이너

이 문서에서는 AKS의 컨테이너 보안 유지 방법을 중점적으로 설명합니다. 다음 방법을 알아봅니다.

> [!div class="checklist"]
> * 이미지 취약성 검색 및 해결
> * 기본 이미지가 업데이트될 때 컨테이너 이미지를 자동으로 트리거하고 다시 배포

[클러스터 보안][best-practices-cluster-security] 및 [Pod 보안][best-practices-pod-security]에 대한 모범 사례를 참조할 수도 있습니다.

또한 [Defender For Cloud의 컨테이너 보안][security-center-containers] 을 사용 하 여 컨테이너에서 취약성을 검색 하는 데 도움이 될 수 있습니다. 클라우드에 대 한 Defender와 [Azure Container Registry 통합][security-center-acr] 하면 취약성 으로부터 이미지 및 레지스트리를 보호할 수 있습니다.

## <a name="secure-the-images-and-run-time"></a>이미지 및 런타임 보호

> **모범 사례 지침** 
>
> 취약성에 대해 컨테이너 이미지를 검사합니다. 유효성이 검사된 이미지만 배포합니다. 기본 이미지 및 애플리케이션 런타임을 정기적으로 업데이트합니다. AKS 클러스터에서 워크로드를 다시 배포합니다.

컨테이너 기반 워크로드를 채택할 때 사용자 고유의 애플리케이션을 빌드하는 데 사용되는 이미지 및 런타임의 보안을 확인하는 것이 좋습니다. 배포에 보안 취약성을 도입하지 않으려면 어떻게 해야 하나요? 
* [Twistlock][twistlock] 또는 [Aqua][aqua]와 같은 도구를 사용하여 컨테이너 이미지를 검사하는 프로세스를 배포 워크플로에 포함시킵니다.
* 확인된 이미지만 배포할 수 있습니다.

![컨테이너 이미지 검색 및 수정, 유효성 검사 후 배포](media/operator-best-practices-container-security/scan-container-images-simplified.png)

예를 들어 CI/CD(연속 통합/지속적인 배포) 파이프라인을 사용하여 이미지 검색, 확인 및 배포를 자동화할 수 있습니다. Azure Container Registry에는 이러한 취약성 검사 기능이 포함되어 있습니다.

## <a name="automatically-build-new-images-on-base-image-update"></a>기본 이미지 업데이트 시 새 이미지를 자동으로 빌드

> **모범 사례 지침** 
>
> 애플리케이션 이미지에 대해 기본 이미지를 사용할 경우 기본 이미지가 업데이트될 때 자동화를 통해 새 이미지를 빌드합니다. 업데이트된 기본 이미지에는 일반적으로 보안 수정 사항이 포함되어 있으므로 모든 다운스트림 애플리케이션 컨테이너 이미지를 업데이트합니다.

기본 이미지가 업데이트될 때마다 모든 다운스트림 컨테이너 이미지도 업데이트해야 합니다. 이 빌드 프로세스를 [Azure Pipelines][azure-pipelines] 또는 Jenkins와 같은 유효성 검사 및 배포 파이프라인에 통합합니다. 이러한 파이프라인은 애플리케이션이 업데이트된 기본 이미지를 계속 실행하도록 합니다. 애플리케이션 컨테이너 이미지의 유효성이 검사되면 안전한 최신 이미지를 실행하도록 AKS 배포를 업데이트할 수 있습니다.

또한 기본 이미지가 업데이트되면 Azure Container Registry 작업도 컨테이너 이미지를 자동으로 업데이트할 수 있습니다. 이 기능을 사용하면 몇 가지 기본 이미지를 빌드하고 버그 및 보안 수정으로 업데이트를 유지할 수 있습니다.

기본 이미지 업데이트에 대한 자세한 내용은 [Azure Container Registry 작업을 사용하여 기본 이미지 업데이트 시 이미지 빌드 자동화][acr-base-image-update]를 참조하세요.

## <a name="next-steps"></a>다음 단계

이 문서에서는 컨테이너를 보호하는 방법을 중점적으로 설명했습니다. 이러한 일부 영역을 구현하려면 다음 문서를 참조하세요.

* [Azure Container Registry 작업을 사용하여 기본 이미지 업데이트 시 이미지 빌드 자동화][acr-base-image-update]

<!-- EXTERNAL LINKS -->
[azure-pipelines]: /azure/devops/pipelines/
[twistlock]: https://www.twistlock.com/
[aqua]: https://www.aquasec.com/

<!-- INTERNAL LINKS -->
[best-practices-cluster-security]: operator-best-practices-cluster-security.md
[best-practices-pod-security]: developer-best-practices-pod-security.md
[acr-base-image-update]: ../container-registry/container-registry-tutorial-base-image-update.md
[security-center-containers]: ../security-center/container-security.md
[security-center-acr]: ../security-center/defender-for-container-registries-introduction.md
