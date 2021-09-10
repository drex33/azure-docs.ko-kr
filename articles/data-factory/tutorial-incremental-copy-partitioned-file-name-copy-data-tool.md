---
title: 시간 분할 파일 이름에 따라 증분 방식으로 새 파일 복사
description: Azure 데이터 팩터리를 만든 다음 데이터 복사 도구를 사용하여 시간 분한 파일 이름에 따라 새 파일을 증분 방식으로 로드합니다.
author: dearandyxu
ms.author: yexu
ms.service: data-factory
ms.subservice: tutorials
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 07/15/2021
ms.openlocfilehash: 79405511c7b2b563dd47db6d4d5f08a6e552d385
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122642507"
---
# <a name="incrementally-copy-new-files-based-on-time-partitioned-file-name-by-using-the-copy-data-tool"></a>데이터 복사 도구를 사용하여 시간 분할 파일 이름에 따라 새 파일을 증분 방식으로 복사

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

이 자습서에서는 Azure Portal을 사용하여 데이터 팩터리를 만듭니다. 그런 다음 데이터 복사 도구를 사용하여 Azure Blob Storage 간에 시간 분할 파일 이름에 기반하여 새 파일을 증분 방식으로 복사하는 파이프라인을 만듭니다.

> [!NOTE]
> Azure Data Factory를 처음 사용하는 경우 [Azure Data Factory 소개](introduction.md)를 참조하세요.

이 자습서에서는 다음 단계를 수행합니다.

> [!div class="checklist"]
> * 데이터 팩터리를 만듭니다.
> * 데이터 복사 도구를 사용하여 파이프라인 만들기
> * 파이프라인 및 작업 실행을 모니터링합니다.

## <a name="prerequisites"></a>필수 구성 요소

* **Azure 구독**: Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/)을 만듭니다.
* **Azure Storage 계정**: Blob Storage를 _원본_ 및 _싱크_ 데이터 저장소로 사용합니다. Azure Storage 계정이 없는 경우 [스토리지 계정 만들기](../storage/common/storage-account-create.md)의 지침을 참조하세요.

### <a name="create-two-containers-in-blob-storage"></a>Blob Storage에 두 개의 컨테이너 만들기

다음 단계를 수행하여 자습서에 대비해 Blob Storage를 준비합니다.

