---
title: 검사를 위한 자격 증명 생성 및 관리
description: Azure Purview에서 자격 증명을 만들고 관리하는 단계에 대해 알아봅니다.
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-map
ms.topic: how-to
ms.date: 11/10/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: a880aeb5ab8d6ac70e9fb99b0b894ed112f2d73d
ms.sourcegitcommit: 1244a72dbec39ac8cf16bb1799d8c46bde749d47
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/18/2021
ms.locfileid: "132764028"
---
# <a name="credentials-for-source-authentication-in-azure-purview"></a>Azure Purview에서 원본 인증을 위한 자격 증명

이 문서에서는 Azure Purview에서 자격 증명을 생성하는 방법에 대해 설명합니다. 저장된 자격 증명을 사용하면 데이터 원본 검사에 저장된 인증 정보를 빠르게 재사용하고 적용할 수 있습니다.

## <a name="prerequisites"></a>필수 구성 요소

- Azure 키 자격 증명 모음. 만드는 방법을 배우려면 [빠른 시작: Azure Portal을 사용하여 키 자격 증명 모음 만들기](../key-vault/general/quick-create-portal.md)를 참조하세요.

## <a name="introduction"></a>소개

자격 증명은 Azure Purview가 등록된 데이터 원본에 인증하는 데 사용할 수 있는 인증 정보입니다. 다양한 유형의 인증 시나리오(예: 사용자 이름/암호가 필요한 기본 인증)에 대해 자격 증명 개체를 만들 수 있습니다. 자격 증명은 선택한 인증 방법 유형에 따라 인증에 필요한 특정 정보를 캡처합니다. 자격 증명 생성 프로세스 중에 자격 증명은 중요한 인증 정보를 검색하기 위해서 기존 Azure Key Vaults 비밀을 사용합니다.

Azure Purview에는 다음 옵션과 같은 데이터 원본을 스캔하기 위한 인증 방법으로 사용할 수 있는 몇 가지 옵션이 있습니다.

