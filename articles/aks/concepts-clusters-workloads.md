---
title: 개념 - AKS(Azure Kubernetes Service)의 Kubernetes 기본 사항
description: Kubernetes의 기본 클러스터 및 워크로드 구성 요소와 AKS(Azure Kubernetes Service)의 기능과의 관계에 대해 알아봅니다.
services: container-service
ms.topic: conceptual
ms.date: 03/05/2020
ms.openlocfilehash: 4db6ee6d117f7743147dad43dcfff98dd99fd76d
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122536001"
---
# <a name="kubernetes-core-concepts-for-azure-kubernetes-service-aks"></a>AKS(Azure Kubernetes Service)의 Kubernetes 핵심 개념

애플리케이션 개발은 컨테이너 기반 방법으로 계속 이동하여 리소스를 오케스트레이션하고 관리해야 하는 필요성이 증가하고 있습니다. 선도적인 플랫폼인 Kubernetes는 내결함성 애플리케이션 워크로드를 안정적으로 예약합니다. 관리되는 Kubernetes 제품인 AKS(Asure Kubernetes Service)는 컨테이너 기반 애플리케이션 배포 및 관리를 더 간소화합니다.

이 문서에서는 다음을 소개합니다.
* 핵심 Kubernetes 인프라 구성 요소:
    * *컨트롤 플레인*
    * *노드*
    * *노드 풀*
* 워크로드 리소스: 
    * *Pod*
    * *배포*
    * *집합* 
* 리소스를 *네임스페이스* 로 그룹화하는 방법

## <a name="what-is-kubernetes"></a>Kubernetes란?

Kubernetes는 컨테이너 기반 애플리케이션과 관련 네트워킹 및 스토리지 구성 요소를 관리하는 플랫폼으로서 빠르게 진화하고 있습니다. Kubernetes는 기본 인프라 구성 요소가 아니라 애플리케이션 워크로드에 중점을 두고 있습니다. Kubernetes는 관리 작업을 위한 강력한 API 집합을 통해 지원되는 선언적 배포 방식을 제공합니다.

Kubernetes를 사용하여 애플리케이션 구성 요소의 가용성을 오케스트레이션하고 관리할 수 있는 이식 가능한 마이크로서비스 기반 최신 애플리케이션을 빌드하고 실행할 수 있습니다. Kubernetes는 팀에서 마이크로 서비스 기반 애플리케이션의 채택을 통해 진행함에 따라 상태 비저장 및 상태 저장 애플리케이션을 모두 지원합니다.

오픈 플랫폼인 Kubernetes를 사용하면 기본 설정 프로그래밍 언어, OS, 라이브러리 또는 메시지 버스를 사용하여 애플리케이션을 빌드할 수 있습니다. 기존의 CI/CD(지속적인 통합 및 지속적인 업데이트) 도구는 Kubernetes와 통합되어 릴리스를 예약하고 배포할 수 있습니다.

AKS는 배포의 복잡성과 핵심 관리 작업(예: 업그레이드 조정)을 줄이는 관리되는 Kubernetes 서비스를 제공합니다. Azure 플랫폼에서 AKS 컨트롤 플레인을 관리하며, 애플리케이션을 실행하는 AKS 노드에 대해서만 비용을 지불합니다. AKS는 오픈 소스 Azure Kubernetes Service 엔진([aks-engine][aks-engine])을 기반으로 합니다.

## <a name="kubernetes-cluster-architecture"></a>Kubernetes 클러스터 아키텍처

Kubernetes 클러스터는 다음 두 가지 구성 요소로 구분됩니다.

- *컨트롤 플레인*: 핵심 Kubernetes 서비스 및 애플리케이션 워크로드 오케스트레이션을 제공합니다.
- *노드*: 애플리케이션 워크로드를 실행합니다.

![Kubernetes 컨트롤 플레인 및 노드 구성 요소](media/concepts-clusters-workloads/control-plane-and-nodes.png)

## <a name="control-plane"></a>제어 평면

AKS 클러스터를 만들면 컨트롤 플레인이 자동으로 만들어지고 구성됩니다. 이 컨트롤 플레인은 사용자로부터 추상화된 관리형 Azure 리소스로 무료로 제공됩니다. AKS 클러스터에 연결된 노드에 대해서만 비용을 지불합니다. 컨트롤 플레인 및 해당 리소스는 클러스터를 만든 지역에만 있습니다.

