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


