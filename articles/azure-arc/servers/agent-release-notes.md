---
title: Azure Arc 지원 서버 에이전트의 새로운 기능
description: 이 문서에는 Azure Arc 지원 서버 에이전트 릴리스 정보가 있습니다. 요약된 문제 중 다수에 추가 세부 정보로 연결되는 링크가 있습니다.
ms.topic: overview
ms.date: 09/01/2021
ms.custom: references_regions
ms.openlocfilehash: 769939162d158ecb8d559fa79125f93af468e394
ms.sourcegitcommit: b00a2d931b0d6f1d4ea5d4127f74fc831fb0bca9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/20/2021
ms.locfileid: "132868797"
---
# <a name="whats-new-with-azure-arc-enabled-servers-agent"></a>Azure Arc 지원 서버 에이전트의 새로운 기능

Azure Connected Machine 에이전트에는 지속적으로 향상된 기능이 제공됩니다. 최신 개발 정보를 확인할 수 있도록 이 문서에서는 다음에 대한 정보를 제공합니다.

- 최신 릴리스
- 알려진 문제
- 버그 수정

이 페이지는 매월 업데이트되므로 정기적으로 다시 방문해 주세요. 6개월이 넘은 항목을 찾으려는 경우 [Azure Arc 지원 서버 에이전트의 새로운 기능 아카이브](agent-release-notes-archive.md)에서 찾을 수 있습니다.

## <a name="november-2021"></a>2021년 11월

버전 1.13

### <a name="fixed"></a>고정

- 에이전트를 설치하거나 업그레이드할 때 안정성이 향상되었습니다.

### <a name="new-features"></a>새로운 기능

- 이제 [azcmagent config 명령](manage-agent.md#config)을 사용하여 에이전트 설정의 로컬 구성을 사용할 수 있습니다.
- 프록시 서버 설정은 환경 변수 대신 [에이전트별 설정을 사용하여 구성](manage-agent.md#update-or-remove-proxy-settings)할 수 있습니다.
- 확장 작업은 새 알림 파이프라인을 사용하여 더 빠르게 실행됩니다. 이 알림 서비스에 대한 새 네트워크 주소를 허용하도록 방화벽 또는 프록시 서버 규칙을 조정해야 할 수도 있습니다([네트워킹 구성](agent-overview.md#networking-configuration) 참조). 확장 관리자는 알림 서비스에 도달할 수 없을 때 5분 간격으로 확인하는 기존 동작으로 대체됩니다.
- Amazon Web Services에서 실행되는 서버에 대한 AWS 계정 ID, 인스턴스 ID 및 지역 정보를 검색합니다.

## <a name="october-2021"></a>2021년 10월

버전 1.12

### <a name="fixed"></a>고정

- 확장 패키지의 서명 유효성을 검사할 때 안정성이 향상되었습니다.
- 이제 Linux의 `azcmagent_proxy remove` 명령이 Red Hat Enterprise Linux 및 관련 배포판에서 환경 변수를 올바르게 제거합니다.
- 이제 `azcmagent logs`에는 이제 로그 파일을 구분하는 데 도움이 되는 컴퓨터 이름과 타임스탬프가 포함되어 있습니다.

## <a name="september-2021"></a>2021년 9월

버전 1.11

### <a name="fixed"></a>고정

- 이제 [시스템 개체: 비 Windows 하위 시스템에 대한 대/소문자 구분 필요](/windows/security/threat-protection/security-policy-settings/system-objects-require-case-insensitivity-for-non-windows-subsystems) 정책을 사용 안 함으로 설정하여 Windows 시스템에 에이전트를 설치할 수 있습니다.
- 이제 게스트 구성 정책 에이전트는 서비스 시작이나 다시 시작 이벤트 중에 오류가 발생하면 자동으로 다시 시도합니다.
- 게스트 구성 감사 정책이 Linux 컴퓨터에서 성공적으로 실행되지 않는 문제를 해결했습니다.

## <a name="august-2021"></a>2021년 8월

버전 1.10

### <a name="fixed"></a>고정

- 이제 게스트 구성 정책 에이전트가 시스템 설정을 구성하고 재구성할 수 있습니다. 기존 정책 할당은 감사 전용으로 계속 유지됩니다. Azure Policy [게스트 구성 수정 옵션](../../governance/policy/concepts/guest-configuration-policy-effects.md)에 대해 자세히 알아보세요.
- 이제 게스트 구성 정책 에이전트가 6시간마다가 아닌 48시간마다 다시 시작됩니다.

## <a name="july-2021"></a>2021년 7월

버전 1.9

## <a name="new-features"></a>새로운 기능

인도네시아어 언어에 대한 지원이 추가됨

### <a name="fixed"></a>고정

미국 서부 3 지역에서 확장 관리를 방지하는 버그를 수정함

버전 1.8

### <a name="new-features"></a>새로운 기능

- Red Hat 및 CentOS 시스템에 Azure Monitor 에이전트 확장을 설치할 때 안정성 개선
- 최대 리소스 이름 길이(54자)의 에이전트 쪽 적용 추가
- 게스트 구성 정책 개선 사항:
  - Linux 운영 체제에서 PowerShell 기반 게스트 구성 정책에 대한 지원 추가
  - 동일한 서버에서 동일한 게스트 구성 정책의 여러 할당에 대한 지원 추가
  - Windows 운영 체제에서 PowerShell Core를 버전 7.1로 업그레이드

### <a name="fixed"></a>고정

- 서비스 시작/중지 이벤트를 Windows 애플리케이션 이벤트 로그에 쓸 수 없는 경우 에이전트는 계속 실행됩니다.

## <a name="next-steps"></a>다음 단계

- 여러 하이브리드 컴퓨터에서 Azure Arc 지원 서버를 평가하거나 사용하기 전에 [Connected Machine 에이전트 개요](agent-overview.md)를 검토하여 요구 사항, 에이전트에 대한 기술 세부 정보 및 배포 방법을 파악합니다.
- [계획 및 배포 가이드](plan-at-scale-deployment.md)를 검토하여 모든 규모의 Azure Arc 지원 서버 배포를 계획하고 중앙 집중식 관리와 모니터링을 구현합니다.
