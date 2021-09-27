---
title: Azure Data Factory에서 매핑 데이터 흐름을 사용한 고정 길이 텍스트 파일 처리
description: 매핑 데이터 흐름을 사용하여 Azure Data Factory에서 고정 길이 텍스 파일을 처리하는 방법에 대해 알아보기.
author: kromerm
ms.service: data-factory
ms.subservice: data-flows
ms.topic: conceptual
ms.date: 8/18/2019
ms.author: makromer
ms.openlocfilehash: 58bd77eb815a510328c49ca7c614b0bc680a4b94
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128571431"
---
# <a name="process-fixed-length-text-files-by-using-data-factory-mapping-data-flows"></a>Data Factory 매핑 데이터 흐름을 사용하여 고정 길이 텍스트 파일 처리

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Microsoft Azure Data Factory에서 매핑 데이터 흐름을 사용하여 고정 폭 텍스트 파일에서 데이터를 변환할 수 있습니다. 다음 작업에서는 구분 기호 없이 텍스트 파일에 대한 데이터 세트를 정의한 다음 서수 위치에 따라 substring 분할을 설정합니다.

## <a name="create-a-pipeline"></a>파이프라인 만들기

1. **+새 파이프라인** 을 선택하여 새 파이프라인을 만듭니다.

2. 고정 폭 파일을 처리하는 데 사용되는 데이터 흐름 작업을 추가합니다.

    :::image type="content" source="media/data-flow/fwpipe.png" alt-text="고정 폭 파이프라인":::

3. 데이터 흐름 작업에서 **새 매핑 데이터 흐름** 을 선택합니다.

4. 원본, 파생 열, 선택 및 싱크 변환을 추가합니다.

    :::image type="content" source="media/data-flow/fw2.png" alt-text="고정 폭 데이터 흐름":::

5. 새 데이터 세트를 사용하도록 Delimited Text 형식으로 원본 변환을 구성합니다.

6. 열 구분 기호 또는 헤더를 설정하지 않습니다.

   이제 이 파일의 콘텐츠에 대한 필드 시작 지점과 길이를 설정합니다.

    ```
    1234567813572468
    1234567813572468
    1234567813572468
    1234567813572468
    1234567813572468
    1234567813572468
    1234567813572468
    1234567813572468
    1234567813572468
    1234567813572468
    1234567813572468
    1234567813572468
    1234567813572468
    ```

7. 원본 변환의 **프로젝션** 탭에 *Column_1* 라는 문자열 열이 표시됩니다.

8. 파생 열에서 새 열을 만듭니다.

9. 열에는 *col1* 과 같은 단순한 이름을 지정합니다.

10. 식 작성기에서 다음을 입력합니다.

    `substring(Column_1,1,4)`

    :::image type="content" source="media/data-flow/fwderivedcol1.png" alt-text="파생 열(derived column)":::

11. 구문 분석을 해야 하는 모든 열에 대해 10단계를 반복합니다.

12. **검사** 탭을 선택하여 생성될 새 열을 확인합니다.

    :::image type="content" source="media/data-flow/fwinspect.png" alt-text="검사":::

13. 변환 선택을 사용하여 변환에 필요하지 않은 열을 제거합니다.

    :::image type="content" source="media/data-flow/fwselect.png" alt-text="변환 선택:":::

14. 싱크를 사용하여 데이터를 폴더에 출력합니다.

    :::image type="content" source="media/data-flow/fwsink.png" alt-text="고정 폭 싱크":::

    출력은 다음과 같습니다.

    :::image type="content" source="media/data-flow/fxdoutput.png" alt-text="고정 폭 출력":::

  이제 고정 폭 데이터가 분할되어 각각 문자 4개를 사용하여 Col1, Col2, Col3, Col4 등에 할당됩니다. 위의 예제에 따라 데이터는 네 개의 열로 분할됩니다.

## <a name="next-steps"></a>다음 단계

* 매핑 데이터 흐름 [변환](concepts-data-flow-overview.md)을 사용하여 나머지 데이터 흐름 논리를 빌드합니다.
