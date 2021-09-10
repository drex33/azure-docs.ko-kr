---
title: Red Hat Enterprise Linux 연장된 수명 주기 지원
description: Red Hat Enterprise 수명 주기 연장 지원 애드온 추가에 대하여 알아봅니다.
author: mathapli
ms.service: virtual-machines
ms.subservice: redhat
ms.collection: linux
ms.topic: article
ms.date: 04/16/2020
ms.author: mathapli
ms.openlocfilehash: 82aa54f1b9aea331c7c08128487fbc9711cac2df
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/23/2021
ms.locfileid: "122696841"
---
# <a name="red-hat-enterprise-linux-rhel-extended-lifecycle-support"></a>RHEL(Red Hat Enterprise Linux) 수명 주기 연장 지원

**적용 대상:** :heavy_check_mark: Linux VM 

본 문서에서는 Red Hat Enterprise 이미지의 수명 주기 연장 지원 관련 정보를 제공합니다.
* Red Hat Enterprise Linux 6  

## <a name="red-hat-enterprise-linux-6-lifecycle"></a>Red Hat Enterprise Linux 6 수명 주기
2020년 11월 30일부터 Red Hat Enterprise Linux 6은 유지 관리 단계가 종료됩니다. 유지 관리 단계 이후 수명 연장 단계가 이어집니다. Red Hat Enterprise Linux 6이 전체/유지 관리 단계 종료로 전환하는 동안, Red Hat Enterprise Linux 7이나 8로의 업그레이드를 강력하게 권장됩니다. Red Hat Enterprise Linux 6을 계속 사용하여야 하는 고객의 경우, Red Hat Enterprise Linux ELS(수명 주기 연장 지원) 애드온을 추가하는 것이 좋습니다.

## <a name="steps-to-add-extended-lifecycle-support-on-marketplace-pay-as-you-go-vms"></a>마켓플레이스 종량제 VM에 수명 주기 연장 지원을 추가하는 단계
1. 연락처 상세 정보와 ELS 지원을 원하는 VM 구독 정보를 [여기에서 사용 가능한 ELS 양식](https://aka.ms/els-form)에 기입하세요. 애드온의 가격 책정 세부 정보도 같은 양식에서 확인할 수 있습니다.
1. 영업일 기준으로 하루이틀 사이에 Azure Red Hat Enterprise Linux 팀이 ELS 지원 애드온용 VM 목록을 가지고 연락드릴 것입니다. 목록을 검토한 다음 애드온 가격 책정에 동의 답신을 보내주세요.
1. Azure Red Hat Enterprise Linux 팀이 ELS 클라이언트 패키지를 VM에 추가하는 단계를 공유합니다. 이메일로 제공될 해당 단계에 따라 계속 소프트웨어 유지 관리(버그 픽스 및 보안 픽스) 및 Red Hat Enterprise Linux 6 지원을 받으세요.

> [!Note]
> 조직 외부의 사람과는 RHEL ELS 애드온 사용 단계를 공유하지 마세요. 추가적인 문의 사항에 대하여서는 AzureRedHatELS@microsoft.com으로 연락하여 지원을 받습니다.

## <a name="frequently-asked-questions"></a>질문과 대답

#### <a name="im-running-red-hat-enterprise-linux-6-and-cant-migrate-to-a-later-version-at-this-time-what-options-do-i-have"></a>Red Hat Enterprise Linux 6을 실행 중이며 지금 상황에서는 이후 버전으로 마이그레이션할 수가 없습니다. 어떤 방법이 있습니까?
* Red Hat Enterprise Linux 6을 계속 사용하되 ELS(수명 주기 연장 지원) 애드온 리포지토리를 구매하여 제한적으로 소프트웨어 유지 관리와 기술 지원을 계속 받습니다(업그레이드 프로세스와 가격 책정 세부 정보는 다음을 참조하세요).
* 가능한 경우 바로 Red Hat Enterprise Linux 7이나 8로 마이그레이션합니다.

#### <a name="what-is-the-additional-charge-for-using-red-hat-enterprise-linux-extended-life-cycle-support-els-add-on"></a>Red Hat Enterprise Linux ELS(수명 주기 연장 지원) 애드온을 사용하는 데 필요한 추가 요금은 어떻게 되나요?
[ELS 양식](https://aka.ms/els-form)에서 수명 주기 연장 지원과 관련된 요금을 확인할 수 있습니다.

#### <a name="ive-deployed-a-vm-by-using-custom-image-how-can-i-add-extended-lifecycle-support-to-this-vm"></a>사용자 지정 이미지를 사용하여서 VM을 배포하였습니다. 이런 VM에 수명 주기 연장 지원을 추가하려면 어떻게 하여야 하나요?
Red Hat에 직접 연락하여서 직접 지원을 받아야 합니다.

#### <a name="ive-deployed-a-vm-by-using-custom-image-can-i-convert-this-vm-to-a-payg-vm"></a>사용자 지정 이미지를 사용하여서 VM을 배포하였습니다. 해당 VM을 종량제 VM으로 전환할 수 있나요?
아니요, 그럴 수 없습니다. 현재, Azure에서는 전환이 지원되지 않습니다.


## <a name="next-steps"></a>다음 단계

* Azure의 RHEL 이미지 전체 목록을 보려면 [Azure에서 사용 가능한 RHEL(Red Hat Enterprise Linux) 이미지](./redhat-imagelist.md)를 참조하세요.
* Azure Red Hat 업데이트 인프라에 대해 자세히 알아보려면 [Azure의 주문형 RHEL VM용 Red Hat 업데이트 인프라](./redhat-rhui.md)를 참조하세요.
* RHEL BYOS 제품에 대해 자세히 알아보려면 [Azure의 Red Hat Enterprise Linux BYOS 골드 이미지](./byos.md)를 참조하세요.
* 모든 RHEL 버전의 Red Hat 지원 정책에 대한 자세한 내용은 [Red Hat Enterprise Linux 수명 주기](https://access.redhat.com/support/policy/updates/errata)를 참조하세요.