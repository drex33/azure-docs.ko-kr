---
title: Azure Spring Cloud 고급 문제 해결을 위한 JVM 옵션의 진단 설정
description: 힙 덤프, JFR 및 GC 로그를 설정하는 JVM 구성의 몇 가지 모범 사례입니다.
author: YinglueZhang-MS
ms.author: yinglzh
ms.service: spring-cloud
ms.topic: how-to
ms.date: 10/31/2021
ms.custom: devx-track-java
ms.openlocfilehash: 259d39eb80907a8c27e77db91bc369d97e690169
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2021
ms.locfileid: "132716014"
---
# <a name="tutorial-diagnostic-settings-of-jvm-options-for-advanced-troubleshooting-in-azure-spring-cloud"></a>자습서: Azure Spring Cloud 고급 문제 해결을 위한 JVM 옵션의 진단 설정

**이 문서는 다음에 적용됩니다.** ✔️ Java ❌ C #

힙 덤프, JFR 및 GC 로그와 관련된 여러 JVM 기반 애플리케이션 시작 매개 변수가 있습니다. Azure Spring Cloud jvm-options를 사용하여 JVM을 구성할 수 있도록 지원합니다. 이 문서에서는 고객에게 유용할 수 있는 몇 가지 예를 제공합니다.

## <a name="prerequisites"></a>필수 구성 요소
이 연습을 완료하려면 다음이 필요합니다.

* 배포된 Azure Spring Cloud 서비스 인스턴스 [Azure CLI를 통한 앱 배포를 위한 빠른 시작](./quickstart.md)을 수행하여 시작하세요.
* 해당 서비스 인스턴스에서 이미 하나 이상의 애플리케이션이 생성되었습니다.
* 하나 이상의 [영구 스토리지가 이미 앱에 바인딩되어](how-to-built-in-persistent-storage.md) 생성된 진단 파일을 저장합니다.

사용자는 배포 [문서에](/cli/azure/spring-cloud/app/deployment?view=azure-cli-latest) 따라 JVM 기반 애플리케이션 시작 매개 변수를 구성할 수 있습니다. 다음 섹션에서는 몇 가지 예를 제공합니다. **수행해야 할 일은 jvm-options에 매개 변수를 추가하는 것입니다.**

## <a name="generate-a-heap-dump-when-out-of-memory"></a>메모리 부족 시 힙 덤프 생성
```heap dump when OOM
   -XX:+HeapDumpOnOutOfMemoryError -XX:HeapDumpPath=<PATH_TO_HEAP_DUMP_FOLDER> 
```
파일의 특정 이름을 지정할 수도 있습니다. 그러나 폴더 경로만 제공하는 것이 좋습니다. 파일 이름이 지정되면 첫 번째 OOM에서 힙 덤프만 생성됩니다. hprof-format 파일을 다룰 수 없으므로 사용자는 다음 OOM의 힙 덤프를 얻을 수 없습니다. 사용자가 폴더에 대한 경로만 제공하는 경우 사용자는 자동으로 생성되는 이름을 가진 모든 OOM의 힙 덤프를 얻게 됩니다.

아래에 지정된 모든 경로 사용자는 고객의 앱에 이미 바인딩된 사용자 영구 스토리지의 탑재 경로 아래에 있어야 합니다.

## <a name="gc-logs"></a>GC 로그
GC 로그와 관련된 JVM 옵션을 사용하는 방법의 예제만 제공합니다. 자세한 내용은 공식 JVM 문서로 이동하면 됩니다.
```GC Logs
   -XX:+PrintGCDetails -Xloggc:<PATH_TO_GC_LOG_FILE>
```

## <a name="jfr-on-exit"></a>종료할 때 JFR
JFR과 관련된 JVM 옵션을 사용하는 방법의 예제만 제공합니다. 자세한 내용은 공식 JVM 문서로 이동하면 됩니다.
```JFR on exit
   -XX:StartFlightRecording=dumponexit=true,dumponexitpath=<PATH_TO_JFR_FILE>
```

## <a name="path-of-generated-file"></a>생성된 파일의 경로입니다.
생성된 파일의 대상 경로가 앱의 영구 스토리지에 있어야 합니다. 스토리지에서 파일을 얻을 수 있도록 합니다.
예를 들어 고객은 사용자가 Azure Spring Cloud 영구 스토리지를 처음 만들 때 json 파일을 제공합니다.
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
또는 고객이 아래 명령을 사용하여 영구 스토리지를 추가합니다.
    
```
   az spring-cloud app append-persistent-storage --persistent-storage-type AzureFileVolume --share-name <azure-file-share-name> --mount-path <unique-mount-path e.g. /test/Path> --storage-name <storage-resource-name> -n <app-name> -g <resource-group-name> -s <spring-instance-name>
```
   
   고객이 위에 제공된 자체 Azure 파일 아래에 덤프를 사용하려는 경우 <PATH_TO_HEAP_DUMP_FOLDER> "/test/Path"로 제공하세요. 고객이 탑재 경로에서 경로를 사용하려는 경우 하위 경로가 이미 만들어졌는지 확인하세요.