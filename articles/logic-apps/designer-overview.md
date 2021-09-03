---
title: 단일 테넌트 워크플로 디자이너 정보
description: 단일 테넌트 Azure Logic Apps의 디자이너가 Azure Portal을 통해 워크플로를 시각적으로 만드는 데 어떻게 도움이 되는지 알아봅니다. 이 최신 버전의 이점과 기능을 알아봅니다.
services: logic-apps
ms.suite: integration
ms.reviewer: logicappspm
ms.topic: conceptual
ms.date: 06/30/2021
ms.openlocfilehash: ad280e8c052b14ce64734012c3677f8fd2ce5096
ms.sourcegitcommit: 695a33a2123429289ac316028265711a79542b1c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/01/2021
ms.locfileid: "113129402"
---
# <a name="about-the-workflow-designer-in-single-tenant-azure-logic-apps"></a>단일 테넌트 Azure Logic Apps의 워크플로 디자이너 정보

Azure Portal에서 Azure Logic Apps로 작업할 때 시각적 또는 프로그래밍 방식으로 [*워크플로*](logic-apps-overview.md#workflow)를 편집할 수 있습니다. 포털에서 [*논리 앱* 리소스](logic-apps-overview.md#logic-app)를 연 후 **개발자** 아래의 리소스 메뉴에서 [**코드** 보기](#code-view)와 **디자이너** 보기 중에서 선택할 수 있습니다. 워크플로를 시각적으로 개발, 편집 및 실행하려면 디자이너 보기를 선택합니다. 언제든지 디자이너 보기와 코드 보기 간에 전환할 수 있습니다.

> [!IMPORTANT]
> 현재 최신 버전의 디자이너는 *단일 테넌트* Azure Logic Apps 환경에서 실행되는 *표준* 논리 앱 리소스에만 사용할 수 있습니다. 논리 앱의 다양한 리소스 유형 및 런타임 환경에 대한 자세한 내용은 [Azure Logic Apps에 대한 단일 테넌트 및 다중 테넌트 및 통합 서비스 환경](single-tenant-overview-compare.md)을 참조하세요.

:::image type="content" source="./media/designer-overview/choose-developer-view.png" alt-text="코드 또는 디자이너 보기에서 워크플로를 보기 위한 사이드바 옵션을 보여 주는 Azure Portal의 논리 앱 리소스 페이지 스크린샷.":::

**디자이너** 보기를 선택하면 워크플로 디자이너에서 워크플로가 열립니다.

:::image type="content" source="./media/logic-apps-overview/example-enterprise-workflow.png" alt-text="워크플로 디자이너 및 스위치와 조건을 사용하는 샘플 엔터프라이즈 워크플로를 보여 주는 스크린샷" lightbox="./media/logic-apps-overview/example-enterprise-workflow.png":::

## <a name="prerequisites"></a>사전 요구 사항

- Azure 계정 및 구독 구독이 없는 경우 [Azure 체험 계정에 등록](https://azure.microsoft.com/free/)합니다.
- [단일 테넌트 환경](single-tenant-overview-compare.md)의 *표준* 논리 앱 리소스. 자세한 내용은 [Azure Portal에서 단일 테넌트 Azure Logic Apps(표준)로 통합 워크플로 만들기](create-single-tenant-workflows-azure-portal.md)를 참조하세요.
- 단일 테넌트 논리 앱에 대한 워크플로

## <a name="latest-version-features"></a>최신 버전 기능

최신 워크플로 디자이너는 다음과 같은 주목할 만한 기능과 이점으로 새로운 경험을 제공합니다.

- 더 복잡한 워크플로를 지원하는 새로운 레이아웃 엔진 
- 새로운 레이아웃 엔진, 더욱 컴팩트해진 캔버스, 카드 기반 레이아웃 업데이트 덕분에 복잡한 워크플로를 깔끔하고 쉽게 만들고 확인할 수 있습니다.
- 워크플로 레이아웃과 별도의 패널을 사용하여 단계를 추가하고 편집합니다. 이 변경은 워크플로 레이아웃을 볼 수 있는 더 깨끗하고 명확한 캔버스를 제공합니다. 자세한 내용은 [워크플로에 단계 추가](#add-steps-to-workflows)를 참조하세요.
- 키보드 탐색을 사용하여 디자이너에서 워크플로의 단계 사이를 이동합니다.
  - 다음 카드로 이동: **Ctrl** + **아래쪽 화살표(&darr;)**
  - 이전 카드로 이동: **Ctrl** + **위쪽 화살표(&uarr;)**

## <a name="add-steps-to-workflows"></a>워크플로에 단계 추가

워크플로 디자이너는 워크플로에서 단계를 추가, 편집 및 삭제할 수 있는 시각적 방법을 제공합니다. 워크플로의 첫 번째 단계로 항상 [*트리거*](logic-apps-overview.md#trigger)를 추가하세요. 그런 다음 하나 이상의 [*작업*](logic-apps-overview.md#action)을 추가하여 워크플로를 완료합니다.

트리거 또는 작업을 워크플로에 추가하려면 다음 단계를 따르세요.

1. 디자이너에서 워크플로를 엽니다.
1. 디자이너에서 **작업 선택** 을 선택하면 **트리거 추가** 또는 **작업 추가** 라는 창이 열립니다. 
1. 열린 창에서 다음과 같은 방법으로 목록을 필터링하여 작업을 찾습니다.
    1. 검색 창에 서비스, 커넥터 또는 범주를 입력하여 관련 작업을 표시합니다. 예를 들어 `Azure Cosmos DB` 또는 `Data Operations`입니다. 
    1. 사용하려는 특정 작업을 알고 있는 경우 검색 창에 이름을 입력합니다. 예를 들어 `Call an Azure function` 또는 `When an HTTP request is received`입니다.
    1. **기본 제공** 탭을 선택하면 [*기본 제공 작업*](logic-apps-overview.md#built-in-operations) 범주만 표시됩니다. 또는 **Azure** 탭을 선택하여 Azure를 통해 사용할 수 있는 다른 작업 범주를 표시합니다.
    1. **트리거** 또는 **작업** 탭을 선택하면 트리거 또는 작업만 볼 수 있습니다. 그러나 첫 번째 단계로 트리거를 추가하고 다음 단계로 작업만 추가할 수 있습니다. 작업 범주에 따라 트리거 또는 작업만 사용할 수 있습니다.
    :::image type="content" source="./media/designer-overview/designer-add-operation.png" alt-text="새 작업을 추가하기 위해 편집 중인 워크플로를 보여 주는 Azure Portal의 논리 앱 디자이너 스크린샷." lightbox="./media/designer-overview/designer-add-operation.png":::
1. 사용할 작업을 선택합니다. 
    :::image type="content" source="./media/designer-overview/designer-filter-operations.png" alt-text="서비스 또는 이름으로 필터링할 수 있는 가능한 작업 창을 보여 주는 논리 앱 디자이너의 스크린샷." lightbox="./media/designer-overview/designer-filter-operations.png":::
1. 필요에 따라 트리거 또는 작업을 구성합니다.
    1. 필수 필드는 이름 앞에 빨간색 별표(&ast;)가 있습니다.
    1. 일부 트리거 및 작업에서는 다른 서비스에 대한 연결을 만들어야 할 수 있습니다. 계정에 로그인하거나 서비스에 대한 자격 증명을 입력해야 할 수 있습니다. 예를 들어 Office 365 Outlook 커넥터를 사용하여 이메일을 보내려면 Outlook 이메일 계정을 인증해야 합니다.
    1. 일부 트리거 및 작업은 정보나 식을 입력하는 대신 변수를 선택할 수 있는 동적 콘텐츠를 사용합니다.
1. 도구 모음에서 **저장** 을 선택하여 변경 사항을 저장합니다. 또한 이 단계에서는 워크플로가 유효한지 확인합니다. 

## <a name="code-view"></a>코드 보기

**코드** 보기를 사용하면 JSON 형식의 워크플로 정의 파일을 직접 수정할 수 있습니다. 이 보기에서 변경한 사항을 저장하려면 **저장** 을 선택해야 합니다. 

> [!TIP]
> **코드** 보기는 Azure 명령줄 인터페이스(Azure CLI) 또는 다른 방법을 사용하는 대신 워크플로 정의를 찾고 복사하는 쉬운 방법이기도 합니다.

:::image type="content" source="./media/designer-overview/code-view.png" alt-text="Azure Portal에서 편집 중인 JSON 워크플로 정의를 보여 주는 코드 보기의 논리 앱 워크플로 스크린샷.":::


## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Azure Portal에서 단일 테넌트 Azure Logic Apps(표준)로 통합 워크플로 만들기](create-single-tenant-workflows-azure-portal.md)
