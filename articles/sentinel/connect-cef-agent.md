---
title: 로그 전달자를 배포하여 CEF 데이터를 Azure Sentinel에 연결 | Microsoft Docs
description: 에이전트를 배포하여 CEF 데이터를 Azure Sentinel에 연결하는 방법을 알아봅니다.
services: sentinel
documentationcenter: na
author: batamig
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/05/2021
ms.author: bagol
ms.openlocfilehash: 2acbc6c48826f9455e264690789dc823da5a762c
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122567250"
---
# <a name="step-1-deploy-the-log-forwarder"></a>1단계: 로그 전달자 배포


이 단계에서는 보안 솔루션의 로그를 Azure Sentinel 작업 영역으로 전달하는 Linux 컴퓨터를 지정하고 구성합니다. 이 컴퓨터는 온-프레미스 환경, Azure VM 또는 다른 클라우드의 VM에서 물리적 컴퓨터 또는 가상 머신일 수 있습니다. 제공된 링크를 사용하여 다음 작업을 수행하는 지정된 머신에서 스크립트를 실행합니다.

- Linux용 Log Analytics 에이전트(OMS 에이전트라고도 함)를 설치하고 다음과 같은 목적으로 구성합니다.
    - TCP 포트 25226의 기본 제공 Linux Syslog 디먼에서 CEF 메시지 수신 대기
    - TLS를 통해 구문 분석 및 보강을 하는 Azure Sentinel 작업 영역에 안전하게 메시지 보내기

- 다음 목적을 위해 기본 제공 Linux Syslog 디먼(rsyslog.d/syslog-ng)을 구성합니다.
    - TCP 포트 514의 보안 솔루션에서 Syslog 메시지 수신 대기
    - TCP 포트 25226을 사용하여 localhost의 Log Analytics 에이전트에 대한 CEF로 식별되는 메시지만 전달
 
## <a name="prerequisites"></a>필수 구성 요소

- 지정된 Linux 컴퓨터에 상승된 권한(sudo)이 있어야 합니다.

- Linux 컴퓨터에 **python 2.7** 또는 **3** 이 설치되어 있어야 합니다.<br>`python --version` 또는 `python3 --version` 명령을 사용하여 확인합니다.

- Log Analytics 에이전트를 설치하기 전에 Linux 컴퓨터가 Azure 작업 영역에 연결되어 있지 않아야 합니다.

- Linux 컴퓨터에는 최소 **4개의 CPU 코어와 8GB RAM** 이 있어야 합니다.

    > [!NOTE]
    > - **rsyslog** 디먼을 사용하는 단일 로그 전달자 컴퓨터에는 수집된 **EPS(초당 이벤트)가 최대 8500개까지** 지원되는 용량이 있습니다.

- 이 프로세스의 어느 시점에서 작업 영역 ID 및 작업 영역 기본 키가 필요할 수 있습니다. 작업 영역 리소스의 **에이전트 관리** 에서 찾을 수 있습니다.

## <a name="run-the-deployment-script"></a>배포 스크립트 실행
 
1. Azure Sentinel 탐색 메뉴에서 **데이터 커넥터** 를 클릭합니다. 커넥터 목록에서 **CEF(Common Event Format)** 타일을 클릭한 다음 오른쪽 아래에 있는 **커넥터 페이지 열기** 단추를 클릭합니다. 

1. **1.2 Linux 컴퓨터에 CEF 수집기를 설치** 에서 **다음 스크립트를 실행하여 CEF 수집기 설치 및 적용** 또는 아래의 텍스트를 통해 제공된 링크를 복사합니다(자리 표시자 위치에 작업 영역 ID 및 기본 키 적용).

    ```bash
    sudo wget -O cef_installer.py https://raw.githubusercontent.com/Azure/Azure-Sentinel/master/DataConnectors/CEF/cef_installer.py&&sudo python cef_installer.py [WorkspaceID] [Workspace Primary Key]
    ```

