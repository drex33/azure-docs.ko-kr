---
title: 단일 테넌트 Azure Logic Apps의 스토리지 비용 예측
description: Logic Apps 스토리지 계산기를 사용하여 워크플로의 스토리지 비용을 예측합니다.
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, azla
ms.topic: how-to
ms.date: 11/10/2021
ms.openlocfilehash: 995c620eeafc5a627a9f9d94733c80de47f2928d
ms.sourcegitcommit: 901ea2c2e12c5ed009f642ae8021e27d64d6741e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/12/2021
ms.locfileid: "132369401"
---
# <a name="estimate-storage-costs-for-workflows-in-single-tenant-azure-logic-apps"></a>단일 테넌트 Azure Logic Apps에 대한 워크플로 비용 예측

Azure Logic Apps는 모든 스토리지 작업에 [Azure Storage](../storage/index.yml)를 사용합니다. 일반 다중 테넌트 Azure Logic Apps에서는 스토리지 사용량 및 비용을 논리 앱에 연결합니다. 현재 단일 테넌트 Azure Logic Apps에서는 사용자의 스토리지 계정을 사용할 수 있습니다. 스토리지 비용은 Azure 청구서에 별도로 표시됩니다. 이 기능으로 논리 앱 데이터를 보다 유연하게 관리할 수 있습니다.

> [!NOTE]
> 이 문서는 단일 테넌트 Azure Logic Apps 환경의 워크플로에 적용됩니다. 이러한 워크플로는 동일한 논리 앱과 동일한 스토리지를 공유하는 단일 테넌트에서 존재합니다. 자세한 내용은 [단일 테넌트와 다중 테넌트 및 통합 서비스 환경 비교](single-tenant-overview-compare.md)를 참조하세요.

