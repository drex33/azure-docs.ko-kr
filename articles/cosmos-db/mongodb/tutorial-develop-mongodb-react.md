---
title: Azure의 MongoDB, React 및 Node.js 자습서
description: 이 동영상 기반 자습서 시리즈에서 MongoDB에 사용하는 것과 정확히 동일한 API를 사용하여 Azure Cosmos DB의 React 및 Node.js를 사용하여 MongoDB 앱을 만드는 방법을 알아봅니다.
author: johnpapa
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 08/26/2021
ms.author: jopapa
ms.reviewer: sngun
ms.custom: devx-track-js
ms.openlocfilehash: 5f219d324b2279949208262c2ace291b2590b82f
ms.sourcegitcommit: 03f0db2e8d91219cf88852c1e500ae86552d8249
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2021
ms.locfileid: "123039489"
---
# <a name="create-a-mongodb-app-with-react-and-azure-cosmos-db"></a>React 및 Azure Cosmos DB에서 MongoDB 앱 만들기  
[!INCLUDE[appliesto-mongodb-api](../includes/appliesto-mongodb-api.md)]

이 여러 비디오 자습서는 React 프런트 엔드에서 추적 앱을 만드는 방법을 보여줍니다. 앱에서 서버에 노드 및 Express를 사용하고, [Azure Cosmos DB의 API for MongoDB](mongodb-introduction.md)를 사용하여 구성된 Cosmos 데이터베이스에 연결하고, 앱의 서버 부분에 React 프런트 엔드를 연결합니다. 이 자습서에는 Azure Portal에서 Cosmos DB 포인트 클릭 동작 크기 조정을 수행하는 방법 및 모든 사용자가 자신이 좋아하는 Heroes를 추적할 수 있도록 인터넷에 앱을 배포하는 방법을 보여줍니다. 

[Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/)는 MongoDB와의 유선 프로토콜 호환성을 지원하므로 클라이언트가 MongoDB 대신 Azure Cosmos DB를 사용할 수 있습니다.  

이 다중 파트 자습서에서 다루는 작업은 다음과 같습니다.

> [!div class="checklist"]
> * 소개
> * 프로젝트 설치
> * React를 사용하여 UI 빌드
> * Azure Portal을 사용하여 Azure Cosmos DB 계정 만들기
> * Mongoose를 사용하여 Azure Cosmos DB에 연결
> * React 추가, 앱에 작업 만들기, 업데이트, 추가 및 삭제

Angular를 사용하여 동일한 앱을 빌드하시겠습니까? [Angular 자습서 비디오 시리즈](tutorial-develop-nodejs-part-1.md)를 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항
* [Node.JS](https://www.nodejs.org)

### <a name="finished-project"></a>완료된 프로젝트
[GitHub에서](https://github.com/Azure-Samples/react-cosmosdb) 완성된 애플리케이션을 가져옵니다.

## <a name="introduction"></a>소개 

이 비디오에서는 Burke Holland는 Azure Cosmos DB를 소개하고 이 비디오 시리즈에서 생성된 응용 프로그램을 설명합니다. 

> [!VIDEO https://www.youtube.com/embed/58IflnJbYJc]

## <a name="project-setup"></a>프로젝트 설정

이 비디오에서는 동일한 프로젝트에서 Express 및 React를 설정하는 방법을 보여줍니다. 그런 다음 Burke가 프로젝트에 있는 코드를 설명합니다.

> [!VIDEO https://www.youtube.com/embed/ytFUPStJJds]

## <a name="build-the-ui"></a>UI 빌드

이 비디오에서는 React를 사용하여 애플리케이션의 UI(사용자 인터페이스)를 만드는 방법을 보여줍니다. 

> [!NOTE]
> 이 비디오에서 참조되는 CSS는 [react-cosmosdb GitHub 리포지토리](https://github.com/Azure-Samples/react-cosmosdb/blob/master/src/index.css)에서 찾을 수 있습니다.

> [!VIDEO https://www.youtube.com/embed/SzHzX0fTUUQ]

## <a name="connect-to-azure-cosmos-db"></a>Azure Cosmos DB에 연결

이 비디오는 Azure Portal에서 Azure Cosmos DB 계정을 만들고, MongoDB 및 Mongoose 패키지를 설치하고, Azure Cosmos DB 연결 문자열을 사용하여 새로 만든 계정에 앱을 연결하는 방법을 보여줍니다. 

> [!VIDEO https://www.youtube.com/embed/0U2jV1thfvs]

## <a name="read-and-create-heroes-in-the-app"></a>앱에서 Heroes 읽기 및 만들기

이 비디오는 Postman 및 React UI를 사용하여 해당 메서드를 테스트하는 방법뿐만 아니라 Cosmos 데이터베이스에서 Heroes를 읽고 만드는 방법을 보여줍니다. 

> [!VIDEO https://www.youtube.com/embed/AQK9n_8fsQI] 

## <a name="delete-and-update-heroes-in-the-app"></a>앱에서 Heroes 삭제 및 업데이트

이 비디오는 앱의 Heroes를 삭제하고 업데이트하며 UI에서 업데이트를 표시하는 방법을 보여줍니다. 

> [!VIDEO https://www.youtube.com/embed/YmaGT7ztTQM] 

## <a name="complete-the-app"></a>앱 완성

이 비디오는 앱을 완성하고 백 엔드 API에 대한 UI 연결을 완성하는 방법을 보여줍니다. 

> [!VIDEO https://www.youtube.com/embed/TcSm2ISfTu8]

## <a name="clean-up-resources"></a>리소스 정리

이 응용 프로그램을 계속 사용하지 않으려면 Azure Portal에서 다음 단계에 따라 이 자습서에서 만든 리소스를 모두 삭제합니다. 

1. Azure Portal의 왼쪽 메뉴에서 **리소스 그룹** 을 클릭한 다음 만든 리소스의 이름을 클릭합니다. 
2. 리소스 그룹 페이지에서 **삭제** 를 클릭하고 텍스트 상자에서 삭제할 리소스의 이름을 입력한 다음 **삭제** 를 클릭합니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음 방법에 대해 알아보았습니다.

> [!div class="checklist"]
> * React, 노드, Express 및 Azure Cosmos DB에서 앱 만들기 
> * Azure Cosmos DB 계정 만들기
> * 앱을 Azure Cosmos DB 계정에 연결
> * Postman을 사용하여 앱 테스트
> * 애플리케이션 실행 및 데이터베이스에 Heroes 추가

다음 자습서로 진행하여 Azure Cosmos DB로 MongoDB 데이터를 가져오는 방법을 알아볼 수 있습니다.  

> [!div class="nextstepaction"]
> [Azure Cosmos DB로 MongoDB 데이터 가져오기](../../dms/tutorial-mongodb-cosmos-db.md?toc=%2fazure%2fcosmos-db%2ftoc.json%253ftoc%253d%2fazure%2fcosmos-db%2ftoc.json)

Azure Cosmos DB로 마이그레이션하기 위한 용량 계획을 수행하려고 하나요? 용량 계획을 위해 기존 데이터베이스 클러스터에 대한 정보를 사용할 수 있습니다.
* 기존 데이터베이스 클러스터의 vCore 및 서버 수만 알고 있는 경우 [vCore 또는 vCPU를 사용하여 요청 단위 예측](../convert-vcore-to-request-unit.md)에 대해 읽어보세요. 
* 현재 데이터베이스 워크로드에 대한 일반적인 요청 비율을 알고 있는 경우 [Azure Cosmos DB 용량 계획 도구를 사용하여 요청 단위 예측](estimate-ru-capacity-planner.md)에 대해 읽어보세요.