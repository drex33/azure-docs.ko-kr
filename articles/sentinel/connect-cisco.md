---
title: Azure Sentinel에 Cisco 데이터 연결| Microsoft Docs
description: Cisco ASA 어플라이언스를 Azure Sentinel에 연결하여 대시보드를 보고, 사용자 지정 경고를 만들고, 조사를 개선하는 방법을 알아봅니다.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: 62029b5c-29d3-4336-8a22-a9db8214eb7e
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/30/2019
ms.author: yelevin
ms.openlocfilehash: db10c9814c04f6e29a017b9a473cd117a0cbf2ae
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/23/2021
ms.locfileid: "122695202"
---
# <a name="connect-cisco-asa-to-azure-sentinel"></a>Azure Sentinel로 Cisco ASA 연결



이 문서는 Cisco ASA 어플라이언스를 Azure Sentinel에 연결하는 방법을 설명합니다. Cisco ASA 데이터 커넥터를 사용하면 Cisco ASA 로그를 Azure Sentinel에 쉽게 연결하여 대시보드를 보고, 사용자 지정 경고를 만들고, 조사를 개선할 수 있습니다. Azure Sentinel에서 Cisco ASA를 사용하면 조직의 인터넷 사용에 대한 인사이트를 제공하고, 보안 작업 기능을 강화할 수 있습니다. 

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]


## <a name="forward-cisco-asa-logs-to-the-syslog-agent"></a>Syslog 에이전트로 Cisco ASA 로그 전달

Cisco ASA는 CEF를 지원하지 않으므로 로그가 Syslog로 전송되고, Azure Sentinel 에이전트는 CEF 로그처럼 구문 분석하는 방법을 알고 있습니다. Syslog 에이전트를 통해 Syslog 메시지를 Azure 작업 영역에 전달하도록 Cisco ASA를 구성합니다.

1. [외부 Syslog 서버에 Syslog 메시지 보내기](https://aka.ms/asi-syslog-cisco-forwarding)로 이동하고, 지침에 따라 연결을 설정합니다. 메시지가 표시되면 해당 매개 변수를 사용합니다.
    - **포트** 를 514 또는 에이전트에서 지정한 포트로 설정합니다.
    - **syslog_ip** 를 에이전트의 IP 주소로 설정합니다.

1. Cisco 이벤트의 Log Analytics에서 관련 스키마를 사용하려면 `CommonSecurityLog`를 검색합니다.

1. [CEF 연결 유효성 검사](troubleshooting-cef-syslog.md#validate-cef-connectivity)로 계속 진행합니다.




## <a name="next-steps"></a>다음 단계
이 문서에서는 Cisco ASA 어플라이언스를 Azure Sentinel에 연결하는 방법을 알아보았습니다. Azure Sentinel에 대한 자세한 내용은 다음 문서를 참조하세요.
- [데이터에 대한 가시성을 얻고 재적 위협을 확인](get-visibility.md)하는 방법을 알아봅니다.
- [Azure Sentinel을 사용하여 위협 검색](detect-threats-built-in.md)을 시작합니다.
- [통합 문서를 사용](monitor-your-data.md)하여 데이터를 모니터링합니다.