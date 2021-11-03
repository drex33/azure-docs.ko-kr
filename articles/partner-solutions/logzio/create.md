---
title: Logz.io 리소스 만들기 - Azure 파트너 솔루션
description: Azure에서 Logz.io 리소스를 만드는 방법을 설명하는 빠른 시작 문서입니다.
author: tfitzmac
ms.topic: conceptual
ms.service: partner-services
ms.date: 10/25/2021
ms.author: tomfitz
ms.custom: references_regions
ms.openlocfilehash: d97339251888b522fd31d30b3ce77d1bb4241f3a
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131070074"
---
# <a name="quickstart-create-a-logzio-resource-in-azure-portal"></a>빠른 시작: Azure Portal에서 Logz.io 리소스 만들기

이 문서에서는 Logz.io SaaS(Software as a Service)의 Azure 통합을 사용하도록 설정하는 방법을 설명합니다. Logz.io를 사용하면 Azure 환경의 상태 및 성능을 모니터링할 수 있습니다.

## <a name="prerequisites"></a>필수 구성 요소

- **구독 소유자**: Logz.io를 설정하려면 Azure 구독에서 [소유자 역할](../../role-based-access-control/rbac-and-directory-admin-roles.md#azure-roles)을 할당받아야 합니다. 이 통합을 시작하기 전에 [액세스를 확인](../../role-based-access-control/check-access.md)하세요.
- **리소스 공급자 등록**: `Microsoft.Insights`가 구독에 아직 등록되지 않았으면 등록합니다. 자세한 내용은 [리소스 공급자 등록](../../azure-resource-manager/management/resource-providers-and-types.md#register-resource-provider)을 참조하세요.

## <a name="find-offer"></a>제안 찾기

Azure Portal을 사용하여 Azure Marketplace에서 Logz.io를 찾습니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. 최근 세션에서 **Marketplace** 를 방문한 경우 사용 가능한 옵션에서 아이콘을 선택합니다. 그렇지 않으면 _Marketplace_ 를 검색합니다.

    :::image type="content" source="./media/create/marketplace.png" alt-text="Azure Marketplace.":::

1. Azure Marketplace에서 _Logz.io_ 를 검색합니다.
1. **Logz.io - ELK 및 Prometheus 기반 가시성**.
1. **설정 + 구독** 단추를 선택합니다. **새 Logz.io 계정 만들기** 창이 열립니다.

    :::image type="content" source="./media/create/logzio-app.png" alt-text="Logz.io 가시성 애플리케이션.":::

## <a name="create-new-logzio-account"></a>새 Logz.io 계정 만들기

**Logz.io 계정 만들기** 화면의 **기본** 탭에서 다음 값을 입력합니다.

| 속성 | 설명 |
| ---- | ---- |
| **구독** | 드롭다운 메뉴에서 소유자 액세스 권한이 있는 Azure 구독을 선택합니다. |
| **리소스 그룹** | 새 리소스 그룹을 만들지, 아니면 기존 리소스 그룹을 사용할지 여부를 지정합니다. [리소스 그룹](../../azure-resource-manager/management/overview.md#resource-groups)은 Azure 솔루션과 관련된 리소스를 보관하는 컨테이너입니다. |
| **Logz 계정 이름** | 만들려는 Logz.io 계정의 이름을 제공합니다. |
| **위치** | **미국 서부 2** 또는 **서유럽** 을 선택합니다. Logz.io 이러한 Azure 지역만 지원합니다. |
| **요금제** | 사용 가능한 Logz.io 플랜 목록에서 선택합니다. |
| **청구 기간** | **매월** 이 기본값입니다. |
| **가격** | 선택한 Logz.io 플랜에 따라 지정됩니다. |

:::image type="content" source="./media/create/basics.png" alt-text="Logz.io 계정을 만듭니다.":::

값을 입력한 후 **다음: 로그 및 메트릭** 단추를 선택합니다.

## <a name="configure-logs"></a>로그 구성

**로그 및 메트릭** 탭에서 Logz.io에 로그 및 메트릭을 전송할 Azure 리소스를 지정합니다.

Azure에서 Logz.io로 전송될 수 있는 두 가지 유형의 로그가 있습니다.

- **구독 수준 로그**: 외부(관리 평면)에서 구독의 각 Azure 리소스 관련 작업에 대한 인사이트 및 **서비스 상태** 이벤트에 대한 업데이트를 제공합니다. **활동 로그** 를 사용하여 구독 리소스에 수행된 모든 쓰기 작업(PUT, POST, DELETE)이 무엇이고, 누가, 언제 수행했는지 확인할 수 있습니다. 각 Azure 구독마다 단일 **활동 로그** 가 있습니다.
- **Azure 리소스 로그**: Azure 리소스(데이터 평면) 내에서 실행된 작업에 대한 인사이트를 제공합니다. 예를 들어 키 자격 증명 모음에서 비밀을 가져오거나 데이터베이스에 요청을 수행할 수 있습니다. 이러한 로그의 내용은 Azure 서비스와 리소스 종류에 따라 달라집니다.

구독 수준 로그는 **구독 수준 로그 보내기** 상자를 선택하여 Logz.io로 보낼 수 있습니다. 이 상자를 선택하지 않으면 구독 수준 로그가 Logz.io로 전송되지 않습니다.

Azure 리소스 로그는 **정의된 모든 리소스에 대한 Azure 리소스 로그 보내기** 라는 상자를 선택하여 Logz.io로 보낼 수 있습니다. Azure 리소스 로그의 유형은 [Azure Monitor 리소스 로그 범주](../../azure-monitor/essentials/resource-logs-categories.md)에 나열되어 있습니다. Azure 리소스 보내기 로그의 특정 집합을 Logz.io로 필터링하려면 Azure 리소스 태그를 사용하면 됩니다.

로그 보내기에 대한 태그 규칙은 다음과 같습니다.

- 기본적으로 모든 리소스에 대한 로그가 수집됩니다.
- _포함_ 태그가 있는 Azure 리소스는 로그를 Logz.io로 전송합니다.
- _제외_ 태그가 있는 Azure 리소스는 로그를 Logz.io로 전송하지 **않습니다**.
- 포함 규칙과 제외 규칙이 충돌하는 경우 제외 규칙이 우선 적용됩니다.

:::image type="content" source="./media/create/logs.png" alt-text="로그 및 메트릭을 설정합니다.":::

로그 및 메트릭을 구성한 후 **다음: 태그** 단추를 선택합니다.

## <a name="add-custom-tags"></a>사용자 지정 태그 추가

키 값 쌍을 추가하여 새 Logz.io 리소스에 대해 사용자 지정 태그를 지정할 수 있습니다.

각 키 값 쌍에는 **이름** 및 **값** 이 포함됩니다.

| 속성 | 설명 |
| ---- | ---- |
| **이름** | Azure Logz.io 리소스에 해당하는 태그의 이름입니다. |
| **값** | Azure Logz.io 리소스에 해당하는 태그의 값입니다. |

:::image type="content" source="./media/create/tags.png" alt-text="사용자 지정 태그를 추가합니다.":::

탭을 추가한 후 **다음: Single Sign-On** 단추를 선택합니다.

## <a name="configure-single-sign-on"></a>Single Sign-On 구성

SSO(Single Sign-On)는 선택적인 기능입니다.

- SSO를 옵트아웃하려면 이 단계를 건너뜁니다.
- SSO를 옵트인하려면 [Logz.io Single Sign-On을 설정](setup-sso.md)합니다.

AAD가 구성된 후 **Single Sign-On** 탭에서 Logz.io SSO 애플리케이션을 선택합니다.

:::image type="content" source="./media/create/sso.png" alt-text="Single Sign-On을 구성합니다.":::

**다음: 검토 + 만들기** 단추를 선택하여 리소스 만들기 최종 단계로 이동합니다.

## <a name="create-resource"></a>리소스 만들기

**검토 + 만들기** 페이지에서 모든 검증이 실행됩니다. **기본**, **로그 및 메트릭**, **태그** 및 **Single Sign-On** 탭에서 선택한 모든 항목을 검토할 수 있습니다. 또한 Logz.io 및 Azure Marketplace 사용 약관을 검토할 수 있습니다.

모든 정보를 검토하여 올바른지 확인합니다. 배포를 시작하려면 **만들기** 단추를 선택합니다.

:::image type="content" source="./media/create/create-resource.png" alt-text="계정을 검토하고 만듭니다.":::

배포가 성공한 후 **리소스로 이동** 단추를 선택하여 배포된 Logz.io 리소스를 확인할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- Logz.io 통합을 [관리](manage.md)하는 방법을 알아봅니다.
- 통합 관련 문제를 해결하려면 [문제 해결](troubleshoot.md)을 참조하세요.
