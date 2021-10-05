---
title: 데이터 지속성 구성 - 프리미엄 Azure Cache for Redis
description: 프리미엄 계층 Azure Cache for Redis 인스턴스에 대한 데이터 지속성을 구성하고 관리하는 방법을 알아봅니다.
author: curib
ms.author: cauribeg
ms.service: cache
ms.topic: conceptual
ms.date: 02/08/2021
ms.openlocfilehash: 472f06a10e76cb557bd5e9350c94a352ebad17b3
ms.sourcegitcommit: c27f71f890ecba96b42d58604c556505897a34f3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/05/2021
ms.locfileid: "129538129"
---
# <a name="configure-data-persistence-for-a-premium-azure-cache-for-redis-instance"></a>프리미엄 Azure Cache for Redis에 대한 데이터 지속성을 구성

[Redis 지속성](https://redis.io/topics/persistence)을 사용하면 Redis에 저장된 데이터를 유지할 수 있습니다. 스냅샷을 찍고 데이터를 백업할 수도 있습니다. 하드웨어 오류가 있는 경우 데이터를 로드합니다. 데이터를 유지하는 기능은 모든 데이터가 메모리에 저장되는 기본 또는 표준 계층에 비해 큰 장점입니다. 캐시 노드가 다운된 경우 오류가 발생하면 데이터 손실이 있을 수 있습니다.

Azure Cache for Redis는 RDB(Redis 데이터베이스) 및 AOF(추가 전용 파일)를 사용하여 Redis 지속성을 제공합니다.

* **RDB 지속성** - RDB 지속성을 사용하는 경우 Azure Cache for Redis는 Redis에 있는 Azure Cache for Redis의 스냅샷을 이진 형식으로 디스크에 유지합니다. 스냅샷은 Azure Storage 계정에 저장됩니다. 구성 가능한 백업 빈도에 따라 스냅샷을 유지할 빈도가 결정됩니다. 중대한 이벤트가 발생하여 주 및 복제본 캐시가 모두 비활성화된 경우 가장 최근의 스냅샷을 사용하여 캐시를 재구성합니다. RDB 지속성의 [장점](https://redis.io/topics/persistence#rdb-advantages) 및 [단점](https://redis.io/topics/persistence#rdb-disadvantages)에 대해 자세히 알아봅니다.
* **AOF 지속성** - AOF 지속성을 사용하는 경우 Azure Cache for Redis는 모든 쓰기 작업을 로그에 저장합니다. 로그는 초당 한 번 이상 Azure Storage 계정에 저장됩니다. 중대한 이벤트가 발생하여 주 및 복제본 캐시가 모두 비활성화된 경우 저장된 쓰기 작업을 사용하여 캐시를 재구성합니다. AOF 지속성의 [장점](https://redis.io/topics/persistence#aof-advantages) 및 [단점](https://redis.io/topics/persistence#aof-disadvantages)에 대해 자세히 알아봅니다.

지속성은 사용자가 소유하고 관리하는 Azure Storage 계정에 Redis 데이터를 기록합니다. 캐시를 만드는 동안 왼쪽에서 **새로운 Azure Cache for Redis** 를 구성합니다. 기존 프리미엄 캐시의 경우 **리소스 메뉴** 를 사용합니다.

> [!NOTE]
>
> Azure Storage는 데이터가 유지될 때 자동으로 데이터를 암호화합니다. 암호화에 고유한 키를 사용할 수 있습니다. 자세한 내용은 [Azure Key Vault를 사용한 고객 관리형 키](../storage/common/storage-service-encryption.md)를 참조하세요.
>
>

## <a name="set-up-data-persistence"></a>데이터 지속성 설정

1. 프리미엄 캐시를 만들려면 [Azure Portal](https://portal.azure.com)에 로그인하여 **리소스 만들기** 를 선택합니다. Azure Portal에 캐시를 만들 수 있습니다. Resource Manager 템플릿, PowerShell 또는 Azure CLI를 사용하여 만들 수도 있습니다. Azure Cache for Redis를 만드는 방법에 대한 자세한 내용은 [캐시 만들기](cache-dotnet-how-to-use-azure-redis-cache.md#create-a-cache)를 참조하세요.

    :::image type="content" source="media/cache-private-link/1-create-resource.png" alt-text="리소스를 만듭니다.":::
  
2. **새로 만들기** 페이지에서 **데이터베이스** 를 선택한 다음, **Azure Cache for Redis** 를 선택합니다.

    :::image type="content" source="media/cache-private-link/2-select-cache.png" alt-text="Azure Cache for Redis를 선택합니다.":::

3. **새 Redis Cache** 페이지에서 새 프리미엄 캐시의 설정을 구성합니다.
  
   | 설정      | 제안 값  | Description |
   | ------------ |  ------- | -------------------------------------------------- |
   | **DNS 이름** | 전역적으로 고유한 이름을 입력합니다. | 캐시 이름은 1~63자의 문자열이어야 하며 숫자, 문자, 하이픈만 포함할 수 있습니다. 이름은 숫자 또는 문자로 시작하고 끝나야 하며 연속 하이픈을 포함할 수 없습니다. 캐시 인스턴스의 *호스트 이름* 은 *\<DNS name>.redis.cache.windows.net* 입니다. |
   | **구독** | 드롭다운하여 구독을 선택합니다. | 이 구독 아래에 새 Azure Cache for Redis 인스턴스가 만들어집니다. |
   | **리소스 그룹** | 드롭다운하여 리소스 그룹을 선택하거나, **새로 만들기** 를 선택하고 새 리소스 그룹 이름을 입력합니다. | 캐시 및 기타 리소스를 만들 새 리소스 그룹의 이름입니다. 모든 앱 리소스를 하나의 리소스 그룹에 배치하면 앱 리소스를 쉽게 관리하거나 삭제할 수 있습니다. |
   | **위치** | 드롭다운하여 위치를 선택합니다. | 캐시를 사용할 다른 서비스와 가까이 있는 [Azure 지역](https://azure.microsoft.com/regions/)을 선택합니다. |
   | **캐시 유형** | 드롭다운하여 프리미엄 기능을 구성하려는 프리미엄 캐시를 선택합니다. 자세한 내용은 [Azure Cache for Redis 가격 책정](https://azure.microsoft.com/pricing/details/cache/)을 참조하세요. |  가격 책정 계층은 캐시에 사용 가능한 크기, 성능 및 기능을 결정합니다. 자세한 내용은 [Azure Cache for Redis 개요](cache-overview.md)를 참조하세요. |

4. **네트워킹** 탭을 선택하거나 페이지 맨 아래에서 **네트워킹** 단추를 선택합니다.

5. **네트워킹** 탭에서 연결 방법을 선택합니다. 프리미엄 캐시 인스턴스의 경우 공용 IP 주소 또는 서비스 엔드포인트를 통해 공개적으로 연결합니다. 프라이빗 엔드포인트를 사용하여 비공개로 연결합니다.

6. **다음: 고급** 탭을 선택하거나 페이지 맨 아래에서 **다음: 고급** 단추를 선택합니다.

7. 프리미엄 캐시 인스턴스의 **고급** 탭에서 TLS가 아닌 포트, 클러스터링 및 데이터 지속성에 대한 설정을 구성합니다. 데이터 지속성의 경우 **RDB** 또는 **AOF** 지속성 중 하나를 선택할 수 있습니다.

8. RDB 지속성을 사용 하도록 설정하려면 **RDB** 를 선택하고 설정을 구성합니다.
  
   | 설정      | 제안 값  | Description |
   | ------------ |  ------- | -------------------------------------------------- |
   | **백업 주기** | 백업 간격을 드롭다운하고 선택합니다. **15분**, **30분**, **60분**, **6시간**, **12시간** 및 **24시간** 중에서 선택할 수 있습니다. | 이 간격은 이전 백업 작업이 성공적으로 완료되어 새 백업이 시작된 시점부터 계산됩니다. |
   | **Storage 계정** | 드롭다운하고 스토리지 계정을 선택합니다. | 캐시와 동일한 영역 및 구독에 있는 스토리지 계정을 선택하며 높은 처리량을 가진 **Premium Storage** 계정을 사용하는 것이 좋습니다.  |
   | **스토리지 키** | 드롭다운하고 사용할 **기본 키** 또는 **보조 키** 중 하나를 선택합니다. | 지속성 계정에 대한 스토리지 키가 다시 생성된 경우에는 **스토리지 키** 드롭다운에서 키를 다시 구성해야 합니다. |

    백업 주기 간격이 경과되면 첫 번째 백업이 시작됩니다.
  
   > [!NOTE]
   > RDB 파일은 저장소에 백업될 때 페이지 Blob 형식으로 저장됩니다.
  
9. AOF 지속성을 사용하려면 **AOF** 를 선택하고 설정을 구성합니다.

   | 설정      | 제안 값  | Description |
   | ------------ |  ------- | -------------------------------------------------- |
   | **첫 번째 스토리지 계정** | 드롭다운하고 스토리지 계정을 선택합니다. | 이 스토리지 계정은 캐시와 동일한 영역 및 구독에 있어야 하며 높은 처리량을 가진 **Premium Storage** 계정을 사용하는 것이 좋습니다. |
   | **첫 번째 스토리지 키** | 드롭다운하고 사용할 **기본 키** 또는 **보조 키** 중 하나를 선택합니다. | 지속성 계정에 대한 스토리지 키가 다시 생성된 경우에는 **스토리지 키** 드롭다운에서 키를 다시 구성해야 합니다. |
   | **두 번째 스토리지 계정** | 드롭다운하고 보조 스토리지 계정을 선택합니다(선택 사항). | 필요에 따라 다른 스토리지 계정을 구성할 수 있습니다. 두 번째 스토리지 계정이 구성된 경우 복제본 캐시에 대한 쓰기는 이 두 번째 스토리지 계정에 기록됩니다. |
   | **두 번째 스토리지 키** | 드롭다운하고 사용할 **기본 키** 또는 **보조 키** 중 하나를 선택합니다(선택 사항). | 지속성 계정에 대한 스토리지 키가 다시 생성된 경우에는 **스토리지 키** 드롭다운에서 키를 다시 구성해야 합니다. |

    AOF 지속성을 사용하도록 설정하여 캐시에 대한 쓰기 작업이 지정된 스토리지 계정(두 번째 스토리지 계정을 구성한 경우 해당 계정도 포함)에 저장됩니다. 주 캐시와 복제본 캐시 둘 다에서 심각한 오류가 발생하면 저장된 AOF 로그가 캐시를 다시 작성하는 데 사용됩니다.

10. **다음: 태그** 탭을 선택하거나 페이지 맨 아래에서 **다음: 태그** 단추를 선택합니다.

11. 필요에 따라 리소스를 분류하려는 경우 **태그** 탭에서 이름 및 값을 입력합니다.

12. **검토 + 만들기** 를 선택합니다. 검토 + 만들기 탭으로 이동됩니다. 여기서 구성이 유효한지 검사됩니다.

13. 녹색 유효성 검사 통과 메시지가 표시되면 **만들기** 를 선택합니다.

캐시를 만드는 데 잠시 시간이 걸립니다. Azure Cache for Redis **개요** 페이지에서 진행률을 모니터링할 수 있습니다. **상태** 가 **실행 중** 으로 표시되면 캐시를 사용할 준비가 된 것입니다.

## <a name="persistence-faq"></a>지속성 FAQ

Azure Cache for Redis 지속성에 대해 자주 묻는 질문과 대답이 나와 있는 목록은 다음과 같습니다.

* [이전에 만든 캐시에서 지속성을 사용할 수 있나요?](#can-i-enable-persistence-on-a-previously-created-cache)
* [AOF 및 RDB 지속성을 동시에 사용할 수 있나요?](#can-i-enable-aof-and-rdb-persistence-at-the-same-time)
* [어떤 지속성 모델을 선택해야 하나요?](#which-persistence-model-should-i-choose)
* [다른 크기로 확장했고 크기 조정 작업 전에 만들어진 백업을 복원할 경우 어떻게 되나요?](#what-happens-if-ive-scaled-to-a-different-size-and-a-backup-is-restored-that-was-made-before-the-scaling-operation)
* [서로 다른 두 캐시의 지속성에 대해 동일한 스토리지 계정을 사용할 수 있나요?](#can-i-use-the-same-storage-account-for-persistence-across-two-different-caches)
* [데이터 지속성에 사용되는 스토리지에 대한 요금이 청구되나요?](#will-i-be-charged-for-the-storage-being-used-in-data-persistence)

### <a name="rdb-persistence"></a>RDB 지속성

* [캐시를 만든 후 RDB 백업 주기를 변경할 수 있나요?](#can-i-change-the-rdb-backup-frequency-after-i-create-the-cache)
* [RDB 백업 주기가 60분인데 왜 백업 사이 간격이 60분 이상이 되나요?](#why-is-there-more-than-60-minutes-between-backups-when-i-have-an-rdb-backup-frequency-of-60-minutes)
* [새 백업을 만들면 이전 RDB 백업은 어떻게 되나요?](#what-happens-to-the-old-rdb-backups-when-a-new-backup-is-made)

### <a name="aof-persistence"></a>AOF 지속성

* [두 번째 스토리지 계정은 언제 사용해야 하나요?](#when-should-i-use-a-second-storage-account)
* [AOF 지속성이 내 캐시의 처리량, 대기 시간 또는 성능에 영향을 미치나요?](#does-aof-persistence-affect-throughout-latency-or-performance-of-my-cache)
* [두 번째 스토리지 계정은 어떻게 제거할 수 있나요?](#how-can-i-remove-the-second-storage-account)
* [다시 쓰기란 무엇이며 내 캐시에 어떤 영향을 미치나요?](#what-is-a-rewrite-and-how-does-it-affect-my-cache)
* [AOF가 설정된 캐시의 크기를 조정하는 경우 어떻게 되나요?](#what-should-i-expect-when-scaling-a-cache-with-aof-enabled)
* [스토리지에서 AOF 데이터는 어떤 방식으로 구성되나요?](#how-is-my-aof-data-organized-in-storage)

### <a name="can-i-enable-persistence-on-a-previously-created-cache"></a>이전에 만든 캐시에서 지속성을 사용할 수 있나요?

예, Redis 지속성은 캐시를 만들 때와 기존 프리미엄 캐시에서 모두 구성할 수 있습니다.

### <a name="can-i-enable-aof-and-rdb-persistence-at-the-same-time"></a>AOF 및 RDB 지속성을 동시에 사용할 수 있나요?

아니요. RDB 또는 AOF를 사용하도록 설정할 수 있지만 동시에 둘 다 설정할 수는 없습니다.

### <a name="which-persistence-model-should-i-choose"></a>어떤 지속성 모델을 선택해야 하나요?

AOF 지속성은 모든 쓰기를 로그에 저장하여 처리량에 상당한 영향을 주며, 구성된 백업 간격에 따라 백업을 저장하고 성능에 미치는 영향을 최소화하면서 AOF를 RDB 지속성과 비교합니다. 기본 목표가 데이터 손실을 최소화하는 것이며 캐시에 대해 낮은 처리량을 처리할 수 있는 경우에는 AOF 지속성을 선택합니다. 캐시에 대해 최적의 처리량을 유지하려고 하면서도 데이터 복구 메커니즘을 계속 유지하려는 경우에는 RDB 지속성을 선택합니다.

* RDB 지속성의 [장점](https://redis.io/topics/persistence#rdb-advantages) 및 [단점](https://redis.io/topics/persistence#rdb-disadvantages)에 대해 자세히 알아봅니다.
* AOF 지속성의 [장점](https://redis.io/topics/persistence#aof-advantages) 및 [단점](https://redis.io/topics/persistence#aof-disadvantages)에 대해 자세히 알아봅니다.

AOF 지속성을 사용하는 경우 성능에 대한 자세한 내용은 [AOF 지속성이 내 캐시의 처리량, 대기 시간 또는 성능에 영향을 미치나요?](#does-aof-persistence-affect-throughout-latency-or-performance-of-my-cache)를 참조하세요.

### <a name="what-happens-if-ive-scaled-to-a-different-size-and-a-backup-is-restored-that-was-made-before-the-scaling-operation"></a>다른 크기로 확장했고 크기 조정 작업 전에 만들어진 백업을 복원할 경우 어떻게 되나요?

RDB 및 AOF 지속성:

* 크기를 더 크게 조정한 경우에는 아무 효과가 없습니다.
* 더 작은 크기를 조정했고 새 크기에 대한 [데이터베이스 제한](cache-configure.md#databases)보다 사용자 지정 [데이터베이스](cache-configure.md#databases) 설정이 더 크다면, 그러한 데이터베이스에 저장된 데이터는 복원되지 않습니다. 자세한 내용은 [사용자 지정 데이터베이스 설정이 크기 조정 동안 영향을 받나요?](cache-how-to-scale.md#is-my-custom-databases-setting-affected-during-scaling)
* 더 작은 크기로 확장하고 마지막 백업의 모든 데이터를 저장할 수 있는 작은 크기의 공간이 충분하지 않으면 복원 프로세스 중에 키가 제거됩니다.  일반적으로 키는 [allkeys-lru](https://redis.io/topics/lru-cache) 제거 정책을 사용하여 제거됩니다.

### <a name="can-i-use-the-same-storage-account-for-persistence-across-two-different-caches"></a>서로 다른 두 캐시의 지속성에 대해 동일한 스토리지 계정을 사용할 수 있나요?

예, 서로 다른 두 캐시의 지속성에 대해 동일한 저장소 계정을 사용할 수 있습니다.

### <a name="can-i-change-the-rdb-backup-frequency-after-i-create-the-cache"></a>캐시를 만든 후 RDB 백업 주기를 변경할 수 있나요?

예, 왼쪽의 **데이터 지속성** 에서 RDB 지속성의 백업 주기를 변경할 수 있습니다. 자세한 내용은 Redis 지속성 구성을 참조하세요.

### <a name="why-is-there-more-than-60-minutes-between-backups-when-i-have-an-rdb-backup-frequency-of-60-minutes"></a>RDB 백업 주기가 60분인데 왜 백업 사이 간격이 60분 이상이 되나요?

RDB 지속성 백업 간격의 주기는 이전 백업 프로세스가 성공적으로 완료되어야 시작됩니다. 백업 간격이 60분이고 백업 프로세스를 완료하는 데 15분이 걸린다면 다음 백업은 이전 백업 시작 시점에서 75분까지 시작되지 않습니다.

### <a name="what-happens-to-the-old-rdb-backups-when-a-new-backup-is-made"></a>새 백업을 만들면 이전 RDB 백업은 어떻게 되나요?

가장 최근 백업을 제외한 모든 RDB 지속성 백업은 자동으로 삭제됩니다. 즉시 삭제되지 않을 수 있으나 오래된 백업을 무한정 유지하지는 않습니다. 스토리지 계정에 대해 일시 삭제를 켜면 일시 삭제 설정이 적용되고 기존 백업은 일시 삭제 상태로 계속 상주합니다.

### <a name="when-should-i-use-a-second-storage-account"></a>두 번째 스토리지 계정은 언제 사용해야 하나요?

AOF 지속성이 캐시에 대해 예상된 설정 작업보다 더 높다고 판단되면 AOF 지속성에 대해 두 번째 스토리지 계정을 사용합니다.  보조 스토리지 계정을 설정하면 캐시가 스토리지 대역폭 제한에 도달하지 않도록 하는 데 도움이 됩니다.

### <a name="does-aof-persistence-affect-throughout-latency-or-performance-of-my-cache"></a>AOF 지속성이 내 캐시의 처리량, 대기 시간 또는 성능에 영향을 미치나요?

AOF 지속성은 캐시가 최대 부하보다 낮을 때(CPU 및 서버 부하 둘 다에서 90% 미만) 처리량에 15%-20% 정도 영향을 미칩니다. 캐시가 이러한 한도 내에 있을 때 대기 시간 문제는 없습니다. 그러나 캐시는 AOF가 사용하도록 설정되면 더 빨리 이러한 한도에 도달합니다.

### <a name="how-can-i-remove-the-second-storage-account"></a>두 번째 스토리지 계정은 어떻게 제거할 수 있나요?

두 번째 스토리지 계정을 첫 번째 스토리지 계정과 동일하게 설정하여 AOF 지속성 보조 스토리지 계정을 제거할 수 있습니다. 기존 캐시의 경우 캐시의 **리소스 메뉴** 에서 왼쪽의 **데이터 지속성** 에 액세스합니다. AOF 지속성을 사용하지 않도록 설정하려면 **사용 안 함** 을 선택합니다.

### <a name="what-is-a-rewrite-and-how-does-it-affect-my-cache"></a>다시 쓰기란 무엇이며 내 캐시에 어떤 영향을 미치나요?

AOF 파일이 충분히 커지면 다시 쓰기가 자동으로 큐에 대기됩니다. 다시 쓰기를 수행하면 현재 데이터 집합을 만드는 데 필요한 작업의 최소 집합을 사용하여 AOF 파일 크기가 다시 조정됩니다. 다시 쓰기 동안, 성능 제한에 더 빠르게 도달할 수 있으며 큰 데이터 세트를 처리할 때 특히 더 그렇습니다. AOF 파일이 더 커지면 다시 쓰기는 덜 자주 발생하지만 일단 발생하면 많은 시간이 소요됩니다.

### <a name="what-should-i-expect-when-scaling-a-cache-with-aof-enabled"></a>AOF가 설정된 캐시의 크기를 조정하는 경우 어떻게 되나요?

크기 조정이 완료된 후에 파일이 다시 로드되므로 크기 조정 시 AOF 파일이 아주 커질 경우 크기 조정 작업이 예상한 것보다 더 오래 걸립니다.

크기 조정에 대한 자세한 내용은 [다른 크기로 확장했고 크기 조정 작업 전에 만들어진 백업을 복원할 경우 어떻게 되나요?](#what-happens-if-ive-scaled-to-a-different-size-and-a-backup-is-restored-that-was-made-before-the-scaling-operation)를 참조하세요.

### <a name="how-is-my-aof-data-organized-in-storage"></a>스토리지에서 AOF 데이터는 어떤 방식으로 구성되나요?

AOF 파일에 저장된 데이터는 스토리지에 데이터를 저장하는 성능을 향상시키기 위해 노드당 여러 페이지 Blob으로 구분됩니다. 다음 표에는 각 가격 책정 계층에 사용되는 페이지 Blob 수가 표시됩니다.

| 프리미엄 계층 | Blob |
|--------------|-------|
| P1           | 분할당 4    |
| P2           | 분할당 8    |
| P3           | 분할당 16   |
| P4           | 분할당 20   |

클러스터링을 사용하도록 설정하면 캐시의 각 분할은 이전 표에 표시된 것처럼 자체 페이지 Blob 집합을 갖습니다. 예를 들어 분할이 3개 있는 P2 캐시는 24개 페이지 Blob에 해당 AOF 파일을 분산합니다(분할된 데이터베이스당 8개 Blob, 3개 분할된 데이터베이스).

다시 쓰기 후 2개의 AOF 파일 집합이 스토리지에 존재합니다. 다시 쓰기 작업은 백그라운드에서 발생하며 첫 번째 파일 세트에 추가됩니다. 다시 쓰는 동안 캐시로 전송되는 설정 작업을 두 번째 집합에 추가합니다. 오류가 발생하는 경우 다시 쓰는 동안 백업이 일시적으로 저장됩니다. 다시 쓰기 작업이 완료되면 백업이 즉시 삭제됩니다. 스토리지 계정에 대해 일시 삭제를 켜면 일시 삭제 설정이 적용되고 기존 백업은 일시 삭제 상태로 계속 상주합니다.

### <a name="will-i-be-charged-for-the-storage-being-used-in-data-persistence"></a>데이터 지속성에 사용되는 스토리지에 대한 요금이 청구되나요?

예, 사용 중인 스토리지 계정에 대한 가격 책정 모델에 따라 사용되는 스토리지에 대한 요금이 청구됩니다.

## <a name="next-steps"></a>다음 단계

Azure Cache for Redis 기능에 대해 자세히 알아보세요.

* [Azure Cache for Redis 프리미엄 서비스 계층](cache-overview.md#service-tiers)

<!-- IMAGES -->

[redis-cache-premium-pricing-tier]: ./media/cache-how-to-premium-persistence/redis-cache-premium-pricing-tier.png

[redis-cache-persistence]: ./media/cache-how-to-premium-persistence/redis-cache-persistence.png

[redis-cache-rdb-persistence]: ./media/cache-how-to-premium-persistence/redis-cache-rdb-persistence.png

[redis-cache-aof-persistence]: ./media/cache-how-to-premium-persistence/redis-cache-aof-persistence.png

[redis-cache-settings]: ./media/cache-how-to-premium-persistence/redis-cache-settings.png
