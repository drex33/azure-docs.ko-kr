---
title: '빠른 시작: Synapse 작업 영역 만들기'
description: 이 가이드의 단계에 따라 Synapse 작업 영역을 만듭니다.
services: synapse-analytics
author: saveenr
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: workspace
ms.date: 09/03/2020
ms.author: saveenr
ms.reviewer: jrasnick
ms.custom: subject-rbac-steps, mode-other
ms.openlocfilehash: 8db73a682ace898c7043c9fb0a3dc7b52967fa28
ms.sourcegitcommit: 56235f8694cc5f88db3afcc8c27ce769ecf455b0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133076990"
---
# <a name="quickstart-create-a-synapse-workspace"></a>빠른 시작: Synapse 작업 영역 만들기
이 빠른 시작에서는 Azure Portal을 사용하여 Azure Synapse 작업 영역을 만드는 단계를 설명합니다.

## <a name="create-a-synapse-workspace"></a>Synapse 작업 영역 만들기

1. [Azure Portal](https://portal.azure.com)을 열고, 위쪽에서 **Synapse** 를 검색합니다.
1. **서비스** 아래의 검색 결과에서 **Azure Synapse Analytics** 를 선택합니다.
1. **추가** 를 선택하여 작업 영역을 만듭니다.
1. **기본 사항** 탭에서 작업 영역에 고유한 이름을 지정합니다. 이 문서에서는 **mysworkspace** 를 사용합니다.
1. 작업 영역을 만들려면 ADLSGEN2 계정이 필요합니다. 새 항목을 만드는 것이 가장 간단합니다. 기존 항목을 다시 사용하려는 경우 몇 가지 추가 구성을 수행해야 합니다. 
1. 옵션 1: 새 ADLSGEN2 계정 만들기 
    1. **Data Lake Storage Gen 2 선택** 에서 **새로 만들기** 를 클릭하고 이름을 **contosolake** 로 지정합니다.
    1. **Data Lake Storage Gen 2 선택** 에서 **파일 시스템** 을 클릭하고 이름을 **users** 로 지정합니다.
1. 옵션 2: 이 문서 하단에 있는 **스토리지 계정 준비** 지침을 참조하세요.
1. Azure Synapse 작업 영역에서는 이 스토리지 계정을 "기본" 스토리지 계정으로 사용하고 작업 영역 데이터를 저장하는 컨테이너로 사용합니다. 작업 영역은 데이터를 Apache Spark 테이블에 저장합니다. Spark 애플리케이션 로그를 **/synapse/workspacename** 이라는 폴더에 저장합니다.
1. **검토 + 만들기** > **만들기** 를 차례로 선택합니다. 몇 분 안에 작업 영역이 준비됩니다.

> [!NOTE]
> Azure Synapse 작업 영역을 만든 후에는 작업 영역을 다른 Azure Active Directory 테넌트로 이동할 수 없습니다. 구독 마이그레이션 또는 기타 작업을 통해 작업을 수행하는 경우 작업 영역 내의 아티팩트에 액세스할 수 없게 될 수 있습니다.
> 또한 현재는 [CSP(클라우드 솔루션 공급자)](/partner-center/csp-overview) 구독에서 Synapse Analytics 작업 영역을 만들 수 없습니다.

## <a name="open-synapse-studio"></a>Synapse Studio 열기

Azure Synapse 작업 영역이 만들어지면 다음 두 가지 방법으로 Synapse Studio를 열 수 있습니다.

* [Azure Portal](https://portal.azure.com)에서 Synapse 작업 영역을 엽니다. **개요** 섹션의 위쪽에서 **Synapse Studio 시작** 을 선택합니다.
* `https://web.azuresynapse.net`(으)로 이동하여 작업 영역에 로그인합니다.

## <a name="prepare-an-existing-storage-account-for-use-with-azure-synapse-analytics"></a>Azure Synapse Analytics와 함께 사용할 기존 스토리지 계정 준비

1. [Azure Portal](https://portal.azure.com)을 엽니다.
1. 기존 ADLSGEN2 스토리지 계정으로 이동
1. **액세스 제어(IAM)** 를 선택합니다.
1. **추가** > **역할 할당 추가** 를 선택하여 역할 할당 추가 페이지를 엽니다.
1. 다음 역할을 할당합니다. 세부 단계에 대해서는 [Azure Portal을 사용하여 Azure 역할 할당](../role-based-access-control/role-assignments-portal.md)을 참조하세요.
    
    | 설정 | 값 |
    | --- | --- |
    | 역할 | 소유자 및 Storage Blob 데이터 소유자 |
    | 다음에 대한 액세스 할당 | [USER |
    | 멤버 | 사용자 이름 |

    ![Azure Portal에서 역할 할당 페이지를 추가합니다.](../../includes/role-based-access-control/media/add-role-assignment-page.png)
1. 왼쪽 창에서 **컨테이너** 를 선택하여 컨테이너를 만듭니다.
1. 임의의 컨테이너 이름을 지정할 수 있습니다. 이 문서에서는 컨테이너 이름을 **users** 로 지정합니다.
1. **공용 액세스 수준** 기본 설정을 적용한 다음, **만들기** 를 선택합니다.

### <a name="configure-access-to-the-storage-account-from-your-workspace"></a>작업 영역에서 스토리지 계정에 대한 액세스 구성

Azure Synapse 작업 영역의 관리 ID는 이미 스토리지 계정에 액세스할 수 있습니다. 다음 단계에 따라 확인합니다.

1. [Azure Portal](https://portal.azure.com) 및 작업 영역에 대해 선택한 기본 스토리지 계정을 엽니다.
1. **액세스 제어(IAM)** 를 선택합니다.
1. **추가** > **역할 할당 추가** 를 선택하여 역할 할당 추가 페이지를 엽니다.
1. 다음 역할을 할당합니다. 세부 단계에 대해서는 [Azure Portal을 사용하여 Azure 역할 할당](../role-based-access-control/role-assignments-portal.md)을 참조하세요.
    
    | 설정 | 값 |
    | --- | --- |
    | 역할 | Storage Blob 데이터 기여자 |
    | 다음에 대한 액세스 할당 | MANAGEDIDENTITY |
    | 멤버 | myworkspace  |

    > [!NOTE]
    > 관리 ID 이름은 작업 영역 이름이기도 합니다.

    ![Azure Portal에서 역할 할당 페이지를 추가합니다.](../../includes/role-based-access-control/media/add-role-assignment-page.png)
1. **저장** 을 선택합니다.

## <a name="next-steps"></a>다음 단계

* [전용 SQL 풀 만들기](quickstart-create-sql-pool-studio.md) 
* [서버리스 Apache Spark 풀 만들기](quickstart-create-apache-spark-pool-portal.md)
* [서버리스 SQL 풀 사용](quickstart-sql-on-demand.md)
