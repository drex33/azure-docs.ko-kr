---
title: Azure Service Bus 토픽 필터 | Microsoft Docs
description: 이 문서에서는 구독자가 필터를 지정하여 토픽에서 수신하려는 메시지를 정의하는 방법을 설명합니다.
ms.topic: conceptual
ms.date: 07/19/2021
ms.openlocfilehash: 310393456b21c43fe6d0665fad9e2f505045253c
ms.sourcegitcommit: 7854045df93e28949e79765a638ec86f83d28ebc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2021
ms.locfileid: "122867087"
---
# <a name="topic-filters-and-actions"></a>토픽 필터 및 작업

구독자는 토픽에서 수신할 메시지를 정의할 수 있습니다. 이러한 메시지는 하나 이상의 명명된 구독 규칙의 형태로 지정됩니다. 각 규칙은 특정 메시지를 선택하는 **필터 조건** 으로 구성되며, **필요에 따라** 선택된 메시지에 주석을 다는 **작업** 을 포함합니다. 

**작업을 포함하지 않는** 모든 규칙은 `OR` 조건을 통해 결합되며 여러 일치 규칙이 있는 경우에도 구독에서 **단일 메시지** 를 생성합니다. 

**작업을 포함하는** 각 규칙은 메시지의 복사본을 생성합니다. 이 메시지에는 값이 해당 일치 규칙의 이름인 `RuleName`라는 속성이 있습니다. 작업은 속성을 추가 또는 업데이트하거나 원래 메시지에서 속성을 삭제하여 구독에 메시지를 생성하는 것일 수 있습니다. 

다음 시나리오를 고려하세요.

- 구독에는 5개의 규칙이 있습니다.
- 두 규칙에는 작업이 포함됩니다.
- 세 가지 규칙에는 작업이 포함되지 않습니다.

이 예시에서는 5개의 규칙과 모두 일치하는 메시지를 하나 보내면 구독에 3개의 메시지가 수신됩니다. 이는 작업을 포함하는 두 가지 규칙에 대한 2개의 메시지이며 작업을 포함하지 않는 세 가지 규칙에 대한 1개의 메시지입니다. 

새로 생성된 각각의 토픽 구독에는 초기 기본 구독 규칙이 있습니다. 규칙에 대한 필터 조건을 명시적으로 지정하지 않는 경우 적용된 필터는 **true** 필터이며 이를 통해 모든 메시지를 구독으로 선택할 수 있습니다. 기본 규칙에는 연결된 주석 작업이 없습니다.

## <a name="filters"></a>필터
Service Bus는 세 가지 필터 조건을 지원합니다.

-   *SQL 필터* - **SqlFilter** 는 broker에서 도착 메시지의 사용자 정의 속성 및 시스템 속성과 비교하여 평가되는 SQL과 비슷한 조건식을 유지합니다. 모든 시스템 속성은 조건식에서 `sys.`로 시작되어야 합니다. [필터 조건용 SQL 언어 하위 집합](service-bus-messaging-sql-filter.md)은 속성의 존재 여부(`EXISTS`), Null 값(`IS NULL`), 논리적 NOT/AND/OR, 관계 연산자, 단순 숫자 산술, `LIKE`와 일치하는 간단한 텍스트 패턴을 테스트합니다.
-   *부울 필터* - **TrueFilter** 및 **FalseFilter** 는 모든 도착 메시지가 구독에 대해 선택되거나(**true**) 선택되지 않도록(**false**) 합니다. 이러한 두 필터는 SQL 필터에서 파생됩니다. 
-   *상관 관계 필터* - **CorrelationFilter** 는 도착 메시지의 사용자 및 시스템 속성 중 하나 이상과 일치하는 조건 집합을 보유합니다. 일반적인 사용법은 **CorrelationId** 속성과 일치시키는 것이지만 애플리케이션은 다음 속성과 일치하도록 선택할 수도 있습니다.

    - **ContentType**
     - **레이블**
     - **MessageId**
     - **ReplyTo**
     - **ReplyToSessionId**
     - **SessionId** 
     - **수행할 작업**
     - 사용자 정의 속성입니다. 
     
     속성에 대한 도착 메시지의 값이 상관 필터에 지정된 값과 같을 때 일치가 존재합니다. 문자열 식의 경우 비교에서 대/소문자가 구분됩니다. 여러 일치 속성을 지정할 때 필터는 이들을 논리적 AND 조건으로 결합합니다. 즉, 필터가 일치하려면 모든 조건이 일치해야 합니다.

모든 필터는 메시지 속성을 평가합니다. 필터는 메시지 본문을 평가할 수 없습니다.

