---
title: 종속성 및 타사 라이브러리를 Azure Functions로 가져오기
description: 파일 또는 타사 라이브러리를 가져오는 방법 알아보기
ms.date: 4/6/2020
ms.topic: article
zone_pivot_groups: bring-third-party-dependency-programming-functions
ms.openlocfilehash: be01f07a9906da58d080060185104aef29f78b77
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130265308"
---
# <a name="bring-dependencies-or-third-party-library-to-azure-functions"></a>종속성 또는 타사 라이브러리를 Azure Functions로 가져오기

이 문서에서는 타사 종속성을 함수 앱으로 가져오는 방법을 알아봅니다. 타사 종속성의 예로는 json 파일, 이진 파일 및 기계 학습 모델이 있습니다. 

이 문서에서는 다음 방법을 설명합니다.
> [!div class="checklist"]
> * Functions 코드 프로젝트를 통해 종속성 가져오기 
::: zone pivot="programming-language-python"
> [!div class="checklist"]
> * 탑재된 Azure 파일 공유를 통해 종속성 가져오기
::: zone-end

## <a name="bring-in-dependencies-from-the-project-directory"></a>프로젝트 디렉터리에서 종속성 가져오기
::: zone pivot="programming-language-python"
종속성을 가져오는 가장 간단한 방법 중 하나는 Functions 프로젝트 디렉터리 구조에 파일/아티팩트를 함수 앱 코드와 함께 두는 것입니다. Python 함수 프로젝트의 디렉터리 샘플 예는 다음과 같습니다.
```
<project_root>/
 | - my_first_function/
 | | - __init__.py
 | | - function.json
 | | - example.py
 | - dependencies/
 | | - dependency1
 | - .funcignore
 | - host.json
 | - local.settings.json
```
종속성을 함수 앱 프로젝트 디렉터리 내 폴더에 두면 종속성 폴더가 코드와 함께 배포됩니다. 따라서 함수 코드는 파일 시스템 api를 통해 클라우드의 종속성에 액세스할 수 있습니다. 

### <a name="accessing-the-dependencies-in-your-code"></a>코드의 종속성에 액세스

다음은 ```<project_root>/ffmpeg_lib``` 디렉터리에 저장되는 ```ffmpeg``` 종속성에 액세스하고 실행하는 예입니다. 


```python
import logging

import azure.functions as func
import subprocess

FFMPEG_RELATIVE_PATH = "../ffmpeg_lib/ffmpeg"

def main(req: func.HttpRequest,
         context: func.Context) -> func.HttpResponse:
    logging.info('Python HTTP trigger function processed a request.')

    command = req.params.get('command')
    # If no command specified, set the command to help
    if not command:
        command = "-h"

    # context.function_directory returns the current directory in which functions is executed 
    ffmpeg_path = "/".join([str(context.function_directory), FFMPEG_RELATIVE_PATH])
    
    try:
        byte_output  = subprocess.check_output([ffmpeg_path, command])
        return func.HttpResponse(byte_output.decode('UTF-8').rstrip(),status_code=200)
    except Exception as e:
        return func.HttpResponse("Unexpected exception happened when executing ffmpeg. Error message:" + str(e),status_code=200)
```
>[!NOTE]
> Linux 환경에서 ffmpeg 이진에 대한 `Execute` 권리를 제공하려면 `chmod`를 사용해야 합니다.
::: zone-end

::: zone pivot="programming-language-java"
종속성을 가져오는 가장 간단한 방법 중 하나는 Functions 프로젝트 디렉터리 구조에 파일/아티팩트를 함수 앱 코드와 함께 두는 것입니다. Java 함수 프로젝트의 디렉터리 샘플 예는 다음과 같습니다.
```
<project_root>/
 | - src/
 | | - main/java/com/function
 | | | - Function.java
 | | - test/java/com/function
 | - artifacts/
 | | - dependency1
 | - host.json
 | - local.settings.json
 | - pom.xml
```
특히 Java의 경우 리소스를 복사할 때 특별히 빌드/대상 폴더에 아티팩트가 포함되어야 합니다. Maven에서 이 작업을 수행하는 방법의 예는 다음과 같습니다.

