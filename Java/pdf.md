# PDF

1.[word转pdf](https://www.docx4java.org/blog/2020/03/documents4j-for-pdf-output/)

> 成功案例

尝试了很多种转pdf的方法，目前这种较为成功没有乱码，格式正确。

```java
<dependency>
  <groupId>com.documents4j</groupId>
  <artifactId>documents4j-local</artifactId>
  <version>1.1.12</version>
</dependency>

<dependency>
  <groupId>com.documents4j</groupId>
  <artifactId>documents4j-transformer-msoffice-word</artifactId>
  <version>1.1.12</version>
</dependency>

<dependency>
  <groupId>org.slf4j</groupId>
  <artifactId>slf4j-simple</artifactId>
</dependency>


public static void pdfCon(String docxFilePath, String pdfFilePath) throws FileNotFoundException {
  FileInputStream wordFile = new FileInputStream(docxFilePath);
  FileOutputStream target = new FileOutputStream(pdfFilePath);
  IConverter converter = LocalConverter.builder()
    .baseFolder(new File("C:\\temp"))
    .workerPool(20, 25, 2, TimeUnit.SECONDS)
    .processTimeout(30, TimeUnit.SECONDS)
    .build();

  Future<Boolean> conversion = converter
    .convert(wordFile).as(DocumentType.MS_WORD)
    .to(target).as(DocumentType.PDF)
    .prioritizeWith(1000) // optional
    .schedule();
}
```



> 瑕疵案例1（丢内容）

```java
<dependency>
  <groupId>fr.opensagres.xdocreport</groupId>
  <artifactId>fr.opensagres.poi.xwpf.converter.pdf-gae</artifactId>
  <version>2.0.2</version>
</dependency>

public static void poi(String docxFilePath, String pdfFilePath) throws IOException {
        FileInputStream fileInputStream = new FileInputStream(docxFilePath);
        XWPFDocument xwpfDocument = new XWPFDocument(fileInputStream);
        PdfOptions pdfOptions = PdfOptions.create();
        FileOutputStream fileOutputStream = new FileOutputStream(pdfFilePath);
        PdfConverter.getInstance().convert(xwpfDocument,fileOutputStream,pdfOptions);
        fileInputStream.close();
        fileOutputStream.close();
    }
 
```

> 瑕疵案例2（模板空格换行，问题较小）

```java
<dependency>
  <groupId>cn.hutool</groupId>
  <artifactId>hutool-all</artifactId>
  <version>5.7.5</version>
</dependency>

<dependency>
  <groupId>com.aspose</groupId>
  <artifactId>aspose-words</artifactId>
  <version>21.1</version>
  <classifier>jdk17</classifier>
</dependency>

public static void aspose(String docxFilePath, String pdfFilePath) throws IOException {
    InputStream replaceStream = new FileInputStream(docxFilePath);
    File tempFile = File.createTempFile("template",".tmp");
    //转pdf
    boolean toPdfFlag = convertStreamToPdf(replaceStream,tempFile.getPath());
    FileUtil.writeFromStream(FileUtil.getInputStream(tempFile), new File(pdfFilePath), false);
}
public static boolean convertStreamToPdf(InputStream inputStream,String pdfPath) {
    FileOutputStream pdfOut = null;
    try {
        Document doc = new Document(inputStream);
        //判断是否目录是否存在
        FileUtil.mkParentDirs(pdfPath);
        //生成流
        pdfOut = new FileOutputStream(pdfPath);
        doc.acceptAllRevisions();
        doc.save(pdfOut, 40);
    }catch (Exception e){
        e.printStackTrace();
        return false;
    }finally {
        IoUtil.close(pdfOut);
        IoUtil.close(inputStream);
    }
    return true;
}
```

