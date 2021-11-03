---
title: Service Connector를 사용 하 여 Confluent Cloud에서 Apache kafka 통합
description: 서비스 커넥터를 사용 하 여 응용 프로그램에 Apache kafka Confluent Cloud 통합
author: shizn
ms.author: xshi
ms.service: serviceconnector
ms.topic: how-to
ms.date: 10/29/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: c0b5c47982fb0b3af7b2ecac6fde12f1e2f2f101
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131052888"
---
# <a name="integrate-apache-kafka-on-confluent-cloud-with-service-connector"></a>Service Connector를 사용 하 여 Confluent Cloud에서 Apache kafka 통합

이 페이지에서는 Service Connector를 사용 하는 서비스를 사용 하 여 Confluent Cloud의 Apache kafka에 대해 지원 되는 인증 유형 및 클라이언트 유형을 보여 줍니다. 서비스 커넥터를 사용 하지 않고 다른 프로그래밍 언어로 Confluent 클라우드의 Apache kafka에 계속 연결할 수 있습니다. 이 페이지에는 서비스 연결을 만들 때 제공 되는 기본 환경 변수 이름 및 값 (또는 스프링 부팅 구성)도 표시 됩니다. [서비스 커넥터 환경 변수 명명 규칙](concept-service-connector-internals.md)에 대해 자세히 알아볼 수 있습니다.

## <a name="supported-compute-service"></a>지원 되는 계산 서비스

- Azure App Service
- Azure Spring Cloud

## <a name="supported-authentication-types-and-client-types"></a>지원 되는 인증 유형 및 클라이언트 유형

| 클라이언트 유형 | 시스템이 할당한 관리 ID | 사용자 할당 관리 Id | 비밀/ConnectionString | 서비스 주체 |
| --- | --- | --- | --- | --- |
| .Net | | | ![예 아이콘](./media/green-check.png) | |
| Java | | | ![예 아이콘](./media/green-check.png) | |
| Java-스프링 부팅 | | | ![예 아이콘](./media/green-check.png) | |
| Node.js | | | ![예 아이콘](./media/green-check.png) | |
| Python | | | ![예 아이콘](./media/green-check.png) | |

## <a name="default-environment-variable-names-or-application-properties"></a>기본 환경 변수 이름 또는 응용 프로그램 속성

### <a name="net-java-nodejs-and-python"></a>.NET, Java, Node.JS 및 Python

| 기본 환경 변수 이름 | 설명 | 예제 값 |
| --- | --- | --- |
| AZURE_CONFLUENTCLOUDKAFKA_BOOTSTRAPSERVER | Kafka 부트스트랩 서버 | `pkc-{serverName}.eastus.azure.confluent.cloud:9092` |
| AZURE_CONFLUENTCLOUDKAFKA_KAFKASASLCONFIG | Kafka SASL 구성 | `org.apache.kafka.common.security.plain.PlainLoginModule required username='{bootstrapServerKey}' password='{bootstrapServerSecret}';` |
| AZURE_CONFLUENTCLOUDSCHEMAREGISTRY_URL | Confluent 레지스트리 URL | `https://psrc-{serverName}.westus2.azure.confluent.cloud` |
| AZURE_CONFLUENTCLOUDSCHEMAREGISTRY_USERINFO |  Confluent 레지스트리 사용자 정보 | `{schemaRegistryKey} + ":" + {schemaRegistrySecret}` |

### <a name="spring-boot"></a>Spring Boot

| 기본 환경 변수 이름 | 설명 | 예제 값 |
| --- | --- | --- |
| 스프링 .kaf. | Kafka 부트스트랩 서버 | `pkc-{serverName}.eastus.azure.confluent.cloud:9092` |
| spring.kafka.properties.sasl.jaas.config | Kafka SASL 구성 | `org.apache.kafka.common.security.plain.PlainLoginModule required username='{bootstrapServerKey}' password='{bootstrapServerSecret}';` |
| 스프링. .aaf. schema. registry. url | Confluent 레지스트리 URL | `https://psrc-{serverName}.westus2.azure.confluent.cloud` |
| spring.kafka.properties.schema.registry.basic.auth.user.info | Confluent 레지스트리 사용자 정보 | `{schemaRegistryKey} + ":" + {schemaRegistrySecret}` |

## <a name="next-steps"></a>다음 단계

서비스 커넥터에 대해 자세히 알아보려면 아래 나열된 자습서를 따르세요.

> [!div class="nextstepaction"]
> [서비스 커넥터 개념에 대해 알아보기](./concept-service-connector-internals.md)
