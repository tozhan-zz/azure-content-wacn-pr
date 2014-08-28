
1. If you have not already registered your app, navigate to the [Submit an app page] at the Dev Center for Windows Store apps, log on with your Microsoft account, and then click **App name**.

   	![](./media/mobile-services-register-windows-store-app/mobile-services-submit-win8-app.png)

2. Type a name for your app in **App name**, click **Reserve app name**, and then click **Save**.

   	![](./media/mobile-services-register-windows-store-app/mobile-services-win8-app-name.png)

   	This creates a new Windows Store registration for your app.

3. In Visual Studio, open the project that you created when you completed the tutorial [Get started with Mobile Services].

4. In solution explorer, right-click the project, click **Store**, and then click **Associate App with the Store...**. 

  	![](./media/mobile-services-register-windows-store-app/mobile-services-store-association.png)

   	This displays the **Associate Your App with the Windows Store** Wizard.

5. In the wizard, click **Sign in** and then login with your Microsoft account.

6. Select the app that you registered in step 2, click **Next**, and then click **Associate**.

   	![](./media/mobile-services-register-windows-store-app/mobile-services-select-app-name.png)

   	This adds the required Windows Store registration information to the application manifest.    

7. Back in the Windows Dev Center page for your new app, click **Services**. 

   	![](./media/mobile-services-register-windows-store-app/mobile-services-win8-edit-app.png) 

8. In the Services page, click **Live Services site** under **Azure Mobile Services**.

	![](./media/mobile-services-register-windows-store-app/mobile-services-win8-edit2-app.png) 

9. In **App settings**, make a note of the values of **Client ID**, **Client secret**, and **Package security identifier (SID)**. 

   	![](./media/mobile-services-register-windows-store-app/mobile-services-win8-app-push-auth.png)

    >[WACOM.NOTE]The client secret and package SID are important security credentials. Do not share these secrets with anyone or distribute them with your app.

10. (Optional) Click **API Settings**, enable **Enhanced redirection security**, supply a value of `https://<mobile_service>.azure-mobile.net/login/microsoftaccount` in **Redirect URL**, then click **Save**.

	![](./media/mobile-services-register-windows-store-app/mobile-services-win8-app-push-auth-2.png)

	This enables Microsoft Account authentication for your app.

10. Log on to the [Azure Management Portal], click **Mobile Services**, and then click your app.

   	![](./media/mobile-services-register-windows-store-app/mobile-services-selection.png)

11. Click the **Push** tab, enter the **Client secret** and **Package SID** values obtained from WNS in Step 4, and then click **Save**.

   	![](./media/mobile-services-register-windows-store-app/mobile-push-tab.png)
 
You are now ready to use a Microsoft Account for authentication in your app by providing the client ID and client secret values to Mobile Services in the **Identity** tab.  

<!-- Anchors. -->

<!-- Images. -->
 

<!-- URLs. -->
[Get started with Mobile Services]: /en-us/develop/mobile/tutorials/get-started/#create-new-service
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[Azure Management Portal]: https://manage.windowsazure.com/
