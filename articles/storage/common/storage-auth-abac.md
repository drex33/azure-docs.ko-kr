---
title: Azure 역할 할당 조건(미리 보기)을 사용하여 Blob에 대한 액세스 권한 부여
titleSuffix: Azure Storage
description: Azure 역할 할당 조건 및 Azure 특성 기반 액세스 제어(Azure ABAC)를 사용하여 Azure Blob에 대한 액세스 권한을 부여합니다. 스토리지 특성을 사용하여 역할 할당에 대한 조건을 정의합니다.
services: storage
author: santoshc
ms.service: storage
ms.topic: conceptual
ms.date: 10/14/2021
ms.author: santoshc
ms.reviewer: jiacfan
ms.subservice: common
ms.openlocfilehash: 0aeb8766822bbfe19dfdd70ed242e7176dd44117
ms.sourcegitcommit: 4abfec23f50a164ab4dd9db446eb778b61e22578
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2021
ms.locfileid: "130062786"
---
# <a name="authorize-access-to-blobs-using-azure-role-assignment-conditions-preview"></a>Azure 역할 할당 조건(미리 보기)을 사용하여 Blob에 대한 액세스 권한 부여

ABAC(특성 기반 액세스 제어)는 보안 주체, 리소스, 요청 및 환경과 관련된 특성을 기반으로 액세스 수준을 정의하는 권한 부여 전략입니다. Azure ABAC는 기존 IAM(ID 및 액세스 관리) 시스템에 [Azure 역할 할당에 조건](../../role-based-access-control/conditions-overview.md)을 추가하여 Azure RBAC(Azure 역할 기반 액세스 제어)를 기반으로 합니다. 이 미리 보기에는 Blob 및 Data Lake Storage Gen2에 대한 역할 할당 조건이 지원됩니다. 리소스 및 요청 특성을 기반으로 역할 할당 조건을 작성할 수 있습니다.

> [!IMPORTANT]
> Azure ABAC 및 Azure 역할 할당 조건은 현재 미리 보기 상태입니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.
> 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

## <a name="overview-of-conditions-in-azure-storage"></a>Azure 스토리지 조건 개요

Azure Storage 사용하면 Azure [AD(Azure Active Directory)를 사용하여](authorize-data-access.md) Blob, 큐 및 테이블 리소스에 대한 요청에 권한을 부여할 수 있습니다. Azure AD는 Azure RBAC를 사용하여 보안 리소스에 대한 액세스 권한을 부여합니다. Azure 스토리지는 Blob 및 큐 데이터에 액세스하는 데 사용되는 공통 권한 집합을 포함하는 Azure [기본 제공 역할](../../role-based-access-control/built-in-roles.md#storage) 집합을 정의합니다. 선택 권한 집합을 사용하여 사용자 지정 역할을 정의할 수도 있습니다. Azure 스토리지는 스토리지 계정 또는 Blob 컨테이너에 대한 역할 할당을 지원합니다.

그러나 경우에 따라 리소스에 대한 보다 세분화된 액세스를 사용하도록 설정하거나 스토리지 리소스에 대한 수백 개의 역할 할당을 간소화해야 할 수 있습니다. 이러한 목표를 달성하기 위해 [DataActions](../../role-based-access-control/role-definitions.md#dataactions)에 대한 [역할 할당 조건](../../role-based-access-control/conditions-overview.md)을 구성할 수 있습니다. [사용자 지정 역할](../../role-based-access-control/custom-roles.md)과 함께 조건을 사용하거나 기본 제공 역할을 선택할 수 있습니다. [스토리지 리소스 공급자](/rest/api/storagerp)를 통한 관리 [작업](../../role-based-access-control/role-definitions.md#actions)에는 조건이 지원되지 않습니다.

Azure 스토리지의 조건은 Blob에 대해 지원됩니다. [HNS(계층 구조 네임스페이스)](../blobs/data-lake-storage-namespace.md) 기능이 사용되도록 설정된 계정에 조건을 사용할 수 있습니다. Azure Storage 큐, 테이블 또는 파일 리소스에 대한 조건은 현재 지원되지 않습니다.

## <a name="supported-attributes-and-operations"></a>지원되는 특성 및 작업

이 미리 보기에서는 기본 제공 역할 또는 사용자 지정 역할에 조건을 추가할 수 있습니다. 사용자 지정 역할을 사용하면 사용자에게 필수 사용 권한 또는 데이터 작업만 부여할 수 있습니다. 이 미리 보기에서 지원되는 기본 제공 역할에는 [스토리지 Blob 데이터 읽기 권한자](../../role-based-access-control/built-in-roles.md#storage-blob-data-reader), [스토리지 Blob 데이터 기여자](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor) 및 [스토리지 Blob 데이터 소유자](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner)가 있습니다.

[Blob 인덱스 태그](../blobs/storage-manage-find-blobs.md)를 기반으로 한 조건으로 작업하는 경우 태그 작업에 대한 권한이 이 역할에 포함되므로 *스토리지 Blob 데이터 소유자* 를 사용해야 합니다.

> [!NOTE]
> Blob 인덱스 태그는 계층 구조 네임스페이스를 사용하는 Data Lake Storage Gen2 스토리지 계정에 지원되지 않습니다. HNS를 사용하도록 설정된 스토리지 계정에서 인덱스 태그를 사용하여 역할 할당 조건을 작성해서는 안됩니다.

[Azure 역할 할당 조건 형식](../../role-based-access-control/conditions-format.md)을 사용하면 조건에서 `@Resource` 또는 `@Request` 특성을 사용할 수 있습니다. `@Resource` 특성은 스토리지 계정, 컨테이너 또는 Blob과 같이 액세스 중인 스토리지 리소스의 기존 특성을 나타냅니다. `@Request` 특성은 스토리지 작업 요청에 포함된 특성을 나타냅니다.

각 DataAction에 지원되는 전체 특성 목록은 [Azure 스토리지의 Azure 역할 할당 조건에 대한 작업 및 특성(미리 보기)](storage-auth-abac-attributes.md)을 참조하세요.

## <a name="see-also"></a>참고 항목

- [Azure 스토리지의 Azure 역할 할당 조건에 대한 보안 고려 사항(미리 보기)](storage-auth-abac-security.md)
- [Azure Storage에서 Azure 역할 할당 조건에 대한 작업 및 특성(미리 보기)](storage-auth-abac-attributes.md)
- [Azure ABAC(Azure 특성 기반 액세스 제어)란 무엇인가요?(미리 보기)](../../role-based-access-control/conditions-overview.md)