- [Azure Purview 시스템 할당 관리 ID](#use-purview-system-assigned-managed-identity-to-set-up-scans)
- [사용자 할당 관리 ID(미리](#create-a-user-assigned-managed-identity) 보기)
- 계정 [키(Key Vault](#create-azure-key-vaults-connections-in-your-azure-purview-account)사용)
- SQL [인증(Key Vault](#create-azure-key-vaults-connections-in-your-azure-purview-account)사용)
- 서비스 [주체(Key Vault](#create-azure-key-vaults-connections-in-your-azure-purview-account)사용)
- 소비자 [키(Key Vault](#create-azure-key-vaults-connections-in-your-azure-purview-account)사용)

자격 증명을 만들기 전에 데이터 원본 유형 및 네트워킹 요구 사항을 고려하여 시나리오에 필요한 인증 방법을 결정합니다. 다음 의사 결정 트리를 검토하여 가장 적합한 자격 증명을 찾습니다.

   :::image type="content" source="media/manage-credentials/manage-credentials-decision-tree-small.png" alt-text="자격 증명 의사 결정 트리 관리" lightbox="media/manage-credentials/manage-credentials-decision-tree.png":::

## <a name="use-purview-system-assigned-managed-identity-to-set-up-scans"></a>Purview 시스템 할당 관리 ID를 사용하여 검사 설정

Purview SAMI(시스템 할당 관리 ID)를 사용하여 검사를 설정하는 경우 자격 증명을 명시적으로 만들고 Key Vault를 Purview에 연결하여 저장할 필요가 없습니다. Purview SAMI를 추가하여 데이터 원본을 검사할 수 있는 액세스 권한을 가지는 방법에 대한 자세한 지침은 아래의 데이터 원본별 인증 섹션을 참조하세요.

- [Azure Blob Storage](register-scan-azure-blob-storage-source.md#authentication-for-a-scan)
- [Azure Data Lake Storage Gen1](register-scan-adls-gen1.md#authentication-for-a-scan)
- [Azure Data Lake Storage Gen2](register-scan-adls-gen2.md#authentication-for-a-scan)
- [Azure SQL Database](register-scan-azure-sql-database.md)
- [Azure SQL Database Managed Instance](register-scan-azure-sql-database-managed-instance.md#authentication-for-registration)
- [Azure Synapse Analytics](register-scan-azure-synapse-analytics.md#authentication-for-registration)

## <a name="create-azure-key-vaults-connections-in-your-azure-purview-account"></a>Azure Purview 계정에 Azure Key Vaults 연결하기

자격 증명을 만들려면 먼저 하나 이상의 기존 Azure Key Vault 인스턴스를 Azure Purview 계정에 연결하세요.

1. [Azure Portal](https://portal.azure.com)Azure Purview 계정을 선택하고 [Purview Studio](https://web.purview.azure.com/resource/)를 엽니다. 스튜디오의 **관리 센터로** 이동한 다음 **자격 증명으로** 이동합니다.

2. **자격 증명 페이지** 에서 **Key Vault 연결 관리하기** 를 선택합니다.

   :::image type="content" source="media/manage-credentials/manage-kv-connections.png" alt-text="Azure Key Vault 연결 관리":::

3. Key Vault 연결 관리하기 페이지에서 **+ 새로 만들기** 를 선택합니다.

4. 필요한 정보를 입력한 다음 **만들기** 를 선택합니다.

5. 다음 예시와 같이 Key Vault가 Azure Purview 계정에 성공적으로 연결되었는지 확인합니다.

   :::image type="content" source="media/manage-credentials/view-kv-connections.png" alt-text="확인을 위해 Azure Key Vault 연결을 봅니다.":::

## <a name="grant-azure-purview-access-to-your-azure-key-vault"></a>Azure Purview에 Azure Key Vault 대한 액세스 권한 부여

현재 Azure Key Vault는 아래의 두 가지 권한 모델을 지원합니다.

- 옵션 1 - 액세스 정책 
- 옵션 2 - 역할 기반 액세스 제어 

Purview 시스템 할당 관리 ID(SAMI)에 대한 액세스를 할당하기 전에 먼저 메뉴의 Key Vault 리소스 **액세스 정책에서** Azure Key Vault 권한 모델을 식별합니다. 관련된 권한 모델에 따라 아래 단계를 수행합니다.  

:::image type="content" source="media/manage-credentials/akv-permission-model.png" alt-text="Azure Key Vault 권한 모델"::: 

### <a name="option-1---assign-access-using-key-vault-access-policy"></a>옵션 1 - Key Vault 액세스 정책을 사용하여 액세스 할당  

Azure Key Vault 리소스의 권한 모델이 **자격 증명 모음 액세스 정책** 으로 설정된 경우에만 다음 단계를 수행합니다.

1. Azure Key Vault로 이동합니다.

2. **액세스 정책** 페이지를 선택합니다.

3. **액세스 정책 추가** 를 선택합니다.

   :::image type="content" source="media/manage-credentials/add-msi-to-akv-2.png" alt-text="AKV에 Purview 관리 ID 추가":::

4. **비밀 권한** 드롭다운에서 **가져오기** 및 **목록** 사용 권한을 선택합니다.

5. **보안 주체 선택에서** Purview 시스템 관리 ID를 선택합니다. Purview 인스턴스 이름 **또는** 관리 ID 애플리케이션 ID를 사용하여 Purview SAMI를 검색할 수 있습니다. 현재는 복합 ID(관리 ID 이름 + 애플리케이션 ID)를 지원하지 않습니다.

   :::image type="content" source="media/manage-credentials/add-access-policy.png" alt-text="액세스 정책 추가":::

6. **추가** 를 선택합니다.

7. **저장** 을 선택하여 액세스 정책을 저장합니다.

   :::image type="content" source="media/manage-credentials/save-access-policy.png" alt-text="액세스 정책 저장":::

### <a name="option-2---assign-access-using-key-vault-azure-role-based-access-control"></a>옵션 2 - Key Vault Azure 역할 기반 액세스 제어를 사용하여 액세스 할당 

Azure Key Vault 리소스의 권한 모델이 **Azure 역할 기반 액세스 제어** 로 설정된 경우에만 다음 단계를 수행합니다.

1. Azure Key Vault로 이동합니다.

2. 왼쪽 탐색 메뉴에서 **액세스 제어(IAM)** 를 선택합니다.

3. **+추가** 를 선택합니다.

4. **역할을** 비밀 **사용자 Key Vault** 설정하고 입력 **선택** 상자에 Azure Purview 계정 이름을 입력합니다. 그런 다음, 저장을 선택하여 Purview 계정에 이 역할을 할당합니다.

   :::image type="content" source="media/manage-credentials/akv-add-rbac.png" alt-text="Azure Key Vault RBAC":::


## <a name="create-a-new-credential"></a>새 자격 증명 만들기

다음 자격 증명 유형은 Purview에서 지원됩니다.

- 기본 인증: 키 자격 증명 모음에서 **암호** 를 비밀로 추가합니다.
- 서비스 주체: 키 자격 증명 모음에 **서비스 주체 키** 를 비밀로 추가합니다.
- SQL 인증: 키 자격 증명 모음에 **암호** 를 비밀로 추가합니다.
- 계정 키: 키 자격 증명 모음에 **계정 키** 를 비밀로 추가합니다.
- 역할 ARN: Amazon S3 데이터 원본의 경우 AWS에 **역할 ARN** 을 추가합니다.
- 소비자 키: Salesforce 데이터 원본의 경우 키 자격 증명 모음에 **암호** 및 **소비자 암호를** 추가할 수 있습니다.
- 사용자 할당 관리 ID(미리 보기): 사용자 할당 관리 ID 자격 증명을 추가할 수 있습니다. 자세한 내용은 [아래의 사용자 할당 관리 ID 만들기 섹션을](#create-a-user-assigned-managed-identity) 참조하세요.

자세한 내용은 [Key Vault에 비밀 추가](../key-vault/secrets/quick-create-portal.md#add-a-secret-to-key-vault) 및 [ Purview에 대한 새 AWS 역할 만들기](register-scan-amazon-s3.md#create-a-new-aws-role-for-purview)를 참조하세요.

키 자격 증명 모음에 비밀을 저장한 후에 다음을 수행합니다.

1. Azure Purview에서 자격 증명 페이지로 이동합니다.

2. **+ 새로 만들기** 를 선택하여 새 자격 증명을 만듭니다.

3. 필요한 정보를 입력합니다. **인증 방법** 및 비밀을 선택할 **Key Vault 연결** 을 선택합니다.

4. 모든 세부 정보를 입력한 후 **만들기** 를 선택합니다.

   :::image type="content" source="media/manage-credentials/new-credential.png" alt-text="새 자격 증명":::

5. 새 자격 증명이 목록 보기에 나타나고 사용할 준비가 되었는지 확인합니다.

   :::image type="content" source="media/manage-credentials/view-credentials.png" alt-text="자격 증명 보기":::

## <a name="manage-your-key-vault-connections"></a>키 자격 증명 모음 연결 관리

1. 이름으로 Key Vault 연결 검색/찾기

   :::image type="content" source="media/manage-credentials/search-kv.png" alt-text="키 자격 증명 모음 검색":::

2. 하나 이상의 Key Vault 연결 삭제

   :::image type="content" source="media/manage-credentials/delete-kv.png" alt-text="키 자격 증명 모음 삭제":::

## <a name="manage-your-credentials"></a>자격 증명 관리

1. 이름으로 자격 증명 검색/찾기.
  
2. 기존 자격 증명을 선택하고 업데이트합니다.

3. 하나 이상의 자격 증명을 삭제합니다.

## <a name="create-a-user-assigned-managed-identity"></a>사용자 할당 관리 ID 만들기

UAMI(사용자 할당 관리 ID)를 사용하면 Azure 리소스가 해당 자격 증명을 관리할 필요 없이 Azure AD(Azure Active Directory) 인증을 사용하여 다른 리소스로 직접 인증할 수 있습니다. 시스템 할당 관리 ID, Azure AD 사용자, Azure AD 그룹 또는 서비스 주체와 마찬가지로 인증하고 액세스를 할당할 수 있습니다. 사용자가 할당한 관리 ID는 기존 리소스에 연결되지 않고 자체 리소스로 만들어집니다. 관리 ID에 대한 자세한 내용은 [Azure 리소스에 대한 관리 ID 설명서를 참조하세요.](../active-directory/managed-identities-azure-resources/overview.md)

다음 단계에서는 Purview용 UAMI를 만드는 방법을 보여줍니다.

### <a name="supported-data-sources-for-uami"></a>UAMI에 대해 지원되는 데이터 원본

* [Azure Data Lake Gen 1](register-scan-adls-gen1.md) 
* [Azure Data Lake Gen 2](register-scan-adls-gen2.md) 
* [Azure SQL Database](register-scan-azure-sql-database.md) 
* [Azure SQL Database Managed Instance](register-scan-azure-sql-database-managed-instance.md)    
* [Azure SQL 전용 SQL 풀](register-scan-azure-synapse-analytics.md) 
* [Azure Blob Storage](register-scan-azure-blob-storage-source.md) 

### <a name="create-a-user-assigned-managed-identity"></a>사용자 할당 관리 ID 만들기

1. Azure [Portal에서](https://portal.azure.com/) Azure Purview 계정으로 이동합니다.

1. 왼쪽 메뉴의 **관리 ID** 섹션에서 + **추가** 단추를 선택하여 사용자 할당 관리 ID를 추가합니다.  
    
    :::image type="content" source="media/manage-credentials/create-new-managed-identity.png" alt-text="사용자 할당 및 추가가 강조 표시된 Azure Portal 관리 ID 화면을 보여주는 스크린샷.":::
   
1. 설정을 완료한 후 Azure Portal에서 Azure Purview 계정으로 돌아갑니다. 관리 ID가 성공적으로 배포되면 Azure Purview 계정의 상태가 **성공으로** 표시됩니다.

   :::image type="content" source="media/manage-credentials/status-successful.png" alt-text="개요 탭 및 필수 메뉴 아래에 상태가 강조 표시된 Azure Portal의 Azure Purview 계정을 스크린샷합니다.":::


1. 관리 ID가 성공적으로 배포되면 [Purview Studio](https://web.purview.azure.com/)열기 단추를 선택하여 **Purview Studio** 로 이동합니다.

1. [Purview Studio에서 스튜디오의](https://web.purview.azure.com/)관리 센터로 이동한 다음 자격 증명 섹션으로 이동합니다.

1. **+새로** 만들기를 선택하여 사용자 할당 관리 ID를 만듭니다. 
1. 관리 ID 인증 방법을 선택하고 드롭다운 메뉴에서 사용자 할당 관리 ID를 선택합니다.

   :::image type="content" source="media/manage-credentials/new-user-assigned-managed-identity-credential.png" alt-text="자세한 정보 링크가 강조 표시된 새 관리 ID 만들기 타일을 보여주는 스크린샷.":::  

    >[!NOTE]
    > 사용자 할당 관리 ID를 만드는 동안 포털이 열려 있는 경우 Purview 웹 포털을 새로 고쳐 Azure Portal 완료된 설정을 로드해야 합니다.

1. 모든 정보를 채운 후 **만들기를** 선택합니다.


## <a name="next-steps"></a>다음 단계

[검사 규칙 집합 만들기](create-a-scan-rule-set.md)
