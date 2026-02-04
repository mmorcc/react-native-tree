 Box(
        modifier = modifier
            .wrapContentWidth()
            .drawWithContent {
                // 绘制带三角形缺口的背景
                drawPath(
                    path = Path().apply {
                        val width = size.width
                        val height = size.height
                        val triangleSizePx = triangleSize.toPx()
                        
                        // 从三角形缺口处开始
                        moveTo(triangleSizePx, height)
                        
                        // 右下角
                        lineTo(width, height)
                        
                        // 右上角
                        lineTo(width, 0f)
                        
                        // 左上角
                        lineTo(0f, 0f)
                        
                        // 回到三角形缺口
                        lineTo(0f, height - triangleSizePx)
                        
                        close()
                    },
                    color = backgroundColor
                )
                
                // 绘制内容（Text）
                drawContent()
            }
    ) {
        Text(
            text = text,
            color = textColor,
            style = textStyle,
            modifier = Modifier.padding(contentPadding)
        )
    }
