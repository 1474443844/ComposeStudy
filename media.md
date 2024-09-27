在使用 ExoPlayer 时，设置 `MediaMetadata` 的 `title` 属性可以为您的媒体项提供更丰富的描述信息，如显示歌曲标题、艺术家名称等。这对于提升用户体验和实现更具交互性的播放器界面非常有帮助。本文将详细介绍如何在创建 `MediaItem` 时设置 `mediaMetadata.title`，并提供 Java 和 Kotlin 的代码示例。

## 1. 理解 `MediaMetadata` 和 `MediaItem`

`MediaMetadata` 是 ExoPlayer 用于存储媒体项相关的元数据（如标题、专辑、艺术家等）的类。每个 `MediaItem` 都可以包含一个 `MediaMetadata` 对象，以便在播放器中显示相关信息。

## 2. 设置 `mediaMetadata.title` 的步骤

### 2.1 使用 `MediaMetadata.Builder` 构建 `MediaMetadata`

首先，您需要使用 `MediaMetadata.Builder` 来创建一个 `MediaMetadata` 对象，并设置所需的属性（如标题）。

### 2.2 将 `MediaMetadata` 设置到 `MediaItem`

在创建 `MediaItem` 时，将构建好的 `MediaMetadata` 对象与媒体 URI 关联起来。

### 2.3 将 `MediaItem` 添加到播放器

最后，将配置好的 `MediaItem` 添加到 ExoPlayer 播放器中，并进行播放。

## 3. Java 示例

以下是使用 Java 语言设置 `mediaMetadata.title` 的完整示例：

```java
import android.net.Uri;
import android.os.Bundle;
import androidx.appcompat.app.AppCompatActivity;
import com.google.android.exoplayer2.MediaItem;
import com.google.android.exoplayer2.MediaMetadata;
import com.google.android.exoplayer2.SimpleExoPlayer;
import com.google.android.exoplayer2.ui.PlayerView;

public class MainActivity extends AppCompatActivity {

    private SimpleExoPlayer player;
    private PlayerView playerView;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main); // 确保布局文件中包含 PlayerView

        // 初始化 PlayerView
        playerView = findViewById(R.id.player_view);

        // 初始化 ExoPlayer
        player = new SimpleExoPlayer.Builder(this).build();
        playerView.setPlayer(player);

        // 创建 MediaMetadata 并设置标题
        MediaMetadata mediaMetadata = new MediaMetadata.Builder()
                .setTitle("歌曲标题示例") // 设置标题
                .setArtist("艺术家名称")   // 可选：设置艺术家
                .setAlbumTitle("专辑名称") // 可选：设置专辑标题
                .build();

        // 创建 MediaItem 并关联 MediaMetadata
        MediaItem mediaItem = new MediaItem.Builder()
                .setUri(Uri.parse("https://example.com/song1.mp3")) // 替换为实际的媒体 URI
                .setMediaMetadata(mediaMetadata)
                .build();

        // 将 MediaItem 添加到播放器
        player.setMediaItem(mediaItem);

        // 准备并开始播放
        player.prepare();
        player.play();
    }

    @Override
    protected void onDestroy() {
        super.onDestroy();
        // 释放播放器资源
        if (player != null) {
            player.release();
            player = null;
        }
    }
}
```

### 说明：

1. **初始化 ExoPlayer 和 PlayerView**：
   - 在 `onCreate` 方法中，初始化 `SimpleExoPlayer` 实例并将其与 `PlayerView` 关联。

2. **创建并设置 `MediaMetadata`**：
   - 使用 `MediaMetadata.Builder` 设置标题、艺术家和专辑等信息。
   - 这些信息将用于显示在播放器的 UI 中，例如在 `PlayerView` 上方显示当前播放的歌曲标题。

3. **创建 `MediaItem` 并关联 `MediaMetadata`**：
   - 使用 `MediaItem.Builder` 创建一个新的媒体项，并将先前创建的 `MediaMetadata` 关联到该媒体项。

