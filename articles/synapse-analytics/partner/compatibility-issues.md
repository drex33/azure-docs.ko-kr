---
title: 타사 애플리케이션 및 Azure Synapse Analytics와의 호환성 문제
description: 타사 애플리케이션에서 Azure Synapse 사용 시 발견되는 알려진 문제에 대한 설명
services: synapse-analytics
author: periclesrocha
ms.service: synapse-analytics
ms.topic: troubleshooting
ms.subservice: sql
ms.date: 11/18/2020
ms.author: procha
ms.reviewer: jrasnick
ms.openlocfilehash: b5b6805e9ce39af71fd5e4592b1e51f0096e2452
ms.sourcegitcommit: e8b229b3ef22068c5e7cd294785532e144b7a45a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/04/2021
ms.locfileid: "123482199"
---
# <a name="compatibility-issues-with-third-party-applications-and-azure-synapse-analytics"></a>타사 애플리케이션 및 Azure Synapse Analytics와의 호환성 문제

SQL Server용으로 빌드된 애플리케이션은 Azure Synapse 전용 SQL 풀에서 원활하게 작동합니다. 그러나 경우에 따라 SQL Server에서 일반적으로 사용되는 기능 및 언어 요소를 Azure Synapse에서 사용하지 못할 수도 있고 다르게 동작할 수도 있습니다.

이 문서에서는 Azure Synapse Analytics와 타사 애플리케이션을 함께 사용하는 경우 발생할 수 있는 알려진 문제에 대해 설명합니다. 

## <a name="tableau-error-an-attempt-to-complete-a-transaction-has-failed-no-corresponding-transaction-found"></a>Tableau error: “트랜잭션을 완료하지 못했습니다. 해당하는 트랜잭션 없음”

Azure Synapse 전용 SQL 풀 버전 10.0.11038.0부터 저장 프로시저를 호출하는 일부 Tableau 쿼리는 다음 오류 메시지가 나타나며 실패할 수 있습니다. " **[Microsoft][ODBC Driver 17 for SQL Server][SQL Server]111214; 트랜잭션을 완료하지 못했습니다. 해당하는 트랜잭션이 없습니다.** "

### <a name="cause"></a>원인

이는 ODBC 및 JDBC 드라이버에서 자동으로 호출되는 새 시스템 저장 프로시저의 도입으로 인해 발생하는 Azure Synapse 전용 SQL 풀의 문제입니다. 이러한 시스템 저장 프로시저 중 하나로 인해 트랜잭션이 실행되지 않을 경우 열려 있는 트랜잭션이 중단될 수 있습니다. 이 문제는 클라이언트 애플리케이션 로직에 따라 발생할 수 있습니다.

### <a name="solution"></a>해결 방법
Azure Synapse 전용 SQL 풀에 연결된 Tableau를 사용하는 경우 이 특정 문제가 확인되는 고객은 SQL 연결에서 FMTONLY를 ‘예’로 설정해야 합니다. Tableau Desktop 및 Tableau Server의 경우 TDC(Tableau 데이터 원본 사용자 지정) 파일을 사용하여 Tableau가 이 매개 변수를 드라이버로 전달하는지 확인해야 합니다.  

> [!NOTE] 
> Microsoft는 타사 도구에 대한 지원을 제공하지 않습니다. 테스트를 거쳐 이 솔루션이 Tableau Desktop 2020.3.2에서 작동함을 확인하였으나 사용자는 이 솔루션을 각자의 용량에 맞게 사용해야 합니다.
>

* [Tableau Desktop에서 TDC 파일을 사용하여 전역 사용자 지정을 만드는 방법에 대한 자세한 내용은 Tableau Desktop 설명서를 참조하세요.](https://help.tableau.com/current/pro/desktop/en-us/odbc_customize.htm)
* [Tableau Server에서 TDC 파일을 사용하여 전역 사용자 지정을 만드는 방법에 대한 자세한 내용은 Tableau Server에서 .TDC 파일 사용하기를 참조하세요.](https://kb.tableau.com/articles/howto/using-a-tdc-file-with-tableau-server)

아래 예제에서는 FMTONLY=YES 매개 변수를 SQL 연결 문자열에 전달하는 Tableau TDC 파일을 보여줍니다.

```json
<connection-customization class='azure_sql_dw' enabled='true' version='18.1'>
    <vendor name='azure_sql_dw' />
    <driver name='azure_sql_dw' />
    <customizations>        
        <customization name='odbc-connect-string-extras' value='UseFMTONLY=yes' />
    </customizations>
</connection-customization>
```
TDC 파일 사용에 대한 자세한 내용은 Tableau 지원에 문의하세요. 

## <a name="see-also"></a>참조

* [Azure Synapse Analytics의 전용 SQL 풀에 대한 T-SQL 언어 요소입니다.](../sql-data-warehouse/sql-data-warehouse-reference-tsql-language-elements.md)
* [Azure Synapse Analytics의 전용 SQL 풀에 대해 지원되는 T-SQL 문입니다.](../sql-data-warehouse/sql-data-warehouse-reference-tsql-statements.md)