<properties
	pageTitle="Get started with Azure Notification Hubs | Microsoft Azure"
	description="In this tutorial, you learn how to use Azure Notification Hubs to push notifications to a Windows Store or Windows Phone 8.1 (non-Silverlight) application."
	services="notification-hubs"
	documentationCenter="windows"
	authors="wesmc7777"
	manager="dwrede"
	editor="dwrede"/>

<tags
	ms.service="notification-hubs"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-windows"
	ms.devlang="dotnet"
	ms.topic="hero-article"
	ms.date="08/24/2015"
	ms.author="wesmc"/>

# Getting started with Notification Hubs

[AZURE.INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

##Overview

This tutorial shows you how to use Azure Notification Hubs to send push notifications to a Windows Store or Windows Phone 8.1 (non-Silverlight) application. If you are targeting Windows Phone 8.1 Silverlight, please refer to the [Windows Phone](notification-hubs-windows-phone-get-started.md) version.
In this tutorial, you create a blank Windows Store app that receives push notifications by using the Windows Push Notification Service (WNS). When you're finished, you'll be able to use your notification hub to broadcast push notifications to all the devices running your app.

This tutorial demonstrates a simple broadcast scenario in using Notification Hubs. Be sure to follow along with the next tutorial to learn how to use Notification Hubs to address specific users and groups of devices.


##Prerequisites

This tutorial requires the following:

+ Microsoft Visual Studio Express 2013 for Windows with Update 2<br/>This version of Visual Studio is required to create a universal app project. If you just want to create a Windows Store app, you need Visual Studio 2012 Express for Windows 8.

+ An active Windows Store account

+ An active Azure account <br/>If you don't have an account, you can create a free trial account in just a couple of minutes. For details, see [Azure Free Trial](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fnotification-hubs-windows-store-dotnet-get-started%2F).

Completing this tutorial is a prerequisite for all other Notification Hubs tutorials for Windows Store apps.

##Register your app for the Windows Store

To send push notifications to Windows Store apps, you must associate your app to the Windows Store. You must then configure your notification hub to integrate with WNS.

1. If you have not already registered your app, navigate to the <a href="http://go.microsoft.com/fwlink/p/?LinkID=266582" target="_blank">Windows Dev Center</a>, sign in with your Microsoft account, and then click **Create a new app**.


2. Type a name for your app and click **Reserve app name**.

   	![](./media/notification-hubs-windows-store-dotnet-get-started/notification-hubs-win8-app-name.png)

   	This creates a new Windows Store registration for your app.

3. In Visual Studio, create a new Visual C# Store Apps project by using the **Blank App** template.

   	![][2]

4. In Solution Explorer, right-click the Windows Store app project, click **Store**, and then click **Associate App with the Store...**.

   	![][3]

   	The **Associate Your App with the Windows Store** wizard appears.

5. In the wizard, click **Sign in** and then sign in with your Microsoft account.

6. Click the app that you registered in step 2, click **Next**, and then click **Associate**.

   	![][4]

   	This adds the required Windows Store registration information to the application manifest.

7. (Optional) Repeat steps 4–6 for the Windows Phone Store app project.  

8. Back on the Windows Dev Center page for your new app, click **Services**, click **Push notifications**, and then click **Live Services site** under **Windows Push Notification Services (WNS) and Microsoft Azure Mobile Services**.

   	![](./media/notification-hubs-windows-store-dotnet-get-started/notification-hubs-win8-app-live-services.png)

9. On the **App Settings** tab, make a note of the values of **Client secret** and **Package security identifier (SID)**.

   	![][6]

 	> [AZURE.WARNING]
	The client secret and package SID are important security credentials. Do not share these values with anyone or distribute them with your app.

##Configure your notification hub

1. Sign in to the [Azure portal], and then click **NEW** at the bottom of the screen.

2. Click **App Services**, click **Service Bus**, click **Notification Hub**, and then click **Quick Create**.

   	![][7]

3. Type a name for your notification hub, select your desired region, and then click **Create a New Notification Hub**.

   	![][8]

4. Click the namespace that you just created (usually ***notification hub name*-ns**), and then click the **Configure** tab at the top.

   	![][9]

5. Select the **Notification Hubs** tab at the top, and then click the notification hub that you just created.

   	![][10]

6. Select the **Configure** tab at the top, enter the **Client secret** and **Package SID** values that you obtained from WNS in the previous section, and then click **Save**.

   	![][11]

7. Select the **Dashboard** tab at the top, and then click the **Connection Information** button at the bottom of the page. Take note of the two connection strings.

   	![][12]

Your notification hub is now configured to work with WNS, and you have the connection strings to register your app and send notifications.

##Connect your app to the notification hub

1. In Visual Studio, right-click the solution, and then click **Manage NuGet Packages**.

	This displays the **Manage NuGet Packages** dialog box.

2. Search for `WindowsAzure.Messaging.Managed` and click **Install**, select all projects in the solution, and accept the terms of use.

	![][20]

	This downloads, installs, and adds a reference in all projects to the Azure Messaging library for Windows by using the <a href="http://nuget.org/packages/WindowsAzure.Messaging.Managed/">WindowsAzure.Messaging.Managed NuGet package</a>.

3. Open the App.xaml.cs project file and add the following `using` statements. In a universal project, this file is located in the `<project_name>.Shared` folder.

        using Windows.Networking.PushNotifications;
        using Microsoft.WindowsAzure.Messaging;
		using Windows.UI.Popups;



4. Also in App.xaml.cs, add the following **InitNotificationsAsync** method definition to the **App** class:

	    private async void InitNotificationsAsync()
        {
            var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();

            var hub = new NotificationHub("<hub name>", "<connection string with listen access>");
			var result = await hub.RegisterNativeAsync(channel.Uri);

            // Displays the registration ID so you know it was successful
            if (result.RegistrationId != null)
            {
                var dialog = new MessageDialog("Registration successful: " + result.RegistrationId);
                dialog.Commands.Add(new UICommand("OK"));
                await dialog.ShowAsync();
            }

        }

    This code retrieves the channel URI for the app from WNS, and then registers that channel URI with your notification hub.

    >[AZURE.NOTE]Make sure to replace the "hub name" placeholder with the name of the notification hub that appears in the portal on the **Notification Hubs** tab (for example, **mynotificationhub2** in the previous example). Also replace the connection string placeholder with the **DefaultListenSharedAccessSignature** connection string that you obtained in the previous section.

5. At the top of the **OnLaunched** event handler in App.xaml.cs, add the following call to the new **InitNotificationsAsync** method:

        InitNotificationsAsync();

    This guarantees that the channel URI is registered in your notification hub each time the application is launched.

6. In Solution Explorer, double-click **Package.appxmanifest** of the Windows Store app, and then in **Notifications**, set **Toast capable** to **Yes**:

   	![][18]

   	From the **File** menu, click **Save All**.

7. (Optional) Repeat the previous step in the Windows Phone Store app project.

8. Press the **F5** key to run the app. A pop-up dialog that contains the registration key is displayed.

   	![][19]

9. (Optional) Repeat the previous step to run the other project.

Your app is now ready to receive toast notifications.

##Send the notification from your backend

You can send notifications by using Notification Hubs from any backend via the <a href="http://msdn.microsoft.com/library/windowsazure/dn223264.aspx">REST interface</a>. In this tutorial, you send notifications by using a .NET console application. For an example of how to send notifications from an Azure Mobile Services backend that's integrated with Notification Hubs, see "Get started with push notifications in Mobile Services" ([.NET backend](../mobile-services-javascript-backend-windows-store-dotnet-get-started-push.md) | [JavaScript backend](../mobile-services-javascript-backend-windows-store-dotnet-get-started-push.md)).  For an example of how to send notifications by using the REST APIs, see "How to use Notification Hubs from Java/PHP" ([Java](notification-hubs-java-backend-how-to.md) | [PHP](notification-hubs-php-backend-how-to.md)).

1. Right-click the solution, select **Add** and **New Project...**, and then under **Visual C#**, click **Windows** and **Console Application**, and click **OK**.

   	![][13]

	This adds a new Visual C# console application to the solution. You can also do this in a separate solution.

4. In Visual Studio, click **Tools**, click **NuGet Package Manager**, and then click **Package Manager Console**.

	This displays the Package Manager Console in Visual Studio.

6. In the Package Manager Console window, set the **Default project** to your new console application project, and then in the console window, execute the following command:

        Install-Package Microsoft.Azure.NotificationHubs

	This adds a reference to the Azure Notification Hubs SDK using the <a href="http://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/">Microsoft.Azure.Notification Hubs NuGet package</a>.

5. Open the Program.cs file and add the following `using` statement:

        using Microsoft.Azure.NotificationHubs;

6. In the **Program** class, add the following method:

        private static async void SendNotificationAsync()
        {
            NotificationHubClient hub = NotificationHubClient
				.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
            var toast = @"<toast><visual><binding template=""ToastText01""><text id=""1"">Hello from a .NET App!</text></binding></visual></toast>";
            await hub.SendWindowsNativeNotificationAsync(toast);
        }

   	Make sure to replace the "hub name" placeholder with the name of the notification hub that appears in the portal on the **Notification Hubs** tab. Also, replace the connection string placeholder with the connection string called **DefaultFullSharedAccessSignature** that you obtained in the section "Configure your notification hub."

	>[AZURE.NOTE]Make sure that you use the connection string that has **Full** access, not **Listen** access. The listen-access string does not have permissions to send notifications.

7. Add the following lines in the **Main** method:

         SendNotificationAsync();
		 Console.ReadLine();

8. Right-click the console application project in Visual Studio, and click **Set as StartUp Project** to set it as the startup project. Then press the **F5** key to run the application.

   	![][14]

	You will receive a toast notification on all registered devices. Clicking or tapping the toast banner loads the app.

You can find all the supported payloads in the [toast catalog], [tile catalog], and [badge overview] topics on MSDN.

##Next steps

In this simple example, you sent broadcast notifications to all your Windows devices. In order to target specific users, refer to the tutorial [Use Notification Hubs to push notifications to users]. If you want to segment your users by interest groups, see [Use Notification Hubs to send breaking news]. To learn more about how to use Notification Hubs, see [Notification Hubs Guidance].



<!-- Images. -->
[2]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-create-windows-universal-app.png
[3]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-associate-win8-app.png
[4]: ./media/notification-hubs-windows-store-dotnet-get-started/mobile-services-select-app-name.png
[6]: ./media/notification-hubs-windows-store-dotnet-get-started/mobile-services-win8-app-push-auth.png
[7]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-create-from-portal.png
[8]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-create-from-portal2.png
[9]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-select-from-portal.png
[10]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-select-from-portal2.png
[11]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-configure-wns.png
[12]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-connection-strings.png
[13]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-create-console-app.png
[14]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-windows-toast.png
[15]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-scheduler1.png
[16]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-scheduler2.png
[18]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-win8-app-toast.png
[19]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-windows-reg.png
[20]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-windows-universal-app-install-package.png

<!-- URLs. -->
[Azure portal]: https://manage.windowsazure.com/
[Notification Hubs Guidance]: http://msdn.microsoft.com/library/jj927170.aspx

[Use Notification Hubs to push notifications to users]: notification-hubs-aspnet-backend-windows-dotnet-notify-users.md
[Use Notification Hubs to send breaking news]: notification-hubs-windows-store-dotnet-send-breaking-news.md

[toast catalog]: http://msdn.microsoft.com/library/windows/apps/hh761494.aspx
[tile catalog]: http://msdn.microsoft.com/library/windows/apps/hh761491.aspx
[badge overview]: http://msdn.microsoft.com/library/windows/apps/hh779719.aspx
