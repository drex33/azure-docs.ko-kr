---
title: 자습서 - Azure Active Directory에서 앱에 대한 사용자 프로비저닝을 위한 SCIM 엔드포인트 개발
description: SCIM(도메인 간 ID 관리를 위한 시스템)은 자동 사용자 프로비저닝을 표준화합니다. 이 자습서에서는 SCIM 엔드포인트를 개발하고, SCIM API를 Azure Active Directory와 통합하고, 프로비저닝 사용자 및 그룹을 클라우드 애플리케이션으로 자동화하는 방법을 알아봅니다.
services: active-directory
author: kenwith
manager: karenh444
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: tutorial
ms.date: 07/26/2021
ms.author: kenwith
ms.reviewer: arvinh
ms.openlocfilehash: 03e2ce2c2d40c5edfa35515dd746802307d05f4b
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/14/2021
ms.locfileid: "129990656"
---
# <a name="tutorial-develop-and-plan-provisioning-for-a-scim-endpoint-in-azure-active-directory"></a>자습서: Azure Active Directory에서 SCIM 엔드포인트에 대한 프로비저닝 개발 및 계획

애플리케이션 개발자는 SCIM(System for Cross-Domain Identity Management) 사용자 관리 API를 사용하여 애플리케이션과 AAD(Azure AD) 간에 사용자 및 그룹을 자동으로 프로비저닝할 수 있습니다. 이 문서에서는 SCIM 엔드포인트를 빌드하고 AAD 프로비저닝 서비스와 통합하는 방법에 대해 설명합니다. SCIM 사양에서는 프로비저닝을 위한 일반 사용자 스키마를 제공합니다. SCIM은 SAML 또는 OpenID Connect와 같은 페더레이션 표준과 함께 사용하면 액세스 관리를 위한 전반적인 표준 기반 솔루션을 관리자에게 제공합니다.

![SCIM을 사용하여 Azure AD에서 앱으로 프로비저닝](media/use-scim-to-provision-users-and-groups/scim-provisioning-overview.png)

SCIM은 두 엔드포인트(`/Users` 엔드포인트 및 `/Groups` 엔드포인트)에 대한 표준화된 정의입니다. 일반 REST 동사를 사용하여 개체를 생성, 업데이트 및 삭제하며 그룹 이름, 사용자 이름, 이름, 성, 메일 등의 일반 특성에 대한 스키마를 사전 정의합니다. SCIM 2.0 REST API를 제공하는 앱은 독점적인 사용자 관리 API로 작업할 필요를 줄이거나 없앨 수 있습니다. 예를 들어 모든 규격 SCIM 클라이언트는 `/Users` 엔드포인트에 대한 JSON 개체의 HTTP POST를 수행하여 새 사용자 항목을 만드는 방법을 알고 있습니다. SCIM 표준을 준수하는 앱은 동일한 기본 작업에 대해 약간 다른 API를 사용하지 않고도 기존 클라이언트, 도구 및 코드를 바로 활용할 수 있습니다. 

