---
title: Azure Stack Edge Pro GPU 디바이스의 Arc 지원 Kubernetes에서 PHP `Guestbook` 앱 배포 | Microsoft Docs
description: Azure Stack Edge Pro 디바이스의 Arc 지원 Kubernetes 클러스터에서 GitOps를 사용하여 Redis와 함께 PHP `Guestbook` 상태 비저장 애플리케이션을 배포하는 방법을 설명합니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 02/22/2021
ms.author: alkohli
ms.openlocfilehash: 4c5a361fafeaed6452481adc9e6eae856828bfda
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128635587"
---
# <a name="deploy-a-php-guestbook-stateless-application-with-redis-on-arc-enabled-kubernetes-cluster-on-azure-stack-edge-pro-gpu"></a>Azure Stack Edge Pro GPU의 Arc 지원 Kubernetes 클러스터에서 Redis와 함께 PHP `Guestbook` 상태 비저장 애플리케이션 배포

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

이 문서에서는 Kubernetes 및 Azure Arc를 사용하여 간단한 다중 계층 웹 애플리케이션을 빌드하고 배포하는 방법을 보여 줍니다. 이 예제는 다음과 같은 구성 요소로 구성됩니다.

- `guestbook` 항목을 저장하는 단일 인스턴스 Redis 마스터
- 읽기를 제공하기 위해 복제된 다중 Redis 인스턴스
- 다중 웹 프런트 엔드 인스턴스

배포는 Azure Stack Edge Pro 디바이스의 Arc 지원 Kubernetes 클러스터에서 GitOps를 사용하여 수행됩니다. 

이 절차는 [Azure Stack Edge Pro 디바이스의 Kubernetes 워크로드](azure-stack-edge-gpu-kubernetes-workload-management.md)를 검토하고 [Azure Arc 지원 Kubernetes(미리 보기)](../azure-arc/kubernetes/overview.md)의 개념에 대해 잘 알고 있는 사용자를 위해 제공됩니다.

> [!NOTE]
> 이 문서에는 Microsoft에서 더 이상 사용하지 않는 용어인 *slave* 에 대한 참조가 포함되어 있습니다. 소프트웨어에서 용어가 제거되면 이 문서에서 해당 용어가 제거됩니다.

## <a name="prerequisites"></a>사전 요구 사항

상태 비저장 애플리케이션을 배포하기 전에 디바이스에 액세스하는 데 사용할 디바이스 및 클라이언트에 대한 다음 필수 구성 요소를 완료했는지 확인합니다.

### <a name="for-device"></a>디바이스의 경우

1. 1노드 Azure Stack Edge Pro 디바이스에 대한 로그인 자격 증명이 있습니다.
    1. 디바이스가 활성화되어야 있습니다. [디바이스 활성화](azure-stack-edge-gpu-deploy-activate.md)를 참조하세요.
    1. 디바이스에 Azure Portal을 통해 구성된 컴퓨팅 역할이 있으며, Kubernetes 클러스터가 있습니다. [컴퓨팅 구성](azure-stack-edge-gpu-deploy-configure-compute.md)을 참조하세요.

1. 디바이스의 기존 Kubernetes 클러스터에서 Azure Arc를 사용하도록 설정하고 Azure Portal에 해당하는 Azure Arc 리소스가 있습니다. 자세한 단계는 [Azure Stack Edge Pro 디바이스에서 Azure Arc 사용](azure-stack-edge-gpu-deploy-arc-kubernetes-cluster.md)을 참조하세요.

### <a name="for-client-accessing-the-device"></a>디바이스에 액세스하는 클라이언트

