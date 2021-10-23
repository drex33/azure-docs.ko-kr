---
title: Azure VM(클래식)은 2023년 3월 1일부로 사용이 중지됩니다.
description: 본 문서에서는 클래식 배포 모델을 사용하여 생성된 VM의 사용 중지에 대한 중요한 개요 사항을 제공합니다.
author: tanmaygore
manager: vashan
ms.service: virtual-machines
ms.subservice: classic-to-arm-migration
ms.workload: infrastructure-services
ms.topic: conceptual
ms.date: 02/10/2020
ms.author: tagore
ms.openlocfilehash: 93b66a1cd8aa6f39cd12284d0a7bd3234d9b8ef5
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130225068"
---
# <a name="migrate-your-iaas-resources-to-azure-resource-manager-by-march-1-2023"></a>IaaS 리소스를 2023년 3월 1일까지 Azure Resource Manager로 마이그레이션합니다. 

**적용 대상:** :heavy_check_mark: Linux VMs :heavy_check_mark: Windows VM

2014년에 [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/)에서 IaaS(서비스로써의 인프라)를 시작했습니다. 그때부터 계속해서 기능을 발전해 왔습니다. 이제 Azure Resource Manager가 모든 IaaS 기능과 기타 고급 기능을 갖추고 있기 때문에 [ASM](./migration-classic-resource-manager-faq.yml)(Azure Service Manager)을 통한 IaaS VM(가상 머신)의 관리는 2020년 2월 28일부로 사용되지 않습니다. 이 기능은 2023년 3월 1일부로 완전히 사용 중지됩니다. 

현재 IaaS VM의 90%가량이 Azure Resource Manager를 사용하고 있습니다. ASM을 통해 IaaS 리소스를 사용하고 계시다면 이제 마이그레이션 계획을 시작하세요. [Azure Resource Manager](../azure-resource-manager/management/index.yml)를 활용하려면 2023년 3월 1일까지 이를 완료하세요.

