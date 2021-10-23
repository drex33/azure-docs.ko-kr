---
title: Apache Kafka 토픽 미러링 - Azure HDInsight
description: Apache Kafka의 미러링 기능으로 보조 클러스터에 토픽을 미러링하여 HDInsight 클러스터에서 Kafka 복제본을 유지하는 방법에 대해 알아봅니다.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 11/29/2019
ms.openlocfilehash: 5c62b183d55023b0b8a25dcde03aef21e0364a3e
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130261903"
---
# <a name="use-mirrormaker-to-replicate-apache-kafka-topics-with-kafka-on-hdinsight"></a>MirrorMaker를 사용하여 HDInsight에서 Kafka와 함께 Apache Kafka 토픽 복제

Apache Kafka의 미러링 기능을 사용하여 토픽을 보조 클러스터로 복제하는 방법에 대해 알아봅니다. 미러링은 연속적 프로세스로 실행되거나 한 클러스터에서 다른 클러스터로 데이터를 마이그레이션하는 방법으로 단속적으로 사용될 수 있습니다.

> [!NOTE]
> 이 문서에는 Microsoft에서 더 이상 사용하지 않는 용어인 *허용 목록* 용어에 대한 참조가 포함되어 있습니다. 소프트웨어에서 용어가 제거되면 이 문서에서 해당 용어가 제거됩니다.

이 예제에서 미러링은 두 HDInsight 클러스터 간에 항목을 복제하는 데 사용됩니다. 두 클러스터는 서로 다른 데이터 센터에 있는 서로 다른 가상 네트워크에 있습니다.

> [!WARNING]  
> 미러링이 내결함성을 달성하는 수단으로 간주되어서는 안됩니다. 토픽 내의 항목에 대한 오프셋은 기본 및 보조 클러스터 간에 서로 다르므로 클라이언트에서는 이 두 가지를 서로 교환하여 사용할 수 없습니다.
>
> 내결함성이 염려되는 경우 클러스터 내의 토픽에 대한 복제를 설정해야 합니다. 자세한 내용은 [HDInsight에서 Apache Kafka 시작](apache-kafka-get-started.md)을 참조하세요.

## <a name="how-apache-kafka-mirroring-works"></a>Apache Kafka 미러링 작동 방식

미러링은 [MirrorMaker](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330) 도구(Apache Kafka의 일부)를 사용하여 기본 클러스터의 토픽에서 레코드를 사용한 다음 보조 클러스터에 로컬 복사본을 생성합니다. MirrorMaker는 기본 클러스터에서 불러온 한 개 이상의 소비자와 로컬 보조 클러스터에 작성하는 프로듀서를 사용합니다.

재해 복구에 가장 유용한 미러링 설정은 여러 Azure 지역에서 Kafka 클러스터를 활용하는 것입니다. 이를 위해 클러스터가 있는 가상 네트워크는 함께 피어링됩니다.

다음 다이어그램에서는 미러링 프로세스와 클러스터 간 통신 흐름을 보여줍니다.

:::image type="content" source="./media/apache-kafka-mirroring/kafka-mirroring-vnets2.png" alt-text="미러링 프로세스 다이어그램" border="false":::

기본 및 보조 클러스터는 노드와 파티션 수에 따라 다를 수 있으며 토픽 내의 오프셋도 다릅니다. 미러링은 분할에 사용되는 키 값을 유지하므로 레코드 순서는 키 기준으로 유지됩니다.

### <a name="mirroring-across-network-boundaries"></a>네트워크 경계를 넘은 미러링

다른 네트워크의 Kafka 클러스터 간에 미러링해야 하는 경우 다음과 같은 추가 고려 사항이 있습니다.

* **게이트웨이**: 네트워크는 TCP/IP 수준에서 통신할 수 있어야 합니다.

* **서버 주소 지정**: IP 주소 또는 정규화된 도메인 이름을 사용하여 클러스터 노드의 주소를 지정하도록 선택할 수 있습니다.

    * **IP 주소**: IP 주소 광고를 사용하도록 Kafka 클러스터를 구성하는 경우 broker 노드의 IP 주소 및 zookeeper 노드의 IP 주소를 사용하여 미러링 설정을 진행할 수 있습니다.
    
    * **도메인 이름**: IP 주소 광고에 대해 Kafka 클러스터를 구성하지 않으면 클러스터는 FQDN(정규화된 도메인 이름)을 사용하여 서로 연결할 수 있어야 합니다. 이렇게 하려면 요청을 다른 네트워크로 전달하도록 구성된 Domain Name System(DNS)서버가 각 네트워크에 필요할 수 있습니다. Azure Virtual Network를 만들 때 네트워크에서 제공되는 자동 DNS를 사용하는 대신 사용자 지정 DNS 서버와 해당 서버의 IP 주소를 지정해야 합니다. 가상 네트워크를 만든 후에는 해당 IP 주소를 사용하는 Azure Virtual Machine을 만든 다음 DNS 소프트웨어를 설치하고 구성해야 합니다.

    > [!WARNING]  
    > HDInsight를 Virtual Network에 설치하기 전에 사용자 지정 DNS 서버를 만들고 구성합니다. Virtual Network에 구성된 DNS 서버를 사용하기 위해 HDInsight를 추가로 구성할 필요는 없습니다.

