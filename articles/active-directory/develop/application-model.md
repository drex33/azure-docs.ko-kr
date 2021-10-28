---
title: 애플리케이션 모델 | Azure
titleSuffix: Microsoft identity platform
description: Microsoft ID 플랫폼과 통합될 수 있도록 애플리케이션을 등록하는 프로세스에 대해 알아봅니다.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/27/2021
ms.author: ryanwi
ms.reviewer: jmprieur, saeeda, sureshja, hirsin
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started
ms.openlocfilehash: 5b2f62b0adc040b5f20b341457789378f1aa983a
ms.sourcegitcommit: df2a8281cfdec8e042959339ebe314a0714cdd5e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/28/2021
ms.locfileid: "129154169"
---
# <a name="application-model"></a>애플리케이션 모델

애플리케이션은 자체적으로 로그인하거나 ID 공급자에 로그인을 위임할 수 있습니다. 이 문서에서는 Microsoft ID 플랫폼에 애플리케이션을 등록하는 데 필요한 단계를 설명합니다.

## <a name="register-an-application"></a>애플리케이션 등록

ID 공급자가 사용자에게 특정 앱에 대한 액세스 권한이 있음을 알고 있는 경우 사용자와 애플리케이션을 모두 ID 공급자에 등록해야 합니다. Azure AD(Azure Active Directory)를 사용하여 애플리케이션을 등록하는 경우 Microsoft ID 플랫폼과 통합될 수 있도록 애플리케이션에 대한 ID 구성을 제공하는 것입니다. 앱을 등록하면 다음 작업도 수행할 수 있습니다.

* 로그인 대화 상자에서 애플리케이션의 브랜딩을 사용자 지정합니다. 이 브랜딩은 사용자가 앱을 처음 사용하는 환경이 로그인이기 때문에 중요합니다.
* 사용자가 조직에 속한 경우에만 로그인할 수 있도록 허용할지 여부를 결정합니다. 이 아키텍처를 단일 테넌트 애플리케이션이라고 합니다. 또는 회사 또는 학교 계정을 사용하여 로그인하도록 허용할 수 있습니다(사용자가 다중 테넌트 애플리케이션이라고 함). 또한 LinkedIn, Google 등에서 개인 Microsoft 계정이나 소셜 계정도 허용할 수 있습니다.
* 범위 권한을 요청합니다. 예를 들어 로그인한 사용자의 프로필을 읽을 수 있는 권한을 부여하는 "user.read" 범위를 요청할 수 있습니다.
* 웹 API에 대한 액세스를 정의하는 범위를 정의합니다. 일반적으로 앱이 API에 액세스하려는 경우 사용자가 정의하는 범위에 대한 권한을 요청해야 합니다.
* 앱의 ID를 증명하는 Microsoft ID 플랫폼과 비밀을 공유합니다. 비밀을 사용하는 것은 앱이 기밀 클라이언트 애플리케이션인 경우와 관련이 있습니다. 기밀 클라이언트 애플리케이션은 자격 증명을 안전하게 보유할 수 있는 애플리케이션입니다. 자격 증명을 저장하려면 신뢰할 수 있는 백 엔드 서버가 필요합니다.

