---
title: 지원되는 계정 유형 | Azure
titleSuffix: Microsoft identity platform
description: 애플리케이션의 대상 그룹 및 지원되는 계정 유형에 대한 개념 설명서
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/14/2020
ms.author: jmprieur
ms.reviewer: saeeda
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: b72e911631bc15402cf504655426c63cd26a69d6
ms.sourcegitcommit: 1f29603291b885dc2812ef45aed026fbf9dedba0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/29/2021
ms.locfileid: "129231732"
---
# <a name="supported-account-types"></a>지원되는 계정 유형

이 문서에서는 Microsoft ID 플랫폼 애플리케이션에서 지원되는 계정 유형(*대상 그룹* 이라고도 함)에 대해 설명합니다.

<!-- This section can be in an include for many of the scenarios (SPA, web app signing-in users, protecting a web API, Desktop (depending on the flows), Mobile -->

## <a name="account-types-in-the-public-cloud"></a>공용 클라우드의 계정 유형

Microsoft Azure 퍼블릭 클라우드에서는 대부분의 앱 유형이 모든 대상 그룹을 사용하여 사용자를 로그인할 수 있습니다.

- LOB(기간 업무) 애플리케이션을 작성하는 경우 사용자를 조직 내에 로그인할 수 있습니다. 이러한 애플리케이션을 *단일 테넌트* 라고도 합니다.
- ISV인 경우 사용자를 로그인하는 애플리케이션을 작성할 수 있습니다.

  - 모든 조직. 이러한 애플리케이션을 *다중 테넌트* 웹 애플리케이션이라고 합니다. 회사 또는 학교 계정으로 사용자를 로그인한다는 내용을 종종 보게 될 것입니다.
  - 회사, 학교 또는 개인 Microsoft 계정을 사용합니다.
  - Microsoft 개인 계정만
    
- B2C(Business-To-Consumer) 애플리케이션을 작성하는 경우 Azure AD B2C(Azure Active Directory B2C)를 사용하여 소셜 ID로 로그인할 수도 있습니다.

## <a name="account-type-support-in-authentication-flows"></a>인증 흐름의 계정 유형 지원

일부 계정 유형은 특정 인증 흐름에 사용할 수 없습니다. 예를 들어 데스크톱, UWP 또는 디먼 애플리케이션은 다음과 같습니다.

- 디먼 애플리케이션은 Azure AD 조직에서만 사용 가능합니다. 디먼 애플리케이션을 사용하여 Microsoft 개인 계정을 조작할 수 없습니다. 관리자 동의가 절대 부여되지 않을 것입니다.
- 소속 조직 또는 타 조직의 회사 또는 학교 계정에 Windows 통합 인증 흐름만 사용할 수 있습니다. Windows 통합 인증은 도메인 계정과 함께 작동하며, 컴퓨터가 도메인에 가입되어 있거나 Azure AD 조인되어 있어야 합니다. 이 흐름에는 Microsoft 개인 계정이 적합하지 않습니다.
- [리소스 소유자 암호 자격 증명 부여](./v2-oauth-ropc.md)(사용자 이름/암호)는 Microsoft 개인 계정에 사용할 수 없습니다. 개인 Microsoft 계정을 사용하려면 사용자가 각 로그인 세션에서 개인 리소스에 대한 액세스에 동의해야 합니다. 따라서 이 동작은 비대화형 흐름과 호환되지 않습니다.

## <a name="account-types-in-national-clouds"></a>국가별 클라우드의 계정 유형

앱에서 사용자를 [국가별 클라우드](authentication-national-cloud.md)에 로그인할 수도 있습니다. 하지만 Microsoft 개인 계정은 국가별 클라우드에서 지원되지 않습니다. 이러한 이유로 국가별 클라우드에 지원되는 계정 유형이 소속 조직(단일 테넌트) 또는 모든 조직(다중 테넌트 애플리케이션)으로 축소됩니다.

## <a name="next-steps"></a>다음 단계

- [Azure Active Directory의 테넌시](./single-and-multi-tenant-apps.md)에 대해 알아봅니다.
- [국가별 클라우드](./authentication-national-cloud.md)에 대해 알아봅니다.
