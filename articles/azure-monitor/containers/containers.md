---
title: Azure Monitor의 VMware 모니터링 솔루션 | Microsoft Docs
description: Log Analytics의 컨테이너 모니터링 솔루션을 사용하여 단일 위치에서 Docker 및 Windows 컨테이너 호스트를 보고 관리할 수 있습니다.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/06/2020
ms.openlocfilehash: 2fd44eb0eca6986111a8300bdd4f48bda611d08d
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128569605"
---
# <a name="container-monitoring-solution-in-azure-monitor"></a>Azure Monitor의 컨테이너 모니터링 솔루션

![컨테이너 기호](./media/containers/containers-symbol.png)

이 문서에서는 단일 위치에서 Docker 및 Windows 컨테이너 호스트를 보고 관리하는 데 도움이 되는 Azure Monitor에서 컨테이너 모니터링 솔루션을 설정하고 사용하는 방법을 설명합니다. Docker는 IT 인프라에 대한 소프트웨어 배포를 자동화하는 컨테이너를 만드는 데 사용되는 소프트웨어 가상화 시스템입니다.

> [!IMPORTANT]
> 컨테이너 모니터링 솔루션을 단계적으로 모니터링 하기 위해 Kubernetes 환경을 모니터링 하려면 [Azure Monitor container insights](container-insights-onboard.md) 를 사용 하는 것이 좋습니다.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

솔루션은 어떤 컨테이너가 실행 중인지, 실행 중인 컨테이너 이미지 및 컨테이너가 실행 중인 위치를 표시합니다. 컨테이너에 사용하는 명령을 표시하는 상세한 감사 정보를 확인할 수 있습니다. 또한 중앙화된 로그를 보고 검색하면 원격으로 Docker 또는 Windows 호스트를 보지 않고도 컨테이너의 문제를 해결할 수 있습니다. 호스트에서 성가시고 과도한 리소스를 소모하는 컨테이너를 찾을 수 있습니다. 또한 컨테이너에 대해 중앙화된 CPU 메모리, 스토리지, 네트워크 사용 및 성능 정보를 확인할 수 있습니다. Windows를 실행하는 컴퓨터에서 Windows Server, Hyper-V, Docker 컨테이너에서 로그를 중앙 집중화 및 비교할 수 있습니다. 솔루션은 다음과 같은 컨테이너 오케스트레이터를 지원합니다.

- Docker Swarm
- DC/OS
- Service Fabric

Kubernetes 및 Red Hat OpenShift를 모니터링 하려면 Azure Monitor Container insights를 사용 하는 것이 좋습니다.
- AKS ([AKS에 대 한 컨테이너 정보 구성](container-insights-enable-existing-clusters.md))
- Red Hat OpenShift ([Azure Arc를 사용 하 여 컨테이너 통찰력 구성](container-insights-enable-arc-enabled-clusters.md))

[Azure Service Fabric](../../service-fabric/service-fabric-overview.md)에 배포된 컨테이너가 있는 경우 [Service Fabric 솔루션과](../../service-fabric/service-fabric-diagnostics-oms-setup.md) 이 솔루션을 모두 사용하여 클러스터 이벤트의 모니터링을 포함하는 것이 좋습니다. Service Fabric 솔루션을 사용하도록 설정하기 전에 [Service Fabric 솔루션을 사용하여](../../service-fabric/service-fabric-diagnostics-event-analysis-oms.md) 제공되는 내용 및 사용 방법에 대해 검토합니다.

AKS(Azure Kubernetes Service)에 호스트된 Kubernetes 환경에 배포된 워크로드의 성능을 모니터링하려는 경우 [Azure Kubernetes Service 모니터링](../containers/container-insights-overview.md)을 참조하세요. 컨테이너 모니터링 솔루션은 해당 플랫폼을 모니터링하도록 지원하지 않습니다.  

다음 다이어그램은 Azure Monitor를 사용하는 다양한 컨테이너 호스트와 에이전트 간의 관계를 보여줍니다.

![컨테이너 다이어그램](./media/containers/containers-diagram.png)

## <a name="system-requirements-and-supported-platforms"></a>시스템 요구 사항 및 지원되는 플랫폼

시작하기 전에 다음 세부 정보를 검토하여 필수 구성 요소를 충족하는지 확인합니다.

### <a name="container-monitoring-solution-support-for-docker-orchestrator-and-os-platform"></a>Docker Orchestrator 및 OS 플랫폼에 대한 컨테이너 모니터링 솔루션 지원

다음 표에서는 Azure Monitor를 사용하여 컨테이너 인벤토리, 성능 및 로그에 대한 Docker 오케스트레이션 및 운영 체제 모니터링 지원을 간략하게 설명합니다.   

|Docker 오케스트레이션 | ACS | Linux | Windows | 컨테이너<br>재고 | 이미지<br>재고 | 노드<br>재고 | 컨테이너<br>성능 | 컨테이너<br>이벤트 | 이벤트<br>로그 | 컨테이너<br>로그 |
|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|
| Kubernetes | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; |
| Mesosphere<br>DC/OS | &#8226; | &#8226; | | &#8226; | &#8226; | &#8226; | &#8226;| &#8226; | &#8226; | &#8226; |
| Docker<br>Swarm | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | | &#8226; |
| 서비스<br>Fabric | | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; |
| Red Hat Open<br>Shift | | &#8226; | | &#8226; | &#8226;| &#8226; | &#8226; | &#8226; | | &#8226; |
| Windows Server<br>(독립 실행형) | | | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | | &#8226; |
| Linux 서버<br>(독립 실행형) | | &#8226; | | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | | &#8226; |

### <a name="docker-versions-supported-on-linux"></a>Linux에서 지원되는 Docker 버전

- Docker 1.11 - 1.13
- Docker CE 및 EE v17.06

### <a name="x64-linux-distributions-supported-as-container-hosts"></a>컨테이너 호스트로 지원되는 x64 Linux 배포

