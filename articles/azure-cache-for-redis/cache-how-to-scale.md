---
title: Azure Cache for Redis 인스턴스 스케일링
description: 이 문서에서는 Azure Portal과 Azure PowerShell 및 Azure CLI와 같은 도구를 사용하여 Azure Cache for Redis 인스턴스를 스케일링하는 방법을 보여 줍니다.
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 02/08/2021
ms.custom: devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: 0b76f98da4823341d5ec0f32c1d8f65a1c37fade
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122566948"
---
# <a name="scale-an-azure-cache-for-redis-instance"></a>Azure Cache for Redis 인스턴스 스케일링

Azure Cache for Redis에는 캐시 크기와 기능을 유연하게 선택할 수 있는 다양한 캐시 제안이 있습니다. 기본, 표준 또는 프리미엄 캐시의 경우 캐시를 만든 후 애플리케이션 요구 사항을 충족하기 위해 크기 및 계층을 변경할 수 있습니다. 이 문서에서는 Azure Portal과 Azure PowerShell 및 Azure CLI와 같은 도구를 사용하여 캐시 크기를 조정하는 방법을 보여 줍니다.

## <a name="when-to-scale"></a>크기를 조정하는 경우

Azure Cache for Redis의 [모니터링](cache-how-to-monitor.md) 기능을 사용하여 캐시의 상태 및 성능을 모니터링할 수 있습니다. 이 정보를 사용하여 캐시 크기 조정 시기를 결정합니다.

다음 메트릭을 모니터링하면 크기를 조정해야 하는지 결정하는데 도움이 될 수 있습니다.

