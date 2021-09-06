---
title: Azure HPC Cache 클라우드 컨테이너로 데이터 이동하기
description: Azure HPC Cache에서 사용할 Azure Blob Storage를 채우는 방법
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 06/30/2021
ms.author: v-erkel
ms.openlocfilehash: ccb052a9eacacdc18d954fedd940bcc6792fb361
ms.sourcegitcommit: b5508e1b38758472cecdd876a2118aedf8089fec
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/09/2021
ms.locfileid: "113586927"
---
# <a name="move-data-to-azure-blob-storage"></a>Azure Blob Storage로 데이터 이동하기

워크플로에 데이터를 Azure Blob Storage로 이동하는 작업이 포함된 경우 효율적인 전략을 사용하고 있는지 확인합니다. 스토리지 대상으로 정의하기 전에 새로운 Blob 컨테이너의 데이터를 미리 로드하거나, 컨테이너를 추가한 후, Azure HPC Cache를 사용하여 데이터를 복사할 수 있습니다.

이 문서에서는 Azure HPC Cache에서 사용할 수 있도록 Blob 스토리지로 데이터를 이동하는 가장 좋은 방법을 설명합니다.

> [!TIP]
>
> 이 문서는 NFS 탑재 Blob 스토리지(ADLS-NFS 스토리지 대상)에는 적용되지 않습니다. NFS 기반 메서드를 사용하여 ADLS-NFS Blob 컨테이너를 HPC Cache에 추가하기 전에 채울 수 있습니다. 자세한 내용은 [NFS 프로토콜을 사용하여 데이터 미리 로드](nfs-blob-considerations.md#pre-load-data-with-nfs-protocol)를 참조하세요.

다음 사항을 명심하십시오.

* Azure HPC Cache는 특수화된 스토리지 형식을 사용하여 Blob 스토리지에서 데이터를 구성합니다. 이로 인해 Blob 스토리지 대상은 새로운 빈 컨테이너이거나, 이전에 Azure HPC Cache 데이터에 사용된 Blob 컨테이너여야 합니다.

* 여러 클라이언트와 병렬 작업을 사용하는 경우 Azure HPC Cache를 사용하여 백 엔드 스토리지 대상으로 데이터를 복사하는 것이 더 효율적입니다. 클라이언트 하나의 단순 복사 명령이 데이터 이동을 느리게 합니다.

Python 기반 유틸리티는 Blob 스토리지 컨테이너에 콘텐츠를 로드하는 데 사용할 수 있습니다. 자세한 내용은 [Blob 스토리지에서 데이터 미리 로드](#pre-load-data-in-blob-storage-with-clfsload)를 참조하세요.

로드 유틸리티를 사용하지 않으려는 경우 또는 기존 스토리지 대상에 콘텐츠를 추가하려는 경우, [AZURE HPC Cache를 사용하는 데이터 복사](#copy-data-through-the-azure-hpc-cache)에서 병렬 데이터 수집 팁을 따르십시오.

## <a name="pre-load-data-in-blob-storage-with-clfsload"></a>CLFSLoad를 사용하여 Blob 스토리지에서 데이터 미리 로드하기

Avere CLFSLoad 유틸리티를 사용하여 데이터를 스토리지 대상으로 추가하기 전에 데이터를 새로운 Blob 스토리지 컨테이너로 복사할 수 있습니다. 이 유틸리티는 단일 Linux 시스템에서 실행되며, Azure HPC Cache에 필요한 제조업체의 포맷으로 데이터를 기록합니다. CLFSLoad는 캐시에 사용할 Blob 스토리지 컨테이너를 채우는 가장 효율적인 방법입니다.

Avere CLFSLoad 유틸리티는 Azure HPC Cache 팀의 요청에 의해 제공됩니다. 이를 위해 연락담당 팀원에게 문의하거나 [지원 티켓](hpc-cache-support-ticket.md)을 열어 도움을 요청하십시오.

이 옵션은 새로운 빈 컨테이너에만 적용됩니다. Avere CLFSLoad를 사용하기 전에 컨테이너를 만듭니다.

자세한 정보는 Azure HPC Cache 팀의 요청으로 사용할 수 있는 Avere CLFSLoad 배포에 포함되어 있습니다.

다음은 프로세스의 일반적인 개요입니다.

1. Python 버전 3.6 이상을 사용하는 Linux 시스템(VM 또는 물리적)을 준비합니다. 더 나은 성능을 위해서는 Python 3.7이 권장됩니다.
1. Linux 시스템에 Avere-CLFSLoad 소프트웨어를 설치합니다.
1. Linux 명령줄에서 전송을 실행합니다.

Avere CLFSLoad 유틸리티에는 다음 정보가 필요합니다.

* Blob 스토리지 컨테이너를 포함하는 스토리지 계정 ID
* 빈 Blob 스토리지 컨테이너 이름
* 유틸리티가 컨테이너에 쓸 수 있도록 하는 SAS(공유 액세스 서명) 토큰
* 데이터 원본에 대한 로컬 경로 - 복사할 데이터를 포함하는 로컬 디렉터리, 또는 데이터가 있는 탑재된 원격 시스템에 대한 로컬 경로

## <a name="copy-data-through-the-azure-hpc-cache"></a>Azure HPC Cache를 사용하는 데이터 복사

Avere CLFSLoad 유틸리티를 사용하지 않으려는 경우 또는 기존 Blob 스토리지 대상에 많은 양의 데이터를 추가하려는 경우, 캐시를 사용하여 복사할 수 있습니다. Azure HPC Cache는 여러 클라이언트를 동시에 지원하도록 설계되었으므로, 캐시를 사용하여 데이터를 복사하려면 여러 클라이언트에서 병렬 쓰기를 사용해야 합니다.

![다중 클라이언트, 다중 스레드 데이터 이동을 보여 주는 다이어그램 - 왼쪽 위의 온-프레미스 하드웨어 스토리지 아이콘에 여러 개의 화살표가 있습니다. 화살표는 네 개의 클라이언트 머신을 가리킵니다. 각 클라이언트 머신에서 나온 세 개의 화살표는 Azure HPC Cache를 가리킵니다. Azure HPC Cache에서 여러 개의 화살표는 Blob 스토리지를 가리킵니다.](media/hpc-cache-parallel-ingest.png)

데이터를 한 스토리지 시스템에서 다른 스토리지 시스템으로 전송하는 데 일반적으로 사용하는 ``cp`` 또는 ``copy`` 명령은 한 번에 하나의 파일만 복사할 수 있는 단일 스레드 프로세스입니다. 즉, 파일 서버에서 한 번에 하나의 파일만 수집한다는 것을 의미합니다. 이로 인해 캐시의 리소스가 낭비됩니다.

이 섹션에서는 Azure HPC Cache를 사용하여 Blob 스토리지로 데이터를 이동하기 위해 다중 클라이언트, 다중 스레드 파일 복사 시스템을 만들기 위한 전략을 설명합니다. 여러 클라이언트와 단순 복사 명령을 사용하여 효율적인 데이터 복사에 사용할 수 있는 파일 전송 개념 및 결정 사항에 대해 설명합니다.

또한 도움이 되는 몇 가지 유틸리티도 설명합니다. ``msrsync`` 유틸리티는 데이터 세트를 버킷으로 분할하고 rsync 명령을 사용하는 프로세스를 부분적으로 자동화하는 데 사용할 수 있습니다. ``parallelcp`` 스크립트는 원본 디렉터리를 읽고 복사 명령을 자동으로 실행하는 또 다른 유틸리티입니다.

### <a name="strategic-planning"></a>전략적 계획

데이터를 병렬로 복사하는 전략을 수립할 때는 파일 크기, 파일 수 및 디렉터리 깊이의 장단점을 이해해야 합니다.

* 파일이 작은 경우 관심 있는 메트릭은 초당 파일 수입니다.
* 파일이 큰 경우(10MiBi 이상) 관심 있는 메트릭은 초당 바이트 수입니다.

각 복사 프로세스에는 복사 명령의 길이를 타이밍으로 측정하고 파일 크기와 파일 수를 팩터링하여 측정할 수 있는 처리량 속도와 파일 전송 속도가 있습니다. 속도를 측정하는 방법은 이 문서의 범위를 벗어나므로 설명할 수 없지만 작거나 큰 파일을 처리할지 여부를 이해하는 것이 중요합니다.

Azure HPC Cache를 사용하는 병렬 데이터 수집 전략은 다음과 같습니다.

* 수동 복사 - 미리 정의된 파일 또는 경로 집합을 기준으로 백그라운드에서 동시에 둘 이상의 복사 명령을 실행하여 클라이언트에 다중 스레드 복사본을 수동으로 만들 수 있습니다. 자세한 내용은 [Azure HPC Cache 데이터 수집 - 수동 복사 방법](hpc-cache-ingest-manual.md)을 참조하십시오.

* ``msrsync`` - ``msrsync``를 사용하는 부분적인 자동 복사는 여러 병렬``rsync`` 프로세스를 실행 하는 래퍼 유틸리티입니다. 자세한 내용은 [Azure HPC Cache 데이터 수집 - msrsync 메서드](hpc-cache-ingest-msrsync.md)를 참조하십시오.

* ``parallelcp``를 사용하는 스크립트 복사 - [Azure HPC Cache 데이터 수집 - 병렬 복사 스크립트 메서드](hpc-cache-ingest-parallelcp.md)에서 병렬 복사 스크립트를 만들고 실행하는 방법을 알아봅니다.

## <a name="next-steps"></a>다음 단계

스토리지를 설정한 후 클라이언트에서 캐시를 탑재하는 방법을 알아봅니다.

* [Azure HPC Cache 시스템에 액세스하기](hpc-cache-mount.md)
