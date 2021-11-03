---
title: 검사 및 수집
description: 이 문서에서는 Azure Purview의 검사 및 수집에 대해 설명합니다.
author: nayenama
ms.author: nayenama
ms.service: purview
ms.subservice: purview-data-map
ms.topic: conceptual
ms.date: 08/18/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: af12878750c6976a3ce3b459d9cf1ff6b262c112
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131024034"
---
# <a name="scans-and-ingestion-in-azure-purview"></a>Azure Purview에서 검사 및 수집

이 문서에서는 Azure Purview의 검사 및 수집 기능에 대한 개요를 제공합니다. 이러한 기능은 Purview 계정을 원본에 연결하여 데이터 맵 및 데이터 카탈로그를 채우므로 Purview를 통해 데이터 탐색 및 관리를 시작할 수 있습니다.

## <a name="scanning"></a>검사

데이터 원본이 Purview 계정에 [등록](manage-data-sources.md)되면 다음 단계는 데이터 원본을 검사하는 것입니다. 검사 프로세스에서는 데이터 원본에 대한 연결을 설정하고 이름, 파일 크기, 열 등과 같은 기술 메타데이터를 캡처합니다. 또한 정형 데이터 원본에 대한 스키마를 추출하고, 스키마에 분류를 적용하고, [Purview 계정이 SCC(Microsoft 365 보안 및 규정 준수 센터)에 연결된 경우 민감도 레이블을 적용](create-sensitivity-label.md)합니다. 검사 프로세스는 즉시 실행하도록 트리거되거나 Purview 계정을 최신 상태로 유지하기 위해 주기적으로 실행하도록 예약할 수 있습니다.

각 검사에는 필요한 정보에 대한 원본만 검색하도록 적용할 수 있는 사용자 지정이 있습니다.

### <a name="choose-an-authentication-method-for-your-scans"></a>검색에 대 한 인증 방법 선택

부서의 범위은 기본적으로 안전 합니다. 암호 또는 암호는 부서의 범위에 직접 저장 되지 않으므로 원본에 대 한 인증 방법을 선택 해야 합니다. 부서의 범위 계정을 인증 하는 네 가지 방법이 있지만 각 데이터 원본에 대 한 모든 방법이 지원 되는 것은 아닙니다.
 - 관리 ID
 - 서비스 주체
 - SQL 인증
 - 계정 키 또는 기본 인증

가능한 경우 관리 되는 Id는 개별 데이터 원본에 대 한 자격 증명을 저장 하 고 관리할 필요가 없기 때문에 기본 인증 방법입니다. 이렇게 하면 사용자와 팀이 검색에 대 한 인증을 설정 하 고 문제를 해결 하는 데 드는 시간을 크게 줄일 수 있습니다. 부서의 범위 계정에 관리 되는 id를 사용 하도록 설정 하면 Azure Active Directory에서 id가 만들어지고 계정의 수명 주기와 연결 됩니다. 

### <a name="scope-your-scan"></a>검사 범위 지정

원본을 검사할 때 전체 데이터 원본을 검사하거나 검사할 특정 엔터티(폴더/테이블)만 선택할 수 있습니다. 사용 가능한 옵션은 검사하는 원본에 따라 다르며, 일회성 검사와 예약된 검사 모두에 대해 정의할 수 있습니다.

