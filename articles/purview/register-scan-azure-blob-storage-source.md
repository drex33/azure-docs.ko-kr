---
title: Azure storage blob을 등록 하 고 검색 하는 방법
description: Azure Purview 데이터 카탈로그에서 Azure Blob 스토리지를 검사하는 방법을 알아봅니다.
author: shsandeep123
ms.author: sandeepshah
ms.service: purview
ms.subservice: purview-data-map
ms.topic: how-to
ms.date: 05/08/2021
ms.openlocfilehash: 40105f18cce8fe515350903837f49d273bd39d03
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/29/2021
ms.locfileid: "129209987"
---
# <a name="register-and-scan-azure-blob-storage"></a>Azure Blob Storage 등록 및 검사

이 문서에서는 Purview에서 Azure Blob Storage 계정을 등록하고 검사를 설정하는 방법을 간략하게 설명합니다.

## <a name="supported-capabilities"></a>지원되는 기능

Azure Blob Storage는 메타데이터 및 스키마를 캡처하기 위해 전체 및 증분 검사를 지원합니다. 또한 시스템 및 사용자 지정 분류 규칙에 따라 데이터를 자동으로 분류합니다.

csv, tsv, psv, ssv와 같은 파일 형식의 경우 다음 논리가 있을 때 스키마가 추출됩니다.

1. 첫 번째 행 값이 비어 있지 않음
2. 첫 번째 행 값이 고유함
3. 첫 번째 행 값은 날짜 및 숫자가 아님

## <a name="prerequisites"></a>필수 조건

- 데이터 원본을 등록하기 전에 Azure Purview 계정을 만듭니다. Purview 계정을 만드는 방법에 관한 자세한 내용은 [빠른 시작: Azure Purview 계정 만들기](create-catalog-portal.md)를 참조하세요.
- Azure Purview 데이터 원본 관리자여야 합니다.

## <a name="setting-up-authentication-for-a-scan"></a>검사 인증 설정

Azure Blob 스토리지 인증을 설정하는 방법에는 다음 세 가지가 있습니다.

- 관리 ID
- 계정 키
- 서비스 주체

### <a name="managed-identity-recommended"></a>관리 ID(권장)

**관리 ID** 를 선택하여 연결을 설정하려면 먼저 Purview 계정에 데이터 원본을 검사할 수 있는 권한을 부여해야 합니다.

1. 스토리지 계정으로 이동합니다.
1. 왼쪽 탐색 메뉴에서 **액세스 제어(IAM)** 를 선택합니다. 
1. **+추가** 를 선택합니다.
1. **역할** 을 **스토리지 Blob 데이터 읽기 권한자** 로 설정하고 입력 **선택** 상자에 Azure Purview 계정 이름을 입력합니다. 그런 다음, **저장** 을 선택하여 Purview 계정에 이 역할을 할당합니다.

> [!Note]
> 자세한 내용은 [Azure Active Directory를 사용하여 Blob 및 큐에 대한 액세스 권한 부여](../storage/blobs/authorize-access-azure-active-directory.md)의 단계를 참조하세요.

### <a name="account-key"></a>계정 키

선택한 인증 방법이 **계정 키** 인 경우 액세스 키를 가져와서 Key Vault에 저장해야 합니다.

