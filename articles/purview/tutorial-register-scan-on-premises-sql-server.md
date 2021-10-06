---
title: '자습서: 온-프레미스 SQL Server 등록 및 검사'
description: 이 자습서에서는 온-프레미스 SQL Server를 Purview에 등록하고, 자체 호스팅 IR을 사용하여 서버를 검사하는 방법을 설명합니다.
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: tutorial
ms.date: 09/27/2021
ms.custom: template-tutorial
ms.openlocfilehash: 0054b41fdf12efa4bbd3f1bf34e66023b7ea2d75
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/29/2021
ms.locfileid: "129213151"
---
# <a name="tutorial-register-and-scan-an-on-premises-sql-server"></a>자습서: 온-프레미스 SQL Server 등록 및 검사

Azure Purview는 중요한 데이터를 관리하고, 데이터 검색을 간소화하고, 데이터가 올바르게 사용되도록 도와주기 위해 데이터 원본에 연결하도록 설계되었습니다. Purview는 다중 클라우드 및 온-프레미스를 포함하여 전체 환경에서 원본에 연결할 수 있습니다. 이 시나리오에서는 자체 호스팅 통합 런타임을 사용하여 온-프레미스 SQL 서버의 데이터에 연결합니다. 그런 다음, Azure Purview를 사용하여 해당 데이터를 검사하고 분류합니다.

이 자습서에서 학습할 방법은 다음과 같습니다.

> [!div class="checklist"]
> * Purview Studio에 로그인
> * Azure Purview에서 컬렉션 만들기
> * 자체 호스팅 통합 런타임을 만듭니다.
> * Azure Key Vault에 자격 증명 저장
> * 온-프레미스 SQL Server를 Purview에 등록
> * SQL Server 검사
> * 데이터 카탈로그를 탐색하여 SQL Server 자산 살펴보기

## <a name="prerequisites"></a>필수 구성 요소

