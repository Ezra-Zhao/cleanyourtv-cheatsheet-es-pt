# CleanYourTV 速查表 | CleanYourTV Cheatsheet | Guía rápida de CleanYourTV

> 一页看懂 CleanYourTV：通过 Wi-Fi 给 Android TV / Google TV 瘦身——先备份，再按品牌对照禁用遥测、广告服务和预装臃肿包，换上一个干净的类 Apple TV 主页。不 root、不拆机、什么都不真卸载（只是禁用），一行恢复脚本随时全部还原。起因是作者给爸妈的电视去广告：开机先弹赞助广告、还没打开 Netflix 就先看广告，受不了，自己动手清了。

- 上游项目：[Gathin23/cleanyourtv](https://github.com/Gathin23/cleanyourtv)（2026-10-19 实测 4,013 ★，MIT 协议；官网 cleanyour.tv，仓库内含 30 份品牌/型号电视清单）
- 适用：Android TV 与 Google TV（小米、Sony、TCL、海信、Philips 等品牌，清单见上游 examples/）
- 前提：一台与电视同一 Wi-Fi 的电脑 + 一个有 shell 权限的编程助手（Claude Code 或类似工具）
- 本页结构：中文主文 → English → Español → Português
- 本速查表为原创撰写（clean-room）：只参照上游公开文档的事实、命令与安全边界，文字与编排均为重新创作。
- 内容边界：本页只讲合法的系统精简——禁用遥测/广告/预装臃肿应用、关闭主页推荐行、换启动器、还原。不涉及破解付费内容、绕过 DRM 或盗版，与上游的做法一致。

---

## 原理（为什么安全）

Android 电视和安卓手机共用同一套调试桥（ADB）。电视上打开 USB 调试、允许你的电脑连一次，剩下的全是标准 Android 工具：

1. **先备份**：导出已装应用包清单和当前设置，出事有据可查。
2. **只禁用不卸载**：用 `pm disable-user` 在用户配置里禁用厂商遥测、广告服务、内容推荐行和预装垃圾。包本体还在电视里，恢复脚本一条命令全部启用回来。
3. **换主页**：装一个小的原生启动器设为默认主页（纯黑底 + 常用应用坞 + 应用网格，观感接近 Apple TV）。

不同品牌的臃肿包不一样，所以上游的做法是让编程助手在你的电视上现场发现、逐项问过你才动手，不套用别人的清单硬删。

## 能禁 / 别碰 对照（动手前先看这张）

| 类别 | 处置 | 说明 |
|---|---|---|
| 厂商遥测、广告服务、赞助内容行 | 可禁 | 这是本项目的主目标：开机广告、主页赞助位、数据回传 |
| 预装臃肿应用（用不上的厂商自带 App、演示/推广包） | 可禁 | 先确认家里没人用它；禁错了恢复脚本启用回来即可 |
| 系统更新服务 | 按需 | 多数清单会顺手禁用，代价是系统更新不再自动来；想更新时先启用回来 |
| Google TV 安装向导（com.google.android.tungsten.setupwraith） | 要禁记得登记 | Google TV 上禁用原启动器后它可能抢主页并让系统崩溃循环；禁它可以，但必须写进你的 debloat 清单，恢复时要带回来 |
| Play 商店 | 别碰 | 流媒体 App 靠它更新，禁了应用生态就断了 |
| Netflix / YouTube 等你在用的流媒体应用 | 别碰 | 本项目只换主页、不动应用本身 |
| Chromecast built-in | 别碰（除非确定不用） | 安卓手机投屏靠它；iPhone 的 AirPlay 不在本项目范围 |
| 系统核心包、原启动器（新启动器装好并跑起来之前） | 绝对别碰 | 顺序错了主页会落到工厂测试应用上；安装脚本会替你排好顺序，自己手动操作时尤其注意 |

## 操作步骤速查

| 步骤 | 做什么 |
|---|---|
| 1. 电视开调试 | 设置 → 设备偏好 → 关于 → 在「版本号/Build」上连按 OK 键 7 次 → 返回 → 开发者选项 → 打开 USB 调试（有「网络/ADB 调试」开关也打开），电视保持开机 |
| 2. 电脑交底 | 把上游仓库的 PROMPT.md 贴给你的编程助手，填上电视型号；电视和电脑连同一个 Wi-Fi |
| 3. 授权连接 | 电视上弹出「允许 USB 调试？」时点允许 |
| 4. 回答清单 | 助手会逐项问：哪些 App 在用、主页坞放哪 4 个、要不要保留语音助手、Chromecast、系统更新——照实答，它据此生成你的专属禁用清单 |
| 5. 分批禁用 | 助手先备份包清单，再小批量禁用并逐项截图核对（清单分批应用就是为了出问题好定位） |
| 6. 装新主页 | 构建并安装干净启动器、设为默认主页；需要时用 ADB 设时钟屏保防烧屏 |
| 7. 收尾 | 关掉电视的 USB 调试（启动器不受影响继续工作），把你的 debloat 清单文件存好——恢复全靠它 |

全程约 15 分钟（上游口径，仓库作为上下文给助手时）。

## 恢复（一行还原）

留好你实际应用的那份清单文件，恢复脚本会把清单里的包逐个启用回来、把原启动器设回主页、重置动画缩放：

```bash
# Android TV
TV_HOST=192.168.1.50 tools/restore.sh examples/<你的电视>/debloat.txt

# Google TV（多带原启动器包名）
TV_HOST=192.168.1.50 tools/restore.sh examples/<你的电视>/debloat.txt com.google.android.apps.tv.launcherx
```

注意：恢复后新启动器仍装在电视上（想彻底去掉去 设置 → 应用 里删）；清单之外的包（你自己在别处禁的）恢复脚本管不着。电视连不上时，电视自己的 设置 → 应用 页面也能手动逐个启用回来。

## 常见坑（上游踩过的三个大坑 + 高频问题）

- **macOS 连不上**：macOS 的本地网络隐私会挡 `adb connect`（报 No route to host），上游的 tools/tv.py 绕开了这个二进制调用，自己中招时先换它的工具试。
- **原启动器禁用顺序**：必须先装好并跑起新启动器，再禁原启动器，顺序反了主页会掉进工厂测试应用。
- **Google TV 向导崩溃循环**：见上文对照表里的 setupwraith——禁原启动器时记得连它一起登记禁用；已经卡住的，用 `tools/tv.py 'pm enable --user 0 com.google.android.apps.tv.launcherx'` 先把原启动器启用回来脱困。
- **会不会变砖？** 不刷机、不 root、不删除任何东西，风险只来自禁错系统依赖的包；分批应用 + 恢复脚本 + 电视设置页手动启用，三重兜底。
- **系统更新怎么办？** 更新服务被禁后不会自动更新；哪天想更新，先启用回更新服务。万一某次更新把包带回来了，重跑一遍脚本即可。
- **应用会坏吗？** 流媒体应用照常经 Play 商店更新，启动器只是列出已装应用并启动它们，机制十年没变。
- **ADB 是不是安全洞？** 就是电视上你亲手点的那一次「允许这台电脑」，和开发安卓 App 的授权一样；完事后关掉 USB 调试即可。
- **有现成 APK 吗？** 没有预编译包：用它的一行脚本自己构建（一分钟），装什么到自己电视上自己说了算。
- **遥控器按键失灵**：个别机型装完后遥控器的输入源键/Home 键行为变了，上游提供两个可选的无障碍服务开关（安装时加 INPUT_KEY_SERVICE=1 或 HOME_KEY_SERVICE=1）对应修复。

## 进阶提示

- **给爸妈家办**是最典型的用法：你远程拿到电视型号、生成清单、一次上门搞定；恢复清单留一份在自己手里，日后电视「坏了」先想是不是包被系统更新带回来了。
- **贡献清单回上游**：你的型号不在 30 份清单里的话，把你的 debloat 清单按上游 CONTRIBUTING 的格式提回去，下一个同型号的人就不用重新摸索——这是上游最欢迎的贡献。

---

## English (summary)

CleanYourTV (by Gathin23, MIT, ~4k stars two days after launch, measured 2026-10-19) strips the bloat from an Android TV or Google TV over Wi-Fi and replaces the ad-filled home screen with a clean, Apple TV-style launcher. No root, no cable, nothing uninstalled — packages are only disabled in the user profile with `pm disable-user` — and a one-line restore script undoes every step.

How it works: Android TVs expose the same ADB debug bridge as Android phones. You switch on USB debugging (tap Build seven times under Settings → Device Preferences → About), then hand the repo's PROMPT.md plus your TV model to a coding assistant with shell access. The assistant discovers your TV on the network, backs up the installed-package list and settings, asks which apps you actually use (and which four go in the dock, whether to keep the voice assistant, Chromecast and updates), then disables the manufacturer's telemetry, ad services, sponsored content rows and preloaded junk in small batches, verifying with screenshots, and installs the clean launcher as the default home. About fifteen minutes with the repo as context.

Safety table, in short: telemetry, ad services and preloaded bloat are the targets; the Play Store (streaming apps update through it), your streaming apps and Chromecast built-in stay untouched; the stock launcher is disabled only after the new one is installed and running, or Home falls back to a factory test app. On Google TV, also disable the setup wizard (com.google.android.tungsten.setupwraith) and record it in your list, or it can grab Home and crash-loop the system. Most lists disable the update service too — re-enable it when you want firmware updates.

Restore: keep the debloat list you applied; `tools/restore.sh` re-enables exactly those packages, puts the stock launcher back and resets animation scales (Google TV needs the stock launcher package name as an extra argument). The TV's own Settings → Apps page can also re-enable packages if the TV is unreachable. Known traps: macOS Local Network privacy can block `adb connect` (the repo's tools/tv.py works around it); turn USB debugging off when done — the launcher keeps working.

