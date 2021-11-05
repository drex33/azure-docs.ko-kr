---
title: 응용 프로그램에 대 한 자동 크기 조정 설정
description: 이 문서에서는 Microsoft Azure Portal 또는 Azure CLI를 사용하여 애플리케이션에 대한 자동 스케일링을 설정하는 방법에 대해 설명합니다.
author: karlerickson
ms.author: karler
ms.service: spring-cloud
ms.topic: how-to
ms.date: 11/03/2021
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: 67b5976e8df650ac8961cdb428338ca8147878d4
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/05/2021
ms.locfileid: "131846637"
---
# <a name="set-up-autoscale-for-applications"></a>응용 프로그램에 대 한 자동 크기 조정 설정

**이 문서는 다음에 적용됩니다.** ✔️ Java ✔️ C#

이 문서에서는 Microsoft Azure Portal 또는 Azure CLI를 사용하여 애플리케이션에 대한 자동 스케일링을 설정하는 방법에 대해 설명합니다.

자동 크기 조정은 Azure 스프링 클라우드의 기본 제공 기능으로, 수요가 변경 될 때 응용 프로그램에서 최적의 성능을 제공 하는 데 도움이 됩니다. Azure 스프링 클라우드는 규모 확장 및 규모 확장을 지원 합니다. 여기에는 앱 인스턴스 수 및 부하 분산을 수정 하는 작업이 포함 됩니다.
 
## <a name="prerequisites"></a>필수 구성 요소

이러한 절차를 따르려면 다음이 필요합니다.

* Azure 구독 Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.
* 배포된 Azure Spring Cloud 서비스 인스턴스 [Azure CLI를 통한 앱 배포를 위한 빠른 시작](./quickstart.md)을 수행하여 시작하세요.
* 해당 서비스 인스턴스에서 이미 하나 이상의 애플리케이션이 생성되었습니다.

## <a name="navigate-to-the-autoscale-page-in-the-azure-portal"></a>Azure Portal에서 자동 스케일링 페이지로 이동합니다.

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.
2. Azure Spring Cloud **개요** 페이지로 이동합니다.
3. 서비스가 포함된 리소스 그룹을 선택합니다.
4. 왼쪽 탐색 창에 있는 메뉴에서 **설정** 아래의 **앱** 탭을 선택합니다.
5. 자동 스케일링을 구성하려는 애플리케이션을 선택합니다. 이 예제에서는 **demo** 라는 애플리케이션을 선택합니다. 그러면 애플리케이션의 **개요** 페이지가 표시됩니다.
6. 왼쪽 탐색 창에 있는 메뉴에서 **설정** 아래의 **스케일 아웃** 탭으로 이동합니다.
7. 자동 스케일링을 설정하려는 배포를 선택합니다. 자동 스케일링 옵션이 다음 섹션에 표시됩니다.

![자동 스케일링 메뉴](./media/spring-cloud-autoscale/autoscale-menu.png)

## <a name="set-up-autoscale-settings-for-your-application-in-the-azure-portal"></a>Azure Portal에서 애플리케이션의 자동 스케일링 설정

자동 스케일링 수요를 관리하는 다음 두 가지 옵션이 있습니다.

* 수동 스케일링: 고정된 인스턴스 수를 유지합니다. 표준 계층에서는 최대 500개 인스턴스까지 스케일 아웃할 수 있습니다. 이 값은 응용 프로그램의 개별 실행 인스턴스 수를 변경 합니다.
* 사용자 지정 자동 스케일링: 메트릭을 기반으로 일정에 따라 스케일링

Azure Portal에서 스케일링 방법을 선택합니다.  다음 그림은 **사용자 지정 스케일링** 옵션 및 모드 설정을 보여줍니다.

![사용자 지정 자동 스케일링](./media/spring-cloud-autoscale/custom-autoscale.png)

## <a name="set-up-autoscale-settings-for-your-application-in-azure-cli"></a>Azure CLI에서 애플리케이션의 자동 스케일링 설정

Azure CLI를 사용하여 자동 스케일링 모드를 설정할 수도 있습니다.  다음 명령은 자동 스케일링 설정과 자동 스케일링 규칙을 만듭니다.

* 자동 스케일링 설정 만들기

   ```azurecli
   az monitor autoscale create -g demo-rg --resource /subscriptions/ffffffff-ffff-ffff-ffff-ffffffffffff/resourcegroups/demo-rg/providers/Microsoft.AppPlatform/Spring/autoscale/apps/demo/deployments/default --name demo-setting --min-count 1 --max-count 5 --count 1
   ```

* 자동 스케일링 규칙 만들기

   ```azurecli
   az monitor autoscale rule create -g demo-rg --autoscale-name demo-setting --scale out 1 --cooldown 1 --condition "tomcat.global.request.total.count > 100 avg 1m where AppName == demo and Deployment == default"
   ```

## <a name="upgrade-to-the-standard-tier"></a>표준 계층으로 업그레이드

기본 계층을 사용하고 이러한 제한 중 하나 이상이 적용되는 경우 표준 계층으로 업그레이드할 수 있습니다. 이렇게 하려면 먼저 **표준 계층** 열을 선택한 다음 **업그레이드** 버튼을 선택하여 **가격** 등급 메뉴로 이동합니다.

## <a name="next-steps"></a>다음 단계

* [Microsoft Azure 자동 스케일링의 개요](../azure-monitor/autoscale/autoscale-overview.md)
* [Azure CLI 모니터링 자동 스케일링](/cli/azure/monitor/autoscale)
