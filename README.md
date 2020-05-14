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

You can grab the sample [docker file](https://docs.docker.com/engine/examples/dotnetcore/) from docker, however since we have used dotnet core 3.1 we have to do some minor changes. Check my [docker](https://github.com/PankajPatro/learning-docker-kubernetes/blob/master/DockerFile) file in my git to find the changes.

Make sure to change the second param of the ENTRYPOINT array to the name of your DLL.

### Build your Image

```cmd
docker build -t learning .
```
You can see why we had to change the docker file for build. We use two environments to build our application as we use dotnet core environment to build the dotnet and use a node environment to build the angular.

Your docker image is built successfully and ready for deployment. You can choose your environment and at this point deploy to a docker and see if it's working.

### Deploy your Image

Make sure your minikube is started.

```cmd
minikube start --container-runtime docker --vm-driver=docker
```

To deploy your image you need a yaml file. You can read more about yaml [here](https://yaml.org/spec/history/2001-03-30.html)

You can find my yaml at my [git](https://github.com/PankajPatro/learning-docker-kubernetes/blob/master/deployment.yaml)

All you say in your yaml is your container name and few more metadata to it. kubectl should have been installed with minikube.

```cmd
kubectl create -f deployment.yaml
```

Now check if the deployment has succeeded

```cmd
kubectl get deployments
```

You can see that though your deployment has gone through they are not yet available. It's because by default minikube does not know about our image available on docker daemon. We have to share the context for it to know the availability of the image or alternatively push your image to docker hub.

```cmd
 & minikube -p docker3 docker-env | Invoke-Expression
 ```

If you check we have put *imagePullPolicy: Never*. This is to ensure that the Docker daemon doesn’ t try to pull unknown images from the Docker registry.

If you re-run your command

```cmd
kubectl get deployments 
```

You should be able to see that the deployments is working. All good so now we know that the image is created and deployed successfully. You can fire the minikube dashboard to see it.

```cmd
minikube dashboard
```

Now how to do we see the same in our browser. For that to work we have to expose the application as a service. The step is similar we have to deploy a service exposing our application. You can check my [services.yaml])(https://github.com/PankajPatro/learning-docker-kubernetes/blob/master/services.yaml)

```cmd
kubectl create -f deployment.yaml
```

Once you see that your services is deployed successfully. All you have to do is find the url and open the url in your browser to see your application in action.

Get your service url

```cmd
minikube service learning --url
```

Once you see your web application. You can do your clean up

```cmd
kubectl delete service learning
kubectl delete deployment learning
minikube delete
minikube stop
```

Enjoy coding. Please visit my [git](https://github.com/PankajPatro/learning-docker-kubernetes)