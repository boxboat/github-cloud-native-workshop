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

## Create a repo, a Personal Access Token (PAT) and launch Codespaces

Link: [Workshop repo](https://github.com/BoxBoat-Codespaces/codespaces-workshop)

Create a new repo from the template linked above. Private works fine.

Create a PAT inside your GitHub profile settings > Developer settings > Personal access tokens. 

The destination should be a **Codespaces enabled Org** and your company likely is providing one for you. You should prepend the repo name with your username, so if your username is dkoch, your repo should be `dkoch-codespaces-workshop`. Below is a screenshot of how to launch Codespaces from the repository. If you do not see the Codespaces selection OR cannot "Create codespace..." reach out to the Orgs admin. 

![Screen Shot 2022-08-09 at 1 41 56 PM](https://user-images.githubusercontent.com/25825925/183722911-d46fa14d-b9dc-45a1-b1cb-499616caf8c4.png)


First thing we need to do is to make a PAT. You do this by clicking on your user bubble in the upper right hand of the screen, then hitting ctrl and clicking "Settings" to open it in a new tab, then "Developer Settings" all the way at the bottom of the menu. Personal Access Tokens is what we need now, so click that and then "Generate a new token". Give it a name, let it expire in 30 days or less, and check the box next to "write: packages". Scroll down and click the green button, then copy the token it gives you.

Go back to your repo tab and click Settings at the top. Under Security, expand Secrets, then choose Actions. Click "New repository secret" and name it "MY_PAT". Paste the PAT you copied into the value box. Click "Add secret".

Now that you have a PAT, a secret and a repo to work from, let's launch Codespaces. Click over to the Code tab at the top of the screen, then the green "Code" button, "Create codespaces on main" in the Codespaces tab here. After a few seconds, you can choose to open that in the browser, or in your Visual Studio Code desktop app. 

## Let's modify our app
Open a terminal in vscode. This can be done in the UI, or with ctrl+`. 

Let's start by creating a 'dev' branch:

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
In vscode, open `Program.cs`. You can see this is a very, very simple API. We have a single `hello` endpoint and several endpoints which return ASCII art. Modify the output in the `string hello()` method, and add a new endpoint at `/blinky` like this:

```c#
app.MapGet("/blinky", () => ascii("blinky"));
```

Let's try it out again. Hit ctrl+c in the terminal to kill our last process, then run it again (hit your up key). Visit the site again and confirm your changes. Hit ctrl+c in the terminal when you are done.

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

Now that we've built a containerized application in Codespaces, how do we get it deployed to the cloud? Well, the first step is a CI/CD process that builds an image and stores it in the GitHub Container Registry.

 **Step 1: Poke around the dotnet-build-push Action**

Open the file at `.github/workflows/dotnet-build-push.yml` and take a look at what's happening. We're building a dotnet app, then building a docker image using that build artifact. We'll trigger it next.

 **Step 2: Trigger the Action**

Now in the Codespaces terminal again, let's stage, commit and push our changes to our app.

```shell
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

Back in our repo tab, we can click on Actions at top and see our Action is running. Take a look at it, you can see the output of all the steps.

 **Step 3: Trigger the whole Action**

Our Action only builds the dotnet app Artifact on the `dev` branch. To build the container, we must create a PR into `main`. Create a PR from `dev` into `main`, notice the checks and complete the PR. Our Action now runs again, this time creating an image which is stored in our repo as a package. 

On the "Code" tab of the repo, you'll see our package. Click on it, and take note and copy the url to the image from the top section on this page. It starts with `ghcr.io/`

## Using GitHub Actions to Deploy Containers to Azure Container Apps

 **Step 1: Look at the terraform Action**

An Azure Container App is essentially a containerized application that runs on top of a Microsoft-managed Kubernetes instance. All you need to worry about is your app, not maintaining the infrastructure. Getting it deployed is also much simpler. Well, we've already taken care of the app, this should be easy right?

Yep.

Open the file at `.github/workflows/terraform.yml` and take a look. We've got two stages again like in the last Action, but this one is going to be responsible for deploying infrastructure and our app. The first state will simply do a 'plan', while the second stage, triggered after a Pull Request, will deploy. This is a basic example of a good Terraform workflow that allows for ample review and collaboration.

 **Step 2: Edit our Terraform**

The Terraform code, in the `terraform/` directory, that will deploy our infrastructure and app consists of 3 files. `main.tf` contains the terraform configuration bits. We don't need to worry about that here. `container_app.tf` deploys a resource group, then a Log Analytics workspace, a Container App Environment and a Container App for us. `backend.tf` is a special file that configures how Terraform keeps track of what it makes.

In Codespaces, let's edit these latter two files. The first is responsible for storing our _state_ so that terraform can always be aware of any changes made. Edit `terraform/backend.tf` and give the __key__ a unique name by using your username. For example, my username is 'dkoch'. __Note, if your username has any special characters like "-" or "_", omit those.__ We only want alphanumeric characters here:

```diff
terraform {
  backend "azurerm" {
    resource_group_name  = "codespaces-demo-resources"
    storage_account_name = "codespacesstate1"
    container_name       = "codespacesstate"
--    key                  = "<USERNAME>.state"
++    key                  = "dkoch.state"
  }
}
```

 Then, edit `terraform/container_app.tf` and change lines `6`, `11`, `37`, `53` and `54` like so. You can use a find and replace in vscode with ctrl + f to do all of these at one time too:

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

__Make sure your image value (line 53) is all lowercase__. Reminder that this value is what we copied earlier from GitHub, on the page we get when we click on our package. In the code view, access the packages by clicking on the packages 

![Screen Shot 2022-08-09 at 1 56 02 PM](https://user-images.githubusercontent.com/25825925/183725577-9c388a78-8c9e-4d71-80f0-66e0ccec684e.png)
![Screen Shot 2022-08-09 at 1 49 41 PM](https://user-images.githubusercontent.com/25825925/183725265-6318bdf1-ce74-4ff3-b406-2507d028409b.png)




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
  (use "git add <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)
        modified:   terraform/backend.tf
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

Our url is the "fqdn" that was output by terraform at the bottom of the Terraform Apply step. Copy that and launch it in the browser. Congrats!

Any changes you want to make to your app can now follow this general flow. Code changes will result in a new build, and a new image with a new _tag_. Update the tag in your terraform, `container-app.tf` and then submit a PR. That's the flow.
