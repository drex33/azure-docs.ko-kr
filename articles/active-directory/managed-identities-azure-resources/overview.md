---
title: Azure 리소스에 대한 관리 ID
description: Azure 리소스에 대한 관리 ID 개요
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.assetid: 0232041d-b8f5-4bd2-8d11-27999ad69370
ms.service: active-directory
ms.subservice: msi
ms.devlang: ''
ms.topic: overview
ms.custom: mvc
ms.date: 08/26/2021
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4f79fc66b44de89ba93137b244e22890348ab1c4
ms.sourcegitcommit: 47fac4a88c6e23fb2aee8ebb093f15d8b19819ad
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2021
ms.locfileid: "122967206"
---
# <a name="what-are-managed-identities-for-azure-resources"></a>Azure 리소스용 관리 ID란?

개발자는 일반적으로 솔루션을 구성하는 서로 다른 구성 요소 간의 통신을 보호하는 데 사용되는 비밀과 자격 증명을 관리하는 데 어려움을 겪고 있습니다. 관리 ID를 통해 개발자는 자격 증명을 관리할 필요가 없습니다. 관리 ID는 Azure AD(Azure Active Directory) 인증을 지원하는 리소스에 연결할 때 사용할 애플리케이션의 ID를 제공합니다. 애플리케이션은 관리 ID를 사용하여 Azure AD 토큰을 가져올 수 있습니다. 예를 들어 애플리케이션은 관리 ID를 사용하여 개발자가 자격 증명을 안전하게 저장하거나 스토리지 계정에 액세스할 수 있는 [Azure Key Vault](../../key-vault/general/overview.md)와 같은 리소스에 액세스할 수 있습니다.

관리 ID를 사용하는 방법 살펴보기</br>

