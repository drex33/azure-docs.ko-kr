---
title: Azure Monitor Workbooks 링크 작업
description: Azure Monitor Workbooks에서 링크 작업을 사용하는 방법
ms.topic: conceptual
ms.date: 01/07/2021
ms.openlocfilehash: dd8d4b1d7aefe070f72c4087162527dadd69f09e
ms.sourcegitcommit: 147910fb817d93e0e53a36bb8d476207a2dd9e5e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/18/2021
ms.locfileid: "130129247"
---
# <a name="link-actions"></a>링크 작업

링크 작업은 통합 문서 링크 단계나 [그리드](../visualize/workbooks-grid-visualizations.md), [제목](../visualize/workbooks-tile-visualizations.md) 또는 [그래프](../visualize/workbooks-graph-visualizations.md)의 열 설정을 통해 액세스할 수 있습니다.

## <a name="general-link-actions"></a>일반 링크 작업

| 링크 작업 | 클릭에 대한 작업 |
|:------------- |:-------------|
| `Generic Details` | 속성 그리드 컨텍스트 보기에서 행 값을 표시합니다. |
| `Cell Details` | 속성 그리드 컨텍스트 보기의 셀 값을 표시합니다. 정보를 포함하는 동적 형식(예: 위치, 역할 인스턴스 등의 요청 속성이 있는 json)이 셀에 포함된 경우에 유용합니다. |
| `Url` | 셀의 값은 유효한 http url이어야 하며, 셀은 새 탭에서 해당 url을 여는 링크입니다.|

## <a name="application-insights"></a>Application Insights

| 링크 작업 | 클릭에 대한 작업 |
|:------------- |:-------------|
| `Custom Event Details` | 셀에서 사용자 지정 이벤트 ID(`itemId`)를 사용하여 Application Insights 검색 세부 정보를 엽니다. |
| `* Details` | 종속성, 예외, 페이지 보기, 요청, 추적을 제외하고 사용자 지정 이벤트 세부 정보와 유사합니다. |
| `Custom Event User Flows` | 셀의 사용자 지정 이벤트 이름에서 피벗된 Application Insights 사용자 흐름 환경을 엽니다. |
| `* User Flows` | 예외, 페이지 보기, 요청을 제외하고 사용자 지정 이벤트 사용자 흐름과 비슷합니다. |
| `User Timeline` | 셀에서 사용자 ID(`user_Id`)를 사용하여 사용자 시간 표시줄을 엽니다. |
| `Session Timeline` | 셀 값에 대한 Application Insights 검색 환경을 엽니다. 예를 들어 abc가 셀의 값이라면 텍스트 ‘abc’를 검색합니다. |

`*`은 위의 표에 대한 와일드카드를 나타냅니다

## <a name="azure-resource"></a>Azure 리소스

