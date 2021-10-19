---
title: REST 클라이언트를 사용하여 Azure Healthcare API에 액세스
description: 이 문서에서는 VSCode에서 REST 클라이언트 확장을 사용하여 의료 API에 액세스하는 방법을 설명합니다.
services: healthcare-apis
author: ginalee-dotcom
ms.service: healthcare-apis
ms.topic: tutorial
ms.date: 07/19/2021
ms.author: ginle
ms.openlocfilehash: 615aaed8be507ed19314e7f7329d7ca2f15a5f0e
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2021
ms.locfileid: "130042355"
---
# <a name="accessing-the-healthcare-apis-preview-using-the-rest-client-extension-in-visual-studio-code"></a>Visual Studio Code REST 클라이언트 확장을 사용하여 의료 API(미리 보기)에 액세스

> [!IMPORTANT]
> Azure Healthcare API는 현재 미리 보기로 제공됩니다. [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)에는 베타 또는 미리 보기로 제공되거나 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 추가 약관이 포함되어 있습니다.

이 문서에서는 [Visual Studio Code REST 클라이언트 확장을](https://marketplace.visualstudio.com/items?itemName=humao.rest-client)사용하여 의료 API에 액세스하는 방법을 알아봅니다.

## <a name="install-rest-client-extension"></a>REST 클라이언트 확장 설치

Visual Studio Code 왼쪽 패널에서 확장 아이콘을 선택하고 "REST 클라이언트"를 검색합니다. REST [클라이언트 확장을](https://marketplace.visualstudio.com/items?itemName=humao.rest-client) 찾아 설치합니다.

[![REST 클라이언트 VSCode 확장 ](media/rest-install.png) ](media/rest-install.png#lightbox)

## <a name="create-a-http-file-and-define-variables"></a>파일 만들기 `.http` 및 변수 정의

Visual Studio Code 새 파일을 만듭니다. 파일에 `GET` 요청 명령줄을 입력하고 로 `test.http` 저장합니다. 파일 접미사는 `.http` REST 클라이언트 환경을 자동으로 활성화합니다. 을 클릭하여 `Send Request` 메타데이터를 가져옵니다. 

[![요청 ](media/rest-send-request.png) 보내기 ](media/rest-send-request.png#lightbox)

## <a name="get-client-application-values"></a>클라이언트 애플리케이션 값 얻기

> [!Important]
> FHIR 서버 REST API 호출하기 전에(메타데이터 가져오기 외) 애플리케이션 **[등록을](register-application.md)** 완료해야 합니다. Azure **테넌트 ID,** **클라이언트 ID, 클라이언트** **암호** 및 **서비스 URL** 을 기록해 둡다.

REST API 대한 호출에서 클라이언트 ID와 같은 값을 직접 사용할 수 있지만 이러한 값에 대한 몇 가지 변수를 정의하고 대신 변수를 사용하는 것이 좋습니다.

파일에 `test.http` 애플리케이션 등록에서 얻은 다음 정보를 포함합니다. 

```
### REST Client
@fhirurl =https://xxx.azurehealthcareapis.com
@clientid =xxx....
@clientsecret =xxx....
@tenantid =xxx....
```

## <a name="get-azure-ad-access-token"></a>Azure AD 액세스 토큰 얻기

파일에 아래 정보를 포함하면 `test.http` 를 `Send Request` 누릅니다. 액세스 토큰을 포함하는 HTTP 응답이 표시됩니다.

로 시작하는 `@name` 줄에는 액세스 토큰을 포함하는 HTTP 응답을 캡처하는 변수가 포함되어 있습니다. 변수 는 `@token` 액세스 토큰을 저장하는 데 사용됩니다.

>[!Note] 
>`grant_type`의 `client_credentials` 는 액세스 토큰을 가져오는 데 사용됩니다.

```
### Get access token 
@name getAADToken 
POST https://login.microsoftonline.com/{{tenantid}}/oauth2/token
Content-Type: application/x-www-form-urlencoded

grant_type=client_credentials
&resource={{fhirurl}}
&client_id={{clientid}}
&client_secret={{clientsecret}}

### Extract access token from getAADToken request
@token = {{getAADToken.response.body.access_token}}
```

[![액세스 토큰 ](media/rest-config.png) 얻기 ](media/rest-config.png#lightbox)

## <a name="get-fhir-patient-data"></a>`GET` FHIR 환자 데이터

이제 요청을 통해 환자 또는 특정 환자 목록을 얻을 수 `GET` 있습니다. 가 있는 `Authorization` 줄은 요청에 대한 헤더 정보입니다. `GET` `PUT` `POST` FHIR 리소스를 만들거나 업데이트하도록 요청하거나 보낼 수도 있습니다.

```
### GET Patient 
GET {{fhirurl}}/Patient/<patientid>
Authorization: Bearer {{token}}
```

[![GET Patient ](media/rest-patient.png) ](media/rest-patient.png#lightbox)

## <a name="run-powershell-or-cli"></a>PowerShell 또는 CLI 실행

Visual Studio Code 내에서 PowerShell 또는 CLI 스크립트를 실행할 수 있습니다. `CTRL`키를 `~` 누르고 PowerShell 또는 Bash를 선택합니다. 자세한 내용은 통합 [터미널](https://code.visualstudio.com/docs/editor/integrated-terminal)에서 확인할 수 있습니다.

### <a name="powershell-in-visual-studio-code"></a>Visual Studio Code PowerShell
[![PowerShell ](media/rest-powershell.png) 실행 ](media/rest-powershell.png#lightbox)

### <a name="cli-in-visual-studio-code"></a>Visual Studio Code CLI
[![CLI ](media/rest-cli.png) 실행 ](media/rest-cli.png#lightbox)

## <a name="troubleshooting"></a>문제 해결

HL7 사양에 따라 액세스 토큰이 필요하지 않은 메타데이터를 가져올 수 없는 경우 FHIR 서버가 제대로 실행되고 있는지 확인합니다.

액세스 토큰을 가져올 수 없는 경우 클라이언트 애플리케이션이 제대로 등록되어 있고 애플리케이션 등록 단계에서 올바른 값을 사용하고 있는지 확인합니다.

FHIR 서버에서 데이터를 가져올 수 없는 경우 클라이언트 애플리케이션(또는 서비스 주체)에 FHIR 서버에 대한 "FHIR 데이터 기여자"와 같은 액세스 권한이 부여되었는지 확인합니다.
