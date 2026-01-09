1️⃣ Theme（如果你项目已经是 M3，可直接跳过）
@Composable
fun AppTheme(
    darkTheme: Boolean = isSystemInDarkTheme(),
    content: @Composable () -> Unit
) {
    val colorScheme = if (darkTheme) {
        darkColorScheme()
    } else {
        lightColorScheme()
    }

    MaterialTheme(
        colorScheme = colorScheme,
        typography = Typography(),
        content = content
    )
}

二、可复用组件设计（拆 3 个组件）

我们拆成：

ExpandableSection —— 可折叠整体

BulletText —— 圆点 + 无障碍

InfoDetailScreen —— 页面组合

2️⃣ BulletText（圆点 + 无障碍，可复用）
@Composable
fun BulletText(
    text: String,
    modifier: Modifier = Modifier
) {
    Row(
        modifier = modifier
            .fillMaxWidth()
            .padding(vertical = 4.dp)
            .semantics {
                contentDescription = "Bullet item: $text"
            },
        verticalAlignment = Alignment.Top
    ) {
        Text(
            text = "•",
            style = MaterialTheme.typography.bodyLarge,
            modifier = Modifier.padding(end = 8.dp)
        )
        Text(
            text = text,
            style = MaterialTheme.typography.bodyMedium,
            color = MaterialTheme.colorScheme.onSurface
        )
    }
}

3️⃣ ExpandableSection（核心可复用折叠组件）
@Composable
fun ExpandableSection(
    icon: ImageVector,
    title: String,
    detail: String,
    bullets: List<String>,
    modifier: Modifier = Modifier
) {
    var expanded by rememberSaveable { mutableStateOf(false) }

    Column(
        modifier = modifier
            .fillMaxWidth()
            .animateContentSize()
    ) {

        Row(
            modifier = Modifier
                .fillMaxWidth()
                .clickable { expanded = !expanded }
                .padding(vertical = 12.dp)
                .semantics {
                    contentDescription =
                        if (expanded) "Collapse section $title"
                        else "Expand section $title"
                },
            verticalAlignment = Alignment.CenterVertically
        ) {

            Icon(
                imageVector = icon,
                contentDescription = null,
                tint = MaterialTheme.colorScheme.primary
            )

            Spacer(modifier = Modifier.width(12.dp))

            Column(modifier = Modifier.weight(1f)) {
                Text(
                    text = title,
                    style = MaterialTheme.typography.titleMedium
                )
                Text(
                    text = detail,
                    style = MaterialTheme.typography.bodyMedium,
                    color = MaterialTheme.colorScheme.onSurfaceVariant
                )
            }

            Icon(
                imageVector = if (expanded)
                    Icons.Default.KeyboardArrowUp
                else
                    Icons.Default.KeyboardArrowDown,
                contentDescription = null
            )
        }

        if (expanded) {
            Column(modifier = Modifier.padding(start = 8.dp)) {
                bullets.forEach {
                    BulletText(text = it)
                }
            }
        }
    }
}


✔ rememberSaveable（旋转不丢状态）
✔ animateContentSize()（平滑动画）
✔ 完整无障碍描述

三、完整页面（Material 3 + 滚动 + 底部按钮）
@OptIn(ExperimentalMaterial3Api::class)
@Composable
fun InfoDetailScreen(
    onBack: () -> Unit,
    onLearnMore: () -> Unit
) {
    Scaffold(
        topBar = {
            CenterAlignedTopAppBar(
                title = { Text("Info Page") },
                navigationIcon = {
                    IconButton(onClick = onBack) {
                        Icon(
                            Icons.Default.ArrowBack,
                            contentDescription = "Navigate back"
                        )
                    }
                }
            )
        },
        bottomBar = {
            Button(
                onClick = onLearnMore,
                modifier = Modifier
                    .fillMaxWidth()
                    .padding(16.dp),
                shape = MaterialTheme.shapes.medium
            ) {
                Text("Learn More")
            }
        }
    ) { padding ->

        Column(
            modifier = Modifier
                .padding(padding)
                .verticalScroll(rememberScrollState())
                .padding(16.dp)
        ) {

            Image(
                painter = painterResource(R.drawable.ic_launcher_foreground),
                contentDescription = "Header image",
                modifier = Modifier
                    .fillMaxWidth()
                    .height(180.dp),
                contentScale = ContentScale.Crop
            )

            Spacer(modifier = Modifier.height(16.dp))

            Text(
                text = "Main Title",
                style = MaterialTheme.typography.headlineSmall
            )

            Spacer(modifier = Modifier.height(16.dp))

            ExpandableSection(
                icon = Icons.Default.Info,
                title = "Feature Title",
                detail = "Tap to see details",
                bullets = listOf(
                    "First bullet description",
                    "Second bullet description",
                    "Third bullet description",
                    "Fourth bullet description"
                )
            )

            Spacer(modifier = Modifier.height(80.dp))
        }
    }
}


✔ 中间可滚动
✔ 底部按钮固定
✔ 暗色模式自动适配

四、无障碍测试示例（Compose Test）

测试点：

折叠前不可见

点击后可见

TalkBack 描述存在

4️⃣ 测试代码
@get:Rule
val composeTestRule = createComposeRule()

@Test
fun expandableSection_accessibility_test() {
    composeTestRule.setContent {
        AppTheme {
            ExpandableSection(
                icon = Icons.Default.Info,
                title = "Accessibility Test",
                detail = "Detail",
                bullets = listOf("Bullet One", "Bullet Two")
            )
        }
    }

    // 初始状态：不可见
    composeTestRule
        .onNodeWithText("Bullet One")
        .assertDoesNotExist()

    // 点击展开
    composeTestRule
        .onNodeWithContentDescription("Expand section Accessibility Test")
        .performClick()

    // 展开后可见
    composeTestRule
        .onNodeWithContentDescription("Bullet item: Bullet One")
        .assertIsDisplayed()

    composeTestRule
        .onNodeWithContentDescription("Bullet item: Bullet Two")
        .assertIsDisplayed()
}

---
category: Components
type: Data Display
title: Tree TreeSelect
subtitle: 树形控件
---

## 使用
- 使用本组件时要同时添加图标库并链接
- 先执行
- npm install react-native-vector-icons --save
- npm install react-native-tree --save
- react-native link
- 然后
- react-native run-android
- 或 react-native run-ios

```jsx
<Tree treeData={[]}/>
<TreeSelect treeData={[]} defaultValue={[]}/>
```

## 运行示例
- git clone https://github.com/AdamRobertHall/react-native-tree.git

- 进入工程目录
- cd Example
- npm install
- react-native link
- react-native run-android
- 或 react-native run-ios
