---
title: Azure 파일을 검색하는 방법
description: 이 방법 가이드에서는 Azure 파일을 검사하는 방법에 대해 자세히 설명합니다.
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 06/22/2021
ms.openlocfilehash: dbb29fea5253bbd00e66f0ed3d123ddc38533b8e
ms.sourcegitcommit: 2eac9bd319fb8b3a1080518c73ee337123286fa2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/31/2021
ms.locfileid: "123256441"
---
# <a name="register-and-scan-azure-files"></a>Azure Files 등록 및 검사

## <a name="supported-capabilities"></a>지원되는 기능

Azure Files는 시스템 기본 및 사용자 지정 분류 규칙을 기반으로 메타데이터 및 분류를 캡처하는 전체 및 증분 검색을 지원합니다.

csv, tsv, psv, ssv와 같은 파일 형식의 경우 다음 논리가 있을 때 스키마가 추출됩니다.

1. 첫 번째 행 값이 비어 있지 않음
2. 첫 번째 행 값이 고유함
3. 첫 번째 행 값은 날짜 및 숫자가 아님

## <a name="prerequisites"></a>필수 조건

- 데이터 원본을 등록하기 전에 Azure Purview 계정을 만듭니다. Purview 계정을 만드는 방법에 관한 자세한 내용은 [빠른 시작: Azure Purview 계정 만들기](create-catalog-portal.md)를 참조하세요.
- 검사를 설정하고 예약하려면 데이터 원본 관리자여야 합니다. 자세한 내용은 [카탈로그 권한](catalog-permissions.md)을 참조하세요.

## <a name="setting-up-authentication-for-a-scan"></a>검사 인증 설정

현재 Azure 파일 공유에 대한 인증을 설정하는 방법은 다음 한 가지뿐입니다.

- 계정 키

### <a name="account-key"></a>계정 키

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

## <a name="register-an-azure-files-storage-account"></a>Azure Files 스토리지 계정 등록

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

## <a name="creating-and-running-a-scan"></a>검사 만들기 및 실행

새 검사를 만들고 실행하려면 다음 단계를 수행합니다.

1. Purview Studio의 왼쪽 창에서 **데이터 맵** 탭을 선택합니다.

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

- [Azure Purview 데이터 카탈로그 찾아보기](how-to-browse-catalog.md)
- [Azure Purview Data Catalog 검색](how-to-search-catalog.md)