4. **添加 `MediaItem` 到播放器并开始播放**：
   - 使用 `player.setMediaItem(mediaItem)` 将媒体项添加到播放器中。
   - 调用 `player.prepare()` 准备播放器，并使用 `player.play()` 开始播放。

## 4. Kotlin 示例

以下是使用 Kotlin 语言设置 `mediaMetadata.title` 的完整示例：

```kotlin
import android.net.Uri
import android.os.Bundle
import androidx.appcompat.app.AppCompatActivity
import com.google.android.exoplayer2.MediaItem
import com.google.android.exoplayer2.MediaMetadata
import com.google.android.exoplayer2.SimpleExoPlayer
import com.google.android.exoplayer2.ui.PlayerView

class MainActivity : AppCompatActivity() {

    private lateinit var player: SimpleExoPlayer
    private lateinit var playerView: PlayerView

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main) // 确保布局文件中包含 PlayerView

        // 初始化 PlayerView
        playerView = findViewById(R.id.player_view)

        // 初始化 ExoPlayer
        player = SimpleExoPlayer.Builder(this).build()
        playerView.player = player

        // 创建 MediaMetadata 并设置标题
        val mediaMetadata = MediaMetadata.Builder()
            .setTitle("歌曲标题示例") // 设置标题
            .setArtist("艺术家名称")    // 可选：设置艺术家
            .setAlbumTitle("专辑名称")  // 可选：设置专辑标题
            .build()

        // 创建 MediaItem 并关联 MediaMetadata
        val mediaItem = MediaItem.Builder()
            .setUri(Uri.parse("https://example.com/song1.mp3")) // 替换为实际的媒体 URI
            .setMediaMetadata(mediaMetadata)
            .build()

        // 将 MediaItem 添加到播放器
        player.setMediaItem(mediaItem)

        // 准备并开始播放
        player.prepare()
        player.play()
    }

    override fun onDestroy() {
        super.onDestroy()
        // 释放播放器资源
        player.release()
    }
}
```

### 说明：

Kotlin 示例与 Java 示例的逻辑基本相同，但语法更简洁。主要步骤包括初始化播放器、创建并设置 `MediaMetadata`、关联到 `MediaItem`，然后添加到播放器并开始播放。

## 5. 处理多个媒体项并设置各自的标题

如果您有多个媒体项，并希望为每个媒体项设置不同的标题，可以按照以下方式进行：

### Java 示例

```java
// 创建第一个 MediaMetadata
MediaMetadata metadata1 = new MediaMetadata.Builder()
        .setTitle("第一首歌曲")
        .setArtist("艺术家A")
        .build();

// 创建第一个 MediaItem
MediaItem mediaItem1 = new MediaItem.Builder()
        .setUri(Uri.parse("https://example.com/song1.mp3"))
        .setMediaMetadata(metadata1)
        .build();

// 创建第二个 MediaMetadata
MediaMetadata metadata2 = new MediaMetadata.Builder()
        .setTitle("第二首歌曲")
        .setArtist("艺术家B")
        .build();

// 创建第二个 MediaItem
MediaItem mediaItem2 = new MediaItem.Builder()
        .setUri(Uri.parse("https://example.com/song2.mp3"))
        .setMediaMetadata(metadata2)
        .build();

// 将多个 MediaItem 添加到播放器
player.addMediaItem(mediaItem1);
player.addMediaItem(mediaItem2);

// 准备并开始播放
player.prepare();
player.play();
```

### Kotlin 示例

