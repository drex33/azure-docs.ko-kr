---
title: Azure 서비스 패브릭 행위자 백업과 복원
description: Azure Service Fabric 행위자에서 백업 및 복원을 구현하는 방법을 알아봅니다.
ms.topic: conceptual
ms.date: 10/29/2018
ms.custom: devx-track-csharp
ms.openlocfilehash: 05dfb7d09e68373f52831a9e5233316df95ee552
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96571337"
---
# <a name="implement-reliable-actors-backup-and-restore"></a>Reliable Actors 백업 및 복원 구현

> [!NOTE]
> Reliable Stateful 서비스 및 Reliable Actors의 데이터 백업 구성에는 [정기적인 백업 및 복원](service-fabric-backuprestoreservice-quickstart-azurecluster.md)을 사용하는 것이 좋습니다. 
> 

다음 예제에서는 사용자 지정 행위자 서비스가 `ActorService`에 이미 나타난 원격 수신기를 활용하여 행위자 데이터를 백업하는 메서드를 노출합니다.

```csharp
public interface IMyActorService : IService
{
    Task BackupActorsAsync();
}

class MyActorService : ActorService, IMyActorService
{
    public MyActorService(StatefulServiceContext context, ActorTypeInformation typeInfo, Func<ActorBase> newActor)
        : base(context, typeInfo, newActor)
    { }

    public Task BackupActorsAsync()
    {
        return this.BackupAsync(new BackupDescription(PerformBackupAsync));
    }

    private async Task<bool> PerformBackupAsync(BackupInfo backupInfo, CancellationToken cancellationToken)
    {
        try
        {
           // store the contents of backupInfo.Directory
           return true;
        }
        finally
        {
           Directory.Delete(backupInfo.Directory, recursive: true);
        }
    }
}
```
```Java
public interface MyActorService extends Service
{
    CompletableFuture<?> backupActorsAsync();
}

class MyActorServiceImpl extends ActorService implements MyActorService
{
    public MyActorService(StatefulServiceContext context, ActorTypeInformation typeInfo, Func<FabricActorService, ActorId, ActorBase> newActor)
    {
       super(context, typeInfo, newActor);
    }

    public CompletableFuture backupActorsAsync()
    {
        return this.backupAsync(new BackupDescription((backupInfo, cancellationToken) -> performBackupAsync(backupInfo, cancellationToken)));
    }

    private CompletableFuture<Boolean> performBackupAsync(BackupInfo backupInfo, CancellationToken cancellationToken)
    {
        try
        {
           // store the contents of backupInfo.Directory
           return true;
        }
        finally
        {
           deleteDirectory(backupInfo.Directory)
        }
    }

    void deleteDirectory(File file) {
        File[] contents = file.listFiles();
        if (contents != null) {
            for (File f : contents) {
               deleteDirectory(f);
             }
        }
        file.delete();
    }
}
```

이 예제에서 `IMyActorService`은 `IService`(C#) 또는 `Service`(Java)를 구현하는 원격 계약이고 `MyActorService`에서 구현됩니다. 이 원격 서비스 계약을 추가하면 `IMyActorService`의 메서드도 `ActorServiceProxy`를 통해 원격 프록시를 만들어 클라이언트에 사용할 수 있게 됩니다.

```csharp
IMyActorService myActorServiceProxy = ActorServiceProxy.Create<IMyActorService>(
    new Uri("fabric:/MyApp/MyService"), ActorId.CreateRandom());

await myActorServiceProxy.BackupActorsAsync();
```
```Java
MyActorService myActorServiceProxy = ActorServiceProxy.create(MyActorService.class,
    new URI("fabric:/MyApp/MyService"), actorId);

myActorServiceProxy.backupActorsAsync();
```

Reliable Actors에 대한 자세한 내용은 다음 문서를 읽어보세요.
* [행위자 상태 관리](service-fabric-reliable-actors-state-management.md)
* [행위자 수명 주기 및 가비지 수집](service-fabric-reliable-actors-lifecycle.md)
* [행위자 API 참조 설명서](/previous-versions/azure/dn971626(v=azure.100))
* [.NET 샘플 코드](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [Java 샘플 코드](https://github.com/Azure-Samples/service-fabric-java-getting-started)

<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-platform/actor-service.png
[2]: ./media/service-fabric-reliable-actors-platform/app-deployment-scripts.png
[3]: ./media/service-fabric-reliable-actors-platform/actor-partition-info.png
[4]: ./media/service-fabric-reliable-actors-platform/actor-replica-role.png
[5]: ./media/service-fabric-reliable-actors-introduction/distribution.png
