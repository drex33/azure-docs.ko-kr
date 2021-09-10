---
title: Confluent 클라우드 - Azure 파트너 솔루션을 위한 커넥터를 추가합니다.
description: 이 문서에서는 Azure 리소스에서 사용하는 Confluent 클라우드용 커넥터를 설치하는 방법을 설명 합니다.
ms.service: partner-services
ms.topic: conceptual
ms.date: 06/07/2021
author: tfitzmac
ms.author: tomfitz
ms.openlocfilehash: c4a15a54c5b9fb3b73a3fcaf81d63a9e176b49c5
ms.sourcegitcommit: 096e7972e2a1144348f8d648f7ae66154f0d4b39
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/23/2021
ms.locfileid: "112534890"
---
# <a name="add-connectors-for-confluent-cloud"></a>Confluent 클라우드에 커넥터를 추가합니다.

이 문서에서는 Confluent 클라우드용 커넥터를 Azure 리소스에 설치하는 방법을 설명합니다.

## <a name="connector-to-azure-cosmos-db"></a>Azure Cosmos DB 커넥터

[Confluent 허브 목록](https://www.confluent.io/hub/microsoftcorporation/kafka-connect-cosmos)이 권장하는 대로 Confluent Hub 클라이언트 내에 Cosmos DB 커넥터를 설치합니다. 

커넥터를 수동으로 설치하려면 먼저 [Cosmos DB 릴리스 페이지](https://github.com/microsoft/kafka-connect-cosmosdb/releases)에서 uber JAR를 다운로드합니다. 또는 [소스 코드에서 직접 uber JAR를 빌드할](https://github.com/microsoft/kafka-connect-cosmosdb/blob/dev/doc/README_Sink.md#install-sink-connector)수 있습니다. [커넥터를 수동으로 설치](https://docs.confluent.io/home/connect/install.html#install-connector-manually)하는 방법에 관한 Confluent 설명서 참고 자료에 따라 설치를 완료합니다.  

## <a name="next-steps"></a>다음 단계

문제 해결에 도움이 필요하면 [Confluent Cloud 솔루션용 Apache Kafka 문제 해결](troubleshoot.md)을 참조하세요.
