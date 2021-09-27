---
title: Windows PowerShell 인터페이스를 통해 Microsoft Azure Stack Edge Pro GPU 디바이스에 연결 및 관리 | Microsoft Docs
description: Windows PowerShell 인터페이스를 통해 Azure Stack Edge Pro GPU에 연결하고 관리하는 방법을 설명합니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 08/10/2021
ms.author: alkohli
ms.openlocfilehash: a2a6b7b514cb95eb327235c59bb569ac66f4a5d0
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128670503"
---
# <a name="manage-an-azure-stack-edge-pro-gpu-device-via-windows-powershell"></a>Windows PowerShell을 통해 Azure Stack Edge Pro GPU 디바이스 관리

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Azure Stack Edge Pro GPU 솔루션을 사용하면 데이터를 처리하고 네트워크를 통해 Azure에 보낼 수 있습니다. 이 문서에서는 Azure Stack Edge Pro GPU 디바이스에 대한 일부 구성 및 관리 작업에 대해 설명합니다. Azure Portal, 로컬 웹 UI 또는 Windows PowerShell 인터페이스를 사용하여 디바이스를 관리할 수 있습니다.

이 문서에서는 디바이스의 PowerShell 인터페이스에 연결하는 방법과 이 인터페이스를 사용하여 수행할 수 있는 작업에 대해 중점적으로 설명합니다. 


## <a name="connect-to-the-powershell-interface"></a>PowerShell 인터페이스에 연결합니다.

[!INCLUDE [Connect to admin runspace](../../includes/azure-stack-edge-gateway-connect-minishell.md)]

## <a name="create-a-support-package"></a>지원 패키지 만들기

[!INCLUDE [Create a support package](../../includes/data-box-edge-gateway-create-support-package.md)]


## <a name="view-device-information"></a>디바이스 정보 보기
 
[!INCLUDE [View device information](../../includes/data-box-edge-gateway-view-device-info.md)]

## <a name="view-gpu-driver-information"></a>GPU 드라이버 정보 보기

디바이스에서 컴퓨팅 역할이 구성된 경우 PowerShell 인터페이스를 통해 GPU 드라이버 정보를 가져올 수도 있습니다. 

