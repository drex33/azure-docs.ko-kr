---
title: Azure API Management 인증 정책 | Microsoft Docs
description: Azure API Management에 사용할 수 있는 인증 정책에 대해 알아봅니다.
services: api-management
documentationcenter: ''
author: dlepow
manager: erikre
editor: ''
ms.assetid: 061702a7-3a78-472b-a54a-f3b1e332490d
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 01/27/2021
ms.author: danlep
ms.openlocfilehash: d43310918f1aaeb7905d22f22726709f8d0ed1f4
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128629771"
---
# <a name="api-management-authentication-policies"></a>API Management 인증 정책
이 문서에서는 다음 API Management 정책에 대한 참조를 제공합니다. 정책의 추가 및 구성에 대한 자세한 내용은 [API Management 정책](./api-management-policies.md)을 참조하세요.

##  <a name="authentication-policies"></a><a name="AuthenticationPolicies"></a> 인증 정책

-   [기본 사용 인증](api-management-authentication-policies.md#Basic) - 기본 인증을 사용하여 백 엔드 서비스를 인증합니다.

-   [클라이언트 인증서 사용 인증](api-management-authentication-policies.md#ClientCertificate) - 클라이언트 인증서를 사용하여 백 엔드 서비스를 인증합니다.

-   [관리 ID로 인증](api-management-authentication-policies.md#ManagedIdentity) - API Management 서비스에 대한 [관리 ID](../active-directory/managed-identities-azure-resources/overview.md)를 사용하여 인증합니다.

##  <a name="authenticate-with-basic"></a><a name="Basic"></a> 기본 사용 인증
 `authentication-basic` 정책을 사용하여 기본 인증을 사용하는 백 엔드 서비스를 인증합니다. 이 정책은 HTTP 권한 부여 헤더를 정책에서 제공한 자격 증명에 해당하는 값으로 효과적으로 설정합니다.

### <a name="policy-statement"></a>정책 문

```xml
<authentication-basic username="username" password="password" />
```

### <a name="example"></a>예제

```xml
<authentication-basic username="testuser" password="testpassword" />
```

### <a name="elements"></a>요소

|이름|Description|필수|
|----------|-----------------|--------------|
|인증-기본|루트 요소입니다.|예|

### <a name="attributes"></a>특성

|Name|Description|필수|기본값|
|----------|-----------------|--------------|-------------|
|사용자 이름|기본 자격 증명의 사용자 이름을 지정합니다.|예|해당 없음|
|password|기본 자격 증명의 비밀번호를 지정합니다.|예|해당 없음|

### <a name="usage"></a>사용량
 이 정책은 다음과 같은 정책 [섹션](./api-management-howto-policies.md#sections) 및 [범위](./api-management-howto-policies.md#scopes)에서 사용할 수 있습니다.

-   **정책 섹션:** inbound

-   **정책 범위:** 모든 범위

##  <a name="authenticate-with-client-certificate"></a><a name="ClientCertificate"></a> 클라이언트 인증서 사용 인증
 `authentication-certificate` 정책을 사용하여 클라이언트 인증서를 사용하는 백 엔드 서비스를 인증합니다. 먼저 인증서를 [API Management에 설치](./api-management-howto-mutual-certificates.md)하고 지문 또는 인증 ID(리소스 이름)으로 식별해야 합니다. 

> [!CAUTION]
> 인증서가 Azure Key Vault에 저장된 인증서를 참조하는 경우 인증서 ID를 사용하여 식별합니다. 키 자격 증명 모음 인증서를 순환하면 API Management의 지문이 변경되고, 새 인증서가 지문으로 식별되는 경우 정책에서 새 인증서를 확인하지 않습니다.

### <a name="policy-statement"></a>정책 문

```xml
<authentication-certificate thumbprint="thumbprint" certificate-id="resource name"/>
```

### <a name="examples"></a>예

이 예제에서 클라이언트 인증서는 인증서 ID로 식별됩니다.

```xml  
<authentication-certificate certificate-id="544fe9ddf3b8f30fb490d90f" />  
``` 

이 예제에서 클라이언트 인증서는 지문으로 식별됩니다.

```xml
<authentication-certificate thumbprint="CA06F56B258B7A0D4F2B05470939478651151984" />
```
이 예제에서는 클라이언트 인증서가 기본 제공 인증서 저장소에서 검색되지 않고 정책에 설정됩니다.

```xml
<authentication-certificate body="@(context.Variables.GetValueOrDefault<byte[]>("byteCertificate"))" password="optional-certificate-password" />
```

### <a name="elements"></a>요소  
  
|이름|Description|필수|  
|----------|-----------------|--------------|  
|인증-인증서|루트 요소입니다.|예|  
  
### <a name="attributes"></a>특성  
  
|Name|Description|필수|기본값|  
|----------|-----------------|--------------|-------------|  
|thumbprint|클라이언트 인증서에 대한 지문입니다.|`thumbprint`와 `certificate-id` 중 하나가 있어야 합니다.|해당 없음|
|certificate-id|인증서 리소스 이름입니다.|`thumbprint`와 `certificate-id` 중 하나가 있어야 합니다.|해당 없음|
|본문|클라이언트 인증서(바이트 배열)입니다.|예|해당 없음|
|password|클라이언트 인증서에 대한 암호입니다.|`body`에 지정된 인증서가 암호로 보호된 경우 사용됩니다.|해당 없음|
  
### <a name="usage"></a>사용량  
 이 정책은 다음과 같은 정책 [섹션](./api-management-howto-policies.md#sections) 및 [범위](./api-management-howto-policies.md#scopes)에서 사용할 수 있습니다.  
  
-   **정책 섹션:** inbound  
  
-   **정책 범위:** 모든 범위  

##  <a name="authenticate-with-managed-identity"></a><a name="ManagedIdentity"></a> 관리 ID를 사용하여 인증  
 `authentication-managed-identity` 정책을 사용하여 관리 ID를 사용하는 백 엔드 서비스를 인증합니다. 이 정책은 기본적으로 관리 ID를 사용하여 Azure Active Directory에서 지정된 리소스에 액세스하기 위한 액세스 토큰을 가져옵니다. 토큰을 성공적으로 가져오면 정책에서 `Authorization` 스키마를 사용하여 `Bearer` 헤더의 토큰 값을 설정합니다.

시스템 할당 ID와 다중 사용자 할당 ID를 모두 사용하여 토큰을 요청할 수 있습니다. `client-id`를 제공하지 않으면 시스템 할당 ID로 가정됩니다. `client-id` 변수가 제공된 경우 Azure Active Directory의 해당 사용자 할당 ID에 대한 토큰이 요청됩니다.
  
### <a name="policy-statement"></a>정책 문  
  
```xml  
<authentication-managed-identity resource="resource" client-id="clientid of user-assigned identity" output-token-variable-name="token-variable" ignore-error="true|false"/>  
```  
  
### <a name="example"></a>예제  
#### <a name="use-managed-identity-to-authenticate-with-a-backend-service"></a>관리 ID를 사용하여 백 엔드 서비스 인증
```xml  
<authentication-managed-identity resource="https://graph.microsoft.com"/> 
```
```xml  
<authentication-managed-identity resource="https://management.azure.com/"/> <!--Azure Resource Manager-->
```
```xml  
<authentication-managed-identity resource="https://vault.azure.net"/> <!--Azure Key Vault-->
```
```xml  
<authentication-managed-identity resource="https://servicebus.azure.net/"/> <!--Azure Service Bus-->
```
```xml  
<authentication-managed-identity resource="https://storage.azure.com/"/> <!--Azure Blob Storage-->
```
```xml  
<authentication-managed-identity resource="https://database.windows.net/"/> <!--Azure SQL-->
```

```xml
<authentication-managed-identity resource="Client_id_of_Backend"/> <!--Your own Azure AD Application-->
```

#### <a name="use-managed-identity-and-set-header-manually"></a>관리 ID 사용 및 수동으로 헤더 설정

```xml
<authentication-managed-identity resource="Client_id_of_Backend"
   output-token-variable-name="msi-access-token" ignore-error="false" /> <!--Your own Azure AD Application-->
<set-header name="Authorization" exists-action="override">
   <value>@("Bearer " + (string)context.Variables["msi-access-token"])</value>
</set-header>
```

#### <a name="use-managed-identity-in-send-request-policy"></a>전송-요청 정책에서 관리 ID 사용
```xml  
<send-request mode="new" timeout="20" ignore-error="false">
    <set-url>https://example.com/</set-url>
    <set-method>GET</set-method>
    <authentication-managed-identity resource="ResourceID"/>
</send-request>
```

### <a name="elements"></a>요소  
  
|이름|Description|필수|  
|----------|-----------------|--------------|  
|authentication-managed-identity |루트 요소입니다.|예|  
  
### <a name="attributes"></a>특성  
  
|Name|Description|필수|기본값|  
|----------|-----------------|--------------|-------------|  
|resource|문자열입니다. Azure Active Directory의 대상 웹 API(보안 리소스)의 앱 ID입니다.|예|해당 없음|
|client-id|문자열입니다. Azure Active Directory 사용자 할당 ID의 앱 ID입니다.|예|시스템 할당 ID|
|output-token-variable-name|문자열입니다. `string` 개체 형식으로 토큰 값을 받을 컨텍스트 변수의 이름입니다. |예|해당 없음|  
|ignore-error|Boolean입니다. `true`로 설정하면, 액세스 토큰을 가져오지 않은 경우에도 정책 파이프라인이 계속 실행됩니다.|예|false|  
  
### <a name="usage"></a>사용량  
 이 정책은 다음과 같은 정책 [섹션](./api-management-howto-policies.md#sections) 및 [범위](./api-management-howto-policies.md#scopes)에서 사용할 수 있습니다.  
  
-   **정책 섹션:** inbound  
  
-   **정책 범위:** 모든 범위

## <a name="next-steps"></a>다음 단계
정책으로 작업하는 방법에 대한 자세한 내용은 다음을 참조하세요.

+ [API Management의 정책](api-management-howto-policies.md)
+ [API 변환](transform-api.md)
+ [정책 참조](./api-management-policies.md)(정책 문 및 해당 설정에 대한 전체 목록)
+ [정책 샘플](./policy-reference.md)
