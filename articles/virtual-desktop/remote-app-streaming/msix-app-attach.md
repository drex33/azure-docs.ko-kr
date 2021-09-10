---
title: Azure Virtual Desktop용 MSIX 앱 연결로 애플리케이션 배포 - Azure
description: Azure Virtual Desktop용 MSIX 앱 연결로 앱을 배포하는 방법입니다.
author: Heidilohr
ms.topic: how-to
ms.date: 07/14/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: 929f69ca4503a48e8e8456111c85043cbf2db9f2
ms.sourcegitcommit: f2eb1bc583962ea0b616577f47b325d548fd0efa
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/28/2021
ms.locfileid: "114730247"
---
# <a name="deploy-apps-with-msix-app-attach"></a>MSIX 앱 연결로 앱 배포

이 문서는 MSIX 앱 연결 기능을 사용하여 Azure Virtual Desktop에서 애플리케이션을 게시하는 방법에 대한 기본 개요입니다. 이 문서에서는 더 자세한 설명과 지침을 제공할 수 있는 리소스에 대한 링크도 제공합니다.

## <a name="what-is-msix-app-attach"></a>MSIX 앱 연결이란?

MSIX 앱 연결은 Azure Virtual Desktop의 활성 사용자 세션에 애플리케이션을 제공할 수 있는 애플리케이션 계층화 솔루션입니다. MSIX 패키지 시스템은 운영 체제에서 앱을 분리하여 가상 머신에 대한 이미지를 더 쉽게 빌드할 수 있습니다. 또한 MSIX 패키지를 사용하면 사용자가 가상 머신에서 액세스할 수 있는 앱을 보다 세부적으로 제어할 수 있습니다. 마스터 이미지에서 앱을 분리하여 나중에 사용자에게 제공할 수도 있습니다.

자세한 내용을 알아보려면 [MSIX 앱 연결이란?](../what-is-app-attach.md)을 참조하세요.

## <a name="requirements"></a>요구 사항

Azure Virtual Desktop에서 MSIX 앱 연결을 사용하려면 다음 항목이 필요합니다.

- MSIX 패키지 애플리케이션
- 확장된 MSIX 애플리케이션에서 만든 MSIX 이미지
- MSIX 이미지를 저장하는 네트워크 위치인 MSIX 공유
- 사용할 호스트 풀에 하나 이상의 정상 및 활성 세션 호스트
- MSIX 패키지 애플리케이션에 프라이빗 인증서가 있는 경우 호스트 풀의 모든 세션 호스트에서 해당 인증서를 사용할 수 있어야 합니다.
- MSIX 앱 연결에 대한 Azure Virtual Desktop 구성(사용자 할당, 앱 그룹과 MSIX 애플리케이션 연결, 호스트 풀에 MSIX 이미지 추가)

## <a name="create-an-msix-package-from-an-existing-installer"></a>기존 설치 관리자에서 MSIX 패키지 만들기

MSIX 앱 연결 사용을 시작하려면 MSIX 패키지 내에 애플리케이션을 배치해야 합니다. 일부 앱은 이미 MSIX 형식이지만 MSI, ClickOnce 등과 같은 레거시 설치 관리자를 사용하는 경우 앱을 MSIX 패키지 형식으로 변환해야 합니다. [MSIX 개요 문서](/windows/msix/packaging-tool/create-an-msix-overview)에서 기존 앱을 MSIX 형식으로 변환하는 방법을 알아봅니다.

## <a name="test-the-application-fidelity-of-your-packaged-app"></a>패키지된 앱의 애플리케이션 충실도 테스트 

애플리케이션을 MSIX 패키지로 다시 패키지한 후에는 애플리케이션 충실도가 높은지 확인해야 합니다. 앱 충실도는 다시 패키지하기 전과 후의 애플리케이션 동작 및 성능입니다. 앱 충실도가 높은 앱 패키지의 성능은 전과 후에 유사합니다.

다시 패키지한 후 앱 충실도가 저하되는 경우 조직에서 앱을 테스트하여 성능이 사용자 표준을 충족하는지 확인해야 합니다. 그렇지 않은 경우 문제를 방지하기 위해 앱을 업데이트하거나 다시 패키지해야 할 수 있습니다.

## <a name="create-an-msix-image"></a>MSIX 이미지 만들기

다음으로, 패키지된 앱에서 MSIX 이미지를 만들어야 합니다. MSIX 이미지는 MSIX 앱 패키지를 확장하고 결과 앱을 VHD(X) 또는 CIM 스토리지에 저장할 때 발생합니다. MSIX 이미지를 만드는 방법을 알아보려면 [MSIX 이미지 만들기](../app-attach-msixmgr.md#create-an-msix-image)를 참조하세요.

## <a name="configure-an-msix-file-share"></a>MSIX 파일 공유 구성

다음으로, MSIX 이미지를 저장하도록 MSIX 네트워크 공유를 설정해야 합니다. 구성되면 세션 호스트는 MSIX 공유를 사용하여 MSIX 패키지를 활성 사용자 세션에 연결하여 사용자에게 앱을 제공합니다. [MSIX 앱 연결에 대한 파일 공유 설정](../app-attach-file-share.md)에서 MSIX 공유를 설정하는 방법을 알아봅니다.

## <a name="configure-msix-app-attach-for-azure-virtual-desktop-host-pool"></a>Azure Virtual Desktop 호스트 풀용 MSIX 앱 연결 구성

MSIX 이미지를 MSIX 공유에 업로드한 후에는 Azure Portal을 열고 MSIX 앱 연결을 수락하는 데 사용할 호스트 풀을 구성해야 합니다. [Azure Portal에서 MSIX 앱 연결 설정](../app-attach-azure-portal.md)에서 호스트 풀을 구성하는 방법을 알아봅니다.
