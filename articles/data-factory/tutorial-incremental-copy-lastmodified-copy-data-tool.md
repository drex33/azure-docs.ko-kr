---
title: 새 파일 및 업데이트된 파일을 증분 방식으로 복사하는 데이터 도구
description: Azure 데이터 팩터리를 만든 다음 데이터 복사 도구를 사용하여 LastModifiedDate에 따라 새 파일을 증분 방식으로 로드합니다.
author: dearandyxu
ms.author: yexu
ms.service: data-factory
ms.subservice: tutorials
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 07/12/2021
ms.openlocfilehash: 0c2cfe51e84931b90ab80588b16fe126aadaf4c3
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122642510"
---
# <a name="incrementally-copy-new-and-changed-files-based-on-lastmodifieddate-by-using-the-copy-data-tool"></a>데이터 복사 도구를 사용하여 LastModifiedDate를 기준으로 새 파일 및 변경된 파일을 증분 방식으로 복사

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

이 자습서에서는 Azure Portal을 사용하여 데이터 팩터리를 만듭니다. 그런 다음 데이터 복사 도구를 사용하여 Azure Blob Storage에서 Azure Blob Storage로 새로운 파일과 변경된 파일만 증분 방식으로 복사하는 파이프라인을 만듭니다. 이 파이프라인은 `LastModifiedDate`를 사용하여 복사할 파일을 결정합니다.

여기에 나와 있는 단계를 완료하면 Azure Data Factory가 원본 스토리지의 모든 파일을 검색하고, `LastModifiedDate`를 기준으로 파일 필터를 적용하고, 새 파일이나 지난 번 이후로 업데이트된 파일만 대상 스토리지에 복사합니다. Data Factory에서 많은 수의 파일을 검색하는 경우에는 오랜 시간이 소요됩니다. 파일 검색은 복사되는 데이터의 양이 적더라도 시간이 많이 소요됩니다.

> [!NOTE]
> Data Factory를 처음 사용하는 경우 [Azure Data Factory 소개](introduction.md)를 참조하세요.

이 자습서에서는 다음 작업을 수행합니다.

> [!div class="checklist"]
> * 데이터 팩터리를 만듭니다.
> * 데이터 복사 도구를 사용하여 파이프라인 만들기
> * 파이프라인 및 작업 실행을 모니터링합니다.

## <a name="prerequisites"></a>필수 구성 요소

* **Azure 구독**: Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/)을 만듭니다.
* **Azure Storage 계정**: 원본 및 싱크 데이터 저장소로 Blob Storage를 사용합니다. Azure Storage 계정이 없는 경우 [스토리지 계정 만들기](../storage/common/storage-account-create.md)의 지침을 따르세요.

## <a name="create-two-containers-in-blob-storage"></a>Blob Storage에 두 개의 컨테이너를 만듭니다.

자습서에 대비해 다음 단계를 완료하여 Blob Storage를 준비합니다.

