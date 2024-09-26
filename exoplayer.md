您在使用 **ExoPlayer** 播放网络音乐时遇到了从 **HTTPS** 重定向到 **HTTP** 的问题。具体错误如下：

```
Caused by: androidx.media3.datasource.HttpDataSource$InvalidResponseCodeException: Response code: 302
```

### 问题分析

**HTTP 状态码 302** 表示 **临时重定向（Found）**，即请求的资源临时被移动到了另一个 URL。在您的情况下，资源从 **HTTPS** 重定向到了 **HTTP**。这种从 **安全的 HTTPS** 到 **不安全的 HTTP** 的重定向在现代 Android 应用中可能会导致问题，特别是在 **Android 9（API 级别 28）** 及更高版本中，默认情况下，应用禁止使用 **明文（HTTP）** 网络流量以确保数据传输的安全性。

### 解决方案

要处理 **从 HTTPS 重定向到 HTTP** 的情况，您需要在应用中允许 **明文流量**。不过，需要注意的是，这样做会降低数据传输的安全性，因此应谨慎使用，最好只针对特定的域名允许明文流量，而不是全局允许。

以下是详细的解决步骤：

#### 1. 配置网络安全设置以允许特定域名的明文流量

##### a. 创建网络安全配置文件

在您的项目的 `res/xml` 目录下创建一个名为 `network_security_config.xml` 的文件（如果 `xml` 目录不存在，请先创建它）。在该文件中，配置允许特定域名的明文流量。例如，假设您的音频资源位于 `www.example.com`：

```xml
<!-- res/xml/network_security_config.xml -->
<?xml version="1.0" encoding="utf-8"?>
<network-security-config>
    <domain-config cleartextTrafficPermitted="true">
        <domain includeSubdomains="true">www.example.com</domain>
    </domain-config>
</network-security-config>
```

**说明：**

- `<network-security-config>`：根元素，定义网络安全配置。
- `<domain-config>`：针对特定域的配置。
  - `cleartextTrafficPermitted="true"`：允许明文流量。
- `<domain>`：指定域名，`includeSubdomains="true"` 表示包含所有子域名。

##### b. 在 `AndroidManifest.xml` 中引用网络安全配置

在您的 `AndroidManifest.xml` 文件的 `<application>` 标签中，添加 `android:networkSecurityConfig` 属性，指向刚才创建的配置文件：

```xml
<!-- AndroidManifest.xml -->
<application
    android:networkSecurityConfig="@xml/network_security_config"
    ... >
    ...
</application>
```

##### c. 确保不全局允许明文流量

请避免将 `android:usesCleartextTraffic="true"` 设置在 `<application>` 标签中，因为这会全局允许所有域的明文流量，降低应用的整体安全性。

```xml
<!-- 不推荐的做法 -->
<application
    android:usesCleartextTraffic="true"
    ... >
    ...
</application>
```

#### 2. 配置 ExoPlayer 使用正确的数据源工厂

确保您在 ExoPlayer 中使用的 `HttpDataSource.Factory` 已正确配置以允许重定向，并设置适当的 `User-Agent`。以下是一个示例：

```kotlin
import android.content.Context
import androidx.media3.datasource.DefaultHttpDataSource
import androidx.media3.datasource.DefaultDataSource
import androidx.media3.datasource.DataSource
import androidx.media3.exoplayer.ExoPlayer
import androidx.media3.common.MediaItem

fun initializeExoPlayer(context: Context): ExoPlayer {
    // 配置 HttpDataSource.Factory
    val userAgent = "YourAppName/1.0" // 替换为您的应用名和版本
    val httpDataSourceFactory = DefaultHttpDataSource.Factory()
        .setUserAgent(userAgent)
        .setFollowRedirects(true) // 确保允许重定向
        .setAllowCrossProtocolRedirects(true) // 允许跨协议重定向（https -> http）
    
    // 创建 DefaultDataSource.Factory
    val dataSourceFactory: DataSource.Factory = DefaultDataSource.Factory(context, httpDataSourceFactory)
    
    // 初始化 ExoPlayer
    val exoPlayer = ExoPlayer.Builder(context)
        .setMediaSourceFactory(DefaultMediaSourceFactory(dataSourceFactory))
        .build()
    
    // 设置 MediaItem
    val mediaItem = MediaItem.fromUri("https://www.example.com/path/to/your/audio.mp3")
    exoPlayer.setMediaItem(mediaItem)
    exoPlayer.prepare()
    
    return exoPlayer
}
```

