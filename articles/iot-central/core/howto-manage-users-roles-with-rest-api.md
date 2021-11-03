---
title: REST API를 사용하여 Azure IoT Central에서 사용자 및 역할 관리
description: IoT Central REST API를 사용하여 애플리케이션에서 사용자 및 역할을 관리하는 방법
author: dominicbetts
ms.author: dobett
ms.date: 08/30/2021
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.openlocfilehash: d9892efecf010ea698e9f628cd92308e9ac8608b
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131078506"
---
# <a name="how-to-use-the-iot-central-rest-api-to-manage-users-and-roles"></a>IoT Central REST API를 사용하여 사용자 및 역할을 관리하는 방법

IoT Central REST API를 사용하면 IoT Central 애플리케이션과 통합되는 클라이언트 애플리케이션을 개발할 수 있습니다. REST API를 사용하여 IoT Central 애플리케이션에서 사용자 및 역할을 관리할 수 있습니다.

모든 IoT Central REST API 호출에는 권한 부여 헤더가 필요합니다. 자세히 알아보려면 [IoT Central REST API 호출을 인증하고 권한을 부여하는 방법](howto-authorize-rest-api.md)을 참조하세요.

IoT Central REST API에 대한 참조 설명서는 [Azure IoT Central REST API 참조](/rest/api/iotcentral/)를 참조하세요.

> [!TIP]
> [PREVIEW API](/rest/api/iotcentral/1.1-previewdataplane/users) 에는 새 [조직 기능](howto-create-organizations.md)에 대 한 지원이 포함 되어 있습니다.

## <a name="manage-roles"></a>역할 관리

REST API를 사용하여 IoT Central 애플리케이션에 정의된 역할을 나열할 수 있습니다. 애플리케이션에서 역할 ID 목록을 검색하려면 다음 요청을 사용합니다.

```http
GET https://{your app subdomain}.azureiotcentral.com/api/roles?api-version=1.0
```

이 요청에 대한 응답은 세 가지 기본 제공 역할과 사용자 지정 역할이 포함된 다음 예제와 같습니다.

```json
{
  "value": [
    {
      "id": "ca310b8d-2f4a-44e0-a36e-957c202cd8d4",
      "displayName": "Administrator"
    },
    {
      "id": "ae2c9854-393b-4f97-8c42-479d70ce626e",
      "displayName": "Operator"
    },
    {
      "id": "344138e9-8de4-4497-8c54-5237e96d6aaf",
      "displayName": "Builder"
    },
    {
      "id": "16f8533f-6b82-478f-8ba8-7e676b541b1b",
      "displayName": "Example custom role"
    }
  ]
}
```

## <a name="manage-users"></a>사용자 관리

REST API를 사용하면 다음을 수행할 수 있습니다.

- 애플리케이션의 사용자 나열
- 개별 사용자의 세부 정보 검색
- 사용자 만들기
- 사용자 수정
- 사용자 삭제

### <a name="list-users"></a>사용자 나열

애플리케이션에서 사용자 목록을 검색하려면 다음 요청을 사용합니다.

```http
GET https://{your app subdomain}.azureiotcentral.com/api/users?api-version=1.0
```

이 요청에 대한 응답은 다음 예제와 같습니다. 역할 값은 사용자가 연결된 역할 ID를 식별합니다.

```json
{
  "value": [
    {
      "id": "91907508-04fe-4349-91b5-b872f3055a95",
      "type": "email",
      "roles": [
        {
          "role": "ca310b8d-2f4a-44e0-a36e-957c202cd8d4"
        }
      ],
      "email": "user1@contoso.com"
    },
    {
      "id": "dc1c916b-a652-49ea-b128-7c465a54c759",
      "type": "email",
      "roles": [
        {
          "role": "ae2c9854-393b-4f97-8c42-479d70ce626e"
        }
      ],
      "email": "user2@contoso.com"
    },
    {
      "id": "3ab9375e-d2d9-42da-b419-6ae86a938321",
      "type": "email",
      "roles": [
        {
          "role": "344138e9-8de4-4497-8c54-5237e96d6aaf"
        }
      ],
      "email": "user3@contoso.com"
    },
    {
      "id": "fc5a250b-83fb-433d-892c-e0a144f68c2b",
      "type": "email",
      "roles": [
        {
          "role": "16f8533f-6b82-478f-8ba8-7e676b541b1b"
        }
      ],
      "email": "user4@contoso.com"
    }
  ]
}
```

