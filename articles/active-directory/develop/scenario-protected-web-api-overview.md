---
title: 보호된 웹 API - 개요
titleSuffix: Microsoft identity platform
description: 보호된 웹 API를 빌드하는 방법을 알아봅니다(개요).
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/26/2021
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: d34d8a361e22f25e82daf0576e65e35af192bdcd
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131462768"
---
# <a name="scenario-protected-web-api"></a>시나리오: 보호된 웹 API

이 시나리오에서는 웹 API를 노출하는 방법을 알아봅니다. 또한 인증된 사용자만 액세스할 수 있도록 웹 API를 보호하는 방법을 알아봅니다.

웹 API를 사용하려면 직장 및 학교 계정 둘 다에서 인증된 사용자를 사용하도록 설정하거나 Microsoft 개인 계정을 사용하도록 설정합니다.

## <a name="specifics"></a>특수 적용 사항

웹 API를 보호하기 위해 알아야 할 구체적인 정보는 다음과 같습니다.

- 앱 등록은 하나 이상의 _범위_ 또는 _애플리케이션 역할_ 을 노출해야 합니다.
  - 범위는 사용자를 대신하여 호출되는 웹 API에 의해 노출됩니다.
  - 애플리케이션 역할은 디먼 애플리케이션에서 호출하는 웹 API에 의해 노출됩니다(자체 웹 API를 호출함).
- 새 웹 API 앱 등록을 만드는 경우 웹 API에서 허용하는 [액세스 토큰 버전](reference-app-manifest.md#accesstokenacceptedversion-attribute)을 `2`로 선택합니다. 레거시 웹 API의 경우 허용되는 토큰 버전은 `null`일 수 있지만 이 값은 로그인 대상을 조직으로만 제한하며 개인 MSA(Microsoft 계정)는 지원되지 않습니다.
- 웹 API에 대한 코드 구성은 웹 API를 호출할 때 사용되는 토큰의 유효성을 검사해야 합니다.
- 컨트롤러 작업의 코드는 토큰의 역할이나 범위의 유효성을 검사해야 합니다.

## <a name="recommended-reading"></a>추천 자료

[!INCLUDE [recommended-topics](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="next-steps"></a>다음 단계

이 시나리오의 다음 문서인 [앱 등록](scenario-protected-web-api-app-registration.md)으로 이동합니다.
