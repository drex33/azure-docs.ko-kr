---
title: Azure Functions를 사용하여 Azure Static Web Apps에서 API 지원
description: Azure Static Web Apps가 지원하는 API 기능 알아보기
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: conceptual
ms.date: 11/08/2021
ms.author: cshoe
ms.openlocfilehash: b7ab83ddf6d3f20c28d030d48b50ebb88d6676ae
ms.sourcegitcommit: 4cd97e7c960f34cb3f248a0f384956174cdaf19f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/08/2021
ms.locfileid: "132027473"
---
# <a name="api-support-in-azure-static-web-apps-with-azure-functions"></a>Azure Functions를 사용하여 Azure Static Web Apps에서 API 지원

Azure Static Web Apps는 [Azure Functions](../azure-functions/functions-overview.md)를 통해 서버리스 API 엔드포인트를 제공합니다. API는 Azure Functions를 사용하여 수요에 따라 동적으로 확장되며 다음 기능을 포함합니다.

- 사용자 [인증 및 역할 기반 권한 부여](user-information.md) 데이터에 직접 액세스할 수 있는 **통합 보안**.

- 사용자 지정 CORS 규칙 없이 웹앱에서 _api_ 경로를 안전하게 사용할 수 있도록 하는 **원활한 라우팅**.

Azure Static Web Apps API는 [호스팅 계획에](plans.md#features)따라 두 가지 가능한 구성에서 지원됩니다.

- **관리형 함수**: 기본적으로 정적 웹앱의 API는 몇 가지 제한과 관련된 Azure Static Web Apps에서 관리 및 배포하는 Azure Functions 애플리케이션입니다.

- **사용자 고유의 함수 가져오기**: 필요에 따라 Azure Functions의 모든 기능이 함께 제공되는 모든 계획 유형의 [기존 Azure Functions 애플리케이션을 제공](functions-bring-your-own.md)할 수 있습니다. 이 구성을 사용하는 경우 Functions 앱에 대한 별도의 배포를 처리해야 합니다.

다음 표에서는 관리형 함수와 기존 함수를 사용하는 경우의 차이점을 대조합니다.

| 기능 | 관리형 함수 | 자체 Functions 가져오기 |
| --- | --- | --- |
| Azure Functions [트리거](../azure-functions/functions-triggers-bindings.md#supported-bindings)에 대한 액세스 | Http 전용 | 모두 |
| 지원되는 Azure Functions [런타임](../azure-functions/supported-languages.md#languages-by-runtime-version) | Node.js 12<br>.NET Core 3.1<br>.NET 6.0<br>Python 3.8 | 모두 |
| 지원되는 Azure Functions [호스팅 계획](../azure-functions/functions-scale.md) | Consumption | Consumption<br>Premium<br>전용 |
| 사용자 인증 및 역할 기반 권한 부여 데이터에 직접 액세스할 수 있는 [통합 보안](user-information.md) | ✔ | ✔ |
| 사용자 지정 CORS 규칙 없이 웹앱에서 _api_ 경로를 안전하게 사용할 수 있도록 하는 [라우팅 통합](./configuration.md?#routes). | ✔ | ✔ |
| [Durable Functions](../azure-functions/durable/durable-functions-overview.md) 프로그래밍 모델 | ✕ | ✔ |
| [관리 ID](../app-service/overview-managed-identity.md) | ✕ | ✔ |
| [Azure App Service 인증 및 권한](../app-service/configure-authentication-provider-aad.md) 토큰 관리 | ✕ | ✔ |
| Azure Static Web Apps 외부에서 사용할 수 있는 API 함수 | ✕ | ✔ |
| [Key Vault 참조](../app-service/app-service-key-vault-references.md) | ✕ | ✔ |

## <a name="configuration"></a>구성

API 엔드포인트는 _api_ 경로를 통해 웹앱에서 사용할 수 있습니다.

| 관리형 함수 | 사용자 고유 함수 가져오기 |
| --- | --- |
| _API_ 경로가 고정되어 있는 동안 관리되는 함수 앱의 소스 코드 폴더 위치를 제어할 수 있습니다. 리포지토리의 _.github/workflows_ 에 있는 [워크플로 YAML 파일을 편집](build-configuration.md)하여 이 위치를 변경할 수 있습니다. | _api_ 경로에 대한 요청은 기존 Azure Functions 앱으로 전송됩니다. |

## <a name="troubleshooting-and-logs"></a>문제 해결 및 로그

로그는 [Application Insights](monitor.md)를 추가하는 경우에만 사용할 수 있습니다.

| 관리형 함수 | 사용자 고유 함수 가져오기 |
| --- | --- |
| 정적 웹앱에서 Application Insights를 활성화하여 로깅을 설정합니다. | Azure Functions 앱에서 Application Insights를 활성화하여 로깅을 설정합니다. |

## <a name="constraints"></a>제약 조건

- API 경로 접두사는 _api_ 여야 합니다.
- API 함수에 대한 경로 규칙은 [리디렉션](configuration.md#defining-routes) 및 [역할을 사용한 경로 보안](configuration.md#securing-routes-with-roles)만 지원합니다.

| 관리형 함수 | 사용자 고유 함수 가져오기 |
| --- | --- |
| <ul><li>트리거는 [HTTP](../azure-functions/functions-bindings-http-webhook.md)로 제한됩니다.</li><li>Azure Functions 앱은 Node.js 12, .NET Core 3.1, .NET 6.0 또는 Python 3.8에 있어야 합니다.</li><li>일부 애플리케이션 설정은 서비스에서 관리되므로 다음 접두사는 런타임에 예약됩니다.<ul><li>*APPSETTING\_, AZUREBLOBSTORAGE\_, AZUREFILESSTORAGE\_, AZURE_FUNCTION\_, CONTAINER\_, DIAGNOSTICS\_, DOCKER\_, FUNCTIONS\_, IDENTITY\_, MACHINEKEY\_, MAINSITE\_, MSDEPLOY\_, SCMSITE\_, SCM\_, WEBSITES\_, WEBSITE\_, WEBSOCKET\_, AzureWeb*</li></ul></li></ul> | <ul><li>Functions 앱 배포를 관리해야 합니다.</li></ul> |

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [API 추가](add-api.md)
