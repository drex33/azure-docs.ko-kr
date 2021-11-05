---
title: Azure Files 커넥트 및 관리
description: 이 가이드에서는 Azure Purview에서 Azure Files 연결하고 Purview의 기능을 사용하여 Azure Files 원본을 검사하고 관리하는 방법을 설명합니다.
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-map
ms.topic: how-to
ms.date: 11/02/2021
ms.custom: template-how-to, ignite-fall-2021
ms.openlocfilehash: 95abf27060748255f24b089cfc4fb9229f33bfa5
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/05/2021
ms.locfileid: "131848062"
---
# <a name="connect-to-and-manage-azure-files-in-azure-purview"></a>Azure Purview에서 Azure Files 커넥트 및 관리

이 문서에서는 Azure Files 등록하는 방법 및 Azure Purview에서 Azure Files 인증하고 상호 작용하는 방법을 설명합니다. Azure Purview에 대한 자세한 내용은 [소개 문서](overview.md)를 읽어보십시오.

## <a name="supported-capabilities"></a>지원되는 기능

|**메타데이터 추출**|  **전체 검사**  |**증분 검사**|**범위 검사**|**분류**|**액세스 정책**|**계보**|
|---|---|---|---|---|---|---|
| [예](#register) | [예](#scan) | [예](#scan) | [예](#scan) | [예](#scan) | 예 | 제한** |

\** 데이터 세트가 에서 원본/싱크로 사용되는 경우 계보가 [지원됩니다Data Factory 복사 작업](how-to-link-azure-data-factory.md) 

Azure Files는 시스템 기본 및 사용자 지정 분류 규칙을 기반으로 메타데이터 및 분류를 캡처하는 전체 및 증분 검색을 지원합니다.

csv, tsv, psv, ssv와 같은 파일 형식의 경우 다음 논리가 있을 때 스키마가 추출됩니다.

1. 첫 번째 행 값이 비어 있지 않음
2. 첫 번째 행 값이 고유함
3. 첫 번째 행 값은 날짜 또는 숫자가 아닙니다.

## <a name="prerequisites"></a>필수 구성 요소

* 활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* 활성 [Purview 리소스](create-catalog-portal.md).

* 원본을 등록하고 Purview Studio에서 관리하려면 데이터 원본 관리자 및 데이터 읽기 권한자여야 합니다. 자세한 내용은 [Azure Purview 권한 페이지](catalog-permissions.md)를 참조하세요.

## <a name="register"></a>등록

이 섹션에서는 [Purview Studio를](https://web.purview.azure.com/)사용하여 Azure Purview에서 Azure Files 등록하는 방법에 대해 설명합니다.

### <a name="authentication-for-registration"></a>등록 인증

현재 Azure 파일 공유에 대한 인증을 설정하는 방법은 다음 한 가지뿐입니다.

- 계정 키

#### <a name="account-key-to-register"></a>등록할 계정 키

선택한 인증 방법이 **계정 키** 인 경우 액세스 키를 가져와서 Key Vault에 저장해야 합니다.

1. 스토리지 계정으로 이동합니다.
1. **설정 > 액세스 키** 선택
1. *키* 를 복사하고 다음 단계를 위해 저장
1. 키 자격 증명 모음으로 이동
1. **설정 > 비밀** 을 선택합니다.
1. **+ 생성/가져오기** 를 선택하고 스토리지 계정의 *키* 로 **이름** 및 **값** 입력
1. **만들기** 를 선택하여 완료합니다.
1. 키 자격 증명 모음이 아직 Purview에 연결되지 않은 경우 [새 키 자격 증명 모음 연결을 만들어야](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account) 합니다.
1. 마지막으로 키를 사용하여 검사를 설정하기 위한 [새 자격 증명을 만듭니다](manage-credentials.md#create-a-new-credential).

### <a name="steps-to-register"></a>등록 단계

새 Azure Files 계정을 데이터 카탈로그에 등록하려면 다음 단계를 수행합니다.

1. Purview Data Studio로 이동합니다.
1. 왼쪽 탐색 메뉴에서 **데이터 맵** 을 선택합니다.
1. **등록** 을 선택합니다.
1. **원본 등록** 에서 **Azure Files** 를 선택합니다.
1. **계속** 을 선택합니다.

:::image type="content" source="media/register-scan-azure-files/register-sources.png" alt-text="새 데이터 원본 등록" border="true":::

**원본 등록(Azure Files)** 화면에서 다음 단계를 수행합니다.

1. 카탈로그에서 나열되는 데이터 원본의 **이름** 을 입력합니다.
2. Azure Storage 계정을 필터링하려면 Azure 구독을 선택합니다.
3. Azure Storage 계정을 선택합니다.
4. 컬렉션을 선택하거나 새로 만듭니다(선택 사항).
5. **등록** 을 선택하여 데이터 원본을 등록합니다.

:::image type="content" source="media/register-scan-azure-files/azure-file-register-source.png" alt-text="원본 등록 옵션" border="true":::

## <a name="scan"></a>검사

아래 단계에 따라 Azure Files 검사하여 자산을 자동으로 식별하고 데이터를 분류합니다. 일반적인 검사에 대한 자세한 내용은 [검사 및 수집 소개](concept-scans-and-ingestion.md)를 참조하세요.

### <a name="create-and-run-scan"></a>검사 만들기 및 실행

새 검사를 만들고 실행하려면 다음 단계를 수행합니다.

1. [Purview Studio](https://web.purview.azure.com/resource/)의 왼쪽 창에서 **데이터 맵** 탭을 선택합니다.

1. 등록한 Azure Files 원본을 선택합니다.

1. **새 검사** 를 선택합니다.

1. 데이터 원본에 연결할 계정 키 자격 증명을 선택합니다.

   :::image type="content" source="media/register-scan-azure-files/set-up-scan-azure-file.png" alt-text="검사 설정":::

1. 목록에서 적절한 항목을 선택하여 검사 범위를 특정 데이터베이스로 지정할 수 있습니다.

   :::image type="content" source="media/register-scan-azure-files/azure-file-scope-your-scan.png" alt-text="검사 범위 지정":::

1. 그런 다음, 검사 규칙 집합을 선택합니다. 시스템 기본값, 기존 사용자 지정 규칙 집합 중 하나를 선택하거나 새 규칙 집합을 인라인으로 만들 수 있습니다.

   :::image type="content" source="media/register-scan-azure-files/azure-file-scan-rule-set.png" alt-text="검사 규칙 집합":::

1. 검사 트리거를 선택합니다. 다시 발생하도록 일정을 설정하거나 검사를 한 번 실행할 수 있습니다.

   :::image type="content" source="media/register-scan-azure-files/trigger-scan.png" alt-text="트리거":::

1. 검사를 검토하고 **저장 및 실행** 을 선택합니다.

[!INCLUDE [create and manage scans](includes/view-and-manage-scans.md)]

## <a name="next-steps"></a>다음 단계

이제 소스를 등록했으므로 아래 가이드에 따라 Purview 및 데이터에 대해 자세히 알아봅니다.

- [Azure Purview의 데이터 인사이트](concept-insights.md)
- [Azure Purview의 계보](catalog-lineage-user-guide.md)
- [Data Catalog 검색](how-to-search-catalog.md)
