---
title: Azure Stack Edge Pro GPU 디바이스에 Azure Arc 데이터 컨트롤러 배포 | Microsoft Docs
description: Azure Stack Edge Pro GPU 디바이스에 Azure Arc 데이터 컨트롤러 및 Azure Data Services를 배포하는 방법을 설명합니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 04/15/2021
ms.author: alkohli
ms.openlocfilehash: e1078bb12b6d33eb4d4d35cc3e1e11f91ef39e9b
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2021
ms.locfileid: "129705550"
---
# <a name="deploy-azure-data-services-on-your-azure-stack-edge-pro-gpu-device"></a>Azure Stack Edge Pro GPU 디바이스에 Azure Data Services 배포

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

이 문서에서는 Azure Arc 데이터 컨트롤러를 만든 다음 Azure Stack Edge Pro GPU 디바이스에 Azure Data Services를 배포하는 프로세스를 설명합니다. 

Azure Arc 데이터 컨트롤러는 고객 관리 환경에서 Azure Data Services를 사용하는 로컬 컨트롤 플레인입니다. Azure Stack Edge Pro GPU 디바이스에서 실행되는 Kubernetes 클러스터에서 Azure Arc 데이터 컨트롤러를 만든 후에는 해당 데이터 컨트롤러에 SQL Managed Instance와 같은 Azure Data Services를 배포할 수 있습니다.

데이터 컨트롤러를 만든 다음 SQL Managed Instance를 배포하는 절차에는 PowerShell 및 `kubectl`(디바이스의 Kubernetes 클러스터에 대한 명령줄 액세스를 제공하는 기본 도구)이 포함됩니다.


## <a name="prerequisites"></a>필수 구성 요소

시작하기 전에 다음 사항을 확인합니다.

1. Azure Stack Edge Pro GPU 디바이스에 대한 액세스 권한이 있고 [Azure Stack Edge Pro 활성화](azure-stack-edge-gpu-deploy-activate.md)에 설명된 대로 디바이스를 활성화했습니다.

1. 디바이스에서 컴퓨팅 역할을 사용하도록 설정했습니다. [Azure Stack Edge Pro GPU 디바이스에서 컴퓨팅 구성](azure-stack-edge-gpu-deploy-configure-compute.md)의 지침에 따라 디바이스에서 컴퓨팅을 구성할 때 디바이스에 Kubernetes 클러스터도 만들었습니다.

