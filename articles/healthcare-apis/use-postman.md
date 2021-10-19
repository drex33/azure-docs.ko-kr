---
title: Postman을 사용 하 여 Azure 의료 Api FHIR 서비스에 액세스
description: 이 문서에서는 Postman을 사용 하 여 Azure 의료 Api FHIR 서비스에 액세스 하는 방법을 설명 합니다.
services: healthcare-apis
author: ginalee-dotcom
ms.service: healthcare-apis
ms.topic: tutorial
ms.date: 08/25/2021
ms.author: ginle
ms.openlocfilehash: 775c8a4366a381d90f8e24772097249b8cba844a
ms.sourcegitcommit: 01dcf169b71589228d615e3cb49ae284e3e058cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/19/2021
ms.locfileid: "130187798"
---
# <a name="access-using-postman"></a>Postman을 사용 하 여 액세스

> [!IMPORTANT]
> Azure 의료 Api는 현재 미리 보기로 제공 됩니다. [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)에는 베타 또는 미리 보기로 제공되거나 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 추가 약관이 포함되어 있습니다.

이 문서에서는 [사후 man](https://www.getpostman.com/)을 사용 하 여 의료 api fhir 서비스 (fhir 서비스 라고 함)에 액세스 하는 단계를 안내 합니다.

## <a name="prerequisites"></a>필수 조건

* Azure에 배포 된 FHIR 서비스입니다. FHIR 서비스를 배포 하는 방법에 대 한 자세한 내용은 [canr 서비스 배포](fhir/fhir-portal-quickstart.md)를 참조 하세요.
* FHIR 서비스에 액세스 하기 위한 등록 된 클라이언트 응용 프로그램입니다. 클라이언트 응용 프로그램을 등록 하는 방법에 대 한 자세한 내용은 [Azure Active Directory에서 서비스 클라이언트 응용 프로그램 등록](register-application.md)을 참조 하세요. 
* 클라이언트 응용 프로그램에 부여 된 사용 권한 및 사용자 계정 (예: "FHIR 데이터 참가자")은 FHIR 서비스에 액세스 합니다. 
* Postman을 로컬로 설치 했습니다. postman에 대 한 자세한 내용은 [postman을 사용 하 여 시작](https://www.getpostman.com/)를 참조 하세요.

## <a name="using-postman-create-workspace-collection-and-environment"></a>Postman 사용: 작업 영역, 컬렉션 및 환경 만들기

Postman을 처음 접하는 경우 다음 단계를 수행 합니다. 그렇지 않으면 이 단계를 건너뛸 수 있습니다.
 
Postman은 사용자와 사용자의 팀이 Api, 컬렉션, 환경 및 기타 구성 요소를 공유할 수 있도록 작업 영역 개념을 소개 합니다. 기본 "내 작업 영역" 또는 "팀 작업 영역"을 사용 하거나 사용자 또는 팀에 대 한 새 작업 영역을 만들 수 있습니다.
 
[![Postman ](media/postman/postman-create-new-workspace.png) 에서 새 작업 영역 만들기의 스크린샷 ](media/postman/postman-create-new-workspace.png#lightbox)

다음으로 관련 된 모든 REST API 요청을 그룹화 할 수 있는 새 컬렉션을 만듭니다. 작업 영역에서 **컬렉션 만들기** 를 선택 합니다. 기본 이름을 **새 컬렉션** 으로 유지 하거나 이름을 바꿀 수 있습니다. 변경 사항이 자동으로 저장됩니다.

[![새 컬렉션 ](media/postman/postman-create-a-new-collection.png) 만들기의 스크린샷 ](media/postman/postman-create-a-new-collection.png#lightbox)

Postman 컬렉션을 가져오고 내보낼 수도 있습니다. 자세한 내용은 [Postman 설명서](https://learning.postman.com/docs/getting-started/importing-and-exporting-data/)를 참조 하세요.

[![데이터 ](media/postman/postman-import-data.png) 가져오기의 스크린샷 ](media/postman/postman-import-data.png#lightbox)

## <a name="create-or-update-environment-variables"></a>환경 변수 만들기 또는 업데이트

요청에서 전체 url을 사용할 수 있지만, url 및 기타 데이터를 변수에 저장 하 고이를 사용 하는 것이 좋습니다.

FHIR 서비스에 액세스 하려면 다음 변수를 만들거나 업데이트 해야 합니다.

* **tenantid** – FHIR 서비스가 배포 되는 Azure 테 넌 트입니다. **응용 프로그램 등록 개요** 메뉴 옵션에서 찾을 수 있습니다.
* **subid** – FHIR 서비스가 배포 되는 Azure 구독입니다. 이는 **Fhir 서비스 개요** 메뉴 옵션에서 찾을 수 있습니다.
* **clientid** – 응용 프로그램 클라이언트 등록 ID입니다.
* **clientsecret** – 응용 프로그램 클라이언트 등록 암호입니다.
* **fhirurl** – fanr 서비스 전체 URL입니다. 예들 들어 `https://xxx.azurehealthcareapis.com`입니다. 이는 **Fhir 서비스 개요** 메뉴 옵션에서 찾을 수 있습니다.
* **bearerToken** – 스크립트에 Azure Active Directory (Azure AD) 액세스 토큰을 저장 하는 변수입니다. 비워 둠

> [!NOTE]
> 클라이언트 응용 프로그램 등록에서 리디렉션 URL를 구성 했는지 확인 `https://www.getpostman.com/oauth2/callback` 합니다.

[![환경 변수의 ](media/postman/postman-environments-variable.png) 스크린샷 ](media/postman/postman-environments-variable.png#lightbox)

## <a name="connect-to-the-fhir-server"></a>FHIR 서버에 커넥트

Postman을 열고 사용 하려는 **작업 영역**, **컬렉션** 및 **환경을** 선택 합니다. 아이콘을 선택 `+` 하 여 새 요청을 만듭니다. 

[![새 요청 ](media/postman/postman-create-new-request.png) 만들기의 스크린샷 ](media/postman/postman-create-new-request.png#lightbox)

## <a name="get-capability-statement"></a>Get 기능 문

`{{fhirurl}}/metadata` `GET` 요청에를 입력 하 고 적중 `Send` 합니다. FHIR 서비스의 기능 문이 표시 되어야 합니다.

[![기능 문 매개 변수의 ](media/postman/postman-capability-statement.png) 스크린샷 ](media/postman/postman-capability-statement.png#lightbox)

[![저장 요청 ](media/postman/postman-save-request.png) 스크린샷 ](media/postman/postman-save-request.png#lightbox)

## <a name="get-azure-ad-access-token"></a>Azure AD 액세스 토큰 가져오기

FHIR 서비스는 Azure AD에 의해 보호 됩니다. 기본 인증을 사용 하지 않도록 설정할 수 없습니다. FHIR 서비스에 액세스 하려면 먼저 Azure AD 액세스 토큰을 가져와야 합니다. 자세한 내용은 [Microsoft ID 플랫폼 액세스 토큰](./../active-directory/develop/access-tokens.md)을 참조 하세요.

새 요청을 만듭니다 `POST` .

1. 요청 헤더에를 입력 합니다. `https://login.microsoftonline.com/{{tenantid}}/oauth2/token`

2. **본문** 탭을 선택 하 고 **x-www-form-urlencoded** 를 선택 합니다. 키 및 값 섹션에서 다음 값을 입력 합니다.
    - **grant_type**: `Client_Credentials`
    - **client_id**: `{{clientid}}`
    - **client_secret**: `{{clientsecret}}`
    - **리소스**: `{{fhirurl}}`
    
3. **테스트** 탭을 선택 하 고 텍스트 섹션에를 입력 합니다.`pm.environment.set("bearerToken", pm.response.json().access_token);`
4. **저장** 을 선택하여 설정을 저장합니다.
5. **보내기** 를 누릅니다. 변수에 자동으로 저장 되는 Azure AD 액세스 토큰과의 응답이 표시 됩니다 `bearerToken` . 그런 다음 모든 FHIR 서비스 API 요청에서 사용할 수 있습니다.

  [![보내기 단추의 ](media/postman/postman-send-button.png) 스크린샷 ](media/postman/postman-send-button.png#lightbox)

와 같은 온라인 도구를 사용 하 여 액세스 토큰을 검사할 수 있습니다 [https://jwt.ms](https://jwt.ms) . 토큰의 각 클레임에 대 한 자세한 설명을 보려면 **클레임** 탭을 선택 합니다.

[![액세스 토큰 클레임 ](media/postman/postman-access-token-claims.png) 의 스크린샷 ](media/postman/postman-access-token-claims.png#lightbox)

## <a name="get-fhir-resource"></a>리소스 가져오기

Azure AD 액세스 토큰을 가져온 후에는 FHIR 데이터에 액세스할 수 있습니다. 새 요청에 `GET` 를 입력 `{{fhirurl}}/Patient` 합니다.

**전달자 토큰** 을 권한 부여 유형으로 선택 합니다.  `{{bearerToken}}` **토큰** 섹션에를 입력 합니다. **보내기** 를 선택합니다. 응답으로 FHIR 리소스에 환자 목록이 표시 됩니다.

[![전달자 토큰 ](media/postman/postman-select-bearer-token.png) 선택의 스크린샷 ](media/postman/postman-select-bearer-token.png#lightbox)

## <a name="create-or-update-your-fhir-resource"></a>FHIR 리소스 만들기 또는 업데이트

Azure AD 액세스 토큰을 가져온 후에는 FHIR 데이터를 만들거나 업데이트할 수 있습니다. 예를 들어 새 환자를 만들거나 기존 환자를 업데이트할 수 있습니다.
 
새 요청을 만들고, 메서드를 "Post"로 변경 하 고, 요청 섹션에 값을 입력 합니다.

`{{fhirurl}}/Patient`

권한 부여 유형으로 **전달자 토큰** 을 선택 합니다.  `{{bearerToken}}` **토큰** 섹션에를 입력 합니다. **본문** 탭을 선택 합니다. **원시** 옵션 및 **JSON** 을 본문 텍스트 형식으로 선택 합니다. 본문 섹션에 텍스트를 복사 하 여 붙여 넣습니다. 


```
{
    "resourceType": "Patient",
    "active": true,
    "name": [
        {
            "use": "official",
            "family": "Kirk",
            "given": [
                "James",
                "Tiberious"
            ]
        },
        {
            "use": "usual",
            "given": [
                "Jim"
            ]
        }
    ],
    "gender": "male",
    "birthDate": "1960-12-25"
}
```
**보내기** 를 선택합니다. JSON 응답에 새 환자가 표시 되어야 합니다.

[![새 환자 ](media/postman/postman-send-create-new-patient.png) 를 만들기 위한 보내기 단추의 스크린샷 ](media/postman/postman-send-create-new-patient.png#lightbox)

## <a name="export-fhir-data"></a>FHIR 데이터 내보내기

Azure AD 액세스 토큰을 가져온 후에는 Azure storage 계정으로 FHIR 데이터를 내보낼 수 있습니다.

새 요청을 만듭니다 `GET` . `{{fhirurl}}/$export?_container=export`

**전달자 토큰** 을 권한 부여 유형으로 선택 합니다.  `{{bearerToken}}` **토큰** 섹션에를 입력 합니다. **헤더** 를 선택 하 여 두 개의 새 헤더를 추가 합니다.

- **수락:**`application/fhir+json`
- **를 선호합니다.**`respond-async`

**보내기를 누릅니다.** `202 Accepted`응답이 있습니다. 응답의 **헤더 탭을** 선택하고 **Content-Location** 의 값을 기록해 둡니다. 값을 사용하여 내보내기 작업 상태를 쿼리할 수 있습니다.

[![새 환자 202 수락 응답을 만드는 게시물의 스크린샷. ](media/postman/postman-202-accepted-response.png) ](media/postman/postman-202-accepted-response.png#lightbox)

## <a name="next-steps"></a>다음 단계

이 문서에서는 Postman을 사용하여 Azure Healthcare API에서 FHIR 서비스에 액세스하는 방법을 배웠습니다. Azure Healthcare API의 FHIR 서비스에 대한 자세한 내용은 다음을 참조하세요.

>[!div class="nextstepaction"]
>[FHIR 서비스란?](overview.md)
