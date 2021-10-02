---
title: 매핑 데이터 흐름 문제 해결
description: Azure Data Factory의 데이터 흐름 문제를 해결하는 방법을 알아봅니다.
ms.author: makromer
author: kromerm
ms.reviewer: daperlov
ms.service: data-factory
ms.subservice: data-flows
ms.topic: troubleshooting
ms.date: 10/01/2021
ms.openlocfilehash: f41d53207eb00e4044e523e481de444a615a5d88
ms.sourcegitcommit: 7bd48cdf50509174714ecb69848a222314e06ef6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/02/2021
ms.locfileid: "129389402"
---
# <a name="troubleshoot-mapping-data-flows-in-azure-data-factory"></a>Azure Data Factory의 매핑 데이터 흐름 문제 해결

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

이 문서에서는 Azure Data Factory의 매핑 데이터 흐름에 관한 일반적인 문제 해결 방법을 살펴봅니다.  특정 [오류 메시지와](data-flow-troubleshoot-errors.md) 관련 원인 및 권장 사항은 일반적인 데이터 흐름 오류 코드 및 메시지를 참조하세요.

## <a name="miscellaneous-troubleshooting-tips"></a>기타 문제 해결 팁
- **이슈**: 예기치 않은 예외가 발생하여 실행에 실패했습니다.
    - **메시지**: 데이터 흐름 활동 실행 중: 예기치 않은 예외로 실행에 실패했습니다.
    - **원인**: 이 오류는 백 엔드 서비스 오류입니다. 작업을 다시 시도하고 디버깅 세션을 다시 시작합니다.
    - **권장 사항**: 다시 시도하고 다시 시작해도 문제가 해결되지 않으면 고객 지원에 문의하세요.

-  **이슈**: 디버그 데이터 미리 보기 중에 조인에 관한 출력 데이터가 없습니다.
    - **메시지**: 샘플링된 행이 너무 적기 때문에 발생할 수 있는 null 값이나 누락된 값이 많이 있습니다. 디버그 행 한도를 업데이트하고 데이터를 새로 고쳐 보세요.
    - **원인**: 조인 조건이 행과 일치하지 않았거나 데이터 미리 보기 중에 많은 수의 null 값이 발생했습니다.
    - **권장 사항**: **디버그 설정** 에서 원본 행 한도의 행 수를 늘립니다. 추가 데이터를 처리할 수 있을 만큼 큰 데이터 흐름 클러스터가 있는 Azure IR을 선택해야 합니다.
    
- **이슈**: 여러 줄로 된 CSV 파일이 있는 원본에서 유효성 검사 오류가 발생했습니다. 
    - **메시지**: 다음 오류 메시지 중 하나가 표시될 수 있습니다.
        - 마지막 열이 null이거나 누락되었습니다.
        - 원본에서 스키마 유효성 검사가 실패합니다.
        - 스키마 가져오기는 UX에 올바르게 표시되지 않으며 마지막 열의 이름에는 줄 바꿈 문자가 있습니다.
    - **원인**: 매핑 데이터 흐름에서 \r\n을 행 구분 기호로 사용하면 여러 줄 CSV 원본 파일이 현재 작동하지 않습니다. 캐리지 리턴 시 추가 줄이 있으면 오류가 발생할 수 있습니다. 
    - **권장 사항**: \r\n이 아닌 \n을 행 구분 기호로 사용하여 원본에서 파일을 생성합니다. 또는 복사 작업을 사용하여 행 구분 기호로 \n을 사용하도록 CSV 파일을 변환합니다.

## <a name="general-troubleshooting-guidance"></a>일반 문제 해결 지침
1. 데이터 세트 연결의 상태를 확인합니다. 각 원본 및 싱크 변환에서 사용 중인 데이터 세트마다 연결된 서비스로 이동하여 연결을 테스트합니다.
2. 데이터 흐름 디자이너에서 파일과 테이블의 연결 상태를 확인합니다. 디버그 모드로 원본 변환에서 **데이터 미리 보기** 를 선택하여 데이터에 액세스할 수 있는지 확인합니다.
3. 데이터 미리 보기에서 모든 항목이 올바르면 파이프라인 디자이너로 이동하여 파이프라인 활동에 데이터 흐름을 배치합니다. 엔드투엔드 테스트에 대해 파이프라인을 디버깅합니다.

### <a name="improvement-on-csvcdm-format-in-data-flow"></a>데이터 흐름의 CSV/CDM 형식 개선 

