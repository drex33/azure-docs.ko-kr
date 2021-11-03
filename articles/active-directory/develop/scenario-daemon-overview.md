---
title: 웹 API를 호출하는 디먼 앱 빌드 | Azure
titleSuffix: Microsoft identity platform
description: 웹 API를 호출하는 디먼 앱을 빌드하는 방법에 대해 알아봅니다.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/14/2021
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 1a9aa754d09333b1384fe46e424deaf3c5525ff3
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131018056"
---
# <a name="scenario-daemon-application-that-calls-web-apis"></a>시나리오: 웹 API를 호출하는 디먼 애플리케이션

웹 API를 호출하는 디먼 애플리케이션을 빌드하는 데 필요한 모든 사항에 대해 알아봅니다.

## <a name="overview"></a>개요

애플리케이션은 해당 애플리케이션 대신(사용자 대신이 아님) 웹 API를 호출하는 토큰을 획득할 수 있습니다. 이 시나리오는 디먼 애플리케이션에 유용합니다. 이 파일은 표준 OAuth 2.0 [클라이언트 자격 증명](v2-oauth2-client-creds-grant-flow.md) 권한 부여를 사용합니다.

![디먼 앱](./media/scenario-daemon-app/daemon-app.svg)

다음은 디먼 앱 사용 사례의 몇 가지 예입니다.

- 사용자를 프로비저닝하거나, 관리하거나, 디렉터리에서 일괄 처리를 수행하는 데 사용되는 웹 애플리케이션
- 백그라운드에서 실행되는 배치 작업이나 운영 체제 서비스를 수행하는 데스크톱 애플리케이션(예: Windows의 Windows 서비스 또는 Linux의 디먼 프로세스)
- 특정 사용자가 아닌 디렉터리를 조작해야 하는 웹 API

비 디먼 애플리케이션이 클라이언트 자격 증명을 사용하는 또 다른 일반적인 경우는 사용자를 대신하여 동작하는 경우에도 기술적인 이유로 자체 ID를 사용해 웹 API 또는 리소스에 액세스해야 하는 경우입니다. 예를 들어 캐시를 위해 Azure Key Vault 또는 Azure SQL Database의 비밀에 액세스하는 것입니다.

고유 ID에 대한 토큰을 획득하는 애플리케이션:

- 기밀 클라이언트 애플리케이션입니다. 해당 앱은 사용자와 독립적으로 리소스에 액세스하는 경우 ID를 증명해야 하는 상당히 중요한 앱으로, Azure AD(Azure Active Directory) 테넌트 관리자의 승인이 필요합니다.
- Azure AD에 비밀(애플리케이션 암호 또는 인증서)을 등록했습니다. 이 비밀은 토큰을 가져오기 위해 Azure AD를 호출하는 동안 전달됩니다.

## <a name="specifics"></a>특수 적용 사항

사용자는 디먼 애플리케이션과 상호 작용할 수 없습니다. 디먼 애플리케이션에는 고유한 ID가 필요합니다. 해당 유형의 애플리케이션은 애플리케이션 ID를 사용하고 애플리케이션 ID, 자격 증명(암호 또는 인증서), Azure AD에 대한 애플리케이션 ID URI 등을 제시하여 액세스 토큰을 요청합니다. 성공적으로 인증되면 디먼은 Microsoft ID 플랫폼에서 액세스 토큰과 새로 고침 토큰을 받습니다. 이 토큰은 웹 API를 호출하는 데 사용되며, 필요에 따라 새로 고쳐집니다.

사용자가 디먼 애플리케이션과 상호 작용할 수 없으므로 증분 동의가 불가능합니다. 모든 필수 API 권한은 애플리케이션 등록 시 구성해야 합니다. 애플리케이션 코드는 정적으로 정의된 사용 권한만 요청합니다. 이는 디먼 애플리케이션이 증분 동의를 지원하지 않는다는 의미이기도 합니다.

개발자의 경우 이 시나리오에 대한 엔드투엔드 환경에는 다음과 같은 측면이 있습니다.

- 디먼 애플리케이션은 Azure AD 테넌트에서만 작동할 수 있습니다. Microsoft 개인 계정을 조작하려고 하는 디먼 애플리케이션을 빌드하는 작업은 의미가 없습니다. LOB(기간 업무) 앱 개발자인 경우 테넌트에서 디먼 앱을 만듭니다. ISV인 경우 다중 테넌트 디먼 애플리케이션을 만들 수 있습니다. 각 테넌트 관리자는 동의를 제공해야 합니다.
- [애플리케이션 등록](./scenario-daemon-app-registration.md) 중에는 회신 URI가 필요하지 않습니다. Azure AD와 비밀/인증서 또는 서명된 어설션을 공유합니다. 또한 애플리케이션 사용 권한을 요청하고 해당 앱 사용 권한을 사용하기 위한 관리자 동의를 부여해야 합니다.
- [애플리케이션 구성](./scenario-daemon-app-configuration.md) 시, 애플리케이션 등록 중에 Azure AD와 공유된 클라이언트 자격 증명을 제공해야 합니다.
- 클라이언트 자격 증명 흐름을 사용하여 토큰을 획득하는 데 사용되는 [범위](scenario-daemon-acquire-token.md#scopes-to-request)는 정적 범위여야 합니다.

## <a name="recommended-reading"></a>추천 자료

[!INCLUDE [recommended-topics](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="next-steps"></a>다음 단계

이 시나리오의 다음 문서인 [앱 등록](./scenario-daemon-app-registration.md)으로 이동합니다.