**关键点：**

- `setAllowCrossProtocolRedirects(true)`：允许跨协议（从 HTTPS 重定向到 HTTP 或反之）的重定向。
- `setFollowRedirects(true)`：确保 ExoPlayer 能够自动跟随重定向。

#### 3. 完整示例

以下是一个整合了上述配置的完整示例，展示如何在 Android 应用中使用 ExoPlayer 播放网络音乐，并处理从 HTTPS 到 HTTP 的重定向。

##### a. 配置网络安全设置

如前所述，创建 `res/xml/network_security_config.xml` 并在 `AndroidManifest.xml` 中引用。

##### b. 创建 `AudioViewModel`

```kotlin
// AudioViewModel.kt
import android.app.Application
import androidx.lifecycle.AndroidViewModel
import androidx.media3.common.MediaItem
import androidx.media3.datasource.DefaultDataSource
import androidx.media3.datasource.DefaultHttpDataSource
import androidx.media3.exoplayer.ExoPlayer

class AudioViewModel(application: Application) : AndroidViewModel(application) {
    val exoPlayer: ExoPlayer

    init {
        // 配置 HttpDataSource.Factory
        val userAgent = "YourAppName/1.0"
        val httpDataSourceFactory = DefaultHttpDataSource.Factory()
            .setUserAgent(userAgent)
            .setFollowRedirects(true)
            .setAllowCrossProtocolRedirects(true) // 允许跨协议重定向

        // 创建 DefaultDataSource.Factory
        val dataSourceFactory: DefaultDataSource.Factory = DefaultDataSource.Factory(application, httpDataSourceFactory)

        // 初始化 ExoPlayer
        exoPlayer = ExoPlayer.Builder(application)
            .setMediaSourceFactory(DefaultMediaSourceFactory(dataSourceFactory))
            .build()

        // 设置 MediaItem
        val mediaItem = MediaItem.fromUri("https://www.example.com/path/to/your/audio.mp3")
        exoPlayer.setMediaItem(mediaItem)
        exoPlayer.prepare()
        exoPlayer.play()
    }

    override fun onCleared() {
        super.onCleared()
        exoPlayer.release()
    }
}
```

##### c. 创建播放控制 UI（使用 Jetpack Compose）

