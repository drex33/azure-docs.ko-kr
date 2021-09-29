---
title: 캐나다 연방 PBMM 청사진 샘플
description: 캐나다 연방 PBMM 청사진 샘플에 대한 개요입니다. 이 청사진 샘플은 고객이 특정 컨트롤을 평가하는 데 도움이 됩니다.
ms.date: 09/08/2021
ms.topic: sample
ms.openlocfilehash: 8c9d27bb0a670fab56637f4d8248924bda921525
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128678159"
---
# <a name="canada-federal-pbmm-blueprint-sample"></a>캐나다 연방 PBMM 청사진 샘플

캐나다 연방 PBMM 청사진 샘플은 특정 [캐나다 연방 PBMM](https://www.canada.ca/en/government/system/digital-government/digital-government-innovations/cloud-services/government-canada-security-control-profile-cloud-based-it-services.html) 컨트롤을 평가하는 데 도움이 되는 [Azure Policy](../../policy/overview.md)를 사용하여 거버넌스 보호책을 제공합니다. 이 청사진은 고객이 캐나다 연방 PBMM 컨트롤을 구현해야 하는 Azure 배포 아키텍처의 핵심 정책 세트를 배포하는 데 도움이 됩니다.

## <a name="control-mapping"></a>컨트롤 매핑

[Azure Policy 컨트롤 매핑](../../policy/samples/canada-federal-pbmm.md)은 이 청사진에 포함된 정책 정의 및 해당 정책 정의가 캐나다 연방 PBMM 프레임워크의 **컨트롤** 에 매핑되는 방법에 관한 세부 정보를 제공합니다. 아키텍처에 할당된 경우 리소스는 할당된 정책 정의를 준수하지 않는지 Azure Policy에서 평가됩니다. 자세한 내용은 [Azure Policy](../../policy/overview.md)를 참조하세요.

## <a name="deploy"></a>배포

Azure Blueprints 캐나다 연방 PBMM 청사진 샘플을 배포하려면 다음 단계를 수행해야 합니다.

> [!div class="checklist"]
> - 샘플에서 새 청사진 만들기
> - 샘플 사본을 **게시됨** 으로 표시
> - 기존 구독에 청사진 사본 할당

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free)을 만듭니다.

### <a name="create-blueprint-from-sample"></a>샘플에서 청사진 만들기

먼저 샘플을 스타터로 사용하여 환경 내에 새 청사진을 만들어 청사진 샘플을 구현합니다.

1. 왼쪽 창에서 **모든 서비스** 를 선택합니다. **청사진** 을 검색하고 선택합니다.

1. **시작** 페이지 왼쪽에서 _청사진 만들기_ 아래의 **만들기** 단추를 선택합니다.

1. _다른 샘플_ 아래에서 **캐나다 연방 PBMM** 청사진 샘플을 찾아서 **이 샘플 사용** 을 선택합니다.

1. 청사진 샘플의 _기본 사항_ 을 입력합니다.

   - **청사진 이름**: 캐나다 연방 PBMM 청사진 샘플 복사본의 이름을 입력합니다.
   - **정의 위치**: 줄임표를 사용하고 샘플 사본을 저장할 관리 그룹을 선택합니다.

1. 페이지 위쪽의 _아티팩트_ 탭 또는 페이지 아래쪽의 **다음: 아티팩트** 를 선택합니다.

1. 청사진 샘플에 포함된 아티팩트 목록을 검토합니다. 아티팩트 대부분에 매개 변수는 나중에 정의할 것입니다. 청사진 샘플 검토가 끝나면 **초안 저장** 을 선택합니다.

### <a name="publish-the-sample-copy"></a>샘플 사본 게시

이제 작업 환경에 청사진 샘플 사본이 만들어졌습니다. 이 청사진 정의는 **초안** 모드로 만들어졌으며 먼저 **게시** 해야만 할당하고 배포할 수 있습니다. 청사진 샘플의 복사본을 환경 및 요구 사항에 맞게 사용자 지정할 수 있지만, 해당 수정으로 인해 캐나다 연방 PBMM 컨트롤을 준수하지 않게 될 수 있습니다.

1. 왼쪽 창에서 **모든 서비스** 를 선택합니다. **청사진** 을 검색하고 선택합니다.

1. 왼쪽 페이지에서 **청사진 정의** 를 선택합니다. 필터를 사용하여 청사진 샘플의 사본을 찾은 다음, 선택합니다.

