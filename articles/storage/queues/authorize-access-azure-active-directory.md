---
title: Active Directory를 사용하여 대기열에 대한 액세스 권한 부여
titleSuffix: Azure Storage
description: Azure AD(Azure Active Directory)를 사용하여 Azure 대기열에 대한 액세스 권한을 부여합니다. 액세스 권한에 대한 Azure 역할을 할당합니다. Azure AD 계정으로 데이터에 액세스합니다.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 07/13/2021
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: 410d5daf67d590d84801a23627b5ce46e26367f5
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/01/2021
ms.locfileid: "129357572"
---
# <a name="authorize-access-to-queues-using-azure-active-directory"></a>Azure Active Directory를 사용하여 큐에 대한 액세스 권한 부여

Azure Storage는 Azure AD(Azure Active Directory)를 사용하여 데이터를 큐에 넣는 요청에 권한을 부여하는 것을 지원합니다. Azure AD를 사용하면 Azure RBAC(Azure 역할 기반 액세스 제어)를 사용하여 사용자, 그룹 또는 애플리케이션 서비스 주체일 수 있는 보안 주체에 권한을 부여할 수 있습니다. 보안 주체는 Azure AD에 의해 인증되어 OAuth 2.0 토큰을 반환합니다. 그런 다음 토큰을 사용하여 큐 서비스에 대한 요청을 승인할 수 있습니다.

Azure AD를 사용하여 Azure Storage에 대한 요청에 권한을 부여하면 공유 키 권한 부여보다 뛰어난 보안과 사용 편의성이 구현됩니다. Microsoft는 최소 필수 권한으로 액세스를 보장하기 위해 가능한 경우 대기열 애플리케이션과 함께 Azure AD 권한 부여를 사용할 것을 권장합니다.

Azure AD를 통한 권한 부여는 모든 공용 지역 및 국가 클라우드의 모든 범용 스토리지 계정에 사용할 수 있습니다. Azure Resource Manager 배포 모델로 만든 스토리지 계정만 Azure AD 권한 부여를 지원합니다.

## <a name="overview-of-azure-ad-for-queues"></a>큐용 Azure AD 개요

보안 주체(사용자, 그룹 또는 응용 프로그램)가 대기열 리소스에 액세스하려고 하면 요청이 승인되어야 합니다. Azure AD의 경우 리소스에 대한 액세스는 2단계 프로세스입니다. 먼저, 보안 주체의 ID가 인증되고 OAuth 2.0 토큰이 반환됩니다. 다음으로 토큰은 큐 서비스에 대한 요청의 일부로 전달되고 서비스에서 지정된 리소스에 대한 액세스 권한을 부여하는 데 사용됩니다.

인증 단계를 수행하려면 런타임 시 애플리케이션 요청에 OAuth 2.0 액세스 토큰이 있어야 합니다. 애플리케이션이 Azure VM, 가상 머신 확장 집합 또는 Azure Functions 앱과 같은 Azure 엔터티 내에서 실행되는 경우 [관리 ID](../../active-directory/managed-identities-azure-resources/overview.md)를 사용하여 큐에 액세스할 수 있습니다. 관리 ID의 요청을 승인하는 방법을 알아보려면 [Azure 리소스에 대한 Azure Active Directory 및 관리 ID를 사용하여 대기열에 대한 액세스 승인](../common/storage-auth-aad-msi.md)을 참조하세요.

권한 부여 단계에서는 보안 주체에 하나 이상의 Azure 역할을 할당해야 합니다. Azure Storage는 큐 데이터에 대한 공통 권한 집합을 포함하는 Azure 역할을 제공합니다. 보안 주체에 할당된 역할에 따라 해당 주체가 갖게 될 권한이 결정됩니다. 대기열 액세스를 위한 Azure 역할 할당에 대한 자세한 내용은 [대기열 데이터에 대한 액세스를 위한 Azure 역할 할당](../queues/assign-azure-role-data-access.md)을 참조하세요.

Azure 큐 서비스에 요청하는 기본 애플리케이션 및 웹 애플리케이션은 Azure AD를 사용하여 액세스 권한을 부여할 수도 있습니다. 액세스 토큰을 요청하고 이를 사용하여 요청을 승인하는 방법을 알아보려면 [Azure Storage 애플리케이션에서 Azure AD를 사용하여 Azure Storage에 대한 액세스 권한 부여](../common/storage-auth-aad-app.md)를 참조하세요.

## <a name="assign-azure-roles-for-access-rights"></a>액세스 권한에 대한 Azure 역할 할당

Azure AD(Azure Active Directory)는 [Azure RBAC](../../role-based-access-control/overview.md)(Azure 역할 기반 액세스 제어)를 통해 보안 리소스에 대한 액세스 권한을 부여합니다. Azure Storage는 큐 데이터에 액세스하는 데 사용되는 공통 권한 집합을 포함하는 Azure 기본 제공 역할 집합을 정의합니다. 대기열 데이터에 액세스하기 위한 사용자 지정 역할을 정의할 수도 있습니다.

