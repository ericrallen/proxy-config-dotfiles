# Corporate Proxy Information for Front End Developers

This document will help you get set up to use `npm`, `bower`, `gem`, and `git` while going through corporate web proxies. However, if the proxy does not allow connections to the relevant domains, these configuration updates will not help. You will need to get the relevant domains unblocked before being able to use them.

Because bash does not adhere to the global proxy settings for our network connection, we're going to need to configure our environment to use the proxy, and due to inconsistent enforcement of proxy connections amonst these various tools, we cannot just set the `HTTP_PROXY` and `HTTPS_PROXY` environemnt variables and call it a day.

We'll also need to provide some GitHub-specific url rewriting for `git` so that we can reliably use `npm` and `bower` to install packages.

**NOTE**: In the following code examples, replace `[proxy url]` with the URL of your proxy and `[proxy port]` with the port for your proxy.

### Table of Contents

1. [Before We Begin](#before-we-begin)
2. [.bash_profile](#bash-profile)
3. [.npmrc](#npmrc)
4. [.bowerrc](#bowerrc)
5. [.gitconfig](#gitconfig)
6. [.gemrc](#gemrc)
7. [Other Miscellaneous Notes](#other-misc-notes)

<a href="javascript:void(0);" id="before-we-begin" name="before-we-begin"></a>
## Before We Begin

If you don't have the following files (all located in your home directory, `~/`), then go ahead and create them now:

- `~/.bash_profile`
- `~/.npmrc`
- `~/.bowerrc`
- `~/.gitconfig`
- `~/.gemrc`

You can create each file using the `touch` command, for example:  `touch ~/.bash_profile` will generate an empty `.bash_profile` file in your home directory, `~/`.

There are example templates for each of these files included in the `/templates/` directory of this repository.

We'll go over adding the necessary configuration settings to your dotfiles via a text editor and via the command line, where applicable.

<a href="javascript:void(0);" id="bash-profile" name="bash-profile"></a>
## .bash_profile

Your `~/.bash_profile` controls various environment variables and functions available to your shell. In this case we need to set up the environment variables `HTTP_PROXY` and `HTTPS_PROXY`.

After updating your `~/.bash_profile` you will need to `source` it with `source ~/.bash_profile` for the changes to take effect.

**NOTE**: In some cases you may be using `~/.profile` or `~/.bashrc` instead of `~/.bash_profile`. This document will not go into the differences or the situations in which you'd want to use one over another. That decision is entirely up to you, and you can substitute the dotfile that you are using to `export` your environment variables for `~/.bash_profile` in the instructions below.

### Text Editor

You'll need to add the following 2 lines to your `~/.bash_profile`:

```shell
export HTTP_PROXY="http://[proxy url]:[proxy port]"
export HTTPS_PROXY="http://[proxy url]:[proxy port]"
```

### Command Line

Run the following commands in your Terminal:

- `echo 'export HTTP_PROXY="http://[proxy url]:[proxy port]"' >> ~/.bash_profile`
- `echo 'export HTTPS_PROXY="http://[proxy url]:[proxy port]"' >> ~/.bash_profile`

<a href="javascript:void(0);" id="npmrc" name="npmrc"></a>
## .npmrc

Your `~/.npmrc` controls configuration options for `npm` and allows you to specify certain settings at a global level. In this case we need to set up the `proxy` and `https-proxy` options and also disable the `strict-ssl` option.

NPM's documentation has [more information regarding .npmrc files](https://docs.npmjs.com/files/npmrc).

### Text Editor

You'll need to add the following 3 lines to your `~/.npmrc`:

```shell
proxy=http://[proxy url]:[proxy port]/
https-proxy=http://[proxy url]:[proxy port]/
strict-ssl=false
```

### Command Line

Run the following commands in your Terminal:

- `npm config set proxy http://[proxy url]:[proxy port]/`
- `npm config set https-proxy http://[proxy url]:[proxy port]/`
- `npm config set strict-ssl false`

<a href="javascript:void(0);" id="bowerrc" name="bowerrc"></a>
## .bowerrc

Your `~/.bowerrc` controls configuration options for `bower` and allows you specify certain settings at a global level. In this case we need to set up the `registry`, `proxy`, and `https-proxy` options.

Bower's documentation has [more information regarding .bowerrc files](http://bower.io/docs/config/).

### Text Editor

You'll need to add the following 3 lines to your `~/.bowerrc`:

```json
  "registry": "http://bower.herokuapp.com",
  "proxy":"http://[proxy url]:[proxy port]",
  "https-proxy":"http://[proxy url]:[proxy port]"
```

**NOTE**: `.bowerrc` files are a JSON object so you must have an opening `{` and closing `}` in your file and the file must be valid JSON

### Command Line

There is not currently a way to generate this configuration via the command line with bower, but the options can be passed to a bower command as CLI arguments like so: `--config.proxy=http://[proxy url]:[proxy port]`.

We could try to approach this similarly to the Command Line method for updating your `~/.bash_profile` or `~/.gemrc`, but since this file has to be valid JSON and it's more trouble than it's worth to pass JSON via the Command Line, I recommend just editing it manually.

<a href="javascript:void(0);" id="gitconfig" name="gitconfig"></a>
## .gitconfig

Your `~/.gitconfig` controls configuration options for `git` and allows you to specify certain settings at a global level. In this case we need to set up the `http.proxy` and `https.proxy` options and also set up some rewrite rules for SSH and git:// protocol connections to GitHub.

Git-SCM has [more information regarding .gitconfig files](https://git-scm.com/docs/git-config).

### Text Editor

You'll need to add the following lines to your `~/.gitconfig`:

```gitconfig
[http]
  proxy = http://[proxy url]:[proxy port]
[https]
  proxy = http://[proxy url]:[proxy port]
[url "https://github.com/"]
  insteadOf = git@github.com:
[url "https://github.com"]
  insteadOf = git://github.com
```

### Command Line

Run the following commands in your Terminal:

- `git config --global http.proxy http://[proxy url]:[proxy port]`
- `git config --global https.proxy http://[proxy url]:[proxy port]`
- `git config --global url"https://github.com/".insteadOf git@github.com:`
- `git config --global url"https://github.com".insteadOf git://github.com`

<a href="javascript:void(0);" id="gemrc" name="gemrc"></a>
## .gemrc

Your `~/.gemrc` file controls configuration options for `gem` and allows you to specify certain settings at a global level. In this case we need to set up the `http-proxy` option and then remove an `https://` source and replace it with an `http://` source because the proxy can interfere with some SSL/TLS connections.

RubyGems has [more information regarding gem environment options](http://guides.rubygems.org/command-reference/).

**NOTE**: Be sure to follow the [One Last Step](#one-last-step) section below before moving on.

### Text Editor

You'll need to add the following line to your `~/.gemrc`:

```yaml
http-proxy: http://[proxy url]:[proxy port]
```

### Command Line

Run the following commands in your Terminal:

- `echo 'http-proxy: http://[proxy url]:[proxy port]/' >> ~/.gemrc`

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
- The Security team at the company will have a heart attack if they find out

If you want to go this route, the URL will look like this:  `http://[user]:[pass]@[proxy url]:[proxy port]` replaced `[user]` with your user ID and `[pass]` with your current password. However, this is not recommended.
