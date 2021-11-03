---
title: 서비스 커넥터 내부
description: Service Connector 내부, 아키텍처, 연결 및 데이터 전송 방법에 대해 알아봅니다.
author: shizn
ms.author: xshi
ms.service: serviceconnector
ms.topic: conceptual
ms.date: 10/29/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 99c793fb5b9b249acd440669a01e50f14c7b6de5
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131052890"
---
# <a name="service-connector-internals"></a>서비스 커넥터 내부

서비스 커넥터는 서비스 간의 연결을 쉽게 만들고 관리하는 방법을 제공하는 확장 리소스 공급자입니다.
- 클라우드 네이티브 애플리케이션과 함께 사용되는 주요 데이터베이스, 스토리지, 실시간 서비스, 상태 및 비밀 저장소를 지원합니다(목록은 현재 확장 중임).
- 네트워크 설정, 인증을 구성하고, 단 한 번의 명령 또는 몇 번의 클릭으로 서비스 연결을 만들어 연결 환경 변수 또는 속성을 관리합니다.
- 연결의 유효성을 검사하고 서비스 연결을 수정하기 위한 해당 제안을 찾습니다. 

## <a name="service-connection-overview"></a>서비스 연결 개요

서비스 연결은 서비스 커넥터의 리소스 모델에서 핵심 개념입니다. 서비스 커넥터에서 서비스 연결은 두 서비스 간의 링크에 대한 추상화입니다. 서비스 연결에 다음 속성이 정의되어 있습니다.

| 속성 | Description |
|--------|-----------|
| 연결 이름 | 서비스 연결의 고유한 이름입니다.  |
| 원본 서비스 유형 | 원본 서비스는 일반적으로 Azure 컴퓨팅 서비스입니다. 서비스 커넥터 기능은 이러한 Azure 컴퓨팅 서비스 공급자를 확장하여 지원되는 컴퓨팅 서비스에서 찾을 수 있습니다.  |
| 대상 서비스 유형 | 대상 서비스는 컴퓨팅 서비스가 연결하는 지원 서비스 또는 종속성 서비스입니다. Service Connector는 주요 데이터베이스, 스토리지, 실시간 서비스, 상태 및 비밀 저장소를 비롯한 다양한 대상 서비스 유형을 지원합니다. |
| 클라이언트 유형 | 클라이언트 유형은 연결 환경 변수 또는 속성의 특정 형식을 허용하는 컴퓨팅 런타임 스택, 개발 프레임워크 또는 특정 클라이언트 라이브러리 형식을 나타냅니다. |
| 인증 유형 | 서비스 연결에 사용되는 인증 유형입니다. 순수 비밀/연결 문자열, 관리 ID 또는 서비스 주체일 수 있습니다. |

인스턴스에서 여러 대상 리소스를 연결해야 하는 경우 하나의 원본 서비스 인스턴스에서 여러 서비스 연결을 만들 수 있습니다. 또한 동일한 대상 리소스를 여러 원본 인스턴스에서 연결할 수 있습니다. 서비스 커넥터는 원본 인스턴스의 속성에 있는 모든 연결을 관리합니다. 즉, 포털 또는 해당 원본 서비스 인스턴스의 CLI 명령에서 연결을 만들고, 얻고, 업데이트하고, 삭제할 수 있습니다. 

연결은 구독 간 또는 테넌트 간을 지원합니다. 원본 및 대상 서비스는 서로 다른 구독 또는 테넌트에서 속할 수 있습니다. 새 서비스 연결을 만들 때 연결 리소스는 기본적으로 컴퓨팅 서비스 인스턴스와 동일한 지역에 있습니다.

## <a name="create-or-update-a-service-connection"></a>서비스 연결 만들기 또는 업데이트

서비스 커넥터는 다음을 포함하여 연결을 만들거나 업데이트하는 동안 여러 단계를 수행합니다.

