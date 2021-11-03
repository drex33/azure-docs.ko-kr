---
title: Azure HPC Cache 관리 및 업데이트
description: Azure Portal 또는 Azure CLI를 사용하여 Azure HPC Cache를 관리하고 업데이트하는 방법
author: femila
ms.service: hpc-cache
ms.topic: how-to
ms.date: 07/08/2021
ms.author: femila
ms.openlocfilehash: 46d891172c3290b4ce21723561f0689bedd7b22d
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131087802"
---
# <a name="manage-your-cache"></a>캐시 관리

Azure Portal의 캐시 개요 페이지에는 캐시에 대한 프로젝트 세부 정보, 캐시 상태, 기본 통계가 표시됩니다. 캐시를 중지 또는 시작하고, 캐시를 삭제하고, 장기 스토리지로 데이터를 플러시하고, 소프트웨어를 업데이트하는 컨트롤도 있습니다.

이 문서에서는 Azure CLI를 사용하여 기본 작업을 수행하는 방법도 설명합니다.

개요 페이지를 열려면 Azure Portal에서 캐시 리소스를 선택합니다. 예를 들어 **모든 리소스** 페이지를 로드하고 캐시 이름을 클릭합니다.

![Azure HPC Cache 인스턴스의 개요 페이지 스크린샷](media/hpc-cache-overview.png)

페이지 맨 위에 있는 단추를 사용하면 캐시를 관리할 수 있습니다.

