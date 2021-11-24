---
title: Azure Portal 사용하여 마이그레이션(Data Lake Storage Gen1에서 Gen2로)
description: Azure Portal 사용하여 Azure Data Lake Storage Gen1과 Azure Data Lake Storage Gen2 간 마이그레이션 작업을 간소화할 수 있습니다.
author: normesta
ms.topic: how-to
ms.author: normesta
ms.date: 07/13/2021
ms.service: storage
ms.reviewer: rukmani-msft
ms.subservice: data-lake-storage-gen2
ms.openlocfilehash: 32fd80d20a105771c1c88461b621422834899ff5
ms.sourcegitcommit: 3a063c59bb9396ce1d4b9a3565b194edf30393a2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/23/2021
ms.locfileid: "132964076"
---
# <a name="migrate-azure-data-lake-storage-from-gen1-to-gen2-by-using-the-azure-portal-preview"></a>Azure Portal(미리 보기)를 사용하여 Azure Data Lake Storage Gen1에서 Gen2로 마이그레이션합니다.

Azure Portal 사용하여 마이그레이션을 완료하는데 필요한 단계 수를 줄일 수 있습니다. 데이터 및 메타데이터(예: 타임스탬프 및 ACL)는 Gen2 사용 계정에 자동으로 이동합니다. 전체 마이그레이션을 수행하는 경우 요청이 자동으로 리디렉션되므로 워크로드를 Gen2로 가리킬 필요가 없습니다.

