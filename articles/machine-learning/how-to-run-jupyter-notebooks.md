---
title: 작업 영역에서 Jupyter Notebook 실행
titleSuffix: Azure Machine Learning
description: Azure Machine Learning 스튜디오에서 작업 영역을 벗어나지 않고 Jupyter Notebook을 실행하는 방법을 알아봅니다.
services: machine-learning
author: abeomor
ms.author: osomorog
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.date: 10/21/2021
ms.openlocfilehash: f6ac69d8bd3049efc55844ddd562e18a2950ef2b
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131560875"
---
# <a name="run-jupyter-notebooks-in-your-workspace"></a>작업 영역에서 Jupyter Notebook 실행

Azure Machine Learning 스튜디오의 작업 영역에서 Jupyter Notebook을 직접 실행하는 방법을 알아봅니다. [Jupyter](https://jupyter.org/) 또는 [JupyterLab](https://jupyterlab.readthedocs.io)을 시작할 수 있으며 작업 영역을 벗어나지 않고 Notebook을 편집 및 실행할 수도 있습니다.

Notebook을 포함하여 파일을 만들고 관리하는 방법에 대한 자세한 내용은 [작업 영역에서 파일 만들기 및 관리](how-to-manage-files.md)를 참조하세요.

> [!IMPORTANT]
> 표시된 기능(미리 보기)은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

* Azure 구독 Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/)을 만듭니다.
* Machine Learning 작업 영역. [Azure Machine Learning 작업 영역 만들기](how-to-manage-workspace.md)를 참조하세요.

## <a name="edit-a-notebook"></a>Notebook 편집

Notebook을 편집하려면 작업 영역의 **사용자 파일** 섹션에 있는 모든 Notebook을 엽니다. 편집하려는 셀을 클릭합니다.  이 섹션에 Notebook이 없으면 [작업 영역에서 파일 만들기 및 관리](how-to-manage-files.md)를 참조하세요.

컴퓨팅 인스턴스에 연결하지 않고 Notebook을 편집할 수 있습니다.  Notebook에서 셀을 실행하려면 컴퓨팅 인스턴스를 선택하거나 만듭니다.  중지된 컴퓨팅 인스턴스를 선택하면 첫 번째 셀을 선택할 때 자동으로 시작됩니다.

