# Spring-WebFlux


## Spring Webflux


-	Each Request to Controller will be handled by Separate  Servlet Thread
-	Servlet Thread or Main Thread  will gets blocked for IO Operations
-	Tomcat or WebServer can handle only 200 Concurrent Requests	
-	Main or Servlet Thread instead of waiting for Blocking IO Operation ... will ask DB to perform IO Operation ... and once Data is Ready ... call CallBack method with any available Thread ... assign data to it
- 	Servlet Thread will provide response to Client


###	Event Loop

-	CallBack mechanism in Which DB Driver will  assign to data to any available Threads
-	Servlet Thread after performing basic operations will delegate a IO Operation Event to DB Driver
-	DB Driver after doing IO Operation will Delegate response by firing an Event ... I am ready with data
-	Any of the Thread from ThreadPool can pick the Event and send response to client
-	No need of 200 Threads
-	Only 1 Thread Per Core is enough to handle any number of Requests
-	More Concurrent Requests
-	More Efficient CPU utilization
-	No More Thread Switches


### Spring WebFlux

-	Spring WebFlux is a Project which handles End to End Reactive or an EventLoop based Mechanism
-	Spring WebFlux enables less Thread and Higher CPU Efficient
-	Spring WebFlux achieves end to end reactive using 

	1.	Servlet 3.0 and 3.1
	2.	Java NIO (File, Network IO)
	3.	MongoDB, Cassandra, Redis

-	All the Components in Spring WebFlux is Reactive 
-	DB Drivers are also reactive

### CompletableFuture

-	CompletableFuture solves Blocking IO Operation Issue 
-	CompletableFuture method theApply() method will be passed with operation or method
-	Thread will perform the operation ... once data is Ready ... 



### Mono and Flux

-	Mono and Flux are CompletableFuture Equivalent of Spring WebFlux
-	Mono can handles 0 .. 1 Elements
- 	Flux can handle 0 ... N Elements



### Hot/Live Source of Data

	Code Snippet for Response:
	
		@RestController
		public class PricingController {
		
			@Autowired
			private PricingRepository pricingRepository; // Reactive DB Drivers
			
			@GetMapping("/get/prices/{id}")
			public Mono<Price> getPrice(@PathVariable String id) {
				return priceRepository.findById(id); Single Value
			}
			
			@GetMapping("/get/prices/all")
			public Flux<Price> getPriceAll() {
				return priceRepository.findAll(); // List of Values
			}
			
			
			@GetMapping("/get/prices/live", produces= "text/even-stream") // Live List of values
			public Flux<Price> getPriceAll() {	
				return priceRepository.findAll();
			}
			
		}

-	Call to "get/prices/live" endpoint is live and keeps remaining open 
-	Call to "get/prices/live" is a Hot or Live Connection b/w User and Server
-	Whenever new data is populated in the DB ... Reactive DB Driver will PUSH the DATA or EVENT to Spring WebFlux  ... Spring WebFlux in turn will keeps open an hot connection b/w Client and Server

	Code Snippet for Request:

	@RestController
		public class PricingRequestsController {
		
			@Autowired
			private PricingRepository pricingRepository; // Reactive DB Drivers
			
			@PostMapping("/save/price")
			public Mono<Price> save(@RequestBody Mono<Price> price) {
				price.subscribe(pricingRepository::save); Single Value
			}
			
			@PostMapping("/save/prices/all")
			public Flux<Price> saveAll(@RequestBody Flux<Price> prices) {
				prices.subscribe(pricingRepository::saveAll); // List of Values
			}
			
			
			@PostMapping("/save/price/", consumes= "application/stream+json") //Hot or Live Connection
			public Flux<Price> savePrice(@RequestBody Flux<Price> prices) {	
				prices.subscribe(pricingRepository::saveAll);
			}
			
		}

		
-	Reading values can also made Non-blocking using Mono and Flux as method parameters
-	consumes= "application/stream+json" keeps connection open and client can keep on pushing the values at any point in time


### When to Use Sprin WebFlux

-	Scalability --> lot 
	
	1.	Hell lot of Concurrent Requests
	2.	Lot of IO Bound Operations
	3.	Efficient CPU utilization
	4.	Data Locality and Less Context Switches

-	Streaming Use-Cases (Live Source of Data)
	
	-	Live Source of Data
	
-   BackPressure