스토리지 비용은 워크플로 콘텐츠에 따라 달라집니다. 트리거, 작업 및 페이로드가 다르면 스토리지 작업 및 요구 사항도 달라집니다. 이 문서에서는 단일 테넌트 기반 논리 앱에서 고유한 Azure Storage 계정을 사용할 때 스토리지 비용을 예측하는 방법을 설명합니다. 먼저 Logic Apps 스토리지 계산기를 사용하여 [수행할 스토리지 작업 수를 예측](#estimate-storage-needs)할 수 있습니다. 그런 다음, Azure 가격 계산기에서 이러한 수치를 사용하여 [가능한 스토리지 비용을 예측](#estimate-storage-costs)할 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

* Azure 계정 및 구독 구독이 없는 경우 [Azure 체험 계정에 등록](https://azure.microsoft.com/free/)합니다.

* 단일 테넌트 기반의 논리 앱 워크플로입니다. [Azure Portal](create-single-tenant-workflows-azure-portal.md) 또는 [Visual Studio Code를 사용하여](create-single-tenant-workflows-visual-studio-code.md) 워크플로를 만들 수 있습니다. 아직 워크플로가 없는 경우 스토리지 계산기에서 소형, 중형, 대형 워크플로 샘플을 사용할 수 있습니다.

* 워크플로에 사용할 Azure 스토리지 계정입니다. 스토리지 계정이 없는 경우 [스토리지 계정을 만듭니다](../storage/common/storage-account-create.md).

## <a name="get-your-workflows-json-code"></a>워크플로의 JSON 코드 가져오기

예측할 워크플로가 있는 경우 워크플로에 대한 JSON 코드를 가져옵니다.

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.

1. **논리 앱** 서비스로 이동하여 워크플로를 선택합니다.

1. 논리 앱 메뉴의 **개발 도구** 에서 **논리 앱 코드 보기** 를 선택합니다.

1. 워크플로의 JSON 코드를 복사합니다.

## <a name="estimate-storage-needs"></a>스토리지 요구량 계산

1. [Logic Apps 스토리지 계산기](https://logicapps.azure.com/calculator)로 이동합니다.

   :::image type="content" source="./media/estimate-storage-costs/storage-calculator.png" alt-text="입력 인터페이스를 보여주는 Logic Apps 스토리지 계산기의 스크린샷." lightbox="./media/estimate-storage-costs/storage-calculator.png":::

1. 단일 테넌트 기반 논리 앱 워크플로에 대한 JSON 코드를 입력, 업로드 또는 선택합니다.

   * 이전 섹션에서 코드를 복사한 경우 **붙여넣기 또는 업로드 workflow.json** 상자에 붙여넣습니다. 
   * **workflow.json** 파일이 로컬에 저장되어 있는 경우 **워크플로 선택** 에서 **파일 찾아보기** 를 선택합니다. 파일을 선택한 다음 **열기** 를 선택합니다.
   * 아직 워크플로가 없는 경우에는 **워크플로 선택** 에서 샘플 워크플로 중 하나를 선택합니다.

1. **고급 옵션** 에서 옵션을 검토합니다. 이러한 설정은 워크플로 유형에 따라 달라지며 다음을 포함할 수 있습니다.

   * 루프 실행 횟수를 입력하는 옵션.
   * 페이로드가 32KB를 초과하는 모든 작업을 선택하는 옵션.

1. **월별 실행** 에 매월 워크플로를 실행하는 횟수를 입력합니다.

1. **계산** 을 선택하고 계산이 실행될 때까지 기다립니다.

1. **스토리지 작업 분석 및 계산 단계** 에서 예상 **작업 수** 를 검토합니다.

    실행별 및 월별 예상 작업 수를 두 테이블로 볼 수 있습니다. 다음 작업이 표시됩니다.

    * **Blob(읽기)** - Azure Blob Storage 읽기 작업용.
    * **Blob(쓰기)** - Azure Blob Storage 쓰기 작업용.
    * **큐** - Azure 큐의 큐 클래스 2 작업용.
    * **테이블** - Azure Table Storage 작업용.

    각 작업의 최소, 최대 및 "최선의 추측" 수가 있습니다. 개별 시나리오에 따라 [스토리지 작업 비용을 예상하는](#estimate-storage-costs) 데 사용할 가장 관련성이 큰 수를 선택합니다. 권장 사항으로 정확도를 위해 "최상의 추측" 수를 사용 합니다. 그러나 최대 수를 사용하여 비용 추산치에 버퍼가 있는지 확인할 수도 있습니다.

    :::image type="content" source="./media/estimate-storage-costs/storage-calculator-results.png" alt-text="예상 작업이 있는 출력이 표시되는 Logic Apps 스토리지 계산기의 스크린샷." lightbox="./media/estimate-storage-costs/storage-calculator-results.png":::

## <a name="estimate-storage-costs"></a>스토리지 비용 예측

[논리 앱 워크플로의 스토리지 요구량을 계산한](#estimate-storage-needs) 후에 월별 스토리지 비용을 예측할 수 있습니다. 다음 스토리지 작업 유형의 가격을 예측할 수 있습니다.

* [Blob 스토리지 읽기 및 쓰기 작업](#estimate-blob-storage-operations-costs)
* [큐 스토리지 작업](#estimate-queue-operations-costs)
* [테이블 스토리지 작업](#estimate-table-operations-costs)

### <a name="estimate-blob-storage-operations-costs"></a>Blob 스토리지 작업 비용 예측

논리 앱의 Blob 스토리지 작업의 월별 비용을 예측하려면 다음을 수행합니다.

1. [Azure 가격 계산기](https://azure.microsoft.com/pricing/calculator/)로 이동합니다.

1. **제품** 탭에서 **스토리지** &gt; **스토리지 계정** 을 선택합니다. 또는 **검색 표시줄** 검색 상자에 **스토리지 계정** 을 입력하고 타일을 선택합니다.

   :::image type="content" source="./media/estimate-storage-costs/pricing-calculator-storage-tile.png" alt-text="스토리지 계정 보기를 추가하는 타일이 표시된 Azure 가격 계산기의 스크린샷." lightbox="./media/estimate-storage-costs/pricing-calculator-storage-tile.png":::

1. **스토리지 계정 추가** 알림에서 **보기** 를 선택하여 계산기의 **스토리지 계정** 섹션을 확인합니다. 또는 **스토리지 계정** 섹션으로 직접 이동합니다.

1. **지역** 에 대해 논리 앱의 지역을 선택합니다.

1. **유형** 에 대해 **블록 Blob 스토리지** 를 선택합니다.

1. **성능 계층** 에 대해 성능 계층을 선택합니다.

1. **중복성** 에 대해 중복성 수준을 선택합니다.

1. 기타 설정은 필요에 따라 조정합니다.

1. **쓰기 작업** 에서 Logic Apps 저장소 계산기의 **Blob (쓰기)** 작업 번호를 *1만으로 나눈* 값을 입력 합니다. 계산기가 저장소 작업을 위한 트랜잭션 단위로 작동 하기 때문에이 단계가 필요 합니다.

1. **읽기 작업** 에서 Logic Apps 저장소 계산기의 **Blob (읽기)** 작업 번호를 *1만으로 나눈* 값을 입력 합니다. 계산기가 저장소 작업을 위한 트랜잭션 단위로 작동 하기 때문에이 단계가 필요 합니다.

1. 예상 Blob 스토리지 작업 비용을 검토합니다.

### <a name="estimate-queue-operations-costs"></a>큐 작업 비용 추산

논리 앱의 큐 스토리지 작업의 월별 비용을 추산하려면 다음을 수행합니다.

1. [Azure 가격 계산기](https://azure.microsoft.com/pricing/calculator/)로 이동합니다.

1. **제품** 탭에서 **스토리지** &gt; **스토리지 계정** 을 선택합니다. 또는 **검색 표시줄** 검색 상자에 **스토리지 계정** 을 입력하고 타일을 선택합니다.

   :::image type="content" source="./media/estimate-storage-costs/pricing-calculator-storage-tile.png" alt-text="스토리지 계정 보기를 추가하는 타일이 표시된 Azure 가격 계산기의 스크린샷." lightbox="./media/estimate-storage-costs/pricing-calculator-storage-tile.png":::

1. **스토리지 계정 추가** 알림에서 **보기** 를 선택하여 계산기의 **스토리지 계정** 섹션을 확인합니다. 또는 **스토리지 계정** 섹션으로 직접 이동합니다.

1. **지역** 에 대해 논리 앱의 지역을 선택합니다.

1. **유형** 에 대해 **Queue Storage** 를 선택합니다.

1. **스토리지 계정 유형** 에 대해 스토리지 계정 유형을 선택합니다.

1. **중복성** 에 대해 중복성 수준을 선택합니다.

1. **큐 클래스 2 작업** 에서 Logic Apps 스토리지 계산기의 **큐** 작업 번호를 10,000으로 나눈 값으로 입력합니다. 계산기가 큐 작업의 트랜잭션 단위로 작동하므로 이 단계는 필수적입니다.

1. 추산된 큐 작업 비용을 검토합니다.

### <a name="estimate-table-operations-costs"></a>테이블 작업 비용 예측

논리 앱의 테이블 스토리지 작업의 월별 비용을 예측하려면 다음을 수행합니다.

1. [Azure 가격 계산기](https://azure.microsoft.com/pricing/calculator/)로 이동합니다.

1. **제품** 탭에서 **스토리지** &gt; **스토리지 계정** 을 선택합니다. 또는 **검색 표시줄** 검색 상자에 **스토리지 계정** 을 입력하고 타일을 선택합니다.

   :::image type="content" source="./media/estimate-storage-costs/pricing-calculator-storage-tile.png" alt-text="스토리지 계정 보기를 추가하는 타일이 표시된 Azure 가격 계산기의 스크린샷." lightbox="./media/estimate-storage-costs/pricing-calculator-storage-tile.png":::

1. **스토리지 계정 추가** 알림에서 **보기** 를 선택하여 계산기의 **스토리지 계정** 섹션을 확인합니다. 또는 **스토리지 계정** 섹션으로 직접 이동합니다.

1. **지역** 에 대해 논리 앱의 지역을 선택합니다.

1. **유형** 에 대해 **테이블 스토리지** 를 선택합니다.

1. **계층** 에 대해 성능 계층을 선택합니다.

1. **중복성** 에 대해 중복성 수준을 선택합니다.

1. **스토리지 트랜잭션** 에서 Logic Apps 스토리지 계산기의 **테이블** 작업 번호를 10,000으로 나눈 값으로 입력합니다. 계산기가 큐 작업의 트랜잭션 단위로 작동하므로 이 단계는 필수적입니다.

1. 추산된 테이블 스토리지 작업 비용을 검토합니다.

## <a name="next-step"></a>다음 단계

> [!div class="nextstepaction"]
> [Logic Apps 비용 계획 및 관리](plan-manage-costs.md)