---
title: Machine Learning Services의 주요 차이점
description: 이 문서에서는 Azure SQL Managed Instance의 Machine Learning Services와 SQL Server Machine Learning Services 사이의 주요 차이점을 설명합니다.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: machine-learning
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: rothja
ms.author: jroth
ms.reviewer: mathoma, davidph
manager: cgronlun
ms.date: 03/17/2021
ms.openlocfilehash: 68590dd5eeba49f71c8548389848e0db7ceb7e92
ms.sourcegitcommit: dcf3424d7149fceaea0340eb0657baa2c27882a5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/30/2021
ms.locfileid: "133269734"
---
# <a name="key-differences-between-machine-learning-services-in-azure-sql-managed-instance-and-sql-server"></a>Azure SQL Managed Instance와 SQL Server의 Machine Learning Services에 대한 주요 차이점

이 문서에서는 [Azure SQL Managed Instance의 Machine Learning Services](machine-learning-services-overview.md)와 [SQL Server Machine Learning Services](/sql/advanced-analytics/what-is-sql-server-machine-learning) 사이에 나타나는 기능의 주요 차이점을 몇 가지 설명합니다.

## <a name="language-support"></a>언어 지원

SQL Managed Instance와 SQL Server의 Machine Learning Services에서는 Python 및 R [확장성 프레임워크](/sql/machine-learning/concepts/extensibility-framework)를 지원합니다. SQL Managed Instance의 주요 차이점은 다음과 같습니다.

- Python 및 R만 지원됩니다. Java와 같은 외부 언어를 추가할 수 없습니다.

- 초기 버전 Python 및 R의 차이점은 다음과 같습니다.

  | 플랫폼                   | Python 런타임 버전           | R 런타임 버전                   |
  |----------------------------|----------------------------------|--------------------------------------|
  | Azure SQL Managed Instance | 3.7.2                            | 3.5.2                                |
  | SQL Server 2019            | 3.7.1                            | 3.5.2                                |
  | SQL Server 2017            | 3.5.2 및 3.7.2(CU22 이상) | 3.3.3 및 3.5.2(CU22 이상)     |
  | SQL Server 2016            | 사용할 수 없음                    | 3.2.2 및 3.5.2(SP2 CU14 이상) |

## <a name="python-and-r-packages"></a>Python 및 R 패키지

설치 또는 사용 시 OS API에 액세스해야 하거나 외부 런타임(예: Java)에 의존하는 패키지의 SQL Managed Instance에서는 지원되지 않습니다.

Python 및 R 패키지를 관리하는 방법에 대한 자세한 내용은 다음을 참조하세요.

- [Python 패키지 정보 가져오기](/sql/machine-learning/package-management/python-package-information?context=%2fazure%2fazure-sql%2fmanaged-instance%2fcontext%2fml-context&preserve-view=true&view=azuresqldb-mi-current)
- [R 패키지 정보 가져오기](/sql/machine-learning/package-management/r-package-information?context=%2fazure%2fazure-sql%2fmanaged-instance%2fcontext%2fml-context&preserve-view=true&view=azuresqldb-mi-current)

## <a name="resource-governance"></a>리소스 거버넌스

SQL Managed Instance에서는 [Resource Governor](/sql/relational-databases/resource-governor/resource-governor?view=azuresqldb-mi-current&preserve-view=true)를 통해 R 리소스를 제한할 수 없으며, 외부 리소스 풀은 지원되지 않습니다.

확장성을 사용하도록 설정한 경우 기본적으로 R 리소스는 사용 가능한 SQL Managed Instance 리소스의 최대 20%로 설정됩니다. 이 기본 백분율을 변경하려면 [https://azure.microsoft.com/support/create-ticket/](https://azure.microsoft.com/support/create-ticket/)에서 Azure 지원 티켓을 만듭니다.

확장성은 다음의 SQL 명령을 통해 사용하도록 설정됩니다(SQL Managed Instance가 다시 시작되고 몇 초간 사용할 수 없음).

```sql
sp_configure 'external scripts enabled', 1;
RECONFIGURE WITH OVERRIDE;
```

확장성을 사용하지 않도록 설정하고 메모리/CPU 리소스의 100%를 SQL Server로 복원하려면 다음 명령을 사용합니다.

```sql
sp_configure 'external scripts enabled', 0;
RECONFIGURE WITH OVERRIDE;
```

SQL Managed Instance에서 사용할 수 있는 전체 리소스는 선택한 서비스 계층에 따라 다릅니다. 자세한 내용은 [Azure SQL Database 구매 모델](../database/purchasing-models.md)을 참조하세요.

### <a name="insufficient-memory-error"></a>메모리 부족 오류

메모리 사용량은 R 스크립트에서 사용되는 양 및 실행되는 병렬 쿼리 수에 따라 달라집니다. R에 사용할 수 있는 메모리가 부족하면 오류 메시지가 표시됩니다. 일반적인 오류 메시지는 다음과 같습니다.

- `Unable to communicate with the runtime for 'R' script for request id: *******. Please check the requirements of 'R' runtime`
- `'R' script error occurred during execution of 'sp_execute_external_script' with HRESULT 0x80004004. ...an external script error occurred: "..could not allocate memory (0 Mb) in C function 'R_AllocStringBuffer'"`
- `An external script error occurred: Error: cannot allocate vector of size.`

이러한 오류 중 하나를 수신하는 경우 데이터베이스를 더 높은 서비스 계층으로 확장하여 문제를 해결할 수 있습니다.

## <a name="sql-managed-instance-pools"></a>SQL Managed Instance 풀

Machine Learning Services는 현재 [Azure SQL Managed Instance 풀(미리 보기)](instance-pools-overview.md)에서 지원되지 않습니다.

## <a name="next-steps"></a>다음 단계

- 개요, [Azure SQL Managed Instance의 Machine Learning Services](machine-learning-services-overview.md)를 참조하세요.
- Machine Learning Services에서 Python을 사용하는 방법을 알아보려면 [Python 스크립트 실행](/sql/machine-learning/tutorials/quickstart-python-create-script?context=/azure/azure-sql/managed-instance/context/ml-context&view=azuresqldb-mi-current&preserve-view=true)을 참조하세요.
- Machine Learning Services에서 R을 사용하는 방법을 알아보려면 [R 스크립트 실행](/sql/machine-learning/tutorials/quickstart-r-create-script?context=/azure/azure-sql/managed-instance/context/ml-context&view=azuresqldb-mi-current&preserve-view=true)을 참조하세요.