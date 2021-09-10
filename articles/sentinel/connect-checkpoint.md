---
title: Check Point 데이터를 Azure Sentinel에 연결 | Microsoft Docs
description: Syslog 에이전트를 통해 CEF 형식의 Syslog 메시지를 Azure Sentinel 작업 영역으로 전달하도록 Check Point 어플라이언스를 구성합니다.
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
ms.openlocfilehash: 72aa2efe611a500a8a7c4a56725bb5bd02b247da
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/23/2021
ms.locfileid: "122694084"
---
# <a name="connect-check-point-to-azure-sentinel"></a>Check Point를 Azure Sentinel에 연결



이 문서에서는 Check Point 어플라이언스를 Azure Sentinel에 연결하는 방법을 설명합니다. Check Point 데이터 커넥터를 사용하면 Check Point 로그를 Azure Sentinel에 쉽게 연결하여 대시보드를 보고, 사용자 지정 경고를 만들고, 조사를 개선할 수 있습니다. Azure Sentinel에서 Check Point를 사용하면 조직의 인터넷 사용에 대한 인사이트를 제공하고 보안 작업 기능을 강화할 수 있습니다. 

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]


## <a name="forward-check-point-logs-to-the-syslog-agent"></a>Syslog 에이전트에 Check Point 로그 전달

Syslog 에이전트를 통해 CEF 형식의 Syslog 메시지를 Azure 작업 영역으로 전달하도록 Check Point 어플라이언스를 구성합니다.

1. [Check Point 로그 내보내기](https://aka.ms/asi-syslog-checkpoint-forwarding)로 이동합니다.
1. 아래 지침에 따라 **기본 배포** 까지 아래로 스크롤한 후 연결을 설정합니다.
   - **Syslog 포트** 를 **514** 또는 에이전트에서 지정한 포트로 설정합니다.
     - CLI에서 **이름** 및 **대상 서버 IP 주소** 를 Syslog 에이전트 이름 및 IP 주소로 바꿉니다.
     - 형식을 **CEF** 로 설정합니다.
1. R77.30 또는 R80.10 버전을 사용하는 경우 **설치** 까지 위로 스크롤한 후 지침에 따라 해당 버전의 Log Exporter를 설치합니다.
1. [CEF 연결 유효성 검사](troubleshooting-cef-syslog.md#validate-cef-connectivity)로 계속 진행합니다.
 

## <a name="next-steps"></a>다음 단계
이 문서에서는 Check Point 어플라이언스를 Azure Sentinel에 연결하는 방법을 알아보았습니다. Azure Sentinel에 대한 자세한 내용은 다음 문서를 참조하세요.
- [연결 유효성을 검사](troubleshooting-cef-syslog.md#validate-cef-connectivity)합니다.
- [Azure Sentinel을 사용하여 위협 검색](detect-threats-built-in.md)을 시작합니다.
- [통합 문서를 사용](monitor-your-data.md)하여 데이터를 모니터링합니다.