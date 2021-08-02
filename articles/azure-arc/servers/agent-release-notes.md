---
title: Azure Arc 지원 서버 에이전트의 새로운 기능
description: 이 문서에는 Azure Arc 지원 서버 에이전트 릴리스 정보가 있습니다. 요약된 문제 중 다수에 추가 세부 정보로 연결되는 링크가 있습니다.
ms.topic: conceptual
ms.date: 05/24/2021
ms.openlocfilehash: 35822fb47add5c355ad9461b09e8e910f0a5a1fd
ms.sourcegitcommit: 80d311abffb2d9a457333bcca898dfae830ea1b4
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/26/2021
ms.locfileid: "110480377"
---
# <a name="whats-new-with-azure-arc-enabled-servers-agent"></a>Azure Arc 지원 서버 에이전트의 새로운 기능

Azure Arc 지원 서버 Connected Machine Agent에는 지속적으로 향상된 기능이 제공됩니다. 최신 개발 정보를 확인할 수 있도록 이 문서에서는 다음에 대한 정보를 제공합니다.

- 최신 릴리스
- 알려진 문제
- 버그 수정

## <a name="may-2021"></a>2021년 5월

버전 1.6

### <a name="new-features"></a>새로운 기능

- SUSE Enterprise Linux 12에 대한 지원이 추가되었습니다
- 게스트 구성 에이전트가 다음을 포함하도록 버전 1.26.12.0으로 업데이트되었습니다.

   - 정책이 별도의 프로세스로 실행됩니다.
   - 확장 유효성 검사에 대한 V2 서명 지원이 추가되었습니다.
   - 데이터 로깅에 대한 사소한 업데이트입니다.

## <a name="april-2021"></a>2021년 4월

버전 1.5

### <a name="new-feature"></a>새로운 기능

- Red Hat Enterprise Linux 8 및 CentOS Linux 8에 대한 지원이 추가되었습니다.
- 새 `-useStderr` 매개 변수를 통해 오류 및 자세한 정보 출력을 stderr로 지정합니다.
- 새 `-json` 매개 변수를 통해 출력을 지정하여 JSON 형식이 되도록 합니다(-useStderr과 함께 사용하는 경우).
- 제조업체, 모델, SQL Server 설치 여부(부울), 클러스터 리소스 ID(Azure Stack HCI 노드의 경우) 등 다른 인스턴스 메타데이터를 수집합니다.
 
## <a name="march-2021"></a>2021년 3월

버전 1.4

### <a name="new-feature"></a>새로운 기능

- 현재 제한된 미리 보기로 제공되는 프라이빗 엔드포인트에 대한 지원이 추가되었습니다.
- azcmagent 종료 코드의 확장된 목록이 제공됩니다.
- 이제 `--config` 매개 변수를 사용하여 파일에서 에이전트 구성 매개 변수를 읽을 수 있습니다.

### <a name="fixed"></a>고정

네트워크 엔드포인트 검사가 더 빨라졌습니다.

## <a name="december-2020"></a>2020년 12월

버전: 1.3

### <a name="new-feature"></a>새로운 기능

Windows Server 2008 R2 SP1에 대한 지원이 추가되었습니다.

### <a name="fixed"></a>고정

Linux에서 사용자 지정 스크립트 확장을 설치하지 못하는 문제가 해결되었습니다.

## <a name="november-2020"></a>2020년 11월

버전: 1.2

### <a name="fixed"></a>고정

RPM 기반 배포에서 업그레이드 후 프록시 구성이 손실되는 문제가 해결되었습니다.

## <a name="october-2020"></a>2020년 10월

버전: 1.1

### <a name="fixed"></a>고정

- 대체 GC 디먼 단위 파일 위치를 처리하도록 프록시 스크립트가 수정되었습니다.
- GuestConfig 에이전트 안정성이 변경되었습니다.
- US Gov 버지니아 지역에 대한 GuestConfig 에이전트 지원이 제공됩니다.
- 오류가 발생하는 경우 GuestConfig 에이전트 확장 보고서 메시지가 더 자세히 표시됩니다.

