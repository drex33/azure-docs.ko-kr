---
title: Azure Red Hat OpenShift 지원 수명 주기
description: Azure Red Hat OpenShift의 지원 수명 주기 및 지원되는 버전을 알아봅니다.
author: sakthi-vetrivel
ms.author: suvetriv
ms.service: azure-redhat-openshift
ms.topic: conceptual
ms.date: 06/16/2021
ms.openlocfilehash: 2c1eeb97fecac449e85aa0a5d1987dc6ef2c4b4f
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/06/2021
ms.locfileid: "129614273"
---
# <a name="support-lifecycle-for-azure-red-hat-openshift-4"></a>Azure Red Hat OpenShift 4 지원 수명 주기

Red Hat는 약 3개월마다 Red Hat OCP(OpenShift Container Platform)의 부 버전을 릴리스합니다. 이러한 릴리스에는 새로운 기능과 향상된 기능이 포함됩니다. 패치는 더 자주(일반적으로 매주) 릴리스되며, 부 버전의 중요한 버그 수정용으로만 제공됩니다. 이러한 패치 릴리스에는 보안 취약점 또는 주요 버그에 대한 수정이 포함될 수 있습니다.

Azure Red Hat OpenShift는 특정 릴리스의 OCP를 기반으로 빌드됩니다. 이 문서에서는 Azure Red Hat OpenShift에 대해 지원되는 OCP 버전과 업그레이드, 사용 중단 및 지원 정책을 자세히 설명합니다.

## <a name="red-hat-openshift-versions"></a>Red Hat OpenShift 버전

Red Hat OpenShift Container Platform은 의미 체계 버전 관리를 사용합니다. 의미 체계 버전 관리에서는 서로 다른 수준의 버전 번호를 사용하여 다른 수준의 버전 관리를 지정합니다. 다음 표에서는 의미 체계 버전 번호의 여러 부분을 보여 줍니다. 여기서는 버전 번호 4.4.11를 예로 사용합니다.

|주 버전(x)|부 버전(y)|패치(z)|
|-|-|-|
|4|4|11|

버전의 각 번호는 이전 버전과의 일반적인 호환성을 나타냅니다.

* **주 버전**: 지금은 주 버전 릴리스가 계획되어 있지 않습니다. 주 버전은 호환되지 않는 API 변경 또는 이전 버전과의 호환성이 손상될 때 변경됩니다.
* **부 버전**: 약 3개월마다 릴리스됩니다. 부 버전 업그레이드에는 추가 기능, 향상된 기능, 사용 중단, 제거, 버그 수정, 향상된 보안 기능 및 기타 개선 사항이 포함될 수 있습니다.
* **패치**: 일반적으로 매주 또는 필요한 경우에 릴리스됩니다. 패치 버전 업그레이드에는 버그 수정, 향상된 보안 기능 및 기타 향상된 기능이 포함될 수 있습니다.

고객은 실행 중인 주 버전의 최신 부 릴리스를 실행해야 합니다. 예를 들어 프로덕션 클러스터가 4.4이고, 4 시리즈에서 최신 일반 공급 부 버전이 4.5인 경우, 가능한 한 빨리 4.5로 업그레이드해야 합니다. 

### <a name="upgrade-channels"></a>업그레이드 채널

업그레이드 채널은 부 버전의 Red Hat OCP(OpenShift Container Platform)에 연결됩니다. 예를 들어, OCP 4.4 업그레이드 채널은 4.5 릴리스로의 업그레이드를 포함하지 않습니다. 업그레이드 채널은 릴리스 선택만 제어하고 클러스터 버전에는 영향을 주지 않습니다.

Azure Red Hat OpenShift 4는 Stable 채널만 지원합니다. 예: stable-4.4.

stable-4.5 채널을 사용하여 Azure Red Hat OpenShift의 이전 부 버전에서 업그레이드할 수 있습니다. Fast, Prerelease 및 Candidate 채널을 사용하여 업그레이드된 클러스터는 지원되지 않습니다.

현재 릴리스가 포함되지 않은 채널로 변경하는 경우 경고가 표시되고 업데이트를 권장하지 않을 수 있지만 언제든지 안전하게 원래 채널로 다시 변경할 수 있습니다.

