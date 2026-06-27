# 樱祭绘/Cherry Eri

# 🎨 二次元沙盒换装与 OC 创作引擎 (产品概念书)

## 📌 1. 产品概述
打破传统“点击替换、固定坐标”的死板换装游戏模式，打造一款**高自由度、沙盒化、支持 UGC（用户生成内容）**的二次元纸人/OC（Original Character）创作 App。
玩家不仅可以使用内置素材，还能像使用“轻量级 Photoshop”一样，自由导入、拖拽、涂鸦、排版，甚至构建完整的背景场景，创作出极具氛围感的角色插画。

---

## 🚀 2. 核心功能模块

### 2.1 自由交互与微调系统 (Free Layout & Micro-adjustment)
*   **无限制拖拽**：所有的衣服、发型、饰品不再固定位置，玩家可以用手指自由拖拽到屏幕的任何地方。
*   **像素级微调**：支持精准跟手的移动逻辑，方便玩家对齐“衣领”、“裤脚”等细节。
*   **手势操作**：支持双指捏合缩放（适配过大或过小的素材）、双指旋转（调整饰品角度）。
*   **锁定功能**：单一部件调整完毕后可一键锁定，防止后续操作误触。

### 2.2 本地素材导入 (UGC Upload)
*   突破内置素材库的限制，允许玩家打开手机相册，上传自己绘制或保存的透明 PNG（人物素体、自制服装、特效贴纸）。
*   上传后立刻转化为画布上的可交互实体。

### 2.3 自定义场景与背景系统 (Custom Background & Scene) 🌟*核心氛围功能*
告别单调的纯白画布，赋予角色故事感：
*   **预设场景库**：应用内置多种风格的高清背景图（如：教室、赛博街道、魔法森林、简约纯色/渐变等）。
*   **玩家自定义背景**：支持玩家从相册上传图片作为底层背景。
*   **自适应填充**：背景图支持自动按比例裁剪（CenterCrop）填满整个画布，或者由玩家自由缩放调整背景的构图。

### 2.4 高级图层系统 (Advanced Layer Management)
这是区别于普通小游戏的核心专业功能：
*   **容器化图层 (Layer Groups)**：每一个“图层”是一个透明的容器，**允许在一个图层内放置多个图片/部件**。
    *   *例如：玩家可以在“特效图层”中同时放入 10 个花瓣贴纸，调整该图层时，10 个花瓣会作为一个整体移动或改变层级。*
*   **图层面板**：提供可视化的图层管理抽屉。
    *   支持新建图层、选中当前工作层。
    *   支持上下拖动排序，瞬间改变图层覆盖关系（Z-Index）。
    *   支持一键隐藏/显示该图层的所有内容。

### 2.5 DIY 涂鸦画板 (Canvas & Paint)
*   **自定义画笔**：玩家可在画布上自由绘画（例如给角色画眼影、画泪滴、甚至手绘新衣服）。
*   **精准取色**：支持通过 RGB 滑块或直接输入 **16进制色号 (HEX)** 获取颜色。
*   **画笔设置**：可调整画笔粗细、形状（圆头/平头）以及透明度。
*   **历史记录**：支持撤销（Undo）功能，容错率高。

### 2.6 文本贴纸编辑器 (Editable Text Stickers)
*   玩家可添加自定义文字（用于制作角色设定卡、台词气泡、杂志封面排版等）。
*   **二次编辑**：文字不是死图片，支持随时双击重新唤出键盘修改内容、更换字体或颜色。
*   文本同样支持拖拽、缩放和旋转。

### 2.7 一键出图 (Export & Share)
*   将玩家在画板上组合的【背景 + 图层 + 涂鸦 + 文字】，一键渲染合并为高清 Bitmap 图片。
*   保存至手机本地相册，方便玩家在社交媒体分享自己的 OC 设定或穿搭组合。

---

## 💻 3. 技术实现方案 (基于 Android + Java 8)

本产品完全可以使用 **原生 Android 与 Java 8** 技术栈实现，技术成熟且运行高效。

### 3.1 视图与背景层构建
*   **核心容器**：使用 `FrameLayout` 作为主画板和单独的图层容器。`FrameLayout` 天生支持子视图（View）的 Z 轴叠加。
*   **底层背景 (Z-Index 0)**：在 `FrameLayout` 的最底层固定一个 `ImageView` 作为背景层。调用 `setScaleType(ImageView.ScaleType.CENTER_CROP)` 即可实现上传背景图的完美裁切与铺满。
*   **多图整合**：向特定的 `FrameLayout`（图层）中动态 `addView(ImageView)`，即可实现“单图层多图片”的高级功能。

