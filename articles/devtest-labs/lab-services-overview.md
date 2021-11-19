---
title: Azure Lab Services 및 Azure DevTest Labs
description: Azure DevTest Labs와 Azure Lab Services의 기능, 시나리오 및 사용 사례를 비교합니다.
ms.topic: overview
ms.date: 11/15/2021
ms.openlocfilehash: 9ae4061713cbb9dd2863ee4f7c236c1474aae4ee
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2021
ms.locfileid: "132722243"
---
# <a name="compare-azure-devtest-labs-and-azure-lab-services"></a>Azure DevTest Labs와 Azure Lab Services 비교

두 가지 Azure 서비스를 사용하여 클라우드에서 랩 환경을 설정할 수 있습니다.

- [Azure DevTest Labs](https://azure.microsoft.com/services/devtest-lab)는 팀을 위한 개발 또는 테스트 클라우드 환경을 제공합니다.

  DevTest Labs에서 랩 소유자는 [랩을 만들고](devtest-lab-create-lab.md) 랩 사용자가 사용할 수 있도록 합니다. 소유자는 필요한 모든 소프트웨어 및 도구가 있는 Windows 또는 Linux VM(가상 머신)을 사용하여 랩을 프로비전합니다. 랩 사용자는 일상적인 작업 및 단기 프로젝트를 위해 랩 VM에 연결합니다. 랩 관리자는 여러 랩에서 리소스 사용량 및 비용을 분석하고 조직 또는 팀 비용을 최적화하는 가장 중요한 정책을 설정할 수 있습니다.

- [Azure Lab Services](https://azure.microsoft.com/services/lab-services)는 관리되는 클래스룸 랩을 제공합니다.

  Lab Services는 VM 회전 및 인프라 확장에서 오류 처리에 이르기까지 모든 인프라 관리를 수행합니다. IT 관리자가 Lab Services 랩 계정을 만든 후 강사는 계정에 [클래스룸 랩을 만들 수](/azure/lab-services/how-to-manage-classroom-labs#create-a-classroom-lab) 있습니다. 강사는 클래스에 필요한 VM의 수와 유형을 지정하고 사용자를 클래스에 추가합니다. 사용자가 클래스에 등록하면 VM에 액세스하여 클래스 연습 및 연습을 수행할 수 있습니다.

## <a name="key-capabilities"></a>주요 기능

DevTest Labs 및 Lab Services는 다음과 같은 주요 기능 및 특징을 지원합니다.

- **빠르고 유연한 랩 설정**. 랩 소유자 및 강사는 자신의 요구에 맞게 랩을 신속하게 설정할 수 있습니다. Lab Services는 모든 Azure 인프라 작업을 처리하고 관리되는 랩에 대한 기본 제공 인프라 크기 조정 및 복원력을 제공합니다. DevTest Labs에서 랩 소유자는 인프라를 자체 관리하고 사용자 지정할 수 있습니다.

- **간소화된 랩 사용자 환경**. Lab Services 클래스룸 랩에서 사용자는 코드에 등록하고 랩에 액세스하여 리소스를 사용할 수 있습니다. DevTest Labs 랩 소유자는 VM을 만들고 액세스하며, 데이터 디스크를 관리 및 재사용하고, 재사용 할 수 있는 비밀을 설정하도록 랩 사용자들에게 권한을 부여할 수 있습니다.

- **비용 최적화 및 분석**. Lab Services에서는 각 학생이 제한된 시간 동안 VM을 클래스 학습에 사용할 수 있게 합니다. DevTest Labs 랩 소유자는 랩 일정을 설정하여 랩 VM을 사용자가 액세스할 수 있는 시기를 지정할 수 있습니다. 일정은 지정된 시간에 VM을 자동으로 종료하고 시작할 수 있습니다. 랩 소유자는 사용자당 또는 랩별로 사용 정책을 설정하여 비용을 최적화할 수 있습니다. 랩 소유자는 랩 사용량 및 활동 추세를 분석할 수 있습니다. 클래스룸 랩은 비용 최적화 및 분석 옵션의 더 작은 하위 집합을 제공합니다.

DevTest Labs는 다음 기능도 지원합니다.

- **내장된 보안**. 랩 소유자는 랩에 대한 프라이빗 가상 네트워크와 서브넷을 설정하고 공유되는 공용 IP 주소를 사용할 수 있습니다. DevTest Labs 랩 사용자는 Azure ExpressRoute 또는 사이트 간 VPN(가상 사설망)을 사용하여 가상 네트워크 리소스에 안전하게 액세스할 수 있습니다.

- **워크플로 및 도구 통합**. DevTest Labs에서는 CI/CD(연속 통합/연속 배포) 도구 내에서 환경을 자동으로 프로비전할 수 있습니다. DevTest Labs를 조직의 웹 사이트 및 관리 시스템에 통합할 수 있습니다.

## <a name="scenarios"></a>시나리오

Lab Services 및 DevTest Labs에 대한 일반적인 시나리오는 다음과 같습니다.

### <a name="set-up-a-resizable-classroom-computer-lab-in-the-cloud"></a>크기 조정이 가능한 클래스룸 컴퓨터 랩을 클라우드에 설정

- 관리되는 클래스룸 랩을 만들려면 Lab Services에 필요한 내용을 알려면 됩니다. 이 서비스는 랩 인프라를 만들고 관리하므로 랩의 기술적 세부 사항이 아닌 강의에만 집중할 수 있습니다.
- Lab Services는 학생에게 정확히 필요한 것으로 구성된 VM 랩을 제공합니다. 각 학생이 제한된 시간 동안 VM을 클래스 학습에 사용할 수 있게 합니다.
- 학교의 실제 컴퓨터 랩을 클라우드로 옮길 수 있습니다. Lab Services는 사용자가 설정한 최대 사용량 및 비용 임계값에 따라서만 VM 수를 자동으로 조정합니다.
- 완료되면 한 번의 클릭으로 랩을 삭제할 수 있습니다.

### <a name="use-devtest-labs-for-development-and-test-environments"></a>개발 및 테스트 환경에 DevTest Labs 사용

여러 주요 시나리오에 DevTest Labs를 사용할 수 있습니다. 한 가지 주요 시나리오는 개발 및 테스트 컴퓨터를 호스트하는 것입니다. DevTest Labs는 개발자와 테스터에게 다음과 같은 이점을 제공합니다.

- 랩 소유자와 사용자가 재사용 가능한 템플릿 및 아티팩트를 사용하여 Windows 및 Linux 환경을 빠르게 프로비전할 수 있습니다.
- 개발자는 필요에 따라 개발 컴퓨터를 신속하게 프로비전하고 필요한 경우 컴퓨터를 쉽게 사용자 지정할 수 있습니다.
- 테스터는 최신 애플리케이션 버전을 테스트하고 여러 테스트 에이전트를 프로비전하여 부하 테스트를 확장할 수 있습니다.
- 관리자는 개발자와 테스터가 필요 이상으로 VM을 차지할 수 없게 하여 비용을 제어할 수 있습니다.
- 관리자는 사용하지 않을 때 VM이 종료되도록 할 수 있습니다.

자세한 내용은 [개발에 DevTest Labs 사용](devtest-lab-developer-lab.md) 및 [테스트에 DevTest Labs 사용](devtest-lab-test-env.md)을 참조하세요.

## <a name="types-of-labs"></a>랩 형식

Lab Services를 통해 **관리되는 랩** 과 DevTest Labs를 통한 **랩** 등, 두 가지 형식의 랩을 만들 수 있습니다. 요구 사항을 입력하는 것으로 서비스 준비를 마치고 필요한 랩 인프라를 관리하려면 Lab Services의 **관리되는 랩 형식** 에서 **클래스룸 랩** 을 선택합니다. 자체 인프라를 관리하려면 DevTest Labs를 사용하여 랩을 만듭니다.

다음 섹션에서는 이러한 랩 형식에 대한 자세한 정보를 제공합니다.

### <a name="managed-labs"></a>관리되는 랩

관리되는 랩은 Azure에서 관리하는 인프라가 있는 Lab Services 랩입니다. 관리되는 랩 유형은 클래스룸 랩과 같은 특정 요구 사항에 적합할 수 있습니다.

관리되는 랩을 사용하면 최소한의 설정으로 바로 시작할 수 있습니다. 클래스룸 랩을 만들려면 먼저 조직에 대한 랩 계정을 만듭니다. 랩 계정은 조직의 모든 랩을 관리하는 중앙 계정 역할을 합니다.

관리되는 랩의 경우 Lab Services는 사용자 고유의 Azure 구독이 아닌 내부 Microsoft 구독에서 Azure 리소스를 만들고 관리합니다. 서비스는 내부 구독에서 리소스 사용량을 추적하고 랩 계정이 포함된 Azure 구독에 사용량을 다시 청구합니다.

다음은 몇 가지 관리되는 랩 형식의 사용 사례입니다.

- 클래스에 정확히 필요한 VM 랩을 학생에게 제공합니다.
- 학생이 VM을 사용할 수 있는 시간을 제한합니다.
- 계산 집약적인 또는 그래픽 집약적인 연구를 수행하기 위한 고성능 컴퓨팅 VM 풀을 설정합니다.
- 학교의 실제 컴퓨터 랩을 클라우드로 옮깁니다.
- hackathon 호스팅용 VM 랩을 신속하게 프로비전합니다.

### <a name="devtest-labs"></a>DevTest Labs

자체 Azure 구독 내에서 모든 랩 인프라 및 구성을 직접 관리할 수 있습니다. 이 시나리오의 경우 Azure Portal DevTest Labs 랩을 만듭니다. DevTest Labs에 대한 랩 계정을 만들거나 사용하지 않습니다.

다음은 DevTest Labs의 몇 가지 사용 사례입니다.

- 해커톤 또는 실습 회의 세션을 호스트하는 VM 랩을 신속하게 프로비전합니다.
- 버그 수정에 사용할 애플리케이션으로 구성된 VM 풀을 만듭니다.
- 개발자에게 필요한 모든 도구로 구성된 VM을 제공합니다.
- 테스트 컴퓨터의 랩을 반복적으로 만들어 최신 비트를 테스트합니다.
- 규모와 성능 테스트를 위해 다르게 구성된 VM 및 여러 테스트 에이전트를 설정합니다.
- 제품의 최신 버전으로 구성된 랩에서 고객 교육 세션을 제공합니다.

## <a name="lab-services-vs-devtest-labs"></a>Lab Services와 DevTest Labs 비교

다음 표에서는 두 가지 유형의 Azure 랩 환경을 비교합니다. 

| 기능 | Lab Services | DevTest Labs |
| -------- | ----------------- | ---------- |
| Azure 인프라 관리 | 서비스가 자동으로 관리 | 사용자가 관리  |
| 인프라 복원력 | 서비스가 자동으로 처리 | 사용자가 처리  |
| 구독 관리 | 서비스가 내부 구독에서 리소스 할당 처리 | 사용자가 자체 Azure 구독에서 관리 |
| 자동 크기 조정. | 서비스가 자동으로 처리 | 자동 크기 조정 없음 |
| Azure Resource Manager 배포 | 사용할 수 없습니다. | 사용 가능. |

## <a name="next-steps"></a>다음 단계

다음 문서를 참조하세요. 

- [클래스룸 랩 정보](../lab-services/classroom-labs-overview.md)
- [DevTest 랩 정보](devtest-lab-overview.md)
