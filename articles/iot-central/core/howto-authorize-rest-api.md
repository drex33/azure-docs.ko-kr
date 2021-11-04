---
title: Azure IoT Central에서 REST API 권한 부여
description: IoT Central REST API 호출을 인증하고 권한을 부여하는 방법
author: dominicbetts
ms.author: dobett
ms.date: 08/25/2021
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 28efc06f2d7c3b5a9fca8d96dda7fa885a98f088
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131070340"
---
# <a name="how-to-authenticate-and-authorize-iot-central-rest-api-calls"></a>IoT Central REST API 호출을 인증하고 권한을 부여하는 방법

IoT Central REST API를 사용하면 IoT Central 애플리케이션과 통합되는 클라이언트 애플리케이션을 개발할 수 있습니다. REST API를 사용하여 디바이스 템플릿, 디바이스, 작업, 사용자, 역할과 같은 IoT Central 애플리케이션에서 리소스 작업을 수행할 수 있습니다.

모든 IoT Central REST API 호출에는 IoT Central이 호출자의 ID를 확인하는 데 사용하는 권한 부여 헤더와 애플리케이션 내에서 호출자에게 부여되는 사용 권한이 필요합니다.

이 문서에서는 권한 부여 헤더에서 사용할 수 있는 토큰 유형과 이를 가져오는 방법에 대해 설명합니다.

## <a name="token-types"></a>토큰 형식

자동화/테스트/API 호출을 직접 수행하는 경우 사용자 전달자 토큰을 사용하는 것이 좋습니다. 개발 환경(즉, devops)을 자동화/스크립팅하는 경우 SPN 전달자 토큰을 사용하는 것이 좋습니다. API 토큰은 두 경우에 모두 사용할 수 있지만 만료 및 누출 위험이 있으므로 가능하면 항상 전달자를 사용하는 것이 좋습니다. 이해가 되셨나요? 

REST API를 사용하여 IoT Central 애플리케이션에 액세스하려면 다음을 사용하면 됩니다.

- _Azure Active Directory 전달자 토큰_. 전달자 토큰은 Azure Active Directory 사용자 계정이나 서비스 주체와 연결됩니다. 토큰은 사용자나 서비스 주체가 IoT Central 애플리케이션에서 가진 사용 권한과 동일한 권한을 호출자에게 부여합니다.
- IoT Central API 토큰. API 토큰은 IoT Central 애플리케이션의 역할과 연결됩니다.

IoT Central의 사용자 및 역할에 대해 자세히 알아보려면 [IoT Central 애플리케이션에서 사용자 및 역할 관리](howto-manage-users-roles.md)를 참조하세요.

## <a name="get-a-bearer-token"></a>전달자 토큰 가져오기

Azure Active Directory 사용자 계정에 대한 전달자 토큰을 가져오려면 다음 Azure CLI 명령을 사용합니다.

```azurecli
az login
az account get-access-token --resource https://apps.azureiotcentral.com
```

> [!IMPORTANT]
> `az login` 명령은 Cloud Shell를 사용하는 경우에도 필요합니다.

이전 명령의 JSON 출력은 다음 예제와 같습니다.

```json
{
  "accessToken": "eyJ0eX...fNQ",
  "expiresOn": "2021-03-22 11:11:16.072222",
  "subscription": "{your subscription id}",
  "tenant": "{your tenant id}",
  "tokenType": "Bearer"
}
```

전달자 토큰은 약 1시간 동안 유효하며, 그 후에는 새로 만들어야 합니다.

