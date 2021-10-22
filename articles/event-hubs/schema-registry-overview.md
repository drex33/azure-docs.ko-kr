---
title: Event Hubs의 Azure 스키마 레지스트리(미리 보기)
description: 이 문서에서는 Azure Event Hubs(미리 보기)의 스키마 레지스트리 지원에 대해 간략하게 설명합니다.
ms.topic: overview
ms.date: 05/10/2021
ms.custom: references_regions
ms.openlocfilehash: bde8b8c436783c4a7248c449bbee536490e2b740
ms.sourcegitcommit: 4abfec23f50a164ab4dd9db446eb778b61e22578
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2021
ms.locfileid: "130066173"
---
# <a name="azure-schema-registry-in-event-hubs-preview"></a>Event Hubs의 Azure Schema Registry(미리 보기)
많은 이벤트 스트리밍 및 메시징 시나리오에서 이벤트 또는 메시지 페이로드에는 Apache Avro와 같은 스키마 기반 형식을 사용하여 직렬화되거나 역직렬화되는 정형 데이터가 포함됩니다. 발신기와 수신기 모두에서 JSON 스키마와 마찬가지로 스키마 문서를 사용하여 데이터의 무결성을 확인하려고 할 수 있습니다. 스키마 기반 형식의 경우 메시지 소비자에서 스키마를 사용할 수 있도록 하는 것이 소비자를 통해 데이터를 역직렬화할 수 있는 필수 구성 요소입니다. 

**Azure 스키마 레지스트리** 는 스키마 문서에 대한 중앙 리포지토리를 이벤트 구동 및 메시징 중심 애플리케이션에 제공하는 Event Hubs의 기능입니다. 생산자 및 소비자 애플리케이션 간에 스키마를 관리하고 공유하지 않고도 데이터를 교환할 수 있는 유연성을 제공하며, 다른 속도로 발전할 수도 있습니다. 또한 스키마 레지스트리는 재사용 가능한 스키마에 대한 간단한 거버넌스 프레임워크를 제공하고, 그룹화 구문(스키마 그룹)을 통해 스키마 간의 관계를 정의합니다.

> [!NOTE]
> - **스키마 레지스트리** 기능은 현재 **미리 보기** 에 있으므로 프로덕션 워크로드에는 추천되지 않습니다.
> - **기본** 계층에서는 이 기능을 사용할 수 없습니다.

Apache Avro와 같은 스키마 구동 직렬화 프레임워크를 사용하면 직렬화 메타데이터를 공유 스키마로 구체화하면 JSON과 같은 태그가 지정된 형식을 사용하는 경우처럼 모든 데이터 세트에 포함된 형식 정보 및 필드 이름의 메시지당 오버헤드를 크게 줄일 수 있습니다. 이벤트와 함께 이벤트 인프라 내에 스키마를 저장하면 직렬화/역직렬화하는 데 필요한 메타데이터가 항상 가까운 곳에 있고 스키마를 잘못 배치할 수 없습니다. 

## <a name="event-hubs-namespace"></a>Event Hubs 네임스페이스
Event Hubs 네임스페이스는 이제 스키마 그룹을 이벤트 허브(또는 Kafka 항목)와 함께 호스팅할 수 있습니다. 스키마 레지스트리를 호스팅하고 여러 스키마 그룹을 포함할 수 있습니다. Azure Event Hubs에서 호스팅되는 경우에도 스키마 레지스트리는 모든 Azure 메시징 서비스 및 기타 메시지 또는 이벤트 broker와 함께 일반적으로 사용할 수 있습니다. 이러한 각 스키마 그룹은 스키마 세트에 대해 개별적으로 보안이 가능한 리포지토리입니다. 그룹은 특정 애플리케이션 또는 조직 구성 단위에 맞출 수 있습니다. 

## <a name="schema-groups"></a>스키마 그룹
스키마 그룹은 비즈니스 기준에 따라 비슷한 스키마의 논리적 그룹입니다. 스키마 그룹은 여러 버전의 스키마를 보유할 수 있습니다. 스키마 그룹의 호환성 적용 설정을 사용하면 최신 스키마 버전이 이전 버전과 호환되도록 할 수 있습니다.

그룹화 메커니즘으로 적용되는 보안 경계는 네임스페이스가 여러 파트너 간에 공유되는 상황에서 영업 비밀이 메타데이터를 통해 실수로 누출되지 않도록 하는 데 도움이 됩니다. 또한 애플리케이션 소유자는 동일한 네임스페이스를 공유하는 다른 애플리케이션과 독립적인 스키마를 관리할 수 있습니다.


