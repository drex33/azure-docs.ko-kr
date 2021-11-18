---
title: Open Service Mesh에 애플리케이션 온보딩
description: Open Service Mesh에 애플리케이션을 온보딩하는 방법
services: container-service
ms.topic: article
ms.date: 8/26/2021
ms.author: pgibson
ms.openlocfilehash: e7e56d0957da67acbbef1716193d64571206152a
ms.sourcegitcommit: 1244a72dbec39ac8cf16bb1799d8c46bde749d47
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/18/2021
ms.locfileid: "132753724"
---
# <a name="onboarding-applications-to-open-service-mesh-osm-azure-kubernetes-service-aks-add-on"></a>OSM(Open Service Mesh) Azure Kubernetes Service(AKS) 추가 기능으로 애플리케이션 온보딩

다음 가이드에서는 KUbernetes 마이크로서비스를 OSM에 온보딩하는 방법을 설명합니다.

## <a name="before-you-begin"></a>시작하기 전에

이 워크스루에 자세히 설명된 단계에서는 이전에 AKS 클러스터에 대해 OSM AKS 추가 기능을 사용하도록 설정했다고 가정합니다. 그렇지 않은 경우 계속하기 전에 [OSM AKS 추가](./open-service-mesh-deploy-addon-az-cli.md) 기능 배포 문서를 검토합니다. 또한 AKS 클러스터는 Kubernetes 버전 `1.19+` 이상이어야 하고, Kubernetes RBAC를 사용하도록 설정하고, 클러스터와의 `kubectl` 연결을 설정해야 합니다. 이러한 항목에 대한 도움이 필요한 경우 [AKS 빠른 시작](./kubernetes-walkthrough.md)을 참조하고 AKS OSM 추가 기능을 설치해야 합니다.

다음 리소스가 설치되어 있어야 합니다.

- Azure CLI 버전 2.20.0 이상
- OSM 추가 기능 버전 v0.11.1 이상
- OSM CLI 버전 v0.11.1 이상

## <a name="verify-the-open-service-mesh-osm-permissive-traffic-mode-policy"></a>OSM(Open Service Mesh) 허용 트래픽 모드 정책을 확인합니다.

