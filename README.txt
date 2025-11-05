<jvmArguments>
    --add-opens=java.base/java.lang=ALL-UNNAMED
    --add-opens=java.base/sun.security.ssl=ALL-UNNAMED
    --add-opens=java.base/java.util=ALL-UNNAMED
    --add-opens=java.base/java.io=ALL-UNNAMED
    --add-opens=java.base/java.net=ALL-UNNAMED
    --add-opens=java.base/sun.nio.ch=ALL-UNNAMED
    --add-opens=java.base/java.io=ALL-UNNAMED
    --add-opens=java.base/sun.util.calendar=ALL-UNNAMED
    --add-opens=java.base/sun.security.action=ALL-UNNAMED
    --add-opens=java.base/java.security=ALL-UNNAMED
    --illegal-access=permit
</jvmArguments>

Альтернативное решение через application.yaml:
yaml
spring:
  jvmargs: >
    --add-opens=java.base/java.lang=ALL-UNNAMED
    --add-opens=java.base/sun.security.ssl=ALL-UNNAMED
    --add-opens=java.base/java.util=ALL-UNNAMED
    --add-opens=java.base/java.io=ALL-UNNAMED
    --add-opens=java.base/java.net=ALL-UNNAMED
    --add-opens=java.base/sun.nio.ch=ALL-UNNAMED
    --illegal-access=permit

Для немедленного решения запустите с флагами:
java --add-opens=java.base/java.lang=ALL-UNNAMED --add-opens=java.base/sun.security.ssl=ALL-UNNAMED --add-opens=java.base/java.util=ALL-UNNAMED --add-opens=java.base/java.io=ALL-UNNAMED --add-opens=java.base/java.net=ALL-UNNAMED --add-opens=java.base/sun.nio.ch=ALL-UNNAMED --illegal-access=permit -jar your-app.jar
