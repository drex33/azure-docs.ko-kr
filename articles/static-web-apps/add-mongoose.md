---
title: '자습서: Azure Static Web Apps에서 Mongoose를 사용하여 Cosmos DB의 데이터에 액세스'
description: Azure Static Web Apps API 함수에서 Mongoose를 사용하여 Cosmos DB의 데이터에 액세스하는 방법을 알아봅니다.
author: GeekTrainer
ms.author: chrhar
ms.service: static-web-apps
ms.topic: tutorial
ms.date: 01/25/2021
ms.openlocfilehash: a048937226f979db58996eb4bf996b9f254d9182
ms.sourcegitcommit: 147910fb817d93e0e53a36bb8d476207a2dd9e5e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/18/2021
ms.locfileid: "130132462"
---
# <a name="tutorial-access-data-in-cosmos-db-using-mongoose-with-azure-static-web-apps"></a>자습서: Azure Static Web Apps에서 Mongoose를 사용하여 Cosmos DB의 데이터에 액세스

[Mongoose](https://mongoosejs.com/)는 가장 인기 있는 Node.js용 ODM(개체 문서 매핑) 클라이언트입니다. 데이터 구조를 설계하고 유효성 검사를 적용하는 데 사용할 수 있는 Mongoose는 Mongoose API를 지원하는 데이터베이스와 상호 작용하는 데 필요한 모든 도구를 제공합니다. [Cosmos DB](../cosmos-db/mongodb-introduction.md)는 필요한 Mongoose API를 지원하며 Azure에서 백 엔드 서버 옵션으로 사용할 수 있습니다.

이 자습서에서는 다음 작업 방법을 알아봅니다.

> [!div class="checklist"]
> - Cosmos DB 서버리스 계정 만들기
> - Azure Static Web Apps 만들기
> - 애플리케이션 설정을 업데이트하여 연결 문자열 저장

Azure 구독이 없는 경우 [평가판 계정](https://azure.microsoft.com/free/)을 만듭니다.

## <a name="prerequisites"></a>필수 구성 요소

- [Azure 계정](https://azure.microsoft.com/free/)
- [GitHub 계정](https://github.com/join)

## <a name="sign-in-to-azure"></a>Azure에 로그인

[Azure Portal](https://portal.azure.com)에 로그인합니다.

## <a name="create-a-cosmos-db-serverless-database"></a>Cosmos DB 서버리스 데이터베이스 만들기

[Cosmos DB 서버리스 계정](../cosmos-db/serverless.md)을 만드는 것부터 시작합니다. 서버리스 계정을 사용하면 리소스를 사용하는 경우에만 요금을 지불하고 전체 인프라를 만들 필요가 없습니다.

1. [https://portal.azure.com](https://portal.azure.com)로 이동합니다.
2. **리소스 만들기** 를 클릭합니다.
3. 검색 상자에 **Azure Cosmos DB** 를 입력합니다.
4. **Azure Cosmos DB** 를 클릭합니다.
5. **생성**
6. **Azure Cosmos DB API for MongoDB** 에서 **만들기** 를 선택합니다.
7. 다음 정보를 사용하여 Azure Cosmos DB 계정을 구성합니다.
    - 구독: 사용하려는 구독 선택
    - 리소스: **새로 만들기** 를 클릭하고 이름을 **aswa-mongoose** 로 설정합니다.
    - 계정 이름: 고유한 값이 필요합니다.
    - 위치: **미국 서부 2**
    - 용량 모드: **서버리스(미리 보기)**
    - 버전: **4.0**
:::image type="content" source="media/add-mongoose/cosmos-db.png" alt-text="새 Cosmos DB 인스턴스 만들기":::
7. **검토 + 만들기** 를 클릭합니다.
8. **생성**

만들기 프로세스는 몇 분 정도 걸립니다. 이후 단계에서는 데이터베이스로 돌아가 연결 문자열을 수집합니다.

## <a name="create-a-static-web-app"></a>정적 웹앱 만들기

이 자습서에서는 애플리케이션을 만드는 데 도움이 되는 GitHub 템플릿 리포지토리를 사용합니다.

1. [시작 템플릿](https://github.com/login?return_to=/staticwebdev/mongoose-starter/generate)으로 이동합니다.
2. **소유자** 를 선택합니다(기본 계정이 아닌 조직을 사용하는 경우).
3. 리포지토리에 **aswa-mongoose-tutorial** 이라는 이름을 지정합니다.
4. **템플릿에서 리포지토리 만들기** 를 클릭합니다.
5. [Azure Portal](https://portal.azure.com)로 돌아갑니다.
6. **리소스 만들기** 를 클릭합니다.
7. 검색 상자에 **정적 웹앱** 을 입력합니다.
8. **Static Web App** 을 선택합니다.
9. **생성**
10. 다음 정보를 사용하여 Azure 정적 웹앱을 구성합니다.
    - 구독: 이전과 동일한 구독을 선택합니다.
    - 리소스 그룹: **aswa-mongoose** 를 선택합니다.
    - 이름: **aswa-mongoose-tutorial**
    - 지역: **미국 서부 2**
    - **GitHub로 로그인** 을 클릭합니다.
    - Azure Static Web Apps가 GitHub Action을 만들어 배포를 지원하도록 허용할지 묻는 메시지가 표시되면 **권한 부여** 를 클릭합니다.
    - 조직: GitHub 계정 이름
    - 리포지토리: **aswa-mongoose-tutorial**
    - 분기: **main**
    - 빌드 사전 설정: **사용자 지정** 을 선택합니다.
    - 앱 위치: **/public**
    - API 위치: **api**
    - 출력 위치: *빈 상태로 둡니다.*
    :::image type="content" source="media/add-mongoose/azure-static-web-apps.png" alt-text="완성된 Azure Static Web Apps 양식":::
11. **검토 후 만들기** 를 클릭합니다.
12. **생성**
13. 만들기 프로세스는 몇 분 정도 걸립니다. 정적 웹앱이 프로비전되면 **리소스로 이동** 을 클릭합니다.

## <a name="configure-database-connection-string"></a>데이터베이스 연결 문자열 구성

웹앱이 데이터베이스와 통신할 수 있도록 데이터베이스 연결 문자열이 [애플리케이션 설정](application-settings.md)으로 저장됩니다. 설정 값은 Node.js에서 `process.env` 개체를 사용하여 액세스할 수 있습니다.

1. Azure Portal의 왼쪽 상단 모서리에서 **홈** 을 클릭합니다(또는 [https://portal.azure.com](https://portal.azure.com)으로 다시 이동).
2. **리소스 그룹** 을 클릭합니다.
3. **aswa-mongoose** 를 클릭합니다.
4. 데이터베이스 계정의 이름을 클릭합니다. **Azure Cosmos DB API for Mongo DB** 유형이 있습니다.
5. **설정** 에서 **연결 문자열** 을 클릭합니다.
6. **기본 연결 문자열** 에 나열된 연결 문자열을 복사합니다.
7. 이동 경로에서 **aswa-mongoose** 를 클릭합니다.
8. **aswa-mongoose-tutorial** 을 클릭하여 웹 사이트 인스턴스로 돌아갑니다.
9. **설정** 에서 **구성** 을 클릭합니다.
10. **추가** 를 클릭하고 다음 값을 사용하여 새 애플리케이션 설정을 만듭니다.
    - 이름: **CONNECTION_STRING**
    - 값: 앞에서 복사한 Storage 연결 문자열을 붙여넣습니다.
11. **확인** 을 클릭합니다.
12. 페이지 맨 아래에 있는 **저장**

## <a name="navigate-to-your-site"></a>사이트로 이동합니다.

이제 정적 웹앱을 살펴볼 수 있습니다.

1. **개요** 를 클릭합니다.
1. 오른쪽 상단에 표시되는 URL을 클릭합니다.
    1. `https://calm-pond-05fcdb.azurestaticapps.net`과 유사합니다.
1. **작업 목록을 보려면 [로그인 하세요]** 를 클릭합니다.
1. **동의 부여** 를 클릭하여 애플리케이션에 액세스합니다.
1. 제목을 입력하고 **작업 추가** 를 클릭하여 새 작업을 만듭니다.
1. 작업이 표시되는지 확인합니다(잠시 시간이 걸릴 수 있음).
1. **확인란을 클릭** 하여 작업을 완료 상태로 표시합니다.
1. **페이지를 새로 고쳐** 데이터베이스가 사용되고 있는지 확인합니다.

## <a name="clean-up-resources"></a>리소스 정리

이 애플리케이션을 계속 사용하지 않으려면 다음 단계에 따라 리소스 그룹을 삭제합니다.

1. [Azure Portal](https://portal.azure.com)로 돌아갑니다.
2. **리소스 그룹** 을 클릭합니다.
3. **aswa-mongoose** 를 클릭합니다.
4. **리소스 그룹 삭제** 를 클릭합니다.
5. 텍스트 상자에 **aswa-mongoose** 를 입력합니다.
6. **삭제** 를 클릭합니다.

## <a name="next-steps"></a>다음 단계

로컬 개발을 구성하는 방법에 대한 다음 문서를 진행하세요.
> [!div class="nextstepaction"]
> [로컬 개발 설정](./local-development.md)
