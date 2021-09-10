---
title: Azure Sentinel에 Fortinet 데이터 연결| Microsoft Docs
description: Fortinet 어플라이언스를 Azure Sentinel에 연결하여 대시보드를 보고, 사용자 지정 경고를 만들고, 조사를 개선합니다.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: add92907-0d7c-42b8-a773-f570f2d705ff
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/30/2019
ms.author: yelevin
ms.openlocfilehash: 9fb3de09195c8dd88110a7723cbe7b4d89e89309
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/23/2021
ms.locfileid: "122695121"
---
# <a name="connect-fortinet-to-azure-sentinel"></a>Azure Sentinel에 Fortinet 연결



이 문서는 Fortinet 어플라이언스를 Azure Sentinel에 연결하는 방법을 설명합니다. Fortinet 데이터 커넥터를 사용하면 Fortinet 로그를 Azure Sentinel에 쉽게 연결하여 대시보드를 보고, 사용자 지정 경고를 만들고, 조사를 개선할 수 있습니다. Azure Sentinel에서 Fortinet을 사용하면 조직의 인터넷 사용에 대한 인사이트를 제공하고 보안 작업 기능을 강화할 수 있습니다. 

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

 
## <a name="forward-fortinet-logs-to-the-syslog-agent"></a>Syslog 에이전트에 Fortinet 로그 전달

Syslog 에이전트를 통해 CEF 형식의 Syslog 메시지를 Azure 작업 영역으로 전달하도록 Fortinet을 구성합니다.

1. Fortinet 어플라이언스에서 CLI를 열고 다음 명령을 실행합니다.

    ```console
    config log syslogd setting
    set status enable
    set format cef
    set port 514
    set server <ip_address_of_Receiver>
    end
    ```

    - 서버 **IP 주소** 를 에이전트의 IP 주소로 바꿉니다.
    - **syslog 포트** 를 **514** 또는 에이전트에 설정된 포트로 설정합니다.
    - 초기 FortiOS 버전에서 CEF 형식을 사용하도록 설정하려면 명령 집합 **csv disable** 을 실행해야 할 수 있습니다.
 
   > [!NOTE] 
   > 자세한 내용은 [Fortinet 문서 라이브러리](https://aka.ms/asi-syslog-fortinet-fortinetdocumentlibrary)로 이동합니다. 버전을 선택하고 **Handbook(핸드북)** 및 **Log Message Reference(로그 메시지 참조)** 를 사용합니다.

1. Azure Monitor Log Analytics에서 Fortinet 이벤트에 대한 관련 스키마를 사용하려면 `CommonSecurityLog`를 검색합니다.

1. [CEF 연결 유효성 검사](troubleshooting-cef-syslog.md#validate-cef-connectivity)로 계속 진행합니다.


## <a name="next-steps"></a>다음 단계
이 문서에서는 Fortinet 어플라이언스를 Azure Sentinel에 연결하는 방법을 알아보았습니다. Azure Sentinel에 대한 자세한 내용은 다음 문서를 참조하세요.
- [데이터에 대한 가시성을 얻고 재적 위협을 확인](get-visibility.md)하는 방법을 알아봅니다.
- [Azure Sentinel을 사용하여 위협 검색](detect-threats-built-in.md)을 시작합니다.
- [통합 문서를 사용](monitor-your-data.md)하여 데이터를 모니터링합니다.