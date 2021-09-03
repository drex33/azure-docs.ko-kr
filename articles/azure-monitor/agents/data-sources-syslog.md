---
title: Azure Monitor에서 Log Analytics 에이전트를 사용하여 Syslog 로그 데이터 원본 수집
description: Syslog는 Linux에 공통되는 이벤트 로깅 프로토콜입니다. 이 문서에서는 Log Analytics의 Syslog 메시지 수집을 구성하는 방법을 설명하고, 생성되는 레코드에 대한 자세한 정보를 제공합니다.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 02/26/2021
ms.openlocfilehash: 49a337d106bab7f33c8f51149c2151c21d78f40b
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/14/2021
ms.locfileid: "122567595"
---
# <a name="collect-syslog-data-sources-with-log-analytics-agent"></a>로그 분석 에이전트로 Syslog 데이터 원본 수집
Syslog는 Linux에 공통되는 이벤트 로깅 프로토콜입니다. 애플리케이션은 로컬 컴퓨터에 저장되거나 Syslog 수집기에 배달될 수 있는 메시지를 전송합니다. Linux용 Log Analytics 에이전트를 설치하면 에이전트에 메시지를 전달하도록 로컬 Syslog 디먼이 구성됩니다. 그러면 에이전트는 레코드가 만들어진 Azure Monitor로 해당 메시지를 보냅니다.  

> [!IMPORTANT]
> 이 문서에서는 Azure Monitor에서 사용하는 에이전트 중 하나인 [Log Analytics 에이전트](./log-analytics-agent.md)를 사용하여 Syslog 이벤트를 수집하는 방법을 설명합니다. 다른 에이전트는 다른 데이터를 수집하고 다르게 구성됩니다. 사용 가능한 에이전트 및 수집할 수 있는 데이터 목록은 [Azure Monitor 에이전트 개요](../agents/agents-overview.md)를 참조하세요.

