---
title: Microsoft Sentinel | Syslog 데이터 커넥트 Microsoft Docs
description: 어플라이언스와 Microsoft Sentinel 간에 Linux 머신의 에이전트를 사용하여 Microsoft Sentinel에 Syslog를 지원하는 모든 컴퓨터 또는 어플라이언스를 커넥트.
author: yelevin
ms.topic: how-to
ms.date: 11/09/2021
ms.author: yelevin
ms.custom: ignite-fall-2021
ms.openlocfilehash: 1b43bf012bc580f2cfbee9d142c448ac7eff87da
ms.sourcegitcommit: 1244a72dbec39ac8cf16bb1799d8c46bde749d47
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/18/2021
ms.locfileid: "132756555"
---
# <a name="collect-data-from-linux-based-sources-using-syslog"></a>Syslog를 사용하여 Linux 기반 소스에서 데이터 수집

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

**Syslog** 는 Linux에 공통되는 이벤트 로깅 프로토콜입니다. Linux 디바이스 및 어플라이언스에 기본 제공되는 Syslog 디먼을 사용하여 지정한 유형의 로컬 이벤트를 수집하고 **Linux용 Log Analytics** 에이전트(이전의 OMS 에이전트)를 사용하여 Microsoft Sentinel에 해당 이벤트를 보내도록 할 수 있습니다.

이 문서에서는 Syslog를 사용하여 Microsoft Sentinel에 데이터 원본을 연결하는 방법을 설명합니다. 이 메서드에 지원되는 커넥터에 대한 자세한 내용은 [데이터 커넥터 참조](data-connectors-reference.md)를 참조하세요.

## <a name="architecture"></a>아키텍처

Log Analytics 에이전트가 VM 또는 어플라이언스에 설치된 경우 설치 스크립트는 UDP 포트 25224의 에이전트로 메시지를 전달하도록 로컬 Syslog 디먼을 구성합니다. 메시지를 받은 후 에이전트는 HTTPS를 통해 Log Analytics 작업 영역으로 보냅니다. 여기서 해당 메시지는 Microsoft **Sentinel > Logs의** Syslog 테이블에 저장됩니다.

자세한 내용은 [Azure Monitor의 Syslog 데이터 원본](../azure-monitor/agents/data-sources-syslog.md)을 참조하세요.

:::image type="content" source="media/connect-syslog/syslog-diagram.png" alt-text="이 다이어그램은 데이터 원본 디바이스에 Log Analytics 에이전트가 직접 설치되는 Microsoft Sentinel 작업 영역으로의 syslog 원본 데이터 흐름을 보여줍니다.":::

Log Analytics 에이전트의 로컬 설치를 허용하지 않는 디바이스 유형의 경우 에이전트를 전용 Linux 기반 로그 전달자에 설치할 수 있습니다. 원래 디바이스는 Syslog 이벤트를 로컬 디먼이 아닌 이 전달자의 Syslog 디먼으로 전송하도록 구성되어야 합니다. 전달자의 Syslog 디먼은 UDP를 통해 이벤트를 Log Analytics 에이전트로 보냅니다. 이 Linux 전달자에서 많은 Syslog 이벤트를 수집해야 하는 경우, 전달자의 Syslog 디먼은 TCP를 통해 이벤트를 에이전트로 보냅니다. 두 경우 모두 에이전트는 해당 위치에서 Microsoft Sentinel의 Log Analytics 작업 영역으로 이벤트를 보냅니다.

:::image type="content" source="media/connect-syslog/syslog-forwarder-diagram.png" alt-text="이 다이어그램은 Syslog 원본에서 별도의 로그 전달 디바이스에 Log Analytics 에이전트가 설치되는 Microsoft Sentinel 작업 영역으로의 데이터 흐름을 보여줍니다.":::

