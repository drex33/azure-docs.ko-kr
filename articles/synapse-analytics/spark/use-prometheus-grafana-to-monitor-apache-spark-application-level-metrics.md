---
title: 자습서 - Prometheus 및 Grafana를 사용하여 Apache Spark 애플리케이션 메트릭 모니터링
description: 자습서 - AKS(Azure Kubernetes Service) 클러스터에 Apache Spark 애플리케이션 메트릭 솔루션을 배포하고 Grafana 대시보드를 통합하는 방법을 알아봅니다.
services: synapse-analytics
author: hrasheed-msft
ms.author: jejiang
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.topic: tutorial
ms.subservice: spark
ms.date: 01/22/2021
ms.openlocfilehash: eb49c65d3d61e17a8e527016d32d80bcc85894d8
ms.sourcegitcommit: f2d0e1e91a6c345858d3c21b387b15e3b1fa8b4c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/07/2021
ms.locfileid: "123541228"
---
# <a name="monitor-apache-spark-applications-metrics-with-prometheus-and-grafana"></a>Prometheus 및 Grafana를 사용하여 Apache Spark 애플리케이션 메트릭 모니터링

## <a name="overview"></a>개요

이 자습서에서는 AKS(Azure Kubernetes Service) 클러스터에 Apache Spark 애플리케이션 메트릭 솔루션을 배포하고 Grafana 대시보드를 통합하는 방법을 알아봅니다.

이 솔루션을 사용하여 거의 실시간으로 Apache Spark 메트릭 데이터를 수집 및 쿼리할 수 있습니다. 통합된 Grafana 대시보드를 사용하여 Apache Spark 애플리케이션을 진단하고 모니터링할 수 있습니다. 소스 코드와 구성이 GitHub에 공개됩니다.

## <a name="prerequisites"></a>필수 구성 요소

