---
title: Azure Static Web Apps에 대한 BYOF(Bring your own Functions)
description: Azure Static Web Apps 사이트에서 기존 Azure Functions 앱을 사용합니다.
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: how-to
ms.date: 05/07/2021
ms.author: cshoe
ms.openlocfilehash: 7925bd70488106943f0030e6b26534938fbc7d36
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128570895"
---
# <a name="bring-your-own-functions-to-azure-static-web-apps"></a>Azure Static Web Apps에 대한 BYOF(Bring your own Functions)

Azure Static Web Apps API는 관리형 함수와 BYOF(Bring your own Functions)의 두 가지 가능한 구성에서 지원됩니다. 두 구성 간 세부 정보에 대한 자세한 내용은 [API 참조](apis.md)를 참조하세요.

이 문서에서는 Azure Static Web Apps 리소스에 기존 Azure Functions 앱을 연결하는 방법을 보여 줍니다.

> [!NOTE]
> BYOF(Bring your own Functions)는 Azure Static Web Apps 표준 플랜에서만 사용할 수 있습니다.

## <a name="example"></a>예제

다음 위치를 통해 엔드포인트를 노출하는 기존 Azure Functions 앱을 고려합니다.

```url
https://my-functions-app.azurewebsites.net/api/getProducts
```

연결되면 이 예제 URL에 표시된 것처럼 정적 웹앱에서 `api` 경로를 통해 동일한 엔드포인트에 액세스할 수 있습니다.

```url
https://red-sea-123.azurestaticapps.net/api/getProducts
```

 두 엔드포인트 URL은 동일한 함수를 가리킵니다.

## <a name="link-an-existing-azure-functions-app"></a>기존 Azure Functions 앱 연결

기존 함수 앱을 연결하기 전에 먼저 정적 웹앱의 구성으로 조정해야 합니다.

1. [워크플로 구성](./build-configuration.md) 파일에서 `api_location` 값을 빈 문자열(`""`)로 설정합니다.

1. [Azure Portal](https://portal.azure.com)에서 Static Web Apps 인스턴스를 엽니다.

1. _설정_ 메뉴에서 **함수** 를 선택합니다.

1. _환경_ 드롭다운에서 **프로덕션** 을 선택합니다.

1. _함수 원본_ 레이블 옆에 있는 **함수 앱에 연결** 을 선택합니다.

1. _구독_ 드롭다운에서 Azure 구독 이름을 선택합니다.

1. _함수 앱_ 드롭다운에서 정적 웹앱에 연결하려는 기존 함수 앱의 이름을 선택합니다.

1. **연결** 단추를 선택합니다.

    :::image type="content" source="media/functions-bring-your-own/azure-static-web-apps-link-existing-functions-app.png" alt-text="기존 함수 앱 연결":::

> [!IMPORTANT]
> 기존 함수 애플리케이션을 연결하기 전에 [워크플로 구성](./build-configuration.md) 파일에서 `api_location` 값을 빈 문자열(`""`)로 설정해야 합니다.

## <a name="deployment"></a>배포

Azure Functions 앱에 대한 [배포 워크플로](../azure-functions/functions-deployment-technologies.md)를 설정해야 합니다.

## <a name="security-constraints"></a>보안 제약 조건

- **인증 및 권한 부여:** 기존 함수 앱에 인증 및 권한 부여 정책이 아직 설정되지 않은 경우 정적 웹앱은 API에 단독으로 액세스할 수 있습니다. 다른 애플리케이션에서 함수 앱에 액세스할 수 있도록 하려면 다른 ID 공급자를 추가하거나 인증되지 않은 액세스를 허용하도록 보안 설정을 변경합니다.

  > [!NOTE]
  > 연결된 Functions 앱에서 인증 및 권한 부여를 사용하도록 설정하는 경우 Azure App Service 인증 및 권한 부여 공급자 버전 2를 사용해야 합니다.

- **필요한 퍼블릭 액세스 가능성:** 기존 함수 앱은 다음 보안 구성을 적용할 필요가 없습니다.
  - 함수 앱의 IP 주소 제한
  - 프라이빗 링크 또는 서비스 엔드포인트를 통해 트래픽 제한

- **함수 액세스 키:** 함수에 [액세스 키](../azure-functions/security-concepts.md#function-access-keys)가 필요한 경우 정적 앱에서 API로의 호출에 해당 키를 제공해야 합니다.

## <a name="restrictions"></a>제한

- 단일 정적 웹앱에서는 하나의 Azure Functions 앱만 사용할 수 있습니다.
- [워크플로 구성](./build-configuration.md)의 `api_location` 값을 빈 문자열로 설정해야 합니다.
- Static Web Apps 프로덕션 환경에서만 지원됩니다.
- Azure Functions 앱은 다양한 트리거에 응답할 수 있지만 정적 웹앱은 Http 엔드포인트를 통해서만 함수에 액세스할 수 있습니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [API 추가](add-api.md)
