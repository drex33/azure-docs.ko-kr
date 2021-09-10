---
title: Microsoft Spark 유틸리티 소개
description: '자습서: Azure Synapse Analytics Notebook의 MSSparkutils'
author: ruixinxu
services: synapse-analytics
ms.service: synapse-analytics
ms.topic: reference
ms.subservice: spark
ms.date: 09/10/2020
ms.author: ruxu
ms.reviewer: ''
zone_pivot_groups: programming-languages-spark-all-minus-sql
ms.custom: subject-rbac-steps
ms.openlocfilehash: 9923a2bd2e36975fe1af77fddb4bb484a4eb87c6
ms.sourcegitcommit: 6bd31ec35ac44d79debfe98a3ef32fb3522e3934
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2021
ms.locfileid: "113217776"
---
# <a name="introduction-to-microsoft-spark-utilities"></a>Microsoft Spark 유틸리티 소개

Microsoft Spark 유틸리티(MSSparkUtils)는 일반적인 작업을 쉽게 할 수 있게 돕는 기본 제공 패키지입니다. MSSparkUtils를 사용하여 파일 시스템 작업을 하고, 환경 변수를 가져오고, Notebook을 서로 연결하고, 비밀을 사용할 수 있습니다. MSSparkUtils는 `PySpark (Python)`, `Scala`, `.NET Spark (C#)` Notebook, Synapse 파이프라인에서 사용할 수 있습니다.

## <a name="pre-requisites"></a>필수 구성 요소

### <a name="configure-access-to-azure-data-lake-storage-gen2"></a>Azure Data Lake Storage Gen2에 대한 액세스 구성 

Synapse Notebook는 Azure Active Directory(Azure AD) 통과를 사용하여 ADLS Gen2 계정에 액세스합니다. ADLS Gen2 계정(또는 폴더)에 액세스하려면 **Storage Blob 데이터 기여자** 여야 합니다. 

Synapse 파이프라인은 작업 영역의 MSI(관리 서비스 ID)를 사용하여 스토리지 계정에 액세스합니다. 파이프라인 활동에서 MSSparkUtils를 사용하려면 작업 영역 ID가 **Storage Blob 데이터 기여자로** 이어야 ADLS Gen2 계정(또는 폴더)에 액세스할 수 있습니다.

