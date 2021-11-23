---
title: 포함 파일
description: 포함 파일
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: include
ms.custom: cog-serv-seo-aug-2020
ms.date: 03/23/2021
ms.openlocfilehash: 0087da1ed65aa1c20ca50602f23d4dc33de438af
ms.sourcegitcommit: 3d04177023a3136832adb561da831ccc8e9910c7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/23/2021
ms.locfileid: "132949572"
---
[참조 설명서](/dotnet/api/Microsoft.Azure.CognitiveServices.Personalizer) | [다중 슬롯 개념](..\concept-multi-slot-personalization.md) | [샘플](https://aka.ms/personalizer/ms-dotnet)

## <a name="prerequisites"></a>필수 구성 요소

* Azure 구독 - [체험 구독 만들기](https://azure.microsoft.com/free/cognitive-services)
* 최신 버전의 [.NET Core](https://dotnet.microsoft.com/download/dotnet-core)
* Azure 구독을 보유한 후에는 Azure Portal에서 <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesPersonalizer"  title="Personalizer 리소스 만들기"  target="_blank">Personalizer 리소스 </a>를 만들어 키와 엔드포인트를 가져옵니다. 배포 후 **리소스로 이동** 을 클릭합니다.
    * 애플리케이션을 Personalizer API에 연결하려면 만든 리소스의 키와 엔드포인트가 필요합니다. 이 빠른 시작의 뒷부분에 나오는 코드에 키와 엔드포인트를 붙여넣습니다.
    * 평가판 가격 책정 계층(`F0`)을 통해 서비스를 사용해보고, 나중에 프로덕션용 유료 계층으로 업그레이드할 수 있습니다.

## <a name="setting-up"></a>설치

[!INCLUDE [Upgrade Personalizer instance to multi-slot](upgrade-personalizer-multi-slot.md)]

[!INCLUDE [Change model frequency](change-model-frequency.md)]

[!INCLUDE [Change reward wait time](change-reward-wait-time.md)]

### <a name="create-a-new-c-application"></a>새 C# 애플리케이션 만들기

선호하는 편집기 또는 IDE에서 .NET Core 애플리케이션을 새로 만듭니다.

콘솔 창(예: cmd, PowerShell 또는 Bash)에서 dotnet `new` 명령을 사용하여 `personalizer-quickstart`라는 새 콘솔 앱을 만듭니다. 이 명령은 `Program.cs`라는 단일 소스 파일을 사용하여 간단한 "Hello World" C# 프로젝트를 만듭니다.

```console
dotnet new console -n personalizer-quickstart
```

새로 만든 앱 폴더로 디렉터리를 변경합니다. 다음을 통해 애플리케이션을 빌드할 수 있습니다.

```console
dotnet build
```

빌드 출력에 경고나 오류가 포함되지 않아야 합니다.

```console
...
Build succeeded.
 0 Warning(s)
 0 Error(s)
...
```

프로젝트 디렉터리의 원하는 편집기나 IDE에서 `Program.cs` 파일을 엽니다. 다음 using 지시문을 추가합니다.

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Net.Http;
using System.Text;
using System.Text.Json;
using System.Text.Json.Serialization;
using System.Threading.Tasks;
```

## <a name="object-model"></a>개체 모델

각 슬롯에 대한 콘텐츠의 가장 적합한 단일 항목을 요청하려면 **MultiSlotRankRequest** 를 만든 다음, 게시 요청을 [multislot/rank](https://westus2.dev.cognitive.microsoft.com/docs/services/personalizer-api-v1-1-preview-1/operations/MultiSlot_Rank)로 보냅니다. 그러면 응답이 **MultiSlotRankResponse** 로 구문 분석됩니다.

Personalizer에 보상 점수를 보내려면 **MultiSlotReward** 를 만든 다음, 게시 요청을 [multislot/events/{eventId}/reward](https://westus2.dev.cognitive.microsoft.com/docs/services/personalizer-api-v1-1-preview-1/operations/MultiSlot_Events_Reward)로 보냅니다.

이 빠른 시작에서 보상 점수를 결정하는 방법은 간단합니다. 프로덕션 시스템에서 [보상 점수](../concept-rewards.md)에 영향을 주는 요소와 크기를 결정하는 것은 복잡한 프로세스일 수 있으며, 시간이 지남에 따라 변경될 수 있습니다. 이 설계 결정은 Personalizer 아키텍처에서 기본 설계 결정 중 하나여야 합니다.

## <a name="code-examples"></a>코드 예제

이러한 코드 조각에서는 .NET에 대한 HTTP 요청을 전송하여 다음 작업을 수행하는 방법을 보여줍니다.

* [기본 URL 만들기](#create-base-urls)
* [다중 슬롯 순위 API](#request-the-best-action)
* [다중 슬롯 보상 API](#send-a-reward)


## <a name="create-base-urls"></a>기본 URL 만들기

이 섹션에서는 다음과 같은 두 가지 작업을 수행합니다.
* 키 및 엔드포인트 지정
* 순위 및 보상 URL 생성

프로그램 클래스에 다음 줄을 추가하여 시작합니다. Personalizer 리소스에서 키와 엔드포인트를 추가해야 합니다.

[!INCLUDE [Personalizer find resource info](find-azure-resource-info.md)]

```csharp
//Replace 'PersonalizationBaseUrl' and 'ResourceKey' with your valid endpoint values.
private const string PersonalizationBaseUrl = "<REPLACE-WITH-YOUR-PERSONALIZER-ENDPOINT>";
private const string ResourceKey = "<REPLACE-WITH-YOUR-PERSONALIZER-KEY>";
```

다음으로, 순위 및 보상 URL을 생성합니다.

```csharp
private static string MultiSlotRankUrl = string.Concat(PersonalizationBaseUrl, "personalizer/v1.1-preview.1/multislot/rank");
private static string MultiSlotRewardUrlBase = string.Concat(PersonalizationBaseUrl, "personalizer/v1.1-preview.1/multislot/events/");
```

## <a name="get-content-choices-represented-as-actions"></a>작업으로 표현되는 콘텐츠 선택 항목 가져오기

작업은 Personalizer에서 최상의 콘텐츠 항목을 선택할 수 있는 콘텐츠 선택 항목을 나타냅니다. Program 클래스에 다음 메서드를 추가하여 작업 세트와 해당 기능을 표시합니다. 

```csharp
private static IList<Action> GetActions()
{
    IList<Action> actions = new List<Action>
    {
        new Action
        {
            Id = "Red-Polo-Shirt-432",
            Features =
            new List<object>() { new { onSale = "true", price = "20", category = "Clothing" } }
        },
        new Action
        {
            Id = "Tennis-Racket-133",
            Features =
            new List<object>() { new { onSale = "false", price = "70", category = "Sports" } }
        },
        new Action
        {
            Id = "31-Inch-Monitor-771",
            Features =
            new List<object>() { new { onSale = "true", price = "200", category = "Electronics" } }
        },
        new Action
        {
            Id = "XBox-Series X-117",
            Features =
            new List<object>() { new { onSale = "false", price = "499", category = "Electronics" } }
        }
    };
    return actions;
}
```

## <a name="get-slots"></a>슬롯 가져오기

슬롯은 사용자가 상호 작용할 페이지를 구성합니다. Personalizer는 정의된 각 슬롯에 표시할 작업을 결정합니다. 동작은 `ExcludeActions`로 표시된 특정 슬롯에서 제외할 수 있습니다. `BaselineAction`은 Personalizer를 사용하지 않고 표시되는 슬롯에 대한 기본 동작입니다.


이 빠른 시작에는 간단한 슬롯 기능이 있습니다. 프로덕션 시스템에서 [기능](../concepts-features.md)을 결정하고 [평가](../concept-feature-evaluation.md)하는 것은 간단한 문제가 아닐 수 있습니다.

```csharp
private static IList<Slot> GetSlots()
{
    IList<Slot> slots = new List<Slot>
    {
        new Slot
        {
            Id = "BigHeroPosition",
            Features = new List<object>() { new { size = "large", position = "left" } },
            ExcludedActions = new List<string>() { "31-Inch-Monitor-771" },
            BaselineAction = "Red-Polo-Shirt-432"

        },
        new Slot
        {
            Id = "SmallSidebar",
            Features = new List<object>() { new { size = "small", position = "right" } },
            ExcludedActions = new List<string>() { "Tennis-Racket-133" },
            BaselineAction = "XBox-Series X-117"
        },
    };

    return slots;
}
```

## <a name="classes-for-constructing-rankreward-requests-and-responses"></a>순위/보상 요청 및 응답 구성을 위한 클래스

순위/보상 요청을 구성하고 해당 응답을 구문 분석하는 데 사용되는 다음 중첩 클래스를 추가합니다.

```csharp
private class Action
{
    [JsonPropertyName("id")]
    public string Id { get; set; }

    [JsonPropertyName("features")]
    public object Features { get; set; }
}
```

```csharp
private class Slot
{
    [JsonPropertyName("id")]
    public string Id { get; set; }

    [JsonPropertyName("features")]
    public List<object> Features { get; set; }

    [JsonPropertyName("excludedActions")]
    public List<string> ExcludedActions { get; set; }

    [JsonPropertyName("baselineAction")]
    public string BaselineAction { get; set; }
}
```

```csharp
private class Context
{
    [JsonPropertyName("features")]
    public object Features { get; set; }
}
```

```csharp
private class MultiSlotRankRequest
{
    [JsonPropertyName("contextFeatures")]
    public IList<Context> ContextFeatures { get; set; }

    [JsonPropertyName("actions")]
    public IList<Action> Actions { get; set; }

    [JsonPropertyName("slots")]
    public IList<Slot> Slots { get; set; }

    [JsonPropertyName("eventId")]
    public string EventId { get; set; }

    [JsonPropertyName("deferActivation")]
    public bool DeferActivation { get; set; }
}
```

```csharp
private class MultiSlotRankResponse
{
    [JsonPropertyName("slots")]
    public IList<SlotResponse> Slots { get; set; }

    [JsonPropertyName("eventId")]
    public string EventId { get; set; }
}
```

```csharp
private class SlotResponse
{
    [JsonPropertyName("id")]
    public string Id { get; set; }

    [JsonPropertyName("rewardActionId")]
    public string RewardActionId { get; set; }
}
```

```csharp
private class MultiSlotReward
{
    [JsonPropertyName("reward")]
    public List<SlotReward> Reward { get; set; }
}
```

```csharp
private class SlotReward
{
    [JsonPropertyName("slotId")]
    public string SlotId { get; set; }

    [JsonPropertyName("value")]
    public float Value { get; set; }
}
```

## <a name="get-user-preferences-for-context"></a>컨텍스트에 대한 사용자 기본 설정 검색

다음 메서드를 Program 클래스에 추가하여 하루 중 시간 및 사용자가 사용 중인 디바이스 유형에 대한 명령줄에서 사용자의 입력을 가져옵니다. 이러한 메서드는 컨텍스트 기능으로 사용됩니다.

```csharp
static string GetTimeOfDayForContext()
{
    string[] timeOfDayFeatures = new string[] { "morning", "afternoon", "evening", "night" };

    Console.WriteLine("\nWhat time of day is it (enter number)? 1. morning 2. afternoon 3. evening 4. night");
    if (!int.TryParse(GetKey(), out int timeIndex) || timeIndex < 1 || timeIndex > timeOfDayFeatures.Length)
    {
        Console.WriteLine("\nEntered value is invalid. Setting feature value to " + timeOfDayFeatures[0] + ".");
        timeIndex = 1;
    }

    return timeOfDayFeatures[timeIndex - 1];
}
```

```csharp
static string GetDeviceForContext()
{
    string[] deviceFeatures = new string[] { "mobile", "tablet", "desktop" };

    Console.WriteLine("\nWhat is the device type (enter number)? 1. Mobile 2. Tablet 3. Desktop");
    if (!int.TryParse(GetKey(), out int deviceIndex) || deviceIndex < 1 || deviceIndex > deviceFeatures.Length)
    {
        Console.WriteLine("\nEntered value is invalid. Setting feature value to " + deviceFeatures[0] + ".");
        deviceIndex = 1;
    }

    return deviceFeatures[deviceIndex - 1];
}
```

두 메서드는 모두 `GetKey` 메서드를 사용하여 명령줄에서 사용자의 선택 항목을 읽습니다.

```csharp
private static string GetKey()
{
    return Console.ReadKey().Key.ToString().Last().ToString().ToUpper();
}
```

```csharp
private static IList<Context> GetContext(string time, string device)
{
    IList<Context> context = new List<Context>
    {
        new Context
        {
            Features = new {timeOfDay = time, device = device }
        }
    };

    return context;
}
```

## <a name="make-http-requests"></a>HTTP 요청 만들기

이러한 함수를 추가하여 다중 슬롯 순위 및 보상 호출에 대한 게시 요청을 Personalizer 엔드포인트로 보냅니다.

```csharp
private static async Task<MultiSlotRankResponse> SendMultiSlotRank(HttpClient client, string rankRequestBody, string rankUrl)
{
    try
    {
    var rankBuilder = new UriBuilder(new Uri(rankUrl));
    HttpRequestMessage rankRequest = new HttpRequestMessage(HttpMethod.Post, rankBuilder.Uri);
    rankRequest.Content = new StringContent(rankRequestBody, Encoding.UTF8, "application/json");
    HttpResponseMessage response = await client.SendAsync(rankRequest);
    response.EnsureSuccessStatusCode();
    MultiSlotRankResponse rankResponse = JsonSerializer.Deserialize<MultiSlotRankResponse>(await response.Content.ReadAsByteArrayAsync());
    return rankResponse;
    }
    catch (Exception e)
    {
        Console.WriteLine("\n" + e.Message);
        Console.WriteLine("Please make sure multi-slot feature is enabled. To do so, follow multi-slot Personalizer documentation to update your loop settings to enable multi-slot functionality.");
        throw;
    }
}
```

```csharp
private static async Task SendMultiSlotReward(HttpClient client, string rewardRequestBody, string rewardUrlBase, string eventId)
{
    string rewardUrl = String.Concat(rewardUrlBase, eventId, "/reward");
    var rewardBuilder = new UriBuilder(new Uri(rewardUrl));
    HttpRequestMessage rewardRequest = new HttpRequestMessage(HttpMethod.Post, rewardBuilder.Uri);
    rewardRequest.Content = new StringContent(rewardRequestBody, Encoding.UTF8, "application/json");

    await client.SendAsync(rewardRequest);
}
```

## <a name="create-the-learning-loop"></a>학습 루프 만들기

Personalizer 학습 루프는 [MultiSlotRank](#request-the-best-action) 및 [MultiSlotReward](#send-a-reward) 호출의 주기입니다. 이 빠른 시작에서는 각 순위 호출을 수행하여 콘텐츠를 맞춤 설정한 다음, 보상 호출을 수행하여 서비스에서 얼마나 잘 수행되었는지 Personalizer에 알려줍니다.

다음 코드는 명령줄을 통해 사용자에게 해당 기본 설정을 요청하고, 해당 정보를 Personalizer로 보내어 각 슬롯에 대한 최상의 작업을 선택하고, 고객에게 해당 선택 항목을 제시하여 목록에서 선택한 다음, 보상 점수를 Personalizer에 전달하여 서비스에서 얼마나 잘 지정했는지 알려주는 주기를 반복합니다.

```csharp
static async Task Main(string[] args)
{
    Console.WriteLine($"Welcome to this Personalizer Quickstart!\n" +
            $"This code will help you understand how to use the Personalizer APIs (multislot rank and multislot reward).\n" +
            $"Each iteration represents a user interaction and will demonstrate how context, actions, slots, and rewards work.\n" +
            $"Note: Personalizer AI models learn from a large number of user interactions:\n" +
            $"You won't be able to tell the difference in what Personalizer returns by simulating a few events by hand.\n" +
            $"If you want a sample that focuses on seeing how Personalizer learns, see the Python Notebook sample.");

    IList<Action> actions = GetActions();
    IList<Slot> slots = GetSlots();

    using (var client = new HttpClient())
    {
        client.DefaultRequestHeaders.Add("ocp-apim-subscription-key", ResourceKey);
        int iteration = 1;
        bool runLoop = true;
        do
        {
            Console.WriteLine($"\nIteration: {iteration++}");
            string timeOfDayFeature = GetTimeOfDayForContext();
            string deviceFeature = GetDeviceForContext();

            IList<Context> context = GetContext(timeOfDayFeature, deviceFeature);

            string eventId = Guid.NewGuid().ToString();

            string rankRequestBody = JsonSerializer.Serialize(new MultiSlotRankRequest()
            {
                ContextFeatures = context,
                Actions = actions,
                Slots = slots,
                EventId = eventId,
                DeferActivation = false
            });

            //Ask Personalizer what action to show for each slot
            MultiSlotRankResponse multiSlotRankResponse = await SendMultiSlotRank(client, rankRequestBody, MultiSlotRankUrl);

            MultiSlotReward multiSlotRewards = new MultiSlotReward()
            {
                Reward = new List<SlotReward>()
            };

            for (int i = 0; i < multiSlotRankResponse.Slots.Count(); ++i)
            {
                Console.WriteLine($"\nPersonalizer service decided you should display: { multiSlotRankResponse.Slots[i].RewardActionId} in slot {multiSlotRankResponse.Slots[i].Id}. Is this correct? (y/n)");
                SlotReward reward = new SlotReward()
                {
                    SlotId = multiSlotRankResponse.Slots[i].Id
                };

                string answer = GetKey();

                if (answer == "Y")
                {
                    reward.Value = 1;
                    Console.WriteLine("\nGreat! The application will send Personalizer a reward of 1 so it learns from this choice of action for this slot.");
                }
                else if (answer == "N")
                {
                    reward.Value = 0;
                    Console.WriteLine("\nYou didn't like the recommended item. The application will send Personalizer a reward of 0 for this choice of action for this slot.");
                }
                else
                {
                    reward.Value = 0;
                    Console.WriteLine("\nEntered choice is invalid. Service assumes that you didn't like the recommended item.");
                }
                multiSlotRewards.Reward.Add(reward);
            }

            string rewardRequestBody = JsonSerializer.Serialize(multiSlotRewards);

            // Send the reward for the action based on user response for each slot.
            await SendMultiSlotReward(client, rewardRequestBody, MultiSlotRewardUrlBase, multiSlotRankResponse.EventId);

            Console.WriteLine("\nPress q to break, any other key to continue:");
            runLoop = !(GetKey() == "Q");
        } while (runLoop);
    }
}
```
다음 섹션에서 순위 및 보상 호출에 대해 자세히 살펴보겠습니다.
코드 파일을 실행하기 전에 [콘텐츠 선택 항목을 가져오고](#get-content-choices-represented-as-actions), [슬롯을 가져오고](#get-slots), [다중 슬롯 순위 및 보상 요청을 보내는](#make-http-requests) 다음 메서드를 추가합니다.

* `GetActions`
* `GetSlots`
* `GetTimeOfDayForContext`
* `GetTimeOfDayForContext`
* `GetKey`
* `GetContext`
* `SendMultiSlotRank`
* `SendMultiSlotReward`

다음 클래스를 추가합니다. 이 클래스는 코드 파일을 실행하기 전에 [순위/보상 요청의 본문을 구성하고 해당 응답을 구문 분석](#classes-for-constructing-rankreward-requests-and-responses)합니다.

* `Action`
* `Slot`
* `Context`
* `MultiSlotRankRequest`
* `MultiSlotRankResponse`
* `SlotResponse`
* `MultiSlotReward`
* `SlotReward`

## <a name="request-the-best-action"></a>최상의 작업 요청

프로그램에서는 순위 요청을 수행하기 위해 사용자의 기본 설정에서 `context` 콘텐츠 선택 항목을 만들도록 요청합니다. 요청 본문에는 해당 기능이 있는 컨텍스트, 작업, 슬롯 등이 포함됩니다. `SendMultiSlotRank` 메서드는 HTTP 클라이언트, 요청 본문, URL에서 요청을 보내는 데 사용됩니다.

이 빠른 시작에는 시간 및 사용자 디바이스에 대한 간단한 컨텍스트 기능이 있습니다. 프로덕션 시스템에서 [작업 및 기능](../concepts-features.md)을 결정하고 [평가](../concept-feature-evaluation.md)하는 것은 간단한 문제가 아닐 수 있습니다.

```csharp
string timeOfDayFeature = GetTimeOfDayForContext();
string deviceFeature = GetDeviceForContext();

IList<Context> context = GetContext(timeOfDayFeature, deviceFeature);

string eventId = Guid.NewGuid().ToString();

string rankRequestBody = JsonSerializer.Serialize(new MultiSlotRankRequest()
{
    ContextFeatures = context,
    Actions = actions,
    Slots = slots,
    EventId = eventId,
    DeferActivation = false
});

//Ask Personalizer what action to show for each slot
MultiSlotRankResponse multiSlotRankResponse = await SendMultiSlotRank(client, rankRequestBody, MultiSlotRankUrl);
```

## <a name="send-a-reward"></a>보상 보내기

보상 요청의 보상 점수를 가져오기 위해 프로그램은 명령줄을 통해 각 슬롯에 대한 사용자의 선택 사항을 가져와서 숫자 값(보상 점수)을 각 선택 항목에 할당한 다음, 고유한 이벤트 ID, 슬롯 ID, 보상 점수를 숫자 값으로 보상 API에 보냅니다. 각 슬롯에 대해 보상을 정의할 필요가 없습니다.

이 빠른 시작에서는 0 또는 1의 간단한 숫자를 보상 점수로 할당합니다. 프로덕션 시스템에서 특정 요구 사항에 따라 [보상](../concept-rewards.md) 호출에 보내는 시기와 대상을 결정하는 것은 간단한 문제가 아닐 수 있습니다.

```csharp
MultiSlotReward multiSlotRewards = new MultiSlotReward()
{
    Reward = new List<SlotReward>()
};

for (int i = 0; i < multiSlotRankResponse.Slots.Count(); ++i)
{
    Console.WriteLine($"\nPersonalizer service decided you should display: { multiSlotRankResponse.Slots[i].RewardActionId} in slot {multiSlotRankResponse.Slots[i].Id}. Is this correct? (y/n)");
    SlotReward reward = new SlotReward()
    {
        SlotId = multiSlotRankResponse.Slots[i].Id
    };

    string answer = GetKey();

    if (answer == "Y")
    {
        reward.Value = 1;
        Console.WriteLine("\nGreat! The application will send Personalizer a reward of 1 so it learns from this choice of action for this slot.");
    }
    else if (answer == "N")
    {
        reward.Value = 0;
        Console.WriteLine("\nYou didn't like the recommended item. The application will send Personalizer a reward of 0 for this choice of action for this slot.");
    }
    else
    {
        reward.Value = 0;
        Console.WriteLine("\nEntered choice is invalid. Service assumes that you didn't like the recommended item.");
    }
    multiSlotRewards.Reward.Add(reward);
}

string rewardRequestBody = JsonSerializer.Serialize(multiSlotRewards);

// Send the reward for the action based on user response for each slot.
await SendMultiSlotReward(client, rewardRequestBody, MultiSlotRewardUrlBase, multiSlotRankResponse.EventId);
```

## <a name="run-the-program"></a>프로그램 실행

애플리케이션 디렉터리에서 dotnet `run` 명령을 사용하여 애플리케이션을 실행합니다.

```console
dotnet run
```

![빠른 시작 프로그램은 기능으로 알려진 사용자 기본 설정을 수집하기 위해 몇 가지 질문을 합니다.](../media/csharp-quickstart-commandline-feedback-loop/multislot-quickstart-program-feedback-loop-example-1.png)


[이 빠른 시작의 소스 코드](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/dotnet/Personalizer/multislot-quickstart-v2PreviewSdk)가 제공됩니다.