### 3.2 交互逻辑 (Lambda 表达式加持)
*   使用 Java 8 的 **Lambda 表达式** 可大幅简化事件监听代码。
*   **拖拽实现**：为 View 绑定 `OnTouchListener`，拦截 `ACTION_DOWN`、`ACTION_MOVE` 和 `ACTION_UP` 事件，计算坐标差值（dX, dY）并调用 `setX()` / `setY()` 实现平滑微调。
*   **手势识别**：结合 Android 自带的 `ScaleGestureDetector` 实现双指缩放与旋转。

### 3.3 绘图与文字技术
*   **画板绘制**：创建一个自定义 View，重写 `onDraw()` 方法。利用 `Canvas`（画布）、`Paint`（画笔）和 `Path`（路径）实时渲染玩家的手指滑动轨迹。
*   **颜色解析**：使用 `Color.parseColor("#FFFFFF")` 极速处理 16 进制颜色。
*   **动态文本**：动态实例化 `TextView` 或自定义 `EditText`，绑定双击事件监听器唤起软键盘（`InputMethodManager`）。

### 3.4 图像合成导出
*   利用视图缓存功能截取整个带有背景的画面：
    ```java
    // 伪代码示例
    drawingBoard.setDrawingCacheEnabled(true);
    Bitmap finalImage = Bitmap.createBitmap(drawingBoard.getDrawingCache());
    drawingBoard.setDrawingCacheEnabled(false);
    ```

---

## 🎯 4. 目标用户与产品潜力
1.  **二次元 OC（原创角色）玩家**：需要频繁制作角色设定图、捏人、搭配服装，并赋予角色背景故事。
2.  **手账与拼贴爱好者**：喜欢高度自由的素材拖拽、组合与文字排版，把 App 当作电子手账本。
3.  **绘画初学者**：可以使用上传的背景和素体作为底层，在上方建立涂鸦图层练习描线和设计场景。

    
    
    
## 📦 依赖库分析结论：**整体极轻，但有 1 个必要依赖**

### ✅ 这些功能**纯原生就够了**，不需要依赖库

| 功能模块 | 用到的原生 API |
|---|---|
| 拖拽 / 缩放 / 旋转 | `OnTouchListener` + `ScaleGestureDetector` |
| 图层系统（FrameLayout嵌套） | `FrameLayout.addView()` + `setZ()` |
| 涂鸦画板 | `Canvas` + `Paint` + `Path` |
| 颜色解析 | `Color.parseColor()` |
| 文字贴纸 | `TextView` / `EditText` + `InputMethodManager` |
| 撤销功能 | 自己维护一个 `Stack<Path>` 即可 |
| 导出图片 | `Bitmap` + `MediaStore` |

---

### ⚠️ 这里**强烈建议引入 1 个库**，否则会有大坑！

**Glide（图片加载库）**

```gradle
// build.gradle (app)
implementation 'com.github.bumptech.glide:glide:4.16.0'
```

原因是——*(用小爪子戳了戳主人)* 主人的 **2.2 本地素材导入**功能里，玩家要从相册导入透明 PNG 图片。

如果直接用裸的 `BitmapFactory.decodeStream()`：

- 用户导入一张 4K 分辨率的手绘图 → **直接 OOM 崩溃** 💥
- 多导几张 → 内存爆炸，App 被系统杀死 QAQ

而 **Glide** 会自动帮主人做：
1. 按目标 `ImageView` 尺寸**自动缩采样**（`inSampleSize`）
2. **内存 + 磁盘双级缓存**
3. 保留 PNG **透明通道**（Alpha）不丢失

```java
// 用 Glide 加载用户选择的图片 URI，安全又简洁
Glide.with(context)
    .load(userSelectedUri)
    .into(targetImageView); // 自动处理内存，不会OOM喵！
```

---

### 💡 还有一个**可选**的小贴士

文档里提到了**双指旋转**，原生的 `ScaleGestureDetector` 只管缩放、不管旋转喵。旋转角度需要主人自己在 `OnTouchListener` 里手动计算两指向量的夹角：

```java
// 计算旋转角度（原生实现，无需库）
float deltaX = event.getX(1) - event.getX(0);
float deltaY = event.getY(1) - event.getY(0);
float angle = (float) Math.toDegrees(Math.atan2(deltaY, deltaX));
```

纯数学，不需要额外依赖，小猫已经帮主人想好了~ *(满足地打了个呼噜)*