**Azure Data Factory V2에서 매핑 데이터 흐름의 구분된 텍스트 또는 CDM 형식** 을 사용하는 경우 **2021년 5월 1일** 부터 데이터 흐름에서 구분된 텍스트/CDM의 개선 때문에 기존 파이프라인의 동작이 변경될 수 있습니다. 

개선 전에 다음과 같은 이슈가 발생할 수 있지만, 개선 후 이슈가 수정되었습니다. 이 개선 사항이 영향을 미치는지 확인하려면 다음 콘텐츠를 읽어 보세요. 

#### <a name="scenario-1-encounter-the-unexpected-row-delimiter-issue"></a>시나리오 1: 예기치 않은 행 구분 기호 이슈 발생

 다음 조건에 해당하는 경우 영향을 받습니다.
 - 여러 줄 설정이 True 또는 CDM으로 설정된 구분된 텍스트를 원본으로 사용합니다.
 - 첫 번째 행에 128자가 넘게 있습니다. 
 - 데이터 파일의 행 구분 기호가 `\n`이 아닙니다.

 개선 전에 기본 행 구분 기호 `\n`은 구분된 텍스트 파일을 구문 분석하는 데 예기치 않게 사용될 수 있습니다. 여러 줄 설정이 True로 설정되면 행 구분 기호 설정이 무효화되고 행 구분 기호가 처음 128자를 기준으로 자동 감지되기 ​​때문입니다. 실제 행 구분 기호를 검색하지 못하면 다시 `\n`으로 대체됩니다.  

 개선 후에 세 개의 행 구분 기호, 즉 `\r`, `\n`, `\r\n` 중 하나가 작동합니다.
 
 다음 예제는 개선 후 파이프라인의 동작 변경 내용을 보여 줍니다.

 **예제**:<br/>
   다음 열의 경우:<br/>
    `C1, C2, {long first row}, C128\r\n `<br/>
    `V1, V2, {values………………….}, V128\r\n `<br/>
 
   개선 전에 `\r`은 열 값에 저장됩니다. 구문 분석된 열 결과는 다음과 같습니다.<br/>
   `C1 C2 {long first row} C128`**`\r`**<br/>
   `V1 V2 {values………………….} V128`**`\r`**<br/> 

   개선 후 구문 분석된 열 결과는 다음과 같아야 합니다.<br/>
   `C1 C2 {long first row} C128`<br/>
   `V1 V2 {values………………….} V128`<br/>
  
#### <a name="scenario-2-encounter-an-issue-of-incorrectly-reading-column-values-containing-rn"></a>시나리오 2: ‘\r\n’이 포함된 열 값을 잘못 읽는 이슈 발생

 다음 조건에 해당하는 경우 영향을 받습니다.
 - 여러 줄 설정이 True 또는 CDM으로 설정된 구분된 텍스트를 원본으로 사용합니다. 
 - 행 구분 기호는 `\r\n`입니다.

 개선 전에 열 값을 읽을 때, 열 값의 `\r\n`은 `\n`으로 잘못 바뀔 수 있습니다. 

 개선 후 열 값의 `\r\n`은 `\n`으로 바뀌지 않습니다.

 다음 예제는 개선 후 파이프라인의 동작 변경 내용을 보여 줍니다.
 
 **예제**:<br/>
  
 다음 열의 경우:<br/>
  **`"A\r\n"`**`, B, C\r\n`<br/>

 개선 전에 구문 분석된 열 결과는 다음과 같습니다.<br/>
  **`A\n`**` B C`<br/>

 개선 후 구문 분석된 열 결과는 다음과 같아야 합니다.<br/>
  **`A\r\n`**` B C`<br/>  

#### <a name="scenario-3-encounter-an-issue-of-incorrectly-writing-column-values-containing-n"></a>시나리오 3: ‘\n’이 포함된 열 값을 잘못 쓰는 이슈 발생

 다음 조건에 해당하는 경우 영향을 받습니다.
 - 구분된 텍스트를 싱크로 사용합니다.
 - 열 값에 `\n`이 포함되어 있습니다.
 - 행 구분 기호가 `\r\n`로 설정되었습니다.
 
 개선 전에 열 값을 쓸 때, 열 값의 `\n`은 `\r\n`으로 잘못 바뀔 수 있습니다. 

 개선 후 열 값의 `\n`은 `\r\n`으로 바뀌지 않습니다.
 
 다음 예제는 개선 후 파이프라인의 동작 변경 내용을 보여 줍니다.

 **예제**:<br/>

 다음 열의 경우:<br/>
 **`A\n`**` B C`<br/>

 개선 전, CSV 싱크는 다음과 같습니다.<br/>
  **`"A\r\n"`**`, B, C\r\n` <br/>

 개선 후, CSV 싱크는 다음과 같아야 합니다.<br/>
  **`"A\n"`**`, B, C\r\n`<br/>

