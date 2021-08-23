---
title: Azure Sentinel에 대한 연결 유효성 검사 | Microsoft Docs
description: 보안 솔루션 연결의 유효성을 검사하여 CEF 메시지가 Azure Sentinel로 전달되는지 확인합니다.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/05/2021
ms.author: yelevin
ms.openlocfilehash: 98d97bd7c8ffab685475f50130d68668fe1c9f8b
ms.sourcegitcommit: 05dd6452632e00645ec0716a5943c7ac6c9bec7c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/17/2021
ms.locfileid: "122530439"
---
# <a name="step-3-validate-connectivity"></a>3단계: 연결 유효성 검사

로그 전달자를 배포하고(1단계) CEF 메시지를 보내도록 보안 솔루션을 구성했으면(2단계), 지침에 따라 보안 솔루션과 Azure Sentinel 간의 연결을 확인합니다.

## <a name="prerequisites"></a>필수 구성 요소

- 로그 전달자 머신에 상승된 권한(sudo)이 있어야 합니다.

- 로그 전달자 머신에 **python 2.7** 또는 **3** 이 설치되어 있어야 합니다.<br>
`python –version` 명령을 사용하여 확인합니다.

- 이 프로세스 중에 작업 영역 ID와 작업 영역 기본 키가 필요할 수 있습니다. 이는 작업 영역 리소스의 **에이전트 관리** 에서 찾을 수 있습니다.

## <a name="how-to-validate-connectivity"></a>연결 유효성 검사 방법

1. Azure Sentinel 탐색 메뉴에서 **로그** 를 엽니다. **CommonSecurityLog** 스키마로 쿼리를 실행하여 보안 솔루션에서 로그를 받는지 확인합니다.<br>
**Log Analytics** 에 로그가 나타나기 시작하는 데 약 20분이 걸릴 수 있습니다.

1. 쿼리에서 결과가 나타나지 않으면 보안 솔루션에서 이벤트가 생성되고 있는지 확인하거나, 이벤트를 생성해 보고 이벤트가 지정한 Syslog 전달자 머신으로 전달되는지 확인합니다.

1. 로그 전달자(자리 표시자 대신에 작업 영역 ID를 적용)에서 다음 스크립트를 실행하여 보안 솔루션, 로그 전달자, Azure Sentinel 간의 연결을 확인합니다. 이 스크립트는 디먼이 올바른 포트에서 수신 대기하고 있는지, 전달이 제대로 구성되었는지, 디먼과 Log Analytics 에이전트 간의 통신을 차단하는 것이 없는지 확인합니다. 또한 ‘TestCommonEventFormat’ 모의 메시지를 전송하여 엔드투엔드 연결을 확인합니다. <br>

    ```bash
    sudo wget -O cef_troubleshoot.py https://raw.githubusercontent.com/Azure/Azure-Sentinel/master/DataConnectors/CEF/cef_troubleshoot.py&&sudo python cef_troubleshoot.py [WorkspaceID]
    ```

   - **‘컴퓨터’ 필드의 매핑** 문제를 해결하기 위해 명령을 실행하라고 지시하는 메시지가 표시될 수 있습니다. 자세한 내용은 [유효성 검사 스크립트 설명](#mapping-command)을 참조하세요.

    - **Cisco ASA 방화벽 로그 구문 분석** 문제를 해결하기 위해 명령을 실행하라고 지시하는 메시지가 표시될 수 있습니다. 자세한 내용은 [유효성 검사 스크립트 설명](#parsing-command)을 참조하세요.

## <a name="validation-script-explained"></a>유효성 검사 스크립트 설명

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

1. MOCK 데이터를 localhost의 포트 514로 보냅니다. 해당 데이터는 Azure Sentinel 작업 영역에서 다음 쿼리를 실행하여 관찰할 수 있어야 합니다.

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

1. MOCK 데이터를 localhost의 포트 514로 보냅니다. 해당 데이터는 Azure Sentinel 작업 영역에서 다음 쿼리를 실행하여 관찰할 수 있어야 합니다.

    ```kusto
    CommonSecurityLog
    | where DeviceProduct == "MOCK"
    ```
---


## <a name="next-steps"></a>다음 단계

이 문서에서는 CEF 어플라이언스를 Azure Sentinel에 연결하는 방법을 알아보았습니다. Azure Sentinel에 대한 자세한 내용은 다음 문서를 참조하세요.

- [CEF 및 CommonSecurityLog 필드 매핑](cef-name-mapping.md)에 대해 알아봅니다.
- [데이터에 대한 가시성을 얻고 재적 위협을 확인](get-visibility.md)하는 방법을 알아봅니다.
- [Azure Sentinel을 사용하여 위협 검색](./detect-threats-built-in.md)을 시작합니다.
- [통합 문서를 사용](monitor-your-data.md)하여 데이터를 모니터링합니다.