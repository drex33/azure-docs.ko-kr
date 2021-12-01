---
title: 간단한 Code SMS 가격 책정 파일
description: 포함 파일
services: azure-communication-services
author: prakulka
manager: shahen
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 11/30/2021
ms.topic: include
ms.custom: Include file
ms.author: prakulka
ms.openlocfilehash: 9c8cc2fbabfa494645c44b436738f61f0e4ffa4b
ms.sourcegitcommit: 66b6e640e2a294a7fbbdb3309b4829df526d863d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/01/2021
ms.locfileid: "133386913"
---
>[!Important] 
>짧은 코드 가용성은 현재 미국에서 청구 주소가 있는 Azure enterprise 구독으로 제한 됩니다.

짧은 코드 서비스는 Azure Portal을 통해 짧은 코드를 프로 비전 해야 합니다. 짧은 코드가 프로 비전 되 면 종 량 제 가격은 임대 요금, 사용 요금 및 캐리어 요금에 적용 됩니다. 임대 요금, 사용 요금 및 운송 업체 요금은 짧은 코드 형식, 짧은 코드의 위치, 대상 및 메시지의 캐리어에 의해 결정 됩니다.

## <a name="short-codes-pricing"></a>짧은 코드 가격

### <a name="provisioning-fee"></a>프로 비전 요금
짧은 코드 프로 비전 요금은 짧은 코드 프로 비전 기간 동안 요금이 청구 됩니다.

|요금 유형   | Description |수수료|
|-----------|-------------|---|
|설치 요금 |짧은 코드가 배달 될 때 요금이 청구 됩니다. |$650 |
|임의 짧은 코드 요금 |코드 배달이 단축 되기 전에 청구 됩니다. 제한 된 기간 동안 면제 받으려면|$1000/mo *|

* 베 니 티 short 코드에 대 한 추가 $500/mo 요금은 청구 됩니다.

>[!Note] 
>짧은 코드 프로 비전은 일반적으로 8-12 주가 소요 됩니다.

### <a name="leasing-fee"></a>임대 요금
짧은 코드 임대 요금은 프로 비전이 완료 된 후 요금이 청구 된 후 월 기준으로 되풀이 됩니다.

|숫자 형식 | 월 요금제 |
|----------|-----------|
|임의 짧은 코드 |$1000/mo * |

* 베 니 티 short 코드에 대 한 추가 $500/mo 요금은 청구 됩니다.

### <a name="usage-fee"></a>사용 요금
SMS는 종량제 가격을 제공합니다. 가격은 메시지 대상을 기준으로 하는 메시지 세그먼트당 요금입니다. 짧은 코드에서 미국 내에 있는 전화번호로 메시지를 보낼 수 있습니다. 

다음 가격에는 필요한 통신세 및 요금이 포함됩니다.

|메시지 유형   |사용 요금 |
|-----------|------------|
|메시지 보내기 (메시지 세그먼트 당 *) |$0.0075 |
|메시지 수신 (메시지 세그먼트 당 *) |$0.0075 |

* 메시지 세그먼트에 대해 자세히 알아보려면 [SMS 문자 제한](../sms/sms-faq.md#what-is-the-sms-character-limit) 에 대 한 가이드를 참조 하세요.

## <a name="carrier-surcharge"></a>캐리어 요금
$0.0025/sent msg 세그먼트의 추가 플랫 캐리어 추가 요금이 적용 될 수도 있습니다. 운송 업체의 요금이 변경 될 수 있습니다. 자세한 내용은 [캐리어 추가 요금](https://github.com/Azure/Communication/blob/master/shortcode-carrier-surcharge.md) 에 대 한 가이드를 참조 하세요.
