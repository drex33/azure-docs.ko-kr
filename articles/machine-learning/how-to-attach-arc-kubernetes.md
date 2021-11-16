---
title: Azure Arc 지원 기계 학습(미리 보기)
description: Azure Machine Learning 기계 학습 모델을 학습하고 유추하도록 Azure Kubernetes Service 및 Azure Arc 지원 Kubernetes 클러스터를 구성합니다.
titleSuffix: Azure Machine Learning
author: luisquintanilla
ms.author: luquinta
ms.service: machine-learning
ms.subservice: core
ms.date: 10/21/2021
ms.topic: how-to
ms.custom: ignite-fall-2021
ms.openlocfilehash: 635153d510b18bc0ce97033094abf21a3b6d3d74
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/15/2021
ms.locfileid: "132491631"
---
# <a name="configure-kubernetes-clusters-for-machine-learning-preview"></a>기계 학습을 위한 Kubernetes 클러스터 구성(미리 보기)

기계 학습 워크로드를 학습하고 추론하기 위해 AKS(Azure Kubernetes Service) 및 Azure Arc 지원 Kubernetes 클러스터를 구성하는 방법을 알아봅니다.

## <a name="what-is-azure-arc-enabled-machine-learning"></a>Azure Arc 지원 기계 학습이란?

Azure Arc를 사용하면 온-프레미스, 다중 클라우드 또는 에지 등 모든 Kubernetes 환경에서 Azure 서비스를 실행할 수 있습니다.

Azure Arc 지원 기계 학습을 사용하면 Azure Kubernetes Service 또는 Azure Arc 지원 Kubernetes 클러스터를 구성하고 사용하여 Azure Machine Learning 기계 학습 모델을 학습, 유추 및 관리할 수 있습니다.

## <a name="machine-learning-on-azure-kubernetes-service"></a>Azure Kubernetes Service Machine Learning

Azure Machine Learning 학습 및 유추 워크로드에 Azure Kubernetes Service 클러스터를 사용하려면 클러스터를 Azure Arc 연결할 필요가 없습니다.