두 개의 Azure Virtual Network 연결에 대한 자세한 내용은 [VNet 간 연결 구성](../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md)을 참조하세요.

## <a name="mirroring-architecture"></a>미러링 아키텍처

이 아키텍처는 서로 다른 리소스 그룹 및 가상 네트워크에 있는 두 개의 클러스터를 제공합니다: **기본** 및 **보조**

### <a name="creation-steps"></a>생성 단계

1. 두 리소스 그룹 생성:

    |리소스 그룹 | 위치 |
    |---|---|
    | kafka-primary-rg | 미국 중부 |
    | kafka-secondary-rg | 미국 중북부 |

1. **kafka-primary-rg** 에서 새 가상 네트워크 **kafka-primary-vnet** 을 만듭니다. 기본 설정을 그대로 둡니다.
1. **kafka-secondary-rg** 에 새 가상 네트워크 **kafka-secondary-vnet** 을 만들고, 역시 기본 설정대로 둡니다.

1. 두 개의 새 Kafka 클러스터 생성:

    | 클러스터 이름 | 리소스 그룹 | Virtual Network | 스토리지 계정 |
    |---|---|---|---|
    | kafka-primary-cluster | kafka-primary-rg | kafka-primary-vnet | kafkaprimarystorage |
    | kafka-secondary-cluster | kafka-secondary-rg | kafka-secondary-vnet | kafkasecondarystorage |

1. Virtual Network 피어링 생성. 이 단계에서는 두 개의 피어링을 만듭니다: 하나는 **kafka-primary-vnet** 에서 **kafka-secondary-vnet** 으로, 하나는 **kafka-secondary-vnet** 에서 **kafka-primary-vnet** 으로 만듭니다.
    1. **kafka-primary-vnet** 가상 네트워크를 선택합니다.
    1. **설정** 에서 **피어링** 을 선택합니다.
    1. **추가** 를 선택합니다.
    1. **피어링 추가** 화면에서 아래 스크린샷에 표시된 대로 세부 정보를 입력합니다.

        :::image type="content" source="./media/apache-kafka-mirroring/hdi-add-vnet-peering.png" alt-text="HDInsight Kafka add vnet peering" border="true":::

### <a name="configure-ip-advertising"></a>IP 보급 구성

클라이언트에서 도메인 이름 대신 broker IP 주소를 사용하여 연결할 수 있도록 IP 보급을 구성합니다.

1. 기본 클러스터에 대한 Ambari 대시보드로 이동합니다: `https://PRIMARYCLUSTERNAME.azurehdinsight.net`.
1. **서비스**  >  **kafka** 를 선택합니다. **Configs** 탭을 선택합니다.
1. 아래쪽 **kafka-env template** 섹션에 다음 구성 줄을 추가합니다. **저장** 을 선택합니다.

    ```
    # Configure Kafka to advertise IP addresses instead of FQDN
    IP_ADDRESS=$(hostname -i)
    echo advertised.listeners=$IP_ADDRESS
    sed -i.bak -e '/advertised/{/advertised@/!d;}' /usr/hdp/current/kafka-broker/conf/server.properties
    echo "advertised.listeners=PLAINTEXT://$IP_ADDRESS:9092" >> /usr/hdp/current/kafka-broker/conf/server.properties
    ```

1. **구성 저장** 화면에서 메모를 입력하고 **저장** 을 클릭합니다.
1. 구성 경고를 표시하는 메시지가 표시되면 **어쨌거나 진행** 을 클릭합니다.
1. **구성 변경 내용 저장** 에서 **확인** 을 선택합니다.
1. **다시 시작 필요 알림** 에서 **재시작** > **모든 영향을 다시 시작** 을 선택합니다. **모두 다시 시작 확인** 을 선택합니다.

    :::image type="content" source="./media/apache-kafka-mirroring/ambari-restart-notification.png" alt-text="`Apache Ambari에 영향을 받는 모든 서비스 다시 시작" border="true":::