- Ubuntu 14.04 LTS 및 16.04 LTS
- CoreOS(stable)
- Amazon Linux 2016.09.0
- openSUSE 13.2
- openSUSE LEAP 42.2
- CentOS 7.2 및 7.3
- SLES 12
- RHEL 7.2 및 7.3
- Red Hat OCP(OpenShift Container Platform) 3.4 및 3.5
- ACS Mesosphere DC/OS 1.7.3 - 1.8.8
- ACS Kubernetes 1.4.5 - 1.6
    - Kubernetes 이벤트, Kubernetes 인벤토리 및 컨테이너 프로세스는 1.4.1-45 이상 버전의 Linux용 Log Analytics 에이전트에서만 지원됩니다.
- ACS Docker Swarm

[!INCLUDE [log-analytics-agent-note.md](../../../includes/log-analytics-agent-note.md)] 

### <a name="supported-windows-operating-system"></a>지원되는 Windows 운영 체제

- Windows Server 2016
- Windows 10 1주년 버전(Professional 또는 Enterprise)

### <a name="docker-versions-supported-on-windows"></a>Windows에서 지원되는 Docker 버전

- Docker 1.12 - 1.13
- Docker 17.03.0 이상

## <a name="installing-and-configuring-the-solution"></a>솔루션 설치 및 구성

다음 정보를 사용하여 솔루션을 설치하고 구성합니다.

1. [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.ContainersOMS?tab=Overview)에서 또는 [솔루션 갤러리에서 모니터링 솔루션 추가](../insights/solutions.md)에서 설명하는 프로세스를 사용하여 Log Analytics 작업 영역에 컨테이너 모니터링 솔루션을 추가합니다.

