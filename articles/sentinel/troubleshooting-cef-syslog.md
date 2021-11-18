---
title: Microsoft 센티널과 CEF 또는 Syslog 데이터 커넥터 간 연결 문제 해결 | Microsoft Docs
description: Microsoft 센티널 CEF 또는 Syslog 데이터 커넥터를 사용 하 여 문제를 해결 하는 방법을 알아봅니다.
services: sentinel
documentationcenter: na
author: batamig
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/09/2021
ms.author: bagol
ms.custom: ignite-fall-2021
ms.openlocfilehash: 4bdc3272fa114d4c270d906b03226a17d52bf893
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2021
ms.locfileid: "132711518"
---
# <a name="troubleshoot-your-cef-or-syslog-data-connector"></a>CEF 또는 Syslog 데이터 커넥터 문제 해결

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

이 문서에서는 Microsoft 센티널의 CEF 또는 Syslog 데이터 커넥터를 확인 하 고 문제를 해결 하는 일반적인 방법을 설명 합니다.

예를 들어 로그가 Microsoft 센티널에 표시 되지 않는 경우 Syslog 또는 일반 보안 로그 테이블에서 데이터 원본이 연결에 실패 하거나 데이터가 수집 되지 않는 또 다른 이유가 있을 수 있습니다.

커넥터가 배포되지 않았을 때의 다른 증상으로, **security_events.conf** 또는 **security-omsagent.config.conf** 파일이 누락되었거나 rsyslog 서버가 포트 514에서 수신 대기 중이지 않은 경우가 있습니다.

자세한 내용은 [Common Event Format을 사용하여 외부 솔루션 연결](connect-common-event-format.md) 및 [Syslog를 사용하여 Linux 기반 소스에서 데이터 수집](connect-syslog.md)을 참조하세요.

> [!NOTE]
> Windows용 Log Analytics 에이전트를 종종 *MMA(Microsoft Monitoring Agent)* 라고 합니다. Linux용 Log Analytics 에이전트를 ‘OMS 에이전트’라고도 합니다.
>

> [!TIP]
> 문제를 해결할 때 Syslog 수집기, 운영 체제 또는 OMS 에이전트의 문제를 확인하고 해결하기 위해, 이 문서의 단계를 제시된 순서대로 진행하는 것이 좋습니다.
>
> 문서화된 프로시저와 다른 방법으로 커넥터를 배포했고 문제가 발생하는 경우, 배포를 제거하고 문서에 나온 대로 다시 설치하는 것이 좋습니다.
>

## <a name="validate-cef-connectivity"></a>CEF 연결 유효성 검사

[로그 전달자를 배포](connect-common-event-format.md) 하 고 [보안 솔루션을 구성 하 여 cef 메시지를 보내도록](./connect-common-event-format.md)한 후에는이 섹션의 단계를 사용 하 여 보안 솔루션과 Microsoft 센티널 간의 연결을 확인 합니다.

1. 다음 필수 조건을 갖추고 있는지 확인합니다.

    - 로그 전달자 머신에 상승된 권한(sudo)이 있어야 합니다.

    - 로그 전달자 머신에 **python 2.7** 또는 **3** 이 설치되어 있어야 합니다. `python –version` 명령을 사용하여 확인합니다.

    - 이 프로세스 중에 작업 영역 ID와 작업 영역 기본 키가 필요할 수 있습니다. 이는 작업 영역 리소스의 **에이전트 관리** 에서 찾을 수 있습니다.

1. Microsoft 센티널 탐색 메뉴에서 **로그** 를 엽니다. **CommonSecurityLog** 스키마로 쿼리를 실행하여 보안 솔루션에서 로그를 받는지 확인합니다.

    로그가 **Log Analytics** 에 표시될 때까지 20분가량 소요될 수 있습니다.

1. 쿼리에서 결과가 나타나지 않으면 보안 솔루션에서 이벤트가 생성되고 있는지 확인하거나, 이벤트를 생성해 보고 이벤트가 지정한 Syslog 전달자 머신으로 전달되는지 확인합니다.

