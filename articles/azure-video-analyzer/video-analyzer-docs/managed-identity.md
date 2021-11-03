---
title: Azure Video Analyzer를 사용한 관리 ID
description: 이 항목에서는 Azure Video Analyzer로 관리 ID를 사용하는 방법에 대해 설명합니다.
ms.service: azure-video-analyzer
ms.topic: how-to
ms.date: 10/21/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 5a0352586279a7d3efe7c40c4e59bda9c9fad355
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131035753"
---
# <a name="managed-identity"></a>관리 ID

개발자는 일반적으로 서로 다른 서비스 간의 통신을 보호하기 위해 비밀과 자격 증명을 관리하는 데 어려움을 겪고 있습니다. Azure에서 관리 ID를 사용하면 개발자가 Azure Active Directory(Azure AD)에서 Azure 리소스에 대한 ID를 제공하고 이를 사용하여 Azure AD 토큰을 가져오는 방식으로 자격 증명을 관리할 필요가 없습니다.

Azure Video Analyzer 계정을 만들 때 Azure Storage 계정을 연결해야 합니다. Video Analyzer를 사용하여 카메라에서 라이브 비디오를 녹화하는 경우 해당 데이터는 스토리지 계정의 컨테이너에 BLOB으로 저장됩니다. 필요에 따라 Video Analyzer 계정과 IoT Hub 연결할 수 있습니다. Video Analyzer 에지 모듈을 [투명 게이트웨이로](./cloud/use-remote-device-adapter.md)사용하는 경우에 필요합니다. 관리 ID를 사용하여 Video Analyzer 계정에 스토리지 계정에 대한 적절한 액세스 권한을 부여하고 다음과 같이 IoT Hub(솔루션에 필요한 경우) 부여해야 합니다.

## <a name="user-assigned-managed-identity-for-video-analyzer"></a>사용자가 할당한 Video Analyzer용 관리 ID

* [UAMI(사용자 할당 관리 ID)](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity) 만들기

> [!NOTE]
> 새 리소스(사용자가 할당한 관리 ID, 스토리지 계정, Video Analyzer 계정)를 만드는 리소스 그룹에 대한 [기여자](../../role-based-access-control/built-in-roles.md#contributor) 역할과 [사용자 액세스 관리자](../../role-based-access-control/built-in-roles.md#user-access-administrator) 역할 모두에 액세스할 수 있는 Azure 구독이 필요합니다. 적절한 권한이 없는 경우 계정 관리자에게 권한을 부여해 달라고 요청하세요. 연결된 스토리지 계정은 Video Analyzer 계정과 동일한 지역에 있어야 합니다. [표준 범용 v2](../../storage/common/storage-account-overview.md#types-of-storage-accounts) 스토리지 계정을 사용하는 것이 좋습니다.
Video Analyzer 계정에 연결하도록 선택하는 경우 IoT Hub에 대한 기여자 역할에 대한 액세스 권한도 있어야 합니다.

### <a name="enable-video-analyzer-account-to-access-storage-account"></a>Video Analyzer 계정에서 Storage 계정에 액세스하도록 설정

* Azure Storage 계정을 만듭니다.

* 위의 스토리지 계정에 대한 [Storage BLOB 데이터 기여자](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor) 및 [읽기 권한자](../../role-based-access-control/built-in-roles.md#reader) 역할을 관리 ID에 추가합니다.

* Video Analyzer 계정을 만들어 사용자 할당 관리 ID 및 스토리지 계정을 관련 속성에 대한 값으로 제공합니다.

그러면 Video Analyzer는 관리 ID를 사용하여 사용자를 대신해 스토리지 계정에 액세스할 수 있습니다.

### <a name="enable-video-analyzer-account-to-access-iot-hub"></a>Video Analyzer 계정에서 IoT Hub 액세스하도록 설정

이전 섹션에서는 Video Analyzer가 스토리지 계정에 액세스할 수 있도록 하는 UAMI를 만들었습니다. 이 단계에서는 Video Analyzer 계정에 UAMI를 통해 IoT Hub 대한 액세스 권한이 부여된 다음, IoT Hub Video Analyzer 계정에 연결됩니다. 관리 ID가 IoT Hub 작동하는 방식에 대한 자세한 내용은 IoT Hub [관리 ID를 참조하세요.](../../iot-hub/iot-hub-managed-identity.md)

* Azure Portal Video Analyzer 관리 블레이드로 이동하고 왼쪽 **창의** **설정** 아래에서 IoT Hub 선택합니다.
* 그런 다음, '연결 IoT Hub' 창에서 **연결** 을 선택합니다. 연결 **IoT Hub** 구성 창에서 필요한 필드 값을 입력합니다.
    * 구독 - IoT Hub 생성되는 Azure 구독 이름을 선택합니다.
    * IoT Hub - Video Analyzer 계정에 연결해야 하는 기존 IoT Hub 선택
    * 관리 ID - IoT Hub 액세스하는 데 사용할 사용자 할당 관리 ID(이전 섹션에서 만든)를 선택합니다.
* **저장을** 클릭하여 IoT Hub Video Analyzer 계정에 연결합니다.

Azure Portal을 사용하여 위의 작업을 수행하는 예제는 [이](create-video-analyzer-account.md) 문서를 참조하세요.

## <a name="next-steps"></a>다음 단계

관리 ID가 사용자 및 Azure 애플리케이션에 대해 수행할 수 있는 작업에 대한 자세한 내용은 [Azure AD 관리 ID](../../active-directory/managed-identities-azure-resources/overview.md)를 참조하세요.