```kotlin
// AudioPlayerControls.kt
import androidx.compose.foundation.layout.*
import androidx.compose.material.*
import androidx.compose.runtime.*
import androidx.compose.ui.Alignment
import androidx.compose.ui.Modifier
import androidx.compose.ui.unit.dp
import androidx.media3.common.Player
import androidx.media3.exoplayer.ExoPlayer
import kotlinx.coroutines.delay
import kotlinx.coroutines.launch

@Composable
fun AudioPlayerControls(exoPlayer: ExoPlayer) {
    val coroutineScope = rememberCoroutineScope()
    var isPlaying by remember { mutableStateOf(exoPlayer.isPlaying) }
    var currentPosition by remember { mutableStateOf(0L) }
    var duration by remember { mutableStateOf(0L) }

    // 监听播放器状态变化
    LaunchedEffect(exoPlayer) {
        val listener = object : Player.Listener {
            override fun onIsPlayingChanged(isPlayingNow: Boolean) {
                isPlaying = isPlayingNow
            }

            override fun onPlaybackStateChanged(state: Int) {
                if (state == Player.STATE_READY) {
                    duration = exoPlayer.duration
                }
            }

            override fun onPositionDiscontinuity(
                oldPosition: Player.PositionInfo,
                newPosition: Player.PositionInfo
            ) {
                currentPosition = exoPlayer.currentPosition
            }
        }

        exoPlayer.addListener(listener)

        // 更新当前播放位置
        while (true) {
            currentPosition = exoPlayer.currentPosition
            delay(1000)
        }

        // 清理
        // exoPlayer.removeListener(listener) // LaunchedEffect 自动清理
    }

    Column(
        modifier = Modifier
            .fillMaxWidth()
            .padding(16.dp),
        horizontalAlignment = Alignment.CenterHorizontally
    ) {
        // 播放/暂停按钮
        IconButton(onClick = {
            if (exoPlayer.isPlaying) {
                exoPlayer.pause()
            } else {
                exoPlayer.play()
            }
        }) {
            Icon(
                imageVector = if (isPlaying) Icons.Default.Pause else Icons.Default.PlayArrow,
                contentDescription = if (isPlaying) "暂停" else "播放"
            )
        }

        Spacer(modifier = Modifier.height(16.dp))

        // 播放进度条
        Slider(
            value = currentPosition.toFloat(),
            onValueChange = { newValue ->
                exoPlayer.seekTo(newValue.toLong())
            },
            valueRange = 0f..duration.toFloat(),
            modifier = Modifier.fillMaxWidth()
        )

        // 显示当前时间和总时长
        Row(
            modifier = Modifier.fillMaxWidth(),
            horizontalArrangement = Arrangement.SpaceBetween
        ) {
            Text(text = formatTime(currentPosition))
            Text(text = formatTime(duration))
        }
    }
}

fun formatTime(ms: Long): String {
    val totalSeconds = ms / 1000
    val minutes = (totalSeconds / 60).toInt()
    val seconds = (totalSeconds % 60).toInt()
    return String.format("%02d:%02d", minutes, seconds)
}
```

##### d. 创建 `AudioPlayerWithLifecycle` Composable

```kotlin
// AudioPlayerWithLifecycle.kt
import androidx.compose.runtime.Composable
import androidx.compose.runtime.DisposableEffect
import androidx.compose.ui.platform.LocalLifecycleOwner
import androidx.lifecycle.Lifecycle
import androidx.lifecycle.LifecycleEventObserver
import androidx.media3.exoplayer.ExoPlayer

@Composable
fun AudioPlayerWithLifecycle(viewModel: AudioViewModel) {
    val exoPlayer = viewModel.exoPlayer
    val lifecycleOwner = LocalLifecycleOwner.current

    DisposableEffect(lifecycleOwner) {
        val observer = LifecycleEventObserver { _, event ->
            when (event) {
                Lifecycle.Event.ON_PAUSE -> exoPlayer.pause()
                Lifecycle.Event.ON_RESUME -> exoPlayer.play()
                else -> {}
            }
        }

        lifecycleOwner.lifecycle.addObserver(observer)
        onDispose {
            lifecycleOwner.lifecycle.removeObserver(observer)
        }
    }

    // 显示播放控制
    AudioPlayerControls(exoPlayer = exoPlayer)
}
```

##### e. 创建 `AudioPlayerScreen` Composable

```kotlin
// AudioPlayerScreen.kt
import androidx.compose.runtime.Composable

@Composable
fun AudioPlayerScreen(viewModel: AudioViewModel) {
    AudioPlayerWithLifecycle(viewModel = viewModel)
}
```

##### f. 配置 `MainActivity`

```kotlin
// MainActivity.kt
import android.os.Bundle
import androidx.activity.ComponentActivity
import androidx.activity.compose.setContent
import androidx.compose.material.MaterialTheme
import androidx.compose.material.Surface
import androidx.lifecycle.viewmodel.compose.viewModel

class MainActivity : ComponentActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContent {
            MaterialTheme {
                Surface {
                    val audioViewModel: AudioViewModel = viewModel()
                    AudioPlayerScreen(viewModel = audioViewModel)
                }
            }
        }
    }
}
```

#### 4. 进一步优化和调试

##### a. 确认重定向目标 URL

确保您的音频 URL 重定向到的 **HTTP** URL 是有效的，并且确实可通过 **HTTP** 访问。您可以使用浏览器或命令行工具（如 `curl`）来验证：

```bash
curl -I -L https://www.example.com/path/to/your/audio.mp3
```

##### b. 检查 SSL 证书