This cheatsheet covers only lawful debloating — disabling telemetry, ads and preinstalled bloat, and restoring them. Nothing here bypasses DRM or paid content.

---

## Español (guía rápida — traducción de estudio, pendiente de revisión por un hablante nativo)

CleanYourTV (de Gathin23, licencia MIT, ~4 mil estrellas a los dos días de publicarse, medido el 2026-10-19) limpia tu Android TV o Google TV por Wi-Fi y sustituye la pantalla de inicio llena de anuncios por un launcher limpio estilo Apple TV. Sin root, sin cables y sin desinstalar nada: los paquetes solo se deshabilitan en el perfil de usuario con `pm disable-user`, y un script de restauración de una línea lo deshace todo.

Pasos:

1. **Activa la depuración** en el televisor: Ajustes → Preferencias del dispositivo → Acerca de → pulsa OK siete veces sobre «Compilación» → vuelve → Opciones de desarrollador → activa «Depuración USB» (y la de red/ADB si aparece). Deja el televisor encendido.
2. **Prepara el ordenador** (misma Wi-Fi que el televisor): pega el archivo PROMPT.md del repositorio en tu asistente de programación y escribe el modelo de tu TV.
3. **Autoriza y responde**: acepta «¿Permitir depuración USB?» en el televisor y responde la lista de preguntas: qué apps usas, cuáles cuatro van en el dock, si mantienes el asistente de voz, Chromecast y las actualizaciones.
4. **Limpieza por lotes**: el asistente primero hace copia de la lista de paquetes, luego deshabilita la telemetría del fabricante, los servicios de anuncios, las filas patrocinadas y las apps preinstaladas que sobran, en lotes pequeños y verificando con capturas, e instala el launcher limpio como inicio predeterminado.
5. **Cierre**: apaga la depuración USB al terminar (el launcher sigue funcionando) y guarda bien tu archivo de lista: la restauración depende de él.

