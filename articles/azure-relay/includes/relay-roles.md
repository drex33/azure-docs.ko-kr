---
author: spelluru
ms.service: service-bus-relay
ms.topic: include
ms.date: 07/19/2021
ms.author: spelluru
ms.openlocfilehash: 43550a98e5df6c58214427a989e290b3c69ab338
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/01/2021
ms.locfileid: "129378493"
---
## <a name="overview"></a>개요
보안 주체(사용자, 그룹, 애플리케이션)가 Relay 엔터티에 액세스하려고 하는 경우 요청에 권한이 부여되어야 합니다. Azure AD의 경우 리소스에 대한 액세스는 2단계 프로세스입니다.

1. 먼저 보안 주체의 ID가 **인증되고** OAuth 2.0 토큰이 반환됩니다. 토큰을 요청하는 리소스 이름은 `https://relay.azure.net`입니다. Azure VM, 가상 머신 확장 집합 또는 Azure Function 앱과 같은 Azure 엔터티 내에서 애플리케이션이 실행되는 경우 관리 ID를 사용하여 리소스에 액세스할 수 있습니다.
2. 그 다음, 토큰은 지정된 리소스(하이브리드 연결, WCF 릴레이)에 대한 액세스 **권한을 부여** 하기 위해 Relay 서비스에 요청의 일부로 전달됩니다. Azure AD(Azure Active Directory)는 [Azure RBAC](../../role-based-access-control/overview.md)(Azure 역할 기반 액세스 제어)를 통해 보안 리소스에 대한 액세스 권한을 부여합니다. Azure Relay는 Relay 엔터티에 액세스하는 데 사용되는 일반 권한 집합을 포함하는 Azure 기본 제공 역할의 집합을 정의합니다. 데이터에 액세스하기 위한 사용자 지정 역할을 정의할 수도 있습니다. Azure Relay에서 지원하는 기본 제공 역할 목록은 [Azure Relay에 대한 Azure 기본 제공 역할](#azure-built-in-roles-for-azure-relay)을 참조하세요. Relay에 요청하는 네이티브 애플리케이션 및 웹 애플리케이션도 Azure AD를 사용하여 권한을 부여할 수 있습니다.  

## <a name="azure-built-in-roles-for-azure-relay"></a>Azure Relay에 대한 Azure 기본 제공 역할
Azure Relay의 경우 Azure Portal 및 Azure 리소스 관리 API를 통한 네임스페이스 및 관련된 모든 리소스의 관리는 이미 Azure RBAC 모델을 사용하여 보호되고 있습니다. Azure는 Relay 네임스페이스에 대한 액세스 권한을 부여하기 위해 아래와 같은 Azure 기본 제공 역할을 제공합니다.

| 역할 | 설명 | 
| ---- | ----------- | 
| [Azure Relay 소유자](../../role-based-access-control/built-in-roles.md#azure-relay-owner) | 이 역할을 사용하여 Azure Relay 리소스에 대한 **전체** 액세스 권한을 부여합니다. |
| [Azure Relay 수신자](../../role-based-access-control/built-in-roles.md#azure-relay-listener) | 이 역할을 사용하여 Azure Relay 리소스에 대한 **수신 대기 및 엔터티 읽기** 액세스 권한을 부여합니다. |
| [Azure Relay 발신자](../../role-based-access-control/built-in-roles.md#azure-relay-sender) | 이 역할을 사용하여 Azure Relay 리소스에 대한 **보내기 및 엔터티 읽기** 액세스 권한을 부여합니다. | 

## <a name="resource-scope"></a>리소스 범위
Azure 역할을 보안 주체에 할당하기 전에 보안 주체에게 부여해야 하는 액세스 범위를 결정합니다. 모범 사례에 따르면 항상 가능한 가장 좁은 범위만 부여하는 것이 가장 좋습니다.

다음 목록에서는 Azure Relay 리소스에 대한 액세스 범위를 가장 좁은 범위에서 시작하여 지정할 수 있는 수준을 설명합니다.

- **Relay 엔터티**: 역할 할당은 하이브리드 연결 또는 WCF 릴레이와 같은 특정 Relay 엔터티에 적용됩니다.
- **Relay 네임스페이스**: 역할 할당은 네임스페이스 아래의 모든 Relay 엔터티에 적용됩니다.
- **리소스 그룹**: 역할 할당이 리소스 그룹 아래에 있는 모든 Relay 리소스에 적용됩니다.
- **구독**: 역할 할당이 구독의 모든 리소스 그룹에 있는 모든 Relay 리소스에 적용됩니다.

> [!NOTE]
> Azure 역할 할당을 전파하는 데 최대 5분이 걸릴 수 있음에 유의하세요. 기본 제공 역할이 정의되는 방법에 대한 자세한 내용은 [역할 정의 이해](../../role-based-access-control/role-definitions.md#control-and-data-actions)를 참조하세요. Azure 사용자 지정 역할 만들기에 대한 자세한 내용은 [Azure 사용자 지정 역할](../../role-based-access-control/custom-roles.md)을 참조하세요. 