#### <a name="scenario-4-encounter-an-issue-of-incorrectly-reading-empty-string-as-null"></a>시나리오 4: 빈 문자열을 NULL로 잘못 읽을 때 이슈 발생
 
 다음 조건에 해당하는 경우 영향을 받습니다.
 - 구분된 텍스트를 원본으로 사용합니다. 
 - NULL 값은 비어 있지 않은 값으로 설정됩니다. 
 - 열 값이 빈 문자열이며 따옴표가 없습니다. 
 
 개선 전에, 따옴표가 없는 빈 문자열의 열 값은 NULL로 읽습니다. 

 개선 후, 빈 문자열은 NULL 값으로 구문 분석되지 않습니다. 
 
 다음 예제는 개선 후 파이프라인의 동작 변경 내용을 보여 줍니다.

 **예제**:<br/>

 다음 열의 경우:<br/>
  `A, ,B, `<br/>

 개선 전에 구문 분석된 열 결과는 다음과 같습니다.<br/>
  `A null B null`<br/>

 개선 후 구문 분석된 열 결과는 다음과 같아야 합니다.<br/>
  `A "" (empty string) B "" (empty string)`<br/>

###  <a name="internal-server-errors"></a>내부 서버 오류

다음은 내부 서버 오류를 일으킬 수 있는 특정 시나리오입니다.

#### <a name="scenario-1-not-choosing-the-appropriate-compute-sizetype-and-other-factors"></a>시나리오 1: 적절한 컴퓨팅 크기/유형 및 기타 요인을 선택하지 않음

  데이터 흐름 실행의 성공 여부는 컴퓨팅 크기/유형, 처리할 원본/싱크 수, 파티션 사양, 포함된 변환, 데이터 세트 크기, 데이터 왜곡도 등의 많은 요인에 따라 달라집니다.<br/>
  
  자세한 지침은 [Integration Runtime 성능](concepts-integration-runtime-performance.md)을 참조하세요.

#### <a name="scenario-2-using-debug-sessions-with-parallel-activities"></a>시나리오 2: 병렬 작업으로 디버그 세션 사용

  파이프라인에서 ForEach와 같은 구문으로 데이터 흐름 디버그 세션을 사용하여 실행을 트리거하면 여러 병렬 실행이 동일한 클러스터에 제출될 수 있습니다. 메모리 부족 등의 리소스 문제로 인해 이러한 작업을 실행하는 동안 클러스터 오류와 관련된 문제가 발생할 수 있습니다.<br/>
  
  변경 내용을 게시한 후에 파이프라인 작업에 정의한 적절한 통합 런타임 구성으로 실행을 제출하려면 **지금 트리거** 또는 **디버그** > **작업 런타임 사용** 옵션을 선택합니다.

#### <a name="scenario-3-transient-issues"></a>시나리오 3: 일시적인 문제

  실행에 관련된 마이크로 서비스의 일시적인 문제로 인해 실행이 실패할 수 있습니다.<br/>
  
  파이프라인 작업에서 다시 시도를 구성하면 일시적인 문제로 인한 문제를 해결할 수 있습니다. 자세한 내용은 [작업 정책](concepts-pipelines-activities.md#activity-json)을 참조하세요.

## <a name="next-steps"></a>다음 단계

문제 해결에 관한 자세한 내용은 다음 리소스를 참조하세요.

- [일반적으로 데이터 흐름 오류 및 메시지 매핑](data-flow-troubleshoot-errors.md)
- [Data Factory 블로그](https://azure.microsoft.com/blog/tag/azure-data-factory/)
- [Data Factory 기능 요청](/answers/topics/azure-data-factory.html)
- [Azure 비디오](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
- [Data Factory에 대한 Stack Overflow 포럼](https://stackoverflow.com/questions/tagged/azure-data-factory)
- [Data Factory에 대한 Twitter 정보](https://twitter.com/hashtag/DataFactory)
