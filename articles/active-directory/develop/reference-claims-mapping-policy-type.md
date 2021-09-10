---
title: 클레임 매핑 정책
titleSuffix: Microsoft identity platform
description: 특정 애플리케이션에 발급된 토큰에서 내보내진 클레임을 수정하는 데 사용되는 클레임 매핑 정책 유형에 대해 알아봅니다.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev
ms.workload: identity
ms.topic: reference
ms.date: 07/01/2021
ms.author: ryanwi
ms.reviewer: paulgarn, hirsin, jeedes, luleon
ms.openlocfilehash: 52de257461c796f7a477091801f4bdc52f46f5c8
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2021
ms.locfileid: "123101535"
---
# <a name="claims-mapping-policy-type"></a>클레임 매핑 정책 유형

Azure AD에서 **정책** 개체는 조직에 있는 개별 애플리케이션 또는 모든 애플리케이션에 적용되는 규칙 집합을 나타냅니다. 각 유형의 정책에는 할당된 개체에 적용되는 속성 집합이 포함된 고유한 구조가 있습니다.

클레임 매핑 정책은 특정 애플리케이션에 발급된 [토큰에 내보내진 클레임을 수정](active-directory-claims-mapping.md)하는 **정책** 개체 유형입니다.

## <a name="claim-sets"></a>클레임 집합

토큰에 사용되는 시기와 방법을 정의하는 특정 클레임 집합이 있습니다.

