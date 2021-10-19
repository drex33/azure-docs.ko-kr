---
title: Azure의 Postman FHIR 서버 - Azure API for FHIR
description: 이 자습서에서는 Postman을 사용 하 여 FHIR 서버에 액세스 하는 데 필요한 단계를 안내 합니다. Postman은 API에 액세스하는 애플리케이션을 디버깅하는 데 유용합니다.
services: healthcare-apis
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: tutorial
ms.reviewer: dseven
ms.author: cavoeg
author: matjazl
ms.date: 03/26/2021
ms.openlocfilehash: 1080b8fb47ebcab07737d26464302a9e18465315
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "121788102"
---
# <a name="access-azure-api-for-fhir-with-postman"></a>Postman을 사용하여 Azure API for FHIR 액세스

클라이언트 애플리케이션은 [REST API](https://www.hl7.org/fhir/http.html)를 통해 Azure API for FHIR에 액세스할 수 있습니다. 요청을 보내고, 응답을 확인하고, 애플리케이션을 빌드 중에 디버그하려면 선택한 API 테스트 도구를 사용합니다. 이 자습서에서는 [Postman](https://www.getpostman.com/)을 사용 하 여 FHIR 서버에 액세스 하는 단계를 안내 합니다. 

## <a name="prerequisites"></a>필수 구성 요소

- Azure의 FHIR 엔드포인트. 

  FHIR 용 Azure API (관리 서비스)를 배포 하려면 [Azure Portal](fhir-paas-portal-quickstart.md), [PowerShell](fhir-paas-powershell-quickstart.md)또는 [Azure CLI](fhir-paas-cli-quickstart.md)를 사용할 수 있습니다.

- FHIR 서비스에 액세스 하기 위한 등록 된 [기밀 클라이언트 응용 프로그램](register-confidential-azure-ad-client-app.md) 입니다.
- 기밀 클라이언트 응용 프로그램에 대 한 사용 권한과 사용자 계정 (예: "FHIR 데이터 참가자")을 사용 하 여 FHIR 서비스에 액세스할 수 있습니다. 자세한 내용은 [FHIR에 대 한 AZURE RBAC 구성](./configure-azure-rbac.md)을 참조 하세요.
- Postman 설치. 
    
  postman에 대 한 자세한 내용은 [postman을 사용 하 여 시작](https://www.getpostman.com)를 참조 하세요.

## <a name="fhir-server-and-authentication-details"></a>FHIR 서버 및 인증 세부 정보

Postman을 사용 하려면 다음 인증 매개 변수가 필요 합니다.

- 사용자의 FHIR 서버 URL (예:) `https://MYACCOUNT.azurehealthcareapis.com`

- FHIR 서버에 대한 ID 공급자 `Authority`(예: `https://login.microsoftonline.com/{TENANT-ID}`)

- 구성 된로, `audience` 일반적으로 FHIR 서버의 URL (예: 또는)입니다 `https://<FHIR-SERVER-NAME>.azurehealthcareapis.com` `https://azurehealthcareapis.com` .

- `client_id`FHIR 서비스에 액세스 하는 데 사용 되는 [기밀 클라이언트 응용 프로그램](register-confidential-azure-ad-client-app.md) 의 또는 응용 프로그램 ID입니다.

- `client_secret`기밀 클라이언트 응용 프로그램의 또는 응용 프로그램 암호입니다.

마지막으로 `https://www.getpostman.com/oauth2/callback`이 클라이언트 애플리케이션에 대해 등록된 회신 URL인지 확인해야 합니다.

## <a name="connect-to-fhir-server"></a>FHIR 서버에 연결

Postman을 열고 **가져오기** 를 선택 하 여 요청을 수행 `https://fhir-server-url/metadata` 합니다.

![Postman 메타데이터 기능 문](media/tutorial-postman/postman-metadata.png)

Azure API for FHIR에 대한 메타데이터 URL은 `https://MYACCOUNT.azurehealthcareapis.com/metadata`입니다. 

이 예제에서 FHIR 서버 URL은이 `https://fhirdocsmsft.azurewebsites.net` 고 서버의 기능 문은에서 사용할 수 있습니다 `https://fhirdocsmsft.azurewebsites.net/metadata` . 이 끝점에는 인증 없이 액세스할 수 있습니다.

제한 된 리소스에 액세스 하려고 하면 "인증 하지 못했습니다." 응답이 발생 합니다.

![인증 실패](media/tutorial-postman/postman-authentication-failed.png)

## <a name="obtaining-an-access-token"></a>액세스 토큰 가져오기
**Get New Access Token**(새 액세스 토큰 가져오기)을 선택합니다.

유효한 액세스 토큰을 얻으려면 **권한 부여** 를 선택 하 고 **유형** 드롭다운 메뉴에서 **OAuth 2.0** 을 선택 합니다.

![OAuth 2.0 설정](media/tutorial-postman/postman-select-oauth-two.png)

**Get New Access Token**(새 액세스 토큰 가져오기)을 선택합니다.

![새 액세스 토큰 요청](media/tutorial-postman/postman-request-token.png)

**새 액세스 토큰 가져오기** 대화 상자에서 다음 세부 정보를 입력 합니다.

| 필드                 | 예제 값                                                                                                   | 주석                    |
|-----------------------|-----------------------------------------------------------------------------------------------------------------|----------------------------|
| 토큰 이름            | MYTOKEN                                                                                                         | 선택한 이름          |
| 권한 부여 유형            | 인증 코드                                                                                              |                            |
| 콜백 URL          | `https://www.getpostman.com/oauth2/callback`                                                                    |                            |
| 인증 URL              | `https://login.microsoftonline.com/{TENANT-ID}/oauth2/authorize?resource=<audience>` | `audience`는 Azure API for FHIR에 대한 `https://MYACCOUNT.azurehealthcareapis.com`입니다. |
| 액세스 토큰 URL      | `https://login.microsoftonline.com/{TENANT ID}/oauth2/token`                                                    |                            |
| 클라이언트 ID             | `XXXXXXXX-XXX-XXXX-XXXX-XXXXXXXXXXXX`                                                                           | 애플리케이션 UI             |
| 클라이언트 암호         | `XXXXXXXX`                                                                                                      | 비밀 클라이언트 키          |
| 범위 | `<Leave Blank>` | 범위가 사용 되지 않습니다. 따라서 비워 둘 수 있습니다.  
| 시스템 상태                 | `1234`     | [상태](https://learning.postman.com/docs/sending-requests/authorization/) 는 사이트 간 요청 위조를 방지 하기 위해 불투명 한 값입니다. 선택적 이며 ' 1234 '과 같은 임의의 값을 사용할 수 있습니다.                           |
| 클라이언트 인증 | 본문에서 클라이언트 자격 증명 보내기                                                                                 |                 

Azure Active Directory 인증 흐름을 통해 전달 될 **요청 토큰** 을 선택 하면 postman에 토큰이 반환 됩니다. 인증 오류가 발생 하는 경우 자세한 내용은 Postman 콘솔을 참조 하세요. **참고**: 리본에서 **보기** 를 선택한 다음, **postman Console 표시** 를 선택 합니다. Postman 콘솔의 바로 가기 키는 **Alt + Ctrl + C** 입니다.

아래로 스크롤하여 반환 된 토큰 화면을 표시 한 다음 **토큰 사용** 을 선택 합니다.

![토큰 사용](media/tutorial-postman/postman-use-token.png)

새로 채워진 토큰을 보려면 **액세스 토큰** 필드를 참조 하세요. **보내기** 를 선택 하 여 리소스 검색을 반복 하면 `Patient` **상태가** `200 OK` 반환 됩니다. 반환 된 상태는 `200 OK` 성공적인 HTTP 요청을 나타냅니다.

![200 정상](media/tutorial-postman/postman-two-hundred-ok.png)

*환자 검색* 예에서는 데이터베이스에 검색 결과가 비어 있는 환자 없습니다.

[Jwt.ms](https://jwt.ms)와 같은 도구를 사용 하 여 액세스 토큰을 검사할 수 있습니다. 내용의 예는 다음과 같습니다.

```json
{
  "aud": "https://MYACCOUNT.azurehealthcareapis.com",
  "iss": "https://sts.windows.net/{TENANT-ID}/",
  "iat": 1545343803,
  "nbf": 1545343803,
  "exp": 1545347703,
  "acr": "1",
  "aio": "AUQAu/8JXXXXXXXXXdQxcxn1eis459j70Kf9DwcUjlKY3I2G/9aOnSbw==",
  "amr": [
    "pwd"
  ],
  "appid": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "oid": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "appidacr": "1",

  ...// Truncated
}
```

문제 해결 시 올바른 대상(`aud` 클레임)이 있는지 확인하는 것이 좋습니다. 토큰이 올바른 발급자 (클레임)를 사용 `iss` 하 고 있고 올바른 대상 그룹 ( `aud` 클레임)을 보유 하 고 있지만 여전히 FHIR API에 액세스할 수 없는 경우 사용자 또는 서비스 주체 ( `oid` 클레임)가 fhir 데이터 평면에 액세스할 수 없는 것일 수 있습니다. Azure [RBAC (역할 기반 액세스 제어)](configure-azure-rbac.md) 를 사용 하 여 사용자에 게 데이터 평면 역할을 할당 하는 것이 좋습니다. 데이터 평면에 대 한 외부의 보조 Azure Active directory 테 넌 트를 사용 하는 경우 [FHIR 할당에 대 한 로컬 RBAC를 구성](configure-local-rbac.md) 해야 합니다.

또한 [Azure CLI를 사용 하 여 FHIR 용 AZURE API](get-healthcare-apis-access-token-cli.md)에 대 한 토큰을 가져올 수 있습니다. Azure CLI 사용 하 여 얻은 토큰을 사용 하는 경우 권한 부여 유형 *전달자 토큰* 을 사용 해야 합니다. 토큰을 직접에 붙여넣습니다.

## <a name="inserting-a-patient"></a>환자 삽입

유효한 액세스 토큰을 사용 하 여 이제 새 환자를 삽입할 수 있습니다. Postman에서 **Post** 를 선택 하 여 메서드를 변경 하 고 요청 본문에 다음 JSON 문서를 추가 합니다.

[!code-json[](../samples/sample-patient.json)]

**보내기** 를 선택 하 여 환자가 성공적으로 만들어졌는지 확인 합니다.

![환자가 성공적으로 생성되었음을 보여주는 스크린샷.](media/tutorial-postman/postman-patient-created.png)

환자 검색을 반복 하면 이제 환자 레코드가 표시 됩니다.

![환자 생성됨](media/tutorial-postman/postman-patient-found.png)

## <a name="next-steps"></a>다음 단계

이 자습서에서는 Postman을 사용 하 여 FHIR 용 Azure API에 액세스 했습니다. FHIR 기능 용 Azure API에 대 한 자세한 내용은을 참조 하세요.
 
>[!div class="nextstepaction"]
>[지원되는 기능](fhir-features-supported.md)
