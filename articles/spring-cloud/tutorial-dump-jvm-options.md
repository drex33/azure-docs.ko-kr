---
title: Azure 스프링 클라우드의 고급 문제 해결을 위한 JVM 옵션의 진단 설정
description: 힙 덤프, JFR 및 GC 로그를 설정 하는 JVM 구성의 몇 가지 모범 사례입니다.
author: YinglueZhang-MS
ms.author: yinglzh
ms.service: spring-cloud
ms.topic: how-to
ms.date: 10/31/2021
ms.custom: devx-track-java
ms.openlocfilehash: fd29b9b7356bc16132d46f125d3f3cf2792c1654
ms.sourcegitcommit: 27ddccfa351f574431fb4775e5cd486eb21080e0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/08/2021
ms.locfileid: "132000804"
---
# <a name="tutorial-diagnostic-settings-of-jvm-options-for-advanced-troubleshooting-in-azure-spring-cloud"></a>자습서: Azure 스프링 클라우드의 고급 문제 해결을 위한 JVM 옵션의 진단 설정

**이 문서는 다음에 적용 됩니다.** ✔️ Java ❌ C #

힙 덤프, JFR 및 GC 로그와 관련 된 몇 가지 JVM 기반 응용 프로그램 시작 매개 변수가 있습니다. Azure 스프링 클라우드에서는 jvm-옵션을 사용 하 여 JVM 구성을 지원 합니다. 이 문서에서는 고객에 게 유용할 수 있는 몇 가지 예제를 제공 합니다.

## <a name="prerequisites"></a>필수 구성 요소
이 연습을 완료하려면 다음이 필요합니다.

* 배포된 Azure Spring Cloud 서비스 인스턴스 [Azure CLI를 통한 앱 배포를 위한 빠른 시작](./quickstart.md)을 수행하여 시작하세요.
* 해당 서비스 인스턴스에서 이미 하나 이상의 애플리케이션이 생성되었습니다.
* 생성 된 진단 파일을 저장 하기 위해 이미 하나 이상의 [영구 저장소가 앱에 바인딩되어](how-to-built-in-persistent-storage.md) 있습니다.

사용자는 [배포 문서](https://docs.microsoft.com/cli/azure/spring-cloud/app/deployment?view=azure-cli-latest) 에 따라 JVM 기반 응용 프로그램 시작 매개 변수를 구성 합니다. 다음 섹션에서는 몇 가지 예제를 제공 합니다. **필요한 작업은 jvm-옵션에서 매개 변수를 추가 하는 것입니다.**

## <a name="generate-a-heap-dump-when-out-of-memory"></a>메모리가 부족 한 경우 힙 덤프 생성
```heap dump when OOM
   -XX:+HeapDumpOnOutOfMemoryError -XX:HeapDumpPath=<PATH_TO_HEAP_DUMP_FOLDER> 
```
또한 파일의 특정 이름을 지정할 수 있습니다. 그러나 폴더 경로만을 지정 하는 것이 좋습니다. 파일 이름이 지정 되 면 첫 번째 OOM에서 힙 덤프가 생성 됩니다. Hprof 파일을 검색할 수 없기 때문에 사용자는 다음 OOM의 힙 덤프를 가져올 수 없습니다. 사용자가 폴더에 대 한 경로만 제공 하는 경우 사용자는 자동 생성 된 이름으로 모든 OOMs의 힙 덤프를 가져옵니다.

아래에 지정 된 모든 사용자 경로는 사용자의 영구 저장소의 탑재 경로 아래에 이미 고객의 앱에 바인딩되어 있어야 합니다.

## <a name="gc-logs"></a>GC 로그
GC 로그와 관련 된 JVM 옵션을 사용 하는 방법에 대 한 예제를 제공 합니다. 자세한 내용은 공식 JVM doc로 이동할 수 있습니다.
```GC Logs
   -XX:+PrintGCDetails -Xloggc:<PATH_TO_GC_LOG_FILE>
```

## <a name="jfr-on-exit"></a>JFR on exit
JFR과 관련 된 JVM 옵션을 사용 하는 방법에 대 한 예제를 제공 합니다. 자세한 내용은 공식 JVM doc로 이동할 수 있습니다.
```JFR on exit
   -XX:StartFlightRecording=dumponexit=true,dumponexitpath=<PATH_TO_JFR_FILE>
```

## <a name="path-of-generated-file"></a>생성 된 파일의 경로입니다.
생성 된 파일의 대상 경로가 앱의 영구 저장소에 있는지 확인 해야 합니다. 저장소에서 파일을 가져올 수 있습니다.
예를 들어 고객은 사용자가 Azure 스프링 클라우드에서 영구 저장소를 먼저 만들 때 json 파일을 제공 합니다.
```json
    {
       "customPersistentDisks": [
          {
              "storageName": "<storage-resource-name>",
              "customPersistentDiskProperties": {
                  "type": "AzureFileVolume",
                  "shareName": "<azure-file-share-name>",
                  "mountPath": "<unique-mount-path e.g. /test/Path>",
                  "mountOptions": [
                      "uid=0",
                      "gid=0"
                   ],
                   "readOnly": false 
                }
          },
          {
              "storageName": "<storage-resource-name>",
              "customPersistentDiskProperties": {
                  "type": "AzureFileVolume",
                  "shareName": "<azure-file-share-name>",
                  "mountPath": "<unique-mount-path e.g. /test/anotherPath>",
                  "readOnly": true
              }
          }
       ]
    }
```
또는 고객은 아래 명령을 사용 하 여 영구 저장소를 추가할 수 있습니다.
    
```
   az spring-cloud app append-persistent-storage --persistent-storage-type AzureFileVolume --share-name <azure-file-share-name> --mount-path <unique-mount-path e.g. /test/Path> --storage-name <storage-resource-name> -n <app-name> -g <resource-group-name> -s <spring-instance-name>
```
   
   고객이 위에 지정 된 자체 Azure 파일 아래에 덤프를 포함 하려면 "/test/Path"로> PATH_TO_HEAP_DUMP_FOLDER <을 제공 하세요. 고객이 탑재 경로 아래에 경로를 사용 하려면 하위 경로가 이미 만들어져 있는지 확인 하세요.




