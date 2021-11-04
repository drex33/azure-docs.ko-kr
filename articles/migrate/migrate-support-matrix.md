---
title: Azure Migrate 지원 매트릭스
description: Azure Migrate서비스의 지원 설정 및 제한 사항에 대한 요약을 제공합니다.
author: ms-psharma
ms.author: panshar
ms.manager: abhemraj
ms.topic: conceptual
ms.date: 07/23/2020
ms.openlocfilehash: 02e6e3566f76b279d1911cdaba4b0f474c9cfe9b
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131509356"
---
# <a name="azure-migrate-support-matrix"></a>Azure Migrate 지원 매트릭스

[Azure Migrate 서비스](./migrate-services-overview.md)를 사용하여 서버를 평가하고 Microsoft Azure 클라우드로 마이그레이션할 수 있습니다. 이 문서에는 Azure Migrate 시나리오 및 배포에 대한 일반 지원 설정과 제한 사항이 요약되어 있습니다.

## <a name="supported-assessmentmigration-scenarios"></a>지원되는 평가/마이그레이션 시나리오

다음 표에서는 지원되는 검색, 평가 및 마이그레이션 시나리오를 안내합니다.

**배포** | **세부 정보**
--- | ---
**검색** | 서버 메타데이터 및 동적 성능 데이터를 검색할 수 있습니다.
**소프트웨어 인벤토리** | VMware VM에서 실행되는 앱, 역할 및 기능을 검색할 수 있습니다. 현재 이 기능은 검색으로만 제한됩니다. 평가는 현재 서버 수준에서 지원됩니다. 앱, 역할 또는 기능 기반 평가는 아직 제공되지 않습니다.
**평가** | VMware VM, Hyper-V VM 및 물리적 서버에서 실행되는 온-프레미스 워크로드와 데이터를 평가합니다. Azure Migrate: 검색 및 평가, Microsoft DMA(Data Migration Assistant)와 기타 도구 및 ISV 제품을 사용하여 평가합니다.
**마이그레이션** | 물리적 서버, VMware VM, Hyper-V VM, 물리적 서버 및 클라우드 기반 VMS에서 실행되는 워크로드와 데이터를 Azure로 마이그레이션합니다. Azure Migrate: Server Migration 및 Azure DMS(Database Migration Service)와 기타 도구 및 ISV 제품을 사용하여 마이그레이션합니다.

> [!NOTE]
> 현재 ISV 도구를 사용하여 Azure Government에 있는 Azure Migrate로 데이터를 보낼 수 없습니다. 통합 Microsoft 도구를 사용하거나 개별적으로 파트너 도구를 사용할 수 있습니다.

## <a name="supported-tools"></a>지원되는 도구

아래 표에 구체적인 도구 지원이 안내되어 있습니다.

