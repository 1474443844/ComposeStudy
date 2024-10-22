好的，我将为您使用 Jetpack Compose 编写一个简单的歌词展示界面。这个界面将显示歌曲的歌词，并支持滚动查看。

## 实现步骤：

1. **创建一个数据类来表示每行歌词。**

```kotlin
data class LyricLine(val time: Int, val text: String)
```

2. **编写 LyricsScreen 组件来显示歌词列表。**

```kotlin
@Composable
fun LyricsScreen(lyrics: List<LyricLine>) {
    LazyColumn(
        modifier = Modifier
            .fillMaxSize()
            .background(Color.Black),
        contentPadding = PaddingValues(16.dp)
    ) {
        items(lyrics) { line ->
            Text(
                text = line.text,
                color = Color.White,
                fontSize: 18.sp,
                modifier = Modifier.padding(vertical = 4.dp)
            )
        }
    }
}
```

3. **在主活动中调用 LyricsScreen，并传入示例歌词。**

```kotlin
class MainActivity : ComponentActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContent {
            val sampleLyrics = listOf(
                LyricLine(0, "第一行歌词"),
                LyricLine(10, "第二行歌词"),
                LyricLine(20, "第三行歌词")
                // 可以添加更多歌词
            )
            LyricsScreen(lyrics = sampleLyrics)
        }
    }
}
```

## 完整代码示例：

```kotlin
// 导入所需的包
import android.os.Bundle
import androidx.activity.ComponentActivity
import androidx.activity.compose.setContent
import androidx.compose.foundation.background
import androidx.compose.foundation.layout.fillMaxSize
import androidx.compose.foundation.layout.padding
import androidx.compose.foundation.lazy.LazyColumn
import androidx.compose.foundation.lazy.items
import androidx.compose.material3.Text
import androidx.compose.runtime.Composable
import androidx.compose.ui.Modifier
import androidx.compose.ui.graphics.Color
import androidx.compose.ui.unit.dp
import androidx.compose.ui.unit.sp

// 数据类表示每行歌词
data class LyricLine(val time: Int, val text: String)

// 主活动
class MainActivity : ComponentActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContent {
            val sampleLyrics = listOf(
                LyricLine(0, "第一行歌词"),
                LyricLine(10, "第二行歌词"),
                LyricLine(20, "第三行歌词")
                // 可以添加更多歌词
            )
            LyricsScreen(lyrics = sampleLyrics)
        }
    }
}

// 显示歌词的组合函数
@Composable
fun LyricsScreen(lyrics: List<LyricLine>) {
    LazyColumn(
        modifier = Modifier
            .fillMaxSize()
            .background(Color.Black),
        contentPadding = PaddingValues(16.dp)
    ) {
        items(lyrics) { line ->
            Text(
                text = line.text,
                color = Color.White,
                fontSize = 18.sp,
                modifier = Modifier.padding(vertical = 4.dp)
            )
        }
    }
}
```

## 解释：

- **LyricLine 数据类**：包含 `time`（时间戳）和 `text`（歌词文本），`time` 可用于同步显示歌词。
- **LyricsScreen 组件**：使用 `LazyColumn` 来高效地显示大量歌词，每行歌词使用 `Text` 组件展示。
- **样式**：背景设为黑色，文字为白色，字体大小为 18sp，符合常见的歌词显示风格。

## 可扩展性：

- **同步歌词高亮**：可以根据当前播放的时间，使用 `time` 属性来高亮显示当前歌词。
- **自动滚动**：当歌词更新时，可以实现自动滚动到当前歌词的位置。
- **用户交互**：添加手势识别，允许用户拖动歌词以查看不同部分。

希望这个示例对您有所帮助！

---

好的，我将详细介绍如何实现之前提到的拓展功能，包括：

1. **同步歌词高亮**：根据当前播放的时间，高亮显示当前的歌词。

2. **自动滚动**：当歌词更新时，自动滚动到当前歌词的位置。

3. **用户交互**：添加手势识别，允许用户拖动歌词以查看不同部分。

## 1. 同步歌词高亮

要实现同步歌词高亮，需要：

- **获取当前播放时间**：从播放器获取当前的播放时间。

- **更新当前歌词索引**：根据当前时间，找到对应的歌词行。

- **高亮当前歌词**：在界面上高亮显示当前的歌词行。

### 实现步骤：

1. **获取播放时间**：

   - 使用 `MediaPlayer` 或其他音频播放器，获取当前的播放时间 `currentTime`。

2. **更新歌词索引**：

   - 在 Compose 中，使用 `LaunchedEffect` 或定时器，每隔一定时间（如 500 毫秒）更新一次 `currentTime`。

   - 根据 `currentTime`，找到需要高亮的歌词行索引 `currentIndex`。

3. **高亮显示歌词**：

   - 在 `LazyColumn` 中，使用 `itemsIndexed` 遍历歌词列表。

   - 对于当前索引的歌词行，设置不同的样式，如改变文字颜色或加粗。

### 代码示例：

```kotlin
@Composable
fun LyricsScreen(lyrics: List<LyricLine>, currentTime: Int) {
    // 找到当前需要高亮的歌词索引
    val currentIndex = lyrics.indexOfLast { it.time <= currentTime }

    LazyColumn(
        modifier = Modifier
            .fillMaxSize()
            .background(Color.Black)
    ) {
        itemsIndexed(lyrics) { index, line ->
            Text(
                text = line.text,
                color = if (index == currentIndex) Color.Yellow else Color.White,
                fontSize = if (index == currentIndex) 20.sp else 18.sp,
                modifier = Modifier.padding(vertical = 4.dp)
            )
        }
    }
}
```

