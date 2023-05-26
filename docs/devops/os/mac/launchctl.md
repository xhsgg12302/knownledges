## launchctl

!> 可以使用的工具有 LaunchControl

```xml
<!-- ~/Library/LaunchAgents/dos.wtfu.site.plist -->
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
    <dict>
        <key>Label</key>
        <string>docs.wtfu.site</string>
        <key>ProgramArguments</key>
        <array>
            <string>sh</string>
            <string>-c</string>
            <string>cd ~/Desktop/knownledges/ &amp;&amp; docsify serve .</string>
        </array>
        <key>RunAtLoad</key>
        <true/>
        <key>EnvironmentVariables</key>
        <dict>
            <key>PATH</key>
            <string>/usr/local/bin</string>
            <!-- <string>/usr/local/bin:/usr/local/opt/node@18/bin</string> -->
        </dict>
        <key>StandardOutPath</key>
        <string>/tmp/docs.wtfu.site.log</string>
        <key>StandardErrorPath</key>
        <string>/tmp/docs.wtfu.site.err</string>
    </dict>
</plist>
```

```shell
alias reload='source ~/.zshrc \!:1'

# Path to your oh-my-zsh installation.
export ZSH="/Users/stevenobelia/.oh-my-zsh"
export RANGER_LOAD_DEFAULT_RC=FALSE

if [ $# -eq 2 ]; then
	ZSH_THEME=$2
else
	ZSH_THEME="random"
fi
plugins=(git)

source $ZSH/oh-my-zsh.sh
source ~/.bash_profile
source /usr/local/share/zsh-syntax-highlighting/zsh-syntax-highlighting.zsh
export PATH="/usr/local/opt/ruby/bin:$PATH"
#export PATH="/usr/local/lib/ruby/gems/3.1.0/bin:$PATH"
___MY_VMOPTIONS_SHELL_FILE="${HOME}/.jetbrains.vmoptions.sh"; if [ -f "${___MY_VMOPTIONS_SHELL_FILE}" ]; then . "${___MY_VMOPTIONS_SHELL_FILE}"; fi
```

## Reference
* [Mac OS 增加开机自启动脚本](https://www.xiaocaicai.com/2021/11/mac-os-%E5%A2%9E%E5%8A%A0%E5%BC%80%E6%9C%BA%E8%87%AA%E5%90%AF%E5%8A%A8%E8%84%9A%E6%9C%AC/)
* https://stackoverflow.com/questions/63169877/running-a-shell-script-automatically-with-launchd-on-mac
* https://stackoverflow.com/questions/15536697/running-python-script-with-launchd-imports-not-found