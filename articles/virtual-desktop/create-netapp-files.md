---
title: Azure NetApp 파일 만들기 Azure Virtual Desktop - Azure
description: 이 문서에서는 Azure Virtual Desktop에서 Azure NetApp Files를 만드는 방법을 설명합니다.
author: Heidilohr
ms.topic: how-to
ms.date: 08/19/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: fa6052c91be73e05bc2413f57810405fe5a198a0
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124818643"
---
# <a name="upload-msix-images-to-azure-netapp-files-in-azure-virtual-desktop"></a>Azure Virtual Desktop에서 MSIX 이미지를 Azure NetApp Files로 업로드

이 문서에서는 Azure Virtual Desktop에서 MSIX 이미지를 Azure NetApp Files로 업로드하는 방법을 설명합니다.

## <a name="requirements"></a>요구 사항

이미지 업로드를 시작하기 전에 아직 설정하지 않은 경우 Azure NetApp Files를 설정해야 합니다.

Azure NetApp Files를 설정하려면 다음이 필요합니다.

- 기여자 또는 관리자 액세스 권한이 있는 Azure 계정

- AD DS(Active Directory Domain Services) 및 액세스 권한에 조인된 VM(가상 머신) 또는 물리적 컴퓨터

- 도메인 조인 세션 호스트로 구성된 Azure Virtual Desktop 호스트 풀 각 세션 호스트는 Azure NetApp Files를 만드는 지역과 동일한 지역에 있어야 합니다. 자세한 내용은 [지역별 가용성](https://azure.microsoft.com/global-infrastructure/services/?products=netapp)을 참조하세요. 기존 세션 호스트가 사용 가능한 지역 중 하나에 없는 경우 새 세션 호스트를 만들어야 합니다.

## <a name="start-using-azure-netapp-files"></a>Azure NetApp Files 사용 시작

Azure NetApp Files 사용을 시작하려면 다음을 수행합니다.

1. [Azure NetApp Files 계정 설정](create-fslogix-profile-container.md#set-up-your-azure-netapp-files-account)의 지침에 따라 Azure NetApp Files 계정을 설정합니다.
2. [용량 풀 설정](../azure-netapp-files/azure-netapp-files-set-up-capacity-pool.md)의 지침에 따라 용량 풀을 만듭니다.
3. [Active Directory 연결 조인](create-fslogix-profile-container.md#join-an-active-directory-connection)의 지침에 따라 Azure AD(Azure Active Directory) 연결을 조인합니다.
4. [새 볼륨 만들기](create-fslogix-profile-container.md#create-a-new-volume) 및 [볼륨 액세스 매개 변수 구성](create-fslogix-profile-container.md#configure-volume-access-parameters)의 지침에 따라 새 볼륨을 만듭니다.
5. [사용자가 Azure NetApp Files 공유에 액세스할 수 있는지 확인](create-fslogix-profile-container.md#make-sure-users-can-access-the-azure-netapp-file-share)의 지침에 따라 Azure NetApp Files 공유에 대한 연결이 작동하는지 확인합니다.

## <a name="upload-an-msix-image-to-the-azure-netapp-file-share"></a>Azure NetApp Files 공유에 MSIX 이미지 업로드

이제 Azure NetApp Files 공유를 설정했으므로 이미지 업로드를 시작할 수 있습니다.

Azure NetApp Files 공유에 MSIX 이미지를 업로드하려면 다음을 수행합니다.

1. 각 세션 호스트에서 MSIX 패키지에 서명한 인증서를 설치합니다. **신뢰할 수 있는 사용자** 라는 폴더에 인증서를 저장해야 합니다.
2. Azure NetApps Files 공유에 추가하려는 MSIX 이미지를 복사합니다.
3. **파일 탐색기** 로 이동하고 탑재 경로를 입력한 다음, MSIX 이미지를 탑재 경로 폴더로 붙여넣습니다.

이제 세션 호스트가 Azure Portal 또는 PowerShell을 사용하여 MSIX 패키지를 추가할 때 MSIX 이미지에 액세스할 수 있습니다.

## <a name="next-steps"></a>다음 단계

이제 Azure NetApp Files 공유를 만들었으므로 Azure Virtual Desktop에서 사용할 수 있는 항목에 대한 몇 가지 리소스는 다음과 같습니다.

- [Azure NetApp Files 및 AD DS를 사용하여 프로필 컨테이너 만들기](create-fslogix-profile-container.md)
- [Azure Virtual Desktop의 FSLogix 프로필 컨테이너에 대한 스토리지 옵션](store-fslogix-profile.md)
- [Azure NetApp Files에 대한 복제 피어링 만들기](../azure-netapp-files/cross-region-replication-create-peering.md)
