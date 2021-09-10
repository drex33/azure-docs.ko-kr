---
title: JavaScript 애플리케이션에 대한 소스 맵 지원 - Azure Monitor Application Insights
description: Application Insights를 사용하여 사용자 고유의 스토리지 계정 Blob 컨테이너에 소스 맵을 업로드하는 방법을 알아봅니다.
ms.topic: conceptual
ms.date: 06/23/2020
ms.custom: devx-track-js
ms.openlocfilehash: 81ca8de41c4d68ddc5ff3fc0355def975e6df30d
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122535893"
---
# <a name="source-map-support-for-javascript-applications"></a>JavaScript 애플리케이션에 대한 소스 맵 지원

Application Insights는 사용자 고유의 스토리지 계정 Blob 컨테이너에 대한 소스 맵의 업로드를 지원합니다.
소스 맵은 엔드투엔드 트랜잭션 세부 정보 페이지에 있는 호출 스택을 축소하는 데 사용할 수 있습니다. [JAVASCRIPT SDK][ApplicationInsights-JS] 또는 [Node.js SDK][ApplicationInsights-Node.js]에서 전송하는 모든 예외는 소스 맵으로 축소할 수 있습니다.

![스토리지 계정과 연결하여 호출 스택 축소](./media/source-map-support/details-unminify.gif)

## <a name="create-a-new-storage-account-and-blob-container"></a>새 스토리지 계정 및 Blob 컨테이너 만들기

기존 스토리지 계정 또는 Blob 컨테이너가 이미 있는 경우 이 단계를 건너뛰어도 됩니다.

1. [새 스토리지 계정 만들기][create storage account]
2. 스토리지 계정에 [Blob 컨테이너를 만듭니다][create blob container]. 소스 맵에 퍼블릭으로 액세스할 수 없도록 하려면 "퍼블릭 액세스 수준"을 `Private`으로 설정해야 합니다.

> [!div class="mx-imgBorder"]
>![컨테이너 액세스 수준을 프라이빗으로 설정해야 합니다.](./media/source-map-support/container-access-level.png)

## <a name="push-your-source-maps-to-your-blob-container"></a>Blob 컨테이너에 소스 맵 푸시

구성된 Blob 컨테이너에 소스 맵을 자동으로 업로드하도록 구성하여 연속 배포 파이프라인을 스토리지 계정과 통합해야 합니다.

소스 맵은 컴파일 및 배포된 것과 동일한 폴더 구조를 사용하여 Blob Storage 컨테이너에 업로드할 수 있습니다. 일반적인 사용 사례는 배포 폴더의 접두사로 버전을 추가하는 것입니다(예: `1.2.3/static/js/main.js`). `sourcemaps`라는 Azure Blob 컨테이너를 축소하는 경우 `sourcemaps/1.2.3/static/js/main.js.map`에 있는 소스 맵을 가져오려고 합니다.

### <a name="upload-source-maps-via-azure-pipelines-recommended"></a>Azure Pipelines를 통해 소스 맵 업로드(권장)

Azure Pipelines를 사용하여 애플리케이션을 지속적으로 빌드 및 배포하는 경우 [Azure 파일 복사][azure file copy] 작업을 파이프라인에 추가하여 소스 맵을 자동으로 업로드합니다.

> [!div class="mx-imgBorder"]
> ![파이프라인에 Azure 파일 복사 작업을 추가하여 소스 맵을 Azure Blob Storage에 업로드](./media/source-map-support/azure-file-copy.png)

## <a name="configure-your-application-insights-resource-with-a-source-map-storage-account"></a>소스 맵 스토리지 계정을 사용하여 Application Insights 리소스 구성

### <a name="from-the-end-to-end-transaction-details-page"></a>엔드투엔드 트랜잭션 세부 정보 페이지에서

엔드투엔드 트랜잭션 세부 정보 탭에서 *축소* 를 클릭할 수 있습니다. 그러면 리소스가 구성되지 않은 경우 구성하라는 메시지가 표시됩니다.

1. Portal에서 축소되지 않은 예외에 대한 세부 정보를 확인합니다.
2. ‘축소 해제’를 선택합니다.
3. 리소스가 구성되지 않은 경우 구성하라는 메시지가 표시됩니다.

### <a name="from-the-properties-page"></a>속성 페이지에서

Application Insights 리소스에 연결된 스토리지 계정 또는 Blob 컨테이너를 구성하거나 변경하려는 경우 Application Insights 리소스의 *속성* 탭에서 해당 작업을 수행할 수 있습니다.

1. Application Insights 리소스의 *속성* 탭으로 이동합니다.
2. ‘소스 맵 Blob 컨테이너 변경’을 선택합니다.
3. 소스 맵 컨테이너와 다른 Blob 컨테이너를 선택합니다.
4. `Apply`를 선택합니다.

> [!div class="mx-imgBorder"]
> ![속성 블레이드로 이동하여 선택한 Azure Blob 컨테이너 다시 구성](./media/source-map-support/reconfigure.png)

## <a name="troubleshooting"></a>문제 해결

### <a name="required-azure-role-based-access-control-azure-rbac-settings-on-your-blob-container"></a>Blob 컨테이너에 대해 Azure RBAC(역할 기반 액세스 제어) 설정 필요

이 기능을 사용하는 포털의 모든 사용자는 적어도 Blob 컨테이너에 [Storage Blob 데이터 읽기 권한자][storage blob data reader]로 할당되어야 합니다. 이 기능을 통해 소스 맵을 사용하는 다른 모든 사용자에게 이 역할을 할당해야 합니다.

> [!NOTE]
> 컨테이너를 만든 방법에 따라 이 역할이 사용자 또는 팀에 자동으로 할당되지 않았을 수 있습니다.

### <a name="source-map-not-found"></a>소스 맵을 찾을 수 없음

1. 해당하는 소스 맵이 올바른 Blob 컨테이너에 업로드되었는지 확인합니다.
2. 소스 맵 파일 이름은 매핑되는 JavaScript 파일의 이름으로 지정되고 접미사 `.map`이 추가되어야 합니다.
    - 예를 들어 `/static/js/main.4e2ca5fa.chunk.js`는 `main.4e2ca5fa.chunk.js.map`이라는 Blob을 검색합니다.
3. 브라우저의 콘솔에서 오류가 기록되는지 확인합니다. 해당 오류를 지원 티켓에 포함합니다.

## <a name="next-steps"></a>다음 단계

* [Azure 파일 복사 작업](/azure/devops/pipelines/tasks/deploy/azure-file-copy)


<!-- Remote URLs -->
[create storage account]: ../../storage/common/storage-account-create.md?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json&tabs=azure-portal
[create blob container]: ../../storage/blobs/storage-quickstart-blobs-portal.md
[storage blob data reader]: ../../role-based-access-control/built-in-roles.md#storage-blob-data-reader
[ApplicationInsights-JS]: https://github.com/microsoft/applicationinsights-js
[ApplicationInsights-Node.js]: https://github.com/microsoft/applicationinsights-node.js
[azure file copy]: https://aka.ms/azurefilecopyreadme
