---
title: Azure API for FHIR에 대한 Azure Active Directory 앱 등록
description: 이 자습서에서는 Azure API for FHIR 및 FHIR Server for Azure에 등록해야 하는 애플리케이션을 설명합니다.
services: healthcare-apis
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: overview
ms.reviewer: dseven
ms.author: cavoeg
author: matjazl
ms.date: 10/13/2019
ms.openlocfilehash: d170c7e081d7e176fa1a0b17dd16c8c1e90867c8
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "121787932"
---
# <a name="register-the-azure-active-directory-apps-for-azure-api-for-fhir"></a>Azure API for FHIR에 대한 Azure Active Directory 앱 등록

Azure API for FHIR 또는 FHIR Server for Azure(OSS)를 설정할 때 선택할 수 있는 몇 가지 구성 옵션이 있습니다. 오픈 소스의 경우 사용자 고유의 리소스 애플리케이션 등록을 만들어야 합니다. Azure API for FHIR의 경우 이 리소스 애플리케이션은 자동으로 생성됩니다.

## <a name="application-registrations"></a>애플리케이션 등록

애플리케이션이 Azure AD와 상호 작용하려면 등록해야 합니다. FHIR 서버의 컨텍스트에서는 다음과 같은 두 가지 종류의 애플리케이션 등록을 설명합니다.

1. 리소스 애플리케이션 등록
1. 클라이언트 애플리케이션 등록

**리소스 애플리케이션** 은 API의 Azure AD 또는 Azure AD로 보안되는 리소스의 표현이며, 특히 Azure API for FHIR이 있습니다. Azure API for FHIR에 대한 리소스 애플리케이션은 서비스를 프로비저닝할 때 자동으로 생성되지만, 오픈 소스 서버를 사용하는 경우 Azure AD에서 [리소스 애플리케이션을 등록](register-resource-azure-ad-client-app.md)해야 합니다. 이 리소스 애플리케이션에는 식별자 URI가 있습니다. 이 URI는 FHIR 서버의 URI와 동일하게 하는 것이 좋습니다. 이 URI는 FHIR 서버에 대한 `Audience`로 사용되어야 합니다. 클라이언트 애플리케이션은 토큰을 요청할 때 이 FHIR 서버에 대한 액세스를 요청할 수 있습니다.

*클라이언트 애플리케이션* 은 토큰을 요청하는 클라이언트의 등록입니다. 일반적으로 OAuth 2.0에서는 다음과 같은 세 가지 유형의 애플리케이션을 구분합니다.

1. **기밀 클라이언트**. Azure AD에서 웹앱이라고도 합니다. 기밀 클라이언트는 [권한 부여 코드 흐름](../../active-directory/azuread-dev/v1-protocols-oauth-code.md)을 사용하여 유효한 자격 증명을 제시하는 로그인한 사용자를 대신하여 토큰을 가져오는 애플리케이션입니다. 비밀을 보유할 수 있고 토큰에 대한 인증 코드를 교환할 때 Azure AD에 이 비밀을 제공하기 때문에 기밀 클라이언트라고 합니다. 기밀 클라이언트는 클라이언트 암호를 사용하여 본인 인증을 할 수 있으므로, 퍼블릭 클라이언트보다 신뢰도가 높고 기한이 더 긴 토큰을 가질 수 있으며 새로 고침 토큰을 부여받을 수 있습니다. [기밀 클라이언트를 등록](register-confidential-azure-ad-client-app.md)하는 방법에서 자세한 내용을 알아보세요. 클라이언트에서 권한 부여 코드를 수신하는 회신 URL을 등록하는 것이 중요합니다.
1. **클라이언트**. 비밀을 유지할 수 없는 클라이언트입니다. 일반적으로 사용자가 클라이언트의 비밀을 검색할 수 있는 모바일 디바이스 애플리케이션 또는 단일 페이지 JavaScript 애플리케이션이 있습니다. 또한 퍼블릭 클라이언트는 권한 부여 코드 흐름을 사용하지만, 토큰을 가져올 때 비밀을 제공할 수 없으며 토큰 기한이 더 짧거나 새로 고침 토큰이 없을 수도 있습니다. [퍼블릭 클라이언트를 등록](register-public-azure-ad-client-app.md)하는 방법에서 자세한 내용을 알아보세요.
1. 서비스 클라이언트. 이러한 클라이언트는 [클라이언트 자격 증명 흐름](../../active-directory/azuread-dev/v1-oauth2-client-creds-grant-flow.md)을 사용하여 본인(사용자 대신이 아님)을 대신하여 토큰을 가져옵니다. 일반적으로 비 대화형 방법으로 FHIR 서버에 액세스하는 애플리케이션을 나타냅니다. 예를 들면 수집 프로세스가 있습니다. 서비스 클라이언트를 사용하는 경우 `/authorize` 엔드포인트에 대한 호출을 사용하여 토큰을 가져오는 프로세스를 시작할 필요가 없습니다. 서비스 클라이언트는 `/token` 엔드포인트로 바로 이동하고 클라이언트 ID 및 클라이언트 암호를 제공하여 토큰을 가져올 수 있습니다. [서비스 클라이언트를 등록](register-service-azure-ad-client-app.md)하는 방법에서 자세한 내용을 알아보세요.

## <a name="next-steps"></a>다음 단계

이 개요에서는 FHIR API를 사용하는 데 필요한 애플리케이션 등록 유형을 살펴보았습니다.

설정에 따라 애플리케이션을 등록하는 방법 가이드를 참조하세요.

* [리소스 애플리케이션 등록](register-resource-azure-ad-client-app.md)
* [기밀 클라이언트 애플리케이션 등록](register-confidential-azure-ad-client-app.md)
* [퍼블릭 클라이언트 애플리케이션 등록](register-public-azure-ad-client-app.md)
* [서비스 애플리케이션 등록](register-service-azure-ad-client-app.md)

애플리케이션을 등록한 후에는 Azure API for FHIR을 배포할 수 있습니다.

>[!div class="nextstepaction"]
>[Azure API for FHIR 배포](fhir-paas-powershell-quickstart.md)