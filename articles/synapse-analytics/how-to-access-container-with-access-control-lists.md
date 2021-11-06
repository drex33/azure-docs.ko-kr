---
title: Azure Synapse Analytics ACL이 있는 ADLS Gen2 폴더 찾아보기
description: Azure Synapse Analytics ACL을 사용하여 ADLS Gen2 폴더를 찾아보는 방법을 알아봅니다.
author: meenalsri
ms.author: mesrivas
ms.service: synapse-analytics
ms.subservice: overview
ms.topic: how-to
ms.date: 10/28/2021
ms.openlocfilehash: 2d16c9eae1e48c1471eb7c250bd9cf9e71d16d79
ms.sourcegitcommit: 591ffa464618b8bb3c6caec49a0aa9c91aa5e882
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/06/2021
ms.locfileid: "131894563"
---
# <a name="browse-adls-gen2-folders-preview-in-an-azure-synapse-analytics-workspace"></a>Azure Synapse Analytics 작업 영역에서 ADLS Gen2 폴더(미리 보기) 찾아보기
이제 데이터 허브의 컨테이너 또는 특정 폴더에 연결하여 Azure Synapse Analytics 작업 영역에서 Azure Data Lake Storage Gen2(ADLS Gen2) 컨테이너 또는 폴더를 찾아볼 수 있습니다. 조직에서 사용자에게 스토리지 계정에 대한 Storage Blob 데이터 기여자 역할을 부여하지 않지만 컨테이너 또는 특정 폴더에 POSIX 유사 ACL(액세스 제어 목록)을 만들 수 있는 경우 이 문서에 나열된 단계에 따라 ADLS Gen2 파일 및 폴더를 찾아볼 수 있습니다.

>[!Note]
>이 미리 보기 기능을 사용하면 사용자가 ACL이 있지만 파일 또는 폴더에 대한 업로드, 다운로드, 만들기, 편집, 삭제 또는 이름 바꾸기 작업을 지원하지 않는 ADLS Gen2 폴더를 찾아볼 수 있습니다. Storage Blob 데이터 기여자 역할이 있는 사용자는 ADLS Gen2 폴더에서 모든 작업을 수행할 수 있습니다.


## <a name="prerequisites"></a>필수 조건
Azure Synapse 컨테이너 또는 폴더를 연결하기 전에 다음 필수 조건이 충족되어야 합니다.
* Azure RBAC(Storage Blob 데이터 기여자) 역할 또는 ACL(액세스 제어 목록)을 Azure AD ID에 부여해야 합니다.
* ADLS Gen2 컨테이너에 연결된 서비스는 Synapse 작업 영역에서 만들어야 합니다.


## <a name="connect-adls-gen2-folder-to-your-azure-synapse-analytics-workspace"></a>커넥트 Azure Synapse Analytics 작업 영역에 폴더 ADLS Gen2
1. 데이터 허브의 **연결된 탭으로** 이동합니다.
2. **Azure Data Lake Storage Gen2를** 마우스 오른쪽 단추로 클릭하고 커넥트 선택하여 **Azure Storage(미리 보기)**
    * ADLS Gen2 컨테이너 또는 폴더 URL을 형식으로 제공합니다. `https://storageaccountname.dfs.core.windows.net/containername/foldername/`
    * 연결에 대한 고유한 이름 제공
    * ADLS Gen2 계정이 포함된 테넌트 이름을 제공합니다. 비워 두면 Synapse 작업 영역과 연결된 테넌트는 사용됩니다.
    ![ACL이 있는 Storage 폴더에 커넥트](./media/connect-to-azure-storage-with-access-control-lists/connect-to-azure-storage-with-acls.png)
3. **연결** 을 클릭합니다. 연결된 컨테이너 아래에 연결된 컨테이너 또는 **폴더가 표시됩니다.**


## <a name="next-steps"></a>다음 단계
Azure Data Lake Storage Gen2의 ACL에 대해 자세히 알아봅니다.
- [Azure Data Lake Storage Gen2의 액세스 제어 목록](../storage/blobs/data-lake-storage-access-control.md)
- [Azure Portal을 사용하여 Azure Data Lake Storage Gen2에서 ACL 관리](../storage/blobs/data-lake-storage-acl-azure-portal.md)
