---
title: MongoDB 및 Python용 Azure Cosmos DB API 사용 시작
description: Azure Cosmos DB의 API for MongoDB를 사용하여 연결 및 쿼리할 수 있는 Python 코드 샘플을 제공합니다.
author: gahl-levy
ms.author: gahllevy
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: quickstart
ms.date: 10/22/2021
ms.custom: mode-other
ms.openlocfilehash: 521612669473ed290cadd6c70af2af96b88a93e3
ms.sourcegitcommit: 56235f8694cc5f88db3afcc8c27ce769ecf455b0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133053114"
---
# <a name="quickstart-get-started-using-azure-cosmos-db-api-for-mongodb-and-python"></a>MongoDB 및 Python에 대 한 Azure Cosmos DB API 사용 시작
[!INCLUDE[appliesto-mongodb-api](../includes/appliesto-mongodb-api.md)]

> [!div class="op_single_selector"]
> * [.NET](create-mongodb-dotnet.md)
> * [Python](create-mongodb-python.md)
> * [Java](create-mongodb-java.md)
> * [Node.JS](create-mongodb-nodejs.md)
> * [Xamarin](create-mongodb-xamarin.md)
> * [Golang](create-mongodb-go.md)
>  

이 [빠른 시작](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-python-getting-started)에서는 다음과 같은 방법을 설명합니다.
1. [Azure Cosmos DB API for MongoDB 계정](mongodb-introduction.md) 만들기 
2. PyMongo를 사용하여 계정에 연결
3. 샘플 데이터베이스 및 컬렉션 만들기
4. 샘플 컬렉션에서 CRUD 작업 수행

## <a name="prerequisites-to-run-the-sample-app"></a>샘플 앱을 실행하기 위한 필수 구성 요소