### 注意事项：

- `currentTime` 应该是一个 `State`，随着播放器的播放而更新。

- `indexOfLast` 用于找到最后一个时间小于等于 `currentTime` 的歌词行。

## 2. 自动滚动

为了在歌词更新时自动滚动到当前歌词，需要：

- **使用 `LazyListState`**：用于控制 `LazyColumn` 的滚动状态。

- **监听歌词索引的变化**：当 `currentIndex` 变化时，自动滚动到对应的位置。

### 实现步骤：

1. **创建 `LazyListState`**：

   ```kotlin
   val listState = rememberLazyListState()
   ```

2. **监听 `currentIndex` 变化并滚动**：

   ```kotlin
   LaunchedEffect(currentIndex) {
       listState.animateScrollToItem(index = currentIndex)
   }
   ```

3. **将 `listState` 传递给 `LazyColumn`**：

   ```kotlin
   LazyColumn(
       state = listState,
       modifier = Modifier
           .fillMaxSize()
           .background(Color.Black)
   ) { /*...*/ }
   ```

### 完整代码示例：

```kotlin
@Composable
fun LyricsScreen(lyrics: List<LyricLine>, currentTime: Int) {
    val currentIndex = lyrics.indexOfLast { it.time <= currentTime }
    val listState = rememberLazyListState()

    // 自动滚动到当前歌词
    LaunchedEffect(currentIndex) {
        listState.animateScrollToItem(index = currentIndex)
    }

    LazyColumn(
        state = listState,
        modifier = Modifier
            .fillMaxSize()
            .background(Color.Black)
    ) {
        itemsIndexed(lyrics) { index, line ->
            Text(
                text = line.text,
                color = if (index == currentIndex) Color.Yellow else Color.White,
                fontSize = if (index == currentIndex) 20.sp else 18.sp,
                modifier = Modifier.padding(vertical = 4.dp)
            )
        }
    }
}
```

### 注意事项：

- `LaunchedEffect` 会在 `currentIndex` 变化时触发滚动。

- 使用 `animateScrollToItem` 可以实现平滑滚动。

## 3. 用户交互

为了允许用户拖动歌词，需要：

- **检测用户滚动手势**：当用户手动滚动时，暂停自动滚动。

- **恢复自动滚动**：在用户停止滚动后，过一段时间恢复自动滚动。

### 实现步骤：

1. **创建一个变量跟踪用户滚动状态**：

   ```kotlin
   var isUserScrolling by remember { mutableStateOf(false) }
   ```

2. **使用 `Modifier.pointerInput` 检测手势**：

   ```kotlin
   LazyColumn(
       state = listState,
       modifier = Modifier
           .fillMaxSize()
           .background(Color.Black)
           .pointerInput(Unit) {
               detectDragGestures(
                   onDragStart = { isUserScrolling = true },
                   onDragEnd = { isUserScrolling = false }
               )
           }
   ) { /*...*/ }
   ```

3. **在自动滚动时检查用户滚动状态**：

   ```kotlin
   LaunchedEffect(currentIndex) {
       if (!isUserScrolling) {
           listState.animateScrollToItem(index = currentIndex)
       }
   }
   ```

### 完整代码示例：

```kotlin
@Composable
fun LyricsScreen(lyrics: List<LyricLine>, currentTime: Int) {
    val currentIndex = lyrics.indexOfLast { it.time <= currentTime }
    val listState = rememberLazyListState()
    var isUserScrolling by remember { mutableStateOf(false) }

    // 自动滚动到当前歌词
    LaunchedEffect(currentIndex) {
        if (!isUserScrolling) {
            listState.animateScrollToItem(index = currentIndex)
        }
    }

    LazyColumn(
        state = listState,
        modifier = Modifier
            .fillMaxSize()
            .background(Color.Black)
            .pointerInput(Unit) {
                detectDragGestures(
                    onDragStart = { isUserScrolling = true },
                    onDragEnd = { isUserScrolling = false }
                )
            }
    ) {
        itemsIndexed(lyrics) { index, line ->
            Text(
                text = line.text,
                color = if (index == currentIndex) Color.Yellow else Color.White,
                fontSize = if (index == currentIndex) 20.sp else 18.sp,
                modifier = Modifier.padding(vertical = 4.dp)
            )
        }
    }
}
```

### 注意事项：

- 当用户开始拖动时，`isUserScrolling` 设为 `true`，暂停自动滚动。

- 当用户停止拖动时，`isUserScrolling` 设为 `false`，恢复自动滚动。

## 综合示例

下面是将上述功能整合到一起的完整代码，包括一个模拟的播放器时间更新：

