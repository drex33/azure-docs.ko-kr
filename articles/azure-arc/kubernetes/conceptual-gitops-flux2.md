---
title: 개념적 개요 Azure Kubernetes 구성 관리(GitOps)
description: 이 문서에서는 AKS(Azure Arc 지원 Kubernetes 및 Azure Kubernetes Service) 클러스터에서 사용하기 위해 Azure의 GitOps에 대한 개념적 개요를 제공합니다.
keywords: GitOps, Flux, Kubernetes, K8s, Azure, Arc, AKS, Azure Kubernetes Service, 컨테이너, devops
services: azure-arc, aks
ms.service: azure-arc
ms.date: 11/22/2021
ms.topic: conceptual
author: csand-msft
ms.author: csand
ms.openlocfilehash: 5b80d5f5fc2ea1f9e523f3d0e88bd34546e0262f
ms.sourcegitcommit: cae9bf0cad514c974c0c0185e24fd4b4b3132432
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/01/2021
ms.locfileid: "133407911"
---
# <a name="gitops-in-azure"></a>Azure의 GitOps

Azure는 AZURE ARC 지원 Kubernetes 및 AKS(Azure Kubernetes Service) 클러스터에서 GitOps를 사용하여 구성 관리 기능을 제공합니다. 이러한 클러스터에서 GitOps를 쉽게 사용하도록 설정하고 사용할 수 있습니다.

GitOps를 사용하면 Git 리포지토리의 파일에서 Kubernetes 클러스터의 원하는 상태를 선언합니다. Git 리포지토리에는 다음 파일이 포함될 수 있습니다.

