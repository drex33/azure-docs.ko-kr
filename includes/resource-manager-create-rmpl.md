---
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 07/29/2021
ms.author: tomfitz
ms.openlocfilehash: 735f19f1c9f66db6d311c42528b36a44bf924ec0
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/30/2021
ms.locfileid: "123227402"
---
프라이빗 링크를 사용하면 가상 네트워크의 프라이빗 엔드포인트를 통해 Azure 서비스에 액세스할 수 있습니다. 프라이빗 링크를 Azure Resource Manager의 작업과 결합하면 특정 엔드포인트에 있지 않은 사용자가 리소스를 관리하지 못하도록 차단합니다. 악의적인 사용자가 구독의 계정에 대한 자격 증명을 얻는 경우 해당 사용자는 특정 엔드포인트에 있지 않고는 리소스를 관리할 수 없습니다.

프라이빗 링크는 다음과 같은 보안 이점을 제공합니다.

* **프라이빗 액세스** - 사용자는 프라이빗 엔드포인트를 통해 개인 네트워크에서 리소스를 관리할 수 있습니다.
* **데이터 반출** - 범위에 포함되지 않은 리소스에 대한 사용자 액세스는 거부됩니다.

## <a name="understand-architecture"></a>아키텍처 이해

이 릴리스의 경우 루트 [관리 그룹](../articles/governance/management-groups/overview.md) 수준에서만 프라이빗 링크 관리 액세스 권한을 적용할 수 있습니다. 이 제한은 프라이빗 링크 액세스가 테넌트 전체에 적용됨을 의미합니다.

프라이빗 링크를 통해 관리를 구현할 때 사용할 두 가지 리소스 종류가 있습니다.

* 리소스 관리 프라이빗 링크(Microsoft.Authorization/resourceManagementPrivateLinks)
* 프라이빗 링크 연결(Microsoft.Authorization/privateLinkAssociations)

다음 이미지는 리소스 관리를 위한 액세스를 제한하는 솔루션을 구성하는 방법을 보여 줍니다.

:::image type="content" source="./media/resource-manager-create-rmpl/resource-management-private-link.svg" alt-text="리소스 관리 프라이빗 링크 다이어그램":::

프라이빗 링크 연결은 루트 관리 그룹을 확장합니다. 프라이빗 링크 연결 및 프라이빗 엔드포인트는 리소스 관리 프라이빗 링크를 참조합니다.

## <a name="workflow"></a>워크플로

리소스에 대한 프라이빗 링크를 설정하려면 다음 단계를 따르세요. 단계는 이 문서의 뒷부분에서 자세히 설명합니다.

1. 리소스 관리 프라이빗 링크를 만듭니다.
1. 프라이빗 링크 연결을 만듭니다. 프라이빗 링크 연결은 루트 관리 그룹을 확장합니다. 또한 리소스 관리 프라이빗 링크에 대한 리소스 ID를 참조합니다.
1. 리소스 관리 프라이빗 링크를 참조하는 프라이빗 엔드포인트를 추가합니다.

이러한 단계를 완료한 후 범위의 계층 구조 내에 있는 Azure 리소스를 관리할 수 있습니다. 서브넷에 연결된 프라이빗 엔드포인트를 사용합니다.

프라이빗 링크에 대한 액세스를 모니터링할 수 있습니다. 자세한 내용은 [로깅 및 모니터링](../articles/private-link/private-link-overview.md#logging-and-monitoring)을 참조하세요.

## <a name="required-permissions"></a>필요한 사용 권한

리소스 관리를 위한 프라이빗 링크를 설정하려면 다음 액세스 권한이 필요합니다.

* 구독의 소유자입니다. 이 액세스는 리소스 관리 프라이빗 링크 리소스를 만드는 데 필요합니다.
* 루트 관리 그룹의 소유자 또는 기여자. 이 액세스는 프라이빗 링크 연결 리소스를 만드는 데 필요합니다.
* Azure Active Directory의 전역 관리자에게 루트 관리 그룹에서 역할을 할당할 수 있는 권한이 자동으로 생기지는 않습니다. 리소스 관리 프라이빗 링크 만들기를 사용하도록 설정하려면 전역 관리자가 테넌트의 모든 구독 및 관리 그룹에 대한 사용자 액세스 관리자 권한을 갖도록 루트 관리 그룹을 읽고 [액세스 권한을 상승](../articles/role-based-access-control/elevate-access-global-admin.md)할 수 있는 권한이 있어야 합니다. 사용자 액세스 관리자 권한을 얻은 후 전역 관리자는 프라이빗 링크 연결을 만드는 사용자에게 루트 관리 그룹의 소유자 또는 기여자 권한을 부여해야 합니다.