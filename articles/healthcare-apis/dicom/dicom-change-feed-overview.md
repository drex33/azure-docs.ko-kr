---
title: DICOM 변경 피드 개요-Azure 의료 Api
description: 이 문서에서는 DICOM 변경 피드의 개념을 배웁니다.
author: stevewohl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 08/04/2021
ms.author: aersoy
ms.openlocfilehash: fc23c4cedbb6ee94c4f253cf53e0cdde27531fb4
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122435412"
---
# <a name="change-feed-overview"></a>변경 피드 개요

> [!IMPORTANT]
> Azure 의료 Api는 현재 미리 보기로 제공 됩니다. [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)에는 베타 또는 미리 보기로 제공되거나 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 추가 약관이 포함되어 있습니다.

변경 피드는 DICOM 서비스에서 발생 하는 모든 변경 내용에 대 한 로그를 제공 합니다. 변경 피드는 이러한 변경 내용에 대 한 정렬 되 고 보장 되며 변경할 수 없는 읽기 전용 로그를 제공 합니다. 변경 피드는 DICOM 서비스 기록을 거치는 기능을 제공 하 고 서비스의 생성 및 삭제에 대해 작동 합니다.

클라이언트 응용 프로그램은 언제 든 지 스트리밍 또는 일괄 처리 모드에서 이러한 로그를 읽을 수 있습니다. 변경 피드를 사용 하면 DICOM 서비스에서 발생 하는 변경 이벤트를 처리 하는 효율적이 고 확장 가능한 솔루션을 빌드할 수 있습니다.

이러한 변경 이벤트는 비동기적으로 또는 전체에서 처리할 수 있습니다. 원하는 수의 클라이언트 응용 프로그램은 변경 피드를 독립적으로 개별적으로 읽을 수 있습니다.

요청을 만들 때 버전을 URL의 일부로 지정 해야 합니다. 자세한 내용은 [DICOM 서비스에 대 한 API 버전 관리 설명서](api-versioning-dicom-service.md)에서 찾을 수 있습니다.

## <a name="api-design"></a>API 디자인

