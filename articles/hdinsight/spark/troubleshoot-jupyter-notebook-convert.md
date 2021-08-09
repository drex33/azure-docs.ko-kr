---
title: Azure HDInsight에서 Jupyter Notebook를 만들 수 없음
description: Azure HDInsight 클러스터와 상호 작용하는 경우 문제 해결 단계 및 가능한 해결 방법에 대해 알아봅니다.
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 02/11/2020
ms.openlocfilehash: d5f6612e22522452efb5eecd14c6c825bf29e6d5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98938675"
---
# <a name="unable-to-create-jupyter-notebook-in-azure-hdinsight"></a>Azure HDInsight에서 Jupyter Notebook를 만들 수 없음

이 문서에서는 Azure HDInsight 클러스터와 상호 작용할 때 문제에 대한 문제 해결 단계 및 가능한 해결 방법을 설명합니다.

## <a name="issue"></a>문제

Jupyter Notebook를 시작할 때 다음을 포함하는 오류 메시지를 수신하게 됩니다.

```error
Cannot convert notebook to v5 because that version doesn't exist
```

## <a name="cause"></a>원인

버전이 일치하지 않습니다.

## <a name="resolution"></a>해결 방법

1. [ssh command](../hdinsight-hadoop-linux-use-ssh-unix.md) 명령을 사용하여 클러스터에 연결합니다. CLUSTERNAME을 클러스터 이름으로 바꿔서 아래 명령을 편집하고, 다음 명령을 입력합니다.

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. 다음 명령을 실행하여 `_version.py`을 시작합니다.

    ```bash
    sudo nano /usr/bin/anaconda/lib/python2.7/site-packages/nbformat/_version.py
    ```

1. **5** 를 **4** 로 변경하여 수정된 줄이 다음과 같이 표시되도록 합니다.

    ```python
    version_info = (4, 0, 3)
    ```

    **Ctrl+X**, **Y**, **Enter** 를 입력 하여 변경 내용을 저장합니다.

1. 웹 브라우저에서 `https://CLUSTERNAME.azurehdinsight.net/#/main/services/JUPYTER`로 이동합니다. 여기서 `CLUSTERNAME`은 클러스터의 이름입니다.

1. Jupyter 서비스를 다시 시작합니다.

## <a name="next-steps"></a>다음 단계

문제가 표시되지 않거나 문제를 해결할 수 없는 경우 다음 채널 중 하나를 방문하여 추가 지원을 받으세요.

* [Azure 커뮤니티 지원](https://azure.microsoft.com/support/community/)을 통해 Azure 전문가로부터 답변을 얻습니다.

* [@AzureSupport](https://twitter.com/azuresupport)(고객 환경을 개선하기 위한 공식 Microsoft Azure 계정)에 연결합니다. Azure 커뮤니티를 적절한 리소스(답변, 지원 및 전문가)에 연결합니다.

* 도움이 더 필요한 경우 [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)에서 지원 요청을 제출할 수 있습니다. 메뉴 모음에서 **지원** 을 선택하거나 **도움말 + 지원** 허브를 엽니다. 자세한 내용은 [Azure 지원 요청을 만드는 방법](../../azure-portal/supportability/how-to-create-azure-support-request.md)을 참조하세요. 구독 관리 및 청구 지원에 대한 액세스는 Microsoft Azure 구독에 포함되며 [Azure 지원 플랜](https://azure.microsoft.com/support/plans/) 중 하나를 통해 기술 지원이 제공됩니다.