컨트롤 플레인에 포함되는 핵심 Kubernetes 구성 요소는 다음과 같습니다.

| 구성 요소 | Description |  
| ----------------- | ------------- |  
| *kube-apiserver*                                                                                 | API 서버는 기본 Kubernetes API를 공개하는 방법입니다. 이 구성 요소는 `kubectl` 또는 Kubernetes 대시보드와 같은 관리 도구에 대한 상호 작용을 제공합니다.                                                        |  
| *etcd* | 고가용성 *etcd* 는 Kubernetes 클러스터 및 구성의 상태를 유지 관리하기 위해 Kubernetes 내에 있는 키 값 저장소입니다.                                      |  
| *kube-scheduler*                                                                            | 애플리케이션을 만들거나 스케일링할 때 스케줄러는 워크로드를 실행할 수 있는 노드를 결정하고 시작합니다.                                                                                    |  
| *kube-controller-manager*                                                                            | 컨트롤러 관리자는 Pod 복제 및 노드 작업 처리와 같은 작업을 수행하는 여러 작은 컨트롤러를 감독합니다.                                                                  |  

AKS는 전용 API 서버, 스케줄러 등이 포함된 단일 테넌트 컨트롤 플레인을 제공합니다. 노드의 수와 크기를 정의하고, Azure 플랫폼에서 컨트롤 플레인과 노드 간의 보안 통신을 구성합니다. 컨트롤 플레인과의 상호 작용은 `kubectl` 또는 Kubernetes 대시보드와 같은 Kubernetes API를 통해 수행됩니다.

이 관리형 컨트롤 플레인을 사용하여 구성 요소(예: 고가용성 *etcd* 저장소)를 구성할 필요는 없으며, 컨트롤 플레인에 직접 액세스할 수 없습니다. Kubernetes 컨트롤 플레인 및 노드 업그레이드는 Azure CLI 또는 Azure Portal을 통해 오케스트레이션됩니다. 가능한 문제를 해결하기 위해 Azure Monitor 로그를 통해 컨트롤 플레인 로그를 검토할 수 있습니다.

컨트롤 플레인을 구성하거나 직접 액세스하려면 [aks-engine][aks-engine]을 사용하여 사용자 고유의 Kubernetes 클러스터를 배포합니다.

관련 모범 사례는 [AKS의 클러스터 보안 및 업그레이드 모범 사례][operator-best-practices-cluster-security]를 참조하세요.

## <a name="nodes-and-node-pools"></a>노드 및 노드 풀

애플리케이션과 지원 서비스를 실행하려면 Kubernetes *노드* 가 필요합니다. AKS 클러스터에는 Kubernetes 노드 구성 요소 및 컨테이너 런타임을 실행하는 하나 이상의 노드, 즉 Azure VM(가상 머신)이 있습니다.

| 구성 요소 | Description |  
| ----------------- | ------------- |  
| `kubelet` | 컨트롤 플레인의 오케스트레이션 요청을 처리하고 요청된 컨테이너를 실행하도록 예약하는 Kubernetes 에이전트입니다. |  
| *kube-proxy* | 각 노드에서 가상 네트워킹을 처리합니다. 프록시는 네트워크 트래픽을 라우팅하고 서비스와 Pod에 대한 IP 주소 지정을 관리합니다. |  
| *컨테이너 런타임* | 컨테이너화된 애플리케이션에서 가상 네트워크 및 스토리지와 같은 추가 리소스를 실행하고 상호 작용할 수 있도록 합니다. Linux용 Kubernetes 버전 1.19 이상 노드 풀을 사용하는 AKS 클러스터는 `containerd`를 컨테이너 런타임으로 사용합니다. Windows용 Kubernetes 버전 1.20 노드 풀부터 `containerd`는 컨테이너 런타임에 대한 미리 보기에서 사용할 수 있지만 Docker는 여전히 기본 컨테이너 런타임입니다. 노드 풀에 이전 버전의 Kubernetes를 사용하는 AKS 클러스터는 Docker를 컨테이너 런타임으로 사용합니다. |  

![Kubernetes 노드용 Azure 가상 머신 및 지원 리소스](media/concepts-clusters-workloads/aks-node-resource-interactions.png)

