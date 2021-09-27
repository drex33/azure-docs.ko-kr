---
title: Azure Cache for Redis로 마이그레이션
description: 기존 캐시를 Azure Cache for Redis 마이그레이션하는 방법을 알아봅니다.
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 07/22/2020
ms.author: yegu
ms.openlocfilehash: bd0e35105174cf64146cb27f75693e8348b1035e
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124824992"
---
# <a name="migrate-to-azure-cache-for-redis"></a>Azure Cache for Redis로 마이그레이션

이 문서에서는 온-프레미스 또는 다른 클라우드 서비스에서 실행되는 기존 Redis Cache를 Azure Cache for Redis 마이그레이션하는 다양한 방법을 설명합니다.

## <a name="migration-scenarios"></a>마이그레이션 시나리오

오픈 소스 Redis는 많은 컴퓨팅 환경에서 실행할 수 있습니다. 일반적인 예제는 다음을 포함합니다.

- **온-프레미스** - 프라이빗 데이터 센터에서 실행되는 Redis 캐시입니다.
- **클라우드 기반 VM** - Azure VM, AWS EC2 등에서 실행되는 Redis 캐시
- **호스팅 서비스** - AWS ElastiCache와 같은 관리되는 Redis 서비스입니다.
- **다른 지역** - 다른 Azure 지역에 있는 Redis 캐시입니다.

이러한 캐시가 있는 경우 중단 또는 가동 중지 시간을 최소화하면서 Azure Cache for Redis 이동할 수 있습니다.

## <a name="migration-options"></a>마이그레이션 옵션

여러 가지 방법으로 한 캐시에서 다른 캐시로 전환할 수 있습니다. 가장 효율적인 방법은 캐시의 위치 및 애플리케이션이 캐시와 상호 작용하는 방식에 따라 다릅니다. 아래에는 자주 사용되는 마이그레이션 전략 중 일부가 설명되어 있습니다.

   | 옵션       | 장점 | 단점 |
   | ------------ | ---------- | ------------- |
   | 새 캐시 만들기 | 가장 간단하게 구현할 수 있습니다. | 많은 애플리케이션에서 작동하지 않을 수 있는 새 캐시에 데이터를 다시 채워야 합니다. |
   | RDB 파일을 통해 데이터 내보내기 및 가져오기 | 일반적으로 Redis Cache와 호환 가능합니다. | RDB 파일이 생성된 후 데이터가 기존 캐시에 기록되는 경우 일부 데이터가 손실될 수 있습니다. | 
   | 2개의 캐시에 데이터 이중 쓰기 | 데이터 손실 또는 가동 중지 시간 없음 기존 캐시의 작업 중단이 없습니다. 새 캐시를 간편하게 테스트할 수 있습니다. | 긴 시간 동안 2개의 캐시가 필요합니다. | 
   | 프로그래밍 방식으로 데이터 마이그레이션 | 데이터 이동 방법을 완벽하게 제어할 수 있습니다. | 사용자 지정 코드가 필요합니다. | 

### <a name="create-a-new-azure-cache-for-redis"></a>새 Azure Cache for Redis 만들기

이 방법은 엄밀히 따지면 마이그레이션이 아닙니다. 데이터 손실이 걱정되지 않는 경우 Azure Cache for Redis로 데이터를 이동하는 가장 쉬운 방법은 캐시 인스턴스를 만들고 애플리케이션을 여기에 연결하는 것입니다. 예를 들어 Redis를 데이터베이스 레코드의 조회 캐시로 사용하는 경우 간단하게 캐시를 처음부터 다시 작성할 수 있습니다.

이 옵션을 구현하는 일반적인 단계는 다음과 같습니다.

1. 새 Azure Cache for Redis 인스턴스를 만듭니다.

2. 새 인스턴스를 사용하도록 애플리케이션을 업데이트합니다.

3. 이전 Redis 인스턴스를 삭제합니다.

### <a name="export-data-to-an-rdb-file-and-import-it-into-azure-cache-for-redis"></a>RDB 파일로 데이터를 내보내고 Azure Cache for Redis

