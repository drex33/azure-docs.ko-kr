---
title: 온-프레미스 SSIS(SQL Server Integration Services) 작업을 Azure Data Factory로 마이그레이션
description: 이 문서에서는 SQL Server Management Studio를 사용하여 SSIS(SQL Server Integration Services) 작업을 Azure Data Factory 파이프라인/작업/트리거로 마이그레이션하는 방법에 대해 설명합니다.
author: chugugrace
ms.author: chugu
ms.service: data-factory
ms.subservice: integration-services
ms.topic: conceptual
ms.date: 4/7/2020
ms.openlocfilehash: 5a9e69b0672a5b4235effcd68b50eeddc5ec9f82
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122528669"
---
# <a name="migrate-sql-server-agent-jobs-to-adf-with-ssms"></a>SSMS를 사용하여 ADF로 SQL Server 에이전트 작업 마이그레이션

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

[온-프레미스 SSIS(SQL Server Integration Services) 워크로드를 ADF에서 SSIS로 마이그레이션할 때](scenario-ssis-migration-overview.md) SSIS 패키지를 마이그레이션한 후에 SQL Server Integration Services 패키지의 작업 단계 유형을 사용하여 SSMS(SQL Server Management Studio) **SSIS 작업 마이그레이션 마법사** 를 통해 SQL Server 에이전트 작업을 ADF(Azure Data Factory) 파이프라인/작업/일정 트리거로 일괄 마이그레이션할 수 있습니다.

일반적으로 적용 가능한 작업 단계 유형을 사용하여 선택한 SQL 에이전트 작업에 대해 **SSIS 작업 마이그레이션 마법사** 는 다음을 수행할 수 있습니다.

- 온-프레미스 SSIS 패키지 위치를 패키지를 마이그레이션할 위치에 매핑합니다. 이 위치는 ADF의 SSIS에서 액세스할 수 있습니다.
    > [!NOTE]
    > 파일 시스템의 패키지 위치만 지원됩니다.
- 적용되는 작업 단계를 사용하여 적용되는 작업을 아래와 같은 해당 ADF 리소스로 마이그레이션합니다.

|SQL 에이전트 작업 개체  |ADF 리소스  |참고|
|---------|---------|---------|
|SQL 에이전트 작업|pipeline     |*\<job name>에 대해 생성* 되는 파이프라인의 이름입니다. <br> <br> 기본 제공 에이전트 작업은 적용되지 않습니다. <li> SSIS 서버 유지 관리 작업 <li> syspolicy_purge_history <li> collection_set_* <li> mdw_purge_data_* <li> sysutility_*|
|SSIS 작업 단계|SSIS 패키지 작업 실행|<li> 작업의 이름은 \<step name>입니다. <li> 작업 단계에서 사용되는 프록시 계정이 이 작업의 Windows 인증으로 마이그레이션됩니다. <li> 작업 단계에서 정의한 *32비트 런타임 사용* 을 제외한 *실행 옵션* 은 마이그레이션에서 무시됩니다. <li> 작업 단계에서 정의된 *확인* 은 마이그레이션에서 무시됩니다.|
|schedule      |일정 트리거        |*\<schedule name>에 대해 생성* 되는 일정 트리거의 이름입니다. <br> <br> SQL 에이전트 작업 일정의 다음 옵션은 마이그레이션에서 무시됩니다. <li> 두 번째 수준 간격 <li> *SQL Server 에이전트가 시작될 때 자동으로 시작* <li> *CPU가 유휴 상태로 될 때마다 시작* <li> *평일* 및 *주말* &gt;표준 시간대&lt; <br> SQL 에이전트 작업 일정이 ADF 일정 트리거로 마이그레이션된 후의 차이점은 다음과 같습니다. <li> ADF 일정 트리거 후속 실행은 실행된 선행 트리거 실행의 실행 상태와는 독립적입니다. <li> ADF 일정 트리거 반복 구성은 SQL 에이전트 작업의 일별 빈도와 다릅니다.|

- 로컬 출력 폴더에 ARM(Azure Resource Manager) 템플릿을 생성하고 직접 또는 나중에 수동으로 데이터 팩터리에 배포합니다. ADF 리소스 관리자 템플릿에 대한 자세한 내용은 [Microsoft.DataFactory 리소스 유형](/azure/templates/microsoft.datafactory/allversions)을 참조하세요.

## <a name="prerequisites"></a>필수 조건

이 문서에 설명된 기능을 사용하려면 SQL Server Management Studio 버전 18.5 및 그 이상이 필요합니다. SSMS의 최신 버전을 다운로드하려면 [Download SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms)(SSMS(SQL Server Management Studio) 다운로드)를 참조하세요.

## <a name="migrate-ssis-jobs-to-adf"></a>SSIS 작업을 ADF로 마이그레이션

