---
layout: post
title:  "How to Prevent Quartz Jobs from Being Deleted on Application Startup"
date:   2024-08-27 17:29:00 -0400
categories: springboot quartz
---

When working with Quartz in a Spring Boot application, it's common to want to persist jobs across application restarts. By default, Quartz can overwrite existing jobs on startup, but you can configure it to retain jobs and avoid deletion.

### Preventing Job Deletion on Startup

To ensure Quartz jobs aren't deleted or overwritten when the application restarts, follow these steps:

#### 1. Set `overwriteExistingJobs` to `false`

In your `schedulerFactoryBean` configuration, ensure that `overwriteExistingJobs` is set to `false`:

```java
@Bean
public SchedulerFactoryBean schedulerFactoryBean(JobFactory jobFactory, DataSource dataSource) {
    SchedulerFactoryBean schedulerFactory = new SchedulerFactoryBean();
    schedulerFactory.setJobFactory(jobFactory);
    schedulerFactory.setDataSource(dataSource);

    // Disable overwriting of existing jobs
    schedulerFactory.setOverwriteExistingJobs(false);
    
    schedulerFactory.setAutoStartup(true);
    return schedulerFactory;
}
```