---
title: 'Storage Explorer: Azure Data Lake Storage Gen2의 ACL 설정'
description: Azure Storage Explorer를 이용해 HNS(계층 구조 네임스페이스)를 사용하도록 설정된 스토리지 계정에서 ACL(액세스 제어 목록)을 관리합니다.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: how-to
ms.date: 10/28/2021
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: 25adc75204a3485810d7f4e3281dde95e0944ecd
ms.sourcegitcommit: 66b6e640e2a294a7fbbdb3309b4829df526d863d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/01/2021
ms.locfileid: "133363129"
---
# <a name="use-azure-storage-explorer-to-manage-acls-in-azure-data-lake-storage-gen2"></a>Azure Storage Explorer를 사용하여 Azure Data Lake Storage Gen2에서 ACL 관리

이 문서에서는 [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/)를 이용해 HNS(계층 구조 네임스페이스)를 사용하도록 설정된 스토리지 계정에서 ACL(액세스 제어 목록)을 관리하는 방법을 보여 줍니다.

Storage Explorer를 사용하여 디렉터리 및 파일의 ACL을 확인한 다음 업데이트할 수 있습니다. 부모 디렉터리 아래에 만들어진 새 자식 항목에서는 이미 ACL 상속을 사용할 수 있는 상태입니다. 그러나 각 자식 항목을 개별적으로 변경할 필요 없이 부모 디렉터리의 기존 자식 항목에서 ACL 설정을 재귀적으로 적용할 수도 있습니다.

이 문서에서는 파일 또는 디렉터리의 ACL을 수정하는 방법과 자식 디렉터리에 ACL 설정을 재귀적으로 적용하는 방법을 보여 줍니다.

## <a name="prerequisites"></a>사전 요구 사항

