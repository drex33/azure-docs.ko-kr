---
title: 런타임 시 사용자 지정 명령 애플리케이션에 대한 문제 해결 가이드
titleSuffix: Azure Cognitive Services
description: 이 문서에서는 사용자 지정 명령 애플리케이션에서 런타임 오류를 디버깅하는 방법에 대해 알아봅니다.
services: cognitive-services
author: xiaojul
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/18/2020
ms.author: xiaojul
ms.openlocfilehash: 7577a1c2666c8b5d34920e3827a9f4549b69edd2
ms.sourcegitcommit: d01c2b2719e363178720003b67b968ac2a640204
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/19/2021
ms.locfileid: "122539546"
---
# <a name="troubleshoot-a-custom-commands-application-at-runtime"></a>런타임 시 사용자 지정 명령 애플리케이션 문제 해결

이 문서에서는 사용자 지정 명령 애플리케이션을 실행하는 동안 오류가 표시될 때 디버깅하는 방법에 대해 설명합니다. 

## <a name="connection-failed"></a>연결 실패

[클라이언트 애플리케이션(Speech SDK 사용)](./how-to-custom-commands-setup-speech-sdk.md) 또는 [Windows 음성 도우미 클라이언트](./how-to-custom-commands-developer-flow-test.md)에서 사용자 지정 명령 애플리케이션을 실행하는 경우 아래에 나열된 연결 오류가 발생할 수 있습니다.