노드의 Azure VM 크기는 스토리지 CPU 수, 메모리, 크기, 사용 가능한 유형(예: 고성능 SSD 또는 일반 HDD)을 정의합니다. 애플리케이션에 많은 양의 CPU 및 메모리 또는 고성능 스토리지가 필요한지 여부를 기준으로 노드 크기를 계획합니다. 요구 사항에 맞게 AKS 클러스터의 노드 수를 스케일 아웃합니다.

AKS에서 클러스터 노드의 VM 이미지는 Ubuntu Linux 또는 Windows Server 2019를 기반으로 합니다. AKS 클러스터를 만들거나 노드 수를 스케일 아웃하면 Azure 플랫폼에서 요청된 수의 VM을 자동으로 만들고 구성합니다. 에이전트 노드는 표준 VM으로 청구되므로 VM 크기 할인([Azure Reservations][reservation-discounts] 포함)이 자동으로 적용됩니다.

다른 호스트 OS, 컨테이너 런타임을 사용하거나 다른 사용자 지정 패키지를 포함하는 경우 [aks-engine][aks-engine]을 사용하여 사용자 고유의 Kubernetes 클러스터를 배포합니다. 업스트림 `aks-engine`은 기능을 릴리스하고, AKS 클러스터에서 지원하기 전에 구성 옵션을 제공합니다. 따라서 `containerd` 또는 Docker 이외의 컨테이너 런타임을 사용하려는 경우 `aks-engine`을 실행하여 현재 요구 사항에 맞게 Kubernetes 클러스터를 구성하고 배포할 수 있습니다.

### <a name="resource-reservations"></a>리소스 예약

AKS는 노드 리소스를 사용하여 노드가 클러스터의 일부로 작동하도록 지원합니다. 이를 사용하면 노드의 총 리소스와 AKS의 할당 가능한 리소스 간에 불일치가 발생할 수 있습니다. 사용자가 배포한 Pod에 대한 요청 및 제한을 설정할 때 이 정보를 잊지 마세요.

노드의 할당 가능한 리소스를 찾으려면 다음을 실행합니다.
```kubectl
kubectl describe node [NODE_NAME]
```

AKS는 노드 성능 및 기능을 유지하기 위해 각 노드에서 리소스를 예약합니다. 노드가 리소스에서 더 커질수록 사용자가 배포한 Pod를 관리할 필요성이 높아지므로 리소스 예약도 증가합니다.

>[!NOTE]
> 컨테이너 인사이트(OMS)와 같은 AKS 추가 기능을 사용하는 경우 추가 노드 리소스가 사용됩니다.

두 가지 종류의 리소스가 예약됩니다.

- **CPU**  
    예약 CPU는 노드 유형 및 클러스터 구성에 따라 달라지므로 추가 기능 실행으로 인해 할당 가능한 CPU가 줄어들 수 있습니다.

   | 호스트의 CPU 코어 수 | 1    | 2    | 4    | 8    | 16 | 32|64|
   |---|---|---|---|---|---|---|---|
   |kube-reserved(밀리코어)|60|100|140|180|260|420|740|

- **메모리**  
    AKS에서 사용하는 메모리에는 다음 두 값의 합계가 포함됩니다.

   1. **`kubelet` 디먼**   
       `kubelet` 디먼은 컨테이너 만들기 및 종료를 관리하기 위해 모든 Kubernetes 에이전트 노드에 설치됩니다. 
   
        기본적으로 AKS에서 `kubelet` 디먼에는 *memory.available<750Mi* 제거 규칙이 있으므로 노드는 항상 750Mi 이상을 할당할 수 있어야 합니다. 호스트가 사용 가능한 메모리 임계값보다 낮은 경우 `kubelet`은 실행 중인 Pod 중 하나를 종료하고 호스트 컴퓨터에서 메모리를 확보하도록 트리거합니다.

   2. kubelet 디먼이 제대로 작동하기 위한 **메모리 예약의 회귀 비율**(*kube-reserved*)
      - 처음 4GB 메모리의 25%
      - 다음 4GB 메모리의 20%(최대8GB)
      - 다음 8GB 메모리의 10%(최대16GB)
      - 다음 112GB 메모리의 6%(최대128GB)
      - 128GB를 초과하는 메모리의 2%

메모리 및 CPU 할당 규칙:
* 클러스터 상태에 중요한 일부 호스팅 시스템 Pod를 포함하여 에이전트 노드를 정상 상태로 유지합니다. 
* 노드에서 할당 가능한 메모리 및 CPU를 Kubernetes 클러스터의 일부가 아닌 경우보다 더 적게 보고하도록 합니다. 