1. 스토리지 계정으로 이동합니다.
1. **설정 > 액세스 키** 선택
1. *키* 를 복사하고 다음 단계를 위해 저장
1. 키 자격 증명 모음으로 이동
1. **설정 > 비밀** 을 선택합니다.
1. **+ 생성/가져오기** 를 선택하고 스토리지 계정의 *키* 로 **이름** 및 **값** 입력
1. **만들기** 를 선택하여 완료합니다.
1. 키 자격 증명 모음이 아직 Purview에 연결되지 않은 경우 [새 키 자격 증명 모음 연결을 생성](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account)해야 합니다.
1. 마지막으로 키를 사용하여 검사를 설정하기 위한 [새 자격 증명](manage-credentials.md#create-a-new-credential)을 만듭니다.

### <a name="service-principal"></a>서비스 사용자

서비스 주체를 사용하려면 기존 주체를 사용하거나 새 주체를 만들 수 있습니다. 

> [!Note]
> 새 서비스 주체를 만들어야 하는 경우 다음 단계를 수행하세요.
> 1. [Azure Portal](https://portal.azure.com)로 이동합니다.
> 1. 왼쪽 메뉴에서 **Azure Active Directory** 를 선택합니다.
> 1. **앱 등록** 을 선택합니다.
> 1. **+ 새 애플리케이션 등록** 을 선택합니다.
> 1. **애플리케이션** 의 이름(서비스 사용자 이름)을 입력합니다.
> 1. **이 조직 디렉터리의 계정만** 을 선택합니다.
> 1. 리디렉션 URI에 대해 **웹** 을 선택하고 원하는 URL을 입력합니다. 실제 또는 작업 URL일 필요가 없습니다.
> 1. 그런 다음, **등록** 을 선택합니다.

서비스 주체의 애플리케이션 ID 및 비밀을 가져오는 데 필요합니다.

1. [Azure Portal](https://portal.azure.com)에서 서비스 주체로 이동합니다.
1. **개요** 에서 **애플리케이션(클라이언트) ID** 값을 복사하고, **인증서 및 비밀** 에서 **클라이언트 암호** 값을 복사합니다.
1. 키 자격 증명 모음으로 이동
1. **설정 > 비밀** 을 차례로 선택합니다.
1. **+ 생성/가져오기** 를 선택하고, 선택한 **이름** 및 **값** 을 서비스 주체의 **클라이언트 암호** 로 입력합니다.
1. **만들기** 를 선택하여 완료합니다.
1. 키 자격 증명 모음이 아직 Purview에 연결되지 않은 경우 [새 키 자격 증명 모음 연결을 생성](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account)해야 합니다.
1. 마지막으로 서비스 주체를 통해 [새 자격 증명을 생성](manage-credentials.md#create-a-new-credential)하여 검사를 설정합니다.

#### <a name="granting-the-service-principal-access-to-your-blob-storage"></a>Blob 스토리지에 대한 서비스 주체 액세스 권한 부여

1. 스토리지 계정으로 이동합니다.
1. 왼쪽 탐색 메뉴에서 **액세스 제어(IAM)** 를 선택합니다. 
1. **+추가** 를 선택합니다.
1. **역할** 을 **스토리지 Blob 데이터 읽기 권한자** 로 설정하고 입력 **선택** 상자에 서비스 주체 이름 또는 개체 ID를 입력합니다. 그런 다음, **저장** 을 선택하여 서비스 주체에 이 역할을 할당합니다.

## <a name="firewall-settings"></a>방화벽 설정

> [!NOTE]
> 스토리지 계정에 대해 방화벽을 사용하도록 설정한 경우에는 검색을 설정할 때 **관리 ID** 인증 방법을 사용해야 합니다.

1. [Azure Portal](https://portal.azure.com)에서 스토리지 계정으로 이동
1. **설정 > 네트워킹** 으로 이동
1. **다음에서 액세스 허용** 에서 **선택한 네트워크** 선택
1. **방화벽** 섹션에서 **신뢰할 수 있는 Microsoft 서비스가 이 스토리지 계정에 액세스할 수 있도록 허용** 을 선택하고 **저장** 선택

:::image type="content" source="./media/register-scan-azure-blob-storage-source/firewall-setting.png" alt-text="방화벽 설정을 보여 주는 스크린샷":::

## <a name="register-an-azure-blob-storage-account"></a>Azure Blob Storage 계정 등록

새 Blob 계정을 데이터 카탈로그에 등록하려면 다음을 수행합니다.

1. 포털의 부서의 범위 계정에서 [부서의 범위 Studio](https://web.purview.azure.com/resource/) 로 이동 합니다.
1. Purview Studio의 홈페이지에서 **원본 등록** 을 선택합니다.
1. **등록** 을 선택합니다.
1. **원본 등록** 에서 **Azure Blob Storage** 를 선택합니다.
1. **계속** 을 선택합니다.

**원본 등록(Azure Blob Storage)** 화면에서 다음과 같이 합니다.

1. 카탈로그에서 나열되는 데이터 원본의 **이름** 을 입력합니다. 
1. 스토리지 계정을 필터링하려면 구독을 선택합니다.
1. 스토리지 계정을 선택합니다.
1. 컬렉션을 선택하거나 새로 만듭니다(선택 사항).
1. **등록** 을 선택하여 데이터 원본을 등록합니다.

:::image type="content" source="media/register-scan-azure-blob-storage-source/register-sources.png" alt-text="원본 등록 옵션" border="true":::

## <a name="creating-and-running-a-scan"></a>검사 만들기 및 실행

새 검색을 만들고 실행하려면 다음을 수행합니다.

1. [부서의 범위 Studio](https://web.purview.azure.com/resource/)의 왼쪽 창에서 **데이터 맵** 탭을 선택 합니다.

1. 등록한 Azure Blob 데이터 원본을 선택합니다.

1. **새 검사** 를 선택합니다.

1. 데이터 원본에 연결할 자격 증명을 선택합니다. 

   :::image type="content" source="media/register-scan-azure-blob-storage-source/set-up-scan-blob.png" alt-text="검사 설정":::

1. 목록에서 적절한 항목을 선택하여 특정 폴더 또는 하위 폴더로 검색 범위를 지정할 수 있습니다.

   :::image type="content" source="media/register-scan-azure-blob-storage-source/blob-scope-your-scan.png" alt-text="검사 범위 지정":::

1. 그런 다음, 검사 규칙 집합을 선택합니다. 시스템 기본값, 기존 사용자 지정 규칙 집합 중 하나를 선택하거나 새 규칙 집합을 인라인으로 만들 수 있습니다.

   :::image type="content" source="media/register-scan-azure-blob-storage-source/blob-scan-rule-set.png" alt-text="검사 규칙 집합":::

1. 검사 트리거를 선택합니다. 일정을 설정하거나 검사를 한 번 실행할 수 있습니다.

   :::image type="content" source="media/register-scan-azure-blob-storage-source/trigger-scan.png" alt-text="트리거":::

1. 검사를 검토하고 **저장 및 실행** 을 선택합니다.

[!INCLUDE [view and manage scans](includes/view-and-manage-scans.md)]

## <a name="next-steps"></a>다음 단계

- [Azure Purview 데이터 카탈로그 찾아보기](how-to-browse-catalog.md)
- [Azure Purview Data Catalog 검색](how-to-search-catalog.md)