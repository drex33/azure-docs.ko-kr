---
title: Azure Functions의 스토리지 고려 사항
description: Azure Functions의 스토리지 요구 사항 및 저장된 데이터 암호화에 관해 알아봅니다.
ms.topic: conceptual
ms.date: 11/09/2021
ms.openlocfilehash: 0e53d2919d8af3f0e8162d4aca9f55f2ec0ab740
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132335882"
---
# <a name="storage-considerations-for-azure-functions"></a>Azure Functions의 스토리지 고려 사항

함수 앱 인스턴스를 만들 때 Azure Functions에는 Azure Storage 계정이 필요합니다. 함수 앱에서 사용할 수 있는 스토리지 서비스는 다음과 같습니다.

|스토리지 서비스  | Functions 사용  |
|---------|---------|
| [Azure Blob Storage](../storage/blobs/storage-blobs-introduction.md)     | 바인딩 상태 및 함수 키를 유지 관리합니다.  <br/>[Durable Functions의 작업 허브](durable/durable-functions-task-hubs.md)에서도 사용됩니다. |
| [Azure 파일](../storage/files/storage-files-introduction.md)  | [사용 플랜](consumption-plan.md) 및 [프리미엄 플랜](functions-premium-plan.md)에서 함수 앱 코드를 저장 및 실행하는 데 사용되는 파일 공유입니다. <br/>Azure Files는 기본적으로 설정되지만 특정 조건에서 [Azure Files 없이 앱을 만들 수 있습니다](#create-an-app-without-azure-files). |
| [Azure Queue Storage](../storage/queues/storage-queues-introduction.md)     | [Durable Functions의 작업 허브](durable/durable-functions-task-hubs.md)에서 사용됩니다.   |
| [Azure Table Storage](../storage/tables/table-storage-overview.md)  |  [Durable Functions의 작업 허브](durable/durable-functions-task-hubs.md)에서 사용됩니다.       |

> [!IMPORTANT]
> 사용/프리미엄 호스팅 계획을 사용하는 경우 함수 코드 및 바인딩 구성 파일은 기본 스토리지 계정의 Azure Files에 저장됩니다. 기본 스토리지 계정을 삭제하면 이 콘텐츠는 삭제되고 복구할 수 없습니다.

## <a name="storage-account-requirements"></a>Storage 계정 요구 사항

함수 앱을 만들 때 Blob, Queue 및 Table 스토리지를 지원하는 범용 Azure Storage 계정을 만들거나 해당 계정에 연결해야 합니다. 이는 Functions가 트리거 관리 및 함수 실행 기록 등의 작업에 Azure Storage를 사용하기 때문입니다. 일부 스토리지 계정은 큐 및 테이블을 지원하지 않습니다. 해당 계정에는 Blob 전용 스토리지 계정 및 Azure Premium Storage가 포함됩니다.

스토리지 계정 유형에 대해 자세히 알아보려면 [Azure Storage 서비스 소개](../storage/common/storage-introduction.md#core-storage-services)를 참조하세요. 

함수 앱에서 기존 스토리지 계정을 사용할 수 있지만 관련 요구 사항을 충족하는지 확인해야 합니다. Azure Portal의 함수 앱 만들기 흐름의 일부로 생성된 스토리지 계정은 관련 스토리지 계정 요구 사항을 충족합니다. Portal에서 함수 앱을 만드는 동안 기존 스토리지 계정을 선택하면 지원되지 않는 계정이 필터링됩니다. 이 흐름에서는 만들려는 함수 앱과 동일한 지역에 있는 기존 스토리지 계정만 선택할 수 있습니다. 자세한 내용은 [스토리지 계정 위치](#storage-account-location)를 참조하세요.

<!-- JH: Does using a Premium Storage account improve perf? -->

## <a name="storage-account-guidance"></a>스토리지 계정 지침

모든 함수 앱은 스토리지 계정이 있어야 작동합니다. 해당 계정이 삭제되면 함수 앱이 실행되지 않습니다. 스토리지 관련 문제를 해결하려면 [스토리지 관련 문제를 해결하는 방법](functions-recover-storage-account.md)을 참조하세요. 함수 앱에서 사용하는 스토리지 계정에는 다음 추가 고려 사항이 적용됩니다.

### <a name="storage-account-location"></a>스토리지 계정 위치

최상의 성능을 위해 함수 앱은 대기 시간이 감소하도록 동일한 지역에서 스토리지 계정을 사용해야 합니다. Azure Portal은 이 모범 사례를 적용합니다. 어떤 이유로든 함수 앱과 다른 지역에서 스토리지 계정을 사용해야 하는 경우 Portal 외부에서 함수 앱을 만들어야 합니다. 

### <a name="storage-account-connection-setting"></a>스토리지 계정 연결 설정

스토리지 계정 연결은 [AzureWebJobsStorage 애플리케이션 설정](./functions-app-settings.md#azurewebjobsstorage)에서 유지 관리됩니다. 

스토리지 키를 다시 생성하는 경우 스토리지 계정 연결 문자열을 업데이트해야 합니다. [여기에서 스토리지 키 관리에 관해 자세히 알아보세요.](../storage/common/storage-account-create.md)

### <a name="shared-storage-accounts"></a>공유 스토리지 계정

여러 함수 앱에서 문제 없이 동일한 스토리지 계정을 공유할 수 있습니다. 예를 들어 Visual Studio에서 Azure Storage 에뮬레이터를 사용하여 여러 앱을 개발할 수 있습니다. 이 경우 에뮬레이터는 단일 스토리지 계정처럼 작동합니다. 함수 앱에서 사용하는 동일한 스토리지 계정을 사용하여 애플리케이션 데이터를 저장할 수도 있습니다. 그러나 프로덕션 환경에서는 이 접근 방법이 항상 좋은 것은 아닙니다.

### <a name="lifecycle-management-policy-considerations"></a>수명 주기 관리 정책 고려 사항

함수는 Blob 저장소를 사용 하 여 [기능 액세스 키](functions-bindings-http-webhook-trigger.md#authorization-keys)와 같은 중요 한 정보를 유지 합니다. Blob Storage 계정에 [수명 주기 관리 정책을](../storage/blobs/lifecycle-management-overview.md) 적용 하는 경우이 정책은 기능 호스트에 필요한 blob를 제거할 수 있습니다. 따라서 함수에서 사용 하는 저장소 계정에 이러한 정책을 적용 하면 안 됩니다. 이러한 정책을 적용 해야 하는 경우 일반적으로 또는로 시작 하는 함수에 사용 되는 컨테이너를 제외 해야 `azure-webjobs` 합니다 `scm` .

### <a name="optimize-storage-performance"></a>스토리지 성능 최적화

[!INCLUDE [functions-shared-storage](../../includes/functions-shared-storage.md)]

## <a name="storage-data-encryption"></a>스토리지 데이터 암호화

[!INCLUDE [functions-storage-encryption](../../includes/functions-storage-encryption.md)]

### <a name="in-region-data-residency"></a>지역 내 데이터 보존

모든 고객 데이터가 단일 지역 내에 유지되어야 하는 경우 함수 앱과 연결된 스토리지 계정은 [지역 중복](../storage/common/storage-redundancy.md)을 사용하는 것이어야 합니다. 또한 지역 중복 스토리지 계정은 [Azure Durable Functions](./durable/durable-functions-perf-and-scale.md#storage-account-selection)에서 사용해야 합니다.

다른 플랫폼 관리형 고객 데이터는 내부적으로 부하가 분산된 ASE(App Service Environment)에서 호스팅할 때만 지역에 저장됩니다. 자세한 내용은 [ASE 지역 중복성](../app-service/environment/zone-redundancy.md#in-region-data-residency)을 참조하세요.

## <a name="create-an-app-without-azure-files"></a>Azure Files 없이 앱 만들기

Azure Files는 확장성이 높은 시나리오에서 공유 파일 시스템으로 사용할 수 있도록 프리미엄 및 Linux가 아닌 소비 계획에 대해 기본적으로 설정되어 있습니다. 파일 시스템은 로그 스트리밍 등의 일부 기능을 위해 플랫폼에서 사용되지만 주로 배포된 함수 페이로드의 일관성을 보장합니다. 앱이 [외부 패키지 URL을 사용하여 배포](./run-functions-from-deployment-package.md)되면 앱 콘텐츠가 별도의 읽기 전용 파일 시스템에서 제공되므로 원하는 경우 Azure Files를 생략할 수 있습니다. 이러한 경우 쓰기 가능 파일 시스템이 제공되지만 모든 함수 앱 인스턴스와 공유되지 않을 수도 있습니다.

Azure Files를 사용 하지 않는 경우 다음을 고려해 야 합니다.

* 외부 패키지 URL에서 배포 해야 합니다.
* 앱은 공유 쓰기 파일 시스템을 사용할 수 없습니다.
* 앱은 런타임 v1 함수를 사용할 수 없습니다.
* Azure Portal과 같은 클라이언트의 로그 스트리밍 환경은 기본적으로 파일 시스템 로그입니다. Application Insights 로그를 대신 사용해야 합니다.

위의 내용이 적절하게 고려되는 경우 Azure Files 없이 앱을 만들 수 있습니다. `WEBSITE_CONTENTAZUREFILECONNECTIONSTRING` 및 `WEBSITE_CONTENTSHARE` 애플리케이션 설정을 지정하지 않고 함수 앱을 만듭니다. 표준 배포에 대 한 ARM 템플릿을 생성 하 고이 두 설정을 제거한 다음 템플릿을 배포 하 여이 작업을 수행할 수 있습니다. 

함수는 동적 스케일 아웃 프로세스 중에 Azure Files를 사용 하기 때문에 사용 및 Premium 계획에 Azure Files 하지 않고 실행할 때 크기 조정을 제한할 수 있습니다.

## <a name="mount-file-shares"></a>파일 공유 탑재

이 기능은 현재 Linux에서 실행하는 경우에만 사용할 수 있습니다. 

기존 Azure Files 공유를 Linux 함수 앱에 탑재할 수 있습니다. Linux 함수 앱에 공유를 탑재하면 기존 기계 학습 모델 또는 함수의 기타 데이터를 활용할 수 있습니다. [`az webapp config storage-account add`](/cli/azure/webapp/config/storage-account#az_webapp_config_storage_account_add) 명령을 사용하여 Linux 함수 앱에 기존 공유를 탑재할 수 있습니다. 

이 명령에서 `share-name`은 기존 Azure Files 공유의 이름이며 `custom-id`는 함수 앱에 탑재될 때 공유를 고유하게 정의하는 문자열일 수 있습니다. 또한 `mount-path`는 함수 앱에서 공유에 액세스하는 경로입니다. `mount-path`는 `/dir-name` 형식이어야 하며 `/home`으로 시작할 수 없습니다.

전체 예제는 [Python 함수 앱 만들기 및 Azure Files 공유 탑재](scripts/functions-cli-mount-files-storage-linux.md)의 스크립트를 참조하세요. 

현재는 `AzureFiles`의 `storage-type`만 지원됩니다. 지정된 함수 앱에는 5개의 공유만 탑재할 수 있습니다. 파일 공유를 탑재하면 콜드 부팅 시간이 최소한 200~300ms 증가하며 스토리지 계정이 다른 지역에 있는 경우 훨씬 더 증가할 수 있습니다.

탑재된 공유는 지정된 `mount-path`에서 함수 코드에 사용할 수 있습니다. 예를 들어 `mount-path`가 `/path/to/mount`인 경우 다음 Python 예제와 같이 파일 시스템 API를 통해 대상 디렉터리에 액세스할 수 있습니다.

```python
import os
...

files_in_share = os.listdir("/path/to/mount")
```

## <a name="next-steps"></a>다음 단계

Azure Functions 호스팅 옵션에 관해 자세히 알아보세요.

> [!div class="nextstepaction"]
> [Azure Functions 비율 크기 조정 및 호스팅](functions-scale.md)
