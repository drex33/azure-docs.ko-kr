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
ms.openlocfilehash: 286103b7649945f02a4ac2aa4fc7a09d4ae68e12
ms.sourcegitcommit: 66b6e640e2a294a7fbbdb3309b4829df526d863d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/01/2021
ms.locfileid: "133386904"
---
# <a name="sms-faq"></a>SMS FAQ
이 문서에서는 SMS 서비스에 대한 일반적인 질문에 답변합니다. 

## <a name="can-a-customer-use-azure-communication-services-for-emergency-purposes"></a>고객이 응급 목적으로 Azure Communication Services를 사용할 수 있나요?

Azure Communication Services는 미국에서 text-to-911 기능을 지원하지 않지만 FCC(연방 통신 위원회)의 규칙에 따라 이를 수행할 의무가 있을 수 있습니다.  FCC의 text-to-911 규칙이 서비스 또는 애플리케이션에 적용되는지 여부를 평가해야 합니다. 이러한 규칙이 적용되는 범위 내에서 911 문자 메시지를 요청하는 비상 콜 센터로 라우팅할 책임이 있습니다. 자체 text-to-911 전달 모델을 자유롭게 결정할 수 있지만, FCC에서 허용하는 한 가지 접근 방식은 네이티브 모바일 통신 사업자를 통해 911 텍스트를 전달하기 위해 사용자의 모바일 디바이스에서 기본 전화 걸기가 자동으로 시작됩니다.

## <a name="are-there-any-limits-on-sending-messages"></a>메시지 전송에 제한이 있나요?

SLA와 일치하는 고품질 서비스를 계속 제공할 수 있도록 Azure Communication Services는 속도 제한을 적용합니다(기본 서비스마다 다름). 한도를 초과하여 API를 호출하는 개발자는 429 HTTP 상태 코드 응답을 받게 됩니다. 회사에서 속도 제한을 초과하는 요구 사항이 있는 경우에는 phone@microsoft.com으로 이메일을 보내주세요.

SMS의 속도 제한:

