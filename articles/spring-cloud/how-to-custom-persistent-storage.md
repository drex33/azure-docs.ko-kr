---
title: Azure Spring Cloud | 자체 영구 스토리지를 사용하도록 설정하는 방법 Microsoft Docs
description: Azure Spring Cloud 자체 스토리지를 영구 스토리지로 가져오는 방법
author: karlerickson
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/28/2021
ms.author: xuycao
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: 2bf84684851b19665e33af0cbfe902145b8ea565
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131483107"
---
# <a name="how-to-enable-your-own-persistent-storage-in-azure-spring-cloud"></a>Azure Spring Cloud 자체 영구 스토리지를 사용하도록 설정하는 방법

**이 문서는 다음에 적용됩니다.** ✔️ Java ✔️ C#

이 문서에서는 Azure Spring Cloud 고유한 영구 스토리지를 사용하도록 설정하는 방법을 보여 줍니다.

Azure Spring Cloud 기본 제공 영구 스토리지를 사용하는 경우 애플리케이션에서 생성된 아티팩트 Azure Storage 계정에 업로드됩니다. Microsoft는 이러한 아티팩트에 대한 미사용 데이터 암호화 및 수명 관리 정책을 제어합니다. 

BYOS(Bring Your Own Storage)를 사용하면 이러한 아티팩트는 사용자가 제어하는 스토리지 계정에 업로드됩니다. 즉, 미사용 데이터 암호화 정책, 수명 관리 정책 및 네트워크 액세스를 제어할 수 있습니다. 그러나 해당 스토리지 계정과 관련된 비용은 사용자가 부담합니다.

## <a name="prerequisites"></a>사전 요구 사항

* 기존 Azure Storage 계정 및 미리 만들어진 Azure 파일 공유. Azure에서 스토리지 계정 및 파일 공유를 만들어야 하는 경우 [Azure 파일 공유 만들기를](../storage/files/storage-how-to-create-file-share.md)참조하세요.
* Azure CLI 대한 Azure Spring Cloud [확장](/cli/azure/azure-cli-extensions-overview)

## <a name="use-the-azure-cli-to-enable-your-own-extra-persistent-storage"></a>Azure CLI 사용하여 고유한 추가 영구 스토리지를 사용하도록 설정

다음 단계를 사용하여 Azure CLI 사용자 고유의 스토리지를 사용하도록 설정할 수 있습니다.

1. 다음 명령을 사용하여 Azure Storage 계정을 Azure Spring Cloud 인스턴스의 스토리지 리소스로 바인딩합니다.

    ```azurecli
   az spring-cloud storage add --storage-type StorageAccount --account-name <account-name> --account-key <account-key>  -g <resource-group-name> -s <spring-instance-name> -n <storage-resource-name>
    ```

1. 다음 명령을 사용하여 고유한 영구 스토리지가 있는 앱을 만듭니다.

    ```azurecli
    az spring-cloud app create -n <app-name> -g <resource-group-name> -s <spring-instance-name> --persistent-storage <path-to-JSON-file>
    ```

    `--persistent-storage`create 명령의 매개 변수에 전달되는 JSON 파일의 샘플은 다음과 같습니다.

    ```json
    {
       "customPersistentDisks": [
          {
              "storageName": "<Storage-Resource-Name>",
              "customPersistentDiskProperties": {
                  "type": "AzureFileVolume",
                  "shareName": "<Azure-File-Share-Name>",
                  "mountPath": "<Unique-Mount-Path>",
                  "mountOptions": [
                      "uid=0",
                      "gid=0"
                   ],
                   "readOnly": false 
                }
          },
          {
              "storageName": "<Storage-Resource-Name>",
              "customPersistentDiskProperties": {
                  "type": "AzureFileVolume",
                  "shareName": "<Azure-File-Share-Name>",
                  "mountPath": "<Unique-Mount-Path e.g. /test/anotherPath>",
                  "readOnly": true
              }
          }
       ]
    }
    ```