- 활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- 활성 [Azure Key Vault](../key-vault/certificates/quick-create-portal.md#create-a-vault)
- Azure Purview 계정. 아직 없는 경우 [빠른 시작 가이드에 따라 새로 생성](create-catalog-portal.md)할 수 있습니다.
- [온-프레미스 SQL Server](https://www.microsoft.com/sql-server/sql-server-downloads)

## <a name="sign-in-to-purview-studio"></a>Purview Studio에 로그인

Purview와 상호 작용할 수 있도록 Azure Portal을 통해 [Purview Studio](https://web.purview.azure.com/resource/)에 연결합니다. 이 스튜디오는 [Azure Portal](https://portal.azure.com)에서 Purview 리소스로 이동한 다음, [개요] 페이지에서 **Purview Studio 열기** 타일을 선택하여 찾을 수 있습니다.

:::image type="content" source="./media/tutorial-register-scan-on-premises-sql-server/open-purview-studio.png" alt-text="Purview Studio 단추가 강조 표시된 Azure Portal Purview 창의 스크린샷" border="true":::

## <a name="create-a-collection"></a>컬렉션 만들기

Azure Purview의 컬렉션은 자산과 원본을 조직 및 검색 가능성을 높이기 위한 사용자 지정 계층 구조로 구성하는 데 사용됩니다. Purview에서 액세스 권한을 관리하는 데 사용되는 도구이기도 합니다. 이 자습서에서는 SQL Server 원본과 모든 자산을 보관하는 컬렉션을 하나 만듭니다. 이 자습서에서는 다른 사용자에게 권한을 할당하는 내용을 다루지 않으므로, 자세한 내용을 보려면 [Purview 가이드](catalog-permissions.md)를 참조하세요.

### <a name="check-permissions"></a>사용 권한 확인

Purview에서 컬렉션을 만들고 관리하려면 Purview 내에서 **컬렉션 관리자** 여야 합니다. 이러한 권한은 [Purview Studio](use-purview-studio.md)에서 확인할 수 있습니다.

1. 왼쪽 창에서 **데이터 맵 > 컬렉션** 을 선택하여 컬렉션 관리 페이지를 엽니다.

    :::image type="content" source="./media/tutorial-register-scan-on-premises-sql-server/find-collections.png" alt-text="데이터 맵이 열리고 컬렉션 탭이 선택된 Purview Studio 창의 스크린샷" border="true":::

1. 루트 컬렉션을 선택합니다. 루트 컬렉션은 컬렉션 목록에서 최상위 컬렉션이며 Purview 리소스와 동일한 이름을 사용합니다. 아래 예제에서는 이름이 Purview Account입니다.

    :::image type="content" source="./media/tutorial-register-scan-on-premises-sql-server/select-root-collection.png" alt-text="데이터 맵이 열리고 루트 컬렉션이 강조 표시된 Purview Studio 창의 스크린샷" border="true":::

1. 컬렉션 창에서 **역할 할당** 을 선택합니다.

    :::image type="content" source="./media/tutorial-register-scan-on-premises-sql-server/role-assignments.png" alt-text="데이터 맵이 열리고 역할 할당 탭이 강조 표시된 Purview Studio 창의 스크린샷" border="true":::

1. 컬렉션을 만들려면 역할 할당 아래의 컬렉션 관리자 목록에 있어야 합니다. Purview 리소스를 만든 사람은 이미 루트 컬렉션 아래에 컬렉션 관리자로 나열되어 있을 것입니다. 나열되어 있지 않으면 컬렉션 관리자에게 권한을 부여해 달라고 문의해야 합니다.

    :::image type="content" source="./media/tutorial-register-scan-on-premises-sql-server/collection-admins.png" alt-text="데이터 맵이 열리고 컬렉션 관리자 섹션이 강조 표시된 Purview Studio 창의 스크린샷" border="true":::

### <a name="create-the-collection"></a>컬렉션 만들기

1. **+ 컬렉션 추가** 를 선택합니다. 다시 말하지만, 오직 [컬렉션 관리자](#check-permissions)만이 컬렉션을 관리할 수 있습니다.

    :::image type="content" source="./media/tutorial-register-scan-on-premises-sql-server/select-add-a-collection.png" alt-text="'컬렉션 추가' 단추가 강조 표시된 새 컬렉션 창을 보여주는 Purview Studio 창의 스크린샷" border="true":::

1. 오른쪽 패널에서 컬렉션 이름과 설명을 입력합니다. 필요한 경우 새 컬렉션에 컬렉션 관리자로 사용자나 그룹도 추가할 수 있습니다.
1. **만들기** 를 선택합니다.

    :::image type="content" source="./media/tutorial-register-scan-on-premises-sql-server/create-collection.png" alt-text="표시 이름 및 컬렉션 관리자를 선택하고 만들기 단추가 강조 표시된 새 컬렉션 창을 보여주는 Purview Studio 창의 스크린샷" border="true":::

1. 새 컬렉션 정보가 페이지에 반영됩니다.

    :::image type="content" source="./media/tutorial-register-scan-on-premises-sql-server/created-collection.png" alt-text="새로 만든 컬렉션 창을 보여 주는 Purview Studio 창의 스크린샷" border="true":::

## <a name="create-a-self-hosted-integration-runtime"></a>자체 호스팅 Integration Runtime 만들기

SHIR(자체 호스팅 통합 런타임)은 Purview에서 온-프레미스 데이터 원본에 연결하는 데 사용되는 컴퓨팅 인프라입니다. SHIR은 온-프레미스 데이터 원본과 동일한 네트워크에 있는 머신에 다운로드되어 설치됩니다.

이 자습서에서는 자체 호스팅 통합 런타임을 설치할 머신이 인터넷에 네트워크 연결할 수 있다고 가정합니다. 이 연결을 통해 SHIR은 원본과 Azure Purview 간에 통신할 수 있습니다. 머신에 제한된 방화벽이 있거나 방화벽을 보호하려는 경우 [자체 호스팅 통합 런타임에 대한 네트워크 요구 사항](manage-integration-runtimes.md#networking-requirements)을 살펴보세요.

1. Purview Studio 홈 페이지의 왼쪽 탐색 창에서 **데이터 맵** 을 선택합니다.

1. 왼쪽 창의 **원본 관리** 에서 **통합 런타임** 을 선택한 다음, **+ 새로 만들기** 를 선택합니다.

      :::image type="content" source="media/tutorial-register-scan-on-premises-sql-server/select-integration-runtime.png" alt-text="통합 런타임 단추를 선택합니다.":::

1. **통합 런타임 설정** 페이지에서 **자체 호스팅** 을 선택하여 자체 호스팅 IR을 만든 다음 **계속** 을 선택합니다.

   :::image type="content" source="media/tutorial-register-scan-on-premises-sql-server/select-self-hosted-ir.png" alt-text="새 SHIR을 만듭니다.":::

1. IR의 이름을 입력하고 생성을 선택합니다.

1. **통합 런타임 설정** 페이지에서 **수동 설정** 섹션의 단계를 따릅니다. 다운로드 사이트에서 통합 런타임을 온-프레미스 SQL Server와 동일한 네트워크에 있는 VM 또는 머신에 다운로드해야 합니다. 필요한 머신 종류에 대한 자세한 내용은 [통합 런타임 관리 가이드](manage-integration-runtimes.md#prerequisites)를 참조하세요.

   :::image type="content" source="media/tutorial-register-scan-on-premises-sql-server/integration-runtime-settings.png" alt-text="키 가져오기":::

   - 인증 키를 복사하여 붙여넣습니다.

   - 로컬 Windows 컴퓨터에 [Microsoft Integration Runtime](https://www.microsoft.com/download/details.aspx?id=39717)에서 자체 호스팅 통합 런타임을 다운로드합니다. 설치 관리자를 실행합니다. 5\.4.7803.1 및 5.6.7795.1 등의 자체 호스팅 통합 런타임 버전이 지원됩니다. 

   - 이전에 저장한 두 키 중 하나를 **Integration Runtime(자체 호스팅) 등록** 페이지에 붙여넣고 **등록** 을 선택합니다.

     :::image type="content" source="media/tutorial-register-scan-on-premises-sql-server/register-integration-runtime.png" alt-text="키를 입력합니다.":::

   - **새 통합 런타임(자체 호스팅) 노드** 페이지에서 **마침** 을 선택합니다.

1. 자체 호스팅 통합 런타임이 성공적으로 등록되면 다음 창이 표시됩니다.

   :::image type="content" source="media/tutorial-register-scan-on-premises-sql-server/successfully-registered.png" alt-text="성공적으로 등록 되었습니다.":::

## <a name="set-up-sql-authentication"></a>SQL 인증 설정

SQL Server 온-프레미스에 대한 인증을 설정하는 방법은 한 가지뿐입니다.

- SQL 인증

### <a name="sql-authentication"></a>SQL 인증

SQL 계정에는 **master** 데이터베이스에 대한 액세스 권한이 있어야 합니다. `sys.databases`가 데이터베이스에 있기 때문입니다. Purview 스캐너는 서버에서 모든 SQL 데이터베이스를 찾으려면 `sys.databases`를 열거해야 합니다.

#### <a name="create-a-new-login-and-user"></a>새 로그인 및 사용자 만들기

새 로그인 및 사용자를 만들어 SQL Server를 검사하려면 아래 단계를 따르세요.

> [!Note]
> 아래의 모든 단계는 [여기](https://github.com/Azure/Purview-Samples/blob/master/TSQL-Code-Permissions/grant-access-to-on-prem-sql-databases.sql)에 제공된 코드를 사용하여 실행할 수 있습니다.

1. SSMS(SQL Server Management Studio)로 이동하여 서버에 연결하고 보안으로 이동한 다음, 로그인을 마우스 오른쪽 단추로 길게 클릭하고(또는 마우스 오른쪽 단추로 클릭하고) 새 로그인을 만듭니다. SQL 인증을 선택해야 합니다.

   :::image type="content" source="media/tutorial-register-scan-on-premises-sql-server/create-new-login-user.png" alt-text="새 로그인 및 사용자를 만듭니다.":::

1. 왼쪽 탐색에서 서버 역할을 선택하고 공개 역할이 할당되었는지 확인합니다.

1. 왼쪽 탐색에서 사용자 매핑을 선택하고 지도에서 모든 데이터베이스를 선택한 다음 데이터베이스 역할(**db_datareader**)을 선택합니다.

1. **확인** 을 선택하여 저장합니다.

1. 사용자를 길게 클릭하고(또는 마우스 오른쪽 단추를 클릭하고) **속성** 을 선택하여 앞에서 만든 사용자로 다시 이동합니다. 새 암호를 입력하고 확인합니다. '이전 암호 지정'을 선택하고 이전 암호를 입력합니다. **새 로그인을 만드는 즉시 암호를 변경해야 합니다.**

   :::image type="content" source="media/tutorial-register-scan-on-premises-sql-server/change-password.png" alt-text="암호 변경":::

#### <a name="create-a-key-vault-credential"></a>Key Vault 자격 증명 만들기

1. Azure Portal에서 키 자격 증명 모음으로 이동합니다. **설정 > 비밀** 을 선택합니다.

   :::image type="content" source="media/tutorial-register-scan-on-premises-sql-server/select-secrets.png" alt-text="왼쪽 메뉴에서 [비밀] 선택":::

1. **+ 생성/가져오기** 를 선택합니다.

   :::image type="content" source="media/tutorial-register-scan-on-premises-sql-server/select-generate-import.png" alt-text="위쪽 메뉴에서 생성/가져오기 선택":::

1. 업로드 옵션의 경우 **수동** 을 선택하고, SQL 서버 로그인의 *암호* 로 **이름** 및 **값** 을 입력합니다. **사용** 이 **예** 로 설정되어 있는지 확인합니다. 활성화 날짜와 만료 날짜를 설정하는 경우 오늘 날짜가 두 날짜 사이에 있어야 합니다. 그렇지 않으면 자격 증명을 사용할 수 없습니다.

   :::image type="content" source="media/tutorial-register-scan-on-premises-sql-server/create-credential-secret.png" alt-text="키 자격 증명 모음 자격 증명에 값 추가":::

1. **만들기** 를 선택하여 완료합니다.
1. [Azure Purview Studio](#sign-in-to-purview-studio)의 왼쪽 메뉴에서 **관리** 페이지로 이동합니다.

   :::image type="content" source="media/tutorial-register-scan-on-premises-sql-server/select-management.png" alt-text="왼쪽 메뉴에서 [관리] 페이지 선택":::

1. **자격 증명** 페이지를 선택합니다.

   :::image type="content" source="media/tutorial-register-scan-on-premises-sql-server/select-credentials.png" alt-text="[관리] 페이지의 자격 증명 단추가 강조 표시되었습니다.":::

1. **자격 증명 페이지** 에서 **Key Vault 연결 관리하기** 를 선택합니다.

   :::image type="content" source="media/tutorial-register-scan-on-premises-sql-server/manage-key-vault-connections.png" alt-text="Azure Key Vault 연결 관리":::

1. Key Vault 연결 관리하기 페이지에서 **+ 새로 만들기** 를 선택합니다.

1. 필요한 정보를 입력한 다음 **만들기** 를 선택합니다.

1. 다음 예시와 같이 Key Vault가 Azure Purview 계정에 성공적으로 연결되었는지 확인합니다.

   :::image type="content" source="media/tutorial-register-scan-on-premises-sql-server/view-kv-connections.png" alt-text="확인을 위해 Azure Key Vault 연결을 봅니다.":::

1. **+ 새로 만들기** 를 선택하여 SQL Server에 대한 새 자격 증명을 만듭니다.

   :::image type="content" source="media/tutorial-register-scan-on-premises-sql-server/select-new.png" alt-text="[+ 새로 만들기]를 선택하여 자격 증명을 만듭니다.":::

1. 필요한 정보를 입력합니다. **인증 방법** 및 비밀을 선택할 **Key Vault 연결** 을 선택합니다.

1. 모든 세부 정보를 입력한 후 **만들기** 를 선택합니다.

   :::image type="content" source="media/tutorial-register-scan-on-premises-sql-server/new-credential.png" alt-text="새 자격 증명":::

1. 새 자격 증명이 목록 보기에 나타나고 사용할 준비가 되었는지 확인합니다.

   :::image type="content" source="media/tutorial-register-scan-on-premises-sql-server/view-credentials.png" alt-text="자격 증명 보기":::

## <a name="register-sql-server"></a>SQL Server 등록

1. [Azure Portal](https://portal.azure.com)에서 Purview 계정으로 이동하여 [Purview Studio](#sign-in-to-purview-studio)를 선택합니다.

1. 왼쪽 탐색의 원본 및 검사에서 **통합 런타임** 을 선택합니다. 자체 호스팅 통합 런타임이 설정되어 있는지 확인합니다. 설정되지 않은 경우 [여기](manage-integration-runtimes.md)에 설명된 단계에 따라 온-프레미스 네트워크에 액세스할 수 있는 온-프레미스 또는 Azure VM에서 검사하기 위한 자체 호스팅 통합 런타임을 만듭니다.

1. 왼쪽 탐색 메뉴에서 **데이터 맵** 을 선택합니다.

1. **등록** 을 선택합니다.

1. **SQL Server** 를 선택한 다음 **계속** 을 선택합니다.

   :::image type="content" source="media/tutorial-register-scan-on-premises-sql-server/set-up-sql-data-source.png" alt-text="SQL 데이터 원본 설정":::

1. 식별 이름과 서버 엔드포인트를 제공한 다음 **마침** 을 선택하여 데이터 원본을 등록합니다. 예를 들어 SQL Server FQDN이 **foobar.database.windows.net** 인 경우 서버 엔드포인트로 *foobar* 를 입력합니다.

새 검색을 만들고 실행하려면 다음을 수행합니다.

1. Purview Studio의 왼쪽 창에서 **데이터 맵** 탭을 선택합니다.

1. 등록한 SQL Server 원본을 선택합니다.

1. **새 검사** 를 선택합니다.

1. 데이터 원본에 연결할 자격 증명을 선택합니다.

1. 목록에서 적절한 항목을 선택하여 검사 범위를 특정 테이블로 지정할 수 있습니다.

   :::image type="content" source="media/tutorial-register-scan-on-premises-sql-server/on-premises-sql-scope-your-scan.png" alt-text="검사 범위 지정":::

1. 그런 다음, 검사 규칙 집합을 선택합니다. 시스템 기본값, 기존 사용자 지정 규칙 집합 중 하나를 선택하거나 새 규칙 집합을 인라인으로 만들 수 있습니다.

   :::image type="content" source="media/tutorial-register-scan-on-premises-sql-server/on-premises-sql-scan-rule-set.png" alt-text="검사 규칙 집합":::

1. 검사 트리거를 선택합니다. 일정을 설정하거나 검사를 한 번 실행할 수 있습니다.

   :::image type="content" source="media/tutorial-register-scan-on-premises-sql-server/trigger-scan.png" alt-text="트리거":::

1. 검사를 검토하고 **저장 및 실행** 을 선택합니다.

[!INCLUDE [view and manage scans](includes/view-and-manage-scans.md)]

## <a name="clean-up-resources"></a>리소스 정리

이 Purview 또는 SQL 원본을 더 이상 필요 없으면 다음 단계에 따라 통합 런타임, SQL 자격 증명 및 Purview 리소스를 삭제해도 됩니다.

### <a name="remove-shir-from-purview"></a>Purview에서 SHIR 제거

1. [Purview Studio](https://web.purview.azure.com/resource/) 홈 페이지의 왼쪽 탐색 창에서 **데이터 맵** 을 선택합니다.

1. 왼쪽 창의 **원본 관리** 에서 **통합 런타임** 을 선택합니다.

      :::image type="content" source="media/tutorial-register-scan-on-premises-sql-server/select-integration-runtime.png" alt-text="통합 런타임 단추를 선택합니다.":::

1. 통합 런타임 옆에 있는 확인란을 선택한 다음, **삭제** 단추를 선택합니다.

      :::image type="content" source="media/tutorial-register-scan-on-premises-sql-server/delete-integration-runtime.png" alt-text="통합 런타임 옆에 있는 확인란과 삭제 단추가 강조 표시되었습니다.":::

1. 다음 창에서 **확인** 을 선택하여 삭제를 확인합니다.

1. 창이 자동으로 새로 고침되고, 통합 런타임에 더 이상 SHIR이 나열되지 않습니다.

### <a name="uninstall-self-hosted-integration-runtime"></a>자체 호스팅 통합 런타임 제거

1. 자체 호스팅 통합 런타임이 설치된 머신에 로그인합니다.
1. 제어판을 열고, *프로그램 제거* 에서 "Microsoft Integration Runtime"을 검색합니다.

1. 기존 통합 런타임을 제거합니다.

> [!IMPORTANT] 
> 다음 프로세스에서 예를 선택합니다. 제거 프로세스 동안 데이터를 유지하지 마세요.

:::image type="content" source="media/tutorial-register-scan-on-premises-sql-server/select-yes.png" alt-text="통합 런타임에서 모든 사용자 데이터를 삭제하는 '예' 단추의 스크린샷":::

### <a name="remove-sql-credentials"></a>SQL 자격 증명 제거

1. [Azure Portal](https://portal.azure.com)로 이동한 다음, Purview 자격 증명을 저장한 Key Vault 리소스로 이동합니다.

1. 왼쪽 메뉴의 **설정** 에서 **비밀** 을 선택합니다.

   :::image type="content" source="media/tutorial-register-scan-on-premises-sql-server/select-secrets.png" alt-text="Azure Key Vault의 왼쪽 메뉴에서 [비밀]을 선택합니다.":::

1. 이 자습서에서 만든 SQL Server 자격 증명 비밀을 선택합니다.
1. **삭제** 선택

   :::image type="content" source="media/tutorial-register-scan-on-premises-sql-server/select-delete-credential.png" alt-text="Azure Key Vault 비밀의 위쪽 메뉴에서 비밀을 삭제합니다.":::

1. **예** 를 선택하여 리소스를 영구적으로 삭제합니다.

### <a name="delete-purview-account"></a>Purview 계정 삭제

이 자습서를 완료한 후 부서의 Purview 계정을 삭제하려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com)로 이동한 다음, Purview 계정으로 이동합니다.

1. 페이지 위쪽에서 **삭제** 단추를 선택합니다.

   :::image type="content" source="media/tutorial-register-scan-on-premises-sql-server/select-delete.png" alt-text="Azure Portal의 Purview 계정 페이지에서 삭제 단추를 선택합니다.":::

1. 프로세스가 완료되면 Azure Portal에서 알림을 받게 됩니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Purview REST API 사용](tutorial-using-rest-apis.md)
