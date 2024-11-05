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