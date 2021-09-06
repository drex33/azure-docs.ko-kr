---
title: Azure 독일에서 글로벌 Azure로 Azure 컴퓨팅 리소스 마이그레이션
description: 이 문서에서는 Azure 컴퓨팅 리소스를 Azure 독일에서 글로벌 Azure로 마이그레이션하는 방법을 설명합니다.
ms.topic: article
ms.date: 10/16/2020
author: gitralf
ms.author: ralfwi
ms.service: germany
ms.custom: bfmigrate
ms.openlocfilehash: 3662039dad5d85c87c2598fb59b7719ab9251090
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/21/2020
ms.locfileid: "122535442"
---
# <a name="migrate-compute-resources-to-global-azure"></a>컴퓨팅 리소스를 글로벌 Azure로 마이그레이션

[!INCLUDE [closureinfo](../../includes/germany-closure-info.md)]

이 문서에는 Azure 컴퓨팅 리소스를 Azure 독일에서 글로벌 Azure로 마이그레이션하는 데 도움이 되는 정보가 있습니다.

## <a name="compute-iaas"></a>컴퓨팅 IaaS

Azure 컴퓨팅 IaaS(Infrastructure as a Service) 리소스를 Azure 독일에서 글로벌 Azure로 직접 마이그레이션할 수 없습니다. 하지만 여러 가지 방법으로 VM을 "복제"할 수 있습니다.

### <a name="duplicate-by-using-site-recovery"></a>Site Recovery를 사용하여 복제

Azure Site Recovery는 VM을 Azure 독일에서 글로벌 Azure로 마이그레이션하는 데 도움이 될 수 있습니다. Azure 독일에서 글로벌 Azure로 마이그레이션하는 경우에는 원본과 대상이 서로 다른 테넌트에 있기 때문에 VM에 제공되는 일반적인 Azure 재해 복구 옵션을 사용할 수 없습니다. 그 대신 대상 환경(글로벌 Azure)에서 Site Recovery 자격 증명 모음을 설정하고 물리적 서버를 Azure로 이동할 때처럼 진행하면 됩니다. Azure Portal에서 **가상화되지 않음** 이라는 레이블이 지정된 복제 경로를 선택합니다. 복제가 완료되면 장애 조치(failover)를 수행합니다.

> [!NOTE]
> 다음 단계는 온-프레미스에서 실행되는 물리적 서버를 Azure로 마이그레이션할 때 수행하는 단계와 동일합니다.

자세한 내용은 이 [유용한 Site Recovery 자습서](../site-recovery/physical-azure-disaster-recovery.md)를 검토하세요. 아래는 이 프로세스를 요약하고 약간 조정한 버전입니다.

원본 환경에 구성/프로세스 서버를 설치하여 서버 이미지를 빌드합니다. 그런 다음, 대상 환경의 Azure Recovery Services 자격 증명 모음에 이미지를 복제합니다. 모든 작업은 구성 서버에서 수행됩니다. 개별 서버를 신경 쓸 필요가 없습니다.

1. Azure 독일 포털에 로그인합니다.
1. 마이그레이션하려는 VM의 OS 버전을 [지원 매트릭스](../site-recovery/vmware-physical-secondary-support-matrix.md)와 비교합니다.
1. 구성 서버 역할을 수행하도록 원본 Azure Virtual Network 인스턴스에 새 VM을 설정합니다.
   1. **DS4v3** 이상(4~8코어, 16GB 메모리)을 선택합니다.
   1. 사용 가능한 공간이 1TB 이상인 추가 디스크를 연결합니다(VM 이미지용).
   1. Windows Server 2012 R2 이상을 사용합니다.
1. 양쪽 방향에서 서브넷에 대해 443 및 9443 포트를 엽니다.
1. 새 VM(ConfigurationServer)에 로그인합니다.
1. 원격 데스크톱 세션에서 글로벌 Azure 자격 증명을 사용하여 글로벌 Azure Portal에 로그인합니다.
1. 복제된 VM이 실행될 가상 네트워크를 설정합니다.
1. Azure Storage 계정 만들기
1. Recovery Services 자격 증명 모음을 설정합니다.
1. **보호 목표** 를 정의합니다(**Azure에** > **가상화되지 않음/기타**).
1. Recovery 통합 설정 설치 파일을 다운로드합니다(**인프라 준비** > **원본**). ConfigurationServer 내에서 포털 URL을 열면 파일이 올바른 서버에 다운로드됩니다. ConfigurationServer 외부에서 설치 파일을 ConfigurationServer에 업로드합니다.
1. 자격 증명 모음 등록 키를 다운로드합니다(필요한 경우 이전 단계와 같은 방법으로 ConfigurationServer에 업로드).
1. ConfigurationServer에서 Recovery 통합 설정 설치를 실행합니다.
1. 대상 환경을 설정합니다(대상 포털에 여전히 로그인되어 있는지 확인).
1. 복제 정책을 정의합니다.
1. 복제를 시작합니다.

