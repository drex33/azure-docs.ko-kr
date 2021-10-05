---
title: Azure에서 사용 가능한 Red Hat Enterprise Linux 이미지
description: Microsoft Azure의 Red Hat Enterprise Linux 이미지에 알아봅니다.
author: mamccrea
ms.service: virtual-machines
ms.subservice: redhat
ms.collection: linux
ms.topic: article
ms.date: 04/16/2020
ms.author: mamccrea
ms.openlocfilehash: f349e525d8fa16e7c218a248cb8e2c1fb4e21762
ms.sourcegitcommit: 557ed4e74f0629b6d2a543e1228f65a3e01bf3ac
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/05/2021
ms.locfileid: "129455117"
---
# <a name="red-hat-enterprise-linux-rhel-images-available-in-azure"></a>Azure에서 사용 가능한 RHEL(Red Hat Enterprise Linux) 이미지

**적용 대상:** :heavy_check_mark: Linux VM 

Azure는 다양한 사용 사례에 대해 다양한 RHEL 이미지를 제공합니다.

> [!NOTE]
> 모든 RHEL 이미지는 Azure 공용 및 Azure Government 클라우드에서 사용할 수 있습니다. Azure 중국 클라우드에서는 사용할 수 없습니다.

## <a name="list-of-rhel-images"></a>RHEL 이미지 목록
Azure에서 사용할 수 있는 RHEL 이미지 목록입니다. 달리 명시되지 않는 한 모든 이미지는 LVM으로 분할되어 일반 RHEL 리포지토리에 연결되었습니다(EUS 아님, E4S 아님). 현재 일반적으로 사용할 수 있는 이미지는 다음과 같습니다.

> [!NOTE]
> RAW 이미지는 더 이상 LVM 분할 이미지를 위해 생성되지 않습니다. LVM은 훨씬 더 유연한 파티션 크기 조정 옵션을 포함하여 이전 원시(비 LVM) 파티션 구성표에 비해 몇 가지 혜택을 제공합니다.