1. 필요에 따라 다음 명령을 사용하여 기존 앱에 영구 스토리지를 추가합니다.

    ```azurecli
    az spring-cloud app append-persistent-storage --persistent-storage-type AzureFileVolume --share-name <azure-file-share-name> --mount-path <unique-mount-path> --storage-name <storage-resource-name> -n <app-name> -g <resource-group-name> -s <spring-instance-name>
    ```

1. 필요에 따라 다음 명령을 사용하여 특정 스토리지 리소스의 기존 영구 스토리지를 모두 나열합니다.

   ```azurecli
   az spring-cloud storage list-persistent-storage -g <resource-group-name> -s <spring-instance-name> -n <storage-resource-name>
   ```

## <a name="use-best-practices"></a>모범 사례 사용

Azure Spring Cloud 고유한 영구 스토리지를 추가할 때 사용하는 모범 사례입니다.

- 잠재적인 대기 시간 문제를 방지하려면 Azure Spring Cloud 인스턴스와 Azure Storage 계정을 동일한 Azure 지역에 배치합니다.

- Azure Storage 계정에서 사용 중인 계정 키를 다시 생성하지 않습니다. 스토리지 계정에는 두 개의 서로 다른 키가 포함되어 있습니다. 단계별 접근 방식을 사용하여 키를 다시 만드는 동안 애플리케이션에서 고유한 영구 스토리지를 계속 사용할 수 있도록 합니다. 

   예를 들어 key1을 사용하여 스토리지 계정을 Azure Spring Cloud 바인딩했다고 가정하면 다음 단계를 사용합니다.

   1. key2를 다시 생성합니다.
   1. 다시 생성한 key2를 사용하도록 스토리지 리소스의 계정 키를 업데이트합니다.
   1. 이 스토리지 리소스에서 영구 스토리지를 탑재하는 애플리케이션을 다시 시작합니다. (를 사용하여 ```az spring-cloud storage list-persistent-storage``` 모든 관련 애플리케이션을 나열할 수 있습니다.)
   1. key1을 다시 생성합니다.

- Azure Storage 계정 또는 Azure 파일 공유를 삭제하는 경우 가능한 오류를 방지하기 위해 애플리케이션에서 해당 스토리지 리소스 또는 영구 스토리지를 제거합니다.

## <a name="faqs"></a>FAQ

다음은 Azure Spring Cloud 함께 고유한 영구 스토리지를 사용하는 것에 대한 FAQ(질문과 대답)입니다.

- 기본 제공 영구 스토리지를 사용하도록 설정한 다음, 내 스토리지를 추가 영구 스토리지로 사용하도록 설정한 경우 내 데이터가 내 Storage 계정으로 마이그레이션될까요?

   *아니요. 하지만 곧 직접 마이그레이션하는 데 도움이 되는 문서를 제공할 예정입니다.*

- 예약된 탑재 경로란?

   *이러한 탑재 경로는 Azure Spring Cloud 서비스에서 예약됩니다.*
   - */tmp*
   - */persistent*
   - */secrets*
   - */app-insights/agents*
   - */etc/azure-spring-cloud/certs*
   - */app-insights/agents/settings*
   - */app-lifecycle/settings*

- 사용 가능한 탑재 옵션은 무엇인가요?

   *현재 지원되는 탑재 옵션은 다음과 같습니다.*
   - `uid`
   - `gid`
   - `file_mode`
   - `dir_mode`
   
   *`mountOptions`속성은 선택 사항입니다. 위의 탑재 옵션에 대한 기본값은 다음과 같습니다. ["uid=0", "gid=0", "file_mode=0777", "dir_mode=0777"]*

## <a name="next-steps"></a>다음 단계

* [애플리케이션 및 서비스 할당량](./quotas.md)에 대해 자세히 알아봅니다.
* [수동으로 애플리케이션 크기를 조정](./how-to-scale-manual.md)하는 방법을 알아봅니다.