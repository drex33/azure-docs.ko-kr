---
title: Azure Sentinel에 CEF 데이터를 연결하도록 보안 솔루션 구성 | Microsoft Docs
description: Azure Sentinel에 CEF 데이터를 연결하도록 보안 솔루션을 구성하는 방법에 대해 알아봅니다.
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
ms.date: 12/30/2019
ms.author: yelevin
ms.openlocfilehash: fe60a36d93a77ee75df578e0f6fc6665da26681e
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122536492"
---
# <a name="step-2-configure-your-security-solution-to-send-cef-messages"></a>2단계: CEF 메시지를 보내도록 보안 솔루션 구성

이 단계에서는 보안 솔루션 자체에서 필요한 구성 변경을 수행하여 CEF 에이전트로 로그를 보냅니다.

## <a name="configure-a-solution-with-a-connector"></a>커넥터를 사용하여 솔루션 구성

보안 솔루션에 기존 커넥터가 이미 있는 경우, 다음과 같이 커넥터 관련 명령을 사용합니다.

- [AI Vectra Detect](connect-ai-vectra-detect.md)
- [Akamai Security Events](connect-akamai-security-events.md)
- [Aruba ClearPass](connect-aruba-clearpass.md)
- [Broadcom Symantec DLP](connect-broadcom-symantec-dlp.md)
- [Check Point](connect-checkpoint.md)
- [Cisco ASA](connect-cisco.md)
- [Citrix WAF](connect-citrix-waf.md)
- [CyberArk Enterprise Password Vault](connect-cyberark.md)
- [ExtraHop Reveal(x)](connect-extrahop.md)
- [F5 ASM](connect-f5.md)
- [Forcepoint 제품](connect-forcepoint-casb-ngfw.md)
- [Fortinet](connect-fortinet.md)
- [Illusive Networks AMS](connect-illusive-attack-management-system.md)
- [Imperva WAF Gateway](connect-imperva-waf-gateway.md)
- [One Identity Safeguard](connect-one-identity.md)
- [Palo Alto Networks](connect-paloalto.md)
- [Thycotic Secret Server](connect-thycotic-secret-server.md)
- [Trend Micro Deep Security](connect-trend-micro.md)
- [Trend Micro TippingPoint](connect-trend-micro-tippingpoint.md)
- [WireX 네트워크 포렌식 플랫폼](connect-wirex-systems.md)
- [Zscaler](connect-zscaler.md)
## <a name="configure-any-other-solution"></a>다른 솔루션 구성

특정 보안 솔루션에 대한 커넥터가 없는 경우 CEF 에이전트로 로그를 전달하는 다음 일반 지침을 사용합니다.

1. CEF 메시지를 보내도록 솔루션을 구성하는 방법에 대한 단계의 특정 구성 문서로 이동합니다. 솔루션이 나열되지 않은 경우 어플라이언스에서 Log Analytics 에이전트에 따라 필요한 로그를 필요한 형식으로 Azure Sentinel Syslog 에이전트로 보내도록 이러한 값을 설정해야 합니다. 이러한 매개 변수는 Azure Sentinel 에이전트의 Syslog 디먼에서도 수정할 수 있는 경우 어플라이언스에서 수정할 수 있습니다.
    - 프로토콜 = TCP
    - 포트 = 514
    - 형식 = CEF
    - IP 주소-이 목적을 위한 전용으로 사용하는 가상 컴퓨터의 IP 주소에 CEF 메시지를 전송해야 합니다.

   이 솔루션은 Syslog RFC 3164 또는 RFC 5424을 지원합니다.

1. Log Analytics에서 CEF 이벤트를 검색하려면 쿼리 창에 `CommonSecurityLog`을 입력합니다.

1. 3단계를 계속 하십시오.: [ 연결 유효성 확인](connect-cef-verify.md).

> [!NOTE]
> **TimeGenerated 필드의 원본 변경**
>
> - 기본적으로 Log Analytics 에이전트는 스키마의 *TimeGenerated* 필드를 에이전트가 Syslog 디먼에서 이벤트를 받은 시간으로 채웁니다. 따라서 원본 시스템에서 이벤트가 생성된 시간은 Azure Sentinel에 기록되지 않습니다.
>
> - 그러나 `TimeGenerated.py` 스크립트를 다운로드하고 실행하면 다음 명령을 실행할 수 있습니다. 이 스크립트는 *TimeGenerated* 필드를 에이전트에서 받은 시간 대신 원본 시스템의 이벤트 원래 시간으로 채우도록 Log Analytics 에이전트를 구성합니다.
>
>    ```bash
>    wget -O TimeGenerated.py https://raw.githubusercontent.com/Azure/Azure-Sentinel/master/DataConnectors/CEF/TimeGenerated.py && python TimeGenerated.py {ws_id}
>    ```

## <a name="next-steps"></a>다음 단계

이 문서에서는 CEF 어플라이언스를 Azure Sentinel에 연결하는 방법을 알아보았습니다. Azure Sentinel에 대한 자세한 내용은 다음 문서를 참조하세요.

- [CEF 및 CommonSecurityLog 필드 매핑](cef-name-mapping.md)에 대해 알아봅니다.
- [데이터에 대한 가시성을 얻고 재적 위협을 확인](get-visibility.md)하는 방법을 알아봅니다.
- [Azure Sentinel을 사용하여 위협 검색](./detect-threats-built-in.md)을 시작합니다.