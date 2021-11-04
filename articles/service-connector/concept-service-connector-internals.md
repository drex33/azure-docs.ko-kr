---
title: 서비스 커넥터 내부
description: 서비스 커넥터 내부 구조, 아키텍처, 연결 및 데이터 전송 방법에 대해 알아봅니다.
author: shizn
ms.author: xshi
ms.service: serviceconnector
ms.topic: conceptual
ms.date: 10/29/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: fda89409a39644057ebd76d949665b281d32dc2a
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131501669"
---
# <a name="service-connector-internals"></a>서비스 커넥터 내부

서비스 커넥터는 서비스 간 연결을 간편 하 게 만들고 관리 하는 방법을 제공 하는 확장 리소스 공급자입니다.
- 는 클라우드 네이티브 응용 프로그램과 함께 사용 되는 주 데이터베이스, 저장소, 실시간 서비스, 상태 및 비밀 저장소 (목록이 적극적으로 확장 됨)를 지원 합니다.
- 단일 명령이 나 몇 번의 클릭 만으로 서비스 연결을 만들어 네트워크 설정, 인증 및 연결 환경 변수 또는 속성을 관리 합니다.
- 연결의 유효성을 검사 하 고 해당 제안 사항을 찾아 서비스 연결을 수정 합니다. 

## <a name="service-connection-overview"></a>서비스 연결 개요

서비스 연결은 서비스 커넥터의 리소스 모델에 있는 주요 개념입니다. 서비스 커넥터에서 서비스 연결은 두 서비스 간의 링크를 추상화 하는 것을 나타냅니다. 다음 속성은 서비스 연결에 대해 정의 됩니다.

| 속성 | Description |
|--------|-----------|
| 연결 이름 | 서비스 연결의 고유 이름입니다.  |
| 원본 서비스 유형 | 원본 서비스는 일반적으로 Azure 계산 서비스입니다. 서비스 커넥터 기능은 이러한 Azure 계산 서비스 공급자를 확장 하 여 지원 되는 계산 서비스에서 찾을 수 있습니다.  |
| 대상 서비스 유형 | 대상 서비스는 계산 서비스와 연결 하는 서비스 또는 종속성 서비스를 지원 합니다. 서비스 커넥터는 주 데이터베이스, 저장소, 실시간 서비스, 상태 및 비밀 저장소를 비롯 한 다양 한 대상 서비스 유형을 지원 합니다. |
| 클라이언트 유형 | 클라이언트 형식은 계산 런타임 스택, 개발 프레임 워크 또는 특정 클라이언트 라이브러리 유형을 참조 하며,이는 연결 환경 변수 또는 속성의 특정 형식을 허용 합니다. |
| 인증 유형 | 서비스 연결에 사용 되는 인증 유형입니다. 순수한 비밀/연결 문자열, 관리 Id 또는 서비스 사용자 일 수 있습니다. |

인스턴스에서 여러 대상 리소스를 연결 해야 하는 경우 하나의 원본 서비스 인스턴스에서 여러 서비스 연결을 만들 수 있습니다. 그리고 동일한 대상 리소스를 여러 원본 인스턴스에서 연결할 수 있습니다. 서비스 커넥터는 원본 인스턴스의 속성에서 모든 연결을 관리 합니다. 즉, 포털 또는 원본 서비스 인스턴스의 CLI 명령에서 연결을 만들고, 가져오고, 업데이트 하 고, 삭제할 수 있습니다. 

연결에서 구독 또는 테 넌 트를 지원 합니다. 원본 및 대상 서비스는 서로 다른 구독 또는 테 넌 트에 속할 수 있습니다. 새 서비스 연결을 만들면 기본적으로 연결 리소스가 계산 서비스 인스턴스와 동일한 지역에 있습니다.

## <a name="create-or-update-a-service-connection"></a>서비스 연결 만들기 또는 업데이트

Service Connector는 다음을 포함 하 여 연결을 만들거나 업데이트 하는 동안 여러 단계를 수행 합니다.

