---
title: AKS(Azure Kubernetes Service) 클러스터 노드에 대한 SSH 연결 만들기
description: 문제 해결 및 유지 관리 작업은 AKS(Azure Kubernetes Service) 클러스터 노드를 사용하여 SSH 연결을 만드는 방법을 알아봅니다.
services: container-service
ms.topic: article
ms.date: 05/17/2021
ms.custom: contperf-fy21q4
ms.openlocfilehash: 87e548cd39c7c064b11131c28790d8335bd942fb
ms.sourcegitcommit: 34aa13ead8299439af8b3fe4d1f0c89bde61a6db
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/18/2021
ms.locfileid: "122530847"
---
# <a name="connect-with-ssh-to-azure-kubernetes-service-aks-cluster-nodes-for-maintenance-or-troubleshooting"></a>유지 관리 또는 문제 해결을 위해 AKS(Azure Kubernetes Service) 클러스터 노드에 대한 SSH와 연결

AKS(Azure Kubernetes Service) 클러스터의 수명 주기 내내 AKS 노드에 액세스해야 합니다. 유지 관리, 로그 수집 또는 기타 문제 해결 작업을 위해 이 액세스를 사용할 수 있습니다. Windows Server 노드를 비롯해 SSH를 사용하여 AKS 노드에 액세스할 수 있습니다. [RDP(원격 데스크톱 프로토콜) 연결을 사용하여 Windows Server 노드에 연결][aks-windows-rdp]할 수도 있습니다. 보안을 위해 AKS 노드는 인터넷에 노출되지 않습니다. AKS 노드에 SSH하려면 `kubectl debug` 또는 개인 IP 주소를 사용합니다.

이 문서에서는 AKS 노드를 통해 SSH를 연결하는 방법을 알려줍니다.

## <a name="before-you-begin"></a>시작하기 전에

이 문서에서는 기존 AKS 클러스터가 있다고 가정합니다. AKS 클러스터가 필요한 경우 AKS 빠른 시작 [Azure CLI 사용][aks-quickstart-cli] 또는 [Azure Portal 사용][aks-quickstart-portal]을 참조하세요.

이 문서에서는 SSH 키가 있다고 가정합니다. [macOS, Linux][ssh-nix] 또는 [Windows][ssh-windows]를 사용하여 SSH 키를 만들 수 있습니다. PuTTY Gen을 사용하여 키 쌍을 만드는 경우 기본 PuTTy 프라이빗 키 형식(.ppk 파일) 대신 OpenSSH 형식으로 키 쌍을 저장합니다.

또한 Azure CLI 버전 2.0.64 이상이 설치되고 구성되어 있어야 합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 설치][install-azure-cli]를 참조하세요.

## <a name="create-the-ssh-connection-to-a-linux-node"></a>Linux 노드에 대한 SSH 연결 만들기

AKS 노드에 대한 SSH 연결을 만들려면 `kubectl debug`를 사용하여 노드에서 권한 있는 컨테이너를 실행합니다. 노드 목록을 만들려면 `kubectl get nodes`를 사용합니다.

```output
$ kubectl get nodes -o wide

NAME                                STATUS   ROLES   AGE     VERSION   INTERNAL-IP   EXTERNAL-IP   OS-IMAGE                         KERNEL-VERSION     CONTAINER-RUNTIME
aks-nodepool1-12345678-vmss000000   Ready    agent   13m     v1.19.9   10.240.0.4    <none>        Ubuntu 18.04.5 LTS               5.4.0-1046-azure   containerd://1.4.4+azure
aks-nodepool1-12345678-vmss000001   Ready    agent   13m     v1.19.9   10.240.0.35   <none>        Ubuntu 18.04.5 LTS               5.4.0-1046-azure   containerd://1.4.4+azure
aksnpwin000000                      Ready    agent   87s     v1.19.9   10.240.0.67   <none>        Windows Server 2019 Datacenter   10.0.17763.1935    docker://19.3.1
```

노드에서 컨테이너 이미지를 실행하여 연결하려면 `kubectl debug`를 사용합니다.

```azurecli-interactive
kubectl debug node/aks-nodepool1-12345678-vmss000000 -it --image=mcr.microsoft.com/aks/fundamental/base-ubuntu:v0.0.11
```

이 명령은 노드에서 권한 있는 컨테이너를 실행하고 SSH를 통해 연결합니다.

```output
$ kubectl debug node/aks-nodepool1-12345678-vmss000000 -it --image=mcr.microsoft.com/aks/fundamental/base-ubuntu:v0.0.11
Creating debugging pod node-debugger-aks-nodepool1-12345678-vmss000000-bkmmx with container debugger on node aks-nodepool1-12345678-vmss000000.
If you don't see a command prompt, try pressing enter.
root@aks-nodepool1-12345678-vmss000000:/#
```

이 권한 있는 컨테이너는 노드에 대한 액세스를 제공합니다.

## <a name="create-the-ssh-connection-to-a-windows-node"></a>Windows 노드에 대한 SSH 연결 만들기

지금은 `kubectl debug`로 SSH를 사용하여 Windows Server 노드에 직접 연결할 수 없습니다. 대신 먼저 클러스터의 다튼 노드에 연결한 다음 SSH를 사용하여 해당 노드에서 Windows Server 노드에 연결해야 합니다. 또는 SSH를 사용하는 대신 [RDP(원격 데스크톱 프로토콜) 연결을 사용하여 Windows Server 노드에 연결][aks-windows-rdp]할 수 있습니다.