2. Log Analytics 에이전트를 사용하여 Docker를 설치하고 사용합니다. 운영 체제 및 Docker 조정자에 따라 에이전트를 구성하는 데 다음 메서드를 사용할 수 있습니다.
   - 독립 실행형 호스트의 경우:
     - 지원되는 Linux 운영 체제에서 Docker를 설치 및 실행한 다음, [Linux용 Log Analytics 에이전트](../vm/monitor-virtual-machine.md)를 설치하고 구성합니다.  
     - CoreOS에서는 Linux용 Log Analytics 에이전트를 실행할 수 없습니다. 대신 Linux용 Log Analytics 에이전트의 컨테이너화된 버전을 실행합니다. Azure Government 클라우드에서 컨테이너를 사용하는 경우 CoreOS를 포함한 Linux 컨테이너 호스트 또는 CoreOS을 포함한 Azure Government Linux 컨테이너 호스트를 검토하세요.
     - Windows Server 2016 및 Windows 10에서 Docker 엔진 및 클라이언트를 설치한 후 에이전트를 연결하여 정보를 수집하고 Azure Monitor에 보냅니다. Windows 환경을 사용하는 경우 [Windows 컨테이너 호스트 설치 및 구성](#install-and-configure-windows-container-hosts)을 검토하세요.
   - Docker 다중 호스트 오케스트레이션의 경우:
     - Red Hat OpenShift 환경인 경우 Red Hat OpenShift용 Log Analytics 에이전트 구성을 검토하세요.
     - Azure Container Service를 사용하는 Kubernetes 클러스터가 있는 경우:
       - [Kubernetes용 Log Analytics Linux 에이전트 구성](#configure-a-log-analytics-linux-agent-for-kubernetes)을 검토합니다.
       - [Kubernetes용 Log Analytics Windows 에이전트 구성](#configure-a-log-analytics-windows-agent-for-kubernetes)을 검토합니다.
       - Helm을 사용하여 Linux Kubernetes에 Log Analytics 에이전트 배포를 검토합니다.
     - Azure Container Service DC/OS 클러스터가 있는 경우 [Azure Monitor를 사용하여 AZURE CONTAINER SERVICE dc/os 클러스터 모니터링](/previous-versions/azure/container-service/dcos-swarm/container-service-monitoring-oms)에서 자세한 내용을 알아보세요.
     - Docker Swarm 모드 환경에 있는 경우 Docker Swarm용 Log Analytics 에이전트 구성에서 자세히 알아보세요.
     - Service Fabric 클러스터가 있는 경우 [Azure Monitor로 컨테이너 모니터링하기](../../service-fabric/service-fabric-diagnostics-oms-containers.md)에서 자세히 알아보세요.

[Windows에서 Docker 엔진](/virtualization/windowscontainers/manage-docker/configure-docker-daemon) 문서에서 Windows를 실행하는 컴퓨터에서 Docker 엔진을 설치하고 구성하는 방법에 대한 추가 정보를 확인합니다.

> [!IMPORTANT]
> Docker는 컨테이너 호스트에 [Linux용 Log Analytics 에이전트](../vm/monitor-virtual-machine.md)를 설치하기 **전에** 실행해야 합니다. Docker 설치에 앞서 에이전트를 이미 설치한 경우 Linux용 Log Analytics 에이전트를 다시 설치해야 합니다. Docker에 대한 자세한 내용은 [Docker 웹 사이트](https://www.docker.com)를 참조하세요.

### <a name="install-and-configure-linux-container-hosts"></a>Linux 컨테이너 호스트 설치 및 구성

Docker를 설치한 후 컨테이너 호스트에 다음 설정을 사용하여 Docker에 사용할 에이전트를 구성합니다. Azure Portal에서 찾을 수 있는 Log Analytics 작업 영역 ID 및 키가 필요합니다. 작업 영역에서 **빠른 시작** > **컴퓨터** 를 클릭하여 **작업 영역 ID** 및 **기본 키** 를 확인합니다.  두 항목을 복사하여 선호하는 편집기에 붙여넣습니다.

**CoreOS를 제외한 모든 Linux 컨테이너 호스트의 경우:**

- Linux용 Log Analytics 에이전트를 설치하는 방법에 대한 자세한 내용과 단계는 [Log Analytics 에이전트 개요](../agents/log-analytics-agent.md)를 참조하세요.

**CoreOS를 포함한 모든 Linux 컨테이너 호스트의 경우:**

모니터링하려는 컨테이너를 시작합니다. 다음 예제를 수정하여 사용합니다.

```
sudo docker run --privileged -d -v /var/run/docker.sock:/var/run/docker.sock -v /var/lib/docker/containers:/var/lib/docker/containers -e WSID="your workspace id" -e KEY="your key" -h=`hostname` -p 127.0.0.1:25225:25225 --name="omsagent" --restart=always mcr.microsoft.com/azuremonitor/containerinsights/ciprod:microsoft-oms-latest
```

**CoreOS를 포함한 모든 Azure Government Linux 컨테이너 호스트의 경우:**

모니터링하려는 컨테이너를 시작합니다. 다음 예제를 수정하여 사용합니다.

```
sudo docker run --privileged -d -v /var/run/docker.sock:/var/run/docker.sock -v /var/log:/var/log -v /var/lib/docker/containers:/var/lib/docker/containers -e WSID="your workspace id" -e KEY="your key" -e DOMAIN="opinsights.azure.us" -p 127.0.0.1:25225:25225 -p 127.0.0.1:25224:25224/udp --name="omsagent" -h=`hostname` --restart=always mcr.microsoft.com/azuremonitor/containerinsights/ciprod:microsoft-oms-latest
```

**설치된 Linux 에이전트에서 컨테이너의 다른 에이전트로 전환**

이전에 직접 설치한 에이전트를 사용하였고 이제 컨테이너에서 실행 중인 에이전트를 사용하려는 경우 먼저 Linux용 Log Analytics 에이전트를 제거해야 합니다. 성공적으로 에이전트를 제거하는 방법은 [Linux용 Log Analytics 에이전트 제거](../vm/monitor-virtual-machine.md)를 참조하세요.  

#### <a name="configure-a-log-analytics-agent-for-docker-swarm"></a>Docker Swarm용 Log Analytics 에이전트 구성

Docker Swarm에서 글로벌 서비스로 Log Analytics 에이전트를 실행할 수 있습니다. 다음 정보를 사용하여 Log Analytics 에이전트 서비스를 만듭니다. Log Analytics 작업 영역 ID와 기본 키를 제공해야 합니다.

- 마스터 노드에서 다음을 실행합니다.

    ```
    sudo docker service create  --name omsagent --mode global  --mount type=bind,source=/var/run/docker.sock,destination=/var/run/docker.sock --mount type=bind,source=/var/lib/docker/containers,destination=/var/lib/docker/containers -e WSID="<WORKSPACE ID>" -e KEY="<PRIMARY KEY>" -p 25225:25225 -p 25224:25224/udp  --restart-condition=on-failure mcr.microsoft.com/azuremonitor/containerinsights/ciprod:microsoft-oms-latest
    ```

##### <a name="secure-secrets-for-docker-swarm"></a>Docker Swarm에 대한 비밀 보호

Docker Swarm의 경우 작업 영역 ID와 기본 키에 대한 비밀을 만들면 비밀 정보를 만드는 데 다음 정보를 사용합니다.

1. 마스터 노드에서 다음을 실행합니다.

    ```
    echo "WSID" | docker secret create WSID -
    echo "KEY" | docker secret create KEY -
    ```

2. 비밀이 제대로 생성되었는지 확인합니다.

    ```
    keiko@swarmm-master-13957614-0:/run# sudo docker secret ls
    ```

    ```
    ID                          NAME                CREATED             UPDATED
    j2fj153zxy91j8zbcitnjxjiv   WSID                43 minutes ago      43 minutes ago
    l9rh3n987g9c45zffuxdxetd9   KEY                 38 minutes ago      38 minutes ago
    ```

3. 다음 명령을 실행하여 비밀을 컨테이너화된 Log Analytics 에이전트에 탑재합니다.

    ```
    sudo docker service create  --name omsagent --mode global  --mount type=bind,source=/var/run/docker.sock,destination=/var/run/docker.sock --mount type=bind,source=/var/lib/docker/containers,destination=/var/lib/docker/containers --secret source=WSID,target=WSID --secret source=KEY,target=KEY  -p 25225:25225 -p 25224:25224/udp --restart-condition=on-failure mcr.microsoft.com/azuremonitor/containerinsights/ciprod:microsoft-oms-latest
    ```

#### <a name="configure-a-log-analytics-agent-for-red-hat-openshift"></a>Red Hat OpenShift용 Log Analytics 에이전트 구성

컨테이너 모니터링 데이터 수집을 시작하기 위해 Red Hat OpenShift에 Log Analytics 에이전트를 추가하는 방법에는 세 가지가 있습니다.

* 각 OpenShift 노드에서 직접 [Linux용 Log Analytics 에이전트를 설치](../vm/monitor-virtual-machine.md)  
* Azure에 있는 각 OpenShift 노드에서 [Log Analytics VM 확장을 사용하도록 설정](../vm/monitor-virtual-machine.md)  
* Log Analytics 에이전트를 OpenShift 디먼 집합으로 설치  

이 섹션에서는 OpenShift 디먼 집합으로 Log Analytics 에이전트를 설치하는 데 필요한 단계를 다룹니다.  

1. OpenShift 마스터 노드에 로그온하고, GitHub에서 마스터 노드로 yaml 파일 [ocp-omsagent.yaml](https://github.com/Microsoft/OMS-docker/blob/master/OpenShift/ocp-omsagent.yaml)을 복사하고, Log Analytics 작업 영역 ID와 기본 키로 값을 수정합니다.
2. 다음 명령을 실행하여 Azure Monitor에 대한 프로젝트를 만들고 사용자 계정을 설정합니다.

    ```
    oc adm new-project omslogging --node-selector='zone=default'
    oc project omslogging  
    oc create serviceaccount omsagent  
    oc adm policy add-cluster-role-to-user cluster-reader   system:serviceaccount:omslogging:omsagent  
    oc adm policy add-scc-to-user privileged system:serviceaccount:omslogging:omsagent  
    ```

3. 디먼 집합을 배포하려면 다음을 실행합니다.

    `oc create -f ocp-omsagent.yaml`

4. 올바르게 구성되어 있고 작동하는지 확인하려면 다음을 입력합니다.

    `oc describe daemonset omsagent`  

    출력은 다음과 유사합니다.

    ```
    [ocpadmin@khm-0 ~]$ oc describe ds oms  
    Name:           oms  
    Image(s):       mcr.microsoft.com/azuremonitor/containerinsights/ciprod:microsoft-oms-latest  
    Selector:       name=omsagent  
    Node-Selector:  zone=default  
    Labels:         agentVersion=1.4.0-12  
                    dockerProviderVersion=10.0.0-25  
                    name=omsagent  
    Desired Number of Nodes Scheduled: 3  
    Current Number of Nodes Scheduled: 3  
    Number of Nodes Misscheduled: 0  
    Pods Status:    3 Running / 0 Waiting / 0 Succeeded / 0 Failed  
    No events.  
    ```

Log Analytics 에이전트 디먼 집합 yaml 파일을 사용할 때 Log Analytics 작업 영역 ID와 기본 키를 보호하기 위해 비밀을 사용하려는 경우 다음 단계를 수행합니다.

1. OpenShift 마스터 노드에 로그온하고 GitHub에서 yaml 파일 [ocp-ds-omsagent.yaml](https://github.com/Microsoft/OMS-docker/blob/master/OpenShift/ocp-ds-omsagent.yaml) 및 비밀 생성 스크립트 [ocp-secretgen.sh](https://github.com/Microsoft/OMS-docker/blob/master/OpenShift/ocp-secretgen.sh)를 복사합니다.  이 스크립트는 비밀 정보를 보호하기 위해 Log Analytics 작업 영역 ID와 기본 키에 대한 비밀 yaml 파일을 생성합니다.  
2. 다음 명령을 실행하여 Azure Monitor에 대한 프로젝트를 만들고 사용자 계정을 설정합니다. 비밀 생성 스크립트는 Log Analytics 작업 영역 ID`<WSID>` 및 기본 키`<KEY>`를 요청하고, 완료되면 ocp-secret.yaml 파일이 생성됩니다.  

    ```
    oc adm new-project omslogging --node-selector='zone=default'  
    oc project omslogging  
    oc create serviceaccount omsagent  
    oc adm policy add-cluster-role-to-user cluster-reader   system:serviceaccount:omslogging:omsagent  
    oc adm policy add-scc-to-user privileged system:serviceaccount:omslogging:omsagent  
    ```

3. 다음을 실행하여 비밀 파일을 배포합니다.

    `oc create -f ocp-secret.yaml`

4. 다음을 실행하여 배포를 확인합니다.

    `oc describe secret omsagent-secret`  

    출력은 다음과 유사합니다.  

    ```
    [ocpadmin@khocp-master-0 ~]$ oc describe secret omsagent-secret  
    Name:           omsagent-secret  
    Namespace:      omslogging  
    Labels:         <none>  
    Annotations:    <none>  

    Type:   Opaque  

    Data  
    ====  
    KEY:    89 bytes  
    WSID:   37 bytes  
    ```

5. 다음을 실행하여 Log Analytics 에이전트 디먼 집합 yaml 파일을 배포합니다.

    `oc create -f ocp-ds-omsagent.yaml`  

6. 다음을 실행하여 배포를 확인합니다.

    `oc describe ds oms`

    출력은 다음과 유사합니다.

    ```
    [ocpadmin@khocp-master-0 ~]$ oc describe ds oms  
    Name:           oms  
    Image(s):       mcr.microsoft.com/azuremonitor/containerinsights/ciprod:microsoft-oms-latest  
    Selector:       name=omsagent  
    Node-Selector:  zone=default  
    Labels:         agentVersion=1.4.0-12  
                    dockerProviderVersion=10.0.0-25  
                    name=omsagent  
    Desired Number of Nodes Scheduled: 3  
    Current Number of Nodes Scheduled: 3  
    Number of Nodes Misscheduled: 0  
    Pods Status:    3 Running / 0 Waiting / 0 Succeeded / 0 Failed  
    No events.  
    ```

#### <a name="configure-a-log-analytics-linux-agent-for-kubernetes"></a>Kubernetes용 Log Analytics Linux 에이전트 구성

Kubernetes의 경우 Linux용 Log Analytics 에이전트를 설치하려면 스크립트를 사용하여 작업 영역 ID 및 기본 키에 대한 비밀 yaml 파일을 생성합니다. [Log Analytics Docker Kubernetes GitHub](https://github.com/Microsoft/OMS-docker/tree/master/Kubernetes) 페이지에는 비밀 정보를 포함 또는 포함하지 않고 사용할 수 있는 파일이 있습니다.

- 비밀 정보(omsagent.yaml)가 없는 Linux용 기본 Log Analytics 에이전트 DaemonSet
- 비밀 yaml(omsagentsecret.yaml) 파일을 생성하는 비밀 생성 스크립트를 통해 비밀 정보(omsagent-ds-secrets.yaml)를 사용하는 Linux용 Log Analytics 에이전트 DaemonSet yaml 파일입니다.

비밀을 포함 또는 포함하지 않고 omsagent DaemonSet를 만들도록 선택할 수 있습니다.

**비밀이 없는 기본 OMSagent DaemonSet yaml 파일**

- 기본 Log Analytics 에이전트 DaemonSet yaml 파일의 경우 `<WSID>` 및 `<KEY>`를 사용자의 WSID 및 KEY로 바꿉니다. 파일을 마스터 노드에 복사하고 다음을 실행합니다.

    ```
    sudo kubectl create -f omsagent.yaml
    ```

**비밀이 있는 기본 OMSagent DaemonSet yaml 파일**

1. 비밀 정보를 사용하여 Log Analytics 에이전트 DaemonSet을 사용하려면 먼저 비밀을 만듭니다.
    1. 스크립트 및 비밀 템플릿 파일을 복사하고 이들이 같은 디렉터리에 있는지 확인합니다.
        - 비밀 생성 스크립트 - secret-gen.sh
        - 비밀 템플릿 - secret-template.yaml
    2. 다음 예제와 같이 스크립트를 실행합니다. 스크립트에서 Log Analytics 작업 영역 ID 및 기본 키를 요청하고 사용자가 이를 입력하면 스크립트에서 비밀 yaml 파일을 생성하며 사용자가 실행할 수 있습니다.   

        ```
        #> sudo bash ./secret-gen.sh
        ```

    3. 다음을 실행하여 비밀 Pod를 만듭니다.
        ```
        sudo kubectl create -f omsagentsecret.yaml
        ```

    4. 확인하려면 다음을 실행합니다.

        ```
        keiko@ubuntu16-13db:~# sudo kubectl get secrets
        ```

        출력은 다음과 유사합니다.

        ```
        NAME                  TYPE                                  DATA      AGE
        default-token-gvl91   kubernetes.io/service-account-token   3         50d
        omsagent-secret       Opaque                                2         1d
        ```

        ```
        keiko@ubuntu16-13db:~# sudo kubectl describe secrets omsagent-secret
        ```

        출력은 다음과 유사합니다.

        ```
        Name:           omsagent-secret
        Namespace:      default
        Labels:         <none>
        Annotations:    <none>

        Type:   Opaque

        Data
        ====
        WSID:   36 bytes
        KEY:    88 bytes
        ```

    5. ```sudo kubectl create -f omsagent-ds-secrets.yaml```을 실행하여 omsagent daemon-set 만들기

2. Log Analytics 에이전트 DaemonSet이 다음과 같이 실행 중인지 확인합니다.

    ```
    keiko@ubuntu16-13db:~# sudo kubectl get ds omsagent
    ```

    ```
    NAME       DESIRED   CURRENT   NODE-SELECTOR   AGE
    omsagent   3         3         <none>          1h
    ```

Kubernetes의 경우 스크립트를 사용하여 Linux용 Log Analytics 에이전트의 작업 영역 ID 및 기본 키에 대한 비밀 yaml 파일을 생성합니다. [omsagent yaml 파일](https://github.com/Microsoft/OMS-docker/blob/master/Kubernetes/omsagent.yaml)에 다음 예제 정보를 사용하여 비밀 정보를 보호합니다.

```
keiko@ubuntu16-13db:~# sudo kubectl describe secrets omsagent-secret
Name:           omsagent-secret
Namespace:      default
Labels:         <none>
Annotations:    <none>

Type:   Opaque

Data
====
WSID:   36 bytes
KEY:    88 bytes
```

#### <a name="configure-a-log-analytics-windows-agent-for-kubernetes"></a>Kubernetes용 Log Analytics Windows 에이전트 구성

Windows Kubernetes의 경우 Log Analytics 에이전트를 설치하려면 스크립트를 사용하여 작업 영역 ID 및 기본 키에 대한 비밀 yaml 파일을 생성합니다. [Log Analytics Docker Kubernetes GitHub](https://github.com/Microsoft/OMS-docker/tree/master/Kubernetes/windows) 페이지에는 비밀 정보를 포함하고 사용할 수 있는 파일이 있습니다.  마스터 및 에이전트 노드에 대해 별도의 Log Analytics 에이전트를 설치해야 합니다.  

1. 마스터 노드에 대한 비밀 정보를 사용하여 Log Analytics 에이전트 DaemonSet을 사용하려면 먼저 로그인하여 비밀을 만듭니다.
    1. 스크립트 및 비밀 템플릿 파일을 복사하고 이들이 같은 디렉터리에 있는지 확인합니다.
        - 비밀 생성 스크립트 - secret-gen.sh
        - 비밀 템플릿 - secret-template.yaml

    2. 다음 예제와 같이 스크립트를 실행합니다. 스크립트에서 Log Analytics 작업 영역 ID 및 기본 키를 요청하고 사용자가 이를 입력하면 스크립트에서 비밀 yaml 파일을 생성하며 사용자가 실행할 수 있습니다.

        ```
        #> sudo bash ./secret-gen.sh
        ```
    3. ```kubectl create -f omsagentsecret.yaml```을 실행하여 omsagent daemon-set 만들기
    4. 확인하려면 다음을 실행합니다.

        ```
        root@ubuntu16-13db:~# kubectl get secrets
        ```

        출력은 다음과 유사합니다.

        ```
        NAME                  TYPE                                  DATA      AGE
        default-token-gvl91   kubernetes.io/service-account-token   3         50d
        omsagent-secret       Opaque                                2         1d
        root@ubuntu16-13db:~# kubectl describe secrets omsagent-secret
        Name:           omsagent-secret
        Namespace:      default
        Labels:         <none>
        Annotations:    <none>

        Type:   Opaque

        Data
        ====
        WSID:   36 bytes
        KEY:    88 bytes
        ```

    5. ```kubectl create -f ws-omsagent-de-secrets.yaml```을 실행하여 omsagent daemon-set 만들기

2. Log Analytics 에이전트 DaemonSet이 다음과 같이 실행 중인지 확인합니다.

    ```
    root@ubuntu16-13db:~# kubectl get deployment omsagent
    NAME       DESIRED   CURRENT   NODE-SELECTOR   AGE
    omsagent   1         1         <none>          1h
    ```

3. Windows를 실행하는 작업자 노드에 에이전트를 설치하려면 [Windows 컨테이너 호스트 설치 및 구성](#install-and-configure-windows-container-hosts) 섹션의 단계를 따릅니다.

#### <a name="use-helm-to-deploy-log-analytics-agent-on-linux-kubernetes"></a>Helm을 사용하여 Linux Kubernetes에 Log Analytics 에이전트 배포

Helm을 사용하여 Linux Kubernetes 환경에 Log Analytics 에이전트를 배포하려면 다음 단계를 수행합니다.

1. ```helm install --name omsagent --set omsagent.secret.wsid=<WSID>,omsagent.secret.key=<KEY> stable/msoms```을 실행하여 omsagent daemon-set 만들기
2. 결과는 다음과 유사합니다.

    ```
    NAME:   omsagent
    LAST DEPLOYED: Tue Sep 19 20:37:46 2017
    NAMESPACE: default
    STATUS: DEPLOYED

    RESOURCES:
    ==> v1/Secret
    NAME            TYPE    DATA  AGE
    omsagent-msoms  Opaque  3     3s

    ==> v1beta1/DaemonSet
    NAME            DESIRED  CURRENT  READY  UP-TO-DATE  AVAILABLE  NODE-SELECTOR  AGE
    omsagent-msoms  3        3        3      3           3          <none>         3s
    ```

3. ```helm status "omsagent"```를 실행하여 omsagent의 상태를 확인할 수 있으며, 출력은 다음과 유사합니다.

    ```
    keiko@k8s-master-3814F33-0:~$ helm status omsagent
    LAST DEPLOYED: Tue Sep 19 20:37:46 2017
    NAMESPACE: default
    STATUS: DEPLOYED
 
    RESOURCES:
    ==> v1/Secret
    NAME            TYPE    DATA  AGE
    omsagent-msoms  Opaque  3     17m
 
    ==> v1beta1/DaemonSet
    NAME            DESIRED  CURRENT  READY  UP-TO-DATE  AVAILABLE  NODE-SELECTOR  AGE
    omsagent-msoms  3        3        3      3           3          <none>         17m
    ```
   
    자세한 내용을 보려면 [컨테이너 솔루션 Helm 차트](https://aka.ms/omscontainerhelm)를 방문하세요.

### <a name="install-and-configure-windows-container-hosts"></a>Windows 컨테이너 호스트 설치 및 구성

Windows 컨테이너 호스트를 설치하고 구성하는 섹션의 정보를 사용합니다.

#### <a name="preparation-before-installing-windows-agents"></a>Windows 에이전트 설치 전 준비

Windows를 실행하는 컴퓨터에 에이전트를 설치하기 전에 Docker 서비스를 구성해야 합니다. 구성을 통해 에이전트는 원격으로 Docker 디먼에 액세스하고 모니터링을 위해 데이터를 캡처할 수 있도록 Windows 에이전트 또는 Azure Monitor 가상 머신 확장이 Docker TCP 소켓을 사용할 수 있습니다.

##### <a name="to-configure-the-docker-service"></a>Docker 서비스를 구성하기 위해서는 다음을 수행합니다.  

Windows 서버에 대해 TCP 파이프 및 명명 된 파이프를 사용하도록 설정 하려면 다음 PowerShell 명령을 수행합니다.

```
Stop-Service docker
dockerd --unregister-service
dockerd --register-service -H npipe:// -H 0.0.0.0:2375  
Start-Service docker
```

Windows 컨테이너에서 사용하는 Docker 데몬 구성에 대한 자세한 내용은 [Windows에서 Docker 엔진](/virtualization/windowscontainers/manage-docker/configure-docker-daemon)을 참조하세요.

#### <a name="install-windows-agents"></a>Windows 에이전트 설치

Windows 및 Hyper-V 컨테이너 모니터링을 사용하도록 설정하려면 컨테이너 호스트인 Windows 컴퓨터에 MMA(Microsoft Monitoring Agent)를 설치합니다. 온-프레미스 환경에서 Windows를 실행하는 컴퓨터는 [Windows 컴퓨터를 Azure Monitor에 연결](../agents/agent-windows.md)를 참조하세요. Azure에서 실행되는 가상 머신의 경우 [가상 머신 확장](../vm/monitor-virtual-machine.md)을 사용하여 Azure Monitor에 연결합니다.

Service Fabric에서 실행 중인 Windows 컨테이너를 모니터링할 수 있습니다. 그러나 [Azure에서 실행 중인 가상 머신](../vm/monitor-virtual-machine.md) 및 [온-프레미스 환경에서 Windows를 실행하는 컴퓨터](../agents/agent-windows.md)만 현재 Service Fabric에 대해 지원됩니다.

컨테이너 모니터링 솔루션이 Windows에 대해 올바르게 설정되어 있는지 확인할 수 있습니다. 관리 팩이 제대로 다운로드되었는지 확인하려면 *ContainerManagement.xxx* 를 찾습니다. 파일은 C:\Program Files\Microsoft Monitoring Agent\Agent\Health Service State\Management Packs 폴더에 있어야 합니다.

## <a name="solution-components"></a>솔루션 구성 요소

Azure Portal에서 *솔루션 갤러리* 로 이동하여 **컨테이너 모니터링 솔루션** 을 추가합니다. Windows 에이전트를 사용하는 경우 이 솔루션을 추가할 때 에이전트와 함께 다음 관리 팩이 각 컴퓨터에 설치되어 있습니다. 관리 팩에는 구성 또는 유지 관리가 필요하지 않습니다.

- C:\Program Files\Microsoft Monitoring Agent\Agent\Health Service State\Management Packs에 설치된 *ContainerManagement.xxx*

## <a name="container-data-collection-details"></a>컨테이너 데이터 컬렉션 세부 정보

컨테이너 모니터링 솔루션은 사용자가 활성화한 에이전트를 사용하여 컨테이너 호스트 및 컨테이너로부터 다양한 성능 메트릭과 로그 데이터를 수집합니다.

데이터는 다음 에이전트 형식으로 3분마다 수집됩니다.

- [Linux용 Log Analytics 에이전트](../vm/monitor-virtual-machine.md)
- [Windows 에이전트](../agents/agent-windows.md)
- [Log Analytics VM 확장](../vm/monitor-virtual-machine.md)

### <a name="container-records"></a>컨테이너 레코드

다음 테이블은 컨테이너 모니터링 솔루션에 의해 수집된 레코드 및 로그 검색 결과에 표시된 데이터 형식의 예를 보여줍니다.

| 데이터 형식 | 로그 검색의 데이터 유형 | 필드 |
| --- | --- | --- |
| 호스트 및 컨테이너에 대한 성능 | `Perf` | 컴퓨터, ObjectName, CounterName &#40;%프로세서 시간, 디스크 읽기 MB, 디스크 쓰기 MB, 메모리 사용 MB, 네트워크 수신 바이트, 네트워크 송신 바이트, 프로세서 사용 초, 네트워크&#41;, CounterValue,TimeGenerated, CounterPath, SourceSystem |
| 컨테이너 인벤토리 | `ContainerInventory` | TimeGenerated, 컴퓨터, 컨테이너 이름, ContainerHostname, 이미지, ImageTag, ContainerState, ExitCode, EnvironmentVar, 명령, CreatedTime, StartedTime, FinishedTime, SourceSystem, ContainerID, ImageID |
| 컨테이너 이미지 인벤토리 | `ContainerImageInventory` | TimeGenerated, 컴퓨터, 이미지, ImageTag, ImageSize, VirtualSize, 실행 중, 일시 중지됨, 중지됨, 실패, SourceSystem, ImageID, TotalContainer |
| 컨테이너 로그 | `ContainerLog` | TimeGenerated, 컴퓨터, 이미지 ID, 컨테이너 이름, LogEntrySource, LogEntry, SourceSystem, ContainerID |
| 컨테이너 서비스 로그 | `ContainerServiceLog`  | TimeGenerated, 컴퓨터, TimeOfCommand, 이미지, 명령, SourceSystem, ContainerID |
| 컨테이너 노드 인벤토리 | `ContainerNodeInventory_CL`| TimeGenerated, 컴퓨터, ClassName_s, DockerVersion_s, OperatingSystem_s, Volume_s, Network_s, NodeRole_s, OrchestratorType_s, InstanceID_g, SourceSystem|
| Kubernetes 인벤토리 | `KubePodInventory_CL` | TimeGenerated, 컴퓨터, PodLabel_deployment_s, PodLabel_deploymentconfig_s, PodLabel_docker_registry_s, Name_s, Namespace_s, PodStatus_s, PodIp_s, PodUid_g, PodCreationTimeStamp_t, SourceSystem |
| 컨테이너 프로세스 | `ContainerProcess_CL` | TimeGenerated, 컴퓨터, Pod_s, Namespace_s, ClassName_s, InstanceID_s, Uid_s, PID_s, PPID_s, C_s, STIME_s, Tty_s, TIME_s, Cmd_s, Id_s, Name_s, SourceSystem |
| kubernetes 이벤트 | `KubeEvents_CL` | TimeGenerated, 컴퓨터, Name_s, ObjectKind_s, Namespace_s, Reason_s, Type_s, SourceComponent_s, SourceSystem, 메시지 |

*PodLabel* 데이터 형식에 추가된 레이블은 사용자 고유의 사용자 지정 레이블입니다. 테이블에 표시된 추가된 PodLabel 레이블은 예입니다. 따라서 `PodLabel_deployment_s`, `PodLabel_deploymentconfig_s`, `PodLabel_docker_registry_s`는 사용자 환경의 데이터 집합에 따라 달라지며 일반적으로 `PodLabel_yourlabel_s`와 비슷합니다.

## <a name="monitor-containers"></a>모니터 컨테이너
Azure Portal에서 솔루션을 사용 하도록 설정한 후 **컨테이너** 타일에 컨테이너 호스트와 호스트에서 실행되는 컨테이너에 대한 요약 정보가 표시됩니다.

![컨테이너 타일](./media/containers/containers-title.png)

타일은 환경에 있는 컨테이너의 수와, 해당 컨테이너의 실패, 실행 중 또는 중지 여부를 보여 줍니다.

### <a name="using-the-containers-dashboard"></a>컨테이너 대시보드 사용

**컨테이너** 타일을 클릭합니다. 여기에서는 다음에 따라 구성된 보기를 확인할 수 있습니다.

- **컨테이너 이벤트** - 컨테이너 상태 및 실패한 컨테이너가 있는 컴퓨터를 보여 줍니다.
- **컨테이너 로그** - 시간에 따라 생성되는 컨테이너 로그 파일의 차트 및 로그 파일 수가 가장 높은 컴퓨터의 목록을 보여 줍니다.
- **Kubernetes 이벤트** - 시간에 따라 생성되는 Kubernetes 이벤트의 차트 및 Pod가 이벤트를 생성하는 이유에 대한 목록을 보여 줍니다. *이 데이터 집합은 Linux 환경에만 사용됩니다.*
- **Kubernetes 네임스페이스 인벤토리** - 네임스페이스 및 Pod의 수를 표시하고 해당 계층을 보여 줍니다. *이 데이터 집합은 Linux 환경에만 사용됩니다.*
- **컨테이너 노드 인벤토리** - 컨테이너 노드/호스트에서 사용되는 오케스트레이션 형식의 수를 보여 줍니다. 컴퓨터 노드/호스트가 컨테이너의 수를 기준으로 나열됩니다. *이 데이터 집합은 Linux 환경에만 사용됩니다.*
- **컨테이너 이미지 인벤토리** - 사용된 총 컨테이너 이미지 수 및 이미지 형식의 수를 표시합니다. 이미지 수는 이미지 태그를 기준으로 나열됩니다.
- **컨테이너 상태** - 컨테이너를 실행 중인 총 컨테이너 노드/호스트 컴퓨터 수를 표시합니다. 컴퓨터는 실행 중인 호스트 수를 기준으로 나열됩니다.
- **컨테이너 프로세스** - 시간에 따른 실행 중인 컨테이너 프로세스의 꺾은선형 차트를 보여 줍니다. 컨테이너는 컨테이너 내 실행 중인 명령/프로세스를 기준으로 나열됩니다. *이 데이터 집합은 Linux 환경에만 사용됩니다.*
- **컨테이너 CPU 성능** - 컴퓨터 노드/호스트에 대한 시간에 따른 평균 CPU 사용률의 꺾은선형 차트를 보여 줍니다. 또한 평균 CPU 사용률을 기준으로 컴퓨터 노드/호스트를 나열합니다.
- **컨테이너 메모리 성능** - 시간에 따른 메모리 사용량의 꺾은선형 차트를 보여 줍니다. 또한 인스턴스 이름을 기준으로 컴퓨터 메모리 사용률을 나열합니다.
- **컴퓨터 성능** - 시간에 따른 CPU 성능의 백분율, 시간에 따른 메모리 사용량의 백분율 및 시간에 따른 사용 가능한 디스크 공간의 메가바이트를 꺾은 선형 차트로 표시합니다. 차트에서 해당 줄 위로 마우스를 이동하면 자세한 정보를 볼 수 있습니다.

대시보드의 각 영역은 수집된 데이터에서 실행되는 검색을 시각적으로 나타냅니다.

![수집된 데이터를 볼 수 있는 대시보드를 보여주는 스크린샷 ](./media/containers/containers-dash01.png)

![컨테이너 대시보드](./media/containers/containers-dash02.png)

**컨테이너 상태** 영역에서 아래 그림과 같이 위쪽 영역을 클릭합니다.

![컨테이너 상태](./media/containers/containers-status.png)

로그 분석이 열리고 컨테이너 상태에 대한 정보가 표시됩니다.

![컨테이너에 관한 로그 분석](./media/containers/containers-log-search.png)

여기에서 검색 쿼리를 편집 수정하여 관심 있는 특정 정보를 찾을 수 있습니다. 로그 쿼리에 대한 자세한 내용은 [Azure Monitor의 로그 쿼리 개요](../logs/log-query-overview.md)를 참조하세요.

## <a name="troubleshoot-by-finding-a-failed-container"></a>실패한 컨테이너를 검색하여 문제 해결

0이 아닌 종료 코드로 종료된 경우 Log Analytics는 이 컨테이너를 **Failed** 로 표시합니다. **실패한 컨테이너** 영역에서 환경의 오류 및 실패 개요를 볼 수 있습니다.

### <a name="to-find-failed-containers"></a>실패한 컨테이너 찾기

1. **컨테이너 상태** 영역을 클릭합니다.  
   ![컨테이너 상태](./media/containers/containers-status.png)
2. 로그 검색이 열리고 다음과 유사한 컨테이너 상태가 표시됩니다.  
   ![컨테이너 상태](./media/containers/containers-log-search.png)
3. 실패한 줄을 확장하고 + 를 클릭하여 쿼리에 해당 조건을 추가합니다. 그런 다음 쿼리에서 줄을 주석으로 요약합니다.
   ![주석처리 되어야 하는 줄을 보여주는 스크린샷](./media/containers/containers-state-failed-select.png)  
1. 쿼리를 실행한 다음, 결과에서 이미지 ID를 확인을 위해 줄을 확장합니다.  
   ![이미지 ID를 보는 방법을 보여 주는 스크린샷](./media/containers/containers-state-failed.png)  
1. 다음을 로그 쿼리에 입력합니다. `ContainerImageInventory | where ImageID == <ImageID>`를 입력하여 이미지 크기 및 중지되고 실패한 이미지 수와 같은 이미지에 대한 세부 정보를 확인합니다.  
   ![실패한 컨테이너](./media/containers/containers-failed04.png)

## <a name="query-logs-for-container-data"></a>컨테이너 데이터에 대한 쿼리 로그

특정 오류 문제를 해결할 때는 환경 내 발생 위치를 확인하는 것이 도움이 될 수 있습니다. 다음 로그 유형은 원하는 정보를 반환하는 쿼리를 만드는 데 도움이 됩니다.

- **ContainerImageInventory** – 이미지별로 정리하여 정보를 찾고 이미지 ID나 크기 같은 이미지 정보를 보려는 경우 이 유형을 사용합니다.
- **ContainerInventory** – 컨테이너 위치, 해당 컨테이너 이름, 실행 중인 이미지에 대한 정보가 필요할 때 이 유형을 사용합니다.
- **ContainerLog** – 특정 오류 로그 정보와 항목을 찾으려 할 때 이 유형을 사용합니다.
- **ContainerNodeInventory_CL** 컨테이너가 상주하는 호스트/노드에 대한 정보를 얻으려면 이 유형을 사용합니다. Docker 버전, 오케스트레이션 형식, 스토리지 및 네트워크 정보를 제공합니다.
- **ContainerProcess_CL** 컨테이너 내에서 실행 중인 프로세스를 신속하게 확인하려면 이 형식을 사용합니다.
- **ContainerServiceLog** – 시작, 중지, 삭제, 끌어오기 명령 등과 같이 Docker 데몬의 감사 추적 정보를 찾으려 할 때 이 유형을 사용합니다.
- **KubeEvents_CL** Kubernetes 이벤트를 확인하려면 이 형식을 사용합니다.
- **KubePodInventory_CL** 클러스터 계층 구조 정보를 이해하려면 이 형식을 사용합니다.

### <a name="to-query-logs-for-container-data"></a>컨테이너 데이터에 대한 로그를 쿼리하려면 다음을 수행해야 합니다.

* 최근에 실패했다고 알고 있는 이미지를 선택하고 그에 대한 오류 로그를 찾습니다. **ContainerInventory** 검색을 통해 해당 이미지를 실행 중인 컨테이너 이름부터 찾습니다. 예를 들어, `ContainerInventory | where Image == "ubuntu" and ContainerState == "Failed"`를 검색합니다.  
    ![Ubuntu 컨테이너에 대한 검색](./media/containers/search-ubuntu.png)

  결과의 모든 행을 확장하여 해당 컨테이너에 대한 세부정보를 봅니다.

## <a name="example-log-queries"></a>샘플 로그 쿼리

한두 가지 예제로 쿼리 구성을 시작한 다음 환경에 맞게 수정하는 것이 유용한 경우가 종종 있습니다. 시작 지점으로 솔루션 페이지의 맨 오른쪽에 있는 **샘플 쿼리** 영역을 시험해 보고 고급 쿼리를 작성 하는데 도움이 될 수 있습니다.

![컨테이너 쿼리](./media/containers/containers-queries.png)

## <a name="saving-log-queries"></a>로그 쿼리 저장하기

쿼리 저장은 Azure Monitor의 표준 기능입니다. 이를 저장해 두면 향후 유용하게 사용할 수 있습니다.

만든 쿼리가 유용하다고 생각되면 로그 검색 페이지 위쪽의 **즐겨찾기** 를 클릭하여 저장합니다. 그러면 나중에 **내 대시보드** 페이지에서 간편하게 액세스할 수 있습니다.

## <a name="next-steps"></a>다음 단계

[로그를 쿼리](../logs/log-query-overview.md)하여 자세한 컨테이너 데이터 레코드를 볼 수 있습니다.