다음 단계를 수행하여 Azure AD 및 작업 영역 MSI가 ADLS Gen2 계정에 액세스할 수 있도록 합니다.
1. [Azure Portal](https://portal.azure.com/) 및 액세스하려는 스토리지 계정을 엽니다. 액세스하려는 특정 컨테이너를 탐색할 수 있습니다.
1. 왼쪽 창에서 **액세스 제어(IAM)** 를 선택합니다.
1. **추가** > **역할 할당 추가** 를 선택하여 역할 할당 추가 페이지를 엽니다.
1. 다음 역할을 할당합니다. 세부 단계에 대해서는 [Azure Portal을 사용하여 Azure 역할 할당](../../role-based-access-control/role-assignments-portal.md)을 참조하세요.
    
    | 설정 | 값 |
    | --- | --- |
    | 역할 | Storage Blob 데이터 기여자 |
    | 다음에 대한 액세스 할당 | USER 및 MANAGEDIDENTITY |
    | 멤버 | Azure AD 계정 및 작업 영역 ID |

    > [!NOTE]
    > 관리 ID 이름은 작업 영역 이름이기도 합니다.

    ![Azure Portal에서 역할 할당 페이지를 추가합니다.](../../../includes/role-based-access-control/media/add-role-assignment-page.png)
 
1. **저장** 을 선택합니다.

Synapse Spark를 사용하여 다음 URL을 통해 ADLS Gen2의 데이터에 액세스할 수 있습니다.

<code>abfss://<container_name>@<storage_account_name>.dfs.core.windows.net/<path></code>

### <a name="configure-access-to-azure-blob-storage"></a>Azure Blob Storage에 대한 액세스 구성  

Synapse는 [**SAS(공유 액세스 서명)**](../../storage/common/storage-sas-overview.md)를 사용하여 Azure Blob Storage에 액세스합니다. 코드에서 SAS 키를 노출하지 않으려면 Synapse 작업 영역에서 액세스하려는 Azure Blob Storage 계정에 연결된 새 서비스를 만드는 것이 좋습니다.

Azure Blob Storage 계정을 위한 새 연결된 서비스를 추가하려면 다음 단계를 수행합니다.

1. [Azure Synapse Studio](https://web.azuresynapse.net/)를 엽니다.
2. 왼쪽 패널에서 **관리** 를 선택하고 **외부 연결** 아래에서 **연결된 서비스** 를 선택합니다.
3. 오른쪽의 **새 연결된 서비스** 패널에서 **Azure Blob Storage** 를 검색합니다.
4. **계속** 을 선택합니다.
5. 연결된 서비스 이름에 액세스하고 구성하려면 Azure Blob Storage 계정을 선택합니다. **인증 방법** 에 **계정 키** 를 사용하는 것이 좋습니다.
6. **연결 테스트** 를 선택하여 설정이 올바른지 확인합니다.
7. 먼저 **만들기** 를 선택하고 **모두 게시** 를 클릭하여 변경 내용을 저장합니다. 

다음 URL을 통해 Synapse Spark를 사용하여 Azure Blob Storage의 데이터에 액세스할 수 있습니다.

<code>wasb[s]://<container_name>@<storage_account_name>.blob.core.windows.net/<path></code>

여기에 코드 예제가 있습니다.

:::zone pivot = "programming-language-python"

```python
from pyspark.sql import SparkSession

# Azure storage access info
blob_account_name = 'Your account name' # replace with your blob name
blob_container_name = 'Your container name' # replace with your container name
blob_relative_path = 'Your path' # replace with your relative folder path
linked_service_name = 'Your linked service name' # replace with your linked service name

blob_sas_token = mssparkutils.credentials.getConnectionStringOrCreds(linked_service_name)

# Allow SPARK to access from Blob remotely

wasb_path = 'wasbs://%s@%s.blob.core.windows.net/%s' % (blob_container_name, blob_account_name, blob_relative_path)

spark.conf.set('fs.azure.sas.%s.%s.blob.core.windows.net' % (blob_container_name, blob_account_name), blob_sas_token)
print('Remote blob path: ' + wasb_path)
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
val blob_account_name = "" // replace with your blob name
val blob_container_name = "" //replace with your container name
val blob_relative_path = "/" //replace with your relative folder path
val linked_service_name = "" //replace with your linked service name


val blob_sas_token = mssparkutils.credentials.getConnectionStringOrCreds(linked_service_name)

val wasbs_path = f"wasbs://$blob_container_name@$blob_account_name.blob.core.windows.net/$blob_relative_path"
spark.conf.set(f"fs.azure.sas.$blob_container_name.$blob_account_name.blob.core.windows.net",blob_sas_token)

```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
var blob_account_name = "";  // replace with your blob name
var blob_container_name = "";     // replace with your container name
var blob_relative_path = "";  // replace with your relative folder path
var linked_service_name = "";    // replace with your linked service name
var blob_sas_token = Credentials.GetConnectionStringOrCreds(linked_service_name);

spark.SparkContext.GetConf().Set($"fs.azure.sas.{blob_container_name}.{blob_account_name}.blob.core.windows.net", blob_sas_token);

var wasbs_path = $"wasbs://{blob_container_name}@{blob_account_name}.blob.core.windows.net/{blob_relative_path}";

Console.WriteLine(wasbs_path);

```

::: zone-end 
 
###  <a name="configure-access-to-azure-key-vault"></a>Azure Key Vault에 대한 액세스 구성

Azure Key Vault를 연결된 서비스로 추가하여 Synapse에서 자격 증명을 관리할 수 있습니다. 다음 단계를 수행하여 Azure Key Vault를 Synapse 연결된 서비스로 추가합니다.
1. [Azure Synapse Studio](https://web.azuresynapse.net/)를 엽니다.
2. 왼쪽 패널에서 **관리** 를 선택하고 **외부 연결** 아래에서 **연결된 서비스** 를 선택합니다.
3. 오른쪽의 **새 연결된 서비스** 패널에서 **Azure Key Vault** 를 검색합니다.
4. 연결된 서비스 이름에 액세스하고 구성하려면 Azure Key Vault 계정을 선택합니다.
5. **연결 테스트** 를 선택하여 설정이 올바른지 확인합니다.
6. 먼저 **만들기** 를 선택하고 **모두 게시** 를 클릭하여 변경 내용을 저장합니다. 

Synapse Notebook는 Azure AD(Azure Active Directory) 통과를 사용하여 Azure Key Vault에 액세스합니다. Synapse 파이프라인은 MSI(작업 영역 ID)를 사용하여 Azure Key Vault에 액세스합니다. Notebook 및 Synapse 파이프라인에서 코드가 작동하는지 확인하려면 Azure AD 계정 및 작업 영역 ID에 비밀 액세스 권한을 부여하는 것이 좋습니다.

작업 영역 ID에 비밀 액세스 권한을 부여하려면 다음 단계를 수행합니다.
1. [Azure Portal](https://portal.azure.com/) 및 액세스하려는 Azure Key Vault를 엽니다. 
2. 왼쪽 창에서 **액세스 정책** 을 선택합니다.
3. **액세스 정책 추가** 를 선택합니다. 
    - **키, 비밀 밀 인증서 관리** 를 구성 템플릿으로 선택합니다.
    - 보안 주체 선택에서 **Azure AD 계정** 및 **작업 영역 ID**(작업 영역 이름과 동일)를 선택하거나 이미 할당되어 있는지 확인합니다. 
4. **선택** 및 **추가** 를 선택합니다.
5. **저장** 단추를 선택하여 변경 내용을 커밋합니다.  

## <a name="file-system-utilities"></a>파일 시스템 유틸리티

`mssparkutils.fs`는 ADLS Gen2(Azure Data Lake Storage Gen2) 및 Azure Blob Storage를 포함하여 다양한 파일 시스템을 사용하기 위한 유틸리티를 제공합니다. [Azure Data Lake Storage Gen2](#configure-access-to-azure-data-lake-storage-gen2) 및 [Azure Blob Storage](#configure-access-to-azure-blob-storage)에 대한 액세스를 적절하게 구성합니다.

사용 가능한 메서드에 관한 개요를 보려면 다음 명령을 실행합니다.

:::zone pivot = "programming-language-python"

```python
from notebookutils import mssparkutils
mssparkutils.fs.help()
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.fs.help()
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
using Microsoft.Spark.Extensions.Azure.Synapse.Analytics.Notebook.MSSparkUtils;
FS.Help()
```

::: zone-end

결과:
```
mssparkutils.fs provides utilities for working with various FileSystems.

Below is overview about the available methods:

cp(from: String, to: String, recurse: Boolean = false): Boolean -> Copies a file or directory, possibly across FileSystems
mv(from: String, to: String, recurse: Boolean = false): Boolean -> Moves a file or directory, possibly across FileSystems
ls(dir: String): Array -> Lists the contents of a directory
mkdirs(dir: String): Boolean -> Creates the given directory if it does not exist, also creating any necessary parent directories
put(file: String, contents: String, overwrite: Boolean = false): Boolean -> Writes the given String out to a file, encoded in UTF-8
head(file: String, maxBytes: int = 1024 * 100): String -> Returns up to the first 'maxBytes' bytes of the given file as a String encoded in UTF-8
append(file: String, content: String, createFileIfNotExists: Boolean): Boolean -> Append the content to a file
rm(dir: String, recurse: Boolean = false): Boolean -> Removes a file or directory

Use mssparkutils.fs.help("methodName") for more info about a method.

```

### <a name="list-files"></a>파일 나열
디렉터리의 콘텐츠를 나열합니다.


:::zone pivot = "programming-language-python"

```python
mssparkutils.fs.ls('Your directory path')
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.fs.ls("Your directory path")
```
::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
FS.Ls("Your directory path")
```

::: zone-end


### <a name="view-file-properties"></a>파일 속성 보기
파일 이름, 파일 경로, 파일 크기 그리고 디렉터리인지 파일인지를 포함하는 파일 속성을 반환합니다.

:::zone pivot = "programming-language-python"

```python
files = mssparkutils.fs.ls('Your directory path')
for file in files:
    print(file.name, file.isDir, file.isFile, file.path, file.size)
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
val files = mssparkutils.fs.ls("/")
files.foreach{
    file => println(file.name,file.isDir,file.isFile,file.size)
}
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
var Files = FS.Ls("/");
foreach(var File in Files) {
    Console.WriteLine(File.Name+" "+File.IsDir+" "+File.IsFile+" "+File.Size);
}
```

::: zone-end

### <a name="create-new-directory"></a>새 디렉터리 만들기

지정된 디렉터리(존재하지 않는 경우) 및 필요한 부모 디렉터리를 만듭니다.

:::zone pivot = "programming-language-python"

```python
mssparkutils.fs.mkdirs('new directory name')
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.fs.mkdirs("new directory name")
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
FS.Mkdirs("new directory name")
```

::: zone-end

### <a name="copy-file"></a>파일 복사

파일 또는 디렉터리를 복사합니다. 파일 시스템 간 복사를 지원합니다.

:::zone pivot = "programming-language-python"

```python
mssparkutils.fs.cp('source file or directory', 'destination file or directory', True)# Set the third parameter as True to copy all files and directories recursively
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.fs.cp("source file or directory", "destination file or directory", true) // Set the third parameter as True to copy all files and directories recursively
```
::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
FS.Cp("source file or directory", "destination file or directory", true) // Set the third parameter as True to copy all files and directories recursively
```

::: zone-end

### <a name="preview-file-content"></a>파일 콘텐츠 미리 보기

지정된 파일의 첫 번째 'maxBytes' 바이트를 UTF-8으로 인코딩된 문자열로 반환합니다.

:::zone pivot = "programming-language-python"

```python
mssparkutils.fs.head('file path', maxBytes to read)
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.fs.head("file path", maxBytes to read)
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
FS.Head("file path", maxBytes to read)
```

::: zone-end

### <a name="move-file"></a>파일 이동

파일 또는 디렉터리를 이동합니다. 파일 시스템 간 이동을 지원합니다.

:::zone pivot = "programming-language-python"

```python
mssparkutils.fs.mv('source file or directory', 'destination directory', True) # Set the last parameter as True to firstly create the parent directory if it does not exist
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.fs.mv("source file or directory", "destination directory", true) // Set the last parameter as True to firstly create the parent directory if it does not exist
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
FS.Mv("source file or directory", "destination directory", true)
```

::: zone-end

### <a name="write-file"></a>파일 쓰기

지정된 문자열을 UTF-8으로 인코딩된 파일에 씁니다.

:::zone pivot = "programming-language-python"

```python
mssparkutils.fs.put("file path", "content to write", True) # Set the last parameter as True to overwrite the file if it existed already
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.fs.put("file path", "content to write", true) // Set the last parameter as True to overwrite the file if it existed already
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
FS.Put("file path", "content to write", true) // Set the last parameter as True to overwrite the file if it existed already
```

::: zone-end

### <a name="append-content-to-a-file"></a>파일에 콘텐츠 추가

지정된 문자열을 UTF-8으로 인코딩된 파일에 추가합니다.

:::zone pivot = "programming-language-python"

```python
mssparkutils.fs.append("file path", "content to append", True) # Set the last parameter as True to create the file if it does not exist
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.fs.append("file path","content to append",true) // Set the last parameter as True to create the file if it does not exist
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
FS.Append("file path", "content to append", true) // Set the last parameter as True to create the file if it does not exist
```

::: zone-end

### <a name="delete-file-or-directory"></a>파일 또는 디렉터리 삭제

파일 또는 디렉터리를 제거합니다.

:::zone pivot = "programming-language-python"

```python
mssparkutils.fs.rm('file path', True) # Set the last parameter as True to remove all files and directories recursively 
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.fs.rm("file path", true) // Set the last parameter as True to remove all files and directories recursively 
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
FS.Rm("file path", true) // Set the last parameter as True to remove all files and directories recursively 
```

::: zone-end

:::zone pivot = "programming-language-python"

## <a name="notebook-utilities"></a>Notebook 유틸리티 

MSSparkUtils Notebook 유틸리티를 사용하여 Notebook을 실행하거나 값이 있는 Notebook을 종료할 수 있습니다. 사용 가능한 메서드에 관한 개요를 가져오려면 다음 명령을 실행합니다.

```python
mssparkutils.notebook.help()
```

결과 가져오기:
```
The notebook module.

exit(value: String): void -> This method lets you exit a notebook with a value.
run(path: String, timeoutSeconds: int, arguments: Map): String -> This method runs a notebook and returns its exit value.

```

### <a name="run-a-notebook"></a>Notebook을 실행합니다.
Notebook을 실행하고 해당 종료 값을 반환합니다. Notebook에서 대화식으로 또는 파이프라인에서 중첩 함수 호출을 실행할 수 있습니다. 참조 중인 Notebook은 Notebook이 이 함수를 호출하는 Spark 풀에서 실행됩니다.  

```python

mssparkutils.notebook.run("notebook path", <timeoutSeconds>, <parameterMap>)

```

다음은 그 예입니다.

```python
mssparkutils.notebook.run("folder/Sample1", 90, {"input": 20 })
```

### <a name="exit-a-notebook"></a>Notebook을 종료합니다.
값이 있는 Notebook을 종료합니다. Notebook에서 대화식으로 또는 파이프라인에서 중첩 함수 호출을 실행할 수 있습니다. 

- 대화식으로 `exit()` 함수를 Notebook으로 호출하면 Azure Synapse는 예외를 throw하고, 실행 중인 하위 시퀀스 셀을 건너뛰고, Spark 세션을 활성 상태로 유지합니다.

- Synapse 파이프라인에서 `exit()` 함수를 호출하는 Notebook을 오케스트레이션하면 Azure Synapse는 종료 값을 반환하고 파이프라인 실행을 완료하고 Spark 세션을 중지합니다.  

- 참조 중인 Notebook에서 `exit()` 함수를 호출하면 Azure Synapse는 참조 중인 Notebook에서 추가 실행을 중지하고 `run()` 함수를 호출하는 Notebook에서 다음 셀을 계속 실행합니다. 예: Notebook1은 3개의 셀을 갖고 있으며 두 번째 셀에서 `exit()` 함수를 호출합니다. Notebook2는 5개의 셀을 갖고 있으며 세 번째 셀에서 `run(notebook1)`을 호출합니다. Notebook2를 실행하면 `exit()` 함수를 사용할 때 Notebook1이 두 번째 셀에서 중지됩니다. Notebook2는 네 번째 셀과 다섯 번째 셀을 계속 실행합니다. 


```python
mssparkutils.notebook.exit("value string")
```

다음은 그 예입니다.

**Sample1** Notebook은 다음 2개의 셀이 있는 **folder/** 아래에 있습니다. 
- 셀 1은 기본값이 10으로 설정된 **입력** 매개변수를 정의합니다.
- 셀 2는 **입력** 을 종료 값으로 사용하여 Notebook을 종료합니다. 

![샘플 Notebook의 스크린샷](./media/microsoft-spark-utilities/spark-utilities-run-notebook-sample.png)

기본값을 사용하여 다른 Notebook에서 **Sample1** 을 실행할 수 있습니다.

```python

exitVal = mssparkutils.notebook.run("folder/Sample1")
print (exitVal)

```
결과:

```
Sample1 run success with input is 10
```

다른 Notebook에서 **Sample1** 을 실행하고 **입력** 값을 20으로 설정할 수 있습니다.

```python
exitVal = mssparkutils.notebook.run("mssparkutils/folder/Sample1", 90, {"input": 20 })
print (exitVal)
```

결과:

```
Sample1 run success with input is 20
```
::: zone-end


:::zone pivot = "programming-language-scala"

## <a name="notebook-utilities"></a>Notebook 유틸리티 

MSSparkUtils Notebook 유틸리티를 사용하여 Notebook을 실행하거나 값이 있는 Notebook을 종료할 수 있습니다. 사용 가능한 메서드에 관한 개요를 가져오려면 다음 명령을 실행합니다.

```scala
mssparkutils.notebook.help()
```

결과 가져오기:
```
The notebook module.

exit(value: String): void -> This method lets you exit a notebook with a value.
run(path: String, timeoutSeconds: int, arguments: Map): String -> This method runs a notebook and returns its exit value.

```

### <a name="run-a-notebook"></a>Notebook을 실행합니다.
Notebook을 실행하고 해당 종료 값을 반환합니다. Notebook에서 대화식으로 또는 파이프라인에서 중첩 함수 호출을 실행할 수 있습니다. 참조 중인 Notebook은 Notebook이 이 함수를 호출하는 Spark 풀에서 실행됩니다.  

```scala

mssparkutils.notebook.run("notebook path", <timeoutSeconds>, <parameterMap>)

```

다음은 그 예입니다.

```scala
mssparkutils.notebook.run("folder/Sample1", 90, {"input": 20 })
```

### <a name="exit-a-notebook"></a>Notebook을 종료합니다.
값이 있는 Notebook을 종료합니다. Notebook에서 대화식으로 또는 파이프라인에서 중첩 함수 호출을 실행할 수 있습니다. 

- 대화식으로 `exit()` 함수를 Notebook으로 호출하면 Azure Synapse는 예외를 throw하고, 실행 중인 하위 시퀀스 셀을 건너뛰고, Spark 세션을 활성 상태로 유지합니다.

- Synapse 파이프라인에서 `exit()` 함수를 호출하는 Notebook을 오케스트레이션하면 Azure Synapse는 종료 값을 반환하고 파이프라인 실행을 완료하고 Spark 세션을 중지합니다.  

- 참조 중인 Notebook에서 `exit()` 함수를 호출하면 Azure Synapse는 참조 중인 Notebook에서 추가 실행을 중지하고 `run()` 함수를 호출하는 Notebook에서 다음 셀을 계속 실행합니다. 예: Notebook1은 3개의 셀을 갖고 있으며 두 번째 셀에서 `exit()` 함수를 호출합니다. Notebook2는 5개의 셀을 갖고 있으며 세 번째 셀에서 `run(notebook1)`을 호출합니다. Notebook2를 실행하면 `exit()` 함수를 사용할 때 Notebook1이 두 번째 셀에서 중지됩니다. Notebook2는 네 번째 셀과 다섯 번째 셀을 계속 실행합니다. 


```python
mssparkutils.notebook.exit("value string")
```

다음은 그 예입니다.

**Sample1** Notebook은 다음 2개의 셀이 있는 **mssparkutils/folder/** 아래에 있습니다. 
- 셀 1은 기본값이 10으로 설정된 **입력** 매개변수를 정의합니다.
- 셀 2는 **입력** 을 종료 값으로 사용하여 Notebook을 종료합니다. 

![샘플 Notebook의 스크린샷](./media/microsoft-spark-utilities/spark-utilities-run-notebook-sample.png)

기본값을 사용하여 다른 Notebook에서 **Sample1** 을 실행할 수 있습니다.

```scala

val exitVal = mssparkutils.notebook.run("mssparkutils/folder/Sample1")
print(exitVal)

```
결과:

```
exitVal: String = Sample1 run success with input is 10
Sample1 run success with input is 10
```


다른 Notebook에서 **Sample1** 을 실행하고 **입력** 값을 20으로 설정할 수 있습니다.

```scala
val exitVal = mssparkutils.notebook.run("mssparkutils/folder/Sample1", 90, {"input": 20 })
print(exitVal)
```

결과:

```
exitVal: String = Sample1 run success with input is 20
Sample1 run success with input is 20
```
::: zone-end


## <a name="credentials-utilities"></a>자격 증명 유틸리티

MSSparkUtils 자격 증명 유틸리티를 사용하여 Azure Key Vault에서 연결된 서비스의 액세스 토큰을 가져오고 비밀을 관리할 수 있습니다. 

사용 가능한 메서드에 관한 개요를 가져오려면 다음 명령을 실행합니다.

:::zone pivot = "programming-language-python"

```python
mssparkutils.credentials.help()
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.credentials.help()
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
Credentials.Help()
```

::: zone-end

결과 가져오기:

```
getToken(audience, name): returns AAD token for a given audience, name (optional)
isValidToken(token): returns true if token hasn't expired
getConnectionStringOrCreds(linkedService): returns connection string or credentials for linked service
getSecret(akvName, secret, linkedService): returns AKV secret for a given AKV linked service, akvName, secret key
getSecret(akvName, secret): returns AKV secret for a given akvName, secret key
putSecret(akvName, secretName, secretValue, linkedService): puts AKV secret for a given akvName, secretName
putSecret(akvName, secretName, secretValue): puts AKV secret for a given akvName, secretName
```

### <a name="get-token"></a>토큰 가져오기

지정된 대상 그룹을 위한 Azure AD 토큰을 반환합니다(선택 사항). 다음 표에서는 사용 가능한 모든 대상 그룹 형식을 나열합니다. 

|대상 형식|대상 키|
|--|--|
|대상 그룹 확인 형식|'Audience'|
|스토리지 대상 그룹 리소스|'Storage'|
|데이터 웨어하우스 대상 그룹 리소스|'DW'|
|Data Lake 대상 그룹 리소스|'AzureManagement'|
|자격 증명 모음 대상 그룹 리소스|'DataLakeStore'|
|Azure OSSDB 대상 그룹 리소스|'AzureOSSDB'|
|Azure Synapse 리소스|'Synapse'|
|Azure Data Factory 리소스|'ADF'|

:::zone pivot = "programming-language-python"

```python
mssparkutils.credentials.getToken('audience Key')
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.credentials.getToken("audience Key")
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
Credentials.GetToken("audience Key")
```

::: zone-end


### <a name="validate-token"></a>토큰 확인

토큰이 만료되지 않은 경우 true를 반환합니다.

:::zone pivot = "programming-language-python"

```python
mssparkutils.credentials.isValidToken('your token')
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.credentials.isValidToken("your token")
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
Credentials.IsValidToken("your token")
```

::: zone-end


### <a name="get-connection-string-or-credentials-for-linked-service"></a>연결된 서비스를 위한 연결 문자열 또는 자격 증명 가져오기

연결된 서비스를 위한 연결 문자열 또는 자격 증명을 반환합니다. 

:::zone pivot = "programming-language-python"

```python
mssparkutils.credentials.getConnectionStringOrCreds('linked service name')
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.credentials.getConnectionStringOrCreds("linked service name")
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
Credentials.GetConnectionStringOrCreds("linked service name")
```

::: zone-end


### <a name="get-secret-using-workspace-identity"></a>작업 영역 ID를 사용하여 비밀 가져오기

작업 영역 ID를 사용하여 지정된 Azure Key Vault 이름, 비밀 이름, 연결된 서비스 이름을 위한 Azure Key Vault 비밀을 반환합니다. [Azure Key Vault](#configure-access-to-azure-key-vault)에 대한 액세스를 적절하게 구성합니다.

:::zone pivot = "programming-language-python"

```python
mssparkutils.credentials.getSecret('azure key vault name','secret name','linked service name')
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.credentials.getSecret("azure key vault name","secret name","linked service name")
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
Credentials.GetSecret("azure key vault name","secret name","linked service name")
```

::: zone-end


### <a name="get-secret-using-user-credentials"></a>사용자 자격 증명을 사용하여 비밀 가져오기

사용자 자격 증명을 사용하여 지정된 Azure Key Vault 이름, 비밀 이름, 연결된 서비스 이름을 위한 Azure Key Vault 비밀을 반환합니다. 

:::zone pivot = "programming-language-python"

```python
mssparkutils.credentials.getSecret('azure key vault name','secret name')
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.credentials.getSecret("azure key vault name","secret name")
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
Credentials.GetSecret("azure key vault name","secret name")
```

::: zone-end

<!-- ### Put secret using workspace identity

Puts Azure Key Vault secret for a given Azure Key Vault name, secret name, and linked service name using workspace identity. Make sure you configure the access to [Azure Key Vault](#configure-access-to-azure-key-vault) appropriately. -->

:::zone pivot = "programming-language-python"

### <a name="put-secret-using-workspace-identity"></a>작업 영역 ID를 사용하여 비밀 배치

작업 영역 ID를 사용하여 지정된 Azure Key Vault 이름, 비밀 이름, 연결된 서비스 이름을 위한 Azure Key Vault 비밀을 넣습니다. [Azure Key Vault](#configure-access-to-azure-key-vault)에 대한 액세스를 적절하게 구성합니다.

```python
mssparkutils.credentials.putSecret('azure key vault name','secret name','secret value','linked service name')
```
::: zone-end

:::zone pivot = "programming-language-scala"

### <a name="put-secret-using-workspace-identity"></a>작업 영역 ID를 사용하여 비밀 배치

작업 영역 ID를 사용하여 지정된 Azure Key Vault 이름, 비밀 이름, 연결된 서비스 이름을 위한 Azure Key Vault 비밀을 넣습니다. [Azure Key Vault](#configure-access-to-azure-key-vault)에 대한 액세스를 적절하게 구성합니다.

```scala
mssparkutils.credentials.putSecret("azure key vault name","secret name","secret value","linked service name")
```

::: zone-end

<!-- :::zone pivot = "programming-language-csharp"

```csharp

```

::: zone-end -->


<!-- ### Put secret using user credentials

Puts Azure Key Vault secret for a given Azure Key Vault name, secret name, and linked service name using user credentials.  -->

:::zone pivot = "programming-language-python"

### <a name="put-secret-using-user-credentials"></a>사용자 자격 증명을 사용하여 비밀 입력

사용자 자격 증명을 사용하여 지정된 Azure Key Vault 이름, 비밀 이름, 연결된 서비스 이름을 위한 Azure Key Vault 비밀을 배치합니다. 

```python
mssparkutils.credentials.putSecret('azure key vault name','secret name','secret value')
```
::: zone-end

:::zone pivot = "programming-language-scala"

### <a name="put-secret-using-user-credentials"></a>사용자 자격 증명을 사용하여 비밀 입력

사용자 자격 증명을 사용하여 지정된 Azure Key Vault 이름, 비밀 이름, 연결된 서비스 이름을 위한 Azure Key Vault 비밀을 배치합니다. 

```scala
mssparkutils.credentials.putSecret("azure key vault name","secret name","secret value")
```

::: zone-end

<!-- :::zone pivot = "programming-language-csharp"

```csharp

```

::: zone-end -->


## <a name="environment-utilities"></a>환경 유틸리티 

사용 가능한 메서드에 관한 개요를 가져오려면 다음 명령을 실행합니다.

:::zone pivot = "programming-language-python"

```python
mssparkutils.env.help()
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.env.help()
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
Env.Help()
```

::: zone-end

결과 가져오기:
```
GetUserName(): returns user name
GetUserId(): returns unique user id
GetJobId(): returns job id
GetWorkspaceName(): returns workspace name
GetPoolName(): returns Spark pool name
GetClusterId(): returns cluster id
```

### <a name="get-user-name"></a>사용자 이름 가져오기

현재 사용자 이름을 반환합니다.

:::zone pivot = "programming-language-python"

```python
mssparkutils.env.getUserName()
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.env.getUserName()
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
Env.GetUserName()
```

::: zone-end

### <a name="get-user-id"></a>사용자 ID 가져오기

현재 사용자 ID를 반환합니다.

:::zone pivot = "programming-language-python"

```python
mssparkutils.env.getUserId()
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.env.getUserId()
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
Env.GetUserId()
```

::: zone-end

### <a name="get-job-id"></a>작업 ID 가져오기

작업 ID를 반환합니다.

:::zone pivot = "programming-language-python"

```python
mssparkutils.env.getJobId()
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.env.getJobId()
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
Env.GetJobId()
```

::: zone-end

### <a name="get-workspace-name"></a>작업 영역 이름 가져오기

작업 영역 이름을 반환합니다.

:::zone pivot = "programming-language-python"

```python
mssparkutils.env.getWorkspaceName()
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.env.getWorkspaceName()
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
Env.GetWorkspaceName()
```

::: zone-end

### <a name="get-pool-name"></a>풀 이름 가져오기

Spark 풀 이름을 반환합니다.

:::zone pivot = "programming-language-python"

```python
mssparkutils.env.getPoolName()
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.env.getPoolName()
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
Env.GetPoolName()
```

::: zone-end

### <a name="get-cluster-id"></a>클러스터 ID 가져오기

현재 클러스터 ID를 반환합니다.

:::zone pivot = "programming-language-python"

```python
mssparkutils.env.getClusterId()
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.env.getClusterId()
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
Env.GetClusterId()
```

::: zone-end

## <a name="next-steps"></a>다음 단계

- [Synapse 샘플 Notebooks 체크 아웃](https://github.com/Azure-Samples/Synapse/tree/master/Notebooks)
- [빠른 시작: 웹 도구를 사용하여 Azure Synapse Analytics에서 Apache Spark 풀 만들기](../quickstart-apache-spark-notebook.md)
- [Azure Synapse Analytics의 Apache Spark란?](apache-spark-overview.md)
- [Azure Synapse Analytics](../index.yml)
