---
title: 프로덕션을 위한 모바일 앱 호출 웹 API 준비 | Azure
titleSuffix: Microsoft identity platform
description: 웹 API를 호출하는 모바일 앱을 빌드하는 방법에 대해 알아봅니다. 프로덕션을 위해 앱을 준비합니다.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.reviewer: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 7756029d4b3ed43f55c86fa12128e1943c99b6bb
ms.sourcegitcommit: 28cd7097390c43a73b8e45a8b4f0f540f9123a6a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/24/2021
ms.locfileid: "122778389"
---
# <a name="prepare-mobile-apps-for-production"></a>프로덕션을 위해 모바일 앱 준비

이 문서에서는 모바일 앱을 프로덕션으로 이동하기 전에 모바일 앱의 품질 및 안정성을 개선하는 방법에 대한 세부 정보를 제공합니다.

## <a name="handle-errors"></a>오류 처리

프로덕션을 위해 모바일 앱을 준비할 때 몇 가지 오류 조건이 발생할 수 있습니다. 처리할 주요 사례는 자동 오류이며 상호 작용에 대한 대체입니다. 네트워크 연결이 없는 상황, 서비스 중단, 관리자 동의에 대한 요구 사항, 기타 시나리오 관련 사례 등을 고려해야 합니다.

각 MSAL(Microsoft 인증 라이브러리) 형식에 대해 오류 조건을 처리하는 방법을 설명하는 샘플 코드와 Wiki 콘텐츠를 찾을 수 있습니다.

- [MSAL Android Wiki](https://github.com/AzureAD/microsoft-authentication-library-for-android)
- [MSAL iOS Wiki](https://github.com/AzureAD/microsoft-authentication-library-for-objc/wiki)
- [MSAL.NET Wiki](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki)


[!INCLUDE [Common steps to move to production](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="next-steps"></a>다음 단계

추가 샘플을 시도하려면 [모바일 퍼블릭 클라이언트 애플리케이션](sample-v2-code.md#mobile)을 참조하세요.