```kotlin
// 创建第一个 MediaMetadata
val metadata1 = MediaMetadata.Builder()
    .setTitle("第一首歌曲")
    .setArtist("艺术家A")
    .build()

// 创建第一个 MediaItem
val mediaItem1 = MediaItem.Builder()
    .setUri(Uri.parse("https://example.com/song1.mp3"))
    .setMediaMetadata(metadata1)
    .build()

// 创建第二个 MediaMetadata
val metadata2 = MediaMetadata.Builder()
    .setTitle("第二首歌曲")
    .setArtist("艺术家B")
    .build()

// 创建第二个 MediaItem
val mediaItem2 = MediaItem.Builder()
    .setUri(Uri.parse("https://example.com/song2.mp3"))
    .setMediaMetadata(metadata2)
    .build()

// 将多个 MediaItem 添加到播放器
player.addMediaItem(mediaItem1)
player.addMediaItem(mediaItem2)

// 准备并开始播放
player.prepare()
player.play()
```

## 6. 在 UI 中显示 `mediaMetadata.title`

为了在播放器的 UI 中显示 `mediaMetadata.title`，您可以使用 `PlayerView` 以及其他 UI 组件（如 `TextView`）。以下是一个简单的示例，展示如何在布局中显示当前播放的歌曲标题。

### 布局文件（XML）

```xml
<!-- activity_main.xml -->
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:orientation="vertical"
    android:layout_width="match_parent"
    android:layout_height="match_parent">
    
    <!-- 显示歌曲标题的 TextView -->
    <TextView
        android:id="@+id/song_title"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="歌曲标题"
        android:textSize="18sp"
        android:padding="16dp" />

    <!-- ExoPlayer 的 PlayerView -->
    <com.google.android.exoplayer2.ui.PlayerView
        android:id="@+id/player_view"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        app:show_shuffle_button="true"
        app:show_fastforward_button="true"
        app:show_previous_button="true"
        app:show_next_button="true"
        app:show_repeat_button="true" />
</LinearLayout>
```

### Java 示例

```java
import android.net.Uri;
import android.os.Bundle;
import android.widget.TextView;
import androidx.appcompat.app.AppCompatActivity;
import com.google.android.exoplayer2.MediaItem;
import com.google.android.exoplayer2.MediaMetadata;
import com.google.android.exoplayer2.Player;
import com.google.android.exoplayer2.SimpleExoPlayer;
import com.google.android.exoplayer2.ui.PlayerView;

public class MainActivity extends AppCompatActivity {

    private SimpleExoPlayer player;
    private PlayerView playerView;
    private TextView songTitleTextView;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main); // 确保布局文件中包含 PlayerView 和 TextView

        // 初始化 UI 组件
        playerView = findViewById(R.id.player_view);
        songTitleTextView = findViewById(R.id.song_title);

        // 初始化 ExoPlayer
        player = new SimpleExoPlayer.Builder(this).build();
        playerView.setPlayer(player);

        // 创建 MediaMetadata 并设置标题
        MediaMetadata mediaMetadata = new MediaMetadata.Builder()
                .setTitle("歌曲标题示例")
                .build();

        // 创建 MediaItem 并关联 MediaMetadata
        MediaItem mediaItem = new MediaItem.Builder()
                .setUri(Uri.parse("https://example.com/song1.mp3"))
                .setMediaMetadata(mediaMetadata)
                .build();

        // 将 MediaItem 添加到播放器
        player.setMediaItem(mediaItem);

        // 添加播放器监听器以更新 UI
        player.addListener(new Player.Listener() {
            @Override
            public void onMediaMetadataChanged(MediaMetadata mediaMetadata) {
                if (mediaMetadata != null && mediaMetadata.title != null) {
                    songTitleTextView.setText(mediaMetadata.title.toString());
                }
            }
        });

        // 准备并开始播放
        player.prepare();
        player.play();
    }

    @Override
    protected void onDestroy() {
        super.onDestroy();
        // 释放播放器资源
        if (player != null) {
            player.release();
            player = null;
        }
    }
}
```

### Kotlin 示例

