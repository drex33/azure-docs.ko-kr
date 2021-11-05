---
title: Azure Chaos Studio 제한 사항 및 알려진 문제
description: Azure Chaos Studio를 사용할 때의 현재 제한 사항 및 알려진 문제를 이해합니다.
services: chaos-studio
author: johnkemnetz
ms.topic: overview
ms.date: 11/02/2021
ms.author: johnkem
ms.service: chaos-studio
ms.openlocfilehash: d843b91864723fa0195430ac0f293fc7159d2adc
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131479739"
---
# <a name="azure-chaos-studio-preview-limitations-and-known-issues"></a>Azure Chaos Studio 미리 보기 제한 사항 및 알려진 문제

Azure Chaos Studio의 공개 미리 보기 중에는 팀에서 인식하고 해결하는 데 사용하는 몇 가지 제한 사항 및 알려진 문제가 있습니다.

## <a name="limitations"></a>제한 사항 

* 에이전트 기반 오류의 경우 머신에 다음 **HTTPS 엔드포인트** 에 대한 액세스 권한이 있어야 합니다.
    * http://agentcommunicationservice-frontdoor-canary.trafficmanager.net 
    * Application Insights에 원격 분석 데이터를 전송하는 경우 [이 문서의](../azure-monitor/app/ip-addresses.md) IP도 필요합니다.
* Chaos Agent를 사용하는 실험을 실행하는 경우 VM은 다음 **운영 체제** 중 하나를 실행해야 합니다.
    * Windows Server 2019, Windows Server 2016, Windows Server 2012 및 Windows Server 2012 R2
    * Redhat Enterprise Linux 8.2, SUSE Enterprise Linux 15 SP2, CentOS 8.2, Debian 10 Buster(압축 풀기 설치 필요), Oracle Linux 7.8 Ubuntu Server 16.04 LTS 및 Ubuntu Server 18.04 LTS
* Chaos Agent는 사용자 지정 Linux 배포, 강화된 Linux 배포(예: FIPS 또는 SELinux)에 대해 테스트되지 않습니다.
* Chaos Studio 포털 환경은 다음 브라우저에서만 테스트되었습니다.
    * **Windows:** Microsoft Edge, Google Chrome, Firefox
    * **MacOS:** Safari, Google Chrome, Firefox

## <a name="known-issues"></a>알려진 문제
* 온보딩을 완료하기 전에 대상 보기에서 다른 곳으로 이동하면 Azure Portal에 대상 온보딩이 실패할 수 있습니다.
* 실험을 만들 때 **검토 + 만들기** 를 클릭한 후에는 생성된 실험이 실험 목록에 표시되기 전에 지연이 발생하고 사용자가 목록에서 실험을 보기 위해 새로 고쳐야 합니다.
* 실험 디자이너에서 에이전트 기반 오류에 대한 대상 리소스를 선택할 때 선택한 오류에서 지원하지 않는 운영 체제를 사용하여 가상 머신 또는 가상 머신 확장 집합을 선택할 수 있습니다.


## <a name="next-steps"></a>다음 단계
아래 링크를 사용하여 Chaos Studio를 통해 애플리케이션 복원력을 개선하기 위한 카오스 상황을 만들고 실행합니다.
- [첫 번째 실험 만들기 및 실행](chaos-studio-tutorial-service-direct.md)
- [카오스 엔지니어링 자세히 알아보기](chaos-studio-chaos-engineering-overview.md)
