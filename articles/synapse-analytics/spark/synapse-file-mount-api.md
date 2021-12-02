---
title: Synapse의 파일 탑재 소개
description: '자습서: Synapse에서 파일 탑재/분리 API를 사용 하는 방법'
author: ruixinxu
services: synapse-analytics
ms.service: synapse-analytics
ms.topic: reference
ms.subservice: spark
ms.date: 11/18/2021
ms.author: ruxu
ms.reviewer: ''
ms.custom: subject-rbac-steps
ms.openlocfilehash: 2f4520ed482ab63f7f7b26e0fe306c1aed8d49c7
ms.sourcegitcommit: 9ef0965834870700468c822ddcafc011881fc2d5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/02/2021
ms.locfileid: "133483102"
---
# <a name="how-to-use-file-mountunmount-api-in-synapse"></a>Synapse에서 파일 탑재/분리 API를 사용 하는 방법 

Synapse studio 팀은 mssparkutils 패키지에 두 개의 새로운 탑재/분리 Api를 구축 했습니다. 탑재를 사용 하 여 모든 작업 노드 (드라이버 노드 및 작업자 노드)에 원격 저장소 (Blob, Gen2, Azure 파일 공유)를 연결할 수 있습니다. 그 후에는 로컬 파일 API를 사용 하는 로컬 파일 시스템 처럼 저장소의 데이터에 액세스할 수 있습니다. 

이 문서에서는 작업 영역에서 탑재/분리 API를 사용 하는 방법을 보여 줍니다. 여기에는 주로 다음 섹션이 포함 됩니다. 

+ ADLS Gen2 Storage 또는 Azure Blob Storage를 탑재 하는 방법 
+ Azure 파일 공유를 탑재 하는 방법 
+ 로컬 파일 시스템 API를 통해 탑재 지점에서 파일에 액세스 하는 방법 
+ Mssparktuils fs API를 사용 하 여 탑재 지점에서 파일에 액세스 하는 방법 
+ Spark 읽기 API를 사용 하 여 탑재 지점에서 파일에 액세스 하는 방법 
+ 탑재 지점을 분리 하는 방법 


## <a name="how-to-mount-gen2blob-storage"></a>Gen2/blob Storage를 탑재 하는 방법  

여기에서는 gen2 storage 계정을 단계별로 탑재 하는 방법을 설명 합니다. 예를 들어 blob storage 탑재는 이와 유사 하 게 작동 합니다. 

**Storegen2** 라는 하나의 gen2 저장소 계정이 있고 계정에 **mycontainer** 라는 컨테이너 이름이 하나 있고, **Mycontainer/test** 를 Spark 풀에 탑재 하려는 경우를 가정 합니다. 

![Gen2 저장소 계정의 스크린샷](./media/synapse-file-mount-api/gen2-storage-account.png)

컨테이너 **mycontainer** 를 탑재 하려면 mssparkutils는 먼저 컨테이너에 액세스할 수 있는 권한이 있는지 여부를 확인 해야 합니다. 현재는 탑재 작업, **LinkeService**, **accountKey** 및 **sastoken** 를 트리거하는 세 가지 인증 방법을 지원 합니다. 

### <a name="via-linked-service-recommend"></a>연결 된 서비스를 통해 (권장):

연결 된 서비스를 통한 트리거 탑재는 권장 방법입니다. mssparkutils는 비밀/인증 값 자체를 저장 하지 않으므로 이러한 방식으로 보안 누수 문제가 발생 하지 않으며, 항상 연결 된 서비스에서 인증 값을 가져와 원격 저장소에서 blob 데이터를 요청 합니다. 

![링크 서비스의 스크린샷](./media/synapse-file-mount-api/synapse-link-service.png)

ADLS gen2 또는 blob 저장소에 대 한 연결 된 서비스를 만들 수 있습니다. 현재는 연결 된 서비스를 만들 때 두 가지 인증 방법이 지원 됩니다. 하나는 계정 키를 사용 하 고 다른 하나는 관리 되는 id를 사용 합니다. 

+ **계정 키** ![ 를 사용 하 여 연결 된 서비스 만들기 계정 키를 사용 하는 링크 서비스의 스크린샷](./media/synapse-file-mount-api/synapse-link-service-using-account-key.png)

+ **관리 id** ![ 를 사용 하 여 연결 된 서비스 만들기 관리 id를 사용 하는 링크 서비스의 스크린샷](./media/synapse-file-mount-api/synapse-link-service-using-managed-identity.png)

> [!NOTE]
> + 관리 id를 사용 하 여 인증 방법으로 연결 된 서비스를 만드는 경우 작업 영역 MSI에 탑재 된 컨테이너의 Storage Blob 데이터 참가자 역할이 있는지 확인 하세요. 
> + 연결 된 서비스가 성공적으로 생성 되도록 하려면 항상 연결 된 서비스 연결을 확인 하세요. 

