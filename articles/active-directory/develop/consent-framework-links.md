---
title: 애플리케이션 동의 작동 원리
description: Azure AD 동의 프레임워크 작동 원리를 이해하여 Azure AD에서 애플리케이션을 개발할 때 이러한 프레임워크를 사용하는 방법 알아보기
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev
ms.workload: identity
ms.topic: conceptual
ms.date: 09/27/2021
ms.author: ryanwi
ROBOTS: NOINDEX
ms.openlocfilehash: 2b68be9da11bc1f868b6bf30d3794ee6a3bc9ab6
ms.sourcegitcommit: df2a8281cfdec8e042959339ebe314a0714cdd5e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/28/2021
ms.locfileid: "129154207"
---
# <a name="how-application-consent-works"></a>애플리케이션 동의 작동 원리

이 문서는 Azure AD 동의 프레임워크의 작동 방식을 이해하여 애플리케이션을 보다 효과적으로 개발할 수 있도록 지원하기 위한 것입니다.

## <a name="recommended-documents"></a>권장되는 문서

- [동의를 통해 리소스 소유자가 리소스에 대한 애플리케이션 액세스를 제어하는 방법](./developer-glossary.md#consent) 이해
- [Azure AD 동의 프레임워크가 동의를 구현하는 방법](./quickstart-register-app.md)에 대한 단계별 개요 확인
- 좀 더 깊이 있는 이해를 위해 [다중 테넌트 애플리케이션이 동의 프레임워크를 사용하여](./howto-convert-app-to-be-multi-tenant.md) "user" 및 "admin" 동의를 구현하고 좀 더 수준 높은 다중 계층 애플리케이션 패턴을 지원하는 방법 알아보기
- 좀 더 깊이 있는 이해를 위해 [인증 코드 권한 부여 흐름 동안 OAuth 2.0 프로토콜 계층에서 동의가 지원되는 방식](../azuread-dev/v1-protocols-oauth-code.md#request-an-authorization-code) 알아보기

## <a name="next-steps"></a>다음 단계
[AzureAD Microsoft Q&A](/answers/topics/azure-active-directory.html)
