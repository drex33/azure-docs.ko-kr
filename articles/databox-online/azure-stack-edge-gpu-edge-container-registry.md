---
title: Azure Stack Edge Pro GPU 디바이스에서 Edge 컨테이너 레지스트리 사용
description: Azure Stack Edge Pro GPU 디바이스에서 로컬 Edge 컨테이너 레지스트리를 사용하도록 설정하는 방법을 설명합니다.
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 02/22/2021
ms.author: alkohli
ms.openlocfilehash: 18fa68b6d0d0922bad0a632ba10bd82a8cfe8506
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128643026"
---
# <a name="enable-edge-container-registry-on-your-azure-stack-edge-pro-gpu-device"></a>Azure Stack Edge Pro GPU 디바이스에서 Edge 컨테이너 레지스트리 사용

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

이 문서에서는 Edge 컨테이너 레지스트리를 사용하도록 설정하고 Azure Stack Edge Pro 디바이스의 Kubernetes 클러스터 내에서 이를 사용하는 방법을 설명합니다. 이 문서에 사용된 예는 원본 레지스트리(이 경우 Microsoft 컨테이너 레지스트리)에서 Azure Stack Edge 디바이스의 레지스트리인 Edge 컨테이너 레지스트리로 이미지를 푸시하는 방법을 자세히 설명합니다.

### <a name="about-edge-container-registry"></a>Edge 컨테이너 레지스트리 정보

컨테이너화된 컴퓨팅 애플리케이션은 컨테이너 이미지에서 실행되며 이러한 이미지는 레지스트리에 저장됩니다. 레지스트리는 Docker Hub와 같은 공용, 비공개 또는 Azure 컨테이너 레지스트리와 같이 관리되는 클라우드 공급자일 수 있습니다. 자세한 내용은 [레지스트리, 리포지토리 및 이미지 정보](../container-registry/container-registry-concepts.md)를 참조하세요.

Edge 컨테이너 레지스트리는 Azure Stack Edge Pro 디바이스의 Edge에 리포지토리를 제공합니다. 이 레지스트리를 사용하여 프라이빗 컨테이너 이미지를 저장하고 관리할 수 ​​있습니다.

다중 노드 환경에서는 컨테이너 이미지를 다운로드하여 Edge 컨테이너 레지스트리에 한 번 푸시할 수 있습니다. 모든 Edge 애플리케이션은 후속 배포에 Edge 컨테이너 레지스트리를 사용할 수 있습니다.


## <a name="prerequisites"></a>필수 구성 요소

시작하기 전에 다음 사항을 확인합니다.

1. Azure Stack Edge Pro 디바이스에 액세스할 수 있습니다.

1. [Azure Stack Edge Pro 활성화](azure-stack-edge-gpu-deploy-activate.md)에 설명된 대로 Azure Stack Edge Pro 디바이스를 활성화했습니다.

1. 디바이스에서 컴퓨팅 역할을 사용하도록 설정했습니다. [Azure Stack Edge Pro 디바이스에서 컴퓨팅 구성](azure-stack-edge-gpu-deploy-configure-compute.md)의 지침에 따라 디바이스에서 컴퓨팅을 구성할 때 디바이스에 Kubernetes 클러스터도 만들었습니다.

