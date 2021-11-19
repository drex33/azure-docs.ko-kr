---
title: 의료 API FHIR 서비스에 대한 Azure Active Directory ID 구성
description: FHIR 서비스에 대한 ID, 인증 및 권한 부여 원칙 알아보기
services: healthcare-apis
author: caitlinv39
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 08/06/2019
ms.author: cavoeg
ms.openlocfilehash: 43d206c30bd4a3ee043dab3d96247c010f4cdc92
ms.sourcegitcommit: 81a1d2f927cf78e82557a85c7efdf17bf07aa642
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/19/2021
ms.locfileid: "132814645"
---
# <a name="azure-active-directory-identity-configuration-for-fhir-service"></a>FHIR 서비스에 대한 ID 구성 Azure Active Directory

> [!IMPORTANT]
> Azure Healthcare API는 현재 미리 보기로 제공됩니다. [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)에는 베타 또는 미리 보기로 제공되거나 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 추가 약관이 포함되어 있습니다.

의료 데이터로 작업하는 경우 데이터를 안전하게 보호하고 권한이 없는 사용자 또는 애플리케이션이 데이터에 액세스할 수 없도록 하는 것이 중요합니다. FHIR 서버는 이 데이터 보안을 위해 [OAuth 2.0](https://oauth.net/2/)을 사용합니다. Azure Healthcare API의 FHIR 서비스(여기서는 FHIR 서비스라고 함)는 OAuth 2.0 ID 공급자의 예인 [Azure Active Directory](../../active-directory/index.yml)를 사용하여 보호됩니다. 이 문서에서는 FHIR 서버 권한 부여에 대한 개요와 FHIR 서버에 액세스하기 위한 토큰을 가져오는 데 필요한 단계를 제공합니다. 이러한 단계는 모든 FHIR 서버 및 ID 공급자에 적용되지만 이 문서에서는 FHIR 서비스 및 azure AD(Azure Active Directory)를 ID 공급자로 안내합니다.

## <a name="access-control-overview"></a>액세스 제어 개요

클라이언트 애플리케이션이 FHIR 서비스에 액세스하려면 액세스 토큰을 제공해야 합니다. 액세스 토큰은 클라이언트에 부여된 클라이언트의 ID 및 역할 및 권한에 대한 정보를 전달하는 서명된 [Base64로](https://en.wikipedia.org/wiki/Base64) 인코딩된 속성(클레임) 컬렉션입니다.

토큰을 가져오는 방법에는 여러 가지가 있지만 FHIR 서비스는 올바른 클레임을 사용하여 적절하게 서명된 토큰인 경우 토큰을 가져오는 방법을 신경 쓰지 않습니다. 

예를 들어 [권한 부여 코드 흐름을](../../active-directory/azuread-dev/v1-protocols-oauth-code.md) 사용하여 FHIR 서버에 액세스하는 것은 아래 네 단계를 수행합니다.

![FHIR 권한 부여](media/azure-active-directory-fhir-service/fhir-authorization.png)

1. 클라이언트는 `/authorize` Azure AD의 엔드포인트에 요청을 보냅니다. Azure AD는 사용자가 적절한 자격 증명(예: 사용자 이름 및 암호 또는 2단계 인증)을 사용하여 인증하는 로그인 페이지로 클라이언트를 리디렉션합니다. [권한 부여 코드 를 가져오는 자세한](../../active-directory/azuread-dev/v1-protocols-oauth-code.md#request-an-authorization-code)내용을 참조하세요. 인증에 성공하면 *권한 부여 코드가* 클라이언트에 반환됩니다. Azure AD는 이 권한 부여 코드만 클라이언트 애플리케이션 등록에 구성된 등록된 회신 URL로 반환되도록 허용합니다(아래 참조).
1. 클라이언트 애플리케이션은 Azure AD의 엔드포인트에서 *액세스 토큰에* 대한 권한 부여 코드를 `/token` 교환합니다. 토큰을 요청할 때 클라이언트 애플리케이션은 클라이언트 암호(애플리케이션 암호)를 제공해야 할 수 있습니다. [액세스 토큰을 가져오는 방법을](../../active-directory/azuread-dev/v1-protocols-oauth-code.md#use-the-authorization-code-to-request-an-access-token)자세히 참조하세요.
1. 클라이언트는 FHIR 서비스에 대한 요청을 `GET /Patient` 만듭니다(예: 모든 환자 검색). 요청을 만들 때 HTTP 요청 헤더에 액세스 토큰이 포함됩니다(예: `Authorization: Bearer eyJ0e...` ). 여기서 `eyJ0e...` 은 Base64로 인코딩된 액세스 토큰을 나타냅니다.
1. FHIR 서비스는 토큰에 적절한 클레임(토큰의 속성)이 포함되어 있음을 확인합니다. 모든 것이 체크 아웃되면 요청을 완료하고 결과가 있는 FHIR 번들을 클라이언트에 반환합니다.

FHIR 서비스는 사용자 자격 증명의 유효성 검사와 관련이 없으며 토큰을 발급하지 않는다는 점에 유의해야 합니다. 인증 및 토큰 만들기는 Azure AD에서 수행됩니다. FHIR 서비스는 토큰이 올바르게 서명되고(인증됨) 적절한 클레임이 있는지 유효성을 검사합니다.

## <a name="structure-of-an-access-token"></a>액세스 토큰의 구조

FHIR 애플리케이션 개발에는 종종 액세스 문제 디버깅이 포함됩니다. 클라이언트가 FHIR 서비스에 대한 액세스가 거부된 경우 액세스 토큰의 구조와 토큰의 콘텐츠(클레임)를 검사하기 위해 디코딩할 수 있는 방법을 이해하는 것이 유용합니다. 

FHIR 서버는 일반적으로 [](https://en.wikipedia.org/wiki/JSON_Web_Token) JSON Web Token(JWT, "jot"로 발음)를 예상합니다. 다음 세 부분으로 구성됩니다.

**1부:** 다음과 같은 헤더입니다.
```json
    {
      "alg": "HS256",
      "typ": "JWT"
    }
```

**2부:** 페이로드(클레임) 예를 들면 다음과 같습니다.
```json
    {
     "oid": "123",
     "iss": "https://issuerurl",
     "iat": 1422779638,
     "roles": [
        "admin"
      ]
    }
```

**3부:** 헤더의 Base64로 인코딩된 내용과 페이로드를 결합하고 헤더에 지정된 알고리즘( )에 따라 암호화 해시를 계산하여 `alg` 계산되는 서명입니다. 서버는 ID 공급자로부터 공개 키를 가져오고 이 토큰이 특정 ID 공급자에 의해 발급되었으며 변조되지 않았는지 확인할 수 있습니다.

전체 토큰은 이러한 세 세그먼트의 Base64 인코딩(실제로 Base64 URL 인코딩) 버전으로 구성됩니다. 세 세그먼트는 (점)으로 결합되고 `.` 구분됩니다.

예제 토큰은 다음과 같습니다.

```
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJvaWQiOiIxMjMiLCAiaXNzIjoiaHR0cHM6Ly9pc3N1ZXJ1cmwiLCJpYXQiOjE0MjI3Nzk2MzgsInJvbGVzIjpbImFkbWluIl19.gzSraSYS8EXBxLN_oWnFSRgCzcmJmMjLiuyu5CSpyHI
```

와 같은 도구를 사용하여 토큰을 디코딩하고 검사할 수 [https://jwt.ms](https://jwt.ms) 있습니다. 토큰을 디코딩한 결과는 다음과 같습니다.

```json
{
  "alg": "HS256",
  "typ": "JWT"
}.{
  "oid": "123",
  "iss": "https://issuerurl",
  "iat": 1422779638,
  "roles": [
    "admin"
  ]
}.[Signature]
```

## <a name="obtaining-an-access-token"></a>액세스 토큰 가져오기

위에서 설명한 대로 Azure AD에서 토큰을 가져오는 방법에는 여러 가지가 있습니다. [Azure AD 개발자 설명서](../../active-directory/develop/index.yml)에 자세히 설명되어 있습니다.

Azure AD에는 및 라고 하는 두 가지 버전의 OAuth 2.0 엔드포인트가 `v1.0` `v2.0` 있습니다. 이러한 두 버전은 모두 OAuth 2.0 엔드포인트이며 `v1.0` 및 `v2.0` 지정은 Azure AD에서 해당 표준을 구현하는 방법의 차이점을 나타냅니다. 

FHIR 서버를 사용하는 경우 또는 엔드포인트를 사용할 수 `v1.0` `v2.0` 있습니다. 선택 항목은 클라이언트 애플리케이션에서 사용하는 인증 라이브러리에 따라 달라질 수 있습니다.

Azure AD 설명서의 관련 섹션은 다음과 같습니다.

* `v1.0` 엔드포인트:
    * [인증 코드 흐름](../../active-directory/azuread-dev/v1-protocols-oauth-code.md).
    * [클라이언트 자격 증명 흐름.](../../active-directory/azuread-dev/v1-oauth2-client-creds-grant-flow.md)
* `v2.0` 엔드포인트:
    * [인증 코드 흐름](../../active-directory/develop/v2-oauth2-auth-code-flow.md).
    * [클라이언트 자격 증명 흐름.](../../active-directory/develop/v2-oauth2-client-creds-grant-flow.md)

토큰을 얻기 위한 다른 변형(예: 흐름 대신)이 있습니다. 자세한 내용은 Azure AD 설명서를 참조하세요. FHIR 서비스를 사용하는 경우 [Azure CLI 사용하여](get-healthcare-apis-access-token-cli.md)액세스 토큰(디버깅용)을 가져오는 몇 가지 바로 가기도 있습니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 Azure AD를 사용하여 FHIR 서비스에 대한 액세스를 보안하는 데 관련된 몇 가지 기본 개념을 배웠습니다. FHIR 서비스를 배포하는 방법에 대한 자세한 내용은 다음을 참조하세요.

>[!div class="nextstepaction"]
>[FHIR 서비스 배포](fhir-portal-quickstart.md)
