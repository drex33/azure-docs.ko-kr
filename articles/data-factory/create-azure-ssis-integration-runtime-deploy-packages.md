---
title: SSIS 패키지 배포
description: Azure-SSIS 통합 런타임을 사용하여 Azure Data Factory SSIS 패키지를 배포하고 실행하는 방법을 알아봅니다.
ms.service: data-factory
ms.subservice: integration-services
ms.topic: conceptual
ms.date: 09/27/2021
author: swinarko
ms.author: sawinark
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: e12968b993a68f8eb0da179c6b4de57256a0c63b
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/29/2021
ms.locfileid: "129220443"
---
# <a name="deploy-ssis-packages"></a>SSIS 패키지 배포

Azure-SSIS 통합 런타임을 구성한 후에는 Azure에서 패키지를 직접 배포하고 실행할 수 있습니다.

## <a name="using-ssisdb"></a>SSISDB 사용

SSISDB를 사용하는 경우 Azure 지원 SSDT 또는 SSMS 도구를 사용하여 SSISDB에 패키지를 배포하고 Azure-SSIS IR에서 실행할 수 있습니다. 이러한 도구는 다음과 같은 서버 엔드포인트를 통해 데이터베이스 서버에 연결됩니다. 

- Azure SQL Database 서버의 경우 서버 엔드포인트 형식은 `<server name>.database.windows.net`입니다.
- 프라이빗 엔드포인트가 있는 관리형 인스턴스의 경우 서버 엔드포인트 형식은 `<server name>.<dns prefix>.database.windows.net`입니다.
- 퍼블릭 엔드포인트가 있는 관리형 인스턴스의 경우 서버 엔드포인트 형식은 `<server name>.public.<dns prefix>.database.windows.net,3342`입니다. 

## <a name="using-file-system-azure-files-or-msdb"></a>파일 시스템, Azure 파일 또는 MSDB 사용

SSISDB를 사용하지 않는 경우 [dtutil](/sql/integration-services/dtutil-utility) 및 [AzureDTExec](./how-to-invoke-ssis-package-azure-enabled-dtexec.md) 명령줄 유틸리티를 사용하여 Azure SQL Managed Instance가 호스트하는 파일 시스템, Azure Files 또는 MSDB에 패키지를 배포하고 Azure-SSIS IR에서 실행할 수 있습니다. 

자세한 내용은 [SSIS 프로젝트/패키지 배포](/sql/integration-services/packages/deploy-integration-services-ssis-projects-and-packages)를 참조하세요.

또한 두 경우 모두 Data Factory 파이프라인에서 SSIS 패키지 실행 활동을 사용하여 배포된 패키지를 Azure-SSIS IR에서 실행할 수도 있습니다. 자세한 내용은 [SSIS 패키지 실행을 Data Factory 첫 번째 클래스 활동으로 호출](./how-to-invoke-ssis-package-ssis-activity.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계

- [Azure Portal 사용하여 Azure-SSIS IR 프로비전하는 방법을 알아봅니다.](create-azure-ssis-integration-runtime-portal.md)
- [Azure PowerShell 사용하여 Azure-SSIS IR 프로비전하는 방법을 알아봅니다.](create-azure-ssis-integration-runtime-powershell.md)
- [Azure Resource Manager 템플릿을 사용하여 Azure-SSIS IR 프로비전하는 방법을 알아봅니다.](create-azure-ssis-integration-runtime-resource-manager-template.md)

이 설명서의 다른 Azure-SSIS IR 항목을 참조하세요.

- [Azure-SSIS 통합 런타임](concepts-integration-runtime.md#azure-ssis-integration-runtime). 이 문서에서는 Azure-SSIS IR을 비롯한 일반적인 통합 런타임에 관한 정보를 제공합니다.
- [Azure-SSIS IR 모니터링](monitor-integration-runtime.md#azure-ssis-integration-runtime). 이 문서에서는 Azure SSIS IR에 대한 정보를 가져와 파악하는 방법을 설명합니다.
- [Azure-SSIS IR 관리](manage-azure-ssis-integration-runtime.md). 이 문서에서는 Azure-SSIS IR을 중지하거나, 시작하거나, 삭제하는 방법을 설명합니다. 또한 노드를 추가하여 Azure-SSIS IR 규모를 확장하는 방법도 설명합니다.
- [Azure에서 SSIS 패키지 배포, 실행 및 모니터링](/sql/integration-services/lift-shift/ssis-azure-deploy-run-monitor-tutorial)   
- [Azure에서 SSISDB에 연결](/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database)
- [Windows 인증을 사용하는 온-프레미스 데이터 원본에 연결](/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth) 
- [Azure에서 패키지 실행 예약](/sql/integration-services/lift-shift/ssis-azure-schedule-packages)
