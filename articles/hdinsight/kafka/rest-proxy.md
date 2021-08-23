---
title: Apache Kafka REST 프록시 - Azure HDInsight
description: Azure HDInsight에서 Kafka REST 프록시를 사용하여 Apache Kafka 작업을 실행하는 방법을 알아봅니다.
ms.service: hdinsight
ms.topic: how-to
ms.custom: devx-track-python
ms.date: 04/03/2020
ms.openlocfilehash: 634526e8a136023368a06887af648ca3925151a0
ms.sourcegitcommit: ddac53ddc870643585f4a1f6dc24e13db25a6ed6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/18/2021
ms.locfileid: "122538906"
---
# <a name="interact-with-apache-kafka-clusters-in-azure-hdinsight-using-a-rest-proxy"></a>REST 프록시를 사용하여 Azure HDInsight에서 Apache Kafka 클러스터와 상호 작용

Kafka REST 프록시를 사용하면 HTTPS를 통한 REST API를 통해 Kafka 클러스터와 상호 작용할 수 있습니다. 이 작업을 통해 Kafka 클라이언트는 가상 네트워크 외부에 있을 수 있습니다. 클라이언트는 Kafka 라이브러리를 사용하는 대신 Kafka 클러스터에 간단하고 안전한 HTTPS 호출을 수행할 수 있습니다. 이 문서에서는 REST 프록시 사용 Kafka 클러스터를 만드는 방법을 보여줍니다. 또한 REST 프록시를 호출하는 방법을 보여주는 샘플 코드도 제공합니다.

## <a name="rest-api-reference"></a>REST API 참조

Kafka REST API에서 지원하는 작업의 경우 [HDInsight Kafka REST 프록시 API 참조](/rest/api/hdinsight-kafka-rest-proxy)를 확인하세요.

## <a name="background"></a>배경

:::image type="content" source="./media/rest-proxy/rest-proxy-architecture.png" alt-text="Kafka REST 프록시 설계" border="false":::

API에서 지원되는 작업의 전체 사양은 [Apache Kafka REST 프록시 API](/rest/api/hdinsight-kafka-rest-proxy)를 참조하세요.

### <a name="rest-proxy-endpoint"></a>REST 프록시 엔드포인트

REST 프록시를 사용하여 HDInsight Kafka 클러스터를 만들면 클러스터에 대한 새 퍼블릭 엔드포인트를 만듭니다. 이 엔드포인트는 Azure Portal의 HDInsight 클러스터 **속성** 에서 찾을 수 있습니다.

### <a name="security"></a>보안

Kafka REST 프록시에 대한 액세스는 Azure Active Directory 보안 그룹을 사용하여 관리됩니다. Kafka 클러스터를 만들 때 REST 엔드포인트 액세스를 사용하여 Azure AD 보안 그룹을 제공합니다. REST 프록시에 대한 액세스가 필요한 kafka 클라이언트는 그룹 소유자가 이 그룹에 등록해야 합니다. 그룹 소유자는 포털 또는 PowerShell을 통해 등록할 수 있습니다.

