---
title: 의료 api에 대 한 Azure Active Directory id 구성 서비스
description: FHIR 서비스에 대 한 id, 인증 및 권한 부여 원칙 알아보기
services: healthcare-apis
author: caitlinv39
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 08/06/2019
ms.author: cavoeg
ms.openlocfilehash: 4571d8f1183cd2aa56568d2e5c4f83abb0f7f5dc
ms.sourcegitcommit: 28cd7097390c43a73b8e45a8b4f0f540f9123a6a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/24/2021
ms.locfileid: "122779348"
---
# <a name="azure-active-directory-identity-configuration-for-fhir-service"></a>fhir 서비스에 대 한 Azure Active Directory id 구성

> [!IMPORTANT]
> Azure 의료 Api는 현재 미리 보기로 제공 됩니다. [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)에는 베타 또는 미리 보기로 제공되거나 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 추가 약관이 포함되어 있습니다.

의료 데이터를 사용 하는 경우 데이터를 안전 하 게 보호 하 고 권한이 없는 사용자 또는 응용 프로그램에서 액세스할 수 없도록 하는 것이 중요 합니다. FHIR 서버는 이 데이터 보안을 위해 [OAuth 2.0](https://oauth.net/2/)을 사용합니다. Azure 의료 api의 fhir 서비스 (fhir 서비스 라고 함)는 OAuth 2.0 id 공급자의 예로 [Azure Active Directory](../../active-directory/index.yml)를 사용 하 여 보안이 유지 됩니다. 이 문서에서는 FHIR 서버 권한 부여에 대한 개요와 FHIR 서버에 액세스하기 위한 토큰을 가져오는 데 필요한 단계를 제공합니다. 이러한 단계는 모든 fhir 서버 및 모든 id 공급자에 적용 되지만이 문서의 id 공급자로 fhir 서비스 및 Azure Active Directory (Azure AD)를 살펴보겠습니다.

## <a name="access-control-overview"></a>액세스 제어 개요

클라이언트 응용 프로그램이 FHIR 서비스에 액세스 하려면 액세스 토큰을 제공 해야 합니다. 액세스 토큰은 클라이언트의 id와 클라이언트에 부여 된 역할 및 권한에 대 한 정보를 전달 하는 속성 (클레임)의 서명 된 [Base64](https://en.wikipedia.org/wiki/Base64) 인코딩 컬렉션입니다.

여러 가지 방법으로 토큰을 가져올 수 있지만, FHIR 서비스는 올바른 클레임을 사용 하 여 적절 하 게 서명 된 토큰 인 경우 토큰을 가져오는 방법을 고려 하지 않습니다. 

예를 들어 [인증 코드 흐름](../../active-directory/azuread-dev/v1-protocols-oauth-code.md) 을 사용 하 여 FHIR 서버에 액세스 하면 아래 4 단계를 거칩니다.

![권한 부여](media/azure-active-directory-fhir-service/fhir-authorization.png)

1. 클라이언트는 `/authorize` AZURE AD의 끝점에 요청을 보냅니다. Azure AD는 사용자가 적절 한 자격 증명 (예: 사용자 이름 및 암호 또는 2 단계 인증)을 사용 하 여 인증 하는 로그인 페이지로 클라이언트를 리디렉션합니다. [권한 부여 코드 가져오기](../../active-directory/azuread-dev/v1-protocols-oauth-code.md#request-an-authorization-code)에 대 한 세부 정보를 참조 하세요. 인증에 성공 하면 인증 *코드가* 클라이언트에 반환 됩니다. Azure AD에서는이 권한 부여 코드를 클라이언트 응용 프로그램 등록에 구성 된 등록 된 회신 URL로만 반환할 수 있습니다 (아래 참조).
1. 클라이언트 응용 프로그램은 Azure AD의 끝점에서 *액세스 토큰* 에 대 한 권한 부여 코드를 교환 합니다 `/token` . 토큰을 요청할 때 클라이언트 응용 프로그램에서 클라이언트 암호 (응용 프로그램 암호)를 제공 해야 할 수 있습니다. [액세스 토큰 가져오기](../../active-directory/azuread-dev/v1-protocols-oauth-code.md#use-the-authorization-code-to-request-an-access-token)에 대 한 세부 정보를 참조 하세요.
1. 클라이언트는 FHIR 서비스에 대 한 요청을 수행 합니다. 예를 들어 `GET /Patient` 모든 환자를 검색 합니다. 요청을 만들 때 HTTP 요청 헤더에 액세스 토큰을 포함 합니다 (예: 여기서는 b a s e `Authorization: Bearer eyJ0e...` `eyJ0e...` 64로 인코딩된 액세스 토큰을 나타냄).
1. FHIR 서비스는 토큰에 적절 한 클레임 (토큰의 속성)이 포함 되어 있는지 확인 합니다. 모든 항목이 체크 아웃 되 면 요청을 완료 하 고 결과가 포함 된 FHIR 번들을 클라이언트에 반환 합니다.

FHIR 서비스는 사용자 자격 증명의 유효성을 검사 하는 것과 관련이 없으며 토큰을 발급 하지 않습니다. 인증 및 토큰 생성은 Azure AD에서 수행 됩니다. FHIR 서비스는 토큰에 올바르게 서명 (인증 됨) 하 고 적절 한 클레임이 있음을 확인 합니다.

## <a name="structure-of-an-access-token"></a>액세스 토큰의 구조

FHIR 응용 프로그램을 개발 하려면 종종 액세스 문제를 디버깅 해야 합니다. 클라이언트는 FHIR 서비스에 대 한 액세스를 거부 한 경우 액세스 토큰의 구조와 토큰의 콘텐츠 (클레임)를 검사 하는 방법을 디코딩하는 방법을 이해 하는 것이 유용 합니다. 

FHIR 서버는 일반적으로 [JSON Web Token](https://en.wikipedia.org/wiki/JSON_Web_Token) (JWT, 때때로 "ab"를 발음)로 간주 합니다. 다음 세 부분으로 구성 됩니다.

**1 부**: 헤더는 다음과 같습니다.
```json
    {
      "alg": "HS256",
      "typ": "JWT"
    }
```

**2 부**: 페이로드 (클레임). 예를 들면 다음과 같습니다.
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

**3 부**: 헤더와 페이로드의 Base64 인코딩 콘텐츠를 연결 하 고 `alg` 헤더에 지정 된 알고리즘 ()에 따라 암호화 해시를 계산 하 여 계산 되는 서명입니다. 서버는 id 공급자에서 공개 키를 가져올 수 있으며,이 토큰이 특정 id 공급자에 의해 발급 되었는지, 변조 되지 않았는지를 확인할 수 있습니다.

전체 토큰은 이러한 세 세그먼트의 Base64 인코딩 (실제로는 Base64 url 인코딩된) 버전으로 구성 됩니다. 세 개의 세그먼트가 연결 되 고 `.` (점)로 구분 됩니다.

예제 토큰은 다음과 같습니다.

```
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJvaWQiOiIxMjMiLCAiaXNzIjoiaHR0cHM6Ly9pc3N1ZXJ1cmwiLCJpYXQiOjE0MjI3Nzk2MzgsInJvbGVzIjpbImFkbWluIl19.gzSraSYS8EXBxLN_oWnFSRgCzcmJmMjLiuyu5CSpyHI
```

토큰은와 같은 도구를 사용 하 여 디코드 하 고 검사할 수 있습니다 [https://jwt.ms](https://jwt.ms) . 토큰을 디코딩하는 결과는 다음과 같습니다.

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

위에서 언급 한 것 처럼 Azure AD에서 토큰을 가져오는 방법에는 여러 가지가 있습니다. [AZURE AD 개발자 설명서](../../active-directory/develop/index.yml)에 자세히 설명 되어 있습니다.

Azure AD에는 및 라고 하는 OAuth 2.0 끝점의 두 가지 버전이 있습니다 `v1.0` `v2.0` . 이러한 두 버전은 모두 OAuth 2.0 끝점 이며 `v1.0` 및 `v2.0` 명칭은 Azure AD가 해당 표준을 구현 하는 방법의 차이를 나타냅니다. 

FHIR 서버를 사용 하는 경우 또는 끝점 중 하나를 사용할 수 있습니다 `v1.0` `v2.0` . 선택은 클라이언트 응용 프로그램에서 사용 하는 인증 라이브러리에 따라 달라질 수 있습니다.

Azure AD 설명서의 관련 섹션은 다음과 같습니다.

* `v1.0` 엔드포인트:
    * [인증 코드 흐름](../../active-directory/azuread-dev/v1-protocols-oauth-code.md)입니다.
    * [클라이언트 자격 증명 흐름](../../active-directory/azuread-dev/v1-oauth2-client-creds-grant-flow.md).
* `v2.0` 엔드포인트:
    * [인증 코드 흐름](../../active-directory/develop/v2-oauth2-auth-code-flow.md)입니다.
    * [클라이언트 자격 증명 흐름](../../active-directory/develop/v2-oauth2-client-creds-grant-flow.md).

토큰을 가져오기 위한 다른 변형 (예: 흐름 대신)이 있습니다. 자세한 내용은 Azure AD 설명서를 확인 하세요. FHIR 서비스를 사용 하는 경우 [Azure CLI를 사용 하 여](get-healthcare-apis-access-token-cli.md)디버깅을 위해 액세스 토큰을 가져오기 위한 몇 가지 바로 가기도 있습니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 Azure AD를 사용 하 여 FHIR 서비스에 대 한 액세스를 보호 하는 데 관련 된 몇 가지 기본 개념을 배웠습니다. FHIR 서비스를 배포 하는 방법에 대 한 자세한 내용은을 참조 하세요.

>[!div class="nextstepaction"]
>[FHIR 서비스 배포](fhir-portal-quickstart.md)