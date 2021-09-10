---
title: SAP HANA(Large Instances)에서 kdump를 사용하도록 설정하는 스크립트 | Microsoft Docs
description: Azure HANA Large Instances 형식 I 및 형식 II에서 kdump 서비스를 사용하도록 설정하는 방법에 대해 알아봅니다.
services: virtual-machines-linux
documentationcenter: ''
author: Ajayan1008
manager: juergent
editor: ''
ms.service: virtual-machines-sap
ms.subservice: baremetal-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 06/22/2021
ms.author: madhukan
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 272ac309629c62ee9fc7d12236aac4b2c3106b8a
ms.sourcegitcommit: 5fabdc2ee2eb0bd5b588411f922ec58bc0d45962
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/23/2021
ms.locfileid: "112540927"
---
# <a name="kdump-for-sap-hana-on-azure-large-instances"></a>Azure Large Instances의 SAP HANA용 kdump

이 문서에서는 Azure HANA Large Instances(HLI) **형식 I 및 형식 II** 에서 kdump 서비스를 사용하도록 설정하는 과정을 설명합니다.

명백한 원인이 없는 시스템 충돌 문제를 해결하려면 kdump를 구성하고 사용하도록 설정해야 합니다. 하드웨어 또는 인프라 문제로 인해 시스템 충돌을 설명할 수 없는 경우도 있습니다. 이러한 경우, 운영 체제 또는 애플리케이션에서 문제를 일으킨 것일 수 있습니다. kdump를 사용하면 SUSE에서 시스템 충돌 원인을 확인할 수 있습니다.

## <a name="supported-skus"></a>지원되는 SKU

|  Hana Large Instance 유형   |  OS 공급업체   |  OS 패키지 버전   |  SKU |
|-----------------------------|--------------|-----------------------|-------------|
|   유형 I                    |  SuSE        |   SLES 12 SP3         |  S224m      |
|   유형 I                    |  SuSE        |   SLES 12 SP4         |  S224m      |
|   유형 I                    |  SuSE        |   SLES 12 SP2         |  S72        |
|   유형 I                    |  SuSE        |   SLES 12 SP2         |  S72m       |
|   유형 I                    |  SuSE        |   SLES 12 SP3         |  S72m       |
|   유형 I                    |  SuSE        |   SLES 12 SP2         |  S96        |
|   유형 I                    |  SuSE        |   SLES 12 SP3         |  S96        |
|   유형 I                    |  SuSE        |   SLES 12 SP2         |  S192       |
|   유형 I                    |  SuSE        |   SLES 12 SP3         |  S192       |
|   유형 I                    |  SuSE        |   SLES 12 SP4         |  S192       |
|   유형 I                    |  SuSE        |   SLES 12 SP2         |  S192m      |
|   유형 I                    |  SuSE        |   SLES 12 SP3         |  S192m      |
|   유형 I                    |  SuSE        |   SLES 12 SP4         |  S192m      |
|   유형 I                    |  SuSE        |   SLES 12 SP2         |  S144       |
|   유형 I                    |  SuSE        |   SLES 12 SP3         |  S144       |
|   유형 I                    |  SuSE        |   SLES 12 SP2         |  S144m      |
|   유형 I                    |  SuSE        |   SLES 12 SP3         |  S144m      |
|   유형 II                   |  SuSE        |   SLES 12 SP2         |  S384       |
|   유형 II                   |  SuSE        |   SLES 12 SP3         |  S384       |
|   유형 II                   |  SuSE        |   SLES 12 SP4         |  S384       |
|   유형 II                   |  SuSE        |   SLES 12 SP2         |  S384xm     |
|   유형 II                   |  SuSE        |   SLES 12 SP3         |  S384xm     |
|   유형 II                   |  SuSE        |   SLES 12 SP4         |  S384xm     |
|   유형 II                   |  SuSE        |   SLES 12 SP2         |  S576m      |
|   유형 II                   |  SuSE        |   SLES 12 SP3         |  S576m      |
|   유형 II                   |  SuSE        |   SLES 12 SP4         |  S576m      |

## <a name="prerequisites"></a>필수 구성 요소

- Kdump 서비스는 `/var/crash` 디렉터리를 사용하여 덤프를 작성합니다. 이 디렉터리에 해당하는 파티션에 덤프를 수용할 수 있는 충분한 공간이 있는지 확인하세요.

## <a name="setup-details"></a>설정 정보

- Kdump를 사용하도록 설정하는 스크립트는 [GitHub의 Azure sap-hana tools](https://github.com/Azure/sap-hana-tools/blob/master/tools/enable-kdump.sh)에서 찾을 수 있습니다

> [!NOTE]
> 이 스크립트는 랩 설정에 기반하여 작성됩니다. 추가 조정에 대해서는 OS 공급 업체에 문의해야 합니다.
> 새 및 기존 서버에 대해 덤프를 저장하기 위해 별도의 논리 단위 번호(LUN)가 프로비전됩니다. 스크립트는 LUN에서 파일 시스템을 구성하는 과정을 처리합니다.
> Microsoft는 덤프 분석에 대해 책임을 지지 않습니다. 분석하려면 OS 공급 업체에서 티켓을 열어야 합니다.

- 다음 명령을 사용하여 HANA Large Instance에서이 스크립트를 실행합니다.

    > [!NOTE]
    > 이 명령을 실행하려면 Sudo 권한이 필요합니다.

    ```bash
    sudo bash enable-kdump.sh
    ```

- 명령 출력에 kdump가 성공적으로 사용되는 것으로 표시되면, 시스템을 재부팅하여 변경 내용을 적용합니다.

- 명령 출력에 작업이 실패한 것으로 표시되면, kdump 서비스를 사용할 수 없습니다. [지원 문제](#support-issues)는 다음 섹션을 참조하세요.

## <a name="test-kdump"></a>Kdump 테스트

> [!NOTE]
>  다음 작업은 커널 충돌 및 시스템 재부팅을 트리거합니다.

- 커널 충돌 트리거

    ```bash
    echo c > /proc/sysrq-trigger
    ```

- 시스템이 성공적으로 재부팅된 후, 커널 크래시 로그에 대한 `/var/crash` 디렉터리를 확인합니다.

- `/var/crash`에 현재 날짜의 디렉터리가 있으면, kdump를 사용하도록 설정합니다.

## <a name="support-issues"></a>지원 문제

오류가 발생하여 스크립트가 실패하거나 kdump를 사용할 수 없는 경우, Microsoft 지원 팀과 함께 서비스 요청을 제기합니다. 다음 세부 정보를 포함합니다.

* HLI 구독 ID

* 서버 이름

* OS 공급업체

* OS 버전

* 커널 버전

자세한 내용은 [kdump 구성](https://www.suse.com/support/kb/doc/?id=3374462)을 참조하세요.

## <a name="next-steps"></a>다음 단계

HANA Large Instances에서 운영 체제를 업그레이드하는 방법에 대해 알아봅니다.

> [!div class="nextstepaction"]
> [운영 체제 업그레이드](os-upgrade-hana-large-instance.md)
