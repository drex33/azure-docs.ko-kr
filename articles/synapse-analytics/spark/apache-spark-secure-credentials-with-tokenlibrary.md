---
title: Azure Synapse Analytics용 Apache Spark에서 연결된 서비스로 액세스 자격 증명 보호
description: 이 문서에서는 연결된 서비스 및 토큰 라이브러리를 사용하여 Azure Synapse Analytics용 Apache Spark를 다른 서비스와 안전하게 통합하는 개념을 제공합니다.
services: synapse-analytics
author: mlee3gsd
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: spark
ms.date: 11/19/2020
ms.author: martinle
ms.reviewer: nirav
zone_pivot_groups: programming-languages-spark-all-minus-sql
ms.openlocfilehash: af247a9bffc0b43328ec4504f64dc695e6cc9310
ms.sourcegitcommit: 81a1d2f927cf78e82557a85c7efdf17bf07aa642
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/19/2021
ms.locfileid: "132809156"
---
# <a name="secure-credentials-with-linked-services-using-the-tokenlibrary"></a>TokenLibrary를 사용하여 연결된 서비스를 통해 자격 증명 보호

외부 소스에서 데이터에 액세스하는 것은 일반적인 패턴입니다. 외부 데이터 원본에서 익명 액세스를 허용하지 않는 한, 자격 증명, 비밀 또는 연결 문자열로 연결을 보호해야 할 수도 있습니다.  

Synapse는 기본적으로 리소스 간의 인증을 위해 AAD(Azure Active Directory) 통과를 사용합니다.  다른 자격 증명을 사용하여 리소스에 연결해야 하는 경우 TokenLibrary를 직접 사용합니다.  TokenLibrary는 연결된 서비스 또는 Azure Key Vault에서 저장된 SAS 토큰, AAD 토큰, 연결 문자열 및 비밀을 검색하는 프로세스를 간소화합니다.

AAD 통과는 Synapse 또는 별도의 서비스 주체에 할당된 권한이 아닌 AAD의 사용자로 할당된 권한을 사용합니다.  예를 들어 AAD 통과를 사용하여 스토리지 계정의 Blob에 액세스하려면 해당 스토리지 계정으로 이동하여 Blob 기여자 역할을 자신에게 할당해야 합니다.

Azure Key Vault에서 비밀을 검색하는 경우 Azure Key Vault 연결된 서비스를 만드는 것이 좋습니다.  Synapse 작업 영역 MSI(관리되는 서비스 ID)에 Azure Key Vault에 대한 비밀 가져오기 권한이 있는지 확인합니다.  Synapse는 Synapse 작업 영역 관리형 서비스 ID를 사용하여 Azure Key Vault에 인증합니다. 연결된 서비스 없이 Azure Key Vault에 직접 연결하는 경우 사용자 Azure Active Directory 자격 증명을 사용하여 인증합니다.

자세한 내용은 [연결된 서비스](../../data-factory/concepts-linked-services.md?context=/azure/synapse-analytics/context/context)를 참조하세요.

## <a name="usage"></a>사용

### <a name="tokenlibraryhelp"></a>TokenLibrary.help()
이 함수는 TokenLibrary에 대한 도움말 설명서를 표시합니다.

::: zone pivot = "programming-language-scala"

```scala
TokenLibrary.help()
```

::: zone-end

::: zone pivot = "programming-language-python"

```python
TokenLibrary.help()
```

::: zone-end

::: zone pivot = "programming-language-csharp"

```csharp
Console.WriteLine(TokenLibrary.help());
```

::: zone-end

## <a name="tokenlibrary-for-azure-data-lake-storage-gen2"></a>Azure Data Lake Storage Gen2에 대한 TokenLibrary

#### <a name="adls-gen2-primary-storage"></a>ADLS Gen2 기본 스토리지

기본 Azure Data Lake Storage에서 파일에 액세스는 기본적으로 인증을 위해 Azure Active Directory 통과를 사용하며, TokenLibrary를 명시적으로 사용할 필요가 없습니다.

::: zone pivot = "programming-language-scala"

