---
title: 커뮤니티 도구 - 클래식 리소스를 Azure Resource Manager로 이동
description: 이 문서에는 IaaS 리소스를 클래식에서 Azure Resource Manager 배포 모델로 마이그레이션하도록 지원하기 위해 커뮤니티에서 제공해온 도구가 설명되어 있습니다.
author: tanmaygore
manager: vashan
ms.service: virtual-machines
ms.subservice: classic-to-arm-migration
ms.workload: infrastructure-services
ms.topic: conceptual
ms.date: 02/06/2020
ms.author: tagore
ms.openlocfilehash: b7d966967988a17065be16ab37c123f17c8d86b3
ms.sourcegitcommit: 6a3096e92c5ae2540f2b3fe040bd18b70aa257ae
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/17/2021
ms.locfileid: "112323361"
---
# <a name="community-tools-to-migrate-iaas-resources-from-classic-to-azure-resource-manager"></a>클래식에서 Azure Resource Manager로 IaaS 리소스를 마이그레이션하기 위한 커뮤니티 도구

> [!IMPORTANT]
> 현재 IaaS VM의 90% 정도는 [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/)를 사용하고 있습니다. 2020년 2월 28일부터 클래식 VM은 더 이상 사용되지 않으며 2023년 3월 1일에 완전히 사용 중지될 예정입니다. 사용 중단 및 [그 영향](classic-vm-deprecation.md#how-does-this-affect-me)에 대해 [자세히 알아보세요]( https://aka.ms/classicvmretirement).

이 문서에는 IaaS 리소스를 클래식에서 Azure Resource Manager 배포 모델로 마이그레이션하도록 지원하기 위해 커뮤니티에서 제공해온 도구가 설명되어 있습니다.

> [!NOTE]
> 이러한 도구는 Microsoft 지원을 통해 공식적으로 지원되지는 않습니다. 따라서 GitHub에 오픈 소스로 제공되며 수정 사항 또는 추가 시나리오에 대한 PR을 환영합니다. 문제를 보고하려면 GitHub의 문제 기능을 사용합니다.
> 
> 이러한 도구를 사용하여 마이그레이션을 수행하면 클래식 Virtual Machine의 가동이 중지됩니다. 플랫폼에서 지원하는 마이그레이션 방법은 다음 페이지를 참조하세요. 
> 
>   * [클래식에서 Azure Resource Manager 스택으로 IaaS 리소스의 플랫폼 지원 마이그레이션](migration-classic-resource-manager-overview.md)
>   * [클래식에서 Azure Resource Manager로의 플랫폼 지원 마이그레이션에 대한 기술 정보](migration-classic-resource-manager-deep-dive.md)
>   * [Azure PowerShell을 사용하여 클래식에서 Azure Resource Manager로 IaaS 리소스 마이그레이션](migration-classic-resource-manager-ps.md)
> 
> 

## <a name="asmmetadataparser"></a>AsmMetadataParser
Azure 서비스 관리에서 Azure Resource Manager로의 엔터프라이즈 마이그레이션 과정에서 생성되는 도우미 도구의 컬렉션입니다. 이 도구를 사용하면 인프라를 마이그레이션 테스트에 사용할 수 있는 다른 구독으로 복제하고, 프로덕션 구독에서 마이그레이션을 실행하기 전에 문제를 해결할 수 있습니다.

[도구 설명서 링크](https://github.com/Azure/classic-iaas-resourcemanager-migration/tree/master/AsmToArmMigrationApiToolset)

## <a name="migaz"></a>migAz
migAz는 전체 클래식 IaaS 리소스 집합을 Azure Resource Manager IaaS 리소스로 마이그레이션할 수 있는 추가 옵션입니다. 동일한 구독 내에서 또는 서로 다른 구독 및 구독 형식(예: CSP 구독) 간에 마이그레이션을 수행할 수 있습니다.

[도구 설명서 링크](https://github.com/Azure/migAz)

## <a name="next-steps"></a>다음 단계

* [클래식에서 Azure Resource Manager로 IaaS 리소스의 플랫폼 지원 마이그레이션 개요](migration-classic-resource-manager-overview.md)
* [클래식에서 Azure Resource Manager로의 플랫폼 지원 마이그레이션에 대한 기술 정보](migration-classic-resource-manager-deep-dive.md)
* [클래식에서 Azure Resource Manager로 IaaS 리소스의 마이그레이션 계획](migration-classic-resource-manager-plan.md)
* [PowerShell을 사용하여 클래식에서 Azure Resource Manager로 IaaS 리소스 마이그레이션](migration-classic-resource-manager-ps.md)
* [CLI를 사용하여 클래식에서 Azure Resource Manager로 IaaS 리소스 마이그레이션](migration-classic-resource-manager-cli.md)
* [가장 일반적인 마이그레이션 오류 검토](migration-classic-resource-manager-errors.md)
* [클래식에서 Azure Resource Manager로의 IaaS 리소스 마이그레이션과 관련된 가장 자주 묻는 질문 검토](migration-classic-resource-manager-faq.yml)
