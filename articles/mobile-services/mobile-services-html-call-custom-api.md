<properties
	pageTitle="Call a custom API from an HTML client - Mobile Services"
	description="Learn how to define a custom API and then call it from an HTML app that uses Azure Mobile Services."
	services="mobile-services"
	documentationCenter=""
	authors="ggailey777"  
	manager="dwrede"
	editor=""/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-html" 
	ms.devlang="javascript"
	ms.topic="article"
	ms.date="06/16/2015"
	ms.author="glenga"/>

# Call a custom API from an HTML application

[AZURE.INCLUDE [mobile-services-selector-call-custom-api](../../includes/mobile-services-selector-call-custom-api.md)]

This topic shows you how to call a custom API from an HTML application. A custom API enables you to define custom endpoints that expose server functionality that does not map to an insert, update, delete, or read operation. By using a custom API, you can have more control over messaging, including reading and setting HTTP message headers and defining a message body format other than JSON.

The custom API created in this topic gives you the ability to send a single POST request that sets the completed flag to `true` for all the todo items in the table. Without this custom API, the client would have to send individual requests to update the flag for each todo item in the table.

This tutorial is based on the Mobile Services quickstart. Before you start this tutorial, you must first complete [Get started with Mobile Services] or [Add Mobile Services to an existing app].

## <a name="define-custom-api"></a>Define the custom API

[AZURE.INCLUDE [mobile-services-create-custom-api](../../includes/mobile-services-create-custom-api.md)]

##<a name="update-app"></a>Update the app to call the custom API

1. Using your text editor, open the index.html file, locate the **button** element named `buttonRefresh`, and add the following new element right after it:

		<button id="buttonCompleteAll">Complete All</button>

	This adds a new button to the page.

2. In page.js, and after the **refreshTodoItems** function, add the following code:

		var completeAllTodoItems = function () {
			// Asynchronously call the custom API using the POST method.
			client.invokeApi("completeall", {
				body: null,
				method: "post"
			}).done(function (results) {
				var message = results.result.count + " item(s) marked as complete.";
				alert(message);
				refreshTodoItems();
			}, function(error) {
				alert(error.message);
			});
		};

		$('#buttonCompleteAll').click(function () {
			completeAllTodoItems();
		});

	This method handles the **Click** event for the new button. The **invokeApi** method is called on the client, which sends a POST request to the new custom API. The result returned by the custom API is displayed in a message dialog, as are any errors.

## <a name="test-app"></a>Test the app

1. Refresh your browser.

2. In the app, type some text in **Insert a TodoItem**, then click **Save**.

3. Repeat the previous step until you have added several todo items to the list.

4. Click the **Complete All** button. A message dialog is displayed that indicates the number of items marked complete and the filtered query is executed again, which clears all items from the list.

## Next steps

This topic showed how to use the **invokeApi** function to call a fairly simple custom API from your HTML/JavaScript app. To learn more about using the **invokeApi** function, see the post [Custom API in Azure Mobile Services](http://blogs.msdn.com/b/carlosfigueira/archive/2013/06/19/custom-api-in-azure-mobile-services-client-sdks.aspx).  

Also, consider finding out more about the following Mobile Services topics:

* [Mobile Services server script reference]
  <br/>Learn more about creating custom APIs.

* [Store server scripts in source control]
  <br/> Learn how to use the source control feature to more easily and securely develop and publish custom API script code.

<!-- Anchors. -->
[Define the custom API]: #define-custom-api
[Update the app to call the custom API]: #update-app
[Test the app]: #test-app
[Next Steps]: #next-steps

<!-- URLs. -->
[Mobile Services server script reference]: http://go.microsoft.com/fwlink/?LinkId=262293
[Get started with Mobile Services]: mobile-services-html-get-started.md
[Add Mobile Services to an existing app]: mobile-services-html-get-started-data.md
[Store server scripts in source control]: mobile-services-store-scripts-source-control.md

test
