---
title: sys.external_streaming_jobs(Transact-SQL) - Azure SQL Edge
description: Azure SQL Edge에서 sys.external_streaming_jobs 사용에 대해 알아봅니다.
keywords: sys.external_streaming_jobs, SQL Edge
services: sql-edge
ms.service: sql-edge
ms.topic: reference
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2019
ms.openlocfilehash: 0a5b943340353b86d0ae6d2002bd2c0e31d86350
ms.sourcegitcommit: 66b6e640e2a294a7fbbdb3309b4829df526d863d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/01/2021
ms.locfileid: "133367928"
---
# <a name="sysexternal_streaming_jobs-transact-sql"></a>sys.external_streaming_jobs(Transact-SQL)

데이터베이스 범위 내에서 만든 각 외부 스트리밍 작업에 대해 하나의 행을 반환합니다.

|열 이름|데이터 형식|Description|  
|-----------------|---------------|-----------------|
|**name**|**sysname**|스트림의 이름입니다. 데이터베이스 내에서 고유합니다.|
|**object_id**|**int**|스트리밍 개체의 개체 ID 번호입니다. 데이터베이스 내에서 고유합니다.|
|**principal_id**|**int**|이 어셈블리를 소유하는 주체의 ID입니다.|
|**schema_id**|**int**| 개체를 포함하는 스키마의 ID입니다.|
|**parent_object_id**|**id**| 이 스트림의 부모 개체에 대한 개체 ID 번호입니다. 현재 구현에서 이 값은 항상 null입니다.|
|**type**|**char(2)**|개체 유형입니다. 스트림 개체의 경우 형식은 항상 ‘EJ’입니다.|
|**type_desc**|**nvarchar(60)**| 개체 형식에 대한 설명입니다. 스트림 개체의 경우 형식은 항상 ‘EXTERNAL_STREAMING_JOB’입니다.|
|**create_date**|**datetime**| 개체를 만든 날짜입니다.|
|**modify_date**|**datetime**| 현재 구현에서 이 값은 스트림 개체의 create_date와 동일합니다. |
|**is_ms_shipped**|**bit**| 내부 구성 요소에서 만든 개체입니다.|  
|**is_published**|**bit**| 개체가 게시됩니다.|  
|**is_schema_published**|**bit**|개체의 스키마만 게시됩니다.|
|**uses_ansi_nulls**|**bit**| 스트림 개체는 SET ANSI_NULLS 데이터베이스 옵션을 ON으로 설정하여 생성되었습니다.|
|**statement**|**nvarchar(max)**| 스트리밍 작업에 대한 스트림 분석 쿼리 텍스트입니다. 자세한 내용은 [sp_create_streaming_job](overview.md)을 참조하세요. |
|**status**|**int**| 스트리밍 작업의 현재 상태입니다. 사용 가능한 값은 다음과 같습니다. <br /><br /> **Created** = 0. 스트리밍 작업이 만들어졌지만 아직 시작되지 않았습니다. <br /><br /> **Starting** = 1. 스트리밍 작업이 시작 단계에 있습니다. <br /><br /> **Failed** = 6. 스트림 작업이 실패했습니다. 일반적으로 처리하는 동안 치명적인 오류가 발생했음을 나타냅니다. <br /><br /> **Stopped** = 4. 스트리밍 작업이 중지되었습니다. <br /><br /> **Idle** = 7. 스트리밍 작업이 실행 중이지만 처리할 입력이 없습니다. <br /><br /> **Processing** = 8. 스트리밍 작업이 실행 중이며 입력을 처리하고 있습니다. 이 상태는 스트리밍 작업이 정상임을 나타냅니다. <br /><br /> **Degraded** = 9. 스트리밍 작업이 실행 중이지만, 입력을 처리하는 중에 일부 치명적이지 않은 입력/출력 직렬화/역직렬화 오류가 발생했습니다. 입력 작업은 계속 실행되지만 오류가 발생하는 입력은 삭제됩니다.|

## <a name="permissions"></a>사용 권한

사용자가 소유하고 있거나 사용 권한을 부여 받은 보안 개체에 대해서만 카탈로그 뷰의 메타데이터를 볼 수 있습니다. 자세한 내용은 [Metadata Visibility Configuration](/sql/relational-databases/security/metadata-visibility-configuration/)을 참조하세요.

## <a name="see-also"></a>참고 항목

- [T-SQL 스트리밍 카탈로그 뷰](overview.md)
- [카탈로그 뷰(Transact-SQL)](/sql/relational-databases/system-catalog-views/catalog-views-transact-sql/)
- [시스템 뷰(Transact-SQL)](/sql/t-sql/language-reference/)

