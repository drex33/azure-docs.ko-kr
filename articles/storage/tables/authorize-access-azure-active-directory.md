---
title: Active Directory를 사용하여 테이블에 대한 액세스 권한 부여(미리 보기)
titleSuffix: Azure Storage
description: Azure AD(Azure Active Directory)를 사용하여 Azure 테이블에 대한 액세스 권한을 부여합니다. 액세스 권한에 대한 Azure 역할을 할당합니다. Azure AD 계정으로 데이터에 액세스합니다.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 07/13/2021
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: 71ce588ea617a0322da4ffc7cf9f2626e4abb609
ms.sourcegitcommit: ee8ce2c752d45968a822acc0866ff8111d0d4c7f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/14/2021
ms.locfileid: "113734219"
---
# <a name="authorize-access-to-tables-using-azure-active-directory-preview"></a>Azure Active Directory를 사용하여 테이블에 대한 액세스 권한 부여

Azure Storage는 Azure AD(Azure Active Directory)를 사용하여 테이블 데이터에 대한 요청을 승인하도록 지원합니다. Azure AD를 사용하면 Azure RBAC(Azure 역할 기반 액세스 제어)를 사용하여 사용자, 그룹 또는 애플리케이션 서비스 주체일 수 있는 보안 주체에 권한을 부여할 수 있습니다. 보안 주체는 Azure AD에 의해 인증되어 OAuth 2.0 토큰을 반환합니다. 그러면 토큰을 사용하여 테이블 서비스에 대한 요청에 권한을 부여할 수 있습니다.

Azure AD를 사용하여 Azure Storage에 대한 요청에 권한을 부여하면 공유 키 권한 부여보다 뛰어난 보안과 사용 편의성이 구현됩니다. 가능한 경우 테이블 애플리케이션과 함께 Azure AD 권한 부여를 사용하여 최소한의 필수 권한으로 액세스를 보장하는 것이 좋습니다.

Azure AD 권한 부여는 모든 공용 지역 및 국가별 클라우드에서 범용으로 모두 사용할 수 있습니다. Azure Resource Manager 배포 모델로 만든 스토리지 계정만 Azure AD 권한 부여를 지원합니다.

