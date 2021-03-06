<properties
   pageTitle="Use Data Lake Store Java SDK to develop applications | Azure"
   description="Use Azure Data Lake Store Java SDK to develop applications"
   services="data-lake-store"
   documentationCenter=""
   authors="nitinme"
   manager="paulettm"
   editor="cgronlun"/>

<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="07/20/2016"
   ms.author="nitinme"/>

# Get started with Azure Data Lake Store using Java

> [AZURE.SELECTOR]
- [Portal](data-lake-store-get-started-portal.md)
- [PowerShell](data-lake-store-get-started-powershell.md)
- [.NET SDK](data-lake-store-get-started-net-sdk.md)
- [Java SDK](data-lake-store-get-started-java-sdk.md)
- [REST API](data-lake-store-get-started-rest-api.md)
- [Azure CLI](data-lake-store-get-started-cli.md)
- [Node.js](data-lake-store-manage-use-nodejs.md)

Learn how to use the Azure Data Lake Store Java SDK to perform basic operations such as creating directories and uploading or downloading files. For more information about Data Lake Store, see [Azure Data Lake Store](data-lake-store-overview.md).

## Prerequisites

* Java Development Kit (JDK 7 or higher, using Java version 1.7 or higher)
* Azure Data Lake Store account. Follow the instructions at [Get started with Azure Data Lake Store using the Azure Portal](data-lake-store-get-started-portal.md).
* [Maven](https://maven.apache.org/install.html). This tutorial uses Maven for build and project dependencies. Although it is possible to build without using a build system like Maven or Gradle, these systems make it much easier to manage dependencies.
* (Optional) An IDE like [IntelliJ IDEA](https://www.jetbrains.com/idea/download/) or [Eclipse](https://www.eclipse.org/downloads/) or similar. The instructions below assume the use of IntelliJ IDEA.

## How do I authenticate using Azure Active Directory?

Your Python solution can authenticate with Azure Active Directory using either of two methods: interactive and non-interactive.

### Interactive authentication

(content)

### Non-interactive authentication

(content)

In this tutorial, we use non-interactive authentication. We use a client ID and client key (also known as a client secret) to retrieve Azure Active Directory (Azure AD) OAuth2.0 tokens. We use these tokens to create a Data Lake Store client object to perform filesystem operations. For instructions on how to authenticate with Azure Data Lake Store, see [Authenticating non-interactively with Data Lake Store using Azure Active Directory](data-lake-store-authenticate-using-active-directory.md).

Azure AD provides other options as well to retrieve a token. You can pick from a number of different authentication mechanisms to suit your scenario. For example, browser-based applications, desktop applications, and server applications running on-premises or in Azure virtual machines can each use different authentication mechanisms. You can also pick from different types of credentials like passwords and certificates. In addition, Azure AD allows you to synchronize your on-premises Active Directory users with the cloud. For details, see [Authentication Scenarios for Azure Active Directory](../active-directory/active-directory-authentication-scenarios.md). 

## Create a Java application

The code sample available [here](https://github.com/asikaria-msft/clientV2-sample) walks you through the process of creating files in the store, uploading files, concatenating files, downloading a file, and deleting a file in the store. This section of the article walks you through the main parts of the code.

1. Create a new Maven project. For instructions on how to do this using IntelliJ, see [here](https://www.jetbrains.com/help/idea/2016.1/creating-and-running-your-first-java-application.html). If using Eclipse, see [here](http://help.eclipse.org/mars/index.jsp?topic=%2Forg.eclipse.jdt.doc.user%2FgettingStarted%2Fqs-3.htm). If using the command line, use [mvn archetype](https://maven.apache.org/guides/getting-started/maven-in-five-minutes.html). 

2. Add the following dependencies to your Maven **pom.xml** file. Add the following snippet of text between the **\</version>** tag and the **\</project>** tag:

        <dependencies>
          <dependency>
            <groupId>com.microsoft.azure</groupId>
            <artifactId>azure-data-lake-store-sdk</artifactId>
            <version>2.0.1-SNAPSHOT</version>
          </dependency>
          <dependency>
            <groupId>org.slf4j</groupId>
            <artifactId>slf4j-nop</artifactId>
            <version>1.7.21</version>
          </dependency>
        </dependencies>

	The first dependency is to use the Data Lake Store SDK (`azure-datalake-store`) from the Maven repository. The second dependency (`slf4j-nop`) is to specify which logging framework to use for this application. The Data Lake Store SDK uses [slf4j](http://www.slf4j.org/) logging façade, which lets you choose from a number of popular logging frameworks, like log4j, Java logging, logback, etc., or no logging. For this example, we will disable logging, hence we use the **slf4j-nop** binding. To use other logging options in your app, see [here](http://www.slf4j.org/manual.html#projectDep).

### Add the application code

There are three main parts to the code.

1. Obtain the Azure AD OAuth 2.0 tokens.

2. Use the tokens to create a Data Lake Store client.

3. Use the Data Lake Store client to perform operations.

#### Step 1: Obtain an Azure Active Directory token.

The Data Lake Store SDK provides convenient methods that let you obtain the security tokens needed to talk to the Data Lake Store account. However, the SDK does not mandate that only these methods be used. You can use any other means of obtaining token as well, like using the [Azure Active Directory SDK](https://github.com/AzureAD/azure-activedirectory-library-for-java), or your own custom code.

To use the Data Lake Store SDK to obtain token for the Active Directory Web application you created earlier, use the static methods in `AzureADAuthenticator` class. Replace **FILL-IN-HERE** with the actual values for the Azure AD Web application.

    private static String clientId = "FILL-IN-HERE";
    private static String authTokenEndpoint = "FILL-IN-HERE";
    private static String clientKey = "FILL-IN-HERE";
    
    AzureADToken token = AzureADAuthenticator.getTokenUsingClientCreds(authTokenEndpoint, clientId, clientKey);

#### Step 2: Create an Azure Data Lake Store client (ADLStoreClient) object

Creating an [ADLStoreClient](https://github.com/AzureAD/azure-activedirectory-library-for-java) object requires you to specify the Data Lake Store account name and the Azure Active Directory token you generated in the last step. Note that the Data Lake Store account name needs to be a fully qualified domain name. For example, replace **FILL-IN-HERE** with something like **mydatalakestore.azuredatalakestore.net**.

    private static String accountFQDN = "FILL-IN-HERE";  // full account FQDN, not just the account name
    ADLStoreClient client = ADLStoreClient.createClient(accountFQDN, token);

### Step 3: Use the ADLStoreClient to perform file and directory operations

The code below contains example snippets of some common operations. You can look at the full API docs of the ADLStoreClient object to see other operations.
 
Note that files are read from and written into using standard Java streams. This means that you can layer any of the Java streams on top of the Data Lake Store streams to benefit from standard Java functionality (e.g., Print streams for formatted output, or any of the compression or encryption streams for additional functionality on top, etc.).

    // set file permission
    client.setPermission(filename, "744");

    // append to file
    stream = client.getAppendStream(filename);
    stream.write(getSampleContent());
    stream.close();

    // Read File
    InputStream in = client.getReadStream(filename);
    byte[] b = new byte[64000];
    while (in.read(b) != -1) {
    	System.out.write(b);
    }
    in.close();

    // concatenate the two files into one
    List<String> fileList = Arrays.asList("/a/b/c.txt", "/a/b/d.txt");
    client.concatenateFiles("/a/b/f.txt", fileList);

    //rename the file
    client.rename("/a/b/f.txt", "/a/b/g.txt");

    // list directory contents
    List<DirectoryEntry> list = client.enumerateDirectory("/a/b", 2000);
    System.out.println("Directory listing for directory /a/b:");
    for (DirectoryEntry entry : list) {
        printDirectoryInfo(entry);
    }

    // delete directory along with all the subdirectories and files in it
    client.deleteRecursive("/a");

#### Step 4: Build and run the application

1. To run from within an IDE, locate and press the **Run** button. To run from Maven, use [exec:exec](http://www.mojohaus.org/exec-maven-plugin/exec-mojo.html).

2. To produce a standalone jar that you can run from command-line build the jar with all dependencies included, using the [Maven assembly plugin](http://maven.apache.org/plugins/maven-assembly-plugin/usage.html). The pom.xml in the [example source code on github](https://github.com/asikaria-msft/clientV2-sample/blob/master/pom.xml) has an example of how to do this.


## Next steps

- [Secure data in Data Lake Store](data-lake-store-secure-data.md)
- [Use Azure Data Lake Analytics with Data Lake Store](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
- [Use Azure HDInsight with Data Lake Store](data-lake-store-hdinsight-hadoop-use-portal.md)