제안| SKU | 분할 | 프로비전 | 참고
:----|:----|:-------------|:-------------|:-----
RHEL          | 6.7      | RAW    | Linux 에이전트 | 연장된 수명 주기 지원은 12월 1일부터 제공됩니다. [자세한 내용은 여기를 참조하세요.](redhat-extended-lifecycle-support.md)
|             | 6.8      | RAW    | Linux 에이전트 | 연장된 수명 주기 지원은 12월 1일부터 제공됩니다. [자세한 내용은 여기를 참조하세요.](redhat-extended-lifecycle-support.md)
|             | 6.9      | RAW    | Linux 에이전트 | 연장된 수명 주기 지원은 12월 1일부터 제공됩니다. [자세한 내용은 여기를 참조하세요.](redhat-extended-lifecycle-support.md)
|             | 6.10     | RAW    | Linux 에이전트 | 연장된 수명 주기 지원은 12월 1일부터 제공됩니다. [자세한 내용은 여기를 참조하세요.](redhat-extended-lifecycle-support.md)
|             | 7-RAW    | RAW    | Linux 에이전트 | RHEL 7.x 이미지 제품군. <br> 기본적으로 일반 리포지토리에 연결되었습니다(EUS 아님).
|             | 7-LVM    | LVM    | Linux 에이전트 | RHEL 7.x 이미지 제품군. <br> 기본적으로 일반 리포지토리에 연결되었습니다(EUS 아님). 배포할 표준 RHEL 이미지를 찾고 있다면 이 이미지 집합 및/또는 해당 2세대 이미지를 사용합니다.
|             | 7lvm-gen2| LVM    | Linux 에이전트 | 2세대, RHEL 7.x 이미지 제품군. <br> 기본적으로 일반 리포지토리에 연결되었습니다(EUS 아님). 배포할 표준 RHEL 이미지를 찾고 있는 경우 이 이미지 집합 및/또는 해당 1세대 이미지를 사용합니다.
|             | 7-RAW-CI | RAW-CI | cloud-init  | RHEL 7.x 이미지 제품군. <br> 기본적으로 일반 리포지토리에 연결되었습니다(EUS 아님).
|             | 7.2      | RAW    | Linux 에이전트 |
|             | 7.3      | RAW    | Linux 에이전트 |
|             | 7.4      | RAW    | Linux 에이전트 | 2019년 4월 기준으로 기본적으로 EUS 리포지토리에 연결되었습니다.
|             | 74-gen2  | RAW    | Linux 에이전트 | 기본적으로 EUS 리포지토리에 연결되었습니다.
|             | 7.5      | RAW    | Linux 에이전트 | 2019년 6월 기준으로 기본적으로 EUS 리포지토리에 연결되었습니다.
|             | 75-gen2  | RAW    | Linux 에이전트 | 기본적으로 EUS 리포지토리에 연결되었습니다.
|             | 7.6      | RAW    | Linux 에이전트 | 2019년 5월 기준으로 기본적으로 EUS 리포지토리에 연결되었습니다.
|             | 76-gen2  | RAW    | Linux 에이전트 | 기본적으로 EUS 리포지토리에 연결되었습니다.
|             | 7.7      | LVM    | Linux 에이전트 | 기본적으로 EUS 리포지토리에 연결되었습니다.
|             | 77-gen2  | LVM    | Linux 에이전트 | 기본적으로 EUS 리포지토리에 연결되었습니다.
|             | 7.8      | LVM    | Linux 에이전트 | 일반 리포지토리에 연결되었습니다(RHEL 7.8에서는 EUS를 사용할 수 없음).
|             | 78-gen2  | LVM    | Linux 에이전트 | 일반 리포지토리에 연결되었습니다(RHEL 7.8에서는 EUS를 사용할 수 없음).
|             | 7.9      | LVM    | Linux 에이전트 | 일반 리포지토리에 연결되었습니다(RHEL 7.9에서는 EUS를 사용할 수 없음).
|             | 79-gen2  | LVM    | Linux 에이전트 | 일반 리포지토리에 연결되었습니다(RHEL 7.9에서는 EUS를 사용할 수 없음).
|             | 8-LVM    | LVM    | Linux 에이전트 | RHEL 8.x 이미지 제품군. 일반 리포지토리에 연결되었습니다.
|             | 8-lvm-gen2| LVM    | Linux 에이전트 | Hyper-V 2세대 - RHEL 8.x 이미지 제품군. 일반 리포지토리에 연결되었습니다.
|             | 8        | LVM    | Linux 에이전트 | RHEL 8.0 이미지.
|             | 8-gen2   | LVM    | Linux 에이전트 | Hyper-V 2세대 - RHEL 8.0 이미지.
|             | 8.1      | LVM    | Linux 에이전트 | 기본적으로 EUS 리포지토리에 연결되었습니다.
|             | 81gen2   | LVM    | Linux 에이전트 | Hyper-V 2세대 - 2020년 11월 기준으로 EUS 리포지토리에 연결됩니다.
|             | 8.1-ci   | LVM    | Linux 에이전트 | 2020년 11월 기준으로 EUS 리포지토리에 연결되었습니다.
|             | 81-ci-gen2| LVM    | Linux 에이전트 | Hyper-V 2세대 - 2020년 11월 기준으로 EUS 리포지토리에 연결되었습니다.
|             | 8.2      | LVM    | Linux 에이전트 | 2020년 11월 기준으로 EUS 리포지토리에 연결되었습니다.
|             | 82gen2   | LVM    | Linux 에이전트 | Hyper-V 2세대 - 2020년 11월 기준으로 EUS 리포지토리에 연결되었습니다.
|             | 8.3   | LVM    | Linux 에이전트 |  일반 리포지토리에 연결되었습니다(RHEL 8.3에서는 EUS를 사용할 수 없음).
|             | 83-gen2   | LVM    | Linux 에이전트 |Hyper-V 2세대 - 일반 리포지토리에 연결되었습니다(RHEL 8.3에서는 EUS를 사용할 수 없음).
RHEL-SAP      | 7.4      | LVM    | Linux 에이전트 | SAP HANA 및 비즈니스 앱용 RHEL 7.4 E4S 리포지토리에 연결되어 SAP 및 RHEL에 대한 프리미엄과 기본 컴퓨팅 요금을 청구합니다.
|             | 74sap-gen2| LVM    | Linux 에이전트 | SAP HANA 및 비즈니스 앱용 RHEL 7.4 2세대 이미지. E4S 리포지토리에 연결되어 SAP 및 RHEL에 대한 프리미엄과 기본 컴퓨팅 요금을 청구합니다.
|             | 7.5       | LVM    | Linux 에이전트 | SAP HANA 및 비즈니스 앱용 RHEL 7.5 E4S 리포지토리에 연결되어 SAP 및 RHEL에 대한 프리미엄과 기본 컴퓨팅 요금을 청구합니다.
|             | 75sap-gen2| LVM    | Linux 에이전트 | SAP HANA 및 비즈니스 앱용 RHEL 7.5 2세대 이미지. E4S 리포지토리에 연결되어 SAP 및 RHEL에 대한 프리미엄과 기본 컴퓨팅 요금을 청구합니다.
|             | 7.6       | LVM    | Linux 에이전트 | SAP HANA 및 비즈니스 앱용 RHEL 7.6 E4S 리포지토리에 연결되어 SAP 및 RHEL에 대한 프리미엄과 기본 컴퓨팅 요금을 청구합니다.
|             | 76sap-gen2| LVM    | Linux 에이전트 | SAP HANA 및 비즈니스 앱용 RHEL 7.6 2세대 이미지. E4S 리포지토리에 연결되어 SAP 및 RHEL에 대한 프리미엄과 기본 컴퓨팅 요금을 청구합니다.
|             | 7.7       | LVM    | Linux 에이전트 | SAP HANA 및 비즈니스 앱용 RHEL 7.7 E4S 리포지토리에 연결되어 SAP 및 RHEL에 대한 프리미엄과 기본 컴퓨팅 요금을 청구합니다.
RHEL-SAP-HANA(2020년 11월 제거 예정) | 6.7       | RAW    | Linux 에이전트 | SAP HANA용 RHEL 6.7 RHEL-SAP 이미지에 비해 구식입니다. 이 이미지는 2020년 11월에 제거됩니다. Red Hat의 SAP 클라우드 제품에 대한 자세한 내용은 [인증된 클라우드 공급자의 SAP 제품](https://access.redhat.com/articles/3751271)에서 제공됩니다.
|             | 7.2       | LVM    | Linux 에이전트 | SAP HANA용 RHEL 7.2 RHEL-SAP 이미지에 비해 구식입니다. 이 이미지는 2020년 11월에 제거됩니다. Red Hat의 SAP 클라우드 제품에 대한 자세한 내용은 [인증된 클라우드 공급자의 SAP 제품](https://access.redhat.com/articles/3751271)에서 제공됩니다.
|             | 7.3       | LVM    | Linux 에이전트 | SAP HANA용 RHEL 7.3 RHEL-SAP 이미지에 비해 구식입니다. 이 이미지는 2020년 11월에 제거됩니다. Red Hat의 SAP 클라우드 제품에 대한 자세한 내용은 [인증된 클라우드 공급자의 SAP 제품](https://access.redhat.com/articles/3751271)에서 제공됩니다.
RHEL-SAP-APPS | 6.8       | RAW    | Linux 에이전트 | SAP Business Applications용 RHEL 6.8 RHEL-SAP 이미지에 비해 구식입니다.
|             | 7.3       | LVM    | Linux 에이전트 | SAP Business Applications용 RHEL 7.3 RHEL-SAP 이미지에 비해 구식입니다.
|             | 7.4       | LVM    | Linux 에이전트 | SAP Business Applications용 RHEL 7.4
|             | 7.6       | LVM    | Linux 에이전트 | SAP Business Applications용 RHEL 7.6
|             | 7.7       | LVM    | Linux 에이전트 | SAP Business Applications용 RHEL 7.7
|             | 77-gen2       | LVM    | Linux 에이전트 | SAP Business Applications용 RHEL 7.7 2세대 이미지
|             | 8.1       | LVM    | Linux 에이전트 | SAP Business Applications용 RHEL 8.1
|             | 81-gen2      | LVM    | Linux 에이전트 | SAP Business Applications용 RHEL 8.1 2세대 이미지.
|             | 8.2       | LVM    | Linux 에이전트 | SAP Business Applications용 RHEL 8.2
|             | 82-gen2      | LVM    | Linux 에이전트 | SAP Business Applications용 RHEL 8.2 2세대 이미지.
RHEL-HA       | 7.4       | LVM    | Linux 에이전트 | HA 추가 항목이 있는 RHEL 7.4. 기본 컴퓨팅 요금 외에 HA 및 RHEL에 대한 프리미엄을 청구합니다. RHEL-SAP-HA 이미지에 비해 구식입니다.
|             | 7.5       | LVM    | Linux 에이전트 | HA 추가 기능이 있는 RHEL 7.5. 기본 컴퓨팅 요금 외에 HA 및 RHEL에 대한 프리미엄을 청구합니다. RHEL-SAP-HA 이미지에 비해 구식입니다.
|             | 7.6       | LVM    | Linux 에이전트 | HA 추가 기능이 있는 RHEL 7.6. 기본 컴퓨팅 요금 외에 HA 및 RHEL에 대한 프리미엄을 청구합니다. RHEL-SAP-HA 이미지에 비해 구식입니다.
RHEL-SAP-HA   | 7.4          | LVM    | Linux 에이전트 | HA 및 업데이트 서비스가 포함된 SAP용 RHEL 7.4. E4S 리포지토리에 연결되었습니다. 기본 컴퓨팅 요금 외에 SAP 및 HA 리포지토리와 RHEL에 대한 프리미엄을 청구합니다.
|             | 74sapha-gen2 | LVM    | Linux 에이전트 | HA 및 업데이트 서비스가 포함된 SAP용 RHEL 7.4. 2세대 이미지. E4S 리포지토리에 연결되었습니다. 기본 컴퓨팅 요금 외에 SAP 및 HA 리포지토리와 RHEL에 대한 프리미엄을 청구합니다.
|             | 7.5          | LVM    | Linux 에이전트 | HA 및 업데이트 서비스가 포함된 SAP용 RHEL 7.5. E4S 리포지토리에 연결되었습니다. 기본 컴퓨팅 요금 외에 SAP 및 HA 리포지토리와 RHEL에 대한 프리미엄을 청구합니다.
|             | 7.6          | LVM    | Linux 에이전트 | HA 및 업데이트 서비스가 포함된 SAP용 RHEL 7.6. E4S 리포지토리에 연결되었습니다. 기본 컴퓨팅 요금 외에 SAP 및 HA 리포지토리와 RHEL에 대한 프리미엄을 청구합니다.
|             | 76sapha-gen2 | LVM    | Linux 에이전트 | HA 및 업데이트 서비스가 포함된 SAP용 RHEL 7.6. 2세대 이미지. E4S 리포지토리에 연결되었습니다. 기본 컴퓨팅 요금 외에 SAP 및 HA 리포지토리와 RHEL에 대한 프리미엄을 청구합니다.
|             | 7.7          | LVM    | Linux 에이전트 | HA 및 업데이트 서비스가 포함된 SAP용 RHEL 7.7. E4S 리포지토리에 연결되었습니다. 기본 컴퓨팅 요금 외에 SAP 및 HA 리포지토리와 RHEL에 대한 프리미엄을 청구합니다.
|             | 77sapha-gen2 | LVM    | Linux 에이전트 | HA 및 업데이트 서비스가 포함된 SAP용 RHEL 7.7. 2세대 이미지. E4S 리포지토리에 연결되었습니다. 기본 컴퓨팅 요금 외에 SAP 및 HA 리포지토리와 RHEL에 대한 프리미엄을 청구합니다.
|             | 8.1          | LVM    | Linux 에이전트 | HA 및 업데이트 서비스가 포함된 SAP용 RHEL 8.1. E4S 리포지토리에 연결되었습니다. 기본 컴퓨팅 요금 외에 SAP 및 HA 리포지토리와 RHEL에 대한 프리미엄을 청구합니다.
|             | 81sapha-gen2          | LVM    | Linux 에이전트 | HA 및 업데이트 서비스가 포함된 SAP용 RHEL 8.1. E4S 리포지토리에 연결된 2세대 이미지. 기본 컴퓨팅 요금 외에 SAP 및 HA 리포지토리와 RHEL에 대한 프리미엄을 청구합니다.
|             | 8.2          | LVM    | Linux 에이전트 | HA 및 업데이트 서비스가 포함된 SAP용 RHEL 8.2. 기본 컴퓨팅 요금 외에 SAP 및 HA 리포지토리와 RHEL에 대한 프리미엄을 청구합니다.
|             | 82sapha-gen2          | LVM    | Linux 에이전트 | HA 및 업데이트 서비스가 포함된 SAP용 RHEL 8.2. E4S 리포지토리에 연결된 2세대 이미지. 기본 컴퓨팅 요금 외에 SAP 및 HA 리포지토리와 RHEL에 대한 프리미엄을 청구합니다.
rhel-byos     |rhel-lvm74| LVM    | Linux 에이전트 | 업데이트 원본에 연결되지 않은 RHEL 7.4 BYOS 이미지에는 RHEL 프리미엄이 부과되지 않습니다.
|             |rhel-lvm75| LVM    | Linux 에이전트 | 업데이트 원본에 연결되지 않은 RHEL 7.5 BYOS 이미지에는 RHEL 프리미엄이 부과되지 않습니다.
|             |rhel-lvm76| LVM    | Linux 에이전트 | 업데이트 원본에 연결되지 않은 RHEL 7.6 BYOS 이미지에는 RHEL 프리미엄이 부과되지 않습니다.
|             |rhel-lvm76-gen2| LVM    | Linux 에이전트 | 업데이트 원본에 연결되지 않은 RHEL 7.6 2세대 BYOS 이미지에는 RHEL 프리미엄이 부과되지 않습니다.
|             |rhel-lvm77| LVM    | Linux 에이전트 | 업데이트 원본에 연결되지 않은 RHEL 7.7 BYOS 이미지에는 RHEL 프리미엄이 부과되지 않습니다.
|             |rhel-lvm77-gen2| LVM    | Linux 에이전트 | 업데이트 원본에 연결되지 않은 RHEL 7.7 2세대 BYOS 이미지에는 RHEL 프리미엄이 부과되지 않습니다.
|             |rhel-lvm78| LVM    | Linux 에이전트 | 업데이트 원본에 연결되지 않은 RHEL 7.8 BYOS 이미지에는 RHEL 프리미엄이 부과되지 않습니다.
|             |rhel-lvm78-gen2| LVM    | Linux 에이전트 | 업데이트 원본에 연결되지 않은 RHEL 7.8 2세대 BYOS 이미지에는 RHEL 프리미엄이 부과되지 않습니다.
|             |rhel-lvm8 | LVM    | Linux 에이전트 | 업데이트 원본에 연결되지 않은 RHEL 8.0 BYOS 이미지에는 RHEL 프리미엄이 부과되지 않습니다.
|             |rhel-lvm8-gen2 | LVM    | Linux 에이전트 | 업데이트 원본에 연결되지 않은 RHEL 8.0 2세대 BYOS 이미지에는 RHEL 프리미엄이 부과되지 않습니다.
|             |rhel-lvm81 | LVM    | Linux 에이전트 | 업데이트 원본에 연결되지 않은 RHEL 8.1 BYOS 이미지에는 RHEL 프리미엄이 부과되지 않습니다.
|             |rhel-lvm81-gen2 | LVM    | Linux 에이전트 | 업데이트 원본에 연결되지 않은 RHEL 8.1 2세대 BYOS 이미지에는 RHEL 프리미엄이 부과되지 않습니다.
|             |rhel-lvm82 | LVM    | Linux 에이전트 | 업데이트 원본에 연결되지 않은 RHEL 8.2 BYOS 이미지에는 RHEL 프리미엄이 부과되지 않습니다.
|             |rhel-lvm82-gen2 | LVM    | Linux 에이전트 | 업데이트 원본에 연결되지 않은 RHEL 8.2 2세대 BYOS 이미지에는 RHEL 프리미엄이 부과되지 않습니다.

> [!NOTE]
> RHEL-SAP-HANA 제품 서비스는 Red Hat에서 수명 종료로 간주됩니다. 기존 배포는 계속해서 정상적으로 작동하지만 Red Hat은 고객이 RHEL-SAP-HANA 이미지에서 SAP HANA 리포지토리와 HA 추가 기능을 포함하는 RHEL-SAP-HA 이미지로 마이그레이션하는 것이 좋습니다. Red Hat의 SAP 클라우드 제품에 대한 자세한 내용은 [인증된 클라우드 공급자의 SAP 제품](https://access.redhat.com/articles/3751271)에서 제공됩니다.

## <a name="next-steps"></a>다음 단계
* [Azure의 Red Hat 이미지](./redhat-images.md)에 대해 자세히 알아봅니다.
* [Red Hat 업데이트 인프라](./redhat-rhui.md)에 대해 자세히 알아봅니다.
* [RHEL BYOS 제품](./byos.md)에 대해 자세히 알아봅니다.
* 모든 RHEL 버전에 대한 Red Hat 지원 정책 관련 정보는 [Red Hat Enterprise Linux 수명 주기](https://access.redhat.com/support/policy/updates/errata) 페이지에서 확인할 수 있습니다.
