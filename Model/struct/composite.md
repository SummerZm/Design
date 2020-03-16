## <b>组合模式</b> ##
> 简介: 很常见，没啥特色， 树结构

### <b>代码实现-例子</b> ###
```java
	public abstract class FileSystemNode {
	  protected String path;

	  public FileSystemNode(String path) {
		this.path = path;
	  }

	  public abstract int countNumOfFiles();
	  public abstract long countSizeOfFiles();

	  public String getPath() {
		return path;
	  }
	}

	public class File extends FileSystemNode {
	  public File(String path) {
		super(path);
	  }

	  @Override
	  public int countNumOfFiles() {
		return 1;
	  }

	  @Override
	  public long countSizeOfFiles() {
		java.io.File file = new java.io.File(path);
		if (!file.exists()) return 0;
		return file.length();
	  }
	}

	public class Directory extends FileSystemNode {
	  private List<FileSystemNode> subNodes = new ArrayList<>();

	  public Directory(String path) {
		super(path);
	  }

	  @Override
	  public int countNumOfFiles() {
		int numOfFiles = 0;
		for (FileSystemNode fileOrDir : subNodes) {
		  numOfFiles += fileOrDir.countNumOfFiles();
		}
		return numOfFiles;
	  }

	  @Override
	  public long countSizeOfFiles() {
		long sizeofFiles = 0;
		for (FileSystemNode fileOrDir : subNodes) {
		  sizeofFiles += fileOrDir.countSizeOfFiles();
		}
		return sizeofFiles;
	  }

	  public void addSubNode(FileSystemNode fileOrDir) {
		subNodes.add(fileOrDir);
	  }

	  public void removeSubNode(FileSystemNode fileOrDir) {
		int size = subNodes.size();
		int i = 0;
		for (; i < size; ++i) {
		  if (subNodes.get(i).getPath().equalsIgnoreCase(fileOrDir.getPath())) {
			break;
		  }
		}
		if (i < size) {
		  subNodes.remove(i);
		}
	  }
	}

	public class Demo {
	  public static void main(String[] args) {
		/**
		 * /
		 * /wz/
		 * /wz/a.txt
		 * /wz/b.txt
		 * /wz/movies/
		 * /wz/movies/c.avi
		 * /xzg/
		 * /xzg/docs/
		 * /xzg/docs/d.txt
		 */
		Directory fileSystemTree = new Directory("/");
		Directory node_wz = new Directory("/wz/");
		Directory node_xzg = new Directory("/xzg/");
		fileSystemTree.addSubNode(node_wz);
		fileSystemTree.addSubNode(node_xzg);

		File node_wz_a = new File("/wz/a.txt");
		File node_wz_b = new File("/wz/b.txt");
		Directory node_wz_movies = new Directory("/wz/movies/");
		node_wz.addSubNode(node_wz_a);
		node_wz.addSubNode(node_wz_b);
		node_wz.addSubNode(node_wz_movies);

		File node_wz_movies_c = new File("/wz/movies/c.avi");
		node_wz_movies.addSubNode(node_wz_movies_c);

		Directory node_xzg_docs = new Directory("/xzg/docs/");
		node_xzg.addSubNode(node_xzg_docs);

		File node_xzg_docs_d = new File("/xzg/docs/d.txt");
		node_xzg_docs.addSubNode(node_xzg_docs_d);

		System.out.println("/ files num:" + fileSystemTree.countNumOfFiles());
		System.out.println("/wz/ files num:" + node_wz.countNumOfFiles());
	  }
	}
```

