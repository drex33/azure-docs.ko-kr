---
title: CLI를 사용하여 데이터 컨트롤러 만들기
description: CLI를 사용하여 이미 만든 일반 다중 노드 Kubernetes 클러스터에 Azure Arc 데이터 컨트롤러를 만듭니다.
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: dnethi
ms.author: dinethi
ms.reviewer: mikeray
ms.date: 11/03/2021
ms.topic: how-to
ms.openlocfilehash: ed542f50c499d7cab1c796fe190bdcd52ae5e5ad
ms.sourcegitcommit: 01b678462a4a390c30463c525432ffbbbe0195cf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/23/2021
ms.locfileid: "132957206"
---
# <a name="create-azure-arc-data-controller-using-the-cli"></a>CLI를 사용하여 Azure Arc 데이터 컨트롤러 만들기


## <a name="prerequisites"></a>필수 구성 요소

개요 정보는 Azure Arc 지원 데이터 서비스 배포 계획 항목을 [검토하세요.](plan-azure-arc-data-services.md)

### <a name="install-tools"></a>도구 설치

CLI를 사용하여 데이터 컨트롤러를 만들려면 Azure(az) CLI용 `arcdata` 확장을 설치해야 합니다. 

[[!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] 설치](install-client-tools.md)

선택한 대상 플랫폼에 관계없이 데이터 컨트롤러를 만들기 전에 다음 환경 변수를 설정해야 합니다. 이러한 환경 변수는 데이터 컨트롤러를 만든 후 메트릭 및 로그 대시보드에 액세스하는 데 사용되는 자격 증명이 됩니다.


### <a name="set-environment-variables"></a>환경 변수 설정

다음은 메트릭 및 로그 대시보드에 액세스하는 데 필요한 두 가지 환경 변수 집합입니다.

환경 변수에는 로그 및 메트릭 서비스에 대한 암호가 포함됩니다. 암호는 8자 이상이어야 하며 라틴어 대문자, 라틴어 소문자, 숫자 및 영숫자가 아닌 문자의 네 가지 범주 중 세 가지 범주의 문자를 포함해야 합니다.


# <a name="linux"></a>[Linux](#tab/linux)

```console
## variables for Metrics and Monitoring dashboard credentials
export AZDATA_LOGSUI_USERNAME=<username for Kibana dashboard>
export AZDATA_LOGSUI_PASSWORD=<password for Kibana dashboard>
export AZDATA_METRICSUI_USERNAME=<username for Grafana dashboard>
export AZDATA_METRICSUI_PASSWORD=<password for Grafana dashboard>
```

# <a name="windows-powershell"></a>[Windows(PowerShell)](#tab/windows)

```PowerShell
## variables for Metrics and Monitoring dashboard credentials
$ENV:AZDATA_LOGSUI_USERNAME="<username for Kibana dashboard>"
$ENV:AZDATA_LOGSUI_PASSWORD="<password for Kibana dashboard>"
$ENV:AZDATA_METRICSUI_USERNAME="<username for Grafana dashboard>"
$ENV:AZDATA_METRICSUI_PASSWORD="<password for Grafana dashboard>"
```

--- 

### <a name="connect-to-kubernetes-cluster"></a>Kubernetes 클러스터에 커넥트

Azure Arc 데이터 컨트롤러를 만들기 전에 Kubernetes 클러스터에 연결하여 인증하고 기존 Kubernetes 컨텍스트를 선택해야 합니다. Kubernetes 클러스터 또는 서비스에 연결하는 방법은 다양합니다. Kubernetes API 서버에 연결하는 방법에 대해서는 사용 중인 Kubernetes 배포 또는 서비스에 대한 문서를 참조하세요.

현재 Kubernetes 연결이 있는지 확인하고 다음 명령을 사용하여 현재 컨텍스트를 확인할 수 있습니다.

```console
kubectl cluster-info
kubectl config current-context
```

## <a name="create-the-azure-arc-data-controller"></a>Azure Arc 데이터 컨트롤러 만들기

