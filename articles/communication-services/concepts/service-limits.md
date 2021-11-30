---
title: Azure Communication Services에 대 한 서비스 제한
titleSuffix: An Azure Communication Services how-to document
description: 학습할 방법
author: manoskow
manager: shahen
services: azure-communication-services
ms.author: manoskow
ms.date: 11/01/2021
ms.topic: how-to
ms.service: azure-communication-services
ms.subservice: data
ms.openlocfilehash: 065b25411e9ef460e50e8ef556e077a4383d260c
ms.sourcegitcommit: 991268c548dd47e5f7487cd025c7501b9315e477
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/30/2021
ms.locfileid: "133289572"
---
# <a name="service-limits-for-azure-communication-services"></a>Azure Communication Services에 대 한 서비스 제한

이 문서에서는 Azure Communication Services의 몇 가지 제한 사항 및 이러한 제한 사항을 실행 하는 경우 수행할 작업에 대해 설명 합니다. 

## <a name="throttling-patterns-and-architecture"></a>조정 패턴 및 아키텍처
서비스 제한에 도달 하면 일반적으로 HTTP 상태 코드 429 (너무 많은 요청)가 표시 됩니다. 일반적으로 제한 처리를 위한 모범 사례는 다음과 같습니다.

- 요청당 작업 수를 줄입니다.
- 호출 빈도 줄이기.
- 모든 요청이 사용 제한에 대해 계산 되기 때문에 즉각적인 재시도를 방지 합니다.

서비스 아키텍처를 설정 하 여 [조정 패턴](/azure/architecture/patterns/throttling.md)에 대 한 [Azure 아키텍처](/azure/architecture.md) 설명서의 제한 및 제한 사항을 처리 하는 방법에 대 한 보다 일반적인 지침을 찾을 수 있습니다.

