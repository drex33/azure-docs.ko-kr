---
title: Azure Synapse Lake 데이터베이스 개념
description: Lake 데이터베이스 개념과 이를 통해 데이터를 구조화할 수 있는 방법을 알아봅니다.
author: gsaurer
ms.author: gesaur
ms.service: synapse-analytics
ms.subservice: database-editor
ms.topic: conceptual
ms.date: 11/02/2021
ms.custom: template-concept, ignite-fall-2021
ms.openlocfilehash: 5b030bc1551bc8d21101b127c123507f08abf62c
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131510431"
---
# <a name="lake-database"></a>Lake 데이터베이스

Azure Synapse Analytics 레이크 데이터베이스를 사용하면 고객이 데이터베이스 디자인, 저장된 데이터에 대한 메타 정보 및 데이터를 저장해야 하는 방법 및 위치를 설명할 수 있습니다. Lake 데이터베이스는 데이터가 구조화되는 방식을 이해하기 어려운 오늘날의 데이터 레이크 문제를 해결합니다.  

![Lake 데이터베이스 개요](./media/concepts-lake-database/lake-database-overview.png)


## <a name="database-designer"></a>데이터베이스 디자이너

새 데이터베이스 디자이너를 사용하면 lake 데이터베이스에 대한 데이터 모델을 만들고 추가 정보를 추가할 수 있습니다. 모든 엔터티 및 특성을 설명하여 엔터티뿐만 아니라 관계도 포함하는 모델을 따라 자세한 정보를 제공할 수 있습니다. 특히 모델 관계가 부족하여 데이터 레이크의 상호 작용에 어려움이 있었습니다. 이 문제는 이제 레이크가 아닌 데이터베이스에서 사용할 수 있는 가능성을 제공하는 통합 디자이너를 통해 해결되었습니다. 또한 모델에 설명 및 가능한 데모 값을 추가하는 기능을 통해 나중에 모델과 상호 작용하는 사용자가 데이터에 대해 더 잘 이해하기 위해 필요한 정보를 얻을 수 있습니다. 

## <a name="data-storage"></a>데이터 스토리지 

Lake 데이터베이스는 Azure Storage 계정의 데이터 레이크를 사용하여 데이터베이스의 데이터를 저장합니다. 데이터는 Parquet 또는 CSV 형식으로 저장할 수 있으며 스토리지를 최적화하는 데 다른 설정을 사용할 수 있습니다. 모든 레이크 데이터베이스는 연결된 서비스를 사용하여 루트 데이터 폴더의 위치를 정의합니다. 모든 엔터티에 대해 기본적으로 데이터 레이크의 이 데이터베이스 폴더 내에 별도의 폴더가 만들어집니다. 기본적으로 Lake 데이터베이스 내의 모든 테이블은 동일한 형식을 사용하지만 요청된 경우 엔터티별로 데이터의 형식과 위치를 변경할 수 있습니다. 


## <a name="database-compute"></a>데이터베이스 컴퓨팅

레이크 데이터베이스는 Synapse SQL 서버리스 SQL 풀에 노출되며 사용자에게 컴퓨팅에서 스토리지를 분리하는 기능을 제공하는 Apache Spark. Lake 데이터베이스와 연결된 메타데이터를 사용하면 여러 컴퓨팅 엔진이 통합 환경을 제공할 뿐만 아니라 원래 데이터 레이크에서 지원되지 않은 추가 정보(예: 관계)를 쉽게 사용할 수 있습니다. 

## <a name="next-steps"></a>다음 단계

아래 링크를 사용하여 데이터베이스 디자이너 기능을 계속 탐색합니다.
- [레이크 데이터베이스 만들기 빠른 시작](quick-start-create-lake-database.md)
- [데이터베이스 템플릿 개념](concepts-database-templates.md)
