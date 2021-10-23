---
title: Azure Log Analytics Linux 에이전트 문제 해결 | Microsoft Docs
description: Azure Monitor의 Linux용 Log Analytics 에이전트에서 발생하는 가장 일반적인 문제에 대한 증상, 원인 및 해결 방법에 대해 설명합니다.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/21/2021
ms.openlocfilehash: fce62f3bbe5a3eca29f89cb47c98df6485d1d123
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130216564"
---
# <a name="how-to-troubleshoot-issues-with-the-log-analytics-agent-for-linux"></a>Linux용 Log Analytics 에이전트의 문제를 해결하는 방법

이 문서에서는 Azure Monitor의 Linux용 Log Analytics 에이전트에서 발생할 수 있는 오류를 해결하기 위한 유용하고, 사용 가능한 솔루션을 제안합니다.

## <a name="log-analytics-troubleshooting-tool"></a>Log Analytics 문제 해결 도구

Log Analytics 에이전트 Linux 문제 해결 도구는 Log Analytics 에이전트의 문제를 찾아 진단하는 데 유용하게 설계된 스크립트입니다. 설치 시 에이전트에 자동으로 포함됩니다. 이 도구의 실행은 문제를 진단하는 첫 번째 단계여야 합니다.

### <a name="how-to-use"></a>사용 방법

Log Analytics 에이전트가 있는 컴퓨터의 터미널 창에 다음 명령을 붙여넣어 문제 해결 도구를 실행할 수 있습니다. `sudo /opt/microsoft/omsagent/bin/troubleshooter`

### <a name="manual-installation"></a>수동 설치

문제 해결 도구는 Log Analytics 에이전트 설치 시 자동으로 포함됩니다. 그러나 설치에 실패하는 경우 다음 단계를 수행하여 수동으로 설치할 수도 있습니다.

