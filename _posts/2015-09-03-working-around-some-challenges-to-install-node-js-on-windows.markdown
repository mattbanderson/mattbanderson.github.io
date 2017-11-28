---

title: Working Around Some Challenges to Install Node.js on Windows
date: '2015-09-03 21:54:36'
---

First things first: download the MSI file for Windows from [nodejs.org](https://nodejs.org). It should detect your operating system and pick the right version for you.

Try double-clicking the MSI file to begin the install; however, depending on your PC setup and user account privileges, it may not be that easy. If you double-click the MSI and nothing happens, a spinning progress wheel hangs, or Windows Explorer freezes, you probably need elevated privileges to install it.

Unfortunately, if you have no way of acquiring those privileges, you are out of luck and will need to contact someone who can provide those privileges for you.

Hypothetically, if you have some way to get those privileges, you can typically shift-right-click on a file and select "Run as administrator" from the context menu. Unfortunately, this is not an option for MSI files (or at least it's not on my machine).

Have no fear! There is an easy way around this. Even though "Run as administrator" is not an option, "Run as different user" should be. Choose this and enter the credentials of an account with admin privileges. That may be the account you are logged in as&mdash;that's OK&mdash;just re-enter those same credentials. 

Voilà! Your install should begin! Select the directory where you would like it to go and continue through the wizard. In a few minutes, Node.js will be installed.

Once installation is complete, you may want to add the installation directory to your PATH environment variable if it was not added automatically by the installer.

To verify installation was successful, open a command prompt and type 

	node --version 

and hit enter. If a version number appears, Node was installed successfully!

If you've made it past the permissions hurdle, you should be mostly in the clear. Nevertheless, a couple other tweaks may be necessary: changing the package install location and configuring proxy settings.

With Node installed, one of the first things you will want to do is install packages via [npm](https://www.npmjs.com/) (the Node package manager). 

But before you do that, you may want to change where Node installs its packages. By default, they go in your user profile, and perhaps your user profile directory is very small. If this is fine, leave it alone. For me, I would prefer to have them installed elsewhere.

To change where they are installed, navigate to the directory where Node is installed (something like [Drive:/path/nodejs/]) and look for the npmrc file in /node_modules/npm. Change the "prefix" value to the location where you want your Node modules installed.

Now you are ready to install packages (maybe)! If you're like me, you type 

	npm install [package-name]

sit back, wait, and nothing happens. 

After a few minutes, Node produces a handy error message indicating something is wrong on my end, most likely a proxy error of some kind. 

Aha! I am behind a proxy, and you may be, too. You'll need to set a pair of proxy config values.

Type 

	npm config set proxy [your-proxy-value] 

and hit enter. Repeat this with 

	npm config set https-proxy [your-https-proxy-value] 

Now you should be ready to go!

Type 

	npm install [package-name] 

and an install should begin! You can use the -g flag to install packages globally, e.g.

	npm install -g [package-name] 

As a general rule, install packages that you will use in a program locally at the root of a project and install those you want to use in the shell or on the command line globally.

Hope this helps you overcome any obstacles you may run into when installing Node.js!
