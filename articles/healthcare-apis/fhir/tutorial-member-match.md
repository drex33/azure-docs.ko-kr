---
title: 자습서 - $member 일치 작업
description: HRex(Da Vinci Health Record Exchange)의 일부로 정의된 $member 일치 작업을 소개합니다.
services: healthcare-apis
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: tutorial
ms.reviewer: matjazl
ms.author: cavoeg
author: caitlinv39
ms.date: 08/06/2021
ms.openlocfilehash: 2771458c6c9f23c9e87691e1a74d3e29986ad482
ms.sourcegitcommit: 28cd7097390c43a73b8e45a8b4f0f540f9123a6a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/24/2021
ms.locfileid: "122778268"
---
# <a name="member-match-operation-in-fhir-service"></a>FHIR 서비스의 $member 일치 작업

> [!IMPORTANT]
> Azure Healthcare API는 현재 미리 보기로 제공됩니다. [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)에는 베타 또는 미리 보기로 제공되거나 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 추가 약관이 포함되어 있습니다.

[$member 일치는](http://hl7.org/fhir/us/davinci-hrex/2020Sep/OperationDefinition-member-match.html) HRex(Da Vinci Health Record Exchange)의 일부로 정의된 작업입니다. 이 가이드에서는 $member 일치의 내용과 사용 방법을 살펴보겠습니다.

## <a name="overview-of-member-match"></a>$member 일치 개요

$member 일치 작업은 새로운 지급자가 환자의 이전 지급자로부터 환자의 고유 식별자를 얻을 수 있도록 하여 지불자 간 데이터 교환을 돕기 위해 만들어졌습니다. $member 일치 작업을 수행하려면 요청 본문에 세 가지 정보를 전달해야 합니다.

* 환자 인구 통계
* 이전 검사 정보
* 새 검사 정보(구현에 따라 필요하지 않음)

데이터가 전달된 후 Azure Healthcare API의 FHIR 서비스(여기서는 FHIR 서비스라고 함)는 전달된 이전 검사 정보와 함께 전달된 인구 통계와 정확히 일치하는 환자를 찾을 수 있는지 확인합니다. 결과가 발견되면 응답은 원래 환자 데이터와 이전 지급자의 새 식별자 및 이전 검사 정보가 있는 번들로 제공됩니다.

> [!NOTE]
> 사양은 새 검사 정보를 전달하고 다시 전달하는 것을 설명합니다. 결과를 더 작게 유지하기 위해 해당 데이터를 생략하기로 결정했습니다.

## <a name="example-of-member-match"></a>$member 일치의 예

$member 일치를 사용하려면 다음 호출을 사용합니다.

`POST {{fhirurl}}/Patient/$member-match`

환자, 이전 검사 및 새 적용 범위를 포함하는 매개 변수 리소스를 본문에 포함해야 합니다. JSON 표현을 보려면 [$member 일치 예제 요청을 참조하세요.](http://hl7.org/fhir/us/davinci-hrex/2020Sep/Parameters-member-match-in.json.html)

단일 일치가 발견되면 다른 식별자가 추가된 200 응답을 받게 됩니다.

:::image type="content" source="media/centers-medicare-services-tutorials/two-hundred-response.png" alt-text="20000개의 응답 코드입니다.":::

$member 일치에서 고유한 일치를 찾을 수 없는 경우 오류 코드와 함께 422 응답을 받게 됩니다.

## <a name="next-steps"></a>다음 단계

이 가이드에서는 $member 일치 작업에 대해 배웠습니다. 다음으로, $member 일치 작업이 필요한 Touchstone에서 Da Vinci Payer Data Exchange IG를 테스트하는 방법을 알아볼 수 있습니다.

>[!div class="nextstepaction"]
>[DaVinci PDex](davinci-pdex-tutorial.md)