1. [PowerShell 인터페이스에 연결하세요](#connect-to-the-powershell-interface).
2. `Get-HcsGpuNvidiaSmi`를 사용하여 디바이스에 대한 GPU 드라이버 정보를 가져옵니다.

    다음 예제에서는 이 cmdlet의 사용법을 보여줍니다.

    ```powershell
    Get-HcsGpuNvidiaSmi
    ```
    이 cmdlet의 샘플 출력에서 드라이버 정보를 적어 둡니다.

    ```powershell    
    +-----------------------------------------------------------------------------+    
    | NVIDIA-SMI 440.64.00    Driver Version: 440.64.00    CUDA Version: 10.2     |    
    |-------------------------------+----------------------+----------------------+    
    | GPU  Name        Persistence-M| Bus-Id        Disp.A | Volatile Uncorr. ECC |    
    | Fan  Temp  Perf  Pwr:Usage/Cap|         Memory-Usage | GPU-Util  Compute M. |    
    |===============================+======================+======================|    
    |   0  Tesla T4            On   | 000029CE:00:00.0 Off |                    0 |    
    | N/A   60C    P0    29W /  70W |   1539MiB / 15109MiB |      0%      Default |    
    +-------------------------------+----------------------+----------------------+    
    |   1  Tesla T4           On  | 0000AD50:00:00.0 Off |                    0 |
    | N/A   58C    P0    29W /  70W |    330MiB / 15109MiB |      0%      Default |
    +-------------------------------+----------------------+----------------------+
    ```

## <a name="enable-multi-process-service-mps"></a>MPS(다중 프로세스 서비스) 사용

Nvidia GPU의 MPS(다중 프로세스 서비스)는 여러 작업에서 GPU를 공유할 수 있는 메커니즘을 제공합니다. 여기서는 각 작업에 GPU 리소스의 일부 백분율이 할당됩니다. MPS는 Azure Stack Edge Pro GPU 디바이스의 미리 보기 기능입니다. 디바이스에서 MPS를 사용하도록 설정하려면 다음 단계를 수행합니다.

[!INCLUDE [Enable MPS](../../includes/azure-stack-edge-gateway-enable-mps.md)]

> [!NOTE]
> 디바이스 소프트웨어와 Kubernetes 클러스터를 업데이트하면 워크로드의 MPS 설정이 유지되지 않습니다. MPS를 다시 사용하도록 설정해야 합니다.

## <a name="reset-your-device"></a>디바이스 재설정

[!INCLUDE [Reset your device](../../includes/data-box-edge-gateway-deactivate-device.md)]

## <a name="get-compute-logs"></a>컴퓨팅 로그 가져오기

디바이스에서 컴퓨팅 역할이 구성된 경우 PowerShell 인터페이스를 통해 컴퓨팅 로그를 가져올 수도 있습니다.

1. [PowerShell 인터페이스에 연결하세요](#connect-to-the-powershell-interface).
2. `Get-AzureDataBoxEdgeComputeRoleLogs`를 사용하여 디바이스에 대한 컴퓨팅 로그를 가져옵니다.

    다음 예제에서는 이 cmdlet의 사용법을 보여줍니다.

    ```powershell
    Get-AzureDataBoxEdgeComputeRoleLogs -Path "\\hcsfs\logs\myacct" -Credential "username" -FullLogCollection    
    ```

    cmdlet에 사용되는 매개 변수에 대한 설명은 다음과 같습니다.
    - `Path`: 컴퓨팅 로그 패키지를 만들려는 공유에 대한 네트워크 경로를 제공합니다.
    - `Credential`: 네트워크 공유에 대한 사용자 이름을 제공합니다. 이 cmdlet을 실행하는 경우 공유 암호를 제공해야 합니다.
    - `FullLogCollection`: 이 매개 변수는 모든 컴퓨팅 로그가 로그 패키지에 포함되도록 합니다. 로그 패키지에는 기본적으로 로그의 하위 집합만 포함되어 있습니다.


## <a name="change-kubernetes-pod-and-service-subnets"></a>Kubernetes Pod 및 서비스 서브넷 변경

기본적으로 Azure Stack Edge 디바이스의 Kubernetes는 172.27.0.0/16 및 172.28.0.0/16의 서브넷을 각각 Pod 및 서비스에 사용합니다. 네트워크에서 이러한 서브넷을 이미 사용하고 있는 경우 `Set-HcsKubeClusterNetworkInfo` cmdlet을 실행하여 해당 서브넷을 변경할 수 있습니다.

이 단계에서 Kubernetes 클러스터가 만들어지므로 Azure Portal에서 컴퓨팅을 구성하기 전에 이 구성을 수행하려고 합니다.

1. [디바이스의 PowerShell 인터페이스에 연결합니다](#connect-to-the-powershell-interface).
1. 디바이스의 PowerShell 인터페이스에서 다음을 실행합니다.

    `Set-HcsKubeClusterNetworkInfo -PodSubnet <subnet details> -ServiceSubnet <subnet details>`

    \<subnet details\>를 사용할 사용하려는 서브넷 범위로 바꾸세요. 

1. 이 명령이 실행되면 `Get-HcsKubeClusterNetworkInfo` 명령을 사용하여 Pod 및 서비스 서브넷이 변경되었는지 확인할 수 있습니다.

이 명령의 샘플 출력은 다음과 같습니다.

```powershell
[10.100.10.10]: PS>Set-HcsKubeClusterNetworkInfo -PodSubnet 10.96.0.1/16 -ServiceSubnet 10.97.0.1/16
[10.100.10.10]: PS>Get-HcsKubeClusterNetworkInfo

Id                                   PodSubnet    ServiceSubnet
--                                   ---------    -------------
6dbf23c3-f146-4d57-bdfc-76cad714cfd1 10.96.0.1/16 10.97.0.1/16
[10.100.10.10]: PS>
```

## <a name="debug-kubernetes-issues-related-to-iot-edge"></a>IoT Edge와 관련된 Kubernetes 문제 디버깅

시작하기 전에 다음이 필요합니다.

- 구성된 컴퓨팅 네트워크. [자습서: GPU가 있는 Azure Stack Edge Pro에 대한 네트워크 구성](azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy.md)을 참조하세요.
- 디바이스에 구성된 컴퓨팅 역할
    
컴퓨팅 역할이 구성된 Azure Stack Edge Pro GPU 디바이스에서 별도의 두 가지 명령 세트를 사용하여 디바이스 문제를 해결하거나 디바이스를 모니터링할 수 있습니다.

- `iotedge` 명령을 사용. 이러한 명령은 디바이스에 대한 기본 작업에 사용할 수 있습니다.
- `kubectl` 명령 사용. 이러한 명령은 디바이스에 대한 광범위한 작업 세트에 사용할 수 있습니다.

위의 명령 세트 중 하나를 실행하려면 [PowerShell 인터페이스에 연결](#connect-to-the-powershell-interface)해야 합니다.

### <a name="use-iotedge-commands"></a>`iotedge` 명령 사용

사용 가능한 명령 목록을 보려면 [PowerShell 인터페이스에 연결](#connect-to-the-powershell-interface)하고, `iotedge` 함수를 사용합니다.

```powershell
[10.100.10.10]: PS>iotedge -?                                                                                                                           
Usage: iotedge COMMAND

Commands:
   list
   logs
   restart

[10.100.10.10]: PS>
```

다음 표에서는 `iotedge`에 사용할 수 있는 명령에 대해 간략히 설명합니다.

|명령을 사용합니다.  |Description |
|---------|---------|
|`list`     | 모듈 목록 표시         |
|`logs`     | 모듈 로그 가져오기        |
|`restart`     | 모듈 중지 및 다시 시작         |

#### <a name="list-all-iot-edge-modules"></a>모든 IoT Edge 모듈 나열

디바이스에서 실행되는 모든 모듈을 나열하려면 `iotedge list` 명령을 사용합니다.

이 명령의 샘플 출력은 다음과 같습니다. 이 명령은 모든 모듈, 연결된 구성 및 모듈과 연결된 외부 IP를 나열합니다. 예를 들어 `https://10.128.44.244`에서 **webserver** 앱에 액세스할 수 있습니다. 


```powershell
[10.100.10.10]: PS>iotedge list

NAME                   STATUS  DESCRIPTION CONFIG                                             EXTERNAL-IP
----                   ------  ----------- ------                                             -----
gettingstartedwithgpus Running Up 10 days  mcr.microsoft.com/intelligentedge/solutions:latest
iotedged               Running Up 10 days  azureiotedge/azureiotedge-iotedged:0.1.0-beta10    <none>
edgehub                Running Up 10 days  mcr.microsoft.com/azureiotedge-hub:1.0             10.128.44.243
edgeagent              Running Up 10 days  azureiotedge/azureiotedge-agent:0.1.0-beta10
webserverapp           Running Up 10 days  nginx:stable                                       10.128.44.244

[10.100.10.10]: PS>
```
#### <a name="restart-modules"></a>모듈 다시 시작

`list` 명령을 사용하여 디바이스에서 실행되는 모든 모듈을 나열할 수 있습니다. 그런 다음, 다시 시작하려는 모듈의 이름을 확인하고, `restart` 명령에 사용합니다.

모듈을 다시 시작하는 방법에 대한 샘플 출력은 다음과 같습니다. 모듈이 실행되는 기간에 대한 설명에 따라 `cuda-sample1`이 다시 시작되었음을 확인할 수 있습니다.

```powershell
[10.100.10.10]: PS>iotedge list

NAME         STATUS  DESCRIPTION CONFIG                                          EXTERNAL-IP PORT(S)
----         ------  ----------- ------                                          ----------- -------
edgehub      Running Up 5 days   mcr.microsoft.com/azureiotedge-hub:1.0          10.57.48.62 443:31457/TCP,5671:308
                                                                                             81/TCP,8883:31753/TCP
iotedged     Running Up 7 days   azureiotedge/azureiotedge-iotedged:0.1.0-beta13 <none>      35000/TCP,35001/TCP
cuda-sample2 Running Up 1 days   nvidia/samples:nbody
edgeagent    Running Up 7 days   azureiotedge/azureiotedge-agent:0.1.0-beta13
cuda-sample1 Running Up 1 days   nvidia/samples:nbody

[10.100.10.10]: PS>iotedge restart cuda-sample1
[10.100.10.10]: PS>iotedge list

NAME         STATUS  DESCRIPTION  CONFIG                                          EXTERNAL-IP PORT(S)
----         ------  -----------  ------                                          ----------- -------
edgehub      Running Up 5 days    mcr.microsoft.com/azureiotedge-hub:1.0          10.57.48.62 443:31457/TCP,5671:30
                                                                                              881/TCP,8883:31753/TC
                                                                                              P
iotedged     Running Up 7 days    azureiotedge/azureiotedge-iotedged:0.1.0-beta13 <none>      35000/TCP,35001/TCP
cuda-sample2 Running Up 1 days    nvidia/samples:nbody
edgeagent    Running Up 7 days    azureiotedge/azureiotedge-agent:0.1.0-beta13
cuda-sample1 Running Up 4 minutes nvidia/samples:nbody

[10.100.10.10]: PS>

```

#### <a name="get-module-logs"></a>모듈 로그 가져오기

`logs` 명령을 사용하여 디바이스에서 실행되는 IoT Edge 모듈에 대한 로그를 가져옵니다. 

컨테이너 이미지를 만들 때 또는 이미지를 끌어오는 동안 오류가 발생한 경우 `logs edgeagent`를 실행합니다. `edgeagent`는 다른 컨테이너를 프로비전해야 하는 IoT Edge 런타임 컨테이너입니다. `logs edgeagent`는 모든 로그를 덤프하므로 최근 오류를 확인할 수 있는 좋은 방법은 `--tail `0` 옵션을 사용하는 것입니다. 

샘플 출력은 다음과 같습니다.

```powershell
[10.100.10.10]: PS>iotedge logs cuda-sample2 --tail 10
[10.100.10.10]: PS>iotedge logs edgeagent --tail 10
<6> 2021-02-25 00:52:54.828 +00:00 [INF] - Executing command: "Report EdgeDeployment status: [Success]"
<6> 2021-02-25 00:52:54.829 +00:00 [INF] - Plan execution ended for deployment 11
<6> 2021-02-25 00:53:00.191 +00:00 [INF] - Plan execution started for deployment 11
<6> 2021-02-25 00:53:00.191 +00:00 [INF] - Executing command: "Create an EdgeDeployment with modules: [cuda-sample2, edgeAgent, edgeHub, cuda-sample1]"
<6> 2021-02-25 00:53:00.212 +00:00 [INF] - Executing command: "Report EdgeDeployment status: [Success]"
<6> 2021-02-25 00:53:00.212 +00:00 [INF] - Plan execution ended for deployment 11
<6> 2021-02-25 00:53:05.319 +00:00 [INF] - Plan execution started for deployment 11
<6> 2021-02-25 00:53:05.319 +00:00 [INF] - Executing command: "Create an EdgeDeployment with modules: [cuda-sample2, edgeAgent, edgeHub, cuda-sample1]"
<6> 2021-02-25 00:53:05.412 +00:00 [INF] - Executing command: "Report EdgeDeployment status: [Success]"
<6> 2021-02-25 00:53:05.412 +00:00 [INF] - Plan execution ended for deployment 11
[10.100.10.10]: PS>
```
> [!NOTE]
> GetModuleLogs 또는 UploadModuleLogs와 같은 직접 메서드는 Azure Stack Edge에서 Kubernetes의 IoT Edge에서 지원되지 않습니다.
 
 
### <a name="use-kubectl-commands"></a>kubectl 명령 사용

컴퓨팅 역할이 구성된 Azure Stack Edge Pro GPU 디바이스에서 모든 `kubectl` 명령을 사용하여 모듈을 모니터링하거나 문제를 해결할 수 있습니다. 사용 가능한 명령 목록을 보려면 명령 창에서 `kubectl --help`를 실행합니다.

```PowerShell
C:\Users\myuser>kubectl --help

kubectl controls the Kubernetes cluster manager.

Find more information at: https://kubernetes.io/docs/reference/kubectl/overview/

Basic Commands (Beginner):
    create         Create a resource from a file or from stdin.
    expose         Take a replication controller, service, deployment or pod and expose it as a new Kubernetes Service
    run            Run a particular image on the cluster
    set            Set specific features on objects
    run-container  Run a particular image on the cluster. This command is deprecated, use "run" instead
==============CUT=============CUT============CUT========================

Usage:
    kubectl [flags] [options]

Use "kubectl <command> --help" for more information about a given command.
Use "kubectl options" for a list of global command-line options (applies to all commands).

C:\Users\myuser>
```

`kubectl` 명령의 전체 목록을 보려면[`kubectl` 치트 시트](https://kubernetes.io/docs/reference/kubectl/cheatsheet/)로 이동합니다.


#### <a name="to-get-ip-of-service-or-module-exposed-outside-of-kubernetes-cluster"></a>Kubernetes 클러스터 외부에 공개된 서비스 또는 모듈의 IP 가져오기

Kubernetes 외부에 공개된 부하 분산 서비스의 IP를 가져오려면 다음 명령을 실행합니다.

`kubectl get svc -n iotedge`

Kubernetes 클러스터 외부에 공개된 모든 서비스 또는 모듈에 대한 샘플 출력은 다음과 같습니다. 


```powershell
[10.100.10.10]: PS>kubectl get svc -n iotedge
NAME           TYPE           CLUSTER-IP      EXTERNAL-IP     PORT(S)                                       AGE
edgehub        LoadBalancer   10.103.52.225   10.128.44.243   443:31987/TCP,5671:32336/TCP,8883:30618/TCP   34h
iotedged       ClusterIP      10.107.236.20   <none>          35000/TCP,35001/TCP                           3d8h
webserverapp   LoadBalancer   10.105.186.35   10.128.44.244   8080:30976/TCP                                16h

[10.100.10.10]: PS>
```
외부 IP 열의 IP 주소는 서비스 또는 모듈의 외부 엔드포인트에 해당합니다. [Kubernetes 대시보드에서 외부 IP를 가져올](azure-stack-edge-gpu-monitor-kubernetes-dashboard.md#get-ip-address-for-services-or-modules) 수도 있습니다.


#### <a name="to-check-if-module-deployed-successfully"></a>모듈이 성공적으로 배포되었는지 확인

컴퓨팅 모듈은 비즈니스 논리가 구현된 컨테이너입니다. Kubernetes Pod는 여러 컨테이너를 실행할 수 있습니다. 

컴퓨팅 모듈이 성공적으로 배포되었는지 확인하려면 디바이스의 PowerShell 인터페이스에 연결합니다.
`get pods` 명령을 실행하여 컴퓨팅 모듈에 해당하는 컨테이너가 실행 중인지 확인합니다.

특정 네임스페이스에서 실행 중인 모든 Pod의 목록을 가져오려면 다음 명령을 실행합니다.

`get pods -n <namespace>`

IoT Edge를 통해 배포된 모듈을 확인하려면 다음 명령을 실행합니다.

`get pods -n iotedge`

`iotedge` 네임스페이스에서 실행 중인 모든 Pod에 대한 샘플 출력은 다음과 같습니다.

```
[10.100.10.10]: PS>kubectl get pods -n iotedge
NAME                        READY   STATUS    RESTARTS   AGE
edgeagent-cf6d4ffd4-q5l2k   2/2     Running   0          20h
edgehub-8c9dc8788-2mvwv     2/2     Running   0          56m
filemove-66c49984b7-h8lxc   2/2     Running   0          56m
iotedged-675d7f4b5f-9nml4   1/1     Running   0          20h

[10.100.10.10]: PS>
```

**Status** 상태는 네임스페이스의 모든 Pod가 실행 중임을 나타내고, **Ready** 상태는 Pod에 배포된 컨테이너 수를 나타냅니다. 이전 샘플에서 모든 Pod가 실행 중이고, 각 Pod에 배포된 모든 모듈이 실행 중입니다. 

Azure Arc를 통해 배포된 모듈을 확인하려면 다음 명령을 실행합니다.

`get pods -n azure-arc`

또는 [Kubernetes 대시보드에 연결하여 IoT Edge 또는 Azure Arc 배포를 확인](azure-stack-edge-gpu-monitor-kubernetes-dashboard.md#view-module-status)할 수 있습니다.


지정된 네임스페이스의 특정 Pod에 대한 더 자세한 출력을 위해 다음 명령을 실행할 수 있습니다.

`kubectl describe pod <pod name> -n <namespace>` 

샘플 출력은 다음과 같습니다.

```
[10.100.10.10]: PS>kubectl describe pod filemove-66c49984b7 -n iotedge
Name:           filemove-66c49984b7-h8lxc
Namespace:      iotedge
Priority:       0
Node:           k8s-1hwf613cl-1hwf613/10.139.218.12
Start Time:     Thu, 14 May 2020 12:46:28 -0700
Labels:         net.azure-devices.edge.deviceid=myasegpu-edge
                net.azure-devices.edge.hub=myasegpu2iothub.azure-devices.net
                net.azure-devices.edge.module=filemove
                pod-template-hash=66c49984b7
Annotations:    net.azure-devices.edge.original-moduleid: filemove
Status:         Running
IP:             172.17.75.81
IPs:            <none>
Controlled By:  ReplicaSet/filemove-66c49984b7
Containers:
    proxy:
    Container ID:   docker://fd7975ca78209a633a1f314631042a0892a833b7e942db2e7708b41f03e8daaf
    Image:          azureiotedge/azureiotedge-proxy:0.1.0-beta8
    Image ID:       docker://sha256:5efbf6238f13d24bab9a2b499e5e05bc0c33ab1587d6cf6f289cdbe7aa667563
    Port:           <none>
    Host Port:      <none>
    State:          Running
        Started:      Thu, 14 May 2020 12:46:30 -0700
    Ready:          True
    Restart Count:  0
    Environment:
        PROXY_LOG:  Debug
=============CUT===============================CUT===========================
Volumes:
    config-volume:
    Type:      ConfigMap (a volume populated by a ConfigMap)
    Name:      iotedged-proxy-config
    Optional:  false
    trust-bundle-volume:
    Type:      ConfigMap (a volume populated by a ConfigMap)
    Name:      iotedged-proxy-trust-bundle
    Optional:  false
    myasesmb1local:
    Type:       PersistentVolumeClaim (a reference to a PersistentVolumeClaim in the same namespace)
    ClaimName:  myasesmb1local
    ReadOnly:   false
    myasesmb1:
    Type:       PersistentVolumeClaim (a reference to a PersistentVolumeClaim in the same namespace)
    ClaimName:  myasesmb1
    ReadOnly:   false
    filemove-token-pzvw8:
    Type:        Secret (a volume populated by a Secret)
    SecretName:  filemove-token-pzvw8
    Optional:    false
QoS Class:       BestEffort
Node-Selectors:  <none>
Tolerations:     node.kubernetes.io/not-ready:NoExecute for 300s
                    node.kubernetes.io/unreachable:NoExecute for 300s
Events:          <none>


[10.100.10.10]: PS>
```

#### <a name="to-get-container-logs"></a>컨테이너 로그 가져오기

모듈에 대한 로그를 가져오려면 디바이스의 PowerShell 인터페이스에서 다음 명령을 실행합니다.

`kubectl logs <pod_name> -n <namespace> --all-containers` 

`all-containers` 플래그에서 모든 컨테이너에 대한 모든 로그를 덤프하므로 최근 오류를 확인하는 좋은 방법은 `--tail 10` 옵션을 사용하는 것입니다.

샘플 출력은 다음과 같습니다. 

```
[10.100.10.10]: PS>kubectl logs filemove-66c49984b7-h8lxc -n iotedge --all-containers --tail 10
DEBUG 2020-05-14T20:40:42Z: loop process - 0 events, 0.000s
DEBUG 2020-05-14T20:40:44Z: loop process - 0 events, 0.000s
DEBUG 2020-05-14T20:40:44Z: loop process - 0 events, 0.000s
DEBUG 2020-05-14T20:40:44Z: loop process - 1 events, 0.000s
DEBUG 2020-05-14T20:40:44Z: loop process - 0 events, 0.000s
DEBUG 2020-05-14T20:42:12Z: loop process - 0 events, 0.000s
DEBUG 2020-05-14T20:42:14Z: loop process - 0 events, 0.000s
DEBUG 2020-05-14T20:42:14Z: loop process - 0 events, 0.000s
DEBUG 2020-05-14T20:42:14Z: loop process - 1 events, 0.000s
DEBUG 2020-05-14T20:42:14Z: loop process - 0 events, 0.000s
05/14/2020 19:46:44: Info: Opening module client connection.
05/14/2020 19:46:45: Info: Open done.
05/14/2020 19:46:45: Info: Initializing with input: /home/input, output: /home/output, protocol: Amqp.
05/14/2020 19:46:45: Info: IoT Hub module client initialized.

[10.100.10.10]: PS>
```

### <a name="change-memory-processor-limits-for-kubernetes-worker-node"></a>Kubernetes 작업자 노드에 대한 메모리 및 프로세서 제한 변경

Kubernetes 작업자 노드에 대한 메모리 또는 프로세서 제한을 변경하려면 다음 단계를 수행합니다.

1. [디바이스의 PowerShell 인터페이스에 연결합니다](#connect-to-the-powershell-interface).
1. 작업자 노드 및 역할 옵션에 대한 현재 리소스를 가져오려면 다음 명령을 실행합니다.

    `Get-AzureDataBoxEdgeRole`

    샘플 출력은 다음과 같습니다. `Resources` 섹션 아래의 `Name` 및 `Compute` 값을 확인합니다. `MemoryInBytes` 및 `ProcessorCount`는 Kubernetes 작업자 노드에 대해 현재 할당된 값(메모리 및 프로세서 수)을 나타냅니다.  

    ```powershell
    [10.100.10.10]: PS>Get-AzureDataBoxEdgeRole
    ImageDetail                : Name:mcr.microsoft.com/azureiotedge-agent
                                 Tag:1.0
                                 PlatformType:Linux
    EdgeDeviceConnectionString :
    IotDeviceConnectionString  :
    HubHostName                : ase-srp-007.azure-devices.net
    IotDeviceId                : srp-007-storagegateway
    EdgeDeviceId               : srp-007-edge
    Version                    :
    Id                         : 6ebeff9f-84c5-49a7-890c-f5e05520a506
    Name                       : IotRole
    Type                       : IOT
    Resources                  : Compute:
                                 MemoryInBytes:34359738368
                                 ProcessorCount:12
                                 VMProfile:
    
                                 Storage:
                                 EndpointMap:
                                 EndpointId:c0721210-23c2-4d16-bca6-c80e171a0781
                                 TargetPath:mysmbedgecloudshare1
                                 Name:mysmbedgecloudshare1
                                 Protocol:SMB
    
                                 EndpointId:6557c3b6-d3c5-4f94-aaa0-6b7313ab5c74
                                 TargetPath:mysmbedgelocalshare
                                 Name:mysmbedgelocalshare
                                 Protocol:SMB
                                 RootFileSystemStorageSizeInBytes:0
    
    HostPlatform               : KubernetesCluster
    State                      : Created
    PlatformType               : Linux
    HostPlatformInstanceId     : 994632cb-853e-41c5-a9cd-05b36ddbb190
    IsHostPlatformOwner        : True
    IsCreated                  : True    
    [10.100.10.10]: PS>
    ```
    
1. 작업자 노드에 대한 메모리 및 프로세서 수 값을 변경하려면 다음 명령을 실행합니다.

   ```powershell
   Set-AzureDataBoxEdgeRoleCompute -Name <Name value from the output of Get-AzureDataBoxEdgeRole> -Memory <Value in Bytes> -ProcessorCount <No. of cores>
   ```

   샘플 출력은 다음과 같습니다. 
    
    ```powershell
    [10.100.10.10]: PS>Set-AzureDataBoxEdgeRoleCompute -Name IotRole -MemoryInBytes 32GB -ProcessorCount 16
    
    ImageDetail                : Name:mcr.microsoft.com/azureiotedge-agent
                                 Tag:1.0
                                 PlatformType:Linux
    
    EdgeDeviceConnectionString :
    IotDeviceConnectionString  :
    HubHostName                : ase-srp-007.azure-devices.net
    IotDeviceId                : srp-007-storagegateway
    EdgeDeviceId               : srp-007-edge
    Version                    :
    Id                         : 6ebeff9f-84c5-49a7-890c-f5e05520a506
    Name                       : IotRole
    Type                       : IOT
    Resources                  : Compute:
                                 MemoryInBytes:34359738368
                                 ProcessorCount:16
                                 VMProfile:
    
                                 Storage:
                                 EndpointMap:
                                 EndpointId:c0721210-23c2-4d16-bca6-c80e171a0781
                                 TargetPath:mysmbedgecloudshare1
                                 Name:mysmbedgecloudshare1
                                 Protocol:SMB
    
                                 EndpointId:6557c3b6-d3c5-4f94-aaa0-6b7313ab5c74
                                 TargetPath:mysmbedgelocalshare
                                 Name:mysmbedgelocalshare
                                 Protocol:SMB
    
                                 RootFileSystemStorageSizeInBytes:0
    
    HostPlatform               : KubernetesCluster
    State                      : Created
    PlatformType               : Linux
    HostPlatformInstanceId     : 994632cb-853e-41c5-a9cd-05b36ddbb190
    IsHostPlatformOwner        : True
    IsCreated                  : True
    
    [10.100.10.10]: PS>    
    ```

메모리 및 프로세서 사용량을 변경하는 동안 다음 지침을 따릅니다.

- 기본 메모리는 디바이스 사양의 25%입니다.
- 기본 프로세서 수는 디바이스 사양의 30%입니다.
- 메모리 및 프로세서 수 값을 변경하는 경우 디바이스 메모리와 프로세서 수의 15%~60%에 해당하는 값으로 변경하는 것이 좋습니다. 
- 시스템 구성 요소에 대한 충분한 리소스가 있도록 60%의 상한을 권장합니다. 

## <a name="connect-to-bmc"></a>BMC에 연결

BMC(베이스보드 관리 컨트롤러)는 디바이스를 원격으로 모니터링하고 관리하는 데 사용됩니다. 이 섹션에서는 BMC 구성을 관리하는 데 사용할 수 있는 cmdlet에 대해 설명합니다. 이러한 cmdlet을 실행하기 전에 [디바이스의 PowerShell 인터페이스에 연결](#connect-to-the-powershell-interface)합니다.

- `Get-HcsNetBmcInterface`: 이 cmdlet을 사용하여 BMC의 네트워크 구성 속성(예: `IPv4Address`, `IPv4Gateway`, `IPv4SubnetMask`, `DhcpEnabled`)을 가져옵니다. 
    
    샘플 출력은 다음과 같습니다.
    
    ```powershell
    [10.100.10.10]: PS>Get-HcsNetBmcInterface
    IPv4Address   IPv4Gateway IPv4SubnetMask DhcpEnabled
    -----------   ----------- -------------- -----------
    10.128.53.186 10.128.52.1 255.255.252.0        False
    [10.100.10.10]: PS>
    ```
- `Set-HcsNetBmcInterface`: 이 cmdlet은 다음 두 가지 방법으로 사용할 수 있습니다.

    - 이 cmdlet에서 적절한 값을 `UseDhcp` 매개 변수에 사용하여 BMC에 대한 DHCP 구성을 사용하거나 사용하지 않도록 설정합니다. 

        ```powershell
        Set-HcsNetBmcInterface -UseDhcp $true
        ```

        샘플 출력은 다음과 같습니다. 

        ```powershell
        [10.100.10.10]: PS>Set-HcsNetBmcInterface -UseDhcp $true
        [10.100.10.10]: PS>Get-HcsNetBmcInterface
        IPv4Address IPv4Gateway IPv4SubnetMask DhcpEnabled
        ----------- ----------- -------------- -----------
        10.128.54.8 10.128.52.1 255.255.252.0         True
        [10.100.10.10]: PS>
        ```

    - 이 cmdlet을 사용하여 BMC에 대한 정적 구성을 구성합니다. `IPv4Address`, `IPv4Gateway` 및 `IPv4SubnetMask`에 대한 값을 지정할 수 있습니다. 
    
        ```powershell
        Set-HcsNetBmcInterface -IPv4Address "<IPv4 address of the device>&quot; -IPv4Gateway &quot;<IPv4 address of the gateway>&quot; -IPv4SubnetMask &quot;<IPv4 address for the subnet mask>"
        ```        
        
        샘플 출력은 다음과 같습니다. 

        ```powershell
        [10.100.10.10]: PS>Set-HcsNetBmcInterface -IPv4Address 10.128.53.186 -IPv4Gateway 10.128.52.1 -IPv4SubnetMask 255.255.252.0
        [10.100.10.10]: PS>Get-HcsNetBmcInterface
        IPv4Address   IPv4Gateway IPv4SubnetMask DhcpEnabled
        -----------   ----------- -------------- -----------
        10.128.53.186 10.128.52.1 255.255.252.0        False
        [10.100.10.10]: PS>
        ```    

- `Set-HcsBmcPassword`: 이 cmdlet을 사용하여 `EdgeUser`에 대한 BMC 암호를 수정합니다. 사용자 이름(`EdgeUser`)은 대/소문자를 구분합니다.

    샘플 출력은 다음과 같습니다. 

    ```powershell
    [10.100.10.10]: PS> Set-HcsBmcPassword -NewPassword "Password1"
    [10.100.10.10]: PS>
    ```

## <a name="exit-the-remote-session"></a>원격 세션 종료

원격 PowerShell 세션을 종료하려면 PowerShell 창을 닫습니다.

## <a name="next-steps"></a>다음 단계

- Azure Portal에서 [Azure Stack Edge Pro GPU](azure-stack-edge-gpu-deploy-prep.md)를 배포합니다.
