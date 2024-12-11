---
title: "Function Level Access Control"
date: 2024-12-11
categories:
  - blog
tags:
  - security
  - spring
  - rbac
---
Function level access control (FLAC) also known as Function-Level Authorization, is a security mechanism used to ensure that users can only access application functionalities that they are authorized to use.
It is often implemented using RBAC (Role-Based Access Control) techniques. RBAC applications defines a set of roles (like 'Admin' or 'User') that are checked when trying to access privileged endpoints to ensure the user has the required permissions in order to execute the business function.
Failing to check these permissions would allow, for example, a regular 'User' to get access to privileged endpoints aimed for an 'Admin' role.

## Key Concepts of Function Level Access Control:
 - Granular Control: FLAC provides fine-grained access control by regulating access to specific functions or methods within an application, rather than just controlling access to entire resources or data sets.
 - Role-Based Access Control (RBAC): Often, FLAC is implemented using RBAC, where users are assigned roles, and each role has specific permissions associated with it. Users can only execute functions that their roles permit.
 - Contextual Access: In some systems, access might not only depend on the user's role but also on the context, such as the time of access, or user location.
 - Logging and Auditing: Proper implementation of FLAC includes logging access attempts, both successful and unsuccessful, to monitor for potential security breaches and to audit user actions.

## Mitigation Techniques
 - Use deny by default principle: Except for public endpoints, the "deny by default" principle should always be applied. Application endpoints should be restricted by default, and access should be granted explicitly
 - Use least privilege principle: When assigning permissions to users, only the minimum required permissions to carry out the business tasks should be assigned
 - Do not rely on hidden URLs: Applying a "security by obscurity" approach is not recommended. Hiding privileged endpoints will not protect the application against these vulnerabilities. Attackers can discover the endpoints and get access to privileged actions
 - Protect all business functions on server side using a Role Based Authorization (RBAC) mechanism, or similar
 - Authorization should be applied using centralized routines either provided by the framework or easy to use external modules

## Function Level Access Control in Spring
The Spring authorization filter allows for easy configuration of access control within an application setting up rules, which are defined using RequestMatchers. Keep in mind that the order of configuration matters.
 - First, configure all the endpoints that require authentication.
 - Then, if roles are defined, specify role-based access control.
 - Use the deny by default principle.

```java
@Configuration
@EnableWebSecurity
public class AuthorizationConfig {
    @Bean
    public SecurityFilterChain securityFilterChain(HttpSecurity http) throws Exception {
        http
            .authorizeHttpRequests(authorize -> authorize
				        .requestMatchers("/").permitAll()
				        .requestMatchers("/reports/**").authenticated()
				        .requestMatchers("/developper-portal").hasRole("DEV")
				        .requestMatchers("/config").hasAnyRole("DEV", "ADMIN")
				        .anyRequest().authenticated()
			      )
			      .and()
			      .formLogin(Customizer.withDefaults());
        return http.build();
    }
}
```
