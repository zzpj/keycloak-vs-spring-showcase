# Keycloak vs Spring Authorization Server

## Installation (00)

### Keycloak
* Download from official [page](https://www.keycloak.org/getting-started/getting-started-zip)
* Run with command: 
  * Linux: ``bin/kc.sh start-dev --http-port=9001``
  * Windows: ``bin\kc.bat start-dev --http-port 9001``

### Spring Authorization Server
* Use Spring Initializr
* Select `Spring Authorization Server`
* Generate, unzip and run it

## First user (01)
### Keycloak
* Use UI

### Spring Authorization Server
Add following snippets:

```java
@Configuration
@EnableWebSecurity
public class SecurityConfig {

    @Bean
    public SecurityFilterChain securityFilterChain(HttpSecurity http) throws Exception {
        http
                .csrf(AbstractHttpConfigurer::disable)
                .authorizeHttpRequests(request -> request.anyRequest().authenticated())
                .formLogin(e -> e.defaultSuccessUrl("/hello", true));
        return http.build();
    }
}
```
```java
@RestController
public class DemoController {

    @GetMapping("/hello")
    public String hello() {
        Authentication auth = SecurityContextHolder.getContext().getAuthentication();
        User user = (User) auth.getPrincipal();
        return String.format("Hello %s!", user.getUsername());
    }
}
```
```yaml
spring:
  application:
    name: AuthorizationServerDemo-01
  security:
    user:
      name: user
      password: password
server:
  port: 8001
```


## Social login (02)
```java
@Configuration
@EnableWebSecurity
public class SecurityConfig {

  @Bean
  public SecurityFilterChain securityFilterChain(HttpSecurity http) throws Exception {
    http
            .csrf(AbstractHttpConfigurer::disable)
            .authorizeHttpRequests(request -> request.anyRequest().authenticated())
            .oauth2Login(e -> e.defaultSuccessUrl("/hello", true))
            .formLogin(e -> e.defaultSuccessUrl("/hello", true))
    ;
    return http.build();
  }
}
```
```java
@RestController
public class DemoController {

  @RequestMapping(value = "/hello", method = RequestMethod.GET, produces = MediaType.APPLICATION_JSON_VALUE)
  public String hello() {
    Authentication auth = SecurityContextHolder.getContext().getAuthentication();
    Object principal = auth.getPrincipal();
    return principal.toString();

  }
}
```
```yaml
spring:
  application:
    name: AuthorizationServerDemo-02

  security:
    user:
      name: user
      password: password
    oauth2:
      client:
        registration:
          google:
            client-name: Login me with Google
            client-id:
            client-secret:
          github:
            client-name: Login me with Github
            client-id:
            client-secret:

server:
  port: 8080

logging:
  level:
    org.springframework: INFO
```

## Application registration (03)

## Start-up and configuration features
* By using different profiles, run it in the appropriate configuration.
* Profiles are named accordingly, like authorization providers, but in lowercase (`keycloak`, `spring`).

### Features of launching the client with Keycloak
* Import 

### Features of launching the client with Spring Authorization Server
You will need to run the module "spring-auth-server" as a separate application.

### Hints
> Spring applications may have conflicts with their cookies due to the fact that both instances `spring-auth-server` 
> and `spring-oauth2-client` will be running on localhost. It doesn't matter that they use different ports.
> Therefore, it is recommended to use a different address than the localhost for one of the services. In this case, you can just add a following entry `auth.mydomain.com    127.0.0.1` to your host file.