```kotlin
import android.net.Uri
import android.os.Bundle
import android.widget.TextView
import androidx.appcompat.app.AppCompatActivity
import com.google.android.exoplayer2.MediaItem
import com.google.android.exoplayer2.MediaMetadata
import com.google.android.exoplayer2.Player
import com.google.android.exoplayer2.SimpleExoPlayer
import com.google.android.exoplayer2.ui.PlayerView

class MainActivity : AppCompatActivity() {

    private lateinit var player: SimpleExoPlayer
    private lateinit var playerView: PlayerView
    private lateinit var songTitleTextView: TextView

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main) // 确保布局文件中包含 PlayerView 和 TextView

        // 初始化 UI 组件
        playerView = findViewById(R.id.player_view)
        songTitleTextView = findViewById(R.id.song_title)

        // 初始化 ExoPlayer
        player = SimpleExoPlayer.Builder(this).build()
        playerView.player = player

        // 创建 MediaMetadata 并设置标题
        val mediaMetadata = MediaMetadata.Builder()
            .setTitle("歌曲标题示例")
            .build()

        // 创建 MediaItem 并关联 MediaMetadata
        val mediaItem = MediaItem.Builder()
            .setUri(Uri.parse("https://example.com/song1.mp3"))
            .setMediaMetadata(mediaMetadata)
            .build()

        // 将 MediaItem 添加到播放器
        player.setMediaItem(mediaItem)

        // 添加播放器监听器以更新 UI
        player.addListener(object : Player.Listener {
            override fun onMediaMetadataChanged(mediaMetadata: MediaMetadata) {
                super.onMediaMetadataChanged(mediaMetadata)
                songTitleTextView.text = mediaMetadata.title ?: "未知标题"
            }
        })

        // 准备并开始播放
        player.prepare()
        player.play()
    }

    override fun onDestroy() {
        super.onDestroy()
        // 释放播放器资源
        player.release()
    }
}
```

### 说明：

1. **布局文件**：
   - 在布局文件中添加一个 `TextView` 用于显示歌曲标题。
   - 使用 `PlayerView` 显示 ExoPlayer 的播放控件。

2. **播放器监听器**：
   - 通过实现 `Player.Listener` 并重写 `onMediaMetadataChanged` 方法，当 `MediaMetadata` 发生变化时更新 `TextView` 中显示的标题。

3. **动态更新标题**：
   - 当播放列表中的媒体项切换或元数据变化时，`onMediaMetadataChanged` 方法会被调用，从而实时更新 UI 中的歌曲标题。

## 7. 高级用法：动态修改 `MediaMetadata`

如果您需要在播放过程中动态修改 `MediaMetadata`，例如更改歌曲标题，可以按照以下步骤操作：

### Java 示例

```java
// 获取当前 MediaItem 的索引
int currentIndex = player.getCurrentWindowIndex();

// 获取当前 MediaItem
MediaItem currentMediaItem = player.getMediaItemAt(currentIndex);

// 创建新的 MediaMetadata
MediaMetadata newMetadata = new MediaMetadata.Builder(currentMediaItem.mediaMetadata)
        .setTitle("更新后的歌曲标题")
        .build();

// 创建新的 MediaItem 并替换旧的
MediaItem updatedMediaItem = new MediaItem.Builder(currentMediaItem)
        .setMediaMetadata(newMetadata)
        .build();

// 替换播放列表中的媒体项
player.setMediaItem(currentIndex, updatedMediaItem);

// 更新播放器
player.prepare();
```

### Kotlin 示例

```kotlin
// 获取当前 MediaItem 的索引
val currentIndex = player.currentWindowIndex

// 获取当前 MediaItem
val currentMediaItem = player.getMediaItemAt(currentIndex)

// 创建新的 MediaMetadata
val newMetadata = currentMediaItem.mediaMetadata.buildUpon()
    .setTitle("更新后的歌曲标题")
    .build()

// 创建新的 MediaItem 并替换旧的
val updatedMediaItem = currentMediaItem.buildUpon()
    .setMediaMetadata(newMetadata)
    .build()

// 替换播放列表中的媒体项
player.setMediaItem(currentIndex, updatedMediaItem)

// 更新播放器
player.prepare()
```

