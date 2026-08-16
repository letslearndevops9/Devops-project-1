---
name: testing-twitter-app
description: How to build, run, and verify the Spring Boot twitter-app in Devops-project-1 locally
---

# Testing the twitter-app (Devops-project-1)

## App overview
- Spring Boot 3.3.2 (Java 17), artifact `twitter-app`, in-memory H2 DB (data resets on restart), Thymeleaf UI, Spring Security form login. Serves on port 8080.
- Routes: `/login`, `/register` are public; `/` (Daily Posts feed) and `/add` (create post) require login; `/logout` redirects to `/login?logout`; `/h2-console` is enabled and public.

## Running locally
- The repo's `mvnw` wrapper is broken as committed: `.mvn/wrapper/maven-wrapper.properties` is missing from the repo. Fix before running:
  ```
  mkdir -p .mvn/wrapper
  printf 'distributionUrl=https://repo.maven.apache.org/maven2/org/apache/maven/apache-maven/3.9.9/apache-maven-3.9.9-bin.zip\n' > .mvn/wrapper/maven-wrapper.properties
  chmod +x mvnw
  ./mvnw spring-boot:run
  ```
- First run downloads Maven + dependencies (~2-4 min). App is ready when the log shows "Tomcat started on port 8080".
- No credentials/secrets needed. Register any user via `/register`, then log in with it.

## Verification flow that works end to end
1. Open `http://localhost:8080/` — expect redirect to `/login` (styled "DevOps Shack" page).
2. Register a user at `/register` — expect redirect to `/register?success` with green "Registration Successful!" banner.
3. Log in — expect redirect to `/` showing "Daily Posts" and nav Home / Add Post / Logout.
4. Add Post → submit text — expect redirect to `/` with the post shown and "Posted by: <username>".
5. Logout — expect `/login?logout` with red "Logout Successful!" banner; `/` is protected again.

## Gotchas
- Pages have a fade-in animation; take a second screenshot after ~1.5s if content looks blank.
- Known cosmetic bug: register page's "Login" link href is `/login1` (register.html); it still lands on `/login` only because unauthenticated requests are redirected there.
- `GET /home` is permitted by SecurityConfig but has no controller mapping (returns 500/404); the real home is `/`.