1. 스크립트를 실행하는 동안 오류 또는 경고 메시지가 표시되지 않는지 확인합니다.
    - *컴퓨터* 필드의 매핑 문제를 해결하기 위해 명령을 실행하라고 지시하는 메시지가 표시될 수 있습니다. 자세한 내용은 [배포 스크립트의 설명](#mapping-command)을 참조하세요.

1. [2단계: CEF 메시지를 전달하도록 보안 솔루션 구성](connect-cef-solution-config.md)을 계속 진행합니다.


> [!NOTE]
> **동일한 컴퓨터를 사용하여 일반 Syslog *및* CEF 메시지** 를 모두 전달
>
> 이 로그 전달자 컴퓨터를 사용하여 [Syslog 메시지](connect-syslog.md)와 CEF를 전달하려는 경우 Syslog 및 CommonSecurityLog 테이블에 대한 이벤트 중복을 방지하려면 다음을 수행합니다.
>
> 1. CEF 형식의 전달자에게 로그를 보내는 각 원본 컴퓨터에서 Syslog 구성 파일을 편집하여 CEF 메시지를 보내는 데 사용되는 기능을 제거해야 합니다. 또한 이렇게 하면 CEF에서 보내는 기능이 Syslog로 전송되지 않습니다. 이 작업을 수행하는 방법에 대한 자세한 지침은 [Linux 에이전트에서 Syslog 구성](../azure-monitor/agents/data-sources-syslog.md#configure-syslog-on-linux-agent)을 참조하세요.
>
> 1. Azure Sentinel에서 Syslog 구성과 에이전트의 동기화를 사용하지 않도록 설정하려면 해당 컴퓨터에서 다음 명령을 실행해야 합니다. 이렇게 하면 이전 단계에서 변경한 구성을 덮어쓰지 않습니다.<br>
> `sudo su omsagent -c 'python /opt/microsoft/omsconfig/Scripts/OMS_MetaConfigHelper.py --disable'`

> [!NOTE]
> **TimeGenerated 필드의 원본 변경**
>
> - 기본적으로 Log Analytics 에이전트는 스키마의 *TimeGenerated* 필드를 에이전트가 Syslog 디먼에서 이벤트를 받은 시간으로 채웁니다. 따라서 원본 시스템에서 이벤트가 생성된 시간은 Azure Sentinel에 기록되지 않습니다.
>
> - 그러나 `TimeGenerated.py` 스크립트를 다운로드하고 실행하면 다음 명령을 실행할 수 있습니다. 이 스크립트는 *Timegenerated* 필드를 에이전트에서 받은 시간 대신 원본 시스템의 이벤트 원래 시간으로 채우도록 Log Analytics 에이전트를 구성합니다.
>
>    ```bash
>    wget -O TimeGenerated.py https://raw.githubusercontent.com/Azure/Azure-Sentinel/master/DataConnectors/CEF/TimeGenerated.py && python TimeGenerated.py {ws_id}
>    ```

## <a name="deployment-script-explained"></a>배포 스크립트 설명

다음은 배포 스크립트의 작업에 대한 명령별 설명입니다.

Syslog 디먼을 선택하여 적절한 설명을 확인합니다.

# <a name="rsyslog-daemon"></a>[rsyslog 디먼](#tab/rsyslog)

1. **Log Analytics 에이전트 다운로드 및 설치:**

    - Log Analytics(OMS) Linux 에이전트에 대한 설치 스크립트를 다운로드합니다.

        ```bash
        wget -O onboard_agent.sh https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/
            master/installer/scripts/onboard_agent.sh
        ```

    - Log Analytics 에이전트를 설치합니다.
    
        ```bash
        sh onboard_agent.sh -w [workspaceID] -s [Primary Key] -d opinsights.azure.com
        ```

1. **Log Analytics 에이전트 구성이 포트 25226에서 수신 대기하도록 설정하고 CEF 메시지를 Azure Sentinel로 전달합니다.**

    - Log Analytics 에이전트 GitHub 리포지토리에서 구성을 다운로드합니다.

        ```bash
        wget -O /etc/opt/microsoft/omsagent/[workspaceID]/conf/omsagent.d/security_events.conf
            https://raw.githubusercontent.com/microsoft/OMS-Agent-for-Linux/master/installer/conf/
            omsagent.d/security_events.conf
        ```

1. **Syslog 디먼 구성:**

    - `/etc/rsyslog.conf` Syslog 구성 파일을 사용하여 TCP 통신용 포트 514를 엽니다.

    - `security-config-omsagent.conf` 특수 구성 파일을 `/etc/rsyslog.d/` Syslog 디먼 디렉터리에 삽입하여 CEF 메시지를 TCP 포트 25226의 Log Analytics 에이전트로 전달하도록 디먼을 구성합니다.

        `security-config-omsagent.conf` 파일의 콘텐츠:

        ```bash
        if $rawmsg contains "CEF:" or $rawmsg contains "ASA-" then @@127.0.0.1:25226 
        ```

1. **Syslog 디먼 및 Log Analytics 에이전트 다시 시작:**

    - rsyslog 디먼을 다시 시작합니다.
    
        ```bash
        service rsyslog restart
        ```

    - Log Analytics 에이전트를 다시 시작합니다.

        ```bash
        /opt/microsoft/omsagent/bin/service_control restart [workspaceID]
        ```

1. **예상대로 *컴퓨터* 필드의 매핑 확인:**

    - 다음 명령을 사용하여 Syslog 원본의 *컴퓨터* 필드가 Log Analytics 에이전트에 제대로 매핑되는지 확인합니다. 

        ```bash
        grep -i "'Host' => record\['host'\]"  /opt/microsoft/omsagent/plugin/filter_syslog_security.rb
        ```
    - <a name="mapping-command"></a>매핑에 문제가 있는 경우 스크립트는 **다음 명령을 수동으로 실행** 하도록 지시하는 오류 메시지를 생성합니다(자리 표시자 대신 작업 영역 ID 적용). 이 명령은 올바른 매핑을 확인하고 에이전트를 다시 시작합니다.
    
        ```bash
        sudo sed -i -e "/'Severity' => tags\[tags.size - 1\]/ a \ \t 'Host' => record['host']" -e "s/'Severity' => tags\[tags.size - 1\]/&,/" /opt/microsoft/omsagent/plugin/filter_syslog_security.rb && sudo /opt/microsoft/omsagent/bin/service_control restart [workspaceID]
        ```

# <a name="syslog-ng-daemon"></a>[syslog-ng 디먼](#tab/syslogng)

1. **Log Analytics 에이전트 다운로드 및 설치:**

    - Log Analytics(OMS) Linux 에이전트에 대한 설치 스크립트를 다운로드합니다.

        ```bash
        wget -O onboard_agent.sh https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/
            master/installer/scripts/onboard_agent.sh
        ```

    - Log Analytics 에이전트를 설치합니다.
    
        ```bash
        sh onboard_agent.sh -w [workspaceID] -s [Primary Key] -d opinsights.azure.com
        ```

1. **Log Analytics 에이전트 구성이 포트 25226에서 수신 대기하도록 설정하고 CEF 메시지를 Azure Sentinel로 전달합니다.**

    - Log Analytics 에이전트 GitHub 리포지토리에서 구성을 다운로드합니다.

        ```bash
        wget -O /etc/opt/microsoft/omsagent/[workspaceID]/conf/omsagent.d/security_events.conf
            https://raw.githubusercontent.com/microsoft/OMS-Agent-for-Linux/master/installer/conf/
            omsagent.d/security_events.conf
        ```

1. **Syslog 디먼 구성:**

    - `/etc/syslog-ng/syslog-ng.conf` Syslog 구성 파일을 사용하여 TCP 통신용 포트 514를 엽니다.

    - `security-config-omsagent.conf` 특수 구성 파일을 `/etc/syslog-ng/conf.d/` Syslog 디먼 디렉터리에 삽입하여 CEF 메시지를 TCP 포트 25226의 Log Analytics 에이전트로 전달하도록 디먼을 구성합니다.

        `security-config-omsagent.conf` 파일의 콘텐츠:

        ```bash
        filter f_oms_filter {match(\"CEF\|ASA\" ) ;};destination oms_destination {tcp(\"127.0.0.1\" port(25226));};
        log {source(s_src);filter(f_oms_filter);destination(oms_destination);};
        ```

1. **Syslog 디먼 및 Log Analytics 에이전트 다시 시작:**

    - syslog-ng 디먼을 다시 시작합니다.
    
        ```bash
        service syslog-ng restart
        ```

    - Log Analytics 에이전트를 다시 시작합니다.

        ```bash
        /opt/microsoft/omsagent/bin/service_control restart [workspaceID]
        ```

1. **예상대로 *컴퓨터* 필드의 매핑 확인:**

    - 다음 명령을 사용하여 Syslog 원본의 *컴퓨터* 필드가 Log Analytics 에이전트에 제대로 매핑되는지 확인합니다. 

        ```bash
        grep -i "'Host' => record\['host'\]"  /opt/microsoft/omsagent/plugin/filter_syslog_security.rb
        ```
    - <a name="mapping-command"></a>매핑에 문제가 있는 경우 스크립트는 **다음 명령을 수동으로 실행** 하도록 지시하는 오류 메시지를 생성합니다(자리 표시자 대신 작업 영역 ID 적용). 이 명령은 올바른 매핑을 확인하고 에이전트를 다시 시작합니다.
    
        ```bash
        sed -i -e "/'Severity' => tags\[tags.size - 1\]/ a \ \t 'Host' => record['host']" -e "s/'Severity' => tags\[tags.size - 1\]/&,/" /opt/microsoft/omsagent/plugin/filter_syslog_security.rb && sudo /opt/microsoft/omsagent/bin/service_control restart [workspaceID]
        ```
---

## <a name="next-steps"></a>다음 단계

이 문서에서는 Log Analytics 에이전트를 배포하여 CEF 어플라이언스를 Azure Sentinel에 연결하는 방법을 알아보았습니다. Azure Sentinel에 대한 자세한 내용은 다음 문서를 참조하세요.

- [CEF 및 CommonSecurityLog 필드 매핑](cef-name-mapping.md)에 대해 알아봅니다.
- [데이터에 대한 가시성을 얻고 재적 위협을 확인](get-visibility.md)하는 방법을 알아봅니다.
- [Azure Sentinel을 사용하여 위협 검색](./detect-threats-built-in.md)을 시작합니다.