위의 리소스 예약은 변경할 수 없습니다.

예를 들어 노드에서 7GB를 제공하는 경우 750Mi 하드 제거 임계값을 포함하여 할당할 수 없는 메모리의 34%를 보고합니다.

`0.75 + (0.25*4) + (0.20*3) = 0.75GB + 1GB + 0.6GB = 2.35GB / 7GB = 33.57% reserved`

또한 기본 노드 OS는 Kubernetes 자체에 대한 예약 외에도 OS 기능을 유지하기 위해 많은 CPU 및 메모리 리소스를 예약합니다.

관련 모범 사례는 [AKS의 기본 스케줄러 기능 모범 사례][operator-best-practices-scheduler]를 참조하세요.

### <a name="node-pools"></a>노드 풀

동일한 구성의 노드는 모두 *노드 풀* 로 그룹화됩니다. Kubernetes 클러스터에는 하나 이상의 노드 풀이 포함됩니다. 노드의 초기 수와 크기는 *기본 노드 풀* 을 만드는 AKS 클러스터를 만들 때 정의됩니다. AKS의 기본 노드 풀에는 에이전트 노드를 실행하는 기본 VM이 포함됩니다.

> [!NOTE]
> 클러스터가 안정적으로 작동하도록 하려면 기본 노드 풀에서 둘 이상의 노드를 실행해야 합니다.

AKS 클러스터를 기본 노드 풀에 대해 스케일링하거나 업그레이드합니다. 특정 노드 풀을 스케일링하거나 업그레이드하도록 선택할 수 있습니다. 업그레이드 작업의 경우, 모든 노드가 성공적으로 업그레이드될 때까지 실행 중인 컨테이너는 노드 풀의 다른 노드에 예약됩니다.

AKS에서 여러 노드 풀을 사용하는 방법에 대한 자세한 내용은 [AKS에서 클러스터에 대한 여러 노드 풀 만들기 및 관리][use-multiple-node-pools]를 참조하세요.

### <a name="node-selectors"></a>노드 선택기

여러 노드 풀이 있는 AKS 클러스터에서는 지정된 리소스에 사용할 노드 풀을 Kubernetes 스케줄러에 알려야 할 수 있습니다. 예를 들어 수신 컨트롤러는 Windows Server 노드에서 실행되지 않습니다. 

노드 선택기를 사용하면 Pod를 예약해야 하는 위치를 제어하는 노드 OS와 같은 다양한 매개 변수를 정의할 수 있습니다.

다음 기본 예제에서는 *"kubernetes.io/os": linux* 노드 선택기를 사용하여 Linux 노드에서 NGINX 인스턴스를 예약합니다.

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: nginx
spec:
  containers:
    - name: myfrontend
      image: mcr.microsoft.com/oss/nginx/nginx:1.15.12-alpine
  nodeSelector:
    "kubernetes.io/os": linux