> [!NOTE]
> - 어플라이언스가 Syslog를 통해 **CEF(Common Event Format)** 를 지원하는 경우 더 완전한 데이터 세트가 수집되고 데이터는 컬렉션에서 구문 분석됩니다. 이 옵션을 선택하고 [디바이스 또는 어플라이언스에서 Microsoft Sentinel로 CEF 형식 로그 얻기의](connect-common-event-format.md)지침을 따라야 합니다.
>
> - Log Analytics는 **rsyslog** 또는 **syslog-ng** 디먼에서 보낸 메시지 수집을 지원하며, 여기서는 rsyslog가 기본값입니다. Red Hat Enterprise Linux(RHEL) 버전 5, CentOS 및 Oracle Linux 버전(**sysklog**)에서는 syslog 이벤트 수집을 위한 기본 syslog 디먼이 지원되지 않습니다. 이 배포의 해당 버전에서 syslog 데이터를 수집하려면 rsyslog 디먼을 설치하고 sysklog를 대체하도록 구성해야 합니다.

Syslog 수집은 3단계로 구성합니다.

- **Linux 디바이스 또는 어플라이언스를 구성합니다**. Log Analytics 에이전트가 설치되는 디바이스를 말합니다. 이벤트가 처음 발생한 것과 같은 디바이스이거나 이벤트를 전달하는 로그 수집기입니다.

- 이벤트를 에이전트로 전달하는 Syslog 디먼의 위치에 해당하는 **애플리케이션의 로깅 설정을 구성합니다**.

- **Log Analytics 에이전트 자체를 구성합니다**. 이 작업은 Microsoft Sentinel 내에서 수행되며 구성은 설치된 모든 에이전트로 전송됩니다.

## <a name="configure-your-linux-machine-or-appliance"></a>Linux 컴퓨터 또는 어플라이언스 구성

1. Microsoft Sentinel 탐색 메뉴에서 **데이터 커넥터를 선택합니다.**

1. 커넥터 갤러리에서 **Syslog** 를 선택한 다음, **커넥터 페이지 열기** 를 선택합니다.

    디바이스 유형이 Microsoft Sentinel 데이터 **커넥터 갤러리에** 나열된 경우 일반 Syslog 커넥터 대신 디바이스에 대한 커넥터를 선택합니다. 본인의 디바이스 유형에 대한 추가 또는 특별 지침이 존재하는 경우 이러한 지침이 통합 문서와 분석 규칙 템플릿 같은 사용자 지정 콘텐츠와 함께 디바이스의 커넥터 페이지에 표시됩니다.

1. Linux 에이전트를 설치합니다. **에이전트를 설치할 위치 선택:** 에서 다음을 수행합니다.

    |머신 형식  |Instructions  |
    |---------|---------|
    |**Azure Linux VM의 경우**     |    1. **Azure Linux 가상 머신에 에이전트 설치** 를 확장합니다. <br><br>2. **Azure Linux 가상 머신용 에이전트 다운로드 및 설치 >** 링크를 선택합니다.<br><br>3. **가상 머신** 블레이드에서 에이전트를 설치할 가상 머신을 선택한 다음 **연결** 을 선택합니다. 연결하려는 각 VM에 이 단계를 반복합니다.     |
    |**기타 Linux 머신의 경우**     |     1. **비 Azure Linux 머신에 에이전트 설치** 를 확장합니다. <br><br>2. **비 Azure Linux 머신용 에이전트 다운로드 및 설치** 링크를 선택합니다.<br><br>3. **에이전트 관리** 블레이드에서 **Linux 서버** 탭을 선택한 다음 **Linux용 에이전트 다운로드 및 온보딩** 명령을 복사하여 Linux 컴퓨터에서 실행합니다.<br><br>        Linux 에이전트 설치 파일의 로컬 복사본을 유지하고 싶다면 ‘에이전트 다운로드 및 온보딩’ 명령 위에 있는 **Linux 에이전트 다운로드** 링크를 선택합니다. |
    |     |         |

   > [!NOTE]
   > 이러한 디바이스의 보안 설정은 조직의 보안 정책에 따라 구성해야 합니다. 예를 들어 조직의 네트워크 보안 정책에 맞게 네트워크 설정을 구성하고 보안 요구 사항에 맞게 디먼의 포트 및 프로토콜을 변경할 수 있습니다.

