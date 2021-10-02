---
title: SMS FAQ
titleSuffix: An Azure Communication Services concept document
description: SMS FAQ
author: prakulka
manager: nmurav
services: azure-communication-services
ms.author: prakulka
ms.date: 06/30/2021
ms.topic: conceptual
ms.service: azure-communication-services
ms.subservice: sms
ms.openlocfilehash: 658e13aa888e448de723a97bf9cc89c162a774b5
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/01/2021
ms.locfileid: "129356318"
---
# <a name="sms-faq"></a>SMS FAQ

[!INCLUDE [Public Preview Notice](../../includes/public-preview-include.md)]
## <a name="can-a-customer-use-azure-communication-services-for-emergency-purposes"></a>고객이 응급 목적으로 Azure Communication Services를 사용할 수 있나요?

Azure Communication Services는 미국에서 text-to-911 기능을 지원하지 않지만 FCC(연방 통신 위원회)의 규칙에 따라 이를 수행할 의무가 있을 수 있습니다.  FCC의 text-to-911 규칙이 서비스 또는 애플리케이션에 적용되는지 여부를 평가해야 합니다. 이러한 규칙이 적용되는 범위 내에서 911 문자 메시지를 요청하는 비상 콜 센터로 라우팅할 책임이 있습니다. 자체 text-to-911 전달 모델을 자유롭게 결정할 수 있지만, FCC에서 허용하는 한 가지 접근 방식은 네이티브 모바일 통신 사업자를 통해 911 텍스트를 전달하기 위해 사용자의 모바일 디바이스에서 기본 전화 걸기가 자동으로 시작됩니다.

## <a name="are-there-any-limits-on-sending-messages"></a>메시지 전송에 제한이 있나요?

SLA와 일치하는 고품질 서비스를 계속 제공할 수 있도록 Azure Communication Services는 속도 제한을 적용합니다(기본 서비스마다 다름). 한도를 초과하여 API를 호출하는 개발자는 429 HTTP 상태 코드 응답을 받게 됩니다. 회사에서 속도 제한을 초과하는 요구 사항이 있는 경우에는 phone@microsoft.com으로 이메일을 보내주세요.

SMS의 속도 제한:

|작업|범위|시간 범위| 제한(요청 #) | 분당 메시지 단위|
|---------|-----|-------------|-------------------|-------------------------|
|메시지 보내기|숫자당|60|200|200|

## <a name="how-does-azure-communication-services-handle-opt-outs-for-toll-free-numbers"></a>Azure Communication Services는 수신자 부담 번호에 대한 옵트아웃을 어떻게 처리하나요?

미국 수신자 부담 번호에 대한 옵트아웃은 미국 이동 통신 사업자에게 의무화되어 시행됩니다.
- STOP - 문자 메시지 수신자가 옵트아웃하려면 수신자 부담 번호로 'STOP'을 보내면 됩니다. 이동 통신 사업자는 STOP에 대해 다음과 같은 기본 응답을 보냅니다. "네트워크 메시지: 이 번호로부터 온 모든 문자를 차단하는 "stop"이라는 단어를 회신했습니다. 메시지를 다시 받으려면 "unstop" 문자를 다시 보내세요."
- START/UNSTOP - 수신자가 수신자 부담 번호로부터 온 문자 메시지를 다시 수신하려면 수신자 부담 번호로 ‘START’ 또는 ‘UNSTOP’을 보내면 됩니다. 이동 통신 사업자는 START/UNSTOP에 대해 다음과 같은 기본 응답을 보냅니다. “네트워크 메시지: “unstop”을 회신했으며 이 번호로부터 메시지를 다시 수신하기 시작합니다.”
- Azure Communication Services는 STOP 메시지를 탐지하여 수신자에게 보내는 모든 메시지를 차단합니다. 전달 보고서에는 "지정된 받는 사람에 대해 보낸 사람이 차단됨"이라는 상태 메시지가 포함된 전달 실패가 표시됩니다.
- STOP, UNSTOP 및 START 메시지는 사용자에게 다시 릴레이됩니다. Azure Communication Services는 이러한 옵트아웃을 모니터링하고 구현하여 통신을 옵트아웃한 수신자에게 더 이상 메시지가 전송되지 않도록 합니다.

## <a name="how-can-i-receive-messages-using-azure-communication-services"></a>Azure Communication Services를 사용하여 메시지를 받으려면 어떻게 해야 하나요?

Azure Communication Services 고객은 Azure Event Grid를 사용하여 들어오는 메시지를 받을 수 있습니다. 이 [빠른 시작](../../quickstarts/telephony-sms/handle-sms-events.md)에 따라 메시지를 받도록 이벤트 그리드를 설정합니다.

## <a name="what-is-the-sms-character-limit"></a>SMS 문자 제한은 무엇인가요?
단일 SMS에 대한 문자 제한은 160자입니다. 메시지를 보낼 때 160자를 넘는 모든 메시지는 세그먼트로 분할되어 개별적으로 전달된 다음 받는 사람의 디바이스에 의해 분리됩니다. 마찬가지로 긴 메시지를 수신하는 경우 긴 메시지의 여러 세그먼트가 자동으로 Azure Event Grid 지정된 엔드포인트에 하나의 메시지로 전달됩니다. 

## <a name="can-i-sendreceive-long-messages-2048-chars"></a>긴 메시지(2048자 미만)를 보내거나 받을 수 있나요?

Azure Communication Services는 SMS를 통해 긴 메시지의 송수신을 지원합니다. 그러나 일부 무선 통신 사업자 또는 디바이스는 긴 메시지를 받을 때 다르게 작동할 수 있습니다.

## <a name="how-are-messages-sent-to-landline-numbers-treated"></a>유선 번호로 전송된 메시지는 어떻게 처리되나요?

미국에서 Azure Communication Services는 유선 번호를 확인하지 않으며 전달을 위해 이동 통신 사업자에 보내려고 시도합니다. 유선 번호로 전송된 메시지에 대한 요금이 고객에게 청구됩니다. 

## <a name="can-i-send-messages-to-multiple-recipients"></a>여러 수신자에게 메시지를 보낼 수 있나요?

예, 여러 수신자에게 하나의 요청을 할 수 있습니다. 이 [빠른 시작](../../quickstarts/telephony-sms/send.md?pivots=programming-language-csharp)에 따라 여러 수신자에게 메시지를 보냅니다.

##  <a name="i-received-a-http-status-202-from-the-send-sms-api-but-the-sms-didnt-reach-my-phone-what-do-i-do-now"></a>SMS 보내기 API에서 HTTP 상태 202를 받았지만 SMS가 내 휴대폰에 도달하지 못했습니다. 이제 어떻게 해야 하나요?

서비스에서 반환된 202는 메시지가 전송되도록 큐에 대기되었으며 전송되지 않았음을 의미합니다. [빠른 시작](../../quickstarts/telephony-sms/handle-sms-events.md)을 사용하여 전송 보고서 이벤트를 구독하고 문제를 해결하세요. 이벤트가 구성되면 전송 보고서의 “deliveryStatus” 필드를 검사하여 전송 성공/실패를 확인합니다.
