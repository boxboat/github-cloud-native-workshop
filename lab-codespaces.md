---
# Page settings
layout: default
keywords:
comments: false

# Hero section
title: Lab - DevOps for dotnet
description: Let's use Codespaces to make a containerized .NET app, then deploy it to Azure Container Apps.

# Author box
author:
    title: About Author
    title_url: '#'
    external_url: true
    description: Daniel is a DevOps Engineer at BoxBoat. He's delivered projects in various clouds. Azure remains his favorite.

# Micro navigation
micro_nav: true

# Page navigation
page_nav:
    prev:
        content: Home
        url: '/'
---

## Create a repo, launch Codespaces

Link: [Workshop repo](https://github.com/BoxBoat-Codespaces/codespaces-workshop)

Create a new public repo from the template.

The destination should be a Codespaces enabled Org. You should prepend the repo name with your username, so if your username is dkoch, your repo should be `dkoch-codespaces-workshop`.

Now that you have a repo to work from, let's launch Codespaces. Click the green "Code" button, "Create codespaces on main". You can now choose to open that in the browser, or in your Visual Studio Code desktop app. 


## Let's modify our app
Open a terminal in vscode. This can be done in the UI, or with ctrl+`. 

```shell
git checkout -b dev
```

**Step 1: Run the app**
```shell
# enable https in a dev environment
dotnet dev-certs https --trust
# run the app
dotnet run

# response
Building...
info: Microsoft.Hosting.Lifetime[14]
      Now listening on: https://localhost:7258
info: Microsoft.Hosting.Lifetime[14]
      Now listening on: http://localhost:5160
info: Microsoft.Hosting.Lifetime[0]
      Application started. Press Ctrl+C to shut down.
info: Microsoft.Hosting.Lifetime[0]
      Hosting environment: Development
info: Microsoft.Hosting.Lifetime[0]
```

**Step 2: Access our app**

Our app is now running, and it's available to us because Codespaces automatically forwards the ports from the `dotnet run` command. Click on the PORTS tab of the console window, then hover over the Local Address for the Application. This is https in a dev environment oddness, so that's why we have to do this. Click the globe to open this URL in the browser.

You are now at the Swagger API documentation. Expand some endpoints and try them out. 

**Step 3: Modify the source code**
In vscode, open `Program.cs`. You can see this is a very, very simple API. We have a single `hello` endpoint and several endpoints which return ASCII art. Modify the output in the `hello()` method, and add a new endpoint at `/blinky` like this:

```c#
app.MapGet("/blinky", () => ascii("blinky"));
```

Let's try it out again. Hit ctrl+c in the terminal to kill our last process, then run it again. Visit the site again and confirm your changes. Hit ctrl+c in the terminal when you are done.

```shell
# run the app
dotnet run

# response
Building...
info: Microsoft.Hosting.Lifetime[14]
      Now listening on: https://localhost:7258
info: Microsoft.Hosting.Lifetime[14]
      Now listening on: http://localhost:5160
info: Microsoft.Hosting.Lifetime[0]
      Application started. Press Ctrl+C to shut down.
info: Microsoft.Hosting.Lifetime[0]
      Hosting environment: Development
info: Microsoft.Hosting.Lifetime[0]
```

## Test the Docker container

**Step 1: Build the docker image.**

```shell
dotnet build -c release
dotnet publish -o app
docker build . -t "my-app"

#response
[+] Building 8.3s (9/9) FINISHED                                               
 => [internal] load build definition from Dockerfile                      0.1s
 => => transferring dockerfile: 35B                                       0.0s
 => [internal] load .dockerignore                                         0.2s
 => => transferring context: 2B                                           0.0s
 => [internal] load metadata for mcr.microsoft.com/dotnet/aspnet:6.0      0.1s
 => [1/4] FROM mcr.microsoft.com/dotnet/aspnet:6.0@sha256:a4295c431776f9  6.0s
 => => resolve mcr.microsoft.com/dotnet/aspnet:6.0@sha256:a4295c431776f9  0.1s
 => => sha256:81ee7784a01fe6531b8fcab9bfd162c8dc779d4512 3.25kB / 3.25kB  0.0s
 => => sha256:bdd7874d464602a566b7f5087754c80ca1f5d01d 31.62MB / 31.62MB  0.8s
 => => sha256:a4295c431776f9a20b3057e32fd2b23ddaf082a2eb 2.17kB / 2.17kB  0.0s
 => => sha256:d2b071da332cf667d22f94503673f015b661af3272 1.37kB / 1.37kB  0.0s
 => => sha256:214ca5fb90323fe769c63a12af092f2572bf1c6b 31.38MB / 31.38MB  0.9s
 => => sha256:562f2b48c06c589de5b8b2f72eb9a0040bc6eab0 14.97MB / 14.97MB  0.8s
 => => sha256:8aa9b64f5310895753358c2676ade6be98179e6b05dadd 154B / 154B  1.1s
 => => sha256:e4e0aa40bc94f2509482211198a5deacb2f5882123 9.45MB / 9.45MB  1.2s
 => => extracting sha256:214ca5fb90323fe769c63a12af092f2572bf1c6b300263e  1.3s
 => => extracting sha256:562f2b48c06c589de5b8b2f72eb9a0040bc6eab0d479295  0.5s
 => => extracting sha256:bdd7874d464602a566b7f5087754c80ca1f5d01d279b251  0.6s
 => => extracting sha256:8aa9b64f5310895753358c2676ade6be98179e6b05dadd8  0.0s
 => => extracting sha256:e4e0aa40bc94f2509482211198a5deacb2f5882123462b1  0.2s
 => [internal] load build context                                         0.2s
 => => transferring context: 4.18MB                                       0.0s
 => [2/4] COPY app/ /app                                                  0.2s
 => [3/4] COPY ascii/ /app/ascii/                                         0.2s
 => [4/4] WORKDIR /app                                                    0.2s
 => exporting to image                                                    1.1s
 => => exporting layers                                                   1.0s
 => => writing image sha256:66a66bc737ab162ef439d747dfaad7b67a4b4a3b9999  0.0s
 => => naming to docker.io/library/my-app                                 0.0s
```

 **Step 2: Run the image**
 
```shell
docker images

#response
my-app                                                              latest    66a66bc737ab   40 seconds ago   212MB
vsc-codespaces-workshop-4a351b83b503e49670b983c5c2d5edf1-features   latest    28fd0b49452c   14 minutes ago   2.58GB
mcr.microsoft.com/vscode/devcontainers/universal                    2         7bac44508cce   12 days ago      11.5GB
mcr.microsoft.com/vscode/devcontainers/universal                    2-focal   7bac44508cce   12 days ago      11.5GB
mcr.microsoft.com/vscode/devcontainers/universal                    2-linux   7bac44508cce   12 days ago      11.5GB
mcr.microsoft.com/vscode/devcontainers/universal                    latest    7bac44508cce   12 days ago      11.5GB


docker run -p 8080:80 my-app

#response
info: Microsoft.Hosting.Lifetime[14]
      Now listening on: http://[::]:80
info: Microsoft.Hosting.Lifetime[0]
      Application started. Press Ctrl+C to shut down.
info: Microsoft.Hosting.Lifetime[0]
      Hosting environment: Production
info: Microsoft.Hosting.Lifetime[0]
      Content root path: /app/
```

Now we need to explicity forward port 8080, because we arbitrarily chose it when running our docker container. Click PORTS in the same are as the terminal, in vscode. Add port 8080. Now you can visit your running container in your web browser, at `http://localhost:8080`.

## Using GitHub Actions to Build Containers

Now that we've built a containerized application in Codespaces, how do we get it deployed to the cloud? Well, the first step is a CI process that builds an image and stores it in GitHub Container Registry.

 **Step 1: Create an Action**

In GitHub, click the Actions tab of your repo. We're going to use a Starter Workflow. This will allow us to very quickly, very easily, get a working Action into our repo. Under "By <YOUR_ORGANIZATION>", chose `.NET Core Container Workflow`

Edit lines `34` and `45` like so:

```diff
--        name: <APP NAME HERE>
++        name: my-app
```

Commit directly to main.

 **Step 2: Trigger the Action**

Now in the Codespaces terminal again, let's pull our changes into our branch and push the ones we've already made.

```shell
# on dev branch still
git pull origin main
# pushing our Action to the dev branch so it can be triggered by the next push
git push origin dev
git status

#response
On branch dev
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)
	modified:   Program.cs

no changes added to commit (use "git add" and/or "git commit -a")
```

Stage, commit and push your changes.

```shell
git add .
git commit -m "updating endpoints"
git push origin dev
```

 **Step 3: Trigger the whole Action**

Our Action only builds the dotnet app Artifact on the `dev` branch. To build the container, we must create a PR into `main`. Create a PR from `dev` into `main`, notice the checks and complete the PR. Our Action now runs again, this time creating an image which is stored in our repo as a package. 

On the "Code" tab of the repo, you'll see our package. Click on it, and take note and copy the url to the image from the top section on this page. It starts with `ghcr.io/`

## Using GitHub Actions to Deploy Containers to Azure Container Apps

 **Step 1: Create an Action**

An Azure Container App is essentially a containerized application that runs on top of a Microsoft-managed Kubernetes instance. All you need to worry about is your app, not the infrastructure. Well, we've already taken care of the app, this should be easy right?

Yep.

Like the last step, we create an Action from a Starter. Click Actions, then click "New workflow" and choose the "Terraform Workflow" by your Organization. We need to make just one change here, to let the Action know where our Terraform code lives. Edit line 9 like so:

```diff
--      - <PATH TO TERRAFORM DIRECTORY>
++      - terraform/**
```

That's it! Someone else has already done the hard work for you, this'll just work! Commit directly to main, just like last time.

Let's get this workflow in our `dev` branch now

```shell
git pull origin main
git push origin dev
```

 **Step 2: Edit our Terraform**

The Terraform code that will deploy our infrastructure and app consists of two files. `main.tf` contains the terraform configuration bits. We don't need to worry about that here. `container_app.tf` deploys a resource group, then a Log Analytics workspace, a Container App Environment and a Container App for us.

In Codespaces, let's edit two more files. The first is responsible for storing our _state_ so that terraform can always be aware of any changes made. Edit `terraform/backend.tf` and give the __key__ a unique name by using your username. For example, my username is 'dkoch'. __Note, if your username has any special characters like "-" or "_", omit those.__ We only want alphanumeric characters here:

```diff
terraform {
  backend "azurerm" {
    resource_group_name  = "codespaces-demo-resources"
    storage_account_name = "codespacesstate"
    container_name       = "codespacesstate"
--    key                  = "<USERNAME>.state"
++    key                  = "dkoch.state"
  }
}
```

 Then, edit `terraform/container_app.tf` and change lies `6`, `11`, `37`, `53` and `54` like so:

```diff
resource "azurerm_resource_group" "rg" {
--  name = "<USERNAME>resources"
++  name = "dkochresources"
  location = local.location
}
```

```diff
resource "azurerm_log_analytics_workspace" "laws" {
--  name                = "<USERNAME>law"
++  name                = "dkochlaw"
  location            = local.location
  resource_group_name = azurerm_resource_group.rg.name
  sku                 = "PerGB2018"
  retention_in_days   = 30
}
```

```diff
resource "azapi_resource" "container_app_environment" {
--  name = "<USERNAME>environment"
++  name = "dkochenvironment"  
  location = local.location
  parent_id = azurerm_resource_group.rg.id
```

__Make sure your image value (line 53) is all lowercase__. Reminder that this value is what we copied earlier from GitHub, on the page we get when we click on our package.

```diff
resource "azapi_resource" "container_app" {
--  name = "<USERNAME>app" 
++  name = "dkochapp"   
  location = local.location
  parent_id = azurerm_resource_group.rg.id
  type = "Microsoft.App/containerApps@2022-01-01-preview"
  body = jsonencode({
    properties = {
      managedEnvironmentId = azapi_resource.container_app_environment.id
      configuration = {
        ingress = {
          targetPort = 80
          external = true
        }
      }
      template = {
        containers = [
          {
--            image = "ghcr.io/<ORG>/<REPO>:<TAG>"
++            image = "ghcr.io/boxboat-codespaces/dkoch-workshop:20220515.3"
--            name = "<USERNAME>container"
++            name = "dkochcontainer"
          }
```
 **Step 2: Push, trigger and deploy**

In the Codespaces terminal:

```shell
# on dev branch still
git status

#response
On branch dev
Changes not staged for commit:
  (use "git add/rm <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)
	modified:   terraform/container_app.tf

no changes added to commit (use "git add" and/or "git commit -a")
```

Now we have our changes from GitHub in our dev branch, and we can stage, commit and push to trigger our new Terraform Action. 

```diff
git add .
git commit -m "deploying container app"
git push origin dev
```

Now our Action will simply generate a Terraform Plan. This shows us what Terraform is planning to make for us. You can review it in the output of the Action run.

We need to make a PR from `dev` -> `main` to deploy our infrastructure and app. Complete the PR and now our Action will deploy our image to an Azure Container Apps. Follow along and then:

Our url is the "fqdn" that was output by terraform. Copy that and launch it in the browser. Congrats!
