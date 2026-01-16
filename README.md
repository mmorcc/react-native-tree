import androidx.compose.foundation.border
import androidx.compose.foundation.layout.Arrangement
import androidx.compose.foundation.layout.Box
import androidx.compose.foundation.layout.Column
import androidx.compose.foundation.layout.Row
import androidx.compose.foundation.layout.fillMaxWidth
import androidx.compose.foundation.layout.padding
import androidx.compose.foundation.layout.size
import androidx.compose.foundation.lazy.LazyColumn
import androidx.compose.foundation.lazy.items
import androidx.compose.foundation.shape.RoundedCornerShape
import androidx.compose.material.icons.Icons
import androidx.compose.material.icons.filled.Check
import androidx.compose.material3.Button
import androidx.compose.material3.Icon
import androidx.compose.material3.MaterialTheme
import androidx.compose.material3.Tab
import androidx.compose.material3.Text
import androidx.compose.runtime.Composable
import androidx.compose.runtime.getValue
import androidx.compose.runtime.mutableStateOf
import androidx.compose.runtime.remember
import androidx.compose.runtime.setValue
import androidx.compose.ui.Alignment
import androidx.compose.ui.Modifier
import androidx.compose.ui.graphics.Color
import androidx.compose.ui.semantics.Role
import androidx.compose.ui.semantics.role
import androidx.compose.ui.semantics.semantics
import androidx.compose.ui.unit.dp
import androidx.compose.ui.unit.sp

// 表格行数据模型
data class TableRowData(
    val header: String, // 左侧表头文本
    val content: @Composable () -> Unit // 右侧自定义内容
)

// Tab 数据模型
data class TabItem(
    val tabTitle: String, // Tab 标题
    val tableData: List<TableRowData> // 对应表格数据
)

// 浅灰边框颜色常量
private val LIGHT_GRAY_BORDER = Color(0xFFE0E0E0)

/**
 * 带 Wrap 布局 Tab 的表格组件
 * @param tabItems Tab 列表数据
 */
@Composable
fun WrapTabTable(
    tabItems: List<TabItem>
) {
    // 当前选中的 Tab 索引
    var selectedTabIndex by remember { mutableStateOf(0) }
    val currentTableData = tabItems[selectedTabIndex].tableData

    Column(
        modifier = Modifier
            .fillMaxWidth()
            .padding(16.dp)
            .semantics { role = Role.Group }
    ) {
        // ========== 横向 Wrap 布局 Tab 栏（带圆角浅灰边框） ==========
        Row(
            modifier = Modifier.fillMaxWidth(),
            horizontalArrangement = Arrangement.spacedBy(8.dp)
        ) {
            tabItems.forEachIndexed { index, item ->
                val isSelected = selectedTabIndex == index
                Tab(
                    selected = isSelected,
                    onClick = { selectedTabIndex = index },
                    modifier = Modifier
                        .border(
                            width = 1.dp,
                            color = LIGHT_GRAY_BORDER,
                            shape = RoundedCornerShape(8.dp)
                        )
                        .background(
                            color = if (isSelected) MaterialTheme.colorScheme.primaryContainer
                            else MaterialTheme.colorScheme.surface,
                            shape = RoundedCornerShape(8.dp)
                        )
                        .semantics { role = Role.Tab }
                ) {
                    Text(
                        text = item.tabTitle,
                        modifier = Modifier.padding(horizontal = 16.dp, vertical = 8.dp),
                        color = if (isSelected) MaterialTheme.colorScheme.onPrimaryContainer
                        else MaterialTheme.colorScheme.onSurface
                    )
                }
            }
        }

        // ========== 表格区域（带浅灰边框，无圆角） ==========
        LazyColumn(
            modifier = Modifier
                .fillMaxWidth()
                .padding(top = 16.dp)
                .border(1.dp, LIGHT_GRAY_BORDER) // 表格边框，无圆角
                .background(MaterialTheme.colorScheme.surface),
            verticalArrangement = Arrangement.spacedBy(1.dp) // 行分隔线
        ) {
            items(currentTableData) { rowData ->
                TableRow(
                    header = rowData.header,
                    content = rowData.content
                )
            }
        }
    }
}

/**
 * 表格行组件（左侧灰色表头 + 右侧内容）
 */
@Composable
private fun TableRow(
    header: String,
    content: @Composable () -> Unit
) {
    Row(
        modifier = Modifier
            .fillMaxWidth()
            .background(MaterialTheme.colorScheme.surface)
            .semantics { role = Role.Row },
        verticalAlignment = Alignment.CenterVertically
    ) {
        // 左侧表头（灰色背景）
        Box(
            modifier = Modifier
                .size(width = 120.dp, height = 56.dp)
                .background(Color(0xFFF5F5F5))
                .padding(8.dp),
            contentAlignment = Alignment.CenterStart
        ) {
            Text(
                text = header,
                fontSize = 14.sp,
                color = Color(0xFF666666),
                modifier = Modifier.semantics { role = Role.Header }
            )
        }

        // 右侧内容区域
        Box(
            modifier = Modifier
                .fillMaxWidth()
                .height(56.dp)
                .padding(horizontal = 16.dp),
            contentAlignment = Alignment.CenterStart
        ) {
            content()
        }
    }
}

// ========== 使用示例 ==========
@Composable
fun WrapTabTableDemo() {
    val tabItems = listOf(
        TabItem(
            tabTitle = "基本信息",
            tableData = listOf(
                TableRowData("姓名") { Text("张三") },
                TableRowData("年龄") { Text("25") },
                TableRowData("性别") { Text("男") },
                TableRowData("状态") {
                    Row(verticalAlignment = Alignment.CenterVertically) {
                        Icon(
                            imageVector = Icons.Default.Check,
                            contentDescription = "已验证",
                            modifier = Modifier.size(16.dp),
                            tint = Color.Green
                        )
                        Text(text = "已验证", modifier = Modifier.padding(start = 4.dp))
                    }
                }
            )
        ),
        TabItem(
            tabTitle = "联系方式",
            tableData = listOf(
                TableRowData("手机号") { Text("138****1234") },
                TableRowData("邮箱") { Text("zhangsan@xxx.com") },
                TableRowData("地址") { Text("北京市朝阳区") },
                TableRowData("操作") {
                    Button(onClick = { /* 点击逻辑 */ }) {
                        Text("编辑")
                    }
                }
            )
        ),
        TabItem(
            tabTitle = "更多设置",
            tableData = listOf(
                TableRowData("通知权限") { Text("已开启") },
                TableRowData("缓存大小") { Text("2.5GB") },
                TableRowData("版本号") { Text("v1.0.0") }
            )
        )
    )

    MaterialTheme {
        WrapTabTable(tabItems = tabItems)
    }
}

// ========== 预览 ==========
import androidx.compose.ui.tooling.preview.Preview

@Preview(showBackground = true, widthDp = 360)
@Composable
fun WrapTabTablePreview() {
    WrapTabTableDemo()
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
