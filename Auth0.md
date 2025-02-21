### **Auth0 in Spring Boot Microservices: Example with Workflow**
Implementing **Auth0 authentication** in a **Spring Boot microservices** architecture involves:
1. **User authentication via Auth0**.
2. **Token validation in Spring Boot API Gateway**.
3. **Propagation of authentication to microservices**.
4. **Role-based access control (RBAC) for securing endpoints**.

---

## **1️⃣ Architecture Workflow**
```
[Frontend (React/Angular)] → [Auth0] → [Spring Boot API Gateway] → [Microservices]
```

### **Workflow:**
1. **User Authentication via Auth0**
   - The user logs in using **Authorization Code with PKCE** (frontend) or via **BFF (backend for frontend)**.
   - Auth0 generates an **access token** (JWT).
   - The token is sent to the **API Gateway**.

2. **Token Validation at the API Gateway**
   - The API Gateway (Spring Boot + Spring Security) validates the JWT using Auth0's public key.
   - If valid, the request is forwarded to microservices.

3. **Microservices Authorization**
   - Microservices receive the token and extract **roles/permissions**.
   - If authorized, the request is processed.

4. **Response Back to User**
   - The response is sent back via API Gateway to the frontend.

---

## **2️⃣ Spring Boot Implementation**
### **A. Dependencies**
Add the required dependencies in `pom.xml`:
```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-oauth2-resource-server</artifactId>
</dependency>

<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-security</artifactId>
</dependency>
```

---

### **B. Configure Auth0 in Spring Boot**
Create an **OAuth2 Security Configuration** to validate Auth0 tokens.

📌 **`SecurityConfig.java`**
```java
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.security.config.annotation.web.builders.HttpSecurity;
import org.springframework.security.config.http.SessionCreationPolicy;
import org.springframework.security.oauth2.server.resource.authentication.JwtAuthenticationConverter;
import org.springframework.security.oauth2.server.resource.authentication.JwtGrantedAuthoritiesConverter;

@Configuration
public class SecurityConfig {

    @Bean
    public SecurityFilterChain securityFilterChain(HttpSecurity http) throws Exception {
        http
            .authorizeHttpRequests(auth -> auth
                .requestMatchers("/public/**").permitAll()
                .requestMatchers("/admin/**").hasAuthority("ROLE_ADMIN")
                .requestMatchers("/user/**").hasAuthority("ROLE_USER")
                .anyRequest().authenticated()
            )
            .oauth2ResourceServer(oauth2 -> oauth2.jwt(
                jwtConfigurer -> jwtConfigurer.jwtAuthenticationConverter(jwtAuthenticationConverter())
            ))
            .sessionManagement(session -> session.sessionCreationPolicy(SessionCreationPolicy.STATELESS));

        return http.build();
    }

    private JwtAuthenticationConverter jwtAuthenticationConverter() {
        JwtGrantedAuthoritiesConverter converter = new JwtGrantedAuthoritiesConverter();
        converter.setAuthoritiesClaimName("roles"); // Map to Auth0 roles claim
        converter.setAuthorityPrefix("ROLE_");

        JwtAuthenticationConverter jwtConverter = new JwtAuthenticationConverter();
        jwtConverter.setJwtGrantedAuthoritiesConverter(converter);
        return jwtConverter;
    }
}
```
🔹 This configures **JWT-based authentication** and extracts user roles from Auth0.

---

### **C. Configure Auth0 Properties**
📌 **`application.yml`**
```yaml
spring:
  security:
    oauth2:
      resourceserver:
        jwt:
          issuer-uri: https://YOUR_AUTH0_DOMAIN/
          audience: YOUR_AUTH0_AUDIENCE
```
🔹 Replace `YOUR_AUTH0_DOMAIN` with your **Auth0 domain** (e.g., `https://dev-xyz.auth0.com/`).  
🔹 Replace `YOUR_AUTH0_AUDIENCE` with your API audience in Auth0 settings.

---

### **D. Creating Microservices**
Example **User Service** that extracts user information from JWT.

📌 **`UserController.java`**
```java
import org.springframework.security.core.annotation.AuthenticationPrincipal;
import org.springframework.security.oauth2.jwt.Jwt;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

import java.util.Map;

@RestController
@RequestMapping("/user")
public class UserController {

    @GetMapping("/profile")
    public Map<String, Object> getUserProfile(@AuthenticationPrincipal Jwt jwt) {
        return jwt.getClaims(); // Returns user's Auth0 claims
    }
}
```
🔹 Authenticated users can retrieve their **profile details**.

---

### **E. Testing the Setup**
1. Start the Spring Boot microservices.
2. Use **Postman** or **Curl** to test API endpoints.

#### ✅ **Testing Public Endpoint**
```sh
curl -X GET http://localhost:8080/public/info
```
_Response:_ ✅ (No token required)

#### ❌ **Testing Secured User Endpoint Without Token**
```sh
curl -X GET http://localhost:8080/user/profile
```
_Response:_ ❌ `401 Unauthorized`

#### ✅ **Testing Secured Endpoint With Auth0 Token**
First, obtain a **JWT token** from Auth0:
```sh
TOKEN=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...
```
Then send the request with the token:
```sh
curl -X GET http://localhost:8080/user/profile -H "Authorization: Bearer $TOKEN"
```
_Response:_ ✅ Returns user profile info.

---

## **3️⃣ API Gateway for Microservices**
In a **microservices setup**, requests should pass through an **API Gateway** (Spring Cloud Gateway).

📌 **`ApiGatewayConfig.java`**
```java
import org.springframework.cloud.gateway.route.RouteLocator;
import org.springframework.cloud.gateway.route.builder.RouteLocatorBuilder;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

@Configuration
public class ApiGatewayConfig {

    @Bean
    public RouteLocator gatewayRoutes(RouteLocatorBuilder builder) {
        return builder.routes()
            .route("user-service", r -> r.path("/user/**")
                .uri("http://localhost:8081"))
            .route("order-service", r -> r.path("/order/**")
                .uri("http://localhost:8082"))
            .build();
    }
}
```
🔹 The **API Gateway** forwards requests to the respective microservices.

---

## **4️⃣ Role-Based Access Control (RBAC) in Auth0**
1. **Enable RBAC in Auth0 Dashboard:**
   - Go to **Auth0 Dashboard** → **APIs** → **Settings**.
   - Enable **RBAC** and **Add Permissions in the Access Token**.

2. **Assign Roles in Auth0 Dashboard:**
   - Navigate to **Users** → Select a user → **Assign Roles**.
   - Example:
     - **Admin Role** → `ROLE_ADMIN`
     - **User Role** → `ROLE_USER`

3. **Secure Endpoints in Spring Boot:**
```java
.requestMatchers("/admin/**").hasAuthority("ROLE_ADMIN")
.requestMatchers("/user/**").hasAuthority("ROLE_USER")
```
🔹 Users will only access routes based on their assigned roles.

---

## **✅ Conclusion**
This example demonstrates:
✅ **Auth0 authentication in Spring Boot**  
✅ **Securing microservices with JWT tokens**  
✅ **RBAC-based access control**  
✅ **API Gateway for request routing**  