클래식 배포 모델을 활용하여 제작된 VM은 사용 중지와 관련된 [최신 수명 주기 정책](https://support.microsoft.com/help/30881/modern-lifecycle-policy)을 따릅니다.

## <a name="how-does-this-affect-me"></a>이 변경 사항은 어떤 영향을 미치나요? 

- 2020년 2월 28일부로 2020년 2월에 ASM을 통해 IaaS VM을 사용하지 않은 고객들은 더 이상 VM(클래식)을 만들 수 없습니다. 
- 2023년 3월 1일부터 고객들은 ASM을 통해 IaaS VM을 시작할 수 없습니다. 실행 중이거나 할당되어 있는 것들은 모두 중지되며 할당이 취소됩니다. 
- 2023년 3월 1일부로 Azure Resource Manager로 마이그레이션하지 않은 구독에는 나머지 VM(클래식) 삭제에 대한 타임라인 정보가 표시됩니다.  

본 사용 중지는 다음의 Azure 서비스 및 기능에는 영향을 주지 *않습니다*. 
- VM(클래식)이 사용하지 *않는* 스토리지 계정 
- VM(클래식)이 사용하지 *않는* 가상 네트워크 
- 기타 클래식 리소스

Azure Cloud Services(클래식) 중지는 2021년 8월에 [발표되었습니다.](https://azure.microsoft.com/updates/cloud-services-retirement-announcement/)

## <a name="what-resources-are-available-for-this-migration"></a>이번 마이그레이션에 사용할 수 있는 리소스는 무엇인가요?

- [Microsoft Q&A](/answers/topics/azure-virtual-machines-migration.html): 마이그레이션 관련 Microsoft 및 커뮤니티 지원.

- [Azure 마이그레이션 지원](https://ms.portal.azure.com/#create/Microsoft.Support/Parameters/{"pesId":"6f16735c-b0ae-b275-ad3a-03479cfa1396","supportTopicId":"1135e3d0-20e2-aec5-4ef0-55fd3dae2d58"}): 마이그레이션 기간의 기술 지원을 위한 전용 지원팀. 기술 지원을 받지 못한 고객은 이번 마이그레이션을 위해 특별히 제공되는 [무료 지원 기능](https://ms.portal.azure.com/#create/Microsoft.Support/Parameters/%7B%0A%20%20%20%20%22pesId%22%3A%20%22f3dc5421-79ef-1efa-41a5-42bf3cbb52c6%22%2C%0A%20%20%20%20%22supportTopicId%22%3A%20%22794bb734-af1b-e2d5-a757-dac7438009ab%22%2C%0A%20%20%20%20%22contextInfo%22%3A%20%22Migrate%20IAAS%20resources%20from%20Classic%20%28ASM%29%20to%20Azure%20Resource%20Manager%20%28ARM%29%22%2C%0A%20%20%20%20%22caller%22%3A%20%22NoSupportPlanASM2ARM%22%2C%0A%20%20%20%20%22severity%22%3A%20%222%22%0A%7D)을 사용할 수 있습니다. 

- [Microsoft 패스트 트랙](https://www.microsoft.com/fasttrack): 이번 마이그레이션과 관련해 자격 있는 고객이 계획을 세워 실행하는 것을 패스트 트랙이 지원할 수 있습니다. DC 마이그레이션 프로그램에 [자신을 추천하세요](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fprograms%2Fazure-fasttrack%2F%23nomination&data=02%7C01%7CTanmay.Gore%40microsoft.com%7C3e75bbf3617944ec663a08d85c058340%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637360526032558561&sdata=CxWTVQQPVWNwEqDZKktXzNV74pX91uyJ8dY8YecIgGc%3D&reserved=0).  

- Microsoft 파트너이거나 Microsoft 담당자(CSA(클라우드 솔루션 설계자) 또는 TAM(기술 계정 관리자) 등)와 협업 중인 회사/조직의 경우에는 마이그레이션 관련 추가 리소스에 대해 이들과 함께 작업하세요.

## <a name="what-actions-should-i-take"></a>어떤 작업을 수행해야 하나요? 

지금 Azure Resource Manager로의 마이그레이션 계획을 시작합니다. 

1. 다음과 같이 영향을 받는 모든 VM의 목록을 만듭니다. 

   - [Azure Portal의 VM 창](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.ClassicCompute%2FVirtualMachines)에 표시되는 **가상 머신(클래식)** 형식의 VM은 모두 구독 기간에 영향을 받는 VM입니다. 
   - 플래그가 지정된 VM(클래식) 전체 목록 및 선택된 구독 관련 정보를 확인하기 위해 [포털](https://portal.azure.com/#blade/HubsExtension/ArgQueryBlade/query/resources%0A%7C%20where%20type%20%3D%3D%20%22microsoft.classiccompute%2Fvirtualmachines%22)이나 [PowerShell](../governance/resource-graph/concepts/work-with-data.md)을 사용하여 Azure Resource Graph에 쿼리할 수도 있습니다. 
   - 구독 중인 고객들에게 2020년 2월 8일과 9월 2일에 "IaaS VM을 Azure Resource Manager로 마이그레이션 하기 위한 계획을 시작하세요"라는 제목의 메일을 보냈습니다. 메일을 통해 전체 구독 목록 및 포함된 VM(클래식) VM을 제공했습니다. 목록 작성에 사용하세요. 

1. [Linux](./migration-classic-resource-manager-plan.md) 및 [Windows](./migration-classic-resource-manager-plan.md) VM(클래식)을 Azure Resource Manager로 마이그레이션하는 데 대해 [자세히 알아보세요](./migration-classic-resource-manager-overview.md). 자세한 내용은 [클래식에서 Azure Resource Manager로의 마이그레이션 관련 자주 묻는 질문](./migration-classic-resource-manager-faq.yml)을 확인하세요.

1. 계획을 시작할 때는 [플랫폼 지원 마이그레이션 도구](./migration-classic-resource-manager-overview.md)를 활용하여 기존의 VM을 마이그레이션할 때 유효성 검사, 준비, 커밋의 세 단계를 따라 손쉽게 진행할 수 있도록 하는 것이 좋습니다. 해당 도구는 가동 중지 시간이 거의 없도록 VM을 마이그레이션할 수 있게 설계되었습니다. 

   - 첫 번째 단계인 유효성 검사는 기존 배포에 영향을 주지 않으며 마이그레이션 지원을 받지 않는 시나리오의 전체 목록을 제공합니다. 
   - [해결 방법 목록](./migration-classic-resource-manager-overview.md#unsupported-features-and-configurations)을 통해 배포를 수정하고 마이그레이션 준비를 할 수 있습니다. 
   - 이상적으로는 유효성 오류를 모두 수정하고 나면 준비 및 커밋 단계에서는 아무런 문제가 없어야 합니다. 커밋을 성공적으로 완료한 뒤에는 배포한 내용이 실시간으로 Azure Resource Manager에 마이그레이션되고, 이후 Azure Resource Manager가 노출하는 새로운 API를 통해 관리할 수 있습니다. 

   마이그레이션 도구가 마이그레이션에 적합하지 않은 경우, 마이그레이션을 위해 [기타 컴퓨팅 제품](/azure/architecture/guide/technology-choices/compute-decision-tree)을 살펴볼 수 있습니다. Azure Compute는 제품군이 다양하므로 이들에 대해 플랫폼에서 지원하는 마이그레이션 경로를 제공할 수는 없습니다.  

1. 허용 목록에 구독을 추가하는 것과 관련된 기술 관련 질문, 문제, 도움말을 확인하려면 [고객 지원팀에 문의](https://ms.portal.azure.com/#create/Microsoft.Support/Parameters/{"pesId":"6f16735c-b0ae-b275-ad3a-03479cfa1396","supportTopicId":"8a82f77d-c3ab-7b08-d915-776b4ff64ff4"})하세요.

1. 마이그레이션을 최대한 빨리 완료하여 사업에 미치는 영향을 막고 Azure Resource Manager의 향상된 성능, 보안 및 새로운 기능을 활용하세요.