```

Pod가 예약되는 위치를 제어하는 방법에 대한 자세한 내용은 [AKS의 고급 스케줄러 기능 모범 사례][operator-best-practices-advanced-scheduler]를 참조하세요.

## <a name="pods"></a>Pod

Kubernetes는 *Pod* 를 사용하여 애플리케이션의 인스턴스를 실행합니다. Pod는 애플리케이션의 단일 인스턴스를 나타냅니다. 

Pod에는 일반적으로 컨테이너와의 1:1 매핑이 포함됩니다. 고급 시나리오에서 Pod는 여러 컨테이너를 포함할 수 있습니다. 다중 컨테이너 Pod는 모두 동일한 노드에서 예약되며, 컨테이너에서 관련 리소스를 공유할 수 있습니다.

Pod를 만드는 경우 특정 양의 CPU 또는 메모리 리소스를 요청하는 *리소스 요청* 을 정의할 수 있습니다. Kubernetes 스케줄러는 사용 가능한 리소스가 있는 노드에서 실행되도록 Pod를 예약하여 요청을 충족하려고 시도합니다. 또한 Pod는 기본 노드에서 컴퓨팅 리소스를 너무 많이 소비하지 못하도록 최대 리소스 제한을 지정할 수 있습니다. Kubernetes 스케줄러에서 허용되는 필수 리소스를 식별하는 데 도움이 되도록 모든 Pod에 대한 리소스 제한을 포함하는 것이 좋습니다.

자세한 내용은 [Kubernetes Pod][kubernetes-pods] 및 [Kubernetes Pod 수명 주기][kubernetes-pod-lifecycle]를 참조하세요.

Pod는 논리적 리소스이지만, 애플리케이션 워크로드는 컨테이너에서 실행됩니다. Pod는 일반적으로 일시적인 일회용 리소스입니다. 개별적으로 예약된 Pod에는 일부 고가용성 및 중복성 Kubernetes 기능 중 일부가 포함되지 않습니다. 대신, 배포 컨트롤러와 같은 Kubernetes *컨트롤러* 에서 Pod를 배포하고 관리합니다.

## <a name="deployments-and-yaml-manifests"></a>배포 및 YAML 매니페스트

*배포* 는 Kubernetes 배포 컨트롤에서 관리하는 것과 동일한 Pod를 나타냅니다. 배포는 만들 Pod *복제본* 의 수를 정의합니다. Pod 또는 노드에 문제가 발생하는 경우 Kubernetes 스케줄러는 추가 Pod가 정상 노드에서 예약되도록 합니다.

Pod, 사용된 컨테이너 이미지 또는 연결된 스토리지의 구성을 변경하도록 배포를 업데이트할 수 있습니다. 배포 컨트롤러에서 수행하는 작업은 다음과 같습니다.
* 지정된 수의 복제본을 드레이닝하고 종료합니다.
* 새 배포 정의에서 복제본을 만듭니다.
* 배포의 모든 복제본이 업데이트될 때까지 프로세스를 계속합니다.

AKS의 상태 비저장 애플리케이션 대부분은 개별 Pod를 예약하는 것이 아니라 배포 모델을 사용해야 합니다. Kubernetes는 배포 상태를 모니터링하여 필요한 수의 복제본이 클러스터 내에서 실행되는지 확인할 수 있습니다. 개별적으로 예약할 때 문제가 발생하면 Pod가 다시 시작되지 않고, 현재 노드에 문제가 발생하면 정상 노드에서 다시 예약되지 않습니다.

애플리케이션에 최소한의 사용 가능한 인스턴스 수가 필요한 경우 업데이트 프로세스를 사용하여 관리 결정을 중단하지 않으려고 합니다. *Pod 중단 예산* 은 업데이트 또는 노드 업그레이드 중에 분해할 수 있는 배포의 복제본 수를 정의합니다. 예를 들어 배포에 *5* 개의 복제본이 있는 경우 한 번에 하나의 복제본만 삭제하거나 다시 예약하도록 Pod 중단을 *4* 로 정의할 수 있습니다. Pod 리소스 제한과 마찬가지로 Pod 중단 예산을 항상 최소 복제본 수가 필요한 애플리케이션에 정의하는 것이 가장 좋습니다.

배포는 일반적으로 `kubectl create` 또는 `kubectl apply`를 사용하여 만들어지고 관리됩니다. YAML 형식의 매니페스트 파일을 정의하여 배포를 만듭니다. 

다음 예제에서는 NGINX 웹 서버의 기본 배포를 만듭니다. 배포에서 만들 *3* 개의 복제본을 지정하고, 컨테이너에서 *80* 포트를 열어야 합니다. CPU 및 메모리에 대한 리소스 요청 및 제한도 정의됩니다.

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: mcr.microsoft.com/oss/nginx/nginx:1.15.2-alpine
        ports:
        - containerPort: 80
        resources:
          requests:
            cpu: 250m
            memory: 64Mi
          limits:
            cpu: 500m
            memory: 256Mi
```

서비스(예: 부하 분산 장치)를 YAML 매니페스트 내에 포함하여 더 복잡한 애플리케이션을 만들 수 있습니다.

자세한 내용은 [Kubernetes 배포][kubernetes-deployments]를 참조하세요.

### <a name="package-management-with-helm"></a>Helm을 사용한 패키지 관리

[Helm][helm]은 일반적으로 Kubernetes에서 애플리케이션을 관리하는 데 사용됩니다. 패키지 버전의 애플리케이션 코드와 Kubernetes YAML 매니페스트가 포함된 기존 퍼블릭 Helm *차트* 를 빌드하고 사용하여 리소스를 배포할 수 있습니다. Helm 차트는 로컬로 또는 원격 리포지토리(예: [Azure Container Registry Helm 차트 리포지토리][acr-helm])에 저장할 수 있습니다.

