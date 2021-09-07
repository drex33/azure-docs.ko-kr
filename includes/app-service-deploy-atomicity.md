---
title: 포함 파일
description: 포함 파일
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 01/07/2020
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: eff2a7eda24834389db7afc0a689767f20da045f
ms.sourcegitcommit: 34aa13ead8299439af8b3fe4d1f0c89bde61a6db
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/18/2021
ms.locfileid: "122423338"
---
## <a name="what-happens-to-my-app-during-deployment"></a>배포하는 동안 앱에서 진행되는 작업

공식적으로 지원되는 모든 배포 방법은 앱의 */home/site/wwwroot* 폴더에서 파일을 변경합니다. 이러한 파일은 앱을 실행하는 데 사용됩니다. 따라서 잠긴 파일로 인해 배포가 실패할 수 있습니다. 모든 파일이 동시에 업데이트되는 것은 아니기 때문에 배포하는 동안 앱이 예상과 다르게 동작할 수도 있습니다. 이러한 동작은 고객용 앱에는 바람직하지 않습니다. 이러한 문제를 방지하는 다음과 같은 몇 가지 방법이 있습니다.

- 압축을 풀지 않고 [ZIP 패키지에서 직접 앱을 실행](../articles/app-service/deploy-run-package.md)합니다.
- 배포하는 동안 앱을 중지하거나 앱에 대한 오프라인 모드를 사용하도록 설정합니다. 자세한 내용은 [배포 중 잠긴 파일 처리](https://github.com/projectkudu/kudu/wiki/Dealing-with-locked-files-during-deployment)를 참조하세요.
- [자동 전환](../articles/app-service/deploy-staging-slots.md#configure-auto-swap)을 켠 상태로 [스테이징 슬롯](../articles/app-service/deploy-staging-slots.md)에 배포합니다. 
