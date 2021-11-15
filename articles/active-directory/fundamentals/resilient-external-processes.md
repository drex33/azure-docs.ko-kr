---
title: Azure AD B2C를 사용하여 외부 프로세스를 사용하는 인터페이스의 복원력 향상 | Microsoft Docs
description: 외부 프로세스를 사용하는 복원력 있는 인터페이스를 빌드하는 방법
services: active-directory
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: how-to
author: gargi-sinha
ms.author: gasinh
manager: martinco
ms.reviewer: ''
ms.date: 11/30/2020
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: fede718111b6f1dcc9e49e8c96ec88a967b31b20
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2021
ms.locfileid: "130042337"
---
# <a name="resilient-interfaces-with-external-processes"></a>외부 프로세스를 사용하는 복원력 있는 인터페이스

이 문서에서는 RESTful API 관련 계획을 수립하고 사용자 경험에 이를 구현하여 API 오류에 대한 애플리케이션의 복원력을 향상하는 방법에 대한 지침을 제공합니다.

![외부 프로세스 구성 요소가 포함된 인터페이스를 보여 주는 이미지](media/resilient-external-processes/external-processes-architecture.png)

## <a name="ensure-correct-placement-of-the-apis"></a>API의 올바른 배치

IEF(Identity Experience Framework) 정책을 사용하면 [RESTful API 기술 프로필](../../active-directory-b2c/restful-technical-profile.md)을 사용하여 외부 시스템을 호출할 수 있습니다. 외부 시스템은 IEF 런타임 환경에 의해 관리되지 않으며 오류가 발생할 가능성이 있습니다.

### <a name="how-to-manage-external-systems-using-apis"></a>API를 사용하여 외부 시스템을 관리하는 방법

- 특정 데이터에 액세스하기 위해 인터페이스를 호출하는 동안 데이터가 인증 결정을 내릴지 여부를 확인합니다. 정보가 애플리케이션의 핵심 기능에 중요한지 여부를 평가합니다. 예를 들어 전자상거래와 보조 기능(예: 관리)을 비교할 수 있습니다. 정보가 인증에 필요하지 않고 보조 시나리오에만 필요한 경우에는 호출을 애플리케이션 로직으로 이동하는 것이 좋습니다.

- 인증에 필요한 데이터가 비교적 정적이고, 작고, 디렉터리에서 외부로 옮길 다른 비즈니스상의 이유가 없는 경우 디렉터리에 두는 것이 좋습니다.

- 가능하면 사전 인증 경로에서 API 호출을 제거합니다. 사용할 수 없는 경우에는 API에 대한 향후의 DoS(서비스 거부) 공격과 DDoS(분산 서비스 거부) 공격에 대비해 엄격한 보호를 적용해야 합니다. 공격자는 로그인 페이지를 로드하고 DoS 공격으로 API 폭주를 일으켜 애플리케이션에 심각한 손상을 줄 수 있습니다. 예를 들어 로그인에 CAPTCHA를 사용하면 등록 흐름이 도움이 될 수 있습니다.

- [기본 제공 사용자 등록 흐름의 API 커넥터](../../active-directory-b2c/api-connectors-overview.md)를 사용하면 단계에 구애받지 않고 등록하는 동안 ID 공급자를 통해 페더레이션한 후 또는 사용자를 만들기 전에 웹 API와 통합할 수 있습니다. 사용자 흐름은 이미 널리 테스트되었으므로 사용자 흐름 수준의 기능, 성능 또는 규모를 테스트할 필요가 없을 수 있습니다. 그러나 애플리케이션의 기능, 성능, 규모는 테스트해야 합니다.

- Azure AD RESTful API [기술 프로필](../../active-directory-b2c/restful-technical-profile.md)은 캐싱 동작을 제공하지 않습니다. 대신 RESTful API 프로필은 정책에 기본 제공되는 다시 시도 논리와 시간 제한을 구현합니다.

- 데이터 작성이 필요한 API의 경우 백그라운드 작업자가 이러한 작업을 실행하도록 작업을 큐에 추가합니다. [Azure 큐](../../storage/queues/storage-queues-introduction.md)와 같은 서비스를 사용할 수 있습니다. 이렇게 하면 API가 효율적으로 반환되고 정책 실행 성능이 향상됩니다.  

## <a name="api-error-handling"></a>API 오류 처리

API는 Azure AD B2C 시스템 외부에 있기 때문에 기술 프로필 내에서 적절한 오류 처리가 필요합니다. 최종 사용자에게 적절한 정보를 제공하고 애플리케이션이 오류를 정상적으로 처리할 수 있는지 확인합니다.

### <a name="how-to-gracefully-handle-api-errors"></a>API 오류를 정상적으로 처리하는 방법

- 다양한 이유로 API에 오류가 발생할 수 있으며, 이러한 오류로부터 애플리케이션을 복원할 수 있어야 합니다. API가 요청을 완료할 수 없는 경우 [HTTP 4XX 오류 메시지를 반환](../../active-directory-b2c/restful-technical-profile.md#returning-validation-error-message)합니다. Azure AD B2C 정책에서 API의 사용 불가 상태를 정상적으로 처리하고 축소된 환경을 렌더링하려고 합니다.

- [일시적인 오류를 정상적으로 처리](../../active-directory-b2c/restful-technical-profile.md#error-handling)합니다. RESTful API 프로필을 사용하면 다양한 [회로 차단기](/azure/architecture/patterns/circuit-breaker)에 대해 오류 메시지를 구성할 수 있습니다.

- 사전에 모니터링하고 CICD(연속 통합/지속적인 업데이트)를 사용하여 [기술 프로필 엔진](../../active-directory-b2c/restful-technical-profile.md)에서 사용하는 암호, 인증서와 같은 API 액세스 자격 증명을 순환합니다.

## <a name="api-management---best-practices"></a>API 관리 - 모범 사례

REST API를 배포하고 RESTful 기술 프로필을 구성하는 동안 권장 모범 사례를 따르면 일반적인 실수를 저지르고 어떤 사항을 간과하는 일이 없도록 할 수 있습니다.

### <a name="how-to-manage-apis"></a>API를 관리하는 방법

- APIM(API Management)은 API를 게시, 관리, 분석합니다. 또한 APIM은 백 엔드 서비스와 마이크로 서비스에 대한 보안 액세스를 제공하기 위해 인증을 처리합니다. API 게이트웨이를 사용하여 API 배포, 캐싱, 부하 분산을 스케일 아웃할 수 있습니다.

- 각 API를 여러 번 호출하고 [Azure APIM API를 보호](../../active-directory-b2c/secure-api-management.md?tabs=app-reg-ga)하는 대신 사용자 경험을 시작할 때 올바른 토큰을 제공하는 것이 좋습니다.

## <a name="next-steps"></a>다음 단계

- [Azure AD B2C 개발자를 위한 복원력 리소스](resilience-b2c.md)
  - [복원력 있는 최종 사용자 환경](resilient-end-user-experience.md)
  - [개발자 모범 사례를 통한 복원력](resilience-b2c-developer-best-practices.md)
  - [모니터링 및 분석을 통한 복원력](resilience-with-monitoring-alerting.md)
- [인증 인프라의 빌드 복원력 구축](resilience-in-infrastructure.md)
- [애플리케이션에서 인증 및 권한 부여 복원력 향상](resilience-app-development-overview.md)