1. **원본** 이라는 컨테이너를 만듭니다.  컨테이너에서 **2021/07/15/06** 으로 폴더 경로를 만듭니다. 빈 텍스트 파일을 만들고 이름을 **file1.txt** 로 지정합니다. 스토리지 계정의 **source/2021/07/15/06** 폴더 경로에 file1.txt를 업로드합니다.  [Azure Storage Explorer](https://storageexplorer.com/)와 같은 다양한 도구를 사용하여 이러한 작업을 수행할 수 있습니다.

    ![파일 업로드](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/upload-file.png)

    > [!NOTE]
    > UTC 시간으로 폴더 이름을 조정하세요.  예를 들어 현재 UTC 시간이 2021년 7월 15일 오전 6시 10분인 경우 **source/{연}/{월}/{일}/{시간}/** 규칙에 따라 폴더 경로를 **source/2021/07/15/06/** 으로 만들 수 있습니다.

2. **대상** 이라는 컨테이너를 만듭니다. [Azure Storage Explorer](https://storageexplorer.com/)와 같은 다양한 도구를 사용하여 이러한 작업을 수행할 수 있습니다.

## <a name="create-a-data-factory"></a>데이터 팩터리 만들기

1. 왼쪽 메뉴에서 **리소스 만들기** > **통합** > **Data Factory** 를 선택합니다.

   !["새로 만들기" 창에서 데이터 팩터리 선택](./media/doc-common-process/new-azure-data-factory-menu.png)

2. **새 데이터 팩터리** 페이지의 **이름** 아래에서 **ADFTutorialDataFactory** 를 입력합니다.

    데이터 팩터리 이름은 _전역적으로 고유_ 해야 합니다. 다음과 같은 오류 메시지가 표시될 수 있습니다.

   :::image type="content" source="./media/doc-common-process/name-not-available-error.png" alt-text="중복된 이름에 대한 새 데이터 팩터리 오류 메시지.":::

   이름 값에 대한 오류 메시지가 표시되면 데이터 팩터리에 대한 다른 이름을 입력합니다. 예를 들어 _**yourname**_**ADFTutorialDataFactory** 를 사용합니다. 데이터 팩터리 아티팩트에 대한 명명 규칙은 [데이터 팩터리 명명 규칙](naming-rules.md)을 참조하세요.
3. 새 데이터 팩터리를 만들 Azure **구독** 을 선택합니다.
4. **리소스 그룹** 에 대해 다음 단계 중 하나를 사용합니다.

    a. **기존 항목 사용** 을 선택하고 드롭다운 목록에서 기존 리소스 그룹을 선택합니다.

    b. **새로 만들기** 를 선택하고 리소스 그룹의 이름을 입력합니다. 
         
    리소스 그룹에 대한 자세한 내용은 [리소스 그룹을 사용하여 Azure 리소스 관리](../azure-resource-manager/management/overview.md)를 참조하세요.

5. **버전** 아래에서 버전에 대해 **V2** 를 선택합니다.
6. **위치** 아래에서 데이터 팩터리에 대한 위치를 선택합니다. 지원되는 위치만 드롭다운 목록에 표시됩니다. 데이터 팩터리에서 사용되는 데이터 저장소(예: Azure Storage, SQL Database) 및 계산(예: Azure HDInsight)은 다른 위치와 지역에 있을 수 있습니다.
7. **만들기** 를 선택합니다.
8. 만들기가 완료되면 **Data Factory** 홈페이지가 표시됩니다.
9. 별도의 탭에서 Azure Data Factory 사용자 인터페이스(UI)를 시작하려면 **Azure Data Factory Studio 열기** 타일에서 **열기** 를 선택합니다.

    :::image type="content" source="./media/doc-common-process/data-factory-home-page.png" alt-text="Azure Data Factory Studio 열기 타일이 있는 Azure Data Factory 홈페이지":::

## <a name="use-the-copy-data-tool-to-create-a-pipeline"></a>데이터 복사 도구를 사용하여 파이프라인 만들기

1. Azure Data Factory 홈페이지에서 **수집** 제목을 선택하여 데이터 복사 도구를 시작합니다.

   ![ADF 홈페이지를 보여 주는 스크린샷](./media/doc-common-process/get-started-page.png)

2. **속성** 페이지에서 다음 단계를 수행합니다.
    1. **작업 유형** 에서 **기본 제공 복사 작업** 을 선택합니다.

    1. **작업 주기 또는 작업 일정** 에서 **연속 창** 을 선택합니다.

    1. **되풀이** 에서 **1시간** 을 입력합니다.

    1. **다음** 을 선택합니다.

    ![속성 페이지](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/copy-data-tool-properties-page.png)
3. **원본 데이터 저장소** 페이지에서 다음 단계를 완료합니다.

    a. **+ 새 연결** 을 선택하여 연결을 추가합니다.
    
    b. 갤러리에서 **Azure Blob Storage** 를 선택한 다음, **계속** 을 선택합니다.
    
    다. **새 연결(Azure Blob Storage)** 페이지에서 연결의 이름을 입력합니다. Azure 구독을 선택하고 **스토리지 계정 이름** 목록에서 스토리지 계정을 선택합니다. 연결을 테스트한 다음, **만들기** 를 선택합니다.

    ![원본 데이터 저장소 페이지](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/source-data-store-page-connection.png)

    d. **원본 데이터 저장소** 페이지의 **연결** 섹션에서 새로 만든 연결을 선택합니다.

    e. **파일 또는 폴더** 섹션에서 **원본** 컨테이너를 찾아 선택한 다음, **확인** 을 선택합니다.

    f. **파일 로드 동작** 에서 **증분 로드: 시간 분할 폴더/파일 이름** 을 선택합니다.

    g. 동적 폴더 경로를 **source/{연}/{월}/{일}/{시간}/** 으로 작성하고 다음 스크린샷에 표시된 대로 형식을 변경합니다. 
    
    h. **이진 복사** 를 선택하고 **다음** 을 선택합니다.

    :::image type="content" source="./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/source-data-store-page.png" alt-text="원본 데이터 저장소 페이지의 구성을 보여 주는 스크린샷":::


4. **대상 데이터 저장소** 페이지에서 다음 단계를 완료합니다.
    1. 데이터 원본 저장소와 동일한 스토리지 계정인 **AzureBlobStorage** 를 선택합니다.

    1. **대상** 폴더를 찾아서 선택하고 **확인** 을 선택합니다.

    1. 동적 폴더 경로를 **destination/{연}/{월}/{일}/{시간}/** 으로 작성하고 다음 스크린샷에 표시된 대로 형식을 변경합니다.

    1. **다음** 을 선택합니다.

    :::image type="content" source="./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/destination-data-store.png" alt-text="대상 데이터 저장소 페이지의 구성을 보여 주는 스크린샷":::

5. **설정** 페이지의 **작업 이름** 에서 **DeltaCopyFromBlobPipeline** 을 입력하고 **다음** 을 선택합니다. Data Factory UI에서 지정한 작업 이름이 있는 파이프라인을 만듭니다.

    :::image type="content" source="./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/settings-page.png" alt-text="설정 페이지의 구성을 보여 주는 스크린샷":::

6. **요약** 페이지에서 설정을 검토하고 **다음** 을 선택합니다.

    ![요약 페이지](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/summary-page.png)

7. **배포** 페이지에서 **모니터** 를 선택하여 파이프라인(작업)을 모니터링합니다.
    ![배포 페이지](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/deployment-page.png)

8. 왼쪽의 **모니터** 탭이 자동으로 선택됩니다.  파이프라인이 실행이 자동으로 트리거될 때를 기다려야 합니다(약 1시간 후). 실행되면 파이프라인 이름 링크 **DeltaCopyFromBlobPipeline** 를 선택하여 활동 실행 세부 사항을 확인하고 파이프라인을 다시 실행합니다. **새로 고침** 을 선택하여 목록을 새로 고칩니다.

    ![파이프라인 실행 창을 보여주는 스크린샷.](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/monitor-pipeline-runs-1.png)
9. 파이프라인에는 하나의 작업(복사 작업)만 있으므로 하나의 항목만 표시됩니다. **원본** 및 **대상** 열의 열 너비를 조정하여 더 자세히 표시합니다(필요한 경우). *source/2021/07/15/06/* 에서 *destination/2021/07/15/06/* 에 복사되고 동일한 파일 이름을 사용하는 원본 파일(file1.txt)을 볼 수 있습니다. 

    ![파이프라인 실행 세부 사항을 보여주는 스크린샷.](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/monitor-pipeline-runs2.png)

    Azure Storage Explorer(https://storageexplorer.com/) 를 사용하여 파일을 검색해서 동일한지 확인할 수도 있습니다.

    ![대상에 대한 파이프라인 실행 세부 사항을 보여주는 스크린샷.](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/monitor-pipeline-runs3.png)

10. **file2.txt** 로 새 이름을 사용하여 다른 빈 텍스트 파일을 만듭니다. 스토리지 계정의 **source/2021/07/15/07** 폴더 경로에 file2.txt 파일을 업로드합니다. [Azure Storage Explorer](https://storageexplorer.com/)와 같은 다양한 도구를 사용하여 이러한 작업을 수행할 수 있습니다.

    > [!NOTE]
    > 새 폴더 경로를 만들어야 할 수 있습니다. UTC 시간으로 폴더 이름을 조정하세요.  예를 들어 현재 UTC 시간이 2021년 7월 15일 오전 7시 30분인 경우. **{연}/{월}/{일}/{시간}/** 규칙에 따라 폴더 경로를 **source/2021/07/15/07/** 로 만들 수 있습니다.

11. **파이프라인 실행** 보기로 다시 돌아가려면 **모든 파이프라인 실행** 을 선택하고, 한 시간 후 동일한 파이프라인이 자동으로 다시 트리거될 때까지 기다립니다.  

    ![해당 페이지로 돌아가는 모든 파이프라인 실행 링크를 보여주는 스크린샷.](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/monitor-pipeline-runs5.png)

12. 두 번째 파이프라인 실행에 대해 새 **DeltaCopyFromBlobPipeline** 링크를 선택하고 세부 사항을 검토하는 작업도 동일하게 수행합니다. **source/2021/07/15/07/** 에서 **destination/2021/07/15/07/** 에 복사되고 동일한 파일 이름을 사용하는 원본 파일(file2.txt)이 표시됩니다. Azure Storage Explorer(https://storageexplorer.com/) 를 사용하여 **destination** 컨테이너의 파일을 검색해서 동일한지 확인할 수도 있습니다.


## <a name="next-steps"></a>다음 단계
Azure에서 Spark 클러스터를 사용하여 데이터를 변환하는 방법을 알아보려면 다음 자습서로 진행하세요.

> [!div class="nextstepaction"]
>[클라우드에서 Spark 클러스터를 사용하여 데이터 변환](tutorial-transform-data-spark-portal.md)
