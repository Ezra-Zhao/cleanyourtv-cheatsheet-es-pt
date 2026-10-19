# CleanYourTV 典型用法示例 | Example Workflows

个人学习整理，配合主 README 使用。命令以官方 README 与仓库脚本为准。

## 1. 给爸妈家的电视去广告（本项目的原点场景）

1. 先拿到电视型号（电视背面贴纸或 设置 → 关于），确认是 Android TV / Google TV。
2. 按主 README 步骤在电视上打开 USB 调试，把 PROMPT.md 连同型号交给编程助手。
3. 清单问答环节替爸妈做主：流媒体应用全保留、语音助手看他们用不用、系统更新先禁着（你下次上门再手动更新）。
4. 分批禁用 + 装新主页后，现场让爸妈按一遍遥控器：开机直达常用应用、没有赞助行，才算完工。
5. 把这台电视的 debloat 清单自己留一份存档——日后恢复和更新都要用它。

English: Get the model, enable USB debugging, answer the checklist for them, debloat in batches, install the clean launcher, and keep their debloat list on file.

## 2. 中途后悔 / 家人要用被禁的应用

1. 不要重装系统。用你存的那份清单跑恢复：

```bash
TV_HOST=<电视IP> tools/restore.sh examples/<你的电视>/debloat.txt
# Google TV 再多加原启动器包名 com.google.android.apps.tv.launcherx
```

2. 只想启用回单个应用的，去电视 设置 → 应用 找到它手动启用即可，不必整表恢复。
3. 想再次精简：重跑安装脚本，它能自己判断该禁哪个启动器。

English: Restore exactly the packages on your list with one command, or re-enable a single app from the TV's own Settings → Apps page.

## 3. Google TV 禁完启动器后开机循环（脱困）

症状：禁掉原启动器后系统反复崩溃、进不了主页。多半是安装向导 setupwraith 抢了主页。

```bash
tools/tv.py 'pm enable --user 0 com.google.android.apps.tv.launcherx'
```

先把原启动器启用回来让电视能进系统，再检查：setupwraith 是否已在你的 debloat 清单里登记禁用；补上后重走安装顺序（新启动器先装好跑起来，再禁原启动器和向导）。

English: Re-enable the stock launcher to get back in, then make sure the setup wizard is on your disable list before redoing the swap in the right order.
