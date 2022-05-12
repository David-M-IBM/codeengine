---

copyright:
  years: 2020, 2022
lastupdated: "2022-04-27"

keywords: applications in code engine, apps in code engine, http requests in code engine, deploy apps in code engine, app workloads in code engine, deploying workloads in code engine, application, app, memory, cpu, environment variables

subcollection: codeengine

---

{{site.data.keyword.attribute-definition-list}}

# Deploying your app from local source code with the CLI
{: #app-local-source-code}

You can deploy your application directly from source code on your local workstation with the {{site.data.keyword.codeenginefull}} CLI. Use the **`app create`** command to both build an image from your local source, and deploy your application to reference this built image.
{: shortdesc}

When you submit a build that pulls code from a local directory, your source code is packed into an archive file. {{site.data.keyword.codeengineshort}} automatically uploads the image to an {{site.data.keyword.registrylong}} namespace in your account, and then creates and deploys your app to reference this built image. Note that you can only target {{site.data.keyword.registrylong_notm}} for your local builds. For this scenario, you need to provide only a name for the app and the path to the local source. For a complete listing of options, see the [**`ibmcloud ce app create`**](/docs/codeengine?topic=codeengine-cli#cli-application-create) command.  

You can choose to ignore certain file patterns from within your source code by using the `.ceignore` file, which behaves similarly to a `.gitignore` file. The source image is created in the same namespace as your build image.

The {{site.data.keyword.registrylong}} is required for this scenario.
{: important}

Before you begin

* Set up your [{{site.data.keyword.codeengineshort}} CLI](/docs/codeengine?topic=codeengine-install-cli) environment.
* [Create and work with a project](/docs/codeengine?topic=codeengine-manage-project).

Before you work with local source, make sure that your source is in an accessible location on your local workstation.  

This example uses the `https://github.com/IBM/CodeEngine` samples; in particular, the `helloworld` sample. 

1. Download the `https://github.com/IBM/CodeEngine` sample source to your local workstation with the following command.

    ```txt
    git clone https://github.com/IBM/CodeEngine
    ```
    {: pre}

2. Change to the `CodeEngine\helloworld` directory. 

3. From the `CodeEngine\helloworld` directory, create and deploy the `myapp-local` app, which uses an image that is built from the `CodeEngine\helloworld` source on your local workstation. This command automatically builds and pushes the image to a {{site.data.keyword.registryshort}} namespace in your account. If you do not have an existing {{site.data.keyword.registryshort}} namespace, {{site.data.keyword.codeengineshort}} automatically creates one for you.

    ```txt
    ibmcloud ce application create --name myapp-local --build-source .
    ```
    {: pre}

    The `.` indicates the build source is located in your current working directory. 
    {: note}


    **Example output**

    ```txt
    Creating application 'myapp-local'...
    Creating build 'myapp-local-build-220414-171750199'...
    Packaging files to upload from source path '.'...
    Submitting build run 'myapp-local-run-220414-171750199'...
    Creating image 'private.us.icr.io/ce--abcde-glxo4kabcde/app-myapp-local:220414-2117-rdaga'...
    Waiting for build run to complete...
    Build run status: 'Running'
    Build run completed successfully.
    Run 'ibmcloud ce buildrun get -n myapp-local-run-220414-171750199' to check the build run status.
    Waiting for application 'myapp-local' to become ready.
    Configuration 'myapp-local' is waiting for a Revision to become ready.
    Ingress has not yet been reconciled.
    Waiting for load balancer to be ready.
    Run 'ibmcloud ce application get -n myapp-local' to check the application status.
    OK

    https://myapp-local.glxo4kabcde.us-south.codeengine.appdomain.cloud
    ```
    {: screen}

    Notice the output of the **`application create`** command provides information on the progression of the build and build run before the app is created and deployed. 
    {: tip}

    In this example, the built image is uploaded to the `ce--abcde-glxo4kabcde` namespace in {{site.data.keyword.registryshort}}. 

    The following table summarizes the options that are used with the **`app create`** command in this example. For more information about the command and its options, see the [**`ibmcloud ce app create`**](/docs/codeengine?topic=codeengine-cli#cli-application-create) command.

    | Option | Description |
    | -------------- | -------------- |
    | `--name` | The name of the application. Use a name that is unique within the project. This value is required. \n - The name must begin with a lowercase letter. \n - The name must end with a lowercase alphanumeric character. \n - The name must be 63 characters or fewer and can contain letters, numbers, and hyphens (-). | 
    | `--build-source` | The path to the local source. |
    {: caption="Table 1. Command description" caption-side="bottom"}

4. Use the **`application get`** command to display information about your app, including information about the build.

    ```txt
    ibmcloud ce application get --name myapp-local
    ```
    {: pre}

    Example output

    ```txt
    [...]
    Name:          myapp-local
    ID:            abcdefgh-abcd-abcd-abcd-1a2b3c4d5e6f  
    Project Name:  myproject  
    Project ID:    01234567-abcd-abcd-abcd-abcdabcd1111 
    Age:           2d15h
    Created:       2022-04-14T16:10:11-04:00
    URL:                https://myapp-local.glxo4kabcde.us-south.codeengine.appdomain.cloud
    Cluster Local URL:  http://myapp-local.glxo4kabcde.svc.cluster.local
    Console URL:        https://cloud.ibm.com/codeengine/project/us-south/01234567-abcd-abcd-abcd-abcdabcd1111/application/myapp-local/configuration
    Status Summary:  Application deployed successfully

    Environment Variables:
      Type     Name          Value
      Literal  CE_APP        myapp-local
      Literal  CE_DOMAIN     us-south.codeengine.appdomain.cloud
      Literal  CE_SUBDOMAIN  glxo4kabcde
    Image:                  private.us.icr.io/ce--abcde-glxo4kabcde/app-myapp-local:220414-2010-sqsoj
    Resource Allocation:
      CPU:                1
      Ephemeral Storage:  400M
      Memory:             4G
    Registry Secrets:
      ce-auto-icr-private-us-south

    Revisions:
      myapp-local-00001:
        Age:                23m
        Latest:             true
        Traffic:            100%
        Image:              private.us.icr.io/ce--abcde-glxo4kabcde/app-myapp-local:220414-2010-sqsoj (pinned to 86944c)  
        Running Instances:  0

    Runtime:
      Concurrency:    100
      Maximum Scale:  10
      Minimum Scale:  0
      Timeout:        300

    Build Information:
      Build Name:         myapp-local-build-220414-161009244
      Build Run Name:     myapp-local-run-220414-161009244
      Build Type:         git
      Build Strategy:     dockerfile-medium
      Timeout:            600
      Source:             https://github.com/IBM/CodeEngine
      Context Directory:  helloworld
      Dockerfile:         Dockerfile

      Build Status:       Succeeded
      Build Reason:       all validations succeeded
      Run 'ibmcloud ce build get -n myapp-local-build-220414-161009244' for details.

      Build Run Summary:  Succeeded
      Build Run Status:   Succeeded
      Build Run Reason:   All Steps have completed executing
      Run 'ibmcloud ce buildrun get -n myapp-local-run-220414-161009244' for details.
    [...]
    ```
    {: screen}


Instead of building your image from local source and deploying your app with a single command, you can choose to build from local source first *before* you deploy your app.  See [Creating a build configuration that pulls source from a local workstation](/docs/codeengine?topic=codeengine-build-image#build-config-local-cli), [Running a build for source from a local workstation](/docs/codeengine?topic=codeengine-build-image#build-run-cli), and [Deploying applications](/docs/codeengine?topic=codeengine-application-workloads). 

## Next steps
{: #nextsteps-app-localdeploysource}

* After your app deploys, [access your app](/docs/codeengine?topic=codeengine-access-service) through a URL.

* Now that you app is deployed, consider making your apps event-driven. By using event subscriptions, you can trigger your apps by [periodic schedules](/docs/codeengine?topic=codeengine-subscribe-cron#eventing-cron-existing-app) or set your app to react to events like [file uploads](/docs/codeengine?topic=codeengine-eventing-cosevent-producer#obstorage_ev_app).

* After your app is deployed, you can [update your deployed app](/docs/codeengine?topic=codeengine-update-app) and its referenced code by using *any* of the following ways, independent of how you created or previously updated your app:

    - If you have a container image, per the [Open Container Initiative (OCI) standard](https://opencontainers.org/){: external}, then you need to provide only a reference to the image, which points to the location of your container registry when you deploy your app. You can deploy your app with an image in a [public registry](/docs/codeengine?topic=codeengine-deploy-app) or [private registry](/docs/codeengine?topic=codeengine-deploy-app-private).

    - If you are starting with source code that resides in a Git repository, you can choose to let {{site.data.keyword.codeengineshort}} take care of building the image from your source and deploying the app with a **single** operation. In this scenario, {{site.data.keyword.codeengineshort}} uploads your image to {{site.data.keyword.registrylong}}. To learn more, see [Deploying your app from repository source code](/docs/codeengine?topic=codeengine-app-source-code). If you want more control over the build of your image, then you can choose to [build the image](/docs/codeengine?topic=codeengine-build-image) with {{site.data.keyword.codeengineshort}} before you deploy your app. 

    - If you are starting with source code that resides on a local workstation, you can choose to let {{site.data.keyword.codeengineshort}} take care of building the image from your source and deploying the app with a **single** CLI command. In this scenario, {{site.data.keyword.codeengineshort}} uploads your image to {{site.data.keyword.registrylong}}. To learn more, see [Deploying your app from local source code with the CLI](/docs/codeengine?topic=codeengine-app-local-source-code). If you want more control over the build of your image, then you can choose to [build the image](/docs/codeengine?topic=codeengine-build-image) with {{site.data.keyword.codeengineshort}} before you deploy your app. 

    For example, you might choose to let {{site.data.keyword.codeengineshort}} handle the build of your local source while you evolve the development of your source for the app. Then, after the image is matured, you can update the deployed app to reference the specific image that you want. You can repeat this process as needed.

    When you deploy your updated app, the latest version of your referenced container image is downloaded and deployed, unless a tag is specified for the image. If a tag is specified for the image, then the tagged image is used for the deployment. 




Looking for more code examples? Check out the [Samples for {{site.data.keyword.codeenginefull_notm}} GitHub repo](https://github.com/IBM/CodeEngine){: external}.
{: tip}


