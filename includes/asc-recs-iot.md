---
author: memildin
ms.service: defender-for-cloud
ms.topic: include
ms.date: 12/04/2021
ms.author: memildin
ms.custom: generated
ms.openlocfilehash: 72d99d5222103f0e65fb997d93fb9f76c58bb0db
ms.sourcegitcommit: b69ce103ff31805cf2002b727670db9452ef8518
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/05/2021
ms.locfileid: "133568687"
---
이 범주에는 **12** 개의 권장 사항이 있습니다.

|권장 |Description |심각도 |
|---|---|---|
|[기본 IP 필터 정책은 거부여야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Security/RecommendationsBlade/assessmentKey/5a3d6cdd-8eb3-46d2-ba11-d24a0d47fe65) |IP 필터 구성에서는 허용된 트래픽에 대한 규칙이 정의되어야 하고 기본적으로 다른 모든 트래픽을 거부해야 합니다.<br />(관련 정책 없음) |중간 |
|[IoT Hub의 진단 로그를 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Security/RecommendationsBlade/assessmentKey/77785808-ce86-4e40-b45f-19110a547397) |로그를 사용하도록 설정하고 최대 1년 간 보존합니다. 이렇게 하면 보안 인시던트가 발생하거나 네트워크가 손상된 경우 조사 목적으로 활동 내역을 다시 만들 수 있습니다.<br />(관련 정책: [IoT Hub에서 진단 로그를 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f383856f8-de7f-44a2-81fc-e5135b5c2aa4)) |낮음 |
|[동일한 인증 자격 증명](https://portal.azure.com/#blade/Microsoft_Azure_Security/RecommendationsBlade/assessmentKey/9d07b7e6-2986-4964-a76c-b2689604e212) |IoT Hub에 대한 동일한 인증 자격 증명이 여러 디바이스에 사용됩니다. 합법적인 디바이스를 가장한 불법 디바이스라는 의미일 수 있습니다. 또한 공격자가 디바이스를 가장할 위험이 있습니다.<br />(관련 정책 없음) |높음 |
|[IoT 디바이스 - 에이전트에서 사용률이 낮다는 메시지 전송](https://portal.azure.com/#blade/Microsoft_Azure_Security/RecommendationsBlade/assessmentKey/a9a59ebb-5d6f-42f5-92a1-036fd0fd1879) |현재 IoT 에이전트 메시지 크기 용량의 사용률이 낮아서 전송된 메시지 수가 증가합니다. 사용률을 높이도록 메시지 간격을 조정하세요.<br />(관련 정책 없음) |낮음 |
|[IoT 디바이스 - Auditd 프로세스가 이벤트 전송을 중지함](https://portal.azure.com/#blade/Microsoft_Azure_Security/RecommendationsBlade/assessmentKey/d74d2738-2485-4103-9919-69c7e63776ec) |이 디바이스에서 더 이상 Auditd 프로세스에서 시작된 보안 이벤트를 수신하지 않습니다.<br />(관련 정책 없음) |높음 |
|[IoT 디바이스 - 디바이스에서 포트 열기](https://portal.azure.com/#blade/Microsoft_Azure_Security/RecommendationsBlade/assessmentKey/1a36f14a-8bd8-45f5-abe5-eef88d76ab5b) |디바이스에서 수신 대기 엔드포인트를 찾았습니다.<br />(관련 정책 없음) |중간 |
|[IoT 디바이스 - 운영 체제 기준선 유효성 검사 실패](https://portal.azure.com/#blade/Microsoft_Azure_Security/RecommendationsBlade/assessmentKey/5f65e47f-7a00-4bf3-acae-90ee441ee876) |보안 관련 시스템 구성 문제 발견<br />(관련 정책 없음) |중간 |
|[IoT 디바이스 - 체인 중 하나에서 허용되는 방화벽 정책을 찾음](https://portal.azure.com/#blade/Microsoft_Azure_Security/RecommendationsBlade/assessmentKey/beb62be3-5e78-49bd-ac5f-099250ef3c7c) |주 방화벽 체인(입/출력)에서 허용되는 방화벽 정책을 찾았습니다. 정책에서 기본적으로 모든 트래픽을 거부해야 합니다. 디바이스와 필요한 통신만 허용하도록 규칙을 정의하세요.<br />(관련 정책 없음) |중간 |
|[IoT 디바이스 - 입력 체인 중 하나에서 허용되는 방화벽 규칙을 찾음](https://portal.azure.com/#blade/Microsoft_Azure_Security/RecommendationsBlade/assessmentKey/ba975338-f956-41e7-a9f2-7614832d382d) |광범위한 IP 주소 또는 포트에 대한 허용 패턴을 포함하는 방화벽 규칙을 찾았습니다.<br />(관련 정책 없음) |중간 |
|[IoT 디바이스 - 출력 체인 중 하나에서 허용되는 방화벽 규칙을 찾음](https://portal.azure.com/#blade/Microsoft_Azure_Security/RecommendationsBlade/assessmentKey/d5a8d84a-9ad0-42e2-80e0-d38e3d46028a) |광범위한 IP 주소 또는 포트에 대한 허용 패턴을 포함하는 방화벽 규칙을 찾았습니다.<br />(관련 정책 없음) |중간 |
|[IoT 디바이스 - TLS 암호화 도구 모음 업그레이드 필요](https://portal.azure.com/#blade/Microsoft_Azure_Security/RecommendationsBlade/assessmentKey/2acc27c6-5fdb-405e-9080-cb66b850c8f5) |안전하지 않은 TLS 구성이 발견되었습니다. TLS 암호화 도구 모음을 즉시 업그레이드해야 합니다.<br />(관련 정책 없음) |중간 |
|[IP 필터 규칙 큰 IP 범위](https://portal.azure.com/#blade/Microsoft_Azure_Security/RecommendationsBlade/assessmentKey/d8326952-60bb-40fb-b33f-51e662708a88) |IP 필터 허용 규칙의 원본 IP 범위가 너무 큽니다. 허용 범위가 과도하게 큰 규칙은 IoT 허브를 악의적인 공격자에게 노출할 수 있습니다.<br />(관련 정책 없음) |중간 |
|||
