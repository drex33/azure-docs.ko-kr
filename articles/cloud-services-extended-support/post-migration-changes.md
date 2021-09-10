---
title: Azure Cloud Services(추가 지원) 마이그레이션 후 변경 내용
description: Cloud Services로 마이그레이션한 뒤 마이그레이션 후 변경 내용의 개요(추가 지원)
ms.topic: how-to
ms.service: cloud-services-extended-support
ms.subservice: classic-to-arm-migration
author: tanmaygore
ms.author: tagore
ms.reviewer: mimckitt
ms.date: 2/08/2021
ms.custom: ''
ms.openlocfilehash: df9a41b6832c9ca93aa7078b0d06082f1441a5e5
ms.sourcegitcommit: beff1803eeb28b60482560eee8967122653bc19c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/07/2021
ms.locfileid: "113439272"
---
# <a name="post-migration-changes"></a>마이그레이션 후 변경 내용
Cloud Services(클래식) 배포는 Cloud Service(확장 지원) 배포로 변환됩니다. 자세한 내용은 [Cloud Services(추가 지원) 설명서](deploy-prerequisite.md)를 참조하세요.  

## <a name="changes-to-deployment-files"></a>배포 파일 변경 내용 

배포 파일이 Azure Resource Manager 및 Cloud Services(확장 지원) 요구 사항을 준수하도록 고객의 .csdef 및 .cscfg 파일이 약간 변경됩니다. 마이그레이션 후 새 배포 파일을 검색하거나 기존 파일을 업데이트합니다. 이는 업데이트/삭제 작업을 수행하는 데 필요합니다.  

- Virtual Network는 .cscfg 파일의 NetworkConfiguration 섹션에 있는 리소스 이름 대신 전체 Azure Resource Manager 리소스 ID를 사용합니다. 예들 들어 `/subscriptions/subscription-id/resourceGroups/resource-group-name/providers/Microsoft.Network/virtualNetworks/vnet-name`입니다. 클라우드 서비스와 동일한 리소스 그룹에 속한 가상 네트워크의 경우 가상 네트워크 이름만 사용하도록 .cscfg 파일을 다시 업데이트하도록 선택할 수 있습니다.  

- Small, Large, ExtraLarge와 같은 클래식 크기는 새 크기 이름(Standard_A*)으로 바뀝니다. 크기 이름은 .csdef 파일에서 새 이름으로 변경해야 합니다. 자세한 내용은 [Cloud Services(확장 지원) 배포 필수 구성 요소](deploy-prerequisite.md#required-service-definition-file-csdef-updates)를 참조하세요.

- 가져오기 API를 사용하여 배포 파일의 최신 복사본을 가져옵니다. 
    - [Portal](../azure-resource-manager/templates/export-template-portal.md), [PowerShell](../azure-resource-manager/management/manage-resource-groups-powershell.md#export-resource-groups-to-templates), [CLI](../azure-resource-manager/management/manage-resource-groups-cli.md#export-resource-groups-to-templates) 및 [Rest API](/rest/api/resources/resourcegroups/exporttemplate)를 사용하여 템플릿을 가져옵니다. 
    - [PowerShell](/powershell/module/az.cloudservice/?preserve-view=true&view=azps-5.4.0#cloudservice) 또는 [Rest API](/rest/api/compute/cloudservices/rest-get-package)를 사용하여 .csdef 파일을 가져옵니다. 
    - [PowerShell](/powershell/module/az.cloudservice/?preserve-view=true&view=azps-5.4.0#cloudservice) 또는 [Rest API](/rest/api/compute/cloudservices/rest-get-package)를 사용하여 .cscfg 파일을 가져옵니다. 
    
 

## <a name="changes-to-customers-automation-cicd-pipeline-custom-scripts-custom-dashboards-custom-tooling-etc"></a>고객의 자동화, CI/CD 파이프라인, 사용자 지정 스크립트, 사용자 지정 대시보드, 사용자 지정 도구 등에 대한 변경 내용  

고객은 새 API/명령을 사용하여 배포를 관리하기 위해 도구 및 자동화를 업데이트해야 합니다. 고객은 이 변경의 일환으로 Azure Resource Manager/Cloud Services(확장 지원)의 새로운 특징과 기능을 쉽게 채택할 수 있습니다. 

- 마이그레이션 후 리소스 및 리소스 그룹 이름 변경
    - 마이그레이션의 일환으로 Cloud Service, 공용 IP 주소 등과 같은 몇 가지 리소스 이름이 변경됩니다. Cloud Service를 업데이트하기 전에 이러한 변경 내용을 배포 파일에 반영해야 할 수 있습니다. [변경되는 리소스 이름에 대해 자세히 알아보세요](in-place-migration-technical-details.md#translation-of-resources-and-naming-convention-post-migration).  

- 클라우드 서비스를 관리하고 크기를 조정하는 데 필요한 규칙 및 정책 다시 만들기 
    - [자동 크기 조정 규칙](configure-scaling.md)은 마이그레이션되지 않습니다. 마이그레이션 후 자동 크기 조정 규칙을 다시 만듭니다.  
    - [경고](enable-alerts.md)는 마이그레이션되지 않습니다. 마이그레이션 후 경고를 다시 만듭니다.
    - Key Vault는 액세스 정책 없이 만들어집니다. Key Vault에서 [적절한 정책을 만들어](../key-vault/general/assign-access-policy-portal.md) 인증서를 보거나 관리합니다. 인증서는 비밀 탭의 설정 아래에 표시됩니다.


## <a name="changes-to-certificate-management-post-migration"></a>마이그레이션 후 인증서 관리 변경 내용 

인증서를 관리하는 표준 방법으로 유효한 모든 .pfx 인증서 파일을 Key Vault의 인증서 저장소에 추가해야 하며, 업데이트는 모든 클라이언트, 즉 포털, Powershell 또는 Rest API를 통해 완벽하게 작동합니다.

현재 Azure Portal에서는 필요한 인증서가 모두 Key Vault의 인증서 저장소에 업로드되었는지 확인하고, 인증서가 없는 경우 경고를 표시합니다. 그러나 인증서를 암호로 사용 하려는 경우에는 해당 지문에 대해 이러한 인증서의 유효성을 검사할 수 없으며, 암호 추가를 포함 하는 모든 업데이트 작업이 포털을 통해 실패 합니다. 고객은 PowerShell 또는 RestAPI를 사용하여 암호와 관련된 업데이트를 계속 진행하는 것이 좋습니다.


## <a name="changes-for-update-via-visual-studio"></a>Visual Studio를 통한 업데이트의 변경 내용
Visual Studio를 통해 직접 업데이트를 게시하는 경우 먼저 배포 후 마이그레이션에서 최신 CSCFG 파일을 다운로드해야 합니다. 이 파일을 참조로 사용하여 Visual Studio 프로젝트의 현재 CSCFG 파일에 네트워크 구성 정보를 추가합니다. 그런 다음 솔루션을 빌드하고 게시합니다. 이 업데이트에 대한 Key Vault 및 리소스 그룹을 선택해야 할 수도 있습니다.


## <a name="next-steps"></a>다음 단계
- [클래식에서 Azure Resource Manager로 IaaS 리소스 플랫폼 지원 마이그레이션 개요](../virtual-machines/migration-classic-resource-manager-overview.md)
- [Azure Portal](in-place-migration-portal.md)을 사용하여 Cloud Services(확장 지원)로 마이그레이션
- [PowerShell](in-place-migration-powershell.md)을 사용하여 Cloud Services(확장 지원)로 마이그레이션
