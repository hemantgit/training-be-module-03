# Backbase Training Exercises

## Portal Backend - Module 3: LDAP Integration

This exercise is part of [Module 3: Security](https://github.com/Backbase/training-be-module-03/tree/code-migration)

In this tutorial you will configure portal to communicate with LDAP server embedded into Training Server application.

### Prerequisites

You need to get a training server up and running before performing this excerise.
Follow the instructions that can be found here [training-server](https://github.com/Backbase/training-server/tree/code-migration)

### Installation & Configuration

- **Copy security-ldap from training-modules into the services folder of your Launchpad 0.12.x project.**

- **Include security-ldap module to the build.** Open `pom.xml` from *exercises-environment/services/*. Add `<module>security-ldap</module>` into  `<modules>` section
	```xml
	    <modules>
	        ...	    
	        <module>security-ldap</module>
	        ...
	    </modules>
	```	
	Re-compile **services** by executing `mvn clean install` in the **services** folder.
	
- **Enable newly created module in Portal application.** Add the following dependency to your `portal/pom.xml` file in `<dependencies>` section:

	```xml
	    <dependency>
	        <groupId>com.backbase.training</groupId>
	        <artifactId>security-ldap</artifactId>
	        <version>1.0-SNAPSHOT</version>
	    </dependency>
	```

- Configure Portal security. Add the following elements to the configuration into **portal/src/main/resources/META-INF/spring/backbase-portal-business-security.xml**:
	```xml
	    <!-- The LDAP context source -->
            <beans:bean id="ldapContextSource" class="org.springframework.security.ldap.DefaultSpringSecurityContextSource">
                <beans:constructor-arg value=" ldap://localhost:33389/dc=backbase,dc=com "/>
            </beans:bean>

            <!--  The LDAP authentication provider configuration -->
            <beans:bean id="ldapAuthenticationProvider"
                        class="org.springframework.security.ldap.authentication.LdapAuthenticationProvider">
        
                <beans:constructor-arg>
                    <beans:bean class="org.springframework.security.ldap.authentication.BindAuthenticator">
                        <beans:constructor-arg ref="ldapContextSource"/>
                        <beans:property name="userSearch">
                            <beans:bean class="org.springframework.security.ldap.search.FilterBasedLdapUserSearch">
                                <beans:constructor-arg index="0" value="ou=people"/>
                                <beans:constructor-arg index="1" value="(uid={0})"/>
                                <beans:constructor-arg index="2" ref="ldapContextSource"/>
                            </beans:bean>
                        </beans:property>
                    </beans:bean>
                </beans:constructor-arg>
        
                <beans:constructor-arg>
                    <beans:bean class="org.springframework.security.ldap.userdetails.DefaultLdapAuthoritiesPopulator">
                        <beans:constructor-arg ref="ldapContextSource"/>
                        <beans:constructor-arg value="ou=groups"/>
                    </beans:bean>
                </beans:constructor-arg>
        
                <beans:property name="userDetailsContextMapper">
                    <beans:bean class="com.backbase.training.security.ldap.UserDetailsContextMapperImpl">
                        <beans:constructor-arg ref="userService"/>
                        <beans:constructor-arg ref="groupService"/>
                    </beans:bean>
                </beans:property>
        
            </beans:bean>
	```
	
	Include the following line within the `<authentication-manager>` block:
	
        ```xml
        <authentication-manager>
            ...
            <authentication-provider ref="ldapAuthenticationProvider"/>
            ...
        </authentication-manager>
        ```

### Build & Run

- Build Portal module with executing `mvn clean install` command from *portal* directory.
- Make sure that Training Server is running.
- Start Portal application with executing `mvn jetty:run` command from *portal* directory.
- Go to CXP Manager and create user group `employees`.
- Create new portal and make sure `employees` group has access to it.
- Create a page and place *Login* and *Profile Summary* widgets on it.
- Login with user *john/backbase* and make sure that user is logged in.