## <a name="red-hat-openshift-container-platform-version-support-policy"></a>Red Hat OpenShift Container Platform 버전 지원 정책

Azure Red Hat OpenShift는 Red Hat OpenShift Container Platform의 GA(일반 공급) 부 버전 두 개를 지원합니다.
* Azure Red Hat OpenShift에서 릴리스된 최신 GA 부 버전(N)
* 이전 부 버전 1개(N-1)

Stable 업그레이드 채널에서 사용할 수 있는 경우 업스트림 OCP에서 사용할 수 있는 최신 부 릴리스(N + 1, N + 2)도 지원될 수 있습니다.

중요 패치 업데이트는 Azure Red Hat OpenShift SRE(사이트 안정성 엔지니어)에 의해 자동으로 클러스터에 적용됩니다. 패치 업데이트를 미리 설치하려는 고객은 무료로 사용할 수 있습니다.

예를 들어 Azure Red Hat OpenShift가 오늘 4.5.z를 릴리스하는 경우 다음 버전이 지원됩니다.

|새 부 버전|지원되는 버전 목록|
|-|-|
|4.5.z|4.5.z, 4.4.z|

여기서 '.z'는 패치 버전을 나타냅니다. Stable 업그레이드 채널에서 사용할 수 있는 경우 고객은 4.6.z로 업그레이드할 수도 있습니다.

새로운 부 버전이 릴리스되면 지원되는 가장 오래된 부 버전 및 패치 릴리스는 사용 중단되고 제거됩니다. 예를 들어 현재 지원되는 버전 목록은 4.5.z 및 4.4.z이라고 하겠습니다. Azure Red Hat OpenShift가 4.6.z를 릴리스하면 4.4.z 릴리스가 제거되고 30일 이내에 지원이 중단됩니다.

> [!NOTE]
> 고객이 지원되지 않는 Red Hat OpenShift 버전을 실행하는 경우 클러스터에 대한 지원을 요청할 때 업그레이드하라는 요청을 받을 수 있습니다. 지원되지 않는 Red Hat OpenShift 릴리스를 실행하는 클러스터는 Azure Red Hat OpenShift SLA가 적용되지 않습니다.

## <a name="release-and-deprecation-process"></a>릴리스 및 사용 중단 프로세스

Azure Red Hat OpenShift 릴리스 일정에서 예정된 버전 릴리스 및 사용 중단을 참조할 수 있습니다.

