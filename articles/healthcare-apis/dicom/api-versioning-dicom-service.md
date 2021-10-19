---
title: DICOM 서비스에 대한 API 버전 관리 - Azure Healthcare API
description: 이 가이드에서는 DICOM 서비스에 대한 API 버전 정책에 대한 개요를 제공합니다.
services: healthcare-apis
author: stevewohl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 08/04/2021
ms.author: aersoy
ms.openlocfilehash: c07a963a9dfeaa8e05d79c43cff18ab934f44f1c
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122435437"
---
# <a name="api-versioning-for-dicom-service"></a>DICOM 서비스에 대한 API 버전

> [!IMPORTANT]
> Azure Healthcare API는 현재 미리 보기로 제공됩니다. [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)에는 베타 또는 미리 보기로 제공되거나 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 추가 약관이 포함되어 있습니다.

이 참조 가이드에서는 DICOM 서비스에 대한 API 버전 정책에 대한 개요를 제공합니다. 

모든 버전의 DICOM API는 항상 DICOMweb™ Standard 사양을 준수하지만, 버전은 [DICOM 준수 문](dicom-services-conformance-statement.md)에 따라 다른 API를 노출할 수 있습니다.

## <a name="specifying-version-of-rest-api-in-requests"></a>요청에서 REST API 버전 지정

REST API 버전은 다음 예제와 같이 요청 URL에 명시적으로 지정해야 합니다.

`<service_url>/v<version>/studies`

현재 버전이 없는 경로는 계속 지원됩니다. 예를 들어 `<service_url>/studies` 에는 v1.0-prerelease로 버전을 지정하는 것과 동일한 동작이 있습니다. 그러나 URL을 통해 모든 요청에서 버전을 지정하는 것이 좋습니다.

## <a name="supported-versions"></a>지원되는 버전

현재 지원되는 버전은 다음과 같습니다.

* v1.0-prerelease

지원되는 버전에 대한 OpenApi 문서는 다음 URL에서 찾을 수 있습니다.
 
`<service_url>/{version}/api.yaml`

## <a name="prerelease-versions"></a>Prerelease 버전

레이블이 "시험판"인 API 버전은 버전이 프로덕션에 사용할 준비가 되지 않았으며 테스트 환경에서만 사용해야 함을 나타냅니다. 이러한 엔드포인트는 통지 없이 주요 변경 내용을 경험할 수 있습니다.

## <a name="how-versions-are-incremented"></a>버전 증가 방법

현재 이전 버전과 호환되지 않는 것으로 간주되는 호환성이 저하되는 변경이 있을 때마다 주 버전만 증가합니다. 모든 부 버전은 0으로 암시됩니다. 모든 버전은 `Major.0` 형식입니다.

다음은 주요 변경 내용의 몇 가지 예입니다(주 버전이 증가함).

1. 엔드포인트 이름 바꾸기 또는 제거
2. 매개 변수 제거 또는 필수 매개 변수 추가
3. 상태 코드 변경
4. 응답에서 속성을 삭제하거나 응답 형식을 전혀 변경하지만 응답에 속성을 추가해도 괜찮습니다.
5. 속성의 형식 변경
6. foo를 수행하는 데 사용되는 비즈니스 논리의 변경과 같이 API가 변경되는 경우의 동작이지만 이제 막대를 수행합니다.

주요 변경 내용(버전이 증가하지 않음):

1. nullable이거나 기본값이 있는 속성을 추가합니다.
2. 응답 모델에 속성 추가
3. 속성 순서 변경

## <a name="header-in-response"></a>응답의 헤더

ReportApiVersions가 켜져 있습니다. 즉, 적절한 경우 api-supported-versions 및 api-deprecated-versions 헤더를 반환합니다.

* api-supported-versions는 요청된 API에 대해 지원되는 버전을 나열합니다. 로 주석이 추가된 엔드포인트를 호출할 때만 `ApiVersion("<someVersion>")` 반환됩니다.

* api-deprecated-versions는 요청된 API에 대해 더 이상 사용되지 않는 버전을 나열합니다. 로 주석이 추가된 엔드포인트를 호출할 때만 `ApiVersion("<someVersion>", Deprecated = true)` 반환됩니다.

예:

ApiVersion("1.0")

ApiVersion("1.0-prerelease", Deprecated = true)

[![API 지원 및 사용 안 됨 버전 ](media/api-supported-deprecated-versions.png) ](media/api-supported-deprecated-versions.png#lightbox)