```scala
val df = spark.read.csv("abfss://<CONTAINER>@<ACCOUNT>.dfs.core.windows.net/<FILE PATH>")
display(df.limit(10))
```

::: zone-end

::: zone pivot = "programming-language-python"

```python
df = spark.read.csv('abfss://<CONTAINER>@<ACCOUNT>.dfs.core.windows.net/<FILE PATH>')
display(df.limit(10))
```

::: zone-end

#### <a name="adls-gen2-storage-with-linked-services"></a>연결된 서비스가 있는 ADLS Gen2 스토리지

Synapse는 Azure Data Lake Storage Gen2에 연결할 때 통합 연결된 서비스 환경을 제공합니다.  **계정 키**, **서비스 주체**, **관리 ID** 또는 **자격 증명** 을 사용하여 인증하도록 연결된 서비스를 구성할 수 있습니다.

연결된 서비스 인증 방법이 **계정 키** 로 설정된 경우 연결된 서비스는 제공된 스토리지 계정 키를 사용하여 인증하고, SAS 키를 요청하고, **LinkedServiceBasedSASProvider** 를 사용하여 스토리지 요청에 자동으로 적용합니다.

::: zone pivot = "programming-language-scala"

```scala
val sc = spark.sparkContext
spark.conf.set("spark.storage.synapse.linkedServiceName", "<LINKED SERVICE NAME>")
spark.conf.set("fs.azure.account.auth.type", "SAS")
spark.conf.set("fs.azure.sas.token.provider.type", "com.microsoft.azure.synapse.tokenlibrary.LinkedServiceBasedSASProvider")

val df = spark.read.csv("abfss://<CONTAINER>@<ACCOUNT>.dfs.core.windows.net/<FILE PATH>")

display(df.limit(10))
```

::: zone-end

::: zone pivot = "programming-language-python"

```python
%%pyspark
# Python code
spark.conf.set("spark.storage.synapse.linkedServiceName", "<lINKED SERVICE NAME>")
spark.conf.set("fs.azure.account.auth.type", "SAS")
spark.conf.set("fs.azure.sas.token.provider.type", "com.microsoft.azure.synapse.tokenlibrary.LinkedServiceBasedSASProvider")

df = spark.read.csv('abfss://<CONTAINER>@<ACCOUNT>.dfs.core.windows.net/<DIRECTORY PATH>')

df.show()
```

::: zone-end

연결된 서비스 인증 방법이 **관리 ID** 또는 **서비스 주체** 로 설정된 경우 연결된 서비스는 **LinkedServiceBasedTokenProvider** 공급자와 함께 관리 ID 또는 서비스 주체 토큰을 사용합니다.  


::: zone pivot = "programming-language-scala"

```scala
val sc = spark.sparkContext
spark.conf.set("spark.storage.synapse.linkedServiceName", "<LINKED SERVICE NAME>")
spark.conf.set("fs.azure.account.oauth.provider.type", "com.microsoft.azure.synapse.tokenlibrary.LinkedServiceBasedTokenProvider") 
val df = spark.read.csv("abfss://<CONTAINER>@<ACCOUNT>.dfs.core.windows.net/<FILE PATH>")

display(df.limit(10))
```

::: zone-end

::: zone pivot = "programming-language-python"

```python
%%pyspark
# Python code
spark.conf.set("spark.storage.synapse.linkedServiceName", "<lINKED SERVICE NAME>")
spark.conf.set("fs.azure.account.oauth.provider.type", "com.microsoft.azure.synapse.tokenlibrary.LinkedServiceBasedTokenProvider")

df = spark.read.csv('abfss://<CONTAINER>@<ACCOUNT>.dfs.core.windows.net/<DIRECTORY PATH>')

df.show()
```

::: zone-end

#### <a name="adls-gen2-storage-without-linked-services"></a>ADLS Gen2 스토리지(연결된 서비스 제외)

SAS 키를 사용하여 ADLS Gen2 스토리지에 직접 연결합니다. **ConfBasedSASProvider** 를 사용하고 **spark.storage.synapse.sas** 구성 설정에 SAS 키를 제공합니다.

::: zone pivot = "programming-language-scala"

