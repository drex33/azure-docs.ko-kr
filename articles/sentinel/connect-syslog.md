---
title: Azure Sentinel에 Syslog 데이터 연결| Microsoft Docs
description: 어플라이언스와 Azure Sentinel 사이에서 Linux 컴퓨터의 에이전트를 사용하여 Syslog를 지원하는 컴퓨터 또는 어플라이언스를 Azure Sentinel에 연결합니다.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/17/2020
ms.author: yelevin
ms.openlocfilehash: d0f4f2ff4fdd2d33e1ac1b56b02fb185203aee39
ms.sourcegitcommit: 05dd6452632e00645ec0716a5943c7ac6c9bec7c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/17/2021
ms.locfileid: "122538319"
---
# <a name="collect-data-from-linux-based-sources-using-syslog"></a>Syslog를 사용하여 Linux 기반 소스에서 데이터 수집

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

Linux 기반 Syslog 지원 컴퓨터 또는 어플라이언스에서 Linux용 Log Analytics 에이전트(이전의 OMS 에이전트)를 사용하여 Azure Sentinel로 이벤트를 스트리밍할 수 있습니다. Log Analytics 에이전트를 직접 설치할 수 있는 컴퓨터라면 이 작업을 수행할 수 있습니다. 컴퓨터의 기본 Syslog 디먼은 지정된 형식의 로컬 이벤트를 수집하여 에이전트에 로컬로 전달하며, 에이전트는 이를 Log Analytics 작업 영역으로 스트리밍합니다.

> [!NOTE]
> - 어플라이언스가 Syslog를 통해 **CEF(Common Event Format)** 를 지원하는 경우 더 완전한 데이터 세트가 수집되고 데이터는 컬렉션에서 구문 분석됩니다. 이 옵션을 선택하고 [CEF를 사용하여 외부 솔루션 연결](connect-common-event-format.md)의 지침에 따라야 합니다.
>
> - Log Analytics는 **rsyslog** 또는 **syslog-ng** 디먼에서 보낸 메시지 수집을 지원하며, 여기서는 rsyslog가 기본값입니다. Red Hat Enterprise Linux(RHEL) 버전 5, CentOS 및 Oracle Linux 버전(**sysklog**)에서는 syslog 이벤트 수집을 위한 기본 syslog 디먼이 지원되지 않습니다. 이 배포의 해당 버전에서 syslog 데이터를 수집하려면 rsyslog 디먼을 설치하고 sysklog를 대체하도록 구성해야 합니다.

## <a name="how-it-works"></a>작동 방법

**Syslog** 는 Linux에 공통되는 이벤트 로깅 프로토콜입니다. **Linux용 Log Analytics 에이전트** 가 VM 또는 어플라이언스에 설치된 경우 설치 루틴은 TCP 포트 25224의 에이전트로 메시지를 전달하도록 로컬 Syslog 디먼을 구성합니다. 그러면 에이전트는 HTTPS를 통해 Log Analytics 작업 영역으로 메시지를 보내고 **Azure Sentinel > 로그** 의 Syslog 테이블에 있는 이벤트 로그 항목으로 구문 분석됩니다.

자세한 내용은 [Azure Monitor의 Syslog 데이터 원본](../azure-monitor/agents/data-sources-syslog.md)을 참조하세요.

## <a name="configure-syslog-collection"></a>Syslog 컬렉션 구성

### <a name="configure-your-linux-machine-or-appliance"></a>Linux 컴퓨터 또는 어플라이언스 구성

1. Azure Sentinel에서 **데이터 커넥터** 를 선택한 다음 **Syslog** 커넥터를 선택합니다.

1. **Syslog** 블레이드에서 **커넥터 페이지 열기** 를 선택합니다.

1. Linux 에이전트를 설치합니다. **에이전트를 설치할 위치 선택:** 에서 다음을 수행합니다.
    
    **Azure Linux VM의 경우:**
      
    1. **Azure Linux 가상 머신에 에이전트 설치** 를 선택합니다.
    
    1. **Azure Linux 가상 머신용 에이전트 다운로드 및 설치** 링크를 클릭합니다. 
    
    1. **가상 머신** 블레이드에서 에이전트를 설치할 가상 머신을 클릭한 다음 **연결** 을 클릭합니다. 연결하려는 각 VM에 이 단계를 반복합니다.
    
    **기타 Linux 머신의 경우:**

    1. **비 Azure Linux 머신에 에이전트 설치** 를 선택합니다.

    1. **비 Azure Linux 컴퓨터용 에이전트 다운로드 및 설치** 링크를 클릭합니다. 

    1. **에이전트 관리** 블레이드에서 **Linux 서버** 탭을 클릭한 다음 **Linux용 에이전트 다운로드 및 온보딩** 명령을 복사하여 Linux 컴퓨터에서 실행합니다. 
    
   > [!NOTE]
   > 이러한 컴퓨터의 보안 설정은 조직의 보안 정책에 따라 구성해야 합니다. 예를 들어 조직의 네트워크 보안 정책에 맞게 네트워크 설정을 구성하고 보안 요구 사항에 맞게 디먼의 포트 및 프로토콜을 변경할 수 있습니다.

### <a name="configure-the-log-analytics-agent"></a>Log Analytics 에이전트 구성

1. Syslog 커넥터 블레이드의 아래쪽에서 **작업 영역 에이전트 구성 열기 >** 링크를 클릭합니다.

