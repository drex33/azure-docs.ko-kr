---
title: 게시된 Azure Certified Device를 편집하는 방법
description: Azure Certified Device 프로그램을 통해 디바이스를 인증하고 게시한 다음 디바이스 정보를 편집하는 가이드입니다.
author: nkuntjoro
ms.author: nikuntjo
ms.service: certification
ms.topic: how-to
ms.date: 07/13/2021
ms.custom: template-how-to
ms.openlocfilehash: 82993fb11ad6f2d1fde3d997494e5fc9151673e5
ms.sourcegitcommit: ee8ce2c752d45968a822acc0866ff8111d0d4c7f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/14/2021
ms.locfileid: "113731615"
---
# <a name="edit-your-published-device"></a>게시된 디바이스 편집

디바이스가 인증되고 Azure Certified Device 카탈로그에 게시된 후에는 디바이스 세부 정보를 업데이트해야 할 수 있습니다. 이는 배포자 목록에 대한 업데이트, 구매 페이지 URL 변경, 하드웨어 사양 업데이트(예: 운영 체제 버전 또는 새 구성 요소 추가) 때문일 수 있습니다. 원래 모델 리포지토리에 업로드한 항목에서 IoT 플러그 앤 플레이 디바이스 모델을 업데이트해야 할 수도 있습니다.


## <a name="prerequisites"></a>필수 구성 요소

- 로그인되어 있고 [Azure Certified Device 포털](https://certify.azure.com)에서 만든 디바이스용 **승인된** 프로젝트가 있어야 합니다. 인증된 디바이스가 없는 경우 이 [자습서](tutorial-01-creating-your-project.md)를 확인하여 시작할 수 있습니다.


## <a name="editing-your-published-project-information"></a>게시된 프로젝트 정보 편집

프로젝트 요약에서는 프로젝트가 이미 검토되고 승인되었으므로 읽기 전용 모드임을 알 수 있습니다. 변경하려면 프로젝트에 대한 편집을 요청하고 Azure 인증 팀에 의해 업데이트가 다시 승인되어야 합니다.

1. 페이지 위에 있는 `Request Metadata Edit` 단추를 클릭합니다.  

    ![메타데이터 업데이트 요청](./media/images/request-metadata-edit.png)

1. 편집 후 검토를 위해 제품을 제출해야 한다는 페이지의 알림을 확인합니다.
    > [!NOTE]
    > 이 편집을 확인하면 디바이스가 이미 게시된 경우 Azure Certified Device 카탈로그에서 디바이스를 제거하지 **않습니다**. 이전 버전의 제품은 디바이스를 다시 게시할 때까지 카탈로그에 유지됩니다.
    > 또한 포털의 연결 및 테스트 섹션을 반복할 필요가 없습니다.

1. 이 경고를 확인하면 디바이스 세부 정보를 편집할 수 있습니다. 변경된 사항에 대한 `Device Details`의 `Comments for Reviewer` 섹션에 메모를 남겨야 합니다.

    ![메타데이터 편집 메모](./media/images/edit-notes.png)

1. 프로젝트 요약 페이지에서 `Submit for review`를 클릭하여 Azure 인증 팀의 변경 내용을 다시 승인합니다.
1. 변경 내용을 검토하고 승인한 후에는 포털을 통해 카탈로그에 변경 내용을 다시 게시할 수 있습니다([자습서](./tutorial-04-publishing-your-device.md) 참조).

## <a name="editing-your-iot-plug-and-play-device-model"></a>IoT 플러그 앤 플레이 디바이스 모델 편집

퍼블릭 모델 리포지토리에 디바이스 모델을 제출한 후에는 해당 모델을 제거할 수 없습니다. 디바이스 모델을 업데이트하고 인증된 디바이스를 새 모델에 다시 연결하려는 경우 디바이스를 새 프로젝트로 **다시 인증해야 합니다**. 이 작업을 수행하는 경우 인증 팀이 이전 디바이스 항목을 제거할 수 있도록 ‘검토자를 위한 설명’ 섹션에 메모를 남겨 주세요.

## <a name="next-steps"></a>다음 단계

Azure Certified Device 카탈로그에서 디바이스를 성공적으로 편집했습니다. 카탈로그에서 변경 내용을 체크 아웃하거나 다른 디바이스를 인증할 수 있습니다.
- [Azure Certified Device 카탈로그](https://devicecatalog.azure.com/)
- [디바이스 인증 시작](./tutorial-01-creating-your-project.md)
