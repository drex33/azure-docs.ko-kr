---
title: Azure Communication Services 숫자 형식 개념
titleSuffix: An Azure Communication Services concept document
description: 숫자 형식 개념에 대해 알아봅니다.
author: sadas
manager: rcole
services: azure-communication-services
ms.author: sadas
ms.date: 11/28/2021
ms.topic: conceptual
ms.service: azure-communication-services
ms.openlocfilehash: 109becf3b45b63af7f07908f95ed82e2e6c65ae1
ms.sourcegitcommit: 66b6e640e2a294a7fbbdb3309b4829df526d863d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/01/2021
ms.locfileid: "133386799"
---
# <a name="number-types"></a>숫자 형식
Azure Communication Services를 사용하면 전화 번호를 사용하여 PSTN(공중 전화망)을 통해 음성 통화를 하고 SMS 메시지를 보낼 수 있습니다. 이 문서에서는 Communication Services 사용하여 전화 통신 및 SMS 솔루션을 계획하기 위한 전화 번호 유형, 지역 가용성 및 사용 사례를 검토합니다.

## <a name="available-options"></a>사용 가능한 옵션
Azure Communication Services 세 가지 유형의 숫자( 무료, 로컬 및 짧은 코드)를 제공합니다.
-   **SMS를 보내거나 받으려면** Toll-Free 번호 또는 짧은 코드를 선택합니다.
-   **전화를 걸거나 전화를 받으려면** Toll-Free 번호 또는 현지 번호를 선택합니다.

아래 표에는 지원되는 기능과 함께 이러한 숫자 형식이 요약됩니다.

|Type |예제|SMS 보내기   | SMS 받기 |호출   |호출 받기|일반적인 사용 사례|제한|
|:-------------|:-------------|:-------------:|:-------------:|:-------------:|:-------------:|:-------------|:-------------|
|[수신자 부담](../../quickstarts/telephony/get-phone-number.md)|+1(8AB) XYZ PQRS|X       |X      |X      |X      |IVR 봇, SMS 알림에 대한 호출 수신|미국 내 SMS만|
|[현지(지역)](../../quickstarts/telephony/get-phone-number.md)|+1(ABC) XYZ PQRS|           |       |X      |X      |지리별 수|호출 전용|
|[짧은 코드](../../quickstarts/sms/apply-for-short-code.md)|ABC-XYZ|X     |X      |       |       |고속 SMS|SMS만|

## <a name="next-steps"></a>다음 단계
- 무료 [또는 로컬 전화 번호](../../quickstarts/telephony/get-phone-number.md) 받기
- 짧은 [코드](../../quickstarts/sms/apply-for-short-code.md) 얻기