복제가 처음으로 성공한 후, 테스트 장애 조치(failover)를 수행하여 시나리오를 테스트합니다. 테스트를 확인하고 삭제합니다. 마지막 단계는 실제 장애 조치(failover)를 수행하는 것입니다.

> [!CAUTION]
> 원본 VM에 다시 동기화하는 작업은 발생하지 않습니다. 다시 마이그레이션하려면 모든 항목을 정리하고 처음부터 다시 시작해야 합니다.

### <a name="duplicate-by-using-resource-manager-template-exportimport"></a>Resource Manager 템플릿 내보내기/가져오기를 사용하여 복제

로컬 머신에 배포하는 데 사용하는 Azure Resource Manager 템플릿을 내보낼 수 있습니다. 템플릿을 편집하여 위치 및 기타 매개 변수 또는 변수를 변경합니다. 그런 다음, 글로벌 Azure에 다시 배포합니다. 

> [!IMPORTANT]
> 새 지역과 일치하도록 위치, Azure Key Vault 비밀, 인증서 및 기타 GUID를 변경합니다.

포털에서 리소스 그룹을 선택하여 Resource Manager 템플릿을 내보냅니다. **배포** 를 선택한 다음, 가장 최근 배포를 선택합니다. 왼쪽 메뉴에서 **템플릿** 을 선택하고 템플릿을 다운로드합니다.

여러 파일이 들어 있는 .zip 파일이 다운로드됩니다. PowerShell, Azure CLI, Ruby 또는 .NET 스크립트는 템플릿 배포를 도와줍니다. *parameters.json* 파일에는 마지막 배포의 모든 입력이 포함되어 있습니다. 아마도 이 파일의 일부 설정을 변경해야 할 것입니다. 일부 리소스만 다시 배포하려면 *template.json* 파일을 편집합니다.

추가 정보는 다음 항목을 참조하세요.

