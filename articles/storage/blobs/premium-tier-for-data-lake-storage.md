---
title: Azure Data Lake Storage에 대한 프리미엄 계층
description: Azure Data Lake Storage Gen2에서 프리미엄 성능 계층 사용
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 10/14/2021
ms.author: normesta
ms.openlocfilehash: e9cab4b65da62bddd47cdab97c6f586b07f379d4
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2021
ms.locfileid: "130044455"
---
# <a name="premium-tier-for-azure-data-lake-storage"></a>Azure Data Lake Storage에 대한 프리미엄 계층

Azure Data Lake Storage Gen2는 이제 [프리미엄 블록 Blob Storage 계정 를 지원합니다.](storage-blob-block-blob-premium.md) Premium 블록 Blob Storage 계정은 일관성 있는 대기 시간이 낮고 트랜잭션 수가 많은 빅 데이터 분석 애플리케이션 및 워크로드에 적합합니다. 워크로드 예에는 대화형 워크로드, IoT, 스트리밍 분석, 인공 지능 및 기계 학습이 포함됩니다. 

>[!TIP]
> 프리미엄 블록 Blob Storage 계정 사용의 성능 및 비용 이점에 대해 자세히 알아보고 다른 Data Lake Storage Gen2 고객이 이러한 유형의 계정을 사용한 방법을 알아보려면 [블록 Blob Storage 계정 Premium 참조하세요.](storage-blob-block-blob-premium.md)

## <a name="getting-started-with-premium"></a>프리미엄 시작

먼저 즐겨찾는 Blob Storage 기능이 프리미엄 블록 Blob Storage 계정과 호환되는지 확인한 다음, 계정을 만듭니다. 

>[!NOTE]
> 기존 표준 범용 v2 스토리지 계정을 프리미엄 블록 Blob Storage 계정으로 변환할 수 없습니다. 프리미엄 블록 Blob Storage 계정으로 마이그레이션하려면 프리미엄 블록 Blob Storage 계정을 만들고 데이터를 새 계정으로 마이그레이션해야 합니다. 

### <a name="check-for-blob-storage-feature-compatibility"></a>Blob Storage 기능 호환성 확인

일부 Blob Storage 기능은 아직 지원되지 않거나 프리미엄 블록 Blob Storage 계정에서 부분적으로 지원됩니다. 프리미엄을 선택하기 전에 [Azure Storage 계정의 Blob Storage 기능 지원](storage-feature-support-in-storage-accounts.md) 문서를 검토하여 사용하려는 기능이 계정에서 완전히 지원되는지 확인합니다. 기능 지원은 항상 확장되므로 업데이트를 위해 이 문서를 정기적으로 검토해야 합니다.

### <a name="create-a-new-storage-account"></a>새 Storage 계정 만들기

새 Azure Storage 계정을 만듭니다. 전체 지침은 [스토리지 계정 만들기를](../common/storage-account-create.md) 참조하세요. 

계정을 만들 때 **Premium** 성능 옵션과 블록 **Blob** 계정 유형을 선택합니다. 

> [!div class="mx-imgBorder"]
> ![블록 Blob Storage 계정 만들기](./media/storage-blob-block-blob-premium/create-block-blob-storage-account.png)

Azure Data Lake Storage Gen2 기능의 잠금을 해제하려면 스토리지 계정 만들기 페이지의 **고급** 탭에서 **계층 구조 네임스페이스** 설정을 사용하도록 **설정합니다.** 

다음 이미지는 **스토리지 계정 만들기** 페이지의 이 설정을 보여 줍니다.

> [!div class="mx-imgBorder"]
> ![계층 구조 네임스페이스 설정](./media/create-data-lake-storage-account/hierarchical-namespace-feature.png)

## <a name="next-steps"></a>다음 단계

Azure Databricks, Azure HDInsight 및 Azure Synapse Analytics와 같이 선호하는 분석 서비스와 Azure Data Lake Storage에 대한 프리미엄 계층을 사용합니다. [Azure Data Lake Storage Gen2에서 Azure 서비스를 사용하는 자습서를](data-lake-storage-integrate-with-services-tutorials.md)참조하세요.
