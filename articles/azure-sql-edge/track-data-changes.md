---
title: Azure SQL Edge에서 데이터 변경 사항 추적
description: Azure SQL Edge에서 변경 내용 추적 및 변경 데이터 캡처에 대해 알아보기
keywords: ''
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: dddaad3e171c757b353deb81ffcb77cfbe706340
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98108265"
---
# <a name="track-data-changes-in-azure-sql-edge"></a>Azure SQL Edge에서 데이터 변경 사항 추적

Azure SQL Edge는 데이터베이스에서 데이터에 대한 변경 내용을 추적하는 [변경 내용 추적](/sql/relational-databases/track-changes/track-data-changes-sql-server#Tracking) 및 [변경 데이터 캡처](/sql/relational-databases/track-changes/track-data-changes-sql-server#Capture)와 같은 두 가지 SQL Server 기능을 제공합니다. 이러한 기능은 데이터베이스의 사용자 테이블에 적용된 데이터 수정 언어 변경 내용(삽입, 업데이트 및 삭제 작업)을 애플리케이션에서 확인할 수 있게 해줍니다. 동일한 데이터베이스에서 변경 데이터 캡처 및 변경 추적을 사용하도록 설정할 수 있습니다. 특별한 고려 사항은 필요하지 않습니다.

데이터베이스에서 변경된 데이터를 쿼리하는 기능은 일부 애플리케이션의 효율성을 위한 중요한 요구 사항입니다. 일반적으로 애플리케이션 개발자는 데이터 변경을 확인하기 위해 애플리케이션에서 트리거, 타임스탬프, 열 및 추가 테이블의 조합으로 사용자 지정 추적 방법을 구현해야 합니다. 이러한 애플리케이션을 만들기 위해서는 많은 구현 작업과 스키마 업데이트가 필요하며 높은 성능 오버헤드가 수반되는 경우도 많습니다.

데이터를 에지에서 클라우드 또는 데이터 센터로 정기적으로 이동해야 하는 IoT 솔루션의 경우 변경 내용 추적이 매우 유용할 수 있습니다. 사용자는 마지막 동기화의 변경 사항만 빠르고 효과적으로 쿼리하고 해당 변경 사항을 클라우드 또는 데이터 센터 대상에 업로드할 수 있습니다. 자세한 내용은 [변경 데이터 캡처 또는 변경 추적 사용의 이점](/sql/relational-databases/track-changes/track-data-changes-sql-server#benefits-of-using-change-data-capture-or-change-tracking)을 참조하세요. 

이 두 기능은 동일하지 않습니다. 자세한 내용은 [변경 데이터 캡처와 변경 추적 간의 기능 차이점](/sql/relational-databases/track-changes/track-data-changes-sql-server#feature-differences-between-change-data-capture-and-change-tracking)을 참조하세요.

## <a name="change-data-capture"></a>변경 데이터 캡처

이 기능의 작동 방식에 대한 자세한 내용은 [변경 데이터 캡처 정보](/sql/relational-databases/track-changes/about-change-data-capture-sql-server)를 참조하세요.

이 기능을 사용하도록 설정하거나 사용하지 않도록 설정하는 방법을 이해하려면 [변경 데이터 캡처를 사용하거나 사용하지 않도록 설정](/sql/relational-databases/track-changes/enable-and-disable-change-data-capture-sql-server)을 참조하세요.

이 기능을 관리하고 모니터링하려면 [변경 데이터 캡처 관리 및 모니터링](/sql/relational-databases/track-changes/administer-and-monitor-change-data-capture-sql-server)을 참조하세요.

변경된 데이터를 쿼리하고 작업하는 방법에 대해 자세히 알아보려면 [변경 데이터 작업](/sql/relational-databases/track-changes/work-with-change-data-sql-server)을 참조하세요.

> [!NOTE]
> Azure SQL Edge는 CLR에 종속된 변경 데이터 캡처 기능을 지원하지 않습니다.

## <a name="change-tracking"></a>변경 내용 추적

이 기능의 작동 방식에 대한 자세한 내용은 [변경 사항 추적 정보](/sql/relational-databases/track-changes/about-change-tracking-sql-server)를 참조하세요.

이 기능을 사용하도록 설정하거나 사용하지 않도록 설정하는 방법을 이해하려면 [변경 추적 사용 또는 사용 안 함](/sql/relational-databases/track-changes/enable-and-disable-change-tracking-sql-server)을 참조하세요.

이 기능을 모니터링 및 관리하려면 [변경 추적 관리 및 모니터링](/sql/relational-databases/track-changes/manage-change-tracking-sql-server)을 참조하세요.

변경된 데이터를 쿼리하고 작업하는 방법에 대해 자세히 알아보려면 [변경 데이터 작업](/sql/relational-databases/track-changes/work-with-change-tracking-sql-server)을 참조하세요.

## <a name="temporal-tables"></a>임시 테이블

Azure SQL Edge는 SQL Server의 temporal 테이블 기능도 지원합니다. 이 기능(*시스템 버전 temporal 테이블* 이라고도 함)은 특정 시점에 테이블에 저장된 데이터에 대한 정보를 제공하기 위한 기본 제공 지원을 제공합니다. 이 기능은 단순히 현재 시점에 올바른 데이터에 대한 정보만 제공하는 것이 아닙니다.

시스템 버전 temporal 테이블은 데이터 변경의 전체 기록을 유지하고 손쉬운 특정 시점 분석을 허용하도록 설계된 일종의 사용자 테이블입니다. 이 유형의 temporal 테이블은 각 행의 유효 기간이 시스템(즉, 데이터베이스 엔진)에 의해 관리되기 때문에 시스템 버전 temporal 테이블이라고 합니다.

모든 temporal 테이블에는 명시적으로 정의된 두 개의 열이 있으며, 각 열의 `datetime2` 데이터 형식은 입니다. 이러한 열을 *기간* 열이라고 합니다. 시스템은 이러한 기간 열을 독점적으로 사용하여 행이 수정될 때마다 각 행의 유효 기간을 기록합니다.

이러한 기간 열 외에도 temporal 테이블은 미러된 스키마를 사용하는 다른 테이블에 대한 참조를 포함합니다. 시스템에서는 이 테이블을 사용하여 temporal 테이블의 행이 업데이트되거나 삭제될 때마다 이전 버전의 행을 자동으로 저장합니다. 이 추가 테이블을 *기록* 테이블이라고 하며, 현재(실제) 행 버전을 저장하는 주 테이블을 *현재* 테이블 또는 temporal 테이블이라고 합니다. temporal 테이블을 만드는 동안 사용자는 기존 기록 테이블을 지정하거나(스키마와 호환되어야 함) 시스템이 기본 기록 테이블을 만들도록 할 수 있습니다.

자세한 내용은 [Temporal 테이블](/sql/relational-databases/tables/temporal-tables)을 참조하세요.

## <a name="next-steps"></a>다음 단계

- [Azure SQL Edge에서 데이터 스트리밍](stream-data.md)
- [Azure SQL Edge에서 ONNX를 사용한 기계 학습 및 AI](onnx-overview.md)
- [Azure SQL Edge로 복제 구성](configure-replication.md)
- [Azure SQL Edge에서 데이터베이스 백업 및 복원](backup-restore.md)
