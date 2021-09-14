---
title: Synapse Notebook 사용 방법
description: 이 문서에서는 데이터 준비 및 시각화를 수행하기 위해 Synapse Notebook을 만들고 개발하는 방법을 알아봅니다.
services: synapse analytics
author: ruixinxu
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: spark
ms.date: 05/08/2021
ms.author: ruxu
ms.reviewer: ''
ms.custom: devx-track-python
ms.openlocfilehash: a0f4a8602b3f4b10ac1ef6ca1ac65e5bedc76210
ms.sourcegitcommit: ef448159e4a9a95231b75a8203ca6734746cd861
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/30/2021
ms.locfileid: "123187400"
---
# <a name="create-develop-and-maintain-synapse-notebooks-in-azure-synapse-analytics"></a>Azure Synapse Analytics에서 Synapse Notebook 만들기, 개발, 유지 관리

Synapse Notebook은 라이브 코드, 시각화, 내레이션 텍스트를 포함하는 파일을 만들 수 있는 웹 인터페이스입니다. Notebook은 아이디어를 검증하고 빠른 실험을 사용하여 데이터를 통해 인사이트를 확보하기 좋은 도구입니다. Notebook은 데이터 준비, 데이터 시각화, 기계 학습 및 기타 빅 데이터 시나리오에서도 널리 사용됩니다.

Synapse Notebook의 이점은 다음과 같습니다.

* 시작에 필요한 설정 작업이 전혀 없습니다.
* 기본 제공 엔터프라이즈 보안 기능으로 데이터 보안을 유지합니다.
* 원시 형식(CSV, txt, JSON 등), 처리된 파일 형식(parquet, Delta Lake, ORC 등), SQL 및 Spark에 대한 SQL 테이블 형식 데이터 파일 모두에서 데이터를 분석합니다.
* 향상된 제작 기능 및 기본 제공 데이터 시각화를 통해 생산성을 높일 수 있습니다.

이 문서에서는 Synapse Studio에서 Notebook을 사용하는 방법을 설명합니다.

## <a name="preview-of-the-new-notebook-experience"></a>새 Notebook 환경의 프리뷰
Synapse 팀은 새로운 Notebook 구성 요소를 Synapse Studio로 가져와서 Microsoft 고객을 위한 일관된 Notebook 환경을 제공하고 검색 기능, 생산성, 공유, 협업을 극대화합니다. 새 Notebook 환경은 프리뷰 준비가 되었습니다. Notebook 도구 모음의 **미리 보기 기능** 단추를 선택하여 이 기능을 켭니다. 다음 표에는 기존 Notebook("클래식 노트북"이라고 함)과 새 프리뷰 Notebook 간의 기능 비교가 정리되어 있습니다.  

|기능|클래식 Notebook|프리뷰 Notebook|
|--|--|--|
|%run| 지원되지 않음 | &#9745;|
|%history| 지원되지 않음 |&#9745;|
|%load| 지원되지 않음 |&#9745;|
|%%html| 지원되지 않음 |&#9745;|
|끌어서 놓아 셀 이동| 지원되지 않음 |&#9745;|
|개요(목차)| 지원되지 않음 |&#9745;|
|변수 탐색기| 지원되지 않음 |&#9745;|
|도구 모음 단추를 사용하여 텍스트 셀 서식 지정|&#9745;| 사용할 수 없음 |
|코드 셀 주석 달기| 지원되지 않음 | &#9745;|


## <a name="create-a-notebook"></a>Notebook 만들기

Notebook을 만드는 방법은 두 가지입니다. **개체 탐색기** 에서 새 Notebook을 만들거나 Synapse 작업 영역으로 기존 Notebook을 가져올 수 있습니다. Synapse Notebook은 표준 Jupyter Notebook IPYNB 파일을 인식합니다.

![새로 만들기 또는 Notebook 가져오기 스크린샷](./media/apache-spark-development-using-notebooks/synapse-create-import-notebook-2.png)

## <a name="develop-notebooks"></a>Notebook 개발

Notebook은 셀로 구성됩니다. 셀은 독립적으로 또는 그룹으로 실행할 수 있는 개별 코드 또는 텍스트 블록입니다.

