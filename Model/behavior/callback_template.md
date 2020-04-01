## <b>模板模式与回调</b> ##
> 目的：解决复用和拓展两个问题 【继承和多态】

- 复用同样的流程代码
    1. 在抽象类中定义算法的流程，把实现推迟到子类中去，实现对流程代码的复用
    2. 代码示例：
        ```Java
        public abstract class InputStream implements Closeable {
            //...省略其他代码...
            public int read(byte b[], int off, int len) throws IOException 
            {
                if (b == null) {
                    throw new NullPointerException();
                } else if (off < 0 || len < 0 || len > b.length - off) {
                    throw new IndexOutOfBoundsException();
                } else if (len == 0) {
                    return 0;
                }

                int c = read();
                if (c == -1) {
                    return -1;
                }
                b[off] = (byte)c;

                int i = 1;
                try {
                    for (; i < len ; i++) {
                        c = read();
                        if (c == -1) {
                            break;
                        }
                        b[off + i] = (byte)c;
                    }
                } 
                catch (IOException ee) {
                }
                return i;
            }
            public abstract int read() throws IOException;
        }

        public class ByteArrayInputStream extends InputStream {
            //...省略其他代码...
            @Override
            public synchronized int read() {
                return (pos < count) ? (buf[pos++] & 0xff) : -1;
            }
        }
        ```

- 使用模板方法使得框架可拓展
    ```Java
    public void service(ServletRequest req, ServletResponse res)
        throws ServletException, IOException
    {
        HttpServletRequest  request;
        HttpServletResponse response;
        if (!(req instanceof HttpServletRequest &&
                res instanceof HttpServletResponse)) {
            throw new ServletException("non-HTTP request or response");
        }
        request = (HttpServletRequest) req;
        response = (HttpServletResponse) res;
        service(request, response);
    }

    protected void service(HttpServletRequest req, HttpServletResponse resp)
        throws ServletException, IOException
    {
        String method = req.getMethod();
        if (method.equals(METHOD_GET)) {
            long lastModified = getLastModified(req);
            if (lastModified == -1) {
                // servlet doesn't support if-modified-since, no reason
                // to go through further expensive logic
                doGet(req, resp);
            } else {
                long ifModifiedSince = req.getDateHeader(HEADER_IFMODSINCE);
                if (ifModifiedSince < lastModified) {
                    // If the servlet mod time is later, call doGet()
                    // Round down to the nearest second for a proper compare
                    // A ifModifiedSince of -1 will always be less
                    maybeSetLastModified(resp, lastModified);
                    doGet(req, resp);
                } else {
                    resp.setStatus(HttpServletResponse.SC_NOT_MODIFIED);
                }
            }
        } else if (method.equals(METHOD_HEAD)) {
            long lastModified = getLastModified(req);
            maybeSetLastModified(resp, lastModified);
            doHead(req, resp);
        } else if (method.equals(METHOD_POST)) {
            doPost(req, resp);
        } else if (method.equals(METHOD_PUT)) {
            doPut(req, resp);
        } else if (method.equals(METHOD_DELETE)) {
            doDelete(req, resp);
        } else if (method.equals(METHOD_OPTIONS)) {
            doOptions(req,resp);
        } else if (method.equals(METHOD_TRACE)) {
            doTrace(req,resp);
        } else {
            String errMsg = lStrings.getString("http.method_not_implemented");
            Object[] errArgs = new Object[1];
            errArgs[0] = method;
            errMsg = MessageFormat.format(errMsg, errArgs);
            resp.sendError(HttpServletResponse.SC_NOT_IMPLEMENTED, errMsg);
        }
    }
    ```
-  回调与模板模式的区别
    1. 回调基于组合关系来实现，把一个对象传递给另一个对象，是一种对象之间的关系；
    2. 模板模式基于继承关系来实现，子类重写父类的抽象方法，是一种类之间的关系。
    3. 组合优于继承
        - Java 这种只支持单继承的语言，基于模板模式编写的子类，已经继承了一个父类，不再具有继承的能力
        - 模板模式针对不同的实现都要定义不同的子类，子类必须实现所有的抽象方法
        - 回调可以使用匿名类来创建回调对象，可以不用事先定义类；而模板模式针对不同的实现都要定义不同的子类
        
- 总结：
    1. 最精华的是不模板方法，而是不同层次，不同部位去理解的模板办法，还有解决了什么问题？