1. 什么是Spring Boot？ 

   Spring  Boot已经建立在现有spring框架之上。使用spring启动，我们避免了之前我们必须做的所有样板代码和配置。因此，Spring  Boot可以帮助我们以最少的工作量，更加健壮地使用现有的Spring 功能.

2. Spring Boot有哪些优点？ 

   减少开发，测试时间和努力。 
   使用JavaConfig有助于避免使用XML。 
   避免大量的Maven 导入和各种版本冲突。 
   提供意见发展方法。 
   通过提供默认值快速开始开发。 
   没有单独的Web 服务器需要。这意味着你不再需要启动Tomcat，Glassfish 或其他任何东西。 
   需要更少的配置  因为没有web.xml 文件。只需添加用@ Configuration 注释的类，然后添加用@Bean注释的方法，Spring 将自动加载对象并像以前一样对其进行管理。您甚至可以将@Autowired 添加到bean 方法中，以使Spring 自动装入需要的依赖关系中。 
   基于环境的配置  使用这些属性，您可以将您正在使用的环境传递到应用程序：-
   Dspring.profiles.active  =  {enviornment}。在加载主应用程序属性文件后，Spring 将在
   （application{environment} .properties）中加载后续的应用程序属性文件

3. 如何重新加载Spring Boot上的更改，而无需重新启动服务器？

   这可以使用DEV工具来实现。通过这种依赖关系，您可以节省任何更改，嵌入式tomcat
   将重新启动。Spring  Boot有一个开发工具（DevTools）模块，它有助于提高开发人员的生产力。Java开发人员面临的一个主要挑战是将文件更改自动部署到服务器并自动重启服务器。开发人员可以重新加载Spring  Boot上的更改，而无需重新启动服务器。这将消除每次手动部署更改的需要。Spring  Boot在发布它的第一个版本时没有这个功能。这是开发人员最需要的功能。DevTools 模块完全满足开发人员的需求。该模块将在生产环境中被禁用。它还提供H2数据库控制台以更好地测试应用程序。 

   org.springframework.boot spring-boot-devtools true 