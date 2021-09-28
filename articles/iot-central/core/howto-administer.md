---
title: Azure IoT Central 애플리케이션 설정 변경 | Microsoft Docs
description: 애플리케이션 이름 및 URL을 변경하고, 이미지를 업로드하고, 애플리케이션을 삭제하여 Azure IoT Central 애플리케이션을 관리하는 방법
author: lizross
ms.author: lizross
ms.date: 08/25/2021
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 5bb973708dda2da52c9647bd58f00fc4009f59c2
ms.sourcegitcommit: 61e7a030463debf6ea614c7ad32f7f0a680f902d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/28/2021
ms.locfileid: "129093861"
---
# <a name="change-iot-central-application-settings"></a>IoT Central 애플리케이션 설정 변경

이 문서에서는 애플리케이션 이름 및 URL을 변경하고, 이미지를 업로드하고, Azure IoT Central 애플리케이션에서 애플리케이션을 삭제하여 애플리케이션을 관리하는 방법을 설명합니다.

**관리** 섹션에 액세스하여 사용하려면 Azure IoT Central 애플리케이션에서 **관리자** 역할이어야 합니다. Azure IoT Central 애플리케이션을 만드는 사용자에게는 자동으로 해당 애플리케이션의 **관리자** 역할이 할당됩니다.

## <a name="change-application-name-and-url"></a>애플리케이션 이름 및 URL 변경

**애플리케이션 설정** 페이지에서 애플리케이션의 이름 및 URL을 변경한 다음, **저장** 을 선택합니다.

![애플리케이션 설정 페이지](media/howto-administer/image-a.png)

관리자가 애플리케이션에 대한 사용자 지정 테마를 만들 경우 이 페이지에는 UI에서 **애플리케이션 이름** 을 숨기는 옵션이 포함됩니다. 이 옵션은 사용자 지정 테마의 애플리케이션 로고가 애플리케이션 이름을 포함하는 경우에 유용합니다. 자세한 내용은 [Azure IoT Central UI 사용자 지정](./howto-customize-ui.md)을 참조하세요.

> [!Note]
> URL을 변경하면 이전 URL을 다른 Azure IoT Central 고객이 사용할 수 있습니다. 이 경우 해당 URL을 더 이상 사용할 수 없습니다. URL을 변경하면 이전 URL이 더 이상 작동하지 않으며 사용자에게 새 URL을 사용하라고 알려야 합니다.

## <a name="delete-an-application"></a>애플리케이션 삭제

IoT Central 애플리케이션을 영구적으로 삭제하려면 **삭제** 단추를 사용합니다. 이 작업을 수행하면 애플리케이션과 연결된 모든 데이터가 영구적으로 삭제됩니다.

> [!Note]
> 애플리케이션을 삭제하려면 애플리케이션을 만들 때 선택한 Azure 구독의 리소스를 삭제할 수 있는 권한도 필요합니다. 자세한 정보는 [Azure 구독 리소스에 대한 액세스를 관리하는 Azure 역할 할당](../../role-based-access-control/role-assignments-portal.md)을 참조하세요.

## <a name="manage-programmatically"></a>프로그래밍 방식 관리

IoT Central Azure Resource Manager SDK 패키지는 Node, Python, C#, Ruby, Java 및 Go에 사용할 수 있습니다. 해당 패키지를 사용하여 IoT Central 애플리케이션을 생성, 나열, 업데이트 또는 삭제할 수 있습니다. 패키지에는 인증 및 오류 처리를 관리하는 도우미가 포함되어 있습니다.

[https://github.com/Azure-Samples/azure-iot-central-arm-sdk-samples](https://github.com/Azure-Samples/azure-iot-central-arm-sdk-samples)에서 Azure Resource Manager SDK 사용 방법의 예제를 찾을 수 있습니다.

자세한 정보는 다음 GitHub 리포지토리 및 패키지를 참조하세요.

| 언어 | 리포지토리 | 패키지 |
| ---------| ---------- | ------- |
| 노드 | [https://github.com/Azure/azure-sdk-for-js](https://github.com/Azure/azure-sdk-for-js) | [https://www.npmjs.com/package/@azure/arm-iotcentral](https://www.npmjs.com/package/@azure/arm-iotcentral)
| Python |[https://github.com/Azure/azure-sdk-for-python](https://github.com/Azure/azure-sdk-for-python) | [https://pypi.org/project/azure-mgmt-iotcentral](https://pypi.org/project/azure-mgmt-iotcentral)
| C# | [https://github.com/Azure/azure-sdk-for-net](https://github.com/Azure/azure-sdk-for-net) | [https://www.nuget.org/packages/Microsoft.Azure.Management.IotCentral](https://www.nuget.org/packages/Microsoft.Azure.Management.IotCentral)
| Ruby | [https://github.com/Azure/azure-sdk-for-ruby](https://github.com/Azure/azure-sdk-for-ruby) | [https://rubygems.org/gems/azure_mgmt_iot_central](https://rubygems.org/gems/azure_mgmt_iot_central)
| Java | [https://github.com/Azure/azure-sdk-for-java](https://github.com/Azure/azure-sdk-for-java) | [https://search.maven.org/search?q=a:azure-mgmt-iotcentral](https://search.maven.org/search?q=a:azure-mgmt-iotcentral)
| Go | [https://github.com/Azure/azure-sdk-for-go](https://github.com/Azure/azure-sdk-for-go) | [https://github.com/Azure/azure-sdk-for-go](https://github.com/Azure/azure-sdk-for-go)

## <a name="next-steps"></a>다음 단계

이제 Azure IoT Central 애플리케이션을 관리하는 방법을 배웠으므로 다음 단계로 Azure IoT Central에서 [사용자 및 역할 관리](howto-manage-users-roles.md)에 대해 알아보는 것을 제안합니다.
