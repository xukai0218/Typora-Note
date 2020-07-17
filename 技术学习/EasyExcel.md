

```
<!--alibaba easyexcel-->
<dependency>
  <groupId>com.alibaba</groupId>
  <artifactId>easyexcel</artifactId>
  <version>1.1.2-beta5</version>
</dependency>
```

```
@Test
public void writeExcel1() throws Exception {
  // 文件输出位置
  OutputStream out = new FileOutputStream("/Users/a123123/Work/tmp_files/test.xlsx");

  ExcelWriter writer = EasyExcelFactory.getWriter(out);

  // 写仅有一个 Sheet 的 Excel 文件, 此场景较为通用
  Sheet sheet1 = new Sheet(1, 0, WriteModel.class);

  // 第一个 sheet 名称
  sheet1.setSheetName("第一个sheet");

  // 写数据到 Writer 上下文中
  // 入参1: 创建要写入的模型数据
  // 入参2: 要写入的目标 sheet
  writer.write(createModelList(), sheet1);

  // 将上下文中的最终 outputStream 写入到指定文件中
  writer.finish();

  // 关闭流
  out.close();
}
```



上面这段示例代码：

- **①**：WriteModel 这个对象就是要写入 Excel 的数据模型对象，**等等，你这好像不行吧？表头 head，以及每个单元格内的数据顺序都没指定，能达到想要的效果么？别急，后面会讨论这块！
- **②**：创建**需要写入的数据集**，当然了，正常业务中，这块都是从数据库中查询出来的。



```
@Data
@NoArgsConstructor
@AllArgsConstructor
@Builder
public class WriteModel extends BaseRowModel {

    @ExcelProperty(value = "姓名", index = 0)
    private String name;

    @ExcelProperty(value = "密码", index = 1)
    private String password;

    @ExcelProperty(value = "年龄", index = 2)
    private Integer age;
}
```

xayExcel 提供注解的方式, 来方便的定义 Excel 需要的数据模型：

- **①**：首先，定义的写入模型必须要继承自 `BaseRowModel.java`;
- **②**：通过 `@ExcelProperty` 注解来指定每个字段的**列名称**，以及**下标位置**；



