| 클레임 집합 | Description |
|---|---|
| 핵심 클레임 집합 | 정책에 관계없이 모든 토큰에 포함됩니다. 또한 이러한 클레임은 제한된 것으로 간주되며 수정할 수 없습니다. |
| 기본 클레임 집합 | 핵심 클레임 집합뿐 아니라 토큰에서 기본적으로 내보내지는 클레임을 포함합니다. 클레임 매핑 정책을 사용하여 기본 클레임을 [생략하거나 수정](active-directory-claims-mapping.md#omit-the-basic-claims-from-tokens)할 수 있습니다. |
| 제한된 클레임 집합 | 정책을 사용하여 수정할 수 없습니다. 데이터 원본을 변경할 수 없으며, 이러한 클레임을 생성할 때 변환이 적용됩니다. |

### <a name="table-1-json-web-token-jwt-restricted-claim-set"></a>테이블 1: JWT(JSON Web Token) 제한된 클레임 집합

| 클레임 형식(이름) |
| ----- |
| _claim_names |
| _claim_sources |
| access_token |
| account_type |
| acr |
| actor |
| actortoken |
| aio |
| altsecid |
| amr |
| app_chain |
| app_displayname |
| app_res |
| appctx |
| appctxsender |
| appId |
| appidacr |
| 어설션 |
| at_hash |
| aud |
| auth_data |
| auth_time |
| authorization_code |
| azp |
| azpacr |
| c_hash |
| ca_enf |
| cc |
| cert_token_use |
| client_id |
| cloud_graph_host_name |
| cloud_instance_name |
| cnf |
| 코드 |
| controls |
| credential_keys |
| csr |
| csr_type |
| deviceId |
| dns_names |
| domain_dns_name |
| domain_netbios_name |
| e_exp |
| 이메일 |
| 엔드포인트(endpoint) |
| enfpolids |
| exp |
| expires_on |
| grant_type |
| graph |
| group_sids |
| groups |
| hasgroups |
| hash_alg |
| home_oid |
| `http://schemas.microsoft.com/ws/2008/06/identity/claims/authenticationinstant` |
| `http://schemas.microsoft.com/ws/2008/06/identity/claims/authenticationmethod` |
| `http://schemas.microsoft.com/ws/2008/06/identity/claims/expiration` |
| `http://schemas.microsoft.com/ws/2008/06/identity/claims/expired` |
| `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress` |
| `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name` |
| `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier` |
| iat |
| identityprovider |
| idp |
| in_corp |
| instance |
| ipaddr |
| isbrowserhostedapp |
| iss |
| jwk |
| key_id |
| key_type |
| mam_compliance_url |
| mam_enrollment_url |
| mam_terms_of_use_url |
| mdm_compliance_url |
| mdm_enrollment_url |
| mdm_terms_of_use_url |
| nameid |
| nbf |
| netbios_name |
| nonce |
| oid |
| on_prem_id |
| onprem_sam_account_name |
| onprem_sid |
| openid2_id |
| password |
| polids |
| pop_jwk |
| preferred_username |
| previous_refresh_token |
| primary_sid |
| puid |
| pwd_exp |
| pwd_url |
| redirect_uri |
| refresh_token |
| refreshtoken |
| request_nonce |
| resource |
| 역할(role) |
| roles |
| scope |
| scp |
| sid |
| 서명 |
| signin_state |
| src1 |
| src2 |
| sub |
| tbid |
| tenant_display_name |
| tenant_region_scope |
| thumbnail_photo |
| tid |
| tokenAutologonEnabled |
| trustedfordelegation |
| unique_name |
| upn |
| user_setting_sync_url |
| 사용자 이름 |
| uti |
| ver |
| verified_primary_email |
| verified_secondary_email |
| wids |
| win_ver |

### <a name="table-2-saml-restricted-claim-set"></a>표 2: SAML 제한된 클레임 집합

| 클레임 형식(URI) |
| ----- |
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/expiration`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/expired`|
|`http://schemas.microsoft.com/identity/claims/accesstoken`|
|`http://schemas.microsoft.com/identity/claims/openid2_id`|
|`http://schemas.microsoft.com/identity/claims/identityprovider`|
|`http://schemas.microsoft.com/identity/claims/objectidentifier`|
|`http://schemas.microsoft.com/identity/claims/puid`|
|`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier [MR1]`|
|`http://schemas.microsoft.com/identity/claims/tenantid`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/authenticationinstant`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/authenticationmethod`|
|`http://schemas.microsoft.com/accesscontrolservice/2010/07/claims/identityprovider`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/groups`|
|`http://schemas.microsoft.com/claims/groups.link`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/role`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/wids`|
|`http://schemas.microsoft.com/2014/09/devicecontext/claims/iscompliant`|
|`http://schemas.microsoft.com/2014/02/devicecontext/claims/isknown`|
|`http://schemas.microsoft.com/2012/01/devicecontext/claims/ismanaged`|
|`http://schemas.microsoft.com/2014/03/psso`|
|`http://schemas.microsoft.com/claims/authnmethodsreferences`|
|`http://schemas.xmlsoap.org/ws/2009/09/identity/claims/actor`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/samlissuername`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/confirmationkey`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/primarygroupsid`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/primarysid`|
|`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/authorizationdecision`|
|`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/authentication`|
|`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/sid`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/denyonlyprimarygroupsid`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/denyonlyprimarysid`|
|`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/denyonlysid`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/denyonlywindowsdevicegroup`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsdeviceclaim`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsdevicegroup`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsfqbnversion`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/windowssubauthority`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsuserclaim`|
|`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/x500distinguishedname`|
|`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid`|
|`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/spn`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/ispersistent`|
|`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/privatepersonalidentifier`|
|`http://schemas.microsoft.com/identity/claims/scope`|

## <a name="claims-mapping-policy-properties"></a>클레임 매핑 정책 속성

내보내는 클레임 및 데이터의 출처를 제어하려면 클레임 매핑 정책의 속성을 사용합니다. 정책을 설정하지 않으면 시스템은 핵심 클레임 집합, 기본 클레임 집합 및 애플리케이션이 수신하도록 선택한 [선택적 클레임](active-directory-optional-claims.md)이 포함된 토큰을 발급합니다.

> [!NOTE]
> 핵심 클레임 집합의 클레임은 이 속성의 설정값에 관계없이 모든 토큰에 표시됩니다.

### <a name="include-basic-claim-set"></a>기본 클레임 집합 포함

**문자열:** IncludeBasicClaimSet

**데이터 형식:** 부울(True 또는 False)

**요약:** 이 속성은 기본 클레임 집합이 이 정책의 영향을 받는 토큰에 포함되는지 여부를 결정합니다.

- True로 설정된 경우 기본 클레임 집합의 모든 클레임이 정책의 영향을 받는 토큰에 내보내집니다.
- False로 설정된 경우 기본 클레임 집합의 클레임은 동일한 정책의 클레임 스키마 속성에 개별적으로 추가되지 않는 한 토큰에 포함되지 않습니다.



### <a name="claims-schema"></a>클레임 스키마

**문자열:** ClaimsSchema

**데이터 형식:** 하나 이상의 클레임 스키마 항목이 있는 JSON Blob입니다.

**요약:** 이 속성은 기본 클레임 집합 및 핵심 클레임 집합 외에도 정책의 영향을 받는 토큰에 포함될 클레임을 정의합니다.
이 속성에 정의된 각 클레임 스키마 항목의 경우 특정 정보가 필요합니다. 데이터의 출처(**Value**, **Source/ID pair** 또는 **Source/ExtensionID pair**) 및 데이터를 내보내는 클레임(**클레임 유형**)을 지정합니다.

### <a name="claim-schema-entry-elements"></a>클레임 스키마 항목 요소

**Value:** Value 요소는 클레임에 내보내질 데이터로 정적 값을 정의합니다.

**Source/ID 쌍:** : Source 및 ID 요소는 클레임의 데이터가 제공되는 위치를 정의합니다.

**Source/ExtensionID 쌍:** Source 및 ExtensionID 요소는 클레임의 데이터가 원본이 되는 디렉터리 스키마 확장 특성을 정의합니다. 자세한 내용은 [클레임에서 디렉터리 스키마 확장 특성 사용](active-directory-schema-extensions.md)을 참조하세요.

Source 요소는 다음 중 하나로 설정합니다.

- “user”: 클레임의 데이터가 User 개체의 속성입니다.
- “application”: 클레임의 데이터가 애플리케이션(클라이언트) 서비스 주체의 속성입니다.
- “resource”: 클레임의 데이터가 리소스 서비스 주체의 속성입니다.
- “audience”: 클레임의 데이터가 토큰의 대상 그룹인 서비스 주체(클라이언트 또는 리소스 서비스 주체)의 속성입니다.
- “company”: 클레임의 데이터가 리소스 테넌트에 대한 Company 개체의 속성입니다.
- “transformation”: 클레임의 데이터가 클레임 변환에서 제공됩니다(이 문서의 뒷부분에 있는 “클레임 변환” 섹션 참조).

원본이 변환인 경우 **TransformationID** 요소도 이 클레임 정의에 포함되어야 합니다.

ID 요소는 클레임의 값을 제공할 원본의 속성을 식별합니다. 다음 표는 각 Source 값에 유효한 ID 값을 나열합니다.

> [!WARNING]
> 현재 사용자 개체의 사용 가능한 다중값 클레임 원본은 AADConnect에서 동기화된 다중값 확장 특성이 유일합니다.  OtherMails 및 태그와 같은 다른 속성은 다중값이지만 원본으로 선택하는 경우에는 하나의 값만 내보내집니다.

#### <a name="table-3-valid-id-values-per-source"></a>표 3: 원본별 유효한 ID 값

| 원본 | ID | Description |
|-----|-----|-----|
| 사용자 | surname | 성 |
| 사용자 | givenname | 이름 |
| 사용자 | displayname | 표시 이름 |
| 사용자 | objectid | ObjectID |
| 사용자 | mail | 메일 주소 |
| 사용자 | userprincipalname | 사용자 계정 이름 |
| 사용자 | department|department|
| 사용자 | onpremisessamaccountname | 온-프레미스 SAM 계정 이름 |
| 사용자 | netbiosname| NetBios 이름 |
| 사용자 | dnsdomainname | DNS 도메인 이름 |
| 사용자 | onpremisesecurityidentifier | 온-프레미스 보안 식별자 |
| 사용자 | companyname| 조직 이름 |
| 사용자 | streetaddress | 주소 |
| 사용자 | postalcode | 우편 번호 |
| 사용자 | preferredlanguage | 기본 설정 언어 |
| 사용자 | onpremisesuserprincipalname | 온-프레미스 UPN |
| 사용자 | mailNickname | 메일 애칭 |
| 사용자 | extensionattribute1 | 확장 특성 1 |
| 사용자 | extensionattribute2 | 확장 특성 2 |
| 사용자 | extensionattribute3 | 확장 특성 3 |
| 사용자 | extensionattribute4 | 확장 특성 4 |
| 사용자 | extensionattribute5 | 확장 특성 5 |
| 사용자 | extensionattribute6 | 확장 특성 6 |
| 사용자 | extensionattribute7 | 확장 특성 7 |
| 사용자 | extensionattribute8 | 확장 특성 8 |
| 사용자 | extensionattribute9 | 확장 특성 9 |
| 사용자 | extensionattribute10 | 확장 특성 10 |
| 사용자 | extensionattribute11 | 확장 특성 11 |
| 사용자 | extensionattribute12 | 확장 특성 12 |
| 사용자 | extensionattribute13 | 확장 특성 13 |
| 사용자 | extensionattribute14 | 확장 특성 14 |
| 사용자 | extensionattribute15 | 확장 특성 15 |
| 사용자 | othermail | 다른 메일 |
| 사용자 | country | 국가/지역 |
| 사용자 | city | City |
| 사용자 | state | 시스템 상태 |
| 사용자 | jobtitle | 직위 |
| 사용자 | employeeid | 직원 ID |
| 사용자 | facsimiletelephonenumber | 팩스 번호 |
| 사용자 | assignedroles | 사용자에게 할당된 앱 역할 목록|
| 애플리케이션, 리소스, 대상 그룹 | displayname | 표시 이름 |
| 애플리케이션, 리소스, 대상 그룹 | objectid | ObjectID |
| 애플리케이션, 리소스, 대상 그룹 | tags | 서비스 주체 태그 |
| 회사 | tenantcountry | 테넌트의 국가/지역 |

**TransformationID:** TransformationID 요소는 Source 요소가 “transformation”으로 설정된 경우에만 제공해야 합니다.

- 이 요소는 이 클레임에 대한 데이터가 생성되는 방식을 정의하는 **ClaimsTransformation** 속성에서 변환 항목의 ID 요소와 일치해야 합니다.

**클레임 유형:** **JwtClaimType** 및 **SamlClaimType** 요소는 이 클레임 스키마 항목이 참조하는 클레임을 정의합니다.

- JwtClaimType에는 JWT로 내보낼 클레임 이름이 포함되어야 합니다.
- SamlClaimType에는 SAML 토큰으로 내보낼 클레임 URI가 포함되어야 합니다.

* **onPremisesUserPrincipalName 특성:** 대체 ID를 사용하면 온-프레미스 특성 userPrincipalName은 Azure AD 특성 onPremisesUserPrincipalName과 동기화됩니다. 이 특성은 대체 ID가 구성되어야만 사용할 수 있지만 MS Graph 베타(https://graph.microsoft.com/beta/me/ )를 통해서도 사용할 수 있습니다.

> [!NOTE]
> 제한된 클레임 집합에 있는 클레임의 이름 및 URI는 클레임 형식 요소에 사용할 수 없습니다. 자세한 내용은 이 문서의 뒷부분에 나오는 "예외 및 제한 사항" 섹션을 참조하세요.

### <a name="claims-transformation"></a>클레임 변환

**문자열:** ClaimsTransformation

**데이터 형식:** 하나 이상의 변환 항목이 있는 JSON Blob입니다.

**요약:** 클레임 스키마에 지정된 클레임에 대한 출력 데이터를 생성하기 위해 이 속성을 사용하여 원본 데이터에 일반 변환을 적용합니다.

**ID:** ID 요소를 사용하여 TransformationID 클레임 스키마 항목에서 이 변환 항목을 참조합니다. 이 값은 이 정책 내에서 각 변환 항목에 고유해야 합니다.

**TransformationMethod:** TransformationMethod 요소는 클레임에 대한 데이터를 생성하기 위해 수행할 작업을 식별합니다.

선택한 방법에 따라 입력 및 출력 집합이 예상됩니다. **InputClaims**, **InputParameters** 및 **OutputClaims** 요소를 사용하여 입력과 출력을 정의합니다.

#### <a name="table-4-transformation-methods-and-expected-inputs-and-outputs"></a>표 4: 변환 메서드와 예상 입력 및 출력

|TransformationMethod|예상 입력|예상 출력|Description|
|-----|-----|-----|-----|
|Join|string1, string2, 구분 기호|outputClaim|구분 기호를 사용하여 입력 문자열을 조인합니다. 예: string1:"foo@bar.com" , string2:"sandbox" , separator:"."는 outputClaim:"foo@bar.com.sandbox"를 생성합니다.|
|ExtractMailPrefix|이메일 또는 UPN|추출된 문자열|ExtensionAttributes 1-15 또는 사용자의 UPN 또는 이메일 주소 값을 저장하는 다른 스키마 확장(예: johndoe@contoso.com)입니다. 메일 주소의 로컬 부분을 추출합니다. 예: mail:"foo@bar.com"은 outputClaim:"foo"를 생성합니다. \@ 기호가 없으면 원본 입력 문자열이 현재 그대로 반환됩니다.|

**InputClaims:** InputClaims 요소를 사용하여 클레임 스키마 항목의 데이터를 변환에 전달합니다. 이 요소에는 두 개의 특성인 **ClaimTypeReferenceId** 및 **TransformationClaimType** 이 있습니다.

- **ClaimTypeReferenceId** 가 클레임 스키마 항목의 ID 요소와 조인되어 적절한 입력 클레임을 찾습니다.
- **TransformationClaimType** 은 이 입력에 고유 이름을 지정하는 데 사용됩니다. 이 이름은 변환 방법에 대한 예상 입력 중 하나와 일치해야 합니다.

**InputParameters:** InputParameters 요소를 사용하여 상수 값을 변환에 전달합니다. 이 요소에는 두 개의 특성인 **Value** 및 **ID** 가 있습니다.

- **Value** 는 전달할 실제 상수 값입니다.
- **ID** 는 입력에 고유 이름을 지정하는 데 사용됩니다. 이름은 변환 방법에 필요한 입력 중 하나와 일치해야 합니다.

**OutputClaims:** OutputClaims 요소를 사용하여 변환에 의해 생성된 데이터를 보관하고 클레임 스키마 항목에 연결합니다. 이 요소에는 두 개의 특성인 **ClaimTypeReferenceId** 및 **TransformationClaimType** 이 있습니다.

- **ClaimTypeReferenceId** 가 클레임 스키마 항목의 ID와 조인되어 적절한 출력 클레임을 찾습니다.
- **TransformationClaimType** 은 출력에 고유 이름을 지정하는 데 사용됩니다. 이름은 변환 방법에 필요한 출력 중 하나와 일치해야 합니다.

### <a name="exceptions-and-restrictions"></a>예외 및 제한 사항

**SAML NameID 및 UPN:** : NameID 및 UPN 값이 제공되는 특성과 허용되는 클레임 변환은 제한됩니다. 허용되는 값을 확인하려면 표 5와 6을 참조하세요.

#### <a name="table-5-attributes-allowed-as-a-data-source-for-saml-nameid"></a>표 5: SAML NameID에 대한 데이터 원본으로 허용되는 특성

|원본|ID|Description|
|-----|-----|-----|
| 사용자 | mail|메일 주소|
| 사용자 | userprincipalname|사용자 계정 이름|
| 사용자 | onpremisessamaccountname|온-프레미스 SAM 계정 이름|
| 사용자 | employeeid|직원 ID|
| 사용자 | extensionattribute1 | 확장 특성 1 |
| 사용자 | extensionattribute2 | 확장 특성 2 |
| 사용자 | extensionattribute3 | 확장 특성 3 |
| 사용자 | extensionattribute4 | 확장 특성 4 |
| 사용자 | extensionattribute5 | 확장 특성 5 |
| 사용자 | extensionattribute6 | 확장 특성 6 |
| 사용자 | extensionattribute7 | 확장 특성 7 |
| 사용자 | extensionattribute8 | 확장 특성 8 |
| 사용자 | extensionattribute9 | 확장 특성 9 |
| 사용자 | extensionattribute10 | 확장 특성 10 |
| 사용자 | extensionattribute11 | 확장 특성 11 |
| 사용자 | extensionattribute12 | 확장 특성 12 |
| 사용자 | extensionattribute13 | 확장 특성 13 |
| 사용자 | extensionattribute14 | 확장 특성 14 |
| 사용자 | extensionattribute15 | 확장 특성 15 |

#### <a name="table-6-transformation-methods-allowed-for-saml-nameid"></a>표 6: SAML NameID에 허용되는 변환 메서드

| TransformationMethod | 제한 |
| ----- | ----- |
| ExtractMailPrefix | None |
| Join | 조인되는 접미사는 리소스 테넌트의 확인된 도메인이어야 합니다. |

## <a name="next-steps"></a>다음 단계

- PowerShell을 사용하여 테넌트의 특정 애플리케이션에 대한 토큰에서 내보낸 클레임을 사용자 지정하는 방법을 알아보려면 [방법: 테넌트의 특정 앱에 대한 토큰에 내보낸 클레임 사용자 지정](active-directory-claims-mapping.md)을 참조하세요.
- Azure Portal을 통해 SAML 토큰에서 발급된 클레임을 사용자 지정하는 방법에 대한 자세한 내용은 [방법: 엔터프라이즈 애플리케이션에 대한 SAML 토큰에 발급된 클레임 사용자 지정](active-directory-saml-claims-customization.md)을 참조하세요.
- 확장 특성에 대해 자세히 알아보려면 [클레임에서 디렉터리 스키마 확장 특성 사용](active-directory-schema-extensions.md)을 참조하세요.
