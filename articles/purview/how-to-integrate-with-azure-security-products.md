---
title: Azure 보안 제품과 통합
description: 이 문서에서는 Azure 보안 서비스 및 Azure 부서의 범위를 연결 하 여 보강 보안 환경을 얻는 방법을 설명 합니다.
author: aashishr
ms.author: aashishr
ms.service: purview
ms.topic: how-to
ms.date: 11/05/2021
ms.openlocfilehash: 7369f64f16724eb5c660b54a3d14a30046a41685
ms.sourcegitcommit: 591ffa464618b8bb3c6caec49a0aa9c91aa5e882
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/06/2021
ms.locfileid: "131894562"
---
# <a name="integrate-azure-purview-with-azure-security-products"></a>Azure 부서의 범위와 azure 보안 제품 통합

이 문서에서는 다양 한 Azure 보안 제품을 사용 하 여 Azure 부서의 범위 계정을 연결 하는 데 필요한 단계를 설명 하 고 데이터 분류 및 민감도 레이블을 사용 하 여 보안 환경을 보강 합니다.

## <a name="microsoft-defender-for-cloud"></a>Microsoft Defender for Cloud
Azure 부서의 범위는 데이터의 민감도에 대 한 풍부한 통찰력을 제공 합니다. 이렇게 하면 Microsoft Defender for Cloud를 사용 하 여 조직의 보안 상태를 관리 하 고 작업에 대 한 위협 으로부터 보호 하는 보안 팀에 게 유용 합니다. 데이터 리소스는 악의적인 행위자의 인기 있는 대상으로 유지 되므로 보안 팀이 클라우드 환경에서 중요 한 데이터 리소스를 식별 하 고 우선 순위를 지정 하 고 보안을 유지 하는 것이 중요 합니다. Azure 부서의 범위와의 통합은 데이터 계층에 대 한 가시성을 확장 하 여 보안 팀이 중요 한 데이터가 포함 된 리소스의 우선 순위를 지정 하도록 합니다.

[Microsoft Defender For Cloud에서이 보강](../security-center/information-protection.md)를 활용 하려면 Azure 부서의 범위에 추가 단계가 필요 하지 않습니다. 분류 및 민감도 레이블을 사용 하 여 데이터 원본 목록을 볼 수 있는 클라우드 [재고](https://portal.azure.com/#blade/Microsoft_Azure_Security/SecurityMenuBlade/25) 에 대해 Microsoft Defender를 사용 하 여 보안 강화를 탐색 하기 시작 합니다.

### <a name="supported-data-sources"></a>지원되는 데이터 원본
통합은 Azure 및 AWS의 데이터 원본을 지원 합니다. 이러한 리소스에서 검색 된 중요 한 데이터는 클라우드 용 Microsoft Defender와 공유 됩니다.
- [Azure Blob Storage](./register-scan-azure-blob-storage-source.md)
- [Azure Cosmos DB](./register-scan-azure-cosmos-database.md)
- [Azure Data Explorer](./register-scan-azure-data-explorer.md)
- [Azure Data Lake Storage Gen1](./register-scan-adls-gen1.md)
- [Azure Data Lake Storage Gen2](./register-scan-adls-gen2.md)
- [Azure 파일](./register-scan-azure-files-storage-source.md)
- [Azure Database for MySQL](./register-scan-azure-mysql-database.md)
- [Azure Database for PostgreSQL](./register-scan-azure-postgresql.md)
- [Azure SQL Database Managed Instance](./register-scan-azure-sql-database-managed-instance.md)
- [Azure 전용 SQL 풀(이전 SQL DW)](./register-scan-azure-synapse-analytics.md)
- [Azure SQL Database](./register-scan-azure-sql-database.md)
- [Azure Synapse Analytics(작업 영역)](./register-scan-synapse-workspace.md)
- [Amazon S3](./register-scan-amazon-s3.md)

### <a name="known-issues"></a>알려진 문제
1. 데이터 민감도 정보는 SAP, Erwin 및 Teradata와 같은 가상 머신 내에서 호스트 되는 원본에 대해 공유 되지 않습니다.
2. 데이터 민감도 정보는 현재 Amazon RDS에 대해 공유 되지 않습니다.
3. 데이터 민감도 정보는 현재 연결 문자열을 사용 하 여 등록 된 Azure PaaS 데이터 원본에 대해 공유 되지 않습니다. 
5. Azure 부서의 범위에서 데이터 원본의 등록을 취소 해도 클라우드의 Microsoft Defender에서 데이터 민감도 보강 제거 되지 않습니다.
6. Azure 부서의 범위 계정을 삭제 하면 Microsoft Defender for Cloud에서 30 일 동안 데이터 민감도 보강 유지 됩니다.
7. Microsoft 365 준수 센터 또는 Azure 부서의 범위에서 정의 된 사용자 지정 분류는 클라우드 용 Microsoft Defender와 공유 되지 않습니다.

## <a name="faq"></a>FAQ
### <a name="why-dont-i-see-the-aws-data-source-i-have-scanned-with-azure-purview-in-microsoft-defender-for-cloud"></a>**Microsoft Defender for Cloud에서 Azure 부서의 범위를 사용 하 여 스캔 한 AWS 데이터 원본이 표시 되지 않는 이유는 무엇 인가요?**

데이터 원본은 클라우드 용 Microsoft Defender에도 등록 해야 합니다. [AWS 계정을 연결](../security-center/quickstart-onboard-aws.md) 하 고 Microsoft Defender for CLOUD에서 AWS 데이터 원본을 확인 하는 방법에 대해 자세히 알아보세요.

### <a name="why-dont-i-see-sensitivity-labels-in-microsoft-defender-for-cloud"></a>**Microsoft Defender for Cloud에서 민감도 레이블을 표시 하지 않는 이유는 무엇 인가요?**

레이블을 Microsoft Defender for Cloud에 표시 하기 전에 먼저 Azure 부서의 범위에서 자산에 레이블을 지정 해야 합니다. [민감도 레이블의 필수 구성 요소가](./how-to-automatically-label-your-content.md) 있는지 확인 합니다. 데이터를 검색 하면 레이블이 Azure 부서의 범위에 표시 되 고 Microsoft Defender for Cloud에 자동으로 표시 됩니다.

## <a name="next-steps"></a>다음 단계
- [Azure 부서의 범위의 민감도를 사용 하는 Cloud 보강 용 Microsoft Defender의 경험](../security-center/information-protection.md)
