---
title: 간소화된 컴퓨팅 노드 통신 사용
description: Azure Batch 서비스가 Batch 풀 인프라를 관리하는 방식과 를 옵트인하거나 옵트아웃하는 방법을 알아봅니다.
ms.topic: how-to
ms.date: 10/21/2021
ms.openlocfilehash: 9bd53bcab9ccb9403d8d851300ba987a23eacc11
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130273810"
---
# <a name="use-simplified-compute-node-communication"></a>간소화된 컴퓨팅 노드 통신 사용

Azure Batch 풀에는 Batch 작업 형식으로 사용자 지정 워크로드를 실행하는 하나 이상의 컴퓨팅 노드가 포함되어 있습니다. Batch 기능 및 Batch 풀 인프라 관리를 사용하도록 설정하려면 컴퓨팅 노드가 Azure Batch 서비스와 통신해야 합니다.

이 문서에서는 Azure Batch 서비스가 Batch 풀 컴퓨팅 노드와 통신하는 방법, 필요할 수 있는 네트워크 구성 변경 내용 및 공개 미리 보기 기간 동안 간소화된 새 컴퓨팅 노드 통신 기능을 사용하여 Batch 계정을 사용하거나 사용하지 않는 방법에 대해 설명합니다.

> [!IMPORTANT]
> Azure Batch 간소화된 컴퓨팅 노드 통신에 대한 지원은 현재 공개 미리 보기로 제공됩니다. 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.
> 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

현재는 옵트인할 필요가 없습니다. 그러나 나중에 모든 Batch 계정에 대해 간소화된 컴퓨팅 노드 통신을 사용해야 합니다. 이때 공식 중지 알림이 제공되며, 이러한 경우 이전에 Batch 풀을 마이그레이션할 수 있습니다.

## <a name="compute-node-communication-changes"></a>컴퓨팅 노드 통신 변경 내용

Azure Batch 서비스는 Batch 풀 인프라가 사용자를 대신하여 관리되는 방식을 간소화합니다. 새 통신 방법은 기준 작업에 필요한 인바운드 및 아웃바운드 네트워킹 연결의 복잡성과 범위를 줄입니다.

간소화된 컴퓨팅 노드 통신을 위해 옵트인되지 않은 계정의 Batch 풀에는 [가상 네트워크에서 풀을 만들](batch-virtual-network.md)때 NSG(네트워크 보안 그룹), UDR(사용자 정의 경로) 및 방화벽에 다음과 같은 네트워킹 규칙이 필요합니다.

- 인바운드:
  - BatchNodeManagement의 TCP를 통해 대상 포트 29876, 29877 *지역*

- 아웃바운드:
  - TCP를 통해 Storage 대상 포트 443 *지역*
  - TCP를 통해 BatchNodeManagement에 대한 대상 포트 443입니다. *작업* 관리자 태스크와 같이 Batch 서비스에 다시 통신해야 하는 특정 워크로드에 대한 지역

새 모델을 사용하면 간소화된 컴퓨팅 노드 통신을 사용하는 계정의 Batch 풀에 NSG, UDR 및 방화벽에서 다음과 같은 네트워킹 규칙이 필요합니다.

- 인바운드:
  - 없음

- 아웃바운드:
  - TCP를 통해 BatchNodeManagement에 대한 대상 포트 443입니다. *지역*

Batch 계정에 대한 아웃바운드 요구 사항은 [아웃바운드 네트워크 종속성 엔드포인트 목록 API를](/rest/api/batchmanagement/batch-account/list-outbound-network-dependencies-endpoints)사용하여 검색할 수 있습니다. 이 API는 Batch 계정 풀 통신 모델에 따라 기본 의존성 집합을 보고합니다. 사용자별 워크로드에는 다른 Azure 리소스(예: 애플리케이션 패키지용 Azure Storage, Azure Container Registry 등)로 트래픽을 여는 것과 같은 추가 규칙 또는 가상 파일 시스템 탑재 기능을 위한 Microsoft 패키지 리포지토리와 같은 엔드포인트가 필요할 수 있습니다.  

## <a name="benefits-of-the-new-model"></a>새 모델의 이점