앱이 등록되면 토큰을 요청할 때 Microsoft ID 플랫폼과 공유하는 고유한 식별자가 제공됩니다. 앱이 [기밀 클라이언트 애플리케이션](developer-glossary.md#client-application)인 경우 인증서 또는 비밀의 사용 여부에 따라 비밀 또는 공개 키도 공유합니다.

Microsoft ID 플랫폼은 다음과 같은 두 가지 주요 기능을 수행하는 모델을 사용하여 애플리케이션을 나타냅니다.

* 지원하는 인증 프로토콜을 통해 앱을 식별합니다.
* 인증에 필요한 모든 식별자, URL, 암호 및 관련 정보를 제공합니다.

Microsoft ID 플랫폼:

* 런타임 시 인증을 지원하는 데 필요한 모든 데이터를 보유합니다.
* 앱에서 액세스해야 하는 리소스를 결정하는 데 필요하고 지정된 요청을 수행해야 하는 상황의 모든 데이터를 보유합니다.
* 앱 개발자 테넌트 및 다른 Azure AD 테넌트 내에서 앱 프로비저닝을 구현하기 위한 인프라를 제공합니다.
* 토큰 요청 시 사용자 동의를 처리하고 테넌트 간에 앱을 동적으로 프로비저닝합니다.

*동의* 는 클라이언트 애플리케이션에 리소스 소유자를 대신하여 보호된 리소스에 액세스할 수 있는 특정 권한을 부여하는 리소스 소유자의 프로세스입니다. Microsoft ID 플랫폼에서 다음을 수행할 수 있습니다.

* 앱이 대신 리소스에 액세스하는 것을 사용자 및 관리자가 동적으로 권한을 부여하거나 동의를 거부합니다.
* 관리자가 궁극적으로 앱이 수행할 수 있는 작업, 특정 앱을 사용할 수 있는 사용자 및 디렉터리 리소스에 액세스하는 방식을 결정합니다.

## <a name="multi-tenant-apps"></a>다중 테넌트 앱

Microsoft ID 플랫폼에서 [애플리케이션 개체](developer-glossary.md#application-object)는 애플리케이션에 대해 설명합니다. 배포 시 Microsoft ID 플랫폼은 디렉터리 또는 테넌트 내에서 애플리케이션의 구체적인 인스턴스를 나타내는 [서비스 주체](developer-glossary.md#service-principal-object)를 만들기 위한 청사진으로 애플리케이션 개체를 사용합니다. 서비스 주체가 특정 대상 디렉터리에서 앱이 실제로 수행할 수 있는 작업, 앱을 사용할 수 있는 대상, 앱이 액세스할 수 있는 리소스 등을 정의합니다. Microsoft ID 플랫폼은 [동의](developer-glossary.md#consent)를 통해 애플리케이션 개체에서 서비스 주체를 만듭니다.

다음 다이어그램에는 동의를 통해 구동되는 간소화된 Microsoft ID 플랫폼 프로비저닝 흐름이 나와 있습니다. 두 개의 테넌트 *A* 와 *B* 가 표시됩니다.

* *테넌트 A* 는 애플리케이션을 소유합니다.
* *테넌트 B* 는 서비스 주체를 통해 애플리케이션을 인스턴스화합니다.

![동의로 구동되는 단순화된 프로비저닝 흐름을 보여 주는 다이어그램입니다.](./media/authentication-scenarios/simplified-provisioning-flow-consent-driven.svg)

이 프로비전 흐름에서

1. 테넌트 B의 사용자가 앱에 로그인하려고 합니다. 권한 부여 엔드포인트는 애플리케이션에 대한 토큰을 요청합니다.
1. 인증에 필요한 사용자 자격 증명을 획득하여 확인합니다.
1. 테넌트 B에 대한 액세스 권한을 얻으려면 앱에 동의하라는 메시지가 사용자에게 표시됩니다.
1. Microsoft ID 플랫폼은 테넌트 A의 애플리케이션 개체를 테넌트 B의 서비스 주체를 만들기 위한 청사진으로 사용합니다.
1. 사용자가 요청된 토큰을 수신합니다.

더 많은 테넌트에 대해 이 프로세스를 반복할 수 있습니다. 테넌트 A에서 앱(애플리케이션 개체)에 대한 청사진을 유지합니다. 앱에 동의한 다른 모든 테넌트의 사용자 및 관리자는 각 테넌트에서 해당하는 서비스 주체 개체를 통해 애플리케이션이 수행할 수 있는 작업에 대한 컨트롤을 유지합니다. 자세한 내용은 [Microsoft ID 플랫폼의 애플리케이션 및 서비스 주체 개체](app-objects-and-service-principals.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

Microsoft ID 플랫폼의 인증 및 권한 부여에 대한 자세한 내용은 다음 문서를 참조하세요.

* 인증 및 권한 부여의 기본 개념에 대한 자세한 내용은 [인증 및 권한 부여](authentication-vs-authorization.md)를 참조하세요.
* 인증 및 권한 부여에 액세스 토큰, 새로 고침 토큰 및 ID 토큰을 사용하는 방법에 대한 자세한 내용은 [보안 토큰](security-tokens.md)을 참조하세요.
* 웹, 데스크톱 및 모바일 앱의 로그인 흐름에 대해 알아보려면 [앱 로그인 흐름](app-sign-in-flow.md)을 참조하세요.

애플리케이션 모델에 대한 자세한 내용은 다음 문서를 참조하세요.

* Microsoft ID 플랫폼의 애플리케이션 개체 및 서비스 주체에 대한 자세한 내용은 [Azure AD에 애플리케이션을 추가하는 방법 및 이유](active-directory-how-applications-are-added.md)를 참조하세요.
* 단일 테넌트 앱 및 다중 테넌트 앱에 대한 자세한 내용은 [Azure Active Directory의 테넌시](single-and-multi-tenant-apps.md)를 참조하세요.
* 조직에서 사용자(주로 고객)가 로그인할 수 있도록 Azure AD에서 Azure Active Directory B2C를 제공하는 방법(일반적으로 Google 계정과 같은 소셜 ID 사용)에 대한 자세한 내용은 [Azure Active Directory B2C 설명서](../../active-directory-b2c/index.yml)를 참조하세요.