## <a name="acquiring-phone-numbers"></a>전화 번호 가져오기
전화 번호를 얻기 전에 구독이 [지리적 및 구독](./telephony-sms/plan-solution.md) 요구 사항을 충족 하는지 확인 하세요. 그렇지 않으면 전화 번호를 구입할 수 없습니다. 다음 제한은 [전화 번호 SDK](./reference.md) 및 [Azure Portal](https://portal.azure.com/)를 통해 구매 번호에 적용 됩니다.

| 작업(Operation) | Scope | 시간 범위 | 제한 (요청 수) |
| --- | -- | -- | -- |
| 전화 번호 구매 | Azure 테넌트 | - | 1 |
| 전화 번호 검색 | Azure 테넌트 | 1주 | 5 |

### <a name="action-to-take"></a>수행할 작업

자세한 내용은 [전화 번호 유형](./telephony-sms/plan-solution.md) 개념 페이지 및 [전화 통신 개념](./telephony-sms/telephony-concept.md) 개요 페이지를 참조 하세요.

더 많은 전화 번호를 구매 하거나 특별 한 순서로 입력 하려면 [여기의 지침](https://github.com/Azure/Communication/blob/master/special-order-numbers.md)을 따르세요. 외부 계정에서 Azure Communication Services 계정으로 무료 전화 번호의 전화 번호를 이식 하려는 경우 여기에 있는 [지침](https://github.com/Azure/Communication/blob/master/port-numbers.md)을 따르세요.

## <a name="identity"></a>ID

| 작업(Operation) | 기간 (초) | 제한 (요청 수) |
|---|--|--|
| **ID 만들기** | 30 | 500|
| **Id 삭제** | 30 | 500|
| **액세스 토큰 발급** | 30 | 500|
| **액세스 토큰 취소**  | 1 | 100|
| **createUserAndToken**| 30 | 1000 |
| **exchangeTokens**| 30 | 500 |

### <a name="action-to-take"></a>수행할 작업
채팅 스레드 만들기 또는 호출 시작 (예: 웹 페이지가 처음 로드 될 때 또는 앱을 시작 하는 경우)과 같은 다른 트랜잭션을 시작 하기 전에 id와 토큰을 획득 하는 것이 좋습니다. 

자세한 내용은 [id 개념 개요](./authentication.md) 페이지를 참조 하세요.

## <a name="sms"></a>sms
많은 양의 메시지를 보내거나 받을 때 오류가 발생할 수 있습니다 ```429``` . 이는 서비스 제한에 도달 하 고 요청 수가 임계값 보다 낮은 경우 메시지가 전송 될 때까지 큐에 대기 한다는 것을 나타냅니다.

SMS의 속도 제한:

|작업(Operation)|Scope|기간 (초)| 제한 (요청 수) | 분당 메시지 단위|
|---------|-----|-------------|-------------------|-------------------------|
|메시지 보내기|숫자당|60|200|200|

### <a name="action-to-take"></a>수행할 작업
속도 제한을 초과 하는 양의 메시지를 전송 해야 하는 경우에 전자 메일을 보내 주세요 phone@microsoft.com .

SMS SDK 및 서비스에 대 한 자세한 내용은 sms [sdk 개요](./telephony-sms/sdk-features.md) 페이지 또는 [sms FAQ](./telephony-sms/sms-faq.md) 페이지를 참조 하십시오.

## <a name="chat"></a>채팅
채팅 Api를 사용 하는 경우 오류가 발생할 수 있습니다 ```429``` . 이는 서비스 제한에 도달 하 고 요청 수가 임계값 보다 낮은 경우 요청이 전송 될 때까지 큐에 대기 한다는 것을 나타냅니다.

| **연산**         | **범위**                 | 시간 범위 | 제한(요청 #) |
|--|--|--|--|
|메시지 보내기           | 스레드 단위            |60             |2000 |
|메시지 보내기            | 스레드별 사용자 당  |60             |50 |
|메시지 가져오기            | 스레드별 사용자 당   |-              |- |
|메시지 가져오기           |스레드별 사용자 당    |5              |15 |
|메시지 가져오기           |스레드 단위         |5          |250 |
|메시지 업데이트     |스레드별 사용자 당    |5          |3 |
|메시지 업데이트         |               |60             |30 |
|메시지 업데이트         |                   |180            |60 | 
|스레드 가져오기             |스레드별 사용자 당    |5              |10 |
|스레드 가져오기             |사용자 단위           |5          |20 |
|스레드 가져오기            |사용자 단위           |5              |40 |
|UpdateThreadRoster     |스레드 단위             |300            |25 |
|UpdateThreadRoster   |사용자 단위               |60           |25 |
|UpdateThreadRoster   |스레드별 사용자 당  |1          |- |

### <a name="other-maximum-limitations"></a>기타 최대 제한 사항

| **이름**         | 제한  |
|--|--|
|스레드의 참가자 수|250 |
|참여자 배치-CreateThread|200 |
|참여자 배치-AddParticipant|200 |

### <a name="action-to-take"></a>수행할 작업

모든 채팅 Sdk는 기본적으로 지 수 백오프를 설정 하 여 다시 시도 정책을 적용 합니다 (원하는 경우이를 재정의할 수 있음). API는 메시지 전송을 다시 시도 하기 전에 대기 하도록 제안 된 시간 (초)을 나타내는 다시 시도 헤더를 반환 합니다.

채팅 SDK 및 서비스에 대 한 자세한 내용은 [채팅 sdk 개요](./chat/sdk-features.md) 페이지를 참조 하세요.

## <a name="voice-and-video-calling"></a>음성 및 비디오 통화

### <a name="call-maximum-limitations"></a>호출의 최대 제한

| **이름**         | 제한  |
|--|--|
|참가자 수 | 350 

### <a name="calling-sdk-streaming-support"></a>통화 SDK 스트리밍 지원
Communication Services 통화 SDK는 다음과 같은 스트리밍 구성을 지원합니다.

| 제한                                                         | 웹                         | Windows/Android/iOS        |
| ------------------------------------------------------------- | --------------------------- | -------------------------- |
| **# 동시에 보낼 수 있는 최대 송신 로컬 스트림 수**     | 비디오 1개 또는 화면 공유 1개 | 비디오 1개 + 화면 공유 1개 |
| **# 동시에 렌더링할 수 있는 최대 수신 원격 스트림 수** | 비디오 4개 + 화면 공유 1개 | 비디오 6개 + 화면 공유 1개 |

통화 SDK는 이러한 제한을 적용하지 않지만 초과할 경우 사용자에게 성능 저하가 발생할 수 있습니다.

### <a name="calling-sdk-timeouts"></a>통화 SDK 제한 시간

Communication Services 통화 SDK에는 다음 제한 시간이 적용됩니다.

| 작업                                                                      | 제한 시간(초) |
| --------------------------------------------------------------------------- | ------------------ |
| 참가자 다시 연결/제거                                               | 120                |
| 통화에서 새 형식을 추가하거나 제거합니다(비디오 또는 화면 공유 시작/중지). | 40                 |
| 통화 전송 작업 제한 시간                                             | 60                 |
| 1:1 통화 설정 제한 시간                                              | 85                 |
| 그룹 통화 설정 제한 시간                                            | 85                 |
| PSTN 통화 설정 제한 시간                                             | 115                |
| 1:1 통화를 그룹 통화 제한 시간으로 승격                                    | 115                |


### <a name="action-to-take"></a>수행할 작업

SDK 및 서비스를 호출 하는 음성 및 비디오에 대 한 자세한 내용은 [sdk 호출 개요](./voice-video-calling/calling-sdk-features.md) 페이지 또는 [알려진 문제](./known-issues.md)를 참조 하세요.

## <a name="teams-interoperability-and-microsoft-graph"></a>Teams 상호 운용성 및 Microsoft Graph
Teams 상호 운용성 시나리오를 사용 하는 경우 일부 Microsoft Graph api를 사용 하 여 [모임을](/graph/cloud-communications-online-meetings.md)만들 수 있습니다.  

Microsoft Graph를 통해 제공 되는 각 서비스에는 서로 다른 제한 사항이 있습니다. 서비스 특정 제한은 여기에 자세히 [설명](/graph/throttling.md) 되어 있습니다.

### <a name="action-to-take"></a>수행할 작업
오류 처리를 구현하는 경우 HTTP 오류 코드 429를 사용하여 제한을 검색합니다. 실패 한 응답에는 ```Retry-After``` 응답 헤더가 포함 됩니다. 지연 시간을 사용 하 여 요청을 백업 하는 ```Retry-After``` 것은 Microsoft Graph에서 클라이언트를 제한 하는 동안 리소스 사용을 계속 기록 하기 때문에 조정에서 복구 하는 가장 빠른 방법입니다.

[Microsoft Graph](/graph/overview.md) 설명서 [에서 Microsoft Graph 제한 제한에](/graph/throttling.md) 대 한 자세한 내용을 확인할 수 있습니다.

## <a name="network-traversal"></a>Network Traversal

| 작업(Operation) | 기간 (초) | 제한 (요청 수) |
|---|--|--|
| **자격 증명 설정 문제** | 5 | 30000|
| **문제 릴레이 구성** | 5 | 30000|

### <a name="action-to-take"></a>수행할 작업
항상 릴레이 연결 만들기와 같은 다른 트랜잭션을 시작 하기 전에 토큰을 획득 하는 것이 좋습니다. 

자세한 내용은 [네트워크 트래버스 개념 개요](./network-traversal.md) 페이지를 참조 하세요.

## <a name="still-need-help"></a>여전히 도움이 필요하세요?
사용 가능한 [도움말 및 지원](../support.md) 옵션을 참조 하세요.

## <a name="next-steps"></a>다음 단계
