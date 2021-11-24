---
title: Synapse Link 사용 계정에 대 한 Azure Cosmos DB 포털의 통합 Power BI 환경
description: 통합 Power BI 환경을 사용 하면 몇 번의 클릭 만으로 Azure Cosmos DB 데이터를 거의 실시간으로 시각화할 수 있습니다. Azure Portal에서 기본 제공 Power BI 통합 기능을 사용 합니다.
author: AnithaAdusumilli
ms.service: cosmos-db
ms.topic: how-to
ms.date: 11/23/2021
ms.author: anithaa
ms.custom: synapse-cosmos-db
ms.openlocfilehash: cb63e0150b1f7ec621dc7dd1506172a3f4891630
ms.sourcegitcommit: 56235f8694cc5f88db3afcc8c27ce769ecf455b0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133077519"
---
# <a name="integrated-power-bi-experience-in-azure-cosmos-db-portal-for-synapse-link-enabled-accounts"></a>Synapse Link 사용 계정에 대 한 Azure Cosmos DB 포털의 통합 Power BI 환경
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

통합 Power BI 환경을 사용 하면 몇 번의 클릭 만으로 Azure Cosmos DB 데이터를 거의 실시간으로 시각화할 수 있습니다. [Azure Synapse 링크](synapse-link.md)와 함께 Azure Portal의 기본 제공 Power BI 통합 기능을 사용 합니다.

