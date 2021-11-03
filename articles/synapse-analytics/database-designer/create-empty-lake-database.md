---
title: 빈 레이크 데이터베이스 만들기
description: Azure Synapse Analytics 쉽게 추가할 수 있는 빈 Lake 데이터베이스를 만드는 방법을 알아봅니다.
author: aamerril
ms.author: aamerril
ms.service: synapse-analytics
ms: 'subservice:'
ms.topic: how-to
ms.date: 11/02/2021
ms.custom: template-how-to, ignite-fall-2021
ms.openlocfilehash: 8717168532f84c8a3ed8735b5298eb0bb8065d20
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131053708"
---
# <a name="how-to-create-an-empty-lake-database"></a>방법: 빈 레이크 데이터베이스 만들기

이 문서에서는 데이터베이스 디자이너를 사용하여 Azure Synapse Analytics 빈 [Lake 데이터베이스를](./concepts-lake-database.md) 만드는 방법을 알아봅니다. 데이터베이스 디자이너를 사용하면 코드를 작성하지 않고도 데이터베이스를 쉽게 만들고 배포할 수 있습니다. 

## <a name="prerequisites"></a>사전 요구 사항

- 갤러리에서 레이크 데이터베이스 템플릿을 탐색하려면 최소한 Synapse 사용자 역할 권한이 필요합니다.
- Synapse 관리자, Synapse 기여자 또는 Synapse Artifact Publisher 레이크 데이터베이스를 만들려면 Synapse 작업 영역에 대한 권한이 필요합니다.
- Storage 데이터 레이크에 대한 Blob 데이터 기여자 권한이 필요합니다.

## <a name="create-lake-database-from-database-template"></a>데이터베이스 템플릿에서 레이크 데이터베이스 만들기
1. Azure Synapse Analytics 작업 영역 **홈** 허브의 왼쪽에 있는 **데이터** 탭을 선택합니다. **데이터** 탭이 열리고 작업 영역에 이미 있는 데이터베이스 목록이 표시됩니다.
2. 단추를 마우스로 **+** 가리키고 를 선택한 다음, **Lake 데이터베이스(미리 보기)** 를 선택합니다.
![빈 레이크 데이터베이스 만들기를 보여주는 스크린샷](./media/create-empty-lake-database/create-empty-lakedb.png)
3. 데이터베이스 디자이너 탭이 빈 데이터베이스와 함께 열립니다.
4. 데이터베이스 디자이너의 오른쪽에는 구성해야 하는 **속성이** 있습니다.
    - **이름** - 데이터베이스에 이름을 지정합니다. 데이터베이스를 게시한 후에는 이름을 편집할 수 없으므로 선택한 이름이 올바른지 확인합니다.
    - **설명** - 데이터베이스에 설명을 제공하는 것은 선택 사항이지만 사용자가 데이터베이스의 용도를 이해할 수 있도록 합니다.
    - **데이터베이스에 대한 Storage 설정** - 이 섹션에는 데이터베이스의 테이블에 대한 기본 스토리지 정보가 포함되어 있습니다. 이 기본값은 테이블 자체에서 재정의되지 않는 한 데이터베이스의 각 테이블에 적용됩니다.
    - **연결된 서비스** - Azure Data Lake Storage 데이터를 저장하는 데 사용되는 기본 연결된 서비스입니다.  Synapse 작업 영역과 연결된 기본 연결된 서비스가 표시되지만 연결된 서비스를 원하는 모든 ADLS 스토리지 **계정으로** 변경할 수 있습니다. 
    - **입력 폴더** - 파일 브라우저를 사용하여 연결된 서비스 내에서 기본 컨테이너 및 폴더 경로를 설정하는 데 사용됩니다.
    - **데이터 형식** - Azure Synapse Lake 데이터베이스는 데이터에 대한 스토리지 형식으로 parquet 및 구분된 텍스트를 지원합니다.

> [!NOTE]
> 항상 테이블별로 기본 스토리지 설정을 재정의할 수 있으며 기본값은 사용자 지정 가능한 상태로 유지됩니다. 무엇을 선택해야 할지 잘 모르는 경우 나중에 다시 방문할 수 있습니다.
 