1. SSMS의 개체 탐색기에서 SQL Server 에이전트를 선택하고 작업을 선택한 다음 마우스 오른쪽 단추를 클릭하고 **ADF로 SSIS 작업 마이그레이션** 을 선택합니다.
![작업을 선택하고 SSIS 작업을 ADF로 마이그레이션할 수 있는 SQL Server Management Studio 개체 탐색기를 보여 주는 스크린샷](media/how-to-migrate-ssis-job-ssms/menu.png)

1. Azure에 로그인하고 Azure 구독, Data Factory, Integration Runtime을 선택합니다. 마이그레이션할 SSIS 작업에 SSIS 파일 시스템 패키지가 있는 경우 패키지 위치 매핑 단계에서 사용되는 Azure Storage는 선택 사항입니다.
![메뉴](media/how-to-migrate-ssis-job-ssms/step1.png)

1. SSIS 패키지의 경로와 SSIS 작업의 구성 파일을 마이그레이션된 파이프라인이 액세스할 수 있는 대상 경로에 매핑합니다. 이 매핑 단계에서는 다음을 수행할 수 있습니다.

    1. 원본 폴더를 선택하고 **매핑 추가** 를 선택합니다.
    1. 원본 폴더 경로를 업데이트합니다. 유효한 경로는 패키지의 폴더 경로 또는 부모 폴더 경로입니다.
    1. 대상 폴더 경로를 업데이트합니다. 기본값은 1단계에서 선택된 기본 스토리지 계정의 상대 경로입니다.
    1. **매핑 삭제** 를 통해 선택한 매핑을 삭제합니다.
![매핑을 추가할 수 있는 Map SSIS 패키지 및 구성 경로 페이지를 보여 주는 스크린샷](media/how-to-migrate-ssis-job-ssms/step2.png)
 ![원본 및 대상 폴더 경로를 업데이트할 수 있는 Map SSIS 패키지 및 구성 경로 페이지를 보여 주는 스크린샷](media/how-to-migrate-ssis-job-ssms/step2-1.png)

1. 마이그레이션이 적용될 작업을 선택하고 해당하는 *실행된 SSIS 패키지 작업* 의 설정을 구성합니다.

    - *기본 설정* 은 기본적으로 선택한 모든 단계에 적용됩니다. 각 속성에 대한 자세한 내용은 패키지 위치가 *파일 시스템(패키지)* 일 때 [SSIS 패키지 작업 실행](how-to-invoke-ssis-package-ssis-activity.md)의 *설정 탭* 을 참조하세요.
    ![해당하는 실행된 SSIS 패키지 작업의 설정을 구성할 수 있는 선택된 SSIS 작업 페이지를 보여 주는 스크린샷](media/how-to-migrate-ssis-job-ssms/step3-1.png)
    - *단계 설정* 은 선택한 단계에 대한 설정을 구성합니다.
        
        **기본 설정 적용**: 기본값이 선택되어 있습니다. 선택한 단계에 대해서만 설정을 구성하려면 선택을 해제합니다.  
        기타 속성에 대한 자세한 내용은 패키지 위치가 *파일 시스템(패키지)* 일 때 [SSIS 패키지 작업 실행](how-to-invoke-ssis-package-ssis-activity.md)에 대한 *설정 탭* 을 참조하세요.
    ![기본 설정을 적용할 수 있는 SSIS 작업 선택 페이지를 보여 주는 스크린샷](media/how-to-migrate-ssis-job-ssms/step3-2.png)

1. ARM 템플릿을 생성하고 배포합니다.
    1. 마이그레이션된 ADF 파이프라인의 ARM 템플릿의 출력 경로를 선택하거나 입력합니다. 폴더가 존재하지 않는 경우 자동으로 만들어집니다.
    2. **데이터 팩터리에 ARM 템플릿 배포** 옵션을 선택합니다.
        - 기본값은 선택되지 않습니다. 생성된 ARM 템플릿을 나중에 수동으로 배포할 수 있습니다.
        - 생성된 ARM 템플릿을 데이터 팩터리에 직접 배포하려면 선택합니다.
    ![마이그레이션 구성 페이지를 보여 주는 스크린샷으로, 여기서 마이그레이션된 ADF 파이프라인의 ARM 템플릿에 대한 출력 경로를 선택하거나 입력하고, 데이터 팩터리에 ARM 템플릿 배포 옵션을 선택할 수 있습니다.](media/how-to-migrate-ssis-job-ssms/step4.png)

1. 마이그레이션 후 결과를 확인합니다.
![마이그레이션 진행 상황을 표시하는 마이그레이션 결과 페이지를 보여 주는 스크린샷](media/how-to-migrate-ssis-job-ssms/step5.png)

## <a name="next-steps"></a>다음 단계

[파이프라인 실행 및 모니터링](how-to-invoke-ssis-package-ssis-activity.md)
