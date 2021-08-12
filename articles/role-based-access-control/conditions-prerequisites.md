---
title: Azure 역할 할당 조건(미리 보기)에 대한 필수 구성 요소
description: Azure 역할 할당 조건(미리 보기)에 대한 필수 구성 요소
services: active-directory
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.subservice: conditions
ms.topic: conceptual
ms.workload: identity
ms.date: 05/06/2021
ms.author: rolyon
ms.openlocfilehash: e0da284f7ce9ac1c8699a6f22f2144ff6f3f8c80
ms.sourcegitcommit: 1fbd591a67e6422edb6de8fc901ac7063172f49e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/07/2021
ms.locfileid: "109489802"
---
# <a name="prerequisites-for-azure-role-assignment-conditions-preview"></a>Azure 역할 할당 조건(미리 보기)에 대한 필수 구성 요소

> [!IMPORTANT]
> Azure ABAC 및 Azure 역할 할당 조건은 현재 미리 보기 상태입니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.
> 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

Azure 역할 할당 조건을 추가하거나 편집하려면 다음 필수 구성 요소가 있어야 합니다.

## <a name="storage-accounts"></a>Storage 계정

Blob 인덱스 태그를 사용하는 조건의 경우 Blob 인덱스 기능과 호환되는 스토리지 계정을 사용해야 합니다. 예를 들어 HNS(계층 구조 네임스페이스)가 비활성화된 GPv2(범용 v2) 스토리지 계정만 현재 지원됩니다. 자세한 내용은 [Blob 인덱스 태그를 사용하여 Azure Blob 데이터 관리 및 찾기(미리 보기)](../storage/blobs/storage-manage-find-blobs.md#regional-availability-and-storage-account-support)를 참조하세요.

## <a name="azure-powershell"></a>Azure PowerShell

Azure PowerShell을 사용하여 조건을 추가하거나 업데이트하는 경우 다음 버전을 사용해야 합니다.

- [Az 모듈 5.5.0 이상](https://www.powershellgallery.com/packages/Az/5.5.0)
- [Az.Resources 모듈 3.2.1 이상](https://www.powershellgallery.com/packages/Az.Resources/3.2.1)
    - Az 모듈 v5.5.0 이상에 포함되어 있지만 PowerShell 갤러리를 통해 수동으로 설치할 수 있습니다.
- [Az.Storage 미리 보기 모듈 2.5.2-preview 이상](https://www.powershellgallery.com/packages/Az.Storage/2.5.2-preview)

## <a name="azure-cli"></a>Azure CLI

Azure CLI를 사용하여 조건을 추가하거나 업데이트하는 경우 다음 버전을 사용해야 합니다.

- [Azure CLI 2.18 이상](/cli/azure/install-azure-cli)

## <a name="permissions"></a>사용 권한

역할 할당과 마찬가지로 조건을 추가하거나 업데이트하려면 [사용자 액세스 관리자](built-in-roles.md#user-access-administrator) 또는 [소유자](built-in-roles.md#owner)와 같은 `Microsoft.Authorization/roleAssignments/write` 및 `Microsoft.Authorization/roleAssignments/delete` 권한이 있는 사용자로 Azure에 로그인해야 합니다.

## <a name="next-steps"></a>다음 단계

- [Azure 역할 할당 조건 예제(미리 보기)](../storage/common/storage-auth-abac-examples.md)
- [자습서: Azure Portal을 사용하여 Blob에 대한 액세스를 제한하는 역할 할당 조건 추가(미리 보기)](../storage/common/storage-auth-abac-portal.md)