연결 된 서비스를 성공적으로 만든 후 아래 코드를 사용 하 여 컨테이너를 Spark 풀에 쉽게 탑재할 수 있습니다. 

```python
mssparkutils.fs.mount( 
    "abfss://mycontainer@<accountname>.dfs.core.windows.net", 
    "/test", 
    {"linkedService":"mygen2account"} 
) 
``` 

**알림**:   

+ 사용할 수 없는 경우 mssparkutils를 가져와야 할 수 있습니다. 

    ```python
    From notebookutils import mssparkutils 
    ``` 
+ 사용 되는 인증 방법에 관계 없이 루트 폴더를 탑재 하는 것은 권장 되지 않습니다.


### <a name="via-shared-access-signature-token-or-account-key"></a>공유 액세스 서명 토큰 또는 계정 키를 통해  

Mssparkutils는 연결 된 서비스를 탑재 하는 것 외에도 명시적으로 계정 키 또는 [SAS (공유 액세스 서명)](/samples/azure-samples/storage-dotnet-sas-getting-started/storage-dotnet-sas-getting-started/) 토큰을 매개 변수로 전달 하 여 대상을 탑재 하도록 지원 합니다. 

계정 키 또는 SAS 토큰을 직접 사용 하 여 컨테이너를 탑재 하려는 경우 보다 안전한 방법은 Azure 주요 자격 증명 모음에 계정 키 또는 SAS 토큰을 저장 하 고 (아래 예제 그림 참조) API를 사용 하 여 검색 하는 것입니다 `mssparkutil.credentials.getSecret` . 


![주요 자격 증명 모음의 스크린샷](./media/synapse-file-mount-api/key-vaults.png)
 
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
> 코드에는 자격 증명을 작성 하지 않는 것이 좋습니다.


## <a name="how-to-mount-azure-file-shares"></a>Azure 파일 공유를 탑재 하는 방법

**Storegen2** 라는 gen2 storage 계정이 있고 계정에 myfilefile이라는 파일 공유가 하나 있고 작업 노드에 **myfilefile/test** 를 탑재 하려는 경우를 가정 합니다.
![파일 공유의 스크린샷](./media/synapse-file-mount-api/file-share.png)
 
Azure 파일 공유 탑재는 계정 키 인증 방법만 지원 하며, 다음은 **myfilefile/test** 를 탑재 하는 코드 샘플 이며 여기서 Azure 키 값 설정을 다시 사용 합니다 `MountKV` . 

```python 
from notebookutils import mssparkutils  

accountKey = mssparkutils.credentials.getSecret("MountKV","mySecret")  
mssparkutils.fs.mount(  
    "https://myfileshare@<accountname>.file.core.windows.net",  
    "/test",  
    {"accountKey":accountKey}  
) 
``` 

위의 예제에서는 파일 공유에 대 한 원본 URL의 스키마 형식을 미리 정의 했으며 `https://<filesharename>@<accountname>.file.core.windows.net` , 계정 키를 AKV에 저장 하 고,이를 탑재 api로 명시적으로 전달 하는 대신 mssparkutil를 사용 하 여 검색 합니다. 

   

## <a name="how-to-access-files-under-mount-point-via-local-file-system-api"></a>로컬 파일 시스템 API를 통해 탑재 지점에서 파일에 액세스 하는 방법

마운트가 성공적으로 실행 되 면 로컬 파일 시스템 API를 통해 데이터에 액세스할 수 있지만, 현재 탑재 지점은 항상 노드의 **/synfs** 폴더 아래에 생성 되 고 작업/세션 수준으로 범위가 제한 됩니다. 

예를 들어 **mycontainer** 를 탑재 하려는 경우 생성 된 로컬 탑재 지점은/synfs/{jobid}/입니다 .이는 성공적인 탑재 후 로컬 fs api를 통해 탑재 지점에 액세스 하려는 경우 사용 되는 로컬 경로는 `/synfs/{jobid}/` 

다음은 작동 방식을 보여 주는 예제입니다. 

```python 
jobId = mssparkutils.env.getJobId() 
f = open(f"/synfs/{jobId}/myFile.txt", "a") 
f.write("Hello world.") 
f.close() 
``` 

## <a name="how-to-access-files-under-mount-point-using-mssparktuils-fs-api"></a>Mssparktuils fs API를 사용 하 여 탑재 지점에서 파일에 액세스 하는 방법 

탑재 작업의 주요 목적은 고객이 로컬 파일 시스템 API를 사용 하 여 원격 저장소 계정에 저장 된 데이터에 액세스할 수 있도록 하는 것입니다. 또한 탑재 된 경로를 매개 변수로 사용 하는 mssparkutils fs API를 사용 하 여 데이터에 액세스할 수 있습니다. 여기서 사용 되는 경로 형식은 조금 다릅니다. 

