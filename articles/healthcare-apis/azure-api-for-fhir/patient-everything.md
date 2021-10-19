---
title: Azure API의 FHIR에서 환자 사용
description: 이 문서에서는 Azure API for FHIR에서 환자-모든 작업을 사용 하는 방법을 설명 합니다.
services: healthcare-apis
author: caitlinv39
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 06/04/2021
ms.author: cavoeg
ms.openlocfilehash: 83c38f2add15481bab844f1c21a142644b546c0c
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/03/2021
ms.locfileid: "123429211"
---
# <a name="patient-everything-in-fhir"></a>FHIR의 Patient-Everything

[$Patient 모든](https://www.hl7.org/fhir/patient-operation-everything.html) 작업은 전체 레코드에 대 한 액세스를 제공 하거나 공급자나 다른 사용자가 대량 데이터 다운로드를 수행할 수 있는 환자를 제공 하는 데 사용 됩니다. 이 작업은이 작업이 호출 되는 리소스 또는 컨텍스트에 설명 된 하나 이상의 환자 관련 된 모든 정보를 반환 하는 데 사용 됩니다.  

## <a name="use-patient-everything"></a>환자 사용-모든 항목
환자-모든 항목을 호출 하려면 다음 명령을 사용 합니다.

```json
GET {FHIRURL}/Patient/{ID}/$everything
```
FHIR 용 Azure API는 제공 된 환자 ID와 일치 하는 환자를 찾을 수 있는지 확인 합니다. 결과가 발견 되 면 응답은 다음 정보를 포함 하는 "searchset" 형식의 번들이 됩니다. 
* [환자 리소스](https://www.hl7.org/fhir/patient.html) 
*  환자 리소스에서 직접 참조 하는 리소스 (제외 링크) 
*  [환자 구획](https://www.hl7.org/fhir/compartmentdefinition-patient.html) 의 리소스
*  환자 리소스를 참조 하는 [장치 리소스](https://www.hl7.org/fhir/device.html) 입니다. 이는 100 장치로 제한 됩니다. 환자에 연결 된 장치가 100 개를 초과 하는 경우 100만 반환 됩니다. 


## <a name="patient-everything-parameters"></a>환자-모든 매개 변수
FHIR 용 Azure API는 다음과 같은 쿼리 매개 변수를 지원 합니다. 이러한 매개 변수는 모두 선택 사항입니다.

|쿼리 매개 변수        |  Description|
|-----------------------|------------|
| \_입력할 | 응답에 포함할 리소스 형식을 지정할 수 있습니다. 예를 들어 \_ type = 발생은 `Encounter` 환자와 연결 된 리소스만 반환 합니다. |
| \_since | 는 제공 된 시간 이후에 수정 된 리소스만 반환 합니다. |
| start | 시작 날짜를 지정 하면 해당 임상 날짜가 지정 된 시작 날짜 이후의 리소스를 가져옵니다. 시작 날짜를 제공 하지 않으면 종료 날짜 이전의 모든 레코드가 범위 내에 있습니다. |
| end | 종료 날짜를 지정 하면 해당 임상 날짜가 지정 된 종료 날짜 보다 앞에 있는 리소스를 가져옵니다. 종료 날짜를 제공 하지 않으면 시작 날짜 이후의 모든 레코드가 범위 내에 있습니다. |

> [!Note]
> 특정 환자에 대 한 ID를 지정 해야 합니다. 모든 환자에 대 한 모든 데이터가 필요한 경우 [$export](../data-transformation/export-data.md)를 참조 하세요. 


## <a name="examples-of-patient-everything"></a>$Patient의 예-모든 항목 

$Patient 모든 작업을 사용 하는 몇 가지 예는 다음과 같습니다. 

$Patient-모든 항목을 사용 하 여 2010과 2020 사이의 환자 "모두"를 쿼리하려면 다음 호출을 사용 합니다. 

```json
GET {FHIRURL}/Patient/{ID}/$everything?start=2010&end=2020
``` 

$Patient를 사용 하 여 환자 관찰 및 발생을 쿼리하려면 다음 호출을 사용 합니다. 
```json
GET {FHIRURL}/Patient/{ID}/$everything?_type=Observation,Encounter 
```

$Patient를 사용 하 여 모든 것이 2021-05-27T05:00:00Z 이후 환자의 "모두"를 쿼리하려면 다음 호출을 사용 합니다. 

```json
GET {FHIRURL}/Patient/{ID}/$everything?_since=2021-05-27T05:00:00Z 
```

이러한 각 호출에 대 한 환자를 발견 하면 해당 리소스의 번들로 200 응답을 받게 됩니다.

## <a name="next-step"></a>다음 단계
이제 환자-모든 작업을 사용 하는 방법을 배웠으므로 검색 개요 가이드에서 더 많은 검색 옵션에 대해 알아볼 수 있습니다.

>[!div class="nextstepaction"]
>[FHIR 검색 개요](overview-of-search.md)
