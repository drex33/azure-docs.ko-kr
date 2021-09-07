---
title: Kubernetes 도구로 데이터 컨트롤러 만들기
description: Kubernetes 도구로 데이터 컨트롤러 만들기
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 07/30/2021
ms.topic: how-to
ms.openlocfilehash: 9f7f5569d5381a7d1ff4d7ebbeac535105f22c93
ms.sourcegitcommit: 86ca8301fdd00ff300e87f04126b636bae62ca8a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/16/2021
ms.locfileid: "122567752"
---
# <a name="create-azure-arc-data-controller-using-kubernetes-tools"></a>Kubernetes 도구로 Azure Arc 데이터 컨트롤러 만들기


## <a name="prerequisites"></a>사전 요구 사항

개요 정보는 [Azure Arc 데이터 컨트롤러 만들기](create-data-controller.md) 항목을 검토하세요.

Kubernetes 도구를 사용하여 Azure Arc 데이터 컨트롤러를 만들려면 Kubernetes 도구가 설치되어 있어야 합니다.  이 문서의 예제에서는 `kubectl`을 사용하지만 이와 마찬가지로, 관련 도구와 Kubernetes yaml/json에 익숙하다면 Kubernetes 대시보드, `oc` 또는 `helm` 등의 다른 Kubernetes 도구를 사용할 수도 있습니다.

