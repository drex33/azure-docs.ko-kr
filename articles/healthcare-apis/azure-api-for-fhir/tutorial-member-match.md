---
title: 자습서-$member-일치 작업-FHIR 용 Azure API
description: 이 자습서에서는 hrex (Da 다빈치 상태 레코드 Exchange)의 일부로 정의 된 $member 일치 작업을 소개 합니다.
services: healthcare-apis
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: tutorial
ms.reviewer: matjazl
ms.author: cavoeg
author: caitlinv39
ms.date: 06/01/2021
ms.openlocfilehash: 592a5a473cf000713bc8a266e36516bc777a6e62
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "121785876"
---
# <a name="member-match-operation-for-azure-api-for-fhir"></a>FHIR 용 Azure API에 대 한 $member 일치 작업

[$member 일치](http://hl7.org/fhir/us/davinci-hrex/2020Sep/OperationDefinition-member-match.html) 는 hrex (Da 다빈치 상태 레코드 Exchange)의 일부로 정의 된 작업입니다. 이 가이드에서는 $member 일치 항목 및 사용 방법에 대해 살펴보겠습니다.

## <a name="overview-of-member-match"></a>$Member-match 개요

지불자 작업 $member은 새로운 지불자가 환자의 이전 지불자에서 환자에 대 한 고유 식별자를 가져오도록 허용 하 여 지불자 데이터 교환을 지원 하기 위해 만들어졌습니다. $Member 일치 작업에는 요청의 본문에 전달 되는 세 가지 정보가 필요 합니다.

* 환자 인구 통계

* 이전 검사 정보

* 새 검사 정보 (구현에 따라 필요 하지 않음)

데이터가 전달 된 후 FHIR 용 Azure API는 전달 된 이전 검사 정보를 사용 하 여 전달 된 인구 통계와 정확히 일치 하는 환자를 찾을 수 있는지 확인 합니다. 결과가 발견 되 면 응답은 원래 환자 데이터와 이전 지불자에서 추가 된 새 식별자 및 이전 검사 정보를 포함 하는 번들이 됩니다.

> [!NOTE]
> 사양은 새 검사 정보를 전달 하 고 백업 하는 방법을 설명 합니다. 결과를 더 작게 유지 하기 위해 해당 데이터를 생략 하기로 결정 했습니다.

## <a name="example-of-member-match"></a>$Member-match의 예

$Member 일치를 사용 하려면 다음 호출을 사용 합니다.

`POST {{fhirurl}}/Patient/$member-match`

환자, 이전 검사 및 새 검사를 포함 하는 매개 변수 리소스를 본문에 포함 해야 합니다. JSON 표현을 보려면 [$member-일치 예제 요청](http://hl7.org/fhir/us/davinci-hrex/2020Sep/Parameters-member-match-in.json.html)을 참조 하세요.

단일 일치 항목이 발견 되 면 다른 식별자가 추가 된 200 응답을 받게 됩니다.

:::image type="content" source="media/cms-tutorials/two-hundred-response.png" alt-text="200 100 응답 코드입니다.":::

$Member 일치에서 고유한 일치 항목을 찾을 수 없는 경우 오류 코드와 함께 422 응답을 받게 됩니다.

## <a name="next-steps"></a>다음 단계

이 가이드에서는 $member-일치 작업에 대해 알아보았습니다. 다음으로, Touchstone에서 $member 지불자 작업을 필요로 하는 Da 다빈치 Exchange 데이터를 테스트 하는 방법을 알아볼 수 있습니다.

>[!div class="nextstepaction"]
>[Davinci.txt PDex](../fhir/davinci-pdex-tutorial.md)