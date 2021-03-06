---
title: PowerApps 엔터프라이즈에 Twitter API 추가 | Microsoft Docs
description: 조직의 앱 서비스 환경에서 새 Twitter API 만들기 또는 구성
services: ''
suite: powerapps
documentationcenter: ''
author: rajeshramabathiran
manager: dwrede
editor: ''

ms.service: powerapps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/02/2016
ms.author: litran

---
# PowerApps 엔터프라이즈에 새 Twitter API 만들기
> [!IMPORTANT]
> 이 항목은 보관되고 곧 제거될 예정입니다. 새 [PowerApps](https://powerapps.microsoft.com)의 새로운 내용을 살펴보세요.
> 
> * PowerApps에 대해 자세히 알아보고 시작하려면 [PowerApps](https://powerapps.microsoft.com)로 이동합니다.  
> * PowerApps에서 사용 가능한 연결에 대해 자세히 알아보려면 [사용 가능한 연결](https://powerapps.microsoft.com/tutorials/connections-list/)로 이동합니다. 
> 
> 

<!--Archived
Add the Twitter API to your organization's (tenant) app service environment. 

## Create the API in the Azure portal

1. In the [Azure portal](https://portal.azure.com/), sign-in with your work account. For example, sign-in with *yourUserName*@*YourCompany*.com. When you do this, you are automatically signed in to your company subscription. 

2. Select **Browse** in the task bar:  
![][14]  

3. In the list, you can scroll to find PowerApps or type in *powerapps*:  
![][15]  

4. In **PowerApps**, select **Manage APIs**:  
![Browse to registered apis][1]

5. In **Manage APIs**, select **Add** to add the new API:    
![Add API][2]

6. Enter a descriptive **name** for your API.  

7. In **Source**, select **Available APIs** to select the pre-built APIs, and select **Twitter**:  
![select Twitter api][3]

8. Select **Settings - Configure required settings**:    
![configure Twitter API settings][4]

9. Enter the *Consumer Key* and *Consumer Secret* of your Twitter application. If you don't have one, see the "Register a Twitter app for use with PowerApps" section in this topic to create the key and secret values you need.  

    > [AZURE.IMPORTANT] Save the **redirect URL**. You may need this value later in this topic.

10. Select **OK** to complete the steps.

When finished, a new Twitter API is added to your app service environment.


## Optional: Register a Twitter app for use with PowerApps

If you don't have an existing Twitter app with the key and secret values, then use the following steps to create the application, and get the values you need. 

1. Go to [https://apps.twitter.com/](https://apps.twitter.com) and sign in with your twitter account.

2. Select **Create New App**:    
![Twitter apps page][6]

3. In **Create an application**:  

    1. Enter a value for **Name**.  
    2. Enter a value for **Description**.  
    3. Enter a value for **Website**.  
    4. Set the **Callback url** to the redirect URL you received when you added the new Twitter API in the Azure Portal (in this topic).  
    5. Agree to the developer agreement and select **Create your Twitter application**.  

    ![Twitter app create][7]

4. On successful app creation, you are redirected to the app page.

A new Twitter app is created. You can use this app in your Twitter API configuration in the Azure portal. 

## See the REST APIs

[Twitter REST API](../connectors/connectors-create-api-twitter.md) reference.


## Summary and next steps
In this topic, you added the Twitter API to your PowersApps Enterprise. Next, give users access to the API so it can be added to their apps: 

[Add a connection and give users access](powerapps-manage-api-connection-user-access.md)
-->

<!--References-->

[1]: ./media/powerapps-create-api-twitter/browse-to-registered-apis.PNG
[2]: ./media/powerapps-create-api-twitter/add-api.PNG
[3]: ./media/powerapps-create-api-twitter/select-twitter-api.PNG
[4]: ./media/powerapps-create-api-twitter/configure-twitter-api.PNG
[6]: ./media/powerapps-create-api-twitter/twitter-apps-page.PNG
[7]: ./media/powerapps-create-api-twitter/twitter-app-create.PNG
[14]: ./media/powerapps-create-api-sqlserver/browseall.png
[15]: ./media/powerapps-create-api-sqlserver/allresources.png

<!---HONumber=AcomDC_0504_2016-->