1. 문제 해결사가 [GDB(GNU Project Debugger)를](https://www.gnu.org/software/gdb/) 사용하는 컴퓨터에 설치되어 있는지 확인합니다.
2. 문제 해결사 번들을 컴퓨터에 복사합니다. `wget https://raw.github.com/microsoft/OMS-Agent-for-Linux/master/source/code/troubleshooter/omsagent_tst.tar.gz`
3. 번들의 압축을 풉니다. `tar -xzvf omsagent_tst.tar.gz`
4. 수동 설치를 실행합니다. `sudo ./install_tst`

### <a name="scenarios-covered"></a>적용되는 시나리오

다음은 문제 해결 도구에서 확인된 시나리오의 목록입니다.

1. 에이전트가 비정상적인 상태입니다. 하트비트가 제대로 작동하지 않습니다
2. 에이전트가 실행되지 않아 Log Analytic 서비스에 연결할 수 없습니다
3. 에이전트 syslog가 작동하지 않습니다
4. 에이전트의 CPU/메모리 사용량이 높습니다
5. 에이전트에 설치 문제가 발생했습니다
6. 에이전트 사용자 지정 로그가 작동하지 않습니다
7. 에이전트 로그 수집

세부 정보는 [GitHub 설명서](https://github.com/microsoft/OMS-Agent-for-Linux/blob/master/docs/Troubleshooting-Tool.md)를 확인하세요.

 > [!NOTE]
 > 문제가 발생하는 경우 로그 수집기 도구를 실행하세요. 즉시 로그를 만들면 지원 팀에서 문제를 더 빨리 해결할 수 있습니다.

## <a name="purge-and-re-install-the-linux-agent"></a>Linux 에이전트 제거 및 재설치

에이전트를 완전히 다시 설치하면 대부분의 문제가 해결됩니다. 실제로 이는 지원 팀에서 에이전트를 무정전 상태로 전환하기 위한 지원의 첫 번째 제안일 수 있습니다. 문제 해결사를 실행하여 로그를 수집하고 새로 설치하면 문제를 보다 신속하게 해결할 수 있습니다.

1. 제거 스크립트를 다운로드합니다.
- `$ wget https://raw.githubusercontent.com/microsoft/OMS-Agent-for-Linux/master/tools/purge_omsagent.sh`
2. 제거 스크립트(sudo 사용 권한 포함)를 실행합니다.
- `$ sudo sh purge_omsagent.sh`

## <a name="important-log-locations-and-log-collector-tool"></a>중요한 로그 위치 및 로그 수집기 도구

 파일 | 경로
 ---- | -----
 Linux용 Log Analytics 에이전트 로그 파일 | `/var/opt/microsoft/omsagent/<workspace id>/log/omsagent.log`
 Log Analytics 에이전트 구성 로그 파일 | `/var/opt/microsoft/omsconfig/omsconfig.log`

 문제 해결을 위해 또는 GitHub 문제를 제출하기 전에 로그 수집기 도구를 사용하여 중요한 로그를 검색하는 것이 좋습니다. 도구 및 도구 실행 방법에 대한 자세한 내용은 [여기](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/tools/LogCollector/OMS_Linux_Agent_Log_Collector.md)서 확인할 수 있습니다.

## <a name="important-configuration-files"></a>중요 구성 파일

 범주 | 파일 위치
 ----- | -----
 syslog | `/etc/syslog-ng/syslog-ng.conf` 또는 `/etc/rsyslog.conf` 또는 `/etc/rsyslog.d/95-omsagent.conf`
 성능, Nagios, Zabbix, Log Analytics 출력 및 일반 에이전트 | `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.conf`
 추가 구성 | `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.d/*.conf`

 > [!NOTE]
 > 사용하는 작업 영역에 대한 Azure Portal의 [데이터 메뉴 Log Analytics 고급 설정](../agents/agent-data-sources.md#configuring-data-sources)에서 컬렉션을 구성한 경우 성능 카운터 및 Syslog에 대한 편집 구성 파일을 덮어씁니다. 모든 에이전트에 구성을 사용하지 않도록 설정하려면 Log Analytics **고급 설정** 에서 컬렉션을 사용하지 않도록 설정합니다. 또는 단일 에이전트인 경우 다음 명령을 실행합니다. `sudo /opt/microsoft/omsconfig/Scripts/OMS_MetaConfigHelper.py --disable && sudo rm /etc/opt/omi/conf/omsconfig/configuration/Current.mof* /etc/opt/omi/conf/omsconfig/configuration/Pending.mof*`

## <a name="installation-error-codes"></a>설치 오류 코드

| 오류 코드 | 의미 |
| --- | --- |
| NOT_DEFINED | 필요한 종속성이 설치되지 않아 auoms auditd 플러그인이 설치되지 않습니다. auoms 설치가 실패했습니다. 패키지 auditd를 설치하세요. |
| 2 | 셸 번들에 잘못된 옵션이 제공되었습니다. `sudo sh ./omsagent-*.universal*.sh --help` 명령을 실행하여 사용 방법을 확인하세요. |
| 3 | 셸 번들에 옵션이 제공되지 않았습니다. `sudo sh ./omsagent-*.universal*.sh --help` 명령을 실행하여 사용 방법을 확인하세요. |
| 4 | 패키지 형식 또는 프록시 설정이 잘못되었습니다. omsagent-*rpm*.sh 패키지는 RPM 기반 시스템에만 설치할 수 있고, omsagent-*deb*.sh 패키지는 Debian 기반 시스템에만 설치할 수 있습니다. [최신 릴리스](../vm/monitor-virtual-machine.md#agents)의 유니버설 설치 관리자를 사용하는 것이 좋습니다. 또한 프록시 설정을 확인하려면 검토하세요. |
| 5 | 셸 번들을 루트로 실행하지 않았거나 온보딩 중에 403 오류가 반환되었습니다. `sudo`를 사용하여 명령을 실행하세요. |
| 6 | 패키지 아키텍처가 잘못되었거나 온보딩 중에 200 오류가 반환되었습니다. omsagent-\*x64.sh 패키지는 64비트 시스템에만 설치할 수 있고, omsagent-\*x86.sh 패키지는 32비트 시스템에만 설치할 수 있습니다. [최신 릴리스](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/latest)에서 아키텍처에 적합한 패키지를 다운로드하세요. |
| 17 | OMS 패키지 설치가 실패했습니다. 명령 출력을 살펴보고 근본 원인을 파악하세요. |
| 18 | OMSConfig 패키지 설치가 실패했습니다. 명령 출력을 살펴보고 근본 원인을 파악하세요. |
| 19 | OMI 패키지 설치가 실패했습니다. 명령 출력을 살펴보고 근본 원인을 파악하세요. |
| 20 | SCX 패키지 설치가 실패했습니다. 명령 출력을 살펴보고 근본 원인을 파악하세요. |
| 21 | 공급자 키트 설치가 실패했습니다. 명령 출력을 살펴보고 근본 원인을 파악하세요. |
| 22 | 번들 패키지 설치가 실패했습니다. 명령 출력을 살펴보고 근본 원인을 파악하세요. |
| 23 | SCX 또는 OMI 패키지가 이미 설치되어 있습니다. `--install` 대신 `--upgrade`를 사용하여 셸 번들을 설치하세요. |
| 30 | 내부 번들 오류가 있습니다. 출력의 세부 정보를 포함하여 [GitHub 문제](https://github.com/Microsoft/OMS-Agent-for-Linux/issues)를 제출하세요. |
| 55 | 지원되지 않는 openssl 버전이거나, Azure Monitor에 연결할 수 없거나, dpkg가 잠겨 있거나, curl 프로그램이 누락되었습니다. |
| 61 | Python ctypes 라이브러리가 없습니다. Python ctypes 라이브러리 또는 패키지(python-ctypes)를 설치합니다. |
| 62 | tar 프로그램이 없습니다. tar를 설치하세요. |
| 63 | sed 프로그램이 없습니다. sed를 설치하세요. |
| 64 | curl 프로그램이 없습니다. curl을 설치하세요. |
| 65 | gpg 프로그램이 없습니다. gpg를 설치하세요. |

## <a name="onboarding-error-codes"></a>온보딩 오류 코드

| 오류 코드 | 의미 |
| --- | --- |
| 2 | omsadmin 스크립트에 잘못된 옵션이 제공되었습니다. `sudo sh /opt/microsoft/omsagent/bin/omsadmin.sh -h` 명령을 실행하여 사용 방법을 확인하세요. |
| 3 | omsadmin 스크립트에 잘못된 구성이 제공되었습니다. `sudo sh /opt/microsoft/omsagent/bin/omsadmin.sh -h` 명령을 실행하여 사용 방법을 확인하세요. |
| 4 | omsadmin 스크립트에 잘못된 프록시가 제공되었습니다. 프록시를 확인하고 [HTTP 프록시 사용에 대한 설명서](./log-analytics-agent.md#firewall-requirements)를 읽어보세요. |
| 5 | Azure Monitor에서 403 HTTP 오류가 수신되었습니다. omsadmin 스크립트의 전체 출력에서 자세한 내용을 확인하세요. |
| 6 | Azure Monitor에서 비-200 HTTP 오류가 수신되었습니다. omsadmin 스크립트의 전체 출력에서 자세한 내용을 확인하세요. |
| 7 | Azure Monitor에 연결할 수 없습니다. omsadmin 스크립트의 전체 출력에서 자세한 내용을 확인하세요. |
| 8 | Log Analytics 작업 영역에 온보딩하는 동안 오류가 발생했습니다. omsadmin 스크립트의 전체 출력에서 자세한 내용을 확인하세요. |
| 30 | 내부 스크립트 오류가 발생했습니다. 출력의 세부 정보를 포함하여 [GitHub 문제](https://github.com/Microsoft/OMS-Agent-for-Linux/issues)를 제출하세요. |
| 31 | 에이전트 ID를 생성하는 동안 오류가 발생했습니다. 출력의 세부 정보를 포함하여 [GitHub 문제](https://github.com/Microsoft/OMS-Agent-for-Linux/issues)를 제출하세요. |
| 32 | 인증서를 생성하는 동안 오류가 발생했습니다. omsadmin 스크립트의 전체 출력에서 자세한 내용을 확인하세요. |
| 33 | omsconfig에 대한 메타 구성을 생성하는 동안 오류가 발생했습니다. 출력의 세부 정보를 포함하여 [GitHub 문제](https://github.com/Microsoft/OMS-Agent-for-Linux/issues)를 제출하세요. |
| 34 | 메타 구성 생성 스크립트가 없습니다. `sudo sh /opt/microsoft/omsagent/bin/omsadmin.sh -w <Workspace ID> -s <Workspace Key>` 명령을 사용하여 다시 온보딩하세요. |

## <a name="enable-debug-logging"></a>디버그 로깅 활성화

### <a name="oms-output-plugin-debug"></a>OMS 출력 플러그 인 디버그

 FluentD를 사용하면 플러그 인 관련 로깅 수준을 통해 입력 및 출력에 대한 다양한 로그 수준을 지정할 수 있습니다. OMS 출력에 대해 다른 로그 수준을 지정하려면 `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.conf`에서 일반 에이전트 구성을 편집합니다.

 OMS 출력 플러그 인에서, 구성 파일이 끝나기 전에 `log_level` 속성을 `info`에서 `debug`로 변경합니다.

```
<match oms.** docker.**>
  type out_oms
  log_level debug
  num_threads 5
  buffer_chunk_limit 5m
  buffer_type file
  buffer_path /var/opt/microsoft/omsagent/<workspace id>/state/out_oms*.buffer
  buffer_queue_limit 10
  flush_interval 20s
  retry_limit 10
  retry_wait 30s
</match>
```

디버그 로깅을 사용하면 Azure Monitor 서비스에 대한 배치 업로드를 형식, 데이터 항목 수, 전송 소요 시간으로 구분하여 확인할 수 있습니다.

*디버그 사용 로그 예:*

```
Success sending oms.nagios x 1 in 0.14s
Success sending oms.omi x 4 in 0.52s
Success sending oms.syslog.authpriv.info x 1 in 0.91s
```

### <a name="verbose-output"></a>자세한 정보 출력

OMS 출력 플러그 인을 사용하는 대신 데이터 항목을 `stdout`으로 직접 출력할 수도 있으며, 이 데이터 항목은 Linux용 Log Analytics 에이전트 로그 파일에서 확인할 수 있습니다.

`/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.conf`의 Log Analytics 일반 에이전트 구성 파일에서, 각 줄 앞에 `#`을 추가하여 OMS 출력 플러그 인을 주석 처리합니다.

```
#<match oms.** docker.**>
#  type out_oms
#  log_level info
#  num_threads 5
#  buffer_chunk_limit 5m
#  buffer_type file
#  buffer_path /var/opt/microsoft/omsagent/<workspace id>/state/out_oms*.buffer
#  buffer_queue_limit 10
#  flush_interval 20s
#  retry_limit 10
#  retry_wait 30s
#</match>
```

출력 플러그 인 아래에서, 각 줄 앞의 `#`을 제거하여 다음 섹션의 주석 처리를 제거합니다.

```
<match **>
  type stdout
</match>
```

## <a name="issue--unable-to-connect-through-proxy-to-azure-monitor"></a>문제: 프록시를 통해 Azure Monitor에 연결할 수 없습니다.

### <a name="probable-causes"></a>가능한 원인

* 등록하는 동안 지정된 프록시가 올바르지 않습니다.
* Azure Monitor 및 Azure Automation 서비스 엔드포인트는 데이터 센터의 승인된 목록에 포함되지 않습니다.

### <a name="resolution"></a>해결 방법

1. 다음 명령을 `-v` 옵션 사용 상태로 사용하여 Linux용 Log Analytics 에이전트로 Azure Monitor에 다시 온보딩합니다. 그러면 Log Analytics에 대한 프록시를 통해 Azure Monitor로 연결되는 에이전트의 자세한 정보를 출력할 수 있습니다. 
`/opt/microsoft/omsagent/bin/omsadmin.sh -w <Workspace ID> -s <Workspace Key> -p <Proxy Conf> -v`

2. [프록시 설정 업데이트](agent-manage.md#update-proxy-settings) 섹션을 검토하여 프록시 서버를 통해 통신하도록 에이전트를 제대로 구성했는지 확인합니다.

3. Azure Monitor [네트워크 방화벽 요구 사항](./log-analytics-agent.md#firewall-requirements) 목록에 설명된 엔드포인트가 허용 목록에 올바르게 추가되었는지 다시 확인합니다. Azure Automation을 사용하는 경우 필요한 네트워크 구성 단계도 위에서 연결됩니다.

## <a name="issue-you-receive-a-403-error-when-trying-to-onboard"></a>문제: 등록하는 동안 403 오류 발생

### <a name="probable-causes"></a>가능한 원인

* Linux 서버의 날짜 및 시간이 올바르지 않습니다
* 사용된 작업 영역 ID 및 작업 영역 키가 올바르지 않습니다.

### <a name="resolution"></a>해결 방법

1. 명령 시간을 사용하여 Linux 서버에서 시간을 확인합니다. 시간이 현재 시간에서 +/-15분인 경우 등록이 실패합니다. 이 문제를 해결하려면 Linux 서버의 날짜 및/또는 시간대를 업데이트합니다.
2. Linux용 Log Analytics 에이전트 최신 버전이 설치되어 있는지 확인합니다.  최신 버전은 시간 차이로 인해 등록 실패가 발생하는지 여부를 알려줍니다.
3. 이 문서 앞부분의 설치 지침에 따라 올바른 작업 영역 ID 및 작업 영역 키를 사용하여 다시 등록합니다.

## <a name="issue-you-see-a-500-and-404-error-in-the-log-file-right-after-onboarding"></a>문제: 등록 직후에 로그 파일에 500 및 404 오류가 표시됨

이 문제는 알려진 문제이며 Linux 데이터를 Log Analytics 작업 영역으로 처음 업로드할 때 발생합니다. 이 문제는 전송되는 데이터 또는 서비스 환경에 영향을 미치지 않습니다.

## <a name="issue-you-see-omiagent-using-100-cpu"></a>문제: CPU를 100% 사용하는 omiagent가 표시됩니다.

### <a name="probable-causes"></a>가능한 원인

nss-pem 패키지 [v1.0.3-5.el7](https://centos.pkgs.org/7/centos-x86_64/nss-pem-1.0.3-7.el7.x86_64.rpm.html)의 회귀로 인해 Redhat/Centos 7.x 배포에서 많은 성능 이슈가 발생했습니다. 이 문제에 대한 자세한 내용은 [libcurl의 버그 1667121 성능 회귀](https://bugzilla.redhat.com/show_bug.cgi?id=1667121) 문서를 참조하세요.

성능 관련 버그는 항상 발생하지는 않으며 재현하기가 매우 어렵습니다. omiagent에 이러한 문제가 발생하는 경우 특정 임계값을 초과할 때 omiagent의 스택 추적을 수집하는 스크립트 omiHighCPUDiagnostics.sh를 사용해야 합니다.

1. 스크립트 다운로드 <br/>
`wget https://raw.githubusercontent.com/microsoft/OMS-Agent-for-Linux/master/tools/LogCollector/source/omiHighCPUDiagnostics.sh`

2. CPU 임계값이 30%인 상태로 24시간 동안 진단 실행 <br/>
`bash omiHighCPUDiagnostics.sh --runtime-in-min 1440 --cpu-threshold 30`

3. 호출 스택은 omiagent_trace 파일에 덤프됩니다. Curl 및 NSS 함수 호출이 많은 경우 아래 해결 방법 단계를 수행합니다.

### <a name="resolution-step-by-step"></a>해결 방법(단계별)

1. nss-pem 패키지를 [v1.0.3-5.el7_6.1](https://centos.pkgs.org/7/centos-x86_64/nss-pem-1.0.3-7.el7.x86_64.rpm.html)로 업그레이드합니다. <br/>
`sudo yum upgrade nss-pem`

2. nss-pem을 업그레이드할 수 없는 경우(대부분 Centos에서 발생) curl을 7.29.0-46으로 다운그레이드합니다. 실수로 "yum update"를 실행하면 curl이 7.29.0-51로 업그레이드되고 문제가 다시 발생합니다. <br/>
`sudo yum downgrade curl libcurl`

3. OMI를 다시 실행합니다. <br/>
`sudo scxadmin -restart`

## <a name="issue-you-are-not-seeing-forwarded-syslog-messages"></a>문제: 전달된 Syslog 메시지가 보이지 않음

### <a name="probable-causes"></a>가능한 원인

* Linux 서버에 적용된 구성에서는 전송된 시설 및/또는 로그 수준을 수집할 수 없습니다.
* Syslog가 Linux 서버로 올바르게 전달되지 않고 있습니다.
* 초당 전달되는 메시지 수가 너무 많아 Linux용 Log Analytics 에이전트의 기본 구성에서 처리할 수 없습니다.

### <a name="resolution"></a>해결 방법

* Syslog용 Log Analytics 작업 영역의 구성에서 모든 설비가 있고 로그 수준이 올바른지 확인합니다. [Azure Portal에서 Syslog 수집 구성](data-sources-syslog.md#configure-syslog-in-the-azure-portal)을 검토합니다.
* 네이티브 syslog 메시징 디먼(`rsyslog`, `syslog-ng`)이 전달된 메시지를 수신할 수 있는지 확인합니다.
* Syslog 서버에서 방화벽 설정을 확인하여 메시지가 차단되지 않는지 확인합니다.
* `logger` 명령을 사용하여 Syslog 메시지를 Log Analytics에 시뮬레이션 합니다.
  * `logger -p local0.err "This is my test message"`

## <a name="issue-you-are-receiving-errno-address-already-in-use-in-omsagent-log-file"></a>문제: 이미 omsagent 로그 파일에서 사용 중인 Errno 주소를 수신

omsagent.log에 `[error]: unexpected error error_class=Errno::EADDRINUSE error=#<Errno::EADDRINUSE: Address already in use - bind(2) for "127.0.0.1" port 25224>` 오류 메시지가 보입니다.

### <a name="probable-causes"></a>가능한 원인

이 오류는 LAD(Linux 진단 확장)이 Log Analytics Linux VM 확장과 함께 설치되었으며, syslog 데이터 수집에 omsagent와 동일한 포트를 사용하고 있음을 의미합니다.

### <a name="resolution"></a>해결 방법

1. 루트로 다음 명령을 실행합니다(25224는 예제이며 해당 환경에서 LAD가 사용하는 다른 포트 번호가 표시될 수 있음).

    ```
    /opt/microsoft/omsagent/bin/configure_syslog.sh configure LAD 25229

    sed -i -e 's/25224/25229/' /etc/opt/microsoft/omsagent/LAD/conf/omsagent.d/syslog.conf
    ```

    그리고 올바른 `rsyslogd` 또는 `syslog_ng` 구성 파일을 편집하고, 작성할 LAD 관련 구성을 포트 25229로 변경해야 합니다.

2. VM에서 `rsyslogd`를 실행 중인 경우 수정할 파일은 `/etc/rsyslog.d/95-omsagent.conf`입니다(이 파일이 없으면 `/etc/rsyslog`). VM에서 `syslog_ng`를 실행 중인 경우 수정할 파일은 `/etc/syslog-ng/syslog-ng.conf`입니다.
3. omsagent `sudo /opt/microsoft/omsagent/bin/service_control restart`를 다시 시작합니다.
4. syslog 서비스를 다시 시작합니다.

## <a name="issue-you-are-unable-to-uninstall-omsagent-using-purge-option"></a>문제: 제거 옵션을 사용하여 omsagent를 제거할 수 없음

### <a name="probable-causes"></a>가능한 원인

* Linux 진단 확장이 설치되었습니다.
* Linux 진단 확장을 설치했다 제거했지만, mdsd에서 omsagent를 사용 중이며 제거할 수 없다는 오류가 계속 표시됩니다.

### <a name="resolution"></a>해결 방법

1. LAD(Linux 진단 확장)를 제거합니다.
2. Linux 진단 확장 파일이 `/var/lib/waagent/Microsoft.Azure.Diagnostics.LinuxDiagnostic-<version>/` 및 `/var/opt/microsoft/omsagent/LAD/`에 있는 경우 머신에서 파일을 제거합니다.

## <a name="issue-you-cannot-see-data-any-nagios-data"></a>문제: Nagios 데이터가 보이지 않음

### <a name="probable-causes"></a>가능한 원인

* Omsagent 사용자가 Nagios 로그 파일에서 읽을 수 있는 권한을 갖고 있지 않습니다.
* omsagent.conf 파일에서 Nagios 원본 및 필터의 주석 처리가 제거되지 않았습니다.

### <a name="resolution"></a>해결 방법

1. 다음 [지침](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/OMS-Agent-for-Linux.md#nagios-alerts)에 따라 Nagios 파일에서 읽을 omsagent 사용자를 추가합니다.
2. `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.conf`의 Linux용 Log Analytics 에이전트 일반 구성 파일에서, Nagios 원본 및 필터 **둘 다** 주석 처리가 제거되었는지 확인합니다.

    ```
    <source>
      type tail
      path /var/log/nagios/nagios.log
      format none
      tag oms.nagios
    </source>

    <filter oms.nagios>
      type filter_nagios_log
    </filter>
    ```

## <a name="issue-you-are-not-seeing-any-linux-data"></a>문제: Linux 데이터가 보이지 않음

### <a name="probable-causes"></a>가능한 원인

* Azure Monitor 온보딩 실패
* Azure Monitor 연결 차단
* 가상 머신이 다시 부팅되었습니다.
* OMI 패키지가 Linux용 Log Analytics 에이전트 패키지에서 설치한 버전보다 높은 버전으로 수동 업그레이드되었습니다.
* OMI가 고정되어 OMS 에이전트를 차단합니다
* DSC 리소스가 `omsconfig.log` 로그 파일에 *클래스를 찾을 수 없음* 오류를 기록합니다.
* 데이터용 Log Analytics 에이전트가 백업되었습니다.
* DSC가 *현재 구성이 존재하지 않습니다. Start-DscConfiguration 명령을 -Path 매개 변수와 함께 실행하여 구성 파일을 명시하고 현재 구성을 우선 만드세요* 를 로그합니다. 오류를 `omsconfig.log` 로그 파일에 기록하지만, `PerformRequiredConfigurationChecks` 작업에 대한 로그 메시지가 없습니다.

### <a name="resolution"></a>해결 방법

1. auditd 패키지 같은 모든 종속성을 설치합니다.
2. 다음 파일이 있는지 확인하여 Azure Monitor 온보딩에 성공했는지 확인합니다. `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsadmin.conf`  성공하지 못했으면 omsadmin.sh 명령줄 [지침](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/OMS-Agent-for-Linux.md#onboarding-using-the-command-line)을 사용하여 다시 등록합니다.
4. 프록시를 사용하는 경우 위의 프록시 문제 해결 단계를 확인합니다.
5. 일부 Azure 배포 시스템에서는 가상 머신이 다시 부팅된 후 omid OMI 서버 디먼이 시작되지 않습니다. 따라서 감사, ChangeTracking 또는 UpdateManagement 솔루션과 관련된 데이터가 표시되지 않습니다. 해결책은 `sudo /opt/omi/bin/service_control restart` 명령을 실행하여 omi 서버를 수동으로 시작하는 것입니다.
6. OMI 패키지를 더 높은 버전으로 수동 업그레이드한 후에는 수동으로 다시 시작해야만 Log Analytics 에이전트가 계속 작동합니다. OMI 서버가 업그레이드 후 자동으로 시작되지 않는 일부 배포판에서는 이 단계가 필수입니다. `sudo /opt/omi/bin/service_control restart` 명령을 실행하여 OMI를 다시 시작합니다.
* 경우에 따라 OMI가 고정될 수 있습니다. OMS 에이전트는 모든 데이터 수집을 차단하여 OMI을 기다리는 차단된 상태에 들어갈 수 있습니다. OMS 에이전트 프로세스는 실행되고 있지만 활동은 없으며, `omsagent.log`에 새로운 로그 라인(예: 전송된 하트비트)이 없다는 점으로 이를 확인할 수 있습니다. `sudo /opt/omi/bin/service_control restart`를 사용해 OMI를 재시작하여 에이전트를 복구합니다.
7. omsconfig.log에 DSC 리소스 *클래스를 찾을 수 없음* 오류가 표시되면 `sudo /opt/omi/bin/service_control restart` 명령을 실행합니다.
8. 경우에 따라 Linux용 Log Analytics 에이전트가 Azure Monitor와 통신할 수 없는 경우 에이전트의 데이터가 전체 버퍼 크기 50MB로 백업됩니다. `/opt/microsoft/omsagent/bin/service_control restart` 명령을 실행하여 에이전트를 다시 시작해야 합니다.

    > [!NOTE]
    > 이 문제는 에이전트 버전 1.1.0-28 이상에서 해결되었습니다.
    >

* `omsconfig.log` 로그 파일에 시스템에서 `PerformRequiredConfigurationChecks` 작업이 주기적으로 실행되고 있다고 표시되지 않으면 cron 작업/서비스에 문제가 있는 것일 수 있습니다. `/etc/cron.d/OMSConsistencyInvoker` 아래에서 cron 작업 있는지 확인합니다. 필요하다면 다음 명령을 실행하여 cron 작업을 만듭니다.

    ```
    mkdir -p /etc/cron.d/
    echo "*/15 * * * * omsagent /opt/omi/bin/OMSConsistencyInvoker >/dev/null 2>&1" | sudo tee /etc/cron.d/OMSConsistencyInvoker
    ```

    또한 cron 서비스가 실행 중인지 확인합니다. Debian, Ubuntu, SUSE에서 `service cron status` 명령을 사용하거나 RHEL, CentOS, Oracle Linux에서 `service crond status` 명령을 사용하여 이 서비스의 상태를 확인할 수 있습니다. 서비스가 존재하지 않는 경우 다음 명령을 사용하여 이진 파일을 설치하고 서비스를 시작할 수 있습니다.

    **Ubuntu/Debian**

    ```
    # To Install the service binaries
    sudo apt-get install -y cron
    # To start the service
    sudo service cron start
    ```

    **SUSE**

    ```
    # To Install the service binaries
    sudo zypper in cron -y
    # To start the service
    sudo systemctl enable cron
    sudo systemctl start cron
    ```

    **RHEL/CeonOS**

    ```
    # To Install the service binaries
    sudo yum install -y crond
    # To start the service
    sudo service crond start
    ```

    **Oracle Linux**

    ```
    # To Install the service binaries
    sudo yum install -y cronie
    # To start the service
    sudo service crond start
    ```

## <a name="issue-when-configuring-collection-from-the-portal-for-syslog-or-linux-performance-counters-the-settings-are-not-applied"></a>문제: 포털에서 Syslog 또는 Linux 성능 카운터에 대한 컬렉션을 구성할 때 설정이 적용되지 않음

### <a name="probable-causes"></a>가능한 원인

* Linux용 Log Analytics 에이전트가 최신 구성을 선택하지 않았습니다.
* 포털에서 변경한 설정이 적용되지 않았습니다.

### <a name="resolution"></a>해결 방법

**백그라운드:** `omsconfig`는 5분마다 새 포털 쪽 구성을 검색하는 Linux용 Log Analytics 에이전트 구성 에이전트입니다. 이 구성은 /etc/opt/microsoft/omsagent/conf/omsagent.conf에 있는 Linux용 Log Analytics 에이전트 구성 파일에 적용됩니다.

* Linux용 Log Analytics 에이전트 구성 에이전트가 포털 구성 서비스와 통신할 수 없어 최신 구성이 적용되지 않는 경우가 있습니다.
  1. `dpkg --list omsconfig` 또는 `rpm -qi omsconfig` 명령을 실행하여 `omsconfig` 에이전트가 설치되어 있는지 확인합니다.  설치되지 않은 경우 Linux용 Log Analytics 에이전트 최신 버전을 다시 설치합니다.

  2. 다음 명령을 실행하여 `omsconfig` 에이전트가 Azure Monitor와 통신할 수 있는지 확인합니다. `sudo su omsagent -c 'python /opt/microsoft/omsconfig/Scripts/GetDscConfiguration.py'`. 이 명령은 Syslog 설정, Linux 성능 카운터, 사용자 지정 로그 등 에이전트가 서비스에서 수신하는 구성을 반환합니다. 이 명령이 실패하는 경우 `sudo su omsagent -c 'python /opt/microsoft/omsconfig/Scripts/PerformRequiredConfigurationChecks.py'` 명령을 실행합니다. 이 명령은 omsconfig 에이전트가 Azure Monitor와 통신하여 최신 구성을 검색하도록 강제합니다.

## <a name="issue-you-are-not-seeing-any-custom-log-data"></a>문제: 사용자 지정 로그 데이터가 보이지 않음

### <a name="probable-causes"></a>가능한 원인

* Azure Monitor에 온보딩이 실패하였습니다.
* **다음 구성을 Linux 서버에 적용** 설정을 선택하지 않았습니다.
* omsconfig가 서비스의 최신 사용자 지정 로그 구성을 선택하지 않았습니다.
* 권한이 없어서 또는 사용자 지정 로그가 없어서 Linux 사용자용 Log Analytics 에이전트 `omsagent`가 사용자 지정 로그에 액세스할 수 없습니다.  다음 오류가 표시될 수 있습니다.
* `[DATETIME] [warn]: file not found. Continuing without tailing it.`
* `[DATETIME] [error]: file not accessible by omsagent.`
* Linux용 Log Analytics 에이전트 버전 1.1.0-217에서 수정된 경합 조건 문제입니다.

### <a name="resolution"></a>해결 방법

1. 다음 파일이 있는지 확인하여 Azure Monitor 온보딩이 성공했는지 확인합니다. `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsadmin.conf`. 실패한 경우 둘 중 하나를 수행합니다.

  1. omsadmin.sh 명령줄 [지침](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/OMS-Agent-for-Linux.md#onboarding-using-the-command-line)을 사용하여 다시 등록합니다.
  2. Azure Portal의 **고급 설정** 아래에서 **다음 구성을 Linux 서버에 적용** 설정을 사용합니다.

2. 다음 명령을 실행하여 `omsconfig` 에이전트가 Azure Monitor와 통신할 수 있는지 확인합니다. `sudo su omsagent -c 'python /opt/microsoft/omsconfig/Scripts/GetDscConfiguration.py'`.  이 명령은 Syslog 설정, Linux 성능 카운터, 사용자 지정 로그 등 에이전트가 서비스에서 수신하는 구성을 반환합니다. 이 명령이 실패하는 경우 `sudo su omsagent -c 'python /opt/microsoft/omsconfig/Scripts/PerformRequiredConfigurationChecks.py'` 명령을 실행합니다. 이 명령은 omsconfig 에이전트가 Azure Monitor와 통신하여 최신 구성을 검색하도록 강제합니다.

**백그라운드:** Linux용 Log Analytics 에이전트가 권한 있는 사용자 `root`로 실행되지 않고 `omsagent` 사용자로 실행됩니다. 대부분의 경우 이 사용자가 특정 파일을 읽으려면 명시적 권한을 부여해야 합니다. `omsagent` 사용자에게 권한을 부여하려면 다음 명령을 실행합니다.

1. `sudo usermod -a -G <GROUPNAME> <USERNAME>` 명령을 사용하여 `omsagent` 사용자를 특정 그룹에 추가합니다.
2. 필수 파일 `sudo chmod -R ugo+rx <FILE DIRECTORY>`에 대한 공용 읽기 액세스 권한을 부여합니다.

Linux용 Log Analytics 에이전트 1.1.0-217 미만 버전에서 발생하는 것으로 알려진 경쟁 조건 문제입니다. 최신 에이전트로 업데이트한 후 `sudo cp /etc/opt/microsoft/omsagent/sysconf/omsagent.conf /etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.conf` 명령을 사용하여 최신 버전의 출력 플러그 인을 가져오세요.

## <a name="issue-you-are-trying-to-reonboard-to-a-new-workspace"></a>문제: 새 작업 영역에 다시 등록하려고 시도

에이전트를 새 작업 영역에 다시 등록하려고 시도할 때 Log Analytics 에이전트 구성을 정리한 후 다시 등록해야 합니다. 에이전트에서 이전 구성을 정리하려면 `--purge` 명령을 사용하여 셸 번들을 실행합니다.

```
sudo sh ./omsagent-*.universal.x64.sh --purge
```

또는

```
sudo sh ./onboard_agent.sh --purge
```

`--purge` 옵션을 사용한 후 다시 등록할 수 있습니다.

## <a name="log-analytics-agent-extension-in-the-azure-portal-is-marked-with-a-failed-state-provisioning-failed"></a>Azure Portal의 Log Analytics 에이전트 확장이 실패 상태: 프로비전 실패로 표시됩니다.

### <a name="probable-causes"></a>가능한 원인

* Log Analytics 에이전트가 운영 체제에서 제거되었습니다.
* Log Analytics 에이전트 서비스가 중지되었거나, 해제되었거나, 구성되지 않았습니다.

### <a name="resolution"></a>해결 방법

다음 단계에 따라 문제를 해결합니다.
1. Azure Portal에서 확장을 제거합니다.
2. [지침](../vm/monitor-virtual-machine.md)에 따라 에이전트를 설치합니다.
3. `sudo /opt/microsoft/omsagent/bin/service_control restart` 명령을 실행하여 에이전트를 다시 시작합니다.
* 몇 분 정도 기다리면 프로비전 상태가 **프로비전 성공** 으로 변경됩니다.

## <a name="issue-the-log-analytics-agent-upgrade-on-demand"></a>문제: Log Analytics 에이전트 주문형 업그레이드

### <a name="probable-causes"></a>가능한 원인

호스트의 Log Analytics 에이전트 패키지가 오래되었습니다.

### <a name="resolution"></a>해결 방법

다음 단계에 따라 문제를 해결합니다.

1. [페이지](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/)에서 최신 릴리스를 확인합니다.
2. 설치 스크립트를 다운로드합니다(예: 1.4.2-124 버전).

    ```
    wget https://github.com/Microsoft/OMS-Agent-for-Linux/releases/download/OMSAgent_GA_v1.4.2-124/omsagent-1.4.2-124.universal.x64.sh
    ```

3. `sudo sh ./omsagent-*.universal.x64.sh --upgrade` 명령을 실행하여 패키지를 업그레이드합니다.

## <a name="issue-installation-is-failing-saying-python2-cannot-support-ctypes-even-though-python3-is-being-used"></a>문제: Python3를 사용 하는 경우에도 Python2가 ctypes를 지원할 수 없다는 오류 메시지가 설치 되지 않습니다.

### <a name="probable-causes"></a>가능한 원인

알려진 문제가 있습니다. VM의 언어가 영어가 아니면 사용 중인 Python 버전을 확인할 때 검사가 실패 합니다. 그러면 에이전트가 항상 Python2을 사용 하 고 있다고 가정 하 고 Python2 없는 경우 오류가 발생 합니다.

### <a name="resolution"></a>해결 방법

VM의 환경 언어를 영어로 변경 합니다.

```
export LANG=en_US.UTF-8
```
