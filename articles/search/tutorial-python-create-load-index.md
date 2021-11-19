---
title: 'Python 자습서: 웹앱에 검색 추가'
titleSuffix: Azure Cognitive Search
description: PYPI 패키지 SDK azure-search-documents를 사용하여 Python으로 인덱스를 만들고 CSV 데이터를 검색 인덱스로 가져옵니다.
manager: nitinme
author: diberry
ms.author: diberry
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 11/17/2021
ms.custom: devx-track-python
ms.devlang: python
ms.openlocfilehash: 279e5f00b68d9b64e84b679942be5aadf395300f
ms.sourcegitcommit: 1244a72dbec39ac8cf16bb1799d8c46bde749d47
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/18/2021
ms.locfileid: "132755643"
---
# <a name="2---create-and-load-search-index-with-python"></a>2 - Python을 사용하여 검색 인덱스 만들기 및 로드

다음을 수행하여 검색 사용 웹 사이트를 계속 빌드합니다.
* VS Code 확장을 사용하여 검색 리소스 만들기
* 샘플 스크립트와 Azure SDK [Azure.Search.Documents](https://pypi.org/project/azure-search-documents/)를 사용하여 Python을 통해 새 인덱스 만들기 및 데이터 가져오기.

## <a name="create-an-azure-cognitive-search-resource"></a>Azure Cognitive Search 리소스 만들기 

Visual Studio Code용 [Azure Cognitive Search](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurecognitivesearch) 확장을 사용하여 새 검색 리소스를 만듭니다.

1. Visual Studio Code에서 [작업 막대](https://code.visualstudio.com/docs/getstarted/userinterface)를 열고, Azure 아이콘을 선택합니다. 

1. 사이드바의 `Azure: Cognitive Search` 영역 아래에서 **마우스 오른쪽 단추로 Azure 구독을 클릭** 하고, **새 검색 서비스 만들기** 를 선택합니다.

    :::image type="content" source="./media/tutorial-javascript-create-load-index/visual-studio-code-create-search-resource.png" alt-text="사이드바의 **Azure: Cognitive Search** 영역 아래에서 마우스 오른쪽 단추로 Azure 구독을 클릭하고, **새 검색 서비스 만들기**를 선택합니다.":::

1. 프롬프트에 따라 다음 정보를 제공합니다.

    |prompt|Enter 키|
    |--|--|
    |새 Search Service에 대해 전역적으로 고유한 이름 입력|**이 이름을 기억하세요.** . 이 리소스 이름은 리소스 엔드포인트의 일부가 됩니다.|
    |새 리소스에 대한 리소스 그룹 선택|이 자습서를 위해 만든 리소스 그룹을 사용합니다.|
    |Search Servic에 대한 SKU 선택|이 자습서에서는 **무료** 를 선택합니다. 서비스가 만들어지면 가격 책정 계층을 변경할 수 없습니다.|
    |새 리소스의 위치 선택|가까운 지역을 선택합니다.|

1. 프롬프트를 완료하면 새 검색 리소스가 만들어집니다. 

## <a name="get-your-search-resource-admin-key"></a>검색 리소스 관리자 키 가져오기

Visual Studio Code 확장을 사용하여 검색 리소스 관리자 키를 가져옵니다. 

1. Visual Studio Code의 사이드바에서 마우스 오른쪽 단추로 검색 리소스를 클릭하고, **관리자 키 복사** 를 선택합니다.

    :::image type="content" source="./media/tutorial-javascript-create-load-index/visual-studio-code-copy-admin-key.png" alt-text="사이드바에서 마우스 오른쪽 단추로 검색 리소스를 클릭하고, **관리자 키 복사**를 선택합니다.":::

1. 이 관리자 키를 유지합니다. [이후 섹션](#prepare-the-bulk-import-script-for-search)에서 개체를 만드는 데 사용해야 합니다. 

## <a name="prepare-the-bulk-import-script-for-search"></a>검색을 위해 대량 가져오기 스크립트 준비

이 스크립트는 Cognitive Search용 Azure SDK를 사용합니다.

* [PYPI 패키지 azure-search-documents](https://pypi.org/project/azure-search-documents/)
* [참조 설명서](/python/api/azure-search-documents)

1. Visual Studio Code에서 `search-website/bulk-upload` 하위 디렉터리의 `bulk_upload.py` 파일을 열고, Azure Search SDK를 사용하여 인증하기 위해 다음 변수를 사용자 고유의 값으로 바꿉니다.

    * YOUR-SEARCH-SERVICE-NAME
    * YOUR-SEARCH-SERVICE-ADMIN-API-KEY

    :::code language="python" source="~/azure-search-python-samples/search-website/bulk-upload/bulk-upload.py" highlight="20,21,69,83,135" :::

1. Visual Studio에서 프로젝트 디렉터리의 `search-website/bulk-upload` 하위 디렉터리에 대한 통합 터미널을 열고, 다음 명령을 실행하여 종속성을 설치합니다. 

    # <a name="macoslinux"></a>[macOS/Linux](#tab/linux-install)
    
    ```bash
    python3 -m pip install -r requirements.txt 
    ```
    
    # <a name="windows"></a>[Windows](#tab/windows-install)

    ```bash
    py -m pip install -r requirements.txt 
    ```

## <a name="run-the-bulk-import-script-for-search"></a>검색을 위해 대량 가져오기 스크립트 실행

1. Visual Studio에서 프로젝트 디렉터리의 `search-website/bulk-upload` 하위 디렉터리에 대한 통합 터미널을 계속 사용하고, 다음 bash 명령을 실행하여 `bulk_upload.py` 스크립트를 실행합니다.

    # <a name="macoslinux"></a>[macOS/Linux](#tab/linux-run)
    
    ```bash
    python3 bulk-upload.py
    ```
    
    # <a name="windows"></a>[Windows](#tab/windows-run)

    ```bash
    py bulk-upload.py
    ```


1. 코드가 실행되면서 진행률이 콘솔에 표시됩니다. 
1. 업로드가 완료되면 콘솔에 출력된 마지막 명령문은 "Done(완료)입니다. 터미널을 종료하려면 아무 키나 누르세요."

## <a name="review-the-new-search-index"></a>새 검색 인덱스 검토

업로드가 완료되면 검색 인덱스를 사용할 준비가 됩니다. 새 인덱스를 검토합니다.

1. Visual Studio Code에서 Azure Cognitive Search 확장을 열고, 검색 리소스를 선택합니다.  

    :::image type="content" source="media/tutorial-javascript-create-load-index/visual-studio-code-search-extension-view-resource.png" alt-text="Visual Studio Code에서 Azure Cognitive Search 확장을 열고, 검색 리소스를 엽니다.":::

1. 인덱스, 문서, `good-books`를 차례로 펼친 다음, 문서를 선택하여 모든 문서 관련 데이터를 표시합니다.
 
    :::image type="content" source="media/tutorial-javascript-create-load-index/visual-studio-code-search-extension-view-docs.png" lightbox="media/tutorial-javascript-create-load-index/visual-studio-code-search-extension-view-docs.png" alt-text="인덱스, 'good-books', 문서를 차례로 선택합니다.":::

## <a name="copy-your-search-resource-name"></a>검색 리소스 이름 복사

**검색 리소스 이름** 을 적어 둡니다. 이는 Azure 함수 앱을 검색 리소스에 연결하는 데 필요합니다. 

> [!CAUTION]
> Azure 함수에서 검색 관리자 키를 사용하려고 할 수 있지만, 이는 최소 권한 원칙을 준수하지 않습니다. Azure 함수는 최소 권한을 준수하기 위해 쿼리 키를 사용합니다. 

## <a name="next-steps"></a>다음 단계

[Static Web App 배포](tutorial-python-deploy-static-web-app.md)