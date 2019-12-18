## <b>Relation and UML</b> ##

### <b>UML 对象分析过程</b> ###
1. 功能职责划分-类
2. 类的属性方法
3. 类间关系结构分析
4. 提供功能入口

### <b>类间关系</b> ###
>  依赖[Dependency]包含关联[Association]，聚合[aggregation]， 组合[Composition]；关联包含聚合，组合。实现[Realization]

1. <b>泛化 - 继承[inherf]</b>:  一般用于拓展第三方类 [三角实线箭头]
2. <b>实现 - 接口[interface]</b>:   类与类之间的协议 [三角虚线箭头]
3. <b>依赖 - 嵌入</b>: 类与类之间的协作
   - 聚合: []A聚合B, AB生命周期可以不同步 
    ```C++
            // 空心菱形箭头
            class A ()
            {
                // Malloc outside.
                public:
                    A(B* b) {mb=b; }
                private:
                    B *mb;
            }
    ```
   - 组合: A组合B,  AB生命周期要求同时释放
   ```C++
            // 实心菱形箭头
            class A()
            {
                // Malloc inside
                public:
                    A() { mb=new B(); }
                private:
                    B *mb;
            }
   ```
   - 传参: 将逻辑封装成类B, 传入类A
    ```C++
        class A()
        {
            // Dependency
            public:
                sort(B* b) { b->quickSort(data); }
            private:
                int data[10];
        }
    ```