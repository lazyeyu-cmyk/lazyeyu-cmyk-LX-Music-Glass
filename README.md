# LX Music Glass

**阶段版本：0.4.0-quickjs-runtime**

独立 Android 改造工程：以 Kotlin + Jetpack Compose 为 UI 基础，目标采用 Miuix Glass 视觉体系；逐步复用 LX Music Mobile 1.9.2 的音源协议与音乐业务。此项目不是上游官方发行版。

## 当前工程包含

- Compose 页面骨架：发现、音乐库、搜索、设置。
- Android Storage Access Framework 本地音频导入与 Media3/ExoPlayer 基础播放。
- 自定义音源脚本导入、元信息解析、持久化、删除。
- QuickJS 独立执行上下文，用户主动点击“启用”后运行脚本。
- LX 风格 `lx.on` / `lx.send` / `lx.request` 入口与原生 OkHttp 请求代理。
- `musicUrl`、`lyric`、`pic` 三类脚本动作的异步 request/response 通道。
- 定时器、取消请求和基础 MD5/Base64/AES/RSA 原生辅助。
- 不向音源脚本开放 Android Context、文件系统、Shell 或 Root 接口。

## 尚未完成的完整客户端功能

- 原版 `musicSdk` 的在线搜索、歌单、排行榜、热门搜索和音质查询尚未整体迁移。
- 自定义音源的 `formData`、binary 响应及所有加密边界还未通过真实脚本回归测试。
- 在线搜索结果到播放队列、音源 URL 解析、歌词与封面还未串成完整播放链路。
- MediaSessionService、后台播放通知、锁屏/耳机控制、下载和收藏迁移尚未完成。
- 当前玻璃卡片仍有 Compose 半透明 fallback；尚未完成 Miuix Glass 采样宿主与组件全量替换。
- 未在 Android 17 真机验证；当前容器没有 Android SDK，无法完成 Gradle 编译或 APK 签名。

## 构建环境

- JDK 17
- Android SDK / Build Tools（compileSdk 36）
- Android Gradle Plugin 8.9.2
- Kotlin 2.1.10

在 Android Studio 中打开项目根目录 `LX-Music-Glass/`，同步 Gradle 后运行 `app`。本包未包含 Gradle Wrapper。

## 上游与依赖

- LX Music Mobile: https://github.com/lyswhut/lx-music-mobile （Apache-2.0；自定义音源协议参考其 1.9.2 实现）
- Mishka Glass migration guide: https://github.com/YuKongA/Mishka/blob/main/docs/miuix-glass-migration.md
- QuickJS wrapper: https://github.com/HarlonWang/quickjs-wrapper （Apache-2.0）
- OkHttp / AndroidX Media3 / Compose：各自遵循上游许可证，见 `THIRD_PARTY_NOTICES.md`。

## Local smoke test

The preload protocol can be smoke-tested with Node.js (no Android SDK required):

```bash
node tools/test-user-api-preload.cjs
```

This verifies the JavaScript-side init handshake, `musicUrl` response envelope and `lx.request` callback path. It does **not** test the Android QuickJS native library, OkHttp integration or an actual provider script.


## GitHub Actions APK build

The repository includes `.github/workflows/android.yml`. After the project files are uploaded to the repository's `main` branch, GitHub Actions installs JDK 17, Gradle 8.11.1 and Android SDK 36, runs the JS bridge smoke test, builds `:app:assembleDebug`, and uploads the debug APK as the `LX-Music-Glass-0.4.0-debug` workflow artifact.

This is a prototype build, not yet a full replacement for LX Music Mobile. See the limitations above.
