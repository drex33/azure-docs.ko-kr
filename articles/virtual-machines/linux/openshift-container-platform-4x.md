---
title: Azure에서 OpenShift Container Platform 4.x 배포
description: Azure에서 OpenShift Container Platform 4.x를 배포합니다.
author: haroldwongms
manager: mdotson
ms.service: virtual-machines
ms.subservice: openshift
ms.collection: linux
ms.topic: how-to
ms.workload: infrastructure
ms.date: 10/14/2019
ms.author: haroldw
ms.openlocfilehash: f1e37e401bd44c4e6077e9a69dbc6ad72f8eaa0d
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/23/2021
ms.locfileid: "122693961"
---
# <a name="deploy-openshift-container-platform-4x-in-azure"></a>Azure에서 OpenShift Container Platform 4.x 배포

**적용 대상:** :heavy_check_mark: Linux VM :heavy_check_mark: 유연한 확장 집합 

이제 Azure에 IPI(설치 프로그램 프로비저닝된 인프라) 모델을 통해 OCP(OpenShift Container Platform) 4.2를 배포할 수 있습니다.  OpenShift 4 체험용 방문 페이지는 [try.openshift.com](https://try.openshift.com/)입니다. OCP 4.2를 Azure에 설치하려면 [Red Hat OpenShift 클러스터 관리자](https://cloud.redhat.com/openshift/install/azure/installer-provisioned) 페이지를 방문하세요.  해당 사이트에 액세스하려면 Red Hat 자격 증명이 필요합니다.


## <a name="notes"></a>메모 

 - AAD(Azure Active Directory) SP(서비스 주체)가 있어야 OCP 4.x를 Azure에 설치하여 실행할 수 있습니다.
     - SP는 Azure Active Directory 그래프에 대해 **Application.ReadWrite.OwnedBy** 의 API 권한을 부여받아야 합니다.
     - AAD 테넌트 관리자가 해당 API 권한을 적용하도록 관리자 동의를 부여해야 합니다.
     - SP는 구독에 대해 **기여자** 및 **사용자 액세스 관리자** 역할을 부여받아야 합니다.
 - OCP 4.x 설치 모델은 3.x 설치 모델과 다르며 Azure에 OCP 4.x을 배포하기 위해 사용할 수 있는 Azure Resource Manager 템플릿은 없습니다.
 - 설치 프로세스 중에 문제가 생기면 관련 회사(Microsoft나 Red Hat)에 문의하세요.

| 문제 설명 | 접점 |
|-------------------|---------------|
| Azure 관련 문제(AAD, SP, Azure 구독 등)                              | Microsoft |
| OpenShift 관련 문제(설치 실패/ 오류, Red Hat 구독 등) |  Red Hat  |




## <a name="next-steps"></a>다음 단계

- [OpenShift Container Platform 시작](https://docs.openshift.com)