### 说明：

1. **获取当前媒体项**：
   - 使用 `player.getCurrentWindowIndex()` 获取当前播放的媒体项在播放列表中的索引。
   - 使用 `player.getMediaItemAt(index)` 获取当前播放的 `MediaItem`。

2. **修改 `MediaMetadata`**：
   - 使用 `MediaMetadata.Builder` 从现有的 `MediaMetadata` 创建一个新的实例，并修改所需的属性（如标题）。

3. **更新 `MediaItem`**：
   - 使用 `MediaItem.Builder` 从现有的 `MediaItem` 创建一个新的实例，并设置更新后的 `MediaMetadata`。

4. **替换播放列表中的媒体项**：
   - 使用 `player.setMediaItem(index, updatedMediaItem)` 将更新后的 `MediaItem` 替换到播放列表中。

5. **准备播放器**：
   - 调用 `player.prepare()` 以使更改生效。

## 8. 总结

通过以上步骤，您可以在 ExoPlayer 中轻松设置和管理 `mediaMetadata.title` 以及其他元数据。这不仅有助于提升播放器的用户体验，还能为用户提供更丰富的信息展示。如果您需要进一步自定义播放器的行为或 UI，可以结合 `MediaMetadata` 提供的其他属性，如艺术家名称、专辑封面等，打造更完善的音乐播放器应用。

如果您在实现过程中遇到任何问题，欢迎继续提问！




在使用 ExoPlayer 管理播放列表时，获取当前播放列表（即所有已添加的 `MediaItem`）是一个常见的需求。本文将详细介绍如何通过 ExoPlayer 的 API 获取播放列表，包括获取播放列表中的所有媒体项、当前播放的媒体项以及播放列表的长度。以下内容将以 Java 和 Kotlin 代码示例进行说明。

## 1. 理解 ExoPlayer 的播放列表管理

ExoPlayer 使用 `MediaItem` 对象来表示每个媒体文件。播放列表实际上是一个 `MediaItem` 的有序集合。ExoPlayer 提供了多种方法来管理和访问这些媒体项，包括添加、删除和获取媒体项。

## 2. 获取播放列表中的所有媒体项

### 2.1 使用 `getMediaItemCount()` 和 `getMediaItemAt(int index)`

ExoPlayer 提供了 `getMediaItemCount()` 方法来获取当前播放列表中媒体项的数量，以及 `getMediaItemAt(int index)` 方法来获取指定位置的媒体项。

#### **Java 示例**

```java
// 假设您已经初始化了 ExoPlayer 实例
SimpleExoPlayer player = new SimpleExoPlayer.Builder(context).build();

// 获取播放列表中的媒体项数量
int mediaItemCount = player.getMediaItemCount();
Log.d("Playlist", "播放列表中共有 " + mediaItemCount + " 个媒体项");

// 遍历并获取每个媒体项的详细信息
for (int i = 0; i < mediaItemCount; i++) {
    MediaItem mediaItem = player.getMediaItemAt(i);
    String title = mediaItem.mediaMetadata.title != null ? mediaItem.mediaMetadata.title.toString() : "未知标题";
    String uri = mediaItem.localConfiguration != null ? mediaItem.localConfiguration.uri.toString() : "未知 URI";
    Log.d("Playlist", "媒体项 " + (i + 1) + ": 标题 = " + title + ", URI = " + uri);
}
```

#### **Kotlin 示例**

