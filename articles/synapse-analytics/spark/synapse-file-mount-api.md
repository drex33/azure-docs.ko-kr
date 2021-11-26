---
title: synapse의 파일 탑재 소개
description: '자습서: Synapse에서 파일 탑재/탑재 해제 API를 재생하는 방법'
author: ruixinxu
services: synapse-analytics
ms.service: synapse-analytics
ms.topic: reference
ms.subservice: spark
ms.date: 11/18/2021
ms.author: ruxu
ms.reviewer: ''
ms.custom: subject-rbac-steps
ms.openlocfilehash: 6f34438dd8ce14c560bbd6cfab9d641affb04b9e
ms.sourcegitcommit: fc912bf0540585f44c09c6d63728c05c5dda558b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/25/2021
ms.locfileid: "133128952"
---
# <a name="how-to-play-with-file-mountunmount-api-in-synapse"></a>Synapse에서 파일 탑재/탑재 해제 API를 재생하는 방법 

Synapse Studio 팀은 mssparkutils 패키지에 두 개의 새로운 탑재/탑재 해제 API를 빌드했습니다. 탑재를 사용하여 원격 스토리지(Blob, Gen2, Azure 파일 공유)를 모든 작업 노드(드라이버 노드 및 작업자 노드)에 연결할 수 있습니다. 그런 다음 로컬 파일 API를 사용하는 로컬 파일 시스템인 것처럼 스토리지의 데이터에 액세스할 수 있습니다. 

이 문서에서는 작업 영역에서 탑재/탑재 해제 API를 재생하는 방법을 보여 줍니다. 주로 아래 섹션이 포함되어 있습니다. 

+ ADLS Gen2 Storage 또는 Azure Blob Storage 탑재하는 방법 
+ Azure 파일 공유를 탑재하는 방법 
+ 로컬 파일 시스템 API를 통해 탑재 지점 아래의 파일에 액세스하는 방법 
+ mssparktuils fs API를 사용하여 탑재 지점 아래의 파일에 액세스하는 방법 
+ Spark Read API를 사용하여 탑재 지점 아래의 파일에 액세스하는 방법 
+ 탑재 지점을 분리하는 방법 


## <a name="how-to-mount-gen2blob-storage"></a>Gen2/Blob Storage 탑재하는 방법  

여기서는 Gen2 스토리지 계정을 단계별로 탑재하는 방법을 보여 줍니다. 예를 들어 Blob Storage 탑재도 비슷하게 작동합니다. 

**storegen2라는** gen2 스토리지 계정이 하나 있고 계정에 **mycontainer라는** 컨테이너 이름이 하나 있고 spark 풀에 **mycontainer/test를** 탑재하려는 경우 

![gen2 스토리지 계정의 스크린샷](./media/synapse-file-mount-api/gen2-storage-account.png)

**컨테이너 mycontainer**, mssparkutils를 탑재하려면 처음에 컨테이너에 액세스할 수 있는 권한이 있는지 확인해야 합니다. 현재 탑재 작업을 트리거하는 세 가지 인증 방법 **LinkeService**, **accountKey** 및 **sastoken** 을 지원합니다. 

### <a name="via-linked-service-recommend"></a>연결된 서비스를 통해(권장):

연결된 서비스를 통해 탑재를 트리거하는 것이 좋습니다. mssparkutils는 비밀/인증 값 자체를 저장하지 않고 항상 연결된 서비스에서 인증 값을 가져와 원격 스토리지에서 Blob 데이터를 요청하기 때문에 이러한 방식으로 보안 누수 문제가 발생하지 않습니다. 

![링크 서비스의 스크린샷](./media/synapse-file-mount-api/synapse-link-service.png)

ADLS gen2 또는 Blob Storage에 대한 연결된 서비스를 만들 수 있습니다. 현재 연결된 서비스를 만들 때 두 가지 인증 방법이 지원됩니다. 하나는 계정 키를 사용하고 다른 하나는 관리 ID를 사용하는 것입니다. 

+ **계정 키를** ![ 사용하여 연결된 서비스 만들기 계정 키를 사용하는 링크 서비스의 스크린샷](./media/synapse-file-mount-api/synapse-link-service-using-account-key.png)

+ **관리 ID를** ![ 사용하여 연결된 서비스 만들기 관리 ID를 사용하는 링크 서비스의 스크린샷](./media/synapse-file-mount-api/synapse-link-service-using-managed-identity.png)

> [!NOTE]
> + 관리 ID를 인증 방법으로 사용하여 연결된 서비스를 만드는 경우 작업 영역 MSI에 탑재된 컨테이너의 Storage Blob 데이터 기여자 역할이 있는지 확인하세요. 
> + 연결된 서비스가 성공적으로 생성되었는지 확인하려면 항상 연결된 서비스 연결을 확인하세요. 

