---
title: 리소스를 등록하는 방법
titleSuffix: Azure Network Function Manager
description: 리소스를 등록하고 사용자 할당 관리 ID를 만드는 방법 알아보기
author: prmitt
ms.service: network-function-manager
ms.topic: how-to
ms.date: 11/02/2021
ms.author: prmitt
ms.custom: ignite-fall-2021
ms.openlocfilehash: e42527e5fdca01b58c08458bc360268b5d36d7d1
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131102996"
---
# <a name="how-to-register-resources-and-create-user-assigned-managed-identities"></a>리소스를 등록하고 사용자 할당 관리 ID를 만드는 방법

이 문서는 디바이스 리소스를 만들고, 관리되는 애플리케이션 리소스를 만들고, 네트워크 기능을 배포하기 위한 사용자 할당 관리 ID에 대한 요구 사항을 이해하는 데 도움이 됩니다.

## <a name="resource-provider-registration-and-permissions"></a><a name="permissions"></a>리소스 공급자 등록 및 권한

Azure Network Function Manager 리소스는 Microsoft.HybridNetwork 리소스 공급자 내에 있습니다. Microsoft.HybridNetwork 리소스 공급자에 구독 ID를 등록합니다. 등록 방법에 관한 자세한 내용은 [Azure 리소스 공급자 및 종류](../azure-resource-manager/management/resource-providers-and-types.md)를 참조하세요.

### <a name="device-resource-accounts"></a>디바이스 리소스 계정

Network Function Manager 디바이스 리소스를 만드는 데 사용하는 계정은 다음 표에서 필요한 작업이 할당된 사용자 지정 역할에 할당되어야 합니다. 자세한 내용은 [사용자 지정 역할](../role-based-access-control/custom-roles.md)을 참조하세요.

| 속성 | 작업|
|---|---|
| Microsoft.DataBoxEdge/dataBoxEdgeDevices/read|네트워크 기능이 배포되는 Azure Stack Edge 리소스를 읽는 데 필요합니다. |
|Microsoft.DataBoxEdge/dataBoxEdgeDevices/getExtendedInformation/action |Azure Stack Edge 리소스의 속성 섹션을 읽는 데 필요합니다. |
|Microsoft.DataBoxEdge/dataBoxEdgeDevices/roles/write |Azure Stack Edge 리소스에서 Network Function Manager 디바이스 리소스를 만드는 데 필요합니다.|
| Microsoft.HybridNetwork/devices/* | Network Function Manager 디바이스 리소스를 만들고, 업데이트하고, 삭제하는 데 필요합니다. |

### <a name="managed-applications-resource-accounts"></a>관리되는 애플리케이션 리소스 계정

Azure 관리형 애플리케이션 리소스를 만드는 데 사용하는 계정은 다음 표에서 필요한 작업이 할당된 [사용자 지정 역할](../role-based-access-control/custom-roles.md)에 할당되어야 합니다. 

|속성 |작업 |
|---|---|
|[관리형 애플리케이션 기여자 역할](../role-based-access-control/built-in-roles.md#managed-application-contributor-role)|관리형 앱 리소스를 만드는 데 필요합니다.|

## <a name="managed-identity"></a><a name="managed-identity"></a>관리 ID

Network Function Manager를 사용하여 Azure 관리형 애플리케이션을 제공하는 네트워크 기능 파트너는 기존 Network Function Manager 디바이스 리소스에 연결된 관리형 애플리케이션을 배포할 수 있는 환경을 제공합니다. Azure Portal 파트너 관리형 애플리케이션을 배포하는 경우 네트워크 함수 관리자 디바이스 리소스에 액세스할 수 있는 Azure 사용자 할당 관리 ID 리소스를 제공해야 합니다. 사용자가 할당한 관리 ID를 사용하면 관리형 애플리케이션 리소스 공급자와 네트워크 기능 게시자가 관리되는 리소스 그룹 외부에 배포된 Network Function Manager 디바이스 리소스에 대한 적절한 권한이 허용됩니다. 자세한 내용은 [Azure Portal에서 사용자가 할당한 관리 ID 관리](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)를 참조하세요.

네트워크 기능을 배포하기 위한 사용자가 할당한 관리 ID를 만들려면:

1. 사용자가 할당한 관리 ID를 만들고 Microsoft.HybridNetwork/devices/join/action에 대한 권한이 있는 사용자 지정 역할에 할당합니다. 자세한 내용은 [Azure Portal에서 사용자가 할당한 관리 ID 관리](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)를 참조하세요.

1. Azure Portal에서 파트너의 관리형 애플리케이션을 만들 때 이 관리 ID를 제공합니다. 자세한 내용은 [Azure Portal을 사용하여 리소스에 대한 관리 ID 액세스 할당](../active-directory/managed-identities-azure-resources/howto-assign-access-portal.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계

자세한 내용은 네트워크 [함수 관리자 FAQ를 참조하세요.](faq.md)