- 대상 리소스 네트워크 및 방화벽 설정을 구성하여 원본 및 대상 서비스가 네트워크 수준에서 서로 대화할 수 있도록 합니다.
- 원본 리소스에 대한 연결 정보 구성
- 필요한 경우 원본 및 대상에 대한 인증 정보 구성
- 실패할 경우 연결 지원 롤백을 만들거나 업데이트합니다.

연결 만들기 및 업데이트에는 여러 단계가 포함됩니다. 단계가 실패한 경우 Service Connector는 초기 설정을 원본 및 대상 인스턴스에 유지하기 위해 모든 이전 단계를 롤백합니다.

## <a name="connection-configurations"></a>연결 구성

서비스 연결이 만들어지면 연결 구성이 원본 서비스로 설정됩니다.
포털에서 서비스 **커넥터(미리 보기)** 페이지로 이동합니다. 각 연결을 확장하고 연결 구성을 볼 수 있습니다.

:::image type="content" source="media/tutorial-java-spring-confluent-kafka/portal-list-connection-config.png" alt-text="포털 구성 나열":::

CLI에서 명령을 사용하여 연결 구성을 볼 수 `list-configuration` 있습니다.

```azurecli
az webapp connection list-configuration -g {webapp_rg} -n {webapp_name} --connection {service_connection_name}
```

```azurecli
az spring-cloud connection list-configuration -g {spring_cloud_rg} -n {spring_cloud_name} --connection {service_connection_name}
```

## <a name="configuration-naming-convention"></a>구성 명명 규칙

서비스 커넥터는 연결을 만들 때 구성(환경 변수 또는 Spring Boot 구성)을 설정합니다. 환경 변수 키-값 쌍은 클라이언트 유형 및 인증 유형에 따라 결정됩니다. 예를 들어 관리 ID와 함께 Azure SDK를 사용하려면 클라이언트 ID, 클라이언트 암호 등이 필요합니다. JDBC 드라이버를 사용하려면 데이터베이스 연결 문자열이 필요합니다. 구성의 명명 규칙은 다음과 같습니다.

클라이언트 유형으로 **Spring Boot** 사용하는 경우:

* 각 대상 서비스에 대한 Spring Boot 라이브러리에는 고유한 명명 규칙이 있습니다. 예를 들어 MySQL 연결 설정은 `spring.datasource.url` , `spring.datasource.username` , `spring.datasource.password` 입니다. Kafka 연결 설정은 `spring.kafka.properties.bootstrap.servers` 입니다.

Spring Boot 제외한 **다른 클라이언트 형식을** 사용하는 경우:

* 대상 서비스에 연결할 때 첫 번째 연결 구성의 키 이름은 `{Cloud}_{Type}_{Name}` 형식입니다. 예: , `AZURE_STORAGEBLOB_RESOURCEENDPOINT` `CONFLUENTCLOUD_KAFKA_BOOTSTRAPSERVER` . 
* 동일한 유형의 대상 리소스에 대해 t 두 번째 연결 구성의 키 이름은 `{Cloud}_{Type}_{Connection Name}_{Name}` 형식입니다. 예: , `AZURE_STORAGEBLOB_CONN2_RESOURCEENDPOINT` `CONFLUENTCLOUD_KAFKA_CONN2_BOOTSTRAPSERVER` .

## <a name="validate-a-service-connection"></a>서비스 연결 유효성 검사
연결의 유효성을 검사하는 동안 다음 항목을 확인합니다.

* 원본 및 대상 리소스가 있는지 확인
* 대상 리소스 네트워크 및 방화벽 설정 유효성 검사
* 원본 리소스에 대한 연결 정보 유효성 검사
* 필요한 경우 원본 및 대상에 대한 인증 정보 유효성 검사

## <a name="delete-connection"></a>연결 삭제

원본 리소스에 대한 연결 정보는 연결을 삭제할 때 삭제됩니다. 
