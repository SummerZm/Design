## <b>责任链模式</b> ##
> 过滤器，拦截器，访问控制功能，比如鉴权、限流、日志等

### 应用场景 ###
1. 只处理自己能处理数据的，处理不的交给其它对象处理
2. 所有处理流程都要走一遍
3. 代码示例：
    ```java    
    // 链表方式实现
    public abstract class Handler {
        protected Handler successor = null;

        public void setSuccessor(Handler successor) {
            this.successor = successor;
        }

        public final void handle() {
            boolean handled = doHandle();
            if (successor != null && !handled) {
            successor.handle();
            }
        }

        protected abstract boolean doHandle();
    }

    public class HandlerA extends Handler {
        @Override
        protected boolean doHandle() {
            boolean handled = false;
            //...
            return handled;
        }
    }

    public class HandlerB extends Handler {
        @Override
        protected boolean doHandle() {
            boolean handled = false;
            //...
            return handled;
        }
    }

    // 数组方式实现
    public interface SensitiveWordFilter {
        boolean doFilter(Content content);
    }

    public class SexyWordFilter implements SensitiveWordFilter {
        @Override
        public boolean doFilter(Content content) {
            boolean legal = true;
            //...
            return legal;
        }
    }

    // PoliticalWordFilter、AdsWordFilter类代码结构与SexyWordFilter类似
    public class SensitiveWordFilterChain {
        private List<SensitiveWordFilter> filters = new ArrayList<>();

        public void addFilter(SensitiveWordFilter filter) {
            this.filters.add(filter);
        }

        // return true if content doesn't contain sensitive words.
        public boolean filter(Content content) {
            for (SensitiveWordFilter filter : filters) {
                if (!filter.doFilter(content)) {
                    return false;
                }
            }
            return true;
        }
    }

    public class ApplicationDemo {
        public static void main(String[] args) {
            SensitiveWordFilterChain filterChain = new SensitiveWordFilterChain();
            filterChain.addFilter(new AdsWordFilter());
            filterChain.addFilter(new SexyWordFilter());
            filterChain.addFilter(new PoliticalWordFilter());

            boolean legal = filterChain.filter(new Content());
            if (!legal) {
            // 不发表
            } else {
            // 发表
            }
        }
    }

    // spring intercpetor
    public class HandlerExecutionChain {
        private final Object handler;
        private HandlerInterceptor[] interceptors;
    
        public void addInterceptor(HandlerInterceptor interceptor) {
            initInterceptorList().add(interceptor);
        }

        boolean applyPreHandle(HttpServletRequest request, HttpServletResponse response) throws Exception {
            HandlerInterceptor[] interceptors = getInterceptors();
            if (!ObjectUtils.isEmpty(interceptors)) 
            {
                for (int i = 0; i < interceptors.length; i++) 
                {
                    HandlerInterceptor interceptor = interceptors[i];
                    if (!interceptor.preHandle(request, response, this.handler)) {
                        triggerAfterCompletion(request, response, null);
                        return false;
                    }
                }
            }
            return true;
        }

        void applyPostHandle(HttpServletRequest request, HttpServletResponse response, ModelAndView mv) throws Exception {
            HandlerInterceptor[] interceptors = getInterceptors();
            if (!ObjectUtils.isEmpty(interceptors)) {
                for (int i = interceptors.length - 1; i >= 0; i--) {
                    HandlerInterceptor interceptor = interceptors[i];
                    interceptor.postHandle(request, response, this.handler, mv);
                }
            }
        }

        void triggerAfterCompletion(HttpServletRequest request, HttpServletResponse response, Exception ex)
            throws Exception {
                HandlerInterceptor[] interceptors = getInterceptors();
                if (!ObjectUtils.isEmpty(interceptors)) {
                    for (int i = this.interceptorIndex; i >= 0; i--) {
                        HandlerInterceptor interceptor = interceptors[i];
                        try {
                            interceptor.afterCompletion(request, response, this.handler, ex);
                        } catch (Throwable ex2) {
                            logger.error("HandlerInterceptor.afterCompletion threw exception", ex2);
                        }
                    }
                }
            }
        }
    ```

### 责任链模式和代理模式的区别 ###
1. 代理模式：使用粒度是类，是对类的一个包装。[颗粒度较精细，广度]
2. 责任链模式: 对一件事，一个流程的定制包装。[侧重于灵活，深度]