API는 `GET` 변경 피드와 상호 작용 하기 위한 두 개의 끝점을 노출 합니다. [다음과 같이](#example-usage-flow)변경 피드를 사용 하는 일반적인 흐름이 제공 됩니다.

동사 | 경로              | 반환     | 설명
:--- | :----------------- | :---------- | :---
GET  | /change피드        | JSON 배열  | [변경 피드 읽기](#read-change-feed)
GET  | /changefeed/latest | JSON 개체 | [변경 피드의 최신 항목 읽기](#get-latest-change-feed-item)

### <a name="object-model"></a>개체 모델

필드               | Type      | 설명
:------------------ | :-------- | :---
시퀀스            | int       | 페이징 (오프셋 via) 또는 앵커에 사용할 수 있는 시퀀스 ID입니다.
StudyInstanceUid    | 문자열    | 연구 인스턴스 UID
SeriesInstanceUid   | 문자열    | 계열 인스턴스 UID입니다.
SopInstanceUid      | 문자열    | Sop 인스턴스 UID
작업              | 문자열    | 수행 된 작업입니다. `create``delete`
타임스탬프           | Datetime  | 작업을 수행한 날짜와 시간 (UTC)입니다.
주               | 문자열    | [메타 데이터의 현재 상태입니다.](#states)
메타데이터            | 개체    | 필요에 따라 인스턴스가 존재 하는 경우 현재 DICOM 메타 데이터

#### <a name="states"></a>상태

시스템 상태    | 설명
:------- | :---
현재  | 이 인스턴스는 현재 버전입니다.
대체됨 | 이 인스턴스는 새 버전으로 대체 되었습니다.
deleted  | 이 인스턴스는 삭제 되었으며 서비스에서 더 이상 사용할 수 없습니다.

### <a name="read-change-feed"></a>변경 피드 읽기

**Route**:/changefeed? offset = {int} &limit = {int} &includemetadata = {**true**| false}
```
[
    {
        "Sequence": 1,
        "StudyInstanceUid": "{uid}",
        "SeriesInstanceUid": "{uid}",
        "SopInstanceUid": "{uid}",
        "Action": "create|delete",
        "Timestamp": "2020-03-04T01:03:08.4834Z",
        "State": "current|replaced|deleted",
        "Metadata": {
            "actual": "metadata"
        }
    },
    {
        "Sequence": 2,
        "StudyInstanceUid": "{uid}",
        "SeriesInstanceUid": "{uid}",
        "SopInstanceUid": "{uid}",
        "Action": "create|delete",
        "Timestamp": "2020-03-05T07:13:16.4834Z",
        "State": "current|replaced|deleted",
        "Metadata": {
            "actual": "metadata"
        }
    }
    ...
]
```
#### <a name="parameters"></a>매개 변수

속성            | Type | 설명
:-------------- | :--- | :---
offset          | int  | 반환할 값 앞에서 건너뛸 레코드 수입니다.
limit           | int  | 반환할 레코드 수 (기본값: 10, min: 1, max: 100)
includemetadata | bool | 메타 데이터를 포함할지 여부 (기본값: true)

### <a name="get-latest-change-feed-item"></a>최신 변경 피드 항목 가져오기

**경로**:/changefeed/latest? includemetadata = {**true**| false}

```
{
    "Sequence": 2,
    "StudyInstanceUid": "{uid}",
    "SeriesInstanceUid": "{uid}",
    "SopInstanceUid": "{uid}",
    "Action": "create|delete",
    "Timestamp": "2020-03-05T07:13:16.4834Z",
    "State": "current|replaced|deleted",
    "Metadata": {
        "actual": "metadata"
    }
}
```

#### <a name="parameters"></a>매개 변수

속성            | Type | 설명
:-------------- | :--- | :---
includemetadata | bool | 메타 데이터를 포함할지 여부 (기본값: true)

## <a name="usage"></a>사용량

### <a name="example-usage-flow"></a>사용 흐름 예제

다음은 DICOM 서비스 내의 인스턴스에서 다른 처리를 수행 하는 예제 응용 프로그램에 대 한 사용 흐름입니다.

1. 변경 피드를 모니터링 하려는 응용 프로그램이 시작 됩니다.
2. 시작 해야 하는 현재 상태가 있는지 여부를 확인 합니다.
   * 상태를 포함 하는 경우 저장 된 오프셋 (시퀀스)을 사용 합니다.
   * 시작 된 적이 없는 경우부터 시작 하려면를 사용 `offset=0` 합니다.  
   * 지금은를 처리 하려는 경우에 `/changefeed/latest` 는 마지막 시퀀스를 가져오도록 쿼리 합니다.
3. 지정 된 오프셋을 사용 하 여 변경 피드를 쿼리 합니다. `/changefeed?offset={offset}`
4. 항목이 있는 경우:
   * 추가 처리를 수행 합니다.  
   * 현재 상태를 업데이트 합니다.  
   * 2 단계에서 다시 시작 합니다.
5. 항목이 없으면 구성 된 시간 동안 절전 모드로 전환 되 고 2 단계에서 다시 시작 됩니다.

### <a name="other-potential-usage-patterns"></a>기타 잠재적인 사용 패턴

변경 피드 지원은 변경 된 개체를 기반으로 데이터를 처리 하는 시나리오에 적합 합니다. 예를 들어 다음을 수행 하는 데 사용할 수 있습니다.

* 변경 이벤트에 반응 하거나 만들어지거나 삭제 된 인스턴스를 기반으로 실행을 예약 하는 ML와 같은 연결 된 응용 프로그램 파이프라인을 빌드합니다.
* 개체에 발생 하는 변경 내용에 따라 비즈니스 분석 정보 및 메트릭을 추출 합니다.
* 변경 피드를 폴링하여 푸시 알림에 대 한 이벤트 원본을 만듭니다.

## <a name="summary"></a>요약

이 문서에서는 변경 피드의 REST API 디자인과 잠재적 사용 시나리오를 검토 했습니다. 변경 피드에 대 한 자세한 내용은 변경 [피드에서 변경 내용 가져오기](pull-dicom-changes-from-change-feed.md)를 참조 하세요.

## <a name="next-steps"></a>다음 단계

>[!div class="nextstepaction"]
>[DICOM 서비스 개요](dicom-services-overview.md)