복잡한 필터 규칙에는 처리 용량이 필요합니다. 특히 SQL 필터 규칙을 사용하면 구독, 토픽, 네임스페이스 수준에서 전반적인 메시지 처리량이 줄어듭니다. 가능할 때마다 애플리케이션은 SQL과 유사한 필터보다 상관 관계 필터를 선택해야 하는데, 이는 상관 관계 필터의 처리 효율이 훨씬 높아서 처리량에 미치는 영향이 적기 때문입니다.

## <a name="actions"></a>동작

SQL 필터 조건을 사용하면 속성 및 값을 추가, 제거 또는 교체하여 메시지에 주석을 달 수 있는 작업을 정의할 수 있습니다. 이 작업은 SQL UPDATE 문에 개괄적으로 의지하는 [SQL-like 식을 사용](service-bus-messaging-sql-rule-action.md)합니다. 이 작업은 메시지가 일치된 이후 및 구독에 선택되기 이전에 해당 메시지에서 수행됩니다. 메시지 속성에 대한 변경 사항은 구독에 복사된 메시지에 대한 프라이빗입니다.

## <a name="usage-patterns"></a>사용 패턴

토픽에 대한 가장 간단한 사용 시나리오는 모든 구독이 토픽에 전송된 각 메시지의 복사본을 가져오는 브로드캐스트 패턴을 사용하는 것입니다.

필터와 작업을 통해 분할과 라우팅이라는 두 가지 패턴 그룹을 추가로 사용할 수 있습니다.

분할은 필터를 사용하여 예측 가능하며 상호 배타적인 방식으로 기존의 여러 토픽 구독에 메시지를 배포합니다. 분할 패턴은 전체 데이터의 하위 집합을 보유하는 기능적으로 동일한 구획에서 다수의 많은 컨텍스트를 처리하도록 시스템 규모가 확장될 때 사용됩니다(예: 고객 프로필 정보). 분할을 사용하면 게시자가 분할 모델에 대한 지식이 없어도 토픽에 메시지를 제출할 수 있습니다. 그런 다음 메시지는 올바른 구독으로 이동되고 파티션의 메시지 처리기에서 검색이 가능해집니다.

라우팅은 필터를 사용하여 예측 가능한 방식으로 토픽 구독에 메시지를 배포하지만 반드시 배타적이지는 않습니다. [자동 전달](service-bus-auto-forwarding.md) 기능과 함께 토픽 필터를 사용하여 Azure 지역 내에 메시지를 배포하기 위해 Service Bus 네임스페이스 내에 복잡한 라우팅 그래프를 만들 수 있습니다. Azure Service Bus 네임스페이스 간에 브리지 역할을 하는 Azure Functions 또는 Azure Logic Apps를 사용하면 LOB(기간 업무) 애플리케이션에 직접 통합하여 복잡한 글로벌 토폴로지를 만들 수 있습니다.

## <a name="examples"></a>예
예시는 [Service Bus 필터 예](service-bus-filter-examples.md)를 참조하세요.



> [!NOTE]
> Azure Portal이 이제 Service Bus Explorer 기능을 지원하므로 포털에서 구독 필터를 만들거나 편집할 수 있습니다. 

## <a name="next-steps"></a>다음 단계
선택한 언어로 샘플을 사용하여 Azure Service Bus 기능을 살펴봅니다. 

- [.NET용 Azure Service Bus 클라이언트 라이브러리 샘플(최신)](/samples/azure/azure-sdk-for-net/azuremessagingservicebus-samples/)
- [Java용 Azure Service Bus 클라이언트 라이브러리 샘플(최신)](/samples/azure/azure-sdk-for-java/servicebus-samples/)
- [Python용 Azure Service Bus 클라이언트 라이브러리 샘플](/samples/azure/azure-sdk-for-python/servicebus-samples/)
- [JavaScript용 Azure Service Bus 클라이언트 라이브러리 샘플](/samples/azure/azure-sdk-for-js/service-bus-javascript/)
- [TypeScript용 Azure Service Bus 클라이언트 라이브러리 샘플](/samples/azure/azure-sdk-for-js/service-bus-typescript/)

이전 .NET 및 Java 클라이언트 라이브러리에 대한 샘플은 다음을 참조하세요.
- [.NET용 Azure Service Bus 클라이언트 라이브러리 샘플(레거시)](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.Azure.ServiceBus/)
- [Java용 Azure Service Bus 클라이언트 라이브러리 샘플(레거시)](https://github.com/Azure/azure-service-bus/tree/master/samples/Java/azure-servicebus/MessageBrowse)