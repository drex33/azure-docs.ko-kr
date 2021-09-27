---
title: Linux 머신에 Log Analytics 에이전트 설치
description: 이 문서에서는 Linux용 Log Analytics 에이전트를 사용하여 다른 클라우드 또는 온-프레미스에 호스트된 Linux 컴퓨터를 Azure Monitor에 연결하는 방법을 설명합니다.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/21/2020
ms.openlocfilehash: a754656db58b1cc3c1414eb36601c873d8ecc785
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128671107"
---
# <a name="install-log-analytics-agent-on-linux-computers"></a>Linux 머신에 Log Analytics 에이전트 설치
이 문서는 다음 방법을 사용하여 Linux 컴퓨터에 Log Analytics 에이전트를 설치하는 방법을 자세히 설명합니다.

* GitHub에 호스트된 [래퍼 스크립트를 호출하는 Linux용 에이전트를 설치](#install-the-agent-using-wrapper-script)합니다. 컴퓨터가 인터넷에 직접 연결되어 있거나 프록시 서버를 통해 연결되어 있는 경우 에이전트를 설치하고 업그레이드하는 것이 권장되는 방법입니다.
* 에이전트를 [수동으로 다운로드하여 설치](#install-the-agent-manually)합니다. 이는 Linux 컴퓨터가 인터넷에 액세스할 수 없고 [Log Analytics 게이트웨이](./gateway.md)를 통해 Azure Monitor 또는 Azure Automation과 통신하는 경우에 필요합니다. 

>[!IMPORTANT]
> 이 문서에서 설명하는 설치 방법은 일반적으로 온-프레미스 또는 다른 클라우드의 가상 머신에 사용됩니다. Azure 가상 머신에 사용할 수 있는 보다 효율적인 옵션은 [설치 옵션](./log-analytics-agent.md#installation-options)을 참조하세요.



## <a name="supported-operating-systems"></a>지원되는 운영 체제

Log Analytics 에이전트에서 지원하는 Linux 배포 목록은 [Azure Monitor 에이전트 개요](agents-overview.md#supported-operating-systems)를 참조하세요.

>[!NOTE]
>OpenSSL 1.1.0은 x86_x64 플랫폼(64비트)에서만 지원되고, OpenSSL 1.x 미만은 어떤 플랫폼에서도 지원되지 않습니다.

>[!NOTE]
>컨테이너에서의 Log Analytics Linux 에이전트 실행은 지원되지 않습니다. 컨테이너를 모니터링해야 하는 경우 Docker 호스트에 대한 [컨테이너 모니터링 솔루션](../containers/containers.md) 또는 Kubernetes용 [컨테이너 인사이트](../containers/container-insights-overview.md)를 활용하세요.

2018년 8월 이후에 출시된 버전부터 지원 모델이 다음과 같이 변경됩니다.  

* 서버 버전만 지원되고 클라이언트 버전은 지원되지 않습니다.  
* [Azure Linux 보증 배포](../../virtual-machines/linux/endorsed-distros.md)를 집중적으로 지원합니다. 새 배포/버전이 Azure Linux 보증 배포가 되고 Log Analytics Linux 에이전트에 대해 지원되는 데 약간의 지연이 있을 수 있습니다.
* 나열된 각 주 버전의 모든 부 버전이 지원됩니다.
* 제조업체의 지원 종료 날짜가 지난 버전은 지원되지 않습니다.
* VM 이미지만 지원합니다. 공식 배포판 게시자 이미지에서 파생된 컨테이너를 포함한 모든 컨테이너는 지원되지 않습니다.
* 새 AMI 버전은 지원되지 않습니다.  
* 기본적으로 OpenSSL 1.x를 실행하는 버전만 지원됩니다.

>[!NOTE]
>현재 지원되지 않고 Microsoft 지원 모델에 맞지 않는 배포판 또는 버전을 사용하는 경우 이 리포지토리를 포크하고, Microsoft 기술 지원에서 포크된 에이전트 버전에 대한 지원을 제공하지 않는다는 것을 확인하는 것이 좋습니다.

### <a name="python-requirement"></a>Python 요구 사항

에이전트 버전 1.13.27부터 Linux 에이전트는 Python 2와 3을 모두 지원합니다. 항상 최신 에이전트를 사용하는 것이 좋습니다. 

이전 버전의 에이전트를 사용하는 경우 가상 머신에서 기본적으로 Python 2를 사용해야 합니다. 가상 머신이 기본적으로 Python 2를 포함하지 않는 배포판을 사용하는 경우 이를 설치해야 합니다. 다음 샘플 명령은 다른 배포판에 Python 2를 설치합니다.

 - Red Hat, CentOS, Oracle: `yum install -y python2`
 - Ubuntu, Debian: `apt-get install -y python2`
 - SUSE: `zypper install -y python2`

다시 말하지만, 이전 버전의 에이전트를 사용하는 경우에만 python2 실행 파일의 별칭을 *python* 으로 지정해야 합니다. 다음은 이 별칭을 설정하는 데 사용할 수 있는 한 가지 방법입니다.

1. 다음 명령을 실행하여 기존 별칭을 제거합니다.
 
    ```
    sudo update-alternatives --remove-all python
    ```

2. 다음 명령을 실행하여 별칭을 만듭니다.

    ```
    sudo update-alternatives --install /usr/bin/python python /usr/bin/python2 1
    ```

## <a name="supported-linux-hardening"></a>지원되는 Linux 강화
OMS 에이전트는 Linux에 대한 제한된 사용자 지정 및 강화 지원을 제공합니다.

현재 지원되는 사항은 다음과 같습니다. 
- FIP
- SELINUX (CENTOS 및 RHEL의 Marketplace 이미지 및 기본 설정)

지원되지 않는 옵션은 다음과 같습니다.
- CIS
- SELINUX (MLS와 같은 사용자 지정 강화)

CIS 및 SELINUX 강화 지원은 [Azure Monitoring Agent](./azure-monitor-agent-overview.md)에 대해 계획되어 있습니다. 추가 강화 및 사용자 지정 방법은 지원되지 않으며 OMS 에이전트에 대해 계획되지도 않습니다.  

## <a name="agent-prerequisites"></a>에이전트 필수 구성 요소

다음 표에서는 에이전트가 설치될 [지원되는 Linux 배포판](#supported-operating-systems)에 필요한 패키지를 보여 줍니다.

|필수 패키지 |Description |최소 버전 |
|-----------------|------------|----------------|
|Glibc |    GNU C 라이브러리 | 2.5-12 
|Openssl    | OpenSSL 라이브러리 | 1.0.x 또는 1.1.x |
|Curl | cURL 웹 클라이언트 | 7.15.5 |
|Python | | 2.7 또는 3.6+
|Python-ctypes | | 
|PAM | 플러그형 인증 모듈 | | 

>[!NOTE]
>Syslog 메시지를 수집하려면 rsyslog 또는 syslog-ng가 필요합니다. Red Hat Enterprise Linux 버전 5, CentOS 및 Oracle Linux 버전(sysklog)에서는 syslog 이벤트 수집을 위한 기본 syslog 디먼이 지원되지 않습니다. 이 배포의 해당 버전에서 syslog 데이터를 수집하려면 rsyslog 디먼을 설치하고 sysklog를 대체하도록 구성해야 합니다.

## <a name="network-requirements"></a>네트워크 요구 사항
Linux 에이전트에 대한 네트워크 요구 사항은 [Log Analytics 에이전트 개요](./log-analytics-agent.md#network-requirements)를 참조하세요.

## <a name="agent-install-package"></a>에이전트 설치 패키지

Linux용 Log Analytics 에이전트는 여러 패키지로 구성됩니다. 릴리스 파일은 다음 패키지를 포함하며, `--extract` 매개 변수와 함께 셸 번들을 실행하면 사용할 수 있습니다.

**패키지** | **버전** | **설명**
----------- | ----------- | --------------
omsagent | 1.13.9 | Linux용 Log Analytics 에이전트
omsconfig | 1.1.1 | Log Analytics 에이전트에 대한 구성 에이전트
omi | 1.6.4 | OMI(Open Management Infrastructure) -- 경량 CIM 서버 OMI에는 서비스의 작동에 필요한 cron 작업을 실행하기 위해 루트 액세스가 필요합니다.
scx | 1.6.4 | 운영 체제 성능 메트릭용 OMI CIM 공급자
apache-cimprov | 1.0.1 | OMI용 Apache HTTP 서버 성능 모니터링 공급자. Apache HTTP 서버가 감지되는 경우에만 설치됨.
mysql-cimprov | 1.0.1 | OMI용 MySQL 서버 성능 모니터링 공급자. MySQL/MariaDB 서버가 감지되는 경우에만 설치됨.
docker-cimprov | 1.0.0 | OMI용 Docker 공급자. Docker가 감지되는 경우에만 설치됨.

### <a name="agent-installation-details"></a>에이전트 설치 세부 정보

Linux용 Log Analytics 에이전트 패키지를 설치한 후에는 다음과 같은 시스템 수준의 구성 변경이 추가로 적용됩니다. 이러한 아티팩트는 omsagent 패키지가 제거되면 제거됩니다.

* `omsagent` 라는 이름의 권한 없는 사용자가 생성됩니다. 디먼이 이 자격 증명으로 실행됩니다. 
* sudoers *include* 파일이 `/etc/sudoers.d/omsagent`에서 생성됩니다. 그러면 `omsagent`에서 syslog 및 omsagent 디먼을 다시 시작할 수 있도록 허가됩니다. 설치된 sudo 버전에서 sudo *include* 지시문이 지원되지 않으면, 이 항목은 `/etc/sudoers`에 기록됩니다.
* syslog 구성은 이벤트 하위 집합을 에이전트에 전달하도록 수정됩니다. 자세한 내용은 [Syslog 데이터 컬렉션 구성](data-sources-syslog.md)을 참조하세요.

모니터링되는 Linux 컴퓨터에서 에이전트는 `omsagent`로 나열됩니다. `omsconfig`는 5분마다 새 포털 쪽 구성을 검색하는 Linux용 Log Analytics 에이전트 구성 에이전트입니다. 새 구성과 업데이트된 구성은 `/etc/opt/microsoft/omsagent/conf/omsagent.conf`에 있는 에이전트 구성 파일에 적용됩니다.

## <a name="install-the-agent-using-wrapper-script"></a>래퍼 스크립트를 사용하여 에이전트 설치

다음 단계는 직접 또는 프록시 서버를 통해 통신할 수 있는 Linux 컴퓨터용 래퍼 스크립트를 사용해 GitHub에서 호스트되는 에이전트를 다운로드하고 설치하는 Azure 및 Azure Government 클라우드의 Log Analytics 에이전트 설정을 구성합니다.  

Linux 컴퓨터에서 프록시 서버를 통해 Log Analytics에 통신해야 하는 경우 이 구성은 명령줄에 `-p [protocol://][user:password@]proxyhost[:port]`를 포함하여 지정할 수 있습니다. *protocol* 속성은 `http` 또는 `https`를 허용하며 *proxyhost* 속성은 프록시 서버의 정규화된 도메인 이름 또는 IP 주소를 허용합니다. 

예: `https://proxy01.contoso.com:30443`

두 경우 중 인증이 필요한 경우 사용자 이름 및 암호를 지정해야 합니다. 예: `https://user01:password@proxy01.contoso.com:30443`

1. Linux 컴퓨터를 Log Analytics 작업 영역에 연결하도록 구성하려면 작업 영역 ID 및 기본 키를 제공하는 다음 명령을 실행합니다. 다음 명령은 에이전트를 다운로드하고, 해당 체크섬의 유효성을 검사한 다음, 설치합니다.
    
    ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY>
    ```

    다음 명령에는 프록시 서버에서 인증이 필요한 경우 `-p` 프록시 매개 변수와 예제 구문이 포함되어 있습니다.

   ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -p [protocol://]<proxy user>:<proxy password>@<proxyhost>[:port] -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY>
    ```

2. Azure Government 클라우드에서 Linux 컴퓨터를 Log Analytics 작업 영역에 연결하도록 구성하려면 앞에서 복사한 작업 영역 ID 및 기본 키를 제공하는 다음 명령을 실행합니다. 다음 명령은 에이전트를 다운로드하고, 해당 체크섬의 유효성을 검사한 다음, 설치합니다. 

    ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY> -d opinsights.azure.us
    ``` 

    다음 명령에는 프록시 서버에서 인증이 필요한 경우 `-p` 프록시 매개 변수와 예제 구문이 포함되어 있습니다.

   ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -p [protocol://]<proxy user>:<proxy password>@<proxyhost>[:port] -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY> -d opinsights.azure.us
    ```
2. 다음 명령을 실행하여 에이전트를 다시 시작합니다. 

    ```
    sudo /opt/microsoft/omsagent/bin/service_control restart [<workspace id>]
    ``` 


## <a name="install-the-agent-manually"></a>수동으로 에이전트 설치

Linux용 Log Analytics 에이전트는 자동으로 압축이 풀리는 설치 가능한 셸 스크립트 번들로 제공됩니다. 이 번들은 각 에이전트 구성 요소에 대한 Debian 및 RPM 패키지를 포함하며, 직접 설치되거나 압축이 풀린 후에 개별 패키지를 검색하는 데 사용될 수 있습니다. x64 아키텍처용 번들 1개와 x86 아키텍처용 번들 1개가 제공됩니다. 

> [!NOTE]
> Azure VM의 경우 Linux용 [Azure Log Analytics VM 확장](../../virtual-machines/extensions/oms-linux.md)을 사용하여 에이전트를 설치하는 것이 좋습니다. 

1. scp/sftp를 사용하여 적절한 번들(x64 또는 x86)을 [다운로드](https://github.com/microsoft/OMS-Agent-for-Linux#azure-install-guide)하고 Linux VM 또는 물리적 컴퓨터에 전송합니다.

2. `--install` 인수를 사용하여 번들을 설치합니다. 설치 중에 Log Analytics 작업 영역에 온보딩하려면 앞서 복사한 `-w <WorkspaceID>` 및 `-s <workspaceKey>` 매개 변수를 제공합니다.

    >[!NOTE]
    >Linux용 System Center Operations Manager 에이전트가 이미 설치되어 있는 경우처럼 omi, scx, omsconfig 또는 이전 버전과 같은 종속 패키지가 설치된 경우에는 `--upgrade` 인수를 사용해야 합니다. 

    ```
    sudo sh ./omsagent-*.universal.x64.sh --install -w <workspace id> -s <shared key>
    ```

3. Log Analytics 게이트웨이를 통해 Log Analytics 작업 영역에 Linux 에이전트를 설치하고 연결하도록 구성하려면 프록시, 작업 영역 ID 및 작업 영역 키 매개 변수를 제공하는 다음 명령을 실행합니다. 이 구성은 `-p [protocol://][user:password@]proxyhost[:port]`를 포함하여 명령줄에서 지정할 수 있습니다. *proxyhost* 속성은 Log Analytics 게이트웨이 서버의 정규화된 도메인 이름 또는 IP 주소를 허용합니다.  

    ```
    sudo sh ./omsagent-*.universal.x64.sh --upgrade -p https://<proxy address>:<proxy port> -w <workspace id> -s <shared key>
    ```

    인증이 필요한 경우 사용자 이름 및 암호를 지정해야 합니다. 예를 들면 다음과 같습니다. 
    
    ```
    sudo sh ./omsagent-*.universal.x64.sh --upgrade -p https://<proxy user>:<proxy password>@<proxy address>:<proxy port> -w <workspace id> -s <shared key>
    ```

4. Azure Government 클라우드에서 Linux 컴퓨터를 Log Analytics 작업 영역에 연결하도록 구성하려면 앞에서 복사한 작업 영역 ID 및 기본 키를 제공하는 다음 명령을 실행합니다.

    ```
    sudo sh ./omsagent-*.universal.x64.sh --upgrade -w <workspace id> -s <shared key> -d opinsights.azure.us
    ```

에이전트 패키지를 설치하고 나중에 특정 Log Analytics 작업 영역에 보고하도록 구성하려면 다음 명령을 실행합니다.

```
sudo sh ./omsagent-*.universal.x64.sh --upgrade
```

에이전트를 설치하지 않고 번들에서 에이전트 패키지를 추출하려면 다음 명령을 실행합니다.

```
sudo sh ./omsagent-*.universal.x64.sh --extract
```

## <a name="upgrade-from-a-previous-release"></a>이전 릴리스에서 업그레이드

버전 1.0.0-47부터 이전 버전에서의 업그레이드가 각 릴리스에서 지원됩니다. `--upgrade` 매개 변수를 사용하여 설치를 수행하면 에이전트의 모든 구성 요소가 최신 버전으로 업그레이드됩니다.

## <a name="cache-information"></a>캐시 정보
Linux용 Log Analytics 에이전트의 데이터가 Azure Monitor로 전송되기 전에 *%STATE_DIR_WS%/out_oms_common*.buffer*로 로컬 머신에 캐시됩니다. 사용자 지정 로그 데이터가 *%STATE_DIR_WS%/out_oms_blob*.buffer*에 버퍼링됩니다. 일부 [솔루션 및 데이터 형식](https://github.com/microsoft/OMS-Agent-for-Linux/search?utf8=%E2%9C%93&q=+buffer_path&type=)에 대한 경로가 다를 수 있습니다.

에이전트는 20초마다 업로드를 시도합니다. 실패할 경우 성공할 때까지 점점 대기 시간이 길어집니다. 두 번째 시도 전 30초, 세 번째 시도 전 60초, 120초 등으로 다시 연결될 때까지 재시도 간 대기 시간은 최대 16분이 될 수 있습니다. 에이전트는 주어진 데이터 청크를 삭제하고 다음으로 이동하기 전에 최대 6회까지 다시 시도합니다. 에이전트가 다시 업로드될 때까지 계속됩니다. 즉, 삭제되기 전에 약 30분까지 데이터가 버퍼링될 수 있습니다.

기본 캐시 크기는 10MB이지만 [omsagent.conf 파일](https://github.com/microsoft/OMS-Agent-for-Linux/blob/e2239a0714ae5ab5feddcc48aa7a4c4f971417d4/installer/conf/omsagent.conf)에서 수정할 수 있습니다.


## <a name="next-steps"></a>다음 단계

- [Windows 및 Linux용 Log Analytics 에이전트 관리 및 유지 관리](agent-manage.md)를 검토하여 가상 머신에서 에이전트를 다시 구성, 업그레이드 또는 제거하는 방법을 알아봅니다.

- 에이전트를 설치하거나 관리하는 중에 문제가 발생하면 [Troubleshooting the Linux agent](agent-linux-troubleshoot.md)(Linux 에이전트 문제 해결)를 참조하세요.
