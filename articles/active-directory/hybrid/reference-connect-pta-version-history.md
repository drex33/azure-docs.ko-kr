---
title: 'Azure AD 통과 인증: 버전 릴리스 기록 | Microsoft Docs'
description: 이 문서에는 Azure AD 통과 인증 에이전트의 모든 릴리스가 나와 있습니다.
services: active-directory
author: billmath
manager: daveba
ms.assetid: ef2797d7-d440-4a9a-a648-db32ad137494
ms.service: active-directory
ms.topic: reference
ms.workload: identity
ms.date: 04/14/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 852a842c9b58a76742d0b482e62c49cd91d34f1b
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2021
ms.locfileid: "123099648"
---
# <a name="azure-ad-pass-through-authentication-agent-version-release-history"></a>Azure AD 통과 인증 에이전트: 버전 릴리스 기록 
 
통과 인증을 사용하는 온-프레미스에 설치된 에이전트는 새로운 기능을 제공하기 위해 정기적으로 업데이트됩니다. 이 문서에는 새로운 기능이 도입될 때 추가된 버전 및 기능이 나와 있습니다. 통과 인증 에이전트는 새 버전이 릴리스될 때 자동으로 업데이트됩니다. 

다음은 관련 항목입니다. 

- [Azure AD 통과 인증을 사용한 사용자 로그인](how-to-connect-pta.md) 
- [Azure AD 통과 인증 에이전트 설치](how-to-connect-pta-quick-start.md) 

## <a name="1524820"></a>1.5.2482.0
### <a name="release-status"></a>릴리스 상태: 
2017년 7월 7일: 다운로드용으로 릴리스됨

### <a name="new-features-and-improvements"></a>새로운 기능 및 향상 기능

- SHA-256RSA를 사용하여 서명된 최신 버전으로 패키지/라이브러리를 업그레이드했습니다.

## <a name="1517420"></a>1.5.1742.0
### <a name="release-status"></a>릴리스 상태: 
2020년 4월 9일: 다운로드용으로 릴리스됨

### <a name="new-features-and-improvements"></a>새로운 기능 및 향상 기능

- 설치 시 클라우드 환경을 대상으로 지정하기 위한 지원을 추가했습니다. 번들을 지정된 클라우드 환경으로 고정할 수 있습니다.



## <a name="1510070"></a>1.5.1007.0 
### <a name="release-status"></a>릴리스 상태 
2019년 1월 22일: 다운로드용으로 릴리스됨  
### <a name="new-features-and-improvements"></a>새로운 기능 및 향상 기능 
- 아웃바운드 연결에 대해 또 다른 연결 복원력 계층을 추가하기 위해 Service Bus 신뢰할 수 있는 채널 지원을 추가했습니다. 
- 에이전트를 등록하는 동안 TLS 1.2 적용 

## <a name="156430"></a>1.5.643.0 
### <a name="release-status"></a>릴리스 상태 
2018년 4월 10일: 다운로드용으로 릴리스됨  
### <a name="new-features-and-improvements"></a>새로운 기능 및 향상 기능 
- 웹 소켓 연결 지원 
- TLS 1.2를 에이전트의 기본 프로토콜로 설정 
 
## <a name="154050"></a>1.5.405.0 
### <a name="release-status"></a>릴리스 상태 
2018년 1월 31일: 다운로드용으로 릴리스됨  
### <a name="fixed-issues"></a>해결된 문제 
- 에이전트에서 약간의 메모리 누수를 초래하는 버그를 수정했습니다. 
- 커넥터 시간 제한 이슈에 대한 버그 수정을 포함하여 Azure Service Bus 버전을 업데이트했습니다. 
### <a name="new-features-and-improvements"></a>새로운 기능 및 향상 기능 
- 연결 복원력을 개선하기 위해 에이전트와 Azure AD 서비스 간 websocket 기반 연결 지원을 추가했습니다.

## <a name="154020"></a>1.5.402.0 
### <a name="release-status"></a>릴리스 상태 
2017년 11월 25일: 다운로드용으로 릴리스됨  
### <a name="fixed-issues"></a>해결된 문제 
- 기본 프록시 시나리오의 DNS 캐시와 관련된 버그를 수정했습니다. 
 
## <a name="153890"></a>1.5.389.0 
### <a name="release-status"></a>릴리스 상태 
2017년 10월 17일: 다운로드용으로 릴리스됨  
### <a name="new-features-and-improvements"></a>새로운 기능 및 향상 기능 
- DNS 오류에서의 복원력을 추가하기 위해 아웃바운드 연결에 대한 DNS 캐시 기능을 추가했습니다. 
 
## <a name="152610"></a>1.5.261.0 
### <a name="release-status"></a>릴리스 상태 
2017년 8월 31일: 다운로드용으로 릴리스됨  
### <a name="new-features-and-improvements"></a>새로운 기능 및 향상 기능 
- Azure AD 통과 인증 에이전트의 GA 버전 

## <a name="next-steps"></a>다음 단계

- [Azure Active Directory 통과 인증으로 사용자 로그인](how-to-connect-pta.md)