SCIM 2.0(RFC [7642](https://tools.ietf.org/html/rfc7642), [7643](https://tools.ietf.org/html/rfc7643), [7644](https://tools.ietf.org/html/rfc7644))에 정의된 관리용 표준 사용자 개체 스키마 및 REST API를 사용하여 ID 공급자와 앱을 서로 쉽게 통합할 수 있습니다. SCIM 엔드포인트를 빌드하는 애플리케이션 개발자는 사용자 지정 작업을 수행하지 않고도 SCIM 규격 클라이언트와 통합할 수 있습니다.

애플리케이션에 대한 프로비저닝을 자동화하려면 SCIM 엔드포인트를 빌드하여 Azure AD SCIM 클라이언트와 통합해야 합니다. 다음 단계를 사용하여 애플리케이션에 대한 사용자 및 그룹 프로비저닝을 시작합니다. 
    
1. 사용자 및 그룹 스키마 디자인

   애플리케이션의 개체 및 특성을 식별하여 AAD SCIM 구현에서 지원하는 사용자 및 그룹 스키마에 매핑하는 방법을 결정합니다.

1. AAD SCIM 구현 이해

   AAD SCIM 클라이언트를 구현하여 SCIM 프로토콜 요청 처리 및 응답을 모델링하는 방법을 이해합니다.

1. SCIM 엔드포인트 빌드

   AAD 프로비저닝 서비스와 통합하려면 엔드포인트가 SCIM 2.0과 호환되어야 합니다. 옵션으로 Microsoft CLI(공용 언어 인프라) 라이브러리 및 코드 샘플을 사용하여 엔드포인트를 빌드합니다. 이러한 샘플은 참조 및 테스트용으로만 사용할 수 있으며, 프로덕션 앱에서 종속성으로 사용하지 않는 것이 좋습니다.

1. SCIM 엔드포인트를 AAD SCIM 클라이언트와 통합 

   조직에서 타사 애플리케이션을 사용하여 AAD에서 지원하는 SCIM 2.0 프로필을 구현하는 경우 사용자와 그룹의 프로비저닝 및 프로비저닝 해제를 모두 빠르게 자동화할 수 있습니다.

1. AAD 애플리케이션 갤러리에 애플리케이션 게시 

   고객이 애플리케이션을 검색하고 프로비저닝을 쉽게 구성할 수 있도록 합니다. 

![SCIM 엔드포인트를 Azure AD와 통합하기 위한 단계](media/use-scim-to-provision-users-and-groups/process.png)

## <a name="design-your-user-and-group-schema"></a>사용자 및 그룹 스키마 디자인

각 애플리케이션에는 사용자 또는 그룹을 만드는 여러 특성이 필요합니다. 애플리케이션에 필요한 개체(사용자, 그룹) 및 특성(이름, 관리자, 직위 등)을 식별하여 통합을 시작합니다. 

SCIM 표준은 사용자 및 그룹을 관리하기 위한 스키마를 정의합니다. 

**core** 사용자 스키마에는 세 가지 특성만 필요합니다(다른 모든 특성은 선택 사항임).

- `id` - 서비스 공급자 정의 식별자
- `userName` - 사용자에 대한 고유 식별자(일반적으로 Azure AD 사용자 계정 이름에 매핑됨)
- `meta` - 서비스 공급자에서 유지 관리하는 *읽기 전용* 메타데이터

SCIM 표준은 **core** 사용자 스키마 외에도 애플리케이션의 요구 사항을 충족하기 위해 사용자 스키마를 확장하는 모델을 사용하여 **enterprise** 사용자 확장을 정의합니다. 

예를 들어 애플리케이션에 사용자의 이메일 및 사용자의 관리자가 모두 필요한 경우 **core** 스키마를 사용하여 사용자의 이메일을 수집하고 **enterprise** 사용자 스키마를 사용하여 사용자의 관리자를 수집합니다.

스키마를 디자인하려면 다음 단계를 수행합니다.

1. 애플리케이션에 필요한 특성을 나열한 다음, 인증에 필요한 특성(예: loginName 및 email), 사용자 수명 주기 관리에 필요한 특성(예: status/active) 및 애플리케이션이 작동하는 데 필요한 기타 모든 특성(예: manager, tag)으로 범주화합니다.

1. 특성이 이미 **core** 사용자 스키마 또는 **enterprise** 사용자 스키마에 정의되어 있는지 확인합니다. 그렇지 않은 경우 누락된 특성이 포함된 사용자 스키마에 대한 확장을 정의해야 합니다. 사용자 `tag` 프로비저닝을 허용하는 사용자 확장은 아래 예제를 참조하세요.

1. SCIM 특성을 Azure AD의 사용자 특성에 매핑합니다. SCIM 엔드포인트에서 정의한 특성 중 하나에 Azure AD 사용자 스키마에 대한 명확한 대응 항목이 없는 경우 테넌트 관리자에게 해당 스키마를 확장하거나 아래와 같이 확장 특성을 `tags` 속성에 사용하도록 안내합니다.

|필수 앱 특성|매핑되는 SCIM 특성|매핑되는 Azure AD 특성|
|--|--|--|
|loginName|userName|userPrincipalName|
|firstName|name.givenName|givenName|
|lastName|name.familyName|surName|
|workMail|emails[type eq “work”].value|Mail|
|manager|manager|manager|
|tag|urn:ietf:params:scim:schemas:extension:CustomExtensionName:2.0:User:tag|extensionAttribute1|
|상태|활성|isSoftDeleted(사용자에 저장되지 않은 계산된 값)|

**필수 특성의 예제 목록**

```json
{
     "schemas": ["urn:ietf:params:scim:schemas:core:2.0:User",
      "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User",
      "urn:ietf:params:scim:schemas:extension:CustomExtensionName:2.0:User"],
     "userName":"bjensen@testuser.com",
     "id": "48af03ac28ad4fb88478",
     "externalId":"bjensen",
     "name":{
       "familyName":"Jensen",
       "givenName":"Barbara"
     },
     "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User": {
     "Manager": "123456"
   },
     "urn:ietf:params:scim:schemas:extension:CustomExtensionName:2.0:User": {
     "tag": "701984",
   },
   "meta": {
     "resourceType": "User",
     "created": "2010-01-23T04:56:22Z",
     "lastModified": "2011-05-13T04:42:34Z",
     "version": "W\/\"3694e05e9dff591\"",
     "location":
 "https://example.com/v2/Users/2819c223-7f76-453a-919d-413861904646"
   }
}   
```
**JSON 페이로드로 정의된 예제 스키마**

> [!NOTE]
> JSON 표현에는 애플리케이션에 필요한 특성 외에도 필수 `id`, `externalId` 및 `meta` 특성이 포함됩니다.

이는 `/User`와 `/Group` 사이를 범주화하여 Azure AD의 기본 사용자 특성을 SCIM RFC에 매핑하는 데 도움이 됩니다. [Azure AD와 SCIM 엔드포인트 간에 매핑되는 특성을 사용자 지정하는 방법](customize-application-attributes.md)을 참조하세요.


| Azure Active Directory 사용자 | "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User" |
| --- | --- |
| IsSoftDeleted |활성 |
|department|urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:department|
| displayName |displayName |
|employeeId|urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:employeeNumber|
| Facsimile-TelephoneNumber |phoneNumbers[type eq "fax"].value |
| givenName |name.givenName |
| jobTitle |title |
| mail |emails[type eq "work"].value |
| mailNickname |externalId |
| manager |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:manager |
| mobile |phoneNumbers[type eq "mobile"].value |
| postalCode |addresses[type eq "work"].postalCode |
| proxy-Addresses |emails[type eq "other"].Value |
| physical-Delivery-OfficeName |addresses[type eq "other"].Formatted |
| streetAddress |addresses[type eq "work"].streetAddress |
| surname |name.familyName |
| telephone-Number |phoneNumbers[type eq "work"].value |
| user-PrincipalName |userName |

**사용자 및 그룹 특성의 예제 목록**

| Azure Active Directory 그룹 | urn:ietf:params:scim:schemas:core:2.0:Group |
| --- | --- |
| displayName |displayName |
| members |members |
| objectId |externalId |

**그룹 특성의 예제 목록**

> [!NOTE]
> 사용자와 그룹을 모두 지원하거나 여기에 나와 있는 특성을 모두 지원할 필요가 없으며, Azure AD의 특성이 SCIM 프로토콜의 속성에 매핑되는 방법에 대한 참조일 뿐입니다. 

SCIM RFC에는 여러 엔드포인트가 정의되어 있습니다. `/User` 엔드포인트를 시작한 다음, 해당 위치에서 확장할 수 있습니다. 

|엔드포인트|설명|
|--|--|
|/User|사용자 개체에 대한 CRUD 작업을 수행합니다.|
|/Group|그룹 개체에 대한 CRUD 작업을 수행합니다.|
|/Schemas|각 클라이언트 및 서비스 공급자가 지원하는 특성 집합은 다를 수 있습니다. 한 서비스 공급자는 `name`, `title` 및 `emails`를 포함할 수 있지만 다른 서비스 공급자는 `name`, `title` 및 `phoneNumbers`를 사용합니다. 스키마 엔드포인트는 지원되는 특성의 검색을 허용합니다.|
|/Bulk|대량 작업을 수행하면 단일 작업에서 대규모 리소스 개체 컬렉션에 대한 작업을 수행할 수 있습니다(예: 대규모 그룹의 멤버 자격 업데이트).|
|/ServiceProviderConfig|지원되는 리소스 및 인증 방법 등 지원되는 SCIM 표준의 기능에 대한 세부 정보를 제공합니다.|
|/ResourceTypes|각 리소스에 대한 메타데이터를 지정합니다.|

**엔드포인트의 예제 목록**

> [!NOTE]
> `/Schemas` 엔드포인트를 사용하여 사용자 지정 특성을 지원하거나, 스키마가 자주 변경되는 경우 클라이언트에서 최신 스키마를 자동으로 검색할 수 있도록 합니다. 그룹을 지원하려면 `/Bulk` 엔드포인트를 사용합니다.

## <a name="understand-the-aad-scim-implementation"></a>AAD SCIM 구현 이해

SCIM 2.0 사용자 관리 API를 지원하기 위해 이 섹션에서는 AAD SCIM 클라이언트를 구현하는 방법을 설명하고 SCIM 프로토콜 요청 처리 및 응답을 모델링하는 방법을 보여 줍니다.

> [!IMPORTANT]
> Azure AD SCIM 구현 동작이 2018년 12월 18일에 마지막으로 업데이트되었습니다. 변경된 내용에 대한 자세한 내용은 [Azure AD 사용자 프로비저닝 서비스의 SCIM 2.0 프로토콜 규정 준수](application-provisioning-config-problem-scim-compatibility.md)를 참조하세요.

[SCIM 2.0 프로토콜 사양](http://www.simplecloud.info/#Specification) 내에서 애플리케이션은 다음과 같은 요구 사항을 충족해야 합니다.

|요구 사항|참조 정보(SCIM 프로토콜)|
|-|-|
|사용자(필요에 따라 그룹) 만들기|[섹션 3.3](https://tools.ietf.org/html/rfc7644#section-3.3)|
|PATCH 요청을 사용하여 사용자 또는 그룹 수정|[섹션 3.5.2](https://tools.ietf.org/html/rfc7644#section-3.5.2). 지원을 통해 그룹 및 사용자가 수행 가능한 방식으로 프로비저닝됩니다.|
|이전에 만든 사용자 또는 그룹에 대해 알려진 리소스 검색|[섹션 3.4.1](https://tools.ietf.org/html/rfc7644#section-3.4.1)|
|사용자 또는 그룹 쿼리|[섹션 3.4.2](https://tools.ietf.org/html/rfc7644#section-3.4.2).  기본적으로 사용자는 `id`로 검색되고 `username` 및 `externalId`에 의해 쿼리되며 그룹은 `displayName`에 의해 쿼리됩니다.|
|그룹 리소스를 쿼리하는 경우 [excludedAttributes=members](#get-group) 필터|섹션 3.4.2.5|
|애플리케이션에 대한 AAD 인증 및 권한 부여에 단일 전달자 토큰 허용||
|사용자 `active=false` 일시 삭제 및 사용자 `active=true` 복원|사용자가 활성 상태인지 여부에 관계없이 요청에서 사용자 개체를 반환해야 합니다. 애플리케이션에서 영구 삭제될 때만 사용자가 반환되지 않습니다.|
|/Schemas 엔드포인트 지원|[섹션 7](https://tools.ietf.org/html/rfc7643#page-30) 스키마 검색 엔드포인트는 추가 특성을 검색하는 데 사용됩니다.|

AAD와의 호환성을 보장하려면 SCIM 엔드포인트를 구현할 때 다음과 같은 일반적인 지침을 사용합니다.

##### <a name="general"></a>일반: 
* `id`는 모든 리소스에 대한 필수 속성입니다. 리소스를 반환하는 모든 응답은 멤버가 0인 `ListResponse`를 제외하고 각 리소스에 이 속성이 있는지 확인해야 합니다.
* 전송된 값은 전송될 때와 같은 형식으로 저장해야 합니다. 잘못된 값은 설명이 포함된 실행 가능한 오류 메시지를 사용하여 거부해야 합니다. Azure AD에서 전송되는 데이터와 SCIM 애플리케이션에 저장되는 데이터 사이에서는 데이터 변환이 발생하면 안 됩니다. 예를 들어 55555555555로 보낸 전화 번호는 + 5 (555) 555-5555로 저장/반환되면 안 됩니다.
* **PATCH** 응답에서 전체 리소스를 포함할 필요가 없습니다.
* [섹션 3.5.2](https://tools.ietf.org/html/rfc7644#section-3.5.2)에서 정의한 대로 SCIM의 구조적 요소, 특히 **PATCH** `op` 작업 값에서 대/소문자를 구분할 필요가 없습니다. AAD는 `op` 값을 **Add**, **Replace**, **Remove** 로 내보냅니다.
* Microsoft AAD는 엔드포인트 및 자격 증명이 유효한지 확인하기 위해 임의의 사용자 및 그룹을 가져오도록 요청합니다. 또한 [Azure Portal](https://portal.azure.com)에서 **연결 테스트** 흐름의 일부로 수행됩니다. 
* SCIM 엔드포인트에서 HTTPS를 지원합니다.
* 사용자 지정 복합 및 다중 값 특성이 지원되지만 이 경우 AAD에는 데이터를 끌어올 복잡한 데이터 구조가 많지 않습니다. 단순 쌍의 이름/값 유형 복합 특성은 쉽게 매핑될 수 있지만, 이 경우에는 세 개 이상의 하위 특성을 가진 복합 특성으로의 데이터 흐름이 잘 지원되지 않습니다.

##### <a name="retrieving-resources"></a>리소스 검색:
* 쿼리/필터 요청에 대한 응답은 항상 `ListResponse`여야 합니다.
* Microsoft AAD는 `eq`, `and` 연산자만 사용합니다.
* 리소스를 쿼리할 수 있는 특성은 [Azure Portal](https://portal.azure.com)의 애플리케이션에서 일치하는 특성으로 설정해야 합니다. [사용자 프로비저닝 특성 매핑 사용자 지정](customize-application-attributes.md)을 참조하세요.

##### <a name="users"></a>/Users:
* 자격 특성은 지원되지 않습니다.
* 사용자 고유성을 고려하는 모든 특성은 필터링된 쿼리의 일부로 사용할 수 있어야 합니다. (예: userName 및 emails[type eq "work"] 둘 다에 대해 사용자 고유성이 평가되는 경우 필터를 사용한 /Users에 대한 GET은 _userName eq "user@contoso.com"_ 및 _emails[type eq "work"] eq "user@contoso.com"_ 쿼리를 둘 다 허용해야 합니다.

##### <a name="groups"></a>/Groups:
* 그룹은 선택 사항이지만 SCIM 구현에서 **PATCH** 요청을 지원하는 경우에만 지원됩니다.
* Azure Active Directory와 SCIM 애플리케이션 간의 일치를 위해 그룹에는 'displayName' 값에 대한 고유성이 있어야 합니다. 이는 SCIM 프로토콜의 요구 사항이 아니지만 Azure Active Directory와 SCIM 서비스를 통합하기 위한 요구 사항입니다.

##### <a name="schemas-schema-discovery"></a>/Schemas(스키마 검색):

* [샘플 요청/응답](#schema-discovery)
* 스키마 검색은 현재 사용자 지정 비갤러리 SCIM 애플리케이션에서 지원되지 않지만 특정 갤러리 애플리케이션에서 사용되고 있습니다. 앞으로 스키마 검색은 기존 갤러리 SCIM 애플리케이션의 스키마에 특성을 추가하는 유일한 방법으로 사용됩니다. 
* 값이 없으면 null 값을 보내지 마세요.
* 속성 값은 camel 대/소문자를 지정해야 합니다(예: readWrite).
* 목록 응답을 반환해야 합니다.
* /schemas 요청은 누군가가 Azure Portal에서 프로비저닝 구성을 저장할 때마다 또는 사용자가 Azure Portal의 프로비저닝 편집 페이지에 도달할 때마다 Azure AD SCIM 클라이언트에서 수행됩니다. 검색된 추가 특성은 대상 특성 목록 아래의 특성 매핑에서 고객에게 표시됩니다. 스키마 검색에서는 추가 대상 특성만 추가됩니다. 특성이 제거되지 않습니다. 

  
### <a name="user-provisioning-and-deprovisioning"></a>사용자 프로비저닝 및 프로비저닝 해제

다음 그림에서는 애플리케이션의 ID 저장소에 있는 사용자의 수명 주기를 관리하기 위해 AAD에서 SCIM 서비스를 보내는 메시지를 보여 줍니다.  

![사용자 프로비저닝 및 프로비저닝 해제 시퀀스를 보여 줌](media/use-scim-to-provision-users-and-groups/scim-figure-4.png)<br/>
‘사용자 프로비저닝 및 프로비저닝 해제 시퀀스’

### <a name="group-provisioning-and-deprovisioning"></a>그룹 프로비저닝 및 프로비저닝 해제

그룹 프로비저닝 및 프로비저닝 해제는 선택 사항입니다. 이러한 작업이 구현되고 사용하도록 설정된 경우 다음 그림에서는 애플리케이션의 ID 저장소에 있는 그룹의 수명 주기를 관리하기 위해 AAD에서 SCIM 서비스를 보내는 메시지를 보여 줍니다. 해당 메시지는 두 가지 측면에서 사용자에 대한 메시지와 다릅니다.

* 그룹을 검색하는 요청은 멤버 특성이 요청에 대한 응답에서 제공된 리소스로부터 제외된다고 지정합니다.  
* 참조 특성에 특정 값이 있는지를 확인하는 요청은 멤버 특성에 대한 요청입니다.  

![그룹 프로비저닝 및 프로비저닝 해제 시퀀스를 보여 줌](media/use-scim-to-provision-users-and-groups/scim-figure-5.png)<br/>
‘그룹 프로비저닝 및 프로비저닝 해제 시퀀스’

### <a name="scim-protocol-requests-and-responses"></a>SCIM 프로토콜 요청 및 응답
이 섹션에서는 AAD SCIM 클라이언트에서 내보낸 SCIM 요청 예제 및 예상 응답 예제를 제공합니다. 최상의 결과를 위해서는 요청을 이 형식으로 처리하고 예상되는 응답을 내보내는 앱을 코딩해야 합니다.

> [!IMPORTANT]
> AAD 사용자 프로비저닝 서비스에서 아래에 설명된 작업을 내보내는 방법과 시기를 이해하려면 [프로비저닝 작동 방식](how-provisioning-works.md)의 [프로비저닝 주기: 초기 및 증분](how-provisioning-works.md#provisioning-cycles-initial-and-incremental) 섹션을 참조하세요.

[사용자 작업](#user-operations)
  - [사용자 만들기](#create-user)([요청](#request) / [응답](#response))
  - [사용자 가져오기](#get-user)([요청](#request-1) / [응답](#response-1))
  - [쿼리로 사용자 가져오기](#get-user-by-query)([요청](#request-2) / [응답](#response-2))
  - [쿼리로 사용자 가져오기 - 결과 없음](#get-user-by-query---zero-results)([요청](#request-3) / [응답](#response-3))
  - [사용자 업데이트[다중값 속성]](#update-user-multi-valued-properties)([요청](#request-4) / [응답](#response-4))
  - [사용자 업데이트[단일값 속성]](#update-user-single-valued-properties)([요청](#request-5) / [응답](#response-5)) 
  - [사용자 사용 안 함](#disable-user)([요청](#request-14) / [응답](#response-14))
  - [사용자 삭제](#delete-user)([요청](#request-6) / [응답](#response-6))

[그룹 작업](#group-operations)
  - [그룹 만들기](#create-group)([요청](#request-7) / [응답](#response-7))
  - [그룹 가져오기](#get-group)([요청](#request-8) / [응답](#response-8))
  - [displayName으로 그룹 가져오기](#get-group-by-displayname)([요청](#request-9) / [응답](#response-9))
  - [그룹 업데이트[비멤버 특성]](#update-group-non-member-attributes)([요청](#request-10) / [응답](#response-10))
  - [그룹 업데이트[멤버 추가]](#update-group-add-members)([요청](#request-11) / [응답](#response-11))
  - [그룹 업데이트[멤버 제거]](#update-group-remove-members)([요청](#request-12) / [응답](#response-12))
  - [그룹 삭제](#delete-group)([요청](#request-13) / [응답](#response-13))

[스키마 검색](#schema-discovery)
  - [스키마 검색](#discover-schema)([요청](#request-15) / [응답](#response-15))

### <a name="user-operations"></a>사용자 작업

* `userName` 또는 `email[type eq "work"]` 특성으로 사용자를 쿼리할 수 있습니다.  

#### <a name="create-user"></a>사용자 만들기

##### <a name="request"></a>요청

*POST /Users*
```json
{
    "schemas": [
        "urn:ietf:params:scim:schemas:core:2.0:User",
        "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"],
    "externalId": "0a21f0f2-8d2a-4f8e-bf98-7363c4aed4ef",
    "userName": "Test_User_ab6490ee-1e48-479e-a20b-2d77186b5dd1",
    "active": true,
    "emails": [{
        "primary": true,
        "type": "work",
        "value": "Test_User_fd0ea19b-0777-472c-9f96-4f70d2226f2e@testuser.com"
    }],
    "meta": {
        "resourceType": "User"
    },
    "name": {
        "formatted": "givenName familyName",
        "familyName": "familyName",
        "givenName": "givenName"
    },
    "roles": []
}
```

##### <a name="response"></a>응답

*HTTP/1.1 201 Created*
```json
{
    "schemas": ["urn:ietf:params:scim:schemas:core:2.0:User"],
    "id": "48af03ac28ad4fb88478",
    "externalId": "0a21f0f2-8d2a-4f8e-bf98-7363c4aed4ef",
    "meta": {
        "resourceType": "User",
        "created": "2018-03-27T19:59:26.000Z",
        "lastModified": "2018-03-27T19:59:26.000Z"
    },
    "userName": "Test_User_ab6490ee-1e48-479e-a20b-2d77186b5dd1",
    "name": {
        "formatted": "givenName familyName",
        "familyName": "familyName",
        "givenName": "givenName",
    },
    "active": true,
    "emails": [{
        "value": "Test_User_fd0ea19b-0777-472c-9f96-4f70d2226f2e@testuser.com",
        "type": "work",
        "primary": true
    }]
}
```

#### <a name="get-user"></a>사용자 가져오기

###### <a name="request"></a><a name="request-1"></a>요청
*GET /Users/5d48a0a8e9f04aa38008* 

###### <a name="response-user-found"></a><a name="response-1"></a>응답(사용자를 찾음)
*HTTP/1.1 200 OK*
```json
{
    "schemas": ["urn:ietf:params:scim:schemas:core:2.0:User"],
    "id": "5d48a0a8e9f04aa38008",
    "externalId": "58342554-38d6-4ec8-948c-50044d0a33fd",
    "meta": {
        "resourceType": "User",
        "created": "2018-03-27T19:59:26.000Z",
        "lastModified": "2018-03-27T19:59:26.000Z"
    },
    "userName": "Test_User_feed3ace-693c-4e5a-82e2-694be1b39934",
    "name": {
        "formatted": "givenName familyName",
        "familyName": "familyName",
        "givenName": "givenName",
    },
    "active": true,
    "emails": [{
        "value": "Test_User_22370c1a-9012-42b2-bf64-86099c2a1c22@testuser.com",
        "type": "work",
        "primary": true
    }]
}
```

###### <a name="request"></a>요청
*GET /Users/5171a35d82074e068ce2* 

###### <a name="response-user-not-found-note-that-the-detail-is-not-required-only-status"></a>응답(사용자를 찾을 수 없습니다. 세부 정보는 필요하지 않으며 상태만 필요합니다.)

```json
{
    "schemas": [
        "urn:ietf:params:scim:api:messages:2.0:Error"
    ],
    "status": "404",
    "detail": "Resource 23B51B0E5D7AE9110A49411D@7cca31655d49f3640a494224 not found"
}
```

#### <a name="get-user-by-query"></a>쿼리로 사용자 가져오기

##### <a name="request"></a><a name="request-2"></a>요청

*GET /Users?filter=userName eq "Test_User_dfeef4c5-5681-4387-b016-bdf221e82081"*

##### <a name="response"></a><a name="response-2"></a>응답

*HTTP/1.1 200 OK*
```json
{
    "schemas": ["urn:ietf:params:scim:api:messages:2.0:ListResponse"],
    "totalResults": 1,
    "Resources": [{
        "schemas": ["urn:ietf:params:scim:schemas:core:2.0:User"],
        "id": "2441309d85324e7793ae",
        "externalId": "7fce0092-d52e-4f76-b727-3955bd72c939",
        "meta": {
            "resourceType": "User",
            "created": "2018-03-27T19:59:26.000Z",
            "lastModified": "2018-03-27T19:59:26.000Z"
            
        },
        "userName": "Test_User_dfeef4c5-5681-4387-b016-bdf221e82081",
        "name": {
            "familyName": "familyName",
            "givenName": "givenName"
        },
        "active": true,
        "emails": [{
            "value": "Test_User_91b67701-697b-46de-b864-bd0bbe4f99c1@testuser.com",
            "type": "work",
            "primary": true
        }]
    }],
    "startIndex": 1,
    "itemsPerPage": 20
}
```

#### <a name="get-user-by-query---zero-results"></a>쿼리로 사용자 가져오기 - 결과 없음

##### <a name="request"></a><a name="request-3"></a>요청

*GET /Users?filter=userName eq "non-existent user"*

##### <a name="response"></a><a name="response-3"></a>응답

*HTTP/1.1 200 OK*
```json
{
    "schemas": ["urn:ietf:params:scim:api:messages:2.0:ListResponse"],
    "totalResults": 0,
    "Resources": [],
    "startIndex": 1,
    "itemsPerPage": 20
}
```

#### <a name="update-user-multi-valued-properties"></a>사용자 업데이트[다중값 속성]

##### <a name="request"></a><a name="request-4"></a>요청

*PATCH /Users/6764549bef60420686bc HTTP/1.1*
```json
{
    "schemas": ["urn:ietf:params:scim:api:messages:2.0:PatchOp"],
    "Operations": [
            {
            "op": "Replace",
            "path": "emails[type eq \"work\"].value",
            "value": "updatedEmail@microsoft.com"
            },
            {
            "op": "Replace",
            "path": "name.familyName",
            "value": "updatedFamilyName"
            }
    ]
}
```

##### <a name="response"></a><a name="response-4"></a>응답

*HTTP/1.1 200 OK*
```json
{
    "schemas": ["urn:ietf:params:scim:schemas:core:2.0:User"],
    "id": "6764549bef60420686bc",
    "externalId": "6c75de36-30fa-4d2d-a196-6bdcdb6b6539",
    "meta": {
        "resourceType": "User",
        "created": "2018-03-27T19:59:26.000Z",
        "lastModified": "2018-03-27T19:59:26.000Z"
    },
    "userName": "Test_User_fbb9dda4-fcde-4f98-a68b-6c5599e17c27",
    "name": {
        "formatted": "givenName updatedFamilyName",
        "familyName": "updatedFamilyName",
        "givenName": "givenName"
    },
    "active": true,
    "emails": [{
        "value": "updatedEmail@microsoft.com",
        "type": "work",
        "primary": true
    }]
}
```

#### <a name="update-user-single-valued-properties"></a>사용자 업데이트[단일값 속성]

##### <a name="request"></a><a name="request-5"></a>요청

*PATCH /Users/5171a35d82074e068ce2 HTTP/1.1*
```json
{
    "schemas": ["urn:ietf:params:scim:api:messages:2.0:PatchOp"],
    "Operations": [{
        "op": "Replace",
        "path": "userName",
        "value": "5b50642d-79fc-4410-9e90-4c077cdd1a59@testuser.com"
    }]
}
```

##### <a name="response"></a><a name="response-5"></a>응답

*HTTP/1.1 200 OK*
```json
{
    "schemas": ["urn:ietf:params:scim:schemas:core:2.0:User"],
    "id": "5171a35d82074e068ce2",
    "externalId": "aa1eca08-7179-4eeb-a0be-a519f7e5cd1a",
    "meta": {
        "resourceType": "User",
        "created": "2018-03-27T19:59:26.000Z",
        "lastModified": "2018-03-27T19:59:26.000Z"
        
    },
    "userName": "5b50642d-79fc-4410-9e90-4c077cdd1a59@testuser.com",
    "name": {
        "formatted": "givenName familyName",
        "familyName": "familyName",
        "givenName": "givenName",
    },
    "active": true,
    "emails": [{
        "value": "Test_User_49dc1090-aada-4657-8434-4995c25a00f7@testuser.com",
        "type": "work",
        "primary": true
    }]
}
```

### <a name="disable-user"></a>사용자 사용 안 함

##### <a name="request"></a><a name="request-14"></a>요청

*PATCH /Users/5171a35d82074e068ce2 HTTP/1.1*
```json
{
    "Operations": [
        {
            "op": "Replace",
            "path": "active",
            "value": false
        }
    ],
    "schemas": [
        "urn:ietf:params:scim:api:messages:2.0:PatchOp"
    ]
}
```

##### <a name="response"></a><a name="response-14"></a>응답

```json
{
    "schemas": [
        "urn:ietf:params:scim:schemas:core:2.0:User"
    ],
    "id": "CEC50F275D83C4530A495FCF@834d0e1e5d8235f90a495fda",
    "userName": "deanruiz@testuser.com",
    "name": {
        "familyName": "Harris",
        "givenName": "Larry"
    },
    "active": false,
    "emails": [
        {
            "value": "gloversuzanne@testuser.com",
            "type": "work",
            "primary": true
        }
    ],
    "addresses": [
        {
            "country": "ML",
            "type": "work",
            "primary": true
        }
    ],
    "meta": {
        "resourceType": "Users",
        "location": "/scim/5171a35d82074e068ce2/Users/CEC50F265D83B4530B495FCF@5171a35d82074e068ce2"
    }
}
```
#### <a name="delete-user"></a>사용자 삭제

##### <a name="request"></a><a name="request-6"></a>요청

*DELETE /Users/5171a35d82074e068ce2 HTTP/1.1*

##### <a name="response"></a><a name="response-6"></a>응답

*HTTP/1.1 204 No Content*

### <a name="group-operations"></a>그룹 작업

* 그룹은 항상 빈 멤버 목록으로 만들어야 합니다.
* `displayName` 특성으로 그룹을 쿼리할 수 있습니다.
* 그룹 PATCH 요청을 업데이트하면 응답에 *HTTP 204 No Content* 이 나타납니다. 모든 멤버 목록이 포함된 본문을 반환하는 것은 권장되지 않습니다.
* 그룹의 모든 멤버를 반환하는 작업을 지원할 필요는 없습니다.

#### <a name="create-group"></a>그룹 만들기

##### <a name="request"></a><a name="request-7"></a>요청

*POST /Groups HTTP/1.1*
```json
{
    "schemas": ["urn:ietf:params:scim:schemas:core:2.0:Group", "http://schemas.microsoft.com/2006/11/ResourceManagement/ADSCIM/2.0/Group"],
    "externalId": "8aa1a0c0-c4c3-4bc0-b4a5-2ef676900159",
    "displayName": "displayName",
    "meta": {
        "resourceType": "Group"
    }
}
```

##### <a name="response"></a><a name="response-7"></a>응답

*HTTP/1.1 201 Created*
```json
{
    "schemas": ["urn:ietf:params:scim:schemas:core:2.0:Group"],
    "id": "927fa2c08dcb4a7fae9e",
    "externalId": "8aa1a0c0-c4c3-4bc0-b4a5-2ef676900159",
    "meta": {
        "resourceType": "Group",
        "created": "2018-03-27T19:59:26.000Z",
        "lastModified": "2018-03-27T19:59:26.000Z"
        
    },
    "displayName": "displayName",
    "members": []
}
```

#### <a name="get-group"></a>그룹 가져오기

##### <a name="request"></a><a name="request-8"></a>요청

*GET /Groups/40734ae655284ad3abcc?excludedAttributes=members HTTP/1.1*

##### <a name="response"></a><a name="response-8"></a>응답
*HTTP/1.1 200 OK*
```json
{
    "schemas": ["urn:ietf:params:scim:schemas:core:2.0:Group"],
    "id": "40734ae655284ad3abcc",
    "externalId": "60f1bb27-2e1e-402d-bcc4-ec999564a194",
    "meta": {
        "resourceType": "Group",
        "created": "2018-03-27T19:59:26.000Z",
        "lastModified": "2018-03-27T19:59:26.000Z"
    },
    "displayName": "displayName",
}
```

#### <a name="get-group-by-displayname"></a>displayName으로 그룹 가져오기

##### <a name="request"></a><a name="request-9"></a>요청
*GET /Groups?excludedAttributes=members&filter=displayName eq "displayName" HTTP/1.1*

##### <a name="response"></a><a name="response-9"></a>응답

*HTTP/1.1 200 OK*
```json
{
    "schemas": ["urn:ietf:params:scim:api:messages:2.0:ListResponse"],
    "totalResults": 1,
    "Resources": [{
        "schemas": ["urn:ietf:params:scim:schemas:core:2.0:Group"],
        "id": "8c601452cc934a9ebef9",
        "externalId": "0db508eb-91e2-46e4-809c-30dcbda0c685",
        "meta": {
            "resourceType": "Group",
            "created": "2018-03-27T22:02:32.000Z",
            "lastModified": "2018-03-27T22:02:32.000Z",
            
        },
        "displayName": "displayName",
    }],
    "startIndex": 1,
    "itemsPerPage": 20
}
```

#### <a name="update-group-non-member-attributes"></a>그룹 업데이트[비멤버 특성]

##### <a name="request"></a><a name="request-10"></a>요청

*PATCH /Groups/fa2ce26709934589afc5 HTTP/1.1*
```json
{
    "schemas": ["urn:ietf:params:scim:api:messages:2.0:PatchOp"],
    "Operations": [{
        "op": "Replace",
        "path": "displayName",
        "value": "1879db59-3bdf-4490-ad68-ab880a269474updatedDisplayName"
    }]
}
```

##### <a name="response"></a><a name="response-10"></a>응답

*HTTP/1.1 204 No Content*

### <a name="update-group-add-members"></a>그룹 업데이트[멤버 추가]

##### <a name="request"></a><a name="request-11"></a>요청

*PATCH /Groups/a99962b9f99d4c4fac67 HTTP/1.1*
```json
{
    "schemas": ["urn:ietf:params:scim:api:messages:2.0:PatchOp"],
    "Operations": [{
        "op": "Add",
        "path": "members",
        "value": [{
            "$ref": null,
            "value": "f648f8d5ea4e4cd38e9c"
        }]
    }]
}
```

##### <a name="response"></a><a name="response-11"></a>응답

*HTTP/1.1 204 No Content*

#### <a name="update-group-remove-members"></a>그룹 업데이트[멤버 제거]

##### <a name="request"></a><a name="request-12"></a>요청

*PATCH /Groups/a99962b9f99d4c4fac67 HTTP/1.1*
```json
{
    "schemas": ["urn:ietf:params:scim:api:messages:2.0:PatchOp"],
    "Operations": [{
        "op": "Remove",
        "path": "members",
        "value": [{
            "$ref": null,
            "value": "f648f8d5ea4e4cd38e9c"
        }]
    }]
}
```

##### <a name="response"></a><a name="response-12"></a>응답

*HTTP/1.1 204 No Content*

#### <a name="delete-group"></a>그룹 삭제

##### <a name="request"></a><a name="request-13"></a>요청

*DELETE /Groups/cdb1ce18f65944079d37 HTTP/1.1*

##### <a name="response"></a><a name="response-13"></a>응답

*HTTP/1.1 204 No Content*

### <a name="schema-discovery"></a>스키마 검색
#### <a name="discover-schema"></a>스키마 검색

##### <a name="request"></a><a name="request-15"></a>요청
*GET /Schemas* 
##### <a name="response"></a><a name="response-15"></a>응답
*HTTP/1.1 200 OK*
```json
{
    "schemas": [
        "urn:ietf:params:scim:api:messages:2.0:ListResponse"
    ],
    "itemsPerPage": 50,
    "startIndex": 1,
    "totalResults": 3,
    "Resources": [
  {
    "schemas": ["urn:ietf:params:scim:schemas:core:2.0:Schema"],
    "id" : "urn:ietf:params:scim:schemas:core:2.0:User",
    "name" : "User",
    "description" : "User Account",
    "attributes" : [
      {
        "name" : "userName",
        "type" : "string",
        "multiValued" : false,
        "description" : "Unique identifier for the User, typically
used by the user to directly authenticate to the service provider.
Each User MUST include a non-empty userName value.  This identifier
MUST be unique across the service provider's entire set of Users.
REQUIRED.",
        "required" : true,
        "caseExact" : false,
        "mutability" : "readWrite",
        "returned" : "default",
        "uniqueness" : "server"
      },                
    ],
    "meta" : {
      "resourceType" : "Schema",
      "location" :
        "/v2/Schemas/urn:ietf:params:scim:schemas:core:2.0:User"
    }
  },
  {
    "schemas": ["urn:ietf:params:scim:schemas:core:2.0:Schema"],
    "id" : "urn:ietf:params:scim:schemas:core:2.0:Group",
    "name" : "Group",
    "description" : "Group",
    "attributes" : [
      {
        "name" : "displayName",
        "type" : "string",
        "multiValued" : false,
        "description" : "A human-readable name for the Group.
REQUIRED.",
        "required" : false,
        "caseExact" : false,
        "mutability" : "readWrite",
        "returned" : "default",
        "uniqueness" : "none"
      },
    ],
    "meta" : {
      "resourceType" : "Schema",
      "location" :
        "/v2/Schemas/urn:ietf:params:scim:schemas:core:2.0:Group"
    }
  },
  {
    "schemas": ["urn:ietf:params:scim:schemas:core:2.0:Schema"],
    "id" : "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User",
    "name" : "EnterpriseUser",
    "description" : "Enterprise User",
    "attributes" : [
      {
        "name" : "employeeNumber",
        "type" : "string",
        "multiValued" : false,
        "description" : "Numeric or alphanumeric identifier assigned
to a person, typically based on order of hire or association with an
organization.",
        "required" : false,
        "caseExact" : false,
        "mutability" : "readWrite",
        "returned" : "default",
        "uniqueness" : "none"
      },
    ],
    "meta" : {
      "resourceType" : "Schema",
      "location" :
"/v2/Schemas/urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"
    }
  }
]
}
```

### <a name="security-requirements"></a>보안 요구 사항
**TLS 프로토콜 버전**

TLS 프로토콜 버전은 TLS 1.2 및 TLS 1.3만 허용됩니다. 다른 버전의 TLS는 허용되지 않습니다. SSL 버전은 허용되지 않습니다. 
- RSA 키는 2048비트 이상이어야 합니다.
- ECC 키는 승인된 타원 곡선을 사용하여 생성된 256비트 이상이어야 합니다.

**키 길이**

모든 서비스는 충분한 길이의 암호화 키를 사용하여 생성된 X.509 인증서를 사용해야 합니다. 즉, 다음을 의미합니다.

**암호 그룹**

모든 서비스는 아래에 지정된 정확한 순서대로 다음 암호 도구 모음을 사용하도록 구성해야 합니다. RSA 인증서만 있는 경우에는 설치된 ECDSA 암호 도구 모음에 영향을 주지 않습니다. </br>

TLS 1.2 암호 도구 모음 최소 막대:

- TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256
- TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
- TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256
- TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256
- TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384

### <a name="ip-ranges"></a>IP 범위
Azure AD 프로비저닝 서비스는 현재 [여기](https://www.microsoft.com/download/details.aspx?id=56519&WT.mc_id=rss_alldownloads_all)에 나열된 AzureActiveDirectory의 IP 범위에서 작동합니다. AzureActiveDirectory 태그 아래에 나열된 IP 범위를 추가하면 Azure AD 프로비저닝 서비스에서 애플리케이션으로의 트래픽을 허용할 수 있습니다. 계산된 주소에 대해 IP 범위 목록을 신중하게 검토해야 합니다. '40.126.25.32'와 같은 주소는 IP 범위 목록에 '40.126.0.0/18'로 표시될 수 있습니다. 또한 다음 [API](/rest/api/virtualnetwork/servicetags/list)를 사용하여 IP 범위 목록을 프로그래밍 방식으로 검색할 수도 있습니다.

또한 Azure AD는 프라이빗 네트워크(온-프레미스, Azure에서 호스트, AWS에서 호스트 등)의 애플리케이션에 대한 연결을 제공하는 에이전트 기반 솔루션을 지원합니다. 고객은 프라이빗 네트워크의 서버에서 인바운드 포트를 열지 않고 Azure AD에 대한 연결을 제공하는 경량 에이전트를 배포할 수 있습니다. [여기](./on-premises-scim-provisioning.md)를 참조하세요.

## <a name="build-a-scim-endpoint"></a>SCIM 엔드포인트 빌드

스키마를 디자인하고 Azure AD SCIM 구현을 이해했으므로 이제 SCIM 엔드포인트 개발을 시작할 수 있습니다. 처음부터 시작하여 구현을 완전히 빌드하지 않고도 SCIM 커뮤니티에서 게시한 여러 오픈 소스 SCIM 라이브러리를 사용할 수 있습니다.

예제를 포함하여 SCIM 엔드포인트를 빌드하는 방법에 대한 지침은 [샘플 SCIM 엔드포인트 개발](use-scim-to-build-users-and-groups-endpoints.md)을 참조하세요.

Azure AD 프로비저닝 팀에서 게시한 오픈 소스 .NET Core [참조 코드 예제](https://aka.ms/SCIMReferenceCode)는 개발을 바로 시작할 수 있는 리소스 중 하나입니다. SCIM 엔드포인트를 빌드하면 테스트를 수행할 수 있습니다. 참조 코드의 일부로 제공된 [postman 테스트](https://github.com/AzureAD/SCIMReferenceCode/wiki/Test-Your-SCIM-Endpoint) 컬렉션을 사용하거나 [위의](#user-operations)에서 제공된 샘플 요청/응답을 실행할 수 있습니다.  

   > [!Note]
   > 참조 코드는 SCIM 엔드포인트 빌드를 시작하도록 고안되었으며 “현재 상태로” 제공됩니다. 커뮤니티에서의 기여는 코드 빌드 및 유지 관리에 도움이 됩니다.

이 솔루션은 2개의 프로젝트인 _Microsoft.SCIM_ 및 _Microsoft.SCIM.WebHostSample_ 로 구성되어 있습니다.

_Microsoft.SCIM_ 프로젝트는 웹 서비스의 구성 요소를 정의하는 라이브러리로, SCIM 사양을 준수합니다. 이 프로젝트는 _Microsoft.SCIM.IProvider_ 인터페이스를 선언하고 요청은 공급자의 메서드에 대한 호출로 변환되며, 이는 ID 저장소에서 작동하도록 프로그래밍됩니다.

![분석 결과: 공급자의 메서드에 대한 호출로 변환된 요청](media/use-scim-to-provision-users-and-groups/scim-figure-3.png)

_Microsoft.SCIM.WebHostSample_ 프로젝트는 ‘빈’ 템플릿을 기반으로 하는 Visual Studio ASP.NET Core 웹 애플리케이션입니다. 이를 통해 샘플 코드를 독립형으로 배포하며 컨테이너 또는 인터넷 정보 서비스 내에 호스팅할 수 있습니다. 또한 메모리의 클래스를 샘플 ID 저장소로 유지하는 _Microsoft.SCIM.IProvider_ 인터페이스를 구현합니다.

```csharp
    public class Startup
    {
        ...
        public IMonitor MonitoringBehavior { get; set; }
        public IProvider ProviderBehavior { get; set; }

        public Startup(IWebHostEnvironment env, IConfiguration configuration)
        {
            ...
            this.MonitoringBehavior = new ConsoleMonitor();
            this.ProviderBehavior = new InMemoryProvider();
        }
        ...
```

### <a name="building-a-custom-scim-endpoint"></a>사용자 지정 SCIM 엔드포인트 빌드

SCIM 서비스에는 루트 인증 기관의 이름이 다음 중 하나인 HTTP 주소 및 서버 인증 인증서가 있어야 합니다.

* CNNIC
* Comodo
* CyberTrust
* DigiCert
* GeoTrust
* GlobalSign
* Go Daddy
* VeriSign
* WoSign
* DST Root CA X3

.NET Core SDK에는 개발 중에 사용할 수 있는 HTTPS 개발 인증서가 포함되어 있으며 인증서는 최초 실행 환경의 일부로 설치됩니다. ASP.NET Core 웹 애플리케이션을 실행하는 방법에 따라 다른 포트를 수신 대기합니다.

* Microsoft.SCIM.WebHostSample: https://localhost:5001
* IIS Express: https://localhost:44359/

ASP.NET Core의 HTTPS에 대한 자세한 내용은 다음 링크를 참조하세요. [ASP.NET Core에서 HTTPS 적용](/aspnet/core/security/enforcing-ssl)

### <a name="handling-endpoint-authentication"></a>엔드포인트 인증 처리

Azure Active Directory에서 요청은 OAuth 2.0 전달자 토큰을 포함합니다. 요청을 받는 모든 서비스는 예상된 Azure Active Directory 테넌트의 Azure Active Directory로 발급자를 인증해야 합니다.

토큰에서 발급자는 `"iss":"https://sts.windows.net/cbb1a5ac-f33b-45fa-9bf5-f37db0fed422/"`와 같은 ISS 클레임으로 식별됩니다. 이 예제에서 클레임 값의 기본 주소인 `https://sts.windows.net`은 Azure Active Directory를 발급자로 식별하며, 상대 주소 세그먼트인 _cbb1a5ac-f33b-45fa-9bf5-f37db0fed422_ 는 토큰이 발급된 Azure Active Directory 테넌트의 고유한 식별자입니다.

토큰의 대상 그룹은 갤러리의 애플리케이션에 대한 애플리케이션 템플릿 ID이며 단일 테넌트에 등록된 각 애플리케이션은 SCIM 요청과 동일한 `iss` 클레임을 받을 수 있습니다. 모든 사용자 지정 앱의 애플리케이션 템플릿 ID는 _8adf8e6e-67b2-4cf2-a259-e3dc5476c621_ 입니다. Azure AD 프로비저닝 서비스로 생성된 토큰은 테스트에만 사용해야 합니다. 프로덕션 환경에서는 사용하면 안 됩니다.

샘플 코드에서 요청은 Microsoft.AspNetCore.Authentication.JwtBearer 패키지를 사용하여 인증됩니다. 다음 코드는 서비스의 엔드포인트에 대한 요청이 지정된 테넌트에 대해 Azure Active Directory에서 발급한 전달자 토큰을 사용하여 인증되도록 합니다.

```csharp
        public void ConfigureServices(IServiceCollection services)
        {
            if (_env.IsDevelopment())
            {
                ...
            }
            else
            {
                services.AddAuthentication(options =>
                {
                    options.DefaultAuthenticateScheme = JwtBearerDefaults.AuthenticationScheme;
                    options.DefaultAuthenticateScheme = JwtBearerDefaults.AuthenticationScheme;
                    options.DefaultChallengeScheme = JwtBearerDefaults.AuthenticationScheme;
                })
                    .AddJwtBearer(options =>
                    {
                        options.Authority = " https://sts.windows.net/cbb1a5ac-f33b-45fa-9bf5-f37db0fed422/";
                        options.Audience = "8adf8e6e-67b2-4cf2-a259-e3dc5476c621";
                        ...
                    });
            }
            ...
        }

        public void Configure(IApplicationBuilder app)
        {
            ...
            app.UseAuthentication();
            app.UseAuthorization();
            ...
       }
```

전달자 토큰은 제공된 [postman 테스트](https://github.com/AzureAD/SCIMReferenceCode/wiki/Test-Your-SCIM-Endpoint)를 사용하고 localhost를 사용하여 로컬 디버깅을 수행하는 데에도 필요합니다. 샘플 코드는 ASP.NET Core 환경을 사용하여 개발 단계 중에 인증 옵션을 변경하고 자체 서명된 토큰을 사용할 수 있도록 합니다.

ASP.NET Core의 여러 환경에 대한 자세한 내용은 [ASP.NET Core에서 여러 환경 사용](/aspnet/core/fundamentals/environments)을 참조하세요.

다음 코드는 서비스의 엔드포인트에 대한 요청이 사용자 지정 키로 서명된 전달자 토큰을 사용하여 인증되도록 합니다.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    if (_env.IsDevelopment())
    {
        services.AddAuthentication(options =>
        {
            options.DefaultAuthenticateScheme = JwtBearerDefaults.AuthenticationScheme;
            options.DefaultAuthenticateScheme = JwtBearerDefaults.AuthenticationScheme;
            options.DefaultChallengeScheme = JwtBearerDefaults.AuthenticationScheme;
        })
        .AddJwtBearer(options =>
        {
            options.TokenValidationParameters =
                new TokenValidationParameters
                {
                    ValidateIssuer = false,
                    ValidateAudience = false,
                    ValidateLifetime = false,
                    ValidateIssuerSigningKey = false,
                    ValidIssuer = "Microsoft.Security.Bearer",
                    ValidAudience = "Microsoft.Security.Bearer",
                    IssuerSigningKey = new SymmetricSecurityKey(Encoding.UTF8.GetBytes("A1B2C3D4E5F6A1B2C3D4E5F6"))
                };
        });
    }
...
```

유효한 전달자 토큰을 받으려면 토큰 컨트롤러에 GET 요청을 보냅니다. _GenerateJSONWebToken_ 메서드는 개발을 위해 구성된 매개 변수와 일치하는 토큰을 만듭니다.

```csharp
private string GenerateJSONWebToken()
{
    // Create token key
    SymmetricSecurityKey securityKey =
        new SymmetricSecurityKey(Encoding.UTF8.GetBytes("A1B2C3D4E5F6A1B2C3D4E5F6"));
    SigningCredentials credentials =
        new SigningCredentials(securityKey, SecurityAlgorithms.HmacSha256);

    // Set token expiration
    DateTime startTime = DateTime.UtcNow;
    DateTime expiryTime = startTime.AddMinutes(120);

    // Generate the token
    JwtSecurityToken token =
        new JwtSecurityToken(
            "Microsoft.Security.Bearer",
            "Microsoft.Security.Bearer",
            null,
            notBefore: startTime,
            expires: expiryTime,
            signingCredentials: credentials);

    string result = new JwtSecurityTokenHandler().WriteToken(token);
    return result;
}
```

### <a name="handling-provisioning-and-deprovisioning-of-users"></a>사용자의 프로비저닝 및 프로비저닝 해제 처리

***예제 1. 일치하는 사용자에 대해 서비스 쿼리***

AAD(Azure Active Directory)는 AAD에 있는 사용자의 mailNickname 특성 값과 일치하는 `externalId` 특성 값이 있는 사용자를 서비스에 쿼리합니다. 쿼리는 이 예제처럼 HTTP(Hypertext Transfer Protocol) 요청으로 표현되며, 여기서 jyoung은 Azure Active Directory에서 사용자의 mailNickname 샘플입니다.

>[!NOTE]
> 이것은 예로 든 것일 뿐입니다. 모든 사용자에게 mailNickname 특성이 있는 것은 아니며 사용자가 가진 값이 디렉터리에서 고유하지 않을 수도 있습니다. 또한 일치에 사용되는 특성(이 경우 `externalId`)은 [AAD 특성 매핑](customize-application-attributes.md)에서 구성할 수 있습니다.

```
GET https://.../scim/Users?filter=externalId eq jyoung HTTP/1.1
 Authorization: Bearer ...
```

샘플 코드에서 요청은 서비스 공급자의 QueryAsync 메서드에 대한 호출로 변환됩니다. 해당 메서드의 서명은 다음과 같습니다. 

```csharp
// System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
// Microsoft.SCIM.IRequest is defined in 
// Microsoft.SCIM.Service.  
// Microsoft.SCIM.Resource is defined in 
// Microsoft.SCIM.Schemas.  
// Microsoft.SCIM.IQueryParameters is defined in 
// Microsoft.SCIM.Protocol.  

Task<Resource[]> QueryAsync(IRequest<IQueryParameters> request);
```

`externalId` 특성의 값이 지정된 사용자에 대한 샘플 쿼리에서 QueryAsync 메서드에 전달된 인수의 값은 다음과 같습니다.

* parameters.AlternateFilters.Count: 1
* parameters.AlternateFilters.ElementAt(0).AttributePath: "externalId"
* parameters.AlternateFilters.ElementAt(0).ComparisonOperator: ComparisonOperator.Equals
* parameters.AlternateFilter.ElementAt(0).ComparisonValue: "jyoung"

***예제 2. 사용자 프로비저닝***

사용자의 mailNickname 특성 값과 일치하는 `externalId` 특성 값이 있는 사용자를 웹 서비스에 쿼리한 경우 응답에서 사용자를 반환하지 않으면 AAD는 서비스에서 AAD의 사용자에 해당하는 사용자를 프로비저닝하도록 요청합니다.  다음은 그러한 요청의 예제입니다. 

```
POST https://.../scim/Users HTTP/1.1
Authorization: Bearer ...
Content-type: application/scim+json
{
   "schemas":
   [
     "urn:ietf:params:scim:schemas:core:2.0:User",
     "urn:ietf:params:scim:schemas:extension:enterprise:2.0User"],
   "externalId":"jyoung",
   "userName":"jyoung@testuser.com",
   "active":true,
   "addresses":null,
   "displayName":"Joy Young",
   "emails": [
     {
       "type":"work",
       "value":"jyoung@Contoso.com",
       "primary":true}],
   "meta": {
     "resourceType":"User"},
    "name":{
     "familyName":"Young",
     "givenName":"Joy"},
   "phoneNumbers":null,
   "preferredLanguage":null,
   "title":null,
   "department":null,
   "manager":null}
```

샘플 코드에서 요청은 서비스 공급자의 CreateAsync 메서드에 대한 호출로 변환됩니다. 해당 메서드의 서명은 다음과 같습니다. 

```csharp
// System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
// Microsoft.SCIM.IRequest is defined in 
// Microsoft.SCIM.Service.  
// Microsoft.SCIM.Resource is defined in 
// Microsoft.SCIM.Schemas.  

Task<Resource> CreateAsync(IRequest<Resource> request);
```

사용자 프로비저닝 요청에서 리소스 인수의 값은 Microsoft.SCIM.Schemas 라이브러리에 정의된 Microsoft.SCIM.Core2EnterpriseUser 클래스의 인스턴스입니다.  사용자를 프로비저닝하기 위한 요청이 성공하는 경우 이 메서드의 구현은 Identifier 특성 값이 새로 프로비저닝된 사용자의 고유 식별자로 설정된 Microsoft.SCIM.Core2EnterpriseUser 클래스의 인스턴스를 반환해야 합니다.  

***예제 3. 사용자의 현재 상태 쿼리*** 

SCIM에 의해 제어되는 ID 저장소에 있는 것으로 알려진 사용자를 업데이트하기 위해 Azure Active Directory는 다음과 같은 요청으로 서비스에서 해당 사용자의 현재 상태를 요청합니다. 

```
GET ~/scim/Users/54D382A4-2050-4C03-94D1-E769F1D15682 HTTP/1.1
Authorization: Bearer ...
```

샘플 코드에서 요청은 서비스 공급자의 RetrieveAsync 메서드에 대한 호출로 변환됩니다. 해당 메서드의 서명은 다음과 같습니다. 

```csharp
// System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
// Microsoft.SCIM.IRequest is defined in 
// Microsoft.SCIM.Service.  
// Microsoft.SCIM.Resource and 
// Microsoft.SCIM.IResourceRetrievalParameters 
// are defined in Microsoft.SCIM.Schemas 

Task<Resource> RetrieveAsync(IRequest<IResourceRetrievalParameters> request);
```

사용자의 현재 상태를 검색하는 요청 예제에서 매개 변수 인수 값으로 제공되는 개체의 속성 값은 다음과 같습니다. 
  
* 식별자: "54D382A4-2050-4C03-94D1-E769F1D15682"
* SchemaIdentifier: "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"

***예제 4. 업데이트할 참조 특성의 값 쿼리*** 

참조 특성을 업데이트해야 하는 경우 Azure Active Directory는 서비스를 쿼리하여 서비스에 의해 제어되는 ID 저장소에 있는 참조 특성의 현재 값이 Azure Active Directory의 해당 특성 값과 이미 일치하는지를 확인합니다. 사용자의 경우 현재 값이 이 방식으로 쿼리된 유일한 특성은 관리자 특성입니다. 사용자 개체의 관리자 특성 값에 현재 특정 값이 있는지 여부를 결정하는 요청의 예는 다음과 같습니다. 샘플 코드에서 요청은 서비스 공급자의 QueryAsync 메서드에 대한 호출로 변환됩니다. 매개 변수 인수의 값으로 제공되는 개체의 속성 값은 다음과 같습니다. 
  
* parameters.AlternateFilters.Count: 2
* parameters.AlternateFilters.ElementAt(x).AttributePath: "ID"
* parameters.AlternateFilters.ElementAt(x).ComparisonOperator: ComparisonOperator.Equals
* parameters.AlternateFilter.ElementAt(x).ComparisonValue: "54D382A4-2050-4C03-94D1-E769F1D15682"
* parameters.AlternateFilters.ElementAt(y).AttributePath: "manager"
* parameters.AlternateFilters.ElementAt(y).ComparisonOperator: ComparisonOperator.Equals
* parameters.AlternateFilter.ElementAt(y).ComparisonValue: "2819c223-7f76-453a-919d-413861904646"
* parameters.RequestedAttributePaths.ElementAt(0): "ID"
* parameters.SchemaIdentifier: "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"

여기에서 필터 쿼리 매개 변수 식의 순서에 따라 인덱스 x의 값은 0이고 인덱스 y의 값은 1일 수 있습니다. 또는 x 값이 1이고 y 값이 0일 수 있습니다.   

***예제 5. 사용자를 업데이트 하기 위해 Azure AD에서 SCIM 서비스로 요청*** 

다음은 Azure Active Directory에서 SCIM 서비스로 사용자를 업데이트하는 요청의 예입니다. 

```
PATCH ~/scim/Users/54D382A4-2050-4C03-94D1-E769F1D15682 HTTP/1.1
Authorization: Bearer ...
Content-type: application/scim+json
{
    "schemas": 
    [
      "urn:ietf:params:scim:api:messages:2.0:PatchOp"],
    "Operations":
    [
      {
        "op":"Add",
        "path":"manager",
        "value":
          [
            {
              "$ref":"http://.../scim/Users/2819c223-7f76-453a-919d-413861904646",
              "value":"2819c223-7f76-453a-919d-413861904646"}]}]}
```

샘플 코드에서 요청은 서비스 공급자의 UpdateAsync 메서드에 대한 호출로 변환됩니다. 해당 메서드의 서명은 다음과 같습니다. 

```csharp
// System.Threading.Tasks.Tasks and 
// System.Collections.Generic.IReadOnlyCollection<T>  // are defined in mscorlib.dll.  
// Microsoft.SCIM.IRequest is defined in
// Microsoft.SCIM.Service.
// Microsoft.SCIM.IPatch, 
// is defined in Microsoft.SCIM.Protocol. 

Task UpdateAsync(IRequest<IPatch> request);
```

사용자를 업데이트하는 요청의 예에서 패치 인수의 값으로 제공되는 개체는 다음과 같은 속성 값이 적용됩니다. 

|인수|값|
|-|-|
|ResourceIdentifier.Identifier|"54D382A4-2050-4C03-94D1-E769F1D15682"|
|ResourceIdentifier.SchemaIdentifier|"urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"|
|(PatchRequest as PatchRequest2).Operations.Count|1|
|(PatchRequest as PatchRequest2).Operations.ElementAt(0).OperationName|OperationName.Add|
|(PatchRequest as PatchRequest2).Operations.ElementAt(0).Path.AttributePath|"manager"|
|(PatchRequest as PatchRequest2).Operations.ElementAt(0).Value.Count|1|
|(PatchRequest as PatchRequest2).Operations.ElementAt(0).Value.ElementAt(0).Reference|http://.../scim/Users/2819c223-7f76-453a-919d-413861904646|
|(PatchRequest as PatchRequest2).Operations.ElementAt(0).Value.ElementAt(0).Value| 2819c223-7f76-453a-919d-413861904646|

***예제 6. 사용자 프로비저닝 해제***

SCIM 서비스와 마주하는 ID 저장소에서 사용자의 프로비저닝을 해제하기 위해 AAD에서 다음과 같은 요청을 보냅니다.

```
DELETE ~/scim/Users/54D382A4-2050-4C03-94D1-E769F1D15682 HTTP/1.1
Authorization: Bearer ...
```

샘플 코드에서 요청은 서비스 공급자의 DeleteAsync 메서드에 대한 호출로 변환됩니다. 해당 메서드의 서명은 다음과 같습니다. 

```csharp
// System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
// Microsoft.SCIM.IRequest is defined in 
// Microsoft.SCIM.Service.  
// Microsoft.SCIM.IResourceIdentifier, 
// is defined in Microsoft.SCIM.Protocol. 

Task DeleteAsync(IRequest<IResourceIdentifier> request);
```

사용자의 프로비저닝을 해제하는 요청의 예에서 resourceIdentifier 인수의 값으로 제공되는 개체는 다음과 같은 속성 값이 적용됩니다. 

* ResourceIdentifier.Identifier: "54D382A4-2050-4C03-94D1-E769F1D15682"
* ResourceIdentifier.SchemaIdentifier: "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"

## <a name="integrate-your-scim-endpoint-with-the-aad-scim-client"></a>SCIM 엔드포인트를 AAD SCIM 클라이언트와 통합

[SCIM 2.0 프로토콜](https://tools.ietf.org/html/rfc7644)의 특정 프로필을 구현하는 애플리케이션에 할당된 사용자 및 그룹을 자동으로 프로비저닝하도록 Azure AD를 구성할 수 있습니다. 프로필의 세부 정보는 [Azure AD SCIM 구현 이해](#understand-the-aad-scim-implementation)에서 설명하고 있습니다.

애플리케이션 공급자 또는 애플리케이션 공급자의 설명서를 통해 이러한 요구 사항과의 호환성을 확인합니다.

> [!IMPORTANT]
> Azure AD SCIM 구현은 Azure AD 사용자 프로비저닝 서비스 위에 빌드되며, Azure AD와 대상 애플리케이션 간에 사용자를 지속적으로 유지하고 매우 구체적인 표준 작업을 구현하도록 디자인되었습니다. Azure AD SCIM 클라이언트의 동작을 이해하려면 해당 동작을 이해해야 합니다. 자세한 내용은 [프로비저닝 작동 방식](how-provisioning-works.md)의 [프로비저닝 작동 방식: 초기 및 증분](how-provisioning-works.md#provisioning-cycles-initial-and-incremental) 섹션을 참조하세요.

### <a name="getting-started"></a>시작

Azure AD 애플리케이션 갤러리에 있는 "비-갤러리 애플리케이션" 기능을 사용하여 이 문서에서 설명한 SCIM 프로필을 지원하는 애플리케이션을 Azure Active Directory에 연결할 수 있습니다. 일단 연결되면 Azure AD는 할당된 사용자 및 그룹에 대해 애플리케이션의 SCIM 엔드포인트를 쿼리하고, 할당 세부 정보에 따라 이러한 사용자 및 그룹을 만들거나 수정하는 동기화 프로세스를 40분마다 실행합니다.

**SCIM을 지원하는 애플리케이션을 연결하려면:**

1. [AAD 포털](https://aad.portal.azure.com)에 로그인합니다. [개발자 프로그램](https://developer.microsoft.com/office/dev-program)에 가입하면 P2 라이선스가 포함된 Azure Active Directory 평가판에 액세스할 수 있습니다.
1. 왼쪽 창에서 **엔터프라이즈 애플리케이션** 을 선택합니다. 갤러리에서 추가된 앱을 포함하여 구성된 모든 앱 목록이 표시됩니다.
1. **+ 새 애플리케이션** >  **+ 자신만의 애플리케이션을 만드세요.** 를 차례로 선택합니다.
1. 애플리케이션 이름을 입력하고, "*갤러리에 없는 다른 애플리케이션 통합*" 옵션을 선택한 다음, **추가** 를 선택하여 앱 개체를 만듭니다. 새 앱이 엔터프라이즈 애플리케이션 목록에 추가되고 해당 앱 관리 화면이 열립니다.

   ![Azure AD 애플리케이션 갤러리를 보여 주는 스크린샷](media/use-scim-to-provision-users-and-groups/scim-figure-2b-1.png)
   *Azure AD 애플리케이션 갤러리*

   > [!NOTE]
   > 이전 앱 갤러리 환경을 사용하는 경우 아래 화면 지침을 따르세요.
   
   ![Azure AD 이전 앱 갤러리 환경을 보여 주는 스크린샷](media/use-scim-to-provision-users-and-groups/scim-figure-2a.png)
   *Azure AD 이전 앱 갤러리 환경*

1. 앱 관리 화면의 왼쪽 패널에서 **프로비저닝** 을 선택합니다.
1. **프로비전 모드** 메뉴에서 **자동** 을 선택합니다.

   ![예: Azure Portal의 앱 프로비저닝 페이지](media/use-scim-to-provision-users-and-groups/scim-figure-2b.png)<br/>
   ‘Azure Portal에서 프로비저닝 구성’

1. **테넌트 URL** 필드에 애플리케이션의 SCIM 엔드포인트 URL을 입력합니다. 예: `https://api.contoso.com/scim/`
1. SCIM 엔드포인트에 Azure AD가 아닌 다른 발급자의 OAuth 전달자 토큰이 필요한 경우 필요한 OAuth 전달자 토큰을 **비밀 토큰** 필드(선택 사항)에 복사합니다. 이 필드를 비워 두면 Azure AD에 각 요청에 따라 Azure AD에서 발급한 OAuth 전달자 토큰이 포함됩니다. ID 공급자로 Azure AD를 사용하는 앱은 Azure AD에서 발급한 토큰의 유효성을 검사할 수 있습니다. 
   > [!NOTE]
   > 이 필드를 비워두고 Azure AD에서 생성한 토큰을 사용하는 것은 권장되지 ***않습니다***. 이 옵션은 주로 테스트 목적으로 사용할 수 있습니다.
1. **연결 테스트** 를 선택하여 Azure Active Directory에서 SCIM 엔드포인트에 연결을 시도합니다. 시도가 실패하면 오류 정보가 표시됩니다.  

    > [!NOTE]
    > **테스트 연결** 은 Azure AD 구성에서 선택된 일치하는 속성으로 임의 GUID를 사용하여 존재하지 않는 사용자의 SCIM 엔드포인트를 쿼리합니다. 예상되는 올바른 응답은 SCIM ListResponse 메시지가 비어 있는 HTTP 200 OK입니다.

1. 애플리케이션에 연결 시도가 성공하면 **저장** 을 선택하여 관리자 자격 증명을 저장합니다.
1. **매핑** 섹션에는 선택 가능한 [특성 매핑](customize-application-attributes.md) 집합이 두 개 있는데, 하나는 사용자 개체용이고 다른 하나는 그룹 개체용입니다. 각 특성 매핑을 선택하여 Azure Active Directory에서 앱으로 동기화되는 특성을 검토합니다. **일치** 속성으로 선택한 특성은 업데이트 작업 시 앱의 사용자와 그룹을 일치시키는 데 사용됩니다. 변경 내용을 커밋하려면 **저장** 을 선택합니다.

    > [!NOTE]
    > 필요에 따라 "그룹" 매핑을 사용하지 않도록 설정하여 그룹 개체의 동기화를 비활성화할 수 있습니다.

1. **설정** 아래의 **범위** 필드는 동기화되는 사용자 및 그룹을 정의합니다. **할당된 사용자 및 그룹만 동기화**(권장)를 선택하면 **사용자 및 그룹** 탭에서 할당된 사용자 및 그룹만 동기화됩니다.
1. 구성이 완료되면 **프로비저닝 상태** 를 **켜기** 로 설정합니다.
1. **저장** 을 선택하여 Azure AD 프로비저닝 서비스를 시작합니다.
1. 할당된 사용자 및 그룹만 동기화하는 경우(권장) **사용자 및 그룹** 탭을 선택하고 동기화하려는 사용자 또는 그룹을 할당합니다.

초기 추기가 시작되면 왼쪽 패널의 **프로비저닝 로그** 탭을 선택하여 진행 상황을 모니터링할 수 있습니다. 이 탭에는 앱의 프로비저닝 서비스에서 수행하는 모든 작업이 표시됩니다. Azure AD 프로비저닝 로그를 읽는 방법에 대한 자세한 내용은 [자동 사용자 계정 프로비저닝에 대한 보고](check-status-user-account-provisioning.md)를 참조하세요.

> [!NOTE]
> 초기 주기는 서비스가 실행되는 동안 약 40분마다 발생하는 후속 동기화보다 더 오래 걸립니다.

## <a name="publish-your-application-to-the-aad-application-gallery"></a>AAD 애플리케이션 갤러리에 애플리케이션 게시

둘 이상의 테넌트가 사용할 애플리케이션을 빌드하는 경우 Azure AD 애플리케이션 갤러리에서 사용하도록 할 수 있습니다. 그러면 조직은 쉽게 애플리케이션을 찾고 프로비저닝을 구성할 수 있습니다. Azure AD 갤러리에 앱을 게시하고 다른 사용자가 프로비저닝을 쉽게 사용할 수 있도록 합니다. [여기](../develop/v2-howto-app-gallery-listing.md)서 단계를 확인하세요. Microsoft는 사용자와 협력하여 애플리케이션을 갤러리에 통합하고, 엔드포인트를 테스트하며, 고객이 사용할 수 있도록 온보딩 [설명서](../saas-apps/tutorial-list.md)를 제공합니다.

### <a name="gallery-onboarding-checklist"></a>갤러리 온보딩 검사 목록
검사 목록을 사용하여 애플리케이션을 빠르게 온보딩하고 고객이 원활한 배포 환경을 갖추도록 합니다. 갤러리에 온보딩하면 해당 정보가 수집됩니다. 
> [!div class="checklist"]
> * [SCIM 2.0](#understand-the-aad-scim-implementation) 사용자 및 그룹 엔드포인트 지원(하나만 필요하지만 둘 다 권장됨)
> * 사용자 및 그룹이 지연 없이 프로비저닝 및 프로비저닝 해제되도록 한 테넌트에 대해 초당 최소 25개의 요청을 지원합니다(필수).
> * 고객이 갤러리 온보딩을 게시할 수 있도록 엔지니어링 및 지원 연락처 설정(필수)
> * 3 애플리케이션에 대해 만료되지 않은 테스트 자격 증명(필수)
> * 아래 설명된 대로 OAuth 인증 코드 부여 또는 수명이 긴 토큰 지원(필수)
> * 고객이 갤러리 온보딩을 게시할 수 있도록 엔지니어링 및 지원 연락처 설정(필수)
> * [지원 스키마 검색(필수)](https://tools.ietf.org/html/rfc7643#section-6)
> * 단일 PATCH를 사용하여 여러 그룹 멤버 자격 업데이트 지원
> * 공개적으로 SCIM 엔드포인트 문서화

### <a name="authorization-to-provisioning-connectors-in-the-application-gallery"></a>애플리케이션 갤러리에서 커넥터 프로비저닝 권한 부여
SCIM 사양에서는 인증 및 권한 부여에 대한 SCIM 관련 체계를 정의하지 않으며 기존 산업 표준을 사용합니다.

|권한 부여 방법|장점|단점|지원|
|--|--|--|--|
|사용자 이름 및 비밀번호(Azure AD에서 권장되지 않거나 지원되지 않음)|쉬운 구현|안전하지 않음 - [암호는 중요하지 않습니다.](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/your-pa-word-doesn-t-matter/ba-p/731984)|새 갤러리 또는 비갤러리 앱에는 지원되지 않습니다.|
|수명이 긴 전달자 토큰|수명이 긴 토큰에는 사용자가 없어도 됩니다. 프로비저닝을 설정할 때 관리자가 쉽게 사용할 수 있습니다.|수명이 간 토큰은 메일처럼 안전하지 않은 방법을 사용하지 않으면 관리자와 공유하기 어려울 수 있습니다. |갤러리 및 비갤러리 앱에 지원됩니다. |
|OAuth 인증 코드 부여|액세스 토큰은 비밀번호보다 수명이 훨씬 짧으며 수명이 긴 전달자 토큰에 없는 자동화된 새로 고침 메커니즘이 있습니다.  책임 수준을 추가하는 초기 권한 부여 중에 실제 사용자가 있어야 합니다. |사용자가 있어야 합니다. 사용자가 조직을 떠나면 토큰이 유효하지 않으므로 권한 부여를 다시 완료해야 합니다.|갤러리 앱은 지원되지만 비갤러리 앱은 지원되지 않습니다. 그러나 단기 테스트 목적으로 UI에서 액세스 토큰을 비밀 토큰으로 제공할 수 있습니다. 갤러리 앱에서 구성 가능한 권한 부여/토큰 URL을 지원하는 것 외에도 비갤러리에서 OAuth 코드 권한 부여를 지원하는 것은 백로그에 있습니다.|
|OAuth 클라이언트 자격 증명 부여|액세스 토큰은 비밀번호보다 수명이 훨씬 짧으며 수명이 긴 전달자 토큰에 없는 자동화된 새로 고침 메커니즘이 있습니다. 인증 코드 부여 및 클라이언트 자격 증명 부여는 모두 동일한 유형의 액세스 토큰을 만들기 때문에 이러한 메서드 간에 이동하는 것은 API에 인식됩니다.  프로비저닝은 완전히 자동화할 수 있으며 사용자 개입 없이도 새 토큰을 자동으로 요청할 수 있습니다. ||갤러리 앱 및 비갤러리 앱에는 지원되지 않습니다. 지원은 백로그에 있습니다.|

> [!NOTE]
> AAD 프로비저닝 구성 사용자 지정 앱 UI에서 토큰 필드를 비워 두지 않는 것이 좋습니다. 생성된 토큰은 주로 테스트 목적으로 사용할 수 있습니다.

### <a name="oauth-code-grant-flow"></a>OAuth 코드 권한 부여 흐름

프로비저닝 서비스는 [인증 코드 권한 부여](https://tools.ietf.org/html/rfc6749#page-24)를 지원하며, 앱을 갤러리에 게시하기 위한 요청을 제출하면 Microsoft 팀에서 사용자와 협력하여 다음 정보를 수집합니다.

- **권한 부여 URL** - 사용자 에이전트 리디렉션을 통해 리소스 소유자로부터 권한을 부여받기 위한 클라이언트의 URL입니다. 액세스 권한을 부여하기 위해 사용자가 URL로 리디렉션됩니다. 

- **토큰 교환 URL** - 클라이언트가 일반적으로 클라이언트 인증을 사용하여 액세스 토큰에 대한 권한 부여를 교환하기 위한 클라이언트의 URL입니다.

- **클라이언트 ID** - 권한 부여 서버는 클라이언트에서 제공한 등록 정보를 나타내는 고유 문자열인 클라이언트 식별자를 등록된 클라이언트에 발급합니다.  클라이언트 식별자는 암호가 아닙니다. 리소스 소유자에게 표시되며 클라이언트 인증을 위해 단독으로 사용해서는 **안 됩니다**.  

- **클라이언트 암호** - 권한 부여 서버에서 생성된 비밀이며, 권한 부여 서버에만 알려진 고유한 값이어야 합니다. 

> [!NOTE]
> **권한 부여 URL** 및 **토큰 교환 URL** 은 현재 테넌트별로 구성할 수 없습니다.

> [!NOTE]
> OAuth v1은 클라이언트 암호 공개로 인해 지원되지 않습니다. OAuth v2는 지원됩니다.  

모범 사례(권장되지만 필수는 아님):
* 여러 리디렉션 URL을 지원합니다. 관리자는 “portal.azure.com” 및 “aad.portal.azure.com”에서 모두 프로비저닝을 구성할 수 있습니다. 여러 리디렉션 URL을 지원하기 때문에 사용자가 두 포털에서 액세스 권한을 부여할 수 있습니다.
* 가동 중지 시간 없이 쉽게 갱신할 수 있도록 여러 비밀을 지원합니다. 

#### <a name="how-to-setup-oauth-code-grant-flow"></a>OAuth 코드 권한 부여 흐름을 설정하는 방법

1. Azure Portal에 로그인하고, **엔터프라이즈 애플리케이션** > **애플리케이션** > **프로비저닝** 으로 차례로 이동하고, **권한 부여** 를 선택합니다.

   1. Azure Portal은 사용자를 권한 부여 URL(타사 앱에 대한 로그인 페이지)로 리디렉션합니다.

   1. 관리자는 타사 애플리케이션에 자격 증명을 제공합니다. 

   1. 타사 앱은 사용자를 다시 Azure Portal로 리디렉션하고 부여 코드를 제공합니다. 

   1. Azure AD 프로비저닝 서비스는 토큰 URL을 호출하고 부여 코드를 제공합니다. 타사 애플리케이션은 액세스 토큰, 새로 고침 토큰 및 만료 날짜를 사용하여 응답합니다.

1. 프로비저닝 주기가 시작되면 서비스는 현재 액세스 토큰이 유효한지 확인하고 필요한 경우 새 토큰으로 교환합니다. 액세스 토큰은 앱에 대한 각 요청에서 제공되며 요청의 유효성은 각 요청 전에 확인됩니다.

> [!NOTE]
> 비갤러리 애플리케이션에서 OAuth를 설정할 수는 없지만, 권한 부여 서버에서 액세스 토큰을 수동으로 생성하여 비밀 토큰으로 비갤러리 애플리케이션에 입력할 수 있습니다. 이렇게 하면 OAuth 코드 권한 부여를 지원하는 앱 갤러리에 온보딩하기 전에 SCIM 서버와 AAD SCIM 클라이언트의 호환성을 확인할 수 있습니다.  

**수명이 긴 OAuth 전달자 토큰:** 애플리케이션에서 OAuth 인증 코드 권한 부여 흐름을 지원하지 않는 경우 관리자가 프로비저닝 통합을 설정하는 데 사용할 수 있는 수명이 긴 OAuth 전달자 토큰을 대신 생성합니다. 토큰은 영구적이어야 합니다. 그렇지 않은 경우 토큰이 만료되면 프로비저닝 작업이 [격리](application-provisioning-quarantine-status.md)됩니다.

추가 인증 및 권한 부여 방법에 대한 자세한 내용은 [UserVoice](https://aka.ms/appprovisioningfeaturerequest)에 알려주세요.

### <a name="gallery-go-to-market-launch-check-list"></a>갤러리 시장 진출 시작 검사 목록
공동 통합에 대한 인식과 수요를 높이려면 기존 설명서를 업데이트하고 마케팅 채널에서 통합을 확대하는 것이 좋습니다.  아래는 시작을 지원하기 위해 완료하면 좋은 검사 목록 활동입니다.

> [!div class="checklist"]
> * 영업 및 고객 지원 팀에서 통합 기능을 인식하고, 준비하고, 이에 대해 말할 수 있는지 확인합니다. 팀에 간략하게 설명하고, FAQ를 제공하며, 통합을 영업 자료에 포함합니다. 
> * 공동 통합, 이점 및 시작 방법을 설명하는 블로그 게시물 또는 보도 자료를 만듭니다. [예: Imprivata 및 Azure Active Directory 보도 자료](https://www.imprivata.com/company/press/imprivata-introduces-iam-cloud-platform-healthcare-supported-microsoft) 
> * Twitter, Facebook 또는 LinkedIn과 같은 소셜 미디어를 활용하여 고객과의 통합을 촉진하세요. 게시물을 리트윗할 수 있도록 @AzureAD를 포함해야 합니다. [예: Imprivata Twitter 게시물](https://twitter.com/azuread/status/1123964502909779968)
> * 공동 통합에 대한 가용성을 포함하도록 마케팅 페이지/웹 사이트(예: 통합 페이지, 파트너 페이지, 가격 책정 페이지 등)를 만들거나 업데이트합니다. [예: Pingboard 통합 페이지](https://pingboard.com/org-chart-for), [Smartsheet 통합 페이지](https://www.smartsheet.com/marketplace/apps/microsoft-azure-ad), [Monday.com 가격 책정 페이지](https://monday.com/pricing/) 
> * 고객이 시작하는 방법에 대한 도움말 센터 문서 또는 기술 문서를 생성합니다. [예: Envoy + Microsoft Azure Active Directory 통합](https://envoy.help/en/articles/3453335-microsoft-azure-active-directory-integration/
) 
> * 고객 커뮤니케이션(월간 뉴스 레터, 메일 캠페인, 제품 릴리스 노트)을 통해 고객에게 새로운 통합을 알리세요. 

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [샘플 SCIM 엔드포인트 개발](use-scim-to-build-users-and-groups-endpoints.md)
> [SaaS 앱에 대한 사용자 프로비저닝 및 프로비저닝 해제 자동화](user-provisioning.md)
> [사용자 프로비저닝에 대한 특성 매핑 사용자 지정](customize-application-attributes.md)
> [특성 매핑 식 작성](functions-for-customizing-application-data.md)
> [사용자 프로비저닝에 대한 범위 지정 필터](define-conditional-rules-for-provisioning-user-accounts.md)
> [계정 프로비저닝 알림](user-provisioning.md)
> [SaaS 앱을 통합하는 방법에 대한 자습서 목록](../saas-apps/tutorial-list.md)
