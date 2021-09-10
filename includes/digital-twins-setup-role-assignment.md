---
author: baanders
description: Azure Digital Twins 설정의 액세스 권한 단계에 대한 포함 파일
ms.service: digital-twins
ms.topic: include
ms.date: 7/17/2020
ms.author: baanders
ms.openlocfilehash: 8e5c39cbd31757116ec17df502c0958830841b07
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/22/2021
ms.locfileid: "114468702"
---
Azure Digital Twins는 RBAC(역할 기반 액세스 제어)에 [Azure AD(Azure Active Directory)](../articles/active-directory/fundamentals/active-directory-whatis.md)를 사용합니다. 즉, 사용자가 Azure Digital Twins 인스턴스에 대한 데이터 평면 호출을 수행하려면 먼저 해당 사용자에게 적절한 권한이 있는 역할을 할당해야 합니다.

Azure Digital Twins의 경우 이 역할은 **Azure Digital Twins 데이터 소유자** 입니다. [Azure Digital Twins 솔루션을 위한 보안](../articles/digital-twins/concepts-security.md)에서 역할 및 보안에 대해 자세히 알아볼 수 있습니다.

> [!NOTE]
> 이 역할은 Azure Digital Twins 인스턴스의 범위에서도 할당될 수 있는 Azure AD **소유자** 역할과 다릅니다. 이는 두 가지 고유한 관리 역할이며 소유자에게는 **Azure Digital Twins 데이터 소유자** 에게 부여된 데이터 평면 기능에 대한 액세스 권한을 부여하지 않습니다.

이 섹션에서는 Azure 구독의 Azure AD 테넌트에서 해당 사용자의 이메일을 사용하여 Azure Digital Twins 인스턴스의 사용자에 대한 역할 할당을 만드는 방법을 보여줍니다. 조직의 역할에 따라 이 권한을 직접 설정하거나 Azure Digital Twins 인스턴스를 관리할 다른 사용자를 대신하여 설정할 수 있습니다.