> [!NOTE]
> Azure Monitor는 rsyslog 또는 syslog-ng에서 보낸 메시지의 컬렉션을 지원합니다. 여기서 rsyslog는 기본 디먼입니다. Red Hat Enterprise Linux 버전 5, CentOS 및 Oracle Linux 버전(sysklog)에서는 syslog 이벤트 수집을 위한 기본 syslog 디먼이 지원되지 않습니다. 이 배포의 해당 버전에서 syslog 데이터를 수집하려면 [rsyslog 디먼](http://rsyslog.com)을 설치하고 sysklog를 대체하도록 구성해야 합니다.
>
>

![Syslog 수집](media/data-sources-syslog/overview.png)

Syslog 수집기에서 지원되는 기능은 다음과 같습니다.

* kern
* 사용자
* mail
* daemon
* auth
* syslog
* lpr
* news
* uucp
* cron
* authpriv
* ftp
* local0-local7

다른 시설의 경우 Azure Monitor에서 [사용자 지정 로그 데이터 원본을 구성](data-sources-custom-logs.md)합니다.
 
## <a name="configuring-syslog"></a>Syslog 구성
Linux용 Log Analytics 에이전트는 해당 구성에 지정된 기능 및 심각도에 따라서만 이벤트를 수집합니다. Azure Portal을 통해 또는 Linux 에이전트의 구성 파일을 관리하여 Syslog를 구성할 수 있습니다.

### <a name="configure-syslog-in-the-azure-portal"></a>Azure Portal에서 Syslog 구성
Log Analytics 작업 영역의 [에이전트 구성 메뉴](../agents/agent-data-sources.md#configuring-data-sources)에서 Syslog를 구성합니다. 이 구성은 각 Linux 에이전트의 구성 파일에 전달됩니다.

**시설 추가** 를 클릭하여 새 시설을 추가할 수 있습니다. 각 기능에 대해, 선택한 심각도의 메시지만 수집됩니다.  수집하려는 특정 기능의 심각도를 확인합니다. 이벤트를 필터링하는 추가 조건을 제공할 수는 없습니다.

[![Syslog 구성](media/data-sources-syslog/configure.png)](media/data-sources-syslog/configure.png#lightbox)

기본적으로, 모든 구성 변경은 모든 에이전트로 자동 푸시됩니다. 각 Linux 에이전트에서 Syslog를 수동으로 구성하려면 *내 머신에 아래 구성 적용* 확인란을 선택 취소합니다.

### <a name="configure-syslog-on-linux-agent"></a>Linux 에이전트에서 Syslog 구성
[Log Analytics 에이전트가 Linux 클라이언트에 설치](../vm/monitor-virtual-machine.md)되어 있으면 OMS 에이전트는 수집되는 메시지의 기능 및 심각도를 정의하는 기본 syslog 구성 파일을 설치합니다. 이 파일을 수정하여 구성을 변경할 수 있습니다. 구성 파일은 클라이언트가 설치한 Syslog 디먼에 따라 다릅니다.

> [!NOTE]
> Syslog 구성을 편집하는 경우, 변경 내용을 적용하려면 syslog 디먼을 다시 시작해야 합니다.
>
>

#### <a name="rsyslog"></a>rsyslog
rsyslog에 대한 구성 파일은 **/etc/rsyslog.d/95-omsagent.conf** 에 있습니다. 기본 내용은 아래와 같습니다. 이 파일은 경고 이상 수준의 모든 기능에 대해 로컬 에이전트에서 전송된 syslog 메시지를 수집합니다.

```config
kern.warning       @127.0.0.1:25224
user.warning       @127.0.0.1:25224
daemon.warning     @127.0.0.1:25224
auth.warning       @127.0.0.1:25224
syslog.warning     @127.0.0.1:25224
uucp.warning       @127.0.0.1:25224
authpriv.warning   @127.0.0.1:25224
ftp.warning        @127.0.0.1:25224
cron.warning       @127.0.0.1:25224
local0.warning     @127.0.0.1:25224
local1.warning     @127.0.0.1:25224
local2.warning     @127.0.0.1:25224
local3.warning     @127.0.0.1:25224
local4.warning     @127.0.0.1:25224
local5.warning     @127.0.0.1:25224
local6.warning     @127.0.0.1:25224
local7.warning     @127.0.0.1:25224
```

구성 파일의 해당 섹션을 제거하여 기능을 제거할 수 있습니다. 해당 기능 항목을 수정하여 특정 기능에 대해 수집되는 심각도를 제한할 수 있습니다. 예를 들어 오류 또는 더 높은 심각도의 메시지로 사용자 기능을 제한하려면 다음 구성 파일 줄을 다음과 같이 수정합니다.

```config
user.error    @127.0.0.1:25224
```

#### <a name="syslog-ng"></a>syslog-ng
syslog-ng의 구성 파일은 **/etc/syslog-ng/syslog-ng.conf** 에 있습니다.  기본 내용은 아래와 같습니다. 이 파일은 모든 기능 및 모든 심각도에 대해 로컬 에이전트에서 전송된 syslog 메시지를 수집합니다.   

```config
#
# Warnings (except iptables) in one file:
#
destination warn { file("/var/log/warn" fsync(yes)); };
log { source(src); filter(f_warn); destination(warn); };

#OMS_Destination
destination d_oms { udp("127.0.0.1" port(25224)); };

#OMS_facility = auth
filter f_auth_oms { level(alert,crit,debug,emerg,err,info,notice,warning) and facility(auth); };
log { source(src); filter(f_auth_oms); destination(d_oms); };

#OMS_facility = authpriv
filter f_authpriv_oms { level(alert,crit,debug,emerg,err,info,notice,warning) and facility(authpriv); };
log { source(src); filter(f_authpriv_oms); destination(d_oms); };

#OMS_facility = cron
filter f_cron_oms { level(alert,crit,debug,emerg,err,info,notice,warning) and facility(cron); };
log { source(src); filter(f_cron_oms); destination(d_oms); };

#OMS_facility = daemon
filter f_daemon_oms { level(alert,crit,debug,emerg,err,info,notice,warning) and facility(daemon); };
log { source(src); filter(f_daemon_oms); destination(d_oms); };

#OMS_facility = kern
filter f_kern_oms { level(alert,crit,debug,emerg,err,info,notice,warning) and facility(kern); };
log { source(src); filter(f_kern_oms); destination(d_oms); };

#OMS_facility = local0
filter f_local0_oms { level(alert,crit,debug,emerg,err,info,notice,warning) and facility(local0); };
log { source(src); filter(f_local0_oms); destination(d_oms); };

#OMS_facility = local1
filter f_local1_oms { level(alert,crit,debug,emerg,err,info,notice,warning) and facility(local1); };
log { source(src); filter(f_local1_oms); destination(d_oms); };

#OMS_facility = mail
filter f_mail_oms { level(alert,crit,debug,emerg,err,info,notice,warning) and facility(mail); };
log { source(src); filter(f_mail_oms); destination(d_oms); };

#OMS_facility = syslog
filter f_syslog_oms { level(alert,crit,debug,emerg,err,info,notice,warning) and facility(syslog); };
log { source(src); filter(f_syslog_oms); destination(d_oms); };

#OMS_facility = user
filter f_user_oms { level(alert,crit,debug,emerg,err,info,notice,warning) and facility(user); };
log { source(src); filter(f_user_oms); destination(d_oms); };
```

구성 파일의 해당 섹션을 제거하여 기능을 제거할 수 있습니다. 특정 기능을 목록에서 제거하여 해당 기능에 대해 수집되는 심각도를 제한할 수 있습니다.  예를 들어 경고 또는 위험 메시지만으로 사용자 기능을 제한하려면 구성 파일의 해당 섹션을 다음과 같이 수정합니다.

```config
#OMS_facility = user
filter f_user_oms { level(alert,crit) and facility(user); };
log { source(src); filter(f_user_oms); destination(d_oms); };
```

### <a name="collecting-data-from-additional-syslog-ports"></a>추가 Syslog 포트에서 데이터 수집
Log Analytics 에이전트는 포트 25224에서 로컬 클라이언트의 Syslog 메시지를 수신합니다.  에이전트가 설치될 때 기본 syslog 구성이 적용되며 다음 위치에서 찾을 수 있습니다.

* Rsyslog: `/etc/rsyslog.d/95-omsagent.conf`
* Syslog-ng: `/etc/syslog-ng/syslog-ng.conf`

두 개의 구성 파일을 만들어 포트 번호를 변경할 수 있습니다. 설치한 Syslog 디먼에 따라 rsyslog 또는 syslog-ng 파일과 FluentD 구성 파일입니다.  

* FluentD 구성 파일은 `/etc/opt/microsoft/omsagent/conf/omsagent.d`에 있는 새 파일이어야 합니다. **포트** 항목의 값을 사용자 지정 포트 번호로 바꿉니다.

    ```xml
    <source>
        type syslog
        port %SYSLOG_PORT%
        bind 127.0.0.1
        protocol_type udp
        tag oms.syslog
    </source>
    <filter oms.syslog.**>
        type filter_syslog
    ```

* rsyslog의 경우 `/etc/rsyslog.d/`에 있는 새 구성 파일을 만들어야 합니다. %SYSLOG_PORT% 값을 사용자 지정 포트 번호로 바꿉니다.  

    > [!NOTE]
    > 구성 파일 `95-omsagent.conf`에서 이 값을 수정하는 경우 에이전트가 기본 구성을 적용할 때 덮어쓰게 됩니다.
    >

    ```config
    # OMS Syslog collection for workspace %WORKSPACE_ID%
    kern.warning              @127.0.0.1:%SYSLOG_PORT%
    user.warning              @127.0.0.1:%SYSLOG_PORT%
    daemon.warning            @127.0.0.1:%SYSLOG_PORT%
    auth.warning              @127.0.0.1:%SYSLOG_PORT%
    ```

* syslog-ng 구성 파일은 아래 표시된 예제 구성을 복사하고 사용자 지정 수정된 설정을 `/etc/syslog-ng/`에 있는 syslog-ng.conf 구성 파일의 끝에 추가하여 수정해야 합니다. 기본 레이블 **% WORKSPACE_ID % _oms** 또는 **%WORKSPACE_ID_OMS** 를 사용하지 **않습니다**. 변경 내용을 구분하기 위해 사용자 지정 레이블을 정의합니다.  

    > [!NOTE]
    > 구성 파일에서 기본 값을 수정하는 경우 에이전트가 기본 구성을 적용할 때 덮어쓰게 됩니다.
    >

    ```config
    filter f_custom_filter { level(warning) and facility(auth; };
    destination d_custom_dest { udp("127.0.0.1" port(%SYSLOG_PORT%)); };
    log { source(s_src); filter(f_custom_filter); destination(d_custom_dest); };
    ```

변경을 완료한 후, Syslog와 Log Analytics 에이전트 서비스를 다시 시작하여 구성 변경 내용이 적용되도록 해야 합니다.   

## <a name="syslog-record-properties"></a>Syslog 레코드 속성
Syslog 레코드는 **Syslog** 형식이며, 다음 표의 속성이 있습니다.

| 속성 | Description |
|:--- |:--- |
| Computer |이벤트가 수집된 컴퓨터입니다. |
| Facility |메시지를 생성한 시스템의 부분을 정의합니다. |
| HostIP |메시지를 보내는 시스템의 IP 주소입니다. |
| HostName |메시지를 보내는 시스템의 이름입니다. |
| SeverityLevel |이벤트의 심각도 수준입니다. |
| SyslogMessage |메시지의 텍스트입니다. |
| ProcessID |메시지를 생성한 프로세스의 ID입니다. |
| EventTime |이벤트가 생성된 날짜 및 시간입니다. |

## <a name="log-queries-with-syslog-records"></a>Syslog 레코드를 포함하는 로그 쿼리
다음 표에는 Syslog 레코드를 검색하는 로그 쿼리의 여러 예제가 나와 있습니다.

| 쿼리 | Description |
|:--- |:--- |
| syslog |모든 Syslog입니다. |
| Syslog &#124; where SeverityLevel == "error" |심각도가 오류인 모든 Syslog 레코드입니다. |
| Syslog &#124; summarize AggregatedValue = count() by Computer |컴퓨터별 Syslog 레코드 수입니다. |
| Syslog &#124; summarize AggregatedValue = count() by Facility |기능별 Syslog 레코드 수입니다. |

## <a name="next-steps"></a>다음 단계
* 데이터 원본 및 솔루션에서 수집한 데이터를 분석하는 [로그 쿼리](../logs/log-query-overview.md)에 대해 알아봅니다.
* [사용자 지정 필드](../logs/custom-fields.md)를 사용하여 syslog 레코드의 데이터를 개별 필드로 구문 분석합니다.
* [Linux 에이전트를 구성](../vm/monitor-virtual-machine.md)합니다.