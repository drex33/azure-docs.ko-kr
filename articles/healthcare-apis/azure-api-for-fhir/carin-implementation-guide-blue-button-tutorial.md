---
title: 파랑 단추에 대 한 CARIN Blue 단추 구현 가이드-FHIR 용 Azure API
description: 이 자습서에서는 FHIR 용 Azure API를 설정 하 여 Touchstone 구현 가이드 (C4BB)에 대 한 CARIN 구현 가이드를 전달 하는 단계를 안내 합니다.
services: healthcare-apis
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: tutorial
ms.reviewer: matjazl
ms.author: cavoeg
author: caitlinv39
ms.date: 11/29/2021
ms.openlocfilehash: e89cc67df87d69d447dd4110c9bca5516fd5c464
ms.sourcegitcommit: 66b6e640e2a294a7fbbdb3309b4829df526d863d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/01/2021
ms.locfileid: "133361859"
---
# <a name="carin-implementation-guide-for-blue-button174-for-azure-api-for-fhir"></a>FHIR 용 Azure API에 대 한 파랑 단추&#174;에 대 한 CARIN 구현 가이드

이 자습서에서는 [Touchstone](https://touchstone.aegis.net/touchstone/) 구현 가이드 (C4BB)에 대 한 [Carin 구현 가이드](https://build.fhir.org/ig/HL7/carin-bb/index.html) 를 전달 하기 위해 Fhir 용 Azure API를 설정 하는 과정을 안내 합니다.

## <a name="touchstone-capability-statement"></a>Touchstone 기능 문

중점적으로 다룰 첫 번째 테스트는 [C4BB IG 기능 문에](https://touchstone.aegis.net/touchstone/testdefinitions?selectedTestGrp=/FHIRSandbox/CARIN/CARIN-4-BlueButton/00-Capability&activeOnly=false&contentEntry=TEST_SCRIPTS)대해 Azure API for FHIR을 테스트 하는 것입니다. 업데이트 없이 FHIR 용 Azure API에 대해이 테스트를 실행 하는 경우 누락 된 검색 매개 변수 및 누락 된 프로필로 인해 테스트가 실패 합니다. 

### <a name="define-search-parameters"></a>검색 매개 변수 정의

C4BB IG의 일환으로 리소스에 대 한 세 개의 [새 검색 매개 변수](how-to-do-custom-search.md) 를 정의 해야 `ExplanationOfBenefit` 합니다. 이 중 두 가지는 기능 문 (형식 및 서비스-날짜)에서 테스트 되며 하나는 검색에 필요 `_include` 합니다 (insurer).  

* [type](https://build.fhir.org/ig/HL7/carin-bb/SearchParameter-explanationofbenefit-type.json)
* [서비스-날짜](https://build.fhir.org/ig/HL7/carin-bb/SearchParameter-explanationofbenefit-service-date.json)
* [insurer](https://build.fhir.org/ig/HL7/carin-bb/SearchParameter-explanationofbenefit-insurer.json)

> [!NOTE]
> 이러한 검색 매개 변수에 대 한 원시 JSON에서는 이름이로 설정 됩니다 `ExplanationOfBenefit_<SearchParameter Name>` . Touchstone 테스트에서는 이러한 이름이 **형식**, **서비스 날짜** 및 **insurer** 이 될 것으로 예상 합니다.  
 
C4BB IG에 필요한 나머지 검색 매개 변수는 기본 사양에 의해 정의 되며 추가 업데이트 없이 FHIR 용 Azure API에서 이미 사용할 수 있습니다.
 
### <a name="store-profiles"></a>저장소 프로필

검색 매개 변수를 정의 하는 것 외에이 테스트를 전달 하기 위해 수행 해야 하는 다른 업데이트는 [필요한 프로필](validation-against-profiles.md)을 로드 하는 것입니다. C4BB IG 내에 정의 된 8 개의 프로필이 있습니다. 

* [C4BB 검사](https://build.fhir.org/ig/HL7/carin-bb/StructureDefinition-C4BB-Coverage.html) 
* [C4BB ExplanationOfBenefit Inpatient 기관](https://build.fhir.org/ig/HL7/carin-bb/StructureDefinition-C4BB-ExplanationOfBenefit-Inpatient-Institutional.html) 
* [C4BB ExplanationOfBenefit Outpatient 기관](https://build.fhir.org/ig/HL7/carin-bb/StructureDefinition-C4BB-ExplanationOfBenefit-Outpatient-Institutional.html) 
* [C4BB ExplanationOfBenefit Pharmacy](https://build.fhir.org/ig/HL7/carin-bb/StructureDefinition-C4BB-ExplanationOfBenefit-Pharmacy.html) 
* [C4BB ExplanationOfBenefit Professional NonClinician](https://build.fhir.org/ig/HL7/carin-bb/StructureDefinition-C4BB-ExplanationOfBenefit-Professional-NonClinician.html) 
* [C4BB 조직](https://build.fhir.org/ig/HL7/carin-bb/StructureDefinition-C4BB-Organization.html) 
* [C4BB 환자](https://build.fhir.org/ig/HL7/carin-bb/StructureDefinition-C4BB-Patient.html) 
* [C4BB 전문가](https://build.fhir.org/ig/HL7/carin-bb/StructureDefinition-C4BB-Practitioner.html) 

### <a name="sample-rest-file"></a>샘플 rest 파일

이러한 검색 매개 변수 및 프로필 생성을 지원 하기 위해 위에 설명 된 모든 단계를 단일 파일에 포함 하는 [샘플 http 파일이](https://github.com/microsoft/fhir-server/blob/main/docs/rest/C4BB/C4BB.http) 있습니다. 필요한 모든 프로필과 검색 매개 변수를 업로드 한 후에는 Touchstone에서 기능 문 테스트를 실행할 수 있습니다.

:::image type="content" source="media/cms-tutorials/capability-test-script-execution-results.png" alt-text="기능 테스트 스크립트 실행 결과입니다.":::

## <a name="touchstone-read-test"></a>Touchstone read 테스트

기능 설명을 테스트 한 후에는 C4BB IG에 대해 Azure API의 [읽기 기능](https://touchstone.aegis.net/touchstone/testdefinitions?selectedTestGrp=/FHIRSandbox/CARIN/CARIN-4-BlueButton/01-Read&activeOnly=false&contentEntry=TEST_SCRIPTS) 을 테스트 합니다. 이 테스트는 첫 번째 테스트에서 로드 한 8 개의 프로필에 대 한 테스트를 준수 합니다. 프로필을 준수 하는 리소스를 로드 해야 합니다. 가장 좋은 경로는 데이터베이스에 이미 있는 리소스에 대해 테스트 하는 것 이지만 리소스를 만들고 테스트 하는 데 사용할 수 있는 예제에서 끌어온 샘플 리소스를 사용 하 여 [http 파일](https://github.com/microsoft/fhir-server/blob/main/docs/rest/C4BB/C4BB_Sample_Resources.http) 을 사용할 수 있습니다.

:::image type="content" source="media/cms-tutorials/test-execution-results-touchstone.png" alt-text="Touchstone read test execution results.":::

## <a name="touchstone-eob-query-test"></a>Touchstone EOB 쿼리 테스트

검토할 다음 테스트는 [EOB 쿼리 테스트](https://touchstone.aegis.net/touchstone/testdefinitions?selectedTestGrp=/FHIRSandbox/CARIN/CARIN-4-BlueButton/02-EOBQuery&activeOnly=false&contentEntry=TEST_SCRIPTS)입니다. 읽기 테스트를 이미 완료 한 경우에는 필요한 모든 데이터를 로드 해야 합니다. 이 테스트는 `Patient` 다양 한 매개 변수를 사용 하 여 특정 및 리소스를 검색할 수 있는지 유효성을 검사 `ExplanationOfBenefit` 합니다.

:::image type="content" source="media/cms-tutorials/test-execution-touchstone-eob-query-test.png" alt-text="Touchstone EOB 쿼리 실행 결과.":::

## <a name="touchstone-error-handling-test"></a>Touchstone 오류 처리 테스트

살펴볼 최종 테스트는 [오류 처리](https://touchstone.aegis.net/touchstone/testdefinitions?selectedTestGrp=/FHIRSandbox/CARIN/CARIN-4-BlueButton/99-ErrorHandling&activeOnly=false&contentEntry=TEST_SCRIPTS)를 테스트 하는 것입니다. 데이터베이스에서 ExplanationOfBenefit 리소스를 삭제 하 고 테스트에서 삭제 된 리소스의 ID를 사용 하기만 하면 됩니다 `ExplanationOfBenefit` .

:::image type="content" source="media/cms-tutorials/test-execution-touchstone-error-handling.png" alt-text="Touchstone EOB 오류 처리 결과.":::


## <a name="next-steps"></a>다음 단계

이 자습서에서는 Touchstone에서 파랑 단추 테스트에 대 한 CARIN을 전달 하는 방법을 살펴보았습니다. 다음으로 Da 다빈치 formulary 테스트를 테스트 하는 방법을 검토할 수 있습니다.

>[!div class="nextstepaction"]
>[Davinci.txt 약품 Formulary](davinci-drug-formulary-tutorial.md)       
 
