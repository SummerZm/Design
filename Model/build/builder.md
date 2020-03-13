## <b>建造者模式</b> ##
> 作用: 解决对像构造传参，验证问题


### <b>对象构造相关</b> ###
- 常用的对象构造方法
	1. 通过构造函数设置必填项
	2. 通过set()方法设置可选配置项 
	3. 说明: 不能把必填项在set设置,因为无法保证验证逻辑一定被执行]

- 常用的对象构造方法问题
	1. 问题1: 过多过长的构造函数个数/参数列表
	2. 问题2: 创建的对象存在"无效"的过渡状态
		```java
			// ResourcePoolConfig使用举例
			ResourcePoolConfig config = new ResourcePoolConfig("dbconnectionpool");
			// 在set做完之前config是“无效的”
			config.setMaxTotal(16);
			config.setMaxIdle(8);
		```
	3. 问题3: 创建不可变对象, 则不能有set()方法

### <b>建造模式应用例子</b> ###
```java
	public class ResourcePoolConfig {
	  private String name;
	  private int maxTotal;
	  private int maxIdle;
	  private int minIdle;

	  private ResourcePoolConfig(Builder builder) {
		this.name = builder.name;
		this.maxTotal = builder.maxTotal;
		this.maxIdle = builder.maxIdle;
		this.minIdle = builder.minIdle;
	  }
	  //...省略getter方法...

	  //我们将Builder类设计成了ResourcePoolConfig的内部类。
	  //我们也可以将Builder类设计成独立的非内部类ResourcePoolConfigBuilder。
	  public static class Builder {
		private static final int DEFAULT_MAX_TOTAL = 8;
		private static final int DEFAULT_MAX_IDLE = 8;
		private static final int DEFAULT_MIN_IDLE = 0;

		private String name;
		private int maxTotal = DEFAULT_MAX_TOTAL;
		private int maxIdle = DEFAULT_MAX_IDLE;
		private int minIdle = DEFAULT_MIN_IDLE;

		public ResourcePoolConfig build() {
		  // 校验逻辑放到这里来做，包括必填项校验、依赖关系校验、约束条件校验等
		  if (StringUtils.isBlank(name)) {
			throw new IllegalArgumentException("...");
		  }
		  if (maxIdle > maxTotal) {
			throw new IllegalArgumentException("...");
		  }
		  if (minIdle > maxTotal || minIdle > maxIdle) {
			throw new IllegalArgumentException("...");
		  }

		  return new ResourcePoolConfig(this);
		}

		public Builder setName(String name) {
		  if (StringUtils.isBlank(name)) {
			throw new IllegalArgumentException("...");
		  }
		  this.name = name;
		  return this;
		}

		public Builder setMaxTotal(int maxTotal) {
		  if (maxTotal <= 0) {
			throw new IllegalArgumentException("...");
		  }
		  this.maxTotal = maxTotal;
		  return this;
		}

		public Builder setMaxIdle(int maxIdle) {
		  if (maxIdle < 0) {
			throw new IllegalArgumentException("...");
		  }
		  this.maxIdle = maxIdle;
		  return this;
		}

		public Builder setMinIdle(int minIdle) {
		  if (minIdle < 0) {
			throw new IllegalArgumentException("...");
		  }
		  this.minIdle = minIdle;
		  return this;
		}
	  }
	}

	// 这段代码会抛出IllegalArgumentException，因为minIdle>maxIdle
	ResourcePoolConfig config = new ResourcePoolConfig.Builder()
			.setName("dbconnectionpool")
			.setMaxTotal(16)
			.setMaxIdle(10)
			.setMinIdle(12)
			.build();
```