여기에서 ADLS gen2 container **mycontainer** 에 탑재 했다고 가정 합니다. 

위의 섹션 공유와 같이 로컬 파일 시스템 API를 사용 하 여 데이터에 액세스 하는 경우 경로 형식은 다음과 같습니다. 

`/synfs/{jobId}/{filename}`

Mssparkutils fs API를 사용 하 여 데이터에 액세스 하려는 경우 경로 형식은 다음과 같습니다. 

`synfs://{jobId}/{filename}`

탑재 된 경로의 일부 대신이 경우 **syfs** 가 스키마로 사용 됩니다. 

다음은 mssparkutils fs를 사용 하 여 탑재 지점 경로를 사용 하 여 파일에 액세스 하는 방법을 보여 주는 세 가지 예제입니다. **49** 는 mssparkutils ()를 호출 하 여 가져온 SPARK 작업 ID입니다. 

+ 디렉터리 나열:  

    ```python 
    mssparkutils.fs.ls("synfs://49/") 
    ``` 

+ 파일 콘텐츠 읽기: 

    ```python 
    mssparkutils.fs.head("synfs://49/myFile.txt") 
    ``` 

+ 디렉터리 만들기: 

    ```python 
    mssparkutils.fs.mkdirs("synfs://49/mydir") 
    ``` 

 

## <a name="how-to-access-files-under-mount-point-using-spark-read-api"></a>Spark 읽기 API를 사용 하 여 탑재 지점에서 파일에 액세스 하는 방법 

탑재 된 경로와 함께 Spark 읽기 API를 매개 변수로 사용 하 여 탑재 후 데이터에 액세스할 수도 있습니다. 여기서 경로 형식은 mssparkutils fs API를 사용 하는 형식과 동일 합니다. 

`synfs://{jobId}/{filename} `

다음은 두 가지 코드 예제입니다. 하나는 탑재 된 gen2 저장소를 위한 것이 고 다른 하나는 탑재 된 blob 저장소에 대 한 것입니다. 

### <a name="read-file-from-a-mounted-gen2-storage-account"></a>탑재 된 gen2 저장소 계정에서 파일 읽기 

```python 
%%pyspark 

# Assume a gen2 storage was already mounted then read file using mount path 

df = spark.read.load("synfs://49/myFile.csv", format='csv') 
df.show() 
``` 

### <a name="read-file-from-a-mounted-blob-storage-account"></a>탑재 된 blob 저장소 계정에서 파일 읽기 

Blob storage 계정을 탑재 한 후 **mssparkutils 또는 SPARK API** 를 사용 하 여 액세스 하려는 경우 데이터를 읽기 전에 먼저 연결 된 서비스를 통해 sas 토큰을 명시적으로 구성 해야 합니다. 

1. 연결 서비스 **myblobstorageaccount** 만들기, 링크 서비스를 사용 하 여 blob storage 계정 탑재 

    ```python 
    %%spark 

    mssparkutils.fs.mount( 
        "wasbs://mycontainer@<blobStorageAccountName>.blob.core.windows.net", 
        "/test", 
        Map("linkedService" -> "myblobstorageaccount") 
    ) 
    ``` 

2. Spark 구성을 업데이트 하 고 Spark Api를 사용 하 여 데이터에 액세스 합니다 .이 단계가 필요 합니다. 
    ```python 
    blob_sas_token = mssparkutils.credentials.getConnectionStringOrCreds("myblobstorageaccount") 

    spark.conf.set('fs.azure.sas.mycontainer.<blobStorageAccountName>.blob.core.windows.net', blob_sas_token) 
    ``` 

3. Spark 읽기 API를 통해 탑재 된 blob storage에서 데이터를 읽습니다. 
    ```python
    %%spark
    // mount blob storage container and then read file using mount path
    val df = spark.read.text("synfs://49/test/myFile.txt")
    df.show()
    ```

## <a name="how-to-unmount-the-mount-point"></a>탑재 지점을 분리 하는 방법 

다음만 호출: 
```python 
mssparkutils.fs.unmount("/your_mount_point") 
``` 

## <a name="known-limitations"></a>알려진 제한 사항

+ Mssparkutils fs help 함수에 탑재/탑재 해제 파트에 대 한 설명이 아직 추가 되지 않았습니다. 

+ 또한 응용 프로그램 실행이 완료 되 면 탑재 지점을 제거 하는 자동 분리 메커니즘을 지원 합니다. 현재이 기능은 아직 구현 되지 않았습니다. 디스크 공간을 해제 하기 위해 탑재 지점을 분리 하려는 경우 코드에서 분리 API를 명시적으로 호출 해야 합니다. 그렇지 않으면 응용 프로그램 실행이 완료 된 후에도 여전히 노드에 탑재 지점이 남아 있습니다. 

+ 지금은 ADLS Gen1 storage 계정을 탑재할 수 없습니다. 

 