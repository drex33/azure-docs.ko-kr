---
title: Azure Portal을 사용하여 Azure Data Lake Storage Gen2에서 ACL 관리
description: Azure Portal로 HNS(계층 구조 네임스페이스)를 사용하도록 설정된 스토리지 계정에서 ACL(액세스 제어 목록)을 관리합니다.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: how-to
ms.date: 04/15/2021
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: bb9404351142e60e242e1e4181961c1ed4609012
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128564981"
---
# <a name="use-the-azure-portal-to-manage-acls-in-azure-data-lake-storage-gen2"></a>Azure Portal을 사용하여 Azure Data Lake Storage Gen2에서 ACL 관리

이 문서에서는 [Azure Portal](https://ms.portal.azure.com/)을 사용하여 계층 구조 네임스페이스가 사용하도록 설정된 스토리지 계정에서 디렉터리 또는 Blob의 ACL(액세스 제어 목록)을 관리하는 방법을 보여줍니다.

ACL의 구조에 대한 자세한 내용은 [Azure Data Lake Storage Gen2의 ACL(액세스 제어 목록)](data-lake-storage-access-control.md)을 참조하세요.

ACL과 Azure 역할을 함께 사용하는 방법에 대한 자세한 내용은 [Azure Data Lake Storage Gen2의 액세스 제어 모델](data-lake-storage-access-control-model.md)을 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

- Azure 구독 [Azure 평가판](https://azure.microsoft.com/pricing/free-trial/)을 참조하세요.

- 계층 구조 네임스페이스 기능을 사용할 수 있는 스토리지 계정. 만들려면 [다음 지침](create-data-lake-storage-account.md)을 수행합니다.

- 다음 보안 권한 중 하나가 있어야 합니다.

  - 사용자 ID에 대상 컨테이너, 스토리지 계정, 부모 리소스 그룹 또는 구독의 범위에서 [Storage Blob 데이터 소유자](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) 역할이 할당되었습니다.

  - ACL 설정을 적용하려는 대상 컨테이너, 디렉터리 또는 Blob을 소유하는 사용자입니다.

## <a name="manage-an-acl"></a>ACL 관리

1. [Azure Portal](https://portal.azure.com/)에 로그인하여 시작합니다.

2. 스토리지 계정을 찾아 계정 개요를 표시합니다.

3. **데이터 스토리지** 아래에서 **컨테이너** 를 선택합니다.

   스토리지 계정의 컨테이너가 나타납니다.

   > [!div class="mx-imgBorder"]
   > ![Azure Portal에서 스토리지 계정 컨테이너의 위치](./media/data-lake-storage-acl-azure-portal/find-containers-in-azure-portal.png)

5. 컨테이너, 디렉터리 또는 Blob으로 이동합니다. 개체를 마우스 오른쪽 단추로 클릭하고 **ACL 관리** 를 선택합니다.

   > [!div class="mx-imgBorder"]
   > ![ACL 관리를 바로 가기 메뉴](./media/data-lake-storage-acl-azure-portal/manage-acl-menu-item.png)

   **ACL 관리** 페이지의 **액세스 권한** 탭이 나타납니다. 이 탭의 컨트롤을 사용하여 개체에 대한 액세스를 관리합니다.

   > [!div class="mx-imgBorder"]
   > ![ACL 관리 페이지의 ACL 탭에 액세스](./media/data-lake-storage-acl-azure-portal/access-acl-page.png)

7. ACL에 *보안 주체* 를 추가하려면 **보안 주체 추가** 단추를 선택합니다.

   > [!TIP]
   > 보안 주체는 Azure AD(Active Directory)에 정의된 사용자, 그룹, 서비스 주체 또는 관리 ID를 나타내는 개체입니다.

   검색 상자를 사용하여 보안 주체를 찾고 **선택** 단추를 클릭합니다.

   > [!div class="mx-imgBorder"]
   > ![ACL에 보안 주체 추가](./media/data-lake-storage-acl-azure-portal/get-security-principal.png)

   > [!NOTE]
   > Azure AD에서 보안 그룹을 만든 다음, 개별 사용자 대신 그룹에 대한 권한을 유지하는 것이 좋습니다. 이 권장 사항 및 기타 모범 사례에 대한 자세한 내용은 [Azure Data Lake Storage Gen2의 액세스 제어 모델](data-lake-storage-access-control-model.md)을 참조하세요.

8. *기본 ACL* 을 관리하려면 **기본 권한 탭** 을 선택하고 **기본 권한 구성** 확인란을 선택합니다.

   > [!TIP]
   > 기본 ACL은 디렉터리에서 만든 자식 항목에 대한 액세스 ACL을 결정하는 ACL의 템플릿입니다. Blob에는 기본 ACL이 없으므로 이 탭은 디렉터리에 대해서만 나타납니다.

   > [!div class="mx-imgBorder"]
   > ![ACL 관리 페이지의 기본 ACL 탭](./media/data-lake-storage-acl-azure-portal/default-acl-page.png)

## <a name="apply-an-acl-recursively"></a>재귀적으로 ACL 적용

각 자식 항목에 대해 개별적으로 변경할 필요 없이 부모 디렉터리의 기존 자식 항목에 ACL 항목을 재귀적으로 적용할 수 있습니다. 그러나 Azure Portal을 사용하여 ACL 항목을 재귀적으로 적용할 수는 없습니다.

ACL을 재귀적으로 적용하려면 Azure Storage Explorer, PowerShell 또는 Azure CLI를 사용합니다. 코드를 작성하는 것을 선호하는 경우 .NET, Java, Python 또는 Node.js API를 사용할 수도 있습니다.

[ACL 설정 방법](data-lake-storage-access-control.md#how-to-set-acls)에서 전체 가이드 목록을 찾을 수 있습니다.

## <a name="next-steps"></a>다음 단계

Data Lake Storage Gen2 권한 모델에 대해 알아봅니다.

> [!div class="nextstepaction"]
> [Azure Data Lake Storage Gen2의 액세스 제어 모델](./data-lake-storage-access-control-model.md)
