---
title: FHIR 서비스에서 인덱스 작업을 실행 하는 방법-Azure 의료 Api (미리 보기)
description: 인덱싱 작업을 실행 하 여 데이터베이스에서 아직 인덱싱되지 않은 검색 또는 정렬 매개 변수를 인덱싱하는 방법
author: ginalee-dotcom
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 08/23/2021
ms.author: cavoeg
ms.openlocfilehash: 5544e0caee421d128d7238cb140c4dd525ed7022
ms.sourcegitcommit: 28cd7097390c43a73b8e45a8b4f0f540f9123a6a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/24/2021
ms.locfileid: "122778916"
---
# <a name="running-a-reindex-job"></a>인덱스 작업 실행

> [!IMPORTANT]
> Azure 의료 Api는 현재 미리 보기로 제공 됩니다. [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)에는 베타 또는 미리 보기로 제공되거나 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 추가 약관이 포함되어 있습니다.

아직 인덱싱되지 않은 Azure 의료 Api (FHIR 서비스 라고 함)의 FHIR 서비스에서 검색 또는 정렬 매개 변수를 사용할 수 있는 시나리오가 있습니다. 이 시나리오는 고유한 검색 매개 변수를 정의 하는 경우와 관련이 있습니다. 검색 매개 변수가 인덱싱되는 동안에는 검색에 사용할 수 없습니다. 이 문서에서는 인덱싱 작업을 실행 하 여 데이터베이스에서 아직 인덱싱되지 않은 모든 검색 또는 정렬 매개 변수를 인덱싱하는 방법에 대해 간략하게 설명 합니다.

> [!Warning]
> 시작 하기 전에이 문서 전체를 읽는 것이 중요 합니다. 인덱스를 사용 하는 작업은 매우 성능이 매우 높습니다. 이 문서에는 인덱스 작업을 제한 하 고 제어 하는 방법에 대 한 옵션이 포함 되어 있습니다.

## <a name="how-to-run-a-reindex-job"></a>재색인 작업 실행 방법 

인덱스를 사용 하 여 작업을 시작 하려면 다음 코드 예제를 사용 합니다.

```json
POST {{FHIR URL}}/$reindex 

{ 

“resourceType”: “Parameters”,  

“parameter”: [] 

}
 ```

요청에 성공 하면 **201 생성** 됨 상태가 반환 됩니다. 이 메시지의 결과는 다음과 같습니다.

```json
HTTP/1.1 201 Created 
Content-Location: https://{{FHIR URL}}/_operations/reindex/560c7c61-2c70-4c54-b86d-c53a9d29495e 

{
  "resourceType": "Parameters",
  "id": "560c7c61-2c70-4c54-b86d-c53a9d29495e",
  "meta": {
    "versionId": "\"4c0049cd-0000-0100-0000-607dc5a90000\""
  },
  "parameter": [
    {
      "name": "id",
      "valueString": "560c7c61-2c70-4c54-b86d-c53a9d29495e"
    },
    {
      "name": "queuedTime",
      "valueDateTime": "2021-04-19T18:02:17.0118558+00:00"
    },
    {
      "name": "totalResourcesToReindex",
      "valueDecimal": 0.0
    },
    {
      "name": "resourcesSuccessfullyReindexed",
      "valueDecimal": 0.0
    },
    {
      "name": "progress",
      "valueDecimal": 0.0
    },
    {
      "name": "status",
      "valueString": "Queued"
    },
    {
      "name": "maximumConcurrency",
      "valueDecimal": 1.0
    },
    {
      "name": "resources",
      "valueString": ""
    },
    {
      "name": "searchParams",
      "valueString": ""
    }
  ]
}
```

> [!NOTE]
> 또는의 상태를 확인 하 여 인덱스 취소 작업을 취소 하려면 인덱스 재 인덱싱 ID가 필요 합니다. 결과 매개 변수 리소스의 ID입니다. 위의 예제에서 재 인덱싱 작업의 ID는 `560c7c61-2c70-4c54-b86d-c53a9d29495e` 입니다.

 ## <a name="how-to-check-the-status-of-a-reindex-job"></a>인덱스 작업의 상태를 확인 하는 방법

재 인덱싱 작업을 시작한 후에는 다음 호출을 사용 하 여 작업 상태를 확인할 수 있습니다.

`GET {{FHIR URL}}/_operations/reindex/{{reindexJobId}`

인덱스 재 인덱싱 작업 결과의 상태는 다음과 같습니다.

