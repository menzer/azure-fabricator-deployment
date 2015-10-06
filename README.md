# Azure Fabricator Deployment

Scripts and instructions for deploying [Fabricator](http://fbrctr.github.io) with Azure App Service

## Why do I need this?

Azure Web Service runs on Windows. In Windows, the maximum allowed length for a file path is 260 characters: npm@2, the version currently bundled with node, will nest all dependencies of dependencies in a folder structure that can easily blow past this limit. npm@3, currently in beta as of writing this, will install all dependencies and their dependencies in a flat structure, mitigating this issue.

Additionally, Azure Web apps are designed to build and run node projects that start a server. The Fabricator project builds a static html site, so a few additional deployment and configuration tweaks are necessary to build and serve the static directory.

## Instructions

### On your system

1. If you're starting a new Fabricator project, follow the instructions in the [Fabricator documention](http://fbrctr.github.io/docs/) to create a new instance of Fabricator in your root project.

2. Copy the `.deployment`,  `deploy.sh`, and `.gitignore` files from the `files` directory in this repo to the root directory of your Fabricator project. Some of the files are hidden files, so a simple way to do this is executing the following command from the root directory of this repo: `cp -R files/. ../fabricator2/` (replace the second argument with the appropriate absolute or relative filepath, but keep the trailing slash). If you have an existing project with an existing `.gitignore`, instead of overriding it copy the lines from this version into your `.gitignore`.

3. Commit the changes to git, then merge into your desired deployment branch. You can optionally push to a remote repo such as GitHub.

### In Azure
(Note: these instructions are based on the Azure Preview Portal)

1. In the Azure Portal, create a new Web App.

2. In your new Web App, go to Settings -> Application settings, find the section labeled **App Settings** and:

  - change the value of `WEBSITE_NODE_DEFAULT_VERSION` to `4.1.0`

  - add the key-value pair `SCM_COMMAND_IDLE_TIMEOUT`: `500` (this prevents the deployment from timing out during the `npm install`)

3. In the same Application settings blade, find the section labeled **Virtual applications and directories** and change the value of the default Virtual directory `/` to `site\wwwroot\dist`

6. Go to Settings -> Continuous Deployment and add a Continuous Deployment. Follow the steps to link it to your git repo via GitHub, a local repo, or other applicable option.

7. If you've linked it to a remote repository such as GitHub, the build should start automatically. If you linked to a local git repository, you'll need to manually add your Azure Web App as a remote repo and push to it: [instructions are here](https://azure.microsoft.com/en-us/documentation/articles/web-sites-publish-source-control/#Step5). When the build succeeds, you can view your new Fabricator instance by clicking "Browse" in the Azure Portal.

## Other tips

- If you're deploying via a remote repository such as GitHub, you can view a stream of the build process output by enabling logging via Settings -> Diagnostic logs -> Application logging, and accessing the stream at Tools -> Log stream.
- If for some reason something has gone wrong and you think your node environment is borked, you can try again with a fresh environment by creating a Deployment Slot via Settings -> Deployment slots -> Add Slot. A deployment slot is just a new web app. Follow the instructions above, and if your build is successful, you can swap it with the primary VM via the top-level
**Swap** action in your Web App's primary blade.
