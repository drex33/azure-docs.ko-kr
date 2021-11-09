---
title: 원장을 사용하는 단일 데이터베이스 만들기
description: Azure Portal을 사용하여 원장이 활성화된 Azure SQL Database에서 단일 데이터베이스를 만듭니다.
ms.service: sql-database
ms.subservice: security
ms.devlang: ''
ms.topic: quickstart
author: rothja
ms.author: jroth
ms.reviewer: vanto
ms.date: 09/09/2021
ms.openlocfilehash: 8538e725583aea6fe765a7afd6e0ac58f176b443
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/09/2021
ms.locfileid: "132063744"
---
# <a name="quickstart-create-a-database-in-azure-sql-database-with-ledger-enabled"></a>빠른 시작: 원장이 활성화된 Azure SQL Database에서 데이터베이스 만들기

[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

> [!NOTE]
> Azure SQL Database 원장은 현재 공개 미리 보기로 제공됩니다.

이 빠른 시작에서는 Azure SQL Database에서 [원장 데이터베이스](ledger-overview.md#ledger-database)를 만들고 Azure Portal을 사용하여 [Azure Blob Storage로 자동 다이제스트 스토리지](ledger-digest-management-and-database-verification.md#automatic-generation-and-storage-of-database-digests)를 구성합니다. 원장에 대한 자세한 내용은 [Azure SQL Database 원장](ledger-overview.md)을 참조하세요.

## <a name="prerequisite"></a>필수 요소

활성 상태인 Azure 구독이 필요합니다. 아직 없는 경우 [체험 계정](https://azure.microsoft.com/free/)을 만들 수 있습니다.

## <a name="create-a-ledger-database-and-configure-digest-storage"></a>원장 데이터베이스 만들기 및 다이제스트 스토리지 구성

[서버리스 컴퓨팅 계층](serverless-tier-overview.md)에 단일 원장 데이터베이스를 만들고 Azure Storage 계정에 원장 다이제스트 업로드를 구성합니다.

### <a name="use-the-azure-portal"></a>Azure Portal 사용

Azure Portal에서 단일 데이터베이스를 만들기 위해 이 빠른 시작이 Azure SQL 페이지에서 시작됩니다.

1. [SQL 배포 옵션 선택](https://portal.azure.com/#create/Microsoft.AzureSQL) 페이지로 이동합니다.

1. **SQL 데이터베이스** 에서 **리소스 유형** 을 **단일 데이터베이스** 로 설정한 상태로 두고 **만들기** 를 선택합니다.

   ![Azure SQL에 추가하는 것을 보여주는 스크린샷.](./media/single-database-create-quickstart/select-deployment.png)

1. **SQL Database 만들기** 양식의 **기본** 탭에 있는 **프로젝트 세부 정보** 아래에서 사용하려는 Azure 구독을 선택합니다.

1. **리소스 그룹** 의 경우 **새로 만들기** 를 선택하고, **myResourceGroup** 을 입력하고, **확인** 을 선택합니다.

1. **데이터베이스 이름** 으로 **demo** 를 입력합니다.

1. **서버** 의 경우 **새로 만들기** 를 선택합니다. 다음 값으로 **새 서버 양식** 을 작성합니다.
   - **서버 이름**: **mysqlserver** 를 입력하고 고유하게 유지하기 위한 일부 문자를 추가합니다. 서버 이름은 구독 내에서 고유한 것이 아니라 Azure의 모든 서버에 대해 전역적으로 고유해야 하므로 사용할 정확한 서버 이름을 제공할 수 없습니다. **mysqlserver12345** 와 같은 항목을 입력하면 포털에서 사용 가능 여부를 알 수 있습니다.
   - **서버 관리자 로그인**: **azureuser** 를 입력합니다.
   - **암호**: 요구 사항을 충족하는 암호를 입력합니다. **암호 확인** 상자에 다시 입력합니다.
   - **위치**: 드롭다운 목록에서 위치를 선택합니다.
   - **Azure 서비스가 이 서버에 액세스하도록 허용**: 다이제스트 스토리지에 대한 액세스를 활성화하려면 이 옵션을 선택합니다.
   
   **확인** 을 선택합니다.
   
1. **SQL 탄력적 풀을 사용하나요?** 를 **아니요** 로 설정된 상태로 둡니다.

1. **컴퓨팅 + 스토리지** 에서 **데이터베이스 구성** 을 선택합니다.

1. 이 빠른 시작에서는 서버리스 데이터베이스를 사용하므로 **서버리스** 를 선택한 다음, **적용** 을 선택합니다. 

      ![서버리스 데이터베이스 구성을 보여주는 스크린샷.](./media/single-database-create-quickstart/configure-database.png)

1. **네트워킹** 탭에서 **연결 방법** 에 대해 **퍼블릭 엔드포인트** 를 선택합니다.
1. **방화벽 규칙** 의 경우 **현재 클라이언트 IP 주소 추가** 를 **예** 로 설정합니다. **Azure 서비스 및 리소스가 이 서버에 액세스할 수 있도록 허용** 을 **아니요** 로 설정된 상태로 둡니다.
1. 페이지 하단에서 **다음: 보안** 을 선택합니다.

   :::image type="content" source="media/ledger/ledger-create-database-networking-tab.png" alt-text="Azure Portal에서 SQL Database 만들기 화면의 네트워킹 탭을 보여주는 스크린샷.":::

1. **보안** 탭의 **원장** 섹션에서 **원장 구성** 옵션을 선택합니다.

    :::image type="content" source="media/ledger/ledger-configure-ledger-security-tab.png" alt-text="Azure Portal의 보안 탭에서 원장 구성을 보여주는 스크린샷.":::

1. **원장 구성** 창의 **원장** 섹션에서 **이 데이터베이스의 모든 향후 테이블에 사용** 확인란을 선택합니다. 이 설정을 통해 데이터베이스의 향후 모든 테이블이 원장 테이블이 됩니다. 이러한 이유로 데이터베이스의 모든 데이터는 변조 증거를 표시합니다. 기본적으로 새 테이블은 [CREATE TABLE](/sql/t-sql/statements/create-table-transact-sql)에 `LEDGER = ON`을 지정하지 않더라도 업데이트 가능한 원장 테이블로 만들어집니다. 이 옵션을 선택하지 않은 상태로 둘 수도 있습니다. 그런 다음, Transact-SQL을 사용하여 새 테이블을 만들 때 테이블별로 원장 기능을 사용하도록 설정해야 합니다.

1. **Digest Storage** 섹션에서 **자동 다이제스트 스토리지 사용** 이 자동으로 선택됩니다. 그런 다음, 다이제스트가 저장된 새 Azure Storage 계정과 컨테이너가 생성됩니다.

1. **적용** 을 선택합니다.

    :::image type="content" source="media/ledger/ledger-configure-ledger-pane.png" alt-text="Azure Portal에서 원장 구성(미리 보기) 창을 보여주는 스크린샷.":::

1. 페이지 아래쪽에서 **검토 + 만들기** 를 선택합니다.

    :::image type="content" source="media/ledger/ledger-review-security-tab.png" alt-text="Azure Portal의 보안 탭에서 원장 데이터베이스를 검토하고 만드는 방법을 보여주는 스크린샷.":::

1. **검토 + 만들기** 페이지에서 검토 후 **만들기** 를 선택합니다.

## <a name="clean-up-resources"></a>리소스 정리

다음 단계를 위해 리소스 그룹, 서버 및 단일 데이터베이스를 유지합니다. 다양한 방법으로 데이터베이스의 원장 기능을 사용하는 방법에 대해 알아봅니다.

이러한 리소스 사용을 완료한 경우 리소스 그룹을 삭제합니다. 이 작업을 수행하면 서버 및 서버 내의 단일 데이터베이스도 삭제됩니다.

### <a name="use-the-azure-portal"></a>Azure Portal 사용

Azure Portal을 사용하여 **myResourceGroup** 및 모든 해당 리소스를 삭제하려면 다음을 수행합니다.

1. 포털에서 **리소스 그룹** 을 검색하여 선택합니다. 그런 다음, 목록에서 **myResourceGroup** 을 선택합니다.
1. 리소스 그룹 페이지에서 **리소스 그룹 삭제** 를 선택합니다.
1. **리소스 그룹 이름 입력** 에 **myResourceGroup** 을 입력한 다음, **삭제** 를 선택합니다.

## <a name="next-steps"></a>다음 단계

다양한 도구 및 언어를 사용하여 데이터베이스를 연결하고 쿼리합니다.

- [업데이트할 수 있는 원장 테이블 만들기 및 사용](ledger-how-to-updatable-ledger-tables.md)
- [추가 전용 원장 테이블 만들기 및 사용](ledger-how-to-append-only-ledger-tables.md) 
