---
title: 템플릿 개요
description: 미리 정의된 템플릿을 사용하여 Azure Data Factory를 빠르게 시작하는 방법을 알아봅니다.
ms.service: data-factory
ms.subservice: tutorials
ms.topic: conceptual
ms.author: susabat
author: ssabat
ms.custom: seo-lt-2019
ms.date: 08/24/2021
ms.openlocfilehash: 5d07ecedbc8fe4ff0670c9e8aa96fdf2a92359d3
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2021
ms.locfileid: "123098952"
---
# <a name="templates"></a>템플릿

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

템플릿은 Data Factory를 빠르게 시작할 수 있게 해주는 미리 정의된 Azure Data Factory 파이프라인입니다. 템플릿은 Data Factory를 처음 사용하며 빠르게 시작하려는 경우에 유용합니다. 이러한 템플릿은 데이터 통합 프로젝트를 빌드하기 위한 개발 시간을 줄여 개발자의 생산성을 향상합니다.

## <a name="create-data-factory-pipelines-from-templates"></a>템플릿에서 Data Factory 파이프라인 만들기

다음 두 가지 방법으로 템플릿에서 Data Factory 파이프라인 생성을 시작할 수 있습니다.

1.  Data Factory 홈페이지의 **자세히 알아보기** 섹션에서 **파이프라인 템플릿** 을 선택하여 템플릿 갤러리를 엽니다.

    ![Data Factory 홈페이지에서 템플릿 갤러리를 여는 방법을 보여주는 스크린샷](media/doc-common-process/home-page-pipeline-templates-tile.png)

1.  리소스 탐색기의 작성자 탭에서 **+** 를 선택한 다음 **템플릿의 파이프라인** 을 선택하여 템플릿 갤러리를 엽니다.

    ![작성자 탭에서 템플릿 갤러리를 여는 방법을 보여주는 스크린샷](media/solution-templates-introduction/templates-introduction-image-2.png)

## <a name="template-gallery"></a>템플릿 갤러리

![템플릿 갤러리 페이지를 보여주는 스크린샷](media/solution-templates-introduction/templates-introduction-image-3.png)

### <a name="out-of-the-box-data-factory-templates"></a>기본 제공 Data Factory 템플릿

Data Factory는 데이터 팩터리 파이프라인 템플릿을 저장하기 위해 Azure Resource Manager 템플릿을 사용합니다. [공식적인 Azure Data Factory GitHub 리포지토리](https://github.com/Azure/Azure-DataFactory/tree/master/templates)에서 기본 제공 Data Factory 템플릿에 사용되는 매니페스트 파일과 함께 모든 Resource Manager 템플릿을 확인할 수 있습니다. Microsoft에서 제공하는 미리 정의된 템플릿에는 다음 항목이 포함되지만 이에만 제한되지는 않습니다.

-   템플릿 복사:

    -   [데이터베이스에서 대량 복사](solution-template-bulk-copy-with-control-table.md)
    
    -   [LastModifiedDate로 새 파일 복사](solution-template-copy-new-files-lastmodifieddate.md)

    -   [파일 기반 저장소 간에 여러 파일 컨테이너 복사](solution-template-copy-files-multiple-containers.md)

    -   [파일 이동](solution-template-move-files.md)

    -   [데이터베이스에서 델타 복사](solution-template-delta-copy-with-control-table.md)

    -   \<source\>에서 \<destination\>으로 복사

        -   [Amazon S3에서 Azure Data Lake Store Gen 2로 복사](solution-template-migration-s3-azure.md)

        -   Google Big Query에서 Azure Data Lake Store Gen 2로 복사

        -   HDF에서 Azure Data Lake Store Gen 2로 복사

        -   Netezza에서 Azure Data Lake Store Gen 1로 복사

        -   온-프레미스 SQL Server에서 Azure SQL Database로 복사

        -   SQL Server 온-프레미스에서 Azure Synapse Analytics로 복사

        -   Oracle 온-프레미스에서 Azure Synapse Analytics로 복사

-   SSIS 템플릿

    -   SSIS 패키지를 실행하도록 Azure-SSIS Integration Runtime 예약

-   템플릿 변환

    -   [Azure Databricks를 사용한 ETL](solution-template-databricks-notebook.md)

### <a name="my-templates"></a>내 템플릿

파이프라인 탭에서 **템플릿으로 저장** 을 선택하여 파이프라인을 템플릿으로 저장할 수도 있습니다.

![파이프라인을 템플릿으로 저장하는 방법을 보여주는 스크린샷](media/solution-templates-introduction/templates-introduction-image-4.png)

**템플릿 갤러리** 페이지에서 **내 템플릿** 상자를 확인한 후 이 페이지의 오른쪽 창에서 템플릿으로 저장된 파이프라인을 볼 수 있습니다. 

![내 템플릿 창을 보여주는 스크린샷](media/solution-templates-introduction/templates-introduction-image-5.png)

> [!NOTE]
> 내 템플릿 기능을 사용하려면 GIT 통합을 사용하도록 설정해야 합니다. Azure DevOps GIT 및 GitHub가 둘 다 지원됩니다.
