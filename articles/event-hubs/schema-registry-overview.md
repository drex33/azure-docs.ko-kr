---
title: Azure Event Hubs의 Azure 스키마 레지스트리
description: 이 문서에서는 Azure Event Hubs의 스키마 레지스트리 지원에 대해 간략하게 설명합니다.
ms.topic: overview
ms.date: 11/02/2021
ms.custom: references_regions, ignite-fall-2021
ms.openlocfilehash: f9e405a82b73530cc53ec98f22b2c1f4473b2550
ms.sourcegitcommit: e1037fa0082931f3f0039b9a2761861b632e986d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/12/2021
ms.locfileid: "132398522"
---
# <a name="azure-schema-registry-in-azure-event-hubs"></a>Azure Event Hubs의 Azure 스키마 레지스트리
많은 이벤트 스트리밍 및 메시징 시나리오에서 이벤트 또는 메시지 페이로드는 구조화된 데이터를 포함합니다. [Apache Avro](https://avro.apache.org/)와 같은 스키마 기반 형식은 종종 이러한 구조화된 데이터를 직렬화하거나 역직렬화하는 데 사용됩니다. 

:::image type="content" source="./media/schema-registry-overview/schema-driven-ser-de.svg" alt-text="스키마 기반 직렬화/역직렬화":::

생산자 애플리케이션은 스키마 문서를 사용하여 이벤트 페이로드를 직렬화하고 Event Hubs와 같은 이벤트 브로커에 게시합니다. 마찬가지로 소비자 애플리케이션은 브로커에서 이벤트 페이로드를 읽고 동일한 스키마 문서를 사용하여 역직렬화합니다. 따라서 생산자와 소비자는 스키마 문서를 사용하여 데이터 무결성의 유효성을 검사할 수 있습니다. 


## <a name="why-we-need-a-schema-registry"></a>스키마 레지스트리가 필요한 이유는 무엇인가요? 
스키마 기반 형식을 사용하는 경우 생산자 애플리케이션은 게시된 이벤트의 스키마를 소비자가 사용할 수 있도록 만듭니다. 각각의 이벤트 데이터와 해당 스키마를 공유할 수는 있지만 이는 비효율적입니다. 새 소비자가 이벤트 데이터를 사용 하려는 경우 게시되는 데이터의 형식을 이해하는 방법이 필요합니다. 또한 생산자와 소비자가 서로 다른 속도로 발전할 수 있도록 스키마 진화를 지원하는지 확인해야 합니다. 


## <a name="azure-schema-registry"></a>Azure Schema Registry
**Azure 스키마 레지스트리** 는 스키마 문서에 대한 중앙 리포지토리를 이벤트 구동 및 메시징 중심 애플리케이션에 제공하는 Event Hubs의 기능입니다. 스키마를 관리하고 공유하지 않고도 생산자 및 소비자 애플리케이션에서 데이터를 교환할 수 있는 유연성을 제공합니다. 또한 스키마 레지스트리는 재사용 가능한 스키마에 대한 간단한 거버넌스 프레임워크를 제공하고, 그룹화 구문(스키마 그룹)을 통해 스키마 간의 관계를 정의합니다.

:::image type="content" source="./media/schema-registry-overview/schema-registry.svg" alt-text="스키마 레지스트리":::

Apache Avro와 같은 스키마 구동 직렬화 프레임워크를 사용하면 직렬화 메타데이터를 공유 스키마로 구체화하면 JSON과 같은 태그가 지정된 형식을 사용하는 경우처럼 모든 데이터 세트에 포함된 형식 정보 및 필드 이름의 메시지당 오버헤드를 크게 줄일 수 있습니다. 이벤트와 함께 이벤트 인프라 내에 스키마를 저장하면 직렬화/역직렬화하는 데 필요한 메타데이터가 항상 가까운 곳에 있고 스키마를 잘못 배치할 수 없습니다. 

> [!NOTE]
> **기본** 계층에서는 이 기능을 사용할 수 없습니다.

## <a name="schema-registry-information-flow"></a>스키마 레지스트리 정보 흐름 
스키마 레지스트리를 사용하는 경우 정보 흐름은 Azure Event Hubs에서 이벤트를 게시하거나 소비하는 데 사용하는 모든 프로토콜에 대해 동일합니다. 다음 다이어그램에서는 사용자 스키마 레지스트리를 나타내는 Kafka 이벤트 생산자 및 소비자 시나리오의 정보 흐름을 보여 줍니다. 

:::image type="content" source="./media/schema-registry-overview/information-flow.svg" lightbox="./media/schema-registry-overview/information-flow.svg" alt-text="스키마 레지스트리 정보 흐름을 보여주는 이미지.":::


정보 흐름은 스키마 문서를 사용하 여 Kafka 생산자가 데이터를 직렬화하는 생산자 쪽에서 시작 됩니다. 
- Kafka 생산자 애플리케이션은 ``KafkaAvroSerializer``클라이언트 쪽에서 지정된 스키마를 사용하여 이벤트 데이터를 직렬화하는 데 사용합니다. 
- 생산자 애플리케이션은 스키마 레지스트리 엔드포인트 및 스키마 유효성 검사에 필요한 기타 선택적 매개 변수의 세부 정보를 제공해야 합니다. 
- 직렬 변환기는 생산자가 이벤트 데이터를 직렬화하는 데 사용하는 스키마 콘텐츠를 사용하여 스키마 레지스트리에서 조회합니다. 
- 이러한 스키마가 발견되면 해당 스키마 ID가 반환됩니다. 생산자 애플리케이션을 구성할 수 있습니다. 스키마가 없는 경우 생산자 애플리케이션은 스키마 레지스트리 클라이언트에서 스키마를 자동으로 등록하도록 구성할 수 있습니다. 
- 그런 다음 직렬 변환기는 해당 스키마 ID를 사용하고 이를 Event Hubs에 게시된 직렬화된 데이터에 앞에 추가할 수 있습니다. 
- ``KafkaAvroDeserializer``은(는) 소비자 쪽에서 스키마 ID를 사용하여 스키마 레지스트리에서 스키마 콘텐츠를 검색합니다. 
- 그러면 직렬 변환기에서 스키마 콘텐츠를 사용하여 이벤트 허브에서 읽은 이벤트 데이터를 역직렬화합니다. 
- 스키마 레지스트리 클라이언트는 캐싱을 사용하여 중복 스키마 레지스트리 조회를 방지합니다.  


## <a name="schema-registry-elements"></a>스키마 레지스트리 요소

Event Hubs 네임스페이스는 이제 스키마 그룹을 이벤트 허브(또는 Kafka 항목)와 함께 호스팅할 수 있습니다. 스키마 레지스트리를 호스팅하고 여러 스키마 그룹을 포함할 수 있습니다. Azure Event Hubs에서 호스팅되는 경우에도 스키마 레지스트리는 모든 Azure 메시징 서비스 및 기타 메시지 또는 이벤트 broker와 함께 일반적으로 사용할 수 있습니다. 이러한 각 스키마 그룹은 스키마 세트에 대해 개별적으로 보안이 가능한 리포지토리입니다. 그룹은 특정 애플리케이션 또는 조직 구성 단위에 맞출 수 있습니다. 

:::image type="content" source="./media/schema-registry-overview/elements.png" alt-text="스키마 레지스트리 요소를 표시 하는 이미지.":::


### <a name="schema-groups"></a>스키마 그룹
스키마 그룹은 비즈니스 기준에 따라 비슷한 스키마의 논리적 그룹입니다. 스키마 그룹은 여러 버전의 스키마를 보유할 수 있습니다. 스키마 그룹의 호환성 적용 설정을 사용하면 최신 스키마 버전이 이전 버전과 호환되도록 할 수 있습니다.

그룹화 메커니즘으로 적용되는 보안 경계는 네임스페이스가 여러 파트너 간에 공유되는 상황에서 영업 비밀이 메타데이터를 통해 실수로 누출되지 않도록 하는 데 도움이 됩니다. 또한 애플리케이션 소유자는 동일한 네임스페이스를 공유하는 다른 애플리케이션과 독립적인 스키마를 관리할 수 있습니다.

### <a name="schemas"></a>스키마
스키마는 생산자와 소비자 간의 계약을 정의합니다. Event Hubs 스키마 레지스트리에 정의된 스키마는 이벤트 데이터 외부에서 계약을 관리하는 데 도움이 되므로 페이로드 오버헤드가 제거됩니다. 스키마에는 이름, 유형(예: 레코드, 배열 등), 호환성 모드(없음, 이후 버전, 이전 버전, 전체) 및 직렬화 유형(현재 Avro만 해당)이 있습니다. 여러 버전의 스키마를 만들고, 특정 버전의 스키마를 검색하여 사용할 수 있습니다. 

## <a name="schema-evolution"></a>스키마 진화 

스키마는 생산자 및 소비자의 비즈니스 요구 사항에 따라 진화해야 합니다. Azure 스키마 레지스트리는 스키마 그룹 수준에서 호환성 모드를 도입하여 스키마 진화를 지원합니다. 스키마 그룹을 만들 때 해당 스키마 그룹에 포함하는 스키마의 호환성 모드를 지정할 수 있습니다. 스키마를 업데이트 하는 경우 변경 내용은 할당된 호환성 모드를 준수해야 하며, 이 경우에만 스키마의 새 버전을 만듭니다. 

Event Hubs에 대한 Azure 스키마 레지스트리는 다음과 같은 호환성 모드를 지원합니다. 

### <a name="backward-compatibility"></a>이전 버전과의 호환성
이전 버전과의 호환성 모드에서는 소비자 코드가 새 버전의 스키마를 사용할 수 있지만, 이전 버전의 스키마를 사용하여 메시지를 처리할 수도 있습니다. 스키마 그룹에서 이전 버전과의 호환성 모드를 사용하면 스키마에 대해 다음과 같은 변경 작업을 수행할 수 있습니다. 

- 필드 삭제 
- 선택적 필드 추가 


### <a name="forward-compatibility"></a>다음 버전과의 호환성
다음 버전과의 호환성 모드에서는 소비자 코드가 이전 버전의 스키마를 사용할 수 있지만 새 스키마를 사용하여 메시지를 읽을 수도 있습니다. 다음 버전과의 호환성 모드를 사용 하면 스키마에 대해 다음과 같은 변경 작업을 수행할 수 있습니다. 
- 필드 추가하기 
- 선택적 필드 삭제 


### <a name="no-compatibility"></a>호환성 없음
``None`` 호환성 모드를 사용하면 스키마를 업데이트할 때 스키마 레지스트리가 호환성 검사를 수행 하지 않습니다. 

## <a name="client-sdks"></a>클라이언트 SDK

다음 라이브러리 중 하나를 사용하 여 Avro 직렬 변환기를 포함할 수 있습니다. 이는 스키마 레지스트리 스키마 식별자 및 Avro로 인코딩된 데이터를 포함하는 페이로드를 직렬화 및 역직렬화하는 데 사용할 수 있습니다.

- [.NET - Microsoft.Azure.Data.SchemaRegistry.ApacheAvro](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/schemaregistry/Microsoft.Azure.Data.SchemaRegistry.ApacheAvro)
- [Java - azure-data-schemaregistry-avro](https://github.com/Azure/azure-sdk-for-java/tree/main/sdk/schemaregistry/azure-data-schemaregistry-apacheavro)
- [Python - azure-schemaregistry-avroserializer](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/schemaregistry/azure-schemaregistry-avroserializer)
- [JavaScript - @azure/schema-registry-avro](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/schemaregistry/schema-registry-avro)
- [Apache Kafka](https://github.com/Azure/azure-schema-registry-for-kafka/) - Azure 스키마 레지스트리에서 지원하는 Kafka 통합 Apache Avro 직렬 변환기 및 역직렬 변환기를 실행합니다. Azure 스키마 레지스트리에 대한 Java 클라이언트의 Apache Kafka 클라이언트 직렬 변환기는 모든 Apache Kafka 시나리오 및 Apache Kafka® 기반 배포 또는 클라우드 서비스에서 사용할 수 있습니다. 

## <a name="limits"></a>제한
Event Hubs 제한(예: 네임스페이스의 스키마 그룹 수)은 [Event Hubs 할당량 및 제한](event-hubs-quotas.md)을 참조하세요.

## <a name="azure-role-based-access-control"></a>Azure 역할 기반 액세스 제어
스키마 레지스트리에 프로그래밍 방식으로 액세스하는 경우 애플리케이션을 Azure AD(Azure Active Directory)에 등록하고 애플리케이션의 보안 주체를 Azure RBAC(Azure 역할 기반 액세스 제어) 역할 중 하나에 추가해야 합니다.

| 역할 | Description | 
| ---- | ----------- | 
| 소유자 | 스키마 레지스트리 그룹 및 스키마를 읽고, 쓰고, 삭제합니다. |
| 참가자 | 스키마 레지스트리 그룹 및 스키마를 읽고, 쓰고, 삭제합니다. |
| [스키마 레지스트리 판독기](../role-based-access-control/built-in-roles.md#schema-registry-reader-preview) | 스키마 레지스트리 그룹 및 스키마를 읽고 나열합니다. |
| [스키마 레지스트리 기여자](../role-based-access-control/built-in-roles.md#schema-registry-reader-preview) | 스키마 레지스트리 그룹 및 스키마를 읽고, 쓰고, 삭제합니다. |

Azure Portal을 사용하여 애플리케이션 등록을 만드는 방법에 대한 지침은 [Azure AD에 앱 등록](../active-directory/develop/quickstart-register-app.md)을 참조하세요. 코드에서 사용할 클라이언트 ID(애플리케이션 ID), 테넌트 ID 및 비밀을 적어 둡니다. 

## <a name="next-steps"></a>다음 단계

- Azure Portal을 사용하여 스키마 레지스트리를 만드는 방법에 대한 자세한 내용은 [Azure Portal을 사용하여 Event Hubs 스키마 레지스트리 만들기](create-schema-registry.md)를 참조하세요.
- 다음 **스키마 레지스트리 Avro 클라이언트 라이브러리** 샘플을 참조하세요.
    - [.NET](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/schemaregistry/Microsoft.Azure.Data.SchemaRegistry.ApacheAvro/tests/Samples)
    - [Java](https://github.com/Azure/azure-sdk-for-java/tree/main/sdk/schemaregistry/azure-data-schemaregistry-apacheavro/src/samples)
    - [JavaScript](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/schemaregistry/schema-registry-avro/samples )
    - [Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/schemaregistry/azure-schemaregistry-avroserializer/samples )
    - [Azure 스키마 레지스트리용 Kafka Avro 통합](https://github.com/Azure/azure-schema-registry-for-kafka/tree/master/csharp/avro/samples)
