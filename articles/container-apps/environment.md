---
title: Azure Container Apps 환경 미리 보기
description: Azure Container Apps에서 환경을 관리 하는 방법을 알아봅니다.
services: container-apps
author: craigshoemaker
ms.service: container-apps
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: cshoe
ms.custom: ignite-fall-2021
ms.openlocfilehash: 122b172fc9fe2a66bae742f907d910cbccbf8fbc
ms.sourcegitcommit: 4cd97e7c960f34cb3f248a0f384956174cdaf19f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/08/2021
ms.locfileid: "132025801"
---
# <a name="azure-container-apps-preview-environments"></a>Azure 컨테이너 앱 미리 보기 환경

개별 컨테이너 앱은 컨테이너 앱 그룹에 대 한 보안 경계 역할을 하는 단일 컨테이너 앱 환경에 배포 됩니다. 동일한 환경의 컨테이너 앱은 동일한 가상 네트워크에 배포 되 고 동일한 Log Analytics 작업 영역에 로그를 기록 합니다.

:::image type="content" source="media/environments/azure-container-apps-environments.png" alt-text="Azure Container Apps 환경.":::

컨테이너 앱을 동일한 환경에 배포 하는 이유는 다음을 수행 해야 하는 경우입니다.

- 관련 서비스 관리
- 동일한 가상 네트워크에 다른 응용 프로그램 배포
- 응용 프로그램이 Eapr를 사용 하 여 서로 통신 하도록 합니다.
- 응용 프로그램에 동일한 (4) 구성
- 응용 프로그램이 동일한 log analytics 작업 영역을 공유 하도록 합니다.

컨테이너 앱을 다른 환경에 배포 하는 이유는 다음을 확인 하려는 경우입니다.

- 두 응용 프로그램이 동일한 계산 리소스를 공유 하지 않음
- 두 응용 프로그램은 두 응용 프로그램을 사용 하 여 서로 통신할 수 없습니다.

## <a name="logs"></a>로그

Azure Container Apps 환경 API 리소스와 관련 된 설정 합니다.

| 속성 | Description |
|---|---|
| `properties.appLogsConfiguration` | 환경에 있는 모든 앱에 대 한 로그를 게시할 Log Analytics 작업 영역을 구성 하는 데 사용 됩니다. |
| `properties.containerAppsConfiguration.daprAIInstrumentationKey` | 추적을 위해 d 4에 제공 된 앱 Insights 계측 키 |

## <a name="billing"></a>결제

청구는 개별 컨테이너 앱 및 해당 리소스 사용량과 관련이 있습니다. 컨테이너 앱 환경과 연결 된 기본 요금은 없습니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [자동 크기 조정 동작 관리](scale-app.md)