```kotlin
import android.os.Bundle
import androidx.activity.ComponentActivity
import androidx.activity.compose.setContent
import androidx.compose.foundation.background
import androidx.compose.foundation.gestures.detectDragGestures
import androidx.compose.foundation.layout.fillMaxSize
import androidx.compose.foundation.layout.padding
import androidx.compose.foundation.lazy.*
import androidx.compose.material3.Text
import androidx.compose.runtime.*
import androidx.compose.ui.Modifier
import androidx.compose.ui.graphics.Color
import androidx.compose.ui.input.pointer.pointerInput
import androidx.compose.ui.unit.dp
import androidx.compose.ui.unit.sp
import kotlinx.coroutines.delay
import kotlinx.coroutines.launch

data class LyricLine(val time: Int, val text: String)

class MainActivity : ComponentActivity() {
    @Composable
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContent {
            val sampleLyrics = listOf(
                LyricLine(0, "第一行歌词"),
                LyricLine(5, "第二行歌词"),
                LyricLine(10, "第三行歌词"),
                LyricLine(15, "第四行歌词"),
                LyricLine(20, "第五行歌词")
            )
            var currentTime by remember { mutableStateOf(0) }
            val coroutineScope = rememberCoroutineScope()

            // 模拟播放时间更新
            LaunchedEffect(Unit) {
                coroutineScope.launch {
                    while (true) {
                        delay(1000)
                        currentTime += 1
                    }
                }
            }

            LyricsScreen(lyrics = sampleLyrics, currentTime = currentTime)
        }
    }
}

@Composable
fun LyricsScreen(lyrics: List<LyricLine>, currentTime: Int) {
    val currentIndex = lyrics.indexOfLast { it.time <= currentTime }
    val listState = rememberLazyListState()
    var isUserScrolling by remember { mutableStateOf(false) }

    // 自动滚动到当前歌词
    LaunchedEffect(currentIndex) {
        if (!isUserScrolling) {
            listState.animateScrollToItem(index = currentIndex)
        }
    }

    LazyColumn(
        state = listState,
        modifier = Modifier
            .fillMaxSize()
            .background(Color.Black)
            .pointerInput(Unit) {
                detectDragGestures(
                    onDragStart = { isUserScrolling = true },
                    onDragEnd = { isUserScrolling = false }
                )
            }
    ) {
        itemsIndexed(lyrics) { index, line ->
            Text(
                text = line.text,
                color = if (index == currentIndex) Color.Yellow else Color.White,
                fontSize = if (index == currentIndex) 20.sp else 18.sp,
                modifier = Modifier.padding(vertical = 4.dp)
            )
        }
    }
}
```

## 进一步优化

### 高亮动画效果

可以在歌词高亮时添加淡入淡出的动画效果，使用 `animateColorAsState`。

```kotlin
import androidx.compose.animation.animateColorAsState
import androidx.compose.ui.text.font.FontWeight

@Composable
fun LyricsScreen(lyrics: List<LyricLine>, currentTime: Int) {
    val currentIndex = lyrics.indexOfLast { it.time <= currentTime }
    val listState = rememberLazyListState()
    var isUserScrolling by remember { mutableStateOf(false) }

    // 自动滚动到当前歌词
    LaunchedEffect(currentIndex) {
        if (!isUserScrolling) {
            listState.animateScrollToItem(index = currentIndex)
        }
    }

    LazyColumn(
        state = listState,
        modifier = Modifier
            .fillMaxSize()
            .background(Color.Black)
            .pointerInput(Unit) {
                detectDragGestures(
                    onDragStart = { isUserScrolling = true },
                    onDragEnd = { isUserScrolling = false }
                )
            }
    ) {
        itemsIndexed(lyrics) { index, line ->
            val color by animateColorAsState(
                targetValue = if (index == currentIndex) Color.Yellow else Color.White
            )
            val fontWeight = if (index == currentIndex) FontWeight.Bold else FontWeight.Normal

            Text(
                text = line.text,
                color = color,
                fontSize = 18.sp,
                fontWeight = fontWeight,
                modifier = Modifier.padding(vertical = 4.dp)
            )
        }
    }
}
```

### 精确同步

如果需要更精确地同步歌词，可以将时间戳改为毫秒级，并在更新 `currentTime` 时获取更精确的时间。

### 滚动速度调整

使用 `animateScrollToItem` 时，可以指定滚动的动画时长，或改用 `scrollToItem` 立即滚动。

## 总结

通过上述步骤，您可以实现：

- **同步歌词高亮**：根据播放时间高亮当前歌词。

- **自动滚动**：歌词更新时自动滚动到对应位置。

- **用户交互**：允许用户手动滚动歌词，并在适当时机恢复自动滚动。

希望这些内容能帮助您完善歌词展示界面！

---

好的，要在您的应用中解析 `.lrc` 格式的歌词文件，我们需要：

1. **了解 `.lrc` 文件格式**：`.lrc` 文件是一种常见的歌词文件格式，包含了时间戳和对应的歌词文本。

2. **编写解析函数**：将 `.lrc` 文件的内容读取并解析为我们之前定义的 `LyricLine` 数据类列表。

3. **处理可能的边缘情况**：如重复的时间戳、不正确的格式等。

## 1. 了解 `.lrc` 文件格式

`.lrc` 文件的基本格式如下：

```
[mm:ss.xx]歌词文本
```

- **时间戳格式**：`[分钟:秒.毫秒]`，例如 `[01:23.45]`。
- **歌词文本**：时间戳后跟随的就是对应的歌词。

一个完整的 `.lrc` 文件示例：

```
[00:12.00]第一行歌词
[00:17.50]第二行歌词
[00:23.00]第三行歌词
```

有时，时间戳可能会有多个格式，也可能包含额外的元数据，例如：

```
[ti:歌曲标题]
[ar:歌手]
[al:专辑]
```

