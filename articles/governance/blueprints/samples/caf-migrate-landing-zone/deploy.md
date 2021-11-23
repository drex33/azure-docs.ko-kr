---
title: CAF Migration 방문 영역 청사진 샘플 배포
description: 청사진 아티팩트 매개 변수 세부 정보를 포함하여 CAF Migration 방문 영역 청사진 샘플에 대한 단계를 배포합니다.
ms.date: 03/12/2021
ms.topic: sample
ms.openlocfilehash: 4a930f7cc285778f54090c760c0109fe98bcab43
ms.sourcegitcommit: 3d04177023a3136832adb561da831ccc8e9910c7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/23/2021
ms.locfileid: "132938498"
---
# <a name="deploy-the-microsoft-cloud-adoption-framework-for-azure-migrate-landing-zone-blueprint-sample"></a>Azure에 대한 Microsoft 클라우드 채택 프레임워크 Migration 방문 영역 청사진 샘플 배포

Azure Blueprints CAF Migration 방문 영역 청사진 샘플을 배포하려면 다음 단계를 수행해야 합니다.

> [!div class="checklist1"]
> - [CAF Foundation](../caf-foundation/index.md) 청사진 샘플을 배포하는 것이 좋습니다.

> [!div class="checklist2"]
> - 샘플에서 새 청사진 만들기
> - 샘플 사본을 **게시됨** 으로 표시
> - 기존 구독에 청사진 사본 할당

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free)을 만듭니다.

## <a name="create-blueprint-from-sample"></a>샘플에서 청사진 만들기

먼저 샘플을 스타터로 사용하여 환경 내에 새 청사진을 만들어 청사진 샘플을 구현합니다.

1. 왼쪽 창에서 **모든 서비스** 를 선택합니다. **청사진** 을 검색하고 선택합니다.

1. **시작** 페이지 왼쪽에서 _청사진 만들기_ 아래의 **만들기** 단추를 선택합니다.

1. **CAF Migration 방문 영역** 청사진 샘플을 _다른 샘플_ 아래에서 찾은 다음, **이 샘플 사용** 을 선택합니다.

1. 청사진 샘플의 _기본 사항_ 을 입력합니다.
   - **청사진 이름** CAF 마이그레이션 랜딩 존 청사진 샘플의 복사본에 사용할 이름을 입력합니다.
   - **위치 정의** 줄임표를 사용하고 샘플 사본을 저장할 관리 그룹을 선택합니다.

1. 페이지 위쪽의 _아티팩트_ 탭 또는 페이지 아래쪽의 **다음: 아티팩트** 를 선택합니다.

1. 청사진 샘플을 구성하는 아티팩트 목록을 검토합니다. 아티팩트 대부분에 매개 변수는 나중에 정의할 것입니다. 청사진 샘플 검토가 끝나면 **초안 저장** 을 선택합니다.

## <a name="publish-the-sample-copy"></a>샘플 사본 게시

이제 작업 환경에 청사진 샘플 사본이 만들어졌습니다. 이 청사진 정의는 **초안** 모드로 만들어졌으며 먼저 **게시** 해야만 할당하고 배포할 수 있습니다. 청사진 샘플의 사본을 환경 및 필요에 맞게 사용자 지정할 수 있지만, 해당 수정으로 CAF Migrate 방문 영역 지침에서 벗어날 수 있습니다.

1. 왼쪽 창에서 **모든 서비스** 를 선택합니다. **청사진** 을 검색하고 선택합니다.

1. 왼쪽 페이지에서 **청사진 정의** 를 선택합니다. 필터를 사용하여 청사진 샘플의 사본을 찾은 다음, 선택합니다.

1. 페이지 맨 위에서 **청사진 게시** 를 선택합니다. 오른쪽의 새 페이지에서 청사진 샘플 사본의 **버전** 을 지정합니다. 이 속성은 나중에 수정할 경우에 유용합니다. “CAF Migration 방문 영역 청사진 샘플에서 게시된 첫 번째 버전”과 같은 **변경 메모** 를 입력합니다. 그런 후 페이지 아래쪽의 **게시** 를 선택합니다.

## <a name="assign-the-sample-copy"></a>샘플 사본 할당

청사진 샘플 사본이 **게시** 되면 저장된 관리 그룹 내의 구독에 할당할 수 있습니다. 이 단계에서는 청사진 샘플 사본의 배포를 고유하게 만들기 위해 매개 변수가 제공됩니다.

1. 왼쪽 창에서 **모든 서비스** 를 선택합니다. **청사진** 을 검색하고 선택합니다.

1. 왼쪽 페이지에서 **청사진 정의** 를 선택합니다. 필터를 사용하여 청사진 샘플의 사본을 찾은 다음, 선택합니다.

1. 청사진 정의 페이지 위쪽에서 **청사진 할당** 을 선택합니다.

