Poi

### 遇到的问题

1. 需求：往已经添加过样式的单元格再次添加样式。

   由于单元格已经添加过CellStyle了，则不能用workbook.createCellStyle()重新创建CellStyle并赋值，如果这样操作为导致前面的CellStyle失效，所以只能获取原来的样式getCellStyle然后再次设置对应的效果，但是此处有一个问题就是如果某个单元格没有设置过CellStyle，那么按理来说我们需要先创建样式，再获取样式。那么此处就需要判断它是否之前添加过样式。常规逻辑就是判断是否为Null，如下

   ```java
   private CellStyle getCellStyle(Workbook workbook,Cell cell){
       if(cell.getCellStyle() == null)// always false
           return workbook.createCellStyle();
       return cell.getCellStyle();
   }
   ```

   但是通过反复测试发现即使有单元格没有设置过CellStyle,它也不为空，这就导致了我无法判断它之前是否设置过CellStyle。而通过测试发现若之前没有创建过CellStyle，直接获取它并使用的话无效，且直接创建新的CellStyle又会导致之前设置的CellStyle失效（之前设置过的话）。那么还是只能通过判断其是否为第一次设置CellStyle来判断是获取CellStyle还是创建新的CellStyle来设置样式。

   该如何判断呢？首先想到的就是去网上搜下，结果找了半天没找到，于是就去翻源码了，通过翻源码可以猜测：cell style 是从事先准备好的集合里拿出来的，如果单元格之前没有设置过cell style的话就直接获取0索引的cell style

   ```java
   /**
     * Return the cell's style.
     * //永远非空，默认获取0索引的cell style
     * @return the cell's style. Always not-null. Default cell style has zero index and can be obtained as
     * <code>workbook.getCellStyleAt(0)</code>
     * @see Workbook#getCellStyleAt(int)
     */
   CellStyle getCellStyle();
   ```

   ```java
    /** 
      * //通过索引获取cell style
      * Get the cell style object at the given index
      * //样式集合中的索引(基于0) 
      * @param idx  index within the set of styles (0-based)
      * @return CellStyle object at the index
      */
   CellStyle getCellStyleAt(int idx);
   ```

   于是就有了如下方式判断cellStyle之前是否设置：(结果还是没有效果)

   ```java
   private CellStyle getCellStyle(Workbook workbook,Cell cell){
       if(cell.getCellStyle() == workbook.getCellStyleAt(0))
           return workbook.createCellStyle();
       return cell.getCellStyle();
   }
   ```

   