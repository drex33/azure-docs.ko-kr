---
title: Azure Static Web Apps를 사용하여 프런트 엔드 프레임워크 구성
description: Azure Static Web Apps에 필요한 널리 사용되는 프런트 엔드 프레임워크 설정
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: conceptual
ms.date: 07/18/2020
ms.author: cshoe
ms.openlocfilehash: be5415658de782c362eca0f40a17a4578932aafa
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124777225"
---
# <a name="configure-front-end-frameworks-and-libraries-with-azure-static-web-apps"></a>Azure Static Web Apps를 사용하여 프런트 엔드 프레임워크와 라이브러리 구성

Azure Static Web Apps를 사용하려면 프런트 엔드 프레임워크 또는 라이브러리의 [빌드 구성 파일](build-configuration.md)에 적절한 구성 값이 있어야 합니다.

## <a name="configuration"></a>구성

다음 테이블에는 일련의 프레임워크와 라이브러리<sup>1</sup> 설정이 나열되어 있습니다.

테이블 열의 의도는 다음 항목에 설명되어 있습니다.

- **출력 위치**: [빌드된 버전의 애플리케이션 파일을 포함하는 폴더](build-configuration.md)인 `output_location`의 값을 나열합니다.

- **사용자 지정 빌드 명령**: 프레임워크에 `npm run build`나 `npm run azure:build`와 다른 명령이 필요하면 [사용자 지정 빌드 명령](build-configuration.md#custom-build-commands)을 정의할 수 있습니다.

| 프레임워크 | 앱 아티팩트 위치 | 사용자 지정 빌드 명령 |
|--|--|--|
| [Alpine.js](https://github.com/alpinejs/alpine/) | `/` | 해당 없음 <sup>2</sup> |
| [Angular](https://angular.io/) | `dist/<APP_NAME>` | `npm run build -- --prod` |
| [Angular Universal](https://angular.io/guide/universal) | `dist/<APP_NAME>/browser` | `npm run prerender` |
| [Aurelia](https://aurelia.io/) | `dist` | 해당 없음 |
| [Backbone.js](https://backbonejs.org/) | `/` | 해당 없음 |
| [Blazor](https://dotnet.microsoft.com/apps/aspnet/web-apps/blazor) | `wwwroot` | 해당 없음 |
| [Ember](https://emberjs.com/) | `dist` | 해당 없음 |
| [Flutter](https://flutter.dev/) | `build/web` | `flutter build web` |
| [Framework7](https://framework7.io/) | `www` | `npm run build-prod` |
| [Glimmer](https://glimmerjs.com/) | `dist` | 해당 없음 |
| [HTML](https://developer.mozilla.org/docs/Web/HTML) | `/` | 해당 없음 |
| [Hyperapp](https://hyperapp.dev/) | `/` | 해당 없음 |
| [JavaScript](https://developer.mozilla.org/docs/Web/javascript) | `/` | 해당 없음 |
| [jQuery](https://jquery.com/) | `/` | 해당 없음 |
| [KnockoutJS](https://knockoutjs.com/) | `dist` | 해당 없음 |
| [LitElement](https://lit-element.polymer-project.org/) | `dist` | 해당 없음 |
| [Marko](https://markojs.com/) | `public` | 해당 없음 |
| [유성](https://www.meteor.com/) | `bundle` | 해당 없음 |
| [Mithril](https://mithril.js.org/) | `dist` | 해당 없음 |
| [Polymer](https://www.polymer-project.org/) | `build/default` | 해당 없음 |
| [Preact](https://preactjs.com/) | `build` | 해당 없음 |
| [React](https://reactjs.org/) | `build` | 해당 없음 |
| [RedwoodJS](https://redwoodjs.com/) | `web/dist` | `yarn rw build` |
| [Stencil](https://stenciljs.com/) | `www` | 해당 없음 |
| [Svelte](https://svelte.dev/) | `public` | 해당 없음 |
| [Three.js](https://threejs.org/) | `/` | 해당 없음 |
| [TypeScript](https://www.typescriptlang.org/) | `dist` | 해당 없음 |
| [Vue.js](https://vuejs.org/) | `dist` | 해당 없음 |

<sup>1</sup> 위의 테이블은 Azure Static Web Apps와 작동하는 프레임워크와 라이브러리의 전체 목록이 아닙니다.

<sup>2</sup> 해당 사항 없음

## <a name="next-steps"></a>다음 단계

- [빌드 및 워크플로 구성](build-configuration.md)
