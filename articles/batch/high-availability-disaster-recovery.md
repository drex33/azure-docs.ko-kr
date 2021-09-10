---
title: 고가용성 및 재해 복구
description: 지역별 서비스 중단 시에 사용하도록 Batch 애플리케이션을 설계하는 방법을 알아봅니다.
ms.topic: how-to
ms.date: 12/30/2020
ms.openlocfilehash: eb3d0cb218e607e38b7478b7cc7956963e450f46
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122528449"
---
# <a name="design-your-batch-application-for-high-availability"></a>고가용성을 위해 Batch 애플리케이션 디자인

Azure Batch는 모든 Azure 지역에서 사용할 수 있지만 Batch 계정을 만들 때 하나의 특정 지역과 연결되어야 합니다. 그러면 Batch 계정으로 수행하는 모든 작업이 해당 지역에 적용됩니다. 예를 들어 풀 및 연결된 VM(가상 머신)은 Batch 계정과 같은 지역에 생성됩니다.

Batch를 사용하는 애플리케이션을 설계할 때는 해당 지역에서 Batch를 사용할 수 없을 가능성을 고려해야 합니다. 드물지만 지역 전체, 지역의 전체 Batch 서비스 또는 특정 Batch 계정에 문제가 발생할 수도 있기 때문입니다.

Batch를 사용하는 애플리케이션이나 솔루션을 항상 사용할 수 있도록 하려면 문제 발생 시 다른 지역으로 장애 조치(failover)하거나 항상 둘 이상의 지역 간에 워크로드를 분할하도록 구성해야 합니다. 둘 중 어떤 방식을 사용하더라도 지역이 서로 다른 둘 이상의 Batch 계정이 필요합니다.

## <a name="multiple-batch-accounts-in-multiple-regions"></a>여러 지역에서 여러 Batch 계정 사용

다양한 지역에서 여러 Batch 계정을 사용하면 한 지역의 Batch 계정을 사용할 수 없게 되더라도 애플리케이션을 계속 실행할 수 있습니다. 애플리케이션을 항상 사용할 수 있어야 하는 경우 여러 계정을 보유하는 것이 특히 중요합니다.

경우에 따라 애플리케이션은 의도적으로 두 개 이상의 지역을 사용하도록 디자인될 수 있습니다. 예를 들어 용량이 많이 필요할 때는 대규모 애플리케이션을 처리하거나 나중에 용량을 늘릴 수 있도록 여러 지역을 사용해야 할 수 있습니다. 또한 이 애플리케이션에는 여러 Batch 계정(지역당 하나가 사용됨)이 필요합니다.

## <a name="design-considerations-for-providing-failover"></a>장애 조치(failover) 기능 제공을 위한 설계 시 고려 사항

대체 지역으로 장애 조치하는 기능을 제공하는 경우 솔루션의 모든 구성 요소를 고려해야 합니다. 두 번째 Batch 계정을 포함하는 것만으로는 충분하지 않습니다. 예를 들어 대다수 Batch 애플리케이션에는 Azure 스토리지 계정이 필요합니다. 그리고 성능을 적절한 수준으로 유지하려면 스토리지 계정과 Batch 계정의 지역이 동일해야 합니다.

장애 조치(failover) 가능한 솔루션을 설계할 때는 다음 요소를 고려하세요.

- Batch 계정, 스토리지 계정 등 필요한 모든 서비스를 각 지역에서 미리 만듭니다. 일반적으로 계정을 만드는 데는 요금이 부과되지 않으며 계정이 사용되거나 데이터가 저장되는 경우에만 요금이 부과됩니다.
- 모든 구독에 적절한 [할당량](batch-quota-limit.md)이 설정되어 있는지 미리 확인합니다. 그래야 Batch 계정을 사용하여 필요한 수의 코어를 할당할 수 있습니다.
- 템플릿 및/또는 스크립트를 사용해 특정 지역에서 애플리케이션 배포를 자동화합니다.
- 모든 지역에서 애플리케이션 이진 파일 및 참조 데이터를 최신 상태로 유지합니다. 이러한 파일/데이터를 최신 상태로 유지하면 파일이 업로드 및 배포될 때까지 기다리지 않고도 지역을 온라인 상태로 빠르게 전환할 수 있습니다. 예를 들어, Batch 애플리케이션 패키지를 사용하여 풀 노드에 설치할 사용자 지정 애플리케이션을 저장 및 참조하는 경우, 애플리케이션의 새 버전을 생성할 때 해당 버전을 각 Batch 계정에 업로드해야 하며 풀 구성에서 참조해야 합니다. 또는 새 버전을 기본 버전으로 지정해야 합니다.
- Batch, 스토리지 및 기타 서비스를 호출하는 애플리케이션에서 클라이언트나 부하를 다른 지역으로 쉽게 전환할 수 있습니다.
- 정상 작업의 일부로 대체 지역으로 자주 전환하는 것이 좋습니다. 예를 들어, 지역이 각기 다른 두 배포에서 매월 대체 지역으로 전환합니다.

## <a name="next-steps"></a>다음 단계

- [Azure Portal](batch-account-create-portal.md), [Azure CLI](./scripts/batch-cli-sample-create-account.md), [PowerShell](batch-powershell-cmdlets-get-started.md) 또는 [Batch 관리 API](batch-management-dotnet.md)를 사용하여 Batch 계정을 만드는 방법에 대해 자세히 알아봅니다.
- [Batch 계정과 연결된 기본 할당량](batch-quota-limit.md) 및 할당량을 늘릴 수 있는 방법에 관해 알아봅니다.
