<plugin>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-maven-plugin</artifactId>
    <configuration>
        <jvmArguments>
            --add-opens java.base/java.lang=ALL-UNNAMED
            --add-opens java.base/sun.security.ssl=ALL-UNNAMED
            --add-opens java.base/java.util=ALL-UNNAMED
            --add-opens java.base/java.io=ALL-UNNAMED
        </jvmArguments>
    </configuration>
</plugin>