* [Python](https://www.python.org/downloads/) 3.9 이상(이 권장 버전을 사용하여 이 문서에 설명된 [샘플 코드](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-python-getting-started)를 실행하는 것이 좋음. 이전 버전의 Python 3에서 작동할 수 있음)
* 머신에 설치된 [PyMongo](https://pypi.org/project/pymongo/)

<a id="create-account"></a>
## <a name="create-a-database-account"></a>데이터베이스 계정 만들기

[!INCLUDE [cosmos-db-create-dbaccount](../includes/cosmos-db-create-dbaccount-mongodb.md)]

## <a name="learn-the-object-model"></a>개체 모델 학습

애플리케이션 빌드를 계속하기 전에 API for MongoDB의 리소스 계층 구조와 해당 리소스를 만들고 액세스하는 데 사용되는 개체 모델을 살펴보겠습니다. API for MongoDB는 다음과 같은 순서로 리소스를 만듭니다.

* Azure Cosmos DB API for MongoDB 계정
* 데이터베이스 
* 컬렉션 
* 문서

엔터티 계층 구조를 자세히 알아보려면 [Azure Cosmos DB 리소스 모델](../account-databases-containers-items.md) 문서를 참조하세요.

## <a name="get-the-code"></a>코드 가져오기

[리포지토리에서](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-python-getting-started) 샘플 Python 코드를 다운로드하거나 git clone을 사용합니다.

```shell
git clone https://github.com/Azure-Samples/azure-cosmos-db-mongodb-python-getting-started
```

## <a name="retrieve-your-connection-string"></a>연결 문자열 검색

샘플 코드를 실행하는 경우 MongoDB 계정의 연결 문자열에 대한 API를 입력해야 합니다. 다음 단계에 따라 API를 찾습니다.

1. [Azure Portal](https://portal.azure.com/)에서 Cosmos DB 계정을 선택합니다.

2. 왼쪽 탐색 영역에서 **연결 문자열** 을 선택한 다음, **읽기 쓰기 키** 를 선택합니다. 화면 오른쪽에 있는 복사 단추를 사용하여 기본 연결 문자열을 복사합니다.

> [!WARNING]
> 암호 또는 기타 중요한 데이터를 소스 코드로 확인하지 마세요.


## <a name="run-the-code"></a>코드 실행

```shell
python run.py
```

## <a name="understand-how-it-works"></a>작동 방식 이해

### <a name="connecting"></a>Connecting

다음 코드는 사용자에게 연결 문자열을 묻는 메시지를 표시합니다. 코드에 연결 문자열을 포함하여 누구나 데이터베이스를 읽거나 쓸 수 있도록 하는 것은 절대 바람직하지 않습니다.

```python
CONNECTION_STRING = getpass.getpass(prompt='Enter your primary connection string: ') # Prompts user for connection string
```

다음 코드는 MongoDB용 API에 대해 클라이언트 연결을 만들고, 유효한지 테스트합니다.

```python
client = pymongo.MongoClient(CONNECTION_STRING)
try:
    client.server_info() # validate connection string
except pymongo.errors.ServerSelectionTimeoutError:
    raise TimeoutError("Invalid API for MongoDB connection string or timed out when attempting to connect")
```

### <a name="resource-creation"></a>리소스 만들기
다음 코드는 CRUD 작업을 수행하는 데 사용되는 예제 데이터베이스 및 컬렉션을 만듭니다. 리소스를 프로그래밍 방식으로 만들 때는 여기에 표시된 것처럼 MongoDB 확장 명령에 API를 사용하는 것이 좋습니다. 이러한 명령에는 리소스 처리량(RU/s)을 설정하고 분할된 데이터베이스를 구성하는 기능이 있기 때문입니다. 

암시적으로 리소스를 만들 수 있지만 기본적으로 처리량에 권장되는 값이 사용되며 분할되지 않습니다.

```python
# Database with 400 RU throughput that can be shared across the DB's collections
db.command({'customAction': "CreateDatabase", 'offerThroughput': 400})
```

```python
 # Creates a unsharded collection that uses the DBs shared throughput
db.command({'customAction': "CreateCollection", 'collection': UNSHARDED_COLLECTION_NAME})
```

### <a name="writing-a-document"></a>문서 작성
다음은 샘플 전체에서 계속 사용되는 샘플 문서를 삽입합니다. 후속 작업에서 쿼리할 수 있도록 고유한 _id 필드 값을 가져옵니다.

```python
"""Insert a sample document and return the contents of its _id field"""
document_id = collection.insert_one({SAMPLE_FIELD_NAME: randint(50, 500)}).inserted_id
```

### <a name="readingupdating-a-document"></a>문서 읽기/업데이트
다음에서는 이전에 삽입한 문서를 쿼리 및 업데이트한 후 다시 쿼리합니다.

```python
print("Found a document with _id {}: {}".format(document_id, collection.find_one({"_id": document_id})))

collection.update_one({"_id": document_id}, {"$set":{SAMPLE_FIELD_NAME: "Updated!"}})
print("Updated document with _id {}: {}".format(document_id, collection.find_one({"_id": document_id})))
```

### <a name="deleting-a-document"></a>문서 삭제
마지막으로, 컬렉션에서 만든 문서를 삭제합니다.
```python
"""Delete the document containing document_id from the collection"""
collection.delete_one({"_id": document_id})
```

## <a name="next-steps"></a>다음 단계
이 빠른 시작에서는 API for MongoDB 계정을 만들고, 코드를 사용하여 데이터베이스 및 컬렉션을 만들고, CRUD 작업을 수행하는 방법을 알아보았습니다. 

Azure Cosmos DB로 마이그레이션하기 위한 용량 계획을 수행하려고 하나요? 용량 계획을 위해 기존 데이터베이스 클러스터에 대한 정보를 사용할 수 있습니다.
* 기존 데이터베이스 클러스터의 vCore 및 서버 수만 알고 있는 경우 [vCore 또는 vCPU를 사용하여 요청 단위 예측](../convert-vcore-to-request-unit.md)에 대해 읽어보세요. 
* 현재 데이터베이스 워크로드에 대한 일반적인 요청 비율을 알고 있는 경우 [Azure Cosmos DB 용량 계획 도구를 사용하여 요청 단위 예측](estimate-ru-capacity-planner.md)에 대해 읽어보세요.

> [!div class="nextstepaction"]
> [Azure Cosmos DB로 MongoDB 데이터 가져오기](../../dms/tutorial-mongodb-cosmos-db.md?toc=%2fazure%2fcosmos-db%2ftoc.json%253ftoc%253d%2fazure%2fcosmos-db%2ftoc.json)
