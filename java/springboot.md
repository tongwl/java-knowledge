# springboot

1. `@ComponentScan`
   `@ComponentScan`主要就是**定义扫描的路径**从中找出标识了**需要装配的类**自动装配到spring的bean容器中。

   做过web开发的同学一定都有用过@Controller，@Service，@Repository注解，查看其源码你会发现，他们中有一个**共同的注解@Component**，没错@ComponentScan注解默认就会装配标识了@Controller，@Service，@Repository，@[Component](https://so.csdn.net/so/search?q=Component&spm=1001.2101.3001.7020)注解的类到spring容器中。

2. `RestController`

   @RestController注解等价于@ResponseBody ＋ @Controller。@RestController和@Controller的共同点是都用来表示Spring某个类是否可以接收HTTP请求，**二者区别： @RestController无法返回指定页面，而@Controller可以**；前者可以直接返回数据，后者需要@ResponseBody辅助。

2. Spring Boot中如何实现对不同环境的属性配置文件的支持？

   Spring-Boot支持不同环境的属性配置文件切换，通过创建application-{profile}.properties文件，其中{profile}是具体的环境标识名称，例如： application-dev.properties用于开发环境，application-test.properties用于测试环境，application-uat.properties用于uat环境。如果要想使用application-dev.properties文件，则在application.properties文件中添加`spring.profiles.active=dev`;如果要想使用application-test.properties文件，则在application.properties文件中添加spring.profiles.active=test。

4. `@Data`

   在创建[实体类](https://so.csdn.net/so/search?q=实体类&spm=1001.2101.3001.7020)的时候 使用@Date注解。
   主要作用是提高代码的简洁，使用这个注解可以省去代码中大量的get()、 set()、 toString()等方法；

5. `@ApiOperation`用于swagger。
   @ApiOperation和@ApiParam为添加的API相关注解，个参数说明如下：
   @ApiOperation(value = “接口说明”, httpMethod = “接口请求方式”, response = “接口返回参数类型”, notes = “接口发布说明”）；其他参数可参考源码；
   @ApiParam(required = “是否必须参数”, name = “参数名称”, value = “参数具体描述”）

   @Autowired
   这个注解的作用是将其他的类，接口引入，类似于之前的类的初始化等，用这个注解，类中或接口的方法就可以直接调用了。

   @RequestMapping
   当前台界面调用Controller处理数据时候告诉控制器怎么操作
   作用：URL映射。

   @GetMapping
   @RequestMapping(method = RequestMethod.GET)的简写
   作用：对应查询，表明是一个查询URL映射

   @PostMapping
   @RequestMapping(method = RequestMethod.POST)的简写
   作用：对应增加，表明是一个增加URL映射

   @PutMapping
   @RequestMapping(method = RequestMethod.PUT)的简写
   作用：对应更新，表明是一个更新URL映射

   @DeleteMapping
   @RequestMapping(method = RequestMethod.DELETE)的简写
   作用：对应删除，表明是一个删除URL映射

6. 使用 Preconditions.checkNotNull(.....) 来处理, 相当于省掉自己再手写 throw new NullPointerException(String.valueOf(errorMessage));

   Link: https://www.cnblogs.com/yangsanluo/p/14210089.html

7. Test

