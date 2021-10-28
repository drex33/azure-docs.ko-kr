---
title: Azure AD B2C에 대한 모범 사례
titleSuffix: Azure AD B2C
description: Azure AD B2C(Azure Active Directory B2C)로 작업할 때 고려해야 할 권장 사항 및 모범 사례입니다.
services: active-directory-b2c
author: vigunase
manager: CelesteDG
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 10/01/2021
ms.author: vigunase
ms.subservice: B2C
ms.openlocfilehash: 1ebdbcea15ae089d2799a107d29ed992807540a6
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2021
ms.locfileid: "130039679"
---
# <a name="recommendations-and-best-practices-for-azure-active-directory-b2c"></a>Azure Active Directory B2C에 대한 권장 사항 및 모범 사례

다음 모범 사례 및 권장 사항은 Azure AD(Azure Active Directory) B2C를 기존 또는 새 애플리케이션 환경에 통합하는 기본적인 몇 가지 측면을 다룹니다.

## <a name="fundamentals"></a>기본 사항

| 모범 사례 | 설명 |
|--|--|
| 대부분의 시나리오에서 사용자 흐름 선택 | Azure AD B2C의 Identity Experience Framework는 서비스의 핵심 강점입니다. 정책은 가입, 로그인 또는 프로필 편집과 같은 ID 환경을 완벽하게 설명합니다. 가장 일반적인 ID 작업을 설정하는 데 도움을 주기 위해 Azure AD B2C 포털은 사용자 흐름이라는 미리 정의되고 구성 가능한 정책을 포함합니다. 사용자 흐름을 사용하면 몇 번의 클릭만으로 몇 분 내에 훌륭한 사용자 환경을 만들 수 있습니다. [사용자 흐름을 사용해야 하는 경우와 사용자 지정 정책을 사용해야 하는 경우를 알아보세요](user-flow-overview.md#comparing-user-flows-and-custom-policies).|
| 앱 등록 | 보안이 설정된 모든 애플리케이션(웹, 네이티브) 및 API는 Azure AD B2C에 등록되어야 합니다. 앱에 iOS 및 Android의 웹 버전과 네이티브 버전이 모두 있는 경우 동일한 클라이언트 ID를 사용하여 Azure AD B2C에서 하나의 애플리케이션으로 등록할 수 있습니다. [OIDC, SAML, 웹 및 네이티브 앱을 등록](./tutorial-register-applications.md?tabs=applications)하는 방법을 알아보세요. [Azure AD B2C에서 사용할 수 있는 애플리케이션 유형에](./application-types.md)대해 자세히 알아보세요. |
| 월간 활성 사용자 청구로 이동 | Azure AD B2C가 월간 활성 인증에서 MAU(월간 활성 사용자) 청구로 이동했습니다. 대부분의 고객은 이 모델이 비용 효율적이라고 생각할 것입니다. [월간 활성 사용자 청구에 대해 자세히 알아보세요](https://azure.microsoft.com/updates/mau-billing/). |

## <a name="planning-and-design"></a>계획 및 설계

애플리케이션 및 서비스 아키텍처를 정의하고, 현재 시스템을 인벤토리하고, Azure AD B2C로의 마이그레이션을 계획합니다.

| 모범 사례 | 설명 |
|--|--|
| 엔드투엔드 솔루션 설계 | Azure AD B2C 통합을 계획할 때 모든 애플리케이션의 종속성을 포함합니다. 현재 환경에 있거나 솔루션에 추가해야 할 수 있는 모든 서비스 및 제품(예: Azure Functions, CRM(고객 관계 관리) 시스템, Azure API Management 게이트웨이 및 스토리지 서비스)을 고려합니다. 모든 서비스에 대한 보안 및 확장성을 고려합니다. |
| 사용자 환경 문서화 | 고객이 애플리케이션에서 경험할 수 있는 모든 사용자 경험에 대해 자세히 설명합니다. 애플리케이션의 ID 및 프로필 측면과 상호 작용할 때 발생할 수 있는 모든 화면 및 분기 흐름을 포함합니다. 계획에 유용성, 접근성 및 지역화를 포함합니다. |
| 올바른 인증 프로토콜 선택 |  다양한 애플리케이션 시나리오 및 권장 인증 흐름을 분석하려면 [시나리오 및 지원되는 인증 흐름](../active-directory/develop/authentication-flows-app-scenarios.md#scenarios-and-supported-authentication-flows)을 참조하세요. |
| POC(개념 증명) 엔드투엔드 사용자 환경 파일럿 | [Microsoft 코드 샘플](integrate-with-app-code-samples.md) 및 [커뮤니티 샘플](https://github.com/azure-ad-b2c/samples)로 시작합니다. |
| 마이그레이션 계획 만들기 |미리 계획하면 마이그레이션이 더욱 원활하게 진행될 수 있습니다. [사용자 마이그레이션](user-migration.md)에 대해 자세히 알아보세요.|
| 유용성 및 보안 | 솔루션은 애플리케이션 유용성과 조직의 허용 가능한 위험 수준 사이에서 적절한 균형을 이루어야 합니다. |
| 온-프레미스 종속성을 클라우드로 이동 | 복원력 있는 솔루션을 보장하려면 기존 애플리케이션 종속성을 클라우드로 이동하는 것을 고려합니다. |
| 기존 앱을 b2clogin.com으로 마이그레이션 | login.microsoftonline.com은 2020년 12월 4일부터 모든 Azure AD B2C 테넌트에서 사용이 중단됩니다. [자세히 알아봅니다](b2clogin.md). |
| ID 보호 및 조건부 액세스 사용 | 이러한 기능을 사용하면 위험한 인증 및 액세스 정책을 훨씬 효과적으로 제어할 수 있습니다. Azure AD B2C Premium P2가 필요합니다. [자세히 알아봅니다](conditional-access-identity-protection-overview.md). |

## <a name="implementation"></a>구현

구현 단계에서 다음 권장 사항을 고려합니다.

| 모범 사례 | 설명 |
|--|--|
| Visual Studio Code의 Azure AD B2C 확장으로 사용자 지정 정책 편집 | [Visual Studio Code Marketplace에서](https://marketplace.visualstudio.com/items?itemName=AzureADB2CTools.aadb2c) Visual Studio Code 및 이 커뮤니티에서 빌드된 확장을 다운로드합니다. 공식 Microsoft 제품은 아니지만 Visual Studio Code의 Azure AD B2C 확장에는 사용자 지정 정책 작업을 용이하게 할 수 있는 여러 기능이 포함되어 있습니다. |
| Azure AD B2C 문제를 해결하는 방법 알아보기 | 개발하는 동안 [사용자 지정 정책의 문제를 해결](./troubleshoot-custom-policies.md?tabs=applications)하는 방법을 알아봅니다. 정상적인 인증 흐름이 어떻게 표시되는지 알아보고 이상 문제 및 오류를 발견하기 위한 도구를 사용합니다. 예를 들어 [Application Insights](troubleshoot-with-application-insights.md)를 사용하여 사용자 경험의 출력 로그를 검토할 수 있습니다. |
| 입증된 사용자 지정 정책 패턴의 라이브러리 활용 | 몇 가지 향상된 Azure AD B2C CIAM(고객 ID 및 액세스 관리) 사용자 경험에 대한 [샘플](https://github.com/azure-ad-b2c/samples)을 찾습니다. |

## <a name="testing"></a>테스트

Azure AD B2C 구현을 테스트하고 자동화합니다.

| 모범 사례 | 설명 |
|--|--|
| 글로벌 트래픽 계정 | 다른 글로벌 주소의 트래픽 원본을 사용하여 성능 및 지역화 요구 사항을 테스트합니다. 모든 HTML, CSS 및 종속성이 성능 요구를 충족할 수 있는지 확인합니다. |
| 기능 및 UI 테스트 | 엔드투엔드 사용자 흐름을 테스트합니다. Selenium, VS 웹 테스트 등을 사용하여 몇 분마다 가상 테스트를 추가합니다. |
| 펜 테스트 | 솔루션과 함께 사용하기 전에 침투 테스트 연습을 수행하여 타사 종속성을 비롯한 모든 구성 요소가 안전한지 확인합니다. 액세스 토큰을 사용하여 API를 보호하고 애플리케이션 시나리오에 올바른 인증 프로토콜을 사용했는지 확인합니다. [침투 테스트](../security/fundamentals/pen-testing.md) 및 [Microsoft Cloud 통합 침투 테스트 시행 규칙](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)에 대해 자세히 알아보세요. |
| A/B 테스트 | 전체 모집단에 공개하기 전에 임의의 소수 사용자 집합과 함께 새로운 기능을 사용해 봅니다. Azure AD B2C에서 JavaScript를 사용하도록 설정하면 Optimizely, Clarity 등과 같은 A/B 테스트 도구와 통합할 수 있습니다. |
| 부하 테스트 | Azure AD B2C를 확장할 수 있지만 애플리케이션은 모든 종속성이 확장될 수 있는 경우에만 확장할 수 있습니다. API 및 CDN을 부하 테스트합니다. |
| 제한 |  Azure AD B2C는 단기간에 동일한 원본에서 너무 많은 요청을 보내는 경우 트래픽을 제한합니다. 부하 테스트 중에 여러 트래픽 원본을 사용하고 애플리케이션에서 `AADB2C90229` 오류 코드를 정상적으로 처리합니다. |
| Automation | CI/CD(연속 통합 및 지속적인 업데이트) 파이프라인을 사용하여 [Azure DevOps](deploy-custom-policies-devops.md)와 같은 테스트 및 배포를 자동화합니다. |

## <a name="operations"></a>작업

Azure AD B2C 환경을 관리합니다.

| 모범 사례 | 설명 |
|--|--|
| 여러 환경 만들기 | 작업 및 배포 공개가 용이하도록 개발, 테스트, 사전 프로덕션 및 프로덕션을 위한 별도의 환경을 만듭니다. 각각에 대해 Azure AD B2C 테넌트를 만듭니다. |
| 사용자 지정 정책에 대한 버전 제어 사용 | Azure AD B2C 사용자 지정 정책에 GitHub, Azure Repos 또는 다른 클라우드 기반 버전 제어 시스템을 사용하는 것을 고려합니다. |
| Microsoft Graph API를 사용하여 B2C 테넌트의 관리 자동화 | Microsoft Graph API:<br/>[Identity Experience Framework](/graph/api/resources/trustframeworkpolicy?preserve-view=true&view=graph-rest-beta) 관리(사용자 지정 정책)<br/>[키](/graph/api/resources/trustframeworkkeyset?preserve-view=true&view=graph-rest-beta)<br/>[사용자 흐름](/graph/api/resources/identityuserflow?preserve-view=true&view=graph-rest-beta) |
| Azure DevOps와 통합 | [CI/CD 파이프라인](deploy-custom-policies-devops.md)을 사용하면 서로 다른 환경 간에 코드를 쉽게 이동할 수 있으며 항상 프로덕션 준비 상태가 보장됩니다.   |
| Azure Monitor와 통합 | [감사 로그 이벤트](view-audit-logs.md)는 7일 동안만 유지됩니다. [Azure Monitor와 통합](azure-monitor.md)하면 로그를 장기간 사용하도록 보존할 수 있고, 타사 SIEM(보안 정보 및 이벤트 관리) 도구와 통합하여 환경에 대한 인사이트를 얻을 수도 있습니다. |
| 활성 경고 및 모니터링 설정 | Application Insights를 사용하여 Azure AD B2C에서 [사용자 동작을 추적](./analytics-with-application-insights.md)합니다. |

## <a name="support-and-status-updates"></a>지원 및 상태 업데이트

서비스의 상태를 최신으로 유지하고 지원 옵션을 찾아봅니다.

| 모범 사례 | 설명 |
|--|--|
| [서비스 업데이트](https://azure.microsoft.com/updates/?product=active-directory-b2c) |  Azure AD B2C 제품 업데이트 및 공지를 최신으로 유지합니다. |
| [Microsoft 지원](support-options.md) | Azure AD B2C 기술 문제에 대한 지원 요청을 제출합니다. 청구 및 구독 관리 지원은 무료로 제공됩니다. |
| [Azure 상태](https://status.azure.com/status) | 모든 Azure 서비스의 현재 상태를 봅니다. |