[새 통신 모델을 옵트인하는](#opt-your-batch-account-in-or-out-of-simplified-compute-node-communication) Azure Batch 사용자는 네트워킹 연결 및 규칙을 간소화하여 이점을 얻을 수 있습니다.

간소화된 컴퓨팅 노드 통신은 인터넷에서 인바운드 통신을 위한 포트 열기 요구 사항을 제거하여 보안 위험을 줄이는 데 도움이 됩니다. 기준 작업에는 잘 알려진 서비스 태그에 대한 단일 아웃바운드 규칙만 필요합니다.

또한 새 모델은 Storage 아웃바운드 통신을 수행하기 때문에 보다 세분화된 데이터 반출 제어를 제공합니다. *지역은* 더 이상 필요하지 않습니다. 워크플로에 필요한 경우(예: AppPackage 스토리지 계정, 리소스 파일 또는 출력 파일의 다른 스토리지 계정 또는 기타 유사한 시나리오) Azure Storage 아웃바운드 통신을 명시적으로 잠글 수 있습니다.

다음 섹션에 설명된 대로 워크로드가 현재 변경 내용의 영향을 받지 않더라도 이제 [간소화된 컴퓨팅 노드 통신을 사용하도록 옵트인하는 것이](#opt-your-batch-account-in-or-out-of-simplified-compute-node-communication) 좋습니다. 이렇게 하면 Batch 워크로드가 이 모델에서 사용하도록 설정된 향후 개선에 대비할 수 있습니다.

## <a name="scope-of-impact"></a>영향 범위

대부분의 경우 이 새 통신 모델은 Batch 워크로드에 직접적인 영향을 미치지 않습니다. 그러나 간소화된 컴퓨팅 노드 통신은 다음과 같은 경우에 영향을 줍니다.

- Batch 풀 만들기의 일부로 Virtual Network 지정하고 다음 중 하나 또는 둘 모두를 수행하는 사용자:
   - 간소화된 컴퓨팅 노드 통신과 호환되지 않는 아웃바운드 네트워크 트래픽 규칙을 명시적으로 사용하지 않도록 설정합니다.
   - 간소화된 컴퓨팅 노드 통신과 호환되지 않는 UDR 및 방화벽 규칙을 사용합니다.
- 컴퓨팅 노드에서 소프트웨어 방화벽을 사용하도록 설정하고 간소화된 컴퓨팅 노드 통신과 호환되지 않는 소프트웨어 방화벽 규칙에서 아웃바운드 트래픽을 명시적으로 사용하지 않도록 설정하는 사용자입니다.

이러한 경우 중 하나가 적용되고 미리 보기에 옵트인하려는 경우 다음 섹션에 설명된 단계에 따라 Batch 워크로드가 새 모델에서 계속 작동할 수 있는지 확인합니다.

### <a name="required-network-configuration-changes"></a>필요한 네트워크 구성 변경

영향을 받는 사용자의 경우 새 통신 모델로 마이그레이션하려면 다음 단계 집합이 필요합니다.

1. Batch 풀(NSG, UDR, 방화벽 등)에 적용 가능한 네트워킹 구성에 모델의 합집합(즉, 간소화된 컴퓨팅 노드 통신 이전 및 이후의 네트워크 규칙)이 포함되어 있는지 확인합니다. 최소한 이러한 규칙은 다음과 같습니다.
   - 인바운드:
     - BatchNodeManagement의 TCP를 통해 대상 포트 29876, 29877 *지역*
   - 아웃바운드:
     - TCP를 통해 Storage 대상 포트 443 *지역*
     - TCP를 통해 BatchNodeManagement에 대한 대상 포트 443입니다. *지역*
1. 워크플로에 필요한 추가 인바운드 또는 아웃바운드 시나리오가 있는 경우 규칙이 이러한 요구 사항을 반영하는지 확인해야 합니다.
1. 아래에 설명된 대로 [간소화된 컴퓨팅 노드 통신을 옵트인합니다.](#opt-your-batch-account-in-or-out-of-simplified-compute-node-communication)
1. 다음 옵션 중 하나를 사용하여 새 통신 모델을 사용하도록 워크로드를 업데이트합니다. 어떤 방법을 사용하든 공용 IP 주소가 없는 풀은 영향을 받지 않으며 현재 간소화된 컴퓨팅 노드 통신을 사용할 수 없습니다. [현재 제한 사항 섹션을](#current-limitations) 참조하세요.
   1. 새 풀을 만들고 새 풀이 제대로 작동하는지 확인합니다. 워크로드를 새 풀로 마이그레이션하고 이전 풀을 삭제합니다.
   1. 모든 기존 풀의 크기를 0개 노드로 조정하고 다시 확장합니다.
1. 모든 이전 풀이 삭제되거나 0으로 확장되고 다시 확장되었는지 확인한 후 [아웃바운드 네트워크 종속성 엔드포인트 나열 API를](/rest/api/batchmanagement/batch-account/list-outbound-network-dependencies-endpoints) 쿼리하여 해당 지역에 Azure Storage 아웃바운드 규칙이 없는지 확인합니다(Batch 계정에 연결된 경우 autostorage 계정은 제외).
1. 적용 가능한 모든 네트워킹 구성을 최소한 간소화된 컴퓨팅 노드 통신 규칙으로 수정합니다(위에서 설명한 대로 필요한 추가 규칙을 참고하세요).
   - 인바운드:
     - 없음
   - 아웃바운드:
     - TCP를 통해 BatchNodeManagement에 대한 대상 포트 443입니다. *지역*

이러한 단계를 수행하지만 나중에 간소화된 컴퓨팅 노드 통신 사용을 중지하려면 다음을 수행해야 합니다.

1. 아래에 설명된 대로 [간소화된 컴퓨팅 노드 통신을 옵트아웃합니다.](#opt-your-batch-account-in-or-out-of-simplified-compute-node-communication)
1. 워크로드를 새 풀로 마이그레이션하거나 기존 풀의 크기를 조정하고 규모 축소합니다(위의 4단계 참조).
1. [아웃바운드 네트워크 종속성 엔드포인트 목록 API를](/rest/api/batchmanagement/batch-account/list-outbound-network-dependencies-endpoints)사용하여 모든 풀이 더 이상 간소화된 컴퓨팅 노드 통신을 사용하지 않는지 확인합니다. 지역에 대해 Azure Storage 아웃바운드 규칙이 표시됩니다(Batch 계정에 연결된 모든 Autostorage 계정과 무관).

## <a name="opt-your-batch-account-in-or-out-of-simplified-compute-node-communication"></a>간소화된 컴퓨팅 노드 통신 안팎으로 Batch 계정 옵트아웃

간소화된 컴퓨팅 노드 통신에서 Batch 계정을 옵트아웃하려면 [Azure Portal 새 지원 요청을 만듭니다.](../azure-portal/supportability/how-to-create-azure-support-request.md)

> [!IMPORTANT]
> 간소화된 컴퓨팅 노드 통신을 옵트인(또는 옵트아웃)하는 경우 변경 내용은 향후 동작에만 영향을 줍니다. 요청 전에 생성된 0이 아닌 컴퓨팅 노드가 포함된 Batch 풀은 영향을 받지 않으며 요청 전에 활성화된 모델을 사용합니다. 옵트인 또는 옵트아웃하기 전에 기존 풀을 마이그레이션하는 방법에 대한 자세한 내용은 마이그레이션 단계를 참조하세요.

요청을 만들 때 다음 옵션을 사용합니다.

:::image type="content" source="media/simplified-compute-node-communication/support-request-opt-in.png" alt-text="간소화된 컴퓨팅 노드 통신을 옵트인하는 지원 요청의 스크린샷.":::

1. **문제 유형** 에서 **기술** 을 선택합니다.
1. **서비스 유형에** 대해 **Batch 서비스** 를 선택합니다.
1. **리소스** 의 경우 이 요청에 대한 Batch 계정을 선택합니다.
1. **요약:**
   - 옵트인하려면 "간소화된 컴퓨팅 노드 통신 사용"을 입력합니다.
   - 옵트인하려면 "간소화된 컴퓨팅 노드 통신 사용 안 함"을 입력합니다.
1. **문제 유형에서** **Batch 계정 을 선택합니다.**
1. **문제 하위 유형** 에서 Batch **계정의 기타 문제를 선택합니다.**
1. **다음을** 선택한 다음, **다음을** 다시 선택하여 **추가 세부 정보** 페이지로 이동합니다.
1. **추가 세부 정보** 에서 필요에 따라 구독 또는 여러 구독에서 모든 Batch 계정을 사용하도록 지정할 수 있습니다. 이렇게 하는 경우 여기에 구독 ID를 포함해야 합니다.
1. 페이지에서 다른 필수 항목을 선택한 후 **다음을** 선택합니다.
1. 요청 세부 정보를 검토한 다음, **만들기를** 선택하여 지원 요청을 제출합니다.

요청이 제출된 후 계정이 옵트인(또는 아웃)되면 알림이 표시됩니다.

## <a name="current-limitations"></a>현재 제한 사항

다음은 간소화된 컴퓨팅 노드 통신을 옵트인하는 계정의 알려진 제한 사항입니다.

- [공용 IP 주소가 없는 풀 만들기는](batch-pool-no-public-ip-address.md) 현재 옵트인된 계정에 대해 지원되지 않습니다.
- 공용 IP 주소가 없는 이전에 만든 풀은 Batch 계정이 옵트인한 경우에도 간소화된 컴퓨팅 노드 통신을 사용하지 않습니다.
- [프라이빗 Batch 계정은](private-connectivity.md) 간소화된 컴퓨팅 노드 통신을 옵트인할 수 있지만, 간소화된 컴퓨팅 노드 통신을 사용하려면 이러한 Batch 계정에서 만든 Batch 풀에 공용 IP 주소가 있어야 합니다.
- 클라우드 서비스 구성 풀은 현재 간소화된 컴퓨팅 노드 통신에 지원되지 않으며 일반적으로 사용되지 않습니다. Batch 풀에 Virtual Machine 구성을 사용하는 것이 좋습니다. 자세한 내용은 [Cloud Services에서 가상 머신으로 Batch 풀 구성 마이그레이션](batch-pool-cloud-service-to-virtual-machine-configuration.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계

- [가상 네트워크의 풀에](batch-virtual-network.md)대해 자세히 알아보세요.
- [지정된 공용 IP 주소를 사용하여 풀 풀을 만드는](create-pool-public-ip.md)방법을 알아봅니다.