### <a name="configure-kafka-to-listen-on-all-network-interfaces"></a>모든 네트워크 인터페이스에서 수신 대기하도록 Kafka을 구성합니다.
    
1. **서비스**  >  **kafka** 에서 **Configs** 탭을 그대로 유지합니다. **Kafka Broker** 에서 **수신기** 속성을 `PLAINTEXT://0.0.0.0:9092`로 설정합니다.
1. **저장** 을 선택합니다.
1. **다시 시작** 을 선택하고, **모두 다시 시작** 을 확인합니다.

### <a name="record-broker-ip-addresses-and-zookeeper-addresses-for-primary-cluster"></a>기본 클러스터에 대한 Broker IP 주소 및 Zookeeper 주소를 기록합니다.

1. Ambari 대시보드에서 **호스트** 를 선택합니다.
1. Brokers 및 Zookeeper의 IP 주소를 기록해 둡니다. broker 노드는 호스트 이름의 처음 두 문자로 **wn**, zookeeper 노드에는 호스트 이름의 처음 두 문자로 **zk** 가 있습니다.

    :::image type="content" source="./media/apache-kafka-mirroring/view-node-ip-addresses2.png" alt-text="Apache Ambari view 노드 ip 주소" border="true":::

1. 두 번째 클러스터에 대해 앞의 세 단계를 반복 합니다 **kafka-secondary-cluster**: IP 보급을 구성하고, 수신기를 설정하고, Broker 및 Zookeeper ip 주소를 기록합니다.

## <a name="create-topics"></a>토픽 만들기

1. **SSH** 를 사용하여 클러스터에 연결합니다.

    ```bash
    ssh sshuser@PRIMARYCLUSTER-ssh.azurehdinsight.net
    ```

    **sshuser** 은 클러스터를 만들 때 사용한 SSH 사용자 이름으로 바꿉니다. **PRIMARYCLUSTER** 은 클러스터를 만들 때 사용한 기본 이름으로 바꿉니다.

    자세한 내용은 [HDInsight와 함께 SSH 사용](../hdinsight-hadoop-linux-use-ssh-unix.md)을 참조하세요.

