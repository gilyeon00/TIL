# JobStep

- Job 에 속하는 Step 중 외부의 Job을 포함하고 있는 Step
- JobStepBuilder 를 반환한다.
- 외부의 Job 이 실패하면 해당 Step 이 실패하므로, 결국 최종 Job 도 실패한다
- 모든 메타데이터는 기본 Job, 외부 Job 별로 각각 저장된다
- 사용 이유 : 커다란 시스템을 작은 모듈로 쪼개고, job 의 흐름을 관리하고자 할 때 사용한다

```java
@Bean
    public Step jobStep() {
        return stepBuilderFactory.get("my-jobSTep")
                .job(Job)           // JobStep 내에서 실행 될 Job 설정한다. JobStepBuilder 반환
                .launcher(JobLauncher) 
                .parametersExtractor(JobParametersExtractor)    
                .build();
    }
```

- `launcher` : Job 을 실행할 JobLauncher 설정한다.
    - .launcher(JobLauncher joblauncher)
- `parametersExtractor` : Step의 ExecutionContext 를 Job 이 실행되는데 필요한 JobParameters 로 변환
    - ExecutionContext 에 있는 값을 key 를 통해 가져올 수 있음 

    ```java
    .parametersExtractor(new JobParametersExtractor() {
                        @Override
                        public JobParameters getJobParameters(Job job, StepExecution stepExecution) {
                            return null;
                        }
                    })
    ```


```java
    @Bean
public Job parentJob() {
        return jobBuilderFactory.get("parentJob")
        .start(jobStep(null))
        .next(step2())
        .build();
        }

// ExecutionContext 에 있는 값을 key 를 통해 가져올 수 있음
private DefaultJobParametersExtractor myJobParametersExtractor() {
        DefaultJobParametersExtractor extractor = new DefaultJobParametersExtractor();
        extractor.setKeys(new String[]{"name"});
        return extractor;
        }
@Bean
public Step jobStep (JobLauncher jobLauncher) {
        return stepBuilderFactory.get("my-jobStep")
        .job(childJob())
        .launcher(jobLauncher)
//                .parametersExtractor(new JobParametersExtractor() {
//                    @Override
//                    public JobParameters getJobParameters(Job job, StepExecution stepExecution) {
//                        return null;
//                    }
//                })
        .parametersExtractor(myJobParametersExtractor())
        .listener(new StepExecutionListener() {
@Override
public void beforeStep(StepExecution stepExecution) {
        stepExecution.getExecutionContext().putString("name", "user1");
        }

@Override
public ExitStatus afterStep(StepExecution stepExecution) {
        return null;
        }
        })
        .build();
        }

@Bean
public Job childJob() {
        return jobBuilderFactory.get("childJob")
        .start(step1())
        .incrementer(new RunIdIncrementer())
        .build();
        }

@Bean
public Step step1 () {
        return stepBuilderFactory.get("step1")
        .tasklet(((contribution, chunkContext) -> RepeatStatus.FINISHED))
        .build();
        }

@Bean
public Step step2 () {
        return stepBuilderFactory.get("step2")
        .tasklet(((contribution, chunkContext) -> RepeatStatus.FINISHED))
        .build();
        }

```