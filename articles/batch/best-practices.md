---
title: 모범 사례
description: Azure Batch 솔루션을 개발하기 위한 모범 사례와 유용한 팁에 대해 알아봅니다.
ms.date: 04/29/2021
ms.topic: conceptual
ms.openlocfilehash: db67f91257db1ddb44f7dfb103ac02e49febe562
ms.sourcegitcommit: 695a33a2123429289ac316028265711a79542b1c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/01/2021
ms.locfileid: "113126467"
---
# <a name="azure-batch-best-practices"></a>Azure Batch 모범 사례

이 문서에서는 Azure Batch 서비스를 효과적이고 사용하기 위한 모범 사례와 유용한 팁에 대해 설명합니다. 이러한 팁은 Batch 솔루션에서 성능을 향상시키고 디자인 문제를 방지하는 데 유용합니다.

> [!TIP]
> Azure Batch의 보안 관련 지침은 [Batch 보안 및 규정 준수 모범 사례](security-best-practices.md)를 참조하세요.

## <a name="pools"></a>풀

[풀](nodes-and-pools.md#pools)은 Batch 서비스에서 작업을 실행하기 위한 컴퓨팅 리소스입니다. 다음 섹션에서는 Batch 풀 작업에 대한 추천 사항을 제공합니다.

### <a name="pool-configuration-and-naming"></a>풀 구성 및 이름 지정

- **풀 할당 모드:** Batch 계정을 만들 때 **Batch 서비스** 또는 **사용자 구독** 의 두 가지 풀 할당 모드 중에서 선택할 수 있습니다. 대부분의 경우 풀이 Batch 관리형 구독에서 내부적으로 할당되는 기본 Batch 서비스를 사용해야 합니다. 대체 사용자 구독 모드인 경우 Batch VM 및 기타 리소스는 풀이 만들어질 때 구독에서 직접 만들어집니다. 사용자 구독 계정은 주로 시나리오의 작지만 중요한 하위 집합을 사용하도록 설정하는 데 사용됩니다. 자세한 내용은 [사용자 구독 모드에 대한 추가 구성](batch-account-create-portal.md#additional-configuration-for-user-subscription-mode)을 참조하세요.

- **'virtualMachineConfiguration' 또는 'cloudServiceConfiguration':** 이러한 구성 중 하나를 사용하여 풀을 만들 수 있지만 'cloudServiceConfiguration'이 아닌 'virtualMachineConfiguration'을 사용하여 새 풀을 구성해야 합니다. Batch의 모든 현재 및 새 기능은 가상 머신 구성 풀에서 지원됩니다. Cloud Services 구성 풀은 모든 기능을 지원하지 않으며 새로운 기능도 계획되어 있지 않습니다. [2024년 2월 29일 이후](https://azure.microsoft.com/updates/azure-batch-cloudserviceconfiguration-pools-will-be-retired-on-29-february-2024/) 새 ‘cloudServiceConfiguration’ 풀을 생성하거나 기존 풀에 새 노드를 추가할 수 없습니다. 자세한 내용은 [Cloud Services에서 가상 머신으로 Batch 풀 구성 마이그레이션](batch-pool-cloud-service-to-virtual-machine-configuration.md)을 참조하세요.

- **작업 및 태스크 실행 시간 고려 사항:** 주로 단기 실행 태스크로 구성된 작업이 있고 예상 총 태스크 수가 작으므로 전체 예상 작업 실행 시간이 길지 않은 경우 새 풀을 각 작업에 할당하지 않습니다. 노드의 할당 시간은 작업 실행 시간을 줄입니다.

- **여러 컴퓨팅 노드:** 개별 노드를 항상 사용할 수 있는 것은 아닙니다. 흔치 않은 경우이지만 하드웨어 오류, 운영 체제 업데이트 및 기타 여러 가지 문제로 인해 개별 노드가 오프라인 상태가 될 수 있습니다. Batch 워크로드에 결정적이고 보장된 진행률이 필요한 경우 여러 노드가 있는 풀을 할당해야 합니다.

- **수명 종료(EOL) 날짜가 임박한 이미지:** Batch 지원 수명 종료(EOL) 날짜가 임박한 이미지는 사용하지 않는 것이 좋습니다. 이러한 날짜는 [`ListSupportedImages` API](/rest/api/batchservice/account/listsupportedimages), [PowerShell](/powershell/module/az.batch/get-azbatchsupportedimage) 또는 [Azure CLI](/cli/azure/batch/pool/supported-images)를 통해 검색할 수 있습니다. 풀에 해당하는 EOL 날짜의 보기를 정기적으로 새로 고치고 EOL 날짜가 되기 전에 워크로드를 마이그레이션하는 것은 사용자의 책임입니다. 지정된 노드 에이전트에서 사용자 지정 이미지를 사용하는 경우 사용자 지정 이미지가 파생되거나 정렬된 이미지에 대한 Batch 지원 수명 종료(EOL) 날짜를 준수해야 합니다.

- **고유한 리소스 이름**: Batch 리소스(작업, 풀 등)는 시간이 지남에 따라 변하는 경우가 많습니다. 예를 들어 월요일에 풀을 만들고, 화요일에 풀을 삭제한 다음, 목요일에 더 작은 다른 풀을 만들 수 있습니다. 새로 만드는 각 리소스에는 이전에 사용하지 않은 고유한 이름을 지정해야 합니다. 이 작업은 GUID(를 전체 리소스 이름 또는 그 일부로)를 사용하거나 리소스를 만든 날짜와 시간을 리소스 이름에 포함하여 수행할 수 있습니다. Batch는 [DisplayName](/dotnet/api/microsoft.azure.batch.jobspecification.displayname)을 지원합니다. 이를 통해 실제 리소스 ID가 그다지 친숙하지 않은 경우에도 리소스에 읽기 가능한 이름을 지정할 수 있습니다. 고유한 이름을 사용하면 로그 및 메트릭에서 작업을 수행한 특정 리소스를 쉽게 구분할 수 있습니다. 또한 리소스에 대한 지원 사례를 제출해야 하는 경우 모호성도 제거됩니다.

- **풀 유지 관리 및 실패 시 연속성:** 작업에서 풀을 동적으로 사용하도록 하는 것이 가장 좋습니다. 작업에서 동일한 풀을 모든 대상에 사용하는 경우 풀에 문제가 있으면 작업이 실행되지 않을 수 있습니다. 이는 시간이 중요한 워크로드에 특히 중요합니다. 이 문제를 해결하려면 각 작업을 예약할 때 풀을 동적으로 선택하거나 만들거나, 비정상 풀을 무시할 수 있도록 풀 이름을 재정의하는 방법을 사용합니다.

- **풀 유지 관리 및 실패 시 비즈니스 연속성:** 내부 오류, 용량 제약 조건 등과 같이 풀이 필요한 크기로 조정되지 않도록 하는 여러 가지 원인이 있습니다. 필요한 경우 다른 풀(다른 VM 크기일 수 있음. Batch에서 [UpdateJob](/dotnet/api/microsoft.azure.batch.protocol.joboperationsextensions.update)을 통해 이를 지원함)에서 작업의 대상을 변경할 수 있어야 합니다. 정적 풀 ID는 절대로 삭제되거나 변경되지 않는다는 가정 하에 사용하지 않도록 합니다.

### <a name="pool-lifetime-and-billing"></a>풀 수명 및 청구

풀 수명은 풀 구성에 적용되는 할당 방법 및 옵션에 따라 달라질 수 있습니다. 풀의 수명은 임의로 지정할 수 있으며, 컴퓨팅 노드 수는 언제든지 달라질 수 있습니다. 사용자가 풀의 컴퓨팅 노드를 명시적으로 관리하거나 서비스에서 제공하는 기능([자동 스케일링](nodes-and-pools.md#automatic-scaling-policy) 또는 [자동 풀](nodes-and-pools.md#autopools))을 통해 관리해야 합니다.

- **풀 새로 고침:** [최신 노드 에이전트 업데이트 및 버그 수정](https://github.com/Azure/Batch/blob/master/changelogs/nodeagent/CHANGELOG.md)을 얻으려면 몇 개월마다 풀 크기를 0으로 조정합니다. 풀을 다시 만들거나 풀 크기를 0개의 컴퓨팅 노드로 조정하지 않으면 풀에서 노드 에이전트 업데이트를 받지 않습니다. 풀을 다시 만들거나 크기를 조정하기 전에 [노드](#nodes) 섹션에서 설명한 대로 디버깅을 위해 노드 에이전트 로그를 다운로드해야 합니다.

- **풀 다시 만들기:** 마찬가지로 매일 풀을 삭제하고 다시 만들지 않도록 합니다. 대신 새 풀을 만들고 기존 작업에서 새 풀을 가리키도록 업데이트합니다. 모든 태스크가 새 풀로 이동되었으면 이전 풀을 삭제합니다.

- **풀 효율성 및 청구:** Batch 자체에는 추가 요금이 부과되지 않지만 사용된 컴퓨팅 리소스에는 요금이 부과됩니다. 상태에 관계없이 풀의 모든 컴퓨팅 노드에 대해 요금이 청구됩니다. 여기에는 스토리지 및 네트워킹 비용과 같이 노드를 실행하는 데 필요한 요금이 포함됩니다. 자세한 내용은 [Azure Batch 비용 분석 및 예산](budget.md)을 참조하세요.

### <a name="pool-allocation-failures"></a>풀 할당 실패

풀 할당 실패는 첫 번째 할당 또는 후속 크기 조정 중에 언제든지 발생할 수 있습니다. 이는 한 지역의 일시적인 용량 고갈 또는 Batch에서 사용하는 다른 Azure 서비스의 오류 때문일 수 있습니다. 코어 할당량은 보장이 아니라 오히려 한도입니다.

### <a name="unplanned-downtime"></a>계획되지 않은 가동 중지 시간

Batch 풀에서 Azure의 가동 중지 시간 이벤트를 경험할 수 있습니다. Batch 시나리오 또는 워크플로를 계획하고 개발하는 경우 이 점을 명심해야 합니다. 노드가 실패하면 Batch에서 자동으로 사용자를 대신하여 이러한 컴퓨팅 노드를 복구하려고 시도합니다. 이 경우 복구된 노드에서 실행되는 모든 태스크를 다시 예약하는 작업이 트리거될 수 있습니다. 중단되는 태스크에 대한 자세한 내용은 [다시 시도 디자인](#design-for-retries-and-re-execution)을 참조하세요.

### <a name="custom-image-pools"></a>사용자 지정 이미지 풀

Virtual Machine 구성을 사용하여 Azure Batch 풀을 만들 경우 풀에서 각 컴퓨팅 노드에 대해 운영 체제를 제공하는 VM 이미지를 지정합니다. 지원되는 Azure Marketplace 이미지로 풀을 만들거나 [Shared Image Gallery 이미지를 사용하여 사용자 지정 이미지를 만들 수 있습니다.](batch-sig-images.md) [관리형 이미지](batch-custom-images.md)를 사용하여 사용자 지정 이미지 풀을 만들 수도 있지만 가능하면 항상 Shared Image Gallery를 사용하여 사용자 지정 이미지를 만드는 것이 좋습니다. Shared Image Gallery를 사용하면 풀을 더 빠르게 프로비저닝하고, 더 많은 수의 VM을 스케일링하고, VM을 프로비저닝할 때 안정성을 개선할 수 있습니다.

### <a name="third-party-images"></a>타사 이미지

Azure Marketplace에 게시된 타사 이미지를 사용하여 풀을 만들 수 있습니다. 사용자 구독 모드 Batch 계정을 사용하면 특정 타사 이미지를 사용하여 풀을 만들 때 "Marketplace 구매 자격 확인으로 인해 할당하지 못했습니다."라는 오류가 표시될 수 있습니다. 이 오류를 해결하려면 이미지 게시자가 설정한 약관에 동의합니다. 이 작업은 [Azure PowerShell](/powershell/module/azurerm.marketplaceordering/set-azurermmarketplaceterms) 또는 [Azure CLI](/cli/azure/vm/image/terms)를 사용하여 수행할 수 있습니다.

### <a name="azure-region-dependency"></a>Azure 지역 종속성

시간이 중요한 워크로드 또는 프로덕션 워크로드가 있는 경우 단일 Azure 지역에 의존하지 않아야 합니다. 흔치 않은 경우이지만 전체 지역에 영향을 줄 수 있는 문제가 있습니다. 예를 들어 처리를 특정 시간에 시작해야 하는 경우 *시작 시간 훨씬 전에* 주 지역의 풀 크기를 강화하는 것이 좋습니다. 풀 크기 조정이 실패하면 하나 이상의 백업 지역에서 풀 크기를 강화하는 것으로 대체할 수 있습니다.

다른 풀에서 문제가 발생하면 다른 지역의 여러 계정에 걸쳐 있는 풀에서 쉽게 액세스할 수 있는 준비된 백업을 제공합니다. 자세한 내용은 [고가용성 애플리케이션 디자인](high-availability-disaster-recovery.md)을 참조하세요.

## <a name="jobs"></a>작업

[작업](jobs-and-tasks.md#jobs)은 수백, 수천, 심지어 수백만 개의 태스크를 포함하도록 설계된 컨테이너입니다. 작업을 만드는 경우 다음 지침을 따릅니다.

### <a name="fewer-jobs-more-tasks"></a>더 적은 작업, 더 많은 태스크

작업을 사용하여 단일 태스크를 실행하는 것은 비효율적입니다. 예를 들어 각각 10개의 태스크가 포함된 100개의 작업을 만드는 대신 1,000개의 태스크가 포함된 단일 작업을 사용하는 것이 더 효율적입니다. 각각 단일 태스크가 포함된 1,000개의 작업을 실행하는 것이 가장 비효율적이고 느리며 비용이 많이 드는 방법입니다.

따라서 수천 개의 동시 활성 작업이 필요한 Batch 솔루션을 설계하지 않아야 합니다. 태스크에 대한 할당량이 없으므로 가능한 한 적은 수의 작업에서 많은 태스크를 실행하면 [작업 및 작업 일정 할당량](batch-quota-limit.md#resource-quotas)을 효율적으로 사용할 수 있습니다.

### <a name="job-lifetime"></a>작업 수명

Batch 작업의 수명은 시스템에서 해당 작업이 삭제될 때까지 무한합니다. 해당 상태는 더 많은 태스크를 예약할 수 있는지 여부를 나타냅니다.

명시적으로 종료되지 않으면 작업이 자동으로 완료됨 상태로 전환되지 않습니다. 이는 [onAllTasksComplete](/dotnet/api/microsoft.azure.batch.common.onalltaskscomplete) 속성 또는 [maxWallClockTime](/rest/api/batchservice/job/add#jobconstraints)을 통해 자동으로 트리거될 수 있습니다.

기본 [활성 작업 및 작업 일정 할당량](batch-quota-limit.md#resource-quotas)이 있습니다. 완료됨 상태의 작업 및 작업 일정은 이 할당량에 계산되지 않습니다.

## <a name="tasks"></a>작업

[태스크](jobs-and-tasks.md#tasks)는 작업을 구성하는 개별 활동 단위입니다. 태스크는 사용자가 제출하고 Batch에서 컴퓨팅 노드에 예약합니다. 다음 섹션에서는 문제를 처리하고 효율적으로 수행할 수 있도록 태스크를 디자인하기 위한 제안 사항을 제공합니다.

### <a name="save-task-data"></a>태스크 데이터 저장

컴퓨팅 노드는 기본적으로 사용 후에 삭제됩니다. [자동 풀](nodes-and-pools.md#autopools), [자동 스케일링](nodes-and-pools.md#automatic-scaling-policy)과 같은 Batch 기능을 사용하면 노드를 쉽게 숨길 수 있습니다. 크기 조정 또는 풀 삭제로 인해 노드가 풀을 벗어나면 해당 노드의 모든 파일도 삭제됩니다. 따라서 태스크는 완료되기 전에 해당 출력을 실행 중인 노드에서 지속형 저장소로 이동해야 합니다. 마찬가지로, 태스크가 실패하면 실패를 진단하는 데 필요한 로그를 지속형 저장소로 이동해야 합니다.

Batch는 [OutputFiles](batch-task-output-files.md) 및 다양한 공유 파일 시스템을 통해 데이터를 업로드할 수 있도록 Azure Storage 지원을 통합했거나 태스크에서 업로드를 직접 수행할 수 있습니다.

### <a name="manage-task-lifetime"></a>태스크 수명 관리

태스크가 더 이상 필요하지 않은 경우 삭제하거나 [retentionTime](/dotnet/api/microsoft.azure.batch.taskconstraints.retentiontime) 태스크 제약 조건을 설정합니다. `retentionTime`이 설정되면 `retentionTime`이 만료될 때 태스크에서 사용한 디스크 공간을 Batch에서 자동으로 정리합니다.

태스크를 삭제하는 경우 두 가지 작업이 수행됩니다. 이렇게 하면 태스크가 작업에 쌓이지 않습니다. 이로 인해 완료됨 태스크를 통해 필터링해야 하므로 관심 있는 태스크를 쿼리하거나 검색하는 것이 더 어려워질 수 있습니다. 또한 노드에서 해당 태스크 데이터를 정리합니다(`retentionTime`에 아직 도달하지 않은 경우). 이렇게 하면 노드에서 태스크 데이터를 채우지 않고 디스크 공간이 부족하지 않도록 할 수 있습니다.

### <a name="submit-large-numbers-of-tasks-in-collection"></a>많은 수의 태스크를 컬렉션에 제출

태스크는 개별 기준 또는 컬렉션 단위에 제출할 수 있습니다. 오버헤드 및 제출 시간을 줄이기 위해 태스크를 대량으로 제출하는 경우 한 번에 최대 100개의 태스크를 [컬렉션](/rest/api/batchservice/task/addcollection)에 제출할 수 있습니다.

### <a name="set-max-tasks-per-node-appropriately"></a>노드당 최대 태스크 수를 적절하게 설정

Batch는 노드에 대한 과도한 구독 태스크를 지원합니다(노드에 있는 코어 수보다 더 많은 작업을 실행함). 사용자는 풀의 노드에 "적합"한 태스크가 되도록 해야 합니다. 예를 들어 각각 하나의 노드에서 25%의 CPU 사용량을 소비하는 8개의 태스크를 예약하려고 하면(`taskSlotsPerNode = 8`인 풀의 경우) 성능이 저하될 수 있습니다.

### <a name="design-for-retries-and-re-execution"></a>다시 시도 및 다시 실행 디자인

Batch는 태스크를 자동으로 다시 시도할 수 있습니다. 사용자 제어 다시 시도 및 내부 다시 시도의 두 가지 유형이 있습니다. 사용자 제어 다시 시도는 태스크의 [maxTaskRetryCount](/dotnet/api/microsoft.azure.batch.taskconstraints.maxtaskretrycount)로 지정됩니다. 태스크에 지정된 프로그램이 0이 아닌 종료 코드를 사용하여 종료되면 태스크가 `maxTaskRetryCount` 값까지 다시 시도됩니다.

흔치 않은 경우이지만 태스크가 실행되는 동안 내부 상태를 업데이트할 수 없거나 노드에 오류가 발생하는 것과 같이 컴퓨팅 노드의 오류로 인해 태스크를 내부적으로 다시 시도할 수 있습니다. 태스크를 포기하고 Batch(잠재적으로 다른 컴퓨팅 노드)에서 다시 예약할 태스크를 지연시키기 전에 가능한 경우 태스크는 내부 제한까지 동일한 컴퓨팅 노드에서 다시 시도됩니다.

전용 노드 또는 낮은 우선 순위의 노드에서 태스크를 실행하는 경우 디자인상의 차이점이 없습니다. 우선 순위가 낮은 노드에서 실행되는 동안 태스크가 선점되든지, 아니면 전용 노드에서 발생한 오류로 인해 중단되든지 간에 두 상황은 모두 오류를 극복할 수 있도록 태스크를 설계함으로써 완화됩니다.

### <a name="build-durable-tasks"></a>지속형 태스크 빌드

태스크는 오류를 극복하고 다시 시도를 수용할 수 있도록 설계되어야 합니다. 이는 장기 실행 태스크에 특히 중요합니다. 이렇게 하려면 태스크가 두 번 이상 실행되는 경우에도 동일한 단일 결과를 생성하도록 합니다. 이를 구현하기 위한 한 가지 방법은 태스크를 "목표 검색"으로 만드는 것입니다. 또 다른 방법은 태스크가 idempotent(멱등원)인지 확인하는 것입니다(실행 횟수에 관계 없이 태스크의 결과가 동일함).

일반적인 예로 파일을 컴퓨팅 노드에 복사하는 태스크가 있습니다. 간단한 방법은 실행될 때마다 지정된 모든 파일을 복사하는 태스크이지만, 이는 비효율적이고 오류를 극복할 수 있도록 구축되지 않습니다. 대신 파일이 컴퓨팅 노드에 있는지 확인하는 태스크, 즉 이미 있는 파일을 다시 복사하지 않는 태스크를 만듭니다. 이러한 방식으로 태스크가 중단되면 중단된 위치를 선택합니다.

### <a name="avoid-short-execution-time"></a>단기 실행 시간 회피

1~2초 동안만 실행되는 태스크는 적합하지 않습니다. 개별 태스크에서 많은 양의 활동을 수행합니다(최소 10초, 최대 시간 또는 일). 각 태스크가 1분 이상 실행되는 경우 전체 컴퓨팅 시간의 일부인 예약 오버헤드가 낮습니다.

### <a name="use-pool-scope-for-short-tasks-on-windows-nodes"></a>Windows 노드에서 짧은 태스크에 풀 범위 사용

Batch 노드에서 태스크를 예약할 때 태스크 범위 또는 풀 범위를 사용하여 실행할지를 선택할 수 있습니다. 태스크가 짧은 시간 동안만 실행되는 경우 해당 태스크에 대한 자동 사용자 계정을 만드는 데 필요한 리소스로 인해 태스크 범위가 비효율적일 수 있습니다. 효율성을 높이기 위해 이러한 태스크를 풀 범위로 설정하는 것이 좋습니다. 자세한 내용은 [풀 범위가 지정된 자동 사용자로 태스크 실행](batch-user-accounts.md#run-a-task-as-an-auto-user-with-pool-scope)을 참조하세요.

## <a name="nodes"></a>노드

[컴퓨팅 노드](nodes-and-pools.md#nodes)는 애플리케이션의 워크로드 중 일부를 처리하는 데 전적으로 사용되는 Azure VM(가상 머신) 또는 클라우드 서비스 VM입니다. 노드를 사용하는 경우 다음 지침을 따릅니다.

### <a name="idempotent-start-tasks"></a>idempotent 시작 태스크

다른 태스크와 마찬가지로 노드가 부팅될 때마다 다시 실행되므로 노드 [시작 태스크](jobs-and-tasks.md#start-task)도 idempotent여야 합니다. idempotent 태스크는 여러 번 실행될 때 일관된 결과를 생성하는 태스크일 뿐입니다.

### <a name="isolated-nodes"></a>격리된 노드

규정 준수 또는 규정 요구 사항을 준수하는 워크로드에 대해 격리된 VM 크기를 사용하는 것이 좋습니다. 가상 머신 구성 모드에서 지원되는 격리된 크기는 `Standard_E80ids_v4`, `Standard_M128ms`, `Standard_F72s_v2`, `Standard_G5` `Standard_GS5` 및 `Standard_E64i_v3`입니다. 격리된 VM 크기에 대한 자세한 내용은 [Azure의 가상 머신 격리](../virtual-machines/isolation.md)를 참조하세요.

### <a name="manage-long-running-services-via-the-operating-system-services-interface"></a>운영 체제 서비스 인터페이스를 통해 장기 실행 서비스 관리

노드에서 Batch 에이전트와 함께 다른 에이전트를 실행해야 하는 경우도 있습니다. 예를 들어 노드에서 데이터를 수집하여 보고하려고 할 수 있습니다. 이러한 에이전트는 Windows 서비스 또는 Linux `systemd` 서비스와 같은 OS 서비스로 배포하는 것이 좋습니다.

이러한 서비스를 실행하는 경우 노드의 Batch 관리형 디렉터리에 있는 파일에 대한 파일 잠금을 사용하지 않아야 합니다. 그렇지 않으면 Batch에서 파일 잠금으로 인해 해당 디렉터리를 삭제할 수 없기 때문입니다. 예를 들어 Windows 서비스를 시작 태스크에 설치하는 경우 시작 태스크 작업 디렉터리에서 직접 서비스를 시작하는 대신, 파일을 다른 위치에 복사합니다(파일이 있는 경우에는 복사를 건너뜀). 그런 다음, 해당 위치에서 서비스를 설치합니다. Batch에서 시작 태스크를 다시 실행하면 시작 작업 태스크 디렉터리가 삭제되고 다시 만들어집니다. 이는 서비스에서 시작 태스크 디렉터리가 아닌 다른 디렉터리에서 파일 잠금을 수행하기 때문에 작동합니다.

### <a name="avoid-creating-directory-junctions-in-windows"></a>Windows에서 디렉터리 접합을 만들지 않음

디렉터리 접합(디렉터리 하드 링크라고도 함)은 태스크 및 작업 정리 중에 처리하기가 어렵습니다. 하드 링크 대신 symlink(소프트 링크)를 사용합니다.

### <a name="collect-batch-agent-logs"></a>Batch 에이전트 로그 수집

노드에서 실행되는 노드 또는 태스크의 동작과 관련된 문제가 있으면 해당 노드를 할당 취소하기 전에 Batch 에이전트 로그를 수집해야 합니다. Batch 에이전트 로그는 Batch 서비스 로그 업로드 API를 사용하여 수집할 수 있습니다. 이러한 로그는 지원 티켓의 일부로 Microsoft에 제공될 수 있으며 문제를 해결하는 데 도움이 됩니다.

### <a name="manage-os-upgrades"></a>OS 업그레이드 관리

사용자 구독 모드 Batch 계정에 대해 자동화된 OS 업그레이드는 특히 태스크가 장기간 실행되는 경우 태스크 진행을 방해할 수 있습니다. [Idempotent 태스크를 빌드하면](#build-durable-tasks) 이러한 중단으로 인해 발생하는 오류를 줄일 수 있습니다. 또한 [태스크가 실행될 것으로 예상되지 않는 시간 동안 OS 이미지 업그레이드를 예약](../virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade.md#manually-trigger-os-image-upgrades)하는 것이 좋습니다.

Windows 풀의 경우 `enableAutomaticUpdates`는 기본적으로 `true`로 설정됩니다. 자동 업데이트를 허용하는 것이 좋지만 OS 업데이트가 예기치 않게 발생하지 않도록 하려면 이 값을 `false`로 설정할 수 있습니다.

## <a name="isolation-security"></a>격리 보안

격리를 위해 시나리오에서 작업을 서로 격리해야 하는 경우 이 작업은 별도의 풀에 배치하여 수행합니다. 풀은 Batch의 보안 격리 경계이며, 기본적으로 두 개의 풀이 표시되지 않거나 서로 통신할 수 없습니다. 별도의 Batch 계정을 격리 수단으로 사용하지 않습니다.

## <a name="connectivity"></a>연결

Batch 솔루션의 연결과 관련된 다음 지침을 검토합니다.

### <a name="network-security-groups-nsgs-and-user-defined-routes-udrs"></a>NSG(네트워크 보안 그룹) 및 UDR(사용자 정의 경로)

[가상 네트워크에서 Batch 풀을 프로비저닝](batch-virtual-network.md)하는 경우 `BatchNodeManagement` 서비스 태그, 포트, 프로토콜 및 규칙 방향의 사용과 관련된 지침을 철저히 준수해야 합니다. 기본 Batch 서비스 IP 주소를 사용하는 대신 서비스 태그를 사용하는 것이 좋습니다. 이는 IP 주소가 시간이 지남에 따라 변경될 수 있기 때문입니다. Batch 서비스 IP 주소를 직접 사용하면 불안정성, 중단 또는 가동 중단이 Batch 풀에 발생할 수 있습니다.

UDR(사용자 정의 경로)의 경우 시간이 지남에 따라 주소가 변경되므로 경로 테이블에서 Batch 서비스 IP 주소를 주기적으로 업데이트하는 프로세스를 수행해야 합니다. Batch 서비스 IP 주소 목록을 가져오는 방법에 대한 자세한 내용은 [온-프레미스 서비스 태그](../virtual-network/service-tags-overview.md)를 참조하세요. Batch 서비스 IP 주소는 `BatchNodeManagement` 서비스 태그(또는 Batch 계정 지역과 일치하는 지역 변형)와 연결됩니다.

### <a name="honoring-dns"></a>DNS 적용

시스템에서 DNS TTL(Time-to-Live)을 Batch 계정 서비스 URL에 적용해야 합니다. 또한 Batch 서비스 클라이언트 및 Batch 서비스에 대한 다른 연결 메커니즘이 IP 주소를 사용하지 않는지 확인합니다(또는 아래 설명된 대로 [고정 공용 IP 주소를 사용하여 풀 만들기](create-pool-public-ip.md)).

요청에서 5xx 수준 HTTP 응답을 받고 "Connection: Close" 헤더가 응답에 있는 경우, Batch 서비스 클라이언트는 기존 연결을 닫고 Batch 계정 서비스 URL에 대한 DNS를 다시 확인하고 새 연결에 대한 다음 요청을 시도하여 추적하여 추천 사항을 준수해야 합니다.

### <a name="retry-requests-automatically"></a>자동으로 요청 다시 시도

Batch 서비스 클라이언트에는 서비스 유지 관리 기간 동안만이 아니라 정상 작업 중에도 요청을 자동으로 다시 시도할 수 있는 적절한 다시 시도 정책이 있어야 합니다. 이러한 다시 시도 정책에는 5분 이상의 간격이 적용되어야 합니다. 자동 다시 시도 기능은 [.NET RetryPolicyProvider 클래스](/dotnet/api/microsoft.azure.batch.retrypolicyprovider)와 같은 다양한 Batch SDK와 함께 제공됩니다.

### <a name="static-public-ip-addresses"></a>고정 공용 IP 주소

일반적으로 Batch 풀의 가상 머신은 풀의 수명 동안 변경될 수 있는 공용 IP 주소를 통해 액세스됩니다. 이를 통해 특정 IP 주소로 액세스를 제한하는 데이터베이스 또는 다른 외부 서비스와 상호 작용하기가 어려울 수 있습니다. 풀의 공용 IP 주소가 예기치 않게 변경되지 않도록 하려면 제어하는 고정 공용 IP 주소 세트를 사용하여 풀을 만들 수 있습니다. 자세한 내용은 [지정된 공용 IP 주소를 사용하여 Azure Batch 풀 만들기](create-pool-public-ip.md)를 참조하세요.

### <a name="testing-connectivity-with-cloud-services-configuration"></a>Cloud Services 구성을 사용하여 연결 테스트

Azure Load Balancer를 통해 ICMP 프로토콜을 사용할 수 없기 때문에 클라우드 서비스에서 일반적인 "ping"/ICMP 프로토콜을 사용할 수 없습니다. 자세한 내용은 [Azure Cloud Services에 대한 연결 및 네트워킹](../cloud-services/cloud-services-connectivity-and-networking-faq.yml#can-i-ping-a-cloud-service-)을 참조하세요.

## <a name="batch-node-underlying-dependencies"></a>Batch 노드 기본 종속성

Batch 솔루션을 설계하는 경우 고려해야 할 종속성 및 제한 사항은 다음과 같습니다.

### <a name="system-created-resources"></a>시스템에서 만든 리소스

Azure Batch는 VM에서 사용자 및 그룹 세트를 만들고 관리합니다. 이러한 사용자 및 그룹 세트는 변경할 수 없습니다. 다음과 같습니다.

Windows:

- **PoolNonAdmin** 사용자
- **WATaskCommon** 사용자 그룹

Linux:

- **_azbatch** 사용자

### <a name="file-cleanup"></a>파일 정리

보존 시간이 만료되면 Batch에서 태스크가 실행되는 작업 디렉터리를 적극적으로 정리하려고 시도합니다. 이 디렉터리 외부에 작성된 파일은 디스크 공간을 채우지 않도록 [사용자가 정리](#manage-task-lifetime)해야 합니다.

startTask 작업 디렉터리에서 Windows의 서비스를 실행하는 경우 폴더가 아직 사용 중이므로 작업 디렉터리에 대한 자동화된 정리가 차단됩니다. 이로 인해 성능이 저하됩니다. 이 문제를 해결하려면 해당 서비스의 디렉터리를 Batch에서 관리하지 않는 별도의 디렉터리로 변경합니다.

## <a name="next-steps"></a>다음 단계

- 풀, 노드, 작업 및 태스크와 같은 [Batch 서비스 워크플로 및 기본 리소스](batch-service-workflow-features.md)에 대해 알아봅니다.
- [기본 Azure Batch 할당량, 한도 및 제약 조건에 대해 알아보고 할당량 증가를 요청하는 방법](batch-quota-limit.md)에 대해 알아봅니다.
- [풀 및 노드 백그라운드 작업에서 오류를 감지하고 방지](batch-pool-node-error-checking.md)하는 방법을 알아봅니다.
