---
title: Azure Cosmos DB 계정에서 Notebook 보기(미리 보기)
description: Azure Cosmos DB의 기본 제공 노트북을 통하여 포털 내에서 데이터를 분석하고 시각화할 수 있습니다. 본 문서에서는 Cosmos 계정에 대하여 해당 기능을 사용하도록 설정하는 방법을 설명합니다.
author: deborahc
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 09/24/2021
ms.author: dech
ms.custom: references_regions
ms.openlocfilehash: 19bb4a91a8612feab4218a16eedb2f961b1d0adb
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131434012"
---
# <a name="enable-notebooks-for-azure-cosmos-db-accounts-preview"></a>Azure Cosmos DB 계정에 대하여 Notebook을 사용하도록 설정하기(미리 보기)
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

> [!IMPORTANT]
> 현재 노트북 기능을 일시적으로 사용할 수 없습니다. Azure Cosmos DB 팀이 수정 작업을 수행 하 고 있습니다.  

Azure Cosmos DB의 기본 제공 Jupyter Notebook을 사용하여 Azure Portal에서 데이터를 분석하고 시각화할 수 있습니다. 이 문서에서는 Azure Cosmos DB 계정에 대해 이 기능을 사용하도록 설정하는 방법을 설명합니다. Azure Cosmos DB 용 노트북은 현재 29 개 [지역](#supported-regions)에서 사용할 수 있습니다. Notebook을 사용하려면 [새 Cosmos 계정을 생성](#create-a-new-cosmos-account)하거나 해당 지역 중 하나에서 [기존 계정에 Notebook을 사용하도록 설정](#enable-notebooks-in-an-existing-cosmos-account)하세요.

## <a name="create-a-new-cosmos-account"></a>새 Cosmos 계정 생성하기
2021년 2월 10일부터, [지원되는 지역](#supported-regions) 중 한 군데에서 만든 새로운 Azure Cosmos 계정에서 자동으로 Notebook을 사용할 수 있습니다. Notebook을 사용하도록 설정하는 데는 추가적인 구성이 필요 없습니다. 새 계정을 만들려면 다음 지침을 따릅니다.
1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.
1. **리소스 만들기** > **데이터베이스** > **Azure Cosmos DB** 를 선택합니다.
1. 계정의 기본 설정을 입력합니다.

   :::image type="content" source="./media/create-cosmosdb-resources-portal/azure-cosmos-db-create-new-account-detail-2.png" alt-text="Azure Cosmos DB에 대한 새 계정 페이지":::

1. **검토 + 만들기** 를 선택합니다. **네트워크** 및 **태그** 옵션은 건너뛸 수 있습니다. 
1. 계정 설정을 검토한 다음, **만들기** 를 선택합니다. 계정을 만드는 데 몇 분이 걸립니다. 포털 페이지에 **배포가 완료됨** 이 표시되기를 기다립니다.

   :::image type="content" source="media/enable-notebooks/create-new-account-with-notebooks-complete.png" alt-text="Azure Portal 알림 창":::

1. **리소스로 이동** 을 선택하여 Azure Cosmos DB 계정 페이지로 이동합니다.

   :::image type="content" source="../includes/media/cosmos-db-create-dbaccount/azure-cosmos-db-account-created-3.png" alt-text="Azure Cosmos DB 계정 페이지":::

1. **데이터 탐색기** 창으로 이동합니다. Notebook 작업 영역이 나타나야 합니다.

    :::image type="content" source="media/enable-notebooks/new-notebooks-workspace.png" alt-text="새 Azure Cosmos DB Notebook 작업 영역":::

## <a name="enable-notebooks-in-an-existing-cosmos-account"></a>기존의 Cosmos 계정에서 Notebook 사용하도록 설정하기

기존 계정에서 Notebook을 사용하도록 설정할 수도 있습니다. 계정마다 한 번씩만 해당 단계를 수행하면 됩니다.

1. Cosmos 계정의 **데이터 탐색기** 창으로 이동합니다.
1. **Notebook 사용** 을 선택합니다.

    :::image type="content" source="media/enable-notebooks/enable-notebooks-workspace.png" alt-text="데이터 탐색기에 새 Notebook 작업 영역 만들기":::

1. 새 Notebook 작업 영역을 만들라는 메시지가 표시될 것입니다. **설정 완료** 를 선택합니다.
1. 이제 내 계정에서 Notebook을 사용할 수 있습니다.

## <a name="create-and-run-your-first-notebook"></a>첫 번째로 Notebook 만들어 실행하기

Notebook을 사용할 수 있는지 확인하려면 샘플 Notebook에서 하나를 선택합니다. 이렇게 하면 Notebook 사본이 작업 영역에 저장되고 열립니다.

본 예제에서는 **GettingStarted.ipynb** 를 사용합니다.

:::image type="content" source="media/enable-notebooks/select-getting-started-notebook.png" alt-text="GettingStarted.ipynb Notebook 보기":::

Notebook을 실행하려면:
1. Python 코드가 들어 있는 첫 번째 코드 셀을 선택합니다.
1. **실행** 을 선택하여 해당 셀을 실행합니다. **Shift + Enter 키** 를 사용하여 해당 셀을 실행할 수도 있습니다.
1. 생성된 데이터베이스와 컨테이너를 확인하려면 리소스 창을 새로 고칩니다.

    :::image type="content" source="media/enable-notebooks/run-first-notebook-cell.png" alt-text="Notebook 시작 실행":::

**New Notebook** 을 선택하여 새 Notebook을 만들거나 **내 Notebook** 메뉴에서 **파일 업로드** 를 선택하여 기존의 Notebook(.ipynb)을 업로드할 수 있습니다. 

:::image type="content" source="media/enable-notebooks/create-or-upload-new-notebook.png" alt-text="새 Notebook 만들거나 업로드하기":::

## <a name="supported-regions"></a>지원되는 지역
Azure Cosmos DB용 기본 제공 Notebook은 현재 29 Azure 지역에서 사용할 수 있습니다. 이러한 지역에서 만든 새 Azure Cosmos 계정에서는 자동으로 Notebook을 사용하도록 설정합니다. Notebook은 계정에서 무료로 사용할 수 있습니다. 

- 오스트레일리아 중부
- 오스트레일리아 중부 2
- 오스트레일리아 동부
- 오스트레일리아 남동부
- 브라질 남부
- 캐나다 중부
- 캐나다 동부
- 인도 중부
- 미국 중부
- 미국 동부
- 미국 동부 2
- 프랑스 중부
- 프랑스 남부
- 독일 북부
- 독일 중서부
- 일본 서부
- 한국 남부
- 미국 중북부
- 북유럽
- 미국 중남부
- 동남아시아
- 스위스 북부
- 아랍에미리트 중부
- 영국 남부
- 영국 서부
- 미국 중서부
- 서유럽
- 인도 서부
- 미국 서부 2

## <a name="next-steps"></a>다음 단계

* [Azure Cosmos DB Jupyter Notebook](../cosmosdb-jupyter-notebooks.md)의 혜택 알아보기
* [Notebook 샘플 갤러리 살펴보기](https://cosmos.azure.com/gallery.html)
* [Azure Cosmos DB Notebook 갤러리에 Notebook 게시](publish-notebook-gallery.md)
* [Python Notebook 기능 및 명령 사용](use-python-notebook-features-and-commands.md)
* [C# Notebook 기능 및 명령 사용](use-csharp-notebook-features-and-commands.md)
* [GitHub 리포지토리에서 Notebooks 가져오기](import-github-notebooks.md)