> [!IMPORTANT]
> Azure Portal 사용하여 Gen1에서 Gen2로 마이그레이션하는 것은 현재 미리 보기로 제공됩니다.
> 베타, 미리 보기로 제공되거나 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 약관은 [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

이 문서에서는 다음 작업을 안내합니다.

- 1단계:미리 보기에 등록

- 2단계: Gen2 기능이 있는 스토리지 계정 만들기

- 3단계: RBAC 역할 할당 확인

- 4단계: 마이그레이션 수행

- 5단계. 애플리케이션 테스트

- 6단계: 마이그레이션 완료

Gen1에서 Gen2로 마이그레이션하는 방법에 대한 일반 지침을 읽어야 합니다. 자세한 내용은 [Azure Data Lake Storage Gen1에서 Gen2로 마이그레이션을 참조하세요.](data-lake-storage-migrate-gen1-to-gen2.md)

> [!NOTE]
> 이 문서에서는 가독성을 높이기 위해 Azure Data Lake Storage Gen1을 *Gen1* 이라고 칭하고, Azure Data Lake Storage Gen2를 *Gen2* 라고 칭합니다.

## <a name="enroll-in-the-preview"></a>미리 보기에 등록

미리 보기에 등록하려면 [이 양식](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR4SeyCfCfrtBlHWFupvoz_BUMEFNQzBSQTE0OU1aM0hXMDlBNEwzVTYyRy4u&wdLOR=cBC075B83-9324-4399-B94E-05A919D007C9)을 참조하세요. 등록한 후 Gen2 기능을 사용하여 스토리지 계정을 만듭니다(다음 섹션 참조).

Microsoft는 약 7일 이내에 새 계정을 마이그레이션할 수 있도록 연락할 예정입니다.

## <a name="create-a-storage-account-with-gen2-capabilities"></a>Gen2 기능 사용 가능한 스토리지 계정을 만듭니다.

Azure Data Lake Storage Gen2는 전용 서비스 또는 스토리지 계정 유형이 아닙니다. Azure Storage 계정의 **계층 구조 네임스페이스** 기능을 사용하도록 설정하면 얻을 수 있는 기능 집합입니다. Gen2 기능이 있는 계정을 만들려면 [Azure Data Lake Storage Gen2와 함께 사용할 스토리지 계정 만들기](create-data-lake-storage-account.md)를 참조하세요.

계정을 만들 때 다음 값을 사용하여 설정을 구성해야 합니다.

| 설정 | 값 |
|--|--|
| **스토리지 계정 이름** | 원하는 이름. 이 이름은 Gen1 계정의 이름과 일치할 필요가 없으며 선택한 구독에 포함될 수 있습니다. |
| **위치** | Data Lake Storage Gen1 계정에서 사용하는 동일한 지역 |
| **복제** | LRS 또는 ZRS |
| **최소 TLS 버전** | 1.0 |
| **NFS v3** | 사용 안 함 |
| **계층 구조 네임스페이스** | 사용 |

> [!NOTE]
> Azure Portal 마이그레이션 도구는 계정 설정을 이동하지 않습니다. 따라서 계정을 만든 후에는 암호화, 네트워크 방화벽, 데이터 보호와 같은 설정을 수동으로 구성해야 합니다.

> [!IMPORTANT]
> 비어 있는 새로 만든 Gen2 계정을 사용해야 합니다. 이전에 사용한 Gen2 계정으로 마이그레이션하지 않는 것이 중요합니다.

## <a name="verify-rbac-role-assignments"></a>RBAC 역할 할당 확인

Gen2의 경우 [스토리지 Blob 데이터 소유자](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) 역할이 스토리지 계정, 상위 리소스 그룹 또는 구독 범위에서 Azure AD(Azure Active Directory) 사용자 ID에 할당되었는지 확인합니다.

Gen1의 경우 Gen1 계정, 상위 리소스 그룹 또는 구독 범위에서 Azure AD ID에 [소유자](../../role-based-access-control/built-in-roles.md#owner) 역할이 할당되었는지 확인합니다.

## <a name="perform-the-migration"></a>마이그레이션 수행

시작하기 전에 데이터만 복사할지 아니면 전체 마이그레이션을 수행할지 결정합니다.

데이터만 복사하는 경우 마이그레이션이 완료된 후 두 계정이 모두 활성 상태로 유지됩니다. 마이그레이션하는 동안 Gen1 계정은 읽기 전용이 됩니다. 그런 다음 새 Gen2 사용 스토리지 계정을 사용하도록 컴퓨팅 워크로드를 업데이트할 수 있습니다. 애플리케이션과 워크로드가 예상대로 작동하는지 확인한 후에는 Gen1 계정 사용을 중지할 수 있습니다. 이 옵션은 Microsoft에서 권장합니다.

전체 마이그레이션을 수행하면 데이터가 Gen1에서 Gen2로 복사됩니다. 그런 다음 Gen1 URI가 Gen2 URI로 리디렉션됩니다. 마이그레이션이 완료되면 Gen1 계정에 액세스할 수 없으며 모든 Gen1 요청은 Gen2 사용 계정으로 리디렉션됩니다. Gen1 계정은 더 이상 데이터 작업에 사용할 수 없으며 Gen1 계정의 데이터에 대한 요금이 더 이상 청구되지 않습니다. Gen2 사용 계정에서 파이프라인이 실행되면 Gen1 계정을 삭제할 수 있습니다.

> [!NOTE]
> Gen2는 Azure Data Lake Analytics 애플리케이션을 지원하지 않습니다. 있는 경우 Gen1에서 Gen2로 마이그레이션하기 전에 Azure Synapse Analytics 또는 지원되는 다른 워크로드로 이동해야 합니다.

### <a name="option-1-copy-data-from-gen1-to-gen2"></a>옵션 1: Gen1에서 Gen2로 데이터 복사

1. [Azure Portal](https://portal.azure.com/)에 로그인하여 시작합니다.

2. Data Lake Storage Gen1 계정을 찾고 계정 개요를 표시합니다.

3. **데이터 마이그레이션** 단추를 고릅니다.

   > [!div class="mx-imgBorder"]
   > ![마이그레이션 단추](./media/data-lake-storage-migrate-gen1-to-gen2-azure-portal/migration-tool.png)

4. **새 Gen2 계정에 데이터 복사** 를 선택합니다.

   > [!div class="mx-imgBorder"]
   > ![데이터 복사 옵션](./media/data-lake-storage-migrate-gen1-to-gen2-azure-portal/migration-data-option.png)

5. 확인란을 선택하여 데이터 마이그레이션을 수행하는 데 Microsoft의 동의를 제공합니다. 그런 다음 **적용** 단추를 클릭합니다.

   > [!div class="mx-imgBorder"]
   > ![동의를 제공하는 확인란](./media/data-lake-storage-migrate-gen1-to-gen2-azure-portal/migration-consent.png)

   > [!IMPORTANT] 
   > 데이터가 마이그레이션되는 동안 Gen1 계정이 읽기 전용이 되고 Gen2 사용 계정이 비활성화됩니다. 마이그레이션이 완료되면 두 계정을 읽고 쓸 수 있습니다.

   **마이그레이션 중지** 버튼을 선택하여 언제든지 마이그레이션을 중지할 수 있습니다.

   > [!div class="mx-imgBorder"]
   > ![마이그레이션 중지 옵션](./media/data-lake-storage-migrate-gen1-to-gen2-azure-portal/migration-stop.png)

### <a name="option-2-perform-a-complete-migration"></a>옵션 2: 전체 마이그레이션 수행

1. [Azure Portal](https://portal.azure.com/)에 로그인하여 시작합니다.

2. Data Lake Storage Gen1 계정을 찾고 계정 개요를 표시합니다.

3. **데이터 마이그레이션** 단추를 고릅니다.

   > [!div class="mx-imgBorder"]
   > ![마이그레이션 단추](./media/data-lake-storage-migrate-gen1-to-gen2-azure-portal/migration-tool.png)

4. **새 2세대 계정으로 마이그레이션 완료를** 선택합니다.

   > [!div class="mx-imgBorder"]
   > ![마이그레이션 완료 옵션](./media/data-lake-storage-migrate-gen1-to-gen2-azure-portal/migration-complete-option.png)

5. 확인란을 선택하여 데이터 마이그레이션을 수행하는 데 Microsoft의 동의를 제공합니다. 그런 다음 **적용** 단추를 클릭합니다.

   > [!div class="mx-imgBorder"]
   > ![동의 확인란](./media/data-lake-storage-migrate-gen1-to-gen2-azure-portal/migration-consent.png)

   > [!IMPORTANT] 
   > 데이터가 마이그레이션되는 동안 Gen1 계정은 읽기 전용이 되고 Gen2 사용 계정은 사용하지 않도록 설정됩니다.
   > 
   > 또한 Gen1 URI가 리디렉션되는 동안에는 두 계정이 모두 비활성화됩니다.
   > 
   > 마이그레이션이 완료되면 Gen1 계정이 비활성화되고 Gen2 사용 계정에 읽고 쓸 수 있습니다.

   **마이그레이션 중지** 버튼을 선택하여 URI가 리디렉션되기 전에 언제든지 마이그레이션을 중지할 수 있습니다.

   > [!div class="mx-imgBorder"]
   > ![마이그레이션 중지 단추](./media/data-lake-storage-migrate-gen1-to-gen2-azure-portal/migration-stop.png)

## <a name="migrate-workloads-and-applications"></a>워크로드 및 애플리케이션 마이그레이션합니다.

1. Gen2 엔드포인트를 가리키도록 [워크로드의 서비스](./data-lake-storage-supported-azure-services.md)를 구성합니다.

2. Gen2 API를 사용하도록 애플리케이션을 업데이트합니다. 다음 항목을 참조하세요.

   | 환경 | 아티클 |
   |--------|-----------|
   |Azure Storage Explorer |[Azure Storage Explorer를 사용하여 Azure Data Lake Storage Gen2에서 디렉터리 및 파일 관리](data-lake-storage-explorer.md)|
   |.NET |[Azure Data Lake Storage Gen2에서 .NET을 사용하여 디렉터리 및 파일 관리](data-lake-storage-directory-file-acl-dotnet.md)|
   |Java|[Azure Data Lake Storage Gen2에서 Java를 사용하여 디렉터리 및 파일 관리](data-lake-storage-directory-file-acl-java.md)|
   |Python|[Python을 사용하여 Azure Data Lake Storage Gen2에서 디렉터리 및 파일 관리](data-lake-storage-directory-file-acl-python.md)|
   |JavaScript(Node.js)|[Node.js에서 JavaScript SDK를 사용하여 Azure Data Lake Storage Gen2의 디렉터리 및 파일 관리](data-lake-storage-directory-file-acl-javascript.md)|
   |REST API |[Azure Data Lake Store REST API](/rest/api/storageservices/data-lake-storage-gen2)|

3. Data Lake Storage Gen2 [PowerShell cmdlet](data-lake-storage-directory-file-acl-powershell.md) 및 [Azure CLI 명령](data-lake-storage-directory-file-acl-cli.md)을 사용하도록 스크립트를 업데이트합니다.

4. 코드 파일 또는 Databricks Notebook, Apache Hive HQL 파일 또는 워크로드의 일부로 사용되는 다른 모든 파일의 `adl://` 문자열을 포함하는 URI 참조를 검색합니다. 이러한 참조를 새 스토리지 계정의 [Gen2 형식 URI](data-lake-storage-introduction-abfs-uri.md)로 바꿉니다. 예: Gen1 URI: `adl://mydatalakestore.azuredatalakestore.net/mydirectory/myfile`이 `abfss://myfilesystem@mydatalakestore.dfs.core.windows.net/mydirectory/myfile`이 될 수 있습니다.

## <a name="leverage-the-gen1-compatibility-layer-optional"></a>Gen1 호환성 계층 활용(선택 사항)

Microsoft는 응용 프로그램이 Gen1 API를 계속 사용하여 Gen2 사용 계정의 데이터와 상호 작용할 수 있도록 제한된 기능으로 응용 프로그램 호환성을 제공합니다. 호환성 계층은 서버에서 실행되므로 설치할 것이 없습니다.

> [!IMPORTANT]
> Microsoft는 워크로드 및 애플리케이션을 마이그레이션하는 대신 이 기능을 권장하지 않습니다. Gen1 호환성 계층에 대한 지원은 Gen1이 2024년 2월 29일에 중단되면 종료됩니다.

호환성 계층에서 발생하는 문제를 최소화하려면 Gen1 SDK가 다음 버전(또는 그 이상)을 사용하는지 확인해야합니다.

   | 언어 | SDK 버전 |
   |--|--|
   | **.NET** | [2.3.9](https://github.com/Azure/azure-data-lake-store-net/blob/master/CHANGELOG.md) |
   | **Java** | [1.1.21](https://github.com/Azure/azure-data-lake-store-java/blob/master/CHANGES.md) |
   | **Python** | [0.0 51](https://github.com/Azure/azure-data-lake-store-python/blob/master/HISTORY.rst) |

다음 기능은 Gen2, 따라서 호환성 계층에서 지원되지 않습니다.

- 항목 앞에 ListStatus API 옵션을 입력 합니다.

- 연속 토큰이 없는 4000 이상의 파일을 포함 하는 ListStatus API.

- 추가 작업을 위한 청크 인코딩.

- https://management.azure.com/Azure Active Directory (Azure AD) 토큰 대상으로 사용 하는 모든 API 호출입니다.

- 공백이나 탭만 있고 `.`로 끝나거나 `:`를 포함하거나 여러 개의 연속 슬래시(`//`)가 있는 파일 또는 디렉터리 이름.

## <a name="frequently-asked-questions"></a>자주 묻는 질문

#### <a name="how-much-does-the-data-migration-cost"></a>데이터 마이그레이션 비용은 얼마인가요?

데이터 마이그레이션 동안 Gen1 계정의 데이터 저장 및 트랜잭션에 대한 요금이 청구됩니다. 데이터만 복사하는 옵션을 선택하면 마이그레이션이 완료된 후 두 계정에 대한 데이터 스토리지 및 트랜잭션에 대한 요금이 청구됩니다. Gen1 계정에 대한 요금이 청구되지 않도록 하려면 Gen1 계정을 삭제해야 합니다. 애플리케이션 업데이트를 완료한 후 Gen1 계정을 삭제합니다. 전체 마이그레이션을 수행하도록 선택하면 마이그레이션 후 Gen2 사용 계정의 데이터 스토리지 및 트랜잭션에 대해서만 요금이 청구됩니다.

#### <a name="after-the-migration-completes-can-i-choose-to-go-back-to-using-the-gen1-account"></a>마이그레이션이 완료된 후 Gen1 계정을 다시 사용하도록 선택할 수 있나요?

지원되지 않습니다. 마이그레이션이 완료된 후에는 Gen1 계정의 데이터에 액세스할 수 없습니다. Azure Portal에서 Gen1 계정을 계속 볼 수 있으며 준비가 되면 계정을 삭제할 수 있습니다.

#### <a name="i-would-like-to-enable-geo-redundant-storage-grs-on-the-gen2-account-how-do-i-do-that"></a>Gen2 계정에서 GRS(지역 중복 스토리지)를 사용하도록 설정하려면 어떻게 해야 할까요?

마이그레이션이 완료되면 ‘데이터 복사’ 및 ‘마이그레이션 완료’ 옵션 모두에서 응용 프로그램 호환성 계층을 사용하지 않으려는 경우 중복 옵션을 GRS로 변경할 수 있습니다. 애플리케이션 호환성은 GRS 중복성을 사용하는 계정에서 작동하지 않습니다.

#### <a name="gen1-doesnt-have-containers-and-gen2-has-them---what-should-i-expect"></a>Gen1에는 컨테이너가 없고 Gen2에는 어떤 것이 있나요?

Gen2 지원 계정으로 데이터를 복사하면 `Gen1`이라는 컨테이너가 자동으로 생성됩니다. 데이터만 복사하도록 선택한 경우 데이터 복사가 완료된 후 해당 컨테이너의 이름을 변경할 수 있습니다. 전체 마이그레이션을 수행하고 응용 프로그램 호환성 계층을 사용할 계획이라면 컨테이너 이름을 변경하지 않아야 합니다. 더 이상 호환성 계층을 사용하지 않으려면 컨테이너 이름을 변경할 수 있습니다.

#### <a name="what-should-i-consider-in-terms-of-migration-performance"></a>마이그레이션 성능 측면에서 고려해 야 할 사항은 무엇 인가요?

Gen2 사용 계정에 데이터를 복사 하는 경우 성능에 영향을 줄 수 있는 두 가지 요소는 파일 수와 사용자가 보유 한 메타 데이터의 크기입니다. 예를 들어 많은 작은 파일이 마이그레이션의 성능에 영향을 줄 수 있습니다.

## <a name="next-steps"></a>다음 단계

- 일반적으로 마이그레이션에 대해 알아봅니다. 자세한 내용은 [Gen1에서 Gen2로 Azure Data Lake Storage 마이그레이션](data-lake-storage-migrate-gen1-to-gen2.md)을 참조 하세요.
