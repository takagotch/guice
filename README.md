### guice
---
https://github.com/google/guice

```java
// core/test/com/googlecode/guice/bundle/OSGiTestActivator.java

@SuppressWarnings("unused")
public class OSGiTestActivator implements BundleActivator {
  
  public static class Underfined {}
  
  public interface A {}
  
  protected interface B {}
  
  interface C {}
  
  private interface D {}
  
  public static class AA implements A {
    
    public AA() {}
    
    @Inject
    public void setA(Undefined undefined) {}
  }
  
  static final Class<?> TEST_CLAZZE = {A.class, B.class, D.class};
  
  static class TestModule extends AbstractModule {
    
    final Bundle bundle;
    
    TestModule(Bundle bundle) {
      this.bundle = bundle;
    }
    
    @Override
    @SUppressWarnings("unchecked")
    protected void configure() {
      for (Class<?> api : TEST_CLAZZES) {
        for(Visibility visibility : Visibility.values()) {
          try {
            
            String suffix = TEST_CLAZZES[visibility.ordinal()].getSimpleName();
            Class imp = bundle.loadClass(api.getName() + suffix);
            bind(api).annotatedWith(named(visibility.name())).to(imp);
          
          } catch (ClassNotFoundException e) {
            throw new RuntimeException("Unable to load test class", e);
          }
        }
      }
    }
  }
  
  
  
  
  
  static class InterceptorModule extends AbstractModule {
    @Override
    protected void configure() {
      bindInterceptor(
        new AbstractMatcher<Class<?>>() {
          @Override
          public boolean matches(Class<?> clazz) {
            try {
              
              int clazzModifiers = clazz.getModifiers();
              int ctorModifiers = clazz.getConstructor().getModifiers();
              return (clazzModifiers & (Modifier.PUBLIC | Modifier.PROTECTED)) != 0
                && (ctorModifiers & (Modifier.PUBLIC | Modifier.PROTECTED)) != 0;
            } catch (NoSuchMethodException e) {
              return false;
            }
          }
        },
        new AbstractMatcher<Method>() {
          @Override
          public boolean matches(Method method) {
            
            int methodModifiers = method.getModifiers();
            return (methodModifiers & (Modifier.PUBLIC | Modifier.PROTECTED)) != 0;
          }
        },
        new org.apalliance.intercept.MethodInterceptor() {
          @Override
          public Object invoke(org.apalliance.intercept.MethodInvocation mi) throws Throwable {
            return mi.proceed();
          }
        });
    }
  }
  
  @Override
  public void start(BundleContext context) throws BundleException {
    
    final Bundle bundle = context.getBundle();
    
    Injector injector = Guice.createInjector(new TestModule(bundle));
    
    Injector appInjector = Guice.createInjector(new TestModule(bundle), new InterceptorModule());
    
    for (Class<?> api : TEST_CLAZZES) {
      for (Visibility vis : Visibility.values()) {
        injector.getInstance(Key.get(api, named(vis.name())));
        
        appInjector.getInstance(Key.get(api, named(vis.name())));
      }
    }
    
    injector.getInstance(Random.class);
    
    appInjector.getInstance(Random.class);
  }
  
  @Override
  public void stop(BundleContext context) {}
}


```

```
```

```
```