## 2. 编写解析函数

我们需要编写一个函数，将 `.lrc` 文件的内容解析为 `List<LyricLine>`。以下是步骤：

- **读取文件内容**：将 `.lrc` 文件的内容读取为字符串。
- **逐行解析**：按行分割文件内容，逐行解析。
- **提取时间戳和歌词**：使用正则表达式匹配时间戳和歌词文本。
- **转换时间戳为毫秒**：将时间戳转换为毫秒或秒的整数表示，便于后续处理。
- **生成 `LyricLine` 对象**：将解析的数据存入 `LyricLine` 对象。

### 代码示例

```kotlin
import java.io.BufferedReader
import java.io.InputStream
import java.io.InputStreamReader

// 定义 LyricLine 数据类
data class LyricLine(val time: Long, val text: String)

// 解析 LRC 歌词文件的函数
fun parseLrcFile(inputStream: InputStream): List<LyricLine> {
    val lyrics = mutableListOf<LyricLine>()
    val reader = BufferedReader(InputStreamReader(inputStream))
    val timeRegex = "\\[(\\d{2}):(\\d{2}\\.\\d{2})]".toRegex()

    reader.useLines { lines ->
        lines.forEach { line ->
            // 跳过空行
            if (line.isBlank()) return@forEach

            // 匹配所有时间戳
            val timeMatches = timeRegex.findAll(line)

            // 提取歌词文本（去除所有时间戳后的剩余部分）
            val text = line.replace(timeRegex, "").trim()

            timeMatches.forEach { matchResult ->
                val minutes = matchResult.groupValues[1].toLongOrNull() ?: 0
                val seconds = matchResult.groupValues[2].toDoubleOrNull() ?: 0.0
                val totalMilliseconds = (minutes * 60 * 1000) + (seconds * 1000).toLong()

                lyrics.add(LyricLine(time = totalMilliseconds, text = text))
            }
        }
    }

    // 按时间排序
    return lyrics.sortedBy { it.time }
}
```

### 解释

- **使用正则表达式匹配时间戳**：`\\[(\\d{2}):(\\d{2}\\.\\d{2})]`。
  - `\\d{2}`：匹配两位数字。
  - `\\.\\d{2}`：匹配小数点和两位数字。
- **提取时间戳中的分钟和秒**：使用 `groupValues`。
- **计算总毫秒数**：将分钟和秒转换为毫秒，方便同步。
- **处理多时间戳的情况**：有时一行可能包含多个时间戳，如：

  ```
  [00:12.00][00:13.00]重复的歌词
  ```

  所以需要对每个时间戳都创建一个 `LyricLine` 对象。

- **去除时间戳后的歌词文本**：使用 `line.replace(timeRegex, "").trim()`。

## 3. 在应用中使用解析函数

### 在 `MainActivity` 中加载和解析歌词文件

假设您将 `.lrc` 文件放在 `assets` 目录下。

```kotlin
import androidx.compose.runtime.remember
import android.content.Context

class MainActivity : ComponentActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContent {
            // 加载并解析歌词
            val lyrics = remember {
                parseLrcFileFromAssets(context = this, fileName = "lyrics.lrc")
            }

            var currentTime by remember { mutableStateOf(0L) }

            // 模拟播放时间更新
            LaunchedEffect(Unit) {
                while (true) {
                    delay(1000)
                    currentTime += 1000
                }
            }

            LyricsScreen(lyrics = lyrics, currentTime = currentTime)
        }
    }
}

// 从 assets 加载并解析歌词文件
fun parseLrcFileFromAssets(context: Context, fileName: String): List<LyricLine> {
    val inputStream = context.assets.open(fileName)
    return parseLrcFile(inputStream)
}
```

### 修改 `LyricsScreen` 以适应时间戳格式

由于时间戳现在是以毫秒为单位的 `Long` 类型，需要在比较 `currentTime` 时做相应调整。

```kotlin
@Composable
fun LyricsScreen(lyrics: List<LyricLine>, currentTime: Long) {
    val currentIndex = lyrics.indexOfLast { it.time <= currentTime }
    // ...其余代码保持不变
}
```

## 4. 处理元数据

有时 `.lrc` 文件包含元数据，例如歌曲标题、歌手、专辑等。

示例：

```
[ti:歌曲标题]
[ar:歌手]
[al:专辑]
[00:12.00]第一行歌词
```

如果需要解析这些信息，可以扩展解析函数。

### 修改解析函数以处理元数据