## <a name="schemas"></a>스키마
스키마는 생산자와 소비자 간의 계약을 정의합니다. Event Hubs 스키마 레지스트리에 정의된 스키마는 이벤트 데이터 외부에서 계약을 관리하는 데 도움이 되므로 페이로드 오버헤드가 제거됩니다. 스키마에는 이름, 유형(예: 레코드, 배열 등), 호환성 모드(없음, 이후 버전, 이전 버전, 전체) 및 직렬화 유형(현재 Avro만 해당)이 있습니다. 여러 버전의 스키마를 만들고, 특정 버전의 스키마를 검색하여 사용할 수 있습니다. 

## <a name="client-sdks"></a>클라이언트 SDK
Avro 직렬 변환기가 포함된 다음 라이브러리 중 하나를 사용할 수 있습니다. 이를 통해 스키마 레지스트리 스키마 식별자 및 Avro 인코딩 데이터가 포함된 페이로드를 직렬화 및 역직렬화할 수 있습니다.

- [.NET - Microsoft.Azure.Data.SchemaRegistry.ApacheAvro](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/schemaregistry/Microsoft.Azure.Data.SchemaRegistry.ApacheAvro)
- [Java - azure-data-schemaregistry-avro](https://github.com/Azure/azure-sdk-for-java/tree/main/sdk/schemaregistry/azure-data-schemaregistry-apacheavro)
- [Python - azure-schemaregistry-avroserializer](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/schemaregistry/azure-schemaregistry-avroserializer)
- [JavaScript - @azure/schema-registry-avro](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/schemaregistry/schema-registry-avro)
- [Apache Kafka](https://github.com/Azure/azure-schema-registry-for-kafka/) - Azure 스키마 레지스트리에서 지원하는 Kafka 통합 Apache Avro 직렬 변환기 및 역직렬 변환기를 실행합니다. Azure 스키마 레지스트리에 대한 Java 클라이언트의 Apache Kafka 클라이언트 직렬 변환기는 모든 Apache Kafka 시나리오 및 Apache Kafka® 기반 배포 또는 클라우드 서비스에서 사용할 수 있습니다. 

다음 이미지에서는 Event Hubs를 사용하는 스키마 레지스트리의 정보 흐름을 보여 줍니다. 

:::image type="content" source="./media/schema-registry-overview/flow-diagram.png" alt-text="흐름 다이어그램":::

## <a name="limits"></a>제한
Event Hubs 제한(예: 네임스페이스의 스키마 그룹 수)은 [Event Hubs 할당량 및 제한](event-hubs-quotas.md)을 참조하세요.

## <a name="azure-role-based-access-control"></a>Azure 역할 기반 액세스 제어
스키마 레지스트리에 프로그래밍 방식으로 액세스하는 경우 애플리케이션을 Azure AD(Azure Active Directory)에 등록하고 애플리케이션의 보안 주체를 Azure RBAC(Azure 역할 기반 액세스 제어) 역할 중 하나에 추가해야 합니다.

| 역할 | Description | 
| ---- | ----------- | 
| 소유자 | 스키마 레지스트리 그룹 및 스키마를 읽고, 쓰고, 삭제합니다. |
| 참가자 | 스키마 레지스트리 그룹 및 스키마를 읽고, 쓰고, 삭제합니다. |
| [스키마 레지스트리 판독기(미리 보기)](../role-based-access-control/built-in-roles.md#schema-registry-reader-preview) | 스키마 레지스트리 그룹 및 스키마를 읽고 나열합니다. |
| [스키마 레지스트리 기여자(미리 보기)](../role-based-access-control/built-in-roles.md#schema-registry-reader-preview) | 스키마 레지스트리 그룹 및 스키마를 읽고, 쓰고, 삭제합니다. |

Azure Portal을 사용하여 애플리케이션 등록을 만드는 방법에 대한 지침은 [Azure AD에 앱 등록](../active-directory/develop/quickstart-register-app.md)을 참조하세요. 코드에서 사용할 클라이언트 ID(애플리케이션 ID), 테넌트 ID 및 비밀을 적어 둡니다. 

## <a name="next-steps"></a>다음 단계

- Azure Portal을 사용하여 스키마 레지스트리를 만드는 방법에 대한 자세한 내용은 [Azure Portal을 사용하여 Event Hubs 스키마 레지스트리 만들기](create-schema-registry.md)를 참조하세요.
- 다음 **스키마 레지스트리 Avro 클라이언트 라이브러리** 샘플을 참조하세요.
    - [.NET](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/schemaregistry/Microsoft.Azure.Data.SchemaRegistry.ApacheAvro/tests/Samples)
    - [Java](https://github.com/Azure/azure-sdk-for-java/tree/main/sdk/schemaregistry/azure-data-schemaregistry-apacheavro/src/samples)
    - [JavaScript](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/schemaregistry/schema-registry-avro/samples )
    - [Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/schemaregistry/azure-schemaregistry-avroserializer/samples )
    - [Azure 스키마 레지스트리용 Kafka Avro 통합](https://github.com/Azure/azure-schema-registry-for-kafka/tree/master/csharp/avro/samples)
