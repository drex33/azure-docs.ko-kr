---
title: Azure HDInsight 디스크 공간 관리
description: Azure HDInsight 클러스터와 상호 작용할 때 디스크 공간 문제를 관리하기 위한 문제 해결 단계 및 사용 가능한 해결 방법입니다.
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 02/17/2020
ms.openlocfilehash: 7164494cb08c4b419b9e4d96075ace3e52187497
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98944812"
---
# <a name="manage-disk-space-in-azure-hdinsight"></a>Azure HDInsight 디스크 공간 관리

이 문서에서는 Azure HDInsight 클러스터와 상호작용할 때 문제에 대한 문제 해결 단계 및 가능한 해결 방법을 설명합니다.

## <a name="hive-log-configurations"></a>Hive 로그 구성

1. 웹 브라우저에서 `https://CLUSTERNAME.azurehdinsight.net`로 이동합니다. 여기서 `CLUSTERNAME`은 클러스터의 이름입니다.

1. **Hive** > **구성** > **고급** > **고급 hive-log4j** 로 이동합니다. 다음 설정을 확인합니다.

    * `hive.root.logger=DEBUG,RFA`. 이 값은 기본값입니다. [로그 수준](https://logging.apache.org/log4j/2.x/log4j-api/apidocs/org/apache/logging/log4j/Level.html)을 `INFO` 로 수정하여 더 적은 로그 항목을 인쇄합니다.

    * `log4jhive.log.maxfilesize=1024MB`. 이 값은 기본값으로, 원하는 대로 수정합니다.

    * `log4jhive.log.maxbackupindex=10`. 이 값은 기본값으로, 원하는 대로 수정합니다. 매개 변수를 생략하면 생성된 로그 파일은 무한합니다.

## <a name="yarn-log-configurations"></a>Yarn 로그 구성

다음 구성을 확인합니다.

* Apache Ambari

    1. 웹 브라우저에서 `https://CLUSTERNAME.azurehdinsight.net`로 이동합니다. 여기서 `CLUSTERNAME`은 클러스터의 이름입니다.

    1. **Hive** > **구성** > **고급** > **리소스 매니저** 로 이동합니다. **로그 집계 사용이** 켜져 있는지 확인합니다. 사용하지 않도록 설정하면 이름 노드는 로그를 로컬로 유지하고 응용 프로그램 완료 또는 종료 시 원격 저장소에 집계하지 않습니다.

* 클러스터 크기가 워크로드에 적합한지 확인합니다. 워크로드가 최근에 변경되었거나 클러스터 크기가 변경되었을 수 있습니다. 더 높은 워크로드에 맞게 클러스터를 [확장](../hdinsight-scaling-best-practices.md) 합니다.

* `/mnt/resource`는 분리된 파일로 채워질 수 있습니다(리소스 관리자를 다시 시작하는 경우처럼). 필요한 경우 `/mnt/resource/hadoop/yarn/log` 및 `/mnt/resource/hadoop/yarn/local`를 수동으로 정리합니다.

## <a name="next-steps"></a>다음 단계

문제가 표시되지 않거나 문제를 해결할 수 없는 경우 다음 채널 중 하나를 방문하여 추가 지원을 받으세요.

* [Azure 커뮤니티 지원](https://azure.microsoft.com/support/community/)을 통해 Azure 전문가로부터 답변을 얻습니다.

* [@AzureSupport](https://twitter.com/azuresupport)(고객 환경을 개선하기 위한 공식 Microsoft Azure 계정)에 연결합니다. Azure 커뮤니티를 적절한 리소스(답변, 지원 및 전문가)에 연결합니다.

* 도움이 더 필요한 경우 [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)에서 지원 요청을 제출할 수 있습니다. 메뉴 모음에서 **지원** 을 선택하거나 **도움말 + 지원** 허브를 엽니다. 자세한 내용은 [Azure 지원 요청을 만드는 방법](../../azure-portal/supportability/how-to-create-azure-support-request.md)을 참조하세요. 구독 관리 및 청구 지원에 대한 액세스는 Microsoft Azure 구독에 포함되며 [Azure 지원 플랜](https://azure.microsoft.com/support/plans/) 중 하나를 통해 기술 지원이 제공됩니다.