1. Azure Stack Edge Pro 디바이스에 액세스하는 데 사용되는 Windows 클라이언트 시스템이 있습니다.
  
    - 클라이언트에서 Windows PowerShell 5.0 이상을 실행하고 있습니다. 최신 버전의 Windows PowerShell을 다운로드하려면 [Windows Powershell 설치](/powershell/scripting/install/installing-windows-powershell)로 이동합니다.
    
    - [지원되는 운영 체제](azure-stack-edge-gpu-system-requirements.md#supported-os-for-clients-connected-to-device)를 사용하는 다른 클라이언트도 있을 수 있습니다. 이 문서에서는 Windows 클라이언트를 사용하는 절차에 대해 설명합니다. 
    
1. [Azure Stack Edge Pro 디바이스에서 Kubernetes 클러스터에 액세스](azure-stack-edge-gpu-create-kubernetes-cluster.md)에 설명된 절차를 완료했습니다. 수행한 작업은 다음과 같습니다.
    
    - 클라이언트에 `kubectl`을 설치했습니다. <!--and saved the `kubeconfig` file with the user configuration to C:\\Users\\&lt;username&gt;\\.kube. -->
    
    - `kubectl` 클라이언트 버전이 Azure Stack Edge Pro 디바이스에서 실행되는 Kubernetes 마스터 버전보다 두 버전 이상 차이나지 않는지 확인합니다. 
      - `kubectl version`을 사용하여 클라이언트에서 실행되는 kubectl의 버전을 확인합니다. 전체 버전을 기록해 둡니다.
      - Azure Stack Edge Pro 디바이스의 로컬 UI에서 **개요** 로 이동하여 Kubernetes 소프트웨어 번호를 확인합니다. 
      - 이 두 개의 버전이 지원되는 Kubernetes 버전에서 제공되는 매핑과 호환되는지 확인합니다. <!--insert link-->

1. [Azure Arc 배포를 실행하는 데 사용할 수 있는 Gitops 구성](https://github.com/kagoyal/dbehaikudemo)이 있습니다. 이 예제에서는 다음 `yaml` 파일을 사용하여 Azure Stack Edge Pro 디바이스에 배포합니다.

    - `frontend-deployment.yaml`<!-- - The guestbook application has a web frontend serving the HTTP requests written in PHP. It is configured to connect to the redis-master Service for write requests and the redis-slave service for Read requests. This file describes a deployment that runs the frontend of the guestbook application.-->
    - `frontend-service.yaml` <!-- - This allows you to configure an externally visible frontend Service that can be accessed from outside the Kubernetes cluster on your device.-->
    - `redis-master-deployment.yaml` <!-- - This deployment file runs a single replica Redis master Pod. The guestbook application uses Redis to store its data. It writes its data to a Redis master instance and reads data from multiple Redis slave instances.-->
    - `redis-master-service.yaml` <!-- - The guestbook application needs to communicate to the Redis master to write its data. You need to apply a Service to proxy the traffic to the Redis master Pod. A Service defines a policy to access the Pods.-->
    - `redis-slave-deployment.yaml` <!-- - Although the Redis master is a single pod, you can make it highly available to meet traffic demands by adding replica Redis slaves. The Redis Slave Deployment specifies two replicas.-->
    - `redis-slave-service.yaml` <!-- - The guestbook application needs to communicate to Redis slaves to read data. To make the Redis slaves discoverable, you need to set up a Service. A Service provides transparent load balancing to a set of Pods.-->

<!-- az cli version requirements-->


## <a name="deploy-configuration"></a>구성 배포

Azure Portal을 통해 GitOps 구성을 배포하도록 Azure Arc 리소스를 구성하려면 다음 단계를 수행합니다. 

1. Azure Portal에서 디바이스의 Kubernetes 클러스터에서 Azure Arc를 사용하도록 설정할 때 만든 Azure Arc 리소스로 이동합니다. 

    ![Azure Arc 리소스로 이동](media/azure-stack-edge-gpu-connect-powershell-interface/verify-azure-arc-enabled-1.png)

1. **구성** 으로 이동하여 **+ 구성 추가** 를 선택합니다.

    ![스크린샷은 구성 추가가 선택된 Azure Arc 지원 Kubernetes 클러스터를 보여 줍니다.](media/azure-stack-edge-gpu-connect-powershell-interface/select-configurations-1.png)

1. **구성 추가** 에서 필드에 적절한 값을 입력한 다음, **적용** 을 선택합니다.

    |매개 변수  |Description |
    |---------|---------|
    |구성 이름     | 구성 리소스의 이름입니다.        |
    |연산자 인스턴스 이름     |특정 구성을 식별하는 운영자의 인스턴스 이름입니다. 이름은 소문자, 영숫자, 하이픈 및 마침표만 가능한 최대 253자의 문자열입니다.         |
    |연산자 네임스페이스     | 배포 `yaml`에 지정된 네임스페이스와 일치하도록 **demotestguestbook** 으로 설정합니다. <br> 필드는 연산자가 설치되는 네임스페이스를 정의합니다. 이름은 소문자, 영숫자, 하이픈 및 마침표만 가능한 최대 253자의 문자열입니다.         |
    |리포지토리 URL     |<br>GitOps 구성이 있는 `http://github.com/username/repo` 또는 `git://github.com/username/repo` 형식의 git 리포지토리에 대한 경로입니다.         |
    |연산자 범위     | **Namespace** 를 선택합니다. <br>이 매개 변수는 운영자가 설치되는 범위를 정의합니다. 배포 yaml 파일에 지정된 네임스페이스에 연산자를 설치하려면 Namespace를 선택합니다.       |
    |연산자 유형     | 기본값으로 둡니다. <br>이 매개 변수는 기본적으로 flux로 설정된 연산자의 유형을 지정합니다.        |
    |연산자 매개 변수     | 이 필드를 비워 둡니다. <br>이 매개 변수는 flux 연산자에 전달할 매개 변수를 포함합니다.        |
    |Helm     | 이 매개 변수를 **사용 안 함** 으로 설정합니다. <br>차트 기반 배포를 수행하는 경우 이 옵션을 사용하도록 설정합니다.        |


    ![구성 추가](media/azure-stack-edge-gpu-connect-powershell-interface/add-configuration-1.png)


1. 구성 배포가 시작되고 **연산자 상태** 가 **보류 중** 으로 표시됩니다. 

    ![스크린샷을 새로 고치면 Azure Arc 지원 Kubernetes 클러스터가 보류 중 상태로 표시됩니다.](media/azure-stack-edge-gpu-connect-powershell-interface/view-configurations-1.png)

1. 배포는 몇 분 정도 소요됩니다. 배포가 완료되면 **연산자 상태** 는 **설치됨** 으로 표시됩니다.

    ![스크린샷은 설치된 상태의 Azure Arc 지원 Kubernetes 클러스터를 보여 줍니다.](media/azure-stack-edge-gpu-connect-powershell-interface/view-configurations-2.png)

## <a name="verify-deployment"></a>배포 확인

GitOps 구성을 통한 배포는 git 리포지토리에 있는 배포 `yaml` 파일에 지정된 대로 `demotestguestbook` 네임스페이스를 만듭니다.

1. GitOps 구성을 적용한 후 [디바이스의 PowerShell 인터페이스에 연결합니다](azure-stack-edge-gpu-connect-powershell-interface.md#connect-to-the-powershell-interface).
1. 다음 명령을 실행하여 배포에 해당하는 `demotestguestbook` 네임스페이스에서 실행 중인 pod를 나열합니다.

    `kubectl get pods -n <your-namespace>`
  
    샘플 출력은 다음과 같습니다.
  
    ```powershell
    [10.128.44.240]: PS>kubectl get pods -n demotestguestbook
    NAME                            READY   STATUS    RESTARTS   AGE
    aseoperator1-5569658644-cqtb5   1/1     Running   0          91m
    frontend-6cb7f8bd65-4xb4f       1/1     Running   0          91m
    frontend-6cb7f8bd65-q9cxj       1/1     Running   0          91m
    frontend-6cb7f8bd65-xpzs6       1/1     Running   0          91m
    memcached-86bdf9f56b-5l2fq      1/1     Running   0          91m
    redis-master-7db7f6579f-2z29w   1/1     Running   0          91m
    redis-slave-7664787fbc-lgr2n    1/1     Running   0          91m
    redis-slave-7664787fbc-vlvzn    1/1     Running   0          91m
    [10.128.44.240]: PS>
    ```  

1. 이 예제에서 프런트 엔드 서비스는 형식:LoadBalancer로 배포되었습니다. `guestbook`을 보려면 이 서비스의 IP 주소를 찾아야 합니다. 다음 명령을 실행합니다.

    `kubectl get service -n <your-namespace>`
    
    ```powershell
    [10.128.44.240]: PS>kubectl get service -n demotestguestbook
    NAME           TYPE           CLUSTER-IP       EXTERNAL-IP     PORT(S)        AGE
    frontend       LoadBalancer   10.96.79.38      10.128.44.245   80:31238/TCP   85m
    memcached      ClusterIP      10.102.47.75     <none>          11211/TCP      85m
    redis-master   ClusterIP      10.104.32.99     <none>          6379/TCP       85m
    redis-slave    ClusterIP      10.104.215.146   <none>          6379/TCP       85m
    [10.128.44.240]: PS>
    ```
1. `type:LoadBalancer`의 프런트 엔드 서비스에는 외부 IP 주소가 있습니다. 이 IP는 디바이스에서 컴퓨팅 네트워크 설정을 구성할 때 외부 서비스에 대해 지정한 IP 주소 범위에서 가져온 것입니다. 이 IP 주소를 사용하여 URL: `https://<external-IP-address>`에서 `guestbook`을 봅니다.

    ![방명록 보기](media/azure-stack-edge-gpu-connect-powershell-interface/view-guestbook-1.png)

## <a name="delete-deployment"></a>배포 삭제

배포를 삭제하려면 Azure Portal에서 구성을 삭제할 수 있습니다. 구성을 삭제하면 배포 및 서비스를 비롯하여 생성된 개체가 삭제됩니다.

1. Azure Portal에서 Azure Arc 리소스 > 구성으로 이동합니다. 
1. 삭제하려는 구성을 찾습니다. ...을 선택하여 상황에 맞는 메뉴를 호출하고 **삭제** 를 선택합니다.
    ![구성 삭제](media/azure-stack-edge-gpu-connect-powershell-interface/delete-configuration-1.png)

구성을 삭제하는 데 몇 분 정도 걸릴 수 있습니다.
 
<!--```powershell
kubectl delete deployment <deployment-name> -n <your-namespace>
kubectl delete service <service-name> -n <your-namespace>
```

Here is a sample output of when you delete the deployments and the services.

```powershell
C:\Users\user>kubectl delete deployment,svc mysql -n userns1
deployment.apps "mysql" deleted
service "mysql" deleted
C:\Users\user>
```-->


## <a name="next-steps"></a>다음 단계

[Azure Stack Edge Pro 디바이스에서 Kubernetes 대시보드를 사용하여 배포를 모니터링](azure-stack-edge-gpu-monitor-kubernetes-dashboard.md)하는 방법을 알아봅니다.