| 오류 코드 | 세부 정보 |
| ------- | -------- |
| [401](#error-401) | AuthenticationFailure: 인증 오류로 인해 WebSocket 업그레이드 실패 |
| [1002](#error-1002) | 상태 코드 '101'이 필요한데 서버에서 상태 코드 '404'를 반환했습니다. |

### <a name="error-401"></a>오류 401
- 클라이언트 애플리케이션에 지정된 지역이 사용자 지정 명령 애플리케이션의 지역과 일치하지 않음

- 음성 리소스 키가 잘못됨
    
    음성 리소스 키가 올바른지 확인합니다.

### <a name="error-1002"></a>오류 1002 
- 사용자 지정 명령 애플리케이션이 게시되지 않음
    
    포털에 애플리케이션을 게시합니다.

- 사용자 지정 명령 애플리케이션이 유효하지 않음

    사용자 지정 명령 애플리케이션 ID가 올바른지 확인합니다.
 음성 리소스 외부의 사용자 지정 명령 애플리케이션

    사용자 지정 명령 애플리케이션이 음성 리소스에 만들어졌는지 확인합니다.

연결 문제 해결에 대한 자세한 내용은 [Windows 음성 도우미 클라이언트 문제 해결](https://github.com/Azure-Samples/Cognitive-Services-Voice-Assistant/tree/master/clients/csharp-wpf#troubleshooting)을 참조하세요.


## <a name="dialog-is-canceled"></a>대화가 취소됨

사용자 지정 명령 애플리케이션을 실행하는 경우 일부 오류가 발생하면 대화가 취소됩니다.

- 포털에서 애플리케이션을 테스트하는 경우 취소 설명을 직접 표시하고 오류 이어콘을 재생합니다. 

- [Windows 음성 도우미 클라이언트](./how-to-custom-commands-developer-flow-test.md)에서 애플리케이션을 실행하는 경우 오류 이어콘을 재생합니다. **활동 로그** 에서 **이벤트: CancelledDialog** 를 찾을 수 있습니다.

- 클라이언트 애플리케이션 예제 [클라이언트 애플리케이션(Speech SDK 포함)](./how-to-custom-commands-setup-speech-sdk.md)를 따르는 경우 오류 이어콘을 재생합니다. **상태** 에서 **이벤트: CancelledDialog** 를 찾을 수 있습니다.

- 고유 클라이언트 애플리케이션을 빌드하는 경우 항상 CancelledDialog 이벤트를 처리하도록 원하는 논리를 디자인할 수 있습니다.

CancelledDialog 이벤트는 아래와 같이 취소 코드와 설명으로 구성됩니다.

| 취소 코드 | 취소 설명 |
| ------- | --------------- | ----------- |
| [MaxTurnThresholdReached](#no-progress-was-made-after-the-max-number-of-turns-allowed) | 허용된 최대 턴 수 이후 진행되지 않음 |
| [RecognizerQuotaExceeded](#recognizer-usage-quota-exceeded) | 인식기 사용량 할당량 초과 |
| [RecognizerConnectionFailed](#connection-to-the-recognizer-failed) | 인식기 연결 실패 |
| [RecognizerUnauthorized](#this-application-cannot-be-accessed-with-the-current-subscription) | 현재 구독으로 이 애플리케이션에 액세스할 수 없음 |
| [RecognizerInputExceededAllowedLength](#input-exceeds-the-maximum-supported-length) | 입력이 인식기의 최대 지원 길이를 초과함 |
| [RecognizerNotFound](#recognizer-not-found) | 인식기를 찾을 수 없음 |
| [RecognizerInvalidQuery](#invalid-query-for-the-recognizer) | 인식기에 대한 잘못된 쿼리 |
| [RecognizerError](#recognizer-return-an-error) | 인식기가 오류를 반환함 |

### <a name="no-progress-was-made-after-the-max-number-of-turns-allowed"></a>허용된 최대 턴 수 이후 진행되지 않음
필요한 슬롯이 특정 수의 턴 이후 성공적으로 업데이트되지 않으면 대화가 취소됩니다. 기본 최대 수는 3입니다.

### <a name="recognizer-usage-quota-exceeded"></a>인식기 사용량 할당량 초과
LUIS(Language Understanding)에는 리소스 사용량에 대한 제한이 있습니다. 일반적으로 "인식기 사용량 할당량 초과" 오류는 다음과 같은 경우에 발생할 수 있습니다. 
- LUIS 작성 제한 초과

    사용자 지정 명령 애플리케이션에 예측 리소스 추가: 
    1. **설정**, LUIS 리소스로 이동
    1. **예측 리소스** 에서 예측 리소스를 선택하거나 **새 리소스 만들기** 클릭 

- LUIS 예측 리소스가 제한을 초과함

    F0 예측 리소스를 사용하는 경우 월간 10,000개, 초당 5개의 쿼리 제한이 있습니다.

LUIS 리소스 제한에 대한 자세한 내용은 [Language Understanding 리소스 사용량 및 제한](../luis/luis-limits.md#resource-usage-and-limits)을 참조하세요.

### <a name="connection-to-the-recognizer-failed"></a>인식기 연결 실패
일반적으로 Language Understanding(LUIS) 인식기에 대한 일시적인 연결 오류를 의미합니다. 다시 시도하면 문제가 해결됩니다.

### <a name="this-application-cannot-be-accessed-with-the-current-subscription"></a>현재 구독으로 이 애플리케이션에 액세스할 수 없음
구독에 LUIS 애플리케이션에 액세스할 수 있는 권한이 없습니다. 

### <a name="input-exceeds-the-maximum-supported-length"></a>입력이 지원되는 길이를 초과함
입력이 500자를 초과했습니다. 입력 발화에 대해 최대 500자까지만 허용됩니다.

### <a name="invalid-query-for-the-recognizer"></a>인식기에 대한 잘못된 쿼리
입력이 500자를 초과했습니다. 입력 발화에 대해 최대 500자까지만 허용됩니다.

### <a name="recognizer-return-an-error"></a>인식기가 오류를 반환함
입력을 인식하려는 동안 LUIS 인식기에서 오류를 반환했습니다.

### <a name="recognizer-not-found"></a>인식기를 찾을 수 없음
사용자 지정 명령 대화 모델에 지정된 인식기 유형을 찾을 수 없습니다. 현재는 [Language Understanding(LUIS) Recognizer](https://www.luis.ai/)만 지원합니다.

## <a name="other-common-errors"></a>기타 일반적인 오류
### <a name="unexpected-response"></a>예기치 않은 응답
여러 원인으로 인해 예기치 않은 응답이 발생할 수 있습니다. 몇 가지 확인할 사항:
- 예제 문장의 Yes/No 의도

    현재는 확인 기능에 사용하는 경우를 제외하고 Yes/No 의도를 지원하지 않습니다. 예제 문장에 정의된 모든 Yes/No 의도는 검색되지 않습니다.

- 명령 간의 유사한 의도 및 예제 문장

    두 명령이 유사한 의도와 예제 문장을 공유하는 경우 LUIS 인식 정확도에 영향을 미칠 수 있습니다. 명령 기능 및 예제 문장을 가능한 구분되도록 만들 수 있습니다.

    인식 정확도 향상에 대한 모범 사례는 [LUIS 모범 사례](../luis/luis-concept-best-practices.md)를 참조하세요.

- 대화가 취소됨
    
    위의 섹션에서 취소 이유를 확인합니다.

### <a name="error-while-rendering-the-template"></a>템플릿 렌더링 도중 오류
음성 응답에서 정의되지 않은 매개 변수가 사용됩니다. 

### <a name="object-reference-not-set-to-an-instance-of-an-object"></a>개체 참조가 개체의 인스턴스로 설정되지 않음
**클라이언트로 작업 보내기** 작업에서 정의된 JSON 페이로드에 매개 변수가 비어 있습니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [GitHub 샘플 참조](https://aka.ms/speech/cc-samples)