Qué se puede deshabilitar y qué no tocar:

- **Sí**: telemetría, servicios publicitarios, filas de contenido patrocinado y apps preinstaladas inútiles (confirmando antes que nadie en casa las usa).
- **Con cuidado**: el servicio de actualizaciones (si se deshabilita, las actualizaciones solo llegan al reactivarlo) y, en Google TV, el asistente de configuración (com.google.android.tungsten.setupwraith): hay que deshabilitarlo junto con el launcher original y anotarlo en la lista, o puede quedarse con el botón de inicio y entrar en un ciclo de fallos.
- **No tocar**: Play Store (por ahí se actualizan tus apps de streaming), las apps que sí usas, Chromecast integrado y el launcher original hasta que el nuevo esté instalado y funcionando; si se deshabilita antes, el inicio puede caer en una app de pruebas de fábrica.

Restauración: el script `tools/restore.sh` con tu lista vuelve a habilitar exactamente esos paquetes, devuelve el launcher original y restablece las escalas de animación (en Google TV se pasa además el nombre del paquete del launcher original). Si el televisor no responde por red, su propia página Ajustes → Aplicaciones también permite reactivar paquetes a mano.

Aviso: esta guía solo cubre la limpieza legal del sistema (deshabilitar telemetría, anuncios y software preinstalado, y restaurarlo). Nada de lo aquí descrito sirve para saltarse DRM ni contenidos de pago.

