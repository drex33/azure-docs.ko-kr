---
title: 자습서 - Da Vinci PDex - Azure Healthcare API(미리 보기)
description: 이 자습서에서는 Da Vinci Payer Data Exchange 구현 가이드에 대한 테스트를 통과하도록 FHIR 서비스를 설정하는 방법을 안내합니다.
services: healthcare-apis
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: tutorial
ms.author: cavoeg
author: caitlinv39
ms.date: 11/12/2021
ms.openlocfilehash: 12f7cbac225cfe4e85d2df21c363d4c08fb0b4c6
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/15/2021
ms.locfileid: "132494458"
---
# <a name="da-vinci-pdex"></a>Da Vinci PDex

> [!IMPORTANT]
> Azure Healthcare API는 현재 미리 보기로 제공됩니다. [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)에는 베타 또는 미리 보기로 제공되거나 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 추가 약관이 포함되어 있습니다.

이 자습서에서는 Azure Healthcare API(여기서는 FHIR 서비스라고 함)에서 FHIR 서비스를 설정하여 [Da Vinci Payer 데이터 Exchange 구현](http://hl7.org/fhir/us/davinci-pdex/toc.html) 가이드(PDex IG)에 대한 [Touchstone](https://touchstone.aegis.net/touchstone/) 테스트를 통과하는 방법을 안내합니다.

> [!NOTE]
> FHIR 서비스는 JSON만 지원합니다. Microsoft 오픈 소스 FHIR 서비스는 JSON과 XML을 모두 지원하며 오픈 소스에서는 _format 매개 변수를 사용하여 XML 기능 문을 볼 수 있습니다. `GET {fhirurl}/metadata?_format=xml`

## <a name="touchstone-capability-statement"></a>Touchstone 기능 문

중점적인 첫 번째 테스트 집합은 PDex IG 기능 문에 대해 FHIR 서비스를 테스트하는 것입니다. 여기에는 세 가지 테스트가 포함됩니다.

* 첫 번째 테스트는 IG 요구 사항에 대해 기본 기능 명령문의 유효성을 검사하고 업데이트 없이 통과합니다.

* 두 번째 테스트는 US Core에 대해 추가된 모든 프로필의 유효성을 검사합니다. 이 테스트는 업데이트 없이 통과하지만 많은 경고가 포함됩니다. 이러한 경고를 제거하려면 US Core [프로필을 로드해야 합니다.](validation-against-profiles.md) 모든 프로필을 만드는 데 안내하는 [샘플 HTTP 파일을](https://github.com/microsoft/fhir-server/blob/main/docs/rest/PayerDataExchange/USCore.http) 만들었습니다. 최신 [버전이](http://hl7.org/fhir/us/core/STU3.1.1/profiles.html#profiles) 있는 HL7 사이트에서 직접 프로필을 얻을 수도 있습니다.

* 세 번째 테스트는 [$patient 작업이](patient-everything.md) 지원되는지 확인합니다.

:::image type="content" source="media/centers-medicare-services-tutorials/davinci-pdex-test-script-failed.png" alt-text="Da Vinci PDex를 실행하지 못했습니다.":::

## <a name="touchstone-member-match-test"></a>Touchstone $member 일치 테스트

Payer Data Exchange 섹션의 [두 번째 테스트는](https://touchstone.aegis.net/touchstone/testdefinitions?selectedTestGrp=/FHIRSandbox/DaVinci/FHIR4-0-1-Test/PDEX/PayerExchange/01-Member-Match&activeOnly=false&contentEntry=TEST_SCRIPTS) $member [일치 작업의](http://hl7.org/fhir/us/davinci-hrex/2020Sep/OperationDefinition-member-match.html)존재 을 테스트합니다. $member 일치 작업 개요 에서 [$member 일치 작업에](tutorial-member-match.md)대해 자세히 읽을 수 있습니다.

이 테스트에서는 테스트를 통과하기 위해 몇 가지 샘플 데이터를 로드해야 합니다. 테스트에 필요한 환자 및 검사가 연결된 rest 파일이 [있습니다.](https://github.com/microsoft/fhir-server/blob/main/docs/rest/PayerDataExchange/membermatch.http) 이 데이터가 로드되면 이 테스트를 성공적으로 통과할 수 있습니다. 데이터가 로드되지 않으면 정확한 일치를 찾지 못하여 422 응답을 받게 됩니다.

:::image type="content" source="media/centers-medicare-services-tutorials/davinci-pdex-test-script-passed.png" alt-text="Da Vinci PDex 테스트 스크립트가 통과되었습니다.":::

## <a name="touchstone-patient-by-reference"></a>참조로 터치석 환자

검토할 다음 테스트는 [참조 테스트에 의한 환자입니다.](https://touchstone.aegis.net/touchstone/testdefinitions?selectedTestGrp=/FHIRSandbox/DaVinci/FHIR4-0-1-Test/PDEX/PayerExchange/02-PatientByReference&activeOnly=false&contentEntry=TEST_SCRIPTS) 이 테스트 집합은 다양한 검색 기준에 따라 환자를 찾을 수 있는지 확인합니다. 참조로 환자를 테스트하는 가장 좋은 방법은 사용자 고유의 데이터에 대해 테스트하는 것이지만, 사용하기 위해 로드할 수 있는 [샘플 리소스 파일도](https://github.com/microsoft/fhir-server/blob/main/docs/rest/PayerDataExchange/PDex_Sample_Data.http) 업로드했습니다.

:::image type="content" source="media/centers-medicare-services-tutorials/davinci-pdex-test-execution-passed.png" alt-text="Da Vinci PDex 실행이 통과되었습니다.":::

## <a name="touchstone-patienteverything-test"></a>Touchstone 환자/$everything 테스트

마지막 테스트는 환자-모든 것을 테스트하는 것입니다. 이 테스트의 경우 환자를 로드한 다음 해당 환자의 ID를 사용하여 $everything 작업을 사용하여 환자와 관련된 모든 데이터를 끌어올 수 있음을 테스트합니다.

:::image type="content" source="media/centers-medicare-services-tutorials/davinci-pdex-test-patient-everything.png" alt-text="터치석 환자/$everything 테스트가 통과되었습니다.":::

## <a name="next-steps"></a>다음 단계

이 자습서에서는 Touchstone에서 Payer Exchange 테스트를 통과하는 방법을 안내했습니다. 다음으로, Da Vinci PDEX Payer Network(Plan-Net) 구현 가이드를 테스트하는 방법을 알아볼 수 있습니다.

>[!div class="nextstepaction"]
>[Da Vinci Plan Net](davinci-plan-net.md)  
