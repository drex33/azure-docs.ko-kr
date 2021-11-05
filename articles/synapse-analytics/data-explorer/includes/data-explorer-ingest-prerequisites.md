---
ms.topic: include
ms.date: 11/02/2021
author: shsagir
ms.author: shsagir
ms.service: synapse-analytics
ms.subservice: data-explorer
ms.openlocfilehash: 153f5c71925e3f4fe1078f014684a12c60a054b1
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131479730"
---
- Azure 구독 [평가판 Azure 계정](https://azure.microsoft.com/free/)을 만듭니다.

- [Synapse Studio](../data-explorer-create-pool-studio.md) 또는 [Azure Portal](../data-explorer-create-pool-portal.md)을 사용하여 Data Explorer 풀 만들기
- Data Explorer 데이터베이스를 만듭니다.
    1. Synapse Studio의 왼쪽 창에서 **데이터** 를 선택합니다.
    1. **&plus;** (새 리소스 추가) > **Data Explorer 풀** 을 선택하고 다음 정보를 사용합니다.

        | 설정 | 제안 값 | Description |
        |--|--|--|
        | 풀 이름 | *contosodataexplorer* | 사용할 Data Explorer 풀의 이름 |
        | Name | *TestDatabase* | 데이터베이스 이름은 클러스터 내에서 고유해야 합니다. |
        | 기본 보존 기간 | *365* | 데이터를 쿼리에 사용할 수 있도록 보장되는 시간 범위(일)입니다. 시간 범위는 데이터가 수집된 시간부터 측정됩니다. |
        | 기본 캐시 기간 | *31* | 자주 쿼리되는 데이터를 장기 스토리지가 아닌 SSD 스토리지 또는 RAM에 보관할 수 있는 시간 범위(일)입니다. |

    1. **만들기** 를 선택하여 데이터베이스를 만듭니다. 만들기에는 일반적으로 채 1분이 소요되지 않습니다.
