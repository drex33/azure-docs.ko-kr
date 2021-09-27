---
title: Azure Service Bus에서 구독 필터 설정 | Microsoft Docs
description: 이 문서에서는 Azure Service Bus 토픽 구독에서 필터 및 작업을 정의하는 예제를 제공합니다.
ms.topic: how-to
ms.date: 09/07/2021
ms.openlocfilehash: d73a8cdade8600a793911acfefc54a8e1e1530b6
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124754973"
---
# <a name="set-subscription-filters-azure-service-bus"></a>구독 필터 설정(Azure Service Bus)
이 문서에서는 Azure Service Bus 토픽 구독에서 필터를 설정하는 몇 가지 예제를 제공합니다. 필터에 대한 개념적 설명은 [필터](topic-filters.md)를 참조하세요.

## <a name="filter-on-system-properties"></a>시스템 속성의 필터
필터에서 시스템 속성을 참조하려면 `sys.<system-property-name>` 형식을 사용합니다. 

```csharp
sys.label LIKE '%bus%'`
sys.messageid = 'xxxx'
sys.correlationid like 'abc-%'
```

## <a name="filter-on-message-properties"></a>메시지 속성의 필터
다음은 필터에서 메시지 속성을 사용하는 예제입니다. `user.property-name` 또는 `property-name`만 사용하여 메시지 속성에 액세스할 수 있습니다.

```csharp
MessageProperty = 'A'
SuperHero like 'SuperMan%'
```

## <a name="filter-on-message-properties-with-special-characters"></a>특수 문자가 있는 메시지 속성의 필터
메시지 속성 이름에 특수 문자가 있으면 큰따옴표(`"`)를 사용하여 속성 이름을 묶습니다. 예를 들어 속성 이름이 `"http://schemas.microsoft.com/xrm/2011/Claims/EntityLogicalName"`이면 필터에서 다음 구문을 사용합니다. 

```csharp
"http://schemas.microsoft.com/xrm/2011/Claims/EntityLogicalName" = 'account'
```

## <a name="filter-on-message-properties-with-numeric-values"></a>숫자 값이 있는 메시지 속성의 필터
다음 예제는 필터에 숫자 값이 있는 속성을 사용하는 방법을 보여 줍니다. 

```csharp
MessageProperty = 1
MessageProperty > 1
MessageProperty > 2.08
MessageProperty = 1 AND MessageProperty2 = 3
MessageProperty = 1 OR MessageProperty2 = 3
```

## <a name="parameter-based-filters"></a>매개 변수 기반 필터
다음은 매개 변수 기반 필터를 사용하는 몇 가지 예제입니다. 이 예제에서 `DataTimeMp`는 `DateTime` 형식의 메시지 속성이고, `@dtParam`은 `DateTime` 개체로 필터에 전달되는 매개 변수입니다.

```csharp
DateTimeMp < @dtParam
DateTimeMp > @dtParam

(DateTimeMp2-DateTimeMp1) <= @timespan //@timespan is a parameter of type TimeSpan
DateTimeMp2-DateTimeMp1 <= @timespan
```

## <a name="using-in-and-not-in"></a>IN 및 NOT IN 사용

```csharp
StoreId IN('Store1', 'Store2', 'Store3')

sys.To IN ('Store5','Store6','Store7') OR StoreId = 'Store8'

sys.To NOT IN ('Store1','Store2','Store3','Store4','Store5','Store6','Store7','Store8') OR StoreId NOT IN ('Store1','Store2','Store3','Store4','Store5','Store6','Store7','Store8')
```

C# 샘플은 [GitHub의 토픽 필터 샘플](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Azure.Messaging.ServiceBus/BasicSendReceiveTutorialwithFilters)을 참조하세요.


## <a name="correlation-filters"></a>상관 관계 필터

### <a name="correlation-filter-using-correlationid"></a>CorrelationID를 사용하는 상관 관계 필터

```csharp
new CorrelationFilter("Contoso");
```

`CorrelationID`가 `Contoso`로 설정된 메시지를 필터링합니다. 

> [!NOTE]
> .NET의 [CorrelationRuleFilter](/dotnet/api/azure.messaging.servicebus.administration.correlationrulefilter) 클래스는 [ServiceBus](/dotnet/api/azure.messaging.servicebus.administration) 네임 스페이스에 있습니다. .NET을 사용 하 여 일반적으로 필터를 만드는 방법을 보여 주는 샘플 코드는 [GitHub에서이 코드](https://github.com/Azure/azure-service-bus/blob/master/samples/DotNet/Azure.Messaging.ServiceBus/BasicSendReceiveTutorialwithFilters/BasicSendReceiveTutorialWithFilters/Program.cs#L179)를 참조 하세요.


### <a name="correlation-filter-using-system-and-user-properties"></a>시스템 및 사용자 속성을 사용한 상관 관계 필터

```csharp
var filter = new CorrelationRuleFilter();
filter.Label = "Important";
filter.ReplyTo = "johndoe@contoso.com";
filter.Properties["color"] = "Red";
```

`sys.ReplyTo = 'johndoe@contoso.com' AND sys.Label = 'Important' AND color = 'Red'`와 동등합니다.





## <a name="next-steps"></a>다음 단계
다음 샘플을 참조하세요. 

- [.NET - 필터가 포함된 기본 송신 및 수신 자습서](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/GettingStarted/BasicSendReceiveTutorialwithFilters/BasicSendReceiveTutorialWithFilters)
- [.NET - 토픽 필터](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.Azure.ServiceBus/TopicFilters)
- [Azure Resource Manager 템플릿](/azure/templates/microsoft.servicebus/2017-04-01/namespaces/topics/subscriptions/rules)