1. 청사진 할당의 매개 변수 값을 다음과 같이 지정합니다.

   - 기본 사항
     - **구독**: 청사진 샘플 사본을 저장한 관리 그룹에 있는 하나 이상의 구독을 선택합니다. 둘 이상의 구독을 선택하면 입력한 매개 변수를 사용하여 각 구독의 할당이 생성됩니다.
     - **할당 이름**: 청사진의 이름에 따라 이 이름이 미리 채워집니다.
       필요에 따라 변경하거나 그대로 둡니다.
     - **위치**: 관리 ID를 만들 지역을 선택합니다.
     - Azure Blueprints는 이 관리 ID를 사용하여 할당된 청사진의 모든 아티팩트를 배포합니다.
       자세히 알아보려면 [Azure 리소스의 관리 ID](../../../../active-directory/managed-identities-azure-resources/overview.md)를 참조하세요.
     - **청사진 정의 버전**: 청사진 샘플 사본의 **게시됨** 버전을 선택합니다.

   - 할당 잠금

     환경에 맞는 청사진 잠금 설정을 선택합니다. 자세한 내용은 [청사진 리소스 잠금](../../concepts/resource-locking.md)을 참조하세요.

   - 관리 ID

     기본 _시스템 할당_ 관리 ID 옵션 또는 _사용자 할당_ ID 옵션 중 하나를 선택합니다.

   - 청사진 매개 변수

     이 섹션에 정의된 매개 변수는 일관성을 제공하기 위해 청사진 정의의 아티팩트 대부분에서 사용됩니다.

     - **조직**: 고유한 조직 이름(예: Contoso 또는 Fabrikam)을 입력합니다.
     - **AzureRegion**: 배포할 Azure 지역을 선택합니다.

   - 아티팩트 매개 변수

     이 섹션에 정의된 매개 변수는 정의된 아티팩트에 적용됩니다. 이러한 매개 변수는 청사진 할당 중에 정의되므로 [동적 매개 변수](../../concepts/parameters.md#dynamic-parameters)입니다. 전체 목록 또는 아티팩트 매개 변수 및 해당 설명은 [아티팩트 매개 변수 테이블](#artifact-parameters-table)을 참조하세요.

1. 모든 매개 변수를 입력한 후에는 페이지 아래쪽에서 **할당** 을 선택합니다. 청사진 할당이 생성되고 아티팩트 배포가 시작됩니다. 배포에는 약 5분이 걸립니다. 배포의 상태를 확인하려면 청사진 할당을 엽니다.

> [!WARNING]
> Azure Blueprints 서비스 및 기본 제공 청사진 샘플은 **무료** 입니다. Azure 리소스는 [제품별로 가격이 책정](https://azure.microsoft.com/pricing/)됩니다. 이 청사진 샘플에서 배포되는 리소스를 실행하는 비용을 추정하려면 [가격 계산기](https://azure.microsoft.com/pricing/calculator/)를 사용합니다.

## <a name="artifact-parameters-table"></a>아티팩트 매개 변수 테이블

다음 테이블은 청사진 아티팩트 매개 변수의 목록을 제공합니다.

|아티팩트 이름|아티팩트 형식|매개 변수 이름|Description|
|-|-|-|-|
|vNET 방문 영역 배포|Resource Manager 템플릿|IPAddress_Space|**잠김** - 처음 두 옥텟(예: 10.0)을 제공|
|Key Vault 배포|Resource Manager 템플릿|KV-AccessPolicy|**잠김** - Key Vault에서 권한을 부여할 그룹 또는 사용자 개체 ID|
|Log Analytics 배포|Resource Manager 템플릿|LogAnalytics_DataRetention|**잠김** - Log Analytics에서 데이터가 유지되는 기간(일)|
|Log Analytics 배포|Resource Manager 템플릿|LogAnalytics_Location|**잠김** - 작업 영역을 설정할 때 사용되는 영역|
|Azure Migrate 배포|Resource Manager 템플릿|Azure_Migrate_Location|**잠김** - Azure Migrate를 배포할 지역 선택|

## <a name="next-steps"></a>다음 단계

지금까지 CAF Migrate 방문 영역 청사진 샘플을 배포하는 단계를 살펴보았으므로 다음 문서를 참조하여 아키텍처에 대해 알아보세요.

> [!div class="nextstepaction"]
> [CAF Migration 방문 영역 청사진 - 개요](./index.md)

청사진 및 사용 방법에 대한 추가 문서:

- [청사진 수명 주기](../../concepts/lifecycle.md)에 대해 알아봅니다.
- [정적 및 동적 매개 변수](../../concepts/parameters.md) 사용 방법 이해
- [청사진 시퀀싱 순서](../../concepts/sequencing-order.md)를 사용자 지정하는 방법 알아보기
- [청사진 리소스 잠금](../../concepts/resource-locking.md)을 활용하는 방법 알아보기
- [기존 할당을 업데이트](../../how-to/update-existing-assignments.md)하는 방법 알아보기
