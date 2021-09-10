---
title: Azure의 개발 가상 머신에서 ElasticSearch 배포
description: Azure에서 개발 Linux VM에 ELK(Elastic Stack) 설치
services: virtual-machines
author: rloutlaw
manager: justhe
ms.service: virtual-machines
ms.collection: linux
ms.workload: infrastructure-services
ms.devlang: azurecli
ms.topic: how-to
ms.date: 10/11/2017
ms.author: routlaw
ms.openlocfilehash: ec348f462255a07841cfd794a3054292d2592f95
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/23/2021
ms.locfileid: "122697733"
---
# <a name="install-the-elastic-stack-elk-on-an-azure-vm"></a>Azure VM에 ELK(Elastic Stack) 설치

**적용 대상:** :heavy_check_mark: Linux VM :heavy_check_mark: 유연한 확장 집합 

이 문서에서는 Azure의 Ubuntu VM에서 [Elasticsearch](https://www.elastic.co/products/elasticsearch), [Logstash](https://www.elastic.co/products/logstash) 및 [Kibana](https://www.elastic.co/products/kibana)를 배포하는 방법을 설명합니다. Elastic Stack이 작동하는지 보려면 Kibana에 선택적으로 연결하여 샘플 로깅 데이터로 작업합니다. 

이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * Azure 리소스 그룹에서 Ubuntu VM 만들기
> * VM에 Elasticsearch, Logstash 및 Kibana 설치
> * Logstash로 샘플 데이터를 Elasticsearch에 보내기 
> * 포트를 열고 Kibana 콘솔에서 데이터 작업


 이 배포는 Elastic Stack을 사용한 기본 개발에 적합합니다. 프로덕션 환경에 대한 권장 사항을 포함하여 Elastic Stack에 대한 자세한 내용은 [Elastic 설명서](https://www.elastic.co/guide/index.html)와 [Azure Architecture Center](/azure/architecture/elasticsearch/)를 참조하세요.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

- 이 문서에는 Azure CLI 버전 2.0.4 이상이 필요합니다. Azure Cloud Shell을 사용하는 경우 최신 버전이 이미 설치되어 있습니다.

## <a name="create-a-resource-group"></a>리소스 그룹 만들기

[az group create](/cli/azure/group) 명령을 사용하여 리소스 그룹을 만듭니다. Azure 리소스 그룹은 Azure 리소스가 배포 및 관리되는 논리적 컨테이너입니다. 

다음 예제에서는 *eastus* 위치에 *myResourceGroup* 이라는 리소스 그룹을 만듭니다.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-virtual-machine"></a>가상 머신 만들기

[az vm create](/cli/azure/vm) 명령을 사용하여 VM을 만듭니다. 

다음 예제에서는 *myVM* 이라는 VM을 만들고 기본 키 위치에 SSH 키가 없는 경우 이 키를 만듭니다. 특정 키 집합을 사용하려면 `--ssh-key-value` 옵션을 사용합니다.  

```azurecli-interactive
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys
```

VM을 만든 경우 Azure CLI는 다음 예제와 비슷한 정보를 표시합니다. `publicIpAddress`을 기록해 둡니다. 이 주소는 VM에 액세스하는 데 사용됩니다.

```output
{
  "fqdns": "",
  "id": "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "eastus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "40.68.254.142",
  "resourceGroup": "myResourceGroup"
}
```

## <a name="ssh-into-your-vm"></a>VM에 SSH 수행

VM의 공용 IP 주소를 알고 있는 경우 [az network public-ip list](/cli/azure/network/public-ip) 명령을 실행합니다.

```azurecli-interactive
az network public-ip list --resource-group myResourceGroup --query [].ipAddress
```

다음 명령을 사용하여 가상 머신과의 SSH 세션을 만듭니다. 가상 머신의 올바른 공용 IP 주소로 대체합니다. 이 예제에서 IP 주소는 *40.68.254.142* 입니다.

```bash
ssh azureuser@40.68.254.142
```

## <a name="install-the-elastic-stack"></a>Elastic Stack 설치

Elasticsearch 서명 키를 가져오고 Elastic 패키지 리포지토리를 포함하도록 APT 원본 목록을 업데이트합니다.

```bash
wget -qO - https://artifacts.elastic.co/GPG-KEY-elasticsearch | sudo apt-key add -
echo "deb https://artifacts.elastic.co/packages/5.x/apt stable main" | sudo tee -a /etc/apt/sources.list.d/elastic-5.x.list
```

VM에 Java Virtual을 설치하고 JAVA_HOME 변수를 구성합니다. Elastic Stack 구성 요소를 실행하는 데 필요합니다.

```bash
sudo apt update && sudo apt install openjdk-8-jre-headless
export JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64
```

다음 명령을 실행하여 Ubuntu 패키지 원본을 업데이트하고 Elasticsearch, Kibana 및 Logstash를 설치합니다.

```bash
sudo apt update && sudo apt install elasticsearch kibana logstash   
```

> [!NOTE]
> 디렉터리 레이아웃 및 초기 구성을 비롯한 자세한 설치 지침은 [Elastic의 설명서](https://www.elastic.co/guide/en/elastic-stack/current/installing-elastic-stack.html)에 유지 관리됩니다.

## <a name="start-elasticsearch"></a>Elasticsearch 시작 

다음 명령으로 VM에서 Elasticsearch를 시작합니다.

```bash
sudo systemctl start elasticsearch.service
```

이 명령은 출력을 생성하지 않으므로 `curl` 명령을 사용하여 Elasticsearch가 VM에서 실행되고 있는지 확인합니다.

```bash
sudo curl -XGET 'localhost:9200/'
```

Elasticsearch가 실행 중이면 다음과 같은 출력이 표시됩니다.

```json
{
  "name" : "w6Z4NwR",
  "cluster_name" : "elasticsearch",
  "cluster_uuid" : "SDzCajBoSK2EkXmHvJVaDQ",
  "version" : {
    "number" : "5.6.3",
    "build_hash" : "1a2f265",
    "build_date" : "2017-10-06T20:33:39.012Z",
    "build_snapshot" : false,
    "lucene_version" : "6.6.1"
  },
  "tagline" : "You Know, for Search"
}
```

## <a name="start-logstash-and-add-data-to-elasticsearch"></a>Logstash를 시작하고 Elasticsearch에 데이터를 추가합니다.

다음 명령으로 Logstash를 시작합니다.

```bash 
sudo systemctl start logstash.service
```

Logstash를 대화형 모드로 테스트하여 올바르게 작동하는지 확인합니다.

```bash
sudo /usr/share/logstash/bin/logstash -e 'input { stdin { } } output { stdout {} }'
```

다음은 표준 출력에 표준 입력을 반향하는 기본 Logstash [파이프 라인](https://www.elastic.co/guide/en/logstash/5.6/pipeline.html)입니다. 

```output
The stdin plugin is now waiting for input:
hello azure
2017-10-11T20:01:08.904Z myVM hello azure
```

이 VM의 커널 메시지를 Elasticsearch로 전달하려면 Logstash를 설정합니다. 빈 디렉터리에 `vm-syslog-logstash.conf`라는 새 파일을 만들고 다음 Logstash 구성을 붙여넣습니다.

```Logstash
input {
    stdin {
        type => "stdin-type"
    }

    file {
        type => "syslog"
        path => [ "/var/log/*.log", "/var/log/*/*.log", "/var/log/messages", "/var/log/syslog" ]
        start_position => "beginning"
    }
}

output {

    stdout {
        codec => rubydebug
    }
    elasticsearch {
        hosts  => "localhost:9200"
    }
}
```

이 구성을 테스트하고 syslog 데이터를 Elasticsearch로 보냅니다.

```bash
sudo /usr/share/logstash/bin/logstash -f vm-syslog-logstash.conf
```

터미널의 syslog 항목이 Elasticsearch로 전송 될 때 반향되는 것을 볼 수 있습니다. 데이터를 보내고 나면 `CTRL+C`를 사용하여 Logstash를 종료합니다.

## <a name="start-kibana-and-visualize-the-data-in-elasticsearch"></a>Kibana를 시작하고 Elasticsearch에서 데이터 시각화

`/etc/kibana/kibana.yml`을 수정하고 Kibana가 수신 대기하는 IP 주소를 웹 브라우저에서 액세스할 수 있도록 변경합니다.

```bash
server.host: "0.0.0.0"
```

다음 명령으로 Kibana를 시작합니다.

```bash
sudo systemctl start kibana.service
```

Azure CLI에서 포트 5601을 열어 Kibana 콘솔에 대한 원격 액세스를 허용합니다.

```azurecli-interactive
az vm open-port --port 5601 --resource-group myResourceGroup --name myVM
```

Kibana 콘솔을 열고 **만들기** 를 선택하여 이전에 Elasticsearch에 보낸 syslog 데이터를 기반으로 기본 인덱스를 생성합니다. 

![Kibana 콘솔을 보여주고 만들기 단추를 강조 표시하는 스크린샷.](media/elasticsearch-install/kibana-index.png)

Kibana 콘솔에서 **검색** 을 선택하여 syslog 이벤트를 검색, 탐색 및 필터링합니다.

![Kibana에서 Syslog 이벤트 찾아보기](media/elasticsearch-install/kibana-search-filter.png)

## <a name="next-steps"></a>다음 단계

이 자습서에서는 Azure의 개발 VM에 Elastic Stack을 배포했습니다. 구체적으로 다음 작업 방법을 알아보았습니다.

> [!div class="checklist"]
> * Azure 리소스 그룹에서 Ubuntu VM 만들기
> * VM에 Elasticsearch, Logstash 및 Kibana 설치
> * Logstash에서 Elasticsearch로 샘플 데이터 보내기 
> * 포트를 열고 Kibana 콘솔에서 데이터 작업
