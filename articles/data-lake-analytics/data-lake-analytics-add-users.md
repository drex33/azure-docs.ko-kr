---
title: Azure Data Lake Analytics 계정에 사용자 추가
description: 사용자 추가 마법사 및 Azure PowerShell을 사용하여 Data Lake Analytics 계정에 사용자를 올바르게 추가하는 방법을 알아봅니다.
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 05/24/2018
ms.openlocfilehash: c04b1bbd62e156aeb8d3a0ebb244cfbc753dec52
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96020826"
---
# <a name="adding-a-user-in-the-azure-portal"></a>Azure Portal에서 사용자 추가

## <a name="start-the-add-user-wizard"></a>사용자 추가 마법사 시작
1. https://portal.azure.com을 통해 Azure Data Lake Analytics를 엽니다.
2. **사용자 추가 마법사** 를 클릭합니다.
3. **사용자 선택** 단계에서 추가하려는 사용자를 찾습니다. **선택** 을 클릭합니다.
4. **역할 선택** 단계에서 **Data Lake Analytics 개발자** 를 선택합니다. 이 역할에는 U-SQL 작업을 제출/모니터링/관리하는 데 필요한 최소한의 사용 권한 집합이 있습니다. Azure 서비스를 관리하기 위한 그룹이 없는 경우 이 역할에 할당합니다.
5. **카탈로그 사용 권한 선택** 단계에서 사용자가 액세스해야 하는 추가 데이터베이스를 선택합니다. 작업을 제출하려면 "마스터"라는 기본 고정적인 데이터베이스에 대한 읽기 및 쓰기 권한이 필요합니다. 작업이 끝나면 **확인** 을 클릭합니다.
6. **선택한 사용 권한 할당** 이라는 마지막 단계에서 마법사에 의한 변경 내용을 검토합니다. **확인** 을 클릭합니다.


## <a name="configure-acls-for-data-folders"></a>데이터 폴더에 대한 ACL 구성
입력 데이터 및 출력 데이터를 포함하는 폴더에서 필요에 따라 "R-X" 또는 "RWX"을 부여합니다.


## <a name="optionally-add-the-user-to-the-azure-data-lake-storage-gen1-role-reader-role"></a>필요에 따라 Azure Data Lake Storage Gen1 **읽기 권한자** 역할에 사용자를 추가합니다.
1.  Azure Data Lake Storage Gen1 계정을 찾습니다.
2.  **사용자** 를 클릭합니다.
3. **추가** 를 클릭합니다.
4.  이 그룹에 할당하려는 Azure 역할을 선택합니다.
5.  읽기 권한자 역할에 할당합니다. 이 역할에는 ADLSGen1에 저장된 데이터를 이동/관리하는 데 필요한 최소한의 사용 권한 집합이 있습니다. Azure 서비스를 관리하기 위한 그룹이 없는 경우 이 역할에 할당합니다.
6.  그룹의 이름에 입력합니다.
7.  **확인** 을 클릭합니다.

## <a name="adding-a-user-using-powershell"></a>PowerShell을 사용하여 사용자 추가

1. [Azure PowerShell을 설치 및 구성하는 방법](/powershell/azure/) 가이드의 지침을 따르세요.
2. [Add-AdlaJobUser.ps1](https://github.com/Azure/AzureDataLake/blob/master/Samples/PowerShell/ADLAUsers/Add-AdlaJobUser.ps1) PowerShell 스크립트를 다운로드합니다.
3. PowerShell 스크립트를 실행합니다. 

사용자에게 작업을 제출하고, 새 작업 메타데이터를 보고, 이전 메타데이터를 보는 액세스 권한을 부여하는 샘플 명령은 다음과 같습니다.

`Add-AdlaJobUser.ps1 -Account myadlsaccount -EntityToAdd 546e153e-0ecf-417b-ab7f-aa01ce4a7bff -EntityType User -FullReplication`


## <a name="next-steps"></a>다음 단계

* [Azure Data Lake Analytics 개요](data-lake-analytics-overview.md)
* [Azure Portal을 사용하여 Data Lake Analytics 시작](data-lake-analytics-get-started-portal.md)
* [Azure PowerShell을 사용하여 Azure Data Lake Analytics 관리](data-lake-analytics-manage-use-powershell.md)
