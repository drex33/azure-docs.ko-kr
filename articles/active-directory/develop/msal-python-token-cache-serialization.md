---
title: 사용자 지정 토큰 캐시 serialization(MSAL Python) | Azure
titleSuffix: Microsoft identity platform
description: Python용 MSAL에 대한 토큰 캐시를 직렬화하는 방법 알아보기
services: active-directory
author: rayluo
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 11/13/2019
ms.author: rayluo
ms.reviewer: nacanuma
ms.custom: aaddev, devx-track-python
ms.openlocfilehash: bbe91d7a25e516766939ba56a9d9cfafff4bf0b5
ms.sourcegitcommit: 1f29603291b885dc2812ef45aed026fbf9dedba0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/29/2021
ms.locfileid: "129229908"
---
# <a name="custom-token-cache-serialization-in-msal-for-python"></a>Python용 MSAL의 사용자 지정 토큰 캐시 serialization

MSAL Python에서는 [ClientApplication](https://msal-python.readthedocs.io/en/latest/#confidentialclientapplication) 인스턴스를 만들 때 앱 세션 기간 동안 지속되는 메모리 내 토큰 캐시가 기본적으로 제공됩니다.

앱의 다른 세션이 액세스할 수 있도록 토큰 캐시의 serialization은 “기본 제공”되지 않습니다. MSAL Python은 웹앱과 같이 파일 시스템에 액세스할 수 없는 앱 유형에서 사용할 수 있기 때문입니다. MSAL Python 앱에 영구 토큰 캐시를 포함하려면 사용자 지정 토큰 캐시 serialization을 제공해야 합니다.

토큰 캐시를 직렬화하는 전략은 퍼블릭 클라이언트 애플리케이션(데스크톱)을 작성하는지 아니면 기밀 클라이언트 애플리케이션(웹앱, 웹 API 또는 디먼 앱)을 작성하는지에 따라 달라집니다.

## <a name="token-cache-for-a-public-client-application"></a>퍼블릭 클라이언트 애플리케이션의 토큰 캐시

퍼블릭 클라이언트 애플리케이션은 사용자의 디바이스에서 실행되고 단일 사용자의 토큰을 관리합니다. 이 경우 전체 캐시를 파일로 직렬화할 수 있습니다. 사용 중인 앱 또는 다른 앱이 캐시에 동시에 액세스할 수 있는 경우 파일 잠금을 제공해야 합니다. 잠금 없이 파일에 토큰 캐시를 직렬화하는 방법의 간단한 예제는 [SerializableTokenCache](https://msal-python.readthedocs.io/en/latest/#msal.SerializableTokenCache) 클래스 참조 설명서의 예제를 참조하세요.

## <a name="token-cache-for-a-web-app-confidential-client-application"></a>웹앱의 토큰 캐시(기밀 클라이언트 애플리케이션)

웹앱 또는 웹 API의 경우 세션, Redis 캐시 또는 데이터베이스를 사용하여 토큰 캐시를 저장할 수 있습니다. 사용자별로 하나의 토큰 캐시가 있어야 하므로 계정별로 토큰 캐시를 직렬화해야 합니다.

## <a name="next-steps"></a>다음 단계

Windows 또는 Linux 웹앱 또는 웹 API에 대해 토큰 캐시를 사용하는 방법의 예제는 [ms-identity-python-webapp](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/0.3.0/app.py#L66-L74)을 참조하세요. 예제는 Microsoft Graph API를 호출하는 웹앱에 관련됩니다.
