---
title: Purview를 사용하여 ADF의 데이터 검색 및 살펴보기
description: Purview를 사용하여 Azure Data Factory에서 데이터를 검색하고 살펴보는 방법 알아보기.
ms.service: data-factory
ms.topic: conceptual
author: linda33wj
ms.author: jingwang
ms.custom: seo-lt-2019
ms.date: 08/10/2021
ms.openlocfilehash: 037e7fadd84d3b3b7405507a6116ed1e8c0ef59d
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/03/2021
ms.locfileid: "123427051"
---
# <a name="discover-and-explore-data-in-adf-using-purview"></a>Purview를 사용하여 ADF의 데이터 검색 및 살펴보기

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

이 문서에서는 사용자가 Azure Purview 계정을 Data Factory에 등록하게 됩니다. 이렇게 연결하면 ADF 기능을 통해 Azure Purview 자산을 찾아 상호 작용할 수 있습니다. 

ADF에서 다음 작업을 수행할 수 있습니다. 
- 위쪽의 검색 상자에서 키워드를 사용하여 Purview 자산 찾기 
- 메타데이터, 계보, 주석을 기반으로 데이터 이해 
- 연결된 서비스 또는 데이터 세트를 사용하여 해당 데이터를 데이터 팩터리에 연결하기 

## <a name="prerequisites"></a>사전 요구 사항 

- [Azure  Purview 계정](../purview/create-catalog-portal.md) 
- [Data Factory](./quickstart-create-data-factory-portal.md) 
- [Azure Purview 계정을 Data Factory에 연결하기](./connect-data-factory-to-azure-purview.md) 

## <a name="using-azure-purview-in-data-factory"></a>Data Factory에서 Azure Purview 사용 

Data Factory에서 Azure Purview를 사용하려면 해당 Purview 계정에 대한 액세스 권한이 있어야 합니다. Data Factory가 Purview 권한을 통과합니다. 예를 들어, 큐레이터 권한 역할을 사용하는 경우 Azure Purview에서 검색된 메타데이터를 편집할 수 있습니다. 

### <a name="data-discovery-search-datasets"></a>데이터 검색: 데이터 세트 검색 

Azure Purview에서 등록하고 검색한 데이터를 검색하기 위해 Data Factory 포털의 위쪽 가운데에 있는 검색 표시줄을 사용할 수 있습니다. Azure Purview를 선택하여 모든 조직 데이터를 검색합니다. 

:::image type="content" source="./media/data-factory-purview/search-dataset.png" alt-text="데이터 세트에 수행하는 것을 보여 주는 스크린샷":::

### <a name="actions-that-you-can-perform-over-datasets-with-data-factory-resources"></a>Data Factory 리소스를 사용하여 데이터 세트에 수행할 수 있는 작업 
Azure Purview에서 검색하는 데이터에 대해 연결된 서비스, 데이터 세트 또는 데이터 흐름을 직접 만들 수 있습니다.

:::image type="content" source="./media/data-factory-purview/actions-over-purview-data.png" alt-text="Azure Purview에서 검색하는 데이터에 대해 연결된 서비스, 데이터 세트 또는 데이터 흐름을 직접 만들 수 있는 방법을 보여 주는 스크린샷":::

##  <a name="nextsteps"></a>다음 단계 

[자습서: Data Factory 계보 데이터를 Azure Purview에 푸시](turorial-push-lineage-to-purview.md)

[Azure Purview 계정을 Data Factory에 연결하기](connect-data-factory-to-azure-purview.md) 

[Azure Purview Data Catalog에서 데이터를 검색하는 방법](../purview/how-to-search-catalog.md)