1. 로컬 웹 UI의 **디바이스** 페이지에 Kubernetes API 엔드포인트가 있습니다. 자세한 내용은 [Kubernetes API 엔드포인트 가져오기](azure-stack-edge-gpu-deploy-configure-compute.md#get-kubernetes-endpoints)의 지침을 참조하세요.

1. [지원되는 운영 체제](azure-stack-edge-gpu-system-requirements.md#supported-os-for-clients-connected-to-device)가 있는 클라이언트 시스템에 액세스할 수 있습니다. Windows 클라이언트를 사용하는 경우 시스템은 PowerShell 5.0 이상을 실행하여 디바이스에 액세스해야 합니다.

    1. 자체 컨테이너 이미지를 가져와서 푸시하려면 시스템에 Docker 클라이언트가 설치되어 있는지 확인합니다. Windows 클라이언트를 사용하는 경우 [Windows에 Docker Desktop을 설치](https://docs.docker.com/docker-for-windows/install/)합니다.  


## <a name="enable-container-registry-as-add-on"></a>컨테이너 레지스트리를 추가 기능으로 사용하도록 설정

첫 번째 단계는 Edge 컨테이너 레지스트리를 추가 기능으로 사용하도록 설정하는 것입니다.

1. [디바이스의 PowerShell 인터페이스에 연결합니다](azure-stack-edge-gpu-connect-powershell-interface.md#connect-to-the-powershell-interface). 

1. 컨테이너 레지스트리를 추가 기능으로 사용하려면 다음을 입력합니다. 

    `Set-HcsKubernetesContainerRegistry`
    
    이 작업을 완료하는 데 몇 분 정도 걸릴 수 있습니다.

    다음은 이 명령의 샘플 출력입니다.    
            
    ```powershell
    [10.128.44.40]: PS>Set-HcsKubernetesContainerRegistry
    Operation completed successfully. Use Get-HcsKubernetesContainerRegistryInfo for credentials    
    ```
            
1. 컨테이너 레지스트리 세부 사항을 가져오려면 다음을 입력합니다.

    `Get-HcsKubernetesContainerRegistryInfo`

    다음은 이 명령의 샘플입니다.  
    
    ```powershell
    [10.128.44.40]: PS> Get-HcsKubernetesContainerRegistryInfo
                
    Endpoint                                   IPAddress    Username     Password
    --------                                   ---------    --------     --------
    ecr.dbe-hw6h1t2.microsoftdatabox.com:31001 10.128.44.41 ase-ecr-user i3eTsU4zGYyIgxV
    ```    

1. `Get-HcsKubernetesContainerRegistryInfo`의 출력에서 ​​사용자 이름과 암호를 기록해 둡니다. 이러한 자격 증명은 이미지를 푸시하는 동안 Edge 컨테이너 레지스트리에 로그인하는 데 사용됩니다.            


## <a name="manage-container-registry-images"></a>컨테이너 레지스트리 이미지 관리

Azure Stack Edge 디바이스 외부에서 컨테이너 레지스트리에 액세스할 수 있습니다. 레지스트리에서 이미지를 푸시하거나 가져올 수도 있습니다.

Edge 컨테이너 레지스트리에 액세스하려면 다음 단계를 따르세요.

1. Edge 컨테이너 레지스트리에 대한 엔드포인트 세부 정보를 가져옵니다.
    1. 디바이스의 로컬 UI에서 **디바이스** 로 이동합니다.
    1. **Edge 컨테이너 레지스트리 엔드포인트** 를 찾습니다.
        ![디바이스의 Edge 컨테이너 레지스트리 엔드포인트 페이지](media/azure-stack-edge-gpu-edge-container-registry/get-edge-container-registry-endpoint-1.png) 
    1. 이 엔드포인트를 복사하고 해당 DNS 항목을 클라이언트의 `C:\Windows\System32\Drivers\etc\hosts` 파일에 작성하여 Edge 컨테이너 레지스트리 엔드포인트에 연결합니다. 

        \<IP address of the Kubernetes main node\>    \<Edge container registry endpoint\> 
        
        ![Edge 컨테이너 레지스트리 엔드포인트에 대한 DNS 항목 추가](media/azure-stack-edge-gpu-edge-container-registry/add-domain-name-service-entry-hosts-1.png)    

1. 로컬 UI에서 Edge 컨테이너 레지스트리 인증서를 다운로드합니다. 
    1. 디바이스의 로컬 UI에서 **인증서** 로 이동합니다.
    1. **Edge 컨테이너 레지스트리 인증서** 항목을 찾습니다. 이 항목의 오른쪽에서 **다운로드** 를 선택하여 디바이스에 액세스하는 데 사용할 클라이언트 시스템에 Edge 컨테이너 레지스트리 인증서를 다운로드합니다. 

        ![Edge 컨테이너 레지스트리 엔드포인트 인증서 다운로드](media/azure-stack-edge-gpu-edge-container-registry/download-edge-container-registry-endpoint-certificate-1.png)  

1. 다운로드한 인증서를 클라이언트에 설치합니다. Windows 클라이언트를 사용하는 경우 다음 단계를 따르세요. 
    1. 인증서를 선택하고 **인증서 가져오기 마법사** 에서 저장소 위치를 **로컬 컴퓨터** 로 선택합니다. 

        ![인증서 설치 1](media/azure-stack-edge-gpu-edge-container-registry/install-certificate-1.png) 
    
    1. 신뢰할 수 있는 루트 저장소의 로컬 컴퓨터에 인증서를 설치합니다. 

        ![인증서 설치 2](media/azure-stack-edge-gpu-edge-container-registry/install-certificate-2.png) 

1. 인증서가 설치된 후 시스템에서 Docker 클라이언트를 다시 시작하세요.

1. Edge 컨테이너 레지스트리에 로그인합니다. 유형:

    `docker login <Edge container registry endpoint> -u <username> -p <password>`

    **디바이스** 페이지에서 Edge 컨테이너 레지스트리 엔드포인트를 제공하고 `Get-HcsKubernetesContainerRegistryInfo`의 출력에서 ​​얻은 사용자 이름 및 암호를 제공합니다. 

1. docker push 또는 pull 명령을 사용하여 컨테이너 레지스트리에서 컨테이너 이미지를 푸시하거나 가져옵니다.
 
    1. Microsoft Container Registry 이미지에서 이미지를 가져옵니다. 유형:
        
        `docker pull <Full path to the container image in the Microsoft Container Registry>`
       
    1. 레지스트리에 대한 정규화된 경로를 사용하여 풀한 이미지의 별칭을 만듭니다.

        `docker tag <Path to the image in the Microsoft container registry> <Path to the image in the Edge container registry/Image name with tag>`

    1. 레지스트리에 이미지를 밀어넣습니다.
    
        `docker push <Path to the image in the Edge container registry/Image name with tag>`

    1. 레지스트리에 푸시한 이미지를 실행합니다.
    
        `docker run -it --rm -p 8080:80 <Path to the image in the Edge container registry/Image name with tag>`

        다음은 pull 및 push 명령의 샘플 출력입니다.

        ```powershell
        PS C:\WINDOWS\system32> docker login ecr.dbe-hw6h1t2.microsoftdatabox.com:31001 -u ase-ecr-user -p 3bbo2sOtDe8FouD
        WARNING! Using --password via the CLI is insecure. Use --password-stdin.
        Login Succeeded
        PS C:\WINDOWS\system32> docker pull mcr.microsoft.com/oss/nginx/nginx:1.17.5-alpine
        1.17.5-alpine: Pulling from oss/nginx/nginx
        Digest: sha256:5466bbc0a989bd1cd283c0ba86d9c2fc133491ccfaea63160089f47b32ae973b
        Status: Image is up to date for mcr.microsoft.com/oss/nginx/nginx:1.17.5-alpine
        mcr.microsoft.com/oss/nginx/nginx:1.17.5-alpine
        PS C:\WINDOWS\system32> docker tag mcr.microsoft.com/oss/nginx/nginx:1.17.5-alpine ecr.dbe-hw6h1t2.microsoftdatabox.com:31001/nginx:2.0
        PS C:\WINDOWS\system32> docker push ecr.dbe-hw6h1t2.microsoftdatabox.com:31001/nginx:2.0
        The push refers to repository [ecr.dbe-hw6h1t2.microsoftdatabox.com:31001/nginx]
        bba7d2385bc1: Pushed
        77cae8ab23bf: Pushed
        2.0: digest: sha256:b4c0378c841cd76f0b75bc63454bfc6fe194a5220d4eab0d75963bccdbc327ff size: 739
        PS C:\WINDOWS\system32> docker run -it --rm -p 8080:80 ecr.dbe-hw6h1t2.microsoftdatabox.com:31001/nginx:2.0
        2020/11/10 00:00:49 [error] 6#6: *1 open() "/usr/share/nginx/html/favicon.ico" failed (2: No such file or directory), client: 172.17.0.1, server: localhost, request: "GET /favicon.ico HTTP/1.1", host: "localhost:8080", referrer: "http://localhost:8080/"
        172.17.0.1 - - [10/Nov/2020:00:00:49 +0000] "GET /favicon.ico HTTP/1.1" 404 555 "http://localhost:8080/" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/86.0.4240.183 Safari/537.36" "-"
        ^C
        PS C:\WINDOWS\system32>    
        ```
    
1. `http://localhost:8080`으로 이동하여 실행 중인 컨테이너를 봅니다. 이 경우 nginx 웹 서버가 실행 중인 것을 볼 수 있습니다.

    ![실행 중인 컨테이너 보기](media/azure-stack-edge-gpu-edge-container-registry/view-running-container-1.png)

    컨테이너를 중지하고 제거하려면 `Control+C`를 누릅니다.

 

## <a name="use-edge-container-registry-images-via-kubernetes-pods"></a>Kubernetes Pod를 통해 Edge 컨테이너 레지스트리 이미지 사용

이제 Kubernetes Pod 내에서 Edge 컨테이너 레지스트리에 푸시한 이미지를 배포할 수 있습니다.

1. 이미지를 배포하려면 *kubectl* 을 통해 클러스터 액세스를 구성해야 합니다. 네임스페이스, 사용자를 만들고 사용자에게 네임스페이스에 대한 액세스 권한을 부여하고 *구성* 파일을 가져옵니다. Kubernetes Pod에 연결할 수 있는지 확인하세요. 
    
    [Azure Stack Edge Pro GPU 디바이스에서 kubectl을 통해 Kubernetes 클러스터에 연결하고 관리](azure-stack-edge-gpu-create-kubernetes-cluster.md)의 모든 단계를 따릅니다. 

    다음은 사용자가 Kubernetes 클러스터에 액세스할 수 있는 디바이스의 네임스페이스에 대한 샘플 출력입니다.

    ```powershell
    [10.128.44.40]: PS>New-HcsKubernetesNamespace -Namespace myecr
    [10.128.44.40]: PS>New-HcsKubernetesUser -UserName ecruser
    apiVersion: v1
    clusters:
    - cluster:
        certificate-authority-data: LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUN5RENDQWJDZ0F3SUJBZ0lCQURBTkJna3Foa2lHOXcwQkFRc0ZBREFWTVJNd0VRWURWUVFERXdwcmRXSmwKY201bGRHVnpNQjRYRFRJd01URXdOVEF6TkRJek1Gb1hEVE13TVRFd016QXpOREl6TUZvd0ZURVRNQkVnNjOVRLWndCQ042cm1XQms2eXFwcXI1MUx6bApTaXMyTy91UEJ2YXNSSUUzdzgrbmEwdG1aTERZZ2F6MkQwMm42Q29mUmtyUTR2d1lLTnR1MlpzR3pUdz0KLS0tLS1FTkQgQ0VSVElGSUNBVEUtLS0tLQo=
        server: https://compute.dbe-hw6h1t2.microsoftdatabox.com:6443
        name: kubernetes
        ===================CUT=========================================CUT==============
        client-certificate-data: LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUMwRENDQWJpZ0F3SUJBZ0lJYmVWRGJSTzZ3ell3RFFZSktvWklodmNOQVFFTEJRQXdGVEVUTUJFR0ExVUUKQXhNS2EzVmlaWEp1WlhSbGN6QWVGdzB5TURFeE1EVXdNelF5TXpCYUZ3MHlNVEV4TURreU16UTRNal
        ===================CUT=========================================CUT==============
        DMVUvN3lFOG5UU3k3b2VPWitUeHdzCjF1UDByMjhDZ1lCdHdRY0ZpcFh1blN5ak16dTNIYjhveFI2V3VWWmZldFFKNElKWEFXOStVWGhKTFhyQ2x4bUcKWHRtbCt4UU5UTzFjQVNKRVZWVDd6Tjg2ay9kSU43S3JIVkdUdUxlUDd4eGVjV2VRcWJrZEVScUsxN0liTXpiVApmbnNxc0dobEdmLzdmM21kTGtyOENrcWs5TU5aM3MvUVIwRlFCdk94ZVpuUlpTeDVGbUR5S1E9PQotLS0tLUVORCBSU0EgUFJJVkFURSBLRVktLS0tLQo=

    [10.128.44.40]: PS>Grant-HcsKubernetesNamespaceAccess -Namespace myecr -UserName ecruser
    [10.128.44.40]: PS>kubectl get pods -n "myecr"
    No resources found.
    PS C:\WINDOWS\system32>
    ```  

2. 이미지 풀 비밀은 이미 디바이스의 모든 Kubernetes 네임스페이스에 설정되어 있습니다. `get secrets` 명령을 사용하여 비밀을 얻을 수 있습니다. 샘플 출력은 다음과 같습니다.

    ```powershell
    PS C:\WINDOWS\system32> .\kubectl.exe get secrets -n myecr
    NAME                  TYPE                                  DATA   AGE
    ase-ecr-credentials   kubernetes.io/dockerconfigjson        1      99m
    default-token-c7kww   kubernetes.io/service-account-token   3      107m
    sec-smbcredentials    microsoft.com/smb                     2      99m
    PS C:\WINDOWS\system32>   
    ```    

3. kubectl을 사용하여 네임스페이스에 Pod를 배포합니다. 다음 `yaml`을 사용합니다. 

    이미지 `<image-name>`를 컨테이너 레지스트리로 푸시된 이미지로 바꿉니다. 이름이 `ase-ecr-credentials`인 imagePullSecrets를 사용하여 네임스페이스의 비밀을 참조하세요.
    
    ```yml
    apiVersion: v1
    kind: Pod
    metadata:
      name: nginx
    spec:
      containers:
      - name: nginx
        image: ecr.dbe-hw6h1t2.microsoftdatabox.com:31001/nginx:2.0
        imagePullPolicy: Always
      imagePullSecrets:
      - name: ase-ecr-credentials
    ```

4. 적용 명령을 사용하여 생성한 네임스페이스에 배포를 적용합니다. 컨테이너가 실행 중인지 확인합니다. 샘플 출력은 다음과 같습니다.
   
    ```yml
    PS C:\Windows\System32> .\kubectl.exe apply -f .\deployment.yml -n myecr
    pod/nginx configured
    PS C:\Windows\System32> .\kubectl.exe get pods -n myecr
    NAME    READY   STATUS    RESTARTS   AGE
    nginx   1/1     Running   0          27m
    PS C:\Windows\System32>
    ```

## <a name="delete-container-registry-images"></a>컨테이너 레지스트리 이미지 삭제

Edge Container Registry 스토리지는 디바이스에서 사용 가능한 스토리지로 제한되는 Azure Stack Edge Pro 디바이스 내의 로컬 공유에서 호스팅됩니다. Docker HTTP v2 API(https://docs.docker.com/registry/spec/api/).NET)를 사용하여 컨테이너 레지스트리에서 사용하지 않는 Docker 이미지를 삭제하는 것은 사용자의 책임입니다.  

하나 이상의 컨테이너 이미지를 제거하려면 다음 단계를 따르세요.

1. 삭제할 이미지에 이미지 이름을 설정합니다.

    ```powershell
    PS C:\WINDOWS\system32> $imageName="nginx"    
    ```

1. 컨테이너 레지스트리의 사용자 이름과 암호를 PS 자격 증명으로 설정합니다.

    ```powershell
    PS C:\WINDOWS\system32> $username="ase-ecr-user"
    PS C:\WINDOWS\system32> $password="3bbo2sOtDe8FouD"
    PS C:\WINDOWS\system32> $securePassword = ConvertTo-SecureString $password -AsPlainText -Force
    PS C:\WINDOWS\system32> $credential = New-Object System.Management.Automation.PSCredential ($username, $securePassword)    
    ```

1. 이미지와 관련된 태그 나열

    ```powershell
    PS C:\WINDOWS\system32> $tags = Invoke-RestMethod -Credential $credential -Uri "https://ecr.dbe-hw6h1t2.microsoftdatabox.com:31001/v2/nginx/tags/list" | Select-Object -ExpandProperty tags
    PS C:\WINDOWS\system32> $tags
    2.0
    PS C:\WINDOWS\system32> $tags = Invoke-RestMethod -Credential $credential -Uri "https://ecr.dbe-hw6h1t2.microsoftdatabox.com:31001/v2/$imageName/tags/list" | Select-Object -ExpandProperty tags
    PS C:\WINDOWS\system32> $tags
    2.0
    PS C:\WINDOWS\system32>    
    ```
  
1. 삭제하려는 태그와 관련된 메시지 다이제스트를 나열합니다. 위 명령의 출력에서 ​​$tags가 사용됩니다. 여러 태그가 있는 경우 그중 하나를 선택하고 다음 명령에서 사용합니다.

    ```powershell
    PS C:\WINDOWS\system32> $response = Invoke-WebRequest -Method Head -Credential $credential -Uri "https://ecr.dbe-hw6h1t2.microsoftdatabox.com:31001/v2/$imageName/manifests/$tags" -Headers @{ 'Accept' = 'application/vnd.docker.distribution.manifest.v2+json' }
    PS C:\WINDOWS\system32> $digest = $response.Headers['Docker-Content-Digest']
    PS C:\WINDOWS\system32> $digest
    sha256:b4c0378c841cd76f0b75bc63454bfc6fe194a5220d4eab0d75963bccdbc327ff
    PS C:\WINDOWS\system32>    
    ```
1. image:tag의 메시지 다이제스트를 사용하여 이미지를 삭제합니다.

    ```powershell
    PS C:\WINDOWS\system32> Invoke-WebRequest -Method Delete -Credential $credential -Uri "https://ecr.dbe-hw6h1t2.microsoftdatabox.com:31001/v2/$imageName/manifests/$digest" | Select-Object -ExpandProperty StatusDescription    
    ```

사용하지 않는 이미지를 삭제하면 참조되지 않은 이미지와 연관된 공간이 야간에 실행되는 프로세스에 의해 자동으로 회수됩니다. 

## <a name="next-steps"></a>다음 단계

- [Azure Stack Edge Pro에 상태 비저장 애플리케이션을 배포합니다](./azure-stack-edge-gpu-deploy-stateless-application-kubernetes.md).