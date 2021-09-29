---
title: Azure App Service로 마이그레이션할 웹앱을 평가하는 자습서
description: Azure Migrate에서 Azure App Service에 대한 평가를 만드는 방법 알아보기
author: rashi-ms
ms.author: rajosh
ms.topic: tutorial
ms.date: 07/28/2021
ms.openlocfilehash: 7d37b3755e79c8117bbec7269e7ef97e8edb7429
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124782312"
---
# <a name="tutorial-assess-aspnet-web-apps-for-migration-to-azure-app-service"></a>자습서: Azure App Service로 마이그레이션할 ASP.NET 웹앱 평가

Azure로 마이그레이션하는 과정의 일환으로 온-프레미스 워크로드를 평가하여 클라우드 준비 상태를 측정하고, 위험을 식별하고, 비용과 복잡성을 예측합니다.
이 문서에서는 Azure Migrate: 검색 및 평가 도구를 사용하여 Azure App Service로의 마이그레이션을 준비하는 과정에서 IIS 웹 서버에서 실행 중인 것으로 검색된 ASP.NET 웹앱을 평가하는 방법을 보여줍니다.

이 자습서에서는 다음과 같은 작업을 수행하는 방법을 살펴봅니다.

> [!div class="checklist"]
> * 웹앱 구성 데이터에 따라 평가를 실행합니다.
> * Azure App Service 평가 검토

> [!NOTE]
> 이 자습서에서는 시나리오를 시도할 수 있는 가장 빠른 경로를 보여 주며, 가능한 경우 기본 옵션을 사용합니다. 

## <a name="prerequisites"></a>사전 요구 사항

- Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/pricing/free-trial/)을 만듭니다.
- 이 자습서를 수행하여 Azure App Service로 마이그레이션할 웹앱을 평가하기 전에, Azure Migrate 어플라이언스를 사용하여 [이 자습서에 따라](tutorial-discover-vmware.md) 평가할 웹앱을 검색했는지 확인합니다.
- 기존 프로젝트에서 이 기능을 사용해 보려면 이 문서의 [필수 구성 요소](how-to-discover-sql-existing-project.md)를 완료했는지 확인하세요.

## <a name="run-an-assessment"></a>평가 실행

다음과 같이 평가를 실행합니다.

1. **개요** 페이지 > **서버, 데이터베이스 및 웹앱** 에서 **검색, 평가 및 마이그레이션** 을 클릭합니다.
    :::image type="content" source="./media/tutorial-assess-webapps/discover-assess-migrate.png" alt-text="Azure Migrate 개요 페이지":::
2. **Azure Migrate: 검색 및 평가** 에서 **평가** 를 클릭하고 평가 유형을 **Azure App Service** 로 선택합니다.
    :::image type="content" source="./media/tutorial-assess-webapps/assess.png" alt-text="평가 유형을 Azure App Service로 선택하는 드롭다운":::
3. **평가 만들기** 에서 **Azure App Service** 로 미리 선택된 평가 유형과 **Azure Migrate 어플라이언스에서 검색된 서버** 로 설정된 검색 원본을 볼 수 있습니다.
4. **편집** 을 클릭하여 평가 속성을 검토합니다.
     :::image type="content" source="./media/tutorial-assess-webapps/assess-webapps.png" alt-text="평가 속성을 사용자 지정할 수 있는 편집 단추":::
