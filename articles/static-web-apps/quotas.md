---
title: Azure Static Web Apps의 할당량
description: Azure Static Web Apps와 관련된 할당량에 대해 알아보기
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: overview
ms.date: 10/13/2021
ms.author: cshoe
ms.openlocfilehash: 4565012ec824961f971f1f1fb34ab330ae11372c
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/14/2021
ms.locfileid: "129999305"
---
# <a name="quotas-in-azure-static-web-apps"></a>Azure Static Web Apps의 할당량

Azure Static Web Apps에는 다음과 같은 할당량이 있습니다.

| 기능                     | 무료 요금제        | 표준 계획 |
|-----------------------------|------------------|---------------|
| 포함된 대역폭          | 구독당 월 100GB | 구독당 월 100GB |
| 초과분 대역폭           | 사용할 수 없음      | GB당 $0.20 |
| Azure 구독당 앱 | 10               | 제한 없음 |
| 앱 크기                    | 250MB           | 500MB |
| 계획 크기                   | 단일 배포의 경우 최대 앱 크기 500MB, 모든 준비 및 프로덕션 환경의 경우 최대 0.50GB  | 단일 배포의 경우 최대 500MB의 앱 크기, 모든 준비 및 프로덕션 환경에서 결합하여 최대 2.00GB |
| 사전 프로덕션 환경 | 3                | 10 |
| 사용자 지정 도메인              | 앱당 2개        | 앱당 5개 |
| 권한 부여(기본 제공 역할) | 기본 제공 `authenticated` 역할로 인증할 수 있는 무제한 최종 사용자 | 기본 제공 `authenticated` 역할로 인증할 수 있는 무제한 최종 사용자 |
| 권한 부여(사용자 지정 역할) | [초대](authentication-authorization.md?tabs=invitations#role-management)를 통해 사용자 지정 역할에 속할 수 있는 최대 25명의 최종 사용자 | [초대](authentication-authorization.md?tabs=invitations#role-management)를 통해 사용자 지정 역할에 속할 수 있는 최대 25명의 최종 사용자 또는 [서버리스 함수](authentication-authorization.md?tabs=function#role-management)를 통해 사용자 지정 역할을 할당할 수 있는 무제한 최종 사용자 |

## <a name="github-storage"></a>GitHub 스토리지

GitHub Actions 및 패키지는 자체 할당량 세트가 있는 GitHub 스토리지를 사용합니다. Azure Static Web Apps 사이트를 만들면 GitHub는 배포 후에도 사이트의 아티팩트를 저장합니다.

자세한 내용은 다음 리소스를 참조하세요.

- [작업 스토리지 공간 관리](https://github.community/t5/GitHub-Actions/Managing-Actions-storage-space/td-p/38944)
- [GitHub Actions 요금 청구 정보](https://help.github.com/github/setting-up-and-managing-billing-and-payments-on-github/about-billing-for-github-actions#about-billing-for-github-actions)
- [GitHub Actions에 대한 지출 한도 관리](https://help.github.com/github/setting-up-and-managing-billing-and-payments-on-github/managing-your-spending-limit-for-github-actions)

## <a name="next-steps"></a>다음 단계

- [개요](overview.md)
