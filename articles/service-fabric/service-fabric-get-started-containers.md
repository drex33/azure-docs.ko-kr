---
title: Azure Service Fabric 컨테이너 애플리케이션 만들기
description: Azure Service Fabric에서 첫 번째 Windows 컨테이너 애플리케이션을 만듭니다. Python 애플리케이션을 사용하여 Docker 이미지를 빌드하고, 이미지를 컨테이너 레지스트리로 푸시한 다음 컨테이너를 빌드하고 Azure Service Fabric에 배포합니다.
ms.topic: conceptual
ms.date: 01/25/2019
ms.custom: devx-track-python
ms.openlocfilehash: fc819ea0141cac1305ca7a9c52d452b2f6e2961a
ms.sourcegitcommit: 838413a8fc8cd53581973472b7832d87c58e3d5f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/10/2021
ms.locfileid: "132137383"
---
# <a name="create-your-first-service-fabric-container-application-on-windows"></a>Windows에서 첫 번째 Service Fabric 컨테이너 애플리케이션 만들기

> [!div class="op_single_selector"]
> * [Windows](service-fabric-get-started-containers.md)
> * [Linux](service-fabric-get-started-containers-linux.md)

Service Fabric 클러스터의 Windows 컨테이너에서 기존 애플리케이션을 실행하더라도 애플리케이션을 변경할 필요가 없습니다. 이 문서에서는 Python [Flask](http://flask.pocoo.org/) 웹 애플리케이션을 포함하는 Docker 이미지를 만들어 Azure Service Fabric 클러스터에 배포하는 과정을 안내합니다. 또한 [Azure Container Registry](../container-registry/index.yml)를 통해 컨테이너화된 애플리케이션을 공유할 수도 있습니다. 이 문서에서는 Docker에 대한 기본적으로 이해하고 있다고 가정합니다. [Docker 개요](https://docs.docker.com/engine/understanding-docker/)를 참고하여 Docker에 대해 알아볼 수 있습니다.

> [!NOTE]
> 이 문서는 Windows 개발 환경에 적용됩니다.  Service Fabric 클러스터 런타임 및 Docker 런타임이 동일한 OS에서 실행되어야 합니다.  Windows 컨테이너는 Linux 클러스터에서 실행할 수 없습니다.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>사전 요구 사항

* 다음을 실행하는 개발 컴퓨터
  * Visual Studio 2015 또는 Visual Studio 2019.
  * [Service Fabric SDK 및 도구](service-fabric-get-started.md)
  *  Windows용 Docker [Windows용 Docker CE 가져오기(안정화)](https://store.docker.com/editions/community/docker-ce-desktop-windows?tab=description) Docker를 설치하고 시작한 후에 트레이 아이콘을 마우스 오른쪽 단추로 클릭하고 **Windows 컨테이너로 전환** 을 선택합니다. 이 단계는 Windows 기반의 Docker 이미지를 실행하는 데 필요합니다.

* 3개 이상의 노드가 있는 Windows 클러스터는 컨테이너가 포함된 Windows Server에서 실행됩니다. 

  이 문서의 경우 클러스터 노드에서 실행되는 컨테이너가 포함된 Windows Server의 버전은 개발 컴퓨터와 일치해야 합니다. 이는 개발 컴퓨터에 docker 이미지를 작성하고 컨테이너 OS 버전과 해당 OS가 배포된 호스트 OS 버전 간의 호환성 제약 조건이 있기 때문입니다. 자세한 내용은 [Windows Server 컨테이너 OS 및 호스트 OS 호환성](#windows-server-container-os-and-host-os-compatibility)을 참조하세요. 
  
    클러스터에 필요한 컨테이너가 포함된 Windows Server의 버전을 확인하려면 개발 컴퓨터의 Windows 명령 프롬프트에서 `ver` 명령을 실행합니다. [클러스터를 만들기](service-fabric-cluster-creation-via-portal.md) 전에 [Windows Server 컨테이너 OS 및 호스트 OS 호환성](#windows-server-container-os-and-host-os-compatibility)을 참조하세요.

* Azure Container Registry의 레지스트리 - Azure 구독 내에서 [컨테이너 레지스트리를 만듭니다](../container-registry/container-registry-get-started-portal.md).

> [!NOTE]
> Windows 10에서 실행 중인 Service Fabric 클러스터에 컨테이너 배포는 지원되지 않습니다.  Windows 컨테이너를 실행하도록 Windows 10을 구성하는 방법에 대한 정보는 [이 문서](service-fabric-how-to-debug-windows-containers.md)를 참조하세요.

> [!NOTE]
> Service Fabric 버전 6.2 이상은 Windows Server version 1709에서 실행 중인 클러스터에 컨테이너 배포를 지원합니다.

## <a name="define-the-docker-container"></a>Docker 컨테이너 정의

Docker 허브에 있는 [Python 이미지](https://hub.docker.com/_/python/)를 기반으로 하는 이미지를 빌드합니다.

Dockerfile에서 Docker 컨테이너를 지정합니다. Dockerfile은 컨테이너 내부 환경 설정, 실행하려는 애플리케이션 로드, 포트 매핑에 대한 지침으로 구성됩니다. Dockerfile는 `docker build` 명령에 대한 입력이며 이미지를 만듭니다.

빈 디렉터리를 만들고 *Dockerfile* 파일(파일 확장명 없음)을 만듭니다. *Dockerfile* 에 다음을 추가하고 변경 내용을 저장합니다.

```
# Use an official Python runtime as a base image
FROM python:2.7-windowsservercore

# Set the working directory to /app
WORKDIR /app

# Copy the current directory contents into the container at /app
ADD . /app

# Install any needed packages specified in requirements.txt
RUN pip install -r requirements.txt

# Make port 80 available to the world outside this container
EXPOSE 80

# Define environment variable
ENV NAME World

# Run app.py when the container launches
CMD ["python", "app.py"]
```

자세한 내용은 [Dockerfile 참조](https://docs.docker.com/engine/reference/builder/)를 참고하세요.

## <a name="create-a-basic-web-application"></a>기본 웹 애플리케이션 만들기
`Hello World!`를 반환하는 포트 80에서 수신 대기하는 Flask 웹 애플리케이션을 만듭니다. 동일한 디렉터리에서 *requirements.txt* 파일을 만듭니다. 다음을 추가하고 변경 내용을 저장합니다.
```
Flask
```

또한 *app.py* 파일을 만들고 다음 코드 조각을 추가합니다.

```python
from flask import Flask

app = Flask(__name__)


@app.route("/")
def hello():

    return 'Hello World!'


if __name__ == "__main__":
    app.run(host='0.0.0.0', port=80)
```

<a id="Build-Containers"></a>

## <a name="login-to-docker-and-build-the-image"></a>Docker에 로그인 및 이미지 빌드

다음으로 웹 애플리케이션을 실행하는 이미지를 만들어 보겠습니다. Docker에서 공용 이미지를 끌어올 때(예: Dockerfile의 `python:2.7-windowsservercore`) 익명 끌어오기 요청을 수행하는 것보다 Docker 허브 계정으로 인증하는 것이 가장 좋습니다.

> [!NOTE]
> 익명 끌어오기 요청을 자주 수행할 경우 `ERROR: toomanyrequests: Too Many Requests.` 또는 `You have reached your pull rate limit.`과 같은 Docker 오류가 표시될 수 있습니다. 이러한 오류가 발생하지 않도록 Docker Hub에 인증하세요. 자세한 내용은 [Azure Container Registry를 사용하여 공용 콘텐츠 관리](../container-registry/buffer-gate-public-content.md)를 참조하세요.

PowerShell 창을 열고 Dockerfile이 있는 디렉터리로 이동합니다. 그런 다음, 다음 명령을 실행합니다.

```
docker login
docker build -t helloworldapp .
```

이 명령은 Dockerfile에 있는 지침을 사용하여 새 이미지를 빌드하며 이미지의 이름을 `helloworldapp`으로 지정(-t 태그 지정)합니다. 컨테이너 이미지를 작성하기 위해 먼저 기본 이미지가 애플리케이션이 추가된 Docker 허브에서 다운로드됩니다. 

빌드 명령이 완료되면 `docker images` 명령을 실행하여 새 이미지에 대한 정보를 확인합니다.

```
$ docker images

REPOSITORY                    TAG                 IMAGE ID            CREATED             SIZE
helloworldapp                 latest              8ce25f5d6a79        2 minutes ago       10.4 GB
```

## <a name="run-the-application-locally"></a>애플리케이션을 로컬로 실행
컨테이너 레지스트리를 푸시하기 전에 먼저 로컬에서 이미지를 확인합니다. 

애플리케이션을 실행합니다.

```
docker run -d --name my-web-site helloworldapp
```

*name* 은 (컨테이너 ID가 아닌) 실행 중인 컨테이너에 이름을 지정합니다.

컨테이너가 시작되면 브라우저에서 실행 중인 컨테이너에 연결할 수 있도록 해당 IP 주소를 찾습니다.
```
docker inspect -f "{{ .NetworkSettings.Networks.nat.IPAddress }}" my-web-site
```

해당 명령이 아무 결과도 반환하지 않는 경우 다음 명령을 실행하고 IP 주소에 대한 **NetworkSettings**->**Networks** 요소를 검사합니다.
```
docker inspect my-web-site
```

실행 중인 컨테이너에 연결합니다. 반환된 IP 주소(예: "http:\//172.31.194.61")를 가리키는 웹 브라우저를 엽니다. 제목인 "Hello World!"가 브라우저에 표시됩니다.

컨테이너를 중지하려면 다음을 실행합니다.

```
docker stop my-web-site
```

개발 컴퓨터에서 컨테이너를 삭제합니다.

```
docker rm my-web-site
```

<a id="Push-Containers"></a>
## <a name="push-the-image-to-the-container-registry"></a>컨테이너 레지스트리에 이미지를 푸시합니다.

컨테이너가 개발 컴퓨터에서 실행되었는지 확인한 후에 Azure Container Registry에서 이미지를 레지스트리에 푸시합니다.

``docker login``을 실행하여 [레지스트리 자격 증명](../container-registry/container-registry-authentication.md)으로 컨테이너 레지스트리에 로그인합니다.

다음 예제는 Azure Active Directory [서비스 주체](../active-directory/develop/app-objects-and-service-principals.md)의 ID와 암호를 전달합니다. 예를 들어 자동화 시나리오를 위해 레지스트리에 서비스 주체를 할당할 수 있습니다. 또는 레지스트리 사용자 이름과 암호를 사용하여 로그인할 수 있습니다.

```
docker login myregistry.azurecr.io -u xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx -p myPassword
```

다음 명령은 레지스트리에 대한 정규화된 경로를 사용하여 이미지의 태그 또는 별칭을 만듭니다. 이 예제는 레지스트리의 루트에서 혼잡을 방지하기 위해 ```samples``` 네임스페이스에 이미지를 배치합니다.

```
docker tag helloworldapp myregistry.azurecr.io/samples/helloworldapp
```

이미지를 컨테이너 레지스트리에 푸시합니다.

```
docker push myregistry.azurecr.io/samples/helloworldapp
```

## <a name="create-the-containerized-service-in-visual-studio"></a>Visual Studio에서 컨테이너화된 서비스 만들기
Service Fabric SDK 및 도구에서는 Service Fabric 클러스터에 컨테이너를 배포할 수 있는 서비스 템플릿을 제공합니다.

1. Visual Studio를 시작합니다. **File** > **New** > **Project** 를 선택합니다.
2. **Service Fabric 애플리케이션** 을 선택하고 "MyFirstContainer"라는 이름을 지정하고 **확인** 을 클릭합니다.
3. **서비스 템플릿** 목록에서 **컨테이너** 를 선택합니다.
4. **이미지 이름** 에서 컨테이너 리포지토리에 푸시된 이미지인 "myregistry.azurecr.io/samples/helloworldapp"을 입력합니다.
5. 서비스에 이름을 지정하고 **확인** 을 클릭합니다.

## <a name="configure-communication"></a>통신 구성
컨테이너화된 서비스에는 통신을 위한 엔드포인트가 필요합니다. ServiceManifest.xml 파일에 프로토콜, 포트 및 형식이 포함된 `Endpoint` 요소를 추가합니다. 이 예제에서는 고정된 8081 포트가 사용됩니다. 포트를 지정하지 않으면 애플리케이션 포트 범위에서 임의의 포트가 선택됩니다. 

```xml
<Resources>
  <Endpoints>
    <Endpoint Name="Guest1TypeEndpoint" UriScheme="http" Port="8081" Protocol="http"/>
  </Endpoints>
</Resources>
```
> [!NOTE]
> 적용 가능한 속성 값으로 EndPoint 요소를 추가로 선언하여 서비스에 대한 추가 Endpoint를 추가할 수 있습니다. 각 포트는 프로토콜 값을 하나만 선언할 수 있습니다.

엔드포인트를 정의하면 Service Fabric에서 엔드포인트를 명명 서비스에 게시합니다. 클러스터에서 실행 중인 다른 서비스에서 이 컨테이너를 확인할 수 있습니다. [역방향 프록시](service-fabric-reverseproxy.md)를 사용하여 컨테이너-컨테이너 통신을 수행할 수도 있습니다. 통신은 역방향 프록시 HTTP 수신 대기 포트 및 통신하려는 서비스의 이름을 환경 변수로 제공하여 수행됩니다.

서비스는 특정 포트에 대해 수신 대기합니다(이 예제에서는 8081). 애플리케이션이 Azure에서 클러스터를 배포하는 경우 클러스터와 애플리케이션 모두 Azure 부하 분산 장치 뒤에서 실행됩니다. 애플리케이션 포트는 인바운드 트래픽이 서비스에 도달할 수 있도록 Azure 부하 분산 장치에서 열려 있어야 합니다.  [PowerShell 스크립트](./scripts/service-fabric-powershell-open-port-in-load-balancer.md) 또는 [Azure Portal](https://portal.azure.com)을 사용하여 Azure 부하 분산 장치에서 이 포트를 열 수 있습니다.

## <a name="configure-and-set-environment-variables"></a>환경 변수 구성 및 설정
서비스 매니페스트의 각 코드 패키지에 대해 환경 변수를 지정할 수 있습니다. 이 기능은 컨테이너 또는 프로세스 또는 게스트 실행 파일로 배포되는지 여부에 관계 없이 모든 서비스에 대해 사용할 수 있습니다. 애플리케이션 매니페스트에 환경 변수 값을 재정의하거나 애플리케이션 매개 변수로 배포하는 동안 지정할 수 있습니다.

다음 서비스 매니페스트 XML 코드 조각은 코드 패키지에 대한 환경 변수를 지정하는 방법의 예제를 보여줍니다.
```xml
<CodePackage Name="Code" Version="1.0.0">
  ...
  <EnvironmentVariables>
    <EnvironmentVariable Name="HttpGatewayPort" Value=""/>    
  </EnvironmentVariables>
</CodePackage>
```

이러한 환경 변수는 애플리케이션 매니페스트에서 재정의할 수 있습니다.

```xml
<ServiceManifestImport>
  <ServiceManifestRef ServiceManifestName="Guest1Pkg" ServiceManifestVersion="1.0.0" />
  <EnvironmentOverrides CodePackageRef="FrontendService.Code">
    <EnvironmentVariable Name="HttpGatewayPort" Value="19080"/>
  </EnvironmentOverrides>
  ...
</ServiceManifestImport>
```

## <a name="configure-container-port-to-host-port-mapping-and-container-to-container-discovery"></a>컨테이너 포트-호스트 포트 매핑 및 컨테이너-컨테이너 검색 구성
컨테이너와 통신하는 데 사용되는 호스트 포트를 구성합니다. 포트를 바인딩하면 서비스가 컨테이너 내에서 수신 대기 중인 포트를 호스트의 포트에 매핑합니다. ApplicationManifest.xml 파일의 `ContainerHostPolicies` 요소에 `PortBinding` 요소를 추가합니다. 이 문서에서 `ContainerPort`는 80(Dockerfile에서 지정된 대로 컨테이너에서 80 포트를 노출함)이고, `EndpointRef`는 "Guest1TypeEndpoint"(이전에 서비스 매니페스트에서 정의된 엔드포인트임)입니다. 8081 포트에서 서비스로 들어오는 요청은 컨테이너의 80 포트에 매핑됩니다.

```xml
<ServiceManifestImport>
    ...
    <Policies>
        <ContainerHostPolicies CodePackageRef="Code">
            <PortBinding ContainerPort="80" EndpointRef="Guest1TypeEndpoint"/>
        </ContainerHostPolicies>
    </Policies>
    ...
</ServiceManifestImport>
```
> [!NOTE]
> 적용 가능한 속성 값으로 PortBinding 요소를 추가로 선언하여 서비스에 대한 추가 PortBinding을 추가할 수 있습니다.

## <a name="configure-container-repository-authentication"></a>컨테이너 리포지토리 인증 구성

컨테이너 이미지 다운로드에 대해 다양한 유형의 인증을 구성하는 방법을 알아보려면 [컨테이너 리포지토리 인증](configure-container-repository-credentials.md)을 참조하세요.

## <a name="configure-isolation-mode"></a>격리 모드 구성
Windows는 컨테이너, 즉 프로세스 및 Hyper-V에 대한 두 가지 격리 모드를 지원합니다. 프로세스 격리 모드를 사용하여 동일한 호스트 컴퓨터에서 실행되는 모든 컨테이너는 호스트와 커널을 공유합니다. Hyper-V 격리 모드를 사용하여 커널은 각 Hyper-V 컨테이너와 컨테이너 호스트 간에 격리됩니다. 격리 모드는 애플리케이션 매니페스트 파일의 `ContainerHostPolicies` 요소에 지정됩니다. 지정될 수 있는 격리 모드는 `process`, `hyperv` 및 `default`입니다. 기본값은 Windows Server 호스트에서 프로세스 격리 모드입니다. Windows 10 호스트에서는 Hyper-V 격리 모드만 지원되므로 격리 모드 설정에 관계없이 컨테이너가 Hyper-V 격리 모드로 실행됩니다. 다음 코드 조각은 격리 모드가 애플리케이션 매니페스트 파일에서 지정되는 방법을 보여 줍니다.

```xml
<ContainerHostPolicies CodePackageRef="Code" Isolation="hyperv">
```
   > [!NOTE]
   > hyperv 격리 모드는 중첩된 가상화 지원을 포함하는 Ev3 및 Dv3 Azure SKU에서 사용할 수 있습니다. 
   >
   >

## <a name="configure-resource-governance"></a>리소스 관리 구성
[리소스 관리](service-fabric-resource-governance.md)는 호스트에서 사용할 수 있는 리소스를 컨테이너에서 제한합니다. 애플리케이션 매니페스트에 지정된 `ResourceGovernancePolicy` 요소는 서비스 코드 패키지에 대한 리소스 제한을 선언하는 데 사용됩니다. Memory, MemorySwap, CpuShares(CPU 상대적 가중치), MemoryReservationInMB, BlkioWeight(BlockIO 상대적 가중치) 리소스에 대한 리소스 제한을 설정할 수 있습니다. 이 예제에서는 Guest1Pkg 서비스 패키지가 배치된 클러스터 노드에서 하나의 코어를 가져옵니다. 메모리 제한은 절대값이므로 코드 패키지는 1,024MB의 메모리(및 동일한 값의 소프트 보증 예약)로 제한됩니다. 코드 패키지(컨테이너 또는 프로세스)는 이 제한보다 많은 메모리를 할당할 수 없으며, 할당하려고 하면 메모리 부족 예외가 발생합니다. 리소스 제한 적용이 작동하려면 서비스 패키지 내의 모든 코드 패키지에 메모리 제한이 지정되어 있어야 합니다.

```xml
<ServiceManifestImport>
  <ServiceManifestRef ServiceManifestName="Guest1Pkg" ServiceManifestVersion="1.0.0" />
  <Policies>
    <ServicePackageResourceGovernancePolicy CpuCores="1"/>
    <ResourceGovernancePolicy CodePackageRef="Code" MemoryInMB="1024"  />
  </Policies>
</ServiceManifestImport>
```
## <a name="configure-docker-healthcheck"></a>Docker HEALTHCHECK 구성 

v6.1을 시작하면 Service Fabric에서 자동으로 [Docker HEALTHCHECK](https://docs.docker.com/engine/reference/builder/#healthcheck) 이벤트를 시스템 상태 보고서에 통합합니다. 즉 컨테이너에 **HEALTHCHECK** 를 사용하도록 설정된 경우, Docker에서 보고한 대로 컨테이너의 상태가 변경될 때마다 Service Fabric에서 상태를 보고합니다. *health_status* 가 *healthy* 이면 [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md)에서 **OK** 상태 보고서가 표시되고, *health_status* 가 *unhealthy* 이면 **경고** 가 표시됩니다. 

최신 릴리스 v6.4부터는 docker HEALTHCHECK 평가를 오류로 보고하도록 지정할 수 있는 옵션이 있습니다. 이 옵션을 사용할 경우, *health_status* 가 *정상* 상태이면 **OK** 상태 보고서가 표시되고 *health_status* 가 *비정상* 상태이면 **ERROR** 가 표시됩니다.

컨테이너 상태를 모니터링하기 위해 수행되는 실제 검사를 가리키는 **HEALTHCHECK** 명령은 컨테이너 이미지를 생성하는 동안 사용되는 Dockerfile에 있어야 합니다.

![배포된 서비스 패키지 NodeServicePackage의 세부 정보를 보여 주는 스크린샷.][3]

![HealthCheckUnhealthyApp][4]

![HealthCheckUnhealthyDsp][5]

ApplicationManifest에서 **ContainerHostPolicies** 의 일부로 **HealthConfig** 옵션을 지정하여 각 컨테이너에 대한 **HEALTHCHECK** 동작을 구성할 수 있습니다.

```xml
<ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="ContainerServicePkg" ServiceManifestVersion="2.0.0" />
    <Policies>
      <ContainerHostPolicies CodePackageRef="Code">
        <HealthConfig IncludeDockerHealthStatusInSystemHealthReport="true"
              RestartContainerOnUnhealthyDockerHealthStatus="false" 
              TreatContainerUnhealthyStatusAsError="false" />
      </ContainerHostPolicies>
    </Policies>
</ServiceManifestImport>
```
기본적으로 *IncludeDockerHealthStatusInSystemHealthReport* 는 **true** 로 설정되고, *RestartContainerOnUnhealthyDockerHealthStatus* 는 **false** 로 설정되며, *TreatContainerUnhealthyStatusAsError* 는 **false** 로 설정됩니다. 

*RestartContainerOnUnhealthyDockerHealthStatus* 가 **true** 로 설정된 경우, 반복적으로 비정상으로 보고하는 컨테이너가 다시 시작됩니다(다른 노드에서도 가능).

*TreatContainerUnhealthyStatusAsError* 가 **true** 로 설정된 경우 컨테이너의 *health_status* 가 *비정상* 일 때 **ERROR** 상태 보고서가 표시됩니다.

전체 Service Fabric 클러스터에 대해 **HEALTHCHECK** 통합을 사용하지 않도록 설정하려면 [EnableDockerHealthCheckIntegration](service-fabric-cluster-fabric-settings.md)을 **false** 로 설정해야 합니다.

## <a name="deploy-the-container-application"></a>컨테이너 애플리케이션 배포
모든 변경 내용을 저장하고 애플리케이션을 빌드합니다. 애플리케이션을 게시하려면 [솔루션 탐색기]에서 **MyFirstContainer** 를 마우스 오른쪽 단추로 클릭하고 **게시** 를 선택합니다.

**연결 엔드포인트** 에서 클러스터에 대한 관리 엔드포인트을 입력합니다. 예들 들어 `containercluster.westus2.cloudapp.azure.com:19000`입니다. [Azure Portal](https://portal.azure.com)에 있는 클러스터의 개요 탭에서 클라이언트 연결 엔드포인트를 찾을 수 있습니다.

**게시** 를 클릭합니다.

[Service Fabric Explorer](service-fabric-visualizing-your-cluster.md)는 Service Fabric 클러스터에서 애플리케이션 및 노드를 검사 및 관리하기 위한 웹 기반 도구입니다. 브라우저를 열고 `http://containercluster.westus2.cloudapp.azure.com:19080/Explorer/`로 이동하여 애플리케이션 배포를 수행합니다. 애플리케이션이 배포되지만 이미지가 클러스터 노드에 다운로드될 때까지 오류 상태입니다(이미지 크기에 따라 약간의 시간이 걸릴 수 있음). ![오류][1]

애플리케이션이 ```Ready``` 상태이면 사용할 준비가 되었습니다. ![준비][2]

브라우저를 열고 `http://containercluster.westus2.cloudapp.azure.com:8081` 로 이동합니다. 제목인 "Hello World!"가 브라우저에 표시됩니다.

## <a name="clean-up"></a>정리

클러스터가 실행되는 동안 요금이 계속 청구되므로 [클러스터를 삭제](./service-fabric-tutorial-delete-cluster.md)하는 것이 좋습니다.

이미지를 컨테이너 레지스트리에 푸시한 후에 개발 컴퓨터에서 로컬 이미지를 삭제할 수 있습니다.

```
docker rmi helloworldapp
docker rmi myregistry.azurecr.io/samples/helloworldapp
```

## <a name="windows-server-container-os-and-host-os-compatibility"></a>Windows Server 컨테이너 OS 및 호스트 OS 호환성

Windows Server 컨테이너는 일부 버전의 호스트 OS에서 호환되지 않습니다. 예를 들면 다음과 같습니다.
 
- Windows Server 버전 1709를 사용하여 빌드된 Windows Server 컨테이너는 Windows Server 버전 2016을 실행하는 호스트에서 작동하지 않습니다. 
- Windows Server 버전 2016을 사용하여 빌드된 Windows Server 컨테이너는 Windows Server 버전 1709를 실행하는 호스트에서 HyperV 격리 모드로만 작동합니다. 
- Windows Server 2016을 사용하여 빌드된 Windows Server 컨테이너는 Windows Server 2016을 실행하는 호스트에서 프로세스 격리 모드로 실행할 때 컨테이너 OS와 호스트 OS의 수정 버전이 동일한지 확인해야 할 수 있습니다.
 
자세한 내용은 [Windows 컨테이너 버전 호환성](/virtualization/windowscontainers/deploy-containers/version-compatibility)을 참조하세요.

Service Fabric 클러스터에 컨테이너를 배포할 때 호스트 OS와 컨테이너 OS의 호환성을 고려해야 합니다. 예를 들면 다음과 같습니다.

- OS가 클러스터 노드의 OS와 호환되는 컨테이너를 배포해야 합니다.
- 컨테이너 앱에 대해 지정된 격리 모드가 배포 중인 노드의 컨테이너 OS에 대한 지원과 일치하는지 확인합니다.
- 클러스터 노드 또는 컨테이너에 대한 OS 업그레이드가 호환성에 영향을 미칠 수 있는 방법을 고려합니다. 

다음 사례로 Service Fabric 클러스터에서 컨테이너가 올바르게 배포되는지 확인하는 것이 좋습니다.

- Docker 이미지와 함께 명시적 이미지 태그 지정을 사용하여 컨테이너가 빌드된 Windows Server OS의 버전을 지정합니다. 
- 애플리케이션 매니페스트 파일에서 [OS 태그 지정](#specify-os-build-specific-container-images)을 사용하여 애플리케이션이 여러 Windows Server 버전 및 업그레이드에서 호환되는지 확인합니다.

> [!NOTE]
> Service Fabric 버전 6.2 이상의 경우 Windows 10 호스트에서 로컬로 Windows Server 2016 기반의 컨테이너를 배포할 수 있습니다. Windows 10에서 컨테이너는 애플리케이션 매니페스트에 설정된 격리 모드에 관계없이 Hyper-V 격리 모드로 실행됩니다. 자세한 내용은 [격리 모드 구성](#configure-isolation-mode)을 참조하세요.   
>
 
## <a name="specify-os-build-specific-container-images"></a>OS 빌드 관련 컨테이너 이미지 지정 

Windows Server 컨테이너는 여러 OS 버전에서 호환되지 않을 수 있습니다. 예를 들어 Windows Server 2016을 사용하여 빌드된 Windows Server 컨테이너는 Windows Server 버전 1709에서 프로세스 격리 모드로 작동하지 않습니다. 따라서 클러스터 노드가 최신 버전으로 업데이트되는 경우 OS의 이전 버전을 사용하여 빌드된 컨테이너 서비스는 실패할 수 있습니다. 런타임의 버전 6.1 및 새로운 버전으로 이 문제를 우회하기 위해 Service Fabric은 컨테이너당 여러 OS 이미지를 지정하고, 애플리케이션 매니페스트에서 OS의 빌드 버전으로 태그를 지정하는 것을 지원합니다. Windows 명령 프롬프트에서 `winver`를 실행하여 OS 빌드 버전을 가져올 수 있습니다. 노드에서 OS를 업데이트하기 전에 애플리케이션 매니페스트를 업데이트하고 OS 버전별로 이미지 재정의를 지정합니다. 다음 코드 조각에서는 애플리케이션 매니페스트 **ApplicationManifest.xml** 에 여러 컨테이너 이미지를 지정하는 방법을 보여 줍니다.


```xml
      <ContainerHostPolicies> 
         <ImageOverrides> 
           <Image Name="myregistry.azurecr.io/samples/helloworldappDefault" /> 
               <Image Name="myregistry.azurecr.io/samples/helloworldapp1701" Os="14393" /> 
               <Image Name="myregistry.azurecr.io/samples/helloworldapp1709" Os="16299" /> 
         </ImageOverrides> 
      </ContainerHostPolicies> 
```
Windows Server 2016 빌드 버전은 14393이고 Windows Server 버전 1709의 빌드 버전은 16299입니다. 서비스 매니페스트는 다음과 같이 컨테이너 서비스당 하나의 이미지만 계속 지정합니다.

```xml
<ContainerHost>
    <ImageName>myregistry.azurecr.io/samples/helloworldapp</ImageName> 
</ContainerHost>
```

   > [!NOTE]
   > OS 빌드 버전 태깅 기능은 Windows에서 Service Fabric에만 사용할 수 있습니다.
   >

VM에서 기본 OS가 16299(버전 1709)에 빌드되면 Service Fabric은 해당 Windows Server 버전에 해당하는 컨테이너 이미지를 선택합니다. 애플리케이션 매니페스트에서 태그가 지정되지 않은 컨테이너 이미지가 태그가 지정된 컨테이너 이미지와 함께 제공되는 경우, Service Fabric은 태그가 지정되지 않은 이미지를 버전 간 작동하는 것으로 처리합니다. 업그레이드하는 동안 문제가 발생하지 않도록 컨테이너 이미지에 명시적으로 태그를 지정합니다.

태그가 지정되지 않은 컨테이너 이미지는 ServiceManifest에 제공되는 이미지에 대한 재정의로 작동합니다. 따라서 이미지 "myregistry.azurecr.io/samples/helloworldappDefault"는 ServiceManifest의 ImageName "myregistry.azurecr.io/samples/helloworldapp"을 재정의합니다.

## <a name="complete-example-service-fabric-application-and-service-manifests"></a>Service Fabric 애플리케이션 및 서비스 매니페스트의 전체 예제
이 문서에서 사용한 전체 서비스 및 애플리케이션 매니페스트는 다음과 같습니다.

### <a name="servicemanifestxml"></a>ServiceManifest.xml
```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest Name="Guest1Pkg"
                 Version="1.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric"
                 xmlns:xsd="https://www.w3.org/2001/XMLSchema"
                 xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance">
  <ServiceTypes>
    <!-- This is the name of your ServiceType.
         The UseImplicitHost attribute indicates this is a guest service. -->
    <StatelessServiceType ServiceTypeName="Guest1Type" UseImplicitHost="true" />
  </ServiceTypes>

  <!-- Code package is your service executable. -->
  <CodePackage Name="Code" Version="1.0.0">
    <EntryPoint>
      <!-- Follow this link for more information about deploying Windows containers to Service Fabric: https://aka.ms/sfguestcontainers -->
      <ContainerHost>
        <ImageName>myregistry.azurecr.io/samples/helloworldapp</ImageName>
        <!-- Pass comma delimited commands to your container: dotnet, myproc.dll, 5" -->
        <!--Commands> dotnet, myproc.dll, 5 </Commands-->
        <Commands></Commands>
      </ContainerHost>
    </EntryPoint>
    <!-- Pass environment variables to your container: -->    
    <EnvironmentVariables>
      <EnvironmentVariable Name="HttpGatewayPort" Value=""/>
      <EnvironmentVariable Name="BackendServiceName" Value=""/>
    </EnvironmentVariables>

  </CodePackage>

  <!-- Config package is the contents of the Config directory under PackageRoot that contains an
       independently-updateable and versioned set of custom configuration settings for your service. -->
  <ConfigPackage Name="Config" Version="1.0.0" />

  <Resources>
    <Endpoints>
      <!-- This endpoint is used by the communication listener to obtain the port on which to
           listen. Please note that if your service is partitioned, this port is shared with
           replicas of different partitions that are placed in your code. -->
      <Endpoint Name="Guest1TypeEndpoint" UriScheme="http" Port="8081" Protocol="http"/>
    </Endpoints>
  </Resources>
</ServiceManifest>
```
### <a name="applicationmanifestxml"></a>ApplicationManifest.xml
```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest ApplicationTypeName="MyFirstContainerType"
                     ApplicationTypeVersion="1.0.0"
                     xmlns="http://schemas.microsoft.com/2011/01/fabric"
                     xmlns:xsd="https://www.w3.org/2001/XMLSchema"
                     xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance">
  <Parameters>
    <Parameter Name="Guest1_InstanceCount" DefaultValue="-1" />
  </Parameters>
  <!-- Import the ServiceManifest from the ServicePackage. The ServiceManifestName and ServiceManifestVersion
       should match the Name and Version attributes of the ServiceManifest element defined in the
       ServiceManifest.xml file. -->
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="Guest1Pkg" ServiceManifestVersion="1.0.0" />
    <EnvironmentOverrides CodePackageRef="FrontendService.Code">
      <EnvironmentVariable Name="HttpGatewayPort" Value="19080"/>
    </EnvironmentOverrides>
    <ConfigOverrides />
    <Policies>
      <ContainerHostPolicies CodePackageRef="Code">
        <RepositoryCredentials AccountName="myregistry" Password="MIIB6QYJKoZIhvcNAQcDoIIB2jCCAdYCAQAxggFRMIIBTQIBADA1MCExHzAdBgNVBAMMFnJ5YW53aWRhdGFlbmNpcGhlcm1lbnQCEFfyjOX/17S6RIoSjA6UZ1QwDQYJKoZIhvcNAQEHMAAEg
gEAS7oqxvoz8i6+8zULhDzFpBpOTLU+c2mhBdqXpkLwVfcmWUNA82rEWG57Vl1jZXe7J9BkW9ly4xhU8BbARkZHLEuKqg0saTrTHsMBQ6KMQDotSdU8m8Y2BR5Y100wRjvVx3y5+iNYuy/JmM
gSrNyyMQ/45HfMuVb5B4rwnuP8PAkXNT9VLbPeqAfxsMkYg+vGCDEtd8m+bX/7Xgp/kfwxymOuUCrq/YmSwe9QTG3pBri7Hq1K3zEpX4FH/7W2Zb4o3fBAQ+FuxH4nFjFNoYG29inL0bKEcTX
yNZNKrvhdM3n1Uk/8W2Hr62FQ33HgeFR1yxQjLsUu800PrYcR5tLfyTB8BgkqhkiG9w0BBwEwHQYJYIZIAWUDBAEqBBBybgM5NUV8BeetUbMR8mJhgFBrVSUsnp9B8RyebmtgU36dZiSObDsI
NtTvlzhk11LIlae/5kjPv95r3lw6DHmV4kXLwiCNlcWPYIWBGIuspwyG+28EWSrHmN7Dt2WqEWqeNQ==" PasswordEncrypted="true"/>
        <PortBinding ContainerPort="80" EndpointRef="Guest1TypeEndpoint"/>
      </ContainerHostPolicies>
      <ServicePackageResourceGovernancePolicy CpuCores="1"/>
      <ResourceGovernancePolicy CodePackageRef="Code" MemoryInMB="1024"  />
    </Policies>
  </ServiceManifestImport>
  <DefaultServices>
    <!-- The section below creates instances of service types, when an instance of this
         application type is created. You can also create one or more instances of service type using the
         ServiceFabric PowerShell module.

         The attribute ServiceTypeName below must match the name defined in the imported ServiceManifest.xml file. -->
    <Service Name="Guest1">
      <StatelessService ServiceTypeName="Guest1Type" InstanceCount="[Guest1_InstanceCount]">
        <SingletonPartition />
      </StatelessService>
    </Service>
  </DefaultServices>
</ApplicationManifest>
```

## <a name="configure-time-interval-before-container-is-force-terminated"></a>컨테이너를 강제로 종료하기 전 시간 간격 구성

서비스 삭제(또는 다른 노드로 이동)가 시작된 후 컨테이너가 제거되기 전에 대기할 런타임 시간 간격을 구성할 수 있습니다. `docker stop <time in seconds>` 명령을 컨테이너로 보내는 시간 간격 구성.  자세한 내용은 [docker stop](https://docs.docker.com/engine/reference/commandline/stop/)을 참조하세요. 대기할 시간 간격은 `Hosting` 섹션 아래에 지정됩니다. `Hosting` 섹션은 클러스터를 만들 때 또는 나중에 구성 업그레이드에서 추가할 수 있습니다. 다음 클러스터 매니페스트 코드 조각은 대기 간격을 설정하는 방법을 보여 줍니다.

```json
"fabricSettings": [
    ...,
    {
        "name": "Hosting",
        "parameters": [
          {
                "name": "ContainerDeactivationTimeout",
                "value" : "10"
          },
          ...
        ]
    }
]
```
기본 시간 간격은 10초로 설정됩니다. 이 구성은 동적이므로 클러스터에 대한 구성 전용 업그레이드만 시간 제한을 업데이트합니다. 


## <a name="configure-the-runtime-to-remove-unused-container-images"></a>사용하지 않는 컨테이너 이미지를 제거할 런타임 구성

노드에서 사용하지 않는 컨테이너 이미지를 제거하기 위해 Service Fabric 클러스터를 구성할 수 있습니다. 이 구성을 통해 노드에 너무 많은 컨테이너 이미지가 있는 경우 디스크 공간을 다시 캡처할 수 있습니다. 이 기능을 사용하려면 다음 코드 조각에 표시된 것처럼 클러스터 매니페스트에서 [Hosting](service-fabric-cluster-fabric-settings.md#hosting) 섹션을 업데이트합니다. 


```json
"fabricSettings": [
    ...,
    {
        "name": "Hosting",
        "parameters": [
          {
                "name": "PruneContainerImages",
                "value": "True"
          },
          {
                "name": "ContainerImagesToSkip",
                "value": "mcr.microsoft.com/windows/servercore|mcr.microsoft.com/windows/nanoserver|mcr.microsoft.com/dotnet/framework/aspnet|..."
          }
          ...
          }
        ]
    } 
]
```

삭제하지 말아야 하는 이미지의 경우 `ContainerImagesToSkip` 매개 변수 아래에 지정할 수 있습니다.  


## <a name="configure-container-image-download-time"></a>컨테이너 이미지 다운로드 시간 구성

Service Fabric 런타임은 대부분의 컨테이너 이미지에 대해 작동하는 컨테이너 이미지를 다운로드하고 추출하는 데 20분을 할당합니다. 큰 이미지의 경우 또는 네트워크 연결이 느린 경우, 이미지 다운로드 및 추출을 중단하기 전에 기다리는 시간을 늘려야 할 수 있습니다. 이 시간 제한은 다음 코드 조각과 같이 클러스터 매니페스트의 **Hosting** 섹션에 있는 **ContainerImageDownloadTimeout** 특성을 사용하여 설정합니다.

```json
"fabricSettings": [
    ...,
    {
        "name": "Hosting",
        "parameters": [
          {
              "name": "ContainerImageDownloadTimeout",
              "value": "1200"
          }
        ]
    }
]
```


## <a name="set-container-retention-policy"></a>컨테이너 보존 정책 설정

컨테이너 시작 오류 진단을 지원하기 위해 Service Fabric(버전 6.1 이상)은 종료되었거나 시작하지 못한 컨테이너를 보존하도록 지원합니다. 이 정책은 다음 코드 조각과 같이 **ApplicationManifest.xml** 파일에서 설정할 수 있습니다.

```xml
 <ContainerHostPolicies CodePackageRef="NodeService.Code" Isolation="process" ContainersRetentionCount="2"  RunInteractive="true"> 
```

**ContainersRetentionCount** 설정은 실패할 때 유지할 컨테이너의 수를 지정합니다. 음수 값을 지정하면 실패한 모든 컨테이너가 유지됩니다. **ContainersRetentionCount** 특성을 지정하지 않으면 컨테이너는 유지되지 않습니다. 또한 **ContainersRetentionCount** 특성은 애플리케이션 매개 변수도 지원하므로 사용자는 테스트 및 프로덕션 클러스터에 대해 다른 값을 지정할 수 있습니다. 이 기능을 사용하여 컨테이너 서비스가 다른 노드로 이동하지 않도록 방지하려면 배치 제약 조건을 사용하여 특정 노드에 대한 컨테이너 서비스를 대상으로 지정합니다. 이 기능을 사용하여 유지된 컨테이너는 수동으로 제거해야 합니다.

## <a name="start-the-docker-daemon-with-custom-arguments"></a>사용자 지정 인수로 Docker 디먼 시작

6.2 버전 이상의 Service Fabric 런타임에서는 사용자 지정 인수로 Docker 디먼을 시작할 수 있습니다. 사용자 지정 인수가 지정되면 Service Fabric은 Docker 엔진에 `--pidfile` 인수를 제외한 다른 인수를 전달하지 않습니다. 따라서, `--pidfile`을 인수로 전달하지 말아야 합니다. 또한, 인수는 Service Fabric이 디먼과 통신할 수 있도록 Docker 디먼이 Windows의 기본 이름 파이프(또는 Linux의 UNIX 도메인 소켓)를 수신 대기하도록 해야 합니다. 사용자 지정 인수는 다음 코드 조각과 같이 **ContainerServiceArguments** 의 **Hosting** 섹션 아래에 있는 클러스터 매니페스트에 전달됩니다. 
 

```json
"fabricSettings": [
    ...,
    { 
        "name": "Hosting", 
        "parameters": [ 
          { 
            "name": "ContainerServiceArguments", 
            "value": "-H localhost:1234 -H unix:///var/run/docker.sock" 
          } 
        ] 
    } 
]
```

## <a name="entrypoint-override"></a>EntryPoint 재정의
8.2 버전의 ServiceFabric Runtime을 사용 하 여 **컨테이너** 및 **exe 호스트** 코드 패키지에 대 한 entrypoint를 재정의할 수 있습니다. 모든 매니페스트 요소가 동일 하 게 유지 되지만 컨테이너 이미지를 변경 해야 하는 경우에는 다른 앱 형식 버전을 프로 비전 하는 것이 더 이상 필요 하지 않으며 진입점은 동일 하 게 유지 되는 경우에 사용할 수 있습니다.

컨테이너 진입점을 재정의 하는 방법에 대 한 예제는 다음과 같습니다.

### <a name="applicationmanifestxml"></a>ApplicationManifest.xml
```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest ApplicationTypeName="MyFirstContainerType"
                     ApplicationTypeVersion="1.0.0"
                     xmlns="http://schemas.microsoft.com/2011/01/fabric"
                     xmlns:xsd="https://www.w3.org/2001/XMLSchema"
                     xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance">
  <Parameters>
    <Parameter Name="ImageName" DefaultValue="myregistry.azurecr.io/samples/helloworldapp" />
    <Parameter Name="Commands" DefaultValue="commandsOverride" />
    <Parameter Name="FromSource" DefaultValue="sourceOverride" />
    <Parameter Name="EntryPoint" DefaultValue="entryPointOverride" />
  </Parameters>
  <!-- Import the ServiceManifest from the ServicePackage. The ServiceManifestName and ServiceManifestVersion
       should match the Name and Version attributes of the ServiceManifest element defined in the
       ServiceManifest.xml file. -->
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="Guest1Pkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <Policies>
      <CodePackagePolicy CodePackageRef="Code">
        <EntryPointOverride>
         <ContainerHostOverride>
            <ImageOverrides>
              <Image Name="[ImageName]" />
            </ImageOverrides>
            <Commands>[Commands]</Commands>
            <FromSource>[Source]</FromSource>
            <EntryPoint>[EntryPoint]</EntryPoint>
          </ContainerHostOverride>
        </EntryPointOverride>
      </CodePackagePolicy>
    </Policies>
  </ServiceManifestImport>
</ApplicationManifest>
```
### <a name="servicemanifestxml"></a>ServiceManifest.xml
```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest Name="Guest1Pkg"
                 Version="1.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric"
                 xmlns:xsd="https://www.w3.org/2001/XMLSchema"
                 xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance">
  <ServiceTypes>
    <!-- This is the name of your ServiceType.
         The UseImplicitHost attribute indicates this is a guest service. -->
    <StatelessServiceType ServiceTypeName="Guest1Type" UseImplicitHost="true" />
  </ServiceTypes>

  <!-- Code package is your service executable. -->
  <CodePackage Name="Code" Version="1.0.0">
    <EntryPoint>
      <!-- Follow this link for more information about deploying Windows containers to Service Fabric: https://aka.ms/sfguestcontainers -->
      <ContainerHost>
        <ImageName>default imagename</ImageName>
        <Commands>default cmd</Commands>
        <EntryPoint>default entrypoint</EntryPoint>
        <FromSource>default source</FromSource>
      </ContainerHost>
    </EntryPoint>
  </CodePackage>

  <ConfigPackage Name="Config" Version="1.0.0" />
</ServiceManifest>
```
응용 프로그램 매니페스트의 재정의를 지정 하면 이미지 이름이 myregistry.azurecr.io/samples/helloworldapp, command commandsOverride, source sourceOverride 및 entryPoint entryPointOverride 인 컨테이너가 시작 됩니다.

마찬가지로 **ExeHost** 를 재정의 하는 방법에 대 한 예제는 다음과 같습니다.
```xml
<?xml version="1.0" encoding="utf-8"?>
<Policies>
  <CodePackagePolicy CodePackageRef="Code">
    <EntryPointOverride>
      <ExeHostOverride>
        <Program>[Program]</Program>
        <Arguments>[Entry]</Arguments>
      </ExeHostOverride>
    </EntryPointOverride>
  </CodePackagePolicy>
</Policies>
```
> [!NOTE]
> 진입점 재정의가 SetupEntryPoint에 대해 지원 되지 않습니다.

## <a name="next-steps"></a>다음 단계
* [Service Fabric의 컨테이너](service-fabric-containers-overview.md)를 실행하는 방법에 대해 자세히 알아봅니다.
* [컨테이너에서 .NET 애플리케이션 배포](service-fabric-host-app-in-a-container.md) 자습서를 참조합니다.
* Service Fabric [애플리케이션 수명 주기](service-fabric-application-lifecycle.md)에 대해 자세히 알아봅니다.
* GitHub에서 [Service Fabric 컨테이너 코드 샘플](https://github.com/Azure-Samples/service-fabric-containers)을 확인합니다.

[1]: ./media/service-fabric-get-started-containers/MyFirstContainerError.png
[2]: ./media/service-fabric-get-started-containers/MyFirstContainerReady.png
[3]: ./media/service-fabric-get-started-containers/HealthCheckHealthy.png
[4]: ./media/service-fabric-get-started-containers/HealthCheckUnhealthy_App.png
[5]: ./media/service-fabric-get-started-containers/HealthCheckUnhealthy_Dsp.png
