---
title: Azure-SSIS Integration Runtime Enterprise 버전 프로비전
description: 이 문서에서는 Azure-SSIS Integration Runtime Enterprise 버전의 기능 및 프로비전하는 방법을 설명합니다.
ms.service: data-factory
ms.subservice: integration-services
ms.topic: conceptual
ms.date: 10/22/2021
author: swinarko
ms.author: sawinark
ms.openlocfilehash: bf8c6cd68262d07109fc51657720f77f6be4a3e6
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/05/2021
ms.locfileid: "131843659"
---
# <a name="provision-enterprise-edition-for-the-azure-ssis-integration-runtime"></a>Azure-SSIS Integration Runtime Enterprise 버전 프로비전

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Azure-SSIS Integration Runtime Enterprise 버전에서는 다음과 같은 고급 및 프리미엄 기능을 사용할 수 있습니다.
-   CDC(변경 데이터 캡처) 구성 요소
-   Oracle, Teradata 및 SAP BW 커넥터
-   SSAS(SQL Server Analysis Services) 및 AAS(Azure Analysis Services) 커넥터와 변환
-   유사 항목 그룹화 및 유사 항목 조회 변환
-   용어 추출 및 용어 조회 변환

이러한 기능 중 일부를 사용하려면 추가 구성 요소를 설치하여 Azure-SSIS IR을 사용자 지정해야 합니다. 추가 구성 요소를 설치하는 방법에 대한 자세한 내용은 [Azure-SSIS 통합 런타임 사용자 지정 설정](how-to-configure-azure-ssis-ir-custom-setup.md)을 참조하세요.

## <a name="enterprise-features"></a>엔터프라이즈 기능

| **엔터프라이즈 기능** | **설명** |
|---|---|
| CDC 구성 요소 | CDC Source, Control Task 및 Splitter Transformation은 Azure-SSIS IR Enterprise Edition에 미리 설치됩니다. Oracle에 연결하려면 다른 컴퓨터에 CDC Designer 및 Service를 설치해야 합니다. |
| Oracle 커넥터 | Oracle Connection Manager, Source 및 Destination은 Azure-SSIS IR Enterprise Edition에 미리 설치됩니다. 또한 OCI(Oracle Call Interface) 드라이버를 설치하고, 필요한 경우 Azure-SSIS IR에서 Oracle TNS(Transport Network Substrate)를 구성해야 합니다. 자세한 내용은 [Azure SSIS 통합 런타임에 대한 사용자 지정 설치](how-to-configure-azure-ssis-ir-custom-setup.md)를 참조하세요. |
| Teradata 커넥터 | Azure-SSIS IR Enterprise Edition에 Teradata Connection Manager, Source 및 Destination 뿐 아니라 TPT(Teradata Parallel Transporter) API와 Teradata ODBC 드라이버를 설치해야 합니다. 자세한 내용은 [Azure SSIS 통합 런타임에 대한 사용자 지정 설치](how-to-configure-azure-ssis-ir-custom-setup.md)를 참조하세요. |
| SAP BW 커넥터 | SAP BW Connection Manager, Source 및 Destination은 Azure-SSIS IR Enterprise Edition에 미리 설치됩니다. 또한 Azure-SSIS IR에서 SAP BW 드라이버를 설치해야 합니다. 이러한 커넥터는 SAP BW 7.0 또는 이전 버전을 지원합니다. 이후 버전의 SAP BW 또는 기타 SAP 제품에 연결하려면 타사 ISV에서 SAP 커넥터를 구입한 후 Azure-SSIS IR에 설치할 수 있습니다. 추가 구성 요소를 설치하는 방법에 대한 자세한 내용은 [Azure-SSIS 통합 런타임 사용자 지정 설정](how-to-configure-azure-ssis-ir-custom-setup.md)을 참조하세요. |
| Analysis Services 구성 요소               | 데이터 마이닝 모델 학습 대상, 차원 처리 대상 및 파티션 처리 대상과 데이터 마이닝 쿼리 변환은 Azure-SSIS IR Enterprise Edition에 미리 설치됩니다. 이러한 모든 구성 요소는 SSAS(SQL Server Analysis Services)를 지원하지만 AAS(Azure Analysis Services)는 파티션 처리 대상만 지원합니다. SSAS에 연결하려면 [SSISDB에서 Windows 인증 자격 증명도 구성](/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth)해야 합니다. 이러한 구성 요소 외에, Analysis Services DDL 실행 태스크, Analysis Services 처리 태스크 및 데이터 마이닝 쿼리 태스크도 Azure-SSIS IR Standard/Enterprise Edition에 미리 설치됩니다. |
| 유사 항목 그룹화 및 유사 항목 조회 변환  | 유사 항목 그룹화 및 유사 항목 조회 변환은 Azure-SSIS IR Enterprise Edition에 미리 설치됩니다. 이러한 구성 요소는 참조 데이터를 저장하기 위해 SQL Server와 Azure SQL Database를 둘 다 지원합니다. |
| 용어 추출 및 용어 조회 변환 | 용어 추출 및 용어 조회 변환은 Azure-SSIS IR Enterprise Edition에 미리 설치됩니다. 이러한 구성 요소는 참조 데이터를 저장하기 위해 SQL Server와 Azure SQL Database를 둘 다 지원합니다. |

## <a name="instructions"></a>Instructions

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

1.  [Azure PowerShell](/powershell/azure/install-az-ps)를 다운로드하여 설치합니다.

2.  PowerShell을 사용하여 Azure-SSIS IR을 프로비전하거나 다시 구성하는 경우 Azure-SSIS IR을 시작하기 전에 **Edition** 매개 변수 값으로 **Enterprise** 를 지정하여 `Set-AzDataFactoryV2IntegrationRuntime`을 실행합니다. 샘플 스크립트는 다음과 같습니다.

    ```powershell
    $MyAzureSsisIrEdition = "Enterprise"

    Set-AzDataFactoryV2IntegrationRuntime -DataFactoryName $MyDataFactoryName
                                               -Name $MyAzureSsisIrName
                                               -ResourceGroupName $MyResourceGroupName
                                               -Edition $MyAzureSsisIrEdition

    Start-AzDataFactoryV2IntegrationRuntime -DataFactoryName $MyDataFactoryName
                                                 -Name $MyAzureSsisIrName
                                                 -ResourceGroupName $MyResourceGroupName
    ```

## <a name="next-steps"></a>다음 단계

-   [Azure-SSIS Integration Runtime의 사용자 지정 설치](how-to-configure-azure-ssis-ir-custom-setup.md)

-   [Azure-SSIS Integration Runtime에 대한 유료 또는 라이선스 사용자 지정 구성 요소를 개발하는 방법](how-to-develop-azure-ssis-ir-licensed-components.md)