Notebook을 개발하기 위한 다양한 작업을 제공합니다.
+ [셀 추가](#add-a-cell)
+ [주 언어 설정](#set-a-primary-language)
+ [여러 언어 사용](#use-multiple-languages)
+ [임시 테이블을 사용하여 언어 간 데이터 참조](#use-temp-tables-to-reference-data-across-languages)
+ [IDE 스타일 IntelliSense](#ide-style-intellisense)
+ [코드 조각](#code-snippets)
+ [도구 모음 단추를 사용하여 텍스트 셀 서식 지정](#format-text-cell-with-toolbar-buttons)
+ [셀 작업 실행 취소](#undo-cell-operation)
+ [코드 셀 주석 달기](#Code-cell-commenting)
+ [셀 이동](#move-a-cell)
+ [셀 삭제](#delete-a-cell)
+ [셀 입력 접기](#collapse-a-cell-input)
+ [셀 출력 접기](#collapse-a-cell-output)
+ [Notebook 개요](#notebook-outline)

<h3 id="add-a-cell">셀 추가</h3>

Notebook에 새 셀을 추가하는 방법은 여러 가지입니다.

# <a name="classical-notebook"></a>[클래식 Notebook](#tab/classical)

1. 왼쪽 위에 있는 **+ 셀** 단추를 펼치고 **코드 셀 추가** 또는 **텍스트 셀 추가** 를 선택합니다.

    ![add-cell-with-cell-button 스크린샷](./media/apache-spark-development-using-notebooks/synapse-add-cell-1.png)

2. 두 셀 사이의 공간을 마우스로 가리키고 **코드 추가** 또는 **텍스트 추가** 를 선택합니다.

    ![add-cell-between-space 스크린샷](./media/apache-spark-development-using-notebooks/synapse-add-cell-2.png)

3. [명령 모드에서 바로 가기 키](#shortcut-keys-under-command-mode)를 사용합니다. 현재 셀 위에 셀을 삽입하려면 **A** 를 누릅니다. 현재 셀 아래에 셀을 삽입하려면 **B** 를 누릅니다.


# <a name="preview-notebook"></a>[프리뷰 Notebook](#tab/preview)

1. 왼쪽 위에 있는 **+ 셀** 단추를 펼치고 **코드 셀** 또는 **Markdown 셀** 을 선택합니다.
    ![add-azure-notebook-cell-with-cell-button 스크린샷](./media/apache-spark-development-using-notebooks/synapse-azure-notebook-add-cell-1.png)
2. 셀의 시작 부분에 있는 더하기 기호를 선택하고 **코드 셀** 또는 **Markdown 셀** 을 선택합니다.

    ![add-azure-notebook-cell-between-space 스크린샷](./media/apache-spark-development-using-notebooks/synapse-azure-notebook-add-cell-2.png)

3. [명령 모드에서 aznb 바로 가기 키](#shortcut-keys-under-command-mode)를 사용합니다. 현재 셀 위에 셀을 삽입하려면 **A** 를 누릅니다. 현재 셀 아래에 셀을 삽입하려면 **B** 를 누릅니다.

---

<h3 id="set-a-primary-language">주 언어 설정</h3>

Synapse Notebook은 4개의 Apache Spark 언어를 지원합니다.

* pySpark(Python)
* Spark(Scala)
* SparkSQL
* .NET for Apache Spark(C#)

새로 추가한 셀의 주 언어는 위쪽 명령 모음의 드롭다운 목록에서 설정할 수 있습니다.

   ![default-synapse-language 스크린샷](./media/apache-spark-development-using-notebooks/synapse-default-language.png)


<h3 id="use-multiple-languages">여러 언어 사용</h3>

셀 시작 부분에 올바른 언어 매직 명령을 지정하면 Notebook 하나에서 여러 언어를 사용할 수 있습니다. 셀 언어를 전환하는 매직 명령은 다음 표에 나열되어 있습니다.

|매직 명령 |언어 | Description |  
|---|------|-----|
|%%pyspark| Python | Spark 컨텍스트에 대해 **Python** 쿼리를 실행합니다.  |
|%%spark| 스칼라 | Spark 컨텍스트에 대해 **Scala** 쿼리를 실행합니다.  |  
|%%sql| SparkSQL | Spark 컨텍스트에 대해 **SparkSQL** 쿼리를 실행합니다.  |
|%%csharp | .NET for Spark C# | Spark 컨텍스트에 대해 **.NET for Spark C#** 쿼리를 실행합니다. |

다음 이미지는 **Spark(Scala)** Notebook에서 **%%pyspark** 매직 명령을 사용하여 PySpark 쿼리를 작성하거나 **%%sql** 매직 명령을 사용하여 SparkSQL 쿼리를 작성하는 방법의 예입니다. Notebook의 주 언어는 pySpark로 설정되어 있습니다.

   ![Synapse Spark 매직 명령 스크린샷](./media/apache-spark-development-using-notebooks/synapse-spark-magics.png)


<h3 id="use-temp-tables-to-reference-data-across-languages&quot;>임시 테이블을 사용하여 언어 간 데이터 참조</h3>

Synapse Notebook의 여러 언어 간에 직접 데이터나 변수를 참조할 수 없습니다. Spark에서는 여러 언어 간에 임시 테이블을 참조할 수 있습니다. 다음은 차선책으로 Spark temp 테이블을 사용하여 `PySpark`와 `SparkSQL`에서 `Scala` DataFrame을 읽는 방법의 예입니다.

1. 셀 1에서는 Scala를 사용하여 SQL 풀 커넥터에서 DataFrame을 읽고 임시 테이블을 생성합니다.

   ```scala
   %%scala
   val scalaDataFrame = spark.read.sqlanalytics(&quot;mySQLPoolDatabase.dbo.mySQLPoolTable")
   scalaDataFrame.createOrReplaceTempView( "mydataframetable" )
   ```

2. 셀 2에서는 Spark SQL을 사용하여 데이터를 쿼리합니다.
   
   ```sql
   %%sql
   SELECT * FROM mydataframetable
   ```

3. 셀 3에서는 PySpark의 데이터를 사용합니다.

   ```python
   %%pyspark
   myNewPythonDataFrame = spark.sql("SELECT * FROM mydataframetable")
   ```

<h3 id="ide-style-intellisense">IDE 스타일 IntelliSense</h3>

Synapse Notebook은 Monaco 편집기와 통합되어 IDE 스타일 IntelliSense를 셀 편집기로 가져옵니다. 구문 강조 표시, 오류 표식, 자동 코드 완성 기능은 신속하게 코드를 작성하고 문제를 식별하는 데 유용합니다.

IntelliSense 기능은 완성도 수준이 언어마다 다릅니다. 지원되는 기능은 다음 표를 참조하세요.

|언어| 구문 강조 표시 | 구문 오류 표식  | 구문 코드 완성 | 변수 코드 완성| 시스템 함수 코드 완성| 사용자 함수 코드 완성| 스마트 들여쓰기 | 코드 접기|
|--|--|--|--|--|--|--|--|--|
|PySpark(Python)|예|예|예|예|예|예|예|예|
|Spark(Scala)|예|예|예|예|-|-|-|예|
|SparkSQL|예|예|-|-|-|-|-|-|
|.NET for Spark(C#)|예|예|예|예|예|예|예|예|

>[!Note]
> 활성 Spark 세션은 변수 코드 완성, 시스템 함수 코드 완성, Spark(C#)용 .NET에 대한 사용자 함수 코드 완료를 활용하는 데 필요합니다.

<h3 id="code-snippets">코드 조각</h3>

Synapse Notebook은 Spark 세션 구성, 데이터를 Spark DataFrame으로 읽기 또는 matplotlib로 차트 그리기와 같은 일반적으로 사용되는 코드 패턴을 쉽게 입력할 수 있게 해주는 코드 조각을 제공합니다.

코드 조각은 [IDE 스타일 IntelliSense의 바로 가기 키](#ide-style-intellisense)에 다른 제안 사항과 함께 표시됩니다. 코드 조각 콘텐츠는 코드 셀 언어에 따라 달라집니다. **Snippet** 을 입력하여 사용 가능한 코드 조각을 확인할 수 있고 또는 아무 키워드나 입력하면 코드 셀 편집기의 코드 조각 제목에 표시됩니다. 예를 들어 **read** 를 입력하면 여러 데이터 원본으로부터 데이터 읽기를 위한 코드 조각 목록이 표시됩니다.

![Synapse 코드 조각 애니메이션 GIF](./media/apache-spark-development-using-notebooks/synapse-code-snippets.gif#lightbox)


<h3 id="format-text-cell-with-toolbar-buttons">도구 모음 단추를 사용하여 텍스트 셀 서식 지정</h3>

# <a name="classical-notebook"></a>[클래식 Notebook](#tab/classical)

텍스트 셀 도구 모음의 서식 단추를 사용하여 일반적인 Markdown 작업을 수행할 수 있습니다. 여기에는 텍스트 굵게 표시, 텍스트에 기울임꼴 적용, 코드 조각 삽입, 순서가 지정되지 않은 목록 삽입, 순서가 지정된 목록 삽입, URL의 이미지 삽입이 포함됩니다.

  ![Synapse 텍스트 셀 도구 모음 스크린샷](./media/apache-spark-development-using-notebooks/synapse-text-cell-toolbar.png)

# <a name="preview-notebook"></a>[프리뷰 Notebook](#tab/preview)

서식 단추 도구 모음은 아직 프리뷰 Notebook 환경에 사용할 수 없습니다. 

---

<h3 id="undo-cell-operation">셀 작업 실행 취소</h3>

# <a name="classical-notebook"></a>[클래식 Notebook](#tab/classical)

**실행 취소** / **다시 실행** 단추를 선택하거나 **Ctrl+Z** / **Ctrl+Y** 를 눌러 최근 셀 작업을 철회합니다. 이제 기록된 최근 셀 작업을 20개까지 실행 취소/다시 실행할 수 있습니다. 

   ![Synapse 실행 취소 셀 스크린샷](./media/apache-spark-development-using-notebooks/synapse-undo-cells.png)

# <a name="preview-notebook"></a>[프리뷰 Notebook](#tab/preview)

**실행 취소** / **다시 실행** 단추를 선택하거나 **Z** / **Shift+Z** 를 눌러 최근 셀 작업을 철회합니다. 이제 기록된 최근 셀 작업을 10개까지 실행 취소/다시 실행할 수 있습니다.

   ![aznb의 Synapse 실행 취소 셀 스크린샷](./media/apache-spark-development-using-notebooks/synapse-undo-cells-aznb.png)

지원되는 실행 취소 셀 작업:
+ 셀 삽입/삭제: **실행 취소** 를 선택하여 삭제 작업을 철회할 수 있습니다. 텍스트 콘텐츠가 셀과 함께 유지됩니다.
+ 셀 다시 정렬
+ 매개 변수 설정/해제
+ 코드 셀과 Markdown 셀 간 변환

> [!NOTE]
> 셀 내 텍스트 작업과 코드 셀 주석 달기 작업은 실행 취소할 수 없습니다.


---

<h3 id="Code-cell-commenting">코드 셀 주석 달기</h3>

# <a name="classical-notebook"></a>[클래식 Notebook](#tab/classical)

지원되지 않습니다.

# <a name="preview-notebook"></a>[프리뷰 Notebook](#tab/preview)

지금은 프리뷰 Notebook에서 코드 셀 주석 처리를 지원합니다.

1. Notebook 도구 모음에서 **주석** 단추를 선택하여 **주석** 창을 엽니다.

   ![Synapse 주석 단추 스크린샷](./media/apache-spark-development-using-notebooks/synapse-comments-button.png)

2. 코드 셀에서 코드를 선택하고 **주석** 창에서 **새로 만들기** 를 클릭한 다음, 주석을 추가하고 **주석 게시** 단추를 클릭하여 저장합니다.

   ![Synapse 새 주석 스크린샷](./media/apache-spark-development-using-notebooks/synapse-new-comments.png)

3. 주석 옆에 있는 **자세히** 단추를 클릭하여 **주석 편집**, **스레드 확인** 또는 **스레드 삭제** 를 수행할 수 있습니다. 

   ![Synapse 주석 편집 스크린샷](./media/apache-spark-development-using-notebooks/synapse-edit-comments.png)

---


<h3 id="move-a-cell">셀 이동</h3>

# <a name="classical-notebook"></a>[클래식 Notebook](#tab/classical)

줄임표(...)를 선택하면 오른쪽 끝에 있는 다른 셀 작업 메뉴에 액세스할 수 있습니다. 그런 다음, **위로 셀 이동** 또는 **아래로 셀 이동** 을 선택하여 현재 셀을 이동합니다. 

[명령 모드에서 바로 가기 키](#shortcut-keys-under-command-mode)를 사용할 수도 있습니다. 현재 셀을 위로 이동하려면 **Ctrl+Alt+↑** 키를 누릅니다. 현재 셀을 아래로 이동하려면 **Ctrl+Alt+↓** 키를 누릅니다.

   ![move-a-cell 스크린샷](./media/apache-spark-development-using-notebooks/synapse-move-cells.png)

# <a name="preview-notebook"></a>[프리뷰 Notebook](#tab/preview)

셀의 왼쪽을 클릭하고 원하는 위치로 끕니다. 
    ![Synapse 셀 이동 애니메이션 GIF](./media/apache-spark-development-using-notebooks/synapse-azure-notebook-drag-drop-cell.gif)

---

<h3 id="delete-a-cell">셀 삭제</h3>

# <a name="classical-notebook"></a>[클래식 Notebook](#tab/classical)

셀을 삭제하려면 줄임표(...)를 선택하여 오른쪽 끝에 있는 다른 셀 작업 메뉴에 액세스한 후 **셀 삭제** 를 선택합니다. 

[명령 모드에서 바로 가기 키](#shortcut-keys-under-command-mode)를 사용할 수도 있습니다. 현재 행을 삭제하려면 **D,D** 를 누릅니다.
  
   ![delete-a-cell 스크린샷](./media/apache-spark-development-using-notebooks/synapse-delete-cell.png)

# <a name="preview-notebook"></a>[프리뷰 Notebook](#tab/preview)

셀을 삭제하려면 셀의 오른쪽에 있는 삭제 단추를 선택합니다. 

[명령 모드에서 바로 가기 키](#shortcut-keys-under-command-mode)를 사용할 수도 있습니다. 현재 행을 삭제하려면 **Shift+D** 를 누릅니다. 

   ![azure-notebook-delete-a-cell 스크린샷](./media/apache-spark-development-using-notebooks/synapse-azure-notebook-delete-cell.png)

---

<h3 id="collapse-a-cell-input">셀 입력 접기</h3>

# <a name="classical-notebook"></a>[클래식 Notebook](#tab/classical)

현재 셀의 아래쪽에 있는 화살표 단추를 선택하면 셀이 축소됩니다. 셀을 펼치려면 셀이 축소된 상태에서 화살표 단추를 선택합니다.

   ![collapse-cell-input 애니메이션 GIF](./media/apache-spark-development-using-notebooks/synapse-collapse-cell-input.gif)

# <a name="preview-notebook"></a>[프리뷰 Notebook](#tab/preview)

셀 도구 모음에서 **기타 명령** 줄임표(...)를 선택하고 현재 셀의 입력을 축소하려면 **입력** 을 선택합니다. 셀을 펼치려면 셀이 축소된 상태에서 **입력 숨김** 을 선택합니다.

   ![azure-notebook-collapse-cell-input 애니메이션 GIF](./media/apache-spark-development-using-notebooks/synapse-azure-notebook-collapse-cell-input.gif)

---

<h3 id="collapse-a-cell-output">셀 출력 접기</h3>

# <a name="classical-notebook"></a>[클래식 Notebook](#tab/classical)

현재 셀 출력의 왼쪽 위에 있는 **출력 접기** 단추를 선택하면 셀 출력이 축소됩니다. 출력을 펼치려면 셀 출력이 축소된 상태에서 **셀 출력 표시** 를 클릭합니다.

   ![collapse-cell-output 애니메이션 GIF](./media/apache-spark-development-using-notebooks/synapse-collapse-cell-output.gif)

# <a name="preview-notebook"></a>[프리뷰 Notebook](#tab/preview)

셀 도구 모음에서 **기타 명령** 줄임표(...)를 선택하고 현재 셀의 출력을 축소하려면 **출력** 을 선택합니다. 셀의 출력이 숨겨져 있을 때 확장하려면 동일한 단추를 선택합니다.

   ![azure-notebook-collapse-cell-output 애니메이션 GIF](./media/apache-spark-development-using-notebooks/synapse-azure-notebook-collapse-cell-output.gif)


---

<h3 id="notebook-outline">Notebook 개요</h3>

# <a name="classical-notebook"></a>[클래식 Notebook](#tab/classical)

지원되지 않습니다.

# <a name="preview-notebook"></a>[프리뷰 Notebook](#tab/preview)

개요(목차)는 빠른 탐색을 위해 사이드바 창에 있는 모든 Markdown 셀의 첫 번째 Markdown 헤더를 나타냅니다. Outlines 사이드바는 가능한 최적의 방법으로 화면에 맞게 크기 조절 및 축소가 가능합니다. Notebook 명령 모음에서 **개요** 단추를 선택하여 사이드바를 열거나 숨길 수 있습니다.

![azure-notebook-outline 스크린샷](./media/apache-spark-development-using-notebooks/synapse-azure-notebook-outline.png)

---


## <a name="run-notebooks"></a>Notebook 실행

Notebook의 코드 셀을 개별적으로 또는 한꺼번에 실행할 수 있습니다. 각 셀의 상태와 진행률이 Notebook에 표시됩니다.

### <a name="run-a-cell"></a>셀 실행

셀에서 코드를 실행하는 방법은 여러 가지입니다.

1. 실행할 셀을 마우스로 가리킨 다음, **셀 실행** 단추를 선택하거나 **Ctrl+Enter** 키를 누릅니다.

   ![run-cell-1 스크린샷](./media/apache-spark-development-using-notebooks/synapse-run-cell.png)
  
2. [명령 모드에서 바로 가기 키](#shortcut-keys-under-command-mode)를 사용합니다. **Shift+Enter** 키를 눌러서 현재 셀을 실행하고 아래 셀을 선택합니다. **Alt+Enter** 키를 눌러 현재 셀을 실행하고 아래에 새 셀을 삽입합니다.

---

### <a name="run-all-cells"></a>모든 셀 실행
**모두 실행** 단추를 선택하면 현재 Notebook의 모든 셀이 순서대로 실행됩니다.

   ![run-all-cells 스크린샷](./media/apache-spark-development-using-notebooks/synapse-run-all.png)


### <a name="run-all-cells-above-or-below"></a>위 또는 아래 셀 모두 실행

# <a name="classical-notebook"></a>[클래식 Notebook](#tab/classical)

오른쪽 끝에 있는 다른 셀 작업 메뉴에 액세스하려면 줄임표( **...** )를 선택합니다. 그런 다음, 현재 셀 위에 있는 모든 셀을 순서대로 실행하려면 **Run cells above**(위 셀 실행)를 선택합니다. 현재 셀 아래 모든 셀을 순서대로 실행하려면 **Run cells below**(아래 셀 실행)을 선택합니다.

   ![run-cells-above-or-below 스크린샷](./media/apache-spark-development-using-notebooks/synapse-run-cells-above-or-below.png)

# <a name="preview-notebook"></a>[프리뷰 Notebook](#tab/preview)

**모두 실행** 단추에서 드롭다운 목록을 확장한 다음, 현재 셀 위의 모든 셀을 순서대로 실행하려면 **위 셀 실행** 을 선택합니다. 현재 셀 아래 모든 셀을 순서대로 실행하려면 **Run cells below**(아래 셀 실행)을 선택합니다.

   ![azure-notebook-run-cells-above-or-below 스크린샷](./media/apache-spark-development-using-notebooks/synapse-aznb-run-cells-above-or-below.png)

---

### <a name="cancel-all-running-cells"></a>실행 중인 모든 셀 취소

# <a name="classical-notebook"></a>[클래식 Notebook](#tab/classical)
실행 중인 셀 또는 큐에서 대기 중인 셀을 취소하려면 **모두 취소** 단추를 선택합니다. 
   ![cancel-all-cells 스크린샷](./media/apache-spark-development-using-notebooks/synapse-cancel-all.png) 

# <a name="preview-notebook"></a>[프리뷰 Notebook](#tab/preview)

실행 중인 셀 또는 큐에서 대기 중인 셀을 취소하려면 **모두 취소** 단추를 선택합니다. 
   ![azure-notebook-cancel-all-cells 스크린샷](./media/apache-spark-development-using-notebooks/synapse-aznb-cancel-all.png) 

---



### <a name="notebook-reference"></a>Notebook 참조

# <a name="classical-notebook"></a>[클래식 Notebook](#tab/classical)

지원되지 않습니다.

# <a name="preview-notebook"></a>[프리뷰 Notebook](#tab/preview)

```%run <notebook path>``` 매직 명령을 사용하여 현재 Notebook의 컨텍스트 내에서 다른 Notebook을 참조할 수 있습니다. 참조 Notebook에 정의된 모든 변수는 현재 Notebook에서 사용할 수 있습니다. ```%run``` 매직 명령은 중첩된 호출을 지원하지만 재귀 호출은 지원하지 않습니다. 문 깊이가 **5** 보다 크면 예외를 수신하게 됩니다.  

예: ``` %run /<path>/Notebook1 { "parameterInt": 1, "parameterFloat": 2.5, "parameterBool": true,  "parameterString": "abc" } ```.

Notebook 참조는 대화형 모드와 Synapse 파이프라인 모두에서 작동합니다.

> [!NOTE]
> - ```%run``` 명령은 현재 절대 경로나 Notebook 이름만 매개 변수로 전달하도록 지원합니다. 상대 경로는 지원되지 않습니다. 
> - ```%run``` 명령은 현재 4개의 매개 변수 값 형식인 `int`, `float`, `bool`, `string`만 지원합니다. 변수 대체 작업은 지원되지 않습니다.
> - 참조된 Notebook을 게시해야 합니다. 참조하려면 Notebook을 게시해야 합니다. Synapse Studio는 Git 리포지토리에서 게시되지 않은 Notebook을 인식하지 못합니다. 
> - 참조된 Notebook은 깊이가 **5** 보다 큰 문을 지원하지 않습니다.
>

---

### <a name="variable-explorer"></a>변수 탐색기

# <a name="classical-notebook"></a>[클래식 Notebook](#tab/classical)

지원되지 않습니다.

# <a name="preview-notebook"></a>[프리뷰 Notebook](#tab/preview)

Synapse Notebook은 PySpark(Python) 셀에 대한 현재 Spark 세션의 변수 이름, 유형, 길이 및 값 목록을 볼 수 있는 기본 제공 변수 탐색기를 제공합니다. 코드 셀에 정의된 대로 더 많은 변수가 자동으로 표시됩니다. 각 열 헤더를 클릭하면 테이블의 변수가 정렬됩니다.

Notebook 명령 모음에서 **변수** 단추를 선택하여 변수 탐색기를 열거나 숨길 수 있습니다.

![azure-notebook-variable-explorer 스크린샷](./media/apache-spark-development-using-notebooks/synapse-azure-notebook-variable-explorer.png)


---

### <a name="cell-status-indicator"></a>셀 상태 표시기

셀 아래에 단계별 셀 실행 상태가 표시되어 현재 진행 상황을 확인할 수 있습니다. 셀 실행이 완료되면 총 지속 시간 및 종료 시간이 포함된 실행 요약이 표시되고 나중에 참조할 수 있도록 보관됩니다.

![cell-status 스크린샷](./media/apache-spark-development-using-notebooks/synapse-cell-status.png)

### <a name="spark-progress-indicator"></a>Spark 진행률 표시기

Synapse Notebook은 순전히 Spark 기반입니다. 코드 셀은 서버리스 Apache Spark 풀에서 원격으로 실행됩니다. Spark 작업 진행률 표시기에 실시간 진행률 표시줄이 제공되어 작업 실행 상태를 이해하는 데 유용합니다.
각 작업 또는 단계당 태스크 수를 통해 Spark 작업의 병렬 수준을 확인할 수 있습니다. 작업(또는 단계) 이름의 링크를 선택하여 특정 작업(또는 단계)의 Spark UI로 드릴다운할 수도 있습니다.


![spark-progress-indicator 스크린샷](./media/apache-spark-development-using-notebooks/synapse-spark-progress-indicator.png)

### <a name="spark-session-config"></a>Spark 세션 구성

**세션 구성** 에서 현재 Spark 세션에 제공할 실행기의 크기와 수, 제한 시간을 지정할 수 있습니다. Spark 세션을 다시 시작하면 구성 변경 사항이 적용됩니다. 캐시된 Notebook 변수는 모두 지워집니다.

[![session-management 스크린샷](./media/apache-spark-development-using-notebooks/synapse-azure-notebook-spark-session-management.png)](./media/apache-spark-development-using-notebooks/synapse-azure-notebook-spark-session-management.png#lightbox)

#### <a name="spark-session-config-magic-command"></a>Spark 세션 구성 매직 명령
**%% configure** 매직 명령을 통해 Spark 세션 설정을 지정할 수도 있습니다. 설정을 적용하려면 Spark 세션을 다시 시작해야 합니다. Notebook의 시작 부분에서 **%% configure** 를 실행하는 것이 좋습니다. 다음은 샘플입니다. 유효한 매개 변수의 전체 목록은 https://github.com/cloudera/livy#request-body 를 참조하세요. 

```json
%%configure
{
    //You can get a list of valid parameters to config the session from https://github.com/cloudera/livy#request-body.
    "driverMemory":"28g", // Recommended values: ["28g", "56g", "112g", "224g", "400g", "472g"]
    "driverCores":4, // Recommended values: [4, 8, 16, 32, 64, 80]
    "executorMemory":"28g",
    "executorCores":4, 
    "jars":["abfs[s]://<file_system>@<account_name>.dfs.core.windows.net/<path>/myjar.jar","wasb[s]://<containername>@<accountname>.blob.core.windows.net/<path>/myjar1.jar"],
    "conf":{
    //Example of standard spark property, to find more available properties please visit:https://spark.apache.org/docs/latest/configuration.html#application-properties.
        "spark.driver.maxResultSize":"10g",
    //Example of customized property, you can specify count of lines that Spark SQL returns by configuring "livy.rsc.sql.num-rows".
        "livy.rsc.sql.num-rows&quot;:&quot;3000" 
    }
}
```
> [!NOTE]
> - “DriverMemory” 및 “ExecutorMemory”는 “driverCores” 및 “executorCores”와 마찬가지로 %%configure에서 동일한 값으로 설정하는 것이 좋습니다.
> - Synapse 파이프라인에서 Spark 세션 구성 매직 명령을 사용할 수 있습니다. 최상위 수준에서 호출된 경우에만 적용됩니다. 참조된 Notebook에 사용된 %%구성은 무시됩니다.
> - Spark 구성 속성은 "conf" 본문에서 사용해야 합니다. Spark 구성 속성에 대한 최상위 참조는 지원되지 않습니다.
>

## <a name="bring-data-to-a-notebook"></a>Notebook에 데이터 가져오기

아래 코드 샘플과 같이 Azure Blob Storage, Azure Data Lake Store Gen 2 및 SQL 풀에서 데이터를 로드할 수 있습니다.

### <a name="read-a-csv-from-azure-data-lake-store-gen2-as-a-spark-dataframe"></a>Azure Data Lake Store Gen2에서 Spark DataFrame으로 CSV 읽기

```python
from pyspark.sql import SparkSession
from pyspark.sql.types import *
account_name = "Your account name"
container_name = "Your container name"
relative_path = "Your path"
adls_path = 'abfss://%s@%s.dfs.core.windows.net/%s' % (container_name, account_name, relative_path)

df1 = spark.read.option('header', 'true') \
                .option('delimiter', ',') \
                .csv(adls_path + '/Testfile.csv')

```

#### <a name="read-a-csv-from-azure-blob-storage-as-a-spark-dataframe"></a>Azure Blob Storage에서 Spark DataFrame으로 CSV 읽기

```python

from pyspark.sql import SparkSession

# Azure storage access info
blob_account_name = 'Your account name' # replace with your blob name
blob_container_name = 'Your container name' # replace with your container name
blob_relative_path = 'Your path' # replace with your relative folder path
linked_service_name = 'Your linked service name' # replace with your linked service name

blob_sas_token = mssparkutils.credentials.getConnectionStringOrCreds(linked_service_name)

# Allow SPARK to access from Blob remotely

wasb_path = 'wasbs://%s@%s.blob.core.windows.net/%s' % (blob_container_name, blob_account_name, blob_relative_path)

spark.conf.set('fs.azure.sas.%s.%s.blob.core.windows.net' % (blob_container_name, blob_account_name), blob_sas_token)
print('Remote blob path: ' + wasb_path)

df = spark.read.option("header", "true") \
            .option("delimiter","|") \
            .schema(schema) \
            .csv(wasbs_path)
```

### <a name="read-data-from-the-primary-storage-account"></a>기본 스토리지 계정에서 데이터 읽기

기본 스토리지 계정의 데이터에 직접 액세스할 수 있습니다. 비밀 키를 제공할 필요가 없습니다. 데이터 탐색기에서 파일을 마우스 오른쪽 단추로 클릭하고 **새 Notebook** 을 선택하여 데이터 추출기가 자동으로 생성된 새 Notebook을 확인합니다.

![data-to-cell 스크린샷](./media/apache-spark-development-using-notebooks/synapse-data-to-cell.png)


## <a name="ipython-widgets"></a>IPython 위젯


# <a name="classical-notebook"></a>[클래식 Notebook](#tab/classical)

지원되지 않습니다.

# <a name="preview-notebook"></a>[프리뷰 Notebook](#tab/preview)

위젯은 일반적으로 슬라이더, 텍스트 상자 등의 컨트롤처럼 브라우저에 표시되는 이벤트성 python 개체입니다. IPython 위젯은 Python 환경에서만 작동하며 다른 언어(예: Scala, SQL, C#)에서는 아직 지원되지 않습니다. 

### <a name="to-use-ipython-widget"></a>IPython 위젯을 사용하려면
1. Jupyter 위젯 프레임워크를 사용하려면 먼저 `ipywidgets` 모듈을 가져와야 합니다.
   ```python
   import ipywidgets as widgets
   ```
2. 최상위 수준 `display` 함수를 사용하여 위젯을 렌더링하거나 코드 셀의 마지막 줄에  **위젯** 형식의 식을 남길 수 있습니다.
   ```python
   slider = widgets.IntSlider()
   display(slider)
   ```

   ```python
   slider = widgets.IntSlider()
   slider
   ```
   
3. 셀을 실행하면 위젯이 출력 영역에 표시됩니다.

   ![ipython 위젯 슬라이더 스크린샷](./media/apache-spark-development-using-notebooks/ipython-widgets-slider.png)

4. 여러 `display()` 호출을 사용하여 동일한 위젯 인스턴스를 여러 번 렌더링할 수 있지만 서로 동기화된 상태를 유지합니다.

   ```python
   slider = widgets.IntSlider()
   display(slider)
   display(slider)
   ```

   ![ipython 위젯 슬라이더 스크린샷](./media/apache-spark-development-using-notebooks/ipython-widgets-multiple-sliders.png)

5. 서로 독립적인 두 위젯을 렌더링하려면 두 개의 위젯 인스턴스를 만듭니다.

   ```python
   slider1 = widgets.IntSlider()
   slider2 = widgets.IntSlider()
   display(slider1)
   display(slider2)
   ```


### <a name="supported-widgets"></a>지원되는 위젯

|위젯 형식|위젯|
|--|--|
|숫자 위젯|IntSlider, FloatSlider, FloatLogSlider, IntRangeSlider, FloatRangeSlider, IntProgress, FloatProgress, BoundedIntText, BoundedFloatText, IntText, FloatText|
|부울 위젯|ToggleButton, Checkbox, Valid|
|선택 위젯|Dropdown, RadioButtons, Select, SelectionSlider, SelectionRangeSlider, ToggleButtons, SelectMultiple|
|문자열 위젯|텍스트, 텍스트 영역, 콤보 상자, 암호, 레이블, HTML, HTML 수학, 이미지, 단추|
|재생(애니메이션) 위젯|날짜 선택기, 색 편집기, 컨트롤러|
|컨테이너/레이아웃 위젯|상자, HBox, VBox, GridBox, 아코디언, 탭, 누적|


### <a name="known-limitations"></a>알려진 제한 사항

1. 다음 위젯은 아직 지원되지 않습니다. 아래와 같은 해당 해결 방법을 따를 수 있습니다.

   |기능|해결 방법|
   |--|--|
   |`Output` 위젯|`print()` 함수를 대신 사용하여 텍스트를 stdout으로 작성할 수 있습니다.|
   |`widgets.jslink()`|`widgets.link()` 함수를 사용하여 두 개의 유사한 위젯을 연결할 수 있습니다.|
   |`FileUpload` 위젯| 아직 지원하지 않습니다.|

2. Synapse에서 제공하는 전역 `display` 함수는 IPython `display(a, b)` 함수와 달리, 1번 호출에서 여러 위젯 표시(즉, `display`)를 지원하지 않습니다.
3. IPython 위젯이 포함된 Notebook을 닫으면 해당 셀을 다시 실행할 때까지 Notebook을 보거나 조작할 수 없습니다.


---
## <a name="save-notebooks"></a>Notebook 저장

작업 영역에 단일 Notebook 또는 모든 Notebook을 저장할 수 있습니다.

1. 단일 Notebook에 변경한 내용을 저장하려면 노트북 명령 모음에서 **게시** 단추를 선택합니다.

   ![publish-notebook 스크린샷](./media/apache-spark-development-using-notebooks/synapse-publish-notebook.png)

2. 작업 영역에 모든 노트북을 저장하려면 작업 영역 명령 모음에서 **모두 게시** 단추를 선택합니다. 

   ![publish-all 스크린샷](./media/apache-spark-development-using-notebooks/synapse-publish-all.png)

Notebook 속성에서 저장할 때 셀 출력을 포함할지 여부를 구성할 수 있습니다.

   ![notebook-properties 스크린샷](./media/apache-spark-development-using-notebooks/synapse-notebook-properties.png)

## <a name="magic-commands"></a>매직 명령
Synapse Notebook에서 익숙한 Jupyter 매직 명령을 사용할 수 있습니다. 현재 사용 가능한 매직 명령은 아래 목록에서 확인하세요. 필요에 맞는 매직 명령을 계속 빌드할 수 있도록 [GitHub에 사용 사례](https://github.com/MicrosoftDocs/azure-docs/issues/new)를 알려주세요.

> [!NOTE]
> Synapse 파이프라인에서는 %%pyspark, %%spark, %%csharp, %%sql 매직 명령만 지원됩니다. 
>
>

# <a name="classical-notebook"></a>[클래식 Notebook](#tab/classical)

사용 가능한 줄 매직: [%lsmagic](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-lsmagic), [%time](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-time), [%timeit](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-timeit)

사용 가능한 셀 매직: [%%time](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-time), [%%timeit](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-timeit), [%%capture](https://ipython.readthedocs.io/en/stable/interactive/magics.html#cellmagic-capture), [%%writefile](https://ipython.readthedocs.io/en/stable/interactive/magics.html#cellmagic-writefile), [%%sql](#use-multiple-languages), [%%pyspark](#use-multiple-languages), [%%spark](#use-multiple-languages), [%%csharp](#use-multiple-languages),[%%configure](#spark-session-config-magic-command)



# <a name="preview-notebook"></a>[프리뷰 Notebook](#tab/preview)

사용 가능한 줄 매직: [%lsmagic](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-lsmagic), [%time](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-time), [%timeit](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-timeit), [%history](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-history), [%run](#notebook-reference), [%load](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-load)

사용 가능한 셀 매직: [%%time](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-time), [%%timeit](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-timeit), [%%capture](https://ipython.readthedocs.io/en/stable/interactive/magics.html#cellmagic-capture), [%%writefile](https://ipython.readthedocs.io/en/stable/interactive/magics.html#cellmagic-writefile), [%%sql](#use-multiple-languages), [%%pyspark](#use-multiple-languages), [%%spark](#use-multiple-languages), [%%csharp](#use-multiple-languages), [%%html](https://ipython.readthedocs.io/en/stable/interactive/magics.html#cellmagic-html), [%%configure](#spark-session-config-magic-command)

--- 

## <a name="integrate-a-notebook"></a>Notebook 통합

### <a name="add-a-notebook-to-a-pipeline"></a>파이프라인에 Notebook 추가

오른쪽 위 모서리에서 **파이프라인에 추가** 단추를 선택하여 기존 파이프라인에 Notebook을 추가하거나 새 파이프라인을 만듭니다.

![파이프라인에 Notebook 추가 스크린샷](./media/apache-spark-development-using-notebooks/add-to-pipeline.png)

### <a name="designate-a-parameters-cell"></a>매개 변수 셀 지정

# <a name="classical-notebook"></a>[클래식 Notebook](#tab/classical)

Notebook을 매개 변수화하려면 줄임표(...)를 선택하여 오른쪽 끝에 있는 다른 셀 작업 메뉴에 액세스하면 됩니다. 그런 다음, **매개 변수 셀 설정/해제** 를 선택하여 셀을 매개 변수 셀로 지정합니다.

![toggle-parameter 스크린샷](./media/apache-spark-development-using-notebooks/toggle-parameter-cell.png)

# <a name="preview-notebook"></a>[프리뷰 Notebook](#tab/preview)

Notebook을 매개 변수화하려면 줄임표(...)를 선택하여 셀 도구 모음에서 **기타 명령** 에 액세스합니다. 그런 다음, **매개 변수 셀 설정/해제** 를 선택하여 셀을 매개 변수 셀로 지정합니다.

![azure-notebook-toggle-parameter 스크린샷](./media/apache-spark-development-using-notebooks/azure-notebook-toggle-parameter-cell.png)

---

Azure Data Factory는 매개 변수 셀을 찾고 실행 시 전달되는 매개 변수의 기본값으로 이 셀을 처리합니다. 실행 엔진이 기본값을 덮어쓰기 위해 입력 매개 변수를 사용하여 매개 변수 셀 아래에 새 셀을 추가합니다. 


### <a name="assign-parameters-values-from-a-pipeline"></a>파이프라인에서 매개 변수 값 할당

매개 변수를 사용하여 Notebook을 만든 후에는 Synapse Notebook 활동을 사용하여 파이프라인에서 이를 실행할 수 있습니다. 파이프라인 캔버스에 활동을 추가한 후에는 **설정** 탭의 **기본 매개 변수** 섹션에서 매개 변수 값을 설정할 수 있습니다. 

![매개 변수 할당 스크린샷](./media/apache-spark-development-using-notebooks/assign-parameter.png)

매개 변수 값을 할당하는 경우 [파이프라인 식 언어](../../data-factory/control-flow-expression-language-functions.md) 또는 [시스템 변수](../../data-factory/control-flow-system-variables.md)를 사용할 수 있습니다.



## <a name="shortcut-keys"></a>바로 가기 키

Jupyter Notebook과 마찬가지로 Synapse Notebook에는 모달 사용자 인터페이스가 있습니다. 키보드는 Notebook 셀이 있는 모드에 따라 다른 작업을 수행합니다. Synapse Notebook은 지정된 코드 셀에 대해 두 가지 모드(명령 모드 및 편집 모드)를 지원합니다.

1. 입력하라는 텍스트 커서가 없으면 셀은 명령 모드에 있습니다. 셀이 명령 모드에 있으면 Notebook을 전체적으로 편집할 수 있지만 개별 셀에는 입력할 수 없습니다. `ESC` 키를 누르거나 마우스를 사용하여 셀의 편집기 영역 외부를 선택하면 명령 모드로 전환됩니다.

   ![command-mode 스크린샷](./media/apache-spark-development-using-notebooks/synapse-command-mode-2.png)

2. 편집 모드에서는 편집기 영역에 입력하라는 텍스트 커서가 표시됩니다. 셀이 편집 모드에 있으면 셀에 입력할 수 있습니다. `Enter` 키를 누르거나 마우스를 사용하여 셀의 편집기 영역을 선택하면 편집 모드로 전환됩니다.
   
   ![edit-mode 스크린샷](./media/apache-spark-development-using-notebooks/synapse-edit-mode-2.png)

### <a name="shortcut-keys-under-command-mode"></a>명령 모드의 바로 가기 키

# <a name="classical-notebook"></a>[클래식 Notebook](#tab/classical)

다음과 같은 키 입력 바로 가기를 사용하면 Synapse Notebook에서 보다 편리하게 코드를 탐색하고 실행할 수 있습니다.

| 작업 |Synapse Notebook 바로 가기  |
|--|--|
|현재 셀을 실행하고 아래 선택 | Shift+Enter |
|현재 셀을 실행하고 아래에 삽입 | Alt+Enter |
|위 셀 선택| 위로 |
|아래 셀 선택| 아래로 |
|위에 셀 삽입| A |
|아래에 셀 삽입| b |
|선택한 셀을 위로 확장| Shift+위쪽 화살표 |
|선택한 셀을 아래로 확장| Shift+아래쪽 화살표|
|위로 셀 이동| Ctrl+Alt+↑ |
|아래로 셀 이동| Ctrl+Alt+↓ |
|선택한 셀 삭제| D, D |
|편집 모드로 전환| Enter |

# <a name="preview-notebook"></a>[프리뷰 Notebook](#tab/preview)

| 작업 |Synapse Notebook 바로 가기  |
|--|--|
|현재 셀을 실행하고 아래 선택 | Shift+Enter |
|현재 셀을 실행하고 아래에 삽입 | Alt+Enter |
|현재 셀 실행| Ctrl+Enter |
|위 셀 선택| 위로 |
|아래 셀 선택| 아래로 |
|이전 셀 선택| K |
|다음 셀 선택| J |
|위에 셀 삽입| A |
|아래에 셀 삽입| b |
|선택한 셀 삭제| Shift+D |
|편집 모드로 전환| Enter |

---

### <a name="shortcut-keys-under-edit-mode"></a>편집 모드의 바로 가기 키


편집 모드에서 다음과 같은 키 입력 바로 가기를 사용하면 Synapse Notebook에서 보다 편리하게 코드를 탐색하고 실행할 수 있습니다.

| 작업 |Synapse Notebook 바로 가기  |
|--|--|
|커서를 위로 이동 | 위로 |
|커서를 아래로 이동|아래로|
|실행 취소|Ctrl + Z|
|다시 실행|Ctrl + Y|
|주석 처리/주석 처리 제거|Ctrl + /|
|이전 단어 삭제|Ctrl + Backspace|
|다음 단어 삭제|Ctrl + Delete|
|셀 시작으로 이동|Ctrl + Home|
|셀 끝으로 이동 |Ctrl + End|
|한 단어 왼쪽으로 이동|Ctrl + Left|
|한 단어 오른쪽으로 이동|Ctrl + 오른쪽 화살표|
|모두 선택|Ctrl + A|
|들여쓰기| Ctrl +]|
|내어쓰기|Ctrl + [|
|명령 모드로 전환| Esc |

---

## <a name="next-steps"></a>다음 단계
- [Synapse 샘플 Notebooks 체크 아웃](https://github.com/Azure-Samples/Synapse/tree/master/Notebooks)
- [빠른 시작: 웹 도구를 사용하여 Azure Synapse Analytics에서 Apache Spark 풀 만들기](../quickstart-apache-spark-notebook.md)
- [Azure Synapse Analytics의 Apache Spark란?](apache-spark-overview.md)
- [Azure Synapse Analytics를 사용하여 Apache Spark에 .NET 사용](spark-dotnet.md)
- [Apache Spark용 .NET 설명서](/dotnet/spark)
- [Azure Synapse Analytics](../index.yml)