```kotlin
// 假设您已经初始化了 ExoPlayer 实例
val player: SimpleExoPlayer = SimpleExoPlayer.Builder(context).build()

// 获取播放列表中的媒体项数量
val mediaItemCount = player.mediaItemCount
Log.d("Playlist", "播放列表中共有 $mediaItemCount 个媒体项")

// 遍历并获取每个媒体项的详细信息
for (i in 0 until mediaItemCount) {
    val mediaItem = player.getMediaItemAt(i)
    val title = mediaItem.mediaMetadata.title ?: "未知标题"
    val uri = mediaItem.localConfiguration?.uri ?: "未知 URI"
    Log.d("Playlist", "媒体项 ${i + 1}: 标题 = $title, URI = $uri")
}
```

### 2.2 使用 `getCurrentMediaItem()` 获取当前播放的媒体项

除了获取整个播放列表，您可能还需要获取当前正在播放的媒体项。ExoPlayer 提供了 `getCurrentMediaItem()` 方法来实现这一点。

#### **Java 示例**

```java
MediaItem currentMediaItem = player.getCurrentMediaItem();
if (currentMediaItem != null) {
    String title = currentMediaItem.mediaMetadata.title != null ? currentMediaItem.mediaMetadata.title.toString() : "未知标题";
    String uri = currentMediaItem.localConfiguration != null ? currentMediaItem.localConfiguration.uri.toString() : "未知 URI";
    Log.d("CurrentMediaItem", "当前播放媒体项: 标题 = " + title + ", URI = " + uri);
} else {
    Log.d("CurrentMediaItem", "当前没有正在播放的媒体项");
}
```

#### **Kotlin 示例**

```kotlin
val currentMediaItem = player.currentMediaItem
if (currentMediaItem != null) {
    val title = currentMediaItem.mediaMetadata.title ?: "未知标题"
    val uri = currentMediaItem.localConfiguration?.uri ?: "未知 URI"
    Log.d("CurrentMediaItem", "当前播放媒体项: 标题 = $title, URI = $uri")
} else {
    Log.d("CurrentMediaItem", "当前没有正在播放的媒体项")
}
```

## 3. 完整示例：获取并显示播放列表

以下是一个完整的示例，展示如何在 Android 应用中获取并显示 ExoPlayer 的播放列表。

### **Java 示例**

```java
public class MainActivity extends AppCompatActivity {

    private SimpleExoPlayer player;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

         // 初始化 ExoPlayer
        player = new SimpleExoPlayer.Builder(this).build();

        // 添加媒体项到播放列表
        MediaItem mediaItem1 = MediaItem.fromUri("https://example.com/song1.mp3");
        MediaItem mediaItem2 = MediaItem.fromUri("https://example.com/song2.mp3");
        player.addMediaItem(mediaItem1);
        player.addMediaItem(mediaItem2);

        // 准备并开始播放
        player.prepare();
        player.play();

        // 获取并显示播放列表
        displayPlaylist();
    }

    private void displayPlaylist() {
        int mediaItemCount = player.getMediaItemCount();
        Log.d("Playlist", "播放列表中共有 " + mediaItemCount + " 个媒体项");
        for (int i = 0; i < mediaItemCount; i++) {
            MediaItem mediaItem = player.getMediaItemAt(i);
            String title = mediaItem.mediaMetadata.title != null ? mediaItem.mediaMetadata.title.toString() : "未知标题";
            String uri = mediaItem.localConfiguration != null ? mediaItem.localConfiguration.uri.toString() : "未知 URI";
            Log.d("Playlist", "媒体项 " + (i + 1) + ": 标题 = " + title + ", URI = " + uri);
        }
    }

    @Override
    protected void onDestroy() {
        super.onDestroy();
        // 释放播放器资源
        if (player != null) {
            player.release();
            player = null;
        }
    }
}
```

### **Kotlin 示例**