1. 다음 명령을 사용하여 Apache Zookeeper 호스트 및 주 클러스터에 대한 Broker 호스트를 사용하여 두 개의 환경 변수를 만듭니다. `ZOOKEEPER_IP_ADDRESS1`과 같은 문자열은 이전에 기록된 `10.23.0.11`와 `10.23.0.7`과 같은 실제 IP 주소로 바꾸어야 합니다 . 에도 `BROKER_IP_ADDRESS1` 마찬가지입니다. 사용자 지정 DNS 서버에서 FQDN 해결을 사용하는 경우, [다음 단계](apache-kafka-get-started.md#getkafkainfo)에 따라 broker 및 zookeeper 이름을 가져옵니다.

    ```bash
    # get the zookeeper hosts for the primary cluster
    export PRIMARY_ZKHOSTS='ZOOKEEPER_IP_ADDRESS1:2181, ZOOKEEPER_IP_ADDRESS2:2181, ZOOKEEPER_IP_ADDRESS3:2181'
    
    # get the broker hosts for the primary cluster
    export PRIMARY_BROKERHOSTS='BROKER_IP_ADDRESS1:9092,BROKER_IP_ADDRESS2:9092,BROKER_IP_ADDRESS2:9092'
    ```

1. `testtopic`이라는 토픽을 만들려면 다음 명령을 사용합니다.

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 2 --partitions 8 --topic testtopic --zookeeper $PRIMARY_ZKHOSTS
    ```

1. 다음 명령을 사용하여 토픽이 만들어졌는지 확인합니다.

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --list --zookeeper $PRIMARY_ZKHOSTS
    ```

    응답에 `testtopic`이 있습니다.

1. 이 **클러스터(기본**) 클러스터에 대한 Broker 호스트 정보를 보려면 다음을 사용합니다.

    ```bash
    echo $PRIMARY_BROKERHOSTS
    ```

    이 명령은 다음 텍스트와 비슷한 정보를 반환합니다.

    `10.23.0.11:9092,10.23.0.7:9092,10.23.0.9:9092`

    이 정보를 저장합니다. 해당 정보는 다음 섹션에서 사용됩니다.

## <a name="configure-mirroring"></a>미러링 구성

1. 다른 SSH 세션을 사용하여 **보조** 클러스터에 연결합니다.

    ```bash
    ssh sshuser@SECONDARYCLUSTER-ssh.azurehdinsight.net
    ```

    **sshuser** 은 클러스터를 만들 때 사용한 SSH 사용자 이름으로 바꿉니다. **SECONDARYCLUSTER** 은 클러스터를 만들 때 사용한 기본 이름으로 바꿉니다.

    자세한 내용은 [HDInsight와 함께 SSH 사용](../hdinsight-hadoop-linux-use-ssh-unix.md)을 참조하세요.

1. `consumer.properties` 파일은 **기본** 클러스터와의 통신을 구성하는 데 사용됩니다. 파일을 만들려면 다음 명령을 사용합니다.

    ```bash
    nano consumer.properties
    ```

    `consumer.properties` 파일의 내용으로 다음 텍스트를 사용합니다.

    ```yaml
    bootstrap.servers=PRIMARY_BROKERHOSTS
    group.id=mirrorgroup
    ```

    **PRIMARY_BROKERHOSTS** **주** 클러스터의 Broker 호스트 IP 주소로 대체합니다.

    이 파일은 기본 Kafka 클러스터에서 읽을 때 사용할 소비자 정보를 설명합니다. 소비자 구성에 대한 자세한 내용은 kafka.apache.org의 [소비자 구성](https://kafka.apache.org/documentation#consumerconfigs)(영문)을 참조하세요.

    파일을 저장하려면 **Ctrl + X**, **Y** 및 **Enter** 키를 사용합니다.

1. 보조 클러스터와 통신하는 프로듀서를 구성하기 전에 **보조** 클러스터의 broker IP 주소에 대한 변수를 설정합니다. 다음 명령을 사용하여 이러한 변수를 만듭니다:

    ```bash
    export SECONDARY_BROKERHOSTS='BROKER_IP_ADDRESS1:9092,BROKER_IP_ADDRESS2:9092,BROKER_IP_ADDRESS2:9092'
    ```

    명령은 다음 텍스트와 비슷한 정보를 반환해야 합니다.

    `10.23.0.14:9092,10.23.0.4:9092,10.23.0.12:9092`

1. `producer.properties` 파일은 **보조** 클러스터와 통신하는 데 사용됩니다. 파일을 만들려면 다음 명령을 사용합니다.

    ```bash
    nano producer.properties
    ```

    `producer.properties` 파일의 내용으로 다음 텍스트를 사용합니다.

    ```yaml
    bootstrap.servers=SECONDARY_BROKERHOSTS
    compression.type=none
    ```

    **SECONDARY_BROKERHOSTS** 을 이전 단계에서 사용된 broker IP 주소로 바꿉니다.

    생산자 구성에 대한 자세한 내용은 kafka.apache.org의 [생산자 구성](https://kafka.apache.org/documentation#producerconfigs)(영문)을 참조하세요.

1. 다음 명령을 사용하여 보조 클러스터용 Zookeeper 호스트의 IP 주소와 함께 환경 변수를 만듭니다.

    ```bash
    # get the zookeeper hosts for the secondary cluster
    export SECONDARY_ZKHOSTS='ZOOKEEPER_IP_ADDRESS1:2181,ZOOKEEPER_IP_ADDRESS2:2181,ZOOKEEPER_IP_ADDRESS3:2181'
    ```

1. HDInsight의 Kafka에 대한 기본 구성이 토픽의 자동 생성을 허용하지 않습니다. 미러링 프로세스를 시작하기 전에 다음 옵션 중 하나를 사용해야 합니다.

    * **보조 클러스터에 토픽 생성하기**: 이 옵션을 사용하면 파티션 및 복제 요소의 수를 설정할 수 있습니다.

        다음 명령을 사용하여 사전에 토픽을 만들 수 있습니다.

        ```bash
        /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 2 --partitions 8 --topic testtopic --zookeeper $SECONDARY_ZKHOSTS
        ```

        `testtopic`을 만들려는 토픽의 이름으로 바꿉니다.

    * **자동 토픽 생성을 위한 클러스터 구성**: 이 옵션을 사용하면 MirrorMaker에서 자동으로 토픽을 만들 수 있지만, 기본 토픽과는 다른 파티션 또는 복제 요소의 수로 만들 수 있습니다.

        자동으로 토픽을 생성하도록 보조 클러스터를 구성하려면 이러한 단계를 수행합니다.

        1. 보조 클러스터에 대한 Ambari 대시보드로 이동합니다: `https://SECONDARYCLUSTERNAME.azurehdinsight.net`.
        1. **Services**  >  **kafka** 를 클릭합니다. **Configs** 탭을 클릭합니다.
        1. __필터__ 필드에 `auto.create` 값을 입력합니다. 이렇게 하면 속성 목록이 필터링되고 `auto.create.topics.enable` 설정이 표시됩니다.
        1. `auto.create.topics.enable` 값을 true로 변경하고 __저장__ 을 선택합니다. 메모를 추가하고 __저장__ 을 다시 선택합니다.
        1. __Kafka__ 서비스를 선택하고 __다시 시작__ 을 선택한 후 __영향을 받는 모든 서비스 다시 시작__ 을 선택합니다. 메시지가 나타나면 __모두 다시 시작 확인__ 을 선택합니다.

        :::image type="content" source="./media/apache-kafka-mirroring/kafka-enable-auto-create-topics.png" alt-text="kafka 자동 생성 항목 사용" border="true":::

## <a name="start-mirrormaker"></a>MirrorMaker 시작

1. **보조** 클러스터에 대한 SSH 연결에서 다음 명령을 사용하여 MirrorMaker 프로세스를 시작합니다.

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-run-class.sh kafka.tools.MirrorMaker --consumer.config consumer.properties --producer.config producer.properties --whitelist testtopic --num.streams 4
    ```

    이 예제에 사용된 매개 변수는 다음과 같습니다.

    |매개 변수 |Description |
    |---|---|
    |--consumer.config|소비자 속성이 포함된 파일을 지정합니다. 이러한 속성은 *기본* Kafka 클러스터에서 읽어 들이는 소비자를 만드는 데 사용됩니다.|
    |--producer.config|생산자 속성이 포함된 파일을 지정합니다. 이러한 속성은 *보조* Kafka 클러스터에 쓰는 프로듀서를 만드는 데 사용됩니다.|
    |--whitelist|MirrorMaker로 기본 클러스터에서 보조 클러스터로 복제하는 토픽의 목록.|
    |--num.streams|생성할 소비자 스레드 수입니다.|

    보조 노드의 소비자는 이제 메시지 수신을 대기하고 있습니다.

2. SSH 연결부터 **기본** 클러스터까지, 다음 명령을 사용하여 Producer를 시작하고 토픽에 메시지를 전송합니다.

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-producer.sh --broker-list $PRIMARY_BROKERHOSTS --topic testtopic
    ```

     커서로 빈 라인에 도달하면 몇 개의 텍스트 메시지를 입력합니다. 메시지는 **기본** 클러스터의 토픽으로 보내집니다. 완료되면 **Ctrl + C** 를 클릭하여 프로듀서 프로세스를 종료합니다.

3. **보조** 클러스터에 대한 SSH 연결에서 **Ctrl + C** 를 사용하여 MirrorMaker 프로세스를 종료합니다. 이 프로세스를 종료하는 데 몇 초 정도 걸릴 수 있습니다. 메시지가 보조에 복제되었는지 확인하려면 다음 명령을 사용합니다.

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-consumer.sh --bootstrap-server $SECONDARY_BROKERHOSTS --topic testtopic --from-beginning
    ```

    이제 토픽 목록에 MirrorMaster가 기본 클러스터에서 보조로 토픽을 미러링할 때 만들어진 `testtopic`이(가) 포함됩니다. 이 항목에서 검색한 메시지는 기본 클러스터에 입력한 것과 동일합니다.

## <a name="delete-the-cluster"></a>클러스터 삭제

[!INCLUDE [delete-cluster-warning](../includes/hdinsight-delete-cluster-warning.md)]

이 문서의 단계는 다른 Azure 리소스 그룹의 클러스터를 만들었습니다. 만든 리소스를 모두 삭제하려면 **kafka-primary-rg** 및 **kafka-secondary_rg** 의 두 리소스 그룹을 삭제하면 됩니다. 리소스 그룹을 삭제하면 클러스터, 가상 네트워크와 스토리지 계정을 포함하여 이 문서를 따라 만든 모든 리소스가 제거됩니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 [MirrorMaker](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330)를 사용하여 [Apache Kafka](https://kafka.apache.org/) 클러스터 복제본을 만드는 방법을 알아보았습니다. Kafka를 사용하는 다른 방법을 찾으려면 다음 링크를 사용하세요.

* [Apache Kafka MirrorMaker 문서](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330)(cwiki.apache.org)
* [Kafka Mirror Maker 모범 사례](https://community.cloudera.com/t5/Community-Articles/Kafka-Mirror-Maker-Best-Practices/ta-p/249269)
* [HDInsight에서 Apache Kafka 시작](apache-kafka-get-started.md)
* [HDInsight에서 Apache Spark 및 Apache Kafka 사용](../hdinsight-apache-spark-with-kafka.md)
* [Azure Virtual Network를 통해 Apache Kafka에 연결](apache-kafka-connect-vpn-gateway.md)