5. 데이터베이스에 테이블을 추가하려면 + **테이블** 단추를 선택합니다. 
    - **사용자 지정** 은 캔버스에 새 테이블을 추가합니다.
    - **템플릿에서** 갤러리가 열리고 새 테이블을 추가할 때 사용할 데이터베이스 템플릿을 선택할 수 있습니다. 자세한 내용은 [데이터베이스 템플릿에서 레이크 데이터베이스 만들기를 참조하세요.](./create-lake-database-from-lake-database-templates.md)
    - **데이터 레이크에서** 이미 레이크에 있는 데이터를 사용하여 테이블 스키마를 가져올 수 있습니다.
6. **사용자 지정** 을 선택합니다. 캔버스에 Table_1 라는 새 테이블이 표시됩니다.
7. 그런 다음 테이블 이름, 설명, 스토리지 설정, 열 및 관계를 포함하여 Table_1 사용자 지정할 수 있습니다. 자세한 내용은 [Lake 데이터베이스 수정을 참조하세요.](./modify-lake-database.md)
8. + 테이블을 선택한 다음 데이터 레이크에서 를 선택하여 **데이터 레이크에서** 새 **테이블을** 추가합니다.
9. **데이터 레이크에서 외부 테이블 만들기** 창이 나타납니다. 아래 세부 정보로 창을 채우고 **계속을** 선택합니다.
    - **외부 테이블 이름은** 만들려는 테이블에 지정하려는 이름입니다.
    - **연결된 서비스는** 데이터 파일이 있는 Azure Data Lake Storage 위치를 포함하는 연결된 서비스입니다.
    - **입력 파일 또는 폴더는** 파일 브라우저를 사용하여 테이블을 만들려는 레이크로 이동하여 파일을 선택합니다.
![Data Lake 창에서 외부 테이블 만들기의 옵션을 보여주는 스크린샷](./media/create-empty-lake-database/create-from-lake.png)
    - 다음 화면에서 Azure Synapse 파일을 미리 보기하고 스키마를 검색합니다.
    - 데이터 형식과 관련된 모든 설정을 업데이트할 수 있는 **새 외부 테이블** 페이지와 Synapse가 파일을 올바르게 식별했는지 확인하기 위해 데이터 미리 **보기** 페이지가 표시됩니다.
    - 설정에 만족하면 **만들기를** 선택합니다.
    - 선택한 이름의 새 테이블이 캔버스에 추가되고 테이블 섹션의 **Storage 설정에** 지정한 파일이 표시됩니다.
    
10. 데이터베이스를 사용자 지정하면 이제 게시할 차례입니다. Synapse 작업 영역과 Git 통합을 사용하는 경우 변경 내용을 커밋하고 협업 분기에 병합해야 합니다. [Azure Synapse 소스 제어에 대해 자세히 알아보세요.](././cicd/../../cicd/source-control.md) Synapse 라이브 모드를 사용하는 경우 "게시"를 선택할 수 있습니다.
    - 데이터베이스가 게시되기 전에 오류에 대한 유효성이 검사됩니다. 발견된 오류는 오류 해결 방법에 대한 지침과 함께 알림 탭에 표시됩니다.
    
       ![데이터베이스의 유효성 검사 오류를 보여주는 유효성 검사 창의 스크린샷](./media/create-empty-lake-database/validation-error.png)
    - 게시하면 Azure Synapse Metastore에 데이터베이스 스키마가 생성됩니다. 게시하면 Azure Synapse Metastore에 데이터베이스 스키마가 생성됩니다. 게시 후 데이터베이스 및 테이블 개체가 다른 Azure 서비스에 표시되고 데이터베이스의 메타데이터가 Power BI 또는 Purview와 같은 앱으로 흐를 수 있습니다.

11. 이제 Azure Synapse 빈 Lake 데이터베이스를 만들고 **사용자 지정** 및 **데이터 레이크에서** 옵션을 사용하여 테이블을 추가했습니다.

## <a name="next-steps"></a>다음 단계

아래 링크를 사용하여 데이터베이스 디자이너 기능을 계속 탐색합니다. 
- [레이크 데이터베이스 수정](./modify-lake-database.md)
- [Lake 데이터베이스에 대해 자세히 알아보기](./concepts-lake-database.md)
- [Lake 데이터베이스 템플릿에서 레이크 데이터베이스 만들기](./create-lake-database-from-lake-database-templates.md)