```kotlin
class MainActivity : AppCompatActivity() {

    private lateinit var player: SimpleExoPlayer

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)
        
        // 初始化 ExoPlayer
        player = SimpleExoPlayer.Builder(this).build()

        // 添加媒体项到播放列表
        val mediaItem1 = MediaItem.fromUri("https://example.com/song1.mp3")
        val mediaItem2 = MediaItem.fromUri("https://example.com/song2.mp3")
        player.addMediaItem(mediaItem1)
        player.addMediaItem(mediaItem2)

        // 准备并开始播放
        player.prepare()
        player.play()

        // 获取并显示播放列表
        displayPlaylist()
    }

    private fun displayPlaylist() {
        val mediaItemCount = player.mediaItemCount
        Log.d("Playlist", "播放列表中共有 $mediaItemCount 个媒体项")
        for (i in 0 until mediaItemCount) {
            val mediaItem = player.getMediaItemAt(i)
            val title = mediaItem.mediaMetadata.title ?: "未知标题"
            val uri = mediaItem.localConfiguration?.uri ?: "未知 URI"
            Log.d("Playlist", "媒体项 ${i + 1}: 标题 = $title, URI = $uri")
        }
    }

    override fun onDestroy() {
        super.onDestroy()
        // 释放播放器资源
        player.release()
    }
}
```

## 4. 处理动态播放列表

如果您的播放列表是动态变化的（例如，用户可以添加或删除媒体项），您可以通过监听播放器的变化来实时获取最新的播放列表。

### **监听播放列表变化**

ExoPlayer 提供了 `Player.Listener` 接口，可以通过实现其中的方法来监听播放列表的变化。

#### **Java 示例**

```java
player.addListener(new Player.Listener() {
    @Override
    public void onMediaItemTransition(MediaItem mediaItem, int reason) {
        // 当媒体项切换时调用
        Log.d("Playlist", "媒体项切换到: " + (mediaItem.mediaMetadata.title != null ? mediaItem.mediaMetadata.title : "未知标题"));
    }

    @Override
    public void onPlaylistChanged() {
        // 播放列表发生变化时调用
        displayPlaylist();
    }
});
```

#### **Kotlin 示例**

```kotlin
player.addListener(object : Player.Listener {
    override fun onMediaItemTransition(mediaItem: MediaItem?, reason: Int) {
        // 当媒体项切换时调用
        mediaItem?.let {
            val title = it.mediaMetadata.title ?: "未知标题"
            Log.d("Playlist", "媒体项切换到: $title")
        }
    }

    override fun onPlaylistChanged(reason: Int) {
        // 播放列表发生变化时调用
        displayPlaylist()
    }
})
```

## 5. 使用 `ConcatenatingMediaSource` 管理播放列表（可选）

虽然现代 ExoPlayer 推荐使用 `MediaItem` 来管理播放列表，但在某些情况下，您可能仍需要使用 `ConcatenatingMediaSource` 来管理更复杂的播放列表。在这种情况下，获取播放列表的方法会有所不同。

### **注意**：

如果您使用 `ConcatenatingMediaSource`，需要手动跟踪播放列表中的媒体项，因为 ExoPlayer 的 `getMediaItemCount()` 和 `getMediaItemAt(int index)` 方法只适用于 `MediaItem` 播放列表。

建议优先使用 `MediaItem` 播放列表管理功能，因为它更简洁且与最新版本的 ExoPlayer 更兼容。

## 6. 总结

通过上述方法，您可以轻松地获取和管理 ExoPlayer 的播放列表。关键步骤包括：

1. 使用 `getMediaItemCount()` 获取播放列表中的媒体项数量。
2. 使用 `getMediaItemAt(int index)` 遍历并获取每个媒体项的详细信息。
3. 使用 `getCurrentMediaItem()` 获取当前正在播放的媒体项。
4. 如果播放列表动态变化，可以通过实现 `Player.Listener` 来监听变化。

确保在适当的生命周期方法中初始化和释放 ExoPlayer，以避免内存泄漏和其他资源管理问题。如果您有更多关于 ExoPlayer 的问题，欢迎继续提问！
