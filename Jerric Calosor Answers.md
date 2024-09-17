### Regarding your salary expectations - are they negotiable?
**Answer**: Yes it is negotiable

### What is the minimum you need and makes you still happy?

**Answer**: I will be all good in **150k PhP**

If so, lets move to speak about your quality.

### Can you answer the following questions please?

1. Describe a situation where you had to explain a complex technical idea to a non-technical person (e.g., a client or manager). How did you go about it?

**Answer:**
- When speaking with a non tech ie: PO (Project Owner) or Stakeholder, you only talk to them about "What" they want, and "When" they want it, and do not speak to them about the "How's" since that is the technical side of things which only technical people will eventually understand. 

2. Imagine you are working in a team where someone else proposes a different technical solution to a problem than you. How do you handle it?

**Answer:**
- Evaluate it, most of the time we do it over a specified tech meeting or over brownbag session, things to do during evaluation are:
  - Identify the pros and cons of that teammate's solution
  - Feasibility of implementation
  - Weigh in our solutions against each other and find the take aways of each others proposition
  - Decide as a team by vote of majority
- sometimes it'll also warrant a POC specially if the tech solution involves subscription ie (Fargate vs Lambda implementation)

3. Give an example of a situation where you identified a potential problem in a project early on and solved it before it became a bigger issue.

**Answer:**
- I have mentored Jr and mid devs before so I have my fair share of identifying loopholes in the code, take this code for example:
```php
foreach (NewsManager::getInstance()->listNews() as $news) {
	echo("############ NEWS " . $news->getTitle() . " ############\n");
	echo($news->getBody() . "\n");
	foreach (CommentManager::getInstance()->listComments() as $comment) {
		if ($comment->getNewsId() == $news->getId()) {
			echo("Comment " . $comment->getId() . " : " . $comment->getBody() . "\n");
		}
	}
}
```
`->listNews()` is a select query for news table same as `->listComments()` is a select query for comments table belonging to that news result,
from the get go I know that querying over a loop is "bad practice" and this will introduce all sorts of memory issues fast once the application scales,
so you'll have to correct this asap, we do this during code review, either we do a huddle and pair-coding or if we have a tight deadline 
I'll just provide a comment in github stating a change request pointing out the issue in this implementation.

4. Tell me about a time when you had to complete a project under tight deadlines. How did you prioritize tasks, and what was the outcome?
- It all comes down to project planning, we work closely with POs since they are the one deciding on the business side on what will be the most beneficial for the company to release first,
- After requirements gathering backlog will be filled with Todo tickets.
- First we will identify what are the "required" features.
- Then identify what are the "nice to haves"
- This is why working closely with POs are important, after identifying these 2 categories, we will prioritize first the "required" ones and prioritize last the "nice to haves"
there will always be approximation regarding completion, especially if there are grey areas or edge cases that we could not identify upfront, 
from there we will identify the minimum requirement for release, to arrive at an acceptance criteria for the POs that they are willing to release only the "required" feature will be included, 
and the "nice to have" will be released on later versions.

### Now some technical issues:

1. What are the differences between require, include, require_once, and include_once in PHP? When would you use each?

**Answer:**
- **require**: 
  - If the file being required does not exists, `require` will throw a fatal error and stop the execution of the code.
  - Usage: It only should be use for files that are essential in running the application ie `config.php`
- **include**:
  - If the file being included does not exists, `include` will only prompt a warning, but will continue with the execution.
  - Usage: It should be use for non-essential files that will not halt the code's execution if missing.
- **require_once**:
  - This includes the file only* if the file hasn't been included yet, it restricts multiple inclusion of the same file.
  - Usage: use for making sure that the file is included once, this is useful for helper class or defined functions ie: `helper.php`
- **include_once**:
  - The same with `include` will only prompt a warning if the file does not exists.
  - Usage: use for making sure that the file is included once, this is used for non-essential files ie: `options.php`

2. Explain the difference between a Service and a Controller in Symfony. How would you create a new Service and use it in a Controller?

**Answer:**
- Controllers: The same with the common MVC frameworks such as Laravel, Zend and Cake: 
  - Acts as the entry point for application interactions 
  - It handles incoming requests and executes business logic then returns a response
- Services: In Symfony you can refer to services as modules or reusable business logic that can* be used by controllers or* other services:
  - A service class is either a collection of abstraction layer that interacts with an API integration or 
  - A class managing User business logic and User management
  - It can also sit on top of entity and repository hierarchy, a service can contain multiple repository classes
  - These classes **should*** be injectable by design, allowing maintainability and testability.
  
  1. In creating a service, I usually put in the `Service` dir ie:
```php
namespace App\Service;

class UserService
{
    public function __construct(private UserRepositoryInterface $userRepository) {}

    public function createUser(array $userParams): User
    {
        // Business logic for user creation,
        // return the newly created user.
        
        // return $this->userRepository->create($userParams);
    }
}
```
  2. Then I could either explicitly define the service in my `services.yaml` file for backwards compatibility Symfony 3.^, or use Autowiring ie:
```php
# config/services.yaml
services:
    _defaults:
        autowire: true
        autoconfigure: true

    App\Repository\UserRespository:
        tags: ['doctrine.repository_service']
        alias: App\Repository\Interfaces\UserRepositoryInterface

    App\Service\UserService:
        arguments:
            $userRepository '@App\Repository\Interfaces\UserRepositoryInterface'
```


3. Create a simple Symfony project that implements a CRUD (Create, Read, Update, Delete) application for a Product entity using a MySQL database. Explain the steps and code you used.

**Answer:**
- Please take a look at https://github.com/jcalosor/wordliner-test

4. Describe how you would handle an unexpected error message in a Symfony project that occurs when processing an HTTP request.
**Answer:**
- This depends, if the application is an MVC app, I would do the following:
  - Write a custom error page, with designated http status code value ie:
  ```php
    templates/bundles/TwigBundle/Exception/http_error.html.twig
  
    # config/routes.yaml
    error:
     path: /error/{statusCode}
     controller: App\Controller\HttpErrorController::show
    ```
- Now if this is a REST API endpoint, I will implement an event listener, that will anticipate exceptions caused by 
http errors and have it registered globally ie:
   ```php

   class HttpErrorListener 
   {
     public function onKernelException(ExceptionEvent $event): void 
     {
        $response = new Response();
        $response->setContent('An error occurred. Please try again later.');
        $response->setStatusCode(Response::HTTP_INTERNAL_SERVER_ERROR);

        $event->setResponse($response);
     }
   }
  
   ```
----
Now more about your job in the future:

Software

The real estate valuation software is designed to assist brokers, property owners, and financial service providers in conducting property valuations, performing affordability checks, and displaying comparable properties on maps. The software integrates external data sources such as Sprengnetter, GeoMap, and Europace to provide comprehensive market value analyses and financing suggestions. It must be scalable, secure, and user-friendly, supporting the entire real estate value chain.

Please answer the questions:

1. Strategic API Integration and Data Management

Question: "You are responsible for integrating the APIs from Sprengnetter, GeoMap, and Europace. How would you approach this integration strategically to ensure that the data is consistent, up-to-date, and accessible across the entire system? What challenges do you anticipate, and how would you address them?"

**Answer:**
- If it is meant to be used for multi-repo or microservices, I would create a php package for it, 
that enforces Adapter pattern and Interface segregation, I have worked on a similar feature before, 
where we needed to integrate Adyen whilst keeping the current integration for Paypal functional,
so I have created the "Payments Service Adapter" package, that allows seameless integration of our 3rd party payment gateways ie:
```php

interface PaymentServiceInterface {
   public function pay($params): Response;
}

class PayPalAdapterService extends PayPalSDK implements PaymentServiceInterface {}

class AdyenAdapterService extends AdyenSDK implements PaymentServiceInterface {}

```

2. Scalable and Secure Database Architecture

Question: "Describe how you would design a scalable and secure database architecture to manage properties, user roles, valuations, and external API data (e.g., from Sprengnetter and GeoMap). How would you ensure that the architecture supports future growth and high data security requirements?"

**Answer:**
- With RDBMS it is crucial to build a database design that are well abstracted and normalized that is essential for
database maintainability and scalabilty.

- Optimized Queries* I can't stress this enough, best practices for query goes a long way, even if we use stored procedures or 
ORM cache if the query is badly written it will badly perform.

- For future scaling there could be a lot of variation based on the growth of the application itself, we will need to 
think about factors like do we need to scale vertically? or horizontally? In these scenario we might need to do load balancing,
sharding or upgrading hardware instances 

3. Complex System Integration and Optimization

Question: "The real estate valuation software must integrate multiple external systems and provide a seamless user experience. Describe how you would optimize the integration of these systems to ensure users can always access up-to-date and consistent data. How would you handle potential bottlenecks and conflicts between the systems?"

Here are the API documentation links:

Sprengnetter API: https://api.avm.sprengnetter.de/service/api/help/index.html

GeoMap API: https://api.geomap.immo/extern/wordliner/v1/index.html

Europace API: https://github.com/europace/baufi-passende-vorschlaege-api/tree/main#request-financial-proposals

**Answer:**
- This depends, if the implementation are already in place, we might need to re-evaluate the implementation, then identify key areas
where we need to make adjustments, or changes, in my experience, when we we're migrating an integration from 1.0 all the way to it's version
5.0 we have decided to write again an "adapter" to interface with the new and old version of the integration, it was risky and a big 
feature since the gap between 1.0 and 5.0 depencies are between PHP (5.6 -> 8.2) versions, so that pose some challenges as well.

- But we made sure that all the critical tests, ( Unit, Functional, End to End ) are in place so that we won't miss an 
existing business logic in the implementation, I think identifying "crucial pain" points 
and prioritizing what needs to be implemented is what's most important. 

Last question:

Which conditions do you need regarding coding, challanges, team, company to be motivated to work years for our company?

- Stable Company
- Career growth :)