## <a name="september-2020"></a>2020년 9월

버전: 1.0(일반 공급)

### <a name="plan-for-change"></a>변경 계획

- 미리 보기 에이전트(1.0 이전의 모든 버전)에 대한 지원이 향후 서비스 업데이트에서 제거될 예정입니다.
- 대체 엔드포인트 `.azure-automation.net`에 대한 지원이 제거되었습니다. 프록시가 있는 경우 엔드포인트 `*.his.arc.azure.com`을 허용해야 합니다.
- Azure에 호스트된 가상 머신에 Connected Machine Agent가 설치된 경우 Arc 지원 서버 리소스에서 VM 확장을 설치하거나 수정할 수 없습니다. 가상 머신의 **Microsoft.Compute** 및 **Microsoft.HybridCompute** 리소스에서 수행되는 확장 작업의 충돌을 방지하기 위한 것입니다. 모든 확장 작업의 경우 머신에 **Microsoft.Compute** 리소스를 사용합니다.
- 게스트 구성 프로세스 이름이 Linux에서는 *gcd* 에서 *gcad* 로, Windows에서는 *gcservice* 에서 *gcarcservice* 로 변경되었습니다.

### <a name="new-feature"></a>새로운 기능

- 지원 정보를 수집하는 `azcmagent logs` 옵션이 추가되었습니다.
- EULA를 표시하는 `azcmagent license` 옵션이 추가되었습니다.
- 에이전트 상태를 쉽게 구문 분석 가능한 형식으로 출력하는 `azcmagent show --json` 옵션이 추가되었습니다.
- 서버가 Azure에 호스트된 가상 머신에 있는지를 나타내는 플래그가 `azcmagent show` 출력에 추가되었습니다.
- Azure 서비스에 연결할 수 없는 경우 로컬 에이전트 상태 재설정을 허용하는 `azcmagent disconnect --force-local-only` 옵션이 추가되었습니다.
- 다른 클라우드를 지원하는 `azcmagent connect --cloud` 옵션이 추가되었습니다. 이 릴리스에서는 에이전트 출시 시점에 Azure만 서비스에서 지원됩니다.
- 에이전트는 Azure 지원 언어로 지역화되었습니다.

### <a name="fixed"></a>고정

- 연결 확인이 향상되었습니다.
- Linux에서 에이전트를 업그레이드하는 경우 프록시 서버 설정이 손실되는 문제가 수정되었습니다.
- Windows Server 2012 R2를 실행하는 서버에 에이전트를 설치하려고 시도하는 경우 발생하는 문제가 해결되었습니다.
- 확장 설치 안정성이 향상되었습니다.

## <a name="august-2020"></a>2020년 8월

버전: 0.11

- 이전에 이 릴리스에서는 Ubuntu 20.04에 대한 지원을 발표했습니다. 일부 Azure VM 확장에서 Ubuntu 20.04를 지원하지 않으므로 이 버전의 Ubuntu에 대한 지원은 제거됩니다.

- 확장 배포에 대한 안정성이 향상되었습니다.

### <a name="known-issues"></a>알려진 문제

이전 버전의 Linux 에이전트를 사용하고 있으며 프록시 서버를 사용하도록 구성된 경우 업그레이드 후 프록시 서버 설정을 다시 구성해야 합니다. 이 위해 실행 `sudo azcmagent_proxy add http://proxyserver.local:83`합니다.

## <a name="next-steps"></a>다음 단계

여러 하이브리드 머신에서 Arc 지원 서버를 평가하거나 활성화하기 전에 [연결된 머신 에이전트 개요](agent-overview.md)를 검토하여 요구 사항, 에이전트에 대한 기술 세부 정보 및 배포 방법을 이해하세요.