1. 로그 전달자 (자리 표시자 대신 작업 영역 ID 적용)에서 다음 스크립트를 실행 하 여 보안 솔루션, 로그 전달자 및 Microsoft 센티널 간의 연결을 확인 합니다. 이 스크립트는 디먼이 올바른 포트에서 수신 대기하고 있는지, 전달이 제대로 구성되었는지, 디먼과 Log Analytics 에이전트 간의 통신을 차단하는 것이 없는지 확인합니다. 또한 ‘TestCommonEventFormat’ 모의 메시지를 전송하여 엔드투엔드 연결을 확인합니다. <br>

    ```bash
    sudo wget -O cef_troubleshoot.py https://raw.githubusercontent.com/Azure/Azure-Sentinel/master/DataConnectors/CEF/cef_troubleshoot.py&&sudo python cef_troubleshoot.py [WorkspaceID]
    ```

   - **‘컴퓨터’ 필드의 매핑** 문제를 해결하기 위해 명령을 실행하라고 지시하는 메시지가 표시될 수 있습니다. 자세한 내용은 [유효성 검사 스크립트 설명](#mapping-command)을 참조하세요.

    - **Cisco ASA 방화벽 로그 구문 분석** 문제를 해결하기 위해 명령을 실행하라고 지시하는 메시지가 표시될 수 있습니다. 자세한 내용은 [유효성 검사 스크립트 설명](#parsing-command)을 참조하세요.

### <a name="cef-validation-script-explained"></a>CEF 유효성 검사 스크립트 설명

유효성 검사 스크립트는 다음 검사를 수행합니다.

# <a name="rsyslog-daemon"></a>[rsyslog 디먼](#tab/rsyslog)

1. 다음 파일이<br>
    `/etc/opt/microsoft/omsagent/[WorkspaceID]/conf/omsagent.d/security_events.conf`<br>
    존재하고 유효한지 확인합니다.

1. 파일에 다음 텍스트가 포함되어 있는지 확인합니다.

    ```bash
    <source>
        type syslog
        port 25226
        bind 127.0.0.1
        protocol_type tcp
        tag oms.security
        format /(?<time>(?:\w+ +){2,3}(?:\d+:){2}\d+|\d{4}-\d{2}-\d{2}T\d{2}:\d{2}:\d{2}.[\w\-\:\+]{3,12}):?\s*(?:(?<host>[^: ]+) ?:?)?\s*(?<ident>.*CEF.+?(?=0\|)|%ASA[0-9\-]{8,10})\s*:?(?<message>0\|.*|.*)/
        <parse>
            message_format auto
        </parse>
    </source>

    <filter oms.security.**>
        type filter_syslog_security
    </filter>
    ```

1. 다음 명령을 사용하여 Cisco ASA 방화벽 이벤트에 대한 구문 분석이 예상대로 구성되었는지 확인합니다.

    ```bash
    grep -i "return ident if ident.include?('%ASA')" /opt/microsoft/omsagent/plugin/security_lib.rb
    ```

    - <a name="parsing-command"></a>구문 분석에 문제가 있는 경우 스크립트는 **다음 명령을 수동으로 실행** 하도록 지시하는 오류 메시지를 생성합니다(자리 표시자 대신 작업 영역 ID 적용). 해당 명령은 올바른 구문 분석을 보장하고 에이전트를 다시 시작합니다.

        ```bash
        # Cisco ASA parsing fix
        sed -i "s|return '%ASA' if ident.include?('%ASA')|return ident if ident.include?('%ASA')|g" /opt/microsoft/omsagent/plugin/security_lib.rb && sudo /opt/microsoft/omsagent/bin/service_control restart [workspaceID]
        ```

1. 다음 명령을 사용하여 syslog 원본의 ‘컴퓨터’ 필드가 Log Analytics 에이전트에 올바르게 매핑되었는지 확인합니다.

    ```bash
    grep -i "'Host' => record\['host'\]"  /opt/microsoft/omsagent/plugin/filter_syslog_security.rb
    ```

    - <a name="mapping-command"></a>매핑에 문제가 있는 경우 스크립트는 **다음 명령을 수동으로 실행** 하도록 지시하는 오류 메시지를 생성합니다(자리 표시자 대신 작업 영역 ID 적용). 이 명령은 매핑이 올바른지 확인하고 에이전트를 다시 시작합니다.

        ```bash
        # Computer field mapping fix
        sed -i -e "/'Severity' => tags\[tags.size - 1\]/ a \ \t 'Host' => record['host']" -e "s/'Severity' => tags\[tags.size - 1\]/&,/" /opt/microsoft/omsagent/plugin/filter_syslog_security.rb && sudo /opt/microsoft/omsagent/bin/service_control restart [workspaceID]
        ```

1. 네트워크 트래픽(예: 호스트 방화벽)을 차단할 수 있는 보안 강화 기능이 머신에 있는지 확인합니다.

1. syslog 디먼(rsyslog)이 TCP 포트 25226의 Log Analytics 에이전트에 CEF로 식별된 메시지를 전송하도록 적절하게 구성되었는지 확인합니다.

    - 구성 파일: `/etc/rsyslog.d/security-config-omsagent.conf`

        ```bash
        if $rawmsg contains "CEF:" or $rawmsg contains "ASA-" then @@127.0.0.1:25226
        ```

1. syslog 디먼 및 Log Analytics 에이전트 다시 시작.

    ```bash
    service rsyslog restart

    /opt/microsoft/omsagent/bin/service_control restart [workspaceID]
    ```

1. 데이터 수신을 위한 tcp 514, syslog 디먼과 Log Analytics 에이전트 간의 내부 통신을 위한 tcp 25226 등 필요한 연결이 설정되었는지 확인합니다.

    ```bash
    netstat -an | grep 514

    netstat -an | grep 25226
    ```

1. syslog 디먼이 포트 514에서 데이터를 수신하고 있는지, 에이전트가 포트 25226에서 데이터를 수신하고 있는지 확인합니다.

    ```bash
    sudo tcpdump -A -ni any port 514 -vv

    sudo tcpdump -A -ni any port 25226 -vv
    ```

1. MOCK 데이터를 localhost의 포트 514로 보냅니다. 이 데이터는 다음 쿼리를 실행 하 여 Microsoft 센티널 작업 영역에서 관찰 가능 해야 합니다.

    ```kusto
    CommonSecurityLog
    | where DeviceProduct == "MOCK"
    ```

# <a name="syslog-ng-daemon"></a>[syslog-ng daemon](#tab/syslogng)

1. 다음 파일이<br>
    `/etc/opt/microsoft/omsagent/[WorkspaceID]/conf/omsagent.d/security_events.conf`<br>
    존재하고 유효한지 확인합니다.

1. 파일에 다음 텍스트가 포함되어 있는지 확인합니다.

    ```bash
    <source>
        type syslog
        port 25226
        bind 127.0.0.1
        protocol_type tcp
        tag oms.security
        format /(?<time>(?:\w+ +){2,3}(?:\d+:){2}\d+|\d{4}-\d{2}-\d{2}T\d{2}:\d{2}:\d{2}.[\w\-\:\+]{3,12}):?\s*(?:(?<host>[^: ]+) ?:?)?\s*(?<ident>.*CEF.+?(?=0\|)|%ASA[0-9\-]{8,10})\s*:?(?<message>0\|.*|.*)/
        <parse>
            message_format auto
        </parse>
    </source>

    <filter oms.security.**>
        type filter_syslog_security
    </filter>
    ```

1. 다음 명령을 사용하여 Cisco ASA 방화벽 이벤트에 대한 구문 분석이 예상대로 구성되었는지 확인합니다.

    ```bash
    grep -i "return ident if ident.include?('%ASA')" /opt/microsoft/omsagent/plugin/security_lib.rb
    ```

    - <a name="parsing-command"></a>구문 분석에 문제가 있는 경우 스크립트는 **다음 명령을 수동으로 실행** 하도록 지시하는 오류 메시지를 생성합니다(자리 표시자 대신 작업 영역 ID 적용). 해당 명령은 올바른 구문 분석을 보장하고 에이전트를 다시 시작합니다.

        ```bash
        # Cisco ASA parsing fix
        sed -i "s|return '%ASA' if ident.include?('%ASA')|return ident if ident.include?('%ASA')|g" /opt/microsoft/omsagent/plugin/security_lib.rb && sudo /opt/microsoft/omsagent/bin/service_control restart [workspaceID]
        ```

1. 다음 명령을 사용하여 syslog 원본의 ‘컴퓨터’ 필드가 Log Analytics 에이전트에 올바르게 매핑되었는지 확인합니다.

    ```bash
    grep -i "'Host' => record\['host'\]"  /opt/microsoft/omsagent/plugin/filter_syslog_security.rb
    ```

    - <a name="mapping-command"></a>매핑에 문제가 있는 경우 스크립트는 **다음 명령을 수동으로 실행** 하도록 지시하는 오류 메시지를 생성합니다(자리 표시자 대신 작업 영역 ID 적용). 이 명령은 매핑이 올바른지 확인하고 에이전트를 다시 시작합니다.

        ```bash
        # Computer field mapping fix
        sed -i -e "/'Severity' => tags\[tags.size - 1\]/ a \ \t 'Host' => record['host']" -e "s/'Severity' => tags\[tags.size - 1\]/&,/" /opt/microsoft/omsagent/plugin/filter_syslog_security.rb && sudo /opt/microsoft/omsagent/bin/service_control restart [workspaceID]
        ```

1. 네트워크 트래픽(예: 호스트 방화벽)을 차단할 수 있는 보안 강화 기능이 머신에 있는지 확인합니다.

1. syslog 디먼(syslog-ng)이 TCP 포트 25226의 Log Analytics 에이전트로 CEF(regex 사용)로 식별된 메시지를 전송하도록 적절하게 구성되었는지 확인합니다.

    - 구성 파일: `/etc/syslog-ng/conf.d/security-config-omsagent.conf`

        ```bash
        filter f_oms_filter {match(\"CEF\|ASA\" ) ;};destination oms_destination {tcp(\"127.0.0.1\" port(25226));};
        log {source(s_src);filter(f_oms_filter);destination(oms_destination);};
        ```

1. syslog 디먼 및 Log Analytics 에이전트 다시 시작.

    ```bash
    service syslog-ng restart

    /opt/microsoft/omsagent/bin/service_control restart [workspaceID]
    ```

1. 데이터 수신을 위한 tcp 514, syslog 디먼과 Log Analytics 에이전트 간의 내부 통신을 위한 tcp 25226 등 필요한 연결이 설정되었는지 확인합니다.

    ```bash
    netstat -an | grep 514

    netstat -an | grep 25226
    ```

1. syslog 디먼이 포트 514에서 데이터를 수신하고 있는지, 에이전트가 포트 25226에서 데이터를 수신하고 있는지 확인합니다.

    ```bash
    sudo tcpdump -A -ni any port 514 -vv

    sudo tcpdump -A -ni any port 25226 -vv
    ```

1. MOCK 데이터를 localhost의 포트 514로 보냅니다. 이 데이터는 다음 쿼리를 실행 하 여 Microsoft 센티널 작업 영역에서 관찰 가능 해야 합니다.

    ```kusto
    CommonSecurityLog
    | where DeviceProduct == "MOCK"
    ```
---

## <a name="verify-cef-or-syslog-prerequisites"></a>CEF 또는 Syslog 필수 조건 확인

다음 섹션을 통해 CEF 또는 Syslog 데이터 커넥터 필수 조건을 확인합니다.

### <a name="azure-virtual-machine-as-a-syslog-collector"></a>Syslog 수집기로서의 Azure Virtual Machine

Azure Virtual Machine을 Syslog 수집기로 사용하는 경우 다음을 확인합니다.

- Syslog 데이터 커넥터를 설정 하는 동안 [MMA/OMS 에이전트](connect-windows-security-events.md#connector-options)에 대 한 [클라우드 자동 프로 비전 설정에 대해 Microsoft Defender](../security-center/security-center-enable-data-collection.md) 를 해제 해야 합니다.

    이는 데이터 커넥터가 완전히 설정된 후 다시 설정할 수 있습니다.

- [Common Event Format 데이터 커넥터 Python 스크립트](./connect-log-forwarder.md)를 배포하기 전에 가상 머신이 기존 Syslog 작업 영역에 벌써 연결되어 있지 않은지 확인합니다. 이 정보는 Log Analytics 작업 영역 가상 머신 목록에서 찾을 수 있습니다. 여기서 Syslog 작업 영역에 연결된 VM은 **연결됨** 으로 나열됩니다.

- Microsoft 센티널이 **Securityinsights** 솔루션을 설치 하 여 올바른 Syslog 작업 영역에 연결 되어 있는지 확인 합니다.

    자세한 내용은 [1단계: 로그 전달자 배포](./connect-log-forwarder.md)를 참조하세요.

- 머신의 크기가 최소 필수 조건 이상으로 올바르게 설정되었는지 확인합니다. 자세한 내용은 [CEF 필수 조건](connect-common-event-format.md#prerequisites)을 참조하세요.

### <a name="on-premises-or-a-non-azure-virtual-machine"></a>온-프레미스 또는 비 Azure 가상 머신

데이터 커넥터에 온-프레미스 머신 또는 비 Azure 가상 머신을 사용하는 경우 지원되는 Linux 운영 체제를 새로 설치할 때 설치 스크립트를 실행했는지 확인하세요.

> [!TIP]
> Microsoft 센티널의 **일반 이벤트 형식** 데이터 커넥터 페이지에서이 스크립트를 찾을 수도 있습니다.
>

```cli
sudo wget -O cef_installer.py https://raw.githubusercontent.com/Azure/Azure-Sentinel/master/DataConnectors/CEF/cef_installer.py&&sudo python cef_installer.py <WorkspaceId> <Primary Key>
```

### <a name="enable-your-syslog-facility-and-log-severity-collection"></a>Syslog 기능 및 로그 심각도 수집 사용

Syslog 서버(rsyslog 또는 syslog-ng)는 관련 구성 파일에 정의된 모든 데이터를 전달합니다. 이 데이터는 Log Analytics 작업 영역에 정의된 설정에 따라 자동으로 채워집니다.

Microsoft 센티널로 수집 하려는 시설 및 심각도 로그 수준에 대 한 세부 정보를 추가 해야 합니다. 구성 프로세스가 완료될 때까지 약 20분 정도 걸립니다.

자세한 내용은 [배포 스크립트 설명](./connect-log-forwarder.md#deployment-script-explained) 및 [Azure Portal에서 Syslog 구성](../azure-monitor/agents/data-sources-syslog.md)을 참조하세요.


**예를 들어 rsyslog 서버의 경우** 다음 명령을 실행하여 Syslog 전달에 대한 현재 설정을 표시하고 구성 파일의 변경 내용을 검토합니다.

```bash
cat /etc/rsyslog.d/95-omsagent.conf
```

이 경우 rsyslog에 대해서는 다음과 유사한 출력이 표시됩니다. 이 파일의 내용은 **Log Analytics 작업 영역 클라이언트 구성 - Syslog 기능 설정** 화면의 Syslog 구성에 정의된 내용을 반영해야 합니다.


```bash
OMS Syslog collection for workspace c69fa733-da2e-4cf9-8d92-eee3bd23fe81
auth.=alert;auth.=crit;auth.=debug;auth.=emerg;auth.=err;auth.=info;auth.=notice;auth.=warning  @127.0.0.1:25224
authpriv.=alert;authpriv.=crit;authpriv.=debug;authpriv.=emerg;authpriv.=err;authpriv.=info;authpriv.=notice;authpriv.=warning  @127.0.0.1:25224
cron.=alert;cron.=crit;cron.=debug;cron.=emerg;cron.=err;cron.=info;cron.=notice;cron.=warning  @127.0.0.1:25224
local0.=alert;local0.=crit;local0.=debug;local0.=emerg;local0.=err;local0.=info;local0.=notice;local0.=warning  @127.0.0.1:25224
local4.=alert;local4.=crit;local4.=debug;local4.=emerg;local4.=err;local4.=info;local4.=notice;local4.=warning  @127.0.0.1:25224
syslog.=alert;syslog.=crit;syslog.=debug;syslog.=emerg;syslog.=err;syslog.=info;syslog.=notice;syslog.=warning  @127.0.0.1:25224
```


**CEF를 전달할 때 rsyslog 서버의 경우** 다음 명령을 실행하여 Syslog 전달에 대한 현재 설정을 표시하고 구성 파일의 변경 내용을 검토합니다.

```bash
cat /etc/rsyslog.d/security-config-omsagent.conf
```

이 경우 rsyslog에 대해서는 다음과 유사한 출력이 표시됩니다.

```bash
if $rawmsg contains "CEF:" or $rawmsg contains "ASA-" then @@127.0.0.1:25226
```

## <a name="troubleshoot-operating-system-issues"></a>운영 체제 문제 해결

이 프로시저에서는 운영 체제 구성에서 파생된 것이 분명한 문제를 해결하는 방법을 설명합니다.

**운영 체제 문제 해결 방법**:

1. 지원되는 운영 체제 및 Python 버전으로 작업하고 있는지 확인합니다. 자세한 내용은 [CEF 필수 조건](connect-common-event-format.md#prerequisites) 및 [Linux 머신 또는 어플라이언스 구성](connect-syslog.md#configure-your-linux-machine-or-appliance)을 참조하세요.

1. 가상 머신이 Azure에 있는 경우 NSG(네트워크 보안 그룹)가 포트 514에서 로그 클라이언트(보낸 사람)의 인바운드 TCP/UDP 연결을 허용하는지 확인합니다.

1. 패킷이 Syslog Collector로 도착하고 있는지 확인합니다. Syslog Collector에 도착하는 syslog 패킷을 캡처하려면 다음을 실행합니다.

    ```config
    tcpdump -Ani any port 514 and host <ip_address_of_sender> -vv
    ```

1. 다음 중 하나를 수행합니다.

    - 패킷이 도착하지 않으면 NSG 보안 그룹 권한 및 Syslog Collector로 향하는 라우팅 경로를 확인합니다.

    - 패킷이 도착하는 경우 패킷이 거부되지 않는지 확인합니다.

    거부된 패킷이 표시되면 IP 테이블이 연결을 차단하지 않는지 확인합니다.

    패킷이 거부되지 않는지 확인하려면 다음을 실행합니다.

    ```config
    watch -n 2 -d iptables -nvL
    ```

1. Syslog 서버가 로그를 처리하고 있는지 확인합니다. 다음을 실행합니다.

    ```config
    tail -f /var/log/messages or tail -f /var/log/syslog
    ```

    처리 중인 Syslog 로그 또는 CEF 로그는 모두 일반 텍스트로 표시됩니다.

1. rsyslog 서버가 TCP/UDP 포트 514에서 수신 대기 중인지 확인합니다. 다음을 실행합니다.

    ```config
    netstat -anp | grep syslog
    ```

    Syslog Collector로 전송되는 CEF 로그 또는 ASA 로그가 있는 경우 TCP 포트 25226에 설정된 연결이 표시됩니다.

    예를 들면 다음과 같습니다.

    ```config
    0 127.0.0.1:36120 127.0.0.1:25226 ESTABLISHED 1055/rsyslogd
    ```

    연결이 차단되면 [OMS 에이전트에 대한 SELinux 연결이 차단](#selinux-blocking-connection-to-the-oms-agent)되거나 [ 방화벽 프로세스가 차단](#blocked-firewall-policy)될 수 있습니다. 다음 지침을 참조하여 문제를 확인합니다.

### <a name="selinux-blocking-connection-to-the-oms-agent"></a>OMS 에이전트에 대한 연결을 차단하는 SELinux

이 프로시저에서는 SELinux가 현재 `permissive` 상태인지 또는 OMS 에이전트에 대한 연결을 차단하는지 확인하는 방법을 설명합니다. 이 프로시저는 운영 체제가 RedHat 또는 CentOS 배포인 경우에 해당합니다.

> [!NOTE]
> CEF 및 Syslog에 대 한 Microsoft 센티널 지원에는 FIPS 강화만 포함 됩니다. SELinux 또는 CIS와 같은 다른 강화법은 현재 지원되지 않습니다.
>

1. 다음을 실행합니다.

    ```config
    sestatus
    ```

    상태는 다음 중 하나로 표시됩니다.

    - `disabled`. 이 구성은 Microsoft 센티널에 연결할 때 지원 됩니다.
    - `permissive`. 이 구성은 Microsoft 센티널에 연결할 때 지원 됩니다.
    - `enforced`. 이 구성은 지원되지 않으며 상태를 사용하지 않도록 설정하거나 `permissive`로 설정해야 합니다.

1. 현재 상태가 `enforced`로 설정되어 있으면 일시 해제하여 차단기인지 확인합니다. 다음을 실행합니다.

    ```config
    setenforce 0
    ```

    > [!NOTE]
    > 이 단계에서는 서버가 다시 부팅될 때까지만 SELinux를 해제합니다. SELinux 구성을 해제된 상태로 유지하도록 수정합니다.
    >

1. 변경되었는지 확인하려면 다음을 실행합니다.

    ```
    getenforce
    ```

    `permissive` 상태는 반환되어야 합니다.

> [!IMPORTANT]
> 시스템이 다시 부팅되면 이 설정 업데이트가 손실됩니다. 이 설정을 `permissive`로 영구적으로 업데이트하려면 **/etc/selinux/config** 파일을 수정하고 `SELINUX` 값을 `SELINUX=permissive`로 변경하세요.
>
> 자세한 내용은 [RedHat 설명서](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/8/html/using_selinux/changing-selinux-states-and-modes_using-selinux)를 참조하세요.


### <a name="blocked-firewall-policy"></a>차단된 방화벽 정책

이 프로시저에서는 방화벽 정책이 Rsyslog 디먼에서 OMS 에이전트로의 연결을 차단하는지를 확인하는 방법과 필요에 따라 사용하지 않도록 설정하는 방법을 설명합니다.


1. 다음 명령을 실행하여 방화벽 정책에 의해 삭제되는 트래픽을 나타내는 IP 테이블에 거부가 있는지 확인합니다.

    ```config
    watch -n 2 -d iptables -nvL
    ```

1. 방화벽 정책을 사용하도록 설정하려면 연결을 허용하는 정책 규칙을 만듭니다. 필요에 따라 활성 방화벽을 통해 TCP/UDP 포트 25226 및 25224를 허용하는 규칙을 추가합니다.

    예를 들면 다음과 같습니다.

    ```config
    Every 2.0s: iptables -nvL                      rsyslog: Wed Jul  7 15:56:13 2021

    Chain INPUT (policy ACCEPT 6185K packets, 2466M bytes)
     pkts bytes target     prot opt in     out     source               destination


    Chain FORWARD (policy ACCEPT 0 packets, 0 bytes)
     pkts bytes target     prot opt in     out     source               destination


    Chain OUTPUT (policy ACCEPT 6792K packets, 6348M bytes)
     pkts bytes target     prot opt in     out     source               destination
    ```

1. 활성 방화벽을 통해 TCP/UDP 포트 25226 및 25224를 허용하는 규칙을 만들려면 필요에 따라 규칙을 추가합니다.

    1. 방화벽 정책 편집기를 설치하려면 다음을 실행합니다.

        ```config
        yum install policycoreutils-python
        ```

    1. 방화벽 정책에 방화벽 규칙을 추가합니다. 예를 들면 다음과 같습니다.

        ```config
        sudo firewall-cmd --direct --add-rule ipv4 filter INPUT 0 -p tcp --dport 25226  -j ACCEPT
        sudo firewall-cmd --direct --add-rule ipv4 filter INPUT 0 -p udp --dport 25224  -j ACCEPT
        sudo firewall-cmd --direct --add-rule ipv4 filter INPUT 0 -p tcp --dport 25224  -j ACCEPT
        ```

    1. 예외가 추가된 것을 확인합니다. 다음을 실행합니다.

        ```config
        sudo firewall-cmd --direct --get-rules ipv4 filter INPUT
        ```

    1. 방화벽 다시 로드. 다음을 실행합니다.

        ```config
        sudo firewall-cmd --reload
        ```

> [!NOTE]
> 방화벽을 해제하려면 `sudo systemctl disable firewalld`를 실행합니다.
>

## <a name="linux-and-oms-agent-related-issues"></a>Linux 및 OMS 에이전트 관련 문제

이 문서의 앞부분에서 설명한 단계를 수행 해도 문제가 해결 되지 않으면 OMS 에이전트와 Microsoft 센티널 작업 영역 간에 연결 문제가 있을 수 있습니다.

이러한 경우 다음을 확인하여 계속해서 문제를 해결합니다.

- Syslog 수집기에서 TCP/UDP 포트 514에 도착하는 패킷을 볼 수 있는지 확인합니다.

- 로컬 로그 파일에 기록되고 있는 로그( **/var/log/messages**  또는 **/var/log/syslog** )를 볼 수 있는지 확인합니다.

- 포트 25224, 25226 또는 둘 다에서 흐르는 데이터 패킷을 볼 수 있는지 확인 합니다.

- 가상 머신이 TCP를 통해 포트 443에 대한 아웃바운드 연결이 있거나 [Log Analytics 엔드포인트에](../azure-monitor/agents/log-analytics-agent.md#network-requirements) 연결할 수 있는지 확인합니다.

- 방화벽 정책을 통해 Syslog 수집기에서 필요한 URL에 액세스할 수 있는지 확인합니다. 자세한 내용은 [Log Analytics 에이전트 방화벽 요구 사항](../azure-monitor/agents/log-analytics-agent.md#firewall-requirements)을 참조하세요.

- Azure Virtual Machine이 작업 영역의 가상 머신 목록에 연결되었다고 표시되어 있는지 확인합니다.

다음 명령을 실행하여 에이전트가 Azure와 성공적으로 통신하는지 또는 OMS 에이전트가 Log Analytics 작업 영역에 연결하지 못하도록 차단되었는지 확인합니다.

```config
Heartbeat
 | where Computer contains "<computername>"
 | sort by TimeGenerated desc
```

에이전트가 성공적으로 통신하는 경우 로그 항목이 반환됩니다. 반환되지 않으면 OMS 에이전트가 차단될 수 있습니다.

## <a name="next-steps"></a>다음 단계

이 문서의 문제 해결 단계가 문제를 해결 하지 못한 경우 지원 티켓을 열거나 Microsoft 센티널 커뮤니티 리소스를 사용 합니다. 자세한 내용은 [Microsoft 센티널 사용을 위한 유용한 리소스](resources.md)를 참조 하세요.

Microsoft 센티널에 대해 자세히 알아보려면 다음 문서를 참조 하세요.

- [CEF 및 CommonSecurityLog 필드 매핑](cef-name-mapping.md)에 대해 알아봅니다.
- [데이터에 대한 가시성을 얻고 재적 위협을 확인](get-visibility.md)하는 방법을 알아봅니다.
- [Microsoft 센티널을 사용 하 여 위협 검색을](./detect-threats-built-in.md)시작 하세요.
- [통합 문서를 사용](monitor-your-data.md)하여 데이터를 모니터링합니다.
