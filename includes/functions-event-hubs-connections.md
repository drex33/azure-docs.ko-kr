---
author: mattchenderson
ms.service: azure-functions
ms.topic: include
ms.date: 10/08/2021
ms.author: mahender
ms.openlocfilehash: 3d1ebc666a1d4c6763b080decdd50fd44b3837dc
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/14/2021
ms.locfileid: "129993318"
---
## <a name="connections"></a>Connections

`connection`속성은 앱이 Event Hubs에 연결 하는 방법을 지정 하는 환경 구성에 대 한 참조입니다. 다음과 같이 지정할 수 있습니다.

- [연결 문자열](#connection-string) 을 포함 하는 응용 프로그램 설정의 이름입니다.
- 여러 응용 프로그램 설정에 대 한 공유 접두사의 이름으로, [id 기반 연결](#identity-based-connections)을 정의 합니다.

구성 된 값이 단일 설정과 정확히 일치 하 고 다른 설정에 대 한 접두사 일치 이면 정확히 일치 하는 항목이 사용 됩니다.

### <a name="connection-string"></a>연결 문자열

이벤트 허브 자체가 아닌 [네임 스페이스](../articles/event-hubs/event-hubs-create.md#create-an-event-hubs-namespace)에 대 한 **연결 정보** 단추를 클릭 하 여이 연결 문자열을 가져옵니다. 연결 문자열은 이벤트 허브 자체가 아닌 Event Hubs 네임 스페이스에 대 한 것 이어야 합니다.

트리거에 사용 되는 경우 연결 문자열에는 함수를 활성화 하는 "읽기" 이상의 권한이 있어야 합니다. 출력 바인딩에 사용 하는 경우 연결 문자열에 이벤트 스트림에 메시지를 보낼 수 있는 "보내기" 권한이 있어야 합니다.

이 연결 문자열은 `connection` 바인딩 구성의 속성에 지정 된 값과 일치 하는 이름을 가진 응용 프로그램 설정에 저장 해야 합니다.

### <a name="identity-based-connections"></a>ID 기반 연결

암호를 사용 하 여 연결 문자열을 사용 하는 대신 [버전 4.x 이상의 확장](../articles/azure-functions/functions-bindings-event-hubs.md#event-hubs-extension-5x-and-higher)을 사용 하는 경우 앱에서 [Azure Active Directory id](../articles/active-directory/fundamentals/active-directory-whatis.md)를 사용 하도록 할 수 있습니다. 이렇게 하려면 `connection` 트리거 및 바인딩 구성의 속성에 매핑되는 일반적인 접두사 아래에 설정을 정의 합니다.

이 모드에서 확장에는 다음 속성이 필요 합니다.

| 속성                  | 환경 변수 템플릿                       | 설명                                | 예제 값                                        |
|---------------------------|-----------------------------------------------------|--------------------------------------------|------------------------------------------------|
| 정규화된 네임스페이스 | `<CONNECTION_NAME_PREFIX>__fullyQualifiedNamespace` | 정규화 된 Event Hubs 네임 스페이스입니다. | <event_hubs_namespace>. servicebus.windows.net |

추가 속성을 설정 하 여 연결을 사용자 지정할 수 있습니다. [Id 기반 연결에 대 한 공용 속성을](../articles/azure-functions/functions-reference.md#common-properties-for-identity-based-connections)참조 하세요.

[!INCLUDE [functions-identity-based-connections-configuration](./functions-identity-based-connections-configuration.md)]

[!INCLUDE [functions-event-hubs-permissions](./functions-event-hubs-permissions.md)]