```xml
...
<execution>
    <id>copy-resources</id>
    <phase>package</phase>
    <goals>
        <goal>copy-resources</goal>
    </goals>
    <configuration>
        <overwrite>true</overwrite>
        <outputDirectory>${stagingDirectory}</outputDirectory>
        <resources>
            <resource>
                <directory>${project.basedir}</directory>
                <includes>
                    <include>host.json</include>
                    <include>local.settings.json</include>
                    <include>artifacts/**</include>
                </includes>
            </resource>
        </resources>
    </configuration>
</execution>
...
```
종속성을 함수 앱 프로젝트 디렉터리 내 폴더에 두면 종속성 폴더가 코드와 함께 배포됩니다. 따라서 함수 코드는 파일 시스템 api를 통해 클라우드의 종속성에 액세스할 수 있습니다. 

### <a name="accessing-the-dependencies-in-your-code"></a>코드의 종속성에 액세스

다음은 ```<project_root>/ffmpeg_lib``` 디렉터리에 저장되는 ```ffmpeg``` 종속성에 액세스하고 실행하는 예입니다. 


```java
public class Function {
    final static String BASE_PATH = "BASE_PATH";
    final static String FFMPEG_PATH = "/artifacts/ffmpeg/ffmpeg.exe";
    final static String HELP_FLAG = "-h";
    final static String COMMAND_QUERY = "command";

    @FunctionName("HttpExample")
    public HttpResponseMessage run(
            @HttpTrigger(
                name = "req",
                methods = {HttpMethod.GET, HttpMethod.POST},
                authLevel = AuthorizationLevel.ANONYMOUS)
                HttpRequestMessage<Optional<String>> request,
            final ExecutionContext context) throws IOException{
        context.getLogger().info("Java HTTP trigger processed a request.");
            
        // Parse query parameter
        String flags = request.getQueryParameters().get(COMMAND_QUERY);
        
        if (flags == null || flags.isBlank()) {
            flags = HELP_FLAG;
        }

        Runtime rt = Runtime.getRuntime();
        String[] commands = { System.getenv(BASE_PATH) + FFMPEG_PATH, flags};
        Process proc = rt.exec(commands);
        
        BufferedReader stdInput = new BufferedReader(new 
        InputStreamReader(proc.getInputStream()));
   
        String out = stdInput.lines().collect(Collectors.joining("\n"));
        if(out.isEmpty()) {
            BufferedReader stdError = new BufferedReader(new 
                InputStreamReader(proc.getErrorStream()));
            out = stdError.lines().collect(Collectors.joining("\n"));
        }
        return request.createResponseBuilder(HttpStatus.OK).body(out).build();

    }
```
>[!NOTE]
> Azure에서 이 코드 조각을 가져와 사용하려면 "/home/site/wwwroot" 값이 있는 "BASE_PATH"의 사용자 지정 애플리케이션 설정을 지정해야 합니다.
::: zone-end


::: zone pivot="programming-language-python"
## <a name="bring-dependencies-by-mounting-a-file-share"></a>파일 공유를 탑재하여 종속성 가져오기

Linux에서 함수 앱을 실행하는 경우 타사 종속성을 가져오는 또 다른 방법이 있습니다. Functions를 사용하면 Azure Files에서 호스트되는 파일 공유를 탑재할 수 있습니다. 애플리케이션 코드에서 종속성이나 아티팩트를 분리하려는 경우에 이 방법을 사용하는 것이 좋습니다.

먼저 Azure Stroage 계정을 만들어야 합니다. 계정에서 Azure Files에도 파일 공유를 만들어야 합니다. 이러한 리소스를 만들려면 이 [가이드](../storage/files/storage-how-to-use-files-portal.md)를 따르세요.

