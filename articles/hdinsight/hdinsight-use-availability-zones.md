---
title: 가용성 영역 사용하는 Azure HDInsight 클러스터 만들기
description: 가용성 영역 사용하는 Azure HDInsight 클러스터를 만드는 방법을 알아봅니다.
ms.service: hdinsight
ms.topic: how-to
ms.custom: references_regions
ms.date: 09/01/2021
ms.openlocfilehash: 1b516db029c5769526f8345d5f6ecdb96c5fad6c
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/03/2021
ms.locfileid: "123441490"
---
# <a name="create-an-hdinsight-cluster-that-uses-availability-zones-preview"></a>가용성 영역 사용하는 HDInsight 클러스터 만들기(미리 보기)

Azure HDInsight 클러스터는 여러 노드(헤드 노드, 작업자 노드, 게이트웨이 노드 및 Zookeeper 노드)로 구성됩니다. 기본적으로 가용성 영역 지원하는 지역에서는 가용성 영역이 프로비전되는 클러스터 노드를 제어할 수 없습니다. 

이 새로운 가용성 영역 기능을 통해 사용자는 이제 HDInsight 클러스터의 모든 노드를 호스트할 가용성 영역을 지정할 수 있습니다. 클러스터 노드는 다른 가용성 영역과 물리적으로 분리되며 동일한 지역의 다른 가용성 영역 오류로부터 격리됩니다. 또한 이 배포 모델은 클러스터 내에서 저렴하고 짧은 대기 시간 네트워크 연결을 제공합니다. 

이 배포 모델을 여러 가용성 영역 복제하면 하드웨어 오류로부터 보호하기 위한 높은 수준의 가용성을 제공할 수 있습니다.

이 문서에서는 가용성 영역 내에서 HDInsight 클러스터를 만드는 방법과 이 기능을 사용하여 더 높은 가용성을 달성하는 방법을 보여줍니다. 

## <a name="before-you-begin"></a>시작하기 전에
가용성 영역 기능은 6월 15일 이후에 생성된 클러스터에 대해서만 지원됩니다. 클러스터를 만든 후에는 가용성 영역 설정을 업데이트할 수 없습니다. 또한 가용성 영역을 사용하도록 기존의 비가용성 영역 클러스터를 업데이트할 수 없습니다.

## <a name="prerequisites-and-region-availability"></a>필수 조건 및 지역 가용성
필수 조건:

 - 클러스터는 사용자 지정 VNet에서 만들어야 합니다. 
 - 동일한 가용성 영역에서 이러한 DB를 구성할 수 있도록 Ambari DB 및 외부 metastore(예: Hive 메타스토어)에 대한 고유한 SQL DB를 가져와야 합니다. 

HDInsight 클러스터는 현재 다음 지역의 가용성 영역을 사용하여 만들 수 있습니다.

 - 오스트레일리아 동부
 - 브라질 남부
 - 캐나다 중부
 - 미국 중부
 - 미국 동부
 - 미국 동부 2
 - 프랑스 중부
 - 독일 중서부
 - 일본 동부
 - 북유럽
 - 동남아시아
 - 미국 중남부
 - 영국 남부
 - US Gov 버지니아
 - 서유럽
 - 미국 서부 2

## <a name="overview-of-availability-zones-for-hdinsight-clusters"></a>HDInsight 클러스터의 가용성 영역 개요

가용성 영역은 지역 내의 고유한 물리적 위치입니다. 각 영역은 독립된 전원, 냉각 및 네트워킹을 갖춘 하나 이상의 데이터 센터로 구성됩니다. Azure에서 지역에는 하나 이상의 가용성 영역 포함되어 있습니다. 지역 내에서 가용성 영역을 물리적으로 분리하는 것은 데이터 센터 오류로부터 애플리케이션과 데이터를 보호합니다. 자세한 내용은 [Azure의 가용성 영역이란?을 참조하세요.](../availability-zones/az-overview.md)

