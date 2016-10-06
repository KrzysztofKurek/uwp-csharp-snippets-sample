# Microsoft Graph UWP Snippets Sample (SDK)

**Table of contents**

* [Introduction](#introduction)
* [Prerequisites](#prerequisites)
* [Register and configure the app](#register)
* [Build and debug](#build)
* [Run the sample](#run)
* [How the sample affects your tenant data](#how-the-sample-affects-your-tenant-data)
* [Add a snippet](#add-a-snippet)
* [Questions and comments](#questions)
* [Additional resources](#additional-resources)

<a name="introduction"></a>
##Introduction

This sample contains a repository of code snippets that show how to use the Microsoft Graph SDK to send email, manage groups, and perform other activities with Office 365 data. It uses the [Microsoft Graph .NET Client Library](https://github.com/microsoftgraph/msgraph-sdk-dotnet) to work with data returned by Microsoft Graph.

This repository shows you how to access multiple resources, including Microsoft Azure Active Directory (AD) and the Office 365 APIs, by making HTTP requests to the Microsoft Graph API in a Windows 10 universal app. 

These snippets are simple and self-contained, and you can copy and paste them into your own code, whenever appropriate, or use them as a resource for learning how to use the Microsoft Graph client library.

**Note:** If possible, please use this sample with a "non-work" or test account. The sample does not always clean up the created objects in your mailbox and calendar. At this time you'll have to manually remove sample mails and calendar events. Also note that the snippets that get and send messages and that get, create, update, and delete events won't work with all personal accounts. These operations will eventually work when those accounts are updated to work with the Azure AD v2.0 endpoint.

 

<a name="prerequisites"></a>
## Prerequisites ##

This sample requires the following:  

  * [Visual Studio 2015](https://www.visualstudio.com/en-us/downloads) 
  * Windows 10 ([development mode enabled](https://msdn.microsoft.com/library/windows/apps/xaml/dn706236.aspx))
  * Windows 10 SDK
  * Either a [Microsoft](www.outlook.com) or [Office 365 for business account](https://msdn.microsoft.com/en-us/office/office365/howto/setup-development-environment#bk_Office365Account).

      
<a name="register"></a>
##Register and configure the app

1. Sign in to the [Azure portal](https://portal.azure.cn/).
2. On the top bar, click on your account and under the **Directory** list, choose the Active Directory tenant where you wish to register your application.
3. Click on **More Services** in the left hand nav, and choose **Azure Active Directory**.
4. Click on **App registrations** and choose **Add**.
5. Enter a friendly name for the application. Select **Native** as the Application Type. For the **Redirect URI**, enter **https://developer.graph.microsoft.com/**. Click on **Create** to create the application.
6. While still in the Azure portal, choose your application, click on **Settings** and choose **Properties**.
7. Find the **Application ID** value and copy it to the clipboard. This is the client ID value we'll add to the project later.
8. Configure **Permissions** for your application - in the **Settings** menu, choose the **Required permissions** section, click on **Add**, then **Select an API**, and type "Microsoft Graph" in the text box. Then, click on **Select Permissions** and select:

	* Read and write signed-in user's calendars
	* Read signed-in user's contacts
	* Read signed-in user's files
	* Send mail as signed-in user
	* Read signed-in user's mail
	* Read all users' full profiles
	* Read and write signed-in user's profile
	* Access directory as the signed-in user
	* Read and write directory data
 
9. Click **Select**.


<a name="build"></a>
## Build and debug ##

**Note:** If you see any errors while installing packages during step 2, make sure the local path where you placed the solution is not too long/deep. Moving the solution closer to the root of your drive resolves this issue.

1. After you've loaded the solution in Visual Studio, configure the sample to use the client id and redirectURI that you registered by adding the corresponding values for these keys in the Application.Resources node of the App.xaml file.
![Office 365 UWP Microsoft Graph connect sample](/readme-images/appId_and_redirectURI.png "Client ID value in App.xaml file")`

2.	If you are planning on signing into the sample with a work or school account that does not have admin permissions, you'll need to leave code that requests scopes requiring admin permissions commented out. If these lines aren't commented, you won't be able to sign in with your non-admin work or school account (if you sign in with a personal account, these scope requests are ignored.)

	In the `GetTokenForUserAsync()` method of the `AuthenticationHelper.cs` file, see the following scope requests. Leave these commented unless you're using a work or school account that has admin permissions:

	```
		//"https://graph.microsoft.com/Directory.AccessAsUser.All",
	    //"https://graph.microsoft.com/User.ReadWrite.All",
	    //"https://graph.microsoft.com/Group.ReadWrite.All",
	```

3. Press F5 to build and debug. Run the solution and sign in with either your personal or work or school account.

<a name="run"></a>
## Run the sample

When launched, the app displays a series of boxes representing common user tasks, or 'stories'. Each story is comprised of one or more code snippets. The stories are grouped by the account type and permission level:

- Tasks that are applicable to both work or school and personal accounts, such as getting and sending email, creating files, etc.
- Tasks that are only applicable to work or school accounts, such as getting a user's manager or account photo.
- Tasks that are only applicable to a work or school account with administrative permissions, such as getting group members or creating new user accounts.

Select the stories you want to execute, and choose the run button. You'll be prompted to log in with your work or school or personal account. Be aware that if you log in with an account that doesn't have applicable permissions for the stories you've selected(for example, if you select stories that are applicable only to a work or school account, and then log in with a personal account), those stories will fail.

Each story turns green if it succeeds, and red if it fails. Additional information is sent to the Output window. Select **Ctrl + W,O** to view the Output window and see information about the operation.

<a name="#how-the-sample-affects-your-tenant-data"></a>
##How the sample affects your tenant data
This sample runs commands that create, read, update, or delete data. When running commands that delete or edit data, the sample creates test entities. The sample will leave behind some of these entities on your tenant.

<a name="add-a-snippet"></a>
##Add a snippet

This project includes two snippets files: 

- Groups\GroupSnippets.cs 
- Users\UserSnippets.cs.

If you have a snippet of your own that you would like to run in this project, just follow these three steps:

1. **Add your snippet to the snippets file.** Be sure to include a try/catch block. 

	```cs
	try
	{
		var graphClient = AuthenticationHelper.GetAuthenticatedClient();
	
		var currentUserObject = await graphClient.Me.Request().GetAsync();
		currentUserName = currentUserObject.DisplayName;
	
		if ( currentUserName != null)
		{
			Debug.WriteLine("Got user: " + currentUserName);
		}
	
	}
	
	catch (ServiceException e)
	{
		Debug.WriteLine("We could not get the current user: " + e.Error.Message);
		return null;
	}
	```

2. **Create a story that uses your snippet and add it to the associated stories file.** For example, the `TryGetMeAsync()` story uses the `GetMeAsync()` snippet inside the Users\UserStories.cs file:

	```cs
	public static async Task<bool> TryGetMeAsync()
	{
		var currentUser = await UserSnippets.GetMeAsync();
	
		return currentUser != null;
	}       
	```
	
	Sometimes your story will need to run snippets in addition to the one that you're implementing. For example, if you want to update an event, you can use the `CreateEventAsync()` method to create an event. Then you can update it. Always be sure to use snippets that already exist in the snippets file. If the operation you need doesn't exist, you'll have to create it and then include it in your story. It's a best practice to delete any entities that you create in a story, especially if you're working on anything other than a test or developer tenant.

3. **Add your story to the story collection in MainPageXaml.cs** (inside the `CreateStoryList()` method):

	```cs
	StoryCollection.Add(new StoryDefinition() 
		{ GroupName = "Users", Title = "Get Me",  
			ScopeGroup= "Applicable to personal or work accounts", RunStoryAsync = UserStories.TryGetMeAsync });
	```

Now you can test your snippet. When you run the app, your snippet will appear as a new box in the grid. Select the box for your snippet, and then run it. Use this as an opportunity to debug your snippet.

<a name="contributing"></a>
## Contributing ##

If you'd like to contribute to this sample, see [CONTRIBUTING.MD](/CONTRIBUTING.md).

This project has adopted the [Microsoft Open Source Code of Conduct](https://opensource.microsoft.com/codeofconduct/). For more information see the [Code of Conduct FAQ](https://opensource.microsoft.com/codeofconduct/faq/) or contact [opencode@microsoft.com](mailto:opencode@microsoft.com) with any additional questions or comments.

<a name="questions"></a>
## Questions and comments

We'd love to get your feedback about the Microsoft Graph UWP Snippets  Library project. You can send your questions and suggestions to us in the [Issues](https://github.com/OfficeDev/Microsoft-Graph-UWP-Snippets-Library/issues) section of this repository.

Your feedback is important to us. Connect with us on [Stack Overflow](http://stackoverflow.com/questions/tagged/office365+or+microsoftgraph). Tag your questions with [MicrosoftGraph].

<a name="additional-resources"></a>
## Additional resources ##

- [Microsoft Graph overview](http://graph.microsoft.io)
- [Office developer code samples](http://dev.office.com/code-samples)
- [Office dev center](http://dev.office.com/)


## Copyright
Copyright (c) 2016 Microsoft. All rights reserved.


