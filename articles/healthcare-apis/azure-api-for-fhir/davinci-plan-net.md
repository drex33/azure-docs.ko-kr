---
title: 자습서-Da 다빈치 요금제 Net-Azure API for FHIR
description: 이 자습서에서는 fhir 용 Azure API에서 fhir 서비스를 설정 하 여 Da 다빈치 지불자 데이터 Exchange 구현 가이드에 대 한 Touchstone 테스트를 전달 하는 과정을 안내 합니다.
services: healthcare-apis
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: tutorial
ms.reviewer: matjazl
ms.author: cavoeg
author: modillon
ms.date: 11/29/2021
ms.openlocfilehash: 84dd99b8712eda5cff23bd7702f1336598ab26df
ms.sourcegitcommit: 66b6e640e2a294a7fbbdb3309b4829df526d863d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/01/2021
ms.locfileid: "133361845"
---
# <a name="da-vinci-plan-net-for-azure-api-for-fhir"></a>FHIR 용 Azure API에 대 한 Da 다빈치 계획 순

이 자습서에서는 FHIR 용 Azure API에서 FHIR 서비스를 설정 하 여 Da 다빈치 PDEX 지불자 Network ( [Touchstone](https://touchstone.aegis.net/touchstone/) ) 구현 가이드에 대해 설명 합니다.

## <a name="touchstone-capability-statement"></a>Touchstone 기능 문

중점적으로 다룰 첫 번째 테스트는 [Da 다빈치 Plan-Net 기능 문에](https://touchstone.aegis.net/touchstone/testdefinitions?selectedTestGrp=/FHIRSandbox/DaVinci/FHIR4-0-1-Test/PDEX/PlanNet/00-Capability&activeOnly=false&contentEntry=TEST_SCRIPTS)대해 Azure API for FHIR을 테스트 하는 것입니다. 업데이트 하지 않고이 테스트를 실행 하면 검색 매개 변수가 누락 되 고 프로필이 누락 되어 테스트가 실패 합니다.

## <a name="define-search-parameters"></a>검색 매개 변수 정의

Da 다빈치 Plan-Net의 일부로 의료 서비스, 보험 계획, 전문가 역할, 조직 및 조직 소속 리소스에 대해 6 개의 [새 검색 매개 변수](how-to-do-custom-search.md) 를 정의 해야 합니다. 이 중 6 개는 모두 기능 문에서 테스트 됩니다.

* [의료 서비스 적용 영역](http://hl7.org/fhir/us/davinci-pdex-plan-net/STU1/SearchParameter-healthcareservice-coverage-area.html)
* [보험 계획 검사 영역](http://hl7.org/fhir/us/davinci-pdex-plan-net/STU1/SearchParameter-insuranceplan-coverage-area.html)
* [보험 요금제 계획 유형](http://hl7.org/fhir/us/davinci-pdex-plan-net/STU1/SearchParameter-insuranceplan-plan-type.html)
* [조직 검사 영역](http://hl7.org/fhir/us/davinci-pdex-plan-net/STU1/SearchParameter-organization-coverage-area.html)
* [조직 소속 네트워크](http://hl7.org/fhir/us/davinci-pdex-plan-net/STU1/SearchParameter-organizationaffiliation-network.html)
* [전문가 역할 네트워크](http://hl7.org/fhir/us/davinci-pdex-plan-net/STU1/SearchParameter-practitionerrole-network.html)

> [!NOTE]
> 이러한 검색 매개 변수에 대 한 원시 JSON에서는 이름이로 설정 됩니다 `Plannet_sp_<Resource Name>_<SearchParameter Name>` . Touchstone 테스트에서는 이러한 이름이 `SearchParameter Name` (검사 영역, 계획 유형 또는 네트워크) 인 것으로 예상 됩니다.

Da 다빈치 Plan-Net IG에 필요한 나머지 검색 매개 변수는 기본 사양에 의해 정의 되며 추가 업데이트 없이 FHIR 용 Azure API에서 이미 사용할 수 있습니다.

## <a name="store-profiles"></a>저장소 프로필

검색 매개 변수를 정의 하는 것 외에이 테스트를 통과 하기 위해 [필요한 프로필 및 확장](./validation-against-profiles.md#storing-profiles) 을 로드 해야 합니다. Da 다빈치 Plan-Net의 일부로 사용 되는 9 개의 프로필이 있습니다.

* [계획-네트워크 끝점](http://hl7.org/fhir/us/davinci-pdex-plan-net/STU1/StructureDefinition-plannet-Endpoint.html)
* [요금제-Net 의료 서비스](http://hl7.org/fhir/us/davinci-pdex-plan-net/STU1/StructureDefinition-plannet-HealthcareService.html)
* [계획-Net InsurancePlan](http://hl7.org/fhir/us/davinci-pdex-plan-net/STU1/StructureDefinition-plannet-InsurancePlan.html) 
* [계획-네트워크 위치](http://hl7.org/fhir/us/davinci-pdex-plan-net/STU1/StructureDefinition-plannet-Location.html)
* [요금제-Net Network](http://hl7.org/fhir/us/davinci-pdex-plan-net/STU1/StructureDefinition-plannet-Network.html)
* [계획-네트워크 조직](http://hl7.org/fhir/us/davinci-pdex-plan-net/STU1/StructureDefinition-plannet-Organization.html)
* [계획-Net OrganizationAffiliation](http://hl7.org/fhir/us/davinci-pdex-plan-net/STU1/StructureDefinition-plannet-OrganizationAffiliation.html)
* [계획-Net 전문가](http://hl7.org/fhir/us/davinci-pdex-plan-net/STU1/StructureDefinition-plannet-Practitioner.html)
* [계획-Net PractitionerRole](http://hl7.org/fhir/us/davinci-pdex-plan-net/STU1/StructureDefinition-plannet-PractitionerRole.html)

## <a name="sample-rest-file"></a>샘플 rest 파일

이러한 검색 매개 변수 및 프로필 생성을 지원 하기 위해, 위에 설명 된 모든 단계를 단일 파일에 포함 하는 샘플 http 파일이 오픈 소스 사이트에 있습니다. 필요한 모든 프로필과 검색 매개 변수를 업로드 한 후에는 Touchstone에서 기능 문 테스트를 실행할 수 있습니다.

:::image type="content" source="media/davinci-plan-net/davinci-plan-net-test-script-execution-passed.png" alt-text="Da 다빈치 계획 net 샘플 rest 테스트 실행 스크립트 전달":::

## <a name="touchstone-error-handling-test"></a>Touchstone 오류 처리 테스트

두 번째 테스트는 [오류 처리](https://touchstone.aegis.net/touchstone/testdefinitions?selectedTestGrp=/FHIRSandbox/DaVinci/FHIR4-0-1-Test/PDEX/PlanNet/01-Error-Codes&activeOnly=false&contentEntry=TEST_SCRIPTS)를 테스트 하는 것입니다. 데이터베이스에서 HealthcareService 리소스를 삭제 하 고 테스트에서 삭제 된 HealthcareService 리소스의 ID를 사용 해야 합니다. 오픈 소스 사이트의 샘플 [DaVinci_PlanNet. http](https://github.com/microsoft/fhir-server/blob/main/docs/rest/DaVinciPlanNet/DaVinci_PlanNet.http) 파일은이 단계에 대 한 게시 및 삭제에 대 한 예제 HealthcareService를 제공 합니다.

:::image type="content" source="media/davinci-plan-net/davinci-test-script-execution-passed.png" alt-text="Da 다빈치 계획 net touchstone 오류 테스트 실행 스크립트가 전달 되었습니다.":::

## <a name="touchstone-query-test"></a>Touchstone 쿼리 테스트

다음으로 살펴볼 테스트는 [쿼리 기능 테스트](https://touchstone.aegis.net/touchstone/testdefinitions?selectedTestGrp=/FHIRSandbox/DaVinci/FHIR4-0-1-Test/PDEX/PlanNet/03-Query&activeOnly=false&contentEntry=TEST_SCRIPTS)입니다. 이 테스트는 첫 번째 테스트에서 로드 한 프로필에 대해 테스트를 준수 합니다. 프로필을 준수 하는 리소스를 로드 해야 합니다. 가장 좋은 경로는 데이터베이스에 이미 있는 리소스에 대해 테스트 하는 것 이지만 리소스를 만들고 테스트 하는 데 사용할 수 있는 예제에서 가져온 샘플 리소스를 포함 하는 [DaVinci_PlanNet_Sample_Resources. http](https://github.com/microsoft/fhir-server/blob/main/docs/rest/DaVinciPlanNet/DaVinci_PlanNet_Sample_Resources.http) 파일도 있습니다.  

:::image type="content" source="media/davinci-plan-net/touchstone-query-test-execution-failed.png" alt-text="Da 다빈치 계획 순 쿼리 테스트 실패":::

> [!NOTE]
> 제공 된 샘플 리소스를 사용 하 여 쿼리 테스트의 98% 성공률을 예측할 수 있습니다.
> 이러한 테스트 중 하나가 실패 하 게 하는 fhir 서버에 대 한 오픈 GitHub 문제가 있습니다.
기본 조건과 _include 조건을 모두 충족 하는 경우 리소스가 여러 번 반환 됩니다. [#2037](https://github.com/microsoft/fhir-server/issues/2037) 

## <a name="next-steps"></a>다음 단계

이 자습서에서는 Da 다빈치 PDEX 지불자 Network (Touchstone) 구현 가이드에 대 한 테스트를 통과 하도록 Azure API를 설정 하는 방법을 살펴보았습니다. FHIR 용 Azure API에서 지원 되는 기능에 대 한 자세한 내용은 다음을 참조 하세요.

>[!div class="nextstepaction"]
>[지원되는 기능](fhir-features-supported.md)
