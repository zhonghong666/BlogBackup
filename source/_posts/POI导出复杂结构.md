---
title: "POI导出复杂结构"
date: 2019-12-8 17:30:58
comments: true
cover: true
categories: Java
tags:
	- Java
---

## POI介绍

​     Apache POI是Apache软件基金会的开源项目，POI提供API给Java程序对Microsoft Office格式档案读和写的功能 。

## 添加maven依赖

```xml
<dependency>
    <groupId>org.apache.poi</groupId>
    <artifactId>poi</artifactId>
    <version>4.1.1</version>
</dependency>
```

## Excel导出操作

 ### **创建Workbook和Sheet**

```java
public void excelExport(HttpServletRequest request, HttpServletResponse response) {
    // 生成工作簿
    HSSFWorkbook workbook = new HSSFWorkbook();
    // 创建sheet页
    HSSFSheet sheet = workbook.createSheet();
    ...
    ...
    // 生成excel文件流
    response.setContentType("application/binary;charset=UTF-8");
    response.setHeader("Content-Disposition", "attachment;fileName=" + URLEncoder.encode("账务明细导出" + ".xls", "UTF-8"));
    workbook.write(response.getOutputStream());
}
```

<!-- more -->

### **创建单元格**

```java
// 标题行
HSSFRow titleRow = sheet.createRow(0);// 创建行,从0开始
HSSFCell cell = titleRow.createCell(0);// 创建行的单元格,也是从0开始
titleRow.createCell(0).setCellValue("账务表");
titleRow.createCell(1).setCellValue("汇总编码");
titleRow.createCell(2).setCellValue("汇总编码生成状态");
```

### **设置单元格样式**

```java
HSSFCellStyle style = workbook.createCellStyle();
// 水平对齐方式
style.setAlignment(HorizontalAlignment.CENTER); //水平居中
style.setVerticalAlignment(VerticalAlignment.CENTER); //垂直居中
style.setWrapText(true);//自动换行
style.setIndention((short)5);//缩进
style.setRotation((short)60);//文本旋转，这里的取值是从-90到90，而不是0-180度

cell.setCellStyle(style);
```

#### 设置边框

​    边框和其他单元格设置一样也是调用CellStyle接口，CellStyle有2种和边框相关的属性，分别是:

| 边框相关属性      | 说明     | 范例                             |
| ----------------- | -------- | -------------------------------- |
| Border+ 方向      | 边框类型 | BorderLeft, BorderRight          |
| 方向 +BorderColor | 边框颜色 | TopBorderColor,BottomBorderColor |

```java
//设置边框
style.setBorderTop(HSSFCellStyle.BORDER_DOTTED);//上边框
style.setBorderBottom(HSSFCellStyle.BORDER_THICK);//下边框
style.setBorderLeft(HSSFCellStyle.BORDER_DOUBLE);//左边框
style.setBorderRight(HSSFCellStyle.BORDER_SLANTED_DASH_DOT);//右边框
style.setTopBorderColor(HSSFColor.RED.index);//上边框颜色
style.setBottomBorderColor(HSSFColor.BLUE.index);//下边框颜色
style.setLeftBorderColor(HSSFColor.GREEN.index);//左边框颜色
style.setRightBorderColor(HSSFColor.PINK.index);//右边框颜色
cell.setCellStyle(style);
```

#### 设置字体

```java
// 设置字体
Font titleFont = workbook.createFont();
titleFont.setFontName("Calibra"); //设置字体名称
titleFont.setFontHeightInPoints((short) 10); //设置字号
font.setColor(HSSFColor.RED.index); //设置字体颜色
font.setUnderline(FontFormatting.U_SINGLE); //设置下划线
font.setTypeOffset(FontFormatting.SS_SUPER); //设置上标下标
font.setStrikeout(true); //设置删除线
titleFont.setBold(true); //是否加粗
titleStyle.setFont(titleFont);
```

#### 设置背景和纹理

```java
style.setFillForegroundColor(IndexedColors.GREY_25_PERCENT.getIndex());//设置图案颜色
style.setFillBackgroundColor(HSSFColor.RED.index);//设置图案背景色
style.setFillPattern(FillPatternType.SOLID_FOREGROUND);//设置图案样式
cell.setCellStyle(style);
```

#### **设置宽度和高度**

```java
sheet.setColumnWidth(1, 31 * 256);//设置第一列的宽度是31个字符宽度
titleRow.setHeightInPoints(30); //设置行的高度
```

### **合并单元格**

```java
//合并列
HSSFCell cell=row.createCell(0);
cell.setCellValue("合并列");
CellRangeAddress region=new CellRangeAddress(0, 0, 0, 5);
sheet.addMergedRegion(region);
//合并行
cell=row.createCell(6);
cell.setCellValue("合并行");
region=new CellRangeAddress(0, 5, 6, 6);
sheet.addMergedRegion(region);
```

`CellRangeAddress`对象其实就是表示一个区域，其构造方法如下：`CellRangeAddress(firstRow, lastRow, firstCol, lastCol)`，参数的说明:

- `firstRow`： 区域中第一个单元格的行号
- `lastRow`：区域中最后一个单元格的行号
- `firstCol`： 区域中第一个单元格的列号
- `lastCol`： 区域中最后一个单元格的列号

### **设置数据格式**

```java
//设置日期格式  --使用Excel内嵌的格式
HSSFCell cell=row.createCell(0);
cell.setCellValue(new Date());
HSSFCellStyle style=workbook.createCellStyle();
style.setDataFormat(HSSFDataFormat.getBuiltinFormat("m/d/yy h:mm"));
cell.setCellStyle(style);

//设置保留2位小数  --使用Excel内嵌的格式
cell=row.createCell(1);
cell.setCellValue(12.3456789);
style=workbook.createCellStyle();
style.setDataFormat(HSSFDataFormat.getBuiltinFormat("0.00"));
cell.setCellStyle(style);

//设置货币格式  --使用自定义的格式
cell=row.createCell(2);
cell.setCellValue(12345.6789);
style=workbook.createCellStyle();
style.setDataFormat(workbook.createDataFormat().getFormat("￥#,##0"));
cell.setCellStyle(style);

//设置百分比格式  --使用自定义的格式
cell=row.createCell(3);
cell.setCellValue(0.123456789);
style=workbook.createCellStyle();
style.setDataFormat(workbook.createDataFormat().getFormat("0.00%"));
cell.setCellStyle(style);

//设置中文大写格式  --使用自定义的格式
cell=row.createCell(4);
cell.setCellValue(12345);
style=workbook.createCellStyle();
style.setDataFormat(workbook.createDataFormat().getFormat("[DbNum2][$-804]0"));
cell.setCellStyle(style);

//设置科学计数法格式  --使用自定义的格式
cell=row.createCell(5);
cell.setCellValue(12345);
style=workbook.createCellStyle();
style.setDataFormat(workbook.createDataFormat().getFormat("0.00E+00"));
cell.setCellStyle(style);
```

## 总结

​    对于一些比较复杂的排版样式的excel导出，其实就是对单个单元格的排版布局，通过设置单元格的位置、合并单元格来实现。