**도구** | **평가** | **마이그레이션**
--- | --- | ---
Azure Migrate: 검색 및 평가 | [VMware VM](./tutorial-discover-vmware.md), [Hyper-V VM](./tutorial-discover-hyper-v.md) 및 [물리적 서버](./tutorial-discover-physical.md)를 평가합니다. |  해당 없음
Azure Migrate: Server Migration | 해당 없음 | [VMware VM](tutorial-migrate-vmware.md), [Hyper-V VM](tutorial-migrate-hyper-v.md) 및 [물리적 서버](tutorial-migrate-physical-virtual-machines.md)를 마이그레이션합니다.
[Carbonite](https://www.carbonite.com/data-protection-resources/resource/Datasheet/carbonite-migrate-for-microsoft-azure) | 해당 없음 | VMware VM, Hyper-V VM, 물리적 서버 및 기타 클라우드 워크로드를 마이그레이션합니다.
[Cloudamize](https://www.cloudamize.com/platform#tab-0)| VMware VM, Hyper-V VM, 물리적 서버 및 기타 클라우드 워크로드를 평가합니다. | 해당 없음
[Corent Technology](https://go.microsoft.com/fwlink/?linkid=2084928) | VMware VM, Hyper-V VM, 물리적 서버 및 기타 클라우드 워크로드를 평가합니다. |  VMware VM, Hyper-V VM, 물리적 서버 및 퍼블릭 클라우드 워크로드를 마이그레이션합니다.
[Device 42](https://go.microsoft.com/fwlink/?linkid=2097158) | VMware VM, Hyper-V VM, 물리적 서버 및 기타 클라우드 워크로드를 평가합니다.| 해당 없음
[DMA](/sql/dma/dma-overview) | SQL Server 데이터베이스를 평가합니다. | 해당 없음
[DMS](../dms/dms-overview.md) | 해당 없음 | SQL Server, Oracle, MySQL, PostgreSQL 및 MongoDB를 마이그레이션합니다.
[Lakeside](https://go.microsoft.com/fwlink/?linkid=2104908) | VDI(가상 데스크톱 인프라)를 평가합니다. | 해당 없음
[Movere](https://www.movere.io/) | VMWare VM, Hyper-V VM, Xen VM, 물리적 서버, 워크스테이션(VDI 포함) 및 기타 클라우드 워크로드를 평가합니다. | 해당 없음
[RackWare](https://go.microsoft.com/fwlink/?linkid=2102735) | 해당 없음 | VMware VM, Hyper-V VM, Xen VM, KVM VM, 물리적 서버 및 기타 클라우드 워크로드 마이그레이션
[Turbonomic](https://go.microsoft.com/fwlink/?linkid=2094295)  | VMware VM, Hyper-V VM, 물리적 서버 및 기타 클라우드 워크로드를 평가합니다. | 해당 없음
[UnifyCloud](https://go.microsoft.com/fwlink/?linkid=2097195) | VMware VM, Hyper-V VM, 물리적 서버, 기타 클라우드 워크로드 및 SQL Server 데이터베이스를 평가합니다. | 해당 없음
[Webapp Migration Assistant](https://appmigration.microsoft.com/) | 웹앱을 평가합니다. | 웹앱을 마이그레이션합니다.
[Zerto](https://go.microsoft.com/fwlink/?linkid=2157322) | 해당 없음 |  VMware VM, Hyper-V VM, 물리적 서버 및 기타 클라우드 워크로드를 마이그레이션합니다.

## <a name="project"></a>프로젝트

**지원** | **세부 정보**
--- | ---
Subscription | 구독 내에서 여러 프로젝트를 포함할 수 있습니다.
Azure 권한 | 사용자는 프로젝트를 만들기 위해 구독에서 기여자 또는 소유자 권한이 필요합니다.
VMware VM  | 단일 프로젝트에서 최대 35,000개의 VMware VM을 평가합니다.
Hyper-V VM    | 단일 프로젝트에서 최대 35,000개의 Hyper-V VM을 평가합니다.

하나의 프로젝트는 평가 한도까지 VMware VM과 Hyper-V VM을 모두 포함할 수 있습니다.

## <a name="azure-permissions"></a>Azure 권한

Azure Migrate가 Azure에서 작동하도록 하려면 서버의 평가 및 마이그레이션을 시작하기 전에 다음과 같은 권한이 필요합니다.

**Task** | **권한** | **세부 정보**
--- | --- | ---
프로젝트 만들기 | Azure 계정에는 프로젝트를 만들 수 있는 권한이 필요합니다. | [VMware](./tutorial-discover-vmware.md#prepare-an-azure-user-account), [Hyper-V](./tutorial-discover-hyper-v.md#prepare-an-azure-user-account) 또는 [물리적 서버](./tutorial-discover-physical.md#prepare-an-azure-user-account)에 대해 설정합니다.
Azure Migrate 어플라이언스 등록| Azure Migrate는 경량 [Azure Migrate 어플라이언스](migrate-appliance.md)를 사용하여 Azure Migrate: 검색 및 평가로 서버를 검색 및 평가하고 Azure Migrate: Server Migration을 통해 VMware VM의 [에이전트 없는 마이그레이션](server-migrate-overview.md)을 실행합니다. 이 어플라이언스는 서버를 검색하고 해당 메타데이터 및 성능 데이터를 Azure Migrate로 보냅니다.<br/><br/> 등록 과정에서, 구독이 리소스 공급자와 작동할 수 있도록 등록 공급자(Microsoft.OffAzure, Microsoft.Migrate 및 Microsoft.KeyVault)가 어플라이언스에서 선택된 구독에 등록됩니다. 리소스 등록하려면 구독의 기여자 또는 소유자 권한이 필요합니다.<br/><br/> **VMware**-온보딩 과정에서, Azure Migrate는 두 개의 Azure AD(Azure Active Directory) 앱을 만듭니다. 첫 번째 앱은 어플라이언스 에이전트와 Azure Migrate 서비스 간에 통신합니다. 이 앱에는 리소스에 대한 Azure Resource Manager 호출 또는 Azure RBAC 액세스를 수행할 수 있는 권한이 없습니다. 두 번째 앱은 에이전트 없는 VMware VM 마이그레이션만을 위해 사용자 구독에서 생성된 Azure Key Vault에 액세스합니다. 에이전트 없는 마이그레이션에서 Azure Migrate는 구독의 복제 스토리지 계정에 대한 액세스 키를 관리하는 Key Vault를 만듭니다. 어플라이언스에서 검색이 시작되면 (고객 테넌트에 있는) Azure Key Vault에 대한 Azure RBAC 권한을 갖습니다.<br/><br/> **Hyper-V**-온보딩 과정에서, Azure Migrate는 하나의 Azure AD 앱을 만듭니다. 이 앱은 어플라이언스 에이전트와 Azure Migrate 서비스 간에 통신합니다. 이 앱에는 리소스에 대한 Azure Resource Manager 호출 또는 Azure RBAC 액세스를 수행할 수 있는 권한이 없습니다. | [VMware](./tutorial-discover-vmware.md#prepare-an-azure-user-account), [Hyper-V](./tutorial-discover-hyper-v.md#prepare-an-azure-user-account) 또는 [물리적 서버](./tutorial-discover-physical.md#prepare-an-azure-user-account)에 대해 설정합니다.
VMware 에이전트 없는 마이그레이션을 위한 Key Vault 만들기 | 에이전트 없는 Azure Migrate Server Migration을 사용하여 VMware VM을 마이그레이션하기 위해, Azure Migrate는 구독의 복제 스토리지 계정에 대한 액세스 키를 관리하는 Key Vault를 만듭니다. 자격 증명 모음을 만들려면 프로젝트가 있는 리소스 그룹에 대한 권한(소유자 또는 기여자 및 사용자 액세스 관리자)을 설정해야 합니다. | 권한을 [설정](./tutorial-discover-vmware.md#prepare-an-azure-user-account)합니다.

## <a name="supported-geographies-public-cloud"></a>지원되는 지역(퍼블릭 클라우드)

프로젝트는 퍼블릭 클라우드에 있는 여러 지역에서 만들 수 있습니다.

- 프로젝트를 만드는 것은 이들 지역으로 한정되지만, 다른 대상 위치에 있는 서버를 평가하거나 마이그레이션할 수 있습니다.
- 프로젝트 지역은 검색된 메타데이터를 저장하는 데만 사용됩니다.
- 프로젝트를 만들 때 지리를 선택합니다. 프로젝트 및 관련 리소스는 지리에 속한 지역 중 하나에 생성됩니다. 지역은 Azure Migrate 서비스를 통해 할당합니다.

**지리** | **메타데이터 스토리지 위치**
--- | ---
아프리카 | 남아프리카 또는 북아프리카
아시아 태평양 | 동아시아 또는 동남 아시아
오스트레일리아 | 오스트레일리아 동부 또는 오스트레일리아 남동부
브라질 | 브라질 남부
Canada | 캐나다 중부 또는 캐나다 동부
유럽 | 북유럽 또는 서유럽
프랑스 | 프랑스 중부
독일 | 독일 중서부
인도 | 인도 중부 또는 인도 남부
일본 |  일본 동부 또는 일본 서부
Jio 인도 | JIO 인도 서부 
한국 | 한국 중부
노르웨이 | 노르웨이 동부
스위스 | 스위스 북부
아랍에미리트연합국 | 아랍에미리트 북부
United Kingdom | 영국 남부 또는 영국 서부
미국 | 미국 중부 또는 미국 서부 2

> [!NOTE]
> 스위스 지리의 경우 스위스 서부는 REST API 사용자만 사용할 수 있으며 승인된 구독이 필요합니다.

## <a name="supported-geographies-azure-government"></a>지원되는 지역(Azure Government)

**Task** | **지리** | **세부 정보**
--- | --- | ---
프로젝트 만들기 | 미국 | 메타데이터는 US Gov 애리조나, US Gov 버지니아에 저장됩니다.
대상 평가 | 미국 | 대상 지역: US Gov 애리조나, US Gov 버지니아, US Gov 텍사스
대상 복제 | 미국 | 대상 지역: US DoD 중부, US DoD 동부, US Gov 애리조나, US Gov 아이오와, US Gov 텍사스, US Gov 버지니아

## <a name="vmware-assessment-and-migration"></a>VMware 평가 및 마이그레이션

VMware VM에 대한 Azure Migrate: 검색 및 평가와 Azure Migrate: Server Migration 지원 매트릭스를 [검토](migrate-support-matrix-vmware.md)하세요.

## <a name="hyper-v-assessment-and-migration"></a>Hyper-V 평가 및 마이그레이션

Hyper-V VM에 대한 Azure Migrate: 검색 및 평가와 Azure Migrate: Server Migration 지원 매트릭스를 [검토](migrate-support-matrix-hyper-v.md)하세요.

## <a name="azure-migrate-versions"></a>Azure Migrate 버전

Azure Migrate 서비스에는 다음과 같은 두 가지 버전이 있습니다.

- **현재 버전**: 이 버전을 사용하여 새 프로젝트를 만들고, 온-프레미스 평가를 검색하고, 평가 및 마이그레이션을 오케스트레이션할 수 있습니다. [자세히 알아보기](whats-new.md).
- **이전 버전**: 이전 버전의 Azure Migrate(온-프레미스 VMware VM의 평가만 지원됨)를 사용하고 있는 고객은 이제 현재 버전을 사용해야 합니다. 이전 버전에서는 새 프로젝트를 만들거나 새 검색을 수행할 수 없습니다.

## <a name="next-steps"></a>다음 단계

- 마이그레이션을 위해 [VMware VM 평가](./tutorial-assess-vmware-azure-vm.md).
- 마이그레이션을 위해 [Hyper-V VM 평가](tutorial-assess-hyper-v.md).