```kotlin
data class LrcMetadata(
    val title: String? = null,
    val artist: String? = null,
    val album: String? = null
)

data class LrcFile(
    val metadata: LrcMetadata,
    val lyrics: List<LyricLine>
)

fun parseLrcFile(inputStream: InputStream): LrcFile {
    val lyrics = mutableListOf<LyricLine>()
    val reader = BufferedReader(InputStreamReader(inputStream))
    val timeRegex = "\\[(\\d{2}):(\\d{2}\\.\\d{2})]".toRegex()
    val metadataRegex = "\\[(\\w+):(.+)]".toRegex()

    var title: String? = null
    var artist: String? = null
    var album: String? = null

    reader.useLines { lines ->
        lines.forEach { line ->
            when {
                // 解析元数据
                metadataRegex.matches(line) -> {
                    val matchResult = metadataRegex.find(line)
                    val tag = matchResult?.groupValues?.get(1)
                    val value = matchResult?.groupValues?.get(2)?.trim()
                    when (tag) {
                        "ti" -> title = value
                        "ar" -> artist = value
                        "al" -> album = value
                    }
                }
                // 解析歌词行
                timeRegex.containsMatchIn(line) -> {
                    val timeMatches = timeRegex.findAll(line)
                    val text = line.replace(timeRegex, "").trim()

                    timeMatches.forEach { matchResult ->
                        val minutes = matchResult.groupValues[1].toLongOrNull() ?: 0
                        val seconds = matchResult.groupValues[2].toDoubleOrNull() ?: 0.0
                        val totalMilliseconds = (minutes * 60 * 1000) + (seconds * 1000).toLong()

                        lyrics.add(LyricLine(time = totalMilliseconds, text = text))
                    }
                }
                else -> {
                    // 处理其他情况，如空行或无效格式
                }
            }
        }
    }

    return LrcFile(
        metadata = LrcMetadata(title = title, artist = artist, album = album),
        lyrics = lyrics.sortedBy { it.time }
    )
}
```

### 在应用中使用解析的元数据

```kotlin
class MainActivity : ComponentActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContent {
            val lrcFile = remember {
                parseLrcFileFromAssets(context = this, fileName = "lyrics.lrc")
            }
            val lyrics = lrcFile.lyrics
            val metadata = lrcFile.metadata

            // 显示元数据（标题、歌手）
            Column(modifier = Modifier.fillMaxSize()) {
                Text(
                    text = metadata.title ?: "未知标题",
                    color = Color.White,
                    fontSize = 24.sp,
                    modifier = Modifier.padding(16.dp)
                )
                Text(
                    text = metadata.artist ?: "未知艺术家",
                    color = Color.Gray,
                    fontSize = 18.sp,
                    modifier = Modifier.padding(start = 16.dp, bottom = 8.dp)
                )

                var currentTime by remember { mutableStateOf(0L) }

                // 模拟播放时间更新
                LaunchedEffect(Unit) {
                    while (true) {
                        delay(1000)
                        currentTime += 1000
                    }
                }

                LyricsScreen(lyrics = lyrics, currentTime = currentTime)
            }
        }
    }
}
```

## 5. 处理可能的边缘情况

### 多时间戳

有些歌词行可能包含多个时间戳，表示同一行歌词在不同时间重复出现。

示例：

```
[00:12.00][00:24.00]重复的歌词
```

在解析函数中，我们已经通过 `timeMatches.forEach` 处理了这种情况。

### 时间戳格式不一致

有些 `.lrc` 文件的时间戳可能不包含毫秒，或者毫秒部分长度不一致。

示例：

```
[01:23]没有毫秒的时间戳
[02:34.5]只有一位毫秒
```

为了解决这个问题，可以调整正则表达式：

```kotlin
val timeRegex = "\\[(\\d{2}):(\\d{2}(?:\\.\\d{1,2})?)]".toRegex()
```

- `(?:\\.\\d{1,2})?`：匹配可选的小数点和一到两位数字。

### 错误的时间戳格式

对于无法解析的时间戳，可以选择忽略或记录日志。

```kotlin
val minutes = matchResult.groupValues[1].toLongOrNull()
val seconds = matchResult.groupValues[2].toDoubleOrNull()
if (minutes == null || seconds == null) {
    // 无效的时间戳，跳过
    return@forEach
}
```

## 6. 完整的解析函数

综合以上修改，最终的解析函数如下：

```kotlin
fun parseLrcFile(inputStream: InputStream): LrcFile {
    val lyrics = mutableListOf<LyricLine>()
    val reader = BufferedReader(InputStreamReader(inputStream))
    val timeRegex = "\\[(\\d{2}):(\\d{2}(?:\\.\\d{1,2})?)]".toRegex()
    val metadataRegex = "\\[(\\w+):(.+)]".toRegex()

    var title: String? = null
    var artist: String? = null
    var album: String? = null

    reader.useLines { lines ->
        lines.forEach { line ->
            when {
                metadataRegex.matches(line) -> {
                    val matchResult = metadataRegex.find(line)
                    val tag = matchResult?.groupValues?.get(1)
                    val value = matchResult?.groupValues?.get(2)?.trim()
                    when (tag) {
                        "ti" -> title = value
                        "ar" -> artist = value
                        "al" -> album = value
                        // 可以添加更多的元数据处理
                    }
                }
                timeRegex.containsMatchIn(line) -> {
                    val timeMatches = timeRegex.findAll(line)
                    val text = line.replace(timeRegex, "").trim()

                    timeMatches.forEach { matchResult ->
                        val minutes = matchResult.groupValues[1].toLongOrNull()
                        val seconds = matchResult.groupValues[2].toDoubleOrNull()
                        if (minutes == null || seconds == null) {
                            // 无效的时间戳，跳过
                            return@forEach
                        }
                        val totalMilliseconds = (minutes * 60 * 1000) + (seconds * 1000).toLong()

                        lyrics.add(LyricLine(time = totalMilliseconds, text = text))
                    }
                }
                else -> {
                    // 处理其他情况，如空行或无效格式
                }
            }
        }
    }

    return LrcFile(
        metadata = LrcMetadata(title = title, artist = artist, album = album),
        lyrics = lyrics.sortedBy { it.time }
    )
}
```

## 7. 测试解析函数

