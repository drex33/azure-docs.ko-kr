---
title: Azure Active Directory SMART on FHIR 프록시
description: 이 자습서에서는 프록시를 사용하여 Azure API for FHIR에서 SMART on FHIR 애플리케이션을 사용하도록 설정하는 방법을 설명합니다.
services: healthcare-apis
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: tutorial
ms.reviewer: dseven
ms.author: matjazl
author: zxue
ms.date: 09/10/2019
ms.openlocfilehash: d5f690b3941e09f7e2a4e999aa59a97872aa3019
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124742780"
---
# <a name="tutorial-azure-active-directory-smart-on-fhir-proxy"></a>자습서: Azure Active Directory SMART on FHIR 프록시

[SMART on FHIR](https://docs.smarthealthit.org/)는 파트너 애플리케이션을 FHIR 서버 및 FHIR 인터페이스를 사용하는 전자 의료 기록 시스템과 통합하는 개방형 사양 세트입니다. 이 사양의 주요 목적 중 하나는 애플리케이션에서 FHIR 서버의 인증 엔드포인트를 검색하고 인증 시퀀스를 시작하는 방법을 설명하는 것입니다. 

인증은 OAuth2 기반입니다. 그러나 SMART on FHIR는 Azure AD(Azure Active Directory)와 즉시 호환되지 않는 매개 변수 명명 규칙을 사용하기 때문에, Azure API for FHIR는 SMART on FHIR 시작 시퀀스의 하위 세트를 사용할 수 있는 Azure AD SMART on FHIR 프록시를 기본적으로 제공합니다. 특히 이 프록시는 [EHR 시작 시퀀스](https://hl7.org/fhir/smart-app-launch/#ehr-launch-sequence)를 지원합니다.

이 자습서에서는 프록시를 사용하여 Azure API for FHIR에서 SMART on FHIR 애플리케이션을 사용하도록 설정하는 방법을 설명합니다.

## <a name="prerequisites"></a>필수 구성 요소

- Azure API for FHIR의 인스턴스
- [.NET Core 2.2](https://dotnet.microsoft.com/download/dotnet-core/2.2)

## <a name="configure-azure-ad-registrations"></a>Azure AD 등록 구성

SMART on FHIR를 사용하려면 `Audience`의 식별자 URI가 FHIR 서비스의 URI와 동일해야 합니다. Azure API for FHIR의 표준 구성은 `https://azurehealthcareapis.com`의 `Audience` 값을 사용합니다. 그러나 FHIR 서비스의 특정 URL과 일치하는 값을 설정할 수도 있습니다(예: `https://MYFHIRAPI.azurehealthcareapis.com`). 이 설정은 SMART on FHIR 프록시를 작업할 때 필요합니다.

클라이언트 애플리케이션 등록도 필요합니다. 대부분의 SMART on FHIR 애플리케이션은 단일 페이지 JavaScript 애플리케이션입니다. 따라서 [Azure AD에서 공용 클라이언트 애플리케이션을](register-public-azure-ad-client-app.md)구성하기 위한 지침을 따라야 합니다.

이러한 단계를 완료한 후에는 다음이 필요합니다.

- 대상을 로 설정한 FHIR `https://MYFHIRAPI.azurehealthcareapis.com` `MYFHIRAPI` 서버입니다. 여기서 은 Azure API for FHIR 인스턴스의 이름입니다.
- 퍼블릭 클라이언트 애플리케이션 등록. 이 클라이언트 애플리케이션의 애플리케이션 ID를 적어 둡니다.

### <a name="set-admin-consent-for-your-app"></a>앱에 대한 관리자 동의 설정

SMART on FHIR을 사용하려면 먼저 앱을 인증하고 권한을 부여해야 합니다. SMART on FHIR을 처음 사용하는 경우 앱이 FHIR 리소스에 액세스할 수 있도록 관리 동의를 받아야 합니다.

앱에 소유권 역할이 없는 경우 앱 소유자에게 문의하고 앱에서 관리자 동의를 부여하도록 요청합니다. 

관리자 권한이 있는 경우 다음 단계를 완료하여 관리자 동의를 직접 부여합니다. (나중에 앱에서 메시지가 표시되면 관리자 동의를 자신에게 부여할 수도 있습니다.) 동일한 단계를 완료하여 다른 사용자를 소유자로 추가하여 이 앱 등록을 보고 편집할 수 있습니다.

자신 또는 다른 사용자를 앱의 소유자로 추가하려면 다음을 수행합니다.

1. Azure Portal에서 Azure Active Directory로 이동합니다.
1. 왼쪽 메뉴에서 앱 **등록을** 선택합니다.
1. 만든 앱 등록을 검색한 다음 선택합니다.
1. 왼쪽 메뉴의 **관리** 아래에서 **소유자를** 선택합니다.
1. **소유자 추가를** 선택한 다음, 관리자 동의를 원하는 사용자 또는 자신을 추가합니다.
1. **저장** 을 선택합니다.

## <a name="enable-the-smart-on-fhir-proxy"></a>SMART on FHIR 프록시를 사용하도록 설정

**SMART on FHIR 프록시** 를 선택하여 Azure API for FHIR 인스턴스의 **인증** 설정에서 SMART on FHIR 프록시를 사용하도록 설정합니다.

![SMART on FHIR 프록시를 사용하도록 설정하기 위한 선택 항목](media/tutorial-smart-on-fhir/enable-smart-on-fhir-proxy.png)

## <a name="enable-cors"></a>CORS를 사용하도록 설정

대부분의 SMART on FHIR 애플리케이션은 단일 페이지 JavaScript 앱이므로, Azure API for FHIR에 [CORS(원본 간 리소스 공유)를 사용하도록 설정](configure-cross-origin-resource-sharing.md)해야 합니다.

![CORS를 사용하기 위한 선택 항목](media/tutorial-smart-on-fhir/enable-cors.png)

## <a name="configure-the-reply-url"></a>회신 URL 구성

SMART on FHIR 프록시는 SMART on FHIR 앱과 Azure AD 간의 중개자 역할을 합니다. 인증 회신(인증 코드)은 앱 자체가 아닌 SMART on FHIR 프록시로 이동해야 합니다. 그러면 프록시가 앱에 회신을 전달합니다. 

이와 같은 2단계 인증 코드 전달 때문에 Azure AD 클라이언트 애플리케이션의 회신 URL(콜백)을 SMART on FHIR 프록시의 회신 URL과 SMART on FHIR 앱의 회신 URL 조합으로 설정해야 합니다. 결합된 회신 URL은 다음 형식을 사용합니다.

```http
https://MYFHIRAPI.azurehealthcareapis.com/AadSmartOnFhirProxy/callback/aHR0cHM6Ly9sb2NhbGhvc3Q6NTAwMS9zYW1wbGVhcHAvaW5kZXguaHRtbA
```

이 회신에서 `aHR0cHM6Ly9sb2NhbGhvc3Q6NTAwMS9zYW1wbGVhcHAvaW5kZXguaHRtbA`는 URL을 지원하는 base64로 인코딩된 SMART on FHIR 앱의 회신 URL 버전입니다. SMART on FHIR 앱 시작 관리자의 경우 앱이 로컬로 실행되면 회신 URL은 `https://localhost:5001/sampleapp/index.html`입니다. 

다음과 같은 스크립트를 사용하여 결합된 회신 URL을 생성할 수 있습니다.

```PowerShell
$replyUrl = "https://localhost:5001/sampleapp/index.html"
$fhirServerUrl = "https://MYFHIRAPI.azurewebsites.net"
$bytes = [System.Text.Encoding]::UTF8.GetBytes($ReplyUrl)
$encodedText = [Convert]::ToBase64String($bytes)
$encodedText = $encodedText.TrimEnd('=');
$encodedText = $encodedText.Replace('/','_');
$encodedText = $encodedText.Replace('+','-');

$newReplyUrl = $FhirServerUrl.TrimEnd('/') + "/AadSmartOnFhirProxy/callback/" + $encodedText
```

앞에서 Azure AD에 대해 만든 퍼블릭 클라이언트 애플리케이션에 회신 URL을 추가합니다.

![퍼블릭 클라이언트에 대해 구성된 회신 URL](media/tutorial-smart-on-fhir/configure-reply-url.png)

## <a name="get-a-test-patient"></a>테스트 환자 가져오기

Azure API for FHIR 및 SMART on FHIR 프록시를 테스트하려면 데이터베이스에 환자가 한 명 이상 있어야 합니다. 아직 API와 상호 작용하지 않았고 데이터베이스에 데이터가 없는 경우 [Postman을 사용하여 FHIR 서비스에 액세스하여](./../use-postman.md) 환자 로드를 참조하세요. 특정 환자의 ID를 적어 둡니다.

## <a name="download-the-smart-on-fhir-app-launcher"></a>SMART on FHIR 앱 시작 관리자 다운로드

오픈 소스인 [Azure 리포지토리용 FHIR 서버](https://github.com/Microsoft/fhir-server)에는 간단한 SMART on FHIR 앱 시작 관리자와 SMART on FHIR 앱 샘플이 포함되어 있습니다. 이 자습서에서는 이 SMART on FHIR 시작 관리자를 로컬로 사용하여 설치를 테스트합니다.

다음 명령을 사용하여 GitHub 리포지토리를 복제하고 애플리케이션으로 이동할 수 있습니다.

```PowerShell
git clone https://github.com/Microsoft/fhir-server
cd fhir-server/samples/apps/SmartLauncher
```

애플리케이션에서 몇 가지 구성을 설정해야 하며 `appsettings.json`에서 설정할 수 있습니다.

```json
{
    "FhirServerUrl": "https://MYFHIRAPI.azurehealthcareapis.com",
    "ClientId": "APP-ID",
    "DefaultSmartAppUrl": "/sampleapp/launch.html"
}
```

다음과 같이 `dotnet user-secrets` 기능을 사용하는 것이 좋습니다.

```PowerShell
dotnet user-secrets set FhirServerUrl https://MYFHIRAPI.azurehealthcareapis.com
dotnet user-secrets set ClientId <APP-ID>
```

다음 명령을 사용하여 애플리케이션을 실행합니다.

```PowerShell
dotnet run
```

## <a name="test-the-smart-on-fhir-proxy"></a>SMART on FHIR 프록시 테스트

SMART on FHIR 앱 시작 관리자를 시작한 후에는 브라우저에서 다음 화면이 표시되는 `https://localhost:5001`을 가리킬 수 있습니다.

![SMART on FHIR 앱 시작 관리자](media/tutorial-smart-on-fhir/smart-on-fhir-app-launcher.png)

**환자**, **방문 기록** 또는 **의사** 정보를 입력하면 **시작 컨텍스트** 가 업데이트되는 것을 볼 수 있습니다. Azure API for FHIR를 사용하는 경우 시작 컨텍스트는 환자, 의사 등에 대한 정보를 포함하는 JSON 문서입니다. 이 시작 컨텍스트는 base64로 인코딩되어 SMART on FHIR 앱에 `launch` 쿼리 매개 변수로 전달됩니다. SMART on FHIR 사양에 따라 이 변수는 SMART on FHIR 앱에 대해 불투명하며 ID 공급자에게 전달됩니다. 

SMART on FHIR 프록시는 이 정보를 사용하여 토큰 응답의 필드를 채웁니다. SMART on FHIR 앱은 이러한 필드를 사용하여 데이터를 요청할 환자 및 애플리케이션을 화면에 렌더링하는 방법을 *제어할 수 있습니다*. SMART on FHIR 프록시는 다음 필드를 지원합니다.

* `patient`
* `encounter`
* `practitioner`
* `need_patient_banner`
* `smart_style_url`

이러한 필드는 앱에 대한 지침을 제공하는 것이 그 목적이지만 보안 정보를 전달하지는 않습니다. SMART on FHIR 애플리케이션에서는 이러한 필드를 무시해도 됩니다.

SMART on FHIR 앱 시작 관리자는 페이지 맨 아래에 있는 **시작 URL** 정보를 업데이트합니다. **시작** 을 선택하여 샘플 앱을 시작하면 다음 샘플과 비슷한 내용이 표시됩니다.

![SMART on FHIR 앱](media/tutorial-smart-on-fhir/smart-on-fhir-app.png)

토큰 응답을 검사하여 시작 컨텍스트 필드가 어떤 방식으로 앱에 전달되는지 확인합니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 Azure Active Directory SMART on FHIR 프록시를 구성했습니다. Azure API for FHIR 및 오픈 소스 FHIR Server for Azure에서 SMART on FHIR 애플리케이션을 사용하는 방법을 살펴보려면 GitHub의 FHIR 서버 샘플 리포지토리로 이동하세요.

>[!div class="nextstepaction"]
>[FHIR 서버 샘플](https://github.com/Microsoft/fhir-server-samples)
