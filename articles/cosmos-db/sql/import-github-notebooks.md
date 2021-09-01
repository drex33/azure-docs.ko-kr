---
title: GitHub 리포지토리에서 Azure Cosmos DB로 Notebook 가져오기 및 실행
description: GitHub에 연결하고 GitHub 리포지토리에서 Azure Cosmos 계정으로 Notebook을 가져오는 방법에 대해 알아봅니다. 가져온 후에는 이를 실행하고, 편집하고, 변경 내용을 GitHub에 다시 저장할 수 있습니다.
author: deborahc
ms.author: dech
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 02/22/2021
ms.openlocfilehash: 5caed47f67e15160f7754bad0003e5675d197573
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2021
ms.locfileid: "123116955"
---
# <a name="import-notebooks-from-a-github-repo-into-azure-cosmos-db"></a>GitHub 리포지토리에서 Azure Cosmos DB로 Notebook 가져오기
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

Azure Cosmos 계정에 대해 [Notebook 지원을 활성화](enable-notebooks.md)한 후 새 Notebook을 만들거나, 로컬 컴퓨터에서 새 Notebook을 업로드하거나, GitHub 계정에서 기존 Notebook을 가져올 수 있습니다. 이 문서에서는 Notebook 작업 영역을 GitHub에 연결하고 GitHub 리포지토리에서 Azure Cosmos 계정으로 Notebook을 가져오는 방법을 보여줍니다. 가져온 후에는 이를 실행하고, 변경하고, 변경 내용을 GitHub에 다시 저장할 수 있습니다.

## <a name="get-notebooks-from-github"></a>GitHub에서 Notebook 가져오기

사용자 고유의 GitHub 리포지토리 또는 다른 공용 GitHub 리포지토리에 연결하여 Azure Cosmos DB에서 Notebook을 읽고, 작성하고, 공유할 수 있습니다. 다음 단계에 따라 GitHub 계정에 연결합니다.

1. [Azure Portal](https://portal.azure.com/)에 로그인하고 Azure Cosmos 계정으로 이동합니다.

1. **Data Explorer** 탭을 엽니다. 이 탭에는 기존의 모든 데이터베이스, 컨테이너 및 Notebook이 표시됩니다.

1. **GitHub에 연결** 메뉴 항목을 선택합니다.

1. **공용 리포지토리** 또는 **공용 및 프라이빗 리포지토리** 에 연결하도록 선택할 수 있는 탭이 열립니다.  필요한 옵션을 선택한 후 **액세스 권한 부여** 를 선택합니다. Azure Cosmos DB가 GitHub 계정의 리포지토리에 액세스하려면 권한 부여가 필요합니다.

   :::image type="content" source="./media/import-github-notebooks/authorize-access-github.png" alt-text="Azure Cosmos DB에 GitHub 리포지토리에 대한 액세스 권한 부여":::

1. 권한 부여를 확인할 수 있는 "github.com" 웹 페이지로 리디렉션됩니다. **AzureCosmosDBNotebooks 권한 부여** 단추를 선택하고 프롬프트에 GitHub 계정 암호를 입력합니다.

1. 권한 부여가 성공하면 Azure Cosmos 계정으로 다시 이동합니다. 그러면 GitHub 계정에서 모든 퍼블릭/프라이빗 리포지토리를 볼 수 있습니다. 사용 가능한 목록에서 리포지토리를 선택하거나 해당 URL을 사용하여 리포지토리를 직접 추가할 수 있습니다.

1. 필요한 리포지토리를 선택하면 리포지토리 항목이 **고정되지 않은 리포지토리** 섹션에서 **고정된 리포지토리** 섹션으로 이동합니다. 필요한 경우 해당 리포지토리의 특정 분기를 선택하여 Notebook을 가져올 수도 있습니다.

   :::image type="content" source="./media/import-github-notebooks/choose-repo-branch.png" alt-text="리포지토리 및 분기 선택":::

1. **확인** 을 선택하여 가져오기 작업을 완료합니다. 선택한 리포지토리 분기에서 사용할 수 있는 모든 Notebook을 Azure Cosmos 계정으로 가져옵니다.

GitHub 계정과 통합한 후에만 Azure Cosmos 계정에서 리포지토리 및 Notebook 목록을 볼 수 있습니다. 이 명령문은 여러 사용자가 Azure Cosmos DB 계정에 로그인하여 자신의 계정을 추가하는 경우에도 마찬가지입니다. 즉, 여러 사용자가 동일한 Azure Cosmos 계정을 사용하여 Notebook 작업 영역을 GitHub에 연결할 수 있습니다. 그러나 각 사용자는 가져온 리포지토리 및 Notebook 목록만 볼 수 있습니다. 다른 사람이 가져온 Notebook은 표시되지 않습니다.

Notebook 작업 영역에서 GitHub 계정의 연결을 끊으려면 **데이터 탐색기** 탭을 열고, **GitHub 리포지토리** 옆에 있는 `…`를 선택하고, **GitHub에서 연결 끊기** 를 선택합니다.

## <a name="edit-a-notebook-and-push-changes-to-github"></a>Notebook을 편집하고 GitHub에 변경 내용 푸시

기존 Notebook을 편집하거나 리포지토리에 새 Notebook을 추가하고 변경 내용을 GitHub에 다시 저장할 수 있습니다.

기존 Notebook을 편집한 후 **저장** 을 선택합니다. 변경 내용에 대한 커밋 메시지를 입력할 수 있는 대화 상자가 열립니다. **커밋** 을 선택하면 GitHub의 Notebook이 업데이트됩니다. GitHub 계정에 로그인하고 커밋 기록을 확인하여 업데이트의 유효성을 검사할 수 있습니다.

변경 내용을 커밋한 후 일반 GitHub 흐름에서 일반적으로 변경 내용을 원격으로 푸시합니다. 그러나 이 경우 커밋 옵션은 GitHub에 대한 업데이트를 "준비, 커밋 및 푸시"하는 용도로 사용됩니다.

:::image type="content" source="./media/import-github-notebooks/commit-changes-github.png" alt-text="Notebooks 편집 및 GitHub 변경 내용 커밋":::

Notebook을 편집한 후에는 [Notebook 갤러리에 게시할](publish-notebook-gallery.md)수 있습니다. 

## <a name="next-steps"></a>다음 단계

* [Azure Cosmos DB Jupyter Notebooks](../cosmosdb-jupyter-notebooks.md)의 이점에 대해 알아봅니다.
* [Notebook 샘플 갤러리 살펴보기](https://cosmos.azure.com/gallery.html)
* [Azure Cosmos DB Notebook 갤러리에 Notebook 게시](publish-notebook-gallery.md)
* [Python Notebook 기능 및 명령 사용](../use-python-notebook-features-and-commands.md)
* [C# Notebook 기능 및 명령 사용](../use-csharp-notebook-features-and-commands.md)