예를 들어 [Azure SQL Database 검색을 만들고 실행](register-scan-azure-sql-database.md#creating-the-scan)할 때 검사할 테이블을 선택하거나 전체 데이터베이스를 선택할 수 있습니다.

### <a name="scan-rule-set"></a>검사 규칙 집합

검사 규칙 집합은 원본 중 하나에 대해 실행될 때 검사에서 찾을 정보의 종류를 확인합니다. 사용 가능한 규칙은 검사하는 원본의 종류에 따라 다르며, 검사해야 하는 [파일 형식](sources-and-scans.md#file-types-supported-for-scanning) 및 필요한 [분류](supported-classifications.md) 종류와 같은 사항을 포함합니다.

많은 데이터 원본 유형에 대해 이미 사용할 수 있는 [시스템 검사 규칙 집합](create-a-scan-rule-set.md#system-scan-rule-sets)이 있으며 [고유의 검사 규칙 집합을 만들어](create-a-scan-rule-set.md) 조직에 맞게 검사를 조정할 수도 있습니다.

### <a name="schedule-your-scan"></a>검색 예약

부서의 범위를 사용 하면 선택한 특정 시간에 매주 또는 매월 스캔을 선택할 수 있습니다. 주별 검색은 개발 중이거나 자주 변경 되는 구조의 데이터 원본에 적합 합니다. 월간 검색은 자주 변경 되지 않는 데이터 원본에 더 적합 합니다. 원본에 대 한 계산 요구가 낮은 시간을 식별 하기 위해 검색 하려는 원본의 관리자와 작업 하는 것이 좋습니다.

### <a name="how-scans-detect-deleted-assets"></a>검사를 통해 삭제된 자산을 검색하는 방법

Azure Purview 카탈로그는 검사를 실행할 때에만 데이터 저장소의 상태를 인식합니다. 카탈로그는 파일, 테이블 또는 컨테이너가 삭제되었는지 여부를 알 수 있도록 마지막 검사 출력과 현재 검사 출력을 비교합니다. 예를 들어 Azure Data Lake Storage Gen2 계정을 마지막으로 검사한 경우 *폴더1* 이라는 폴더를 포함한다고 가정합니다. 동일한 계정을 다시 검사하면 *폴더1* 이 누락됩니다. 따라서 카탈로그는 폴더가 삭제된 것으로 가정합니다.

#### <a name="detecting-deleted-files"></a>삭제된 파일 검색

누락된 파일을 검색하는 논리는 동일한 사용자와 다른 사용자가 여러 검색을 수행하는 데 사용할 수 있습니다. 예를 들어 사용자가 A, B, C 폴더의 Data Lake Storage Gen2 데이터 저장소에서 일회성 검사를 실행한다고 가정합니다. 나중에 동일한 계정에 있는 다른 사용자가 동일한 데이터 저장소의 폴더 C, D 및 E에 대해 다른 일회성 검사를 실행합니다. C 폴더가 두 번 검사되었으므로 카탈로그는 이것이 삭제할 수 있는 것인지 확인합니다. 그러나 폴더 A, B, D 및 E는 한 번만 검사되었으며 카탈로그는 그것들이 삭제된 자산인지 확인하지 않습니다.

삭제된 파일을 카탈로그에서 유지하려면 일반 검사를 실행해야 합니다. 검사 간격은 중요합니다. 카탈로그는 다른 검사를 실행할 때까지 삭제된 자산을 검색할 수 없기 때문입니다. 따라서 특정 저장소에서 한 달에 한 번 검사를 실행하는 경우 나중에 한 달에 검사를 실행할 때까지 카탈로그는 해당 저장소에서 삭제된 데이터 자산을 검색할 수 없습니다.

Data Lake Storage Gen2 같은 대량 데이터 저장소를 열거하는 경우 정보가 누락될 수 있는 방법이 여러 가지(열거 오류 및 끊어진 이벤트 등) 있습니다. 특정 검사에서 파일이 만들어지거나 삭제되지 않았을 수 있습니다. 따라서 카탈로그는 특정 파일이 삭제된 것이 확실한 경우를 제외하고는 카탈로그에서 삭제하지 않습니다. 이 전략은 검사된 데이터 저장소에 없는 파일이 여전히 카탈로그에 있는 경우 오류가 발생할 수 있음을 의미합니다. 일부 경우에 데이터 저장소는 삭제된 특정 자산을 포착하기 전에 2-3번 검사해야 할 수 있습니다.

> [!NOTE]
> 삭제 하도록 표시 된 자산은 성공적으로 검색 된 후 삭제 됩니다. 삭제 된 자산은 처리 및 제거 되기 전에 카탈로그에서 계속 표시 될 수 있습니다.

## <a name="ingestion"></a>수집

그런 다음, 검사 프로세스에서 식별된 기술 메타데이터 또는 분류가 수집으로 전송됩니다. 수집 프로세스는 데이터 맵 채우기를 수행하며 Purview에서 관리됩니다.  수집은 검사의 입력을 분석하고 [리소스 집합 패턴을 적용하고](concept-resource-sets.md#how-azure-purview-detects-resource-sets), 사용 가능한 [계보](concept-data-lineage.md) 정보를 채운 다음, 데이터 맵을 자동으로 로드합니다. 자산/스키마는 수집이 완료된 후에만 검색하거나 큐레이팅할 수 있습니다. 따라서 검사가 완료되었지만 데이터 맵이나 카탈로그에 자산이 표시되지 않은 경우 수집 프로세스가 완료될 때까지 기다려야 합니다.

## <a name="next-steps"></a>다음 단계

자세한 내용이나 원본 검사에 대한 특정 지침은 아래 링크를 따르세요.

* 리소스 집합을 이해하려면 [리소스 집합 문서](concept-resource-sets.md)를 참조하세요.
* [Azure SQL Database 등록 및 검사 방법](register-scan-azure-sql-database.md#creating-the-scan)
* [Azure Purview의 계보](catalog-lineage-user-guide.md)