서비스 주체용 전달자 토큰을 가져오려면 [서비스 주체 인증](/rest/api/iotcentral/authentication#service-principal-authentication)을 참조하세요.

## <a name="get-an-api-token"></a>API 토큰 가져오기

API 토큰을 가져오려면 IoT Central UI 또는 REST API 호출을 사용하면 됩니다. 루트 조직과 연결 된 관리자와 올바른 역할에 할당 된 사용자는 API 토큰을 만들 수 있습니다.

IoT Central UI 사용:

1. **관리 > API 토큰** 으로 이동합니다.
1. **+ 토큰 만들기** 를 선택 합니다.
1. 토큰의 이름을 입력 하 고 역할 및 [조직을](howto-create-organizations.md)선택 합니다.
1. **생성** 을 선택합니다.
1. IoT Central에 다음 예제와 같은 토큰이 표시됩니다.

    `SharedAccessSignature sr=5782ed70...&sig=dvZZE...&skn=operator-token&se=1647948035850`

    이 화면은 API 토큰을 볼 수 있는 유일한 시간입니다. 분실한 경우 새로 생성해야 합니다.

API 토큰은 약 1년 동안 유효합니다. IoT Central 애플리케이션에서 기본 제공 역할과 사용자 지정 역할 모두에 대한 토큰을 생성할 수 있습니다. API 토큰을 만들 때 선택한 조직에 따라 API가 액세스할 수 있는 장치가 결정 됩니다. 응용 프로그램에 조직을 추가 하기 전에 만든 모든 API 토큰은 루트 조직과 연결 됩니다.

액세스를 취소해야 하는 경우 IoT Central UI에서 API 토큰을 삭제할 수 있습니다.

REST API 사용:

1. REST API를 사용하여 애플리케이션에서 역할 ID 목록을 검색합니다.

    ```http
    GET https://{your app subdomain}.azureiotcentral.com/api/roles?api-version=1.0
    ```

    이 요청에 대한 응답은 다음 예제와 같습니다.

    ```json
    {
      "value": [
        {
          "displayName": "Administrator",
          "id": "ca310b8d-2f4a-44e0-a36e-957c202cd8d4"
        },
        {
          "displayName": "Operator",
          "id": "ae2c9854-393b-4f97-8c42-479d70ce626e"
        },
        {
          "displayName": "Builder",
          "id": "344138e9-8de4-4497-8c54-5237e96d6aaf"
        }
      ]
    }
    ```

1. REST API를 사용하여 역할에 대한 API 토큰을 만듭니다. 예를 들어 연산자 역할에 대해 `operator-token`이라는 API 토큰을 만들려면 다음을 사용합니다.

    ```http
    PUT https://{your app subdomain}.azureiotcentral.com/api/roles/operator-token?api-version=1.0
    ```

    본문 요청:

    ```json
    {
      "roles": [
        {
          "role": "ae2c9854-393b-4f97-8c42-479d70ce626e"
        }
      ]
    }
    ```

    이전 명령에 대한 응답은 다음 JSON과 같습니다.

    ```json
    {
      "expiry": "2022-03-22T12:01:27.889Z",
      "id": "operator-token",
      "roles": [
        {
          "role": "ae2c9854-393b-4f97-8c42-479d70ce626e"
        }
      ],
      "token": "SharedAccessSignature sr=e8a...&sig=jKY8W...&skn=operator-token&se=1647950487889"
    }
    ```

    이 응답은 API 토큰에 액세스할 수 있는 유일한 시간입니다. 분실한 경우 새로 생성해야 합니다.

REST API를 사용하여 애플리케이션에서 API 토큰을 나열하고 삭제할 수 있습니다.

> [!TIP]
> [PREVIEW API](/rest/api/iotcentral/1.1-previewdataplane/api-tokens) 에는 새 [조직 기능](howto-create-organizations.md)에 대 한 지원이 포함 되어 있습니다.

## <a name="use-a-bearer-token"></a>전달자 토큰 사용

REST API를 호출할 때 전달자 토큰을 사용하려면 권한 부여 헤더가 다음 예제와 같습니다.

`Authorization: Bearer eyJ0eX...fNQ`

## <a name="use-an-api-token"></a>API 토큰 사용

REST API를 호출할 때 API 토큰을 사용하려면 권한 부여 헤더가 다음 예제와 같습니다.

`Authorization: SharedAccessSignature sr=e8a...&sig=jKY8W...&skn=operator-token&se=1647950487889`

## <a name="next-steps"></a>다음 단계

REST API 호출에 권한을 부여 하는 방법을 배웠으므로 이제 제안 된 다음 단계는 [IoT Central REST API를 사용](howto-query-with-rest-api.md)하 여 장치를 쿼리 하는 방법입니다.
