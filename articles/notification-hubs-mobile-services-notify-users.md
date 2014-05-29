<properties linkid="notification-hubs-how-to-guides-howto-notify-users-mobileservices" urlDisplayName="通知用户" pageTitle="使用通知中心通知用户移动服务事件" metaKeywords="" description="遵循此教程使用通知中心注册以接收来自移动服务的通知" metaCanonical="" services="mobile-services,notification-hubs" documentationCenter="" title="使用通知中心通知用户" authors="glenga" solutions="" manager="" editor="" />
# <a name="getting-started"> </a>使用通知中心通知用户

<div class="dev-center-tutorial-selector sublanding">
    <a href="/zh-cn/manage/services/notification-hubs/notify-users" title="移动服务" class="current">移动服务</a><a href="/zh-cn/manage/services/notification-hubs/notify-users-aspnet" title="ASP.NET">ASP.NET</a>
</div> 

本教程演示如何使用 Azure 通知中心将推送通知发送到特定设备上的特定应用程序用户。使用 Azure 移动服务后端对客户端进行身份验证和生成通知。本教程以您在前面的**通知中心入门**教程中创建的通知中心为基础。将通知注册代码从客户端移到后端服务。这确保仅在客户端已经过服务验证后才完成注册。它还表示通知中心凭据不随客户端应用程序一起分发。服务还控制在注册期间请求的标签。

本教程将指导您完成以下基本步骤：

+ [更新移动服务以注册通知]
+ [更新应用程序以登录和请求注册]
+ [更新移动服务以发送通知]

## 先决条件

在您开始本教程之前，必须先完成以下教程：