* **시작** 및 [**중지**](#stop-the-cache) - 캐시 작업을 다시 시작하거나 일시 중단합니다.
* [**플러시**](#flush-cached-data) - 변경된 데이터를 스토리지 대상에 씁니다.
* [**업그레이드**](#upgrade-cache-software) - 캐시 소프트웨어를 업데이트합니다.
* [**진단 수집**](#collect-diagnostics) - 디버깅 정보를 업로드합니다.
* **새로 고침** - 개요 페이지를 다시 로드합니다.
* [**삭제**](#delete-the-cache) - 캐시를 영구적으로 삭제합니다.

옵션에 대한 자세한 내용은 아래를 참조하세요.

> [!TIP]
> 개별 저장소 대상을 관리할 수도 있습니다. 자세한 내용은 [보기 및 저장소 대상 관리](manage-storage-targets.md) 를 참조 하세요.

캐시 관리 작업을 보여 주는 [동영상](https://azure.microsoft.com/resources/videos/managing-hpc-cache/)을 시청하려면 아래 이미지를 클릭하세요.

[![동영상 썸네일: Azure HPC Cache: 관리(클릭하여 동영상 페이지 방문)](media/video-5-manage.png)](https://azure.microsoft.com/resources/videos/managing-hpc-cache/)

## <a name="stop-the-cache"></a>캐시 중지

비활성 기간 동안 캐시를 중지하여 비용을 절감할 수 있습니다. 캐시를 중지하는 동안에는 작동 시간에 대한 요금이 부과되지 않지만 캐시의 할당된 디스크 스토리지에 대한 요금은 청구됩니다. (자세한 내용은 [가격 책정](https://aka.ms/hpc-cache-pricing) 페이지를 참조하세요.)

중지된 캐시는 클라이언트 요청에 응답하지 않습니다. 캐시를 중지하기 전에 클라이언트를 분리해야 합니다.

### <a name="portal"></a>[포털](#tab/azure-portal)

**중지** 단추를 클릭하면 활성 캐시가 일시 중단됩니다. **중지** 단추는 캐시의 상태가 **정상** 이거나 **저하** 된 경우에 사용할 수 있습니다.

![상단의 중지 단추가 강조 표시되어 있으며, 예(기본값) 및 아니요 단추를 포함하여 중지 작업을 설명하고 '계속하시겠습니까?'라고 묻는 팝업 메시지의 스크린샷](media/stop-cache.png)

예를 클릭하여 캐시 중지를 확인한 후 캐시에서 해당 콘텐츠를 스토리지 대상에 자동으로 플러시합니다. 이 프로세스는 다소 시간이 걸릴 수 있지만 데이터 일관성을 보장합니다. 마지막으로 캐시 상태가 **중지됨** 으로 변경됩니다.

중지된 캐시를 다시 활성화하려면 **시작** 단추를 클릭합니다. 확인이 필요하지 않습니다.

![상단의 시작 단추가 강조 표시되어 있는 스크린샷](media/start-cache.png)

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[Azure HPC Cache용 Azure CLI 설정](./az-cli-prerequisites.md).

[Az hpc cache stop](/cli/azure/hpc-cache#az_hpc_cache_stop) 명령을 사용하여 캐시를 임시로 일시 중단합니다. 이 작업은 캐시 상태가 **정상** 또는 **저하** 인 경우에만 유효합니다.

캐시는 중지하기 전에 해당 콘텐츠를 스토리지 대상에 자동으로 플러시합니다. 이 프로세스는 다소 시간이 걸릴 수 있지만 데이터 일관성을 보장합니다.

작업이 완료되면 캐시 상태가 **중지됨** 으로 변경됩니다.

[az hpc-cache start](/cli/azure/hpc-cache#az_hpc_cache_start)를 사용하여 중지된 캐시를 다시 활성화합니다.

시작 또는 중지 명령을 발행하면 작업이 완료될 때까지 명령줄에 “실행 중” 상태 메시지가 표시됩니다.

```azurecli
$ az hpc-cache start --name doc-cache0629
 - Running ..
```

완료되면 메시지가 “완료됨”으로 업데이트되고 반환 코드 및 기타 정보가 표시됩니다.

```azurecli
$ az hpc-cache start --name doc-cache0629
{- Finished ..
  "endTime": "2020-07-01T18:46:43.6862478+00:00",
  "name": "c48d320f-f5f5-40ab-8b25-0ac065984f62",
  "properties": {
    "output": "success"
  },
  "startTime": "2020-07-01T18:40:28.5468983+00:00",
  "status": "Succeeded"
}
```

---

## <a name="flush-cached-data"></a>캐시된 데이터 플러시

개요 페이지의 **플러시** 단추는 캐시에 저장되어 있는 모든 변경된 데이터를 백 엔드 스토리지 대상에 즉시 쓰도록 캐시에 지시합니다. 캐시는 데이터를 스토리지 대상에 정기적으로 저장하므로 백 엔드 스토리지 시스템이 최신 상태인지 확인하려는 경우가 아니라면 이 작업을 수동으로 수행할 필요가 없습니다. 예를 들어 스토리지 스냅샷을 만들거나 데이터 세트 크기를 확인하기 전에 **플러시** 를 사용할 수 있습니다.

> [!NOTE]
> 플러시 프로세스 중에 캐시는 클라이언트 요청을 처리할 수 없습니다. 작업이 완료된 후에는 캐시 액세스가 일시 중단되었다가 다시 시작됩니다.

캐시 플러시 작업을 시작하면 캐시에서 클라이언트 요청 수락이 중지되고 개요 페이지의 캐시 상태가 **플러시 중** 으로 변경됩니다.

캐시의 데이터는 적절한 스토리지 대상에 저장됩니다. 플러시되어야 하는 데이터 양에 따라 프로세스가 몇 분 또는 한 시간 이상 걸릴 수 있습니다.

모든 데이터가 스토리지 대상에 저장되고 나면 캐시가 자동으로 클라이언트 요청을 다시 가져오기 시작합니다. 캐시 상태가 **정상** 으로 돌아갑니다.

### <a name="portal"></a>[포털](#tab/azure-portal)

캐시를 플러시하려면 **플러시** 단추를 클릭한 다음 **예** 를 클릭하여 작업을 확인합니다.

![상단의 플러시 단추가 강조 표시되어 있으며, 예(기본값) 및 아니요 단추를 포함하여 플러시 작업을 설명하고 '계속하시겠습니까?'라고 묻는 팝업 메시지의 스크린샷](media/hpc-cache-flush.png)

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[Azure HPC Cache용 Azure CLI 설정](./az-cli-prerequisites.md).

[az hpc-cache flush](/cli/azure/hpc-cache#az_hpc_cache_flush)를 사용하여 캐시에서 변경된 모든 데이터를 스토리지 대상에 쓰도록 강제 실행합니다.

예제:

```azurecli
$ az hpc-cache flush --name doc-cache0629 --resource-group doc-rg
 - Running ..
```

플러시가 완료되면 성공 메시지가 반환됩니다.

```azurecli
{- Finished ..
  "endTime": "2020-07-09T17:26:13.9371983+00:00",
  "name": "c22f8e12-fcf0-49e5-b897-6a6e579b6489",
  "properties": {
    "output": "success"
  },
  "startTime": "2020-07-09T17:25:21.4278297+00:00",
  "status": "Succeeded"
}
$
```

---

## <a name="upgrade-cache-software"></a>캐시 소프트웨어 업그레이드

새 소프트웨어 버전이 제공될 경우 **업그레이드** 단추가 활성화됩니다. 또한 페이지 맨 위에 소프트웨어 업데이트에 대한 메시지가 표시됩니다.

![업그레이드 단추가 활성화된 상단 단추 행의 스크린샷](media/hpc-cache-upgrade-button.png)

클라이언트 액세스는 소프트웨어를 업그레이드하는 동안 중단되지 않지만 캐시 성능이 느려집니다. 사용량이 많지 않은 사용 시간 또는 계획된 유지 관리 기간에 소프트웨어를 업그레이드하도록 계획합니다.

소프트웨어 업데이트에는 몇 시간이 걸릴 수 있습니다. 처리량이 높게 구성된 캐시는 최대 처리량 값이 낮은 캐시보다 업그레이드하는 데 더 많은 시간이 걸립니다.

소프트웨어 업그레이드가 제공되면 일주일이 기간이 주어지며 수동으로 적용해야 합니다. 종료 날짜는 업그레이드 메시지에 표시됩니다. 이 기간에 업그레이드하지 않으면 Azure에서 자동으로 캐시에 업데이트를 적용합니다. 자동 업그레이드 타이밍은 구성할 수 없습니다. 캐시 성능 영향에 대해 우려하는 경우 기간이 만료되기 전에 소프트웨어를 직접 업그레이드해야 합니다.

종료일이 지나고 캐시가 중지되면 다음에 시작될 때 캐시가 자동으로 소프트웨어를 업그레이드합니다. (업데이트는 즉시 시작되지는 않을 수도 있지만 한 시간 이내에 시작됩니다.)

### <a name="portal"></a>[포털](#tab/azure-portal)

**업그레이드** 단추를 클릭하여 소프트웨어 업데이트를 시작합니다. 작업이 완료될 때까지 캐시 상태가 **업그레이드 중** 으로 변경됩니다.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[Azure HPC Cache용 Azure CLI 설정](./az-cli-prerequisites.md).

Azure CLI에서 새 소프트웨어 정보는 캐시 상태 보고서의 끝에 포함됩니다. ([az hpc-cache show](/cli/azure/hpc-cache#az_hpc_cache_show)를 사용하여 확인합니다.) 메시지에서 “upgradeStatus” 문자열을 찾습니다.

[az hpc-cache upgrade-firmware](/cli/azure/hpc-cache#az_hpc_cache_upgrade-firmware)를 사용하여 업데이트가 있는 경우 적용합니다.

업데이트를 사용할 수 없는 경우에는 이 작업이 적용되지 않습니다.

이 예제는 캐시 상태(업데이트를 사용할 수 없음)와 upgrade-firmware 명령의 결과를 보여 줍니다.

```azurecli
$ az hpc-cache show --name doc-cache0629
{
  "cacheSizeGb": 3072,
  "health": {
    "state": "Healthy",
    "statusDescription": "The cache is in Running state"
  },

<...>

  "tags": null,
  "type": "Microsoft.StorageCache/caches",
  "upgradeStatus": {
    "currentFirmwareVersion": "5.3.61",
    "firmwareUpdateDeadline": "0001-01-01T00:00:00+00:00",
    "firmwareUpdateStatus": "unavailable",
    "lastFirmwareUpdate": "2020-06-29T22:18:32.004822+00:00",
    "pendingFirmwareVersion": null
  }
}
$ az hpc-cache upgrade-firmware --name doc-cache0629
$
```

---

## <a name="collect-diagnostics"></a>진단 수집

**진단 수집** 단추는 시스템 정보를 수집하고 Microsoft 서비스에 업로드하는 프로세스를 수동으로 시작하고 문제 해결을 지원합니다. 캐시는 심각한 캐시 문제가 발생하는 경우 동일한 진단 정보를 자동으로 수집하고 업로드합니다.

Microsoft 서비스 및 지원에서 요청하는 경우 이 컨트롤을 사용합니다.

단추를 클릭한 후 **예** 를 클릭하여 업로드를 확인합니다.

!['진단 컬렉션 시작' 팝업 확인 메시지의 스크린샷. 기본 단추 '예'가 강조 표시됩니다.](media/diagnostics-confirm.png)

## <a name="delete-the-cache"></a>캐시 삭제

**삭제** 단추를 클릭하면 캐시가 삭제됩니다. 캐시를 삭제하면 모든 리소스가 삭제되고 더 이상 계정 요금이 발생하지 않습니다.

스토리지 대상으로 사용되는 백 엔드 스토리지 볼륨은 캐시를 삭제할 때 영향을 받지 않습니다. 나중에 향후 캐시에 추가하거나 개별적으로 서비스를 해제할 수 있습니다.

> [!NOTE]
> Azure HPC Cache는 캐시를 삭제하기 전에 캐시에서 백 엔드 스토리지 시스템으로 변경된 데이터를 자동으로 쓰지 않습니다.
>
> 캐시의 모든 데이터가 장기 스토리지에 기록되었는지 확인하려면 삭제하기 전에 [캐시를 중지](#stop-the-cache)합니다. 삭제하기 전에 상태가 **중지됨** 으로 표시되는지 확인합니다.

### <a name="portal"></a>[포털](#tab/azure-portal)

캐시를 중지한 후 **삭제** 단추를 클릭하여 캐시를 영구적으로 제거합니다.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[Azure HPC Cache용 Azure CLI 설정](./az-cli-prerequisites.md).

Azure CLI 명령 [az hpc-cache delete](/cli/azure/hpc-cache#az_hpc_cache_delete)를 사용하여 캐시를 영구적으로 제거합니다.

예:
```azurecli
$ az hpc-cache delete --name doc-cache0629
 - Running ..

<...>

{- Finished ..
  "endTime": "2020-07-09T22:24:35.1605019+00:00",
  "name": "7d3cd0ba-11b3-4180-8298-d9cafc9f22c1",
  "startTime": "2020-07-09T22:13:32.0732892+00:00",
  "status": "Succeeded"
}
$
```

---

## <a name="view-warnings"></a>경고 보기

캐시가 비정상 상태가 되면 **경고** 페이지를 확인합니다. 이 페이지는 해당 상태를 이해하는 데 도움이 될 수 있는 캐시 소프트웨어의 알림을 보여 줍니다.

알림은 Azure Portal에 의해 제어되지 않으므로 활동 로그에 표시되지 않습니다. 일반적으로 사용자가 만든 사용자 지정 설정과 관련되어 있습니다.

여기에 표시될 수 있는 경고의 종류는 다음과 같습니다.

* 캐시가 NTP 서버에 연결할 수 없음
* 캐시가 확장 그룹 사용자 이름 정보를 다운로드하지 못함
* 스토리지 대상에서 사용자 지정 DNS 설정이 변경됨

![확장 그룹 사용자 이름을 다운로드할 수 없다는 메시지를 표시하는 모니터링 > 경고 페이지의 스크린샷](media/warnings-page.png)

## <a name="next-steps"></a>다음 단계

* [통계를 사용하여 캐시 모니터링](metrics.md)
* [Azure HPC Cache 관련 지원](hpc-cache-support-ticket.md) 받기