|작업(Operation)|전화 번호 유형 |Scope|시간 범위| 제한(요청 #) | 분당 메시지 단위|
|---------|---|--|-------------|-------------------|-------------------------|
|메시지 보내기|수신자 부담|숫자당|60|200|200|
|메시지 보내기|짧은 코드 |숫자당|60|6000|6000|


## <a name="how-does-azure-communication-services-handle-opt-outs-for-toll-free-numbers"></a>Azure Communication Services 무료 번호에 대한 옵트아웃을 어떻게 처리하나요?

미국 무료 번호에 대한 옵트아웃은 미국 운송업체에 의해 위임되고 적용되며 재정의할 수 없습니다. 
- **STOP** - 문자 메시지 수신자가 옵트아웃하려는 경우 수신자 부담 번호로 'STOP'을 보낼 수 있습니다. 이동 통신 사업자는 STOP에 대해 다음과 같은 기본 응답을 보냅니다. *"네트워크 메시지: 이 번호로부터 온 모든 문자를 차단하는 "stop"이라는 단어를 회신했습니다. 메시지를 다시 받으려면 "unstop" 문자를 다시 보내세요."*
- **START/UNSTOP** - 수신자가 무료 번호의 문자 메시지를 다시 구독하려는 경우 수신자 부담 번호로 'START' 또는 'UNSTOP'을 보낼 수 있습니다. 이동 통신 사업자는 START/UNSTOP에 대해 다음과 같은 기본 응답을 보냅니다. *“네트워크 메시지: “unstop”을 회신했으며 이 번호로부터 메시지를 다시 수신하기 시작합니다.”*
- Azure Communication Services는 STOP 메시지를 탐지하여 수신자에게 보내는 모든 메시지를 차단합니다. 전달 보고서에는 "지정된 받는 사람에 대해 보낸 사람이 차단됨"이라는 상태 메시지가 포함된 전달 실패가 표시됩니다.
- STOP, UNSTOP 및 START 메시지는 사용자에게 다시 릴레이됩니다. Azure Communication Services는 이러한 옵트아웃을 모니터링하고 구현하여 통신을 옵트아웃한 수신자에게 더 이상 메시지가 전송되지 않도록 합니다.

## <a name="how-does-azure-communication-services-handle-opt-outs-for-short-codes"></a>Azure Communication Services 짧은 코드에 대한 옵트아웃을 어떻게 처리하나요?
- **STOP** - 문자 메시지 수신자가 옵트아웃하려는 경우 짧은 코드로 'STOP'을 보낼 수 있습니다. Azure Communication Services STOP에 대한 기본 응답을 *보냅니다. "이 번호의 메시지를 성공적으로 구독 취소했습니다. 다시 구독하기 위한 회신 시작"*
- **START/UNSTOP** - 수신자가 무료 번호의 문자 메시지를 다시 구독하려는 경우 'START' 또는 'UNSTOP을 무료 번호로 보낼 수 있습니다. Azure Communication Service는 START/UNSTOP에 대해 다음과 같은 기본 응답을 *보냅니다. "이 번호의 메시지를 성공적으로 다시 구독했습니다. 구독을 취소하려면 STOP으로 회신합니다."*
- Azure Communication Services는 STOP 메시지를 탐지하여 수신자에게 보내는 모든 메시지를 차단합니다. 전달 보고서에는 "지정된 받는 사람에 대해 보낸 사람이 차단됨"이라는 상태 메시지가 포함된 전달 실패가 표시됩니다.
- STOP, UNSTOP 및 START 메시지는 사용자에게 다시 릴레이됩니다. Azure Communication Services는 이러한 옵트아웃을 모니터링하고 구현하여 통신을 옵트아웃한 수신자에게 더 이상 메시지가 전송되지 않도록 합니다.

## <a name="how-can-i-receive-messages-using-azure-communication-services"></a>Azure Communication Services를 사용하여 메시지를 받으려면 어떻게 해야 하나요?

Azure Communication Services 고객은 Azure Event Grid를 사용하여 들어오는 메시지를 받을 수 있습니다. 이 [빠른 시작](../../quickstarts/sms/handle-sms-events.md)에 따라 메시지를 받도록 이벤트 그리드를 설정합니다.

## <a name="what-is-the-sms-character-limit"></a>SMS 문자 제한은 무엇인가요?
 단일 SMS 메시지의 크기는 140바이트입니다. 전송되는 단일 메시지당 문자 제한은 사용된 메시지 콘텐츠 및 인코딩에 따라 달라집니다. Azure Communication Services GSM-7 및 UCS-2 인코딩을 모두 지원합니다. 

- **GSM-7** - 텍스트 문자만 포함된 메시지는 GSM-7을 사용하여 인코딩됩니다.
- **UCS-2** - 유니코드(이모지, 국제 언어)를 포함하는 메시지는 UCS-2를 사용하여 인코딩됩니다.

다음 표에서는 SMS 세그먼트당 운송업체에 보낼 수 있는 최대 문자 수를 보여 드립니다.

|메시지|유형|메시지에 사용되는 문자|Encoding|단일 세그먼트의 최대 문자 수|
|-------|----|---------------|--------|--------------------------------------|
|Hello World|텍스트|GSM 표준|GSM-7|160|
|你好|Unicode|Unicode|UCS-2|70|

## <a name="can-i-sendreceive-long-messages-2048-chars"></a>긴 메시지(2048자 미만)를 보내거나 받을 수 있나요?

Azure Communication Services는 SMS를 통해 긴 메시지의 송수신을 지원합니다. 그러나 일부 무선 통신 사업자 또는 디바이스는 긴 메시지를 받을 때 다르게 작동할 수 있습니다.

## <a name="how-are-messages-sent-to-landline-numbers-treated"></a>유선 번호로 전송된 메시지는 어떻게 처리되나요?

미국에서 Azure Communication Services는 유선 번호를 확인하지 않으며 전달을 위해 이동 통신 사업자에 보내려고 시도합니다. 유선 번호로 전송된 메시지에 대한 요금이 고객에게 청구됩니다. 

## <a name="can-i-send-messages-to-multiple-recipients"></a>여러 수신자에게 메시지를 보낼 수 있나요?

예, 여러 수신자에게 하나의 요청을 할 수 있습니다. 이 [빠른 시작](../../quickstarts/sms/send.md?pivots=programming-language-csharp)에 따라 여러 수신자에게 메시지를 보냅니다.

##  <a name="i-received-a-http-status-202-from-the-send-sms-api-but-the-sms-didnt-reach-my-phone-what-do-i-do-now"></a>SMS 보내기 API에서 HTTP 상태 202를 받았지만 SMS가 내 휴대폰에 도달하지 못했습니다. 이제 어떻게 해야 하나요?

서비스에서 반환된 202는 메시지가 전송되도록 큐에 대기되었으며 전송되지 않았음을 의미합니다. [빠른 시작](../../quickstarts/sms/handle-sms-events.md)을 사용하여 전송 보고서 이벤트를 구독하고 문제를 해결하세요. 이벤트가 구성되면 전송 보고서의 “deliveryStatus” 필드를 검사하여 전송 성공/실패를 확인합니다.

## <a name="what-is-the-eligibility-to-apply-for-a-short-code"></a>짧은 코드에 적용할 자격은 무엇인가요?
짧은 코드 가용성은 현재 미국 청구 주소가 있는 유료 Azure 엔터프라이즈 구독으로 제한됩니다. 평가판 계정 또는 Azure 무료 크레딧을 사용하여 짧은 코드를 획득할 수 없습니다. 자세한 내용은 [구독 자격 페이지](../numbers/sub-eligibility-number-capability.md)를 확인하세요.

## <a name="can-you-text-to-a-toll-free-number-from-a-short-code"></a>짧은 코드에서 무료 번호로 텍스트를 입력할 수 있나요?
아니요. 짧은 코드에서 무료 번호로 문자 메시지는 지원되지 않습니다. 또한 무료 번호에서 짧은 코드로 메시지를 받을 수 없습니다.

## <a name="how-should-a-short-code-be-formatted"></a>짧은 코드의 형식은 어떻게 지정해야 합니까?
짧은 코드는 E.164 서식 지정 지침에 속하지 않으며 국가 코드 또는 "+" 기호 접두사도 없습니다. SMS API 요청에서 짧은 코드는 접두사 없이 짧은 코드 블레이드에 표시되는 5-6자리 숫자로 전달되어야 합니다. 

## <a name="how-long-does-it-take-to-get-a-short-code-what-happens-after-a-short-code-program-brief-application-is-submitted"></a>짧은 코드를 얻는 데 얼마나 걸리나요? 짧은 코드 프로그램 간략한 애플리케이션이 제출되면 어떻게 되나요?
Azure Portal 짧은 코드 프로그램 간략한 애플리케이션을 제출하면 Azure Communication Services 집계와 함께 작동하여 각 이동 통신 사업자가 애플리케이션을 승인합니다. 이 프로세스는 일반적으로 8~12주가 걸립니다.