Azure HDInsight 클러스터는 하나의 가용성 영역 내에 배포하도록 구성할 수 있습니다. 헤드 노드 2개, Zookeeper 노드 3개, 게이트웨이 노드 2개, 작업자 노드 1개를 포함한 이 HDInsight 클러스터의 모든 노드는 지정된 가용성 영역에 배치됩니다.  예를 들어 미국 동부에는 세 개의 가용성 영역이 있습니다. 가용성 영역 1의 모든 노드를 통해 미국 동부의 HDInsight 클러스터를 만들 수 있습니다. 

이러한 방식으로 HDInsight 클러스터에서 가용성 영역을 사용하면 성능 및 비용 이점을 모두 제공할 수 있습니다. 

 - 짧은 대기 시간 네트워크 연결로 인한 성능 향상
 - 저렴한 비용: 동일한 가용성 영역 내의 데이터 전송은 무료입니다. 가용성 영역 간에 데이터를 전송하면 추가 네트워킹 비용이 발생합니다. 

애플리케이션에 여러 가용성 영역에서 고가용성이 필요한 경우 하나의 가용성 영역에 하나의 기본 HDInsight 클러스터를 만들고 최소 크기로 다른 가용성 영역에 보조 HDInsight 클러스터를 만들어 비용을 절감할 수 있습니다. 이 디자인을 사용하면 다른 가용성 영역 중 하나가 중단되면 이 HDInsight 클러스터는 영향을 받지 않습니다. 이 가용성 영역이 중단되면 고객은 다른 가용성 영역의 보조 클러스터를 주 클러스터로 전환하고, 워크로드를 이 새 주 클러스터로 라우팅하고, 클러스터 크기를 신속하게 확장하여 데이터 처리를 선택해야 합니다.   

## <a name="create-an-hdinsight-cluster-using-availability-zone"></a>가용성 영역을 사용하여 HDInsight 클러스터 만들기
ARM(Azure Resource Manager) 템플릿을 사용하여 HDInsight 클러스터를 지정된 가용성 영역으로 시작할 수 있습니다. 

리소스 섹션에서 '영역'의 섹션을 추가하고 이 클러스터를 배포할 가용성 영역을 제공해야 합니다. 

```json
   "resources": [
        {
            "type": "Microsoft.HDInsight/clusters",
            "apiVersion": "2018-06-01-preview",
            "name": "[parameters('cluster name')]",
            "location": "East US 2",
            "zones": [
                "1"
            ],
```
 
## <a name="verify-nodes-within-one-availability-zone-across-zones"></a>영역 간에 하나의 가용성 영역 내의 노드 확인
HDInsight 클러스터가 준비되면 위치를 확인하여 배포된 가용성 영역을 확인할 수 있습니다.

:::image type="content" source="./media/hdinsight-use-availability-zones/cluster-availability-zone-info.png" alt-text="클러스터 개요의 가용성 영역 정보를 소개하는 스크린샷" border="true":::

**API 응답 받기:** 

```json
 [
        {
            "location": "East US 2",
            "zones": [
                "1"
            ],
```

## <a name="scale-up-the-cluster"></a>클러스터 강화
더 많은 작업자 노드를 통해 HDInsight 클러스터를 확장할 수 있습니다. 새로 추가된 작업자 노드는 이 클러스터의 동일한 가용성 영역에 배치됩니다. 

**단점**: 

 - HDInsight의 가용성 영역 기능은 관리 디스크가 필요한 클러스터를 지원하지 않습니다(Kafka 클러스터 및 가속 쓰기 기능 사용 HBase 클러스터). 

## <a name="best-practices"></a>모범 사례

 - Ambari DB에서 구성을 정기적으로 백업합니다. 
 - 워크로드를 보조 클러스터로 쉽게 라우팅하는 논리를 구현합니다.

## <a name="when-az-goes-down-what-to-expect"></a>AZ가 다운되면 예상되는 결과
 - 이 클러스터에 ssh할 수 없습니다.
 - 이 클러스터를 삭제하거나 강화하거나 축소할 수 없습니다.
 - 작업을 제출하거나 작업 기록을 볼 수 없습니다.
 - 다른 지역에서 새 클러스터 만들기 요청을 제출할 수 있습니다.