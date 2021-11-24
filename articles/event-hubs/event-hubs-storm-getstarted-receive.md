---
title: '빠른 시작: Apache Storm을 사용하여 이벤트 수신 - Azure Event Hubs'
description: '빠른 시작: 이 문서에서는 Apache Storm을 사용하여 Azure Event Hubs에서 이벤트를 수신하는 방법에 대한 정보를 제공합니다.'
ms.topic: quickstart
ms.date: 09/28/2021
ms.custom: mode-other
ms.openlocfilehash: c3729d2478d1bb28f142e78b68fc47f7b0eed3bd
ms.sourcegitcommit: 56235f8694cc5f88db3afcc8c27ce769ecf455b0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133041792"
---
# <a name="quickstart-receive-events-from-event-hubs-using-apache-storm"></a>빠른 시작: Apache Storm을 사용하여 Event Hubs에서 이벤트 수신

[Apache Storm](https://storm.incubator.apache.org)은 분산된 실시간 계산 시스템으로, 바인딩되지 않은 데이터 스트림의 안정적인 처리를 간소화합니다. 이 섹션에서는 Azure Event Hubs Storm Spout를 사용하여 Event Hubs에서 이벤트를 수신하는 방법을 보여 줍니다. Apache Storm을 사용하면 다른 노드에 호스트된 여러 프로세스 간에 이벤트를 분할할 수 있습니다. Event Hubs와 Storm을 통합하면 Storm의 Zookeeper 설치를 통해 진행률을 투명하게 확인하고 지속적인 검사점을 관리하여 이벤트 사용이 간소화되고 Event Hubs에서 병렬 수신됩니다.

Event Hubs 수신기 패턴에 대한 자세한 내용은 [Event Hubs 개요][Event Hubs overview]를 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항
이 빠른 시작을 시작하기 전에 **Event Hubs 네임스페이스 및 이벤트 허브를 만듭니다**. [Azure Portal](https://portal.azure.com)을 사용하여 Event Hubs 형식의 네임스페이스를 만들고, 애플리케이션에서 이벤트 허브와 통신하는 데 필요한 관리 자격 증명을 얻습니다. 네임스페이스 및 이벤트 허브를 만들려면 [이 문서](event-hubs-create.md)의 절차를 따릅니다. 

## <a name="create-project-and-add-code"></a>프로젝트 만들기 및 코드 추가

이 자습서에서는 이미 사용할 수 있는 Event Hubs spout와 함께 제공되는 [HDInsight Storm][HDInsight Storm] 설치를 사용합니다.

1. [HDInsight Storm - 시작 절차](../hdinsight/storm/apache-storm-overview.md)에 따라 새 HDInsight 클러스터를 만들고 원격 데스크톱을 통해 이 클러스터에 연결합니다.
2. `%STORM_HOME%\examples\eventhubspout\eventhubs-storm-spout-0.9-jar-with-dependencies.jar` 파일을 로컬 개발 환경에 복사합니다. 이 파일에는 events-storm-spout가 포함되어 있습니다.
3. 다음 명령을 사용하여 패키지를 로컬 Maven 저장소에 설치합니다. 그러면 이후 단계에서 Storm 프로젝트에 참조로 추가할 수 있습니다.

    ```shell
    mvn install:install-file -Dfile=target\eventhubs-storm-spout-0.9-jar-with-dependencies.jar -DgroupId=com.microsoft.eventhubs -DartifactId=eventhubs-storm-spout -Dversion=0.9 -Dpackaging=jar
    ```
4. Eclipse에서 새 Maven 프로젝트를 만듭니다(**파일**, **새로 만들기**, **프로젝트** 를 차례로 클릭).
   
    ![파일 > 새로 만들기 > 프로젝트][12]
5. **Use default Workspace location**(기본 작업 영역 위치 사용)을 선택하고 **다음** 을 클릭합니다.
6. **maven-archetype-quickstart** 원형을 선택하고 **다음** 을 클릭합니다.
7. **GroupId** 및 **ArtifactId** 를 삽입하고 **마침** 을 클릭합니다.
8. **pom.xml** 에서 `<dependency>` 노드에 다음 종속성을 추가합니다.

    ```xml  
    <dependency>
        <groupId>org.apache.storm</groupId>
        <artifactId>storm-core</artifactId>
        <version>0.9.2-incubating</version>
        <scope>provided</scope>
    </dependency>
    <dependency>
        <groupId>com.microsoft.eventhubs</groupId>
        <artifactId>eventhubs-storm-spout</artifactId>
        <version>0.9</version>
    </dependency>
    <dependency>
        <groupId>com.netflix.curator</groupId>
        <artifactId>curator-framework</artifactId>
        <version>1.3.3</version>
        <exclusions>
            <exclusion>
                <groupId>log4j</groupId>
                <artifactId>log4j</artifactId>
            </exclusion>
            <exclusion>
                <groupId>org.slf4j</groupId>
                <artifactId>slf4j-log4j12</artifactId>
            </exclusion>
        </exclusions>
        <scope>provided</scope>
    </dependency>
    ```

9. **src** 폴더에서 **Config.properties** 라는 파일을 만들고 다음 콘텐츠를 복사하여 `receive rule key` 및 `event hub name` 값으로 대체합니다.

    ```java
    eventhubspout.username = ReceiveRule
    eventhubspout.password = {receive rule key}
    eventhubspout.namespace = ioteventhub-ns
    eventhubspout.entitypath = {event hub name}
    eventhubspout.partitions.count = 16
       
    # if not provided, will use storm's zookeeper settings
    # zookeeper.connectionstring=localhost:2181
       
    eventhubspout.checkpoint.interval = 10
    eventhub.receiver.credits = 10
    ```
    **eventhub.receiver.credits** 의 값에 따라 이벤트를 Storm 파이프라인으로 릴리스하기 전에 얼마나 많은 수의 이벤트가 일괄 처리되는지 결정됩니다. 간단히 하기 위해 이 예에서는 이 값을 10으로 설정합니다. 프로덕션 환경에서 일반적으로 더 높게(예: 1024) 설정해야 합니다.
10. 다음 코드를 포함하는 **LoggerBolt** 클래스를 새로 만듭니다.
    
    ```java
    import java.util.Map;
    import org.slf4j.Logger;
    import org.slf4j.LoggerFactory;
    import backtype.storm.task.OutputCollector;
    import backtype.storm.task.TopologyContext;
    import backtype.storm.topology.OutputFieldsDeclarer;
    import backtype.storm.topology.base.BaseRichBolt;
    import backtype.storm.tuple.Tuple;
    
    public class LoggerBolt extends BaseRichBolt {
        private OutputCollector collector;
        private static final Logger logger = LoggerFactory
                  .getLogger(LoggerBolt.class);
    
        @Override
        public void execute(Tuple tuple) {
            String value = tuple.getString(0);
            logger.info("Tuple value: " + value);
   
            collector.ack(tuple);
        }
   
        @Override
        public void prepare(Map map, TopologyContext context, OutputCollector collector) {
            this.collector = collector;
            this.count = 0;
        }
        
        @Override
        public void declareOutputFields(OutputFieldsDeclarer declarer) {
            // no output fields
        }
    
    }
    ```
    
    이 Storm 볼트는 수신된 이벤트의 콘텐츠를 기록합니다. 튜플을 스토리지 서비스에 저장하도록 간단히 확장할 수 있습니다. [이벤트 허브를 사용한 HDInsight Storm 예제]에서도 이와 동일한 방법을 사용하여 Azure Storage 및 Power BI에 데이터를 저장합니다.
11. 다음 코드를 포함하는 **LogTopology** 라는 클래스를 만듭니다.
    
    ```java
    import java.io.FileReader;
    import java.util.Properties;
    import backtype.storm.Config;
    import backtype.storm.LocalCluster;
    import backtype.storm.StormSubmitter;
    import backtype.storm.generated.StormTopology;
    import backtype.storm.topology.TopologyBuilder;
    import com.microsoft.eventhubs.samples.EventCount;
    import com.microsoft.eventhubs.spout.EventHubSpout;
    import com.microsoft.eventhubs.spout.EventHubSpoutConfig;
        
    public class LogTopology {
        protected EventHubSpoutConfig spoutConfig;
        protected int numWorkers;
        
        protected void readEHConfig(String[] args) throws Exception {
            Properties properties = new Properties();
            if (args.length > 1) {
                properties.load(new FileReader(args[1]));
            } else {
                properties.load(EventCount.class.getClassLoader()
                        .getResourceAsStream("Config.properties"));
            }
        
            String username = properties.getProperty("eventhubspout.username");
            String password = properties.getProperty("eventhubspout.password");
            String namespaceName = properties
                    .getProperty("eventhubspout.namespace");
            String entityPath = properties.getProperty("eventhubspout.entitypath");
            String zkEndpointAddress = properties
                    .getProperty("zookeeper.connectionstring"); // opt
            int partitionCount = Integer.parseInt(properties
                    .getProperty("eventhubspout.partitions.count"));
            int checkpointIntervalInSeconds = Integer.parseInt(properties
                    .getProperty("eventhubspout.checkpoint.interval"));
            int receiverCredits = Integer.parseInt(properties
                    .getProperty("eventhub.receiver.credits")); // prefetch count
                                                                // (opt)
            System.out.println("Eventhub spout config: ");
            System.out.println("  partition count: " + partitionCount);
            System.out.println("  checkpoint interval: "
                    + checkpointIntervalInSeconds);
            System.out.println("  receiver credits: " + receiverCredits);
     
            spoutConfig = new EventHubSpoutConfig(username, password,
                    namespaceName, entityPath, partitionCount, zkEndpointAddress,
                    checkpointIntervalInSeconds, receiverCredits);
        
            // set the number of workers to be the same as partition number.
            // the idea is to have a spout and a logger bolt co-exist in one
            // worker to avoid shuffling messages across workers in storm cluster.
            numWorkers = spoutConfig.getPartitionCount();
        
            if (args.length > 0) {
                // set topology name so that sample Trident topology can use it as
                // stream name.
                spoutConfig.setTopologyName(args[0]);
            }
        }
        
        protected StormTopology buildTopology() {
            TopologyBuilder topologyBuilder = new TopologyBuilder();
       
            EventHubSpout eventHubSpout = new EventHubSpout(spoutConfig);
            topologyBuilder.setSpout("EventHubsSpout", eventHubSpout,
                    spoutConfig.getPartitionCount()).setNumTasks(
                    spoutConfig.getPartitionCount());
            topologyBuilder
                    .setBolt("LoggerBolt", new LoggerBolt(),
                            spoutConfig.getPartitionCount())
                    .localOrShuffleGrouping("EventHubsSpout")
                    .setNumTasks(spoutConfig.getPartitionCount());
            return topologyBuilder.createTopology();
        }
        
        protected void runScenario(String[] args) throws Exception {
            boolean runLocal = true;
            readEHConfig(args);
            StormTopology topology = buildTopology();
            Config config = new Config();
            config.setDebug(false);
        
            if (runLocal) {
                config.setMaxTaskParallelism(2);
                LocalCluster localCluster = new LocalCluster();
                localCluster.submitTopology("test", config, topology);
                Thread.sleep(5000000);
                localCluster.shutdown();
            } else {
                config.setNumWorkers(numWorkers);
                StormSubmitter.submitTopology(args[0], config, topology);
            }
        }
        
        public static void main(String[] args) throws Exception {
            LogTopology topology = new LogTopology();
            topology.runScenario(args);
        }
    }
    ```

    이 클래스는 새 Event Hubs spout를 만들고, 구성 파일의 속성을 사용하여 spout를 인스턴스화합니다. 이 예에서는 이벤트 허브에서 허용하는 최대 병렬 처리를 사용하기 위해 이벤트 허브의 파티션 수만큼 Spout 작업을 만듭니다.

## <a name="next-steps"></a>다음 단계
Event Hubs에 대한 자세한 내용은 다음 링크를 참조하세요.

* [Event Hubs 개요][Event Hubs overview]
* [이벤트 허브 만들기](event-hubs-create.md)
* [Event Hubs FAQ](event-hubs-faq.yml)

<!-- Links -->
[Event Hubs overview]: ./event-hubs-about.md
[HDInsight Storm]: ../hdinsight/storm/apache-storm-overview.md
[이벤트 허브를 사용한 HDInsight Storm 예제]: https://github.com/Azure-Samples/hdinsight-java-storm-eventhub

<!-- Images -->

[12]: ./media/event-hubs-get-started-receive-storm/create-storm1.png
