---
title: 렌더링 기능
description: 표준 Azure Batch 기능은 렌더링 워크로드 및 앱을 실행하는 데 사용됩니다. Batch에는 렌더링 워크로드를 지원하는 특정 기능이 포함됩니다.
author: mscurrell
ms.author: markscu
ms.date: 03/12/2021
ms.topic: how-to
ms.openlocfilehash: 57925df3babc22a6cfdff2f81d23bbbd60767cc6
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131461611"
---
# <a name="azure-batch-rendering-capabilities"></a>Azure Batch 렌더링 기능

표준 Azure Batch 기능은 렌더링 워크 로드 및 애플리케이션을 실행하는 데 사용됩니다. 또한 Batch에는 렌더링 워크 로드를 지원하는 특정 기능이 포함됩니다.

풀, 작업 및 태스크를 포함한 Batch 개념의 개요는 [이 문서](./batch-service-workflow-features.md)를 참조하세요.

## <a name="batch-pools-using-custom-vm-images-and-standard-application-licensing"></a>사용자 지정 VM 이미지 및 표준 애플리케이션 라이선스를 사용하는 Batch 풀

다른 워크로드 및 애플리케이션 유형과 마찬가지로 필요한 렌더링 애플리케이션 및 플러그 인을 통해 사용자 지정 VM 이미지를 만들 수 있습니다. 사용자 지정 VM 이미지는 [Azure Compute 갤러리에](../virtual-machines/shared-image-galleries.md) 배치되며 [Batch 풀을 만드는 데 사용할 수 있습니다.](batch-sig-images.md)

작업 명령줄 문자열은 사용자 지정 VM 이미지를 만들 때 사용하는 애플리케이션 및 경로를 참조해야 합니다.

대부분의 렌더링 애플리케이션에는 라이선스 서버에서 가져온 라이선스가 필요합니다. 기존 온-프레미스 라이선스 서버가 있으면 풀과 라이선스 서버가 모두 동일한 [가상 네트워크](../virtual-network/virtual-networks-overview.md)에 있어야 합니다. 또한 Batch 풀과 라이선스 서버 VM이 동일한 가상 네트워크에 있는 Azure VM에서 라이선스 서버를 실행할 수 있습니다.

## <a name="batch-pools-using-rendering-vm-images"></a>렌더링 VM 이미지를 사용하는 Batch 풀