인바운드 및 아웃바운드 네트워크 트래픽을 구성해야 합니다. 자세한 내용은 [AKS(인바운드 및 아웃바운드 네트워크 트래픽) 구성을](how-to-access-azureml-behind-firewall.md#azure-kubernetes-services-1)참조하세요.

Azure Kubernetes Service 클러스터에 Azure Machine Learning 확장을 배포하려면 Azure Machine Learning 확장 배포 [섹션을](#deploy-azure-machine-learning-extension) 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

* Azure 구독 Azure를 구독하고 있지 않다면 시작하기 전에 [무료 계정을 만듭니다](https://azure.microsoft.com/free).
* Azure Arc 지원 Kubernetes 클러스터. 자세한 내용은 [Azure Arc에 기존 Kubernetes 클러스터 연결 빠른 시작 가이드](../azure-arc/kubernetes/quickstart-connect-cluster.md)를 참조하세요.

    > [!NOTE]
    > AKS 클러스터의 경우 Azure Arc 연결은 선택 **사항입니다.**

* [Azure Arc 네트워크 요구 사항](/azure/azure-arc/kubernetes/quickstart-connect-cluster?tabs=azure-cli#meet-network-requirements) 충족

    > [!IMPORTANT]
    > 아웃바운드 프록시 서버 또는 방화벽 뒤에서 실행되는 클러스터에는 추가 네트워크 구성이 필요합니다.
    >
    > 자세한 내용은 [인바운드 및 아웃바운드 네트워크 트래픽 구성(Azure Arc 지원 Kubernetes) 을 참조하세요.](how-to-access-azureml-behind-firewall.md#arc-kubernetes)

* [Azure Arc 지원 Kubernetes 클러스터 확장 필수 구성 요소는 충족합니다.](../azure-arc/kubernetes/extensions.md#prerequisites)
  * Azure CLI 버전 >= 2.24.0
  * Azure CLI k8s-extension extension version >= 1.0.0

* Azure Machine Learning 작업 영역 아직 구독하지 않는 경우 시작하기 전에 [작업 영역을 만드세요](how-to-manage-workspace.md?tabs=python).
  * Azure Machine Learning Python SDK 버전 >= 1.30
* Azure CLI 사용하여 Azure에 로그인합니다.

    ```azurecli
    az login
    az account set --subscription <your-subscription-id>
    ```  

* **ARO(Azure RedHat OpenShift Service) 및 OCP(OpenShift Container Platform)만 해당**

    * ARO 또는 OCP Kubernetes 클러스터가 실행되고 있습니다. 자세한 내용은 [ARO Kubernetes 클러스터 만들기](/azure/openshift/tutorial-create-cluster) 및 [OCP Kubernetes 클러스터 만들기를 참조하세요.](https://docs.openshift.com/container-platform/4.6/installing/installing_platform_agnostic/installing-platform-agnostic.html)
    * AzureML 서비스 계정에 대한 권한 있는 액세스 권한을 부여합니다.

        를 실행하고 `oc edit scc privileged` 다음을 추가합니다. 

        * ```system:serviceaccount:azure-arc:azure-arc-kube-aad-proxy-sa```
        * ```system:serviceaccount:azureml:{EXTENSION NAME}-kube-state-metrics```**(참고:** ```{EXTENSION NAME}``` **여기서는 단계에서 사용된 확장 이름과 일치해야 합니다.)** ```az k8s-extension create --name``` 
        * ```system:serviceaccount:azureml:cluster-status-reporter```
        * ```system:serviceaccount:azureml:prom-admission```
        * ```system:serviceaccount:azureml:default```
        * ```system:serviceaccount:azureml:prom-operator```
        * ```system:serviceaccount:azureml:csi-blob-node-sa```
        * ```system:serviceaccount:azureml:csi-blob-controller-sa```
        * ```system:serviceaccount:azureml:load-amlarc-selinux-policy-sa```
        * ```system:serviceaccount:azureml:azureml-fe```
        * ```system:serviceaccount:azureml:prom-prometheus```

## <a name="deploy-azure-machine-learning-extension"></a>Azure Machine Learning 확장 배포

Azure Arc 지원 Kubernetes에는 Azure Policy 정의, 모니터링, 기계 학습 등 다양한 에이전트를 설치할 수 있는 클러스터 확장 기능이 있습니다. Azure Machine Learning을 사용하려면 *Microsoft.AzureML.Kubernetes* 클러스터 확장을 이용해 Kubernetes 클러스터에 Azure Machine Learning 에이전트를 배포해야 합니다. Azure Machine Learning 확장이 설치되면 클러스터를 Azure Machine Learning 작업 영역에 연결하고 다음 시나리오에 사용할 수 있습니다.

* [학습](#training)
* [실시간 추론만](#inferencing)
* [학습 및 추론](#training-inferencing)

> [!TIP]
> 학습 전용 클러스터는 Azure Machine Learning Pipelines 일부로 일괄 처리 추론도 지원합니다.

Azure CLI 확장 명령을 사용하여 `k8s-extension` [`create`](/cli/azure/k8s-extension?view=azure-cli-latest&preserve-view=true) Azure Arc 지원 Kubernetes 클러스터에 Azure Machine Learning 확장을 배포합니다.

> [!IMPORTANT]
> 매개 `--cluster-type` 변수를 로 `managedClusters` 설정하여 AKS 클러스터에 Azure Machine Learning 확장을 배포합니다.

다음은 다양한 Azure Machine Learning 확장 배포 시나리오에 사용할 수 있는 구성 설정 목록입니다.

또는 를 사용하여 ```--config``` ```--config-protected``` Azure Machine Learning 배포 구성에 대한 키-값 쌍 목록을 지정할 수 있습니다.

> [!TIP]
> 매개 `openshift` 변수를 로 `True` 설정하여 ARO 및 OCP Kubernetes 클러스터에 Azure Machine Learning 확장을 배포합니다.

| 구성 설정 키 이름  | 설명  | 교육 | 유추 | 학습 및 유추 |
|---|---|---|---|---|
| ```enableTraining``` | 기본값은 `False`입니다. `True`로 설정하여 기계 학습 모델 학습을 위한 확장 인스턴스를 만듭니다. |  **&check;** | 해당 없음 |  **&check;** |
|```logAnalyticsWS```  | 기본값은 `False`입니다. Azure Machine Learning 확장은 Azure LogAnalytics 작업 영역과 통합됩니다. LogAnalytics 작업 영역을 통해 로그 보기 및 분석 기능을 제공하려면 `True`로 설정합니다. LogAnalytics 작업 영역 비용이 적용될 수 있습니다. | 선택 사항 | 선택 사항 | 선택 사항 |
|```installNvidiaDevicePlugin```  | 기본값은 `True`입니다. Nvidia GPU 하드웨어에서 학습 및 추론하려면 Nvidia 디바이스 플러그 인이 필요합니다. Azure Machine Learning 확장은 Kubernetes 클러스터에 GPU 하드웨어가 있는지 여부에 관계없이 Azure Machine Learning 인스턴스를 만드는 동안 기본적으로 Nvidia 디바이스 플러그인을 설치합니다. `False`GPU를 사용할 계획이 없거나 Nvidia 디바이스 플러그 인이 이미 설치된 경우 로 설정합니다.  | 선택 사항 |선택 사항 | 선택 사항 |
| ```enableInference``` | 기본값은 `False`입니다.  로 `True`  설정하여 기계 학습 모델을 유추하기 위한 확장 인스턴스를 만듭니다. | 해당 없음 | **&check;** |  **&check;** |
| ```allowInsecureConnections``` | 기본값은 `False`입니다. 및 가 `True` 제공되지 않은 경우 유추를 위해 HTTP 엔드포인트를 지원하는 Azure Machine Learning 확장 배포의 경우 로 ```sslCertPemFile``` ```sslKeyPemFile``` 설정합니다. | 해당 없음 | 선택 사항 |  선택 사항 |
| ```sslCertPemFile``` & ```ssKeyPMFile``` | SSL 인증서 및 키 파일(PEM 인코딩)의 경로입니다. 유추를 위해 HTTPS 엔드포인트를 지원하는 AzureML 확장 배포에 필요합니다. | 해당 없음 | 선택 사항 |  선택 사항 |
| ```privateEndpointNodeport``` | 기본값은 `False`입니다.  `True`NodePort를 사용하여 기계 학습 유추 프라이빗 엔드포인트 지원을 사용하는 Azure Machine Learning 확장 배포의 경우 로 설정합니다. | 해당 없음 | 선택 사항 |  선택 사항 |
| ```privateEndpointILB``` | 기본값은 `False`입니다.  `True`serviceType 내부 부하 분산 Azure Machine Learning 사용하여 Machine Learning 유추 프라이빗 엔드포인트 지원을 사용하는 확장 배포의 경우 로 설정합니다. | 해당 없음| 선택 사항 |  선택 사항 |
| ```inferenceLoadBalancerHA``` | 기본값은 `True`입니다. 기본적으로 Azure Machine Learning 확장은 고가용성을 위해 여러 수신 컨트롤러 복제본을 배포합니다. 클러스터 `False` 리소스가 제한되거나 개발 및 테스트용으로만 Azure Machine Learning 확장을 배포하려는 경우 로 설정합니다. 고가용성 부하 분산 장치는 사용하지 않고 수신 컨트롤러 복제본을 하나만 배포합니다. | 해당 없음 | 선택 사항 |  선택 사항 |
|```openshift``` | 기본값은 `False`입니다. `True`ARO 또는 OCP 클러스터에서 Azure Machine Learning 확장 배포의 경우로 설정 합니다. 배포 프로세스는 자동으로 각 노드에서 정책 패키지를 컴파일하고 정책 패키지를 로드 하므로 Azure Machine Learning 서비스 작업이 제대로 작동할 수 있습니다. | 선택 사항 | 선택 사항 |  선택 사항 |

> [!WARNING]
> Nvidia 장치 플러그 인이 클러스터에 이미 설치 되어 있는 경우 다시 설치 하면 확장 설치 오류가 발생할 수 있습니다. `installNvidiaDevicePlugin` `False` 배포 오류를 방지 하려면로 설정 합니다.

### <a name="deploy-extension-for-training-workloads"></a>학습 워크 로드를 위한 확장 배포 <a id="training"></a>

다음 Azure CLI 명령을 사용 하 여 Azure Machine Learning 확장을 배포 하 고 Kubernetes 클러스터에서 학습 워크 로드를 사용 하도록 설정 합니다.

```azurecli
az k8s-extension create --name arcml-extension --extension-type Microsoft.AzureML.Kubernetes --config enableTraining=True --cluster-type connectedClusters --cluster-name <your-connected-cluster-name> --resource-group <resource-group> --scope cluster
```

### <a name="deploy-extension-for-real-time-inferencing-workloads"></a>실시간 추론 워크 로드에 대 한 확장 배포 <a id="inferencing"></a>

네트워크 설정, Kubernetes 배포 변형 및 Kubernetes 클러스터가 호스트 되는 위치 (온-프레미스 또는 클라우드)에 따라 다음 옵션 중 하나를 선택 하 여 Azure Machine Learning 확장을 배포 하 고 Kubernetes 클러스터에서 추론 작업을 사용 하도록 설정 합니다.

#### <a name="public-endpoints-support-with-public-load-balancer"></a>공용 부하 분산 장치를 통한 공용 끝점 지원

* **HTTPS**

    ```azurecli
    az k8s-extension create --name arcml-extension --extension-type Microsoft.AzureML.Kubernetes --cluster-type connectedClusters --cluster-name <your-connected-cluster-name> --config enableInference=True --config-protected sslCertPemFile=<path-to-the-SSL-cert-PEM-ile> sslKeyPemFile=<path-to-the-SSL-key-PEM-file> --resource-group <resource-group> --scope cluster
    ```

* **HTTP**

    > [!WARNING]
    > 공용 부하 분산 장치를 통한 공용 HTTP 끝점 지원은 실시간 추론 시나리오에 대 한 Azure Machine Learning 확장을 배포 하는 가장 안전한 방법이 아니므로 권장 **되지 않습니다** .

    ```azurecli
    az k8s-extension create --name arcml-extension --extension-type Microsoft.AzureML.Kubernetes --cluster-type connectedClusters --cluster-name <your-connected-cluster-name>  --configuration-settings enableInference=True allowInsecureConnections=True --resource-group <resource-group> --scope cluster
    ```

#### <a name="private-endpoints-support-with-internal-load-balancer"></a>내부 부하 분산 장치를 사용한 개인 끝점 지원

* **HTTPS**

    ```azurecli
    az k8s-extension create --name amlarc-compute --extension-type Microsoft.AzureML.Kubernetes --cluster-type connectedClusters --cluster-name <your-connected-cluster-name> --config enableInference=True privateEndpointILB=True --config-protected sslCertPemFile=<path-to-the-SSL-cert-PEM-ile> sslKeyPemFile=<path-to-the-SSL-key-PEM-file> --resource-group <resource-group> --scope cluster
    ```

* **HTTP**

   ```azurecli
   az k8s-extension create --name arcml-extension --extension-type Microsoft.AzureML.Kubernetes --cluster-type connectedClusters --cluster-name <your-connected-cluster-name> --config enableInference=True privateEndpointILB=True allowInsecureConnections=True --resource-group <resource-group> --scope cluster
   ```

#### <a name="endpoints-support-with-nodeport"></a>NodePort를 사용 하 여 끝점 지원

NodePort를 사용 하면 사용자 고유의 부하 분산 솔루션을 설정 하거나, Kubernetes에서 완벽 하 게 지원 되지 않는 환경을 구성 하거나, 하나 이상의 노드 Ip를 직접 노출 시킬 수 있습니다.

NodePort 서비스를 사용 하 여 배포 하는 경우 점수 매기기 url (또는 swagger url)이 노드 IP (예:) 중 하나로 바뀌고 ```http://<NodeIP><NodePort>/<scoring_path>``` 노드를 사용할 수 없는 경우에도 변경 되지 않은 상태로 유지 됩니다. 그러나 다른 노드 IP로 바꿀 수 있습니다.

* **HTTPS**

    ```azurecli
    az k8s-extension create --name arcml-extension --extension-type Microsoft.AzureML.Kubernetes --cluster-type connectedClusters --cluster-name <your-connected-cluster-name> --resource-group <resource-group> --scope cluster --config enableInference=True privateEndpointNodeport=True --config-protected sslCertPemFile=<path-to-the-SSL-cert-PEM-ile> sslKeyPemFile=<path-to-the-SSL-key-PEM-file>
    ```

* **HTTP**

   ```azurecli
   az k8s-extension create --name arcml-extension --extension-type Microsoft.AzureML.Kubernetes --cluster-type connectedClusters --cluster-name <your-connected-cluster-name> --config enableInference=True privateEndpointNodeport=True allowInsecureConnections=Ture --resource-group <resource-group> --scope cluster
   ```

### <a name="deploy-extension-for-training-and-inferencing-workloads"></a>학습 및 추론 워크 로드에 대 한 확장 배포 <a id="training-inferencing"></a>

다음 Azure CLI 명령을 사용 하 여 Azure Machine Learning 확장을 배포 하 고 Kubernetes 클러스터에서 클러스터 실시간 추론, 일괄 처리 추론 및 교육 워크 로드를 사용 하도록 설정 합니다.

```azurecli
az k8s-extension create --name arcml-extension --extension-type Microsoft.AzureML.Kubernetes --cluster-type connectedClusters --cluster-name <your-connected-cluster-name> --config enableTraining=True enableInference=True --config-protected sslCertPemFile=<path-to-the-SSL-cert-PEM-ile> sslKeyPemFile=<path-to-the-SSL-key-PEM-file>--resource-group <resource-group> --scope cluster
```

## <a name="resources-created-during-deployment"></a>배포 하는 동안 생성 되는 리소스

Azure Machine Learning 확장이 배포 되 면 클러스터에서 실행 하는 작업에 따라 Azure에서 Kubernetes 클러스터에 대해 다음과 같은 리소스가 생성 됩니다.

|리소스 이름  |리소스 유형 |교육 |유추 |학습 및 유추|
|---|---|---|---|---|
|Azure ServiceBus|Azure 리소스|**&check;**|**&check;**|**&check;**|
|Azure Relay|Azure 리소스|**&check;**|**&check;**|**&check;**|
|{확장 이름}|Azure 리소스|**&check;**|**&check;**|**&check;**|
|aml-연산자|Kubernetes 배포|**&check;**|해당 없음|**&check;**|
|{EXTENSION-NAME}-kube|Kubernetes 배포|**&check;**|**&check;**|**&check;**|
|{EXTENSION-NAME}-프로메테우스-연산자|Kubernetes 배포|**&check;**|**&check;**|**&check;**|
|amlarc-controller|Kubernetes 배포|해당 없음|**&check;**|**&check;**|
|amlarc-프록시|Kubernetes 배포|해당 없음|**&check;**|**&check;**|
|azureml-fe|Kubernetes 배포|해당 없음|**&check;**|**&check;**|
|유추-연산자-컨트롤러-관리자|Kubernetes 배포|해당 없음|**&check;**|**&check;**|
|메트릭-컨트롤러-관리자|Kubernetes 배포|**&check;**|**&check;**|**&check;**|
|relayserver|Kubernetes 배포|**&check;**|**&check;**|**&check;**|
|클러스터-상태-보고자|Kubernetes 배포|**&check;**|**&check;**|**&check;**|
|nfd-마스터|Kubernetes 배포|**&check;**|해당 없음|**&check;**|
|gateway|Kubernetes 배포|**&check;**|**&check;**|**&check;**|
|csi-controller|Kubernetes 배포|**&check;**|해당 없음|**&check;**|
|csi-node|Kubernetes daemonset|**&check;**|해당 없음|**&check;**|
|흐름 비트|Kubernetes daemonset|**&check;**|**&check;**|**&check;**|
|k8s-daemonset|Kubernetes daemonset|**&check;**|**&check;**|**&check;**|
|nfd-worker|Kubernetes daemonset|**&check;**|해당 없음|**&check;**|
|프로메테우스-prom-프로메테우스|Kubernetes statefulset|**&check;**|**&check;**|**&check;**|
|frameworkcontroller|Kubernetes statefulset|**&check;**|해당 없음|**&check;**|

> [!IMPORTANT]
> Azure ServiceBus 및 Azure Relay 리소스는 Arc 클러스터 리소스와 동일한 리소스 그룹에 있습니다. 이러한 리소스를 사용 하 여 Kubernetes 클러스터와 통신 하 고 수정 하면 연결 된 계산 대상이 중단 됩니다.

> [!NOTE]
> **{Extension-name}** 은 (는) Azure CLI 명령으로 지정 된 확장 이름입니다 ```az k8s-extension create --name``` .

## <a name="verify-your-azureml-extension-deployment"></a>AzureML 확장 배포 확인

```azurecli
az k8s-extension show --name arcml-extension --cluster-type connectedClusters --cluster-name <your-connected-cluster-name> --resource-group <resource-group>
```

응답에서 `"extensionType": "arcml-extension"` 및 `"installState": "Installed"`를 찾습니다. 처음 몇 분 동안 `"installState": "Pending"`을 표시할 수 있습니다.

`installState`에서 **설치됨** 을 표시하면 클러스터를 가리키는 kubeconfig 파일이 있는 머신에서 다음 명령을 실행하여 *azureml* 네임스페이스에 있는 모든 Pod가 *실행 중* 상태인지 확인합니다.

```bash
kubectl get pods -n azureml
```

## <a name="attach-arc-cluster"></a>Arc 클러스터 연결

### <a name="studio"></a>[스튜디오](#tab/studio)

Azure Arc 사용 Kubernetes 클러스터를 연결 하면 작업 영역에서 학습에 사용할 수 있습니다.

1. [Azure Machine Learning 스튜디오](https://ml.azure.com)로 이동합니다.
1. **관리** 에서 **컴퓨팅** 을 선택합니다.
1. **연결된 컴퓨팅** 탭을 선택합니다.
1. **+새로 만들기 > Kubernetes(미리 보기)** 를 선택합니다.

   ![Kubernetes 클러스터 연결](./media/how-to-attach-arc-kubernetes/attach-kubernetes-cluster.png)

1. 컴퓨팅 이름을 입력하고 드롭다운에서 Azure Arc 지원 Kubernetes 클러스터를 선택합니다.

   **(선택 사항)** 시스템 할당 또는 사용자 할당 관리 ID를 할당합니다. 관리 ID를 통해 개발자는 자격 증명을 관리할 필요가 없습니다. 자세한 내용은 [관리 ID 개요를](/azure/active-directory/managed-identities-azure-resources/overview) 참조하세요.

   ![Kubernetes 클러스터 구성](./media/how-to-attach-arc-kubernetes/configure-kubernetes-cluster-2.png)

1. **연결** 을 선택합니다.

    연결된 컴퓨팅 탭에서 클러스터의 초기 상태는 *만들기* 입니다. 클러스터가 연결되면 상태가 *성공* 으로 변경됩니다. 그렇지 않으면 상태가 *실패* 로 변경됩니다.

    ![리소스 프로비전](./media/how-to-attach-arc-kubernetes/provision-resources.png)

### <a name="python-sdk"></a>[Python SDK](#tab/sdk)

Azure Machine Learning Python SDK를 사용하여 Azure Arc 지원 Kubernetes 클러스터를 메서드를 사용하는 컴퓨팅 대상으로 연결할 수 [`attach_configuration`](/python/api/azureml-core/azureml.core.compute.kubernetescompute.kubernetescompute?view=azure-ml-py&preserve-view=true) 있습니다.

다음 Python 코드는 Azure Arc 지원 Kubernetes 클러스터를 연결하고 관리 ID를 사용하도록 설정된 컴퓨팅 대상으로 사용하는 방법을 보여 줍니다.

관리 ID를 통해 개발자는 자격 증명을 관리할 필요가 없습니다. 자세한 내용은 [관리 ID 개요를](/azure/active-directory/managed-identities-azure-resources/overview) 참조하세요.

```python
from azureml.core.compute import KubernetesCompute
from azureml.core.compute import ComputeTarget
from azureml.core.workspace import Workspace
import os

ws = Workspace.from_config()

# Specify a name for your Kubernetes compute
amlarc_compute_name = "<COMPUTE_CLUSTER_NAME>"

# resource ID for the Kubernetes cluster and user-managed identity
resource_id = "/subscriptions/<sub ID>/resourceGroups/<RG>/providers/Microsoft.Kubernetes/connectedClusters/<cluster name>"

user_assigned_identity_resouce_id = ['subscriptions/<sub ID>/resourceGroups/<RG>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<identity name>']

ns = "default" 

if amlarc_compute_name in ws.compute_targets:
    amlarc_compute = ws.compute_targets[amlarc_compute_name]
    if amlarc_compute and type(amlarc_compute) is KubernetesCompute:
        print("found compute target: " + amlarc_compute_name)
else:
   print("creating new compute target...")


# assign user-assigned managed identity
amlarc_attach_configuration = KubernetesCompute.attach_configuration(resource_id = resource_id, namespace = ns,  identity_type ='UserAssigned',identity_ids = user_assigned_identity_resouce_id) 

# assign system-assigned managed identity
# amlarc_attach_configuration = KubernetesCompute.attach_configuration(resource_id = resource_id, namespace = ns,  identity_type ='SystemAssigned') 

amlarc_compute = ComputeTarget.attach(ws, amlarc_compute_name, amlarc_attach_configuration)
amlarc_compute.wait_for_completion(show_output=True)

# get detailed compute description containing managed identity principle ID, used for permission access. 
print(amlarc_compute.get_status().serialize())
```

매개 `identity_type` 변수를 사용하여 또는 관리 ID를 사용하도록 `SystemAssigned` `UserAssigned` 설정합니다.

### <a name="cli"></a>[CLI](#tab/cli)

Azure Machine Learning 2.0 CLI(미리 보기)를 사용하여 AKS 또는 Azure Arc 지원 Kubernetes 클러스터를 연결할 수 있습니다.

Azure Machine Learning CLI 명령을 사용하고 [`attach`](/cli/azure/ml/compute?view=azure-cli-latest&preserve-view=true) `--type` 인수를 로 `kubernetes` 설정하여 Azure Machine Learning 2.0 CLI를 사용하여 Kubernetes 클러스터를 연결합니다.

> [!NOTE]
> AKS 또는 Azure Arc 사용하도록 설정된 Kubernetes 클러스터에 대한 컴퓨팅 연결 지원을 사용하려면 Azure CLI `ml` 확장 버전 >= 2.0.1a4가 필요합니다. 자세한 내용은 [CLI 설치 및 설정(v2)을 참조하세요.](how-to-configure-cli.md)

다음 명령은 Azure Arc 지원 Kubernetes 클러스터를 연결하고 관리 ID를 사용하도록 설정된 컴퓨팅 대상으로 사용하는 방법을 보여 줍니다.

**AKS**

```azurecli
az ml compute attach --resource-group <resource-group-name> --workspace-name <workspace-name> --name amlarc-compute --resource-id "/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Kubernetes/managedclusters/<cluster-name>" --type kubernetes --identity-type UserAssigned --user-assigned-identities "subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<identity-name>" --no-wait
```

**Azure Arc 지원 Kubernetes**

```azurecli
az ml compute attach --resource-group <resource-group-name> --workspace-name <workspace-name> --name amlarc-compute --resource-id "/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Kubernetes/connectedClusters/<cluster-name>" --type kubernetes --user-assigned-identities "subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<identity-name>" --no-wait
```

인수를 사용하여 `identity_type` 또는 관리 ID를 사용하도록 `SystemAssigned` `UserAssigned` 설정합니다.

> [!IMPORTANT]
> `--user-assigned-identities` 는 관리 `UserAssigned` ID에만 필요합니다. 쉼표로 구분된 사용자 관리 ID 목록을 제공할 수 있지만 클러스터를 연결할 때 첫 번째 ID만 사용됩니다.

---

## <a name="next-steps"></a>다음 단계

- [학습 및 추론 워크로드에 대한 다른 인스턴스 유형 만들기 및 선택](how-to-kubernetes-instance-type.md)
- [CLI(v2)를 사용하여 모델 학습](how-to-train-cli.md)
- [학습 실행 구성 및 제출](how-to-set-up-training-targets.md)
- [하이퍼 매개 변수 조정](how-to-tune-hyperparameters.md)
- [Scikit-learn을 사용하여 모델 학습](how-to-train-scikit-learn.md)
- [TensorFlow 모델 학습](how-to-train-tensorflow.md)
- [PyTorch 모델 학습](how-to-train-pytorch.md)
- [Azure Machine Learning 파이프라인을 사용하여 학습](how-to-create-machine-learning-pipelines.md)
- [아웃바운드 프록시 서버를 사용하여 온-프레미스 모델 학습](../azure-arc/kubernetes/quickstart-connect-cluster.md#4a-connect-using-an-outbound-proxy-server)