为了确保解析函数的正确性，建议对不同的 `.lrc` 文件进行测试，包括：

- 包含元数据和不包含元数据的文件。
- 时间戳格式不同的文件。
- 包含多时间戳的歌词行。
- 含有错误格式的文件。

## 8. 处理编码问题

有些 `.lrc` 文件可能使用不同的字符编码（如 GBK、UTF-8）。在读取文件时，需要指定正确的编码：

```kotlin
val reader = BufferedReader(InputStreamReader(inputStream, Charsets.UTF_8))
```

如果发现解析后的歌词出现乱码，可能需要尝试不同的字符集，例如 `Charsets.GBK`。然而，Android 默认不支持 GBK 编码，需要引入第三方库或自行实现。

## 9. 小结

通过解析 `.lrc` 文件，您可以将歌词内容和时间戳加载到应用中，实现同步显示歌词的功能。以下是实现步骤：

1. **定义数据结构**：`LyricLine`、`LrcMetadata`、`LrcFile`。
2. **编写解析函数**：`parseLrcFile`，处理时间戳、歌词文本和元数据。
3. **处理边缘情况**：考虑不同的时间戳格式、多时间戳、错误格式等。
4. **在应用中使用**：将解析得到的歌词列表传递给 `LyricsScreen` 进行显示。

希望这些内容能帮助您成功解析 `.lrc` 歌词文件并实现歌词同步显示！

---

好的，如果您希望直接从字符串加载歌词，而不通过文件，我们可以修改解析函数，使其接受一个 `String` 类型的参数。这样，您就可以从任意来源获取歌词字符串，例如从网络请求中获取的歌词内容，然后直接解析。

## 1. 修改解析函数以接受字符串

我们将原来的 `parseLrcFile` 函数修改为 `parseLrcString`，它接受一个 `String` 参数，而不是 `InputStream`。解析逻辑基本保持不变。

### 修改后的解析函数

```kotlin
fun parseLrcString(lrcContent: String): LrcFile {
    val lyrics = mutableListOf<LyricLine>()
    val timeRegex = "\\[(\\d{2}):(\\d{2}(?:\\.\\d{1,2})?)]".toRegex()
    val metadataRegex = "\\[(\\w+):(.+)]".toRegex()

    var title: String? = null
    var artist: String? = null
    var album: String? = null

    // 将字符串按行分割
    val lines = lrcContent.lines()

    lines.forEach { line ->
        when {
            metadataRegex.matches(line) -> {
                val matchResult = metadataRegex.find(line)
                val tag = matchResult?.groupValues?.get(1)
                val value = matchResult?.groupValues?.get(2)?.trim()
                when (tag) {
                    "ti" -> title = value
                    "ar" -> artist = value
                    "al" -> album = value
                    // 可以添加更多的元数据处理
                }
            }
            timeRegex.containsMatchIn(line) -> {
                val timeMatches = timeRegex.findAll(line)
                val text = line.replace(timeRegex, "").trim()

                timeMatches.forEach { matchResult ->
                    val minutes = matchResult.groupValues[1].toLongOrNull()
                    val seconds = matchResult.groupValues[2].toDoubleOrNull()
                    if (minutes == null || seconds == null) {
                        // 无效的时间戳，跳过
                        return@forEach
                    }
                    val totalMilliseconds = (minutes * 60 * 1000) + (seconds * 1000).toLong()

                    lyrics.add(LyricLine(time = totalMilliseconds, text = text))
                }
            }
            else -> {
                // 处理其他情况，如空行或无效格式
            }
        }
    }

    return LrcFile(
        metadata = LrcMetadata(title = title, artist = artist, album = album),
        lyrics = lyrics.sortedBy { it.time }
    )
}
```

### 解释

- **输入参数**：`lrcContent: String`，表示歌词的完整内容。
- **字符串分割**：使用 `lrcContent.lines()` 将字符串按行分割为列表。
- **解析逻辑**：与之前的函数相同，只是将读取行的方式从文件读取改为字符串列表遍历。

## 2. 在应用中使用解析函数

您可以直接在 `setContent` 或任何需要的地方调用 `parseLrcString`，传入您的歌词字符串。

### 示例

假设我们有一个歌词字符串：

```kotlin
val lrcContent = """
[ti:歌曲标题]
[ar:歌手]
[al:专辑]
[00:12.00]第一行歌词
[00:17.50]第二行歌词
[00:23.00]第三行歌词
""".trimIndent()
```

### 在 `MainActivity` 中使用

```kotlin
class MainActivity : ComponentActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContent {
            val lrcContent = """
            [ti:歌曲标题]
            [ar:歌手]
            [al:专辑]
            [00:12.00]第一行歌词
            [00:17.50]第二行歌词
            [00:23.00]第三行歌词
            """.trimIndent()

            // 解析歌词字符串
            val lrcFile = parseLrcString(lrcContent)
            val lyrics = lrcFile.lyrics
            val metadata = lrcFile.metadata

            var currentTime by remember { mutableStateOf(0L) }

            // 模拟播放时间更新
            LaunchedEffect(Unit) {
                while (true) {
                    delay(1000)
                    currentTime += 1000
                }
            }

            // 显示歌词界面
            LyricsScreen(lyrics = lyrics, currentTime = currentTime, metadata = metadata)
        }
    }
}
```

### 修改 `LyricsScreen` 以显示元数据

