---
title: Confluent 클라우드 - Azure 파트너 솔루션을 위한 커넥터를 추가합니다.
description: 이 문서에서는 Azure 리소스에서 사용하는 Confluent 클라우드용 커넥터를 설치하는 방법을 설명 합니다.
ms.service: partner-services
ms.topic: conceptual
ms.date: 08/31/2021
author: tfitzmac
ms.author: tomfitz
ms.openlocfilehash: d37f34005dd2b4774828e79006d33d7a398cbc07
ms.sourcegitcommit: 851b75d0936bc7c2f8ada72834cb2d15779aeb69
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/31/2021
ms.locfileid: "123315907"
---
# <a name="add-connectors-for-confluent-cloud"></a>Confluent 클라우드에 커넥터를 추가합니다.

이 문서에서는 Confluent 클라우드용 커넥터를 Azure 리소스에 설치하는 방법을 설명합니다.

## <a name="connector-to-azure-cosmos-db"></a>Azure Cosmos DB 커넥터

Azure Cosmos DB 싱크 커넥터 완전 관리형 커넥터는 일반적으로 Confluent Cloud 내에서 사용할 수 있습니다. 완전 관리형 커넥터를 사용하면 사용자 지정 통합을 개발 및 관리하지 않아도 되며, Confluent Cloud와 Azure Cosmos DB 간에 데이터를 연결하는 전반적인 운영 부담을 줄일 수 있습니다. Confluent Cloud용 Microsoft Azure Cosmos 싱크 커넥터는 Microsoft Azure Cosmos 데이터베이스에서 데이터를 읽고 씁니다. 커넥터는 Kafka의 데이터를 폴링하고 데이터베이스 컨테이너에 씁니다.

커넥터를 설정하려면 [Confluent Cloud용 Azure Cosmos DB 싱크 커넥터](https://docs.confluent.io/cloud/current/connectors/cc-azure-cosmos-sink.html)를 참조하세요.

커넥터를 수동으로 설치하려면 먼저 [Cosmos DB 릴리스 페이지](https://github.com/microsoft/kafka-connect-cosmosdb/releases)에서 uber JAR를 다운로드합니다. 또는 [소스 코드에서 직접 uber JAR를 빌드](https://github.com/microsoft/kafka-connect-cosmosdb/blob/dev/doc/README_Sink.md#install-sink-connector)할 수 있습니다. [커넥터를 수동으로 설치](https://docs.confluent.io/home/connect/install.html#install-connector-manually)하는 방법에 관한 Confluent 설명서 참고 자료에 따라 설치를 완료합니다.  

## <a name="next-steps"></a>다음 단계

문제 해결에 도움이 필요하면 [Confluent Cloud 솔루션용 Apache Kafka 문제 해결](troubleshoot.md)을 참조하세요.