OSM 허용 트래픽 정책 모드는 [SMI](https://smi-spec.io/) 트래픽 정책 적용을 우회하는 모드입니다. 이 모드에서 OSM은 서비스 메시의 일부인 서비스를 자동으로 검색하고 각 Envoy 프록시 사이드카에 트래픽 정책 규칙을 프로그래밍하여 이러한 서비스와 통신할 수 있도록 합니다.

클러스터에 대한 OSM의 현재 허용 트래픽 모드를 확인하려면 다음 명령을 실행합니다.

```azurecli-interactive
kubectl get meshconfig osm-mesh-config -n kube-system -o jsonpath='{.spec.traffic.enablePermissiveTrafficPolicyMode}{"\n"}'
true
```

**enablePermissiveTrafficPolicyMode가** **true** 로 구성된 경우 서비스 간 통신을 중단하지 않고도 네임스페이스를 안전하게 온보딩할 수 있습니다. **enablePermissiveTrafficPolicyMode가** **false로** 구성된 경우 올바른 [SMI](https://smi-spec.io/) 트래픽 액세스 정책 매니페스트가 배포되었는지 확인해야 합니다. 또한 네임스페이스에 배포된 각 서비스를 나타내는 서비스 계정이 있는지 확인해야 합니다. 허용 트래픽 모드에 대한 자세한 내용은 을 방문하여 [허용 트래픽 정책 모드](https://docs.openservicemesh.io/docs/guides/traffic_management/permissive_mode/) 문서를 읽어보십시오.

## <a name="onboard-applications-with-open-service-mesh-osm-permissive-traffic-policy-configured-as-true"></a>OSM(Open Service Mesh) 허용 트래픽 정책이 True로 구성된 애플리케이션 온보딩

1. [애플리케이션을](https://docs.openservicemesh.io/docs/guides/app_onboarding/prereqs/) 온보딩하기 전에 애플리케이션 요구 사항 가이드를 참조하세요.

1. 메시의 애플리케이션이 Kubernetes API 서버와 통신해야 하는 경우 사용자는 IP 범위 제외를 사용하거나 송신 정책을 만들어 이를 명시적으로 허용해야 합니다.

1. Kubernetes 네임스페이스를 OSM에 온보딩

    OSM에서 관리될 애플리케이션이 포함된 네임스페이스를 온보딩하려면 명령을 실행합니다. `osm namespace add`

    ```console
    $ osm namespace add <namespace>
    ```

    기본적으로 명령은 `osm namespace add` 네임스페이스의 Pod에 대해 자동 사이드카 주입을 사용하도록 설정합니다.

    메시에 네임스페이스를 등록하는 과정의 일부로 자동 사이드카 주입을 사용하지 않도록 설정하려면 를 `osm namespace add <namespace> --disable-sidecar-injection` 사용합니다.
    네임스페이스가 온보딩되면 자동 사이드카 주입을 구성하여 메시에 Pod를 등록할 수 있습니다. 자세한 내용은 [사이드카 삽입](https://docs.openservicemesh.io/docs/guides/app_onboarding/sidecar_injection/) 문서를 참조하세요.

1.  새 애플리케이션 배포 또는 기존 애플리케이션 다시 배포

    기본적으로 온보딩된 네임스페이스의 새 배포는 자동 사이드카 주입에 대해 사용하도록 설정됩니다. 즉, 관리되는 네임스페이스에 새 Pod가 만들어지면 OSM은 사이드카 프록시를 Pod에 자동으로 삽입합니다.
    OSM이 Pod를 다시 만들 때 사이드카 프록시를 자동으로 삽입할 수 있도록 기존 배포를 다시 시작해야 합니다. 명령을 사용하여 배포에서 관리되는 Pod를 다시 시작할 수 `kubectl rollout restart deploy` 있습니다.

    프로토콜별 트래픽을 서비스 포트로 올바르게 라우팅하려면 사용할 애플리케이션 프로토콜을 구성합니다. 자세한 내용은 [애플리케이션 프로토콜 선택 가이드를](https://docs.openservicemesh.io/docs/guides/app_onboarding/app_protocol_selection/) 참조하세요.


## <a name="onboard-existing-deployed-applications-with-open-service-mesh-osm-permissive-traffic-policy-configured-as-false"></a>OSM(Open Service Mesh) 허용 트래픽 정책을 False로 구성하여 배포된 기존 애플리케이션 온보딩

허용 트래픽 정책에 대한 OSM 구성이 `false`로 설정된 경우 OSM은 클러스터 내에서 발생하는 서비스 간 통신을 위해 명시적 [SMI](https://smi-spec.io/) 트래픽 액세스 정책을 배포해야 합니다. OSM은 Kubernetes 서비스 계정을 사용하여 메시의 애플리케이션 간에 액세스 제어 정책을 구현하므로 [SMI](https://smi-spec.io/) 트래픽 액세스 정책을 적용하여 애플리케이션 간의 트래픽 흐름에 권한을 부여합니다.

예를 들어 SMI 정책은 다음 예제를 참조하세요.
    - [demo/deploy-traffic-specs.sh](https://github.com/openservicemesh/osm/blob/release-v0.11/demo/deploy-traffic-specs.sh)
    - [demo/deploy-traffic-split.sh](https://github.com/openservicemesh/osm/blob/release-v0.11/demo/deploy-traffic-split.sh)
    - [demo/deploy-traffic-target.sh](https://github.com/openservicemesh/osm/blob/release-v0.11/demo/deploy-traffic-target.sh)


#### <a name="removing-namespaces"></a>네임스페이스 제거
명령을 사용하면 OSM 메시에서 네임스페이스를 제거할 수 있습니다. `osm namespace remove`

```console
$ osm namespace remove <namespace>
```

> [!NOTE]
>
> - **`osm namespace remove`** 명령은 OSM에 네임스페이스의 사이드카 프록시 구성에 대한 업데이트 적용을 중지하도록 지시합니다. 프록시 사이드카를 **제거하지 않습니다.** 즉, 기존 프록시 구성은 계속 사용되지만 OSM 컨트롤 플레인에서 업데이트되지 않습니다. 모든 Pod에서 프록시를 제거하려면 OSM LCI를 사용하여 메시에서 Pod의 네임스페이스를 제거하고 해당 Pod 또는 배포를 다시 배포합니다.