> [!IMPORTANT]
> 렌더링 VM 이미지 및 사용 당 지불 라이선싱은 [더 이상 사용되지 않으며 2024년 2월 29일부터 사용 중지됩니다](https://azure.microsoft.com/updates/azure-batch-rendering-vm-images-licensing-will-be-retired-on-29-february-2024/). 렌더링에 Batch를 사용하려면 [사용자 지정 VM 이미지와 표준 애플리케이션 라이선싱을 사용해야 합니다.](batch-rendering-functionality.md#batch-pools-using-custom-vm-images-and-standard-application-licensing)

### <a name="rendering-application-installation"></a>애플리케이션 설치 렌더링

미리 설치된 애플리케이션만 사용해야 하는 경우 풀 구성에서 Azure Marketplace 렌더링 VM 이미지를 지정할 수 있습니다.

Windows 이미지 및 CentOS 이미지가 있습니다.  [Azure Marketplace](https://azuremarketplace.microsoft.com)에서 'batch rendering'(일괄 렌더링)를 검색하여 VM 이미지를 찾을 수 있습니다.

Azure Portal 및 Batch Explorer는 풀을 만들 때 렌더링 VM 이미지를 선택하기 위한 GUI 도구를 제공합니다.  Batch API를 사용하는 경우 풀을 만들 때 [ImageReference](/rest/api/batchservice/pool/add#imagereference)에 대한 다음 속성 값을 지정합니다.

| 게시자 | 제안 | SKU | 버전 |
|---------|---------|---------|--------|
| 일괄 처리 | rendering-centos73 | 렌더링 | 최신 |
| 일괄 처리 | rendering-windows2016 | 렌더링 | 최신 |

추가 애플리케이션이 풀 VM에서 필요한 경우 다른 옵션을 사용할 수 있습니다.

* Azure Compute 갤러리의 사용자 지정 이미지:
  * 이 옵션을 사용하여 정확한 애플리케이션 및 필요한 특정 버전으로 VM을 구성할 수 있습니다. 자세한 내용은 [Azure Compute 갤러리를 사용하여 풀 만들기를 참조하세요.](batch-sig-images.md) Autodesk 및 Chaos Group은 Azure Batch 라이선스 서비스에 대한 유효성을 검사하기 위해 Arnold 및 V-Ray를 각각 수정하였습니다. 이 지원이 포함된 이러한 애플리케이션 버전이 있는지 확인해야 합니다. 그렇지 않으면 사용량 과금 라이선스가 적용되지 않습니다. 현재 버전의 Maya 또는 3ds Max는 헤드리스를 실행하는 경우(배치/명령줄 모드로) 라이선스 서버가 필요하지 않습니다. 이 옵션을 진행하는 방법을 잘 모르는 경우 Azure 지원 담당자에게 문의하세요.
* [애플리케이션 패키지](./batch-application-packages.md).
  * 하나 이상의 ZIP 파일을 사용하여 애플리케이션 파일을 패키지 처리하고, Azure Portal을 통해 업로드하고, 풀 구성에서 패키지를 지정합니다. 풀 VM이 생성되면 ZIP 파일이 다운로드되고 파일이 추출됩니다.
* 리소스 파일.
  * Azure Blob Storage에 애플리케이션 파일이 업로드되면, [풀 시작 태스크](/rest/api/batchservice/pool/add#starttask)에서 파일 참조를 지정합니다. 풀 VM을 만들면 리소스 파일이 각 VM에 다운로드됩니다.

### <a name="pay-for-use-licensing-for-pre-installed-applications"></a>미리 설치된 애플리케이션에 대한 사용량 과금 라이선스

라이선스 요금이 있고 사용될 애플리케이션은 풀 구성에서 지정되어야 합니다.

* [풀을 만드는](/rest/api/batchservice/pool/add#request-body) 경우 `applicationLicenses` 속성을 지정합니다.  문자열 배열에서 "vray", "arnold", "3dsmax", "maya" 등의 값이 지정될 수 있습니다.
* 하나 이상의 애플리케이션을 지정하면 해당 애플리케이션의 비용은 VM 비용에 추가됩니다.  애플리케이션 가격은 [Azure Batch 가격 책정 페이지](https://azure.microsoft.com/pricing/details/batch/#graphic-rendering)에 나열되어 있습니다.

> [!NOTE]
> 대신 라이선스 서버에 연결하여 렌더링 애플리케이션을 사용하는 경우에는 `applicationLicenses` 속성을 지정하지 마세요.

애플리케이션을 선택하고 애플리케이션 가격을 표시하려면 Azure Portal 또는 Batch Explorer를 사용할 수 있습니다.

애플리케이션을 사용하려고 하는데 애플리케이션이 풀 구성의 `applicationLicenses` 속성에 지정되지 않았거나 라이선스 서버에 연결하지 않는 경우 애플리케이션 실행이 실패하며 라이선스 오류와 0이 아닌 종료 코드가 표시됩니다.

### <a name="environment-variables-for-pre-installed-applications"></a>미리 설치된 애플리케이션에 대한 환경 변수

렌더링 태스크에 대한 명령줄을 만들 수 있으려면, 렌더링 애플리케이션 실행 파일의 설치 위치를 지정해야 합니다.  실제 경로를 지정하지 않고 사용할 수 있는 시스템 환경 변수를 Azure Marketplace VM 이미지에서 만들었습니다.  [표준 Batch 환경 변수](./batch-compute-node-environment-variables.md) 외에 이러한 환경 변수가 각 태스크에 대해 만들어집니다.

|애플리케이션|애플리케이션 실행 파일|환경 변수|
|---------|---------|---------|
|Autodesk 3ds Max 2021|3dsmaxcmdio.exe|3DSMAX_2021_EXEC|
|Autodesk Maya 2020|render.exe|MAYA_2020_EXEC|
|Chaos Group V-Ray 독립 실행형|vray.exe|VRAY_4.10.03_EXEC|
|Arnold 2020 명령줄|kick.exe|ARNOLD_2020_EXEC|
|Blender|blender.exe|BLENDER_2018_EXEC|

## <a name="azure-vm-families"></a>Azure VM 제품군

다른 워크로드와 마찬가지로 렌더링 애플리케이션 시스템 요구 사항은 각기 다르며 성능 요구 사항은 작업 및 프로젝트에 따라 달라집니다.  다양한 VM 제품군은 가장 낮은 비용, 최상의 가격/성능, 최고의 성능 등의 요구 사항을 기준으로 Azure에서 사용할 수 있습니다.
Arnold 같은 일부 렌더링 애플리케이션은 CPU 기반이며, V-Ray 및 Blender Cycles 같은 기타 렌더링 애플리케이션은 CPU 및/또는 GPU를 사용할 수 있습니다.
사용 가능한 VM 제품군 및 VM 크기에 대한 설명은 [VM 유형 및 크기를 참조 하세요](../virtual-machines/sizes.md).

## <a name="low-priority-vms"></a>우선 순위가 낮은 VM

다른 워크 로드와 마찬가지로 우선 순위가 낮은 VM은 렌더링에 대한 Batch 풀에서 활용할 수 있습니다.  우선 순위가 낮은 VM은 기본 전용 VM과 동일하게 작동하지만 여분의 Azure 용량을 활용하며, 대폭 할인된 가격에 사용할 수 있습니다.  우선 순위가 낮은 VM은 할당이 가능하지 않거나, 사용 가능한 용량에 따라, 언제든지 선점될 수 있다는 단점이 있습니다. 이러한 이유로 우선 순위가 낮은 VM은 모든 렌더링 작업에 적합하지 않습니다. 예를 들어 이미지 렌더링에 많은 시간이 걸릴 경우 선점된 VM 때문에 해당 이미지의 렌더링을 중단했다가 다시 시작하는 것은 허용되지 않을 것 같습니다.

우선 순위가 낮은 VM의 특징 및 Batch를 사용하여 VM을 구성하는 다양한 방법에 대한 자세한 내용은 [Batch에서 낮은 우선 순위 VM 사용](./batch-low-pri-vms.md)을 참조하세요.

## <a name="jobs-and-tasks"></a>작업 및 태스크

렌더링 특정 지원은 작업 및 태스크에 필요하지 않습니다.  기본 구성 항목은 태스크 명령줄로서 필요한 애플리케이션을 참조해야 합니다.
Azure Marketplace VM 이미지를 사용하는 경우의 모범 사례는 환경 변수를 사용하여 경로 및 애플리케이션 실행 파일을 지정하는 것입니다.

## <a name="next-steps"></a>다음 단계

* [Batch를 이용해 렌더링 애플리케이션을 사용](batch-rendering-applications.md)하는 방법에 대해 알아봅니다.
* [자산 및 출력 파일을 렌더링하기 위한 스토리지 및 데이터 이동 옵션](batch-rendering-storage-data-movement.md)에 대해 알아봅니다.
