---
title: 지원되는 데이터 원본 및 파일 형식
description: 이 문서에서는 Purview에서 지원되는 데이터 원본 및 파일 형식에 대한 개념적 세부 정보를 제공합니다.
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 11/24/2020
ms.custom: references_regions
ms.openlocfilehash: 5ed4ae8fd3de642332cc59e7514b05b6154dd556
ms.sourcegitcommit: 48500a6a9002b48ed94c65e9598f049f3d6db60c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/26/2021
ms.locfileid: "129054454"
---
# <a name="supported-data-sources-and-file-types-in-azure-purview"></a>Azure Purview에서 지원되는 데이터 원본 및 파일 형식

이 문서에서는 Purview에서 지원되는 데이터 원본, 파일 형식 및 검사 개념에 대해 설명합니다.

## <a name="supported-data-sources"></a>지원되는 데이터 원본

Purview는 [여기](purview-connector-overview.md)에 나열된 모든 데이터 원본을 지원합니다.

## <a name="file-types-supported-for-scanning"></a>검사가 지원되는 파일 형식

해당하는 경우 스키마 추출 및 분류를 위해 다음 파일 형식이 검사에 지원됩니다.

- 확장 프로그램에서 지원하는 구조적 파일 형식: AVRO, ORC, PARQUET, CSV, JSON, PSV, SSV, TSV, TXT, XML, GZIP
- 확장 프로그램에서 지원하는 문서 파일 형식: DOC, DOCM, DOCX, DOT, ODP, ODS, ODT, PDF, POT, PPS, PPSX, PPT, PPTM, PPTX, XLC, XLS, XLSB, XLSM, XLSX, XLT
- Purview는 사용자 지정 파일 확장명 및 사용자 지정 파서도 지원합니다.
 
> [!Note]
> 모든 Gzip 파일은 내부 단일 csv 파일에 매핑되어야 합니다. Gzip 파일에는 시스템 및 사용자 지정 분류 규칙이 적용됩니다. 현재는 내부 여러 파일 또는 csv 이외의 파일 형식에 매핑된 gzip 파일 검사를 지원하지 않습니다. 또한 Purview 스캐너는 스키마 추출 및 분류를 위해 snappy 압축 PARQUET 형식 검사를 지원합니다. 

> [!Note]
> 부서의 범위 스캐너는 AVRO, ORC 및 PARQUET 파일 형식의 복잡 한 데이터 형식 (예: 맵, 목록, 구조체)을 스키마 추출을 지원 하지 않습니다.   

## <a name="sampling-within-a-file"></a>파일 내 샘플링

Purview 용어에서
- L1 검사: 파일 이름, 크기, 정규화된 이름과 같은 기본 정보 및 메타데이터를 추출합니다.
- L2 검사: 구조적 파일 형식 및 데이터베이스 테이블에 대한 스키마를 추출합니다.
- L3 검사: 해당하는 경우 스키마를 추출하고 샘플링된 파일을 시스템 및 사용자 지정 분류 규칙에 적용합니다.

모든 구조적 파일 형식에 대해 Purview 스캐너는 다음과 같은 방법으로 파일을 샘플링합니다.

- 구조적 파일 형식의 경우 각 열에서 상위 128개 행 또는 처음 1MB 중 더 낮은 행을 샘플링합니다.
- 문서 파일 형식의 경우 각 파일의 처음 20MB를 샘플링합니다.
    - 문서 파일이 20MB보다 큰 경우 심층 검사가 적용되지 않습니다(분류에 따름). 이 경우 Purview는 파일 이름 및 정규화된 이름과 같은 기본 메타데이터만 캡처합니다.
- **표 형식 데이터 원본(SQL, CosmosDB)** 의 경우 상위 128개 행을 샘플링합니다. 

## <a name="resource-set-file-sampling"></a>리소스 집합 파일 샘플링

Purview에서 파티션 파일의 폴더 또는 그룹이 시스템 리소스 집합 정책 또는 고객 정의 리소스 집합 정책과 일치하는 경우 해당 *리소스 집합* 으로 검색됩니다. 리소스 집합이 검색되면 Purview는 포함된 각 폴더를 샘플링합니다. [여기](concept-resource-sets.md)에서 리소스 집합에 대해 자세히 알아보세요.

파일 형식별 리소스 집합에 대한 파일 샘플링:

- **구분된 파일 형식(CSV, PSV, SSV, TSV)** - 100개 파일 중 1개가 '리소스 집합'으로 간주되는 파티션 파일의 폴더 또는 그룹 내에서 샘플링됩니다(L3 검사).
- **데이터 레이크 파일 형식(Parquet, Avro, Orc)** - 18446744073709551615(최대 길이) 파일 중 1개가 *리소스 집합* 으로 간주되는 파티션 파일의 폴더 또는 그룹 내에서 샘플링됩니다(L3 검사).
- **다른 구조적 파일 형식(JSON, XML, TXT)** - 100개 파일 중 1개가 '리소스 집합'으로 간주되는 파티션 파일의 폴더 또는 그룹 내에서 샘플링됩니다(L3 검사).
- **SQL 개체 및 CosmosDB 엔터티** -각 파일은 L3에서 검사됩니다.
- **문서 파일 형식** - 각 파일은 L3에서 검사됩니다. 리소스 집합 패턴은 이러한 파일 형식에 적용되지 않습니다.

## <a name="classification"></a>분류

모든 206 시스템 분류 규칙은 구조적 파일 형식에 적용됩니다. MCE 분류 규칙만 문서 파일 형식에 적용됩니다(데이터 검사 기본 regex 패턴, 블룸 필터 기반 검색에는 적용되지 않음). 지원되는 분류에 대한 자세한 내용은 [Azure Purview에서 지원되는 분류](supported-classifications.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

- [부서의 범위에서 검색 및 수집](concept-scans-and-ingestion.md)
- [Azure Purview에서 데이터 원본 관리(미리 보기)](manage-data-sources.md)