| 링크 작업 | 클릭에 대한 작업 |
|:------------- |:-------------|
| `ARM Deployment` | Azure Resource Manager 템플릿을 배포합니다.  이 항목을 선택하면 작성자가 열려는 Azure Resource Manager 템플릿, 템플릿에 대한 매개 변수 등을 구성할 수 있도록 추가 필드가 표시됩니다. [Azure Resource Manager 배포 링크 설정을 참조하세요](#azure-resource-manager-deployment-link-settings). |
| `Create Alert Rule` | 리소스에 대한 경고 규칙을 만듭니다.  |
| `Custom View` | 사용자 지정 보기를 엽니다. 이 항목을 선택하면 작성자가 확장 보기, 이름 보기, 보기를 여는 데 사용되는 매개 변수를 구성할 수 있는 추가 필드가 표시됩니다. [사용자 지정 보기를 참조하세요](#custom-view-link-settings). |
| `Metrics` | 메트릭 보기를 엽니다.  |
| `Resource overview` | 셀의 리소스 ID 값을 기반으로 포털에서 리소스의 보기를 엽니다. 작성자는 리소스 보기에서 특정 메뉴 항목을 여는 `submenu` 값을 선택적으로 설정할 수도 있습니다. |
| `Workbook (template)` | 통합 문서 템플릿을 엽니다.  이 항목을 선택하면 작성자가 열려는 템플릿 등을 구성할 수 있도록 추가 필드가 표시됩니다.  |

## <a name="link-settings"></a>링크 설정

링크 렌더러를 사용하는 경우 다음 설정을 사용할 수 있습니다.

![링크 설정에 대한 스크린샷](./media/workbooks-link-actions/link-settings.png)

| 설정 | 설명 |
|:------------- |:-------------|
| `View to open` | 작성자가 위에 열거된 작업 중 하나를 선택할 수 있습니다. |
| `Menu item` | “리소스 개요”를 선택하면 리소스 개요의 메뉴 항목이 열립니다. 이를 사용하여 리소스에 대한 “개요” 대신 경고 또는 활동 로그를 열 수 있습니다. 각 Azure `Resourcetype`에 대한 메뉴 항목 값은 서로 다릅니다.|
| `Link label` | 지정된 경우 이 값은 그리드 열에 표시됩니다. 이 값을 지정하지 않으면 셀의 값이 표시됩니다. 열 지도 또는 아이콘과 같이 다른 값을 표시하려면, `Link` 렌더러를 사용하지 말고 적절한 렌더러를 사용하여 `Make this item a link` 옵션을 선택합니다. |
| `Open link in Context Blade` | 지정된 경우 링크가 전체 뷰로 열리는 대신 창의 오른쪽에 팝업 항목 “컨텍스트” 보기로 열립니다. |

`Make this item a link` 옵션을 사용하는 경우 다음 설정을 사용할 수 있습니다.

| 설정 | 설명 |
|:------------- |:-------------|
| `Link value comes from` | 링크를 사용하여 셀을 렌더러로 표시하는 경우, 이 필드는 링크에 사용할 “링크” 값을 가져올 위치를 지정합니다. 이 필드는 작성자가 그리드에 있는 다른 열의 드롭다운 목록에서 선택할 수 있도록 합니다. 예를 들어 셀은 열 지도 값이 될 수 있지만 링크를 통해 행의 리소스 ID에 대한 리소스 개요가 열려야 합니다. 이 경우 `Resource Id` 필드에서 가져오는 링크 값을 설정합니다.
| `View to open` | 위와 동일. |
| `Menu item` | 위와 동일. |
| `Open link in Context Blade` | 위와 동일. |

## <a name="azure-resource-manager-deployment-link-settings"></a>Azure Resource Manager 배포 링크 설정

선택한 링크 형식이 `ARM Deployment`인 경우, 작성자가 추가 설정을 지정해야 Azure Resource Manager 배포를 열 수 있습니다. 구성에는 두 가지 주요 탭이 있습니다.

### <a name="template-settings"></a>템플릿 설정

이 섹션에서는 템플릿을 가져오는 위치 및 Azure Resource Manager 배포를 실행하는 데 사용되는 매개 변수를 정의합니다.

| 원본 | 설명 |
|:------------- |:-------------|
|`Resource group id comes from` | 리소스 ID는 배포된 리소스를 관리하는 데 사용됩니다. 구독은 배포된 리소스와 비용을 관리하는 데 사용됩니다. 리소스 그룹은 폴더처럼 모든 리소스를 구성하고 관리하는 데 사용됩니다. 이 값을 지정하지 않으면 배포가 되지 않습니다. [링크 원본](#link-sources)에서 `Cell`, `Column`, `Static Value` 또는 `Parameter`를 선택합니다.|
|`ARM template URI from` | Azure Resource Manager 템플릿 자체에 대한 URI입니다. 템플릿을 배포할 사용자가 템플릿 URI에 액세스할 수 있어야 합니다. [링크 원본](#link-sources)에서`Cell`, `Column`, `Parameter` 또는 `Static Value`를 선택합니다. 초보자들은 [템플릿 빠른 시작](https://azure.microsoft.com/resources/templates/)을 참조하세요.|
|`ARM Template Parameters` | 이 섹션에서는 위에서 정의된 템플릿 URI에 사용하는 템플릿 매개 변수를 정의합니다. 이러한 매개 변수는 실행 페이지에 템플릿을 배포하는 데 사용됩니다. 그리드에는 템플릿 URI에 정의된 이름을 사용하여 매개 변수를 채우고 고정된 빈 값으로 설정하는 데 사용할 수 있는 도구 모음 확장 단추가 있습니다. 이 옵션은 그리드에 매개 변수가 없고 템플릿 URI가 설정된 경우에만 사용할 수 있습니다. 아래쪽 섹션은 매개 변수 출력의 모양에 대한 미리 보기입니다. 최신 변경 내용으로 미리 보기를 업데이트하려면 새로 고침을 선택합니다. 매개 변수는 일반적으로 값이지만, 참조는 사용자가 액세스할 수 있는 keyvault 비밀을 가리킬 수 있는 항목입니다. <br/><br/> **템플릿 뷰어 블레이드 제한 사항** - 참조 매개 변수를 올바르게 렌더링하지 않아 null/값으로 표시되므로, 사용자가 템플릿 뷰어 탭에서 참조 매개 변수를 올바르게 배포할 수 없습니다.|

![Azure Resource Manager 템플릿 설정 스크린샷](./media/workbooks-link-actions/template-settings.png)

### <a name="ux-settings"></a>UX 설정

이 섹션에서는 Azure Resource Manager 배포를 실행하기 전에 사용자에게 표시되는 내용을 구성합니다.

| 원본 | 설명 |
|:------------- |:-------------|
|`Title from` | 보기 실행에 사용되는 제목입니다. [링크 원본](#link-sources)에서 `Cell`, `Column`, `Parameter` 또는 `Static Value`를 선택합니다.|
|`Description from` | 사용자가 템플릿을 배포하려는 경우 사용자에게 유용한 설명을 제공하는 데 사용되는 markdown 텍스트입니다. [링크 원본](#link-sources)에서`Cell`, `Column`, `Parameter` 또는 `Static Value`를 선택합니다. <br/><br/> **참고:** `Static Value`를 선택하면 다중 선 텍스트 상자가 표시됩니다. 이 텍스트 상자에서 `{paramName}`를 사용하여 매개 변수를 확인할 수 있습니다. `{columnName_column}` 같은 열 이름 뒤에 `_column`을 추가하여 열을 매개 변수로 취급할 수도 있습니다. 아래 예제 이미지에서는 `{VMName_column}`를 작성하여 열 `VMName`을 참조할 수 있습니다. 콜론 뒤의 값은 [매개 변수 포맷터](../visualize/workbooks-parameters.md#parameter-options)로, 이 경우에서는 `value`입니다.|
|`Run button text from` | Azure Resource Manager 템플릿을 배포하기 위해 실행 단추에 사용되는 레이블입니다. Azure Resource Manager 템플릿 배포를 시작하기 위해 사용자가 선택하는 항목입니다.|

![Azure Resource Manager UX 설정 스크린샷](./media/workbooks-link-actions/ux-settings.png)

이러한 구성을 설정한 후 사용자가 링크를 선택하면 UX 설정에 설명된 UX를 사용하여 보기가 열립니다. 사용자가 `Run button text from`를 선택하는 경우 [템플릿 설정](#template-settings)의 값을 사용하여 Azure Resource Manager 템플릿을 배포합니다. 템플릿 보기를 배포하기 전에 사용자가 템플릿 및 매개 변수를 검사할 템플릿 뷰어 탭을 엽니다.

![Azure Resource Manager 보기 실행 스크린샷](./media/workbooks-link-actions/run-tab.png)

## <a name="custom-view-link-settings"></a>사용자 지정 보기 링크 설정

이를 사용하여 Azure Portal에서 사용자 지정 보기를 열 수 있습니다. 모든 구성 및 설정을 확인합니다. 잘못된 값은 포털에서 오류를 발생시키거나 보기를 올바르게 열지 못합니다. 설정을 구성하는 방법에는 `Form` 또는 `URL` 두 가지가 있습니다.

> [!NOTE]
> 메뉴를 포함하는 보기는 컨텍스트 탭에서 열 수 없습니다. 메뉴를 포함하는 보기가 컨텍스트 탭에서 열리도록 구성된 경우 링크를 선택하면 컨텍스트 탭이 표시되지 않습니다.

### <a name="form"></a>Form

| 원본 | 설명 |
|:------------- |:-------------|
|`Extension name` | 보기의 이름을 호스트하는 확장의 이름입니다.|
|`View name` | 열려는 보기의 이름입니다.|

#### <a name="view-inputs"></a>보기 입력

입력에는 그리드와 JSON이라는 두 가지 형식이 있습니다. 단순 키 및 값 탭 입력에는 `Grid`를 사용하고 중첩된 JSON 입력을 지정하려면 `JSON`을 선택합니다.

- 그리드
    - `Parameter Name`: 보기 입력 매개 변수의 이름입니다.
    - `Parameter Comes From`: 보기 매개 변수 값을 가져오는 위치입니다. [링크 원본](#link-sources)에서 `Cell`, `Column`, `Parameter` 또는 `Static Value`를 선택합니다.
    > [!NOTE]
    > `Static Value`를 선택하면 텍스트 상자에서 대괄호 링크 `{paramName}`를 사용하여 매개 변수를 확인할 수 있습니다. `{columnName_column}` 같은 열 이름 뒤에 `_column`을 추가하여 열을 매개 변수 열로 처리할 수 있습니다.

    - `Parameter Value`: `Parameter Comes From`에 따라 사용 가능한 매개 변수, 열 또는 고정된 값의 드롭다운이 됩니다.

    ![양식의 사용자 지정 보기 설정을 표시한 열 편집 설정의 스크린샷.](./media/workbooks-link-actions/custom-tab-settings.png)
- JSON
    - 편집기에서 JSON 형식으로 탭 입력을 지정합니다. `Grid` 모드처럼 매개 변수에는 `{paramName}`, 열에는 `{columnName_column}`를 사용하여 매개 변수 및 열을 참조할 수 있습니다. `Show JSON Sample`를 선택하면 확인된 모든 매개 변수 및 사용자 열에 대한 보기 입력의 예상 출력이 표시됩니다.

    ![JSON에 대한 보기 입력으로 사용자 지정 보기 설정을 여는 방법을 보여 주는 스크린샷.](./media/workbooks-link-actions/custom-tab-json.png)

### <a name="url"></a>URL

확장, 보기의 이름, 보기를 여는 데 필요한 모든 입력을 포함하는 포털 URL을 붙여넣습니다. `Initialize Settings`을 선택한 후에는 작성자가 보기 입력을 추가/수정/제거하도록 `Form`을 채웁니다.

![URL의 사용자 지정 보기 설정이 표시된 열 설정을 보여 주는 스크린샷. ](./media/workbooks-link-actions/custom-tab-settings-url.png)

## <a name="workbook-template-link-settings"></a>통합 문서(템플릿) 링크 설정

선택한 링크 형식이 `Workbook (Template)`인 경우 작성자는 추가 설정을 지정하여 올바른 통합 문서 템플릿을 열어야 합니다. 아래 설정에는 각 설정에 대한 적절한 값을 그리드에서 찾는 방법에 대한 옵션이 있습니다.

| 설정 | 설명 |
|:------------- |:-------------|
| `Workbook owner Resource Id` | 통합 문서를 “소유”하는 Azure Resource의 리소스 ID입니다. 일반적으로 Application Insights 리소스 또는 Log Analytics 작업 영역입니다. Azure Monitor 내에서 리터럴 문자열 `"Azure Monitor"`일 수도 있습니다. 통합 문서가 저장되면 통합 문서가 여기로 연결됩니다. |
| `Workbook resources` | 통합 문서에 사용되는 기본 리소스를 지정하는 Azure Resource ID의 배열입니다. 예를 들어 열려는 템플릿에 가상 머신 메트릭이 표시되는 경우, 여기에 있는 값은 가상 머신 리소스 ID입니다.  소유자 및 리소스는 여러 번 동일한 설정으로 설정됩니다. |
| `Template Id` | 템플릿에서 열려는 ID를 지정하세요. 가장 일반적인 갤러리의 커뮤니티 템플릿인 경우 `Workbooks/Performance/Apdex` 템플릿에 `Community-Workbooks/Performance/Apdex`를 붙이듯이 템플릿의 경로에 `Community-` 접두사를 붙입니다. 저장된 통합 문서/템플릿에 대한 링크인 경우 해당 항목의 전체 Azure 리소스 ID입니다. |
| `Workbook Type` | 열려는 통합 문서 템플릿의 종류를 지정합니다. 가장 일반적인 경우는 `Default` 또는 `Workbook` 옵션을 사용하여 현재 통합 문서의 값을 사용합니다. |
| `Gallery Type` | 이렇게 해서 열리는 템플릿의 “갤러리” 보기에 표시되는 갤러리 유형을 지정합니다. 가장 일반적인 경우는 `Default` 또는 `Workbook` 옵션을 사용하여 현재 통합 문서의 값을 사용합니다. |
|`Location comes from` | 특정 통합 문서 리소스를 여는 경우에는 위치 필드를 지정해야 합니다. 위치를 지정하지 않으면 통합 문서 콘텐츠 찾기가 훨씬 느려집니다. 위치를 알고 있다면 지정하세요. 위치를 알 수 없거나 특정한 위치가 없는 템플릿을 여는 경우에는 이 필드를 “기본값”으로 그대로 둡니다.|
|`Pass specific parameters to template` | 특정 매개 변수를 템플릿에 전달하려면 선택합니다. 이 항목이 선택된 경우 지정된 매개 변수만 템플릿에 전달되고, 기타의 경우에는 현재 통합 문서의 모든 매개 변수가 템플릿에 전달되며, 이 경우 해당 매개 변수 값이 작동하려면 모든 통합 문서에서 매개 변수 *이름* 이 동일해야 합니다.|
|`Workbook Template Parameters` | 이 섹션에서는 대상 템플릿에 전달되는 매개 변수를 정의합니다. 이름은 대상 템플릿의 매개 변수 이름과 일치해야 합니다. `Cell`, `Column`, `Parameter`, `Static Value`에서 값을 선택합니다. 대상 템플릿에 해당 매개 변수를 전달하기 위해 이름 및 값은 비워 둘 수 없습니다.|

위의 각 설정에 대해, 작성자는 연결된 통합 문서의 값을 가져올 위치를 선택해야 합니다. [링크 원본](#link-sources) 참조

통합 문서 링크가 열리면 위의 설정에서 구성된 모든 값이 새 통합 문서 보기에 전달됩니다.

![통합 문서 링크 설정 스크린샷](./media/workbooks-link-actions/workbook-link-settings.png)

![통합 문서 템플릿 매개 변수 설정의 스크린샷](./media/workbooks-link-actions/workbook-template-link-settings-parameter.png)

## <a name="link-sources"></a>링크 원본

| 원본 | 설명 |
|:------------- |:-------------|
| `Cell` | 이를 통해 그리드에서 해당 셀의 값이 링크 값으로 사용됩니다 |
| `Column` | 이를 선택하면 작성자가 그리드에서 다른 열을 선택할 수 있도록 다른 필드가 표시됩니다.  행에 대한 해당 열의 값은 링크 값에 사용됩니다. 이는 일반적으로 `Template Id` 필드를 `column`로 설정하여 그리드의 각 행이 다른 템플릿을 열거나, `Workbook resources` 필드가 Azure Resource ID를 포함하는 열로 설정된 경우 다른 리소스에 대해 동일한 통합 문서 템플릿을 여는 데 사용됩니다. |
| `Parameter` | 이를 선택하면 작성자가 매개 변수를 선택할 수 있도록 다른 필드가 표시됩니다. 해당 매개 변수의 값은 링크를 클릭했을 때의 값으로 사용됩니다. |
| `Static value` | 이를 선택하면 연결된 통합 문서에서 사용될 고정된 값으로 작성자가 입력할 있도록 다른 필드가 표시됩니다. 그리드에 있는 모든 행이 필드에 대해 동일한 값을 사용하는 경우에 일반적으로 이 방식이 사용됩니다. |
| `Step` | 통합 문서의 현재 단계에 설정된 값을 사용합니다. 이는 연결된 통합 문서의 통합 문서 리소스를 현재 통합 문서가 아닌 *쿼리/메트릭 단계에서* 사용되는 것으로 설정하는 쿼리 및 메트릭 단계에서 일반적입니다. |
| `Workbook` | 현재 통합 문서에 설정된 값을 사용합니다. |
| `Default` | 값을 지정하지 않은 경우 사용될 기본값을 사용합니다. 이는 갤러리 유형에서 일반적으로, 기본 갤러리는 소유자 리소스의 유형에 의해 설정됩니다. |

## <a name="next-steps"></a>다음 단계

- 통합 문서 리소스에 대한 액세스를 [제어](../visualize/workbooks-access-control.md)하고 공유합니다.
- [통합 문서에서 그룹](../visualize/workbooks-groups.md)을 사용하는 방법을 알아봅니다.