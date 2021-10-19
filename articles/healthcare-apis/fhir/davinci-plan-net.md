---
title: 자습서 - Da Vinci Plan Net - Azure API for FHIR
description: 이 자습서에서는 Da Vinci Payer Data Exchange 구현 가이드에 대한 Touchstone 테스트를 통과하도록 Azure API for FHIR 설정하는 방법을 안내합니다.
services: healthcare-apis
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: tutorial
ms.reviewer: matjazl
ms.author: cavoeg
author: modillon
ms.date: 08/03/2021
ms.openlocfilehash: 9f45db7642128d504d908b7ca26716d2940a7f3c
ms.sourcegitcommit: 28cd7097390c43a73b8e45a8b4f0f540f9123a6a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/24/2021
ms.locfileid: "122778034"
---
# <a name="da-vinci-plan-net"></a>Da Vinci Plan Net

> [!IMPORTANT]
> Azure Healthcare API는 현재 미리 보기로 제공됩니다. [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)에는 베타 또는 미리 보기로 제공되거나 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 추가 약관이 포함되어 있습니다.

이 자습서에서는 Azure Healthcare API(여기서는 FHIR 서비스라고 함)에서 FHIR 서비스를 설정하여 Da Vinci PDEX Payer Network(Plan-Net) 구현 가이드에 대한 [Touchstone](https://touchstone.aegis.net/touchstone/) 테스트를 통과하는 방법을 안내합니다.

## <a name="touchstone-capability-statement"></a>Touchstone 기능 문

집중해야 할 첫 번째 테스트는 [Da Vinci Plan-Net 기능 문에](https://touchstone.aegis.net/touchstone/testdefinitions?selectedTestGrp=/FHIRSandbox/DaVinci/FHIR4-0-1-Test/PDEX/PlanNet/00-Capability&activeOnly=false&contentEntry=TEST_SCRIPTS)대해 FHIR 서비스를 테스트하는 것입니다. 업데이트 없이 이 테스트를 실행하면 누락된 검색 매개 변수 및 누락된 프로필로 인해 테스트가 실패합니다.

## <a name="define-search-parameters"></a>검색 매개 변수 정의

Da Vinci Plan-Net IG의 일부로 의료 서비스, 보험 계획, 전문가 역할, 조직 및 조직 소속 리소스에 대한 6개의 [새 검색 매개 변수를](how-to-do-custom-search.md) 정의해야 합니다. 이 중 6개는 모두 capability 문에서 테스트됩니다.

* [의료 서비스 적용 범위 영역](http://hl7.org/fhir/us/davinci-pdex-plan-net/STU1/SearchParameter-healthcareservice-coverage-area.html)
* [보험 플랜 적용 범위](http://hl7.org/fhir/us/davinci-pdex-plan-net/STU1/SearchParameter-insuranceplan-coverage-area.html)
* [보험 플랜 플랜 유형](http://hl7.org/fhir/us/davinci-pdex-plan-net/STU1/SearchParameter-insuranceplan-plan-type.html)
* [조직 적용 범위 영역](http://hl7.org/fhir/us/davinci-pdex-plan-net/STU1/SearchParameter-organization-coverage-area.html)
* [조직 소속 네트워크](http://hl7.org/fhir/us/davinci-pdex-plan-net/STU1/SearchParameter-organizationaffiliation-network.html)
* [전문가 역할 네트워크](http://hl7.org/fhir/us/davinci-pdex-plan-net/STU1/SearchParameter-practitionerrole-network.html)

> [!NOTE]
> 이러한 검색 매개 변수에 대한 원시 JSON에서 이름은 로 `Plannet_sp_<Resource Name>_<SearchParameter Name>` 설정됩니다. Touchstone 테스트에서는 이러한 이름이 `SearchParameter Name` (검사 영역, 계획 유형 또는 네트워크)만 될 것으로 예상합니다.

Da Vinci Plan-Net IG에 필요한 나머지 검색 매개 변수는 기본 사양에 의해 정의되며 추가 업데이트 없이 FHIR 서비스에서 이미 사용할 수 있습니다.

## <a name="store-profiles"></a>프로필 저장

검색 매개 변수를 정의하는 것 외에 이 테스트를 통과하려면 [필요한 프로필 및 확장을](./validation-against-profiles.md#storing-profiles) 로드해야 합니다. Da Vinci Plan-Net IG의 일부로 사용되는 프로필은 9개입니다.

* [Plan-Net 엔드포인트](http://hl7.org/fhir/us/davinci-pdex-plan-net/STU1/StructureDefinition-plannet-Endpoint.html)
* [Plan-Net Healthcare Service](http://hl7.org/fhir/us/davinci-pdex-plan-net/STU1/StructureDefinition-plannet-HealthcareService.html)
* [Plan-Net InsurancePlan](http://hl7.org/fhir/us/davinci-pdex-plan-net/STU1/StructureDefinition-plannet-InsurancePlan.html) 
* [Plan-Net 위치](http://hl7.org/fhir/us/davinci-pdex-plan-net/STU1/StructureDefinition-plannet-Location.html)
* [Plan-Net 네트워크](http://hl7.org/fhir/us/davinci-pdex-plan-net/STU1/StructureDefinition-plannet-Network.html)
* [Plan-Net 조직](http://hl7.org/fhir/us/davinci-pdex-plan-net/STU1/StructureDefinition-plannet-Organization.html)
* [Plan-Net OrganizationAffiliation](http://hl7.org/fhir/us/davinci-pdex-plan-net/STU1/StructureDefinition-plannet-OrganizationAffiliation.html)
* [Plan-Net 전문가](http://hl7.org/fhir/us/davinci-pdex-plan-net/STU1/StructureDefinition-plannet-Practitioner.html)
* [Plan-Net 한정자역할](http://hl7.org/fhir/us/davinci-pdex-plan-net/STU1/StructureDefinition-plannet-PractitionerRole.html)

## <a name="sample-rest-file"></a>샘플 rest 파일

이러한 검색 매개 변수 및 프로필 생성을 지원하기 위해 위에 설명된 모든 단계를 단일 파일에 포함하는 샘플 http 파일이 오픈 소스 사이트에 있습니다. 필요한 프로필 및 검색 매개 변수를 모두 업로드한 후에는 Touchstone에서 capability 문 테스트를 실행할 수 있습니다.

:::image type="content" source="media/davinci-plan-net/davinci-plan-net-test-script-execution-passed.png" alt-text="Da Vinci 계획 net sample rest test execution script passed":::

## <a name="touchstone-error-handling-test"></a>Touchstone 오류 처리 테스트

살펴보겠습니다. 두 번째 테스트는 [오류 처리를 테스트하는 것입니다.](https://touchstone.aegis.net/touchstone/testdefinitions?selectedTestGrp=/FHIRSandbox/DaVinci/FHIR4-0-1-Test/PDEX/PlanNet/01-Error-Codes&activeOnly=false&contentEntry=TEST_SCRIPTS) 수행해야 하는 유일한 단계는 데이터베이스에서 HealthcareService 리소스를 삭제하고 테스트에서 삭제된 HealthcareService 리소스의 ID를 사용하는 것입니다. 오픈 소스 사이트의 샘플 [DaVinci_PlanNet.http](https://github.com/microsoft/fhir-server/blob/main/docs/rest/DaVinciPlanNet/DaVinci_PlanNet.http) 파일은 이 단계에 대한 게시 및 삭제 예제 HealthcareService를 제공합니다.

:::image type="content" source="media/davinci-plan-net/davinci-test-script-execution-passed.png" alt-text="Da Vinci plan net touchstone error test execution script passed":::

## <a name="touchstone-query-test"></a>Touchstone 쿼리 테스트

다음 테스트는 [쿼리 기능 테스트입니다.](https://touchstone.aegis.net/touchstone/testdefinitions?selectedTestGrp=/FHIRSandbox/DaVinci/FHIR4-0-1-Test/PDEX/PlanNet/03-Query&activeOnly=false&contentEntry=TEST_SCRIPTS) 이 테스트는 첫 번째 테스트에서 로드한 프로필에 대한 규격을 테스트합니다. 프로필을 준수하는 리소스가 로드되어 있어야 합니다. 가장 좋은 경로는 데이터베이스에 이미 있는 리소스에 대해 테스트하는 것이지만, 리소스를 만들고 테스트하는 데 사용할 수 있는 IG의 예제에서 가져온 샘플 리소스가 있는 [DaVinci_PlanNet_Sample_Resources.http](https://github.com/microsoft/fhir-server/blob/main/docs/rest/DaVinciPlanNet/DaVinci_PlanNet_Sample_Resources.http) 파일도 있습니다.  

:::image type="content" source="media/davinci-plan-net/touchstone-query-test-execution-failed.png" alt-text="Da Vinci 계획 순 쿼리 테스트 실패":::

> [!NOTE]
> 샘플 리소스를 제공하면 쿼리 테스트의 성공률이 98%로 예상됩니다.

> * FHIR 서버에 대해 열려 있는 GitHub 문제로 인해 이러한 테스트 중 하나가 실패합니다. [리소스가 기본 조건과 _include 조건을 모두 충족하는 경우 여러 번 반환됩니다. 문제 #2037 , microsoft/fhir-server(github.com)](https://github.com/microsoft/fhir-server/issues/2037)

## <a name="next-steps"></a>다음 단계

이 자습서에서는 Da Vinci PDEX Payer Network(Plan-Net) 구현 가이드에 대한 Touchstone 테스트를 통과하도록 Azure API for FHIR 설정하는 방법을 안내했습니다. 다음으로 모든 FHIR 서비스 기능에 대해 알아볼 수 있습니다.

>[!div class="nextstepaction"]
>[지원되는 기능](fhir-features-supported.md)
