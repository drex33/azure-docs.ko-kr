---
title: Azure Data Lake Storage Gen2용 스토리지 계정 만들기
description: Azure Data Lake Storage Gen2에서 사용할 스토리지 계정을 만드는 방법을 알아봅니다.
author: normesta
ms.topic: how-to
ms.author: normesta
ms.date: 10/14/2021
ms.service: storage
ms.reviewer: stewu
ms.subservice: data-lake-storage-gen2
ms.openlocfilehash: 35761e10cecf1cb209f004f99f773c09d91dc0e5
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2021
ms.locfileid: "130046870"
---
# <a name="create-a-storage-account-to-use-with-azure-data-lake-storage-gen2"></a>Data Lake Storage Gen2에서 사용할 스토리지 계정 만들기

Data Lake Storage Gen2 기능을 사용하려면 계층 구조 네임스페이스가 있는 스토리지 계정을 만듭니다.

단계별 지침은 [스토리지 계정 만들기](../common/storage-account-create.md?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json)를 참조하세요.

계정을 만들 때 이 문서에 설명된 옵션을 선택해야 합니다.

## <a name="choose-a-storage-account-type"></a>스토리지 계정 유형 선택

Data Lake Storage 기능은 다음 유형의 스토리지 계정에서 지원됩니다.

- 표준 범용 v2
- 프리미엄 블록 Blob

이러한 항목 중 하나를 선택하는 방법에 대한 자세한 내용은 [스토리지 계정 개요](../common/storage-account-overview.md?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json)를 참조하세요.

**스토리지 계정 만들기** 페이지의 **기본** 탭에서 이러한 두 가지 유형의 계정 중에서 선택할 수 있습니다.

표준 범용 v2 계정을 만들려면 **표준** 을 선택합니다.

프리미엄 블록 Blob 계정을 만들려면 **프리미엄** 을 선택합니다. 그런 다음, **프리미엄 계정 유형** 드롭다운 목록에서 **블록 Blob** 을 선택합니다.

> [!div class="mx-imgBorder"]
> ![프리미엄 블록 Blob 옵션](./media/create-data-lake-storage-account/premium-block-blob-option.png)

## <a name="enable-the-hierarchical-namespace"></a>계층 구조 네임스페이스 사용

**스토리지 계정 만들기** 페이지의 **고급** 탭에서 **계층 구조 네임스페이스 사용** 설정을 선택하여 Data Lake Storage 기능을 잠금 해제합니다. 

다음 이미지는 **스토리지 계정 만들기** 페이지의 이 설정을 보여 줍니다.

> [!div class="mx-imgBorder"]
> ![계층 구조 네임스페이스 설정](./media/create-data-lake-storage-account/hierarchical-namespace-feature.png)

기존 계정에서 Data Lake Storage 기능을 사용 하도록 설정 하려면 [Azure Data Lake Storage Gen2 기능으로 Azure Blob Storage 업그레이드](upgrade-to-data-lake-storage-gen2-how-to.md)를 참조 하세요.

> [!NOTE]
> **데이터 보호** 및 계층 구조 네임스페이스는 동시에 사용하도록 설정할 수 없습니다.

## <a name="next-steps"></a>다음 단계

- [Storage 계정 개요](../common/storage-account-overview.md)
- [Azure Data Lake Storage Gen2 기능으로 Azure Blob Storage 업그레이드](upgrade-to-data-lake-storage-gen2-how-to.md)
- [Azure Data Lake Storage Gen2의 액세스 제어](data-lake-storage-access-control.md)
