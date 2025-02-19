# GitHub Pages [jEnv](https://dixitgurv.github.io/lightning-talks-jenv/#1)

# GitHub Pages [Guava - Preconditions](https://dixitgurv.github.io/Guava-Preconditions/)

# GitHub Pages [Shelve - Intelli J](https://medium.com/@dixitgurv/intellij-shelve-a-help-in-busy-days-8423c693d809)


```
spring:
  kafka:
    bootstrap-servers: localhost:9092
    consumer:
      group-id: test-group
      auto-offset-reset: earliest
  myapp:
    kafka-topic: test-topic

@TestConfiguration
@Profile("integration")
@Testcontainers
public class KafkaTestConfiguration {

    @Container
    public static KafkaContainer kafkaContainer = 
        new KafkaContainer(DockerImageName.parse("confluentinc/cp-kafka:latest"));

    @Container
    public static PostgreSQLContainer<?> postgreSQLContainer = 
        new PostgreSQLContainer<>("postgres:latest");

    @PostConstruct
    public void setup() {
        System.setProperty("spring.kafka.bootstrap-servers", kafkaContainer.getBootstrapServers());
        System.setProperty("spring.datasource.url", postgreSQLContainer.getJdbcUrl());
        System.setProperty("spring.datasource.username", postgreSQLContainer.getUsername());
        System.setProperty("spring.datasource.password", postgreSQLContainer.getPassword());
    }
}

@SpringBootTest(classes = KafkaTestConfiguration.class)
@ActiveProfiles("integration")  // Activate the integration profile
public class KafkaListenerIntegrationTest {

    @Autowired
    private TestKafkaProducer kafkaProducer;  

    @Autowired
    private JdbcTemplate jdbcTemplate;

    @Autowired
    private KafkaListenerEndpointRegistry kafkaListenerEndpointRegistry;

    @BeforeEach
    void setup() {
        kafkaListenerEndpointRegistry.getListenerContainers()
                                     .forEach(container -> container.start());
    }

    @Test
    public void testKafkaConsumerCreateEvent() throws Exception {
        String message = "{ \"eventType\": \"CREATE\", \"id\": \"123\", \"data\": \"New Record\" }";
        kafkaProducer.sendMessage("test-topic", message);

        Thread.sleep(5000);  

        int count = jdbcTemplate.queryForObject("SELECT COUNT(*) FROM my_table WHERE id = ?", 
                                                 Integer.class, "123");
        assertEquals(1, count);
    }

   
}

```