Azure AD 보안 주체에 Azure 역할을 할당하는 경우 Azure는 해당 보안 주체의 해당 리소스에 대한 액세스 권한을 부여합니다. Azure AD 보안 주체는 사용자, 그룹, 애플리케이션 서비스 사용자 또는 [Azure 리소스의 관리 ID](../../active-directory/managed-identities-azure-resources/overview.md)일 수 있습니다.

### <a name="resource-scope"></a>리소스 범위

Azure RBAC 역할을 보안 주체에 할당하기 전에 보안 주체에게 부여해야 하는 액세스 범위를 결정합니다. 모범 사례에 따르면 항상 가능한 가장 좁은 범위만 부여하는 것이 가장 좋습니다. 더 광범위한 범위에서 정의된 Azure RBAC 역할은 그 아래에 있는 리소스에 상속됩니다.

가장 좁은 범위에서 시작 하 여 다음 수준에서 Azure queue 리소스에 대 한 액세스 범위를 지정할 수 있습니다.

- **개별 큐.** 이 범위의 역할 할당은 큐의 메시지와 큐 속성 및 메타데이터에 적용됩니다.
- **스토리지 계정.** 이 범위의 역할 할당은 모든 큐와 해당 메시지에 적용됩니다.
- **리소스 그룹.** 이 범위의 역할 할당은 리소스 그룹의 모든 스토리지 계정에 있는 모든 큐에 적용됩니다.
- **구독.** 이 범위의 역할 할당은 구독의 모든 리소스 그룹에 있는 모든 스토리지 계정의 모든 큐에 적용됩니다.
- **관리 그룹.** 이 범위의 역할 할당은 관리 그룹의 모든 구독에 있는 모든 리소스 그룹의 모든 스토리지 계정에 있는 모든 큐에 적용됩니다.

Azure RBAC 역할 할당 범위에 대한 자세한 내용은 [Azure RBAC 범위 이해](../../role-based-access-control/scope-overview.md)를 참조하세요.

### <a name="azure-built-in-roles-for-queues"></a>큐에 대한 Azure 기본 제공 역할

Azure RBAC는 Azure AD 및 OAuth를 사용하여 큐 데이터에 대한 액세스 권한을 부여할 수 있도록 여러 Azure 기본 역할을 제공합니다. Azure Storage의 데이터 리소스에 대한 권한을 제공하는 역할의 몇 가지 예는 다음과 같습니다.