5. 다음은 Azure App Service 평가 속성에 포함된 항목입니다.

    **속성** | **세부 정보**
    --- | ---
    **대상 위치** | 마이그레이션할 Azure 지역입니다. Azure App Service 구성 및 비용 권장 사항은 사용자가 지정한 위치를 기반으로 합니다.
    **격리 필요** | 더 빠른 프로세서, SSD 스토리지가 있는 Dv2 시리즈 VM을 사용하여 Azure 데이터 센터의 프라이빗 및 전용 환경에서 웹앱을 실행하고 표준 플랜에 비해 메모리 대 코어 비율을 두 배로 늘리려면 예를 선택합니다.
    **예약 인스턴스** | 평가의 비용 예측이 고려할 수 있도록 예약 인스턴스를 지정합니다.<br/><br/> 예약 인스턴스 옵션을 선택하면 “할인율(%)”을 지정할 수 없습니다.
    **제품** | 등록한 [Azure 제품](https://azure.microsoft.com/support/legal/offer-details/)입니다. 평가는 해당 제품에 대한 비용을 추정합니다.
    **통화** | 계정의 청구 통화입니다.
    **할인(%)** | Azure 제품에 적용되는 구독별 할인입니다. 기본 설정은 0%입니다.
    **EA 구독** | 비용 예측 시 EA(기업계약) 구독을 사용하도록 지정합니다. 구독에 적용되는 할인을 고려합니다. <br/><br/> 예약 인스턴스 및 할인율(%) 속성에 대한 설정을 기본 설정으로 둡니다.

    :::image type="content" source="./media/tutorial-assess-webapps/webapps-assessment-properties.png" alt-text="App Service 평가 속성":::

1. **평가 만들기** 에서 다음을 클릭합니다.
1. **평가할 서버 선택** > **평가 이름** 에서 평가 이름을 지정합니다.
1. **그룹 선택 또는 만들기** 에서 **새로 만들기** 를 선택하고 그룹 이름을 지정합니다.
1. 어플라이언스를 선택하고 그룹에 추가할 서버를 선택합니다. 그런 후 "다음"을 클릭합니다.
1. **검토 + 평가 만들기** 에서 평가 세부 정보를 검토하고, 평가 만들기를 클릭하여 그룹을 만들고 평가를 실행합니다.
1. 평가를 만든 후에는 **서버, 데이터베이스 및 웹앱** > **Azure Migrate: 검색 및 평가** 타일로 이동하고 타일의 맨 위쪽에서 새로 고침 옵션을 클릭하여 타일 데이터를 새로 고칩니다. 데이터가 새로 고쳐질 때까지 기다립니다.
    :::image type="content" source="./media/tutorial-assess-webapps/tile-refresh.png" alt-text="검색 및 평가 도구 데이터 새로 고침":::
1. Azure App Service 평가 옆에 있는 숫자를 클릭합니다.
    :::image type="content" source="./media/tutorial-assess-webapps/assessment-webapps-navigation.png" alt-text="만든 평가로 이동":::
1. 보려는 평가 이름을 클릭합니다.

## <a name="review-an-assessment"></a>평가 검토

**평가를 보려면** 다음을 수행합니다.

1. **서버, 데이터베이스 및 웹앱** > **Azure Migrate: 검색 및 평가** > Azure App Service 평가 옆에 있는 숫자를 클릭합니다.
2. 보려는 평가 이름을 클릭합니다.
:::image type="content" source="./media/tutorial-assess-webapps/assessment-webapps-summary.png" alt-text="App Service 평가 개요":::
3. 평가 요약을 검토합니다. 평가 속성을 편집하거나 평가를 다시 계산할 수도 있습니다.

#### <a name="azure-app-service-readiness"></a>Azure App Service 준비 상태

평가된 웹앱의 분포를 나타냅니다. 드릴다운하여 Azure App Service로 마이그레이션하기 전에 수정할 수 있는 마이그레이션 문제/경고에 대한 세부 정보를 파악할 수 있습니다. [자세한 정보](concepts-azure-webapps-assessment-calculation.md) 권장 App Service SKU를 보고 Azure App Service 마이그레이션을 계획할 수도 있습니다.

#### <a name="azure-app-service-cost-details"></a>Azure App Service 비용 세부 정보

[App Service 요금제](../app-service/overview-hosting-plans.md)는 사용하는 컴퓨팅 리소스에 대해 [요금](https://azure.microsoft.com/pricing/details/app-service/windows/)을 부과합니다.

### <a name="review-readiness"></a>준비 상태 검토

1. **Azure App Service 준비 상태** 를 클릭합니다.
    :::image type="content" source="./media/tutorial-assess-webapps/assessment-webapps-readiness.png" alt-text="Azure App Service 준비 상태 세부 정보":::
1. 평가된 웹앱에 대한 표의 Azure App Service 준비 상태 열을 검토합니다.
    1. 호환성 문제가 발견되지 않은 경우 대상 배포 유형의 준비 상태가 **준비 완료** 로 표시됩니다.
    1. 특정 대상 배포 유형으로의 마이그레이션을 차단하지 않는 중요하지 않은 호환성 문제(예: 성능 저하 또는 지원되지 않는 기능)가 있는 경우 준비 상태는 **조건과 함께 준비 완료**(하이퍼링크)로 표시되고 **경고** 세부 정보와 권장 수정 지침이 함께 표시됩니다.
    1. 특정 대상 배포 유형으로의 마이그레이션을 차단할 수 있는 호환성 문제가 있는 경우 준비 상태는 **이슈** 세부 정보 및 권장되는 수정 지침을 나타내면서 **준비 안 됨** 으로 표시됩니다.
    1. 검색이 아직 진행 중이거나 웹앱에 검색 문제가 있는 경우 평가에서 해당 웹앱의 준비 상태를 파악할 수 없으므로 준비 상태는 **알 수 없음** 으로 표시됩니다.
1. 아래 행렬에 따라 결정되는 웹앱에 대한 권장 SKU를 검토합니다.

    **격리 필요** | **예약 인스턴스** | **App Service 요금제/SKU**
    --- | --- | ---
    예  | 예 | I1
    예  | 아니요  | I1
    아니요  | 예 | P1v3
    아니요  | 아니요 | P1v2

    **Azure App Service 준비 상태** | **App Service SKU 확인** | **예상 비용 확인**
    --- | --- | ---
    준비  | 예 | 예
    조건부 준비 완료  | 예  | 예
    준비 안 됨  | 예 | 예
    알 수 없음  | 아니요 | 아니요

1. 컴퓨팅 리소스 및 동일한 요금제에 포함된 다른 웹앱과 같은 App Service 요금제 세부 정보를 보려면 표의 App Service 요금제 하이퍼링크를 클릭합니다.

### <a name="review-cost-estimates"></a>예상 비용 검토

평가 요약은 App Service에서 웹앱을 호스트하는 데 필요한 월별 예상 비용을 보여 줍니다. App Service에서는 웹앱이 아닌 App Service 요금제에 따라 요금을 지불합니다. 하나 이상의 앱은 동일한 컴퓨팅 리소스(또는 동일한 App Service 계획)에서 실행하도록 구성될 수 있습니다. App Service 계획에 정의된 대로 이 App Service 계획에 추가한 모든 앱은 이러한 컴퓨팅 리소스에서 실행됩니다.
비용을 최적화하기 위해 Azure Migrate 평가는 권장되는 각 App Service 요금제에 여러 웹앱을 할당합니다. 각 요금제 인스턴스에 할당되는 웹앱 수는 아래 표와 같습니다.

**App Service 계획** | **App Service 요금제당 웹앱 수**
--- | ---
I1  | 8
P1v2  | 8
P1v3  | 16

:::image type="content" source="./media/tutorial-assess-webapps/assessment-webapps-cost.png" alt-text="비용 세부 정보":::

## <a name="next-steps"></a>다음 단계

Azure App Service 평가를 계산하는 방법에 대해 [자세히 알아보세요](concepts-azure-webapps-assessment-calculation.md).