```scala
%%spark
spark.conf.set("fs.azure.account.auth.type", "SAS")
spark.conf.set("fs.azure.sas.token.provider.type", "com.microsoft.azure.synapse.tokenlibrary.ConfBasedSASProvider")
spark.conf.set("spark.storage.synapse.sas", "<SAS KEY>")

val df = spark.read.csv("abfss://<CONTAINER>@<ACCOUNT>.dfs.core.windows.net/<FILE PATH>")

display(df.limit(10))
```

::: zone-end

::: zone pivot = "programming-language-python"

```python
%%pyspark

spark.conf.set("fs.azure.account.auth.type", "SAS")
spark.conf.set("fs.azure.sas.token.provider.type", "com.microsoft.azure.synapse.tokenlibrary.ConfBasedSASProvider")
spark.conf.set("spark.storage.synapse.sas", "<SAS KEY>")

df = spark.read.csv('abfss://<CONTAINER>@<ACCOUNT>.dfs.core.windows.net/<FILE PATH>')

display(df.limit(10))
```

::: zone-end

#### <a name="adls-gen2-storage-with-azure-key-vault"></a>Azure Key Vault가 있는 ADLS Gen2 스토리지

Azure Key Vault 비밀에 저장된 SAS 토큰을 사용하여 ADLS Gen2 스토리지에 연결합니다.  

::: zone pivot = "programming-language-scala"

```scala
%%spark
spark.conf.set("fs.azure.account.auth.type", "SAS")
spark.conf.set("fs.azure.sas.token.provider.type", "com.microsoft.azure.synapse.tokenlibrary.AkvBasedSASProvider")
spark.conf.set("spark.storage.synapse.akv", "<AZURE KEY VAULT NAME>")
spark.conf.set("spark.storage.akv.secret", "<SECRET KEY>")

val df = spark.read.csv("abfss://<CONTAINER>@<ACCOUNT>.dfs.core.windows.net/<FILE PATH>")

display(df.limit(10))
```

::: zone-end

::: zone pivot = "programming-language-python"

```python
%%pyspark
spark.conf.set("fs.azure.account.auth.type", "SAS")
spark.conf.set("fs.azure.sas.token.provider.type", "com.microsoft.azure.synapse.tokenlibrary.AkvBasedSASProvider")
spark.conf.set("spark.storage.synapse.akv", "<AZURE KEY VAULT NAME>")
spark.conf.set("spark.storage.akv.secret", "<SECRET KEY>")

df = spark.read.csv('abfss://<CONTAINER>@<ACCOUNT>.dfs.core.windows.net/<FILE PATH>')

display(df.limit(10))
```

::: zone-end

## <a name="tokenlibrary-for-other-linked-services"></a>다른 연결된 서비스용 TokenLibrary

다른 연결된 서비스에 연결하려면 TokenLibrary를 직접 호출하면 됩니다.

#### <a name="getconnectionstring"></a>getConnectionString()

 연결 문자열을 검색하려면 **getConnectionString** 함수를 사용하고 **연결된 서비스 이름** 을 전달합니다.

::: zone pivot = "programming-language-scala"

```scala
%%spark
// retrieve connectionstring from TokenLibrary

import com.microsoft.azure.synapse.tokenlibrary.TokenLibrary

val connectionString: String = TokenLibrary.getConnectionString("<LINKED SERVICE NAME>")
println(connectionString)
```

::: zone-end

::: zone pivot = "programming-language-python"

```python
%%pyspark
# retrieve connectionstring from TokenLibrary

from pyspark.sql import SparkSession

sc = SparkSession.builder.getOrCreate()
token_library = sc._jvm.com.microsoft.azure.synapse.tokenlibrary.TokenLibrary
connection_string = token_library.getConnectionString("<LINKED SERVICE NAME>")
print(connection_string)
```

::: zone-end

::: zone pivot = "programming-language-csharp"

```csharp
%%csharp
// retrieve connectionstring from TokenLibrary

using Microsoft.Spark.Extensions.Azure.Synapse.Analytics.Utils;

string connectionString = TokenLibrary.GetConnectionString(<LINKED SERVICE NAME>);
Console.WriteLine(connectionString);
```

