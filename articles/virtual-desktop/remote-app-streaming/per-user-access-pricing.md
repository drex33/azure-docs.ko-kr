---
title: Azure Virtual Desktop 등록 사용자별 액세스 가격 - Azure
description: Azure Virtual Desktop에 대한 사용자별 액세스 가격 등록 방법.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 07/14/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: 67e16d2cf0a9ddbc7b4447b22207c908be4a3ac9
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131036659"
---
# <a name="enroll-your-subscription-in-per-user-access-pricing"></a>사용자별 액세스 가격으로 구독 등록

외부 사용자가 배포에 연결하려면, 먼저 사용자 단위 액세스 가격에 구독을 등록해야 합니다. 사용자 단위 액세스 가격은 사용자가 제공 하 고 관리 하는 id를 사용 하 여 구독에서 앱 및 데스크톱에 액세스할 수 있도록 조직 외부의 사용자를 되며 합니다. 등록된 구독은 Azure Virtual Desktop 리소스에 연결하는 고유 사용자 수를 기준으로 매달 청구됩니다.

>[!NOTE]
>외부 *사용자* 를 외부 *id* 와 혼동 하지 않도록 주의 하세요. Azure 가상 데스크톱은 현재 게스트 계정 또는 B2B (기업 간) id를 비롯 한 외부 id를 지원 하지 않습니다. Azure 가상 데스크톱을 사용 하 여 내부 사용자나 외부 사용자를 제공 하는 경우에는 해당 사용자에 대 한 id를 직접 만들고 관리 해야 합니다. 사용자 단위 액세스 가격은 Azure 가상 데스크톱에서 게스트 사용자 계정을 사용 하도록 설정 하는 방법이 아닙니다. 자세한 내용은 [라이선스 이해 및 사용자 단위 액세스 가격 책정](licensing.md)을 참조 하세요.

## <a name="how-to-enroll"></a>등록 방법

사용자 단위 액세스 가격에 Azure 구독을 등록하려면 다음을 수행합니다.

1. [https://portal.azure.com](https://portal.azure.com/)에서 Azure Portal에 로그인합니다.

2. 검색 창에서 **Azure Virtual Desktop** 을 입력한 다음, 서비스 아래에서 **Azure Virtual Desktop** 을 찾아서 선택합니다.

3. **Azure Virtual Desktop** 개요 페이지에서 **사용자 단위 액세스 가격** 을 선택합니다.

4. 구독 목록에서 Azure Virtual Desktop 리소스를 배포할 구독을 선택합니다.

5. **등록** 을 선택합니다.

6. 제품 약관을 검토 한 다음 **등록** 을 선택하여 등록을 시작합니다. 등록 프로세스가 완료되는 데 최대 한 시간이 걸릴 수 있습니다.

7. 등록이 완료된 후, 구독 목록의 **사용자 단위 액세스 가격** 열에서 값을 확인하여 "등록"에서 "등록됨"으로 변경되었는지 확인합니다.

## <a name="next-steps"></a>다음 단계

사용자별 액세스 가격에 대한 자세한 내용은 [라이선스 및 사용자별 액세스 가격 이해](licensing.md)를 참조하세요. 배포에 대한 사용자별 앱 스트리밍 비용을 예측하는 방법을 알아보려면 [Azure Virtual Desktop에 대한 사용자별 앱 스트리밍 비용 예측](streaming-costs.md)을 참조하세요. 총 배포 비용 예측에 대한 자세한 내용은 [총 Azure Virtual Desktop 배포 비용 이해](total-costs.md)를 참조하세요.
