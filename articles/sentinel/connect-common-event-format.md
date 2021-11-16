---
title: 장치 또는 어플라이언스에서 Microsoft 센티널로 CEF 형식의 로그 가져오기 | Microsoft Docs
description: Linux 기반 로그 전달자에 설치 된 Log Analytics 에이전트를 사용 하 여 Syslog를 통해 CEF (Common Event Format)로 전송 된 로그를 Microsoft 센티널 작업 영역으로 수집 합니다.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: microsoft-sentinel
ms.subservice: microsoft-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/09/2021
ms.author: yelevin
ms.custom: ignite-fall-2021
ms.openlocfilehash: 734ed8aaa1ad3557981c76f380227e7574861086
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132521893"
---
# <a name="get-cef-formatted-logs-from-your-device-or-appliance-into-microsoft-sentinel"></a>장치 또는 어플라이언스에서 Microsoft 센티널로 CEF 형식의 로그 가져오기

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

많은 네트워킹 및 보안 디바이스와 어플라이언스는 Syslog 프로토콜을 통해 CEF(Common Event Format)라는 특수 형식으로 시스템 로그를 보냅니다. 이 형식에는 표준 Syslog 형식보다 더 많은 정보가 포함되며 구문 분석된 키-값 배열로 정보를 표시합니다. Log Analytics 에이전트는 CEF 로그를 수락 하 고 microsoft 센티널 작업 영역으로 전달 하기 전에 Microsoft 센티널에서 사용 하기 위해 특히 형식을 지정 합니다.

이 문서에서는 CEF 형식 로그를 사용하여 데이터 원본을 연결하는 프로세스를 설명합니다. 이 방법을 사용 하는 데이터 커넥터에 대 한 자세한 내용은 [Microsoft 센티널 데이터 커넥터 참조](data-connectors-reference.md)를 참조 하세요.

이 연결을 만드는 주요 2단계는 아래에서 자세히 설명합니다.

- Linux 컴퓨터 또는 VM을 전용 로그 전달자로 지정 하 고, 여기에 Log Analytics 에이전트를 설치 하 고, Microsoft 센티널 작업 영역으로 로그를 전달 하도록 에이전트를 구성 합니다. 에이전트의 설치 및 구성은 배포 스크립트에 의해 처리됩니다.

- CEF 형식의 로그를 Syslog 서버로 보내도록 디바이스를 구성합니다.

> [!NOTE]
> Microsoft 센티널을 실행 하는 작업 영역의 지리적 위치에 데이터가 저장 됩니다.

## <a name="supported-architectures"></a>지원되는 아키텍처

다음 다이어그램에서는 Azure에 있는 Linux VM의 설정을 설명합니다.

 ![Azure의 CEF](./media/connect-cef/cef-syslog-azure.png)

다른 클라우드 또는 온-프레미스 머신에서 VM을 사용하는 경우에는 다음 설정을 사용합니다.

 ![온-프레미스의 CEF](./media/connect-cef/cef-syslog-onprem.png)

## <a name="prerequisites"></a>필수 조건

Microsoft 센티널 작업 영역은 CEF 데이터를 Log Analytics으로 수집 하기 위해 필요 합니다.

- 이 작업 영역에 대한 읽기 및 쓰기 권한이 있어야 합니다.

- 작업 영역 공유 키에 대한 읽기 권한이 있어야 합니다. [작업 영역 키에 대해 자세히 알아보세요](../azure-monitor/agents/agent-windows.md).

## <a name="designate-a-log-forwarder-and-install-the-log-analytics-agent"></a>로그 전달자를 지정 및 Log Analytics 에이전트 설치

이 섹션에서는 장치에서 Microsoft 센티널 작업 영역으로 로그를 전달 하는 Linux 컴퓨터를 지정 하 고 구성 하는 방법을 설명 합니다.

Linux 컴퓨터는 온-프레미스 환경, Azure VM 또는 다른 클라우드의 VM에서 물리적 컴퓨터 또는 가상 머신일 수 있습니다.

**CEF(Common Event Format) 데이터 커넥터 페이지** 에 나오는 링크를 사용하여 지정된 머신에서 스크립트를 실행하고 다음 작업을 수행합니다.

- **Linux용 Log Analytics 에이전트**(OMS 에이전트라고도 함)를 설치하고 다음과 같은 목적으로 구성합니다.
    - TCP 포트 25226의 기본 제공 Linux Syslog 디먼에서 CEF 메시지 수신 대기
    - Microsoft 센티널 작업 영역에 TLS를 통해 메시지를 안전 하 게 전송 (구문 분석 및 보강)

- **다음 목적을 위해 기본 제공 Linux Syslog 디먼**(rsyslog.d/syslog-ng)을 구성합니다.
    - TCP 포트 514의 보안 솔루션에서 Syslog 메시지 수신 대기
    - TCP 포트 25226을 사용하여 localhost의 Log Analytics 에이전트에 대한 CEF로 식별되는 메시지만 전달

자세한 내용은 [Microsoft 센티널에 Syslog 및 CEF 로그를 수집 하는 로그 전달자 배포](connect-log-forwarder.md)를 참조 하세요.

### <a name="security-considerations"></a>보안 고려 사항

머신 보안은 조직의 보안 정책에 따라 구성해야 합니다. 예를 들어 네트워크가 회사 네트워크 보안 정책에 맞게 구성되고 디먼의 포트 및 프로토콜이 사용자의 요구 사항에 맞게 변경될 수 있습니다.

