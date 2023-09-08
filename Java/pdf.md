# PDF

1.[word转pdf](https://www.docx4java.org/blog/2020/03/documents4j-for-pdf-output/)

> 成功案例

尝试了很多种转pdf的方法，目前这种较为成功没有乱码，格式正确。

```java
<dependency>
  <groupId>com.documents4j</groupId>
  <artifactId>documents4j-local</artifactId>
  <version>1.1.1</version>
</dependency>

<dependency>
  <groupId>com.documents4j</groupId>
  <artifactId>documents4j-transformer-msoffice-word</artifactId>
  <version>1.1.1</version>
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