- [Site Recovery 자습서](../site-recovery/index.yml)를 완료하여 새 정보를 얻습니다.
- [Resource Manager 템플릿 내보내기](../azure-resource-manager/templates/export-template-portal.md)에 대해 알아보거나 [Azure Resource Manager](../azure-resource-manager/management/overview.md) 개요를 읽습니다.
- [Site Recovery를 사용하여 물리적 서버에서 Azure로 재해 복구](../site-recovery/physical-azure-disaster-recovery.md)에 대해 자세히 알아봅니다.
- [Azure 위치 개요](https://azure.microsoft.com/global-infrastructure/locations/)를 읽습니다.
- [템플릿 다시 배포](../azure-resource-manager/templates/deploy-powershell.md)에 대해 알아봅니다.

## <a name="cloud-services"></a>Cloud Services

`.cspkg` 및 `.cscfg` 정의를 다시 제공하여 Azure Cloud Services 리소스를 다시 배포할 수 있습니다.

### <a name="azure-portal"></a>Azure portal

Azure Portal에서 클라우드 서비스를 다시 배포하려면 다음을 수행합니다.

1. `.cspkg` 및 `.cscfg` 정의를 사용하여 [새 클라우드 서비스를 만듭니다](../cloud-services/cloud-services-how-to-create-deploy-portal.md).
1. 새 클라우드 서비스로의 트래픽을 가리키도록 [CNAME 또는 A 레코드](../cloud-services/cloud-services-custom-domain-name-portal.md)를 업데이트합니다.
1. 트래픽이 새 클라우드 서비스를 가리키는 경우 Azure 독일에서 기존 클라우드 서비스를 삭제합니다.

### <a name="powershell"></a>PowerShell

PowerShell을 사용하여 클라우드 서비스를 다시 배포하려면 다음을 수행합니다.

1. `.cspkg` 및 `.cscfg` 정의를 사용하여 [새 클라우드 서비스를 만듭니다](/powershell/module/servicemanagement/azure.service/new-azureservice).

    ```powershell
    New-AzureService -ServiceName <yourServiceName> -Label <MyTestService> -Location <westeurope>
    ```

1. `.cspkg` 및 `.cscfg` 정의를 사용하여 [새 배포를 만듭니다](/powershell/module/servicemanagement/azure.service/new-azuredeployment).

    ```powershell
    New-AzureDeployment -ServiceName <yourServiceName> -Slot <Production> -Package <YourCspkgFile.cspkg> -Configuration <YourConfigFile.cscfg>
    ```

1. 새 클라우드 서비스로의 트래픽을 가리키도록 [CNAME 또는 A 레코드](../cloud-services/cloud-services-custom-domain-name-portal.md)를 업데이트합니다.
1. 트래픽이 새 클라우드 서비스를 가리키는 경우 Azure 독일에서 [기존 클라우드 서비스를 삭제](/powershell/module/servicemanagement/azure.service/remove-azureservice)합니다.

    ```powershell
    Remove-AzureService -ServiceName <yourOldServiceName>
    ```

### <a name="rest-api"></a>REST API

REST API를 사용하여 클라우드 서비스를 다시 배포하려면 다음을 수행합니다.

1. 대상 환경에서 [새 클라우드 서비스를 만듭니다](/rest/api/compute/cloudservices/rest-create-cloud-service).

    ```http
    https://management.core.windows.net/<subscription-id>/services/hostedservices
    ```

1. [배포 만들기 API](/previous-versions/azure/reference/ee460813(v=azure.100))를 사용하여 새 배포를 만듭니다. `.cspkg` 및 `.cscfg` 정의를 찾으려면 [패키지 가져오기 API](/previous-versions/azure/reference/jj154121(v=azure.100))를 호출합니다.

    ```http
    https://management.core.windows.net/<subscription-id>/services/hostedservices/<cloudservice-name>/deploymentslots/production
    ```

1. 트래픽이 새 클라우드 서비스를 가리키는 경우 Azure 독일에서 [기존 클라우드 서비스를 삭제](/rest/api/compute/cloudservices/rest-delete-cloud-service)합니다.

    ```http
    https://management.core.cloudapi.de/<subscription-id>/services/hostedservices/<old-cloudservice-name>
    ```

추가 정보는 다음 항목을 참조하세요.

- [Cloud Services 개요](../cloud-services/cloud-services-choose-me.md)를 검토합니다.

## <a name="service-fabric"></a>Service Fabric

Azure Service Fabric 리소스를 Azure 독일에서 글로벌 Azure로 마이그레이션할 수 없습니다. 새 환경에서 Service Fabric 리소스를 다시 배포해야 합니다.

PowerShell cmdlet을 사용하여 현재 Service Fabric 환경에 대한 정보를 가져올 수 있습니다. PowerShell에서 `Get-Help *ServiceFabric*`을 입력하여 Service Fabric과 관련된 모든 cmdlet에 액세스하면 됩니다.

추가 정보는 다음 항목을 참조하세요.

- [Service Fabric 자습서](../service-fabric/service-fabric-tutorial-create-dotnet-app.md)를 완료하여 새 정보를 얻습니다.
- [새 클러스터 만들기](../service-fabric/service-fabric-cluster-creation-via-portal.md)에 대해 알아봅니다.
- [Service Fabric 개요](../service-fabric/service-fabric-overview.md)를 검토합니다.

## <a name="batch"></a>Batch

Azure Batch 계정 데이터를 한 지역에서 다른 지역으로 마이그레이션할 수 없습니다. 이 계정은 연결된 VM을 실행 중이고 스토리지 계정, 데이터베이스 또는 다른 스토리지 시스템의 데이터와 적극적으로 상호 작용하는 중일 수 있기 때문입니다.

새 지역에 배포 스크립트, 템플릿 또는 코드를 다시 배포하세요. 다시 배포하려면 다음 작업을 수행합니다.

1. [Batch 계정을 만듭니다](../batch/batch-account-create-portal.md).
1. [Batch 계정 할당량을 늘립니다](../batch/batch-quota-limit.md).
1. Batch 풀을 만듭니다.
1. 입력 및 출력 데이터를 유지하는 데 사용되는 새 스토리지 계정, 데이터베이스 및 기타 서비스를 만듭니다.
1. 새 Batch 계정을 가리키도록 구성 및 코드를 업데이트하고 새 자격 증명을 사용합니다.

추가 정보는 다음 항목을 참조하세요.

- [Batch 자습서](../batch/tutorial-parallel-dotnet.md)를 완료하여 새 정보를 얻습니다.
- [Azure Batch 개요](../batch/batch-technical-overview.md)를 검토합니다.

## <a name="functions"></a>Functions

Azure 독일에서 글로벌 Azure로의 Azure Functions 리소스 마이그레이션은 현재 지원되지 않습니다. Resource Manager 템플릿을 내보내고 위치를 변경한 다음, 대상 지역에 다시 배포하는 것이 좋습니다.

> [!IMPORTANT]
> 새 지역과 일치하도록 위치, Key Vault 비밀, 인증서, 앱 설정 및 기타 GUID를 변경합니다.

추가 정보는 다음 항목을 참조하세요.

- [함수 자습서](../azure-functions/index.yml)를 완료하여 새 정보를 얻습니다.
- [Resource Manager 템플릿 내보내기](../azure-resource-manager/templates/export-template-portal.md) 방법을 알아보거나 [Azure Resource Manager](../azure-resource-manager/management/overview.md) 개요를 읽습니다.
- [Azure Functions 개요](../azure-functions/functions-overview.md)를 검토합니다.
- [Azure 위치 개요](https://azure.microsoft.com/global-infrastructure/locations/)를 읽습니다.
- [템플릿 다시 배포](../azure-resource-manager/templates/deploy-powershell.md)에 대해 알아봅니다.

## <a name="virtual-machine-scale-sets"></a>가상 머신 크기 집합

가상 머신 확장 집합을 글로벌 Azure로 마이그레이션하려면 Resource Manager 템플릿을 내보내고 새 환경에 맞게 적용한 다음, 대상 지역에 다시 배포합니다. 기본 템플릿만 내보내고 새 환경에 템플릿을 다시 배포합니다. 개별 가상 머신 확장 집합 인스턴스는 모두 동일해야 합니다.

> [!IMPORTANT]
> 새 지역과 일치하도록 위치, Key Vault 비밀, 인증서 및 기타 GUID를 변경합니다.

추가 정보는 다음 항목을 참조하세요.

- [가상 머신 확장 집합 자습서](../virtual-machine-scale-sets/tutorial-create-and-manage-cli.md)를 완료하여 새 정보를 얻습니다.
- [Azure Resource Manager 템플릿 내보내기](../azure-resource-manager/templates/export-template-portal.md)에 대해 알아봅니다.
- [Azure Resource Manager 개요](../azure-resource-manager/management/overview.md)를 검토합니다.
- [가상 머신 확장 집합](../virtual-machine-scale-sets/overview.md)의 개요를 알아봅니다.
- [Azure 위치 개요](https://azure.microsoft.com/global-infrastructure/locations/)를 읽습니다.
- [템플릿 다시 배포](../azure-resource-manager/templates/deploy-powershell.md)에 대해 알아봅니다.

## <a name="web-apps"></a>Web Apps

현재는 Azure App Service의 Web Apps 기능을 사용하여 만든 앱을 Azure 독일에서 글로벌 Azure로 마이그레이션할 수 없습니다. 웹앱을 Resource Manager 템플릿으로 내보낸 다음, 위치 속성을 새 지역으로 변경하고 다시 배포하는 것이 좋습니다.

> [!IMPORTANT]
> 새 지역과 일치하도록 위치, Key Vault 비밀, 인증서 및 기타 GUID를 변경합니다.

추가 정보는 다음 항목을 참조하세요.

- [App Service 자습서](../app-service/tutorial-dotnetcore-sqldb-app.md)를 완료하여 새 정보를 얻습니다.
- [Resource Manager 템플릿 내보내기](../azure-resource-manager/templates/export-template-portal.md) 방법을 알아보거나 [Azure Resource Manager](../azure-resource-manager/management/overview.md) 개요를 읽습니다.
- [App Service 개요](../app-service/overview.md)를 검토합니다.
- [Azure 위치 개요](https://azure.microsoft.com/global-infrastructure/locations/)를 읽습니다.
- [템플릿 다시 배포](../azure-resource-manager/templates/deploy-powershell.md)에 대해 알아봅니다.

## <a name="next-steps"></a>다음 단계

다음 서비스 범주에서 리소스 마이그레이션을 위한 도구, 기술 및 권장 사항에 대해 알아봅니다.

- [네트워킹](./germany-migration-networking.md)
- [스토리지](./germany-migration-storage.md)
- [Web](./germany-migration-web.md)
- [데이터베이스](./germany-migration-databases.md)
- [분석](./germany-migration-analytics.md)
- [IoT](./germany-migration-iot.md)
- [통합](./germany-migration-integration.md)
- [ID](./germany-migration-identity.md)
- [보안](./germany-migration-security.md)
- [관리 도구](./germany-migration-management-tools.md)
- [미디어](./germany-migration-media.md)