1. 로컬 웹 UI의 **디바이스** 페이지에 Kubernetes API 엔드포인트가 있습니다. 자세한 내용은 [Kubernetes API 엔드포인트 가져오기](azure-stack-edge-gpu-deploy-configure-compute.md#get-kubernetes-endpoints)의 지침을 참조하세요.

1. 디바이스에 연결할 클라이언트에 액세스했습니다. 
    1. 이 문서에서는 PowerShell 5.0 이상을 실행하는 Windows 클라이언트 시스템을 사용하여 디바이스에 액세스합니다. [지원되는 운영 체제](azure-stack-edge-gpu-system-requirements.md#supported-os-for-clients-connected-to-device)가 있는 다른 클라이언트를 사용할 수 있습니다. 
    1. 클라이언트에 `kubectl`을 설치합니다. 클라이언트 버전의 경우:
        1. 디바이스에 설치된 Kubernetes 서버 버전을 식별합니다. 디바이스의 로컬 UI에서 **소프트웨어 업데이트** 페이지로 이동합니다. 이 페이지에서 **Kubernetes 서버 버전** 을 확인합니다.
        1. 마스터에서 부 버전이 두 개 이상 왜곡된 클라이언트를 다운로드합니다. 그러나 클라이언트 버전은 최대 하나의 부 버전으로 마스터를 이끌 수 있습니다. 예를 들어, v1.3 마스터는 v1.1, v1.2 및 v1.3 노드와 함께 사용해야 하며 v1.2, v1.3 및 v1.4 클라이언트에서 작동해야 합니다. Kubernetes 클라이언트 버전에 대한 자세한 내용은 [Kubernetes 버전 및 버전 기울이기 지원 정책](https://kubernetes.io/docs/setup/release/version-skew-policy/#supported-version-skew)을 참조하세요.
    
1. 필요에 따라 [Azure Arc 사용 데이터 서비스를 배포 하 고 관리 하기 위한 클라이언트 도구를 설치](../azure-arc/data/install-client-tools.md)합니다. 이러한 도구는 필수는 아니지만 권장됩니다.  
1. 데이터 컨트롤러와 하나의 SQL Managed Instance를 프로비저닝하기에 충분한 리소스가 디바이스에 있는지 확인합니다. 데이터 컨트롤러와 하나의 SQL Managed Instance의 경우 최소 16GB의 RAM과 4개의 CPU 코어가 필요합니다. 자세한 지침은 [Azure Arc 사용 데이터 서비스 배포에 대 한 최소 요구 사항](../azure-arc/data/sizing-guidance.md#minimum-deployment-requirements)을 참조 하세요.


## <a name="configure-kubernetes-external-service-ips"></a>Kubernetes 외부 서비스 IP 구성

1. 디바이스의 로컬 웹 UI로 이동한 다음 **컴퓨팅** 으로 이동합니다.
1. 컴퓨팅에 사용하도록 설정된 네트워크를 선택합니다. 

    ![로컬 UI의 컴퓨팅 페이지 2](./media/azure-stack-edge-gpu-deploy-arc-data-controller/compute-network-1.png)

1. 다른 외부 서비스 또는 컨테이너에 대해 이미 구성한 IP 외에 추가로 Kubernetes 외부 서비스 IP 3개를 제공해야 합니다. 데이터 컨트롤러는 두 개의 서비스 IP를 사용하고 세 번째 IP는 SQL Managed Instance를 만들 때 사용됩니다. 배포할 추가 데이터 서비스마다 하나의 IP가 필요합니다. 

    ![로컬 UI의 컴퓨팅 페이지 3](./media/azure-stack-edge-gpu-deploy-arc-data-controller/compute-network-2.png)

1. 설정을 적용하면 이러한 새 IP가 이미 기존 Kubernetes 클러스터에 즉시 적용됩니다. 


## <a name="deploy-azure-arc-data-controller"></a>Azure Arc 데이터 컨트롤러 배포

데이터 컨트롤러를 배포하기 전에 네임스페이스를 만들어야 합니다.

### <a name="create-namespace"></a>네임스페이스 만들기 

데이터 컨트롤러를 배포할 새 전용 네임스페이스를 만듭니다. 또한 사용자를 만든 다음 만든 네임스페이스에 대한 액세스 권한을 사용자에게 부여합니다. 

> [!NOTE]
> 네임스페이스와 사용자 이름 모두에 [DNS 하위 도메인 명명 규칙](https://kubernetes.io/docs/concepts/overview/working-with-objects/names/#dns-subdomain-names)이 적용됩니다.

1. [PowerShell 인터페이스에 연결하세요](azure-stack-edge-gpu-connect-powershell-interface.md#connect-to-the-powershell-interface).
1. 네임스페이스 만들기 유형:

    `New-HcsKubernetesNamespace -Namespace <Name of namespace>`

1. 사용자를 만듭니다. 유형: 

    `New-HcsKubernetesUser -UserName <User name>`

1. 구성 파일은 일반 텍스트로 표시됩니다. 이 파일을 복사하고 *구성* 파일로 저장합니다. 

    > [!IMPORTANT]
    > 구성 파일을 *.txt* 파일로 저장하지 말고 파일 확장자 없이 파일을 저장합니다.

1. 구성 파일은 로컬 컴퓨터에 있는 사용자 프로필의 `.kube` 폴더에 있어야 합니다. 사용자 프로필의 해당 폴더에 파일을 복사합니다.

    ![클라이언트에 있는 구성 파일의 위치](media/azure-stack-edge-gpu-create-kubernetes-cluster/location-config-file.png)
1. 만든 네임스페이스에 사용자 액세스 권한을 부여합니다. 유형: 

    `Grant-HcsKubernetesNamespaceAccess -Namespace <Name of namespace> -UserName <User name>`

    다음은 이전 명령의 샘플 출력입니다. 이 예에서는 `myadstest` 네임스페이스, `myadsuser` 사용자를 만들고 네임스페이스에 대한 액세스 권한을 사용자에게 부여했습니다.
    
    ```powershell
    [10.100.10.10]: PS>New-HcsKubernetesNamespace -Namespace myadstest
    [10.100.10.10]: PS>New-HcsKubernetesUser -UserName myadsuser
    apiVersion: v1
    clusters:
    - cluster:
        certificate-authority-data: LS0tLS1CRUdJTiBD=======//snipped//=======VSVElGSUNBVEUtLS0tLQo=
        server: https://compute.myasegpudev.wdshcsso.com:6443
      name: kubernetes
    contexts:
    - context:
        cluster: kubernetes
        user: myadsuser
      name: myadsuser@kubernetes
    current-context: myadsuser@kubernetes
    kind: Config
    preferences: {}
    users:
    - name: myadsuser
      user:
        client-certificate-data: LS0tLS1CRUdJTiBDRV=========//snipped//=====EE9PQotLS0kFURSBLRVktLS0tLQo=
    
    [10.100.10.10]: PS>Grant-HcsKubernetesNamespaceAccess -Namespace myadstest -UserName myadsuser
    [10.100.10.10]: PS>Set-HcsKubernetesAzureArcDataController -SubscriptionId db4e2fdb-6d80-4e6e-b7cd-736098270664 -ResourceGroupName myasegpurg -Location "EastUS" -UserName myadsuser -Password "Password1" -DataControllerName "arctestcontroller" -Namespace myadstest
    [10.100.10.10]: PS>
    ```
1. 시스템의 호스트 파일에 DNS 항목을 추가합니다. 

    1. 관리자 권한으로 메모장을 실행하고 `C:\windows\system32\drivers\etc\hosts`에 있는 `hosts` 파일을 엽니다. 
    2. **디바이스** 페이지의 로컬 UI에 저장한 정보(전제 조건)를 사용하여 호스트 파일에 항목을 작성합니다. 

        예를 들어 이 엔드포인트 `https://compute.myasegpudev.microsoftdatabox.com/[10.100.10.10]`을 복사하여 디바이스 IP 주소 및 DNS 도메인으로 다음 항목을 작성합니다. 

        `10.100.10.10 compute.myasegpudev.microsoftdatabox.com`

1. Kubernetes Pod에 연결할 수 있는지 확인하려면 cmd 프롬프트 또는 PowerShell 세션을 시작합니다. 유형:
    
    ```powershell
    PS C:\WINDOWS\system32> kubectl get pods -n "myadstest"
    No resources found.
    PS C:\WINDOWS\system32>
    ```
이제 네임스페이스에 데이터 컨트롤러 및 데이터 서비스 애플리케이션을 배포한 다음 애플리케이션과 해당 로그를 볼 수 있습니다.

### <a name="create-data-controller"></a>데이터 컨트롤러 만들기

데이터 컨트롤러는 API, 컨트롤러 서비스, 부트스트래퍼, 모니터링 데이터베이스, 대시보드를 제공하기 위해 Kubernetes 클러스터에 배포되는 Pod의 컬렉션입니다. 다음 단계에 따라 이전에 만든 네임스페이스의 Azure Stack Edge 디바이스에 있는 Kubernetes 클러스터에 데이터 컨트롤러를 만듭니다.   

1. 데이터 컨트롤러를 만드는 데 필요한 다음 정보를 수집합니다.

    
    |열1  |Column2  |
    |---------|---------|
    |데이터 컨트롤러 이름     |데이터 컨트롤러를 설명하는 이름입니다. 예들 들어 `arctestdatacontroller`입니다.         |
    |데이터 컨트롤러 사용자 이름     |데이터 컨트롤러 관리자의 사용자 이름입니다. 데이터 컨트롤러 사용자 이름과 암호는 관리 함수를 수행하기 위해 데이터 컨트롤러 API를 인증하는 데 사용됩니다.          |
    |데이터 컨트롤러 암호     |데이터 컨트롤러 관리자의 암호입니다. 보안 암호를 선택하고 클러스터 관리자 권한이 필요한 암호를 사용하여 공유합니다.         |
    |Kubernetes 네임스페이스 이름     |데이터 컨트롤러를 만들려는 Kubernetes 네임스페이스의 이름입니다.         |
    |Azure 구독 ID입니다.     |Azure에서 데이터 컨트롤러 리소스를 만들려는 위치에 대한 Azure 구독 GUID입니다.         |
    |Azure 리소스 그룹 이름     |Azure에서 데이터 컨트롤러 리소스를 만들려는 리소스 그룹의 이름입니다.         |
    |Azure 위치     |데이터 컨트롤러 리소스 메타데이터가 Azure에 저장될 Azure 위치입니다. 사용 가능한 지역 목록은 Azure 글로벌 인프라/지역별 제품을 참조하세요.|


1. PowerShell 인터페이스에 연결하세요. 데이터 컨트롤러를 만들려면 다음을 입력합니다. 

    ```powershell
    Set-HcsKubernetesAzureArcDataController -SubscriptionId <Subscription ID> -ResourceGroupName <Resource group name> -Location <Location without spaces> -UserName <User you created> -Password <Password to authenticate to Data Controller> -DataControllerName <Data Controller Name> -Namespace <Namespace you created>    
    ```
    다음은 이전 명령의 샘플 출력입니다.

    ```powershell
    [10.100.10.10]: PS>Set-HcsKubernetesAzureArcDataController -SubscriptionId db4e2fdb-6d80-4e6e-b7cd-736098270664 -ResourceGroupName myasegpurg -Location "EastUS" -UserName myadsuser -Password "Password1" -DataControllerName "arctestcontroller" -Namespace myadstest
    [10.100.10.10]: PS> 
    ```
    
    배포를 완료하는 데 약 5분이 걸릴 수 있습니다.

    > [!NOTE]
    > Azure Stack Edge Pro GPU 디바이스의 Kubernetes 클러스터에서 만들어진 데이터 컨트롤러는 현재 릴리스의 연결 해제 모드에서만 작동합니다. 이 연결 해제 모드는 디바이스가 아닌 데이터 컨트롤러용입니다.

### <a name="monitor-data-creation-status"></a>데이터 만들기 상태 모니터링

1. 다른 PowerShell 창을 엽니다.
1. 다음 `kubectl` 명령을 사용하여 데이터 컨트롤러의 만들기 상태를 모니터링합니다. 

    ```powershell
    kubectl get datacontroller/<Data controller name> --namespace <Name of your namespace>
    ```
    컨트롤러가 만들어지면 `Ready` 상태여야 합니다.
    다음은 이전 명령의 샘플 출력입니다.

    ```powershell
    PS C:\WINDOWS\system32> kubectl get datacontroller/arctestcontroller --namespace myadstest
    NAME                STATE
    arctestcontroller   Ready
    PS C:\WINDOWS\system32>
    ```
1. 데이터 컨트롤러에서 실행 중인 외부 서비스에 할당된 IP를 식별하려면 `kubectl get svc -n <namespace>` 명령을 사용합니다. 샘플 출력은 다음과 같습니다.

    ```powershell
    PS C:\WINDOWS\system32> kubectl get svc -n myadstest
    NAME                      TYPE           CLUSTER-IP       EXTERNAL-IP   PORT(S)                                       AGE
    controldb-svc             ClusterIP      172.28.157.130   <none>        1433/TCP,8311/TCP,8411/TCP                    3d21h
    controller-svc            ClusterIP      172.28.123.251   <none>        443/TCP,8311/TCP,8301/TCP,8411/TCP,8401/TCP   3d21h
    controller-svc-external   LoadBalancer   172.28.154.30    10.57.48.63   30080:31090/TCP                               3d21h
    logsdb-svc                ClusterIP      172.28.52.196    <none>        9200/TCP,8300/TCP,8400/TCP                    3d20h
    logsui-svc                ClusterIP      172.28.85.97     <none>        5601/TCP,8300/TCP,8400/TCP                    3d20h
    metricsdb-svc             ClusterIP      172.28.255.103   <none>        8086/TCP,8300/TCP,8400/TCP                    3d20h
    metricsdc-svc             ClusterIP      172.28.208.191   <none>        8300/TCP,8400/TCP                             3d20h
    metricsui-svc             ClusterIP      172.28.158.163   <none>        3000/TCP,8300/TCP,8400/TCP                    3d20h
    mgmtproxy-svc             ClusterIP      172.28.228.229   <none>        443/TCP,8300/TCP,8311/TCP,8400/TCP,8411/TCP   3d20h
    mgmtproxy-svc-external    LoadBalancer   172.28.166.214   10.57.48.64   30777:30621/TCP                               3d20h
    sqlex-svc                 ClusterIP      None             <none>        1433/TCP                                      3d20h
    PS C:\WINDOWS\system32>
    ```

## <a name="deploy-sql-managed-instance"></a>SQL 관리되는 인스턴스 배포

데이터 컨트롤러를 성공적으로 만든 후 템플릿을 사용하여 데이터 컨트롤러에 SQL Managed Instance를 배포할 수 있습니다.

### <a name="deployment-template"></a>배포 템플릿

다음 배포 템플릿을 사용하여 디바이스의 데이터 컨트롤러에 SQL Managed Instance를 배포합니다.

```yml
apiVersion: v1
data:
    password: UGFzc3dvcmQx
    username: bXlhZHN1c2Vy
kind: Secret
metadata:
    name: sqlex-login-secret
type: Opaque
---
apiVersion: sql.arcdata.microsoft.com/v1alpha1
kind: sqlmanagedinstance
metadata:
    name: sqlex
spec:
    limits:
    memory: 4Gi
    vcores: "4"
    requests:
    memory: 2Gi
    vcores: "1"
    service:
    type: LoadBalancer
    storage:
    backups:
        className: ase-node-local
        size: 5Gi
    data:
        className: ase-node-local
        size: 5Gi
    datalogs:
        className: ase-node-local
        size: 5Gi
    logs:
        className: ase-node-local
        size: 1Gi
```


#### <a name="metadata-name"></a>메타데이터 이름
    
메타데이터 이름은 SQL Managed Instance의 이름입니다. 앞의 `deployment.yaml`에서 연관된 Pod의 이름은 `sqlex-n`입니다(`n`은 애플리케이션과 연관된 Pod의 수입니다). 
    
#### <a name="password-and-username-data"></a>암호 및 사용자 이름 데이터

데이터 컨트롤러 사용자 이름과 암호는 관리 함수를 수행하기 위해 데이터 컨트롤러 API를 인증하는 데 사용됩니다. 배포 템플릿의 데이터 컨트롤러 사용자 이름 및 암호에 대한 Kubernetes 비밀은 base64로 인코딩된 문자열입니다. 

원하는 사용자 이름과 암호를 Base64로 인코딩하기 위하여 온라인 도구를 사용하거나, 플랫폼에 따라 기본 제공되는 CLI 도구를 사용할 수 있습니다. 온라인 Base64 인코딩 도구를 사용할 때 도구에 사용자 이름과 암호 문자열(데이터 컨트롤러를 만드는 동안 입력됨)을 제공하면 도구가 해당 base64로 인코딩된 문자열을 만듭니다.
    
#### <a name="service-type"></a>서비스 유형

서비스 유형은 `LoadBalancer`로 설정되어야 합니다.
    
#### <a name="storage-class-name"></a>스토리지 클래스 이름

배포에서 데이터, 백업, 데이터 로그 및 로그에 사용할 Azure Stack Edge 디바이스의 스토리지 클래스를 식별할 수 있습니다. `kubectl get storageclass` 명령을 사용하여 디바이스에 배치된 스토리지 클래스를 가져옵니다.

```powershell
PS C:\WINDOWS\system32> kubectl get storageclass
NAME             PROVISIONER      RECLAIMPOLICY  VOLUMEBINDINGMODE     ALLOWVOLUMEEXPANSION   AGE
ase-node-local   rancher.io/local-path   Delete  WaitForFirstConsumer  false                  5d23h
PS C:\WINDOWS\system32>
```
앞의 샘플 출력에서 ​​디바이스의 스토리지 클래스 `ase-node-local`이 템플릿에 지정되어야 합니다.
 
#### <a name="spec"></a>사양

Azure Stack Edge 디바이스에서 SQL Managed Instance를 만들려면 `deployment.yaml`의 사양 섹션에서 메모리 및 CPU 요구 사항을 지정할 수 있습니다. 각 SQL Managed Instance는 다음 예와 같이 최소 2GB 메모리와 1개의 CPU 코어를 요청해야 합니다. 

```yml
spec:
    limits:
    memory: 4Gi
    vcores: "4"
    requests:
    memory: 2Gi
    vcores: "1"
```  

데이터 컨트롤러 및 1개의 SQL Managed Instance에 대한 자세한 크기 조정 지침은 [SQL Managed Instance 크기 조정 세부 정보](../azure-arc/data/sizing-guidance.md#sql-managed-instance-sizing-details)를 검토하세요.

### <a name="run-deployment-template"></a>배포 템플릿 실행

다음 명령을 사용하여 `deployment.yaml`을 실행합니다.

```powershell
kubectl create -n <Name of namespace that you created> -f <Path to the deployment yaml> 
```

다음 명령의 샘플 출력입니다.

```powershell
PS C:\WINDOWS\system32> kubectl get pods -n "myadstest"
No resources found.
PS C:\WINDOWS\system32> 
PS C:\WINDOWS\system32> kubectl create -n myadstest -f C:\azure-arc-data-services\sqlex.yaml  secret/sqlex-login-secret created
sqlmanagedinstance.sql.arcdata.microsoft.com/sqlex created
PS C:\WINDOWS\system32> kubectl get pods --namespace myadstest
NAME                 READY   STATUS    RESTARTS   AGE
bootstrapper-mv2cd   1/1     Running   0          83m
control-w9s9l        2/2     Running   0          78m
controldb-0          2/2     Running   0          78m
controlwd-4bmc5      1/1     Running   0          64m
logsdb-0             1/1     Running   0          64m
logsui-wpmw2         1/1     Running   0          64m
metricsdb-0          1/1     Running   0          64m
metricsdc-fb5r5      1/1     Running   0          64m
metricsui-56qzs      1/1     Running   0          64m
mgmtproxy-2ckl7      2/2     Running   0          64m
sqlex-0              3/3     Running   0          13m
PS C:\WINDOWS\system32>
```

샘플 출력의 `sqlex-0` Pod는 SQL Managed Instance의 상태를 나타냅니다.

## <a name="remove-data-controller"></a>데이터 컨트롤러 제거

데이터 컨트롤러를 제거하려면 배포한 전용 네임스페이스를 삭제합니다.


```powershell
kubectl delete ns <Name of your namespace>
```


## <a name="next-steps"></a>다음 단계

- [Azure Stack Edge Pro에 상태 비저장 애플리케이션을 배포합니다](./azure-stack-edge-gpu-deploy-stateless-application-kubernetes.md).
