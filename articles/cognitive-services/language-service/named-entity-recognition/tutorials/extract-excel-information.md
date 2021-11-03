---
title: Power Automate를 사용하여 Excel에서 정보 추출
titleSuffix: Azure Cognitive Services
description: 명명 된 엔터티 인식 및 Power Automate를 사용 하 여 코드를 작성 하지 않고도 Excel 텍스트를 추출 하는 방법을 알아봅니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: article
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-ner, ignite-fall-2021
ms.openlocfilehash: a53ff3e69842bcd2f42e8463f3f84638f512450f
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131053547"
---
# <a name="extract-information-in-excel-using-named-entity-recognitionner-and-power-automate"></a>NER (명명 된 엔터티 인식) 및 Power Automate를 사용 하 여 Excel에서 정보 추출 

이 자습서에서는 코드를 작성할 필요 없이 Excel 스프레드시트에서 텍스트를 추출하는 Power Automate 흐름을 만듭니다. 

이 흐름은 아파트 단지에 대해 보고된 문제를 스프레드시트에서 배관 및 기타의 두 가지 범주로 분류합니다. 또한 정보를 보낸 세입자의 이름과 전화 번호를 추출합니다. 마지막으로 이 정보를 Excel 시트에 추가합니다. 

이 자습서에서 학습할 방법은 다음과 같습니다.

> [!div class="checklist"]
> * Power Automate를 사용하여 흐름 만들기
> * 비즈니스용 OneDrive에서 Excel 데이터 업로드
> * Excel에서 텍스트를 추출 하 고 명명 된 엔터티 인식에 대해 보냅니다 (NER). 
> * API의 정보를 사용하여 Excel 시트 업데이트

## <a name="prerequisites"></a>사전 요구 사항

