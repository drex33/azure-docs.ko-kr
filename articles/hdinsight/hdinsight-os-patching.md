---
title: Azure HDInsight 클러스터의 OS 패치 일정 구성
description: Linux 기반 HDInsight 클러스터의 OS 패치 일정을 구성하는 방법에 대해 알아봅니다.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 08/30/2021
ms.openlocfilehash: 10ab6462096bbdabee0730e6ef60e07d8d51b438
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/30/2021
ms.locfileid: "123223565"
---
# <a name="configure-the-os-patching-schedule-for-linux-based-hdinsight-clusters"></a>Linux 기반 HDInsight 클러스터의 OS 패치 일정 구성

> [!IMPORTANT]
> Ubuntu 이미지는 게시된 날부터 3개월 이내에 새 Azure HDInsight 클러스터를 만드는 데 사용할 수 있게 됩니다. 실행 중인 클러스터는 자동 패치되지 않습니다. 고객이 스크립트 동작이나 기타 메커니즘을 사용하여 실행 중인 클러스터를 패치해야 합니다. 이 스크립트 작업을 실행하고 클러스터를 만든 직후에 보안 업데이트를 적용하는 것이 좋습니다.

HDInsight는 OS 패치, OS 보안 업데이트, 재부팅 노드 설치와 같은 일반적인 작업을 클러스터에서 수행할 수 있도록 지원합니다. 이 작업은 [스크립트 작업](hdinsight-hadoop-customize-cluster-linux.md)으로 실행하고 매개 변수를 통해 구성할 수 있는 다음 두 스크립트를 사용하여 수행됩니다.

- `schedule-reboots.sh` - 즉시 다시 시작하거나 클러스터 노드에서 다시 시작을 예약합니다.
- `install-updates-schedule-reboots.sh` - 모든 업데이트, 커널 + 보안 업데이트만 또는 커널 업데이트만 설치합니다.

> [!NOTE]  
> 스크립트 작업은 이후 모든 업데이트 주기에 대해 업데이트를 자동으로 적용하지 않습니다. 업데이트를 설치하기 위해 새 업데이트를 적용해야 할 때마다 스크립트를 실행한 다음, VM을 다시 시작합니다.

## <a name="preparation"></a>준비

프로덕션 환경에 배포하기 전에 대표적인 비프로덕션 환경에서 패치합니다. 실제 패치하기 전에 시스템을 적절하게 테스트하는 계획을 개발합니다.

시간이 지나면서 클러스터를 사용한 SSH 세션에서 보안 업데이트를 사용할 수 있다는 메시지가 표시될 수 있습니다. 메시지는 다음과 같이 표시될 수 있습니다.

```
89 packages can be updated.
82 updates are security updates.

*** System restart required ***

Welcome to Spark on HDInsight.

```

패치는 선택 사항이며 사용자의 판단에 따라 결정됩니다.

## <a name="restart-nodes"></a>노드 다시 시작
  
스크립트 [schedule-reboots](https://hdiconfigactions.blob.core.windows.net/linuxospatchingrebootconfigv02/schedule-reboots.sh)는 클러스터의 머신에서 수행될 다시 부팅 유형을 설정합니다. 스크립트 작업을 제출할 때 헤드 노드, 작업자 노드, Zookeeper의 세 가지 노드 형식에서 모두 적용되도록 설정합니다. 스크립트가 노드 형식에 적용되지 않으면 해당 노드 형식의 VM이 업데이트되거나 다시 시작되지 않습니다.

`schedule-reboots script`는 하나의 숫자 매개 변수를 허용합니다.

| 매개 변수 | 허용되는 값 | 정의 |
| --- | --- | --- |
| 수행할 다시 시작 유형 | 1 또는 2 | 값 1을 사용하면 일정을 다시 시작할 수 있습니다(12~24시간 이내로 예약됨). 값 2를 사용하면 즉시 다시 시작할 수 있습니다(5분 이내). 매개 변수를 지정하지 않으면 기본값은 1입니다. |  

## <a name="install-updates-and-restart-nodes"></a>업데이트 설치 및 노드 다시 시작

스크립트 [install-updates-schedule-reboots.sh](https://hdiconfigactions.blob.core.windows.net/linuxospatchingrebootconfigv02/install-updates-schedule-reboots.sh)는 다양한 유형의 업데이트를 설치하고 VM을 다시 시작하는 옵션을 제공합니다.

`install-updates-schedule-reboots` 스크립트는 다음 표에 설명된 대로 두 개의 숫자 매개 변수를 허용합니다.

| 매개 변수 | 허용되는 값 | 정의 |
| --- | --- | --- |
| 설치할 업데이트 유형 | 0, 1 또는 2 | 값 0은 커널 업데이트만 설치합니다. 값 1은 커널 + 보안 업데이트를 설치하고 2는 모든 업데이트를 설치합니다. 매개 변수를 제공하지 않으면 기본값은 0입니다. |
| 수행할 다시 시작 유형 | 0, 1 또는 2 | 값 0은 다시 시작을 사용하지 않도록 설정합니다. 값 1은 다시 시작을 예약할 수 있고, 2는 즉시 다시 시작할 수 있습니다. 매개 변수를 제공하지 않으면 기본값은 0입니다. 사용자는 입력 매개 변수 1을 입력 매개 변수 2로 변경해야 합니다. |

> [!NOTE]
> 스크립트를 기존 클러스터에 적용한 후 지속됨으로 표시해야 합니다. 그렇지 않은 경우 크기 조정 작업을 통해 만들어진 모든 새 노드는 기본 패치 일정을 사용합니다. 클러스터를 만드는 프로세스의 일부로 스크립트를 적용하는 경우 스크립트가 자동으로 유지됩니다.

> [!NOTE]
> 예약된 다시 시작 옵션은 12~24시간 동안 패치된 클러스터 노드의 자동화된 롤링 다시 시작을 수행하고 고가용성, 업데이트 도메인, 장애 도메인 고려 사항을 고려합니다. 예약된 다시 시작은 실행 중인 워크로드를 종료하지 않지만 노드를 사용할 수 없을 때 중간에 클러스터 용량을 제거할 수 있으므로 처리 시간이 길어집니다. 

## <a name="next-steps"></a>다음 단계

스크립트 작업을 사용하는 방법에 대한 특정 단계는 [스크립트 작업을 사용하여 Linux 기반 HDInsight 클러스터 사용자 지정](hdinsight-hadoop-customize-cluster-linux.md)에서 다음 섹션을 참조하세요.

- [클러스터를 만드는 동안 스크립트 작업 사용](hdinsight-hadoop-customize-cluster-linux.md#script-action-during-cluster-creation)
- [실행 중인 클러스터에 스크립트 작업 적용](hdinsight-hadoop-customize-cluster-linux.md#script-action-to-a-running-cluster)
