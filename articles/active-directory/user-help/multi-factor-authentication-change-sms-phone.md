---
title: 문자 메시지 2단계 인증을 사용하는 경우의 일반적인 문제 - Azure Active Directory | Microsoft Docs
description: 2단계 인증 방법으로 다른 모바일 디바이스를 설정하는 방법을 알아봅니다.
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: end-user-help
ms.date: 07/28/2021
ms.author: curtand
ms.openlocfilehash: 2589fee3b974940a4ca60ad2e2f9861d8a5f69d5
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122536987"
---
# <a name="common-problems-with-text-message-two-step-verification"></a>문자 메시지 2단계 인증의 일반적인 문제

문자 메시지로 확인 코드를 받는 것이 2단계 인증의 일반적인 확인 방법입니다. 예기치 않게 코드를 받았거나 잘못된 휴대폰에서 코드를 받은 경우 다음 단계를 사용하여 이 문제를 해결합니다.  

> [!Note]
> 조직에서 확인용 문자 메시지 수신을 허용하지 않는 경우 다른 방법을 선택하거나 관리자에게 도움을 요청해야 합니다.

## <a name="if-you-received-the-code-on-the-wrong-phone"></a>잘못된 휴대폰에서 코드를 받은 경우

1. **내 보안 정보** 에 로그인하여 보안 정보를 관리합니다.

1. **보안 정보** 페이지의 등록된 인증 방법 목록에서 변경하려는 전화 번호를 선택한 다음, **변경** 을 선택합니다.

1. 새 번호의 국가나 지역을 선택하고 모바일 디바이스 전화 번호를 입력합니다.

1. **확인용 문자 메시지를 받기 위해 내게 문자 메시지로 코드 보내기** 를 선택한 후 **다음** 을 선택합니다.

1. 메시지가 표시되면 Microsoft에서 받은 문자 메시지의 확인 코드를 입력하고 **다음** 을 선택합니다.

1. 휴대폰이 성공적으로 등록되었다는 알림이 표시되면 **완료** 를 선택합니다.

## <a name="if-you-receive-a-code-unexpectedly"></a>예기치 않게 코드를 받은 경우

### <a name="if-you-already-registered-your-phone-number-for-two-step-verification"></a>2단계 인증을 위해 전화 번호를 이미 등록한 경우

예기치 않은 문자 메시지를 받은 경우 누군가가 암호를 알고 계정을 사용하려는 것일 수 있습니다. 암호를 즉시 변경하고 조직의 관리자에게 알립니다.

### <a name="if-you-never-registered-your-phone-number-for-two-step-verification"></a>2단계 인증을 위해 전화 번호를 등록하지 않은 경우

문자 메시지 본문에 `STOP`을 사용하여 문자 메시지에 회신할 수 있습니다. 이 메시지를 보내면 공급자가 더 이상 해당 전화 번호로 메시지를 보내지 않습니다. 다른 코드를 사용하여 유사한 메시지에 회신해야 할 수도 있습니다.  

그러나 이미 2단계 인증을 사용 중인 경우 이 메시지를 보내면 해당 전화 번호로 로그인할 수 없습니다. 문자 메시지 수신을 다시 시작하려면 본문에 `START`를 사용하여 초기 문자 메시지에 회신합니다.

## <a name="next-steps"></a>다음 단계

- [2단계 인증에 관한 도움말 얻기](multi-factor-authentication-end-user-troubleshoot.md)
- [2단계 인증 방법으로 휴대폰 설정](multi-factor-authentication-setup-phone-number.md)