> [!IMPORTANT]
> 테이블에 대한 Azure AD 권한 부여는 현재 **미리 보기** 상태입니다. 베타, 미리 보기로 제공되거나 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 약관은 [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

## <a name="overview-of-azure-ad-for-tables"></a>테이블에 대한 Azure AD 개요

보안 주체(사용자, 그룹 또는 애플리케이션)가 테이블 리소스에 액세스하려고 시도하는 경우 요청에 권한이 부여되어 있어야 합니다. Azure AD의 경우 리소스에 대한 액세스는 2단계 프로세스입니다. 먼저, 보안 주체의 ID가 인증되고 OAuth 2.0 토큰이 반환됩니다. 그런 다음, 토큰이 테이블 서비스에 대한 요청의 일부로 전달되고 서비스가 지정된 리소스에 대한 액세스 권한을 부여하는 데 사용됩니다.

인증 단계를 수행하려면 런타임 시 애플리케이션 요청에 OAuth 2.0 액세스 토큰이 있어야 합니다. 애플리케이션이 Azure VM, 가상 머신 확장 집합 또는 Azure Functions 앱 같은 Azure 엔터티 내에서 실행되는 경우 [관리 ID](../../active-directory/managed-identities-azure-resources/overview.md)를 사용하여 테이블에 액세스할 수 있습니다. 관리 ID에서 수행한 요청에 권한을 부여하는 방법을 알아보려면 [Azure 리소스에 대한 Azure Active Directory 및 관리 ID를 사용하여 테이블에 대한 액세스 권한 부여](../common/storage-auth-aad-msi.md)를 참조하세요.

권한 부여 단계에서는 보안 주체에 하나 이상의 Azure 역할을 할당해야 합니다. Azure Storage는 테이블 데이터에 대한 일반적인 권한 집합을 포함하는 Azure 역할을 제공합니다. 보안 주체에 할당된 역할에 따라 보안 주체가 보유하는 권한이 결정됩니다. 테이블 액세스 시 Azure 역할을 할당하는 방법에 대한 자세한 내용은 [테이블 데이터에 액세스하기 위한 Azure 역할 할당](assign-azure-role-data-access.md)을 참조하세요.

Azure Table 서비스에 대한 요청을 수행하는 네이티브 애플리케이션과 웹 애플리케이션은 Azure AD를 사용하여 액세스에 권한을 부여할 수도 있습니다. 액세스 토큰을 요청하고 이를 사용하여 요청에 권한을 부여하는 방법을 알아보려면 [Azure Storage 애플리케이션에서 Azure AD를 사용하여 Azure Storage에 대한 액세스 권한 부여](../common/storage-auth-aad-app.md)를 참조하세요.

## <a name="assign-azure-roles-for-access-rights"></a>액세스 권한에 대한 Azure 역할 할당

Azure AD(Azure Active Directory)는 [Azure RBAC](../../role-based-access-control/overview.md)(Azure 역할 기반 액세스 제어)를 통해 보안 리소스에 대한 액세스 권한을 부여합니다. Azure Storage는 테이블 데이터에 액세스하는 데 사용되는 일반 권한 집합을 포함하는 Azure 기본 제공 역할 집합을 정의합니다. 테이블 데이터에 액세스하기 위한 사용자 지정 역할도 정의할 수 있습니다.

Azure AD 보안 주체에 Azure 역할을 할당하는 경우 Azure는 해당 보안 주체의 해당 리소스에 대한 액세스 권한을 부여합니다. Azure AD 보안 주체는 사용자, 그룹, 애플리케이션 서비스 사용자 또는 [Azure 리소스의 관리 ID](../../active-directory/managed-identities-azure-resources/overview.md)일 수 있습니다.

### <a name="resource-scope"></a>리소스 범위

Azure RBAC 역할을 보안 주체에 할당하기 전에 보안 주체에게 부여해야 하는 액세스 범위를 결정합니다. 모범 사례에 따르면 항상 가능한 가장 좁은 범위만 부여하는 것이 가장 좋습니다. 더 광범위한 범위에서 정의된 Azure RBAC 역할은 그 아래에 있는 리소스에 상속됩니다.

가장 좁은 범위로 시작하여 다음 수준에서 Azure 테이블 리소스에 대한 액세스 범위를 지정할 수 있습니다.

- **개별 테이블.** 이 범위의 역할 할당은 지정된 테이블에 적용됩니다.
- **스토리지 계정.** 이 범위의 역할 할당은 계정에 있는 모든 테이블에 적용됩니다.
- **리소스 그룹.** 이 범위의 역할 할당은 리소스 그룹의 전체 스토리지 계정에 있는 모든 테이블에 적용됩니다.
- **구독.** 이 범위의 역할 할당은 구독의 전체 리소스 그룹에 있는 모든 스토리지 계정의 테이블에 모두 적용됩니다.
- **관리 그룹.** 이 범위의 역할 할당은 관리 그룹의 전체 구독에 있는 모든 리소스 그룹의 전체 스토리지 계정에 있는 테이블에 모두 적용됩니다.

Azure RBAC 역할 할당 범위에 대한 자세한 내용은 [Azure RBAC 범위 이해](../../role-based-access-control/scope-overview.md)를 참조하세요.

### <a name="azure-built-in-roles-for-tables"></a>테이블에 대한 Azure 기본 제공 역할

Azure RBAC는 기본 제공 역할을 부여하여 Azure AD 및 OAuth를 사용해 테이블 데이터에 대한 액세스 권한을 부여할 수 있습니다. Azure Storage에서 테이블에 대한 권한을 제공하는 기본 제공 역할은 다음과 같습니다.

- [스토리지 테이블 데이터 참가자](../../role-based-access-control/built-in-roles.md#storage-table-data-contributor): Table Storage 리소스에 대한 읽기/쓰기/삭제 권한을 부여하는 데 사용합니다.
- [스토리지 테이블 데이터 읽기 권한자](../../role-based-access-control/built-in-roles.md#storage-table-data-reader): Table Storage 리소스에 읽기 전용 권한을 부여하는 데 사용합니다.

보안 주체에 Azure 기본 제공 역할을 할당하는 방법을 알아보려면 [테이블 데이터에 액세스하기 위한 Azure 역할 할당](assign-azure-role-data-access.md)을 참조하세요. Azure RBAC 역할 및 해당 권한을 나열하는 방법을 알아보려면 [Azure 역할 정의 나열](../../role-based-access-control/role-definitions-list.md)을 참조하세요.

기본 제공 역할을 Azure Storage에 정의하는 방법에 대한 자세한 내용은 [역할 정의 이해](../../role-based-access-control/role-definitions.md#management-and-data-operations)를 참조하세요. Azure 사용자 지정 역할 만들기에 대한 자세한 내용은 [Azure 사용자 지정 역할](../../role-based-access-control/custom-roles.md)을 참조하세요.

데이터 액세스에 대해 명시적으로 정의된 역할만 보안 주체가 테이블 데이터에 액세스하도록 허용합니다. **소유자**, **참가자** 및 **스토리지 계정 참가자** 같은 기본 제공 역할은 보안 주체가 스토리지 계정을 관리하도록 허용하지만, Azure AD를 통해 해당 계정 내의 테이블 데이터에 액세스할 수 있는 권한은 제공하지 않습니다. 그러나 역할에 **Microsoft.Storage/storageAccounts/listKeys/action** 이 포함되어 있는 경우에는 해당 역할이 할당된 사용자가 계정 액세스 키를 사용하여 공유 키 인증을 통해 스토리지 계정의 데이터에 액세스할 수 있습니다.

데이터 서비스 및 관리 서비스에 대한 Azure Storage의 Azure 기본 제공 역할에 대한 자세한 내용은 [Azure RBAC에 대한 Azure 기본 제공 역할](../../role-based-access-control/built-in-roles.md#storage)의 **스토리지** 섹션을 참조하세요. 또한 Azure에서 권한을 제공하는 여러 유형의 역할에 대한 자세한 내용은 [클래식 구독 관리자 역할, Azure 역할 및 Azure AD 역할](../../role-based-access-control/rbac-and-directory-admin-roles.md)을 참조하세요.

> [!IMPORTANT]
> Azure 역할 할당이 전파되는 데 최대 30분이 걸릴 수 있습니다.

### <a name="access-permissions-for-data-operations"></a>데이터 작업에 대한 액세스 권한

특정 테이블 서비스 작업을 호출하는 데 필요한 권한에 대한 자세한 내용은 [데이터 작업을 호출하기 위한 권한](/rest/api/storageservices/authorize-with-azure-active-directory#permissions-for-calling-data-operations)을 참조하세요.

## <a name="next-steps"></a>다음 단계

- [Azure Storage의 데이터에 대한 액세스 권한 부여](../common/authorize-data-access.md)
- [테이블 데이터에 액세스하기 위한 Azure 역할 할당](assign-azure-role-data-access.md)