스토리지 계정과 파일 공유를 만든 후에는 다음 예와 같이 [az webapp config storage-account add](/cli/azure/webapp/config/storage-account#az_webapp_config_storage_account_add) 명령을 사용하여 파일 공유를 함수 앱에 연결합니다.

```console
az webapp config storage-account add \
  --name < Function-App-Name > \
  --resource-group < Resource-Group > \
  --subscription < Subscription-Id > \
  --custom-id < Unique-Custom-Id > \
  --storage-type AzureFiles \
  --account-name < Storage-Account-Name > \
  --share-name < File-Share-Name >  \
  --access-key < Storage-Account-AccessKey > \
  --mount-path </path/to/mount>
```



|       플래그    |  값     |
| ------------- | ---------------------------------- | 
| custom-id      |  고유한 모든 문자열 |
| storage-type      |  현재 AzureFiles만 지원됩니다. |
| share-name      |  기존 공유 |
| mount-path     |   컨테이너 내에서 공유에 액세스할 수 있는 경로입니다. 값은 `/dir-name` 형식이어야 하며 `/home`으로 시작할 수 없습니다. |

[여기](/cli/azure/webapp/config/storage-account#az-webapp-config-storage-account-update)에서 파일 공유 구성을 수정/삭제하는 더 많은 명령을 찾을 수 있습니다.


### <a name="uploading-the-dependencies-to-azure-files"></a>Azure Files에 종속성 업로드

종속성을 Azure Files에 업로드하는 한 가지 옵션은 Azure Portal을 통한 업로드입니다. 포털을 사용하여 종속성을 업로드하는 지침은 이 [가이드](../storage/files/storage-how-to-use-files-portal.md#upload-a-file)를 참조하세요. [Azure CLI](../storage/files/storage-how-to-use-files-portal.md#upload-a-file) 및 [PowerShell](../storage/files/storage-how-to-use-files-portal.md#upload-a-file)을 통해 종속성을 Azure Files에 업로드하는 다른 옵션도 있습니다.


### <a name="accessing-the-dependencies-in-your-code"></a>코드의 종속성에 액세스

종속성을 파일 공유에 업로드한 후에는 코드에서 종속성에 액세스할 수 있습니다. 지정된 *mount-path*(예: ```/path/to/mount```)에서 탑재된 공유를 사용할 수 있습니다. 파일 시스템 API를 사용하여 대상 디렉터리에 액세스할 수 있습니다.

다음 예에서는 탑재된 파일 공유에 저장된 `ffmpeg` 라이브러리에 액세스하는 HTTP 트리거 코드를 보여줍니다.

```python
import logging

import azure.functions as func
import subprocess 

FILE_SHARE_MOUNT_PATH = os.environ['FILE_SHARE_MOUNT_PATH']
FFMPEG = "ffmpeg"

def main(req: func.HttpRequest) -> func.HttpResponse:
    logging.info('Python HTTP trigger function processed a request.')

    command = req.params.get('command')
    # If no command specified, set the command to help
    if not command:
        command = "-h"

    try:
        byte_output  = subprocess.check_output(["/".join(FILE_SHARE_MOUNT_PATH, FFMPEG), command])
        return func.HttpResponse(byte_output.decode('UTF-8').rstrip(),status_code=200)
    except Exception as e:
        return func.HttpResponse("Unexpected exception happened when executing ffmpeg. Error message:" + str(e),status_code=200)
```

Azure의 함수 앱에 이 코드를 배포하는 경우 키 이름 `FILE_SHARE_MOUNT_PATH`와 탑재된 파일 공유 경로 값(이 예에서는 `/azure-files-share`)을 사용하여 [앱 설정을 만들어야](functions-how-to-use-azure-function-app-settings.md#settings) 합니다. 로컬 디버깅을 수행하려면 종속성이 로컬 컴퓨터에 저장된 파일 경로로 `FILE_SHARE_MOUNT_PATH`를 채워야 합니다. `local.settings.json`을 사용하여 `FILE_SHARE_MOUNT_PATH`를 설정하는 예는 다음과 같습니다.

```json
{
  "IsEncrypted": false,
  "Values": {
    "AzureWebJobsStorage": "",
    "FUNCTIONS_WORKER_RUNTIME": "python",
    "FILE_SHARE_MOUNT_PATH" : "PATH_TO_LOCAL_FFMPEG_DIR"
  }
}

```
::: zone-end



## <a name="next-steps"></a>다음 단계

+ [Azure Functions Python 개발자 가이드](functions-reference-python.md)
+ [Azure Functions Java 개발자 가이드](functions-reference-java.md)
+ [Azure Functions 개발자 참조](functions-reference.md)