Synapse 링크를 사용 하면 트랜잭션 워크 로드에 대 한 성능 또는 비용 영향 없이 ETL 파이프라인 없이 Power BI 대시보드를 빌드할 수 있습니다. [DirectQuery](/power-bi/connect-data/service-dataset-modes-understand#directquery-mode) 또는 [가져오기](/power-bi/connect-data/service-dataset-modes-understand#import-mode) 모드를 사용할 수 있습니다. DirectQuery를 사용 하면 데이터를 Power BI으로 가져오거나 복사 하지 않고 Azure Cosmos DB 계정의 라이브 데이터를 사용 하 여 대시보드를 작성할 수 있습니다.

## <a name="build-a-power-bi-report"></a>Power BI 보고서 작성

DirectQuery 모드의 Azure Cosmos DB 데이터에서 Power BI 보고서를 작성 하려면 다음 단계를 사용 합니다.

1. [Azure Portal](https://portal.azure.com/)에 로그인하고 Azure Cosmos DB 계정으로 이동합니다.

1. **통합** 섹션에서 **Power BI** 창을 열고 **시작** 을 선택 합니다.

   > [!NOTE]
   > 현재이 옵션은 SQL API 계정에만 사용할 수 있습니다.

1. Synapse Link를 사용 하 여 계정을 아직 사용 하도록 설정 하지 않은 경우 **Azure Synapse 링크 사용** 탭에서 **이 계정에 대 한 Azure Synapse 링크 사용** 섹션에서 사용 하도록 설정할 수 있습니다. 계정에 대해 Synapse 링크를 이미 사용 하도록 설정한 경우에는이 탭이 표시 되지 않습니다.

   > [!NOTE]
   > 일단 사용 하도록 설정 하면 Azure Synapse 링크를 사용 하지 않도록 설정할 수 없습니다. Azure Synapse 링크를 사용 하도록 설정 하면 비용에 영향을 줍니다. 자세한 내용은 [Azure Synapse 링크 가격 책정](synapse-link.md#pricing) 섹션을 참조 하세요.

1. **컨테이너에 대해 Azure Synapse 링크 사용** 섹션에서 Synapse를 사용 하도록 설정 하는 데 필요한 컨테이너 링크를 선택 합니다.

   * 일부 컨테이너에서 Synapse Link를 이미 사용 하도록 설정한 경우 컨테이너 이름 옆에 있는 확인란이 선택 됩니다. Power BI 시각화 하려는 데이터에 따라 선택적으로 선택 취소할 수 있습니다.

   * 기존 컨테이너에서 Synapse 링크를 사용 하도록 설정할 수 있습니다.

   > [!IMPORTANT]
   > 단기 용량 제약 조건으로 인해 기존 컨테이너에서 Synapse 링크를 사용 하도록 등록 합니다. 보류 중인 요청에 따라이 요청을 승인 하는 일은 한 주에서 1 주일까지 걸릴 수 있습니다. 문제 또는 질문이 있으면 [Azure Cosmos DB Synapse 팀](mailto:cosmosdbsynapselink@microsoft.com)에 문의 하세요.

   :::image type="content" source="./media/integrated-power-bi-synapse-link/register-synapse-link.png" alt-text="선택한 컨테이너에 대해 Synapse 링크를 등록 합니다." border="true" lightbox="./media/integrated-power-bi-synapse-link/register-synapse-link.png":::

   > [!NOTE]
   > 각 구독 당 한 번만 등록 하면 됩니다. 구독이 승인 되 면 해당 구독 내의 모든 적격 계정에서 기존 컨테이너에 대해 Synapse 링크를 사용 하도록 설정할 수 있습니다.

1. **등록** 을 선택 하 여 기존 계정에서 Synapse 링크를 사용 하도록 설정 합니다. 상태가 "등록 보류 중"으로 변경 됩니다. Azure Cosmos DB 팀에서 요청을 승인한 후에는이 단추가 사라지고 기존 컨테이너를 선택할 수 있습니다.

1. 기존 컨테이너를 선택 하 고 **다음** 을 선택 합니다.

   컨테이너에서 Synapse 링크를 사용 하도록 설정 하는 경우 해당 컨테이너의 데이터가 포함 되지 않습니다. 나중에이 탭으로 돌아와서 컨테이너를 사용 하는 경우 데이터를 가져와야 합니다.

   :::image type="content" source="./media/integrated-power-bi-synapse-link/synapse-link-progress-existing-containers.png" alt-text="기존 컨테이너에서 활성화 된 Synapse 링크의 진행률입니다." border="true" lightbox="./media/integrated-power-bi-synapse-link/synapse-link-progress-existing-containers.png":::

1. 컨테이너의 데이터 양에 따라 Synapse 링크를 사용 하도록 설정 하는 데 시간이 걸릴 수 있습니다. 자세히 알아보려면 [기존 컨테이너에서 Synapse 링크 사용](configure-synapse-link.md#update-analytical-ttl) 문서를 참조 하세요.  

   다음 화면에 표시 된 것 처럼 포털에서 진행률을 확인할 수 있습니다. 진행률이 100%에 도달 하면 컨테이너는 Synapse 링크로 활성화 됩니다.

   :::image type="content" source="./media/integrated-power-bi-synapse-link/synapse-link-existing-containers-registration-complete.png" alt-text="선택한 컨테이너에서 Synapse 링크를 사용 하도록 설정 했습니다." border="true" lightbox="./media/integrated-power-bi-synapse-link/synapse-link-existing-containers-registration-complete.png":::

1. **작업 영역 선택** 탭에서 Azure Synapse Analytics 작업 영역을 선택 하 고 **다음** 을 선택 합니다. 이렇게 하면 이전에 선택한 컨테이너에 대해 Synapse Analytics에서 T SQL 보기가 자동으로 만들어집니다. Cosmos DB를 Power BI에 연결 하는 데 필요한 T SQL 보기에 대 한 자세한 내용은 [뷰 준비](../synapse-analytics/sql/tutorial-connect-power-bi-desktop.md#3---prepare-view) 문서를 참조 하세요.

1. 기존 작업 영역을 선택 하거나 새 작업 영역을 만들 수 있습니다. 기존 작업 영역을 선택 하려면 **구독**, **작업 영역** 및 **데이터베이스** 세부 정보를 제공 합니다. Azure Portal는 Azure AD 자격 증명을 사용 하 여 자동으로 Synapse 작업 영역에 연결 하 고 보기를 만듭니다. 이 작업 영역에 대 한 "Synapse administrator" 권한이 있는지 확인 합니다.

   :::image type="content" source="./media/integrated-power-bi-synapse-link/synapse-create-views.png" alt-text="Synapse Link 작업 영역을 커넥트 하 고 뷰를 만듭니다." border="true" lightbox="./media/integrated-power-bi-synapse-link/synapse-create-views.png":::

1. 그런 다음 **pbids** 를 선택 하 여 Power BI 데이터 원본 파일을 다운로드 합니다. 다운로드 한 파일을 엽니다. 필요한 연결 정보를 포함 하 고 Power BI 대시보드를 엽니다.

   :::image type="content" source="./media/integrated-power-bi-synapse-link/download-powerbi-desktop-files.png" alt-text="pbids 형식으로 Power BI 데스크톱 파일을 다운로드 합니다." border="true" lightbox="./media/integrated-power-bi-synapse-link/download-powerbi-desktop-files.png":::

1. 이제 Synapse 링크를 사용 하 여 Power BI에서 Azure Cosmos DB 데이터에 연결할 수 있습니다. 각 컨테이너의 데이터에 해당 하는 T SQL 뷰 목록이 표시 됩니다.

   예를 들어, 다음 화면은 차량 제 데이터를 표시 합니다. 추가 분석을 위해이 데이터를 로드 하거나 로드 하기 전에 데이터를 변환할 수 있습니다.

   :::image type="content" source="./media/integrated-power-bi-synapse-link/powerbi-desktop-select-view.png" alt-text="각 컨테이너의 데이터에 해당 하는 T SQL 뷰입니다." border="true" lightbox="./media/integrated-power-bi-synapse-link/powerbi-desktop-select-view.png":::

1. 이제 Azure Cosmos DB의 분석 데이터를 사용 하 여 대시보드 작성을 시작할 수 있습니다. 데이터가 분석 저장소에 복제 되는 즉시 데이터 변경 내용이 대시보드에 반영 됩니다 .이는 일반적으로 몇 분 내에 발생 합니다.

## <a name="next-steps"></a>다음 단계

* [Azure Cosmos DB에 대 한 Azure Synapse 링크](synapse-link.md) 에 대 한 자세한 정보
* [서버를 사용 하지 않는 SQL 풀 커넥트 Power BI Desktop & 보고서 만들기](../synapse-analytics/sql/tutorial-connect-power-bi-desktop.md#prerequisites)