::: zone-end

#### <a name="getconnectionstringasmap"></a>getConnectionStringAsMap()

getConnectionStringAsMap은 Scala 및 Python에서 사용할 수 있는 도우미 함수입니다. 연결 문자열의 _key=value_ 쌍에서 특정 값을 구문 분석하려면 다음과 같이 수행합니다.

_`DefaultEndpointsProtocol=https;AccountName=<ACCOUNT NAME>;AccountKey=<ACCOUNT KEY>`_

**getConnectionStringAsMap** 함수를 사용하고 키를 전달하여 값을 반환합니다.  위의 연결 문자열 예제에서 

_**TokenLibrary.getConnectionStringAsMap("DefaultEndpointsProtocol")**_

다음을 반환합니다.

**_"https"_**

::: zone pivot = "programming-language-scala"

```scala
// Linked services can be used for storing and retrieving credentials (e.g, account key)
// Example connection string (for storage): "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
import com.microsoft.azure.synapse.tokenlibrary.TokenLibrary

val accountKey: String = TokenLibrary.getConnectionStringAsMap("<LINKED SERVICE NAME">).get("<KEY NAME>")
println(accountKey)
```
::: zone-end

::: zone pivot = "programming-language-python"

```python
# Linked services can be used for storing and retrieving credentials (e.g, account key)
# Example connection string (for storage): "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
from pyspark.sql import SparkSession

sc = SparkSession.builder.getOrCreate()
token_library = sc._jvm.com.microsoft.azure.synapse.tokenlibrary.TokenLibrary
accountKey = token_library.getConnectionStringAsMap("<LINKED SERVICE NAME>").get("<KEY NAME>")
print(accountKey)
```

::: zone-end

#### <a name="getsecret"></a>getSecret()

Azure Key Vault에서 저장된 비밀을 검색하려면 Synapse 작업 영역 내에서 Azure Key Vault에 대한 연결된 서비스를 만드는 것이 좋습니다. Synapse 작업 영역 관리 서비스 ID는 Azure Key Vault에 대한 비밀 **가져오기** 권한을 부여 받아야 합니다.  연결된 서비스는 관리 서비스 ID를 사용하여 Azure Key Vault 서비스에 연결하여 비밀을 검색합니다.  그렇지 않으면 Azure Key Vault에 직접 연결하는 경우 사용자의 AAD(Azure Active Directory) 자격 증명을 사용합니다.  이 경우 사용자에게 Azure Key Vault에서 비밀 가져오기 권한이 있어야 합니다.

`TokenLibrary.getSecret("<AZURE KEY VAULT NAME>", "<SECRET KEY>" [, <LINKED SERVICE NAME>])`

Azure Key Vault에서 비밀을 검색하려면 **TokenLibrary.getSecret()** 함수를 사용합니다.

::: zone pivot = "programming-language-scala"

```scala
import com.microsoft.azure.synapse.tokenlibrary.TokenLibrary

val connectionString: String = TokenLibrary.getSecret("<AZURE KEY VAULT NAME>", "<SECRET KEY>", "<LINKED SERVICE NAME>")
println(connectionString)
```

::: zone-end

::: zone pivot = "programming-language-python"

```python
import sys
from pyspark.sql import SparkSession

sc = SparkSession.builder.getOrCreate()
token_library = sc._jvm.com.microsoft.azure.synapse.tokenlibrary.TokenLibrary

connection_string = token_library.getSecret("<AZURE KEY VAULT NAME>", "<SECRET KEY>", "<LINKED SERVICE NAME>")
print(connection_string)
```

::: zone-end

::: zone pivot = "programming-language-csharp"

```csharp
using Microsoft.Spark.Extensions.Azure.Synapse.Analytics.Utils;

string connectionString = TokenLibrary.getSecret("<AZURE KEY VAULT NAME>", "<SECRET KEY>", "<LINKED SERVICE NAME>");
Console.WriteLine(connectionString);
```

::: zone-end

## <a name="next-steps"></a>다음 단계

- [전용 SQL 풀에 쓰기](./synapse-spark-sql-pool-import-export.md)
