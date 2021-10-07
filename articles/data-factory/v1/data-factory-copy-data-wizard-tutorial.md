---
title: '자습서: 복사 마법사를 사용하여 파이프라인 만들기 '
description: 이 자습서에서는 데이터 팩터리가 지원하는 복사 마법사를 사용하여 복사 작업이 있는 Azure Data Factory 파이프라인을 만듭니다.
author: linda33wj
ms.service: data-factory
ms.subservice: v1
ms.topic: tutorial
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 91951af14a24c29bc6d3247f333f73f3e225ba57
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128653479"
---
# <a name="tutorial-create-a-pipeline-with-copy-activity-using-data-factory-copy-wizard"></a>자습서: Data Factory 복사 마법사를 사용하여 복사 작업이 있는 파이프라인 만들기
> [!div class="op_single_selector"]
> * [개요 및 필수 구성 요소](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [복사 마법사](data-factory-copy-data-wizard-tutorial.md)
> * [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)
> * [PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)
> * [Azure Resource Manager 템플릿](data-factory-copy-activity-tutorial-using-azure-resource-manager-template.md)
> * [REST API](data-factory-copy-activity-tutorial-using-rest-api.md)
> * [.NET API](data-factory-copy-activity-tutorial-using-dotnet-api.md)

> [!NOTE]
> 이 아티클은 Data Factory 버전 1에 적용됩니다. 현재 버전의 Data Factory 서비스를 사용 중인 경우, [복사 작업 자습서](../quickstart-create-data-factory-dot-net.md)를 참조하세요. 


이 자습서에서는 **복사 마법사** 를 사용하여 Azure Blob 스토리지에서 Azure SQL Database로 데이터를 복사하는 방법을 보여줍니다. 

Azure Data Factory **복사 마법사** 를 사용하면 지원되는 원본 데이터 저장소에서 지원되는 대상 데이터 저장소로 데이터를 복사하는 데이터 파이프라인을 빠르게 만들 수 있습니다. 따라서 데이터 이동 시나리오에 대한 샘플 파이프라인을 만드는 첫 번째 단계로 마법사를 사용하는 것이 좋습니다. 원본 및 대상으로 지원되는 데이터 저장소의 목록은 [지원되는 데이터 저장소](data-factory-data-movement-activities.md#supported-data-stores-and-formats)를 참조하세요.  

이 자습서는 Azure Data Factory를 만들고, 복사 마법사를 실행하고, 데이터 수집/이동 시나리오에 대한 세부 정보를 제공하는 일련의 단계를 수행하는 방법을 보여줍니다. 마법사의 단계를 마치면, Azure Blob 스토리지에서 Azure SQL Database로 데이터를 복사하는 복사 작업이 있는 파이프라인이 마법사에서 자동으로 생성됩니다. 복사 활동에 대한 자세한 내용은 [데이터 이동 활동](data-factory-data-movement-activities.md)을 참조하세요.

## <a name="prerequisites"></a>필수 구성 요소
이 자습서를 수행하기 전에 [자습서 개요](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) 문서에 나열된 필수 구성 요소를 완료합니다.

## <a name="create-data-factory"></a>데이터 팩터리 만들기
이 단계에서는 Azure 포털을 사용하여 **ADFTutorialDataFactory** 라는 Azure Data Factory를 만듭니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. 왼쪽 위 모서리에서 **리소스 만들기** 를 클릭하고 **데이터 + 분석**, **Data Factory** 를 차례로 클릭합니다. 
   
   :::image type="content" source="./media/data-factory-copy-data-wizard-tutorial/new-data-factory-menu.png" alt-text="새로 만들기->DataFactory":::
2. **새 데이터 팩터리** 블레이드에서 다음을 수행합니다.
   
   1. **ADFTutorialDataFactory** 를 **이름** 으로 입력합니다.
       Azure Data Factory 이름은 전역적으로 고유해야 합니다. `Data factory name "ADFTutorialDataFactory" is not available` 오류가 표시되면 데이터 팩터리 이름을 변경하고(예: yournameADFTutorialDataFactoryYYYYMMDD) 다시 만듭니다. 데이터 팩터리 아티팩트에 대한 명명 규칙은 [데이터 팩터리 - 명명 규칙](data-factory-naming-rules.md) 항목을 참조하세요.  
      
       :::image type="content" source="./media/data-factory-copy-data-wizard-tutorial/getstarted-data-factory-not-available.png" alt-text="데이터 팩터리 이름을 사용할 수 없음":::    
   2. Azure **구독** 을 선택합니다.
   3. 리소스 그룹에 대해 다음 단계 중 하나를 수행합니다. 
      
      - **기존 항목 사용** 을 선택하고 기존 리소스 그룹을 선택합니다.
      - **새로 만들기** 를 선택하고 리소스 그룹의 이름을 입력합니다.
          
        이 자습서의 단계 중 일부에서는 리소스 그룹에 대해 **ADFTutorialResourceGroup** 이라는 이름을 사용한다고 가정합니다. 리소스 그룹에 대한 자세한 내용은 [리소스 그룹을 사용하여 Azure 리소스 관리](../../azure-resource-manager/management/overview.md)를 참조하세요.
   4. Data Factory의 **위치** 를 선택합니다.
   5. 블레이드 하단에서 **대시보드에 고정** 확인란을 선택합니다.  
   6. **만들기** 를 클릭합니다.
      
       :::image type="content" source="media/data-factory-copy-data-wizard-tutorial/new-data-factory-blade.png" alt-text="새 데이터 팩터리 블레이드":::            
3. 만들기가 완료되면 다음 이미지와 같이 **Data Factory** 블레이드가 표시됩니다.
   
   :::image type="content" source="./media/data-factory-copy-data-wizard-tutorial/getstarted-data-factory-home-page.png" alt-text="데이터 팩터리 홈페이지":::

## <a name="launch-copy-wizard"></a>복사 마법사 시작
1. Data Factory 블레이드에서 **데이터 복사** 를 클릭하여 **복사 마법사** 를 시작합니다. 
   
   > [!NOTE]
   > 웹 브라우저가 "권한 부여 중..." 상태에서 갇혀 있음이 확인되면, 브라우저 설정에서 **타사 쿠키 및 사이트 데이터 차단** 설정을 사용 안 함/선택 취소로 지정하거나 계속 사용하도록 유지하고 **login.microsoftonline.com** 에 대한 예외를 만든 다음, 마법사를 다시 시작해 봅니다.
2. **속성** 페이지에서 다음을 수행합니다.
   
   1. **태스크 이름** 에 **CopyFromBlobToAzureSql** 을 입력합니다.
   2. **설명** 을 입력합니다(선택 사항).
   3. 종료 날짜가 오늘로 설정되고 시작 날짜가 5일 전으로 설정되도록 **시작 날짜 시간** 및 **종료 날짜 시간** 을 변경합니다.  
   4. **다음** 을 클릭합니다.  
      
      :::image type="content" source="./media/data-factory-copy-data-wizard-tutorial/copy-tool-properties-page.png" alt-text="복사 도구 - 속성 페이지"::: 
3. **원본 데이터 저장소** 페이지에서 **Azure Blob Storage** 타일을 클릭합니다. 이 페이지를 사용하여 복사 작업에 사용할 원본 데이터 저장소를 지정합니다. 
   
    :::image type="content" source="./media/data-factory-copy-data-wizard-tutorial/copy-tool-source-data-store-page.png" alt-text="복사 도구 - 원본 데이터 저장소 페이지":::
4. **Azure Blob Storage 계정 지정** 페이지에서 다음을 수행합니다.
   
   1. **연결된 서비스 이름** 에 **AzureStorageLinkedService** 를 입력합니다.
   2. **계정 선택 방법** 에 **Azure 구독에서** 옵션이 선택되었는지 확인합니다.
   3. Azure **구독** 을 선택합니다.  
   4. 선택한 구독에서 사용할 수 있는 Azure Storage 계정 목록에서 **Azure Storage 계정** 을 선택합니다. **계정 선택 방법** 으로 **수동으로 입력** 옵션을 선택하여 스토리지 계정 설정을 수동으로 입력할 수도 있습니다. 그리고 **다음** 을 클릭합니다. 
      
      :::image type="content" source="./media/data-factory-copy-data-wizard-tutorial/copy-tool-specify-azure-blob-storage-account.png" alt-text="복사 도구 - Azure Blob Storage 계정 지정 페이지":::
5. **입력 파일 또는 폴더 선택** 페이지에서 다음을 수행합니다.
   
   1. **adftutorial**(폴더)을 두 번 클릭합니다.
   2. **emp.txt** 를 선택하고 **선택** 을 클릭합니다.
      
      :::image type="content" source="./media/data-factory-copy-data-wizard-tutorial/copy-tool-choose-input-file-or-folder.png" alt-text="스크린샷은 입력 파일에 대한 선택 옵션을 보여줍니다.":::
6. **입력 파일 또는 폴더 선택** 페이지에서 **다음** 을 클릭합니다. **이진 복사** 를 선택하지 않습니다. 
   
    :::image type="content" source="./media/data-factory-copy-data-wizard-tutorial/chose-input-file-folder.png" alt-text="스크린샷은 입력에 대한 이진 복사 옵션을 보여줍니다."::: 
7. **파일 형식 설정** 페이지에 파일을 구문 분석하여 마법사에 의해 자동으로 감지되는 구분 기호와 스키마가 표시됩니다. 복사 마법사의 자동 감지를 중지하거나 재정하기 위해 구분 기호를 수동으로 입력할 수도 있습니다. 구분 기호를 검토하고 데이터를 미리 본 후에 **다음** 을 클릭합니다. 
   
    :::image type="content" source="./media/data-factory-copy-data-wizard-tutorial/copy-tool-file-format-settings.png" alt-text="복사 도구 - 파일 형식 설정":::  
8. 대상 데이터 저장소 페이지에서 **Azure SQL Database** 를 선택하고 **다음** 을 클릭합니다.
   
    :::image type="content" source="./media/data-factory-copy-data-wizard-tutorial/choose-destination-store.png" alt-text="복사 도구 - 대상 저장소 선택":::
9. **Azure SQL 데이터베이스 지정** 페이지에서 다음을 수행합니다.
   
   1. **연결 이름** 필드에 **AzureSqlLinkedService** 를 입력합니다.
   2. **서버/데이터베이스 선택 방법** 에 **Azure 구독에서** 옵션이 선택되었는지 확인합니다.
   3. Azure **구독** 을 선택합니다.  
   4. **서버 이름** 및 **데이터베이스** 를 선택합니다.
   5. **사용자 이름** 및 **암호** 를 입력합니다.
   6. **다음** 을 클릭합니다.  
      
      :::image type="content" source="./media/data-factory-copy-data-wizard-tutorial/specify-azure-sql-database.png" alt-text="복사 도구 - Azure SQL Database 지정":::
10. **테이블 매핑** 페이지에 있는 드롭다운 목록의 **대상** 필드에서 **emp** 를 선택하고 **아래쪽 화살표** 를 클릭하여(선택 사항) 스키마를 확인하고 데이터를 미리 봅니다.
    
     :::image type="content" source="./media/data-factory-copy-data-wizard-tutorial/copy-tool-table-mapping-page.png" alt-text="복사 도구 - 테이블 매핑"::: 
11. **스키마 매핑** 페이지에서 **다음** 을 클릭합니다.
    
    :::image type="content" source="./media/data-factory-copy-data-wizard-tutorial/schema-mapping-page.png" alt-text="복사 도구 - 스키마 매핑":::
12. **성능 설정** 페이지에서 **다음** 을 클릭합니다. 
    
    :::image type="content" source="./media/data-factory-copy-data-wizard-tutorial/performance-settings.png" alt-text="스크린샷은 다음을 선택할 수 있는 성능 설정 페이지를 보여줍니다.":::
13. **요약** 페이지에서 정보를 검토하고 **마침** 을 클릭합니다. 마법사는 데이터 팩터리(복사 마법사를 실행한 위치)에 두 개의 연결된 서비스, 두 개의 데이터 세트(입력 및 출력), 하나의 파이프라인을 만듭니다. 
    
    :::image type="content" source="./media/data-factory-copy-data-wizard-tutorial/summary-page.png" alt-text="스크린샷은 다음을 선택할 수 있는 요약 페이지를 보여줍니다.":::

## <a name="launch-monitor-and-manage-application"></a>애플리케이션 모니터링 및 관리 시작
1. **배포** 페이지에서 `Click here to monitor copy pipeline` 링크를 클릭합니다.
   
   :::image type="content" source="./media/data-factory-copy-data-wizard-tutorial/copy-tool-deployment-succeeded.png" alt-text="복사 도구 - 배포 성공 페이지":::  
2. 모니터링 애플리케이션이 웹 브라우저의 별도 탭에서 시작됩니다.   
   
   :::image type="content" source="./media/data-factory-copy-data-wizard-tutorial/monitoring-app.png" alt-text="모니터링 앱":::   
3. 매시간 조각의 최신 상태를 보려면 아래쪽의 **활동 창** 목록에서 **새로 고침** 단추를 클릭합니다. 파이프라인의 시작 시간과 종료 시간 사이의 5일 동안 5개의 활동 창이 표시됩니다. 목록을 자동으로 새로 고치지 않으므로 [준비] 상태의 모든 활동 창을 표시하려면 [새로 고침] 단추를 몇 번 클릭해야 할 수도 있습니다. 
4. 목록에서 활동 창을 선택합니다. **활동 창 탐색기** 에서 해당 활동 창에 대한 세부 정보를 확인합니다.

    :::image type="content" source="media/data-factory-copy-data-wizard-tutorial/activity-window-details.png" alt-text="활동 창 세부 정보":::    

    11, 12, 13, 14 및 15에 해당하는 날짜가 녹색으로 표시되어 있습니다. 즉 이러한 날짜에 대해 이미 출력 조각이 매일 생성되었음을 의미합니다. 또한 다이어그램 보기에서도 파이프라인과 출력 데이터 세트에 대한 이러한 색 구분이 표시됩니다. 이전 단계에서 색 구분을 기반으로 하여 두 조각이 이미 생성되었고, 하나의 조각이 현재 처리 중이며, 다른 두 조각이 처리 대기 중임을 알 수 있습니다. 

    이 애플리케이션을 사용하는 방법에 대한 자세한 내용은 [모니터링 앱을 사용하여 파이프라인 모니터링 및 관리](data-factory-monitor-manage-app.md) 문서를 참조하세요.

## <a name="next-steps"></a>다음 단계
이 자습서에서는 Azure Blob 스토리지를 원본 데이터 저장소로 사용하고 Azure SQL Database를 복사 작업의 대상 데이터 저장소로 사용했습니다. 다음 표에서는 복사 활동에서 원본 및 싱크로 지원되는 데이터 저장소의 목록을 제공합니다. 

[!INCLUDE [data-factory-supported-data-stores](includes/data-factory-supported-data-stores.md)]

데이터 저장소에 대한 복사 마법사에 표시되는 필드/속성에 대한 자세한 내용을 보려면 표에 나와 있는 해당 데이터 저장소에 대한 링크를 클릭합니다. 