1. **에이전트 구성** 블레이드에서 **Syslog** 탭을 선택합니다. 그런 다음 수집할 커넥터의 기능을 추가합니다. **기능 추가** 를 선택하고 기능 드롭다운 목록에서 선택합니다.
    
    - syslog 어플라이언스의 로그 헤더에 포함된 기능을 추가합니다. 
    
    - 수집하는 데이터와 함께 비정상적인 SSH 로그인 검색을 사용하려는 경우 **auth** 및 **authpriv** 를 추가합니다. 자세한 내용은 [다음 섹션](#configure-the-syslog-connector-for-anomalous-ssh-login-detection)을 참조하세요.

1. 모니터링할 모든 기능을 추가했으면 원하는 모든 심각도의 확인란이 표시되어 있는지 확인합니다.

1. **적용** 을 선택합니다. 

1. VM 또는 어플라이언스에서 지정한 기능이 전송되고 있는지 확인합니다.

1. **로그** 에서 syslog 로그 데이터를 쿼리하려면 쿼리 창에 `Syslog`를 입력합니다.

1. [Azure Monitor 로그 쿼리에서 함수 사용](../azure-monitor/logs/functions.md)에 설명된 쿼리 매개 변수를 사용하여 Syslog 메시지를 구문 분석할 수 있습니다. 그런 다음 쿼리를 새 Log Analytics 함수로 저장하고 새 데이터 형식으로 사용할 수 있습니다.

> [!NOTE]
> **동일한 컴퓨터를 사용하여 일반 Syslog 및 CEF 메시지 전달**
>
> 기존 [CEF 로그 전달자 컴퓨터](connect-cef-agent.md)를 사용하여 일반 Syslog 원본에서도 로그를 수집하고 전달할 수 있습니다. 그러나 두 가지 형식 모두로 이벤트를 Azure Sentinel로 보내면 이벤트 중복이 발생하므로 다음 단계를 사용하여 이를 방지해야 합니다.
>
>    이미 [CEF 원본에서 데이터 수집](connect-common-event-format.md)을 설정하고 위와 같이 Log Analytics 에이전트를 구성한 경우:
>
> 1. CEF 형식의 로그를 보내는 각 컴퓨터에서 Syslog 구성 파일을 편집하여 CEF 메시지를 보내는 데 사용되는 기능을 제거해야 합니다. 이렇게 하면 CEF 형식으로 전송되는 기능이 Syslog 형식으로도 전송되는 것을 방지할 수 있습니다. 이 작업을 수행하는 방법에 대한 자세한 지침은 [Linux 에이전트에서 Syslog 구성](../azure-monitor/agents/data-sources-syslog.md#configure-syslog-on-linux-agent)을 참조하세요.
>
> 1. Azure Sentinel에서 Syslog 구성과 에이전트의 동기화를 사용하지 않도록 설정하려면 해당 컴퓨터에서 다음 명령을 실행해야 합니다. 이렇게 하면 이전 단계에서 변경한 구성을 덮어쓰지 않습니다.<br>
> `sudo su omsagent -c 'python /opt/microsoft/omsconfig/Scripts/OMS_MetaConfigHelper.py --disable'`

### <a name="configure-the-syslog-connector-for-anomalous-ssh-login-detection"></a>비정상적인 SSH 로그인 검색을 위한 Syslog 커넥터 구성

> [!IMPORTANT]
> 비정상적인 SSH 로그인 검색은 현재 공개 미리 보기 상태입니다.
> 이 기능은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 권장되지 않습니다.
> 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

Azure Sentinel은 syslog 데이터에 기계 학습(ML)을 적용하여 비정상적인 SSH(Secure Shell) 로그인 작업을 식별할 수 있습니다. 다음과 같은 시나리오가 있습니다.

- 불가능한 이동 - 두 로그인 이벤트의 시간 범위 내에 도달이 불가능한 두 위치에서 두 개의 성공적인 로그인 이벤트가 발생하는 경우입니다.
- 예기치 않은 위치 – 성공적인 로그인 이벤트가 발생한 위치가 의심스러운 경우입니다. 최근에 본 적이 없는 위치를 예로 들 수 있습니다.
 
이 검색에는 Syslog 데이터 커넥터의 특정 구성이 필요합니다. 

1. 위의 [Log Analytics 에이전트 구성](#configure-the-log-analytics-agent) 2단계의 경우 **auth** 및 **authpriv** 모두 모니터링할 기능으로 선택되고 모든 심각도가 선택되어 있는지 확인합니다. 

2. syslog 정보를 수집할 수 있는 충분한 시간을 허용합니다. 그런 다음 **Azure Sentinel - 로그** 로 이동하고 다음 쿼리를 복사하여 붙여넣습니다.
    
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
이 문서에서는 Syslog 온-프레미스 어플라이언스를 Azure Sentinel에 연결하는 방법을 알아보았습니다. Azure Sentinel에 대한 자세한 내용은 다음 문서를 참조하세요.
- [데이터에 대한 가시성을 얻고 재적 위협을 확인](get-visibility.md)하는 방법을 알아봅니다.
- [Azure Sentinel을 사용하여 위협 검색](detect-threats-built-in.md)을 시작합니다.
- [통합 문서를 사용](monitor-your-data.md)하여 데이터를 모니터링합니다.

{"mode":"full","isActive":false}