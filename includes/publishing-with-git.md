# Publishing from Source Control to Azure Web Sites

Azure Web Sites supports continuous deployment from source code control and repository tools like BitBucket, CodePlex, Dropbox, Git, GitHub, Mercurial, and TFS. You can use these tools to maintain the content and code for your web site, and then quickly and easily push changes to your site when you want.

In this article, you will learn how to use Git to publish directly from your local computer to an Azure Web Site (in Azure, this method of publishing is called **Local Git**). You will also learn how to enable continuous deployment from repository web sites like BitBucket, CodePlex, DropBox, GitHub, or Mercurial. For information about using TFS for continuous deployment, see [Continuous delivery to Azure using Visual Studio Online].

> [WACOM.NOTE] Many of the Git commands described in this article are performed automatically when creating a Web Site using the <a href="/en-us/develop/nodejs/how-to-guides/command-line-tools/">Azure Command-Line Tools for Mac and Linux</a>.

The task includes the following steps:

* [Install Git](#Step1)
* [Create a local repository](#Step2)
* [Add a web page](#Step3)
* [Enable the web site repository](#Step4)
* [Deploy your project](#Step5)
	* [Pushing local files to Azure (Local Git)](#Step6)
	* [Deploy files from a repository web site like BitBucket, CodePlex, Dropbox, GitHub, or  Mercurial](#Step7)
* [Troubleshooting](#Step8)

<h2><a id="Step2"></a>Installing Git</h2>

The steps required to install Git vary between operating systems. See [Installing Git] for operating system specific distributions and installation guidance.

> [WACOM.NOTE] On some operating systems, both a command-line and GUI version of Git will are available. The instructions provided in this article use the command-line version.

<h2><a id="Step2"></a>Create a local repository</h2>

Perform the following tasks to create a new Git repository.

1. Create a directory named MyGitRepository to contain your Git repository and web site files.

2. Open a command-line, such as **GitBash** (Windows) or **Bash** (Unix Shell). On OS X systems you can access the command-line through the **Terminal** application.

3. From the command line, change to the MyGitRepository directory.

		cd MyGitRepository

4. Use the following command to initialize a new Git repository:

		git init

	This should return a message such as **Initialized empty Git repository in [path]**.

<h2><a id="Step3"></a>Add a web page</h2>

Azure Web Sites support applications created in a variety of programming languages. For this example, you will use a static .html file. For information on publishing web sites in other programming languages to Azure, see the [Azure Developer Center].

1. Using a text editor, create a new file named **index.html** in the root of the Git repository (the MyGitRepository directory that you created earlier).

2. Add the following text as the contents for the index.html file and save it.

		Hello Git!

3. From the command-line, verify that you are in the root of your Git repository. Then use the following command to add the **index.html** file to the repository:

		git add index.html 

	> [WACOM.NOTE] You can find help for any git command by typing -help or --help after the command. For example, for parameter options for the add command, type ‘git add -help’ for command-line help or ‘git add --help' for more detailed help.

4. Next, commit the changes to the repository by using the following command:

		git commit -m "Adding index.html to the repository"

	You should see output similar to the following:

		[master (root-commit) 369a79c] Adding index.html to the repository
		 1 file changed, 1 insertion(+)
		 create mode 100644 index.html

<h2><a id="Step4"></a>Enable the web site repository</h2>

Perform the following steps to enable a Git repository for your web site by using the Azure portal:

1. Login to the [Azure portal].

2. On the left of the page, select **Web Sites**, and then select the web site for which you want to enable a repository.

	![An image displaying a selected web site][portal-select-website]

3. Select the **DASHBOARD** tab.

4. In the **quick glance** section, select **Set up deployment from source control**.  The following **SET UP DEPLOYMENT** dialog appears.

	![git-WhereIsYourSourceCode][git-WhereIsYourSourceCode]

4. Choose **Local Git**, and then click the **Next** arrow.

	
5. After a short delay, you should be presented with a message that your repository is ready. 

	![git-instructions][git-instructions]

<h2><a id="Step5"></a>Deploy your project</h2>

<h3><a id="Step6"></a>Pushing local files to Azure (Local Git)</h3>

At this point, the portal displays instructions for initializing a local repository and adding files. You have already done this in the previous steps in this topic. However, if you have not set up your deployment credentials, you must follow step 3 in the instructions. This directs you follow a link labeled **Reset your deployment credentials**.

Use the following steps to publish your web site to Azure using Local Git:

1. Using the command-line, verify that you are in the root of your local Git repository that contains the previously created index.html file.

2. Copy git remote add command listed in step 3 of the instructions returned by the portal. It will look similar to the following command:

		git remote add azure https://username@needsmoregit.scm.azurewebsites.net:443/NeedsMoreGit.git

    > [WACOM.NOTE] The **remote** command adds a named reference to a remote repository. In this example, it creates a reference named 'azure' for your Azure Web Site repository.

1. Use the following from the command-line to push the current repository contents from the local repository to the 'azure' remote:

		git push azure master

	You will be prompted for the password you created earlier when you reset your deployment credentials in the portal. Enter the password (note that Gitbash does not echo asterisks to the console as you type your password). You should see output similar to the following:

		Counting objects: 6, done.
		Compressing objects: 100% (2/2), done.
		Writing objects: 100% (6/6), 486 bytes, done.
		Total 6 (delta 0), reused 0 (delta 0)
		remote: New deployment received.
		remote: Updating branch 'master'.
		remote: Preparing deployment for commit id '369a79c929'.
		remote: Preparing files for deployment.
		remote: Deployment successful.
		To https://username@needsmoregit.scm.azurewebsites.net:443/NeedsMoreGit.git
		* [new branch]		master -> master

	> [WACOM.NOTE] The repository created for your Azure web site expects push requests to target the <strong>master</strong> branch of its repository, which will then be used as the content of the web site.

2. In the portal, click the **BROWSE** link at the bottom of the portal to verify that the **index.html** has been deployed. A page containing 'Hello Git!' will appear.

	![A webpage containing 'Hello Git!'][hello-git]

3. Using a text editor, change the **index.html** file so that it contains 'Yay!', and then save the file.

4. Use the following commands from the command-line to **add** and **commit** the changes, and then **push** the changes to the remote repository:

		git add index.html
		git commit -m "Celebration"
		git push azure master

	Once the **push** command has completed, refresh the browser (you may have to press Ctrl+F5 for the browser to properly refresh) and note that the content of the page now reflects the latest commit change.

	![A webpage containing 'Yay!'][yay]

<h3><a id="Step7"></a>Deploy files from a repository web site like BitBucket, CodePlex, Dropbox, GitHub, or Mercurial</h3>

Pushing local files to Azure by using Local Git allows you to manually push updates from a local project to your Azure Web Site, while deploying from BitBucket, CodePlex, Dropbox, GitHub, or  Mercurial results in a continuous deployment process where Azure will pull in the most recent updates from your project.

While both methods result in your project being deployed to an Azure Web Site, continuous deployment is useful when you have multiple people working on a project and want to ensure that the latest version is always published regardless of who made the most recent update. Continuous deployment is also useful if you are using one of the above mentioned tools as the central repository for your application.

Deploying files from either GitHub, CodePlex, or BitBucket requires that you have published your local project to one of these services. For more information on publishing your project to these services, see [Create a Repo (GitHub)], [Using Git with CodePlex], [Create a Repo (BitBucket)], [Using Dropbox to Share Git Repositories], or [Quick Start - Mercurial].

1. First put your web site files into the selected repository that will be used for continuous deployment.

2. In the Azure Portal for your web site,  go to the **DASHBOARD** tab. In the **quick glance** section, select **Set up deployment from source control**.  The **Set Up Deployment dialog** appears that asks **Where is your source code?**. 

2. Choose the source control method that you want to use for continuous deployment.
	
3. When prompted, enter your credentials for the service you selected.

4. After you have authorized Azure to access your account, you will be prompted with a list of repositories. 

	![git-ChooseARepositoryToDeploy][git-ChooseARepositoryToDeploy]
  
5. Select the repository that you want to associate with your Azure Web Site. Click the checkmark to continue.

	> [WACOM.NOTE] When enabling continuous deployment with GitHub or BitBucket, both public and private projects will be displayed.

6. Azure creates an association with the selected repository, and pulls in the files from the master branch. After this process completes, the **deployment history** on the **Deployments** page will show an **Active Deployment** message like the following:

	![git-githubdeployed][git-githubdeployed]

7. At this point your project has been deployed from your repository of choice to your Azure web site. To verify that the site is active, click the **Browse** link at the bottom of the portal. The browser should navigate to the web site.

8. To verify that continuous deployment is occurring, make a change to your project and then push the update to the repository you have associated with this web site. Your web site should update to reflect the changes shortly after the push to the repository completes. You can verify that it has pulled in the update on the **Deployments** page of your Web Site.

	![git-GitHubDeployed-Updated][git-GitHubDeployed-Updated]


<h4>How continuous deployment works</h4>
Continuous deployment works by providing the **DEPLOYMENT TRIGGER URL** found in the **deployments** section of your site's **Configure** tab.

![git-DeploymentTrigger][git-DeploymentTrigger]

When updates are made to your repository, a POST request is sent to this URL, which notifies your Azure Web Site that the repository has been updated. At this point it retrieves the update and deploys it to your web site.

<h4>Specifying the branch to use</h4>

When you enable continuous deployment, it will default to the **master** branch of the repository. If you want to use a different branch, perform the following steps:

1. In the portal, select your web site and then select **CONFIGURE**.

2. In the **deployments** section of the page, enter the branch you wish to use in the **BRANCH TO DEPLOY** field, and then hit enter. Finally, click **SAVE**.

	Azure should immediately begin updating based on changes to the new branch.

<h4>Disabling continuous deployment</h4>

Continuous deployment can be disabled from the Azure **Dashboard**. Under the **quick glance** section, choose the option to disconnect from the repository that you are using:

![git-DisconnectFromGitHub][git-DisconnectFromGitHub]	

After answering **Yes** to the confirmation message, you can return to **quick glance** and click **Set up deployment from source control** if you would like to set up publishing from another source.

<h2><a id="Step8"></a>Troubleshooting</h2>

The following are errors or problems commonly encountered when using Git to publish to an Azure web site:

****

**Symptom**: Unable to access '[siteURL]': Failed to connect to [scmAddress]

**Cause**: This error can occur if the web site is not up and running.

**Resolution**: Start the web site in the Azure portal. Git deployment will not work unless the web site is running. 


****

**Symptom**: Couldn't resolve host 'hostname'

**Cause**: This error can occur if the address information entered when creating the 'azure' remote was incorrect.

**Resolution**: Use the `git remote -v` command to list all remotes, along with the associated URL. Verify that the URL for the 'azure' remote is correct. If needed, remove and recreate this remote using the correct URL.

****

**Symptom**: No refs in common and none specified; doing nothing. Perhaps you should specify a branch such as 'master'.

**Cause**: This error can occur if you do not specify a branch when performing a git push operation, and have not set the push.default value used by Git.

**Resolution**: Perform the push operation again, specifying the master branch. For example:

	git push azure master

****

**Symptom**: src refspec [branchname] does not match any.

**Cause**: This error can occur if you attempt to push to a branch other than master on the 'azure' remote.

**Resolution**: Perform the push operation again, specifying the master branch. For example:

	git push azure master

****

**Symptom**: Error - Changes commited to remote repository but your web site not updated.

**Cause**: This error can occur if you are deploying a Node.js application containing a package.json file that specifies additional required modules.

**Resolution**: Additional messages containing 'npm ERR!' should be logged prior to this error, and can provide additional context on the failure. The following are known causes of this error and the corresponding 'npm ERR!' message:

* **Malformed package.json file**: npm ERR! Couldn't read dependencies.

* **Native module that does not have a binary distribution for Windows**:

	* npm ERR! \`cmd "/c" "node-gyp rebuild"\` failed with 1

		OR

	* npm ERR! [modulename@version] preinstall: \`make || gmake\`


## Additional Resources

* [How to use PowerShell for Azure]
* [How to use the Azure Command-Line Tools for Mac and Linux]
* [Git Documentation]

[Azure Developer Center]: http://www.windowsazure.com/en-us/develop/overview/
[Azure portal]: http://manage.windowsazure.com
[Git website]: http://git-scm.com
[Installing Git]: http://git-scm.com/book/en/Getting-Started-Installing-Git
[How to use PowerShell for Azure]: http://www.windowsazure.com/en-us/develop/nodejs/how-to-guides/powershell-cmdlets/
[How to use the Azure Command-Line Tools for Mac and Linux]: /en-us/develop/nodejs/how-to-guides/command-line-tools/
[Git Documentation]: http://git-scm.com/documentation

[portal-select-website]: ./media/publishing-with-git/git-select-website.png
[git-WhereIsYourSourceCode]: ./media/publishing-with-git/git-WhereIsYourSourceCode.png
[git-instructions]: ./media/publishing-with-git/git-instructions.png
[portal-deployment-credentials]: ./media/publishing-with-git/git-deployment-credentials.png

[git-ChooseARepositoryToDeploy]: ./media/publishing-with-git/git-ChooseARepositoryToDeploy.png
[hello-git]: ./media/publishing-with-git/git-hello-git.png
[yay]: ./media/publishing-with-git/git-yay.png
[git-githubdeployed]: ./media/publishing-with-git/git-GitHubDeployed.png
[git-GitHubDeployed-Updated]: ./media/publishing-with-git/git-GitHubDeployed-Updated.png
[git-DisconnectFromGitHub]: ./media/publishing-with-git/git-DisconnectFromGitHub.png
[git-DeploymentTrigger]: ./media/publishing-with-git/git-DeploymentTrigger.png
[Create a Repo (GitHub)]: https://help.github.com/articles/create-a-repo
[Using Git with CodePlex]: http://codeplex.codeplex.com/wikipage?title=Using%20Git%20with%20CodePlex&referringTitle=Source%20control%20clients&ProjectName=codeplex
[Create a Repo (BitBucket)]: https://confluence.atlassian.com/display/BITBUCKET/Create+an+Account+and+a+Git+Repo
[Quick Start - Mercurial]: http://mercurial.selenic.com/wiki/QuickStart
[Using Dropbox to Share Git Repositories]: https://gist.github.com/trey/2722927
[Continuous delivery to Azure using Visual Studio Online]: http://www.windowsazure.com/en-us/develop/net/common-tasks/publishing-with-tfs/
