---
title: 빈 lake 데이터베이스 만들기
description: Azure Synapse Analytics에서에 쉽게 추가할 수 있는 빈 lake 데이터베이스를 만드는 방법에 대해 알아봅니다.
author: aamerril
ms.author: aamerril
ms.service: synapse-analytics
ms.subservice: database-editor
ms.topic: how-to
ms.date: 11/02/2021
ms.custom: template-how-to, ignite-fall-2021
ms.openlocfilehash: 9612fabc94e3425c42bb30e3075a56fb3f789626
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131510452"
---
# <a name="how-to-create-an-empty-lake-database"></a>방법: 빈 lake 데이터베이스 만들기

이 문서에서는 데이터베이스 디자이너를 사용 하 여 Azure Synapse Analytics에서 빈 [lake 데이터베이스](./concepts-lake-database.md) 를 만드는 방법에 대해 알아봅니다. 데이터베이스 디자이너를 사용 하면 코드를 작성 하지 않고도 데이터베이스를 쉽게 만들고 배포할 수 있습니다. 

## <a name="prerequisites"></a>필수 구성 요소

- 갤러리에서 lake 데이터베이스 템플릿을 탐색 하려면 최소 Synapse 사용자 역할 권한이 필요 합니다.
- Synapse 관리자, Synapse 기여자 또는 Synapse 아티팩트 Publisher 권한은 lake 데이터베이스를 만들기 위한 Synapse 작업 영역에 필요 합니다.
- Storage Data lake에는 Blob 데이터 참가자 권한이 필요 합니다.

## <a name="create-lake-database-from-database-template"></a>데이터베이스 템플릿에서 lake 데이터베이스 만들기
1. Azure Synapse Analytics 작업 영역 **홈** 허브에서 왼쪽의 **데이터** 탭을 선택 합니다. **데이터** 탭이 열리고 작업 영역에 이미 있는 데이터베이스 목록이 표시 됩니다.
2. 단추를 마우스로 가리키고 **+** 를 선택한 다음, **Lake 데이터베이스 (미리 보기)** 를 선택 합니다.
![빈 lake 데이터베이스 만들기를 보여 주는 스크린샷](./media/create-empty-lake-database/create-empty-lakedb.png)
3. 데이터베이스 디자이너 탭이 빈 데이터베이스와 함께 열립니다.
4. 데이터베이스 디자이너의 오른쪽에는 구성 해야 하는 **속성이** 있습니다.
    - **이름** -데이터베이스 이름을 지정 합니다. 데이터베이스를 게시 한 후에는 이름을 편집할 수 없으므로 선택한 이름이 올바른지 확인 하십시오.
    - **설명** -데이터베이스 설명에 대 한 설명은 선택 사항 이지만 사용자가 데이터베이스의 용도를 이해할 수 있습니다.
    - **데이터베이스에 대 한 Storage 설정** -이 섹션에서는 데이터베이스의 테이블에 대 한 기본 저장소 정보를 포함 합니다. 이 기본값은 테이블 자체에서 재정의 되지 않는 한 데이터베이스의 각 테이블에 적용 됩니다.
    - **연결 된 서비스** -Azure Data Lake Storage에 데이터를 저장 하는 데 사용 되는 기본 연결 된 서비스입니다.  Synapse 작업 영역과 연결 된 기본 연결 된 서비스가 표시 되지만, **연결 된 서비스** 를 원하는 ADLS 저장소 계정으로 변경할 수 있습니다. 
    - **입력 폴더** -파일 브라우저를 사용 하 여 연결 된 서비스 내에서 기본 컨테이너 및 폴더 경로를 설정 하는 데 사용 됩니다.
    - Azure Synapse의 **data format** -Lake 데이터베이스는 데이터에 대 한 저장소 형식으로 parquet 및 구분 된 텍스트를 지원 합니다.

> [!NOTE]
> 항상 테이블의 기본 저장소 설정을 재정의할 수 있으며 기본값은 사용자 지정 가능 합니다. 선택할 사항을 잘 모르는 경우 나중에 다시 방문할 수 있습니다.
 
