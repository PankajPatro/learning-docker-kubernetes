# Asp.Net core + Kubernetes 

This project is a self learning for first timers. The objective of this project is to create a simple asp.net core web application using the standard dotnet template and then delpoy it to a kubernetes cluster. The plan is for learning and see it working on local system.

### Pre-Requisite

    - Windows 10
    - Code Editor(Visual Studio Code)
    - Docker Desktop for Windows
    - MiniKube
    - dotnet core

### Installation

Before starting we have to make sure your environment is ready. Let's start by installing the VS Code.

| Tables                                           |
| -----------------------------------------------  |
| install [VSCode](https://code.visualstudio.com/) |


Install the latest dotnet sdk

| Tables                                                                  |
| ----------------------------------------------------------------------- |
| install [dotnet](https://dotnet.microsoft.com/download/dotnet-core/3.1) |


Install Docker Desktop

| Tables                                                                                 |
| -------------------------------------------------------------------------------------- |
| install [docker](https://hub.docker.com/editions/community/docker-ce-desktop-windows/) |

Install Minikube

It's easier to install minikube with all its dependencies using [chocaltey](https://chocolatey.org/install). Once installed run the below command in a command prompt, preferably in Admin mode.

```cmd
choco install minikube
```

### Creating a dotnet application.

For this example we have used the dotnet angular template. Open VSCode in your working folder and create the sample dotnet project.

```cmd
dotnet new angular -o learning
```

Check your application by running it from vs code. Hopefully it should work.

### Preparing your application for docker.

Before you create a docker file to deploy your application, we have to change our application a bit.
Open the learning.csproj file and remove the PublishSPAWebpack Target section

<del>
&lt;Target Name="PublishRunWebpack" AfterTargets="ComputeFilesToPublish"&gt;
    &lt;!-- As part of publishing, ensure the JS resources are freshly built in production mode --&gt;
    &lt;Exec WorkingDirectory="$(SpaRoot)" Command="npm install" /&gt;
    &lt;Exec WorkingDirectory="$(SpaRoot)" Command="npm run build -- --prod" /&gt;
    &lt;Exec WorkingDirectory="$(SpaRoot)" Command="npm run build:ssr -- --prod" Condition=" '$(BuildServerSideRenderer)' == 'true' " /&gt;
    &lt;!-- Include the newly-built files in the publish output --&gt;
    &lt;ItemGroup&gt;
      &lt;DistFiles Include="$(SpaRoot)dist\**; $(SpaRoot)dist-server\**" /&gt;
      &lt;DistFiles Include="$(SpaRoot)node_modules\**" Condition="'$(BuildServerSideRenderer)' == 'true'" /&gt;
      &lt;ResolvedFileToPublish Include="@(DistFiles-&gt;'%(FullPath)')" Exclude="@(ResolvedFileToPublish)"&gt;
        &lt;RelativePath&gt;%(DistFiles.Identity)&lt;/RelativePath&gt;
        &lt;CopyToPublishDirectory&gt;PreserveNewest&lt;/CopyToPublishDirectory&gt;
        &lt;ExcludeFromSingleFile&gt;true&lt;/ExcludeFromSingleFile&gt;
      &lt;/ResolvedFileToPublish&gt;
    &lt;/ItemGroup&gt;
  &lt;/Target&gt;
</del>


We will understand later why we need to remove this step from csproj.

### Create Docker file
To deploy our application we need to prepare our application for docker. We need to create the docker file.

You can grab the sample [docker file](https://docs.docker.com/engine/examples/dotnetcore/) from docker, however since we have used dotnet core 3.1 we have to do some minor changes. Check my git to find the changes.

