# Dealing with Proxies

This document will help you get set up to use `npm`, `bower`, `gem`, and `git` while going through the proxy.

Because bash does not adhere to the global proxy settings for our network connection, we're going to need to configure our environment to use the proxy, and due to inconsistent enforcement of proxy connections amonst these various tools, we cannot just set the `HTTP_PROXY` and `HTTPS_PROXY` environemnt variables and call it a day. There are also several settings related to how TLS is handled due to the way our proxy implements it's certificate.

We'll also need to provide some GitHub-specific url rewriting for `git` so that we can reliably use `npm` and `bower` to install packages.

As an added bonus, we'll go over configuring some popular text editors to use the proxy for their package management features.

### Table of Contents

1. [Before We Begin](#before-we-begin)
1. [.bash_profile](#bash-profile)
1. [.npmrc](#npmrc)
1. [.bowerrc](#bowerrc)
1. [.gitconfig](#gitconfig)
1. [.gemrc](#gemrc)
1. [Other Miscellaneous Notes](#other-misc-notes)
1. [Text Editors](#text-editors)

<a href="javascript:void(0);" id="before-we-begin" name="before-we-begin"></a>
## Before We Begin

If you don't have the following files (all located in your home directory, `~/`), then go ahead and create them now:

- `~/.bash_profile`
- `~/.npmrc`
- `~/.bowerrc`
- `~/.gitconfig`
- `~/.gemrc`

You can create each file using the `touch` command, for example:  `touch ~/.bash_profile` will generate an empty `.bash_profile` file in your home directory, `~/`.

There are example templates for each of these files included in the `templates/dotfiles` directory.

We'll go over adding the necessary configuration settings to your dotfiles via a text editor and via the command line.

There are also example configurations for several popular editors (*Sublime Text, GitHub Atom, Adobe Brackets, and Visual Studio Code*) included in the `templates/editors/` directory. Information about configuring these editors can be found in the [Text Editors](#text-editors) section.

<a href="javascript:void(0);" id="bash-profile" name="bash-profile"></a>
## .bash_profile

Your `~/.bash_profile` controls various environment variables and functions available to your shell. In this case we need to set up the environment variables `HTTP_PROXY` and `HTTPS_PROXY`.

There are some `npm` packages that will also require that `node-gyp` download and utilize resources from nodejs.org, but `node-gyp` won't use the TLS settings we are going to configure in our `.npmrc` file below, so you may also need to set the environemnt variable `NODE_TLS_REJECT_UNAUTHORIZED`.

**NOTE**: You can forego setting the `NODE_TLS_REJECT_UNAUTHORIZED` environment variable and opt to just toggle it when you encounter a TLS error with `node-gyp` by running the following command and then performing your `npm install` again: `set NODE_TLS_REJECT_UNAUTHORIZED=0`. This is probably the safer option, but is also more tedious.

After updating your `~/.bash_profile` you will need to `source` it with `source ~/.bash_profile` for the changes to take effect.

**NOTE**: In some cases you may be using `~/.profile` or `~/.bashrc` instead of `~/.bash_profile`. This document will not go into the differences or the situations in which you'd want to use one over another. That decision is entirely up to you, and you can substitute the dotfile that you are using to `export` your environment variables for `~/.bash_profile` in the instructions below.

### Text Editor

You'll need to add the following lines to your `~/.bash_profile`:

```shell
#proxy config
export HTTP_PROXY="[proxy_url]:[proxy_port]"
export HTTPS_PROXY="[proxy_url]:[proxy_port]"

#node-gyp TLS
export NODE_TLS_REJECT_UNAUTHORIZED=0
```

### Command Line

Run the following commands in your Terminal:

- `echo 'export HTTP_PROXY="[proxy_url]:[proxy_port]"' >> ~/.bash_profile`
- `echo 'export HTTPS_PROXY="[proxy_url]:[proxy_port]"' >> ~/.bash_profile`
- `echo 'export NODE_TLS_REJECT_UNAUTHORIZED=0' >> ~/.bash_profile`

<a href="javascript:void(0);" id="npmrc" name="npmrc"></a>
## .npmrc

Your `~/.npmrc` controls configuration options for `npm` and allows you to specify certain settings at a global level. In this case we need to set up the `proxy` and `https-proxy` options and also disable the `strict-ssl` and enable the `unsafe-perm` options.

NPM's documentation has [more information regarding .npmrc files](https://docs.npmjs.com/files/npmrc).

### Text Editor

You'll need to add the following 3 lines to your `~/.npmrc`:

```shell
registry=http://registry.npmjs.org/
proxy=[proxy_url]:[proxy_port]/
https-proxy=[proxy_url]:[proxy_port]/
strict-ssl=false
unsafe-perm=true
```

### Command Line

Run the following commands in your Terminal:

- `npm config set registry http://registry.npmjs.org/`
- `npm config set proxy [proxy_url]:[proxy_port]/`
- `npm config set https-proxy [proxy_url]:[proxy_port]/`
- `npm config set strict-ssl false`
- `npm config set unsafe-perm true`

<a href="javascript:void(0);" id="bowerrc" name="bowerrc"></a>
## .bowerrc

Your `~/.bowerrc` controls configuration options for `bower` and allows you specify certain settings at a global level. In this case we need to set up the `registry`, `proxy`, and `https-proxy` options.

Bower's documentation has [more information regarding .bowerrc files](http://bower.io/docs/config/).

### Text Editor

You'll need to add the following 3 lines to your `~/.bowerrc`:

```json
  "registry": "http://bower.herokuapp.com",
  "proxy":"[proxy_url]:[proxy_port]",
  "https-proxy":"[proxy_url]:[proxy_port]"
```

**NOTE**: `.bowerrc` files are a JSON object so you must have an opening `{` and closing `}` in your file and the file must be valid JSON

### Command Line

There is not currently a way to generate this configuration via the command line with bower, but the options can be passed to a bower command as CLI arguments like so: `--config.proxy=[proxy_url]:[proxy_port]`.

We could try to approach this similarly to the Command Line method for updating your `~/.bash_profile` or `~/.gemrc`, but since this file has to be valid JSON and it's more trouble than it's worth to pass JSON via the Command Line, I recommend just editing it manually.

<a href="javascript:void(0);" id="gitconfig" name="gitconfig"></a>
## .gitconfig

Your `~/.gitconfig` controls configuration options for `git` and allows you to specify certain settings at a global level. In this case we need to set up the `http.proxy` and `https.proxy` options and also set up some rewrite rules for SSH and git:// protocol connections to GitHub.

Git-SCM has [more information regarding .gitconfig files](https://git-scm.com/docs/git-config).

### Text Editor

You'll need to add the following lines to your `~/.gitconfig`:

```gitconfig
[http]
  proxy = [proxy_url]:[proxy_port]
[https]
  proxy = [proxy_url]:[proxy_port]
[url "https://github.com/"]
  insteadOf = git@github.com:
[url "https://github.com"]
  insteadOf = git://github.com
[url "https://bitbucket.org/"]
  insteadOf = git@bitbucket.org:
[url "https://bitbucket.org"]
  insteadOf = git://bitbucket.org
```

### Command Line

Run the following commands in your Terminal:

- `git config --global http.proxy [proxy_url]:[proxy_port]`
- `git config --global https.proxy [proxy_url]:[proxy_port]`
- `git config --global url"https://github.com/".insteadOf git@github.com:`
- `git config --global url"https://github.com".insteadOf git://github.com`
- `git config --global url"https://bitbucket.org/".insteadOf git@bitbucket.org:`
- `git config --global url"https://bitbucket.org".insteadOf git://bitbucket.org`

<a href="javascript:void(0);" id="gemrc" name="gemrc"></a>
## .gemrc

Your `~/.gemrc` file controls configuration options for `gem` and allows you to specify certain settings at a global level. In this case we need to set up the `http-proxy` option and then remove an `https://` source and replace it with an `http://` source because the proxy can interfere with some SSL/TLS connections.

RubyGems has [more information regarding gem environment options](http://guides.rubygems.org/command-reference/).

**NOTE**: Be sure to follow the [One Last Step](#one-last-step) section below before moving on.

### Text Editor

You'll need to add the following line to your `~/.gemrc`:

```yaml
http-proxy: [proxy_url]:[proxy_port]
```

### Command Line

Run the following commands in your Terminal:

- `echo 'http-proxy: [proxy_url]:[proxy_port]/' >> ~/.gemrc`

<a href="javascript:void(0);" id="one-last-step" name="one-last-step"></a>
### One Last Step

Regardless of which approach you took to setting up your `~/.gemrc` file, make sure you run the commands below in your Terminal

- `gem source -r https://rubygems.org/`
- `gem source -a http://rubygems.org/`

<a href="javascript:void(0);" id="other-misc-notes" name="other-misc-notes"></a>
## Other Miscellaneous Notes

### HTTP Authentication

Technically, you could add http authentication to your proxy URL in all of these isntances, which will make the proxy automatically authenticate if your authenticated connection has been idle for too long, but this comes with some serious caveats:

- Password must be stored in plaintext in these files
- Password must be updated in several places whenever you change it
- Configuration files cannot easily be shared with others because they contain your user id and password

If you want to go this route, the URL will look like this:  `http://[user]:[pass]@10.43.196.132:8080` replaced `[user]` with your user ID and `[pass]` with your current password. However, this is not recommended.

<a href="javascript:void(0);" id="text-editors" name="text-editors"></a>
## Text Editors

If your editor has a plugin or update system, you'll need to configure it to run through the web proxy, as well.

Below we'll go through the steps to set up the proxy configuration with each editor.

Because menu locations can vary depending on Operating System, we will not be explicitly stating the menu tree, but will generally refer to the Settings or Preferences menu depending on the language that the application uses.

#### Editors

- [Sublime Text](#sublime-text)
- [Atom](#atom)
- [Brackets](#brackets)
- [VS Code](#vs-code)

<a href="javascript:void(0);" id="sublime-text" name="sublime-text"></a>
### Sublime Text Package Control

Sublime Text's [Package Control](https://packagecontrol.io/) is the plugin that opens up the world of plugins to your editor and, as such, if you want to easily install and update plugins, you'll need it working with the Web Proxy.

In Sublime Text, pull up the Preferences menu and then navigate to: `Package Settings > Package Control > Settings - User`.

Add the following lines to your `Package Control.sublime-settings` file:

```json
  "http_proxy": "[proxy_url]:[proxy_port]",
  "https_proxy": "[proxy_url]:[proxy_port]"
```

**NOTE**: You may need to do some work to get Package Control to install through your corporate proxy, this basically just entails editing the `urllib.request.ProxyHandler()` to have your proxy information passed into it. You can find some basic information about how to acheive this in the following aritcle: [Install Package Control from behind a proxy](http://michael.laffargue.fr/blog/2014/01/23/install-package-control-behind-proxy-sublimetext3/).

<a href="javascript:void(0);" id="atom" name="atom"></a>
### GitHub Atom apm

Atom's `apm` is very similar to `npm`, but doesn't use the proxy settings from your `.npmrc`. Luckily, setting up an `.apmrc` file is just as easy.

If you don't have an `~/.atom/.apmrc` file, then go ahead and create one now.

Add the following lines to your `~/.atom/.apmrc` file:

```shell
proxy=[proxy_url]:[proxy_port]/
https-proxy=[proxy_url]:[proxy_port]/
strict-ssl=false
```

<a href="javascript:void(0);" id="brackets" name="brackets"></a>
### Adobe Brackets

Brackets will need to run through the Web Proxy to install extensions.

In Brackets, pull up the Preferences `brackets.json` file and add the following line:

```json
  "proxy": "[proxy_url]:[proxy_port]/"
```

<a href="javascript:void(0);" id="vs-code" name="vs-code"></a>
### Visual Studio Code

Visual Studio Code will need to run through the Web Proxy to install extensions.

In Visual Studio Code, pull up the Preferences menu and then navigate to:  `User Settings`.

Add the following lines to your `settings.json` file:

```json
  "http.proxy": "[proxy_url]:[proxy_port]/",
  "http.proxyStrictSSL": false
```

**Note**: You'll need to have some sort of [credential caching](https://help.github.com/articles/caching-your-github-password-in-git/) for your SSH Key password if you want to use Visual Studio Code's `git` integration with your remote repositories.