> [!VIDEO https://channel9.msdn.com/Shows/On-NET/Using-Azure-Managed-identities/player?format=ny]



관리 ID를 사용하는 경우 얻을 수 있는 몇 가지 혜택은 다음과 같습니다.

- 자격 증명을 관리할 필요가 없으며, 자격 증명에 액세스할 수도 없습니다.
- 관리 ID를 사용하여 사용자 고유의 애플리케이션을 포함하여 [Azure Active Directory 인증](../authentication/overview-authentication.md)을 지원하는 모든 리소스에 인증할 수 있습니다.
- 관리 ID는 추가 비용 없이 사용할 수 있습니다.

> [!NOTE]
> Azure 리소스에 대한 관리 ID는 이전에 MSI(관리 서비스 ID)로 알려진 서비스에 대한 새 이름입니다.

## <a name="managed-identity-types"></a>관리 ID 유형

두 가지 종류의 관리 ID가 있습니다.

- **시스템 할당** 일부 Azure 서비스를 사용하면 서비스 인스턴스에서 직접 관리 ID를 사용하도록 설정할 수 있습니다. 시스템이 할당한 관리 ID를 사용하도록 설정하면 해당 서비스 인스턴스의 수명 주기와 연결된 ID가 Azure AD에 만들어집니다. 따라서 리소스가 삭제되면 Azure에서 자동으로 ID를 삭제합니다. 의도적으로 해당 Azure 리소스만 이 ID를 사용하여 Azure AD에서 토큰을 요청할 수 있습니다.
- **사용자 할당** 관리 ID를 독립 실행형 Azure 리소스로 만들 수도 있습니다. [사용자가 할당한 관리 ID를 만들고](how-to-manage-ua-identity-portal.md), 이를 하나 이상의 Azure 서비스 인스턴스에 할당할 수 있습니다. 사용자가 할당한 관리 ID는 이를 사용하는 리소스와 별도로 관리됩니다. </br></br>


아래 표에서는 두 가지 유형의 관리 ID 간의 차이점을 보여 줍니다.

|  속성    | 시스템 할당 관리 ID | 사용자 할당 관리 ID |
|------|----------------------------------|--------------------------------|
| 만들기 |  Azure 리소스의 일부로 생성됨(예: Azure 가상 머신 또는 Azure App Service) | 독립 실행형 Azure 리소스로 생성됨 |
| 수명 주기 | 관리 ID를 만드는 데 사용된 Azure 리소스와 공유되는 수명 주기입니다. <br/> 부모 리소스를 삭제하면 관리 ID도 삭제됩니다. | 독립적인 수명 주기. <br/> 명시적으로 삭제되어야 합니다. |
| Azure 리소스 전체에서 공유 | 공유할 수 없습니다. <br/> 단일 Azure 리소스하고만 연결할 수 있습니다. | 공유할 수 있습니다. <br/> 동일한 사용자 할당 관리 ID를 둘 이상의 Azure 리소스와 연결할 수 있습니다. |
| 일반 사용 예 | 단일 Azure 리소스에 포함된 워크로드 <br/> 독립적인 ID가 필요한 워크로드 <br/> 예를 들어 단일 가상 머신에서 실행되는 애플리케이션 | 여러 리소스에서 실행되며 단일 ID를 공유할 수 있는 워크로드입니다. <br/> 프로비전 흐름에 보안 리소스 사전 승인이 필요한 워크로드입니다. <br/> 리소스가 자주 재활용되지만 권한이 알관적으로 유지되어야 하는 워크로드입니다. <br/> 예를 들어 여러 가상 머신이 동일한 리소스에 액세스해야 하는 워크로드가 있습니다. |

> [!IMPORTANT]
> 선택한 ID 유형에 관계없이 관리 ID는 Azure 리소스에만 사용할 수 있는 특수 유형의 서비스 주체입니다. 관리 ID가 삭제되면 해당하는 서비스 주체가 자동으로 제거됩니다.

## <a name="how-can-i-use-managed-identities-for-azure-resources"></a>Azure 리소스에 대한 관리 ID를 사용하는 방법

![개발자가 인증 정보를 관리하지 않고 코드에서 리소스에 액세스하기 위해 관리 ID를 사용할 수 있는 방법의 몇 가지 예](media/overview/when-use-managed-identities.png)

## <a name="what-azure-services-support-the-feature"></a>Azure 서비스에서 어떤 기능을 지원하나요?<a name="which-azure-services-support-managed-identity"></a>

Azure 리소스에 대한 관리 ID는 Azure AD 인증을 지원하는 서비스를 인증하는 데 사용할 수 있습니다. Azure 리소스에 대한 관리 ID 기능을 지원하는 Azure 서비스 목록은 [Azure 리소스에 대한 관리 ID를 지원하는 서비스](./services-support-managed-identities.md)를 참조하세요.

## <a name="which-operations-can-i-perform-using-managed-identities"></a>관리 ID를 사용하여 수행할 수 있는 작업은 무엇인가요?

시스템 할당 관리 ID를 지원하는 리소스를 통해 다음 작업을 수행할 수 있습니다.

- 리소스 수준에서 관리 ID를 사용하거나 사용하지 않도록 설정합니다.
- RBAC 역할을 사용하여 [권한을 부여](howto-assign-access-portal.md)합니다.
- [Azure 활동 로그](../../azure-resource-manager/management/view-activity-logs.md)에서 CRUD(만들기, 읽기, 업데이트, 삭제) 작업을 봅니다.
- Azure AD [로그인 로그](../reports-monitoring/concept-sign-ins.md)에서 로그인 활동을 봅니다.

대신 사용자 할당 관리 ID를 선택하는 경우:

- ID를 [만들고, 읽고, 업데이트하고, 삭제](how-to-manage-ua-identity-portal.md)할 수 있습니다.
- RBAC 역할 할당을 사용하여 [권한을 부여](howto-assign-access-portal.md)할 수 있습니다.
- 사용자 할당 관리 ID는 둘 이상의 리소스에서 사용할 수 있습니다.
- CRUD 작업은 [Azure 활동 로그](../../azure-resource-manager/management/view-activity-logs.md)에서 검토할 수 있습니다.
- Azure AD [로그인 로그](../reports-monitoring/concept-sign-ins.md)에서 로그인 활동을 봅니다.

ARM(Azure Resource Manager) 템플릿, Azure Portal, Azure CLI, PowerShell 및 REST API를 사용하여 관리 ID에 대한 작업을 수행할 수 있습니다.

## <a name="next-steps"></a>다음 단계

* [Windows VM 시스템 할당 관리 ID를 사용하여 Resource Manager에 액세스](tutorial-windows-vm-access-arm.md)
* [Linux VM 시스템 할당 관리 ID를 사용하여 Resource Manager에 액세스](tutorial-linux-vm-access-arm.md)
* [App Service 및 Azure Functions에 대한 관리 ID를 사용하는 방법](../../app-service/overview-managed-identity.md)
* [Azure Container Instances에서 관리 ID를 사용하는 방법](../../container-instances/container-instances-managed-identity.md)
* [Microsoft Azure 리소스에 대한 관리 ID 구현](https://www.pluralsight.com/courses/microsoft-azure-resources-managed-identities-implementing)