- Microsoft Azure 계정. [체험 계정 만들기](https://azure.microsoft.com/free/cognitive-services/) 또는 [로그인](https://portal.azure.com/).
- 언어 리소스입니다. 없는 경우 [Azure Portal에서 만들고](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics) 무료 계층을 사용하여 이 자습서를 완료할 수 있습니다.
- 등록 중에 생성된 [키 및 엔드포인트](../../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource).
- 세입자 문제가 포함된 스프레드시트. 데이터 예는 GitHub에서 제공됩니다.
- 비즈니스용 OneDrive가 포함된 Microsoft 365.

## <a name="add-the-excel-file-to-onedrive-for-business"></a>비즈니스용 OneDrive에 Excel 파일 추가

[GitHub](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/TextAnalytics/sample-data/ReportedIssues.xlsx)에서 Excel 파일 예를 다운로드합니다. 이 파일은 비즈니스용 OneDrive 계정에 저장해야 합니다.

:::image type="content" source="../media/tutorials/excel/example-data.png" alt-text="Excel 파일의 예" lightbox="../media/tutorials/excel/example-data.png":::

이러한 문제는 원시 텍스트로 보고됩니다. NER 기능을 사용 하 여 사용자 이름 및 전화 번호를 추출 합니다. 그러면 이 흐름은 설명에서 "배관"이라는 단어를 찾아 문제를 분류합니다. 

## <a name="create-a-new-power-automate-workflow"></a>새 Power Automate 워크플로 만들기

[Power Automate 사이트](https://preview.flow.microsoft.com/)로 이동하여 로그인합니다. 그런 다음 **만들기** 및 **예약된 흐름** 을 클릭합니다.

:::image type="content" source="../media/tutorials/excel/flow-creation.png" alt-text="워크플로 만들기 화면" lightbox="../media/tutorials/excel/flow-creation.png":::

**예약된 클라우드 흐름 빌드** 페이지에서 다음 필드를 사용하여 흐름을 초기화합니다.

|필드 |값  |
|---------|---------|
|**흐름 이름**     | **예약된 검토** 또는 다른 이름입니다.         |
|**시작 중**     |  현재 날짜 및 시간을 입력합니다.       |
|**반복 간격**     | **1시간**        |

## <a name="add-variables-to-the-flow"></a>흐름에 변수 추가

Excel 파일에 추가될 정보를 나타내는 변수를 만듭니다. **새 단계** 를 클릭하고 **변수 초기화** 를 검색합니다. 이 작업을 4번 수행하여 4개의 변수를 만듭니다.

:::image type="content" source="../media/tutorials/excel/initialize-variables.png" alt-text="변수 초기화 단계" lightbox="../media/tutorials/excel/initialize-variables.png":::


만든 변수에 다음 정보를 추가합니다. Excel 파일의 열을 나타냅니다. 축소된 변수가 있으면 해당 변수를 클릭하여 확장할 수 있습니다.

| 작업 |속성   | Type | 값 |
|---------|---------|---|---|
| 변수 초기화 | var_person | String | 사람 |
| 변수 2 초기화 | var_phone | String | 전화 번호 |
| 변수 3 초기화 | var_plumbing | String | plumbing |
| 변수 4 초기화 | var_other | String | 기타 | 

:::image type="content" source="../media/tutorials/excel/flow-variables.png" alt-text="흐름 변수에 포함된 정보" lightbox="../media/tutorials/excel/flow-variables.png":::

## <a name="read-the-excel-file"></a>Excel 파일 읽기

**새 단계** 를 클릭하고 **Excel** 을 입력한 다음 작업 목록에서 **테이블에 있는 행 나열** 을 선택합니다.

:::image type="content" source="../media/tutorials/excel/list-excel-rows.png" alt-text="흐름에 Excel 행 추가" lightbox="../media/tutorials/excel/list-excel-rows.png":::

이 작업의 필드를 채워 Excel 파일을 흐름에 추가합니다. 이 자습서에서 설명한 대로 파일을 비즈니스용 OneDrive에 업로드해야 합니다.

:::image type="content" source="../media/tutorials/excel/list-excel-rows-options.png" alt-text="흐름에서 Excel 행 채우기" lightbox="../media/tutorials/excel/list-excel-rows-options.png":::

**새 단계** 를 클릭하고 **각각에 적용** 작업을 추가합니다.

:::image type="content" source="../media/tutorials/excel/add-apply-action.png" alt-text="각 작업에 적용 추가" lightbox="../media/tutorials/excel/add-apply-action.png":::

**이전 단계에서 출력 선택** 을 클릭합니다. 동적 콘텐츠 상자가 나타나면 **값** 을 선택합니다.

:::image type="content" source="../media/tutorials/excel/select-output.png" alt-text="Excel 파일에서 출력 선택" lightbox="../media/tutorials/excel/select-output.png":::

## <a name="send-a-request-for-entity-recognition"></a>엔터티 인식 요청 보내기

아직 수행 하지 않은 경우 Azure Portal에서 [언어 리소스](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics) 를 만들어야 합니다.

### <a name="create-a-language-service-connection"></a>언어 서비스 연결 만들기

**각각에 적용** 에서 **작업 추가** 를 클릭합니다. Azure Portal에서 언어 리소스의 **키 및 끝점** 페이지로 이동 하 고 언어 리소스에 대 한 키 및 끝점을 가져옵니다.

흐름에서 다음 정보를 입력 하 여 새 언어 연결을 만듭니다.

> [!NOTE]
> 이미 언어 연결을 만든 경우 연결 정보를 변경 하려면 오른쪽 위 모서리에서 줄임표를 클릭 하 고 **+ 새 연결 추가** 를 클릭 합니다.

| 필드           | 값                                                                                                             |
|-----------------|-------------------------------------------------------------------------------------------------------------------|
| 연결 이름 | 언어 리소스에 대 한 연결의 이름입니다. 예들 들어 `TAforPowerAutomate`입니다. |
| 계정 키     | 언어 리소스의 키입니다.                                                                                   |
| 사이트 URL        | 언어 리소스의 끝점입니다.                                                       |

:::image type="content" source="../media/tutorials/excel/add-credentials.png" alt-text="흐름에 언어 리소스 자격 증명 추가" lightbox="../media/tutorials/excel/add-credentials.png":::


## <a name="extract-the-excel-content"></a>Excel 콘텐츠 추출 

연결이 만들어지면 **Text Analytics** 를 검색하고 **명명된 엔터티 인식** 을 선택합니다. 이렇게 하면 문제의 설명 열에서 정보가 추출됩니다.

:::image type="content" source="../media/tutorials/excel/extract-info.png" alt-text="Excel 시트에서 엔터티 추출" lightbox="../media/tutorials/excel/extract-info.png":::

**텍스트** 필드를 클릭하고 표시되는 동적 콘텐츠 창에서 **설명** 을 선택합니다. 언어에 `en`을 입력하고 문서 ID로 고유한 이름을 입력합니다(**고급 옵션 표시** 를 클릭해야 할 수도 있음).

:::image type="content" source="../media/tutorials/excel/description-from-dynamic-content.png" alt-text="Excel 시트에서 설명 열 텍스트 가져오기" lightbox="../media/tutorials/excel/description-from-dynamic-content.png":::


**각각에 적용** 에서 **작업 추가** 를 클릭하고 **각각에 적용** 작업을 하나 더 만듭니다. 텍스트 상자 내부를 클릭하고 표시되는 동적 콘텐츠 창에서 **문서** 를 선택합니다.

:::image type="content" source="../media/tutorials/excel/apply-to-each-documents.png" alt-text="각 작업에 다른 적용을 만듭니다." lightbox="../media/tutorials/excel/apply-to-each-documents.png":::


## <a name="extract-the-person-name"></a>사람 이름 추출

다음으로, NER 출력에서 person 엔터티 형식을 찾습니다. **각각에 적용 2** 에서 **작업 추가** 를 클릭하고 **각각에 적용** 작업을 하나 더 만듭니다. 텍스트 상자 내부를 클릭하고 표시되는 동적 콘텐츠 창에서 **엔터티** 를 선택합니다.

:::image type="content" source="../media/tutorials/excel/add-apply-action-2.png" alt-text="NER 출력에서 사람 엔터티 찾기" lightbox="../media/tutorials/excel/add-apply-action-2.png":::


새로 만든 **각각에 적용 3** 작업에서 **작업 추가** 를 클릭하고 **조건** 컨트롤을 추가합니다.

:::image type="content" source="../media/tutorials/excel/create-condition.png" alt-text="각각에 적용 3 작업에 조건 컨트롤 추가" lightbox="../media/tutorials/excel/create-condition.png":::


조건 창에서 첫 번째 텍스트 상자를 클릭합니다. 동적 콘텐츠 창에서 **카테코리** 를 검색하여 선택합니다.

:::image type="content" source="../media/tutorials/excel/choose-entities-value.png" alt-text="컨트롤 조건에 범주 추가" lightbox="../media/tutorials/excel/choose-entities-value.png":::


두 번째 상자가 **같음** 으로 설정되어 있는지 확인합니다. 그런 다음 세 번째 상자를 선택하고 동적 콘텐츠 창에서 `var_person`을 검색합니다. 

:::image type="content" source="../media/tutorials/excel/choose-variable-value.png" alt-text="person 변수 추가" lightbox="../media/tutorials/excel/choose-variable-value.png":::


**예인 경우** 조건에서 Excel을 입력한 다음 **행 업데이트** 를 선택합니다.

:::image type="content" source="../media/tutorials/excel/yes-column-action.png" alt-text="예 조건 업데이트" lightbox="../media/tutorials/excel/yes-column-action.png":::

Excel 정보를 입력하고 **키 열**, **키 값** 및 **PersonName** 필드를 업데이트합니다. 그러면 API에서 검색한 이름이 Excel 시트에 추가됩니다. 

:::image type="content" source="../media/tutorials/excel/yes-column-action-options.png" alt-text="Excel 정보 추가" lightbox="../media/tutorials/excel/yes-column-action-options.png":::

## <a name="get-the-phone-number"></a>전화 번호 가져오기

이름을 클릭하여 **각각에 적용 3** 작업을 최소화합니다. 그런 다음, 이전과 같이 다른 **각각에 적용** 작업에 **각각에 적용 2** 를 추가합니다. 이름이 **각각에 적용 4** 로 지정됩니다. 텍스트 상자를 선택하고 이 작업에 대한 출력으로 **엔터티** 를 추가합니다. 

:::image type="content" source="../media/tutorials/excel/add-apply-action-phone.png" alt-text="NER 출력의 엔터티를 각 작업에 다른 적용에 추가합니다." lightbox="../media/tutorials/excel/add-apply-action-phone.png":::

**각각에 적용 4** 에서 **조건** 컨트롤을 추가합니다. **조건 2** 로 이름이 지정됩니다. 첫 번째 텍스트 상자의 동적 콘텐츠 창에서 **카테고리** 를 검색하고 추가합니다. 가운데 상자가 **같음** 으로 설정되어 있는지 확인합니다. 그런 다음 오른쪽 텍스트 상자에 `var_phone`을 입력합니다. 

:::image type="content" source="../media/tutorials/excel/condition-2-options.png" alt-text="두 번째 조건 컨트롤 추가" lightbox="../media/tutorials/excel/condition-2-options.png":::

**예인 경우** 조건에서 **행 업데이트** 작업을 추가합니다. 그런 다음 위와 같이 Excel 시트의 전화 번호 열에 대한 정보를 입력합니다. 그러면 API가 검색한 전화 번호가 Excel 시트에 추가됩니다. 

:::image type="content" source="../media/tutorials/excel/condition-2-yes-column.png" alt-text="예 조건인 경우 두 번째에 Excel 정보 추가" lightbox="../media/tutorials/excel/condition-2-yes-column.png":::

## <a name="get-the-plumbing-issues"></a>배관 문제 가져오기

이름을 클릭하여 **각각에 적용 4** 를 최소화합니다. 그런 다음 상위 작업에서 **각각에 적용** 을 하나 더 만듭니다. 텍스트 상자를 선택하고 동적 콘텐츠 창에 **엔터티** 를 이 작업의 출력으로 추가합니다. 

:::image type="content" source="../media/tutorials/excel/add-apply-action-plumbing.png" alt-text="각 작업에 다른 적용 만들기" lightbox="../media/tutorials/excel/add-apply-action-plumbing.png":::

다음으로 흐름은 Excel 테이블 행의 문제 설명에 "배관"이라는 단어가 포함되어 있는지 확인합니다. 포함되어 있으면 IssueType 열에 "배관"이 추가됩니다. 포함되어 있지 않은 경우 "기타"를 입력합니다.

**각각에 적용 4** 작업 안에 **조건** 컨트롤을 추가합니다. 이름이 **조건 3** 으로 지정됩니다. 첫 번째 텍스트 상자에서 동적 콘텐츠 창을 사용하여 Excel 파일에서 **설명** 을 검색하고 추가합니다. 가운데 상자에 **포함** 이 표시되어 있는지 확인합니다. 그런 다음 오른쪽 텍스트 상자에서 `var_plumbing`을 찾아 선택합니다. 

:::image type="content" source="../media/tutorials/excel/condition-3-options.png" alt-text="새 조건 컨트롤 만들기" lightbox="../media/tutorials/excel/condition-3-options.png":::

**예인 경우** 조건에서 **작업 추가** 를 클릭하고 **행 업데이트** 를 선택합니다. 그런 다음 이전과 같이 정보를 입력합니다. IssueType 열에서 `var_plumbing`을 선택합니다. 이렇게 하면 행에 "배관" 레이블이 적용됩니다.

**아니요인 경우** 조건에서 **작업 추가** 를 클릭하고 **행 업데이트** 를 선택합니다. 그런 다음 이전과 같이 정보를 입력합니다. IssueType 열에서 `var_other`를 선택합니다. 이렇게 하면 행에 "기타" 레이블이 적용됩니다.

:::image type="content" source="../media/tutorials/excel/plumbing-issue-condition.png" alt-text="두 조건에 정보 추가" lightbox="../media/tutorials/excel/plumbing-issue-condition.png":::

## <a name="test-the-workflow"></a>워크플로 테스트

화면의 오른쪽 위 모서리에서 **저장**, **테스트** 를 차례로 클릭합니다. **테스트 흐름** 에서 **수동으로** 를 선택합니다. 그런 다음, **테스트**, **흐름 실행** 을 차례로 클릭합니다.

Excel 파일이 OneDrive 계정에서 업데이트됩니다. 다음과 같이 표시됩니다.

:::image type="content" source="../media/tutorials/excel/updated-excel-sheet.png" alt-text="워크플로 테스트 및 출력 보기" lightbox="../media/tutorials/excel/updated-excel-sheet.png":::

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [REST API 또는 클라이언트 라이브러리를 사용하여 NER 호출](../quickstart.md)
