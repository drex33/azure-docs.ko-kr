---
title: Azure VMware Solution by CloudSimple - CloudSimple 권한 에스컬레이션
description: 프라이빗 클라우드 vCenter에서 관리 기능을 수행하기 위해 CloudSimple 권한을 에스컬레이션하는 방법에 대해 설명합니다.
author: suzizuber
ms.author: v-szuber
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 36a8929b679f4c5aed1d7067dce855bef304986b
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132322249"
---
# <a name="escalate-cloudsimple-privileges-to-perform-administrative-functions-in-private-cloud-vcenter"></a>프라이빗 클라우드 vCenter에서 관리 기능을 수행하기 위한 CloudSimple 권한 에스컬레이션

CloudSimple 권한 접근 방식은 vCenter 사용자에게 일반 작업을 수행하는 데 필요한 권한을 제공하도록 설계되었습니다. 경우에 따라 사용자는 특정 작업을 수행하기 위한 추가 권한이 필요할 수 있습니다.  제한된 기간 동안 vCenter SSO 사용자의 권한을 에스컬레이션할 수 있습니다.

권한을 에스컬레이션하는 이유는 다음과 같습니다.

* ID 원본 구성
* 사용자 관리
* 분산 포트 그룹 삭제
* VCenter 솔루션(예: 백업 앱) 설치
* 서비스 계정 만들기

> [!WARNING]
> 에스컬레이션된 권한 상태에서 수행된 작업은 시스템에 부정적인 영향을 미치고 시스템을 사용할 수 없게 만들 수 있습니다. 에스컬레이션 기간 동안 필요한 작업만 수행합니다.

CloudSimple 포털에서 vCenter SSO의 CloudOwner 로컬 사용자에 대한 [권한을 에스컬레이션](escalate-private-cloud-privileges.md)합니다.  추가 ID 공급자가 vCenter에 구성된 경우에만 원격 사용자의 권한을 에스컬레이션할 수 있습니다.  권한 에스컬레이션에는 선택한 사용자를 vSphere 기본 제공 관리자 그룹에 추가하는 작업이 포함됩니다.  사용자 한 명에게만 에스컬레이션된 권한이 있을 수 있습니다.  다른 사용자의 권한을 에스컬레이션해야 하는 경우 먼저 현재 사용자의 권한을 에스컬레이션 해제합니다.

추가 ID 원본의 사용자를 CloudOwner 그룹의 구성원으로 추가해야 합니다.

> [!CAUTION]
> 새 사용자는 *클라우드-소유자-그룹*, *클라우드-글로벌 클러스터-관리자-그룹*, *클라우드-글로벌-스토리지 관리자-그룹*, *클라우드-글로벌-네트워크-관리자-그룹* 또는 *클라우드-글로벌-VM 관리자-그룹* 에만 추가해야 합니다.  *관리자* 그룹에 추가된 사용자는 자동으로 제거됩니다.  서비스 계정만 *관리자* 그룹에 추가해야 하며, 서비스 계정은 vSphere 웹 UI에 로그인하는 데 사용하면 안 됩니다.

에스컬레이션 기간 동안 CloudSimple은 연결된 경고 알림과 함께 자동화된 모니터링을 사용하여 환경에 대한 실수로 인한 변경 내용을 식별합니다.