[kubectl 도구 설치](https://kubernetes.io/docs/tasks/tools/install-kubectl/)

> [!NOTE]
> 아래에 표시된 Azure Arc 데이터 컨트롤러를 만드는 단계 중 일부에서는 Kubernetes 클러스터 관리자 권한이 필요합니다.  Kubernetes 클러스터 관리자가 아닌 경우 Kubernetes 클러스터 관리자가 사용자 대신 이러한 단계를 수행하도록 해야 합니다.

### <a name="cleanup-from-past-installations"></a>이전 설치에서 정리

과거에 같은 클러스터에 Azure Arc 데이터 컨트롤러를 설치했다가 삭제한 경우, 함께 삭제해야 하는 클러스터 수준 개체가 있을 수 있습니다. 다음 명령을 실행하여 Azure Arc 데이터 컨트롤러 클러스터 수준 개체를 삭제합니다.

```console
# Cleanup azure arc data service artifacts

# Note: not all of these objects will exist in your environment depending on which version of the Arc data controller was installed

# Custom resource definitions (CRD)
kubectl delete crd datacontrollers.arcdata.microsoft.com
kubectl delete crd postgresqls.arcdata.microsoft.com
kubectl delete crd sqlmanagedinstances.sql.arcdata.microsoft.com
kubectl delete crd sqlmanagedinstancerestoretasks.tasks.sql.arcdata.microsoft.com
kubectl delete crd dags.sql.arcdata.microsoft.com
kubectl delete crd exporttasks.tasks.arcdata.microsoft.com
kubectl delete crd monitors.arcdata.microsoft.com

# Cluster roles and role bindings
kubectl delete clusterrole arcdataservices-extension
kubectl delete clusterrole arc:cr-arc-metricsdc-reader
kubectl delete clusterrole arc:cr-arc-dc-watch
kubectl delete clusterrole cr-arc-webhook-job

# Substitute the name of the namespace the data controller was deployed in into {namespace}.  If unsure, get the name of the mutatingwebhookconfiguration using 'kubectl get clusterrolebinding'
kubectl delete clusterrolebinding {namespace}:crb-arc-metricsdc-reader
kubectl delete clusterrolebinding {namespace}:crb-arc-dc-watch
kubectl delete clusterrolebinding crb-arc-webhook-job

# API services
# Up to May 2021 release
kubectl delete apiservice v1alpha1.arcdata.microsoft.com
kubectl delete apiservice v1alpha1.sql.arcdata.microsoft.com

# June 2021 release
kubectl delete apiservice v1beta1.arcdata.microsoft.com
kubectl delete apiservice v1beta1.sql.arcdata.microsoft.com

# GA/July 2021 release
kubectl delete apiservice v1.arcdata.microsoft.com
kubectl delete apiservice v1.sql.arcdata.microsoft.com

# Substitute the name of the namespace the data controller was deployed in into {namespace}.  If unsure, get the name of the mutatingwebhookconfiguration using 'kubectl get mutatingwebhookconfiguration'
kubectl delete mutatingwebhookconfiguration arcdata.microsoft.com-webhook-{namespace}

```

## <a name="overview"></a>개요

다음과 같은 대략적인 단계로 Azure Arc 데이터 컨트롤러를 만듭니다.

   > [!IMPORTANT]
   > 아래의 일부 단계에서는 Kubernetes 클러스터 관리자 권한이 필요합니다.

1. Arc 데이터 컨트롤러, Azure SQL Managed Instance 및 PostgreSQL 하이퍼스케일에 대한 사용자 지정 리소스 정의를 만듭니다. 
1. 데이터 컨트롤러가 만들어지는 네임스페이스 만들기 
1. 복제본 집합, 서비스 계정, 역할, 역할 바인딩을 포함하여 부트스트래퍼 서비스를 만듭니다.
1. 데이터 컨트롤러 관리자 사용자 이름 및 암호에 대한 비밀을 만듭니다.
1. 웹후크 배포 작업, 클러스터 역할 및 클러스터 역할 바인딩을 만듭니다. 
1. 데이터 컨트롤러를 만듭니다.


## <a name="create-the-custom-resource-definitions"></a>사용자 지정 리소스 정의 만들기

다음 명령을 실행하여 사용자 지정 리소스 정의를 만듭니다.  

   > [!IMPORTANT]
   > Kubernetes 클러스터 관리자 권한이 필요합니다.

```console
kubectl create -f https://raw.githubusercontent.com/microsoft/azure_arc/main/arc_data_services/deploy/yaml/custom-resource-definitions.yaml
```

## <a name="create-a-namespace-in-which-the-data-controller-will-be-created"></a>데이터 컨트롤러가 만들어지는 네임스페이스 만들기

다음과 유사한 명령을 실행하여 데이터 컨트롤러가 만들어질 새 전용 네임스페이스를 만듭니다.  이 예제와 이 문서의 나머지 예제에서는 네임스페이스 이름 `arc`를 사용합니다. 다른 이름을 사용하도록 선택할 경우, 전체에서 같은 이름을 사용합니다.

```console
kubectl create namespace arc
```
OpenShift를 사용하는 경우 `kubectl edit namespace <name of namespace>`를 사용하여 네임스페이스의 `openshift.io/sa.scc.supplemental-groups` 및 `openshift.io/sa.scc.uid-range` 주석을 편집해야 합니다.  이러한 기존 주석을 이러한 _특정_ UID 및 fsGroup ID/범위와 일치하도록 변경합니다.

```console
openshift.io/sa.scc.supplemental-groups: 1000700001/10000
openshift.io/sa.scc.uid-range: 1000700001/10000
```

클러스터 관리자가 아닌 다른 사용자가 이 네임스페이스를 사용할 경우, 네임스페이스 관리자 역할을 만들고 역할 바인딩을 통해 사용자에게 해당 역할을 부여하는 것이 좋습니다.  네임스페이스 관리자는 해당 네임스페이스에 대한 모든 권한을 보유해야 합니다.  [Azure Arc GitHub 리포지토리](https://github.com/microsoft/azure_arc/tree/main/arc_data_services/deploy/yaml/rbac)에서 보다 세분화된 역할 및 예제 역할 바인딩을 찾을 수 있습니다.

## <a name="create-the-bootstrapper-service"></a>부트스트래퍼 서비스 만들기

부트스트래퍼 서비스는 데이터 컨트롤러, SQL Managed Instance 또는 PostgreSQL 하이퍼스케일 서버 그룹 등 사용자 지정 리소스의 만들기, 편집 및 삭제를 위해 들어오는 요청을 처리합니다.

다음 명령을 실행하여 부트스트래퍼 서비스, 부트스트래퍼 서비스의 서비스 계정, 역할, 부트스트래퍼 서비스 계정에 대한 역할 바인딩을 만듭니다.

```console
kubectl create --namespace arc -f https://raw.githubusercontent.com/microsoft/azure_arc/main/arc_data_services/deploy/yaml/bootstrapper.yaml
```

다음 명령으로 부트스트래퍼 Pod가 실행 중인지 확인합니다.  상태가 `Running`으로 변경될 때까지 몇 차례 실행해야 할 수 있습니다.

```console
kubectl get pod --namespace arc
```

bootstrapper.yaml 템플릿 파일은 기본적으로 MCR(Microsoft Container Registry)에서 부트스트래퍼 컨테이너 이미지를 풀합니다.  사용자 환경에 Microsoft Container Registry에 대한 직접 액세스 권한이 없는 경우 다음을 수행할 수 있습니다.
- [Microsoft Container Registry에서 컨테이너 이미지를 풀하여 프라이빗 컨테이너 레지스트리에 푸시](offline-deployment.md)하는 단계를 수행합니다.
- 프라이빗 컨테이너 레지스트리에 대한 [이미지 풀 비밀을 만듭니다](https://kubernetes.io/docs/tasks/configure-pod-container/pull-image-private-registry/#create-a-secret-by-providing-credentials-on-the-command-lin).
- 부트스트래퍼 컨테이너에 이미지 풀 비밀을 추가합니다. 아래 예제를 참조하세요.
- 부트스트래퍼 이미지 위치를 변경합니다. 아래 예제를 참조하세요.

아래 예에서는 이미지 풀 비밀 이름 `arc-private-registry`를 만들었다고 가정합니다.

```yaml
#Just showing only the relevant part of the bootstrapper.yaml template file here
    spec:
      serviceAccountName: sa-bootstrapper
      nodeSelector:
        kubernetes.io/os: linux
      imagePullSecrets:
      - name: arc-private-registry #Create this image pull secret if you are using a private container registry
      containers:
      - name: bootstrapper
        image: mcr.microsoft.com/arcdata/arc-bootstrapper:v1.0.0_2021-07-30 #Change this registry location if you are using a private container registry.
        imagePullPolicy: Always
```

## <a name="create-a-secret-for-the-kibanagrafana-dashboards"></a>Kibana/Grafana 대시보드의 비밀 만들기

사용자 이름 및 암호는 Kibana 및 Grafana 대시보드에서 관리자 권한으로 인증을 받는 데 사용됩니다.  보안 암호를 선택하고 이러한 권한이 필요한 암호를 사용하여 공유합니다.

Kubernetes 비밀은 Base64로 인코딩한 문자열로, 사용자 이름을 위해 하나, 암호를 위해 하나가 저장됩니다.

원하는 사용자 이름과 암호를 Base64로 인코딩하기 위해 온라인 도구를 사용하거나, 플랫폼에 따라 기본 제공되는 CLI 도구를 사용할 수 있습니다.

PowerShell

```console
[Convert]::ToBase64String([System.Text.Encoding]::Unicode.GetBytes('<your string to encode here>'))

#Example
#[Convert]::ToBase64String([System.Text.Encoding]::Unicode.GetBytes('example'))

```

Linux/macOS

```console
echo -n '<your string to encode here>' | base64

#Example
# echo -n 'example' | base64
```

사용자 이름과 암호를 인코딩한 후에는 [템플릿 파일](https://raw.githubusercontent.com/microsoft/azure_arc/main/arc_data_services/deploy/yaml/controller-login-secret.yaml)을 기반으로 파일을 만들고 사용자 이름과 암호 값을 사용자 고유 값으로 바꿀 수 있습니다.

이 후 다음 명령을 실행하여 비밀을 만듭니다.

```console
kubectl create --namespace arc -f <path to your data controller secret file>

#Example
kubectl create --namespace arc -f C:\arc-data-services\controller-login-secret.yaml
```

## <a name="create-the-webhook-deployment-job-cluster-role-and-cluster-role-binding"></a>웹후크 배포 작업, 클러스터 역할 및 클러스터 역할 바인딩을 만들기

먼저 일부 설정을 수정할 수 있도록 컴퓨터에 로컬로 [템플릿 파일](https://raw.githubusercontent.com/microsoft/azure_arc/main/arc_data_services/deploy/yaml/web-hook.yaml)의 사본을 만듭니다.

파일을 편집하고 모든 위치의 `{{namespace}}`를 이전 단계에서 만든 네임스페이스의 이름으로 바꿉니다. **파일을 저장합니다.**

다음 명령을 실행하여 클러스터 역할 및 클러스터 역할 바인딩을 만듭니다.  

   > [!IMPORTANT]
   > Kubernetes 클러스터 관리자 권한이 필요합니다.

```console
kubectl create -n arc -f <path to the edited template file on your computer>
```


## <a name="create-the-data-controller"></a>데이터 컨트롤러 만들기

이제 데이터 컨트롤러 자체를 만들 준비가 되었습니다.

먼저 일부 설정을 수정할 수 있도록 컴퓨터에 로컬로 [템플릿 파일](https://raw.githubusercontent.com/microsoft/azure_arc/main/arc_data_services/deploy/yaml/data-controller.yaml)의 사본을 만듭니다.

필요에 따라 다음을 편집합니다.

**필수**
- **location**: 이 항목을, 데이터 컨트롤러 관련 _메타데이터_ 가 저장될 Azure 위치로 변경합니다.  [데이터 컨트롤러 만들기 개요](create-data-controller.md) 문서에서 사용 가능한 Azure 위치 목록을 볼 수 있습니다.
- **resourceGroup**: Azure Resource Manager에서 데이터 컨트롤러 Azure 리소스를 만들려는 Azure 리소스 그룹입니다.  일반적으로 이 리소스 그룹은 미리 있어야 하지만 데이터를 Azure하는 시점까지는 필요하지 않습니다.
- **subscription**: Azure 리소스를 만들려는 구독에 대한 Azure 구독 GUID입니다.

**기본값을 검토하고 해당하면 변경 권장**
- **storage..className**: 데이터 컨트롤러 데이터와 로그 파일에 사용할 스토리지 클래스입니다.  Kubernetes 클러스터에서 사용 가능한 스토리지 클래스를 확실히 모르겠으면 `kubectl get storageclass` 명령을 실행합니다.  기본값은 `default`로, 스토리지 클래스가 있고 이름이 `default`이며 기본값 _인_ 스토리지 클래스가 없다고 가정합니다.  참고: 원하는 스토리지 클래스에 대해 데이터에 대해 하나, 로그에 대해 하나 등 두 className 설정을 설정합니다.
- **serviceType**: LoadBalancer를 사용하지 않는 경우 서비스 유형을 `NodePort`로 변경합니다.
- **보안** Azure Red Hat OpenShift 또는 Red Hat OpenShift 컨테이너 플랫폼의 경우 데이터 컨트롤러 yaml 파일에서 `security:` 설정을 다음 값으로 바꿉니다.

```yml
  security:
    allowDumps: false
    allowNodeMetricsCollection: false
    allowPodMetricsCollection: false
```

**선택 사항**
- **name**: 데이터 컨트롤러의 기본 이름은 `arc`이지만 원하는 경우 변경할 수 있습니다.
- **displayName**: 이 값을 파일 맨 위의 이름 특성과 동일한 값으로 설정합니다.
- **registry**: Microsoft Container Registry가 기본값입니다.  Microsoft Container Registry에서 이미지를 끌어오고 [프라이빗 컨테이너 레지스트리에 푸시](offline-deployment.md)하는 경우 여기에 레지스트리의 IP 주소 또는 DNS 이름을 입력합니다.
- **dockerRegistry**: 필요한 경우 프라이빗 컨테이너 레지스트리에서 이미지를 풀하는 데 사용할 이미지 풀 비밀입니다.
- **repository**: Microsoft Container Registry의 기본 리포지토리는 `arcdata`입니다.  프라이빗 컨테이너 레지스트리를 사용할 경우 Azure Arc 지원 데이터 서비스 컨테이너 이미지를 포함하는 폴더/리포지토리의 경로를 입력합니다.
- **imageTag**: 현재 최신 버전 태그는 템플릿에서 기본값으로 사용되지만 그 이전 버전을 사용하려는 경우 변경할 수 있습니다.

다음 예제에서는 완료된 데이터 컨트롤러 yaml 파일을 보여줍니다. 사용자의 요구 사항 및 위의 정보에 따라 사용자 환경에 대해 예제를 업데이트합니다.

```yml
apiVersion: v1
kind: ServiceAccount
metadata:
  name: sa-mssql-controller
---
apiVersion: arcdata.microsoft.com/v1
kind: DataController
metadata:
  generation: 1
  name: arc-dc
spec:
  credentials:
    controllerAdmin: controller-login-secret
    dockerRegistry: arc-private-registry #Create a registry secret named 'arc-private-registry' if you are going to pull from a private registry instead of MCR.
    serviceAccount: sa-arc-controller
  docker:
    imagePullPolicy: Always
    imageTag: v1.0.0_2021-07-30
    registry: mcr.microsoft.com
    repository: arcdata
  infrastructure: other #Must be a value in the array [alibaba, aws, azure, gcp, onpremises, other]
  security:
    allowDumps: true #Set this to false if deploying on OpenShift
    allowNodeMetricsCollection: true #Set this to false if deploying on OpenShift
    allowPodMetricsCollection: true #Set this to false if deploying on OpenShift
  services:
  - name: controller
    port: 30080
    serviceType: LoadBalancer # Modify serviceType based on your Kubernetes environment
  settings:
    ElasticSearch:
      vm.max_map_count: "-1"
    azure:
      connectionMode: indirect
      location: eastus # Choose a different Azure location if you want
      resourceGroup: <your resource group>
      subscription: <your subscription GUID>
    controller:
      displayName: arc-dc
      enableBilling: "True"
      logs.rotation.days: "7"
      logs.rotation.size: "5000"
  storage:
    data:
      accessMode: ReadWriteOnce
      className: default # Use default configured storage class or modify storage class based on your Kubernetes environment
      size: 15Gi
    logs:
      accessMode: ReadWriteOnce
      className: default # Use default configured storage class or modify storage class based on your Kubernetes environment
      size: 10Gi
```

편집한 파일을 로컬 컴퓨터에 저장하고 다음 명령을 실행하여 데이터 컨트롤러를 만듭니다.

```console
kubectl create --namespace arc -f <path to your data controller file>

#Example
kubectl create --namespace arc -f C:\arc-data-services\data-controller.yaml
```

## <a name="monitoring-the-creation-status"></a>생성 상태 모니터링

컨트롤러 만들기를 완료하려면 몇 분 정도 걸립니다. 다음 명령을 사용하여 다른 터미널 창에서 진행률을 모니터링할 수 있습니다.

> [!NOTE]
>  아래 예제 명령에서는 `arc`라는 이름으로 데이터 컨트롤러 및 Kubernetes 네임스페이스를 만들었다고 가정합니다.  다른 네임스페이스/데이터 컨트롤러 이름을 사용한 경우 `arc`를 해당 이름으로 바꿀 수 있습니다.

```console
kubectl get datacontroller/arc --namespace arc
```

```console
kubectl get pods --namespace arc
```

아래와 같은 명령을 실행하여 특정 Pod의 생성 상태를 확인할 수도 있습니다.  이 기능은 문제를 해결하는 데 특히 유용합니다.

```console
kubectl describe pod/<pod name> --namespace arc

#Example:
#kubectl describe pod/control-2g7bl --namespace arc
```

## <a name="troubleshooting-creation-problems"></a>생성 문제 해결

생성 관련 문제가 발생하는 경우 [문제 해결 가이드](troubleshoot-guide.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

- [Kubernetes 원시 도구를 사용해 SQL Managed Instance 만들기](./create-sql-managed-instance-using-kubernetes-native-tools.md)
- [Kubernetes 원시 도구를 사용하여 PostgreSQL 하이퍼스케일 서버 그룹 만들기](./create-postgresql-hyperscale-server-group-kubernetes-native-tools.md)