### <a name="get-a-user"></a>사용자 가져오기

애플리케이션에서 개별 사용자의 세부 정보를 검색하려면 다음 요청을 사용합니다.

```http
GET https://{your app subdomain}.azureiotcentral.com/api/users/dc1c916b-a652-49ea-b128-7c465a54c759?api-version=1.0
```

이 요청에 대한 응답은 다음 예제와 같습니다. 역할 값은 사용자가 연결된 역할 ID를 식별합니다.

```json
{
  "id": "dc1c916b-a652-49ea-b128-7c465a54c759",
  "type": "email",
  "roles": [
    {
      "role": "ae2c9854-393b-4f97-8c42-479d70ce626e"
    }
  ],
  "email": "user2@contoso.com"
}
```

### <a name="create-a-user"></a>사용자 만들기

애플리케이션에서 사용자를 만들려면 다음 요청을 사용합니다. ID 및 메일은 애플리케이션에서 고유해야 합니다.

```http
PUT https://{your app subdomain}.azureiotcentral.com/api/users/user-001?api-version=1.0
```

다음 요청 본문에서 `role` 값은 이전에 검색한 운영자 역할에 대한 값입니다.

```json
{
  "id": "user-001",
  "type": "email",
  "roles": [
    {
      "role": "ae2c9854-393b-4f97-8c42-479d70ce626e"
    }
  ],
  "email": "user5@contoso.com"
}
```

이 요청에 대한 응답은 다음 예제와 같습니다. 역할 값은 사용자가 연결된 역할을 식별합니다.

```json
{
  "id": "user-001",
  "type": "email",
  "roles": [
    {
      "role": "ae2c9854-393b-4f97-8c42-479d70ce626e"
    }
  ],
  "email": "user5@contoso.com"
}
```

REST API 호출에 서비스 주체 인증을 사용해야 하는 경우에 유용한 서비스 주체 사용자를 추가할 수도 있습니다. 자세한 내용은 [서비스 주체 사용자 추가 또는 업데이트](/rest/api/iotcentral/1.0dataplane/users/create#add-or-update-a-service-principal-user)를 참조하세요.

### <a name="change-the-role-of-a-user"></a>사용자의 역할 변경

다음 요청을 사용하여 사용자에게 할당된 역할을 변경합니다. 이 예에서는 이전에 검색한 작성기 역할의 ID를 사용합니다.

```http
PATCH https://{your app subdomain}.azureiotcentral.com/api/users/user-001?api-version=1.0
```

요청 본문. 이 값은 이전에 검색한 작성기 역할에 대한 것입니다.

```json
{
  "roles": [
    {
      "role": "344138e9-8de4-4497-8c54-5237e96d6aaf"
    }
  ]
}
```

이 요청에 대한 응답은 다음 예제와 같습니다.

```json
{
  "id": "user-001",
  "type": "email",
  "roles": [
    {
      "role": "344138e9-8de4-4497-8c54-5237e96d6aaf"
    }
  ],
  "email": "user5@contoso.com"
}
```

### <a name="delete-a-user"></a>사용자 삭제

사용자를 삭제하려면 다음 요청을 사용합니다.

```http
DELETE https://{your app subdomain}.azureiotcentral.com/api/users/user-001?api-version=1.0
```

## <a name="next-steps"></a>다음 단계

이제 REST API를 사용 하 여 사용자 및 역할을 관리 하는 방법을 배웠으므로 제안 된 다음 단계는 [IoT Central REST API를 사용 하 여 데이터 내보내기를 관리 하는 방법을 설명](howto-manage-data-export-with-rest-api.md) 하는 것입니다.