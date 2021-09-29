---
title: Azure Purview 연결 관련 문제 해결
description: 본 문서는 Azure Purview 연결 관련 문제 해결 단계를 설명합니다.
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-map
ms.topic: how-to
ms.date: 09/27/2021
ms.openlocfilehash: cfcac68bfc09a4c3b6d4794469734d0634b3ad0c
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/29/2021
ms.locfileid: "129213461"
---
# <a name="troubleshoot-your-connections-in-azure-purview"></a>Azure Purview 연결 관련 문제 해결

본 문서는 Azure Purview의 데이터 원본에 대한 검사를 설정하는 동안의 연결 오류 문제를 해결하는 방법을 설명합니다.

## <a name="permission-the-credential-on-the-data-source"></a>데이터 원본에 대한 자격 증명 권한

관리형 ID나 서비스 사용자를 검사에 대한 인증 메서드로 사용하고 있다면 해당 ID가 데이터 원본에 액세스할 수 있도록 하여야 합니다.

원본 유형별 구체적 지침은 다음과 같습니다.

- [Azure 여러 원본](register-scan-azure-multiple-sources.md#set-up-authentication-to-scan-resources-under-a-subscription-or-resource-group)
- [Azure Blob Storage](register-scan-azure-blob-storage-source.md#setting-up-authentication-for-a-scan)
- [Azure Cosmos DB](register-scan-azure-cosmos-database.md#setting-up-authentication-for-a-scan)
- [Azure Data Explorer](register-scan-azure-data-explorer.md#setting-up-authentication-for-a-scan)
- [Azure Data Lake Storage Gen1](register-scan-adls-gen1.md#setting-up-authentication-for-a-scan)
- [Azure Data Lake Storage Gen2](register-scan-adls-gen2.md#setting-up-authentication-for-a-scan)
- [Azure SQL Database](register-scan-azure-sql-database.md)
- [Azure SQL Database Managed Instance](register-scan-azure-sql-database-managed-instance.md#setting-up-authentication-for-a-scan)
- [Azure Synapse Analytics](register-scan-azure-synapse-analytics.md#setting-up-authentication-for-a-scan)
- [SQL Server](register-scan-on-premises-sql-server.md#setting-up-authentication-for-a-scan)
- [Power BI](register-scan-power-bi-tenant.md)
- [Amazon S3](register-scan-amazon-s3.md#create-a-purview-credential-for-your-aws-s3-scan)

## <a name="verifying-azure-role-based-access-control-to-enumerate-azure-resources-in-azure-purview-studio"></a>Azure Purview Studio에서 Azure 리소스를 열거하기 위한 Azure 역할 기반 액세스 제어 확인

### <a name="registering-single-azure-data-source"></a>단일 Azure 데이터 원본 등록
Azure Blog Storage 또는 Azure SQL 데이터베이스와 같은 단일 데이터 원본을 Azure Purview에 등록하려면 리소스에 대해 최소한 **읽기 권한자** 역할이 부여되거나 리소스 그룹 또는 구독과 같은 상위 범위에서 상속되어야 합니다. 보안 관리자와 같은 일부 Azure RBAC 역할에는 컨트롤 플레인에서 Azure 리소스를 볼 수 있는 읽기 액세스 권한이 없습니다.  

다음 단계를 수행하여 이를 확인합니다.

1. [Azure Portal](https://portal.azure.com)에서 Azure Purview에 등록하려는 리소스로 이동합니다. 리소스를 볼 수 있다면 리소스에 대해 최소한 읽기 권한자 역할이 이미 있을 가능성이 높습니다. 
2. **액세스 제어(IAM)**  > **역할 할당** 을 선택합니다.
3. Azure Purview에서 데이터 원본을 등록하려는 사용자의 이름 또는 이메일 주소로 검색합니다.
4. 읽기 권한자와 같은 역할 할당이 목록에 있는지 확인하거나 필요한 경우 새 역할 할당을 추가합니다.

### <a name="scanning-multiple-azure-data-sources"></a>여러 Azure 데이터 원본 검사
1. [Azure Portal](https://portal.azure.com)에서 구독 또는 리소스 그룹으로 이동합니다.  
2. 왼쪽 메뉴에서  **액세스 제어(IAM)**  를 선택합니다. 
3. **+추가** 를 선택합니다. 
4. **입력 선택** 상자에서 **읽기 권한자** 역할을 선택하고 Azure Purview 계정 이름(MSI 이름을 나타냄)을 입력합니다. 
5. **저장** 을 선택하여 역할 할당을 완료합니다.
6. 위의 단계를 반복하여 Azure Purview에서 여러 데이터 원본에 대한 새 검사를 만들려는 사용자의 ID를 추가합니다.

## <a name="scanning-data-sources-using-private-link"></a>Private Link를 사용하여 데이터 원본 검사 
데이터 원본에서 공용 엔드포인트가 제한된 경우 Private Link를 사용하여 Azure 데이터 원본을 검사하려면 자체 호스팅 통합 런타임을 설정하고 자격 증명을 만들어야 합니다. 

> [!IMPORTANT]
> _공용 네트워크 액세스 거부_ 를 사용하여 Azure SQL 데이터베이스로 데이터베이스를 포함하는 여러 데이터 원본을 검사하면 실패합니다. 프라이빗 엔드포인트를 사용하여 이러한 데이터 원본을 검사하려면 대신 단일 데이터 원본 등록 옵션을 사용합니다.

자체 호스팅 통합 런타임 설정에 관한 자세한 내용은 [수집 프라이빗 엔드포인트 및 검사 원본](catalog-private-link-ingestion.md#deploy-self-hosted-integration-runtime-ir-and-scan-your-data-sources)을 참조하세요.

Azure Purview에서 새 자격 증명을 만드는 방법에 대한 자세한 내용은 [Azure Purview에서 원본 인증을 위한 자격 증명](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account)을 참조하세요.

## <a name="storing-your-credential-in-your-key-vault-and-using-the-right-secret-name-and-version"></a>키 자격 증명 모음에 자격 증명을 저장하고 올바른 비밀 이름 및 버전 사용하기

또한 Azure Key Vault 인스턴스에 자격 증명을 저장하고 올바른 비밀 이름 및 버전을 사용하여야 합니다.

다음 단계를 수행하여 이를 확인합니다.

1. 키 자격 증명 모음으로 이동합니다.
1. **설정** > **비밀** 을 선택합니다.
1. 검사할 데이터 원본에 대하여 인증하기 위하여 사용하는 비밀을 선택합니다.
1. 사용할 버전을 선택하고 비밀 값 표시를 선택하여 암호 또는 계정 키가 올바른지 **확인합니다.** 

## <a name="verify-permissions-for-the-purview-managed-identity-on-your-azure-key-vault"></a>Azure Key Vault에서 Purview 관리형 ID에 대한 권한 확인하기

Purview 관리형 ID에 Azure Key Vault에 액세스하기 위한 올바른 권한이 구성되었는지 확인합니다.

이를 확인하려면 다음 단계를 수행합니다.

1. 키 자격 증명 모음 및 **액세스 정책** 섹션으로 이동합니다.

1. Purview 관리형 ID가 *현재 액세스 정책* 섹션에서 적어도 비밀 관련 **Get** 및 **List** 권한이 있는 것으로 표시되는지 확인합니다.

   :::image type="content" source="./media/troubleshoot-connections/verify-minimum-permissions.png" alt-text="Get과 List 권한 옵션 두 가지를 모두 선택한 드롭다운을 보여 주는 이미지":::

목록에 Purview 관리형 ID가 없으면 [검사용 자격 증명 만들고 관리하기](manage-credentials.md)의 단계에 따라 해당 ID를 추가합니다. 

## <a name="next-steps"></a>다음 단계

- [Azure Purview 데이터 카탈로그 찾아보기](how-to-browse-catalog.md)
- [Azure Purview Data Catalog 검색](how-to-search-catalog.md)