+ **通知中心入门**（[Windows 应用商店 C#][Get started Windows Store]/[iOS][Get started iOS]/[Android][Get started Android]）

+ **移动服务中身份验证入门**（[Windows 应用商店 C#][Get started auth Windows Store]/[iOS][Get started auth iOS]/[Android][Get started auth Android]）

本教程以您在**通知中心入门**中创建的应用程序和通知中心为基础。它还利用您在**移动服务中身份验证入门**中配置的经过身份验证的移动服务。

<div class="dev-callout"><b>注意</b>
	<p>默认情况下，<strong>移动服务中身份验证入门</strong>教程使用 Facebook 身份验证。您不能在此教程中使用 Microsoft 帐户身份验证，因为两个 Windows 应用商店应用程序无法共享单个 Live Connect 注册。要使用 Microsoft 帐户身份验证，移动服务和通知中心必须注册到 Live Connect 中的同一应用程序。</p>
</div>

<h2><a name="register-notification"></a><span class="short-header">注册通知</span>更新移动服务以注册通知</h2>

因为通知注册只能在客户端已经过服务的身份验证后才能完成，由在移动服务中定义的自定义 API 来执行注册。经过身份验证的客户端调用这个自定义 API 来请求注册通知。在此节中，您将更新经过身份验证的移动服务，该服务是在您完成**移动服务中身份验证入门**教程时定义的。

1. 登录到 [Azure 管理门户][Management Portal]，依次单击 **Service Bus**、您的命名空间和“通知中心”，然后选择您的通知中心并单击“连接信息”。

	![][6]

2. 记录通知中心的名称并复制 **DefaultFullSharedAccessSignature** 的连接字符串。

	![][7]

	您将使用此连接字符串和通知中心名称来注册和发送通知。

2. 仍是在管理门户中，单击“移动服务”，然后单击您的应用程序。

   	![][0]

2. 单击 **API** 选项卡，然后单击“创建自定义 API”。

   	![][1]

   	这将显示“创建新的自定义 API”对话框。

3. 在“API 名称”中键入 <em>register_notifications</em>，为“POST 权限”选择“仅经过身份验证的用户”，然后单击选中按钮。

   	![][2]

  	这将创建 API，要求在使用 HTTP POST 方法调用该 API 前对用户进行身份验证。我们不需要设置其他 HTTP 方法，因为我们不实现它们。

4. 单击 API 表中新的 **register_notifications** 条目。

	![][3]

5. 单击“脚本”选项卡，将现有代码替换为以下代码：

		exports.post = function(request, response) {

			// Create a notification hub instance.
		    var azure = require('azure');
		    var hub = azure.createNotificationHubService('<NOTIFICATION_HUB_NAME>', 
				'<FULL_SAS_CONNECTION_STRING>');
		
		    // Get the registration info that we need from the request. 
		    var platform = request.body.platform;
		    var userId = request.user.userId;
		    var installationId = request.header('X-ZUMO-INSTALLATION-ID');
		    
		    // Function called when registration is completed.
		    var registrationComplete = function(error, registration) {
		        if (!error) {
		            // Return the registration.
		            response.send(200, registration);
		        } else {
		            response.send(500, 'Registration failed!');
		        }
		    }
		    // Function called to log errors.
		    var logErrors = function(error) {
		        if (error) {
		            console.error(error)
		        }
		    }
		    // Get existing registrations.
		    hub.listRegistrationsByTag(installationId, function(error, existingRegs) {
		        var firstRegistration = true;
		        if (existingRegs.length > 0) {
		             for (var i = 0; i < existingRegs.length; i++) {
		                if (firstRegistration) {
		                    // Update an existing registration.
		                    if (platform === 'win8') {
		                        existingRegs[i].ChannelUri = request.body.channelUri;                        
		                        hub.updateRegistration(existingRegs[i], registrationComplete);                        
		                    } else if (platform === 'ios') {
		                        existingRegs[i].DeviceToken = request.body.deviceToken;
		                        hub.updateRegistration(existingRegs[i], registrationComplete);
		                    } else {
		                        response.send(500, 'Unknown client.');
		                    }
		                    firstRegistration = false;
		                } else {
		                    // We shouldn't have any extra registrations; delete if we do.
		                    hub.deleteRegistration(existingRegs[i].RegistrationId, logErrors);
		                }
		            }
                } else {
                    // Create a new registration.
                    if (platform === 'win8') {                
                        hub.wns.createNativeRegistration(request.body.channelUri, 
                        [userId, installationId], registrationComplete);
                    } else if (platform === 'ios') {
                        hub.apns.createNativeRegistration(request.body.deviceToken, 
                        [userId, installationId], registrationComplete);
                    } else {
                        response.send(500, 'Unknown client.');
                    }
                }
            });
        }

	此代码从消息正文获取平台和设备 ID 信息。此数据和请求标头中的安装 ID 以及登录用户的用户 ID 一起用于更新注册（如果存在），否则要创建新注册。此注册使用用户 ID 和安装 ID 进行标记。

6. 更新该脚本以将 _`<NOTIFICATION_HUB_NAME>`_ 和 _`<FULL_SAS_CONNECTION_STRING>`_ 替换为用于您通知中心的值，然后单击“保存”。

	<div class="dev-callout"><b>注意</b>
		<p>确保将 <strong>DefaultFullSharedAccessSignature</strong> 用于 <em><code>&lt;FULL_SAS_CONNECTION_STRING&gt;</code></em>。此声明允许您的自定义 API 方法创建和更新注册。</p>
	</div>

<h2><a name="update-app"></a><span class="short-header">更新应用程序</span>更新应用程序以登录和请求注册</h2>

接下来，您需要通过调用新的自定义 API 来更新 TodoList 应用程序以请求注册通知。

1. 根据您的客户端平台，按**通过使用移动服务注册推送通知的当前用户**的以下版本之一中的步骤操作：

	+ [Windows 应用商店 C# 版本][Client topic Windows Store C# version]
	+ [iOS 版本][Client topic iOS version]

2. 运行更新的应用程序，使用 Facebook 登录，然后验证是否显示分配给通知的注册 ID。

<h2><a name="send-notifications"></a><span class="short-header">发送通知</span>更新移动服务以发送通知</h2>

最后一步是添加用于在移动服务中发送通知的代码。将此通知代码添加到注册到 **TodoItem** 表的 insert 处理程序的服务器脚本。

1. 返回管理门户，单击“数据”选项卡，然后单击 **TodoItem** 表。

   	![][4]

2. 在 **todoitem** 中，单击“脚本”选项卡，然后选择“插入”。
   
  	![][5]

   	将显示当 **TodoItem** 表中发生插入时所调用的函数。

3. 将 insert 函数替换为以下代码：

		function insert(item, user, request) {
		    var azure = require('azure');
		    var hub = azure.createNotificationHubService('<NOTIFICATION_HUB_NAME>', 
		    '<FULL_SAS_CONNECTION_STRING>');
		
 		   // Create the payload for a Windows Store app.
		    var wnsPayload = '<toast><visual><binding template="ToastText02"><text id="1">New item added:</text><text id="2">' + item.text + '</text></binding></visual></toast>';
		
		    // Execute the request and send notifications.
		    request.execute({
		        success: function() {
		            // Write the default response and send a notification 
		            // to the user on all devices by using the userId tag.
		            request.respond();
		            // Send to Windows Store apps.
		            hub.wns.send(user.userId, wnsPayload, 'wns/toast', function(error) {
		                if (error) {
		                    console.log(error);
		                }
		            });
		            // Send to iOS apps.
		            hub.apns.send(user.userId, {
		                alert: item.text
		            }, function(error) {
		                if (error) {
		                    console.log(error);
		                }
		            });
		        }
		    });
		}
	
	这尝试将通知发送到 Windows 应用商店和 iOS 应用程序中的当前已注册用户的标签。
		
4. 更新该脚本以将 _`<NOTIFICATION_HUB_NAME>`_ 和 _`<FULL_SAS_CONNECTION_STRING>`_ 替换为用于您通知中心的值，然后单击“保存”。

   	此操作将注册一个新插入脚本，该脚本使用通知中心将推送通知（插入的文本）发送到插入请求中提供的通道。

	<div class="dev-callout"><b>注意</b>
		<p>确保将 <strong>DefaultFullSharedAccessSignature</strong> 用于 <em><code>&lt;FULL_SAS_CONNECTION_STRING&gt;</code></em>。此声明为您的插入脚本提供完全访问权限，包括可将通知发送到注册的客户端。</p>
	</div>

<h2><a name="test"></a><span class="short-header">测试应用程序</span>在应用程序中测试推送通知</h2>

现在您已配置了通知，可以通过插入数据来生成通知，测试应用程序了。

1. 运行应用程序。

	启动时再次显示注册通知。

2. 输入和以前一样的文本并添加新项。

	请注意，完成插入后，应用程序将收到来自通知中心的推送通知。

	<div class="dev-callout"><b>注意</b>
		<p>当缺少注册的平台（请求将通知发送到该平台）时，会在后端引发错误。在本示例中，可以忽略此错误。要了解如何使用模板来避免此情况，请参见<a href="/zh-cn/manage/services/notification-hubs/notify-users-xplat-mobile-services/" target="_blank">使用通知中心向用户发送跨平台通知</a>。</p>
	</div>

3. （可选）将客户端应用程序部署到第二个设备，然后运行该应用程序并插入文本。

	通知将显示在每个设备上。

## <a name="next-steps"> </a>后续步骤
现在您已完成本教程，请考虑继续学习以下教程：

+ **使用通知中心发送突发新闻（[Windows 应用商店 C#][Breaking news .NET] / [iOS][Breaking news iOS]）**<br/>此平台特定的教程演示如何使用标签来允许用户订阅他们感兴趣的通知类型。

+ **[使用通知中心向用户发送跨平台通知]**<br/>此教程是对当前**使用通知中心通知用户**教程的扩展，以使用平台特定的模板来注册通知。这允许您使用服务器端代码中的单个方法来发送通知。

有关通知中心的更多信息，请参见 [Azure 通知中心]。

<!-- Anchors. -->
[更新移动服务以注册通知]: #register-notification
[更新应用程序以登录和请求注册]: #update-app
[更新移动服务以发送通知]: #send-notifications

<!-- Images. -->
[0]: ./media/notification-hubs-mobile-services-notify-users/mobile-services-selection.png
[1]: ./media/notification-hubs-mobile-services-notify-users/mobile-custom-api-create.png
[2]: ./media/notification-hubs-mobile-services-notify-users/mobile-custom-api-create2.png
[3]: ./media/notification-hubs-mobile-services-notify-users/mobile-custom-api-select.png
[4]: ./media/notification-hubs-mobile-services-notify-users/mobile-portal-data-tables.png
[5]: ./media/notification-hubs-mobile-services-notify-users/mobile-insert-script-push2.png
[6]: ./media/notification-hubs-mobile-services-notify-users/notification-hub-select-hub-connection.png
[7]: ./media/notification-hubs-mobile-services-notify-users/notification-hub-connection-strings.png

<!-- URLs. -->
[Get started Windows Store]: /zh-cn/manage/services/notification-hubs/getting-started-windows-dotnet
[Get started iOS]: /zh-cn/manage/services/notification-hubs/get-started-notification-hubs-ios
[Get started Android]: /zh-cn/manage/services/notification-hubs/get-started-notification-hubs-android
[Get started auth Windows Store]: /zh-cn/develop/mobile/tutorials/get-started-with-users-dotnet/
[Get started auth iOS]: /zh-cn/develop/mobile/tutorials/get-started-with-users-ios/
[Get started auth Android]: /zh-cn/develop/mobile/tutorials/get-started-with-users-android/
[Client topic Windows Store C# version]: /zh-cn/manage/services/notification-hubs/register-users-mobile-services-dotnet 
[Client topic iOS version]: /zh-cn/manage/services/notification-hubs/register-users-ios 
[Visual Studio 2012 Express for Windows 8]: http://go.microsoft.com/fwlink/?LinkId=257546

[Management Portal]: https://manage.windowsazure.com/
[使用通知中心向用户发送跨平台通知]: /zh-cn/manage/services/notification-hubs/notify-users-xplat-mobile-services
[Breaking news .NET]: /zh-cn/manage/services/notification-hubs/breaking-news-dotnet
[Breaking news iOS]: /zh-cn/manage/services/notification-hubs/breaking-news-ios
[Azure 通知中心]: http://msdn.microsoft.com/zh-cn/library/windowsazure/jj927170.aspx