5. 데이터베이스에 테이블을 추가 하려면 **+ 테이블** 단추를 선택 합니다. 
    - **사용자 지정** 은 캔버스에 새 테이블을 추가 합니다.
    - **템플릿에서** 갤러리를 열고 새 테이블을 추가할 때 사용할 데이터베이스 템플릿을 선택할 수 있습니다. 자세한 내용은 [데이터베이스 템플릿에서 lake 데이터베이스 만들기](./create-lake-database-from-lake-database-templates.md)를 참조 하세요.
    - **Data Lake에서** 이미 lake에 있는 데이터를 사용 하 여 테이블 스키마를 가져올 수 있습니다.
6. **사용자 지정** 을 선택합니다. Table_1 라는 캔버스에 새 테이블이 표시 됩니다.
7. 그런 다음 테이블 이름, 설명, 저장소 설정, 열, 관계 등을 포함 하 여 Table_1를 사용자 지정할 수 있습니다. 자세한 내용은 [lake 데이터베이스 수정](./modify-lake-database.md)을 참조 하세요.
8. **+ 테이블** 을 선택 하 고 **data lake에서** data lake의 새 테이블을 추가 합니다.
9. **Data lake에서 외부 테이블 만들기** 창이 표시 됩니다. 아래 세부 정보를 사용 하 여 창을 입력 하 고 **계속** 을 선택 합니다.
    - **외부 테이블 이름** 만들려는 테이블에 지정할 이름입니다.
    - **연결 된 서비스** 데이터 파일이 있는 Azure Data Lake Storage 위치를 포함 하는 연결 된 서비스입니다.
    - **입력 파일 또는 폴더** 파일 브라우저를 사용 하 여 lake에서 테이블을 만드는 데 사용할 파일을 찾아 선택 합니다.
![Data lake에서 외부 테이블 만들기 창에 있는 옵션을 보여 주는 스크린샷](./media/create-empty-lake-database/create-from-lake.png)
    - 다음 화면에서 Azure Synapse는 파일을 미리 보고 스키마를 검색 합니다.
    - 데이터 형식과 관련 된 설정을 업데이트 하 고, Synapse에서 파일을 올바르게 식별 했는지 확인 하기 위해 **데이터를 미리 볼** 수 있는 **새로운 외부 테이블** 페이지가 표시 됩니다.
    - 설정에 만족 하는 경우 **만들기** 를 선택 합니다.
    - 선택한 이름을 가진 새 테이블이 캔버스에 추가 되 고 **테이블의 Storage 설정** 섹션에 지정한 파일이 표시 됩니다.
    
10. 사용자 지정 된 데이터베이스를 사용 하 여 지금 게시할 시간입니다. Synapse 작업 영역에서 Git 통합을 사용 하는 경우 변경 내용을 커밋하고 공동 작업 분기에 병합 해야 합니다. [Azure Synapse의 원본 제어에 대해 자세히 알아보세요](././cicd/../../cicd/source-control.md). Synapse Live 모드를 사용 하는 경우 "게시"를 선택할 수 있습니다.
    - 게시 하기 전에 데이터베이스의 오류에 대 한 유효성을 검사 합니다. 발견 된 모든 오류는 오류를 해결 하는 방법에 대 한 지침이 포함 된 알림 탭에 표시 됩니다.
    
       ![데이터베이스의 유효성 검사 오류를 보여 주는 유효성 검사 창의 스크린샷](./media/create-empty-lake-database/validation-error.png)
    - 게시 하면 Azure Synapse Metastore에 데이터베이스 스키마가 생성 됩니다. 게시 후 데이터베이스 및 테이블 개체는 다른 Azure 서비스에 표시 되며 데이터베이스의 메타 데이터를 Power BI 또는 부서의 범위와 같은 앱으로 전달할 수 있습니다.

11. 이제 Azure Synapse에서 빈 lake 데이터베이스를 만들고 **사용자 지정** 및 **data lake** 옵션을 사용 하 여 테이블을 추가 했습니다.

## <a name="next-steps"></a>다음 단계

아래 링크를 사용하여 데이터베이스 디자이너 기능을 계속 탐색합니다. 
- [Lake 데이터베이스 수정](./modify-lake-database.md)
- [Lake 데이터베이스에 대 한 자세한 정보](./concepts-lake-database.md)
- [Lake 데이터베이스 템플릿에서 lake 데이터베이스 만들기](./create-lake-database-from-lake-database-templates.md)
