---
title: Azure Portal 통해 Azure-SSIS 통합 런타임 만들기
description: Azure에서 SSIS 패키지를 배포하고 실행할 수 있도록 Azure Portal 통해 Azure Data Factory Azure-SSIS 통합 런타임을 만드는 방법을 알아봅니다.
ms.service: data-factory
ms.subservice: integration-services
ms.topic: conceptual
ms.date: 10/22/2021
author: swinarko
ms.author: sawinark
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 13c5a0d77e82e699c24f13fce3f27d0ddea7fc9e
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/05/2021
ms.locfileid: "131851176"
---
# <a name="create-an-azure-ssis-integration-runtime-via-azure-portal"></a>Azure Portal 통해 Azure-SSIS 통합 런타임 만들기 

이 문서에서는 Azure Portal/ADF UI를 통해 ADF(Azure Data Factory)에서 SSIS(Azure-SQL Server Integration Services) IR(통합 런타임)을 만드는 방법을 보여줍니다.

## <a name="create-a-data-factory"></a>데이터 팩터리 만들기

Azure Portal을 통해 데이터 팩터리를 만들려면 [UI를 통해 데이터 팩터리 만들기](./quickstart-create-data-factory-portal.md#create-a-data-factory)의 단계별 지침을 따릅니다. 만든 후에 빠르게 액세스할 수 있도록 하려면 이 작업을 수행하면서 **대시 보드에 고정** 을 선택합니다. 

데이터 팩터리가 만들어지면 Azure Portal에서 개요 페이지를 엽니다. **작성자 및 모니터** 타일을 선택하여 별도의 탭에서 **시작하기** 페이지를 엽니다. 여기서 Azure-SSIS IR을 계속 만들 수 있습니다.   

## <a name="provision-an-azure-ssis-integration-runtime"></a>Azure-SSIS 통합 런타임 프로비전

홈페이지에서 **SSIS 구성** 타일을 선택하여 **통합 런타임 설정** 창을 엽니다.

   :::image type="content" source="./media/doc-common-process/get-started-page.png" alt-text="ADF 홈페이지를 보여 주는 스크린샷":::

   **통합 런타임 설치** 창에는 일반, 배포 및 고급 설정을 연속으로 구성하는 세 개의 페이지가 있습니다.

### <a name="general-settings-page"></a>일반 설정 페이지

**통합 런타임 설치** 창의 **일반 설정** 페이지에서 다음 단계를 완료합니다.

:::image type="content" source="./media/tutorial-create-azure-ssis-runtime-portal/general-settings.png" alt-text="일반 설정":::

1. **이름** 에는 통합 런타임의 이름을 입력합니다.

2. **설명** 에는 통합 런타임에 대한 설명을 입력합니다.

3. **위치** 에는 통합 런타임의 위치를 선택합니다. 지원되는 위치만 표시됩니다. SSISDB를 호스트하는 데이터베이스의 서버와 동일한 위치를 선택하는 것이 좋습니다.

4. **노드 크기** 는 통합 런타임 클러스터의 노드 크기를 선택합니다. 지원되는 노드 크기만 표시됩니다. 여러 개의 계산 집약적 또는 메모리 집약적 패키지를 실행하려면 큰 노드 크기(강화)를 선택합니다.

   > [!NOTE]
   > [컴퓨팅 격리](../azure-government/azure-secure-isolation-guidance.md#compute-isolation)가 필요한 경우 **Standard_E64i_v3** 노드 크기를 선택하세요. 이 노드 크기는 전체 실제 호스트를 사용하고 미 국방부의 Impact Level 5(IL5) 워크로드 같은 특정 워크로드에 필요한 격리 수준을 제공하는 격리된 가상 머신을 나타냅니다.
   
5. **노드 수** 로는 통합 런타임 클러스터의 노드 수를 선택합니다. 지원되는 노드 수만 표시됩니다. 여러 패키지를 동시에 실행하려면 노드 수가 많은 대형 클러스터(규모 확장)를 선택합니다.

6. **버전/라이선스** 의 경우 통합 런타임에 대한 SQL Server 버전, 즉 Standard 또는 Enterprise를 선택합니다. 통합 런타임에서 고급 기능을 사용하려면 Enterprise를 선택합니다.

7. **비용 절감** 의 경우 통합 런타임에 대한 AHB(Azure 하이브리드 혜택) 옵션에서 **예** 또는 **아니요** 를 선택합니다. Software Assurance를 통해 사용자 고유의 SQL Server 라이선스를 가져와서 하이브리드를 사용함으로써 비용을 절감하려면 **예** 를 선택합니다.

8. **계속** 을 선택합니다.

### <a name="deployment-settings-page"></a>배포 설정 페이지

**통합 런타임 설치** 창의 **배포 설정** 페이지에서 SSISDB 및 또는 Azure-SSIS IR 패키지 저장소를 만드는 옵션이 있습니다.

#### <a name="creating-ssisdb"></a>SSISDB 생성

**통합 런타임 설정** 창의 **배포 설정** 페이지에서 SSISDB(프로젝트 배포 모델)에 패키지를 배포하려면 **프로젝트/패키지/환경/실행 로그를 저장하기 위해 Azure SQL Database 서버/Managed Instance가 호스트하는 SSIS 카탈로그(SSISDB) 만들기** 확인란을 선택합니다. 또는 Azure SQL Managed Instance(패키지 배포 모델)가 호스트하는 파일 시스템, Azure Files 또는 SQL Server 데이터베이스(MSDB)에 패키지를 배포하려는 경우에는 SSISDB를 만들거나 확인란을 선택할 필요가 없습니다.

배포 모델에 관계없이 Azure SQL Managed Instance가 호스트하는 SQL Server 에이전트를 사용하여 패키지 실행을 오케스트레이션/예약하려는 경우 SSISDB에서 사용하도록 설정되어 있으므로 이 확인란을 선택합니다. 자세한 내용은 [Azure SQL Managed Instance 에이전트를 통해 SSIS 패키지 실행 예약](./how-to-invoke-ssis-package-managed-instance-agent.md)을 참조하세요.
   
확인란을 선택한 경우 다음 단계를 완료하여 사용자를 대신하여 만들고 관리할 SSISDB를 호스트하는 사용자 고유의 데이터베이스 서버를 가져옵니다.

:::image type="content" source="./media/tutorial-create-azure-ssis-runtime-portal/deployment-settings.png" alt-text="SSISDB의 배포 설정":::
   
1. **구독** 에서는 SSISDB를 호스트하는 데이터베이스 서버가 있는 Azure 구독을 선택합니다. 

1. **위치** 에서는 SSISDB를 호스트하는 데이터베이스 서버의 위치를 선택합니다. 통합 런타임과 동일한 위치를 선택하는 것이 좋습니다.

1. **카탈로그 데이터베이스 서버 엔드포인트** 로는 SSISDB를 호스트하는 데이터베이스 서버의 엔드포인트를 선택합니다. 
   
   선택한 데이터베이스 서버에 따라 사용자를 대신하여 SSISDB 인스턴스를 단일 데이터베이스, 탄력적 풀의 일부 또는 관리형 인스턴스로 만들 수 있습니다. 이는 공용 네트워크에서 액세스하거나 가상 네트워크에 조인하여 액세스할 수 있습니다. SSISDB를 호스트할 데이터베이스 서버의 유형을 선택하는 방법에 대한 지침은 [SQL Database 및 SQL Managed Instance 비교](../data-factory/create-azure-ssis-integration-runtime.md#comparison-of-sql-database-and-sql-managed-instance)를 참조하세요.   

   IP 방화벽 규칙/가상 네트워크 서비스 엔드포인트가 있는 Azure SQL Database 서버 또는 프라이빗 엔드포인트가 있는 관리형 인스턴스를 선택하여 SSISDB를 호스팅하거나 자체 호스팅 IR을 구성하지 않고 온-프레미스 데이터에 액세스해야 하는 경우 Azure-SSIS IR을 가상 네트워크에 조인해야 합니다. 자세한 내용은 [가상 네트워크에 Azure-SSIS IR 조인하기](./join-azure-ssis-integration-runtime-virtual-network.md)를 참조하세요.
      
1. **데이터 팩터리의 시스템 관리 ID로 AAD 인증 사용** 또는 **데이터 팩터리의 사용자 할당 관리 ID로 AAD 인증 사용** 확인란을 선택하여 SSISDB를 호스트하는 데이터베이스에 액세스할 Azure-SSIS IR에 대한 Azure AD 인증 방법을 선택합니다. 대신 SQL 인증 방법을 선택하려면 아무 확인란도 선택하지 마세요.

   이 확인란 중 하나를 선택하면 데이터 팩터리의 지정된 시스템/사용자 할당 관리 ID를 데이터베이스 서버에 대한 액세스 권한이 있는 Azure AD 그룹에 추가해야 합니다. **데이터 팩터리의 사용자 할당 관리 ID로 AAD 인증 사용** 확인란을 선택하면 지정된 사용자 할당 관리 ID를 사용하여 만든 기존 자격 증명을 선택하거나 새 자격 증명을 만들 수 있습니다. 자세한 내용은 [Azure-SSIS IR에 Azure AD 인증 사용](./enable-aad-authentication-azure-ssis-ir.md)을 참조하세요.

1. **관리 사용자 이름** 에 대해 SSISDB를 호스트하는 데이터베이스 서버의 SQL 인증 사용자 이름을 입력합니다. 

1. **관리자 암호** 에 대해 SSISDB를 호스트하는 데이터베이스 서버의 SQL 인증 암호를 입력합니다. 

1. BCDR(비즈니스 연속성 및 재해 복구)을 위해 Azure SQL Database/Managed Instance 장애 조치(failover) 그룹과 동기화되는 이중 대기 Azure SSIS IR 쌍을 구성하려면 **SSISDB 장애 조치(failover)에서 이중 대기 Azure-SSIS 통합 런타임 쌍 사용** 확인란을 선택합니다.
   
   확인란을 선택하는 경우 **이중 대기 쌍 이름** 텍스트 상자에 기본 및 보조 Azure-SSIS IR 쌍을 식별하는 이름을 입력합니다. 기본 및 보조 Azure-SSIS IR을 만들 때 동일한 쌍 이름을 입력해야 합니다.

   자세한 내용은 [BCDR용 Azure-SSIS IR 구성](./configure-bcdr-azure-ssis-integration-runtime.md)을 참조하세요.

1. **카탈로그 데이터베이스 서비스 계층** 에 대해 SSISDB를 호스팅할 데이터베이스 서버의 서비스 계층을 선택합니다. 기본, 표준 또는 프리미엄 계층을 선택하거나 탄력적 풀 이름을 선택합니다.

**연결 테스트** 를 선택하고(해당하는 경우) 테스트가 성공하면 **계속** 을 선택합니다.

> [!NOTE]
> Azure SQL Database 서버를 사용하여 SSISDB를 호스트하는 경우, 데이터는 기본적으로 백업용 지역 중복 저장소에 저장됩니다. 다른 지역에 데이터를 복제하지 않으려는 경우 [PowerShell을 사용하여 백업 스토리지 중복성 구성](../azure-sql/database/automated-backups-overview.md?tabs=single-database#configure-backup-storage-redundancy-by-using-powershell) 지침을 따르세요.
   
#### <a name="creating-azure-ssis-ir-package-stores"></a>Azure-SSIS IR 패키지 저장소 만들기

**통합 런타임 설정** 창의 **배포 설정** 페이지에서 Azure-SSIS IR 패키지 저장소를 통해 MSDB, 파일 시스템 또는 Azure Files(패키지 배포 모델)에 배포된 패키지를 관리하려면 **Azure SQL Managed Instance가 호스트하는 파일 시스템/Azure Files/SQL Server 데이터베이스(MSDB)에 배포되는 패키지를 관리할 패키지 저장소 만들기** 확인란을 선택합니다.
   
Azure-SSIS IR 패키지 저장소를 사용하면 [레거시 SSIS 패키지 저장소](/sql/integration-services/service/package-management-ssis-service)와 비슷하게 SSMS를 통해 패키지를 가져오고/내보내고/삭제하고/실행하고 실행 중인 패키지를 모니터링/중지할 수 있습니다. 자세한 내용은 [Azure-SSIS IR 패키지 저장소를 사용하여 SSIS 패키지 관리](./azure-ssis-integration-runtime-package-store.md)를 참조하세요.
   
이 확인란을 선택하면 **새로 만들기** 를 선택하여 Azure-SSIS IR에 여러 패키지 저장소를 추가할 수 있습니다. 반대로, 패키지 저장소 하나를 여러 Azure SSIS IR이 공유할 수 있습니다.

:::image type="content" source="./media/tutorial-create-azure-ssis-runtime-portal/deployment-settings2.png" alt-text="MSDB/파일 시스템/Azure Files의 배포 설정":::

**패키지 저장소 추가** 창에서 다음 단계를 완료합니다.
   
   1. **패키지 저장소 이름** 으로 패키지 저장소의 이름을 입력합니다. 

   1. **패키지 저장소 연결된 서비스** 로는 패키지가 배포되는 파일 시스템/Azure Files/Azure SQL Managed Instance 대한 액세스 정보를 저장하는 기존의 연결된 서비스를 선택하거나, **새로 생성** 를 선택하여 새로 생성합니다. **새 연결된 서비스** 창에서 다음 단계를 완료합니다.
   
      > [!NOTE]
      > **Azure File Storage** 또는 **파일 시스템** 연결 서비스를 사용하여 Azure Files에 액세스할 수 있습니다. **Azure File Storage** 연결 서비스를 사용하는 경우 Azure-SSIS IR 패키지 저장소는 현재 **기본**(**계정 키** 또는 **SAS URI** 아님) 인증 방법만 지원합니다.  

      :::image type="content" source="./media/tutorial-create-azure-ssis-runtime-portal/deployment-settings-linked-service.png" alt-text="연결된 서비스의 배포 설정":::

      1. **이름** 에는 연결된 서비스의 이름을 입력합니다. 
         
      1. **설명** 에는 연결된 서비스에 대한 설명을 입력합니다. 
         
      1. **형식** 으로는 **Azure File Storage**, **Azure SQL Managed Instance** 또는 **파일 시스템** 을 선택합니다.

      1. 항상 Azure-SSIS IR를 사용하여 패키지 저장소에 대한 액세스 정보를 가져오기 때문에 **통합 런타임을 통해 연결** 을 무시해도 됩니다.

      1. **Azure File Storage** 를 선택하는 경우 **인증 방법** 에 대해 **기본** 을 선택한 후, 다음 단계를 완료합니다. 

         1. **계정 선택 방법** 으로는 **Azure 구독에서 선택** 또는 **수동으로 입력** 을 선택합니다.
         
         1. **Azure 구독에서 선택** 을 선택하는 경우 관련된 **Azure 구독**, **스토리지 계정 이름** 및 **파일 공유** 를 선택합니다.
            
         1. **수동으로 입력** 을 선택하는 경우 **호스트** 로 `\\<storage account name>.file.core.windows.net\<file share name>`을 입력하고, **사용자 이름** 으로 `Azure\<storage account name>`을 입력하고, **암호** 로 `<storage account key>`를 입력하거나 비밀로 저장된 **Azure Key Vault** 를 선택합니다.

      1. **Azure SQL Managed Instance** 를 선택하는 경우 다음 단계를 완료합니다. 

         1. **연결 문자열** 또는 비밀로 저장된 **Azure Key Vault** 를 선택합니다.

         1. **연결 문자열** 을 선택하는 경우 다음 단계를 완료합니다. 
             1. **계정 선택 방법** 에 대해 **Azure 구독에서** 를 선택한 경우 관련 **Azure 구독**, **서버 이름**, **엔드포인트 유형** 및 **데이터베이스 이름** 을 선택합니다. **수동으로 입력** 을 선택한 경우 다음 단계를 완료합니다. 
                1.  **정규화된 도메인 이름** 으로 Azure SQL Managed Instance의 프라이빗 엔드포인트인 `<server name>.<dns prefix>.database.windows.net` 또는 퍼블릭 엔드포인트인 `<server name>.public.<dns prefix>.database.windows.net,3342`를 입력합니다. 프라이빗 엔드포인트를 입력하면 ADF UI가 연결할 수 없기 때문에 **연결 테스트** 를 사용할 수 없습니다.

                1. **데이터베이스 이름** 으로 `msdb`를 입력합니다.

            1. **인증 유형** 에 대해 **SQL 인증**, **관리 ID**, **서비스 주체** 또는 **사용자 할당 관리 ID** 를 선택합니다.

                - **SQL 인증** 을 선택하는 경우 관련 **사용자 이름** 및 **암호** 를 입력하거나 비밀로 저장된 **Azure Key Vault** 를 선택합니다.

                -  **관리 ID** 를 선택하는 경우 Azure SQL Managed Instance에 대한 액세스 권한을 ADF에 대한 시스템 관리 ID에 부여합니다.

                - **서비스 주체** 를 선택하는 경우 관련 **서비스 주체 ID** 및 **서비스 주체 키** 를 입력하거나 비밀로 저장된 **Azure Key Vault** 를 선택합니다.

                -  **사용자 할당 관리 ID** 를 선택하는 경우 Azure SQL Managed Instance에 대한 액세스 권한을 ADF에 대한 지정된 사용자 할당 관리 ID에 부여합니다. 그런 다음, 지정된 사용자 할당 관리 ID를 사용하여 만든 기존 자격 증명을 선택하거나 새 자격 증명을 만들 수 있습니다.

      1. **파일 시스템** 을 선택하는 경우 패키지가 배포되는 폴더의 UNC 경로를 **호스트** 로 입력하고, 관련 **사용자 이름** 및 **암호** 을 입력하거나, 비밀로 저장된 **Azure Key Vault** 를 선택합니다.

      1. **연결 테스트** 를 선택하고(해당하는 경우), 테스트가 성공하면 **만들기** 를 선택합니다.

   1. 추가된 패키지 저장소가 **배포 설정** 페이지에 표시됩니다. 저장소를 제거하려면 해당 확인란을 선택한 다음, **삭제** 를 선택합니다.

**연결 테스트** 를 선택하고(해당하는 경우) 테스트가 성공하면 **계속** 을 선택합니다.

### <a name="advanced-settings-page"></a>고급 설정 페이지

**통합 런타임 설치** 창의 **고급 설정** 페이지에서 다음 단계를 완료합니다.

   :::image type="content" source="./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings.png" alt-text="고급 설정":::

   1. **노드당 최대 병렬 실행 수** 에 대해 통합 런타임 클러스터에서 노드당 동시에 실행할 최대 패키지 수를 선택합니다. 지원되는 패키지 수만 표시됩니다. 둘 이상의 코어를 사용하여 계산 집약적이거나 메모리 집약적인 하나의 대형 패키지를 실행하려면 낮은 숫자를 선택합니다. 하나의 코어에서 하나 이상의 소형 패키지를 실행하려면 높은 숫자를 선택합니다.

   1. **Customize your Azure-SSIS Integration Runtime with additional system configurations/component installations**(추가 시스템 구성/구성 요소 설치를 통해 Azure-SSIS Integration Runtime 사용자 지정) 확인란을 선택하여 Azure-SSIS IR에 표준/빠른 사용자 지정 설치를 추가할지 여부를 선택합니다. 자세한 내용은 [Azure-SSIS IR 사용자 지정 설치](./how-to-configure-azure-ssis-ir-custom-setup.md)를 참조하세요.

      확인란을 선택하는 경우 다음 단계를 완료하세요.

      :::image type="content" source="./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-custom.png" alt-text="사용자 지정 설치가 포함된 고급 설정":::
   
      1. **사용자 지정 설치 컨테이너 SAS URI** 에는 표준 사용자 지정 설치의 스크립트 및 관련 파일을 저장하는 컨테이너의 SAS URI를 입력합니다.

      1. **기본 사용자 지정 설치** 의 경우, **새로 만들기** 를 선택하여 **기본 사용자 지정 설치** 패널을 추가하고 **기본 사용자 지정 설치 유형** 드롭다운 메뉴(예: **cmdkey 명령 실행**, **환경 변수 추가**, **사용이 허가된 구성 요소 설치** 등)에서 유형을 선택합니다.

         **사용이 허가된 구성 요소** 유형을 선택하는 경우 **구성 요소 이름** 드롭다운 메뉴에서 ISV 파트너에서 통합된 구성 요소를 선택하고, 필요한 경우 제품 라이선스 키를 입력하고 해당 제품에서 구입한 제품 라이선스 파일을 **라이선스 키**/**라이선스 파일** 상자에 업로드 합니다.
  
         추가된 기본 사용자 지정 설치가 **고급 설정** 페이지에 표시됩니다. 이를 제거하려면 해당 확인란을 선택한 다음, **삭제** 를 선택합니다.

   1. **조인할 Azure-SSIS Integration Runtime에 대한 VNet을 선택하고 ADF가 특정 네트워크 리소스를 만들고 선택적으로 자체 고정 공용 IP 주소를 가져오도록 허용** 확인란을 선택하여 Azure-SSIS IR을 가상 네트워크에 조인할지 여부를 선택합니다. 

      가상 네트워크를 조인하여 SSISDB를 호스트하는 가상 네트워크 서비스 엔드포인트/IP 방화벽 규칙/프라이빗 엔드포인트 또는 Azure SQL Managed Instance 구성된 Azure SQL Database 서버를 사용하거나, 자체 호스팅 IR을 구성하지 않고 온-프레미스 데이터에 액세스해야 하는 경우(즉, SSIS 패키지에 온-프레미스 데이터 원본/대상이 있는 경우) 선택합니다. 자세한 내용은 [가상 네트워크에 Azure-SSIS IR 조인하기](join-azure-ssis-integration-runtime-virtual-network.md)를 참조하세요. 

      확인란을 선택하는 경우 다음 단계를 완료하세요.

      :::image type="content" source="./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-vnet.png" alt-text="가상 네트워크 관련 고급 설정":::

      1. **구독** 에 대해서는 가상 네트워크가 있는 Azure 구독을 선택합니다.

      1. **위치** 에는 통합 런타임의 동일한 위치가 선택됩니다.

      1. **유형에서** 가상 네트워크의 **유형(Azure Resource Manager Virtual Network/클래식** 가상 네트워크)을 선택합니다. 클래식 가상 네트워크는 곧 **사용되지 않으므로 Azure Resource Manager Virtual Network** 를 선택하는 것이 좋습니다.

      1. **VNet 이름** 에서 가상 네트워크의 이름을 선택합니다. SSISDB를 호스트하는 Azure SQL Database 서버에 대한 가상 네트워크 서비스 엔드포인트/프라이빗 엔드포인트를 구성하는 데 사용되는 것과 동일해야 합니다. 또는 SSISDB를 호스트하는 Azure SQL Managed Instance 조인한 것과 동일해야 합니다. 또는 온-프레미스 네트워크에 연결된 것과 동일해야 합니다. 그렇지 않으면 Azure-SSIS IR에 자체 정적 공용 IP를 가져오는 임의의 가상 네트워크일 수 있습니다.

      1. **서브넷 이름** 에서 가상 네트워크의 서브넷 이름을 선택합니다. SSISDB를 호스트하는 Azure SQL Database 서버에 대한 가상 네트워크 서비스 엔드포인트를 구성하는 데 사용되는 것과 동일해야 합니다. 또는 SSISDB를 호스트하는 Azure SQL Managed Instance 조인한 서브넷과 다른 서브넷이어야 합니다. 그렇지 않은 경우에는 Azure-SSIS IR의 자체 고정 공용 IP를 가져오는 임의의 서브넷이 될 수 있습니다.

      1. **VNet 삽입 방법** 의 경우 가상 네트워크 삽입 방법 **Express** / **Standard** 를 선택합니다. 
   
         이러한 방법을 비교하려면 [표준 및 Express 가상 네트워크 삽입 방법 비교](azure-ssis-integration-runtime-virtual-network-configuration.md#compare) 문서를 참조하세요. 
   
         **Express를** 선택하는 경우 [Express 가상 네트워크 삽입 방법](azure-ssis-integration-runtime-express-virtual-network-injection.md) 문서를 참조하세요. 
      
         **표준을** 선택하는 경우 [표준 가상 네트워크 주입 방법](azure-ssis-integration-runtime-standard-virtual-network-injection.md) 문서를 참조하세요.  이 메서드를 사용하여 다음을 수행할 수도 있습니다.

         1. Azure-SSIS Integration Runtime **고정 공용 IP 주소** 가져오기 확인란을 선택하여 데이터 저장소의 방화벽에서 허용할 수 있도록 Azure-SSIS IR 고유한 고정 공용 IP 주소를 가져올지 여부를 선택합니다.

            확인란을 선택하는 경우 다음 단계를 완료하세요.

            1. **첫 번째 고정 공용 IP 주소** 는 Azure-SSIS IR [요구 사항을 충족](azure-ssis-integration-runtime-standard-virtual-network-injection.md#ip)하는 첫 번째 고정 공용 IP를 선택합니다. 없는 경우 **새로 만들기** 링크를 클릭하여 Azure Portal 고정 공용 IP 주소를 만든 다음 여기에서 새로 고침 단추를 클릭하여 선택할 수 있습니다.
      
            1. **두 번째 고정 공용 IP** 는 Azure-SSIS IR [요구 사항을 충족](azure-ssis-integration-runtime-standard-virtual-network-injection.md#ip)하는 두 번째 고정 공용 IP 주소를 선택합니다. 없는 경우 **새로 만들기** 링크를 클릭하여 Azure Portal 고정 공용 IP 주소를 만든 다음 여기에서 새로 고침 단추를 클릭하여 선택할 수 있습니다.

   1. **Set up Self-Hosted Integration Runtime as a proxy for your Azure-SSIS Integration Runtime**(자체 호스팅 통합 런타임을 Azure-SSIS Integration Runtime의 프록시로 설정) 확인란을 선택하여 자체 호스팅 IR을 Azure-SSIS IR의 프록시로 구성할지 여부를 선택합니다. 자세한 내용은 [자체 호스팅 IR을 프록시로 설정](./self-hosted-integration-runtime-proxy-ssis.md)을 참조하세요. 

      확인란을 선택하는 경우 다음 단계를 완료하세요.

      :::image type="content" source="./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-shir.png" alt-text="자체 호스팅 IR을 사용하는 고급 설정":::

      1. **자체 호스팅 통합 런타임** 의 경우, Azure-SSIS IR의 프록시로 기존 자체 호스팅 IR을 선택합니다.

      1. **스테이징 저장소 연결 서비스** 의 경우, 기존 Azure Blob 저장소 연결 서비스를 선택하거나 스테이징용 서비스를 새로 만듭니다.

      1. **준비 경로** 의 경우, 선택한 Azure Blob 스토리지 계정에 blob 컨테이너를 지정하거나 스테이징에 기본 항목을 사용하도록 비워 둡니다.

   1. **VNet 유효성 검사** 를 선택합니다. 유효성 검사에 성공하면 **계속** 을 선택합니다. 

**요약** 페이지에서 Azure-SSIS IR 대한 모든 설정을 검토하고, 권장 설명서 링크에 책갈피를 지정하고, **마침을** 선택하여 통합 런타임 만들기를 시작합니다.

> [!NOTE]
> 이 프로세스는 사용자 지정 설치 시간을 제외하고 5분 이내에 완료됩니다. 그러나 Azure-SSIS IR 표준 삽입 방법으로 가상 네트워크에 가입하는 데 20-30분이 걸릴 수 있습니다.
>
> SSISDB를 사용하는 경우 Data Factory 서비스에서 데이터베이스 서버에 연결하여 SSISDB를 준비합니다. 또한 이 서비스는 가상 네트워크에 대한 권한 및 설정을 구성하며, 지정된 경우 Azure-SSIS IR을 가상 네트워크에 조인합니다.
>
> Azure-SSIS IR을 프로비저닝하는 경우 Access 재배포 가능 패키지 및 Azure Feature Pack for SSIS도 설치됩니다. 이러한 구성 요소는 기본 제공 구성 요소에서 이미 지원하는 데이터 원본 외에도 Excel 파일, Access 파일 및 다양한 Azure 데이터 원본에 대한 연결을 제공합니다. 기본 제공/미리 설치된 구성 요소에 대한 자세한 내용은 [Azure-SSIS IR의 기본 제공/미리 설치된 구성 요소](./built-in-preinstalled-components-ssis-integration-runtime.md)를 참조하세요. 설치할 수 있는 추가 구성 요소에 대한 자세한 내용은 [Azure-SSIS IR 사용자 지정 설치](./how-to-configure-azure-ssis-ir-custom-setup.md)를 참조하세요.

### <a name="connections-pane"></a>[연결] 창

**관리** 허브의 **연결** 창에서 **통합 런타임** 페이지로 전환하여 **새로 고침** 을 선택합니다. 

   :::image type="content" source="./media/tutorial-create-azure-ssis-runtime-portal/connections-pane.png" alt-text="[연결] 창":::

   Azure-SSIS IR의 이름을 선택하여 편집/재구성할 수 있습니다. 또한 관련된 단추를 선택하여 Azure-SSIS IR을 모니터링/시작/중지/삭제하고, SSIS 패키지 실행 작업을 사용하여 Azure-SSIS IR에서 실행할 ADF 파이프라인을 자동으로 생성하고, Azure-SSIS IR의 JSON 코드/페이로드를 볼 수 있습니다.  Azure-SSIS IR이 중지된 상태에서만 편집/삭제할 수 있습니다.

## <a name="azure-ssis-integration-runtimes-in-the-portal"></a>포털의 Azure SSIS 통합 런타임

1. Azure Data Factory UI에서 **관리** 탭으로 전환한 다음, **연결** 에서 **통합 런타임** 탭으로 전환하여 데이터 팩터리에서 기존 통합 런타임을 확인합니다.

   :::image type="content" source="./media/tutorial-create-azure-ssis-runtime-portal/view-azure-ssis-integration-runtimes.png" alt-text="기존 IR 보기":::

1. **새로 만들기** 를 선택하여 새 Azure-SSIS IR을 만들고, **통합 런타임 설치** 창을 엽니다.

   :::image type="content" source="./media/tutorial-create-azure-ssis-runtime-portal/edit-connections-new-integration-runtime-button.png" alt-text="메뉴를 통한 통합 런타임":::

1. **통합 런타임 설정** 창에서 **Azure에서 실행할 기존 SSIS 패키지 리프트 앤 시프트** 타일을 선택한 다음, **계속** 을 선택합니다.

   :::image type="content" source="./media/tutorial-create-azure-ssis-runtime-portal/integration-runtime-setup-options.png" alt-text="통합 런타임 유형 지정":::

1. 나머지 Azure-SSIS IR 설치 단계는 [Azure SSIS 통합 런타임 프로비전](#provision-an-azure-ssis-integration-runtime) 섹션을 참조하세요.
 
## <a name="next-steps"></a>다음 단계

- [Azure PowerShell 를 통해 Azure-SSIS IR 만듭니다.](create-azure-ssis-integration-runtime-powershell.md)
- [Azure Resource Manager 템플릿을 통해 Azure-SSIS IR 만듭니다.](create-azure-ssis-integration-runtime-resource-manager-template.md)
- [Azure-SSIS IR 에서 SSIS 패키지를 배포하고 실행합니다.](create-azure-ssis-integration-runtime-deploy-packages.md)

Azure-SSIS IR에 대한 자세한 내용은 다음 문서를 참조하세요. 

- [Azure SSIS IR](concepts-integration-runtime.md#azure-ssis-integration-runtime). 이 문서에서는 Azure-SSIS IR 등 IR에 대한 일반적인 개념적 정보를 제공합니다. 
- [Azure-SSIS IR 모니터링](monitor-integration-runtime.md#azure-ssis-integration-runtime). 이 문서에서는 Azure SSIS IR에 대한 정보를 가져와 파악하는 방법을 설명합니다.
- [Azure-SSIS IR 관리](manage-azure-ssis-integration-runtime.md). 이 문서에서는 Azure-SSIS IR을 중지하거나, 시작하거나, 삭제하는 방법을 설명합니다. 또한 노드를 추가하여 Azure-SSIS IR 규모를 확장하는 방법도 설명합니다.
- [Azure에서 SSIS 패키지 배포, 실행 및 모니터링](/sql/integration-services/lift-shift/ssis-azure-deploy-run-monitor-tutorial)   
- [Azure에서 SSISDB에 연결](/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database)
- [Windows 인증을 사용 하 여 온-프레미스 데이터 저장소에 커넥트](/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth) 
- [Azure에서 SSIS 패키지 실행 예약](/sql/integration-services/lift-shift/ssis-azure-schedule-packages)
