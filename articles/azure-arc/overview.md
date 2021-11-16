---
title: Azure Arc 개요
description: Azure Arc란 무엇이며 고객이 다른 Azure 서비스 및 기능을 사용하여 하이브리드 리소스의 관리 및 거버넌스를 활성화하는 데 어떤 도움이 되는지 알아봅니다.
ms.date: 05/25/2021
ms.topic: overview
ms.openlocfilehash: 62395b53fea3f86e42726f1bb2d35b12d8a0bdca
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132345741"
---
# <a name="azure-arc-overview"></a>Azure Arc 개요

오늘날 기업은 점점 더 복잡해지는 환경을 제어하고 관리하는 데 어려움을 겪고 있습니다. 이러한 환경은 데이터 센터, 여러 클라우드 및 에지 간에 확장됩니다. 각 환경 및 클라우드에는 학습하고 운영하는 데 필요한 자체 분리된 관리 도구 세트가 있습니다.

기존 도구가 새로운 클라우드 네이티브 패턴에 대한 지원을 제공하지 않으므로 새 DevOps 및 ITOps 운영 모델은 구현하기 어렵습니다.

Azure Arc는 일관적인 다중 클라우드 및 온-프레미스 관리 플랫폼을 제공하여 거버넌스 및 관리를 간소화합니다. Azure Arc를 사용하면 다음을 수행할 수 있습니다.
* 기존 비 Azure, 온-프레미스 또는 기타 클라우드 리소스를 Azure Resource Manager로 프로젝션하여 단일 창으로 전체 환경을 관리합니다. 
* Azure에서 실행되는 것처럼 가상 머신, Kubernetes 클러스터 및 데이터베이스를 관리합니다. 
* 실제 위치에 관계없이 친숙한 Azure 서비스 및 관리 기능을 사용합니다. 
* 기존 ITOps를 계속 사용하는 동시에 사용자 환경에서 새로운 클라우드 기본 패턴을 지원하기 위한 DevOps 사례를 도입합니다.
* Azure Arc 지원 Kubernetes 클러스터, 클러스터 연결 및 클러스터 확장을 기반으로 사용자 지정 위치를 추상화 계층으로 구성합니다.  

:::image type="content" source="./media/overview/azure-arc-control-plane.png" alt-text="Azure Arc 관리 컨트롤 플레인 다이어그램" border="false":::

현재 Azure Arc를 사용하면 Azure 외부에서 호스트되는 다음 리소스 종류를 관리할 수 있습니다.

* 서버 - Windows 또는 Linux를 실행하는 물리적 머신 및 가상 머신
* Kubernetes 클러스터 - 여러 Kubernetes 배포 지원
* Azure 데이터 서비스 - Azure SQL Managed Instance 및 PostgreSQL 하이퍼스케일 서비스
* SQL Server - [Azure Arc 지원 서버에서 SQL Server](/sql/sql-server/azure-arc/overview)를 사용하여 모든 위치에서 인스턴스를 등록합니다.

## <a name="what-does-azure-arc-deliver"></a>Azure Arc는 어떤 기능을 제공하나요?

Azure Arc의 핵심 기능은 다음과 같습니다.

* 사용자 환경에서 서버에 대한 일관된 인벤토리, 관리, 거버넌스 및 보안을 구현합니다.

* Azure 관리 서비스를 사용하여 서버를 모니터링하고, 보안을 설정하고, 업데이트하도록 [Azure VM 확장](./servers/manage-vm-extensions.md)을 구성합니다.

* 대규모로 Kubernetes 클러스터를 관리하고 제어합니다.

* GitOps를 사용하여 Git 리포지토리에서 하나 이상의 클러스터에 구성을 배포합니다.

*  Azure Policy를 사용하여 Kubernetes 클러스터에 대한 제로 터치 규정 준수 및 구성.

