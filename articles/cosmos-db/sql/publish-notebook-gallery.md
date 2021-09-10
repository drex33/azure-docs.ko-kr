---
title: Azure Cosmos DB Notebook 갤러리에 Notebook 게시
description: 공용 갤러리에서 Notebook을 다운로드하고, 편집하고, 자신의 Notebook을 갤러리에 게시하는 방법에 대해 알아봅니다.
author: deborahc
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 03/02/2021
ms.author: dech
ms.openlocfilehash: 016bd8c73e12297988f3a2d47cde0163821d2dcd
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2021
ms.locfileid: "123116537"
---
# <a name="publish-notebooks-to-the-azure-cosmos-db-notebook-gallery"></a>Azure Cosmos DB Notebook 갤러리에 Notebook 게시
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

Azure Cosmos DB 기본 제공 Jupyter Notebook은 Azure Portal의 Azure Cosmos DB 계정에 직접 통합됩니다. 이러한 Notebook을 사용하여 Azure Portal에서 데이터를 분석하고 시각화할 수 있습니다. Azure Cosmos DB의 기본 제공 Notebook은 현재 [여러 지역](https://azure.microsoft.com/global-infrastructure/services/?products=cosmos-db&regions=all)에서 사용할 수 있습니다. Notebook을 사용하려면 [새 Cosmos 계정을 생성](create-cosmosdb-resources-portal.md)하거나 해당 지역 중 하나에서 [기존 계정에 Notebook을 사용하도록 설정](enable-notebooks.md)하세요.

Azure Portal의 Notebook 환경에는 Azure Cosmos DB 팀에서 게시한 몇 가지 샘플이 있습니다. 또한 사용자 고유의 Notebook을 게시하고 공유할 수 있는 공용 갤러리가 있습니다. 갤러리에 Notebook을 게시한 후에는 모든 Azure Cosmos DB 사용자가 보고 사용할 수 있습니다. 이 문서에서는 공용 갤러리에서 Notebook을 사용하고 갤러리에 Notebook을 게시하는 방법을 알아봅니다.

## <a name="download-a-notebook-from-the-gallery"></a>갤러리에서 Notebook 다운로드

다음 단계를 사용하여 갤러리에서 자신의 Notebook 작업 영역으로 Notebook을 보고 다운로드합니다.

1. [Azure Portal](https://portal.azure.com/)에 로그인하고 Notebook 환경에서 사용하도록 설정된 Azure Cosmos DB 계정으로 이동합니다.

1. **Data Explorer** > **Notebook** > **갤러리** > **공용 갤러리** 탭으로 이동합니다.

1. 갤러리에서 Notebook을 보거나 게시하기 전에 [사용 규정](https://azure.microsoft.com/support/legal/cosmos-db-public-gallery-code-of-conduct/)에 동의합니다. 사용 규정은 구독 수준 및 사용자에 따라 로그됩니다. 다른 구독으로 전환하는 경우 갤러리에 액세스하기 전에 다시 동의해야 합니다. 사용 규정에 동의하려면 확인란을 선택하고 **계속** 을 선택합니다.

   :::image type="content" source="./media/publish-notebook-gallery/view-public-gallery.png" alt-text="Notebook 공용 갤러리로 이동하여 사용 규정에 동의합니다.":::

1. 다음으로, 특정 Notebook을 즐겨찾기 탭에 추가하거나 다운로드할 수 있습니다. Notebook을 다운로드하면 실행하거나 편집할 수 있는 Notebook 작업 영역에 복사됩니다.

   :::image type="content" source="./media/publish-notebook-gallery/download-notebook-gallery.png" alt-text="갤러리에서 작업 영역으로 Notebook을 다운로드합니다.":::

## <a name="publish-a-notebook-to-the-gallery"></a>갤러리에 Notebook 게시

고유한 Notebook을 빌드하거나 다른 시나리오에 맞게 기존 Notebook을 편집하는 경우 갤러리에 게시하는 것이 좋습니다. 갤러리에 Notebook을 게시한 후에는 다른 사용자가 해당 Notebook에 액세스할 수 있습니다. [Notebook 샘플 갤러리를 탐색](https://cosmos.azure.com/gallery.html)하여 현재 사용할 수 있는 Notebook을 볼 수 있습니다.

다음 단계에 따라 Notebook을 게시합니다.

1. [Azure Portal](https://portal.azure.com/)에 로그인하고 Notebook 환경에서 사용하도록 설정된 Azure Cosmos DB 계정으로 이동합니다.

1. **Data Explorer** > **Notebook** > **내 Notebook** 탭으로 이동합니다.

1. 게시하려는 Notebook으로 이동합니다. 명령 모음에서 **저장** 단추를 선택하고 **갤러리에 게시** 를 선택합니다.

   :::image type="content" source="./media/publish-notebook-gallery/choose-notebook-publish-option-2.png" alt-text="갤러리에 게시할 Notebook을 선택합니다.":::

   Notebook 이름 옆에 있는 **...** 단추를 선택하여 **갤러리에 게시** 옵션을 찾을 수도 있습니다.

   :::image type="content" source="./media/publish-notebook-gallery/choose-notebook-publish.png" alt-text="갤러리에 게시할 Notebook을 선택하는 또 다른 방법입니다.":::

1. 다음 세부 정보를 사용하여 **갤러리에 게시** 양식을 채웁니다.

   * **이름:** Notebook을 식별하는 대화명입니다.
   * **설명:** Notebook의 용도에 대한 간단한 설명입니다.
   * **태그:** 태그는 선택 사항이며 키워드로 검색할 때 결과를 필터링하는 데 사용됩니다.
   * **표지 이미지:** Notebook을 게시할 때 표지에 사용되는 이미지입니다. 다음 옵션 중 하나를 선택할 수 있습니다.
   * **사용자 지정 이미지** - 컴퓨터에서 이미지를 업로드할 수 있습니다. 가로 세로 비율이 256x144인 이미지 파일을 선택합니다.
   * **URL** - 이미지가 있고 공개적으로 액세스할 수 있는 URL을 제공합니다.
   * **스크린샷 만들기** - 열려 있는 Notebook의 스크린샷이 자동으로 생성되어 미리 보기에 업로드됩니다.
   * **첫 번째 표시 출력 사용** - 표시 출력이 있는 첫 번째 셀의 출력입니다. Markdown/텍스트만 표시하는 셀은 표시 출력으로 계산되지 않습니다.

   :::image type="content" source="./media/publish-notebook-gallery/publish-notebook.png" alt-text="갤러리에 게시 양식을 채웁니다.":::

   > [!NOTE]
   > Notebook 이름 옆에 있는 **...** 단추에서 **갤러리에 게시** 옵션을 사용하는 경우 **모든 표지 이미지** 옵션이 표시되지 않습니다. Notebook이 열려 있지 않았을 수도 있고 Azure Cosmos DB에서 스크린샷을 만들거나 첫 번째 표시 출력에 액세스할 수 있는 권한이 없기 때문입니다.

1. 미리 보기가 양호한지 확인하고 **게시** 를 선택합니다. 이 Notebook이 게시되면 Azure Cosmos DB Notebook 공용 갤러리에 표시됩니다. 게시하기 전에 중요한 데이터 또는 출력을 제거했는지 확인합니다. 게시하기 전에 Notebook 콘텐츠에서 Microsoft 정책 위반 사항이 있는지 검색합니다. 이 프로세스는 일반적으로 완료하는 데 몇 초 정도 걸립니다. 위반이 발견되면 알림 탭에 메시지가 표시됩니다. 위반을 발견하면 Notebook이 게시되지 않으며 위반된 텍스트를 제거하고 다시 게시합니다.

   > [!NOTE]
   > Notebook이 공용 갤러리에 게시된 후에는 모든 Azure Cosmos DB 사용자가 볼 수 있습니다. 구독 또는 조직 수준에 따라 액세스가 제한되지 않습니다.

1. Notebook이 갤러리에 게시되면 **내 게시된 작업** 탭에서 볼 수 있습니다. 공용 갤러리에서 게시한 Notebook을 제거하거나 삭제할 수도 있습니다.

1. 또한 사용 규정을 위반하는 Notebook을 신고할 수도 있습니다. 위반이 발견되면 Cosmos DB가 갤러리에서 Notebook을 자동으로 제거합니다. Notebook이 제거되면 사용자가 **내 게시된 작업** 탭에서 제거된 노트와 함께 볼 수 있습니다. Notebook을 신고하려면 **Data Explorer** > **Notebook** > **갤러리** > **공용 갤러리** 탭으로 이동합니다. 신고하려는 Notebook을 열고 오른쪽 모서리에 있는 **도움말** 단추를 마우스로 가리킨 후 **신고하기** 를 선택합니다.

   :::image type="content" source="./media/publish-notebook-gallery/report-notebook-violation.png" alt-text="사용 규정을 위반하는 Notebook을 신고합니다.":::

## <a name="next-steps"></a>다음 단계

* [Azure Cosmos DB Jupyter Notebook](../cosmosdb-jupyter-notebooks.md)의 혜택 알아보기
* [Python Notebook 기능 및 명령 사용](use-python-notebook-features-and-commands.md)
* [C# Notebook 기능 및 명령 사용](use-csharp-notebook-features-and-commands.md)
* [GitHub 리포지토리에서 Notebooks 가져오기](import-github-notebooks.md)
