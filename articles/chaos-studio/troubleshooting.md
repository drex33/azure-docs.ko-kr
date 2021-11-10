---
title: 일반적인 Azure 비정상 스튜디오 문제 해결
description: Azure 비정상 스튜디오를 사용할 때 발생 하는 일반적인 문제를 해결 하는 방법 알아보기
author: c-ashton
ms.service: chaos-studio
ms.author: cashton
ms.topic: troubleshooting
ms.date: 11/01/2021
ms.custom: template-troubleshooting, ignite-fall-2021
ms.openlocfilehash: 235dccf84d28f159a4ef5337a431c27c0316d6ec
ms.sourcegitcommit: 512e6048e9c5a8c9648be6cffe1f3482d6895f24
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/10/2021
ms.locfileid: "132158935"
---
# <a name="troubleshoot-issues-with-azure-chaos-studio"></a>Azure 비정상 스튜디오 문제 해결

비정상 스튜디오를 사용 하는 경우 가끔 몇 가지 문제가 발생할 수 있습니다. 이 문서에서는 일반적인 문제 및 문제 해결 단계에 대해 자세히 설명 합니다.

## <a name="issues-due-to-prerequisites"></a>필수 조건으로 인 한 문제

일부 문제는 필수 구성 요소가 누락 되었기 때문에 발생 합니다. 

### <a name="why-do-agent-based-faults-fail-on-my-linux-virtual-machines"></a>Linux 가상 머신에서 에이전트 기반 오류가 실패 하는 이유는 무엇 인가요?

[CPU 압력](chaos-studio-fault-library.md#cpu-pressure), [실제 메모리 부족](chaos-studio-fault-library.md#physical-memory-pressure), [디스크 i/o 압력](chaos-studio-fault-library.md#disk-io-pressure-linux)및 [임의 스트레스](chaos-studio-fault-library.md#arbitrary-stress-ng-stress) 오류에 대 한 모든 요구 사항을 가상 컴퓨터에 설치 [해야 합니다.](https://wiki.ubuntu.com/Kernel/Reference/stress-ng) 자세한 내용은 오류 전제 조건 섹션을 참조 하세요.

### <a name="my-aks-chaos-mesh-faults-are-failing"></a>내 AKS 비정상 메시 오류가 실패 합니다.

AKS에 대해 비정상 메시 오류를 사용 하기 전에 먼저 비정상 메시를 설치 해야 합니다. 지침은 [AKS의 비정상 메시 오류 자습서](chaos-studio-tutorial-aks.md#set-up-chaos-mesh-on-your-aks-cluster)에서 찾을 수 있습니다.

## <a name="experiment-design-and-creation"></a>실험 디자인 및 만들기

### <a name="why-do-i-get-the-error-the-microsoftagent-provider-requires-a-managed-identity-when-i-try-to-create-an-experiment"></a>실험을 만들려고 할 때 오류가 발생 하는 이유는 무엇 `The microsoft:agent provider requires a managed identity` 인가요? 

이 오류는 에이전트가 가상 컴퓨터에 배포 되지 않은 경우에 발생 합니다. 설치 지침은 [에이전트 기반 오류를 사용 하는 실험 만들기 및 실행](chaos-studio-tutorial-agent-based.md)을 참조 하세요.

### <a name="when-creating-an-experiment-i-get-the-error-the-content-media-type-null-is-not-supported-only-applicationjson-is-supported-what-does-this-mean"></a>실험을 만들 때이 오류의 의미를 얻는 `The content media type '<null>' is not supported. Only 'application/json' is supported.` 것은 무엇 인가요?

실험 JSON을 직접 작성 하는 경우이 오류는 실험 정의에서 잘못 된 JSON으로 인해 발생 합니다. 괄호와 괄호 (및)가 일치 하지 않는 경우와 같이 구문 오류가 있는지 확인 {} 하십시오 \[ \] .

## <a name="experiment-execution"></a>실험 실행

### <a name="the-execution-status-of-my-experiment-is-failed-how-do-i-determine-what-went-wrong"></a>내 실험의 실행 상태는 "실패"입니다. 어떤 문제가 발생 했는지 확인 하는 어떻게 할까요?

실험 페이지에서 실험 이름을 클릭 하 여 실험 세부 정보 뷰로 이동 합니다. 기록 섹션에서 실행 인스턴스에 대 한 세부 정보 링크를 클릭 하 여 자세한 정보를 확인 합니다.

![실험 기록](images/run-experiment-history.png)

### <a name="my-agent-based-fault-failed-with-error-verify-that-the-target-is-correctly-onboarded-and-proper-read-permissions-are-provided-to-the-experiment-msi"></a>오류가 발생 하 여 에이전트 기반 오류가 실패 했습니다. 대상이 올바르게 등록 실험 msi에 적절 한 읽기 권한이 제공 되는지 확인 하세요.

이 문제는 알려진 문제를 포함 하는 Azure Portal를 사용 하 여 에이전트를 등록 경우 발생할 수 있습니다. 에이전트 기반 대상을 사용 하도록 설정 해도 사용자 할당 관리 id를 가상 머신 또는 가상 머신 확장 집합에 할당 하지 않습니다.

이 문제를 해결 하려면 Azure Portal에서 가상 머신 또는 가상 머신 확장 집합으로 이동 하 고, **id** 로 이동 하 여 **사용자 할당 됨** 탭을 열고, 사용자 할당 Id를 가상 머신에 **추가** 합니다. 완료 되 면 에이전트를 연결 하기 위해 가상 머신을 다시 부팅 해야 할 수 있습니다.

### <a name="how-do-i-collect-agent-logs-on-a-linux-virtual-machine"></a>Linux 가상 머신에서 에이전트 로그를 수집할 어떻게 할까요? 있나요?

VM에서 다음 명령을 실행 합니다. `journalctl -u azure-chaos-agent`
