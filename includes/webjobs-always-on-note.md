---
title: 파일 포함
description: 포함 파일
services: app-service
author: ggailey777
ms.service: app-service
ms.topic: include
ms.date: 09/01/2021
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 0d6363861a8412c7732a697f7e869504ddb106c6
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128558516"
---
> [!NOTE]
> 웹앱은 20분 동안 활동이 없을 경우 시간이 초과되고 실제 웹앱에 대한 요청만 타이머를 다시 설정할 수 있습니다. Azure Portal에서 앱의 구성을 보거나 고급 도구 사이트(`https://<app_name>.scm.azurewebsites.net`)에 요청을 수행해도 타이머가 다시 설정되지 않습니다. 작업을 지속적으로 실행하거나, 일정에 따라 실행하거나, 이벤트 기반 트리거를 사용하도록 호스트하는 웹앱을 설정하는 경우 웹앱의 Azure **구성** 페이지에서 **Always on** 설정을 사용하도록 설정합니다. Always On 설정을 사용하면 이러한 종류의 WebJobs를 안정적으로 실행할 수 있습니다. 이 기능은 기본, 표준 및 프리미엄 [가격 책정 계층](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)에서만 사용할 수 있습니다.
