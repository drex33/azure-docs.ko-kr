---
title: Azure Communication Services에 대한 SMS SDK 개요
titleSuffix: An Azure Communication Services concept document
description: SMS SDK 및 해당 제품에 대한 개요를 제공합니다.
author: probableprime
manager: chpalm
services: azure-communication-services
ms.author: prakulka
ms.date: 06/30/2021
ms.topic: conceptual
ms.service: azure-communication-services
ms.subservice: sms
ms.openlocfilehash: 3be8101834eaa9f023c12b61d37c3b572d7a6ac6
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131434126"
---
# <a name="sms-sdk-overview"></a>SMS SDK 개요

[!INCLUDE [Regional Availability Notice](../../includes/regional-availability-include.md)]

Azure Communication Services SMS SDK를 사용하여 SMS 메시징을 애플리케이션에 추가할 수 있습니다.

## <a name="sms-sdk-capabilities"></a>SMS SDK 기능

다음 목록에서는 현재 SDK에서 사용할 수 있는 기능 세트를 보여 줍니다.

| 기능 그룹 | 기능                                                                            | JS  | Java | .NET | Python |
| ----------------- | ------------------------------------------------------------------------------------- | --- | ---- | ---- | ------ |
| 핵심 기능 | SMS 메시지 보내기 및 받기                                                         | ✔️   | ✔️    | ✔️    | ✔️      |
|                   | 보낸 메시지에 대한 전달 보고서 사용                                             | ✔️   | ✔️    | ✔️    | ✔️      |
|                   | 모든 문자 집합(언어/유니코드 지원)                                         | ✔️   | ✔️    | ✔️    | ✔️      |
|                   | 긴 메시지 지원(최대 2048바이트)                                          | ✔️   | ✔️    | ✔️    | ✔️      |
|                   | 긴 메시지 자동 연결                                                   | ✔️   | ✔️    | ✔️    | ✔️      |
|                   | 한 번에 여러 받는 사람에게 메시지 보내기                                        | ✔️   | ✔️    | ✔️    | ✔️      |
|                   | 멱등성 지원                                                               | ✔️   | ✔️    | ✔️    | ✔️      |
|                   | 메시지에 대한 사용자 지정 태그                                                             | ✔️   | ✔️    | ✔️    | ✔️      |
| 이벤트            | Event Grid를 사용하여 인바운드 메시지 및 전달 보고서를 수신하도록 웹후크 구성 | ✔️   | ✔️    | ✔️    | ✔️      |
| 전화 번호      | 수신자 부담 번호, 짧은 코드(*출시 예정*)                                        | ✔️   | ✔️    | ✔️    | ✔️      |
| PSTN 호출      | SMS 사용 가능 수신자 부담 번호에 PSTN 통화 기능 추가                    | ✔️   | ✔️    | ✔️    | ✔️      |

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [sms 전송 시작](../../quickstarts/telephony-sms/send.md)

다음 문서는 사용자에게 유용할 수 있습니다.

- 일반적인 [SMS 개념](../telephony-sms/concepts.md) 숙지
- SMS 지원 [전화 번호](../../quickstarts/telephony-sms/get-phone-number.md) 가져오기
- [Azure Communication Services의 전화 번호 유형](../telephony-sms/plan-solution.md)
