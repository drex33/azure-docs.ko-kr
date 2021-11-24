---
title: Azure 스프링 클라우드에서 사용자 고유의 영구 저장소를 사용 하도록 설정 하는 방법 | Microsoft Docs
description: Azure 스프링 클라우드에서 사용자 고유의 저장소를 영구 저장소로 가져오는 방법
author: karlerickson
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/28/2021
ms.author: xuycao
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: fc4fe991e48a0ab6abeff96edfe01d678f3ad344
ms.sourcegitcommit: 1aeff9f012cfd868104ef0159c5204e402d75696
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133030038"
---
# <a name="how-to-enable-your-own-persistent-storage-in-azure-spring-cloud"></a>Azure 스프링 클라우드에서 사용자 고유의 영구 저장소를 사용 하도록 설정 하는 방법

**이 문서는 다음에 적용됩니다.** ✔️ Java ✔️ C#

이 문서에서는 Azure 스프링 클라우드에서 사용자 고유의 영구 저장소를 사용 하도록 설정 하는 방법을 보여 줍니다.

Azure 스프링 클라우드에서 기본 제공 영구 저장소를 사용 하는 경우 응용 프로그램에서 생성 된 아티팩트가 Azure Storage 계정에 업로드 됩니다. Microsoft는 이러한 아티팩트에 대한 미사용 데이터 암호화 및 수명 관리 정책을 제어합니다. 

BYOS(Bring Your Own Storage)를 사용하면 이러한 아티팩트는 사용자가 제어하는 스토리지 계정에 업로드됩니다. 즉, 미사용 데이터 암호화 정책, 수명 관리 정책 및 네트워크 액세스를 제어할 수 있습니다. 그러나 해당 스토리지 계정과 관련된 비용은 사용자가 부담합니다.

## <a name="prerequisites"></a>필수 구성 요소

* 기존 Azure Storage 계정 및 미리 만든 Azure 파일 공유. Azure에서 저장소 계정 및 파일 공유를 만들어야 하는 경우 [azure 파일 공유 만들기](../storage/files/storage-how-to-create-file-share.md)를 참조 하세요.
* Azure CLI에 대 한 [Azure 스프링 클라우드 확장](/cli/azure/azure-cli-extensions-overview)

## <a name="mount-your-own-extra-persistent-storage-to-applications"></a>응용 프로그램에 추가 영구 저장소 탑재

> [!NOTE]
> 영구 저장소를 업데이트 하면 응용 프로그램이 다시 시작 됩니다.

# <a name="portal"></a>[포털](#tab/Azure-portal)

다음 단계를 사용 하 여 Azure Storage 계정을 Azure 스프링 클라우드의 저장소 리소스로 바인딩하고 고유한 영구 저장소로 앱을 만듭니다.

1. 서비스 **개요** 페이지로 이동한 다음 왼쪽 탐색 창에서 **Storage** 을 선택 합니다.

1. **Storage** 페이지에서 **저장소 추가** 를 선택 하 고 다음 표에 있는 값을 추가한 다음 **적용** 을 선택 합니다.

   | 설정      | 값                                                                                      |
   |--------------|--------------------------------------------------------------------------------------------|
   | 스토리지 이름 | Azure 스프링 클라우드의 서비스 수준 리소스인 저장소 리소스의 이름입니다. |
   | 계정 이름 | 스토리지 계정 이름입니다.                                                           |
   | 계정 키  | 스토리지 계정 키입니다.                                                                   |

   :::image type="content" source="media/how-to-custom-persistent-storage/add-storage-resource.png" alt-text="Storage 페이지 및 ' 저장소 추가 ' 창을 보여 주는 Azure Portal 스크린샷" lightbox="media/how-to-custom-persistent-storage/add-storage-resource.png":::

1. **앱** 페이지로 이동한 다음 영구 저장소를 탑재할 응용 프로그램을 선택 합니다.

   :::image type="content" source="media/how-to-custom-persistent-storage/select-app-mount-persistent-storage.png" alt-text="앱 페이지의 스크린샷" lightbox="media/how-to-custom-persistent-storage/select-app-mount-persistent-storage.png":::

1. **구성** 을 선택한 다음 **영구 Storage** 를 선택 합니다.

1. **영구 저장소 추가** 를 선택 하 고 다음 표에 있는 값을 추가한 다음 **적용** 을 선택 합니다.

    | 설정                 | 값                                                          |
    |-------------------------|----------------------------------------------------------------|
    | 스토리지 이름            | 이전에 입력 한 저장소 리소스의 이름입니다.     |
    | 영구 저장소 유형 | **AzureFileVolume**                                            |
    | 공유 이름              | Azure Storage 계정의 Azure 파일 공유 이름입니다. |
    | 탑재 경로              | 고유한 탑재 경로입니다.                                           |
    | 탑재 옵션           | 선택 사항                                                       |
    | 읽기 전용               | 선택 사항                                                       |

   :::image type="content" source="media/how-to-custom-persistent-storage/add-persistent-storage.png" alt-text="' 영구 저장소 추가 ' 폼의 스크린샷":::

1. **저장** 을 선택 하 여 모든 구성 변경 내용을 적용 합니다.

   :::image type="content" source="media/how-to-custom-persistent-storage/save-persistent-storage-changes.png" alt-text="구성 페이지의 영구 Storage 섹션 스크린샷" lightbox="media/how-to-custom-persistent-storage/save-persistent-storage-changes.png":::

# <a name="cli"></a>[CLI](#tab/Azure-CLI)

다음 단계를 사용 하 여 Azure CLI에서 사용자 고유의 저장소를 사용 하도록 설정할 수 있습니다.

