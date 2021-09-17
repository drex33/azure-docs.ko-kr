---
title: Azure IoT Central UI 사용자 지정 | Microsoft Docs
description: Azure IoT Central 애플리케이션의 테마 및 도움말 링크를 사용자 지정하는 방법
author: dominicbetts
ms.author: dobett
ms.date: 08/18/2021
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 87ff1d68076f9e4ce45a6b0de6a567b2ce90da16
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/03/2021
ms.locfileid: "123431004"
---
# <a name="customize-the-azure-iot-central-ui"></a>Azure IoT Central UI 사용자 지정

이 문서에서는 사용자 지정 테마를 적용하고 고유의 사용자 지정 도움말 리소스를 가리키도록 도움말 링크를 수정하여 애플리케이션의 UI를 사용자 지정하는 방법에 대해 설명합니다. 

다음 스크린샷은 표준 테마를 사용하는 페이지를 보여 줍니다.

![표준 IoT Central 테마](./media/howto-customize-ui/standard-ui.png)

다음 스크린샷은 사용자 지정 UI 요소가 강조 표시된 사용자 지정 스크린샷을 사용하는 페이지를 보여 줍니다.

![사용자 지정 IoT Central 테마](./media/howto-customize-ui/themed-ui.png)

## <a name="create-theme"></a>테마 만들기

사용자 지정 테마를 만들려면 **관리** 섹션의 **애플리케이션 사용자 지정** 페이지로 이동합니다.

![IoT Central 테마](./media/howto-customize-ui/themes.png)

이 페이지에서 애플리케이션의 다음 측면을 사용자 지정할 수 있습니다.

### <a name="application-logo"></a>애플리케이션 로고

투명한 배경을 사용하며 1MB보다 크지 않은 PNG 이미지입니다. 이 로고는 IoT Central 애플리케이션 제목 표시줄의 왼쪽에 표시됩니다.

로고 이미지에 애플리케이션 이름이 포함된 경우 애플리케이션 이름 텍스트를 숨길 수 있습니다. 자세한 내용은 [애플리케이션 관리](howto-administer.md#change-application-name-and-url)를 참조하세요.

### <a name="browser-icon-favicon"></a>브라우저 아이콘(파비콘)

투명한 배경을 사용하며 32x32픽셀보다 크지 않은 PNG 이미지입니다. 웹 브라우저는 주소 표시줄, 기록, 책갈피, 브라우저 탭에서 이 이미지를 사용할 수 있습니다.

### <a name="browser-colors"></a>브라우저 색

페이지 헤더의 색과 단추 및 기타 강조 표시에 사용되는 색을 변경할 수 있습니다. `##ff6347` 형식의 6자 16진수 색상 값을 사용합니다. **16진수 값** 색상 표기법에 대한 자세한 내용은 [HTML 색](https://www.w3schools.com/html/html_colors.asp)을 참조하세요.

> [!NOTE]
> 언제든지 **애플리케이션 사용자 지정** 페이지의 기본 옵션으로 되돌릴 수 있습니다.

### <a name="changes-for-operators"></a>운영자의 변경 내용

관리자가 사용자 지정 테마를 만들면 애플리케이션의 운영자 및 다른 사용자가 더 이상 **설정** 에서 테마를 선택할 수 없습니다.

## <a name="replace-help-links"></a>도움말 링크 바꾸기

운영자 및 다른 사용자에게 사용자 지정 도움말 정보를 제공하기 위해 애플리케이션 **도움말** 메뉴에서 링크를 수정할 수 있습니다.

도움말 링크를 수정하려면 **관리** 섹션에서 **도움말 사용자 지정** 페이지로 이동합니다.

![IoT Central 도움말 링크 사용자 지정](./media/howto-customize-ui/help-links.png)

도움말 메뉴에 새 항목을 추가하고 기본 항목을 제거할 수도 있습니다.

![사용자 지정된 IoT Central 도움말](./media/howto-customize-ui/custom-help.png)

> [!NOTE]
> 언제든지 **도움말 사용자 지정** 페이지의 기본 도움말 링크로 되돌릴 수 있습니다.

## <a name="next-steps"></a>다음 단계

이제 IoT Central 애플리케이션에서 UI를 사용자 지정하는 방법에 대해 알아보았으므로 몇 가지 다음 단계를 제안합니다.

- [애플리케이션 관리](./howto-administer.md)
- [대시보드에 타일 추가](howto-manage-dashboards.md)