Red Hat OpenShift Container Platform의 새로운 부 버전:
* Azure Red Hat OpenShift SRE 팀은 새 버전을 릴리스하는 경우 예정일을 미리 발표하고 이전 버전을 사용 중단하는 경우 [Azure Red Hat OpenShift 릴리스 정보](https://github.com/Azure/OpenShift/releases)에 제거하기 적어도 30일 전에 공지합니다.
* Azure Red Hat OpenShift SRE 팀은 Azure Red Hat OpenShift 및 포털 액세스 권한이 있는 모든 고객에 게 제공되는 서비스 상태 알림을 게시하고 구독 관리자에게 예정된 버전 제거 날짜를 이메일로 보냅니다.
* 고객은 버전 제거 후 30일 동안 지원되는 부 버전 릴리스로 업그레이드하여 지원을 계속 받을 수 있습니다.

Red Hat OpenShift Container Platform의 새 패치 버전:
* 패치 버전의 긴급성 때문에 Azure Red Hat OpenShift SRE 팀이 서비스에 릴리스되는 패치 버전을 도입할 수 있습니다.
* 일반적으로 Azure Red Hat OpenShift SRE 팀은 새로운 패치 버전의 설치를 광범위하게 알리지 않습니다. 그러나 팀은 적시에 패치 버전을 지원하기 위해 사용 가능한 CVE 패치를 지속적으로 모니터링하고 유효성을 검사합니다. 고객 조치가 필요한 경우 팀은 업그레이드 정보를 고객에게 알립니다.

## <a name="supported-versions-policy-exceptions"></a>지원되는 버전 정책 예외

Azure Red Hat OpenShift SRE 팀은 사전 공지 없이 프로덕션 환경에 영향을 미치는 하나 이상의 중대한 버그 또는 보안 문제가 확인된 새/기존 버전을 추가 또는 제거하거나 향후 부 릴리스 버전을 지연할 권리를 보유합니다.

버그 또는 보안 문제의 심각도에 따라 특정 패치 릴리스를 건너뛰거나 롤아웃을 가속화할 수 있습니다.

## <a name="azure-portal-and-cli-versions"></a>Azure Portal 및 CLI 버전

포털 또는 Azure CLI를 사용하여 Azure Red Hat OpenShift 클러스터를 배포하는 경우 클러스터에는 기본적으로 최신(N) 부 버전 및 최신 중요 패치가 적용됩니다. 예를 들어 Azure Red Hat OpenShift가 4.5.z 및 4.4.z를 지원하는 경우 새 설치의 기본 버전은 4.5.z입니다. 최신 업스트림 OCP 부 버전(N+1, N+2)을 사용하려는 고객은 언제든지 Stable 업그레이드 채널에서 사용할 수 있는 릴리스로 클러스터를 업그레이드할 수 있습니다.

## <a name="azure-red-hat-openshift-release-calendar"></a>Azure Red Hat OpenShift 릴리스 일정

[이전 Red Hat OpenShift Container Platform(업스트림) 릴리스 내역](https://access.redhat.com/support/policy/updates/openshift/#dates)에 대한 다음 가이드를 참조하세요.

|OCP 버전|업스트림 릴리스|Azure Red Hat OpenShift 일반 공급|수명 종료|
|-|-|-|-|
|4.4.|2020년 5월|2020년 7월|4.6 GA|
|4.5.|2020년 7월| 2020년 11월|4.7 GA|
|4.6|2020년 10월| 2021년 2월|4.8 GA|
|4.7|2021년 2월| 7 월 15 2021|4.9 GA|
|4.8|2021년 7월| 9 월 15 2021|4.10 GA|

## <a name="faq"></a>FAQ

**사용자가 지원되지 않는 부 버전을 사용하여 OpenShift 클러스터를 업그레이드하면 어떻게 되나요?**

N-2 이전 버전인 경우에는 지원 범위에 해당되지 않으므로 계속해서 지원을 받으려면 업그레이드 요청을 받게 됩니다. 버전 N-2에서 N-1로 업그레이드하면 지원에 다시 포함됩니다. N-3 버전 또는 이전 버전을 지원되는 버전으로 업그레이드하는 것은 어려울 수 있으며 경우에 따라 불가능할 수 있습니다. 잠재적인 업그레이드 문제를 방지하려면 클러스터를 최신 OpenShift 버전으로 유지하는 것이 좋습니다. 예를 들어:
* 지원되는 가장 오래된 Azure Red Hat OpenShift 버전이 4.4.z일 때 4.3.z 이전 버전을 사용하는 경우 사용자는 지원 범위에서 제외됩니다.
* 4\.3.z에서 4.4.z 이상으로 업그레이드하면 다시 지원 정책에 포함됩니다. 

클러스터를 이전 버전으로 롤백하는 것은 지원되지 않습니다. 최신 버전으로의 업그레이드만 지원됩니다.

**'지원에 포함되지 않음'은 무엇을 의미하나요?**

ARO 클러스터가 지원되는 버전 목록에 없거나 [지원되지 않는 클러스터 구성](./support-policies-v4.md)을 사용하는 OpenShift 버전을 실행하는 경우 클러스터는 "지원되지 않음"입니다. 결과는 다음과 같습니다.
- 클러스터에 대한 지원 티켓을 열 때 클러스터를 지원되는 버전으로 업그레이드하라는 메시지가 표시됩니다. 버전 지원이 종료된 후 30일 유예 기간 내에 속하지 않는 한 지원을 받기 전입니다. 
- 지원 이외의 클러스터에 대한 모든 런타임 또는 SLA 보장은 무효화됩니다.
- 지원 이외의 클러스터는 최상의 노력으로만 패치됩니다.
- 지원 이외의 클러스터는 모니터링되지 않습니다.
