---
author: mattchenderson
ms.service: azure-functions
ms.topic: include
ms.date: 10/11/2021
ms.author: mahender
ms.openlocfilehash: 8cef6aa5daa8bbbdda6971724343e65ea7e47aef
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/14/2021
ms.locfileid: "129992774"
---
Azure Functions 서비스에서 호스트되는 경우 ID 기반 연결에 [관리 ID](../articles/app-service/overview-managed-identity.md?toc=%2fazure%2fazure-functions%2ftoc.json)가 사용됩니다. 사용자가 할당한 ID는 `credential` 및 `clientID` 속성을 사용하여 지정할 수 있지만 기본적으로 시스템 할당 ID가 사용됩니다. 로컬 개발과 같은 다른 컨텍스트에서 실행 하는 경우에는 사용자 지정할 수 있지만 대신 개발자 id를 사용 합니다. [Id 기반 연결을 사용한 로컬 개발을](../articles/azure-functions/functions-reference.md#local-development-with-identity-based-connections)참조 하세요.

#### <a name="grant-permission-to-the-identity"></a>ID에 권한 부여

사용되는 모든 ID에는 의도한 작업을 수행할 수 있는 권한이 있어야 합니다. 이러한 권한을 제공 하는 기본 제공 또는 사용자 지정 역할을 사용 하 여 [AZURE RBAC에서 역할을 할당](../articles/role-based-access-control/role-assignments-steps.md)해야 합니다.

> [!IMPORTANT]
> 일부 사용 권한은 모든 컨텍스트에 필요하지 않은 대상 서비스에 의해 노출될 수 있습니다. 가능한 경우 **최소 권한 원칙** 을 준수하여 ID에 필요한 권한만 부여하세요. 예를 들어 앱이 데이터 원본에서 읽을 수 있어야 하는 경우에만 읽을 수 있는 권한만 있는 역할을 사용 합니다. 읽기 작업에 대 한 과도 한 사용 권한이 있기 때문에 해당 서비스에 대 한 쓰기도 허용 하는 역할을 할당 하는 것은 적합 하지 않습니다. 마찬가지로, 읽기 해야 하는 리소스에 대해서만 역할 할당의 범위를 지정 해야 합니다.