1. 다음 명령을 사용 하 여 Azure Storage 계정을 Azure 스프링 클라우드 인스턴스의 저장소 리소스로 바인딩합니다.

    ```azurecli
   az spring-cloud storage add \
       --resource-group <resource-group-name> \
       --service <Azure-Spring-Cloud-instance-name> \
       --name <storage-resource-name> \
       --storage-type StorageAccount \
       --account-name <account-name> \
       --account-key <account-key>
    ```

1. 다음 명령을 사용 하 여 사용자 고유의 영구 저장소로 앱을 만듭니다.

   ```azurecli
   az spring-cloud app create \
       --resource-group <resource-group-name> \
       --service <Azure-Spring-Cloud-instance-name> \
       --name <app-name> \
       --persistent-storage <path-to-JSON-file>
   ```

   다음은 `--persistent-storage` create 명령의 매개 변수에 전달 되는 JSON 파일의 샘플입니다.

   ```json
   {
      "customPersistentDisks": [
         {
             "storageName": "<storage-resource-name>",
             "customPersistentDiskProperties": {
                 "type": "AzureFileVolume",
                 "shareName": "<Azure-file-share-name>",
                 "mountPath": "<unique-mount-path>",
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
                 "shareName": "<Azure-file-share-name>",
                 "mountPath": "<unique-mount-path>",
                 "readOnly": true
             }
         }
      ]
   }
   ```

1. 필요에 따라 다음 명령을 사용 하 여 기존 앱에 추가 영구 저장소를 추가 합니다.

   ```azurecli
   az spring-cloud app append-persistent-storage \
       --resource-group <resource-group-name> \
       --service <Azure-Spring-Cloud-instance-name> \
       --name <app-name> \
       --persistent-storage-type AzureFileVolume \
       --share-name <Azure-file-share-name> \
       --mount-path <unique-mount-path> \
       --storage-name <storage-resource-name>
   ```

1. 필요에 따라 다음 명령을 사용 하 여 특정 저장소 리소스의 기존 영구 저장소를 모두 나열 합니다.

   ```azurecli
   az spring-cloud storage list-persistent-storage \
       --resource-group <resource-group-name> \
       --service <Azure-Spring-Cloud-instance-name> \
       --name <storage-resource-name>
   ```

---

## <a name="use-best-practices"></a>모범 사례 사용

Azure 스프링 클라우드에 고유한 영구 저장소를 추가 하는 경우 다음 모범 사례를 따르십시오.

* 잠재적 대기 시간 문제를 방지 하려면 azure 스프링 클라우드 인스턴스와 Azure Storage 계정을 동일한 azure 지역에 두십시오.

* Azure Storage 계정에서 사용 중인 계정 키를 다시 생성 하지 않습니다. 스토리지 계정에는 두 개의 서로 다른 키가 포함되어 있습니다. 키를 다시 생성 하는 동안 응용 프로그램에서 영구 저장소를 사용할 수 있도록 하려면 단계별 방법을 사용 합니다.

   예를 들어, key1을 사용 하 여 저장소 계정을 Azure 스프링 클라우드에 바인딩하는 경우 다음 단계를 사용 합니다.

   1. key2를 다시 생성합니다.
   1. 다시 생성 된 key2를 사용 하도록 저장소 리소스의 계정 키를 업데이트 합니다.
   1. 이 저장소 리소스에서 영구 저장소를 탑재 하는 응용 프로그램을 다시 시작 합니다. 를 사용 하 여 `az spring-cloud storage list-persistent-storage` 모든 관련 응용 프로그램을 나열할 수 있습니다.
   1. key1을 다시 생성합니다.

* Azure Storage 계정 또는 Azure 파일 공유를 삭제 하는 경우 오류를 방지 하기 위해 응용 프로그램에서 해당 저장소 리소스 또는 영구 저장소를 제거 합니다.

## <a name="faqs"></a>FAQ

다음은 Azure 스프링 클라우드에서 고유한 영구 저장소를 사용 하는 방법에 대 한 FAQ (질문과 대답)입니다.

* 기본 제공 영구 저장소를 사용 하도록 설정한 다음 자체 저장소를 추가 영구 저장소로 사용 하는 경우 내 데이터를 내 Storage 계정으로 마이그레이션해야 하나요?

   *아니요. 하지만 곧 마이그레이션을 수행 하는 데 도움이 되는 문서를 제공할 예정입니다.*

* 예약 된 탑재 경로는 무엇입니까?

   *이러한 탑재 경로는 Azure 스프링 클라우드 서비스에서 예약 됩니다.*

   * */tmp*
   * */영구적*
   * */비밀*
   * */app-insights/agents*
   * */etc/azure-spring-cloud/certs*
   * */app-insights/agents/settings*
   * */app-lifecycle/settings*

* 사용 가능한 탑재 옵션은 무엇 인가요?

   *현재 다음과 같은 탑재 옵션이 지원 됩니다.*

   * `uid`
   * `gid`
   * `file_mode`
   * `dir_mode`

   *`mountOptions`속성은 선택 사항입니다. 위의 탑재 옵션에 대 한 기본값은 ["uid = 0", "gid = 0", "file_mode = 0777", "dir_mode = 0777"]입니다.*

## <a name="next-steps"></a>다음 단계

* [Logback을 사용 하 여 사용자 지정 영구 저장소에 로그를 기록 하는 방법](how-to-write-log-to-custom-persistent-storage.md)입니다.
* [Azure 스프링 클라우드에서 응용 프로그램의 크기를 조정](how-to-scale-manual.md)합니다.
