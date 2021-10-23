---
title: 자습서 - Power BI Desktop과 Azure Analysis Services 연결 | Microsoft Docs
author: minewiskan
description: 이 자습서에서는 Azure Portal에서 Analysis Services 서버 이름을 가져온 다음, Power BI Desktop을 사용하여 서버에 연결하는 방법에 대해 알아봅니다.
ms.service: azure-analysis-services
ms.topic: tutorial
ms.date: 10/12/2021
ms.author: owend
ms.reviewer: owend
ms.openlocfilehash: dcee9b5cb2f2117c6ccec68ca9fff63ada8abe5b
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/14/2021
ms.locfileid: "130001811"
---
# <a name="tutorial-connect-with-power-bi-desktop"></a>자습서: Power BI Desktop을 사용하여 연결

이 자습서에서는 Power BI Desktop을 사용하여 서버의 adventureworks 샘플 모델 데이터베이스에 연결합니다. 여기서는 모델에 대한 일반적인 사용자 연결을 시뮬레이션하고 모델 데이터에서 기본 보고서를 만듭니다.

> [!div class="checklist"]
> * 포털에서 서버 이름 가져오기
> * Power BI Desktop을 사용하여 연결
> * 기본 보고서 만들기

## <a name="prerequisites"></a>사전 요구 사항

- 서버에 [adventureworks 샘플 모델 데이터베이스를 추가](../analysis-services-create-sample-model.md)합니다.
- adventureworks 샘플 모델 데이터베이스에 대한 [*읽기*](../analysis-services-server-admins.md) 권한이 있어야 합니다.
- [최신 Power BI Desktop을 설치합니다](https://powerbi.microsoft.com/desktop).

## <a name="sign-in-to-the-azure-portal"></a>Azure Portal에 로그인
이 자습서에서는 포털에 로그인하여 서버 이름만 가져옵니다. 일반적으로 사용자는 서버 관리자에서 서버 이름을 가져옵니다.

[포털](https://portal.azure.com/)에 로그인합니다.

## <a name="get-server-name"></a>서버 이름 가져오기
Power BI Desktop에서 서버에 연결하려면 먼저 서버 이름이 필요합니다. 포털에서 서버 이름을 가져올 수 있습니다.

**Azure Portal** > 서버 > **개요** > **서버 이름** 에서 서버 이름을 복사합니다.
   
   ![Azure에서 서버 이름 가져오기](./media/analysis-services-tutorial-pbid/aas-copy-server-name.png)

## <a name="connect-in-power-bi-desktop"></a>Power BI Desktop에서 연결

1. Power BI Desktop에서 **데이터 가져오기** > **Azure** > **Azure Analysis Services 데이터베이스** 를 클릭합니다.

   ![데이터 가져오기에서 연결](./media/analysis-services-tutorial-pbid/aas-pbid-connect-aasserver.png)

2. **서버** 에서 서버 이름을 붙여넣고, **데이터베이스** 에서 **adventureworks** 를 입력한 다음, **확인** 을 클릭합니다.

   ![서버 이름 및 모델 데이터베이스 지정](./media/analysis-services-tutorial-pbid/aas-pbid-connect-aas-servername.png)

3. 메시지가 표시되면 자격 증명을 입력합니다. 입력한 계정에는 적어도 adventureworks 샘플 모델 데이터베이스에 대한 읽기 권한이 있어야 합니다.

    adventureworks 모델은 Power BI Desktop에서 [보고서] 보기의 빈 보고서와 함께 열립니다. **필드** 목록에는 숨겨지지 않은 모델 개체가 모두 표시됩니다. 연결 상태는 오른쪽 아래 모서리에 표시됩니다.

4. **시각화** 에서 **묶은 가로 막대형 차트** 를 선택하고, **서식**(페인트 롤러 아이콘)을 클릭한 다음, **데이터 레이블** 을 켭니다. 

   ![시각화](./media/analysis-services-tutorial-pbid/aas-pbid-visualizations-report.png)

5. **필드** > **Internet Sales**(인터넷 판매) 테이블에서 **Internet Sales Total**(인터넷 판매 합계) 및 **Margin**(이익) 측정값을 선택합니다. **Product Category**(제품 범주) 테이블에서 **Product Category Name**(제품 범주 이름)을 선택합니다.

   ![보고서 완성](./media/analysis-services-tutorial-pbid/aas-pbid-complete-report.png)

    몇 분 정도 시간을 내어 여러 시각화를 만들고 데이터와 메트릭을 조각화하면서 adventureworks 샘플 모델을 살펴봅니다. 보고서에 만족하면 저장합니다.

## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요하지 않은 경우 보고서를 저장하지 않거나 이를 저장했으면 해당 파일을 삭제합니다.

## <a name="next-steps"></a>다음 단계
이 자습서에서는 Power BI Desktop을 사용하여 서버의 데이터 모델에 연결하고 기본 보고서를 만드는 방법을 알아보았습니다. 데이터 모델을 만드는 방법에 익숙하지 않은 경우 SQL Server Analysis Services 문서의 [Adventure Works Internet Sales 테이블 형식 데이터 모델링 자습서](/analysis-services/tutorial-tabular-1400/as-adventure-works-tutorial)를 참조하세요.