---
title: Visual Studio 2019 설치
description: Synapse SQL용 Visual Studio 및 SSDT(SQL Server 개발 도구) 설치
ms.custom: vs-azure, azure-synapse
ms.workload: azure-vs
author: WilliamDAssafMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 05/11/2020
ms.author: wiassaf
ms.reviewer: igorstan
ms.openlocfilehash: 7109219b01f6f19b3356d2185516c46097a21cb1
ms.sourcegitcommit: 66b6e640e2a294a7fbbdb3309b4829df526d863d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/01/2021
ms.locfileid: "133361114"
---
# <a name="getting-started-with-visual-studio-2019"></a>Visual Studio 2019 시작

Visual Studio **2019** SSDT(SQL Server Data Tools)는 다음을 수행할 수 있는 단일 도구입니다.

- 애플리케이션 연결, 쿼리 및 개발
- 개체 탐색기를 활용하여 테이블, 뷰, 저장 프로시저 등을 비롯한 데이터 모델의 모든 개체를 시각적으로 탐색합니다.
- 개체에 대한 T-SQL DDL(데이터 정의 언어) 스크립트 생성
- SSDT 데이터베이스 프로젝트를 통해 상태 기반 방법을 사용하여 데이터 웨어하우스 개발
- Azure Repos를 사용하는 Git과 같은 소스 제어 시스템과 데이터베이스 프로젝트 통합
- Azure DevOps와 같은 자동화 서버를 사용하여 연속 통합 및 배포 파이프라인 설정

## <a name="install-visual-studio-2019"></a>Visual Studio 2019 설치

Visual Studio **16.3 이상** 을 다운로드하고 설치하려면 [Visual Studio 2019 다운로드](https://visualstudio.microsoft.com/downloads/)를 참조하세요. 설치하는 동안 데이터 스토리지 및 처리 워크로드를 선택합니다. Visual Studio 2019에서는 독립 실행형 SSDT 설치가 더 이상 필요하지 않습니다.

## <a name="unsupported-features-in-ssdt"></a>SSDT에서 지원되지 않는 기능

Synapse SQL의 기능 릴리스에 SSDT에 대한 지원이 포함되지 않는 경우가 있습니다. 현재 지원되지 않는 기능은 다음과 같습니다.


- [워크로드 관리](sql-data-warehouse-workload-management.md) - 워크로드 그룹 및 분류자
- [행 수준 보안](/sql/relational-databases/security/row-level-security?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)(테이블 반환 함수 포함)
  - 지원되는 기능을 가져오려면 [지원 티켓 또는 투표](https://feedback.azure.com/d365community/idea/a9192b4c-0b25-ec11-b6e6-000d3a4f07b8)를 제출합니다.
  - 지원되는 기능을 가져오려면 [지원 티켓 또는 투표](https://feedback.azure.com/d365community/idea/ab192b4c-0b25-ec11-b6e6-000d3a4f07b8)를 제출합니다.
- 다음과 같은 특정 T-SQL 기능:
   - [STRING_AGG](/sql/t-sql/functions/string-agg-transact-sql) 문자열 함수의 *WITHIN GROUP* 절.

## <a name="next-steps"></a>다음 단계

이제 최신 버전의 SSDT가 설치되었으므로 SQL 풀에 [연결](sql-data-warehouse-query-visual-studio.md)할 수 있습니다.