如果您的应用在处理 **HTTPS** 重定向到 **HTTP** 时仍然遇到问题，请确保目标 **HTTP** 服务器没有使用需要验证的证书或存在其他 SSL 问题。

##### c. 使用不同的数据源工厂

有时，使用不同的 **HTTP** 客户端（如 **OkHttp**）可以更好地处理复杂的重定向或自定义需求。以下是如何使用 **OkHttp** 作为数据源工厂的示例：

```kotlin
import androidx.media3.datasource.okhttp.OkHttpDataSource
import androidx.media3.datasource.DefaultDataSource
import androidx.media3.exoplayer.ExoPlayer
import okhttp3.OkHttpClient

fun initializeExoPlayerWithOkHttp(context: Context): ExoPlayer {
    // 创建 OkHttpClient
    val okHttpClient = OkHttpClient.Builder()
        .followRedirects(true)
        .build()

    // 创建 OkHttpDataSource.Factory
    val httpDataSourceFactory = OkHttpDataSource.Factory(okHttpClient)
        .setUserAgent("YourAppName/1.0")
        .setAllowCrossProtocolRedirects(true)

    // 创建 DefaultDataSource.Factory
    val dataSourceFactory: DefaultDataSource.Factory = DefaultDataSource.Factory(context, httpDataSourceFactory)

    // 初始化 ExoPlayer
    val exoPlayer = ExoPlayer.Builder(context)
        .setMediaSourceFactory(DefaultMediaSourceFactory(dataSourceFactory))
        .build()

    // 设置 MediaItem
    val mediaItem = MediaItem.fromUri("https://www.example.com/path/to/your/audio.mp3")
    exoPlayer.setMediaItem(mediaItem)
    exoPlayer.prepare()
    exoPlayer.play()

    return exoPlayer
}
```

##### d. 处理 Cookie（如果需要）

如果重定向涉及到会话管理，可能需要处理 **Cookie**。您可以在 `HttpDataSource.Factory` 中设置默认请求属性来包含 **Cookie**：

```kotlin
val httpDataSourceFactory = DefaultHttpDataSource.Factory()
    .setUserAgent("YourAppName/1.0")
    .setFollowRedirects(true)
    .setAllowCrossProtocolRedirects(true)
    .setDefaultRequestProperties(mapOf(
        "Cookie" to "SESSION=your_session_id"
    ))
```

##### e. 启用详细日志以调试

启用 ExoPlayer 的详细日志记录，以获取更多关于重定向和错误的上下文信息：

```kotlin
import com.google.android.exoplayer2.util.Log
import com.google.android.exoplayer2.util.LogUtil

// 设置日志级别为 DEBUG
Log.setLogLevel(Log.DEBUG)
```

确保在开发过程中启用日志记录，帮助您更好地理解问题所在。

### 注意事项

1. **安全性考虑**：允许从 **HTTPS** 重定向到 **HTTP** 会降低数据传输的安全性，容易受到中间人攻击（MITM）。请仅在绝对必要时使用，并尽量避免。

2. **仅针对特定域名**：如上所述，尽量只针对需要的域名允许明文流量，而不是全局允许，以减少安全风险。

3. **服务器端优化**：如果可能，建议在服务器端修复重定向问题，确保所有资源都通过 **HTTPS** 提供，避免从 **HTTPS** 重定向到 **HTTP**。

4. **遵循最佳实践**：尽量使用 **HTTPS** 以确保数据传输的安全性，并遵循 **Android** 的网络安全最佳实践。

### 参考资料

- [Android 网络安全配置](https://developer.android.google.cn/training/articles/security-config)
- [ExoPlayer 官方文档](https://exoplayer.dev/)
- [Handling HTTP redirects in ExoPlayer](https://exoplayer.dev/issues.html)
- [OkHttp 官方文档](https://square.github.io/okhttp/)

通过以上步骤，您应该能够解决 ExoPlayer 在处理从 **HTTPS** 重定向到 **HTTP** 时遇到的问题。如果在实施过程中仍然遇到困难，请提供更多的日志信息或代码片段，以便进一步协助您解决问题。
