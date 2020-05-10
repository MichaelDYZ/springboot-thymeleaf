# springboot-thymeleaf
springboot集成 thymeleaf 模板引擎
该项目展示了springboot如何集成 thymeleaf 模板引擎
一.创建新的springboot项目，引入pom文件。
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.2.7.RELEASE</version>
        <relativePath/> <!-- lookup parent from repository -->
    </parent>
    <groupId>com.thymeleaf</groupId>
    <artifactId>thymeleaf</artifactId>
    <version>0.0.1-SNAPSHOT</version>
    <name>thymeleaf</name>
    <description>Demo project for Spring Boot</description>

    <properties>
        <java.version>1.8</java.version>
    </properties>

    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter</artifactId>
        </dependency>


        <dependency>
            <groupId>cn.hutool</groupId>
            <artifactId>hutool-all</artifactId>
            <version>5.3.3</version>
        </dependency>

        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
            <optional>true</optional>
        </dependency>

        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-thymeleaf</artifactId>
        </dependency>

        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>

        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
            <exclusions>
                <exclusion>
                    <groupId>org.junit.vintage</groupId>
                    <artifactId>junit-vintage-engine</artifactId>
                </exclusion>
            </exclusions>
        </dependency>
    </dependencies>

    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
            </plugin>
        </plugins>
    </build>

</project>

二、修改application.yml：
server:
  port: 8080
  servlet:
    context-path: /demo
spring:
  thymeleaf:
    mode: HTML
    encoding: UTF-8
    servlet:
      content-type: text/html
    cache: false
三、创建一个学生实体类
/**
 * @author dyz
 * @version 1.0
 * @date 2020/5/9 14:30
 */
@Data
public class Student {
    private String name;
    private String password;
}
四、创建主页访问的Controller和学生登录页的Controller
/**
 * @author dyz
 * @version 1.0
 * @date 2020/5/9 14:31
 */
@Controller
@Slf4j
public class IndexController {

    /**
     *
     * @param request
     * @return
     */
    @GetMapping(value = {"", "/"})
    public ModelAndView index(HttpServletRequest request) {
        ModelAndView mv = new ModelAndView();

        Student student = (Student) request.getSession().getAttribute("student");
        if (ObjectUtil.isNull(student)) {
            mv.setViewName("redirect:/student/login");
        } else {
            mv.setViewName("page/index");
            mv.addObject(student);
        }

        return mv;
    }

}

/**
 * @author dyz
 * @version 1.0
 * @date 2020/5/9 14:32
 */
@Controller
@RequestMapping("/student")
@Slf4j
public class StudentController {


    /**
     * 登录
     * @param student
     * @param request
     * @return
     */
    @PostMapping("/login")
    public ModelAndView login(Student student, HttpServletRequest request) {
        ModelAndView mv = new ModelAndView();

        mv.addObject(student);
        mv.setViewName("redirect:/");

        request.getSession().setAttribute("student", student);
        return mv;
    }

    @GetMapping("/login")
    public ModelAndView login() {
        return new ModelAndView("page/login");
    }
}
五、创建登录页HTML以及首页HTML
<!doctype html>
<html lang="en" xmlns:th="http://www.thymeleaf.org">
<header th:replace="~{common/head :: header}"></header>
<body>
<div id="app" style="margin: 20px 20%">
	<form action="/demo/student/login" method="post">
		学生名<input type="text" name="name" placeholder="学生名"/>
		密码<input type="password" name="password" placeholder="密码"/>
		<input type="submit" value="登录">
	</form>
</div>
</body>
</html>
<!doctype html>
<html lang="en" xmlns:th="http://www.thymeleaf.org">
<header th:replace="~{common/head :: header}"></header>
<body>
<div id="app" style="margin: 20px 20%">
	欢迎登录，<span th:text="${student.name}"></span>！
</div>
</body>
</html>
六、启动项目，地址栏输入：http://127.0.0.1:8080/demo


​

七、源码地址：https://github.com/MichaelDYZ/springboot-thymeleaf
