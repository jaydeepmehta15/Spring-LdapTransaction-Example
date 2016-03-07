# Spring-LdapTransaction-Example
Spring Ldap Project 事务配置文件

``` java
@Configuration
@ComponentScan(basePackages = "com")
@PropertySource("classpath:application.properties")
@EnableTransactionManagement
public class AppConfig {

    @Bean
    public LdapTemplate ldapTemplate(TransactionAwareContextSourceProxy dcs) {
        return new LdapTemplate(dcs);
    }

    @Bean
    public LdapContextSource contextSource(Environment env) {
        LdapParams ldapParams = new LdapParams();
        ldapParams.setPwd(env.getProperty("ldap.pwd"));
        ldapParams.setUrl(env.getProperty("ldap.url"));
        ldapParams.setUserDN(env.getProperty("ldap.userDN"));

        LdapContextSource dcs = new LdapContextSource();
        // build connection pool,default max is 8
        dcs.setPooled(true);
        dcs.setUrl(ldapParams.getUrl());
        dcs.setUserDn(ldapParams.getUserDN());
        dcs.setPassword(ldapParams.getPwd());
        // LdapContextSource is InitializingBean, so ...
        dcs.afterPropertiesSet();
        return dcs;
    }

    @Bean
    public TransactionAwareContextSourceProxy transactionAwareContextSourceProxy(LdapContextSource ldapContextSource) {
        return new TransactionAwareContextSourceProxy(ldapContextSource);
    }

    @Bean
    public ContextSourceTransactionManager contextSourceTransactionManager(TransactionAwareContextSourceProxy transactionAwareContextSourceProxy) throws Exception {
        ContextSourceTransactionManager contextSourceTransactionManager = new ContextSourceTransactionManager();
        contextSourceTransactionManager.setContextSource(transactionAwareContextSourceProxy);
        contextSourceTransactionManager.setRenamingStrategy(new DefaultTempEntryRenamingStrategy());
        contextSourceTransactionManager.afterPropertiesSet();
        return contextSourceTransactionManager;
    }
}
```