- Azure 구독 [Azure 평가판](https://azure.microsoft.com/pricing/free-trial/)을 참조하세요.

- HNS(계층 구조 네임스페이스)를 사용하도록 설정된 스토리지 계정입니다. 만들려면 [다음 지침](../common/storage-account-create.md)을 수행합니다.

- Azure Storage Explorer가 로컬 컴퓨터에 설치되었습니다. Windows, Macintosh 또는 Linux용 Azure Storage Explorer를 설치하려면 [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/)를 참조하세요.

- 다음 보안 권한 중 하나가 있어야 합니다.

  - 사용자 ID에 대상 컨테이너, 스토리지 계정, 부모 리소스 그룹 또는 구독의 범위에서 [Storage Blob 데이터 소유자](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) 역할이 할당되었습니다.

  - ACL 설정을 적용하려는 대상 컨테이너, 디렉터리 또는 Blob을 소유하는 사용자입니다.

> [!NOTE]
> Storage Explorer는 Azure Data Lake Storage Gen2와 작업할 때 Blob(blob)과 Data Lake Storage Gen2(dfs) 양쪽의 [엔드포인트](../common/storage-private-endpoints.md#private-endpoints-for-azure-storage)를 모두 사용합니다. 프라이빗 엔드포인트를 사용하여 Azure Data Lake Storage Gen2에 대한 액세스를 구성하는 경우, 스토리지 계정에 두 개의 프라이빗 엔드포인트를 만듭니다. 여기서 하나는 대상 하위 리소스 `blob`이고 다른 하나는 대상 하위 리소스 `dfs`입니다.

## <a name="sign-in-to-storage-explorer"></a>Storage Explorer에 로그인

Storage Explorer를 처음 시작 하는 경우 **Azure Storage 창에 대 한 Microsoft Azure Storage Explorer 커넥트** 표시 됩니다. Storage Explorer에서는 몇 가지 방법으로 스토리지 계정에 연결할 수 있지만, 현재는 ACL을 관리하는 한 가지 방법만 지원됩니다.

**리소스 선택** 패널에서 **구독** 을 선택합니다.

:::image type="content" source="./media/data-lake-storage-explorer-acl/storage-explorer-connect-sml.png" alt-text="리소스 Microsoft Azure Storage Explorer 선택 창을 보여 주는 스크린샷" lightbox="./media/data-lake-storage-explorer-acl/storage-explorer-connect-lrg.png":::

**Azure 환경 선택** 패널에서 로그인할 Azure 환경을 선택합니다. 글로벌 Azure, 국가 클라우드 또는 Azure Stack 인스턴스에 로그인할 수 있습니다. 그런 후 **다음** 을 선택합니다.

:::image type="content" alt-text="Microsoft Azure Storage Explorer 표시 하 고 Azure 환경 선택 옵션을 강조 표시 하는 스크린샷" source="./media/data-lake-storage-explorer-acl/storage-explorer-select-sml.png"  lightbox="./media/data-lake-storage-explorer-acl/storage-explorer-select-sml.png":::

Storage Explorer는 로그인할 웹 페이지를 엽니다.

Azure 계정으로 성공적으로 로그인하면 계정과 계정에 연결된 Azure 구독이 **계정 관리** 아래에 나타납니다. 작업 하려는 Azure 구독을 선택 하 고 **탐색기 열기** 를 선택 합니다.

:::image type="content" alt-text="Microsoft Azure Storage Explorer 표시 하 고 계정 관리 창 및 탐색기 열기 단추를 강조 표시 하는 스크린샷" source="./media/data-lake-storage-explorer-acl/storage-explorer-account-panel-sml.png"  lightbox="./media/data-lake-storage-explorer-acl/storage-explorer-account-panel-sml.png":::

작업이 완료되면 Azure Storage Explorer는 표시된 **탐색기** 탭을 로드합니다. 이 보기는 [Azure Storage 에뮬레이터](../common/storage-use-azurite.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json), [Cosmos DB](../../cosmos-db/storage-explorer.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) 계정 또는 [Azure Stack](/azure-stack/user/azure-stack-storage-connect-se?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) 환경을 통해 구성된 로컬 스토리지뿐만 아니라 모든 Azure Storage 계정에 대한 정보를 제공합니다.

:::image type="content" alt-text="Microsoft Azure Storage Explorer - 연결 창" source="./media/data-lake-storage-explorer-acl/storage-explorer-main-page-sml.png" lightbox="./media/data-lake-storage-explorer-acl/storage-explorer-main-page-lrg.png":::

## <a name="manage-an-acl"></a>ACL 관리

컨테이너, 디렉터리 또는 파일을 마우스 오른쪽 단추로 클릭한 다음 **액세스 제어 목록 관리** 를 클릭합니다.  다음 스크린샷에서는 디렉터리를 마우스 오른쪽 단추로 클릭할 때 표시되는 메뉴를 보여 줍니다.

> [!div class="mx-imgBorder"]
> ![Azure Storage Explorer에서 디렉터리를 마우스 오른쪽 단추로 클릭](./media/data-lake-storage-explorer-acl/manage-access-control-list-option.png)

**액세스 관리** 대화 상자에서는 소유자 및 소유자 그룹의 사용 권한을 관리할 수 있습니다. 또한 사용 권한을 관리할 수 있는 액세스 제어 목록에 새 사용자 및 그룹을 추가할 수 있습니다.

> [!div class="mx-imgBorder"]
> ![액세스 관리 대화 상자](./media/data-lake-storage-explorer-acl/manage-access-dialog-box.png)

액세스 제어 목록에 새 사용자 또는 그룹을 추가하려면 **추가** 단추를 선택합니다. 목록에 추가할 해당하는 Azure AD(Azure Active Directory) 항목을 입력하고 **추가** 를 선택합니다.  이제 사용자 또는 그룹이 **사용자 및 그룹** 필드에 표시되며, 사용 권한 관리를 시작할 수 있습니다.

> [!NOTE]
> Azure AD에서 보안 그룹을 만들고 개별 사용자가 아닌 그룹에서 사용 권한을 유지 관리하는 것이 권장되는 모범 사례입니다. 이 권장 사항 및 기타 모범 사례에 대한 자세한 내용은 [Azure Data Lake Storage Gen2의 액세스 제어 모델](data-lake-storage-explorer-acl.md)을 참조하세요.

확인란 컨트롤을 사용하여 액세스 및 기본 ACL을 설정합니다. 이러한 ACL 유형 간의 차이점에 대한 자세한 내용은 [ACL 유형](data-lake-storage-access-control.md#types-of-acls)을 참조하세요.

## <a name="apply-acls-recursively"></a>재귀적으로 ACL 적용

각 자식 항목에 대해 개별적으로 변경할 필요 없이 부모 디렉터리의 기존 자식 항목에 ACL 항목을 재귀적으로 적용할 수 있습니다.

ACL 항목을 재귀적으로 적용하려면 컨테이너 또는 디렉터리를 마우스 오른쪽 단추로 클릭한 다음 **액세스 제어 목록 전파** 를 클릭합니다.  다음 스크린샷에서는 디렉터리를 마우스 오른쪽 단추로 클릭할 때 표시되는 메뉴를 보여 줍니다.

> [!div class="mx-imgBorder"]
> ![디렉터리를 마우스 오른쪽 단추로 클릭하고 액세스 제어 설정 전파를 선택](./media/data-lake-storage-explorer-acl/propagate-access-control-list-option.png)

## <a name="next-steps"></a>다음 단계

Data Lake Storage Gen2 권한 모델에 대해 알아봅니다.

> [!div class="nextstepaction"]
> [Azure Data Lake Storage Gen2의 액세스 제어 모델](./data-lake-storage-access-control-model.md)
