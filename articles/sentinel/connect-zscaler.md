---
title: Azure Sentinel에 Zscaler 데이터 연결| Microsoft Docs
description: Azure Sentinel에 Zscaler 데이터를 연결하는 방법을 알아봅니다.
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
ms.openlocfilehash: a08004abc62bfe0e01c42ea7d3e5d6eeecb89170
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/23/2021
ms.locfileid: "122695031"
---
# <a name="connect-zscaler-internet-access-to-azure-sentinel"></a>Azure Sentinel에 Zscaler 인터넷 액세스 연결

이 문서에서는 Azure Sentinel에 Zscaler 인터넷 액세스를 연결하는 방법을 설명합니다. Zscaler 데이터 커넥터를 사용하면 ZIA(Zscaler 인터넷 액세스) 로그를 Azure Sentinel에 쉽게 연결하여 대시보드를 보고, 사용자 지정 경고를 만들고, 조사 기능을 개선할 수 있습니다. Azure Sentinel에서 Zscaler를 사용하면 조직의 인터넷 사용량에 대한 인사이트를 제공하고 보안 작업 기능을 강화할 수 있습니다. 

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

## <a name="configure-your-zscaler-to-send-cef-messages"></a>CEF 메시지를 보내도록 Zscaler 구성

1. Zscaler 어플라이언스에서 Log Analytics 에이전트에 따라 필요한 로그를 필요한 형식으로 Azure Sentinel Syslog 에이전트로 보내도록 이러한 값을 설정해야 합니다. 이러한 매개 변수는 Azure Sentinel 에이전트의 Syslog 데몬에서도 수정한다면 어플라이언스에서 수정할 수 있습니다.
    - 프로토콜 = TCP
    - 포트 = 514
    - 형식 = CEF
    - IP 주소 - CEF 메시지를 이 용도로만 사용하는 가상 컴퓨터의 IP 주소로 전송해야 합니다.
 자세한 내용은 [Zscaler 및 Azure Sentinel 배포 가이드](https://aka.ms/ZscalerCEFInstructions)를 참조하세요.
 
   > [!NOTE]
   > 이 솔루션은 Syslog RFC 3164 또는 RFC 5424를 지원합니다.


1. CEF 이벤트의 Log Analytics에서 관련 스키마를 사용하려면 `CommonSecurityLog`를 검색합니다.
1. [CEF 연결 유효성 검사](troubleshooting-cef-syslog.md#validate-cef-connectivity)로 계속 진행합니다.


## <a name="next-steps"></a>다음 단계
이 문서에서는 Azure Sentinel에 Zscaler 인터넷 액세스를 연결하는 방법을 알아보았습니다. Azure Sentinel에 대한 자세한 내용은 다음 문서를 참조하세요.
- [데이터에 대한 가시성을 얻고 재적 위협을 확인](get-visibility.md)하는 방법을 알아봅니다.
- [Azure Sentinel을 사용하여 위협 검색](./detect-threats-built-in.md)을 시작합니다.
- [통합 문서를 사용](monitor-your-data.md)하여 데이터를 모니터링합니다.