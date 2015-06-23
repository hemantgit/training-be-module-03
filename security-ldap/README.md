# Backbase Training Exercises

## Security LDAP integration 
In this tutorial you will configure portal to communicate with LDAP server embedded into Training Server application.

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
	Re-compile *exercises-environment/services/* executing `mvn clean install` command.
	
- **Enable newly created module in Portal application.** Add the following dependency to your `portal/pom.xml` file in `<dependencies>` section:

	```xml
	    <dependency>
	        <groupId>com.backbase.expert.training</groupId>
	        <artifactId>security-ldap</artifactId>
	        <version>1.0-SNAPSHOT</version>
	    </dependency>
	```

- **Configure Portal security.** Add following elements to the configuration into *[Your CXP Root]/portal/src/main/resources/META-INF/spring/backbase-portal-business-security.xml*
	```xml
	        <!-- The LDAP context source -->
            <beans:bean id="ldapContextSource" class="org.springframework.security.ldap.DefaultSpringSecurityContextSource">
                <beans:constructor-arg value=" ldap://localhost:33389/dc=backbase,dc=com "/>
            </beans:bean>

            <!-- Configure Authentication mechanism -->
            <authentication-manager alias="authenticationManager">
                <authentication-provider user-service-ref="portalUserDetailsService">
                    <password-encoder ref="passwordEncoder"/>
                </authentication-provider>
                <authentication-provider ref="ldapAuthenticationProvider"/>
            </authentication-manager>
        
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

### Build & Run

- Build Portal module with executing `mvn clean install` command from *portal* directory.
- Make sure that Training Server is running.
- Start Portal application with executing `mvn jetty:run` command from *portal* directory.
- Go to CXP Manager and create user group `employees`.
- Create new portal and make sure `employees` group has access to it.
- Create a page and place *Login* and *Profile Summary* widgets on it.
- Login with user *john/backbase* and make sure that user is logged in.
