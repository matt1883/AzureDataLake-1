<properties
   pageTitle="Authenticate with Data Lake Store using Active Directory | Azure"
   description="Learn how to authenticate with Data Lake Store using Active Directory"
   services="data-lake-store"
   documentationCenter=""
   authors="nitinme"
   manager="paulettm"
   editor="cgronlun"/>

<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="07/20/2016"
   ms.author="nitinme"/>

# Authenticate with Data Lake Store using Azure Active Directory

Azure Data Lake Store uses Azure Active Directory (Azure AD) for authentication. This article provides instructions on how to create an Azure AD application and use it to authenticate with Azure Data Lake Store.

## Prerequisites

* An Azure subscription. See [Get Azure free trial](https://azure.microsoft.com/pricing/free-trial/).

## Non-interactive authentication using Azure Active Directory client key (secret)

In this section, we will create an Azure AD Web Application, generate a client key (client secret). You can use these to enable your solution to authenticate without any user interaction. Using this kind of application is a 3-step process.

1. Create an Azure AD application of type "Web Application".
2. Retrieve the client ID, client key (secret), and OAuth2.0 token endpoint for the application.
3. Grant permissions for the application to access the Azure Data Lake Store filesystem.

### Step 1: Create an Azure AD identity

1. Log in to your Azure Account through the [classic portal](https://manage.windowsazure.com/).

2. Select **Active Directory** from the left pane.

     ![select Active Directory](./media/data-lake-store-authenticate-using-active-directory/active-directory.png)
     
3. Select the Azure Active Directory that you want to use for creating the new application. If you have more than one Active Directory, you need to create the application in the directory where your subscription resides. You can only grant access to resource in your subscription for applications in the same directory as your subscription.  

     ![choose directory](./media/data-lake-store-authenticate-using-active-directory/active-directory-details.png)
    
3. To view the applications in your directory, click on **Applications**.

     ![view applications](./media/data-lake-store-authenticate-using-active-directory/view-applications.png)

4. If you haven't created an application in that directory before, then you should see something similar to following image. Click on **ADD AN APPLICATION**

     ![add application](./media/data-lake-store-authenticate-using-active-directory/create-application.png)

     Or, click **Add** in the bottom pane.

     ![add](./media/data-lake-store-authenticate-using-active-directory/add-icon.png)

6. Provide a name for the application and select the type of application you want to create. For this tutorial, create a **WEB APPLICATION AND/OR WEB API** and click the next button. If you select **NATIVE CLIENT APPLICATION**, the remaining steps of this article will not match your experience.

     ![name application](./media/data-lake-store-authenticate-using-active-directory/tell-us-about-your-application.png)

7. Fill in the properties for your app. For **SIGN-ON URL**, provide the URI to a web site that describes your application. The existence of the web site is not validated. For **APP ID URI**, provide the URI that identifies your application.

     ![application properties](./media/data-lake-store-authenticate-using-active-directory/app-properties.png)

	Click the check mark to complete the wizard and create the application.

### Step 2: Get client id, client key, and token endpoint

When programmatically logging in, you need the client ID and key (secret) for your application.

1. Click on the **Configure** tab to configure your application's password.

     ![configure application](./media/data-lake-store-authenticate-using-active-directory/application-configure.png)

2. Copy the **CLIENT ID**.
  
     ![client id](./media/data-lake-store-authenticate-using-active-directory/client-id.png)

3. Since the application will run under its own credentials, scroll down to the **Keys** section and select how long you would like your password to be valid.

     ![keys](./media/data-lake-store-authenticate-using-active-directory/create-key.png)

4. Select **Save** to create your key.

    ![save](./media/data-lake-store-authenticate-using-active-directory/save-icon.png)

    The saved key is displayed and you can copy it. You will not be able to retrieve the key later, so you must copy it now.

    ![saved key](./media/data-lake-store-authenticate-using-active-directory/save-key.png)

5. Retrieve the OAuth2.0 token endpoint by selecting **View endpoints** at the bottom of the screen and retrieving the value for **OAuth 2.0 Token Endpoint** field, as shown below.  

	![tenant id](./media/data-lake-store-authenticate-using-active-directory/save-tenant.png)

### Step 3: Assign the identity to the Azure Data Lake Store account

Now you will grant permissions for your application to access your Data Lake Store filesystem.

1. Sign on to the new [Azure Portal](https://portal.azure.com) and open the Azure Data Lake Store account that you want to associate with the Azure Active Directory application you created earlier.

2. From the Data Lake Store blade, click the **Data Explorer** icon.

3. From the Data Explorer blade, click the **Access** icon.

4. From the **Access** blade, click **Add**.

5. By providing the client ID of your application, give your application recursive read, write, and execute permissions on the Data Lake Store filesystem.

## Interactive authentication using Azure Active Directory native client application

In this section, we will create an Azure AD Native Client Application. You can use this to enable your solution to authenticate an end-user, so that your solution can access Data Lake Store as the end-user. Using this kind of application is a 2-step process.

1. Create an Azure AD application of type "Native Client Application".
2. Retrieve the client ID and OAuth2.0 token endpoint for the application.

### Step 1: Create an Azure AD identity

1. Log in to your Azure Account through the [classic portal](https://manage.windowsazure.com/).

2. Select **Active Directory** from the left pane.

     ![select Active Directory](./media/data-lake-store-authenticate-using-active-directory/active-directory.png)
     
3. Select the Azure Active Directory that you want to use for creating the new application. If you have more than one Active Directory, you need to create the application in the directory where your subscription resides. You can only grant access to resource in your subscription for applications in the same directory as your subscription.  

     ![choose directory](./media/data-lake-store-authenticate-using-active-directory/active-directory-details.png)
    
3. To view the applications in your directory, click on **Applications**.

     ![view applications](./media/data-lake-store-authenticate-using-active-directory/view-applications.png)

4. If you haven't created an application in that directory before, then you should see something similar to following image. Click on **ADD AN APPLICATION**

     ![add application](./media/data-lake-store-authenticate-using-active-directory/create-application.png)

     Or, click **Add** in the bottom pane.

     ![add](./media/data-lake-store-authenticate-using-active-directory/add-icon.png)

6. Provide a name for the application and select the type of application you want to create. For this tutorial, create a **NATIVE CLIENT APPLICATION** and click the next button. If you select **WEB APPLICATION AND/OR WEB API**, the remaining steps of this article will not match your experience.

	Click the check mark to complete the wizard and create the application.

### Step 2: Get client id and token endpoint

When interactively logging end-users in, you need the client ID for your application.

1. Click on the **Configure** tab to configure your application.

     ![configure application](./media/data-lake-store-authenticate-using-active-directory/application-configure.png)

2. Copy the **CLIENT ID**.
  
     ![client id](./media/data-lake-store-authenticate-using-active-directory/client-id.png)

3. Retrieve the OAuth2.0 token endpoint by selecting **View endpoints** at the bottom of the screen and retrieving the value for **OAuth 2.0 Token Endpoint** field, as shown below.  

	![tenant id](./media/data-lake-store-authenticate-using-active-directory/save-tenant.png)

## Sample applications

Once you have set up your Azure Active Directory application, you can write an application that accesses Data Lake Store. Following are some examples on how to achieve this.

* [Get started with Azure Data Lake Store using Java SDK](data-lake-store-get-started-java-sdk.md)


## Next steps

- [Secure data in Data Lake Store](data-lake-store-secure-data.md)
- [Use Azure Data Lake Analytics with Data Lake Store](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
- [Use Azure HDInsight with Data Lake Store](data-lake-store-hdinsight-hadoop-use-portal.md)

