---
title: Azure Communication Services 구독 자격 및 숫자 기능
titleSuffix: An Azure Communication Services concept document
description: Communication Services PSTN 및 SMS 번호의 구독 자격 및 번호 기능에 대해 알아봅니다.
author: sadas
manager: rcole
services: azure-communication-services
ms.author: sadas
ms.date: 11/28/2021
ms.topic: conceptual
ms.service: azure-communication-services
ms.openlocfilehash: e261d63549952dbf11bee3d1a64fddce24dff629
ms.sourcegitcommit: 66b6e640e2a294a7fbbdb3309b4829df526d863d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/01/2021
ms.locfileid: "133386798"
---
# <a name="subscription-eligibility-and-number-capabilities"></a>구독 자격 및 숫자 기능

번호는 적격 Azure 구독 및 Communication Services 제공할 수 있는 지역에서 구입할 수 있습니다.

## <a name="subscription-eligibility"></a>구독 자격

전화 번호를 얻으려면 유료 Azure 구독에 있어야 합니다. 평가판 계정 또는 Azure 체험 크레딧으로는 전화 번호를 얻을 수 없습니다.

적격 구독 유형에 대한 추가 세부 정보는 다음과 같습니다.

|전화 번호 유형   |적격 Azure 계약 유형|
|:---|:---|
|Toll-Free 및 로컬(지리적)|최신 고객 계약(현장 및 고객 주도), CSP(최신 파트너 계약), 기업계약|
|Short-Codes   |최신 고객 계약(현장 주도) 및 기업계약 전용|

## <a name="number-capabilities"></a>숫자 기능

사용할 수 있는 기능은 (Azure 청구 주소 위치) 내에서 운영하는 국가, 사용 사례 및 선택한 전화 번호 유형에 따라 달라집니다. 이러한 기능은 규정 요구 사항 때문에 국가별로 다릅니다.

아래 표에는 현재 가용성이 요약됩니다.

### <a name="customers-with-us-azure-billing-addresses"></a>미국 Azure 청구 주소가 있는 고객
|숫자|유형   |SMS 보내기   | SMS 받기 |호출   |호출 받기|
|:---|:---|:---|:---|:---|:---|
|미국(PR 포함)   |수신자 부담   |GA   |GA   |GA   |GA   |
|미국(PR 포함)   |로컬 |  |   |GA   |GA   |
|USA  |Short-Codes |공개 미리 보기  |공개 미리 보기   |   |   |

### <a name="new-customers-with-uk-azure-billing-addresses"></a>(신규) 영국 Azure 청구 주소가 있는 고객
|숫자|유형   |SMS 보내기   | SMS 받기 |호출   |호출 받기|
|:---|:---|:---|:---|:---|:---|
|미국(PR 포함)   |수신자 부담   |GA   |GA  |공개 미리 보기   |공개 미리 보기   |
|미국(PR 포함)   |로컬 |   |   |공개 미리 보기   |공개 미리 보기   |

### <a name="new-customers-with-ireland-azure-billing-addresses"></a>(신규) 아일랜드 Azure 청구 주소가 있는 고객
|숫자|유형   |SMS 보내기   | SMS 받기 |호출   |호출 받기|
|:---|:---|:---|:---|:---|:---|
|미국(PR 포함)   |수신자 부담   |GA   |GA   |GA   |GA   |
|미국(PR 포함)   |로컬 |  |   |GA   |GA   |

## <a name="next-steps"></a>다음 단계
- 무료 [또는 로컬 전화 번호](../../quickstarts/telephony/get-phone-number.md) 받기
- 짧은 [코드](../../quickstarts/sms/apply-for-short-code.md) 얻기

