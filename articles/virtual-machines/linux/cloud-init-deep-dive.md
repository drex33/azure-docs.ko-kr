---
title: cloud-init 이해
description: cloud-init을 사용하여 Azure VM을 프로비전하는 방법을 자세히 알아봅니다.
author: danielsollondon
ms.service: virtual-machines
ms.topic: conceptual
ms.date: 07/06/2020
ms.author: danis
ms.reviewer: cynthn
ms.subservice: cloud-init
ms.openlocfilehash: 4ca42c259e10742e61fbd7f36b73f332cba5a8db
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/22/2021
ms.locfileid: "114445833"
---
# <a name="diving-deeper-into-cloud-init"></a>cloud-init에 대해 자세히 알아보기
[cloud-init](https://cloudinit.readthedocs.io/en/latest/index.html)에 대해 자세히 알아보거나 심층적으로 문제를 해결하려면 작동 방식을 이해해야 합니다. 이 문서에서는 중요한 부분을 강조하고 Azure 사양에 대해 설명합니다.

cloud-init이 범용 이미지에 포함되고 해당 이미지에서 VM이 생성되면 초기 부팅 중에 구성이 처리되고 5단계를 거쳐 실행됩니다. 이러한 단계는 cloud-init이 구성을 적용할 시점을 보여주기 때문에 중요합니다. 


## <a name="understand-cloud-init-configuration"></a>Cloud-Init 구성 이해
플랫폼에서 실행되도록 VM을 구성한다는 것은 cloud-init이 여러 구성을 적용해야 함을 의미합니다. 이미지 소비자가 상호 작용할 기본 구성은 여러 형식을 지원하는 `User data`(customData)입니다. 자세한 내용은 [사용자 데이터 형식 및 cloud-init 21.2 문서](https://cloudinit.readthedocs.io/en/latest/topics/format.html#user-data-formats). 추가 구성을 위해 스크립트(/var/lib/cloud/scripts)를 추가하고 실행할 수도 있습니다. 이에 대한 자세한 내용은 아래에서 설명합니다.

일부 구성은 cloud-init과 함께 제공되는 Azure Marketplace 이미지로 이미 준비되어 있습니다. 예를 들면 다음과 같습니다.

1. **클라우드 데이터 원본** - cloud-init에는 클라우드 플랫폼과 상호 작용할 수 있는 코드가 포함되어 있으며 이를 '데이터 원본'이라고 합니다. [Azure](https://cloudinit.readthedocs.io/en/latest/topics/datasources/azure.html#azure)의 cloud-init 이미지에서 VM이 생성되면 cloud-init은 Azure 데이터 원본을 로드하며 Azure 메타데이터 엔드포인트와 상호 작용하여 VM별 구성을 가져옵니다.
2. **런타임 구성**(/run/cloud-init)
3. **이미지 구성**(/etc/cloud)(예: `/etc/cloud/cloud.cfg` `/etc/cloud/cloud.cfg.d/*.cfg`) Azure에서 사용되는 경우의 예로, cloud-init이 있는 Linux OS 이미지에는 Azure 데이터 원본 지시문이 있는 것이 일반적입니다. 즉, cloud-init에 사용해야 하는 데이터 원본을 알려주므로 cloud-init 시간이 절약됩니다.

   ```bash
   /etc/cloud/cloud.cfg.d# cat 90_dpkg.cfg
   # to update this file, run dpkg-reconfigure cloud-init
   datasource_list: [ Azure ]
   ```


## <a name="cloud-init-boot-stages-processing-configuration"></a>Cloud-init 부팅 단계(구성 처리)

cloud-init으로 프로비전할 때 구성을 처리하고 로그에 표시되는 5단계의 부팅이 있습니다.

1. [생성기 단계](https://cloudinit.readthedocs.io/en/latest/topics/boot.html#generator): cloud-init systemd 생성기가 시작되고, cloud-init이 부팅 목표에 포함되어야 함을 확인한 후 해당하는 경우 cloud-init을 사용합니다. 

2. [Cloud-init 로컬 단계](https://cloudinit.readthedocs.io/en/latest/topics/boot.html#local): 여기에서 cloud-init은 로컬 "Azure" 데이터 원본을 찾아서 cloud-init이 Azure와 상호 작용할 수 있도록 하며, 대체 항목을 포함한 네트워킹 구성을 적용합니다.

3. [Cloud-init 시작 단계(네트워크)](https://cloudinit.readthedocs.io/en/latest/topics/boot.html#network): 네트워킹이 온라인 상태여야 하며 NIC 및 경로 테이블 정보가 생성되어야 합니다. 이 단계에서는 /etc/cloud/cloud.cfg의 `cloud_init_modules`에 나열된 모듈이 실행됩니다. Azure의 VM이 탑재되고, 임시 디스크가 포맷되며, 호스트 이름이 다른 작업과 함께 설정됩니다.

   다음은 `cloud_init_modules`의 일부입니다.
   
   ```bash
   - migrator
   - seed_random
   - bootcmd
   - write-files
   - growpart
   - resizefs
   - disk_setup
   - mounts
   - set_hostname
   - update_hostname
   - ssh
   ```
   
   이 단계가 끝나면 cloud-init은 VM이 성공적으로 프로비전되었음을 Azure 플랫폼에 알립니다. 일부 모듈은 실패했을 수 있으며 일부 모듈 실패로 인해 프로비전 실패가 발생하는 것은 아닙니다.

4. [Cloud-init 구성 단계](https://cloudinit.readthedocs.io/en/latest/topics/boot.html#config): 이 단계에서는 /etc/cloud/cloud.cfg에 정의되고 나열된 `cloud_config_modules`의 모듈이 실행됩니다.


5. [Cloud-init 최종 단계](https://cloudinit.readthedocs.io/en/latest/topics/boot.html#final): 이 단계에서는 /etc/cloud/cloud.cfg에 나열된 `cloud_final_modules`의 모듈이 실행됩니다. 여기에는 패키지 설치 및 스크립트 실행 등 부팅 프로세스를 실행하는 동안 늦게 실행해야 하는 모듈이 있습니다. 

   -   이 단계에서는 `/var/lib/cloud/scripts` 아래의 디렉터리에 스크립트를 배치하여 실행할 수 있습니다.
   - `per-boot` - 이 디렉터리 내의 스크립트는 재부팅할 때마다 실행됩니다.
   - `per-instance` - 이 디렉터리 내의 스크립트는 새 인스턴스가 처음 부팅될 때 실행됩니다.
   - `per-once` - 이 디렉터리 내의 스크립트는 한 번만 실행됩니다.

## <a name="next-steps"></a>다음 단계

[cloud-init 문제 해결](cloud-init-troubleshooting.md)