다음 섹션에서는 특정 유형의 Kubernetes 플랫폼에 대한 지침을 제공합니다. 플랫폼에 대한 지침을 따릅니다.

- [AKS(Azure Kubernetes Service)](#create-on-azure-kubernetes-service-aks)
- [Azure Stack HCI의 AKS](#create-on-aks-on-azure-stack-hci)
- [ARO(Azure Red Hat OpenShift)](#create-on-azure-red-hat-openshift-aro)
- [Red Hat OCP(OpenShift Container Platform)](#create-on-red-hat-openshift-container-platform-ocp)
- [오픈 소스, 업스트림 Kubernetes(kubeadm)](#create-on-open-source-upstream-kubernetes-kubeadm)
- [AWS EKS(Elastic Kubernetes Service)](#create-on-aws-elastic-kubernetes-service-eks)
- [Google Cloud Kubernetes Engine Service(GKE)](#create-on-google-cloud-kubernetes-engine-service-gke)

> [!TIP]
> Kubernetes 클러스터가 없는 경우 Azure에서 만들 수 있습니다. [AKS에서 클러스터 만들기의](create-aks-cluster-data-controller.md)지침을 따릅니다. 
>
> 그런 다음 [AKS(Azure Kubernetes Service 만들기)](#create-on-azure-kubernetes-service-aks)의 지침을 따릅니다.

## <a name="create-on-azure-kubernetes-service-aks"></a>AKS(Azure Kubernetes Service)에서 만들기

기본적으로 AKS 배포 프로필은 `managed-premium` 스토리지 클래스를 사용합니다. `managed-premium` 스토리지 클래스는 프리미엄 디스크가 있는 VM 이미지를 사용하여 배포된 VM이 있는 경우에만 작동합니다.

`managed-premium`을 스토리지 클래스로 사용하려는 경우 다음 명령을 실행하여 데이터 컨트롤러를 만들 수 있습니다. 명령의 자리 표시자를 리소스 그룹 이름, 구독 ID 및 Azure 위치로 대체합니다.

```azurecli
az arcdata dc create --profile-name azure-arc-aks-premium-storage --k8s-namespace <namespace> --name arc --azure-subscription <subscription id> --resource-group <resource group name> --location <location> --connectivity-mode indirect --use-k8s

#Example:
#az arcdata dc create --profile-name azure-arc-aks-premium-storage --k8s-namespace arc --name arc --azure-subscription xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx --resource-group my-resource-group --location eastus --connectivity-mode indirect --use-k8s
```

사용할 스토리지 클래스가 확실하지 않은 경우 사용 중인 VM 유형에 관계없이 지원되는 `default` 스토리지 클래스를 사용해야 합니다. 가장 빠른 성능을 제공하지는 않습니다.

`default` 스토리지 클래스를 사용하려는 경우 다음 명령을 실행할 수 있습니다.

```azurecli
az arcdata dc create --profile-name azure-arc-aks-default-storage --k8s-namespace <namespace> --use-k8s --name arc --subscription <subscription id> --resource-group <resource group name> --location <location> --connectivity-mode indirect

#Example:
#az arcdata dc create --profile-name azure-arc-aks-default-storage  --k8s-namespace arc --use-k8s --name arc --subscription xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx --resource-group my-resource-group --location eastus --connectivity-mode indirect
```

명령을 실행한 후 [만들기 상태 모니터링](#monitor-the-creation-status)으로 계속 진행합니다.

## <a name="create-on-aks-on-azure-stack-hci"></a>Azure Stack HCI의 AKS에서 만들기

### <a name="configure-storage-azure-stack-hci-with-aks-hci"></a>스토리지 구성(AKS-HCI가 포함된 Azure Stack HCI)

AKS-HCI에서 Azure Stack HCI 사용하는 경우 를 사용하여 사용자 지정 스토리지 클래스를 `fsType` 만듭니다.

   ```json
   fsType: ext4
   ```

이 형식을 사용하여 데이터 컨트롤러를 배포합니다. [Azure Stack HCI의 AKS 디스크에 대한 사용자 지정 스토리지 클래스 만들기](/azure-stack/aks-hci/container-storage-interface-disks#create-a-custom-storage-class-for-an-aks-on-azure-stack-hci-disk)의 전체 지침을 참조하세요.

기본적으로 배포 프로필은 `default`라는 스토리지 클래스와 서비스 유형 `LoadBalancer`를 사용합니다.

다음 명령을 실행하여 `default` 스토리지 클래스와 서비스 유형 `LoadBalancer`를 사용하여 데이터 컨트롤러를 만들 수 있습니다.

```azurecli
az arcdata dc create --profile-name azure-arc-aks-hci  --k8s-namespace <namespace> --use-k8s --name arc --subscription <subscription id> --resource-group <resource group name> --location <location> --connectivity-mode indirect

#Example:
#az arcdata dc create --profile-name azure-arc-aks-hci  --k8s-namespace arc --use-k8s --name arc --subscription xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx --resource-group my-resource-group --location eastus --connectivity-mode indirect
```

명령을 실행한 후 [만들기 상태 모니터링](#monitor-the-creation-status)으로 계속 진행합니다.

## <a name="create-on-azure-red-hat-openshift-aro"></a>ARO(Azure Red Hat OpenShift)에서 만들기

### <a name="create-custom-deployment-profile"></a>사용자 지정 배포 프로필 만들기

Azure Red Hat OpenShift에 `azure-arc-azure-openshift` 프로필을 사용합니다.

```azurecli
az arcdata dc config init --source azure-arc-azure-openshift --path ./custom
```

### <a name="create-data-controller"></a>데이터 컨트롤러 만들기

다음 명령을 실행하여 데이터 컨트롤러를 만들 수 있습니다.

```azurecli
az arcdata dc create --profile-name azure-arc-azure-openshift  --k8s-namespace <namespace> --use-k8s --name arc --subscription <subscription id> --resource-group <resource group name> --location <location> --connectivity-mode indirect

#Example
#az arcdata dc create --profile-name azure-arc-azure-openshift  --k8s-namespace arc --use-k8s --name arc --subscription xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx --resource-group my-resource-group --location eastus --connectivity-mode indirect
```

명령을 실행한 후 [만들기 상태 모니터링](#monitor-the-creation-status)으로 계속 진행합니다.

## <a name="create-on-red-hat-openshift-container-platform-ocp"></a>Red Hat OCP(OpenShift Container Platform)에서 만들기

### <a name="determine-storage-class"></a>스토리지 클래스 결정

또한 다음 명령을 실행하여 사용할 스토리지 클래스를 결정해야 합니다.

```console
kubectl get storageclass
```

### <a name="create-custom-deployment-profile"></a>사용자 지정 배포 프로필 만들기

다음 명령을 실행하여 `azure-arc-openshift` 배포 프로필을 기반으로 새 사용자 지정 배포 프로필 파일을 만듭니다. 이 명령은 현재 작업 디렉터리에 `custom` 디렉터리를 만들고 해당 디렉터리에 사용자 지정 배치 프로필 파일 `control.json`을 만듭니다.

OpenShift Container Platform에 `azure-arc-openshift` 프로필을 사용합니다.

```azurecli
az arcdata dc config init --source azure-arc-openshift --path ./custom
```

### <a name="set-storage-class"></a>스토리지 클래스 설정 

이제 아래 명령의 `<storageclassname>`을 위의 `kubectl get storageclass` 명령을 실행하여 결정된 스토리지 클래스의 이름으로 대체하여 원하는 스토리지 클래스를 설정합니다.

```azurecli
az arcdata dc config replace --path ./custom/control.json --json-values "spec.storage.data.className=<storageclassname>"
az arcdata dc config replace --path ./custom/control.json --json-values "spec.storage.logs.className=<storageclassname>"

#Example:
#az arcdata dc config replace --path ./custom/control.json --json-values "spec.storage.data.className=mystorageclass"
#az arcdata dc config replace --path ./custom/control.json --json-values "spec.storage.logs.className=mystorageclass"
```

### <a name="set-loadbalancer-optional"></a>LoadBalancer 설정(선택 사항)

기본적으로 `azure-arc-openshift` 배포 프로필은 `NodePort`를 서비스 유형으로 사용합니다. 부하 분산 장치와 통합된 OpenShift 클러스터를 사용하는 경우 다음 명령을 사용하여 `LoadBalancer` 서비스 유형을 사용하도록 구성을 변경할 수 있습니다.

```azurecli
az arcdata dc config replace --path ./custom/control.json --json-values "$.spec.services[*].serviceType=LoadBalancer"
```

### <a name="create-data-controller"></a>데이터 컨트롤러 만들기

이제 다음 명령을 사용하여 데이터 컨트롤러를 만들 준비가 되었습니다.

> [!NOTE]
>   `--path` 매개 변수는 control.json 파일 자체가 아니라 control.json 파일이 포함된 _디렉터리_ 를 가리켜야 합니다.

> [!NOTE]
>   OpenShift Container Platform에 배포하는 경우 `--infrastructure` 매개 변수 값을 지정해야 합니다.  옵션은 `aws`, `azure`, `alibaba`, `gcp`, `onpremises`입니다.

```azurecli
az arcdata dc create --path ./custom  --k8s-namespace <namespace> --use-k8s --name arc --subscription <subscription id> --resource-group <resource group name> --location <location> --connectivity-mode indirect --infrastructure <infrastructure>

#Example:
#az arcdata dc create --path ./custom  --k8s-namespace arc --use-k8s --name arc --subscription xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx --resource-group my-resource-group --location eastus --connectivity-mode indirect --infrastructure onpremises
```

명령을 실행한 후 [만들기 상태 모니터링](#monitor-the-creation-status)으로 계속 진행합니다.

## <a name="create-on-open-source-upstream-kubernetes-kubeadm"></a>오픈 소스, 업스트림 Kubernetes(kubeadm)에서 만들기

기본적으로 kubeadm 배치 프로파일은 `local-storage`라는 스토리지 클래스와 서비스 유형 `NodePort`를 사용합니다. 이것이 허용되는 경우 원하는 스토리지 클래스 및 서비스 유형을 설정하는 아래 지침을 건너뛰고 바로 아래의 `az arcdata dc create` 명령을 실행할 수 있습니다.

특정 스토리지 클래스 및/또는 서비스 유형을 지정하도록 배포 프로필을 사용자 지정하려면 다음 명령을 실행하여 kubeadm 배포 프로필을 기반으로 새 사용자 지정 배포 프로필 파일을 만듭니다. 이 명령은 현재 작업 디렉터리에 `custom` 디렉터리를 만들고 해당 디렉터리에 사용자 지정 배치 프로필 파일 `control.json`을 만듭니다.

```azurecli
az arcdata dc config init --source azure-arc-kubeadm --path ./custom 
```

다음 명령을 실행하여 사용 가능한 스토리지 클래스를 조회할 수 있습니다.

```console
kubectl get storageclass
```

이제 아래 명령의 `<storageclassname>`을 위의 `kubectl get storageclass` 명령을 실행하여 결정된 스토리지 클래스의 이름으로 대체하여 원하는 스토리지 클래스를 설정합니다.

```azurecli
az arcdata dc config replace --path ./custom/control.json --json-values "spec.storage.data.className=<storageclassname>"
az arcdata dc config replace --path ./custom/control.json --json-values "spec.storage.logs.className=<storageclassname>"

#Example:
#az arcdata dc config replace --path ./custom/control.json --json-values "spec.storage.data.className=mystorageclass"
#az arcdata dc config replace --path ./custom/control.json --json-values "spec.storage.logs.className=mystorageclass"
```

기본적으로 kubeadm 배포 프로필은 `NodePort`를 서비스 유형으로 사용합니다. 부하 분산 장치와 통합된 Kubernetes 클러스터를 사용하는 경우 다음 명령을 사용하여 구성을 변경할 수 있습니다.

```azurecli
az arcdata dc config replace --path ./custom/control.json --json-values "$.spec.services[*].serviceType=LoadBalancer" --k8s-namespace <namespace> --use-k8s
```

이제 다음 명령을 사용하여 데이터 컨트롤러를 만들 준비가 되었습니다.

> [!NOTE]
>   OpenShift Container Platform에 배포하는 경우 `--infrastructure` 매개 변수 값을 지정해야 합니다.  옵션은 `aws`, `azure`, `alibaba`, `gcp`, `onpremises`입니다.

```azurecli
az arcdata dc create --path ./custom  --k8s-namespace <namespace> --use-k8s --name arc --subscription <subscription id> --resource-group <resource group name> --location <location> --connectivity-mode indirect --infrastructure <infrastructure>

#Example:
#az arcdata dc create --path ./custom - --k8s-namespace <namespace> --use-k8s --name arc --subscription xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx --resource-group my-resource-group --location eastus --connectivity-mode indirect --infrastructure onpremises
```

명령을 실행한 후 [만들기 상태 모니터링](#monitor-the-creation-status)으로 계속 진행합니다.

## <a name="create-on-aws-elastic-kubernetes-service-eks"></a>AWS EKS(Elastic Kubernetes Service)에서 만들기

기본적으로 EKS 스토리지 클래스는 `gp2`이고 서비스 유형은 `LoadBalancer`입니다.

제공된 EKS 배포 프로필을 사용하여 데이터 컨트롤러를 만들려면 다음 명령을 실행합니다.

```azurecli
az arcdata dc create --profile-name azure-arc-eks  --k8s-namespace <namespace> --use-k8s --name arc --subscription <subscription id> --resource-group <resource group name> --location <location> --connectivity-mode indirect

#Example:
#az arcdata dc create --profile-name azure-arc-eks  --k8s-namespace <namespace> --use-k8s --name arc --subscription xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx --resource-group my-resource-group --location eastus --connectivity-mode indirect
```

명령을 실행한 후 [만들기 상태 모니터링](#monitor-the-creation-status)으로 계속 진행합니다.

## <a name="create-on-google-cloud-kubernetes-engine-service-gke"></a>GKE(Google Cloud Kubernetes Engine) 서비스에서 만들기

기본적으로 GKE 스토리지 클래스는 `standard`이고 서비스 유형은 `LoadBalancer`입니다.

제공된 GKE 배포 프로필을 사용하여 데이터 컨트롤러를 만들려면 다음 명령을 실행합니다.

```azurecli
az arcdata dc create --profile-name azure-arc-gke --k8s-namespace <namespace> --use-k8s --name arc --subscription <subscription id> --resource-group <resource group name> --location <location> --connectivity-mode indirect

#Example:
#az arcdata dc create --profile-name azure-arc-gke --k8s-namespace <namespace> --use-k8s --name arc --subscription xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx --resource-group my-resource-group --location eastus --connectivity-mode indirect
```

명령을 실행한 후 [만들기 상태 모니터링](#monitor-the-creation-status)으로 계속 진행합니다.

## <a name="monitor-the-creation-status"></a>만들기 상태 모니터링

컨트롤러 만들기를 완료하려면 몇 분 정도 걸립니다. 다음 명령을 사용하여 다른 터미널 창에서 진행률을 모니터링할 수 있습니다.

> [!NOTE]
>  아래 예제 명령에서는 `arc`라는 이름으로 데이터 컨트롤러 및 Kubernetes 네임스페이스를 만들었다고 가정합니다. 다른 네임스페이스/데이터 컨트롤러 이름을 사용한 경우 `arc`를 해당 이름으로 바꿀 수 있습니다.

```console
kubectl get datacontroller/arc --namespace arc
```

```console
kubectl get pods --namespace arc
```

아래와 같은 명령을 실행하여 특정 Pod의 생성 상태를 확인할 수도 있습니다. 이 기능은 문제를 해결하는 데 특히 유용합니다.

```console
kubectl describe po/<pod name> --namespace arc

#Example:
#kubectl describe po/control-2g7bl --namespace arc
```

## <a name="troubleshooting-creation-problems"></a>만들기 문제 해결

만들기 관련 문제가 발생하는 경우 [문제 해결 가이드](troubleshoot-guide.md)를 참조하세요.
