## 访问者模式 ##
- 简介
    1. 函数与函数参数的组合关系
    2. 具体的对象继承或实现函数，【操作逻辑】继承或实现函数参数，通过传参实现灵活组合/搭配

- 代码示例
    ```java
    public abstract class ResourceFile {
        protected String filePath;
        public ResourceFile(String filePath) {
            this.filePath = filePath;
        }
        abstract public void accept(Visitor vistor);
    }

    public class PdfFile extends ResourceFile {
        public PdfFile(String filePath) {
            super(filePath);
        }

        @Override
        public void accept(Visitor visitor) {
            visitor.visit(this);
        }

        //...
    }
    //...PPTFile、WordFile跟PdfFile类似，这里就省略了...

    public interface Visitor {
        void visit(PdfFile pdfFile);
        void visit(PPTFile pdfFile);
        void visit(WordFile pdfFile);
    }

    public class Extractor implements Visitor {
        @Override
        public void visit(PPTFile pptFile) {
            //...
            System.out.println("Extract PPT.");
        }

        @Override
        public void visit(PdfFile pdfFile) {
            //...
            System.out.println("Extract PDF.");
        }

        @Override
        public void visit(WordFile wordFile) {
            //...
            System.out.println("Extract WORD.");
        }
    }

    public class Compressor implements Visitor {
        @Override
        public void visit(PPTFile pptFile) {
            //...
            System.out.println("Compress PPT.");
        }

        @Override
        public void visit(PdfFile pdfFile) {
            //...
            System.out.println("Compress PDF.");
        }

        @Override
        public void visit(WordFile wordFile) {
            //...
            System.out.println("Compress WORD.");
        }

    }

    public class ToolApplication {
        public static void main(String[] args) {
            Extractor extractor = new Extractor();
            List<ResourceFile> resourceFiles = listAllResourceFiles(args[0]);
            for (ResourceFile resourceFile : resourceFiles) {
                resourceFile.accept(extractor);
            }

            Compressor compressor = new Compressor();
            for(ResourceFile resourceFile : resourceFiles) {
                resourceFile.accept(compressor);
            }
        }

        private static List<ResourceFile> listAllResourceFiles(String resourceDirectory) {
            List<ResourceFile> resourceFiles = new ArrayList<>();
            //...根据后缀(pdf/ppt/word)由工厂方法创建不同的类对象(PdfFile/PPTFile/WordFile)
            resourceFiles.add(new PdfFile("a.pdf"));
            resourceFiles.add(new WordFile("b.word"));
            resourceFiles.add(new PPTFile("c.ppt"));
            return resourceFiles;
        }
    }
    ```