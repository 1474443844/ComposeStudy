是的，您可以获取 `Box`（或其他可组合项）的高度，并将其设置为 `BottomSheetScaffold` 的 `sheetPeekHeight`。这需要在 Jetpack Compose 中测量可组合项的大小，然后将其用于配置 `sheetPeekHeight`。

以下是详细的步骤和代码示例：

---

### **步骤 1：创建一个用于存储高度的可变状态**

首先，您需要创建一个 `MutableState` 来存储测量得到的高度。这可以使用 `remember { mutableStateOf(...) }` 来实现。

```kotlin
import androidx.compose.runtime.mutableStateOf
import androidx.compose.runtime.remember

@Composable
fun MyApp() {
    var sheetHeightDp by remember { mutableStateOf(0.dp) }
    // 继续下一步
}
```

### **步骤 2：在 `Box` 中测量高度**

使用 `Modifier.onGloballyPositioned` 来获取 `Box` 的布局信息，包括其尺寸。然后，将测量得到的高度从像素转换为 Dp。

```kotlin
import androidx.compose.ui.layout.onGloballyPositioned
import androidx.compose.ui.platform.LocalDensity
import androidx.compose.ui.unit.toSize

@Composable
fun MyApp() {
    var sheetHeightDp by remember { mutableStateOf(0.dp) }
    val density = LocalDensity.current

    BottomSheetScaffold(
        sheetContent = {
            Box(
                modifier = Modifier
                    .fillMaxWidth()
                    .onGloballyPositioned { layoutCoordinates ->
                        val heightPx = layoutCoordinates.size.height.toFloat()
                        // 将像素转换为 Dp
                        sheetHeightDp = with(density) { heightPx.toDp() }
                    }
            ) {
                // 您的 Box 内容
                Text("这是一个测试内容")
            }
        },
        sheetPeekHeight = sheetHeightDp
    ) {
        // 主内容
    }
}
```

### **步骤 3：处理初始状态和避免无限重组**

由于在首次组合时，`sheetHeightDp` 可能为 `0.dp`，这可能导致 `BottomSheetScaffold` 的 `sheetPeekHeight` 为零。为了避免这个问题，您可以提供一个默认值，或者在高度未测量完成之前，避免渲染依赖该高度的组件。

**方法一：提供默认值**

```kotlin
var sheetHeightDp by remember { mutableStateOf(56.dp) } // 默认值为 56.dp
```

**方法二：使用条件渲染**

```kotlin
if (sheetHeightDp > 0.dp) {
    BottomSheetScaffold(
        sheetContent = { /* ... */ },
        sheetPeekHeight = sheetHeightDp
    ) {
        // 主内容
    }
}
```

### **完整示例**

下面是完整的代码示例，结合了上述所有步骤：

```kotlin
import androidx.activity.ComponentActivity
import androidx.compose.foundation.layout.*
import androidx.compose.material3.*
import androidx.compose.runtime.*
import androidx.compose.ui.Modifier
import androidx.compose.ui.layout.onGloballyPositioned
import androidx.compose.ui.platform.LocalDensity
import androidx.compose.ui.unit.dp

@Composable
fun MyApp() {
    var sheetHeightDp by remember { mutableStateOf(0.dp) }
    val density = LocalDensity.current

    BottomSheetScaffold(
        sheetContent = {
            Box(
                modifier = Modifier
                    .fillMaxWidth()
                    .onGloballyPositioned { layoutCoordinates ->
                        val heightPx = layoutCoordinates.size.height.toFloat()
                        sheetHeightDp = with(density) { heightPx.toDp() }
                    }
            ) {
                // 您的 Box 内容
                Text("这是一个测试内容", modifier = Modifier.padding(16.dp))
            }
        },
        sheetPeekHeight = if (sheetHeightDp > 0.dp) sheetHeightDp else 56.dp // 提供默认值
    ) {
        // 主内容
        Text("主屏幕内容", modifier = Modifier.padding(16.dp))
    }
}
```

### **注意事项**

1. **处理初始高度为零的情况：** 在组件初次加载时，`onGloballyPositioned` 还未调用，`sheetHeightDp` 可能为 `0.dp`。为了避免 `sheetPeekHeight` 为零，您可以提供一个合理的默认值（如 `56.dp`），或者在高度为零时延迟渲染 `BottomSheetScaffold`。

2. **避免无限重组：** 如果在 `onGloballyPositioned` 中直接更新状态，可能会导致无限重组。为了避免这种情况，确保状态只在需要时更新。例如，可以在比较新旧高度值，如果高度发生变化才更新状态。

   ```kotlin
   onGloballyPositioned { layoutCoordinates ->
       val heightPx = layoutCoordinates.size.height.toFloat()
       val newHeightDp = with(density) { heightPx.toDp() }
       if (sheetHeightDp != newHeightDp) {
           sheetHeightDp = newHeightDp
       }
   }
   ```

3. **确保 `sheetContent` 的布局稳定：** 如果 `sheetContent` 的内容会动态变化，导致高度不断变化，可能会影响用户体验。建议在可能的情况下，给 `sheetContent` 设置固定的高度或限制其最大高度。

### **总结**

通过使用 `onGloballyPositioned`，您可以获取 `Box`（或其他可组合项）的高度，并将其转换为 Dp，随后用于设置 `BottomSheetScaffold` 的 `sheetPeekHeight`。请注意处理初始状态和避免无限重组，以确保您的应用性能和用户体验。

---

如果您对上述步骤有任何疑问，或者需要进一步的帮助，请随时提问！