또한 컴퓨팅 인스턴스가 실행 중일 때 모든 Python Notebook에서[Intellisense](https://code.visualstudio.com/docs/editor/intellisense) 기반 코드 완성 기능을 사용할 수 있습니다.

Notebook 도구 모음에서 Jupyter 또는 JupyterLab을 시작할 수도 있습니다.  Azure Machine Learning은 Microsoft 지원이 안 되는 오픈 소스 제품이므로 Jupyter 또는 JupyterLab에서 업데이트를 제공하거나 버그를 수정하지 않습니다.

## <a name="focus-mode"></a>포커스 모드

활성 탭에 집중할 수 있도록 포커스 모드를 사용하여 현재 보기를 확장합니다. 포커스 모드는 Notebook 파일 탐색기를 숨깁니다.

1. 터미널 창 도구 모음에서 **포커스 모드** 를 선택하여 포커스 모드를 설정합니다. 창 너비에 따라 도구가 도구 모음에서 **...** 메뉴 항목 아래에 표시될 수 있습니다.
1. 포커스 모드에 있는 동안 **표준 보기** 를 선택하여 표준 보기로 돌아갑니다.

    :::image type="content" source="media/how-to-run-jupyter-notebooks/focusmode.gif" alt-text="포커스 모드/표준 보기 토글":::

## <a name="code-completion-intellisense"></a>코드 완성(IntelliSense)

[IntelliSense](https://code.visualstudio.com/docs/editor/intellisense)는 멤버 목록, 매개 변수 정보, 요약 정보 및 전체 단어와 같은 여러 기능을 포함하는 코드 완성 지원 도구입니다. 몇 번의 키 입력만으로 다음을 수행할 수 있습니다.
* 사용 중인 코드 정보 확인
* 입력하는 매개 변수 추적
* 속성 및 메서드 호출 추가 

### <a name="insert-code-snippets-preview"></a>코드 조각 삽입(미리 보기)

**Ctrl+스페이스바** 를 사용하여 IntelliSense 코드 조각을 트리거합니다.  추천 항목을 스크롤하거나 입력을 시작하여 삽입하려는 코드를 찾습니다.  코드를 삽입한 후 Tab 키를 눌러 인수를 선택하고 고유 용도에 맞게 코드를 사용자 지정합니다.

:::image type="content" source="media/how-to-run-jupyter-notebooks/insert-snippet.gif" alt-text="코드 조각 삽입":::

VS Code에서 Notebook을 열 때 이러한 동일한 코드 조각을 사용할 수 있습니다. 사용 가능한 코드 조각의 전체 목록은 [Azure Machine Learning VS Code 코드 조각](https://github.com/Azure/azureml-snippets/blob/main/snippets/snippets.md)을 참조하세요.

코드 조각 패널을 열기 위해 Notebook 도구 모음을 사용하여 코드 조각 목록을 찾아보고 검색할 수 있습니다.

:::image type="content" source="media/how-to-run-jupyter-notebooks/open-snippet-panel.png" alt-text="Notebook 도구 모음에서 코드 조각 패널 도구 열기":::

코드 조각 패널에서 새 코드 조각을 추가하도록 요청을 제출할 수도 있습니다.

:::image type="content" source="media/how-to-run-jupyter-notebooks/propose-new-snippet.png" alt-text="코드 조각 패널에서 새 코드 조각 제안 가능":::

## <a name="collaborate-with-notebook-comments-preview"></a>Notebook 주석을 사용하여 공동 작업(미리 보기)

Notebook 주석을 사용하여 Notebook에 액세스할 수 있는 다른 사용자와 공동 작업합니다.

Notebook 맨 위에 있는 **Notebook 주석** 도구를 사용하여 주석 창을 설정하고 해제합니다.  화면이 충분히 넓지 않으면 먼저 도구 세트 끝에 있는 **...** 를 선택하여 이 도구를 찾습니다.

:::image type="content" source="media/how-to-run-jupyter-notebooks/notebook-comments-tool.png" alt-text="위쪽 도구 모음에 있는 Notebook 주석 도구의 스크린샷":::  

주석 창이 표시되는지 여부와 관계없이 코드 셀에 주석을 추가할 수 있습니다.

1. 코드 셀에서 일부 텍스트를 선택합니다.  코드 셀의 텍스트만 주석 처리할 수 있습니다.
1. **새 주석 스레드** 도구를 사용하여 주석을 만듭니다.
    :::image type="content" source="media/how-to-run-jupyter-notebooks/comment-from-code.png" alt-text="코드 셀에 주석 추가 도구의 스크린샷":::
1. 이전에 숨겨져 있던 주석 창이 이제 열립니다.  
1. 주석을 입력하고 도구를 사용하여 게시하거나 **Ctrl+Enter** 를 사용합니다.
1. 주석이 게시되면 오른쪽 위에서 **...** 를 선택하여 다음을 수행합니다.
    * 주석 편집
    * 스레드 확인
    * 스레드 삭제

주석 처리된 텍스트는 코드에 자주색으로 강조 표시되어 나타납니다. 주석 창에서 주석을 선택하면 Notebook이 강조 표시된 텍스트가 포함된 셀로 스크롤됩니다.

> [!NOTE]
> 주석은 코드 셀의 메타데이터에 저장됩니다.

## <a name="clean-your-notebook-preview"></a>Notebook 정리(미리 보기)

Notebook을 만들기가 완료되면 일반적으로 데이터 탐색 또는 디버깅에 사용한 셀이 남게 됩니다. *수집* 기능은 이러한 추가 셀 없이 깨끗한 Notebook을 만드는 데 도움이 됩니다.

1. 모든 Notebook 셀을 실행합니다.
1. 새 Notebook으로 실행할 코드가 포함된 셀을 선택합니다. 예를 들어 실험을 제출하는 코드 또는 모델을 등록하는 코드를 사용할 수 있습니다.
1. 셀 도구 모음에 표시되는 **수집** 아이콘을 선택합니다.
    :::image type="content" source="media/how-to-run-jupyter-notebooks/gather.png" alt-text="스크린샷: 수집 아이콘 선택":::
1. “수집된” 새 Notebook의 이름을 입력합니다.  

새 Notebook에는 코드 셀만 포함됩니다. 여기에는 수집하도록 선택한 셀과 동일한 결과를 생성하기 위해 필요한 모든 셀이 포함됩니다.

## <a name="save-and-checkpoint-a-notebook"></a>Notebook 저장 및 검사점

Azure Machine Learning은 사용자가 *ipynb* 파일을 만들 때 검사점 파일을 만듭니다.

Notebook 도구 모음에서 메뉴를 선택한 후 **파일&gt;저장 및 검사점** 을 선택하여 Notebook을 수동으로 저장합니다. 그러면 Notebook과 연관된 검사점 파일이 추가됩니다.

:::image type="content" source="media/how-to-run-jupyter-notebooks/file-save.png" alt-text="Notebook 도구 모음의 저장 도구 스크린샷":::

모든 Notebook이 30초마다 자동 저장됩니다. 자동 저장은 검사점 파일이 아니라 초기 *ipynb* 파일만 업데이트합니다.
 
Notebook 메뉴에서 **검사점** 을 선택하여 이름이 지정된 검사점을 만들고 Notebook을 저장된 검사점으로 되돌립니다.

## <a name="export-a-notebook"></a>Notebook 내보내기

Notebook 도구 모음에서 메뉴를 선택한 후 **다음으로 내보내기** 를 선택하여 Notebook을 지원되는 유형으로 내보냅니다.

* Notebook
* Python
* HTML
* LaTeX

:::image type="content" source="media/how-to-run-jupyter-notebooks/export-notebook.png" alt-text="컴퓨터에 Notebook 내보내기":::

내보낸 파일이 컴퓨터에 저장됩니다.

## <a name="run-a-notebook-or-python-script"></a>Notebook 또는 Python 스크립트 실행

Notebook 또는 Python 스크립트를 실행하려면 먼저 실행 중인 [컴퓨팅 인스턴스](concept-compute-instance.md)에 연결합니다.

* 컴퓨팅 인스턴스가 없는 경우 다음 단계를 사용하여 만듭니다.

    1. Notebook 또는 스크립트 도구 모음의 컴퓨팅 드롭다운 오른쪽에서 **+ 새 컴퓨팅** 을 선택합니다. 화면 크기에 따라 **...** 메뉴 아래에 있을 수 있습니다.
        :::image type="content" source="media/how-to-run-jupyter-notebooks/new-compute.png" alt-text="새 컴퓨팅 만들기":::
    1. 컴퓨팅 이름을 지정하고 **Virtual Machine 크기** 를 선택합니다. 
    1. **만들기** 를 선택합니다.
    1. 컴퓨팅 인스턴스가 파일에 자동으로 연결됩니다.  이제 컴퓨팅 인스턴스 왼쪽의 도구를 사용하여 Notebook 셀 또는 Python 스크립트를 실행할 수 있습니다.

* 중지된 컴퓨팅 인스턴스가 있으면 컴퓨팅 드롭다운 오른쪽에서 **컴퓨팅 시작** 을 선택합니다. 화면 크기에 따라 **...** 메뉴 아래에 있을 수 있습니다.

    :::image type="content" source="media/how-to-run-jupyter-notebooks/start-compute.png" alt-text="컴퓨팅 인스턴스 시작":::
    
컴퓨팅 인스턴스에 연결되면 도구 모음을 사용하여 Notebook의 모든 셀을 실행하거나 Control + Enter를 사용하여 선택한 단일 셀을 실행합니다. 

사용자가 만든 컴퓨팅 인스턴스만 보고 사용할 수 있습니다.  **사용자 파일** 은 VM과 별도로 저장되고 작업 영역의 모든 컴퓨팅 인스턴스 간에 공유됩니다.

### <a name="view-logs-and-output"></a>로그 및 출력 보기

[Notebook 위젯](/python/api/azureml-widgets/azureml.widgets)을 사용하여 실행 및 로그의 진행률을 볼 수 있습니다. 위젯은 비동기적이며 학습을 마칠 때까지 업데이트를 제공합니다. Azure Machine Learning 위젯은 Jupyter 및 JupyterLab에서도 지원됩니다.

:::image type="content" source="media/how-to-run-jupyter-notebooks/jupyter-widget.png" alt-text="스크린샷: Jupyter Notebook 위젯":::

## <a name="explore-variables-in-the-notebook"></a>Notebook 변수 살펴보기

Notebook 도구 모음에서 **변수 탐색기** 도구를 사용하여 Notebook에 생성된 모든 변수의 이름, 유형, 길이 및 샘플 값을 표시합니다.

:::image type="content" source="media/how-to-run-jupyter-notebooks/variable-explorer.png" alt-text="스크린샷: 변수 탐색기 도구":::

도구를 선택하여 변수 탐색기 창을 표시합니다.

:::image type="content" source="media/how-to-run-jupyter-notebooks/variable-explorer-window.png" alt-text="스크린샷: 변수 탐색기 창":::

## <a name="navigate-with-a-toc"></a>TOC로 탐색

Notebook 도구 모음에서 **목차** 도구를 사용하여 목차를 표시하거나 숨깁니다.  제목으로 Markdown 셀을 시작하여 목차에 추가합니다. 테이블의 항목을 클릭하여 Notebook의 해당 셀로 스크롤합니다.  

:::image type="content" source="media/how-to-run-jupyter-notebooks/table-of-contents.png" alt-text="스크린샷: Notebook의 목차":::

## <a name="change-the-notebook-environment"></a>Notebook 환경 변경

Notebook 도구 모음을 사용하면 Notebook이 실행되는 환경을 변경할 수 있습니다.  

다음 작업은 Notebook 상태 또는 Notebook에 있는 변수 값을 변경하지 않습니다.

|작업  |결과  |
|---------|---------| --------|
|커널 중지     |  실행 중인 셀을 모두 중지합니다. 셀을 실행하면 커널이 자동으로 다시 시작됩니다. |
|다른 작업 영역 섹션으로 이동     |     실행 중인 셀이 중지됩니다. |

다음 작업은 Notebook 상태를 다시 설정하고 Notebook의 모든 변수를 다시 설정합니다.

|작업  |결과  |
|---------|---------| --------|
| 커널 변경 | Notebook에서 새 커널 사용 |
| 컴퓨팅 전환    |     Notebook은 새 컴퓨팅을 자동으로 사용합니다. |
| 컴퓨팅 다시 설정 | 셀을 실행하려고 할 때 다시 시작합니다. |
| 컴퓨팅 중지     |    실행할 셀 없음  |
| Jupyter 또는 JupyterLab에서 Notebook 열기     |    새 탭에서 Notebook을 열었습니다.  |

## <a name="add-new-kernels"></a>새 커널 추가

[터미널을 사용](how-to-access-terminal.md#add-new-kernels)하여 새 커널을 만들고 컴퓨팅 인스턴스에 추가합니다. Notebook은 연결된 컴퓨팅 인스턴스에 설치된 모든 Jupyter 커널을 자동으로 찾습니다.

오른쪽의 커널 드롭다운을 사용하여 설치된 커널로 변경합니다.  


### <a name="status-indicators"></a>상태 표시기

**컴퓨팅** 드롭다운 옆의 표시기에 해당 상태가 표시됩니다.  상태는 드롭다운 자체에도 표시됩니다.  

|색 |컴퓨팅 상태 |
|---------|---------| 
| 녹색 | 컴퓨팅 실행 중 |
| 빨강 |컴퓨팅 실패 | 
| 검정 | 컴퓨팅 중지됨 |
|  연한 파란색 |컴퓨팅 만들기, 시작, 다시 시작, 설정 |
|  회색 |컴퓨팅 삭제, 중지 |

**커널** 드롭다운 옆의 표시기에 해당 상태가 표시됩니다.

|색 |커널 상태 |
|---------|---------|
|  녹색 |커널이 연결됨, 유휴 상태, 사용 중|
|  회색 |커널이 연결되지 않음 |

## <a name="find-compute-details"></a>컴퓨팅 세부 정보 찾기

[스튜디오](https://ml.azure.com)의 **컴퓨팅** 페이지에서 컴퓨팅 인스턴스에 대한 세부 정보를 찾습니다.

## <a name="useful-keyboard-shortcuts"></a>유용한 바로 가기 키
Jupyter Notebook과 마찬가지로 Azure Machine Learning Studio Notebook에는 모달 사용자 인터페이스가 있습니다. 키보드는 Notebook 셀이 있는 모드에 따라 다른 작업을 수행합니다. Azure Machine Learning Studio Notebook은 지정된 코드 셀에 대해 두 가지 모드(명령 모드 및 편집 모드)를 지원합니다.

### <a name="command-mode-shortcuts"></a>명령 모드 바로 가기

입력하라는 텍스트 커서가 없으면 셀은 명령 모드에 있습니다. 셀이 명령 모드에 있으면 Notebook을 전체적으로 편집할 수 있지만 개별 셀에는 입력할 수 없습니다. `ESC` 키를 누르거나 마우스를 사용하여 셀의 편집기 영역 외부를 선택하면 명령 모드로 전환됩니다.  활성 셀의 왼쪽 테두리는 파란색 단색이고 **실행** 단추는 파란색입니다.

   :::image type="content" source="media/how-to-run-jupyter-notebooks/command-mode.png" alt-text="명령 모드의 Notebook 셀 ":::

| 바로 가기                      | 설명                          |
| ----------------------------- | ------------------------------------|
| Enter 키                         | 편집 모드 시작             |        
| Shift+Enter                 | 셀을 실행하고 아래 선택         |     
| Control/Command+Enter       | 셀 실행                            |
| Alt+Enter                   | 셀 실행, 아래에 코드 셀 삽입    |
| Control/Command+Alt+Enter | 셀 실행, 아래에 Markdown 셀 삽입|
| Alt+R                       | 모두 실행      |                       
| Y                             | 셀을 코드로 변환    |                         
| M                             | 셀을 Markdown으로 변환  |                       
| 위쪽 화살표/K                          | 위 셀 선택    |               
| 아래쪽 화살표/J                        | 아래 셀 선택    |               
| A                             | 위에 코드 셀 삽입  |            
| b                             | 아래에 코드 셀 삽입   |           
| Control/Command+Shift+A   | 위에 Markdown 셀 삽입    |      
| Control/Command+Shift+B   | 아래에 Markdown 셀 삽입   |       
| X                             | 선택한 셀 잘라내기    |               
| C                             | 선택한 셀 복사   |               
| Shift+V                     | 위에 선택한 셀 붙여넣기           |
| V                             | 아래에 선택한 셀 붙여넣기    |       
| D D                           | 선택한 셀 삭제|                
| O                             | Toggle Output         |              
| Shift+O                     | 출력 스크롤 토글   |          
| I I                           | 인터럽트 커널 |                   
| 0 0                           | 커널 다시 시작 |                     
| Shift+스페이스바                 | 위로 스크롤  |                         
| Space                         | 아래로 스크롤|
| 탭                           | 다음 포커스 가능 항목으로 포커스 변경(탭 트래핑을 사용하지 않을 경우)|
| Control/Command+S           | Notebook 저장 |                      
| 1                             | h1로 변경|                       
| 2                             | h2로 변경|                        
| 3                             | h3로 변경|                        
| 4                             | h4로 변경 |                       
| 5                             | h5로 변경 |                       
| 6                             | h6로 변경 |                       

### <a name="edit-mode-shortcuts"></a>편집 모드 바로 가기

편집 모드에서는 편집기 영역에 입력하라는 텍스트 커서가 표시됩니다. 셀이 편집 모드에 있으면 셀에 입력할 수 있습니다. `Enter` 키를 누르거나 마우스를 사용하여 셀의 편집기 영역을 선택하면 편집 모드로 전환됩니다. 활성 셀의 왼쪽 테두리는 녹색 및 빗금이고 **실행** 단추는 녹색입니다. 또한 편집 모드의 셀에는 커서 프롬프트가 표시됩니다.

   :::image type="content" source="media/how-to-run-jupyter-notebooks/edit-mode.png" alt-text="편집 모드의 Notebook 셀":::

편집 모드에서 다음과 같은 키 입력 바로 가기를 사용하면 Azure Machine Learning Notebook에서 보다 편리하게 코드를 탐색하고 실행할 수 있습니다.

| 바로 가기                      | 설명|                                     
| ----------------------------- | ----------------------------------------------- |
| 이스케이프                        | 명령 모드 입력|  
| Control/Command+스페이스바       | IntelliSense 활성화 |
| Shift+Enter                 | 셀을 실행하고 아래 선택 |                         
| Control/Command+Enter       | 셀 실행  |                                      
| Alt+Enter                   | 셀 실행, 아래에 코드 셀 삽입  |              
| Control/Command+Alt+Enter | 셀 실행, 아래에 Markdown 셀 삽입  |          
| Alt+R                       | 모든 셀 실행     |                              
| 위로                            | 위 또는 이전 셀로 커서 이동    |             
| 아래로                          | 아래 또는 다음 셀로 커서 이동 |                  
| Control/Command+S           | Notebook 저장   |                                
| Control/Command+Up          | 셀 시작으로 이동   |                             
| Control/Command+Down        | 셀 끝으로 이동 |                                 
| 탭                           | 코드 완성 또는 들여쓰기(탭 트래핑을 사용하는 경우) |
| Control/Command+M           | 탭 트래핑 사용/사용 안 함  |                       
| Control/Command+]           | 들여쓰기 |                                         
| Control/Command+[           | 내어쓰기  |                                        
| Control/Command+A           | 모두 선택|                                      
| Control/Command+Z           | 실행 취소 |                                           
| Control/Command+Shift+Z   | 다시 실행 |                                           
| Control/Command+Y           | 다시 실행 |                                           
| Control/Command+Home        | 셀 시작으로 이동|                                
| Control/Command+End         | 셀 끝으로 이동   |                               
| Control/Command+Left        | 한 단어 왼쪽으로 이동 |                               
| Control/Command+Right       | 한 단어 오른쪽으로 이동 |                              
| Control/Command+백스페이스   | 이전 단어 삭제 |                             
| Control/Command+Delete      | 다음 단어 삭제 |                              
| Control/Command+/           | 셀에서 주석 토글

## <a name="troubleshooting"></a>문제 해결

* Notebook에 연결할 수 없으면 WebSocket 통신이 사용하지 않도록 설정되지 **않았는지** 확인합니다. 컴퓨팅 인스턴스 Jupyter 기능이 작동하기 위해서는 WebSocket 통신이 사용하도록 설정되어 있어야 합니다. 네트워크에서 *. instances.azureml.net 및 *. instances.azureml.ms에 대 한 [websocket 연결을 허용](how-to-access-azureml-behind-firewall.md?tabs=ipaddress#microsoft-hosts) 하는지 확인 합니다. 

* 계산 인스턴스가 개인 끝점을 사용 하는 작업 영역에 배포 된 경우 [가상 네트워크 내 에서만 액세스할](./how-to-secure-training-vnet.md)수 있습니다. 사용자 지정 DNS 또는 호스트 파일을 사용 하는 경우 < 인스턴스 이름 >에 대 한 항목을 작업 영역 개인 끝점의 개인 IP 주소를 사용 하 여 < 지역 >에 추가 합니다. 자세한 내용은 [사용자 지정 DNS](./how-to-custom-dns.md?tabs=azure-cli) 문서를 참조하세요.

* 커널이 충돌하여 다시 시작된 경우 `sudo journalctl -u jupyter` 명령을 실행하여 jupyter 로그를 확인하고 자세한 정보를 확인할 수 있습니다. 커널 문제가 지속되면 더 많은 메모리가 있는 컴퓨팅 인스턴스를 사용하는 것이 좋습니다.

* 만료된 토큰 문제가 발생하면 Azure ML 스튜디오에서 로그아웃했다가 다시 로그인한 다음, Notebook 커널을 다시 시작합니다.
    
## <a name="next-steps"></a>다음 단계

* [첫 번째 실험 실행](tutorial-1st-experiment-sdk-train.md)
* [스냅샷으로 파일 스토리지 백업](../storage/files/storage-snapshots-files.md)
* [보안 환경에서 작업](./how-to-secure-training-vnet.md#compute-cluster)
