# Quartz
 - Job Scheduling 라이브러리
 - Java 로 개발됨
 - 매주 일요일 12시에 실행하는 종류의 cron 작업도 지원


## 용어
 1. Job
    - Job 이라는 인터페이스가 존재한다.
    - 이 인터페이스에는 execute 라는 하나의 메서드가 존재.
        - 이 메서드에 수행해야할 작업을 작성한다.
    - 이 메서드에는 JobExecutionContext 라는 매개변수가 있는데 이는 작업 인스턴스에 대한 정보를 제공한다.
        - Schedule, Trigger, Key 등등..
    
    ```java
    @Slf4j
    class CronJob: Job {
    
        private lateinit var currentThread: Thread
        private val log = LoggerFactory.getLogger(this.javaClass)
    
        override fun execute(context: JobExecutionContext?) {
            val jobKey: JobKey? = context?.jobDetail?.key
    
            currentThread = Thread.currentThread()
    
            log.info("Job Start !! JobKey : $jobKey, CurrentThread : $currentThread")
    
            val a = 0..10
            a.forEach {
                log.info("$a 'st Job !!")
                Thread.sleep(5000)
            }
    
            log.info("Job End !! JobKey : $jobKey, CurrentThread : $currentThread")
        }
    }
    ```
  

 2. JobDetail
    - Job 을 실행시키기 위한 정보를 담고 있는 객체
        - Job 의 이름, 그룹
        - JobDataMap
    - Trigger 는 Job 을 수행할 때 이 정보를 기반으로 스케줄링을 해준다.

    ```java
    class CronJobDetail: JobDetail {
        override fun clone(): Any = TODO("Not yet implemented")
        override fun getKey(): JobKey = TODO("Not yet implemented")
        override fun getDescription(): String = TODO("Not yet implemented")
        override fun getJobClass(): Class<out Job> = TODO("Not yet implemented")
        override fun getJobDataMap(): JobDataMap = TODO("Not yet implemented")
        override fun isDurable(): Boolean = TODO("Not yet implemented")
        override fun isPersistJobDataAfterExecution(): Boolean = TODO("Not yet implemented")
        override fun isConcurrentExectionDisallowed(): Boolean = TODO("Not yet implemented")
        override fun requestsRecovery(): Boolean = TODO("Not yet implemented")
        override fun getJobBuilder(): JobBuilder = TODO("Not yet implemented")
    }
    
    fun createJob(jobExecutionContext: JobExecutionContext, job: Class<QuartzJobBean>, applicationContext: ApplicationContext): JobDetail =
            JobDetailFactoryBean().apply {
            this.setJobClass(job)
            this.setApplicationContext(applicationContext)
        }.`object`!!
    ```


 3. JobDataMap
    - 위의 Job 인스턴스가 실행될 때 필요한 정보를 담을 수 있는 컬렉션.
    ```java
    class CronJobDataMap: JobDataMap() {
        override fun put(key: String?, value: Float) {
            super.put(key, value)
        }
    
        override fun putAsString(key: String?, value: Int?) {
            super.putAsString(key, value)
        }
    ...
    }
    ```
    
    - Job 실행시 아래와 같이 접근가능.
    ```java
    val jobDataMap: jobDataMap? = context?.jobDetail?.jobDataMap
    ```
    
 4. Trigger
    - Job 을 실행시킬 스케줄링 조건을 가진다.
        - 반복 횟수, 반복 시간 ...
    - 스케줄러가 이 정보를 기반으로 Job 을 수행시킨다.
    - Trigger 종류
        - SimpleTrigger : Job 의 반복 횟수, 실행 간격을 지정할 수 있다.
        - CronTrigger : Job 의 반복 횟수, 실행 간격을 `Cron 식`으로 지정할 수 있다.
    
 5. Listener
    - Scheduler 의 이벤트를 받을 수 있도록 Quartz 에서 제공하는 인터페이스
        - job 의 전, 후로 어떤 작업을 실행한다던가.
        - Trigger 이 발생할때 어떤 작업을 실행한다던가.
    
 6. JobStore
    - Job 과 Trigger 의 정보를 저장할 수 있다.
    - 두가지 방식이 존재
        - JDBC JobStore : 스케줄 정보를 DB 에 저장하여, 영속화된다. 시스템이 죽어도 DB 에서 꺼내서 다시 사용가능.
        - In-Memory JobStore : Quartz 의 장점중 하나로, 메모리에 스케줄 정보를 저장. 성능상 좋다 !