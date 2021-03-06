---
title: "모바일 앱을 사용하여 Apache Cordova에 인증 추가 | Microsoft Docs"
description: "Azure 앱 서비스에서 모바일 앱을 사용하여 Google, Facebook, Twitter, Microsoft를 비롯한 다양한 ID 공급자를 통해 Apache Cordova 앱의 사용자를 인증하는 방법을 알아봅니다."
services: app-service\mobile
documentationcenter: javascript
author: adrianhall
manager: erikre
editor: 
ms.assetid: 10dd6dc9-ddf5-423d-8205-00ad74929f0d
ms.service: app-service-mobile
ms.workload: na
ms.tgt_pltfrm: mobile-html
ms.devlang: javascript
ms.topic: article
ms.date: 10/01/2016
ms.author: adrianha
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: a5da863c7cbd5760a15f5e72fa7a884973ed38af


---
# <a name="add-authentication-to-your-apache-cordova-app"></a>Apache Cordova 앱에 인증 추가
[!INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

## <a name="summary"></a>요약
이 자습서에서는 지원되는 ID 공급자를 사용하여 Apache Cordova의 할 일 모음 빠른 시작 프로젝트에 인증을 추가합니다. 이 자습서는 [모바일 앱 시작] 자습서를 기반으로 하며 이를 먼저 완료해야 합니다.

## <a name="a-nameregisteraregister-your-app-for-authentication-and-configure-the-app-service"></a><a name="register"></a>인증을 위해 앱 등록 및 앱 서비스 구성
[!INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

[유사한 단계를 보여 주는 비디오 보기](https://channel9.msdn.com/series/Azure-connected-services-with-Cordova/Azure-connected-services-task-8-Azure-authentication)

## <a name="a-namepermissionsarestrict-permissions-to-authenticated-users"></a><a name="permissions"></a>사용 권한을 인증된 사용자로 제한
[!INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

이제 백 엔드에 대한 익명 액세스가 비활성화되었는지 확인할 수 있습니다. Visual Studio에서 [모바일 앱 시작]자습서를 완료했을 때 만든 프로젝트를 연 다음 **Google Android 에뮬레이터** 에서 응용 프로그램을 실행하고 앱이 시작된 후 예기치 않은 연결 오류가 표시되는지 확인합니다.

다음으로 앱을 업데이트하여 모바일 앱 백 엔드에서 리소스를 요청하기 전에 사용자를 인증하도록 합니다.

## <a name="a-nameadd-authenticationaadd-authentication-to-the-app"></a><a name="add-authentication"></a>앱에 인증 추가
1. **Visual Studio**에서 프로젝트를 연 다음 편집을 위해 `www/index.html` 파일을 엽니다.
2. 헤드 섹션에서 `Content-Security-Policy` META 태그를 찾습니다.  허용된 원본 목록에 OAuth 호스트를 추가해야 합니다.
   
   | 공급자 | SDK 공급자 이름 | OAuth 호스트 |
   |:--- |:--- |:--- |
   | Azure Active Directory |aad |https://login.windows.net |
   | Facebook |Facebook |https://www.facebook.com |
   | Google |Google |https://accounts.google.com |
   | Microsoft |microsoftaccount |https://login.live.com |
   | Twitter |Twitter |https://api.twitter.com |
   
    다음은 Content-Security-Policy(Azure Active Directory용으로 구현됨) 예제입니다.
   
        <meta http-equiv="Content-Security-Policy" content="default-src 'self'
            data: gap: https://login.windows.net https://yourapp.azurewebsites.net; style-src 'self'">
   
    `https://login.windows.net` 을 위 표의 OAuth 호스트로 바꿔야 합니다.  이 Meta 태그에 대한 자세한 내용은 [Content-Security-Policy 설명서] 를 참조하세요.
   
    일부 인증 공급자에서는 적절한 모바일 장치에서 사용하는 경우 Content-Security-Policy 변경이 필요하지 않습니다.  예를 들어 Android 장치에서 Google 인증을 사용하는 경우 Content-Security-Policy를 변경하지 않아도 됩니다.
3. 편집을 위해 `www/js/index.js` 파일을 열고 `onDeviceReady()` 메서드를 찾아 클라이언트 생성 코드에서 다음을 추가합니다.
   
        // Login to the service
        client.login('SDK_Provider_Name')
            .then(function () {
   
                // BEGINNING OF ORIGINAL CODE
   
                // Create a table reference
                todoItemTable = client.getTable('todoitem');
   
                // Refresh the todoItems
                refreshDisplay();
   
                // Wire up the UI Event Handler for the Add Item
                $('#add-item').submit(addItemHandler);
                $('#refresh').on('click', refreshDisplay);
   
                // END OF ORIGINAL CODE
   
            }, handleError);
   
    테이블 참조를 만들고 UI를 새로 고치는 기존 코드를 이 코드로 바꿉니다.
   
    login() 메서드는 공급자를 사용하여 인증을 시작합니다. login() 메서드는 JavaScript 프라미스를 반환하는 비동기 함수입니다.  초기화의 나머지는 login() 메서드가 완료될 때까지 실행되지 않도록 프라미스 응답 안에 배치됩니다.
4. 방금 추가한 코드에서 `SDK_Provider_Name` 를 로그인 공급자 이름으로 바꿉니다. 예를 들어 Azure Active Directory의 경우 `client.login('aad')`를 사용합니다.
5. 프로젝트를 실행합니다.  프로젝트 초기화가 완료되면 응용 프로그램에서 선택한 인증 공급자에 대한 OAuth 로그인 페이지를 표시합니다.

## <a name="a-namenext-stepsanext-steps"></a><a name="next-steps"></a>다음 단계
* Azure 앱 서비스의 [인증 정보] 에 대해 자세히 알아봅니다.
* [푸시 알림] 을 Apache Cordova 앱에 추가하여 자습서를 계속합니다.

SDK 사용 방법을 알아봅니다.

* [Apache Cordova SDK]
* [ASP.NET 서버 SDK]
* [Node.js 서버 SDK]

<!-- URLs. -->
[모바일 앱 시작]: app-service-mobile-cordova-get-started.md
[Content-Security-Policy 설명서]: https://cordova.apache.org/docs/en/latest/guide/appdev/whitelist/index.html
[푸시 알림]: app-service-mobile-cordova-get-started-push.md
[인증 정보]: app-service-mobile-auth.md
[Apache Cordova SDK]: app-service-mobile-cordova-how-to-use-client-library.md 
[ASP.NET 서버 SDK]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[Node.js 서버 SDK]: app-service-mobile-node-backend-how-to-use-server-sdk.md



<!--HONumber=Nov16_HO3-->


