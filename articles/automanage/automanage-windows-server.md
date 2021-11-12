---
title: Windows Server용 Azure Automanage
description: Windows Server 컴퓨터에 대해 자동으로 등록 구성 되 고 구성 되는 서비스에 대 한 가상 컴퓨터의 Azure automanage 모범 사례에 대해 알아봅니다.
author: deanwe
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 02/22/2021
ms.author: deanwe
ms.openlocfilehash: 510cb39b6e28ea8a1a723c8a10e86ff9c339cd8d
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132313074"
---
# <a name="azure-automanage-for-machines-best-practices---windows-server"></a>컴퓨터용 Azure Automanage 모범 사례 - Windows Server

이러한 Azure 서비스는 Windows Server VM에서 Automanage 컴퓨터 모범 사례를 사용하면 자동으로 온보딩됩니다. 이는 [클라우드 채택 프레임워크](/azure/cloud-adoption-framework/manage/azure-server-management)에서 찾을 수 있는 모범 사례 백서에 필수적입니다.

이러한 모든 서비스에 대해 자동으로 온보딩하고, 자동 구성하고, 드리프트가 있는지 모니터링하고 드리프트가 검색되면 수정합니다. 자세히 알아보려면 [가상 컴퓨터에 대 한 Azure Automanage](automanage-virtual-machines.md)로 이동 하세요.

## <a name="supported-windows-server-versions"></a>지원되는 Windows Server 버전

Automanage는 다음 Windows Server 버전을 지원합니다.

- Windows Server 2012/R2
- Windows Server 2016
- Windows Server 2019
- Windows Server 2022
- Windows Server 2022 Azure Edition

## <a name="participating-services"></a>참여 서비스