```json
{

  "resourceType": "Parameters",
  "id": "b65fd841-1c62-47c6-898f-c9016ced8f77",
  "meta": {

    "versionId": "\"1800f05f-0000-0100-0000-607a1a7c0000\""
  },
  "parameter": [

    {

      "name": "id",
      "valueString": "b65fd841-1c62-47c6-898f-c9016ced8f77"
    },
    {

      "name": "startTime",
      "valueDateTime": "2021-04-16T23:11:35.4223217+00:00"
    },
    {

      "name": "queuedTime",
      "valueDateTime": "2021-04-16T23:11:29.0288163+00:00"
    },
    {

      "name": "totalResourcesToReindex",
      "valueDecimal": 262544.0
    },
    {

      "name": "resourcesSuccessfullyReindexed",
      "valueDecimal": 5754.0
    },
    {

      "name": "progress",
      "valueDecimal": 2.0
    },
    {

      "name": "status",
      "valueString": "Running"
    },
    {

      "name": "maximumConcurrency",
      "valueDecimal": 1.0
    },
    {

      "name": "resources",
      "valueString": 
      "{LIST OF IMPACTED RESOURCES}"
    },
    {
```

다음 정보는 재 인덱싱 작업 결과에 표시 됩니다.

* **totalResourcesToReindex**: 작업의 일부로 인덱싱해야 되는 총 리소스 수를 포함 합니다.

* **resourcesSuccessfullyReindexed**: 이미 인덱싱해야 된 합계입니다.

* **진행률**: 작업 완료율을 인덱싱합니다. ResourcesSuccessfullyReindexed/totalResourcesToReindex x 100와 같습니다.

* **상태**: 인덱스 취소 작업이 큐에 대기, 실행 중, 완료, 실패 또는 취소 되었는지 여부를 나타내는 상태입니다.

* **리소스**: 인덱스 작업의 영향을 받는 리소스 종류를 모두 나열 합니다.

## <a name="delete-a-reindex-job"></a>인덱스 삭제 작업 삭제

인덱스 재 작업을 취소 해야 하는 경우 delete 호출을 사용 하 고 인덱스 취소 작업 ID를 지정 합니다.

`Delete {{FHIR URL}}/_operations/reindex/{{reindexJobId}`

## <a name="performance-considerations"></a>성능 고려 사항

인덱스를 사용 하면 성능이 크게 향상 될 수 있습니다. 데이터베이스에서 재 인덱싱 작업이 실행 되는 방식을 관리 하는 데 도움이 되는 몇 가지 제한 컨트롤을 구현 했습니다.

> [!NOTE]
> 인덱스 작업을 일 동안 실행 하는 경우에는 일반적이 지 않은 대량 데이터 집합에서 자주 발생 하지 않습니다.

다음은 사용 가능한 매개 변수, 기본값 및 권장 범위에 대 한 개요입니다. 이러한 매개 변수를 사용 하 여 프로세스의 속도를 높이고 (더 많은 계산 사용) 프로세스 속도를 낮출 수 있습니다 (더 낮은 계산 사용). 

| **매개 변수**                     | **설명**              | **기본값**        | **사용 가능한 범위**            |
| --------------------------------- | ---------------------------- | ------------------ | ------------------------------- |
| QueryDelayIntervalInMilliseconds  | 일괄 처리 작업을 수행 하는 동안 시작 되는 각 리소스 일괄 처리 사이의 지연입니다. 숫자가 작을수록 작업 속도는 향상 되 고 더 높은 숫자는 속도가 느려집니다. | 500 밀리초 (5 초) | 50 ~ 50만 |
| MaximumResourcesPerQuery  | 인덱싱해야 리소스의 일괄 처리에 포함 되는 최대 리소스 수입니다.  | 100 | 1-5000 |
| MaximumConcurrency  | 한 번에 수행 된 일괄 처리의 수입니다.  | 1 | 1-10 |

위의 매개 변수를 사용 하려는 경우에는 인덱스 작업을 시작할 때 매개 변수 리소스에 전달할 수 있습니다.

```json
{
  "resourceType": "Parameters",
  "parameter": [
    {
      "name": "maximumConcurrency",
      "valueInteger": "3"
    },
    {
      "name": "queryDelayIntervalInMilliseconds",
      "valueInteger": "1000"
    },
    {
      "name": "maximumNumberOfResourcesPerQuery",
      "valueInteger": "1"
    }
  ]
}
```

## <a name="next-steps"></a>다음 단계

이 문서에서는 인덱스 작업을 시작 하는 방법을 배웠습니다. 인덱스를 새로 만드는 작업을 필요로 하는 새 검색 매개 변수를 정의 하는 방법에 대 한 자세한 내용은 

>[!div class="nextstepaction"]
>[사용자 지정 검색 매개 변수 정의](how-to-do-custom-search.md)
