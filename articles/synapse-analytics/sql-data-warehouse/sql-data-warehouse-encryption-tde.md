---
title: 전용 SQL 풀(이전 SQL DW)의 투명한 데이터 암호화(포털)
description: Azure Synapse Analytics에 있는 전용 SQL 풀(이전 SQL DW)의 TDE(투명한 데이터 암호화)
services: synapse-analytics
author: julieMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 06/23/2021
ms.author: jrasnick
ms.reviewer: rortloff
ms.custom: seo-lt-2019
ms.openlocfilehash: ddaa7656b5d926b3bf7fdb34548eafe99e9bcf89
ms.sourcegitcommit: 8b38eff08c8743a095635a1765c9c44358340aa8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/30/2021
ms.locfileid: "113092062"
---
# <a name="get-started-with-transparent-data-encryption-tde-for-dedicated-sql-pool-formerly-sql-dw-in-azure-synapse-analytics"></a>Azure Synapse Analytics에 있는 전용 SQL 풀(이전 SQL DW)의 TDE(투명한 데이터 암호화) 시작

> [!div class="op_single_selector"]
>
> * [보안 개요](sql-data-warehouse-overview-manage-security.md)
> * [인증](sql-data-warehouse-authentication.md)
> * [암호화(포털)](sql-data-warehouse-encryption-tde.md)
> * [암호화(T-SQL)](sql-data-warehouse-encryption-tde-tsql.md)

> [!NOTE]
> 이 문서는 Azure SQL Database, Azure SQL Managed Instance, Azure Synapse Analytics(전용 SQL 풀(이전의 SQL DW))에 적용됩니다. Synapse 작업 영역 내의 전용 SQL 풀을 위한 투명한 데이터 암호화에 관한 설명서는 [Azure Synapse Analytics 암호화](../security/workspaces-encryption.md)를 참조하세요.

## <a name="required-permissions"></a>필요한 권한

TDE(투명한 데이터 암호화)를 사용하려면 관리자 또는 dbmanager 역할의 멤버여야 합니다.

## <a name="enabling-encryption"></a>암호화 설정

TDE를 사용하려면 다음 단계를 수행합니다.

1. [Azure 포털](https://portal.azure.com)
2. 데이터베이스 블레이드에서 **설정** 단추 클릭
3. **투명한 데이터 암호화** 옵션 ![포털 설정](./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings.png) 선택
4. **켜기** 설정 ![포털 설정 켜기](./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-on.png) 선택
5. **저장**
   ![포털 설정 저장](./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-save.png) 선택  

## <a name="disabling-encryption"></a>암호화 비활성화

TDE를 사용하지 않으려면 다음 단계를 수행합니다.

1. [Azure 포털](https://portal.azure.com)
2. 데이터베이스 블레이드에서 **설정** 단추 클릭
3. **투명한 데이터 암호화** 옵션 ![포털 설정](./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings.png) 선택
4. **끄기** 설정 ![포털 설정 끄기](./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-off.png) 선택
5. **저장**
   ![포털 설정 저장 2](./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-save2.png) 선택  

## <a name="encryption-dmvs"></a>암호화 DMV

다음 DMV로 암호화를 확인할 수 있습니다.

* [sys.databases](/sql/relational-databases/system-catalog-views/sys-databases-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)
* [sys.dm_pdw_nodes_database_encryption_keys](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-nodes-database-encryption-keys-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)
