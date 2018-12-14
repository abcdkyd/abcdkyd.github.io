### mac下php版本切换
#### brew安装php-version

```
# brew install php56
# brew install php70
# brew install php-version
# source $(brew --prefix php-version)/php-version.sh

# php-version
# php-version 5.6.5
```

#### php开机启动

```
# mkdir -p ~/Library/LaunchAgents
# cp /usr/local/opt/php56/homebrew.mxcl.php56.plist ~/Library/LaunchAgents/
# launchctl load -w ~/Library/LaunchAgents/homebrew.mxcl.php56.plist
# launchctl unload -w ~/Library/LaunchAgents/homebrew.mxcl.php56.plist
```

#### 切换本机php版本

```
// 先确认phpinfo里面的php版本，先卸载5.6的plist，在加载7.1的plist
# launchctl unload -w ~/Library/LaunchAgents/homebrew.mxcl.php56.plist
# launchctl unload -w ~/Library/LaunchAgents/homebrew.mxcl.php71.plist
```