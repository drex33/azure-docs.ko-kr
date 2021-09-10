---
title: Azure HPC Cache 집계 네임스페이스 이해
description: Azure HPC Cache용 가상 네임스페이스를 계획하는 방법
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 09/30/2020
ms.author: v-erkel
ms.openlocfilehash: 500cc23f2091c38006f7e58e150fb246f2a9ca64
ms.sourcegitcommit: b5508e1b38758472cecdd876a2118aedf8089fec
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/09/2021
ms.locfileid: "113586908"
---
# <a name="plan-the-aggregated-namespace"></a>집계형 네임스페이스 계획

Azure HPC Cache를 사용하면 클라이언트가 백 엔드 스토리지 시스템의 세부 정보를 숨기는 가상 네임스페이스를 통해 다양한 스토리지 시스템에 액세스할 수 있습니다.

스토리지 대상을 추가한 후에는 스토리지 대상에 대해 하나 이상의 클라이언트 쪽 네임스페이스 경로를 설정합니다. 클라이언트 머신은 스토리지 시스템을 직접 탑재하는 대신 이 파일 경로를 탑재하고 캐시에 대해 파일 읽기 요청을 수행할 수 있습니다.

Azure HPC Cache에서 이 가상 파일 시스템을 관리하기 때문에 클라이언트 쪽 경로를 변경하지 않고도 스토리지 대상을 변경할 수 있습니다. 예를 들어 클라이언트 쪽 프로시저를 다시 작성하지 않고도 하드웨어 스토리지 시스템을 클라우드 스토리지로 바꿀 수 있습니다.

## <a name="aggregated-namespace-example"></a>집계 네임스페이스 예제

클라이언트 머신이 필요한 정보에 편리하게 연결할 수 있고 관리자와 워크플로 엔지니어가 경로를 쉽게 구분할 수 있도록 집계 네임스페이스를 계획합니다.

예를 들어 Azure HPC Cache 인스턴스를 사용하여 Azure Blob에 저장된 데이터를 처리하는 시스템을 가정합니다. 분석을 수행하려면 온-프레미스 데이터 센터에 저장된 템플릿 파일이 필요합니다.

템플릿 데이터는 데이터 센터에 저장되어 있고, 이 작업에 필요한 정보는 다음 하위 디렉터리에 저장되어 있습니다.

* */goldline/templates/acme2017/sku798*
* */goldline/templates/acme2017/sku980*

데이터 센터 스토리지 시스템은 다음 내보내기를 노출합니다.

* */*
* */goldline*
* */goldline/templates*

분석할 데이터는 [CLFSLoad 유틸리티](hpc-cache-ingest.md#pre-load-data-in-blob-storage-with-clfsload)를 사용하여 “sourcecollection”이라는 Azure Blob Storage 컨테이너에 복사되었습니다.

캐시를 통해 쉽게 액세스할 수 있게 하려면 다음 가상 네임스페이스 경로를 사용하여 스토리지 대상을 만드는 것이 좋습니다.

| 백 엔드 스토리지 시스템 <br/> (NFS 파일 경로 또는 Blob 컨테이너) | 가상 네임스페이스 경로 |
|-----------------------------------------|------------------------|
| /goldline/templates/acme2017/sku798     | /templates/sku798      |
| /goldline/templates/acme2017/sku980     | /templates/sku980      |
| sourcecollection                        | /source/               |

NFS 스토리지 대상은 각각 고유한 내보내기 경로를 참조하는 한 가상 네임스페이스 경로를 여러 개 포함할 수 있습니다. (NFS 스토리지 대상과 함께 여러 네임스페이스 경로를 사용하는 방법에 대해 자세히 알아보려면 [NFS 네임스페이스 경로](add-namespace-paths.md#nfs-namespace-paths)를 읽어보세요.)

NFS 원본 경로는 동일한 내보내기의 하위 디렉터리이므로 동일한 스토리지 대상에서 여러 네임스페이스 경로를 정의해야 합니다.

| 스토리지 대상 호스트 이름  | NFS 내보내기 경로     | 하위 디렉터리 경로 | 네임스페이스 경로    |
|--------------------------|---------------------|-------------------|-------------------|
| ‘IP 주소 또는 호스트 이름’ | /goldline/templates | acme2017/sku798   | /templates/sku798 |
| ‘IP 주소 또는 호스트 이름’ | /goldline/templates | acme2017/sku980   | /templates/sku980 |

클라이언트 애플리케이션은 캐시를 탑재하고 집계 네임스페이스 파일 경로 ``/source``, ``/templates/sku798``, ``/templates/sku980``에 쉽게 액세스할 수 있습니다.

대체 방법은 부모 디렉터리 `acme2017`에 연결되는 `/templates` 같은 가상 경로를 만든 다음, 클라이언트가 캐시를 탑재한 후 개별 `sku798` 및 `sku980` 디렉터리로 이동하게 하는 것입니다. 그러나 다른 네임스페이스 경로의 하위 디렉터리인 네임스페이스 경로는 만들 수 없습니다. 따라서 `acme2017` 디렉터리의 경로를 만드는 경우 해당 하위 디렉터리에 직접 액세스하는 네임스페이스 경로도 만들 수는 없습니다.

Azure HPC Cache **네임스페이스** 설정 페이지에는 클라이언트 쪽 파일 시스템이 표시되며, 여기서 경로를 추가하거나 편집할 수 있습니다. 자세한 내용은 [집계 네임스페이스 설정](add-namespace-paths.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계

가상 파일 시스템을 설정하는 방법을 결정한 후에는 다음 단계를 수행하여 시스템을 만듭니다.

* [스토리지 대상을 만들어](hpc-cache-add-storage.md) Azure HPC Cache에 백 엔드 스토리지 시스템 추가
* [네임스페이스 경로를 추가](add-namespace-paths.md)하여 클라이언트 머신이 파일에 액세스하는 데 사용하는 집계 네임스페이스 만들기