연결된 서비스를 성공적으로 만든 후에는 아래 코드를 사용하여 컨테이너를 Spark 풀에 쉽게 탑재할 수 있습니다. 

```python
mssparkutils.fs.mount( 
    "abfss://mycontainer@<accountname>.dfs.core.windows.net", 
    "/test", 
    {"linkedService":"mygen2account"} 
) 
``` 

**주의:**   

+ 사용할 수 없는 경우 mssparkutils를 가져와야 할 수 있습니다. 

    ```python
    From notebookutils import mssparkutils 
    ``` 
+ 어떤 인증 방법을 사용하든 루트 폴더를 탑재하지 않는 것이 좋습니다.


### <a name="via-shared-access-signature-token-or-account-key"></a>공유 액세스 서명 토큰 또는 계정 키를 통해  

mssparkutils는 연결된 서비스를 사용하여 탑재하는 것 외에도 계정 키 또는 [SAS(공유 액세스 서명)](/samples/azure-samples/storage-dotnet-sas-getting-started/storage-dotnet-sas-getting-started/) 토큰을 매개 변수로 명시적으로 전달하여 대상을 탑재하도록 지원합니다. 

계정 키 또는 SAS 토큰을 직접 사용하여 컨테이너를 탑재하려는 경우 보다 안전한 방법은 아래 그림과 같이 Azure Key Vault에 계정 키 또는 SAS 토큰을 저장한 다음, API를 사용하여 검색하는 `mssparkutil.credentials.getSecret` 것입니다. 


![키 자격 증명 모음의 스크린샷](./media/synapse-file-mount-api/key-vaults.png)
 
샘플 코드는 다음과 같습니다. 

```python 
from notebookutils import mssparkutils  

accountKey = mssparkutils.credentials.getSecret("MountKV","mySecret")  
mssparkutils.fs.mount(  
    "abfss://mycontainer@<accountname>.dfs.core.windows.net",  
    "/test",  
    {"accountKey":accountKey}
) 
``` 

> [!Note]
> 코드에서 자격 증명을 작성하지 않는 것이 좋습니다.


## <a name="how-to-mount-azure-file-shares"></a>Azure 파일 공유를 탑재하는 방법

**storegen2라는 gen2** 스토리지 계정이 있고 계정에 **myfileshare라는** 하나의 파일 공유가 있다고 가정하고 작업 노드에 **myfileshare/test를** 탑재하려고 합니다.
![파일 공유 스크린샷](./media/synapse-file-mount-api/file-share.png)
 
Azure 파일 공유 탑재는 계정 키 인증 방법만 지원합니다. 다음은 **myfileshare/test를** 탑재하는 코드 샘플이며, 여기서 Azure 키 값 설정을 다시 사용합니다. `MountKV` 

```python 
from notebookutils import mssparkutils  

accountKey = mssparkutils.credentials.getSecret("MountKV","mySecret")  
mssparkutils.fs.mount(  
    "https://myfileshare@<accountname>.file.core.windows.net",  
    "/test",  
    {"accountKey":accountKey}  
) 
``` 

위의 예제에서는 파일 공유에 대한 원본 URL의 스키마 형식을 로 미리 `https://<filesharename>@<accountname>.file.core.windows.net` 정의하고, 계정 키를 AKV에 저장하고, 탑재 API에 명시적으로 전달하는 대신 mssparkutil.credentials.getSecret API를 사용하여 검색했습니다. 

   

## <a name="how-to-access-files-under-mount-point-via-local-file-system-api"></a>로컬 파일 시스템 API를 통해 탑재 지점 아래의 파일에 액세스하는 방법

탑재가 성공적으로 실행되면 로컬 파일 시스템 API를 통해 데이터에 액세스할 수 있지만, 현재는 **노드의 /synfs** 폴더 아래에 항상 생성되는 탑재 지점을 제한하며 작업/세션 수준으로 범위가 제한되었습니다. 

따라서 예를 들어 **mycontainer** 를 탑재하려는 경우 생성된 로컬 탑재 지점은 /synfs/{jobid}/입니다. 즉, 성공적으로 탑재된 후 로컬 fs API를 통해 탑재 지점에 액세스하려는 경우 로컬 경로가 사용되어야 합니다. `/synfs/{jobid}/` 

다음은 작동 방식을 보여주는 예제입니다. 

```python 
jobId = mssparkutils.env.getJobId() 
f = open(f"/synfs/{jobId}/myFile.txt", "a") 
f.write("Hello world.") 
f.close() 
``` 

## <a name="how-to-access-files-under-mount-point-using-mssparktuils-fs-api"></a>mssparktuils fs API를 사용하여 탑재 지점 아래의 파일에 액세스하는 방법 

탑재 작업의 주요 목적은 고객이 로컬 파일 시스템 API를 사용하여 원격 스토리지 계정에 저장된 데이터에 액세스할 수 있도록 하는 것입니다. 탑재된 경로를 매개 변수로 사용하여 mssparkutils fs API를 사용하여 데이터에 액세스할 수도 있습니다. 여기서 사용되는 경로 형식은 약간 다릅니다. 