- Redis 서버 부하
  - Redis 서버는 단일 스레드 프로세스입니다. Redis 서버 부하가 높으면 서버가 모든 클라이언트 연결의 요청을 계속해서 수행할 수 없습니다. 이러한 경우 오버헤드 함수가 여러 Redis 프로세스에 분산될 수 있도록 클러스터링을 사용 설정하거나 분할 수를 늘리는 데 도움이 됩니다. 클러스터링 및 대규모 분할 수는 TLS 암호화 및 암호 해독을 배포하고 TLS 연결 및 연결 해제를 배포합니다.
  - 자세한 내용은 [클러스터링 설정](cache-how-to-premium-clustering.md#set-up-clustering)을 참조하세요.
- 메모리 사용량
  - 높은 메모리 사용량은 데이터 크기가 현재 캐시 크기에 비해 너무 큰 것을 나타냅니다. 더 큰 메모리를 사용하는 캐시 크기로 크기를 조정하는 것을 고려합니다.
- 클라이언트 연결
  - 각 캐시 크기에는 지원할 수 있는 클라이언트 연결 수에 대한 제한이 있습니다. 클라이언트 연결이 캐시 크기 제한에 근접한 경우 더 큰 계층으로 스케일 업하는 것을 고려하거나 스케일 아웃하여 클러스터링을 사용하도록 설정하고 분할 수를 늘리는 것을 고려합니다. Redis 서버 부하 및 메모리 사용량에 따라 선택합니다.
  - 캐시 크기에 의한 연결 제한 사항에 대한 자세한 내용은 [Azure Cache for Redis 계획 FAQ](./cache-planning-faq.yml)를 참조하세요.
- 네트워크 대역폭
  - Redis 서버에서 사용 가능한 대역폭을 초과하는 경우 서버에서 클라이언트에 데이터를 충분히 빠르게 푸시할 수 없기 때문에 클라이언트 요청 시간이 초과될 수 있습니다. "Cache Read" 및 "Cache Write" 메트릭에서 서버 쪽 대역폭의 사용량을 확인합니다. Redis 서버에서 사용 가능한 네트워크 대역폭을 초과하는 경우 더 높은 네트워크 대역폭을 사용하는 더 큰 캐시 크기로 스케일 업하는 것을 고려해야 합니다.
  - 캐시 크기별로 네트워크에서 사용 가능한 대역폭에 대한 자세한 내용은 [Azure Cache for Redis 계획 FAQ](./cache-planning-faq.yml)를 참조하세요.

캐시가 더 이상 애플리케이션 요구 사항을 충족하지 못한다고 판단되면 애플리케이션에 적합한 캐시 가격 책정 계층으로 크기를 조정할 수 있습니다. 사용자의 요구에 맞게 더 크거나 더 작은 캐시를 선택할 수 있습니다.

사용할 캐시 가격 책정 계층을 결정하는 방법에 대한 자세한 내용은 [올바른 계층 선택](cache-overview.md#choosing-the-right-tier) 및 [Azure Cache for Redis 계획 FAQ](./cache-planning-faq.yml)를 참조하세요.

## <a name="scale-a-cache"></a>캐시 크기 조정

캐시 크기를 조정하려면 [Azure Portal](cache-configure.md#configure-azure-cache-for-redis-settings)에서 [캐시를 찾은](https://portal.azure.com) 다음 **리소스 메뉴** 에서 **크기 조정** 을 선택합니다.

![확장](./media/cache-how-to-scale/redis-cache-scale-menu.png)

왼쪽에 있는 **가격 책정 계층 선택** 및 **선택** 에서 원하는 가격 책정 계층을 선택합니다.

:::image type="content" source="media/cache-how-to-scale/redis-cache-pricing-tier-blade.png" alt-text="redis 캐시 가격 책정 계층 스크린샷":::

다른 가격 책정 계층으로 크기를 조정할 수 있지만 다음과 같은 제한 사항이 있습니다.

- 높은 가격 책정 계층에서 낮은 가격 책정 계층으로 크기를 조정할 수 없습니다.
  - **프리미엄** 캐시에서 **표준** 또는 **기본** 캐시로 축소할 수 없습니다.
  - **표준** 캐시에서 **기본** 캐시로 축소할 수 없습니다.
- **기본** 캐시에서 **표준** 캐시로 크기를 조정할 수 있지만 동시에 크기를 변경할 수는 없습니다. 다른 크기가 필요한 경우 나중에 원하는 크기로 크기 조정 작업을 수행할 수 있습니다.
- **기본** 캐시에서 바로 **프리미엄** 캐시로 확장할 수 없습니다. 먼저 크기 조정 작업을 통해 **기본** 에서 **표준** 으로 확장한 다음, 이후 크기 조정 작업을 통해 **표준** 에서 **프리미엄** 으로 확장합니다.
- 더 큰 크기에서 **C0(250MB)** 크기로 축소할 수 없습니다. 단, 동일한 가격 책정 계층 내에서는 다른 크기로 스케일 다운할 수 있습니다. 예를 들어 C5 Standard에서 C1 Standard로 스케일 다운할 수 있습니다.

캐시의 크기를 새 가격 책정 계층으로 조정하는 동안에는 **Azure Cache for Redis** 의 왼쪽에 **크기 조정 중** 상태가 표시됩니다.

:::image type="content" source="media/cache-how-to-scale/redis-cache-scaling.png" alt-text="redis 캐시 크기 조정":::

크기 조정이 완료되면 상태가 **Scaling(크기 조정 중)** 에서 **실행 중** 으로 변경됩니다.

## <a name="how-to-automate-a-scaling-operation"></a>크기 조정 작업을 자동화하는 방법

Azure Portal에서 캐시 인스턴스의 크기를 조정할 수 있습니다. PowerShell cmdlet, Azure CLI 및 MAML(Microsoft Azure 관리 라이브러리)을 사용하여 크기를 조정할 수 있습니다.

- [PowerShell을 사용하여 크기 조정](#scale-using-powershell)
- [Azure CLI를 사용한 크기 조정](#scale-using-azure-cli)
- [MAML을 사용하여 크기 조정](#scale-using-maml)

### <a name="scale-using-powershell"></a>PowerShell을 사용하여 크기 조정

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

`Size`, `Sku` 또는 `ShardCount` 속성을 수정할 때 [Set-AzRedisCache](/powershell/module/az.rediscache/set-azrediscache) cmdlet를 사용하여 PowerShell을 통해 Azure Cache for Redis 인스턴스의 크기를 조정할 수 있습니다. 다음 예제에서는 `myCache` 라는 캐시를 2.5GB 캐시로 크기를 조정하는 방법을 보여 줍니다.

```powershell
   Set-AzRedisCache -ResourceGroupName myGroup -Name myCache -Size 2.5GB
```

PowerShell을 사용하여 스케일링하는 방법에 대한 자세한 내용은 [PowerShell을 사용하여 Azure Cache for Redis 스케일링](cache-how-to-manage-redis-cache-powershell.md#scale)을 참조하세요.

### <a name="scale-using-azure-cli"></a>Azure CLI를 사용한 크기 조정

Azure CLI를 사용하여 Azure Cache for Redis 인스턴스의 크기를 조정하려면 `azure rediscache set` 명령을 호출하고 원하는 크기 조정 작업에 따라 새 크기, SKU, 또는 클러스터 크기가 포함된 원하는 구성 변경 내용을 전달합니다.

Azure CLI를 사용하여 크기를 조정하는 방법에 대한 자세한 내용은 [기존 Azure Cache for Redis에 대한 설정 변경](cache-manage-cli.md#scale)을 참조하세요.

### <a name="scale-using-maml"></a>MAML을 사용하여 크기 조정

[MAML(Microsoft Azure Management Libraries)](https://azure.microsoft.com/updates/management-libraries-for-net-release-announcement/)을 사용하여 Azure Cache for Redis 인스턴스의 크기를 조정하려면 `IRedisOperations.CreateOrUpdate` 메서드를 호출하고 `RedisProperties.SKU.Capacity`에 대한 새 크기를 전달합니다.

```csharp
    static void Main(string[] args)
    {
        // For instructions on getting the access token, see
        // https://azure.microsoft.com/documentation/articles/cache-configure/#access-keys
        string token = GetAuthorizationHeader();

        TokenCloudCredentials creds = new TokenCloudCredentials(subscriptionId,token);

        RedisManagementClient client = new RedisManagementClient(creds);
        var redisProperties = new RedisProperties();

        // To scale, set a new size for the redisSKUCapacity parameter.
        redisProperties.Sku = new Sku(redisSKUName,redisSKUFamily,redisSKUCapacity);
        redisProperties.RedisVersion = redisVersion;
        var redisParams = new RedisCreateOrUpdateParameters(redisProperties, redisCacheRegion);
        client.Redis.CreateOrUpdate(resourceGroupName,cacheName, redisParams);
    }
```

자세한 내용은 [MAML을 사용하여 Azure Cache for Redis 관리](https://github.com/rustd/RedisSamples/tree/master/ManageCacheUsingMAML) 샘플을 참조하세요.

## <a name="scaling-faq"></a>크기 조정 FAQ

Azure Cache for Redis 크기 조정에 대해 자주 묻는 질문과 대답이 나와 있는 목록은 다음과 같습니다.

- [프리미엄 캐시로 확장하거나, 이 캐시를 축소하거나 이 캐시 내에서 크기를 조정할 수 있나요?](#can-i-scale-to-from-or-within-a-premium-cache)
- [크기를 조정한 후 내 캐시 이름 또는 액세스 키를 변경해야 하나요?](#after-scaling-do-i-have-to-change-my-cache-name-or-access-keys)
- [크기 조정은 어떻게 수행되나요?](#how-does-scaling-work)
- [크기를 조정하는 동안 캐시의 데이터가 손실되나요?](#will-i-lose-data-from-my-cache-during-scaling)
- [사용자 지정 데이터베이스 설정이 크기 조정 하는 동안에 영향을 받나요?](#is-my-custom-databases-setting-affected-during-scaling)
- [크기를 조정하는 동안 내 캐시를 사용할 수 있나요?](#will-my-cache-be-available-during-scaling)
- [지역 복제에 크기 조정 제한 사항이 있나요?](#are-there-scaling-limitations-with-geo-replication)
- [지원되지 않는 작업](#operations-that-arent-supported)
- [크기 조정은 시간이 얼마나 걸리나요?](#how-long-does-scaling-take)
- [크기 조정이 완료되었는지 어떻게 알 수 있나요?](#how-can-i-tell-when-scaling-is-complete)

### <a name="can-i-scale-to-from-or-within-a-premium-cache"></a>프리미엄 캐시로 확장하거나, 이 캐시를 축소하거나 이 캐시 내에서 크기를 조정할 수 있나요?

- **프리미엄** 캐시에서 **기본** 또는 **표준** 가격 책정 계층으로 축소할 수 없습니다.
- 하나의 **프리미엄** 캐시 가격 책정 계층에서 다른 프리미엄 캐시 가격 책정 계층으로 크기를 조정할 수 있습니다.
- **기본** 캐시에서 바로 **프리미엄** 캐시로 확장할 수 없습니다. 먼저 크기 조정 작업을 통해 **기본** 에서 **표준** 으로 확장한 다음, 이후 크기 조정 작업을 통해 **표준** 에서 **프리미엄** 으로 확장합니다.
- **프리미엄** 캐시를 만들 때 클러스터링을 사용하도록 설정했으면 [클러스터 크기를 변경](cache-how-to-premium-clustering.md#cluster-size)할 수 있습니다. 클러스터를 사용하지 않고 캐시를 만든 경우 나중에 클러스터링를 구성할 수 있습니다.

자세한 내용은 [프리미엄 Azure Cache for Redis에 대한 클러스터링을 구성하는 방법](cache-how-to-premium-clustering.md)을 참조하세요.

### <a name="after-scaling-do-i-have-to-change-my-cache-name-or-access-keys"></a>크기를 조정한 후 내 캐시 이름 또는 액세스 키를 변경해야 하나요?

아니요, 캐시 이름 및 키는 크기 조정 작업을 수행하는 동안 변경되지 않습니다.

### <a name="how-does-scaling-work"></a>크기 조정은 어떻게 수행되나요?

- **기본** 캐시 크기를 다른 크기로 조정하는 경우 캐시가 종료되고 새 크기를 사용하여 새 캐시를 프로비전합니다. 이 시간 동안에는 캐시를 사용할 수 없으며 캐시의 모든 데이터가 손실됩니다.
- **기본** 캐시 크기를 **표준** 캐시로 조정하는 경우 복제본 캐시가 프로비전되며 데이터가 주 캐시에서 복제본 캐시로 복사됩니다. 크기를 조정하는 동안 캐시를 계속 사용할 수 있습니다.
- **표준** 캐시 크기를 다른 크기 또는 **프리미엄** 캐시로 조정하는 경우 복제본 중 하나가 종료되고 새 크기로 다시 프로비전되며 데이터가 전송됩니다. 그런 다음, 나머지 복제본이 장애 조치(failover)를 수행한 후 다시 프로비전됩니다. 이는 캐시 노드 중 하나에 장애가 발생하면 수행되는 프로세스와 비슷합니다.
- 클러스터형 캐시를 스케일 아웃하는 경우 새 분할이 프로비저닝되고 Redis 서버 클러스터에 추가됩니다. 그러면 모든 분할에 걸쳐 데이터가 재분할됩니다.
- 클러스터형 캐시에서 크기를 조정하는 경우 데이터를 먼저 재분할한 다음, 클러스터 크기를 필요한 분할로 줄입니다.

### <a name="will-i-lose-data-from-my-cache-during-scaling"></a>크기를 조정하는 동안 캐시의 데이터가 손실되나요?

- **기본** 캐시 크기를 새 크기로 조정하는 경우 모든 데이터가 손실되고 크기 조정 작업을 수행하는 동안 캐시를 사용할 수 없습니다.
- **기본** 캐시 크기를 **표준** 캐시로 조정하는 경우 일반적으로 캐시의 데이터가 유지됩니다.
- **표준** 캐시 크기를 더 큰 크기나 계층으로 조정하거나 **프리미엄** 캐시 크기가 더 큰 크기로 조정되는 경우 일반적으로 모든 데이터가 유지됩니다. 표준 또는 프리미엄 캐시 크기를 더 작은 크기로 조정하는 경우 데이터 크기가 축소될 때 더 작은 새 크기를 초과하면 데이터가 손실될 수 있습니다. 크기를 축소하는 경우 데이터가 손실되면 [allkeys-lru](https://redis.io/topics/lru-cache) 제거 정책을 사용하여 키를 제거합니다.

### <a name="is-my-custom-databases-setting-affected-during-scaling"></a>사용자 지정 데이터베이스 설정이 크기 조정 하는 동안에 영향을 받나요?

캐시 생성 중에 `databases` 설정에 대한 사용자 지정 값을 구성한 경우 일부 가격 책정 계층에 서로 다른 [데이터베이스 제한](cache-configure.md#databases)이 있습니다. 이 시나리오를 확장할 때 몇 가지 고려 사항이 있습니다.

- 현재 계층보다 낮은 `databases` 제한을 가진 가격 책정 계층으로 크기를 조정할 때:
  - 모든 가격 책정 계층에 대해 기본값이 16개인 `databases`을 사용하는 경우 데이터 손실은 전혀 없습니다.
  - 크기 조정하는 계층에 대한 제한 내에 포함되는 `databases`의 사용자 지정 수를 사용하는 경우, 이 `databases` 설정은 유지되고 데이터 손실은 전혀 없습니다.
  - 새 계층의 제한을 초과하는 `databases`의 사용자 지정 수를 사용하는 경우, `databases` 설정은 새 계층의 제한까지 낮춰지고 제거된 데이터베이스의 모든 데이터는 손실됩니다.
- 현재 계층보다 같거나 높은 `databases` 제한을 가진 가격 책정 계층으로 크기를 조정할 때 `databases` 설정은 유지되고 데이터 손실은 전혀 없습니다.

표준 및 프리미엄 캐시의 가용성에 대한 SLA는 99.9%이나 데이터 손실에 대한 SLA는 없습니다.

### <a name="will-my-cache-be-available-during-scaling"></a>크기를 조정하는 동안 내 캐시를 사용할 수 있나요?

- **표준** 및 **프리미엄** 캐시는 크기 조정 작업을 수행하는 동안 사용할 수 있습니다. 그러나 표준 및 프리미엄 캐시 크기를 조정하는 동안 및 기본 에서 표준 캐시로 확장하는 동안 연결 블립이 발생할 수 있습니다. 이러한 연결 블립은 작을 것으로 예상되며 redis 클라이언트가 일반적으로 해당 연결을 즉시 다시 설정할 수 있습니다.
- 작업을 다른 크기로 확장하는 동안 **기본** 캐시는 오프라인 상태입니다. **기본** 에서 **표준** 으로 크기를 조정하는 동안 기본 캐시를 그대로 사용할 수 있지만 작은 연결 블립이 발생할 수도 있습니다. 연결 블립이 발생하는 경우 Redis 클라이언트가 일반적으로 해당 연결을 즉시 다시 설정할 수 있습니다.

### <a name="are-there-scaling-limitations-with-geo-replication"></a>지역 복제에 크기 조정 제한 사항이 있나요?

지역 복제를 구성하면 캐시 크기를 조정하거나 클러스터의 분할을 변경할 수 없다는 것을 알 수 있습니다. 두 캐시 간의 지역 복제 링크를 사용하면 작업 크기를 조정하거나 클러스터의 분할 수를 변경할 수 없게 됩니다. 캐시의 연결을 해제하여 이러한 명령을 실행해야 합니다. 자세한 내용은 [지역 복제 구성](cache-how-to-geo-replication.md)을 참조하세요.

### <a name="operations-that-arent-supported"></a>지원되지 않는 작업

- 높은 가격 책정 계층에서 낮은 가격 책정 계층으로 크기를 조정할 수 없습니다.
  - **프리미엄** 캐시에서 **표준** 또는 **기본** 캐시로 축소할 수 없습니다.
  - **표준** 캐시에서 **기본** 캐시로 축소할 수 없습니다.
- **기본** 캐시에서 **표준** 캐시로 크기를 조정할 수 있지만 동시에 크기를 변경할 수는 없습니다. 다른 크기가 필요한 경우 나중에 원하는 크기로 조정 작업을 수행할 수 있습니다.
- **기본** 캐시에서 바로 **프리미엄** 캐시로 확장할 수 없습니다. 먼저 크기 조정 작업을 통해 **기본** 에서 **표준** 으로 크기를 조정한 다음, 이후 작업을 통해 **표준** 에서 **프리미엄** 으로 크기를 조정합니다.
- 더 큰 크기에서 **C0(250MB)** 크기로 축소할 수 없습니다.

크기 조정 작업이 실패하면 서비스는 작업을 되돌리려고 하며 캐시는 원래 크기로 되돌아갑니다.

### <a name="how-long-does-scaling-take"></a>크기 조정은 시간이 얼마나 걸리나요?

크기 조정 시간은 몇 가지 요인에 따라 달라집니다. 크기 조정 시간에 영향을 줄 수 있는 몇 가지 요인은 다음과 같습니다.

- 데이터 양: 많은 양의 데이터를 복제하는 데 시간이 더 오래 소요됩니다.
- 높은 쓰기 요청: 쓰기 수가 많으면 노드 또는 분할 간에 더 많은 데이터가 복제됩니다.
- 높은 서버 부하: 서버 부하가 높으면 Redis 서버가 사용 중이며 데이터 재배포를 완료하는 데 CPU 주기가 제한되어 있습니다.

일반적으로 데이터가 없는 캐시 크기를 조정하는 경우 약 20분이 소요됩니다. 클러스터형 캐시의 경우 크기 조정은 최소 데이터로 분할당 약 20분이 소요됩니다.

### <a name="how-can-i-tell-when-scaling-is-complete"></a>크기 조정이 완료되었는지 어떻게 알 수 있나요?

Azure Portal에서 진행 중인 크기 조정 작업을 볼 수 있습니다. 크기 조정이 완료되면 캐시 상태가 **실행 중** 으로 변경됩니다.

<!-- IMAGES -->

[redis-cache-pricing-tier-blade]: ./media/cache-how-to-scale/redis-cache-pricing-tier-blade.png

[redis-cache-scaling]: ./media/cache-how-to-scale/redis-cache-scaling.png