* Kubernetes 리소스(예: 네임스페이스, 비밀, 배포 등)를 설명하는 [YAML 형식 매니페스트](https://yaml.org/)
* 애플리케이션 배포를 위한 [Helm 차트](https://helm.sh/docs/topics/charts/)
* 환경별 변경 내용을 설명하는 [Kustomize 파일](https://kustomize.io/)

이러한 파일은 Git 리포지토리에 저장되므로 버전이 관리되며 버전 간의 변경 내용을 쉽게 추적할 수 있습니다. Kubernetes 컨트롤러는 클러스터에서 실행되며 Git 리포지토리에 선언된 원하는 상태로 클러스터 상태를 지속적으로 조정합니다. 이러한 연산자는 Git 리포지토리에서 파일을 끌어와 원하는 상태를 클러스터에 적용합니다. 또한 운영자는 클러스터가 원하는 상태로 유지되는 것을 지속적으로 보장합니다.

Azure Arc 지원 Kubernetes 또는 Azure Kubernetes Service GitOps는 인기 있는 오픈 소스 도구 집합인 [Flux를](https://fluxcd.io/docs/)사용합니다. Flux는 일반적인 파일 원본(Git 및 Helm 리포지토리, 버킷) 및 템플릿 형식(YAML, Helm 및 Kustomize)을 지원합니다. Flux는 [다른 기능](https://fluxcd.io/docs/)중에서 다중 테넌트 및 배포 종속성 관리도 지원합니다.

## <a name="flux-cluster-extension"></a>Flux 클러스터 확장

:::image type="content" source="media/gitops/flux2-extension-install-arc.png" alt-text="Azure Arc 지원 Kubernetes 클러스터에 대한 Flux 확장 설치를 보여 주는 다이어그램" lightbox="media/gitops/flux2-extension-install-arc.png":::

:::image type="content" source="media/gitops/flux2-extension-install-aks.png" alt-text="Azure Kubernetes Service 클러스터용 Flux 확장 설치를 보여 주는 다이어그램" lightbox="media/gitops/flux2-extension-install-aks.png":::

GitOps는 Azure Arc 지원 Kubernetes 또는 AKS 클러스터에서 `Microsoft.KubernetesConfiguration/extensions/microsoft.flux` [클러스터 확장](./conceptual-extensions.md) 리소스로 사용하도록 설정됩니다.  포털 또는 `microsoft.flux` *Azure CLI(az k8s-extension create --extensionType=microsoft.flux)를* 사용하여 확장을 수동으로 설치하거나 클러스터에서 첫 번째 를 만들 때 자동으로 설치할 수 `Microsoft.KubernetesConfiguration/fluxConfigurations` 있습니다. `microsoft.flux`하나 이상의 확장을 만들려면 먼저 클러스터에 확장을 설치해야 `fluxConfigurations` 합니다.

`microsoft.flux`확장은 기본적으로 [Flux](https://fluxcd.io/docs/components/) 컨트롤러(Source, Kustomize, Helm, Notification) 및 FluxConfig CRD, fluxconfig-agent 및 fluxconfig-controller를 설치합니다. 이러한 컨트롤러 중 어떤 컨트롤러가 설치되어 있는지 제어할 수 있으며 선택적으로 Docker 이미지 업데이트 및 검색과 관련된 기능을 제공하는 Flux 이미지 자동화 및 이미지 리플렉터 컨트롤러를 설치할 수 있습니다.

* [Flux 원본 컨트롤러:](https://toolkit.fluxcd.io/components/source/controller/)source.toolkit.fluxcd.io 사용자 지정 리소스를 감시합니다. Git 리포지토리, Helm 리포지토리 및 버킷 간의 동기화를 처리합니다. 프라이빗 Git 및 Helm 리포지토리에 대한 원본을 통해 권한 부여를 처리합니다. tar 보관 파일을 통해 원본에 대한 최신 변경 내용을 표면화합니다.
* [Flux Kustomize 컨트롤러:](https://toolkit.fluxcd.io/components/kustomize/controller/) `kustomization.toolkit.fluxcd.io` 사용자 지정 리소스를 감시합니다. 원본의 Kustomize 또는 원시 YAML 파일을 클러스터에 적용합니다.
* [Flux Helm 컨트롤러:](https://toolkit.fluxcd.io/components/helm/controller/) `helm.toolkit.fluxcd.io` 사용자 지정 리소스를 감시합니다. 원본 컨트롤러에 의해 표면화되는 Helm 리포지토리 소스에서 연결된 차트를 검색합니다. 사용자 `HelmChart` 지정 리소스를 만들고 `HelmRelease` 지정된 버전, 이름 및 고객 정의 값을 가진 를 클러스터에 적용합니다.
* [Flux 알림 컨트롤러:](https://toolkit.fluxcd.io/components/notification/controller/) `notification.toolkit.fluxcd.io` 사용자 지정 리소스를 감시합니다. 모든 Flux 컨트롤러에서 알림을 받습니다. 사용자 정의 webhook 엔드포인트에 알림을 푸시합니다.
* Flux 사용자 지정 리소스 정의:
    * `kustomizations.kustomize.toolkit.fluxcd.io`
    * `imagepolicies.image.toolkit.fluxcd.io`
    * `imagerepositories.image.toolkit.fluxcd.io`
    * `imageupdateautomations.image.toolkit.fluxcd.io`
    * `alerts.notification.toolkit.fluxcd.io`
    * `providers.notification.toolkit.fluxcd.io`
    * `receivers.notification.toolkit.fluxcd.io`
    * `buckets.source.toolkit.fluxcd.io`
    * `gitrepositories.source.toolkit.fluxcd.io`
    * `helmcharts.source.toolkit.fluxcd.io`
    * `helmrepositories.source.toolkit.fluxcd.io`
    * `helmreleases.helm.toolkit.fluxcd.io`
    * `fluxconfigs.clusterconfig.azure.com`
* [FluxConfig CRD:](https://github.com/Azure/ClusterConfigurationAgent/blob/master/charts/azure-arc-flux/templates/clusterconfig.azure.com_fluxconfigs.yaml) `fluxconfigs.clusterconfig.azure.com` Kubernetes 개체를 정의하는 사용자 지정 리소스에 대한 사용자 지정 리소스 `FluxConfig` 정의입니다.
* fluxconfig-agent: Azure에서 새 리소스 또는 `fluxConfigurations` 업데이트된 리소스를 감시하고 클러스터에서 연결된 Flux 구성을 시작하는 업무를 담당합니다. 또한 는 각 리소스에 대해 클러스터의 Flux 상태 변경 내용을 Azure로 다시 `fluxConfigurations` 푸시합니다.
* fluxconfig-controller: `fluxconfigs.clusterconfig.azure.com` 사용자 지정 리소스를 감시하고 클러스터에서 GitOps 기계의 새로운 구성 또는 업데이트된 구성으로 변경 내용에 응답합니다.

> [!NOTE]
> `microsoft.flux`확장은 `flux-system` 네임스페이스에 설치되며 클러스터 전체 범위가 있습니다. 네임스페이스 범위에서 이 확장을 설치하는 옵션을 사용할 수 없으며 네임스페이스 범위에서 설치를 시도하면 400 오류가 발생하며 실패합니다.

## <a name="flux-configurations"></a>Flux 구성

:::image type="content" source="media/gitops/flux2-config-install.png" alt-text="Azure Arc 지원 Kubernetes 또는 Azure Kubernetes Service 클러스터에서 Flux 구성 설치를 보여 주는 다이어그램" lightbox="media/gitops/flux2-config-install.png":::

`microsoft.flux`확장이 클러스터에 설치된 경우 Flux 구성 리소스()를 만들어 `Microsoft.KubernetesConfiguration/fluxConfigurations` Git 리포지토리에서 클러스터의 GitOps 관리를 사용하도록 설정할 수 있습니다. 리소스를 만들 때 `fluxConfigurations` 대상 Git 리포지토와 같은 매개 변수에 제공하는 값은 해당 클러스터에서 GitOps 프로세스를 사용하도록 설정하는 Kubernetes 개체를 만들고 구성하는 데 사용됩니다. 데이터 보안을 위해 `fluxConfigurations` 리소스 데이터는 클러스터 구성 서비스에 의해 Azure Cosmos DB 데이터베이스에 암호화되어 저장됩니다.

`fluxconfig-agent`확장과 함께 설치된 및 `fluxconfig-controller` 에이전트는 `microsoft.flux` GitOps 구성 프로세스를 관리합니다.  

`fluxconfig-agent` 은 다음을 담당합니다.

* 신규 또는 업데이트된 리소스에 대한 Kubernetes 구성 데이터 평면 서비스를 `fluxConfigurations` 폴합니다.
* 구성 정보를 통해 클러스터에서 사용자 지정 리소스를 만들거나 `FluxConfig` 업데이트합니다.
* `FluxConfig`사용자 지정 리소스를 감시하고 상태 변경 내용을 연결된 Azure fluxConfiguration 리소스에 다시 푸시합니다.

`fluxconfig-controller` 은 다음을 담당합니다.

* 관리되는 에서 만든 Flux 사용자 지정 리소스에 대한 상태 업데이트를 `fluxConfigurations` 감시합니다.
* 의 수명 동안 존재하는 프라이빗/퍼블릭 키 쌍을 `fluxConfigurations` 만듭니다. 이 키는 URL이 SSH 기반이고 사용자가 구성을 만드는 동안 자신의 프라이빗 키를 제공하지 않는 경우 인증에 사용됩니다.
* 사용자가 제공한 private-key/http basic-auth/known-hosts/no-auth 데이터를 기반으로 사용자 지정 인증 비밀을 만듭니다.
* RBAC(프로비전된 서비스 계정, 생성/할당된 역할 바인딩, 생성/할당된 역할)를 설정합니다.
* `GitRepository`사용자 지정 `Kustomization` 리소스의 정보에서 사용자 지정 리소스 및 `FluxConfig` 사용자 지정 리소스를 만듭니다.

Azure의 각 `fluxConfigurations` 리소스는 하나의 Flux 사용자 지정 리소스와 하나 이상의 사용자 지정 리소스가 있는 Kubernetes 클러스터에 `GitRepository` `Kustomization` 연결됩니다. 리소스를 만들 때 `fluxConfigurations` Git 리포지토리에 대한 URL과 각 에 대한 Git 리포지토리의 동기화 대상을 `Kustomization` 지정합니다. `Kustomization`배포 시퀀싱을 제어하도록 사용자 지정 리소스 간의 의존도를 구성할 수 있습니다. 또한 동일한 클러스터에 여러 네임스페이스 범위 리소스를 만들 수 `fluxConfigurations` 있습니다.

> [!NOTE]
> * `fluxconfig-agent` 는 Azure에서 새 리소스 또는 `fluxConfiguration` 업데이트된 리소스를 모니터링합니다. 에이전트는 의 원하는 상태를 `fluxConfiguration` 클러스터에 적용하려면 Azure에 연결해야 합니다. 에이전트가 Azure에 연결할 수 없는 경우 에이전트가 연결할 수 있을 때까지 클러스터에서 변경이 지연됩니다. 클러스터가 Azure에서 48시간 넘게 연결이 끊어지면 클러스터에 대한 요청 시간이 초과되고 변경 내용을 Azure에 다시 적용해야 합니다.
> * 프라이빗 키, 알려진 호스트 콘텐츠, HTTPS 사용자 이름 및 토큰/암호와 같은 중요한 고객 입력은 Kubernetes 구성 서비스에 48시간 이내에 저장됩니다. Azure에서 이러한 값을 업데이트하는 경우 클러스터가 48시간 이내에 Azure와 연결되었는지 확인합니다.

## <a name="next-steps"></a>다음 단계

다음 자습서로 진행하여 Azure Arc 지원 Kubernetes 또는 AKS 클러스터에서 GitOps를 사용하도록 설정하는 방법을 알아봅니다.
> [!div class="nextstepaction"]
* [Flux로 GitOps 사용](./tutorial-use-gitops-flux2.md)
