# 感谢每一位为 Linux 生态做出贡献的 geeker

## Ubuntu22.04 游戏初体验

- 安装：官方下载的 deb 安装包在更新时提示 i386 的库文件有问题，后面确认 steam 的运行依赖 32bit 的 OpenGL
- 更改 apt 安装的 architecture，添加 i386
- 直接用 apt 安装 steam
- 其次是每次进入 steam 会出现`系统策略禁止了用户修改网络配置`，这貌似是 22 才加进去的安全策略，因为之前使用
  ubuntu20 的时候查看 WiFi 状态不会出现这个弹窗，换了 22 之后有了。更可气的是这个对话框要疯狂点击才能关闭，关了之后
  又会打开，可以参考以下链接的讨论

```
  1. https://steamcommunity.com/app/221410/discussions/0/3106901665845459558/
  2. https://github.com/ValveSoftware/steam-for-linux/issues/7856
  3. https://askubuntu.com/questions/708261/how-do-i-set-the-policy-for-users-to-modify-the-network-state-and-connections
```

- 安全策略可以阅读`/usr/share/polkit-1/actions/org.freedesktop.NetworkManager.policy`，具体操作是在`/var/lib/polkit-1/localauthority/50-local.d`下创建`10-network-manager.pkla`，内容是

```bash
[Let foo modify system settings for network]
Identity=unix-user:User
Action=org.freedesktop.NetworkManager.settings.modify.system
ResultAny=no
ResultInactive=no
ResultActive=no
```

## Ubuntu 游戏推荐

- CSGO、DOTA 和文明居然支持 steam，不过分的硬盘空间不够，要外接硬盘
- 备受推荐的 Terraria