클러스터의 다른 노드에 연결하려면 `kubectl debug`를 사용합니다. 자세한 정보는 [Linux 노드에 대한 SSH 연결 만들기][ssh-linux-kubectl-debug]를 참조하세요.

다른 노드에서 Windows Server 노드로의 SSH 연결을 만들려면 AKS 클러스터를 만들 때 제공된 SSH 키와 Windows Server 노드의 내부 IP 주소를 사용합니다.

새 터미널 창을 열고 `kubectl get pods`를 사용하여 `kubectl debug`에서 시작한 Pod의 이름을 가져옵니다.

```output
$ kubectl get pods

NAME                                                    READY   STATUS    RESTARTS   AGE
node-debugger-aks-nodepool1-12345678-vmss000000-bkmmx   1/1     Running   0          21s
```

위의 예제에서 *node-debugger-aks-nodepool1-12345678-vmss000000-bkmmx* 는 `kubectl debug`에서 실행된 Pod의 이름입니다.

`kubectl port-forward`를 사용하여 배포된 Pod에 대한 연결을 열 수 있습니다.

```
$ kubectl port-forward node-debugger-aks-nodepool1-12345678-vmss000000-bkmmx 2022:22
Forwarding from 127.0.0.1:2022 -> 22
Forwarding from [::1]:2022 -> 22
```

위의 예제는 개발 컴퓨터의 포트 2022에서 배포된 Pod의 포트 22로 네트워크 트래픽을 전달하기 시작합니다. `kubectl port-forward`를 사용하여 연결을 열고 네트워크 트래픽을 전달하면 `kubectl port-forward` 명령을 중지할 때까지 연결은 열린 상태로 유지됩니다.

새 터미널을 열고 `kubectl get nodes`를 사용하여 Windows Server 노드의 내부 IP 주소를 표시합니다.

```output
$ kubectl get nodes -o wide

NAME                                STATUS   ROLES   AGE     VERSION   INTERNAL-IP   EXTERNAL-IP   OS-IMAGE                         KERNEL-VERSION     CONTAINER-RUNTIME
aks-nodepool1-12345678-vmss000000   Ready    agent   13m     v1.19.9   10.240.0.4    <none>        Ubuntu 18.04.5 LTS               5.4.0-1046-azure   containerd://1.4.4+azure
aks-nodepool1-12345678-vmss000001   Ready    agent   13m     v1.19.9   10.240.0.35   <none>        Ubuntu 18.04.5 LTS               5.4.0-1046-azure   containerd://1.4.4+azure
aksnpwin000000                      Ready    agent   87s     v1.19.9   10.240.0.67   <none>        Windows Server 2019 Datacenter   10.0.17763.1935    docker://19.3.1
```

위의 예제에서 *10.240.0.67* 은 Windows Server 노드의 내부 IP 주소입니다.

내부 IP 주소를 사용하여 Windows Server 노드에 대한 SSH 연결을 만듭니다. AKS 노드의 기본 사용자 이름은 *azureuser* 입니다. 프롬프트를 수락해 연결을 계속합니다. 그러면 Windows Server 노드의 Bash 프롬프트가 제공됩니다.

```output
$ ssh -o 'ProxyCommand ssh -p 2022 -W %h:%p azureuser@127.0.0.1' azureuser@10.240.0.67

The authenticity of host '10.240.0.67 (10.240.0.67)' can't be established.
ECDSA key fingerprint is SHA256:1234567890abcdefghijklmnopqrstuvwxyzABCDEFG.
Are you sure you want to continue connecting (yes/no)? yes

[...]

Microsoft Windows [Version 10.0.17763.1935]
(c) 2018 Microsoft Corporation. All rights reserved.

azureuser@aksnpwin000000 C:\Users\azureuser>
```

위의 예제는 개발 컴퓨터의 포트 2022를 통해 Windows Server 노드의 포트 22에 연결합니다.

> [!NOTE]
> 암호 인증을 사용하려면 `-o PreferredAuthentications=password`를 사용합니다. 예를 들면 다음과 같습니다.
>
> ```console
>  ssh -o 'ProxyCommand ssh -p 2022 -W %h:%p azureuser@127.0.0.1' -o PreferredAuthentications=password azureuser@10.240.0.67
> ```

## <a name="remove-ssh-access"></a>SSH 액세스 제거

작업이 완료되면 SSH 세션을 `exit`하고 포트 전달을 중지한 다음, 대화형 컨테이너 세션을 `exit`합니다. 대화형 컨테이너 세션이 닫히면 AKS 클러스터에서 SSH 액세스에 사용되는 Pod가 삭제됩니다.

## <a name="next-steps"></a>다음 단계

추가적인 문제 해결 데이터가 필요한 경우 [kubelet 로그를 확인][view-kubelet-logs]하거나 [Kubernetes 마스터 노드 로그를 확인][view-master-logs]하면 됩니다.


<!-- INTERNAL LINKS -->
[view-kubelet-logs]: kubelet-logs.md
[view-master-logs]: monitor-aks-reference.md#resource-logs
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[aks-windows-rdp]: rdp.md
[ssh-nix]: ../virtual-machines/linux/mac-create-ssh-keys.md
[ssh-windows]: ../virtual-machines/linux/ssh-from-windows.md
[ssh-linux-kubectl-debug]: #create-the-ssh-connection-to-a-linux-node