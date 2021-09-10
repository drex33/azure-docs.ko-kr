---
title: Power BI를 사용하여 Azure Analysis Services에 연결 | Microsoft Docs
description: Power BI를 사용하여 Azure Analysis Services 서버에 연결하는 방법에 대해 알아봅니다. 연결되면 사용자가 모델 데이터를 탐색할 수 있습니다.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 06/30/2021
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: a956d5e3be0a5f92e22dcd1536339ff5244e19dc
ms.sourcegitcommit: 6bd31ec35ac44d79debfe98a3ef32fb3522e3934
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2021
ms.locfileid: "113213358"
---
# <a name="connect-with-power-bi"></a>Power BI로 연결

Azure에서 서버를 만들고 테이블 형식 모델을 배포하면 조직의 사용자는 데이터를 연결하고 탐색할 준비가 되었습니다. 

> [!NOTE]
> Power BI Desktop 모델을 Power BI 서비스에 게시하는 경우 Azure Analysis Services 서버에서 대소문자 구분 데이터 정렬 서버 속성이 선택되어 있지 않은지 확인합니다(기본값). 대소문자 구분 서버 속성은 SQL Server Management Studio를 사용하여 설정할 수 있습니다.
> 
> 
  
## <a name="connect-in-power-bi-desktop"></a>Power BI Desktop에서 연결

1. Power BI Desktop에서 **데이터 가져오기** > **Azure** > **Azure Analysis Services 데이터베이스** 를 클릭합니다.

2. **서버** 에 서버 이름을 입력합니다. 전체 URL을 포함하려면 예를 들어 asazure://westcentralus.asazure.windows.net/advworks 같이 입력합니다.

3. **데이터베이스** 에서 연결하려는 테이블 형식 모델 데이터베이스 및 큐브 뷰의 이름을 알고 있는 경우 여기에 붙여 넣습니다. 그러지 않으면 이 필드를 비워 두고 나중에 데이터베이스 또는 큐브 뷰를 선택합니다.

4. 연결 옵션을 선택하고 **연결** 을 누릅니다. 

    **라이브 연결** 및 **가져오기** 옵션 둘 다 지원됩니다. 그러나 가져오기 모드는 몇 가지 제한이 있으며, 특히 가져오는 동안 서버 성능이 영향을 받을 수 있으므로 라이브 연결을 권장합니다.
    
    [혼합 저장소 모드](/power-bi/transform-model/desktop-composite-models)인 Power BI 모델이 있는 경우 **라이브 연결** 옵션이 **[DirectQuery](/power-bi/connect-data/desktop-directquery-datasets-azure-analysis-services)** 옵션으로 바뀝니다. 모델이 가져오기에서 혼합 저장소 모드로 전환되면 라이브 연결도 DirectQuery로 자동 업그레이드됩니다.

5. 자격 증명을 입력하라는 메시지가 표시되면 **Microsoft 계정** 을 선택한 다음, **로그인** 을 클릭합니다. 

    :::image type="content" source="media/analysis-services-connect-pbi/aas-sign-in.png" alt-text="Azure AS에 로그인":::

   > [!NOTE]
   > Windows 및 기본 인증은 지원되지 않습니다. 

6. **탐색기** 에서 서버를 확장한 다음 연결하려는 모델 또는 큐브 뷰를 선택한 다음 **연결** 을 클릭합니다. 모델 또는 큐브 뷰를 클릭하여 해당 보기에 대한 모든 개체를 표시합니다.

    모델은 보고서 보기의 빈 보고서와 함께 Power BI Desktop에서 열립니다. 필드 목록에는 숨겨지지 않은 모든 모델 개체가 표시됩니다. 연결 상태는 오른쪽 아래 모서리에 표시됩니다.

## <a name="connect-in-power-bi-service"></a>Power BI(서비스)에서 연결

1. 서버의 모델에 대한 라이브 연결을 포함하는 Power BI Desktop 파일을 만듭니다.
2. [Power BI](https://powerbi.microsoft.com)에서 **데이터 가져오기** > **파일** 을 클릭한 다음, .pbix 파일을 찾아서 선택합니다.

## <a name="request-memory-limit"></a>메모리 제한 요청

시스템의 성능을 보호하기 위해 Azure Analysis Services 서버에 구성된 [쿼리 메모리 제한](/analysis-services/server-properties/memory-properties?view=azure-analysis-services-current&preserve-view=true)에 관계없이 Azure Analysis Services에 대해 Power BI 보고서에서 실행한 모든 쿼리에 메모리 제한이 적용됩니다. 쿼리가 너무 많은 메모리를 사용하는 경우 사용자는 쿼리나 해당 계산을 간소화하는 것을 고려해야 합니다.

|쿼리 유형| 메모리 제한 요청 |
|-----------------------------------------------------------|----------------------|
| Power BI에서 라이브 연결                            | 10 GB  |
| 공유 작업 영역의 Power BI 보고서에서 DirectQuery  | 1GB   |
| 프리미엄 작업 영역의 Power BI 보고서에서 DirectQuery | 10 GB  |
| Power BI 질문 및 답변 | 100MB |

## <a name="see-also"></a>참조
[Azure Analysis Services에 연결](analysis-services-connect.md)   
[클라이언트 라이브러리](/analysis-services/client-libraries?view=azure-analysis-services-current&preserve-view=true)
