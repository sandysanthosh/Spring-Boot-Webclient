# Spring-Boot-Webclient

Spring Boot program that uses the Spring WebClient to communicate with another Spring Boot application

This example uses the WebClient class to create a web client that communicates with a Spring Boot application running on http://localhost:8080.

The getDataFromOtherService method makes a GET request to the /getData endpoint and the postDataToOtherService method makes a POST request to the /postData endpoint, passing in a string as the request body. Both methods return a reactive Mono object containing the response from the other service.

You can use the above code in your service class or in controller class and call the methods whenever you want to communicate with other service.
```

import org.springframework.web.reactive.function.client.WebClient;

@Service
public class CommunicationService {

    private WebClient webClient;

    public CommunicationService() {
        this.webClient = WebClient.create("http://localhost:8080");
    }

    public Mono<String> getDataFromOtherService() {
        return webClient.get()
                .uri("/getData")
                .retrieve()
                .bodyToMono(String.class);
    }

    public Mono<String> postDataToOtherService(String data) {
        return webClient.post()
                .uri("/postData")
                .bodyValue(data)
                .retrieve()
                .bodyToMono(String.class);
    }
}



```


#### JUnit test case for the CommunicationService class::


```
import static org.mockito.Mockito.when;
import static org.junit.Assert.assertEquals;

import org.junit.Test;
import org.junit.runner.RunWith;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.boot.test.mock.mockito.MockBean;
import org.springframework.http.MediaType;
import org.springframework.test.context.junit4.SpringRunner;
import org.springframework.test.web.reactive.server.WebTestClient;

import reactor.core.publisher.Mono;

@RunWith(SpringRunner.class)
@SpringBootTest(webEnvironment = SpringBootTest.WebEnvironment.RANDOM_PORT)
public class CommunicationServiceTest {

    @Autowired
    private CommunicationService communicationService;

    @MockBean
    private WebTestClient webTestClient;

    @Test
    public void testGetDataFromOtherService() {
        when(webTestClient.get().uri("/getData")
                .accept(MediaType.APPLICATION_JSON)
                .exchange())
                .thenReturn(WebTestClient.ResponseSpec
                        .create(HttpStatus.OK)
                        .body(Mono.just("Hello from other service"), String.class));

        Mono<String> result = communicationService.getDataFromOtherService();
        assertEquals("Hello from other service", result.block());
    }

    @Test
    public void testPostDataToOtherService() {
        when(webTestClient.post().uri("/postData")
                .contentType(MediaType.APPLICATION_JSON)
                .bodyValue("Hello from this service")
                .exchange())
                .thenReturn(WebTestClient.ResponseSpec
                        .create(HttpStatus.OK)
                        .body(Mono.just("Data received"), String.class));

        Mono<String> result = communicationService.postDataToOtherService("Hello from this service");
        assertEquals("Data received", result.block());
    }
}



```

