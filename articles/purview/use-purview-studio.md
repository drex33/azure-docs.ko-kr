---
title: Azure Purview Studio 사용
description: 이 문서에서는 Azure Purview Studio를 사용하는 방법을 설명합니다.
author: nayenama
ms.author: nayenama
ms.service: purview
ms.topic: conceptual
ms.date: 09/27/2021
ms.openlocfilehash: 2e3bcd7e51226f437f42f03fa43b144bee434f33
ms.sourcegitcommit: 557ed4e74f0629b6d2a543e1228f65a3e01bf3ac
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/05/2021
ms.locfileid: "129456141"
---
# <a name="use-purview-studio"></a>Purview Studio 사용

이 문서에서는 Azure Purview의 일부 기본 기능을 간단히 설명합니다.

## <a name="prerequisites"></a>필수 구성 요소

* Azure Portal에서 Active 부서의 범위 계정이 이미 만들어지고 사용자에 게 [부서의 범위 Studio](https://web.purview.azure.com/resource/)에 액세스할 수 있는 권한이 있습니다.

## <a name="launch-purview-account"></a>Purview 계정 시작

* Purview 계정을 시작하려면 Azure Portal에서 Purview 계정으로 이동하고, 시작하려는 계정을 선택하고, 계정을 시작합니다.

  :::image type="content" source="./media/use-purview-studio/open-purview-studio.png" alt-text="부서의 범위 Studio 단추가 강조 표시 된 Azure Portal 부서의 범위 창의 스크린샷" border="true":::

* Purview 계정을 시작하는 또 다른 방법은 `https://web.purview.azure.com`으로 이동하고 **Azure Active Directory** 및 계정 이름을 선택하여 계정을 시작하는 것입니다.

## <a name="home-page"></a>홈 페이지

**홈** 은 Azure Purview 클라이언트의 시작 페이지입니다.

:::image type="content" source="./media/use-purview-studio/purview-homepage.png" alt-text="홈페이지의 스크린샷":::

다음 목록에서는 **홈페이지** 의 주요 기능을 요약합니다. 목록의 각 숫자는 이전 스크린샷에서 강조 표시된 숫자에 해당합니다.

1. 카탈로그의 식별 이름입니다. **관리**  >  **계정 정보** 에서 카탈로그 이름을 설정할 수 있습니다.

2. 카탈로그 분석에는 다음의 수가 표시됩니다.

   * 데이터 원본
   * 자산
   * 용어집 용어

3. 검색 상자를 사용하여 데이터 카탈로그에서 데이터 자산을 검색할 수 있습니다.

4. 빠른 실행 단추를 통해 애플리케이션의 자주 사용되는 기능에 액세스할 수 있습니다. 표시 되는 단추는 루트 컬렉션에서 사용자 계정에 할당 된 역할에 따라 달라 집니다.

   * *수집 관리자* 의 경우 사용 가능한 단추는 **기술 자료 센터** 입니다.
   * *데이터 큐레이터* 경우 단추는 **자산 찾아보기**, **용어 관리** 및 **지식 센터** 입니다.
   * *데이터 판독기* 의 경우 단추는 **자산 찾아보기**, **용어 보기** 및 **지식 센터** 입니다.
   * *데이터 원본 관리*  +  *데이터 큐레이터* 에 대 한 단추는 **자산 찾아보기**, **용어 관리** 및 **지식 센터** 입니다.
   * *데이터 원본 관리*  +  *데이터 판독기* 의 경우 단추는 **자산 찾아보기**, **용어 보기** 및 **지식 센터** 입니다.
  
   > [!NOTE]
   > Azure 부서의 범위 역할에 대 한 자세한 내용은 [Azure 부서의 범위의 Access control](catalog-permissions.md)을 참조 하세요.

5. 왼쪽 탐색 모음을 사용하여 애플리케이션의 기본 페이지를 찾을 수 있습니다.   
6. **최근에 액세스한 항목** 탭에는 최근에 액세스한 데이터 자산 목록이 표시됩니다. 자산 액세스에 관한 자세한 내용은 [Data Catalog 검색](how-to-search-catalog.md) 및 [자산 유형별 찾아보기](how-to-browse-catalog.md)를 참조하세요.  **내 항목** 탭은 로그온한 사용자가 소유한 데이터 자산 목록입니다.
7. **링크** 에는 지역 상태, 설명서, 가격 책정, 개요 및 부서의 범위 상태에 대 한 링크가 포함 되어 있습니다.
8. 위쪽 탐색 모음에는 릴리스 정보/업데이트, Purview 계정 변경, 알림, 도움말, 피드백 섹션에 관한 정보가 포함됩니다.

## <a name="knowledge-center"></a>지식 센터

지식 센터에서는 Purview와 관련된 모든 비디오 및 자습서를 찾을 수 있습니다.

## <a name="guided-tours"></a>둘러보기

Azure Purview Studio의 각 UX에는 페이지의 개요를 제공하는 둘러보기가 있습니다. 둘러보기를 시작하려면 위쪽 표시줄에서 **도움말** 을 선택하고 **둘러보기** 를 선택합니다.

:::image type="content" source="./media/use-purview-studio/guided-tour.png" alt-text="둘러보기의 스크린샷":::

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [보안 주체 추가](tutorial-scan-data.md)
