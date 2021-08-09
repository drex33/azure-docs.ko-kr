---
title: Reliable Actors의 타이머 및 미리 알림
description: Service Fabric Reliable Actors의 타이머와 미리 알림을 소개하고 각각의 적절한 사용 시기에 대한 지침도 함께 제공합니다.
ms.topic: conceptual
ms.date: 11/02/2017
ms.custom: devx-track-csharp
ms.openlocfilehash: f77eb29c9146fe66d5d2b6073c33e30fbab649c2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98791797"
---
# <a name="actor-timers-and-reminders"></a>행위자 타이머 및 미리 알림
행위자는 타이머 또는 미리 알림을 등록하여 정기적인 작업을 예약할 수 있습니다. 이 문서에서는 타이머와 미리 알림을 사용하는 방법을 보여 주고 둘 간의 차이점을 설명합니다.

## <a name="actor-timers"></a>행위자 타이머
행위자 타이머는 콜백 메서드가 행위자 런타임에서 제공하는 턴 기반 동시성 보증을 준수하는 .NET 또는 Java 타이머에 대한 간단한 래퍼를 제공합니다.

행위자는 기본 클래스에서 `RegisterTimer`(C#) 또는 `registerTimer`(Java) 및 `UnregisterTimer`(C#) 또는 `unregisterTimer`(Java) 메서드를 사용하여 해당 타이머를 등록 및 등록 취소할 수 있습니다. 아래 예제에서는 타이머 API의 사용 방법을 보여줍니다. API는 .NET 타이머 또는 Java 타이머와 매우 유사합니다. 이 예제의 경우 타이머가 만료되면 행위자 런타임에서는 `MoveObject`(C#) 또는 `moveObject`(Java) 메서드를 호출합니다. 메서드는 턴 기반 동시성을 준수해야 합니다. 즉, 다른 행위자 메서드나 타이머/미리 알림 콜백은 이 콜백의 실행이 완료될 때까지 진행되어야 합니다.

```csharp
class VisualObjectActor : Actor, IVisualObject
{
    private IActorTimer _updateTimer;

    public VisualObjectActor(ActorService actorService, ActorId actorId)
        : base(actorService, actorId)
    {
    }

    protected override Task OnActivateAsync()
    {
        ...

        _updateTimer = RegisterTimer(
            MoveObject,                     // Callback method
            null,                           // Parameter to pass to the callback method
            TimeSpan.FromMilliseconds(15),  // Amount of time to delay before the callback is invoked
            TimeSpan.FromMilliseconds(15)); // Time interval between invocations of the callback method

        return base.OnActivateAsync();
    }

    protected override Task OnDeactivateAsync()
    {
        if (_updateTimer != null)
        {
            UnregisterTimer(_updateTimer);
        }

        return base.OnDeactivateAsync();
    }

    private Task MoveObject(object state)
    {
        ...
        return Task.FromResult(true);
    }
}
```
```Java
public class VisualObjectActorImpl extends FabricActor implements VisualObjectActor
{
    private ActorTimer updateTimer;

    public VisualObjectActorImpl(FabricActorService actorService, ActorId actorId)
    {
        super(actorService, actorId);
    }

    @Override
    protected CompletableFuture onActivateAsync()
    {
        ...

        return this.stateManager()
                .getOrAddStateAsync(
                        stateName,
                        VisualObject.createRandom(
                                this.getId().toString(),
                                new Random(this.getId().toString().hashCode())))
                .thenApply((r) -> {
                    this.registerTimer(
                            (o) -> this.moveObject(o),                        // Callback method
                            "moveObject",
                            null,                                             // Parameter to pass to the callback method
                            Duration.ofMillis(10),                            // Amount of time to delay before the callback is invoked
                            Duration.ofMillis(timerIntervalInMilliSeconds));  // Time interval between invocations of the callback method
                    return null;
                });
    }

    @Override
    protected CompletableFuture onDeactivateAsync()
    {
        if (updateTimer != null)
        {
            unregisterTimer(updateTimer);
        }

        return super.onDeactivateAsync();
    }

    private CompletableFuture moveObject(Object state)
    {
        ...
        return this.stateManager().getStateAsync(this.stateName).thenCompose(v -> {
            VisualObject v1 = (VisualObject)v;
            v1.move();
            return (CompletableFuture<?>)this.stateManager().setStateAsync(stateName, v1).
                    thenApply(r -> {
                      ...
                      return null;});
        });
    }
}
```

다음 타이머 시간 간격은 콜백 실행이 완료된 후 시작됩니다. 이는 타이머 콜백이 실행되는 동안 타이머는 중지되고 콜백이 완료되면 시작된다는 것을 의미합니다.

행위자 런타임은 콜백이 완료되면 행위자의 상태 관리자에 대한 변경 내용을 저장합니다. 상태를 저장하는 중에 오류가 발생하는 경우 해당 행위자 개체는 비활성화되고 새 인스턴스가 활성화됩니다.

[미리 알림](#actor-reminders)과 달리 타이머는 업데이트할 수 없습니다. `RegisterTimer`가 다시 호출되면 새 타이머가 등록됩니다.

행위자가 가비지 수집의 일환으로 비활성화되면 모든 타이머가 중지됩니다. 그다음 타이머 콜백이 호출되지 않습니다. 또한 행위자 런타임은 비활성화 전에 실행 중이었던 타이머에 대한 정보를 유지하지 않습니다. 나중에 다시 활성화될 때 필요한 모든 타이머를 등록하는 것은 행위자의 일입니다. 자세한 내용은 [행위자 가비지 수집](service-fabric-reliable-actors-lifecycle.md)섹션을 참조하세요.

## <a name="actor-reminders"></a>행위자 미리 알림
미리 알림은 행위자에서 지정된 시간에 영구 콜백을 트리거하는 메커니즘입니다. 기능은 타이머와 비슷합니다. 하지만 타이머와 달리 미리 알림은 행위자가 명시적으로 등록을 취소하거나 행위자가 명시적으로 삭제할 때까지 모든 상황에서 트리거됩니다. 구체적으로, 미리 알림은 행위자 런타임이 행위자 상태 제공자를 사용하여 행위자의 미리 알림에 대한 정보를 유지하므로 행위자 비활성화 및 장애 조치를 통해 트리거됩니다. 또한 타이머와 달리 동일한 *reminderName* 을 사용하여 등록 메서드(`RegisterReminderAsync`)를 다시 호출하면 기존의 미리 알림을 업데이트할 수 있습니다.

> [!NOTE]
> 미리 알림의 안정성은 작업자 상태 제공자가 제공한 상태 안정성 보장과 연결되어 있습니다. 즉, 상태 지속성이 ‘없음’으로 설정된 작업자의 경우 장애 조치(failover) 후 미리 알림이 발생하지 않습니다.

미리 알림을 등록하려면 작업자가 다음 예제와 같이 기본 클래스에서 제공된 [`RegisterReminderAsync`](/dotnet/api/microsoft.servicefabric.actors.runtime.actorbase.registerreminderasync#remarks) 메서드를 호출해야 합니다.

```csharp
protected override async Task OnActivateAsync()
{
    string reminderName = "Pay cell phone bill";
    int amountInDollars = 100;

    IActorReminder reminderRegistration = await this.RegisterReminderAsync(
        reminderName,
        BitConverter.GetBytes(amountInDollars),
        TimeSpan.FromDays(3),    //The amount of time to delay before firing the reminder
        TimeSpan.FromDays(1));    //The time interval between firing of reminders
}
```

```Java
@Override
protected CompletableFuture onActivateAsync()
{
    String reminderName = "Pay cell phone bill";
    int amountInDollars = 100;

    ActorReminder reminderRegistration = this.registerReminderAsync(
            reminderName,
            state,
            dueTime,    //The amount of time to delay before firing the reminder
            period);    //The time interval between firing of reminders
}
```

이 예제에서 `"Pay cell phone bill"` 은 미리 알림 이름입니다. 행위자가 미리 알림을 고유하게 식별하는 데 사용하는 문자열입니다. `BitConverter.GetBytes(amountInDollars)`(C#)는 해당 미리 알림에 연결되는 컨텍스트입니다. 또한 이 값은 미리 알림 콜백의 인수(`IRemindable.ReceiveReminderAsync`(C#) 또는 `Remindable.receiveReminderAsync`(Java))로 행위자에게 다시 전달됩니다.

미리 알림을 사용하는 행위자는 아래 예제에 나온 대로 `IRemindable` 인터페이스를 구현해야 합니다.

```csharp
public class ToDoListActor : Actor, IToDoListActor, IRemindable
{
    public ToDoListActor(ActorService actorService, ActorId actorId)
        : base(actorService, actorId)
    {
    }

    public Task ReceiveReminderAsync(string reminderName, byte[] context, TimeSpan dueTime, TimeSpan period)
    {
        if (reminderName.Equals("Pay cell phone bill"))
        {
            int amountToPay = BitConverter.ToInt32(context, 0);
            System.Console.WriteLine("Please pay your cell phone bill of ${0}!", amountToPay);
        }
        return Task.FromResult(true);
    }
}
```
```Java
public class ToDoListActorImpl extends FabricActor implements ToDoListActor, Remindable
{
    public ToDoListActor(FabricActorService actorService, ActorId actorId)
    {
        super(actorService, actorId);
    }

    public CompletableFuture receiveReminderAsync(String reminderName, byte[] context, Duration dueTime, Duration period)
    {
        if (reminderName.equals("Pay cell phone bill"))
        {
            int amountToPay = ByteBuffer.wrap(context).getInt();
            System.out.println("Please pay your cell phone bill of " + amountToPay);
        }
        return CompletableFuture.completedFuture(true);
    }

```

미리 알림이 트리거되면 Reliable Actors 런타임에서 행위자에 대해 `ReceiveReminderAsync`(C#) 또는 `receiveReminderAsync`(Java) 메서드를 호출합니다. 행위자는 미리 알림을 여러 개 등록할 수 있으며 `ReceiveReminderAsync`(C#) 또는 `receiveReminderAsync`(Java) 메서드는 이러한 미리 알림 중 하나가 트리거되면 언제든지 호출됩니다. 행위자는 `ReceiveReminderAsync`(C#) 또는 `receiveReminderAsync`(Java) 메서드로 전달되는 미리 알림 이름을 사용하여 미리 알림이 트리거되었는지 알아낼 수 있습니다.

행위자 런타임은 `ReceiveReminderAsync`(C#) 또는 `receiveReminderAsync`(Java) 호출이 완료되면 행위자의 상태를 저장합니다. 상태를 저장하는 중에 오류가 발생하는 경우 해당 행위자 개체는 비활성화되고 새 인스턴스가 활성화됩니다.

미리 알림을 등록 취소하려면 행위자가 아래 예제에 나온 대로 `UnregisterReminderAsync`(C#) 또는 `unregisterReminderAsync`(Java) 메서드를 호출합니다.

```csharp
IActorReminder reminder = GetReminder("Pay cell phone bill");
Task reminderUnregistration = UnregisterReminderAsync(reminder);
```
```Java
ActorReminder reminder = getReminder("Pay cell phone bill");
CompletableFuture reminderUnregistration = unregisterReminderAsync(reminder);
```

위의 그림과 같이 `UnregisterReminderAsync`(C#) 또는 `unregisterReminderAsync`(Java) 메서드는 `IActorReminder`(C#) 또는 `ActorReminder`(Java) 인터페이스를 수락합니다. 행위자 기본 클래스는 미리 알림 이름에 전달하여 `IActorReminder`(C#) 또는 `ActorReminder`(Java) 인터페이스를 검색하는 데 사용할 수 있는 `GetReminder`(C#) 또는 `getReminder`(Java) 메서드를 지원합니다. 이 방법은 행위자가 `RegisterReminder`(C#) 또는 `registerReminder`(Java) 메서드 호출에서 반환된 `IActorReminder`(C#) 또는 `ActorReminder`(Java) 인터페이스를 유지할 필요가 없기 때문에 편리합니다.

## <a name="next-steps"></a>다음 단계
Reliable Actor 이벤트 및 재진입에 대해 알아봅니다.
* [행위자 이벤트](service-fabric-reliable-actors-events.md)
* [행위자 다시 표시](service-fabric-reliable-actors-reentrancy.md)