REST 프록시 엔드포인트 요청의 경우 클라이언트 애플리케이션은 OAuth 토큰을 가져와야 합니다. 해당 토큰은 보안 그룹 멤버 자격을 확인하는 데 사용됩니다. OAuth 토큰을 가져오는 방법을 보여 주는 아래 [클라이언트 애플리케이션 샘플](#client-application-sample)을 찾습니다. 클라이언트 애플리케이션은 HTTPS 요청의 OAuth 토큰을 REST 프록시로 전달합니다.

> [!NOTE]
> AAD 보안 그룹에 대한 자세한 내용은 [Azure Active Directory 그룹을 사용하여 앱 및 리소스 액세스 관리](../../active-directory/fundamentals/active-directory-manage-groups.md)를 참조하세요. OAuth 토큰이 작동하는 방법에 대한 자세한 내용은 [OAuth 2.0 코드 권한 부여 흐름을 사용하여 Azure Active Directory 웹 애플리케이션에 대한 액세스 권한 부여](../../active-directory/azuread-dev/v1-protocols-oauth-code.md)를 참조하세요.

## <a name="kafka-rest-proxy-with-network-security-groups"></a>네트워크 보안 그룹을 사용하는 kafka REST 프록시
사용자 고유의 VNet을 가져오고 네트워크 보안 그룹을 사용하여 네트워크 트래픽을 제어하는 경우 포트 443 외에도 포트 **9400** 에서 **인바운드** 트래픽을 허용합니다. 이로써 Kafka REST 프록시 서버에 도달할 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

1. Azure AD에 애플리케이션을 등록합니다. Kafka REST 프록시와 상호 작용하도록 작성하는 클라이언트 애플리케이션은 이 애플리케이션의 ID 및 비밀을 사용하여 Azure에 인증합니다.

1. Azure AD 보안 그룹을 만듭니다. 그룹의 **구성원** 으로 Azure AD를 사용하여 보안 그룹에 등록한 애플리케이션을 추가합니다. 이 보안 그룹은 REST 프록시와 상호 작용할 수 있는 애플리케이션을 제어하는 데 사용됩니다. Azure AD 그룹을 만드는 방법에 대한 자세한 내용은 [Azure Active Directory를 사용하여 기본 그룹 만들기 및 멤버 추가](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md)를 참조하세요.

    그룹이 **보안** 유형인지 확인합니다.
    :::image type="content" source="./media/rest-proxy/rest-proxy-group.png" alt-text="보안 그룹" border="true":::

    애플리케이션이 그룹의 구성원인지 확인합니다.
    :::image type="content" source="./media/rest-proxy/rest-proxy-membergroup.png" alt-text="멤버 자격 확인" border="true":::

## <a name="create-a-kafka-cluster-with-rest-proxy-enabled"></a>REST 프록시를 사용하여 Kafka 클러스터 만들기

아래 단계에서는 Azure Portal을 사용합니다. Azure CLI를 사용하는 예제는 [Azure CLI을 사용하여 Apache Kafka REST 프록시 클러스터 만들기](tutorial-cli-rest-proxy.md)를 참조하세요.

1. Kafka 클러스터 만들기 워크플로 중 **보안 + 네트워킹** 탭에서 **Kafka REST 프록시 사용** 옵션을 선택합니다.

     :::image type="content" source="./media/rest-proxy/azure-portal-cluster-security-networking-kafka-rest.png" alt-text="보안 + 네트워킹이 선택된 HDInsight 클러스터 만들기 페이지를 보여 주는 스크린샷." border="true":::

1. **보안 그룹 선택** 을 클릭합니다. 보안 그룹 목록에서 REST 프록시에 액세스하려는 보안 그룹을 선택합니다. 검색 상자를 사용하여 적절한 보안 그룹을 찾을 수 있습니다. 아래쪽에서 **선택** 단추를 클릭합니다.

     :::image type="content" source="./media/rest-proxy/azure-portal-cluster-security-networking-kafka-rest2.png" alt-text="보안 그룹을 선택하는 옵션이 있는 HDInsight 클러스터 만들기 페이지를 보여 주는 스크린샷." border="true":::

1. [Azure Portal을 사용하여 Azure HDInsight에서 Apache Kafka 클러스터 만들기](./apache-kafka-get-started.md)에 설명된 대로 클러스터를 만드는 나머지 단계를 완료합니다.

1. 클러스터가 만들어지면 클러스터 속성으로 이동하여 Kafka REST 프록시 URL을 입력합니다.

     :::image type="content" source="./media/rest-proxy/apache-kafka-rest-proxy-view-proxy-url.png" alt-text="REST 프록시 URL 보기" border="true":::

## <a name="client-application-sample"></a>클라이언트 애플리케이션 샘플

아래 python 코드를 사용하여 Kafka 클러스터에서 REST 프록시와 상호 작용할 수 있습니다. 코드 샘플을 사용하려면 다음 단계를 수행합니다.

1. Python이 설치된 컴퓨터에 샘플 코드를 저장합니다.
1. `pip3 install msal`을 실행하여 필요한 python 종속성을 설치합니다.
1. **다음 속성 구성** 코드 섹션을 수정하고 사용자 환경에 대한 다음 속성을 업데이트합니다.

    |속성 |Description |
    |---|---|
    |테넌트 ID|구독이 위치한 Azure 테넌트입니다.|
    |클라이언트 ID|보안 그룹에 등록한 애플리케이션의 ID입니다.|
    |클라이언트 암호|보안 그룹에 등록한 애플리케이션의 비밀입니다.|
    |Kafkarest_endpoint|[배포 섹션](#create-a-kafka-cluster-with-rest-proxy-enabled)에 설명된 대로 클러스터 개요의 **속성** 탭에서 이 값을 가져옵니다. `https://<clustername>-kafkarest.azurehdinsight.net` 형식이어야 합니다.|

1. 명령줄에서 `sudo python3 <filename.py>`을 실행하여 python 파일을 실행합니다.

이 코드는 다음 작업을 수행합니다.

1. Azure AD에서 OAuth 토큰을 가져옵니다.
1. Kafka REST 프록시로 요청을 만드는 방법을 보여줍니다.

Python에서 OAuth 토큰을 가져오는 방법에 대한 자세한 내용은 [Python AuthenticationContext 클래스](/python/api/adal/adal.authentication_context.authenticationcontext)를 참조하세요. Kafka REST 프록시를 통해 생성 또는 삭제되지 않은 `topics`이 거기에서 리플렉트되고 있다면 지연 시간이 표시될 수 있습니다. 이 지연 시간은 캐시 새로 고침으로 인해 발생합니다. 생산자 API의 **값** 필드가 향상되었습니다. 이제 JSON 개체와 모든 직렬화된 양식이 허용됩니다.

```python
#Required python packages
#pip3 install msal

import json
import msal
import random
import requests
import string
import sys
import time

def get_random_string():
    letters = string.ascii_letters
    random_string = ''.join(random.choice(letters) for i in range(7))

    return random_string


#--------------------------Configure these properties-------------------------------#
# Tenant ID for your Azure Subscription
tenant_id = 'ABCDEFGH-1234-1234-1234-ABCDEFGHIJKL'
# Your Client Application Id
client_id = 'XYZABCDE-1234-1234-1234-ABCDEFGHIJKL'
# Your Client Credentials
client_secret = 'password'
# kafka rest proxy -endpoint
kafkarest_endpoint = "https://<clustername>-kafkarest.azurehdinsight.net"
#--------------------------Configure these properties-------------------------------#

# Get access token
# Scope
scope = 'https://hib.azurehdinsight.net/.default'
#Authority
authority = 'https://login.microsoftonline.com/' + tenant_id

app = msal.ConfidentialClientApplication(
    client_id , client_secret, authority,
    #cache - For details on how look at this example: https://github.com/Azure-Samples/ms-identity-python-webapp/blob/master/app.py
)

# The pattern to acquire a token looks like this.
result = None
result = app.acquire_token_for_client(scopes=[scope])
accessToken = result['access_token']
verify_https = True
request_timeout = 10

# Print access token
print("Access token: " + accessToken)

# API format
api_version = 'v1'
api_format = kafkarest_endpoint + '/{api_version}/{rest_api}'
get_topic_api = 'metadata/topics'
topic_api_format = 'topics/{topic_name}'
producer_api_format = 'producer/topics/{topic_name}'
consumer_api_format = 'consumer/topics/{topic_name}/partitions/{partition_id}/offsets/{offset}?count={count}'  # by default count = 1

# Request header
headers = {
    'Authorization': 'Bearer ' + accessToken,
    'Content-type': 'application/json'          # set Content-type to 'application/json'
}

# New topic
new_topic = 'hello_topic_' + get_random_string()
print("Topic " + new_topic + " is going to be used for demo.")

topics = []

# Create a  new topic
# Example of topic config
topic_config = {
    "partition_count": 1,
    "replication_factor": 1,
    "topic_properties": {
        "retention.ms": 604800000,
        "min.insync.replicas": "1"
    }
}

create_topic_url = api_format.format(api_version=api_version, rest_api=topic_api_format.format(topic_name=new_topic))
response = requests.put(create_topic_url, headers=headers, json=topic_config, timeout=request_timeout, verify=verify_https)
print(response.content)

if response.ok:
    while new_topic not in topics:
        print("The new topic " + new_topic + " is not visible yet. sleep 30 seconds...")
        time.sleep(30)
        # List Topic
        get_topic_url = api_format.format(api_version=api_version, rest_api=get_topic_api)

        response = requests.get(get_topic_url, headers={'Authorization': 'Bearer ' + accessToken}, timeout=request_timeout, verify=verify_https)
        topic_list = response.json()
        topics = topic_list.get("topics", [])
else:
    print("Topic " + new_topic + " was created. Exit.")
    sys.exit(1)

# Produce messages to new_topic
# Example payload of Producer REST API
payload_json = {
    "records": [
        {
            "key": "key1",
            "value": "**********"         # A string                              
        },
        {
            "partition": 0,
            "value": 5                    # An integer
        },
        {
            "value": 3.14                 # A floating number
        },
        {
            "value": {                    # A JSON object
                "id": 1,
                "name": "HDInsight Kafka REST proxy"
            }
        },
        {
            "value": [                    # A list of JSON objects
                {
                    "id": 1,
                    "name": "HDInsight Kafka REST proxy 1"
                },
                {
                    "id": 2,
                    "name": "HDInsight Kafka REST proxy 2"
                },
                {
                    "id": 3,
                    "name": "HDInsight Kafka REST proxy 3"
                }
            ]
        },
        {
            "value": {                  # A nested JSON object
                "group id": 1,
                "HDI Kafka REST": {
                    "id": 1,
                    "name": "HDInsight Kafka REST proxy 1"
                },
                "HDI Kafka REST server info": {
                    "id": 1,
                    "name": "HDInsight Kafka REST proxy 1",
                    "servers": [
                        {
                            "server id": 1,
                            "server name": "HDInsight Kafka REST proxy server 1"
                        },
                        {
                            "server id": 2,
                            "server name": "HDInsight Kafka REST proxy server 2"
                        },
                        {
                            "server id": 3,
                            "server name": "HDInsight Kafka REST proxy server 3"
                        }
                    ]
                }
            }
        }
    ]
}

print("Payloads in a Producer request: \n", payload_json)
producer_url = api_format.format(api_version=api_version, rest_api=producer_api_format.format(topic_name=new_topic))
response = requests.post(producer_url, headers=headers, json=payload_json, timeout=request_timeout, verify=verify_https)
print(response.content)

# Consume messages from the topic
partition_id = 0
offset = 0
count = 2

while True:
    consumer_url = api_format.format(api_version=api_version, rest_api=consumer_api_format.format(topic_name=new_topic, partition_id=partition_id, offset=offset, count=count))
    print("Consuming " + str(count) + " messages from offset " + str(offset))

    response = requests.get(consumer_url, headers=headers, timeout=request_timeout, verify=verify_https)

    if response.ok:
        messages = response.json()
        print("Consumed messages: \n" + json.dumps(messages, indent=2))
        next_offset = response.headers.get("NextOffset")
        if offset == next_offset or not messages.get("records", []):
            print("Consumer caught up with producer. Exit for now...")
            break

        offset = next_offset

    else:
        print("Error " + str(response.status_code))
        break
        
# List partitions
get_partitions_url = api_format.format(api_version=api_version, rest_api=partitions_api_format.format(topic_name=new_topic))
print("Fetching partitions from  " + get_partitions_url)

response = requests.get(get_partitions_url, headers={'Authorization': 'Bearer ' + accessToken}, timeout=request_timeout, verify=verify_https)
partition_list = response.json()
print("Partition list: \n" + json.dumps(partition_list, indent=2))

# List a partition
get_partition_url = api_format.format(api_version=api_version, rest_api=partition_api_format.format(topic_name=new_topic, partition_id=partition_id))
print("Fetching metadata of a partition from  " + get_partition_url)

response = requests.get(get_partition_url, headers={'Authorization': 'Bearer ' + accessToken}, timeout=request_timeout, verify=verify_https)
partition = response.json()
print("Partition metadata: \n" + json.dumps(partition, indent=2))

```

curl 명령을 사용하는 Azure REST 프록시를 위해 Azure에서 토큰을 가져오는 방법에 대한 다른 샘플은 아래에서 확인하세요. **토큰을 가져오는 동안 지정된 `scope=https://hib.azurehdinsight.net/.default`가 필요합니다.**

```cmd
curl -X POST -H "Content-Type: application/x-www-form-urlencoded" -d 'client_id=<clientid>&client_secret=<clientsecret>&grant_type=client_credentials&scope=https://hib.azurehdinsight.net/.default' 'https://login.microsoftonline.com/<tenantid>/oauth2/v2.0/token'
```

## <a name="next-steps"></a>다음 단계

* [Kafka REST 프록시 API 참조 문서](/rest/api/hdinsight-kafka-rest-proxy/)
