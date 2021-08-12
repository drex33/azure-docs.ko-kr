---
title: 포함 파일
description: 포함 파일
services: cosmos-db
author: SnehaGunda
ms.service: cosmos-db
ms.topic: include
ms.date: 04/13/2018
ms.author: sngun
ms.custom: include file
ms.openlocfilehash: 4f11826c5f4a39a6df8c1b9823c54620cb66f3ee
ms.sourcegitcommit: f3b930eeacdaebe5a5f25471bc10014a36e52e5e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/16/2021
ms.locfileid: "113223502"
---
이제 Azure Portal에서 데이터 탐색기 도구를 사용하여 그래프 데이터베이스를 만들 수 있습니다. 

1. **데이터 탐색기** > **새 그래프** 를 선택합니다.

    **그래프 추가** 영역이 맨 오른쪽에 표시되면 확인하기 위해 오른쪽으로 스크롤해야 합니다.

    ![Azure Portal 데이터 탐색기, 그래프 추가 페이지](./media/cosmos-db-create-graph/azure-cosmosdb-data-explorer-graph.png)

2. **그래프 추가** 페이지에서 새 그래프에 대한 설정을 입력합니다.

    설정|제안 값|Description
    ---|---|---
    데이터베이스 ID|sample-database|새 데이터베이스의 이름으로 *sample-database* 를 입력합니다. 데이터베이스 이름은 1~255자 사이여야 하며 `/ \ # ?` 또는 후행 공백을 포함할 수 없습니다.
    처리량|400RU|처리량을 400RU/s(초당 요청 단위)로 변경합니다. 대기 시간을 줄이면 나중에 처리량을 늘릴 수 있습니다.
    그래프 ID|sample-graph|새 컬렉션의 이름으로 *sample-graph* 를 입력입니다. 그래프 이름은 데이터베이스 ID와 동일한 문자 요구 사항을 갖습니다.
    파티션 키| /pk |모든 Cosmos DB 계정에는 수평 확장을 위한 파티션 키가 필요합니다. [그래프 데이터 분할 문서](../graph-partitioning.md)에서 적절한 파티션 키를 선택하는 방법에 대해 알아봅니다.

3. 양식을 작성한 후 **확인** 을 선택합니다.