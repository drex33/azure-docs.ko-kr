---
title: GitHub에 연결
titleSuffix: Azure Data Factory & Azure Synapse
description: GitHub를 사용하여 Common Data Model 엔터티 참조 지정
author: linda33wj
ms.service: data-factory
ms.subservice: data-movement
ms.custom: synapse
ms.topic: conceptual
ms.date: 08/30/2021
ms.author: jingwang
ms.openlocfilehash: 8d765a5150180303f55cc669da3ddb84deea68da
ms.sourcegitcommit: 851b75d0936bc7c2f8ada72834cb2d15779aeb69
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/31/2021
ms.locfileid: "123312903"
---
# <a name="use-github-to-read-common-data-model-entity-references"></a>GitHub를 사용하여 Common Data Model 엔터티 참조 읽기

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Azure Data Factory의 GitHub 커넥터는 데이터 흐름 매핑에서 [Common Data Model](format-common-data-model.md) 형식에 대한 엔터티 참조 스키마를 수신하는 데만 사용됩니다.

## <a name="create-a-linked-service-to-github-using-ui"></a>UI를 사용하여 GitHub에 연결된 서비스 만들기

다음 단계를 사용하여 Azure Portal UI에서 GitHub에 연결된 서비스를 만듭니다.

1. Azure Data Factory 또는 Synapse 작업 영역에서 관리 탭으로 이동하여 연결된 서비스를 선택한 후 새로 만들기를 클릭합니다.

    # <a name="azure-data-factory"></a>[Azure Data Factory](#tab/data-factory)

    :::image type="content" source="media/doc-common-process/new-linked-service.png" alt-text="Azure Data Factory UI를 사용하여 새로운 연결된 서비스를 만드는 스크린샷":::

    # <a name="azure-synapse"></a>[Azure Synapse](#tab/synapse-analytics)

    :::image type="content" source="media/doc-common-process/new-linked-service-synapse.png" alt-text="Azure Synapse UI를 사용하여 새로운 연결된 서비스를 만드는 스크린샷":::

2. GitHub를 검색하고 GitHub 커넥터를 선택합니다.

   :::image type="content" source="media/connector-github/github-connector.png" alt-text="GitHub 커넥터의 스크린샷":::    


1. 서비스 세부 정보를 구성하고, 연결을 테스트하고, 새로운 연결된 서비스를 만듭니다.

   :::image type="content" source="media/connector-github/configure-github-linked-service.png" alt-text="GitHub에 연결된 서비스 구성의 스크린샷":::


## <a name="linked-service-properties"></a>연결된 서비스 속성

GitHub 연결된 서비스에 다음 속성이 지원됩니다.

| 속성 | 설명 | 필수 |
|:--- |:--- |:--- |
| type | 형식 속성은 **GitHub** 로 설정해야 합니다. | 예
| userName | GitHub 사용자 이름 | 예 |
| password | GitHub 암호 | 예 |

## <a name="next-steps"></a>다음 단계

매핑 데이터 흐름에서 [원본 데이터 세트](data-flow-source.md)를 만듭니다.