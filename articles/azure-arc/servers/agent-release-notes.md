---
title: Azure Arc 지원 서버 에이전트의 새로운 기능
description: 이 문서에는 Azure Arc 지원 서버 에이전트 릴리스 정보가 있습니다. 요약된 문제 중 다수에 추가 세부 정보로 연결되는 링크가 있습니다.
ms.topic: overview
ms.date: 09/01/2021
ms.custom: references_regions
ms.openlocfilehash: 027d682a6f9727edb7ce39ac1eeea9947b2e4957
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128628537"
---
# <a name="whats-new-with-azure-arc-enabled-servers-agent"></a>Azure Arc 지원 서버 에이전트의 새로운 기능

Azure Arc 지원 서버 Connected Machine 에이전트에는 지속적으로 향상된 기능이 제공됩니다. 최신 개발 정보를 확인할 수 있도록 이 문서에서는 다음에 대한 정보를 제공합니다.

- 최신 릴리스
- 알려진 문제
- 버그 수정

이 페이지는 매월 업데이트되므로 정기적으로 다시 방문해 주세요. 6개월이 넘은 항목을 찾으려는 경우 [Azure Arc 지원 서버 에이전트의 새로운 기능 아카이브](agent-release-notes-archive.md)에서 찾을 수 있습니다.

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

## <a name="june-2021"></a>2021년 6월

버전 1.7

### <a name="new-features"></a>새로운 기능

- 온보딩 중에 안정성 개선:
  - HIMDS를 사용할 수 없을 때 다시 시도 논리 개선
  - OS 정보를 얻을 수 없는 경우 이제 중단하지 않고 온보딩을 계속 진행합니다.
- Red Hat 및 CentOS 시스템에 OMS 에이전트 확장을 설치할 때 안정성 개선

## <a name="may-2021"></a>2021년 5월

버전 1.6

### <a name="new-features"></a>새로운 기능

- SUSE Enterprise Linux 12에 대한 지원이 추가되었습니다
- 게스트 구성 에이전트가 다음을 포함하도록 버전 1.26.12.0으로 업데이트되었습니다.

   - 정책이 별도의 프로세스로 실행됩니다.
   - 확장 유효성 검사에 대한 V2 서명 지원이 추가되었습니다.
   - 데이터 로깅에 대한 사소한 업데이트입니다.

## <a name="next-steps"></a>다음 단계

- 여러 하이브리드 컴퓨터에서 Azure Arc 지원 서버를 평가하거나 사용하기 전에 [Connected Machine 에이전트 개요](agent-overview.md)를 검토하여 요구 사항, 에이전트에 대한 기술 세부 정보 및 배포 방법을 파악합니다.

- [계획 및 배포 가이드](plan-at-scale-deployment.md)를 검토하여 모든 규모의 Azure Arc 지원 서버 배포를 계획하고 중앙 집중식 관리와 모니터링을 구현합니다.