Helm을 사용하려면 Helm 클라이언트를 컴퓨터에 설치하거나 [Azure Cloud Shell][azure-cloud-shell]에서 Helm 클라이언트를 사용합니다. Helm 차트를 검색하거나 만든 다음, Kubernetes 클러스터에 설치합니다. 자세한 내용은 [AKS에서 Helm을 사용하여 기존 애플리케이션 설치][aks-helm]를 참조하세요.

## <a name="statefulsets-and-daemonsets"></a>StatefulSets 및 DaemonSets

Kubernetes 스케줄러를 사용하면 배포 컨트롤러에서 사용 가능한 리소스가 있는 사용 가능한 노드에서 복제본을 실행합니다. 이 방법은 상태 비저장 애플리케이션에 충분할 수 있지만 배포 컨트롤러는 다음이 필요한 애플리케이션에 적합하지 않습니다.
* 영구적 명명 규칙 또는 스토리지 
* 클러스터 내의 각 선택 노드에 있는 복제본

그러나 두 개의 Kubernetes 리소스를 사용하면 다음과 같은 유형의 애플리케이션을 관리할 수 ​​있습니다.

- *StatefulSet* 는 애플리케이션 상태를 스토리지와 같은 개별 Pod 수명 주기 이상으로 유지합니다.
- *DaemonSet* 는 Kubernetes 부트스트랩 프로세스의 초기에 각 노드에서 실행 중인 인스턴스를 보장합니다.

### <a name="statefulsets"></a>StatefulSets

최신 애플리케이션 개발은 상태 비저장 애플리케이션을 목표로 하는 경우가 많습니다. 데이터베이스 구성 요소를 포함하는 것과 같은 상태 저장 애플리케이션의 경우 *StatefulSet* 를 사용할 수 있습니다. 배포와 마찬가지로 StatefulSet도 하나 이상의 동일한 Pod를 만들고 관리합니다. StatefulSet의 복제본은 배포, 스케일링, 업그레이드 및 종료에 대한 정상적이고 순차적인 방법을 따릅니다. 명명 규칙, 네트워크 이름 및 스토리지는 복제본이 StatefulSet를 사용하여 다시 예약될 때 유지됩니다.

`kind: StatefulSet`를 사용하여 애플리케이션을 YAML 형식으로 정의합니다. 여기서 StatefulSet 컨트롤러는 필요한 복제본의 배포 및 관리를 처리합니다. 데이터는 Azure Managed Disks 또는 Azure Files에서 제공되는 영구적 스토리지에 기록됩니다. StatefulSet를 사용하면 StatefulSet가 삭제된 경우에도 기본 영구 스토리지가 유지됩니다.

자세한 내용은 [Kubernetes StatefulSets][kubernetes-statefulsets]를 참조하세요.

StatefulSet의 복제본은 AKS 클러스터의 사용 가능한 모든 노드에서 예약되고 실행됩니다. 집합에 있는 하나 이상의 Pod가 노드에서 실행되도록 하려면 DaemonSet를 대신 사용합니다.

### <a name="daemonsets"></a>DaemonSets

특정 로그 수집 또는 모니터링의 경우 모든 노드 또는 선택한 노드에서 Pod를 실행해야 할 수 있습니다. 하나 이상의 동일한 Pod에서 *DaemonSet* 배포를 사용할 수 있지만, DaemonSet 컨트롤러는 지정된 각 노드에서 Pod의 인스턴스를 실행하도록 합니다.

기본 Kubernetes 스케줄러가 시작되기 전에 DaemonSet 컨트롤러에서 클러스터 부팅 프로세스의 초기에 Pod를 노드에 예약할 수 있습니다. 이 기능은 Deployment 또는 StatefulSet의 기존 Pod가 예약되기 전에 DaemonSet의 Pod가 시작되도록 합니다.

StatefulSet과 마찬가지로 DaemonSet은 `kind: DaemonSet`을 사용하여 YAML 정의의 일부로 정의됩니다.

자세한 내용은 [Kubernetes DaemonSets][kubernetes-daemonset]를 참조하세요.

