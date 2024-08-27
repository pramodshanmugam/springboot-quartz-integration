---
layout: post
title:  "How to Integrate Quartz with Spring Boot"
date:   2024-08-27 16:09:13 -0400
categories: springboot quartz
---

Quartz is a richly featured, open-source job scheduling library that can be integrated within virtually any Java application. Its flexibility, along with its ability to handle complex schedules and persistent job states, makes it ideal for enterprise-level scheduling needs.

### Setting Up Quartz in Spring Boot

#### 1. Gradle Dependencies

Start by adding the necessary dependencies to your `build.gradle`:

```groovy
    implementation 'org.springframework.boot:spring-boot-starter-quartz'
    implementation 'com.zaxxer:HikariCP:4.0.3'
```

#### 2. Configuring Quartz Scheduler

```groovy    
    @Bean
    public SchedulerFactoryBean schedulerFactoryBean(JobFactory jobFactory, DataSource dataSource) {
        SchedulerFactoryBean schedulerFactory = new SchedulerFactoryBean();
        schedulerFactory.setJobFactory(jobFactory);
        schedulerFactory.setDataSource(dataSource);
        schedulerFactory.setOverwriteExistingJobs(true);
        schedulerFactory.setAutoStartup(true);
        return schedulerFactory;
    }

    @Bean
    public JobDetailFactoryBean jobDetail() {
        JobDetailFactoryBean factoryBean = new JobDetailFactoryBean();
        factoryBean.setJobClass(SampleJob.class);
        factoryBean.setDescription("Sample Quartz Job");
        factoryBean.setDurability(true);
        return factoryBean;
    }

    @Bean
    public SimpleTriggerFactoryBean trigger(JobDetail jobDetail) {
        SimpleTriggerFactoryBean factoryBean = new SimpleTriggerFactoryBean();
        factoryBean.setJobDetail(jobDetail);
        factoryBean.setRepeatInterval(5000);  // Run every 5 seconds
        factoryBean.setRepeatCount(SimpleTrigger.REPEAT_INDEFINITELY);
        return factoryBean;
    }

    public static class AutowiringSpringBeanJobFactory extends SpringBeanJobFactory {
        @Autowired
        private AutowireCapableBeanFactory beanFactory;

        @Override
        protected Object createJobInstance(TriggerFiredBundle bundle) throws Exception {
            Object job = super.createJobInstance(bundle);
            beanFactory.autowireBean(job);
            return job;
        }
 ```

#### 3. Defining a DataSource

Defining the datasoruce shows where the schedulded job can be stored.

```groovy    

    public DataSource quartzDataSource() {
        HikariConfig config = new HikariConfig();
        config.setJdbcUrl(jdbcUrl);
        config.setUsername(username);
        config.setPassword(password);
        config.setDriverClassName(driverClassName);
        config.setMaximumPoolSize(maximumPoolSize);
        return new HikariDataSource(config);
    }
```

#### 4. Creating a Sample Quartz Job 

```groovy    

    @Component
    public class SampleJob implements Job {
    
        @Override
        public void execute(JobExecutionContext context) throws JobExecutionException {
            System.out.println("Sample Job is executing...");
            // Your business logic here
        }
    }
```