```kotlin
@Composable
fun LyricsScreen(lyrics: List<LyricLine>, currentTime: Long, metadata: LrcMetadata) {
    val currentIndex = lyrics.indexOfLast { it.time <= currentTime }
    val listState = rememberLazyListState()
    var isUserScrolling by remember { mutableStateOf(false) }

    // 自动滚动到当前歌词
    LaunchedEffect(currentIndex) {
        if (!isUserScrolling) {
            listState.animateScrollToItem(index = currentIndex)
        }
    }

    Column(
        modifier = Modifier
            .fillMaxSize()
            .background(Color.Black)
    ) {
        // 显示元数据（标题和艺术家）
        Text(
            text = metadata.title ?: "未知标题",
            color = Color.White,
            fontSize = 24.sp,
            modifier = Modifier.padding(16.dp)
        )
        Text(
            text = metadata.artist ?: "未知艺术家",
            color = Color.Gray,
            fontSize = 18.sp,
            modifier = Modifier.padding(start = 16.dp, bottom = 8.dp)
        )

        // 显示歌词列表
        LazyColumn(
            state = listState,
            modifier = Modifier
                .weight(1f)
                .fillMaxWidth()
                .pointerInput(Unit) {
                    detectDragGestures(
                        onDragStart = { isUserScrolling = true },
                        onDragEnd = { isUserScrolling = false }
                    )
                }
        ) {
            itemsIndexed(lyrics) { index, line ->
                val color by animateColorAsState(
                    targetValue = if (index == currentIndex) Color.Yellow else Color.White
                )
                val fontWeight = if (index == currentIndex) FontWeight.Bold else FontWeight.Normal

                Text(
                    text = line.text,
                    color = color,
                    fontSize = 18.sp,
                    fontWeight = fontWeight,
                    modifier = Modifier.padding(vertical = 4.dp, horizontal = 16.dp)
                )
            }
        }
    }
}
```

### 解释

- **`metadata` 参数**：将元数据传递给 `LyricsScreen`，以便在界面上显示歌曲标题和艺术家。
- **`Column` 布局**：在顶部显示元数据信息，下面是歌词列表。
- **`modifier.weight(1f)`**：使 `LazyColumn` 占据剩余的空间。
- **手势检测**：在 `LazyColumn` 中检测用户滚动手势，控制自动滚动的行为。

## 3. 从网络获取歌词字符串

如果您的歌词字符串来自网络请求，例如通过 API 获取，可以在异步加载后解析并更新界面。

### 示例

```kotlin
@Composable
fun LyricsApp() {
    var lrcFile by remember { mutableStateOf<LrcFile?>(null) }
    var currentTime by remember { mutableStateOf(0L) }

    // 模拟从网络获取歌词
    LaunchedEffect(Unit) {
        // 模拟网络请求延迟
        delay(2000)
        val lrcContent = """
        [ti:歌曲标题]
        [ar:歌手]
        [al:专辑]
        [00:12.00]第一行歌词
        [00:17.50]第二行歌词
        [00:23.00]第三行歌词
        """.trimIndent()

        lrcFile = parseLrcString(lrcContent)
    }

    // 模拟播放时间更新
    LaunchedEffect(Unit) {
        while (true) {
            delay(1000)
            currentTime += 1000
        }
    }

    if (lrcFile != null) {
        LyricsScreen(
            lyrics = lrcFile!!.lyrics,
            currentTime = currentTime,
            metadata = lrcFile!!.metadata
        )
    } else {
        // 显示加载中或占位界面
        Box(
            modifier = Modifier
                .fillMaxSize()
                .background(Color.Black),
            contentAlignment = Alignment.Center
        ) {
            Text(text = "加载中...", color = Color.White, fontSize = 18.sp)
        }
    }
}
```

### 解释

- **使用 `LaunchedEffect` 模拟网络请求**：在实际应用中，您将使用类似 `Retrofit` 的网络库进行请求。
- **更新 `lrcFile` 状态**：在获取到歌词字符串后，解析并更新状态，触发界面重新组合。
- **显示加载状态**：在 `lrcFile` 为 `null` 时，显示一个加载中的界面。

## 4. 处理不同的字符编码

当从不同的来源获取歌词字符串时，可能会遇到不同的字符编码问题。例如，某些歌词可能是以 `GBK` 编码的。如果您从网络请求中获取的是 `String`，通常已经是正确的编码。但如果您从字节数组或其他二进制数据中获取，需要指定编码。

### 示例

```kotlin
// 从字节数组中获取歌词字符串，指定编码
val lrcBytes: ByteArray = // 从某处获取的字节数组
val lrcContent = String(lrcBytes, charset("GBK")) // 指定编码为 GBK

// 然后解析歌词
val lrcFile = parseLrcString(lrcContent)
```

## 5. 完整的代码示例

下面是一个完整的代码示例，包括解析函数、主活动和歌词界面：

