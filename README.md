# spring-boot-kafka-producer

# 1. Dependências no pom.xml:

```
<dependency>
   <groupId>org.apache.kafka</groupId>
   <artifactId>kafka-streams</artifactId>
</dependency>
<dependency>
   <groupId>org.springframework.kafka</groupId>
   <artifactId>spring-kafka</artifactId>
</dependency>
```

# 2. Depois disso vamos para o application.properties:

```
# Producer properties
spring.kafka.producer.bootstrap-servers=127.0.0.1:9092
spring.kafka.producer.key-serializer=org.apache.kafka.common.serialization.StringSerializer
spring.kafka.producer.value-serializer=org.apache.kafka.common.serialization.StringSerializer
spring.kafka.producer.group-id=group_id
topic.name.producer=topico.comando.teste

# Common Kafka Properties
auto.create.topics.enable=true
```
Nesse primeiro momento coloquei a classe StringSerializer para a fazer a serialização das mensagens, só pra mandar um texto qualquer.
A propriedade auto.create.topics.enable=true cria automaticamente os tópicos registrados nessas propriedades. Nesse caso coloquei topic.name.producer=topico.comando.teste.
Bora para o código então.
Na classe main coloquei a anotação “@ EnableKafka”, que faz com que seja possível a conexão com um tópico.

```
@EnableKafka
@SpringBootApplication
public class DemoApplication {

   public static void main(String[] args) {
      SpringApplication.run(DemoApplication.class, args);
   }

}
```

A minha classe producer ficou assim:

```
@Slf4j
@Service
@RequiredArgsConstructor
public class TopicProducer {

    @Value("${topic.name.producer}")
    private String topicName;

    private final KafkaTemplate<String, String> kafkaTemplate;

    public void send(String message){
        log.info("Payload enviado: {}" message);
        kafkaTemplate.send(topicName, message);
    }

}
```

KafkaTemplate é a classe que faz o envio de mensagens para os tópicos, o primeiro String é o tópico e o segundo o tipo de informação.
Por fim criei uma controller para fazer testes:

```
@RequiredArgsConstructor
@RestController
@RequestMapping(value = "/kafka")
public class KafkaController {
    private final TopicProducer topicProducer;
    @GetMapping (value = "/send")
    public void send(){
        topicProducer.send("Mensagem de teste enviada ao tópico");
    }
}
```

De uma forma bem simples é isso.
