---
title: Compass를 사용하여 Azure Cosmos DB에 연결
description: MongoDB Compass를 사용하여 Azure Cosmos DB에서 데이터를 저장 및 관리하는 방법을 알아봅니다.
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: how-to
ms.date: 08/26/2021
author: gahl-levy
ms.author: gahllevy
ms.openlocfilehash: 8d345dad82c692c76ff2701c2b9a1b15ba5390c1
ms.sourcegitcommit: 03f0db2e8d91219cf88852c1e500ae86552d8249
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2021
ms.locfileid: "123032626"
---
# <a name="use-mongodb-compass-to-connect-to-azure-cosmos-dbs-api-for-mongodb"></a>MongoDB Compass를 사용하여 Azure Cosmos DB의 API for MongoDB에 연결
[!INCLUDE[appliesto-mongodb-api](../includes/appliesto-mongodb-api.md)]

이 자습서에서는 Cosmos DB에서 데이터를 저장 및/또는 관리할 때 [MongoDB Compass](https://www.mongodb.com/products/compass)를 사용하는 방법을 보여 줍니다. 이 연습에서는 MongoDB용 Azure Cosmos DB API를 사용합니다. 사용 방법을 잘 모르는 사용자에게 Compass는 MongoDB용 GUI입니다. 일반적으로 데이터 관리와 함께 데이터를 시각화하고 임시 쿼리를 실행하는 데 사용됩니다.

Cosmos DB는 전역적으로 배포된 Microsoft의 다중 모델 데이터베이스 서비스입니다. Cosmos DB의 핵심인 글로벌 배포 및 수평적 크기 조정 기능의 이점을 활용하여 문서, 키/값 및 그래프 데이터베이스를 빠르게 만들고 쿼리할 수 있습니다.

## <a name="pre-requisites"></a>필수 구성 요소

MongoDB Compass를 사용하여 Cosmos DB 계정에 연결하려면 다음을 수행해야 합니다.

* [Compass](https://www.mongodb.com/download-center/compass?jmp=hero) 다운로드 및 설치
* Cosmos DB [연결 문자열](connect-mongodb-account.md) 정보 확보

## <a name="connect-to-cosmos-dbs-api-for-mongodb"></a>MongoDB용 Cosmos DB API에 연결

Cosmos DB 계정을 Compass에 연결하려면 아래 단계를 따릅니다.

1. [여기](connect-mongodb-account.md)에 설명된 지침에 따라 Azure Cosmos DB의 MongoDB용 API로 구성된 Cosmos 계정에 대한 연결 정보를 검색합니다.

    :::image type="content" source="./media/connect-using-compass/mongodb-compass-connection.png" alt-text="연결 문자열 블레이드의 스크린샷":::

2. Cosmos DB에서 **기본/보조 연결 문자열** 옆에 있는 **클립보드로 복사** 라는 단추를 클릭합니다. 이 단추를 클릭하면 전체 연결 문자열이 클립보드로 복사됩니다.

    :::image type="content" source="./media/connect-using-compass/mongodb-connection-copy.png" alt-text="클립보드로 복사 단추의 스크린샷":::

3. 데스크톱/머신에서 Compass를 열고 **연결**, **연결 대상...** 을 차례로 클릭합니다.

4. Compass는 클립보드에서 연결 문자열을 자동으로 검색하며 연결하는 데 이 연결 문자열을 사용할지 묻는 메시지를 표시합니다. 아래 스크린샷과 같이 **예** 를 클릭합니다.

    :::image type="content" source="./media/connect-using-compass/mongodb-compass-detect.png" alt-text="스크린샷에 클립보드에 연결 문자열이 있음을 설명하는 대화 상자가 표시됩니다.":::

5. 위 단계에서 **예** 를 클릭하면 연결 문자열의 세부 정보가 자동으로 채워집니다. **복제본 세트 이름** 필드에서 채워진 값을 제거하여 비어 있는지 확인합니다.

    :::image type="content" source="./media/connect-using-compass/mongodb-compass-replica.png" alt-text="스크린샷에 복제본 세트 이름 텍스트 상자가 표시됩니다.":::

6. 페이지 아래쪽에서 **연결** 을 클릭합니다. 이제 Cosmos DB 계정 및 데이터베이스가 MongoDB Compass 내에 표시됩니다.

## <a name="next-steps"></a>다음 단계

- Azure Cosmos DB의 API for MongoDB와 함께 [Studio 3T를 사용](connect-using-mongochef.md)하는 방법을 알아봅니다.
- Azure Cosmos DB의 API for MongoDB를 사용하여 MongoDB [샘플](nodejs-console-app.md)을 살펴봅니다.
- Azure Cosmos DB로 마이그레이션하기 위한 용량 계획을 수행하려고 하시나요? 용량 계획을 위해 기존 데이터베이스 클러스터에 대한 정보를 사용할 수 있습니다.
    - 기존 데이터베이스 클러스터의 vCore 및 서버 수만 알고 있는 경우 [vCore 또는 vCPU를 사용하여 요청 단위 예측](../convert-vcore-to-request-unit.md)에 대해 읽어보세요. 
    - 현재 데이터베이스 워크로드에 대한 일반적인 요청 비율을 알고 있는 경우 [Azure Cosmos DB 용량 계획 도구를 사용하여 요청 단위 예측](estimate-ru-capacity-planner.md)에 대해 읽어보세요.
