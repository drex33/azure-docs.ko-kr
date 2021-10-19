---
title: Da 다빈치 약품 Formulary 자습서
description: 이 자습서에서는 Davinci.txt 약품 Formulary 구현 가이드에 대해 Touchstone 테스트를 전달 하도록 FHIR 서비스를 설정 하는 과정을 안내 합니다.
services: healthcare-apis
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: tutorial
ms.reviewer: matjazl
ms.author: cavoeg
author: modillon
ms.date: 08/06/2021
ms.openlocfilehash: 56cf21f960cadd54340be48deeb44b1e37470f69
ms.sourcegitcommit: 28cd7097390c43a73b8e45a8b4f0f540f9123a6a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/24/2021
ms.locfileid: "122778817"
---
# <a name="tutorial-for-da-vinci-drug-formulary"></a>Da 다빈치 약품 Formulary 자습서

> [!IMPORTANT]
> Azure 의료 Api는 현재 미리 보기로 제공 됩니다. [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)에는 베타 또는 미리 보기로 제공되거나 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 추가 약관이 포함되어 있습니다.

이 자습서에서는 Touchstone [지불자 Data Exchange US 약품 Formulary 구현 가이드](http://hl7.org/fhir/us/Davinci-drug-formulary/)에 대 한 [](https://touchstone.aegis.net/touchstone/) 테스트를 전달 하기 위해 Azure 의료 api (fhir 서비스 라고 함)에서 fhir 서비스를 설정 하는 과정을 안내 합니다.

## <a name="touchstone-capability-statement"></a>Touchstone 기능 문

중점적으로 다룰 첫 번째 테스트는 [Da 다빈치 약품 Formulary 기능 문에](https://touchstone.aegis.net/touchstone/testdefinitions?selectedTestGrp=/FHIRSandbox/DaVinci/FHIR4-0-1-Test/PDEX/Formulary/00-Capability&activeOnly=false&contentEntry=TEST_SCRIPTS)대해 FHIR 서비스를 테스트 하는 것입니다. 업데이트 하지 않고이 테스트를 실행 하면 검색 매개 변수가 누락 되 고 프로필이 누락 되어 테스트가 실패 합니다.

### <a name="define-search-parameters"></a>검색 매개 변수 정의

Da 다빈치 마약 Formulary IG의 일환으로 FormularyDrug 리소스에 대해 3 개의 [새 검색 매개 변수](how-to-do-custom-search.md) 를 정의 해야 합니다. 이러한 세 가지 모두 기능 문에서 테스트 됩니다.

* [DrugTier](http://hl7.org/fhir/us/davinci-drug-formulary/STU1.0.1/SearchParameter-DrugTier.json.html)
* [DrugPlan](http://hl7.org/fhir/us/davinci-drug-formulary/STU1.0.1/SearchParameter-DrugPlan.json.html)
* [DrugName](http://hl7.org/fhir/us/davinci-drug-formulary/STU1.0.1/SearchParameter-DrugName.json.html)

Da 다빈치 약품 Formulary IG에 필요한 나머지 검색 매개 변수는 기본 사양에 의해 정의 되며 추가 업데이트 없이 FHIR 서비스에서 이미 사용할 수 있습니다.

### <a name="store-profiles"></a>저장소 프로필

검색 매개 변수를 정의 하는 것 외에이 테스트를 전달 하기 위해 수행 해야 하는 다른 업데이트는 [필요한 프로필](validation-against-profiles.md)을 로드 하는 것입니다. Da 다빈치 약품 Formulary IG의 일부로 사용 되는 두 개의 프로필이 있습니다.

* [Formulary 약품](http://hl7.org/fhir/us/davinci-drug-formulary/STU1.0.1/StructureDefinition-usdf-FormularyDrug.html)
* [Formulary 검사 계획](http://hl7.org/fhir/us/davinci-drug-formulary/STU1.0.1/StructureDefinition-usdf-CoveragePlan.html)

### <a name="sample-rest-file"></a>샘플 rest 파일

이러한 검색 매개 변수 및 프로필 생성을 지원 하기 위해, 위에 설명 된 모든 단계를 단일 파일에 포함 하는 [Da 다빈치 Formulary](https://github.com/microsoft/fhir-server/blob/main/docs/rest/DaVinciFormulary/DaVinciFormulary.http) 샘플 HTTP 파일이 오픈 소스 사이트에 있습니다. 필요한 모든 프로필과 검색 매개 변수를 업로드 한 후에는 Touchstone에서 기능 문 테스트를 실행할 수 있습니다. 성공적으로 실행 하려면 다음을 실행 해야 합니다.

:::image type="content" source="media/centers-medicare-services-tutorials/davinci-test-script-execution.png" alt-text="Da 다빈치 테스트 스크립트 실행":::

## <a name="touchstone-query-test"></a>Touchstone 쿼리 테스트

두 번째 테스트는 [쿼리 기능](https://touchstone.aegis.net/touchstone/testdefinitions?selectedTestGrp=/FHIRSandbox/DaVinci/FHIR4-0-1-Test/PDEX/Formulary/01-Query&activeOnly=false&contentEntry=TEST_SCRIPTS)입니다. 이 테스트는 다양 한 매개 변수를 사용 하 여 특정 검사 계획 및 약품 리소스를 검색할 수 있는지 확인 합니다. 가장 좋은 경로는 데이터베이스에 이미 있는 리소스에 대해 테스트 하는 것 이지만, 리소스를 만들고 테스트 하는 데 사용할 수 있는 예제에서 가져온 샘플 리소스와 함께 사용할 수 있는 [Da VinciFormulary_Sample_Resources](https://github.com/microsoft/fhir-server/blob/main/docs/rest/DaVinciFormulary/DaVinciFormulary_Sample_Resources.http) HTTP 파일도 있습니다.

:::image type="content" source="media/centers-medicare-services-tutorials/davinci-test-execution-results.png" alt-text="Da 다빈치 테스트 실행 결과입니다.":::

## <a name="next-steps"></a>다음 단계

이 자습서에서는 Touchstone에서 미국 약품 Formulary Exchange Da 다빈치 지불자 데이터를 전달 하는 방법을 살펴보았습니다. 다음으로 Touchstone에서 Da 다빈치 PDex 구현 가이드를 테스트 하는 방법을 배울 수 있습니다.

>[!div class="nextstepaction"]
>[Da Vinci PDex](davinci-pdex-tutorial.md)