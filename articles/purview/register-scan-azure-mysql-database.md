---
title: Azure MySQL Database 등록 및 검사
description: 이 자습서에서는 Azure Purview에서 Azure MySQL 데이터베이스를 등록하고 검사하는 방법을 설명합니다.
author: evwhite
ms.author: evwhite
ms.service: purview
ms.subservice: purview-data-map
ms.topic: tutorial
ms.date: 09/27/2021
ms.openlocfilehash: 8f4a5480b76e03a57ff810c88a0a1660ae561071
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/29/2021
ms.locfileid: "129209866"
---
# <a name="register-and-scan-an-azure-mysql-database"></a>Azure MySQL Database 등록 및 검사

이 문서에서는 Azure MySQL Database를 등록하고 검색하는 방법을 설명합니다.

## <a name="supported-capabilities"></a>지원되는 기능
- **전체 및 증분 검사** - Azure MySQL Database에서 메타데이터 및 분류를 캡처합니다.

- **계보** - ADF 복사 및 데이터 흐름 작업에 대한 데이터 자산 간의 계보입니다.

### <a name="known-limitations"></a>알려진 제한 사항
Purview는 Azure MySQL Database에 대한 SQL 인증만 지원합니다.


## <a name="prerequisites"></a>필수 조건

1. 아직 Purview 계정이 없는 경우 새로 만듭니다.

2. Purview 계정과 Azure MySQL Database 간의 네트워킹 액세스입니다.

### <a name="set-up-authentication-for-a-scan"></a>검사 인증 설정

다음 단계를 수행하려면 **사용자 이름** 및 **암호** 가 필요합니다.

[데이터베이스 및 사용자 만들기](../mysql/howto-create-users.md)의 지침에 따라 Azure MySQL Database에 대한 로그인을 만듭니다.

1. Azure Portal에서 키 자격 증명 모음으로 이동합니다.
1. **설정 > 비밀** 을 선택합니다.
1. **+ 생성/가져오기** 를 선택하고, Azure SQL Database의 *암호* 로 **이름** 및 **값** 을 입력합니다.
1. **만들기** 를 선택하여 완료합니다.
1. 키 자격 증명 모음이 아직 Purview에 연결되지 않은 경우 [새 키 자격 증명 모음 연결을 생성](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account)해야 합니다.
1. 마지막으로 **사용자 이름** 및 **암호** 를 사용하여 검사를 설정하기 위한 SQL 인증 유형의 [새 자격 증명을 만듭니다](manage-credentials.md#create-a-new-credential).

## <a name="register-an-azure-mysql-database-data-source"></a>Azure MySQL Database 데이터 원본 등록

새 Azure MySQL Database를 데이터 카탈로그에 등록하려면 다음을 수행합니다.

1. Purview 계정으로 이동합니다.

1. 왼쪽 탐색 메뉴에서 **데이터 맵** 을 선택합니다.

1. **등록** 을 선택합니다.

1. **원본 등록** 에서 **Azure MySQL Database** 를 선택합니다. **계속** 을 선택합니다.

:::image type="content" source="media/register-scan-azure-mysql/01-register-azure-mysql-data-source.png" alt-text="새 데이터 원본 등록" border="true":::

**원본 등록(Azure MySQL Database)** 화면에서 다음을 수행합니다.

1. 데이터 원본의 **이름** 을 입력합니다. 이 이름은 카탈로그에서 이 데이터 원본의 표시 이름이 됩니다.
1. **Azure 구독에서** 를 선택하고, **Azure 구독** 드롭다운 상자에서 적절한 구독을 선택하고, **서버 이름** 드롭다운 상자에서 적절한 서버를 선택합니다.
1. **등록** 을 선택하여 데이터 원본을 등록합니다. 

:::image type="content" source="media/register-scan-azure-mysql/02-register-azure-mysql-name-connection.png" alt-text="원본 등록 옵션" border="true":::

## <a name="creating-and-running-a-scan"></a>검사 만들기 및 실행

새 검색을 만들고 실행하려면 다음을 수행합니다.

1. [Purview Studio](https://web.purview.azure.com/resource/)의 왼쪽 창에서 **데이터 맵** 탭을 선택합니다.

1. 등록한 Azure Database for MySQL 원본을 선택합니다.

1. **새 검사** 를 선택합니다.

1. 자격 증명을 선택하여 데이터 원본에 연결하고 자격 증명이 올바르게 구성되었는지 연결을 확인합니다.

   :::image type="content" source="media/register-scan-azure-mysql/03-new-scan-azure-mysql-connection-credential.png" alt-text="검사 설정":::

1. 목록에서 적절한 항목을 선택하여 특정 폴더 또는 하위 폴더로 검색 범위를 지정할 수 있습니다.

   :::image type="content" source="media/register-scan-azure-mysql/04-scope-azure-mysql-scan.png" alt-text="검사 범위 지정":::

1. 그런 다음, 검사 규칙 집합을 선택합니다. 시스템 기본값, 기존 사용자 지정 규칙 집합 중 하나를 선택하거나 새 규칙 집합을 인라인으로 만들 수 있습니다.

   :::image type="content" source="media/register-scan-azure-mysql/05-scan-rule-set.png" alt-text="검사 규칙 집합":::

1. 검사 트리거를 선택합니다. 일정을 설정하거나 검사를 한 번 실행할 수 있습니다.

   :::image type="content" source="media/register-scan-azure-mysql/06-trigger-scan.png" alt-text="트리거":::

1. 검사를 검토하고 **저장 및 실행** 을 선택합니다.

:::image type="content" source="media/register-scan-azure-mysql/07-review-your-scan.png" alt-text="스캔 검토" border="true":::

[!INCLUDE [view and manage scans](includes/view-and-manage-scans.md)]

> [!NOTE]
> * 검사를 삭제해도 이전 Azure MySQL Database 검사에서 만든 자산이 삭제되지 않습니다.
> * 원본 테이블을 변경하고 Purview의 스키마 탭에서 설명을 편집한 후에 원본 테이블을 다시 검사하면 해당 자산이 스키마 변경 내용으로 더 이상 업데이트되지 않습니다.

## <a name="next-steps"></a>다음 단계

- [Azure Purview 데이터 카탈로그 찾아보기](how-to-browse-catalog.md)
- [Azure Purview Data Catalog 검색](how-to-search-catalog.md)