1.  [Azure CLI](/cli/azure/install-azure-cli)
2.  [Helm 클라이언트 3.30+](https://github.com/helm/helm/releases)
3.  [kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/)
4.  [AKS(Azure Kubernetes Service)](https://azure.microsoft.com/services/kubernetes-service/)

또는 기본적으로 Azure CLI, Helm 클라이언트 및 kubectl이 포함되어 있는 [Azure Cloud Shell](https://shell.azure.com/)을 사용합니다.

## <a name="log-in-to-azure"></a>Azure에 로그인

```bash
az login
az account set --subscription "<subscription_id>"
```

## <a name="create-an-azure-kubernetes-service-instance-aks"></a>AKS(Azure Kubernetes Service) 인스턴스 만들기

Azure CLI 명령을 사용하여 구독에 Kubernetes 클러스터를 만듭니다.

```
az aks create --name <kubernetes_name> --resource-group <kubernetes_resource_group> --location <location> --node-vm-size Standard_D2s_v3
az aks get-credentials --name <kubernetes_name> --resource-group <kubernetes_resource_group>
```

참고: AKS 클러스터가 이미 있는 경우 이 단계를 건너뛰어도 됩니다.

## <a name="create-a-service-principal-and-grant-permission-to-synapse-workspace"></a>서비스 주체를 만들고 Synapse 작업 영역에 대한 권한 부여

```bash
az ad sp create-for-rbac --name <service_principal_name>
```

결과는 다음과 같습니다.

```json
{
  "appId": "abcdef...",
  "displayName": "<service_principal_name>",
  "name": "http://<service_principal_name>",
  "password": "abc....",
  "tenant": "<tenant_id>"
}
```

appId, 암호 및 tenantID를 적어둡니다.

[![스크린샷 권한 부여 srbac](./media/monitor-azure-synapse-spark-application-level-metrics/screenshot-grant-permission-srbac-new.png)](./media/monitor-azure-synapse-spark-application-level-metrics/screenshot-grant-permission-srbac-new.png#lightbox)

1. Synapse 관리자로 [Azure Synapse Analytics 작업 영역](https://web.azuresynapse.net/)에 로그인합니다.

2. Synapse Studio의 왼쪽 창에서 **관리 > 액세스 제어** 를 선택합니다.

3. 왼쪽 위에 있는 추가 단추를 클릭하여 **역할 할당을 추가** 합니다.

4. 범위로 **작업 영역** 을 선택합니다.

5. 역할로 **Synapse Compute 역할** 을 선택합니다.

6. 선택한 사용자에 대해 **<service_principal_name>** 을 입력하고 서비스 주체를 클릭합니다.

7. **적용** 을 클릭합니다(권한이 적용될 때까지 3분 대기).

## <a name="install-connector-prometheus-server-grafana-dashboard"></a>커넥터, Prometheus 서버, Grafana 대시보드 설치

1. synapse-charts 리포지토리를 Helm 클라이언트에 추가합니다.

```bash
helm repo add synapse-charts https://github.com/microsoft/azure-synapse-spark-metrics/releases/download/helm-chart
```

2.  Helm 클라이언트를 통해 구성 요소를 설치합니다.

```bash
helm install spo synapse-charts/synapse-prometheus-operator --create-namespace --namespace spo \
    --set synapse.workspaces[0].workspace_name="<workspace_name>" \
    --set synapse.workspaces[0].tenant_id="<tenant_id>" \
    --set synapse.workspaces[0].service_principal_name="<service_principal_app_id>" \
    --set synapse.workspaces[0].service_principal_password="<service_principal_password>" \
    --set synapse.workspaces[0].subscription_id="<subscription_id>" \
    --set synapse.workspaces[0].resource_group="<workspace_resource_group_name>"
```

 - workspace_name: Synapse 작업 영역 이름
 - subscription_id: Synapse 작업 영역 구독 ID
 - workspace_resource_group_name: Synapse 작업 영역 리소스 그룹 이름
 - tenant_id: Synapse 작업 영역 테넌트 ID
 - service_principal_app_id: 서비스 주체 "appId"
 - service_principal_password: 사용자가 만든 서비스 주체 암호

## <a name="log-in-to-grafana"></a>Grafana에 로그인

Grafana의 기본 암호와 주소를 가져옵니다. Grafana 설정에서 암호를 변경할 수 있습니다.

```bash
kubectl get secret --namespace spo spo-grafana -o jsonpath="{.data.admin-password}" | base64 --decode ; echo
kubectl -n spo get svc spo-grafana
```

서비스 ip를 가져오고, 외부 ip를 복사하여 브라우저에 붙여넣고, 사용자 이름 "admin" 및 암호를 사용하여 로그인합니다.

## <a name="use-grafana-dashboards"></a>Grafana 대시보드 사용

Grafana 페이지의 왼쪽 위 모서리에 있는 Synapse 대시보드를 찾고(홈 -> Synapse Workspace/Synapse Application), Synapse Studio에서 예제 코드를 실행하고 메트릭이 표시될 때까지 몇 초 동안 기다립니다.

또한 "Synapse 작업 영역/작업 영역" 및 "Synapse 작업 영역/Apache Spark 풀" 대시보드를 사용하여 작업 영역 및 Apache Spark 풀의 개요를 볼 수 있습니다.

## <a name="uninstall"></a>제거

다음과 같이 Helm 명령으로 구성 요소를 제거합니다.

```bash
helm delete <release_name> -n <namespace>
```

AKS 클러스터를 삭제합니다.

```bash
az aks delete --name <kubernetes_cluster_name> --resource-group <kubernetes_cluster_rg>
```

## <a name="components-introduction"></a>구성 요소 소개

Azure Synapse Analytics는 Prometheus Operator 및 Synapse Prometheus 커넥터를 기준으로 하는 [Helm 차트](https://github.com/microsoft/azure-synapse-spark-metrics/tree/main/helm)를 제공합니다. Helm 차트는 Apache Spark 애플리케이션 수준 메트릭에 대한 Prometheus 서버, Grafana 서버 및 Grafana 대시보드를 포함합니다. 널리 사용되는 오픈 소스 모니터링 시스템인 [Prometheus](https://prometheus.io/)를 사용하여 거의 실시간으로 이러한 메트릭을 수집하고 시각화를 위해 [Grafana](https://github.com/grafana/grafana)를 사용할 수 있습니다.

### <a name="synapse-prometheus-connector"></a>Synapse Prometheus 커넥터

Synapse Prometheus 커넥터는 Azure Synapse Apache Spark 풀과 Prometheus 서버를 연결하는 데 유용합니다. 이 커넥터는 다음을 구현합니다.

1.  인증: AAD 기반 인증이며 애플리케이션 검색, 메트릭 수집 및 기타 함수에 대한 서비스 주체의 AAD 토큰을 자동으로 새로 고칠 수 있습니다.
2.  Apache Spark 애플리케이션 검색: 대상 작업 영역에서 애플리케이션을 제출할 때 Synapse Prometheus 커넥터가 이러한 애플리케이션을 자동으로 검색할 수 있습니다.
3.  Apache Spark 애플리케이션 메타데이터: 기본 애플리케이션 정보를 수집하고 데이터를 Prometheus로 내보냅니다.

Synapse Prometheus 커넥터가 [Microsoft Container Registry](https://github.com/microsoft/containerregistry)에서 호스트되는 docker 이미지로 해제됩니다. 오픈 소스이며 [Azure Synapse Apache Spark 애플리케이션 메트릭](https://github.com/microsoft/azure-synapse-spark-metrics)에 있습니다.

### <a name="prometheus-server"></a>Prometheus 서버

Prometheus는 오픈 소스 모니터링 및 경고 도구 키트입니다. Prometheus는 CNCF(Cloud Native Computing Foundation)에서 확장되었으며 클라우드 네이티브 모니터링에 대한 사실상의 표준이 되었습니다. Prometheus는 방대한 양의 시계열 데이터를 수집, 쿼리 및 저장하는 데 유용하며 Grafana와 쉽게 통합될 수 있습니다. 이 솔루션에서는 helm 차트를 기준으로 하는 Prometheus 구성 요소를 배포합니다.

### <a name="grafana-and-dashboards"></a>Grafana 및 대시보드

Grafana은 오픈 소스 시각화 및 분석 소프트웨어입니다. 이를 통해 메트릭을 쿼리, 시각화, 경고 및 탐색할 수 있습니다. Azure Synapse Analytics는 Apache Spark 애플리케이션 수준 메트릭을 시각화하기 위한 기본 Grafana 대시보드 세트를 제공합니다.

"Synapse 작업 영역/작업 영역" 대시보드는 모든 Apache Spark 풀, 애플리케이션 개수, cpu 코어 등에 대한 작업 영역 수준 보기를 제공합니다.

[![스크린샷 대시보드 작업 영역](./media/monitor-azure-synapse-spark-application-level-metrics/screenshot-dashboard-workspace.png)](./media/monitor-azure-synapse-spark-application-level-metrics/screenshot-dashboard-workspace.png#lightbox)

"Synapse 작업 영역/Apache Spark 풀" 대시보드에는 해당 기간 동안 선택한 Apache Spark 풀에서 실행되는 Apache Spark 애플리케이션의 메트릭이 포함됩니다.

[![스크린샷 대시보드 sparkpool](./media/monitor-azure-synapse-spark-application-level-metrics/screenshot-dashboard-sparkpool.png)](./media/monitor-azure-synapse-spark-application-level-metrics/screenshot-dashboard-sparkpool.png#lightbox)

"Synapse 작업 영역/Apache Spark 애플리케이션" 대시보드에는 선택한 Apache Spark 애플리케이션이 포함되어 있습니다.

[![스크린샷 대시보드 애플리케이션](./media/monitor-azure-synapse-spark-application-level-metrics/screenshot-dashboard-application.png)](./media/monitor-azure-synapse-spark-application-level-metrics/screenshot-dashboard-application.png#lightbox)

위의 대시보드 템플릿은 [Azure Synapse Apache Spark 애플리케이션 메트릭](https://github.com/microsoft/azure-synapse-spark-metrics/tree/main/helm/synapse-prometheus-operator/grafana_dashboards)에 소스가 공개되어 있습니다.