---
title: Azure Storage Explorer를 사용하여 Blob 스토리지 데이터 이동 - Team Data Science Process
description: Azure Storage Explorer를 사용하여 Azure Blob Storage 데이터를 업로드하고 다운로드하는 방법을 알아봅니다.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 53cb8cdd1c5f9824b07b16b8b6c70648603b9f38
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "98788912"
---
# <a name="move-data-to-and-from-azure-blob-storage-using-azure-storage-explorer"></a>Azure Storage Explorer를 사용하여 Azure Blob Storage 간에 데이터 이동
Azure Storage Explorer는 Windows, macOS 및 Linux에서 Azure Storage 데이터 작업 시에 사용할 수 있는 Microsoft의 무료 도구입니다. 이 항목에서는 Azure Storage Explorer를 사용하여 Azure Blob Storage에서 데이터를 업로드 및 다운로드하는 방법을 설명합니다. 이 도구는 [Microsoft Azure Storage Explorer](https://storageexplorer.com/)에서 다운로드할 수 있습니다.

[!INCLUDE [blob-storage-tool-selector](../../../includes/machine-learning-blob-storage-tool-selector.md)]

> [!NOTE]
> [Azure의 데이터 과학 가상 머신](../data-science-virtual-machine/overview.md)에서 제공하는 스크립트를 통해 설정된 VM을 사용하는 경우 Azure Storage Explorer가 VM에 이미 설치되어 있습니다.
> 
> [!NOTE]
> Azure Blob Storage에 대한 전체 소개 내용은 [Azure Blob 기본 사항](../../storage/blobs/storage-quickstart-blobs-dotnet.md) 및 [Azure Blob Service](/rest/api/storageservices/Blob-Service-Concepts)를 참조하세요.   
> 
> 

## <a name="prerequisites"></a>사전 요구 사항
이 문서에서는 사용자에게 Azure 구독, 스토리지 계정 및 계정에 해당하는 스토리지 키가 있다고 가정합니다. 데이터를 업로드/다운로드하려면 Azure Storage 계정 이름 및 계정 키를 알아야 합니다. 

* Azure 구독을 설정하려면 [1개월 무료 평가판](https://azure.microsoft.com/pricing/free-trial/)을 참조하세요.
* 스토리지 계정을 만들고 계정 및 키 정보를 가져오는 방법에 대한 지침은 [Azure Storage 계정 정보](../../storage/common/storage-account-create.md)를 참조하세요. Storage 계정의 선택키를 적어 두세요. Azure Storage Explorer 도구를 사용하여 계정에 연결하려면 이 키가 있어야 합니다.
* Azure Storage Explorer 도구는 [Microsoft Azure Storage Explorer](https://storageexplorer.com/)에서 다운로드할 수 있습니다. 설치 중에 표시되는 기본값을 적용합니다.

<a id="explorer"></a>

## <a name="use-azure-storage-explorer"></a>Azure Storage Explorer 사용
다음 단계에서는 Azure Storage Explorer를 사용하여 데이터를 업로드/다운로드하는 방법을 설명합니다. 

1. Microsoft Azure Storage Explorer를 시작합니다.
2. **계정에 로그인...** 마법사를 실행하려면 **Azure 계정 설정** 아이콘을 선택하고 **계정 추가** 를 선택한 후에 자격 증명을 입력합니다. 
![Azure Storage 계정 추가](./media/move-data-to-azure-blob-using-azure-storage-explorer/add-an-azure-store-account.png)
3. **Azure Storage에 연결** 마법사를 실행하려면 **Azure Storage에 연결** 아이콘을 선택합니다. ![“Azure Storage에 연결” 클릭](./media/move-data-to-azure-blob-using-azure-storage-explorer/connect-to-azure-storage-1.png)
4. **Azure Storage에 연결** 마법사에서 Azure Storage 계정의 액세스 키를 입력한 후에 **다음** 을 클릭합니다. ![Azure Storage 계정의 액세스 키 입력](./media/move-data-to-azure-blob-using-azure-storage-explorer/connect-to-azure-storage-2.png)
5. **계정 이름** 상자에 스토리지 계정 이름을 입력한 후에 **다음** 을 클릭합니다. ![외부 스토리지 연결](./media/move-data-to-azure-blob-using-azure-storage-explorer/attach-external-storage.png)
6. 이제 추가된 스토리지 계정이 표시됩니다. 스토리지 계정에서 Blob 컨테이너를 만들려면 해당 계정의 **Blob 컨테이너** 노드를 마우스 오른쪽 단추로 클릭하고 **Blob 컨테이너 만들기** 를 선택한 후에 이름을 입력합니다.
7. 컨테이너에 데이터를 업로드하려면 대상 컨테이너를 선택하고 **업로드** 단추를 클릭합니다.
![스토리지 계정](./media/move-data-to-azure-blob-using-azure-storage-explorer/storage-accounts.png)
8. **파일** 상자 오른쪽의 **...** 를 클릭하고 파일 시스템에서 업로드할 파일을 하나 이상 선택한 후에 **업로드** 를 클릭하여 파일 업로드를 시작합니다.![파일 업로드](./media/move-data-to-azure-blob-using-azure-storage-explorer/upload-files-to-blob.png)
9. 데이터를 다운로드하려면 해당 컨테이너에서 다운로드하려는 Blob를 선택한 후에 **다운로드** 를 클릭합니다. ![파일 다운로드](./media/move-data-to-azure-blob-using-azure-storage-explorer/download-files-from-blob.png)