오픈 소스 Redis는 캐시의 메모리 내 데이터 세트 스냅샷을 만들어서 파일에 저장하는 표준 메커니즘을 정의합니다. 이 RDB 파일은 다른 Redis 캐시에서 읽을 수 있습니다. [Azure Cache for Redis 프리미엄 계층](cache-overview.md#service-tiers)은 RDB 파일을 통해 캐시 인스턴스로 데이터 가져오기를 지원합니다. RDB 파일을 사용하여 기존 캐시에서 Azure Cache for Redis로 데이터를 전송할 수 있습니다.

> [!IMPORTANT]
> RDB 파일 형식은 Redis 버전에 따라 변경될 수 있으며 이전 버전과의 호환성이 유지되지 않을 수 있습니다. 데이터를 내보내려는 캐시의 Redis 버전은 Azure Cache for Redis에서 제공하는 버전과 같거나 낮아야 합니다.
>

이 옵션을 구현하는 일반적인 단계는 다음과 같습니다.

1. 프리미엄 계층에서 기존 캐시와 크기가 같거나 보다 큰 새 Azure Cache for Redis 인스턴스를 만듭니다.

2. 기존 Redis 캐시의 스냅샷을 저장합니다. 주기적으로 [스냅샷을 저장하도록 Redis를 구성](https://redis.io/topics/persistence)할 수도 있고, [SAVE](https://redis.io/commands/save) 또는 [BGSAVE](https://redis.io/commands/bgsave) 명령을 사용하여 프로세스를 수동으로 실행할 수도 있습니다. RDB 파일의 이름은 기본적으로 “dump.rdb”이며, *redis.conf* 구성 파일에 지정된 경로에 배치됩니다.

    > [!NOTE]
    > Azure Cache for Redis 내에서 데이터를 마이그레이션하는 경우 [RDB 파일을 내보내는 방법에 대한 지침](cache-how-to-import-export-data.md)을 참조하거나 [PowerShell Export cmdlet](/powershell/module/azurerm.rediscache/export-azurermrediscache)을 대신 사용하세요.
    >

3. 새 캐시가 있는 지역의 Azure 스토리지 계정에 RDB 파일을 복사합니다. 이 작업에는 AzCopy를 사용하면 됩니다.

4. [가져오기 지침](cache-how-to-import-export-data.md) 또는 [PowerShell Import cmdlet](/powershell/module/azurerm.rediscache/import-azurermrediscache)을 사용하여 RDB 파일을 새 캐시로 가져옵니다.

5. 새 캐시 인스턴스를 사용하도록 애플리케이션을 업데이트합니다.

### <a name="write-to-two-redis-caches-simultaneously-during-migration-period"></a>마이그레이션 기간 동안 동시에 2개의 Redis 캐시에 쓰기

캐시 간에 직접 데이터를 이동하는 대신, 애플리케이션을 사용하여 기존 캐시 및 설정 중인 새 캐시에 데이터를 쓸 수 있습니다. 애플리케이션은 처음에는 여전히 기존 캐시에서 데이터를 읽습니다. 새 캐시에 필요한 데이터가 생기면 애플리케이션을 해당 캐시로 전환하고 이전 캐시를 사용 중지합니다. 예를 들어 Redis를 세션 저장소로 사용하며, 애플리케이션 세션은 7일 동안 유효하다고 가정해 보겠습니다. 일주일 동안 2개의 캐시에 쓰면 새 캐시에 만료되지 않은 모든 세션 정보가 포함됩니다. 해당 시점부터 데이터 손실에 대한 걱정 없이 안전하게 새 캐시를 사용할 수 있습니다.

이 옵션을 구현하는 일반적인 단계는 다음과 같습니다.

1. 프리미엄 계층에서 기존 캐시와 크기가 같거나 보다 큰 새 Azure Cache for Redis 인스턴스를 만듭니다.

2. 새 인스턴스와 원래 인스턴스 모두에 쓰도록 애플리케이션 코드를 수정합니다.

3. 새 인스턴스에 데이터가 충분히 채워질 때까지 계속해서 원래 인스턴스에서 데이터를 읽습니다.

4. 새 인스턴스에서만 데이터를 읽고 쓰도록 애플리케이션 코드를 업데이트합니다.

5. 원래 인스턴스를 삭제합니다.

### <a name="migrate-programmatically"></a>프로그래밍 방식으로 마이그레이션

기존 캐시에서 데이터를 프로그래밍 방식으로 읽고 Azure Cache for Redis 작성하여 사용자 지정 마이그레이션 프로세스를 만들 수 있습니다. 이 [오픈 소스 도구](https://github.com/deepakverma/redis-copy)를 사용하여 한 Azure Cache for Redis 인스턴스에서 다른 인스턴스로 데이터를 복사할 수 있습니다. 이 도구는 서로 다른 Azure 캐시 지역에 있는 캐시 인스턴스 간에 데이터를 이동하는 데 유용합니다. [컴파일된 버전](https://github.com/deepakverma/redis-copy/releases/download/alpha/Release.zip)도 사용할 수 있습니다. 또한 고객 고유의 마이그레이션 도구를 작성하는 데 도움이 되는 소스 코드도 찾을 수 있습니다.

> [!NOTE]
> 이 도구는 Microsoft에서 공식적으로 지원하는 도구가 아닙니다. 
>

이 옵션을 구현하는 일반적인 단계는 다음과 같습니다.

1. 기존 캐시가 있는 지역에 VM을 만듭니다. 데이터 세트가 크면 비교적 강력한 VM을 선택하여 복사 시간을 줄입니다.

2. 새 Azure Cache for Redis 인스턴스를 만듭니다.

3. 새 캐시의 데이터를 플러시하여 캐시를 비웁니다. 복사 도구 자체는 대상 캐시의 기존 키를 덮어쓰지 않기 때문에 이 단계가 필수입니다.

    > [!IMPORTANT]
    > 원본 캐시의 데이터는 플러시하지 않습니다.
    >

4. 위의 오픈 소스 도구와 같은 애플리케이션을 사용하여 원본 캐시에서 대상으로 데이터 복사를 자동화합니다. 데이터 세트의 크기에 따라 복사 프로세스가 완료될 때까지 다소 시간이 걸릴 수 있습니다.

## <a name="next-steps"></a>다음 단계
Azure Cache for Redis 기능에 대해 자세히 알아보세요.

* [Azure Cache for Redis - 서비스 계층](cache-overview.md#service-tiers)
* [데이터 가져오기](cache-how-to-import-export-data.md#import)