---

## Português (guia rápido — tradução de estudo, aguardando revisão de um falante nativo)

O CleanYourTV (de Gathin23, licença MIT, ~4 mil estrelas dois dias após o lançamento, medido em 2026-10-19) limpa a sua Android TV ou Google TV pelo Wi-Fi e troca a tela inicial cheia de anúncios por um launcher limpo no estilo Apple TV. Sem root, sem cabo e sem desinstalar nada: os pacotes são apenas desabilitados no perfil do usuário com `pm disable-user`, e um script de restauração de uma linha desfaz tudo.

Passos:

1. **Ative a depuração** na TV: Configurações → Preferências do dispositivo → Sobre → toque OK sete vezes em «Número da versão» → volte → Opções do desenvolvedor → ative «Depuração USB» (e a de rede/ADB, se aparecer). Deixe a TV ligada.
2. **Prepare o computador** (na mesma Wi-Fi da TV): cole o arquivo PROMPT.md do repositório no seu assistente de programação e informe o modelo da sua TV.
3. **Autorize e responda**: aceite «Permitir depuração USB?» na TV e responda à lista de perguntas: quais apps você usa, quais quatro vão para o dock, se mantém o assistente de voz, o Chromecast e as atualizações.
4. **Limpeza em lotes**: o assistente primeiro salva a lista de pacotes, depois desabilita a telemetria do fabricante, os serviços de anúncios, as fileiras patrocinadas e os apps pré-instalados inúteis, em lotes pequenos e conferindo com capturas, e instala o launcher limpo como tela inicial padrão.
5. **Fechamento**: desligue a depuração USB ao final (o launcher continua funcionando) e guarde bem o seu arquivo de lista: a restauração depende dele.

O que pode desabilitar e o que não mexer:

- **Pode**: telemetria, serviços de publicidade, fileiras de conteúdo patrocinado e apps pré-instalados inúteis (confirmando antes que ninguém em casa os usa).
- **Com cuidado**: o serviço de atualizações (se desabilitado, as atualizações só voltam ao reativá-lo) e, na Google TV, o assistente de configuração (com.google.android.tungsten.setupwraith): é preciso desabilitá-lo junto com o launcher original e anotá-lo na lista, ou ele pode tomar o botão de início e entrar em ciclo de falhas.
- **Não mexer**: Play Store (é por ela que seus apps de streaming atualizam), os apps que você realmente usa, o Chromecast integrado e o launcher original até o novo estar instalado e rodando; se desabilitar antes, a tela inicial pode cair num app de testes de fábrica.

Restauração: o script `tools/restore.sh` com a sua lista reabilita exatamente aqueles pacotes, devolve o launcher original e redefine as escalas de animação (na Google TV passa-se também o nome do pacote do launcher original). Se a TV não responder pela rede, a própria página Configurações → Apps também permite reativar pacotes manualmente.

Aviso: este guia cobre apenas a limpeza legal do sistema (desabilitar telemetria, anúncios e software pré-instalado, e restaurá-los). Nada aqui serve para burlar DRM ou conteúdo pago.