* Azure에서 실행되는 것처럼 모든 Kubernetes 환경에서 [Azure 데이터 서비스](../azure-arc/kubernetes/custom-locations.md)를 실행합니다(특히 업그레이드, 업데이트, 보안 및 모니터링과 같은 이점이 있는 Azure SQL Managed Instance 및 Azure Database for PostgreSQL Hyperscale). Azure에 대한 지속적인 연결 없이도, 탄력적인 스케일링을 사용하고 애플리케이션 다운타임 없이 업데이트를 적용합니다.

* Azure 서비스 인스턴스를 배포하기 위한 대상 위치로 사용하여 [Azure Arc 지원 Kubernetes](./kubernetes/overview.md) 클러스터를 기반으로 [사용자 지정 위치](./kubernetes/custom-locations.md)를 만듭니다. [Azure Arc 지원 Data Services](./data/create-data-controller-direct-azure-portal.md), [Azure Arc의 App Services](../app-service/overview-arc-integration.md)(웹, 함수 및 논리 앱 포함) 및 [Kubernetes의 Event Grid](../event-grid/kubernetes/overview.md)에 대한 Azure 서비스 클러스터 확장을 배포합니다.

* Azure Portal, Azure CLI, Azure PowerShell 또는 Azure REST API를 사용하는지에 관계없이 Azure Arc 지원 리소스를 보는 통합 환경입니다.

## <a name="how-much-does-azure-arc-cost"></a>Azure Arc 비용은 얼마입니까?

다음은 현재 Azure Arc에서 사용 가능한 기능에 대한 가격 정보입니다.

### <a name="azure-arc-enabled-servers"></a>Azure Arc 지원 서버

다음 Azure Arc 컨트롤 플레인 기능은 추가 비용 없이 제공됩니다.

* Azure 관리 그룹 및 태그 태그를 통한 리소스 구성.

* Azure Resource Graph를 통한 검색 및 인덱싱.

* Azure RBAC 및 구독을 통한 액세스 및 보안.

* 템플릿과 확장을 통한 환경 및 자동화.

* 업데이트 관리.

Microsoft Defender for Cloud 또는 Azure Monitor와 같이 Azure Arc 지원 서버에서 사용되는 모든 Azure 서비스는 해당 서비스에 대한 가격 책정에 따라 요금이 청구됩니다. 자세한 내용은 [Azure 가격 책정 페이지](https://azure.microsoft.com/pricing/)를 참조하세요.

### <a name="azure-arc-enabled-kubernetes"></a>Azure Arc 지원 Kubernetes

Microsoft Defender for Cloud 또는 Azure Monitor와 같이 Azure Arc 지원 Kubernetes에서 사용되는 모든 Azure 서비스는 해당 서비스에 대한 가격 책정에 따라 요금이 청구됩니다. Azure Arc 지원 Kubernetes의 상단에 있는 구성 가격 책정에 대한 자세한 내용은 [Azure 가격 책정 페이지](https://azure.microsoft.com/pricing/)를 참조하세요.

### <a name="azure-arc-enabled-data-services"></a>Azure Arc 지원 데이터 서비스

자세한 내용은 [Azure 가격 책정 페이지](https://azure.microsoft.com/pricing/)를 참조하세요.

## <a name="next-steps"></a>다음 단계

* Azure Arc 지원 서버에 대한 자세한 내용은 다음 [개요](./servers/overview.md)를 참조하세요.

* Azure Arc 지원 Kubernetes에 대한 자세한 내용은 다음 [개요](./kubernetes/overview.md)를 참조하세요.

* Azure Arc 지원 데이터 서비스에 대한 자세한 내용은 다음 [개요](https://azure.microsoft.com/services/azure-arc/hybrid-data-services/)를 참조하세요.

* Azure Arc 지원 서버의 SQL Server에 대해 자세히 알아보려면 다음 [개요](/sql/sql-server/azure-arc/overview)를 참조하세요.

* [Jumpstart 개념 증명](https://azurearcjumpstart.io/azure_arc_jumpstart/)에서 Azure Arc 지원 서비스를 경험하세요.