> [!NOTE]
> [가상 노드 추가 기능](virtual-nodes-cli.md#enable-virtual-nodes-addon)을 사용하는 경우 DaemonSet는 Pod를 가상 노드에 만들지 않습니다.

## <a name="namespaces"></a>네임스페이스

Pod 및 배포와 같은 Kubernetes 리소스는 AKS 클러스터를 분할하고 리소스에 대한 액세스의 만들기, 보기 및 관리를 제한하기 위해 논리적으로 *네임스페이스* 로 그룹화됩니다. 예를 들어 비즈니스 그룹을 구분하는 네임스페이스를 만들 수 있습니다. 사용자는 할당된 네임스페이스 내의 리소스와만 상호 작용할 수 있습니다.

![리소스와 애플리케이션이 논리적으로 분할된 Kubernetes 네임스페이스](media/concepts-clusters-workloads/namespaces.png)

AKS 클러스터를 만들 때 사용할 수 있는 네임스페이스는 다음과 같습니다.

| 네임스페이스 | Description |  
| ----------------- | ------------- |  
| *default*                                                                                 | Pod 및 배포가 제공되지 않는 경우 기본적으로 만들어지는 위치입니다. 소규모 환경에서는 논리적 구분을 추가로 만들지 않고 애플리케이션을 기본 네임스페이스로 직접 배포할 수 있습니다. `kubectl get pods`와 같이 Kubernetes API와 상호 작용할 때 아무 것도 지정되지 않으면 기본 네임스페이스가 사용됩니다.                                                        |  
| *kube-system* | 네트워크 기능(예: DNS 및 프록시) 또는 핵심 리소스(예: Kubernetes 대시보드)가 있는 위치입니다. 일반적으로 사용자 고유의 애플리케이션은 이 네임스페이스에 배포하지 않습니다.                                      |  
| *kube-public*                                                                            | 일반적으로 사용되지는 않지만 클러스터 전체에서 리소스를 표시하는 데 사용할 수 있으며, 모든 사용자가 볼 수 있습니다.                                                                                    |  


자세한 내용은 [Kubernetes 네임스페이스][kubernetes-namespaces]를 참조하세요.

## <a name="next-steps"></a>다음 단계

이 문서에서는 Kubernetes 핵심 구성 요소 중 일부와 AKS 클러스터에 이를 적용하는 방법에 대해 설명하고 있습니다. Kubernetes 및 AKS 핵심 개념에 대한 자세한 내용은 다음 문서를 참조하세요.

- [Kubernetes/AKS 액세스 및 ID][aks-concepts-identity]
- [Kubernetes/AKS 보안][aks-concepts-security]
- [Kubernetes/AKS 가상 네트워크][aks-concepts-network]
- [Kubernetes/AKS 스토리지][aks-concepts-storage]
- [Kubernetes/AKS 크기 조정][aks-concepts-scale]

<!-- EXTERNAL LINKS -->
[aks-engine]: https://github.com/Azure/aks-engine
[kubernetes-pods]: https://kubernetes.io/docs/concepts/workloads/pods/pod-overview/
[kubernetes-pod-lifecycle]: https://kubernetes.io/docs/concepts/workloads/pods/pod-lifecycle/
[kubernetes-deployments]: https://kubernetes.io/docs/concepts/workloads/controllers/deployment/
[kubernetes-statefulsets]: https://kubernetes.io/docs/concepts/workloads/controllers/statefulset/
[kubernetes-daemonset]: https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/
[kubernetes-namespaces]: https://kubernetes.io/docs/concepts/overview/working-with-objects/namespaces/
[helm]: https://helm.sh/
[azure-cloud-shell]: https://shell.azure.com

<!-- INTERNAL LINKS -->
[aks-concepts-identity]: concepts-identity.md
[aks-concepts-security]: concepts-security.md
[aks-concepts-scale]: concepts-scale.md
[aks-concepts-storage]: concepts-storage.md
[aks-concepts-network]: concepts-network.md
[acr-helm]: ../container-registry/container-registry-helm-repos.md
[aks-helm]: kubernetes-helm.md
[operator-best-practices-cluster-security]: operator-best-practices-cluster-security.md
[operator-best-practices-scheduler]: operator-best-practices-scheduler.md
[use-multiple-node-pools]: use-multiple-node-pools.md
[operator-best-practices-advanced-scheduler]: operator-best-practices-advanced-scheduler.md
[reservation-discounts]:../cost-management-billing/reservations/save-compute-costs-reservations.md
