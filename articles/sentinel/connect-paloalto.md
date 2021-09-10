---
title: Palo Alto Networks 데이터를 Azure Sentinel에 연결 | Microsoft Docs
description: Palo Alto Networks 데이터 커넥터를 사용하여 Palo Alto Networks 로그를 Azure Sentinel에 쉽게 연결하는 방법을 알아봅니다.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: a4b21d67-1a72-40ec-bfce-d79a8707b4e1
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/30/2019
ms.author: yelevin
ms.openlocfilehash: b0ed9a2e81585df053a94e242456a2b5e9ff60c4
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/23/2021
ms.locfileid: "122693782"
---
# <a name="connect-palo-alto-networks-to-azure-sentinel"></a>Palo Alto Networks를 Azure Sentinel에 연결



이 문서에서는 Palo Alto Networks 어플라이언스를 Azure Sentinel에 연결하는 방법을 설명합니다. Palo Alto Networks 데이터 커넥터를 사용하면 Palo Alto Networks 로그를 Azure Sentinel에 쉽게 연결하여 대시보드를 보고, 사용자 지정 경고를 만들고, 조사를 개선할 수 있습니다. Azure Sentinel에서 Palo Alto Networks를 사용하면 조직의 인터넷 사용에 대한 인사이트를 제공하고 보안 작업 기능을 강화할 수 있습니다. 

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

## <a name="forward-palo-alto-networks-logs-to-the-syslog-agent"></a>Syslog 에이전트에 Palo Alto Networks 로그 전달

Syslog 에이전트를 통해 CEF 형식의 Syslog 메시지를 Azure 작업 영역으로 전달하도록 Palo Alto Networks를 구성합니다.
1.  [CEF(Common Event Format) 구성 가이드](https://docs.paloaltonetworks.com/resources/cef)로 이동하여 해당 어플라이언스 유형의 pdf를 다운로드합니다. 가이드의 모든 지침에 따라 Palo Alto Networks 어플라이언스에서 CEF 이벤트를 수집하도록 설정합니다. 

1.  [Syslog 모니터링 구성](https://docs.paloaltonetworks.com/pan-os/8-1/pan-os-admin/monitoring/use-syslog-for-monitoring/configure-syslog-monitoring)으로 이동한 후 2단계 및 3단계를 수행하여 Palo Alto Networks 어플라이언스에서 Azure Sentinel로 CEF 이벤트를 전달하도록 구성합니다.

    1. **Syslog 서버 형식** 을 **BSD** 로 설정해야 합니다.

       > [!NOTE]
       > PDF의 복사/붙여넣기 작업은 텍스트를 변경하고 임의의 문자를 삽입할 수도 있습니다. 이를 방지하려면 이 예제에 나와 있는 것처럼 텍스트를 편집기에 복사한 후 붙여넣기 전에 로그 형식을 손상할 수 있는 문자를 모두 제거하세요.
 
        ![CEF 텍스트 복사 문제](./media/connect-cef/paloalto-text-prob1.png)

1. Palo Alto Networks 이벤트에 대한 Log Analytics에서 관련 스키마를 사용하려면 **CommonSecurityLog** 를 검색합니다.

1. [CEF 연결 유효성 검사](troubleshooting-cef-syslog.md#validate-cef-connectivity)로 계속 진행합니다.




## <a name="next-steps"></a>다음 단계
이 문서에서는 Palo Alto Networks 어플라이언스를 Azure Sentinel에 연결하는 방법을 알아보았습니다. Azure Sentinel에 대한 자세한 내용은 다음 문서를 참조하세요.
- [데이터에 대한 가시성을 얻고 재적 위협을 확인](get-visibility.md)하는 방법을 알아봅니다.
- [Azure Sentinel을 사용하여 위협 검색](detect-threats-built-in.md)을 시작합니다.
- [통합 문서를 사용](monitor-your-data.md)하여 데이터를 모니터링합니다.