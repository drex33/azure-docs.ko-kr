---
title: FHIR 서비스에 대해 식별 되지 않은 데이터 (미리 보기) 내보내기
description: 이 문서에서는 식별 되지 않은 내보내기를 설정 하 고 사용 하는 방법을 설명 합니다.
author: ranvijaykumar
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 9/28/2020
ms.author: ranku
ms.openlocfilehash: a7757830dfb75f7ad111913ee4a8ea41926db600
ms.sourcegitcommit: b00a2d931b0d6f1d4ea5d4127f74fc831fb0bca9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/20/2021
ms.locfileid: "132867377"
---
# <a name="exporting-de-identified-data-preview"></a>식별 되지 않은 데이터 내보내기 (미리 보기)

> [!Note] 
> 확인 되지 않은 내보내기를 사용 하는 경우의 결과는 데이터 입력 및 고객이 선택한 함수에 따라 달라 집니다. Microsoft는 확인 되지 않은 내보내기 출력을 평가 하거나 고객의 사용 사례 및 규정 준수 요구 사항에 대 한 acceptability를 확인할 수 없습니다. 식별 되지 않은 내보내기는 특정 법률, 규정 또는 규정 준수 요구 사항을 충족 하는 것이 보장 되지 않습니다.

$Export 명령을 사용 하 여 FHIR 서버에서 식별 되지 않은 데이터를 내보낼 수도 있습니다. [익명화에 대 한 Fhir 도구의](https://github.com/microsoft/FHIR-Tools-for-Anonymization)익명화 엔진을 사용 하 고 쿼리 매개 변수에서 익명화 구성 세부 정보를 가져옵니다. 사용자 고유의 익명화 config 파일을 만들거나 HIPAA 금고 Harbor 메서드에 대 한 [샘플 구성 파일](https://github.com/microsoft/Tools-for-Health-Data-Anonymization/blob/master/docs/FHIR-anonymization.md#sample-configuration-file) 을 시작 점으로 사용할 수 있습니다. 

## <a name="configuration-file"></a>구성 파일

익명화 엔진은 HIPAA 금고 Harbor 방법의 요구 사항을 충족 하는 데 도움이 되는 샘플 구성 파일을 제공 합니다. 구성 파일은 `fhirVersion` ,, `processingErrors` `fhirPathRules` ,의 4 개 섹션을 포함 하는 JSON 파일 `parameters` 입니다. 
* `fhirVersion` 익명화 엔진에 대 한 FHIR 버전을 지정 합니다.
* `processingErrors` 익명화 중에 발생할 수 있는 처리 오류에 대해 수행할 작업을 지정 합니다. 사용자의 요구에 따라 예외를 _발생_ 시키거나 _유지할_ 수 있습니다.
* `fhirPathRules` 사용할 익명화 메서드를 지정 합니다. 규칙은 구성 파일의 모양에 따라 실행 됩니다.
* `parameters`_Fhirpathrules_ 에 지정 된 익명화 동작에 대 한 규칙을 설정 합니다.

다음은 4의 샘플 구성 파일입니다.

```json
{
  "fhirVersion": "R4",
  "processingError":"raise",
  "fhirPathRules": [
    {"path": "nodesByType('Extension')", "method": "redact"},
    {"path": "Organization.identifier", "method": "keep"},
    {"path": "nodesByType('Address').country", "method": "keep"},
    {"path": "Resource.id", "method": "cryptoHash"},
    {"path": "nodesByType('Reference').reference", "method": "cryptoHash"},
    {"path": "Group.name", "method": "redact"}
  ],
  "parameters": {
    "dateShiftKey": "",
    "cryptoHashKey": "",
    "encryptKey": "",
    "enablePartialAgesForRedact": true
  }
}
```

구성 파일의 이러한 4 개 섹션 각각에 대 한 자세한 내용은 [여기](https://github.com/microsoft/Tools-for-Health-Data-Anonymization/blob/master/docs/FHIR-anonymization.md#configuration-file-format)를 확인 하세요.
## <a name="using-export-command-for-the-de-identified-data"></a>확인 되지 않은 데이터에 대 한 $export 명령 사용
 `https://<<FHIR service base URL>>/$export?_container=<<container_name>>&_anonymizationConfig=<<config file name>>&_anonymizationConfigEtag=<<ETag on storage>>`

> [!Note] 
> 현재 FHIR 서비스는 시스템 수준 ($export)에서 확인 되지 않은 내보내기를 지원 합니다.

|쿼리 매개 변수            | 예제 |옵션| 설명|
|---------------------------|---------|-----------|------------|
| _\_anonymizationConfig_   |DemoConfig. json|식별 되지 않은 내보내기에 필요 |구성 파일의 이름입니다. [여기](https://github.com/microsoft/FHIR-Tools-for-Anonymization#configuration-file-format)에서 구성 파일 형식을 참조 하세요. 이 파일은 내보내기 위치로 구성 된 동일한 Azure storage 계정 내의 **익명화** 이라는 컨테이너 내에 보관 되어야 합니다. |
| _\_anonymizationConfigEtag_|"0x8D8494A069489EC"|식별 되지 않은 내보내기의 경우 선택 사항|이는 구성 파일의 Etag입니다. blob 속성에서 Azure Storage Explorer를 사용 하 여 Etag를 가져올 수 있습니다.|

> [!IMPORTANT]
> 원시 내보내기와 식별 되지 않은 내보내기는 모두 내보내기 구성의 일부로 지정 된 동일한 Azure storage 계정에 기록 합니다. 다른 식별 되지 않은 구성에 해당 하는 다른 컨테이너를 사용 하 고 컨테이너 수준에서 사용자 액세스를 관리 하는 것이 좋습니다.
