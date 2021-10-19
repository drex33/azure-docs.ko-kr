---
title: 사용자 지정 헤더를 사용하여 감사 로그에 데이터 추가 - Azure API for FHIR
description: 이 문서에서는 Azure API for FHIR에서 사용자 지정 HTTP 헤더를 사용하여 감사 로그에 데이터를 추가하는 방법을 설명합니다.
services: healthcare-apis
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: tutorial
ms.reviewer: dseven
ms.author: zxue
author: matjazl
ms.date: 10/13/2019
ms.openlocfilehash: 78f370851d67fc64279b3b97459794ecb740be7e
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "121787026"
---
# <a name="add-data-to-audit-logs-by-using-custom-http-headers-in-azure-api-for-fhir"></a>FHIR 용 Azure API에서 사용자 지정 HTTP 헤더를 사용 하 여 감사 로그에 데이터 추가

Azure FHIR(전자 의료 기록 교환) API에서 사용자는 호출 시스템에서 제공되는 추가 정보를 로그에 포함할 수 있습니다.

예를 들어 API의 사용자가 외부 시스템에서 인증되는 경우 해당 시스템은 FHIR API에 대한 호출을 전달합니다. 호출이 전달되었으므로 FHIR API 계층에서 원래 사용자에 대한 정보가 손실되었습니다. 감사 또는 관리 목적으로 이 사용자 정보를 기록하고 유지해야 할 수도 있습니다. 호출 시스템은 HTTP 헤더에 사용자 ID, 호출자 위치 또는 기타 필요한 정보를 제공할 수 있습니다. 이 정보는 호출이 전달될 때 함께 전달됩니다.

다음 다이어그램에서 이 데이터 흐름을 확인할 수 있습니다.

:::image type="content" source="media/custom-headers/custom-headers-diagram.png" alt-text="사용자 지정 헤더의 다이어그램":::

사용자 지정 헤더를 사용하여 여러 유형의 정보를 캡처할 수 있습니다. 예를 들면 다음과 같습니다.

* ID 또는 권한 부여 정보
* 호출자의 원점
* 원본 조직
* 클라이언트 시스템 세부 정보(전자 건강 기록, 환자 포털)

> [!IMPORTANT]
> 사용자 지정 헤더로 전송된 정보는 Azure Log 모니터링에서 사용할 수 있으면 30일 동안 Microsoft 내부 로깅 시스템에 저장됩니다. 사용자 지정 헤더에 추가하기 전에 모든 정보를 암호화하는 것이 좋습니다. 고객 헤더를 통해 모든 PHI 정보를 전달하면 안 됩니다.

HTTP 헤더에는 다음 명명 규칙을 사용해야 합니다. X-MS-AZUREFHIR-AUDIT-\<name>.

이러한 HTTP 헤더는 로그에 추가되는 속성 모음에 포함되어 있습니다. 예를 들면 다음과 같습니다.

* X-MS-AZUREFHIR-AUDIT-USERID: 1234 
* X-MS-AZUREFHIR-AUDIT-USERLOCATION: XXXX
* X-MS-AZUREFHIR-AUDIT-XYZ: 1234

이 정보는 로그의 속성 열에 추가될 때 JSON으로 직렬화됩니다. 예를 들면 다음과 같습니다.

```json
{ "X-MS-AZUREFHIR-AUDIT-USERID" : "1234",
"X-MS-AZUREFHIR-AUDIT-USERLOCATION" : "XXXX",
"X-MS-AZUREFHIR-AUDIT-XYZ" : "1234" }
```
 
모든 HTTP 헤더와 마찬가지로 동일한 헤더 이름을 다른 값으로 반복할 수 있습니다. 예를 들면 다음과 같습니다.

* X-MS-AZUREFHIR-AUDIT-USERLOCATION: HospitalA
* X-MS-AZUREFHIR-AUDIT-USERLOCATION: 응급

로그에 추가되는 경우 값은 쉼표로 구분된 목록으로 결합됩니다. 예를 들면 다음과 같습니다.

{ "X-MS-AZUREFHIR-AUDIT-USERLOCATION" : "HospitalA, Emergency" }
 
최대 10개의 고유 헤더를 추가할 수 있습니다. 다른 값을 사용하는 동일한 헤더의 반복은 하나로 계산됩니다. 한 헤더에 대한 값의 총 최대 길이는 2048자입니다.

Firefly C# 클라이언트 API 라이브러리를 사용하는 경우 코드는 다음과 같습니다.

```C#
FhirClient client;
client = new FhirClient(serverUrl);
client.OnBeforeRequest += (object sender, BeforeRequestEventArgs e) =>
{
    // Add custom headers to be added to the logs
    e.RawRequest.Headers.Add("X-MS-AZUREFHIR-AUDIT-UserLocation", "HospitalA");
};
client.Get("Patient");
```
## <a name="next-steps"></a>다음 단계
이 문서에서는 Azure API for FHIR에서 사용자 지정 헤더를 사용하여 감사 로그에 데이터를 추가하는 방법에 대해 알아보았습니다. 다음으로 Azure API for FHIR에서 구성할 수 있는 다른 추가 설정에 대해 알아봅니다.
 
>[!div class="nextstepaction"]
>[추가 설정](azure-api-for-fhir-additional-settings.md)
