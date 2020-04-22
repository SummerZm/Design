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

- 其他讨论
    1. Single Dispatch，指的是执行哪个对象的方法，根据对象的运行时类型来决定；执行对象的哪个方法，根据方法参数的编译时类型来决定。
    2. Double Dispatch，指的是执行哪个对象的方法，根据对象的运行时类型来决定；执行对象的哪个方法，根据方法参数的运行时类型来决定。
    3. Dispatch：编程语言中一般指：消息的传递。一个对象调用另一个对象的方法相当于发消息。消息包括对象，方法名，参数。
    4. 具体到编程语言的语法机制，Single Dispatch 和 Double Dispatch 跟多态和函数重载直接相关
    5. 当前主流的面向对象编程语言（比如，Java、C++、C#）都只支持 Single Dispatch，不支持 Double Dispatch。

- 用工厂模式替代访问者模式
    ```java
    public abstract class ResourceFile {
        protected String filePath;
        public ResourceFile(String filePath) {
            this.filePath = filePath;
        }
        public abstract ResourceFileType getType();
    }

    public class PdfFile extends ResourceFile {
        public PdfFile(String filePath) {
            super(filePath);
        }

        @Override
        public ResourceFileType getType() {
            return ResourceFileType.PDF;
        }
    //...
    }

    //...PPTFile/WordFile跟PdfFile代码结构类似，此处省略...
    public interface Extractor {
        void extract2txt(ResourceFile resourceFile);
    }

    public class PdfExtractor implements Extractor {
        @Override
        public void extract2txt(ResourceFile resourceFile) {
            //...
        }
    }

    //...PPTExtractor/WordExtractor跟PdfExtractor代码结构类似，此处省略...

    public class ExtractorFactory {
        private static final Map<ResourceFileType, Extractor> extractors = new HashMap<>();
        static {
            extractors.put(ResourceFileType.PDF, new PdfExtractor());
            extractors.put(ResourceFileType.PPT, new PPTExtractor());
            extractors.put(ResourceFileType.WORD, new WordExtractor());
        }

        public static Extractor getExtractor(ResourceFileType type) {
            return extractors.get(type);
        }
    }

    public class ToolApplication {
        public static void main(String[] args) {
            List<ResourceFile> resourceFiles = listAllResourceFiles(args[0]);
            for (ResourceFile resourceFile : resourceFiles) {
                Extractor extractor = ExtractorFactory.getExtractor(resourceFile.getType());
                extractor.extract2txt(resourceFile);
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