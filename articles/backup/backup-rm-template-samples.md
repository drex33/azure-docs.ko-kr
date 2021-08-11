---
title: Azure 리소스 관리자 템플릿
description: Recovery Services 자격 증명 모음 및 Azure Backup 기능과 함께 사용할 수 있는 Azure Resource Manager 템플릿
ms.topic: sample
ms.date: 01/31/2019
ms.custom: mvc
ms.openlocfilehash: 4e3ef31252956532be38d987ffa40d1581377b1a
ms.sourcegitcommit: 91fdedcb190c0753180be8dc7db4b1d6da9854a1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/17/2021
ms.locfileid: "112293647"
---
# <a name="azure-resource-manager-templates-for-azure-backup"></a>Azure Backup용 Azure Resource Manager 템플릿

다음 표에는 Recovery Services 자격 증명 모음 및 Azure Backup 기능과 함께 사용할 수 있는 Azure Resource Manager 템플릿에 대한 링크가 포함되어 있습니다. JSON 구문 및 속성에 대한 자세한 내용은 [Microsoft.RecoveryServices 리소스 종류](/azure/templates/microsoft.recoveryservices/allversions)를 참조하세요.

| 템플릿 | Description |
|---|---|
|**Recovery Services 자격 증명 모음** | |
| [Recovery Services 자격 증명 모음 만들기](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.recoveryservices/recovery-services-vault-create)| Recovery Services 자격 증명 모음을 만듭니다. 자격 증명 모음은 Azure Backup 및 Azure Site Recovery에 사용할 수 있습니다. |
|**가상 머신 설정**| |
| [Resource Manager VM 백업](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.recoveryservices/recovery-services-backup-vms) | 기존 Recovery Services 자격 증명 모음 및 백업 정책을 사용하여 동일한 리소스 그룹에 있는 Resource Manager 가상 머신을 백업합니다.|
| [Recovery Services 자격 증명 모음에 IaaS VM 백업](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.recoveryservices/recovery-services-backup-classic-resource-manager-vms) | 클래식 및 Resource Manager 가상 머신을 백업하기 위한 템플릿입니다. |
| [IaaS VM용 매주 백업 정책 만들기](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.recoveryservices/recovery-services-weekly-backup-policy-create) | 클래식 및 Resource Manager 가상 머신을 백업하는 데 사용되는 Recovery Services 자격 증명 모음 및 매주 백업 정책을 만드는 템플릿입니다.|
| [IaaS VM용 매일 백업 정책 만들기](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.recoveryservices/recovery-services-daily-backup-policy-create) | 클래식 및 Resource Manager 가상 머신을 백업하는 데 사용되는 Recovery Services 자격 증명 모음 및 매일 백업 정책을 만드는 템플릿입니다.|
| [백업이 활성화된 Windows Server VM 배포](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.recoveryservices/recovery-services-create-vm-and-configure-backup) | 기본 백업 정책이 활성화된 Windows Server VM 및 Recovery Services 자격 증명 모음을 만드는 템플릿입니다.|
|**백업 작업 모니터링** |  |
| [Azure Backup을 통한 Azure Monitor 로그 사용](https://github.com/Azure/azure-quickstart-templates/tree/master/demos/backup-oms-monitoring) | Recovery Services 자격 증명 모음에서 사용된 백업 및 복원 작업, 백업 경고 및 클라우드 스토리지를 모니터링할 수 있는 Azure Backup을 통해 Azure Monitor 로그를 배포하는 템플릿입니다.|
|**Azure VM에서 SQL Server 백업** |  |
| [Azure VM에서 SQL Server 백업](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.recoveryservices/recovery-services-vm-workload-backup) | Recovery Services 자격 증명 모음 및 워크로드 관련 백업 정책을 만드는 템플릿입니다. Azure Backup 서비스에 VM을 등록하고 해당 VM에서 보호를 구성합니다. 현재 SQL 갤러리 이미지에 대해서만 작동합니다. |
|**Azure 파일 공유 백업** |  |
| [Azure 파일 공유 백업](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.recoveryservices/recovery-services-backup-file-share) | 이 템플릿은 Recovery Services 자격 증명 모음 및 백업 정책에 대한 적절한 세부 정보를 지정하여 기존 Azure 파일 공유에 대한 보호를 구성합니다. 필요에 따라 새 Recovery Services 자격 증명 모음 및 백업 정책을 만들고 파일 공유를 포함하는 스토리지 계정을 Recovery Services 자격 증명 모음에 등록합니다. |
|   |   |
