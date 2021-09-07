---
title: Microsoft Azure Maps에서 사용자에게 역할 기반 액세스 권한 부여
titleSuffix: Azure Maps
description: 역할 기반 액세스 제어를 사용하여 사용자에게 Azure Maps에 대한 권한 부여
author: anastasia-ms
ms.author: v-stharr
ms.date: 06/21/2021
ms.topic: include
ms.service: azure-maps
services: azure-maps
custom.ms: subject-rbac-steps
ms.openlocfilehash: 22b3eaaff0989718a781b29549b4fab102673273
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "121802261"
---
## <a name="grant-role-based-access-for-users-to-azure-maps"></a>사용자에게 Azure Maps에 대한 역할 기반 액세스 권한 부여

하나 이상의 Azure Maps 역할 정의에 Azure AD 그룹 또는 보안 주체를 할당하여 *Azure RBAC(Azure 역할 기반 액세스 제어)* 를 부여할 수 있습니다.

Azure Maps에 대한 사용 가능한 Azure 역할 정의를 보려면 [기본 제공 Azure Maps 역할 정의 보기](../how-to-manage-authentication.md#view-built-in-azure-maps-role-definitions)를 참조하세요.

만든 관리 ID 또는 서비스 주체에 사용 가능한 Azure Maps 역할을 할당하는 방법에 대한 자세한 단계는 [Azure Portal을 사용하여 Azure 역할 할당](../../role-based-access-control/role-assignments-portal.md)을 참조하세요.

Azure Maps 앱 및 많은 수의 사용자에 대한 리소스 액세스를 효율적으로 관리하려면 [Azure AD 그룹](../../active-directory/fundamentals/active-directory-manage-groups.md)을 참조하세요.

>[!IMPORTANT]
>사용자가 애플리케이션에 대한 인증을 받을 수 있게 하려면 먼저 Azure AD에서 사용자를 만들어야 합니다. 자세한 내용은 [Azure AD를 사용하여 사용자 추가 또는 삭제](../../active-directory/fundamentals/add-users-azure-active-directory.md)를 참조하세요.

사용자를 위해 큰 디렉터리를 효과적으로 관리하는 방법에 대한 자세한 내용은 [Azure AD](../../active-directory/fundamentals/index.yml)를 참조하세요.

> [!WARNING]
> Azure Maps 기본 제공 역할 정의는 많은 Azure Maps REST API에 강력한 액세스 권한을 부여합니다. API 액세스를 최소한으로 제한하려면 [사용자 지정 역할 정의 만들기를 참조하고 시스템 할당 ID](../../role-based-access-control/custom-roles.md)를 사용자 지정 역할 정의에 할당합니다. 이렇게 하면 애플리케이션에서 Azure Maps에 액세스하는 데 필요한 최소 권한을 사용할 수 있습니다.
