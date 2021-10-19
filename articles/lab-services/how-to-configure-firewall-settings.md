---
title: Azure Lab Services에 대한 방화벽 설정
description: 정보를 방화벽 규칙에 추가할 수 있도록 랩의 가상 머신에 대한 공용 IP 주소와 포트 번호 범위를 확인하는 방법에 대해 알아봅니다.
author: emaher
ms.author: enewman
ms.date: 06/26/2020
ms.topic: how-to
ms.openlocfilehash: 2c98bbbfea0e31b156f45600bff310306650816b
ms.sourcegitcommit: 92889674b93087ab7d573622e9587d0937233aa2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/19/2021
ms.locfileid: "130180783"
---
# <a name="firewall-settings-for-azure-lab-services"></a>Azure Lab Services에 대한 방화벽 설정

각 조직 또는 학교는 자신의 요구에 가장 적합한 방식으로 자체 네트워크를 설정합니다.  경우에 따라 자체 네트워크 외부의 머신에 대한 RDP(원격 데스크톱 프로토콜) 또는 SSH(Secure Shell) 연결을 차단하는 방화벽 규칙을 설정하는 경우가 있습니다.  Azure Lab Services는 퍼블릭 클라우드에서 실행되기 때문에 학생이 캠퍼스 네트워크에서 연결할 때 해당 VM에 액세스할 수 있도록 몇 가지 추가 구성이 필요할 수 있습니다.

각 랩에서는 단일 공용 IP 주소와 여러 포트를 사용합니다.  모든 VM, 템플릿 VM 및 학생 VM 모두는 이 공용 IP 주소를 사용합니다.  공용 IP 주소는 랩의 수명 동안 변경되지 않습니다.  그러나 각 VM은 서로 다른 포트 번호를 갖게 됩니다.  포트 번호의 범위는 49152에서 65535까지입니다.  공용 IP 주소와 포트 번호의 조합은 강사와 학생을 올바른 VM에 연결하는 데 사용됩니다.  이 문서에서는 랩에서 사용되는 특정 공용 IP 주소를 찾는 방법을 설명합니다.  이러한 정보는 학생이 해당 VM에 액세스할 수 있도록 인바운드 및 아웃바운드 방화벽 규칙을 업데이트하는 데 사용할 수 있습니다.

>[!IMPORTANT]
>각 랩에는 서로 다른 공용 IP 주소가 있습니다.

> [!NOTE]
> 학교에서 [CIPA(Children's Internet Protection Act)](https://www.fcc.gov/consumers/guides/childrens-internet-protection-act) 준수와 같은 콘텐츠 필터링을 수행해야 하는 경우 타사 소프트웨어를 사용해야 합니다.  자세한 내용은 [Lab Services를 통해 콘텐츠 필터링](./administrator-guide.md#content-filtering)에 대한 지침을 참조하세요.

## <a name="find-public-ip-for-a-lab"></a>랩에 대한 공용 IP 찾기

각 랩에 대한 공용 IP 주소는 랩 서비스 랩 계정의 **모든 랩** 페이지에 나열됩니다.  **모든 랩** 페이지를 찾는 방법에 대한 지침은 [랩 계정에서 랩 보기](manage-labs.md#view-labs-in-a-lab-account)를 참조하세요.  

> [!div class="mx-imgBorder"]
> ![모든 랩 페이지](./media/how-to-configure-firewall-settings/all-labs-properties.png)

>[!NOTE]
>랩에 대한 템플릿 머신이 아직 게시되지 않은 경우 공용 IP 주소가 표시되지 않습니다.

## <a name="conclusion"></a>결론

이제 랩에 대한 공용 IP 주소를 알고 있습니다.  공용 IP 주소 및 포트 범위 49152-65535에 대한 조직의 방화벽에 대해 인바운드 및 아웃바운드 규칙을 만들 수 있습니다.  규칙을 업데이트한 후에는 학생이 액세스를 차단하는 네트워크 방화벽 없이 해당 VM에 액세스할 수 있습니다.

## <a name="next-steps"></a>다음 단계
다음 문서를 참조하세요.

- [랩 작성자가 랩 위치를 선택하도록 허용](allow-lab-creator-pick-lab-location.md)
- [랩의 네트워크를 피어 가상 네트워크에 연결](how-to-connect-peer-virtual-network.md)
- [공유 이미지 갤러리를 랩에 연결](how-to-attach-detach-shared-image-gallery.md)
- [사용자를 랩 소유자로 추가](how-to-add-user-lab-owner.md)
- [랩에 대한 방화벽 설정 보기](how-to-configure-firewall-settings.md)
- [랩에 대한 기타 설정 구성](how-to-configure-lab-accounts.md)
