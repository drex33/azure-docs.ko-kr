---
title: Azure Arc 지원 Open Service Mesh(미리 보기)
description: Azure Arc 사용 Kubernetes 클러스터에서 OSM (Open Service 메시) 확장
ms.service: azure-arc
ms.date: 07/23/2021
ms.topic: article
author: mayurigupta13
ms.author: mayg
ms.openlocfilehash: d09bed41ff7553e79a4d05e616e07fabf85d71a9
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130227134"
---
# <a name="azure-arc-enabled-open-service-mesh-preview"></a>Azure Arc 지원 Open Service Mesh(미리 보기)

[OSM(Open Service Mesh)](https://docs.openservicemesh.io/)은 확장 가능한 네이티브 클라우드 서비스 메시이며, 사용자는 매우 동적인 마이크로 서비스 환경을 일관되게 관리하고, 안전하게 만들고, 즉시 사용 가능한 가시성 기능을 사용할 수 있습니다.

OSM은 Kubernetes에서 Envoy 기반 컨트롤 플레인을 실행하고, [SMI](https://smi-spec.io/) API를 사용하여 구성될 수 있으며, 애플리케이션의 각 인스턴스 옆에 사이드카 컨테이너로 Envoy 프록시를 삽입하여 작동합니다. Open Service Mesh에서 사용하도록 설정된 서비스 메시 시나리오에 대해 [자세히 읽어 보세요.](https://docs.openservicemesh.io/#features)

### <a name="support-limitations-for-azure-arc-enabled-open-service-mesh"></a>Azure Arc 사용 개방형 서비스 메시에 대 한 지원 제한

- Azure Arc에 연결 된 Kubernetes 클러스터에는 개방형 서비스 메시 인스턴스를 하나만 배포할 수 있습니다.
- 공개 미리 보기는 Open Service Mesh 버전 v0.8.4 이상에서 사용할 수 있습니다. [여기](https://github.com/Azure/osm-azure/releases)에서 최신 버전의 릴리스를 확인하세요. 지원되는 릴리스 버전에는 참고가 추가됩니다. 중간 릴리스와 연결된 태그를 무시합니다. 
- 현재 지원 되는 Kubernetes 분포는 다음과 같습니다.
    - AKS 엔진
    - HCI의 AKS
    - Cluster API Azure
    - Google Kubernetes Engine
    - Canonical Kubernetes Distribution
    - Rancher Kubernetes Engine
    - OpenShift Kubernetes Distribution
    - Amazon Elastic Kubernetes Service
- Azure Arc와의 통합 Azure Monitor 지원 되는 서비스 메시는 [제한 된 지원](https://github.com/microsoft/Docker-Provider/blob/ci_dev/Documentation/OSMPrivatePreview/ReadMe.md)으로 제공 됩니다.

[!INCLUDE [preview features note](./includes/preview/preview-callout.md)]

### <a name="prerequisites"></a>필수 구성 요소

- [여기](extensions.md#prerequisites)에 나열된 클러스터 확장에 대한 모든 일반적인 필수 구성 요소가 충족되어야 합니다.
- az k8s-extension CLI 버전 v0.4.0 이상 사용

## <a name="install-azure-arc-enabled-open-service-mesh-osm-on-an-azure-arc-enabled-kubernetes-cluster"></a>Azure Arc 사용 Kubernetes 클러스터에서 Azure Arc 사용 OSM (Open Service 메시) 설치

다음 단계에서는 Azure Arc에 연결된 지원되는 Kubernetes 배포를 사용하는 클러스터가 이미 있다고 가정합니다.

### <a name="install-a-specific-version-of-osm"></a>특정 OSM 버전 설치

KUBECONFIG 환경 변수가 OSM 확장을 설치하려는 Kubernetes 클러스터의 kubeconfig를 가리키는지 확인합니다.

환경 변수를 설정합니다.

```azurecli-interactive
export VERSION=<osm-arc-version>
export CLUSTER_NAME=<arc-cluster-name>
export RESOURCE_GROUP=<resource-group-name>
```

Azure Arc 사용 개방형 서비스 메시는 미리 보기 상태 이지만 `az k8s-extension create` 명령은 `pilot` 플래그에 대해서만 허용 됩니다 `--release-train` . `--auto-upgrade-minor-version`은 항상 `false`로 설정되고 버전을 제공해야 합니다. OpenShift 클러스터가 있는 경우 [섹션](#install-a-specific-version-of-osm-on-openshift-cluster)의 단계를 사용합니다.

```azurecli-interactive
az k8s-extension create --cluster-name $CLUSTER_NAME --resource-group $RESOURCE_GROUP --cluster-type connectedClusters --extension-type Microsoft.openservicemesh --scope cluster --release-train pilot --name osm --version $VERSION
```

다음과 유사한 결과가 나타나야 합니다. 실제 OSM helm 차트를 클러스터에 배포하는 데 3-5분 정도 걸릴 수 있습니다. 이 배포가 시작될 때까지 installState가 계속 보류 중으로 표시됩니다.

```json
{
  "autoUpgradeMinorVersion": false,
  "configurationSettings": {},
  "creationTime": "2021-04-29T17:50:11.4116524+00:00",
  "errorInfo": {
    "code": null,
    "message": null
  },
  "extensionType": "microsoft.openservicemesh",
  "id": "/subscriptions/<subscription-id>/resourceGroups/$RESOURCE_GROUP/providers/Microsoft.Kubernetes/connectedClusters/$CLUSTER_NAME/providers/Microsoft.KubernetesConfiguration/extensions/osm",
  "identity": null,
  "installState": "Pending",
  "lastModifiedTime": "2021-04-29T17:50:11.4116525+00:00",
  "lastStatusTime": null,
  "location": null,
  "name": "osm",
  "releaseTrain": "pilot",
  "resourceGroup": "$RESOURCE_GROUP",
  "scope": {
    "cluster": {
      "releaseNamespace": "arc-osm-system"
    },
    "namespace": null
  },
  "statuses": [],
  "type": "Microsoft.KubernetesConfiguration/extensions",
  "version": "x.x.x"
}
```

### <a name="install-a-specific-version-of-osm-on-openshift-cluster"></a>OpenShift 클러스터에 특정 버전의 OSM 설치

1. 다음 콘텐츠를 복사하여 JSON 파일에 저장합니다. 구성 설정 파일을 이미 만든 경우 기존 파일에 다음 줄을 추가하여 이전 변경 내용을 유지합니다.
   ```json
   {
       "osm.OpenServiceMesh.enablePrivilegedInitContainer": "true"
   }
   ```

   파일 경로를 환경 변수로 설정합니다.
   ```azurecli-interactive
   export SETTINGS_FILE=<json-file-path>
   ```
   
2. OSM 확장을 만드는 데 사용되는 `az k8s-extension create` 명령을 실행하고 구성 설정을 사용하여 설정 파일을 전달합니다.
   ```azurecli-interactive
   az k8s-extension create --cluster-name $CLUSTER_NAME --resource-group $RESOURCE_GROUP --cluster-type connectedClusters --extension-type Microsoft.openservicemesh --scope cluster --release-train pilot --name osm --version $VERSION --configuration-settings-file $SETTINGS_FILE
   ```
   
3. 메시의 애플리케이션에 대한 각 서비스 계정에 권한 있는 [보안 컨텍스트 제약 조건](https://docs.openshift.com/container-platform/4.7/authentication/managing-security-context-constraints.html)을 추가합니다.
   ```azurecli-interactive
   oc adm policy add-scc-to-user privileged -z <service account name> -n <service account namespace>
   ```

실제 OSM helm 차트를 클러스터에 배포하는 데 3-5분 정도 걸릴 수 있습니다. 이 배포가 시작될 때까지 installState가 계속 보류 중으로 표시됩니다.

권한 있는 init 컨테이너 설정이 기본값으로 되돌아가지 않도록 하려면 "osm.OpenServiceMesh.enablePrivilegedInitContainer":" true "구성 설정을 모든 후속 az k8s-extension 명령으로 설정합니다.

### <a name="install-azure-arc-enabled-osm-using-arm-template"></a>ARM 템플릿을 사용 하 여 Azure Arc 사용 OSM 설치

클러스터를 Azure Arc에 연결한 후 다음 형식의 json 파일을 만들어 및 값을 업데이트 합니다 \<cluster-name\> \<osm-arc-version\> .

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "ConnectedClusterName": {
            "defaultValue": "<cluster-name>",
            "type": "String",
            "metadata": {
                "description": "The Connected Cluster name."
            }
        },
        "ExtensionInstanceName": {
            "defaultValue": "osm",
            "type": "String",
            "metadata": {
                "description": "The extension instance name."
            }
        },
        "ExtensionVersion": {
            "defaultValue": "<osm-arc-version>",
            "type": "String",
            "metadata": {
                "description": "The extension type version."
            }
        },
        "ExtensionType": {
            "defaultValue": "Microsoft.openservicemesh",
            "type": "String",
            "metadata": {
                "description": "The extension type."
            }
        },
        "ReleaseTrain": {
            "defaultValue": "Pilot",
            "type": "String",
            "metadata": {
                "description": "The release train."
            }
        }
    },
    "functions": [],
    "resources": [
        {
            "type": "Microsoft.KubernetesConfiguration/extensions",
            "apiVersion": "2020-07-01-preview",
            "name": "[parameters('ExtensionInstanceName')]",
            "properties": {
                "extensionType": "[parameters('ExtensionType')]",
                "releaseTrain": "[parameters('ReleaseTrain')]",
                "version": "[parameters('ExtensionVersion')]"
            },
            "scope": "[concat('Microsoft.Kubernetes/connectedClusters/', parameters('ConnectedClusterName'))]"
        }
    ]
}
```

환경 변수를 설정합니다.

```azurecli-interactive
export TEMPLATE_FILE_NAME=<template-file-path>
export DEPLOYMENT_NAME=<desired-deployment-name>
```

마지막으로 다음 명령을 실행하여 az CLI를 통해 OSM 확장을 설치합니다.

```azurecli-interactive
az deployment group create --name $DEPLOYMENT_NAME --resource-group $RESOURCE_GROUP --template-file $TEMPLATE_FILE_NAME
```

이제 OSM 리소스가 표시되고 클러스터에서 OSM 확장을 사용할 수 있습니다.

## <a name="validate-the-azure-arc-enabled-open-service-mesh-installation"></a>Azure Arc 사용 가능 서비스 메시 설치의 유효성을 검사 합니다.

다음 명령을 실행합니다.

```azurecli-interactive
az k8s-extension show --cluster-type connectedClusters --cluster-name $CLUSTER_NAME --resource-group $RESOURCE_GROUP --name osm
```

다음과 유사한 JSON 출력이 표시됩니다.

```json
{
  "autoUpgradeMinorVersion": false,
  "configurationSettings": {},
  "creationTime": "2021-04-29T19:22:00.7649729+00:00",
  "errorInfo": {
    "code": null,
    "message": null
  },
  "extensionType": "microsoft.openservicemesh",
  "id": "/subscriptions/<subscription-id>/resourceGroups/$RESOURCE_GROUP/providers/Microsoft.Kubernetes/connectedClusters/$CLUSTER_NAME/providers/Microsoft.KubernetesConfiguration/extensions/osm",
  "identity": null,
  "installState": "Installed",
  "lastModifiedTime": "2021-04-29T19:22:00.7649731+00:00",
  "lastStatusTime": "2021-04-29T19:23:27.642+00:00",
  "location": null,
  "name": "osm",
  "releaseTrain": "pilot",
  "resourceGroup": "$RESOURCE_GROUP",
  "scope": {
    "cluster": {
      "releaseNamespace": "arc-osm-system"
    },
    "namespace": null
  },
  "statuses": [],
  "type": "Microsoft.KubernetesConfiguration/extensions",
  "version": "x.x.x"
}
```
## <a name="osm-controller-configuration"></a>OSM 컨트롤러 구성
OSM은 arc-osm-system 네임스페이스에서 제어 평면의 일부로 MeshConfig 리소스 `osm-mesh-config`를 배포합니다. 이 MeshConfig의 목적은 메시 소유자/운영자가 필요에 따라 일부 메시 구성을 업데이트할 수 있는 기능을 제공하는 것입니다. 기본값을 보려면 다음 명령을 사용합니다.

```azurecli-interactive
kubectl describe meshconfig osm-mesh-config -n arc-osm-system
```
출력에는 기본값이 표시됩니다.

```azurecli-interactive
Certificate:
    Service Cert Validity Duration:  24h
  Feature Flags:
    Enable Egress Policy:      true
    Enable Multicluster Mode:  false
    Enable WASM Stats:         true
  Observability:
    Enable Debug Server:  false
    Osm Log Level:        info
    Tracing:
      Address:   jaeger.osm-system.svc.cluster.local
      Enable:    false
      Endpoint:  /api/v2/spans
      Port:      9411
  Sidecar:
    Config Resync Interval:            0s
    Enable Privileged Init Container:  false
    Envoy Image:                       mcr.microsoft.com/oss/envoyproxy/envoy:v1.18.3
    Init Container Image:              mcr.microsoft.com/oss/openservicemesh/init:v0.9.1
    Log Level:                         error
    Max Data Plane Connections:        0
    Resources:
  Traffic:
    Enable Egress:                          false
    Enable Permissive Traffic Policy Mode:  true
    Inbound External Authorization:
      Enable:              false
      Failure Mode Allow:  false
      Stat Prefix:         inboundExtAuthz
      Timeout:             1s
    Use HTTPS Ingress:     false
```
자세한 내용은 [Config API 참조](https://docs.openservicemesh.io/docs/api_reference/config/v1alpha1/)를 참조하세요. **spec.traffic.enablePermissiveTrafficPolicyMode** 는 **true** 로 설정되어 있습니다. OSM의 허용 트래픽 정책 모드는 [SMI](https://smi-spec.io/) 트래픽 정책 적용을 우회하는 모드입니다. 이 모드에서 OSM은 서비스 메시의 일부인 서비스를 자동으로 검색하고 각 Envoy 프록시 사이드카에 트래픽 정책 규칙을 프로그래밍하여 이러한 서비스와 통신할 수 있도록 합니다.

### <a name="making-changes-to-osm-controller-configuration"></a>OSM 컨트롤러 구성 변경

> [!NOTE]
> MeshConfig `osm-mesh-config`의 값은 업그레이드 간에 지속됩니다.

kubectl patch 명령을 사용하여 `osm-mesh-config`를 변경할 수 있습니다. 다음 예제에서는 허용 트래픽 정책 모드가 false로 변경됩니다.

```azurecli-interactive
kubectl patch meshconfig osm-mesh-config -n arc-osm-system -p '{"spec":{"traffic":{"enablePermissiveTrafficPolicyMode":false}}}' --type=merge
```

잘못된 값을 사용하면 MeshConfig CRD의 유효성 검사에서 변경을 방지하고, 값이 잘못된 이유를 설명하는 오류 메시지를 표시합니다. 예를 들어 아래 명령은 enableEgress를 부울이 아닌 값으로 패치하면 어떻게 되는지 보여 줍니다.

```azurecli-interactive
kubectl patch meshconfig osm-mesh-config -n arc-osm-system -p '{"spec":{"traffic":{"enableEgress":"no"}}}'  --type=merge

# Validations on the CRD will deny this change
The MeshConfig "osm-mesh-config" is invalid: spec.traffic.enableEgress: Invalid value: "string": spec.traffic.enableEgress in body must be of type boolean: "string"
```

## <a name="osm-controller-configuration-version-v084"></a>OSM 컨트롤러 구성(버전 v0.8.4)

현재는 ConfigMap을 통해 OSM 컨트롤러 구성에 액세스하여 구성할 수 있습니다. OSM 컨트롤러 구성 설정을 보려면 `kubectl`를 통해 `osm-config` ConfigMap을 쿼리하여 해당 구성 설정을 확인합니다.

```azurecli-interactive
kubectl get configmap osm-config -n arc-osm-system -o json
```

출력:

```json
{
  "egress": "false",
  "enable_debug_server": "false",
  "enable_privileged_init_container": "false",
  "envoy_log_level": "error",
  "permissive_traffic_policy_mode": "true",
  "prometheus_scraping": "true",
  "service_cert_validity_duration": "24h",
  "tracing_enable": "false",
  "use_https_ingress": "false"
}
```

사용 가능한 각 구성을 알아보려면 [OSM ConfigMap 설명서](https://release-v0-8.docs.openservicemesh.io/docs/osm_config_map/)를 참조하세요. 

버전 v0.8.4의 OSM ConfigMap을 변경하려면 다음 지침을 따릅니다.

1. 변경 내용을 복사하여 JSON 파일에 저장합니다. 이 예시에서는 permissive_traffic_policy_mode를 true에서 false로 변경합니다. `osm-config`를 변경할 때마다 JSON 파일에서 변경 내용의 전체 목록을 제공해야 합니다(기본값 `osm-config`와 비교).
    ```json
    {
        "osm.OpenServiceMesh.enablePermissiveTrafficPolicy" : "false"
    }
    ```
    
    파일 경로를 환경 변수로 설정합니다.
    
    ```azurecli-interactive
    export SETTINGS_FILE=<json-file-path>
    ```
    
2. 확장을 만드는 데 사용한 것과 동일한 `az k8s-extension create` 명령을 실행하지만 이제 구성 설정 파일을 전달합니다. 
    ```azurecli-interactive
    az k8s-extension create --cluster-name $CLUSTER_NAME --resource-group $RESOURCE_GROUP --cluster-type connectedClusters --extension-type Microsoft.openservicemesh --scope cluster --release-train pilot --name osm --version $VERSION --configuration-settings-file $SETTINGS_FILE
    ```
    
    > [!NOTE]
    > ConfigMap 변경 내용이 기본값으로 되돌아가지 않도록 하려면 모든 후속 az k8s-extension 생성 명령에 동일한 구성 설정을 전달합니다.

## <a name="using-the-azure-arc-enabled-open-service-mesh"></a>Azure Arc 사용 개방형 서비스 메시 사용

OSM 기능 사용을 시작하려면 먼저 애플리케이션 네임스페이스를 서비스 메시에 온보딩해야 합니다. [OSM GitHub 릴리스 페이지](https://github.com/openservicemesh/osm/releases/)에서 OSM CLI를 다운로드합니다. 메시에 네임스페이스가 추가되면 원하는 OSM 기능을 달성하도록 SMI 정책을 구성할 수 있습니다.

### <a name="onboard-namespaces-to-the-service-mesh"></a>서비스 메시에 네임스페이스 온보딩

다음 명령을 실행하여 메시에 네임스페이스를 추가합니다.

```azurecli-interactive
osm namespace add <namespace_name>
```

온보딩 서비스에 대한 자세한 내용은 [여기](https://docs.openservicemesh.io/docs/guides/app_onboarding/#onboard-services)를 참조하세요.

### <a name="configure-osm-with-service-mesh-interface-smi-policies"></a>SMI(서비스 메시 인터페이스) 정책을 통해 OSM 구성

[데모 애플리케이션](https://release-v0-11.docs.openservicemesh.io/docs/getting_started/quickstart/manual_demo/#deploy-applications)으로 시작하거나 테스트 환경을 사용하여 SMI 정책을 사용해 볼 수 있습니다.

> [!NOTE] 
> 실행하는 북스토어 애플리케이션 버전이 클러스터에 설치된 OSM 확장 버전과 일치하는지 확인합니다. 예: OSM 확장의 v0.8.4를 사용하는 경우 OSM 업스트림 리포지토리의 release-v0.8 분기에서 북스토어 데모를 사용합니다.

### <a name="configuring-your-own-jaeger-prometheus-and-grafana-instances"></a>사용자 고유의 Jaeger, Prometheus 및 Grafana 인스턴스 구성

OSM 확장 프로그램은 [Jaeger](https://www.jaegertracing.io/docs/getting-started/), [Prometheus](https://prometheus.io/docs/prometheus/latest/installation/) 및 [Grafana](https://grafana.com/docs/grafana/latest/installation/)와 같은 추가 항목을 설치하지 않으므로 사용자가 해당 도구의 실행 중인 자체 인스턴스와 OSM을 통합할 수 있습니다. 사용자의 인스턴스와 통합하려면 다음 설명서를 확인하세요.

> [!NOTE]
> OSM GitHub 설명서에 제공된 명령을 주의하여 사용하세요. `osm-mesh-config`를 변경할 때 올바른 네임스페이스 이름 ‘arc-osm-system’을 사용하는지 확인합니다.

- [BYO-Jaeger 인스턴스](https://docs.openservicemesh.io/docs/guides/observability/tracing/#byo-bring-your-own)
- [BYO-Prometheus 인스턴스](https://docs.openservicemesh.io/docs/guides/observability/metrics/#byo-prometheus)
- [BYO-Grafana 대시보드](https://docs.openservicemesh.io/docs/guides/observability/metrics/#importing-dashboards-on-a-byo-grafana-instance)


## <a name="monitoring-application-using-azure-monitor-and-applications-insights"></a>Azure Monitor 및 Applications Insights을 사용한 모니터링 애플리케이션

Azure Monitor 및 Azure Application Insights 모두를 사용하면 클라우드 및 온-프레미스 환경에서 원격 분석을 수집, 분석 및 작동하기 위한 종합적인 솔루션을 제공하여 애플리케이션 및 서비스의 가용성과 성능을 극대화할 수 있습니다.

Azure Arc 사용 개방형 서비스 메시는 이러한 두 Azure 서비스 모두에 긴밀 하 게 통합 되며 OSM 메트릭에 의해 제공 되는 중요 Kpi를 보고 응답 하기 위한 원활한 통합과 Azure 환경을 제공 합니다. 다음 단계를 수행하여 애플리케이션 메트릭을 수집하기 위해 Azure Monitor 스크랩 prometheus 엔드포인트를 허용합니다. 

1. 모니터링할 애플리케이션 네임스페이스가 메시에 온보딩되어 있는지 확인합니다. [여기에서 제공](#onboard-namespaces-to-the-service-mesh)하는 지침을 따르세요.

2. 애플리케이션 네임스페이스에 대한 prometheus 엔드포인트를 공개합니다.
    ```azurecli-interactive
    osm metrics enable --namespace <namespace1>
    osm metrics enable --namespace <namespace2>
    ```
    v0.8.4의 경우 `osm-config` ConfigMap에서 `prometheus_scraping`이 `true`로 설정되어 있는지 확인합니다.

3. [여기](../../azure-monitor/containers/container-insights-enable-arc-enabled-clusters.md?toc=/azure/azure-arc/kubernetes/toc.json)에서 제공하는 지침을 사용하여 Azure Monitor 확장을 설치합니다.

4. 모니터링하려는 네임스페이스를 container-azm-osmconfig ConfigMap에 추가합니다. [여기](https://github.com/microsoft/Docker-Provider/blob/ci_prod/kubernetes/container-azm-ms-osmconfig.yaml)에서 ConfigMap을 다운로드합니다.
    ```azurecli-interactive
    monitor_namespaces = ["namespace1", "namespace2"]
    ```

5. 다음 kubectl 명령을 실행합니다
    ```azurecli-interactive
    kubectl apply -f container-azm-ms-osmconfig.yaml
    ```

메트릭이 Log Analytics에 표시되는 데 최대 15분이 걸릴 수 있습니다. InsightsMetrics 테이블을 쿼리해 볼 수 있습니다.

```azurecli-interactive
InsightsMetrics
| where Name contains "envoy"
| extend t=parse_json(Tags)
| where t.app == "namespace1"
```
[여기](https://github.com/microsoft/Docker-Provider/blob/ci_dev/Documentation/OSMPrivatePreview/ReadMe.md)에서 Azure Monitor와의 통합에 대해 자세히 알아보세요.

### <a name="navigating-the-osm-dashboard"></a>OSM 대시보드 탐색

1. 이 [링크](https://aka.ms/azmon/osmarcux)를 사용 하 여 Azure Arc에 연결 된 Kubernetes 클러스터에 액세스 합니다.
2. Azure Monitor로 가서 보고서 탭으로 이동하여 OSM 통합 문서에 액세스합니다.
3. 시간 범위 및 네임스페이스를 선택하여 서비스 범위를 지정합니다.

![OSM 통합 문서](./media/tutorial-arc-enabled-open-service-mesh/osm-workbook.jpg)

#### <a name="requests-tab"></a>요청 탭

- 이 탭에서는 서비스를 통해 OSM 서비스로 전송된 모든 http 요청에 대한 요약을 제공합니다.
- 그리드에서 서비스를 선택하여 모든 서비스 및 통신하는 모든 서비스를 볼 수 있습니다.
- 총 요청, 요청 오류 비율, P90 대기 시간을 볼 수 있습니다.
- 대상으로 드릴다운하고 HTTP 오류/성공 코드, 성공률, Pod 리소스 사용률 및 대기 시간에 대한 추세를 다른 백분위수로 볼 수 있습니다.

#### <a name="connections-tab"></a>연결 탭

- 이 탭에서는 Open Service Mesh의 서비스 간 모든 연결에 대한 요약을 제공합니다.
- 아웃바운드 연결: 원본 서비스와 대상 서비스 간의 총 연결 수입니다.
- 아웃바운드 활성 연결: 선택한 시간 범위에서 원본과 대상 간의 활성 연결의 최근 수입니다.
- 아웃바운드 연결 실패: 원본 서비스와 대상 서비스 간의 총 실패한 연결 수

## <a name="upgrade-the-osm-extension-instance-to-a-specific-version"></a>OSM 확장 인스턴스를 특정 버전으로 업그레이드

업그레이드하는 동안 컨트롤 플레인의 가동 중지 시간이 있을 수 있습니다. 데이터 평면은 CRD 업그레이드 중에만 영향을 받습니다.

### <a name="supported-upgrades"></a>지원되는 업그레이드

OSM 확장은 다음 부 버전까지 업그레이드할 수 있습니다. 현재 다운그레이드 및 주 버전 업그레이드는 지원되지 않습니다.

### <a name="crd-upgrades"></a>CRD 업그레이드

기존 CRD를 먼저 삭제하지 않고 해당 버전에 CRD 버전 업데이트가 포함된 경우 OSM 확장을 새 버전으로 업그레이드할 수 없습니다. [OSM 릴리스 정보](https://github.com/openservicemesh/osm/releases)의 CRD 업데이트 섹션을 확인하여 OSM 업그레이드에 CRD 버전 업데이트도 포함되어 있는지 확인할 수 있습니다.

업그레이드 후 쉽게 다시 만들 수 있도록 CRD를 삭제하기 전에 사용자 지정 리소스를 백업해야 합니다. 그런 다음, 아래 캡처된 업그레이드 지침을 따릅니다.

> [!NOTE] 
> CRD 업그레이드는 삭제된 시간과 다시 생성된 시간 사이에 SMI 정책이 존재하지 않기 때문에 데이터 평면에 영향을 줍니다.

### <a name="upgrade-instructions"></a>업그레이드 지침

1. 이전 CRD 및 사용자 지정 리소스를 삭제합니다([OSM 리포지토리](https://github.com/openservicemesh/osm)의 루트에서 실행). [OSM CRD](https://github.com/openservicemesh/osm/tree/main/cmd/osm-bootstrap/crds)의 태그가 차트의 새 버전과 일치하는지 확인합니다.
    ```azurecli-interactive
    kubectl delete --ignore-not-found --recursive -f ./charts/osm/crds/

2. Install the updated CRDs.
    ```azurecli-interactive
    kubectl apply -f charts/osm/crds/
    ```

3. 새 차트 버전을 환경 변수로 설정합니다.
    ```azurecli-interactive
    export VERSION=<chart version>
    ```
    
4. 새 차트 버전의 az k8s-extension 생성을 실행합니다
    ```azurecli-interactive
    az k8s-extension create --cluster-name $CLUSTER_NAME --resource-group $RESOURCE_GROUP --cluster-type connectedClusters --extension-type Microsoft.openservicemesh --scope cluster --release-train pilot --name osm --version $VERSION --configuration-settings-file $SETTINGS_FILE
    ```

5. 새 CRD를 사용하여 사용자 지정 리소스 재생성

## <a name="uninstall-azure-arc-enabled-open-service-mesh"></a>Azure Arc 사용 가능 개방형 서비스 메시 제거

다음 명령을 사용합니다.

```azurecli-interactive
az k8s-extension delete --cluster-type connectedClusters --cluster-name $CLUSTER_NAME --resource-group $RESOURCE_GROUP --name osm -y
```

확장 인스턴스가 삭제되었는지 확인합니다.

```azurecli-interactive
az k8s-extension list --cluster-type connectedClusters --cluster-name $CLUSTER_NAME --resource-group $RESOURCE_GROUP
```

이 출력에는 OSM이 포함되어서는 안됩니다. 클러스터에 다른 확장을 설치하지 않은 경우에는 빈 배열입니다.

az k8s-extension 명령을 사용하여 OSM 확장을 삭제하는 경우 arc-osm-system 네임스페이스는 제거되지 않고 네임스페이스 내의 실제 리소스(예: 변경 웹후크 구성 및 osm-controller pod)는 삭제하는 데 약 10분이 소요됩니다.

> [!NOTE] 
> az k8s-extension CLI를 사용하여 Arc에서 관리하는 OSM 구성 요소를 제거합니다. OSM CLI를 사용하여 제거하는 것은 Arc에서 지원되지 않으므로 원치 않는 동작이 발생할 수 있습니다.

## <a name="troubleshooting"></a>문제 해결

[여기에서 제공하는](troubleshooting.md#azure-arc-enabled-open-service-mesh) 문제 해결 가이드를 확인하세요.

## <a name="next-steps"></a>다음 단계

> **작업을 시도해보시겠습니까?**  
> 클러스터 API를 사용하여 [Azure Arc Jumpstart](https://aka.ms/arc-jumpstart-osm) 시나리오를 빠르게 시작하세요.