자세한 내용은 [Azure의 보안 VM](../virtual-machines/security-policy.md)과 [네트워크 보안 모범 사례](../security/fundamentals/network-best-practices.md)를 참조하세요.

로그 전달자가 클라우드에 있을 때처럼 디바이스가 TLS를 통해 Syslog 및 CEF 로그를 보내는 경우에는 TLS에서 통신하도록 Syslog 디먼(rsyslog 또는 syslog-ng)을 구성해야 합니다. 

자세한 내용은 다음을 참조하세요.

- [TLS를 통해 Syslog 트래픽 암호화 - rsyslog](https://www.rsyslog.com/doc/v8-stable/tutorials/tls_cert_summary.html)
- [TLS를 통해 로그 메시지 암호화 - syslog-ng](https://support.oneidentity.com/technical-documents/syslog-ng-open-source-edition/3.22/administration-guide/60#TOPIC-1209298)

## <a name="configure-your-device"></a>디바이스 구성

CEF 형식의 로그를 SIEM 또는 로그 서버에 전송하는 디바이스 공급업체의 구성 지침을 찾고 지침에 따릅니다. 

제품이 데이터 커넥터 갤러리에 표시 되는 경우 [Microsoft 센티널 데이터 커넥터 참조](data-connectors-reference.md) 에서 도움이 될 수 있습니다. 여기서 구성 지침은 아래 목록의 설정을 포함 해야 합니다.

   - 프로토콜 = TCP
   - 포트 = 514
   - 형식 = CEF
   - IP 주소-이 목적을 위한 전용으로 사용하는 가상 컴퓨터의 IP 주소에 CEF 메시지를 전송해야 합니다.

이 솔루션은 Syslog RFC 3164 또는 RFC 5424을 지원합니다.

> [!TIP]
> 로그 전달자의 Syslog 디먼에서도 동일한 변경을 수행한다면, 디바이스에서 필요에 따라 다른 프로토콜이나 포트 번호를 정의합니다.
>

## <a name="find-your-data"></a>데이터 찾기

데이터가 연결된 후 Log Analytics에 표시되기까지 최대 20분이 걸릴 수 있습니다.

Log Analytics에서 CEF 이벤트를 검색하려면 쿼리 창에서 `CommonSecurityLog` 테이블을 쿼리합니다.

데이터 커넥터 갤러리에 나오는 일부 제품은 추가 파서가 있어야 최상의 결과를 얻을 수 있습니다. 이러한 파서는 Kusto 함수를 사용하여 구현됩니다. 자세한 내용은 [Microsoft 센티널 데이터 커넥터 참조](data-connectors-reference.md) 페이지에서 해당 제품에 대 한 섹션을 참조 하세요.

이러한 제품에 대한 CEF 이벤트를 찾으려면 ‘CommonSecurityLog’ 대신 Kusto 함수의 이름을 쿼리 제목으로 입력하세요.

Microsoft 센티널 포털에서 제품의 데이터 커넥터 페이지의 **다음 단계** 탭에서 특히 제품에 대해 수행 되는 유용한 샘플 쿼리, 통합 문서 및 분석 규칙 템플릿을 찾을 수 있습니다.

데이터가 표시되지 않는 경우 [CEF 문제 해결](./troubleshooting-cef-syslog.md) 페이지에서 지침을 확인하세요.

### <a name="changing-the-source-of-the-timegenerated-field"></a>TimeGenerated 필드의 원본 변경

기본적으로 Log Analytics 에이전트는 스키마의 *TimeGenerated* 필드를 에이전트가 Syslog 디먼에서 이벤트를 받은 시간으로 채웁니다. 따라서 원본 시스템에서 이벤트가 생성 된 시간은 Microsoft 센티널에 기록 되지 않습니다.

그러나 `TimeGenerated.py` 스크립트를 다운로드하고 실행하면 다음 명령을 실행할 수 있습니다. 이 스크립트는 *TimeGenerated* 필드를 에이전트에서 받은 시간 대신 원본 시스템의 이벤트 원래 시간으로 채우도록 Log Analytics 에이전트를 구성합니다.

```bash
wget -O TimeGenerated.py https://raw.githubusercontent.com/Azure/Azure-Sentinel/master/DataConnectors/CEF/TimeGenerated.py && python TimeGenerated.py {ws_id}
```

## <a name="next-steps"></a>다음 단계

이 문서에서는 Microsoft 센티널이 장치 및 어플라이언스에서 CEF 로그를 수집 하는 방법을 배웠습니다. Microsoft 센티널에 제품을 연결 하는 방법에 대해 자세히 알아보려면 다음 문서를 참조 하세요.

- [Syslog/CEF 전달자 배포](connect-log-forwarder.md)
- [Microsoft 센티널 데이터 커넥터 참조](data-connectors-reference.md)
- [로그 전달자 연결 문제 해결](troubleshooting-cef-syslog.md#validate-cef-connectivity)

Microsoft 센티널에서 수집한 데이터로 수행할 작업에 대해 자세히 알아보려면 다음 문서를 참조 하세요.

- [CEF 및 CommonSecurityLog 필드 매핑](cef-name-mapping.md)에 대해 알아봅니다.
- [데이터 및 잠재적 위협에 대한 가시성을 확보](get-visibility.md)하는 방법을 알아봅니다.
- [Microsoft 센티널을 사용 하 여 위협 검색을](./detect-threats-built-in.md)시작 하세요.
