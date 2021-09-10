---
title: 검사 및 수집
description: 이 문서에서는 Azure Purview의 검사 및 수집에 대해 설명합니다.
author: nayenama
ms.author: nayenama
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 08/18/2021
ms.openlocfilehash: 140e87f4e03081825fe75ba73b7c5ebd33b20ada
ms.sourcegitcommit: ddac53ddc870643585f4a1f6dc24e13db25a6ed6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/18/2021
ms.locfileid: "122530779"
---
# <a name="scans-and-ingestion-in-azure-purview"></a>Azure Purview에서 검사 및 수집

이 문서에서는 Azure Purview의 검사 및 수집 기능에 대한 개요를 제공합니다. 이러한 기능은 Purview 계정을 원본에 연결하여 데이터 맵 및 데이터 카탈로그를 채우므로 Purview를 통해 데이터 탐색 및 관리를 시작할 수 있습니다.

## <a name="scanning"></a>검사

데이터 원본이 Purview 계정에 [등록](manage-data-sources.md)되면 다음 단계는 데이터 원본을 검사하는 것입니다. 검사 프로세스에서는 데이터 원본에 대한 연결을 설정하고 이름, 파일 크기, 열 등과 같은 기술 메타데이터를 캡처합니다. 또한 정형 데이터 원본에 대한 스키마를 추출하고, 스키마에 분류를 적용하고, [Purview 계정이 SCC(Microsoft 365 보안 및 규정 준수 센터)에 연결된 경우 민감도 레이블을 적용](create-sensitivity-label.md)합니다. 검사 프로세스는 즉시 실행하도록 트리거되거나 Purview 계정을 최신 상태로 유지하기 위해 주기적으로 실행하도록 예약할 수 있습니다.

각 검사에는 필요한 정보에 대한 원본만 검색하도록 적용할 수 있는 사용자 지정이 있습니다.

### <a name="scope-your-scan"></a>검사 범위 지정

원본을 검사할 때 전체 데이터 원본을 검사하거나 검사할 특정 엔터티(폴더/테이블)만 선택할 수 있습니다. 사용 가능한 옵션은 검사하는 원본에 따라 다르며, 일회성 검사와 예약된 검사 모두에 대해 정의할 수 있습니다.

예를 들어 [Azure SQL Database 검색을 만들고 실행](register-scan-azure-sql-database.md#creating-and-running-a-scan)할 때 검사할 테이블을 선택하거나 전체 데이터베이스를 선택할 수 있습니다.

### <a name="scan-rule-set"></a>검사 규칙 집합

검사 규칙 집합은 원본 중 하나에 대해 실행될 때 검사에서 찾을 정보의 종류를 확인합니다. 사용 가능한 규칙은 검사하는 원본의 종류에 따라 다르며, 검사해야 하는 [파일 형식](sources-and-scans.md#file-types-supported-for-scanning) 및 필요한 [분류](supported-classifications.md) 종류와 같은 사항을 포함합니다.

많은 데이터 원본 유형에 대해 이미 사용할 수 있는 [시스템 검사 규칙 집합](create-a-scan-rule-set.md#system-scan-rule-sets)이 있으며 [고유의 검사 규칙 집합을 만들어](create-a-scan-rule-set.md) 조직에 맞게 검사를 조정할 수도 있습니다.

## <a name="ingestion"></a>수집

그런 다음, 검사 프로세스에서 식별된 기술 메타데이터 또는 분류가 수집으로 전송됩니다. 수집 프로세스는 데이터 맵 채우기를 수행하며 Purview에서 관리됩니다.  수집은 검사의 입력을 분석하고 [리소스 집합 패턴을 적용하고](concept-resource-sets.md#how-azure-purview-detects-resource-sets), 사용 가능한 [계보](concept-data-lineage.md) 정보를 채운 다음, 데이터 맵을 자동으로 로드합니다. 자산/스키마는 수집이 완료된 후에만 검색하거나 큐레이팅할 수 있습니다. 따라서 검사가 완료되었지만 데이터 맵이나 카탈로그에 자산이 표시되지 않은 경우 수집 프로세스가 완료될 때까지 기다려야 합니다.

## <a name="next-steps"></a>다음 단계

자세한 내용이나 원본 검사에 대한 특정 지침은 아래 링크를 따르세요.

* 리소스 집합을 이해하려면 [리소스 집합 문서](concept-resource-sets.md)를 참조하세요.
* [Azure SQL Database 등록 및 검사 방법](register-scan-azure-sql-database.md#creating-and-running-a-scan)
* [Azure Purview의 계보](catalog-lineage-user-guide.md)