### <a name="using-the-same-machine-to-forward-both-plain-syslog-and-cef-messages"></a>동일한 컴퓨터를 사용하여 일반 Syslog 및 CEF 메시지 전달

기존 [CEF 로그 전달자 컴퓨터](connect-log-forwarder.md)를 사용하여 일반 Syslog 원본에서도 로그를 수집하고 전달할 수 있습니다. 그러나 두 형식의 이벤트를 Microsoft Sentinel에 보내지 않도록 하려면 다음 단계를 수행해야 합니다. 따라서 이벤트가 중복됩니다.

이미 [CEF 원본에서 데이터 수집](connect-common-event-format.md)을 설정하고 Log Analytics 에이전트를 구성한 경우:

1. CEF 형식의 로그를 보내는 각 컴퓨터에서 Syslog 구성 파일을 편집하여 CEF 메시지를 보내는 데 사용되는 기능을 제거해야 합니다. 이렇게 하면 CEF 형식으로 전송되는 기능이 Syslog 형식으로도 전송되는 것을 방지할 수 있습니다. 이 작업을 수행하는 방법에 대한 자세한 지침은 [Linux 에이전트에서 Syslog 구성](../azure-monitor/agents/data-sources-syslog.md#configure-syslog-on-linux-agent)을 참조하세요.

1. 이러한 컴퓨터에서 다음 명령을 실행하여 Microsoft Sentinel에서 Syslog 구성과 에이전트의 동기화를 사용하지 않도록 설정해야 합니다. 이렇게 하면 이전 단계에서 변경한 구성을 덮어쓰지 않습니다.

    ```c
    sudo su omsagent -c 'python /opt/microsoft/omsconfig/Scripts/OMS_MetaConfigHelper.py --disable
    ```

## <a name="configure-your-devices-logging-settings"></a>디바이스의 로깅 설정 구성

많은 디바이스 유형에서는 자체 데이터 커넥터가 **데이터 커넥터** 갤러리에 표시됩니다. 이러한 커넥터 중 일부는 Microsoft Sentinel에서 로그 수집을 올바르게 설정하기 위한 특별한 추가 지침이 필요합니다. 이 지침에는 Kusto 함수를 기반으로 하는 파서 구현이 포함될 수 있습니다.

갤러리에 나열된 모든 커넥터는 포털의 해당 커넥터 페이지와 [Microsoft Sentinel 데이터 커넥터 참조](data-connectors-reference.md) 페이지의 해당 섹션에 특정 지침을 표시합니다.


## <a name="configure-the-log-analytics-agent"></a>Log Analytics 에이전트 구성

1. Syslog 커넥터 블레이드의 아래쪽에서 **작업 영역 에이전트 구성 열기 >** 링크를 선택합니다.

1. **에이전트 구성** 블레이드에서 **Syslog** 탭을 선택합니다. 그런 다음 수집할 커넥터의 기능을 추가합니다. **기능 추가** 를 선택하고 기능 드롭다운 목록에서 선택합니다.

    - syslog 어플라이언스의 로그 헤더에 포함된 기능을 추가합니다.

    - 수집하는 데이터와 함께 비정상적인 SSH 로그인 검색을 사용하려는 경우 **auth** 및 **authpriv** 를 추가합니다. 자세한 내용은 [다음 섹션](#configure-the-syslog-connector-for-anomalous-ssh-login-detection)을 참조하세요.

1. 모니터링할 모든 기능을 추가했다면, 수집하지 않을 확인란을 심각도에 상관없이 선택 취소합니다. 기본적으로는 모든 확인란이 선택됩니다.

1. **적용** 을 선택합니다.

1. VM 또는 어플라이언스에서 지정한 기능이 전송되고 있는지 확인합니다.

## <a name="find-your-data"></a>데이터 찾기

1. **로그** 에서 syslog 로그 데이터를 쿼리하려면 쿼리 창에 `Syslog`를 입력합니다.

1. [Azure Monitor 로그 쿼리에서 함수 사용](../azure-monitor/logs/functions.md)에 설명된 쿼리 매개 변수를 사용하여 Syslog 메시지를 구문 분석할 수 있습니다. 그런 다음 쿼리를 새 Log Analytics 함수로 저장하고 새 데이터 형식으로 사용할 수 있습니다.

### <a name="configure-the-syslog-connector-for-anomalous-ssh-login-detection"></a>비정상적인 SSH 로그인 검색을 위한 Syslog 커넥터 구성

> [!IMPORTANT]
> 비정상적인 SSH 로그인 검색은 현재 **미리 보기** 상태입니다. 베타 또는 미리 보기로 제공되거나 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 추가 약관은 [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

Microsoft Sentinel은 syslog 데이터에 기계 학습(ML)을 적용하여 SSH(비정상적인 Secure Shell) 로그인 활동을 식별할 수 있습니다. 다음과 같은 시나리오가 있습니다.

- 불가능한 이동 - 두 로그인 이벤트의 시간 범위 내에 도달이 불가능한 두 위치에서 두 개의 성공적인 로그인 이벤트가 발생하는 경우입니다.

- 예기치 않은 위치 – 성공적인 로그인 이벤트가 발생한 위치가 의심스러운 경우입니다. 최근에 본 적이 없는 위치를 예로 들 수 있습니다.
 
이 검색에는 Syslog 데이터 커넥터의 특정 구성이 필요합니다. 

1. 위의 [Log Analytics 에이전트 구성](#configure-the-log-analytics-agent) 2단계의 경우 **auth** 및 **authpriv** 모두 모니터링할 기능으로 선택되고 모든 심각도가 선택되어 있는지 확인합니다. 

2. syslog 정보를 수집할 수 있는 충분한 시간을 허용합니다. 그런 **다음, Microsoft Sentinel - Logs** 로 이동하여 다음 쿼리를 복사하여 붙여넣습니다.
    
    ```kusto
    Syslog
    | where Facility in ("authpriv","auth")
    | extend c = extract( "Accepted\\s(publickey|password|keyboard-interactive/pam)\\sfor ([^\\s]+)",1,SyslogMessage)
    | where isnotempty(c)
    | count 
    ```
    
    필요한 경우 **시간 범위** 를 변경하고 **실행** 을 선택합니다.
    
    결과 개수가 0이면 커넥터의 구성을 확인하고 모니터링되는 컴퓨터에 쿼리를 위해 지정한 기간 동안 성공한 로그인 작업이 있는지 확인합니다.
    
    결과 개수가 0보다 크면 syslog 데이터가 비정상적인 SSH 로그인 검색에 적합한 것입니다. **분석** >  **규칙 템플릿** >  **(미리 보기) 비정상적인 SSH 로그인 검색** 에서 이 검색을 사용하도록 설정합니다.

## <a name="next-steps"></a>다음 단계
이 문서에서는 Syslog 온-프레미스 어플라이언스를 Microsoft Sentinel에 연결하는 방법을 배웠습니다. Microsoft Sentinel에 대한 자세한 내용은 다음 문서를 참조하세요.
- [데이터에 대한 가시성을 얻고 재적 위협을 확인](get-visibility.md)하는 방법을 알아봅니다.
- [Microsoft Sentinel을 사용하여 위협 검색을](detect-threats-built-in.md)시작합니다.
- [통합 문서를 사용](monitor-your-data.md)하여 데이터를 모니터링합니다.
