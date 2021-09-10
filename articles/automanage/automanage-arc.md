---
title: Arc 지원 서버용 Azure Automanage
description: Arc 지원 서버용 Azure Automanage에 대해 알아봅니다
author: asinn826
ms.service: virtual-machines
ms.subservice: automanage
ms.collection: linux
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 06/24/2021
ms.author: alsin
ms.openlocfilehash: 87008ec2ba23e642ecbbe1e91c7f87d0cc07012f
ms.sourcegitcommit: a038863c0a99dfda16133bcb08b172b6b4c86db8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/29/2021
ms.locfileid: "113018732"
---
# <a name="azure-automanage-for-machines-best-practices---arc-enabled-servers"></a>컴퓨터용 Azure Automanage 모범 사례 - Arc 지원 서버

Arc 지원 서버 VM에서 Automanage Machine 모범 사례를 사용하는 경우 이러한 Azure 서비스가 자동으로 온보딩됩니다. 이는 [클라우드 채택 프레임워크](/azure/cloud-adoption-framework/manage/azure-server-management)에서 찾을 수 있는 모범 사례 백서에 필수적입니다.

이러한 모든 서비스에 대해 자동으로 온보딩하고, 자동 구성하고, 드리프트가 있는지 모니터링하고 드리프트가 검색되면 수정합니다. 이 프로세스에 대해 자세히 알아보려면 [가상 머신에 대한 Azure Automanage](automanage-virtual-machines.md)를 참조하세요.

## <a name="supported-operating-systems"></a>지원되는 운영 체제

Automanage는 Arc 지원 서버에 대해 다음 운영 체제를 지원합니다

- Windows Server 2012/R2
- Windows Server 2016
- Windows Server 2019
- CentOS 7.3+, 8
- RHEL 7.4+, 8
- Ubuntu 16.04 및 18.04
- SLES 12(SP3-SP5 전용)

## <a name="participating-services"></a>참여 서비스

|서비스    |설명    |지원되는 환경 <sup>1</sup>    |지원되는 기본 설정 <sup>1</sup>    |
|-----------|---------------|----------------------|-------------------------|
|[컴퓨터 인사이트 모니터링](../azure-monitor/vm/vminsights-overview.md)    |컴퓨터용 Azure Monitor는 실행 중인 프로세스와 다른 리소스에 대한 종속성을 비롯하여 가상 머신의 성능과 상태를 모니터링합니다. [자세히](../azure-monitor/vm/vminsights-overview.md) 알아봅니다.    |프로덕션    |아니요    |
|[Azure Security Center](../security-center/security-center-introduction.md)    |Azure Security Center는 데이터 센터의 보안 상태를 강화하는 통합 인프라 보안 관리 시스템으로, 클라우드의 하이브리드 워크로드에서 Advanced Threat Protection을 제공합니다. [자세히](../security-center/security-center-introduction.md) 알아봅니다.  Automanage는 VM이 Azure Security Center의 체험 계층 제품으로 상주하는 구독을 구성합니다. 구독이 이미 Azure Security Center에 등록된 경우 Automanage는 해당 구독을 다시 구성하지 않습니다.    |프로덕션, 개발/테스트    |아니요    |
|[업데이트 관리](../automation/update-management/overview.md)    |Azure Automation의 업데이트 관리를 사용하여 컴퓨터 운영 체제 업데이트를 관리합니다. 모든 에이전트 머신에서 사용 가능한 업데이트의 상태를 신속하게 평가하고 서버의 필수 업데이트를 설치하는 프로세스를 관리할 수 있습니다. [자세히](../automation/update-management/overview.md) 알아봅니다.    |프로덕션, 개발/테스트    |아니요    |
|[변경 내용 추적 및 인벤토리](../automation/change-tracking/overview.md) |변경 내용 추적 및 인벤토리는 변경 추적 및 인벤토리 기능을 결합하여 가상 머신 및 서버 인프라 변경 내용을 추적할 수 있도록 합니다. 이 서비스는 사용자 환경에서 서비스, 디먼, 소프트웨어, 레지스트리 및 파일에 변경한 내용을 추적하는 기능을 지원하기 때문에 원하지 않는 변경 내용을 진단하고 경고를 생성할 수 있습니다. 인벤토리 지원을 사용하면 게스트 내 리소스를 쿼리하여 설치된 애플리케이션 및 기타 구성 항목을 볼 수 있습니다.  [자세히](../automation/change-tracking/overview.md) 알아봅니다.    |프로덕션, 개발/테스트    |아니요    |
|[Azure 게스트 구성](../governance/policy/concepts/guest-configuration.md)  | 게스트 구성 정책은 머신의 규정 준수에 대한 구성 및 보고서를 모니터링하는 데 사용됩니다. Automanage 서비스는 게스트 구성 확장을 사용하여 Azure Linux 기준을 설치합니다. Linux 머신의 경우 게스트 구성 서비스는 감사 전용 모드에서 기준선을 설치합니다. VM이 기준을 준수하지 않는 위치를 확인할 수 있지만 비규격은 자동으로 수정되지 않습니다. [자세히](../governance/policy/concepts/guest-configuration.md) 알아봅니다.    |프로덕션, 개발/테스트    |아니요    |
|[Azure Automation 계정](../automation/automation-create-standalone-account.md)    |Azure Automation은 인프라와 애플리케이션의 수명 주기 전반에 걸친 관리를 지원합니다. [자세히](../automation/automation-intro.md) 알아봅니다.    |프로덕션, 개발/테스트    |아니요    |
|[Log Analytics 작업 영역](../azure-monitor/logs/log-analytics-overview.md) |Azure Monitor는 Azure 리소스이자 데이터가 수집 및 집계되는 컨테이너로, 관리 경계로 기능하는 Log Analytics 작업 영역에 로그 데이터를 저장합니다. [자세히](../azure-monitor/logs/design-logs-deployment.md) 알아봅니다.    |프로덕션, 개발/테스트    |아니요    |


<sup>1</sup> 환경 선택은 Automanage를 사용하도록 설정할 때 사용할 수 있습니다. [자세히](automanage-virtual-machines.md#environment-configuration) 알아봅니다. 환경의 기본 설정을 조정하고 모범 사례 제약 조건 내에서 고유한 기본 설정을 지정할 수도 있습니다.


## <a name="next-steps"></a>다음 단계

Azure Portal에서 컴퓨터용 Automanage를 사용해 보세요.

> [!div class="nextstepaction"]
> [Azure Portal에서 컴퓨터용 Automanage 사용](quick-create-virtual-machines-portal.md)