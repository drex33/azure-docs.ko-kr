---
title: Azure AD 애플리케이션 등록 구성에 대한 모범 사례 - Microsoft ID 플랫폼
description: Azure AD 애플리케이션 등록 구성에 대한 모범 사례 및 일반 참고 자료 모음에 대해 알아봅니다.
services: active-directory
author: Chrispine-Chiedo
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/8/2021
ms.custom: template-concept
ms.author: cchiedo
ms.reviewer: saumadan, marsma
ms.openlocfilehash: cd03531e35d51bafeceb2df913e17fc4814e911c
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131050580"
---
# <a name="azure-ad-application-registration-security-best-practices"></a>Azure AD 애플리케이션 등록 보안 모범 사례

Azure AD(Azure Active Directory) 애플리케이션 등록은 비즈니스 애플리케이션의 중요한 부분입니다. 구성이 잘못되거나 애플리케이션의 방역이 잘못되면 가동 중지 시간이나 손상이 발생할 수 있습니다.

애플리케이션 등록은 노출 영역 때문에 비즈니스 애플리케이션보다 더 광범위한 영향을 미친다는 것을 이해하는 것이 중요합니다. 애플리케이션에 추가된 권한에 따라 손상된 앱은 조직 전체에 영향을 미칠 수 있습니다.
애플리케이션 등록은 사용자가 로그인하도록 하는 데 필수적이므로 가동 중지 시간이 발생하면 비즈니스 또는 비즈니스가 의존하는 일부 중요한 서비스에 영향을 미칠 수 있습니다. 따라서 애플리케이션 등록이 항상 정상적인 상태를 유지하도록 시간과 리소스를 할당하는 것이 중요합니다. 코드에 대한 보안 위협 모델 평가와 마찬가지로 애플리케이션에 대한 정기적인 보안 및 상태 평가를 수행하는 것이 좋습니다. 조직의 보안에 대한 광범위한 관점을 보려면 [SDL(보안 개발 수명 주기)](https://www.microsoft.com/securityengineering/sdl)을 확인하세요.

이 문서에서는 다음 애플리케이션 등록 속성에 대한 보안 모범 사례를 설명합니다.

- 리디렉션 URI
- 액세스 토큰에 대한 암시적 허용 흐름
- 자격 증명
- AppId URI
- 애플리케이션 소유권
- 검사 목록

## <a name="redirect-uri-configuration"></a>리디렉션 URI 구성

애플리케이션의 리디렉션 URI를 최신 상태로 유지하는 것이 중요합니다. 리디렉션 URI 중 하나의 소유권이 상실되면 애플리케이션이 손상될 수 있습니다. 모든 DNS 레코드가 업데이트되고 변경 사항에 대해 주기적으로 모니터링되는지 확인합니다. 모든 URI의 소유권을 유지하는 것과 함께 와일드카드 회신 URL이나 http 또는 URN과 같은 안전하지 않은 URI 체계를 사용하지 마세요.

![redirect_uri](media/active-directory-application-registration-best-practices/redirect-uri.png)

### <a name="redirect-uri-summary"></a>리디렉션 URI 요약

| 수행                                    | 안 함          |
| ------------------------------------- | -------------- |
| 모든 URI의 소유권 유지        | 와일드카드 사용  |
| DNS를 최신 상태로 유지                   | URN 체계 사용 |
| 목록을 작게 유지                   |   -----        |
| 불필요한 URI 자르기             |   -----        |
| Http에서 Https 체계로 URL 업데이트 |   -----        |

## <a name="implicit-flow-token-configuration"></a>암시적 흐름 토큰 구성

**암시적 흐름** 이 필요한 시나리오는 이제 **인증 코드 흐름** 을 사용하여 암시적 허용 흐름 오용과 관련된 손상 위험을 줄일 수 있습니다. 암시적 흐름을 사용하여 액세스 토큰을 가져오도록 애플리케이션 등록을 구성했지만 적극적으로 사용하지 않는 경우 오용으로부터 보호하기 위해 설정을 끄는 것이 좋습니다.

![암시적 흐름에 사용되는 액세스 토큰](media/active-directory-application-registration-best-practices/implict-grant-flow.png)

### <a name="implicit-grant-flow-summary"></a>암시적 허용 흐름 요약

| 수행                                                                    | 안 함                                                                  |
| --------------------------------------------------------------------- | ---------------------------------------------------------------------- |
| [암시적 흐름이 필요한](./v2-oauth2-implicit-grant-flow.md#suitable-scenarios-for-the-oauth2-implicit-grant) 경우 이해 | [명시적으로 요구](./v2-oauth2-implicit-grant-flow.md#suitable-scenarios-for-the-oauth2-implicit-grant)하지 않는 한 암시적 흐름 사용 |
| (유효한) 암시적 흐름 시나리오에 대한 별도의 앱 등록 |   -----                                                |
| 사용하지 않는 암시적 흐름 해제 | -----              |

## <a name="credential-configuration"></a>자격 증명 구성

자격 증명은 애플리케이션이 기밀 클라이언트로 사용될 때 애플리케이션 등록의 중요한 부분입니다. 앱 등록이 공용 클라이언트 앱으로만 사용되는 경우(사용자가 공용 엔드포인트를 사용하여 로그인할 수 있음) 애플리케이션 개체에 자격 증명이 없는지 확인합니다. 애플리케이션에 사용된 자격 증명의 유효성 및 만료를 검토합니다. 애플리케이션에서 사용되지 않는 자격 증명을 사용하면 보안 위반이 발생할 수 있습니다.
암호 비밀을 자격 증명으로 사용하는 것이 편리하지만 애플리케이션에 대한 토큰을 가져오기 위한 유일한 자격 증명 형식으로 x509 인증서를 사용하는 것이 좋습니다. 프로덕션 파이프라인을 모니터링하여 모든 종류의 자격 증명이 코드 리포지토리로 커밋되지 않도록 합니다. Azure를 사용하는 경우 애플리케이션 자격 증명이 자동으로 관리되도록 관리 ID를 사용하는 것이 좋습니다. 자세한 내용은 [관리 ID 문서](../managed-identities-azure-resources/overview.md)를 참조하세요. [자격 증명 스캐너](../../security/develop/security-code-analysis-overview.md#credential-scanner)는 소스 코드 및 빌드 출력에서 자격 증명 및 기타 중요한 콘텐츠를 검색하는 데 사용할 수 있는 정적 분석 도구입니다.

![Azure Portal의 인증서 및 비밀](media/active-directory-application-registration-best-practices/credentials.png)

| 수행                                                                     | 안 함                             |
| ---------------------------------------------------------------------- | --------------------------------- |
| [인증서 자격 증명](./active-directory-certificate-credentials.md) 사용              | 암호 자격 증명 사용          |
| [관리 ID](../managed-identities-azure-resources/overview.md)와 함께 Key Vault 사용 | 앱 간에 자격 증명 공유     |
| 자주 롤오버                                                    | 하나의 앱에 여러 자격 증명 보유  |
|     -----                                                              | 부실 자격 증명을 사용 가능한 상태로 둠 |
|     -----                                                              | 코드에서 자격 증명 커밋        |

## <a name="appid-uri-configuration"></a>AppId URI 구성

특정 애플리케이션은 WebAPI를 통해 리소스를 노출할 수 있으므로 테넌트의 리소스를 고유하게 식별하는 AppId URI를 정의해야 합니다. API 또는 https URI 체계 중 하나를 사용하고 조직에서 URI 충돌을 방지하기 위해 AppId URI를 다음 형식으로 설정하는 것이 좋습니다.
AppId URI는 API 코드에서 참조되는 범위에 대한 접두사 역할을 하며 확인된 고객 소유 도메인을 사용해야 합니다. 다중 테넌트 애플리케이션의 경우에는 값도 전역적으로 고유해야 합니다.

[!INCLUDE [active-directory-identifierUri](../../../includes/active-directory-identifier-uri-patterns.md)]

![애플리케이션 ID URI](media/active-directory-application-registration-best-practices/app-id-uri.png)

### <a name="appid-uri-summary"></a>AppId URI 요약

| 수행                                           | 안 함                  |
| -------------------------------------------- | ---------------------- |
| 유효한 URI 형식을 사용하여 충돌을 방지합니다. | 와일드카드 AppId URI 사용 |
| LoB(기간 업무) 앱에서 확인된 도메인 사용 | 잘못된 URI    |
| AppId URI 인벤토리 구축                    |      -----             |
| AppId URI를 사용하여 조직에 WebApi 노출| AppId URI를 사용하여 애플리케이션을 식별하고 대신 appId 속성을 사용합니다.|

## <a name="app-ownership-configuration"></a>앱 소유권 구성

앱 소유권이 조직 내 최소한의 사람들에게만 유지되도록 합니다. 소유자가 여전히 조직의 일부이고 애플리케이션 등록 소유권에 대한 선언 계정이 있는지 확인하기 위해 몇 달에 한 번씩 소유자 목록을 실행하는 것이 좋습니다. 자세한 내용은 [Azure AD 액세스 검토](../governance/access-reviews-overview.md)를 확인하세요.

![사용자 프로비저닝 서비스 - 소유자](media/active-directory-application-registration-best-practices/app-ownership.png)

### <a name="app-ownership-summary"></a>앱 소유권 요약

| 수행                  | 안 함 |
| ------------------- | ----- |
| 작게 유지       | ----- |
| 소유자 목록 모니터링 | ----- |

## <a name="checklist"></a>검사 목록

앱 개발자는 Azure Portal에서 제공되는 _검사 목록_ 을 사용하여 앱 등록이 고품질 기준을 충족하는지 확인하고 안전하게 통합하기 위한 참고 자료를 제공할 수 있습니다. 통합 도우미는 Microsoft ID 플랫폼과 통합할 때 일반적인 실수를 방지하는 데 도움이 되는 모범 사례와 권장 사항을 강조합니다.

![Azure Portal의 통합 도우미 검사 목록](media/active-directory-application-registration-best-practices/checklist.png)

### <a name="checklist-summary"></a>검사 목록 요약

| 수행                                                 | 안 함 |
| -------------------------------------------------- | ----- |
| 검사 목록을 사용하여 시나리오 기반 권장 사항 가져오기 | ----- |
| 앱 등록 블레이드에 대한 딥 링크             | ----- |


## <a name="next-steps"></a>다음 단계
인증 코드 흐름에 대한 자세한 내용은 [OAuth 2.0 인증 코드 흐름](./v2-oauth2-auth-code-flow.md)을 참조하세요.