```kotlin
// 数据类定义
data class LyricLine(val time: Long, val text: String)
data class LrcMetadata(
    val title: String? = null,
    val artist: String? = null,
    val album: String? = null
)
data class LrcFile(
    val metadata: LrcMetadata,
    val lyrics: List<LyricLine>
)

// 解析 LRC 歌词字符串的函数
fun parseLrcString(lrcContent: String): LrcFile {
    val lyrics = mutableListOf<LyricLine>()
    val timeRegex = "\\[(\\d{2}):(\\d{2}(?:\\.\\d{1,2})?)]".toRegex()
    val metadataRegex = "\\[(\\w+):(.+)]".toRegex()

    var title: String? = null
    var artist: String? = null
    var album: String? = null

    // 将字符串按行分割
    val lines = lrcContent.lines()

    lines.forEach { line ->
        when {
            metadataRegex.matches(line) -> {
                val matchResult = metadataRegex.find(line)
                val tag = matchResult?.groupValues?.get(1)
                val value = matchResult?.groupValues?.get(2)?.trim()
                when (tag) {
                    "ti" -> title = value
                    "ar" -> artist = value
                    "al" -> album = value
                    // 可以添加更多的元数据处理
                }
            }
            timeRegex.containsMatchIn(line) -> {
                val timeMatches = timeRegex.findAll(line)
                val text = line.replace(timeRegex, "").trim()

                timeMatches.forEach { matchResult ->
                    val minutes = matchResult.groupValues[1].toLongOrNull()
                    val seconds = matchResult.groupValues[2].toDoubleOrNull()
                    if (minutes == null || seconds == null) {
                        // 无效的时间戳，跳过
                        return@forEach
                    }
                    val totalMilliseconds = (minutes * 60 * 1000) + (seconds * 1000).toLong()

                    lyrics.add(LyricLine(time = totalMilliseconds, text = text))
                }
            }
            else -> {
                // 处理其他情况，如空行或无效格式
            }
        }
    }

    return LrcFile(
        metadata = LrcMetadata(title = title, artist = artist, album = album),
        lyrics = lyrics.sortedBy { it.time }
    )
}

// 主活动
class MainActivity : ComponentActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContent {
            LyricsApp()
        }
    }
}

// 应用入口
@Composable
fun LyricsApp() {
    var lrcFile by remember { mutableStateOf<LrcFile?>(null) }
    var currentTime by remember { mutableStateOf(0L) }

    // 模拟从网络获取歌词
    LaunchedEffect(Unit) {
        // 模拟网络请求延迟
        delay(2000)
        val lrcContent = """
        [ti:歌曲标题]
        [ar:歌手]
        [al:专辑]
        [00:12.00]第一行歌词
        [00:17.50]第二行歌词
        [00:23.00]第三行歌词
        """.trimIndent()

        lrcFile = parseLrcString(lrcContent)
    }

    // 模拟播放时间更新
    LaunchedEffect(Unit) {
        while (true) {
            delay(1000)
            currentTime += 1000
        }
    }

    if (lrcFile != null) {
        LyricsScreen(
            lyrics = lrcFile!!.lyrics,
            currentTime = currentTime,
            metadata = lrcFile!!.metadata
        )
    } else {
        // 显示加载中或占位界面
        Box(
            modifier = Modifier
                .fillMaxSize()
                .background(Color.Black),
            contentAlignment = Alignment.Center
        ) {
            Text(text = "加载中...", color = Color.White, fontSize: 18.sp)
        }
    }
}

// 歌词界面
@Composable
fun LyricsScreen(lyrics: List<LyricLine>, currentTime: Long, metadata: LrcMetadata) {
    val currentIndex = lyrics.indexOfLast { it.time <= currentTime }
    val listState = rememberLazyListState()
    var isUserScrolling by remember { mutableStateOf(false) }

    // 自动滚动到当前歌词
    LaunchedEffect(currentIndex) {
        if (!isUserScrolling) {
            listState.animateScrollToItem(index = currentIndex)
        }
    }

    Column(
        modifier = Modifier
            .fillMaxSize()
            .background(Color.Black)
    ) {
        // 显示元数据（标题和艺术家）
        Text(
            text = metadata.title ?: "未知标题",
            color = Color.White,
            fontSize = 24.sp,
            modifier = Modifier.padding(16.dp)
        )
        Text(
            text = metadata.artist ?: "未知艺术家",
            color = Color.Gray,
            fontSize = 18.sp,
            modifier = Modifier.padding(start = 16.dp, bottom = 8.dp)
        )

        // 显示歌词列表
        LazyColumn(
            state = listState,
            modifier = Modifier
                .weight(1f)
                .fillMaxWidth()
                .pointerInput(Unit) {
                    detectDragGestures(
                        onDragStart = { isUserScrolling = true },
                        onDragEnd = { isUserScrolling = false }
                    )
                }
        ) {
            itemsIndexed(lyrics) { index, line ->
                val color by animateColorAsState(
                    targetValue = if (index == currentIndex) Color.Yellow else Color.White
                )
                val fontWeight = if (index == currentIndex) FontWeight.Bold else FontWeight.Normal

                Text(
                    text = line.text,
                    color = color,
                    fontSize = 18.sp,
                    fontWeight = fontWeight,
                    modifier = Modifier.padding(vertical = 4.dp, horizontal = 16.dp)
                )
            }
        }
    }
}
```

## 6. 小结

通过将解析函数修改为接受字符串输入，您可以更灵活地从不同来源加载歌词，例如：

- 从网络请求中获取歌词字符串。
- 从数据库或缓存中读取歌词。
- 直接在代码中定义歌词字符串（用于测试或演示）。

在应用中，您只需调用 `parseLrcString`，传入歌词字符串，即可得到解析后的 `LrcFile` 对象，然后将其用于显示歌词界面。

希望这些内容能帮助您实现从字符串直接加载并解析 `.lrc` 歌词的功能！
