---
title: 관리 ID를 사용하여 데이터 원본에 대한 연결 설정
titleSuffix: Azure Cognitive Search
description: 관리 ID를 사용하여 데이터 원본에 대한 인덱서 연결을 설정하는 방법 알아보기
author: gmndrg
ms.author: gimondra
manager: nitinme
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 07/02/2021
ms.openlocfilehash: 6c038b7bac8d0791aafcda0010adeae138489ceb
ms.sourcegitcommit: 591ffa464618b8bb3c6caec49a0aa9c91aa5e882
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/06/2021
ms.locfileid: "131893788"
---
# <a name="set-up-an-indexer-connection-to-a-data-source-using-a-managed-identity"></a>관리 ID를 사용하여 데이터 원본에 대한 인덱서 연결 설정

> [!IMPORTANT] 
> 관리 ID를 사용하여 데이터 원본에 대한 연결을 설정하는 것은 무료 Azure Cognitive Search 계층에서 지원되지 않습니다.

Azure Cognitive Search의 [인덱서](search-indexer-overview.md)는 데이터 원본에서 Azure Cognitive Search로 데이터를 가져오는 방법을 제공하는 크롤러입니다. 인덱서는 사용자가 만든 데이터 원본 개체에서의 데이터 원본 연결을 획득합니다. 데이터 원본 개체는 일반적으로 대상 데이터 원본에 대한 자격 증명을 포함합니다. 예를 들어 Blob 스토리지 컨테이너에서 데이터를 인덱싱하는 경우 데이터 원본 개체에 Azure Storage 계정 키가 포함될 수 있습니다.

대부분의 경우 데이터 원본 개체에 직접 자격 증명을 제공하는 것은 문제가 되지 않지만 다음과 같은 몇 가지 문제가 발생할 수 있습니다.
* 데이터 원본 개체를 만드는 코드에서 자격 증명을 안전하게 유지하려면 어떻게 해야 하나요?
* 내 계정 키 또는 암호가 손상되어 변경해야 하는 경우, 이제 내 인덱서가 데이터 원본에 다시 연결할 수 있도록 새 계정 키 또는 암호를 사용하여 데이터 원본 개체를 업데이트해야 합니다.

이러한 문제는 관리 ID를 사용하여 연결을 설정하면 해결할 수 있습니다.

## <a name="using-managed-identities"></a>관리 ID 사용

[관리 ID](../active-directory/managed-identities-azure-resources/overview.md)는 Azure AD(Azure Active Directory)에서 자동으로 관리 ID를 애플리케이션에 제공하는 기능입니다. Azure Cognitive Search에서 이 기능을 사용하여 자격 증명을 포함하지 않는 연결 문자열을 사용하여 데이터 원본 개체를 만들 수 있습니다. 대신, Azure RBAC(Azure 역할 기반 액세스 제어)를 통해 검색 서비스에 데이터 원본에 대한 액세스 권한이 부여됩니다.

관리 ID를 사용하여 데이터 원본을 설정할 때 데이터 원본 자격 증명을 변경할 수 있으며, 인덱서는 여전히 데이터 원본에 연결할 수 있습니다. 계정 키를 포함하거나 Key Vault를 사용하여 계정 키를 검색하지 않고도 코드에서 데이터 원본 개체를 만들 수도 있습니다.

두 가지 종류의 관리 ID가 있습니다. Azure Cognitive Search 시스템 할당 관리 ID 및 사용자가 할당한 관리 ID를 지원합니다.

### <a name="system-assigned-managed-identity"></a>시스템 할당 관리 ID

[시스템 할당 관리 ID](../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types)는 단일 Azure 서비스에 할당할 수 있습니다. 시스템 할당 관리 ID를 단일 Azure Cognitive Search 서비스에 할당할 수 있으며 해당 검색 서비스의 수명 주기에 연결됩니다.

### <a name="user-assigned-managed-identity-preview"></a>사용자가 할당한 관리 ID(미리 보기)

> [!IMPORTANT]
>이 기능은 [추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)에 따라 공개 미리 보기로 제공됩니다. REST API 버전 2021-04-30-Preview 및 [Management REST API 2021-04-01-Preview](/rest/api/searchmanagement/2021-04-01-preview/services/create-or-update)에서 이 기능을 제공합니다.

[사용자가 할당한 관리 ID](../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types)는 하나 이상의 Azure 서비스에 할당할 수 있는 독립 실행형 Azure 리소스입니다. 단일 Azure Cognitive Search 서비스에는 하나 이상의 사용자가 할당한 관리 ID가 할당될 수 있습니다. 단일 사용자가 할당한 관리 ID를 여러 검색 서비스에 할당할 수 있습니다.

## <a name="limitations"></a>제한 사항

다음 데이터 원본은 관리 ID를 사용하는 인덱서 연결 설정을 지원합니다. 

* [Azure Blob Storage,  Azure Data Lake Storage Gen2(미리 보기), Azure Table Storage](search-howto-managed-identities-storage.md)
* [Azure Cosmos DB](search-howto-managed-identities-cosmos-db.md)
* [Azure SQL Database](search-howto-managed-identities-sql.md)

다음 기능은 현재 관리 ID를 사용하는 연결 설정을 지원하지 않습니다.
* 지식 저장소
* 사용자 지정 기술
 
## <a name="next-steps"></a>다음 단계

관리 ID를 사용하여 인덱서 연결을 설정하는 방법에 대해 자세히 알아봅니다.

* [Azure Blob Storage,  Azure Data Lake Storage Gen2(미리 보기), Azure Table Storage](search-howto-managed-identities-storage.md)
* [Azure Cosmos DB](search-howto-managed-identities-cosmos-db.md)
* [Azure SQL Database](search-howto-managed-identities-sql.md)