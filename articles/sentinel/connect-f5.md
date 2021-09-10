---
title: Azure Sentinel에 F5 ASM 데이터 연결| Microsoft Docs
description: F5 ASM 데이터 커넥터를 사용하여 Azure Sentinel에 F5 ASM 로그를 가져오는 방법을 알아봅니다. 통합 문서에서 F5 ASM 데이터를 보고, 경고를 만들고, 조사를 개선합니다.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: 0001cad6-699c-4ca9-b66c-80c194e439a5
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/20/2020
ms.author: yelevin
ms.openlocfilehash: db79aaa5f949d60d79e2500d0d282c2f5909bf32
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/23/2021
ms.locfileid: "122694028"
---
# <a name="connect-f5-asm-to-azure-sentinel"></a>Azure Sentinel에 F5 ASM 연결

이 문서에서는 F5 ASM 데이터 커넥터를 사용하여 F5 ASM 로그를 Azure Sentinel로 쉽게 가져오는 방법을 설명합니다. 이를 통해 통합 문서에서 F5 ASM 데이터를 보고 이를 사용하여 사용자 지정 경고를 만들고 이를 통합하여 조사를 개선할 수 있습니다. Azure Sentinel에 F5 ASM 데이터가 있으면 조직의 웹 애플리케이션 보안에 대한 자세한 인사이트를 제공하고 보안 운영 기능을 향상시킬 수 있습니다. 

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

## <a name="configure-your-f5-asm-to-send-cef-messages"></a>CEF 메시지를 보내도록 F5 ASM 구성

1. [F5 애플리케이션 보안 이벤트 로깅 구성](https://techdocs.f5.com/kb/en-us/products/big-ip_asm/manuals/product/asm-implementations-11-5-0/12.html)의 지침에 따라 원격 로깅을 설정합니다. 다음 지침을 사용하세요.
   - **원격 스토리지 형식** 을 **CEF** 로 설정합니다.
   - **프로토콜** 을 **TCP** 로 설정합니다.
   - **IP 주소** 를 Syslog 서버 IP 주소로 설정합니다.
   - **포트 번호** 를 **514** 또는 에이전트가 사용하도록 설정한 포트로 설정합니다.
   - **최대 쿼리 문자열 크기** 를 에이전트에서 설정한 크기로 설정할 수 있습니다.

1. CEF 이벤트의 Log Analytics에서 관련 스키마를 사용하려면 `CommonSecurityLog`를 검색합니다.

1. [CEF 연결 유효성 검사](troubleshooting-cef-syslog.md#validate-cef-connectivity)로 계속 진행합니다.


## <a name="next-steps"></a>다음 단계
이 문서에서는 F5 ASM을 Azure Sentinel에 연결하는 방법을 알아보았습니다. Azure Sentinel에 대한 자세한 내용은 다음 문서를 참조하세요.
- [데이터에 대한 가시성을 얻고 재적 위협을 확인](get-visibility.md)하는 방법을 알아봅니다.
- [Azure Sentinel을 사용하여 위협 검색](./detect-threats-built-in.md)을 시작합니다.
- [통합 문서를 사용](monitor-your-data.md)하여 데이터를 모니터링합니다.