여기서 ADLS gen2 컨테이너 **mycontainer에** 탑재했다고 가정합니다. 

위의 섹션에서 공유한 것처럼 로컬 파일 시스템 API를 사용하여 데이터에 액세스하는 경우 경로 형식은 와 같습니다. 

`/synfs/{jobId}/{filename}`

mssparkutils fs API를 사용하여 데이터에 액세스하려는 경우 경로 형식은 다음과 같습니다. 

`synfs://{jobId}/{filename}`

이 경우 **탑재된 경로의** 일부가 아닌 스키마로 사용되는 합성을 확인할 수 있습니다. 

다음은 mssparkutils fs를 사용하여 탑재 지점 경로로 파일에 액세스하는 방법을 보여주는 세 가지 예제이며, **49는** mssparkutils.env.getJobId()를 호출하여 얻은 Spark 작업 ID입니다. 

+ dirs 나열:  

    ```python 
    mssparkutils.fs.ls("synfs://49/") 
    ``` 

+ 파일 콘텐츠를 읽습니다. 

    ```python 
    mssparkutils.fs.head("synfs://49/myFile.txt") 
    ``` 

+ 디렉터리 만들기: 

    ```python 
    mssparkutils.fs.mkdirs("synfs://49/mydir") 
    ``` 

 

## <a name="how-to-access-files-under-mount-point-using-spark-read-api"></a>Spark Read API를 사용하여 탑재 지점 아래의 파일에 액세스하는 방법 

탑재된 경로와 함께 Spark 읽기 API를 매개 변수로 사용하여 탑재 후 데이터에 액세스할 수도 있습니다. 여기서 경로 형식은 mssparkutils fs API를 사용하는 형식과 동일합니다. 

`synfs://{jobId}/{filename} `

다음은 탑재된 gen2 스토리지에 대한 코드 예제이고, 다른 하나는 탑재된 Blob Storage용 코드 예제입니다. 

### <a name="read-file-from-a-mounted-gen2-storage-account"></a>탑재된 gen2 스토리지 계정에서 파일 읽기 

```python 
%%pyspark 

# Assume a gen2 storage was already mounted then read file using mount path 

df = spark.read.load("synfs://49/myFile.csv", format='csv') 
df.show() 
``` 

### <a name="read-file-from-a-mounted-blob-storage-account"></a>탑재된 Blob Storage 계정에서 파일 읽기 

Blob Storage 계정을 탑재한 다음 **mssparkutils 또는 Spark API를** 사용하여 액세스하려는 경우 데이터를 읽기 전에 먼저 연결된 서비스를 통해 sas 토큰을 명시적으로 구성해야 합니다. 

1. 링크 서비스 **myblobstorageaccount** 만들기 , 링크 서비스를 사용하여 Blob Storage 계정 탑재 

    ```python 
    %%spark 

    mssparkutils.fs.mount( 
        "wasbs://mycontainer@<blobStorageAccountName>.blob.core.windows.net", 
        "/test", 
        Map("linkedService" -> "myblobstorageaccount") 
    ) 
    ``` 

2. Spark 구성을 업데이트하고 Spark API를 사용하여 데이터에 액세스합니다. 이 단계가 필요합니다. 
    ```python 
    blob_sas_token = mssparkutils.credentials.getConnectionStringOrCreds("myblobstorageaccount") 

    spark.conf.set('fs.azure.sas.mycontainer.<blobStorageAccountName>.blob.core.windows.net', blob_sas_token) 
    ``` 

3. Spark 읽기 API를 통해 탑재된 Blob Storage에서 데이터를 읽습니다. 
    ```python
    %%spark
    // mount blob storage container and then read file using mount path
    val df = spark.read.text("synfs://49/test/myFile.txt")
    df.show()
    ```

## <a name="how-to-unmount-the-mount-point"></a>탑재 지점을 분리하는 방법 

다음을 호출합니다. 
```python 
mssparkutils.fs.unmount("/your_mount_point") 
``` 

## <a name="known-limitations"></a>알려진 제한 사항

+ mssparkutils fs help 함수는 탑재/탑재 해제 부분에 대한 설명을 아직 추가하지 않았습니다. 

+ 또한 애플리케이션 실행이 완료되면 탑재 지점을 제거하는 자동 탑재 해제 메커니즘을 지원하며, 현재 구현되지 않았습니다. 탑재 지점을 분리하여 디스크 공간을 해제하려면 코드에서 분리 API를 명시적으로 호출해야 합니다. 그렇지 않으면 애플리케이션 실행이 완료된 후에도 탑재 지점이 노드에 계속 존재합니다. 

+ ADLS Gen1 스토리지 계정 탑재는 현재 지원되지 않습니다. 

 