- [Storage 큐 데이터 기여자](../../role-based-access-control/built-in-roles.md#storage-queue-data-contributor): Azure 큐에 대한 읽기/쓰기/삭제 권한을 부여하는 데 사용합니다.
- [Storage 큐 데이터 읽기 권한자](../../role-based-access-control/built-in-roles.md#storage-queue-data-reader): Azure 큐에 대한 읽기 전용 권한을 부여하는 데 사용합니다.
- [Storage 큐 데이터 메시지 처리자](../../role-based-access-control/built-in-roles.md#storage-queue-data-message-processor): Azure Storage 큐의 메시지에 대한 선택, 검색 및 삭제 권한을 부여하는 데 사용됩니다.
- [Storage 큐 데이터 메시지 보내는 사람](../../role-based-access-control/built-in-roles.md#storage-queue-data-message-sender): Azure Storage 큐의 메시지에 대한 추가 권한을 부여하는 데 사용됩니다.

Azure 기본 제공 역할을 보안 주체에 할당하는 방법을 알아보려면 [대기열 데이터에 액세스하기 위한 Azure 역할 할당](../queues/assign-azure-role-data-access.md)을 참조하세요. Azure RBAC 역할 및 해당 권한을 나열하는 방법을 알아보려면 [Azure 역할 정의 나열](../../role-based-access-control/role-definitions-list.md)을 참조하세요.

기본 제공 역할을 Azure Storage에 정의하는 방법에 대한 자세한 내용은 [역할 정의 이해](../../role-based-access-control/role-definitions.md#control-and-data-actions)를 참조하세요. Azure 사용자 지정 역할 만들기에 대한 자세한 내용은 [Azure 사용자 지정 역할](../../role-based-access-control/custom-roles.md)을 참조하세요.

데이터 액세스에 대해 명시적으로 정의된 역할만 보안 주체가 큐 데이터에 액세스할 수 있도록 허용합니다. **소유자**, **기여자** 및 **스토리지 계정 기여자** 같은 기본 제공 역할은 보안 주체가 스토리지 계정을 관리하는 것을 허용하지만, Azure AD를 통해 해당 계정 내의 큐 데이터에 액세스할 수 있는 권한은 제공하지 않습니다. 그러나 역할에 **Microsoft.Storage/storageAccounts/listKeys/action** 이 포함되어 있는 경우에는 해당 역할이 할당된 사용자가 계정 액세스 키를 사용하여 공유 키 인증을 통해 스토리지 계정의 데이터에 액세스할 수 있습니다. 자세한 내용은 [Azure Portal에서 Blob 데이터에 대한 액세스 권한을 부여하는 방법 선택](../../storage/queues/authorize-data-operations-portal.md)을 참조하세요.

데이터 서비스 및 관리 서비스에 대한 Azure Storage의 Azure 기본 제공 역할에 대한 자세한 내용은 [Azure RBAC에 대한 Azure 기본 제공 역할](../../role-based-access-control/built-in-roles.md#storage)의 **스토리지** 섹션을 참조하세요. 또한 Azure에서 권한을 제공하는 여러 유형의 역할에 대한 자세한 내용은 [클래식 구독 관리자 역할, Azure 역할 및 Azure AD 역할](../../role-based-access-control/rbac-and-directory-admin-roles.md)을 참조하세요.

> [!IMPORTANT]
> Azure 역할 할당이 전파되는 데 최대 30분이 걸릴 수 있습니다.

### <a name="access-permissions-for-data-operations"></a>데이터 작업에 대한 액세스 권한

특정 큐 서비스 작업을 호출하는 데 필요한 권한에 대한 자세한 내용은 [데이터 작업 호출 권한](/rest/api/storageservices/authorize-with-azure-active-directory#permissions-for-calling-data-operations)을 참조하세요.

## <a name="access-data-with-an-azure-ad-account"></a>Azure AD 계정으로 데이터에 액세스

Azure Portal, PowerShell 또는 Azure CLI를 통해 큐 데이터에 액세스하려면 사용자의 Azure AD 계정을 사용하거나 계정 액세스 키(공유 키 권한 부여)를 사용하여 권한을 부여할 수 있습니다.

### <a name="data-access-from-the-azure-portal"></a>Azure Portal에서 데이터 액세스

Azure Portal은 Azure AD 계정 또는 계정 액세스 키를 사용하여 Azure Storage 계정의 큐 데이터에 액세스할 수 있습니다. Azure Portal에서 사용하는 권한 부여 체계는 할당된 Azure 역할에 따라 달라집니다.

대기열 데이터에 액세스하려고 하면 Azure Portal은 먼저 **Microsoft.Storage/storageAccounts/listkeys/action** 을 사용하여 Azure 역할이 할당되었는지 확인합니다. 이 작업으로 역할이 할당된 경우 Azure Portal은 공유 키 권한 부여를 통해 큐 데이터에 액세스하기 위해 계정 키를 사용합니다. 이 작업에 역할을 할당하지 않은 경우 Azure Portal은 Azure AD 계정을 사용하여 데이터에 액세스하려고 합니다.

Azure AD 계정을 사용하여 Azure Portal에서 큐 데이터에 액세스하려면 큐 데이터에 액세스할 수 있는 권한이 필요하고 Azure Portal에서 스토리지 계정 리소스를 탐색할 수 있는 권한도 필요합니다. Azure Storage에서 제공하는 기본 제공 역할은 큐 리소스에 대한 액세스 권한을 부여하지만 스토리지 계정 리소스에 대한 권한은 부여하지 않습니다. 이러한 이유로 포털에 액세스하려면 스토리지 계정 수준 이상으로 범위가 지정된 [읽기 권한자](../../role-based-access-control/built-in-roles.md#reader) 역할과 같은 Azure Resource Manager 역할도 할당해야 합니다. **Reader** 역할은 가장 제한된 사용 권한을 부여하지만, 스토리지 계정 관리 리소스에 대한 액세스 권한을 부여하는 다른 Azure Resource Manager 역할도 허용됩니다. Azure AD 계정을 사용하여 Azure Portal에서 사용자에게 데이터 액세스 권한을 할당하는 방법에 대한 자세한 내용은 [대기열 데이터에 대한 액세스를 위한 Azure 역할 할당](../queues/assign-azure-role-data-access.md) 또는 [액세스를 위한 Azure 역할 할당을 참조하세요. 큐 데이터](../queues/assign-azure-role-data-access.md).

Azure Portal은 큐로 이동할 때 사용 중인 권한 부여 체계를 나타냅니다. 포털의 데이터 액세스에 대한 자세한 내용은 [Azure Portal의 큐 데이터에 대한 액세스 권한 부여 방법 선택](../queues/authorize-data-operations-portal.md) 및 [Azure Portal의 큐 데이터 액세스 권한 부여 방법 선택](../queues/authorize-data-operations-portal.md)을 참조하세요.

### <a name="data-access-from-powershell-or-azure-cli"></a>PowerShell 또는 Azure CLI에서 데이터 액세스

이제 Azure CLI 및 PowerShell에서는 Azure AD 자격 증명을 사용하여 로그인하도록 지원합니다. 로그인하면 세션이 해당 자격 증명으로 실행됩니다. 다음 문서에서 자세한 내용을 참조하세요.

- [Azure CLI에서 큐 데이터에 대한 액세스 권한을 부여하는 방법 선택](authorize-data-operations-cli.md)
- [Azure AD 자격 증명으로 PowerShell 명령을 실행하여 큐 데이터에 액세스](authorize-data-operations-powershell.md)

## <a name="next-steps"></a>다음 단계

- [Azure Storage의 데이터에 대한 액세스 권한 부여](../common/authorize-data-access.md)
- [ 데이터에 액세스하기 위한 Azure 역할 할당](assign-azure-role-data-access.md)