1. 페이지 맨 위에서 **청사진 게시** 를 선택합니다. 오른쪽의 새 페이지에서 청사진 샘플 사본의 **버전** 을 지정합니다. 이 속성은 나중에 수정할 경우에 유용합니다. "캐나다 연방 PBMM 청사진 샘플에서 게시된 첫 번째 버전"과 같은 **변경 메모** 를 입력합니다. 그런 후 페이지 아래쪽의 **게시** 를 선택합니다.

### <a name="assign-the-sample-copy"></a>샘플 사본 할당

청사진 샘플 사본이 **게시** 되면 저장된 관리 그룹 내의 구독에 할당할 수 있습니다. 이 단계에서는 청사진 샘플 사본의 배포를 고유하게 만들기 위해 매개 변수가 제공됩니다.

1. 왼쪽 창에서 **모든 서비스** 를 선택합니다. **청사진** 을 검색하고 선택합니다.

1. 왼쪽 페이지에서 **청사진 정의** 를 선택합니다. 필터를 사용하여 청사진 샘플의 사본을 찾은 다음, 선택합니다.

1. 청사진 정의 페이지 위쪽에서 **청사진 할당** 을 선택합니다.

1. 청사진 할당의 매개 변수 값을 다음과 같이 지정합니다.

   - 기본 사항

     - **구독**: 청사진 샘플 사본을 저장한 관리 그룹에 있는 하나 이상의 구독을 선택합니다. 둘 이상의 구독을 선택하면 입력한 매개 변수를 사용하여 각 구독의 할당이 생성됩니다.
     - **할당 이름**: 청사진의 이름에 따라 이 이름이 미리 채워집니다.
       필요에 따라 변경하거나 그대로 둡니다.
     - **위치**: 관리 ID를 만들 지역을 선택합니다. Azure Blueprints는 이 관리 ID를 사용하여 할당된 청사진의 모든 아티팩트를 배포합니다. 자세히 알아보려면 [Azure 리소스의 관리 ID](../../../active-directory/managed-identities-azure-resources/overview.md)를 참조하세요.
     - **청사진 정의 버전**: 청사진 샘플 사본의 **게시됨** 버전을 선택합니다.

   - 할당 잠금

     환경에 맞는 청사진 잠금 설정을 선택합니다. 자세한 내용은 [청사진 리소스 잠금](../concepts/resource-locking.md)을 참조하세요.

   - 관리 ID

     기본값 _시스템이 할당한_ 관리 ID 옵션을 유지합니다.

   - 아티팩트 매개 변수

     이 섹션에 정의된 매개 변수는 정의된 아티팩트에 적용됩니다. 이러한 매개 변수는 청사진 할당 중에 정의되므로 [동적 매개 변수](../concepts/parameters.md#dynamic-parameters)입니다. 전체 목록 또는 아티팩트 매개 변수 및 해당 설명은 [아티팩트 매개 변수 테이블](#artifact-parameters-table)을 참조하세요.

1. 모든 매개 변수를 입력한 후에는 페이지 아래쪽에서 **할당** 을 선택합니다. 청사진 할당이 생성되고 아티팩트 배포가 시작됩니다. 배포에는 약 1 시간이 걸립니다. 배포의 상태를 확인하려면 청사진 할당을 엽니다.

> [!WARNING]
> Azure Blueprints 서비스 및 기본 제공 청사진 샘플은 **무료** 입니다. Azure 리소스는 [제품별로 가격이 책정](https://azure.microsoft.com/pricing/)됩니다. 이 청사진 샘플에서 배포되는 리소스를 실행하는 비용을 추정하려면 [가격 계산기](https://azure.microsoft.com/pricing/calculator/)를 사용합니다.

### <a name="artifact-parameters-table"></a>아티팩트 매개 변수 테이블

다음 테이블은 청사진 아티팩트 매개 변수의 목록을 제공합니다.

|아티팩트 이름|아티팩트 형식|매개 변수 이름|Description|
|-|-|-|-|
|\[미리 보기\]: Linux VM용 Log Analytics 에이전트 배포 |정책 할당 |Linux VM용 Log Analytics 작업 영역 |자세한 내용은 [Azure Portal에서 Log Analytics 작업 영역 만들기](../../../azure-monitor/logs/quick-create-workspace.md)를 참조하세요. |
|\[미리 보기\]: Linux VM용 Log Analytics 에이전트 배포 |정책 할당 |선택 사항: 범위에 추가할 Linux OS를 지원하는 VM 이미지 목록 |빈 배열(`[]`)을 사용하여 선택적 매개 변수가 없음을 나타낼 수 있습니다. |
|\[미리 보기\]: Windows VM용 Log Analytics 에이전트 배포 |정책 할당 |선택 사항: 범위에 추가할 Windows OS를 지원하는 VM 이미지 목록 |빈 배열(`[]`)을 사용하여 선택적 매개 변수가 없음을 나타낼 수 있습니다. |
|\[미리 보기\]: Windows VM용 Log Analytics 에이전트 배포 |정책 할당 |Windows VM용 Log Analytics 작업 영역 |자세한 내용은 [Azure Portal에서 Log Analytics 작업 영역 만들기](../../../azure-monitor/logs/quick-create-workspace.md)를 참조하세요. |
|\[미리 보기\]: 캐나다 연방 PBMM 컨트롤 감사 및 감사 요구 사항을 지원하기 위한 특정 VM 확장 배포 |정책 할당 |VM을 구성해야 하는 Log Analytics 작업 영역 ID |VM을 구성해야 하는 Log Analytics 작업 영역의 ID(GUID)입니다. |
|\[미리 보기\]: 캐나다 연방 PBMM 컨트롤 감사 및 감사 요구 사항을 지원하기 위한 특정 VM 확장 배포 |정책 할당 |진단 로그를 사용해야 하는 리소스 종류 목록 |진단 로그 설정을 사용하도록 설정하지 않은 경우 감사할 리소스 종류의 목록입니다. 허용되는 값은 [Azure Monitor 진단 로그 스키마](../../../azure-monitor/essentials/resource-logs-schema.md#service-specific-schemas)에서 확인할 수 있습니다. |
|\[미리 보기\]: 캐나다 연방 PBMM 컨트롤 감사 및 감사 요구 사항을 지원하기 위한 특정 VM 확장 배포 |정책 할당 |관리자 그룹 |그룹입니다. 예: `Administrator; myUser1; myUser2` |
|\[미리 보기\]: 캐나다 연방 PBMM 컨트롤 감사 및 감사 요구 사항을 지원하기 위한 특정 VM 확장 배포 |정책 할당 |Windows VM 관리자 그룹에 포함해야 하는 사용자 목록 |관리자 로컬 그룹에 포함해야 하는 구성원의 세미콜론으로 구분된 목록입니다. 예: `Administrator; myUser1; myUser2` |
|스토리지 계정에 Advanced Threat Protection 배포 |정책 할당 |영향 |정책의 영향에 대한 내용은 [Azure Policy의 영향 파악](../../policy/concepts/effects.md)에서 찾을 수 있습니다. |
|SQL Server에 감사 배포 |정책 할당 |보존 기간 값(일)(0은 보존 기간에 제한이 없음을 표시) |보존 일 수(선택 사항, 지정하지 않는 경우 _180_ 일) |
|SQL Server에 감사 배포 |정책 할당 |SQL 서버 감사를 위한 스토리지 계정의 리소스 그룹 이름 |감사에서는 데이터베이스 이벤트를 Azure Storage 계정의 감사 로그에 씁니다(스토리지 계정은 각 지역에 만들어지고, 각 지역에 만들어지는 SQL Server를 해당 지역의 모든 서버가 공유). 중요 - 적절한 감사 작업을 위해 리소스 그룹 또는 스토리지 계정을 삭제하거나 이름을 바꾸지 마세요. |
|네트워크 보안 그룹에 대한 진단 설정 배포 |정책 할당 |네트워크 보안 그룹 진단을 위한 스토리지 계정 접두사 |이 접두사와 네트워크 보안 그룹 위치가 결합되어 스토리지 계정 이름이 구성됩니다. |
|네트워크 보안 그룹에 대한 진단 설정 배포 |정책 할당 |네트워크 보안 그룹 진단을 위한 스토리지 계정의 리소스 그룹 이름(있어야 함) |스토리지 계정이 만들어지는 리소스 그룹. 이 리소스 그룹은 이미 있어야 합니다. |

## <a name="next-steps"></a>다음 단계

청사진 및 사용 방법에 대한 추가 문서:

- [청사진 수명 주기](../concepts/lifecycle.md)에 대해 알아봅니다.
- [정적 및 동적 매개 변수](../concepts/parameters.md) 사용 방법 이해
- [청사진 시퀀싱 순서](../concepts/sequencing-order.md)를 사용자 지정하는 방법 알아보기
- [청사진 리소스 잠금](../concepts/resource-locking.md)을 활용하는 방법 알아보기
- [기존 할당을 업데이트](../how-to/update-existing-assignments.md)하는 방법 알아보기
