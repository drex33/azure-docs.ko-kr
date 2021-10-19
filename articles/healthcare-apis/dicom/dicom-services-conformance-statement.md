---
title: Azure 의료 Api에 대 한 DICOM 준수 문
description: 이 문서에서는 Azure 의료 Api에 대 한 DICOM 준수 문에 대해 자세히 설명 합니다.
services: healthcare-apis
author: stevewohl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 10/05/2021
ms.author: aersoy
ms.openlocfilehash: 32a368243ab2114799dd9ce9d2ffcdf3306115c9
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2021
ms.locfileid: "130044670"
---
# <a name="dicom-conformance-statement"></a>DICOM 준수 문

> [!IMPORTANT]
> Azure 의료 Api는 현재 미리 보기로 제공 됩니다. [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)에는 베타 또는 미리 보기로 제공되거나 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 추가 약관이 포함되어 있습니다.

**DICOM 용 AZURE API 서비스** 는 DICOMweb 표준의 하위 집합을 지원 합니다 &trade; . 이러한 지원이 다음을 포함합니다.

* [매장 (적재-RS)](#store-stow-rs)
* [검색 (WADO-RS)](#retrieve-wado-rs)
* [검색 (QIDO-RS)](#search-qido-rs)

또한 다음과 같은 비표준 API가 지원 됩니다.

- [Delete](#delete)

또한 서비스에서는 REST API 버전 관리를 사용 합니다. 요청을 만들 때 버전을 지정 하는 방법에 대 한 자세한 내용은 [DICOM 서비스에 대 한 API 버전 관리 설명서](api-versioning-dicom-service.md)를 참조 하세요.

## <a name="store-stow-rs"></a>매장 (적재-RS)

이 트랜잭션은 POST 메서드를 사용 하 여 요청 페이로드에 포함 된 연구, 시리즈 및 인스턴스의 표현을 저장 합니다.

| 메서드 | 경로               | 설명 |
| :----- | :----------------- | :---------- |
| POST   | .. /연구         | 인스턴스를 저장 합니다. |
| POST   | .. /studies/{study} | 특정 연구에 대 한 인스턴스를 저장 합니다. |

매개 변수 `study` 는 DICOM 특성 StudyInstanceUID에 해당 합니다. 지정 된 경우 제공 된 연구에 속하지 않는 모든 인스턴스는 경고 코드와 함께 거부 됩니다 `43265` .

`Accept`응답에 대 한 다음 헤더가 지원 됩니다.

* `application/dicom+json`

`Content-Type`지원 되는 헤더는 다음과 같습니다.

* `multipart/related; type="application/dicom"`
* `application/dicom`

> [!NOTE]
> 서버는 기존 데이터와 충돌 하는 특성을 강제 변환 하거나 대체 **하지 않습니다** . 모든 데이터는 제공 된 대로 저장 됩니다.

저장 하려는 모든 DICOM 파일에는 다음과 같은 DICOM 요소가 있어야 합니다.

* StudyInstanceUID
* SeriesInstanceUID
* SOPInstanceUID
* SOPClassUID
* 환자 ID

> [!NOTE]
> 모든 식별자의 길이는 1 ~ 007e; 64 자 여야 하며 영숫자 문자 또는 '. ', '-' 등의 특수 문자만 포함 해야 합니다.

저장 된 각 파일에는 StudyInstanceUID, SeriesInstanceUID 및 SopInstanceUID의 고유한 조합이 있어야 합니다. `45070`동일한 식별자를 가진 파일이 이미 있는 경우 경고 코드가 반환 됩니다.

DICOM 파일 크기 제한: 기본적으로 DICOM 파일의 크기 제한은 2gb입니다.

### <a name="store-response-status-codes"></a>저장소 응답 상태 코드

| 코드                         | Description |
| :--------------------------- | :---------- |
| 200(확인)                     | 요청의 모든 SOP 인스턴스가 저장 되었습니다. |
| 202(수락됨)               | 요청의 일부 인스턴스가 저장 되었지만 다른 인스턴스가 실패 했습니다. |
| 204(내용 없음)             | 저장소 트랜잭션 요청에 콘텐츠가 제공 되지 않았습니다. |
| 400(잘못된 요청)            | 요청의 형식이 잘못 되었습니다. 예를 들어 제공 된 연구 인스턴스 식별자가 예상 UID 형식을 따르지 않습니다. |
| 401(권한이 없음)           | 클라이언트가 인증 되지 않았습니다. |
| 403(사용할 수 없음)              | 사용자에 게 권한이 없습니다. |
| 406 (허용 되지 않음)         | 지정 된 `Accept` 헤더는 지원 되지 않습니다. |
| 409(충돌)               | 저장소 트랜잭션 요청의 인스턴스가 모두 저장 되지 않았습니다. |
| 415(지원되지 않는 미디어 형식) | 제공 된는 `Content-Type` 지원 되지 않습니다. |
| 503 (서비스를 사용할 수 없음)    | 서비스를 사용할 수 없거나 사용 중입니다. 나중에 다시 시도하세요. |

### <a name="store-response-payload"></a>저장소 응답 페이로드

응답 페이로드는 다음 요소를 사용 하 여 DICOM 데이터 집합을 채웁니다.

| 태그          | Name                  | 설명 |
| :----------- | :-------------------- | :---------- |
| (0008, 1190) | RetrieveURL           | StudyInstanceUID이 매장 요청에서 제공 되 고 하나 이상의 인스턴스가 성공적으로 저장 된 경우에는 연구 URL을 검색 합니다. |
| (0008, 1198) | FailedSOPSequence     | 저장 하지 못한 인스턴스의 시퀀스입니다. |
| (0008, 1199) | ReferencedSOPSequence | 저장 된 인스턴스의 시퀀스입니다. |

의 각 데이터 집합에는 `FailedSOPSequence` 다음과 같은 요소가 있습니다 (저장 하려는 DICOM 파일을 읽을 수 있는 경우).

| 태그          | Name                     | 설명 |
| :----------- | :----------------------- | :---------- |
| (0008, 1150) | ReferencedSOPClassUID    | SOP 클래스에서 저장 하지 못한 인스턴스의 고유 식별자입니다. |
| (0008, 1150) | ReferencedSOPInstanceUID | 저장 하지 못한 인스턴스의 SOP 인스턴스 고유 식별자입니다. |
| (0008, 1197) | FailureReason            | 이 인스턴스를 저장 하지 못한 이유 코드입니다. |

의 각 데이터 집합에는 `ReferencedSOPSequence` 다음과 같은 요소가 있습니다.

| 태그          | Name                     | 설명 |
| :----------- | :----------------------- | :---------- |
| (0008, 1150) | ReferencedSOPClassUID    | SOP 클래스에서 저장 하지 못한 인스턴스의 고유 식별자입니다. |
| (0008, 1150) | ReferencedSOPInstanceUID | 저장 하지 못한 인스턴스의 SOP 인스턴스 고유 식별자입니다. |
| (0008, 1190) | RetrieveURL              | DICOM 서버에서이 인스턴스의 검색 URL입니다. |

다음은 헤더를 사용 하는 예제 응답입니다 `Accept` `application/dicom+json` .

```json
{
  "00081190":
  {
    "vr":"UR",
    "Value":["http://localhost/studies/d09e8215-e1e1-4c7a-8496-b4f6641ed232"]
  },
  "00081198":
  {
    "vr":"SQ",
    "Value":
    [{
      "00081150":
      {
        "vr":"UI","Value":["cd70f89a-05bc-4dab-b6b8-1f3d2fcafeec"]
      },
      "00081155":
      {
        "vr":"UI",
        "Value":["22c35d16-11ce-43fa-8f86-90ceed6cf4e7"]
      },
      "00081197":
      {
        "vr":"US",
        "Value":[43265]
      }
    }]
  },
  "00081199":
  {
    "vr":"SQ",
    "Value":
    [{
      "00081150":
      {
        "vr":"UI",
        "Value":["d246deb5-18c8-4336-a591-aeb6f8596664"]
      },
      "00081155":
      {
        "vr":"UI",
        "Value":["4a858cbb-a71f-4c01-b9b5-85f88b031365"]
      },
      "00081190":
      {
        "vr":"UR",
        "Value":["http://localhost/studies/d09e8215-e1e1-4c7a-8496-b4f6641ed232/series/8c4915f5-cc54-4e50-aa1f-9b06f6e58485/instances/4a858cbb-a71f-4c01-b9b5-85f88b031365"]
      }
    }]
  }
}
```

### <a name="store-failure-reason-codes"></a>저장소 오류 이유 코드

| 코드  | Description |
| :---- | :---------- |
| 272   | 작업 처리에 일반적인 오류가 발생 하 여 저장소 트랜잭션에서 인스턴스를 저장 하지 않았습니다. |
| 43264 | DICOM 인스턴스에서 유효성 검사에 실패 했습니다. |
| 43265 | 제공 된 인스턴스 StudyInstanceUID가 저장소 요청의 지정 된 StudyInstanceUID와 일치 하지 않습니다. |
| 45070 | 동일한 StudyInstanceUID, SeriesInstanceUID 및 SopInstanceUID를 사용 하는 DICOM 인스턴스가 이미 저장 되었습니다. 내용을 업데이트 하려는 경우 먼저이 인스턴스를 삭제 합니다. |
| 45071 | 다른 프로세스에서 DICOM 인스턴스를 만드는 중이거나 이전에 만들려고 했지만 정리 프로세스가 아직 정리 되지 않았습니다. 인스턴스를 먼저 삭제 한 후 다시 만드십시오. |

## <a name="retrieve-wado-rs"></a>검색 (WADO-RS)

이 검색 트랜잭션은 저장 된 연구, 시리즈, 인스턴스 및 프레임을 참조로 검색 하는 기능을 제공 합니다.

| 메서드 | 경로                                                                    | 설명 |
| :----- | :---------------------------------------------------------------------- | :---------- |
| GET    | .. /studies/{study}                                                      | 연구 내의 모든 인스턴스를 검색 합니다. |
| GET    | .. /studies/{study}/metadata                                             | 연구 내의 모든 인스턴스에 대 한 메타 데이터를 검색 합니다. |
| GET    | .. /studies/{study}/series/{series}                                      | 계열 내의 모든 인스턴스를 검색 합니다. |
| GET    | .. /studies/{study}/series/{series}/metadata                             | 계열 내의 모든 인스턴스에 대 한 메타 데이터를 검색 합니다. |
| GET    | .. /studies/{study}/series/{series}/instances/{instance}                 | 단일 인스턴스를 검색 합니다. |
| GET    | .. /studies/{study}/series/{series}/instances/{instance}/metadata        | 단일 인스턴스에 대 한 메타 데이터를 검색 합니다. |
| GET    | .. /studies/{study}/series/{series}/instances/{instance}/frames/{frames} | 단일 인스턴스에서 하나 이상의 프레임을 검색 합니다. 둘 이상의 프레임을 지정 하려면 쉼표를 사용 하 여 각 프레임을 구분 하 여 반환 합니다. 예:/studies/1/series/2/instance/3/frames/4, 5, 6 |

### <a name="retrieve-instances-within-study-or-series"></a>연구 또는 계열 내에서 인스턴스 검색

다음 `Accept` 헤더는 연구 또는 시리즈 내에서 인스턴스를 검색 하는 데 지원 됩니다.


* `multipart/related; type="application/dicom"; transfer-syntax=*`
* `multipart/related; type="application/dicom";` 전송 구문이 지정 되지 않은 경우 1.2.840.10008.1.2.1가 기본값으로 사용 됩니다.
* `multipart/related; type="application/dicom"; transfer-syntax=1.2.840.10008.1.2.1`
* `multipart/related; type="application/dicom"; transfer-syntax=1.2.840.10008.1.2.4.90`

### <a name="retrieve-an-instance"></a>인스턴스 검색

`Accept`특정 인스턴스를 검색 하는 데 지원 되는 헤더는 다음과 같습니다.

* `application/dicom; transfer-syntax=*`
* `multipart/related; type="application/dicom"; transfer-syntax=*`
* `application/dicom;` 전송 구문이 지정 되지 않은 경우 1.2.840.10008.1.2.1가 기본값으로 사용 됩니다.
* `multipart/related; type="application/dicom"` 전송 구문이 지정 되지 않은 경우 1.2.840.10008.1.2.1가 기본값으로 사용 됩니다.
* `application/dicom; transfer-syntax=1.2.840.10008.1.2.1`
* `multipart/related; type="application/dicom"; transfer-syntax=1.2.840.10008.1.2.1`
* `application/dicom; transfer-syntax=1.2.840.10008.1.2.4.90`
* `multipart/related; type="application/dicom"; transfer-syntax=1.2.840.10008.1.2.4.90`

### <a name="retrieve-frames"></a>프레임 검색

다음 `Accept` 헤더는 프레임 검색에 대해 지원 됩니다.

* `multipart/related; type="application/octet-stream"; transfer-syntax=*`
* `multipart/related; type="application/octet-stream";` 전송 구문이 지정 되지 않은 경우 1.2.840.10008.1.2.1가 기본값으로 사용 됩니다.
* `multipart/related; type="application/octet-stream"; transfer-syntax=1.2.840.10008.1.2.1`
* `multipart/related; type="image/jp2";` 전송 구문이 지정 되지 않은 경우 1.2.840.10008.1.2.4.90가 기본값으로 사용 됩니다.
* `multipart/related; type="image/jp2";transfer-syntax=1.2.840.10008.1.2.4.90`

### <a name="retrieve-transfer-syntax"></a>전송 구문 검색

요청 된 전송 구문이 원본 파일과 다른 경우 원래 파일은 요청 된 전송 구문으로 트랜스 코딩 됩니다. 원본 파일은 다음 형식 중 하나 여야 합니다. 그렇지 않으면 코드 변환이 실패할 수 있습니다.

* 1.2.840.10008.1.2 (작은 Endian 암시적)
* 1.2.840.10008.1.2.1 (작은 Endian Explicit)
* 1.2.840.10008.1.2.2 (명시적 VR 빅 Endian)
* 1.2.840.10008.1.2.4.50 (JPEG 기준 프로세스 1)
* 1.2.840.10008.1.2.4.57 (JPEG 무손실)
* 1.2.840.10008.1.2.4.70 (JPEG 무손실 선택 값 1)
* 1.2.840.10008.1.2.4.90 (JPEG 2000 무손실에만 해당)
* 1.2.840.10008.1.2.4.91 (JPEG 2000)
* 1.2.840.10008.1.2.5 (RLE 무손실)

지원 되지 않는 경우 `transfer-syntax` 이 발생 `406 Not Acceptable` 합니다.

### <a name="retrieve-metadata-for-study-series-or-instance"></a>메타 데이터 검색 (연구, 시리즈 또는 인스턴스의 경우)

다음 `Accept` 헤더는 연구, 시리즈 또는 인스턴스에 대 한 메타 데이터를 검색 하는 데 지원 됩니다.

* `application/dicom+json`

메타 데이터 검색은 다음 값 표현으로 특성을 반환 하지 않습니다.

| VR 이름 | 설명            |
| :------ | :--------------------- |
| OB      | 기타 바이트             |
| OD      | 기타 Double           |
| OF      | 기타 Float            |
| OL      | 기타 Long             |
| OV-ES      | 기타 64 비트 매우 김 |
| OW      | 기타 단어             |
| UN      | Unknown                |

### <a name="retrieve-metadata-cache-validation-for-study-series-or-instance"></a>(연구, 시리즈 또는 인스턴스)에 대 한 메타 데이터 캐시 유효성 검사를 검색 합니다.

메커니즘을 사용 하 여 캐시 유효성 검사를 지원 `ETag` 합니다. 메타 데이터 요청에 대 한 응답에서 ETag는 헤더 중 하나로 반환 됩니다. 이 ETag는 `If-None-Match` 나중에 동일한 메타 데이터에 대 한 요청에서 캐시 되 고 헤더로 추가 될 수 있습니다. 데이터가 존재 하는 경우 두 가지 유형의 응답이 가능 합니다.

* 마지막 요청 이후 데이터가 변경 되지 않았습니다. HTTP 304 (수정 되지 않음) 응답이 응답 본문 없이 전송 됩니다.
* 마지막 요청 이후 데이터가 변경 되었습니다. HTTP 200 (OK) 응답은 업데이트 된 ETag와 함께 전송 됩니다. 필요한 데이터도 본문의 일부로 반환 됩니다.

### <a name="retrieve-response-status-codes"></a>응답 상태 코드 검색

| 코드                         | Description |
| :--------------------------- | :---------- |
| 200(확인)                     | 요청 된 모든 데이터가 검색 되었습니다. |
| 304(수정 안 됨)           | 요청한 데이터가 마지막 요청 이후 수정 되지 않았습니다. 이 경우 콘텐츠는 응답 본문에 추가 되지 않습니다. 자세한 내용은 위의 섹션인 **메타 데이터 캐시 유효성 검사 검색 (연구, 시리즈 또는 인스턴스)** 을 참조 하세요. |
| 400(잘못된 요청)            | 요청의 형식이 잘못 되었습니다. 예를 들어 제공 된 연구 인스턴스 식별자가 예상 UID 형식을 따르지 않거나 요청 된 전송 구문 인코딩이 지원 되지 않습니다. |
| 401(권한이 없음)           | 클라이언트가 인증 되지 않았습니다. |
| 403(사용할 수 없음)              | 사용자에 게 권한이 없습니다. |
| 404 (찾을 수 없음)              | 지정한 DICOM 리소스를 찾을 수 없습니다. |
| 406 (허용 되지 않음)         | 지정 된 `Accept` 헤더는 지원 되지 않습니다. |
| 503 (서비스를 사용할 수 없음)    | 서비스를 사용할 수 없거나 사용 중입니다. 나중에 다시 시도하세요. |

## <a name="search-qido-rs"></a>검색 (QIDO-RS)

DICOM 개체의 ID를 기반으로 하는 쿼리 (QIDO)를 사용 하 여 특성을 기준으로 연구, 시리즈 및 인스턴스를 검색할 수 있습니다.

| 메서드 | 경로                                            | 설명                       |
| :----- | :---------------------------------------------- | :-------------------------------- |
| *연구 검색*                                                                         |
| GET    | .. /연구?...                                  | 연구 검색                |
| *계열 검색*                                                                          |
| GET    | .. /series?...                                   | 계열 검색                 |
| GET    |.. /studies/{study}/series?...                    | 연구에서 시리즈 검색      |
| *인스턴스 검색*                                                                       |
| GET    |.. /인스턴스?...                                 | 인스턴스 검색              |
| GET    |.. /studies/{study}/instances?...                 | 연구에서 인스턴스 검색   |
| GET    |.. /studies/{study}/series/{series}/instances?... | 계열의 인스턴스 검색  |

`Accept`검색에 지원 되는 헤더는 다음과 같습니다.

- `application/dicom+json`

### <a name="supported-search-parameters"></a>지원 되는 검색 매개 변수

각 쿼리에 대해 다음 매개 변수가 지원 됩니다.

| 키              | 지원 값              | 허용 되는 개수 | 설명 |
| :--------------- | :---------------------------- | :------------ | :---------- |
| `{attributeID}=` | 기본값                       | 0 ... N         | 쿼리에서 특성/값 일치를 검색 합니다. |
| `includefield=`  | `{attributeID}`<br/>`all`   | 0 ... N         | 응답에 반환할 추가 특성입니다. 공용 및 개인 태그가 둘 다 지원 됩니다.<br/>`all`가 제공 된 경우 각 쿼리 유형에 대해 반환 되는 특성에 대 한 자세한 내용은 [검색 응답](#search-response) 을 참조 하세요.<br/>{AttributeID} 및 ' a l l '을 혼합 하 여 제공 하면 서버는 기본적으로 ' 모두 '를 사용 합니다. |
| `limit=`         | 기본값                       | 0..1          | 응답에서 반환 되는 값의 수를 제한 하는 정수 값입니다.<br/>값은 1 >= x <= 200 범위에 있을 수 있습니다. 기본값은 100입니다. |
| `offset=`        | 기본값                       | 0..1          | {Value} 개 결과를 건너뜁니다.<br/>오프셋이 검색 쿼리 결과 수보다 크게 제공 되는 경우 204 (콘텐츠 없음) 응답이 반환 됩니다. |
| `fuzzymatching=` | `true` \| `false`             | 0..1          | True 인 경우 true 일치가 PatientName 특성에 적용 됩니다. PatientName value 내에서 이름 부분의 접두사 단어 일치를 수행 합니다. 예를 들어, PatientName가 "John ^ Doe" 인 경우 "joh", "do", "Doe do", "Doe" 및 "John Doe"는 모두 일치 합니다. 그러나 "ohn"는 일치 하지 않습니다. |

#### <a name="searchable-attributes"></a>검색 가능한 특성

다음 특성 및 검색 유형을 검색 하는 것을 지원 합니다.

| Attribute 키워드 | 민감도 | 계열 | 인스턴스 |
| :---------------- | :---: | :----: | :------: |
| StudyInstanceUID | X | X | X |
| PatientName | X | X | X |
| 환자 ID | X | X | X |
| AccessionNumber | X | X | X |
| ReferringPhysicianName | X | X | X |
| StudyDate | X | X | X |
| StudyDescription | X | X | X |
| SeriesInstanceUID |  | X | X |
| 형식 |  | X | X |
| PerformedProcedureStepStartDate |  | X | X |
| SOPInstanceUID |  |  | X |

#### <a name="search-matching"></a>검색 일치

다음과 같은 일치 형식을 지원합니다.

| 검색 유형 | 지원되는 특성 | 예제 |
| :---------- | :------------------ | :------ |
| 범위 쿼리 | StudyDate | {attributeID}={value1}-{value2}. 날짜/시간 값의 경우 태그에 포함 범위를 지원했습니다. 이는 `attributeID >= {value1} AND attributeID <= {value2}` 에 매핑됩니다. |
| 정확하게 일치 | 지원되는 모든 특성 | {attributeID}={value1} |
| Fuzzy Match | PatientName | 값으로 시작하는 환자 이름의 구성 요소와 일치합니다. |

#### <a name="attribute-id"></a>특성 ID

태그는 쿼리 매개 변수에 대해 여러 가지 방법으로 인코딩할 수 있습니다. [PS3.18 6.7.1.1.1](http://dicom.nema.org/medical/dicom/2019a/output/chtml/part18/sect_6.7.html#sect_6.7.1.1.1)에 정의된 대로 표준을 부분적으로 구현했습니다. 태그에 대한 다음 인코딩이 지원됩니다.

| 값            | 예제          |
| :--------------- | :--------------- |
| {group} {element} | 0020000D         |
| {dicomKeyword}   | StudyInstanceUID |

인스턴스에 대한 쿼리 검색 예제: **.. /instances? Modality=CT&00280011=512&includefield=00280010&limit=5&offset=0**

### <a name="search-response"></a>검색 응답

응답은 DICOM 데이터 세트의 배열입니다. 리소스에 따라 *기본적으로* 다음 특성이 반환됩니다.

#### <a name="default-study-tags"></a>기본 연구 태그

| 태그          | 특성 이름 |
| :----------- | :------------- |
| (0008, 0005) | SpecificCharacterSet |
| (0008, 0020) | StudyDate |
| (0008, 0030) | StudyTime |
| (0008, 0050) | AccessionNumber |
| (0008, 0056) | InstanceAvailability |
| (0008, 0090) | ReferringPhysicianName |
| (0008, 0201) | TimezoneOffsetFromUTC |
| (0010, 0010) | PatientName |
| (0010, 0020) | 환자 ID |
| (0010, 0030) | PatientBirthDate |
| (0010, 0040) | PatientSex |
| (0020, 0010) | StudyID |
| (0020, 000D) | StudyInstanceUID |

#### <a name="default-series-tags"></a>기본 계열 태그

| 태그          | 특성 이름 |
| :----------- | :------------- |
| (0008, 0005) | SpecificCharacterSet |
| (0008, 0060) | 형식 |
| (0008, 0201) | TimezoneOffsetFromUTC |
| (0008, 103E) | SeriesDescription |
| (0020, 000E) | SeriesInstanceUID |
| (0040, 0244) | PerformedProcedureStepStartDate |
| (0040, 0245) | PerformedProcedureStepStartTime |
| (0040, 0275) | RequestAttributesSequence |

#### <a name="default-instance-tags"></a>기본 인스턴스 태그

| 태그          | 특성 이름 |
| :----------- | :------------- |
| (0008, mylnxcn-0001) | SpecificCharacterSet |
| (0008, 0016) | SOPClassUID |
| (0008, 0018) | SOPInstanceUID |
| (0008, 0056) | InstanceAvailability |
| (0008, 0201) | TimezoneOffsetFromUTC |
| (0020, 0013) | InstanceNumber |
| (0028, mylnxcn-0006 mylnxcn-0010) | 행 |
| (0028, 0011) | 열 |
| (0028, 0100) | BitsAllocated |
| (0028, 0008) | NumberOfFrames |

Includefield = all 인 경우 아래 특성이 기본 특성과 함께 포함 됩니다. 기본 특성과 함께이는 각 리소스 수준에서 지원 되는 특성의 전체 목록입니다.

#### <a name="other-study-tags"></a>기타 연구 태그

| 태그          | 특성 이름 |
| :----------- | :------------- |
| (0008, 1030) | 연구 설명 |
| (0008, 0063) | AnatomicRegionsInStudyCodeSequence |
| (0008, 1032) | ProcedureCodeSequence |
| (0008, 1060) | NameOfPhysiciansReadingStudy |
| (0008, 1080) | AdmittingDiagnosesDescription |
| (0008, 1110) | ReferencedStudySequence |
| (mylnxcn-0006 mylnxcn-0010, 1010) | PatientAge |
| (mylnxcn-0006 mylnxcn-0010, 1020) | PatientSize |
| (mylnxcn-0006 mylnxcn-0010, 1030) | PatientWeight |
| (mylnxcn-0006 mylnxcn-0010, 2180) | Occupation |
| (mylnxcn-0006 mylnxcn-0010, 21B0) | AdditionalPatientHistory |

#### <a name="other-series-tags"></a>기타 계열 태그

| 태그          | 특성 이름 |
| :----------- | :------------- |
| (0020, 0011) | SeriesNumber |
| (0020, 0060) | Laterality |
| (0008, 0021) | SeriesDate |
| (0008, 0031) | SeriesTime |

반환 되는 특성은 다음과 같습니다.

* 리소스 url의 모든 일치 쿼리 매개 변수 및 Uid입니다.
* 해당 리소스 수준에서 지원 되는 IncludeField 특성 
* 대상 리소스가 모두 계열 이면 연구 수준 특성도 반환 됩니다.
* 대상 리소스가 모든 인스턴스인 경우에는 연구 및 계열 수준 특성도 반환 됩니다.
* 대상 리소스가 연구의 인스턴스인 경우 계열 수준 특성도 반환 됩니다.

### <a name="search-response-codes"></a>검색 응답 코드

쿼리 API는 응답에서 다음 상태 코드 중 하나를 반환 합니다.

| 코드                      | Description |
| :------------------------ | :---------- |
| 200(확인)                  | 응답 페이로드는 일치 하는 모든 리소스를 포함 합니다. |
| 204(내용 없음)          | 검색이 성공적으로 완료 되었지만 결과가 반환 되지 않았습니다. |
| 400(잘못된 요청)         | 쿼리 구성 요소가 잘못 되어 서버에서 쿼리를 수행할 수 없습니다. 응답 본문에는 오류에 대 한 세부 정보가 포함 됩니다. |
| 401(권한이 없음)        | 클라이언트가 인증 되지 않았습니다. |
| 403(사용할 수 없음)           | 사용자에 게 권한이 없습니다. |
| 503 (서비스를 사용할 수 없음) | 서비스를 사용할 수 없거나 사용 중입니다. 나중에 다시 시도하세요. |

### <a name="extra-notes"></a>추가 참고 사항

* ()를 사용 하는 쿼리 `TimezoneOffsetFromUTC` `00080201` 는 지원 되지 않습니다.
* 쿼리 API는 413 (요청 엔터티 너무 큼)을 반환 하지 않습니다. 요청 된 쿼리 응답 제한이 허용 되는 범위를 벗어나면 잘못 된 요청이 반환 됩니다. 허용 되는 범위 내에서 요청 된 모든 항목이 확인 됩니다.
* 대상 리소스가 연구/시리즈 인 경우 여러 인스턴스에서 일치 하지 않는 연구/시리즈 수준 메타 데이터를 사용할 가능성이 있습니다. 예를 들어 두 인스턴스의 patientName 서로 다를 수 있습니다. 이 경우 최신이 승리 하 고 최신 데이터만 검색할 수 있습니다.
* 페이지 단위 결과는 일치 하는 *최신* 인스턴스를 먼저 반환 하도록 최적화 되어 있습니다. 그러면 쿼리와 일치 하는 최신 데이터가 추가 된 경우 다음 페이지에 중복 레코드가 생성 될 수 있습니다.
* 일치는 PN VR 형식에 대 한 대/소문자 구분 및 악센트 구분입니다.
* 일치는 다른 문자열 VR 형식에 대 한 대/소문자 구분 및 악센트 구분입니다.

## <a name="delete"></a>삭제

이 트랜잭션은 공식 DICOMweb 표준의 일부가 아닙니다 &trade; . DELETE 메서드를 사용 하 여 상점에서 연구, 시리즈 및 인스턴스의 표현을 제거 합니다.

| 메서드 | 경로                                                    | 설명 |
| :----- | :------------------------------------------------------ | :---------- |
| Delete | .. /studies/{study}                                      | 특정 연구의 모든 인스턴스를 삭제 합니다. |
| Delete | .. /studies/{study}/series/{series}                      | 연구 내의 특정 계열에 대 한 모든 인스턴스를 삭제 합니다. |
| Delete | .. /studies/{study}/series/{series}/instances/{instance} | 계열 내의 특정 인스턴스를 삭제 합니다. |

`study`, `series` 및 매개 변수 `instance` 는 각각 DICOM 특성 StudyInstanceUID, SeriesInstanceUID 및 SopInstanceUID에 해당 합니다.

요청의 `Accept` 헤더, `Content-Type` 헤더 또는 본문 콘텐츠에 대 한 제한은 없습니다.

> [!NOTE]
> 삭제 트랜잭션 후에는 삭제 된 인스턴스를 복구할 수 없습니다.

### <a name="response-status-codes"></a>응답 상태 코드

| 코드                         | Description |
| :--------------------------- | :---------- |
| 204(내용 없음)             | 모든 SOP 인스턴스가 삭제 된 경우 |
| 400(잘못된 요청)            | 요청의 형식이 잘못 되었습니다. |
| 401(권한이 없음)           | 클라이언트가 인증 되지 않았습니다. |
| 403(사용할 수 없음)              | 사용자에 게 권한이 없습니다. |
| 404 (찾을 수 없음)              | 지정 된 계열을 연구에서 찾을 수 없거나 지정 된 인스턴스를 계열 내에서 찾을 수 없는 경우 |
| 503 (서비스를 사용할 수 없음)    | 서비스를 사용할 수 없거나 사용 중입니다. 나중에 다시 시도하세요. |

### <a name="delete-response-payload"></a>응답 페이로드 삭제

응답 본문은 비어 있게 됩니다. 상태 코드는 반환 되는 유일한 유용한 정보입니다.

### <a name="next-steps"></a>다음 단계

DICOM 서비스에 대 한 자세한 내용은 다음을 참조 하세요. 

>[!div class="nextstepaction"]
>[DICOM 서비스 개요](dicom-services-overview.md)
