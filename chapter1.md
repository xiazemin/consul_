# chapter1

$   brew cask install consul

Error: No available Cask for consul. Did you mean one of:

consul-cli                           consul-web-ui

Error: nothing to install

$  brew install caskroom/cask/brew-cask

Updating Homebrew...

==&gt; Auto-updated Homebrew!

==&gt; brew cask install caskroom/cask/brew-cask

Error: '/usr/local/Homebrew/Library/Taps/caskroom/homebrew-cask/Casks/brew-cask.rb' does not exist.

Error: nothing to install

brew和brew／cask 区别：brew 源码下载编译安装，cask 安装包安装

$  brew install consul

Updating Homebrew...

\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\# 100.0%

==&gt; Pouring consul-0.8.1.el\_capitan.bottle.tar.gz

==&gt; Caveats

If consul was built with --with-web-ui, you can activate the UI by running

consul with \`-ui-dir /usr/local/opt/consul/share/consul/web-ui\`.

zsh completions have been installed to:

/usr/local/share/zsh/site-functions

To have launchd start consul now and restart at login:

brew services start consul

Or, if you don't want/need a background service you can just run:

consul agent -dev -advertise 127.0.0.1

==&gt; Summary

🍺  /usr/local/Cellar/consul/0.8.1: 5 files, 34.3MB

$    consul -v

Consul v0.8.1

Protocol 2 spoken by default, understands 2 to 3 \(agent will automatically use protocol &gt;2 when speaking to compatible agents\)

安装成功