1. **원본** 이라는 컨테이너를 만듭니다. [Azure Storage Explorer](https://storageexplorer.com/)와 같은 다양한 도구를 사용하여 이 작업을 수행할 수 있습니다.

2. **대상** 이라는 컨테이너를 만듭니다.

## <a name="create-a-data-factory"></a>데이터 팩터리 만들기

1. 왼쪽 창에서 **리소스 만들기** 를 선택합니다. **통합** > **데이터 팩터리** 를 선택합니다.

   ![데이터 팩터리 선택](./media/doc-common-process/new-azure-data-factory-menu.png)

2. **새 데이터 팩터리** 페이지의 **이름** 아래에서 **ADFTutorialDataFactory** 를 입력합니다.

   데이터 팩터리 이름은 전역적으로 고유해야 합니다. 다음 오류 메시지가 표시될 수 있습니다.

    :::image type="content" source="./media/doc-common-process/name-not-available-error.png" alt-text="중복된 이름에 대한 새 데이터 팩터리 오류 메시지.":::

   이름 값에 대한 오류 메시지가 표시되면 데이터 팩터리에 대한 다른 이름을 입력합니다. 예를 들어 _**yourname**_**ADFTutorialDataFactory** 를 사용합니다. 데이터 팩터리 아티팩트에 대한 명명 규칙은 [데이터 팩터리 명명 규칙](naming-rules.md)을 참조하세요.
3. **구독** 에서 새 데이터 팩터리를 만들 Azure 구독을 선택합니다.
4. **리소스 그룹** 에서 다음 단계 중 하나를 수행합니다.

    * **기존 항목 사용** 을 선택한 다음 목록에서 기존 리소스 그룹을 선택합니다.

    * **새로 만들기** 를 선택한 다음 리소스 그룹 이름을 입력합니다.
         
    리소스 그룹에 대한 자세한 내용은 [리소스 그룹을 사용하여 Azure 리소스 관리](../azure-resource-manager/management/overview.md)를 참조하세요.

5. **버전** 에서 **V2** 를 선택합니다.
6. **위치** 아래에서 데이터 팩터리에 대한 위치를 선택합니다. 목록에는 지원되는 위치만 표시됩니다. 데이터 팩터리에서 사용하는 데이터 스토리지(예: Azure Storage, Azure SQL Database) 및 계산(예: Azure HDInsight)은 다른 위치와 지역에 있을 수 있습니다.
8. **만들기** 를 선택합니다.
9. 데이터 팩터리를 만든 후에는 데이터 팩터리 홈페이지가 나타납니다.
10. 별도의 탭에서 Azure Data Factory 사용자 인터페이스(UI)를 열려면 **Azure Data Factory Studio 열기** 타일에서 **열기** 를 선택합니다.

    :::image type="content" source="./media/doc-common-process/data-factory-home-page.png" alt-text="Azure Data Factory Studio 열기 타일이 있는 Azure Data Factory 홈페이지":::

## <a name="use-the-copy-data-tool-to-create-a-pipeline"></a>데이터 복사 도구를 사용하여 파이프라인 만들기

1. Azure Data Factory 홈페이지에서 **수집** 타일을 선택하여 데이터 복사 도구를 엽니다.

   ![ADF 홈페이지를 보여 주는 스크린샷](./media/doc-common-process/get-started-page.png)

2. **속성** 페이지에서 다음 단계를 수행합니다.

    1. **작업 유형** 에서 **기본 제공 복사 작업** 을 선택합니다.

    1. **작업 주기 또는 작업 일정** 에서 **연속 창** 을 선택합니다.

    1. **되풀이** 에서 **15분** 을 입력합니다.

    1. **다음** 을 선택합니다.

    ![데이터 복사 속성 페이지](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/copy-data-tool-properties-page.png)

3. **원본 데이터 저장소** 페이지에서 다음 단계를 완료합니다.

    1. **+ 새 연결 만들기** 를 선택하여 연결을 추가합니다.

    1. 갤러리에서 **Azure Blob Storage** 를 선택한 다음 **계속** 을 선택합니다.

        ![Azure Blob Storage 선택](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/source-data-store-page-select-blob.png)

    1. **새 연결(Azure Blob Storage)** 페이지의 **Azure 구독** 목록에서 자신의 Azure 구독을 선택하고 **스토리지 계정 이름** 목록에서 자신의 스토리지 계정을 선택합니다. 연결을 테스트한 다음 **만들기** 를 선택합니다.

    1. **연결** 블록에서 새로 만든 연결을 선택합니다.

    1. **파일 또는 폴더** 섹션에서 **찾아보기** 를 선택한 후 **원본** 폴더와 **확인** 을 차례로 선택합니다.

    1. **파일 로드 동작** 에서 **증분 로드: LastModifiedDate** 와 **이진 복사** 를 차례로 선택합니다.
    
    1. **다음** 을 선택합니다.

    :::image type="content" source="./media/tutorial-incremental-copy-lastmodified-copy-data-tool/source-data-store-page.png" alt-text="‘원본 데이터 저장소’ 페이지를 보여 주는 스크린샷.":::

4. **대상 데이터 저장소** 페이지에서 다음 단계를 완료합니다.
    1. 만든 **AzureBlobStorage** 연결을 선택합니다. 원본 데이터 저장소와 동일한 스토리지 계정입니다.

    1. **대상 경로** 섹션에서 **대상** 폴더를 찾아 선택한 다음 **확인** 을 선택합니다.

    1. **다음** 을 선택합니다.

    :::image type="content" source="./media/tutorial-incremental-copy-lastmodified-copy-data-tool/destination-data-store-page.png" alt-text="‘대상 데이터 저장소’ 페이지를 보여 주는 스크린샷.":::

5. **설정** 페이지의 **작업 이름** 에서 **DeltaCopyFromBlobPipeline** 을 입력하고 **다음** 을 선택합니다. Data Factory에서 지정한 작업 이름이 있는 파이프라인을 만듭니다.

    :::image type="content" source="./media/tutorial-incremental-copy-lastmodified-copy-data-tool/settings-page.png" alt-text="설정 페이지를 보여 주는 스크린샷.":::

6. **요약** 페이지에서 설정을 검토하고 **다음** 을 선택합니다.

    ![요약 페이지](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/summary-page.png)

7. **배포** 페이지에서 **모니터** 를 선택하여 파이프라인(작업)을 모니터링합니다.

    ![배포 페이지](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/deployment-page.png)

8. 왼쪽의 **모니터** 탭이 자동으로 선택됩니다. 애플리케이션이 **모니터** 탭으로 전환되며 파이프라인의 상태가 표시됩니다. **새로 고침** 을 선택하여 목록을 새로 고칩니다. **파이프라인 이름** 에서 링크를 선택하여 활동 실행 세부 정보를 보거나 파이프라인을 다시 실행합니다.

    ![목록 새로 고침 및 작업 실행 세부 정보 보기](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs-1.png)

9. 파이프라인에는 하나의 작업(복사 작업)만 있으므로 하나의 항목만 표시됩니다. 복사 작업에 대한 자세한 내용을 보려면 **활동 실행** 페이지에서 **활동 이름** 열에 있는 **세부 정보** 링크(안경 아이콘)를 선택합니다. 속성에 대한 자세한 내용은 [복사 작업 개요](copy-activity-overview.md)를 참조하세요.

    ![파이프라인의 복사 작업](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs2.png)

    Blob Storage 계정의 원본 컨테이너에 파일이 없기 때문에 계정에서 대상 컨테이너로 복사된 파일이 표시되지 않습니다.

    ![원본 컨테이너 또는 대상 컨테이너에 파일이 없음](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs3.png)

10. 빈 텍스트 파일을 만들고 이름을 **file1.txt** 로 합니다. 스토리지 계정의 원본 컨테이너에 이 텍스트 파일을 업로드합니다. [Azure Storage Explorer](https://storageexplorer.com/)와 같은 다양한 도구를 사용하여 해당 작업을 수행할 수 있습니다.

    ![file1.txt를 만들고 원본 컨테이너에 업로드합니다.](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs3-1.png)

11. **파이프라인 실행** 보기로 돌아가려면 **활동 실행** 페이지의 이동 경로 메뉴에서 **모든 파이프라인 실행** 링크를 선택하고 동일한 파이프라인이 자동으로 다시 트리거될 때까지 기다립니다.  

12. 두 번째 파이프라인 실행이 완료되면 앞에서 설명한 것과 동일한 단계를 수행하여 작업 실행 세부 정보를 검토합니다.  

    원본 컨테이너에서 Blob Storage 계정의 대상 컨테이너로 하나의 파일(file1.txt)이 복사된 것을 볼 수 있습니다.

    ![원본 컨테이너에서 대상 컨테이너로 file1.txt가 복사됨](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs6.png)

13. 다른 빈 텍스트 파일을 만들고 이름을 **file2.txt** 로 합니다. Blob Storage 계정의 원본 컨테이너에 이 텍스트 파일을 업로드합니다.

14. 두 번째 텍스트 파일에서 11단계와 12단계를 반복합니다. 이 파이프라인을 실행하는 동안 새 파일(file2.txt)만 원본 컨테이너에서 스토리지 계정의 대상 컨테이너로 복사된 것을 볼 수 있습니다.  

    [Azure Storage Explorer](https://storageexplorer.com/)를 사용해 파일을 검색하여 파일을 한 개만 복사했는지 확인할 수도 있습니다.

    ![Azure Storage Explorer를 사용하여 파일 검사](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs8.png)

## <a name="next-steps"></a>다음 단계
다음 자습서로 진행하여 Azure에서 Apache Spark 클러스터를 사용하여 데이터를 변환하는 방법을 알아보세요.

> [!div class="nextstepaction"]
>[Apache Spark 클러스터를 사용하여 클라우드의 데이터 변환](tutorial-transform-data-spark-portal.md)
