---
author: mattchenderson
ms.service: azure-functions
ms.topic: include
ms.date: 10/08/2021
ms.author: mahender
ms.openlocfilehash: 288539542c5b811206bd3e128dbceca317b2a27a
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/14/2021
ms.locfileid: "130137856"
---
## <a name="connections"></a>Connections

`connection`속성은 앱이 Service Bus 연결하는 방법을 지정하는 환경 구성에 대한 참조입니다. 다음을 지정할 수 있습니다.

- [연결 문자열을](#connection-string) 포함하는 애플리케이션 설정의 이름
- ID [기반 연결을](#identity-based-connections)정의하는 여러 애플리케이션 설정에 대한 공유 접두사 이름입니다.

구성된 값이 단일 설정과 정확히 일치하고 다른 설정에 대한 접두사 일치인 경우 정확한 일치가 사용됩니다.

### <a name="connection-string"></a>연결 문자열

연결 문자열을 얻으려면 [관리 자격 증명 가져오기](../articles/service-bus-messaging/service-bus-quickstart-portal.md#get-the-connection-string)에 나온 단계를 따릅니다. 연결 문자열은 Service Bus 네임스페이스에 대한 것이어야 하며, 특정 큐 또는 항목으로 제한되지 않습니다.

이 연결 문자열은 바인딩 구성의 속성에 지정된 값과 일치하는 이름을 가진 애플리케이션 설정에 저장되어야 `connection` 합니다.

앱 설정 이름이 "AzureWebJobs"로 시작하는 경우 이름의 나머지만을 지정할 수 있습니다. 예를 들어 `connection`을 "MyServiceBus"로 설정한 경우 Functions 런타임 기능은 "AzureWebJobsMyServiceBus"라는 앱 설정을 찾습니다. `connection`을 비워 두면 함수 런타임 기능은 "AzureWebJobsServiceBus"라는 앱 설정에서 기본 Service Bus 연결 문자열을 사용합니다.

### <a name="identity-based-connections"></a>ID 기반 연결

비밀이 있는 연결 문자열을 사용하는 대신 [버전 5.x 이상 확장](../articles/azure-functions/functions-bindings-service-bus.md#service-bus-extension-5x-and-higher)를 사용하는 경우 앱에서 Azure Active Directory [ID를](../articles/active-directory/fundamentals/active-directory-whatis.md)사용하도록 할 수 있습니다. 이렇게 하려면 트리거 및 바인딩 구성의 속성에 매핑되는 공통 접두사 아래에 설정을 `connection` 정의합니다.

이 모드에서는 확장에 다음 속성이 필요합니다.

| 속성                  | 환경 변수 템플릿                       | 설명                                | 예제 값 |
|---------------------------|-----------------------------------------------------|--------------------------------------------|---------|
| 정규화된 네임스페이스 | `<CONNECTION_NAME_PREFIX>__fullyQualifiedNamespace` | 정규화된 Service Bus 네임스페이스입니다. | <service_bus_namespace>.servicebus.windows.net  |

연결을 사용자 지정하기 위해 추가 속성을 설정할 수 있습니다. [ID 기반 연결에 대한 공용 속성을 참조하세요.](../articles/azure-functions/functions-reference.md#common-properties-for-identity-based-connections)

[!INCLUDE [functions-identity-based-connections-configuration](./functions-identity-based-connections-configuration.md)]

[!INCLUDE [functions-service-bus-permissions](./functions-service-bus-permissions.md)]