|서비스    |설명    |구성 프로필<sup>1</sup>    |
|-----------|---------------|----------------------|
|[컴퓨터 인사이트 모니터링](../azure-monitor/vm/vminsights-overview.md)    |컴퓨터용 Azure Monitor는 실행 중인 프로세스와 다른 리소스에 대한 종속성을 비롯해 가상 머신의 성능과 상태를 모니터링합니다.    |프로덕션    |
|[Backup](../backup/backup-overview.md)    |Azure Backup은 컴퓨터에서 의도 하지 않은 데이터 소멸 으로부터 보호 하기 위해 독립적인 격리 된 백업을 제공 합니다. 요금은 보호되는 VM 수와 크기를 기준으로 합니다.    |프로덕션    |
|[Microsoft Defender for Cloud](../security-center/security-center-introduction.md)    |Microsoft Defender for Cloud는 데이터 센터의 보안 상태를 강화 하 고 클라우드의 하이브리드 워크 로드에서 고급 위협 방지를 제공 하는 통합 인프라 보안 관리 시스템입니다.  Automanage는 VM이 Microsoft Defender for Cloud의 무료 계층 제공에 상주 하는 구독을 구성 합니다. 구독이 이미 Microsoft Defender for Cloud로 등록 자동 관리를 통해 다시 구성 되지 않습니다.    |프로덕션, 개발/테스트    |
|[Microsoft Antimalware](../security/fundamentals/antimalware.md)    |Azure용 Microsoft 맬웨어 방지 프로그램은 바이러스, 스파이웨어 및 기타 악성 소프트웨어를 식별하고 제거하는 데 도움이 되는 평가판 실시간 보호 기능입니다. 알려진 악성 또는 원치 않는 소프트웨어가 Azure 시스템에서 스스로의 설치나 실행을 시도할 때 경고를 생성합니다. **참고:** Microsoft Antimalware를 사용하려면 다른 맬웨어 방지 소프트웨어가 설치되어 있지 않아야 합니다. 그렇지 않으면 작동하지 않을 수 있습니다. |프로덕션, 개발/테스트    |
|[업데이트 관리](../automation/update-management/overview.md)    |Azure Automation의 업데이트 관리를 사용하여 컴퓨터 운영 체제 업데이트를 관리합니다. 모든 에이전트 머신에서 사용 가능한 업데이트의 상태를 신속하게 평가하고 서버의 필수 업데이트를 설치하는 프로세스를 관리할 수 있습니다.    |프로덕션, 개발/테스트    |
|[변경 내용 추적 및 인벤토리](../automation/change-tracking/overview.md) |변경 내용 추적 및 인벤토리는 변경 추적 및 인벤토리 기능을 결합하여 가상 머신 및 서버 인프라 변경 내용을 추적할 수 있도록 합니다. 이 서비스는 사용자 환경에서 서비스, 디먼, 소프트웨어, 레지스트리 및 파일에 변경한 내용을 추적하는 기능을 지원하기 때문에 원하지 않는 변경 내용을 진단하고 경고를 생성할 수 있습니다. 인벤토리 지원을 사용하면 게스트 내 리소스를 쿼리하여 설치된 애플리케이션 및 기타 구성 항목을 볼 수 있습니다.    |프로덕션, 개발/테스트    |
|[게스트 구성](../governance/policy/concepts/guest-configuration.md) | 게스트 구성 정책은 구성을 모니터링하고 머신의 규정 준수를 보고하는 데 사용됩니다. Automanage 서비스는 게스트 구성 확장을 사용하여 [Windows 보안 기준](/windows/security/threat-protection/windows-security-baselines)을 설치합니다. Windows 컴퓨터의 경우 게스트 구성 서비스는 규정을 준수하지 않을 경우 기준 설정을 자동으로 다시 적용합니다.    |프로덕션, 개발/테스트    |
|[부팅 진단](../virtual-machines/boot-diagnostics.md)    | 부팅 진단은 VM 부팅 실패를 진단할 수 있도록 하는 Azure VM(가상 머신)에 대한 디버깅 기능입니다. 부팅 진단을 통해 사용자는 직렬 로그 정보와 스크린샷을 수집하여 부팅하는 VM의 상태를 관찰할 수 있습니다. 이는 관리 디스크를 사용하는 머신에만 사용할 수 있습니다. |프로덕션, 개발/테스트    |
|[Windows Admin Center](/windows-server/manage/windows-admin-center/azure/manage-vm)    | Azure Portal에서 Windows Admin Center(미리 보기)를 사용하여 Azure VM 내의 Windows Server 운영 체제를 관리합니다. 이 방법은 Windows Server 2016 이상을 사용하는 머신에서만 지원됩니다. Automanage는 개인 IP 주소를 통해 Windows Admin Center를 구성합니다. 공용 IP 주소를 통해 Windows Admin Center에 연결하려면 포트 6516에 대한 인바운드 포트 규칙을 여세요. Automanage가 기본적으로 개발/테스트 프로필에 Windows Admin Center를 온보딩합니다. 기본 설정을 사용하여 프로덕션 및 개발/테스트 환경에 Windows Admin Center를 사용하거나 사용하지 않도록 설정합니다. |프로덕션, 개발/테스트    |
|[Azure Automation 계정](../automation/automation-create-standalone-account.md)    |Azure Automation은 인프라와 애플리케이션의 수명 주기 전반에 걸친 관리를 지원합니다.    |프로덕션, 개발/테스트    |
|[Log Analytics 작업 영역](../azure-monitor/logs/log-analytics-overview.md) |Azure Monitor는 Azure 리소스이자 데이터가 수집 및 집계되는 컨테이너로, 관리 경계로 기능하는 Log Analytics 작업 영역에 로그 데이터를 저장합니다.    |프로덕션, 개발/테스트    |


<sup>1</sup> 자동 관리를 사용 하도록 설정 하는 경우 [구성 프로필](automanage-virtual-machines.md#configuration-profile) 선택을 사용할 수 있습니다. 필요한 Azure 서비스 및 설정 집합을 사용 하 여 사용자 지정 프로필을 만들 수도 있습니다.


## <a name="next-steps"></a>다음 단계

Azure Portal에서 컴퓨터용 Automanage를 사용해 보세요.

> [!div class="nextstepaction"]
> [Azure Portal에서 컴퓨터용 Automanage 사용](quick-create-virtual-machines-portal.md)