- 대상 리소스 네트워크 및 방화벽 설정을 구성 하 여 원본 및 대상 서비스가 네트워크 수준에서 서로 통신할 수 있도록 합니다.
- 원본 리소스에 대 한 연결 정보 구성
- 필요한 경우 원본 및 대상에 대 한 인증 정보 구성
- 실패 한 경우 연결 지원 롤백을 만들거나 업데이트 합니다.

연결 만들기 및 업데이트에는 여러 단계가 포함 됩니다. 어떤 단계가 실패 한 경우 서비스 커넥터는 이전 단계를 모두 롤백하여 원본 및 대상 인스턴스의 초기 설정을 유지 합니다.

## <a name="connection-configurations"></a>연결 구성

서비스 연결이 만들어지면 연결 구성이 원본 서비스로 설정 됩니다.
포털에서 **서비스 커넥터 (미리 보기)** 페이지로 이동 합니다. 각 연결을 확장 하 고 연결 구성을 볼 수 있습니다.

:::image type="content" source="media/tutorial-java-spring-confluent-kafka/portal-list-connection-config.png" alt-text="포털 구성 나열":::

CLI에서 명령을 사용 하 여 `list-configuration` 연결 구성을 볼 수 있습니다.

```azurecli
az webapp connection list-configuration -g {webapp_rg} -n {webapp_name} --connection {service_connection_name}
```

```azurecli
az spring-cloud connection list-configuration -g {spring_cloud_rg} -n {spring_cloud_name} --connection {service_connection_name}
```

## <a name="configuration-naming-convention"></a>구성 명명 규칙

서비스 커넥터는 연결을 만들 때 구성 (환경 변수 또는 스프링 부팅 구성)을 설정 합니다. 환경 변수 키-값 쌍은 클라이언트 유형 및 인증 유형에 따라 결정 됩니다. 예를 들어 관리 되는 id와 함께 Azure SDK를 사용 하려면 클라이언트 ID, 클라이언트 암호 등이 필요 합니다. JDBC 드라이버를 사용 하려면 데이터베이스 연결 문자열이 필요 합니다. 구성의 명명 규칙은 다음과 같습니다.

클라이언트 형식으로 **스프링 부팅** 을 사용 하는 경우:

* 각 대상 서비스에 대 한 스프링 부팅 라이브러리에는 고유한 명명 규칙이 있습니다. 예: MySQL 연결 설정은 `spring.datasource.url` , `spring.datasource.username` , `spring.datasource.password` 입니다. Kafka 연결 설정은 `spring.kafka.properties.bootstrap.servers` 입니다.

스프링 부트를 제외한 **다른 클라이언트 유형을** 사용 하는 경우:

* 대상 서비스에 연결 하는 경우 첫 번째 연결 구성의 키 이름은 형식입니다 `{Cloud}_{Type}_{Name}` . 예:, `AZURE_STORAGEBLOB_RESOURCEENDPOINT` , `CONFLUENTCLOUD_KAFKA_BOOTSTRAPSERVER` . 
* 동일한 유형의 대상 리소스의 경우 두 번째 연결 구성의 키 이름은 형식으로 지정 됩니다 `{Cloud}_{Type}_{Connection Name}_{Name}` . 예:, `AZURE_STORAGEBLOB_CONN2_RESOURCEENDPOINT` , `CONFLUENTCLOUD_KAFKA_CONN2_BOOTSTRAPSERVER` .

## <a name="validate-a-service-connection"></a>서비스 연결의 유효성을 검사 합니다.
연결의 유효성을 검사 하는 동안 다음 항목이 확인 됩니다.

* 원본 및 대상 리소스의 존재 여부 확인
* 대상 리소스 네트워크 및 방화벽 설정 유효성 검사
* 원본 리소스에 대 한 연결 정보 유효성 검사
* 필요한 경우 원본 및 대상에 대 한 인증 정보 유효성 검사

## <a name="delete-connection"></a>연결 삭제

연결을 삭제할 때 원본 리소스의 연결 정보는 삭제 됩니다. 
